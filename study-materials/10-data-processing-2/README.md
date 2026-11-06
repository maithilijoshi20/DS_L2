# Lesson 10: Data Processing 2

After addressing missing values and unusual observations as in [Lesson 9](../09-data-processing-1/README.md), we may transform variables into forms better suited to analysis. A transformation changes the representation, so choose one for a reason and interpret results on the appropriate scale. A **feature** is an input variable used by a model.

```text
Numerical values ──> reshape a distribution (power or log)
                 └─> change scale (min–max or standardization)
Categories      ──> encode as numerical indicators or ordered codes
```

## 1. Reshaping numerical data

A distribution is **right-skewed** when its longer tail extends toward large values; left-skew has a longer tail toward small values. As in [Lesson 2](../02-statistics/README.md), extreme values can pull the mean and affect spread. A nonlinear transformation changes distances between values and can reduce skew or the influence of large observations. It can sometimes help meet an analysis assumption, but neither symmetry nor normality is guaranteed; inspect the result and check the assumption relevant to the particular method. Some methods assume normal *errors*, not normally distributed inputs. Transforming also changes the meaning of summaries and model effects.

A **power transformation** uses `y = xᵖ`. For nonnegative data, `p = 0.5` gives the square root: `1, 4, 100 → 1, 2, 10`. It compresses large values relative to small ones and may reduce right skew. For suitable positive data, a power greater than 1, such as squaring, can sometimes reduce left skew. These are starting points, not guarantees. The reciprocal `x⁻¹ = 1/x` is another power transformation; it requires nonzero values and reverses their order on positive inputs. Domains depend on the power: a real square root requires `x ≥ 0`.

**Source correction:** Pages 3–4 and 16–17 describe powers of `x` but show `aˣ`, an exponential function. `xᵖ` and `aˣ` are different functions. The example is a square-root *power* transformation, and `x⁻¹` is a reciprocal, not an exponential function of `x`.

A **log transformation** `y = log₁₀(x)` requires `x > 0`. It turns multiplicative ratios into additive differences: `1, 10, 100 → 0, 1, 2`. Larger values are compressed relative to smaller ones, often reducing right skew and the influence of a large upper tail. Values between 0 and 1 have negative logarithms. The original value is recovered with `x = 10ʸ`. A shifted log such as `log(1 + x)` can handle zero for nonnegative data, but changes the transformation and its interpretation; it does not make negative inputs universally valid.

**Source clarification:** Pages 5 and 19 discuss reducing variance, but a log does not universally reduce skew, outlier influence, or numerical variance. Its effect depends on the data, units, and log base. For instance, `0.001` and `0.002` become approximately `−3` and `−2.699` under base 10: their numerical separation increases from `0.001` to about `0.301`. Variances before and after also have different units, so their numerical sizes alone are not a scale-free comparison.

*Source: pp. 2–6, 16–19, 24–25; domains, interpretation, and qualifications added.*

## 2. Scaling numerical features

Scaling puts features on comparable numerical scales. For example, a distance-based method comparing age in years with income in currency units can be dominated by income's larger numbers. Scaling changes that weighting; it does not establish that the features are equally important. The following positive linear transformations preserve order, relative spacing, and distribution shape, including skew. They do not make skewed data normal.

**Min–max scaling** maps the minimum of a reference dataset to 0 and its maximum to 1:

```text
scaled x = (x − minimum) / (maximum − minimum)
minimum ───────── x ───────── maximum
   0        relative position      1
```

For `[1, 3, 4, 6, 8, 9, 10]`, `6` becomes `(6 − 1)/(10 − 1) = 5/9 ≈ 0.556`. The formula is undefined when all reference values are identical. An extreme minimum or maximum can squeeze the other values into a narrow interval. A later value outside the reference range can scale below 0 or above 1.

**Source correction:** Page 21's explanation calls the maximum 9; it is 10. The stated result is nevertheless correct because the denominator is the range, 9.

**Standardization** expresses distance from the mean in standard deviations:

```text
z = (x − mean) / standard deviation
```

For a mean of 50 and SD of 10, `x = 65` has `z = 1.5`, meaning 1.5 SD above the mean. The reference data have mean 0 and SD 1 after transformation, using the same SD convention, provided the SD is nonzero. The mean and SD themselves are sensitive to outliers. Standardizing a normal population with its own mean and SD gives the standard normal distribution from [Lesson 7](../07-continuous-probability-distributions/README.md); standardizing arbitrary data preserves their shape, not normality. Values need not lie between 0 and 1. Terminology varies: “normalization” sometimes refers broadly to scaling, not making data normally distributed.

**Added practice:** For predictive ML, split data before learning preprocessing parameters. Calculate the minimum, maximum, mean, SD, or other learned choices on the training set only; apply those same values to validation, test, and future data. Otherwise information from evaluation data can leak into training and give an overly optimistic assessment. Handle constant features and missing values explicitly, and retain the parameters needed to process future records consistently.

*Source: pp. 7–11, 20–21, 24–25; example correction and ML practice added.*

## 3. Encoding categorical data

Categorical values identify groups. **Nominal** categories have no inherent order, like colors; **ordinal** categories have an order, like low, medium, and high. Encoding makes categories available to methods that require numerical inputs, but numbers do not automatically make category differences meaningful.

**One-hot encoding** creates one 0/1 indicator per category. For a single color per row:

```text
color        red  green  blue
red           1     0      0
green         0     1      0
blue          0     0      1
```

This represents nominal categories without imposing an order. With many categories it can create many columns. If every row has exactly one known category, one indicator can be omitted: with red and green retained, `0, 0` identifies blue as the reference category. In a linear model with an intercept, omitting one avoids exact redundancy; other algorithms may retain all indicators. Missing or unseen categories need an explicit policy so they are not accidentally confused with the reference.

**Label encoding** assigns distinct integer codes, such as red → 0, green → 1, blue → 2. The codes are identifiers; their order and distances are arbitrary. Some algorithms interpreting them as numerical inputs may infer a false order or distance, so one-hot encoding is often safer for nominal input features. **Source correction:** Page 14 calls these codes continuous numerical data. They are discrete labels; `0` through `n − 1` is a common convention, not a mathematical requirement.

**Ordinal encoding** assigns codes respecting a meaningful order, for example low → 0, medium → 1, high → 2. It is compact and retains rank, but equal code differences do not prove equal real-world gaps. A model treating the codes as numbers may impose that additional assumption. Page 15 illustrates descending ranks for numerical values: ranks preserve order in the chosen direction but discard the original distances. Ranking measured numbers is not necessarily the same task as encoding ordinal categories.

Arithmetic on codes must be justified by their meaning: averaging arbitrary color codes is meaningless, while averaging a binary indicator can yield a category proportion. Keep the category mapping consistent between training and later data, and plan for unseen and missing categories.

*Source: pp. 12–15, 22–23; model implications and missing-category qualifications added.*

## What to carry forward

Power and log transformations can change shape; min–max scaling and standardization change location and scale while preserving shape. Encoding represents categories for computation, but must respect what their order and differences mean. Choose and assess each transformation in the context of the analysis.

---

*Source and readability note: Adapted from `[10차시] 데이터 처리 2_Lv2기본.pdf`. All 25 pages were rendered and visually reviewed, including figures, tables, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with corrections and useful additions identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
