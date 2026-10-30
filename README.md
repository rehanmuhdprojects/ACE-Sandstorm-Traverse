# Sandstorm Traverse: ESP32 Motor Control & Anti-Stall Protection

**ACE Robotics | Electrical & Embedded Systems Subsystem**

## Project Overview

Sandstorm Traverse is an ESP32-based motor control and anti-stall protection system simulated using Wokwi. It demonstrates how a planetary rover can detect excessive motor current, attempt to free an obstructed wheel, and safely stop motor operation to reduce electrical stress.

**Wokwi Simulation:** https://wokwi.com/projects/479297368787302401

## Components

- ESP32 DevKit microcontroller
- Slide potentiometer (simulated 0–30 A current sensor)
- Green LED (left motor PWM indicator, GPIO 18)
- Blue LED (right motor PWM indicator, GPIO 19)
- Red LED (stall warning, GPIO 23)
- Three 220 Ω resistors

The potentiometer's signal output connects to GPIO 34.

## Working Principle

### 1. Motor Control

The system uses differential steering to calculate independent left and right motor commands:

- Left Speed = v − (ω × W / 2)
- Right Speed = v + (ω × W / 2)

Here, `v` is forward velocity, `ω` is turning rate, and `W` is rover width (0.4 m).

### 2. Current Monitoring and Stall Detection

The ESP32 reads simulated motor current using its ADC.

**Current (A) = ADC Reading × 30 / 4095**

- Below 10 A: Normal operation.
- Above 15 A: High-current warning.
- Above 20 A continuously for 350 ms: Stall detected.

The time threshold helps reject brief overcurrent spikes.

### 3. Automatic Recovery and Protection

Once a stall is detected, the controller:

1. Limits motor PWM magnitude to 20%.
2. Applies a 200 ms reverse pulse to attempt to free the wheel.
3. Stops both motor outputs and enters a protective lockout.
4. Activates the red warning LED.

The system can be reset by lowering the simulated current below 10 A and entering `r` in the Serial Monitor.

### 4. Non-Blocking Scheduling

Two periodic loops are implemented using `millis()`:

- **100 Hz:** Motor control, current monitoring, and stall detection.
- **20 Hz:** Command processing and telemetry reporting.

This allows communication and motor monitoring to continue during the recovery process without blocking delays.

## Testing and Results

The Wokwi simulation demonstrated:

| Test | Result |
|---|---|
| Fast loop frequency | 98–99 Hz |
| Communication loop frequency | 20 Hz |
| Differential steering | Passed |
| Sustained overcurrent detection | Passed |
| 20% PWM power reduction | Passed |
| Reverse recovery sequence | Passed |
| Motor lockout and warning LED | Passed |

The configured 350 ms detection and 200 ms reverse intervals have not been independently measured.

## Limitations

The project uses simulated current and LED-based PWM indicators rather than physical motors. Actual battery and motor protection requires appropriate motor drivers, current sensors, and hardware safety circuitry.

## Project Files

- `sketch.ino`: Commented ESP32 firmware
- `diagram.json`: Wokwi circuit configuration
- `README.md`: Documentation

## Conclusion

The project demonstrates differential steering, current-based stall detection, non-blocking embedded control, and automated recovery followed by safe motor shutdown.
