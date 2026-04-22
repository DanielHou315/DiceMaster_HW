# ADR-002: Custom PCB vs Off-the-Shelf Dev Boards

## Status

Accepted

## Context

DiceMaster needs to fit a Raspberry Pi, six ESP32-S3 screen controllers, an IMU, a battery board, a buck regulator, and a cooling fan inside a cube-shaped enclosure. The enclosure face dimensions are constrained by the 75 × 79 mm screen panels.

**Prototype v1** used off-the-shelf Adafruit dev boards (Adafruit Qualia ESP32-S3 #5800, 44.35 × 51.7 mm each) wired together with jumper cables. This proved the software stack but exposed hardware problems:

- The Qualia ESP32-S3 dev boards protrude connectors (USB-C, JST, headers) on all four edges, making flush face mounting impossible.
- Six individual dev boards plus a breadboard-style wiring harness do not fit reliably inside the enclosure.
- Loose jumper wire connections caused intermittent SPI failures during handling.
- Battery life was ~45 minutes under full load, partly due to inefficient power distribution.

A production-grade design requires tight, mechanically stable integration.

## Decision

Use a **custom DiceMaster Router PCB** (in `PCB/DiceMaster_Router/`) that centralises all inter-board routing.

Key design choices on the custom PCB:

- **Bare ESP32-S3 modules** (via Adafruit Qualia boards retained for now, connected via JST GH 6-pin cables rather than loose jumpers) replace the prototype wiring harness.
- **JST GH 6-pin connectors (J_A1–J_F1)** provide one keyed, latching connector per face — no loose wires.
- **TPS565201 buck converter** on the Router board provides a regulated 3.3 V rail directly, eliminating per-board linear regulators.
- The Router board footprint is ≤ 60 × 60 mm so it fits beneath the Pi inside the enclosure.
- Gerbers are committed and have been ordered from JLCPCB with SMD assembly for passive components.

## Consequences

**Positive:**

- Compact, mechanically reliable form factor — all inter-board connections are short, keyed cables.
- Centralised power regulation reduces heat and improves efficiency over per-board LDOs.
- Production-ready: the PCB can be re-ordered from JLCPCB using the committed Gerber and BOM files.
- Eliminates the intermittent SPI failures seen with jumper wiring.

**Negative:**

- PCB manufacturing lead time (~5–7 business days for JLCPCB standard service) slows hardware iteration.
- SMD soldering or PCBA service required for passive components; hand-soldering the TPS565201 SOT-23-6 demands fine-pitch skill.
- A PCB spin is needed to change any electrical interface.

## Alternatives Considered

**Prototype v1: dev boards with breakout wiring**
Used in the initial prototype. Fast to iterate, but mechanically unreliable and physically too large for the target enclosure. Retained as a software development reference only.

**Flexible PCB (FPC)**
An FPC could route connections along the inside faces of the cube enclosure. Rejected for v2 because FPC tooling and design complexity is significantly higher, and the rigid Router PCB approach already solves the mechanical reliability problem at lower cost.
