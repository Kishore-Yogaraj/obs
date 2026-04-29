### Skeleton Code
```cpp
#include <Arduino.h>
#include "driver/pcnt.h"

// ── Pin definitions ──────────────────────────────
#define ENC_A     25
#define ENC_B     26
#define RPWM_PIN  27
#define LPWM_PIN  14

// ── PWM config ───────────────────────────────────
#define PWM_FREQ        20000
#define PWM_RESOLUTION  8
#define RPWM_CH         0
#define LPWM_CH         1

// ── Encoder config ───────────────────────────────
#define CPR  4192.0f

// ── Target ───────────────────────────────────────
const float TARGET_DEGREES = 90.0f;

void setup() {
    Serial.begin(115200);
    Serial.println("Booting...");
}

void loop() {
}
```

### Pin Definitions
The pin definitions act as the map between the code and the actual microcontroller. If you trace the wires from pins 25, 26, 27 and 14 you would see that it it connects to the appropriate wires on the motor itself. 

Pin 25 on the ESP connects to encoder A
Pin 26 on the ESP connects to encoder B
- Encoder A and Encoder B are two different encoder sensors which both output a pulse. When you read which pin pulses first the firmware can determine which direction the motor is rotating. Counting the actual pulses determines your distance. 
- These are called **Quadrature Encoders**
Pin 27 on the ESP controls the right rotation of the motor (when you send a signal here it rotate clockwise)
Pin 14 on the ESP controls the left rotation of the motor (when you send a signal here it rotates counter clockwise)

### Pin Configurations
Microcontroller can vary their voltage output (It's either low which is 0V or high which is 3.3V). **PWM** is how we are able control the speed of our motors.

PWM needs to be set up with a frequency (How fast the pin flicks), resolution which the step size of our speed control. 8 bit is standard and allows us to control a speed from 0 to 255 because of 2^8. On the esp we can't just write to a pin we assign a channel to that pin to handle the timing automatically

### Encoder Configurations
CPR stands for **Counts Per Revolution**. It tells the firmware that for every 360 degree of ration the encoder will send 4192 pules.

Our target count effectively becomes **(target count/360) x 4192**

### Difference between define and const
Define is an instruction we give to the preprocessor which runs before the code is actually compiled. This doesn't exist in memory as a variable it a hardcoded number in our instruction.

### void setup
This is the setup of your code. It's where you "officially" initialize all the pin definitions you set up and send these instructions to your ESP to remember

### void loop
This is the code that is run over and over again from top to bottom based on the logic you have set up


### PWM Setup
```cpp
void setup() {
  Serial.begin(115200);
  
  //PWM setup
  pinMode(RPWM_PIN, OUTPUT);
  pinMode(LPWM_PIN, OUTPUT);
  digitalWrite(RPWM_PIN, LOW);
  digitalWrite(LPWM_PIN, LOW);

  ledcSetup(RPWM_CH, PWM_FREQ, PWM_RESOLUTION);
  ledcAttachPin(RPWM_PIN, RPWM_CH);
  ledcSetup(LPWM_CH, PWM_FREQ, PWM_RESOLUTION);
  ledcAttachPin(LPWM_PIN, LPWM_CH);

  Serial.println("PWM ready");
}
  
```

Remember that the setup function is where we physically wire our code to the esp32 hardware so we know where to send signals from and where to receive them and how to receive them.

On the esp32, motor control is a two step process
- We have tell the pin that it is an output and you have to tell the hardware what kind of signal to send through that pin

### pinMode
the pinMode function can be thought of as a one way gate. You can either take an input from this connection or send an output from this connection. In this case we are letting the hardware know that pin 27 and pin 14 on the esp will be sending information out.

### digitalWrite
the role of this function is to set the intensity of the signal sent out. We can either set it to High (Full voltage) or Low (0V). Here we are setting both pins to LOW to make sure the motors don't turn when you immediately power on the esp.

### ledcSetup
This function sets up our speed control. It initializes our ability to set the speed of the motor from 0 to 255. We can refer to this as a "ghost channel". We basically set up this black box of information.

### ledcAttachPin
This function takes the black box of information we just created and attaches it directly to the pin. We now have a pin that is configured and ready to output information and control the speed of the motors using PWM signals from 0 to 255 and we can control the direction of the motors by setting the designated pin to HIGH or LOW.

**The whole process of the PWM setup is to provide instructions to a pin on how it should send signal and what signal it can send**

##### Practice
```cpp
#define RPWM_PIN 27
#define LRPWM_PIN 14

#define PWM_FREQ = 2000
#define PWM_RESO = 8
#define PWM_CHANEL_A = 0
#define PWM_CHANEL_B = 0

void setup(){
	pinMode(RPWM_PIN, OUTPUT)
	digitalWrite(RPWM_PIN, LOW)
	ledcSetup(PWM_CHANEL_A, PWM_FREQ, PWM_RESO)
	ledcAttachPin(RPWM_PIN, PWM_CHANEL_A)
}
```

**Process**
Connect your wires
- define pins for left and write
Lay out your parts for your engine
- define your frequency, resolution and channels
Tell the pin what its main job is
- Output signal only, no listening
Set a safety measure on startup
- Tell the pin to stay low voltage, do not send a signal
Build the engine using your components
- ledcSetup(channel, frequency, resolution)
- ledcAttachPin(pin number, channel)

##### How does a motor spin at 50% of the speed
Let's say we set the PWM signal to be 127 which means to spin at 50% of the speed. What happens is that in the 20 000 "on and offs" sent to the motor, 50% of the time the voltage is low or "off". The voltage sent is the "logic voltage" which is 3.3v but can vary for microcontrollers. The high and low is spread out along the entire 20 000 clicks. It always alternates between high and low but the duration at which it stays high or low is what changes depending on the PWM sent.
![[22845.jpg]]

### PCNT Setup
Given the high definition of our motor which is 4192 counts per rotation. If we ever need to read encoder values and we use the main cpu on the micro controller the cpu has to stop whatever its doing, save its current state, read the values from the encoder and output it somewhere then come back to the state it was in. All of this takes a lot of time when using interrupts and we could miss counts if we are in the middle of a process. This is especially hard when we start working with two motors with two different encoders. To prevent this error we use PCNT which is a piece of hardware on the ESP that is dedicated to just counting encoder pulses. **Uses ZERO CPU** and counts at megahertz speed.

### Code for PCNT
All we are doing here is just setting up the hardware on the ESP so it knows what its job is and what it has to do
```cpp
void setupEncoder() {
    pcnt_config_t pcnt_config = {}; //set up configuratio structure for pcnt hardware

    pcnt_config.pulse_gpio_num = ENC_A; //determine which encoder is used to count
    pcnt_config.ctrl_gpio_num  = ENC_B; //determine which encoder is used to determine direction
    pcnt_config.unit           = PCNT_UNIT_0; //ESP has 8 PCNT units for counting we are telling it to us unit 0
    pcnt_config.channel        = PCNT_CHANNEL_0; //Also use channel 0

    // on ENC_A rising edge: if ENC_B is LOW count up, if HIGH count down
    pcnt_config.pos_mode   = PCNT_COUNT_INC; //On the positive edge increment (High to Low)
    pcnt_config.neg_mode   = PCNT_COUNT_DEC; //On the negative edge decrement (Low to Highe)
    pcnt_config.lctrl_mode = PCNT_MODE_REVERSE; //If encoder B is low reverse the direciton
    pcnt_config.hctrl_mode = PCNT_MODE_KEEP; //If encoder B is high continue on the same direction 

    // hardware limits (int16 max range)
    pcnt_config.counter_h_lim =  32767; //Setting limits for max we can count to
    pcnt_config.counter_l_lim = -32768; //Setting limits for min we can count

    pcnt_unit_config(&pcnt_config); //Push the configuration of the pcnt to hardware

    // filter out glitches shorter than 10 APB clock cycles
    pcnt_set_filter_value(PCNT_UNIT_0, 10);
    pcnt_filter_enable(PCNT_UNIT_0);
    
	//Reset the counter on startup
    pcnt_counter_pause(PCNT_UNIT_0);
    pcnt_counter_clear(PCNT_UNIT_0);
    pcnt_counter_resume(PCNT_UNIT_0);
}
```

First we create the configuration object. In **line 1** we're just setting our two variables to 0

In **lines 2 and 3** we are setting setting up our PCNT to understand which pin you should read from to watch for pulses and which pin decides whether you should count up or count down

In **lines 4 and 5** we are telling the hardware which hardware counter unit to use and which channel to use of this unit (there are 8 counter units) that can be used


in **lines 6 and 7** we are telling the unit to count on the positive edge and decrement on the negative edge
in **lines 8 and 9** we are looking at the control pin and we say if enc b is low then reverse the counting direction and if its high then keep counting in the same direction
- For 6 - 9 we are saying that if we read encoder B before encoder A then the motor is moving counter clockwise. If we see A before B then the motor is moving clockwise

**lines 10 and 11** these simply set the range for how high the encoder can possibly count

**line 12** actually send the information we just created to the esp hardware

**line 13 and 14** ignore pulses that last less than 10 clock cycles so we don't run into unwanted counts

**lines 15 - 17** are just used to reset the count of the PCNT on set up so we start from 0 

##### Summary
PCNT is a piece of hardware in a microcontroller in which its one job is it keep count of pulses. It does a more accurate job of keeping count of pulses because if we use the main cpu to read encoder values then we run into the probability of losing track of the counts. Typically the process would be to use **software interrupts** where we would stop the process of the main cpu, count the encoder value, output the data of the encoder value and then resume with operation. These leads to the possibility of missing counts or interrupting other crucial processes. Instead we used a piece of hardware on the microcontroller who's only job is to count pulses

### Controlling Motor to go To Certain Position Without PID
```cpp
void loop(){
  int target_counts = (TARGET_DEGREES/360.0f) * CPR;

  if (getPosition() < target_counts){
    ledcWrite(RPWM_CH, 100);
    ledcWrite(LPWM_CH, 0);
  }
  else {
    ledcWrite(RPWM_CH, 0);
    ledcWrite(LPWM_CH, 0);
  }

  Serial.print("pos: ");
  Serial.println(getPosition());
  delay(100);
}
```


### PID Setup
```cpp
//PID state
float target = 0.0f;
float prev_error = 0.0f;
float integral = 0.0f;
unsigned long prevTime = 0;

//PID gains
float kp = 1.5f;
float ki = 0.0f;
float kd = 0.3f;

//PWM cap
#define PWM_MAX 150
```

#### Float Integral
This stores the running sum of the past errors. The micro controller runs in steps so we approximate the integral to be: integral += error x dt

We are effectively accumulating our ever over time.

We think of the integral term like this:
- The motor wants to get to 100
- It undershoots at stops at 95
- Your error has become too small so your Kp x error wont produce enough voltage to move the motor
- Your derivative term has no change in error over time because the motor is not moving anymore
- The integral term will continue to add this error up with each time step 
```
5 + 5 + 5 + 5 + 5 ....
```

Eventually the Ki gain multiplied by the integral will have a big enough control voltage to move the motors.

#### prevTime
This is just a variable we set to hold the previous time recorded of when the control loop ran. This is because in order to find out what dt is we need to do:
dt = current Time - prevTime

Here is an example of this implementation
```cpp
unsigned long prevTime = 0;
unsigned long currentTime = millis();
dt = (currentTime - prevTime);
prevTime = currentTime;
```

You will notice above that we using the **millis()** function. All this does is it keeps track of how long the program has been running since program start

This function also returns an unsigned long value so its better to have the two timer values use the same type

### Update function for PID Implementation
```cpp
void update(){
  //Change in time calculation
  unsigned long currentTime = millis();
  float dt = (currentTime - prevTime)/1000.0f; //change in time in seconds

  if (dt <= 0.0f){
    return;
  }

  prevTime = currentTime;

  //Calculating error
  float position = getPosition();
  float error = target - position;

  //Integral Calculation
  integral += error * dt;

  //Derivative calculation
  float derivative = (error - prev_error)/dt;

  float control_signal = kp * error + ki * integral + kd * derivative;
  prev_error = error;

  //Converting to PWM------
  int pwm = 0;
  int scale_pwm = 100.0f;

  if (control_signal >= 0){
    pwm = (int)((control_signal / scale_pwm) * 255.0f); //Convert to integer for pwm
    pwm = constrain(pwm, 0, PWM_MAX); //Contrain value between 0 and 150
    ledcWrite(RPWM_CH, pwm);
    ledcWrite(LPWM_CH, 0);
  }
  else{
    pwm = (int)(((-control_signal) / scale_pwm) * 255.0f);
    pwm = constrain(pwm, 0, PWM_MAX);
    ledcWrite(RPWM_CH, 0);
    ledcWrite(LPWM_CH, pwm);
  }

}
```

### Motor Definitions
Motor 1 is the motor closest to the ESP
RPWCH high makes the motor go clockwise
Control signal positive makes the motor go clockwise

### Cleaning Up Directory for Working code
The core idea is that one class will have one responsibility. So one CPP file and header combo will have one responsibility.

Encoder class is responsible for our reading encoder values and displaying it
Motor class is for initializing our motors and controlling it
PID class is for any PID control logic

#### What is a header file
We can think of a header file as a table of contents or declaration of what exists while the cpp file is where we write what these declarations and what it does

When the compile builds the project, it needs to know two separate things:
- Does thing exist and what does it look like - answered by the header file
- What does it actually do - answered by the cpp file

In the header file for the encoder we have this
```cpp
#pragma once
#include "driver/pcnt.h"

class Encoder {
public: //Variables and functions accessible by the publics
    Encoder(int pinA, int pinB, pcnt_unit_t unit); //Variables needed for Encoder
    void begin(); //Function to build encoder
    int  getPosition() const; //Function to get positon of encoder
    void reset(); //Function to reset encoder

private:
    int         _pinA, _pinB;
    pcnt_unit_t _unit;
};
```

This file is basically saying that there is this thing called an Encoder and it has these functions and these internal variables and that's all you need to know on how to use it.

#### Class Encoder
We are creating a blueprint called Encoder. It doesn't create anything in the code yet it just defines what an Encoder is and what it can do. We are simply creating the blueprint for the encoder.

#### Public
Everything listed under public is accessible from outside the class. This is the interface we expose to main.cpp. We can think of this as buttons on the outside of a TV. We don't need to know how the buttons work, we just press the button

#### Encoder(int pinA, int pinB, pcnt_unit_t unit);
This is the **constructor** — a special function that runs automatically when you create an Encoder. It's how you pass in the specific pins and PCNT unit for _this particular_ encoder instance.

So when you write `Encoder enc1(25, 26, PCNT_UNIT_0)`, those three values get handed to this constructor. This is what allows `enc1` and `enc2` to be identical in behavior but operate on completely different hardware pins.

For the header file we are basically making the assumption of everything the main cpp file for the encoder will actually do. We are just laying out everything we need to actually make the encoder work. Typically for the encoder if we were to do it in one file, we would set up the pins and the variables for the pins and then set up the functions that encoder would do and create the functions that the encoder would do. But with OOP we are able to make all these definitions in a header file and then use these definitions in the main CPP file for the encoder.

#### Public vs Private
Put it in public if:
- It is a command
- It is a request
- It is the constructor
Put it in Private if:
- Its a state variable and it shouldn't be changed when put in main.cpp
- Its a helper function specific to the logic you are using
- Its pin or hardware info. The main.cpp does not need to that the motor is on GPIO pin 27, it just has to know that the motor can turn
