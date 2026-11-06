# Lesson 22: Ensemble Modeling

An **ensemble** combines predictions from multiple models to make one prediction. The hope is that models capture different aspects of a problem, so their errors partly cancel. For example, several classifiers can vote on a category; several regression models can average their numerical predictions. An ensemble must still be evaluated on unseen data: combining models does not guarantee an improvement. [Lesson 18](../18-decision-trees/README.md) explains decision trees, a common base learner.

```text
                         model 1 ── prediction 1 ──┐
input ────────────────── model 2 ── prediction 2 ──┼─ combine ─→ final prediction
                         model 3 ── prediction 3 ──┘
```

## 1. Why combine models?

A **base learner** is an individual model in an ensemble. Base learners can use the same algorithm or different algorithms, and can be trained on the same data or on different samples. Thus the slides distinguish four possibilities: one or multiple datasets crossed with one or multiple algorithms. Even models using the same algorithm can differ because their training samples or settings differ. A **weak learner** is a model only slightly better than an appropriate chance baseline; a **strong learner** performs well. Neither term requires a particular model type.

**Diversity** matters: if all models make the same mistakes, combining them adds little. Useful ensembles balance diversity with individual competence; an inaccurate model does not become useful merely by being different. Different algorithms, training samples, or feature choices can produce different errors. Model weights can reflect validated performance and domain knowledge, but more models also cost computation and may make the result harder to interpret.

As in [Lesson 13](../13-machine-learning-overview-2/README.md), **bias** describes systematic prediction error across hypothetical training samples, while **variance** describes sensitivity to those samples. Averaging suitably diverse models often reduces variance, making predictions more stable. Some ensemble methods can also reduce bias. These are tendencies, not guarantees: an ensemble can still overfit or inherit shared bias. Disagreement among models can be informative about uncertainty, but it is not automatically a calibrated measure of uncertainty.

**Source corrections:** Page 2's “above 50% accuracy” is not a general definition of a weak learner: the chance or baseline performance depends on the task and class distribution. Page 3's claims of reducing bias and variance and preventing overfitting describe possible aims, not assured outcomes.

*Source: pp. 2–5, 10, 15–16; evaluation and uncertainty qualifications added.*

## 2. Bootstrap samples and out-of-bag cases

A **bootstrap sample** is drawn from the training dataset **with replacement**: after each draw, the selected record is available again. A common choice is to draw `n` times from `n` original records. The new sample has `n` draws, but some records repeat and others are absent. For example, a sample of four records `A, B, C, D` could be `A, A, C, D`; `B` is **out of bag** (OOB) for that sample.

```text
training records:  A  B  C  D
bootstrap sample:  A  A  C  D  → fit one model
                         B       → OOB for that model
```

For a particular record, the chance of missing one draw is `1 − 1/n`. The chance of missing all `n` independent draws is `(1 − 1/n)ⁿ`, approaching `1/e ≈ 0.368` as `n` grows. Thus about 36.8% of *distinct original records* are expected to be OOB for a large ordinary bootstrap sample; the exact fraction varies from sample to sample. This result assumes equal-probability sampling and `n` draws, not every possible resampling scheme.

In bagging, a record's OOB prediction can be made by combining only models whose bootstrap samples omitted that record. Comparing these predictions with known outcomes gives an **OOB error estimate** without a separate validation split. It is useful during development, but repeated tuning against it can make it optimistic; retain an independent test set for final assessment. Resampling can also be designed to address class imbalance, but an ordinary bootstrap does not automatically balance classes.

**Source correction:** Page 6 attributes bias reduction to “boosting” while defining bootstrap sampling. Bootstrap resampling and boosting are different ideas; an ordinary bootstrap does not inherently reduce bias. Boosting is described below.

*Source: pp. 6–7, 19–20; OOB evaluation and sampling conditions added.*

## 3. Bagging and voting

**Bagging** means *bootstrap aggregating*: draw multiple bootstrap samples, fit a separate base model on each, and combine their predictions. The models can be trained independently. For regression, the usual combination is the arithmetic mean; for classification, it is often a vote. Bagging is especially useful for unstable learners whose predictions change substantially when training data changes, such as deep decision trees. Averaging can reduce variance when errors are not perfectly correlated; it does not necessarily fix a systematic error shared by all models.

```text
training data ─┬─ bootstrap 1 → model 1 ─┐
               ├─ bootstrap 2 → model 2 ─┼─ average or vote → prediction
               └─ bootstrap 3 → model 3 ─┘
```

In **hard voting**, each classifier votes for a class and the most frequent class wins. If five models predict `A, B, A, C, A`, the result is `A` with three votes. A tie needs a stated tie-breaking rule. Models can alternatively receive different vote weights.

In **soft voting**, average the predicted probabilities for each class, optionally using weights, and select the class with the highest combined probability. For three models with probabilities for `A` of `0.8, 0.3, 0.4` and for `B` of `0.1, 0.4, 0.3`, the averages are `0.50` for `A` and about `0.27` for `B`. If their probabilities for `C` are `0.1, 0.3, 0.3`, its average is about `0.23`, so soft voting selects `A`. Hard voting on those same model probabilities also selects `A`: the individual highest-probability classes are `A, B, A`. The two methods can nevertheless disagree. Soft voting is most meaningful when probabilities are reasonably calibrated and refer to the same classes.

**Source correction:** Page 9's soft-voting illustration shows `0.95, 0.81, 0.86` yielding `0.95`, suggesting selection of the largest individual probability. Standard soft voting combines each class's probabilities across models; these three values would average to approximately `0.873` if they all refer to the same class. The other class probabilities are also needed to choose a class.

*Source: pp. 8–9, 11–14; tie handling, calibration, and correction added.*

## 4. Boosting and practical evaluation

**Boosting** builds an ensemble sequentially: later learners focus on shortcomings of the current ensemble, and their contributions are combined. Depending on the method, this may mean giving more weight to previously misclassified cases or fitting new models to residual-like errors. Boosting often reduces bias compared with a single simple learner; it can also change variance and can overfit, particularly if noise or settings are problematic. Bagging typically trains models independently on bootstrap samples and primarily aims to reduce variance; boosting typically builds them in sequence to improve the current fit. These are useful tendencies, not exclusive rules.

Choose candidate models and weights using a validation set or cross-validation, keeping preprocessing inside each training fold as described in [Lesson 13](../13-machine-learning-overview-2/README.md). Compare the ensemble with its individual models and a simple baseline using a metric suited to the task. For imbalanced classes, accuracy alone may hide poor performance on the minority class; inspect appropriate class-specific measures. Once choices are fixed, assess once on a representative untouched test set. Performance gains should justify the extra cost and complexity.

*Source: pp. 10, 17–18; boosting mechanism and evaluation guidance added.*

---

*Source and readability note: Adapted from `[22차시] 앙상블 모델링_Lv2기본.pdf`. All 20 pages were rendered and visually reviewed, including figures, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format; corrections and additions are identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
