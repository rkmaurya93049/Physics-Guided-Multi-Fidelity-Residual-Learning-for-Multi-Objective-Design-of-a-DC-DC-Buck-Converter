# Reproducibility Guide

This guide maps the research artifacts to the reported workflow. It is written for a reader who wants to verify the paper rather than merely view figures.

## Reproduction order

### Step 1 — Analytical data

Use the analytical-data notebook to generate or load the 70,000-point Latin-hypercube dataset.

Verify:

- five primary design variables are present,
- derived physics-guided features are generated consistently,
- analytical targets include efficiency, output ripple, and total loss,
- the analytical train/test split uses 80:20 with seed/random state 42 where applicable.

Expected location:

```text
notebooks/
data/analytical/
```

### Step 2 — LTspice matched-coordinate dataset

Use the LTspice automation notebook and common reference circuit/template to evaluate the first 300 analytical coordinates.

Verify:

- duty ratio is fixed at 5/12,
- `Rload = 5/Iload`,
- simulation duration is 5 ms,
- original measurement window is 4–5 ms,
- filtering leaves 285 retained rows.

Expected locations:

```text
notebooks/
ltspice/automation/
data/ltspice/
```

### Step 3 — Analytical surrogate training

Train/evaluate the Random Forest, XGBoost, and MLP analytical-domain models using the implementation settings documented in `METHODOLOGY.md`.

The purpose of this stage is not only to obtain high analytical-domain accuracy, but also to establish the base prediction later tested against switching-level labels.

### Step 4 — Cross-fidelity transfer

Evaluate the analytical XGBoost base model directly on the matched LTspice coordinates/targets.

The reported direct-transfer R² values are approximately:

| Target | XGBoost → LTspice R² |
|---|---:|
| Efficiency | -2.479 |
| Output ripple | -0.261 |
| Total loss | 0.631 |

These poor transfer results motivate the residual-learning stage.

### Step 5 — Residual correction

Construct target-wise residuals:

```text
r = y_LTspice - yhat_XGB
```

Train the separate Gradient Boosting residual models and evaluate both the 75:25 hold-out split and shuffled five-fold cross-validation.

Headline stored results include:

| Target | Corrected hold-out R² | Stored five-fold R² |
|---|---:|---:|
| Efficiency | 0.301 | 0.127 ± 0.093 |
| Output ripple | 0.728 | 0.724 ± 0.095 |
| Total loss | 0.956 | 0.964 ± 0.008 |

Efficiency five-fold MAE is approximately 0.38 percentage points and is more informative than R² for the narrow efficiency range.

After validation, refit the final residual models on all 285 retained LTspice rows before optimization.

### Step 6 — Broad NSGA-II stress test

Run the initial broad optimization and inspect the resulting objective values.

The important reproducibility point is the failure itself: optimizer search can exploit surrogate extrapolation and produce a physically impossible negative corrected-ripple estimate or very large validation error at an extreme.

Do not remove this evidence from the research history.

### Step 7 — Safeguarded final NSGA-II search

Rerun optimization using the empirical per-variable extrema of the retained LTspice dataset and the explicit non-negative ripple objective floor.

Expected approximate bounds:

```text
L        2.17–46.6 µH
C        10.1–468 µF
fsw      102.201–999.719 kHz
RDS(on)  5.1–49.9 mΩ
```

Explicit ripple objective safeguard:

```text
max(raw_corrected_ripple, 1e-6 V)
```

Expected final stored candidate count:

```text
100 surrogate non-dominated candidates
```

Expected location:

```text
data/pareto/
notebooks/
```

### Step 8 — Balanced candidate selection

From the final candidate CSV:

1. min–max normalize efficiency, ripple, and total loss,
2. define the ideal vector as `[maximum efficiency, zero ripple, zero loss]`,
3. compute equal-weight Euclidean distance,
4. select the minimum-distance candidate.

Expected selected design (rounded):

```text
L        29.418 µH
C        158.710 µF
fsw      103.160 kHz
RDS(on)  5.103 mΩ
```

### Step 9 — Representative original-protocol LTspice checks

Re-evaluate three representative designs using the original 4–5 ms protocol:

| Representative | Predicted η / LTspice η | Ripple objective / LTspice ripple |
|---|---:|---:|
| Minimum ripple | 96.51 / 96.13 % | 0.001 / 0.92 mV |
| Maximum surrogate-predicted η | 96.98 / 96.14 % | 61.21 / 58.78 mV |
| Balanced selected | 96.94 / 96.27 % | 17.08 / 14.65 mV |

The 0.001 mV minimum-ripple objective is the explicit 1 µV floor, not necessarily the raw unconstrained residual-model prediction.

### Step 10 — Manual source-level reconstruction

For the rounded maximum surrogate-predicted-efficiency design, reproduce the original 4–5 ms measurement:

```text
Efficiency ≈ 96.1438%
Ripple     ≈ 58.769 mV
```

The same rounded circuit over 9–10 ms gives approximately:

```text
Efficiency ≈ 95.9074%
Ripple     ≈ 58.749 mV
```

This difference is documented as averaging-window sensitivity. Do not mix the alternate window into the original three-candidate validation table.

## File provenance expectations

A strong archival release should make it possible to trace each manuscript claim to one or more of:

- a notebook cell,
- a CSV row/table,
- an LTspice source netlist/schematic,
- an LTspice log,
- a generated figure,
- a deterministic selection calculation.

Use `docs/FILE_GUIDE.md` to record the exact filenames after the source files are uploaded.

## Environment reproducibility

The historical environment was not pinned. Before a DOI release:

```bash
python --version
pip freeze > requirements-lock.txt
```

Record the LTspice version as well. Cross-version changes can cause small metric differences, so exact version metadata should be preserved in the archival release.

## What counts as successful reproduction?

The goal is not bit-identical floating-point output across every platform. A successful reproduction should recover:

- the 70,000 analytical-row scale,
- the 300 generated / 285 retained LTspice workflow,
- the poor direct analytical-to-LTspice transfer,
- substantial residual-correction improvement,
- the extrapolation failure of the broad optimizer,
- the corrected 10.1 µF final capacitance lower bound,
- the 1 µV ripple safeguard,
- the 100-candidate final stored front,
- the selected balanced design to rounding precision, and
- the representative LTspice validation values to the precision reported in the manuscript.
