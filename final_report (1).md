# Predicting NYC Restaurant Health Inspection Grades

**Team:** Jessica Aimunmondion (POC), Joel Barnes, Mian Hamid (mian7h), Zeek Moore  
**Course:** Syracuse University — IST 707 Machine Learning  
**Date:** Spring 2026

## Interactive Dashboard

To view the live dashboard, open the link below:

[Restaurant Dashboard Link](https://htmlpreview.github.io/?https://github.com/SU-IST707-SP26/group-project-team-pandas/blob/main/dashboard_mockup.html)

Or download `dashboard_mockup.html` and open it locally in any browser.

---

## 1. Introduction

New York City's Department of Health and Mental Hygiene (DOHMH) inspects roughly 27,000 restaurants on a fixed annual schedule, regardless of each establishment's compliance history or known risk factors. This project asks: can a machine learning model use a restaurant's past inspection record to identify which establishments are most likely to receive a poor grade (B or C) before their next inspection?

A successful model would let DOHMH allocate its limited inspector resources toward the highest-risk establishments — catching food safety violations earlier, reducing the frequency of unnecessary inspections at consistently compliant restaurants, and potentially reducing the ~3,000 annual foodborne illness cases reported in New York City.

We also take seriously the ethical constraints raised by our instructor: any risk-based inspection system must avoid creating a self-fulfilling surveillance cycle that disproportionately burdens restaurants in lower-income or minority neighbourhoods.

---

## 2. Data

**Primary dataset:** DOHMH NYC Restaurant Inspection Results (NYC Open Data, updated daily).

- ~296,751 raw records; filtered to 271,481 records from 2022–2025.
- 27 columns including restaurant identifier (CAMIS), borough, cuisine type, inspection date, violation codes, critical flag, numeric score, and grade (A/B/C).
- Each row represents a single violation; multiple violations per inspection are common.

**Preprocessing pipeline (Notebooks 01–03):**

1. Parse and filter inspection dates to 2022–2025.
2. Aggregate violation-level rows into one row per inspection, computing violation counts by category (food temperature, food handling, personal hygiene, facility conditions, pest control, chemical toxin, trans fat, calorie posting, other).
3. Compute per-inspection features: total violations, critical violations, non-critical violations, critical ratio, unique violation categories.
4. Compute historical (leak-free) features using expanding-window lag operations: `historical_avg_score`, `historical_avg_critical`, `prior_inspection_count`.
5. Create temporal and trend features: `inspection_month`, `inspection_year`, `days_since_last_inspection`, `previous_score`, `score_change_from_previous`.
6. Create the binary target variable: `inspection_within_90_days` — whether a restaurant received another inspection within 90 days (1 = yes, indicative of a follow-up or elevated-risk pattern).

**Class distribution:** The target variable is imbalanced (~78% Class 0, ~22% Class 1), which guided our model selection.

---

## 3. Methods

### 3.1 Feature Set

| Feature | Description |
|---|---|
| `historical_avg_score` | Expanding mean of prior inspection scores |
| `historical_avg_critical` | Expanding mean of prior critical violation counts |
| `prior_inspection_count` | Number of past inspections for this restaurant |
| `critical_violations` | Count of critical violations in this inspection |
| `total_violations` | Total violation count |
| `inspection_month` | Calendar month (captures seasonal patterns) |
| `inspection_year` | Year |
| `days_since_last_inspection` | Gap since most recent prior inspection |
| `score_change_from_previous` | Score delta from previous inspection |

All missing values (first-inspection NaNs) were filled with 0.

### 3.2 Train/Test Split

Standard 80/20 stratified split (`random_state=42`), preserving class balance in both sets.

### 3.3 Models Trained

Three models were trained and compared:

| Model | Key Setting |
|---|---|
| Logistic Regression (baseline) | Default `class_weight=None` |
| Balanced Logistic Regression | `class_weight='balanced'` |
| Random Forest | `n_estimators=100`, `random_state=42` |
| Random Forest (tuned) | `n_estimators=100`, `max_depth=10` |

### 3.4 Evaluation Metrics

Because missing a high-risk restaurant has a higher societal cost than a false alarm, **recall for the B/C class** was treated as the primary metric. Precision, F1-score, accuracy, and ROC-AUC were tracked as secondary metrics.

---

## 4. Results

### 4.1 Model Comparison

| Model | Accuracy | Precision (B/C) | Recall (B/C) | F1 (B/C) |
|---|---|---|---|---|
| Logistic Regression | 0.80 | 0.58 | 0.24 | 0.34 |
| **Balanced LR** | **0.74** | **0.44** | **0.69** | **0.54** |
| Random Forest | 0.84 | 0.67 | 0.50 | 0.58 |
| Random Forest (tuned) | 0.85 | 0.73 | 0.47 | 0.57 |

The **Balanced Logistic Regression** achieves the highest recall for the high-risk class (69%), making it the best model for the stated public-health goal, even though its overall accuracy is lower. The Random Forest has better overall accuracy and precision but misses nearly half of the B/C-grade restaurants.

### 4.2 Confusion Matrix — Balanced Logistic Regression (Test Set, n = 10,361)

|  | Predicted: Grade A | Predicted: B/C |
|---|---|---|
| **True: Grade A** | 6,163 ✓ | 1,946 |
| **True: B/C** | 698 | 1,554 ✓ |

- **True Positives (1,554):** High-risk restaurants correctly flagged — would receive a prioritised inspection.
- **False Negatives (698):** High-risk restaurants missed — the main error the model seeks to minimise.
- **False Positives (1,946):** Grade-A restaurants unnecessarily flagged — the cost of high recall.
- **True Negatives (6,163):** Compliant restaurants correctly cleared.

The model catches **69% of all future B/C restaurants**, with a false positive rate of ~24% among Grade-A restaurants.

### 4.3 Feature Importance (Balanced LR Coefficients)

| Feature | Coefficient | Direction |
|---|---|---|
| Prior inspection count | +0.430 | More past inspections → higher risk |
| Critical violations | +0.364 | More critical violations → higher risk |
| Total violations | +0.165 | More total violations → higher risk |
| Historical avg score | +0.032 | Slightly positive (higher score = worse) |
| Inspection month | +0.028 | Minor seasonal signal |
| Score change from previous | +0.009 | Small positive trend effect |
| Days since last inspection | −0.001 | Negligible |
| Inspection year | −0.001 | Negligible |
| Historical avg critical | **−0.345** | Sustained critical history → paradoxically protective |

**Key insight:** The strongest positive predictors are `prior_inspection_count` and `critical_violations` — consistent with the literature finding that past behaviour predicts future compliance. The negative coefficient on `historical_avg_critical` is counterintuitive but may reflect the fact that restaurants with a long history of critical violations are well-known to inspectors and may have taken corrective action.

---

## 5. Discussion

### 5.1 Model Utility

The Balanced Logistic Regression model provides a practical tool for risk-based inspection scheduling. By flagging ~3,847 restaurants per quarter as high-priority, it could:

- **Reduce unnecessary inspections** by approximately 18% by deprioritising consistently Grade-A restaurants.
- **Catch violations earlier** by directing inspection resources to establishments with recent critical violations.
- **Support transparent decisions** — logistic regression coefficients are directly interpretable, satisfying the stakeholder requirement for explainability.

### 5.2 Limitations

1. **Class imbalance:** The target variable (inspection within 90 days) is inherently imbalanced. Balancing improves recall but lowers precision, meaning some Grade-A restaurants are unnecessarily flagged.
2. **Feature scope:** The model uses only internal inspection history. Incorporating external signals (311 complaint data, Yelp reviews, census demographics) could improve performance.
3. **Temporal leakage risk:** Although expanding-window lag features were used to prevent leakage, inspections in the test set are only ~12 months removed from the training set. Model degradation over longer horizons was not evaluated.
4. **Label definition:** "Inspection within 90 days" is a proxy for high risk, not a direct measure of B/C grade — restaurants re-inspected quickly may include those under routine follow-up for unrelated reasons.
5. **Missing cuisine encoding:** Cuisine type (known from the literature to be predictive) was not included in the final feature set due to high cardinality. Target encoding could be applied in future iterations.

### 5.3 Ethical Considerations

Our instructor rightly flagged the ethical dimension of risk-based inspection systems. We highlight three concerns:

**1. Surveillance burden.** Increased inspection frequency is not cost-free for restaurant owners — it demands staff time, documentation, and potential temporary closure. A system that repeatedly directs inspections to the same restaurants may impose a discriminatory burden.

**2. Self-fulfilling prophecy.** If lower-income neighbourhoods are overrepresented in the "high-risk" bucket (due to structural factors like older buildings, lower access to training, or tighter margins), the model will increase inspections there — finding more violations — which feeds back into the training data and deepens the cycle.

**3. Equity-aware deployment.** To mitigate these risks, we recommend:
   - Capping the maximum inspection frequency any single restaurant can receive via model prioritisation.
   - Monitoring flagging rates by borough and zip code, and adjusting threshold by area if disparate impact is detected.
   - Making model scores available to restaurant owners so they can self-correct proactively (the "early warning" use case).
   - Never using the model as the sole determinant of inspection scheduling — inspector judgment should remain primary.

The model is best understood as a decision-support tool, not an autonomous enforcement system.

---

## 6. Conclusions

We developed a machine learning system to predict which NYC restaurants are likely to receive a poor inspection grade (B or C) before their next scheduled inspection. Among three models tested, the **Balanced Logistic Regression** best serves the public-health objective, catching 69% of high-risk restaurants — a 45 percentage point improvement over the unbalanced baseline.

The strongest predictors are **prior inspection count** and **critical violation count**, confirming that past behaviour is the best predictor of future compliance. The model is interpretable, reproducible, and deployable as a prioritisation layer within DOHMH's existing inspection workflow.

With appropriate equity safeguards — threshold monitoring by neighbourhood, capping on repeat inspections, and transparency to restaurant owners — this system could meaningfully improve food safety outcomes for New York City residents while reducing unnecessary burdens on compliant establishments.

---

## 7. References

1. Jin, G. Z., & Leslie, P. (2003). The effect of information on product quality: Evidence from restaurant hygiene grade cards. *Quarterly Journal of Economics.*
2. Kang, J. S., et al. (2013). Where not to eat? Improving public policy by predicting hygiene inspections. *EMNLP.*
3. Xia, L., Sokolova, M., & Fernandez, R. (2021). Predicting food safety violations with ensemble learning. *arXiv.*
4. Zhang, Y., et al. (2020). Using online reviews to predict restaurant inspection outcomes. *ACM.*
5. Yun, J., et al. (2022). Socioeconomic factors and food safety risk prediction. *Decision Support Systems.*

---

## Appendix: Notebook Index

| Notebook | Contents |
|---|---|
| `01.2_data_loading_and_cleaning.ipynb` | Data acquisition, date parsing, year filtering (2022–2025), saved `cleaned_inspections.csv` |
| `02_exploratory_data_analysis.ipynb` | Grade distributions, borough patterns, score distributions, inspection volume over time |
| `03_inspection_level_preprocessing.ipynb` | Violation aggregation, chain detection, categorical encoding, inspection-level dataset |
| `04_feature_engineering.ipynb` | Target variable creation, temporal features, trend features |
| `05_baseline_modeling.ipynb` | Logistic Regression, Balanced LR, Random Forest, hyperparameter tuning, saved predictions |
| `06_model_evaluation_and_interpretation.ipynb` | Model comparison, feature importance, confusion matrix |
| `08_confusion_matrix.ipynb` | Detailed confusion matrix analysis and threshold sensitivity |
