### Hardware Architecture: The "Lean" Approach
I think the biggest bottleneck in the proposed plan is the Arduino nano. Nanos are 8-biut and slow and trying to coordinate two them with an ESP32 will cause "jitter" (the motors won't start and stop at the exact same time) which is bad for a parallel robot where the links depend on each other.

**The Recommended Setup: Laptop + Single ESP32**
A single ESP is more than powerful enough to handle the two motors, two encoders, and PID loops simultaneously.

**Laptop:** Handles the "Brain" (CV, Inverse Kinematics, Trajectory Planning)
**ESP32:** Handles the "Muscle" (High-speed encoder reading, Dual PID loops, Motor Driver PWM)
**Connection:** USB-Serial

### Communication and Software Plan
Since we want to use the Laptop for IK and CV, here is the "Data Flow" for the prototype:

**Phase A: Laptop (Python)**
Instead of just simulation, the python script will now act as the controller
- **Calculate IK:** Use the existing `all_ik_solutions` function to find the theta1 and theta2 for our target (x,y)
- **Convert to Counts:** Convert those angles into encoder "ticks" based on the gear ration (131:1) and encoder PPR (Pulses Per Revolution)
- **Send Packet:** Send a simple string over Serial to the ESP32 `<target1, target2>`

**Phase B: The ESP32 (C++)**
- **Listen:** Parse the incoming serial string to get the new `target1` and `target2`
- **Compute PID:** Run two independent PID loops (one for each motor) in the `loop()`
- **Drive:** Send PWM signals to the motor drivers (L298N)

### Writing the script to send serial string to the ESP

#### Connection and Protocol
- **Hardware Connection:** We will be using a standard USB-C or Micro-USB cable (depending on our ESP model) connecting the laptop to the ESP
- **Communication Protocol:** We will use UART serial communication. Python uses the `pyserial` library to send strings, and the ESP32 uses the built in `Serial` library to listen

#### communication.py
**Hardware and Serial Config**
`SERIAL_PORT` : This is the address of your microcontroller on your computer. This is the port that the computer will use to send messages through to the ESP
`BAUD_RATE`: This is the speed of data transmission. 115200 is very common high speed setting for microcontrollers. If this doesn't match the code on your ESP32 then we'll get gibberish characters.
- Baud rate is specifically the number of signaling events per second
- A signaling event also called a symbol is one time interval where the voltage on the wire is held at a defined level
- For UART, 1 baud = 1 bit per second because UART only has two voltage levels, either high or low
- Baud rate is better referred to as something that determines the fixed time slot used for the receiver to interpret the signal
- It controls when the receiver is allowed to look
- Tbit = 1/115200 so the receiver samples the message being sent every 8.68 microsecond
- Each character has 1 start bit, 8 data bits and 1 stop bit so **10 bits per character**
- If we send `34.6, 53.2` so we send 10 characters each with a value of 10 bits with a baud rate of 115200 it would take 100/115200 seconds to send the message over UART
`GEAR_RATIO`: The moto spins 131 times for every 1 single rotation of the output shafter. Provides high torque but requires more encoder pulses to track movement
`ENCODER_PPR` The magnetic encoder on the motor's rear shaft produces 11 pulses per single motor revolution
- Output shaft turns 1 full revolution
- Gearbox forces motor shaft to turn 131 revolutions
- Encoder sees 131 revolutions
- Each motor revolution produces 16 counts
- 131 x 16 = 2096
`TICKS_PER_REV`: Encoder counts per revolution
`TICKS_PER_RAD`: Ticks in radians. There are 917.36 ticks per radian. All math functions accept radians as inputs not degrees

**Robot Geometry**
`A1 and A2`: Coordinates for left motor and right motor

**Physical Calibration**
`HOME_THETA1 AND HOME_THETA2`: Homed position. Measure the angle that is made by the proximal arm with the x-axis

**Kinematic Logic**
`clamp` helper

```python
def clamp(x, lo=-1.0, hi=1.0): 
	return max(lo, min(hi, x))
```

This is a safety net for the math. The arcos function only works for values between -1 and 1. If a tiny floating-point rounding error makes a number 1.0000000000001, the program would crash. This forces the number to stay within the valid range.

`solve_arm(A)`:

```python
v = E - A
	r = np.linalg.norm(v)
	# Check reachability
	if not (abs(L1 - L2) <= r <= (L1 + L2)):
		return None
```

`v` is a vector pointing from the motor base A to the target point E
`r` is the straight line distance between the motor and the target
The if statement is the **reachability check**. A triangle cannot be formed if one side is longer than the other two combined. If r is too long (target is too far) or too short (target is inside the robot's own base) the function returns `None`

`The Triangle Math (Law of Cosines)` 
To find the motor angle, we split it into two parts: Beta and Alpha
```python
beta = np.arctan2(v[1], v[0])
c = clamp((L1**2 + r**2 - L2**2) / (2.0 * L1 * r))
alpha = np.arccos(c)
```

- Beta is the angle of the line connect the motor to the target. It tells us the general direction the arm needs to point
- Alpha is the internal angle of the proximal arm (L1) relative to that center line. We use the law of cosines to get this value and
![[Pasted image 20260211141812.png]]

`Choosing the Elbow Configuration (Mode)
Every robot arm has two ways to reach a point: "Elbow Up" or "Elbow Down" or in our case its "Elbow Left or Elbow Right"
```python
# We select the "elbow outward" solutions based on previous logic
# For Arm 1 (Left), we usually want the elbow-left solution
# For Arm 2 (Right), we usually want the elbow-right solution
if A[0] < 0: # Left Motor
	return beta + alpha
else: # Right Motor
	return beta - alpha
```


`Final output`
The final output are the motor angles we want:
```python
th1 = solve_arm(A1)
th2 = solve_arm(A2)
```


**Main Execution Loop**
#### Opening the serial pipeline

```python
  try:
	ser = serial.Serial(SERIAL_PORT, BAUD_RATE, timeout=0.1)
	time.sleep(2) # Allow ESP32 to reset
	print(f"--- Connection established on {SERIAL_PORT} ---")
except Exception as e:
	print(f"Error: Could not open serial port {SERIAL_PORT}. {e}")
	return
```

serial.Serial opens the communication line and then we set a 2 second sleep timer to make sure we give it enough time to reboot instead of sending the data immediately

#### User Input Loop
```python
user_input = input("\nTarget X Y: ")
x, y = map(float, user_input.split())
```

The script pauses and waits for you. When you type a value in the terminal like `0 150`, it splits the string at the space and converts those two pieces of text into floating point numbers

#### Calculating Relative Movement
```python
t1 = int((th1 - HOME_THETA1) * TICKS_PER_RAD)
t2 = int((th2 - HOME_THETA2) * TICKS_PER_RAD)
```

With the inverse kinematics calculations we get an absolute angle like 110 degrees but the motors do not know where 0 degrees is. They only know where they started when you turned them on which is the Home position
- **`th1 - HOME_THETA1`**: This finds the **difference** (delta) between where the arm is now (90°) and where it needs to go.
- **Multiplying by `TICKS_PER_RAD`**: Converts that angular difference into the number of encoder "clicks" the motor needs to turn.
- **`int(...)`**: Motors can't move 0.5 ticks, so we round to the nearest whole number.

#### Data packaging
```python
# Format and send to ESP32
packet = f"<{t1},{t2}>\n"
ser.write(packet.encode())
```

The ESP32 needs to understand which number belongs to Motor 1 and and which to Motor 1

**The Format**: The code wraps the numbers in `< >` brackets and separates them with a comma. This is a common practice called **delimiters**. It tells the ESP32: _"Start reading at '<', put the first number in variable A, ignore the comma, put the second number in variable B, and stop at '>'."_

**`.encode()`**: This converts the Python string into **bytes**, which is the "language" of serial cables.

### How the ESP32 handles the packet

The ESP32 usually follows this logic in its code:
1. **Wait for the Start Marker:** It ignores everything until it sees the `<` character.
2. **Read the First Number:** It collects all characters until it hits the `,`. It then converts that text (like `"500"`) into a real integer for **Motor A**.
3. **Read the Second Number:** It collects all characters until it hits the `>`. It converts that text into an integer for **Motor B**.
4. **Stop:** It ignores the `\n` (newline) and executes the movement.

#### Graceful Exit
```python
ser.close()
print("Serial port closed.")
```

If you type 'q', the loop breaks and the script releases the Serial port. If you don't close the port, you might find that other programs (or the next run of this script) can't access the COM port because it’s still "busy."

### 1. The "Point-to-Point" (PTP) Approach

The Python script is now doing **Point-to-Point** communication. Instead of sending 100 tiny incremental steps, it sends one single "Target Ticks" packet.
- **Simulation:** Python says "Be at 1.1 degrees... now 1.2 degrees... now 1.3 degrees."
- **Hardware:** Python says "Go to 5000 ticks," and then it sits back and waits for your next command.

### 2. Motion Profiling on the ESP32
If you sent a "Go to 5000 ticks" command and the ESP32 instantly applied full power, the robot would jerk violently, potentially breaking the 3D-printed parts or slipping the gears.

To fix this, you usually use a library on the ESP32 like **AccelStepper** or a custom **PID controller**. These libraries handle the "Smoothstep" (acceleration and deceleration) internally.
- **Acceleration:** The ESP32 starts the motor slowly.
- **S-Curve/Smoothstep:** It mimics that math you have ($3\tau^2 - 2\tau^3$) to ensure the start and stop are gentle.
- **Deceleration:** As it approaches the target ticks, it slows down to prevent overshooting.

---
### Do you still need it in Python?
Only if you want to perform **Path Following** (like drawing a circle or a straight line).
- **If you want the robot to move in a straight line:** You _do_ still need that math in Python. You would break the line into 50 small $(x, y)$ points and send them one by one.
- **If you just want to get to a target:** You let the ESP32 handle the ramp-up and ramp-down logic.


### Testing Workflow
### 1. The Workspace Setup: One Window or Two?

You can absolutely run them from the **same VS Code workspace**, and I actually recommend it so you can quickly reference your Python geometry (like `L1`, `L2`) while writing your C++ code.
- **Structure:** Create a main project folder. Inside, have a `firmware/` folder (your PlatformIO project) and a `scripts/` folder (your Python code).
- **VS Code Tip:** You can open both folders in one window using `File > Add Folder to Workspace...`.
- **Running them:** You will use the **PlatformIO terminal** to flash the ESP32 and a **standard Python terminal** to run your script.

---

### 2. The Flashing & Testing Process
Your mental model is spot on, but there is one "gotcha" regarding the Serial Port: **only one program can talk to the ESP32 at a time.**
1. **Develop:** Write your C++ code in PlatformIO.
2. **Upload:** Click the "Upload" arrow in PlatformIO. This uses the Serial port to flash the chip.
3. **The Serial Hand-off:** Once the upload is finished, **close the PlatformIO Serial Monitor** (if you have it open). If you don't, your Python script will throw an "Access Denied" error because PlatformIO is "holding the line."
4. **Execute:** Run your Python script. It will open the port, send the coordinates, and the motors should move.

---

### 3. ESP32 Implementation Strategy

For the firmware, we need to handle three main tasks simultaneously. Since we are using an ESP32, we want to make sure the motor control is "non-blocking" (the robot shouldn't stop thinking while it's moving).
#### Key Components we'll need:
- **Serial Parser:** To look for the `<` and `>` markers and extract `t1` and `t2`.
- **Encoder Setup:** Using interrupts or the ESP32's hardware PCNT (Pulse Counter) to track those 2,096 ticks per revolution accurately.
- **PID Controller:** A loop that says: _"I am at tick 0, I need to be at tick 500, let me calculate the motor power needed to get there smoothly."_
- **PWM Output:** Sending the actual power to the H-Bridge motor drivers.

### Writing the script to receive serial string

## Phase 1: Setting up the Workspace

We want a "Clean Room" approach. One main folder, two distinct projects.

1. **Create a Root Folder:** Name it something like `FiveBar_Project`.
    
2. **The Python Side:** * Create a folder inside called `python_control`.
    
    - Save your Python script there as `main.py`.
        
3. **The Firmware Side:**
    
    - Open **VS Code**.
        
    - Click the **PlatformIO icon** (the alien head) on the left sidebar.
        
    - Click **"Pick a folder"** or **"New Project"**.
        
    - **Project Name:** `esp32_firmware`
        
    - **Board:** Search for **"Seeed Studio XIAO ESP32S3"**.
        
    - **Framework:** Arduino.
        
    - **Location:** Save it inside your `FiveBar_Project` folder.
        

---

## Phase 2: The ESP32 "Listener" Code

We need to flash the ESP32 with code that knows how to:

1. Open the Serial port.
    
2. Look for the `<` and `>` markers.
    
3. Parse the two numbers.
    
4. Print them back to you so you can confirm it worked.
    

**Copy this into your `src/main.cpp` in PlatformIO:**

```cpp
#include <Arduino.h>

// Variables to store the target ticks from Python
long targetTicks1 = 0;
long targetTicks2 = 0;

void setup() {
    // Must match the Python BAUD_RATE
    Serial.begin(115200);
    while (!Serial); // Wait for Serial to initialize
    Serial.println("ESP32 Ready. Waiting for Python commands...");
}

void loop() {
    // Check if data is available
    if (Serial.available() > 0) {
        char startChar = Serial.read();

        // Look for the start marker '<'
        if (startChar == '<') {
            // Read the first integer (stops at the comma)
            targetTicks1 = Serial.parseInt();
            
            // Read the second integer (stops at the '>')
            targetTicks2 = Serial.parseInt();

            // Feedback: Print back what we received
            Serial.print("Received Ticks -> Motor 1: ");
            Serial.print(targetTicks1);
            Serial.print(" | Motor 2: ");
            Serial.println(targetTicks2);
        }
    }
}
```

### Tuning Guide (How to set Kp, Ki, Kd)

You will likely need to adjust the numbers at the top of the file:

1. **Start with `Kp` only (Ki = 0, Kd = 0):** Increase `Kp` until the robot gets to the target reasonably fast but starts to oscillate (wiggle back and forth) around the endpoint.
    
2. **Add `Kd`:** Increase `Kd` to stop the wiggling. It acts like a shock absorber. The arm should snap to position and stop dead.
    
3. **Add `Ki` (Last):** If the arm stops _near_ the target but not _exactly_ on it (e.g., target 500, arm stops at 495), increase `Ki` slightly to push it that last millimeter.


### 2. Power Connections (Crucial!)

The ESP32 cannot power these motors directly. You need an external 12V power supply (battery or wall adapter).

- **L298N "12V" Terminal:** Connect to your **External Power (+) (Red)**.
    
- **L298N "GND" Terminal:** Connect to **External Power (-) (Black)** AND **ESP32 "GND"**.
    
    - _Warning:_ If you don't connect the ESP32 Ground to the L298N Ground, the signals won't complete the circuit, and nothing will work.
        
- **L298N "5V" Terminal:**
    
    - **If your power source is < 12V:** You _can_ use this pin to power the ESP32 (connect to ESP32 "5V" or "VIN").
        
    - **Safest Option:** Leave this empty for now and just power the ESP32 via USB-C to your laptop.
        

#### **Encoder Power**

The encoders on the motors also need power to work.

- **Red Wire (Encoder VCC):** Connect to **ESP32 3.3V** (Do not use 5V, it might hurt the ESP32 data pins).
    
- **Black Wire (Encoder GND):** Connect to **ESP32 GND**.
    

---

### 3. The "Launch Sequence"

Once you have wired everything:

1. **Upload Firmware:** Connect ESP32 via USB. Flash the code using PlatformIO.
    
2. **Power Up:** Turn on your 12V Motor Power Supply.
    
3. **Physical "Zeroing":** Manually move the robot arms to the **HOME position** ($90^\circ$ straight up).
    
    - _Note:_ The ESP32 assumes "Current Ticks = 0" when it turns on. You must place it at Home _before_ you power the ESP32 on or press the Reset button.
        
4. **Run Python:** Open your terminal, run `python main.py`.
    
5. **Enter Command:** Type `0 150`.
    

**If you wire it up now, do you have a 12V power supply ready for the motors?**



**Left Motor (Motor 1)**

| ESP32 Pin | Connect To       | Component     | Function          |
| --------- | ---------------- | ------------- | ----------------- |
| GPIO 32   | ENA              | L298N Driver  | PWM Speed Control |
| GPIO 33   | IN1              | L298N Driver  | Direction A       |
| GPIO 25   | IN2              | L298N Driver  | Direction B       |
| GPIO 26   | Phase A (Yellow) | Motor Encoder | Reads Position    |
| GPIO 27   | Phase B (Green)  | Motor Encoder | Reads Direction   |

**Right Motor (Motor 2)**

| ESP32 Pin | Connect To       | Component     | Function          |
| --------- | ---------------- | ------------- | ----------------- |
| GPIO 14   | ENB              | L298N Driver  | PWM Speed Control |
| GPIO 12   | IN3              | L298N Driver  | Direction A       |
| GPIO 13   | IN4              | L298N Driver  | Direction B       |
| GPIO 18   | Phase A (Yellow) | Motor Encoder | Reads Position    |
| GPIO 19   | Phase B (Green)  | Motor Encoder | Reads Direction   |
```cpp
#include <Arduino.h>

// ============================================================
// PIN DEFINITIONS (ESP32-WROOM-32)
// ============================================================

// --- Motor 1 (Left) ---
#define M1_ENA  32  // PWM Speed Control
#define M1_IN1  33  // Direction Pin 1
#define M1_IN2  25  // Direction Pin 2
#define M1_ENC_A 26 // Yellow Wire (Phase A)
#define M1_ENC_B 27 // Green Wire (Phase B)

// --- Motor 2 (Right) ---
#define M2_ENB  14  // PWM Speed Control
#define M2_IN3  12  // Direction Pin 1
#define M2_IN4  13  // Direction Pin 2
#define M2_ENC_A 18 // Yellow Wire (Phase A)
#define M2_ENC_B 19 // Green Wire (Phase B)

// ============================================================
// PID CONSTANTS (TUNING REQUIRED)
// ============================================================
// Start with Kp, then add Ki slightly, then Kd to dampen.
float Kp = 1.2;  // Power proportional to error 0.5 - 5.0
float Ki = 0.05; // Power scales with *time* (fixes small gaps) 0.01 - 0.5
float Kd = 0.1;  // Power opposes *change* (dampens oscillation) 0.05 to 1.0

// Deadzone (Stops motor buzz when close)
int deadzone = 5; 

// PWM Properties
const int freq = 5000;
const int resolution = 8;
const int M1_PWM_CHANNEL = 0;
const int M2_PWM_CHANNEL = 1;

// ============================================================
// GLOBAL VARIABLES
// ============================================================
volatile long currentTicks1 = 0;
volatile long currentTicks2 = 0;
long targetTicks1 = 0;
long targetTicks2 = 0;

// PID Variables
long prevError1 = 0;
long prevError2 = 0;
float integral1 = 0;
float integral2 = 0;
unsigned long lastTime = 0;

// ============================================================
// INTERRUPT SERVICE ROUTINES
// ============================================================
void IRAM_ATTR readEncoder1() {
  if (digitalRead(M1_ENC_B) > 0) {
    currentTicks1++;
  } else {
    currentTicks1--;
  }
}

void IRAM_ATTR readEncoder2() {
  if (digitalRead(M2_ENC_B) > 0) {
    currentTicks2--; // Reversed for symmetry if needed
  } else {
    currentTicks2++;
  }
}

// ============================================================
// MOTOR DRIVER LOGIC
// ============================================================
void setMotor(int pwmChannel, int in1, int in2, int speed) {
  // Constrain speed to valid PWM range
  if (speed > 255) speed = 255;
  if (speed < -255) speed = -255;

  if (speed > 0) {
    digitalWrite(in1, HIGH);
    digitalWrite(in2, LOW);
    ledcWrite(pwmChannel, speed);
  } else if (speed < 0) {
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    ledcWrite(pwmChannel, abs(speed));
  } else {
    digitalWrite(in1, LOW);
    digitalWrite(in2, LOW);
    ledcWrite(pwmChannel, 0);
  }
}

// ============================================================
// SETUP
// ============================================================
void setup() {
  Serial.begin(115200);

  // Motor 1
  pinMode(M1_IN1, OUTPUT);
  pinMode(M1_IN2, OUTPUT);
  pinMode(M1_ENC_A, INPUT_PULLUP);
  pinMode(M1_ENC_B, INPUT_PULLUP);
  ledcSetup(M1_PWM_CHANNEL, freq, resolution);
  ledcAttachPin(M1_ENA, M1_PWM_CHANNEL);

  // Motor 2
  pinMode(M2_IN3, OUTPUT);
  pinMode(M2_IN4, OUTPUT);
  pinMode(M2_ENC_A, INPUT_PULLUP);
  pinMode(M2_ENC_B, INPUT_PULLUP);
  ledcSetup(M2_PWM_CHANNEL, freq, resolution);
  ledcAttachPin(M2_ENB, M2_PWM_CHANNEL);

  // Interrupts
  attachInterrupt(digitalPinToInterrupt(M1_ENC_A), readEncoder1, RISING);
  attachInterrupt(digitalPinToInterrupt(M2_ENC_A), readEncoder2, RISING);

  Serial.println("ESP32 PID Ready.");
}

// ============================================================
// MAIN LOOP
// ============================================================
void loop() {
  unsigned long now = micros();
  float dt = (now - lastTime) / 1000000.0;
  lastTime = now;

  // --- NEW ROBUST SERIAL PARSING ---
  if (Serial.available() > 0) {
    String data = Serial.readStringUntil('\n'); // Read the whole line
    if (data.startsWith("<") && data.endsWith(">")) {
      // Remove the brackets and parse
      data = data.substring(1, data.length() - 1);
      int commaIndex = data.indexOf(',');
      if (commaIndex != -1) {
        targetTicks1 = data.substring(0, commaIndex).toInt();
        targetTicks2 = data.substring(commaIndex + 1).toInt();
        
        integral1 = 0;
        integral2 = 0;
        
        // SEND IMMEDIATE FEEDBACK
        Serial.print("Target Recv: ");
        Serial.print(targetTicks1);
        Serial.print(",");
        Serial.println(targetTicks2);
      }
    }
  }

  // 3. Compute Errors
  long error1 = targetTicks1 - currentTicks1;
  long error2 = targetTicks2 - currentTicks2;

  // 4. PID Calculations (Motor 1)
  integral1 += error1 * dt;
  float derivative1 = (error1 - prevError1) / dt;
  float output1 = (Kp * error1) + (Ki * integral1) + (Kd * derivative1);
  prevError1 = error1;

  // 5. PID Calculations (Motor 2)
  integral2 += error2 * dt;
  float derivative2 = (error2 - prevError2) / dt;
  float output2 = (Kp * error2) + (Ki * integral2) + (Kd * derivative2);
  prevError2 = error2;

  // 6. Anti-Windup (Integral Clamping)
  // Prevents the "I" term from growing huge if the motor stalls
  if (integral1 > 1000) integral1 = 1000;
  if (integral1 < -1000) integral1 = -1000;
  if (integral2 > 1000) integral2 = 1000;
  if (integral2 < -1000) integral2 = -1000;

  // 7. Drive Motors
  // If close to target, cut power to prevent jitter
  if (abs(error1) < deadzone) {
    setMotor(M1_PWM_CHANNEL, M1_IN1, M1_IN2, 0);
    integral1 = 0; // Clear built-up pressure
  } else {
    setMotor(M1_PWM_CHANNEL, M1_IN1, M1_IN2, output1);
  }

  if (abs(error2) < deadzone) {
    setMotor(M2_PWM_CHANNEL, M2_IN3, M2_IN4, 0);
    integral2 = 0;
  } else {
    setMotor(M2_PWM_CHANNEL, M2_IN3, M2_IN4, output2);
  }
  
  // Optional: Debugging (Comment out for production)
  // Serial.printf("E1:%ld  Out1:%.2f\n", error1, output1);
}
```


```cpp
#include <Arduino.h>

void setup() {
  // Must match the BAUD_RATE in your Python script
  Serial.begin(115200); 
  delay(1000);
  Serial.println("ESP32 Online. Waiting for Python command...");
}

void loop() {
  // Check if Python has sent any data
  if (Serial.available() > 0) {
    
    // Read the incoming string until the newline character
    String incoming = Serial.readStringUntil('\n');
    incoming.trim(); // Remove any stray spaces

    // Check for our specific packet format: <number,number>
    if (incoming.startsWith("<") && incoming.endsWith(">")) {
      
      // Remove the brackets
      String payload = incoming.substring(1, incoming.length() - 1);
      
      // Find the comma separating the two values
      int commaIndex = payload.indexOf(',');

      if (commaIndex != -1) {
        // Extract the strings
        String s1 = payload.substring(0, commaIndex);
        String s2 = payload.substring(commaIndex + 1);

        // Convert strings to actual integers
        int tick1 = s1.toInt();
        int tick2 = s2.toInt();

        // Send confirmation back to Python
        Serial.print("SUCCESS: Received Ticks [M1: ");
        Serial.print(tick1);
        Serial.print(" | M2: ");
        Serial.print(tick2);
        Serial.println("]");
      } else {
        Serial.println("ERROR: Missing comma in packet.");
      }
    } else {
      Serial.print("ERROR: Invalid format. Received: ");
      Serial.println(incoming);
    }
  }
}
```


### Working PID for one motor
```cpp
#include <Arduino.h>

// --- Motor 1 Pins (Left) ---
#define M1_ENA  32  // PWM Speed Control
#define M1_IN1  33  // Direction Pin 1
#define M1_IN2  25  // Direction Pin 2
#define M1_ENC_A 26 // Encoder Phase A (Yellow)
#define M1_ENC_B 27 // Encoder Phase B (Green)

// --- TUNED PID PARAMETERS ---
float Kp = 0.9;    // The "Drive": Power proportional to distance
float Ki = 0.5;    // The "Pressure": Helps overcome friction near the target
float Kd = 0.2;   // The "Brakes": Dampens momentum to prevent overshoot

// --- Constraints & Safety ---
int deadzone = 2;       // Stop completely when within 2 ticks
int maxPWM = 200;       // Cap power (out of 255) for safer testing
float iLimit = 100.0;   // Integral Clamp: Prevents the 'I' term from growing too large

// --- Global Variables ---
volatile long currentTicks = 0;
long targetTicks = 2096; 
bool isRunning = false;

long prevError = 0;
float integral = 0;
unsigned long lastTime = 0;
unsigned long lastPrint = 0;

// --- PWM Properties ---
const int freq = 5000;
const int resolution = 8;
const int M1_PWM_CHANNEL = 0;

// Interrupt logic: Handles position tracking
void IRAM_ATTR readEncoder() {
  if (digitalRead(M1_ENC_B) > 0) {
    currentTicks++;
  } else {
    currentTicks--;
  }
}

// Driver logic: Converts PID output to physical motor movement
void driveMotor(int speed) {
  speed = constrain(speed, -maxPWM, maxPWM);

  if (speed > 0) {
    digitalWrite(M1_IN1, HIGH);
    digitalWrite(M1_IN2, LOW);
    ledcWrite(M1_PWM_CHANNEL, speed);
  } else if (speed < 0) {
    digitalWrite(M1_IN1, LOW);
    digitalWrite(M1_IN2, HIGH);
    ledcWrite(M1_PWM_CHANNEL, abs(speed));
  } else {
    digitalWrite(M1_IN1, LOW);
    digitalWrite(M1_IN2, LOW);
    ledcWrite(M1_PWM_CHANNEL, 0);
  }
}

void setup() {
  Serial.begin(115200);
  
  pinMode(M1_IN1, OUTPUT);
  pinMode(M1_IN2, OUTPUT);
  pinMode(M1_ENC_A, INPUT_PULLUP);
  pinMode(M1_ENC_B, INPUT_PULLUP);
  
  ledcSetup(M1_PWM_CHANNEL, freq, resolution);
  ledcAttachPin(M1_ENA, M1_PWM_CHANNEL);
  
  attachInterrupt(digitalPinToInterrupt(M1_ENC_A), readEncoder, RISING);

  Serial.println("\n--- FULL PID SYSTEM READY ---");
  Serial.println("Type 'g' to start 2096 tick move.");
  Serial.println("Type 'r' to stop and reset.");
}

void loop() {
  // 1. Handle Commands
  if (Serial.available() > 0) {
    char cmd = Serial.read();
    if (cmd == 'g') {
      currentTicks = 0;
      prevError = targetTicks;
      integral = 0;
      isRunning = true;
      Serial.println(">> GO: Precision move starting...");
    }
    if (cmd == 'r') {
      isRunning = false;
      driveMotor(0);
      currentTicks = 0;
      Serial.println(">> RESET: System zeroed.");
    }
  }

  // 2. PID Execution
  if (isRunning) {
    unsigned long now = micros();
    float dt = (now - lastTime) / 1000000.0; // Seconds
    lastTime = now;

    if (dt <= 0) dt = 0.001; // Avoid division by zero

    long error = targetTicks - currentTicks;

    // --- Proportional ---
    float P = Kp * error;

    // --- Integral ---
    integral += error * dt;
    // Anti-Windup: Clamp the integral to prevent it from spinning the motor out of control
    integral = constrain(integral, -iLimit, iLimit);
    float I = Ki * integral;

    // --- Derivative ---
    float derivative = (error - prevError) / dt;
    float D = Kd * derivative;
    prevError = error;

    // Final Output Calculation
    float totalOutput = P + I + D;

    // 3. Drive or Stop
    if (abs(error) < deadzone) {
      driveMotor(0);
      isRunning = false;
      Serial.print(">> TARGET REACHED. Final Pos: ");
      Serial.println(currentTicks);
    } else {
      driveMotor((int)totalOutput);
    }
  }

  // Debug Output
  if (millis() - lastPrint > 100) {
    if (isRunning) {
      Serial.printf("Pos: %ld | Err: %ld | P:%.1f I:%.1f D:%.1f\n", 
                    currentTicks, (targetTicks - currentTicks), 
                    Kp * (targetTicks - currentTicks), Ki * integral, Kd * ((targetTicks - currentTicks - prevError) / 0.1));
    }
    lastPrint = millis();
  }
}
```



### Working Firmware code for one motor
```cpp
#include <Arduino.h>

// =========================
// Pin Configuration
// =========================
#define M1_ENA    32   // PWM enable pin to motor driver
#define M1_IN1    33   // direction pin 1
#define M1_IN2    25   // direction pin 2
#define M1_ENC_A  26   // encoder A
#define M1_ENC_B  27   // encoder B

// =========================
// Control Parameters
// =========================
float Kp = 1.00f;
float Ki = 0.30f;
float Kd = 0.35f;

int deadzoneTicks = 2;      // consider "arrived" if |error| <= this
int maxPWM = 200;           // max drive command (0..255)
int minPWMToMove = 70;      // overcome static friction (tune this)

// Timeouts / telemetry
unsigned long moveTimeoutMs = 6000;   // fail-safe timeout per move
unsigned long telemetryPeriodMs = 100;

// =========================
// State Variables
// =========================
volatile long currentTicks = 0;       // encoder count (ISR updates)
long targetTicks = 0;                 // absolute target
bool isMoving = false;

float integral = 0.0f;
long prevError = 0;
unsigned long lastMicros = 0;
unsigned long moveStartMs = 0;
unsigned long lastTelemetryMs = 0;

// =========================
// Serial parser state
// Accepts frames: <123>, <-250>
// Also accepts line commands: S, Z
// =========================
char frameBuf[32];
uint8_t frameIdx = 0;
bool inFrame = false;

void IRAM_ATTR readEncoderISR() {
  // Direction via B phase on A rising
  if (digitalRead(M1_ENC_B)) {
    currentTicks++;
  } else {
    currentTicks--;
  }
}

long atomicReadTicks() {
  noInterrupts();
  long t = currentTicks;
  interrupts();
  return t;
}

void atomicWriteTicks(long v) {
  noInterrupts();
  currentTicks = v;
  interrupts();
}

void driveMotorRaw(int pwm) {
  pwm = constrain(pwm, -maxPWM, maxPWM);

  if (pwm > 0) {
    digitalWrite(M1_IN1, HIGH);
    digitalWrite(M1_IN2, LOW);
    ledcWrite(0, pwm);
  } else if (pwm < 0) {
    digitalWrite(M1_IN1, LOW);
    digitalWrite(M1_IN2, HIGH);
    ledcWrite(0, -pwm);
  } else {
    // Coast stop (safer default than hard brake for many drivers)
    digitalWrite(M1_IN1, LOW);
    digitalWrite(M1_IN2, LOW);
    ledcWrite(0, 0);
  }
}

void stopMotion(const char* reason) {
  driveMotorRaw(0);
  isMoving = false;
  integral = 0.0f;
  prevError = 0;
  Serial.printf("%s\n", reason);  // DONE / TIMEOUT / STOPPED / etc.
}

void startMoveToAbsolute(long absTarget) {
  targetTicks = absTarget;
  integral = 0.0f;
  prevError = 0;
  lastMicros = micros();
  moveStartMs = millis();
  isMoving = true;
  Serial.printf("ACK:%ld\n", targetTicks);
}

void handleLineCommand(const String& lineRaw) {
  String line = lineRaw;
  line.trim();

  if (line.length() == 0) return;

  // Single-letter commands
  if (line.equalsIgnoreCase("S")) {
    stopMotion("STOPPED");
    return;
  }
  if (line.equalsIgnoreCase("Z")) {
    atomicWriteTicks(0);
    Serial.println("ZEROED");
    return;
  }
  if (line.equalsIgnoreCase("P")) {
    long pos = atomicReadTicks();
    Serial.printf("POS:%ld\n", pos);
    return;
  }

  // Unknown line command
  Serial.printf("ERR:UNKNOWN_CMD:%s\n", line.c_str());
}

void pollSerial() {
  while (Serial.available() > 0) {
    char c = (char)Serial.read();

    // Framed move command starts
    if (c == '<') {
      inFrame = true;
      frameIdx = 0;
      continue;
    }

    // Framed move command ends
    if (c == '>' && inFrame) {
      frameBuf[frameIdx] = '\0';
      long val = atol(frameBuf);   // supports negative numbers

      // Move mode: absolute target
      // If you want relative mode instead, use:
      // long valRel = atol(frameBuf);
      // long val = atomicReadTicks() + valRel;
      startMoveToAbsolute(val);

      inFrame = false;
      frameIdx = 0;
      continue;
    }

    // Capture frame contents
    if (inFrame) {
      if (frameIdx < sizeof(frameBuf) - 1) {
        frameBuf[frameIdx++] = c;
      } else {
        inFrame = false;
        frameIdx = 0;
        Serial.println("ERR:FRAME_OVERFLOW");
      }
      continue;
    }

    // Non-framed line commands (S, Z, P)
    // We'll parse by lines using a small static line buffer.
    static char lineBuf[32];
    static uint8_t lineIdx = 0;

    if (c == '\n' || c == '\r') {
      if (lineIdx > 0) {
        lineBuf[lineIdx] = '\0';
        handleLineCommand(String(lineBuf));
        lineIdx = 0;
      }
    } else {
      if (lineIdx < sizeof(lineBuf) - 1) {
        lineBuf[lineIdx++] = c;
      } else {
        lineIdx = 0;
        Serial.println("ERR:LINE_OVERFLOW");
      }
    }
  }
}

void runPID() {
  if (!isMoving) return;

  unsigned long nowUs = micros();
  float dt = (nowUs - lastMicros) * 1e-6f;
  lastMicros = nowUs;

  // Guard dt
  if (dt <= 0.0f || dt > 0.2f) dt = 0.001f;

  long pos = atomicReadTicks();
  long error = targetTicks - pos;

  // Arrival check
  if (abs(error) <= deadzoneTicks) {
    stopMotion("DONE");
    return;
  }

  // Timeout fail-safe
  if (millis() - moveStartMs > moveTimeoutMs) {
    stopMotion("TIMEOUT");
    return;
  }

  // PID
  integral += error * dt;
  integral = constrain(integral, -300.0f, 300.0f);

  float derivative = (error - prevError) / dt;
  prevError = error;

  float u = Kp * error + Ki * integral + Kd * derivative;

  // Add minimum PWM to break stiction when command is non-zero
  int pwm = (int)u;
  if (pwm > 0) pwm = max(pwm, minPWMToMove);
  if (pwm < 0) pwm = min(pwm, -minPWMToMove);

  driveMotorRaw(pwm);

  // Telemetry
  if (millis() - lastTelemetryMs >= telemetryPeriodMs) {
    lastTelemetryMs = millis();
    Serial.printf("MOVING POS:%ld TARGET:%ld ERR:%ld PWM:%d\n", pos, targetTicks, error, pwm);
  }
}

void setup() {
  Serial.begin(115200);
  delay(100);

  pinMode(M1_IN1, OUTPUT);
  pinMode(M1_IN2, OUTPUT);
  pinMode(M1_ENC_A, INPUT_PULLUP);
  pinMode(M1_ENC_B, INPUT_PULLUP);

  // ESP32 LEDC PWM channel 0, 5 kHz, 8-bit
  ledcSetup(0, 5000, 8);
  ledcAttachPin(M1_ENA, 0);

  attachInterrupt(digitalPinToInterrupt(M1_ENC_A), readEncoderISR, RISING);

  driveMotorRaw(0);

  Serial.println("READY");
  Serial.println("CMDS: <ticks> | S(stop) | Z(zero) | P(position)");
}

void loop() {
  pollSerial();
  runPID();
}

```

### Python working script
```python
import serial
import time
import sys

PORT = "COM4"        # Update this
BAUD = 115200

ESP_BOOT_WAIT_S = 2.5
MOVE_WAIT_TIMEOUT_S = 10.0

def open_serial():
    ser = serial.Serial(
        PORT,
        BAUD,
        timeout=0.05,        # read timeout
        write_timeout=0.2
    )
    return ser

def read_available_lines(ser, duration_s=0.4, prefix="[ESP]"):
    """Read and print whatever is available for duration_s."""
    t0 = time.time()
    while time.time() - t0 < duration_s:
        if ser.in_waiting > 0:
            line = ser.readline().decode("utf-8", errors="ignore").strip()
            if line:
                print(f"{prefix} {line}")
        else:
            time.sleep(0.005)

def send_line(ser, line: str):
    payload = (line + "\n").encode("utf-8")
    ser.write(payload)
    ser.flush()

def send_move_ticks(ser, ticks: int):
    # Framed command expected by ESP
    packet = f"<{ticks}>"
    send_line(ser, packet)
    print(f"[HOST] Sent move: {packet}")

def wait_for_move_result(ser, timeout_s=MOVE_WAIT_TIMEOUT_S):
    """
    Wait until DONE / TIMEOUT / STOPPED / ERR.
    Returns final status string.
    """
    start = time.time()
    saw_ack = False

    while True:
        if ser.in_waiting > 0:
            line = ser.readline().decode("utf-8", errors="ignore").strip()
            if not line:
                continue

            print(f"[ESP] {line}")

            if line.startswith("ACK:"):
                saw_ack = True
            elif line == "DONE":
                return "DONE"
            elif line == "TIMEOUT":
                return "TIMEOUT"
            elif line == "STOPPED":
                return "STOPPED"
            elif line.startswith("ERR:"):
                return line

        if time.time() - start > timeout_s:
            # Host-side timeout; send stop for safety
            print("[HOST] Timeout waiting for completion; sending S")
            send_line(ser, "S")
            return "HOST_TIMEOUT"

        time.sleep(0.005)

def parse_int(s: str):
    try:
        return int(s)
    except ValueError:
        return None

def repl(ser):
    print("\nCommands:")
    print("  integer (e.g., 1200, -300) -> move to absolute ticks")
    print("  z -> zero encoder on ESP")
    print("  p -> request position")
    print("  s -> stop motor")
    print("  q -> quit\n")

    while True:
        cmd = input("Enter command: ").strip().lower()

        if cmd == "q":
            print("[HOST] Quitting.")
            return
        elif cmd == "z":
            send_line(ser, "Z")
            read_available_lines(ser, duration_s=0.5)
        elif cmd == "p":
            send_line(ser, "P")
            read_available_lines(ser, duration_s=0.5)
        elif cmd == "s":
            send_line(ser, "S")
            read_available_lines(ser, duration_s=0.5)
        else:
            val = parse_int(cmd)
            if val is None:
                print("[HOST] Invalid input. Enter integer, z, p, s, or q.")
                continue

            send_move_ticks(ser, val)
            result = wait_for_move_result(ser)
            print(f"[HOST] Move result: {result}")

def main():
    ser = None
    try:
        ser = open_serial()
        print(f"[HOST] Opened {PORT} @ {BAUD}")
        print("[HOST] Waiting for ESP32 boot...")
        time.sleep(ESP_BOOT_WAIT_S)

        ser.reset_input_buffer()
        ser.reset_output_buffer()

        # Read startup banner if present
        read_available_lines(ser, duration_s=1.0, prefix="[BOOT]")

        repl(ser)

    except serial.SerialException as e:
        print(f"[HOST] Serial error: {e}")
        sys.exit(1)
    except KeyboardInterrupt:
        print("\n[HOST] Interrupted by user.")
    finally:
        if ser is not None and ser.is_open:
            try:
                # Safety stop before closing
                send_line(ser, "S")
                time.sleep(0.05)
            except Exception:
                pass
            ser.close()
            print("[HOST] Serial closed.")

if __name__ == "__main__":
    main()

```

Test in this order:

- `p` (should show current position)
    
- `z` (should return `ZEROED`)
    
- `0` (should ACK then DONE quickly)
    
- `500` (motor should move)
    
- `s` (should stop immediately)

```cpp
// =========================
// Pin Configuration
// =========================
#define M1_ENA    14   // PWM enable pin to motor driver - ENB 14 32
#define M1_IN1    12   // direction pin 1 IN3 12 33
#define M1_IN2    13   // direction pin 2 IN4 13 25
#define M1_ENC_A  18   // encoder A 18 26
#define M1_ENC_B  19   // encoder B 19 27
```

### Two Pin Config for C++ and Python
```cpp
#include <Arduino.h>

// =====================================
// Pin Configuration
// =====================================
// Motor 1 (as requested)
#define M1_ENA    32
#define M1_IN1    33
#define M1_IN2    25
#define M1_ENC_A  26
#define M1_ENC_B  27

// Motor 2 (as requested)
#define M2_ENA    14
#define M2_IN1    12
#define M2_IN2    13
#define M2_ENC_A  18
#define M2_ENC_B  19

// =====================================
// Per-Motor PID Parameters (tune separately)
// =====================================
float M1_Kp = 1.00f;
float M1_Ki = 0.30f;
float M1_Kd = 0.35f;

float M2_Kp = 1.00f;
float M2_Ki = 0.30f;
float M2_Kd = 0.35f;

// Shared behavior settings
int deadzoneTicks = 2;
int maxPWM_M1 = 200;
int maxPWM_M2 = 200;
int minPWMToMove_M1 = 70;
int minPWMToMove_M2 = 70;

unsigned long moveTimeoutMs = 6000;
unsigned long telemetryPeriodMs = 100;

// =====================================
// State Variables
// =====================================
volatile long m1Ticks = 0;
volatile long m2Ticks = 0;

long m1Target = 0;
long m2Target = 0;

bool movingGroup = false;  // true when a synchronized move is active
bool m1Done = false;
bool m2Done = false;

float m1Integral = 0.0f;
float m2Integral = 0.0f;
long m1PrevError = 0;
long m2PrevError = 0;

unsigned long lastMicros = 0;    // shared dt clock for both loops
unsigned long moveStartMs = 0;
unsigned long lastTelemetryMs = 0;

// =====================================
// Serial parser state
// Supports:
//   <ticks>   -> move BOTH motors to same absolute target
//   S         -> stop both
//   Z         -> zero both encoders (immediate, no explicit stop)
//   P         -> print both positions
// =====================================
char frameBuf[32];
uint8_t frameIdx = 0;
bool inFrame = false;

// =====================================
// ISR
// =====================================
void IRAM_ATTR m1EncISR() {
  if (digitalRead(M1_ENC_B)) m1Ticks++;
  else m1Ticks--;
}

void IRAM_ATTR m2EncISR() {
  if (digitalRead(M2_ENC_B)) m2Ticks++;
  else m2Ticks--;
}

// =====================================
// Atomic helpers
// =====================================
long readM1Ticks() {
  noInterrupts();
  long t = m1Ticks;
  interrupts();
  return t;
}

long readM2Ticks() {
  noInterrupts();
  long t = m2Ticks;
  interrupts();
  return t;
}

void writeBothTicks(long a, long b) {
  noInterrupts();
  m1Ticks = a;
  m2Ticks = b;
  interrupts();
}

// =====================================
// Motor drive helpers
// =====================================
void driveM1(int pwm) {
  pwm = constrain(pwm, -maxPWM_M1, maxPWM_M1);

  if (pwm > 0) {
    digitalWrite(M1_IN1, HIGH);
    digitalWrite(M1_IN2, LOW);
    ledcWrite(0, pwm);
  } else if (pwm < 0) {
    digitalWrite(M1_IN1, LOW);
    digitalWrite(M1_IN2, HIGH);
    ledcWrite(0, -pwm);
  } else {
    // coast
    digitalWrite(M1_IN1, LOW);
    digitalWrite(M1_IN2, LOW);
    ledcWrite(0, 0);
  }
}

void driveM2(int pwm) {
  pwm = constrain(pwm, -maxPWM_M2, maxPWM_M2);

  if (pwm > 0) {
    digitalWrite(M2_IN1, HIGH);
    digitalWrite(M2_IN2, LOW);
    ledcWrite(1, pwm);
  } else if (pwm < 0) {
    digitalWrite(M2_IN1, LOW);
    digitalWrite(M2_IN2, HIGH);
    ledcWrite(1, -pwm);
  } else {
    // coast
    digitalWrite(M2_IN1, LOW);
    digitalWrite(M2_IN2, LOW);
    ledcWrite(1, 0);
  }
}

void stopBoth(const char* reason) {
  driveM1(0);
  driveM2(0);

  movingGroup = false;
  m1Done = false;
  m2Done = false;

  m1Integral = 0.0f;
  m2Integral = 0.0f;
  m1PrevError = 0;
  m2PrevError = 0;

  Serial.println(reason); // DONE / TIMEOUT / STOPPED
}

void startSynchronizedMove(long absTarget) {
  m1Target = absTarget;
  m2Target = absTarget;

  m1Integral = 0.0f;
  m2Integral = 0.0f;
  m1PrevError = 0;
  m2PrevError = 0;

  m1Done = false;
  m2Done = false;

  lastMicros = micros();
  moveStartMs = millis();
  movingGroup = true;

  // Single ACK for group command
  Serial.printf("ACK:%ld\n", absTarget);
}

// =====================================
// Command handling
// =====================================
void handleLineCommand(const String& lineRaw) {
  String line = lineRaw;
  line.trim();

  if (line.length() == 0) return;

  if (line.equalsIgnoreCase("S")) {
    stopBoth("STOPPED");
    return;
  }

  if (line.equalsIgnoreCase("Z")) {
    // Immediate zero as requested, no explicit stop command
    writeBothTicks(0, 0);
    Serial.println("ZEROED");
    return;
  }

  if (line.equalsIgnoreCase("P")) {
    long p1 = readM1Ticks();
    long p2 = readM2Ticks();
    Serial.printf("POS M1:%ld M2:%ld\n", p1, p2);
    return;
  }

  Serial.printf("ERR:UNKNOWN_CMD:%s\n", line.c_str());
}

void pollSerial() {
  while (Serial.available() > 0) {
    char c = (char)Serial.read();

    // framed command begin
    if (c == '<') {
      inFrame = true;
      frameIdx = 0;
      continue;
    }

    // framed command end
    if (c == '>' && inFrame) {
      frameBuf[frameIdx] = '\0';
      long target = atol(frameBuf);  // absolute target for both
      startSynchronizedMove(target);

      inFrame = false;
      frameIdx = 0;
      continue;
    }

    // framed payload
    if (inFrame) {
      if (frameIdx < sizeof(frameBuf) - 1) {
        frameBuf[frameIdx++] = c;
      } else {
        inFrame = false;
        frameIdx = 0;
        Serial.println("ERR:FRAME_OVERFLOW");
      }
      continue;
    }

    // line commands S/Z/P
    static char lineBuf[32];
    static uint8_t lineIdx = 0;

    if (c == '\n' || c == '\r') {
      if (lineIdx > 0) {
        lineBuf[lineIdx] = '\0';
        handleLineCommand(String(lineBuf));
        lineIdx = 0;
      }
    } else {
      if (lineIdx < sizeof(lineBuf) - 1) {
        lineBuf[lineIdx++] = c;
      } else {
        lineIdx = 0;
        Serial.println("ERR:LINE_OVERFLOW");
      }
    }
  }
}

// =====================================
// PID group update
// =====================================
void runSynchronizedPID() {
  if (!movingGroup) return;

  unsigned long nowUs = micros();
  float dt = (nowUs - lastMicros) * 1e-6f;
  lastMicros = nowUs;
  if (dt <= 0.0f || dt > 0.2f) dt = 0.001f;

  long p1 = readM1Ticks();
  long p2 = readM2Ticks();

  long e1 = m1Target - p1;
  long e2 = m2Target - p2;

  // Individual done flags
  m1Done = (abs(e1) <= deadzoneTicks);
  m2Done = (abs(e2) <= deadzoneTicks);

  // Group done condition
  if (m1Done && m2Done) {
    stopBoth("DONE");
    return;
  }

  // Group timeout: either one taking too long means fail
  if (millis() - moveStartMs > moveTimeoutMs) {
    stopBoth("TIMEOUT");
    return;
  }

  // ---- Motor 1 PID ----
  int pwm1 = 0;
  if (!m1Done) {
    m1Integral += e1 * dt;
    m1Integral = constrain(m1Integral, -300.0f, 300.0f);

    float d1 = (e1 - m1PrevError) / dt;
    m1PrevError = e1;

    float u1 = M1_Kp * e1 + M1_Ki * m1Integral + M1_Kd * d1;
    pwm1 = (int)u1;

    if (pwm1 > 0) pwm1 = max(pwm1, minPWMToMove_M1);
    if (pwm1 < 0) pwm1 = min(pwm1, -minPWMToMove_M1);
  }
  driveM1(pwm1);

  // ---- Motor 2 PID ----
  int pwm2 = 0;
  if (!m2Done) {
    m2Integral += e2 * dt;
    m2Integral = constrain(m2Integral, -300.0f, 300.0f);

    float d2 = (e2 - m2PrevError) / dt;
    m2PrevError = e2;

    float u2 = M2_Kp * e2 + M2_Ki * m2Integral + M2_Kd * d2;
    pwm2 = (int)u2;

    if (pwm2 > 0) pwm2 = max(pwm2, minPWMToMove_M2);
    if (pwm2 < 0) pwm2 = min(pwm2, -minPWMToMove_M2);
  }
  driveM2(pwm2);

  // telemetry
  if (millis() - lastTelemetryMs >= telemetryPeriodMs) {
    lastTelemetryMs = millis();
    Serial.printf(
      "MOVING M1 POS:%ld T:%ld E:%ld PWM:%d | M2 POS:%ld T:%ld E:%ld PWM:%d\n",
      p1, m1Target, e1, pwm1,
      p2, m2Target, e2, pwm2
    );
  }
}

void setup() {
  Serial.begin(115200);
  delay(100);

  // Motor pins
  pinMode(M1_IN1, OUTPUT);
  pinMode(M1_IN2, OUTPUT);
  pinMode(M2_IN1, OUTPUT);
  pinMode(M2_IN2, OUTPUT);

  // Encoder pins
  pinMode(M1_ENC_A, INPUT_PULLUP);
  pinMode(M1_ENC_B, INPUT_PULLUP);
  pinMode(M2_ENC_A, INPUT_PULLUP);
  pinMode(M2_ENC_B, INPUT_PULLUP);

  // PWM setup (2 channels)
  ledcSetup(0, 5000, 8);
  ledcAttachPin(M1_ENA, 0);

  ledcSetup(1, 5000, 8);
  ledcAttachPin(M2_ENA, 1);

  // Encoder interrupts
  attachInterrupt(digitalPinToInterrupt(M1_ENC_A), m1EncISR, RISING);
  attachInterrupt(digitalPinToInterrupt(M2_ENC_A), m2EncISR, RISING);

  // safe startup
  driveM1(0);
  driveM2(0);

  Serial.println("READY");
  Serial.println("CMDS: <ticks> (both motors absolute) | S | Z | P");

  Serial.println("MOTOR TEST START");

  // Test M1 forward 1s
  driveM1(120);
  delay(1000);
  driveM1(0);
  delay(500);

  // Test M2 forward 1s
  driveM2(120);
  delay(1000);
  driveM2(0);

  Serial.println("MOTOR TEST END");
}

void loop() {
  pollSerial();
  runSynchronizedPID();
}

```

```python
import serial
import time
import sys

PORT = "COM4"        # Update this
BAUD = 115200

ESP_BOOT_WAIT_S = 2.5
MOVE_WAIT_TIMEOUT_S = 10.0

def open_serial():
    ser = serial.Serial(
        PORT,
        BAUD,
        timeout=0.05,
        write_timeout=0.2
    )
    return ser

def read_available_lines(ser, duration_s=0.4, prefix="[ESP]"):
    t0 = time.time()
    while time.time() - t0 < duration_s:
        if ser.in_waiting > 0:
            line = ser.readline().decode("utf-8", errors="ignore").strip()
            if line:
                print(f"{prefix} {line}")
        else:
            time.sleep(0.005)

def send_line(ser, line: str):
    payload = (line + "\n").encode("utf-8")
    ser.write(payload)
    ser.flush()

def send_move_ticks(ser, ticks: int):
    packet = f"<{ticks}>"
    send_line(ser, packet)
    print(f"[HOST] Sent synchronized move: {packet}")

def wait_for_move_result(ser, timeout_s=MOVE_WAIT_TIMEOUT_S):
    start = time.time()
    saw_ack = False

    while True:
        if ser.in_waiting > 0:
            line = ser.readline().decode("utf-8", errors="ignore").strip()
            if not line:
                continue

            print(f"[ESP] {line}")

            if line.startswith("ACK:"):
                saw_ack = True
            elif line == "DONE":
                return "DONE"
            elif line == "TIMEOUT":
                return "TIMEOUT"
            elif line == "STOPPED":
                return "STOPPED"
            elif line.startswith("ERR:"):
                return line

        if time.time() - start > timeout_s:
            print("[HOST] Timeout waiting for completion; sending S")
            send_line(ser, "S")
            return "HOST_TIMEOUT"

        time.sleep(0.005)

def parse_int(s: str):
    try:
        return int(s)
    except ValueError:
        return None

def repl(ser):
    print("\nCommands:")
    print("  integer (e.g., 0, 500, -300) -> move BOTH motors to same absolute ticks")
    print("  z -> zero BOTH encoders immediately")
    print("  p -> show BOTH positions")
    print("  s -> stop BOTH motors")
    print("  q -> quit\n")

    while True:
        cmd = input("Enter command: ").strip().lower()

        if cmd == "q":
            print("[HOST] Quitting.")
            return
        elif cmd == "z":
            send_line(ser, "Z")
            read_available_lines(ser, duration_s=0.5)
        elif cmd == "p":
            send_line(ser, "P")
            read_available_lines(ser, duration_s=0.5)
        elif cmd == "s":
            send_line(ser, "S")
            read_available_lines(ser, duration_s=0.5)
        else:
            val = parse_int(cmd)
            if val is None:
                print("[HOST] Invalid input. Enter integer, z, p, s, or q.")
                continue

            send_move_ticks(ser, val)
            result = wait_for_move_result(ser)
            print(f"[HOST] Move result: {result}")

def main():
    ser = None
    try:
        ser = open_serial()
        print(f"[HOST] Opened {PORT} @ {BAUD}")
        print("[HOST] Waiting for ESP32 boot...")
        time.sleep(ESP_BOOT_WAIT_S)

        ser.reset_input_buffer()
        ser.reset_output_buffer()

        read_available_lines(ser, duration_s=1.0, prefix="[BOOT]")
        repl(ser)

    except serial.SerialException as e:
        print(f"[HOST] Serial error: {e}")
        sys.exit(1)
    except KeyboardInterrupt:
        print("\n[HOST] Interrupted by user.")
    finally:
        if ser is not None and ser.is_open:
            try:
                send_line(ser, "S")
                time.sleep(0.05)
            except Exception:
                pass
            ser.close()
            print("[HOST] Serial closed.")

if __name__ == "__main__":
    main()

```

### Working code for two motors without proper tuning 
```cpp
#include <Arduino.h>

// ============================================================
// PIN DEFINITIONS (ESP32-WROOM-32)
// ============================================================

// --- Motor 1 (Left) ---
#define M1_ENA   32  // PWM Speed Control
#define M1_IN1   33  // Direction Pin 1
#define M1_IN2   25  // Direction Pin 2
#define M1_ENC_A 26  // Yellow Wire (Phase A)
#define M1_ENC_B 27  // Green Wire (Phase B)

// --- Motor 2 (Right) ---
#define M2_ENB   14  // PWM Speed Control
#define M2_IN3   12  // Direction Pin 1 (BOOT STRAP PIN: change if boot issues)
#define M2_IN4   13  // Direction Pin 2
#define M2_ENC_A 18  // Yellow Wire (Phase A)
#define M2_ENC_B 19  // Green Wire (Phase B)

// ============================================================
// USER SETTINGS
// ============================================================

// Your encoder info (x4 counts per output shaft revolution)
static const long COUNTS_PER_REV = 2096;

// Set targets in x4 quadrature counts:
static const long M1_TARGET_COUNTS = 1000;
static const long M2_TARGET_COUNTS = 2000;

// Direction for this run
enum Dir { FORWARD = 1, REVERSE = -1 };
static const Dir RUN_DIR_M1 = FORWARD;
static const Dir RUN_DIR_M2 = FORWARD;

// PWM (0-255)
static const int M1_PWM = 180;
static const int M2_PWM = 180;

// PWM (LEDC) settings
static const int PWM_FREQ = 20000;
static const int PWM_RES  = 8;
static const int CH_M1 = 0;
static const int CH_M2 = 1;

// ============================================================
// QUADRATURE DECODER STATE
// ============================================================

// Transition table for quadrature decoding.
// Index = (old_state << 2) | new_state, state is 2-bit [A B] or [A<<1|B].
// Values: +1, -1, or 0 for invalid/no move.
static const int8_t QDEC_TABLE[16] = {
  0,  +1, -1,  0,
 -1,   0,  0, +1,
 +1,   0,  0, -1,
  0,  -1, +1,  0
};

volatile long m1_counts = 0;
volatile long m2_counts = 0;

volatile uint8_t m1_prev_state = 0;
volatile uint8_t m2_prev_state = 0;

// Commanded direction sign (lets you treat targets as positive counts even in reverse)
volatile int m1_dir_sign = +1;
volatile int m2_dir_sign = +1;

// ============================================================
// ISR HELPERS
// ============================================================

static inline uint8_t readEncState_M1() {
  // state: bit1=A, bit0=B
  uint8_t a = (uint8_t)digitalRead(M1_ENC_A);
  uint8_t b = (uint8_t)digitalRead(M1_ENC_B);
  return (uint8_t)((a << 1) | b);
}

static inline uint8_t readEncState_M2() {
  uint8_t a = (uint8_t)digitalRead(M2_ENC_A);
  uint8_t b = (uint8_t)digitalRead(M2_ENC_B);
  return (uint8_t)((a << 1) | b);
}

void IRAM_ATTR isr_m1_qdec() {
  uint8_t new_state = readEncState_M1();
  uint8_t idx = (uint8_t)((m1_prev_state << 2) | new_state);
  int8_t step = QDEC_TABLE[idx];
  m1_prev_state = new_state;
  m1_counts += (long)(step * m1_dir_sign);
}

void IRAM_ATTR isr_m2_qdec() {
  uint8_t new_state = readEncState_M2();
  uint8_t idx = (uint8_t)((m2_prev_state << 2) | new_state);
  int8_t step = QDEC_TABLE[idx];
  m2_prev_state = new_state;
  m2_counts += (long)(step * m2_dir_sign);
}

// ============================================================
// MOTOR CONTROL
// ============================================================

void motor1Stop() {
  ledcWrite(CH_M1, 0);
  digitalWrite(M1_IN1, LOW);
  digitalWrite(M1_IN2, LOW);
}

void motor2Stop() {
  ledcWrite(CH_M2, 0);
  digitalWrite(M2_IN3, LOW);
  digitalWrite(M2_IN4, LOW);
}

void motor1Run(Dir dir, int pwm) {
  m1_dir_sign = (dir == FORWARD) ? +1 : -1;

  if (dir == FORWARD) { digitalWrite(M1_IN1, HIGH); digitalWrite(M1_IN2, LOW);  }
  else               { digitalWrite(M1_IN1, LOW);  digitalWrite(M1_IN2, HIGH); }

  ledcWrite(CH_M1, constrain(pwm, 0, 255));
}

void motor2Run(Dir dir, int pwm) {
  m2_dir_sign = (dir == FORWARD) ? +1 : -1;

  if (dir == FORWARD) { digitalWrite(M2_IN3, HIGH); digitalWrite(M2_IN4, LOW);  }
  else               { digitalWrite(M2_IN3, LOW);  digitalWrite(M2_IN4, HIGH); }

  ledcWrite(CH_M2, constrain(pwm, 0, 255));
}

// ============================================================
// RUN LOGIC
// ============================================================

bool running = false;
bool m1_done = false;
bool m2_done = false;

void resetRunState() {
  motor1Stop();
  motor2Stop();

  noInterrupts();
  m1_counts = 0;
  m2_counts = 0;
  m1_prev_state = readEncState_M1();
  m2_prev_state = readEncState_M2();
  interrupts();

  running = false;
  m1_done = false;
  m2_done = false;
}

void startRun() {
  resetRunState();
  running = true;

  // Start both motors "simultaneously" (same function call sequence)
  motor1Run(RUN_DIR_M1, M1_PWM);
  motor2Run(RUN_DIR_M2, M2_PWM);

  Serial.println("Started run (x4 quadrature).");
  Serial.print("M1 target counts: "); Serial.println(M1_TARGET_COUNTS);
  Serial.print("M2 target counts: "); Serial.println(M2_TARGET_COUNTS);
}

// ============================================================
// SETUP / LOOP
// ============================================================

void setup() {
  Serial.begin(115200);
  delay(200);

  // Motor pins
  pinMode(M1_IN1, OUTPUT);
  pinMode(M1_IN2, OUTPUT);
  pinMode(M2_IN3, OUTPUT);
  pinMode(M2_IN4, OUTPUT);

  // Encoder pins (pullups help if your encoder outputs are open-collector or noisy)
  pinMode(M1_ENC_A, INPUT_PULLUP);
  pinMode(M1_ENC_B, INPUT_PULLUP);
  pinMode(M2_ENC_A, INPUT_PULLUP);
  pinMode(M2_ENC_B, INPUT_PULLUP);

  // PWM setup
  ledcSetup(CH_M1, PWM_FREQ, PWM_RES);
  ledcAttachPin(M1_ENA, CH_M1);

  ledcSetup(CH_M2, PWM_FREQ, PWM_RES);
  ledcAttachPin(M2_ENB, CH_M2);

  // Initialize previous states BEFORE attaching interrupts
  noInterrupts();
  m1_prev_state = readEncState_M1();
  m2_prev_state = readEncState_M2();
  interrupts();

  // Attach CHANGE interrupts on both A and B for x4 counting
  attachInterrupt(digitalPinToInterrupt(M1_ENC_A), isr_m1_qdec, CHANGE);
  attachInterrupt(digitalPinToInterrupt(M1_ENC_B), isr_m1_qdec, CHANGE);

  attachInterrupt(digitalPinToInterrupt(M2_ENC_A), isr_m2_qdec, CHANGE);
  attachInterrupt(digitalPinToInterrupt(M2_ENC_B), isr_m2_qdec, CHANGE);

  resetRunState();

  Serial.println("Ready.");
  Serial.println("Press 'g' then Enter to start. Press 's' to stop/reset.");
  Serial.print("Counts per output rev (x4): "); Serial.println(COUNTS_PER_REV);
}

void loop() {
  // Start/stop commands
  if (Serial.available()) {
    char c = (char)Serial.read();
    if (c == 'g' || c == 'G') startRun();
    if (c == 's' || c == 'S') { Serial.println("Stopped/reset."); resetRunState(); }
  }

  if (!running) return;

  long c1, c2;
  noInterrupts();
  c1 = m1_counts;
  c2 = m2_counts;
  interrupts();

  if (!m1_done && labs(c1) >= labs(M1_TARGET_COUNTS)) {
    motor1Stop();
    m1_done = true;
    Serial.println("Motor 1 done.");
  }

  if (!m2_done && labs(c2) >= labs(M2_TARGET_COUNTS)) {
    motor2Stop();
    m2_done = true;
    Serial.println("Motor 2 done.");
  }

  if (m1_done && m2_done) {
    running = false;
    Serial.println("Run complete.");
  }

  // Debug print at ~5 Hz
  static unsigned long lastPrint = 0;
  unsigned long now = millis();
  if (now - lastPrint >= 200) {
    lastPrint = now;

    // also show revolutions for intuition
    float rev1 = (float)c1 / (float)COUNTS_PER_REV;
    float rev2 = (float)c2 / (float)COUNTS_PER_REV;

    Serial.print("M1: "); Serial.print(c1); Serial.print(" ("); Serial.print(rev1, 3); Serial.print(" rev)");
    Serial.print(" | M2: "); Serial.print(c2); Serial.print(" ("); Serial.print(rev2, 3); Serial.println(" rev)");
  }
}

```

### Wiring Pin Outs

#### Motor 1
Black wire to OUT1
RED wire to OUT 2
Blue wire to 3.3v
Green wire to ground breadboard
White wire to Pin 26
Yellow wire to Pin 27
ENA to 32
IN1 to 33
IN2 to 25
#### Motor 2
Black to OUT 4
Red to OUT 3
Blue wire to 3.3v
Green wire to ground breadboard
Yellow wire to pin 18
White wire to pin 27
ENB to 14
IN3 to 12
IN4 to 13
#### Extra
12V motor driver to positive bread board
GND motor driver to GND bread board
5V motor driver to 3.3V esp


### Working config 2 motors at the same time with hardcoded values:
```cpp
#include <Arduino.h>

// ============================================================
// PIN DEFINITIONS (ESP32-WROOM-32)
// ============================================================

// --- Motor 1 (Left) ---
#define M1_ENA   32  // PWM Speed Control
#define M1_IN1   33  // Direction Pin 1
#define M1_IN2   25  // Direction Pin 2
#define M1_ENC_A 26  // Yellow Wire (Phase A)
#define M1_ENC_B 27  // White Wire (Phase B)

// --- Motor 2 (Right) ---
#define M2_ENB   14  // PWM Speed Control
#define M2_IN3   12  // Direction Pin 1 (BOOT STRAP PIN: change if boot issues)
#define M2_IN4   13  // Direction Pin 2
#define M2_ENC_A 18  // Yellow Wire (Phase A)
#define M2_ENC_B 19  // White Wire (Phase B)

// ============================================================
// USER SETTINGS
// ============================================================

// Your encoder info (x4 counts per output shaft revolution)
static const long COUNTS_PER_REV = 8384;

// Set targets in x4 quadrature counts:
static const long M1_TARGET_COUNTS = 500;
static const long M2_TARGET_COUNTS = 500;

// Direction for this run
enum Dir { FORWARD = 1, REVERSE = -1 };
static const Dir RUN_DIR_M1 = REVERSE; //Reverse is clockwise for motor 1
static const Dir RUN_DIR_M2 = FORWARD; //Forward is counterclockwise for motor2

// PWM (0-255)
static const int M1_PWM = 180;
static const int M2_PWM = 180;

// PWM (LEDC) settings
static const int PWM_FREQ = 20000;
static const int PWM_RES  = 8;
static const int CH_M1 = 0;
static const int CH_M2 = 1;

// ============================================================
// QUADRATURE DECODER STATE
// ============================================================

// Transition table for quadrature decoding.
// Index = (old_state << 2) | new_state, state is 2-bit [A B] or [A<<1|B].
// Values: +1, -1, or 0 for invalid/no move.
static const int8_t QDEC_TABLE[16] = {
  0,  +1, -1,  0,
 -1,   0,  0, +1,
 +1,   0,  0, -1,
  0,  -1, +1,  0
};

volatile long m1_counts = 0;
volatile long m2_counts = 0;

volatile uint8_t m1_prev_state = 0;
volatile uint8_t m2_prev_state = 0;

// Commanded direction sign (lets you treat targets as positive counts even in reverse)
volatile int m1_dir_sign = +1;
volatile int m2_dir_sign = +1;

// ============================================================
// ISR HELPERS
// ============================================================

static inline uint8_t readEncState_M1() {
  // state: bit1=A, bit0=B
  uint8_t a = (uint8_t)digitalRead(M1_ENC_A);
  uint8_t b = (uint8_t)digitalRead(M1_ENC_B);
  return (uint8_t)((a << 1) | b);
}

static inline uint8_t readEncState_M2() {
  uint8_t a = (uint8_t)digitalRead(M2_ENC_A);
  uint8_t b = (uint8_t)digitalRead(M2_ENC_B);
  return (uint8_t)((a << 1) | b);
}

void IRAM_ATTR isr_m1_qdec() {
  uint8_t new_state = readEncState_M1();
  uint8_t idx = (uint8_t)((m1_prev_state << 2) | new_state);
  int8_t step = QDEC_TABLE[idx];
  m1_prev_state = new_state;
  m1_counts += (long)(step * m1_dir_sign);
}

void IRAM_ATTR isr_m2_qdec() {
  uint8_t new_state = readEncState_M2();
  uint8_t idx = (uint8_t)((m2_prev_state << 2) | new_state);
  int8_t step = QDEC_TABLE[idx];
  m2_prev_state = new_state;
  m2_counts += (long)(step * m2_dir_sign);
}

// ============================================================
// MOTOR CONTROL
// ============================================================

void motor1Stop() {
  ledcWrite(CH_M1, 0);
  digitalWrite(M1_IN1, LOW);
  digitalWrite(M1_IN2, LOW);
}

void motor2Stop() {
  ledcWrite(CH_M2, 0);
  digitalWrite(M2_IN3, LOW);
  digitalWrite(M2_IN4, LOW);
}

void motor1Run(Dir dir, int pwm) {
  m1_dir_sign = (dir == FORWARD) ? +1 : -1;

  if (dir == FORWARD) { digitalWrite(M1_IN1, HIGH); digitalWrite(M1_IN2, LOW);  }
  else               { digitalWrite(M1_IN1, LOW);  digitalWrite(M1_IN2, HIGH); }

  ledcWrite(CH_M1, constrain(pwm, 0, 255));
}

void motor2Run(Dir dir, int pwm) {
  m2_dir_sign = (dir == FORWARD) ? +1 : -1;

  if (dir == FORWARD) { digitalWrite(M2_IN3, HIGH); digitalWrite(M2_IN4, LOW);  }
  else               { digitalWrite(M2_IN3, LOW);  digitalWrite(M2_IN4, HIGH); }

  ledcWrite(CH_M2, constrain(pwm, 0, 255));
}

// ============================================================
// RUN LOGIC
// ============================================================

bool running = false;
bool m1_done = false;
bool m2_done = false;

void resetRunState() {
  motor1Stop();
  motor2Stop();

  noInterrupts();
  m1_counts = 0;
  m2_counts = 0;
  m1_prev_state = readEncState_M1();
  m2_prev_state = readEncState_M2();
  interrupts();

  running = false;
  m1_done = false;
  m2_done = false;
}

void startRun() {
  resetRunState();
  running = true;

  // Start both motors "simultaneously" (same function call sequence)
  motor1Run(RUN_DIR_M1, M1_PWM);
  motor2Run(RUN_DIR_M2, M2_PWM);

  Serial.println("Started run (x4 quadrature).");
  Serial.print("M1 target counts: "); Serial.println(M1_TARGET_COUNTS);
  Serial.print("M2 target counts: "); Serial.println(M2_TARGET_COUNTS);
}

// ============================================================
// SETUP / LOOP
// ============================================================

void setup() {
  Serial.begin(115200);
  delay(200);

  // Motor pins
  pinMode(M1_IN1, OUTPUT);
  pinMode(M1_IN2, OUTPUT);
  pinMode(M2_IN3, OUTPUT);
  pinMode(M2_IN4, OUTPUT);

  // Encoder pins (pullups help if your encoder outputs are open-collector or noisy)
  pinMode(M1_ENC_A, INPUT_PULLUP);
  pinMode(M1_ENC_B, INPUT_PULLUP);
  pinMode(M2_ENC_A, INPUT_PULLUP);
  pinMode(M2_ENC_B, INPUT_PULLUP);

  // PWM setup
  ledcSetup(CH_M1, PWM_FREQ, PWM_RES);
  ledcAttachPin(M1_ENA, CH_M1);

  ledcSetup(CH_M2, PWM_FREQ, PWM_RES);
  ledcAttachPin(M2_ENB, CH_M2);

  // Initialize previous states BEFORE attaching interrupts
  noInterrupts();
  m1_prev_state = readEncState_M1();
  m2_prev_state = readEncState_M2();
  interrupts();

  // Attach CHANGE interrupts on both A and B for x4 counting
  attachInterrupt(digitalPinToInterrupt(M1_ENC_A), isr_m1_qdec, CHANGE);
  attachInterrupt(digitalPinToInterrupt(M1_ENC_B), isr_m1_qdec, CHANGE);

  attachInterrupt(digitalPinToInterrupt(M2_ENC_A), isr_m2_qdec, CHANGE);
  attachInterrupt(digitalPinToInterrupt(M2_ENC_B), isr_m2_qdec, CHANGE);

  resetRunState();

  Serial.println("Ready.");
  Serial.println("Press 'g' then Enter to start. Press 's' to stop/reset.");
  Serial.print("Counts per output rev (x4): "); Serial.println(COUNTS_PER_REV);
}

void loop() {
  // Start/stop commands
  if (Serial.available()) {
    char c = (char)Serial.read();
    if (c == 'g' || c == 'G') startRun();
    if (c == 's' || c == 'S') { Serial.println("Stopped/reset."); resetRunState(); }
  }

  if (!running) return;

  long c1, c2;
  noInterrupts();
  c1 = m1_counts;
  c2 = m2_counts;
  interrupts();

  if (!m1_done && labs(c1) >= labs(M1_TARGET_COUNTS)) {
    motor1Stop();
    m1_done = true;
    Serial.println("Motor 1 done.");
  }

  if (!m2_done && labs(c2) >= labs(M2_TARGET_COUNTS)) {
    motor2Stop();
    m2_done = true;
    Serial.println("Motor 2 done.");
  }

  if (m1_done && m2_done) {
    running = false;
    Serial.println("Run complete.");
  }

  // Debug print at ~5 Hz
  static unsigned long lastPrint = 0;
  unsigned long now = millis();
  if (now - lastPrint >= 200) {
    lastPrint = now;

    // also show revolutions for intuition
    float rev1 = (float)c1 / (float)COUNTS_PER_REV;
    float rev2 = (float)c2 / (float)COUNTS_PER_REV;

    Serial.print("M1: "); Serial.print(c1); Serial.print(" ("); Serial.print(rev1, 3); Serial.print(" rev)");
    Serial.print(" | M2: "); Serial.print(c2); Serial.print(" ("); Serial.print(rev2, 3); Serial.println(" rev)");
  }
}
```


### Tasks Left to Do 
- Output two tick values to each motor from python file
- Verify kinematic calculation and output to real robot
- Develop computer vision program to identify object and coordinates for pick and place
- Integrate computer vision detected coordinates to kinematic solver to ticks to motor control