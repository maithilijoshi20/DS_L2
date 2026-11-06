# Lesson 13: Machine Learning Overview 2

A useful machine learning model must work on **new cases**, not just describe its training data. This lesson extends [Lesson 12](../12-machine-learning-overview-1/README.md): it explains how to assess that ability, how models can compare observations, and why feature scales matter.

## 1. Underfitting, overfitting, and the bias–variance tradeoff

**Underfitting** occurs when a model misses important patterns: it often performs poorly even on training data. A straight line fitted to a strongly curved relationship might underfit. **Overfitting** occurs when a model fits training-specific noise or peculiarities so closely that it performs worse on new data. A very wiggly curve through every training point might overfit. A useful fit captures a general pattern while tolerating some noise.

```text
Model too simple            Useful fit             Model too flexible
misses the curve            follows the trend      follows individual noise
poor train and new fit      good new-case fit      good train, worse new fit
```

Compare training and held-out performance using the same metric: poor results on both can suggest underfitting; much better training than validation results can suggest overfitting. These are diagnostic clues, not proofs. Model complexity, training data, features, noise, and the evaluation setup all matter. Regularization, simpler models, and more representative data can help with overfitting; richer features or a more flexible model can help with underfitting. Check changes on validation data.

**Bias** is systematic error in the average prediction across hypothetical training samples; **variance** measures how much predictions change across those samples. High bias can miss a real pattern; high variance makes a fit sensitive to the particular training sample. For squared prediction error, a standard theoretical decomposition is:

```text
expected prediction error = bias² + variance + irreducible noise
```

The expectation is over training samples and new outcomes at a fixed input. Irreducible noise reflects unpredictable outcome variation even if the underlying relationship were known. Increasing flexibility often reduces bias and increases variance, producing a tradeoff, but this is a tendency, not a universal law.

**Source corrections:** Pages 3 and 17 state that bias and variance cannot both be reduced; that is too absolute. Better data or a better method can reduce both. Page 3's displayed expression is not the general bias–variance decomposition: the variance of a fitted prediction is not generally the shown noise variance divided by the number of terms. The decomposition above specifies the usual squared-error setting. Bias is not simply an error caused by an overly simple model, nor variance simply an error caused by a complex one.

*Source: pp. 2–3, 16–17; diagnostic and theoretical qualifications added.*

## 2. Holdout and cross-validation

A **holdout** split reserves data for evaluation. Fit model parameters on training data, use validation data to choose features, model, and hyperparameters, and use a separate test set once choices are fixed to estimate final performance. Common examples are 80:20 training/test or 50:30:20 training/validation/test; there is no universally correct ratio. A holdout does not itself prevent overfitting, but a properly isolated set helps reveal it. Repeated decisions based on the test set compromise its role. See [Lesson 12](../12-machine-learning-overview-1/README.md) for metrics and leakage.

In **k-fold cross-validation (CV)**, divide the development data into `k` nonoverlapping folds, often 5 or 10. For each run, fit a fresh model on `k − 1` folds and evaluate it on the remaining fold. Rotate the held-out fold so each serves once, then summarize the scores, often by their mean.

```text
              Fold 1   Fold 2   Fold 3
Run 1         assess   train    train
Run 2         train    assess   train
Run 3         train    train    assess
                       ↓
              summarize the three scores
```

For example, fold accuracies of 0.80, 0.90, and 0.85 average to 0.85. Scores can vary across folds, so their spread is informative too. For a fair comparison, keep a candidate's hyperparameters fixed across its folds; compare candidates by repeating CV, then select settings. Refit the chosen procedure on all development data and evaluate it on the untouched test set. The selected CV score can be optimistic because it helped select the model.

Split in a way that reflects future use: keep related records from the same person together, use later data for evaluation when forecasting, and consider stratification for imbalanced classes. Ordinary random folds are not always appropriate. **Source clarification:** Pages 5 and 19 say hyperparameters are not changed during CV. They remain fixed *within evaluation of a candidate*; CV can also be used to compare different hyperparameter choices. The slide's “test fold” is a validation fold during tuning, distinct from the final test set.

*Source: pp. 4–6, 18–19; selection, leakage, and split guidance added.*

## 3. Distance and similarity

A **feature vector** lists an observation's numerical features, such as `(age, income, years of experience)`. A distance expresses how far two vectors are apart: smaller means closer according to that measure. A similarity expresses resemblance: larger usually means more similar. The choice defines what “similar” means, which matters for methods such as nearest neighbors and clustering.

For vectors `p` and `q` with coordinates indexed by `i`:

```text
Manhattan:  d(p, q) = Σ |pᵢ − qᵢ|
Euclidean:  d(p, q) = √[Σ (pᵢ − qᵢ)²]
Minkowski:  d(p, q) = [Σ |pᵢ − qᵢ|ʳ]^(1/r),  r ≥ 1
```

Manhattan distance is the sum of coordinate differences, like traveling along city blocks. Euclidean distance is the straight-line distance, extending the Pythagorean theorem. Minkowski is a family: `r = 1` gives Manhattan, `r = 2` Euclidean, and as `r` approaches infinity it approaches the largest absolute coordinate difference, also called Chebyshev distance. For `(0, 0)` and `(3, 4)`, these are respectively `7`, `5`, and `4`. The slides' customers `(28, 5000, 5)` and `(40, 13000, 9)` have Manhattan distance `8016` and Euclidean distance `√64,000,160 ≈ 8000.01`; income dominates because its numbers are much larger.

```text
(0,4) ───── (3,4)    Manhattan: 4 up + 3 across = 7
  │        /          Euclidean: diagonal = 5
  │      /
(0,0)
```

**Mahalanobis distance** accounts for feature spread and correlation using a covariance matrix `Σ`. For column vectors, `d(p, q) = √[(p − q)ᵀ Σ⁻¹ (p − q)]`. Differences in a direction with high variability count less; correlated directions are adjusted together. If the covariance matrix is the identity, it becomes Euclidean distance. Equivalently, after a suitable covariance-based whitening transformation, Euclidean distance in transformed coordinates equals Mahalanobis distance. It requires a suitable invertible covariance estimate; singular or poorly estimated covariance needs special treatment. Consistent changes of units are accounted for when the covariance is transformed too.

*Source: pp. 7–12; examples, conditions, and interpretation added.*

**Cosine similarity** compares vector directions rather than their magnitudes. For nonzero vectors `a` and `b`:

```text
cosine similarity = (a · b) / (||a|| ||b||)
                  = Σ(aᵢbᵢ) / [√Σ(aᵢ²) × √Σ(bᵢ²)]
```

It is `1` for the same direction, `0` for perpendicular vectors, and `−1` for opposite directions. It is undefined for a zero vector. For example, `(4, 0)` and `(5, 2)` have similarity `20/(4√29) = 5/√29 ≈ 0.9285`. Cosine is a similarity, not a distance. **Source correction:** Page 13's expanded denominator uses a plus sign between the two vector lengths; the correct operation is multiplication, as in its dot-product formula above it.

**Jaccard similarity** compares sets by the fraction of distinct elements in their union that are shared:

```text
J(A, B) = |A ∩ B| / |A ∪ B| = |A ∩ B| / (|A| + |B| − |A ∩ B|)
```

For `A = {red, blue}` and `B = {blue, green}`, the intersection has one element and the union three, so similarity is `1/3`. Disjoint sets score `0`; identical nonempty sets score `1`. If both are empty the formula is `0/0`, so a convention is needed. Jaccard counts shared presence, not shared absence.

*Source: pp. 13–14, 20–23; zero cases and examples added.*

## 4. Scaling without leakage

As the customer example shows, numerical scale can dominate distance. **Scaling** puts features on comparable scales when that suits the task. One option is min–max scaling, `(x − training minimum) / (training maximum − training minimum)`; another is standardization, subtracting the training mean and dividing by the training standard deviation. These transformations change the relative influence of features, so the choice should reflect the problem rather than be automatic. A constant training feature needs special handling because its denominator is zero.

Learn scaling rules from the training data only, then apply those same rules to validation and test data. For training values `0, 2, 4, 10`, min–max scaling divides by 10: a test value of `12` becomes `1.2`. Values outside `[0, 1]` are possible; they do not justify refitting on test data. Using the combined training and test range would leak information and make evaluation less representative of genuinely unseen cases. Within CV, fit the scaling separately on each run's training folds and apply it to that run's held-out fold; after selection, fit it on the full development set before the final test.

*Source: pp. 8, 10, 15, 24–25; scaling alternatives and fold-level procedure added.*

---

*Source and readability note: Adapted from `[13차시] 머신러닝 개요 2_Lv2기본.pdf`. All 25 pages were rendered and visually reviewed, including figures, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with corrections and useful additions identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
