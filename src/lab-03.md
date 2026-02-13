# Lab 3: Regression, Graphing, and Simulation

## Simulating data: Outcome ~ Treatment

### Step 1: Set up your R environment

Ensure R or RStudio is installed and open.

### Step 2: Set a seed for reproducibility

```r
set.seed(123)
```

### Step 3: Simulate continuous data

```r
n <- 100
mean <- 50
sd <- 10
data_continuous <- rnorm(n, mean, sd)
head(data_continuous)
hist(data_continuous)
```

### Step 4: Simulate categorical data

```r
levels <- c("Male", "Female")
data_categorical <- sample(levels, n, replace = TRUE)
table(data_categorical)

# with unequal probabilities
data_categorical_unequal <- sample(levels, n, replace = TRUE, prob = c(0.3, 0.7))
table(data_categorical_unequal)
```

## Simulating outcomes from treatments

```r
library(tidyverse)
library(ggplot2)
library(patchwork)
library(parameters)
library(report)
library(ggeffects)

set.seed(123)
groupA_scores <- rnorm(100, mean = 100, sd = 15)
groupB_scores <- rnorm(100, mean = 105, sd = 15)

df_scores <- data.frame(
  group = rep(c("A", "B"), each = 100),
  scores = c(groupA_scores, groupB_scores)
)

df_scores_1 <- df_scores |>
  mutate(group = as.factor(group))
```

## Visualising simulated data

```r
ggplot(df_scores_1, aes(x = group, y = scores, fill = group)) +
  geom_boxplot() +
  theme_minimal() +
  labs(title = "score distribution by group", x = "group", y = "scores")
```

## Histogram

```r
ggplot(df_scores_1, aes(x = scores, fill = group)) +
  geom_histogram(binwidth = 5, color = "black") +
  labs(title = "distribution of scores by group", x = "scores", y = "frequency") +
  facet_wrap(~group, ncol = 1) +
  theme_minimal()
```

## Exercise 1

1. Modify the simulation parameters to change each group's mean and standard deviation. Observe how these changes affect the distribution.
2. Experiment with different bin widths in the histogram. How do large and small bin widths speak differently to the data?

## Simulating data for statistical tests

```r
# one-sample t-test
set.seed(123)
data <- rnorm(100, mean = 5, sd = 1)
mod_first_test <- t.test(data, mu = 4)
parameters::parameters(mod_first_test)
report::report(mod_first_test)

# two-sample t-test
group1 <- rnorm(50, mean = 5, sd = 1)
group2 <- rnorm(50, mean = 5.5, sd = 1)
mod_t_test_result <- t.test(group1, group2)
report::report(mod_t_test_result)

# paired t-test
pre_test <- rnorm(30, mean = 80, sd = 10)
post_test <- rnorm(30, mean = pre_test + 5, sd = 5)
mod_pre_post <- t.test(pre_test, post_test, paired = TRUE)
report::report(mod_pre_post)
```

## Understanding regression using simulation

```r
set.seed(123)
n <- 100

treatment <- rnorm(n, mean = 50, sd = 10)
beta_a <- 2
outcome <- 5 + beta_a * treatment + rnorm(n, mean = 0, sd = 20)

df <- data.frame(treatment = treatment, outcome = outcome)

fit <- lm(outcome ~ treatment, data = df)
summary(fit)

predicted_values <- ggeffects::ggemmeans(fit, terms = c("treatment"))
plot(predicted_values, dot_alpha = 0.35, show_data = TRUE, jitter = .1)
```

## Equivalence of ANOVA and regression

```r
set.seed(123)
n <- 90
k <- 3

group <- factor(rep(1:k, each = n/k))
means <- c(100, 100, 220)
sd <- 15
y <- rnorm(n, mean = rep(means, each = n/k), sd = sd)
df_1 <- cbind.data.frame(y, group)

# ANOVA
anova_model <- aov(y ~ group, data = df_1)
report::report(anova_model)

# regression (equivalent)
fit_regression <- lm(y ~ group, data = df_1)
parameters::model_parameters(fit_regression)
```

## Combination plots

```r
library(patchwork)

coefficient_plot <- plot(parameters::model_parameters(fit_regression))

predictive_plot <- plot(
  ggeffects::ggpredict(fit_regression, terms = "group"),
  dot_alpha = 0.35, show_data = TRUE, jitter = .1, colors = "reefs"
) +
  scale_y_continuous(limits = c(0, 260)) +
  labs(title = "predictive graph", x = "treatment group", y = "response")

my_first_combination_plot <- coefficient_plot / predictive_plot +
  plot_annotation(title = "coefficient and predictive plots", tag_levels = "A")

my_first_combination_plot
```

## Upshot

ANOVA partitions variance into between-group and within-group components. Regression estimates the mean of the dependent variable as a linear function of independent variables. For many questions ANOVA is appropriate, but when comparing groups we often want finer-grained comparisons. Note: comparisons do not establish causality.

## Exercise 2

1. Simulate two continuous variables `Y` and `A` with `n = 100`. Variable `A` should have mean 50, sd 10.
2. Define the effect of `A` on `Y` as `beta_A <- 2`.
3. Include an error term with sd 20.
4. Run `lm(Y ~ A)` and report results.

Template:

```r
library(parameters)
set.seed()  # pick a number

n <- 100
A <- rnorm(n, mean = 50, sd = 10)
beta_A <- 2

df_3 <- data.frame(
  A = A,
  Y = 5 + beta_A * A + rnorm(n, mean = 0, sd = 20)
)

fit_3 <- lm(Y ~ A, data = df_3)
parameters::model_parameters(fit_3)
report::report(fit_3)
```

---

# What you have learned

- **Data simulation:** simulating datasets in R for exploring statistical concepts and causal inference.
- **Data visualisation:** expanding your capacity for visualisation with ggplot2.
- **Statistical tests:** t-tests, ANOVA, and regression.
- **ANOVA and regression:** their equivalence and when to prefer regression.

## Package references

- [ggplot2](https://ggplot2.tidyverse.org/): declarative graphics based on the Grammar of Graphics.
- [Parameters](https://easystats.github.io/parameters/): utilities for processing model parameters.
- [Report](https://easystats.github.io/report/index.html): automated report generation from statistical models.

---

# Appendix A: Solution to Exercise 2

```r
library(parameters)
set.seed(12345)

n <- 100
A <- rnorm(n, mean = 50, sd = 10)
beta_A <- 2

df_3 <- data.frame(
  A = rnorm(n, mean = 50, sd = 10),
  Y = 5 + beta_A * A + rnorm(n, mean = 0, sd = 20)
)

fit_3 <- lm(Y ~ A, data = df_3)
parameters::model_parameters(fit_3)
report::report(fit_3)
```

# Appendix B: ANOVA post hoc comparisons

```r
tukey_post_hoc <- TukeyHSD(anova_model)
print(tukey_post_hoc)
plot(tukey_post_hoc)
```

# Appendix C: Adding complexity in simulation

```r
data_frame <- data.frame(
  ID = 1:n,
  Gender = sample(c("Male", "Female"), n, replace = TRUE),
  Age = rnorm(n, mean = 30, sd = 5),
  Income = rnorm(n, mean = 50000, sd = 10000)
)

# income as a function of age
intercept <- 20000
beta_age <- 1500
error_sd <- 10000
Age <- rnorm(n, mean = 30, sd = 5)
Income <- intercept + beta_age * Age + rnorm(n, mean = 0, sd = error_sd)
data_complex <- data.frame(Age, Income)

ggplot(data_complex, aes(x = Age, y = Income)) +
  geom_point() +
  theme_minimal() +
  labs(title = "simulated age vs. income", x = "age", y = "income")
```

# Appendix D: Causal Inference Glossary

[Download the glossary of key terms in causal inference (PDF)](figures/glossary.pdf)
