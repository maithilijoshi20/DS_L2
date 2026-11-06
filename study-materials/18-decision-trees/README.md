# Lesson 18: Decision Trees

A **decision tree** learns a sequence of if–then rules from labeled examples. It uses those rules to predict a category (**classification**) or a number (**regression**) for a new case. Like the supervised models in [Lesson 12](../12-machine-learning-overview-1/README.md), it learns from inputs and known targets; its goal is to generalize to cases it has not seen.

## 1. Reading a tree

A **node** represents the cases that reach a point in the tree. The **root** contains the training cases at the start; an internal node tests a feature and sends cases down **branches** to child nodes. A **leaf** or terminal node gives the prediction. A parent is the node immediately above its children. The root has depth 0, its children depth 1, and so on; some diagrams call the root level 1.

```text
                     Study time < 20 hours?
                       /             \
                    yes               no
                 predict A         predict B
                   leaf               leaf
```

For 25 hours, follow “no” and predict B. The path is a rule: if study time is at least 20 hours, predict B. With more nodes, apply each test in turn until a leaf is reached. In a typical classification tree, the leaf predicts its most common training class; its class proportions can also provide estimated probabilities, though small leaves can make these unreliable. Ties require a specified tie-breaking rule. In a common regression tree, the leaf predicts the mean of its training target values. Thus if the corresponding leaf's scores average 75, the prediction for 25 hours is 75. A leaf prediction is not a guarantee for every case in that group.

Trees can reveal predictive patterns in areas such as manufacturing quality and credit assessment, and their paths can make learned rules explicit. A simple tree is easy to inspect, but a large tree may not be. Its rules describe associations learned from the data, not necessarily causes or justified decisions. Ordinary threshold-based trees generally do not require feature scaling and do not assume a particular input distribution. Their piecewise-constant regression predictions approximate smooth curves in steps and typically extrapolate poorly. Trees are **eager learners**: they build a model during training before a new case arrives. **Nonparametric** here means they do not require a fixed-size parametric form, not that they have no parameters or assumptions.

*Source: pp. 2–8, 16–18, 22–23; prediction and interpretation qualifications added.*

## 2. Growing a classification tree

Training repeatedly considers candidate feature tests and chooses a split that makes the resulting groups more homogeneous in their target classes. For example, a numerical feature can be split at a threshold; a categorical feature can be split into groups of categories. A **binary split** produces two children; a multiway split can produce more. The process is typically greedy: the best immediate split is chosen at each node, without guaranteeing the globally best tree. Growth stops when a stopping condition is met.

A mixed node has nonzero **impurity**; a pure node contains only one class. If `pᵢ` is the proportion in class `i` among the cases at a node, two common measures are:

```text
Entropy = −Σ pᵢ log₂(pᵢ)       Gini impurity = 1 − Σ pᵢ²
```

The sums cover all classes; treat `0 log₂(0)` as zero. Both are zero for a pure node. In binary classification, a 50/50 mix maximizes entropy at 1 and Gini at 0.5. A 2/3 versus 1/3 mix has entropy approximately 0.918 and Gini 4/9 ≈ 0.444. With `C` classes, the maxima are `log₂(C)` and `1 − 1/C`, respectively, when all class proportions are equal.

A split is judged by the decrease from parent impurity to the **weighted average** child impurity, where each weight is the fraction of parent cases sent to that child:

```text
impurity decrease = impurity(parent) − Σ [child size / parent size × impurity(child)]
```

Using entropy, this decrease is **information gain**. For the slide's 1,000 cases, the parent has 700 in one class and 300 in the other, entropy ≈ 0.881. Children of sizes 400 and 600 have entropies 1 and approximately 0.650. The gain is `0.881 − (0.4 × 1 + 0.6 × 0.650) ≈ 0.091`. The weights matter because the children differ in size. Gini decrease uses the same weighted calculation with Gini values; it is commonly called impurity decrease rather than entropy information gain. The best immediate decrease does not establish that the split will predict well on new data.

**Source clarification:** Page 12's example of parent impurity 2 and child impurity 0.5 is arithmetically a decrease of 1.5, but cannot be binary entropy or Gini: their binary maxima are 1 and 0.5. Entropy can reach 2 with at least four classes. Page 13's values are rounded, so the displayed gain is approximate.

*Source: pp. 9–13, 28–29; multiclass bounds and greedy-growth explanation added.*

## 3. Tree variants and regression splits

CART (Classification and Regression Trees) uses binary splits. For classification, CART commonly uses Gini decrease. For numerical regression targets, it commonly chooses splits that reduce within-node squared error, and predicts the leaf mean. For example, targets `2, 4, 8, 10` have mean 6 and total squared error 40; splitting them into `2, 4` and `8, 10` gives leaf means 3 and 9 and total squared error 4. The lower error favors that split among candidates, subject to growth constraints. Other regression-tree losses can use different leaf summaries, such as a median for absolute error.

The slides also mention C4.5 and C5.0, which can make multiway categorical splits, and CHAID, which uses chi-square tests to guide splits. C4.5 commonly uses a gain ratio based on entropy information gain. The impurity criterion and number of branches are distinct choices; an algorithm's specific rules determine which combinations it supports. In particular, C4.5 may also make binary threshold splits for numerical features.

**Source corrections:** Page 7 says regression CART mainly uses Gini; Gini measures class proportions and is not the usual criterion for a numerical regression target. Pages 24–25 suggest C4.5 always uses multiway splits; its numerical threshold splits can be binary. A leaf mean is common, not mandatory for every regression-tree loss.

*Source: pp. 6–7, 22–25; regression calculation and algorithm qualifications added.*

## 4. Controlling complexity

A deep tree can fit noise and peculiar training examples. A large gap between strong training performance and weaker validation performance is a warning of **overfitting**, although sampling variation or a change in data distribution can also contribute. No single depth or training score proves overfitting. Conversely, an overly restricted tree may underfit.

**Pre-pruning** limits growth, for example by setting a maximum depth or number of leaves, requiring a minimum number of cases to split a node or remain in a leaf, or requiring a minimum impurity decrease. **Post-pruning** first grows a tree and then removes branches according to a criterion, replacing them with leaves. Both can trade some training fit for a simpler model that may generalize better; neither guarantees improvement. Choose settings using representative validation data or cross-validation, then evaluate the final choice once on a separate test set, as in [Lesson 13](../13-machine-learning-overview-2/README.md).

*Source: pp. 5, 14–15, 26–27; evaluation qualifications added.*

## 5. Evaluation and practical considerations

Evaluate predictions on held-out cases using measures suited to the target and the consequences of mistakes. For binary classification, with a chosen positive class, `TP` and `TN` are correct positive and negative predictions; `FP` is a false positive and `FN` a missed positive:

```text
Accuracy  = (TP + TN) / (TP + TN + FP + FN)
Precision = TP / (TP + FP)          Recall = TP / (TP + FN)
F1        = 2 × precision × recall / (precision + recall)
```

Precision is the share of predicted positives that are positive; recall is the share of actual positives found. F1 balances those two. Ratios with a zero denominator are undefined unless a reporting convention is specified. With 5 positives and 95 negatives, predicting all negative gives 95% accuracy but zero positive recall. Accuracy alone can therefore obscure failure on a rare class; choose metrics in light of error costs, not class balance alone.

For regression, with actual `yᵢ`, prediction `ŷᵢ`, error `eᵢ = yᵢ − ŷᵢ`, and `n` evaluated cases:

```text
MAE = Σ|eᵢ| / n       MSE = Σeᵢ² / n       RMSE = √MSE
MAPE = (100/n) Σ|eᵢ/yᵢ|
```

MAE and RMSE are in target units; MSE squares the units and emphasizes large errors. MAPE is a percentage but is undefined for zero actual values and unstable near zero. Compare models on the same representative cases and against a simple baseline. See [Lesson 12](../12-machine-learning-overview-1/README.md) and [Lesson 15](../15-linear-regression-2/README.md) for more on these measures.

A categorical feature's **cardinality** is its number of distinct values. Many rare categories can create sparse groups and complicated rules. Depending on the application, combine rare categories into “other” or group related categories using domain knowledge; this may discard useful distinctions. Decide groupings using training data and apply the same mapping to new cases. Missing and unseen values, categorical encoding, and available split types depend on the implementation; decide how to handle them before deployment. Use only features available at prediction time.

*Source: pp. 19–21, 30–31; metric limitations and practical guidance added.*

---

*Source and readability note: Adapted from `[18차시] 의사결정 나무_Lv2기본.pdf`. All 31 pages were rendered and visually reviewed, including figures and quiz questions and answers; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with corrections and additions identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
