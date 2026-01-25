
# Encoding Categorical & Other Non-numeric Features  


## 0. Why do we need encoding at all?

Most machine learning algorithms (except tree-based models) cannot work directly with:

- strings → "Kerala", "Female", "High"
- categories with >2 levels → "Thrissur", "Kochi", "Kozhikode", …
- ordinal labels → "Low", "Medium", "High", "Very High"
- dates treated as strings
- rare/infrequent categories

Encoding = converting these into numbers (or number-like structures) that preserve as much useful information as possible while introducing as little noise/harm as possible.

## 1. Most Basic Approaches (Never skip understanding these)

| Method                  | When to use (very rough)                  | Pros                              | Cons / Dangers                              | sklearn / pandas function                     |
|-------------------------|--------------------------------------------|-----------------------------------|----------------------------------------------|-----------------------------------------------|
| Label Encoding          | Ordinal + tree models                      | Very fast, small memory           | Implies unwanted order, bad for non-trees    | `LabelEncoder`, `astype('category').cat.codes`|
| One-Hot Encoding        | Nominal < ~15–25 categories, linear models | No artificial order                | Curse of dimensionality, memory explosion    | `pd.get_dummies`, `OneHotEncoder`             |
| Binary Encoding         | Nominal 15–100 categories                  | Compact (log₂ categories columns) | Still somewhat arbitrary                     | `category_encoders.BinaryEncoder`             |

### Quick comparison example – 50 unique districts

```python
# One-hot     → 50 columns
# Binary      → 6 columns  (2⁶ = 64 > 50)
# Label       → 1 column   (but dangerous order)
# Target / MTE→ 1 column   (but leakage risk)
```

## 2. Label Encoding – Variants & Gotchas

```python
# Pure alphabetical (almost never what you want)
df['district'] = df['district'].astype('category').cat.codes

# Custom order – ordinal
education_map = {'Illiterate':0, 'Primary':1, 'Secondary':2, 'Higher':3, 'PG':4, 'PhD':5}
df['education_rank'] = df['education'].map(education_map)
```

**2025 best practice tip**  
Use `sklearn.preprocessing.OrdinalEncoder` when you want to preserve categories in the fitted object (useful in pipelines).

## 3. One-Hot → The realistic limits in 2025

```python
# Reasonable today
OneHotEncoder(max_categories=30, handle_unknown='infrequent_if_exist', min_frequency=0.005, sparse_output=False)

# Alternative memory-friendly pattern (pandas style)
df_encoded = pd.get_dummies(df, columns=['district','occupation'],
                            prefix=['dist','occ'],
                            drop_first=False,        # usually keep all for tree models
                            dtype='int8')            # ← important in 2025
```

**Rule of thumb 2025**

| Dataset rows | Max one-hot categories (reasonable) | Recommendation                              |
|--------------|--------------------------------------|---------------------------------------------|
| <  10k       | ~40–60                               | One-hot almost always fine                  |
| 10k–100k     | ~25–40                               | One-hot + rare grouping                     |
| 100k–1M      | ~12–25                               | One-hot only very important features        |
| > 1M         | ≤ 12                                 | Target / MTE / embeddings / hashing         |

## 4. Grouping Rare Categories (very important in real life)

```python
def group_rare_categories(s: pd.Series, min_count=50, replace_with='Other') -> pd.Series:
    counts = s.value_counts()
    mask = s.isin(counts[counts >= min_count].index)
    return s.where(mask, replace_with)

df['state_grouped'] = group_rare_categories(df['state'], min_count=100)
```

Modern libraries often do this automatically:

- `OneHotEncoder(min_frequency=0.01)`
- `TargetEncoder(min_samples_leaf=20)`

## 5. Target / Mean / Posterior Encoding Family (very powerful, but leakage risk)

Family names you will see:

- Target Encoding
- Mean Encoding
- M-Estimate Encoding
- Leave-One-Out Encoding
- James-Stein Encoding
- Additive Smoothing Encoding

```python
from category_encoders import TargetEncoder, MEstimateEncoder, LeaveOneOutEncoder

encoder = TargetEncoder(cols=['district','occupation'],
                        smoothing=10.0,      # very important hyperparameter
                        min_samples_leaf=20)

# Important patterns in 2025
X_train_enc = encoder.fit_transform(X_train, y_train)
X_valid_enc = encoder.transform(X_valid)          # ← no leakage
```

**Smoothing values quick cheat-sheet**

| Situation                        | Suggested smoothing |
|----------------------------------|----------------------|
| Very noisy target (CTR, fraud)   | 20–100              |
| Clean regression target          | 5–30                |
| Very large data (> 5M rows)      | 1–10                |
| Small data + many categories     | 50–300              |

## 6. Frequency / Count Encoding (simple but surprisingly useful)

```python
freq = df['pincode'].value_counts(normalize=True)
df['pincode_freq'] = df['pincode'].map(freq)

# or log version (helps with very skewed distribution)
df['pincode_log_freq'] = np.log1p(df['pincode'].map(df['pincode'].value_counts()))
```

## 7. Advanced / Situational Encodings (2024–2026 EDA toolkit)

| Encoding type              | Best used when                                      | Library / implementation                     | Approx. columns added |
|----------------------------|------------------------------------------------------|----------------------------------------------|------------------------|
| Hashing Encoder            | > 500–1000 categories, memory tight                 | `category_encoders.HashingEncoder`           | fixed (10–32)         |
| WOE (Weight of Evidence)   | Credit risk, binary classification                  | `category_encoders.WOEEncoder`               | 1 per column          |
| IV-based feature selection | Quick filtering before modeling                     | custom + `score_iv` functions                | —                     |
| CatBoost native encoding   | You are going to use CatBoost anyway                | CatBoost built-in (no manual encoding needed) | —                     |
| Target-guided PCA / UMAP   | Very high-cardinality + want 2–10 dense features    | custom pipeline                              | 2–16                  |
| Entity Embeddings          | Deep learning or very high-cardinality nominal      | PyTorch / Keras embedding layer              | embedding_dim         |

## 8. Quick Decision Flow (copy-paste friendly)

```text
For each categorical column ask yourself:

1. Is it ordinal? 
   → OrdinalEncoder / manual mapping

2. Cardinality ≤ 15–20 and rows < 200k ?
   → OneHot (drop_first=False for trees)

3. Cardinality 20–150 and regression / binary target ?
   → TargetEncoder or MEstimateEncoder (smoothing 10–50)

4. Cardinality > 150 ?
   → Rare grouping → Target / Freq / Hashing

5. Doing deep learning or tabular DL (TabNet, FT-Transformer, …) ?
   → Entity embeddings or leave almost as is + CatBoost-style encoding

6. Using only tree models (LightGBM/XGB/CatBoost/HistGradient) ?
   → You can usually just use raw categories (they handle categorical natively)
```

Happy EDA! 🧹📊
