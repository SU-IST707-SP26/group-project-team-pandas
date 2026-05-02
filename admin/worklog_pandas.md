## 2026-04-26 - Geographic Integration and Professor Feedback Implementation (Team)

**Context**: Holding off on merging geographic features pending professor feedback. Feedback received and team now moving to integrate geo features alongside two additional recommendations.

**Work Completed**:
- (Zeek) Integrating geographic features from `temp-work` branch into main pipeline after professor sign-off
- (Team) Adding restaurant age feature: engineering time since first inspection as a proxy for how long a restaurant has been operating, with a default assumption that restaurants receive their first inspection within five years of opening
- (Team) Expanding training set to include the union of all restaurants so test set restaurants are represented in training with zeroed inspection rows, avoiding data leakage while giving the model exposure to unseen restaurants

**Impact**: Addresses the core blind spot identified by professor: restaurants in wealthier or under-inspected neighborhoods that never appear in inspection records. Geographic and socioeconomic context combined with restaurant age features give the model something to work with for never-inspected restaurants.

---

## 2026-04-12 - Geographic Features Built, Awaiting Feedback Before Merge (Team)

**Context**: Final sprint focused on completing outputs and preparing findings for presentation. Geographic features developed but held from main branch pending professor review.

**Work Completed**:
- (Zeek) Built geographic features into the pipeline using `temp-work` branch; changes not yet promoted to main pending professor feedback
- (Joel) Completed model interpretation, explainability analysis, and error diagnostics; built the final presentation deck
- (Mian) Completed visualization development and prototype dashboard design; supported pipeline validation
- (Jessica) Led presentation refinement, final review, and project coordination
- (Team) Updated worklog and workplan

**Impact**: All presentation deliverables complete. Geographic work staged and ready for integration pending feedback.

---

## 2026-03-29 - Model Interpretation Begun, Dashboard Work Started (Team)

**Context**: With the modeling pipeline in place, focus shifted to interpreting results and beginning dashboard development.

**Work Completed**:
- (Jessica) Reviewed and improved preprocessing workflow and led documentation review
- (Zeek) Ensured consistency across the codebase and managed branch workflow; created separate `temp-work` branch to test improvements before merging
- (Joel) Began model interpretation, explainability analysis, and error diagnostics
- (Mian) Started dashboard planning and initial prototype development

**Impact**: Interpretability work underway. Experimental improvements being tested in isolation before promotion to main branch.

---

## 2026-03-15 - Project Pivot to Inspection Likelihood, Modeling Pipeline Built (Team)

**Context**: Based on professor feedback, the team pivoted from predicting inspection outcomes to predicting inspection likelihood. This required rebuilding the target variable and modeling approach.

**Work Completed**:
- (Jessica) Refined modeling approach, implemented target variable for inspection within 90 days, and led notebook restructuring and GitHub organization
- (Joel) Finalized feature engineering pipeline and historical aggregation logic
- (Zeek) Continued preprocessing support and encoding validation
- (Mian) Supported report edits and reviewed model outputs
- (Team) Built and compared logistic regression, balanced logistic regression, and random forest models
- (Team) Performed hyperparameter tuning on random forest and evaluated models using classification metrics and confusion matrix
- (Team) Identified balanced logistic regression as the preferred model based on recall

**Impact**: Project fully realigned with stakeholder goals. Modeling pipeline complete with emphasis on recall and interpretability to support proactive decision making for restaurant owners.

---

## 2026-02-28 - Midterm Report Submitted, Feature Engineering Advancing (Team)

**Context**: Wrapping up the midterm deliverable while continuing parallel progress on feature engineering.

**Work Completed**:
- (Team) Completed and submitted the midterm report
- (Jessica) Identified the top 10 independent restaurant chains in the dataset
- (Zeek) Implemented frequency encoding for text-based features and applied one hot encoding for categorical variables
- (Joel) Led continued development of feature engineered variables
- (Mian) Assisted with editing and finalizing the midterm report

**Impact**: M2 deliverable complete. Feature engineering pipeline advancing with encoding methods in place.

---

## 2026-02-17 - EDA Complete, Feature Strategy Defined (Team)

**Context**: Beginning feature engineering phase after initial data acquisition.

**Work Completed**:
- (Team) Finalized cleaned dataset and completed exploratory data analysis including distributions and spike inspection
- (Mian) Reduced inspection years from 2015–2025 to 2022–2025 based on temporal filtering analysis
- (Jessica) Identified key feature opportunities including chain vs independent restaurants and violation frequency
- (Joel) Began researching feature engineering methods for structured data
- (Zeek) Started development of binary features and text feature exploration

**Impact**: Dataset scope narrowed and feature strategy aligned across team. Feature engineering work divided across binary features, temporal features, and text features.

**Next Steps**: Complete binary features, frequency encoding, and text feature exploration by Feb 28. Resolve encoding strategy and temporal data leakage concerns before modeling begins.







