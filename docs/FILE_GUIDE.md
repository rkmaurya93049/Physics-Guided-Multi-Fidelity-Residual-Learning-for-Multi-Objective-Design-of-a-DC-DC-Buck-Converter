# File Guide

This document is the map between repository folders and the research workflow. Update the exact filenames after the original project files are uploaded.

## `manuscript/`

Purpose: publication/preprint files.

Expected contents:

- final submission/preprint PDF
- optional editable DOCX source

Do not keep multiple outdated manuscript drafts in the public release.

## `notebooks/`

Purpose: executable research workflow.

Expected notebook roles:

| Role | Suggested filename |
|---|---|
| Analytical data generation / model equations | `01_analytical_data_generation.ipynb` |
| Automated LTspice sweep / parsing | `02_spice_automation.ipynb` |
| ML training, transfer evaluation, residual correction | `03_ml_training_and_residual_correction.ipynb` |
| NSGA-II optimization and candidate selection | `04_nsga2_optimization.ipynb` |

If your existing notebook names differ, keep them and update this table rather than risking accidental code changes.

## `data/analytical/`

Purpose: low-fidelity analytical data.

Expected content:

- the 70,000-row analytical dataset
- any compact metadata describing columns/units

## `data/ltspice/`

Purpose: switching-level data used for cross-fidelity correction.

Expected content:

- 300-run automation output if retained
- cleaned 285-row dataset used for residual learning
- any explicit filtering summary

## `data/pareto/`

Purpose: final safeguarded NSGA-II outputs.

Expected content:

- final constrained Pareto CSV with 100 surrogate non-dominated candidates
- optional normalized-distance selection table

## `ltspice/reference/`

Purpose: common switching-level circuit/template used during automation.

Include source schematics/netlists needed to understand the reference model.

## `ltspice/automation/`

Purpose: sweep templates or generated configuration needed by the automation notebook.

Avoid uploading hundreds of redundant generated files when a template + dataset + notebook is sufficient to reproduce them.

## `ltspice/validation/`

Purpose: the representative final candidate source files.

Important representative cases:

- minimum-ripple candidate
- maximum surrogate-predicted-efficiency candidate
- balanced selected candidate
- optional baseline/reference engineering check

## `ltspice/logs/`

Purpose: direct numerical evidence from LTspice.

Include the logs used to support the representative validation table and manual reconstruction/window-sensitivity discussion.

## `figures/`

Purpose: publication-quality figures.

Keep only the final/verified versions in the archival release. Recommended filenames:

- `workflow_verified.png`
- `ltspice_reference.png`
- `residual_correction_verified.png`
- `pareto_front_verified.png`

## `results/`

Purpose: small human-readable summaries derived from the notebooks/data.

Good examples:

- model metric table
- final search bounds
- representative validation summary
- balanced-candidate selection summary

Do not duplicate entire source datasets here.

## `docs/`

Purpose: research documentation.

Current files:

- `METHODOLOGY.md` — implemented research method and limitations
- `REPRODUCIBILITY.md` — source-to-result reproduction procedure
- `FILE_GUIDE.md` — this file

Optional future documents:

- technical source audit
- data dictionary
- changelog for the archival release

## Naming and provenance rules

1. Preserve original raw/source files when they carry evidentiary value.
2. Do not silently edit historical LTspice logs or CSV outputs.
3. If a cleaned file is derived from a raw file, make the relationship explicit in a notebook or README.
4. Record units in filenames/metadata or column documentation where ambiguity is possible.
5. Keep superseded files out of the release or place them in a clearly marked archive outside the main reproducibility path.
6. Never upload credentials, tokens, personal identity documents, or unrelated files.
