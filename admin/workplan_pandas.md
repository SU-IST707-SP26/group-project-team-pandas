# WORKPLAN.md

## Active Plan

### Milestone 1: Data Acquisition & Understanding
- [✅] M1.T1 — Obtain NYC restaurant inspection dataset (Team)
- [✅] M1.T2 — Explore dataset structure and variables (Team)
- [✅] M1.T3 — Assess data quality and completeness (Team)

---

### Milestone 2: Data Preparation & Preprocessing
- [✅] M2.T1 — Clean dataset (handle nulls, format dates, filter years 2022–2025) (Team)
- [✅] M2.T2 — Convert data to inspection-level records (Joel)
- [✅] M2.T3 — Aggregate violation counts and critical violations (Joel)
- [✅] M2.T4 — Apply encoding (one-hot, frequency encoding) (Zeek)

---

### Milestone 3: Feature Engineering
- [✅] M3.T1 — Create temporal features (month, year) (Team)
- [✅] M3.T2 — Create historical features (average score, violations) (Joel)
- [✅] M3.T3 — Create trend features (score change, prior inspections) (Team)
- [✅] M3.T4 — Define target variable (inspection within 90 days) (Jessica)

---

### Milestone 4: Baseline Modeling
- [✅] M4.T1 — Train/test split (Team)
- [✅] M4.T2 — Logistic regression baseline (Jessica)
- [✅] M4.T3 — Balanced logistic regression (Jessica)
- [✅] M4.T4 — Random Forest model (Jessica)

---

### Milestone 5: Model Improvement
- [✅] M5.T1 — Hyperparameter tuning (Random Forest) (Jessica)
- [✅] M5.T2 — Model comparison and selection (Team)

---

### Milestone 6: Evaluation & Interpretation
- [✅] M6.T1 — Generate classification reports (Team)
- [✅] M6.T2 — Analyze feature importance (Jessica)
- [✅] M6.T3 — Create confusion matrix (Jessica)
- [✅] M6.T4 — Interpret model results for stakeholders (Team)

---

### Milestone 7: Visualization & Final Outputs
- [✅] M7.T1 — Create feature importance visualizations (Team)
- [⏳] M7.T2 — Design dashboard mockup (Team)
- [⏳] M7.T3 — Final report writing and narrative (Team)
- [✅] M7.T4 — Organize notebooks and GitHub structure (Jessica)

---

## Changelog

### 2026-03-10
- (Team) ⏳ M1.T2 — Dataset contains multiple entries per inspection; decided to restructure to inspection-level

### 2026-03-12
- (Joel) ⏳ M3.T2 — Implemented historical aggregation using expanding window to avoid data leakage

### 2026-03-13
- (Team) 🔄 M4.* — Initial focus on predicting inspection outcomes (scores and grades)

#### 2026-03-18
- (Team) 🔄 M3.T4 — Pivoted target variable to inspection likelihood based on professor feedback  
- (Jessica) ⏳ M4.T3 — Balanced logistic regression significantly improved recall for inspections  
- (Jessica) ⏳ M5.T1 — Hyperparameter tuning improved accuracy but not recall; retained balanced logistic regression  
- (Jessica) 🆕 M6.T3 — Added confusion matrix for deeper evaluation  
- (Jessica) ⏳ M7.T4 — Restructured project into modular notebooks and cleaned GitHub (removed large files)