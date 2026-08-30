# Data

The public data is separated by research fidelity/stage:

- `analytical/analytical_dataset.csv` — 70,000 low-fidelity analytical design points.
- `ltspice/spice_dataset.csv` — retained switching-level dataset used downstream.
- `ltspice/spice_vs_analytical_comparison.csv` — matched analytical/LTspice comparison.
- `pareto/pareto_front_constrained.csv` — final 100 surrogate non-dominated candidates.

A separate raw pre-filter 300-run CSV is not currently archived. See `../docs/REPOSITORY_STATUS.md` for provenance limitations and `../docs/REPRODUCIBILITY.md` for the workflow.
