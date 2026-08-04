## Complete Study Notebook — UNIT 3
### Classification: Generative vs Discriminative Models, Logistic Regression, LDA

> **Running Example:** AirTel-Fresh (our telecom) wants to predict **Churn (Yes/No)** based on customer behavior — this is a **binary classification** problem, the perfect vehicle for every idea in this unit.

---

# PART A — What is Classification?

## 1. Big Picture

In Unit 1 you learned Regression predicts a **number**. Classification predicts a **category**. This unit is about *how* a model draws the line between categories, and how to read/interpret the numbers a classifier spits out — this is the single most numerically-tested unit in your course (Mid-Term Cases 4 & 5, End-Term Case 6 are entirely classification-based).

## 2. Intuition

Imagine plotting every customer as a dot on a graph — x-axis = "number of complaints," y-axis = "monthly spend." Customers who churned are **red dots**, customers who stayed are **blue dots**. A classifier's whole job is to draw a **boundary line** (or curve) separating red from blue, so that any *new* customer's dot tells you which side (class) they fall into.

```
  Spend
    │
    │   B B B  B          B = Stayed (blue)
    │  B  B   B    ╲       R = Churned (red)
    │ B    B        ╲___
    │_______________/____╲______________
    │      R    R  /  R    R
    │    R   R    /    R
    │  R    R    /
    └────────────────────────────► Complaints
              ↑
     This curved boundary = the "decision boundary"
```

## 3. Formal Definitions

| Term | Meaning |
|---|---|
| **Classifier** | An algorithm that assigns each observation to one of several predefined categories (classes) |
| **Binary Classification** | Exactly 2 possible classes (Churn: Yes/No; Fraud: Yes/No) |
| **Multiclass Classification** | 3+ possible classes (e.g., Customer tier: Bronze/Silver/Gold) |
| **Decision Boundary** | The line/curve/surface that separates predicted classes in feature space |
| **Feature Space** | The mathematical space defined by all your predictor variables — each observation is a point in this space |
| **Class Labels** | The category values themselves (Yes/No, Fraud/Genuine) |

## 4. Common Confusions

- "Classification always outputs a category directly" — **Not quite.** Most classifiers (logistic regression, LDA) first output a **probability**, then apply a **threshold** (commonly 0.5) to convert that probability into a final class label. The probability is the real output; the label is a *decision* built on top of it.

---

# PART B — Generative vs Discriminative Models

## 1. Big Picture

This is the deepest conceptual split in classification, and a favorite "compare and contrast" exam theme. Every classifier you'll learn (Naive Bayes, LDA = generative; Logistic Regression = discriminative) falls into one of these two philosophies.

## 2. Intuition

Imagine you're trying to identify whether a photo shows a "cat" or "dog."

- **Generative approach:** Learn what cats *typically* look like (their whole visual distribution), and separately learn what dogs *typically* look like. Given a new photo, ask: "Which learned distribution does this look more like it came from?" — You've modeled *how the data itself is generated* for each class.
- **Discriminative approach:** Don't bother modeling what cats/dogs look like in general. Just directly learn the **boundary** that separates cat-photos from dog-photos. You only care about "which side of the line is this on?"

**Analogy:** A generative model is like a forger who has learned to *paint* both a Rembrandt and a Picasso from scratch, then compares your painting to each style. A discriminative model is like an art critic who has never painted anything but can instantly tell you "this is not a Rembrandt" just by learning the *differences*.

## 3. Formal Definitions

| Concept | Meaning |
|---|---|
| **Joint Probability** P(X, Y) | Probability of features X *and* class Y occurring together |
| **Conditional Probability** P(Y\|X) | Probability of class Y, *given* we've already observed features X |
| **Generative Model** | Models P(X\|Y) and P(Y) for each class, then uses Bayes' theorem to compute P(Y\|X). It models how the data is *generated* within each class. |
| **Discriminative Model** | Directly models P(Y\|X) (or the boundary) — skips modeling how X is distributed at all. |

### The Math Bridge — Bayes' Theorem (how Generative models get to P(Y|X))

$$ P(Y|X) = \frac{P(X|Y) \cdot P(Y)}{P(X)} $$

In words: *Posterior (what we want) = Likelihood × Prior, divided by Evidence.*

- P(Y) = **Prior** — how common is each class *before* seeing any features? (e.g., 75% of customers are "No default" — this literally appeared as `Prior probabilities: No=0.75, Yes=0.25` in your End-Term Case 5 LDA output.)
- P(X|Y) = **Likelihood** — given a class, what does the feature distribution look like?
- P(Y|X) = **Posterior** — the answer we actually want: given these features, what's the probability of each class?

## 4. Comparison Table

| | **Generative** (Naive Bayes, LDA) | **Discriminative** (Logistic Regression) |
|---|---|---|
| What it models | P(X\|Y) and P(Y), then derives P(Y\|X) via Bayes | P(Y\|X) directly |
| Assumes distribution of X? | Yes (e.g., Naive Bayes assumes feature independence; LDA assumes Gaussian features with equal covariance) | No distributional assumption on X |
| Can generate new synthetic data? | Yes — it knows the full data distribution | No — it only knows the boundary |
| Works well with small data / strong assumptions hold | Often more data-efficient if assumptions are right | Needs more data to directly estimate the boundary reliably |
| Robust to assumption violations? | Less robust — wrong distributional assumptions hurt badly | More robust — fewer assumptions to violate |
| Typical business use | LDA for credit scoring with well-behaved numeric features; Naive Bayes for text/spam classification | Logistic regression for churn, credit risk, marketing response — the default "interpretable" workhorse |

## 5. Advantages / Disadvantages

**Naive Bayes (Generative)**
- *Advantages:* Extremely fast, works well with high-dimensional data (e.g., text — thousands of word-features), needs relatively little data.
- *Disadvantages:* The "naive" independence assumption (features are unrelated given the class) is almost never fully true in the real world — yet often works "well enough" anyway.

**Logistic Regression (Discriminative)**
- *Advantages:* Highly interpretable coefficients, no distributional assumptions on X, robust, the business standard for regulated industries (credit, insurance, healthcare).
- *Disadvantages:* Assumes a specific (linear-in-log-odds) shape for the boundary; struggles if the true boundary is very non-linear without added features/interactions.

## 6. Common Confusions

- "Generative models are always more accurate" — **False.** Discriminative models often outperform when you have plenty of data and don't want to risk wrong distributional assumptions.
- "P(X|Y) and P(Y|X) are basically the same thing" — **False**, and this exact confusion (P(X,Y) vs P(Y|X)) is explicitly listed as an exam trap in your syllabus. They answer completely different questions: P(X|Y) asks "given the class, what do features look like?" P(Y|X) asks "given the features, what's the class?"

---

# PART C — Logistic Regression (Discriminative, Deep Dive)

## 1. Big Picture

Logistic Regression is the **single most tested topic** in your Classification unit (it drives Mid-Term Case 4 and End-Term Case 6 entirely). It's the natural next step after linear regression — but adapted so its output is always a valid probability between 0 and 1.

## 2. Intuition — Why Linear Regression Fails for Classification

Suppose you tried to predict Churn (Yes=1, No=0) using plain linear regression: $\hat{y} = \beta_0 + \beta_1 X$.

**Problem:** A straight line has no ceiling or floor — for extreme values of X, it can predict things like "-0.3" or "1.8" probability, which is **meaningless** (probability must be between 0 and 1).

```
  P(Churn)
    1.8 ┤                              ╱  <- impossible! (>1)
    1.0 ┤- - - - - - - - - - -  ╱ - - -
        │                    ╱
    0.5 ┤                 ╱
        │              ╱
    0.0 ┤- - -  ╱ - - - - - - - - - - - -
   -0.3 ┤   ╱                              <- impossible! (<0)
        └──────────────────────────────► X (e.g., Complaints)
             (straight line — no ceiling/floor)
```

**Solution:** Squash the straight line through an **S-shaped (sigmoid) function** that naturally bounds output between 0 and 1.

## 3. The Sigmoid (Logistic) Function

$$ P(Y=1|X) = \frac{1}{1 + e^{-(\beta_0 + \beta_1 X)}} $$

```
  P(Y=1)
    1.0 ┤                    _______________
        │                 ╱
    0.5 ┤              ╱
        │           ╱
    0.0 ┤ _______╱
        └──────────────────────────────► β₀+β₁X (the "linear score")
                    (S-curve, always between 0 and 1)
```

No matter how extreme the input, the sigmoid output always stays between 0 and 1 — this is *why* logistic regression works where plain linear regression can't.

## 4. Odds, Log-Odds (Logit), and the Bridge Back to Linearity

This is the conceptual key that makes logistic regression *interpretable* — even though the probability curve is an S-shape, if you transform probability into **log-odds**, you get back a straight line!

| Term | Formula | Meaning |
|---|---|---|
| **Odds** | $\text{Odds} = \dfrac{P(Y=1)}{P(Y=0)} = \dfrac{p}{1-p}$ | "For every 1 person who does NOT churn, how many DO churn?" |
| **Log-Odds (Logit)** | $\text{logit}(p) = \ln\left(\dfrac{p}{1-p}\right) = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + ...$ | The **linear** combination of predictors — this is what logistic regression actually fits directly! |

**The whole model, in one line:** *Logistic regression is just a linear regression... on the log-odds scale.* Everything you know about interpreting coefficients from linear regression, you now apply to log-odds instead of the raw outcome.

## 5. Maximum Likelihood Estimation (MLE) — How Coefficients Are Found

Unlike linear regression's OLS ("minimize squared error"), logistic regression uses **Maximum Likelihood Estimation**: find the β coefficients that make the **observed data most probable** under the model.

**Intuition:** Try many different candidate lines/boundaries. For each candidate, ask: "If this were the true relationship, how likely is it that I'd see exactly this pattern of Yes/No outcomes in my data?" Pick the coefficients that make the actual observed outcomes *most likely*.

**Important Exam Fact — Perfect Separation:** If your data is **perfectly separable** (some feature threshold perfectly divides Yes from No with zero overlap), MLE coefficients **diverge to infinitely large magnitudes** — there's no finite best answer, because pushing β higher and higher keeps making the fit "more confident" and more likely, forever. (This is Quiz 1 Q9 — correct answer: coefficients diverge, no finite MLE.)

## 6. Decision Boundary and Classification Rule

- The **decision boundary** is where $P(Y=1) = 0.5$, which is exactly where **log-odds = 0**, i.e., where $\beta_0 + \beta_1 X_1 + \beta_2 X_2 = 0$ (Quiz 1 Q16).
- **Classification Rule:** Pick a probability **threshold** (commonly 0.5). If predicted probability > threshold → classify as "Yes" (or whichever is the positive class); else "No."
- The threshold is a **business choice**, not a mathematical law — lower it if false negatives are very costly (catch more true positives, tolerate more false alarms); raise it if false positives are very costly.

## 7. Interpreting Coefficients — Odds Ratios

This is the **#1 most commonly tested calculation** in the whole unit.

- A raw coefficient $\beta_j$ tells you: "A 1-unit increase in $X_j$ changes the **log-odds** of Y by $\beta_j$ (holding other variables fixed)." Log-odds aren't intuitive to a business audience, so we exponentiate.
- $e^{\beta_j}$ = the **Odds Ratio** — "A 1-unit increase in $X_j$ **multiplies** the odds of Y by $e^{\beta_j}$, holding other variables fixed." (Quiz 1 Q8 — correct answer: multiplicative change in odds, NOT probability.)

**Business Interpretation Examples:**
- If $\beta_j = 0.4055$, then $e^{0.4055} \approx 1.5$ → "each extra unit of $X_j$ makes churn **1.5× as likely** (a 50% increase in odds)."
- If $\beta_j$ is negative, $e^{\beta_j} < 1$ → the odds **shrink** — the variable is *protective* against the outcome.

**Common Mistake:** Students often say "$e^{\beta_j}$ = change in probability." **This is wrong.** It is a change in **odds**, and odds and probability are related but different scales (odds can range 0 to ∞; probability only 0 to 1).

## 8. Model Assumptions

- Observations are independent of each other.
- The **log-odds** (not the raw outcome) is a linear function of the predictors.
- No severe multicollinearity among predictors (same VIF logic as linear regression).
- Large enough sample size for stable MLE estimates (small/separable data → unstable/diverging coefficients, as above).

## 9. Advantages & Limitations

| Advantages | Limitations |
|---|---|
| Outputs a genuine probability, not just a label | Assumes a linear relationship on the log-odds scale — misses strongly non-linear boundaries unless you add interaction/polynomial terms |
| Coefficients are directly, cleanly interpretable via odds ratios | Struggles with perfectly/near-perfectly separable data (MLE instability) |
| Fast to train, works well even with modest data | Sensitive to multicollinearity, like linear regression |
| Easy to explain to regulators/auditors — the industry standard for credit, insurance, healthcare | Only handles linear decision boundaries in the log-odds space |

---

# PART D — Worked Example: Reading a Real Logistic Regression Output

*(This mirrors your Mid-Term Case 4 exactly — Telecom churn model: `Churn ~ Tickets_30d + Plan`.)*

## The R Output

```
Coefficients:
              Estimate  Std. Error  z value  Pr(>|z|)
(Intercept)    3.0674     0.8022     3.824   0.000131 ***
Tickets_30d   -0.5399     0.1322    -4.083   4.45e-05 ***
PlanPremium    1.8949     0.6785     2.793   0.005224 **

Null deviance: 103.591 on 79 degrees of freedom
Residual deviance: 71.622 on 77 degrees of freedom
```

## Step-by-Step Interpretation

| Output piece | Meaning |
|---|---|
| `(Intercept) = 3.0674` | Baseline log-odds when Tickets_30d = 0 and Plan = Standard (the reference category) |
| `Tickets_30d = -0.5399` | Each extra support ticket **decreases** log-odds by 0.5399 → odds multiply by $e^{-0.5399} \approx 0.58$ (a ~42% *reduction* in odds per extra ticket, holding Plan fixed) |
| `PlanPremium = 1.8949` | Being on Premium (vs. Standard baseline) changes log-odds by +1.8949 → odds multiply by $e^{1.8949} \approx 6.65$ |
| `Pr(>|z|)` column | p-value for each coefficient — all three are highly significant (p < 0.01) |
| `Null deviance` vs `Residual deviance` | Like SSE in linear regression — measures "lack of fit." Null deviance = fit with intercept only; Residual deviance = fit with your predictors. A big drop (103.6 → 71.6) tells you the predictors meaningfully improve the fit. |
| Degrees of freedom = 79 (null) | n − 1 = 79 → **n = 80 customers** in this dataset |

## Solved Numerical Questions (exact exam format)

**Q1: Log-odds of churn for a Premium subscriber with Tickets_30d = 3.5?**
$$ 3.0674 + (-0.5399 \times 3.5) + 1.8949 \times 1 = 3.0674 - 1.8897 + 1.8949 = 3.07 $$

**Q2: Predicted probability of churn for a Standard subscriber with Tickets_30d = 7.0?**
$$\text{log-odds} = 3.0674 + (-0.5399 \times 7.0) + 0 = 3.0674 - 3.7793 = -0.7119$$
$$ p = \frac{1}{1+e^{0.7119}} = \frac{1}{1+2.038} = 0.329 $$

**Q3: Decision boundary (p=0.5, log-odds=0) for a Standard subscriber?**
$$ 3.0674 - 0.5399x = 0 \implies x = \frac{3.0674}{0.5399} = 5.68 $$

**Q4: Change in log-odds when Tickets_30d increases by +2.0 (Plan fixed)?**
$$ -0.5399 \times 2.0 = -1.08 $$

**Q5: Total number of customers?**
Residual df = 77, and we used 3 parameters (intercept + 2 predictors) → n = 77 + 3 = **80**. (Cross-checked against Null deviance df = 79 = n − 1 = 80 − 1. ✓)

## Common Mistake to Watch For

When R codes a two-level factor as the response in `glm()`, it treats the **alphabetically/order-wise second level** as the modeled "success" (1) outcome — *not* necessarily the level you intended. Always sanity-check: do the **signs** of your coefficients match business intuition (e.g., "more support tickets should raise churn risk")? If the signs look backwards, you may be modeling the *opposite* class — verify with `levels()` before trusting the raw output.

---

# PART E — LDA (Linear Discriminant Analysis) — A Generative Classifier

## 1. Big Picture

LDA is your course's concrete example of a **generative** model (Part B). Instead of directly modeling P(Y|X) like logistic regression, LDA models each class's feature distribution (assuming Gaussian, shared covariance) and uses Bayes' theorem to classify.

## 2. Intuition

Picture two overlapping bell curves — one for "Defaulters," one for "Non-defaulters" — plotted over a feature like account balance. LDA finds the single point (or line, in higher dimensions) where the two bell curves cross, weighted by how common each class is (the **prior**). Any new customer is classified based on which bell curve they're more likely to have come from.

## 3. Worked Example (mirrors your End-Term Case 5 — Credit Default LDA)

```
Prior probabilities:  No = 0.75,  Yes = 0.25
Group means:
              Balance_k   LatePays
    No          810.21      2.77
    Yes        1695.73      5.43

Confusion Matrix:
              Predicted
Actual    No    Yes
   No     87     3
   Yes     2    28
```

**Interpretation:**
- **Prior = 0.75 for "No"** means: before even looking at any customer's balance or late-payment history, 75% of the population is a non-defaulter — this is simply the base rate in the training data.
- **Group means** tell you the "typical" customer profile the model has learned for each class: defaulters carry roughly **double** the balance ($1,695.73k vs $810.21k) and almost double the late payments (5.43 vs 2.77) of non-defaulters.
- **Total customers:** 87+3+2+28 = **120**.
- **Accuracy:** $(87+28)/120 = 115/120 = 0.96$ (96%).
- **If the model predicts "No" (non-default) when a customer actually defaulted (Yes)** — this is a **missed detection**, i.e., a **False Negative → Type II Error**. (The convention: Type I Error = false positive/false alarm; Type II Error = false negative/miss.)

## 4. Common Confusions

- **LDA vs Logistic Regression:** Both give linear decision boundaries, but LDA *assumes* the features are Gaussian with equal covariance across classes (generative), while logistic regression makes no such assumption (discriminative). If that Gaussian assumption roughly holds, LDA can be more stable/efficient with less data; if it's violated, logistic regression is often safer.
- **Type I vs Type II Error:** Type I = False Positive (crying wolf — flagging a genuine customer as a defaulter). Type II = False Negative (missing a real defaulter). This maps exactly onto Precision (Type I-averse) vs Recall (Type II-averse) from Unit 1.

---

# PART F — Classification With Imbalanced Data (SMOTE) + Full Model Diagnostics

*(Mirrors End-Term Case 6 — ride-hailing driver churn, extremely imbalanced 2476 "No" vs 24 "Yes.")*

## 1. Big Picture

Real-world classification problems are often **imbalanced** — churners, fraudsters, and defaulters are rare by nature. A model can get 99% accuracy by *always* predicting "No churn" and still be useless. This section is about **fixing the imbalance** and **diagnosing** the resulting model properly.

## 2. SMOTE (Synthetic Minority Over-sampling) — Intuition

Instead of just duplicating existing minority-class (rare) rows, SMOTE generates **new synthetic examples** that sit "between" real minority-class points in feature space — like sketching a few new dots along the lines connecting existing rare dots, so the model gets more (varied) exposure to the minority pattern without simply memorizing repeats.

**Worked calculation:** Original data: 2476 No, 24 Yes. After SMOTE (target N = 2×max class = 4952): resulting counts were 2496 No, 2456 Yes.
$$\text{Post-SMOTE churn (Yes) proportion} = \frac{2456}{2496+2456} = \frac{2456}{4952} = 0.4960 \rightarrow \mathbf{49.60\%}$$

## 3. Reading the Post-SMOTE Logistic Regression Output

```
Coefficients:
                Estimate   Std. Error   z value   Pr(>|z|)
(Intercept)      3.909728    0.586856    6.662    2.70e-11
Cancellations    0.678051    0.027276   24.859    < 2e-16
Weekly_Trips    -0.096242    0.003997  -24.078    < 2e-16
Avg_Rating      -0.501443    0.127053   -3.947    7.92e-05
VerifiedYes     -0.722871    0.072701   -9.943    < 2e-16
```

**Strongest risk-increasing driver:** Look for the largest **positive** coefficient (magnitude matters, but sign tells direction) — here, **Cancellations (+0.678)** is the only positive driver of churn: more rider cancellations attributed to a driver → sharply higher churn odds ($e^{0.678} \approx 1.97$, almost doubling odds per cancellation). All other predictors (Weekly_Trips, Avg_Rating, VerifiedYes) are protective (negative coefficients — they *reduce* churn odds).

## 4. Confusion Matrix, ROC, AUC — In Context

```
              Actual
Predicted    No     Yes
    No      1840    690
    Yes      656    1766
```

- **False Alarms (False Positives)** = predicted "Yes" (churn) but actually "No" = **656**.
- **AUC = 0.811** — Recall from Unit 1: this measures ranking quality across *all* thresholds, independent of the 0.5 cutoff. 0.811 indicates good (not perfect) ability to rank churners above non-churners.
- **"Remaining lack-of-fit after all predictors" (Residual Deviance = 5235.2)** — this is the *deviance-based* analogue of "unexplained variation" — lower is better, and it's what remains after Cancellations, Weekly_Trips, Avg_Rating, and Verified have done their work.

## 5. Exam Perspective

- SMOTE questions almost always ask you to compute the **new class proportion** — practice this division quickly.
- "Strongest driver" questions require you to scan **all** coefficients and correctly separate risk-increasing (positive) from protective (negative) — don't just pick the largest absolute value blindly if the sign matters for the specific question asked.
- False alarm / false negative counts come straight from the confusion matrix — always double check which axis is "Predicted" vs "Actual" before reading off a cell.

---

# PART G — Master Formula Sheet (Unit 3)

| Formula | Meaning | When to Use |
|---|---|---|
| $p = \dfrac{1}{1+e^{-(\beta_0+\beta_1X)}}$ | Sigmoid — converts linear score to probability | Getting predicted probability from a fitted logistic model |
| $\text{Odds} = \dfrac{p}{1-p}$ | Odds from probability | Converting between probability and odds |
| $\ln\left(\dfrac{p}{1-p}\right) = \beta_0+\beta_1X_1+...$ | Log-odds (logit) — the linear part of the model | Computing/interpreting raw coefficients |
| $e^{\beta_j}$ | Odds Ratio for predictor $X_j$ | Business interpretation of a coefficient |
| Decision boundary: $\beta_0+\beta_1X_1+\beta_2X_2=0$ | Where $p=0.5$ | Finding the cutoff value of X where classification flips |
| $P(Y|X) = \dfrac{P(X|Y)P(Y)}{P(X)}$ | Bayes' theorem | The mechanism behind generative models (LDA, Naive Bayes) |
| Accuracy $=\dfrac{TP+TN}{Total}$ | Overall correctness | Simple summary metric (careful with imbalance!) |

---

# PART H — Common Confusions Recap (Unit 3)

| Confused Pair | Resolution |
|---|---|
| Odds vs Probability | Odds = p/(1-p), ranges 0 to ∞. Probability ranges 0 to 1. $e^{\beta}$ changes **odds** multiplicatively, not probability. |
| Log-Odds vs Likelihood | Log-odds is the *linear score* inside the sigmoid for one observation. Likelihood is the *overall probability of the entire observed dataset* given a candidate set of coefficients — used by MLE to fit the model, not to describe one prediction. |
| Generative vs Discriminative | Generative (LDA, Naive Bayes) models how X is distributed *within* each class, then uses Bayes' rule. Discriminative (Logistic Regression) models the boundary/P(Y\|X) directly. |
| Type I vs Type II Error | Type I = False Positive (false alarm). Type II = False Negative (missed detection). |
| Decision Boundary vs Probability Threshold | The threshold (e.g., 0.5) is a *chosen* business cutoff on probability. The decision boundary is the corresponding *location in feature space* (a specific X value or line) where that threshold is crossed. |

---

# ✅ UNIT 3 — MASTER ONE-PAGE REVISION SHEET

1. **Classification** predicts a category; most classifiers output a **probability** first, then apply a **threshold** to get the final label.
2. **Generative models** (Naive Bayes, LDA) model P(X\|Y) and P(Y), then use **Bayes' theorem** to get P(Y\|X). **Discriminative models** (Logistic Regression) model P(Y\|X) directly.
3. **Linear regression fails for classification** because it has no ceiling/floor — the **sigmoid function** fixes this, always outputting between 0 and 1.
4. **Log-odds = linear function of predictors.** This is the mathematical trick that keeps logistic regression interpretable.
5. **Odds Ratio = $e^{\beta_j}$** — a *multiplicative* change in odds per unit increase in $X_j$, NOT a change in probability.
6. **MLE**, not OLS, fits logistic regression — perfectly separable data causes coefficients to diverge (no finite MLE).
7. **Decision boundary** = where log-odds = 0, i.e., $p = 0.5$.
8. **LDA** assumes Gaussian, equal-covariance features per class; gives you Priors, Group Means, and a linear discriminant.
9. **Type I Error** = False Positive. **Type II Error** = False Negative (a missed defaulter/churner/fraud case).
10. **SMOTE** rebalances a skewed dataset by synthesizing new minority-class points — always double-check the resulting class proportions.
11. **AUC** summarizes ranking quality across all thresholds; the **confusion matrix** at a *specific* threshold (usually 0.5) gives you the operational false-alarm/missed-detection counts businesses actually act on.

---

*End of Unit 3. Since you're studying backward from the end: Unit 2 (Regression — Simple & Multiple Linear Regression, R output interpretation, VIF, dummy variables) is next whenever you're ready, followed by a final revision pass connecting all three units together.*
