# Week 9: Resource Allocation and Policy Trees

**Date: 6 May 2026**

```admonish note title="Readings"
**Required**

- GRF documentation: <https://grf-labs.github.io/grf/>

**Optional**

- VanderWeele et al. (2020) *Outcome-Wide Longitudinal Designs for Causal Inference*
- Suzuki et al. (2020) *Causal Diagrams*
- Bulbulia et al. (2024) *A Practical Guide to Causal Inference with Panel Data*
- Hoffman et al. (2023) *Causal Forests*
```

```admonish important title="Key Concepts"
This week builds on the heterogeneous-treatment-effect (HTE) framework introduced in Week 8. By the end of this lecture you should understand six technical ideas:

1. ATE (Weeks 1--5)
2. CATE (Week 6)
3. The estimator $\widehat{\tau}(x)$ (Week 6)
4. RATE statistics derived from a **Targeting-Operator Characteristic** (TOC) curve (Week 8, applied here)
5. Qini curves (new)
6. **Policy trees** and how each fits into an applied research pipeline (new)
```

## Recap: RATE Statistics and the TOC Curve

In Week 8 we introduced the machinery for deciding whether heterogeneity in treatment effects is large enough to act on. The central tool is the **Targeting-Operator Characteristic (TOC)** curve, which plots cumulative gain on the vertical axis against the fraction $q$ of the population treated on the horizontal axis, after ranking individuals by their estimated CATE $\widehat{\tau}(x)$ from largest to smallest:

$$
G(q)=\frac{1}{n}\sum_{i=1}^{\lfloor qn\rfloor}\widehat{\tau}_{(i)}, \qquad 0\le q\le1.
$$

Two summary statistics integrate the TOC curve into a single number:

- **RATE AUTOC** (Area Under the TOC) weights every fraction $q$ equally, answering: if benefits concentrate among the best prospects, how much can we harvest by selecting them?
- **RATE Qini** weights the mid-range of $q$ more heavily, suiting investigators who face a fixed, moderate-sized budget (Yadlowsky et al. 2021). We evaluate the curve at treatment of 20% and 50% of the population.

A positive, statistically reliable RATE statistic tells us that targeting outperforms blanket treatment. This week we apply these statistics to worked examples and extend the pipeline with Qini curves and policy trees.

## Section 7: RATE AUTOC Example

Although out-of-bag (OOB) predictions are "out-of-sample" for individual trees, the full forest still reuses information. A simple remedy when estimating the RATE AUTOC and Qini is to split the data, **training** the forest on one fold and **testing** RATE/Qini on the other. This explicit splitting blocks optimistic bias and yields honest test statistics, including confidence intervals (GRF Labs 2024).

The RATE AUTOC curve for the effect of hours socialising on sense of meaning illustrates the typical pattern. The curve rises steeply at first: a small, correctly targeted programme could deliver large gains. Past about 30% of the sample the curve dips below zero, meaning that beyond that threshold targeting the CATE may perform worse than simply applying the ATE to everyone. The initial steepness reflects concentration of benefit among a minority of individuals, while the subsequent decline shows that extending the programme to those with small or negative estimated effects dilutes its value.

```admonish warning title="A Difficult Problem"
Figuring out who will benefit from a treatment is a difficult statistical problem (GRF Labs 2024). The RATE AUTOC curve summarises the *potential* for targeting, not a guarantee.
```

## Section 8: Visualising Policy Value with the Qini Curve

A **Qini curve** displays cumulative benefit on the vertical axis and treatment coverage (percentage of the population treated) on the horizontal axis. As with the AUTOC curve, we use a held-out test fold to validate the response curve.

For the effect of hours socialising on social belonging, the Qini curve shows that focussing on the top 20% of individuals nets a gain of roughly 0.08 units (95% CI 0.04--0.12). Widening coverage to 50% increases the cumulative gain to approximately 0.13 units (95% CI 0.07--0.19). Beyond that point the curve flattens: once we have treated everyone who offers a meaningful return, additional coverage adds little.

The Qini curve therefore translates CATE estimates into a practical budgeting tool. A policy-maker can read off the expected gain at any feasible coverage level and weigh it against cost.

## Section 9: From a Black Box to Simple Rules --- Policy Trees

The causal forest produces a personalised CATE for every individual, mapping a high-dimensional covariate vector $X$ to a number $\widehat{\tau}(X)$. Useful as that forecast may be, it stops short of telling us what to do. The function itself is too tangled (thousands of overlapping splits) to translate directly into a policy.

The **policytree** algorithm bridges that gap by collapsing the forest's $\widehat{\tau}(X)$ values into a single, shallow decision tree whose depth you choose. Each split is chosen to maximise expected benefit (Sverdrup et al. 2024). In this course we cap the depth at two, for three reasons:

1. At most three yes/no questions per rule, so the logic fits on a slide you can present to policy-makers.
2. Each leaf still contains enough observations to yield a stable effect estimate.
3. Deeper trees increase computational complexity faster than they improve payoffs.

### Policy tree example: effect of hours socialising on social belonging

The decision tree for social belonging first splits participants by self-esteem at $-0.925$ (original scale: 3.958). For those with self-esteem at or below this threshold, the next split is by neuroticism at $0.642$ (original scale: 4.228). Within that subgroup, individuals with neuroticism at or below the threshold are recommended **control**, while those above are recommended **treated**.

For participants with self-esteem above $-0.925$, the second split is by social belonging at $0.776$ (original scale: 5.972). In this subgroup, individuals with social belonging at or below the threshold are recommended **treated**, while those above are recommended **control**.

The corresponding policy map plots each individual in the two-dimensional covariate space defined by the tree's splitting variables, with colour indicating the recommended treatment assignment. The vertical and horizontal lines mark the split thresholds, carving the space into four rectangular regions. This visualisation makes the tree's logic immediately legible: one can see at a glance which combinations of covariates lead to a treatment recommendation and which do not. Because predictions are generated out of training sample, the map also serves as an informal check on how well the rule generalises.

## Section 10: Ethical and Practical Considerations

There is no guarantee that statistical optimality will align with social optimality. A rule that maximises expected health gains might still be **unaffordable** for a public agency, **unfair** to a protected group, or **opaque** to those asked to trust it. We all have our notions of fairness, and we cannot be expected to ignore them. Moreover, the estimation of CATE is always sensitive to which variables we include in our model (see the caveats in Week 6 regarding evidence for effect modification relative to the inclusion of other variables).

We should not consider CATE an absolute guide to practice. Caution is warranted.

Yet the very same CATE machinery that powers targeting also helps science move past a one-size-fits-all mindset. By mapping treatment effects across a high-dimensional covariate space, we can test whether our favourite categories (gender, age group, clinical severity) actually capture the differences that matter. Sometimes they do; often they do not, revealing that nature is not carved at the joints of our folk classifications. Discovering where the forest finds meaningful splits can generate fresh psychological hypotheses about who responds, why, and under what circumstances, even when no policy decision is on the table. Over the coming weeks we shall return to this point with examples.

## Summary and Next Steps

Our workflow answers three questions in sequence:

1. **Is there substantial heterogeneity?** Reject $H_0{:}\;\tau(x)$ constant if RATE AUTOC or RATE Qini is positive and statistically reliable.
2. **Does targeting pay at realistic budgets?** Inspect the slope of the Qini curve around plausible coverage levels.
3. **Can we express the targeting rule in a few defensible steps?** Fit and validate a shallow policy tree.

In the lab section you will reproduce each stage on a simulated dataset.
