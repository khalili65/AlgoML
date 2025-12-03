# Regression: Complete Interview Guide

> Predicting continuous values by modeling the relationship between dependent and independent variables.

---

## 📊 Overview

**Regression** is a supervised learning technique used to predict a **continuous target variable** based on one or more predictor variables.

```
┌─────────────────────────────────────────────────────────────┐
│                    REGRESSION TYPES                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Simple Linear        →  y = β₀ + β₁x                       │
│  Multiple Linear      →  y = β₀ + β₁x₁ + β₂x₂ + ... + βₙxₙ  │
│  Polynomial           →  y = β₀ + β₁x + β₂x² + ... + βₙxⁿ   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 📈 Simple Linear Regression

### The Model

```
y = β₀ + β₁x + ε
```

| Symbol | Meaning |
|--------|---------|
| y | Dependent variable (target) |
| x | Independent variable (predictor) |
| β₀ | Y-intercept (value of y when x = 0) |
| β₁ | Slope (change in y for unit change in x) |
| ε | Error term (residual) |

### Goal

Find the **best-fitting line** that minimizes the **sum of squared differences** between actual and predicted values.

```
Minimize: Σ(yᵢ - ŷᵢ)² = Σ(yᵢ - (β₀ + β₁xᵢ))²
```

### Visual Intuition

```
  y
  ↑
  │       •    
  │    •    •   ←── Actual points
  │  •  ────────── ←── Best fit line (ŷ = β₀ + β₁x)
  │•     •
  │   •
  └─────────────────→ x
```

---

## 📐 Multiple Linear Regression

### The Model

When you have **multiple predictor variables**:

```
y = β₀ + β₁x₁ + β₂x₂ + ... + βₙxₙ + ε
```

### Example: House Price Prediction

```
Price = β₀ + β₁(Size) + β₂(Bedrooms) + β₃(Age) + ε
```

Each coefficient βᵢ represents the **change in y** for a **one-unit change in xᵢ**, holding all other variables constant.

### Matrix Form

```
y = Xβ + ε

Where:
  y = [n × 1] target vector
  X = [n × (p+1)] design matrix (with intercept column of 1s)
  β = [(p+1) × 1] coefficient vector
  ε = [n × 1] error vector
```

### Solving for β (Ordinary Least Squares)

```
β = (XᵀX)⁻¹Xᵀy
```

---

## 🎯 R-Squared (R²): Coefficient of Determination

### Definition

> **R²** measures the **proportion of variance** in the target variable that is **explained by the predictor variables**.

```
         Unexplained Variance       SSR        Σ(yᵢ - ŷᵢ)²
R² = 1 - ──────────────────── = 1 - ──── = 1 - ────────────
           Total Variance          SST        Σ(yᵢ - ȳ)²
```

### Components

| Term | Name | Formula | Meaning |
|------|------|---------|---------|
| **SST** | Total Sum of Squares | Σ(yᵢ - ȳ)² | Total variance in y |
| **SSR** | Residual Sum of Squares | Σ(yᵢ - ŷᵢ)² | Unexplained variance |
| **SSE** | Explained Sum of Squares | Σ(ŷᵢ - ȳ)² | Explained variance |

**Relationship:** SST = SSE + SSR

### Interpretation

| R² Value | Interpretation |
|----------|----------------|
| 0.0 | Model explains **none** of the variance |
| 0.5 | Model explains **50%** of the variance |
| 0.8 | Model explains **80%** of the variance |
| 1.0 | Model explains **all** of the variance (perfect fit) |

### Concrete Example: Student Study Hours

```
Students studying for a test:

Student  Hours(x)  Actual Score(y)  Predicted(ŷ)
───────  ────────  ───────────────  ────────────
   A        1           70              71
   B        2           75              76
   C        3           80              81
   D        4           85              86
   E        5           90              91

Mean actual score (ȳ) = 80

Total Variance (SST):
  = (70-80)² + (75-80)² + (80-80)² + (85-80)² + (90-80)²
  = 100 + 25 + 0 + 25 + 100 = 250

Residual Variance (SSR):
  = (70-71)² + (75-76)² + (80-81)² + (85-86)² + (90-91)²
  = 1 + 1 + 1 + 1 + 1 = 5

R² = 1 - (5/250) = 1 - 0.02 = 0.98

→ 98% of variance in scores is explained by hours studied!
```

### Visual Intuition

```
┌─────────────────────────────────────────────────────────────┐
│                    UNDERSTANDING R²                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Total Variance (SST) = What we're trying to explain        │
│  ├── Explained by model (SSE) ────→ This is GOOD ✓          │
│  └── Unexplained/Residual (SSR) ──→ This is ERROR ✗         │
│                                                             │
│  R² = SSE/SST = Explained/Total = How much model explains   │
│                                                             │
│  High R² → Model captures most variance → Good fit          │
│  Low R²  → Model misses most variance  → Poor fit           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## ⚖️ Adjusted R-Squared

### The Problem with R²

R² **always increases** (or stays the same) when you add more predictors, even if they're useless!

```
Add irrelevant feature → R² goes up → Misleading!
```

### The Solution: Adjusted R²

**Adjusted R²** penalizes for adding predictors that don't improve the model significantly.

```
                         (1 - R²)(n - 1)
Adjusted R² = 1 - ───────────────────────
                        n - k - 1

Where:
  n = number of samples
  k = number of predictors
```

### Comparison

| Metric | Behavior | When to Use |
|--------|----------|-------------|
| **R²** | Always increases with more features | Single model evaluation |
| **Adjusted R²** | Can decrease if feature isn't useful | Comparing models with different # of features |

### Example

```
Model A: 3 features,  R² = 0.85,  Adjusted R² = 0.83
Model B: 10 features, R² = 0.87,  Adjusted R² = 0.79

→ Model A is better! Extra features in B don't help much.
```

---

## 📏 Evaluation Metrics

### Mean Squared Error (MSE)

```
         1
MSE = ─────  Σ(yᵢ - ŷᵢ)²
         n
```

- Average squared difference between actual and predicted
- **Lower is better**
- Units are squared (e.g., dollars² for price)

### Root Mean Squared Error (RMSE)

```
RMSE = √MSE = √[ (1/n) Σ(yᵢ - ŷᵢ)² ]
```

- Same units as target variable
- **Lower is better**
- More interpretable than MSE

### Mean Absolute Error (MAE)

```
         1
MAE = ─────  Σ|yᵢ - ŷᵢ|
         n
```

- Less sensitive to outliers than MSE/RMSE
- **Lower is better**

### Comparison

| Metric | Outlier Sensitivity | Interpretation |
|--------|---------------------|----------------|
| MSE | High (squares errors) | Average squared error |
| RMSE | High | Average error in original units |
| MAE | Low (absolute value) | Average absolute error |
| R² | Moderate | Proportion of variance explained |

---

## 🔢 Handling Categorical Variables: Dummy Variables

### The Problem

Regression requires **numerical** inputs, but categorical variables are not numerical.

```
City: "New York", "Los Angeles", "Chicago"  ← Can't use directly!
```

### The Solution: One-Hot Encoding (Dummy Variables)

Create **binary columns** for each category:

```
Original:                    Dummy Encoded:
┌──────────────┐            ┌────────────┬─────────────┬─────────┐
│    City      │            │ City_NYC   │ City_LA     │ City_CHI│
├──────────────┤            ├────────────┼─────────────┼─────────┤
│ New York     │     →      │     1      │      0      │    0    │
│ Los Angeles  │            │     0      │      1      │    0    │
│ Chicago      │            │     0      │      0      │    1    │
│ New York     │            │     1      │      0      │    0    │
└──────────────┘            └────────────┴─────────────┴─────────┘
```

### The Dummy Variable Trap

> **Important:** Use **n-1 dummy variables** for n categories to avoid multicollinearity!

```python
# WRONG: Creates multicollinearity
pd.get_dummies(df['City'])  # 3 columns for 3 cities

# CORRECT: Drop one category (reference category)
pd.get_dummies(df['City'], drop_first=True)  # 2 columns for 3 cities
```

### Why Drop One?

If you know City_NYC=0 and City_LA=0, then City_CHI **must** be 1.
The dropped category becomes the **reference/baseline** category.

---

## 📈 Polynomial Regression

### When Linear Isn't Enough

Sometimes the relationship is **non-linear**:

```
Linear: y = β₀ + β₁x              (straight line)
Quadratic: y = β₀ + β₁x + β₂x²    (parabola)
Cubic: y = β₀ + β₁x + β₂x² + β₃x³ (S-curve)
```

### Visual Example

```
  y                           y
  ↑      Linear               ↑     Polynomial (degree 2)
  │    ────────               │         ╱╲
  │   ╱                       │        ╱  ╲
  │  ╱   • • •                │   •  •╱    ╲•  •
  │ ╱  •     •                │  •   ╱      ╲   •
  │╱ •         •              │    •          •
  └─────────────→ x           └─────────────────→ x
     Poor fit!                     Better fit!
```

### Implementation

```python
from sklearn.preprocessing import PolynomialFeatures
from sklearn.linear_model import LinearRegression

# Transform features to include polynomial terms
poly = PolynomialFeatures(degree=2, include_bias=False)
X_poly = poly.fit_transform(X)

# X = [[2], [3], [4]]
# X_poly = [[2, 4], [3, 9], [4, 16]]  ← includes x and x²

# Fit linear regression on polynomial features
model = LinearRegression()
model.fit(X_poly, y)
```

### Choosing the Degree

| Degree | Risk |
|--------|------|
| Too low | Underfitting (high bias) |
| Too high | Overfitting (high variance) |

Use **cross-validation** to find optimal degree!

---

## 🔬 Assumptions of Linear Regression

### The 5 Key Assumptions

| # | Assumption | Meaning | How to Check |
|---|------------|---------|--------------|
| 1 | **Linearity** | Relationship is linear | Residual vs fitted plot |
| 2 | **Independence** | Observations are independent | Durbin-Watson test |
| 3 | **Homoscedasticity** | Constant variance of residuals | Residual plot (no funnel shape) |
| 4 | **Normality** | Residuals are normally distributed | Q-Q plot, Shapiro-Wilk test |
| 5 | **No Multicollinearity** | Predictors aren't highly correlated | VIF (Variance Inflation Factor) |

### Visual: Residual Diagnostics

```
GOOD Residual Plot          BAD Residual Plot (Heteroscedasticity)
       •                           •    •  •
    •     •                              •    •   •
  •    •    •                     •         •       •
──────────────── 0              ─────────────────────── 0
    •  •   •                    •  •
      •                         •
  Random scatter!               Funnel shape = problem!
```

---

## 🛠️ Implementation in Python

### Using Scikit-Learn

```python
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Train model
model = LinearRegression()
model.fit(X_train, y_train)

# Predict
y_pred = model.predict(X_test)

# Evaluate
print(f"R²: {r2_score(y_test, y_pred):.4f}")
print(f"RMSE: {np.sqrt(mean_squared_error(y_test, y_pred)):.4f}")
print(f"Coefficients: {model.coef_}")
print(f"Intercept: {model.intercept_}")
```

### Using Statsmodels (for p-values!)

```python
import statsmodels.api as sm

# Add constant for intercept
X_const = sm.add_constant(X)

# Fit OLS model
model = sm.OLS(y, X_const).fit()

# Get summary with p-values, confidence intervals, etc.
print(model.summary())

# Output includes:
# - R², Adjusted R²
# - F-statistic and its p-value
# - Coefficient p-values (is each predictor significant?)
# - Confidence intervals
```

### Sample Statsmodels Output

```
                            OLS Regression Results
==============================================================================
Dep. Variable:                  price   R-squared:                       0.850
Model:                            OLS   Adj. R-squared:                  0.845
                                        F-statistic:                     156.2
                                        Prob (F-statistic):           1.23e-45
==============================================================================
                 coef    std err          t      P>|t|      [0.025      0.975]
------------------------------------------------------------------------------
const       12500.00    1200.00     10.417      0.000    10100.00   14900.00
size          150.50      12.30     12.236      0.000      126.20     174.80
bedrooms     5000.00    1500.00      3.333      0.001     2050.00    7950.00
==============================================================================
```

**Interpretation:**
- `P>|t|` < 0.05 means the predictor is **statistically significant**
- `size` coefficient 150.50: Each sq ft adds $150.50 to price
- `bedrooms` coefficient 5000: Each bedroom adds $5000 to price

---

## 🔄 Regularization in Regression

### Why Regularize?

Prevent overfitting by **penalizing large coefficients**.

### Ridge Regression (L2)

```
Minimize: Σ(yᵢ - ŷᵢ)² + λ Σβⱼ²

- Shrinks coefficients toward zero
- Keeps all features
- Good for multicollinearity
```

### Lasso Regression (L1)

```
Minimize: Σ(yᵢ - ŷᵢ)² + λ Σ|βⱼ|

- Can shrink coefficients to exactly zero
- Performs feature selection
- Good for sparse models
```

### Elastic Net

```
Minimize: Σ(yᵢ - ŷᵢ)² + λ₁ Σ|βⱼ| + λ₂ Σβⱼ²

- Combines L1 and L2
- Best of both worlds
```

### Comparison

| Method | Penalty | Feature Selection | Best For |
|--------|---------|-------------------|----------|
| **OLS** | None | No | Interpretability |
| **Ridge** | L2 (β²) | No | Multicollinearity |
| **Lasso** | L1 (\|β\|) | Yes | Feature selection |
| **Elastic Net** | L1 + L2 | Yes | Many correlated features |

---

## 💡 Interview Tips

1. **Know the formulas** — R², Adjusted R², MSE, RMSE
2. **Explain assumptions** — linearity, independence, homoscedasticity, normality, no multicollinearity
3. **Understand R² limitations** — doesn't indicate model correctness, can be misleading
4. **Discuss regularization** — when and why to use Ridge vs Lasso
5. **Know dummy variable trap** — why drop one category
6. **Connect to bias-variance** — simple model = high bias, complex = high variance

---

## ❓ Common Interview Questions

| Question | Key Points |
|----------|------------|
| What is R²? | Proportion of variance explained; 0 to 1 (usually) |
| Can R² be negative? | Yes! If model is worse than predicting mean |
| R² vs Adjusted R²? | Adjusted penalizes for adding useless predictors |
| What are regression assumptions? | Linearity, independence, homoscedasticity, normality, no multicollinearity |
| How to handle categorical variables? | Dummy encoding, drop one category |
| When to use Ridge vs Lasso? | Ridge: multicollinearity. Lasso: feature selection |
| What is multicollinearity? | Predictors highly correlated, unstable coefficients |
| How to detect overfitting? | Compare train vs test performance, use cross-validation |
| What does the coefficient mean? | Change in y for one-unit change in x (holding others constant) |
| How to interpret p-values in regression? | Predictor significant if p < 0.05 |

---

## 🔗 Related Concepts

- **Bias-Variance Tradeoff** — Model complexity trade-off
- **Cross-Validation** — Model evaluation technique
- **Feature Engineering** — Creating meaningful predictors
- **Gradient Descent** — Optimization algorithm
- **Logistic Regression** — Classification (not regression!)

---

## 🔬 Quick Reference

```
┌─────────────────────────────────────────────────────────────┐
│                   REGRESSION SUMMARY                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Simple Linear:    y = β₀ + β₁x                             │
│  Multiple Linear:  y = β₀ + β₁x₁ + β₂x₂ + ... + βₙxₙ        │
│                                                             │
│  R² = 1 - (SSR/SST) = 1 - Σ(y-ŷ)² / Σ(y-ȳ)²                 │
│                                                             │
│  Adjusted R² = 1 - [(1-R²)(n-1)] / (n-k-1)                  │
│                                                             │
│  Key Metrics:                                               │
│    MSE  = (1/n) Σ(y-ŷ)²                                     │
│    RMSE = √MSE                                              │
│    MAE  = (1/n) Σ|y-ŷ|                                      │
│                                                             │
│  Regularization:                                            │
│    Ridge (L2): Shrinks coefficients                         │
│    Lasso (L1): Feature selection                            │
│                                                             │
│  Assumptions: Linear, Independent, Homoscedastic,           │
│               Normal residuals, No multicollinearity        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

