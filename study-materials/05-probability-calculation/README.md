# Lesson 5: Calculating Probabilities

Probability calculations answer different questions depending on what is known. [Lesson 4](../04-probability/README.md) introduced events and their combinations. Here, an event is a set of possible outcomes; `A ∩ B` means both events occur, `A ∪ B` means at least one occurs, and `Aᶜ` means A does not occur. The rules below show how to combine probabilities and update them when new information is available.

## 1. Conditional probability and independence

`P(A | B)` reads “the probability of A given B”: restrict attention to outcomes where B occurred, then ask what fraction of their probability also belongs to A. Provided `P(B) > 0`,

```text
P(A | B) = P(A ∩ B) / P(B)
```

The denominator is the probability of the condition. For a fair die, let A be “even” and B be “at most 3.” Within B, the equally likely outcomes are `{1, 2, 3}`; only 2 is even, so `P(A | B) = 1/3`. In the other direction, `P(B | A) = 1/3` here, but the two conditional probabilities need not be equal: for A = “roll 2” and the same B, `P(A | B) = 1/3` while `P(B | A) = 1`.

A and B are **independent** when `P(A ∩ B) = P(A)P(B)`. If `P(B) > 0`, this is equivalent to `P(A | B) = P(A)`: learning B does not change A's probability under the model. Independence is symmetric; when both conditioning events have positive probability, `P(B | A) = P(B)` too. Independent fair coin tosses provide an example: the chance of heads on the second remains 1/2 after heads on the first. Independence must be justified by the model or evidence; separate production lines or people's birthdays are not automatically independent.

Independence also extends to complements: if A and B are independent, so are A and `Bᶜ`, `Aᶜ` and B, and `Aᶜ` and `Bᶜ`. For instance, `P(A ∩ Bᶜ) = P(A) − P(A ∩ B) = P(A)(1 − P(B))`.

**Disjoint** events cannot occur together: `A ∩ B = ∅`. Disjoint events with positive probabilities cannot be independent, because their intersection has probability 0, while `P(A)P(B) > 0`. An intersection can have probability 0 without being empty in an infinite model; page 4's zero-probability condition alone does not establish disjointness.

If `P(A | B) > P(A)`, B is positively associated with A; if it is smaller, the association is negative. Such comparisons describe probabilities, not necessarily causation. Page 7's language that B “promotes” or “suppresses” A should not be read as proof of a causal effect. Conditional formulas on pp. 5–6 also require positive denominators; independence itself remains defined by the product rule even for zero-probability events.

*Source: pp. 2–7; die examples, qualifications, and corrections added.*

## 2. Combining events

### “Or”: addition and overlap

Adding `P(A)` and `P(B)` counts outcomes in both events twice, so subtract their overlap once:

```text
P(A ∪ B) = P(A) + P(B) − P(A ∩ B)
```

For example, if 60% of employees speak English, 40% speak Chinese, and 25% speak both, 75% speak at least one: `0.60 + 0.40 − 0.25 = 0.75`. If the events are disjoint, their intersection is empty and the overlap term is zero.

For three events, the **inclusion–exclusion** rule accounts for all overlaps:

```text
P(A ∪ B ∪ C) = P(A) + P(B) + P(C)
                − P(A ∩ B) − P(A ∩ C) − P(B ∩ C)
                + P(A ∩ B ∩ C)
```

The triple intersection was counted three times in the first line and subtracted three times in the second, so add it once to count it exactly once. Independence is not required for either addition rule.

### “And”: multiplication

Rearranging conditional probability gives the general multiplication rule:

```text
P(A ∩ B) = P(A)P(B | A) = P(B)P(A | B)
```

Each conditional expression requires its conditioning event to have positive probability. For example, draw two cards without replacement from a standard 52-card deck. The probability both are aces is `(4/52) × (3/51) = 1/221`: after an ace, only three aces remain among 51 cards. If A and B are independent, the conditional probability equals the unconditional probability, giving `P(A ∩ B) = P(A)P(B)`. Thus independent pass probabilities 0.9 and 0.8 give a probability of both passing of 0.72.

**Source correction:** Page 11 omits `P` before `(A | B)` in its second product; the correct factor is `P(A | B)`.

### “Not”: complements

`P(Aᶜ) = 1 − P(A)`. De Morgan's laws translate complements of combined events:

```text
(A ∪ B)ᶜ = Aᶜ ∩ Bᶜ     neither A nor B
(A ∩ B)ᶜ = Aᶜ ∪ Bᶜ     at least one of A and B does not occur
```

For independent A and B with probabilities 0.4 and 0.3, the probability that at least one does not occur is `1 − (0.4 × 0.3) = 0.88`. This includes outcomes where neither occurs. On p. 27 the equation and answer are correct, but the check mark shown in the overlap of A and B can mislead: that overlap is excluded from `Aᶜ ∪ Bᶜ`.

*Source: pp. 8–12, 20–23, 26–27; card example and diagram clarification added.*

## 3. Total probability: combine separate paths

A **partition** divides the sample space into nonempty, mutually disjoint events `A₁, …, Aₙ` whose union is the entire space. Every outcome belongs to exactly one part. To find the probability of B, split B across these parts and add the probabilities of the separate paths:

```text
                         ┌─ A₁ ── B    P(A₁)P(B | A₁)
Start ─ choose a part ───┼─ A₂ ── B    P(A₂)P(B | A₂)   ── add paths ──> P(B)
                         └─ ...
```

For a finite partition with `P(Aᵢ) > 0` for each part,

```text
P(B) = Σᵢ P(Aᵢ ∩ B) = Σᵢ P(Aᵢ)P(B | Aᵢ)
```

The first equality follows because the pieces of B are disjoint; the second uses multiplication. Positive probability is needed for these conditional terms, but a nonempty event can have probability zero. Page 13 labels `P(Aᵢ) > 0` as nonemptiness: it is a stronger condition, useful for the displayed conditional formula, not the definition of nonempty.

Suppose machines A, B, and C produce 50%, 30%, and 20% of products, with defect rates 2%, 3%, and 5% respectively. For a product selected at random, the overall defect probability is the weighted average

```text
P(defect) = 0.50 × 0.02 + 0.30 × 0.03 + 0.20 × 0.05
          = 0.010 + 0.009 + 0.010 = 0.029, or 2.9%.
```

Weights are production shares, and the conditional rates are within each machine's output. A simple unweighted average would answer a different question. The same method applies to the two-factory example on pp. 24–25.

*Source: pp. 13–15, 24–25; interpretation and correction added.*

## 4. Bayes' theorem: update after evidence

Bayes' theorem reverses the direction of a conditional probability. Let H be a hypothesis and E observed evidence. When `P(E) > 0` and `P(H) > 0`,

```text
P(H | E) = P(E | H) P(H) / P(E)
          = likelihood × prior / probability of evidence
```

- **Prior `P(H)`:** probability of H before the specified evidence E, informed by a model, data, or judgment.
- **Likelihood `P(E | H)`:** probability of the observed evidence if H holds. It is not the probability that H is true.
- **Evidence or marginal probability `P(E)`:** overall probability of E across all possible hypotheses; it normalizes the result.
- **Posterior `P(H | E)`:** updated probability of H after observing E.

If `H₁, …, Hₙ` partition the possibilities, compute the denominator by total probability:

```text
P(Hᵢ | E) = P(E | Hᵢ)P(Hᵢ) / Σⱼ P(E | Hⱼ)P(Hⱼ)
```

The sum includes **every** partition part, not just the hypothesis of interest. **Source correction:** The expanded denominator on p. 16 ends with an `i` subscript where it should end with `n`; stopping at `i` would generally omit possible causes.

For the slide's illustrative test, disease prevalence is 1%, the chance of a positive test given disease is 90%, and the chance of a positive given no disease is 5%. Think of 10,000 people under this model:

```text
10,000 people
├─ 100 have disease       ── 90% positive ──>  90 positive
└─ 9,900 do not           ──  5% positive ──> 495 positive
                                                 ───
                                   total positive 585
```

Among positives, the disease probability is `90 / 585 ≈ 0.154`, or **15.4%**. Equivalently, `(0.90 × 0.01) / (0.90 × 0.01 + 0.05 × 0.99)`. The 90% sensitivity is `P(positive | disease)`; it is not `P(disease | positive)`. The latter depends on prevalence and the false-positive rate as well. These numbers are illustrative, not a medical estimate.

**Added ML context:** A probabilistic classifier may combine prior class frequencies with how compatible observed features are with each class to estimate class probabilities. Those estimates depend on the model and the population to which its assumptions apply.

*Source: pp. 16–19, 28–29; frequency diagram, correction, and ML context added.*

## What to carry forward

Identify the event and the conditioning information first. Add disjoint paths, subtract overlap for an “or,” and multiply along a path using the appropriate conditional probability. Total probability collects all paths to evidence; Bayes' theorem uses those paths to update a probability after the evidence is observed.

---

*Source and readability note: Adapted from `[5차시] 확률의 계산_Lv2기본.pdf`. All 29 pages were rendered and visually reviewed, including figures, quiz questions, and answer panels; page numbers count from the title page. The guide explains the substantive concepts without reproducing quiz format, adds examples and qualifications, and identifies source errors above. It is a concise concept guide rather than a certified translation; lecture audio and instructor commentary were unavailable.*
