# Lesson 4: Probability

Probability gives a numerical description of uncertainty. In [Lesson 3](../03-data-presentation/README.md), relative frequency described a proportion *in observed data*. Probability describes uncertainty under a specified model or interpretation; observed frequencies can help estimate it. For example, a model might assign a defective product probability 0.02, while the fraction defective in a particular sample can differ from 0.02.

## 1. Three interpretations

- **Classical:** If a finite set of outcomes is equally likely, an event's probability is the number of outcomes in the event divided by the total number. For a fair six-sided die, `P(even) = 3/6 = 1/2`. Equal likelihood is an assumption, not a consequence of having six outcomes. This counting formula does not directly apply to unequal probabilities or infinitely many outcomes.
- **Frequentist:** Probability describes the long-run relative frequency of an event under repeated trials in the same conditions. If `n(A)` of `n` trials result in A, its observed relative frequency is `n(A)/n`. In an appropriate repeated-trial model, this approaches `P(A)` as trials increase. A finite run can differ substantially from that probability; infinite repetition is an idealization and a unique event is harder to interpret this way.
- **Subjective:** Probability expresses a degree of belief given available information. Different people can assign different probabilities because their information or judgments differ. A **prior** is a probability assigned before considering specified new evidence; Bayesian inference updates it using that evidence. Subjective judgments should still obey probability rules.

The same mathematical rules can support these interpretations. Classical reasoning assumes equal likelihood, which itself needs justification; frequentist probabilities require a suitable repetition model; subjective probabilities depend on the information and model used.

*Source: pp. 2–5; the finite-sample and model qualifications are added.*

## 2. Experiments, outcomes, and events

A **random experiment** is a process with an uncertain outcome. A **trial** is one performance or component of the process. An **outcome** is one possible result at the level of detail chosen; the **sample space**, written `Ω` (also `S` or `U`), is the set of all possible outcomes. An **event** is a set of outcomes in that space. It occurs when the observed outcome belongs to the set.

```text
Experiment: toss two distinguishable coins
                         outcome
                            ↓
Sample space Ω = { HH, HT, TH, TT }
Event A = at least one head = { HH, HT, TH }
```

The first letter refers to the first coin, so HT and TH are distinct. A simple or **elementary event** contains one outcome, such as `{HH}`; a **compound event** contains multiple outcomes, such as A. The **empty event** `∅` contains no outcomes, and the **sure event** `Ω` contains all outcomes. For this finite space, every subset is an event: four outcomes yield `2⁴ = 16` possible events, including `∅` and `Ω`.

**Source corrections:** Page 6 describes trials as independent, but independence is an additional assumption, not part of the definition: drawing cards without replacement gives dependent trials. On pp. 21–22, the second choice changes from “compound” to “elementary” and the answer highlight conflicts with the explanation. At least one head is compound; the displayed four-outcome space is correct, and there are 16 events, not 8. This count assumes the coins are distinguishable and all subsets are allowed.

*Source: pp. 6–9, 13, 21–22.*

## 3. Types of sample space

| Space | Meaning | Example |
|---|---|---|
| Finite | A finite number of outcomes | Die: `{1, 2, 3, 4, 5, 6}` |
| Countably infinite | Outcomes can be listed as a sequence | Number of tosses until the first head: `1, 2, 3, …` |
| Uncountable | Outcomes cannot be listed in such a sequence | A modeled lifetime taking any real value in `[0, ∞)` |

For a countable space, the probabilities of individual outcomes sum to 1. For a fair coin, the probability that the first head occurs on toss `k` is `(1/2)ᵏ`; these probabilities sum to 1. The model can also include the infinite sequence with no head, which has probability 0. Zero probability does not always mean an outcome is impossible.

For continuous models, intervals are usually more useful than exact values. For example, if a measurement is uniformly distributed on `[0, 1]`, the probability of any particular exact value is 0, while the probability of `[0.2, 0.5]` is 0.3. Square brackets include endpoints; parentheses exclude them. With this continuous model, inclusion of an endpoint does not change the interval probability.

**Source correction:** Page 20 says that individual outcomes in an uncountable space have probability exactly 0. That is true for a continuous distribution, not for every probability distribution on an uncountable space: a model can assign positive probability to a particular value as well as probability across a continuum. For infinite spaces, not every subset must be an event; mathematically, probabilities are assigned to a suitable collection of subsets.

*Source: pp. 9–12, 19–20; examples and qualifications added.*

## 4. Combining events

Let A be “even” and B be “at most 3” for one die roll. Thus `A = {2, 4, 6}` and `B = {1, 2, 3}`. Event operations work like set operations:

| Notation | Meaning | Die example |
|---|---|---|
| `A ∪ B` | A **or** B, including both | `{1, 2, 3, 4, 6}` |
| `A ∩ B` | A **and** B | `{2}` |
| `Aᶜ` | Not A, relative to Ω | `{1, 3, 5}` |
| `A − B` | A occurs and B does not | `{4, 6}` |

Events are **disjoint** or mutually exclusive when they cannot happen together: their intersection is empty. Even and odd on one roll are disjoint. In contrast, A and B above overlap at 2.

**Independence** means `P(A ∩ B) = P(A)P(B)`. When `P(B) > 0`, equivalently `P(A | B) = P(A)`: knowing B occurred does not change A's probability under the model. Here `P(A | B)` means `P(A ∩ B)/P(B)`; the conditioning event supplies the denominator, as with conditional relative frequencies in Lesson 3.

For two independent fair coin tosses, “first is heads” and “second is heads” each have probability 1/2, and both occur with probability 1/4. They overlap and are independent. Disjoint events with positive probabilities cannot be independent, because their intersection has probability 0 rather than the positive product. Independence is a property of the probability model, not a claim of causation.

*Source: pp. 13–14, 17–18, 23–26; conditional notation and examples added.*

## 5. Axioms and useful consequences

The Kolmogorov axioms set out the basic rules for a probability assignment:

1. **Nonnegativity:** `P(A) ≥ 0` for every event A.
2. **Normalization:** `P(Ω) = 1`.
3. **Countable additivity:** For pairwise disjoint events, the probability of their union equals the sum of their probabilities, including for a countably infinite sequence.

These imply the following rules:

```text
P(∅) = 0                         0 ≤ P(A) ≤ 1
P(Aᶜ) = 1 − P(A)                If A ⊆ B, then P(A) ≤ P(B)
If A ⊆ B: P(B − A) = P(B) − P(A)
P(A ∪ B) = P(A) + P(B) − P(A ∩ B)
```

The last rule subtracts the overlap because adding the two probabilities counts it twice. If A and B are disjoint, the overlap is zero, so their union probability is the sum. For the fair die events above, `P(A ∪ B) = 3/6 + 3/6 − 1/6 = 5/6`. The complement of rolling 5 has probability `1 − 1/6 = 5/6`. Multiplying the two unconditional probabilities for an intersection requires independence; in general, `P(A ∩ B) = P(A | B)P(B)` when `P(B) > 0`. Adding for a union requires accounting for overlap.

*Source: pp. 15–18, 25–26; the general union rule and worked example are added.*

## What to carry forward

Define the experiment and its possible outcomes before calculating. State the assumptions that assign probabilities, distinguish “or” from “and,” and check whether events overlap or are independent. These ideas connect observed proportions to models of uncertainty and prepare the way for probability distributions and inference.

---

*Source note: Adapted from `[4차시] 확률_Lv2기본.pdf`. All 26 pages were rendered and visually reviewed, including quiz questions and answer panels; page references count from the title page. Examples and qualifications extend the slides, and source errors are identified above. This is a concise concept guide, not a certified translation; lecture audio and instructor commentary were unavailable.*
