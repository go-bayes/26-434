# Lab 6: Data Wrangling and Effect Modification

## Lab Part 1: Setting up your data

## Set up your libraries

```r
library("margot")
library("tidyverse")
library("ggplot2")
library("here")
library("skimr")
if (!require(gtsummary)) install.packages("gtsummary")
if (!require(janitor)) install.packages("janitor")

# if you need to update the margot package, uncomment and do this
# devtools::install_github("go-bayes/margot")
```

## Set up a path to a folder in your directory

This will allow you to save the outputs of models and other information, which will be handy when you are producing your manuscript. Call this `push_mods`.

```r
# create a folder called saved and make a path like this
push_mods <- here::here("saved")

# view it (will be different for you)
push_mods

# another option
# saveRDS(object, here::here(push_mods, "object"))
```

## Initial Data Wrangling to select the study sample

### Where to find variable names information

Find it in the data directory here: <https://osf.io/75snb/>

Also see here: <https://github.com/go-bayes/templates/tree/main/method>

### Think: what are my eligibility criteria?

- Participated at baseline
- Participated at treatment wave
- May have been lost to follow up at the end of the study
- Full information on the treatment variable (think about this...)

```r
library(margot)
library(tidyverse)
library(table1)
library(gtsummary)

# eliminate haven labels
df_nz <- as.data.frame(df_nz)
df_nz <- haven::zap_formats(df_nz)
df_nz <- haven::zap_label(df_nz)
df_nz <- haven::zap_widths(df_nz)

# name output folder
push_mods <- here::here("outputs")

# set exposure name
name_exposure <- "perfectionism"

# obtain ids for individuals who participated in 2018
# and have no missing baseline exposure
ids_2018 <- df_nz |>
  dplyr::filter(year_measured == 1, wave == 2018) |>
  dplyr::filter(!is.na(!!sym(name_exposure))) |>
  pull(id)

# obtain ids for individuals who participated in 2019
ids_2019 <- df_nz |>
  dplyr::filter(year_measured == 1, wave == 2019) |>
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
    "household_inc",
    "partner",
    "parent",
    "alert_level_combined_lead",
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
    "religion_religious",
    "kessler_latent_depression",
    "kessler_latent_anxiety"
  ) |>
  mutate(
    # initialise 'censored'
    censored = ifelse(lead(year_measured) == 1, 1, 0),
    # modify 'censored' based on the condition
    censored = ifelse(is.na(censored) &
                        year_measured == 1, 1, censored)
  ) |>
  select(-year_measured) |>
  dplyr::mutate(
    # rescale these variables to get all variables on a similar scale
    # otherwise your models can blow up, or become uninterpretable
    household_inc_log = log(household_inc + 1),
    hours_exercise_log = log(hours_exercise + 1)
  ) |>
  dplyr::select(
    -c(
      household_inc,
      hours_exercise)
  ) |>
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
  data.frame() |>
  droplevels() |>
  arrange(id, wave) |>
  data.frame()

# check n in this sample
n_participants <- skimr::n_unique(dat_long$id)

# make number pretty
n_participants <- prettyNum(n_participants, big.mark = ",")

# save this so that you can use it in your manuscript
margot::here_save(n_participants, "n_participants")

# try reading
n_participants_did_it_work_question_mark <- margot::here_read("n_participants")
```

## Define your baseline covariates, treatment (exposure), and outcome

```r
# for example
baseline_vars <- c(
  "age", "male", "edu", "eth_cat", "partner", "employed",
  "born_nz", "neighbourhood_community", "household_inc_log",
  "parent", "religion_religious", "rural_gch_2018_l",
  "sample_weights", "employed", "alert_level_combined_lead"
)

# treatment
exposure_var <- c("perfectionism", "censored")

# outcome, can be many
outcome_vars <- c("kessler_latent_anxiety", "kessler_latent_depression")

# define waves
baseline_wave <- "2018"

# exposure waves
exposure_waves <- c("2018", "2019")

# outcome wave
outcome_wave <- "2020"

# exposure
name_exposure <- "perfectionism"
```

## Make your baseline table

```r
tables <- margot::margot_summary_tables(
  dat_long,
  exposure_waves = exposure_waves,
  baseline_wave = baseline_wave,
  outcome_wave = outcome_wave,
  name_exposure = name_exposure,
  baseline_vars = baseline_vars,
  outcome_vars = outcome_vars
)
```

### Baseline table

```r
tables$baseline_table
```

### Exposure table

```r
# tables$exposure_tables
```

### Outcome table

```r
tables$outcome_table
```

## Inspect the distribution of the exposure in treatment wave

```r
# select 2019 wave
dt_19 <- dat_long |> dplyr::filter(wave == 2019)

# mean
mean_exposure <- mean(dt_19$perfectionism, na.rm = TRUE)

# median
median_exposure <- median(dt_19$perfectionism, na.rm = TRUE)

# generate bar plot
graph_density_of_exposure_up <- margot::margot_plot_shift(
  dt_19,
  shift = "up",
  col_name = "perfectionism",
  binwidth = .25,
  range_highlight = c(0, mean_exposure)
)

# show
graph_density_of_exposure_up
```

## Check for change in the treatment (Positivity)

```r
# select data from wave 18 and 19
dt_18_19_positivity <- dat_long |>
  dplyr::filter(wave == 2018 | wave == 2019) |>
  dplyr::mutate(perfectionism_round = round(perfectionism, digits = 0)) |>
  dplyr::select(perfectionism_round, id, wave) |>
  droplevels()

out <- margot::margot_transition_table(
  data = dt_18_19_positivity,
  state_var = "perfectionism_round",
  id_var = "id",
  wave_var = "wave"
)

# table
out$tables[[1]]
```

### Explanation for the table

```r
cat(out$explanation)
```


## Lab Part 2: Simulation to Clarify Why the Distribution of Effect Modifiers Matters for Estimating Treatment Effects

First, we load the `stdReg` library, which obtains marginal effect estimates by simulating counterfactuals under different levels of treatment (Sjolander, 2016). If a treatment is continuous, the levels can be specified.

We also load the `parameters` library, which creates nice tables (Ludecke et al., 2020).

```r
# to obtain marginal effects
library(stdReg)
# to create nice tables
library(parameters)
```

Next, we write a function to simulate data for the sample and target populations.

We assume the treatment effect is the same in the sample and target population. We assume that the coefficient for the effect-modifier and the coefficient for interaction are the same. We assume no unmeasured confounding throughout the study. We assume only selective attrition of one effect modifier such that the baseline population differs from the sample population at the end of the study.

That is: **the distribution of effect modifiers is the only respect in which the sample will differ from the target population.**

This function will generate data under a range of scenarios.

```r
# function to generate data for the sample and population,
# along with precise sample weights for the population, there are differences
# in the distribution of the true effect modifier but no differences in the
# treatment effect or the effect modification. all that differs between the
# sample and the population is the distribution of effect-modifiers.

# reproducibility
set.seed(123)

# simulate the data -- you can use different parameters
data <- margot::simulate_ate_data_with_weights(
  n_sample = 10000,
  n_population = 100000,
  p_z_sample = 0.1,
  p_z_population = 0.5,
  beta_a = 1,
  beta_z = 2.5,
  noise_sd = 0.5
)

# view
str(data)
```

We have generated both sample and population data. Next, we verify that the distributions of effect modifiers differ in the sample and in the target population:

```r
# obtain the generated data
sample_data <- data$sample_data
population_data <- data$population_data

# check imbalance
table(sample_data$z_sample)         # type 1 is rare
table(population_data$z_population) # type 1 is common
```

Good, the distributions differ. The simulation is working as intended.

Next, consider the question: "What are the differences in the coefficients that we obtain from the study population at the end of study, as compared with the target population?"

First, we obtain the coefficients for the sample:

```r
# model coefficients sample
model_sample <- glm(y_sample ~ a_sample * z_sample, data = sample_data)

# summary
parameters::model_parameters(model_sample, ci_method = "wald")
```

Now let's obtain the coefficients for the weighted regression of the sample. Notice that the coefficients are virtually the same:

```r
# model the sample weighted to the population
model_weighted_sample <- glm(
  y_sample ~ a_sample * z_sample,
  data = sample_data,
  weights = weights
)

# summary
summary(parameters::model_parameters(model_weighted_sample, ci_method = "wald"))
```

We might be tempted to infer that weighting wasn't relevant to the analysis. However, we'll see that such an interpretation would be a mistake.

Next, let us obtain model coefficients for the population. Note again there is no difference, only narrower errors owing to the large sample size.

```r
# model coefficients population
model_population <- glm(
  y_population ~ a_population * z_population,
  data = population_data
)

parameters::model_parameters(model_population, ci_method = "wald")
```

Again, there is no difference. All model coefficients are practically equivalent. The different distribution of effect modifiers does not result in different coefficient values for the treatment effect, the effect-modifier "effect," or the interaction of effect modifier and treatment.

Consider why this is the case: in a large sample where the causal effects are invariant (as we have simulated them to be), we will have good replication in the effect modifiers within the sample, so our statistical model can recover the *coefficients* for the population without difficulty.

However, **in causal inference, we are interested in obtaining the marginal effect of the treatment**. That is, we seek an estimate for the counterfactual contrast in which everyone in a pre-specified population was subject to one level of treatment compared with a counterfactual condition in which everyone in a population was subject to another level of the same treatment. **When the sample population differs in the distribution of effect modifiers from the target population effect, the marginal effect estimates will typically differ.**

To see this, we use the `stdReg` package to recover marginal effect estimates, comparing (1) the sample ATE, (2) the true oracle ATE for the population, and (3) the weighted sample ATE. We will use the outputs of the same models above. The only difference is that we will calculate marginal effects from these outputs. We will contrast a difference from an intervention in which everyone receives treatment = 0 with one in which everyone receives treatment = 1; however, this choice is arbitrary, and the general lessons apply irrespective of the estimand.

First, consider the ATE for the sample population.

```r
# regression standardisation
library(stdReg)

# obtain sample ate
fit_std_sample <- stdReg::stdGlm(
  model_sample,
  data = sample_data,
  X = "a_sample"
)

# summary
summary(fit_std_sample, contrast = "difference", reference = 0)
```

The treatment effect is given as a 1.06 unit change in the outcome across the sample population, with a confidence interval from 1.04 to 1.08.

Next, we obtain the true (oracle) treatment effect for the population under the same intervention.

```r
# obtain true ate
fit_std_population <- stdReg::stdGlm(
  model_population,
  data = population_data,
  X = "a_population"
)

# summary
summary(fit_std_population, contrast = "difference", reference = 0)
```

Note: the true treatment effect is a 1.25 unit change in the population, with a confidence bound between 1.24 and 1.26. This is well outside the ATE that we obtain from the sample population.

Next, consider the ATE in the weighted regression, where the sample was weighted to the target population's true distribution of effect modifiers.

```r
# weighted ate where we correctly assign population weights to the sample
fit_std_weighted_sample_weights <- stdReg::stdGlm(
  model_weighted_sample,
  data = sample_data,
  X = "a_sample"
)

# this gives us the right answer
summary(fit_std_weighted_sample_weights, contrast = "difference", reference = 0)
```

We find that we obtain the population-level causal effect estimate with accurate coverage by weighting the sample to the target population. So with appropriate weights, our results generalise from the sample to the target population.


## Lessons from Lab 2

Regression coefficients do not clarify the problem of sample/target population mismatch, or selection bias as discussed here. The correct advice to investigators is that they should not rely on regression coefficients when evaluating the biases that arise from sample attrition. This advice applies to both methods that the authors use to investigate threats of bias. That is, to implement this advice, the authors must first take it.

Generally, observed data are insufficient for assessing threats. Observed data do not clarify structural sources of bias, nor do they clarify effect-modification across the full counterfactual data conditions, in which (1) all receive the treatment and (2) all do not receive the treatment (at the same level). To properly assess bias, one would need access to the counterfactual outcome, what would have happened to the missing participants had they not been lost to follow-up or had they responded. Again, the joint distributions over "full data" are inherently unobservable (van der Laan, 2011).

In simple settings like the one we just simulated, we may address the gap between the sample and target population using methods such as modelling the censoring (e.g., censoring weighting). However, we never know what setting we are in or whether it is simple; such modelling must be handled with care. There is a large and growing epidemiology literature on this topic (see, for example, Li et al., 2023).

