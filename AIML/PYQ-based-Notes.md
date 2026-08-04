# 📘 Machine Learning with Business Applications — Master Study Manual
### BDA/IDA · Term II · Built from Quiz 1, Quiz 2, Mid-Term & End-Term question papers

> **How to use this manual**: Each module explains the concept from scratch, shows the R output you'll see in the exam, works a full numeric example, and ends with an "Exam Keyword → Concept" lookup table. Search this document by module number during the open-book portions, and use the worked examples as templates for numerical questions.

---

## 🗂️ Table of Contents
1. [Module 1 — Fundamental Paradigms & Business ML Tasks](#m1)
2. [Module 2 — Model Evaluation, Validation & Data Leakage](#m2)
3. [Module 3 — Linear Regression & Causal/Predictive Modeling](#m3)
4. [Module 4 — Logistic Regression & Classification](#m4)
5. [Module 5 — Linear Discriminant Analysis (LDA)](#m5)
6. [Module 6 — Cluster Analysis](#m6)
7. [Module 7 — Dimensionality Reduction (PCA)](#m7)
8. [Module 8 — Association Rule Mining (Apriori)](#m8)
9. [Master Formula Sheet](#formulas)

---

<a id="m1"></a>
# Module 1 — Fundamental Paradigms & Business ML Tasks

## 1.1 The Three Learning Paradigms

| Paradigm | What it needs | What it does | Business example |
|---|---|---|---|
| **Supervised Learning** | Labeled data (input **X** + known output **Y**) | Learns a mapping X → Y to predict Y for new X | Predicting churn (Yes/No) from customer history |
| **Unsupervised Learning** | Unlabeled data (only **X**) | Finds structure/patterns with no "right answer" given | Segmenting customers into groups with no predefined labels |
| **Reinforcement Learning** | An agent, an environment, and rewards | Learns actions that maximize cumulative reward through trial and error | Dynamic pricing bots, ad-bidding agents |

> **Exam Tip**: If the question says *"labeled"*, *"historical outcome known"*, *"Fraud Yes/No column available"* → **Supervised**. If it says *"no labels available"*, *"discover natural groups"* → **Unsupervised**.

## 1.2 Mapping Business Problems → ML Tasks

| Business need | ML Task | Learning type |
|---|---|---|
| Predict a category (Yes/No, class A/B/C) | **Classification** | Supervised |
| Predict a continuous number (revenue, price, demand) | **Regression** | Supervised |
| Group similar records with no known groups | **Clustering** | Unsupervised |
| Flag rare/unusual points (fraud, defects) | **Anomaly Detection** | Usually unsupervised (sometimes supervised if labeled) |
| Suggest items, produce a ranked Top-K list | **Recommendation & Ranking** | Supervised or hybrid |
| Compress many features into fewer, denoised dimensions | **Dimensionality Reduction** | Unsupervised |

### Worked reasoning pattern (as tested in Quiz 1)
The quiz repeatedly gives a **business narrative** and asks "what's the primary task?" The trick is to identify **what the output looks like**:
- A **Yes/No decision per record**, made to hand off for review → **Classification**
- A **continuous number to forecast** → **Regression**
- **Groups discovered with no predefined labels** → **Clustering**
- A **ranked list of top items per user**, where *order* matters more than calibrated probabilities → **Recommendation + Ranking**
- **Compressing many correlated features into a handful of factors** → **Dimensionality Reduction**

> ⚠ **Exam Pitfall**: A "Top-10 personalized list" is **Recommendation + Ranking**, NOT plain Classification — even though a classifier might power it internally, the *primary task* is defined by the business deliverable (an ordered list), not the underlying algorithm.

> ⚠ **Exam Pitfall**: "Discover fraud/anomalies early, hand off to analysts" (rare, evolving cases, no fixed rule) → **Anomaly Detection**, not plain Classification — the tell is *rare + evolving + unusual-pattern detection* rather than a stable, well-labeled binary outcome.

## 1.3 Inference vs. Prediction

| | **Inference** | **Prediction** |
|---|---|---|
| Goal | Understand/explain the *relationship* between X and Y ("why") | Forecast Y accurately for new X ("what will happen") |
| Business question | "Which channels **drive** leads?" | "How many leads **will** we get next month?" |
| Model preference | Simpler, interpretable models preferred | Any model that predicts best, even if complex |

> **Exam Tip**: Look for the verb. *"Forecast"*, *"predict"*, *"estimate probability for new cases"* → **Prediction**. *"Explain"*, *"understand drivers"*, *"quantify the effect of X on Y controlling for..."* → **Inference**. A single business problem can require **both** (e.g., forecast next month's leads = Prediction; explain which channels drive leads = Inference) — these are two separate tasks even though they share the same dataset.

## 1.4 Accuracy–Interpretability Trade-off

- **Simple models** (Linear/Logistic Regression, single Decision Trees): easy to explain coefficient-by-coefficient, auditable, preferred in **regulated domains** (banking, healthcare, insurance) — but may sacrifice some raw predictive accuracy.
- **Complex "black-box" models** (Neural Networks, Random Forests, Gradient Boosting/Ensembles): often more accurate on complex patterns, but hard to explain to regulators/customers ("why was I denied this loan?").

> ⚠ **Common Wrong Answer**: "Complex models are always both more accurate AND more interpretable" — **FALSE**. The trade-off is real: gains in accuracy typically cost you interpretability, and vice versa. Also false: "complex models are never allowed in regulated industries" — they *can* be used, but require extra explainability tooling/justification.

### 🎯 How to Answer Exam Questions — Module 1

| Keyword in question | Concept / Answer |
|---|---|
| "no labels available", "discover natural groups" | Clustering (Unsupervised) |
| "predict Yes/No", "flag as X or Y" | Classification |
| "forecast a number/amount/demand" | Regression |
| "compress features", "reduce dimensions", "retain variance" | Dimensionality Reduction (PCA) |
| "ranked Top-K list", "order matters" | Recommendation + Ranking |
| "rare, evolving, unusual pattern, automated first-pass screen" | Anomaly Detection |
| "which factors drive/explain Y" | Inference |
| "forecast/estimate next period's Y" | Prediction |
| "must be explainable for auditors/regulators" | Prefer simple/interpretable model |

---

<a id="m2"></a>
# Module 2 — Model Evaluation, Validation & Data Leakage

## 2.1 Train / Validation / Test — Exact Roles

| Set | Used for | Touched how often? |
|---|---|---|
| **Train** | Fit model parameters (coefficients, splits, weights) | Repeatedly, during fitting |
| **Validation** | Tune hyperparameters, compare/select among candidate models | Repeatedly, during model selection |
| **Test** | Final, **unbiased** performance estimate — reported once | **Exactly once**, at the very end |

> ⚠ **Exam Pitfall (Quiz 1, Q1 & Q20)**: The test set's job is to give the *final unbiased performance estimate* — **not** to tune hyperparameters repeatedly (that's the validation set's job). Using test-set performance to pick a model creates **optimistic selection bias** — you're indirectly fitting to the test set.

> ⚠ **Exam Pitfall (Quiz 1, Q4)**: Selecting features using **all rows before** the train/validation/test split is a classic cause of **data leakage** and optimistically biased model selection — because information from what should be "unseen" data (validation/test) has already influenced which features you kept.

## 2.2 Cross-Validation

### k-Fold Cross-Validation
Split the data into `k` equal folds. Train on `k-1` folds, test on the remaining fold; repeat `k` times so every fold serves as the test set once. Average the `k` performance scores.

- **Why?** A single train/test split gives one noisy estimate. Averaging over `k` folds gives a **more stable, reliable** estimate of true model performance (Quiz 2, Q9).
- **Typical choice**: **5-fold or 10-fold** CV — a good balance of bias, variance, and computation time for moderately-sized datasets (e.g. 50,000 rows) (Quiz 2, Q18).

### Leave-One-Out Cross-Validation (LOOCV)
A special case where `k = n` (number of rows) — each fold has exactly **one** observation as the "test" set.

| | k-Fold (e.g. k=5 or 10) | LOOCV (k=n) |
|---|---|---|
| Bias | Slightly higher (less data per training fold) | Very low bias (almost all data used to train each time) |
| Variance of the estimate | Lower (folds are more different from each other) | Higher (folds highly overlap/correlated → estimate variance can be higher) |
| Computation cost | Moderate | Very high for large `n` (must refit the model `n` times) |

> ⚠ **Exam Pitfall (Quiz 2, Q18)**: For a large dataset (50,000 rows) with a moderately complex model, **LOOCV is usually a poor choice** due to extreme computation cost — **5- or 10-fold CV** is the practical, balanced answer.

## 2.3 Data Leakage & Pipeline Integration

**Rule**: Any transformation that "learns" something from the data (scaling parameters, PCA components, feature selection, imputation values) must be **fit only on the training fold**, then simply **applied** (`transform`, not `fit`) to the validation/test fold.

> ⚠ **Exam Pitfall (Quiz 2, Q7)**: When integrating PCA into k-fold Cross-Validation, the *correct* approach is to **compute PCA separately inside each training fold**, and apply that fold's PCA transform to its own test fold — **not** to compute PCA once on the full dataset (that leaks test-set structure into training).

> ⚠ **Exam Pitfall (Quiz 2, Q8)**: After using CV to select the best model/hyperparameters, do **NOT** report the CV score itself as your final unbiased estimate — instead, **refit the chosen model on the full training data and evaluate once on a separate, untouched holdout test set**.

## 2.4 Model Performance Metrics — Regression

| Metric | Formula | Interpretation |
|---|---|---|
| **R²** | $1 - \dfrac{\text{SS}_{res}}{\text{SS}_{tot}}$ | Proportion of variance in Y explained by the model (0 to 1) |
| **Adjusted R²** | $1-(1-R^2)\dfrac{n-1}{n-p-1}$ | Like R², but **penalizes** adding predictors that don't genuinely help |
| **MSE** | $\dfrac{1}{n}\sum (y_i - \hat y_i)^2$ | Average squared error (heavily penalizes large errors) |
| **RMSE** | $\sqrt{\text{MSE}}$ | Same units as Y; penalizes large errors more than small ones |
| **MAE** | $\dfrac{1}{n}\sum \lvert y_i - \hat y_i \rvert$ | Average absolute error — treats all errors proportionally |
| **MAPE** | $\dfrac{100\%}{n}\sum \left\lvert \dfrac{y_i - \hat y_i}{y_i}\right\rvert$ | Average % error — scale-independent, good across different-sized targets |

> **Exam Tip (Quiz 1, Q11)**: "Single-number summary in the same units as the target, that penalizes large errors more" → **RMSE** (not MAE, which penalizes proportionally; not R², which is unitless; not MAPE, which is a percentage).

> ⚠ **Exam Pitfall (Quiz 1, Q6)**: Adding an **unhelpful** predictor to a regression model will **always** increase (or never decrease) plain R² — but it can **decrease Adjusted R²**, because Adjusted R² explicitly penalizes for the added complexity if the predictor doesn't pull its weight.

## 2.5 Classification Metrics — the Confusion Matrix

|  | **Predicted Positive** | **Predicted Negative** |
|---|---|---|
| **Actual Positive** | True Positive (TP) | False Negative (FN) — *Type II Error* |
| **Actual Negative** | False Positive (FP) — *Type I Error* | True Negative (TN) |

$$\text{Accuracy} = \frac{TP+TN}{TP+TN+FP+FN} \qquad \text{Precision} = \frac{TP}{TP+FP} \qquad \text{Recall (Sensitivity)} = \frac{TP}{TP+FN}$$
$$\text{Specificity} = \frac{TN}{TN+FP} = 1 - \text{False Positive Rate}$$

### Worked Example (Quiz 1, Q4d — Telecom Churn)
> On a 200-subscriber holdout, the model predicted 50 as "At Risk." Of these, 35 actually churned. Of the remaining 150 predicted "Safe," 20 churned.

- Predicted At-Risk (Positive) = 50 → TP = 35, FP = 50 − 35 = **15**
- Predicted Safe (Negative) = 150 → FN = 20 (churned but predicted safe), TN = 150 − 20 = **130**
- **Accuracy** = (TP + TN) / Total = (35 + 130) / 200 = 165/200 = **0.825 (82.5%)**

> **Business framing of Type I vs. Type II errors**:
> - **Type I (False Positive)**: flagging a *good* case as bad (e.g., false fraud alarm → annoyed genuine customer, unnecessary outreach cost).
> - **Type II (False Negative)**: missing a *real* bad case (e.g., a genuine defaulter/fraud/readmission slips through — often the *costlier* mistake in domains like healthcare readmission or fraud).

> ⚠ **Exam Pitfall**: In Case 1 (Mid-Term) with "costs of missing a true readmission are far higher than unnecessary outreach", the business wants to **minimize False Negatives** — i.e., prioritize **Recall/Sensitivity** over Precision, even at the cost of more false alarms.

## 2.6 ROC Curve, AUC, and Class Imbalance

- The **ROC curve** plots **Sensitivity (TPR)** vs. **1 − Specificity (FPR)** across all possible classification thresholds.
- **AUC (Area Under the Curve)**: a **threshold-independent ranking metric** — the probability that the model ranks a random positive case above a random negative case. AUC = 0.5 → no better than random guessing; AUC = 1.0 → perfect ranking.
- A diagonal reference line (`abline(a=0,b=1)`) represents random guessing.

> From Case 6 (End-Term): `AUC: 0.811` → this tells us the model does a **good job ranking** churners above non-churners, substantially better than random (0.5).

### Handling Class Imbalance
When the positive class (e.g., Churn=Yes, Fraud=Yes) is rare, a model can get "high accuracy" simply by always predicting the majority class — which is useless. Fixes:
- **Oversampling** the minority class (duplicate/synthesize minority examples).
- **SMOTE (Synthetic Minority Oversampling Technique)**: creates *synthetic* new minority-class examples (not just duplicates) by interpolating between existing minority points and their neighbors.
- **Undersampling** the majority class.

### Worked Example (Case 6, End-Term — post-SMOTE proportion)
> Original: No = 2476, Yes = 24. After SMOTE (`ovun.sample`, method="both", N=4952): No = 2496, Yes = 2456.

$$\text{Post-SMOTE churn \%} = \frac{2456}{2496+2456}\times 100 = \frac{2456}{4952}\times100 \approx \mathbf{49.60\%}$$

> ⚠ **Exam Pitfall**: SMOTE should be applied **only to the training data**, never to the validation/test data — otherwise synthetic points derived from test-set neighbors leak information into training (a subtle form of data leakage).

### Confusion-matrix-from-glm worked example (Case 6)
```
          Actual
Predicted   No   Yes
   No     1840   690
   Yes     656  1766
```
- **False alarms (False Positives)** = cases predicted "Yes" (churn) that were actually "No" = **656**.
- **Residual Deviance** (5235.2) vs **Null Deviance** (6864.6): the drop shows the predictors meaningfully improve fit over an intercept-only model; residual deviance itself represents the model's **remaining lack-of-fit** after including all predictors.

### 🎯 How to Answer Exam Questions — Module 2

| Keyword in question | Concept / Formula |
|---|---|
| "final, unbiased performance estimate" | Test set (touched once) |
| "tune hyperparameters / choose model" | Validation set / Cross-Validation |
| "more stable performance estimate than one split" | k-Fold CV |
| "large data, moderate model complexity, balance cost/bias/variance" | 5- or 10-fold CV (not LOOCV) |
| "fit transform inside each fold" | Correct pipeline integration (avoid leakage) |
| "penalizes large errors, same units as Y" | RMSE |
| "average absolute deviation" | MAE |
| "adding a useless predictor" | Adjusted R² may decrease |
| "missing a positive case is very costly" | Prioritize Recall/Sensitivity |
| "ranking quality regardless of threshold" | AUC / ROC |
| "rare positive class" | SMOTE / oversampling / undersampling |

---

<a id="m3"></a>
# Module 3 — Linear Regression & Causal/Predictive Modeling

## 3.1 The Multiple Linear Regression Equation

$$Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \dots + \beta_p X_p + \varepsilon$$

- $\beta_0$ = intercept (predicted Y when all X = 0, or for the baseline category)
- $\beta_j$ = slope for $X_j$ — the change in Y for a **one-unit increase in $X_j$, holding all other predictors constant**
- $\varepsilon$ = irreducible random error

## 3.2 Reading R's `lm()` Output

```
Coefficients:
              Estimate  Std. Error  t value  Pr(>|t|)
(Intercept)   35.1556    1.0677     32.93    <2e-16 ***
Spend_k        3.6525    0.1765     20.69    <2e-16 ***
SegmentRetail -13.9608    0.8870   -15.74    <2e-16 ***

Residual standard error: 2.771 on 37 degrees of freedom
Multiple R-squared: 0.9464,  Adjusted R-squared: 0.9435
F-statistic: 326.7 on 2 and 37 DF, p-value: < 2.2e-16
```

| Term | Meaning |
|---|---|
| **Estimate** | The fitted coefficient $\hat\beta_j$ |
| **Std. Error** | Uncertainty in the coefficient estimate |
| **t value** | Estimate / Std.Error — how many standard errors away from 0 |
| **Pr(>\|t\|)** | p-value — probability of seeing this large a t-value if the true effect were 0. Small (< 0.05) → statistically significant |
| **Residual Standard Error (RSE)** | Typical size of the residuals (prediction error) in Y's units |
| **Multiple R²** | Proportion of variance in Y explained by the model |
| **Adjusted R²** | R² penalized for the number of predictors |
| **F-statistic & its p-value** | Tests whether **at least one** predictor is useful, vs. an intercept-only model. Significant F-stat = the model as a whole beats the null model |

> **Exam Tip**: "Which statistic tells you at least one predictor improves the model beyond intercept-only?" → **the F-statistic** (Module 2/3 crossover, seen in End-Term Case 7).

## 3.3 Categorical Predictors — Dummy Variables & the Trap

A categorical variable with $G$ categories is represented using $G-1$ dummy (0/1) variables, with one category left out as the **baseline/reference**.

> ⚠ **The Dummy Variable Trap**: Including all $G$ dummies **plus** an intercept causes **perfect multicollinearity** (the dummies always sum to 1, exactly duplicating the intercept column) — the model matrix becomes singular and coefficients cannot be uniquely estimated. **Fix**: always use exactly $G-1$ dummies (Quiz 1, Q15 → answer is **G − 1**).

### Worked Example — Case 3 (Mid-Term): `Revenue_k ~ Spend_k + Segment`
```
(Intercept)     35.1556
Spend_k          3.6525
SegmentRetail  -13.9608
```
Here `Segment` has 2 categories (Retail, Online); **Online is the baseline** (not shown = 0), **Retail** is the one dummy included.

- **Fitted equation for Online**: $\widehat{Revenue}_k = 35.1556 + 3.6525\times Spend_k$
- **Fitted equation for Retail**: $\widehat{Revenue}_k = (35.1556 - 13.9608) + 3.6525\times Spend_k = 21.1948 + 3.6525\times Spend_k$

**(b) Predicted Revenue for Retail, Spend_k = 5:**
$$21.1948 + 3.6525(5) = 21.1948 + 18.2625 = \mathbf{39.46}$$

**(c) Spend_k needed for Online to reach Revenue_k = 60:**
$$60 = 35.1556 + 3.6525\, X \;\Rightarrow\; X = \frac{60-35.1556}{3.6525} = \frac{24.8444}{3.6525} \approx \mathbf{6.80}$$

**(d) Incremental lift (Online − Retail) at Spend_k = 5:**
Since Segment only shifts the **intercept** (no interaction term here), the lift is constant = $-(-13.9608) = \mathbf{13.96}$ (Online is higher by 13.96 at every spend level).

**(e) Higher baseline revenue at Spend_k = 0:** Compare intercepts: Online = 35.16, Retail = 21.19 → **Online** has the higher baseline.

## 3.4 Interaction Terms

An interaction term like `Price:Promo` allows the **slope** of `Price` to differ depending on whether `Promo` is on or off:

$$Y = \beta_0 + \beta_1 \text{Price} + \beta_2 \text{Promo} + \beta_3(\text{Price}\times \text{Promo}) + \varepsilon$$

- $\beta_2$ (main effect of Promo) → shifts the **intercept** when Promo = 1.
- $\beta_3$ (interaction coefficient) → shifts the **slope** of Price when Promo = 1.

> **Exam Tip (Quiz 1, Q17)**: The coefficient of `Price:Promo` is best read as **the change in the slope of Price when promotion is on** — NOT a change in the intercept, and NOT a simple "average discount."

## 3.5 Multicollinearity & VIF

**Variance Inflation Factor (VIF)** measures how much a predictor's variance is inflated due to correlation with other predictors:
$$VIF_j = \frac{1}{1-R_j^2}$$
where $R_j^2$ is from regressing $X_j$ on all other predictors.

| VIF value | Interpretation |
|---|---|
| ≈ 1 | No meaningful multicollinearity |
| 1–5 | Moderate, usually acceptable |
| **> 5 (or > 10, stricter rule)** | **Severe** multicollinearity — coefficient estimates become unstable |

### Worked Example — Case 7 (End-Term)
```
Income  2.78
Limit   229.24
Rating  230.87
Cards   1.44
Age     1.04
Student 1.01
```
**Limit** and **Rating** have VIF far above 10 → **severely multicollinear with each other** (they measure closely related concepts — credit limit and credit rating move together). These are the variables to investigate/consider dropping one of, to stabilize coefficient estimates.

- **R² = 0.9547** → about **95%** of variance in Balance is explained by the model.
- **RSE = 98.61** → typical prediction error is about **±98.61** (in Balance's units) — this is the "typical error band" for monitoring drift.
- **Incremental effect of being a Student** (holding other variables fixed) = coefficient of `StudentYes` = **+425.61** (students carry, on average, ₹425.61 more balance, controlling for income/limit/rating/cards/age).
- **F-statistic** (1380, p < 2.2e-16) → strong evidence at least one predictor is useful.

## 3.6 Prediction Interval vs. Confidence Interval

- **Confidence Interval (CI) for the mean response**: range for the *average* Y at a given X — narrower, because it only accounts for uncertainty in estimating the mean.
- **Prediction Interval (PI) for an individual response**: range for **one new observation's** Y — wider, because it must account for **both** estimation uncertainty **and** the natural random scatter ($\varepsilon$) of an individual point around the mean.

$$\boxed{\text{Prediction Interval is ALWAYS WIDER than the Confidence Interval, for the same X and confidence level.}}$$

> **Exam Tip (Quiz 1, Q7)**: "95% PI for next city's sales vs. 95% CI for mean sales" → **Prediction interval is wider**.

### 🎯 How to Answer Exam Questions — Module 3

| Keyword in question | Concept / Formula |
|---|---|
| "G categories, avoid dummy trap" | Use G − 1 dummies |
| "change in slope when X is on" | Interaction coefficient |
| "change in intercept when X is on" | Main-effect dummy coefficient |
| "VIF > 5 or >10" | Severe multicollinearity |
| "typical prediction error, same units as Y" | Residual Standard Error (RSE) |
| "at least one predictor helps" | F-statistic significance |
| "individual future observation range" | Prediction Interval (wider) |
| "range for the mean response" | Confidence Interval (narrower) |
| "proportion of variance explained" | R² / Adjusted R² |

---

<a id="m4"></a>
# Module 4 — Logistic Regression & Classification

## 4.1 The Logit Function, Log-Odds, and Odds Ratios

Logistic regression models the **probability** of a binary outcome using the **logit (log-odds)** link:

$$\text{logit}(p) = \ln\!\left(\frac{p}{1-p}\right) = \beta_0 + \beta_1 X_1 + \dots + \beta_p X_p$$

Solving for $p$ (the **sigmoid function**):
$$p(X) = \frac{1}{1+e^{-(\beta_0+\beta_1X_1+\dots)}}$$

- $\beta_j$ = the change in **log-odds** for a one-unit increase in $X_j$ (additive).
- $e^{\beta_j}$ = the **multiplicative change in odds** for a one-unit increase in $X_j$, holding others fixed. This is the **Odds Ratio**.

> **Exam Tip (Quiz 1, Q8)**: $e^{\beta_j}$ = **multiplicative change in odds** per unit of $X_j$ — NOT an additive change in probability, and NOT a change in log-odds (that's $\beta_j$ itself, additively).

## 4.2 Decision Boundary

The classification boundary is where $p(x) = 0.5$, which occurs exactly where the **log-odds equal 0**:
$$\beta_0 + \beta_1 x_1 + \beta_2 x_2 + \dots = 0$$

> **Exam Tip (Quiz 1, Q16)**: With two predictors, the decision boundary is where $\beta_0+\beta_1x_1+\beta_2x_2 = 0$ (log-odds = 0) — not $p(x)=0$ or $p(x)=1$.

## 4.3 Maximum Likelihood Estimation (MLE) & Perfect Separation

Logistic regression coefficients are found via **Maximum Likelihood Estimation** — choosing $\beta$'s that make the observed data most probable. When the classes are **perfectly (linearly) separable**, the likelihood keeps increasing as coefficients grow larger and larger — so **MLE coefficients diverge to very large magnitudes with no finite solution** (Quiz 1, Q9).

## 4.4 Interpreting R's `glm(family=binomial)` Output

```
Coefficients:
              Estimate  Std. Error  z value  Pr(>|z|)
(Intercept)    3.0674     0.8022     3.824   0.000131 ***
Tickets_30d   -0.5399     0.1322    -4.083   4.45e-05 ***
PlanPremium    1.8949     0.6785     2.793   0.005224 **

Null deviance: 103.591 on 79 degrees of freedom
Residual deviance: 71.622 on 77 degrees of freedom
AIC: 77.622
```

| Term | Meaning |
|---|---|
| **Null Deviance** | Lack-of-fit of an intercept-only model (no predictors) |
| **Residual Deviance** | Lack-of-fit of the fitted model (with predictors) — should be notably lower than Null Deviance if predictors help |
| **AIC** | Akaike Information Criterion — balances fit vs. complexity; **lower AIC is better** when comparing models |

### Worked Example — Case 4 (Mid-Term): Telecom Churn Logistic Regression

Model: `Churn ~ Tickets_30d + Plan` (baseline Plan = Standard; modeling P(Churn = "Yes")).
```
(Intercept)   3.0674
Tickets_30d  -0.5399
PlanPremium   1.8949
```

**(a) Log-odds of churn, Premium subscriber, Tickets_30d = 3.5:**
$$3.0674 + (-0.5399)(3.5) + 1.8949(1) = 3.0674 - 1.88965 + 1.8949 = \mathbf{3.02}$$

**(b) Predicted probability of churn, Standard subscriber, Tickets_30d = 7.0:**
Log-odds $= 3.0674 + (-0.5399)(7.0) + 1.8949(0) = 3.0674 - 3.7793 = -0.7119$
$$p = \frac{1}{1+e^{-(-0.7119)}} = \frac{1}{1+e^{0.7119}} = \frac{1}{1+2.0378} \approx \mathbf{0.329}$$

**(c) Decision boundary for Standard (p = 0.5, i.e. log-odds = 0):**
$$3.0674 - 0.5399\, X = 0 \;\Rightarrow\; X = \frac{3.0674}{0.5399} \approx \mathbf{5.68}$$

**(d) Change in log-odds for +2.0 increase in Tickets_30d (Plan fixed):**
$$\Delta\text{log-odds} = -0.5399\times 2.0 = \mathbf{-1.08}$$

**(e) Total customers**: Residual deviance df = 77 = n − (number of parameters) = n − 3 ⟹ **n = 80**.

> ⚠ **Exam Pitfall**: Notice **PlanPremium has a POSITIVE coefficient (+1.8949)** — meaning Premium customers have *higher* log-odds of churn than Standard, all else equal (perhaps because Premium subscribers who still complain a lot despite paying more are especially dissatisfied) — always read the **sign** carefully; don't assume "Premium = loyal."

### 🎯 How to Answer Exam Questions — Module 4

| Keyword in question | Concept / Formula |
|---|---|
| "multiplicative change in odds" | $e^{\beta_j}$ |
| "additive change in log-odds" | $\beta_j$ itself |
| "decision boundary / cutoff p=0.5" | log-odds = 0, solve for X |
| "perfectly separable data, MLE" | Coefficients diverge, no finite MLE |
| "compare fit before/after predictors" | Null deviance vs. Residual deviance |
| "compare models, penalize complexity" | AIC (lower is better) |
| "compute probability from log-odds" | $p = 1/(1+e^{-\text{logit}})$ |

---

<a id="m5"></a>
# Module 5 — Linear Discriminant Analysis (LDA)

## 5.1 Foundations of LDA

LDA classifies observations into groups by assuming:
1. Each class $k$ has a **prior probability** $P(Y=k)$ (how common that class is overall).
2. Each class has its own **mean vector** $\mu_k$ (the "typical" feature profile for that class).
3. All classes **share the same covariance matrix** $\Sigma$ (same spread/shape, just shifted centers).

LDA then computes, for a new observation, which class's fitted (multivariate normal) density is highest — equivalently, it finds the **linear discriminant function** that best separates the classes.

## 5.2 Interpreting R's `lda()` Output

```
Prior probabilities of groups:
   No    Yes
 0.75   0.25

Group means:
     Balance_k  LatePays
No    810.21     2.77
Yes  1695.73     5.43

Coefficients of linear discriminants:
              LD1
Balance_k  0.00351
LatePays  -0.00679
```

| Term | Meaning |
|---|---|
| **Prior probabilities** | The proportion of each class in the training data (baseline class frequencies) |
| **Group means** | Average feature values *within* each class — shows how classes differ on each predictor |
| **LD1 coefficients** | Weights combining the predictors into a single discriminant score used to separate classes |

### Worked Example — Case 5 (Mid-Term): Credit Default LDA

```
Prior probabilities: No = 0.75, Yes = 0.25
Group means: Yes → Balance_k = 1695.7347
Confusion Matrix:
         Predicted
Actual    No   Yes
  No      87    3
  Yes      2   28
```

**(a) Prior probability of No** = **0.75**

**(b) Average Balance_k for Yes (defaulters)** = **1695.73**

**(c) Total customers** = 87 + 3 + 2 + 28 = **120**

**(d) Accuracy** = (TP+TN)/Total = (87+28)/120 = 115/120 ≈ **0.96 (95.83%, rounds to 0.96)**

**(e) Misclassifying a true "Yes" (defaulter) as "No"** = a **False Negative** = **Type II Error** (missing a real positive case).

> **Exam Tip**: In LDA confusion matrices, always identify which class is your "positive"/event class (here, "Yes" = Default) before labeling Type I vs. Type II errors — Type I = false alarm on a true negative (No misclassified as Yes); Type II = miss on a true positive (Yes misclassified as No).

### 🎯 How to Answer Exam Questions — Module 5

| Keyword in question | Concept |
|---|---|
| "baseline class frequency" | Prior probabilities |
| "typical feature profile per class" | Group means |
| "misclassify a true positive as negative" | Type II Error (False Negative) |
| "misclassify a true negative as positive" | Type I Error (False Positive) |
| "% correctly classified" | Accuracy = (TP+TN)/Total |

---

<a id="m6"></a>
# Module 6 — Cluster Analysis (Unsupervised Segmentation)

## 6.1 Distance Metrics

- **Euclidean distance** (straight-line "as the crow flies"):
  - 1D: $d = \lvert x_1 - x_2 \rvert$ (absolute difference)
  - nD: $d = \sqrt{\sum_i (x_{1i}-x_{2i})^2}$
- **Manhattan distance** (grid/"city block"): $d = \sum_i \lvert x_{1i}-x_{2i}\rvert$

## 6.2 k-Means Clustering

**Algorithm**:
1. Choose $k$ (number of clusters) and initialize $k$ centroids (often randomly).
2. **Assignment step**: assign each point to its nearest centroid.
3. **Update step**: recompute each centroid as the **mean** of the points currently assigned to it.
4. Repeat steps 2–3 until centroids stop changing (**convergence**).

### Worked Example — Case 3 (End-Term): k-Means on Tower Traffic
Data: T1=8, T2=10, T3=12, T4=28, T5=30, T6=33. Initial centroids: 8 (Normal), 28 (Hotspot).

**Iteration 1 — Assignment** (nearest centroid by absolute distance):
- T1(8): |8-8|=0 vs |8-28|=20 → Normal
- T2(10): |10-8|=2 vs |10-28|=18 → Normal
- T3(12): |12-8|=4 vs |12-28|=16 → Normal
- T4(28): |28-8|=20 vs |28-28|=0 → Hotspot
- T5(30): |30-8|=22 vs |30-28|=2 → Hotspot
- T6(33): |33-8|=25 vs |33-28|=5 → Hotspot

**Hotspot cluster after first assignment = {T4, T5, T6} = {28, 30, 33}**

**Updated centroids**:
$$\text{Normal} = \frac{8+10+12}{3} = \mathbf{10.00} \qquad \text{Hotspot} = \frac{28+30+33}{3} = \mathbf{30.33}$$

**Check for re-assignment with new centroids (10.00, 30.33)**: every point's nearest centroid is unchanged (8,10,12 are still closer to 10.00; 28,30,33 are still closer to 30.33) → **converged**.

**Final centroids: Normal = 10.00, Hotspot = 30.33**

**New tower at 20 TB/day**: distance to Normal = |20-10.00| = 10.00; distance to Hotspot = |20-30.33| = 10.33 → **closer to Normal** → classified as **Normal-load**.

**Business framing**: Between (i) balanced-size clusters and (ii) clusters that isolate a small set of extreme towers, for a *congestion-management* use case, **(ii) isolating extreme towers is more actionable** — it directly flags the specific towers needing capacity upgrades/maintenance, which is the whole point of the exercise.

### Sensitivity to Scale & Initialization

> ⚠ **Exam Pitfall (Quiz 2, Q10)**: Running k-means with **unscaled** features (e.g., income in lakhs vs. a 1–5 satisfaction score) means the **largest-scale feature (income) will dominate** the Euclidean distance calculation, effectively drowning out the other features. **Always standardize/scale features before k-means.**

> **Exam Tip (Quiz 2, Q17)**: Different random initializations giving very different clusters → **run k-means multiple times with different initializations and pick the solution with the lowest WSS (Within-cluster Sum of Squares)**.

### Choosing k — the Elbow Method
Plot **WSS (Within-cluster Sum of Squares)** against different values of $k$. WSS always decreases as $k$ increases, but the *rate of decrease* slows sharply at the "elbow" — the point of diminishing returns is the recommended $k$.

## 6.3 Hierarchical Clustering

**Agglomerative approach**: start with every point as its own cluster, then repeatedly merge the two *closest* clusters until only one remains — visualized as a **dendrogram** (a tree showing merge order and merge height/distance).

- **Cutting the dendrogram** at a chosen height gives a specific number of clusters — everything merged *below* that height stays together, everything above splits apart (Quiz 2, Q3: "cut the dendrogram at the height that yields 5 clusters").

### Linkage Criteria

| Linkage | Distance between two clusters = | Effect |
|---|---|---|
| **Single** | Minimum distance between any pair of points (one from each cluster) | Can create long, "chained" elongated clusters |
| **Complete** | **Maximum** distance between any pair of points | Produces tight, compact clusters |
| **Average** | Average of all pairwise distances | Balance between single and complete |
| **Centroid** | Distance between cluster centroids | Sensitive to scale; needs standardized data |

> **Exam Tip (Quiz 2, Q1 & Q2)**: "Chaining effect" in a dendrogram → caused by **Single Linkage**; fix by switching to **Complete Linkage**. "Management wants tight, compact clusters (any two products in the same cluster are relatively similar)" → **Complete Linkage**.

### Worked Example — Case 2 (End-Term): Hierarchical Clustering of Agent AHT
Data (1D): 120, 128, 140, 205, 215, 405. Distance = absolute difference.

**Pairwise distances (nearest neighbors)**:
- |128-120|=8, |140-128|=12, |205-140|=65, |215-205|=10, |405-215|=190

**Single linkage — first merge**: the smallest distance anywhere is **|128-120| = 8** → **agents 120 and 128 merge first**, at **height 8**.

Continuing single linkage (always uses the *minimum* distance from any point in a cluster to any point in another):
1. Merge {120,128} (dist 8) → cluster A = {120,128}
2. Next smallest: |140-128|=12 → merge A with 140 → {120,128,140}
3. Next: |215-205|=10... but wait, ordering by actual minimum remaining distances: after step 2, remaining points 205,215,405 and cluster {120,128,140}. Distances: {140}-{205}=65, {205}-{215}=10, {215}-{405}=190. Smallest = 10 → merge {205,215}
4. Next smallest: {140-cluster}-{205,215-cluster} = min(205-140,215-140)=65 → merge {120,128,140} with {205,215} → {120,128,140,205,215}
5. Final merge: with {405} → distance = min(405-120...405-215) = 190

**Under complete linkage, cutting at height 15**: Using complete linkage (max pairwise distance) merge heights would be: {120,128}@8, then {120,128,140}@max(140-120,140-128)=20 (since 140 joins only after height 20 under complete linkage — at height 15, this merge hasn't happened yet). So at height 15, we have clusters: {120,128}, {140}, {205}, {215}, {405} — but check {205,215} merge height = 10, which IS below 15, so that merges too.
**Clusters at height 15 (complete linkage): {120,128}, {140}, {205,215}, {405} → 4 clusters.**

**Final complete-linkage merge**: The last (largest-height) merge joins the two most dissimilar remaining super-clusters — the low-AHT group {120,128,140,205,215} merges with the extreme outlier **{405}** at the greatest height, since 405 is far from everything else.

> **Exam Tip**: For "if the goal is high within-cluster similarity (tight clusters), which linkage?" → **Complete Linkage**.

## 6.4 Cluster Evaluation

| Measure | Type | Needs true labels? | What it measures |
|---|---|---|---|
| **Silhouette Score** | Internal | No | Combines cluster **tightness** (how close points are to their own cluster) and **separation** (how far from other clusters) |
| **Dunn Index** | Internal | No | Ratio of minimum inter-cluster distance to maximum intra-cluster distance (higher = better separated) |
| **WSS** | Internal | No | Total within-cluster variance (used for the Elbow method) |
| **Rand Index / Adjusted Rand Index (ARI)** | **External** | **Yes** | Compares clustering assignments against **known true labels** |

> **Exam Tip (Quiz 2, Q4)**: "Which measure is external and requires true class labels?" → **Rand Index / ARI** (not Silhouette, Dunn, or WSS — those are all internal/unsupervised).
> **Exam Tip (Quiz 2, Q5)**: "Assess cluster separation without external labels" → **Silhouette Score**.

### 🎯 How to Answer Exam Questions — Module 6

| Keyword in question | Concept |
|---|---|
| "chaining effect" | Single linkage problem → fix with Complete linkage |
| "tight/compact clusters" | Complete linkage |
| "cut dendrogram for N clusters" | Cut at the height yielding N clusters |
| "unscaled features, one dominates distance" | Scale/standardize before k-means |
| "different results across k-means runs" | Multiple random initializations, pick lowest WSS |
| "average customer profile" | Cluster centroid |
| "requires true labels" | External measure: Rand Index / ARI |
| "no labels, measure separation" | Internal measure: Silhouette / Dunn / WSS |
| "choosing k" | Elbow method on WSS |

---

<a id="m7"></a>
# Module 7 — Dimensionality Reduction (PCA)

## 7.1 Concepts & Objectives

PCA transforms a set of possibly-correlated features into a smaller set of **uncorrelated** variables called **Principal Components (PCs)**, ordered so that PC1 captures the **most variance**, PC2 the next most (subject to being uncorrelated with PC1), and so on.

**Goals**: compress features, retain as much variance (information) as possible, and **eliminate multicollinearity** (PCs are, by construction, uncorrelated with each other).

## 7.2 Why Standardize Before PCA

PCA is based on variance, which is scale-dependent. If one feature is in ₹lakhs and another is a 1–5 rating, the ₹lakhs feature will dominate the variance purely due to its scale — **not** because it's more informative.

> **Exam Tip (Quiz 2, Q13)**: "Variables measured in very different units, before PCA" → **standardize each variable (zero mean, unit variance)** — PCA is **NOT** scale-invariant (this is a common false statement to watch for).

## 7.3 Interpreting R's `prcomp()` Output

```
Importance of components:
                          PC1     PC2     PC3     PC4
Standard deviation     1.9309  0.51632 0.07084 0.01267
Proportion of Variance 0.9321  0.06665 0.00125 0.00004
Cumulative Proportion  0.9321  0.99871 0.99996 1.00000
```

| Term | Meaning |
|---|---|
| **Standard deviation** of a PC | Square root of that PC's eigenvalue — how much variance it captures (in original units-equivalent) |
| **Proportion of Variance** | Fraction of total variance explained by that single PC |
| **Cumulative Proportion** | Running total of variance explained, PC1 through PC_k |
| **Loadings (rotation matrix)** | Weights showing how much each *original* feature contributes to each PC — direction/meaning of the component |
| **Scores** (`pca_result$x`) | Coordinates of each observation in the new PC space |

### Worked Example — Case 4 (End-Term): Clinic Operations PCA

**How many PCs for ≥ 95% variance?** Cumulative: PC1=93.21%, PC1+PC2=99.871% → **93.21% alone is just under 95%**, so you need **2 components** to cross the 95% threshold.

**% variance NOT explained by PC1+PC2**: $100\% - 99.871\% = \mathbf{0.13\%}$

**Loadings for PC1**: `Avg_Wait_Time=0.502, Patients_Per_Day=0.497, Staff_Per_Shift=0.503, Readmission_30D=0.497` — all roughly equal and **positive** → PC1 is essentially a **general "clinic size/intensity/complexity" axis**: clinics high on PC1 have high wait time, high patient volume, high staffing, AND high readmission (i.e., busier, higher-risk clinics). This matches the interpretation pattern from Case 4 (Mid-Term style PCA) where a component with large positive loadings on "defect rate"/"rework time" and negative on "yield" was called a **"quality risk"** dimension — analogously here PC1 reads as an **overall operational-intensity / risk** dimension.

**Most extreme clinics on PC1**: Looking at the scores — Clinic **D = −2.36** (lowest / most "low-intensity") and Clinic **E = +2.48** (highest / most "high-intensity").

**Euclidean distance between C and E in (PC1,PC2) space**:
C: (1.4228, −0.1435), E: (2.4761, 0.0527)
$$d = \sqrt{(2.4761-1.4228)^2 + (0.0527-(-0.1435))^2} = \sqrt{(1.0533)^2+(0.1962)^2} = \sqrt{1.1094+0.0385} = \sqrt{1.1479} \approx \mathbf{1.07}$$
This is a **relatively small distance** in PC-space (compared to, e.g., D vs E which would be much larger) → **C and E are relatively similar/close** in overall operational profile.

**Which PC = "high-throughput, well-staffed, efficient/better outcomes"?** This describes the **opposite sign pattern** from PC1's "risk" reading on Readmission but same-direction on Patients/Staff — check PC2's loadings: `Avg_Wait=-0.464, Patients=+0.537, Staff=+0.460, Readmission=-0.533` → PC2 has **positive** loadings on Patients_Per_Day & Staff_Per_Shift but **negative** on Avg_Wait_Time & Readmission — exactly the "high throughput + well staffed + LOW wait + LOW readmission (better outcomes)" profile → **PC2**.

## 7.4 Visualization: Scree Plots & Biplots

- **Scree Plot**: variance explained (or eigenvalue) on the y-axis vs. PC number on the x-axis. Look for the **"elbow"** — where the curve flattens — as a rule-of-thumb for how many PCs to retain.
- **Biplot**: shows both the **observation scores** (points) and the **loading vectors** (arrows for original variables) on the same PC1–PC2 plane. Arrows pointing in similar directions = originally correlated variables; a point's position relative to an arrow shows how "high" that observation scores on that original variable.

## 7.5 Integration with Other Models

> **Exam Tip (Quiz 2, Q15)**: To compress features and then cluster, the correct pipeline order is: **PCA first, then run k-means on the resulting principal components** (not the reverse, and not run independently).

### 🎯 How to Answer Exam Questions — Module 7

| Keyword in question | Concept |
|---|---|
| "different units, before PCA" | Standardize first |
| "cumulative variance ≥ X%" | Count PCs until cumulative proportion crosses threshold |
| "loadings, which features dominate a PC" | Read the rotation matrix, look at sign & magnitude |
| "compress then cluster" | PCA first, then k-means on PCs |
| "elbow in variance plot" | Scree plot |
| "coordinates of observations in reduced space" | Scores (`$x`) |

---

<a id="m8"></a>
# Module 8 — Association Rule Mining (Apriori Algorithm)

## 8.1 Core Definitions

- **Transaction**: one "basket" of items bought together.
- **Itemset**: a set of one or more items.
- **Rule**: $A \rightarrow B$ ("if a customer buys itemset A, they also tend to buy B") — **A = antecedent (LHS)**, **B = consequent (RHS)**.

## 8.2 Core Metrics

$$\text{Support}(A\rightarrow B) = \frac{\text{Count}(A\cup B)}{N} \qquad \text{(how often A and B appear together, out of all transactions)}$$
$$\text{Confidence}(A\rightarrow B) = \frac{\text{Support}(A\cup B)}{\text{Support}(A)} \qquad \text{(P(B | A) — reliability of the rule)}$$
$$\text{Coverage}(A\rightarrow B) = \text{Support}(A)$$
$$\text{Lift}(A\rightarrow B) = \frac{\text{Confidence}(A\rightarrow B)}{\text{Support}(B)} = \frac{\text{Support}(A\cup B)}{\text{Support}(A)\times\text{Support}(B)}$$
$$\text{Uplift}(A\rightarrow B) = \text{Confidence}(A\rightarrow B) - \text{Support}(B)$$

| Metric | Interpretation |
|---|---|
| **Support** | How common/frequent this combination is overall |
| **Confidence** | Given A, how often B also happens (like a conditional probability) |
| **Lift** | How much more likely B is, given A, **compared to B's baseline popularity**. Lift > 1 → positive association; Lift = 1 → independent; Lift < 1 → negative association |
| **Uplift** | Absolute percentage-point gain in likelihood of B, given A, over B's baseline rate |

## 8.3 Worked Example — Case 1 (End-Term): Food Delivery Apriori

```
lhs         rhs        support  confidence  coverage  lift      count
{}       => {Salad}    0.4      0.4000000   1.0       1.000000  4
{}       => {Coke}     0.4      0.4000000   1.0       1.000000  4
{}       => {Burger}   0.6      0.6000000   1.0       1.000000  6
{}       => {Fries}    0.6      0.6000000   1.0       1.000000  6
{Coke}   => {Fries}    0.3      0.7500000   0.4       1.250000  3
{Fries}  => {Coke}     0.3      0.5000000   0.6       1.250000  3
{Burger} => {Fries}    0.4      0.6666667   0.6       1.111111  4
{Fries}  => {Burger}   0.4      0.6666667   0.6       1.111111  4
```
(N = 10 transactions)

**Q1: Highest confidence rule (non-empty antecedent)**: Compare 0.75, 0.50, 0.667, 0.667 → **{Coke} ⇒ {Fries}** with **confidence 0.75** is the highest.

**Q2: "Increases purchase likelihood by at least 20% above baseline" → Lift ≥ 1.20**: Rules with Lift = 1.25: **{Coke}⇒{Fries}** and **{Fries}⇒{Coke}** both qualify (lift 1.25 = 25% above baseline). {Burger}⇒{Fries}/{Fries}⇒{Burger} only have lift 1.111 (11.1% above baseline) — don't qualify.

**Q3: P(Fries | Coke)**: This is exactly the **Confidence** of {Coke}⇒{Fries} = **0.75**.

**Q4: "Coke and Fries are mutually reinforcing" — single metric supporting both directions improving over baseline**: **Lift** — because Lift is **symmetric**: $\text{Lift}(A\to B) = \text{Lift}(B\to A)$ (both equal 1.25 here), directly showing the *mutual* reinforcement, unlike Confidence which differs by direction (0.75 vs 0.50).

**Q5: Maximize Uplift = Confidence − Support(rhs)**:
- {Coke}⇒{Fries}: 0.75 − 0.6 = **0.15**
- {Fries}⇒{Coke}: 0.50 − 0.4 = **0.10**
- {Burger}⇒{Fries}: 0.667 − 0.6 = **0.067**
- {Fries}⇒{Burger}: 0.667 − 0.6 = **0.067**

**Rule #5 ({Coke} ⇒ {Fries}) maximizes Uplift at 0.15** → this is the rule the CFO should select.

> ⚠ **Exam Pitfall**: Don't confuse **Confidence** (asymmetric — direction matters, P(B|A) ≠ P(A|B) in general) with **Lift** (symmetric — same value in both directions) — a classic "read the definition carefully" trap.

### 🎯 How to Answer Exam Questions — Module 8

| Keyword in question | Concept / Formula |
|---|---|
| "reliability of A predicting B" / "P(B given A)" | Confidence |
| "how common is this combo overall" | Support |
| "compared to baseline popularity of B" | Lift |
| "mutual reinforcement, symmetric" | Lift (same in both directions) |
| "absolute percentage-point gain over baseline" | Uplift = Confidence − Support(B) |
| "how often does A alone appear" | Coverage = Support(A) |
| "most reliable single rule" | Highest Confidence |

---

<a id="formulas"></a>
# 🎯 Master Formula Sheet (Quick Reference)

## Regression
$$R^2 = 1-\frac{SS_{res}}{SS_{tot}} \quad\text{Adj. } R^2 = 1-(1-R^2)\frac{n-1}{n-p-1} \quad RMSE=\sqrt{\tfrac1n\sum(y_i-\hat y_i)^2} \quad MAE=\tfrac1n\sum|y_i-\hat y_i|$$
$$VIF_j = \frac{1}{1-R_j^2} \quad(\text{severe if} > 5\text{--}10)$$

## Classification
$$Accuracy=\frac{TP+TN}{N}\quad Precision=\frac{TP}{TP+FP}\quad Recall=\frac{TP}{TP+FN}\quad Specificity=\frac{TN}{TN+FP}$$

## Logistic Regression
$$\text{logit}(p)=\ln\frac{p}{1-p}=\beta_0+\beta_1X_1+\dots \qquad p=\frac{1}{1+e^{-\text{logit}(p)}} \qquad \text{Odds Ratio}=e^{\beta_j}$$

## Clustering
$$d_{Euclidean}=\sqrt{\sum_i(x_{1i}-x_{2i})^2} \qquad d_{Manhattan}=\sum_i|x_{1i}-x_{2i}|$$

## Association Rules
$$Support(A\to B)=\frac{Count(A\cup B)}{N}\quad Confidence(A\to B)=\frac{Sup(A\cup B)}{Sup(A)}\quad Lift(A\to B)=\frac{Conf(A\to B)}{Sup(B)}\quad Uplift=Conf-Sup(B)$$

## Golden Rules to Never Forget
1. **Test set = touched once**, at the very end, for the final unbiased estimate.
2. **Fit scalers/PCA/imputers on training data only** — apply (don't refit) to validation/test.
3. **Adjusted R²** penalizes useless predictors; plain R² never decreases when you add predictors.
4. **Prediction Interval > Confidence Interval** in width, always.
5. **$G-1$ dummies** for a $G$-category variable (with intercept) to avoid the dummy trap.
6. **Standardize before** k-means and before PCA.
7. **Complete linkage** → tight clusters; **Single linkage** → chaining.
8. **Lift is symmetric**; **Confidence is not**.
9. **AUC** judges ranking quality across all thresholds; **Accuracy/Confusion Matrix** judges one specific threshold.
10. Always match the **business cost of errors** (Type I vs Type II) to the **right metric** (Precision vs Recall) before picking a threshold.

---

*End of Master Study Manual — Good luck on your Mid-Term and End-Term! 🎓*
