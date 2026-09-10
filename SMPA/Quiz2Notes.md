# Exam-ready notes for MCQ Quiz 
## Probability Foundations → Random Variables & Distributions


---

## 📋 HOW THIS DOCUMENT IS ORGANIZED

| Part | Sessions | Topics |
|---|---|---|
| **A** | 13–14 | Probability Foundations, Events, Addition Rule |
| **B** | 15 | Conditional Probability & Independence |
| **C** | 16 | Bayes Theorem |
| **D** | 17 | Random Variables, PMF, Expected Value, Variance |
| **E** | 18–19 | Continuous Random Variables, PDF, CDF |
| **F** | 20 | Joint, Marginal & Conditional Distributions |
| **G** | — | Master Formula Sheet, FAQs, MCQ Bank, Traps |

---

# PART A — PROBABILITY FOUNDATIONS (Sessions 13–14)

## A1. Core Vocabulary Chain

```
RANDOM PHENOMENON → RANDOM EXPERIMENT → OUTCOME → SAMPLE SPACE (S) → EVENT
```

| Term | Meaning | Example |
|---|---|---|
| **Random Phenomenon** | Individual outcome can't be predicted with certainty, though possible outcomes are known | Whether an order is late |
| **Random Experiment** | A repeatable process that produces an uncertain outcome | Dispatch an order |
| **Outcome** | One specific result of the experiment | "Late" |
| **Sample Space (S)** | Complete set of ALL possible outcomes | S = {On Time, Late} |
| **Event** | A subset of the sample space; one or more outcomes of interest | A = {Rework, Scrap} |

> ⚠️ **Key distinction:** Outcome = "What happened?" Event = "What are we interested in?" An event can be a **single outcome (simple event)** or **multiple outcomes (compound event)**.

**Sample space depends on how the experiment is defined:**
- One order → S = {O, L}
- Two orders → S = {(O,O), (O,L), (L,O), (L,L)}

### Types of Events
| Type | Definition | Example |
|---|---|---|
| Simple Event | Exactly 1 outcome | A = {5 stars} |
| Compound Event | More than 1 outcome | B = {4,5 stars} |
| Certain Event | Entire sample space; P(S) = 1 | Order is On Time or Late |
| Impossible Event | Cannot occur; P = 0 | Rating = 7 when S={1..5} |

## A2. Probability Scale

$$0 \le P(A) \le 1$$

| P(A) | % | Meaning |
|---|---|---|
| 0 | 0% | Impossible |
| 0.25 | 25% | Unlikely |
| 0.50 | 50% | Equally likely |
| 0.75 | 75% | Likely |
| 1.00 | 100% | Certain |

> 🔑 **Critical exam trap:** Probability describes **likelihood over repeated trials**, NOT a guarantee for one specific future outcome. P(Late)=0.10 does NOT mean "exactly 1 in every 10 orders is late" or "order #10 will be late."

## A3. Classical vs Empirical Probability

| | Classical (Theoretical) | Empirical |
|---|---|---|
| Based on | Equally likely theoretical outcomes | Observed historical data |
| Formula | Favourable outcomes ÷ Total outcomes | Frequency of A ÷ Total observations |
| Example | P(6 on fair die) = 1/6 | 140 late out of 1000 → P(Late)=0.14 |
| Common in | Coins, dice, cards | Business analytics |

$$P(A) = \frac{\text{Number of favourable outcomes}}{\text{Total number of possible outcomes}} \quad \text{(Classical)}$$

$$P(A) = \frac{\text{Frequency of A}}{\text{Total Frequency}} \quad \text{(Empirical)}$$

> ⚠️ **Common MCQ trap:** "Two possible outcomes" does NOT automatically mean 50-50. On Time/Late are 2 outcomes but P(On Time)=0.86, P(Late)=0.14 — NOT equal!

### 🧮 Worked Example (Empirical Probability)
QuickKart: 860 On Time, 140 Late out of 1000 orders.
- P(Late) = 140/1000 = **0.14 (14%)**
- P(On Time) = 860/1000 = **0.86 (86%)**

## A4. Set Operations on Events (A = Late, B = Returned)

| Operation | Meaning | Notation | Venn Diagram Idea |
|---|---|---|---|
| **OR (Union)** | A occurs, B occurs, or both | A ∪ B | Combined area of both circles |
| **AND (Intersection)** | Both A and B occur together | A ∩ B | Overlapping area only |
| **NOT (Complement)** | A does not occur | Aᶜ | Everything outside A |

**Complement Rule:**
$$P(A^c) = 1 - P(A)$$
If P(Late) = 0.14 → P(Not Late) = 1 − 0.14 = **0.86**

## A5. Mutually Exclusive Events

Events that **cannot occur at the same time** (no overlap).

$$P(A \cap B) = 0 \quad \text{(for mutually exclusive events)}$$

Example: An order is either On Time OR Late — never both.

> ⚠️⚠️ **HIGH-YIELD TRAP:** Mutually Exclusive ≠ Independent!
> - Mutually Exclusive: events **cannot co-occur** — knowing A occurred means B is now **impossible**, so it DOES change P(B). This means mutually exclusive events (with positive probabilities) are **automatically DEPENDENT**, never independent!
> - Independent: events **can co-occur**, and knowing one occurred **doesn't change** the probability of the other.

## A6. The Addition Rule

$$P(A \cup B) = P(A) + P(B) - P(A \cap B)$$

*(Subtract the intersection once to avoid double-counting.)*

**Special case — mutually exclusive events** (P(A∩B)=0):
$$P(A \cup B) = P(A) + P(B)$$

### 🧮 Worked Example — QuickKart (Addition Rule)

| Delivery Status | Returned | Not Returned | Total |
|---|---|---|---|
| Late | 8 | 7 | 15 |
| On Time | 4 | 21 | 25 |
| **Total** | **12** | **28** | **40** |

- P(A) = P(Late) = 15/40 = 0.375
- P(B) = P(Returned) = 12/40 = 0.300
- P(A ∩ B) = 8/40 = 0.200
- **P(A ∪ B) = 0.375 + 0.300 − 0.200 = 0.475 (47.5%)**

*Interpretation: 47.5% of orders had at least one issue (late OR returned) — not the sum 67.5%, because 20% had BOTH and would be double-counted.*

### 🧮 Worked Example — Manufacturing
200 components: 24 dimensional defects, 18 surface defects, 7 both.
$$P(A \cup B) = \frac{24}{200} + \frac{18}{200} - \frac{7}{200} = \frac{35}{200} = 17.5\%$$
*(NOT 12%+9%=21% — that double-counts the 7 with both defects)*

## A7. Basic Properties of Probability (memorize!)

| Property | Meaning |
|---|---|
| 0 ≤ P(A) ≤ 1 | Never negative, never above 1 |
| P(S) = 1 | Something in sample space must occur |
| P(∅) = 0 | Impossible event has zero probability |
| P(A) + P(Aᶜ) = 1 | Event + complement = whole space |

## A8. Excel Quick Reference (Part A)
- `COUNTIF(range, "Late")` → single condition count
- `COUNTIFS(range1,"Late",range2,"Returned")` → intersection (AND) count

---

# PART B — CONDITIONAL PROBABILITY & INDEPENDENCE (Session 15)

## B1. What Is Conditional Probability?

The probability of B **given that A has already occurred** — narrows the relevant population.

$$P(B \mid A) = \frac{P(A \cap B)}{P(A)}, \quad P(A) > 0$$

> 🔑 **GOLDEN RULE (memorize this — appears constantly in MCQs):**
> **The event AFTER the vertical bar "|" determines the denominator.**
> For P(B|A) → denominator relates to A.

## B2. Worked Example — QuickKart (40 orders)

| Delivery Status | Returned | Not Returned | Total |
|---|---|---|---|
| Late | 8 | 7 | **15** |
| On Time | 4 | 21 | 25 |
| Total | 12 | 28 | 40 |

$$P(\text{Returned}\mid \text{Late}) = \frac{8/40}{15/40} = \frac{8}{15} = 53.33\%$$

**Reversing the condition:**
$$P(\text{Late}\mid \text{Returned}) = \frac{8}{12} = 66.67\%$$

> ⚠️ **CRITICAL TRAP:** P(A|B) ≠ P(B|A) in general! Same numerator (8), **different denominators** (15 vs 12) because the "given" group changes.

### Full Conditional Table (very testable)
| Condition | Calc | Probability |
|---|---|---|
| Returned \| Late | 8/15 | 53.33% |
| Not Returned \| Late | 7/15 | 46.67% |
| Returned \| On Time | 4/25 | 16.00% |
| Not Returned \| On Time | 21/25 | 84.00% |
| Late \| Returned | 8/12 | 66.67% |
| On Time \| Returned | 4/12 | 33.33% |
| Late \| Not Returned | 7/28 | 25.00% |
| On Time \| Not Returned | 21/28 | 75.00% |

## B3. Marginal vs Joint vs Conditional (very high-yield distinction)

| Type | Question | Formula/Example | Result |
|---|---|---|---|
| **Marginal** | P(one event)? | P(Returned) = 12/40 | 30.00% |
| **Joint** | P(both events together)? | P(Late AND Returned) = 8/40 | 20.00% |
| **Conditional** | P(one event within a restricted group)? | P(Returned\|Late) = 8/15 | 53.33% |

> **Memory trick:** Marginal = 1 event (uses margins/totals of table). Joint = 2 events with AND (uses total sample as denominator). Conditional = 1 event within a restricted subgroup (uses restricted denominator).

## B4. Multiplication Rule

Rearranging the conditional probability formula:
$$P(A \cap B) = P(A) \times P(B \mid A) = P(B) \times P(A \mid B)$$

*Verification: P(Late)=0.375 × P(Returned|Late)=0.5333 ≈ 0.20 = 8/40 ✓*

## B5. Independence

Two events are **independent** if knowing one occurred does NOT change the probability of the other.

$$\text{Independent} \iff P(B \mid A) = P(B) \iff P(A \cap B) = P(A) \times P(B)$$

### 🧮 Worked Example — Testing Independence
QuickKart: P(Returned) = 30%, but P(Returned|Late) = 53.33% → **NOT equal → NOT independent**

Cross-check via multiplication: independent would give 0.375 × 0.30 = 11.25%, but actual joint = 20% → confirms dependence.

## B6. Mutually Exclusive vs Independent (recap table — exam favorite)

| | Mutually Exclusive | Independent |
|---|---|---|
| Can co-occur? | No | Yes |
| Formula | P(A∩B) = 0 | P(A∩B) = P(A)P(B) |
| Effect of A on B | Rules B out completely | Doesn't change P(B) at all |
| Relationship | If both have positive probability, they are **automatically dependent** | Separate concept entirely |

## B7. Worked Example — Waiting Time & Complaints

| Service Experience | Complained | Did Not | Total |
|---|---|---|---|
| Long Wait | 90 | 60 | 150 |
| Normal Wait | 50 | 300 | 350 |
| Total | 140 | 360 | 500 |

- P(Complaint) = 140/500 = 28%
- P(Complaint\|Long Wait) = 90/150 = **60%**
- P(Complaint\|Normal Wait) = 50/350 = **14.29%**

> ⚠️ Compare **rates**, not raw counts, when group sizes differ (90 vs 50 looks close, but 60% vs 14.29% tells the real story).

## B8. Excel Reference (Part B)
```
Conditional P(Returned|Late) = COUNTIFS(B:B,"Late",C:C,"Returned") / COUNTIF(B:B,"Late")
```

---

# PART C — BAYES THEOREM (Session 16)

## C1. The Core Idea

Bayes Theorem **reverses** a known conditional probability. We often know P(Evidence|Condition) but need P(Condition|Evidence).

| Concept | Meaning | Question |
|---|---|---|
| **Prior** P(A) | Probability before evidence | What did we know before? |
| **Evidence** P(B) | New information observed | What did we observe? |
| **Likelihood** P(B\|A) | How likely is evidence, if condition true | — |
| **Posterior** P(A\|B) | Updated probability after evidence | What should we believe now? |

## C2. Bayes Formula

$$P(A \mid B) = \frac{P(B \mid A) \times P(A)}{P(B)}$$

**Where does it come from?** From equating the two forms of the Multiplication Rule:
P(A∩B) = P(A)P(B|A) = P(B)P(A|B) → rearrange → Bayes formula.

**Total Probability (when P(B) isn't given directly):**
$$P(B) = P(B\mid A)P(A) + P(B\mid A^c)P(A^c)$$

Full form:
$$P(A\mid B) = \frac{P(B\mid A)P(A)}{P(B\mid A)P(A) + P(B\mid A^c)P(A^c)}$$

## C3. 🌟 THE Classic Worked Example — Fraud Detection (memorize this fully)

**Given:** P(Fraud)=2%, P(Genuine)=98%, P(Flagged\|Fraud)=90%, P(Flagged\|Genuine)=5%.

**Step-by-step:**
| Step | Calculation | Result |
|---|---|---|
| P(Fraud ∩ Flagged) | 0.02 × 0.90 | 0.018 (1.8%) |
| P(Genuine ∩ Flagged) | 0.98 × 0.05 | 0.049 (4.9%) |
| P(Flagged) | 0.018 + 0.049 | 0.067 (6.7%) |
| **P(Fraud \| Flagged)** | 0.018 / 0.067 | **0.2687 (26.87%)** |

### Verification with 10,000 transactions (natural frequencies)
| Actual | Flagged | Not Flagged | Total |
|---|---|---|---|
| Fraud | 180 | 20 | 200 |
| Genuine | 490 | 9310 | 9800 |
| **Total** | **670** | 9330 | 10000 |

P(Fraud\|Flagged) = 180/670 = **26.87%** ✓ matches!

> 🔑 **WHY THIS IS SURPRISING (guaranteed exam concept):** The system catches 90% of actual fraud, yet only 26.87% of FLAGGED transactions are truly fraudulent. This happens because **fraud is rare (2%)** — even a small 5% false-positive rate on the huge genuine population (9800) generates more false flags (490) than true flags (180) combined... wait, actually true flags (180) < false flags (490), so most alerts are false alarms.

> ⚠️⚠️ **GOLDEN RULE:** Strong evidence (90% detection) does NOT automatically imply high posterior probability when the underlying event is **rare**. Always check the **prior/base rate**.

## C4. Worked Example — Supplier Quality

Supplier A: 70% of components, 2% defect rate. Supplier B: 30% of components, 6% defect rate. A defective component is found — probability it's from Supplier B?

| Step | Calc | Value |
|---|---|---|
| P(A∩D) | 0.70 × 0.02 | 0.014 |
| P(B∩D) | 0.30 × 0.06 | 0.018 |
| P(D) | 0.014+0.018 | 0.032 (3.2%) |
| **P(B\|D)** | 0.018/0.032 | **56.25%** |

*Interpretation: Supplier B gives only 30% of parts but 56.25% of the defects found — disproportionate, supports investigation (not automatic blame).*

## C5. Bayes vs Conditional Probability

| Conditional Probability | Bayes Theorem |
|---|---|
| Calculates probability under a known condition | Reverses/updates a conditional probability |
| E.g., P(Returned\|Late) | E.g., P(Late\|Returned) |
| Restricts denominator | Combines prior + evidence |
| Foundation concept | Builds on Conditional Probability |

## C6. Excel Reference (Part C)
```
P(Flagged) = (Prior×Likelihood) + ((1-Prior)×FalsePositiveRate)
P(Fraud|Flagged) = (Prior×Likelihood) / P(Flagged)
```

---

# PART D — RANDOM VARIABLES, PMF, EXPECTED VALUE & VARIANCE (Session 17)

## D1. Random Variable — Definition
A random variable assigns a **numerical value** to the outcome of an uncertain process. Denoted by capital letters (X, Y, Z).

| Type | Definition | Examples |
|---|---|---|
| **Discrete** | Finite/countable values (individually identifiable) | # complaints, # defects, # customers |
| **Continuous** | Any value within an interval (measured) | Waiting time, weight |

| Basis | Discrete | Continuous |
|---|---|---|
| Obtained by | Counting | Measurement |
| Possible values | Separate/countable | Any value in interval |
| Probability tool | PMF | PDF |
| Probability considered for | Individual values | Intervals |

## D2. Probability Mass Function (PMF)

Gives P(X=x) for each possible discrete value.

**Two required properties of a valid PMF:**
$$0 \le P(X=x) \le 1 \qquad \sum P(X=x) = 1$$

### 🧮 Worked Example (used throughout D) — Complaints per Hour

| X | Frequency | P(X=x) |
|---|---|---|
| 0 | 5 | 0.10 |
| 1 | 10 | 0.20 |
| 2 | 15 | 0.30 |
| 3 | 10 | 0.20 |
| 4 | 7 | 0.14 |
| 5 | 3 | 0.06 |
| **Total** | 50 | **1.00** |

Empirical Probability: P(X=x) = Frequency of x ÷ Total Observations. E.g. P(X=2) = 15/50 = **0.30**

**Reading off probabilities:**
- P(X≤2) = 0.10+0.20+0.30 = **0.60**
- P(X≥3) = 0.20+0.14+0.06 = **0.40**
- P(1≤X≤3) = 0.20+0.30+0.20 = **0.70**

## D3. Expected Value E(X)

The **probability-weighted average** — the "centre" of the distribution.

$$E(X) = \mu = \sum x \cdot P(X=x)$$

| X | P(X=x) | x·P(X=x) |
|---|---|---|
| 0 | .10 | .00 |
| 1 | .20 | .20 |
| 2 | .30 | .60 |
| 3 | .20 | .60 |
| 4 | .14 | .56 |
| 5 | .06 | .30 |
| **Total** | 1.00 | **2.26** |

**E(X) = 2.26 complaints/hour** — a long-run average; individual hours won't show exactly 2.26.

**Cross-check with raw data:** (0×5+1×10+2×15+3×10+4×7+5×3)/50 = 113/50 = 2.26 ✓
Excel: `=AVERAGE(range)` or `=SUMPRODUCT(X_range, Prob_range)`

## D4. Variance & Standard Deviation

Variance measures spread/uncertainty around E(X). *(Same E(X) can hide very different variability — E(X) alone is NOT enough!)*

**Method 1 (definitional):**
$$Var(X) = \sum (x-\mu)^2 P(X=x)$$

**Method 2 (shortcut, easier for MCQs):**
$$Var(X) = E(X^2) - [E(X)]^2$$

### 🧮 Worked Example (Method 2 — faster)
| X | P(X=x) | X² | X²P(X=x) |
|---|---|---|---|
| 0 | .10 | 0 | .00 |
| 1 | .20 | 1 | .20 |
| 2 | .30 | 4 | 1.20 |
| 3 | .20 | 9 | 1.80 |
| 4 | .14 | 16 | 2.24 |
| 5 | .06 | 25 | 1.50 |
| **Total** | | | **6.94** |

Var(X) = 6.94 − (2.26)² = 6.94 − 5.1076 = **1.8324 ≈ 1.83**

**Standard Deviation:**
$$SD(X) = \sqrt{Var(X)} = \sqrt{1.8324} \approx 1.35 \text{ complaints}$$

> ⚠️ Why square deviations? Without squaring, positive and negative deviations cancel out. Squaring makes all contributions non-negative and weights larger deviations more heavily.
>
> ⚠️ Don't automatically assume "majority of data lies within μ±SD" — that requires knowing the shape of the distribution (e.g., only true under Normal-like assumptions / Empirical Rule).

## D5. Properties of Expected Value (memorize — frequently tested)

| Property | Formula |
|---|---|
| Linear transform | E(aX+b) = aE(X) + b |
| Scaling only | E(aX) = aE(X) |
| Shift only | E(X+b) = E(X)+b |
| Sum of two RVs | E(X+Y) = E(X)+E(Y) — **independence NOT required** |

### 🧮 Worked Example
E(X)=2.9 complaints/hr, each complaint costs ₹200 → C = 200X
- E(C) = 200×2.9 = **₹580/hour**
- With fixed cost ₹1000: C=200X+1000 → E(C) = 200(2.9)+1000 = **₹1,580/hour**

E(X+Y): morning E=4.2, evening E=3.6 → E(X+Y) = **7.8 complaints**

## D6. Excel Reference (Part D)
```
=COUNTIF(range, value)              → frequency
=I2/COUNT(range)                    → empirical probability
=SUMPRODUCT(X_range, Prob_range)    → E(X)
=AVERAGE(range)                     → cross-check E(X) from raw data
```

---

# PART E — CONTINUOUS RANDOM VARIABLES, PDF & CDF (Sessions 18–19)

## E1. Discrete → Continuous Transition

Y = Average Waiting Time (minutes) — can take ANY value (5, 5.4, 5.43, 5.437...) → **continuous random variable**.

## E2. Probability Density Function (PDF), f(y)

$$f(y) \ge 0 \qquad \int f(y)\,dy = 1 \text{ (total area under curve = 1)}$$

> 🔑 **THE #1 continuous-probability concept for MCQs:** Probability = **AREA under the curve**, NOT the height of the curve at a point!

$$P(a \le Y \le b) = \text{Area under } f(y) \text{ between } a \text{ and } b$$

## E3. Probability at an Exact Point = ZERO

$$P(Y=y) = 0 \text{ for any continuous random variable}$$

*A single point has zero width → zero area → zero probability. This does NOT mean the value can't occur — it just contributes no "area."*

**Consequence — boundary points don't matter for continuous RVs:**
$$P(Y<5)=P(Y\le5) \qquad P(2<Y<5)=P(2\le Y\le5)$$

## E4. 🧮 Worked Example — Constant (Uniform) Density

Y ranges 0 to 10 minutes, constant density. Since total area = 1:
$$\text{Width} \times \text{Height} = 1 \Rightarrow 10 \times h = 1 \Rightarrow h = 0.10$$

| Question | Calculation | Answer |
|---|---|---|
| P(2≤Y≤5) | (5−2)×0.10 | **0.30** |
| P(Y>7) | (10−7)×0.10 | **0.30** |
| P(Y≤8) | 8×0.10 | **0.80** |
| P(Y>8) | 1−0.80 | **0.20** |
| P(Y=5) | — | **0** |

## E5. CDF (Cumulative Distribution Function)

$$F(y) = P(Y \le y) \qquad P(a<Y\le b) = F(b)-F(a)$$
$$F(y) = \int_{-\infty}^{y} f(t)\,dt$$

## E6. Empirical Approach with Real Data
Before formal PDFs, group observations into intervals for empirical probability:
```
Empirical Probability = Interval Frequency / Total Observations
COUNTIF(range,"<3")                          → Y<3
COUNTIFS(range,">=3",range,"<5")             → 3≤Y<5
```
A **histogram** with more/finer bins → smooths into the theoretical density curve (PDF).

## E7. Quick Comparison Table

| | PMF (Discrete) | PDF (Continuous) |
|---|---|---|
| Gives | P(X=x) directly | Density — area gives probability |
| At exact point | Can be >0 | Always P(Y=y)=0 |
| Sum/Integral | Σ P(X=x) = 1 | ∫f(y)dy = 1 |
| Probability of range | Sum of P(X=x) | Area under curve |

---

# PART F — JOINT, MARGINAL & CONDITIONAL DISTRIBUTIONS (Session 20)

## F1. From One Variable to Two: Random Vector

X = Number of Complaints, Y = Number of Returns → observation (X,Y) = (2,1) → this pair is a **Random Vector**.

```
Two Random Variables → Random Vector (X,Y) → Joint Distribution P(X=x,Y=y)
                                                    ↙                ↘
                              Marginal Distribution              Conditional Distribution
                                 P(X=x) or P(Y=y)                    P(X=x|Y=y)
                                                                          ↓
                                                                Conditional Expected Value
                                                                     E(X|Y=y)
```

## F2. 🧮 Master Worked Example — Joint Distribution Table

Raw joint frequency table (50 hours), X=complaints (0-3), Y=returns (0-2):

| X \ Y | Y=0 | Y=1 | Y=2 | Total |
|---|---|---|---|---|
| X=0 | 8 | 2 | 0 | 10 |
| X=1 | 6 | 7 | 2 | 15 |
| X=2 | 3 | 7 | 5 | 15 |
| X=3 | 1 | 4 | 5 | 10 |
| **Total** | 18 | 20 | 12 | 50 |

**Converted to Joint Probability Distribution** (divide every cell by 50):

| X \ Y | Y=0 | Y=1 | Y=2 | **P(X=x)** |
|---|---|---|---|---|
| X=0 | 0.16 | 0.04 | 0.00 | **0.20** |
| X=1 | 0.12 | 0.14 | 0.04 | **0.30** |
| X=2 | 0.06 | 0.14 | 0.10 | **0.30** |
| X=3 | 0.02 | 0.08 | 0.10 | **0.20** |
| **P(Y=y)** | **0.36** | **0.40** | **0.24** | **1.00** |

> 🔑 Every internal cell = a **joint probability**. The row/column totals (margins) = **marginal probabilities**.

## F3. Joint / Marginal / Conditional — Definitions & Formulas

| Type | Question | Formula | Example (from table) |
|---|---|---|---|
| **Joint** | P(X AND Y together)? | Direct table cell | P(X=2,Y=1) = 0.14 |
| **Marginal** | P(one variable alone)? | Sum across the other variable's row/column | P(X=2) = 0.06+0.14+0.10 = 0.30 |
| **Conditional** | P(X given Y)? | P(X=x,Y=y)/P(Y=y) | P(X=2\|Y=1) = 0.14/0.40 = 0.35 |

Using **raw frequencies** directly is often easier:
$$P(X=2\mid Y=1) = \frac{7}{20} = 0.35$$
*(7 = joint frequency cell; 20 = column total for Y=1)*

> 🔑 **Golden Rule (same as Part B):** the condition after "|" determines the denominator.

## F4. Marginal Distributions (separately)

| X | P(X=x) |
|---|---|
| 0 | 0.20 |
| 1 | 0.30 |
| 2 | 0.30 |
| 3 | 0.20 |

| Y | P(Y=y) |
|---|---|
| 0 | 0.36 |
| 1 | 0.40 |
| 2 | 0.24 |

## F5. Conditional Distribution of X given Y=1

| X | Frequency (Y=1) | P(X=x\|Y=1) |
|---|---|---|
| 0 | 2 | 0.10 |
| 1 | 7 | 0.35 |
| 2 | 7 | 0.35 |
| 3 | 4 | 0.20 |
| **Total** | 20 | **1.00** |

## F6. Direction Matters! (same trap as Part B, now for joint tables)

$$P(X=2\mid Y=1) = \frac{7}{20} = 0.35 \quad \ne \quad P(Y=1\mid X=2) = \frac{7}{15} \approx 0.467$$

*Same numerator (joint frequency = 7), different denominators (column total vs row total).*

## F7. Marginal vs Conditional Comparison (reveals dependence)

| X | Marginal P(X=x) | Conditional P(X=x\|Y=1) |
|---|---|---|
| 0 | 0.20 | 0.10 |
| 1 | 0.30 | 0.35 |
| 2 | 0.30 | 0.35 |
| 3 | 0.20 | 0.20 |

The distribution of X **changes** once Y=1 is known → suggests X and Y are related (not independent).

## F8. Conditional Expected Value

$$E(X\mid Y=y) = \sum_x x\cdot P(X=x\mid Y=y)$$

| X | P(X=x\|Y=1) | x·P(X=x\|Y=1) |
|---|---|---|
| 0 | .10 | .00 |
| 1 | .35 | .35 |
| 2 | .35 | .70 |
| 3 | .20 | .60 |
| **Total** | 1.00 | **1.65** |

**E(X) = 1.50 (overall)** vs **E(X\|Y=1) = 1.65** — expected complaints are higher among hours with exactly 1 return.

## F9. Independence of Random Variables

$$X \perp Y \iff P(X=x\mid Y=y) = P(X=x) \iff P(X=x,Y=y) = P(X=x)P(Y=y)$$

In the example: P(X=2)=0.30 vs P(X=2\|Y=1)=0.35 → **not equal → not independent** (empirically). *(But remember: an empirical difference in a sample is not automatically formal statistical proof of dependence — that requires inference, e.g., Chi-Square test.)*

## F10. Excel Reference (Part F)
```
=COUNTIFS($B$2:$B$51,$E3,$C$2:$C$51,F$2)     → joint frequency cell
Rows=X (Complaints), Columns=Y (Returns), Values=Count   → PivotTable shortcut
```

---

# PART G — MASTER FORMULA SHEET (all sessions)

| # | Concept | Formula |
|---|---|---|
| 1 | Probability scale | 0 ≤ P(A) ≤ 1 |
| 2 | Classical probability | Favourable ÷ Total possible |
| 3 | Empirical probability | Frequency of A ÷ Total observations |
| 4 | Complement | P(Aᶜ) = 1 − P(A) |
| 5 | Mutually exclusive | P(A∩B) = 0 |
| 6 | Addition Rule (general) | P(A∪B) = P(A)+P(B)−P(A∩B) |
| 7 | Addition Rule (mutually exclusive) | P(A∪B) = P(A)+P(B) |
| 8 | Conditional probability | P(B\|A) = P(A∩B)/P(A) |
| 9 | Multiplication Rule | P(A∩B) = P(A)P(B\|A) = P(B)P(A\|B) |
| 10 | Independence test | P(B\|A)=P(B) ⟺ P(A∩B)=P(A)P(B) |
| 11 | Bayes Theorem | P(A\|B) = P(B\|A)P(A) / P(B) |
| 12 | Total Probability | P(B) = P(B\|A)P(A)+P(B\|Aᶜ)P(Aᶜ) |
| 13 | PMF requirement | ΣP(X=x) = 1 |
| 14 | Expected Value | E(X) = Σx·P(X=x) |
| 15 | Variance (def) | Var(X) = Σ(x−μ)²P(X=x) |
| 16 | Variance (shortcut) | Var(X) = E(X²) − [E(X)]² |
| 17 | Standard Deviation | SD(X) = √Var(X) |
| 18 | Linear transform of E(X) | E(aX+b) = aE(X)+b |
| 19 | Sum of expectations | E(X+Y) = E(X)+E(Y) |
| 20 | Continuous point probability | P(Y=y) = 0 |
| 21 | CDF | F(y) = P(Y≤y) |
| 22 | Interval probability (CDF) | P(a<Y≤b) = F(b)−F(a) |
| 23 | Joint probability | P(X=x, Y=y) |
| 24 | Conditional (joint form) | P(X=x\|Y=y) = P(X=x,Y=y)/P(Y=y) |
| 25 | Conditional Expected Value | E(X\|Y=y) = Σx·P(X=x\|Y=y) |
| 26 | Independence (random variables) | P(X=x\|Y=y) = P(X=x) |

---

# PART H — FAQ BANK (high-yield for MCQs)

**Q1. Does a 90% detection rate mean 90% of flagged items are truly positive?**
No — this confuses P(Flagged\|Fraud) with P(Fraud\|Flagged). These are reverse conditionals; only Bayes Theorem correctly converts one into the other, and the result depends heavily on the prior (base rate).

**Q2. If two events have positive probability and are mutually exclusive, can they be independent?**
No. If A occurs, B becomes impossible (P(B|A)=0), so P(B|A) ≠ P(B) (assuming P(B)>0) — this is the definition of dependence.

**Q3. Why is P(Y=y)=0 for continuous random variables, and does that mean y can never happen?**
It's because a single point has zero width, so it contributes zero area under the density curve. It does NOT mean the value is impossible — just that an exact point carries no "probability mass" in a continuous setting.

**Q4. Why doesn't P(A∪B) just equal P(A)+P(B)?**
Because outcomes in the overlap A∩B would be counted twice. Subtract P(A∩B) once (Addition Rule) — unless A and B are mutually exclusive, in which case P(A∩B)=0 and simple addition works.

**Q5. What's the difference between "given" (conditional) and "AND" (joint)?**
"AND"/joint uses the FULL sample as denominator: P(A∩B) = joint frequency / total. "Given"/conditional RESTRICTS the denominator to only the conditioning group: P(B\|A) = joint frequency / frequency of A.

**Q6. Why can Expected Value be a non-integer (e.g., 2.26 complaints) even though complaints must be whole numbers?**
E(X) is a long-run theoretical average across many repetitions, not a value that must be observable in any single trial.

**Q7. Is Expected Value alone sufficient to describe a random variable?**
No — two distributions can share the same E(X) but differ greatly in spread. Variance/SD is needed to describe variability around the center.

**Q8. Does E(X+Y) = E(X)+E(Y) require X and Y to be independent?**
No! This property holds regardless of independence — it's one of the most commonly mis-tested facts.

**Q9. In a joint probability table, how do you get the marginal distribution?**
Sum across the row (for P(X=x)) or down the column (for P(Y=y)) — i.e., read the table's margins/totals.

**Q10. What's the quickest way to test independence of two random variables from a joint table?**
Compare P(X=x\|Y=y) to the marginal P(X=x) — if equal for all x,y, independent; if not, dependent. Or compare P(X=x,Y=y) to P(X=x)×P(Y=y).

**Q11. Why did the fraud detection posterior (26.87%) come out so much lower than the 90% detection rate?**
Because fraud is rare (only 2% prior). Even a small false-positive rate (5%) applied to the huge genuine population (98%) generates more false alarms in absolute numbers than true detections — the base rate dominates.

**Q12. What's the difference between classical and empirical probability, and which is used in business analytics?**
Classical assumes theoretically equally-likely outcomes (dice, coins). Empirical is estimated from observed historical frequencies — this is what's overwhelmingly used in business analytics (transactions, defects, complaints, etc.).

**Q13. When boundaries are included/excluded in a continuous probability question (e.g., P(Y<5) vs P(Y≤5)), does it matter?**
No — because P(Y=exact value)=0 for continuous variables, so including or excluding a single boundary point never changes the probability.

**Q14. What does "height of a PDF curve" represent — is it probability?**
No! The height f(y) is a *density*, not a probability. Only the **area** under the curve over an interval equals probability.

**Q15. How is Variance Method 2 [E(X²)−[E(X)]²] different from Method 1, and are they always equal?**
They are algebraically identical (Method 2 is the derived shortcut) — always give the same answer, but Method 2 is typically faster to compute in MCQs since you don't need (x−μ) each time.

---

# PART I — MCQ SELF-TEST (with answers)

**1.** A fair claim like "two outcomes → 50% each" is:
a) Always true b) True only for classical/equally-likely settings c) True only empirically d) Never true
**Answer: b** — equal-outcome-count does NOT imply equal probability unless outcomes are truly equally likely (classical probability); business data (empirical) routinely violates this.

**2.** If P(A)=0.4, P(B)=0.5, P(A∩B)=0.2, then P(A∪B) = ?
**Answer:** 0.4+0.5−0.2 = **0.7**

**3.** If A and B are mutually exclusive with P(A)=0.3, P(B)=0.2, they must be:
a) Independent b) Dependent c) Cannot tell d) Both a and b
**Answer: b (Dependent)** — mutually exclusive events with positive probability are always dependent.

**4.** P(Late)=0.375, P(Returned|Late)=0.5333. Find P(Late ∩ Returned).
**Answer:** 0.375 × 0.5333 ≈ **0.20**

**5.** For a discrete RV with P(X=0)=0.1, P(X=1)=0.3, P(X=2)=0.4, P(X=3)=0.2 — is this a valid PMF?
**Answer: Yes** — all values between 0 and 1, and 0.1+0.3+0.4+0.2 = 1.0 ✓

**6.** Using the above PMF, E(X) = ?
**Answer:** 0(.1)+1(.3)+2(.4)+3(.2) = 0+0.3+0.8+0.6 = **1.7**

**7.** For a continuous RV uniform over [0,20], find the density height.
**Answer:** 1/20 = **0.05**

**8.** P(Y=10) for a continuous RV is:
**Answer: 0** (always, for any specific point in a continuous distribution)

**9.** In a joint table, P(X=1,Y=2)=0.08 and P(Y=2)=0.24. Find P(X=1|Y=2).
**Answer:** 0.08/0.24 = **0.333**

**10.** True or False: E(X+Y) requires X, Y independent.
**Answer: False**

**11.** A test detects disease with 95% sensitivity; disease prevalence is 1%. A positive result does NOT automatically mean 95% chance of disease because:
**Answer:** The prior/base rate (1%) is very low, so false positives from the large healthy population dominate — must apply Bayes Theorem, not just read off sensitivity.

**12.** Var(X) computed two ways (definitional formula and E(X²)−[E(X)]²) should:
**Answer:** Always produce the **same numeric result** — they are algebraically equivalent.

---

# PART J — COMMON MISTAKES CHEAT-SHEET (quick pre-quiz scan)

| ❌ Mistake | ✅ Correct Approach |
|---|---|
| Assuming 2 outcomes = 50/50 | Check if outcomes are truly equally likely; else use empirical data |
| Confusing X (variable) with X=x (event) | X is the random variable; X=x is one specific event/value |
| Adding P(A)+P(B) without checking overlap | Use Addition Rule: subtract P(A∩B) unless mutually exclusive |
| Treating "mutually exclusive" = "independent" | They are different; mutually exclusive (with P>0) ⇒ dependent |
| Using total sample as denominator in conditional prob. | Denominator = the group named after "\|" |
| Assuming P(A\|B) = P(B\|A) | Generally false — same numerator, different denominator |
| Reading PDF height as probability | Probability = AREA under curve, not height |
| Treating continuous exact-point probability as nonzero | P(Y=y) = 0 always, for continuous RVs |
| Ignoring the prior in Bayes problems | Always factor in base rate — strong likelihood ≠ high posterior when rare |
| Reporting E(X) without variability | Always pair E(X) with Var(X)/SD(X) |
| Treating empirical differences as statistical proof of dependence | Empirical gaps only *suggest* association; formal tests (e.g., Chi-Square) are needed to confirm |
| Assuming most data falls within μ±SD automatically | Requires knowledge of the distribution's shape (not automatic) |

---

# PART K — QUICK-GLANCE DECISION TREE

```
See "GIVEN"?                     → Conditional Probability (change denominator to that group)
See "AND"?                       → Joint / Intersection: P(A∩B)=P(A)P(B|A)
See "OR"?                        → Union: P(A∪B)=P(A)+P(B)-P(A∩B)
Need to check independence?      → Compare P(B|A) to P(B), or P(A∩B) to P(A)P(B)
Question reverses the condition? → Bayes Theorem (P(Evidence|Condition) → P(Condition|Evidence))
Asked for "average"/"center"?    → Expected Value E(X)=Σx·P(X=x)
Asked for "spread"/"risk"?       → Variance / Standard Deviation
Continuous variable + interval?  → PDF → Area under curve
Continuous variable + exact pt?  → Probability = 0
Two variables together?          → Joint/Marginal/Conditional distribution table
```

**Good luck on the quiz! 🎯**
