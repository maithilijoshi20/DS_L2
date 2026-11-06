# Lesson 7: Continuous Probability Distributions

[Lesson 6](../06-discrete-probability-distributions/README.md) modeled counts using probability masses. Continuous distributions instead model quantities such as idealized waiting times or measurements over a range. They are models: recorded measurements may be rounded, and a particular distribution should be chosen for its assumptions and fit, not just because a variable is numerical.

## 1. Density and probability

For a continuous random variable `X`, a **probability density function** (PDF) `f(x)` describes how probability is distributed. Probability is the area under the curve over an interval:

```text
                  density f(x)
                       /\
                      /██\
                     /████\
              ______/|████|\______  x
                     a    b
                 shaded area = P(a ≤ X ≤ b)

P(a ≤ X ≤ b) = ∫ₐᵇ f(x) dx       f(x) ≥ 0       ∫₋∞∞ f(x) dx = 1
```

The integral adds up density across the interval. Density at a point is a height, not the probability of that exact value; it can exceed 1. For a continuous distribution `P(X = x) = 0`, so including or excluding interval endpoints does not change its probability. The **cumulative distribution function** (CDF), `F(x) = P(X ≤ x)`, is the area to the left of x, and `P(a < X ≤ b) = F(b) − F(a)`. A CDF never decreases and can be flat.

**Source correction:** Page 2 reverses the integral limits, writes `p(x)` for the density's nonnegativity, and says every interval probability is strictly positive. The correct limits and `f(x) ≥ 0` are above; an interval can have probability zero.

*Source: pp. 2, 23–24; CDF formula and corrections added.*

## 2. Uniform distribution

`X ~ Uniform(a, b)`, with `a < b`, has constant density `1/(b − a)` between its lower and upper bounds and density 0 outside. Equal-length intervals inside the range have equal probability. Its mean is `(a + b)/2`, the midpoint; its CDF rises linearly from 0 at a to 1 at b.

For `Uniform(2, 8)`, the density is `1/6`, the mean is 5, and `P(3 ≤ X ≤ 5) = (5 − 3)/6 = 1/3`. The density is not `1/8`; the width is 6. The endpoint values assigned to the density do not affect probabilities.

*Source: pp. 3–4, 15–16; interval example added.*

## 3. Normal distribution

The **normal**, or Gaussian, distribution `X ~ N(μ, σ²)` is a symmetric bell-shaped density centered at mean `μ`, with variance `σ²` and standard deviation `σ > 0`. Its density is

```text
f(x) = [1 / (σ√(2π))] exp(−(x − μ)² / (2σ²)),   −∞ < x < ∞
```

Changing `μ` shifts the curve; increasing `σ` spreads it out. Some natural measurements are approximately normal, but normality is an assumption to check, not a universal rule. The **standard normal** `Z ~ N(0, 1)` has mean 0 and standard deviation 1. For a normal X, standardizing gives `Z = (X − μ)/σ`; it measures distance from the mean in standard deviations.

```text
             central 95% (approximately)
                 .-''''-.
              .-'        '-.
      _______/______|_______\_______
           −1.96    0       1.96        standard deviations
         2.5% tail          2.5% tail
```

Approximately 98% lies between −2.33 and 2.33, and 99% between −2.58 and 2.58; the displayed cutoffs are rounded. For example, if `X ~ N(100, 10²)`, then `P(80.4 ≤ X ≤ 119.6) ≈ 0.95`. These are model probabilities, not promises that every dataset has those proportions.

*Source: pp. 5–6; standardization and example added.*

## 4. Student's t distribution

The **t distribution** is symmetric about 0 and has heavier tails than the standard normal: relatively more probability lies far from the center. Its parameter, degrees of freedom `ν > 0`, controls its shape. As `ν` increases, the distribution approaches the standard normal; at `ν = 1` it is not normal.

A common use is inference about a population mean when its standard deviation is unknown. For an independent sample of size `n` from a normal population, `(sample mean − μ) / (sample standard deviation / √n)` has a t distribution with `n − 1` degrees of freedom. Estimating the mean imposes a constraint on deviations: once `n − 1` deviations are known, the last is determined because they sum to zero. The t distribution is useful beyond small samples; the normal-population assumption matters for this exact result.

*Source: pp. 7–8, 17–18; sampling conditions and degrees-of-freedom explanation added.*

## 5. Exponential distribution: waiting for one event

In a homogeneous Poisson process, events occur independently across disjoint intervals at constant rate `λ > 0`. As introduced in [Lesson 6](../06-discrete-probability-distributions/README.md), the number of events in an interval is Poisson; the waiting time `T` to the first event is **exponential**:

```text
f(t) = λe⁻λᵗ,  t ≥ 0       P(T > t) = e⁻λᵗ       E[T] = 1/λ
```

Here `λ` is the average event rate per unit time, so the mean wait uses the reciprocal time units. If the rate is 2 per hour, the mean wait is half an hour and `P(T > 1 hour) = e⁻² ≈ 0.1353`.

The exponential distribution is **memoryless**: `P(T > s + t | T > s) = P(T > t)` for `s, t ≥ 0`. Having waited s without an event does not change the distribution of the additional wait under this model. It does not say that a real system cannot age, or that the probability of an event over a longer future interval is unchanged. A constant average rate alone does not establish an exponential waiting time.

*Source: pp. 9–10, 19–20; process assumptions and numerical example added.*

## 6. Gamma and chi-square distributions

The **gamma distribution** generalizes the exponential. Using shape `α > 0` and **rate** `β > 0`, its density for `x > 0` is

```text
f(x) = βᵅ xᵅ⁻¹ e⁻ᵝˣ / Γ(α)       E[X] = α/β
Γ(α) = ∫₀∞ tᵅ⁻¹e⁻ᵗ dt
```

The gamma function `Γ` normalizes the density; for positive integers, `Γ(n) = (n − 1)!`. When `α = 1`, gamma is exponential with rate β. When `α` is a positive integer, it models the wait for the αth event in a homogeneous Poisson process of rate β. More generally α need not be an event count. Some references use **scale** `θ = 1/β` instead of rate, so check the parameter convention. As shape grows, a suitably centered and scaled gamma approaches a normal distribution, and its skew decreases.

The **chi-square distribution** with `ν` degrees of freedom is the distribution of the sum of squares of `ν` independent standard normal variables, for positive integer `ν`:

```text
Z₁, …, Zν independent N(0, 1)  →  Z₁² + … + Zν² ~ χ²(ν)
χ²(ν) = Gamma(shape = ν/2, rate = 1/2)       E[χ²] = ν
```

It is supported on nonnegative values, generally right-skewed, and becomes more symmetric as degrees of freedom increase. It is used in statistical inference, including methods concerning variance. The gamma identity is exact, not merely an approximation. A gamma shape of zero is invalid; it does not produce a uniform distribution.

**Source clarifications:** Pages 11–12 describe α as an event count, but noninteger shapes are valid. The normal approximation for growing shape is understood after suitable centering and scaling. Page 12 says the stated gamma parameters approach chi-square; they actually define it exactly.

*Source: pp. 11–14, 21–22; parameter conventions, assumptions, and clarifications added.*

## What to carry forward

Area under a density gives interval probability. Uniform has constant density on a bounded interval; normal describes a symmetric bell; t accounts for estimated spread in common mean-inference settings; exponential models a Poisson-process wait; gamma includes sums of such waits; and chi-square is a gamma distribution arising from squared standard normals. Their shapes and parameters answer different modeling questions.

---

*Source and readability note: Adapted from `[7차시] 연속 확률 분포_Lv2기본.pdf`. All 24 pages were rendered and visually reviewed, including plots, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with additions and source corrections identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
