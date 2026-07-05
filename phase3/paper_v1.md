---
title: Posterior-Based Decision Stability under Cross-City Transfer in Urban Flood Risk Prioritisation
author: |
  Mikel Martinez Mugica  
  Independent Researcher  
  https://habnetic.org
date: June 2026
---

# Abstract

Urban risk prioritisation workflows frequently produce deterministic rankings despite substantial uncertainty in hazard representation, exposure estimation, and model specification. While uncertainty is often analysed within individual modelling components, downstream prioritisation decisions are less commonly evaluated explicitly as probabilistic objects.

This paper presents a Bayesian framework for analysing decision stability under uncertainty propagation. Rather than treating rankings as fixed outputs, the framework derives posterior-based decision quantities including top-k membership probability and borderline decision share.

A reproducible exposure--hazard--outcome pipeline is implemented using a logistic Bayesian baseline with simplified exposure and pluvial hazard proxies. Posterior inference is performed using PyMC and prioritisation decisions are derived directly from posterior draws. The objective is methodological evaluation of posterior-derived decision behaviour rather than hydraulic calibration.

The framework is evaluated across three phases: a Rotterdam baseline, controlled hazard perturbation experiments, and fixed-specification cross-city transfer to Hamburg and Donostia--San Sebastián.

Results indicate that decision instability is not diffuse across the system. Instead, instability remains concentrated near a narrow decision boundary while most assets exhibit stable prioritisation behaviour across posterior draws. At comparable 1% prioritisation thresholds, the unstable boundary represents 0.0136% of assets in Rotterdam, 0.0164% in Hamburg, and 0.1676% in Donostia--San Sebastián.

These results demonstrate how posterior inference can be extended beyond predictive estimation toward explicit analysis of posterior decision stability under uncertainty.

---

# Introduction

Urban resilience planning frequently requires prioritising assets for intervention, such as buildings, infrastructure segments, or neighbourhoods. These priorities are commonly derived from deterministic risk scores or expected damage estimates. In practice, they directly determine which assets receive intervention under limited budgets. Decision stability is therefore operationally important, but it is often less directly examined than model-level uncertainty.

Risk estimation involves substantial uncertainty arising from:

- hazard variability
- exposure measurement error
- model specification
- limited observational data

Although uncertainty may be analysed within individual modelling components, it is less commonly propagated through the full modelling chain to the final prioritisation decisions.

As a result, the stability of prioritisation decisions remains comparatively underexplored relative to uncertainty analysis within individual modelling components. This paper proposes treating posterior decision stability itself as the primary inferential object.

Conventional risk prioritisation asks:

> Which assets appear most at risk?

This paper instead asks:

> Which prioritisation decisions remain reliable once uncertainty has been propagated through the modelling pipeline?

Under posterior uncertainty, rankings induce probabilistic membership in prioritised subsets rather than fixed deterministic decisions. The present work therefore evaluates prioritisation decisions as posterior-derived quantities rather than deterministic outputs.

The framework is evaluated across three phases: a Rotterdam baseline, hazard perturbation experiments, and fixed-specification transfer to Hamburg and Donostia--San Sebastián. The objective is not predictive optimisation for individual cities, but to evaluate whether posterior-derived decision stability remains structurally concentrated under uncertainty and distributional shift.

## Contributions

This paper makes four main contributions:

- A reproducible Bayesian framework for posterior decision-stability analysis
- A set of decision-oriented posterior metrics including top-k membership probability and borderline decision share
- A perturbation analysis showing that instability remains localised under perturbed hazard representation
- A fixed-specification cross-city transfer experiment for evaluating decision stability under distributional shift

---

# Conceptual Framework

Urban flood risk assessments typically follow a conceptual structure linking:

Exposure → Hazard → Impact (Outcome)

where exposure describes asset characteristics, hazard represents environmental forcing, and impact represents realised damage or disruption.

Within the proposed framework, this structure is extended into a Bayesian inference workflow:

Exposure → Hazard → Impact → Posterior inference → Posterior decision metrics

Posterior uncertainty over model parameters induces uncertainty over asset rankings, which in turn induces uncertainty over prioritisation membership. Rather than collapsing posterior distributions into deterministic rankings, the proposed framework derives posterior decision metrics directly from posterior draws.

![Hierarchical exposure--hazard--impact model](figures/fig01_graphical_model.pdf)

Figure 1 summarises the probabilistic structure of the proposed framework. Exposure indicators and hazard processes jointly determine impact, posterior inference propagates uncertainty through the model, and posterior-derived decision metrics quantify uncertainty in prioritisation decisions. The empirical implementation uses simplified exposure and pluvial hazard proxies together with a synthetic binary outcome to isolate decision behaviour under uncertainty. The figure is intended as a conceptual representation of the proposed framework rather than a hydraulic flood-process diagram.

---

# Experimental Design

## Phase 1 — Rotterdam baseline

The baseline implementation is performed using Rotterdam as the reference study area, with 221,324 buildings.

Exposure is represented using hydrographic proximity indicators, including distance to water and local water-density aggregation. Hazard is represented using an ERA5-derived pluvial precipitation proxy.

A synthetic Bernoulli outcome variable is used to isolate the behaviour of the probabilistic inference-to-decision pipeline from empirical calibration challenges. This means the experiment evaluates the stability of posterior-derived prioritisation under a controlled outcome construction; it does not claim validation against observed flood damage.


## Phase 2 — Hazard perturbation

Robustness is evaluated through controlled perturbation of the hazard proxy.

The standardised hazard representation is perturbed with additive Gaussian noise while maintaining the same model structure and inference procedure.

The objective is not to simulate physically realistic perturbations, but to evaluate whether instability diffuses across the prioritisation system under perturbed hazard representation.

## Phase 3 — Fixed-specification cross-city transfer

The framework is transferred from Rotterdam to Hamburg and Donostia--San Sebastián under fixed specification:

- same priors
- same model structure
- same feature definitions
- same scaling reference
- same decision metrics

Feature standardisation is performed using Rotterdam statistics, which are retained as the scaling reference for the transferred cities.

The objective is not predictive optimisation for each city individually, but evaluation of whether the decision-stability structure persists under fixed specification and distributional shift.

---

# Bayesian Model

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
- $Y_i \in \{0,1\}$ denotes the binary impact indicator

## Priors

Weakly informative Gaussian priors are assigned to the intercept and regression coefficients:

$$
\alpha, \beta_E, \beta_H \sim \mathcal{N}(0, 2.5)
$$

where 2.5 denotes the prior standard deviation.

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

Prior predictive checks were used to verify that the prior specification does not imply implausibly high citywide event rates before observing the synthetic outcome data. Posterior predictive checks were used as internal model checks under the synthetic Bernoulli outcome construction. These diagnostics support stable inference behaviour under the current specification, but they should not be interpreted as empirical flood validation.

![Prior predictive event-rate check](figures/fig_prior_predictive_event_rate.pdf)

Figure 2 shows the prior predictive event-rate check across Rotterdam, Hamburg, and Donostia--San Sebastián. The prior predictive intervals remain broad, as expected under weakly informative priors, but low baseline event rates remain plausible before observing the synthetic outcome data.

## Model scope

The logistic specification is intentionally minimal. It serves as a baseline to isolate the behaviour of posterior-derived decision metrics rather than to optimise predictive performance.

The present results should therefore not be interpreted as hydraulic validation or operational flood prediction.

---

# Posterior-Derived Decision Metrics

Rather than relying on point estimates, prioritisation decisions are derived directly from posterior draws.

For each posterior draw $s$:

1. asset-level impact probabilities $p_i^{(s)}$ are computed
2. assets are ranked by $p_i^{(s)}$
3. the top-k subset $\text{Top}_k^{(s)}$ is extracted

This produces a posterior distribution over prioritisation membership rather than a single deterministic ranking.

## Posterior mean risk

Posterior mean risk is defined as the posterior expectation of the asset-level impact probability:

$$
p_{\text{mean}, i}
=
\mathbb{E}_{p(\theta \mid \text{data})}
\left[
P(Y_i = 1 \mid \theta)
\right]
$$

Equivalently, using posterior draws:

$$
p_{\text{mean}, i}
\approx
\frac{1}{S}
\sum_{s=1}^{S}
p_i^{(s)}
$$

## Top-k membership probability

For a prioritisation threshold $k$, the posterior probability that asset $i$ belongs to the top-k highest-risk assets is:

$$
\pi_{i,k}
=
P(i \in \text{Top}_k \mid \text{data})
$$

This is estimated from posterior draws as:

$$
\pi_{i,k}
\approx
\frac{1}{S}
\sum_{s=1}^{S}
\mathbb{1}
\left(
i \in \text{Top}_k^{(s)}
\right)
$$

This quantity forms the primary inferential object of the analysis.

## Decision stability classes

For a given threshold $k$, each asset is assigned to one of three posterior decision-stability classes using $\pi_{i,k}$:

$$
\pi_{i,k} \leq 0.2
$$

Stable low-priority: the asset is unlikely to belong to the prioritised set.

$$
0.2 < \pi_{i,k} < 0.8
$$

Unstable boundary: the asset has materially uncertain prioritisation membership.

$$
\pi_{i,k} \geq 0.8
$$

Stable high-priority: the asset is likely to belong to the prioritised set.

## Borderline share

Borderline share is defined as the proportion of assets in the unstable boundary class:

$$
\frac{1}{N}\sum_{i=1}^{N}
\mathbb{1}
\left(
0.2 < \pi_{i,k} < 0.8
\right)
$$

This quantity measures how much of the prioritisation system remains decision-unstable under posterior uncertainty.

---

# Results

## Baseline risk concentration and decision-stability structure

Figure 3 shows the deterministic ranking structure induced by posterior mean risk in the Rotterdam baseline. Expected risk is concentrated in a small upper tail rather than distributed evenly across the asset population.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/phase3_expected_risk_ranking_RTM.pdf}
\caption{Expected-risk ranking for the Rotterdam baseline.}
\end{figure}
```

This ranking alone is not the main inferential object of the paper. It motivates the decision-stability analysis by showing where prioritisation pressure concentrates under a limited intervention budget.

Figure 4 shows the posterior top-k membership structure for the Rotterdam baseline at an approximately 1% prioritisation threshold. The curve separates the asset population into three decision-stability regions: stable high-priority assets, an unstable boundary, and stable low-priority assets.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/phase3_decision_stability_structure_RTM.pdf}
\caption{Posterior decision-stability structure for the Rotterdam baseline.}
\end{figure}
```

The result is not that all risk estimates are certain. The result is more specific: uncertainty affecting prioritisation membership is concentrated near the decision boundary. Most buildings are consistently classified as either inside or outside the prioritised set across posterior draws.

```{=latex}
\FloatBarrier
```

## Decision-stability composition across cities

Figure 5 compares the share of assets in each decision-stability class across Rotterdam, Hamburg, and Donostia--San Sebastián at comparable 1% prioritisation thresholds.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/phase3_certainty_composition_bar.pdf}
\caption{Decision-stability composition across the three study areas.}
\end{figure}
```

At comparable thresholds, the unstable boundary remains small in all three cities. Rotterdam and Hamburg exhibit almost negligible unstable shares, while Donostia--San Sebastián shows a wider unstable boundary under fixed-specification transfer. Even in the strongest transfer-stress case, however, the unstable set remains below 0.2% of assets at the comparable 1% threshold.

## Robustness to hazard perturbation

To evaluate whether the observed concentration of instability depends on deterministic hazard specification, controlled perturbations are introduced into the standardised hazard representation:

$$
H_i^{\text{perturbed}} = H_i + \epsilon_i,
\quad
\epsilon_i \sim \mathcal{N}(0, \sigma)
$$

The following perturbation levels are evaluated:

* $\sigma = 0.00$
* $\sigma = 0.05$
* $\sigma = 0.10$
* $\sigma = 0.20$
* $\sigma = 0.30$

| $\sigma$ | Borderline share |
| -------- | ---------------: |
| 0.00     |            1.58% |
| 0.05     |            1.30% |
| 0.10     |            1.48% |
| 0.20     |            1.72% |
| 0.30     |            1.74% |

Figure 6 shows that increasing hazard perturbation does not produce diffuse instability across the system. Instead, instability remains concentrated near the prioritisation boundary across all tested perturbation levels.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/fig07_borderline_vs_sigma.pdf}
\caption{Decision instability under hazard perturbation.}
\end{figure}
```

The perturbation experiment therefore supports the interpretation that decision instability is structurally localised rather than uniformly distributed across the asset population.

## Cross-city transfer

The same framework is applied to Hamburg and Donostia--San Sebastián under fixed specification.

To make the transfer comparison interpretable across differently sized cities, the main cross-city comparison uses approximately 1% prioritisation thresholds.

```{=latex}
\begin{table}[H]
\centering
\begin{tabular}{lrrrr}
\toprule
City & Representative $k$ & Assets & Prioritised share & Borderline share \\
\midrule
RTM & 2,213 & 221,324 & 1.00\% & 0.0136\% \\
HAM & 3,415 & 341,530 & 1.00\% & 0.0164\% \\
DON & 78 & 7,755 & 1.01\% & 0.1676\% \\
\bottomrule
\end{tabular}
\caption{Cross-city comparison at approximately 1\% prioritisation thresholds.}
\end{table}
```

Table 1 summarises the main transfer result. Rotterdam and Hamburg exhibit almost negligible borderline share. Donostia--San Sebastián shows a wider unstable boundary under fixed-specification transfer, but even there the unstable boundary remains below 0.2% of assets at the comparable 1% prioritisation threshold.

Figure 7 shows the same comparison using log-scaled rank, which makes the narrow transition region near the prioritisation boundary easier to inspect.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/phase3_cross_city_stability_structure.pdf}
\caption{Cross-city posterior top-k membership structure using log-scaled rank.}
\end{figure}
```

Across all three cities, posterior instability remains concentrated near a narrow prioritisation boundary. Rotterdam and Hamburg exhibit highly polarised membership structure, while Donostia--San Sebastián exhibits moderate local broadening of the unstable boundary region consistent with distributional shift.

Figure 8 shows how borderline share evolves across prioritisation thresholds for all three cities.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/phase3_borderline_share_vs_k.pdf}
\caption{Borderline share as a function of the prioritisation threshold.}
\end{figure}
```

Across all evaluated thresholds, instability remains concentrated in a relatively small subset of assets. Donostia--San Sebastián exhibits a wider unstable boundary under fixed-specification transfer, but the overall instability structure remains highly localised relative to the full asset population.

## Spatial structure of posterior decision stability

Figure 9 shows the spatial structure of posterior top-k membership probability and local transition regions for Rotterdam, Hamburg, and Donostia--San Sebastián under comparable prioritisation thresholds.

The spatial comparison illustrates that posterior instability is not spatially diffuse across the urban system. Instead, uncertainty in prioritisation membership remains concentrated within relatively narrow local transition structures separating stable high-priority and stable low-priority assets.

Rotterdam and Hamburg exhibit highly polarised posterior membership distributions with extremely narrow unstable regions. Donostia--San Sebastián exhibits moderate local broadening of the transition structure under fixed-specification transfer, consistent with stronger distributional shift relative to the Rotterdam reference specification.

These spatial figures are not presented as hydraulic validation. Their purpose is methodological: they expose the spatial organisation of posterior-derived decision stability under uncertainty propagation and cross-city transfer.

```{=latex}
\clearpage

\begin{figure}[!p]
\centering
\includegraphics[width=0.95\textwidth]{figures/RTM_paper_citywide_topk_map.pdf}

\vspace{0.5em}

\includegraphics[width=0.82\textwidth]{figures/RTM_paper_boundary_zoom_map.pdf}

\caption{Spatial structure of posterior top-k membership probability and local transition region for Rotterdam. The citywide map shows posterior top-k membership probability; the zoom map shows the local transition region near the prioritisation boundary.}
\end{figure}

\clearpage

\begin{figure}[!p]
\centering
\includegraphics[width=0.95\textwidth]{figures/HAM_paper_citywide_topk_map.pdf}

\vspace{0.5em}

\includegraphics[width=0.82\textwidth]{figures/HAM_paper_boundary_zoom_map.pdf}

\caption{Spatial structure of posterior top-k membership probability and local transition region for Hamburg. The citywide map shows posterior top-k membership probability; the zoom map shows the local transition region near the prioritisation boundary.}
\end{figure}

\clearpage

\begin{figure}[!p]
\centering
\includegraphics[width=0.95\textwidth]{figures/DON_paper_citywide_topk_map.pdf}

\vspace{0.5em}

\includegraphics[width=0.82\textwidth]{figures/DON_paper_boundary_zoom_map.pdf}

\caption{Spatial structure of posterior top-k membership probability and local transition region for Donostia--San Sebastián. The citywide map shows posterior top-k membership probability; the zoom map shows the local transition region near the prioritisation boundary.}
\end{figure}

\clearpage
```

---

# Discussion

This study demonstrates how prioritisation decisions can be analysed as probabilistic objects rather than deterministic rankings. By deriving decision metrics directly from posterior distributions, the proposed framework evaluates not only which assets appear most at risk, but which prioritisation decisions remain reliable under uncertainty.

Across the baseline, perturbation, and transfer experiments, posterior uncertainty remains concentrated near a relatively narrow decision boundary while most assets exhibit stable prioritisation behaviour across posterior draws. These findings suggest that uncertainty propagation does not necessarily imply diffuse decision instability. Instead, uncertainty can remain localised near prioritisation thresholds, allowing most decisions to remain stable even when predictive uncertainty is substantial.

The perturbation experiments further suggest that this concentration of instability is not highly sensitive to moderate changes in the hazard representation. Similarly, the fixed-specification transfer experiments indicate that the overall decision-stability structure may persist under moderate distributional shift, although the width of the unstable boundary varies across cities.

These findings should not be interpreted as evidence of predictive generalisation or hydraulic validity across study areas. Rather, the experiments evaluate the structural behaviour of posterior-derived decision metrics under a fixed probabilistic specification. The framework therefore assesses the stability of prioritisation decisions conditional on the assumed model, rather than the empirical correctness of the prioritisation itself.

---

# Limitations

Several limitations should be noted.

- The hazard representation is proxy-based and does not model hydraulic flood dynamics.
- The outcome variable is synthetic and not calibrated against observed damage data.
- Exposure is represented using simplified hydrographic proximity indicators.
- The current framework evaluates a single model family with limited prior sensitivity analysis.
- The framework does not currently include latent hazard processes or utility-theoretic decision modelling.

These limitations are central to the interpretation of the results. The analysis does not claim operational flood prediction, hydraulic validation, or empirical correctness of the prioritised assets. It isolates the statistical behaviour of posterior-derived prioritisation metrics under a deliberately simplified model.

No claims of operational deployment or empirical flood prediction are made.

---

# Conclusion

This paper presented a Bayesian framework for analysing posterior decision stability under uncertainty propagation.

Rather than treating rankings as deterministic outputs, the framework derives posterior-based decision quantities that quantify uncertainty in prioritisation membership itself.

Across baseline, perturbation, and transfer experiments, instability remains concentrated near a narrow prioritisation boundary while most assets exhibit stable prioritisation behaviour under posterior uncertainty. Under comparable 1% prioritisation thresholds, unstable boundary shares remain small across all three evaluated cities, even under fixed-specification transfer.

The contribution is methodological rather than hydraulic. The results demonstrate how posterior inference can be extended from predictive estimation toward explicit analysis of posterior decision stability under uncertainty.

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

A five-row sample of the Rotterdam model input table is provided to make the asset-level schema explicit.

The complete processed input tables are stored in:

- `outputs/phase3/RTM/phase3_features_scaled.parquet`
- `outputs/phase3/HAM/phase3_features_scaled.parquet`
- `outputs/phase3/DON/phase3_features_scaled.parquet`

```{=latex}
\begin{table}[H]
\centering
\small
\begin{tabular}{rrrrr}
\toprule
Building ID & Exposure ($E$) & Hazard mm & Hazard log-rel & damage \\
\midrule
305012 & -0.033 & 25.422 & -0.00868 & 0 \\
313960 & 0.238 & 25.419 & -0.00881 & 0 \\
313263 & -0.131 & 25.423 & -0.00864 & 0 \\
310491 & -0.273 & 25.425 & -0.00859 & 0 \\
313127 & -0.342 & 25.424 & -0.00862 & 0 \\
\bottomrule
\end{tabular}
\end{table}
```

The sample is included for transparency only and is not used directly for inference beyond illustrating the asset-level schema consumed by the model.
