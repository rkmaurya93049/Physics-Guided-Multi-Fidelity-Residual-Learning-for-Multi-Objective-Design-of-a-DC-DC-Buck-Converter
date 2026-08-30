# Reproducibility Guide

This guide maps the public artifacts to the reported workflow. The goal is to verify the scientific chain without rewriting the historical notebooks or silently reconciling different LTspice measurement windows.

## Before running notebooks

The original notebooks were developed with local relative paths such as `analytical_dataset.csv` and `Draft1.asc`. The public repository deliberately separates data, code and LTspice sources into cleaner folders.

For a fresh rerun, create a **temporary working directory** (outside the tracked source folders), copy the notebook you want to execute into it, and copy the dependencies listed below into that same working directory. This preserves the original notebook logic while avoiding edits to source evidence.

| Notebook | Copy alongside it for historical relative paths |
|---|---|
| `notebooks/analytical_model.ipynb` | no input dataset required for generation |
| `notebooks/spice_automation.ipynb` | `data/analytical/analytical_dataset.csv`, `ltspice/reference/Draft1.asc` |
| `notebooks/ml_training.ipynb` | `data/analytical/analytical_dataset.csv`, `data/ltspice/spice_dataset.csv`, `data/ltspice/spice_vs_analytical_comparison.csv` as required by the executed cells |

The final Pareto CSV is already stored at `data/pareto/pareto_front_constrained.csv` for source verification.

## Step 1 — Analytical model and 70,000-point dataset

Source: `notebooks/analytical_model.ipynb`.

Verify:

- five sampled variables: L, C, switching frequency, load-current setting and switch on-resistance,
- ranges approximately L 1–47 µH, C 10–470 µF, fsw 100 kHz–1 MHz, Iload 0.1–5 A, RDS(on) 5–50 mΩ,
- Latin-hypercube sampling with seed 42,
- 70,000 generated points,
- fixed analytical DCR=10 mΩ and ESR=10 mΩ in the stored generation workflow,
- analytical targets include efficiency, output ripple and total loss.

Stored output: `data/analytical/analytical_dataset.csv`.

## Step 2 — Matched switching-level LTspice sweep

Sources:

- `notebooks/spice_automation.ipynb`
- `ltspice/reference/Draft1.asc`

Verify from the automation code/reference circuit:

- the LTspice sweep evaluates the first 300 analytical design coordinates,
- nominal duty is fixed at 5/12,
- the nominal load implementation uses `Rload = 5/Iload`,
- transient duration is 5 ms,
- the original measurement window is 4–5 ms,
- switch-model `Ron` is patched to the sampled RDS(on),
- downstream sanity filtering retains 285 rows.

Stored public data:

- `data/ltspice/spice_dataset.csv`
- `data/ltspice/spice_vs_analytical_comparison.csv`

A separate raw pre-filter 300-run CSV is not currently public. The repository therefore does **not** claim that such a raw file is archived.

## Step 3 — Analytical-domain ML models

Source: `notebooks/ml_training.ipynb`.

Verify:

- analytical train/test split: 80/20, random state 42,
- engineered features: Q-related feature, LC resonant frequency and fsw/fLC ratio,
- Random Forest: 100 trees,
- XGBoost: 200 estimators, depth 6, learning rate 0.1,
- MLP: hidden layers (64, 32), max_iter 500, standardized inputs/targets.

Representative analytical-domain R² values stored in the notebook include:

- RF ripple ≈ 0.752,
- XGBoost ripple ≈ 0.901,
- MLP ripple ≈ 0.981.

## Step 4 — Cross-fidelity transfer at matched coordinates

Evaluate the analytical-trained XGBoost model against the switching-level rows at the matched design coordinates.

Reported direct-transfer R²:

| Target | XGBoost → LTspice R² |
|---|---:|
| Efficiency | -2.479 |
| Output ripple | -0.261 |
| Total loss | 0.631 |

This is a **cross-fidelity transfer test at matched design coordinates**, not an unseen-input-location experiment.

## Step 5 — Target-wise residual correction

Residual definition:

```text
r = y_LTspice - yhat_XGB
```

For each target, the notebook trains a Gradient Boosting residual model with:

```text
n_estimators = 50
max_depth = 2
learning_rate = 0.05
subsample = 0.8
random_state = 42
```

Validation includes a 75/25 switching-level hold-out split and shuffled five-fold CV.

| Target | Corrected hold-out R² | Five-fold mean R² |
|---|---:|---:|
| Efficiency | 0.301 | 0.127 ± 0.093 |
| Output ripple | 0.729 | 0.724 ± 0.095 |
| Total loss | 0.956 | 0.964 ± 0.008 |

Efficiency five-fold MAE is approximately **0.38 percentage points**.

The notebook also records that deriving efficiency from the corrected loss model performed poorly (R² ≈ -1.864, MAE ≈ 0.008397), which motivates retaining target-wise outputs while acknowledging power-balance consistency as a limitation.

After validation, the final residual models are refit on all 285 retained switching-level rows for optimization.

## Step 6 — Broad optimizer stress test

The initial broad NSGA-II search is part of the research result because it exposes surrogate extrapolation: the optimizer can exploit a region with a physically impossible negative corrected-ripple prediction or produce a large error at an extreme.

Do not delete this history merely to make the optimization look cleaner.

## Step 7 — Safeguarded final NSGA-II

The final problem uses the empirical per-variable extrema of the 285 retained LTspice rows:

```text
L        ≈ 2.17–46.6 µH
C        ≈ 10.1–468 µF
fsw      ≈ 102.201–999.719 kHz
RDS(on)  ≈ 5.1–49.9 mΩ
```

Explicit ripple-objective safeguard:

```text
max(raw_corrected_ripple, 1e-6 V)
```

NSGA-II settings:

```text
population = 100
generations = 100
SBX probability = 0.9, eta = 15
polynomial mutation eta = 20
seed = 42
```

Stored final output: `data/pareto/pareto_front_constrained.csv`, containing 100 **surrogate non-dominated candidates**.

## Step 8 — Balanced candidate selection

From the final candidate set:

1. min–max normalize efficiency, ripple and total loss,
2. define the ideal as maximum efficiency / zero ripple / zero loss,
3. use equal-weight Euclidean distance,
4. choose the minimum-distance candidate.

Expected rounded selected design:

```text
L        29.418 µH
C        158.710 µF
fsw      103.160 kHz
RDS(on)  5.103 mΩ
```

## Step 9 — Representative original-protocol validation

The manuscript reports the following three representative checks under the common original **4–5 ms** protocol:

| Representative | Predicted η / LTspice η | Ripple objective / LTspice ripple |
|---|---:|---:|
| Minimum ripple | 96.51 / 96.13 % | 0.001 / 0.92 mV |
| Maximum surrogate-predicted η | 96.98 / 96.14 % | 61.21 / 58.78 mV |
| Balanced selected | 96.94 / 96.27 % | 17.08 / 14.65 mV |

The 0.001 mV minimum-ripple value is the explicit 1 µV objective floor, not necessarily the raw corrected prediction.

Representative source schematics are under `ltspice/validation/`. The preserved manual logs under `ltspice/logs/` also contain later engineering checks; do not substitute those alternate windows into the common validation table.

## Step 10 — Averaging-window sensitivity

For the rounded maximum surrogate-predicted-efficiency design, the preserved source-level measurements give approximately:

```text
4–5 ms:  efficiency = 96.1438%, ripple = 58.769 mV
9–10 ms: efficiency = 95.9074%, ripple = 58.749 mV
```

The near-unchanged ripple and shifted efficiency are documented as averaging-window sensitivity. The balanced design also exceeds the maximum surrogate-predicted-efficiency candidate in the later manual comparison, reinforcing that a surrogate ranking is not guaranteed to be the LTspice ranking when predicted efficiencies are very close.

## Curated vs raw visual evidence

- `figures/main/` contains only four curated figures used to communicate the final scientific story.
- `figures/archive/raw_uploads/` preserves all uploaded screenshots and development waveforms.

For numerical verification, prefer CSV/notebook/LTspice-log evidence over reading values from screenshots.

## Environment reproducibility

The historical environment was not pinned. If the original environment is still available, capture before DOI archival:

```bash
python --version
pip freeze > requirements-lock.txt
```

Also record the LTspice version. Until then, `requirements.txt` intentionally lists dependency families without fabricated versions.

## Successful reproduction criteria

A successful reproduction should recover the substantive scientific chain rather than require bit-identical floating-point output across platforms:

- 70,000 analytical rows,
- 300 matched LTspice attempts/generated cases and 285 retained rows,
- poor direct cross-fidelity transfer,
- substantial residual-correction improvement,
- optimizer extrapolation failure in the broad search,
- corrected final C lower bound of 10.1 µF,
- explicit 1 µV ripple floor,
- 100-candidate final stored front,
- balanced selected design to rounding precision,
- representative LTspice values to the precision reported in the manuscript.
