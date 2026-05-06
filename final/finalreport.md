# Predicting NYC Restaurant Inspection Likelihood

### Team
Jessica Aimunmondion — POC (GitHub: jessdion), Joel Barnes (GitHub: jjbarnes21), Mian Hamid (GitHub: [Mian's GitHub ID]), Zeek Moore (GitHub: nlmoore-crypto)

---

### Introduction

Every year, the NYC Department of Health and Mental Hygiene (DOHMH) conducts tens of thousands of restaurant inspections across the five boroughs. The current system operates on a roughly annual schedule, treating all establishments with similar regularity regardless of their risk profile. This means that a restaurant with a history of critical violations receives the same inspection frequency as one with a clean record, and restaurants in neighborhoods with lower enforcement activity may go extended periods without regulatory attention.

This project began with the goal of predicting inspection outcomes, specifically the grade a restaurant would receive. Early in the process, however, it became clear that predicting outcomes after the fact does not create actionable value for the people most affected by inspections. Following feedback received at the midterm checkpoint, the project pivoted to a more meaningful framing: predicting the likelihood that a restaurant will be inspected at all within the next 90 days. The primary stakeholder shifted accordingly from the inspector workforce to restaurant owners themselves. Rather than building a tool that helps inspectors decide where to go, we set out to build one that helps restaurant owners anticipate when an inspector is coming and use that window to bring their establishment into compliance.

This reframing changes what the model needs to do and what success looks like. A restaurant owner who receives an early signal that an inspection is likely has a concrete opportunity to audit their kitchen, retrain staff, and fix outstanding violations before the official visit. The business stakes are real: poor grades carry documented revenue losses, and a proactive compliance posture is far more sustainable than reactive scrambling after a bad inspection. Our solution combines historical inspection records, engineered temporal and violation history features, and neighborhood-level geographic and socioeconomic data to estimate inspection likelihood. The final model, a balanced logistic regression, achieves 70% recall on the inspection class, meaning it correctly identifies 7 out of every 10 restaurants that will be inspected within the 90-day window.

The model does not fully solve the problem. Restaurants that have never been inspected remain largely invisible to any model trained on inspection records, and the 30% of inspections the model misses represent real establishments that would receive no advance signal. We address the first problem partially through a union training strategy and geographic features that allow the model to reason about neighborhood-level risk. The second problem is a limitation we acknowledge openly. What this project represents is a meaningful first step toward a compliance-oriented early warning system for NYC restaurant owners.

---

### Literature Review

Prior research has established that restaurant inspection outcomes are not random — they follow predictable temporal and spatial patterns. Jin and Leslie (2003) found that inspection results tend to repeat over time, with restaurants that received poor scores in one period more likely to perform poorly in subsequent cycles. Their work also showed that public disclosure policies, such as mandatory grade posting, measurably improve restaurant compliance behavior. This finding is foundational to the restaurant owner framing of our project: when operators have information about their risk profile, they act on it. Building on this, Kang et al. (2013) applied machine learning techniques directly to inspection records, demonstrating that classification models could predict violation likelihood with meaningful accuracy using structured administrative data alone. Their work validated the core premise that inspection patterns are learnable from historical records.

Later work expanded both the feature set and the algorithmic approach. Xia et al. (2021) demonstrated that ensemble methods substantially outperform single classifiers on inspection prediction tasks, particularly when the feature set includes violation history and temporal indicators. Zhang et al. (2020) pushed further by integrating user-generated content with administrative inspection records, finding that community signals often anticipate formal inspection outcomes by weeks. Their results suggest that combining structured and unstructured data sources produces more robust predictions, and point toward the potential value of incorporating complaint data such as NYC 311 filings in future work.

More recent work has drawn attention to the role of socioeconomic context in shaping inspection patterns. Yun et al. (2022) found that neighborhood-level factors including income, housing density, and demographic composition were significantly associated with violation rates even after controlling for establishment characteristics. This matters for a restaurant owner-facing tool because it suggests that some of the variation in inspection likelihood is structural rather than behavioral. A restaurant in a lower-income, higher-density neighborhood may face a different inspection cadence than one in a wealthier area regardless of its compliance record. Incorporating these signals allows the model to surface that context rather than treat all establishments as operating in identical environments.

Our approach builds on these contributions while departing from them in a direction motivated by stakeholder need. Most prior work frames the problem from the enforcement side: which restaurants should inspectors visit? We invert that framing. The goal here is to model the decision logic of the inspection system and surface the output as a signal for restaurant owners, giving them a data-driven basis to anticipate regulatory attention and act on it proactively. This distinction also shapes our choice of primary metric. We prioritize recall over precision because from a restaurant owner's perspective, a missed warning is a worse outcome than a false alarm that prompts unnecessary preparation.

---

### Data and Methods

#### Data

**Dataset Overview**

The primary dataset used in this project is the DOHMH NYC Restaurant Inspection Results dataset, sourced from NYC Open Data. The dataset is updated daily and at the time of use contained approximately 300,000 records across 27 columns, covering every formal inspection conducted by the New York City Department of Health and Mental Hygiene across all five boroughs. Each row represents a single inspection event, meaning that one restaurant may appear multiple times corresponding to its full inspection history.

Key fields used in this project include: CAMIS (a unique restaurant identifier assigned by DOHMH), DBA (the restaurant's operating name), borough, ZIP code, latitude and longitude coordinates, cuisine description, inspection date, violation codes, critical flag (indicating whether a violation was cited as critical or not critical), inspection score, and grade. Inspection scores are computed by summing the point values of all violations cited during a visit, with higher scores indicating worse performance. Grades of A, B, and C are assigned based on score thresholds and represent the public-facing outcome of an inspection cycle.

**Temporal Filtering**

The dataset spans inspections from the early 2000s through the present, but training on the full historical range introduced significant noise. COVID-19 substantially disrupted both restaurant operations and DOHMH enforcement activity beginning in 2020, resulting in inspection gaps, temporary closures, and irregular scoring patterns that do not reflect steady-state behavior. To focus on post-pandemic operational patterns and avoid this structural break in the data, we filtered to inspection records dated between January 2022 and December 2025. This window captures the return to normal enforcement cadence while providing sufficient volume for modeling. The filtered dataset retained approximately 200,000 records across the five boroughs.

**Class Imbalance**

A key characteristic of the filtered dataset is a substantial class imbalance in the target variable. When the binary target — whether a restaurant receives an inspection within the next 90 days — is computed, non-inspection records outnumber inspection records at a ratio of approximately 3.5 to 1. This reflects the natural distribution of inspection activity: most restaurants in any given 90-day window are not inspected, particularly lower-risk establishments that have demonstrated consistent compliance. This imbalance was a central design consideration in model selection and is discussed further in the Methods section.

**Exploratory Data Analysis**

Initial EDA conducted in notebook 02 revealed several patterns that informed both feature engineering and modeling decisions.

Inspection volume over time showed clear periodicity, with higher inspection activity in summer months consistent with known patterns in food safety enforcement — warmer temperatures and higher dining traffic correlate with elevated risk and increased inspector deployment. A notable dip in inspection counts is visible in the 2020 to 2021 period, validating the decision to exclude pre-2022 records from modeling.

The borough-level breakdown of inspection counts showed Manhattan and Brooklyn accounting for the largest share of inspections by volume, reflecting their higher restaurant density. However, when normalized by restaurant count, inspection rates across boroughs are more comparable, suggesting that raw inspection volume differences are largely a function of establishment density rather than differential enforcement intensity.

Violation score distributions showed a right skew, with the majority of inspections resulting in scores below 28 (the threshold for an A grade) and a smaller but meaningful tail of high-score inspections indicating serious compliance failures. The distribution of critical violation flags showed that approximately 40 percent of inspection records included at least one critical violation, making this a substantive signal for the model.

**Supplemental Data: Census ACS Estimates**

To capture neighborhood-level socioeconomic context, U.S. Census American Community Survey (ACS) 5-year estimates were incorporated as supplemental features. Borough-level variables derived from this source include median household income, population density, and poverty rate. These features were merged onto the inspection-level dataset using borough as the join key. While borough-level aggregation is coarser than tract or block-level data, it allowed the model to distinguish broad socioeconomic gradients across the city without introducing the data sparsity that would result from a finer geographic join.

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

All models were evaluated on a held-out test set of 9,718 records using precision, recall, F1-score, and accuracy. Because our goal is to give restaurant owners the earliest possible signal that an inspection is coming, recall on the inspection class (class 1) is our primary metric. A missed warning is a worse outcome than a false alarm that prompts unnecessary preparation.

**Logistic Regression**

The baseline logistic regression achieved 80% overall accuracy but performed poorly on the inspection class, with a recall of only 0.26. The model was heavily biased toward predicting no inspection, reflecting the class imbalance in the dataset where non-inspection records outnumber inspection records roughly 3.5 to 1. From a restaurant owner's perspective, this model would fail to flag the majority of upcoming inspections.

**Balanced Logistic Regression**

Applying class weights to penalize the model for missing inspections substantially improved recall on the inspection class to 0.70, at the cost of lower precision (0.44). Overall accuracy dropped to 73%, but the model correctly identified the majority of actual inspections in the test set. A restaurant owner using this model would receive advance warning roughly 7 out of every 10 times an inspection was coming. This is our preferred model given the stakeholder goal.

**Random Forest**

The random forest achieved the highest overall accuracy at 81% and reasonable precision at 0.62, but recall was only 0.38, meaning it missed more than half of actual inspections. While it generates fewer false alarms, its value as an early warning tool for restaurant owners is limited by how many real inspections it fails to surface.

| Model | Accuracy | Precision (inspection) | Recall (inspection) | F1 (inspection) |
|---|---|---|---|---|
| Logistic Regression | 0.80 | 0.60 | 0.26 | 0.36 |
| Balanced Logistic Regression | 0.73 | 0.44 | 0.70 | 0.54 |
| Random Forest | 0.81 | 0.62 | 0.38 | 0.47 |

The balanced logistic regression was selected as the final model. Feature importance analysis from the model coefficients identified time since open, historical average score, and critical violation count as the strongest positive drivers of inspection likelihood, suggesting that restaurants with longer operating histories, worse past scores, and more frequent violations are most likely to be inspected. Zip code inspection rate was also a notable positive driver, consistent with the hypothesis that restaurants in neighborhoods with higher baseline inspection activity face a higher probability of an upcoming visit regardless of their individual compliance record. This is precisely the kind of signal a restaurant owner benefits from knowing.

The addition of geographic and socioeconomic features in the final modeling phase, including latitude, longitude, borough indicators, median household income, population density, and poverty rate, was motivated by feedback received during the project and represents the most significant improvement over the baseline feature set. The union training strategy, which added zeroed placeholder rows for restaurants appearing in the test set but not in training, addressed the model's original blind spot around never-inspected restaurants.

Additional results including confusion matrices, feature importance charts, and error diagnostics are available in `work/06_model_evaluation_and_interpretation.ipynb`.

---

### Discussion

This project started with one stakeholder framing and ended with a better one. Early modeling efforts focused on predicting inspection outcomes, which would have been useful for inspectors deciding where to focus enforcement resources. Feedback received during the project pointed toward a more empowering direction: building a tool that gives restaurant owners themselves advance notice of a likely inspection, so they can take corrective action before the official visit rather than after. That pivot shaped every subsequent decision, from how we defined the target variable to why recall became the metric that mattered most.

The balanced logistic regression achieves 70% recall, meaning a restaurant owner using this model would receive a meaningful early warning roughly 7 out of 10 times an inspection was coming. The 30% of inspections the model misses represent a real gap. In a deployment context, this means the tool should be framed as a preparedness aid rather than a guarantee. A restaurant that receives a high-risk signal has a concrete reason to conduct an internal audit and address outstanding violations. A restaurant that does not receive a signal should not interpret that as clearance. Used correctly, the model creates better compliance incentives without requiring restaurant owners to wait passively for an inspector to arrive.

The feature importance results also carry a meaningful message for restaurant owners. The strongest drivers of inspection likelihood are a restaurant's own history: how long it has been operating, what its average score has been, and how many critical violations it has accumulated. This tells owners that their past performance is the most reliable predictor of when they will next be visited. Zip code inspection rate adds geographic context, confirming that neighborhood matters alongside individual track record. The inclusion of socioeconomic features like poverty rate and population density surfaces a structural reality: inspection patterns are not entirely driven by restaurant behavior. Some of the variation in inspection likelihood is geographic and systemic, and a complete early warning system for restaurant owners needs to account for that context, not just the establishment's own record.

---

### Limitations

Several limitations affect the scope and generalizability of the results presented in this project, and should be considered by any practitioner looking to extend or deploy this work.

The most immediate limitation is class imbalance. Despite applying class weights inversely proportional to class frequency in the balanced logistic regression, the 3.5-to-1 ratio of non-inspection to inspection records means the model operates in a structurally difficult classification environment. The balanced weights improve recall substantially, but the resulting precision of 0.44 means that roughly half of the restaurants the model flags as likely to be inspected will not actually receive a visit within the 90-day window. For a restaurant owner, a false alarm means unnecessary preparation costs. Further work on threshold tuning or alternative resampling strategies such as SMOTE would be needed to reduce this friction before any real-world deployment.

A second limitation concerns the never-inspected restaurant blind spot. The model is trained on inspection records, which means it has no direct signal for restaurants that have never appeared in the DOHMH dataset. These establishments may include some of the highest-risk targets precisely because they have not been subject to regular regulatory attention. The union training strategy adopted in this project, which added zeroed-out feature rows for restaurants appearing in the test set but absent from training, partially addresses this by ensuring the model encounters every establishment during training. However, a zeroed feature vector is a weak proxy for genuine risk assessment, and a restaurant owner who has never been inspected would receive a prediction based on neighborhood context alone rather than any record of their own compliance history.

A third limitation involves several proxy features that introduce structural uncertainty. The time-since-open feature assumes that each restaurant opened five years before its first recorded inspection, since actual open dates are not available in the DOHMH dataset. This assumption is applied uniformly and introduces noise for both newer and older restaurants. The socioeconomic features operate at the borough level, which groups vastly different neighborhoods together and limits the model's ability to distinguish between communities within the same borough. Additionally, approximately 15 percent of records had ungeocodable or zero-valued coordinates and were addressed through borough-median imputation, meaning a meaningful share of the training data carries estimated rather than observed geographic features. Finally, the model was trained exclusively on 2022 to 2025 data, so its behavior under different enforcement regimes or longer historical patterns is unknown.

---

### Future Work

Several directions could strengthen the model's value as a restaurant owner-facing compliance tool. The time-since-open feature currently relies on a proxy that assumes each restaurant opened five years before its first recorded inspection. Integrating actual open dates from the NYC business license database would eliminate this assumption and produce a more reliable signal, particularly for newer establishments that currently receive inaccurate age estimates. Similarly, the socioeconomic features used in this project operate at the borough level, which groups vastly different neighborhoods together. Replacing these with block-level or tract-level Census ACS data, mapped through HUD USPS crosswalk files, would give the model much finer geographic resolution and a better ability to distinguish between communities within the same borough.

Beyond data improvements, the modeling approach itself could benefit from architectures that explicitly capture spatial relationships. Graph neural networks or spatial models could learn from patterns among neighboring restaurants, helping determine whether a cluster of violations in one area predicts elevated risk for nearby establishments. Incorporating NYC 311 complaint data as an additional input signal would give the model access to community-reported concerns that often precede formal inspections and are currently invisible to the feature set. On the deployment side, translating this model into a restaurant-facing application — one where an owner enters their CAMIS ID and receives a risk score along with the top factors driving it — would transform the analysis into a practical compliance tool. Finally, expanding the training window beyond 2022 to 2025 to include pre-pandemic years, with careful handling of the COVID-era disruption, would allow the model to capture longer-term cyclical patterns in inspection behavior.

---

### References

¹ Jin, G. Z., & Leslie, P. (2003). *The effect of information on product quality: Evidence from restaurant hygiene grade cards*. Quarterly Journal of Economics. https://academic.oup.com/qje/article/118/2/409/1880761

² Kang, J. S., Kuznetsova, P., Luca, M., & Choi, Y. (2013). *Where not to eat? Improving public policy by predicting hygiene inspections*. EMNLP. https://www.aclweb.org/anthology/D13-1178/

³ Xia, L., Sokolova, M., & Fernandez, R. (2021). *Predicting food safety violations with ensemble learning*. arXiv. https://arxiv.org/abs/2101.08943

⁴ Zhang, Y., et al. (2020). *Using online reviews to predict restaurant inspection outcomes*. ACM. https://dl.acm.org/doi/10.1145/3394486.3403242

⁵ Yun, J., et al. (2022). *Socioeconomic factors and food safety risk prediction*. Decision Support Systems. https://www.sciencedirect.com/science/article/pii/S0168169922001056