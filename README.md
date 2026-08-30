# Physics-Guided Multi-Fidelity Residual Learning for Multi-Objective Design of a DC-DC Buck Converter

**Author:** Raushan Kumar  
**Affiliation:** Department of Electrical and Electronics Engineering, Lok Nayak Jai Prakash Institute of Technology (Bihar Engineering University), Chapra, Bihar, India  
**Status:** Independent research project; manuscript prepared for peer-reviewed submission.

## Research overview

This repository supports a simulation-level study of a nominal fixed-duty **12 V-to-5 V DC-DC buck converter** using a physics-guided multi-fidelity workflow. The project combines low-cost analytical converter modelling, automated switching-level LTspice simulations, machine-learning residual correction, and NSGA-II multi-objective optimization.

The core question is whether a large low-fidelity analytical design space can be corrected using a much smaller set of matched switching-level simulations before optimization, while explicitly detecting and managing optimizer-driven surrogate extrapolation.

## Headline scale

- **70,000** Latin-hypercube analytical design points
- **300** matched automated LTspice runs
- **285** retained switching-level simulations after numerical/operating sanity filtering
- Random Forest, XGBoost, and multilayer-perceptron analytical surrogates
- Target-wise Gradient Boosting residual correction
- **100** surrogate non-dominated candidates from the final safeguarded NSGA-II search
- **3** representative final candidates re-evaluated with the original LTspice validation protocol

## Workflow

```text
Analytical model
      ↓
70,000 low-fidelity design points
      ↓
Matched LTspice reference simulations
300 generated → 285 retained
      ↓
Analytical ML surrogate
      ↓
Target-wise residual correction
      ↓
Cross-fidelity validation
      ↓
NSGA-II extrapolation stress test
      ↓
Empirical LTspice search envelope + 1 µV ripple safeguard
      ↓
100 surrogate non-dominated candidates
      ↓
Representative LTspice re-evaluation
```

## Key reported results

| Target | Stored five-fold residual result |
|---|---:|
| Output-voltage ripple | R² ≈ 0.724 |
| Total loss | R² ≈ 0.964 |
| Efficiency | MAE ≈ 0.38 percentage points |

For the three representative candidates evaluated with the original 4–5 ms LTspice protocol, the reported surrogate-to-LTspice differences are:

- **Efficiency:** 0.38–0.84 percentage points
- **Output ripple:** 0.92–2.43 mV

A separate averaging-window sensitivity check on the maximum surrogate-predicted-efficiency candidate gives **96.1438%** over 4–5 ms and **95.9074%** over 9–10 ms, while ripple remains essentially unchanged. This is reported as a measurement-window sensitivity observation rather than as a replacement for the original validation protocol.

## Research contribution

The contribution is **methodological rather than a new machine-learning algorithm**. The study emphasizes:

1. abundant analytical low-fidelity coverage,
2. matched analytical/LTspice cross-fidelity assessment,
3. target-wise residual correction using limited switching-level data,
4. explicit diagnosis of optimizer-driven extrapolation failure,
5. safeguarded multi-objective re-optimization, and
6. source-level reproducibility of representative validation cases.

## Scope and limitations

This is a **simulation-level study**. LTspice is used as a higher-detail switching reference relative to the analytical model; it is not treated as physical hardware ground truth. The LTspice reference uses simplified switching elements, and hardware validation remains future work.

The 5 V value is the nominal analytical target. The switching-level study uses a fixed open-loop duty ratio and does not optimize closed-loop output-voltage regulation.

## Repository structure

```text
.
├── README.md
├── CITATION.cff
├── .gitignore
├── requirements.txt
├── UPLOAD_CHECKLIST.md
├── manuscript/
├── notebooks/
├── data/
├── ltspice/
├── figures/
├── results/
└── docs/
    ├── METHODOLOGY.md
    ├── REPRODUCIBILITY.md
    └── FILE_GUIDE.md
```

See [`UPLOAD_CHECKLIST.md`](UPLOAD_CHECKLIST.md) for where to place each research file.

## Reproducing the work

See [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md) for the source-to-result map and reproduction sequence.

## Software stack

The project uses Python scientific/ML tooling and LTspice, including NumPy, pandas, scikit-learn, XGBoost, pymoo, matplotlib, and LTspice automation tooling. The original environment was not version-locked; before DOI archival, the exact working environment should be exported and pinned.

## Manuscript

Place the current manuscript in [`manuscript/`](manuscript/). Until formal acceptance, describe it as a **manuscript/preprint**, not as a published peer-reviewed article.

## Data and code availability

The repository is intended to contain the processed data, notebooks, LTspice netlists/logs, figures, and validation materials supporting the manuscript. Large or restricted files should be documented rather than silently omitted.

## Citation

Citation metadata is provided in [`CITATION.cff`](CITATION.cff). DOI/arXiv identifiers can be added after archival or preprint release.

## License

A reuse license has not yet been selected. This is intentionally left open until the preferred licensing terms for code and data are confirmed.

## Contact

Raushan Kumar  
Email: raushankumar321670@gmail.com
