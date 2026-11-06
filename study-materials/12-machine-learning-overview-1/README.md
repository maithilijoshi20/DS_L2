# Lesson 12: Machine Learning Overview 1

**Machine learning (ML)** builds models that learn patterns from data. A model is a mathematical representation or set of rules used to describe a phenomenon or make predictions. An algorithm is the procedure used to learn the model. For example, a model trained on past orders might predict whether someone will order chicken today. The aim is often to perform well on new cases, not merely to reproduce the training data.

```text
Past examples → training algorithm → fitted model → prediction for a new case
                                      ↑
                         validation guides choices
                                      ↓
                         test set checks final performance
```

Statistics often emphasizes estimation and inference, as in [Lesson 8](../08-inferential-statistics/README.md); ML often emphasizes prediction. These goals overlap: statistical models can predict, and ML can support inference. Good prediction alone does not establish causation, as discussed in [Lesson 11](../11-correlation-analysis/README.md).

*Source: pp. 2–4; distinctions and interpretation added.*

## 1. Training, tuning, and evaluation

**Training** fits a model using a training dataset. Its learned **parameters** are values estimated from that data. A **hyperparameter** is a setting chosen for the learning procedure or model structure, such as the maximum depth of a decision tree. **Tuning** means trying choices and selecting a model according to an evaluation criterion. Think of a recipe: the recipe is the model, cooking time and temperature are adjustable settings, and taste is a possible evaluation criterion.

Split available data into roles before modeling:

- **Training set:** fit model parameters and learn preprocessing choices.
- **Validation set:** compare models or hyperparameters and make choices before final evaluation.
- **Test set:** estimate final performance on held-out cases after choices are fixed.

A high training score may reflect **overfitting**: the model has learned peculiarities of the training cases that do not generalize. Evaluation data should represent the future task and be kept separate from fitting and tuning. Repeatedly choosing based on test results turns the test set into another validation set. For time-ordered prediction, a later period is often a more realistic test than a random split. As in [Lesson 10](../10-data-processing-2/README.md), learn scaling or other preprocessing parameters on training data only and apply them unchanged to held-out data; otherwise information can leak into evaluation. A validation score is itself affected by model selection, so it is not a substitute for a final test.

*Source: pp. 2–4; parameters, generalization, splitting, and leakage guidance added.*

## 2. Types of learning and variables

**Supervised learning** learns from inputs paired with known outcomes, also called **labels** or targets. For a new input, it predicts an outcome. A **regression** task predicts a numerical quantity, such as tomorrow's sales; a **classification** task predicts a category, such as whether an order will occur. Linear regression and decision trees are examples of supervised approaches; trees can be used for either task. A numerical target can be divided into categories for classification, although this discards information about differences within each category.

**Unsupervised learning** seeks structure in inputs without target labels. **Clustering**, for example, groups similar customers to explore patterns. There is no supplied correct group for each observation, so interpreting and evaluating groups needs context; it is still possible to assess them using suitable criteria or external information. Pattern discovery can support applications such as recommendations. **Reinforcement learning** is different again: an agent interacts with an environment, takes actions, and learns a strategy to maximize accumulated reward. The chicken-order prediction is supervised classification if past examples include whether an order actually occurred.

```text
Supervised:       inputs + known target → learn to predict a target
Unsupervised:     inputs, no target     → explore structure or groups
Reinforcement:    state → action → reward → improve future decisions
```

In supervised learning, the **dependent variable** or target, often `Y`, is the outcome to predict; **independent variables**, **features**, or inputs, often `X`, provide information used for prediction. For the order example, the target is order/no order; features might include recent advertising exposure and days since the last order. Use only information available when the prediction is made. Handle missing or unusual feature values and categorical encoding as appropriate to the method, following [Lessons 9](../09-data-processing-1/README.md) and [10](../10-data-processing-2/README.md).

**Source clarifications:** Page 10 calls independent variables causes. A predictive feature can be associated with an outcome without causing it. Page 9 says training targets must never be missing: ordinary supervised fitting needs a known target for each labeled training example, but missing labels can be excluded or handled with appropriate methods, and other learning settings can use unlabeled examples. Regression commonly predicts continuous values, but numerical targets need not be continuous.

*Source: pp. 5–10, 17–20; evaluation and causal qualifications added.*

## 3. Evaluating numerical predictions

For `n` cases, let `yᵢ` be the actual value, `ŷᵢ` its prediction, and `eᵢ = yᵢ − ŷᵢ` the error. Common regression metrics summarize error across cases; lower values are better for the same dataset and metric.

```text
MAE  = Σ|eᵢ| / n                   mean absolute error
MSE  = Σeᵢ² / n                    mean squared error
RMSE = √(Σeᵢ² / n) = √MSE        root mean squared error
MAPE = (100/n) Σ|eᵢ / yᵢ|         mean absolute percentage error
```

MAE measures average absolute error in the target's units. MSE squares errors, giving larger errors disproportionately more weight, and has squared units. RMSE takes the square root of MSE, returning to the target's units while still emphasizing larger errors. For actual values `3, 5` and predictions `2, 7`, errors are `1, −2`; MAE is `1.5`, MSE is `2.5`, and RMSE is approximately `1.58`. The slide's seven-case example has squared errors totaling `3.48`, so RMSE = `√(3.48/7) ≈ 0.705`.

MAPE expresses average absolute error as a percentage of each actual value. It is undefined when an actual value is zero and can be dominated by cases near zero; its usefulness also depends on a meaningful zero and scale. Choose a metric that reflects the costs of errors. For example, RMSE may be useful when large misses are especially costly. Compare models on the same held-out cases, and consider a simple baseline as context.

*Source: pp. 11–13, 21–22; interpretation, example, and evaluation guidance added.*

## 4. Evaluating binary classification

Choose which category is the **positive** class, such as an order. Each prediction falls into one cell of a **confusion matrix**:

```text
                         Predicted positive    Predicted negative
Actual positive          TP: correct positive  FN: missed positive
Actual negative          FP: false alarm        TN: correct negative
```

A false positive (FP) is a type I error and a false negative (FN) a type II error under this positive-class convention. From these counts:

```text
Accuracy    = (TP + TN) / (TP + TN + FP + FN)
Precision   = TP / (TP + FP)
Recall      = TP / (TP + FN)
Specificity = TN / (TN + FP)
F1          = 2 × precision × recall / (precision + recall)
```

**Accuracy** is the fraction of all predictions that are correct. **Precision** asks what fraction of predicted positives are actually positive. **Recall**, also called sensitivity, asks what fraction of actual positives were found. **Specificity** is the fraction of actual negatives correctly identified. The slide also gives **negative predictive value**, `TN / (TN + FN)`: the fraction of predicted negatives that are actually negative. **F1** is the harmonic mean of precision and recall; it is high only when both are high and does not directly include true negatives.

For example, if `TP = 2`, `FP = 2`, `FN = 2`, and `TN = 1`, precision and recall are each `2/4 = 0.5`, F1 is `0.5`, and accuracy is `3/7 ≈ 0.43`. The slide's separate recall example has `TP = 3` and `FN = 2`, giving recall `3/5 = 0.6`. These metrics range from 0 to 1 when defined. If a denominator is zero, the corresponding ratio is undefined; reporting conventions vary, so state how it was handled.

Accuracy alone can mislead when classes are imbalanced: predicting “no order” for all 100 cases is 95% accurate if only five ordered, but recall for orders is zero. Choose metrics according to the consequences of false alarms and misses. For models producing scores, changing the threshold for calling a case positive usually trades precision against recall; inspect both rather than assuming F1 or accuracy captures every cost. The positive class and evaluation population matter.

*Source: pp. 14–16, 23–26; undefined cases, imbalance, and threshold guidance added.*

---

*Source and readability note: Adapted from `[12차시] 머신러닝 개요 1_Lv2기본.pdf`. All 26 pages were rendered and visually reviewed, including figures, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with clarifications and useful additions identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
