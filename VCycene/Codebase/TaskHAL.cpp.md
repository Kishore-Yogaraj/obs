# TaskHAL Module – Developer Documentation

> **Purpose:** This document explains how the `TaskHAL` module works in this codebase: responsibilities, threading/event model, data flows, timing, APIs, and patterns for extending, testing, and debugging.

---

## 1) Big Picture

* **Platform:** STM32-class MCU using STM32Cube HAL and an RTOS with FreeRTOS-like primitives (EventGroup, Queue, Semaphore).
* **Role:** `TaskHAL` is the **hardware abstraction** task. It is the only task that directly touches peripherals (GPIO/EXTI, ADC+DMA, I2C, UART, timers). It publishes processed measurements and raises system events for higher-level tasks such as:

  * `TaskSYS` – system state, safety and watchdog coordination
  * `TaskPROC` – application-level consumers of sensor data
  * `TaskCHM` – chamber control (left/right) reacting to motor current anomalies
  * `TaskWD` – watchdog heartbeat
  * `TaskSD` – optional logging to SD

### Responsibilities

* ADC sampling + reduction (AC currents, temperatures, ID) with DMA-based frames → smoothed values.
* I2C polling/recovery for **BME688** sensors (Left, Right, optional Intake/Exhaust).
* UART polling/recovery for **weight sensors** (Back on UART4, Front on USART3).
* EXTI interrupt handling for AC zero-crossing/phase, motor pulses, RPMs, encoder.
* AC heater **phase-synchronized PWM** via EXTI half-cycles.
* Main/chamber motors control: start/stop/brake sequencing and jam detection.
* Safety checks for lids/locks with blocking wait-until-safe behavior if required.
* Event/command arbitration: converts ISR/timer ticks into RTOS events consumed in `Run()`.

### Non-responsibilities

* UI/business logic, algorithms not tied to hardware, or long-running computations (those live in `TaskPROC`/`TaskSYS`).

---

## 2) Concurrency Model & Event Flow

### Contexts

* **ISR context**: ADC DMA completion, GPIO EXTI (AC edges, RPM, encoder), UART IRQ callbacks.
  → Do **not** block. Increment counters, copy small data, **set event bits**.
* **Task context** (`TaskHAL::Run()` loop): waits on an **EventGroup** and performs I/O, math, and inter-task messaging.

### Primary Events (semantic summary)

* `TASK_HAL_CMD_START` – begin the main loop (bootstrap gate).
* **Sensor windows**

  * `TASK_HAL_EVENT_AC_CALCULATE` – ADC AC-current window complete; run `CalculatingAcCurrents()`.
  * `TASK_HAL_EVENT_T_READY` – temperature accumulator ready; run `CalculatingTSensors()`.
  * `TASK_HAL_EVENT_GET_BME688_LEFT/RIGHT/INTAKE/EXHAUST` – due to read each BME.
* **UART**

  * `TASK_HAL_EVENT_UART4_RX_CPLT/ERROR` – back weight sensor done/error.
  * `TASK_HAL_EVENT_UART3_RX_CPLT/ERROR` – front weight sensor done/error.
* **Commands**

  * `TASK_HAL_EVENT_SYS_COMMAND` – a `TSysCommand` is in the queue; `ProcessSysCommand()` will dispatch it (self-test, motors, heaters, fans, BME tests, AC power test).
  * `TASK_HAL_CMD_STOP_ALL` – stop motor & AC power.
  * `TASK_HAL_CMD_GRINDING` – execute grinding routine with jam recovery.

> **Pattern:** ISRs set bits → `Run()` wakes → performs safe work (I2C/UART/processing) → pushes results to `TaskPROC` or `TaskSYS`.

### Startup Sequence

1. `Init()` resets power paths and internal state, binds singletons (`TGpio`, `THalHeaters`, `THalDcFans`, `TMotChambers`).
2. `CreateOsResources()` creates the command queue and semaphores.
3. `CreateTask()` (or `CreateTaskStatic()`) starts `TaskHAL`.
4. `Run()` waits for `TASK_HAL_CMD_START`, then re-inits UART3/4 and proceeds to the main event loop.

---

## 3) Sensor & Actuator Pipelines

### 3.1 ADC + DMA (AC currents, temperatures, board ID)

* **Source:** `HAL_ADC_ConvCpltCallback` → `AdcConversionComplete()`.
* **Per-frame work:**

  * Accumulate channels into `calculationResultAdc1[]`.
  * Push instantaneous samples into ring buffers:

    * `acPowerAdcBuffer[]`, `acChamberLeftAdcBuffer[]`, `acChamberRightAdcBuffer[]`.
  * Issue `TASK_HAL_EVENT_AC_CALCULATE` when a window fills (`TASK_HAL_AC_CURRENT_TIME_MEAS`).
* **Temperature smoothing:** maintains 3-sample moving averages for each thermistor. After `HAL_T_NUMBER_CONVERSION` frames, it stores `adcTPadLeft/Right`, `adcTPtcIntake/Internal` and signals `TASK_HAL_EVENT_T_READY`.
* **Board ID:** simple ADC threshold selects `boardVersion` (`v5`/`v6`).

#### AC current calculation (`CalculatingAcCurrents()`)

* Compute DC offsets (`zeroLevel*`) from the window means.
* Rectify with a small deadband (±2 counts) and sum absolute deltas.
* Convert to engineering-ish units via scale constants:
  `resultAcPowerCurrent` (mA), `resultChamberLeftCurrent`, `resultChamberRightCurrent`.
* Maintain `averageAcPowerCurrent` as a simple IIR (divide by 2 after accumulation).
* **Jam detection:** compares per-chamber current vs direction-specific thresholds `MOT_CHAMBER_JAMMED_CURRENT_{CW,CCW}`; on first hit sets an internal latched flag; on second consecutive hit posts `TASK_CHM_EVENT_HIGH_CURRENT` to `TaskChamberLeft/Right`.

#### Temperature calculation (`CalculatingTSensors()`)

* For each probe (Pad L/R, PTC Intake/Internal):

  * Map the raw ADC value into °C using piecewise linear segments with constants `TASK_HAL_T_*_DEG_C` at 5°C steps.
  * Apply small nonlinear corrections at ≥40/50/60°C.
  * Update task-local fields (`tPadLeft`, …) and push to `TaskPROC.UpdateTemperature*`.

### 3.2 BME688 Sensors (I2C2 for Left/Right, I2C3 for Intake/Exhaust)

* **Scheduling:** `SetEventTickFromISR()` decrements a per-sensor counter; when it reaches 0 it sets `TASK_HAL_EVENT_GET_BME688_*` and resets the counter to `GetSamplingTime()`.
* **Read path:** `ProcessDataBme688(TBme688* p)`

  * Calls `p->GetData()` to retrieve up to 3 frames; selects the first with `BME688_VALID_DATA`, otherwise uses the freshest with NEW\_DATA flag.
  * Writes a `TBme688Sensors` struct back to the BME object and publishes to `TaskPROC.UpdateBme688Sensor*`.
  * On error: logs, runs `RecoveryBme688_Sensors()` (I2C deinit, power-cycle GPIOs, `Init()` each BME, restart measurements), or sets a `TaskSYS` error (`SysError_Bme688_*`).
* **Init path:** `StartBme688()` staggers sampling counters across sensors, then calls `StartMeasurement()` on each.

### 3.3 Weight Sensors (UART)

* **Ports:** `UART4` (Back) and `USART3` (Front), both `19200 8N1`.
* **Once/sec** (driven from AC calculation), the task triggers `WeightSensor.StartReadWeightBack()` and `StartReadWeightFront()`.
* On `*_RX_CPLT` events, it calls `UpdateWeight*()` and publishes to `TaskPROC.UpdateWeightSensor*`.
* Timeouts/errors are detected by a per-port tick watchdog → the loop reinitializes the UART (`ReInitUart4/3`).

### 3.4 Heaters (AC phase-angle PWM in EXTI)

* **Zero-crossing input:** `AC_MAIN_Pin`;
* **On each edge:**

  * `ProcessHeater()` compares `counterPwmHeater` with desired PWM (0–`TASK_HAL_AC_MAX_PWM_HEATER`) per channel and pulses `THalHeaters::PulseOn/Off*()` appropriately.
  * `ProcessAcPhase()` increments and wraps `counterPwmHeater` once per half-cycle.

### 3.5 Motors & AC Power

* **AC power gate:** `AcPowerOn()` energizes AC path, validates presence via `flagAcMainPresent`; `AcPowerOff()` removes power and checks expected absence.
* **Main motor start:** `StartMainMotor(dir)` sequences brake-off → driver status checks → precharge resistor → enable → bypass resistor.
* **Stop:** resistor on → motor off → brake on (with delays).
* **Grinding:** `ProcessGrinding()` performs a timed forward run; on over/undercurrent uses short reverse attempts to unjam; always posts `TASK_SYS_EVENT_END_GRINDING` before return.

### 3.6 Safety Switches

* `CheckSwitches()` debounces **Back Lid**, **Top Lock**, **Front Lid**.
  If back lid opens: stop motor, AC off, send `TASK_SYS_EVENT_BACK_LID_OPEN` and **block** until closed (while petting the watchdog).
  Front lid generates open/close events. (Top lock handling is present but commented for now.)

---

## 4) Public APIs & Integration Points

> Note: Full signatures are in `TaskHAL.hpp`. This summarizes behavior relevant to callers.

### Lifecycle

* `EOsResult Init()`: Bind drivers/singletons and reset state. Safe to call at boot.
* `EOsResult CreateOsResources()`: Create queue and semaphores. Call before `CreateTask*()`.
* `void CreateTask()` / `void CreateTaskStatic()`: Start the RTOS task.
* `void Run()`: Main loop; waits for `TASK_HAL_CMD_START` before active processing.

### Commands from other tasks

* `EOsResult SendSysCommand(ESysCommand cmd, u8* data)`: Queue a system command and set `TASK_HAL_EVENT_SYS_COMMAND`. Supported commands (via `ProcessSysCommand()`): self-test, AC power test, ozone lamp test, chamber motors (L/R), main motor, heaters (pad L/R, PTC intake/internal), fans (blower/intake), BME tests, weight sensor tests, etc.
* `SetEvents(...)` / `SetEventsFromISR(...)`: Raise event bits to wake `Run()`; internal to HAL and used by ISR proxies.

### Sensor getters

* `TBme688Sensors* GetPointerBme688_Left/Right/Intake/Exhaust()` – access last written BME readings.
* Temperature getters: `GetTemperaturePadLeft/Right()`, `GetTemperaturePtcIntake/Internal()`, `GetTemperatureCpu2/3()`.
* `u16 GetAverageAcPowerCurrent()` – slow IIR of AC current (mA-ish).

### Actuation (internal orchestration)

* `EOsResult AcPowerOn()/AcPowerOff()`; `EOsResult StartMainMotor(EMotorDir)/void StopMainMotor()`.
* `void ProcessStopAll()`; `void ProcessGrinding()`; `void RecoveryStateParts()`.

### ISR entry points (HAL glue)

* `SetEventAdcConvCpltFromISR()`, `SetEventAdcErrorFromISR()` from ADC IRQ.
* `HandlerGpioInterrupt(u16 pin)` from EXTI IRQ (AC main/motor, RPM, encoder).
* `SetEventUart{3,4}{Tx,Rx}CpltFromISR()`, `SetEventUart{3,4}ErrorFromISR()` from UART IRQs.

---

## 5) State & Timing

### Important flags/counters

* **Presence:** `flagAcMainPresent`, `flagAcMotorPresent` from pulse counts within `counterTimeAcMeasurement` windows.
* **RPM:** `rpmMotorMain`, `rpmBlower` from edge counters within their time bases.
* **BME scheduling:** per-sensor `CounterSamplingTime` vs `GetSamplingTime()`.
* **UART timeouts:** `timeoutBackMassSensor`, `timeoutFrontMassSensor` vs `TASK_HAL_TIMEOUT_TX_MASS_SENSOR`.
* **Heater PWM:** `counterPwmHeater` wraps at `TASK_HAL_AC_MAX_PWM_HEATER`.

### Periodicities (names; see header for actual constants)

* **Tick**: calls `SetEventTickFromISR()` – starts ADC, updates schedulers and watchdogs.
* **AC current window**: `TASK_HAL_AC_CURRENT_TIME_MEAS` samples → `AC_CALCULATE` event.
* **Temp aggregation**: `HAL_T_NUMBER_CONVERSION` ADC frames → `T_READY` event.
* **RPM windows**: `TASK_HAL_TIME_MOTOR_MAIN_RPM`, `TASK_HAL_TIME_BLOWER_RPM`.
* **Weight sensors**: kicked roughly once per second from the AC calculation cadence.

---

## 6) Error Handling & Recovery

* **UART**: timeout → set `*_ERROR` event → `Run()` calls `ReInitUart*()` and resumes.
* **BME688**: read error → `RecoveryBme688_Sensors()` power-cycles and re-inits I2C + sensors. On persistent failure, set `TaskSYS` error (`SysError_Bme688_*`).
* **AC Power**: `AcPowerOn()` validates mains presence; failures propagate as `OsResult_Error` to callers who typically notify `TaskSYS`.
* **Grinding**: overcurrent → `OsResult_MainMotorError`; undercurrent (fuse/driver) → `OsResult_MainMotorFuse`.
* **Safety switches**: back lid open triggers immediate motor stop & AC off and blocks until safe.

---

## 7) Extending the Module

### Add a new I2C sensor

1. Add a driver class similar to `TBme688` with `Init()`, `StartMeasurement()`, `GetData()`.
2. Mirror the **per-tick scheduler**: counter + event bit + `ProcessDataNewSensor()`.
3. Validate in task context, publish to `TaskPROC.UpdateNewSensor()`.
4. Add recovery path to `Recovery*()` if it can hang the I2C bus.

### Add a new UART peripheral

1. Create `ReInitUartX()` with port config and pinmux.
2. Add timeout counters in the tick, RX/TX complete ISR bridges, and events.
3. In `Run()`, on `*_RX_CPLT`, parse/validate, then publish to `TaskPROC`.

### Add a new actuator

* Encapsulate raw GPIO in a `THalSomething` class.
* Drive it from task context, with any timing synchronized from ISR (like heaters).
* Define safety interactions inside `CheckSwitches()` if relevant.

---

## 8) Testing & Debugging Playbook

1. **Enable diagnostics:** compile with `__DEBUG_HAL_OUTPUT_ENABLED` and use `DiagNotice()` in task context.
2. **Trace events:** temporarily log `resultBits` mask inside `Run()` to see what wakes the task.
3. **AC timing:** scope `AC_MAIN_Pin` and verify `ProcessHeater()` runs every half-cycle; adjust `TASK_HAL_AC_MAX_PWM_HEATER` if needed.
4. **ADC sanity:** log raw `adc*` and scaled `result*Current`; confirm scale constants match hardware.
5. **BME688:** on errors, log status bits (`BME688_VALID_DATA`, `BME688_NEW_DATA_MSK`, `gas_index`, `meas_index`); confirm recovery toggles sensor power pins.
6. **UART sensors:** induce disconnect and verify `*_ERROR` → `ReInitUart*()` path works and recoveries resume reads.
7. **Safety:** open back lid during grinding; verify immediate stop and blocking until closed.

---

## 9) ASCII Sequence Snapshots

### ADC → AC current window

```
ISR (DMA Cplt)  -> AdcConversionComplete()
                  ├─ push samples to ring buffers
                  ├─ if window full: set EVENT_AC_CALCULATE
                  └─ temp accumulators → EVENT_T_READY periodically
TaskHAL::Run()   -> if EVENT_AC_CALCULATE: CalculatingAcCurrents() → jam checks → publish
```

### BME688 schedule & read

```
Tick ISR        -> decrement per-sensor counters
                 -> when 0: set EVENT_GET_BME688_X and reset counter
TaskHAL::Run()  -> on EVENT_GET_BME688_X: ProcessDataBme688()
                 -> publish to TaskPROC or recover I2C on error
```

### Heater PWM (phase-angle)

```
EXTI(AC_MAIN)   -> HandlerGpioInterrupt()
                 ├─ ProcessHeater(): PulseOn/Off channels based on counterPwmHeater
                 └─ ProcessAcPhase(): counterPwmHeater = (counter+1) % MAX
```

---

## 10) Glossary

* **HAL**: STM32 hardware abstraction layer.
* **EventGroup**: bitmask synchronization (similar to FreeRTOS Event Groups).
* **Queue**: inter-task message buffer.
* **ISR**: interrupt service routine; keep work minimal.
* **DMA**: direct memory access; used for ADC sample transfers.
* **BME688**: Bosch gas + T/P/H sensor over I2C.
* **PTC**: Positive Temperature Coefficient heater.

---
## 12) File Map (from this module)

* **`TaskHAL.cpp`** – this file (task logic).
* **`TaskHAL.hpp`** – declarations, constants, event masks, member fields.
* **`TaskSYS.*`** – system state/events and watchdog.
* **`TaskPROC.*`** – application-level sensor value consumers.
* **`TaskCHM.*`** – chamber control (left/right motors).
* **`TaskWD.*`** – watchdog task.
* **`TaskSD.*`** – logging (optional).
* **`THalHeaters.*`, `TMotChambers.*`, `THalDcFans.*`, `TGpio.*`** – peripheral wrappers.

---

### Appendix A – Quick API Reference (from TaskHAL.cpp)

**Lifecycle**

* `EOsResult Init()`
* `EOsResult CreateOsResources()`
* `void CreateTask()` / `void CreateTaskStatic()`
* `void Run()`

**Commands**

* `EOsResult SendSysCommand(ESysCommand cmd, u8* data)`
* Event bits consumed: `TASK_HAL_CMD_START`, `TASK_HAL_CMD_GRINDING`, `TASK_HAL_CMD_STOP_ALL`, `TASK_HAL_EVENT_SYS_COMMAND`, sensor/uart events.

**Heaters & Motors**

* `EOsResult AcPowerOn()/AcPowerOff()`
* `EOsResult StartMainMotor(EMotorDir); void StopMainMotor()`
* `void ProcessGrinding(); void ProcessStopAll(); void RecoveryStateParts()`

**Sensors**

* `TBme688Sensors* GetPointerBme688_Left/Right/Intake/Exhaust()`
* `s8 GetTemperaturePadLeft/Right()`; `s8 GetTemperaturePtcIntake/Internal()`
* `u16 GetAverageAcPowerCurrent()`

**ISRs (bridges)**

* `SetEventAdcConvCpltFromISR() / SetEventAdcErrorFromISR()`
* `HandlerGpioInterrupt(u16 pin)`
* `SetEventUart{3,4}{Tx,Rx}CpltFromISR()`; `SetEventUart{3,4}ErrorFromISR()`

---
