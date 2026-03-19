# WORKPLAN.md

## Active Tasks

### Milestone 1: Data Acquisition & Understanding
- [✅] M1.T1 — Obtain NYC restaurant inspection dataset (Team)
- [✅] M1.T2 — Explore dataset structure and variables (Team)
- [✅] M1.T3 — Assess data quality and completeness (Team)

---

### Milestone 2: Data Preparation & Preprocessing
- [✅] M2.T1 — Clean dataset (filter 2022–2025, handle nulls, format dates) (Team)
- [✅] M2.T2 — Convert data to inspection-level records (Joel)
- [✅] M2.T3 — Aggregate violation and critical violation counts (Joel)
- [✅] M2.T4 — Apply encoding (one-hot, frequency encoding) (Zeek)

---

### Milestone 3: Feature Engineering
- [✅] M3.T1 — Create temporal features (month, year) (Team)
- [✅] M3.T2 — Create historical features (rolling averages, prior inspections) (Joel)
- [✅] M3.T3 — Create trend features (score change, inspection intervals) (Team)
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
- [✅] M5.T2 — Compare models and select best approach (Team)

---

### Milestone 6: Evaluation & Interpretation
- [✅] M6.T1 — Generate classification reports (Team)
- [⏳] M6.T2 — Interpret results and align with stakeholder goals (Team)
- [✅] M6.T3 — Create confusion matrix (Jessica)
- [✅] M6.T4 — Analyze feature importance (Jessica)

---

### Milestone 7: Final Outputs & Submission
- [⏳] M7.T1 — Design dashboard mockup (Team)
- [⏳] M7.T2 — Write final report (Team)
- [ ] M7.T3 — Final presentation preparation (Team)
- [✅] M7.T4 — Organize notebooks and GitHub structure (Jessica)

---

## Changelog

### 2026-03-18
- (Team) 🔄 M3.T4 — Pivoted from inspection outcome prediction to inspection likelihood prediction based on professor feedback  
- (Jessica) ⏳ M4.T3 — Balanced logistic regression improved recall for inspections  
- (Jessica) ⏳ M5.T1 — Hyperparameter tuning improved accuracy but not recall; retained balanced logistic regression  
- (Jessica) 🆕 M6.T3 — Added confusion matrix for evaluation  
- (Jessica) ⏳ M7.T4 — Restructured project into modular notebooks and cleaned GitHub (removed large files)