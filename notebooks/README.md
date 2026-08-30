# Notebooks

These are the canonical historical notebooks for the research workflow.

| Notebook | Role |
|---|---|
| `analytical_model.ipynb` | analytical equations, LHS sampling and 70,000-point dataset generation |
| `spice_automation.ipynb` | PyLTSpice matched-coordinate automation and measurement parsing |
| `ml_training.ipynb` | RF/XGBoost/MLP, cross-fidelity evaluation, target-wise residual correction, validation, NSGA-II and balanced-candidate selection |

`ml_training.ipynb` already contains the final optimization pipeline, so a separate optimization notebook is not required.

The files preserve the research-development history and local relative paths. For a clean rerun, follow `../docs/REPRODUCIBILITY.md` rather than editing outputs or silently changing the implemented method.
