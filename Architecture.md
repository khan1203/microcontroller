# III. System Architecture

## A. Overall Architecture Overview

The proposed Accident Detection and Emergency Alert System follows a modular embedded system architecture. It consists of sensing, processing, alert, communication, and power subsystems working together in real time.

### 1. High-Level Architecture

Accelerometer Sensor  
        ↓  
Microcontroller (Data Processing Unit)  
        ↓  
Decision Logic (Threshold Detection Algorithm)  
        ↓  
Alert System (Buzzer + LED)  
        ↓  
GSM Module (SMS Notification – Optional)  

---

## B. Hardware Architecture

### 1. Sensing Layer
- **Accelerometer (MPU6050 / ADXL345)**  
  - Measures acceleration in X, Y, and Z axes.
  - Communicates with the microcontroller via I2C protocol.
  - Provides raw acceleration data in digital form.

### 2. Processing Layer
- **Microcontroller (Arduino / ESP32)**  
  - Reads acceleration data periodically.
  - Applies filtering and threshold logic.
  - Controls alert mechanisms.
  - Interfaces with GSM module for SMS transmission.

### 3. Alert Layer
- **Buzzer**
  - Provides audible emergency alert.
- **LED**
  - Green LED: Normal system operation.
  - Red LED: Accident detected.

### 4. Communication Layer (Optional)
- **GSM Module (SIM800 / SIM900)**
  - Connected via UART serial communication.
  - Sends SMS using AT commands.
  - Transmits predefined emergency message.

### 5. Power Management
- Powered by rechargeable battery or USB supply.
- Voltage regulation ensures stable operation of all modules.

---

## C. Software Architecture

The software follows a loop-based embedded control structure:

1. Initialization Phase  
2. Continuous Monitoring Phase  
3. Accident Detection Phase  
4. Alert Activation Phase  
5. Reset/Recovery Phase  

---

# IV. Detailed Methodology

## A. System Initialization

When powered on:

1. Microcontroller initializes:
   - I2C communication for accelerometer.
   - Serial communication for GSM module.
   - GPIO pins for buzzer and LED.
2. Accelerometer calibration is performed:
   - Read baseline acceleration values.
   - Compensate for gravity (1g on Z-axis).
3. Threshold value is loaded into memory.

---

## B. Continuous Data Acquisition

The accelerometer continuously measures acceleration:

- Ax → Acceleration along X-axis  
- Ay → Acceleration along Y-axis  
- Az → Acceleration along Z-axis  

Sampling frequency: 50–100 Hz (configurable).

The microcontroller reads raw sensor values at fixed intervals using I2C communication.

---

## C. Signal Processing and Filtering

To avoid false positives caused by vibrations or noise:

1. Apply basic filtering:
   - Moving average filter or simple smoothing.
2. Calculate resultant acceleration magnitude:

\[
A = \sqrt{Ax^2 + Ay^2 + Az^2}
\]

This gives total impact force independent of direction.

3. Remove gravitational component (~1g).

---

## D. Threshold-Based Accident Detection

1. Define a threshold value (e.g., 8g–12g depending on calibration).
2. Compare resultant acceleration with threshold.

If:

\[
A > Threshold
\]

Then:
- Mark as potential accident event.

To reduce false triggers:
- Require threshold to be exceeded for a minimum time (e.g., 50–100 ms).
- Optionally check sudden deceleration pattern.

---

## E. Alert Activation

Once accident is confirmed:

### 1. Local Alert
- Activate buzzer for predefined duration (e.g., 5 seconds).
- Turn ON red LED.
- Turn OFF green LED.

### 2. Remote Alert (If GSM Module Integrated)

Steps:
1. Initialize GSM module.
2. Send AT commands:
   - Set SMS mode (AT+CMGF=1).
   - Specify phone number.
3. Send predefined emergency message:

   "Emergency: Accident detected. Immediate assistance required."

4. Wait for delivery confirmation.

---

## F. Reset Mechanism

System can reset by:

1. Automatic Reset:
   - After alert duration expires.
   - Resume monitoring mode.

2. Manual Reset:
   - User presses reset button.
   - Clears alert state.

---

## G. Testing and Calibration Procedure

1. Perform controlled impact simulations:
   - Gentle shaking (non-accident scenario).
   - Sudden jerk or drop simulation.
2. Adjust threshold to:
   - Minimize false positives.
   - Ensure real accidents trigger detection.
3. Log sensor data for validation.

---

## H. System Workflow Summary

1. Power ON  
2. Initialize modules  
3. Read acceleration data  
4. Compute magnitude  
5. Compare with threshold  
6. If exceeded → Trigger Alert  
7. Send SMS (optional)  
8. Reset and continue monitoring  

---

## I. Performance Considerations

- Response Time: < 1 second detection latency.
- Power Consumption: Optimized for battery use.
- Reliability: Stable operation under vibration.
- Scalability: Supports GPS and Io-Fi integration.

---

This architecture ensures modularity, real-time responsiveness, and expandability while maintaining low implementation complexity suitable for a college-level embedded systems project.
