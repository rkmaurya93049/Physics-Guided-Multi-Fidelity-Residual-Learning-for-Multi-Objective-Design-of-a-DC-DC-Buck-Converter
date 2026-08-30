# Repository Status

This file records what is currently available in the public research repository and what is **not** claimed to be archived.

## Present core artifacts

| Research stage | Public artifact | Status |
|---|---|---|
| Analytical model and LHS generation | `notebooks/analytical_model.ipynb` | present |
| 70,000-row analytical dataset | `data/analytical/analytical_dataset.csv` | present |
| LTspice sweep automation | `notebooks/spice_automation.ipynb` | present |
| Common LTspice reference schematic | `ltspice/reference/Draft1.asc` | present |
| Retained switching-level dataset | `data/ltspice/spice_dataset.csv` | present |
| Matched analytical/LTspice comparison | `data/ltspice/spice_vs_analytical_comparison.csv` | present |
| Base ML, residual correction, CV and optimization | `notebooks/ml_training.ipynb` | present |
| Final 100-candidate constrained Pareto CSV | `data/pareto/pareto_front_constrained.csv` | present |
| Balanced candidate schematic | `ltspice/validation/Draft2.asc` | present |
| Minimum-ripple schematic | `ltspice/validation/Draft3_minRipple.asc` | present |
| Maximum surrogate-predicted-efficiency schematic | `ltspice/validation/Draft4_maxEfficiency.asc` | present |
| Manual LTspice logs | `ltspice/logs/` | present |
| Final manuscript | `manuscript/publication_report.pdf` | present |
| Curated scientific figures | `figures/main/` | present |
| Original screenshot archive | `figures/archive/raw_uploads/` | preserved |

## Known archival limitations

### 1. Historical Python environment was not version-locked

The dependency families are known, but the original project did not save exact package versions at the time every result was produced. `requirements.txt` therefore avoids inventing versions. If the original working environment is still available, export it before the first DOI-tagged release.

### 2. Raw pre-filter 300-run CSV is not currently archived as a separate file

The public repository contains the retained switching-level dataset used in downstream residual learning and the matched analytical/LTspice comparison. The paper's workflow records 300 attempted/generated matched simulations and 285 retained rows, but this repository should not claim that a separate raw 300-row pre-filter CSV is public unless such a file is added.

### 3. Historical notebooks use local relative paths

The notebooks are preserved as research records and contain paths such as `analytical_dataset.csv` or `Draft1.asc` that reflected the original working directory. The public repository uses a cleaner folder structure. Follow `docs/REPRODUCIBILITY.md` to create a local rerun workspace rather than interpreting folder separation as a change in methodology.

### 4. LTspice evidence spans two measurement purposes

The manuscript's representative surrogate-validation table uses the common original 4–5 ms protocol. Later manual engineering checks also use 9–10 ms windows. These should not be mixed. The maximum surrogate-predicted-efficiency log contains direct window-sensitivity evidence; later manual logs for other circuits are preserved as supplemental engineering evidence.

### 5. Hardware validation is not included

All validation in the current study is simulation-level. No physical prototype measurements are claimed.

## Release rule

Before creating a Zenodo DOI or calling a tagged release archival-quality, re-check:

- manuscript status and venue/preprint policy,
- exact environment metadata if recoverable,
- LTspice version,
- license choice,
- `CITATION.cff` DOI/arXiv fields if applicable,
- that all public links in the manuscript still resolve.
