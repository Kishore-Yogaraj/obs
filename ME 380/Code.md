```c ++
#include <Arduino.h>
#include <util/atomic.h>

#define ENCODER_A 2
#define ENCODER_B 3
#define PWM_PIN   9
#define IN1_PIN   8
#define IN2_PIN   7

volatile int posi = 0;

void readEncoderA();

void setup() {
  Serial.begin(9600);

  pinMode(ENCODER_A, INPUT_PULLUP);
  pinMode(ENCODER_B, INPUT_PULLUP);

  // Use RISING edge for both channels
  attachInterrupt(digitalPinToInterrupt(ENCODER_A), readEncoderA, RISING);

}

// A rising edge → read B to determine direction
void readEncoderA() {
  if (digitalRead(ENCODER_B)) posi++;
  else posi--;
}

void loop() {

  int pos = 0;
  ATOMIC_BLOCK(ATOMIC_RESTORESTATE) { pos = posi; }

  Serial.println(pos);

}
```