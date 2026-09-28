# Smart Industrial Safety and Gas Leak Detection System

An ESP32-based IoT safety monitoring system designed to detect gas leakage, monitor human presence and proximity to the affected area, classify the situation into different safety levels, and provide local and remote alerts.

## Overview

Gas leakage can create serious safety risks in residential, commercial, and industrial environments. This project combines multiple sensors with an ESP32 to continuously monitor environmental conditions and determine the severity of a detected gas leak.

The system uses:

- MQ-series gas sensor for gas-level monitoring
- PIR sensor for human motion detection
- HC-SR04 ultrasonic sensor for proximity measurement
- SSD1306 OLED for local status display
- LEDs and buzzer for local alerts
- ThingSpeak for remote data logging
- Telegram Bot API for remote alerts

The system classifies the monitored environment into three states:

- **SAFE**
- **HAZARDOUS**
- **CRITICAL**

## Key Features

- Real-time gas-level monitoring
- Seven-sample averaging for sensor readings
- Human motion detection using PIR
- Distance measurement using an ultrasonic sensor
- Three-level safety classification
- OLED-based local monitoring
- LED and buzzer warning system
- ThingSpeak cloud data logging
- Telegram-based remote notifications
- Edge-triggered alerts to avoid repeatedly sending the same notification

## System Working

The ESP32 continuously collects readings from the gas sensor, PIR sensor, and ultrasonic sensor.

Seven gas sensor readings are collected and averaged before the safety condition is evaluated. The ultrasonic sensor measures the distance of an object from the sensor assembly.

The system then evaluates the sensor values using predefined thresholds.

### Safety Classification

| Condition | Status | Status Code |
|---|---|---:|
| Gas value < 1750 | SAFE | 0 |
| Gas value >= 1750 without critical human-presence condition | HAZARDOUS | 2 |
| Gas value >= 1750, motion detected, and distance < 100 cm | CRITICAL | 1 |

### SAFE

When the gas reading is below the configured threshold:

- Green LED is turned ON
- Red LED is turned OFF
- Buzzer remains OFF
- System status is displayed as `SAFE`

### HAZARDOUS

When the gas reading reaches or exceeds the threshold but the critical human-presence conditions are not satisfied:

- Green LED is turned OFF
- Red LED is turned ON
- Buzzer is activated
- A Telegram warning is sent when the state changes
- System status is displayed as `HAZARDOUS`

### CRITICAL

When the gas reading reaches or exceeds the threshold and:

- Motion is detected
- Distance is less than 100 cm

the system enters the critical state:

- Green LED is turned OFF
- Red LED is turned ON
- Buzzer is activated
- A Telegram emergency alert is sent when the state changes
- System status is displayed as `CRITICAL`

## Hardware Components

| Component | Purpose |
|---|---|
| ESP32 | Main microcontroller and Wi-Fi communication |
| MQ-series gas sensor | Gas-level detection |
| PIR motion sensor | Human motion detection |
| HC-SR04 ultrasonic sensor | Proximity/distance measurement |
| SSD1306 OLED | Local display of sensor values and status |
| Red LED | Warning indication |
| Green LED | Safe-state indication |
| Buzzer | Audible warning |
| Regulated power supply | Power for the prototype |

## Pin Configuration

| Component | ESP32 Pin |
|---|---:|
| MQ-series Gas Sensor | GPIO 34 |
| PIR Motion Sensor | GPIO 32 |
| HC-SR04 TRIG | GPIO 5 |
| HC-SR04 ECHO | GPIO 18 |
| Buzzer | GPIO 25 |
| Red LED | GPIO 26 |
| Green LED | GPIO 2 |
| SSD1306 OLED | I2C |
| OLED Address | `0x3C` |

## Circuit Diagram

![Circuit Diagram](docs/circuit-diagram.png)

## Software

The project was developed using:

- Arduino IDE
- Embedded C/C++
- ESP32 Arduino board support
- Wi-Fi communication
- HTTPClient
- Wire
- Adafruit GFX
- Adafruit SSD1306
- ThingSpeak
- Telegram Bot API

## Sensor Processing

The system collects seven gas sensor readings during each measurement cycle and calculates their average:

```text
Average Gas Value =
(Gas1 + Gas2 + Gas3 + Gas4 + Gas5 + Gas6 + Gas7) / 7