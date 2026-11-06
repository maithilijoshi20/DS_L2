# Lesson 17: k-Nearest Neighbors

**k-nearest neighbors (k-NN)** predicts an outcome for a new case from the outcomes of similar, previously observed cases. It can predict a category (**classification**) or a number (**regression**). Its central assumption is that cases close in the chosen features tend to have similar outcomes; that assumption may or may not suit the problem.

```text
new case → measure distance to training cases → select k nearest → combine their outcomes
                                                               ├─ vote: category
                                                               └─ average: number
```

Each training case has **features** (inputs) and a known **target** (the outcome to predict). A new case supplies features, but its target is unknown. The positive integer `k` is a chosen hyperparameter, no larger than the number of available training cases.

k-NN is **instance-based**: it uses individual training examples directly. It is also called **lazy learning** because ordinary k-NN retains the examples and defers most prediction work until a query arrives, rather than fitting a fixed equation such as a regression line. It is **nonparametric**: it does not assume a fixed functional form for the relationship. This does not mean it has no choices or assumptions: distance, features, and `k` all matter. Storage and prediction can become costly with many cases; indexing can sometimes speed the neighbor search.

*Source: pp. 2–5, 12–13; computational tradeoff added.*

## 1. Finding neighbors and making a prediction

Choose a distance suitable for the features, calculate the distance from the new case to each training case, and select the `k` smallest. For numerical feature vectors `p` and `q`, with coordinates indexed by `i`:

```text
Manhattan distance = Σ |pᵢ − qᵢ|
Euclidean distance = √[Σ (pᵢ − qᵢ)²]
```

Manhattan adds coordinate differences; Euclidean is straight-line distance. For `(0, 0)` and `(3, 4)`, they are `7` and `5`. Different distance choices can select different neighbors. See [Lesson 13](../13-machine-learning-overview-2/README.md) for more on distance and scaling.

For **classification**, predict the most frequent class among the neighbors. In the slide example, a student with 19 study hours has three nearest cases at 20, 17, and 15 hours, with distances 1, 2, and 4 and outcomes pass, pass, and fail. With `k = 3`, the prediction is **pass** by two votes to one. It is a prediction, not a guarantee of passing.

```text
15 fail ─── 17 pass ── 19 new ─ 20 pass       (one-dimensional distance)
                         ← three nearest →        pass: 2, fail: 1
```

For **regression**, a common prediction is the arithmetic mean of the neighbors' numerical targets. If the three targets are 50, 55, and 51, the prediction is `(50 + 55 + 51) / 3 = 52`. A distance-weighted mean can give closer neighbors more influence; a median is another possible local summary. Similarly, classification can use weighted votes. A mode is the usual classification summary, not the standard continuous regression prediction.

**Source clarification:** Page 7 suggests an even `k` makes binary classification incorrect. An even `k` can predict normally when votes do not tie; an odd `k` avoids vote ties in *unweighted binary* voting. It does not eliminate multiclass ties, distance ties at the selection boundary, or all weighted-vote ties. Specify a consistent tie rule. Page 17's claim that a mode is inappropriate for continuous regression is best read as a practical distinction, not a mathematical impossibility.

*Source: pp. 6–8, 16–19; tie and weighted-vote qualifications added.*

## 2. Choosing k

A small `k` uses a narrow neighborhood. It can capture local detail, but a noisy or unusual neighbor can change the prediction sharply, increasing the risk of **overfitting**. A larger `k` averages over more cases, often smoothing noise but potentially mixing distinct regions and **underfitting**. These are tendencies, not guarantees. At the extreme, using every training case gives the same unweighted prediction everywhere: the overall majority class or target mean.

```text
small k: local detail, more sensitive to noise  ←→  large k: smoother, may miss local patterns
```

Choose `k`, distance, weighting, and preprocessing using validation data or cross-validation, with a metric suited to the task. In cross-validation, fit preprocessing separately on each training fold, transform its validation fold using those fitted rules, and compare candidate settings. Refit the chosen procedure on all development data, then evaluate once on an untouched test set. The `k` in k-NN counts neighbors; the `k` in k-fold cross-validation counts folds and need not be the same number. See [Lesson 13](../13-machine-learning-overview-2/README.md).

*Source: pp. 3, 11, 14–15; validation procedure added.*

## 3. Features, scaling, and missing values

Distance depends on the representation. A feature measured in thousands can dominate another measured in single digits even if it is not more useful. Scaling can change which cases are nearest. For example, standardization subtracts the training mean and divides by the training standard deviation; min–max scaling subtracts the training minimum and divides by the training range. Handle constant features separately because their denominator is zero. Fit these rules on training data only and apply the same rules to new cases. Scaling is a modeling choice: it does not automatically make all features equally meaningful.

For an unordered category, arbitrary codes such as red = 1, blue = 2, green = 3 impose artificial ordering and unequal numerical distances. **One-hot encoding** represents each category with indicator features and is one possible remedy; its influence relative to numerical features still needs consideration. Other distance measures or encodings may suit a particular problem better. Irrelevant features can obscure useful neighbors, especially in many dimensions.

Missing inputs need a defined treatment before a distance can be used. One option is to impute a missing value from similar cases using features that are observed for both cases; this requires a distance that handles missingness and a fallback when suitable neighbors are unavailable. Fit imputation rules within training folds to avoid leakage. **Source clarification:** Page 21 dismisses global mean imputation for k-NN. It can erase local variation and is not universally adequate, but it is a valid baseline in some settings; neighbor-based imputation is not always better. Likewise, scaling is often useful, not invariably required.

*Source: pp. 4, 11, 20–21; leakage, encoding tradeoffs, and imputation qualifications added.*

## 4. Evaluating predictions and using neighbors

Evaluate on representative cases whose outcomes were held out from training and model selection. For binary classification, define a positive class and count true positives (`TP`), true negatives (`TN`), false positives (`FP`), and false negatives (`FN`):

| Measure | Formula | Meaning |
| --- | --- | --- |
| Accuracy | `(TP + TN) / (TP + TN + FP + FN)` | Fraction of all predictions correct. |
| Precision | `TP / (TP + FP)` | Fraction of predicted positives that are positive. |
| Recall | `TP / (TP + FN)` | Fraction of actual positives found. |
| F1 | `2 × precision × recall / (precision + recall)` | Harmonic mean of precision and recall. |

These formulas require nonzero denominators; zero cases need an explicit convention. Accuracy alone can hide poor performance on a rare class. Choose measures according to the costs of mistakes; multiclass tasks need a stated averaging scheme. See [Lesson 12](../12-machine-learning-overview-1/README.md).

For regression, let `eᵢ = yᵢ − ŷᵢ` be actual minus predicted for `n` cases. **MAE** is `Σ|eᵢ|/n`; **MSE** is `Σeᵢ²/n`; **RMSE** is `√MSE`; and **MAPE** is `(100/n) Σ|eᵢ/yᵢ|`. MAE and RMSE are in outcome units, while MSE emphasizes larger errors through squaring. MAPE is undefined for zero actual values and can be misleading near zero. Lower error is better on the same data and metric. See [Lesson 15](../15-linear-regression-2/README.md).

Neighbor methods also support other tasks: unusually large distances to nearby cases can flag potential **outliers** for investigation; neighbor-based imputation can fill missing values; similar customers or transactions can inform credit or fraud models; and similar gene sequences can help predict gene function. A distance flag is not proof of an error or fraud, and predictive similarity does not establish causation. These uses depend on meaningful features, representative data, and suitable evaluation.

*Source: pp. 4, 9–10; metric limitations and practical qualifications added.*

---

*Source and readability note: Adapted from `[17차시] k-NN_Lv2기본.pdf`. All 21 pages were rendered and visually reviewed, including figures, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format; corrections and useful additions are identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
