# FAQs — Classification (Logistic Regression), Sessions 9–12

---

## Part A: Conceptual Basics

**Q1. What is a classification problem in supervised learning? (Short)**
Classification is a supervised learning task that learns a function *f: X → Y*, mapping a feature vector *X* to one of *K* discrete class labels *Y = {1, 2, …, K}*, using a labeled training set {(X₁,Y₁), …, (Xₙ,Yₙ)}.

**Q2. How is the input space different from the output space in classification? (Short)**
The input space *X* is typically continuous (real-valued features), while the output space *Y* is discrete and finite (a fixed set of class labels).

**Q3. What is the difference between generative and discriminative models? (Long)**
- **Generative models** learn the *joint* distribution P(X, Y) by modeling P(X|Y) and P(Y) separately. They try to understand *how the data was generated*, and because of this they can also generate new synthetic data. Examples: Naive Bayes, HMM, GMM, GANs, VAEs, LDA (Linear Discriminant Analysis), QDA.
- **Discriminative models** directly estimate the *conditional* distribution P(Y|X) — i.e., they focus only on the decision boundary between classes rather than how the data arose. Examples: Logistic Regression, SVM, Decision Trees, Random Forests, k-NN, Neural Networks.

| Aspect | Generative | Discriminative |
|---|---|---|
| Models | P(X,Y) | P(Y\|X) |
| Can generate new data | Yes | No |
| Training complexity | Higher | Lower |
| Typical accuracy (classification) | Often lower | Often higher |

**Q4. Why is logistic regression called "discriminative" and not "generative"?**
Because it directly models P(Y|X) — the probability of a class given the features — without ever modeling how X itself was generated (i.e., it never estimates P(X|Y) or P(X)). It only cares about separating classes, not simulating data.

**Q5. Name two strengths and two weaknesses of generative models.**
Strengths: (1) can simulate/generate new data, (2) can naturally handle missing data.
Weaknesses: (1) more computationally expensive to train, (2) harder to tune since it involves modeling the full joint distribution.

---

## Part B: Logistic Regression Mechanics

**Q6. Write the logistic regression formula for P(Y=1|X) and explain each term. (Long)**
$$P(Y=1\mid X) = \frac{e^{(\beta_0+\beta_1x_1+\beta_2x_2)}}{1+e^{(\beta_0+\beta_1x_1+\beta_2x_2)}} = \frac{1}{1+e^{-(\beta_0+\beta_1x_1+\beta_2x_2)}}$$
- β₀ is the intercept (log-odds when all predictors = 0).
- β₁, β₂ are coefficients that quantify how each feature (x₁, x₂) shifts the log-odds of Y=1.
- The **sigmoid (logistic) function** squashes the linear combination β₀+β₁x₁+β₂x₂ (which can range from −∞ to +∞) into a probability between 0 and 1.

**Q7. How does logistic regression convert a predicted probability into a class label? (Short)**
Using a threshold, typically 0.5:
- P(Y=1|X) ≥ 0.5 ⇒ predict Y = 1
- P(Y=1|X) < 0.5 ⇒ predict Y = 0

**Q8. What is a decision boundary, and what equation defines it in logistic regression? (Short)**
The decision boundary is the line/hyperplane where the model is equally likely to predict either class (probability = 0.5). It occurs where the linear predictor equals zero:
$$\beta_0+\beta_1x_1+\beta_2x_2=0$$
This line splits the feature space into a region predicted as Y=1 and a region predicted as Y=0.

**Q9. Why is the decision boundary linear even though the sigmoid function is non-linear?**
Because the boundary is defined by setting the *argument* of the sigmoid (the linear combination β₀+β₁x₁+β₂x₂) to zero, not the sigmoid output itself. Since that argument is a linear function of the inputs, the boundary itself is linear (a straight line in 2D, a hyperplane in higher dimensions) — the non-linearity of the sigmoid only affects how probabilities are distributed on either side of that line.

---

## Part C: Odds, Log-Odds, and Odds Ratio

**Q10. Define "odds" and give a numeric example. (Short)**
Odds = p̂ / (1−p̂), where p̂ is the probability the event occurs. Example: if p̂ = 0.2, Odds = 0.2/0.8 = 0.25, meaning for every 1 event that occurs, about 4 do not.

**Q11. What is the "log-odds" (logit), and how does it relate to the logistic regression equation? (Long)**
The log-odds is the natural log of the odds:
$$\log\left(\frac{\hat p}{1-\hat p}\right) = \beta_0+\beta_1x_1+\beta_2x_2$$
This is the key reformulation that makes logistic regression a *linear* model — although P(Y|X) itself is a non-linear (S-shaped) function of X, the **log-odds** is a linear function of X. Each coefficient βⱼ tells you how much the log-odds change for a one-unit increase in xⱼ, holding other variables constant.

**Q12. What is an odds ratio (OR), and how do you compute it from a logistic regression coefficient? (Short)**
The odds ratio compares the odds of an event between two groups (e.g., students vs. non-students). For a coefficient β on a predictor, OR = e^β. It tells you the multiplicative change in odds associated with a one-unit change (or category change) in that predictor.

**Q13. In the Credit Default model, βstudentYes = −0.7149. Interpret this fully. (Long)**
- **Log-odds interpretation:** Being a student decreases the log-odds of default by 0.7149 relative to a non-student, holding balance constant.
- **Odds ratio:** OR = e^(−0.7149) ≈ 0.489.
- **Plain-language interpretation:** The odds of default for a student are about 48.9% of the odds for a non-student (i.e., roughly 51.1% lower), for the same credit card balance. So, at equal balance levels, students are meaningfully less likely to default than non-students.

**Q14. If a coefficient β = 0.005738 for "balance," what does e^β mean?**
e^0.005738 ≈ 1.0057. This means each additional dollar of balance increases the odds of default by about 0.57%. Small per-dollar effect, but it compounds — someone with $1,000 more balance has meaningfully higher odds than someone with a low balance.

---

## Part D: Model Fitting, Deviance, and AIC (with R code)

**Q15. What R code fits a logistic regression predicting `default` from `balance`, and how do you read the output? (Long, includes R code)**
```r
data <- Default
model <- glm(default ~ balance, data = data, family = binomial)
summary(model)
```
- `glm()` = Generalized Linear Model; `family = binomial` tells R to fit a **logistic regression** (binomial family with a logit link), rather than ordinary linear regression.
- The `summary()` output gives coefficient estimates, standard errors, z-values, and p-values (testing whether each coefficient is significantly different from 0), plus deviance and AIC statistics.
- From the slide: Intercept = −10.65, balance coefficient = 0.005499, both highly significant (p < 2e-16), meaning balance is a strong predictor of default.

**Q16. What is Null Deviance vs. Residual Deviance, and how should you interpret their difference? (Long)**
- **Null deviance** measures the fit of a model with *only an intercept* (no predictors) — it reflects total unexplained variability in the response.
- **Residual deviance** measures the fit of the model *including* the predictors.
- A large **drop** from null deviance to residual deviance (e.g., 2920.6 → 1571.7 in the balance+student model) indicates the predictors substantially improve model fit — much of the variability in `default` is being explained by balance and student status.

**Q17. What is AIC, and why is a lower AIC "better"? (Short)**
AIC = Residual Deviance + 2 × (number of parameters). It rewards better fit (lower deviance) but penalizes model complexity (more parameters), so a lower AIC indicates a better trade-off between explanatory power and simplicity — useful for comparing competing models on the same data.

**Q18. In the lecture, three models were fit: `balance` only, `balance+income+student`, and `balance+student`. Which was preferred and why? (Long)**
The `balance+student` model (AIC = 1577.7) was preferred over `balance+income+student` (AIC = 1579.5) because it achieved a very similar residual deviance (1571.7 vs. 1571.5) with **one fewer parameter** (dropping `income`, whose coefficient was statistically insignificant, p = 0.712). Since AIC penalizes unnecessary complexity, the simpler model with essentially the same fit gets a lower (better) AIC — this illustrates the principle that **insignificant predictors can be dropped without meaningfully hurting fit**.

**Q19. Why was `income` dropped from the final model?**
Its coefficient (3.033e-06) had a p-value of 0.712, far above conventional significance thresholds (e.g., 0.05), meaning there's no statistical evidence income affects default probability once balance and student status are accounted for.

**Q20. Write R code to visualize the decision boundary of a logistic regression model with `balance` and `student` as predictors. (Long, includes R code)**
```r
library(ISLR2); library(ggplot2)
data <- Default
model <- glm(default ~ balance + student, data = data, family = binomial)

balance_grid <- seq(min(data$balance), max(data$balance), length.out = 100)
prediction_grid <- expand.grid(balance = balance_grid, student = c("Yes", "No"))
prediction_grid$prob <- predict(model, newdata = prediction_grid, type = "response")

ggplot(data, aes(x = balance, y = as.numeric(student == "Yes"), color = default)) +
  geom_point(alpha = 0.6) +
  geom_contour(data = prediction_grid,
               aes(x = balance, y = as.numeric(student == "Yes"), z = prob),
               breaks = 0.5, color = "black")
```
Key idea: `expand.grid()` builds a fine grid over the predictor space; `predict(..., type="response")` returns *probabilities* (not log-odds, because `type="response"` applies the inverse-logit transform); `geom_contour(breaks=0.5)` draws the line where predicted probability crosses 0.5 — this is exactly the decision boundary.

---

## Part E: Model Evaluation — Confusion Matrix and Metrics

**Q21. What is a confusion matrix, and what do TP, FP, FN, TN mean in the credit default context? (Long)**
A confusion matrix cross-tabulates predicted vs. actual classes.
| | Actual: Default=Yes | Actual: Default=No |
|---|---|---|
| Predicted: Yes | True Positive (TP) | False Positive (FP) |
| Predicted: No | False Negative (FN) | True Negative (TN) |

- **TP:** correctly flagged an actual defaulter.
- **FP:** wrongly flagged a good customer as risky (a "false alarm").
- **FN:** missed an actual defaulter (predicted they wouldn't default, but they did).
- **TN:** correctly identified a non-defaulter.
- In credit risk, **false negatives are usually more costly** — failing to catch a real defaulter directly costs the lender money, whereas a false positive just means unnecessary caution toward a good customer.

**Q22. Define Accuracy, Sensitivity (Recall), Specificity, and Precision, and explain what each answers. (Long)**
$$\text{Accuracy} = \frac{TP+TN}{TP+TN+FP+FN} \quad \text{Sensitivity} = \frac{TP}{TP+FN} \quad \text{Specificity} = \frac{TN}{TN+FP} \quad \text{Precision} = \frac{TP}{TP+FP}$$
- **Accuracy:** overall, what fraction of predictions were correct?
- **Sensitivity (Recall):** of all actual defaulters, how many did the model catch?
- **Specificity:** of all actual non-defaulters, how many did the model correctly clear?
- **Precision:** of everyone flagged as a defaulter, how many actually defaulted?
- All four depend on the chosen classification **threshold** (e.g., 0.5) — changing the threshold trades off these metrics against each other.

**Q23. In the lecture's test-set results, Sensitivity = 0.364 but Specificity = 0.994, and overall Accuracy = 0.974. Why is accuracy misleading here? (Long)**
The dataset is highly **imbalanced** — only about 3.3% of customers actually default (Prevalence = 0.033). A model could achieve ~96.7% accuracy just by predicting "No Default" for everyone (the "No Information Rate"), without identifying any actual defaulters. Here, sensitivity of only 36.4% means the model misses the majority of true defaulters (63 missed vs. 36 caught) — which, as noted above, is the costlier error type in this business context. So even though accuracy (97.4%) looks great, it hides poor performance on the minority class of real interest, and metrics like Sensitivity, Precision, or the ROC/AUC give a fuller picture.

**Q24. R code: How do you compute a confusion matrix and metrics using the `caret` package? (Long, includes R code)**
```r
library(caret)
test_pred <- factor(ifelse(test_prob >= 0.50, "Yes", "No"), levels = c("No","Yes"))
test_actual <- factor(test_data$default, levels = c("No", "Yes"))
confusionMatrix(data = test_pred, reference = test_actual, positive = "Yes")
```
- `ifelse()` applies the 0.50 threshold to convert predicted probabilities into class labels.
- `positive = "Yes"` tells `caret` which class to treat as the "positive" class when computing Sensitivity/Specificity/Precision.
- The `factor(..., levels = c("No","Yes"))` step ensures predicted and actual factors share identical level ordering, which is required for `confusionMatrix()` to align rows/columns correctly.

**Q25. What is a stratified train-test split, and why use `createDataPartition()` instead of a simple random split? (Short)**
```r
train_index <- createDataPartition(data$default, p = 0.70, list = FALSE)
```
`createDataPartition()` performs a **stratified** split — it preserves the same proportion of "Yes"/"No" default cases in both the training and test sets as in the full data. This matters especially for imbalanced data (like default, at ~3.3% prevalence), since a plain random split could accidentally leave too few (or zero) positive cases in the test set.

---

## Part F: ROC Curve and AUC

**Q26. What is an ROC curve, and what do its two axes represent? (Short)**
The ROC (Receiver Operating Characteristic) curve plots **Sensitivity (True Positive Rate)** on the y-axis against **Specificity** (or 1 − Specificity, the False Positive Rate) on the x-axis, as the classification threshold is varied continuously from 0 to 1. It shows the trade-off between catching more true positives and generating more false positives.

**Q27. What does the AUC (Area Under the Curve) mean, and how do you interpret an AUC of 0.9561? (Long)**
AUC summarizes the ROC curve into a single number between 0 and 1, representing the probability that the model ranks a randomly chosen actual positive (defaulter) higher than a randomly chosen actual negative (non-defaulter), across all thresholds.
- AUC = 0.5 → no better than random guessing (diagonal line).
- AUC = 1.0 → perfect separation of classes.
- **AUC = 0.9561** in this example indicates the model has **excellent discriminative ability** — it is very good at ranking defaulters as riskier than non-defaulters, even though a single fixed 0.5 threshold (as in Q23) doesn't capture this well due to class imbalance. This is why AUC is often preferred over accuracy for imbalanced classification problems — it's threshold-independent.

**Q28. R code: How do you generate an ROC curve and compute AUC using the `pROC` package? (Long, includes R code)**
```r
library(pROC)
test_roc <- roc(response = test_actual, predictor = test_prob,
                 levels = c("No", "Yes"), direction = "<")
plot(test_roc, main = "ROC Curve on Test Data")
auc(test_roc)
```
- `response` = actual class labels; `predictor` = predicted *probabilities* (not hard class labels) — the ROC curve needs continuous scores to sweep across thresholds.
- `levels = c("No","Yes")` defines which level is the "control" vs. "case."
- `direction = "<"` tells `pROC` that higher predictor values are associated with the "Yes"/positive class (i.e., higher predicted probability → more likely to default).
- `auc()` extracts the scalar AUC value from the fitted ROC object.

---

## Part G: Quick-Fire Short Q&A (rapid review)

**Q29.** What does K represent in Y = {1, 2, …, K}? — *The number of distinct classes.*

**Q30.** Is logistic regression generative or discriminative? — *Discriminative.*

**Q31.** What function maps a linear combination of predictors to a probability in [0,1]? — *The sigmoid/logistic function.*

**Q32.** At what probability value does logistic regression's default decision rule switch classes? — *0.5.*

**Q33.** What sign would you expect for a coefficient on a "risk factor" that increases default probability? — *Positive.*

**Q34.** What sign did the `studentYes` coefficient have in the models, and what does it imply? — *Negative; being a student is associated with lower default risk (holding balance fixed).*

**Q35.** Which metric answers "of those we flagged as risky, how many really were"? — *Precision.*

**Q36.** Which metric answers "of the actual defaulters, how many did we catch"? — *Sensitivity/Recall.*

**Q37.** True or False: A model with higher accuracy always has better AUC. — *False — accuracy is threshold- and class-balance-dependent; AUC is threshold-independent and can reveal strong ranking ability even when accuracy (at one threshold) looks unimpressive on the minority class.*

**Q38.** In `glm()`, what argument specifies that you're fitting a logistic regression rather than linear regression? — *`family = binomial`.*

**Q39.** What does `type = "response"` do inside `predict()` for a `glm` logistic model? — *Returns predicted probabilities (applies the inverse-logit/sigmoid transform), rather than raw log-odds.*

**Q40.** Why might a bank prefer to lower the classification threshold below 0.5 for flagging defaults? — *To increase sensitivity (catch more true defaulters) at the cost of more false positives — appropriate when false negatives (missed defaulters) are more costly than false positives, as is typical in credit risk.*

---

*Prepared from: "Applied AI & ML — Classification Part I" (Sessions 9–12) lecture slides.*
