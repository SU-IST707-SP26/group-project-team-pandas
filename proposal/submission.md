# Predicting NYC Restaurant Health Inspection Grades

### Team:

Jessica Aimunmondion (login: jessdion), Joel Barnes, Mian Hamid, Zeek Moore, Adelina Dunina (login: Adelina2302)

### Introduction

This project aims to develop a predictive model for New York City restaurant health inspection grades. Currently, the NYC Department of Health and Mental Hygiene (DOHMH) conducts restaurant inspections on a fixed schedule, typically once per year for all establishments. We propose to build a machine learning system that can identify restaurants at high risk of receiving poor grades (B or C) before their scheduled inspections occur.

Our approach represents a shift from the current reactive inspection model to a proactive, risk-based system. Instead of treating all restaurants equally regardless of their compliance history, our model will analyze multiple signals including restaurant characteristics (cuisine type, location, age), historical inspection patterns, violation trends, and neighborhood factors to prioritize high-risk establishments. This predictive capability would enable inspectors to focus their limited resources where they are most needed, potentially catching food safety issues before they result in public health incidents.

While past research has explored predictive models for restaurant inspections, our work is novel in several ways. First, we incorporate a richer feature set that includes temporal patterns and neighborhood-level characteristics beyond what previous studies have used. Second, we focus specifically on NYC's A/B/C grading system, which has direct business implications that make our predictions actionable for multiple stakeholders. Third, we design our model with interpretability as a core requirement, ensuring that predictions can be explained to restaurant owners and justified to the public.

If successful, three main groups benefit:

**1. NYC Department of Health (DOHMH)**

- Can schedule inspections more efficiently by focusing on high-risk restaurants first
- Reduces foodborne illness outbreaks by catching problems earlier
- Better allocation of limited inspection resources

**2. Restaurant Owners**

- Get early warning if they're at risk of a bad grade
- Can fix problems before the official inspection
- Avoid the revenue loss that comes with B or C grades (studies show 20-30% drop in customers)

**3. NYC Residents/Diners**

- Safer dining experiences
- More informed restaurant choices
- Fewer cases of food poisoning (NYC reports ~3,000 foodborne illness cases annually)

### Literature Review

Restaurant health inspections in New York City and many other cities are usually conducted on a fixed schedule, with restaurants inspected periodically and graded after violations are found. Research by **Jin and Leslie (2003)** showed that inspection results tend to repeat over time, meaning restaurants with past violations are more likely to perform poorly again¹. This suggests that inspection data contains useful patterns that can be used for prediction. More recent studies have built on this idea by applying machine learning methods to inspection records. **Kang et al. (2013)** found that predictive models can improve the identification of high risk restaurants², while **Xia et al. (2021)** demonstrated that modern ensemble methods further increase accuracy³. **Zhang et al. (2020)** also showed that combining inspection data with online reviews improves risk detection⁴. Although these studies show that prediction is possible, most existing systems remain focused on analyzing past results rather than providing early warnings that can be used before inspections occur. In addition, many models are developed for specific cities or datasets, limiting their broader use.

These limitations highlight important needs for stakeholders. The **NYC Department of Health and Mental Hygiene (DOHMH)** needs tools that help prioritize inspections based on risk so that limited staff and resources are used more effectively. Studies suggest that neighborhood and socioeconomic factors can improve predictions, but these are rarely used in practice⁵. **Restaurant owners** need early alerts that allow them to fix problems before official inspections, helping them avoid poor grades and financial losses. **NYC residents and diners** depend on inspections to ensure food safety and need reliable and fair systems that protect public health. **Policy makers** also rely on inspection data to guide regulations and funding decisions. Our project addresses these needs by developing a transparent and reproducible model using NYC open data to support more proactive and equitable food safety monitoring.


# Method

## Data Preprocessing Needs

### A. Cyclical Features:
- Convert inspection dates to datetime  
   - Helps to observe cycles in the dataset (days, months). For example: 
        - Amount of time since a Restaurant’s last inspection
        - If restaurant has had past violations
        - Is there a trend or seasonality to a Restaurant’s violations

### B. Categorical Features:
- **One-Hot Encoding**
  - Food specialization of each Restaurant
  - The borough of each Restaurant
- **Binary**
  - Chain vs Independent
  - Frequency Encoding
  - Past Violations

### C. Text Data:
- Violation descriptions 
- Business name 
- Address 

### D. Feature Engineering:
- Create risk scores from previous performance

## Modeling Approach

### A. Base Models:
- XGBoost
- Random Forest
- Logistic Regression 

## Evaluation Strategy

### A. Primary Metrics:

- **Recall:** 
  Recall = True Positives / (True Positives + False Negatives)  
    - This metric indicates how many High-Risk Restaurants were caught by the model.

- **Precision:** 
  Precision = True Positives / (True Positives + False Positives)  
    - This metric indicates how many High-Risk Restaurants flagged by the model that actually turned out to be High-Risk.

- **F1-Score:** 
  F1 = 2 * (Precision * Recall) / (Precision + Recall)  
  
  - Balances between Precision and Recall:
    - Not missing dangerous restaurants (recall)
    - Not wasting resources (precision)

### B. Business-Specific Metrics:
- Cost savings 
- Fewer unnecessary inspections
- Reduced overhead expenses
- Reduction in foodborne illness reports
- Efficiency of inspector operations
- Fewer trips made from inspector
- Better geographic coverage
- Early problem detection

### C. Validation Strategy:
- Train/Test split (prevent data leakage)
- Rolling window (seasonal patterns)
- Geographic (Borough-specific patterns)


### Project Plan


### Risks


### References

¹ Jin, G. Z., & Leslie, P. (2003). *The effect of information on product quality: Evidence from restaurant hygiene grade cards*. Quarterly Journal of Economics.  
https://academic.oup.com/qje/article/118/2/409/1880761

² Kang, J. S., Kuznetsova, P., Luca, M., & Choi, Y. (2013). *Where not to eat? Improving public policy by predicting hygiene inspections*. EMNLP.  
https://www.aclweb.org/anthology/D13-1178/

³ Xia, L., Sokolova, M., & Fernandez, R. (2021). *Predicting food safety violations with ensemble learning*. arXiv.  
https://arxiv.org/abs/2101.08943

⁴ Zhang, Y., et al. (2020). *Using online reviews to predict restaurant inspection outcomes*. ACM.  
https://dl.acm.org/doi/10.1145/3394486.3403242

⁵ Yun, J., et al. (2022). *Socioeconomic factors and food safety risk prediction*. Decision Support Systems.  
https://www.sciencedirect.com/science/article/pii/S0168169922001056