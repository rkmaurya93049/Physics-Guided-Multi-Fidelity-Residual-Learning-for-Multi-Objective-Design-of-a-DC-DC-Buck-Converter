# File Guide

This is the artifact map for the current public repository. Original research files are preserved where they carry provenance value; curated documentation/figures are separated from raw evidence.

## Manuscript

- `manuscript/publication_report.pdf` — current 4-page scientific manuscript.

Do not add multiple outdated manuscript drafts to the public research path.

## Canonical notebooks

| File | Role |
|---|---|
| `notebooks/analytical_model.ipynb` | analytical buck equations, Latin-hypercube sampling, 70,000-row dataset generation |
| `notebooks/spice_automation.ipynb` | PyLTSpice matched-coordinate sweep, reference-circuit editing and log parsing |
| `notebooks/ml_training.ipynb` | RF/XGBoost/MLP training, analytical→LTspice transfer, target-wise residual correction, hold-out/CV evaluation, broad and constrained NSGA-II, balanced-candidate selection |

There is **no separate optimization notebook required**: the final optimization pipeline is already contained in `ml_training.ipynb`.

The notebooks are historical research records and may contain iterative/development cells. Do not silently rewrite their numerical history merely for appearance.

## Data

### `data/analytical/`

- `analytical_dataset.csv` — 70,000 analytical design points and analytical targets.

### `data/ltspice/`

- `spice_dataset.csv` — retained switching-level dataset used downstream.
- `spice_vs_analytical_comparison.csv` — matched analytical/LTspice comparison with derived error/feature columns.

A separate raw pre-filter 300-run CSV is **not currently archived**; do not claim otherwise unless one is later added.

### `data/pareto/`

- `pareto_front_constrained.csv` — final 100 surrogate non-dominated candidates from the safeguarded bounded search.

## LTspice

### `ltspice/reference/`

- `Draft1.asc` — reference/common buck schematic used by the automation workflow.

### `ltspice/automation/`

This folder documents the automation role. The canonical automation notebook is `notebooks/spice_automation.ipynb`; keeping a second byte-identical notebook here is unnecessary.

### `ltspice/validation/`

- `Draft2.asc` — balanced selected design/manual engineering check.
- `Draft3_minRipple.asc` — minimum-ripple representative design.
- `Draft4_maxEfficiency.asc` — maximum **surrogate-predicted** efficiency representative design.

The filenames are historical. Interpret candidate labels using the manuscript and this guide rather than assuming that “maxEfficiency” means measured LTspice maximum efficiency.

### `ltspice/logs/`

- `Draft1.log`
- `Draft2.log`
- `Draft3_minRipple.log`
- `Draft4_maxEfficiency.log`

These logs preserve direct manual LTspice measurements. They include later 9–10 ms engineering checks; the manuscript's main representative validation table remains the common original 4–5 ms protocol. Do not mix the two purposes.

## Figures

### `figures/main/`

Reviewer/professor-facing curated figures:

- `workflow.svg`
- `ltspice_reference.svg`
- `residual_correction.svg`
- `pareto_front.svg`

### `figures/archive/raw_uploads/`

All originally uploaded screenshots are retained here as raw visual provenance. These include development plots, switching waveforms, startup plots and redundant/intermediate screenshots. They are not all publication figures.

## Documentation

- `docs/METHODOLOGY.md` — implemented scientific method, assumptions and limitations.
- `docs/REPRODUCIBILITY.md` — reproduction sequence and source-to-result checks.
- `docs/FILE_GUIDE.md` — this file.
- `docs/REPOSITORY_STATUS.md` — completeness matrix and known archival limitations.

## Provenance rules

1. Never modify historical CSV values or LTspice logs to make them agree with a narrative.
2. Keep the original 4–5 ms validation protocol distinct from later 9–10 ms engineering characterization.
3. Refer to final optimizer outputs as **surrogate non-dominated candidates** until independently simulated/measured.
4. Treat LTspice as a switching-level simulation reference, not hardware ground truth.
5. Preserve raw evidence in clearly marked archives rather than cluttering the main reviewer-facing path.
6. Do not upload credentials, identity documents or unrelated private files.
