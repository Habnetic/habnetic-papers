---
title: Posterior-Based Decision Stability under Cross-City Transfer in Urban Flood Risk Prioritisation
author: |
  Mikel Martinez Mugica  
  Independent Researcher  
  https://habnetic.org
date: May 2026
---

# Abstract

Urban risk prioritisation workflows frequently produce deterministic rankings despite substantial uncertainty in hazard representation, exposure estimation, and model specification. While uncertainty is often analysed within individual modelling components, downstream prioritisation decisions are less commonly evaluated explicitly as probabilistic objects.

This paper presents a Bayesian framework for analysing prioritisation stability under uncertainty propagation. Rather than treating rankings as fixed outputs, the framework derives posterior-based decision quantities including top-k membership probability and borderline decision share.

A reproducible exposure--hazard--outcome pipeline is implemented using a logistic Bayesian baseline with simplified exposure and pluvial hazard proxies. Posterior inference is performed using PyMC and prioritisation decisions are derived directly from posterior draws. The objective is methodological evaluation of posterior-derived decision behaviour rather than hydraulic calibration.

The framework is evaluated across three phases: a Rotterdam baseline, controlled hazard perturbation experiments, and fixed-specification cross-city transfer to Hamburg and Donostia--San Sebastián.

Results indicate that decision instability is not diffuse across the system. Instead, instability remains concentrated near a narrow prioritisation boundary while most assets exhibit stable prioritisation behaviour across posterior draws. At comparable 1% prioritisation thresholds, the unstable boundary represents 0.0136% of assets in Rotterdam, 0.0164% in Hamburg, and 0.1676% in Donostia--San Sebastián.

The contribution of the paper is methodological rather than hydraulic. The framework demonstrates how posterior inference can be extended beyond predictive estimation toward explicit analysis of prioritisation stability under uncertainty.

---

# 1. Introduction

Urban resilience planning frequently relies on ranked lists of assets requiring intervention, such as buildings, infrastructure segments, or neighbourhoods. These rankings typically emerge from deterministic risk scores or expected damage estimates. In practice, they directly determine which assets receive intervention under limited budgets. Ranking stability is therefore operationally important, but it is often less directly examined than model-level uncertainty.

Risk estimation involves substantial uncertainty arising from:

- hazard variability
- exposure measurement error
- model specification
- limited observational data

Although uncertainty may be analysed within individual modelling components, it is less commonly propagated through the full modelling chain to the final prioritisation decisions.

As a result, the stability of ranked intervention lists remains comparatively underexplored relative to uncertainty analysis within individual modelling components. This paper proposes treating prioritisation stability itself as the primary inferential object.

Conventional risk prioritisation asks:

> Which assets appear most at risk?

This paper asks instead:

> Which prioritisation decisions remain reliable once uncertainty is propagated through the modelling pipeline?

Under posterior uncertainty, rankings induce probabilistic membership in prioritised subsets rather than fixed deterministic decisions. The present work therefore evaluates prioritisation decisions as posterior-derived quantities rather than deterministic outputs.

The framework is evaluated across three phases: a Rotterdam baseline, hazard perturbation experiments, and fixed-specification transfer to Hamburg and Donostia--San Sebastián. The objective is not predictive optimisation for individual cities, but evaluation of whether posterior-derived decision stability remains structurally concentrated under uncertainty and distributional shift.

## Contributions

This paper makes four main contributions:

- A reproducible Bayesian baseline for posterior-derived prioritisation analysis
- A set of decision-oriented posterior metrics including top-k membership probability and borderline decision share
- A perturbation analysis showing that instability remains localised under degraded hazard representation
- A fixed-specification cross-city transfer experiment evaluating decision stability under distributional shift

---

# 2. Conceptual Framework

Urban flood risk assessments typically follow a conceptual structure linking:

Exposure → Hazard → Impact (Outcome)

where exposure describes asset characteristics, hazard represents environmental forcing, and impact represents realised damage or disruption.

In probabilistic terms, this can be expressed as a generative model:

Data → Exposure → Hazard → Outcome  
→ Posterior inference  
→ Decision metrics

Posterior uncertainty over model parameters induces uncertainty over rankings, which in turn induces uncertainty over prioritisation membership. Rather than collapsing posterior distributions to deterministic rankings, the framework derives decision quantities directly from posterior draws.

## Graphical model

![Hierarchical exposure--hazard--impact model](figures/fig01_graphical_model.png)

Figure 1 illustrates the exposure--hazard--impact structure underlying the model.

## Decision stability concept

![Classification of stable and unstable prioritisation regions](figures/RTM_03_stability_classification.png)

Figure 2 illustrates the central decision structure explored in the paper. Most assets exhibit stable prioritisation behaviour with posterior top-k membership probabilities close to either 0 or 1. Only a narrow intermediate boundary region exhibits unstable prioritisation behaviour under posterior uncertainty.

## Cross-city transfer concept

![Cross-city transfer concept](figures/fig03_domain_shift_illustrative.png)

Figure 3 illustrates the fixed-specification transfer experiment. The model specification, priors, feature definitions, scaling reference, and decision metrics remain fixed while the framework is applied across different cities. Under distributional shift, ranking variability may increase locally near the prioritisation boundary.

---

# 3. Experimental Design

## 3.1 Phase 1 — Rotterdam baseline

The baseline implementation is performed using Rotterdam as the reference study area, with 221,324 buildings.

Exposure is represented using hydrographic proximity indicators, including distance to water and local water-density aggregation. Hazard is represented using an ERA5-derived pluvial precipitation proxy.

A synthetic Bernoulli outcome variable is used to isolate the behaviour of the probabilistic inference-to-decision pipeline from empirical calibration challenges. This means the experiment evaluates the stability of posterior-derived prioritisation under a controlled outcome construction; it does not claim validation against observed flood damage.

Posterior-derived decision metrics are computed from posterior draws rather than deterministic risk scores.

## 3.2 Phase 2 — Hazard perturbation

Robustness is evaluated through controlled perturbation of the hazard proxy.

The standardised hazard representation is perturbed with additive Gaussian noise while maintaining the same model structure and inference procedure.

The objective is not to simulate physically realistic perturbations, but to evaluate whether instability diffuses across the prioritisation system under degraded hazard representation.

## 3.3 Phase 3 — Cross-city transfer

The framework is transferred from Rotterdam to Hamburg and Donostia--San Sebastián under fixed specification:

- same priors
- same model structure
- same feature definitions
- same scaling reference
- same decision metrics

Rotterdam statistics are retained as the scaling reference for transferred cities.

The objective is not predictive optimisation for each city individually, but evaluation of whether the decision-stability structure persists under fixed specification and distributional shift.

---

# 4. Bayesian Model

The model defines a simple generative structure linking exposure, hazard proxy, and binary impact outcome.

Asset-level impact probability is modelled using a logistic regression structure:

$$
p_i =
\operatorname{logit}^{-1}
\left(
\alpha + \beta_E E_i + \beta_H H_i
\right)
$$

$$
Y_i \sim \operatorname{Bernoulli}(p_i)
$$

where:

- $E_i$ denotes the exposure proxy
- $H_i$ denotes the hazard proxy
- $Y_i \in \{0,1\}$ denotes the impact indicator

## Priors

Weakly informative priors are assigned to model parameters:

$$
\alpha, \beta_E, \beta_H \sim \mathcal{N}(0, 2.5)
$$

## Inference

Posterior inference is performed using PyMC with Hamiltonian Monte Carlo sampling through the NUTS sampler.

Inference is performed using:

- 4 chains
- 1000 tuning iterations
- 1000 posterior draws per chain
- `target_accept = 0.9`

Diagnostics include:

- $\hat{R}$ convergence diagnostics
- effective sample size (ESS)
- prior predictive checks
- posterior predictive checks

## Prior predictive validation

Prior predictive checks were used to verify that the prior specification does not imply implausibly high citywide event rates before observing the synthetic outcome data.

![Prior predictive event-rate check](figures/fig_prior_predictive_event_rate.png)

The prior predictive distribution remains compatible with low baseline event rates while still allowing uncertainty across cities. This supports the use of the prior specification as a weakly informative baseline rather than an implicit high-risk assumption.

All reported models achieved stable convergence behaviour under the current specification.

## Model scope

The logistic specification is intentionally minimal. It serves as a baseline to isolate the behaviour of posterior-derived decision metrics rather than to optimise predictive performance.

The present results should therefore not be interpreted as hydraulic validation or operational flood prediction.

---

# 5. Posterior-Derived Decision Metrics

Rather than relying on point estimates, prioritisation decisions are derived directly from posterior draws.

For each posterior draw:

1. asset-level impact probabilities are computed
2. assets are ranked
3. the top-k subset is extracted

This produces a posterior distribution over prioritisation membership rather than a single deterministic ranking.

## Posterior mean risk

$$
p_{\text{mean}, i}
=
\mathbb{E}_{p(\theta \mid \text{data})}
\left[
P(Y_i = 1 \mid \theta)
\right]
$$

## Top-k membership probability

For a prioritisation threshold $k$, the probability that asset $i$ belongs to the top-k highest-risk assets is:

$$
P(i \in \text{Top}_k \mid \text{posterior})
$$

This quantity forms the primary inferential object of the analysis.

## Decision stability classes

For a given threshold $k$, each asset is assigned to one of three posterior decision-stability classes:

$$
P(i \in \text{Top}_k) \leq 0.2
$$

Stable low-priority: the asset is unlikely to belong to the prioritised set.

$$
0.2 < P(i \in \text{Top}_k) < 0.8
$$

Unstable boundary: the asset has materially uncertain prioritisation membership.

$$
P(i \in \text{Top}_k) \geq 0.8
$$

Stable high-priority: the asset is likely to belong to the prioritised set.

## Borderline share

Borderline share is defined as the proportion of assets in the unstable boundary class:

$$
\frac{1}{N}\sum_{i=1}^{N}
\mathbb{1}\left(0.2 < P(i \in \text{Top}_k) < 0.8\right)
$$

This quantity measures how much of the prioritisation system remains decision-unstable under posterior uncertainty.

---

# 6. Results

## 6.1 Rotterdam baseline

The Rotterdam baseline shows that posterior-derived prioritisation instability is strongly localised. Figure 4 shows the empirical cumulative distribution of posterior mean impact probabilities across Rotterdam.

![Empirical cumulative distribution of posterior mean probabilities](figures/fig04_pmean_city_ecdf.png)

Posterior mean probabilities are strongly concentrated near low values, while only a small subset of assets occupies the upper tail of the distribution.

Decision stability is evaluated through posterior-derived top-k membership probabilities. Figure 5 shows that borderline share remains small across evaluated prioritisation thresholds.

![Borderline share across prioritisation thresholds](figures/fig05_borderline_vs_k.png)

For the Top-1000 prioritisation threshold, most assets exhibit highly polarised membership probabilities close to either 0 or 1.

Figure 6 illustrates the deterministic ranking structure induced by posterior mean risk estimates.

![Deterministic ranking structure](figures/RTM_01_deterministic_ranking.png)

A small subset of assets occupies the upper tail of the prioritisation distribution, while the majority of assets exhibit substantially lower expected risk values. This concentration motivates the use of top-k prioritisation analysis under posterior uncertainty.

Figure 7 shows posterior top-k membership probability as a function of asset rank.

![Posterior top-k membership probability structure](figures/RTM_02_probability_ranking.png)

Most assets exhibit stable prioritisation behaviour with probabilities close to either 0 or 1. Only a narrow boundary region exhibits intermediate membership probabilities, indicating localised instability rather than diffuse uncertainty across the prioritisation system.

## Posterior predictive checks

Figure 8 shows the posterior predictive check for the Rotterdam baseline model.

![Posterior predictive check for Rotterdam](figures/ppc_RTM.png)

The posterior predictive distribution remains closely aligned with the observed synthetic outcome distribution, suggesting stable inference behaviour under the current baseline formulation. Because the outcome is synthetic, this diagnostic should be interpreted as an internal model check rather than empirical validation of flood occurrence.

---

## 6.2 Robustness to hazard perturbation

To evaluate whether the observed concentration of instability depends on deterministic hazard specification, controlled perturbations are introduced into the standardised hazard representation:

$$
H_i^{\text{perturbed}} = H_i + \epsilon_i,
\quad
\epsilon_i \sim \mathcal{N}(0, \sigma)
$$

The following perturbation levels are evaluated:

- $\sigma = 0.00$
- $\sigma = 0.05$
- $\sigma = 0.10$
- $\sigma = 0.20$
- $\sigma = 0.30$

| $\sigma$ | Borderline share |
|---|---:|
| 0.00 | 1.58% |
| 0.05 | 1.30% |
| 0.10 | 1.48% |
| 0.20 | 1.72% |
| 0.30 | 1.74% |

Figure 9 shows that increasing hazard perturbation does not produce diffuse instability across the system. Instead, instability remains concentrated near the prioritisation boundary across all tested perturbation levels.

![Decision instability under hazard perturbation](figures/fig07_borderline_vs_sigma.png)

The perturbation experiment therefore supports the interpretation that decision instability is structurally localised rather than uniformly distributed across the asset population.

---

## 6.3 Cross-city transfer

The same framework is applied to Hamburg and Donostia--San Sebastián under fixed specification.

To make the transfer comparison interpretable across differently sized cities, the main cross-city comparison uses approximately 1% prioritisation thresholds:

| City | Representative $k$ | Assets | Prioritised share | Borderline share |
|---|---:|---:|---:|---:|
| RTM | 2,213 | 221,324 | 1.00% | 0.0136% |
| HAM | 3,415 | 341,530 | 1.00% | 0.0164% |
| DON | 78 | 7,755 | 1.01% | 0.1676% |

Table 1 summarises the main transfer result. Rotterdam and Hamburg exhibit almost negligible borderline share. Donostia--San Sebastián shows a wider unstable boundary under fixed-specification transfer, but even there the unstable boundary remains below 0.2% of assets at the comparable 1% prioritisation threshold.

Figure 10 compares borderline share across cities and top-k thresholds under fixed-specification transfer.

![Borderline share under fixed-specification transfer](figures/phase3_borderline_share_vs_k.png)

### Rotterdam → Hamburg

The Hamburg transfer exhibits decision behaviour similar to the Rotterdam baseline. Top-k membership probabilities remain strongly polarised and borderline share remains negligible across evaluated prioritisation thresholds.

The concentration of instability near the prioritisation boundary therefore remains structurally preserved under transfer.

### Rotterdam → Donostia--San Sebastián

Donostia--San Sebastián exhibits moderate broadening of the unstable prioritisation boundary consistent with distributional shift, while overall instability remains spatially concentrated.

Figure 11 compares posterior top-k membership probability structure across Rotterdam, Hamburg, and Donostia--San Sebastián under fixed-specification transfer.

![Cross-city posterior probability comparison](figures/04_cross_city_probability_structure.png)

Across all three cities, posterior instability remains concentrated near a narrow prioritisation boundary. Rotterdam and Hamburg exhibit highly polarised membership structure, while Donostia--San Sebastián exhibits moderate local broadening of the unstable boundary region consistent with distributional shift.

Figures 12--14 show the spatial distribution of posterior top-k membership probability for the Rotterdam reference case and the two transferred cities.

![Rotterdam posterior top-k probability map, k = 1000](figures/RTM_topk_prob_k1000_map.png)

![Hamburg posterior top-k probability map, k = 1000](figures/HAM_topk_prob_k1000_map.png)

![Donostia--San Sebastián posterior top-k probability map, k = 1000](figures/DON_topk_prob_k1000_map.png)

The Rotterdam and Hamburg maps show highly polarised posterior membership structure, with most assets assigned probabilities close to either 0 or 1. Donostia--San Sebastián exhibits increased local fragmentation near the prioritisation boundary under fixed-specification transfer. Despite this degradation, instability remains spatially concentrated rather than diffuse across the system.

The transfer experiments suggest that the concentration of decision instability near a narrow prioritisation boundary may represent a structural property of the inference-to-decision pipeline rather than a city-specific artefact.

---

# 7. Discussion

This study demonstrates how prioritisation decisions can be analysed as probabilistic objects rather than deterministic rankings.

By deriving decision metrics directly from posterior distributions, the framework evaluates not only which assets appear most at risk, but which prioritisation decisions remain stable under uncertainty propagation.

Across baseline, perturbation, and transfer experiments, posterior uncertainty remains concentrated near a relatively narrow prioritisation boundary while the majority of assets exhibit stable membership behaviour across posterior draws.

The results suggest that uncertainty propagation does not necessarily imply diffuse decision instability. Instead, posterior uncertainty can concentrate into localised unstable boundary regions near prioritisation thresholds. This distinction matters operationally because uncertainty-aware prioritisation may remain actionable even when predictive uncertainty is substantial.

The perturbation experiments indicate that this concentration of instability is not highly sensitive to moderate degradation in hazard representation. Similarly, the transfer experiments suggest that the overall decision-stability structure may persist under moderate distributional shift when the model specification remains fixed.

The present results should not be interpreted as evidence of predictive generalisation across cities. The experiments instead evaluate whether posterior-derived decision behaviour remains structurally stable under fixed-specification transfer.

Likewise, the framework evaluates stability of prioritisation under uncertainty rather than correctness of prioritisation itself. The present framework does not evaluate whether prioritisation decisions are correct in a hydraulic or causal sense. It evaluates the structural stability of prioritisation behaviour conditional on the assumed model specification.

---

# 8. Limitations

Several limitations should be noted.

- The hazard representation is proxy-based and does not model hydraulic flood dynamics.
- The outcome variable is synthetic and not calibrated against observed damage data.
- Exposure is represented using simplified hydrographic proximity indicators.
- The current framework evaluates a single model family with limited prior sensitivity analysis.
- The framework does not currently include latent hazard processes or utility-theoretic decision modelling.

These limitations are central to the interpretation of the results. The analysis does not claim operational flood prediction, hydraulic validation, or empirical correctness of the prioritised assets. It isolates the statistical behaviour of posterior-derived prioritisation metrics under a deliberately simplified model.

No claims of operational deployment or empirical flood prediction are made.

---

# 9. Conclusion

This paper presented a Bayesian framework for analysing prioritisation decision stability under uncertainty propagation.

Rather than treating rankings as deterministic outputs, the framework derives posterior-based decision quantities that quantify uncertainty in prioritisation membership itself.

Across baseline, perturbation, and transfer experiments, instability remains concentrated near a narrow prioritisation boundary while most assets exhibit stable prioritisation behaviour under posterior uncertainty. Under comparable 1% prioritisation thresholds, unstable boundary shares remain small across all three evaluated cities, even under fixed-specification transfer.

The contribution of the framework is methodological rather than hydraulic. The results demonstrate how posterior inference can be extended from predictive estimation toward explicit analysis of prioritisation stability under uncertainty.

---

# References

Hall, J. W., & Harvey, H. (2009). Decision making under severe uncertainties for flood risk management: A case study of Info-Gap robustness analysis. *Journal of Flood Risk Management*.

Lv, H., Wu, Z., Guan, X., & Meng, Y. (2021). The construction of flood loss ratio function in cities lacking loss data based on dynamic proportional substitution and hierarchical Bayesian model. *Journal of Hydrology*.

McMillan, H., & Brasington, J. (2008). End-to-end flood risk assessment: A coupled model cascade with uncertainty estimation. *Water Resources Research*.

Mohor, G. S., et al. (2021). Residential flood loss estimated from Bayesian multilevel models. *Natural Hazards and Earth System Sciences*.

Sairam, N., Schröter, K., Rözer, V., Merz, B., & Kreibich, H. (2019). A Bayesian hierarchical model for flood damage estimation in data-scarce regions. *Water Resources Research*.

Wu, Y., et al. (2019). Assessing urban flood disaster risk using Bayesian network model and GIS applications. *International Journal of River Basin Management*.

Wu, Y., et al. (2020). Urban flood disaster risk evaluation based on ontology and Bayesian Network. *Journal of Hydrology*.


# Appendix A. Model input sample

A five-row sample of the Rotterdam model input table is provided to make the asset-level schema explicit. The full input tables are stored in:

- `outputs/phase3/RTM/phase3_features_scaled.parquet`
- `outputs/phase3/HAM/phase3_features_scaled.parquet`
- `outputs/phase3/DON/phase3_features_scaled.parquet`

| bldg_id | E_hat_v0 | H_pluvial_v1_mm | H_pluvial_v1_logrel | Y_damage |
|---|---:|---:|---:|---:|
| 305012 | -0.0333624 | 25.4222 | -0.00867723 | 0 |
| 313960 | 0.237889 | 25.4188 | -0.00880855 | 0 |
| 313263 | -0.130974 | 25.4231 | -0.00863978 | 0 |
| 310491 | -0.272604 | 25.4245 | -0.00858525 | 0 |
| 313127 | -0.342371 | 25.4235 | -0.00862493 | 0 |

The sample is included for transparency only and is not used directly for inference beyond illustrating the asset-level schema consumed by the model.
