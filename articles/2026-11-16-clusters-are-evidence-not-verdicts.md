# Clusters Are Evidence, Not Verdicts

A heat map shows three crisp bands, and the team is ready to name them: low, medium, and high response. Then someone standardizes the measurements, changes the linkage rule, and the bands shift. Which picture is the truth?

Neither picture earns that title just by looking tidy. Clustering is a useful way to explore structure without supplying group labels, but its output depends on choices made before and during the analysis. A sound interpretation asks which patterns survive reasonable changes, not merely which run produces the prettiest plot.

## A cluster is the result of a recipe

A clustering algorithm does not discover a single, inevitable partition waiting inside a data set. It applies a definition of similarity and a procedure for grouping observations. Change the ingredients, and the result may change.

For example, suppose observations have two measurements: annual energy use in kilowatt-hours and building age in years. If Euclidean distance is used on the raw measurements, a difference of 100 kilowatt-hours can dominate a difference of 10 years simply because the numeric scales differ. Standardizing each feature to mean zero and standard deviation one changes the relative influence of the measurements. That could be appropriate when both features deserve comparable weight, but it is not automatically correct: if energy use is the scientific focus, reducing its influence may be undesirable.

Linkage matters too in hierarchical clustering. Single linkage defines the distance between two groups by their closest pair of observations. Complete linkage uses their farthest pair, while average linkage uses the average of pairwise distances. These rules can produce different branches in a dendrogram and different groups at the same cut. The choice is not cosmetic. It expresses what kinds of groups the method tends to form.

The section’s practical advice is to vary such decisions and examine the full collection of results. This is not a demand to try every possible setting. It is a way to test whether an interpretation depends on one fragile choice. State the alternatives you considered and why they are plausible for the measurements and research question.

## A small worked example: a recurring pair

Consider six fictional field stations measured on two variables: a standardized soil-moisture index and a standardized canopy index. The coordinates are on standardized scales, but they are illustrative and do not have exact mean zero and standard deviation one within these six stations. The observations are:

| Station | Moisture | Canopy |
|---|---:|---:|
| Alder | -2.0 | -1.8 |
| Birch | -1.8 | -2.1 |
| Cedar | -0.1 | 0.0 |
| Dogwood | 0.1 | 0.2 |
| Elm | 1.8 | 2.0 |
| Fir | 2.1 | 1.7 |

A quick scatterplot suggests three nearby pairs. Check the distances for Alder and Birch. Their Euclidean distance is

$$
d(A,B)=\sqrt{(-2.0+1.8)^2+(-1.8+2.1)^2}=\sqrt{0.04+0.09}=\sqrt{0.13}\approx0.361.
$$

For Cedar and Dogwood, the distance is

$$
d(C,D)=\sqrt{(-0.1-0.1)^2+(0.0-0.2)^2}=\sqrt{0.04+0.04}=\sqrt{0.08}\approx0.283.
$$

For Elm and Fir, it is

$$
d(E,F)=\sqrt{(1.8-2.1)^2+(2.0-1.7)^2}=\sqrt{0.09+0.09}=\sqrt{0.18}\approx0.424.
$$

These calculations support the visual impression that each pair is close. They do not prove that the pairs are three natural biological types. To investigate stability, an analyst might compare complete and average linkage, then repeat the analysis after leaving out one station at a time. If Alder and Birch repeatedly join early, even when other observations are omitted or a reasonable linkage choice changes, that pairing is more robust than a group that appears only under one recipe.

The example is deliberately small. With only six stations, a single observation can alter distances and branch structure substantially. And the standardized values are an assumption, not a neutral transformation. If the original measurement units carry meaningful relative importance, standardizing both variables may change the scientific question.

## Test stability, not just visual appeal

A useful robustness check changes one design decision at a time. For example:

1. Compare a justified set of feature scalings, such as raw values and standardized values.
2. Compare suitable linkage rules or distance measures.
3. Repeat clustering on subsets of observations, or use repeated resamples, to see whether proposed groups recur.
4. Record the choices, outputs, and any preprocessing so another analyst can reproduce the comparison.

Changing one factor at a time helps reveal what drove a change. Later, combinations of choices can test whether two decisions interact. There is no universal number of runs that establishes stability. A large pile of results is not useful if the alternatives are arbitrary or if the analyst reports only the convenient ones.

Subset analysis needs careful interpretation. When a member is omitted, a cluster can split, merge, or disappear because the algorithm is run on a different data set. Compare the memberships of observations shared between runs, rather than expecting cluster labels such as “1” and “2” to retain meaning. Cluster labels are names assigned by software, not measurements. A cluster called 1 in one run may correspond to cluster 3 in another.

For a more formal comparison, one can summarize how often each pair of observations appears together across repeated runs. That frequency is a descriptive stability measure, not a probability that the pair belongs to a true group. Its value depends on the resampling design, the clustering settings, and the observed data.

## From exploratory pattern to scientific question

The central caution is about reporting. A cluster assignment is not an absolute truth about the data. It is a result under particular definitions, measurements, and algorithmic choices. Treat it as a starting point for a scientific hypothesis and further study.

Suppose a recurring group of stations has low moisture and sparse canopy. A useful next step is to ask whether soil drainage, disturbance history, or another measured factor could explain the pattern. Those are hypotheses to investigate, not conclusions supplied by the clustering. If possible, test the proposed interpretation with an independent data set. An independent set can reveal whether a pattern generalizes beyond the observations that helped inspire it.

When presenting results, report the features included, their scaling, the distance and linkage choices, and how sensitive the proposed groups were to plausible alternatives. Show ambiguity where it exists. A dendrogram cut or colored scatterplot can communicate a partition, but it should not hide the decisions that produced it. If several reasonable analyses disagree, that disagreement is itself a result worth discussing.

Clustering is especially helpful when labels are unavailable or when researchers want to explore broad structure before defining a targeted analysis. It can suggest candidate subgroups, flag unusual observations, or organize a high-dimensional data set for follow-up. It cannot, on its own, tell us why groups formed, whether they are real categories, or whether group membership causes an outcome.

## Assumptions and common traps

Every analysis embeds assumptions. Distance-based methods assume that the selected representation makes distances meaningful. Standardization assumes that each feature’s variation should be put on a comparable scale, which can amplify noisy or low-importance features. Linkage determines how cluster-to-cluster distance is summarized. K-means, another common method, seeks compact groups under squared Euclidean distance and can depend on its starting assignments. These methods answer related but not identical questions.

Several mistakes are easy to make:

- **Calling a convenient cut “the” answer.** A dendrogram can be cut at different heights, producing different counts of groups. Explain why a cut is useful for the research goal.
- **Ignoring preprocessing.** Missing-value handling, transformations, outlier treatment, and scaling can all affect the geometry.
- **Overreading a plot.** A two-dimensional projection can obscure structure present in more dimensions, or suggest separation that is weak in the original feature space.
- **Treating stability as truth.** A pattern can recur because of a systematic bias or a feature choice, and a stable pattern is not automatically scientifically meaningful.
- **Explaining clusters after the fact without testing.** Descriptions of group differences can generate hypotheses, but they do not establish mechanisms or causal effects.

A careful analysis does not make uncertainty vanish. It makes the sources of uncertainty visible and gives readers enough information to judge how much weight a proposed pattern deserves.

## Exercises with short answers

**1.** A clustering result changes when one high-range feature is standardized. Is the standardized result necessarily better?

**Answer:** No. Standardization changes the relative contribution of features. Choose it based on the scientific meaning of the variables, then report and compare defensible alternatives.

**2.** Two runs produce the same groups but swap labels 1 and 2. Did the result change?

**Answer:** Not in membership. Cluster identifiers are arbitrary labels; compare which observations are grouped together.

**3.** A proposed group recurs across several subsets. Can you conclude that it is a distinct biological type?

**Answer:** No. Recurrence supports stability under those checks, but biological interpretation needs scientific reasoning and further validation, preferably on independent data.

## A practical standard

The strongest clustering report is not necessarily the one with the cleanest separation. It is the one that shows what was tried, what persisted, what changed, and what remains uncertain. Use the recurring patterns to sharpen a question, then seek evidence beyond the clustering run. That is a tempered interpretation: curious about structure, honest about choices, and unwilling to mistake an algorithm’s partition for nature’s final word.

---

Published 2026-11-16.

**Source:** An Introduction to Statistical Learning  with Applications in R, section **401 A Tempered Approach to Interpreting the Results of Clustering**.

This is an original explanatory note; the source book is not redistributed.
