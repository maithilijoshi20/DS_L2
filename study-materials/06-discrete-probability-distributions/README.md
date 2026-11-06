# Lesson 6: Discrete Probability Distributions

A probability distribution describes how probability is allocated among the possible values of a quantity. [Lesson 4](../04-probability/README.md) introduced outcomes and events; [Lesson 5](../05-probability-calculation/README.md) showed how to combine their probabilities. Here we turn outcomes into numerical values and use distributions to model counts and yes/no results.

## 1. Random variables and distributions

A **random variable** is a rule that assigns a real number to each outcome of an experiment. Its value is uncertain before the outcome is known. For two distinguishable coin tosses, let `X` be the number of heads:

```text
Outcome:       TT      TH      HT      HH
                │       │       │       │
Value of X:     0       1       1       2
```

`X` is the rule; `X = 1` is the event `{TH, HT}`, not the random variable itself. Different outcomes can produce the same value. If the tosses are independent and fair, each outcome has probability 1/4, so `P(X = 1) = 1/2`.

A **discrete** random variable takes values in a finite or countably infinite set, such as a die result or the number of arrivals in an hour. A continuous model can describe measurements such as time or height over a range; its interval probabilities are described by a density. These are modeling choices: a measured height rounded to a millimeter is recorded discretely.

*Source: pp. 2–3, 15–16; probability assumptions added.*

## 2. PMF, density, and cumulative probability

The **probability mass function** (PMF) of a discrete variable assigns the probability of each exact value: `p(x) = P(X = x)`. Every mass is nonnegative, and the masses over all possible values sum to 1. Values outside the support have mass 0. For the fair two-toss example:

| Heads `x` | 0 | 1 | 2 |
|---|---:|---:|---:|
| PMF `p(x)` | 1/4 | 1/2 | 1/4 |
| CDF `F(x)` | 1/4 | 3/4 | 1 |

The **cumulative distribution function** (CDF) is `F(x) = P(X ≤ x)`: add all masses at or below x. Thus `F(1) = 3/4`, while `p(1) = 1/2`. The CDF is defined for all real x, stays between 0 and 1, never decreases, and approaches 0 to the far left and 1 to the far right. For this discrete example it is a step function, jumping by the mass at each value:

```text
x:      below 0     0 to <1     1 to <2     2 and above
F(x):      0          1/4          3/4            1
```

For any distribution, `P(a < X ≤ b) = F(b) − F(a)` when `a ≤ b`. Endpoints matter for discrete values: `P(0 < X ≤ 1) = F(1) − F(0) = 1/2`; including 0 would add its mass.

For a continuous variable with density `f`, the probability over `[a, b]` is the **area under the density** from a to b, `∫ₐᵇ f(x) dx`. The density is nonnegative and its total area is 1. Its height at a point is not the probability at that point and can exceed 1; individual points have probability 0 in a continuous distribution. A CDF applies to both discrete and continuous distributions.

**Source corrections:** Page 5 reverses the integral limits and uses `p(x)` instead of `f(x)` for density nonnegativity. An interval can have probability zero, contrary to the slide's strict `> 0` statement. Page 6's CDF is nondecreasing, not necessarily strictly increasing; it can be flat.

*Source: pp. 4–6, 17–18; table, endpoint example, and corrections added.*

## 3. Bernoulli: one binary result

A **Bernoulli trial** has two outcomes labeled success and failure. “Success” simply names the event being counted; it could mean a defective product. Let `X = 1` for success and `X = 0` for failure, with success probability `p`, where `0 ≤ p ≤ 1`:

```text
P(X = 1) = p          P(X = 0) = 1 − p
P(X = k) = pᵏ(1 − p)¹⁻ᵏ,  k ∈ {0, 1}
```

For example, if `p = 0.8`, the masses at 1 and 0 are 0.8 and 0.2. At `p = 0` or `p = 1` the result is certain. The expected value is `E[X] = p`, and the variance is `p(1 − p)`: these describe the model's center and spread, not a guarantee about one trial. Expectation is a probability-weighted average, connecting to the averages in [Lesson 2](../02-statistics/README.md).

Page 7 describes independence and constant p as trial characteristics. A single Bernoulli variable needs only the binary probabilities above; independence and a common p are additional assumptions for the repeated-trial binomial model below.

*Source: pp. 7–9; mean, variance, and qualification added.*

## 4. Binomial: successes in a fixed number of trials

If `n` Bernoulli trials are independent and each has the same success probability `p`, their success count `X` has a **binomial distribution**, written `X ~ Binomial(n, p)`. Its possible values are `0, 1, …, n`:

```text
P(X = k) = C(n, k) pᵏ(1 − p)ⁿ⁻ᵏ
C(n, k) = n! / [k!(n − k)!]
E[X] = np                 Var(X) = np(1 − p)
```

The factor `pᵏ(1 − p)ⁿ⁻ᵏ` is the probability of one particular sequence with k successes. `C(n, k)` counts the ways to choose the k success positions; `n!` means `n × (n − 1) × … × 1`, with `0! = 1`. Add the probabilities of the possible sequences to get the count's probability.

For five independent products, each defective with probability 0.1, the probability of exactly two defects is `C(5, 2) × 0.1² × 0.9³ = 10 × 0.01 × 0.729 = 0.0729`. The expected count is `5 × 0.1 = 0.5`; this need not be a possible result in one batch. Random selection alone does not ensure independence: sampling without replacement from a finite batch generally changes the probabilities between draws.

For `n = 20, p = 0.5`, the distribution is symmetric about 10, its expected count, as in the slide's plot. Other p values change its shape. **Source correction:** Page 10 prints the failure exponent as `1 − k`; it must be `n − k`, as used correctly in the worked answer on p. 20.

*Source: pp. 10–11, 19–20; variance and sampling qualification added.*

## 5. Poisson: counts over an interval

A **Poisson distribution** models a nonnegative count over a specified interval of time, distance, or area. If `X ~ Poisson(λ)`, the parameter `λ` is the expected count in that entire interval:

```text
P(X = k) = e⁻λ λᵏ / k!,   k = 0, 1, 2, …
E[X] = λ                   Var(X) = λ
```

Here `e ≈ 2.71828`. For a constant rate of 3 calls per hour, the expected count in one hour is `λ = 3`; over two hours it is 6. Under the Poisson model, exactly four calls in one hour has probability `e⁻³ × 3⁴ / 4! ≈ 0.1680`. A mean of 3 alone does not establish that calls follow this distribution.

A **homogeneous Poisson process** is one way to justify the model: counts in disjoint intervals are independent, and the average rate per unit is constant. Over a very short interval, one event has probability approximately rate × interval length, while two or more have probability negligible relative to that length. It can model arrivals, production defects over time, or flaws along a length when these assumptions are plausible. Clustering, changing rates, or dependent arrivals may make it a poor fit.

The Poisson distribution also approximates `Binomial(n, p)` when n is large and p small, with `λ = np`; in the limiting result, n grows while np stays fixed. This is an approximation for finite n, not an identity.

**Source corrections:** Page 13 incorrectly stops Poisson values at n: the support continues indefinitely. Page 12 and the quiz on pp. 23–24 say at most one event can occur in a very short interval; two or more remain possible, though increasingly unlikely as the interval shrinks. Independence applies to counts in **disjoint** intervals, not arbitrary overlapping intervals. The distribution parameter is the expected count in the chosen interval, so multiply a per-unit rate by interval length. The slide's example on pp. 21–22 implicitly assumes a Poisson model in addition to its stated average.

*Source: pp. 12–14, 21–24; process precision and interval scaling added.*

## What to carry forward

Choose the variable and interval first. A PMF gives exact-value probabilities; a CDF accumulates them. Bernoulli models one binary result, binomial counts successes in a fixed number of independent equal-probability trials, and Poisson models counts over an interval under suitable assumptions. A model's probabilities are only as useful as its fit to the situation.

---

*Source and readability note: Adapted from `[6차시] 이산 확률 분포_Lv2기본.pdf`. All 24 pages were rendered and visually reviewed, including graphs, quiz questions, and answer panels; page numbers count from the title page. The guide covers their substantive concepts without quiz format, labels additions and source corrections, and checks the numerical examples. It is a concise concept guide, not a certified translation; lecture audio and instructor commentary were unavailable.*
