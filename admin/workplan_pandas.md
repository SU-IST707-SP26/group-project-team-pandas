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

### 2026-02-14
- (Jessica, Joel) ✅ M1.T1 — Acquired and uploaded NYC restaurant inspection dataset

### 2026-02-28
- (Zeek) ✅ M2.T4 — Implemented binary features: chain vs independent, target encoding (cuisine description), frequency encoding (violation codes), one-hot encoding (inspection type, boro)
- (Zeek) ✅ M2.T5 — Explored text features: violation descriptions, business names, addresses
- (Mian) ✅ M2.T1 — Reduced inspection year range from 2015–2025 to 2022–2025; analyzed inspection volume spikes
- (Joel, Jessica) ✅ M3.T5 — Completed feature engineering research; identified domain-relevant features as highest priority
- (Team) ✅ M3.T1 — Created temporal features (month, year)

### 2026-03-07
- (Zeek, Mian) ✅ Wrote the mid-term report
- (Joel) ✅ Pushed mid-term file from Google Doc to GitHub (`checkpoint/projectmidtermreport`)
- (Zeek) ✅ Uploaded plots and plot analysis to `checkpoint/`

### 2026-03-14
- (Jessica) ✅ M4.T5 — Project coordination; identified top 10 independent restaurant chains
- (Zeek) ✅ M2.T4 — Implemented frequency encoding for text features and one-hot encoding for categorical variables

### 2026-03-18
- (Team) M3.T4 — Pivoted from inspection outcome prediction to inspection likelihood prediction based on professor feedback
- (Jessica) ✅ M4.T3 — Balanced logistic regression improved recall for inspections to 69%
- (Jessica) ✅ M5.T1 — Hyperparameter tuning improved accuracy but not recall; retained balanced logistic regression as primary recall model


### 2026-03-28
- (Jessica) ✅ M5.T2 — Refined modeling approach; restructured project from single notebook (`01_initial_exploration.ipynb`) into modular notebooks (`00` through `07`); added `.gitignore`
- (Jessica) ✅ M3.T4 — Implemented target variable for inspection within 90 days
- (Jessica) ✅ M5.T1 — Hyperparameter tuning improved accuracy but not recall; retained balanced logistic regression


### 2026-04-11
- (Joel) ✅ M6.T6 — model interpretation, explainability analysis, and error diagnostics
- (Joel) ✅ M6.T3 — Created and refined confusion matrices across `05_baseline_modeling.ipynb` and `06_model_evaluation_and_interpretation.ipynb`; edited confusion matrix logic across full notebook pipeline
- (Zeek) 🆕 Created `temp-work` branch for experimental pipeline changes
- (Zeek) Refactored `03_inspection_level_preprocessing.ipynb` on `temp-work` — replaced static two-column groupby with dynamic `agg_spec` that conditionally includes contextual columns



### 2026-04-25
- (Team) Reconciled workplan against spreadsheet worklog
- (Joel) Completed M7.T3 — Explainability analysis and error diagnostics committed and pushed

### 2026-05-01
(Joel) ✅ M3.T6 — Added time_since_open_days feature to inspection_model_df in notebook 04; estimates restaurant open date as 5 years before first inspection and computes days elapsed

### 2026-05-02
(Zeek) ✅ M7.T2 — Pushed Option A fix to main: refactored notebook 03 .agg() to carry boro, zipcode, latitude, longitude through the pipeline
(Zeek, Mian) ✅ M3.T7 — Wrote geo/socioeconomic feature engineering code for notebook 04: lat/long cleaning, borough encoding, spatial grid, zipcode-level proxies, borough-level Census/ACS data, inspection coverage ratio; code ready, pending insertion and testing
(Zeek, Mian) ✅ M5.T8 — Wrote notebook 05 edits to add new geo/socioeconomic feature names to Jessica's feature list; on branch, not yet merged to main

### 2026-05-03
(Jessica) ✅ M5.T7 — Working on union-based train/test approach: every restaurant in test set also appears in training set with zeroed-out inspection rows to prevent data leakage