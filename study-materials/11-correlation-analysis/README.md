# Lesson 11: Correlation Analysis

**Correlation analysis** describes the direction and strength of association between two variables measured on the same units. For example, each record might pair a person's hours of practice with their test score. Correlation is useful for exploring relationships in fields from manufacturing and agriculture to health and social science. It is a summary of observed association, not evidence by itself that changing one variable causes the other to change.

```text
Paired observations → scatterplot → choose a measure → interpret in context
                       shape, outliers        direction, strength, uncertainty
```

A **scatterplot** puts each pair `(x, y)` at one point. An upward cloud suggests positive association; a downward cloud suggests negative association. A **monotonic** relationship consistently moves in one direction as X increases, though the rate can change and values may tie. A curved increasing pattern can be monotonic without being linear. Inspect the plot: a single number cannot reveal every shape, subgroup, or unusual point.

*Source: pp. 2–5, 16–17, 33–34; interpretation added.*

## 1. Covariance and Pearson correlation

**Covariation** means variables vary together. **Covariance** measures their joint variation around their respective means. For `n` paired observations, sample covariance is

```text
cov(X, Y) = Σ[(xᵢ − x̄)(yᵢ − ȳ)] / (n − 1)
```

Pairs above both means or below both means contribute positive products; pairs on opposite sides contribute negative products. Positive covariance indicates an overall tendency to vary in the same direction, negative covariance the opposite. Near zero indicates little *linear* covariation, not necessarily no relationship. Its magnitude depends on the variables' units and scales, making direct comparisons difficult. Here `n` is the number of pairs; **source correction:** page 19 labels `n` as degrees of freedom, but the denominator's degrees of freedom are `n − 1`.

The **Pearson correlation coefficient** standardizes covariance using the sample standard deviations `sₓ` and `sᵧ`:

```text
r = cov(X, Y) / (sₓ sᵧ)
  = Σ[(xᵢ − x̄)(yᵢ − ȳ)] / √[Σ(xᵢ − x̄)² Σ(yᵢ − ȳ)²]
```

It measures the direction and strength of a **linear** relationship. It is unitless, symmetric in X and Y, and lies between −1 and 1 when both variables vary. Positive linear rescaling, such as changing meters to centimeters, leaves it unchanged; reversing one scale reverses its sign. A constant variable has zero standard deviation, so its correlation is undefined. A coefficient of −0.85 indicates a strong negative linear association in the observed data; it does not mean one variable causes the other. Strength labels depend on context.

```text
r near +1             r near −1              r near 0
      •                •                     •   •
   •                      •                •   •   •
 •                           •               •   •
positive linear        negative linear       little linear trend
```

`r = +1` or `−1` means all nonconstant observed pairs lie exactly on an increasing or decreasing straight line. Correlation describes how closely points follow a line, not the line's slope: steep and shallow lines can both have `r = 1`. A near-zero Pearson coefficient can coexist with a strong nonlinear relationship; for example, the pairs `(-1, 1), (0, 0), (1, 1)` follow `y = x²` but have `r = 0`.

**Source correction:** Page 12 lists X = `1, 2, 3, 4, 5, 6` and Y = `2, 3, 4, 5, 6, 7`. These pairs satisfy `Y = X + 1`, so `r = 1`, not 0.943. The displayed cross-products for the middle two pairs have incorrect signs; the first squared deviation is 6.25, not 6.5. The sum of cross-products is 17.5, equal to both sums of squared deviations. The mean expression also swaps the middle Y values, although the mean remains 4.5.

*Source: pp. 10–12, 16–19, 27–28, 31–32; examples and scale qualifications added.*

## 2. Spearman rank correlation

**Spearman's rank correlation** measures the strength and direction of a monotonic association. Replace each variable's values by their ranks, then calculate Pearson correlation on the two rank columns. It can be used with ordered categories or numerical values. The ordering must have meaning; arbitrary codes for nominal categories such as colors do not supply meaningful ranks. Unlike Pearson on original values, Spearman can capture a nonlinear but monotonic pattern.

```text
X values:       1   2   3       ranks: 1   2   3
Y values:       1   4   9       ranks: 1   2   3   → Spearman = +1
```

When no ranks are tied, the shortcut is `rₛ = 1 − 6Σdᵢ² / [n(n² − 1)]`, where `dᵢ` is the difference between the two ranks for pair `i`. Use the same rank direction for both variables. The slide's five-pair example has `Σdᵢ² = 16`, giving `rₛ = 1 − 96/120 = 0.2`. If values tie, assign each tied value the average of the ranks they occupy and calculate Pearson correlation on those ranks; the shortcut above generally no longer applies. Both measures are undefined if either resulting variable is constant.

Spearman uses order instead of numerical distances, so a very large value often has less influence than under Pearson. It is not immune to unusual observations, and it answers a different question. It does not require a normal distribution to calculate. **Source clarification:** Pages 3, 7, and 13 can suggest that all correlation requires linearity or that Spearman requires the data to be monotonic in order to calculate it. Both coefficients can be calculated without those shapes; the shape determines what each coefficient usefully summarizes. Spearman values near zero do not rule out nonmonotonic relationships.

*Source: pp. 5, 7, 13–15, 29–30; tied-rank procedure and qualifications added.*

## 3. Sampling and assumptions

A sample coefficient describes the observed pairs. As in [Lesson 8](../08-inferential-statistics/README.md), using it to infer a population relationship requires an appropriate sampling design and a method for uncertainty, such as a confidence interval or hypothesis test. A small sample can yield an unstable estimate, a wide interval, and low power to detect a real association; a larger biased sample does not automatically fix selection bias. Statistical significance and practical importance are different questions.

**Source clarification:** Page 6 lists linearity, normality, equal variance, independence, and continuous measurements as Pearson assumptions. The coefficient itself can be computed for any paired numerical data with nonzero variation, including a binary indicator. Linearity matters when interpreting it as a summary of a straight-line pattern. Independence of pairs and distributional assumptions matter for particular tests and intervals, not for the arithmetic itself. Classical Pearson inference commonly assumes independent pairs from a bivariate normal population; its joint distribution has normally distributed margins, but normal margins alone do not establish joint normality. Under that model, conditional spread is constant; other inference methods may use different assumptions. Spearman is nonparametric but its inference still needs a valid sampling or dependence model.

**Autocorrelation** is association between observations of the same variable at different time lags: today's temperature may resemble yesterday's. It can violate the independent-pair assumption and distort standard errors if ignored. A time-series method or a dependence-aware uncertainty estimate may be needed. It does not establish causation. **Source correction:** Page 26 says correlation analysis is unrelated to mean-based calculations; Pearson covariance and correlation explicitly use means, as shown above.

*Source: pp. 6–9, 24–26; distinctions between calculation and inference added.*

## 4. Interpretation in context

Correlation does not identify cause. Ice-cream sales and swimming accidents might both rise in hot weather; temperature is a possible common cause. Education and income might be related in both directions or influenced by family resources. Even a very high correlation between two trends over time may be coincidental or reflect shared time trends. Establishing causation requires additional design and evidence; the correlation alone cannot decide between these explanations.

As in [Lesson 9](../09-data-processing-1/README.md), investigate outliers rather than automatically removing them. A single unusual point can pull Pearson correlation toward or away from zero, potentially masking a pattern or inflating it. Compare the plot and results with and without an investigated point, and explain any treatment. Spearman may be useful as a complementary rank summary, not an automatic correction for bad data. Missing values, restricted ranges, mixed subgroups, and time dependence can also change the apparent association.

*Source: pp. 20–23; additional interpretation cautions added.*

## What to carry forward

Use Pearson for linear association in the measured values and Spearman for monotonic association in ranks. Examine the paired data and its shape before interpreting either; quantify uncertainty when making population claims, and do not infer causation from correlation alone.

---

*Source and readability note: Adapted from `[11차시] 상관분석_Lv2기본.pdf`. All 34 pages were rendered and visually reviewed, including figures, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with corrections and useful additions identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
