# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

ACRP-Lab is a research tool for aircraft tire contact stress analysis and airfield pavement response modeling. It calculates 3D contact stress distributions beneath aircraft tires using the Hernandez & Al-Qadi (2015) methodology, processes aircraft data from XML databases, and exports results for finite element analysis (Abaqus).

## Technology Stack

- **Python 3.12** with **Jupyter Notebooks** as the primary execution environment
- Core libraries: pandas, numpy, matplotlib, seaborn, openpyxl, xlsxwriter
- No package manager config (no requirements.txt/pyproject.toml) — dependencies are implicit

## Running the Project

All computation is notebook-based. Execute cells sequentially in each notebook:

1. **`1. Pre-Processing.ipynb`** — Parses `input_data/aircraft.xml` (FAAR database) → produces `input_data/aircraft.xlsx` (413+ aircraft records)
2. **`2. Distribution.ipynb`** — Reads `aircraft.xlsx` → generates statistical visualizations in `figures_aircraft_tires/`
3. **`3. Aircraft_CS.ipynb`** — Reads single aircraft from `aircraft.xlsx` → calculates 3D stress distributions → exports to `output/` as Excel

Input parameters (aircraft name, tire pressure, load) are defined as variables at the top of each notebook.

## Architecture

### Three-Stage Pipeline

**Pre-Processing:** XML parsing with namespace handling → tire assignment via 30+ pattern-matching rules → rib profile classification (3/5/7-rib) with heuristic fallback → unit conversions (lbs↔N, psi↔MPa, in↔mm)

**Distribution Analysis:** Flexible column detection with fallback names → derived metrics (aspect ratio, rectangularity, contact stress) → 10+ plot types in dual units (imperial & metric) → class-stratified coloring via viridis colormap

**Stress Calculation:** Three stress components computed per element:
- **SSZ (vertical):** Power law distribution per rib, parameter `n` from equilibrium
- **SSX (longitudinal):** Asymmetric rational quadratic with front/rear segments
- **SSY (transverse):** Proportional to vertical via `ssy_factor`

Results are discretized (10 divisions along contact length, 2-4 partitions per rib) and averaged at cell centers for FEM compatibility.

### Validation

No formal test suite. Validation is via equilibrium checks in notebook 3: calculated load vs. applied load must be within 2.5% tolerance.

### Output Format

Output files follow naming: `ICT-{AircraftName}_P{LoadLabel}_{TirePressure}.xlsx`

## Key Code Patterns

- XML namespace handling with helper functions (`get_scalar()`, `get_us()`)
- Regex-based aircraft model matching with normalized names
- `DataFrame.apply()` with lambdas for vectorized row operations
- Every visualization generated twice (imperial & metric units)
- `GridSpec` for complex layouts; 3D surface plots with heatmap overlays
