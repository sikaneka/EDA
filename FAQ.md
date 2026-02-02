# Exploratory Data Analysis (EDA) – Frequently Asked Questions

This document addresses commonly asked questions related to **Exploratory Data Analysis (EDA)**. It is intended for academic use, project repositories, and professional data science workflows.

---

## 1. What is Exploratory Data Analysis (EDA)?

Exploratory Data Analysis (EDA) is a systematic approach to analyzing datasets to summarize their main characteristics, identify patterns, detect anomalies, and validate assumptions using statistical techniques and visualizations.

---

## 2. What is the objective of performing EDA?

The primary objectives of EDA are to:

* Understand the structure and quality of the dataset
* Identify missing values, inconsistencies, and anomalies
* Analyze variable distributions and relationships
* Guide data preprocessing and feature engineering decisions
* Reduce risks during model development

---

## 3. When should EDA be performed in the data science lifecycle?

EDA should be conducted **after data collection and before preprocessing or modeling**. It forms the foundation for all subsequent steps, including data cleaning, transformation, and model selection.

---

## 4. What are the standard steps involved in EDA?

A typical EDA workflow includes:

1. Dataset overview (dimensions, schema, data types)
2. Data quality checks (missing values, duplicates)
3. Descriptive statistical analysis
4. Univariate analysis
5. Bivariate and multivariate analysis
6. Outlier detection
7. Distribution and skewness assessment
8. Correlation and dependency analysis

---

## 5. How are missing values identified and handled during EDA?

Missing values are identified through summary statistics and visual inspection. Handling strategies depend on context and may include:

* Removal of records or features
* Statistical imputation (mean, median, mode)
* Domain-driven or model-based imputation

---

## 6. What are outliers, and how are they detected?

Outliers are observations that deviate significantly from the majority of the data. Common detection methods include:

* Interquartile Range (IQR)
* Z-score analysis
* Boxplots and distribution plots

---

## 7. Should outliers always be removed?

No. Outliers should only be removed if they are confirmed to be errors or irrelevant to the analysis. In many real-world domains, outliers may represent meaningful extreme cases.

---

## 8. What is skewness, and why is it important?

Skewness describes the asymmetry of a variable’s distribution. Highly skewed features may impact statistical assumptions and model performance, particularly for linear and distance-based models.

---

## 9. How is skewness typically handled?

Common approaches include:

* Logarithmic or power transformations
* Robust scaling techniques
* Using models that are less sensitive to distribution shape

---

## 10. What role do visualizations play in EDA?

Visualizations provide intuitive insights into data behavior and are essential for:

* Identifying trends and patterns
* Detecting anomalies
* Understanding feature relationships

Common visualizations include histograms, boxplots, scatter plots, and heatmaps.

---

## 11. What is correlation analysis?

Correlation analysis quantifies the strength and direction of relationships between numerical variables and assists in feature selection and multicollinearity detection.

---

## 12. Which correlation methods are commonly used?

* **Pearson correlation**: Linear relationships, normally distributed data
* **Spearman correlation**: Monotonic relationships, non-parametric data
* **Kendall correlation**: Ordinal data or small sample sizes

---

## 13. How are categorical variables analyzed during EDA?

Categorical variables are analyzed using frequency distributions, bar charts, and cross-tabulations to understand category dominance and relationships with the target variable.

---

## 14. Is feature scaling part of EDA?

Feature scaling is not part of EDA. However, EDA helps determine whether scaling is required during the preprocessing stage.

---

## 15. How does EDA differ for regression and classification problems?

While the fundamental steps remain similar, the focus varies:

* **Regression**: Target distribution, variance, outliers, skewness
* **Classification**: Class distribution, imbalance, and separability

---

## 16. How much EDA is considered sufficient?

EDA is considered sufficient when the analyst has a clear understanding of data quality, structure, and key relationships necessary to proceed confidently with preprocessing and modeling.

---

## 17. Does EDA guarantee improved model performance?

EDA does not guarantee optimal model performance; however, it significantly reduces modeling errors and improves interpretability and reliability.

---

## 18. What tools are commonly used for EDA?

Commonly used tools and libraries include:

* Python
* Pandas
* NumPy
* Matplotlib
* Seaborn

---

## 19. Is this EDA framework reusable for other projects?

Yes. The EDA framework presented in this repository is generic and can be adapted to a wide range of datasets and problem statements.

---

## 20. How can issues or improvements be suggested?

For issues, clarifications, or enhancement requests, please open an issue or discussion within this repository.

---

## 21. What is data leakage in the context of EDA?

Data leakage occurs when information from outside the training dataset, or from the target variable itself, is unintentionally used during analysis or feature preparation. During EDA, leakage may arise if insights from the full dataset influence preprocessing or feature selection before data splitting.

---

## 22. How can data leakage be avoided during EDA?

To minimize data leakage:

* Perform train–test splitting before target-dependent analysis
* Avoid using target variable statistics for imputing or transforming features
* Conduct target-based EDA only on the training dataset
* Clearly separate exploratory insights from preprocessing decisions

---

## 23. What are the ethical considerations in EDA?

Ethical EDA practices include:

* Avoiding biased interpretations of data
* Ensuring transparency in assumptions and decisions
* Respecting data privacy and confidentiality
* Clearly documenting data sources and limitations

EDA should not be used to manipulate results to support preconceived conclusions.

---

## 24. What is the scope of Exploratory Data Analysis?

The scope of EDA includes:

* Understanding data structure and quality
* Identifying patterns, trends, and anomalies
* Supporting informed preprocessing and modeling decisions

EDA is primarily diagnostic and descriptive in nature.

---

## 25. What are the limitations of EDA?

EDA has several limitations:

* It does not establish causality
* Findings may be subjective and analyst-dependent
* Visual interpretations may be misleading without statistical validation
* EDA alone cannot ensure model generalization or performance

---

## 26. Should outlier handling be done on the target variable during EDA?

Outlier handling on the target variable depends on the problem context and modeling objective. In general, target outliers should not be removed blindly during EDA.

* If the target outliers represent real and valid observations (e.g., high house prices, extreme sales, rare events), they should be retained, as removing them may bias the model and reduce real-world performance.
* If the outliers are caused by data entry errors, measurement issues, or noise, they can be corrected or removed after proper validation.
* In regression problems, extreme target values can heavily influence the model. In such cases, techniques like log transformation, winsorization, or robust models can be considered instead of deletion.
* In classification problems, outlier handling is usually applied to features, not the target, since class imbalance is addressed using different strategies (resampling, class weights).

Key takeaway:
Outliers in the target variable should be handled only after understanding the domain, data source, and business impact, and removal should be the last option, not the first.

---

## 27. How should EDA findings be documented in academic work?

EDA findings should be:

* Clearly structured and reproducible
* Supported by figures and summary statistics
* Explicit about assumptions and data limitations
* Aligned with academic and ethical standards

Proper documentation improves transparency and research credibility.

---

*End of document*
