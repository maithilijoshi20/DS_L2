# Lesson 20: k-Means Clustering

**k-Means** groups observations by similarity without being given correct group labels. It can reveal patterns for further investigation: for example, customers with similar purchasing behavior or manufactured items with similar measurements. The resulting groups are descriptions of the chosen data and features, not automatically real categories or proof of a cause. This builds on distance and scaling in [Lesson 13](../13-machine-learning-overview-2/README.md).

## 1. Clusters and centroids

Each observation is a point whose coordinates are its numerical features. Choose a number of groups, `k`. k-Means partitions the points into groups called **clusters**, assigning each point to its nearest **centroid**. A centroid is the coordinate-wise arithmetic mean of the points currently assigned to a cluster; it need not be an actual observation. Standard k-means uses Euclidean distance and aims to minimize the **within-cluster sum of squared distances** to centroids:

```text
within-cluster sum of squares = Σ over clusters Σ over points in cluster ||point − centroid||²
```

A smaller value means points are closer to their assigned centers in the selected feature space. The mean minimizes squared distances within a fixed nonempty cluster. Squaring also makes distant points influential, so outliers can pull a centroid far from the majority.

Clustering can organize data for later analysis and help discover patterns. Applications include customer or market segmentation and recommendations, manufacturing quality and maintenance investigation, traffic and delivery patterns, and energy use or environmental monitoring. A cluster of unusual items can guide inspection, but its label as defective requires external evidence. *Source: pp. 2–4.*

## 2. How the algorithm works

1. **Initialize:** choose `k` initial centroid locations, often using a randomized method.
2. **Assign:** assign each observation to its closest centroid.
3. **Update:** calculate each cluster's new centroid as the mean of its assigned points, feature by feature.
4. Repeat assignment and update until assignments or centroid changes satisfy a stopping rule, or a maximum iteration count is reached.

```text
choose k centers → assign each point → move centers to group means
                         ↑                       │
                         └──────── repeat ───────┘
```

For the one-dimensional points `1, 2, 8, 9`, take `k = 2` and initial centers `1` and `9`. Assignment gives `{1, 2}` and `{8, 9}`; their updated centers are `1.5` and `8.5`. Reassignment leaves the groups unchanged, so the process has converged. In multiple dimensions, average each coordinate separately. Ties and empty clusters require an implementation-specific rule.

Each assignment and mean update cannot increase the squared-distance objective, but convergence does not guarantee the best possible partition. Different starting centers can produce different results. Multiple initializations and retaining the lowest objective can improve the result; k-means++ is one common initialization method. **Source clarification:** The slides describe initial positions as arbitrary; they need not be uniformly random, and the final result depends on initialization. *Source: pp. 3, 5, 13–14, 19–20; objective and initialization details added.*

**Source correction:** Pages 11–12 count `(n × k + k) × iterations` as a total number of numerical operations, giving 300 for 19 points, 3 centers, and 5 iterations. This is a simplified count of 285 point-to-center distance calculations plus 15 centroid updates, not a literal count of arithmetic operations: both distances and means require operations across features and points. Actual cost also depends on dimensionality and implementation. *Source: pp. 11–12.*

## 3. Choosing and interpreting clusters

Choose `k` using the purpose of the analysis, candidate results, and domain knowledge. The training objective alone is insufficient: its best achievable value cannot increase as `k` increases, so it rewards additional clusters even when they are not useful. Consider whether groups are stable across initializations, large enough to matter, distinct, and actionable. If two groups have very similar profiles, consider a smaller `k`, then check the consequences.

Interpret each cluster by comparing its centroid's feature values with those of other clusters and with the overall population. For example, a manufacturing cluster with high surface roughness and contamination may warrant closer inspection. Give a descriptive name only after checking what the measurements mean; centroid averages can conceal variation within the cluster. If clustering used scaled values, translate centroids back into original units or summarize the original measurements for interpretation. Cluster numbers are arbitrary identifiers, not rankings or known labels. *Source: pp. 6–7, 10; interpretation guidance added.*

### Cohesion and separation

Internal validity measures summarize how tightly points group together (**cohesion**) and how far groups lie from each other (**separation**). They can help compare candidate partitions, but reflect the chosen features, scaling, distance and data; a high score does not establish that a clustering is meaningful or best for every purpose.

```text
weak separation                  clearer separation
  • •  × ×                        • •       × ×
   • × ×                           • •       × ×
```

For a point `i`, the **silhouette coefficient** compares its average distance `a(i)` to other points in its own cluster with `b(i)`, the smallest of its average distances to the points in each *other* cluster:

```text
s(i) = [b(i) − a(i)] / max[a(i), b(i)]
silhouette score = mean of s(i) over all points
```

The coefficient lies from `−1` to `1`: near 1 suggests a point is well separated, near 0 suggests a boundary, and negative values suggest it is closer on average to another cluster. For `a = 2` and `b = 8`, the coefficient is `0.75`. The overall mean can hide weak individual clusters, so inspect individual or cluster-level values too. A singleton cluster's coefficient is conventionally set to zero; silhouette needs at least two clusters and fewer clusters than points. **Source correction:** Page 8 describes `b(i)` as the minimum distance to an element in another cluster; it is the minimum *cluster-average* distance. *Source: pp. 7–8, 17–18; edge cases added.*

The **Dunn index** is the smallest separation between any two clusters divided by the largest within-cluster diameter. One common definition uses the closest pair of points across each pair of clusters for separation and the farthest pair within a cluster for diameter. Larger values favor separation relative to spread. Definitions of distance and diameter vary; compare scores calculated in the same way. Outliers can strongly affect these extremes, and a zero maximum diameter makes the ratio undefined. Neither Dunn nor silhouette supplies a universally correct `k`. *Source: pp. 7, 9; definitions and limitations added.*

## 4. Preparing data and recognizing limits

- **Choose meaningful features and scales.** A feature with large numerical units can dominate Euclidean distance. Standardization or another appropriate scaling can give features more comparable influence; the intended meaning of similarity should guide the choice. Fit transformations on training data only when evaluating assignments on new data, as in Lesson 13.
- **Investigate outliers and errors.** Measurement or equipment errors can distort means and squared distances. Correct or exclude errors with justification; unusual valid cases may be important and should not be removed automatically.
- **Consider redundant features.** Strongly correlated measurements may count essentially the same characteristic multiple times in distance calculations. Review or combine them with domain knowledge rather than automatically including or discarding all of them.
- **Handle categories deliberately.** Integer labels can create artificial order and distances. One-hot encoding can represent nominal categories, but changes their distance contribution and yields fractional centroid coordinates interpreted as category proportions. Scaling and weighting matter; other methods may suit mixed data better.

**Source clarification:** Pages 10 and 20 recommend one-hot encoding for categorical variables and imply mixed categorical and continuous data can be handled after preprocessing. This is possible numerically, but does not guarantee meaningful Euclidean distances or good clusters.

k-Means requires `k` in advance and is sensitive to initialization and outliers. Its distance-to-centroid partitions favor compact, roughly spherical groups in the chosen scaled space; it may split curved or irregular groups, merge unequal-density groups, or impose groups when no natural clusters exist. Consider alternative clustering methods when those assumptions are unsuitable. *Source: pp. 3, 10, 15–16, 19–20; practical qualifications added.*

---

*Source and readability note: Adapted from `[20차시] k-Means 군집분석_Lv2기본.pdf`. All 20 pages were rendered and visually reviewed, including diagrams, quiz questions and answer panels; page numbers count from the title page. Substantive slide concepts are incorporated without quiz format; corrections and useful additions are identified above. Numerical examples were checked. This concise guide is not a certified translation; lecture audio and instructor commentary were unavailable.*
