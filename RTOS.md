 # Introduction to RTOS with STM Microcontrollers

This document provides an in-depth introduction and tutorial on Real-Time Operating Systems (RTOS) in the context of STM32 microcontrollers. The aim is to give a beginner-friendly but technically detailed explanation of how RTOS works, why it is used, and how to implement it step by step on STM32 boards.

---

## 1. What is an RTOS?

A **Real-Time Operating System (RTOS)** is a lightweight operating system designed to manage hardware resources and execute multiple tasks (threads) with precise timing guarantees. Unlike a general-purpose OS (Windows, Linux), an RTOS prioritizes **deterministic response times**. This makes it essential for embedded systems where timing is critical.

### Key Features of RTOS

- **Task scheduling**: Multiple tasks can run seemingly in parallel.
    
- **Determinism**: Tasks execute within predictable time windows.
    
- **Priorities**: Higher-priority tasks pre-empt lower-priority tasks.
    
- **Inter-task communication**: Queues, semaphores, and message buffers.
    
- **Resource management**: Mutexes prevent resource conflicts.
    

### Why use an RTOS with STM32?

- Simplifies handling multiple concurrent processes (e.g., motor control, sensor acquisition, communication).
    
- Provides better code modularity compared to bare-metal superloops.
    
- Scales well when projects grow beyond simple loops.
    

---

## 2. RTOS vs. Bare-Metal (Superloop)

|Feature|Bare-Metal Superloop|RTOS|
|---|---|---|
|Task management|All tasks in one loop|Independent tasks (threads)|
|Timing|Cooperative, manual delays|Pre-emptive, scheduler-driven|
|Scalability|Becomes messy with many tasks|Handles complex systems gracefully|
|Determinism|Harder to guarantee|Built-in real-time guarantees|

---

## 3. Popular RTOS Options for STM32

1. **FreeRTOS (most common)**
    
    - Open-source, widely supported.
        
    - Officially supported in STM32CubeIDE.
        
    - Excellent community and documentation.
        
2. **Zephyr RTOS**
    
    - Newer, Linux Foundation backed.
        
    - More modular, but heavier than FreeRTOS.
        
3. **RTX (CMSIS-RTOS)**
    
    - ARM’s official RTOS.
        
    - Integrated with Keil uVision.
        

For this tutorial, we will use **FreeRTOS**.

---

## 4. Setting Up FreeRTOS on STM32

### Step 1: Tools Required

- **STM32 Nucleo or Discovery board** (e.g., STM32F401RE, STM32F446RE).
    
- **STM32CubeIDE** (ST’s official IDE with FreeRTOS support).
    
- **STM32CubeMX** (for hardware configuration).
    

### Step 2: Project Setup

1. Open **STM32CubeIDE**.
    
2. Create a new project → Select your STM32 board.
    
3. Enable **FreeRTOS** under Middleware in CubeMX.
    
4. Configure system clock, GPIO, UART as needed.
    
5. Generate code → CubeIDE will include FreeRTOS files.
    

### Step 3: Understanding FreeRTOS Files

- **FreeRTOSConfig.h** – Main configuration file (tick rate, priorities).
    
- **tasks.c** – RTOS kernel task management.
    
- **queue.c, semphr.c** – Inter-task communication.
    

---

## 5. Creating Tasks in FreeRTOS

A task is like a small program that runs independently.

### Example Code: Two Tasks Blinking LEDs

```c
#include "FreeRTOS.h"
#include "task.h"
#include "stm32f4xx_hal.h"

void Task_LED1(void *pvParameters) {
    while(1) {
        HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5); // Toggle LED1
        vTaskDelay(500 / portTICK_PERIOD_MS); // 500 ms delay
    }
}

void Task_LED2(void *pvParameters) {
    while(1) {
        HAL_GPIO_TogglePin(GPIOB, GPIO_PIN_0); // Toggle LED2
        vTaskDelay(1000 / portTICK_PERIOD_MS); // 1s delay
    }
}

int main(void) {
    HAL_Init();
    SystemClock_Config();
    MX_GPIO_Init();

    xTaskCreate(Task_LED1, "LED1", 128, NULL, 1, NULL);
    xTaskCreate(Task_LED2, "LED2", 128, NULL, 1, NULL);

    vTaskStartScheduler();

    while(1) {}
}
```

### Key Concepts in Example

- `xTaskCreate()` → Creates a new task.
    
- `vTaskDelay()` → Non-blocking delay (lets scheduler switch to other tasks).
    
- `vTaskStartScheduler()` → Starts the FreeRTOS kernel.
    

---

## 6. RTOS Scheduling and Priorities

- **Pre-emptive scheduling**: Higher-priority task interrupts lower-priority.
    
- **Round-robin**: Same-priority tasks share CPU time equally.
    
- **Idle task**: Runs when no other task is ready.
    

Example: If a motor control task is **high priority** and LED blinking is **low priority**, the motor task always runs first.

---

## 7. Inter-Task Communication

### Queues

- Pass messages between tasks safely.
    

```c
xQueueHandle queue;
int value = 10;
xQueueSend(queue, &value, portMAX_DELAY);
xQueueReceive(queue, &value, portMAX_DELAY);
```

### Semaphores

- Synchronization between tasks or with ISRs.
    
- Binary semaphore = task signaling.
    

### Mutexes

- Prevents resource conflicts (e.g., two tasks using UART).
    

---

## 8. Using FreeRTOS with Interrupts (ISR)

In embedded systems, ISRs (Interrupt Service Routines) often need to communicate with tasks.

- Use **FromISR APIs** (`xQueueSendFromISR`, `xSemaphoreGiveFromISR`).
    
- Keep ISRs short, defer work to tasks.
    

---

## 9. Debugging FreeRTOS on STM32

- **CubeIDE FreeRTOS Debugger** → Task state visualization.
    
- **Tracealyzer** → Advanced tracing (optional tool).
    
- **UART logging** → Print debug messages from tasks.
    

---

## 10. Example Project: Sensor + UART + LED

- **Task 1**: Read sensor (e.g., temperature) every 100 ms.
    
- **Task 2**: Send sensor data over UART.
    
- **Task 3**: Blink status LED.
    
- Tasks communicate via a **queue** (sensor data → UART task).
    

---

## 11. Best Practices

- Keep tasks small and modular.
    
- Assign appropriate stack size (avoid stack overflow).
    
- Use semaphores/mutexes for shared peripherals.
    
- Avoid long blocking calls in tasks.
    
- Use `vTaskDelay()` instead of `HAL_Delay()` (blocking).
    

---

## 12. Next Steps for Learning

1. Experiment with task creation and priorities.
    
2. Implement inter-task communication using queues.
    
3. Add interrupt handling with ISRs and semaphores.
    
4. Explore FreeRTOS advanced features: timers, event groups.
    
5. Try porting to more complex projects (motor control, communication protocols).
    

---

# Conclusion

An RTOS like FreeRTOS on STM32 simplifies managing multiple tasks, ensures real-time behavior, and improves modularity in embedded systems. By learning how to create tasks, schedule them, and manage communication, you gain the tools to build scalable and reliable embedded applications.

---

Would you like me to also create a **step-by-step lab exercise** (e.g., Task1 = LED blink, Task2 = UART print, Task3 = Button ISR) that you can directly implement on your STM32 board as a guided practice?