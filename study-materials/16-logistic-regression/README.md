# Lesson 16: Binary Logistic Regression

**Binary logistic regression** models the probability of one of two outcomes, such as whether a customer buys a product. It is supervised learning: each training case has inputs and an observed outcome coded `1` or `0`. Despite its name, it is commonly used for classification. Unlike the numerical predictions in [Lesson 14](../14-linear-regression-1/README.md), its output is a probability that can be converted into a class decision.

```text
inputs → linear score → logistic function → estimated probability → threshold → class
           η                sigmoid                  p̂                 t       0 or 1
```

The method can describe associations as well as predict. Applications include disease screening, customer response, credit risk, equipment failure, and educational outcomes. A fitted association does not by itself establish causation. “Success” means the chosen `1` outcome; it need not be desirable.

*Source: pp. 2–4; causal qualification added.*

## 1. From a score to a probability

For input values `x₁, …, xₖ`, the model combines coefficients into a **linear predictor** `η`, then applies the logistic function:

```text
η = β₀ + β₁x₁ + ⋯ + βₖxₖ
p = P(Y = 1 | x) = 1 / (1 + e⁻η)
```

Here `β₀` is the intercept, `βᵢ` is the coefficient of input `i`, and `e ≈ 2.718`. The fitted model replaces unknown coefficients by estimates and produces `p̂`. Any finite score maps to a probability strictly between 0 and 1. The sigmoid is S-shaped: it crosses `p = 0.5` at `η = 0`, rises fastest there, and flattens near 0 and 1. Its slope with respect to `η` is `p(1 − p)`, at most `0.25`. Its curve is symmetric about `(0, 0.5)`.

```text
p   1 |                   _____
      |               ___/
  0.5 |-------------●-------------
      |         ___/
    0 |________/                   η →
                    0
```

A single binary outcome follows a **Bernoulli distribution** with success probability `p` and failure probability `1 − p`. A binomial count sums independent Bernoulli trials with a common probability; logistic regression allows different cases to have different probabilities according to their inputs. **Source clarification:** Page 5 lists independence and a constant success probability as properties of a Bernoulli trial. They are conditions for the usual binomial model of repeated trials, not requirements of a single trial or that every case in logistic regression has the same probability.

Logistic regression is a **generalized linear model** (GLM): it models a transformed conditional mean as a linear function of inputs. The transformation is the logit, the inverse of the logistic function. It is the score, not generally the probability, that is linear in the coefficients. The binary outcome does not need to be normally distributed. [Lesson 14](../14-linear-regression-1/README.md) provides the corresponding linear-regression context.

*Source: pp. 2–3, 5–7, 18–19, 24–25; distribution and GLM distinctions added.*

## 2. Odds, log-odds, and coefficients

**Odds** compare the probability of success with that of failure. The **logit** is their natural logarithm:

```text
odds = p / (1 − p)              logit(p) = ln[p / (1 − p)] = η
```

At `p = 0.5`, odds are 1; above 0.5 they exceed 1, and below 0.5 they are less than 1. For `p = 0.75`, odds are `0.75/0.25 = 3`, or 3 to 1, not a probability of 3. The logit maps probabilities between 0 and 1 to scores from negative to positive infinity; the sigmoid maps them back.

Holding other inputs fixed, a one-unit increase in `xᵢ` changes log-odds by `βᵢ` and multiplies odds by `e^βᵢ`, the **odds ratio** (OR). A positive coefficient gives OR > 1 and increases the modelled probability; a negative coefficient gives OR < 1 and decreases it. A zero coefficient gives OR = 1 and no change in the model's prediction for that input under this specification. The intercept is the log-odds when all inputs are zero, which may not be a meaningful case.

For an OR of `0.6`, odds are multiplied by 0.6, a **40% decrease in odds**, holding other inputs fixed. If starting probability is 0.5, starting odds are 1; new odds are 0.6 and new probability is `0.6/1.6 = 0.375`, a decrease of 12.5 percentage points. The probability change depends on the starting probability: an odds ratio is not a probability ratio.

**Source corrections:** Page 21 marks “probability decreases 40%” as the answer even though its explanation calculates a 40% decrease in odds; the odds option is correct. Page 13's displayed derivation uses `β₁` as its intercept; it should be `β₀`, which cancels in the ratio. Pages 12–13 describe coefficients as effects; these are conditional model associations, not necessarily causal effects. A fitted zero coefficient also does not prove absence of a population relationship.

*Source: pp. 10–13, 20–21; numerical interpretation and causal qualification added.*

## 3. Fitting and checking the model

**Maximum likelihood estimation** chooses coefficients under which the observed outcomes have the greatest likelihood. For independent cases, each observed 1 contributes its modelled probability `pᵢ`, and each observed 0 contributes `1 − pᵢ`. Thus, with `yᵢ` equal to 0 or 1:

```text
likelihood L = ∏ pᵢʸⁱ (1 − pᵢ)¹⁻ʸⁱ
log-likelihood = Σ [yᵢ ln(pᵢ) + (1 − yᵢ) ln(1 − pᵢ)]
```

Maximizing the log-likelihood gives the same optimum as maximizing the likelihood, because logarithms preserve order. This fitting objective rewards high probability for observed outcomes; likelihood compares parameter values for the observed data, not probabilities assigned to parameter values. Practical fitting usually uses numerical optimization. This differs from the ordinary least-squares objective in [Lesson 15](../15-linear-regression-2/README.md).

For credible estimates and predictions, consider:

- **Functional form:** The log-odds are linear in the included terms. A continuous input's relationship can be curved; transformed terms or splines may help. Misspecification can hurt prediction and interpretation.
- **Independent observations:** The ordinary likelihood and standard errors commonly assume independent cases. Repeated observations or clusters may require a model or uncertainty method that accounts for dependence.
- **Collinearity:** Strongly overlapping inputs can make individual coefficients unstable; perfect collinearity prevents unique ordinary estimates. High correlation is a warning, not an absolute prohibition.
- **Separation:** If inputs perfectly separate observed classes, ordinary unpenalized maximum likelihood can have no finite coefficient estimates. For example, if all observed 0s have `x < 60` and all 1s have `x > 60`, increasing the slope can keep improving the likelihood. Regularization is one possible remedy.
- **Sample size and imbalance:** Too few informative outcomes relative to model complexity can produce unstable estimates and misleading performance. A rare class also makes accuracy alone misleading; representative data and appropriate evaluation matter.

**Source clarification:** Page 17's 10–15 events per predictor is a rough historical rule, not a guaranteed minimum; required data depend on the event rate, total sample, number of parameters, and modelling goal. Page 9's “no multicollinearity” is best read as avoiding severe redundancy, not demanding uncorrelated predictors. Independence and sufficient data help inference but do not guarantee a correctly specified model.

*Source: pp. 8–9, 17, 24–25; likelihood for binary outcomes and qualifications added.*

## 4. From probabilities to decisions

Choose a **threshold** `t` and classify a case as 1 when `p̂ ≥ t`, otherwise 0. For example, `p̂ = 0.48` is class 0 at `t = 0.5` but class 1 at `t = 0.3`. The threshold of 0.5 is common, not mandatory; costs of false alarms and missed cases, class prevalence, and available resources can justify another choice. A threshold changes class labels, not the fitted probabilities. Decide how to handle equality consistently; page 14 uses “greater than” in its description.

For a fixed set of scores, increasing the threshold can only remove positive predictions: true positives and false positives cannot increase. **Recall cannot increase, but may stay the same**; precision may rise, fall, or remain unchanged. **Source correction:** Page 23 says recall definitely decreases. It may be unchanged if no actual positive crosses the threshold.

*Source: pp. 14, 22–23; threshold choice and equality convention added.*

## 5. Evaluating classification

Compare predicted labels with actual labels on representative held-out cases, following the separation of training, validation, and test data in [Lesson 13](../13-machine-learning-overview-2/README.md):

```text
                         actual 1        actual 0
predicted 1          TP: true positive   FP: false positive
predicted 0          FN: false negative  TN: true negative
```

At a chosen threshold, common measures are:

| Measure | Formula | Meaning |
| --- | --- | --- |
| Accuracy | `(TP + TN) / (TP + TN + FP + FN)` | Fraction of all cases classified correctly. |
| Precision | `TP / (TP + FP)` | Fraction of predicted positives that are actual positives. |
| Recall (TPR) | `TP / (TP + FN)` | Fraction of actual positives found. |
| F1 | `2 × precision × recall / (precision + recall)` | Harmonic mean of precision and recall. |

These ratios need nonzero denominators; software conventions for undefined cases differ. For `TP = 30`, `FP = 10`, `FN = 20`, and `TN = 40`, accuracy is 70%, precision 75%, recall 60%, and F1 approximately 66.7%. With rare events, predicting every case negative can yield high accuracy while finding none of the positives. Choose measures for the consequences of errors, and evaluate probability quality separately when probabilities themselves matter.

The **ROC curve** plots true positive rate (recall) vertically against false positive rate `FPR = FP / (FP + TN)` horizontally, as the threshold varies. The diagonal represents chance-level ranking; a curve nearer the upper-left generally indicates better discrimination. **AUC** is the area under this curve: 1 is perfect ranking and 0.5 is chance-level ranking. Equivalently, it measures how often a randomly selected positive scores above a randomly selected negative, with ties counted half. AUC assesses ranking across thresholds; changing one operating threshold does not change the AUC of a fixed set of scores. It does not establish that probabilities are calibrated or that performance at a particular threshold is acceptable. When positives are rare, precision and recall are especially useful alongside ROC-AUC.

**Source clarification:** Page 16 calls ROC an assessment of overall performance; it summarizes discrimination across thresholds, not calibration or decision costs. The quiz on pages 26–27 correctly distinguishes the axes and notes that AUC does not increase merely because one threshold changes.

*Source: pp. 15–16, 26–27; worked example, held-out evaluation, and limitations added.*

---

*Source and readability note: Adapted from `[16차시] 이항 로지스틱 회귀분석_Lv2기본.pdf`. All 27 pages were rendered and visually reviewed, including figures, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with corrections and useful additions identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
