# DiceMaster HW

Hardware design files for the DiceMaster project, using KiCad.

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
