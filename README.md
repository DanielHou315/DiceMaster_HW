# DiceMaster HW

Hardware design files for the DiceMaster project, using KiCad.

## Board Status

The current production board is **PCB v1** (`PCB/DiceMaster_Router/`), finalized
for production on 2026-04-05 (commit `4ef69b5`, "finalized pcb v1 for prod").
Gerbers and the JLCPCB BOM under `PCB/DiceMaster_Router/gerbers/` are from this
run; the manufactured boards were received around mid-April 2026.

### Known issues (PCB v1 — unverified, to be handled later)

These issues were observed on the v1 boards but **not yet root-caused**:

1. **Some JST ports could not be soldered during manufacturing.** One or more of
   the JST GH SMD connectors (`J_A1`–`J_F1`, `J_BTN1`, `J_SWITCH1`, `J_FAN1`,
   `PiSugarPowerConnector1` — see `docs/runbooks/pcb-assembly.md` §4) could not
   be soldered on during assembly. Cause not yet determined (footprint/pad
   geometry vs. process).

2. **5V→3.3V converter does not work.** The onboard buck converter (IC1,
   TPS565201, SOT-23-6) did not produce a working 3.3 V rail after hand-soldering
   the part. It is **not yet known whether this is a design issue or a soldering
   issue** — this needs to be re-verified before the next production run. See the
   "No 3.3 V output" row in `docs/runbooks/pcb-assembly.md` §Troubleshooting and
   the converter subcircuit (IC1 / L1 / R_BOT1 / output caps) in the schematic.

> Status: both issues are **open and unverified**. Do not assume v1 boards have a
> working 3.3 V rail. Revisit during the next hardware bring-up / v2 design.

## KiCad MCP Server Setup

This project is configured with [kicad-mcp](https://github.com/lamaalrajih/kicad-mcp) to enable Claude Code to interact with KiCad projects directly.

### Prerequisites

- KiCad 9.0+ (installed at `/Applications/KiCad/KiCad.app`)
- Python 3.10+ and `uv`

### Installation

The MCP server is installed at `~/Applications/kicad-mcp`. To reinstall:

```bash
git clone https://github.com/lamaalrajih/kicad-mcp.git ~/Applications/kicad-mcp
cd ~/Applications/kicad-mcp
make install
cp .env.example .env
# Edit .env to set KICAD_SEARCH_PATHS to your project directory
```

### Usage

Start Claude Code from this directory — the `.mcp.json` file automatically connects the KiCad MCP server.

```bash
cd DiceMaster_HW
claude
```

Claude can then:

- **List projects** — discover KiCad projects in the configured search paths
- **Open projects** — launch KiCad with a specific project
- **Inspect schematics** — read components, nets, and symbols
- **Analyze PCBs** — view board properties, footprints, and design rules
- **Manage libraries** — search and retrieve KiCad symbols/footprints

### Configuration

- **`.mcp.json`** — MCP server config (points to the kicad-mcp installation)
- **`~/Applications/kicad-mcp/.env`** — KiCad paths and project search directories

To add more search paths, edit `~/Applications/kicad-mcp/.env`:

```
KICAD_SEARCH_PATHS=~/Code/DiceMaster/DiceMaster_HW,~/other/projects
```
