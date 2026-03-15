# CAN-Based Throttle Input System using Arduino UNO Q and ESP32
##  Team members: 
              Ghoorah Hemshini
              Boodhram Ritviksingh
Both team members contributed jointly to the hardware setup, CAN communication testing, debugging, implementation, and documentation.

## Overview
This project implements a CAN-based throttle input system using an Arduino UNO Q and an ESP32, connected through MCP2515 CAN controller modules.
The project was developed in two stages:

1. **Fixed CAN frame transmission test**
   - The Arduino UNO Q sends a fixed CAN message.
   - The ESP32 receives and displays the message.
   - This validates wiring, SPI communication, CAN configuration, and bus termination.

2. **Potentiometer-based throttle transmission**
   - A potentiometer is connected to the Arduino UNO Q and used as a **throttle input simulator**.
   - The Arduino reads the analog voltage, converts it into a throttle percentage, and transmits it over CAN.
   - The ESP32 receives the throttle value and displays it on the Serial Monitor.

This is a **bench-top embedded systems project** intended for learning, testing, and demonstration purposes.

## Objectives

- Establish reliable CAN communication between two microcontrollers
- Use the MCP2515 CAN controller with SPI interface
- Simulate a throttle input using a potentiometer
- Transmit throttle percentage over CAN bus
- Receive and decode throttle data on a second node
- Build a foundation for future EV, motor-control, or BMS-related projects

## System Architecture

### Sender Node
- **Board:** Arduino UNO Q
- **Function:** Read potentiometer and send throttle value over CAN

### Receiver Node
- **Board:** ESP32
- **Function:** Receive CAN message and display throttle value

### Communication Interface
- **CAN Controller:** MCP2515
- **Physical Layer:** CAN transceiver integrated on MCP2515 module
- **Protocol:** Classic CAN
- **Bitrate:** 125 kbps

## Hardware Used

- Arduino UNO Q
- ESP32 development board
- 2 × MCP2515 CAN modules
- 1 × potentiometer
- jumper wires
- USB cables
- breadboard or direct jumper wiring

### Notes
- Both nodes must share a **common ground**
- CANH must connect to CANH
- CANL must connect to CANL
- With power off, the full bus should measure approximately **60 Ω** between CANH and CANL

## Working Principle
The potentiometer acts as a **throttle command simulator**.
1. The Arduino UNO Q reads the potentiometer voltage using an analog pin
2. The analog value is converted to a digital ADC reading
3. The ADC value is mapped to a throttle percentage from **0% to 100%**
4. The throttle percentage is inserted into a CAN frame
5. The CAN frame is transmitted over the CAN bus
6. The ESP32 receives the frame and displays the throttle percentage

## Wiring
## Potentiometer to Arduino UNO Q

| Potentiometer Pin | Arduino UNO Q |
|     ---           |      ---      |
| Outer pin 1       | 3.3V |
| Middle pin        | A0 |
| Outer pin 2       | GND |

If the throttle direction is reversed, swap the two outer pins.

## Arduino UNO Q to MCP2515

| Arduino UNO Q | MCP2515 |
|       ---     |   ---   |
| D10           | CS |
| D11           | MOSI / SI |
| D12           | MISO / SO |
| D13           | SCK |
| GND           | GND |
| VCC           | VCC |
| D2            | INT |

## ESP32 to MCP2515

| ESP32     | MCP2515 |
|   ---     |  ---    |
| GPIO 5    | CS |
| GPIO 23   | MOSI / SI |
| GPIO 19   | MISO / SO |
| GPIO 18   | SCK |
| GND       | GND |
| VCC       | VCC |
| GPIO 4    | INT |

## CAN Bus Wiring

| MCP2515 Node A | MCP2515 Node B |
|     ---        |     ---        |
| CANH           | CANH |
| CANL           | CANL |
| GND            | GND |

### Important
- Connect **CANH ↔ CANH**
- Connect **CANL ↔ CANL**
- Connect **GND ↔ GND**
- With both end terminations enabled, the complete bus should measure **~60 Ω** between CANH and CANL when power is off

## Software and Libraries
### Development Environment
- Arduino IDE
### Required Libraries
```cpp
#include <SPI.h>
#include <mcp2515.h>
```
## MCP2515 Library
Use the autowp MCP2515 library.

## CAN Configuration
### Bitrate
Both nodes use:
125 kbps

### Crystal Configuration
Each MCP2515 module must match its actual crystal frequency:
MCP_8MHZ for modules marked 8.000
MCP_16MHZ for modules marked 16.000
This setting is critical for proper communication.

## Project Development Stages

### Stage 1: Fixed CAN Frame Test
Before integrating the potentiometer, a fixed CAN frame was transmitted from the Arduino UNO Q to the ESP32.

### Test Frame
CAN ID: 0x100
DLC: 4
Data: 11 22 33 44

### Purpose
This stage validated:
- SPI wiring
- MCP2515 initialization
- CAN bus termination
- Successful transmission and reception

### Expected Receiver Output
ID: 0x100 DLC: 4 DATA: 11 22 33 44
Stage 2: Potentiometer Throttle Transmission
After successful basic communication, the potentiometer was added to the Arduino UNO Q.

### Sender Behavior
The Arduino:
- Reads the analog value on A0
- Converts it into a percentage
-Inserts the value into the CAN frame
- Sends it cyclically over CAN

### Receiver Behavior
The ESP32:
- Waits for CAN frames
- Filters for CAN ID 0x100
- Extracts the first data byte
- Interprets it as throttle percentage

## How to Run the Project
-Connect the potentiometer to the Arduino UNO Q
- Connect both MCP2515 modules to their respective microcontrollers
- Connect the CAN bus:
- CANH ↔ CANH
- CANL ↔ CANL
- GND ↔ GND
- Verify the bus resistance between CANH and CANL with power off (expected value: ~60 Ω)
- Upload the sender sketch to the Arduino UNO Q
- Upload the receiver sketch to the ESP32
- Open both Serial Monitors
- Rotate the potentiometer and observe transmitted and received throttle values
### Expected Results
### Arduino UNO Q Serial Monitor
```
Raw: 105  Throttle: 10%  Sent OK
Raw: 512  Throttle: 50%  Sent OK
Raw: 918  Throttle: 89%  Sent OK
```
### ESP32 Serial Monitor
```
Throttle received: 10%
Throttle received: 50%
Throttle received: 89%
```
## Validation
The following tests were completed successfully during development:
- Local loopback test on Arduino UNO Q MCP2515
- Local loopback test on ESP32 MCP2515
- Fixed CAN frame transmission test
- Live CAN bus communication verification
- Potentiometer-based throttle transmission
- Reception and decoding of throttle value on ESP32
- Bus termination verification using resistance measurement

 ## Conclusion
A functional CAN-based throttle input system was developed using an Arduino UNO Q, an ESP32, and MCP2515 CAN modules. The project successfully demonstrated analog throttle acquisition, CAN message creation, transmission over a properly terminated CAN bus, and real-time reception and decoding on a second microcontroller. This project provides a solid foundation for more advanced automotive, EV, or embedded control applications.
