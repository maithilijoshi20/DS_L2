# Lesson 14: Linear Regression 1

**Linear regression** models a numerical outcome using one or more inputs. It can summarize an association and predict an outcome for a new case. For example, a business might use advertising spending to predict sales. As in [Lesson 11](../11-correlation-analysis/README.md), association alone does not establish that advertising caused the sales: season, price, and other factors may matter. Linear regression is a supervised learning method, connecting the numerical prediction task in [Lesson 12](../12-machine-learning-overview-1/README.md) to an explicit model.

```text
observed inputs and outcomes → fit coefficients → predict for a new input
                                  ↓
                         inspect fit and uncertainty
```

Applications include income and consumption in economics, price and demand in marketing, education and income in social science, and temperature and material strength in engineering. Regression is generally used for a numerical target; a categorical classification outcome calls for a suitable classification model. Linear models can be simple and interpretable, but outliers and strongly related inputs can make results sensitive or unstable.

*Source: pp. 2–4, 19–20, 27–28; causal and classification qualifications added.*

## 1. The model and its coefficients

For one input, **simple linear regression** describes the outcome `Y` as

```text
Y = β₀ + β₁X + ε                 population model
ŷ = b₀ + b₁x                     fitted prediction
```

`X` is the input (independent variable), `Y` the outcome (dependent variable), `β₀` the population intercept, `β₁` the population slope, and `ε` the unobserved error. The fitted coefficients `b₀` and `b₁` are estimates from a sample. If the conditional mean of the error is zero, `β₀ + β₁x` is the population mean outcome at input `x`; `ŷ` estimates that mean. The intercept is the predicted mean at `x = 0`, which may be outside the meaningful range of the data. The slope is the predicted change in the mean outcome for a one-unit increase in X, in outcome units per input unit.

**Multiple linear regression** uses two or more inputs for one outcome:

```text
Y = β₀ + β₁X₁ + β₂X₂ + ... + βₚXₚ + ε
```

For instance, with spending and price as inputs, `β₁` describes the change in the conditional mean associated with one more unit of spending **holding price fixed**. This is an adjusted association; controlling for measured variables does not automatically remove all confounding or identify a causal effect. A coefficient can be unstable when inputs are highly correlated (**multicollinearity**), since the data may not distinguish their separate contributions well. Perfect collinearity prevents a unique ordinary least-squares solution.

“Linear” refers to the coefficients: a model can include a transformed input such as `X²` and still be linear in its coefficients. **Source clarification:** Pages 2–3 describe a linear relationship and a “pure effect” after control. The model is linear in its parameters, and adjustment alone does not establish a causal effect. Page 2 calls linear regression a generalized linear model (GLM): normal linear regression with an identity link is a special case of a GLM, but linear regression need not assume normal errors merely to fit a line.

*Source: pp. 2–6; conditional-mean interpretation, transformed inputs, and causal qualifications added.*

## 2. Errors, residuals, and least squares

An **error** `εᵢ` is an observation's difference from the unobserved population conditional mean. A **residual** `eᵢ = yᵢ − ŷᵢ` is its observed difference from the fitted prediction. Residuals can be calculated from the data; true errors generally cannot, because the population coefficients are unknown.

```text
Y        • observed point
         │ residual = observed − fitted
         × fitted line
        /  
       /                         X →
```

**Ordinary least squares (OLS)** chooses coefficients that minimize the sum of squared residuals:

```text
SSE = Σ(yᵢ − ŷᵢ)² = Σ[yᵢ − (b₀ + b₁xᵢ)]²
```

Squaring prevents positive and negative residuals from cancelling and gives large residuals more weight. That also makes OLS sensitive to unusual points, especially those with unusual input values. Investigate such observations in context; do not automatically delete them. With an intercept and varying X, the simple-regression estimates are

```text
b₁ = Σ[(xᵢ − x̄)(yᵢ − ȳ)] / Σ(xᵢ − x̄)² = sample covariance(X,Y) / sample variance(X)
b₀ = ȳ − b₁x̄
```

The common `n − 1` factors in sample covariance and variance cancel. The fitted line passes through `(x̄, ȳ)`. If all X values are identical, the slope is not identifiable from these data. Unlike correlation, the slope has units and changes if measurement units change.

For the slide's five pairs `(1,2), (2,3), (3,5), (4,4), (5,6)`, `x̄ = 3`, `ȳ = 4`, the numerator is `9`, and the denominator is `10`. Thus `b₁ = 0.9`, `b₀ = 4 − 0.9(3) = 1.3`, and `ŷ = 1.3 + 0.9x`. At `x = 3`, the prediction is `4`; the observed `5` has residual `1`. Across all five points, SSE is `1.9`.

**Source notation correction:** Pages 8–10 use population-style `β` symbols for quantities estimated from the sample. The formulas here use `b` for fitted estimates; OLS minimizes residual squares, not the unobservable population error squares.

*Source: pp. 7–10, 21–22; intercept, example calculations, and interpretation added.*

## 3. Uncertainty in a slope

A different sample would usually give a different estimated slope. Under the simple linear model with an intercept, fixed X values, independent errors of mean zero and common variance `σ²`, the sampling variance of the slope is

```text
Var(b₁ | X) = σ² / Σ(xᵢ − x̄)²
estimated SE(b₁) = √[s² / Σ(xᵢ − x̄)²],   s² = SSE / (n − 2)
```

The **standard error** (SE) estimates the standard deviation of the slope estimate across repeated samples under the model. More error variation increases uncertainty; more spread in X, measured by the denominator, reduces it. Changing the mean of X alone does not change that spread. A larger sample *typically* improves precision when it adds information, but sample size alone does not guarantee a narrower interval for every realized dataset.

For independent normally distributed errors with common variance, a two-sided confidence interval for the population slope is

```text
b₁ ± t* × SE(b₁),    t* = t critical value with n − 2 degrees of freedom
```

For a 95% interval, `t*` leaves 2.5% in each tail. In repeated samples under the assumptions, 95% of intervals produced by this procedure would contain the fixed population slope. It is not a 95% probability that this particular fixed coefficient lies in the already calculated interval. A higher confidence level uses a larger critical value and gives a wider interval, all else equal.

For the five pairs above, `s² = 1.9/3 ≈ 0.633`, `SE(b₁) = √(0.633/10) ≈ 0.252`, and `t* ≈ 3.182` for 3 degrees of freedom. The 95% interval is approximately `0.9 ± 0.801`, or `(0.10, 1.70)`. This quantifies uncertainty about a slope under the model, not the uncertainty of an individual future outcome. With heteroscedastic or dependent errors, the displayed classical SE and interval can be misleading; suitable robust or dependence-aware methods may be needed.

**Source clarification:** Pages 11–13 and 23–26 present the variance formula and sample-size effects without all their conditions. `σ²` is usually unknown and must be estimated; more observations do not mechanically guarantee a smaller realized interval. The formula concerns spread of X around its mean, not its mean itself.

*Source: pp. 11–13, 23–26; estimation, interval interpretation, and conditions added.*

## 4. Assumptions and diagnostics

OLS can always be attempted without normally distributed data, but the meaning of coefficients and validity of classical uncertainty estimates depend on assumptions. Inspect the data and residuals in context; diagnostics suggest problems but cannot prove every assumption.

- **Conditional mean and functional form:** The model should describe the mean outcome adequately, with `E(ε | X) = 0`. A curved residual pattern can suggest a missing nonlinear term. An omitted relevant variable or measurement error in an input can also distort estimates. **Source correction:** Page 14 calls zero conditional error mean “linearity”; it is a related but stronger requirement than linearity in coefficients, and zero correlation between an input and error alone does not guarantee zero conditional mean.
- **Constant error variance (homoscedasticity):** The conditional spread of errors is constant across input values. A fan-shaped residual plot may suggest changing variance (**heteroscedasticity**), perhaps because variability rises with income or groups have different spreads. Constant variance does not mean every individual prediction is equally accurate or certain. Heteroscedasticity does not by itself bias OLS coefficients when the conditional mean assumption holds, but it affects classical standard errors.
- **Independence:** Errors across observations should be independent for the classical model. Time trends, seasonal cycles, geographic proximity, or clustered records can induce dependence. Random sampling can help but does not guarantee independent errors in every design. Dependence can make ordinary standard errors unreliable.
- **Normality:** For the exact small-sample t interval above, errors conditional on X are assumed normal. With the other model assumptions, this makes `Y | X` normal around the regression mean; the overall distribution of Y need not be normal. Normality is not required to calculate OLS, and other inference approaches can be valid under different conditions.

```text
Residual vs fitted:                  Normal Q–Q plot:
random cloud  → no obvious pattern   points near line → roughly normal residuals
curve or fan  → investigate         systematic bends → investigate tails/shape
```

A **Q–Q plot** compares ordered residual quantiles with theoretical normal quantiles. Large systematic departures from a straight line, including at the tails, can indicate nonnormality; a few departures are not proof. Outliers, skewness, heavy tails, or a mixture of groups can produce departures. Use residual-versus-fitted plots to look for curvature and unequal spread, and residuals against time or groups to investigate dependence. Diagnostics are imperfect, especially with small samples.

**Source clarification:** Pages 15–18 conflate constant variance with equal prediction precision, and describe normality without distinguishing conditional from overall outcome distributions. The distinctions above matter for interpreting both predictions and intervals.

*Source: pp. 14–18; diagnostic guidance and qualifications added.*

For prediction, evaluate the fitted procedure on held-out cases using a suitable error metric, as in [Lessons 12](../12-machine-learning-overview-1/README.md) and [13](../13-machine-learning-overview-2/README.md). A good fit to training data does not ensure accurate new predictions. Be especially cautious when predicting far outside the observed input range. If there are more coefficients than independent observations, ordinary least squares cannot uniquely identify all coefficients without further constraints; regularization is one possible alternative, with its own assumptions and tradeoffs. A nonlinear relationship may sometimes be modeled using transformed inputs, but an unmodified straight-line mean may be inappropriate.

*Source: pp. 27–28; held-out evaluation, extrapolation, and regularization context added.*

---

*Source and readability note: Adapted from `[14차시] 선형 회귀분석 1_Lv2기본.pdf`. All 28 pages were rendered and visually reviewed, including figures, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with corrections and useful additions identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
