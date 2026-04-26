# Worklog PANDAS

---

## 2026-05-05 - Final Report, Documentation, and Submission (Jessica, Joel)
**Context**: All project deliverables must be finalized, documented, and submitted by the deadline.

**Status**: In Progress | **Priority**: High

**Solution Implemented**:
- Final report writing
- Code documentation
- GitHub cleanup
- Submission preparation

**Files to Modify**: `final_report (1).md`, `README.md`, `work/*.ipynb`, `admin/worklog_pandas.md`

**Impact**: Represents the culmination of the project — all code, documentation, and deliverables submitted in final form.

**Related Plans**: See `admin/workplan_pandas.md`

---

## 2026-04-25 - Visualization and Dashboard Prototype (Mian)
**Context**: The risk dashboard mockup was the primary deliverable for this sprint. A functional prototype was needed to demonstrate how model outputs could be communicated to non-technical stakeholders.

**Solution Implemented**:
- Integrated dashboard section between sections 6 and 7 in the final report
- Realigned final report structure to accommodate new content

**Files Modified**: `final_report (1).md`

**Impact**: Delivered a functional risk dashboard mockup integrated into the final report, enabling the team to proceed to final documentation and submission.

**Next Steps**: Begin final report writing, code documentation, GitHub cleanup, and submission preparation.

---

## 2026-04-25 - Model Explainability Finalization and Full Workbook Update (Joel)
**Context**: Model interpretation work needed to be finalized and committed. All notebooks required a comprehensive update to ensure consistency before the final phase.

**Solution Implemented**:
- Performed a full update across all workbook notebooks to ensure consistency
- Completed and committed model interpretation, explainability analysis, and error diagnostics
- Updated inspection-related visualizations for the final report
- Created the Final Presentation

**Files Modified**: `work/01.2_data_loading_and_cleaning.ipynb`, `work/02_exploratory_data_analysis.ipynb`, `work/03_inspection_level_preprocessing.ipynb`, `work/04_feature_engineering.ipynb`, `work/05_baseline_modeling.ipynb`, `work/06_model_evaluation_and_interpretation.ipynb`, `work/07_visualizations_for_report.ipynb`, `work/wholecode.ipynb`

**Impact**: Delivered a consistent, up-to-date set of notebooks, presentation-ready visualizations, and a final presentation for stakeholder delivery.

---

## 2026-04-25 - Geo Information Pipeline Verification (Zeek, Mian)
**Context**: Geographical data needed to be confirmed as part of the modeling pipeline to support location-based analysis. This work built on the earlier experimental refactoring and was conducted on the `temp-work` branch.

**Solution Implemented**:
- Extended the optional first map in `03_inspection_level_preprocessing.ipynb` to include latitude and longitude columns alongside the existing contextual features (DBA, boro, cuisine, street info)
- Added geo coordinates to the numeric candidates list in `05_baseline_modeling.ipynb` so they are available as model features when present
- Added a coordinate cleaning block in `05_baseline_modeling.ipynb` to handle missing or malformed geo values before modeling

**Files Modified** (`temp-work` branch): `work/01.2_data_loading_and_cleaning.ipynb`, `work/02_exploratory_data_analysis.ipynb`, `work/03_inspection_level_preprocessing.ipynb`, `work/04_feature_engineering.ipynb`, `work/05_baseline_modeling.ipynb`

**Breaking Changes**: Changes isolated to `temp-work` branch; only reviewed adjustments promoted to `main`.

**Impact**: Confirmed geographic feature availability across the pipeline. Geo coordinates can now flow from data loading through to the model when available.

---

## 2026-04-11 - Dashboard and Report Upload (Mian)
**Context**: A dashboard and consolidated notebook were needed to present model results and risk insights to non-technical stakeholders. No visualization layer existed at this point.

**Solution Implemented**:
- Uploaded initial dashboard mockup, final report draft, and consolidated notebook with contributions

**Files Created**: `dashboard_mockup.html`, `final_report (1).md`, `wholecode_ipynb_(with_mian's_contribution).ipynb`

**Impact**: Laid the groundwork for the risk dashboard mockup and final report targeted in the next sprint.

---

## 2026-04-11 - Model Interpretation, Confusion Matrix, and Error Diagnostics (Joel)
**Context**: With modeling complete, the team needed to understand why the model made specific predictions and where it failed.

**Problem Identified**: Model predictions were not explainable in their raw form, making it difficult to communicate findings or identify systematic failure patterns.

**Solution Implemented**:
- Began model interpretation and explainability analysis
- Created and refined confusion matrix outputs across modeling and evaluation notebooks
- Edited confusion matrix formatting and logic across the full notebook pipeline

**Files Modified**: `work/05_baseline_modeling.ipynb`, `work/06_model_evaluation_and_interpretation.ipynb`, `work/01.2_data_loading_and_cleaning.ipynb`, `work/02_exploratory_data_analysis.ipynb`, `work/03_inspection_level_preprocessing.ipynb`, `work/04_feature_engineering.ipynb`

**Impact**: Generated interpretable results and performance insights aligned with project objectives.

---

## 2026-04-11 - Experimental Refactoring of Preprocessing and Modeling Pipeline (Zeek)
**Context**: The preprocessing aggregation and modeling feature selection on `main` were rigid — the groupby in `03` only kept score and grade, and the feature list in `05` was hardcoded to 9 numeric columns. Exploratory changes were needed to make the pipeline more flexible, but had to be tested without affecting `main`.

**Problem Identified**: Adding new features (chain indicators, cuisine, boro, etc.) required manual edits in multiple cells. The pipeline needed to dynamically pick up available columns rather than relying on a static feature list.

**Solution Implemented**:
- **`work/03_inspection_level_preprocessing.ipynb`**: Replaced the two-column groupby aggregation (score, grade) with a dynamic `agg_spec` dictionary that conditionally includes optional contextual columns (`DBA_clean`, `BORO`, `CUISINE DESCRIPTION`, `INSPECTION TYPE`, `street_num`, `street_name`) using `first`, chain/name features (`is_chain`, `chain_size`, `name_len`, `name_has_digit`) using `max`, and joined violation text using a lambda — all gated by column availability checks
- **`work/05_baseline_modeling.ipynb`**: Replaced the hardcoded 9-feature list with a `numeric_candidates` list (13 features including chain and name indicators) and a `categorical_candidates` list (`boro`, `cuisine_description`, `inspection_type`, `grade`). Added dynamic column availability checks, type-specific null handling (`fillna(0)` for numeric, `fillna('Unknown')` for categorical), and one-hot encoding via `pd.get_dummies`. Removed a redundant train/test split rebuild cell and a duplicate model training block
- **`work/06_model_evaluation_and_interpretation.ipynb`**: Updated confusion matrix cell to load predictions from `shared_data/model_predictions.csv` instead of relying on in-memory variables, allowing the cell to run independently

**Decision**: All changes were committed to the `temp-work` branch rather than `main` to allow the team to evaluate whether the expanded feature set and dynamic pipeline improved model performance before promoting to production.

**Breaking Changes**: The aggregation output in `03` now includes additional columns when available, which changes the shape of `inspection_level` downstream. The feature matrix in `05` is now dynamically constructed and one-hot encoded, changing `X.shape` and potentially affecting model comparison baselines established on `main`.

**Impact**: Transformed the pipeline from a static, manually-maintained feature list to a flexible system that automatically incorporates new features as they become available in upstream preprocessing.

---

## 2026-04-11 - Codebase Consistency Review (Zeek)
**Context**: As the project accumulated multiple notebooks and scripts across sprints, inconsistencies in naming, formatting, and logic had developed. A review was needed before entering the final project stages.

**Solution Implemented**:
- Reviewed and ensured consistency across the codebase

**Files Reviewed**: `work/00_notebook_index.ipynb`, `work/01.2_data_loading_and_cleaning.ipynb`, `work/02_exploratory_data_analysis.ipynb`, `work/03_inspection_level_preprocessing.ipynb`, `work/04_feature_engineering.ipynb`, `work/05_baseline_modeling.ipynb`

**Impact**: Reduced technical debt and improved maintainability ahead of the final project stages.

---

## 2026-04-11 - Workplan and Admin Documentation Updates (Zeek, Mian)
**Context**: Project documentation needed to reflect current progress and updated plans.

**Solution Implemented**:
- Updated workplan and worklog documentation
- Adjusted vision document to reflect revised project direction

**Files Modified**: `admin/workplan_pandas.md`, `admin/worklog_pandas.md`, `admin/vision_pandas.md`

**Impact**: Kept project documentation aligned with current sprint progress and goals.

---

## 2026-03-28 - Modeling Pivot, Notebook Restructuring, and Target Variable Implementation (Jessica)
**Context**: Professor feedback indicated the original project focus on inspection outcome prediction did not align well with stakeholder goals.

**Problem Identified**: The initial framing of the problem was not actionable for restaurant owners. Predicting inspection outcomes offered limited practical value. A pivot to inspection likelihood prediction better supported proactive decision-making.

**Solution Implemented**:
- Refined modeling approach and implemented target variable for inspection within 90 days
- Restructured the monolithic `work/01_initial_exploration.ipynb` into organized modular notebooks (`01.2` through `07`)
- Added `.gitignore` to exclude generated data files
- Created a notebook index for easier navigation
- Built and compared models: logistic regression, balanced logistic regression, and random forest
- Performed hyperparameter tuning on random forest
- Identified balanced logistic regression as the preferred model based on recall

**Files Created**: `work/00_notebook_index.ipynb`, `work/01.2_data_loading_and_cleaning.ipynb`, `work/02_exploratory_data_analysis.ipynb`, `work/03_inspection_level_preprocessing.ipynb`, `work/04_feature_engineering.ipynb`, `work/05_baseline_modeling.ipynb`, `work/06_model_evaluation_and_interpretation.ipynb`, `work/07_visualizations_for_report.ipynb`, `work/wholecode.ipynb`, `.gitignore`

**Files Modified**: `work/01_initial_exploration.ipynb`

**Decision**: Balanced logistic regression was selected over random forest because recall was prioritized — the cost of missing a likely inspection outweighed the cost of false positives. Interpretability for stakeholders was a secondary factor.

**Breaking Changes**: Project restructured from a single notebook (`01_initial_exploration.ipynb`) into modular notebooks. Target variable redefined from inspection outcome to inspection likelihood within 90 days.

**Impact**: Realigned the project with stakeholder needs and established a finalized, modular modeling pipeline with emphasis on recall and interpretability.

**Related Plans**: See `admin/workplan_pandas.md`, `admin/vision_pandas.md`

---

## 2026-03-28 - Random Forest Confusion Matrix (Zeek)
**Context**: Model comparison required visual diagnostic tools to evaluate classifier performance across classes.

**Solution Implemented**:
- Created Random Forest confusion matrix for model evaluation
- Continued preprocessing support and encoding validation

**Files Modified**: `work/05_baseline_modeling.ipynb`, `work/03_inspection_level_preprocessing.ipynb`

**Impact**: Provided a visual diagnostic for Random Forest performance, supporting the team's model comparison and selection process.

---

## 2026-03-28 - Feature Engineering Pipeline and Aggregation Logic (Joel)
**Context**: Historical aggregation features were needed to capture restaurant-level patterns over time, such as violation frequency and recency of past inspections.

**Solution Implemented**:
- Finalized the feature engineering pipeline
- Implemented historical aggregation logic

**Files Modified**: `work/04_feature_engineering.ipynb`

**Impact**: Completed the feature engineering phase, providing the modeling pipeline with a robust set of engineered variables.

---

## 2026-03-28 - Report Edits and Model Output Review (Mian)

**Context**: Ongoing documentation and quality assurance of model outputs were necessary to maintain project standards following the modeling pivot.

**Solution Implemented**:
- Supported report edits
- Reviewed model outputs for accuracy and consistency

**Files Modified**: `wholecode_ipynb_(with_mian's_contribution).ipynb`

**Impact**: Contributed to documentation quality and verified that model results were reliable.

---

## 2026-03-14 - Baseline Model Development and Evaluation (Jessica)
**Context**: With feature engineering nearing completion, the team needed to establish baseline models to evaluate predictive performance.

**Solution Implemented**:
- Coordinated project deliverables and overall model development strategy
- Identified the top 10 independent restaurant chains in the dataset

**Files Modified**: `work/01_initial_exploration.ipynb`

**Impact**: Established baseline model benchmarks and provided a curated restaurant chain list to support binary feature development.

---

## 2026-03-14 - Frequency and One-Hot Encoding Implementation (Zeek)
**Context**: Categorical and text-based variables required encoding before they could be used in modeling. The team needed to balance information preservation against dimensionality.

**Solution Implemented**:
- Implemented frequency encoding for text-based features
- Applied one-hot encoding for categorical variables

**Files Modified**: `work/01_initial_exploration.ipynb`

**Decision**: Frequency encoding was chosen for text features to avoid the high dimensionality that one-hot encoding would introduce for high-cardinality text columns.

**Impact**: Transformed raw categorical and text data into model-ready numeric representations.

---

## 2026-03-14 - Feature Engineering Development (Joel)
**Context**: Engineered features needed to be formalized into a reproducible pipeline rather than remaining as ad-hoc transformations.

**Solution Implemented**:
- Led continued development of feature engineered variables

**Files Modified**: `work/01_initial_exploration.ipynb`

**Impact**: Advanced the feature set toward completion, supporting the transition to the modeling phase.

---

## 2026-03-14 - Midterm Report Editing and Finalization (Mian)
**Context**: The midterm report required review and refinement before submission to ensure it accurately reflected the team's methodology and progress.

**Solution Implemented**:
- Assisted with editing and finalizing the midterm report

**Files Modified**: `checkpoint/projectmidtermreport`

**Impact**: Ensured the submitted report was polished and accurately reflected the team's progress.

---

## 2026-03-07 - Midterm Report Submission (Zeek, Mian, Joel)
**Context**: The midterm report deadline required consolidation of all progress to date, including visualizations and analysis.

**Solution Implemented**:
- Wrote the mid-term report (Zeek, Mian)
- Pushed mid-term file from Google Doc to GitHub (Joel)
- Uploaded plots and plot analysis to the report (Zeek)
- Uploaded distribution charts (Bar Chart, Inspection Scores, Inspection Volume) to the checkpoint directory

**Files Modified**: `checkpoint/projectmidtermreport`, `checkpoint/Bar-Chart-Distribution.png`, `checkpoint/Distribution-of-inspection-Scores.png`, `checkpoint/Inspection-Scores.png`, `checkpoint/Inspection-Volume-Over-Time.png`, `Bar-Chart-Distribution-of-Inspection-Grades.png`

**Impact**: Successfully submitted the midterm report with supporting visualizations.

---

## 2026-02-28 - Binary Feature Development (Zeek)
**Context**: Structured binary and encoded features needed to be created to capture categorical patterns in the dataset.

**Solution Implemented**:
- Implemented chain vs independent restaurant classification
- Applied target encoding for cuisine description
- Implemented frequency encoding for violation codes
- Applied one-hot encoding for inspection type, boro, and chain vs independent

**Files Modified**: `work/01_initial_exploration.ipynb`

**Impact**: Created a comprehensive set of binary and encoded features ready for model consumption.

---

## 2026-02-28 - Temporal Filtering and Data Refinement (Mian)
**Context**: Temporal boundaries of the dataset needed tightening to improve model relevance and reduce leakage risk.

**Solution Implemented**:
- Reduced inspection year range from 2015–2025 to 2022–2025
- Collected and analyzed spikes in inspection volume

**Files Modified**: `work/01_initial_exploration.ipynb`

**Decision**: Year range was reduced to 2022–2025 to limit noise from older records and reduce the risk of temporal leakage, as inspection patterns and regulations changed significantly over the full 10-year span.

**Impact**: Reduced noise from older records and mitigated the risk of temporal data leakage in downstream modeling.

---

## 2026-02-28 - Feature Engineering Research (Joel, Jessica)
**Context**: The team needed a grounded understanding of which feature engineering methods would be most effective for the machine learning pipeline.

**Solution Implemented**:
- Researched feature engineering methods for structured data
- Identified domain-relevant features as the highest priority
- Began implementing frequency encoding strategies

**Files Modified**: `work/01_initial_exploration.ipynb`

**Impact**: Informed the team's approach to encoding and feature construction, prioritizing domain-specific features for the modeling phase.

---

## 2026-02-28 - Text Feature Exploration (Zeek)
**Context**: Text-based fields in the dataset (violation descriptions, business names, addresses) represented a potential source of predictive signal that had not yet been explored.

**Problem Identified**: Incorporating text features risked creating very high-dimensional data that could degrade model performance.

**Solution Implemented**:
- Explored text features including violation descriptions, business names, and addresses

**Files Modified**: `work/01_initial_exploration.ipynb`

**Impact**: Assessed the viability of text-based features and flagged dimensionality concerns for future mitigation.

---

## 2026-02-14 - Data Acquisition, Dataset Validation, and Initial EDA (Jessica, Joel)
**Context**: The project required an initial dataset and exploratory analysis before any feature engineering or modeling could begin.

**Solution Implemented**:
- Acquired and uploaded the NYC restaurant inspection dataset
- Performed dataset validation and initial data exploration
- Identified and addressed null values across columns
- Created initial visualizations including inspection score distributions and inspection volume over time
- Established project structure for the exploration notebook

**Files Modified**: `work/01_initial_exploration.ipynb`, `data/dataset.md`, `data/healthcare`, `checkpoint/Checkpoint 2.11.26`

**Impact**: Established a validated, cleaned dataset and initial visualizations providing a foundation for all subsequent feature engineering and modeling work.
