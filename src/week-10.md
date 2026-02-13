# Week 10: Classical Measurement Theory from a Causal Perspective

**Date: 13 May 2026**

```admonish info title="Readings"
**Required**

- Fischer & Karl (2019). A primer on measurement invariance testing. [link](https://www.dropbox.com/scl/fi/1h8slzy3vzscvbtp6yrjh/FischeKarlprimer.pdf?rlkey=xl93d5y7280c1qjhn3k2g8qls&dl=0)

**Optional**

- Van de Vijver (2021). *Culture and Psychology.* [link](https://doi.org/10.1017/9781107415188)
- He & van de Vijver (2012). Bias and equivalence in cross-cultural research. [link](https://www.dropbox.com/scl/fi/zuv4odmxbz8dbtdjfap3e/He-BiasandEquivalence.pdf?rlkey=wezprklb4jm6rgvvx0g58nw1n&dl=0)
- Harkness (2003). Questionnaire translation. [link](https://www.dropbox.com/scl/fi/hmmje9vbunmcu3oiahaa5/Harkness_CC_translation.pdf?rlkey=6vqq3ap5n52qp7t1e570ubpgt&dl=0)
```

```admonish warning title="Key Concepts"
- Exploratory Factor Analysis (EFA)
- Confirmatory Factor Analysis (CFA)
- Multigroup CFA
- Invariance Testing (configural, metric, scalar)
```

## Overview

By the conclusion of this session, you will gain proficiency in:

1. Exploratory Factor Analysis (EFA),
2. Confirmatory Factor Analysis (CFA),
3. Multigroup Confirmatory Factor Analysis,
4. Partial Invariance (configural, metric, and scalar equivalence).

We will learn these concepts by working through an analysis.

## Part 1: Factor Analysis and Measurement Invariance

### Focus on Kessler-6 Anxiety

The code below will:

1. Load required packages.
2. Select the Kessler 6 items.
3. Check whether there is sufficient correlation among the variables to support factor analysis.

#### Select a scale to validate: Kessler-6 Distress

```r
# get synthetic data
library(margot)
library(tidyverse)
library(performance)

# select the columns of the kessler-6
dt_only_k6 <- df_nz |>
  filter(wave == 2018) |>
  select(
    kessler_depressed,
    kessler_effort,
    kessler_hopeless,
    kessler_worthless,
    kessler_nervous,
    kessler_restless
  )

# check factor structure
performance::check_factorstructure(dt_only_k6)
```

### Practical definitions of the Kessler-6 items

The `df_nz` dataset is loaded with the `margot` package. It is a synthetic dataset. We take items from the Kessler-6 (K6) scale: depressed, effort, hopeless, worthless, nervous, and restless (Kessler et al. 2002; Kessler et al. 2010). The Kessler-6 is used as a diagnostic screening tool for depression by physicians in New Zealand.

The Kessler-6 (K6) is a widely used diagnostic screening tool designed to identify levels of psychological distress that may indicate mental health disorders such as depression and anxiety. Physicians in New Zealand use it to screen patients quickly (Krynen et al. 2013). Each item on the Kessler-6 asks respondents to reflect on their feelings and behaviours over the past 30 days, with responses provided on a five-point ordinal scale:

1. **"...you feel hopeless"**: This item measures the frequency of feelings of hopelessness. It assesses a core symptom of depression, where the individual perceives little or no optimism about the future.

2. **"...you feel so depressed that nothing could cheer you up"**: This statement gauges the depth of depressive feelings and the inability to gain pleasure from normally enjoyable activities, a condition known as anhedonia.

3. **"...you feel restless or fidgety"**: This item evaluates agitation and physical restlessness, which are common in anxiety disorders but can also be present in depressive states.

4. **"...you feel that everything was an effort"**: This query assesses feelings of fatigue or exhaustion with everyday tasks, reflecting the loss of energy that is frequently a component of depression.

5. **"...you feel worthless"**: This item measures feelings of low self-esteem or self-worth, which are critical indicators of depressive disorders.

6. **"...you feel nervous"**: This question is aimed at identifying symptoms of nervousness or anxiety, helping to pinpoint anxiety disorders.

#### Response options

The ordinal response options provided for the Kessler-6 are designed to capture the frequency of these symptoms, which is crucial for assessing the severity and persistence of psychological distress:

- **1. "None of the time"**: The symptom was not experienced at all.
- **2. "A little of the time"**: The symptom was experienced infrequently.
- **3. "Some of the time"**: The symptom was experienced occasionally.
- **4. "Most of the time"**: The symptom was experienced frequently.
- **5. "All of the time"**: The symptom was constantly experienced.

In clinical practice, higher scores on the Kessler-6 are indicative of greater distress and a higher probability of a mental health disorder. Physicians use a sum score of 13 to decide on further diagnostic evaluations or immediate therapeutic interventions.

The simplicity and quick administration of the Kessler-6 make it an effective tool for primary care settings in New Zealand, allowing for the early detection and management of mental health issues. Do the items in this scale cohere? Do they all relate to depression? Might we quantitatively evaluate "coherence" in this scale?

### Exploratory Factor Analysis

We employ `performance::check_factorstructure()` to evaluate the data's suitability for factor analysis. Two tests are reported:

#### Bartlett's test of sphericity

Bartlett's Test of Sphericity is used in psychometrics to assess the appropriateness of factor analysis for a dataset. It tests the hypothesis that the observed correlation matrix is an identity matrix, which would suggest that all variables are orthogonal (i.e., uncorrelated) and therefore factor analysis would be inappropriate.

The outcome:

- **Chi-square (Chisq)**: 50402.32 with **degrees of freedom (15)** and a **p-value** < .001

This result (p < .001) confirms that the observed correlation matrix is not an identity matrix, substantiating the factorability of the dataset.

#### Kaiser-Meyer-Olkin (KMO) measure

The KMO test assesses sampling adequacy by comparing the magnitudes of observed correlation coefficients to those of partial correlation coefficients. A KMO value nearing 1 indicates appropriateness for factor analysis. The results are:

- **Overall KMO**: 0.87. This value suggests good sampling adequacy, indicating that the sum of partial correlations is relatively low compared to the sum of correlations, thus supporting the potential for distinct and reliable factors.

Each item's KMO value exceeds the acceptable threshold of 0.5, so the data are suitable for factor analysis.

#### Explore factor structure

The following R code allows us to perform EFA on the Kessler-6 data, assuming three latent factors.

```r
# exploratory factor analysis
# explore a factor structure made of 3 latent variables
library("psych")
library("parameters")

# do efa
efa <- psych::fa(dt_only_k6, nfactors = 3) |>
  model_parameters(sort = TRUE, threshold = "max")

print(efa)
```

#### What is "rotation"?

In factor analysis, rotation is a mathematical technique applied to the factor solution to make it more interpretable. Rotation is analogous to adjusting the angle of a camera to get a better view. When we rotate the factors, we are not changing the underlying data, just how we are looking at it, to make the relationships between variables and factors clearer and more meaningful.

The main goal of rotation is to achieve a simpler and more interpretable factor structure. This simplification is achieved by making the factors as distinct as possible, by aligning them closer with specific variables, which makes it easier to understand what each factor represents.

There are two types:

**Orthogonal rotations** (such as Varimax) assume that the factors are uncorrelated and keep the axes at 90 degrees to each other. This is useful when we assume that the underlying factors are independent.

**Oblique rotations** (such as Oblimin) allow the factors to correlate. This is more realistic in psychological and social sciences because constructs such as stress and anxiety may naturally correlate with each other, so oblique rotation is often a better option.

#### Results

Using oblimin rotation, the items loaded as follows on the three factors:

- **MR3**: Strongly associated with `kessler_hopeless` (0.79) and `kessler_worthless` (0.79). This factor might capture aspects related to feelings of hopelessness and worthlessness, often linked with depressive affect.
- **MR1**: Mostly linked with `kessler_depressed` (0.99), suggesting this factor represents core depressive symptoms.
- **MR2**: Includes `kessler_restless` (0.72), `kessler_nervous` (0.43), and `kessler_effort` (0.38). This factor seems to encompass symptoms related to anxiety and agitation.

The **complexity** values indicate the number of factors each item loads on "significantly." A complexity near 1.00 suggests that the item predominantly loads on a single factor, which is seen with most items except for `kessler_nervous` and `kessler_effort`, which show higher complexity and thus share variance with more than one factor.

**Uniqueness** values represent the variance in each item not explained by the common factors. Lower uniqueness values for items like `kessler_depressed` indicate that the factor explains most of the variance for that item.

#### Variance explained

The three factors together account for 62.94% of the total variance in the data, distributed as follows:

- **MR3**: 28.20%
- **MR1**: 17.56%
- **MR2**: 17.18%

This indicates a substantial explanation of the data's variance by the model, with the highest contribution from the factor associated with hopelessness and worthlessness.

#### Consensus view?

There are different algorithms for assessing the factor structure. The `performance` package allows us to consider a "consensus" view.

```r
n <- n_factors(dt_only_k6)
n
# plot
plot(n) + theme_classic()
```

Output:

> The choice of 1 dimensions is supported by 8 (50.00%) methods out of 16 (Optimal coordinates, Acceleration factor, Parallel analysis, Kaiser criterion, Scree (SE), Scree (R2), VSS complexity 1, Velicer's MAP).

The result indicates that a single dimension is supported by half of the methods used (8 out of 16). However, science is not a matter of voting. Also, does it make sense that there is one latent factor here?

### Confirmatory Factor Analysis (ignoring groups)

CFA validates the hypothesised factor structures derived from EFA.

- **One-factor model**: assumes all items measure a single underlying construct.
- **Two-factor model**: assumes two distinct constructs measured by the items.
- **Three-factor model**: assumes three distinct constructs measured by the items.

#### 1. Data partition

First, we take the dataset (`dt_only_k6`) and partition it into training and testing sets. This division helps in validating the model built on the training data against an unseen test set, enhancing robustness of the factor analysis findings.

```r
set.seed(123)
part_data <- datawizard::data_partition(dt_only_k6, training_proportion = .7, seed = 123)
training <- part_data$p_0.7
test <- part_data$test
```

#### 2. Model setup for CFA

Based on the EFA results, we consider three different factor structures. We fit each model to the training data:

```r
# one factor model
structure_k6_one <- psych::fa(training, nfactors = 1) |>
  efa_to_cfa()

# two factor model
structure_k6_two <- psych::fa(training, nfactors = 2) |>
  efa_to_cfa()

# three factor model
structure_k6_three <- psych::fa(training, nfactors = 3) |>
  efa_to_cfa()

# inspect models
structure_k6_one
structure_k6_two
structure_k6_three
```

- **One-factor model**: All items are linked to a single factor (`MR1`).

- **Two-factor model**:
  - `MR1` is linked with `kessler_depressed`, `kessler_hopeless`, and `kessler_worthless`, suggesting these items might represent a more depressive aspect of distress.
  - `MR2` is associated with `kessler_effort`, `kessler_nervous`, and `kessler_restless`, which could indicate a different aspect, perhaps related to anxiety or agitation.

- **Three-factor model**:
  - `MR1` includes `kessler_depressed`, `kessler_effort`, `kessler_hopeless`, and `kessler_worthless`, indicating a broad factor possibly encompassing overall distress.
  - `MR2` consists solely of `kessler_effort`.
  - `MR3` includes `kessler_nervous` and `kessler_restless`, which might imply these are distinctive from other distress components.

Do these results make sense? Note they are different from the EFA. Why might that be?

Next we perform the confirmatory factor analysis itself:

```r
# fit and compare models
library(lavaan)

# one latent model
one_latent <-
  suppressWarnings(lavaan::cfa(structure_k6_one, data = test))

# two latents model
two_latents <-
  suppressWarnings(lavaan::cfa(structure_k6_two, data = test))

# three latents model
three_latents <-
  suppressWarnings(lavaan::cfa(structure_k6_three, data = test))

# compare models
compare <-
  performance::compare_performance(one_latent, two_latents, three_latents, verbose = FALSE)

# select cols we want
key_columns_df <- compare[, c("Model", "Chi2", "Chi2_df", "CFI", "RMSEA",
                               "RMSEA_CI_low", "RMSEA_CI_high", "AIC", "BIC")]

# view as table
as.data.frame(key_columns_df) |>
  kbl(format = "markdown")
```

#### Metrics

- **Chi2 (Chi-Square Test)**: A lower Chi2 value indicates a better fit of the model to the data.
- **df (Degrees of Freedom)**: Reflects the model complexity.
- **CFI (Comparative Fit Index)**: Values closer to 1 indicate a better fit. A value above 0.95 is generally considered good.
- **RMSEA (Root Mean Square Error of Approximation)**: Values less than 0.05 indicate a good fit, and values up to 0.08 are acceptable.
- **AIC (Akaike Information Criterion) and BIC (Bayesian Information Criterion)**: Lower values are better, indicating a more parsimonious model, with BIC imposing a penalty for model complexity.

#### Model selection

- The **three latents model** shows the best fit across all indicators, having the lowest Chi2, RMSEA, and the highest CFI. It also has the lowest AIC and BIC scores.

However:

- The **CFI** for the two-factor model is 0.990, which is close to 1 and suggests a very good fit. This is superior to the one-factor model (CFI = 0.947) and slightly less than the three-factor model (CFI = 0.995).
- The **RMSEA** for the two-factor model is 0.044, which is within the excellent fit range (below 0.05). It improves upon the one-factor model's RMSEA of 0.099 and is only slightly worse than the three-factor model's 0.031.
- **BIC** is not very different.

The two-factor model strikes a balance between simplicity and model fit. It has fewer factors than the three-factor model, making it potentially easier to interpret while still capturing the variance in the data.

Does anxiety appear to differ from depression?

### Measurement Invariance in Multigroup Confirmatory Factor Analysis

When we use tools like surveys or tests to measure psychological constructs (such as distress, intelligence, satisfaction), we often need to ensure that these tools work similarly across different groups of people. This is crucial for fair comparisons.

#### Levels of measurement invariance

Measurement invariance tests how consistently a measure operates across different groups, such as ethnic or gender groups. Consider how we can understand it through the K6 Distress Scale applied to different demographic groups in New Zealand:

**1. Configural invariance**

The measure's structure is the same across groups. For the K6 Distress Scale, both Maori and New Zealand Europeans use the same six questions to gauge distress. However, how strongly each question predicts distress can vary between the groups. (Note we are using "prediction" here; we are only assessing associations.)

**2. Metric invariance**

The strength of the relationship between each question and the construct (distress) is consistent across groups. If metric invariance holds, a unit change in the latent distress factor affects scores on questions (such as feeling nervous or hopeless) equally for Maori and New Zealand Europeans.

**3. Scalar invariance**

Beyond having the same structure and relationships, everyone starts from the same baseline. If scalar invariance holds, the actual scores on the distress scale mean the same thing across different groups. If a Maori participant scores 15 and a New Zealander of European descent scores 15, both are experiencing a comparable level of distress.

#### Concept of "partial invariance"

Sometimes, not all conditions for full metric or scalar invariance are met, which could hinder meaningful comparisons across groups. This is where the concept of "partial invariance" comes into play.

**Partial invariance** occurs when invariance holds for some but not all items of the scale across groups.

- **Metric partial invariance**: Most items relate similarly to the underlying factor across groups, but one or two do not. Researchers might decide that there is enough similarity to proceed with comparisons of relationships (correlations) but should proceed with caution.

- **Scalar partial invariance**: Most but not all items show the same intercepts across groups. It suggests that while comparisons of the construct means can be made, some scores might need adjustments or nuanced interpretation.

In practical terms, achieving partial invariance allows for some comparisons but signals a need for careful interpretation. For instance, if partial scalar invariance is found on the K6 Distress Scale, researchers might compare overall distress levels between Maori and New Zealand Europeans but should acknowledge that differences in certain item responses might reflect measurement bias rather than true differences in distress.

Understanding these levels of invariance helps ensure that when we compare mental health or other constructs across different groups, we are making fair and meaningful comparisons.

#### Running multigroup CFA

The following script runs multigroup confirmatory factor analysis (MG-CFA) to assess the invariance of the K6 distress scale across two ethnic groups: European New Zealanders and Maori.

```r
# select needed columns plus 'ethnicity'
# filter dataset for only 'euro' and 'maori' ethnic categories
dt_eth_k6_eth <- df_nz |>
  filter(wave == 2018) |>
  filter(eth_cat == "euro" | eth_cat == "maori") |>
  select(kessler_depressed, kessler_effort, kessler_hopeless,
         kessler_worthless, kessler_nervous, kessler_restless, eth_cat)

# partition the dataset into training and test subsets
# stratify by ethnic category to ensure balanced representation
part_data_eth <- datawizard::data_partition(dt_eth_k6_eth,
  training_proportion = .7, seed = 123, group = "eth_cat")

training_eth <- part_data_eth$p_0.7
test_eth <- part_data_eth$test

# configural invariance models
# run cfa models specifying one, two, and three latent variables
# without constraining across groups
one_latent_eth_configural <- suppressWarnings(
  lavaan::cfa(structure_k6_one, group = "eth_cat", data = test_eth))
two_latents_eth_configural <- suppressWarnings(
  lavaan::cfa(structure_k6_two, group = "eth_cat", data = test_eth))
three_latents_eth_configural <- suppressWarnings(
  lavaan::cfa(structure_k6_three, group = "eth_cat", data = test_eth))

# compare model performances for configural invariance
compare_eth_configural <- performance::compare_performance(
  one_latent_eth_configural, two_latents_eth_configural,
  three_latents_eth_configural, verbose = FALSE)

# metric invariance models
# run cfa models holding factor loadings equal across groups
one_latent_eth_metric <- suppressWarnings(
  lavaan::cfa(structure_k6_one, group = "eth_cat",
              group.equal = "loadings", data = test_eth))
two_latents_eth_metric <- suppressWarnings(
  lavaan::cfa(structure_k6_two, group = "eth_cat",
              group.equal = "loadings", data = test_eth))
three_latents_eth_metric <- suppressWarnings(
  lavaan::cfa(structure_k6_three, group = "eth_cat",
              group.equal = "loadings", data = test_eth))

# compare model performances for metric invariance
compare_eth_metric <- performance::compare_performance(
  one_latent_eth_metric, two_latents_eth_metric,
  three_latents_eth_metric, verbose = FALSE)

# scalar invariance models
# run cfa models holding factor loadings and intercepts equal across groups
one_latent_eth_scalar <- suppressWarnings(
  lavaan::cfa(structure_k6_one, group = "eth_cat",
              group.equal = c("loadings", "intercepts"), data = test_eth))
two_latents_eth_scalar <- suppressWarnings(
  lavaan::cfa(structure_k6_two, group = "eth_cat",
              group.equal = c("loadings", "intercepts"), data = test_eth))
three_latents_eth_scalar <- suppressWarnings(
  lavaan::cfa(structure_k6_three, group = "eth_cat",
              group.equal = c("loadings", "intercepts"), data = test_eth))

# compare model performances for scalar invariance
compare_eth_scalar <- performance::compare_performance(
  one_latent_eth_scalar, two_latents_eth_scalar,
  three_latents_eth_scalar, verbose = FALSE)
```

### Configural invariance results

The table represents the comparison of three MG-CFA models conducted to test for configural invariance across different ethnic categories. Configural invariance refers to whether the pattern of factor loadings is the same across groups. It is the most basic form of measurement invariance.

Looking at the results:

1. **Chi2 (Chi-square)**: A lower value suggests a better model fit. The three-factor model performs best.
2. **GFI (Goodness of Fit Index) and AGFI (Adjusted Goodness of Fit Index)**: Values range from 0 to 1, with values closer to 1 suggesting a better fit. All models are close.
3. **NFI, NNFI (TLI), CFI**: Range from 0 to 1, with values closer to 1 suggesting a better fit. The two- and three-factor models have the highest values.
4. **RMSEA**: Lower values are better, with values below 0.05 considered good and up to 0.08 considered acceptable. Only the two- and three-factor models meet this threshold.
5. **AIC and BIC**: The three-factor model has the lowest values.

**Analysis of the results:**

- **One latent model**: Shows the poorest fit among the models with a high RMSEA and the lowest CFI. The Chi-squared value is significantly high, indicating a substantial misfit with the observed data.
- **Two latents model**: Displays a much improved fit compared to the one-latent model, as evident from its much lower Chi-squared value, lower RMSEA, and higher CFI.
- **Three latents model**: Provides the best fit metrics among the three configurations.

### Metric equivalence

Metric equivalence (also known as weak measurement invariance) tests whether factor loadings are equal across groups. The three-factor model again performs best.

### Scalar equivalence

Scalar equivalence tests whether both factor loadings and intercepts are equal across groups. This is the strongest form of invariance and is required for meaningful mean comparisons across groups.

Overall, there is good evidence for the three-factor model of Kessler-6, but the two-factor model is close.

Consider: when might we prefer a two-factor model? When might we prefer a three-factor model? When might we prefer a one-factor model?

### Conclusion: understanding the limits of association in factor models

This discussion of measurement invariance across different demographic groups underscores the reliance of factor models on the underlying associations in the data. These models are fundamentally descriptive, not prescriptive; they organise the observed data into a coherent structure based on correlations and assumed relationships among variables.

Factor analysis assumes that the latent variables (factors) causally influence the observed indicators. This is a strong assumption that can profoundly affect the interpretation of results. Understanding and critically evaluating our assumptions is essential when applying factor analysis to real-world scenarios.

The assumption that latent variables cause the observed indicators, rather than merely being associated with them, suggests a directional relationship that can affect decisions made based on the analysis. For instance, if we believe that a latent construct like psychological distress causally influences responses on the K6 scale, interventions might be designed to target this distress directly. However, if the relationship is more complex or bidirectional, such straightforward interventions might not be as effective.

## Part 2: How Traditional Measurement Theory Fails

```admonish info title="Readings for Part 2"
**Required**

- VanderWeele (2022). Constructs and measures. [link](https://www.dropbox.com/scl/fi/mmyguc0hrci8wtyyfkv6w/tyler-vanderweele-contruct-measures.pdf?rlkey=o18fiyajdqqpyjgssyh6mz6qm&dl=0)

**Suggested**

- Harkness & van de Vijver (2003). Questionnaire design in comparative research. [link](https://www.dropbox.com/scl/fi/uhj73050r1i7rznn6i57f/HarknessvandeVijver2003_Chapter-2.pdf?rlkey=ijxobrh7czj3rfnq9air342d2&dl=0)
```

```admonish warning title="Key Concepts for Part 2"
- The Formative Model in Factor Analysis
- The Reflective Model in Factor Analysis
- How to use causal diagrams to evaluate assumptions
```

### Overview

By the end of this section you will:

1. Understand the causal assumptions implied by the factor analytic interpretation of the formative and reflective models.
2. Be able to distinguish between statistical and structural interpretations of these models.
3. Understand why VanderWeele argues that consistent causal estimation is possible using the theory of multiple versions of treatments for constructs with multiple indicators.

### Two ways of thinking about measurement in psychometric research

In psychometric research, formative and reflective models describe the relationship between latent variables and their respective indicators. VanderWeele discusses this in the assigned reading (VanderWeele 2022).

#### Reflective model (factor analysis)

In a reflective measurement model, also known as an effect indicator model, the latent variable is understood to cause the observed variables. In this model, changes in the latent variable cause changes in the observed variables. Each indicator (observed variable) is a "reflection" of the latent variable: they are effects or manifestations of the latent variable.

The reflective model may be expressed:

$$X_i = \lambda_i \eta + \varepsilon_i$$

Here, $X_i$ is an observed variable (indicator), $\lambda_i$ is the factor loading for $X_i$, $\eta$ is the latent variable, and $\varepsilon_i$ is the error term associated with $X_i$. It is assumed that all the indicators are interchangeable and have a common cause, which is the latent variable $\eta$.

In the conventional approach of factor analysis, the assumption is that a common latent variable is responsible for the correlation seen among the indicators. Any fluctuation in the latent variable should lead to similar changes in the indicators.

**Diagram: Reflective model.** The latent variable $\eta$ gives rise to indicators $X_1 \dots X_n$.

```
    η ──→ X₁
    η ──→ X₂
    η ──→  ⋮
    η ──→ Xₙ
```

#### Formative model (factor analysis)

In a formative measurement model, the observed variables are seen as causing or determining the latent variable. Here again there is a single latent variable, but this latent variable is taken to be an effect of the underlying indicators.

The formative model may be expressed:

$$\eta = \sum_i \lambda_i X_i + \varepsilon$$

In this equation, $\eta$ is the latent variable, $\lambda_i$ is the weight for $X_i$ (the observed variable), and $\varepsilon$ is the error term. The latent variable $\eta$ is a composite of the observed variables $X_i$.

In the context of a formative model, correlation or interchangeability between indicators is not required. Each indicator contributes distinctively to the latent variable. A modification in one indicator does not automatically imply a corresponding change in the other indicators.

**Diagram: Formative model.** The indicators $X_1 \dots X_n$ give rise to the latent variable $\eta$.

```
    X₁ ──→ η
    X₂ ──→ η
     ⋮ ──→ η
    Xₙ ──→ η
```

### Structural interpretation of the formative and reflective models

VanderWeele distinguishes between statistical and structural interpretations of the equations presented above.

1. **Statistical model:** A mathematical construct that shows how observable variables (indicators) are related to latent or unseen variables.

2. **Structural model:** The causal assumptions or hypotheses about the relationships among variables in a statistical model. The assumptions of the factor analytic tradition (that the latent variable is causally efficacious) are structural claims.

The **reflective model** statistically implies that the observed variables are reflections of the latent variable, expressed as $X_i = \lambda_i \eta + \varepsilon_i$. However, the factor analytic tradition makes the additional structural assumption that a univariate latent variable causally influences the observed variables.

The **formative model** statistically implies that the latent variable is formed by the observed variables, expressed as $\eta = \sum_i \lambda_i X_i + \varepsilon$. However, the factor analytic tradition makes the additional assumption that the observed variables give rise to a univariate latent variable.

### Problems with the structural interpretations

While the statistical model $X_i = \lambda_i \eta + \varepsilon_i$ aligns with the standard reflective structural interpretation, it is also compatible with a model where indicators themselves cause the outcome. Cross-sectional data do not provide enough information to discern between these different structural interpretations.

Similarly, the statistical model $\eta = \sum_i \lambda_i X_i + \varepsilon$ agrees with the standard formative structural interpretation, but it also agrees with a model in which the indicators, not the latent composite, exert causal effects on the outcome.

The formative and reflective conceptions of factor analysis are compatible with:

- Indicators having direct causal effects on the outcome (rather than operating through the latent variable).
- A multivariate reality ($\eta_1 \dots \eta_n$) giving rise to multiple indicators, each of which may have distinct causal relevance.
- Only one or several of the indicators being causally efficacious, even though we construct our measure from all of them.

```admonish note title="VanderWeele's key observation"
While cross-sectional data can provide insights into the relationships between variables, they cannot conclusively determine the causal direction of these relationships.
```

This result is concerning. The structural assumptions of factor analysis underpin nearly all psychological research. If the cross-sectional data used to derive factor structures cannot decide whether the structural interpretations of factor models are accurate, where does that leave us?

More concerning still, VanderWeele discusses several longitudinal tests for structural interpretations of univariate latent variables that do not pass.

Where does that leave us? In psychology we have heard about a replication crisis. The reliance on factor models may be an aspect of a much larger "causal crisis" (Bulbulia et al. 2022).

### Review of the theory of multiple versions of treatment

Perhaps not all is lost. VanderWeele looks to the theory of multiple versions of treatment.

Recall, a causal effect is defined as the difference in the expected potential outcome when everyone is exposed (perhaps contrary to fact) to one level of a treatment, conditional on their levels of a confounder, with the expected potential outcome when everyone is exposed to a different level of a treatment (perhaps contrary to fact), conditional on their levels of a confounder.

$$\delta = \sum_l \left( \mathbb{E}[Y|A=a,l] - \mathbb{E}[Y|A=a^*,l] \right) P(l)$$

where $\delta$ is the causal estimand on the difference scale ($\mathbb{E}[Y^1 - Y^0]$).

In causal inference, the multiple versions of treatment theory allows us to handle situations where the treatment is not uniform but instead has several variations. Each variation of the treatment, or "version," can have a different impact on the outcome. Consistency is not violated because it is redefined: for each version of the treatment, the outcome under that version is equal to the observed outcome when that version is received. We may think of the indicator $A$ as corresponding to many versions of the true treatment $K$. Where conditional independence holds such that there is an absence of confounding for the effect of $K$ on $Y$ given $L$, we have: $Y_k \perp\!\!\!\perp A | K, L$. This states that conditional on $L$, $A$ gives no information about $Y$ once $K$ and $L$ are accounted for. When $Y = Y_k$ if $K = k$ and $Y_k$ is independent of $K$ conditional on $L$, then $A$ may be thought of as a coarsened indicator of $K$. We may estimate consistent causal effects where:

$$\delta = \sum_{k,l} \mathbb{E}[Y_k|l] P(k|a,l) P(l) - \sum_{k,l} \mathbb{E}[Y_k|l] P(k|a^*,l) P(l)$$

The scenario represents a hypothetical randomised trial where within strata of covariates $L$, individuals in one group receive a treatment $K$ version randomly assigned from the distribution of $K$ among the $(A = 1, L = l)$ sub-population. Meanwhile, individuals in the other group receive a randomly assigned $K$ version from the $(A = 0, L = l)$ sub-population.

#### Reflective and formative measurement models may be approached as multiple versions of treatment

VanderWeele applies the following substitution:

$$\delta = \sum_{\eta,l} \mathbb{E}[Y_\eta|l] P(\eta|A=a+1,l) P(l) - \sum_{\eta,l} \mathbb{E}[Y_\eta|l] P(\eta|A=a,l) P(l)$$

Specifically, we substitute $K$ with $\eta$ and compare the measurement response $A = a + 1$ with $A = a$. We discover that if the influence of $\eta$ on $Y$ is not confounded given $L$, then the multiple versions of reality consistent with the reflective and formative statistical models will not lead to biased estimation. The parameter $\delta$ retains its interpretability as a comparison in a hypothetical randomised trial in which the distribution of coarsened measures of $\eta_A$ are balanced within levels of the treatment, conditional on $\eta_L$.

This connection between measurement and the multiple versions of treatment framework provides hope for consistent causal inference under varying reliabilities of measurement.

However, as with the theory of multiple treatments, we might not know how to interpret our results because we do not know the true relationships between our measured indicators and underlying reality.

### VanderWeele's model of reality

VanderWeele's article concludes as follows:

> A preliminary outline of a more adequate approach to the construction and use of psychosocial measures might thus be summarised by the following propositions: (1) Traditional univariate reflective and formative models do not adequately capture the relations between the underlying causally relevant phenomena and our indicators and measures. (2) The causally relevant constituents of reality related to our constructs are almost always multidimensional, giving rise both to our indicators from which we construct measures, and also to our language and concepts, from which we can more precisely define constructs. (3) In measure construction, we ought always to specify a definition of the underlying construct, from which items are derived, and by which analytic relations of the items to the definition are made clear. (4) The presumption of a structural univariate reflective model impairs measure construction, evaluation, and use. (5) If a structural interpretation of a univariate reflective factor model is being proposed this should be formally tested, not presumed; factor analysis is not sufficient for assessing the relevant evidence. (6) Even when the causally relevant constituents of reality are multidimensional, and a univariate measure is used, we can still interpret associations with outcomes using theory for multiple versions of treatment, though the interpretation is obscured when we do not have a clear sense of what the causally relevant constituents are. (7) When data permit, examining associations item-by-item, or with conceptually related item sets, may give insight into the various facets of the construct.

> A new integrated theory of measurement for psychosocial constructs is needed in light of these points, one that better respects the relations between our constructs, items, indicators, measures, and the underlying causally relevant phenomena. (VanderWeele 2022)

This seems sensible. However, VanderWeele's diagram of multivariate reality ($\mathcal{R}$) giving rise to concepts, constructs, latent variables ($\eta$), indicators ($X_1 \dots X_n$), measures ($A$), and outcomes ($Y$) is not strictly a causal graph. The arrows do not clearly represent causal relations. It leaves unclear what to do in practice.

### How measurement error can be incorporated into causal diagrams

By now you are familiar with the New Zealand Attitudes and Values Study (NZAVS), which is a national probability survey that collects a wide range of information, including data on distress, exercise habits, and cultural backgrounds.

Consider a study that seeks to use this dataset to investigate the effect of regular exercise on psychological distress. In contrast to previous graphs, let us allow for latent reality to affect our measurements, as well as the discrepancies between our measurements and true underlying reality.

We represent true exercise by $\eta_A$. We represent true psychological distress by $\eta_Y$. Let $\eta_L$ denote a person's true workload, and assume that this state of work affects both levels of exercise and psychological distress.

To bring the model into contact with measurement theory, let us describe measurements of these latent true underlying realities as functions of multiple indicators: $L_{f(X_1\dots X_n)}$, $A_{f(X_1\dots X_n)}$, and $Y_{f(X_1\dots X_n)}$. These constructs are measured realisations of the underlying true states. We assume that the true states of these variables affect their corresponding measured states, and so draw arrows from $\eta_L \rightarrow L_{f(X_1\dots X_n)}$, $\eta_A \rightarrow A_{f(X_1\dots X_n)}$, $\eta_Y \rightarrow Y_{f(X_1\dots X_n)}$.

We also assume unmeasured sources of error that affect the measurements: $U_L \rightarrow L_{f(X_1\dots X_n)}$, $U_A \rightarrow A_{f(X_1\dots X_n)}$, and $U_Y \rightarrow Y_{f(X_1\dots X_n)}$. That is, we allow that our measured indicators may imperfectly reflect the underlying true reality they hope to capture. We use $U_L$, $U_A$ and $U_Y$ to denote the unmeasured sources of error in the measured indicators.

Allow that the true underlying reality represented by $\eta_{var}$ may be multivariate. Similarly, allow the true underlying reality represented by $U_{var}$ to be multivariate.

We now have a causal diagram that more precisely captures VanderWeele's thinking. We have fleshed out $\mathcal{R}$ in a way that may include natural language concepts and scientific constructs as latent realities and latent unmeasured sources of error in our constructs.

The utility of describing the measurement dynamics using causal graphs is apparent. We can understand that the measured states, once conditioned upon, create *collider biases* which open paths between the unmeasured sources of error and the true underlying state that gives rise to our measurements.

```admonish warning title="Key Insight"
Where true unmeasured (multivariate) psycho-physical states are related to true unmeasured (multivariate) sources of error in the measurement of those states, the very act of measurement opens pathways to confounding.
```

If for each measured construct $var_{f(X_1, X_2,\dots X_n)}$, the sources of error $U_{var}$ and the unmeasured constituents of reality that give rise to our measures $\eta_{var}$ are uncorrelated with other variables $U'_{var}$, $\eta'_{var}$, and $var'_{f(X_1, X_2,\dots X_n)}$, our estimates may be downwardly biased toward the null. However, d-separation is preserved. Where errors are uncorrelated with true latent realities, there is no new pathway that opens information between our exposure and outcome.

#### Directed and correlated measurement error

When measurement errors are not merely random but are correlated or directed, additional pathways to confounding can open:

- $\eta_L \rightarrow L$: We assume that the true workload state affects its measurement. This measurement, however, may be affected by an unmeasured error source, $U_L$. Personal perceptions of workload can introduce this error. Cultural influences like societal expectations of productivity could shape responses independently of the true workload state.

- $\eta_A \rightarrow A$: When it comes to exercise, the true state may affect the measured frequency (questions about exercise are not totally uninformative). However, this measurement is also affected by an unmeasured source of error, $U_A$. For example, a cultural shift towards valuing physical health might prompt participants to report higher activity levels.

- $\eta_Y \rightarrow Y$: Actual distress affects the measured distress. However, this measurement is subject to unmeasured error, $U_Y$. For instance, an increased societal acceptance of mental health might change how distress is reported.

- $U_L \rightarrow U_A$ and $U_L \rightarrow U_Y$: The error in the stress measurement can correlate with those in the exercise and mood measurements. This could stem from a common cultural bias affecting how a participant self-reports across these areas.

- $\eta_A \rightarrow U_Y$ and $\eta_L \rightarrow U_A$: The actual state of one variable can affect the error in another variable's measurement. For example, a cultural emphasis on physical health leading to increased exercise might, in turn, affect the reporting of distress levels.

### Confounding control by baseline measures: dependent directed measurement error in three-wave panels

1. We propose a three-wave panel design to control confounding. This design adjusts for baseline measurements of both exposure and the outcome.

2. Understanding this approach in the context of potential directed and correlated measurement errors gives us a clearer picture of its strengths and limitations.

3. This three-wave panel design incorporates baseline measurements of both exposure and confounders. As a result, any bias that could come from unmeasured sources of measurement errors should be uncorrelated with their baseline effects.

4. For instance, if individuals have a social desirability bias at baseline, they would have to develop a different bias unrelated to the initial one for new bias to occur due to correlated unmeasured sources of measurement errors.

5. However, we cannot completely eliminate the possibility of such new bias development. There could also be potential new sources of bias from directed effects of the exposure on the error term of the outcome, which can often occur due to panel attrition.

6. To mitigate this risk, we adjust for panel attrition/non-response using methods like multiple imputation. We also consistently perform sensitivity analyses to detect any unanticipated bias.

7. Despite these potential challenges, by including measures of both exposure and outcome at baseline, the chances of new confounding are significantly reduced.

8. Therefore, adopting this practice should be a standard procedure in multi-wave studies as it substantially minimises the probability of introducing novel confounding factors.

#### Comment on slow changes

Over long periods of time we can expect additional sources of confounding. Changes in cultural norms and attitudes can occur over the duration of a longitudinal study like the NZAVS, leading to residual confounding. For example, if there is a cultural shift towards increased acceptance of mental health issues, this might change how psychological distress is reported over time, irrespective of baseline responses.

```admonish tip title="Key Takeaway"
We must always perform sensitivity analyses because we can never be certain that our confounding control strategy has worked. The three-wave panel design with baseline measures of exposure and outcome substantially reduces, but does not eliminate, the risk of bias from measurement error.
```

## Appendix A: What is a Correlation Matrix?

A correlation matrix is a square matrix that contains the Pearson correlation coefficients between each pair of variables within a dataset. Each element in the matrix represents the correlation between two variables.

### Structure

- **Dimensions**: The matrix is $p \times p$ where $p$ is the number of variables.
- **Diagonal elements**: All diagonal elements are 1, because each variable has a perfect correlation with itself.
- **Off-diagonal elements**: These elements, denoted as $r_{ij}$, are the Pearson correlation coefficients between the $i^{th}$ and $j^{th}$ variables, ranging from -1 to +1.
  - $r_{ij} = 1$ indicates a perfect positive linear relationship.
  - $r_{ij} = -1$ indicates a perfect negative linear relationship.
  - $r_{ij} = 0$ indicates no linear relationship.

### Properties

- **Symmetry**: The matrix is symmetric around the diagonal, meaning $r_{ij} = r_{ji}$.
- **Real values**: All entries are real numbers.
- **Bounded values**: Values are constrained between -1 and 1, inclusive.

### Use

- Exploring relationships between variables.
- Conducting factor analysis to identify latent factors.

```r
# compute the correlation matrix
library(margot)
library(tidyverse)

dt_only_k6 <- df_nz |>
  dplyr::filter(wave == 2018) |>
  dplyr::select(
    kessler_depressed,
    kessler_effort,
    kessler_hopeless,
    kessler_worthless,
    kessler_nervous,
    kessler_restless
  )

cor_matrix <- cor(dt_only_k6, use = "pairwise.complete.obs", method = "pearson")
print(round(cor_matrix, 2))
```
