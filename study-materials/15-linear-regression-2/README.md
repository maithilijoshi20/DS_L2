# Lesson 15: Linear Regression 2

A fitted linear regression turns input values into a numerical prediction. This lesson builds on [Lesson 14](../14-linear-regression-1/README.md): interpret the coefficients, assess the fit, check whether inputs overlap too much, and examine prediction errors. A model can describe an association or predict well without establishing cause.

```text
inputs → fitted equation → predictions → evaluate on new cases
              ↓                                ↓
       interpret coefficients          inspect errors and limitations
```

## 1. Interpreting a fitted equation

Ordinary least squares (OLS) chooses coefficients to minimize the sum of squared training residuals, where a residual is observed outcome minus fitted prediction. For one input, the fitted equation is `ŷ = b₀ + b₁x`. The intercept `b₀` is the predicted outcome at `x = 0`; it may have little practical meaning if zero is outside the observed range. The slope `b₁` is the change in predicted outcome for a one-unit increase in `x`. A positive slope means predictions rise, a negative slope means they fall, and zero means a flat fitted line. The simple OLS slope is `b₁ = Σ[(xᵢ − x̄)(yᵢ − ȳ)] / Σ(xᵢ − x̄)²`, provided X varies.

For the slide's `ŷsales = 4000 + 2500 × customers`, one additional customer is associated with 2,500 more units of *predicted* sales; two customers give `ŷ = 9000`. These are predictions, not guaranteed actual sales or causal effects.

Multiple regression adds inputs: `ŷ = b₀ + b₁x₁ + b₂x₂ + …`. Each slope describes a change in prediction when that input increases by one unit **while the other inputs are held fixed**. In the slide's fitted report, the humidity coefficient is approximately `−0.8367`: a one-unit increase in humidity corresponds to a 0.8367 decrease in the predicted outcome, holding the other listed inputs fixed. The `coef` column contains estimates; standard errors and confidence intervals in the report express uncertainty, not prediction accuracy for individual cases.

**Source corrections:** Pages 2–3 use population `β` symbols for fitted estimates; `b` distinguishes estimates from unknown population coefficients. A zero fitted slope does not prove there is no relationship: a nonlinear pattern may remain, and an estimate of zero does not establish the population slope is zero. The page 4 example gives predicted, not necessarily actual, sales.

*Source: pp. 2–5; interpretation and uncertainty qualifications added.*

## 2. R² and adjusted R²

The **coefficient of determination**, `R²`, compares squared prediction errors with a baseline that always predicts the observed outcome mean `ȳ`. For observations `i = 1, …, n`:

```text
SST = Σ(yᵢ − ȳ)²      total variation around the mean
SSE = Σ(yᵢ − ŷᵢ)²      residual, or unexplained, sum of squares
SSR = Σ(ŷᵢ − ȳ)²      regression, or explained, sum of squares
R²  = 1 − SSE/SST
```

For OLS fitted and evaluated on the same data **with an intercept**, `SST = SSR + SSE`, so `R² = SSR/SST` and lies between 0 and 1 when `SST > 0`. An `R²` of 0.75 means the model reduces training SSE by 75% compared with the training-mean baseline; it does not mean 75% of individual outcomes are predicted correctly. In simple OLS with an intercept, `R²` equals the squared Pearson correlation between X and Y. The relationship does not generally hold for arbitrary models or evaluation data.

For example, if `SST = 100` and `SSE = 25`, then `R² = 0.75`. If `SSE = SST`, `R² = 0`: the squared error matches the mean baseline. If the evaluation SSE exceeds SST, `R²` is negative. This can happen on held-out data or with a model without an intercept; the `SSR/SST` formula and decomposition need not then apply. With constant observed outcomes, `SST = 0`, and the formula is undefined.

Adding predictors to an intercept OLS model cannot decrease **training** `R²`, even when the added inputs are unhelpful. **Adjusted R²** accounts for the number of fitted predictors:

```text
adjusted R² = 1 − [SSE / (n − p − 1)] / [SST / (n − 1)]
```

Here `p` is the number of predictors excluding the intercept; `n − p − 1` is the residual degrees of freedom, assuming full rank and `n > p + 1`. Adjusted R² can decrease when an added predictor does not improve fit enough, and it can be negative. For fixed `p`, its difference from R² tends to shrink as `n` grows, all else equal. Use it to compare fits on the same outcome and observations, not as a substitute for held-out evaluation.

**Source corrections:** Page 9 labels `k` as the number of predictors but uses `n − k`; if `k` excludes the intercept, the denominator must be `n − k − 1`. Its claim that adjusted R² is always strictly smaller has exceptions: equality occurs, for example, with a perfect fit. Pages 15 and 19–20 imply low R² identifies missing important predictors and high R² identifies an influential predictor; neither conclusion follows by itself. Noise, outcome variability, functional form, and data selection also matter. High training R² does not guarantee good new-case predictions, low error in outcome units, or stable coefficients.

*Source: pp. 6–9, 15, 17, 19–20, 23–24; evaluation conditions and baseline added.*

## 3. Multicollinearity

**Multicollinearity** occurs when one predictor is closely approximated by a linear combination of other predictors. For example, total floor area and the sum of room areas may contain almost the same information. Near redundancy makes their separate coefficients sensitive to small changes in data: standard errors can grow, confidence intervals widen, and signs or magnitudes can change. Predictions can still be useful within the observed range, but may become unstable in other settings. Perfect collinearity prevents a unique set of OLS coefficients.

The **variance inflation factor** for predictor `i` is

```text
VIFᵢ = 1 / (1 − Rᵢ²)
```

`Rᵢ²` is from an auxiliary regression of that predictor on the *other predictors*, including an intercept; it is not the outcome model's R². Under the usual homoscedastic OLS assumptions, VIF measures the factor by which that coefficient's variance is inflated relative to an otherwise comparable orthogonal predictor. If `Rᵢ² = 0.8`, VIF is `5`; its standard error is multiplied by `√5`, not 5. VIF is at least 1 in this setting; 1 indicates no linear association with the other predictors, not necessarily statistical independence. At perfect collinearity the denominator is zero and the VIF is not finite.

Cutoffs such as 5 or 10 are screening conventions, not universal rules. Inspect context and the intended use. Possible responses include collecting more informative data, combining related inputs, removing a redundant variable, or using regularization; each changes the model or interpretation. If removing variables, reassess one at a time rather than automatically dropping several. Removing an important confounder can distort remaining coefficients. **Source clarification:** Pages 12 and 21–22 present removal and a threshold as simple remedies; neither guarantees valid inference or a better predictive model.

*Source: pp. 10–12, 21–22; examples, conditions, and alternatives added.*

## 4. Predicting and evaluating

To predict, supply every input in the fitted equation using the same units and preprocessing used in training. For `ŷ = 2 + 3A + 5B`, values `A = 1`, `B = 2` give `15`. If training used scaling or categorical encoding, apply the training-fitted transformations to new cases as well. Fit preprocessing only on training data to avoid leakage, as in [Lesson 13](../13-machine-learning-overview-2/README.md). Predictions far beyond the training range are extrapolations and can be unreliable.

Compare predictions with outcomes on representative held-out cases. Let `eᵢ = yᵢ − ŷᵢ` and let `n` be the number of evaluated cases:

| Measure | Formula | Interpretation |
| --- | --- | --- |
| MAE | `Σ|eᵢ| / n` | Average absolute error, in outcome units. |
| MSE | `Σeᵢ² / n` | Average squared error; penalizes large errors more heavily. |
| RMSE | `√MSE` | Square root of MSE, in outcome units; sensitive to large errors. |
| MAPE | `(100/n) Σ|eᵢ/yᵢ|` | Average absolute percentage error, when every actual `yᵢ` is nonzero. |

For actual values `10, 20` and predictions `8, 24`, residuals are `2, −4`: MAE is `3`, MSE `10`, RMSE `√10 ≈ 3.16`, and MAPE `20%`. Smaller errors are better on the same dataset and scale. MAPE is undefined at zero and can be dominated by actual values near zero; choose a metric suited to the decision. R² is relative to an evaluation-set mean baseline, whereas these measures express error magnitude. Training performance is typically optimistic; keep the final test set separate from model selection.

*Source: pp. 13–15, 25–26; leakage, metric limitations, and example added.*

## 5. Comparing models and diagnosing residuals

**Information criteria** balance likelihood-based fit against the number of estimated parameters. For models fitted by maximum likelihood to the same observations and outcome, commonly used forms are

```text
AIC = −2 log(L̂) + 2k
BIC = −2 log(L̂) + k log(n)
```

`L̂` is the maximized likelihood, `k` counts estimated free parameters (including the intercept and, when estimated, error variance), and `n` is sample size. Lower values are preferred *among comparable candidates*. The penalty discourages unnecessary complexity; neither criterion guarantees best test performance or a true model. BIC penalizes each parameter more strongly than AIC when `n > e² ≈ 7.39`, not for every sample size.

**Source correction:** Page 16 equates maximized likelihood with `SSR/n`. This is not a likelihood formula. In normal-error OLS, the maximum-likelihood estimate of error variance is `SSE/n`; the likelihood also depends on the assumed probability distribution and its normalization. The criterion formulas require the actual maximized likelihood, not `SSR/n` substituted for it.

Plot residuals against fitted values and, when relevant, against time or individual inputs. Look for patterns, not just a high R²:

```text
residual vs fitted     random cloud        fan shape          curved pattern
                       •  • •  •           •    •             •         •
               zero ───•──•──•──       ───•──────•──       ───•───────•──
                        • •  •              •     •             • • •
                       no clear pattern    changing spread    missing curvature?
```

A cloud centered around zero with roughly constant spread is consistent with an adequate mean and constant variance; it does not prove the assumptions. A fan suggests heteroscedasticity; a curve suggests the fitted mean may need a nonlinear term or another specification. A trend may suggest a missing input or other misspecification, not a unique diagnosis. Examine time or group patterns for dependence. Investigate outliers in context. Diagnostics, appropriate uncertainty methods, and held-out performance complement one another.

**Source clarification:** Page 28 treats a random-looking residual plot as proof that model assumptions hold; it is only a limited diagnostic. A quadratic term is one possible response to curvature, not a universal requirement.

*Source: pp. 16–18, 27–28; comparability and diagnostic limitations added.*

---

*Source and readability note: Adapted from `[15차시] 선형 회귀분석 2_Lv2기본.pdf`. All 28 pages were rendered and visually reviewed, including figures, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with corrections and useful additions identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
