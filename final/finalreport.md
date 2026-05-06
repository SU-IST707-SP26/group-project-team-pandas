# Predicting NYC Restaurant Inspection Likelihood

### Team
Jessica Aimunmondion — POC (GitHub: jessdion), Joel Barnes (GitHub: [ GitHub ID: jjbarnes21]), Mian Hamid (GitHub: [Mian's GitHub ID]), Zeek Moore (GitHub: [Zeek's GitHub ID])

---

### Introduction

Every year, the NYC Department of Health and Mental Hygiene (DOHMH) conducts tens of thousands of restaurant inspections across the five boroughs. The current system operates on a roughly annual schedule, treating all establishments with similar regularity regardless of their risk profile. This means that a restaurant with a history of critical violations receives the same inspection frequency as one with a clean record, and restaurants in under-inspected neighborhoods may go years without any regulatory attention at all.

This project addresses a core inefficiency in that system: the inability to proactively identify which restaurants are most likely to require an inspection within a given window of time. Our primary stakeholder is the DOHMH inspector workforce. Inspectors operate with finite time and resources, and a model that helps them prioritize which establishments to visit next has direct operational value. Rather than predicting the outcome of an inspection (the grade), we predict the likelihood that a restaurant will receive an inspection within the next 90 days, framing the problem as a binary classification task.

Our solution combines historical inspection records with engineered temporal features, violation history, and neighborhood-level geographic and socioeconomic data to surface high-risk restaurants before they appear on a fixed schedule. A key design choice throughout this project was prioritizing recall over precision: in this context, missing a restaurant that needed inspection is a worse outcome than flagging one that did not. Our final model, a balanced logistic regression, achieves 70% recall on the inspection class, meaning it correctly identifies 7 out of every 10 restaurants that will be inspected. This represents a meaningful step toward a proactive, risk-based inspection system.

If the work does not fully solve the problem, it is by design an incremental step. Restaurants that have never been inspected remain a blind spot in any model trained on inspection records alone. We address this partially by incorporating geographic and socioeconomic features that allow the model to reason about neighborhood-level risk, and by including all restaurants in the training set regardless of inspection history. Further progress toward surfacing truly invisible restaurants would require additional data sources outside the scope of this project.

---

### Literature Review

Within our project, we have reviewed a variety of academic research to better understand the industry in regards to inspections. Prior research has established that restaurant inspection outcomes are not random — they follow predictable temporal and spatial patterns. Jin and Leslie (2003) research states that inspection reviews are mostly based on external factors such as news coverage, scoring rule changes, and disclosure policy. This tells that time dictates the convenience of scoring in regards to some restaurant based on societal standards and policies. Building on this foundation, Kang et al. (2013) applied machine learning techniques directly to inspection records, showing that classification models could predict violation likelihood with meaningful accuracy using structured administrative data alone. Their work validated the core premise that inspection outcomes are learnable, and helped establish the methodological groundwork for subsequent predictive modeling efforts in this space.

Later work expanded both the parameter representation and the algorithmic approach. Muramatsu (2022) explored greedy merge methods — including degrading-merge and upgrading-merge algorithms — and found that combining canonical representation with these quantization techniques substantially improved computational performance over conventional approaches, particularly in cases where the bit error probability is close to zero and relative precision becomes the critical measure. Around the same time, Meng et al. (2020) pushed the boundaries of what counts as relevant input by integrating user-provided category hierarchies with large-scale text corpora such as New York Times articles and arXiv paper abstracts. Their results suggested that minimal, easy-to-provide supervision in the form of category names carries genuine guiding signals, and that models enriched with this hierarchical structure outperformed those relying on unsupervised topic discovery alone.

More recent work has drawn attention to the role of socioeconomic context in shaping inspection outcomes. Yun et al. (2022) found that neighborhood-level factors — including income, housing density, and demographic composition — were significantly associated with violation rates even after controlling for establishment characteristics. This finding made it apparent that socioeconomic features will improve the model but also cause biases to occur if overturned too much. Taking this to consideration, Yun et al. (2022) work details the concept  of a richer, multi-source modeling paradigm that accounts for the geographic and social context in which food service establishments operate.

Our approach builds on these contributions while departing from them in three important ways. First, we focus on estimating relative likelihood of a violation across establishments, granting restaurant owners knowledge of what code violations would cause more frequent visits of an inspector. Second, we incorporate both geospatial features and census-derived socioeconomic variables as first-class inputs, recognizing that neighborhood context shapes compliance in ways that establishment-level data alone cannot capture. Third, we want to make sure we capture recall rather than precision within this model as we want to maximize the restaurant owners who are at more risk to fail to be notified of upcoming inspections than restaurants that are relatively at good standards. This ensures that the model assist more restaurant owners. These design choices reflect a shift from academic benchmarking toward a deployment-oriented framing of the inspection prioritization problem.


---

### Data and Methods

#### Data

[Mian — write this section. Cover the following: the DOHMH NYC Restaurant Inspection Results dataset from NYC Open Data, approximately 300,000 records across 27 columns updated daily. Describe the key fields: CAMIS, DBA, borough, ZIP code, lat/long, cuisine type, inspection date, violation codes, critical flags, scores, and grades. Discuss how we filtered to 2022–2025 inspection years. Include the class imbalance: roughly 3.5 non-inspection records for every inspection record. Include 2–3 visualizations from notebook 02 such as the inspection distribution over time and the borough breakdown. Note that Census ACS 5-year estimates were incorporated for socioeconomic features at the borough level.]

#### Methods

The dataset was filtered to inspection records between 2022 and 2025 to focus on post-pandemic patterns and avoid inconsistencies introduced by COVID-era closures and reduced enforcement. Where latitude or longitude values were missing or recorded as zero, borough-level median coordinates were imputed to preserve the record.

1. **Preprocessing**:
Preprocessing and early-stage encoding explored several directions including frequency encoding for violation codes, target encoding for cuisine descriptions, TF-IDF vectorization of violation text, and binary chain detection, though not all of these carried through to the final modeling pipeline. The features that reached the model included: historical aggregation features (average score, average critical violations, prior inspection count, days since last inspection, score change from previous), violation counts (total violations, critical violations), one-hot encoding for borough, time-since-open proxy (days since first inspection with 5-year default assumption), and geographic/socioeconomic features (latitude, longitude, zipcode, zip average score, zip restaurant count, zip inspections per restaurant, median household income, population density, poverty rate).

2. **Feature engineering**:
The historical aggregation features were computed using two approaches: average score, average critical violations, and prior inspection count used expanding windows with a one-row shift to prevent data leakage, while days since last inspection used a simple date difference and score change used a lagged subtraction. The time-since-open proxy estimated each restaurant's age by assuming it opened five years before its first recorded inspection and computing the elapsed days. For the geographic and socioeconomic features, raw coordinates and zipcode were preserved through the inspection-level groupby via a dynamic aggregation spec, then cleaned in the feature engineering stage by replacing zero-valued coordinates with borough median imputation. Zipcode-level proxies were derived by aggregating the inspection data itself per zipcode, while borough-level socioeconomic indicators were merged from U.S. Census ACS 5-year estimates.

3. **Target variable**:
The target variable was defined as a binary indicator: whether a given restaurant would receive an inspection within the next 90 days. This was computed from each restaurant's inspection history by checking whether a subsequent inspection occurred within 90 days of the current record. The train/test split followed a union strategy in which every restaurant appearing in the test set was also represented in the training set with zeroed-out feature rows. This design choice addressed the blind spot of never-inspected restaurants by ensuring the model encountered every establishment during training, even those with no prior inspection history to learn from.

4. **Models**:
Three models were trained and evaluated. A baseline logistic regression served as the reference point. A balanced logistic regression applied class weights inversely proportional to class frequency, penalizing the model more heavily for missing actual inspections. A random forest with 100 estimators was trained as a nonlinear comparison. Both logistic regression models used a StandardScaler via a scikit-learn Pipeline to normalize the wide feature value ranges introduced by the geographic and socioeconomic columns.

#### Supporting Files

The following notebooks in the `work/` folder contain all supporting analysis for this report:

| Notebook | Purpose |
|---|---|
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

The baseline logistic regression achieved 80% overall accuracy but performed poorly on the inspection class, with a recall of only 0.26. The model was heavily biased toward predicting no inspection, reflecting the class imbalance in the dataset where non-inspection records outnumber inspection records roughly 3.5 to 1.

**Balanced Logistic Regression**

Applying class weights to penalize the model for missing inspections substantially improved recall on the inspection class to 0.70, at the cost of lower precision (0.44). Overall accuracy dropped to 73%, but the model correctly identified the majority of actual inspections in the test set. This is our preferred model given the stakeholder goal.

**Random Forest**

The random forest achieved the highest overall accuracy at 81% and reasonable precision on the inspection class at 0.62, but recall was only 0.38, meaning it missed more than half of actual inspections. It is the most conservative of the three models, flagging fewer restaurants but with higher confidence when it does.

| Model | Accuracy | Precision (inspection) | Recall (inspection) | F1 (inspection) |
|---|---|---|---|---|
| Logistic Regression | 0.80 | 0.60 | 0.26 | 0.36 |
| Balanced Logistic Regression | 0.73 | 0.44 | 0.70 | 0.54 |
| Random Forest | 0.81 | 0.62 | 0.38 | 0.47 |

The balanced logistic regression was selected as the final model. Feature importance analysis from the model coefficients identified time since open, historical average score, and critical violation count as the strongest positive drivers of inspection likelihood, suggesting that restaurants with longer operating histories, worse past scores, and more frequent violations are most likely to be inspected. Zip code inspection rate was also a notable positive driver, consistent with the hypothesis that restaurants in neighborhoods with higher baseline inspection activity are more likely to be flagged.

The addition of geographic and socioeconomic features in the final modeling phase, including latitude, longitude, borough indicators, median household income, population density, and poverty rate, was motivated directly by professor feedback and represents the most significant improvement over the baseline feature set. The union training strategy, which added zeroed placeholder rows for restaurants appearing in the test set but not in training, addressed the model's original blind spot around never-inspected restaurants.

Additional results including confusion matrices, feature importance charts, and error diagnostics are available in `work/06_model_evaluation_and_interpretation.ipynb`.

---

### Discussion

Overall, the balanced logistic regression model helps pave the way for a proactive inspection prioritization tool. However, the 70% recall means that three out of the ten restaurants will not be notified of an upcoming inspection and be flagged as a low priority. This could mark an issue as some restaurant owners will be unaware of an inspection taking place without any warning,  The pivot from grade prediction to likelihood prediction was the right call for the stakeholder. Predicting whether a restaurant will receive a grade of A or C is an outcome question that does not change inspector behavior in advance. Predicting whether it will be inspected at all within 90 days is an operational question — one that maps directly onto how inspectors actually plan their weeks.
The features that are very prominent in the model are the combination of the restaurant’s own history and what neighborhood it operates within. The restaurant who has a past that revolves around having usually low average scores and high number of code violations will more likely be scheduled again for an inspection. At the same time, zip code inspection rate emerged as a notable driver, suggesting the model is also picking up on structural patterns in how enforcement resources are distributed geographically. This raises an equity question in reflection to if inspectors usually over analyse restaurants in a particular neighborhood compared to others. The inclusion of socioeconomic features like poverty rate and population density was intended to partially correct for this, but borough-level aggregation limits how much granularity the model can actually capture. In its current form, the model gives the majority of restaurant owners more of a chance to be prepared to correct any pass issues when an inspector comes by and try to improve their chances of passing.


---

### Limitations

[Mian — write this section. Cover the following: class imbalance despite using balanced weights, the never-inspected restaurant blind spot and how we only partially addressed it, the proxy nature of time since open (we don't have actual open dates), the fact that our socioeconomic features are at the borough level rather than the block level which loses granularity, the 15% data loss from ungeocodable records, and the model being trained on 2022–2025 data only so its behavior on pre-pandemic patterns is unknown. Aim for 2–3 paragraphs.]

---

### Future Work

Several directions could strengthen the model's ability to surface high-risk and under-inspected restaurants. The time-since-open feature currently relies on a proxy that assumes each restaurant opened five years before its first recorded inspection. Integrating actual open dates from the NYC business license database would eliminate this assumption and produce a more reliable signal. Similarly, the socioeconomic features used in this project operate at the borough level, which groups vastly different neighborhoods together. Replacing these with block-level or tract-level Census ACS data, mapped through HUD USPS crosswalk files, would give the model much finer geographic resolution and a better ability to distinguish between neighborhoods within the same borough.

Beyond data improvements, the modeling approach itself could benefit from architectures that explicitly capture spatial relationships. Graph neural networks or spatial models could learn from patterns among neighboring restaurants, helping determine whether a cluster of violations in one block predicts risk for nearby establishments. Incorporating NYC 311 complaint data as an additional input signal would give the model access to community-reported concerns that often precede formal inspections. On the deployment side, building a live inspector-facing dashboard that pulls predictions from the model in real time would translate the analysis into an operational tool. Finally, expanding the training window beyond 2022–2025 to include pre-pandemic years would allow the model to capture longer-term cyclical patterns in inspection behavior, though this would require careful handling of the disruption introduced by COVID-era enforcement changes.

---

### References

¹ Jin, G. Z., & Leslie, P. (2003). *The effect of information on product quality: Evidence from restaurant hygiene grade cards*. Quarterly Journal of Economics. https://academic.oup.com/qje/article/118/2/409/1880761

² Kang, J. S., Kuznetsova, P., Luca, M., & Choi, Y. (2013). *Where not to eat? Improving public policy by predicting hygiene inspections*. EMNLP. https://www.aclweb.org/anthology/D13-1178/

³ Xia, L., Sokolova, M., & Fernandez, R. (2021). *Predicting food safety violations with ensemble learning*. arXiv. https://arxiv.org/abs/2101.08943

⁴ Zhang, Y., et al. (2020). *Using online reviews to predict restaurant inspection outcomes*. ACM. https://dl.acm.org/doi/10.1145/3394486.3403242

⁵ Yun, J., et al. (2022). *Socioeconomic factors and food safety risk prediction*. Decision Support Systems. https://www.sciencedirect.com/science/article/pii/S0168169922001056
