
## Phase 1: The Hardware Layout

### 1. The Sensor (MPU6050)

The MPU6050 measures acceleration. It uses the **I2C protocol**, so it must go to specific pins on the Arduino:

* **VCC** → Arduino **5V**
* **GND** → Arduino **GND**
* **SCL** → Arduino **A5**
* **SDA** → Arduino **A4**

### 2. The Alert System

* **Buzzer (+)** → Arduino **Pin 8**
* **Buzzer (-)** → Arduino **GND**
* **LED (+)** → Arduino **Pin 9** (Place a **220Ω resistor** in between)
* **LED (-)** → Arduino **GND**

---

## Phase 2: Detailed Assembly Steps

1. **Place the Arduino:** Secure your Arduino Uno on a flat surface or breadboard.
2. **Seat the MPU6050:** Plug the sensor into the breadboard. Ensure the pins are pushed in firmly; loose connections cause "Sensor Not Found" errors.
3. **Bridge the Power:** Connect the 5V and GND from the Arduino to the breadboard power rails.
4. **Wire the Components:** Follow the pin-out listed in Phase 1.
> **Tip:** Keep your wires short. Long, dangling wires can act like antennas and pick up electrical noise, causing false accident triggers.



---

## Phase 3: The "Brain" (The Code)

This code calculates the "Resultant Force." Instead of just checking if the sensor moved left or right, it calculates the total impact force in 3D space.

```cpp
#include <Adafruit_MPU6050.h>
#include <Adafruit_Sensor.h>
#include <Wire.h>

Adafruit_MPU6050 mpu;

const int buzzerPin = 8;
const int ledPin = 9;

// Sensitivity settings
float threshold = 25.0; // Acceleration in m/s^2. (Gravity is ~9.8)

void setup() {
  Serial.begin(115200);
  
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledPin, OUTPUT);

  // Initialize the sensor
  if (!mpu.begin()) {
    Serial.println("Failed to find MPU6050 chip!");
    while (1) { yield(); } // Stop if sensor is missing
  }

  Serial.println("MPU6050 Found! System Monitoring...");
}

void loop() {
  sensors_event_t a, g, temp;
  mpu.getEvent(&a, &g, &temp);

  // Calculate the total magnitude of acceleration
  // Formula: sqrt(x^2 + y^2 + z^2)
  float totalAcc = sqrt(pow(a.acceleration.x, 2) + 
                        pow(a.acceleration.y, 2) + 
                        pow(a.acceleration.z, 2));

  // If the impact is greater than our threshold
  if (totalAcc > threshold) {
    Serial.print("IMPACT DETECTED! Force: ");
    Serial.println(totalAcc);
    triggerEmergencyAlert();
  }

  delay(50); // High sampling rate for accuracy
}

void triggerEmergencyAlert() {
  // Beep and Flash for 5 seconds
  for(int i=0; i<10; i++) {
    digitalWrite(ledPin, HIGH);
    tone(buzzerPin, 1500); // 1.5kHz tone
    delay(250);
    digitalWrite(ledPin, LOW);
    noTone(buzzerPin);
    delay(250);
  }
}

```

---

## Phase 4: Testing & Calibration

### 1. The "Idle" Test

Upload the code and open the **Serial Monitor** (set to 115200 baud). With the sensor sitting still, the values should stay below the threshold (around 9.8 to 11.0 m/s²). If it triggers while sitting still, your threshold is too low.

### 2. The "Impact" Test

Gently tap the sensor or drop it onto a soft surface (like a sponge). You should see the LED flash and hear the buzzer beep.

### 3. Tuning the Math

If you want the system to be harder to trigger (only for high-speed crashes), change the threshold in the code:

* **20.0 - 25.0**: Sensitive (Triggered by a hard shake).
* **40.0 - 50.0**: Rugged (Triggered by a drop or sharp hit).

---

## Phase 5: Troubleshooting for Beginners

* **Continuous Beeping:** Your threshold is likely lower than 9.8. Since gravity is always pulling at 9.8 m/s², your threshold must be significantly higher than that.
* **Garbage Text in Serial Monitor:** Ensure the Baud Rate in the bottom right corner of the monitor is set to **115200**.
* **Library Errors:** Make sure you went to **Tools > Manage Libraries** and installed "Adafruit MPU6050".

Would you like me to show you how to add a **"Reset Button"** so you can stop the alarm manually after it triggers?
