---
title: Posterior-Based Decision Stability under Cross-City Stress Testing in Urban Flood Risk Prioritisation
author: |
  \begin{tabular}{c}
  Mikel Martínez Mugica \\
  {\small Independent Researcher} \\
  {\small \href{https://habnetic.org}{https://habnetic.org}} \\
  {\small Correspondence: \href{mailto:mikel.knowledge@gmail.com}{mikel.knowledge@gmail.com}} \\
  {\small ORCID: \href{https://orcid.org/0009-0006-5170-4405}{https://orcid.org/0009-0006-5170-4405}} \\
  {\small DOI: \href{https://doi.org/10.5281/zenodo.23599344}{https://doi.org/10.5281/zenodo.23599344}} \\[0.7em]
  {\small\itshape This manuscript is a non-peer-reviewed preprint.}
  \end{tabular}
date: October 2026

---

# Abstract

Urban risk prioritisation often requires selecting a small subset of assets for inspection or intervention under a fixed capacity constraint. Uncertainty in estimated asset-level risk is distinct from uncertainty in the resulting discrete selection decision.

Building on established concepts in Bayesian ranking and selection and stochastic multicriteria acceptability analysis, this study implements posterior top-\(k\) membership probabilities as diagnostics of selection uncertainty in spatial asset prioritisation. Posterior draws are propagated through a ranking and fixed-capacity selection rule to distinguish assets consistently included, consistently excluded, and assigned intermediate membership probabilities.

The approach is examined with a deliberately simplified Bayesian logistic model fitted to synthetic binary outcomes generated from exposure and pluvial hazard proxies. Experiments comprise a Rotterdam baseline, repeated perturbations of the hazard proxy, and fixed-specification stress tests in independently fitted models for Hamburg and Donostia--San Sebastián.

At approximately 1% prioritisation capacity, the intermediate-probability boundary comprises 0.0136%, 0.0164%, and 0.1676% of the asset populations in Rotterdam, Hamburg, and Donostia--San Sebastián, respectively. Relative to selection capacity, these correspond to 1.36%, 1.64%, and 16.67%.

Under the evaluated synthetic specification, intermediate posterior membership probabilities occur in a comparatively small subset of assets. These conditional results do not establish predictive transfer between cities, empirical flood-risk validity, stability of asset identities across perturbation scenarios, or decision optimality.

*Revision note (v1.1, October 2026): This corrected preprint updates Figure 2 and its prior interpretation, the realised synthetic event rates, the spatial-map selection capacities and explanatory captions, and the positioning of the work relative to existing probabilistic ranking and SMAA literature. The main top-\(k\) borderline counts and Tables 2 and 3 remain unchanged.*

---

# Introduction

Urban resilience planning often requires choosing a limited number of assets for inspection, intervention, or further investigation. A municipality may face tens or hundreds of thousands of buildings while having the capacity to act on only a small subset. Under these conditions, risk estimation ultimately becomes a selection problem: which assets should fall inside the prioritised set?

Risk estimates are uncertain. Hazard representation, exposure measurement, model specification, and limited observational data can all introduce uncertainty into estimated asset-level risk. However, uncertainty in risk estimates does not necessarily imply uncertainty in the resulting prioritisation decision. Two assets may have uncertain risk estimates while nevertheless remaining consistently on opposite sides of the selection boundary.

The operational question is therefore not only:

> Which assets appear most at risk?

but also:

> Which assets remain prioritised once uncertainty is propagated through the decision rule?

For a prioritisation capacity of \(k\) assets, each posterior draw induces a ranking and therefore a top-\(k\) selected set. Repeating this process across posterior draws produces a posterior distribution over prioritisation membership. Assets can then be interpreted as robustly inside the selected set, robustly outside it, or part of an uncertain boundary where additional evidence or judgement may affect the decision.

This distinction may help identify assets whose inclusion in a proposed fixed-capacity selection is sensitive to posterior parameter uncertainty. A small intermediate-probability set is a descriptive finding about the chosen model and selection rule, not evidence that the resulting decisions are correct or optimal.

This paper implements established probabilistic ranking-and-selection ideas for fixed-capacity spatial risk prioritisation. The primary inferential quantity is the top-\(k\) membership probability: the posterior probability that an asset belongs to the selected priority set under the fitted model. The analysis uses this quantity to describe selection membership and measure the size of an intermediate-probability boundary.

The objective is methodological rather than predictive. The empirical implementation uses simplified exposure and pluvial hazard proxies together with a synthetic binary outcome in order to isolate the behaviour of the inference-to-decision pipeline. It does not claim hydraulic validation or operational flood prediction.

The framework is evaluated in three stages: a Rotterdam baseline, controlled hazard perturbation experiments, and a fixed-specification cross-city stress test in Hamburg and Donostia--San Sebastián. The experiments examine whether posterior decision instability remains localised near the prioritisation boundary under hazard perturbation and across different urban input distributions.

## Contributions

Building on existing work in probabilistic ranking, Bayesian selection, and stochastic decision analysis, this study makes four implementation and experimental contributions:

- An implementation of posterior top-\(k\) membership analysis for fixed-capacity urban asset prioritisation, propagating fitted-model parameter uncertainty through ranking and selection.
- A characterisation of intermediate-probability membership boundaries under alternative classification thresholds, reporting their size relative to the asset population and to selection capacity.
- A repeated hazard-proxy perturbation experiment describing how the **size** of the uncertain membership boundary changes under controlled modifications of one model input.
- A fixed-specification cross-city stress test describing the behaviour of the same decision-analysis procedure under three different urban input distributions, with independent model fitting in each city.

These contributions concern a particular implementation and experimental evaluation of established probabilistic decision quantities. They do not establish a new general ranking-and-selection method or predictive transferability between cities.

---

# Related Work

Uncertainty representation and propagation are longstanding concerns in flood-risk modelling. McMillan and Brasington (2008) propagated uncertainty through an end-to-end flood-risk model cascade, illustrating how uncertainty introduced at different stages can affect downstream risk estimates. Hall and Solomatine (2008) extended the problem explicitly into the decision domain, examining how uncertainty in flood-risk analysis can affect the preference ordering of management alternatives. Hall and Harvey (2009) similarly considered decision-making under severe uncertainty using robustness-based analysis. More recently, Mik-Meyer et al. (2026) reviewed uncertainty representation and propagation across flood-risk modelling under climate change, showing that uncertainty treatment remains heterogeneous across model chains and applications.

Bayesian methods are also well established in flood-risk and flood-damage modelling. Hierarchical and multilevel approaches have been used to represent spatial and temporal heterogeneity and to quantify uncertainty in flood-loss relationships (Sairam et al., 2019; Mohor et al., 2021; Lv et al., 2021). Bayesian-network approaches have likewise been applied to urban flood-risk assessment and spatial risk evaluation (Wu et al., 2019, 2020). These studies demonstrate the value of probabilistic inference for flood-risk estimation, but their primary quantities of interest are generally hazard, damage, loss, or risk estimates rather than the stability of a fixed-capacity asset-selection decision.

An important methodological precursor is stochastic multicriteria acceptability analysis (SMAA), introduced by Lahdelma et al. (1998) and extended to rank acceptability in SMAA-2 by Lahdelma and Salminen (2001). SMAA characterises the acceptability of alternatives under uncertain criterion evaluations and preference information; SMAA-2 includes probabilities associated with ranking positions (Tervonen and Figueira, 2008). For any probabilistic ranking, the probability of membership in the first \(k\) positions can be expressed as the sum of the probabilities assigned to those positions. This is closely related to posterior top-\(k\) membership, although the uncertainty distributions and decision models need not coincide.

Bayesian inference has also been combined directly with SMAA and related multicriteria ranking approaches. Li et al. (2018) combined Bayesian meta-analysis with SMAA for benefit--risk assessment, and Li et al. (2019) combined a Bayesian individual-level model, MCMC, and SMAA. Ru et al. (2022) used a posterior distribution over preference models for multicriteria choice and ranking. These precedents show that propagating posterior uncertainty into probabilistic ranking or acceptability measures is not new in itself. The present work applies related ideas to a fixed-capacity **spatial asset-selection** setting and evaluates a particular synthetic hazard-proxy perturbation and cross-city design. It does not implement classical SMAA preference-weight analysis.

A separate statistical literature addresses ranking and selection under uncertainty. Berger and Deely (1988) developed a Bayesian approach in which posterior probabilities are used to characterise whether alternatives occupy extreme ranks. Henderson and Newton (2016) considered ranking and selection in large populations, using posterior quantities to improve the expected overlap between true and reported sets of highly ranked units. Eckman and Henderson (2022) further demonstrated how posterior quantities such as the probability of good selection and posterior expected opportunity cost can be used to evaluate the quality of a selection decision. Bowen (2022) considered Bayesian ranking and selection under noisy estimates, including settings in which candidates are classified according to membership in an upper fraction of the population.

An earlier Phase 1 research manuscript (Martínez Mugica, 2026) documented an initial implementation of posterior top-\(k\) membership probabilities and a borderline selection region within the Habnetic project. The present study extends that implementation through repeated hazard-proxy perturbations, sensitivity analysis of classification thresholds, and fixed-specification cross-city stress testing. The earlier manuscript documents the project's development rather than establishing priority over the broader ranking-and-selection or stochastic acceptability literature.

Subsequent work by Xu et al. (2026) also uses posterior top-\(k\) membership probabilities in a Bayesian ranking setting. Their work addresses top-\(k\) identification from noisy and potentially biased pairwise comparisons and uses posterior membership uncertainty within a top-\(k\)-aware active comparison-acquisition framework. Although the posterior membership quantity is closely related, the inferential and operational objectives differ: the present framework uses posterior top-\(k\) membership to characterise the stability of fixed-capacity spatial risk decisions under fitted-model uncertainty, controlled input perturbation, and fixed-specification cross-city stress testing.

The present study is positioned as an application and experimental evaluation of established probabilistic ranking and selection concepts in a fixed-capacity spatial risk-prioritisation setting. Its contribution is the documented behaviour of posterior-derived selection membership under a shared synthetic model specification, controlled hazard-proxy perturbations, and independently fitted cross-city stress tests. The analysis reports intermediate-probability boundary sizes relative both to inventory size and to selection capacity.

No novelty is claimed for posterior rank probabilities, top-\(k\) membership probabilities, or stochastic acceptability analysis as general mathematical constructions. The experiments do not establish predictive transferability, empirical flood-risk accuracy, invariance of selected asset identities under refitting, or decision optimality.

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

Under this mechanism, the realised synthetic event rates in the model-input datasets used for inference are approximately 6.30% in Rotterdam, 7.72% in Hamburg, and 19.73% in Donostia--San Sebastián. These rates describe synthetic outcomes, not observed flood damage.

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

Independent Gaussian priors, centred at zero on the log-odds scale, are assigned to the intercept and regression coefficients:

$$
\alpha, \beta_E, \beta_H \sim \mathcal{N}(0, 2.5)
$$

where 2.5 denotes the prior standard deviation. These priors permit large positive and negative log-odds effects and are not calibrated to an expected low event prevalence. In particular, the zero-centred symmetric prior for the intercept implies a prior mean event probability of 0.5, when averaged over the symmetric prior on all coefficients for any fixed predictor values.

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

Prior predictive diagnostics were evaluated using the same baseline priors as those used for inference: independent \(\mathcal{N}(0,2.5)\) priors for the intercept and both coefficients. For each city, the displayed distribution was obtained from 1,000 draws of the prior coefficients and the corresponding mean Bernoulli event probability across a fixed subsample of up to 50,000 assets. The figure reports the median and 5th--95th percentile interval of these **prior-implied expected event fractions**; it does not depict empirical damage probabilities or posterior predictive intervals.

![Prior-implied mean event fraction under the baseline Gaussian priors. Markers indicate the realised synthetic event fractions; intervals show the 5th--95th percentiles across prior draws, with the median indicated separately.](figures/fig_prior_predictive_event_rate.pdf)

Figure 2 shows that the baseline priors imply very broad citywide event-fraction distributions with simulated medians near 0.52, rather than concentrating prior mass around the lower observed synthetic fractions (6.30%, 7.72%, and 19.73%). With the symmetric zero-centred prior specification, the prior **mean** event probability is 0.5 for each asset; the realised Monte Carlo median of a citywide mean need not be exactly 0.5. Consequently, the prior predictive check reveals a limitation of the baseline prior specification rather than ruling out implausibly high event fractions. Posterior predictive checks are internal diagnostics under the synthetic Bernoulli model and do not constitute empirical flood validation.

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

This quantity is the proportion of evaluated assets whose posterior top-\(k\) membership probabilities fall within the specified intermediate interval. It is conditional on the fitted model, selection capacity, and classification thresholds. It does not measure predictive accuracy, expected decision loss, or whether the selected assets are appropriate. Reporting the borderline count relative to selection capacity \(k\) additionally helps contextualise its potential practical significance. Because \(k\ll N\) in the main experiments, most assets have small membership probabilities by construction; a small borderline fraction relative to \(N\) should not on its own be interpreted as evidence of decision quality.

---

# Results

## Baseline risk concentration and decision-stability structure

Figure 3 shows a descriptive plug-in risk ranking for Rotterdam, calculated by applying the logistic link to the **posterior mean coefficients**. This approximation is not algebraically identical to the posterior mean asset-level risk defined above, which averages logistic probabilities across posterior draws. The plot is a descriptive diagnostic; posterior top-\(k\) membership throughout the main analysis is computed directly from the ranking within each posterior draw.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/phase3_expected_risk_ranking_RTM.pdf}
\caption{Plug-in risk ranking for Rotterdam based on the logistic transform of posterior mean coefficients. This descriptive ranking approximates, but is not identical to, the posterior mean asset-level probability.}
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

At comparable thresholds, the intermediate-probability boundary comprises a small share of each entire asset population. However, the same 13 borderline assets in Donostia--San Sebastián amount to 16.67% of its prioritisation capacity, compared with 1.36% in Rotterdam and 1.64% in Hamburg. The citywide percentage alone therefore understates the boundary's importance relative to the number of selected assets.

## Sensitivity to hazard-proxy perturbation

Repeated perturbation of the transformed hazard proxy produces only limited changes in the **count** of assets with intermediate posterior top-\(k\) membership probability under the evaluated design.

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
\caption{Size of the posterior-uncertain membership boundary under repeated hazard-proxy perturbation. Thin grey lines represent 20 paired perturbation paths at non-zero levels; the black line is the median and the shaded band the empirical 10th--90th percentile across replicates. The unperturbed condition has one reference fit. Counts are relative to \(N=50{,}000\), with \(k=500\); identity stability across perturbations is not assessed.}
\end{figure}
```

Figure 6 shows that the median **size** of the posterior-uncertain membership boundary changes little across the evaluated perturbation magnitudes. At the largest level, the median borderline count is 11 assets, corresponding to 0.0220% of the experimental population and 2.20% of selection capacity. Similar counts do not establish that the same assets remain selected or borderline across independently refitted perturbation scenarios: selected-set overlap and asset-level membership changes were not evaluated directly.

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

The plotted posterior membership probabilities concentrate near zero or one for many assets under the common synthetic specification, with intermediate probabilities localised in a smaller subset. At approximately 1% selection capacity, Donostia--San Sebastián has a wider intermediate-probability boundary relative to \(k\) than Rotterdam and Hamburg. This describes posterior membership uncertainty, not empirical accuracy.

Figure 8 shows how borderline share evolves across prioritisation thresholds for all three cities.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.82\textwidth]{figures/phase3_borderline_share_vs_k.pdf}
\caption{Borderline share as a function of the prioritisation threshold.}
\end{figure}
```

Across the plotted selection thresholds, the intermediate-probability share is generally small relative to the full asset population. Its operational interpretation depends on selection capacity as well as on \(N\); the figure does not test changes in the identity of selected assets between alternative capacity choices.

## Spatial structure of posterior decision stability

Appendix B displays citywide posterior top-\(k\) membership maps together with local diagnostic views for each study area. The revised maps use the same approximately 1% selection capacities as Table 2: \(k=2{,}213\) for Rotterdam, \(k=3{,}415\) for Hamburg, and \(k=78\) for Donostia--San Sebastián. Buildings with \(0.2<\pi_{i,k}<0.8\) are outlined to visualise intermediate-probability membership.

The maps locate model-conditional membership patterns in space; they are not hydraulic hazard maps or independent evidence of spatial predictive accuracy. The local views are illustrative excerpts selected for legibility, not representative random samples of all borderline buildings. In particular, the revised Donostia--San Sebastián local view contains four of the city's 13 borderline buildings under \(k=78\); the remaining borderline buildings lie outside that excerpt. The displayed probability colour scale emphasises the \(0.2\) to \(1.0\) range and does not resolve differences below \(0.2\).

---

# Discussion

The experiments illustrate how established posterior ranking-and-selection quantities can describe uncertainty in fixed-capacity spatial asset prioritisation. Under the fitted synthetic logistic specification, many assets have posterior top-\(k\) membership probabilities close to zero or one, while intermediate values occur in a comparatively small subset. This is a conditional property of the tested data-generating mechanism, model, covariate distributions, sample sizes, and selected capacities, not an established general property of uncertain spatial prioritisation systems.

The hazard-proxy perturbation experiment indicates limited changes in the **size** of the intermediate-probability boundary over the evaluated perturbations. It does not show that the same buildings remain selected or uncertain after each refit. Likewise, the cross-city stress test shows that the same modelling specification and decision-analysis procedure yield concentrated posterior membership structures when fitted independently to three different urban input distributions. No posterior parameters or predictive information are transferred between cities, and the observed distribution shifts have not been established as causal drivers of differences in membership uncertainty.

Several features of the experiment may help explain the concentration of posterior membership probabilities. The inference model shares its functional form with the synthetic data-generating process, has only three fitted coefficients, and is trained on large asset populations in Rotterdam and Hamburg. Moreover, the relative contributions of exposure and hazard to the ranking have not been isolated in the experiments reported here. With approximately 1% selection capacity, most assets must have small membership probabilities in aggregate, although this constraint alone does not determine the number of intermediate-probability assets. High posterior membership concentration should therefore not be equated with resistance to model misspecification, measurement error, alternative hazard representations, or inappropriate exposure assumptions. Without empirical outcome validation and a specified loss or utility function, this analysis does not assess the practical value or optimality of the selected sets.

---

# Limitations

Several limitations are central to the interpretation of these results:

- The hazard representation is proxy-based and does not represent hydraulic flood dynamics; exposure is based on simplified hydrographic proximity indicators.
- The outcome is synthetic and has not been calibrated or validated against observed flood damage.
- The synthetic data-generating mechanism and fitted logistic model share the same functional form. The experiments do not test substantial model misspecification or alternative outcome-generating processes.
- The model is low-dimensional, and two study areas have large sample sizes. Posterior concentration under these conditions does not establish equally concentrated decisions under greater parameter uncertainty, spatial dependence, or more complex models.
- Only one prior specification is used in the fitted models. The baseline zero-centred priors imply a prior mean event probability of 0.5 and permit very high aggregate event fractions; sensitivity to alternative, prevalence-informed priors has not been evaluated.
- The hazard-proxy perturbation experiment measures variation in the **number** of intermediate-probability assets, not selected-set overlap or changes in the identities of selected or borderline buildings between refits.
- The relative effects of exposure and hazard on selected-set composition have not been isolated through component ablations within the experiments reported here.
- Classification thresholds of 0.2 and 0.8 are conventions, not model-derived decision boundaries; selection capacity and the small selection fraction affect how the resulting shares should be interpreted.
- The model does not include latent physical hazard processes, an explicit decision-loss or utility function, or empirical assessment of selected-set quality. Cross-city fitting uses a common specification but does not test predictive transfer of fitted parameters.

The study characterises posterior membership uncertainty under a controlled specification. It does not claim operational flood prediction, hydraulic validation, invariance of selected assets across experiments, or empirical decision optimality.

---

# Conclusion

This study implemented established posterior ranking-and-selection concepts to examine fixed-capacity spatial asset prioritisation under a simplified Bayesian model. Posterior top-\(k\) membership probabilities were used to characterise uncertainty in the inclusion of individual buildings, and the resulting intermediate-probability boundary was examined through a Rotterdam baseline, repeated hazard-proxy perturbations, and independently fitted cross-city stress tests.

Under the evaluated synthetic specification, intermediate membership probabilities were concentrated within a small proportion of the total asset population. Their significance relative to selection capacity was nevertheless greater in Donostia--San Sebastián, where 13 borderline assets represented 16.67% of \(k=78\). The perturbation experiment established limited changes in boundary counts, not invariance of selected asset identities.

The study demonstrates the feasibility of applying these diagnostics to large spatial inventories. It does not establish a new general probabilistic ranking method, predictive transferability between cities, empirical flood-risk validity, or optimal decisions. Future work should evaluate alternative priors and likelihoods, contributions of individual model inputs, selected-set overlap across perturbations, and performance under observed outcomes and explicit decision consequences.

---

# Code and Data Availability

Code, analysis scripts, and processed model-input tables used in this study are publicly available at
https://github.com/Habnetic/resilient-housing-bayes.

Processed source data and supporting data products are available at
https://github.com/Habnetic/data.

Additional project documentation is available at
https://github.com/Habnetic/docs.

Earlier research manuscripts documenting the development of the framework are available at
https://github.com/Habnetic/habnetic-papers.

A subsequent Phase 4 M0 reproducibility audit re-executed the Rotterdam baseline using code commit `5e19254`. The audit reproduced the 221,324-building dataset and achieved 100% overlap with the original top-\(k\) selection at \(k=2{,}213\). Minor differences remained in posterior parameter estimates and the stability classifications of two assets. The audit is documented in the Habnetic documentation repository (commit `2ec70fb`). It did not constitute a complete end-to-end reproduction of the Phase 2 perturbation experiments or the independent Hamburg and Donostia--San Sebastián fits.

The repositories referenced above are evolving resources rather than a complete frozen archival snapshot. Although the Rotterdam reproducibility audit identifies a specific code revision, a fully versioned release of all experimental inputs and associated checksums would be required to support byte-for-byte reproduction of every experiment.

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

Lahdelma, R., Hokkanen, J., & Salminen, P. (1998). SMAA - Stochastic multiobjective acceptability analysis. *European Journal of Operational Research*, 106(1), 137–143. https://doi.org/10.1016/S0377-2217(97)00163-X

Lahdelma, R., & Salminen, P. (2001). SMAA-2: Stochastic multicriteria acceptability analysis for group decision making. *Operations Research*, 49(3), 444–454. https://doi.org/10.1287/opre.49.3.444.11220

Tervonen, T., & Figueira, J. R. (2008). A survey on stochastic multicriteria acceptability analysis methods. *Journal of Multi-Criteria Decision Analysis*, 15(1–2), 1–14. https://doi.org/10.1002/mcda.407

Li, K., Yuan, S. S., Wang, W., Wan, S. S., Ceesay, P., Heyse, J. F., Mt-Isa, S., & Luo, S. (2018). Periodic benefit-risk assessment using Bayesian stochastic multi-criteria acceptability analysis. *Contemporary Clinical Trials*, 67, 100–108. https://doi.org/10.1016/j.cct.2018.02.016

Li, K., Luo, S., Yuan, S., & Mt-Isa, S. (2019). A Bayesian approach for individual-level drug benefit-risk assessment. *Statistics in Medicine*, 38(16), 3040–3052. https://doi.org/10.1002/sim.8166

Ru, Z., Liu, J., Kadziński, M., & Liao, X. (2022). Bayesian ordinal regression for multiple criteria choice and ranking. *European Journal of Operational Research*, 299(2), 600–620. https://doi.org/10.1016/j.ejor.2021.09.028

Martínez Mugica, M. (2026, March). Posterior-Based Decision Stability in Urban Flood Risk Prioritisation. Phase 1 research manuscript, Habnetic. [Available online](https://github.com/Habnetic/habnetic-papers/blob/master/phase1_rtm_decision_stability/phase1_rtm_decision_stability.pdf)

Xu, J., Zeng, D., Paisley, J., & Zhao, Q. (2026). When Can You Debias an LLM Judge? Identifiability Limits, a Test, and Designs for Top-k Ranking. *arXiv preprint arXiv:2607.02104*. https://doi.org/10.48550/arXiv.2607.02104

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

The following figures show spatial diagnostics for the three study areas at the approximately 1% prioritisation thresholds used in Table 2: Rotterdam \(k=2{,}213\), Hamburg \(k=3{,}415\), and Donostia--San Sebastián \(k=78\). The upper panel of each figure shows citywide posterior top-\(k\) membership probabilities and the lower panel a selected local view. Outlined buildings have intermediate membership probability \(0.2<\pi_{i,k}<0.8\). The probability colour scale emphasises \(0.2\) to \(1.0\); areas below \(0.2\) are compressed into the low end of the scale. Local views illustrate particular areas rather than the full spatial distribution of all borderline assets.

```{=latex}
\begin{figure}[H]
\centering
\includegraphics[width=0.78\textwidth]{figures/RTM_paper_citywide_topk_map.pdf}

\vspace{0.4em}

\includegraphics[width=0.68\textwidth]{figures/RTM_paper_boundary_zoom_map.pdf}

\caption{Rotterdam, \(N=221{,}324\), \(k=2{,}213\) (approximately 1\% selection capacity). Citywide posterior top-\(k\) membership probabilities (upper panel) and illustrative local transition region (lower panel). Outlined buildings satisfy \(0.2<\pi_{i,k}<0.8\). These are model-conditional selection diagnostics, not flood-hazard validation.}
\end{figure}

\begin{figure}[p]
\centering
\includegraphics[width=0.78\textwidth]{figures/HAM_paper_citywide_topk_map.pdf}

\vspace{0.4em}

\includegraphics[width=0.68\textwidth]{figures/HAM_paper_boundary_zoom_map.pdf}

\caption{Hamburg, \(N=341{,}530\), \(k=3{,}415\) (1\% selection capacity). Citywide posterior top-\(k\) membership probabilities (upper panel) and illustrative local transition region (lower panel). Outlined buildings satisfy \(0.2<\pi_{i,k}<0.8\). These are model-conditional selection diagnostics, not flood-hazard validation.}
\end{figure}

\begin{figure}[p]
\centering
\includegraphics[width=0.78\textwidth]{figures/DON_paper_citywide_topk_map.pdf}

\vspace{0.4em}

\includegraphics[width=0.68\textwidth]{figures/DON_paper_boundary_zoom_map.pdf}

\caption{Donostia--San Sebastián, \(N=7{,}755\), \(k=78\) (approximately 1\% selection capacity). Citywide posterior top-\(k\) membership probabilities (upper panel) and an illustrative local view (lower panel), containing four of the city's 13 borderline buildings. Outlined buildings satisfy \(0.2<\pi_{i,k}<0.8\). These are model-conditional selection diagnostics, not flood-hazard validation.}
\end{figure}

\clearpage
```