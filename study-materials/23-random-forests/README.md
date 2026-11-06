# Lesson 23: Random Forests

A **random forest** is a supervised learning model that combines many decision trees to predict a category (**classification**) or a numerical value (**regression**). Each tree learns from a different sample of the training data and considers random subsets of features. Their predictions are combined to make a usually more stable prediction than one tree alone. This is an example of **ensemble learning**: combining models to improve prediction. Improvement is possible, not guaranteed; compare models on data they have not seen. This lesson builds on [decision trees](../18-decision-trees/README.md) and [ensemble modeling](../22-ensemble-modeling/README.md).

```text
labeled training data
   ├─ bootstrap sample 1 → tree 1 ─┐
   ├─ bootstrap sample 2 → tree 2 ─┼─→ vote or average → forest prediction
   └─ bootstrap sample 3 → tree 3 ─┘
        random candidate features at each split
```

Forests can help predict demand for inventory planning, estimate manufacturing outcomes under different process conditions, or combine financial indicators for trading decisions. A prediction is an input to a decision, not proof that changing a feature will cause the predicted outcome. *Source: pp. 2–5; decision and causal qualifications added.*

## 1. From one tree to a forest

A decision tree repeatedly splits cases using feature-based rules. Its first node is the **root**; each split creates **child** nodes from a **parent**. A terminal node is a **leaf**, which supplies a prediction. Depth counts edges from the root: the root has depth 0 and its children depth 1. For example:

```text
                    temperature > 25?   root, depth 0
                       /        \
                    yes          no
                 high demand   low demand   leaves, depth 1
```

A real tree chooses splits from the training data to make the resulting groups more alike in their outcomes. Classification trees often use a measure of class mixture, such as **Gini impurity**; regression trees commonly use within-node squared error. Deep trees can capture complex patterns but may fit noise and change substantially when training data changes: this is high **variance** and can contribute to overfitting. Restricting depth or requiring more cases in a leaf can reduce complexity, though excessive restriction can underfit. *Source: p. 6; splitting and underfitting explanation added.*

An ensemble may use the same or different algorithms, fitted to the same or different datasets. A forest uses many trees. **Bagging** (bootstrap aggregating) trains models on bootstrap samples and combines their predictions. A bootstrap sample is drawn **with replacement** from the training cases, commonly with the same number of draws as the original dataset. Thus a case can appear more than once in a sample, and another may be absent. For example, from `A, B, C, D`, one possible sample is `A, A, C, D`. Each tree can be trained independently and in parallel. *Source: pp. 7–9, 22–23.*

## 2. How a forest learns and predicts

For each tree, draw a bootstrap sample. At each split, randomly select a subset of candidate features, then choose a good split among those candidates. Repeat until the tree reaches its stopping conditions; repeat independently for the remaining trees. This extra feature randomness makes trees less alike: if every tree always chose the same dominant feature, their errors might be strongly correlated and averaging would help less. Individual trees need not be weak or shallow. Unlike boosting, this process does not sequentially fit each new tree to correct the previous tree's errors.

**Source clarification:** Pages 2, 11 and 17 can suggest one fixed subset is selected for an entire tree. In the standard random forest described here, a fresh candidate subset is normally selected at each split; implementations and settings can vary. *Source: pp. 2–3, 9, 11, 16–17; split-level detail and comparison added.*

For a new case, pass the same features through every tree:

- **Classification:** combine class predictions, commonly by majority vote. If three trees predict `yes, yes, no`, the vote is `yes`. Some implementations average tree class probabilities and select the largest average instead, which can differ from a hard vote; ties require a rule.
- **Regression:** take the arithmetic mean. If three trees predict `10, 12, 14`, the forest predicts `12`. With `B` trees and tree `b` predicting `h_b(x)` for input `x`, the prediction is `ŷ(x) = (1/B) Σ h_b(x)`.

Averaging can reduce variation from individual trees, particularly when their errors differ. It does not remove shared bias or guarantee immunity to noisy data and outliers. *Source: pp. 3, 9, 12, 18–19, 24–25; probability and limitations added.*

## 3. Benefits, costs, and settings

A forest often generalizes better and is less sensitive to the particular training sample than a single deep tree, but its many rules make it harder to explain in a compact way. Trees generally do not require feature scaling or normalization because their splits depend on ordering, although data still needs appropriate cleaning and handling of missing or categorical values for the chosen implementation. *Source: pp. 3, 22–23; practical qualifications added.*

The **number of trees**, maximum depth, minimum leaf size, and number of candidate features per split are hyperparameters: settings chosen before or during model selection. More trees usually stabilize the aggregate and eventually yield diminishing returns; training work and memory generally increase, and prediction may take longer. Parallel training can reduce elapsed time, depending on resources. More trees do not ensure a higher test score at every step.

**Source correction:** Page 15 says too many trees increase overfitting risk, whereas pages 24–25 reject the claim that increasing tree count always causes overfitting. Adding trees generally reduces Monte Carlo variation and tends toward a limiting forest; it does not inherently cause overfitting in the way making individual trees more complex can. A forest can nevertheless overfit, and performance depends on data and settings. Tune complexity and tree count using validation, considering cost as well as performance. *Source: pp. 4, 15, 24–25; clarification added.*

## 4. What feature importance means

A common forest measure, **mean decrease in impurity** (MDI), summarizes how much splits using each feature reduce impurity across the trees, weighting splits by how many training cases reach them. For classification, impurity measures class mixture; for regression, it can measure outcome variation. Importances are commonly normalized to sum to 1 when there is a positive total decrease. They can be computed from the fitted trees without retraining, though calculating and storing them still takes some computation.

For example, an importance of `0.4` means 40% of the forest's normalized training impurity reduction was attributed to that feature under this measure. It is not a 40% causal effect or an assurance that the feature will be useful on new data. Features with many possible split points or categories can receive inflated MDI scores because they offer more opportunities for a favorable training split; correlated features can share or distort importance. As an added alternative, **permutation importance** measures the change in a chosen score when a feature's values are shuffled on evaluation data, but correlated features can also complicate its interpretation. *Source: pp. 3, 10, 20–21; interpretation and alternative added.*

## 5. Evaluate on unseen cases

Keep training, validation, and final test data separate as described in [Lesson 12](../12-machine-learning-overview-1/README.md). Fit preprocessing on training data only. Choose settings on validation data, then use the held-out test set for a final estimate. Choose a split suited to the application, such as a later period for future predictions. Comparing training accuracy alone can hide overfitting.

For classification, choose a positive class. `TP` and `TN` are correct positive and negative predictions; `FP` is a false positive and `FN` a missed positive. The following measures focus on different errors:

```text
accuracy  = (TP + TN) / (TP + TN + FP + FN)
precision = TP / (TP + FP)          recall = TP / (TP + FN)
F1        = 2 × precision × recall / (precision + recall)
```

Precision asks how many predicted positives are actually positive; recall asks how many actual positives were found. F1 balances them through their harmonic mean. Accuracy can look high when one class dominates even if the model misses the minority class. A ratio with a zero denominator is undefined; reporting conventions differ.

For regression, let `yᵢ` be the actual value, `ŷᵢ` the prediction, `eᵢ = yᵢ − ŷᵢ`, and `n` the number of cases:

```text
MAE  = Σ|eᵢ| / n              MSE  = Σeᵢ² / n
RMSE = √MSE                   MAPE = (100/n) Σ|eᵢ/yᵢ|
```

MAE and RMSE are in outcome units; MSE squares the units and emphasizes large errors. MAPE is a percentage, undefined if any actual value is zero and unstable near zero. For actual values `10, 20` and predictions `8, 24`, MAE is `3`, MSE `10`, RMSE approximately `3.16`, and MAPE `20%`. Select the metric according to the cost of errors and compare models on the same held-out cases. *Source: pp. 13–14; evaluation practice, limitations, and example added.*

---

*Source and readability note: Adapted from `[23차시] 랜덤포레스트_Lv2기본.pdf`. All 25 pages were rendered and visually reviewed, including figures, questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with corrections and additions identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
