# Lesson 8: Inferential Statistics

A sample tells us what was observed. **Inferential statistics** uses sample data and assumptions about how they were obtained to learn about a wider population, while accounting for uncertainty. As in [Lesson 1](../01-statistical-analysis/README.md), the population is the group of interest; a parameter, such as its mean `μ` or proportion `p`, describes that population. A statistic, such as sample mean `x̄`, is calculated from the sample.

```text
Population (unknown parameter) ── sample ──> observations
          ▲                                    │
          └──── estimate, with uncertainty ────┘
```

Studying everyone may be too costly or impractical. Inference supports estimation, hypothesis testing, and prediction, and provides evidence for decisions under uncertainty. A hypothesis test assesses how compatible data are with a specified claim under a model; it does not prove the claim true or false. Prediction concerns an unobserved or future outcome, which is different from estimating a population mean. The quality of each conclusion depends on the sampling design and assumptions. For example, measuring only the morning shift may systematically miss differences in the evening shift; a larger morning-only sample does not fix that bias.

*Source: pp. 2–4, 17–18; sampling and interpretation qualifications added.*

## 1. Sampling variation and standard error

Different samples from the same population can yield different statistics. This is **sampling variation**. Imagine repeatedly drawing samples of the same size in the same way and calculating a mean for each. The distribution of those possible sample means is the **sampling distribution** of the mean. It is a distribution of statistics, not the distribution of individual observations. Its shape and spread depend on the population and sampling method, including sample size.

```text
Same population ──> sample 1 ──> mean 1 ─┐
                ├─> sample 2 ──> mean 2 ─┼─> sampling distribution of means
                └─> sample 3 ──> mean 3 ─┘
```

The **standard error** (SE) is the standard deviation of a statistic's sampling distribution. It measures sampling variability, whereas the standard deviation of observations describes variability among individual values. Smaller SE means greater precision across repeated samples; it does not guarantee accuracy if the estimator is biased.

For `n` independent observations from the same distribution with population standard deviation `σ`, the sample mean has

```text
SE(x̄) = σ / √n                 estimated SE(x̄) = s / √n
```

Here `s` is the sample standard deviation. If `σ = 6 g` and `n = 36`, the mean's SE is `1 g`; for `n = 144` it is `0.5 g`. Four times as many independent observations halve the SE. Dependence and sampling a substantial fraction of a finite population can change this formula.

For independent Bernoulli observations with success probability `p`, the sample proportion `p̂` has `SE(p̂) = √[p(1 − p)/n]`; substituting `p̂` for `p` gives a common estimated SE. **Source correction:** Page 10 labels `σ²√[2/(n − 1)]` as the proportion's SE. It is not; under normal sampling it is instead the SE of the usual sample variance `s²`. Page 22's claim that smaller SE makes an estimate more accurate needs the bias qualification above.

*Source: pp. 6, 9–10, 21–22; formulas' conditions, examples, and corrections added.*

## 2. The central limit theorem

The **central limit theorem** (CLT) explains why the normal distribution from [Lesson 7](../07-continuous-probability-distributions/README.md) often appears in inference. For independent, identically distributed observations with finite mean `μ` and finite positive variance `σ²`, the standardized sample mean approaches a standard normal distribution as `n` grows:

```text
Z = (x̄ − μ) / (σ / √n)  →  N(0, 1) in distribution
```

Equivalently, for a sufficiently large sample, the sampling distribution of `x̄` is approximately normal, centered at `μ` with SE `σ/√n`. The individual observations need not be normal, and increasing n does not make their distribution normal. If the population itself is normal, the mean is exactly normal for any sample size under independent sampling.

The slide's plot shows sample means becoming more concentrated around the population mean as sample size grows, with an increasingly bell-shaped distribution. **Source correction:** Pages 7 and 20 suggest `n ≥ 30` as a general threshold and say the result holds for any population distribution. Thirty is only a rough heuristic, not a guarantee; severe skew or heavy tails can require much more data, and the stated finite-variance conditions matter. Other versions of the CLT can allow some dependence or nonidentical distributions, but require additional conditions.

*Source: pp. 7–8, 19–20; assumptions and corrections added.*

## 3. Point estimation

A **point estimate** is a single value used to estimate an unknown parameter. An **estimator** is the rule applied to a sample; the **estimate** is its value for the sample actually observed. For example, `x̄ = (x₁ + … + xₙ)/n` is an estimator of `μ`; if 36 observed weights average 50 g, the estimate is 50 g. A point alone does not express its sampling uncertainty.

Useful properties of an estimator, evaluated under a specified model, include:

- **Unbiasedness:** Its average over repeated samples equals the parameter. Individual estimates can still be wrong.
- **Consistency:** As sample size grows, estimates converge in probability to the parameter: large errors become increasingly unlikely under the model.
- **Efficiency:** Among comparable unbiased estimators, a smaller sampling variance means greater precision. More generally, comparisons must state their criterion and class of estimators.
- **Sufficiency:** A statistic retains all information in the sample about a specified parameter under the model, in the formal statistical sense. It is not simply the same thing as a representative sample.

**Source corrections:** Page 13 describes unbiasedness as the estimator equaling the parameter; it is the estimator's *expected value* that must equal it. Consistency is an increasing-sample property, not merely sampling close to the population size; and sufficiency is a model-specific property of a statistic, not a synonym for representativeness.

*Source: pp. 5, 11–13, 23–24; definitions and corrections clarified.*

## 4. Interval estimation and confidence

An **interval estimate** gives a range of plausible parameter values produced by a specified method. A common two-sided confidence interval has the form

```text
estimate ± margin of error       margin of error = critical value × SE
            lower bound ├──── estimate ────┤ upper bound
```

The **confidence level** describes the method's long-run coverage: if we repeatedly draw samples and construct intervals in the same way, 95% of those intervals contain the fixed population parameter, provided the method's assumptions hold. The realized interval either contains the parameter or it does not; 95% is not the frequentist probability that this fixed parameter lies in this already calculated interval. Nor does it mean 95% of individual observations lie in the interval. Page 15's description of confidence as the probability an interval contains the parameter needs this repeated-sampling interpretation.

For an independent sample from a normal population with **known** population SD `σ`, a two-sided confidence interval for the mean is exactly

```text
x̄ ± z* × σ/√n
```

`z*` is a positive standard-normal critical value. For confidence levels 90%, 95%, and 99%, it is approximately 1.645, 1.960, and 2.576, respectively. The remaining probability `α` lies in the two tails combined; for 95% confidence, `α = 0.05`, or 0.025 per tail. For nonnormal populations the formula may be approximate when the CLT approximation is adequate.

In the slide's example, `n = 36`, `x̄ = 50 g`, and known `σ = 6 g`. The SE is `6/√36 = 1 g`; a 95% interval is `50 ± 1.96 × 1`, or **[48.04, 51.96] g**. Its coverage interpretation assumes appropriate independent sampling and either normality or a suitable normal approximation; the slides do not establish those conditions from the example alone.

At the same SE, a higher confidence level uses a larger critical value and produces a wider interval. Under the independent-sampling formula, increasing n reduces SE and narrows the interval; neither action repairs selection bias. In practice `σ` is often unknown. **Added context:** For independent normal observations, use `x̄ ± t* × s/√n`, where `t*` is the appropriate t critical value with `n − 1` degrees of freedom. For nonnormal data this can be approximate, depending on sample size and shape. A confidence interval for the mean is not a prediction interval for an individual future observation.

*Source: pp. 14–16, 25–26; repeated-sampling interpretation, assumptions, and t interval added.*

## What to carry forward

Inference connects samples to population questions through a sampling model. A sampling distribution describes how a statistic varies across samples; its standard error quantifies that variation. Point estimates supply single values, while confidence intervals report uncertainty using a procedure with a stated coverage rate. Sound sampling and appropriate assumptions remain essential.

---

*Source and readability note: Adapted from `[8차시] 추론 통계_Lv2기본.pdf`. All 26 pages were rendered and visually reviewed, including the sampling-distribution figure, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with additions and source corrections identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
