# TaskSYS.cpp — Detailed Design & Developer Guide

> Target reader: new team members working on firmware (STM32/FreeRTOS-style). This doc explains what **TTaskSYS** is responsible for, how it interacts with other tasks and HAL layers, the boot/OTA pipeline, the VIP protocol handling, and where/why specific callbacks exist.

---

## 1) High‑level Role

`TTaskSYS` is the **system orchestrator** task. It:

* Initializes subsystems (`TaskUI`, `TaskHAL`, `TaskSD`, optional `TaskCLI`).
* Owns the **main event loop** for system-level messages and error handling.
* Bridges **VIP interface** packets ↔ system/HAL actions (fans, heaters, motors, lamps, sensors, RTC, version/SN, etc.).
* Handles **UART & SD callbacks** (from ISRs) by turning them into task-level events.
* Implements **FW update & bank swap** logic (when `__OTA_ENABLED`), including decrypt → flash write → CRC → bank swap & jump.
* Exposes system **state** to the UI and to the VIP interface, including mapping to error codes and UI codes via `TableSysState.h`.

`TTaskSYS` is *not* the low-level driver; it orchestrates and delegates to singletons: `THalDcFans`, `THalHeaters`, `TMotChambers`, `TGpio`, `TaskHAL`, `TaskSD`.

---

## 2) Build‑time Feature Flags

* `__OTA_ENABLED`: switches which UART the VIP slave uses, enables OTA write paths, and changes `module_ident` version string.
* `__DEBUG_SYS_OUTPUT_ENABLED`: enables `DiagPrintf/DiagNotice` routed via `TaskCLI`.
* `__DEBUG`: creates the CLI task and initializes it.

**Implication:** UART routing is compile‑time dependent:

* **OTA build:** VIP RX/TX on `USART2`; CLI on `USART1`.
* **Non‑OTA build:** VIP RX/TX on `USART1`; (no CLI by default).

---

## 3) Static Module Identity (`module_ident`)

A 64‑byte, linker‑placed blob (`.module_ident` section) holding:

* 16B version string (e.g., `LL01-AMT-001.006`),
* 4B `CRC_`, 4B `SIZE`,
* 16B reserved,
* 16B encryption key placeholder (zeroed at build; populated during OTA provisioning).

**Usage:** During OTA, the first flash packets (up to `TASK_SYS_ADDRESS_ENCRYPT_END`) are cached, key extracted, cipher initialized, then bulk decode+program proceeds.

---

## 4) OS Events & Main Loop

### 4.1 Event sources

* **ISRs:**

  * `vApplicationTickHook` → `SetEventTickFromISR()` (soft tick fan‑out to `TaskHAL` when enabled).
  * `HAL_UART_*Callback` (for UART1/2/3/4) → sets SYS/HAL/CLI events depending on build and port.
  * `HAL_SD_*Callback` (for `hsd1`) → notifies `TaskSD` events.
* **VIP frames parsed** by `InterfaceSlaveVIP` → `SetEventUartRxCpltFromISR()` or `…ErrorFromISR()`.

### 4.2 Main task `Run()`

1. `Init()` all subsystems; set state to `SysState_Idle`.
2. Loop `WaitOrBits()` on SYS events with 1s timeout.
3. Dispatch:

   * `UART_RX_CPLT` → `ProcessRxData()` (VIP command handler)
   * `UART_ERROR` → `ReInitUart(); StartRxData();`
   * `RESET` → `Reset()`
   * `JUMP_APPLICATION` → `JumpToApplication()`
   * `START_TEST` → `ProcessTest()`
   * `ERROR` → handled by `ProcessError()` path

### 4.3 Error path `ProcessError()`

* Puts UI into error (`TaskUI.SetState(TASK_UI_EVENT_ERROR)`), then loops waiting for RX/ERROR/RESET; will re‑init UART on UART errors; returns on `RESET` event.

---

## 5) VIP Interface Command Handling (`ProcessRxData`)

`InterfaceSlaveVIP` provides `GetCommand()` and a pointer to the RX payload. The handler reads/modifies a 32‑byte data area (`IFC_VIP_UART_SIZE_DATA`) and finally calls `InterfaceSlaveVIP.StartTxData()` with the reply command and payload. A 10 ms inter‑frame delay is inserted for all commands except `WritePacket`.

### 5.1 State/Info

* **`GetState`** → reports `sysState` mapped to **VIP state code** + error code (via `TableSysState.h` tables `interfaceVipCode[]` and `errorCode[]`).
* **`GetVersion`** (Part1/Part2) → returns 8‑byte halves from `module_ident` version.
* **`SetRTC`/`GetRTC`** → proxy to `TRtc` singleton.
* **`SetSerialNumber`/`GetSerialNumber`** → serial number stored/retrieved via `TaskSD` API; write is two‑part, then persisted.

### 5.2 Sensors

* **`GetBme688_Part1`** → returns struct `TBme688Sensors` (minus last 4B) for one of 4 BME688 roles: Left, Right, Intake, Exhaust.
* **`GetTemperature`** → routes to `TaskHAL` sensors (pad L/R, CPU, PTC intake/internal) based on `EIfcVipTemperature`.
* **`GetWeightSensor`** → returns one of four cached weight sensor values from `TTaskSYS` fields.

### 5.3 Actuators

* **Fans**

  * `ControlFan` → `THalDcFans::StartBlower/StartFanIntake(pwm)`
  * `GetStateFan` → returns PWM and RPM (16‑bit)
* **Lamps**

  * `ControlLamp` → `TGpio::LampPlasmaOn/Off` or `LedCatalyticOn/Off`
  * `GetStateLamp` → (currently NOP; placeholder)
* **Motors**

  * `ControlMotor` → Main motor via `TaskHAL` (dir, start/stop). Chamber1/2 via `TMotChambers` (dir + start/stop).
  * `GetStateMotor` → returns direction and current for Chamber1/2 (main motor returns immediately with zeros).
* **Heaters**

  * `ControlHeater` → `THalHeaters::StartHeater*` with PWM for pad L/R and PTC intake/internal.
  * `GetStateHeater` → returns PWM for pad heaters (PTC getters are TODO/commented).

### 5.4 Control Flow commands

* `StartTest` → latched into `testIfcVipComponent`; raises `START_TEST` event.
* `Reset` → raises `RESET` event.
* `JumpApplication` → raises `JUMP_APPLICATION` event.

### 5.5 OTA / Memory (guarded by `__OTA_ENABLED`)

* **`WritePacket`** → two destinations:

  * `IfcVipMemory_Cpu1` → decrypted flash programming of the *other* bank’s application region in **quad‑word** chunks.
  * `IfcVipMemory_AiData` → decrypted writes to SD “AI data” region.
* (ReadByte/ReadPacket etc. are present but disabled/commented.)

---

## 6) UART/SD Callback Matrix

**RX complete:**

* OTA build: VIP on `USART2` → `TaskSYS.SetEventUartRxCpltFromISR()`
* Non‑OTA: VIP on `USART1` → `TaskSYS.SetEventUartRxCpltFromISR()`
* `USART3/4` are for `TaskHAL` peripherals (Sys delegates to HAL events).

**TX complete:**

* OTA build: CLI on `USART1` (→ `TaskCLI.SetEventUartDmaCpltFromISR()`)
* Non‑OTA: SYS on `USART2` (→ `TaskSYS.SetEventUartDmaCpltFromISR()`)

**Error:** routed similarly to RX (VIP port → SYS error event; HAL ports → HAL error events).

**SD callbacks:**

* `HAL_SD_*` on `hsd1` notify `TaskSD` (RX/TX complete or Error).

---

## 7) Initialization Sequence (`Init()`)

1. Reset counters; get `TRtc` singleton.
2. `TaskUI.Init()`; move UI to INIT via `TASK_UI_CMD_START`; set system state `NoState` briefly.
3. Set `SysState_Init`; **re‑init UART** (sets baud, word length, etc., for VIP port per build flag); start VIP RX.
4. If debug, `TaskCLI.Init()`.
5. Log power‑up and **SD card** init → `TaskSD.Init(); SetEvents(TASK_SD_CMD_START)`.
6. Log main CPU version from `module_ident`.
7. Enable `enableTickHook`; init `TaskHAL` and send `TASK_HAL_CMD_START`.
8. Read **Serial Number** from SD (blocking wait for OK/ERROR).
9. Delay and return `OsResult_Ok`.

**Note:** `CreateApplicationTasks()` creates all tasks statically (CLI optional), then `TaskSYS`.

---

## 8) System State & UI Mapping

`SetSysState()` stores `sysState` and maps to UI events:

* `Idle` → `TASK_UI_EVENT_IDLE`
* `Init/Busy/SelfTest` → `TASK_UI_EVENT_INIT`
* `FrontLidOpen/BackLidOpen/TopUnlocked` → `TASK_UI_EVENT_TOP_REMOVED`
* Default → `TASK_UI_NO_STATE`

If `sysState > SysError_Start`, logs error and returns (UI already in error via `ProcessError()` path).

`GetErrorCode()` returns the byte from `errorCode[]` table indexed by `sysState` (table generated by `TableSysState.h` and macro trickery).

---

## 9) Timekeeping

* `systemCounter` increments every tick hook (`ms` unit), gated by `enableTickHook`.
* `GetTimeSystem()` returns `{ms, s, m, h, d}` split from the counter.
* `GetSystemCounter()` returns the raw `u64` ticks.

---

## 10) OTA / Bank Swap Flow (when `__OTA_ENABLED`)

### 10.1 Packet ingest & decryption

* First region up to `TASK_SYS_ADDRESS_ENCRYPT_END` is **buffered** in `tempEncryptedPackets` (RAM).
* On reaching the last packet of that region:

  1. `ProcessEraseMemory()` → erase *other* flash bank page range (ICACHE disable/enable around erase).
  2. Extract **key** from buffered `module_ident` area and `GC2Init(key)`.
  3. Decode in `ENCRYPTION_GC2_BLOCK_LEN` blocks; skip the key blocks (write zeros).
  4. Program in pairs (two blocks = quadword), via `WriteFlash()`.
* Remaining packets (≥ `TASK_SYS_ADDRESS_ENCRYPT_END`) are decoded immediately and written using the same pairing logic.

### 10.2 CRC check & jump

* `CheckCrcApplication()`

  * Reads `SIZE` and `CRC` fields from the flashed app image header (excluding its own CRC block index = `i != 3`).
  * Streams through flash computing CRC32 (LSB‑first bit iterator, final XOR `~`).
  * Compares with header CRC.
* `JumpToApplication()`

  * Validates version header `LL01` signature.
  * Performs Option Byte **SWAP_BANK** toggle (to boot from the newly written bank) and launches OB reload (MCU resets).

**Failure modes:** any error returns `OsResult_Error`; the running bank remains active.

---

## 11) SD “AI Data” Writes

* Addressing: `addressSd` → `indexBuffer = addr % BLOCK_SIZE`, `blockNumber = addr / BLOCK_SIZE + BLOCK_START_AI_DATA` (+ page1 offset if `sysCurrentBank == Bank1`).
* Data packed into an internal `bufferBlockSd[]` until exactly one block filled; then `TaskSD.StartWriteBlock()` is issued and SYS waits on `TASK_SYS_SD_RESULT_OK/ERROR` (400ms timeout).
* Higher‑level helpers:

  * `WritePacketToAiData()` mirrors the OTA decrypt logic but targets SD via `WriteAiData()`.

---

## 12) Per‑Feature Walkthroughs

### 12.1 Example: Control Fan

1. VIP host sends `IfcVipCommand_ControlFan` with `NUMBER_OF_ITEM` = `Blower` or `Intake` and PWM.
2. SYS → `ControlFan()` → `THalDcFans::StartBlower/StartFanIntake(pwm)`.
3. Host may query `GetStateFan` → returns current PWM and measured RPM.

### 12.2 Example: Motor State

* `GetStateMotor()` returns dir + current for `Chamber1/2` via `TMotChambers`; for main motor it returns defaults (no telemetry exposed here).

### 12.3 Example: Set RTC

* SYS copies `TRtcDate` from payload, calls `Rtc->SetDate()`, sets `SysError_Application` on failure.

---

## 13) Invariants, Assumptions, and Gotchas

* **Endianess:** CRC and multi‑byte fields are memcpy’d; both sender and MCU must agree on little‑endian packing.
* **Packet sizes:** VIP data buffer is a fixed size (`IFC_VIP_UART_SIZE_DATA`). Handlers that `memcpy` must respect struct sizes (e.g., `TBme688Sensors` minus 4B tail).
* **Delays:** A 10 ms delay is inserted before every TX except `WritePacket` to pace the link.
* **UART hot‑reinit:** On RX errors, SYS de‑inits and re‑inits the port, then restarts RX DMA immediately.
* **`GetStateLamp()`** is a stub; if the UI depends on it, implement GPIO reads.
* **PTC heater getters** are commented; if host expects them, wire up to `THalHeaters`.
* **Bank swap logic** relies on OB reload resetting the MCU; execution never returns on success.
* **Tick hook fan‑out** to `TaskHAL` is gated by `enableTickHook` (set in `Init()`).

---