# Lesson 21: Feature Engineering

A **feature** is a piece of information supplied to a model to help it make a prediction; the **target** is the outcome the model is trained to predict. Feature engineering turns raw data into inputs that make useful patterns easier for a model to learn. For example, when predicting a customer's next purchase, the time since their previous purchase may be more informative than a raw transaction timestamp alone.

```text
raw records → create useful features → select features → transform representation → model
                     ↑                      │                         │
                     └──── evaluate and improve using training data ──┘
```

These steps may be revisited in a different order. The aim is better performance on new cases, and sometimes a simpler, more interpretable model. Neither outcome is guaranteed: extra features can add noise and encourage overfitting. **Domain knowledge**—understanding the business or problem—helps suggest meaningful features and check whether they will be available when a prediction is made. [Lessons 10](../10-data-processing-2/README.md) and [13](../13-machine-learning-overview-2/README.md) introduce transformations and evaluation.

*Source: pp. 2–3, 16–17; prediction-time and evaluation qualifications added.*

## 1. Create features

A new feature can combine existing inputs. A **feature interaction** represents a relationship in which the effect of one feature can depend on another. For instance, adding `price × discount rate` gives a discount amount: a price of 80 and a rate of 0.25 give 20. In a linear model, including a product term lets the contribution of price vary with the discount rate. Ratios can also express useful relationships: profit divided by revenue is profit margin, provided revenue is nonzero. Decide how to handle zero or missing denominators.

Features can also summarize patterns across records. For each customer, a most frequently visited store, mean interval between purchases, weekday-to-weekend purchase ratio, or seasonal variation in spending may be informative. Similarly, minimum delivery time or maximum production time can summarize a history. The **coefficient of variation** is standard deviation divided by the mean; it expresses spread relative to the mean when that ratio is meaningful, and needs special care when the mean is zero or close to zero. Define the observation window and how ties, missing histories, and zero counts are handled.

```text
customer's past transactions → purchase dates → intervals → average interval
                                      └────────→ store visits → most frequent store
```

A layered approach can start with raw records, derive basic features, then combine or summarize them into **higher-order features**. A product such as price × discount rate is one example. Such features can expose nonlinear relationships or interactions to a model; their usefulness must be tested. **Source clarification:** Pages 18–19 imply higher-order features must be complex and less interpretable. They can be simple and interpretable, although adding many interactions can make a model harder to explain. Domain knowledge can help at every stage, not only at the higher-order stage.

An arbitrary customer ID is generally a poor numeric feature: its numerical distance and order need not mean anything. **Source clarification:** Page 15 calls IDs entirely unrelated to purchase patterns. An identifier can sometimes carry information, such as account creation order, or link to useful history; whether it generalizes and is available at prediction time must be assessed. Avoid treating arbitrary ID arithmetic as meaningful.

*Source: pp. 3–4, 14–15, 18–19; examples and practical qualifications added.*

## 2. Select features

Feature selection retains some candidate inputs and removes others. It can reduce computation, noise, and complexity, but removing a feature can also discard useful information. Three broad approaches are:

- **Filter:** score features using a statistical criterion independently of the eventual model, such as correlation with the target. A feature with little individual correlation can still be useful in a nonlinear relationship or interaction.
- **Wrapper:** compare subsets by fitting and evaluating a model, then choose a subset that scores well. Searching many subsets can be expensive and can overfit the selection data; practical searches do not necessarily find the globally best subset.
- **Embedded:** selection occurs as part of fitting a model. A decision tree chooses features for its splits; regularized models can also shrink some coefficients to zero. A tree's split choices do not guarantee the best subset for every model or dataset.

Importance scores are one source of evidence for selection. They describe a model and dataset under a particular metric, not a feature's causal effect or universal value. Correlated features can substitute for one another, making an individual feature appear less important. A weak model may also assign misleading importance.

*Source: pp. 5–6; examples and limitations added.*

### Drop-column importance

Train and evaluate a model with all candidate features. Remove one feature, **retrain** a comparable model, and evaluate again on the same held-out data. The deterioration in the chosen metric measures the cost of losing that feature under this procedure. Repeat for other features. For a lower-is-better metric such as root mean squared error (RMSE), use reduced-model RMSE minus full-model RMSE.

The slide's full model has RMSE 13.232; after dropping A it has RMSE 21.342, so the increase is `8.110`. Its other increases are 1.113, 3.425, and 6.337 for B, C, and D. A has the largest increase in this example. The displayed importance values divide each increase by their sum, 18.985: A's is about `0.427`. Such normalization is optional, and negative changes are possible if removing a feature improves the score. Retraining for each feature can be computationally costly.

*Source: pp. 7, 10–11; metric interpretation and negative-change qualification added.*

### Permutation importance

Keep the fitted model fixed. Measure its score on evaluation data, randomly shuffle one feature's values across those rows, and score again. Shuffling preserves that column's values but disrupts their pairing with the target and other features. A large deterioration suggests the fitted model relied on that feature on those data. Repeat shuffles and summarize the changes because a single random shuffle can give an unrepresentative result, even leaving some values in place.

```text
original:  age  experience  target       shuffled:  age  experience  target
            52      24        45                     52       1        45
            27       1         0                     27      24         0
            34       6        12                     34       6        12
```

Unlike drop-column importance, permutation does not retrain the model; both methods can be applied to many model types. Correlated inputs can mask importance, and independent shuffling can create unrealistic combinations. **Source correction:** Page 13 says individual permutation does not consider interactions. It does not *separately identify* or fully characterize interactions, but its score can reflect reliance on interactions already learned by the model, since shuffling disrupts them. The result is not a causal effect.

*Source: pp. 8, 12–13; interpretation and limitations added.*

## 3. Transform features

A transformation changes a feature's representation. The appropriate choice depends on the data and model; it is not necessary to put every feature on the same scale.

- **Scaling numerical inputs:** min–max scaling uses `(x − minimum)/(maximum − minimum)`; standardization uses `(x − mean)/standard deviation`. For a reference mean of 50 and standard deviation of 10, 65 becomes 1.5. Scaling can prevent large numerical units from dominating distance-based methods. Constant features need special handling because the denominator is zero.
- **Changing distribution shape:** a log or power transformation can compress a long upper tail. For positive inputs, `log₁₀(1, 10, 100) = (0, 1, 2)`. Ordinary logs require positive inputs; transformations do not guarantee a normal distribution. An exponential transformation is another possible nonlinear mapping, but may amplify large values rather than compress them.
- **Encoding categories:** one-hot encoding represents a category with separate 0/1 indicators. Integer label codes are compact, but for unordered categories their numerical order and distances are arbitrary and may mislead a model. Plan how to represent missing or unseen categories consistently.

*Source: p. 9; definitions, examples, domains, and qualifications added. See [Lesson 10](../10-data-processing-2/README.md) for more detail.*

## 4. Evaluate the whole procedure

**Added practice:** Split data before learning feature choices or preprocessing parameters. Create each feature using only information available at the intended prediction time. For example, a customer's average purchase interval must use past purchases, not future ones. Fit scaling, category mappings, feature selection, and any other learned steps on training data only; apply the resulting rules to validation data. During cross-validation, repeat those steps separately inside each training fold. Compare alternatives using validation results, then evaluate the selected procedure on an untouched test set. Repeatedly selecting features using test results leaks information and makes the final score optimistic. See [Lesson 13](../13-machine-learning-overview-2/README.md).

---

*Source and readability note: Adapted from `[21차시] 특성공학_Lv2기본.pdf`. All 19 pages were rendered and visually reviewed, including the importance table, diagram, questions, and answer panels; page numbers count from the title page. Substantive slide concepts are incorporated without quiz format; corrections and useful additions are identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
