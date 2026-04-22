# Runbook: KiCad Project Setup

## Prerequisites

- macOS (or Linux/Windows) with administrator access.
- KiCad 9.0 or later. Download from [kicad.org](https://www.kicad.org/download/).
  - macOS default install path: `/Applications/KiCad/KiCad.app`.
- Python 3.10+ and `uv` (for the kicad-mcp server, if using Claude Code integration).
- The `DiceMaster_HW` repository cloned locally (this repo contains the `.kicad_pro` file).

## Steps

### 1. Install KiCad

Download and install KiCad 9.0+ from [kicad.org](https://www.kicad.org/download/). Accept the default install location. KiCad bundles its own footprint and symbol libraries — no separate library install is needed for standard parts.

### 2. Open the Project

1. Launch **KiCad**.
2. Select **File → Open Project**.
3. Navigate to `DiceMaster_HW/PCB/DiceMaster_Router/` and open `DiceMaster_Router.kicad_pro`.
4. The KiCad project manager opens, showing links to the schematic (`DiceMaster_Router.kicad_sch`) and PCB layout (`DiceMaster_Router.kicad_pcb`).

### 3. Install Project-Local Footprint Libraries

The Router project uses custom footprints for the TPS565201 (SOT-23-6 variant) stored in `PCB/DiceMaster_Router/Components.pretty/`. These are referenced via a project-local `fp-lib-table` file already committed to the repo — KiCad loads them automatically when the project is opened from its directory.

To verify:
1. Open the **PCB Editor** (click the PCB layout icon in the project manager).
2. Go to **Preferences → Manage Footprint Libraries**.
3. The **Project Specific Libraries** tab should list `Components` pointing to `${KIPRJMOD}/Components.pretty`.

If the library is missing or shows a path error, click **Add Existing** and browse to `PCB/DiceMaster_Router/Components.pretty/`.

Similarly, custom schematic symbols are in `PCB/DiceMaster_Router/Components/` and referenced in `sym-lib-table`. Verify under **Schematic Editor → Preferences → Manage Symbol Libraries**.

### 4. Configure the kicad-mcp Server (Claude Code Integration)

The `.mcp.json` file in `DiceMaster_HW/` configures the kicad-mcp server for Claude Code. This lets Claude inspect schematics and PCB files interactively.

**Install the server:**

```bash
git clone https://github.com/lamaalrajih/kicad-mcp.git ~/Applications/kicad-mcp
cd ~/Applications/kicad-mcp
make install
cp .env.example .env
```

**Edit `~/Applications/kicad-mcp/.env`** to set the search path:

```
KICAD_SEARCH_PATHS=~/Code/DiceMaster/DiceMaster_HW
```

**Start Claude Code from the project directory** so `.mcp.json` is picked up automatically:

```bash
cd DiceMaster_HW
claude
```

Claude can then list projects, open KiCad, inspect schematics, analyze PCB layout, and search the footprint library.

### 5. Run DRC Before Exporting

Always run the Design Rules Checker before exporting Gerbers or sharing the board file:

1. Open the **PCB Editor**.
2. Go to **Inspect → Design Rules Checker**.
3. Click **Run DRC**.
4. Resolve all **Errors** (warnings may be acceptable, but document any intentional rule exceptions).

Common DRC items to watch for in this design:
- Clearance violations near the TPS565201 SOT-23-6 pads (dense layout).
- Courtyard overlaps if components are placed tightly.
- Unconnected nets (indicates a ratsnest was missed during routing).

### 6. Export Gerbers

After DRC passes, see the [PCB Assembly runbook](pcb-assembly.md) for the full Gerber export and ordering workflow.

## Verify

- KiCad project opens without missing library warnings.
- Schematic loads all symbols without "symbol not found" errors.
- PCB layout shows all footprints placed and all ratsnest connections routed.
- DRC reports zero errors.

## Troubleshooting

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| "Symbol not found" in schematic | `sym-lib-table` path broken after repo move | Re-add `Components/` under **Manage Symbol Libraries → Project Specific** |
| "Footprint not found" in PCB editor | `fp-lib-table` path broken | Re-add `Components.pretty/` under **Manage Footprint Libraries → Project Specific** |
| kicad-mcp server not connecting | `.env` path wrong, or `uv` not installed | Verify `KICAD_SEARCH_PATHS` in `.env`; run `which uv` to confirm `uv` is on PATH |
| DRC reports clearance errors | Component placement too tight | Use the interactive router's push-and-shove mode to reroute; adjust board outline if space allows |
| KiCad version mismatch warning on open | File was saved with a newer KiCad version | Upgrade KiCad to match or newer; older versions may silently drop features |
