# DiceMaster Router - BOM & Ordering Notes

## JLCPCB SMD Assembly Parts

### Resistors (0402, 1%)
| Ref | Value | Package | Notes |
|-----|-------|---------|-------|
| R_FG1 | 1kΩ | 0402 | MOSFET gate resistor |
| R_FPD1 | 100kΩ | 0402 | MOSFET gate pull-down |
| R_Top1 | 100kΩ | 0402 | TPS565201 feedback divider top |
| R_BOT1 | 30.1kΩ | 0402 | TPS565201 feedback divider bottom. **Must be 1% tolerance** — sets 3.3V output voltage |

### Capacitors (0402 / 0805)
| Ref | Value | Package | Notes |
|-----|-------|---------|-------|
| C_BST1 | 100nF | 0402 | TPS565201 bootstrap cap |
| C1 | 22µF | 0805 | TPS565201 input cap. **Must be ≥10V rated.** e.g., Samsung CL21A226MAQNNNE |
| C3 | 22µF | 0805 | TPS565201 input cap. Same spec as C1 |
| C_OUT1 | 22µF | 0805 | TPS565201 output cap. ≥10V rated |
| C_OUT2 | 22µF | 0805 | TPS565201 output cap. ≥10V rated |
| C_OUT3 | 22µF | 0805 | TPS565201 output cap. ≥10V rated |

**WARNING:** 0805 22µF ceramic caps at 6.3V rating have severe DC bias derating — at 5V they may only provide ~10µF effective capacitance. Always pick **≥10V rated** parts to maintain full capacitance at operating voltage.

### Inductor
| Ref | Value | Package | Notes |
|-----|-------|---------|-------|
| L1 | 3.3µH | 5x5mm (NR-50xx) | Power inductor for TPS565201. **Must be rated ≥3A saturation current.** Search JLCPCB for "3.3uH 5040" or "3.3uH 5020". e.g., Sunlord SWPA5040S3R3NT |

### Semiconductor
| Ref | Value | Package | Notes |
|-----|-------|---------|-------|
| IC1 | TPS565201DDCR | SOT-23-6 (SOT95P280X110-6N) | 5V→3.3V buck converter |
| Q_FAN1 | 2N7002 | SOT-23 | N-channel MOSFET, fan driver. 115mA max continuous |
| D_FAN1 | 1N5819 | SOD-323 | Schottky flyback diode. SOD-323 equivalent: 1N5819HW or B5819W |

### Fan Current Budget
- Fan: WINSINN 30mm 5V Blower (0.09A / 0.5W)
- Q_FAN (2N7002) rated 115mA continuous
- Margin: 26mA (23%) — acceptable for this fan, but do NOT substitute a larger fan without upgrading the MOSFET (e.g., IRLML6344 for >1A fans)

## Hand-Soldered Connectors

| Ref | Part | Notes |
|-----|------|-------|
| J1 | 2x20 pin socket header, 2.54mm | Raspberry Pi 40-pin female header |
| J_A1–J_F1 | SM06B-GHS-TB | JST GH 6-pin SMD horizontal |
| J_BTN1 | SM05B-GHS-TB | JST GH 5-pin SMD horizontal |
| J_SWITCH1 | SM03B-GHS-TB | JST GH 3-pin SMD horizontal |
| J_FAN1 | SM02B-GHS-TB | JST GH 2-pin SMD horizontal |
| PiSugarPowerConnector1 | SM02B-GHS-TB | JST GH 2-pin SMD horizontal |

## IMU Breakout (separate board, plugs in)

| Part | Notes |
|------|-------|
| GY-BMI160 | 18.3mm × 13.3mm breakout board. Mounts via pin headers into U1 footprint (7+5 pin, 10.16mm row spacing, 2.54mm pitch) |

## PCB Specs for JLCPCB Order
- Max board size: 60mm × 60mm
- Layers: 2 (or 4 if routing is tight)
- Copper weight: 1oz standard
- Surface finish: HASL or LeadFree HASL
