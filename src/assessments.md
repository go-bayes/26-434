# Assessments

## Assessment 1: Lab Diaries (10%)

Eight weekly lab diaries submitted across the teaching weeks. Each diary is marked satisfactory/not satisfactory (1.25% each, full credit for satisfactory). Labs take place in the final 60–90 minutes of the seminar. Coding itself is not marked; the diary records progress and engagement.

## Assessment 2: In-Class Test 1 (20%) — 22 April

Covers material from weeks 1–6 (causal diagrams, confounding, ATE, effect modification). Test duration is 50 minutes. The allocated time is 1 hour 50 minutes. Required: pen/pencil. No devices permitted.

```admonish warning title="Test Location"
The test is in class. Come to the seminar room (EA120) with a writing instrument.
```

## Assessment 3: In-Class Test 2 (20%) — 20 May

Covers material from weeks 8–10 (machine learning, IPTW, measurement, Quarto workflows). Same format and conditions as test 1.

## Assessment 4: In-Class Presentation (10%) — 27 May

10-minute presentation summarising the student's study. Assessment criteria: clarity, efficiency, and quality of presentation.

## Assessment 5: Research Report (40%) — Due 30 May

```admonish info title="Research Report"
We will supply the data. Lab sessions are designed to support you in this assignment. We assume no statistical background.
```

Students choose **one** of two formats and must declare their choice by end of w6 (2 April).

### Option A: Research Report

Quantify the Average Treatment Effect of a specific exposure using the NZAVS longitudinal synthetic dataset.

- **Introduction:** 1,500-word limit.
- **Conclusion:** 1,500-word limit.
- **Methods/Results:** concise, no specific word limit.
- APA style. Submit as a single PDF with R code appendix.

#### Assessment Criteria (Option A)

**Stating the problem.** State your question clearly. Explain its scientific importance. Frame it as a causal question. Identify any subgroup analysis (e.g. cultural group). Explain the causal inference framework for non-specialists. Describe ethics/policy relevance. Confirm data are from the NZAVS simulated dataset using three waves.

**Determining the outcome.** Define outcome variable $Y$. Assess multiple outcomes if applicable. Explain how outcomes relate to the question. Address outcome type (binary and rare?) and timing (after exposure).

**Determining the exposure.** Define exposure variable $A$. Explain relevance, positivity, consistency, and exchangeability. Specify exposure type (binary or continuous) and whether you contrast static interventions or modified treatment policies. Confirm exposure precedes outcome.

**Accounting for confounders.** Define baseline confounders $L$. Justify how they could affect both $A$ and $Y$. Confirm they are measured before exposure. Include baseline measures of exposure and outcome in confounder set. Assess sufficiency; explain sensitivity analysis (E-values) if needed. Address confounder type (z-scores for continuous; one-hot encoding for categorical with three or more levels).

**Drawing a causal diagram.** Include both measured and unmeasured confounders. Describe potential measurement error biases. Add time indicators ensuring correct temporal order.

**Identifying the estimand.** State your causal contrast clearly.

**Source and target populations.** Explain how your sample relates to your target populations.

**Eligibility criteria.** State the eligibility criteria for the study.

**Sample characteristics.** Provide descriptive statistics for baseline demographics. Describe magnitude of exposure change from baseline. Include references for more information about the sample. Make clear the data are simulated.

**Missing data.** Check for missing data. Describe how you will address the problem (IPCW).

**Model approach.** Decide between G-computation, IPTW, or doubly-robust estimation. Specify model. Explain how machine learning works. Address outcome specifics (logistic regression for rare binary outcomes; z-scores for continuous). Describe sensitivity analysis (E-values).

### Option B: Marsden Fund Expression of Interest (EOI)

Write a first-round Marsden Fund EOI following the RSNZ 2026 guidelines. The research question must use the causal inference framework taught in this course.

#### Required Sections (CV excluded)

1. **Research Title** (max 25 words, plain language)
2. **Research Summary** (max 200 words, plain language for non-specialists)
3. **Abstract** (one page: aim, importance, methods, key information)
4. **Benefit Statement** (one standalone page: why this research benefits New Zealand)
5. **References** (max 3 pages)
6. **Roles and Resources** (max one page: team and resources required)
7. **Vision Mātauranga** (200 words: relevance to the four VM themes, or justification for why not applicable)

**Formatting:** 12-point Times, single spacing, 2 cm margins.

#### Assessment Criteria (Option B)

- Novelty and ambition of the research question.
- Rigour of the proposed methods.
- Clarity of the benefit case.
- Quality of the causal reasoning.
- Adherence to EOI formatting requirements.
