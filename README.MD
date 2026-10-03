# Habnetic Papers

This repository contains research manuscripts and methodological documents
produced as part of the **Habnetic** project.

Habnetic develops open-source methods for **probabilistic prioritisation and
decision stability under uncertainty**, with spatial risk assessment as the
first reference application.

## Current research

### Phase 3 — Cross-City Decision Stability

**Posterior-Based Decision Stability under Cross-City Stress Testing in Urban Flood Risk Prioritisation**

Case studies:

- Rotterdam
- Hamburg
- Donostia-San Sebastián

The study evaluates posterior decision stability under controlled hazard
perturbation and fixed-specification cross-city stress testing.

**Preprint:** Zenodo  
**DOI:** https://doi.org/10.5281/zenodo.23110714

Directory:

`phase3/`

Main files:

- `paper_v1.md` — canonical manuscript source
- `paper_v1.tex` — generated LaTeX source used for compilation
- `paper_v1.pdf` — canonical compiled preprint
- `header.tex` — LaTeX configuration used during compilation
- `figures/` — figures used in the manuscript
- `tables/` — supporting result tables
- `supporting/` — supporting communication material
- `archive/` — superseded figures, submission packages, and revision material

The analysis code and processed model inputs are maintained separately in the
main research repository.

## Earlier work

### Phase 1 — Rotterdam Decision Stability Baseline

Initial methodological implementation of posterior decision stability using
Rotterdam as the reference case.

Directory:

`phase1_rtm_decision_stability/`

This material represents an earlier stage of the research programme and is
retained for research history and reproducibility.

### Historical research plans

Earlier research plans and methodological development documents are retained in:

`archive/research_plan_2026_03/`

These documents describe earlier stages of the Habnetic research programme
and should **not** be treated as the current methodological or product roadmap.

Current research planning and project architecture are maintained in the
Habnetic documentation repository.

## Repository scope

This repository is intended for:

- research manuscripts
- compiled preprints
- manuscript figures and tables
- methodological research documents
- archived manuscript history

It is **not** the source of truth for:

- analysis code
- production Python packages
- processed datasets
- raw data
- current project planning

Those materials are maintained in the related repositories below.

## Related repositories

### Research and model implementation

https://github.com/Habnetic/resilient-housing-bayes

### Data

https://github.com/Habnetic/data

### Project documentation

https://github.com/Habnetic/docs

### Project website

https://habnetic.org

## Reproducibility

Each research phase should document:

- required input data
- analysis code
- generated outputs
- manuscript build instructions
- software environment and dependency versions
- dataset versions or provenance

The canonical implementation of the current research pipeline is maintained in:

`resilient-housing-bayes`

## License

See `LICENSE`.