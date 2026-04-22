# ADR-001: KiCad as PCB Design Tool

## Status

Accepted

## Context

DiceMaster requires custom PCB design for the Router board. The design files must be:

- Stored in Git alongside the rest of the project source.
- Reproducible on any developer machine without a paid license or cloud dependency.
- Compatible with standard PCB fabrication services (JLCPCB, PCBWay) that accept Gerber outputs.

The team is a small hardware/software group on macOS and Linux. Tooling cost and vendor lock-in are primary concerns.

## Decision

Use **KiCad 9.0+** as the sole PCB design tool.

- The project file is `PCB/DiceMaster_Router/DiceMaster_Router.kicad_pro`.
- Schematics use `.kicad_sch` format; board layout uses `.kicad_pcb`.
- Custom symbols and footprints specific to this project (TPS565201, BMI160 breakout) live in `PCB/DiceMaster_Router/Components/` and `Components.pretty/` and are checked into Git.
- Gerber outputs are committed to `PCB/DiceMaster_Router/gerbers/` for traceability.
- The project is configured with a [kicad-mcp](https://github.com/lamaalrajih/kicad-mcp) server so Claude Code can inspect schematics and PCB files directly.

## Consequences

**Positive:**

- KiCad is free and open-source (GPL). No license cost.
- All file formats (`.kicad_sch`, `.kicad_pcb`, `.kicad_pro`) are plain text (S-expression). Git diffs are human-readable and merge conflicts are resolvable.
- Ships with a large built-in symbol and footprint library covering most standard passive and active components.
- Cross-platform: runs on macOS, Linux, and Windows.
- Gerber export is well-supported and accepted by all major PCB fabs.

**Negative:**

- Steeper initial learning curve than Eagle's legacy UI or EasyEDA's guided workflow.
- KiCad's scripting API (Python) is powerful but sparsely documented compared to commercial tools.
- Version upgrades (e.g., KiCad 8 → 9) occasionally change file formats, requiring migration.

## Alternatives Considered

**Eagle (Autodesk)**
Eagle was the de-facto hobbyist standard before Autodesk acquisition. It is now bundled with Fusion 360 under a subscription model. File format is proprietary XML; while text-based, diffs are noisy and imports into other tools are lossy. Rejected due to cost and vendor lock-in.

**EasyEDA (LCSC/JLCPCB)**
Browser-based tool tightly integrated with JLCPCB's component catalog. Convenient for ordering, but files live in the cloud, offline access is limited, and version control requires manual JSON export. Rejected because reproducibility and Git integration are first-class requirements for this project.
