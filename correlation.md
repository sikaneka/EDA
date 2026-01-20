# Correlation Analysis Guide

## Introduction

Correlation quantifies the strength and direction of the relationship between two (or more) variables. The coefficient typically ranges from **−1** to **+1**:

- **+1** → perfect positive relationship  
- **0**  → no (linear/monotonic) relationship  
- **−1** → perfect negative relationship  

Correlation is a fundamental tool in exploratory data analysis, feature selection, multicollinearity detection, and hypothesis generation — but **correlation ≠ causation**.

This README covers the most commonly used correlation measures for different data types:  
- Continuous / numeric  
- Ordinal  
- Nominal / categorical  
- Mixed-type combinations  

## Correlation Measures by Data Type

### 1. Continuous × Continuous (Numeric)

| Method              | Measures              | Range     | Parametric? | Robust to outliers? | Best for                              |
|---------------------|-----------------------|-----------|-------------|----------------------|---------------------------------------|
| **Pearson**         | Linear relationship   | -1 to +1  | Yes         | No                   | Normally distributed, linear patterns |
| **Spearman**        | Monotonic relationship| -1 to +1  | No          | Yes                  | Non-normal, monotonic, ranks          |
| **Kendall τ**       | Ordinal association   | -1 to +1  | No          | Yes                  | Small samples, many ties              |

**Pearson formula** (product-moment):

$$
r = \frac{\sum (x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum (x_i - \bar{x})^2 \sum (y_i - \bar{y})^2}}
$$

**Spearman formula** (rank-based):

$$
\rho = 1 - \frac{6 \sum d_i^2}{n(n^2 - 1)}
$$

### 2. Categorical & Mixed-Type Data

When at least one variable is **categorical** (nominal or ordinal with few levels), standard Pearson/Spearman/Kendall are usually inappropriate.

| Data Combination                     | Recommended Method(s)              | Range       | Best For / Typical Use Case                                 |
|--------------------------------------|------------------------------------|-------------|-------------------------------------------------------------|
| Nominal × Nominal                    | Cramér's V                         | 0 to 1      | Gender × product category, city × blood type                |
| Binary × Binary                      | Phi / Tetrachoric                  | -1 to +1    | Disease present/absent × test result                        |
| Ordinal × Ordinal                    | Polychoric / Spearman / Kendall    | -1 to +1    | Likert scale × education level (low/med/high)               |
| Binary × Continuous                  | Point-Biserial                     | -1 to +1    | Gender (0/1) × salary, survived (0/1) × age                 |
| Ordinal × Continuous                 | Polyserial / Spearman              | -1 to +1    | Satisfaction (1–5) × income                                 |
| Multi-level Nominal × Continuous     | ANOVA + Eta-squared (η²)           | 0 to 1      | Region (4 levels) × house price                             |
| Nominal × Nominal (large table)      | Theil's U / Uncertainty Coefficient| 0 to 1      | Asymmetric association (one variable predicts the other)    |

**Cramér's V formula**:

$$
V = \sqrt{\frac{\chi^2}{n \cdot (k-1)}}
$$

where $k = \min(\text{rows}, \text{columns})$, $\chi^2$ from chi-square test.

**Rough interpretation for Cramér's V**:
- < 0.1  → negligible  
- 0.1–0.3 → weak  
- 0.3–0.5 → moderate  
- > 0.5   → strong  

### 3. Quick Reference – Which Method for Which Data?

| You have →                  | And →                        | Use primarily                     | Alternative / Notes                               |
|-----------------------------|------------------------------|-----------------------------------|---------------------------------------------------|
| Two continuous              | —                            | Pearson (if linear & normal)      | Spearman / Kendall if non-normal or monotonic     |
| One or both ordinal         | —                            | Spearman / Kendall                | Polychoric if assuming latent normality           |
| Both nominal                | —                            | Cramér's V                        | Phi (2×2), Theil's U (asymmetric)                 |
| Binary (0/1)                | Continuous                   | Point-Biserial                    | Just use Pearson after coding 0/1                 |
| Ordinal (≥3 ordered levels) | Continuous                   | Polyserial (preferred)            | Or treat as numeric → Spearman                    |
| Nominal (≥3 unordered)      | Continuous                   | ANOVA → η²                        | Or correlation ratio (η if nominal predicts num)  |

**Golden rule**:  
If either variable is **nominal** (no natural order), do **not** use Pearson/Spearman/Kendall directly — prefer Cramér's V, chi-square-based measures, or group-comparison statistics.

## Python Examples – All Major Cases

```python
import pandas as pd
import pingouin as pg
from scipy.stats import pearsonr, spearmanr, pointbiserialr
from dython.nominal import associations, correlation_ratio

df = pd.read_csv("your_data.csv")  # example dataset

# 1. Numeric × Numeric
print("Pearson:", pearsonr(df["age"], df["income"]))
print("Spearman:", spearmanr(df["age"], df["spending_score"]))

# 2. Binary × Numeric → Point-Biserial
print("Point-Biserial:", pointbiserialr(df["gender_binary"], df["salary"]))

# 3. Ordinal × Ordinal → Polychoric (pingouin)
print("Polychoric:", pg.polychoric(df["satisfaction_1to5"], df["education_ordinal"]))

# 4. Nominal × Nominal → Cramér's V & full matrix (dython is excellent)
associations(df, 
             nominal_columns=['gender', 'region', 'product_category'],
             filename='associations_heatmap.png')

# 5. Nominal → Numeric (correlation ratio / eta-like)
print("Correlation Ratio (region → income):", 
      correlation_ratio(df["region"], df["income"]))
