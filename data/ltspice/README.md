# LTspice-Derived Data

- `spice_dataset.csv` — retained switching-level dataset used for downstream cross-fidelity/residual learning.
- `spice_vs_analytical_comparison.csv` — matched switching-level and analytical values with derived comparison/error columns.

The implemented workflow generated/evaluated 300 matched design coordinates and retained 285 rows after numerical/operating sanity filtering. A separate raw pre-filter 300-run CSV is not currently archived, so this directory does not claim to contain one.

See `../../notebooks/spice_automation.ipynb` and `../../docs/REPRODUCIBILITY.md`.
