# Lesson 1: Introduction to Statistical Analysis

Statistical analysis helps us turn observations into useful conclusions. We use it to summarize what happened, understand relationships, estimate what we have not measured, and support predictions and decisions.

The starting point is simple: **what does the data represent, and what can we reasonably conclude from it?**

## 1. Describing data and drawing conclusions

Suppose a factory makes thousands of products each day. You weigh 100 of them and find an average weight of 98 g.

- **Descriptive statistics** summarize the observations you have: “These 100 products average 98 g.”
- **Inferential statistics** use those observations to estimate something larger: “Today's entire production probably averages close to 98 g.”

The second statement needs more justification. It depends on how the 100 products were selected and involves uncertainty because most products were not measured.

A useful description also goes beyond the average. The **distribution** tells us how values are spread out: which are common, which are rare, and how much they vary. The weights `[99, 100, 101]` and `[50, 100, 150]` have the same mean but very different spreads.

Statistics can also reveal relationships, such as one production line having heavier products. But a relationship does not automatically explain its cause: that line might simply make a different product. **Correlation alone does not establish causation.**

*Source: pp. 2-3.*

## 2. Population and sample

The **population** is the complete group you want to understand. A **sample** is the smaller group you actually examine. **Sampling** is the process used to select that group.

For the factory example:

| Concept | Meaning | Example |
|---|---|---|
| Population | All units of interest | Every product made on Monday |
| Sample | The units examined | 100 selected Monday products |
| Parameter | A numerical characteristic of the population | Mean weight of all Monday products |
| Statistic | A numerical summary calculated from the sample | Mean weight of the 100 measured products |

```text
Population ── select observations ──> Sample
    │                                   │
    │ has a                             │ calculate a
    ▼                                   ▼
Parameter <──── estimate using ───── Statistic
```

A parameter exists even when we do not know its value. A statistic gives us a way to estimate it.

Imagine a tiny population with weights `[90, 100, 110, 120]` g. Its mean is 105 g. A sample containing the first two values averages 95 g; one containing the last two averages 115 g. **Different samples can give different answers even though the population has not changed.**

The population must be clearly defined. Monday's products and the whole year's products are different populations. Likewise, measuring only the morning shift may not represent the entire day. A larger sample does not automatically fix an unrepresentative selection process.

### The few symbols you need

| Quantity | Population symbol | Sample symbol |
|---|---|---|
| Mean | μ, pronounced “mu” | x̄, pronounced “x bar” |
| Standard deviation | σ, pronounced “sigma” | s |
| Variance | σ² | s² |
| Proportion | P | P̂, pronounced “P hat” |

Variance is standard deviation squared. A proportion is the fraction satisfying a condition: if 8 of 100 sampled products are defective, the sample proportion is 8/100 = 0.08, or 8%. The hat marks an estimate; some books use lowercase p and p̂ instead.

*Source: pp. 4-7, 20-21.*

## 3. Reading a dataset

**Data** are recorded observations or measurements. A **dataset** collects them in a shared structure, often a table.

| Product ID | Production line | Weight |
|---|---|---:|
| 1001 | A | 101 |
| 1002 | A | 99 |
| 1003 | B | 102 |
| 1004 | C | 89 |

Each **row** is an observation, record, or data point: one product in this example. Each **column** is a variable: an attribute recorded for each product. A cell contains one variable's value for one observation.

For a programmer, a row is like an object and its columns are fields. A record can contain both numbers and categories. The product ID is an identifier; averaging IDs would not tell us anything useful about the products.

The four displayed weights average 97.75. That describes these records. Without knowing how they were collected, we cannot assume it describes the whole factory. The example's original weight unit is unspecified.

### Inputs and outcomes

If we want to predict weight from production line:

```text
Production line ── prediction rule ──> Predicted weight
   input / X                            output / Y
```

An input is a **feature**, **predictor**, or **explanatory variable**, often written X. The outcome we want to predict is the **target** or **dependent variable**, often written Y. These roles depend on the question; a dataset used only for description does not need a target.

“Independent variable” is another name for an input. It does not mean the input causes the outcome or that different inputs are unrelated to one another.

Machine learning often learns a prediction rule from examples. Before learning algorithms, it is essential to understand what each example represents and which outcome, if any, we want to predict. In ML, “sample” sometimes means one record; in statistical sampling, it usually means the collection of selected records.

*Source: pp. 8-10, 18-19; ML connection added for context.*

## 4. Numerical and categorical data

**Numerical data** represent quantities. **Categorical data** represent labels or groups.

```text
Data
├── Numerical: quantities
│   ├── Discrete: separate possible values, such as defect counts
│   └── Continuous: values throughout an interval, such as weight
└── Categorical: groups
    ├── Nominal: no inherent order, such as production line
    └── Ordinal: meaningful order, such as low / medium / high
```

A defect count can be 0, 1, or 2, but not 1.7. Weight is usually modeled as continuous, even if a scale rounds its reading to the nearest gram. Discrete values do not have to be whole numbers; a quantity restricted to steps of 0.5 also has separate possible values.

**Storage type does not determine statistical meaning.** A category stored as `1`, `2`, or `3` remains a category. A continuous weight stored as an integer remains a rounded measurement of a continuous quantity.

*Source: pp. 11-12, 16-17.*

## 5. Measurement scales: which comparisons make sense?

A measurement scale describes what the recorded values mean and which comparisons they support.

| Scale | What is meaningful? | Example |
|---|---|---|
| Nominal | Same or different category | Color, phone number, product name |
| Ordinal | Category and order | Rank, satisfaction level, letter grade |
| Interval | Order and equal differences | Celsius temperature, calendar year |
| Ratio | Equal differences and ratios, with a meaningful zero | Height, weight, number of defects |

**Nominal:** A phone number is a label, even though it uses digits. Subtracting two phone numbers is not meaningful. Counts and proportions of categories are useful summaries.

**Ordinal:** “High” ranks above “medium,” which ranks above “low.” But the gaps need not be equal. Similarly, the change from 1 to 2 stars may not represent the same change in satisfaction as 4 to 5 stars. Averaging rating codes assumes more than order alone provides.

**Interval:** The difference between 10°C and 20°C equals the difference between 20°C and 30°C. But 20°C is not twice the absolute temperature of 10°C: Celsius zero is a reference point, not absence of temperature. Converting those readings to Fahrenheit changes their ratio.

**Ratio:** A 200 g mass is twice a 100 g mass. Converting to kilograms preserves that ratio. Zero represents absence of the measured quantity. This supports “twice as much” comparisons, though division by zero is still undefined.

Discrete/continuous and interval/ratio answer different questions. A defect count is **discrete** because its possible values are separated, and **ratio-scaled** because zero means no defects and ratios are meaningful.

Units and definitions matter. Celsius and Fahrenheit are interval scales; Kelvin has an absolute zero and is ratio-scaled. An amount of money can support ratios when currency and context are consistent. IQ is commonly treated as interval in introductory courses, but an IQ of 120 does not mean twice the intelligence of 60.

*Source: pp. 13-15, 22-25.*

## What to carry forward

Before calculating a summary or building a model, identify what one row represents, what each variable means, and which population you want to understand. Then choose operations that fit the variable's scale. A correct calculation is only useful when its interpretation matches the data.

---

*Source note: Adapted from `[1차시] 통계 분석 개요_Lv2기본.pdf`. All 25 pages were visually reviewed because ordinary text extraction missed most content. Examples and explanations extend the original definitions. Ambiguous wording about continuous values, measurement scales, and causation has been clarified; the original table does not specify weight units or sampling method. Page references count from the PDF's title page.*
