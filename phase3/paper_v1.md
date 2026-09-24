---
title: Posterior-Based Decision Stability under Cross-City Stress Testing in Urban Flood Risk Prioritisation
author: |
  Mikel Martinez Mugica  
  Independent Researcher  
  https://habnetic.org
date: October 2026
---

# Abstract

Urban risk prioritisation often requires selecting a small subset of assets for inspection, intervention, or further assessment from a much larger portfolio. Risk models may quantify uncertainty in estimated risk, but the operational decision is discrete: which assets fall inside the selected top-k set, and which remain outside it. Uncertainty in estimated risk is therefore not equivalent to uncertainty in the resulting decision.

This paper presents a Bayesian framework for analysing the stability of prioritisation decisions under uncertainty. Rather than treating a ranking as a fixed model output, the framework derives posterior top-k membership probabilities and decision-stability classes directly from posterior draws. This separates assets that are robustly prioritised, assets that are robustly excluded, and a smaller review set whose prioritisation changes under posterior uncertainty.

The framework is evaluated using a deliberately simplified Bayesian logistic model with exposure and pluvial hazard proxies and a synthetic binary outcome. Experiments include a Rotterdam baseline, controlled perturbation of the hazard representation, and a fixed-specification cross-city stress test in Hamburg and Donostia--San Sebastián.

Across the experiments, uncertainty affecting prioritisation membership remains concentrated near the selection boundary rather than diffusing across the full asset population. At comparable approximately 1% prioritisation thresholds, the unstable boundary represents 0.0136% of assets in Rotterdam, 0.0164% in Hamburg, and 0.1676% in Donostia--San Sebastián.

The contribution is methodological rather than hydraulic: the framework shows how posterior inference can be propagated through a ranking and selection rule to identify where uncertainty can actually change a prioritisation decision.

---

# Introduction

Urban resilience planning often requires choosing a limited number of assets for inspection, intervention, or further investigation. A municipality may face tens or hundreds of thousands of buildings while having the capacity to act on only a small subset. Under these conditions, risk estimation ultimately becomes a selection problem: which assets should fall inside the prioritised set?

Risk estimates are uncertain. Hazard representation, exposure measurement, model specification, and limited observational data can all introduce uncertainty into estimated asset-level risk. However, uncertainty in risk estimates does not necessarily imply uncertainty in the resulting prioritisation decision. Two assets may have uncertain risk estimates while nevertheless remaining consistently on opposite sides of the selection boundary.

The operational question is therefore not only:

> Which assets appear most at risk?

but also:

> Which assets remain prioritised once uncertainty is propagated through the decision rule?

For a prioritisation capacity of \(k\) assets, each posterior draw induces a ranking and therefore a top-\(k\) selected set. Repeating this process across posterior draws produces a posterior distribution over prioritisation membership. Assets can then be interpreted as robustly inside the selected set, robustly outside it, or part of an uncertain boundary where additional evidence or judgement may affect the decision.

This distinction matters because uncertainty need not require reviewing the entire asset portfolio. If posterior decision instability is concentrated near the selection boundary, uncertainty analysis can identify a smaller review set while leaving most prioritisation decisions structurally stable.

This paper develops a Bayesian framework for analysing this posterior decision-stability structure. The primary inferential quantity is the top-\(k\) membership probability: the posterior probability that an asset belongs to the selected priority set. From this quantity, the framework derives decision-stability classes and measures the size of the unstable boundary.

The objective is methodological rather than predictive. The empirical implementation uses simplified exposure and pluvial hazard proxies together with a synthetic binary outcome in order to isolate the behaviour of the inference-to-decision pipeline. It does not claim hydraulic validation or operational flood prediction.

The framework is evaluated in three stages: a Rotterdam baseline, controlled hazard perturbation experiments, and a fixed-specification cross-city stress test in Hamburg and Donostia--San Sebastián. The experiments examine whether posterior decision instability remains localised near the prioritisation boundary under hazard perturbation and across different urban input distributions.

## Contributions

This paper makes four main contributions:

- A decision-centred Bayesian formulation that separates uncertainty in estimated risk from uncertainty in the resulting prioritisation decision
- Posterior decision metrics that distinguish robustly prioritised assets, robustly excluded assets, and an uncertain review set around the selection boundary
- A perturbation experiment evaluating whether decision instability remains localised under changes in hazard representation
- A fixed-specification cross-city stress test examining whether the resulting decision-stability structure persists across heterogeneous urban contexts

---

# Conceptual Framework

The present study focuses on the propagation of uncertainty from model inputs to a discrete prioritisation decision.

The implemented computational pipeline is:

Exposure and hazard proxies → Bayesian logistic model → posterior asset-level risk → ranking within each posterior draw → top-k membership → posterior decision-stability metrics

The exposure and hazard variables are treated as model inputs rather than as latent physical processes. The Bayesian model estimates uncertainty in their relationship with the synthetic binary outcome. Posterior uncertainty in model parameters induces uncertainty in asset-level risk estimates, which in turn induces uncertainty in rankings and top-k membership.

Rather than collapsing this posterior information into a single deterministic ranking, the framework retains the full posterior set of prioritisation decisions and evaluates how consistently each asset remains inside or outside the selected top-k set.

![Computational pipeline for posterior decision-stability analysis](figures/fig01_graphical_model.pdf)

Figure 1 summarises the computational pipeline used in the present experiments. Spatial and environmental inputs are transformed into exposure and hazard proxies, which enter a Bayesian logistic model. Posterior draws generate asset-level risk estimates, rankings, and top-k selected sets. Repeated selection across posterior draws produces posterior top-k membership probabilities and decision-stability classes.

The figure represents the implemented statistical workflow rather than a hydraulic or latent-hazard process model.

---

# Experimental Design

## Phase 1 — Rotterdam baseline

The baseline implementation uses Rotterdam as the reference study area, comprising 221,324 buildings.

Exposure is represented by the RTM-anchored exposure composite \(E_i\), derived from hydrographic proximity indicators. The notation \(E_i\) is used throughout for this exposure representation. Hazard is represented by a pluvial precipitation proxy derived from ERA5 data. For cross-city consistency, the hazard variable is expressed relative to the Rotterdam reference median:

\[
H_i
=
\log
\left(
\frac{H^{\mathrm{mm}}_i}
{\operatorname{median}(H^{\mathrm{mm}}_{\mathrm{RTM}})}
\right).
\]

### Synthetic outcome generation

The binary impact outcome used in the present experiments is synthetic. Its purpose is not to reproduce observed flood damage, but to provide a controlled data-generating process through which posterior uncertainty can be propagated into prioritisation decisions.

For each asset \(i\), a latent event probability is generated using

\[
\operatorname{logit}(p_i^{\mathrm{gen}})
=
\alpha_{\mathrm{gen}}
+
\beta_{E,\mathrm{gen}} E_i
+
\beta_{H,\mathrm{gen}} H_i,
\]

with fixed generating coefficients

\[
\alpha_{\mathrm{gen}}=-3.0,
\qquad
\beta_{E,\mathrm{gen}}=1.0,
\qquad
\beta_{H,\mathrm{gen}}=1.0.
\]

The synthetic outcome is then sampled as

\[
Y_i^{\mathrm{synthetic}}
\sim
\operatorname{Bernoulli}
\left(
p_i^{\mathrm{gen}}
\right).
\]

Outcome generation uses random seed `20260430`.

The same generating mechanism and coefficients are applied to Rotterdam, Hamburg, and Donostia--San Sebastián. No city-specific coefficients or outcome-generation rules are introduced. Differences in synthetic event prevalence therefore arise from differences in the input feature distributions under the common specification rather than from city-specific tuning.

Under this mechanism, the realised synthetic event rates are approximately 6.30% in Rotterdam, 7.70% in Hamburg, and 19.42% in Donostia--San Sebastián.

The fixed coefficients were chosen to create a deliberately simple and reproducible outcome process with positive exposure and hazard effects and a relatively sparse baseline event rate. Their role is experimental rather than predictive: they provide a common data-generating mechanism for testing whether posterior-derived prioritisation decisions remain stable under uncertainty across different urban input distributions.

Accordingly, the synthetic outcome should not be interpreted as observed flood damage or as a calibrated estimate of empirical flood probability.


## Phase 2 — Hazard perturbation

Robustness is evaluated through controlled perturbation of the hazard proxy.

The transformed hazard representation is perturbed with additive Gaussian noise while maintaining the same model structure and inference procedure.

The objective is not to simulate physically realistic perturbations, but to evaluate whether instability diffuses across the prioritisation system under perturbed hazard representation.

## Phase 3 — Fixed-specification cross-city stress test

The Rotterdam specification is applied independently to Hamburg and Donostia--San Sebastián as a cross-city stress test.

No posterior parameter estimates are transferred between cities. Instead, each city is fitted independently while retaining the same:

- prior specification
- logistic model structure
- feature definitions
- Rotterdam-based scaling reference
- synthetic outcome-generating mechanism
- posterior decision metrics

Feature transformations and scaling parameters derived from Rotterdam are retained as the common reference. Consequently, differences in posterior decision stability across cities arise from differences in the city-level input distributions and resulting fitted posteriors rather than from city-specific model redesign.

The purpose of this experiment is not to test predictive transferability or out-of-sample flood prediction. It tests whether the posterior decision-stability structure persists when the same modelling specification is applied to different urban input distributions.

---

# Bayesian Model

The Bayesian inference model uses the same logistic functional form as the controlled synthetic data-generating process, but estimates its parameters from the generated observations rather than treating the generating coefficients as known.

Asset-level impact probability is modelled as:

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

- $E_i$ denotes the exposure proxy used in inference
- $H_i$ denotes the transformed hazard proxy
- $Y_i \in \{0,1\}$ denotes the synthetic binary impact observation

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

At comparable thresholds, the unstable boundary remains small in all three cities. Rotterdam and Hamburg exhibit almost negligible unstable shares, while Donostia--San Sebastián shows a wider unstable boundary under the fixed-specification cross-city stress test. Even in the strongest stress-test case, however, the unstable set remains below 0.2% of assets at the comparable 1% threshold.

## Robustness to hazard perturbation

To evaluate whether the observed concentration of instability depends on deterministic hazard specification, controlled perturbations are introduced into the transformed hazard representation:

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

## Fixed-specification cross-city stress test

The same framework is applied to Hamburg and Donostia--San Sebastián under fixed specification.

To make the cross-city comparison interpretable across differently sized cities, the main analysis uses approximately 1% prioritisation thresholds.

```{=latex}
\begin{table}[H]
\centering
\small
\begin{tabular}{lrrrrr}
\toprule
City & Assets $N$ & $k$ & Borderline count & Borderline / $N$ & Borderline / $k$ \\
\midrule
RTM & 221,324 & 2,213 & 30 & 0.0136\% & 1.36\% \\
HAM & 341,530 & 3,415 & 56 & 0.0164\% & 1.64\% \\
DON & 7,755 & 78 & 13 & 0.1676\% & 16.67\% \\
\bottomrule
\end{tabular}
\caption{Cross-city decision-stability comparison at approximately 1\% prioritisation thresholds. Borderline counts are reported relative both to the full asset population \(N\) and to prioritisation capacity \(k\).}
\end{table}
```

Table 1 summarises the cross-city stress-test result. Rotterdam and Hamburg contain approximately 30 and 56 borderline assets respectively, corresponding to 1.36% and 1.64% of their prioritisation capacities. Donostia--San Sebastián contains only 13 borderline assets in absolute terms, but these represent approximately 16.67% of its much smaller prioritisation capacity of 78 assets. The operational interpretation therefore differs substantially from the citywide borderline share alone.

Figure 7 shows the same comparison using log-scaled rank, which makes the narrow transition region near the prioritisation boundary easier to inspect.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/phase3_cross_city_stability_structure.pdf}
\caption{Cross-city posterior top-k membership structure using log-scaled rank.}
\end{figure}
```

Across all three cities, posterior instability remains concentrated near a narrow prioritisation boundary. Rotterdam and Hamburg exhibit highly polarised membership structures, while Donostia--San Sebastián exhibits moderate local broadening of the unstable boundary region under the common specification.

Figure 8 shows how borderline share evolves across prioritisation thresholds for all three cities.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/phase3_borderline_share_vs_k.pdf}
\caption{Borderline share as a function of the prioritisation threshold.}
\end{figure}
```

Across all evaluated thresholds, instability remains concentrated in a relatively small subset of assets. Donostia--San Sebastián exhibits a wider unstable boundary under the fixed-specification cross-city stress test, but the overall instability structure remains highly localised relative to the full asset population.

## Spatial structure of posterior decision stability

Figure 9 shows the spatial structure of posterior top-k membership probability and local transition regions for Rotterdam, Hamburg, and Donostia--San Sebastián under comparable prioritisation thresholds.

The spatial comparison illustrates that posterior instability is not spatially diffuse across the urban system. Instead, uncertainty in prioritisation membership remains concentrated within relatively narrow local transition structures separating stable high-priority and stable low-priority assets.

Rotterdam and Hamburg exhibit highly polarised posterior membership distributions with extremely narrow unstable regions. Donostia--San Sebastián exhibits moderate local broadening of the transition structure under the fixed-specification cross-city stress test.

These spatial figures are not presented as hydraulic validation. Their purpose is methodological: they expose the spatial organisation of posterior-derived decision stability under uncertainty propagation and fixed-specification cross-city stress testing.

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

Across the baseline, perturbation, and cross-city stress-test experiments, posterior uncertainty remains concentrated near a relatively narrow decision boundary while most assets exhibit stable prioritisation behaviour across posterior draws. These findings suggest that uncertainty propagation does not necessarily imply diffuse decision instability. Instead, uncertainty can remain localised near prioritisation thresholds, allowing most decisions to remain stable even when predictive uncertainty is substantial.

The perturbation experiments further suggest that this concentration of instability is not highly sensitive to moderate changes in the hazard representation. Similarly, the fixed-specification cross-city stress test shows that the overall decision-stability structure persists across the three evaluated urban input distributions, although the width of the unstable boundary varies across cities.

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

Across baseline, perturbation, and cross-city stress-test experiments, instability remains concentrated near a narrow prioritisation boundary while most assets exhibit stable prioritisation behaviour under posterior uncertainty. Under comparable 1% prioritisation thresholds, unstable boundary shares remain small across all three evaluated cities, although their operational importance relative to prioritisation capacity varies substantially.

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
