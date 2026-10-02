---
title: Posterior-Based Decision Stability under Cross-City Stress Testing in Urban Flood Risk Prioritisation
author: |
  \begin{tabular}{c}
  Mikel Martinez Mugica \\
  {\small Independent Researcher} \\
  {\small \href{https://habnetic.org}{https://habnetic.org}} \\
  {\small Correspondence: \href{mailto:mikel.knowledge@gmail.com}{mikel.knowledge@gmail.com}} \\
  {\small ORCID: \href{https://orcid.org/0009-0006-5170-4405}{https://orcid.org/0009-0006-5170-4405}} \\[0.7em]
  {\small\itshape This manuscript is a non-peer-reviewed preprint} \\
  {\small\itshape submitted to EarthArXiv.}
  \end{tabular}
date: October 2026

---

# Abstract

Urban risk prioritisation often requires selecting a small subset of assets for inspection, intervention, or further assessment from a much larger portfolio. Risk models may quantify uncertainty in estimated risk, but the operational decision is discrete: which assets fall inside the selected top-k set, and which remain outside it. Uncertainty in estimated risk is therefore not equivalent to uncertainty in the resulting decision.

This paper presents a Bayesian framework for analysing the stability of prioritisation decisions under uncertainty. Rather than treating a ranking as a fixed model output, the framework uses posterior top-k membership probabilities derived from posterior draws to construct decision-stability classes. This separates assets that are robustly prioritised, assets that are robustly excluded, and a smaller review set whose prioritisation changes under posterior uncertainty.

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

This paper develops a decision-stability framework for fixed-capacity spatial risk prioritisation. The primary inferential quantity is the top-\(k\) membership probability: the posterior probability that an asset belongs to the selected priority set. From this quantity, the framework derives decision-stability classes and measures the size of the unstable boundary.

The objective is methodological rather than predictive. The empirical implementation uses simplified exposure and pluvial hazard proxies together with a synthetic binary outcome in order to isolate the behaviour of the inference-to-decision pipeline. It does not claim hydraulic validation or operational flood prediction.

The framework is evaluated in three stages: a Rotterdam baseline, controlled hazard perturbation experiments, and a fixed-specification cross-city stress test in Hamburg and Donostia--San Sebastián. The experiments examine whether posterior decision instability remains localised near the prioritisation boundary under hazard perturbation and across different urban input distributions.

## Contributions

This paper makes four main contributions:

- A decision-stability formulation for fixed-capacity spatial risk prioritisation in which posterior uncertainty is propagated through the top-\(k\) selection rule rather than summarised only at the level of asset-level risk estimates

- An operational characterisation of the resulting decision boundary using posterior top-\(k\) membership probabilities, distinguishing decisions that remain stable from a smaller review set whose membership is sensitive to posterior uncertainty

- A repeated perturbation experiment evaluating whether the localisation of this decision boundary persists under controlled changes in the hazard representation

- A fixed-specification cross-city stress test examining whether the same decision-stability structure persists across materially different urban input distributions

---

# Related Work

Uncertainty representation and propagation are longstanding concerns in flood-risk modelling. McMillan and Brasington (2008) propagated uncertainty through an end-to-end flood-risk model cascade, illustrating how uncertainty introduced at different stages can affect downstream risk estimates. Hall and Solomatine (2008) extended the problem explicitly into the decision domain, examining how uncertainty in flood-risk analysis can affect the preference ordering of management alternatives. Hall and Harvey (2009) similarly considered decision-making under severe uncertainty using robustness-based analysis. More recently, Mik-Meyer et al. (2026) reviewed uncertainty representation and propagation across flood-risk modelling under climate change, showing that uncertainty treatment remains heterogeneous across model chains and applications.

Bayesian methods are also well established in flood-risk and flood-damage modelling. Hierarchical and multilevel approaches have been used to represent spatial and temporal heterogeneity and to quantify uncertainty in flood-loss relationships (Sairam et al., 2019; Mohor et al., 2021; Lv et al., 2021). Bayesian-network approaches have likewise been applied to urban flood-risk assessment and spatial risk evaluation (Wu et al., 2019, 2020). These studies demonstrate the value of probabilistic inference for flood-risk estimation, but their primary quantities of interest are generally hazard, damage, loss, or risk estimates rather than the stability of a fixed-capacity asset-selection decision.

A separate statistical literature addresses ranking and selection under uncertainty. Berger and Deely (1988) developed a Bayesian approach in which posterior probabilities are used to characterise whether alternatives occupy extreme ranks. Henderson and Newton (2016) considered ranking and selection in large populations, using posterior quantities to improve the expected overlap between true and reported sets of highly ranked units. Eckman and Henderson (2022) further demonstrated how posterior quantities such as the probability of good selection and posterior expected opportunity cost can be used to evaluate the quality of a selection decision. Bowen (2022) considered Bayesian ranking and selection under noisy estimates, including settings in which candidates are classified according to membership in an upper fraction of the population.

The gap addressed here lies in connecting these strands. Flood-risk research has extensively studied uncertainty in hazard, loss, model outputs, and decision alternatives, while Bayesian ranking-and-selection research has developed posterior quantities for uncertain rankings and selections. The present study does not claim novelty for probabilistic ranking or selection itself. Instead, the framework propagates posterior risk uncertainty through a fixed-capacity spatial prioritisation rule and treats asset-level top-\(k\) membership as the decision quantity of interest. The emphasis is not on recovering a globally correct ranking or introducing a new ranking algorithm, but on identifying and quantifying the localised set of assets whose prioritisation can change under posterior uncertainty and testing whether that decision-stability structure persists under controlled input perturbation and fixed-specification cross-city stress testing.

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

Robustness to hazard-input perturbation is evaluated using a fixed random subsample of 50,000 Rotterdam assets and a prioritisation capacity of \(k=500\), corresponding to 1% of the experimental sample. The same assets, exposure values, synthetic outcome realisation, prior specification, and inference procedure are retained across all perturbation runs.

For perturbation level \(\sigma\), the transformed hazard representation is modified as

\[
H_{i,r}^{(\sigma)}
=
H_i
+
\sigma s_{H,\mathrm{RTM}} z_{i,r},
\qquad
z_{i,r}\sim\mathcal{N}(0,1),
\]

where \(s_{H,\mathrm{RTM}}\) is the standard deviation of the transformed Rotterdam hazard feature and \(r\) indexes the perturbation realisation. Thus, \(\sigma\) represents perturbation magnitude relative to the observed Rotterdam hazard variability rather than an absolute change in the transformed hazard scale.

The unperturbed condition \(\sigma=0\) is fitted once as the reference case. For each non-zero perturbation level \(\sigma\in\{0.05,0.10,0.20,0.30\}\), 20 perturbation realisations are evaluated. Within each realisation, the same standard-normal perturbation vector is scaled across perturbation levels, producing paired perturbation paths.

The Bayesian model is refitted after every perturbation. Borderline decision instability is defined using \(0.2 < \pi_{i,k} < 0.8\) and reported both relative to the experimental population \(N\) and to prioritisation capacity \(k\). Results across perturbation realisations are summarised using the median and empirical 10th--90th percentiles.

The purpose of the experiment is not to represent physically calibrated hazard uncertainty, but to test whether posterior decision instability remains localised when the hazard representation is progressively perturbed.

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

To characterise cross-city input-distribution differences independently of the resulting decision-stability metrics, the marginal distributions of the exposure and hazard features were compared against the Rotterdam reference. Distribution shift was quantified using the one-dimensional Wasserstein distance. Because the two features operate on different scales, Wasserstein distances were additionally normalised by the corresponding Rotterdam standard deviation. Descriptive statistics, including the median and interquartile range, were retained to make the direction and magnitude of the shifts interpretable.

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

Because exactly \(k\) assets are selected in every posterior draw, top-\(k\) membership probabilities satisfy

\[
\sum_{i=1}^{N} \pi_{i,k} = k.
\]

This identity follows directly from the selection rule: each posterior draw contributes exactly \(k\) selected memberships. Consequently, changing the thresholds used to classify assets as stable or unstable does not change the expected total membership mass; it changes only how that probability mass is partitioned into decision-stability classes.

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

The \(0.2/0.8\) thresholds are used as the primary classification convention rather than treated as model-derived cutoffs. Sensitivity to this convention is evaluated using the wider unstable interval \(0.1 < \pi_{i,k} < 0.9\) and the narrower interval \(0.25 < \pi_{i,k} < 0.75\).

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

## Cross-city input-distribution shift

The fixed-specification cross-city stress test is conducted under materially different input distributions. Table 1 summarises the exposure and transformed hazard distributions for each city together with their Wasserstein distances from the Rotterdam reference.

```{=latex}
\begin{table}[H]
\centering
\small
\begin{tabular}{llrrrr}
\toprule
City & Feature & Median & IQR & $W_1$ vs RTM & $W_1 / s_{\mathrm{RTM}}$ \\
\midrule
RTM & Exposure & -0.085 & [-0.494, 0.400] & -- & -- \\
HAM & Exposure & -0.074 & [-0.670, 0.667] & 0.186 & 0.22 \\
DON & Exposure & 0.532 & [-0.058, 1.447] & 0.738 & 0.89 \\
RTM & Hazard & 0.000 & [-0.005, 0.004] & -- & -- \\
HAM & Hazard & 0.072 & [0.067, 0.077] & 0.072 & 9.49 \\
DON & Hazard & 0.607 & [0.597, 0.617] & 0.605 & 79.94 \\
\bottomrule
\end{tabular}
\caption{Cross-city input-distribution shift relative to Rotterdam. $W_1$ denotes the one-dimensional Wasserstein distance and $W_1 / s_{\mathrm{RTM}}$ the same distance normalised by the Rotterdam standard deviation of the corresponding feature.}
\end{table}
```

Exposure distributions differ relatively little between Rotterdam and Hamburg, with a normalised Wasserstein distance of 0.22, while Donostia--San Sebastián exhibits a larger exposure shift of 0.89. The transformed hazard proxy differs much more strongly across cities: the normalised Wasserstein distance is 9.49 for Hamburg and 79.94 for Donostia--San Sebastián relative to Rotterdam.

The descriptive distributions show the same pattern. Median transformed hazard is approximately 0.000 in Rotterdam, 0.072 in Hamburg, and 0.607 in Donostia--San Sebastián. Donostia--San Sebastián therefore exhibits the strongest shift in both model inputs, particularly in the hazard representation.

These results establish that the cross-city experiment applies a common model specification to materially different input distributions. They do not establish that distribution shift causes the observed differences in posterior decision stability; the input-distribution comparison and the decision-stability analysis are reported as distinct components of the stress test.

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

Repeated perturbation of the transformed hazard representation produces only limited broadening of the posterior decision boundary.

The experiment uses a fixed Rotterdam subsample of \(N=50{,}000\) assets and a prioritisation capacity of \(k=500\), corresponding to 1% of the experimental population. The unperturbed reference contains 10 borderline assets, representing 0.0200% of the sample and 2.00% of prioritisation capacity.

```{=latex}
\begin{table}[H]
\centering
\small
\begin{tabular}{rrrrrr}
\toprule
$\sigma$ & Reps. & Median count & Median / $N$ & P10--P90 / $N$ & Median / $k$ \\
\midrule
0.00 & 1  & 10 & 0.0200\% & --                  & 2.00\% \\
0.05 & 20 & 10 & 0.0200\% & 0.0200--0.0220\%   & 2.00\% \\
0.10 & 20 & 10 & 0.0200\% & 0.0200--0.0240\%   & 2.00\% \\
0.20 & 20 & 11 & 0.0220\% & 0.0200--0.0242\%   & 2.20\% \\
0.30 & 20 & 11 & 0.0220\% & 0.0196--0.0262\%   & 2.20\% \\
\bottomrule
\end{tabular}
\end{table}
```

The median unstable boundary remains unchanged through \(\sigma=0.10\) and increases only slightly at \(\sigma=0.20\) and \(\sigma=0.30\). Variation between individual perturbation realisations increases with perturbation magnitude, but the resulting empirical intervals overlap substantially across all evaluated levels.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/fig06_hazard_perturbation_repeated.pdf}
\caption{Decision instability under repeated hazard perturbation. Thin grey lines show the 20 paired perturbation realisations; the black line shows the median and the shaded band the empirical 10th--90th percentile. The unperturbed case is shown as a single reference fit. \(N=50{,}000\), \(k=500\).}
\end{figure}
```

Figure 6 shows that progressively perturbing the hazard input does not generate diffuse decision instability. Even at the largest evaluated perturbation level, the median unstable set contains only 11 assets, corresponding to 0.0220% of the experimental population and 2.20% of prioritisation capacity.

The individual perturbation paths are not uniformly monotonic, indicating that small changes between adjacent perturbation levels depend partly on the particular perturbation realisation. The repeated experiment therefore does not support interpreting the non-monotonic shape of any single perturbation run as structural behaviour.

Across all 81 Bayesian fits, no divergent transitions were observed. The maximum \(\hat{R}\) was approximately 1.004 and the minimum bulk effective sample size was approximately 1,992, indicating stable posterior sampling across the perturbation experiment.

These perturbation results are specific to the fixed 50,000-asset experimental subsample and should not be interpreted as a replacement for the full-city Rotterdam baseline reported in the cross-city comparison.

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

Table 2 summarises the cross-city decision-stability result. Rotterdam and Hamburg contain approximately 30 and 56 borderline assets respectively, corresponding to 1.36% and 1.64% of their prioritisation capacities. Donostia--San Sebastián contains only 13 borderline assets in absolute terms, but these represent approximately 16.67% of its much smaller prioritisation capacity of 78 assets. The operational interpretation therefore differs substantially from the citywide borderline share alone.

Sensitivity to the decision-stability thresholds was evaluated at the same approximately 1% prioritisation capacities. Table 3 reports borderline assets relative to \(k\) under three alternative threshold conventions.

```{=latex}
\begin{table}[H]
\centering
\small
\begin{tabular}{lrrr}
\toprule
City & $0.10/0.90$ & $0.20/0.80$ & $0.25/0.75$ \\
\midrule
RTM & 48 (2.17\%) & 30 (1.36\%) & 26 (1.17\%) \\
HAM & 91 (2.66\%) & 56 (1.64\%) & 47 (1.38\%) \\
DON & 14 (17.95\%) & 13 (16.67\%) & 8 (10.26\%) \\
\bottomrule
\end{tabular}
\caption{Sensitivity of the unstable boundary to alternative decision-stability thresholds at approximately 1\% prioritisation capacity. Entries report borderline asset count with borderline count as a percentage of \(k\) in parentheses.}
\end{table}
```

As expected, the wider \(0.1/0.9\) interval classifies more assets as unstable and the narrower \(0.25/0.75\) interval classifies fewer. The qualitative cross-city result is unchanged across all three conventions: Rotterdam and Hamburg retain small unstable sets relative to prioritisation capacity, while Donostia--San Sebastián retains a substantially wider unstable boundary. The magnitude of the Donostia boundary is nevertheless sensitive to the classification convention, declining from 17.95\% of \(k\) under \(0.1/0.9\) to 10.26\% under \(0.25/0.75\).

Sensitivity to prioritisation capacity \(k\) is examined separately in Figure 8.

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

The spatial organisation of posterior decision stability is examined for Rotterdam, Hamburg, and Donostia--San Sebastián using citywide posterior top-k membership maps together with local transition-region views.

Across the three study areas, posterior instability is spatially concentrated near relatively narrow transitions between stable high-priority and stable low-priority assets rather than diffused across the urban system. Rotterdam and Hamburg exhibit particularly narrow transition structures, while Donostia--San Sebastián shows greater local broadening under the fixed-specification cross-city stress test.

The full citywide and local transition maps are provided in Appendix B. These maps are presented as methodological diagnostics of posterior-derived decision stability and should not be interpreted as hydraulic validation.

---

# Discussion

This study demonstrates how prioritisation decisions can be analysed as probabilistic objects rather than deterministic rankings. By deriving decision metrics directly from posterior distributions, the proposed framework evaluates not only which assets appear most at risk, but which prioritisation decisions remain reliable under uncertainty.

Across the baseline, repeated hazard-perturbation, and cross-city stress-test experiments, posterior uncertainty remains concentrated near a relatively narrow decision boundary while most assets exhibit stable prioritisation behaviour across posterior draws. These findings suggest that uncertainty propagation does not necessarily imply diffuse decision instability. Instead, uncertainty can remain localised near prioritisation thresholds, allowing most decisions to remain stable despite posterior uncertainty in the fitted model.

The repeated hazard-perturbation experiment shows that the unstable boundary remains highly localised under progressively stronger perturbations of the hazard representation. Median borderline membership changes only from 10 assets in the unperturbed reference to 11 assets at the two highest perturbation levels, although variability between perturbation realisations increases with perturbation magnitude. The experiment therefore supports robustness of the overall decision-stability structure while also showing that the detailed trajectory of any single perturbation realisation should not be interpreted as structurally meaningful. Similarly, the fixed-specification cross-city stress test shows that the overall decision-stability structure persists across the three evaluated urban input distributions, despite substantial independently measured differences in the model inputs, particularly in the transformed hazard proxy. The width of the unstable boundary nevertheless varies across cities, and the present experiment does not isolate distribution shift as its causal mechanism.

These findings should not be interpreted as evidence of predictive generalisation or hydraulic validity across study areas. Rather, the experiments evaluate the structural behaviour of posterior-derived decision metrics under a fixed probabilistic specification. The framework therefore assesses the stability of prioritisation decisions conditional on the assumed model, rather than the empirical correctness of the prioritisation itself.

---

# Limitations

Several limitations should be noted.

- The hazard representation is proxy-based and does not model hydraulic flood dynamics.
- The outcome variable is synthetic and not calibrated against observed damage data.
- Exposure is represented using simplified hydrographic proximity indicators.
- The current framework evaluates a single model family and a single prior specification; systematic prior-sensitivity analysis is left for future work.
- The framework does not currently include latent hazard processes or utility-theoretic decision modelling.

These limitations are central to the interpretation of the results. The analysis does not claim operational flood prediction, hydraulic validation, or empirical correctness of the prioritised assets. It isolates the statistical behaviour of posterior-derived prioritisation metrics under a deliberately simplified model.

No claims of operational deployment or empirical flood prediction are made.

---

# Conclusion

This paper presented a Bayesian framework for analysing posterior decision stability under uncertainty propagation.

Rather than treating rankings as deterministic outputs, the framework derives posterior-based decision quantities that quantify uncertainty in prioritisation membership itself.

Across baseline, repeated hazard-perturbation, and cross-city stress-test experiments, instability remains concentrated near a narrow prioritisation boundary while most assets exhibit stable prioritisation behaviour under posterior uncertainty. Under comparable 1% prioritisation thresholds, unstable boundary shares remain small across all three evaluated cities, although their operational importance relative to prioritisation capacity varies substantially.

The contribution is methodological rather than hydraulic. The results demonstrate how posterior inference can be extended from predictive estimation toward explicit analysis of posterior decision stability under uncertainty.

---

# Code and Data Availability

Code, analysis scripts, and processed model-input tables used in this study are publicly available at
https://github.com/Habnetic/resilient-housing-bayes.

Processed source data and supporting data products are available at
https://github.com/Habnetic/data.

Additional project documentation is available at
https://github.com/Habnetic/docs.

---

# References

Hall, J. W., & Harvey, H. (2009). Decision making under severe uncertainty for flood risk management: A case study of Info-Gap robustness analysis. *Proceedings of the 8th International Conference on Hydroinformatics*, Concepción, Chile.

Hall, J. W., & Solomatine, D. (2008). A framework for uncertainty analysis in flood risk management decisions. *International Journal of River Basin Management*, 6(2), 85–98. https://doi.org/10.1080/15715124.2008.9635339

McMillan, H. K., & Brasington, J. (2008). End-to-end flood risk assessment: A coupled model cascade with uncertainty estimation. *Water Resources Research*, 44, W03419. https://doi.org/10.1029/2007WR005995

Sairam, N., Schröter, K., Rözer, V., Merz, B., & Kreibich, H. (2019). Hierarchical Bayesian Approach for Modeling Spatiotemporal Variability in Flood Damage Processes. *Water Resources Research*, 55, 8223–8237. https://doi.org/10.1029/2019WR025068

Mohor, G. S., Thieken, A. H., & Korup, O. (2021). Residential flood loss estimated from Bayesian multilevel models. *Natural Hazards and Earth System Sciences*, 21, 1599–1614. https://doi.org/10.5194/nhess-21-1599-2021

Lv, H., Wu, Z., Guan, X., & Meng, Y. (2021). The construction of flood loss ratio function in cities lacking loss data based on dynamic proportional substitution and hierarchical Bayesian model. *Journal of Hydrology*, 592, 125797. https://doi.org/10.1016/j.jhydrol.2020.125797

Wu, Z., Shen, Y., Wang, H., & Wu, M. (2019). Assessing urban flood disaster risk using Bayesian network model and GIS applications. *Geomatics, Natural Hazards and Risk*, 10(1), 2163–2184. https://doi.org/10.1080/19475705.2019.1685010

Wu, Z., Shen, Y., Wang, H., & Wu, M. (2020). Urban flood disaster risk evaluation based on ontology and Bayesian Network. *Journal of Hydrology*, 583, 124596. https://doi.org/10.1016/j.jhydrol.2020.124596

Mik-Meyer, V., Doyle, E. E. H., Larsen, M. A. D., Kool, R., & Drews, M. (2026). Uncertainty Representation and Propagation in Flood Risk Modeling Under Climate Change: A Systematic Review. *WIREs Climate Change*, 17(2), e70045. https://doi.org/10.1002/wcc.70045

Berger, J. O., & Deely, J. (1988). A Bayesian Approach to Ranking and Selection of Related Means with Alternatives to Analysis-of-Variance Methodology. *Journal of the American Statistical Association*, 83(402), 364–373. https://doi.org/10.1080/01621459.1988.10478606

Henderson, N. C., & Newton, M. A. (2016). Making the Cut: Improved Ranking and Selection for Large-Scale Inference. *Journal of the Royal Statistical Society: Series B*, 78(4), 781–804. https://doi.org/10.1111/rssb.12131

Eckman, D. J., & Henderson, S. G. (2022). Posterior-Based Stopping Rules for Bayesian Ranking-and-Selection Procedures. *INFORMS Journal on Computing*, 34(3), 1711–1728. https://doi.org/10.1287/ijoc.2021.1132

Bowen, D. (2022). Bayesian ranking and selection with applications to field studies, economic mobility, and forecasting. *arXiv preprint arXiv:2208.02038*.

---

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
Building ID & Exposure ($E$) & Hazard mm & Hazard log-rel & Synthetic outcome ($Y$) \\
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

```{=latex}
\clearpage
```

# Appendix B. Spatial decision-stability maps

The following figures provide the full spatial diagnostics for the three study areas. For each city, the upper panel shows citywide posterior top-\(k\) membership probability and the lower panel shows a local transition region near the prioritisation boundary.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.78\textwidth]{figures/RTM_paper_citywide_topk_map.pdf}

\vspace{0.4em}

\includegraphics[width=0.68\textwidth]{figures/RTM_paper_boundary_zoom_map.pdf}

\caption{Spatial structure of posterior top-\(k\) membership probability and local transition region for Rotterdam. The citywide map shows posterior top-\(k\) membership probability; the zoom map shows the local transition region near the prioritisation boundary.}
\end{figure}

\begin{figure}[p]
\centering
\includegraphics[width=0.78\textwidth]{figures/HAM_paper_citywide_topk_map.pdf}

\vspace{0.4em}

\includegraphics[width=0.68\textwidth]{figures/HAM_paper_boundary_zoom_map.pdf}

\caption{Spatial structure of posterior top-\(k\) membership probability and local transition region for Hamburg. The citywide map shows posterior top-\(k\) membership probability; the zoom map shows the local transition region near the prioritisation boundary.}
\end{figure}

\begin{figure}[p]
\centering
\includegraphics[width=0.78\textwidth]{figures/DON_paper_citywide_topk_map.pdf}

\vspace{0.4em}

\includegraphics[width=0.68\textwidth]{figures/DON_paper_boundary_zoom_map.pdf}

\caption{Spatial structure of posterior top-\(k\) membership probability and local transition region for Donostia--San Sebastián. The citywide map shows posterior top-\(k\) membership probability; the zoom map shows the local transition region near the prioritisation boundary.}
\end{figure}

\clearpage
```