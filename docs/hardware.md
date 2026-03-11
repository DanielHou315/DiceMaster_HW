# Programmable Dice Hardware

We used
- [Adafruit LCD 4’ square display ](https://www.adafruit.com/product/5827?gad_source=1&gclid=CjwKCAiAt5euBhB9EiwAdkXWO5H7oAc5CIfmYq2neX-Bj9BTCdCDGCmmzOEcVKMwPx-uup0nb6TqVRoCSccQAvD_BwE)
- [Esp32-s3 controller](https://www.adafruit.com/product/5800) for the above display. [Guides for using the board](https://learn.adafruit.com/adafruit-qualia-esp32-s3-for-rgb666-displays). 
- We name a Screen Module an assembly of the above screen and an ESP 32 processor along with its external shell. 


## Dimensions (for physical design)

ESP32 Board
- Board dimensions: 44.35 x 51.7mm. 
- Between holes: 31.15 x 36.85
- Hole dimension: 2.5mm (~0.1 inch).

Screen: 
- Board dimensions: 75.00mm x 79.10mm

Raspberry Pi 3B+ board: 
- Between holes: 49 x 58 mm
- Hole dimension: 2.5mm

PiSugar 3S Battery
- Holes same as above, height 19mm

Raspberry Pi Compute Module 4 
- Board dimensions: 40 x 55mm
- Between holes 33 x 48 mm
- Hole dimension: 2.5 mm


## Pinout Design

- [Raspberry Pi pinout](https://pinout.xyz/)
- [ESP-32 S3 guide](https://learn.adafruit.com/adafruit-qualia-esp32-s3-for-rgb666-displays)
- [Connecting screen to Raspberry Pi](https://pinout.xyz/pinout/spi)
- [Connecting MPU6050](https://www.instructables.com/How-to-Use-the-MPU6050-With-the-Raspberry-Pi-4/)

## Electrical Design

### Data Lines
**Screens**
We use 3 SPI buses to connect a total of six screens. To minimize CS switching time, we design an application layer protocol that is similar to CAN, where devices choose to accept/respond to messages based on the device ID. 

SPI 0: 
- SCK: 23
- MISO: 21 (not used)
- MOSI: 19
- CS: 24 (for both screens)

SPI 1: 
- SCK: 40
- MISO: 35 (not used)
- MOSI: 38
- CS: 12 (for both screens)

SPI 3: 
- SCK: 5
- MISO: 28 (not used)
- MOSI: 3
- CS: 27 (for both screens)

**IMU/Battery**
IMU and Battery uses I2C6. 
- SDA: 15
- SCL: 16

**Power Switch**

**Status LED**

**Buttons**

### Power Lines

### PCB

Yuzhen is working on this


## HW v2

### Reliability

### Cost Reduction

**Whiteboard version**: 


[Example dice](https://www.amazon.com/Scribbledo-Multipurpose-Colorful-Educational-Classroom/dp/B0B3S75YCV). 

### Batter Life Improvement

For HW v1, the batter lasts about 45 minutes when all displays are active, as was tested during serious play.


### Additional features

1. Cooling: adding a fan for Raspberry Pi, air channels, and optimized layout of 5V to 3.3V regulators. 
2. 