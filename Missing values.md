# Handling Missing Values in Exploratory Data Analysis (EDA)

## Introduction

Missing values are one of the most common issues encountered when working with real-world datasets. They appear when data is not recorded for a particular observation and variable — often shown as `NaN`, `None`, `null`, empty strings, or special codes like -999.

Failing to address missing values properly can lead to:
- Incorrect statistical summaries
- Biased model training
- Errors during computation
- Misleading visualizations and insights

This section explains how to identify, understand, and handle missing values effectively during EDA.

## Types of Missing Data (Mechanisms)

The way data becomes missing affects which handling methods are appropriate. There are three main categories:

1. **Missing Completely at Random (MCAR)**  
   The probability of missingness is the same for all observations — unrelated to observed or unobserved data.  
   Example: A randomly dropped survey page, sensor malfunction at random times.

2. **Missing at Random (MAR)**  
   The probability of missingness depends on observed variables, but not on the missing value itself.  
   Example: Younger people are less likely to report income (age is observed), but within the same age group, reporting is random.

3. **Missing Not at Random (MNAR)**  
   The probability of missingness depends on the value that is missing.  
   Example: People with very high or very low income refuse to answer salary questions.

Most real datasets fall somewhere between MAR and MNAR. The mechanism is rarely known with certainty, but domain knowledge and patterns in the data help make reasonable assumptions.

## Detecting Missing Values

### Basic Summary Statistics

```python
import pandas as pd

# Count missing values per column
df.isnull().sum()

# Percentage of missing values
df.isnull().mean() * 100

# Total missing values in the dataset
df.isnull().sum().sum()

# Rows with at least one missing value
df.isnull().any(axis=1).sum()
```

### Useful Visualizations

```python
# Recommended library for missing value patterns
import missingno as msno
import matplotlib.pyplot as plt

# Bar plot — shows count of non-missing values per column
msno.bar(df)
plt.show()

# Matrix — visualizes missingness pattern across rows
msno.matrix(df)
plt.show()

# Heatmap — correlation between missingness in different columns
msno.heatmap(df)
plt.show()

# Dendrogram — hierarchical clustering of variables by missingness similarity
msno.dendrogram(df)
plt.show()
```

These plots quickly reveal:
- Which columns have the most missing data
- Whether missingness occurs in blocks (rows with many missings)
- Whether columns have correlated missing patterns

## Strategies for Handling Missing Values

No single method is best in all situations. The choice depends on:
- Proportion of missing data
- Type of variable (numeric / categorical / time-series)
- Assumed missingness mechanism
- Downstream task (visualization, modeling, etc.)
- Domain knowledge

### 1. Deletion Approaches

- **Listwise deletion** (drop rows)  
  ```python
  df.dropna()                    # drop any row with ≥1 missing
  df.dropna(how='all')           # drop only if all values missing
  df.dropna(subset=['col1','col2'])  # drop if specific columns missing
  ```
  → Use when missing % is very low (< 2–5%) and MCAR is plausible.

- **Pairwise deletion**  
  Many pandas/statistical functions do this automatically for correlations, means, etc.

- **Column deletion**  
  ```python
  df.drop(columns=['col_with_80%_missing'])
  ```
  → Common when a column has >60–80% missing and is not critical.

### 2. Simple / Single Imputation

| Variable Type   | Common Strategies                              | Code Example                                      |
|-----------------|------------------------------------------------|---------------------------------------------------|
| Numeric         | Mean                                           | `df['age'].fillna(df['age'].mean(), inplace=True)` |
| Numeric (skewed/outliers) | Median                                 | `df['income'].fillna(df['income'].median(), ...)` |
| Categorical     | Mode (most frequent value)                     | `df['gender'].fillna(df['gender'].mode()[0], ...)` |
| Categorical     | New category: 'Missing' / 'Unknown'            | `df['education'].fillna('Missing', inplace=True)` |
| Any             | Constant (0, -1, 'other', etc.)                | `df['has_children'].fillna(0, inplace=True)`      |

→ Fast and simple, but underestimates variability and can distort relationships.

### 3. More Sophisticated Imputation

- **K-Nearest Neighbors Imputation**  
  Uses similarity between rows to fill values.  
  ```python
  from sklearn.impute import KNNImputer

  imputer = KNNImputer(n_neighbors=5)
  df_numeric = pd.DataFrame(
      imputer.fit_transform(df.select_dtypes(include='number')),
      columns=df.select_dtypes(include='number').columns,
      index=df.index
  )
  ```

- **Iterative / Multivariate Imputation (MICE-style)**  
  Models each variable with missing values using the others.  
  ```python
  from sklearn.experimental import enable_iterative_imputer
  from sklearn.impute import IterativeImputer

  imputer = IterativeImputer(
      random_state=42,
      max_iter=10,
      initial_strategy='median'
  )
  df_imputed = pd.DataFrame(
      imputer.fit_transform(df),
      columns=df.columns,
      index=df.index
  )
  ```

- **Time Series Specific**  
  ```python
  df['sales'].fillna(method='ffill')          # forward fill
  df['sales'].fillna(method='bfill')          # backward fill
  df['temperature'].interpolate(method='linear')
  df['temperature'].interpolate(method='spline', order=2)
  ```

### 4. Indicator / Missingness Pattern Approach

Create a binary column that flags whether the value was originally missing.  
```python
df['age_missing'] = df['age'].isnull().astype(int)
df['age'].fillna(df['age'].median(), inplace=True)
```

→ Lets models learn if missingness itself carries information (especially useful when MNAR is suspected).

## Quick Decision Guide

| Situation                              | Suggested Approach                                 |
|----------------------------------------|-----------------------------------------------------|
| < 5% missing overall, appears random   | Listwise deletion or simple mean/median/mode        |
| 5–30% missing, numeric, no strong pattern | Median + KNN or IterativeImputer                    |
| High missing in one column (>60%)      | Drop column (unless domain-critical)                |
| Categorical variable                   | Mode or add 'Missing' category                      |
| Time series / ordered data             | Interpolation, forward/backward fill                |
| Missingness seems informative          | Missing indicator + imputation                      |
| Preparing for tree-based models        | Sometimes leave NaN (XGBoost, LightGBM, CatBoost handle natively) |

## Best Practices During EDA

- Always report missingness statistics early in your analysis/notebook
- Visualize patterns before and after imputation
- Compare distributions (histograms, boxplots) before vs. after imputation
- Avoid imputing before train-test split → can cause data leakage
- Document every imputation choice and reasoning
- When in doubt, try multiple strategies and compare downstream results

## References & Further Reading

- Little, R. J. A., & Rubin, D. B. (2019). *Statistical Analysis with Missing Data*
- `missingno` documentation: https://github.com/ResidentMario/missingno
- Scikit-learn imputation: https://scikit-learn.org/stable/modules/impute.html

This file is part of the EDA repository.  
See also: [Introduction.md](README.md), [Correlation.md](correlation.md), [Scaling.md](Scaling.md).  

Happy exploring!
