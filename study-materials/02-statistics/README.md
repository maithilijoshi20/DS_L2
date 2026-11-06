# Lesson 2: Statistics and Summary Measures

A dataset can contain thousands of observations. A **statistic** condenses some aspect of those observations into a number: a mean, median, or standard deviation, for example. Different summaries answer different questions. To understand a distribution, look at its **center**, **spread**, and **shape** together.

As in [Lesson 1](../01-statistical-analysis/README.md), a sample statistic describes the observed sample and can help estimate a population parameter. Different samples can produce different statistics, even when the population has not changed.

## 1. What statistics can tell us

```text
Observed sample ── calculate ──> Statistic
                                    │
                       ┌────────────┴────────────┐
                       ▼                         ▼
                Describe the sample       Estimate a population
```

A **descriptive statistic** summarizes observed data. An **estimator** is a rule for estimating a population parameter: the sample mean x̄, for instance, can estimate population mean μ. A **test statistic** is used in a procedure that evaluates evidence about a hypothesis. The same statistic can serve more than one role.

An **order statistic** identifies a position in sorted data, such as the smallest or third-smallest observation. A **sufficient statistic** retains all the sample information about a particular parameter under a specified probability model. It does not necessarily preserve every detail of the dataset.

*Source: pp. 2-6.*

## 2. Finding the center

The mean, median, and mode describe different aspects of a typical value.

| Measure | How to find it | Useful when |
|---|---|---|
| Arithmetic mean | Add the values and divide by their count | Every value should contribute to the summary |
| Median | Sort the values and find the middle | Extreme values would distort the mean |
| Mode | Find the most frequent value | The most common value or category matters |

For the added example `2, 4, 5, 6, 83`, the mean is 20, the median is 5, and there is no unique mode. The large value pulls the mean upward. The median is resistant to an individual extreme value; it does not describe the total in the way the mean does. If there are an even number of numerical observations, the median is the average of the two middle values. For `2, 4, 6, 8`, it is `(4 + 6) / 2 = 5`.

A mode can describe nominal categories, such as the most common production line. There may be several tied modes. A continuous distribution can also have multiple peaks; a histogram helps visualize them, although its bin sizes affect the picture. An ordered category can have a median too, provided its ordering is meaningful.

### Different kinds of average

The right average depends on what is being combined:

- **Weighted mean:** Give each value a weight. If two groups of 2 and 3 products have mean weights 80 g and 90 g, the combined mean is `(2 × 80 + 3 × 90) / 5 = 86 g`. Group size determines each weight.
- **Geometric mean:** Use for multiplying factors across equal periods. Growth factors 1.10 and 0.90 have geometric mean `√(1.10 × 0.90) ≈ 0.995`, or about **−0.5% per period**. The two changes together leave 99% of the starting value.
- **Harmonic mean:** Useful for some rates. Traveling 60 km at 60 km/h and another 60 km at 120 km/h takes 1.5 hours, so the overall speed is `120 / 1.5 = 80 km/h`. This is the harmonic mean `2 / (1/60 + 1/120)`. For equal *time* at the two speeds, the appropriate average is 90 km/h instead.

For the same positive observations with equal weights, harmonic mean ≤ geometric mean ≤ arithmetic mean. The harmonic formula divides by each observation, so it cannot be used directly when an observation is zero. These examples are added to make the choice of average concrete.

*Source: pp. 7-13.*

## 3. Quantiles: positions in a distribution

A **quantile** marks a position in sorted data. Quartiles divide the distribution into four portions; percentiles divide it into hundredths, and deciles into tenths.

```text
small values                                               large values
     │───────────│───────────│───────────│───────────│
    minimum      Q₁          Q₂          Q₃        maximum
                25th        median       75th
              percentile  / 50th       percentile
```

The portions represent approximately equal **proportions of observations**, not necessarily equal distances on the number line. Quartiles are also the basis of a box plot: its box runs from Q₁ to Q₃, with a line at the median.

**Added example:** Sort `2, 4, 6, 8, 10`. If we include the median in each half, the lower half `2, 4, 6` has Q₁ = 4 and the upper half `6, 8, 10` has Q₃ = 8. The middle half spans from 4 to 8.

Quartile calculations use different conventions, especially for small samples. Excluding the median from each half instead gives Q₁ = 3 and Q₃ = 9. Both conventions are used; check the method before comparing results. Ties and small samples also mean that exactly 25% of observations need not fall in each portion.

*Source: pp. 14-16, 34-35; the quartile method and alternatives are clarified here.*

## 4. Measuring spread

Two datasets can have the same center but very different variability. The range, interquartile range, and standard deviation describe spread in different ways.

| Measure | Calculation | Interpretation |
|---|---|---|
| Range | Maximum − minimum | Total span; depends entirely on the two extremes |
| Interquartile range (IQR) | Q₃ − Q₁ | Width of the middle portion; relatively resistant to extremes |
| Variance | Average squared deviation from the mean, with a specified denominator | Spread in squared units; sensitive to extremes |
| Standard deviation | Square root of variance | Spread in the original units |

For `2, 4, 6, 8, 10`, the mean is 6, the range is `10 − 2 = 8`, and the IQR is `8 − 4 = 4` using the first quartile convention above. The deviations from the mean are `−4, −2, 0, 2, 4`; their squares add to 40.

```text
Variance describing these five values = 40 / 5 = 8
Standard deviation                     = √8 ≈ 2.83
```

There are two common variance denominators. Dividing by **n** describes these observed values and gives population variance when the data cover the entire population. Dividing by **n − 1** gives the usual sample variance when estimating population variance: here `40 / 4 = 10`, with sample standard deviation `√10 ≈ 3.16`. The latter requires at least two observations. Under independent, identically distributed sampling, the `n − 1` variance is an unbiased estimator of population variance; its square root is not generally unbiased for population standard deviation. Always check which convention a calculation uses.

The **coefficient of variation** (CV) compares standard deviation to the mean. If mean weight is 100 g and standard deviation is 5 g, CV = `5 / 100 = 5%`. It is useful for relative spread when the mean is positive and zero has a meaningful interpretation. It is undefined at a zero mean, unstable near zero, and unsuitable for Celsius temperature because Celsius has an arbitrary zero.

*Source: pp. 17-21; the denominator distinction and CV example are added clarifications.*

## 5. Reading the shape of a distribution

**Skewness** describes asymmetry. A long tail toward larger values is called right or positive skew; a long tail toward smaller values is left or negative skew.

```text
Left skew                 Roughly symmetric                 Right skew
long tail ←  peak              balanced               peak  → long tail
```

In many simple, single-peaked distributions, the mean is pulled toward the long tail. For example, a few unusually large incomes can make mean income higher than median income. This is a useful tendency, not a rule that fixes the order of mean, median, and mode for every distribution. A skewness of zero alone does not prove symmetry.

**Kurtosis** uses the fourth power of standardized deviations, giving extreme values substantial influence. It is often reported as **excess kurtosis**, which subtracts 3 from ordinary kurtosis. A normal distribution has ordinary kurtosis 3 and excess kurtosis 0. Zero excess kurtosis does not prove that a distribution is normal.

Kurtosis is sometimes described as the sharpness of a peak. That description is incomplete: kurtosis alone does not determine peak height or how much data lies near the mean. It is strongly influenced by extreme standardized values. Check whether a tool reports ordinary or excess kurtosis, and inspect a plot rather than inferring the entire shape from one number.

*Source: pp. 22-27; the claims about zero skewness and peakedness are corrected here.*

## 6. How transformations change summaries

Changing every value by the same rule also changes its summaries in predictable ways. If `Y = aX + b`, then:

```text
mean of Y     = a × mean of X + b
variance of Y = a² × variance of X
SD of Y       = |a| × SD of X
```

Adding `b` shifts the whole distribution without changing its spread. Multiplying by `a` scales distances from the mean by `|a|`; variance scales by `a²` because those distances are squared.

**Added example:** If X has mean 10 and variance 4, then `Y = 3X + 5` has mean `3 × 10 + 5 = 35`, variance `3² × 4 = 36`, and standard deviation 6.

When combining two variables, their relationship matters for variance. For `Z = aX + bY + c`:

```text
mean of Z     = a × mean of X + b × mean of Y + c
variance of Z = a² Var(X) + b² Var(Y) + 2ab Cov(X, Y)
```

**Covariance** measures how deviations tend to move together: positive when high values of one tend to accompany high values of the other, negative when high values tend to accompany low values. If X and Y are independent and have finite variances, covariance is zero. The reverse does not generally hold: zero covariance does not prove independence.

For example, if `Var(X) = 4`, `Var(Y) = 9`, and `Cov(X, Y) = 3`, then `Var(X + Y) = 4 + 9 + 2 × 3 = 19`. If they were independent, the covariance term would vanish and the variance would be 13. The mean formula does not require independence.

**Added ML context:** Standardizing a feature by subtracting its training mean and dividing by its nonzero training standard deviation gives the training values mean 0 and standard deviation 1. Apply those same training quantities to validation and test data. Standardization does not make the distribution normal or remove outliers.

*Source: pp. 28-29, 38-39.*

## What to carry forward

Choose a statistic to match the question and the data's meaning. Report center alongside spread, examine the distribution's shape, and state calculation conventions when they can change the result. A compact summary is useful, but it cannot retain every detail of the observations.

---

*Source note: Adapted from `[2차시] 통계량_Lv2기본.pdf`. All 39 pages were rendered and visually reviewed, including figures and answer panels, because the PDF is predominantly images. Page references count from the title page. Examples and ML context extend the source. The guide clarifies quartile conventions, variance denominators, conditions for averages, skewness, and kurtosis. It is a concept-based guide, not a certified translation; lecture audio and instructor commentary were unavailable.*
