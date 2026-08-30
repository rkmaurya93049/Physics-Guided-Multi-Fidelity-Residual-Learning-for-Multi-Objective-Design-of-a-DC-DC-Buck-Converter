# Methodology

This document summarizes the implemented research workflow so that the repository can be understood independently of the manuscript layout.

## 1. Problem setup

The study considers a nominal fixed-duty **12 V-to-5 V buck converter**. The 5 V value is the analytical target; switching-level LTspice simulations use a fixed open-loop duty ratio rather than closed-loop output regulation.

## 2. Low-fidelity analytical model

The analytical stage samples five primary design variables:

- inductance `L`,
- capacitance `C`,
- switching frequency `fsw`,
- load-current setting `Iload`, and
- MOSFET on-resistance `RDS(on)`.

The analytical implementation evaluates continuous-conduction-mode quantities including inductor ripple, RMS current, output ripple, loss, and efficiency. The original analytical implementation uses 10 mΩ ESR, 10 mΩ DCR, and 20 ns rise/fall switching intervals.

Latin-hypercube sampling with seed 42 generates **70,000** analytical design points.

## 3. Switching-level LTspice reference

The first **300 matched analytical design coordinates** are also evaluated with automated LTspice simulations. The LTspice model is a higher-detail switching reference relative to the analytical model, not hardware ground truth.

The implemented reference includes:

- voltage-controlled switch with parameterized on-resistance,
- simplified diode,
- 1 mΩ inductor series resistance,
- no explicit capacitor ESR,
- 1 ns PWM edges,
- 5 ms transient simulation,
- original measurement window from 4 ms to 5 ms.

The automation retains rows satisfying the implemented sanity conditions, including `eta <= 1` and `Vout_max < 5.5 V`. This leaves **285 switching-level rows**. These are numerical/operating sanity filters, not an explicit rule that removes a fixed number of discontinuous-conduction-mode cases.

## 4. Physics-guided features

Three derived features augment the five primary design variables:

- load-related quality-factor feature,
- LC resonance-frequency feature,
- switching-frequency / LC-frequency ratio.

These features provide physics-linked structure without using a PINN-style governing-equation loss. For that reason the work is described as **physics-guided**, not as a physics-informed neural network.

## 5. Analytical-domain surrogate models

The analytical dataset is split 80:20 using `random_state=42`.

Models used in the implemented workflow include:

- Random Forest: 100 trees,
- XGBoost: 200 estimators, maximum depth 6, learning rate 0.1,
- scikit-learn MLP: hidden layers `(64, 32)`, `max_iter=500`, standardized inputs and targets.

The important finding is that strong analytical-domain accuracy does **not** imply accurate transfer to LTspice.

## 6. Cross-fidelity residual correction

XGBoost supplies the analytical base prediction. For each target, the discrepancy is defined as:

```text
residual = LTspice target - analytical-surrogate prediction
```

Separate Gradient Boosting residual models are trained target-wise using:

- 50 estimators,
- maximum depth 2,
- learning rate 0.05,
- subsample 0.8,
- `random_state=42`.

A 75:25 LTspice hold-out split and shuffled five-fold cross-validation are used for validation. The final residual models are refit on all 285 retained LTspice rows before optimization.

Stored five-fold headline results include approximately:

- output ripple: R² ≈ 0.724,
- total loss: R² ≈ 0.964,
- efficiency: MAE ≈ 0.38 percentage points.

## 7. Multi-objective optimization

NSGA-II minimizes the three-objective vector:

```text
[-efficiency, output ripple, total loss]
```

The final optimization uses a nominal 2 A load setting, corresponding to a 2.5 Ω resistive load at the 5 V analytical target.

Implemented NSGA-II settings include:

- population size 100,
- 100 generations,
- simulated-binary crossover probability 0.9,
- crossover distribution index 15,
- polynomial mutation distribution index 20,
- seed 42.

## 8. Extrapolation failure and safeguard

An initial broader optimization exposed non-physical/extrapolative surrogate behavior, including a negative output-ripple prediction. Rather than hiding this failure, the final workflow treats it as a reliability diagnostic.

The final rerun uses the empirical per-variable extrema of the retained LTspice dataset:

- `L`: approximately 2.17–46.6 µH,
- `C`: approximately 10.1–468 µF,
- `fsw`: approximately 102.201–999.719 kHz,
- `RDS(on)`: approximately 5.1–49.9 mΩ.

The objective code also applies an explicit non-negative ripple safeguard:

```text
ripple_objective = max(raw_corrected_ripple, 1e-6 V)
```

Therefore, non-negative ripple in the final optimization is partly enforced rather than automatically learned.

## 9. Candidate set and selection

The safeguarded final run produces **100 surrogate non-dominated candidates**.

A balanced candidate is chosen by min–max normalizing efficiency, ripple, and loss and minimizing equal-weight Euclidean distance to the ideal vector `[maximum efficiency, zero ripple, zero loss]`.

The selected design is approximately:

- `L = 29.418 µH`,
- `C = 158.710 µF`,
- `fsw = 103.160 kHz`,
- `RDS(on) = 5.103 mΩ`.

## 10. Representative LTspice validation

Three representative candidates are checked using the original 4–5 ms LTspice protocol:

- minimum-ripple candidate,
- maximum **surrogate-predicted** efficiency candidate,
- balanced selected candidate.

The label “maximum surrogate-predicted efficiency” is intentional: LTspice re-evaluation does not preserve that exact ranking in all comparisons.

Reported original-protocol errors across the three representatives are:

- efficiency: 0.38–0.84 percentage points,
- output ripple: 0.92–2.43 mV.

## 11. Averaging-window sensitivity

A manual reconstruction of the maximum surrogate-predicted-efficiency design reproduces the original 4–5 ms result at approximately **96.1438% efficiency** and **58.769 mV ripple**. The same circuit evaluated over 9–10 ms gives approximately **95.9074% efficiency** and **58.749 mV ripple**.

This is retained as a sensitivity observation. The original 4–5 ms protocol remains the common basis for the three-point validation table.

## 12. Main limitations

The main limitations are:

1. no physical hardware prototype has been tested,
2. the LTspice model uses simplified switching components,
3. analytical and LTspice parasitic assumptions differ,
4. the empirical per-variable box does not guarantee dense joint-domain coverage,
5. non-negative ripple is partly enforced by an explicit 1 µV floor,
6. efficiency and total loss are learned by separate residual models rather than constrained to exact power balance,
7. only three final candidates are checked under the original LTspice protocol,
8. the optimization uses a nominal 2 A load setting, and
9. the original Python environment was not version-locked.

These limitations should remain visible in any future manuscript, preprint, or repository release.
