**Correlation & Multicollinearity Analysis Guide**

## 1. Introduction

Correlation quantifies the **strength** and **direction** of the relationship between variables.  
Multicollinearity refers to high inter-correlations among predictor variables in a regression model, causing unstable and unreliable estimates.

**Key reminder**: Correlation ≠ causation. Always visualize your data.

## 2. Multicollinearity – Definition, Problems, Detection & Solutions

**What is multicollinearity?**  
High linear dependence among two or more independent (predictor) variables in a regression model (e.g., height, weight, and BMI in a health model).

**Why is it a problem?**  
- Inflated variance of regression coefficients → large standard errors  
- Unstable coefficient estimates (small change in data → large change in coefficients)  
- Coefficients difficult to interpret (shared explanatory power)  
- Unexpected signs or insignificant p-values for truly important variables  
- Poor generalizability and sensitivity to outliers

**How does the correlation matrix help detect it?**  
A correlation matrix (Pearson or Spearman) provides a quick pairwise overview.  
Look for absolute values **|r| > 0.7–0.8** as a red flag for potential multicollinearity.  
It is fast and intuitive but **incomplete** — it misses multicollinearity involving **more than two** variables.  
**VIF** is the gold-standard follow-up because it quantifies the combined effect of all other predictors on one variable.

**How to solve multicollinearity?**  
- Remove one or more highly correlated predictors (use VIF iteratively)  
- Combine variables (create indices, PCA, domain-knowledge features)  
- Regularization (Ridge/Lasso regression)  
- Collect more data or use centering/scaling  
- Partial least squares regression or principal component regression




**Illustration**: Example of correlated predictors (height, weight, BMI) and their impact on regression matrix plots.

## 3. Correlation Measures – Numerical / Continuous Data

| Method          | Measures                  | Range     | Assumptions                          | Robust to outliers? |
|-----------------|---------------------------|-----------|--------------------------------------|----------------------|
| Pearson         | Linear relationship       | -1 to +1  | Normality, linearity, homoscedasticity | No                   |
| Spearman        | Monotonic relationship    | -1 to +1  | None (rank-based)                    | Yes                  |
| Kendall τ       | Ordinal association       | -1 to +1  | None; good for small samples/ties    | Yes                  |

**Pearson formula**:

$$
r = \frac{\sum_{i=1}^{n} (x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum_{i=1}^{n} (x_i - \bar{x})^2 \sum_{i=1}^{n} (y_i - \bar{y})^2}}
$$

**Spearman formula**:

$$
\rho = 1 - \frac{6 \sum_{i=1}^{n} d_i^2}{n(n^2 - 1)}
$$

where $d_i$ = difference in ranks of corresponding values.

**Kendall τ (tau-a)**:

$$
\tau = \frac{2}{n(n-1)} (C - D)
$$

where $C$ = number of concordant pairs, $D$ = number of discordant pairs.

**Why visualization is essential** (Anscombe’s quartet & Datasaurus Dozen show datasets with identical correlation statistics but completely different patterns):


![Correlation Heatmap](https://github.com/sikaneka/EDA/blob/main/images/correlated%20predicters.png)






## 4. Categorical & Mixed-Type Associations

| Variable Types                        | Recommended Measure                  | Range     | Notes                                          |
|---------------------------------------|--------------------------------------|-----------|------------------------------------------------|
| Nominal × Nominal                     | Chi-square → Cramér's V              | 0 to 1    | Strength only (no direction)                   |
| Binary × Binary                       | Phi / Tetrachoric                    | -1 to +1  | Latent normality assumed for tetrachoric       |
| Ordinal × Ordinal                     | Polychoric / Spearman / Kendall      | -1 to +1  | Polychoric best for Likert-type data           |
| Binary × Continuous                   | Point-Biserial                       | -1 to +1  | Special case of Pearson                        |
| Ordinal × Continuous                  | Polyserial / Spearman                | -1 to +1  | Polyserial preferred under normality assumption|
| Multi-level Nominal × Continuous      | ANOVA → Eta-squared (η²)             | 0 to 1    | Proportion of variance explained               |

**Chi-square**:

$$
\chi^2 = \sum \frac{(O_{ij} - E_{ij})^2}{E_{ij}}
$$

**Cramér's V**:

$$
V = \sqrt{\frac{\chi^2}{n \cdot (k-1)}}
$$

**Visual examples**:

- Categorical vs Continuous → Box / violin plots:




- Categorical × Categorical → Mosaic plots:




## 5. Multicollinearity Detection: Variance Inflation Factor (VIF)

**Formula**:

$$
\mathrm{VIF}_j = \frac{1}{1 - R_j^2}
$$

where $R_j^2$ is from regressing $X_j$ on all other predictors.

**Interpretation**:

| VIF     | Severity                     | Action                                   |
|---------|------------------------------|------------------------------------------|
| 1       | None                         | Ideal                                    |
| < 5     | Moderate                     | Usually acceptable                       |
| 5–10    | High                         | Investigate / consider removal           |
| > 10    | Severe                       | Remove or combine strongly recommended   |

**Correlation heatmap** (pairs well with VIF):




## 6. Quick Decision Guide

- Two continuous → Pearson (linear) → Spearman/Kendall (monotonic)  
- Any ordinal → Spearman/Kendall/Polychoric  
- Any nominal → Chi-square + Cramér's V  
- Regression model → Correlation matrix + **VIF**


## 7. Common Pitfalls

- Relying solely on correlation matrix without VIF  
- Pearson on non-normal/non-linear data  
- Low expected counts in chi-square  
- Ignoring visualizations (Anscombe/Datasaurus)

License: MIT  
Contributions welcome!
