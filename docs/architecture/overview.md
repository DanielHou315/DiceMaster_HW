# Hardware Architecture Overview

## Summary

DiceMaster is a programmable electronic die with six faces. Each face displays a screen driven by a dedicated ESP32-S3 processor. A central Raspberry Pi (CM4 or 3B+) coordinates all six faces over SPI and reads orientation from an IMU over I2C. A TPS565201 buck converter on the Router PCB steps the battery voltage down to 3.3 V for all logic.

---

## Boards and Modules

### Raspberry Pi (Central Controller)

The Raspberry Pi acts as the main application processor. Either a Raspberry Pi 3B+ or a Compute Module 4 (CM4) is supported:

- **Raspberry Pi 3B+**: 85 × 56 mm, mounting holes 49 × 58 mm apart, 2.5 mm diameter.
- **Raspberry Pi CM4**: 40 × 55 mm, mounting holes 33 × 48 mm apart, 2.5 mm diameter.

The Pi runs the `dicemaster_central` ROS 2 package and owns the SPI and I2C buses.

### DiceMaster Router PCB (`PCB/DiceMaster_Router/`)

The Router is a custom 2-layer KiCad PCB (≤ 60 × 60 mm, 1 oz copper) that acts as the electrical hub of the dice. It provides:

- A **2×20 pin female header (J1)** that mates directly to the Raspberry Pi 40-pin GPIO header.
- **Six JST GH 6-pin connectors (J_A1–J_F1)**, one per screen/ESP32 face module, carrying SPI data lines and 3.3 V power.
- A **TPS565201DDCR** synchronous buck regulator (5 V → 3.3 V, SOT-23-6) with supporting passives (L1, C1/C3/C_OUT1-3, R_Top1/R_BOT1, C_BST1).
- A **2N7002 N-channel MOSFET (Q_FAN1)** and **1N5819 Schottky diode (D_FAN1)** for PWM-controlled fan driving.
- A **GY-BMI160 IMU breakout** socket (U1, 7+5 pin headers, 10.16 mm row spacing) for orientation sensing over I2C.
- A **JST GH 5-pin connector (J_BTN1)** for the button array.
- A **JST GH 3-pin connector (J_SWITCH1)** for the power switch.
- A **JST GH 2-pin connector (J_FAN1)** for the 30 mm 5 V blower fan.
- A **JST GH 2-pin connector (PiSugarPowerConnector1)** for the PiSugar 3S battery board.

Custom KiCad symbols and footprints for the TPS565201 and BMI160 breakout live in `PCB/DiceMaster_Router/Components/` and `Components.pretty/`.

### Screen Modules (× 6)

Each screen module is an assembly of:

- **Adafruit 4-inch square RGB666 LCD** (Adafruit #5827), PCB 75.00 × 79.10 mm.
- **Adafruit Qualia ESP32-S3 controller** (Adafruit #5800), PCB 44.35 × 51.7 mm, mounting holes 31.15 × 36.85 mm apart, 2.5 mm diameter.
- A physical shell that forms one face of the die enclosure (see Enclosure below).

Each ESP32-S3 receives commands from the Pi over SPI and drives its attached display directly.

### PiSugar 3S Battery Board

The PiSugar 3S attaches to the underside of the Raspberry Pi. Its mounting holes match the Pi's 49 × 58 mm pattern. It connects to the Router PCB via `PiSugarPowerConnector1`. Battery life is approximately 45 minutes with all six displays active.

---

## Interconnects

### SPI (Pi → ESP32-S3 face modules)

Three SPI buses each carry two face modules. All six modules share a CAN-inspired application-layer protocol: each ESP32-S3 accepts or ignores messages based on a device ID, so a single CS line per bus services two modules without per-device chip-select toggling.

| Bus   | SCK | MOSI | MISO (unused) | CS |
|-------|-----|------|---------------|----|
| SPI 0 | 23  | 19   | 21            | 24 |
| SPI 1 | 40  | 38   | 35            | 12 |
| SPI 3 | 5   | 3    | 28            | 27 |

Pin numbers are Raspberry Pi physical/BCM GPIO as documented at [pinout.xyz](https://pinout.xyz/pinout/spi).

Each face module connects to the Router PCB via a JST GH 6-pin cable (J_A1–J_F1), carrying SCK, MOSI, CS, 3.3 V, and GND.

### I2C (Pi → IMU and Battery)

The BMI160 IMU breakout and PiSugar battery management share **I2C bus 6**:

- SDA: GPIO 15
- SCL: GPIO 16

### Power Distribution

```
Battery (LiPo via PiSugar 3S)
  └─► PiSugar board → Raspberry Pi (5 V USB-style)
  └─► Router PCB PiSugarPowerConnector1
        └─► TPS565201 buck converter (5 V → 3.3 V)
              └─► 3.3 V rail → six face module JST connectors
              └─► 3.3 V rail → logic on Router PCB
  └─► Fan connector (5 V, MOSFET-switched via Q_FAN1)
```

---

## Enclosure (shell/)

The `shell/` directory holds the physical enclosure design files for the dice body. The shell is shaped as a cube with six flat faces; each face seats one screen module assembly. The enclosure design is currently a placeholder — no CAD files have been added yet.

Reference for physical form factor: [Scribbledo foam cube dice](https://www.amazon.com/Scribbledo-Multipurpose-Colorful-Educational-Classroom/dp/B0B3S75YCV).

---

## HW v1 vs v2

**v1** used Adafruit dev boards with external wiring. Battery life was ~45 minutes under full load.

**v2 goals** (in progress): custom compact PCB (this Router board), improved battery life, cooling fan, optimized 5 V→3.3 V regulator layout, and reduced cost via bare modules.
