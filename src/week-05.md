# Week 5: Causal Inference: Average Treatment Effects

**Date: 25 Mar 2026**

```admonish note title="Readings"
**Required**
- Hernan, M. A. & Robins, J. M. (2024). *Causal Inference: What If.* Chapters 1--3. [link](https://www.dropbox.com/scl/fi/9hy6xw1g1o4yz94ip8cvd/hernanrobins_WhatIf_2jan24.pdf?rlkey=8eaw6lqhmes7ddepuriwk5xk9&dl=0)

**Optional**
- Neal, B. (2020). *Introduction to Causal Inference.* Chapters 1--2. [link](https://www.dropbox.com/scl/fi/9hy6xw1g1o4yz94ip8cvd/hernanrobins_WhatIf_2jan24.pdf?rlkey=8eaw6lqhmes7ddepuriwk5xk9&dl=0)
```

```admonish warning title="Key concepts for the test(s)"
- **The Fundamental Problem of Causal Inference**
- **Causal Inference in Randomised Experiments**
- **Causal Inference in Observational Studies: Average (Marginal) Treatment Effects**
- **Three Fundamental Assumptions for Causal Inference**
```

```admonish important title="Lab"
For the lab, copy and paste code chunks following the "Part 2: Lab" section.
```

```admonish note title="Terminology"
In these notes, we use the terms "counterfactual outcomes" and "potential outcomes" interchangeably.
```

---

## Seminar

### Learning Outcomes

- You will understand why causation is never directly observed.
- You will understand how experiments address this "causal gap."
- You will understand how applying three principles from experimental research allows human scientists to close this "causal gap" when making inferences about a population as a whole, that is, inferences about "marginal effects."

### Opening

```admonish quote title="Robert Frost"
> Two roads diverged in a yellow wood,
> And sorry I could not travel both
> And be one traveler, long I stood
> And looked down one as far as I could
> To where it bent in the undergrowth;
>
> Then took the other, as just as fair,
> And having perhaps the better claim,
> Because it was grassy and wanted wear;
> Though as for that the passing there
> Had worn them really about the same,
>
> And both that morning equally lay
> In leaves no step had trodden black.
> Oh, I kept the first for another day!
> Yet knowing how way leads on to way,
> I doubted if I should ever come back.
>
> I shall be telling this with a sigh
> Somewhere ages and ages hence:
> Two roads diverged in a wood, and I --
> I took the one less traveled by,
> And that has made all the difference.
>
> -- *The Road Not Taken*
```

### Introduction: Motivating Example

Consider the following cross-cultural question:

> Does bilingualism improve cognitive abilities in children?

There is evidence that bilingual children perform better at cognitive tasks, but is this improvement truly caused by learning more than one language, or is it confounded by other factors (e.g., cultural environment, parental influence)? How can we know? Each child might answer, like the traveller in Frost's poem:

**"And sorry I could not travel both. And be one traveler $\dots$"**

### Part 1: The Fundamental Problem of Causal Inference as a Missing Data Problem

**The fundamental problem of causal inference** is that causality is never directly observed.

Let $Y$ and $A$ denote random variables.

We formulate a causal question by asking whether experiencing an exposure $A$, when this exposure is set to level $A = a$, would lead to a difference in $Y$, compared to what would have occurred had the exposure been set to a different level, say $A=a'$ will lead to a difference in outcome $Y$. For simplicity, we imagine binary exposure such that $A = 1$ denotes receiving the "bilingual" exposure and $A = 0$ denotes receiving the "monolingual" exposure. Assume these are the only two exposures of interest:

Let:

- $Y_i(a = 1)$ denote the cognitive ability of child $i$ if the child were bilingual (potential outcome when $A_i = 1$).
- $Y_i(a = 0)$ denote the cognitive ability of child $i$ if the child were monolingual (potential outcome when $A_i = 0$).

What does it mean to *quantify* a causal effect? We may define the individual-level causal effect of bilingualism on cognitive ability for child $i$ as the difference between two states of the world: one for which the child experiences a bilingual exposure and the other for which the child does not. We write this contrast by referring to the potential outcomes under different levels of exposure:

$$
\text{Causal Effect}_i = Y_i(1) - Y_i(0).
$$

We say there is a causal effect of the bilingual exposure if

$$
Y_i(1) - Y_i(0) \neq 0.
$$

Because each child experiences only **one** exposure condition in reality, we cannot directly compute this difference from any dataset. The missing observation is called the **counterfactual**:

- If $Y_i|A_i = 1$ is observed, then $Y_i(0)|A_i=1$ is counterfactual.
- If $Y_i|A_i = 0$ is observed, then $Y_i(1)|A_i=1$ is counterfactual.

**"And sorry I could not travel both / And be one traveler, long I stood $\dots$"**

In short, individuals cannot simultaneously experience both exposure conditions, so one outcome is inevitably missing.

### How can we make contrasts between counterfactual (potential) outcomes?

#### Fundamental Assumption 1: Causal Consistency

Causal consistency means that the potential outcome corresponding to the exposure an individual actually receives is exactly what we observe. In other words, if individual $i$ receives exposure $a$, then the *potential outcome* (or equivalently the *counterfactual outcome* under a given level of exposure $A=a$, that is $Y_i(a)$) is equivalent to the *observed outcome*: $Y_i \mid A_i \equiv a$. Where the symbol $\equiv$ means "equivalent to", when we assume that the causal consistency assumption is satisfied, we assume that:

$$
\begin{aligned}
\underbrace{Y_i(1)}_{\text{counterfactual}} &\equiv \underbrace{(Y_i \mid A_i = 1)}_{\text{observable}}, \\
\underbrace{Y_i(0)}_{\text{counterfactual}} &\equiv \underbrace{(Y_i \mid A_i = 0)}_{\text{observable}}.
\end{aligned}
$$

Notice however that we cannot generally obtain individual causal effects because at any given time, each individual may only receive at most one level of an exposure. Where the symbol $\implies$ means "implies," at any given time, receiving one level of an exposure precludes receiving any other level of that exposure:

$$
Y_i|A_i = 1 \implies Y_i(0)|A_i = 1~ \text{is counterfactual}
$$

Likewise:

$$
Y_i|A_i = 0 \implies Y_i(1)|A_i = 1~ \text{is counterfactual}
$$

Because of the laws of physics (above the atomic scale), an individual can experience only one exposure level at any moment. Consequently, we can observe only one of the two counterfactual outcomes needed to quantify a causal effect. This is the fundamental problem of causal inference. Counterfactual contrasts cannot be individually observed.

However, because of the causal consistency assumption, we can nevertheless recover half of the missing counterfactual (or "potential") outcomes needed to estimate average treatment effects. We may do this if two other assumptions are satisfied.

#### Fundamental Assumption 2: Exchangeability

Exchangeability justifies recovering unobserved counterfactuals from observed outcomes and averaging them. By accepting that $Y_i(a) = Y_i$ if $A_i = a$, we can estimate population-level average potential outcomes. In an experiment where exposure groups are comparable, we define the Average Treatment Effect (ATE) as:

$$
\begin{aligned}
\text{ATE} &= \mathbb{E}[Y(1)] - \mathbb{E}[Y(0)] \\
           &= \mathbb{E}(Y \mid A=1) \;-\; \mathbb{E}(Y \mid A=0).
\end{aligned}
$$

Because randomisation ensures that missing counterfactuals are exchangeable with those observed, we can still estimate $\mathbb{E}[Y(a)]$. For instance, assume:

$$
 \underbrace{\mathbb{E}[Y(1)\mid A=1]}_{\text{counterfactual}}  = \textcolor{red}{\underbrace{\mathbb{E}[Y(1)\mid A=0]}_{\text{unobservable}}}  =  \underbrace{(Y_i \mid A_i = 1)}_{\text{observed}}
$$

which lets us infer the average outcome if everyone were treated. Likewise, if

$$
 \underbrace{\mathbb{E}[Y(0)\mid A=0]}_{\text{counterfactual}}  = \textcolor{red}{\underbrace{\mathbb{E}[Y(1)\mid A=0]}_{\text{unobservable}}}  =  \underbrace{\mathbb{E}(Y \mid A_i = 0)}_{\text{observed}}
$$

then we can infer the average outcome if everyone were given the control. The difference between these two quantities gives the ATE:

$$
\text{ATE} = \Big[
\overbrace{\mathbb{E}[Y(1)\mid A=1]}^{\substack{\text{by consistency:}\\ \equiv \text{ observed } \; \mathbb{E}[Y\mid A=1]}}
\;+\;
\overbrace{\textcolor{red}{\mathbb{E}[Y(1)\mid A=0]}}^{\substack{\text{by exchangeability:}\\ \text{unobservable, yet } \; \equiv \mathbb{E}[Y\mid A=1]}}
\Big]
-\,
\Big[
\overbrace{\mathbb{E}[Y(0)\mid A=0]}^{\substack{\text{by consistency:}\\ \equiv  \text{observed } \; \mathbb{E}[Y\mid A=0]}}
\;+\;
\overbrace{\textcolor{red}{\mathbb{E}[Y(0)\mid A=1]}}^{\substack{\text{by exchangeability:}\\ \text{unobservable, yet } \; \equiv \mathbb{E}[Y\mid A=0]}}
\Big]
$$

We have it that $\mathbb{E}[Y\mid A=1]$ and $\mathbb{E}[Y\mid A=0]$ and $\mathbb{E}[Y(1)\mid A=0]$ are observed. If both consistency and exchangeability are satisfied then we may use these observed quantities to identify contrasts of counterfactual quantities.

Thus, although individual-level counterfactuals are missing, the consistency assumption and the exchangeability assumption allow us to identify the average effect of treatment using observed data. Randomised controlled experiments allow us to meet these assumptions. Randomisation warrants the exchangeability assumption. Control warrants the consistency assumption.

#### Fundamental Assumption 3: Positivity

There is one further assumption, called positivity. It states that treatment assignments cannot be deterministic. That is, for every covariate pattern $L = l$, each individual has a non-zero probability of receiving every treatment level to be compared:

$$
P(A = a \mid L = l) > 0.
$$

Randomised experiments achieve positivity by design, at least for the sample that is selected into the study. In observational settings violations occur if some subgroups never receive a particular treatment. If treatments occur but are rare, we may have sufficient data from which to obtain convincing causal inferences.

Positivity is the only assumption that can be verified with data. We will consider how to assess this assumption using data when we develop our data analytic workflows in the second half of this course.

### Challenges with Observational Data

#### 1. Satisfying Causal Consistency is Difficult in Observational Settings

Below are some ways in which real-world complexities can violate causal consistency in observational studies. Causal consistency requires there is no interference between units (also called "SUTVA" or "Stable Unit Treatment Value"). Causal consistency also requires that each treatment level is well-defined and applied uniformly. If these conditions fail, then $Y(a)$ may not reflect a consistent exposure across individuals. We are then comparing apples with oranges. Consider some examples:

1. **Cultural differences**: one group's "second-language exposure" may differ qualitatively from another's if cultural norms shape how, when, or by whom the second language is taught. A child in a bilingual community may receive diverse and immersive language experiences all of which are coded as $A=1$. Yet the treatments might be quite different. We might be comparing apples with oranges.

2. **Age of acquisition**: the developmental effect of learning a second language may vary by when the child is exposed. Comparing acquisition at, say, age two with acquisition at, say, age twelve might be comparing apples with oranges.

3. **Language variation**: sign languages, highly tonal languages, or unwritten languages may demand different cognitive tasks than spoken, nontonal, or widely documented languages. Lumping them together as "learning a second language" can obscure the fact that these distinct exposures might produce fundamentally different outcomes. Again, comparisons here would be of apples with oranges.

These sources of heterogeneity reveal why careful delineation of treatments is crucial. If the actual exposures differ across individuals, then consistency $(Y_i(a) = Y_i \mid A_i = a)$ may fail, because $A=a$ is not the same phenomenon for everyone.

#### 2. Conditional Exchangeability (No Unmeasured Confounding) Is Difficult to Achieve

In theory, we can identify a causal effect from observational data if all confounders $L$ are measured. Formally, we need the potential outcomes to be independent of treatment once we condition on $L$. One way to express this assumption is: $Y(a) \coprod A \mid L$. If the potential outcomes are independent of treatment assignment, we can identify the Average Treatment Effect (ATE) as:

$$
\text{ATE} \;=\; \sum_{l}
\Bigl[\mathbb{E}\bigl(Y \mid A=1, L=l\bigr) \;-\; \mathbb{E}\bigl(Y \mid A=0, L=l\bigr)\Bigr] \;\Pr(L=l).
$$

In randomised experiments, conditioning is automatic because $A$ is unrelated to potential outcomes by design. In observational studies, ensuring or approximating such **conditional exchangeability** is often difficult. For example, bilingualism research would need to consider:

- **Cultural histories**: cultures that value language acquisition might also value knowledge acquisition. Associations might arise from culture, not causation.
- **Personal values**: families who place a high priority on bilingualism may also promote other developmental resources.

If important confounders go unmeasured or are poorly measured, these differences can bias causal effect estimates.

#### 3. The Positivity Assumption May Fail: Treatments Might Not Exist for All

Positivity requires that each individual could, in principle, receive *any* exposure level. In real-world observational settings, some groups have no access to bilingual education (or no reason to be monolingual), making certain treatment levels impossible for them. If a treatment level does not appear in the data for a given subgroup, any causal effect estimate for that subgroup is purely an extrapolation (Westreich & Cole, 2010; Hernan & Robins, 2023).

### Summary

We introduced the fundamental problem of causal inference by distinguishing correlation (associations in the data) from causation (contrasts between potential outcomes, of which only one can be observed for each individual).

**Randomised experiments** address this problem by balancing confounding variables across treatment levels. Although individual causal effects are unobservable, random assignment allows us to infer **average** causal effects, also called *marginal* effects.

In **observational data**, inferring average treatment effects demands that we satisfy three assumptions that are automatically satisfied in (well-conducted) experiments: **causal consistency**, **exchangeability**, and **positivity**. These assumptions ensure that we can compare like-with-like (that the population-level treatment effect is consistent across individuals), that there are no unmeasured common causes of the exposure and outcomes that may lead to associations in the absence of causality, and that every exposure level is a real possibility for each subgroup.

---

## Part 2: Lab

#### Focus

- Application of regression and simulation in R for ATE estimation

#### Setting up your R script

First, reinstall the `margot` package: <https://go-bayes.github.io/margot/>

```r
# functions explained here: https://go-bayes.github.io/margot/

# installation
if (!require(devtools, quietly = TRUE)) {
  install.packages("devtools")
  library(devtools)
}

# reinstall the `margot` package with updates
# devtools::install_github("go-bayes/margot", quietly = TRUE)

# call package
library("margot")
library("tidyverse")
library("parameters")
library("skimr")
library("haven")
library("stdReg")
library('mice')
library("clarify")

# uncomment and check simulated data
# head(df_nz)
```

#### Download a copy of the data directory from the NZAVS OSF website

Find the data directory here: <https://osf.io/75snb/>

The variables in the simulated data `df_nz` correspond to a subset of the variables in this directory.

#### Check N

```r
# check total n in data
# total nzavs participants
n_total <- skimr::n_unique(df_nz$id)
print(n_total)
```

#### Get data into shape for analysis

```r
#### data wrangling: select columns and transpose the data from long to wide
library(tidyverse)

# filter the original dataset for these IDs three waves
df_nz <- as.data.frame(df_nz)
df_nz <- haven::zap_formats(df_nz)
df_nz <- haven::zap_label(df_nz)
df_nz <- haven::zap_widths(df_nz)

name_exposure <-  "perfectionism"

# obtain ids for individuals who participated in 2018 and have no missing baseline exposure
ids_2018 <- df_nz %>%
   dplyr::filter(year_measured == 1, wave == 2018) %>%
   dplyr::filter(!is.na(!!sym(name_exposure))) |>
  pull(id)

# obtain ids for individuals who participated in 2019
ids_2019 <- df_nz %>%
   dplyr::filter(year_measured == 1, wave == 2019) %>%
   dplyr::filter(!is.na(!!sym(name_exposure))) |>
  pull(id)

# intersect IDs from 2018 and 2019 to ensure participation in both years
ids_2018_2019 <- intersect(ids_2018, ids_2019)


# data wrangling
dat_long <- df_nz |>
  dplyr::filter(id %in% ids_2018_2019 &
                  wave %in% c(2018, 2019, 2020)) |>
  arrange(id, wave) |>
  select(
    "id",
    "wave",
    "year_measured",
    "age",
    "male",
    "born_nz",
    "eth_cat",
    "employed",
    "edu",
    "kessler6_sum",
    "household_inc",
    "partner",
    "parent",
    "political_conservative",
    "hours_exercise",
    "agreeableness",
    "conscientiousness",
    "extraversion",
    "honesty_humility",
    "openness",
    "neuroticism",
    "modesty",
    "sample_weights",
    "neighbourhood_community",
    "belong",
    "rural_gch_2018_l",
    "support",
    "perfectionism",
    "kessler6_sum"
  ) |>
  mutate(
    # initialise 'censored'
    censored = ifelse(lead(year_measured) == 1, 1, 0),

    # modify 'censored' based on the condition
    censored =  ifelse(is.na(censored) &
                         year_measured == 1, 1, censored)
  ) |>
  select(-year_measured) |>
  dplyr::mutate(
    household_inc_log = log(household_inc + 1),
    hours_exercise_log = log(hours_exercise + 1)  ) |>
  dplyr::select(
    -c(
      household_inc,
      hours_exercise
    )
  ) |>
  droplevels() |>
  arrange(id, wave) |>
  droplevels() |>
  data.frame() |>
  droplevels() |>
  arrange(id, wave) |>
  mutate(
  rural_gch_2018_l = as.numeric(as.character(rural_gch_2018_l)),
  partner = as.numeric(as.character(partner)),
  born_nz = as.numeric(as.character(born_nz)),
  censored = as.numeric(as.character(censored)),
  employed = as.numeric(as.character(employed))
  ) |>
  droplevels() |>
  arrange(id, wave) |>
  data.frame()

# check n in this sample
skimr::n_unique(dat_long$id)

# check
# head(dat_long)
```

#### Convert data from long to wide and impute baseline values

```r
# baseline variables
baseline_vars = c("age", "male", "edu", "partner", "employed")
exposure_var = c("perfectionism", "censored") # we will use the censored variable later
outcome_vars = c("kessler6_sum")


# function will add exposure and outcome to baseline
dat_long_wide <- margot::margot_wide_impute_baseline(dat_long, baseline_vars = baseline_vars, exposure_var = exposure_var, outcome_vars =outcome_vars)

# check
head(dat_long_wide)

# standardise vars
dt <- dat_long_wide |>
  dplyr::mutate(
    t0_age_z = scale(t0_age),
    t0_edu_z = scale(t0_edu),
    t0_kessler6_sum_z =  scale(t0_kessler6_sum),
    t0_perfectionism_z = scale(t0_perfectionism),
    t1_perfectionism_z = scale(t1_perfectionism),
    t2_kessler6_sum_z = scale(t2_kessler6_sum)) |>
  data.frame()

# we are not handling missing data well, and this will throw bugs downstream
# to avoid this problem we remove attributes
dt <- margot::remove_numeric_attributes(dt)
```

#### Use regression to obtain *conditional average treatment* effect

```r
# note that we are not handling missing data appropriately here

# take interaction of treatment and baseline vars
fit_1 <- glm(t2_kessler6_sum_z  ~ t1_perfectionism_z * (t0_age_z +  t0_edu_z + t0_kessler6_sum_z + t0_perfectionism_z), data = dt)

# summarise
regression_table  <- parameters::model_parameters(fit_1)
```

This regression coefficient has a *causal* interpretation (it is the ATE, at the value when all variables in the model are set to zero):

```r
regression_table[2, ]
```

#### Average Treatment Effect

First we will use the `stdReg` package:

```r
# fit the ate
fit_ate <- stdReg::stdGlm(fit_1, data = dt, X = "t1_perfectionism_z", x = seq(0, 1))
print(summary(fit_ate))

# summarise the ate
ate_stdreg_method <- summary(fit_ate,  contrast = "difference",  reference = 0)

print(ate_stdreg_method)
# graph ate using stdReg method
plot(ate_stdreg_method)
```

##### Obtain ATE by hand using g-computation

```r
# recall our fit
fit_1 <- glm(t2_kessler6_sum_z ~ t1_perfectionism_z + t0_age_z + t0_edu_z + t0_kessler6_sum_z + t0_perfectionism_z, data = dt, family = gaussian())

# step 2: create a new dataset where everyone receives the treatment
dt_treated <- dt
dt_treated$t1_perfectionism_z <- mean(dt$t1_perfectionism_z)  # setting treatment to the mean can be adjusted as needed

# step 3: predict outcomes under the treatment scenario
dt_treated$predicted_outcome <- predict(fit_1, newdata = dt_treated, type = "response")

# step 4: calculate the mean outcome under this treatment scenario
mean_outcome_treated <- mean(dt_treated$predicted_outcome)

# repeat steps 2-4 for control or another treatment level
dt_control <- dt
dt_control$t1_perfectionism_z <- 1  # one sd increase in perfectionism

dt_control$predicted_outcome <- predict(fit_1, newdata = dt_control, type = "response")
mean_outcome_control <- mean(dt_control$predicted_outcome)

# step 5: calculate the average treatment effect
ate_gcomp <- round(mean_outcome_treated - mean_outcome_control, 2)
print(paste("Average Treatment Effect (ATE) of t1_perfectionism_z using g-computation: ", ate_gcomp))
```

##### Next obtain the ATE and standard errors by simulation using `clarify`

```r
library("clarify")

# setup simulation with clarify to manipulate the treatment variable and predict outcomes
set.seed(123)
sim_coefs <- sim(fit_1)

# compute ate
sim_est <- sim_ame(sim_coefs, contrast = "diff", var =  "t1_perfectionism_z")

# summarise
summary(sim_est)
```

---

## Appendix A: Notation Variants

Consider that in the causal inference literature, we may write the contrast of two potential outcomes under treatment as:

$$
\text{Causal Effect}_i = Y_i^{a=1} - Y_i^{a=0}
$$

or

$$
\text{Causal Effect}_i = Y_i^{1} - Y_i^{0}
$$

or:

$$
\text{Causal Effect}_i = Y_{1} - Y_0
$$

Where subscripts are dropped. You will soon encounter many notational variants across sources.
