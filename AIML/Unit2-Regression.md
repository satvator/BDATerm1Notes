# Machine Learning with Business Applications
## Complete Study Notebook — UNIT 2
### Regression: Simple & Multiple Linear Regression, R Output Interpretation

> **Running Example:** AirTel-Fresh wants to understand and forecast **Revenue** — first from a single driver (ad spend), then from multiple drivers (spend, segment, region) — the perfect vehicle for every idea in this unit.

---

# PART A — What Is Regression, and Why?

## 1. Big Picture

Regression is the tool for anything with a **continuous, numeric outcome** — revenue, price, time, cost. Unit 1 taught you *when* to call something "regression" (word-problem signals); this unit teaches you *how* it actually works, and — most importantly — **how to read a regression output table**, which is the single most tested skill on your exams (Mid-Term Cases 3 & 7).

## 2. Intuition

Imagine you're a shop owner tracking, for many past weeks, how much you spent on Instagram ads (X) and how much revenue came in that week (Y). If you plot every week as a dot, you'll notice a rough upward trend. Regression finds the **single straight line** that best summarizes that trend — so that for a *new* ad-spend value, you can read off an expected revenue.

## 3. Purpose of Regression — Prediction vs Inference (Recap + Regression-Specific Framing)

| | **Prediction** | **Inference** |
|---|---|---|
| Regression Question | "What will next week's revenue be if we spend $5k on ads?" | "How much does each extra $1k of ad spend actually cause revenue to change, holding other factors fixed?" |
| What matters | Accuracy of the number produced | Correctness/stability/interpretability of the *coefficient* itself |
| Business use | Forecasting, budgeting | Actuarial pricing, ROI attribution, policy justification (Mid-Term Case 1's "Estimating Claim Amount Drivers" is a pure inference task) |

## 4. Formal Vocabulary

| Term | Meaning |
|---|---|
| **Dependent Variable (Y)** | The outcome you're trying to explain/predict (Revenue) |
| **Independent Variable (X)** | The predictor(s) (Ad Spend, Region) |
| **Regression Equation** | The mathematical rule linking X to Y |
| **Population Model** | The *true*, unknown relationship in the entire universe of data: $Y = \beta_0+\beta_1X+\varepsilon$ |
| **Sample Model** | Our *estimate* of that relationship, fit from the limited data we actually have: $\hat{Y}=\hat\beta_0+\hat\beta_1X$ |
| **Residual** | The gap between an *actual* observed Y and the model's *predicted* $\hat{Y}$ for that same point: $e_i = y_i - \hat{y}_i$ |
| **Error (ε)** | The *true, unobservable* population-level deviation — residuals are our best *estimate* of error, computed from sample data |
| **Noise** | Random, irreducible variation in Y that no predictor can ever explain |
| **Linearity (assumption)** | The relationship between X and Y is assumed to be a straight line (or linear in the coefficients) |

**Memory trick:** *Error is the ghost (true, invisible). Residual is the ghost's shadow (visible, measurable, from your sample).*

---

# PART B — Simple Linear Regression

## 1. Big Picture

One predictor, one outcome, one straight line. Everything here scales up almost unchanged into Multiple Regression (Part C) — so nail the intuition here first.

## 2. Intuition — Least Squares ("Best Fit Line")

Picture your scatter of (Ad Spend, Revenue) dots. Infinitely many lines *could* be drawn through this cloud. **Ordinary Least Squares (OLS)** picks the ONE line that makes the **sum of squared vertical gaps (residuals)** between each dot and the line as small as possible.

```
   Revenue
      │        ●
      │    ●  ╱│  <- residual (gap between dot and line)
      │   ╱ ●  │
      │  ╱●    ●
      │ ╱  ●
      │╱________________________► Ad Spend
       (the "best fit line" minimizes the SUM of all squared gaps)
```

**Why square the residuals** (instead of just summing them, or summing absolute values)?
- Squaring makes all gaps positive (so above-line and below-line errors don't cancel out).
- Squaring **penalizes big misses much more** than small ones — pushing the line to avoid large errors.
- Squaring also makes the math solvable in closed form (calculus: take a derivative, set to zero — this is exactly how the OLS formulas below are *derived*).

## 3. The Regression Equation

$$ \hat{Y} = \hat\beta_0 + \hat\beta_1 X $$

| Symbol | Name | Meaning |
|---|---|---|
| $\hat\beta_0$ | **Intercept** | Predicted Y when X = 0 |
| $\hat\beta_1$ | **Slope** | The change in predicted Y for every 1-unit increase in X |
| $\hat{Y}$ | **Prediction / Fitted value** | The model's guess for Y, given a specific X |

## 4. Mathematical Derivation of OLS Estimators (Intuition-First)

We want to minimize the **Sum of Squared Residuals**:
$$ SSR = \sum_{i=1}^n (y_i - \hat\beta_0 - \hat\beta_1 x_i)^2 $$

**The calculus idea (no need to memorize the derivation, just understand the logic):** Take the derivative of SSR with respect to $\hat\beta_0$ and $\hat\beta_1$ separately, set each to zero (this is where the curve is flattest — the minimum point), and solve the two resulting equations simultaneously. This produces:

$$ \hat\beta_1 = \frac{\sum(x_i-\bar{x})(y_i-\bar{y})}{\sum(x_i-\bar{x})^2} \qquad\qquad \hat\beta_0 = \bar{y} - \hat\beta_1\bar{x} $$

**Plain-English translation of the slope formula:** *"How much do X and Y move together (numerator), relative to how much X moves on its own (denominator)?"* — this is literally **covariance of X,Y divided by variance of X**.

## 5. Interpretation of Coefficients

- **Slope ($\hat\beta_1$):** "For every additional $1,000 in ad spend, revenue is predicted to increase by $\hat\beta_1$ (in whatever units Y is measured), **holding nothing else fixed** (there's nothing else in a simple regression)."
- **Intercept ($\hat\beta_0$):** "Predicted revenue when ad spend is zero." — Caution: this is often not practically meaningful if X=0 never actually occurs in your data (extrapolating outside the observed range).

## 6. Residual Plot

A **residual plot** graphs residuals (y-axis) against fitted values or X (x-axis). It's your #1 diagnostic tool for checking whether linearity/constant-variance assumptions hold.

```
 Good (random scatter, no pattern):      Bad (funnel/curve pattern):
   Residual                                Residual
      │  ●  ●    ●  ●                         │      ●
      │●   ●  ●    ●                          │   ●●●●
      │────────────────► Fitted               │ ●●●●●●●●●●
      │  ●    ●  ●  ●                         │●●●●●●●●●●●●
      │    ●     ●                            └──────────► Fitted
   (assumptions look fine)              (variance grows with fitted value —
                                          "heteroscedasticity," a violated assumption)
```

## 7. Standard Error, t-Test, p-value

- **Standard Error (of a coefficient):** How much the estimated coefficient would bounce around if you re-sampled the data repeatedly — a measure of the coefficient's *precision*.
- **t-statistic:** $t = \dfrac{\hat\beta_1}{SE(\hat\beta_1)}$ — "How many standard errors is this coefficient away from zero?" A large \|t\| means the coefficient is unlikely to be zero by pure chance.
- **p-value:** The probability of seeing a t-statistic this extreme (or more extreme) **if the true coefficient were actually zero**. Small p-value (< 0.05 conventionally) → we reject "this variable has no effect" → the variable is **statistically significant**.
- **Hypothesis test being run:** $H_0: \beta_1 = 0$ (no relationship) vs $H_1: \beta_1 \neq 0$ (there IS a relationship).

## 8. Confidence Interval vs Prediction Interval — THE most confused pair in Regression

*(This exact confusion is explicitly flagged in your syllabus and appeared in Quiz 1 Q7.)*

| | **Confidence Interval (CI)** | **Prediction Interval (PI)** |
|---|---|---|
| What it's an interval FOR | The **average/mean** Y for a given X (the *population regression line* itself) | A **single, individual, new** observation's Y value |
| Width | **Narrower** | **Wider** — always, for the same X and confidence level |
| Why the width differs | Only accounts for uncertainty in *estimating the mean* | Accounts for uncertainty in the mean estimate **PLUS** the natural random scatter (noise) of any one individual point around that mean |
| Business framing | "We're 95% confident the *average* sales across all cities at this media-mix level is between $A and $B" | "We're 95% confident *this specific* city's sales will be between $C and $D" (Quiz 1 Q7 — PI is wider) |

**Memory trick:** *Confidence Interval = about the LINE. Prediction Interval = about a POINT. A point has more ways to go wrong than the line's average, so PI is always wider.*

## 9. RMSE and Residual Standard Error

- **Residual Standard Error (RSE):** Roughly, the "typical size" of a residual — a rough estimate of the standard deviation of the irreducible error, adjusted for degrees of freedom used.
- **RMSE:** Very similar in spirit (root mean squared error) — both express "typical prediction error" in the *same units as Y*.

---

# PART C — Multiple Linear Regression

## 1. Big Picture

Real business problems almost never have just one driver. Multiple regression lets you include several predictors **simultaneously**, and — crucially — lets you say "holding all other variables fixed" when interpreting any one coefficient. This is where nearly all of your exam's regression case studies live (Mid-Term Case 3, Case 5, Case 7).

## 2. Why We Need Multiple Regression

If you regress Revenue on Ad Spend *alone*, but Segment (Retail vs Online) also strongly affects revenue, your single-predictor model will have a **biased, confused** slope — it will partly be capturing the effect of Segment mixed into the Ad Spend coefficient (this is the seed of **omitted variable bias**). Adding Segment as a second predictor lets the model correctly separate the two effects.

## 3. The Equation

$$ \hat{Y} = \hat\beta_0 + \hat\beta_1X_1 + \hat\beta_2X_2 + ... + \hat\beta_pX_p $$

**Interpretation of each $\hat\beta_j$:** *"Holding all OTHER predictors fixed, a 1-unit increase in $X_j$ changes predicted Y by $\hat\beta_j$."* This "holding others fixed" clause is the entire reason multiple regression is more useful than doing several separate simple regressions.

## 4. Matrix Form (Conceptual, Not Computational)

$$ \mathbf{Y} = \mathbf{X}\boldsymbol\beta + \boldsymbol\varepsilon \qquad\qquad \hat{\boldsymbol\beta} = (\mathbf{X}^T\mathbf{X})^{-1}\mathbf{X}^T\mathbf{Y} \text{ (the "Normal Equation")} $$

**Plain-English version:** Stack every observation's predictors into a big table (matrix) X, stack every observation's outcome into a column Y. The **Normal Equation** is just the matrix version of "set the derivative of SSR to zero and solve" — the same OLS logic as simple regression, generalized to many predictors at once. You will never be asked to compute this by hand; just know *what it represents* and that it's how software finds all coefficients simultaneously.

- **Hat Matrix (H):** The matrix that "puts a hat on Y" — i.e., turns actual Y into predicted $\hat{Y}$: $\hat{Y} = HY$.
- **Residual Matrix:** $e = Y - \hat{Y} = (I-H)Y$ — the leftover, unexplained part.
- **BLUE Estimator:** OLS coefficients are the **Best Linear Unbiased Estimator** — under the standard assumptions (linearity, independence, homoscedasticity, no perfect multicollinearity), OLS gives the *lowest possible variance* among all linear, unbiased ways to estimate the coefficients (the Gauss-Markov theorem, conceptually).

## 5. Dummy Variables (Encoding Categorical Predictors)

Regression is fundamentally a numeric-input machine — it can't directly "understand" a text category like "Retail" or "Online." **Dummy variables** convert categories into 0/1 indicator columns.

**Worked Example (mirrors Mid-Term Case 3 exactly):** `Segment` has 2 categories: Retail, Online. R automatically picks one as the **baseline/reference** (alphabetically first by default, here "Online") and creates ONE dummy: `SegmentRetail` (=1 if Retail, =0 if Online).

```
model_case3 <- lm(Revenue_k ~ Spend_k + Segment, data = case3)

Coefficients:
                Estimate   Std. Error   t value   Pr(>|t|)
(Intercept)      35.1556     1.0677     32.93     <2e-16
Spend_k           3.6525     0.1765     20.69     <2e-16
SegmentRetail   -13.9608     0.8870    -15.74     <2e-16
```

**Interpretation:**
- **Intercept (35.1556):** predicted Revenue_k for the **baseline category (Online)** when Spend_k = 0.
- **SegmentRetail (-13.9608):** Retail's revenue is $13.96k **lower** than Online's, at the *same* Spend_k level, holding spend fixed. This coefficient is a **shift in the intercept only** — it moves the whole line up/down for Retail vs Online, without changing the slope.

### Solved Numerical Questions (Mid-Term Case 3 exact format)

**Predicted Revenue_k for Retail at Spend_k = 5:**
$$ 35.1556 + 3.6525(5) - 13.9608(1) = 35.1556 + 18.2625 - 13.9608 = 39.46 $$

**Spend_k needed (for Online) to reach predicted Revenue_k = 60:**
$$ 60 = 35.1556 + 3.6525 \cdot x \implies x = \frac{60-35.1556}{3.6525} = \frac{24.8444}{3.6525} = 6.80 $$

**Incremental lift (Online − Retail) at Spend_k = 5:**
Since SegmentRetail only shifts the intercept (no interaction term here), the lift = $-(-13.9608) = 13.96$ at *every* Spend_k level, including 5. (Online = Retail + 13.9608, always, since there's no Spend×Segment interaction in this model.)

**Baseline revenue (Spend_k = 0): which segment is higher?** Online = 35.1556 (the intercept); Retail = 35.1556 − 13.9608 = 21.19. **Online has the higher baseline.**

## 6. The Dummy Variable Trap

**What it is:** If you include a dummy for **every** category of a variable **AND** keep the intercept, the dummies + intercept become perfectly collinear (they always sum to 1, exactly mirroring the intercept's constant "1" column) — the model matrix becomes **singular**, and coefficients can't be uniquely estimated.

**The Fix:** With **G** categories and an intercept, include only **G − 1** dummies (Quiz 1 Q15 — correct answer: G−1). The omitted category becomes the "baseline," and every dummy coefficient is interpreted *relative to that baseline*.

## 7. Interaction Terms

An interaction term (e.g., `Spend_k:Promo`) lets the **slope itself change** across categories — not just the intercept.

**Worked Example (Quiz 2 Q17-style, End-Term style):** `lm(Sales ~ Price + Promo + Price:Promo)`. The coefficient on `Price:Promo` represents: **"the change in the slope of Price, specifically when Promo is turned on."** (Not a change in the intercept — that would be the `Promo` main-effect coefficient alone.)

```
Without interaction (parallel lines):        With interaction (different slopes):
  Y                                              Y
   │      ___Promo=1                              │           ___Promo=1 (steeper)
   │   __/                                        │        __/
   │__/    ___Promo=0                              │     _/      ___Promo=0
   │    __/                                        │  __/     __/
   └──────────────► X                              └──────────────► X
   (same slope, different intercept)          (different slope AND intercept)
```

## 8. Multicollinearity and VIF

**What it is:** When two or more predictors are highly correlated *with each other*, the model struggles to tell their individual effects apart — coefficient estimates become unstable (wildly different if you slightly change the sample), and standard errors inflate.

**Variance Inflation Factor (VIF):** Measures how much a coefficient's variance is "inflated" due to its correlation with the other predictors.

$$ VIF_j = \frac{1}{1-R_j^2} $$

where $R_j^2$ comes from regressing $X_j$ on *all other* predictors.

| VIF Value | Interpretation |
|---|---|
| ≈ 1 | No meaningful multicollinearity |
| 1–5 | Moderate, generally acceptable |
| **> 5–10** | **Problematic** — investigate/consider dropping one of the correlated variables |

**Worked Example (exact Mid-Term Case 7 style — Credit dataset):**
```
Variable    VIF
Income       2.78
Limit      229.24   <- extremely high
Rating     230.87   <- extremely high
Cards        1.44
Age          1.04
Student      1.01
```
**Limit and Rating** have enormous VIFs (>200) — they are almost perfectly correlated with each other (a customer's credit rating is essentially derived from their credit limit and usage). This means: even though both show up as "statistically significant" individually, their *individual* coefficient values are unstable and shouldn't be trusted in isolation — this is exactly the "predictor most likely to cause unstable coefficient estimates" answer pattern.

**Important nuance:** High VIF hurts the *precision/stability of individual coefficients*, but does **not** necessarily hurt the model's overall predictive accuracy ($R^2$) — this is a classic exam distinction: multicollinearity is an **inference** problem more than a **prediction** problem.

## 9. Adjusted R² (Recap in Regression Context)

$$ \text{Adjusted }R^2 = 1-(1-R^2)\frac{n-1}{n-p-1} $$

Where $p$ = number of predictors. As you add more predictors, plain $R^2$ can only go up or stay flat (mathematically, more flexibility always fits training data at least as well) — Adjusted $R^2$ explicitly penalizes for each added predictor, so it can *decrease* if the new predictor doesn't pull its weight. This is *why* Adjusted R² (not R²) is the fairer way to compare models with different numbers of predictors.

---

# PART D — Reading a Full Multiple Regression Output (End-to-End Worked Example)

*(Mirrors Mid-Term Case 7 — Credit balance model — line by line.)*

```
lm(formula = Balance ~ Income + Limit + Rating + Cards + Age + Student)

Coefficients:
              Estimate    Std. Error   t value   Pr(>|t|)
(Intercept)   -493.734     24.825      -19.889    <2e-16
Income          -7.795      0.233      -33.395    <2e-16
Limit            0.194      0.032        5.981    4.98e-09
Rating           1.091      0.485        2.251    0.0250
Cards           18.212      4.319        4.217    3.08e-05
Age             -0.624      0.292       -2.139    0.0331
StudentYes     425.610     16.510       25.780    <2e-16

Residual standard error: 98.61 on 393 degrees of freedom
Multiple R-squared: 0.9547, Adjusted R-squared: 0.954
F-statistic: 1380 on 6 and 393 DF, p-value: < 2.2e-16
```

## Line-by-Line Interpretation Cheat Sheet

| Output row/field | What to say about it |
|---|---|
| **Estimate** column | The coefficient itself — the "holding everything else fixed" effect size |
| **Std. Error** | Precision of that estimate — smaller = more precise |
| **t value** | Estimate ÷ Std.Error — how many SEs away from 0 |
| **Pr(>\|t\|)** | p-value — is this variable statistically significant? (All are here, since all < 0.05) |
| **StudentYes = 425.610** | Being a student adds **$425.61** to predicted balance, holding income/limit/rating/cards/age fixed — this is the "incremental effect of being a student" (a **dummy variable**, baseline = non-student) |
| **Residual Standard Error = 98.61** | The "typical" prediction error size, in the same units as Balance ($) |
| **Multiple R-squared = 0.9547** | 95.47% of the variation in Balance is explained by this model |
| **Adjusted R-squared = 0.954** | Same idea, penalized for the 6 predictors used — barely different from R², meaning none of the 6 predictors are "dead weight" |
| **F-statistic = 1380, p-value < 2.2e-16** | Tests $H_0$: **ALL** coefficients (except intercept) are simultaneously zero, vs $H_1$: **at least one** is non-zero. A huge F-stat with a tiny p-value = strong evidence the model *as a whole* is meaningfully better than an intercept-only (mean-only) baseline. |
| **Degrees of Freedom (393)** | n − (p+1) = 400 − 7 = 393 → tells you n = 400 customers |

## Solved Exam-Style Questions

**"What proportion of variance in Balance is explained by the model?"** → **Multiple R² = 0.95** (rounded to 2 decimals).

**"Typical prediction error scale?"** → **Residual Standard Error = 98.61**.

**"Which statistic shows at least one predictor improves the model beyond intercept-only?"** → **The F-statistic** (not any individual t-test — the F-test is the *joint* test across all predictors simultaneously).

**"Which predictor(s) should be investigated for multicollinearity instability?"** → **Limit and Rating** (VIF > 200 each, from Part C §8 above).

---

# PART E — LOOCV in a Regression Context (Mid-Term Case 5 style)

## 1. Big Picture

You met Cross-Validation conceptually in Unit 1. Here's how it looks applied specifically to a *regression* model's output — reading LOOCV prediction tables and turning them into business-friendly statements.

## 2. Worked Example

```
LOOCV results: RMSE = 0.4843613, R-squared = 0.6909671, MAE = 0.453438

Final model (fit on full data):
(Intercept) = 2.20000140, Flights = 0.03399463, Weather = 0.24010565
```

## Solved Questions

**"Which predictor has a larger marginal impact — Flights or Weather?"**
Careful — you can't just compare raw coefficients when predictors are on **different scales**! Flights ranges over ~80–160 (large scale, small coefficient 0.034); Weather ranges 0–5 (small scale, larger coefficient 0.240). A useful check: multiply each coefficient by a "typical" one-unit-of-realistic-change, or simply note that **Weather's coefficient (0.24) is over 7× larger than Flights' (0.034)**, meaning a one-unit change in the *disruption index* moves penalty cost much more than a one-unit change in flight count — so **Weather** has the larger marginal impact *per unit*.

**"Predict penalty cost for Flights=130, Weather=2.8":**
$$ 2.2000 + 0.03399(130) + 0.24011(2.8) = 2.2000+4.4187+0.6723 = 7.29 $$

**"Which day has the largest absolute LOOCV error?"** → Straight from the sorted table: **Day 1, error = 0.70**.

**"On average, how far off are LOOCV predictions?"** → This is literally asking for **RMSE (or MAE)** in plain language: "on average, about **0.48 lakhs**" (using RMSE) — or "about 0.45 lakhs" if you frame it via MAE specifically, since MAE is the literal "average absolute error."

**"% of variation explained?"** → **R² = 0.69, i.e., 69.10%.**

---

# PART F — Common Confusions Recap (Unit 2)

| Confused Pair | Resolution |
|---|---|
| Residual vs Error | Residual = observed, from your *sample* ($e_i = y_i-\hat{y}_i$). Error = true, unobservable, population-level ($\varepsilon$). |
| R² vs Adjusted R² | R² never decreases when adding predictors. Adjusted R² penalizes useless additions and *can* decrease. |
| Confidence Interval vs Prediction Interval | CI = uncertainty about the *mean* Y at a given X (narrower). PI = uncertainty about *one new* Y (wider — adds irreducible noise on top). |
| t-test vs F-test | t-test = is *this one* coefficient individually significant? F-test = are *all* coefficients (jointly) significant, beyond an intercept-only model? |
| Coefficient vs Odds Ratio | (Bridge to Unit 3) In linear regression, coefficients are additive, in the *same units as Y*. This is different from logistic regression, where $e^\beta$ becomes multiplicative on **odds**. |

---

# PART G — Exam Perspective (Unit 2)

- **Guaranteed numerical questions:** plug specific X values into a fitted equation and compute $\hat{Y}$ — practice doing this arithmetic fast and cleanly, always double-checking baseline categories for any dummy variables.
- **"Read the R output" questions** are essentially guaranteed on every exam — memorize what each column (Estimate, Std.Error, t-value, Pr(>\|t\|)) and each summary line (RSE, R², Adjusted R², F-statistic, degrees of freedom) *means in one sentence*, exactly as drilled in Part D's cheat sheet.
- **VIF questions** always ask "which variable(s) should be investigated" — scan for VIF > 5–10, and remember multicollinearity is primarily an **inference** (coefficient stability) issue, not necessarily a prediction-accuracy issue.
- **Dummy variable trap** (G−1 rule) is a guaranteed conceptual MCQ.
- Confidence vs Prediction Interval — always: **PI is wider**. Every single time.

---

# ✅ UNIT 2 — MASTER ONE-PAGE REVISION SHEET

1. **Regression = predicting/explaining a continuous outcome.** Purpose is either **Prediction** ("what will happen") or **Inference** ("why/how much").
2. **Residual** (sample, observed) vs **Error** (population, true, unobservable).
3. **OLS** finds the line minimizing the **sum of squared residuals** — squaring avoids cancellation and heavily penalizes large misses.
4. **Slope** = effect per unit of X. **Intercept** = predicted Y when X=0 (careful extrapolating).
5. **t-test/p-value** tests one coefficient; **F-test** tests all coefficients jointly (vs intercept-only baseline).
6. **Confidence Interval** (about the mean, narrower) vs **Prediction Interval** (about one new point, always wider).
7. **Multiple regression:** each coefficient's meaning includes "**holding all other predictors fixed**."
8. **Dummy variables:** G categories → **G−1 dummies + intercept** (avoids the dummy variable trap / perfect collinearity).
9. **Interaction terms** change the **slope** across groups; main-effect dummies change only the **intercept**.
10. **VIF > 5–10** signals problematic multicollinearity — hurts coefficient *stability/inference*, not necessarily prediction accuracy.
11. **Adjusted R²** penalizes useless predictors (can decrease); plain **R²** never decreases as you add predictors.
12. **Residual Standard Error / RMSE** = typical prediction error, in Y's own units.

---

*End of Unit 2. All three units (1, 2, 3) are now complete. Whenever you're ready, I can build a final cross-unit revision sheet/flash-card set that pulls the most exam-critical facts from all three units into one condensed sheet for last-minute review.*
