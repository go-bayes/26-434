# Lab 5: G-Computation and the clarify Package

### Focus

- Application of regression and simulation in R for ATE estimation

### Setting up your R script

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

### Download a copy of the data directory from the NZAVS OSF website

Find the data directory here: <https://osf.io/75snb/>

The variables in the simulated data `df_nz` correspond to a subset of the variables in this directory.

### Check N

```r
# check total n in data
# total nzavs participants
n_total <- skimr::n_unique(df_nz$id)
print(n_total)
```

### Get data into shape for analysis

```r
### data wrangling: select columns and transpose the data from long to wide
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

### Convert data from long to wide and impute baseline values

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

### Use regression to obtain *conditional average treatment* effect

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

### Average Treatment Effect

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

