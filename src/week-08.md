# Week 8: Heterogeneous Treatment Effects and Machine Learning

**Date: 29 Apr 2026**

```admonish note title="Readings"
**Required**
- [grf: Generalised Random Forests](https://grf-labs.github.io/grf/)

**Optional**
- VanderWeele et al. (2020), "Outcome-Wide Longitudinal Designs for Causal Inference"
- Suzuki et al. (2020), "Causal Diagrams"
- Bulbulia (2024), "A Practical Guide to Causal Inference with Time-Series Cross-Sectional Data"
- Hoffman et al. (2023), "Introducing suringer"
```

```admonish warning title="Key Concepts"
The workflow below introduces **heterogeneous-treatment-effect (HTE) analysis** with *causal forests*. By the end of the lecture you should understand six technical ideas:

1. ATE (lectures 1--5)
2. CATE (lecture 6)
3. The estimator $\widehat{\tau}(x)$ (lecture 6)
4. The RATE statistics drawn from a **Targeting-Operator Characteristic** (TOC) curve (new)
5. Qini curves (new)
6. **Policy trees**, and how each fits into an applied research pipeline (new)
```

## Why worry about heterogeneity?

Relying on the average treatment effect (ATE) is a bit like handing out size-nine shoes to an entire student body: on *average* they might fit, but watch the tall students hobble and the small ones trip.

Today we focus on the causal question: "What would be the effects on multi-dimensional well-being if everyone spent at least one hour a week socialising with their community?"

A one-hour boost in weekly community socialising could send some students' sense of belonging soaring while leaving others unmoved. Spotting that spread, measuring how large it really is, and deciding whether it is worth tailoring an exposure to individual "shoe sizes" are the three practical goals of heterogeneous treatment effects analysis.

---

## 1. Start with estimating the average treatment effect (ATE)

Assume the three fundamental assumptions of causal inference (consistency, exchangeability, positivity) are met. Suppose we wish to estimate the average treatment effect for socialising with one's community.

We begin with the most straightforward (and secretly impossible) counterfactual: *run two parallel universes, one where **everyone** gets the treatment, another where **no one** does, and compare the final scores*. The resulting difference is the **average treatment effect**:

$$
\text{ATE} = E\!\bigl[Y(1) - Y(0)\bigr].
$$

This gives us the average response. You have seen this before.

---

## 2. Do effects differ across people?

Variation is captured by the **conditional average treatment effect (CATE)**,

$$
\tau(x) = E\!\bigl[Y(1) - Y(0) \mid X = x\bigr],
$$

where $X$ gathers pre-treatment covariates: age, baseline wellbeing, personality, and whatever else we have measured and included in our model. Normally these will be our *baseline confounders*.

If $\tau(x)$ turns out to be flat, we say there is no evidence for heterogeneity worth targeting.

People differ in countless, overlapping ways. Think of age, baseline wellbeing, personality traits, study habits, and more. A linear interaction model tests whether the treatment works differently along one straight dimension, such as gender, by fitting a straight line. But real-world data often twist and turn. If the true relationship bends like a garden hose, a straight line will miss the curve.

Regression forests fix this by letting the data place splits wherever the shape changes, so they can follow any bends that appear (Wager and Athey, 2018). Straight-line models are fine for simple patterns, but regression forests can trace the curves that simple lines overlook.

Causal forests are based on regression forests, where the splitting attempts to maximise differences in causal effect estimates. What this means will soon be clear.

---

## 3. From straight lines to trees

Traditional "parametric" models (like simple regression) guess a single functional shape, often a straight line, before seeing the data. A **non-parametric** model, by contrast, lets the data decide the shape. A *regression tree* is the simplest non-parametric learner we will use.

### Regression tree

The idea is to split the covariate space by asking yes/no questions ("Age $\le$ 20?", "Baseline wellbeing $> 0.3$?") until each terminal **leaf** is fairly homogeneous. Inside a leaf the predicted outcome is just the sample mean, so the tree builds a *piecewise-constant* surface instead of a global line. Think of tiling a garden with stepping stones: each stone is flat, but taken together they follow the ground's contours.

### Regression forest

A single tree is quick and interpretable but unstable: small changes in the data can move the splits and shift predictions. A **random forest** grows many trees on bootstrap samples and averages their outputs. Averaging cancels much of the noise (Breiman, 2001).

### Causal forest

To estimate treatment effects rather than outcomes, each tree plays a two-step "honest" game (Wager and Athey, 2018). First, one half of its sample is used to choose splits that separate treated from control units. Second, the other half is used to compute treatment-control differences within every leaf.

For a new individual with covariates $x_i$, each tree supplies a noisy leaf-level effect; the forest reports the **average**, written

$$
\widehat{\tau}(x) = E[Y(1) - Y(0) \mid X = x].
$$

Because the noisy estimates point in many directions, their average is markedly less variable. The wisdom of trees is a wisdom of crowds.

Straight-line models suit simple patterns; regression forests flex to any bends; causal forests add a third dimension, namely variation in treatment responses.

For more about causal forests, see the lecture by Susan Athey (starting around 18 minutes in): [https://www.youtube.com/watch?v=YBbnCDRCcAI](https://www.youtube.com/watch?v=YBbnCDRCcAI)

---

## 4. Building honest trees: avoiding overfitting

Sample splitting means partitioning your data into training and testing sets. This avoids overfitting the model to observations. Remember, we seek to estimate parameters for an entire population under two different exposures, at most only one of which is observed on any individual. Sample splitting is a feature of estimation in causal forests: we separate model selection from estimation.

Moreover, the forest adds a second safeguard: **out-of-bag (OOB) prediction**. Each $\widehat{\tau}(x_i)$ is averaged only over trees that never used $i$ in their split phase. Together, honesty and OOB prediction deliver reliable uncertainty estimates even in high-dimensional settings (i.e. settings with *many* covariates).

---

## 5. Handling missing data

The `grf` package adopts **Missing Incorporated in Attributes (MIA)** splitting. "Missing" can itself become a branch, so cases are neither discarded nor randomly imputed. This pragmatic approach keeps all observations in play while preserving the forest's interpretability.

---

## 6. Is the heterogeneity actionable? RATE statistics

Once we have a personalised score $\widehat{\tau}(x)$ for every unit, the practical question is whether *targeting* high scorers delivers a benefit large enough to justify the extra effort. The tool of choice is the **Targeting-Operator Characteristic (TOC)** curve:

$$
G(q) = \frac{1}{n}\sum_{i=1}^{\lfloor qn\rfloor}\widehat{\tau}_{(i)}, \qquad 0 \le q \le 1,
$$

where $\widehat{\tau}_{(1)} \ge \widehat{\tau}_{(2)} \ge \cdots$ are the estimated effects sorted from largest to smallest. The horizontal axis $q$ is the fraction of the population we would treat; the vertical axis $G(q)$ is the cumulative gain we expect from treating that top slice.

Two integrals of the TOC curve summarise how lucrative targeting could be.

**RATE AUTOC** (Area Under the TOC) puts equal weight on every $q$. This answers: if benefits are concentrated among the very best prospects, how much can we harvest by cherry-picking them?

**RATE Qini** applies heavier weight to the mid-range of $q$. This is the go-to metric when investigators face a fixed, moderate-sized budget, say, "we can afford to treat 40% of individuals; will targeting help?" (Yadlowsky et al., 2021). We will evaluate the curve at treatment of 20% and 50% of the population.

To quantify the economic or policy value of heterogeneity, rank units by $\widehat{\tau}(x)$ and draw a TOC curve that plots cumulative gain against the fraction $q$ of the population treated.

---

## Summary

Our workflow in this lecture answers three questions in sequence:

1. **Is there substantial heterogeneity?** Reject $H_0{:}\;\tau(x)$ constant if RATE AUTOC or RATE Qini is positive and statistically reliable.
2. **Does targeting pay at realistic budgets?** Inspect the slope of the Qini curve around plausible coverage levels.
3. **Can we express the targeting rule in a few defensible steps?** Fit and validate a shallow policy tree.

In the lab section you will reproduce each stage on a simulated dataset. Next week we turn to the applied side of RATE statistics: interpreting AUTOC curves, Qini curves, and building policy trees from causal forest output.

---

Lab materials: [Lab 7: Data Wrangling and Effect Modification](lab-07.md)
