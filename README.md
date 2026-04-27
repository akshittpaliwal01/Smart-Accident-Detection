<img width="1408" height="768" alt="Gemini_Generated_Image_hgg97thgg97thgg9" src="https://github.com/user-attachments/assets/49a8ef23-7600-4be3-b90e-fb2a7d40e115" /># Project Title (Biggest Heading)
## Overview (Section Heading)
### Hardware Components (Sub-section)
#### Pin Mapping (Smaller sub-section)

## 🛠️ Hardware Requirements
* Arduino Uno / ESP32
* MPU6050 Accelerometer
* Neo-6M GPS Module
* SIM800L GSM Module

## 🔌 Circuit Connection
* **MPU6050 VCC** -> Arduino 5V
* **MPU6050 GND** -> Arduino GND
* **MPU6050 SCL** -> Arduino A5
* **MPU6050 SDA** -> Arduino A4

## 🚀 How to Use
1. Clone this repository.
2. Open `Accident_Alert_System.ino` in Arduino IDE.
3. Install the `TinyGPS++` library.
4. Upload the code to your board

## 🛠️ Hardware Requirements
* Arduino Uno / ESP32
* MPU6050 Accelerometer
* Neo-6M GPS Module
* SIM800L GSM Module

## 🔌 Circuit Connection
* **MPU6050 VCC** -> Arduino 5V
* **MPU6050 GND** -> Arduino GND
* **MPU6050 SCL** -> Arduino A5
* **MPU6050 SDA** -> Arduino A4

## 🚀 How to Use
1. Clone this repository.
2. Open `Accident_Alert_System.ino` in Arduino IDE.
3. Install the `TinyGPS++` library.
4. Upload the code to your board.

## 🔌 Hardware Connections
The system is built using an **ESP32** and uses the following pin mapping:

### 1. I2C Devices (OLED & MPU6050)
* **SDA:** GPIO 21
* **SCL:** GPIO 22

### 2. Communication Modules
* **GPS (Neo-6M):** TX -> GPIO 32 | RX -> GPIO 33
* **GSM (SIM800L):** TX -> GPIO 26 | RX -> GPIO 27

### 3. Peripherals
* **Buzzer:** GPIO 25
* **SOS Button:** GPIO 14 (Internal Pull-up)

> **Note:** Ensure the SIM800L is powered by an external source capable of 2A bursts to prevent system brownouts.


## 🖼️ Circuit Diagram
![Circuit Diagram]<img width="1408" height="768" alt="circuit diagram" src="https://github.com/user-attachments/assets/efd50d4a-ab96-49ee-ab31-734810010720" />





![Circuit Diagram](circuit.jpg)
