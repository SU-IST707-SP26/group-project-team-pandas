# Predicting NYC Restaurant Inspection Likelihood

### Team
Jessica Aimunmondion — POC (GitHub: jessdion), Joel Barnes (GitHub: [Joel's GitHub ID]), Mian Hamid (GitHub: [Mian's GitHub ID]), Zeek Moore (GitHub: [Zeek's GitHub ID])

---

### Introduction

Every year, the NYC Department of Health and Mental Hygiene (DOHMH) conducts tens of thousands of restaurant inspections across the five boroughs. The current system operates on a roughly annual schedule, treating all establishments with similar regularity regardless of their risk profile. This means that a restaurant with a history of critical violations receives the same inspection frequency as one with a clean record, and restaurants in under-inspected neighborhoods may go years without any regulatory attention at all.

This project addresses a core inefficiency in that system: the inability to proactively identify which restaurants are most likely to require an inspection within a given window of time. Our primary stakeholder is the DOHMH inspector workforce. Inspectors operate with finite time and resources, and a model that helps them prioritize which establishments to visit next has direct operational value. Rather than predicting the outcome of an inspection (the grade), we predict the likelihood that a restaurant will receive an inspection within the next 90 days, framing the problem as a binary classification task.

Our solution combines historical inspection records with engineered temporal features, violation history, and neighborhood-level geographic and socioeconomic data to surface high-risk restaurants before they appear on a fixed schedule. A key design choice throughout this project was prioritizing recall over precision: in this context, missing a restaurant that needed inspection is a worse outcome than flagging one that did not. Our final model, a balanced logistic regression, achieves 70% recall on the inspection class, meaning it correctly identifies 7 out of every 10 restaurants that will be inspected. This represents a meaningful step toward a proactive, risk-based inspection system.

If the work does not fully solve the problem, it is by design an incremental step. Restaurants that have never been inspected remain a blind spot in any model trained on inspection records alone. We address this partially by incorporating geographic and socioeconomic features that allow the model to reason about neighborhood-level risk, and by including all restaurants in the training set regardless of inspection history. Further progress toward surfacing truly invisible restaurants would require additional data sources outside the scope of this project.

---

### Literature Review

[Joel — write this section. Cover the following: Jin and Leslie (2003) on inspection patterns repeating over time, Kang et al. (2013) on ML for inspection prediction, Xia et al. (2021) on ensemble methods, Zhang et al. (2020) on combining inspection data with external sources, and Yun et al. (2022) on socioeconomic factors. Then explain how our approach differs: we pivot from outcome prediction to likelihood prediction, we incorporate geo and socioeconomic features, and we prioritize recall and interpretability. You can pull from the proposal's literature review as a starting point but update it to reflect the final project direction. Aim for 3–4 paragraphs.]

---

### Data and Methods

#### Data

[Mian — write this section. Cover the following: the DOHMH NYC Restaurant Inspection Results dataset from NYC Open Data, approximately 300,000 records across 27 columns updated daily. Describe the key fields: CAMIS, DBA, borough, ZIP code, lat/long, cuisine type, inspection date, violation codes, critical flags, scores, and grades. Discuss how we filtered to 2022–2025 inspection years and pivoted to violent public records only. Include the class imbalance: roughly 3.5 non-inspection records for every inspection record. Include 2–3 visualizations from notebook 02 such as the inspection distribution over time and the borough breakdown. Note that Census ACS 5-year estimates were incorporated for socioeconomic features by tract, mapped to blocks using HUD USPS crosswalk files.]

#### Methods

The dataset was filtered to inspection records between 2022 and 2025 to focus on post-pandemic patterns and avoid inconsistencies introduced by COVID-era closures and reduced enforcement. Where latitude or longitude values were missing or recorded as zero, borough-level median coordinates were imputed to preserve the record.

1. **Preprocessing**:
Preprocessing and early-stage encoding explored several directions — frequency encoding for violation codes, target encoding for cuisine descriptions, TF-IDF vectorization of violation text, and binary chain detection — though not all of these carried through to the final modeling pipeline. The features that reached the model included: historical aggregation features (average score, average critical violations, prior inspection count, days since last inspection, score change from previous), violation counts (total violations, critical violations), one-hot encoding for borough, time-since-open proxy (days since first inspection with 5-year default assumption), and geographic/socioeconomic features (latitude, longitude, zipcode, zip average score, zip restaurant count, zip inspections per restaurant, median household income, population density, poverty rate).

2. **Feature engineering**:
The historical aggregation features were computed using two approaches: average score, average critical violations, and prior inspection count used expanding windows with a one-row shift to prevent data leakage, while days since last inspection used a simple date difference and score change used a lagged subtraction. The time-since-open proxy estimated each restaurant's age by assuming it opened five years before its first recorded inspection and computing the elapsed days. For the geographic and socioeconomic features, raw coordinates and zipcode were preserved through the inspection-level groupby via a dynamic aggregation spec, then cleaned in the feature engineering stage by replacing zero-valued coordinates with borough median imputation. Zipcode-level proxies were derived by aggregating the inspection data itself per zipcode, while borough-level socioeconomic indicators were merged from hardcoded U.S. Census ACS 5-year estimates.

3. **Target variable**: 
The target variable was defined as a binary indicator: whether a given restaurant would receive an inspection within the next 90 days. In practice, this was computed from each restaurant's inspection history by checking whether a subsequent inspection occurred within 90 days of the current record; rows with no future inspection were excluded. The train/test split followed a union strategy in which every restaurant appearing in the test set was also represented in the training set with zeroed-out feature rows. This design choice addressed the blind spot of never-inspected restaurants by ensuring the model encountered every establishment during training, even those with no prior inspection history to learn from.

4. **Models**: 
Three models were trained and evaluated. A baseline logistic regression served as the reference point. A balanced logistic regression applied class weights inversely proportional to class frequency, penalizing the model more heavily for missing actual inspections. A random forest with 100 estimators was trained as a nonlinear comparison. Both logistic regression models used a StandardScaler via a scikit-learn Pipeline to normalize the wide feature value ranges introduced by the geographic and socioeconomic columns.


#### Supporting Files

The following notebooks in the `work/` folder contain all supporting analysis for this report:

| Notebook | Purpose |
| `01_2_data_loading_and_cleaning.ipynb` | Initial data ingestion, cleaning, and validation |
| `02_exploratory_data_analysis.ipynb` | EDA including distributions, class balance, and spike inspection |
| `03_inspection_level_preprocessing.ipynb` | Inspection-level feature prep and target variable creation |
| `04_feature_engineering.ipynb` | Full feature engineering pipeline including geo and socioeconomic features |
| `05_baseline_modeling.ipynb` | Model training, union split strategy, and classification results |
| `06_model_evaluation_and_interpretation.ipynb` | Confusion matrices, error diagnostics, and feature importance |
| `07_visualizations_for_report.ipynb` | Final charts and visualizations used in this report |

---

### Results

All models were evaluated on a held-out test set of 9,718 records using precision, recall, F1-score, and accuracy. Because our stakeholder goal is proactive inspection prioritization, recall on the inspection class (class 1) is our primary metric. Missing a restaurant that needed inspection is a worse outcome than a false alarm.

**Logistic Regression**

The baseline logistic regression achieved 80% overall accuracy but performed poorly on the inspection class, with a recall of only 0.27. The model was heavily biased toward predicting no inspection, reflecting the class imbalance in the dataset where non-inspection records outnumber inspection records roughly 3.5 to 1.

**Balanced Logistic Regression**

Applying class weights to penalize the model for missing inspections substantially improved recall on the inspection class to 0.70, at the cost of lower precision (0.44). Overall accuracy dropped to 74%, but the model correctly identified 1,497 of 2,143 actual inspections in the test set. This is our preferred model given the stakeholder goal.

**Random Forest**

The random forest achieved the highest overall accuracy at 84% and the best precision on the inspection class at 0.69, but recall was only 0.48, meaning it missed more than half of actual inspections. It is the most conservative of the three models, flagging fewer restaurants but with higher confidence when it does.

| Model | Accuracy | Precision (inspection) | Recall (inspection) | F1 (inspection) |
|---|---|---|---|---|
| Logistic Regression | 0.80 | 0.60 | 0.27 | 0.38 |
| Balanced Logistic Regression | 0.74 | 0.44 | 0.70 | 0.54 |
| Random Forest | 0.84 | 0.69 | 0.48 | 0.57 |

The balanced logistic regression was selected as the final model. Feature importance analysis from the model coefficients identified historical average score, zip code average score, and critical violation count as the strongest positive drivers of inspection likelihood. Median household income and zip code inspection rate were among the strongest negative drivers, consistent with the hypothesis that restaurants in wealthier, lower-density inspection areas are systematically less likely to appear in inspection records regardless of their actual risk.

The addition of geographic and socioeconomic features in the final modeling phase, including latitude, longitude, borough indicators, median household income, population density, and poverty rate, was motivated directly by professor feedback and represents the most significant improvement over the baseline feature set. The union training strategy, which added zeroed placeholder rows for restaurants appearing in the test set but not in training, addressed the model's original blind spot around never-inspected restaurants.

Additional results including confusion matrices, feature importance charts, and error diagnostics are available in `work/06_model_evaluation_and_interpretation.ipynb`.

---

### Discussion

[Joel — write this section. Reflect honestly on the results. Address the following: did we achieve our goal of building a useful inspection prioritization tool? A 70% recall means 30% of inspections are still missed — is that acceptable for this use case? What does the feature importance tell us about what actually drives inspection likelihood — is it the restaurant's behavior or the neighborhood it's in? Reflect on the pivot from grade prediction to likelihood prediction and whether that was the right call. Connect the results back to the stakeholder: does this model actually help a DOHMH inspector prioritize their day? Be frank. Aim for 2–3 paragraphs.]

---

### Limitations

[Mian — write this section. Cover the following: class imbalance despite using balanced weights, the never-inspected restaurant blind spot and how we only partially addressed it, the proxy nature of time since open (we don't have actual open dates), the fact that our socioeconomic features are at the borough level rather than the block level which loses granularity, the hardcoded model results in the save cell which should be replaced with live outputs in a production system, and the 15% data loss from ungeocodable records. Also note that the model was trained on 2022–2025 data only so its behavior on pre-pandemic patterns is unknown. Aim for 2–3 paragraphs.]

---

### Future Work

Several directions could strengthen the model's ability to surface high-risk and under-inspected restaurants. The time-since-open feature currently relies on a proxy that assumes each restaurant opened five years before its first recorded inspection. Integrating actual open dates from the NYC business license database would eliminate this assumption and produce a more reliable signal. Similarly, the socioeconomic features used in this project operate at the borough level, which groups vastly different neighborhoods together. Replacing these with block-level or tract-level Census ACS data, mapped through HUD USPS crosswalk files, would give the model much finer geographic resolution and a better ability to distinguish between neighborhoods within the same borough.

Beyond data improvements, the modeling approach itself could benefit from architectures that explicitly capture spatial relationships. Graph neural networks or spatial models could learn from patterns among neighboring restaurants. This could help determine whether a cluster of violations in one block predicts risk for nearby establishments. Incorporating NYC 311 complaint data as an additional input signal would give the model access to community-reported concerns that often precede formal inspections. On the deployment side, building a live inspector-facing dashboard that pulls predictions from the model in real time would translate the analysis into an operational tool. Finally, expanding the training window beyond 2022–2025 to include pre-pandemic years would allow the model to capture longer-term cyclical patterns in inspection behavior, though this would require careful handling of the disruption introduced by COVID-era enforcement changes.

---

### References

¹ Jin, G. Z., & Leslie, P. (2003). *The effect of information on product quality: Evidence from restaurant hygiene grade cards*. Quarterly Journal of Economics. https://academic.oup.com/qje/article/118/2/409/1880761

² Kang, J. S., Kuznetsova, P., Luca, M., & Choi, Y. (2013). *Where not to eat? Improving public policy by predicting hygiene inspections*. EMNLP. https://www.aclweb.org/anthology/D13-1178/

³ Xia, L., Sokolova, M., & Fernandez, R. (2021). *Predicting food safety violations with ensemble learning*. arXiv. https://arxiv.org/abs/2101.08943

⁴ Zhang, Y., et al. (2020). *Using online reviews to predict restaurant inspection outcomes*. ACM. https://dl.acm.org/doi/10.1145/3394486.3403242

⁵ Yun, J., et al. (2022). *Socioeconomic factors and food safety risk prediction*. Decision Support Systems. https://www.sciencedirect.com/science/article/pii/S0168169922001056
