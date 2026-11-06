# Lesson 3: Presenting Data

Raw data are observations in their collected form. A long list of records can be hard to interpret: a table or graph makes distributions, group differences, and relationships easier to see. A table is useful for looking up exact values; a graph makes patterns easier to notice. Both compress information, so keep the original data and ask what the summary leaves out.

[Lesson 1](../01-statistical-analysis/README.md) distinguished categorical labels from numerical quantities. [Lesson 2](../02-statistics/README.md) introduced center, spread, and quartiles. This lesson shows how to present those ideas visually.

*Source: pp. 2–4, 12.*

## 1. Frequency distributions

A **frequency distribution** counts how many observations fall in each category or numerical interval. Frequency is a count; **relative frequency** is that count divided by the total; percentage is relative frequency multiplied by 100. **Cumulative frequency** is the running count up to a given value or ordered interval.

For example, suppose 10 products have the following defect counts:

| Defects | Frequency | Relative frequency | Cumulative frequency |
|---:|---:|---:|---:|
| 0 | 4 | 0.40 | 4 |
| 1 | 3 | 0.30 | 7 |
| 2 | 2 | 0.20 | 9 |
| 3 | 1 | 0.10 | 10 |
| **Total** | **10** | **1.00** | — |

Thus 30% have one defect, and 7 have at most one. If categories cover every observation exactly once, counts sum to the total and relative frequencies sum to 1, apart from rounding. Relative frequencies lie between 0 and 1 inclusive. They are not *always strictly less* than their counts: with one observation in a category and one overall, both are 1. A cumulative count is meaningful when values or categories have an order; an arbitrary order of nominal labels has no inherent cumulative interpretation.

For continuous measurements, group nearby values into **classes**, or **bins**. The **bin width** is the distance between its boundaries; the **class mark** is the midpoint. Specify boundaries so every value belongs to exactly one bin. For example, `[15, 25)` includes 15 but excludes 25, which belongs to `[25, 35)`. If seven equal bins span 15 to 85, their width is `(85 − 15) / 7 = 10`; the last bin must include 85. Bin choices affect what patterns remain visible.

**Source correction:** On p. 8 the final class is printed as 205–210 with class mark 207. Its endpoint midpoint is 207.5, and it is wider than the preceding integer ranges. If 205–209 was intended, its midpoint would be 207; the original intent cannot be established from the slide. For continuous measurements the printed integer ranges also need explicit boundaries.

*Source: pp. 5–8, 25–28; the defect example and boundary notation are added.*

## 2. Contingency tables: two categorical variables

A **contingency table** cross-tabulates counts for combinations of categories. In the slide example, 180 people are grouped by gender and preferred drink:

| | Coffee | Tea | Juice | Total |
|---|---:|---:|---:|---:|
| Men | 45 | 15 | 20 | 80 |
| Women | 25 | 35 | 40 | 100 |
| **Total** | **70** | **50** | **60** | **180** |

The count 45 is a **joint frequency**: both men and coffee. The row and column totals are **marginal frequencies**: 80 men and 70 coffee preferences. Divide a cell by the grand total for a **joint relative frequency**: `45 / 180 = 0.25`. Divide by the relevant group total for a **conditional relative frequency**: `45 / 80 = 56.25%` of men prefer coffee, whereas `45 / 70 ≈ 64.3%` of coffee-preferring people are men. The denominator changes the question.

Comparing within-group percentages can be more informative than comparing counts when groups differ in size. These descriptive associations do not establish why preferences differ.

*Source: pp. 9–11; conditional calculations and interpretation added.*

## 3. Graphs for one variable

A **univariate** graph describes one variable. Choose a display to fit what the values mean.

| Graph | Best suited to | How to read it |
|---|---|---|
| Bar chart | Categories or discrete values | Bar length represents a count, proportion, or other stated quantity; compare lengths. Bars are usually separated. |
| Histogram | Numerical distribution grouped into intervals | Adjacent bars cover numerical bins; look for peaks, gaps, tails, and unusual values. |
| Pie chart | A small number of parts of one whole | Each sector shows a share of the total; its angle is share × 360°. |
| Box plot | Numerical distribution | Compare median, quartiles, spread, and flagged observations. |

A bar chart can show categories in a useful order; a histogram's bins follow numerical order. For equal-width histogram bins, bar heights can show counts. With unequal widths, **area**, rather than height alone, must represent count or proportion: use frequency density (count divided by bin width) for height. Histogram shape can change with bin width and starting point. Pie charts become difficult to compare when there are many similar slices; for example, a 25% slice has angle `0.25 × 360° = 90°`.

### Reading a box plot

The five-number summary is minimum, Q₁, median, Q₃, and maximum. The box spans Q₁ to Q₃, with a line at the median; its width is the interquartile range, `IQR = Q₃ − Q₁`. As in Lesson 2, quartile conventions can produce different values for small samples.

```text
                  box: middle half
              Q₁      median       Q₃
               │         │          │
   whisker ────[─────────|──────────]──── whisker       •
                                                    flagged value
```

In the common 1.5 × IQR convention shown in the slides, the **fences** are `Q₁ − 1.5 × IQR` and `Q₃ + 1.5 × IQR`. Whiskers extend to the most extreme observed values within those fences; observations beyond them are plotted individually. Fences need not be observations, and whiskers need not reach the overall minimum and maximum. Other box plot conventions exist, so check the definition. A flagged point is a potential outlier, not automatically an error.

For the slide's sorted values `2, 4, 6, 8, 10, 12, 14, 16, 18`, the convention used there gives Q₁ = 6, median = 10, and Q₃ = 14. IQR = 8, fences are −6 and 26, and the whiskers reach 2 and 18. Excluding the median when finding the halves instead gives Q₁ = 5 and Q₃ = 15. Side-by-side box plots make group medians and spreads easy to compare, but do not show every individual value or reliably reveal multiple peaks.

*Source: pp. 13–19, 29–32; unequal-width histograms and interpretation cautions added.*

## 4. Graphs for relationships and comparisons

A **bivariate** display considers two variables together. The choice depends on whether the variables are numerical, categorical, or ordered in time.

- **Scatter plot:** Each point is one pair of numerical values, with one variable on each axis. It can reveal direction, curvature, clusters, gaps, and outliers. Many overlapping points can hide density. A pattern is an association, not proof of causation.
- **Line chart:** Connect observations in a meaningful order, often time on the horizontal axis, to show change, trends, or recurring patterns. Connecting points implies an ordered progression; it does not prove the exact path between measurements.
- **Grouped bar chart:** Place subgroup bars side by side within each main category. A shared baseline makes subgroup comparisons easier.
- **Stacked bar chart:** Stack subgroups within each main category. Total height shows the total and segment heights show contributions. Middle segments lack a common baseline, making their sizes harder to compare across groups. A 100% stacked chart instead normalizes each total to show composition and hides differences in total size.

```text
Grouped bars                 Stacked bars
A: coffee ███  tea ██        A: ███▓▓  total 5
B: coffee ██   tea ████      B: ██▓▓▓▓ total 6
                               █ coffee   ▓ tea
```

Here the grouped view makes the tea counts 2 and 4 easy to compare; the stacked view emphasizes totals 5 and 6. Numerical values can also be compared across categories with grouped box plots. Label axes, units, groups, and whether a scale shows counts or percentages; for bars, a zero baseline helps lengths represent magnitude honestly.

*Source: pp. 13, 20–24, 33–34; example and 100% stacked-chart distinction added.*

## What to carry forward

Start with the question and the variable types. Frequency tables preserve exact counts, while graphs reveal shape and relationships. Check denominators, bin boundaries, scales, and plotting conventions before interpreting a visual. A compact display helps explore the observations, but it cannot by itself establish representativeness or causation.

---

*Source note: Adapted from `[3차시] 자료의 표현_Lv2기본.pdf`. All 34 pages were rendered and visually reviewed, including figures and quiz answer panels; page numbers count from the title page. Examples and cautions labeled as added extend the slides. The p. 8 bin inconsistency is corrected explicitly, and bin endpoints, quartile conventions, and box-plot whiskers are clarified. This is a concept-based guide rather than a certified translation; lecture audio and instructor commentary were unavailable.*
