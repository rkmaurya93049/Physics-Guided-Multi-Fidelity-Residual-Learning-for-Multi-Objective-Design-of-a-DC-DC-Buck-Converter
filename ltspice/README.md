# LTspice Evidence

This directory contains the switching-level source/evidence used to support the research.

- `reference/Draft1.asc` — common/reference circuit used by the automated sweep.
- `automation/` — pointer to the canonical automation notebook in `../notebooks/`.
- `validation/` — representative candidate schematics.
- `logs/` — preserved manual LTspice measurement logs.

The main manuscript validation table uses the common original **4–5 ms** protocol. Some preserved manual engineering logs use **9–10 ms** windows; they are supplemental sensitivity/engineering evidence and must not be mixed into the original protocol comparison.

LTspice is used as a switching-level simulation reference relative to the analytical model, not as hardware ground truth.
