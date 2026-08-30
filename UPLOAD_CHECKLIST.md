# Upload Checklist

Use this checklist to populate the repository without changing the scientific claims in the manuscript.

## 1. Manuscript

Place the final submission/preprint PDF in:

```text
manuscript/publication_report.pdf
```

Optional editable source:

```text
manuscript/publication_report.docx
```

Do not label the paper as *published* until it is formally accepted/published.

## 2. Notebooks

Place the original research notebooks in `notebooks/`.

Recommended names:

```text
notebooks/01_analytical_data_generation.ipynb
notebooks/02_spice_automation.ipynb
notebooks/03_ml_training_and_residual_correction.ipynb
notebooks/04_nsga2_optimization.ipynb
```

If your existing filenames differ, you may keep the original filenames and update `docs/FILE_GUIDE.md` instead of renaming code blindly.

## 3. Data

Place analytical and LTspice-derived data in:

```text
data/analytical/
data/ltspice/
data/pareto/
```

Important files to include if available:

- 70,000-point analytical dataset
- raw or processed 300-run LTspice output table
- cleaned 285-row LTspice table used for residual learning
- constrained Pareto-front CSV containing the final 100 surrogate non-dominated candidates
- any saved train/test or validation summaries needed to reproduce reported metrics

Do not upload duplicate intermediate files unless they are needed to explain provenance.

## 4. LTspice sources and validation evidence

Use:

```text
ltspice/reference/
ltspice/automation/
ltspice/validation/
ltspice/logs/
```

Include, where available:

- common/reference switching-level schematic or netlist
- automated sweep template/files
- representative final candidate netlists
- baseline/reference manual check
- selected/balanced candidate check
- minimum-ripple candidate check
- maximum surrogate-predicted-efficiency candidate check
- corresponding LTspice `.log` files

Keep source `.asc`, `.net`, `.cir`, `.lib` (when redistributable), and `.log` files. Large generated `.raw` waveform files should normally remain local unless genuinely necessary.

## 5. Figures

Place publication figures in `figures/` using clear names such as:

```text
figures/workflow_verified.png
figures/ltspice_reference.png
figures/residual_correction_verified.png
figures/pareto_front_verified.png
```

Avoid uploading old workflow/figure versions containing superseded ranges, hyperparameters, or claims.

## 6. Results and audit material

Place compact result summaries in `results/`, for example:

```text
results/model_metrics.csv
results/representative_validation.csv
results/final_search_bounds.txt
```

If you want to publish the full source-audit report, place it in `docs/` and clearly label it as a technical/reproducibility audit rather than as the research paper.

## 7. Environment

Before DOI archival or final public release:

1. open the original working Python environment,
2. run `pip freeze > requirements-lock.txt`,
3. add `requirements-lock.txt` to the repository,
4. keep the existing `requirements.txt` as the readable dependency list.

This matters because the stored work showed small cross-version sensitivity in some validation metrics.

## 8. Files that should NOT be uploaded

- passwords, tokens, private keys or API credentials
- personal documents unrelated to the research
- duplicate manuscript drafts
- superseded figures with wrong hyperparameters/ranges
- temporary LTspice cache files
- very large `.raw` files unless essential
- copyrighted third-party PDFs that you do not have redistribution rights for

## 9. Final pre-archive check

Before Zenodo/arXiv/venue submission, verify that a new reader can find:

- [ ] final/preprint manuscript
- [ ] analytical generation notebook
- [ ] LTspice automation notebook
- [ ] ML/residual-learning notebook
- [ ] NSGA-II optimization notebook
- [ ] analytical dataset
- [ ] cleaned LTspice dataset
- [ ] final Pareto CSV
- [ ] representative validation netlists/logs
- [ ] four final publication figures
- [ ] pinned environment file
- [ ] README
- [ ] CITATION.cff
- [ ] reproducibility instructions

When these are present, the repository is ready for a versioned release and DOI archival.
