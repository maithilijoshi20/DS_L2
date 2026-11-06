# Lesson 9: Data Processing 1

Before summarizing data or fitting a model, make sure the records are usable and understand how they were selected. Missing values, unusual observations, joins, and sampling can all change what the resulting data represent. The population and sample distinction from [Lesson 1](../01-statistical-analysis/README.md) and sampling uncertainty from [Lesson 8](../08-inferential-statistics/README.md) remain central.

```text
Collect records → inspect quality → combine related tables → analyze
       ↑                 ↑                   ↑
 sampling design    missing / unusual     matching and row counts
```

These steps can occur in a different order or be repeated. The appropriate decisions depend on the question and how the data were produced.

## 1. Missing values

A **missing value** is an unavailable value where one could have been recorded. It may appear as a blank, `NA`, or database `NULL`; a code such as `-` might also mean missing, but check the data dictionary. Zero is a measured value, not automatically missing. Missingness affects calculations and can change which population the available records describe.

The mechanism matters. For example, suppose income is missing in a survey:

| Mechanism | Meaning | Illustrative reason for missing income |
|---|---|---|
| **MCAR**: missing completely at random | Missingness is unrelated to observed or unobserved data values. | A random technical failure loses responses independently of respondents' characteristics. |
| **MAR**: missing at random | Given observed variables, missingness does not depend on the missing value itself. | Missingness depends on recorded age, but within each age group not on income. |
| **MNAR**: missing not at random | Even after conditioning on observed data, missingness depends on an unobserved value. | Higher-income people are less likely to report their income, even after accounting for observed variables. |

The same pattern of blanks can be consistent with different mechanisms; observed data alone generally cannot prove MAR rather than MNAR. A difference in missingness by a recorded variable is compatible with MAR, but does not establish it. Random selection of a question to omit is MCAR only if the selection and loss are independent of relevant values.

Common responses are to **delete** incomplete records for a particular analysis or **impute** plausible values. Deletion loses information and can bias results if complete records differ systematically; even MCAR deletion reduces precision. Imputing a mean or median is simple but can shrink variation and weaken relationships. A group-specific value or a prediction model can use other information, but the result still depends on assumptions. Some methods can handle missing values directly. A small missing percentage is not a guarantee of negligible bias, particularly if missingness is concentrated in a consequential group.

**Source correction:** Page 4 calls filling missing cells with group values or machine-learning predictions “multiple imputation.” Those can be *single* imputation if each cell receives one completed value. Multiple imputation instead creates several plausible completed datasets, analyzes each, and combines results so uncertainty about missing values is represented. The slide's deletion/imputation rule is a heuristic, not a universal choice. Document the reason, method, and effect of any treatment.

*Source: pp. 2–4, 22–23; mechanism conditions and treatment cautions added.*

## 2. Extreme values and outliers

An **extreme value** lies near an end of a numerical distribution. An **outlier** is an observation unusually different from others under a chosen comparison. It can strongly affect a mean, standard deviation, graph, or model. As [Lesson 2](../02-statistics/README.md) showed, the median is often less sensitive to a single extreme value.

An outlier may be **artificial**, such as a unit conversion or entry error, or **natural**, such as a real rare event. Context matters: daily traffic of over 100,000 on the first day of a major concert sale is extreme relative to an ordinary 1,000–1,500 visitors, but may be explained by the event. It is not automatically erroneous or irrelevant. Whether it is unusual *for that context* depends on a suitable comparison with similar events; the slide's label of contextual outlier is not established by ordinary-day traffic alone.

A **univariate** outlier is unusual in one variable. A **multivariate** outlier can have ordinary values individually but an unusual combination, such as a very high weight paired with an unusually short height. Two common univariate screens are:

- **Standard deviations:** compare a value's distance from the mean with the standard deviation. Under a normal model, about 99.7% of values lie within three standard deviations, but this is a model property, not a universal rule. Extreme values themselves can influence the mean and SD.
- **IQR fences:** using the quartiles from [Lesson 3](../03-data-presentation/README.md), calculate `IQR = Q₃ − Q₁`. Flag values below `Q₁ − 1.5 × IQR` or above `Q₃ + 1.5 × IQR`. A box plot shows these candidates; quartile conventions can differ.

```text
 lower fence      Q₁     median     Q₃       upper fence
      │           [────────|────────]             │     •
    Q₁−1.5 IQR        middle half             Q₃+1.5 IQR
                                                    flagged value
```

A flag prompts investigation, not automatic removal. Correct a verified error when possible; otherwise retaining, reporting separately, or using a robust method may be appropriate. Compare conclusions with and without a questionable value when useful, and record the choice. Removing a real rare event may erase exactly what the analysis needs to detect.

*Source: pp. 2, 5–6, 24–25; contextual comparison and treatment cautions added.*

## 3. Joining tables

A **join** combines rows from two tables according to a condition, often equality of a shared key. A row is a record and a column is a variable. A **primary key** uniquely identifies a row in its own table; a join may instead use a foreign key or other columns, and the matching columns need not be unique. `NULL` indicates an unknown or absent value in a database. In ordinary SQL equality joins, `NULL` does not match another `NULL`.

Consider employees A and departments B, joined on department ID:

```text
A: employee → department       B: department → name
   Ana      → 10                  10 → Sales
   Ben      → 20                  30 → Support
```

| Join | Rows retained in this example |
|---|---|
| **Inner** | Matching pair: Ana / 10 / Sales. |
| **Left** | Every A row: Ana's match and Ben / 20 with missing B fields. |
| **Right** | Every B row: Ana's match and 30 / Support with missing A fields. |
| **Full outer** | The match plus both unmatched rows, with missing fields on the absent side. |
| **Cross** | Every A–B pair: 2 × 2 = 4 rows, without a matching condition. |
| **Left anti** | A rows with no B match: Ben / 20. A right anti join reverses the sides. |

Outer joins introduce missing fields for an unmatched side; an inner or cross join can still contain missing values that already existed in its inputs. A left join preserves a customer with no matching order, but does not invent an order: order fields are missing. A cross join of 20 specifications and 5 specifications produces 100 combinations, useful when every pair must be considered.

**Source corrections:** Pages 9 and 12 give upper bounds of `min(|A|, |B|)` for an inner join and `|A| + |B|` for a full join. These fail with repeated matching keys: two A rows and three B rows with the same key produce six matched rows in either join. The full join also retains unmatched rows. Pages 10–11's outer-join upper bound `|A| × |B|` fails if the other input is empty; every row on the preserved side still appears. Row counts depend on matching multiplicity and unmatched rows, not just table sizes. Check key uniqueness, unmatched records, and row counts to avoid unintended duplication or loss. A left anti join returns at most `|A|` rows; a cross join returns exactly `|A| × |B|`.

*Source: pp. 7–14, 26–29; duplicate-key and SQL qualifications added.*

## 4. Sampling designs

**Sampling** selects part of a population to learn about the whole. It is often faster and cheaper than a census. A sample is useful when its selection and measurement support the intended population claim. **Sampling error** is the difference between a sample-based estimate and the population quantity due to which units were selected; it varies across possible samples. Increasing sample size generally reduces sampling variation under a suitable design, but does not by itself fix coverage bias, nonresponse, or measurement error.

**Probability sampling** uses a random design with known, nonzero inclusion probabilities for population units. **Non-probability sampling** uses selection without such a design, for example a convenience sample or judgment-based selection. The latter can be useful but does not by itself justify design-based estimates of sampling error or population representativeness.

```text
Stratified:  split into strata → randomly select from EACH stratum
Cluster:     split into clusters → randomly select SOME clusters
Systematic:  random start → select every k-th unit in an ordered list
```

| Probability design | How it works | Practical consideration |
|---|---|---|
| **Simple random sampling** | For a fixed size, every possible sample of that size has the same chance. | Straightforward, but a small subgroup may be missed by chance. Equal individual chances alone do not define this design. |
| **Stratified random sampling** | Divide the population into nonoverlapping strata, then randomly sample within each. Allocation may be proportional to stratum size or equal across strata. | Ensures planned subgroup coverage; can improve precision when strata are internally similar. Unequal allocation generally requires weights for population estimates. |
| **Systematic sampling** | Choose a random start, then take units at a fixed interval from a list. | Convenient, but periodic structure in the list can align with the interval and reduce representativeness. |
| **Cluster sampling** | Divide into clusters and randomly choose some; measure all units in chosen clusters or sample within them in a further stage. | Can reduce collection cost. Similarity within clusters and differences between clusters can increase sampling error. |

For instance, sampling people from every workplace is stratification if workplaces are the strata. Selecting only some workplaces and then sampling people within them is multistage cluster sampling. **Source clarification:** The example on pp. 30–31 omits workplace C and calls simple random and cluster sampling possible, while ruling out stratification. That is consistent with stratifying by workplace and requiring a positive sample from each, but the observed rows alone cannot identify the design: another stratification variable or allocation could produce the same sample. Likewise, selecting only some people from chosen workplaces requires a second sampling stage if workplaces are clusters.

*Source: pp. 15–21, 30–31; probability design, weighting, and sampling-stage qualifications added.*

## What to carry forward

Investigate why values are missing or unusual before changing them. Choose a join for the rows the question requires, and check its matching behavior. For population conclusions, understand the sampling design as well as the sample size; processing cannot repair a fundamentally unrepresentative selection automatically.

---

*Source and readability note: Adapted from `[9차시] 데이터 처리 1_Lv2기본.pdf`. All 31 pages were rendered and visually reviewed, including diagrams, tables, quiz questions, and answer panels; page numbers count from the title page. Substantive concepts are incorporated without quiz format, with additions and source corrections identified above. Examples and row counts were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
