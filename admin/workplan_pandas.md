# Work Plan

## Active Tasks

### Milestone 1: Data Acquisition & Understanding
- ✅ M1.T1 — Obtain NYC restaurant inspection dataset (Team)
- ✅ M1.T2 — Explore dataset structure and variables (Team)
- ✅ M1.T3 — Assess data quality and completeness (Team)

---

### Milestone 2: Data Preparation & Preprocessing
- ✅ M2.T1 — Clean dataset (filter 2022–2025, handle nulls, format dates) (Team)
- ✅ M2.T2 — Convert data to inspection-level records (Joel)
- ✅ M2.T3 — Aggregate violation and critical violation counts (Joel)
- ✅ M2.T4 — Apply encoding: one-hot (inspection type, BORO), frequency encoding (violation code), target encoding (cuisine description), chain vs independent binary (Zeek)
- ✅ M2.T5 — Text preprocessing for violation descriptions, business name, and address fields (Zeek)

---

### Milestone 3: Feature Engineering
- ✅ M3.T1 — Create temporal features (month, year) (Team)
- ✅ M3.T2 — Create historical features (rolling averages, prior inspections) (Joel)
- ✅ M3.T3 — Create trend features (score change, inspection intervals) (Team)
- ✅ M3.T4 — Define target variable: inspection within 90 days (Jessica)
- ✅ M3.T5 — Research and validate domain-appropriate features for ML (Joel, Jessica)

---

### Milestone 4: Baseline Modeling
- ✅ M4.T1 — Train/test split (Team)
- ✅ M4.T2 — Logistic regression baseline (Jessica)
- ✅ M4.T3 — Balanced logistic regression (Jessica)
- ✅ M4.T4 — Random Forest model (Jessica)
- ✅ M4.T5 — Project coordination and identification of top independent restaurant chains (Jessica)
- ✅ M4.T6 — Assisted with editing and finalizing the midterm report (Mian)

---

### Milestone 5: Model Improvement
- ✅ M5.T1 — Hyperparameter tuning (Random Forest) (Jessica)
- ✅ M5.T2 — Refined modeling approach, implemented target variable, led notebook structure (Jessica)
- ✅ M5.T3 — Continued preprocessing support and encoding validation (Zeek)
- ✅ M5.T4 — Finalized feature engineering pipeline and historical aggregation logic (Joel)
- ✅ M5.T5 — Supported report edits and reviewed model outputs (Mian)
- ✅ M5.T6 — Compare models and select best approach (Team)

---

### Milestone 6: Evaluation & Interpretation
- ✅ M6.T1 — Generate classification reports (Team)
- ✅ M6.T2 — Interpret results and align with stakeholder goals (Joel)
- ✅ M6.T3 — Create confusion matrices for all models (Zeek)
- ✅ M6.T4 — Analyze feature importance and coefficient magnitudes (Jessica)
- ✅ M6.T5 — Ensure consistency across the codebase (Zeek)
- ✅ M6.T6 — Model interpretation, explainability analysis, error diagnostics (Joel)

---

### Milestone 7: Visualization & Dashboard
- ✅ M7.T1 — Visualization development and prototype dashboard design (Mian)
- ✅ M7.T2 — Check geo information is added to pipeline (Zeek, Mian)
- ✅ M7.T3 — Model interpretation, explainability analysis, error diagnostics — committed and pushed (Joel)
- ✅ M7.T4 — Organize notebooks and GitHub structure (Jessica)

---

### Milestone 8: Final Report, Submission & Presentation
- ⏳ M8.T1 — Write final report with interpretability section and stakeholder narrative (Team)
- [ ] M8.T2 — Code documentation and GitHub cleanup (Team)
- [ ] M8.T3 — Final presentation preparation (Joel)
- [ ] M8.T4 — Submission prep and final review (Jessica, Joel)

---

## Changelog

### 2026-04-25
- (Team) Reconciled workplan against spreadsheet worklog; updated all milestones to reflect current state
- (Mian) Completed M7.T1 — Visualization development and prototype dashboard design (Apr 12–Apr 25)
- (Joel) Completed M7.T3 — Explainability analysis and error diagnostics committed and pushed
- (Team) Added Milestone 8 for final report, submission, and presentation (deadline May 5, 2026)
- (Team) Marked Milestone 7 fully complete; M8.T1 set as current active task

### 2026-03-18
- (Team) M3.T4 — Pivoted from inspection outcome prediction to inspection likelihood prediction based on professor feedback
- (Jessica) Completed M4.T3 — Balanced logistic regression improved recall for inspections to 69%
- (Jessica) Completed M5.T1 — Hyperparameter tuning improved accuracy but not recall; retained balanced logistic regression as primary recall model
- (Jessica) Added M6.T3 — Confusion matrix analysis added for all three models
- (Jessica) Completed M7.T4 — Restructured project into modular notebooks and cleaned GitHub (removed large files)