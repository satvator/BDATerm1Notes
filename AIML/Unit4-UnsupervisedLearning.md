# Unsupervised Learning — End-Term Notes
### PCA · K-Means · Hierarchical Clustering · Association Rules (Apriori)

**How to use this doc:** each topic goes intuition → formula/steps → fully-solved numeric examples, including **real past-exam cases** (End-Term, Dec 2024) reworked step-by-step. Numbers you can reuse directly on similar exam questions are marked **[EXAM PATTERN]**.

---

## 0. Quick Map

| Technique | What it does | Business example |
|---|---|---|
| PCA | Reduce many correlated variables → few uncorrelated components | Summarize survey items into 2–3 dimensions |
| K-Means | Partition observations into *k* compact groups | Segment customers into k pre-chosen tiers |
| Hierarchical | Build a nested tree of clusters, cut at any level | Explore supplier/store similarity without fixing k upfront |
| Association Rules (Apriori) | Find items that co-occur frequently | "Frequently bought together" bundles, cross-sell |

---

## 1. Principal Component Analysis (PCA)

### 1.1 Intuition
Many correlated variables → few uncorrelated **components**, each a weighted sum of the originals, chosen to capture maximum leftover variance.

### 1.2 Vocabulary

| Term | Meaning |
|---|---|
| Principal component (PC) | New summary axis = weighted combination of original (standardized) variables |
| Loading | Weight of an original variable in a PC. Large \|loading\| → that variable defines the component |
| Score | An observation's coordinate on a PC (its value after projection) |
| Variance explained | Share of total variance captured by a PC |
| Cumulative variance | Running total of variance explained as you add PCs |
| Eigenvalue / eigenvector | Eigenvector = direction of a PC; eigenvalue = variance captured along it |

**Sign is arbitrary** — never interpret "positive = good." Interpret *relative magnitude* and *which variables share vs. oppose sign*.

### 1.3 Algorithm (5 steps)

1. **Standardize**: `Z_j = (X_j − mean_j) / sd_j` (needed whenever variables are on different scales)
2. **Find directions**: eigen-decompose the correlation/covariance matrix of Z → eigenvectors (directions) + eigenvalues (variance captured)
3. **Rank**: sort components by eigenvalue, largest first (PC1 ≥ PC2 ≥ …)
4. **Select k**: use cumulative variance explained + scree plot elbow (commonly retain enough PCs for ~80–90%, or whatever cumulative-variance threshold the question gives — e.g. ≥95%)
5. **Transform**: `T = Z · W` where W = matrix of chosen eigenvectors → T is the new lower-dimensional data

### 1.4 How many components to keep

| Method | Rule |
|---|---|
| Cumulative variance | Keep smallest k such that cumulative % ≥ business threshold (typ. 80–90%, sometimes 95%) |
| Scree plot | Keep components before the "elbow" (where the curve flattens) |
| Eigenvalue > 1 (Kaiser rule) | Sometimes used with correlation-matrix PCA |

### 1.5 Naming a component from loadings — worked drill

Real slide example, **USArrests** (Murder, Assault, UrbanPop, Rape):

| Variable | PC1 loading | PC2 loading |
|---|---|---|
| Murder | −0.536 | −0.418 |
| Assault | −0.583 | −0.188 |
| UrbanPop | **−0.278** | **0.873** |
| Rape | −0.543 | 0.167 |

- **PC1**: Murder, Assault, Rape all large and *same sign* (~−0.53 to −0.58); UrbanPop is much smaller (−0.28) → PC1 ≈ **"overall violent-crime" dimension**. A state with a very negative PC1 score = high crime overall, largely regardless of urbanization.
- **PC2**: UrbanPop dominates (0.873) → PC2 ≈ **"urbanization" dimension**.

**[EXAM PATTERN]** naming rule: find the variable(s) with the *largest absolute loading* on a PC; check whether other variables reinforce (same sign, similar size) or oppose (opposite sign) it; name the PC after that shared theme.

*Illustrative-only drill* (constructed numbers, practice naming a business dimension — not from a real fitted model):

| Variable | PC1 | PC2 |
|---|---|---|
| Website ease of use | 0.40 | 0.10 |
| Delivery experience | 0.38 | 0.05 |
| Price perception | −0.12 | 0.75 |
| Brand trust | 0.44 | −0.30 |
| Product quality | 0.46 | −0.35 |
| Repeat purchase intention | 0.48 | 0.15 |

- PC1: nearly all loadings positive & similar size → **"Overall experience / loyalty"** dimension.
- PC2: dominated by Price perception (+0.75) opposing Brand trust/Product quality (negative) → **"price-sensitivity vs. quality-trust"** dimension. A real exam question style (End-Term, Case 4) frames exactly this pattern: large positive loadings on "defect rate"/"rework time" plus a large negative loading on "first-pass yield" on PC1 → that PC is read as a **"quality-risk"** dimension. The rule is the same: same-signed variables reinforce each other; you name the PC after what they jointly represent.

### 1.6 PCA inside Cross-Validation — the leakage trap

| ❌ Wrong (leaks data) | ✅ Correct |
|---|---|
| Standardize the **full** dataset once | Standardize using **train-fold statistics only** |
| Fit PCA (get loadings) on the **full** dataset | Fit PCA on the **training fold only** |
| *Then* split into CV folds / train-test | Apply the **train-fold's** mean/sd and loadings to transform the test fold (never re-fit on test) |

**Why it matters:** if PCA "sees" the test fold before it's held out, the components already encode test-set structure → CV performance estimate is optimistically biased. Rule of thumb: *any quantity estimated from data (mean, sd, eigenvectors) is estimated on train only, then applied to test.*
**[EXAM PATTERN]** — if a question says "compute PCA once on the full dataset and reuse in each fold" vs. "compute PCA separately inside each training fold and apply the resulting transform to its test fold," the second is always correct.

### 1.7 Worked numeric example (real R output, 5 observations × 3 features)

`summary(pca_result)`:

| | PC1 | PC2 | PC3 |
|---|---|---|---|
| Std deviation | 1.4011 | 0.9361 | 0.4009 |
| Proportion of Variance | 0.6544 | 0.2921 | 0.0536 |
| **Cumulative Proportion** | **0.6544** | **0.9465** | 1.0000 |

Scores (`pca_result$x`):

| Obs | PC1 | PC2 | PC3 |
|---|---|---|---|
| 1 | −1.2787 | 0.1238 | 0.5238 |
| 2 | 0.9768 | −0.7505 | −0.3145 |
| 3 | −1.7179 | −0.3828 | −0.3692 |
| 4 | 0.6411 | 1.5702 | −0.1654 |
| 5 | 1.3787 | −0.5606 | 0.3254 |

**(a) Cumulative variance from first k PCs** — just add the "Proportion of Variance" row:
- k=1: 0.6544 (65.44%)
- k=2: 0.6544 + 0.2921 = **0.9465 (94.65%)**
- k=3: 1.0000 (100%)

**(b) Most extreme observation on PC1** — compare \|PC1\|: 1.2787, 0.9768, **1.7179**, 0.6411, 1.3787 → **Observation 3** is most extreme (largest magnitude, most negative).

**(c) Euclidean distance using only PC1 & PC2** (Obs 1 vs Obs 3):
```
d = sqrt( (PC1_1 − PC1_3)^2 + (PC2_1 − PC2_3)^2 )
ΔPC1 = −1.2787 − (−1.7179) = 0.4392
ΔPC2 =  0.1238 − (−0.3828) = 0.5066
d = sqrt(0.4392² + 0.5066²) = sqrt(0.1929 + 0.2566) = sqrt(0.4495) ≈ 0.670
```

### 1.8 PCA — what it does / doesn't do

| Does | Doesn't |
|---|---|
| Reduce many related variables to fewer components | Select variables based on a business outcome |
| Help visualize high-dimensional data | Prove causality |
| Remove redundancy among correlated variables | Guarantee better prediction |
| Feed clustering/predictive models with cleaner inputs | Automatically name/interpret components |

### 1.9 Real exam case — outpatient clinics (End-Term, Case 4)

A hospital network runs PCA on **4 standardized variables**: `Avg_Wait_Time`, `Patients_Per_Day`, `Staff_Per_Shift`, `30Day_Readmission_Rate`, across 5 clinics (A–E).

`summary(pca_result)`:

| | PC1 | PC2 | PC3 | PC4 |
|---|---|---|---|---|
| Std deviation | 1.9309 | 0.5163 | 0.0708 | 0.0127 |
| Proportion of Variance | 0.9321 | 0.0667 | 0.0013 | 0.0000 |
| **Cumulative Proportion** | **0.9321** | **0.9987** | 0.9999 | 1.0000 |

Loadings:

| Variable | PC1 | PC2 |
|---|---|---|
| Avg_Wait_Time | 0.5022 | −0.4643 |
| Patients_Per_Day | 0.4975 | 0.5371 |
| Staff_Per_Shift | 0.5029 | 0.4599 |
| Readmission_30D | 0.4974 | −0.5333 |

Scores:

| Clinic | PC1 | PC2 |
|---|---|---|
| A | −0.7771 | 0.8068 |
| B | −0.7605 | −0.6185 |
| C | 1.4228 | −0.1435 |
| D | −2.3613 | −0.0975 |
| E | 2.4761 | 0.0527 |

**Q1 — components needed for ≥95% variance?**
Cumulative: PC1 alone = 93.21% (< 95%); PC1+PC2 = 99.87% (≥ 95%). → **need 2 components.**

**Q2 — % of variance NOT explained by PC1+PC2?**
`100% − 99.87% = 0.13%` (rounding the raw cumulative 0.99871 → not-explained = 1 − 0.99871 = 0.00129 → **0.13%**).

**Q3 — most extreme high-intensity vs. low-intensity clinic on PC1?**
All four PC1 loadings are positive and similar in size → high PC1 = clinic that is high on *everything* (busier, more staff, longer waits, higher readmission) = **"high-intensity"** dimension.
PC1 scores: A=−0.78, B=−0.76, C=1.42, D=**−2.36**, E=**2.48**.
→ Most extreme **high-intensity: Clinic E** (PC1 = 2.48). Most extreme **low-intensity: Clinic D** (PC1 = −2.36).

**Q4 — Euclidean distance between C and E using (PC1, PC2)?**
```
C = (1.4228, −0.1435), E = (2.4761, 0.0527)
ΔPC1 = 1.4228 − 2.4761 = −1.0533
ΔPC2 = −0.1435 − 0.0527 = −0.1962
d = sqrt((−1.0533)² + (−0.1962)²) = sqrt(1.1094 + 0.0385) = sqrt(1.1479) ≈ 1.07
```
Interpretation: for reference, the two most extreme clinics (D vs E) are about 4.84 apart on PC1 alone — so a distance of **≈1.07** between C and E is comparatively small: they are **moderately similar**, both sitting on the higher-intensity side of PC1, even though they aren't a close match.

**Q5 — which PC represents "high-throughput, well-staffed, efficient / better-outcomes"?**
Look at PC2's loading *signs*: Patients_Per_Day (+0.537) and Staff_Per_Shift (+0.460) are positive; Avg_Wait_Time (−0.464) and Readmission_30D (−0.533) are negative. So a clinic with **high PC2** = high patient throughput & staffing **and** low wait time & low readmission — exactly "efficient / better outcomes." → **PC2.**

---

## 2. K-Means Clustering

### 2.1 Intuition
Partition n points into *k* groups by minimizing **within-cluster sum of squares (WCSS)**:
```
WCSS = Σ over clusters Σ over points in cluster (point − its centroid)^2
```
A **centroid** is just the *mean* of the points currently assigned to that cluster — it need not be an actual observed data point.

### 2.2 Algorithm

1. Choose k; pick k initial centroids (random or k-means++)
2. **Assign**: put each point in the cluster of its nearest centroid (usually Euclidean distance)
3. **Update**: recompute each centroid = mean of points now assigned to it
4. **Repeat** steps 2–3 until assignments stop changing (or max iterations)

### 2.3 Initial centroids & restarts
Different starting centroids can converge to **different local optima** (different final clusters/WCSS). Fix: run k-means many times with different random starts (`n_init` in sklearn) and keep the run with the **lowest WCSS**.

### 2.4 Silhouette score
For a point i: `a(i)` = avg. distance to other points in *its own* cluster; `b(i)` = avg. distance to points in the *nearest other* cluster.
```
s(i) = (b(i) − a(i)) / max(a(i), b(i))
```
Range **−1 to +1**; closer to +1 = well-clustered (tight and well-separated); near 0 = on a border; negative = likely mis-clustered.

### 2.5 Full manual walkthrough — 1D data, k = 2

Points: **2, 4, 6, 10, 12, 14**. Given initial centroids: **c1 = 2, c2 = 14**.

**Iteration 1 — Assign** (distance = \|point − centroid\|):

| Point | dist to c1 (2) | dist to c2 (14) | Assigned |
|---|---|---|---|
| 2 | 0 | 12 | c1 |
| 4 | 2 | 10 | c1 |
| 6 | 4 | 8 | c1 |
| 10 | 8 | 4 | c2 |
| 12 | 10 | 2 | c2 |
| 14 | 12 | 0 | c2 |

**Update centroids**: Cluster1 = {2,4,6} → mean = (2+4+6)/3 = **4**. Cluster2 = {10,12,14} → mean = (10+12+14)/3 = **12**.

**Iteration 2 — Assign with c1 = 4, c2 = 12**:

| Point | dist to c1 (4) | dist to c2 (12) | Assigned |
|---|---|---|---|
| 2 | 2 | 10 | c1 |
| 4 | 0 | 8 | c1 |
| 6 | 2 | 6 | c1 |
| 10 | 6 | 2 | c2 |
| 12 | 8 | 0 | c2 |
| 14 | 10 | 2 | c2 |

Same assignment as before → **clusters unchanged → converged.**
Final: **c1 = 4** → {2,4,6}; **c2 = 12** → {10,12,14}.

**Classify a new point x = 9**:
```
dist to c1 (4) = |9−4| = 5
dist to c2 (12) = |9−12| = 3   → smaller
```
→ **x = 9 is assigned to cluster 2.**

### 2.6 K-means vs. K-means++ vs. multiple restarts — quick note
- Plain k-means: random initial centroids → can converge to a bad local optimum.
- k-means++: smarter initialization that spreads out initial centroids → usually converges faster/better, still not guaranteed global optimum.
- **Multiple restarts** (`n_init` > 1): the standard fix in practice — run several times, keep the lowest-WCSS solution.

### 2.7 Real exam case — cell-tower hotspots (End-Term, Case 3)

Six towers' peak-hour traffic (TB/day): T1=8, T2=10, T3=12, T4=28, T5=30, T6=33. **k=2**, initial centroids: **Normal = 8, Hotspot = 28.**

**Iteration 1 — Assign:**

| Tower | dist to Normal(8) | dist to Hotspot(28) | Assigned |
|---|---|---|---|
| T1=8 | 0 | 20 | Normal |
| T2=10 | 2 | 18 | Normal |
| T3=12 | 4 | 16 | Normal |
| T4=28 | 20 | 0 | Hotspot |
| T5=30 | 22 | 2 | Hotspot |
| T6=33 | 25 | 5 | Hotspot |

**Hotspot cluster after 1st assignment: {T4, T5, T6} = {28, 30, 33}.**

**Update centroids:**
```
Normal  = (8+10+12)/3  = 30/3  = 10.00
Hotspot = (28+30+33)/3 = 91/3  = 30.33
```

**Iteration 2 — reassign with Normal=10.00, Hotspot=30.33:** every tower's nearest centroid is unchanged (T1–T3 still nearer 10.00; T4–T6 still nearer 30.33) → **converged.**

**Final centroids: Normal = 10.00, Hotspot = 30.33.**

**Classify a new tower at 20 TB/day:**
```
dist to Normal (10.00) = |20−10.00| = 10.00
dist to Hotspot (30.33) = |20−30.33| = 10.33
```
10.00 < 10.33 → **assigned to the Normal-load cluster** (a close call — just 0.33 apart, worth flagging to ops as borderline).

**Business framing question** — "which segmentation outcome is more actionable: (i) balanced cluster sizes, or (ii) isolating a small set of extreme towers?"
**Answer: (ii).** The business goal is to find true hotspots for *targeted* capacity upgrades/maintenance — forcing balanced-size clusters would dilute exactly the signal (a small set of genuinely overloaded towers) that operations needs to act on.

---

## 3. Hierarchical Clustering

### 3.1 Intuition
Agglomerative approach: start with every point as its own cluster, repeatedly merge the two *closest* clusters, record merge height, until one cluster remains. Result = **dendrogram** (tree); merge height = the inter-cluster distance at which that merge happened. **Cutting** the dendrogram at height h gives however many clusters exist at that height.

### 3.2 Linkage definitions

| Linkage | Inter-cluster distance = | Behavior |
|---|---|---|
| Single | **minimum** distance between any pair (one point per cluster) | Prone to **chaining**: a string of close points can drag distant points together early |
| Complete | **maximum** distance between any pair | Favors compact, similarly-sized clusters; resists chaining |
| Average | mean of all pairwise distances | Compromise between single and complete |

**Exam trap:** the *first* merge is always the same for every linkage (it's just the globally closest pair of individual points) — linkage choice only starts to matter from the **second merge onward**, once a cluster has more than one point.

### 3.3 Manual walkthrough (small example)

Four suppliers on one score: **A=1, B=2, C=6, D=13**.

Pairwise (Euclidean, 1D = absolute difference):

| | A | B | C | D |
|---|---|---|---|---|
| A | – | 1 | 5 | 12 |
| B | 1 | – | 4 | 11 |
| C | 5 | 4 | – | 7 |
| D | 12 | 11 | 7 | – |

**Single linkage:**
- Merge 1: smallest overall distance = A–B = **1** → {A,B}
- Merge 2: dist({AB},C) = min(AC,BC) = min(5,4) = 4; dist({AB},D) = min(12,11) = 11; dist(C,D) = 7 → smallest = **4** → merge {A,B}+C
- Merge 3: dist({ABC},D) = min(12,11,7) = **7** → merge with D
- Heights: **1 → 4 → 7**

**Complete linkage:**
- Merge 1: A–B = **1** (same as always)
- Merge 2: dist({AB},C) = max(5,4) = 5; dist({AB},D) = max(12,11) = 12; dist(C,D) = 7 → smallest = **5** → merge {A,B}+C
- Merge 3: dist({ABC},D) = max(12,11,7) = **12**
- Heights: **1 → 5 → 12**

**Average linkage** (for reference):
- Merge 2: dist({AB},C) = (5+4)/2 = **4.5**; dist({AB},D) = (12+11)/2 = 11.5 → merge {AB}+C at 4.5
- Merge 3: dist({ABC},D) = (12+11+7)/3 = **10**
- Heights: **1 → 4.5 → 10**

**Which merges first?** All three linkages merge **A & B first**, at height 1 — identical, per the exam trap above. Divergence appears at merge 2.

### 3.4 Cutting the dendrogram → cluster count

Cut all three trees at **height = 4**:

| Linkage | Merges completed by height 4 | Resulting clusters |
|---|---|---|
| Single | A-B (1), ABC (4) → 2 merges | **{A,B,C}, {D} → 2 clusters** |
| Complete | A-B (1) only → 1 merge | **{A,B}, {C}, {D} → 3 clusters** |
| Average | A-B (1) only → 1 merge | **{A,B}, {C}, {D} → 3 clusters** |

This is **chaining** in action: at the same cut height, single linkage has already pulled C into the {A,B} group (because it only needs *one* close pair, B–C=4), while complete/average require *all* pairwise distances small and so keep C separate longer.

**General rule**: clusters remaining = (number of original points) − (number of merges completed at or below the cut height).

### 3.5 Real exam case — BPO agent handling times (End-Term, Case 2)

Six agents' Average Handling Time (minutes): **120, 128, 140, 205, 215, 405** — call them A, B, C, D, E, F. Distance = absolute difference.

Key pairwise distances: AB=8, DE=10, BC=12 (via AC=20,BC=12), AC=20, CD=65, CE=75, AD=85, BD=77, AE=95, BE=87, CF=265, DF=200, EF=190, AF=285, BF=277.

**Single linkage merge sequence:**
| Step | Merge | Height |
|---|---|---|
| 1 | A(120) + B(128) | **8** |
| 2 | D(205) + E(215) | 10 |
| 3 | {A,B} + C(140) — since min(AC,BC)=min(20,12)=12 | 12 |
| 4 | {A,B,C} + {D,E} — since min over all cross-pairs, smallest is C–D=65 | 65 |
| 5 | {A,B,C,D,E} + F(405) — closest remaining is E–F=190 | 190 |

**Complete linkage merge sequence:**
| Step | Merge | Height |
|---|---|---|
| 1 | A(120) + B(128) | **8** (same first merge, as always) |
| 2 | D(205) + E(215) | 10 |
| 3 | {A,B} + C(140) — max(AC,BC)=max(20,12)=20 | 20 |
| 4 | {A,B,C} + {D,E} — max over all cross-pairs = max(A–D,A–E,B–D,B–E,C–D,C–E) = 95 | 95 |
| 5 | {A,B,C,D,E} + F(405) — max over all = A–F=285 | 285 |

**Q1 — under complete linkage, cutting at height 15, how many clusters?**
Merges completed at ≤15: only merge 1 (height 8) and merge 2 (height 10) — merge 3 is at height 20 (>15). So 2 merges done on 6 points → **6 − 2 = 4 clusters**: {A,B}, {C}, {D,E}, {F}.

**Q2 — final step of complete linkage: which two clusters merge?**
The last merge (height 285) joins **{A,B,C,D,E} = {120,128,140,205,215}** with **{F} = {405}**.

**Q3 — under single linkage, which two agents merge first?**
**A and B**, i.e. agents with AHT **120 and 128** (smallest pairwise distance, 8 minutes) — and this is true regardless of linkage (exam trap from §3.3).

**Q4 — for high within-cluster similarity, which linkage is preferred?**
**Complete linkage** — it only merges clusters when *every* pairwise distance is small, producing tight, mutually-similar groups (vs. single linkage's chaining risk).

**Q5 — at what height does the first merge occur (single linkage)?**
**Height = 8** (|128 − 120| = 8).

---

## 4. Association Rule Mining (Apriori)

### 4.1 Intuition
Finds items that frequently **co-occur** in transactions — "customers who buy X also tend to buy Y." Drives bundling, cross-sell, and store/menu layout decisions. Unlike PCA/clustering, this works on *transactions* (baskets of items), not numeric feature vectors.

### 4.2 Key metrics

| Metric | Formula | Reads as |
|---|---|---|
| Support(X) | (# transactions containing X) / (total transactions) | How popular X is on its own |
| Support(X→Y) | Support(X ∪ Y) | How often X and Y appear *together* |
| Confidence(X→Y) | Support(X∪Y) / Support(X) = P(Y \| X) | Reliability: given X, how often does Y also appear |
| Lift(X→Y) | Confidence(X→Y) / Support(Y) = P(Y\|X) / P(Y) | Strength of association vs. baseline chance: lift=1 → independent; >1 → positively associated; <1 → negatively associated |

**Note:** Lift is symmetric — `lift(X→Y) = lift(Y→X)` always, which is why lift is the right metric for claiming a *mutual*/two-way association, while confidence is direction-specific.

### 4.3 Apriori algorithm (brief)
1. Find all **frequent itemsets** meeting a minimum support threshold (prune anything rarer).
2. Generate candidate **rules** from those itemsets.
3. Keep rules meeting a minimum **confidence** threshold.
4. Rank/filter surviving rules by **lift** (or business uplift) to find the genuinely useful ones.

### 4.4 Real exam case — "Frequently Bought Together" (End-Term, Case 1)

A food-delivery platform ran Apriori (min support = 0.25, min confidence = 0.4) on 10 orders. Rules table:

| # | Rule | Support | Confidence | Lift |
|---|---|---|---|---|
| 1 | {} → {Salad} | 0.4 | 0.40 | 1.00 |
| 2 | {} → {Coke} | 0.4 | 0.40 | 1.00 |
| 3 | {} → {Burger} | 0.6 | 0.60 | 1.00 |
| 4 | {} → {Fries} | 0.6 | 0.60 | 1.00 |
| 5 | {Coke} → {Fries} | 0.3 | **0.75** | 1.25 |
| 6 | {Fries} → {Coke} | 0.3 | 0.50 | 1.25 |
| 7 | {Burger} → {Fries} | 0.4 | 0.667 | 1.111 |
| 8 | {Fries} → {Burger} | 0.4 | 0.667 | 1.111 |

(Rules 1–4 have an empty antecedent — they just restate an item's own baseline support/popularity, so their confidence always equals that item's support and their lift is always 1. Useful as the "baseline" row to compare against.)

**Q1 — highest-confidence rule with a non-empty antecedent (best single "attach" rule)?**
Compare rules 5–8: 0.75, 0.50, 0.667, 0.667 → highest is **Rule 5: {Coke} → {Fries}, confidence = 0.75.**

**Q2 — which rule(s) increase purchase likelihood by ≥20% above baseline (lift ≥ 1.20)?**
Rules 5 & 6 have lift = 1.25 (a 25% lift, ≥ 20% ✓). Rules 7 & 8 have lift = 1.111 (only 11.1% ✗).
→ **Rules 5 and 6** — the Coke↔Fries pair (both directions) meet the policy.

**Q3 — estimated P(Fries \| Coke)?**
That's exactly the confidence of Rule 5: **0.75.**

**Q4 — single metric proving Coke & Fries are *mutually* reinforcing?**
**Lift.** Both {Coke}→{Fries} and {Fries}→{Coke} show lift = 1.25 > 1 — and lift is symmetric by construction, so it directly captures "both directions improve over baseline" in one number (unlike confidence, which is direction-specific and differs, 0.75 vs 0.50).

**Q5 — rule maximizing Uplift = confidence − support(rhs)?**
```
Rule 5: {Coke}→{Fries}:  0.75  − support(Fries)=0.6  = 0.15
Rule 6: {Fries}→{Coke}:  0.50  − support(Coke)=0.4   = 0.10
Rule 7: {Burger}→{Fries}: 0.667 − support(Fries)=0.6  = 0.067
Rule 8: {Fries}→{Burger}: 0.667 − support(Burger)=0.6 = 0.067
```
Maximum = 0.15 → **Rule 5** ({Coke} → {Fries}).

---

## 5. K-Means vs. Hierarchical — comparison

| Aspect | K-Means | Hierarchical |
|---|---|---|
| Must pre-specify k? | **Yes** | No — choose after seeing the dendrogram, by cutting at any height |
| Deterministic? | No — depends on initial centroids (use multiple restarts) | Yes — same distance metric + linkage always gives the same tree |
| Sensitivity to outliers | High (mean is pulled by extreme points) | Depends on linkage: single very sensitive (chaining), complete/average more robust |
| Scalability | Good for large n | Poor — typically O(n²) or worse; struggles at scale |
| Cluster shape assumption | Roughly spherical, similar-sized clusters | More flexible depending on linkage |
| Key knob | k, initial centroids, restarts | Linkage method (single/complete/average/Ward), cut height |
| When to prefer | Large datasets, need speed, roughly know k | Small/medium data, want to explore multiple k's, want a visual nested structure |

*(Association rules sit outside this comparison — they operate on transactional/basket data, not on a feature-space distance, so "k vs. no-k" and outlier-sensitivity don't directly apply.)*

---

## 6. MCQ Self-Test

**Q1.** In `USArrests` PCA, PC1 explains 62.01% and PC1+PC2 explain 86.75% of variance. How many components are needed for ≥85% cumulative variance?
(a) 1 (b) 2 (c) 3 (d) 4
**Answer: (b).** Cumulative at k=1 is 62.01% (<85%); at k=2 it's 86.75% (≥85%) — stop at 2.

**Q2.** All four USArrests variables load negatively on PC1, but UrbanPop's loading (−0.28) is much smaller than Murder/Assault/Rape's (~−0.53 to −0.58). A state with a very negative PC1 score most likely means:
(a) very high urbanization only (b) very high overall violent crime, largely independent of urbanization (c) very low population (d) an average state (e) measurement error
**Answer: (b).** The three crime variables dominate PC1 with similar large loadings of the same sign; UrbanPop contributes comparatively little to PC1 (it dominates PC2 instead).

**Q3.** A student standardizes the whole dataset and fits PCA on it *before* running 5-fold CV to pick the number of components. What's the problem?
(a) PCA never works with CV (b) Nothing — this is standard practice (c) Data leakage: test-fold information influenced the PCA fit, biasing CV performance optimistically (d) PCA can only use 1 fold
**Answer: (c).** Mean/sd and loadings must be learned on the training fold only, then applied unchanged to the test fold.

**Q4.** Why does k-means use multiple random restarts (`n_init`)?
(a) To speed up convergence (b) To avoid getting stuck at a poor local optimum from unlucky initial centroids; keep the run with lowest WCSS (c) To automatically choose k (d) To compute silhouette score
**Answer: (b).**

**Q5.** After k-means converges, a cluster's centroid is (5, 12). Must this be an actual data point?
(a) Yes, always (b) No — it is the mean of the assigned points and may not match any real observation (c) Only if k=2 (d) Only for 1D data
**Answer: (b).**

**Q6.** Using the walkthrough centroids c1 = 4, c2 = 12, classify new point x = 5.
(a) Cluster 1 (b) Cluster 2 (c) Tie (d) Cannot say
**Answer: (a).** dist to c1 = \|5−4\|=1; dist to c2 = \|5−12\|=7 → nearer to c1.

**Q7.** A point has a(i) = 2 (avg. distance within own cluster) and b(i) = 6 (avg. distance to nearest other cluster). Compute its silhouette score and interpret.
**Answer:** `s = (b−a)/max(a,b) = (6−2)/6 = 0.667` — close to 1, so the point is well-clustered (tight within its cluster, well separated from the nearest neighboring cluster).

**Q8.** A single-linkage dendrogram has merges at heights 1, 4, and 7 for 4 objects. Cutting at height 5 gives how many clusters?
(a) 1 (b) 2 (c) 3 (d) 4
**Answer: (b).** Merges completed at ≤5: heights 1 and 4 (both ≤5) = 2 merges done → 4 − 2 = 2 clusters.

**Q9.** Which linkage is generally *most* prone to the chaining effect?
(a) Complete (b) Average (c) Single (d) Ward's
**Answer: (c).** Single linkage only needs one close pair between clusters to trigger a merge, so a string of nearby points can chain far-apart groups together.

**Q10.** *(exam-style)* A supply-chain manager applies hierarchical clustering with single linkage to group suppliers. The dendrogram shows a "chaining" effect where most suppliers end up in one long stretched cluster. Which change is most likely to reduce this chaining?
(a) Switch to complete linkage
(b) Switch to Manhattan distance with single linkage
(c) Increase the number of suppliers
(d) Normalize variables but keep single linkage
(e) None of the above
**Answer: (a).** Chaining is a structural property of the *single-linkage rule itself* (merge on the single nearest pair) — switching the distance metric (b) or normalizing scale (d) doesn't change that rule, and more data (c) worsens rather than helps. Complete linkage requires *all* pairwise distances to be small before merging, directly countering chaining.

**Q11.** *(End-Term, Case 8)* A marketing head wants customer groups discovered with **no labels available**, to design different campaign themes. Which ML task fits?
(a) Regression (b) Classification (c) Clustering (d) Association rules only
**Answer: (c).** No target/label, goal is to discover natural groupings → unsupervised **clustering**.

**Q12.** *(End-Term, Case 8)* A CTO wants to compress **20 behavioral variables into 2–3 dimensions** for visualization while retaining most of the information. Which task fits?
(a) K-means clustering (b) Logistic regression (c) PCA / dimensionality reduction (d) Association rule mining
**Answer: (c).** Compressing many correlated variables into a few summary dimensions for visualization is exactly what **PCA (dimensionality reduction)** is for — clustering groups *rows*, not variables.

---

### One-line recap
- **PCA** simplifies variables (columns); interpret via loadings, decide k via cumulative variance/scree, and never fit PCA on data outside the training fold.
- **K-means** partitions rows into k groups by nearest-centroid + mean-update, repeated to convergence; watch initialization sensitivity.
- **Hierarchical** builds a full merge tree; linkage choice (esp. single vs. complete) governs chaining; no need to fix k upfront — cut the tree instead.
- **Association rules (Apriori)** find co-occurring items in transactions; support = popularity, confidence = direction-specific reliability, lift = symmetric strength of association vs. chance.
