# Runbook: PCB Ordering and Assembly

## Prerequisites

- KiCad 9.0+ installed (to regenerate Gerbers if needed).
- Access to `PCB/DiceMaster_Router/gerbers/` — pre-exported Gerbers are committed, so re-export is only needed after schematic/layout changes.
- JLCPCB account (or PCBWay account as alternative).
- Component sourcing: DigiKey, LCSC, or JLCPCB parts catalog for SMD components; DigiKey or Adafruit for through-hole connectors.
- Fine-tip soldering iron and hot-air station, or use JLCPCB's SMT assembly service for SMD parts.

## Steps

### 1. Export Gerbers from KiCad (if files have changed)

1. Open `PCB/DiceMaster_Router/DiceMaster_Router.kicad_pro` in KiCad.
2. Open **PCB Editor**.
3. Run **DRC** (Inspect → Design Rules Checker). Resolve all errors before exporting.
4. Go to **File → Fabrication Outputs → Gerbers**.
5. Select output directory: `PCB/DiceMaster_Router/gerbers/`.
6. Enable all copper layers (F.Cu, B.Cu), edge cuts, silkscreen (F.Silkscreen, B.Silkscreen), soldermask (F.Mask, B.Mask), and paste (F.Paste, B.Paste).
7. Click **Plot**.
8. Then generate drill files: **File → Fabrication Outputs → Drill Files**. Export both PTH and NPTH drill files to the same gerbers directory.
9. Zip the contents of `gerbers/` — a pre-zipped archive is at `gerbers/DiceMaster_Router_gerbers.zip`.

### 2. Upload to JLCPCB

1. Go to [jlcpcb.com](https://jlcpcb.com) and click **Order Now**.
2. Upload `DiceMaster_Router_gerbers.zip`.
3. Set PCB parameters:
   - **Layers**: 2
   - **Dimensions**: ≤ 60 × 60 mm (verify from KiCad board outline)
   - **PCB thickness**: 1.6 mm
   - **Stackup**: FR4
   - **Copper weight**: 1 oz
   - **Surface finish**: HASL or LeadFree HASL (ENIG if budget allows — better for fine-pitch SOT-23 pads)
4. For **SMT Assembly (PCBA)**: toggle "PCB Assembly" on. Upload the BOM CSV (`gerbers/BOM_JLCPCB.csv`) and CPL CSV (`gerbers/DiceMaster_Router-CPL.csv`). Review component matches in the JLCPCB parts selector.

### 3. Component Sourcing

SMD components are sourced via JLCPCB's parts catalog (LCSC part numbers in the BOM) or DigiKey for alternatives. Key items to verify before ordering:

| Part | Critical Spec | Reference |
|------|--------------|-----------|
| TPS565201DDCR | SOT-23-6 buck converter | IC1 |
| L1 (3.3 µH inductor) | ≥ 3 A saturation current, NR-50xx 5 × 5 mm footprint (e.g., Sunlord SWPA5040S3R3NT) | L1 |
| C1, C3, C_OUT1–3 (22 µF, 0805) | **≥ 10 V voltage rating** — 6.3 V-rated parts derate severely at 5 V | capacitors |
| R_BOT1 (30.1 kΩ) | **1% tolerance** — sets 3.3 V output via feedback divider | R_BOT1 |
| Q_FAN1 (2N7002) | SOT-23, 115 mA continuous — only suitable for the WINSINN 30 mm 0.09 A fan; upgrade MOSFET for any larger fan | Q_FAN1 |
| D_FAN1 (1N5819) | SOD-323 Schottky (equivalent: 1N5819HW or B5819W) | D_FAN1 |

See `PCB/DiceMaster_Router/BOM_ordering_notes.md` for the full BOM with ordering notes.

### 4. Hand-Soldered Connectors

The following connectors are not included in JLCPCB PCBA — solder by hand after the board arrives:

| Ref | Part |
|-----|------|
| J1 | 2×20 pin socket header, 2.54 mm pitch (Raspberry Pi 40-pin female header) |
| J_A1–J_F1 | JST GH 6-pin SMD horizontal (SM06B-GHS-TB) |
| J_BTN1 | JST GH 5-pin SMD horizontal (SM05B-GHS-TB) |
| J_SWITCH1 | JST GH 3-pin SMD horizontal (SM03B-GHS-TB) |
| J_FAN1 | JST GH 2-pin SMD horizontal (SM02B-GHS-TB) |
| PiSugarPowerConnector1 | JST GH 2-pin SMD horizontal (SM02B-GHS-TB) |

The GY-BMI160 breakout (U1) mounts via 2.54 mm pin headers into a 7+5 pin socket footprint (10.16 mm row spacing).

### 5. SMD Soldering (if not using PCBA service)

1. Apply solder paste to SMD pads using a stencil (order stencil from JLCPCB on the same order).
2. Place components with tweezers. The TPS565201 SOT-23-6 is the most challenging part — use flux and a fine iron tip or hot-air at ~300 °C.
3. Reflow on a hotplate or in a toaster-oven reflow setup.
4. Inspect under magnification; touch up any bridges with flux and solder wick.

## Verify

1. **Visual inspection**: check all SMD pads for solder bridges, especially under IC1 (TPS565201 SOT-23-6).
2. **Continuity test (before powering on)**:
   - Measure resistance between 3.3 V rail and GND with a multimeter (should be several kΩ, not a short).
   - Probe each JST GH connector pin against the schematic nets.
3. **Power-on test**:
   - Apply 5 V to the PiSugar power connector.
   - Measure 3.3 V on the 3.3 V rail output (within ±2% = 3.234–3.366 V).
4. **Functional test**:
   - Seat the Raspberry Pi on J1.
   - Connect one face module via J_A1.
   - Boot the Pi and verify SPI communication with the ESP32-S3 on that face.

## Troubleshooting

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| No 3.3 V output | TPS565201 not soldering properly, or L1 saturation current exceeded | Reflow IC1; verify L1 is ≥3 A rating |
| 3.3 V output too high or too low | R_BOT1 wrong value or wrong tolerance | Verify R_BOT1 is 30.1 kΩ ±1% |
| Fan does not spin | Q_FAN1 gate not driven, or MOSFET damaged | Check PWM signal on gate; verify D_FAN1 is not shorted |
| Intermittent SPI errors | Bad JST cable crimp or wrong pin mapping | Re-seat JST connectors; verify cable wiring against schematic |
| Pi does not boot | 3.3 V rail sagging under load | Check all C_OUT capacitors are ≥10 V rated; check L1 saturation rating |
