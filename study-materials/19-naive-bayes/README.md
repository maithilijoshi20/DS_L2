# Lesson 19: Naive Bayes

Naive Bayes is a **supervised classification** method: it learns from labeled examples and assigns a class, such as spam or normal, to a new observation. It combines the prior frequency of each class with how compatible the observation's features are with that class. Its simplifying assumption is that features are independent **given the class**. This builds on conditional probability and Bayes' theorem in [Lesson 5](../05-probability-calculation/README.md) and classification evaluation in [Lesson 12](../12-machine-learning-overview-1/README.md).

## 1. From Bayes' theorem to a classifier

For evidence `E` and a hypothesis `H`, Bayes' theorem is

```text
P(H | E) = P(E | H) × P(H) / P(E)
```

`P(H)` is the **prior**, before observing this evidence; `P(E | H)` is the **likelihood** of the evidence under H; and `P(H | E)` is the **posterior**, after observing it. `P(E)` is the marginal probability of the evidence across all possible hypotheses. It normalizes the posterior probabilities so they sum to 1, provided `P(E) > 0`. Likelihood and posterior reverse the condition and generally differ.

For mutually exclusive, exhaustive classes `c` and features `x = (x₁, …, xₙ)`, a naive Bayes model calculates:

```text
score(c) = P(c) × ∏ᵢ P(xᵢ | c)
P(c | x) = score(c) / Σⱼ score(classⱼ)

labeled examples → estimate class priors and feature distributions
new features     → score each class → normalize → choose a class
```

The product `∏` multiplies one factor for each feature, and the denominator sums scores across *all* classes. The **maximum a posteriori** (MAP) rule chooses the class with the largest posterior. Because the denominator is the same for every class, comparing unnormalized scores is enough to choose the winner; normalization is needed to report probabilities. Other decisions may use a different threshold when mistakes have different costs.

The product comes from the **conditional independence** assumption: once the class is known, learning one feature does not change the modeled distribution of another. This does not require features to be independent overall. In text, for example, related words can still occur together within spam, so the assumption may be wrong. Naive Bayes can nevertheless classify well, although its reported probabilities may be poorly calibrated.

**Source correction:** Page 7's illustrative class values 0.86, 0.23, and 0.45 sum to 1.54, so they cannot be posterior probabilities for three exhaustive, mutually exclusive classes for the same document. They could be ranking scores, but the slide does not establish that interpretation.

*Source: pp. 2, 6–8, 21–22; classification and normalization details added.*

## 2. A worked update

Suppose a component is either defective (D) or normal (N). The priors are `P(D) = 0.1` and `P(N) = 0.9`. The observed features are high temperature and high pressure. Assume that, within each class, these features are independent:

| Conditional probability | Defective | Normal |
| --- | ---: | ---: |
| High temperature | 0.8 | 0.2 |
| High pressure | 0.7 | 0.3 |

```text
Defective score = 0.1 × 0.8 × 0.7 = 0.056
Normal score    = 0.9 × 0.2 × 0.3 = 0.054
Total evidence  = 0.056 + 0.054   = 0.110
P(D | both high) = 0.056 / 0.110 ≈ 0.509
```

The MAP prediction is defective, narrowly, even though only 10% of components were defective before these observations. The result depends on both the priors and the conditional feature probabilities; it is a model estimate, not a guarantee for an individual component.

*Source: pp. 15–16; calculation expanded.*

## 3. Match the model to the features

Different naive Bayes variants specify different class-conditional distributions:

- **Gaussian naive Bayes** models each continuous feature within each class using a normal distribution, estimated from that class's training examples. It uses probability *densities* for continuous values; densities need not be at most 1, but normalized class scores can still produce probabilities.
- **Multinomial naive Bayes** is commonly used for counts, such as word occurrences in documents. It estimates a word distribution for each class. A word appearing twice contributes its factor twice; word order is discarded. For a fixed document, the class-independent multinomial coefficient can be omitted when comparing or normalizing class scores.
- **Bernoulli naive Bayes** uses binary features, such as whether each word is present. Both presence and absence contribute evidence; repeating a word does not change its binary feature.

For example, a text classifier could learn from labeled messages, then use the words in a new message to compare spam and normal scores. Other applications include sentiment classification, predicting disease from symptoms, and identifying unusual financial transactions. Suitability and consequences depend on the data and evaluation; a model's prediction alone is not a diagnosis or proof of fraud.

**Source correction:** In the spam example on p. 10, `0.3 × 0.4 × 0.1 × 0.05 = 0.0006` and `0.05 × 0.01 × 0.6 × 0.1 = 0.00003` are products of conditional word factors, not `P(spam | email)` and `P(normal | email)`. If both class priors are 0.5, the scores are 0.0003 and 0.000015, giving a modeled spam posterior of `0.0003 / 0.000315 ≈ 0.952`. The slide's statement `P(spam) = P(email) = 0.5` does not establish equal class priors; the calculation above explicitly assumes them. If the table instead represents individual word-presence probabilities, multiplying only present-word factors omits the absence factors required by Bernoulli naive Bayes.

*Source: pp. 3, 5, 8, 10; density, count, and example qualifications added.*

## 4. Unseen words and smoothing

Without adjustment, a word with zero occurrences in a class receives an estimated probability of zero. Its factor then makes the entire class score zero, even when the word was merely absent from a small training sample. **Laplace smoothing** adds one to every vocabulary word's count within each class:

```text
P(word w | class c) = (count(w, c) + 1) / (total word count in c + |V|)
```

`V` is the chosen vocabulary and `|V|` its number of distinct words. For three vocabulary words with class counts `2, 1, 0`, the smoothed probabilities are `3/6, 2/6, 1/6`; they still sum to 1. This makes unseen-in-class vocabulary words possible, at the cost of shifting all estimates. A completely new word outside `V` requires a defined vocabulary or unknown-word policy; smoothing alone does not specify one. The formula is for multinomial word counts, not a universal formula for all naive Bayes variants.

*Source: p. 9; example and vocabulary qualification added.*

## 5. Evaluation and practical limits

On held-out labeled data, choose a positive class and count true positives (TP), true negatives (TN), false positives (FP), and false negatives (FN). Common measures are:

```text
accuracy  = (TP + TN) / (TP + TN + FP + FN)
precision = TP / (TP + FP)       recall = TP / (TP + FN)
F1        = 2 × precision × recall / (precision + recall)
```

Precision asks how many predicted positives are correct; recall asks how many actual positives were found. F1 is their harmonic mean. A zero denominator needs an explicit reporting convention. With rare positives, accuracy can look high while the model misses nearly all of them; inspect class-specific precision and recall and select metrics for the cost of errors. ROC AUC summarizes ranking across thresholds using true- and false-positive rates; it does not pick an operating threshold and can look reassuring on imbalanced data, so precision–recall analysis can also be useful. RMSE is commonly used for regression, though squared probability error can also evaluate probabilistic classification; the slide's categorical dismissal is too broad. Evaluate probability calibration if probabilities will drive decisions.

Naive Bayes can be fast and effective with relatively little training data, and its probabilistic output is interpretable as a model-based estimate. However, correlated or redundant features can count the same evidence repeatedly and produce overconfident probabilities; interactions between features may also be missed. Diameter, height, and a volume derived from them are a plausible example of redundant measurements, although conditional dependence must be checked in the actual data. Independence violations affect likelihood modeling, not inherently the estimation of class priors. Outliers can distort estimated distributions, especially Gaussian means and variances; missing values need an appropriate handling strategy. Consider removing redundant features or using another model, and validate choices on held-out data without leaking information from the test set.

*Source: pp. 4, 11–14, 17–20; metric qualifications, calibration, and validation guidance added.*

---

*Source and readability note: Adapted from `[19차시] 나이브베이즈_Lv2기본.pdf`. All 22 pages were rendered and visually reviewed, including figures, questions, and answer panels; page numbers count from the title page. Substantive concepts are integrated without quiz format, and numerical examples were checked. Corrections and useful additions are identified above. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
