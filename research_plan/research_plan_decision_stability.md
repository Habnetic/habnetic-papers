---
title: "Probabilistic Uncertainty Propagation and Decision Stability in Urban Pluvial Risk Assessment"
subtitle: "Research Plan"
author: "Mikel Martinez Mugica"
date: "March 2026"
---

# 1. Research Question

How stable are prioritisation decisions in urban pluvial risk assessments once uncertainty is propagated coherently across the full exposure–hazard–impact chain?

Urban pluvial risk assessments guide infrastructure investment and resilience planning through ranked asset lists or hotspot maps. These rankings are typically derived from expected impact estimates or deterministic risk indices. While uncertainty may be quantified within individual components (e.g. hazard or damage), it is usually handled modularly rather than through joint inference across the entire exposure–hazard–impact structure. The stability of prioritisation decisions under epistemic uncertainty and specification sensitivity therefore remains unclear.

This project treats prioritisation stability itself as the object of inference.

# 2. Conceptual Framing

Three established strands address uncertainty in urban flood risk:

- Hierarchical Bayesian damage models focus on parameter uncertainty within the impact component.
- Ensemble rainfall–runoff–hydraulic cascades propagate uncertainty across physical processes.
- Robust decision-making frameworks evaluate policy alternatives under deep uncertainty.

However, these approaches typically do not:

- Embed the full exposure–hazard–impact chain within a single coherent posterior distribution;
- Derive posterior distributions over prioritisation rankings;
- Treat ranking stability as a primary inferential quantity.

## Scope (Not Claimed)

This project does not propose:

- A new flood hazard model;
- A new physical cascade uncertainty propagation framework;
- A new robust decision paradigm.

The structural distinction lies in:

1. Elevating posterior ranking stability to the inferential target.
2. Integrating exposure–hazard–impact within a unified hierarchical generative model.

The aim is not to replace robust planning frameworks, but to clarify how epistemic uncertainty propagates into prioritisation outputs.

# 3. Generative Structure

The modelling framework specifies a hierarchical generative structure linking:

- Exposure indicators $X_i$
- Latent hazard intensity $H_i$
- Observed impact $Y_i$
- Global parameter vectors $\theta_H$ and $\theta_Y$

Hazard is conceptualised as a probabilistic input signal informed by long-term precipitation metrics rather than as a deterministic inundation map. In the current empirical implementation the hazard component is approximated using a precipitation-derived proxy. The objective is transparent uncertainty propagation through the exposure–hazard–impact chain into decision-level quantities.

Bayesian inference yields a joint posterior over model parameters and latent hazard states. Posterior predictive impact quantities are derived from this joint distribution. Decision quantities are computed as functionals of posterior draws.

![Hierarchical exposure–hazard–impact model](phase1_rtm_decision_stability/figures/fig01_graphical_model.png)

In the current empirical phase, hazard intensity is approximated by an observed precipitation-derived proxy. This corresponds to a simplified instantiation of the conceptual model in which hazard uncertainty is not yet explicitly modelled.

# 4. Model Specification Outline

Impact $Y_i$ is modelled conditionally on exposure indicators $X_i$ and hazard intensity $H_i$ through a hierarchical regression structure. Depending on data availability, the likelihood will be specified as:

- Gaussian (for continuous loss ratios)
- Log-normal or Gamma (for strictly positive loss values)
- Zero-inflated variants where non-damage cases are present

Hazard enters the impact layer as a continuous predictor, potentially interacting with exposure indicators. Hierarchical partial pooling is applied at the city level while maintaining a fixed structural specification across contexts to enable transfer-based stress testing.

Posterior inference is conducted via MCMC, and posterior predictive draws form the basis for decision-level quantities.

In Phase 1, the precipitation-derived hazard proxy enters the model as an observed covariate; explicit latent hazard modelling is reserved for extended versions of the framework.

# 5. Decision-Level Inference

For each posterior draw $m$, assets are ranked according to their model-implied risk. Prioritisation therefore becomes a distribution rather than a fixed ordering.

Decision-stability metrics include:

- Posterior rank distributions $r_i^{(m)}$
- Posterior probability of belonging to a top-k risk group $P(r_i \le k)$
- Ranking variability (e.g. rank standard deviation)

Decision stability is thus quantified directly from posterior draws rather than inferred from point estimates with post-hoc resampling.

![Decision stability concept](phase1_rtm_decision_stability/figures/fig02_decision_stability_concept.png)

Wide rank distributions indicate instability under epistemic uncertainty. High top-k probabilities indicate robust prioritisation membership.

# 6. Cross-City Transfer as Structural Stress Test

To examine structural robustness, the model is applied to two additional cities under the same generative specification. Parameters are not structurally redefined between contexts. Transfer is therefore interpreted as a stress test of structural invariance rather than recalibration.

Changes in posterior uncertainty and ranking stability under domain shift are analysed. Expansion of ranking variability indicates sensitivity of prioritisation stability to contextual differences.

![Domain-shift stress test concept](phase1_rtm_decision_stability/figures/fig03_domain_shift_illustrative.png)

Increasing posterior rank standard deviation under domain shift reflects structural stress within the generative specification.

# 7. Empirical Implementation

## Data

The Rotterdam dataset builds on a spatial data integration pipeline developed prior to the present modelling work. This pipeline combines building footprints, hydrography layers, and derived exposure indicators into a consistent asset-level dataset. Constructing this infrastructure required substantial data harmonisation and spatial processing and provides the empirical foundation for the probabilistic modelling framework implemented in the present study.

The reference city is Rotterdam. The empirical dataset includes:

- Asset-level exposure indicators
- Precipitation-derived hazard metrics (ERA5-based)
- Observed impact data where available

## Computational Strategy

- Hierarchical exposure–hazard–impact structure
- Bayesian inference via MCMC
- Posterior predictive impact simulation
- Ranking functional computation

# 8. Expected Contributions

## Methodological Contributions

1. Formalisation of exposure–hazard–impact within a unified hierarchical posterior.
2. Elevation of decision stability to a primary inferential output.
3. Structured evaluation of ranking behaviour under fixed-spec cross-city transfer.
4. Clarification of the relationship between expected-loss ranking and posterior ranking stability.

## Empirical Contributions

1. Quantification of prioritisation instability under epistemic uncertainty.
2. Evidence on how ranking robustness degrades under domain shift.

The framework reframes urban pluvial risk modelling from loss estimation toward inference about the stability of prioritisation decisions under uncertainty.

# 9. Work Plan (March 2026 – February 2027)

## Phase 1 — Baseline probabilistic pipeline (completed)
February–March 2026

- Finalise Rotterdam exposure–hazard–impact dataset derived from the existing spatial pipeline
- Implement Bayesian baseline model
- Compute posterior decision stability metrics
- Produce methodological baseline manuscript

## Phase 2 — Posterior decision stability analysis
April–June 2026

- Extend posterior analysis
- Sensitivity analysis of priors and specifications
- Alternative hazard proxy definitions
- Robustness analysis of ranking metrics

## Phase 3 — Cross-city structural transfer
July–October 2026

- Apply fixed-spec model to additional cities
- Evaluate posterior ranking behaviour under domain shift
- Quantify instability inflation across contexts

## Phase 4 — Model extension and synthesis
November 2026 – February 2027

- Introduce explicit latent hazard layer
- Compare hazard proxy vs latent hazard models
- Integrate cross-city results
- Prepare journal manuscript