# Multiple Linear Regression (Sessions 6, 7 & 8) — FAQ Bank
### Short & Long Questions with Full Worked Solutions (R-code included where relevant)

---

## SECTION 1 — Matrix Form & Core Theory (Short FAQs)

**Q1. In matrix form, how is the multiple regression model written?**
> $y = X\beta + \epsilon$, where $\epsilon \sim N(0,\sigma^2 I)$. $X$ is the $n\times(p+1)$ design matrix (a leading column of 1's for the intercept, plus one column per predictor), $\beta$ is the $(p+1)\times1$ coefficient vector, and $y$ is the $n\times1$ response vector.

**Q2. What is the OLS estimator $\hat\beta$ in matrix form, and what property does it have?**
> $\hat\beta = \arg\min_\beta (y-X\beta)'(y-X\beta) = (X'X)^{-1}X'y$. It is the **BLUE** — Best Linear Unbiased Estimator — meaning among all linear, unbiased estimators, it has the smallest possible variance (given standard OLS assumptions hold).

**Q3. What is the Hat Matrix, and what does it do?**
> $H = X(X'X)^{-1}X'$. It "puts a hat on y": $\hat y = Xy\hat\beta = Hy$. In plain English, $H$ is the machine that turns your actual observed outcomes into the model's fitted (predicted) values — purely a function of the predictors X, not of y's actual values.

**Q4. How are residuals defined in matrix form?**
> $e = y-\hat y = (I-H)y$. This is just "actual minus fitted," expressed as one matrix operation for the whole dataset at once.

**Q5. How is $\hat\sigma^2$ computed, and why divide by $n-p-1$ instead of $n$?**
$$
H_0:\ \beta_{\text{years}} = 0
\quad \text{vs} \quad
H_1:\ \beta_{\text{years}} \neq 0
$$

Since

$$
0.0188 < 0.05,
$$

we reject \(H_0\).

**Conclusion:** **Years** is a statistically significant predictor of **sales**, after accounting for (or **holding constant**) the effect of **score**.

**Q6. Are $\hat\beta$ and $\hat\sigma^2$ biased or unbiased estimators?**
> Both are **unbiased** estimators of the true population $\beta$ and $\sigma^2$ — meaning if you repeated the sampling-and-fitting process infinitely many times, the average of all your $\hat\beta$'s (or $\hat\sigma^2$'s) would converge exactly to the true value.

---

## SECTION 2 — Product Sales Group (Long FAQ, Full Worked Example, with R code)

**Setup / R Code:**
```r
sales <- c(100,90,80,70,60,50,40,30)
years <- c(10,8,5,5,7,3,1,1)
score <- c(9,10,7,8,4,5,4,1)
product <- lm(sales ~ years + score)
summary(product)
sqrt(mean(product$residuals^2))   # RMSE
```

```
Coefficients:
              Estimate  Std. Error  t value  Pr(>|t|)
(Intercept)   19.883     4.970       4.000    0.0103 *
years          3.736     1.092       3.420    0.0188 *
score          4.406     1.175       3.751    0.0133 *

Residual standard error: 5.994 on 5 degrees of freedom
Multiple R-squared: 0.9572, Adjusted R-squared: 0.9401
F-statistic: 55.94 on 2 and 5 DF, p-value: 0.0003784
```

### Q1. What is the fitted regression model?
$$ \widehat{\text{sales}} = 19.883 + 3.736 \times \text{years} + 4.406 \times \text{score} $$

### Q2. Predicted sales for an employee with 4 years of tenure and a score of 6?
$$ 19.883 + 3.736(4) + 4.406(6) = 19.883 + 14.944 + 26.436 = \mathbf{61.26} $$
*(Note: "predicted sales" and "fitted value" ask the exact same thing here — both mean plugging X-values into the fitted equation. The only difference in wording is whether X is a value from the original 8 employees ("fitted value" for an existing row) or a brand-new hypothetical employee ("prediction"). The arithmetic is identical either way.)*

### Q3. What is the residual when years = 1 and score = 4?
Scan the original data: `years = (10,8,5,5,7,3,1,1)`, `score = (9,10,7,8,4,5,4,1)` → years=1 & score=4 occurs at **observation 7**. Reading directly off the R output's residuals list: **residual = -1.243**.
> Interpretation: This employee's *actual* sales were 1.243 units **below** what the model predicted for someone with their years/score profile.

### Q4. What are $\hat\beta_1$ (years) and its standard error?
$$ \hat\beta_1 = 3.736, \qquad SE(\hat\beta_1) = 1.092 $$

### Q5. What hypothesis does the p-value 0.0188 (for years) correspond to?
$$ H_0: \beta_{\text{years}} = 0 \quad \text{vs} \quad H_1: \beta_{\text{years}} \neq 0 $$
Since $0.0188 < 0.05$, we **reject $H_0$** — years is a statistically significant predictor of sales, holding score fixed.

### Q6. At α = 0.05, what is the critical t-value, and the 95% CI for $\beta_1$?
Degrees of freedom $= n-p-1 = 8-2-1 = 5$. Two-tailed critical value: $t_{0.025,5} = 2.571$.
$$ CI = \hat\beta_1 \pm t_{0.025,5}\times SE(\hat\beta_1) = 3.736 \pm 2.571(1.092) = 3.736 \pm 2.807 = (\mathbf{0.929, \; 6.543}) $$
> Since this interval does **not** contain 0, it agrees with our earlier rejection of $H_0$.

### Q7. What is $\hat\sigma$?
Read directly from output: **Residual standard error = 5.994.**

### Q8. What is $R^2$, and what does it mean?
$$ R^2 = 0.9572 $$
> 95.72% of the variability in **this sample's** sales is explained by years and score jointly.

### Q9. What is the *population* proportion of variability explained (as opposed to sample R²)?
This is asking for the **Adjusted R²** — R² as computed is a *sample* statistic that tends to overstate the true population relationship (especially with small n and multiple predictors); Adjusted R² corrects this bias:
$$ \text{Adjusted } R^2 = 0.9401 $$

### Q10. What hypothesis does the F-statistic test, and what's the critical value?
$$
H_0:\ \beta_{\text{years}} = \beta_{\text{score}} = 0
\quad
\text{(Neither predictor helps — the model is no better than simply predicting the mean.)}
$$

$$
H_1:\ \text{At least one } \beta_j \neq 0
$$

**Critical value:**

$$
F_{0.05,\;2,\;5} \approx 5.79
$$

Since the observed

$$
F = 55.94 \gg 5.79
$$

and the p-value is

$$
0.0003784 \ll 0.05,
$$

we reject \(H_0\).

**Conclusion:** The regression model as a whole is statistically significant. This means that **at least one of the predictors ("years" or "score") contributes significantly to predicting the response variable**, and the model performs **significantly better than simply predicting the mean** for every observation.

---

## SECTION 3 — TRP (Television Rating Points) Example — Standardized Coefficients

**R Code:**
```r
cor.data3 <- read.csv("dk10_1.csv")
revenue <- cor.data3$revenue; promotion <- cor.data3$promotion; ctrp <- cor.data3$ctrp
trp.lm <- lm(revenue ~ promotion + ctrp)
summary(trp.lm)

# Standardized version:
revenue1 <- scale(revenue); promotion1 <- scale(promotion); ctrp1 <- scale(ctrp)
trp1.lm <- lm(revenue1 ~ promotion1 + ctrp1)
summary(trp1.lm)
```

### Q1 (Short). What is the fitted (raw) regression equation?
$$ \widehat{\text{revenue}} = 41010 + 3.163(\text{promotion}) + 5932(\text{ctrp}) $$

### Q2 (Long). Why do we need *standardized* regression coefficients here, and how do we interpret them?

**Why:** Promotion is measured in rupees (huge numbers, e.g. ~100,000+) while ctrp is a rating score (roughly 90–160). Because the raw coefficients (3.163 vs 5932) are on completely different scales, you **cannot** compare them directly to ask "which predictor matters more" — a bigger raw coefficient might just reflect a smaller-scale unit, not a bigger real-world impact.

**Fix:** Standardize (z-score) every variable — subtract the mean, divide by the standard deviation — before refitting. Now every variable is on the same "number of standard deviations" scale, and coefficients become directly comparable.

**Result:**
```
promotion1 = 0.7363,   ctrp1 = 0.7322
```

**Interpretation:** *"For a one-standard-deviation increase in promotion spend, revenue increases by 0.7363 standard deviations (holding ctrp fixed). For a one-standard-deviation increase in ctrp, revenue increases by 0.7322 standard deviations (holding promotion fixed)."* Since 0.7363 > 0.7322, **promotion has a very slightly larger standardized impact on revenue than ctrp** — but the two are nearly tied.

**Common Mistake:** Do NOT say "promotion causes more revenue because 3.163 (raw coefficient)... " — that raw comparison is meaningless across differently-scaled variables. Only the **standardized** coefficients are directly comparable.

---

## SECTION 4 — Advertising Dataset — The Four Big Business Questions

**R Code:**
```r
advertising_data <- read.csv("Advertising.csv")
lm_advertising <- lm(sales ~ TV + radio + newspaper, data = advertising_data)
summary(lm_advertising)
```

```
Coefficients:
              Estimate   Std. Error   t value   Pr(>|t|)
(Intercept)   2.938889    0.311908    9.422     <2e-16
TV            0.045765    0.001395    32.809    <2e-16
radio         0.188530    0.008611    21.893    <2e-16
newspaper    -0.001037    0.005871   -0.177     0.86

Residual standard error: 1.686 on 196 degrees of freedom
Multiple R-squared: 0.8972, Adjusted R-squared: 0.8956
F-statistic: 570.3 on 3 and 196 DF, p-value: < 2.2e-16
```

### Q1 (Short). Is at least one predictor useful for predicting sales?
> Yes. The F-statistic (570.3) is very large with p-value < 2.2e-16, so we reject $H_0: \beta_{TV}=\beta_{radio}=\beta_{newspaper}=0$ — **at least one** predictor is genuinely related to sales.

### Q2 (Long). Do ALL predictors help, or only a subset? Walk through the logic.

Look at the **individual** t-tests/p-values ($H_0: \beta_j=0$ for each $j$ separately):
- TV: p < 2e-16 → highly significant
- radio: p < 2e-16 → highly significant
- newspaper: p = 0.86 → **NOT significant** (fails to reject $H_0:\beta_{newspaper}=0$)

**Conclusion:** Only **TV and radio** are related to sales; there is **no evidence** that newspaper spend affects sales *once TV and radio are already accounted for* (note the careful phrasing — this doesn't mean newspaper has zero correlation with sales on its own, only that it adds nothing **beyond** what TV/radio already explain — newspaper spend likely correlates with TV/radio spend in this dataset, so its marginal contribution vanishes once the other two are in the model).

**Refit without newspaper:**
```r
lm(sales ~ TV + radio, data = advertising_data)
```
```
TV:    0.04575, p<2e-16
radio: 0.18799, p<2e-16
Multiple R-squared: 0.8972 (essentially unchanged from 0.8972 with newspaper!)
```
> Notice R² barely moves (0.8972 → 0.8972) when newspaper is dropped — strong confirmation that newspaper was contributing essentially nothing.

### Q3 (Short). How well does the model fit the data?
> $R^2 = 0.8972$ → the model explains about **89.72%** of the variance in sales. Residual Standard Error = 1.686 → predictions typically miss actual sales by about **1.686 units**.

### Q4 (Long). Predict sales for $100,000 TV / $20,000 radio spend, with 95% Prediction and Confidence Intervals — and explain why the two intervals differ.

**R Code:**
```r
new_data <- data.frame(TV = 100, radio = 20, newspaper = 0)  # units per dataset scale (thousands of $)
predict(lm_advertising, newdata = new_data, interval = "prediction")
predict(lm_advertising, newdata = new_data, interval = "confidence")
```

```
Predicted Sales: 11.25647

95% Prediction Interval:  ( 7.9296 , 14.5833 )   <- WIDER
95% Confidence Interval:  (10.9853 , 11.5277 )   <- NARROWER
```

**Explanation of the gap:**
- The **Confidence Interval (10.99, 11.53)** is for the **average** sales across *all* cities/markets that spend exactly $100k on TV and $20k on radio — it only reflects uncertainty in estimating that average.
- The **Prediction Interval (7.93, 14.58)** is for **one specific, new, individual** market's sales — it must additionally account for that one market's own random deviation from the average (the natural "noise" any single data point has). That's why it's dramatically wider.

---

## SECTION 5 — Qualitative (Categorical) Predictors — Own & Region

### Q1 (Short). What is one-hot encoding, and why is it needed?
> One-hot encoding converts a categorical variable into multiple binary (0/1) columns — one per category — because regression models are purely numeric machines and cannot directly process text labels like "Red"/"Blue"/"Green." Each new column answers: "Is this observation THIS particular category?"

### Q2 (Long). Interpret this model in full: `Balance ~ Own`

**R Code:**
```r
credit_data <- read.csv("Credit.csv")
credit_model <- lm(Balance ~ Own, credit_data)
summary(credit_model)
```
```
Coefficients:
             Estimate   Std. Error   t value   Pr(>|t|)
(Intercept)   509.80     33.13       15.389    <2e-16
OwnYes         19.73     46.05        0.429     0.669

Residual standard error: 460.2 on 398 degrees of freedom
Multiple R-squared: 0.0004611, Adjusted R-squared: -0.00205
F-statistic: 0.1836 on 1 and 398 DF, p-value: 0.6685
```

**Full walk-through:**
- **(Intercept) = 509.80:** Since `Own` has 2 levels (Yes/No) and R picked "No" as the baseline (reference), this is the estimated average Balance for people who do **not** own their home.
- **OwnYes = 19.73:** Home owners' average balance is $19.73 **higher** than non-owners — BUT the p-value (0.669) is far above 0.05, so **this difference is not statistically significant**; we cannot conclude home ownership meaningfully affects balance.
- **R² = 0.00046 (0.046%):** Practically zero — `Own` explains almost none of the variation in Balance.
- **Adjusted R² = -0.00205 (negative!):** This is a strong signal that `Own` is actively **worse** than a model with no predictors at all (just predicting the overall mean for everyone) — negative Adjusted R² is only possible when a predictor adds essentially pure noise.
- **F-statistic = 0.1836, p = 0.6685:** Confirms the whole model (just `Own` alone) is **not** statistically significant.

**Business takeaway:** Don't use home-ownership status to explain or predict credit card balances — it has no meaningful relationship in this dataset.

### Q3 (Short). In the model `Balance ~ Region` (levels: East/South/West, East = baseline), how do you interpret `RegionWest = -18.69`?
> Individuals in the West region have an average Balance **$18.69 lower** than individuals in the East region (the reference category), holding nothing else constant. (Also not statistically significant here, p=0.774.)

### Q4 (Short). With G categories and an intercept, how many dummy columns should be included, and why?
> **G − 1** dummies. Including all G would create perfect multicollinearity with the intercept (the dummy-variable trap) — the G dummy columns would always sum to exactly 1 for every row, exactly duplicating the intercept's constant column, making the design matrix singular (non-invertible).

---

## SECTION 6 — Full Model vs Reduced Model vs VIF (Credit Dataset)

### Q1 (Long). Compare the full model (`Balance ~ .`) to the reduced model (`Balance ~ Income + Limit + Cards + Student`). Why drop the other variables?

**R Code:**
```r
credit_data <- read.csv("Credit.csv")
full_model <- lm(Balance ~ ., data = credit_data)
summary(full_model)

model_revised <- lm(Balance ~ Income + Limit + Cards + Student, data = credit_data)
summary(model_revised)
```

**Full model key output:** Education (p=0.4921), OwnYes (p=0.2832), MarriedYes (p=0.4107), RegionSouth (p=0.4083), RegionWest (p=0.2347) are **all statistically insignificant** — none of these help explain Balance once Income, Limit, Rating, Cards, Age, and Student are already in the model.

**Reduced model:** Keeping only Income, Limit, Cards, Student:
```
Income:     -7.839,  p<2e-16
Limit:       0.2666, p<2e-16
Cards:      23.18,   p<2e-16
StudentYes: 429.6,   p<2e-16
Adjusted R-squared: 0.9531  (vs 0.9538 for the full model — essentially the same!)
```
> Dropping 7 insignificant variables cost us almost nothing in Adjusted R² (0.9538 → 0.9531) — strong evidence that the simpler, 4-predictor model is preferable: it's just as good at explaining Balance, easier to interpret, and avoids overfitting to noise variables.

### Q2 (Long). Compute and interpret the predicted Balance, 95% CI, and 95% PI for Income=50, Limit=4000, Cards=3, Student=Yes.

**R Code:**
```r
new_cust <- data.frame(Income = 50, Limit = 4000, Cards = 3, Student = "Yes")
predict(model_revised, newdata = new_cust, interval = "prediction")
predict(model_revised, newdata = new_cust, interval = "confidence")
```

**Manual computation (using coefficients: Intercept=-499.7, Income=-7.839, Limit=0.2666, Cards=23.18, StudentYes=429.6):**
$$ \widehat{\text{Balance}} = -499.7 - 7.839(50) + 0.2666(4000) + 23.18(3) + 429.6(1) $$
$$ = -499.7 - 391.95 + 1066.4 + 69.54 + 429.6 = \mathbf{673.89} \approx 674.02 \text{ (matches R's exact output)} $$

**Given output:**
```
95% Prediction Interval: (475.77, 872.27)   <- for ONE new customer
95% Confidence Interval: (642.50, 705.55)   <- for the AVERAGE customer with this profile
```
> As always, PI ⊃ CI (wider) because PI adds the individual customer's own random deviation on top of the uncertainty in estimating the mean.

### Q3 (Long). Why is Income's coefficient negative (-7.839) — doesn't higher income usually mean people carry MORE balance, not less?

This is a classic "**counter-intuitive sign, but statistically correct**" discussion point:
- The coefficient is **conditional** — "holding Limit, Cards, and Student status fixed."
- In practice, once you already control for credit **Limit** (which itself correlates strongly with income — richer people get bigger limits), the *remaining* (residual) effect of income, independent of limit, tends to be that higher-income people **pay down balances faster / carry proportionally less debt relative to their limit** — they don't need to revolve as much credit.
- This is a good teaching moment on **interpreting multiple-regression coefficients as "adjusted for other included variables"** — the sign/magnitude of any one coefficient can look surprising in isolation but makes sense once you remember every other variable in the model is being held constant.
- **Caution:** This is still a **statistical association**, not necessarily a proven causal mechanism (see Section 7).

### Q4 (Short). What is VIF, and what does a VIF near 1 vs a VIF over 200 tell you?

**R Code:**
```r
library(car)
vif(model_revised)
```
```
Income: 2.69   Limit: 2.69   Cards: 1.00   Student: 1.00
```
> VIF near 1 → almost no multicollinearity, coefficients are stable and trustworthy.

Compare to the version that **also** includes Rating:
```
Income: 2.78   Limit: 229.24   Rating: 230.87   Cards: 1.44   Age: 1.04   Student: 1.01
```
> **Limit and Rating** both exceed VIF = 200 — they are almost perfectly linearly related to each other (credit rating is essentially derived from credit limit/usage). This inflates both their standard errors dramatically, making their *individual* coefficients unstable and hard to interpret in isolation — even though both remain "statistically significant" by p-value. **Fix:** drop one of the two (commonly the less theoretically central one), or use regularization methods (Ridge/Lasso — covered in Term II).

### Q5 (Short). Does high VIF hurt prediction accuracy or coefficient interpretation more?
> Primarily **coefficient interpretation/inference** — the model's overall predictive accuracy ($R^2$, RMSE) is usually barely affected by multicollinearity; what suffers is your ability to trust *any single* coefficient's exact value as a stable, individual effect.

---

## SECTION 7 — From R Output to Business Decisions (Short FAQs)

**Q1. In the reduced Credit model, do students carry meaningfully higher balances than non-students?**
> Yes — StudentYes = +429.6, p < 2e-16 (highly significant, holding income/limit/cards fixed). This is a strong, actionable signal for student-targeted credit products.

**Q2. Does income reduce expected balance after controlling for credit limit?**
> Yes, statistically — Income's coefficient is significantly negative (-7.839, p<2e-16) even after Limit is held fixed. But see Section 6 Q3 — interpret this as an adjusted statistical association, not a guaranteed causal driver businesses should act on without further validation.

**Q3. How much does expected balance rise when credit limit increases?**
> By $0.2666 for every $1 increase in Limit (holding Income, Cards, Student fixed) — directly readable from the `Limit` coefficient.

**Q4. Which insights are only statistical associations and should NOT be treated as causal?**
> Any coefficient from an **observational** dataset like this (no randomized experiment) — e.g., the negative Income effect — describes a correlation *conditional on the other variables in the model*, not a guaranteed cause-and-effect relationship. Confusing "controls for X" with "proves causation" is one of the most common real-world misapplications of regression output.

**Q5. Which insights are most actionable for customer targeting / credit-risk monitoring?**
> The **Student** effect (large, stable, highly significant, consistent across both full and reduced models) is the most reliable, actionable lever — e.g., for designing student credit products or adjusting risk models by student status.

---

# ✅ QUICK-REFERENCE: Most Frequently Re-Asked Calculation Types

| Ask | How to Answer |
|---|---|
| "What is the fitted/predicted value for X = ...?" | Plug values into $\hat\beta_0+\hat\beta_1X_1+...$ directly from the Estimate column |
| "What is the residual for observation i?" | Read directly off the `Residuals` list in R output, OR compute actual − fitted |
| "What hypothesis does this p-value test?" | $H_0: \beta_j = 0$ vs $H_1: \beta_j \neq 0$ for individual coefficients; $H_0:$ all $\beta_j=0$ (except intercept) vs $H_1:$ at least one ≠ 0, for the F-statistic |
| "95% CI for a coefficient?" | $\hat\beta_j \pm t_{\alpha/2,\,n-p-1} \times SE(\hat\beta_j)$ |
| "Prediction Interval vs Confidence Interval?" | PI is always wider (individual point vs the mean) |
| "Should I drop this predictor?" | Check its individual p-value AND whether Adjusted R² improves/barely changes without it |
| "Is multicollinearity a problem?" | Check VIF — flag anything > 5 (conservative) or > 10 |
