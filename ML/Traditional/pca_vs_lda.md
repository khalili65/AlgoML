# PCA vs LDA: Dimensionality Reduction

> Two fundamental techniques for reducing dimensions, but with very different objectives.

---

## 📊 Overview

| Aspect | PCA | LDA |
|--------|-----|-----|
| **Full Name** | Principal Component Analysis | Linear Discriminant Analysis |
| **Type** | Unsupervised | Supervised |
| **Goal** | Maximize variance | Maximize class separability |
| **Uses Labels?** | ❌ No | ✅ Yes |
| **Best For** | Signal representation, compression | Classification tasks |

---

## 🎯 The Key Difference

```
┌─────────────────────────────────────────────────────────────┐
│                    PCA vs LDA VISUALIZATION                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Feature 2                                                  │
│     ↑            2 2                                        │
│     │          2 2 2 2                                      │
│     │    1 1 1   2 2 2 2                                    │
│     │  1 1 1 1 1   2 2 2                                    │
│     │    1 1 1 1     2 2 2                                  │
│     │      1 1 1 1     2 2                                  │
│     │        1 1 1       2                                  │
│     │          1 1 1                                        │
│     └──────────────────────────→ Feature 1                  │
│                                                             │
│   PCA Direction: / (maximum variance - ignores classes)     │
│   LDA Direction: \ (maximum class separation)               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**PCA** finds directions of **maximum variance** in the data — it doesn't care about class labels.

**LDA** finds directions that **best separate classes** — it explicitly uses class labels.

---

## 🔵 PCA: Principal Component Analysis

### What It Does

PCA finds new axes (principal components) that:
1. Are **orthogonal** to each other
2. Capture the **maximum variance** in the data
3. Are ordered by the amount of variance explained

### Mathematical Objective

```
Maximize: Var(Xw) = w^T Σ w

Subject to: ||w|| = 1
```

Where `Σ` is the covariance matrix of the data.

### Algorithm Steps

1. **Standardize** the data (mean=0, std=1)
2. Compute the **covariance matrix**
3. Calculate **eigenvalues and eigenvectors**
4. Sort eigenvectors by eigenvalues (descending)
5. Select top `k` eigenvectors
6. **Transform** data to new subspace

### When to Use PCA

✅ **Use PCA when:**
- You want to reduce dimensions for **any task** (not just classification)
- You don't have labels
- You want to **visualize** high-dimensional data
- You need **data compression**
- You want to remove **multicollinearity**

### Key Properties

| Property | Description |
|----------|-------------|
| **Unsupervised** | Doesn't need class labels |
| **Variance-based** | Preserves directions with most spread |
| **Orthogonal** | Components are uncorrelated |
| **Linear** | Finds linear combinations of features |
| **Max Components** | min(n_samples, n_features) |

---

## 🔴 LDA: Linear Discriminant Analysis

### What It Does

LDA finds new axes that:
1. **Maximize** the distance between class means
2. **Minimize** the variance within each class
3. Optimize for **class separability**

### Mathematical Objective

```
                    w^T S_B w
Maximize: J(w) = ─────────────
                    w^T S_W w
```

Where:
- `S_B` = **Between-class** scatter matrix
- `S_W` = **Within-class** scatter matrix

### Algorithm Steps

1. Compute the **mean** of each class
2. Compute **within-class scatter matrix** S_W
3. Compute **between-class scatter matrix** S_B
4. Solve generalized eigenvalue problem: `S_B w = λ S_W w`
5. Select top `k` eigenvectors
6. **Transform** data to new subspace

### When to Use LDA

✅ **Use LDA when:**
- You have a **classification** task
- You have **labeled data**
- You want features optimized for **class separation**
- You want dimensionality reduction that **improves classification**

### Key Properties

| Property | Description |
|----------|-------------|
| **Supervised** | Requires class labels |
| **Separability-based** | Maximizes class distinction |
| **Max Components** | min(n_classes - 1, n_features) |
| **Assumes** | Normal distribution, equal covariance |
| **Can Classify** | Can be used directly as a classifier |

---

## ⚖️ Side-by-Side Comparison

| Aspect | PCA | LDA |
|--------|-----|-----|
| **Supervision** | Unsupervised | Supervised |
| **Optimization Goal** | Max variance | Max class separability |
| **Uses Labels** | No | Yes |
| **Max Components** | min(n, d) | min(c-1, d) |
| **Assumptions** | None | Gaussian, equal covariance |
| **When Classes Overlap** | May mix classes | Tries to separate |
| **Output** | Principal components | Discriminant components |
| **Primary Use** | Compression, visualization | Classification preprocessing |

Where: `n` = samples, `d` = features, `c` = classes

---

## 🔬 Mathematical Details

### PCA: Covariance Matrix Eigen-decomposition

```
Covariance Matrix: Σ = (1/n) X^T X

Eigen-decomposition: Σv = λv

Principal Components: Columns of V (eigenvectors)
Explained Variance: λ (eigenvalues)
```

### LDA: Scatter Matrices

**Within-class scatter:**
```
S_W = Σ_c Σ_{x∈c} (x - μ_c)(x - μ_c)^T
```

**Between-class scatter:**
```
S_B = Σ_c n_c (μ_c - μ)(μ_c - μ)^T
```

**Generalized eigenvalue problem:**
```
S_B w = λ S_W w   →   S_W^(-1) S_B w = λw
```

---

## 📈 Example: When PCA Fails but LDA Succeeds

```
Scenario: Two classes with different means but same direction of spread

Class 1: ●●●●●●●●●●  (spread along diagonal)
Class 2:           ○○○○○○○○○○  (spread along same diagonal)

PCA Result: Projects onto diagonal → Classes MIXED
LDA Result: Projects perpendicular → Classes SEPARATED
```

This happens because:
- **PCA** maximizes variance (the diagonal has most variance)
- **LDA** maximizes separation (perpendicular direction separates classes)

---

## 🛠️ Practical Considerations

### When to Choose PCA

```python
# Use PCA for:
from sklearn.decomposition import PCA

# 1. General dimensionality reduction
pca = PCA(n_components=50)
X_reduced = pca.fit_transform(X)

# 2. Visualization
pca = PCA(n_components=2)
X_2d = pca.fit_transform(X)

# 3. Removing multicollinearity before regression
pca = PCA(n_components=0.95)  # Keep 95% variance
X_clean = pca.fit_transform(X)
```

### When to Choose LDA

```python
# Use LDA for:
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis

# 1. Preprocessing for classification
lda = LinearDiscriminantAnalysis(n_components=2)
X_lda = lda.fit_transform(X, y)  # Note: requires labels!

# 2. As a classifier itself
lda = LinearDiscriminantAnalysis()
lda.fit(X_train, y_train)
predictions = lda.predict(X_test)
```

### Common Pipeline

```python
# Often used together!
# Step 1: PCA to reduce noise and speed up
pca = PCA(n_components=100)
X_pca = pca.fit_transform(X)

# Step 2: LDA for class-optimized reduction
lda = LinearDiscriminantAnalysis(n_components=9)  # for 10 classes
X_lda = lda.fit_transform(X_pca, y)

# Step 3: Classification
classifier.fit(X_lda, y)
```

---

## ⚠️ Limitations

### PCA Limitations
- May discard dimensions important for classification
- Sensitive to feature scaling
- Only captures linear relationships
- Components may be hard to interpret

### LDA Limitations
- Requires labeled data
- Assumes Gaussian distribution
- Assumes equal class covariance
- Max `c-1` components (limiting for many classes)
- Sensitive to outliers

---

## 💡 Interview Tips

1. **Know the fundamental difference** — variance vs. class separation
2. **Explain when each is appropriate** — unsupervised vs. supervised scenarios
3. **Understand the math** — at least conceptually (covariance, scatter matrices)
4. **Mention limitations** — LDA's Gaussian assumption, PCA's variance focus
5. **Real-world example** — face recognition often uses PCA then LDA

---

## ❓ Common Interview Questions

| Question | Key Points |
|----------|------------|
| What's the difference between PCA and LDA? | PCA: variance, unsupervised. LDA: separation, supervised |
| When would PCA fail but LDA succeed? | When max variance direction doesn't separate classes |
| What's the max number of LDA components? | min(n_classes - 1, n_features) |
| Can LDA be used as a classifier? | Yes, it models class distributions |
| What are PCA's assumptions? | Just needs numeric data; LDA assumes Gaussian |

---

## 🔗 Related Concepts

- **Curse of Dimensionality** — Why we need dimensionality reduction
- **t-SNE / UMAP** — Non-linear dimensionality reduction
- **Feature Selection** — Choosing subset of original features
- **Kernel PCA** — Non-linear extension of PCA
- **QDA** — Quadratic Discriminant Analysis (relaxes equal covariance)

---

## 🔬 Quick Reference

```
┌─────────────────────────────────────────────────────────────┐
│                    PCA vs LDA SUMMARY                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  PCA (Principal Component Analysis)                         │
│    • Unsupervised (no labels)                               │
│    • Maximizes VARIANCE                                     │
│    • Best for: compression, visualization, preprocessing    │
│    • Max components: min(n_samples, n_features)             │
│                                                             │
│  LDA (Linear Discriminant Analysis)                         │
│    • Supervised (needs labels)                              │
│    • Maximizes CLASS SEPARABILITY                           │
│    • Best for: classification preprocessing                 │
│    • Max components: min(n_classes - 1, n_features)         │
│                                                             │
│  Rule of Thumb:                                             │
│    No labels? → PCA                                         │
│    Classification task? → Consider LDA                      │
│    Often: PCA first (speed), then LDA (separation)          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

