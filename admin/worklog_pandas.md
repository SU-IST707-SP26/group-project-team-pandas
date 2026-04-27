# Worklog PANDAS

## Team Worklog (Feb 17–23, 2026)

### Team Members and Roles
- **Jessica** – Project Lead, Feature Strategy and Coordination  
- **Joel** – Feature Engineering Research  
- **Mian** – Temporal Filtering and Data Refinement  
- **Zeek** – Binary Feature Development and Text Feature Exploration  

### Tasks Completed
- Finalized cleaned dataset  
- Completed exploratory data analysis including distributions and spike inspection  
- Reduced inspection years from **2015–2025 to 2022–2025**  
- Identified important feature opportunities such as **chain vs independent restaurants** and **violation frequency**

### Tasks In Progress
- Developing binary features  
- Implementing frequency encoding  
- Researching feature engineering methods for structured data  
- Exploring text features including **violation descriptions, business names, and addresses**

### Blockers / Issues
- Choosing the best encoding strategy  
- Incorporating text features without creating very high dimensional data  
- Avoiding temporal data leakage  
- Determining which engineered features will be most useful

### Notes
- Feature engineering expected to be completed by **Feb 28**  
- **Modeling scheduled to begin March 1**  
- Work divided across **binary features, temporal features, and text features**

## Team Worklog (Feb 28 – Mar 14, 2026)

### Team Members and Roles
- **Jessica** – Project coordination, identified top 10 independent restaurant chains  
- **Zeek** – Implemented frequency encoding for text features and one hot encoding  
- **Joel** – Led feature engineering development  
- **Mian** – Assisted with editing and finalizing the midterm report  

### Tasks Completed
- Completed and submitted the **midterm report**  
- Identified the **top 10 independent restaurant chains** in the dataset  
- Implemented **frequency encoding** for text based features  
- Applied **one hot encoding** for categorical variables  
- Continued development of **feature engineered variables**

### Notes
- Work during this period focused on preparing and finalizing the **midterm report** while continuing progress on feature engineering.


## Team Worklog (Mar 15 – Mar 28, 2026)

### Team Members and Roles
- **Jessica** – Refined modeling approach, implemented target variable, led notebook restructuring and GitHub organization  
- **Zeek** – Continued preprocessing support and encoding validation  
- **Joel** – Finalized feature engineering pipeline and historical aggregation logic  
- **Mian** – Supported report edits and reviewed model outputs  

### Tasks Completed
- Pivoted project focus from **inspection outcome prediction** to **inspection likelihood prediction** based on professor feedback  
- Created target variable for **inspection within 90 days**  
- Built and compared models: **logistic regression, balanced logistic regression, and random forest**  
- Performed **hyperparameter tuning** on random forest  
- Evaluated models using **classification metrics and confusion matrix**  
- Identified **balanced logistic regression as the preferred model** based on recall  
- Restructured project into organized **modular notebooks**  
- Cleaned and updated **GitHub repository** (removed large files, added .gitignore)  

### Notes
- This phase focused on aligning the project with stakeholder goals and finalizing the modeling pipeline.  
- Emphasis was placed on **recall and interpretability** to support proactive decision making for restaurant owners.  


## Team Worklog (Mar 29 – Apr 11, 2026)

### Team Members and Roles
- **Jessica** – Data preprocessing refinement, project coordination, documentation review  
- **Zeek** – Ensured consistency across the codebase and branch management  
- **Joel** – Model interpretation, explainability analysis, and error diagnostics  
- **Mian** – Dashboard planning and initial prototype development  

### Tasks Completed
- Reviewed and improved preprocessing workflow  
- Ensured consistency across the codebase  
- Began model interpretation and explainability analysis  
- Conducted error diagnostics  
- Started dashboard creation  

### Notes
- This phase focused on evaluating model performance and interpreting results to ensure alignment with project objectives.  
- A separate branch (`temp-work`) was created to test possible improvements before merging.  
- Sprint goal: Interpretable results and performance insights generated.  
- Marked complete as of Apr 11, 2026. Priority: Medium.  


## Team Worklog (Apr 12 – Apr 25, 2026)

### Team Members and Roles
- **Jessica** – Presentation refinement, project coordination, final review support  
- **Zeek** – Verified geographic information in pipeline and tested code updates  
- **Joel** – Completed model interpretation, explainability analysis, error diagnostics, and built final presentation  
- **Mian** – Visualization development, prototype dashboard design, and pipeline validation support  

### Tasks Completed
- Verified geographic information was added to the pipeline using `temp-work` branch  
- Updated worklog and workplan  
- Completed and committed model interpretation, explainability analysis, and error diagnostics  
- Created final presentation deck  
- Completed visualization development and prototype dashboard design  
- Assessed changes for promotion to main branch  

### Notes
- This phase focused on finalizing outputs and preparing findings for presentation.  
- Team reviewed which experimental changes should be merged into the main branch.  
- Sprint goal: Risk dashboard mockup completed.  
- Marked complete as of Apr 25, 2026. Priority: Medium.







