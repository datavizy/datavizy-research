# Correlation, Regression, and the Shape of a Relationship

A scatterplot of two measurements can look like a cloud, a tilted stripe, or a smooth curve. Those shapes matter. If you summarize every relationship with one number, a curved pattern can disappear into a correlation near zero. If you turn an association into a prediction, you also need to be clear about what your model does, and does not, establish.

The selected section of *Biostatistics For Dummies, 2nd Edition*, Chapter 15, introduces correlation and regression as methods for studying relationships among variables. It distinguishes correlation, which describes the strength and direction of association, from regression, which models how an outcome varies with one or more other variables. Its central warning is especially useful in practice: Pearson correlation describes linear association, not every possible relationship.

## Start with paired measurements and a plot

A correlation requires paired values: each $X$ value must belong to the same observational unit as its corresponding $Y$ value. For example, a participant's height and weight form a pair. A spreadsheet with heights from one group and weights from another does not become suitable for correlation merely because the columns have the same number of rows.

Before calculating a coefficient, plot the pairs. A scatterplot can reveal a curve, separate clusters, an outlier, or a data-entry mistake that a single summary statistic might conceal. Also check that each row represents the intended unit and that the pairing has not been disrupted by sorting one column independently.

## What Pearson's $r$ measures

Pearson's correlation coefficient, usually written $r$, summarizes the direction and strength of a *linear* relationship between two quantitative variables. It ranges from $-1$ to $1$. A positive value indicates that larger values of one variable tend to occur with larger values of the other. A negative value indicates that larger values of one tend to occur with smaller values of the other. Values near zero indicate little linear association in the observed data.

For paired observations $(x_i,y_i)$, one formula is

$$
r = \frac{\sum_{i=1}^{n}(x_i-\bar{x})(y_i-\bar{y})}{\sqrt{\sum_{i=1}^{n}(x_i-\bar{x})^2\sum_{i=1}^{n}(y_i-\bar{y})^2}}.
$$

The numerator measures whether the deviations from the two means tend to have the same or opposite signs. The denominator scales that sum by the spread in each variable. This scaling makes $r$ unitless: changing centimeters to meters does not change the coefficient.

A perfect positive straight-line pattern has $r=1$; a perfect negative straight-line pattern has $r=-1$. A value of zero does not mean that the variables are unrelated in every sense. For instance, a symmetric U-shaped pattern can have strong structure while positive and negative deviations cancel in the numerator. A plot is how you notice the shape that $r$ cannot describe.

Correlation is also not a measure of causation. A strong association may reflect a causal effect, a shared cause, selection, or another process. The coefficient alone cannot distinguish these explanations. Nor does a high absolute value of $r$ tell you whether a change in $X$ produces a meaningful change in $Y$ on the original measurement scales.

## A worked example, from arithmetic to interpretation

Suppose five laboratory samples have the following paired measurements. Let $X$ be a preparation reading and $Y$ be a response reading, both in arbitrary units:

| Sample | $X$ | $Y$ |
|---|---:|---:|
| 1 | 1 | 2 |
| 2 | 2 | 3 |
| 3 | 3 | 5 |
| 4 | 4 | 4 |
| 5 | 5 | 6 |

The means are $\bar{x}=3$ and $\bar{y}=4$. The deviations are $-2,-1,0,1,2$ for $X$, and $-2,-1,1,0,2$ for $Y$. Their cross-products sum to $4+1+0+0+4=9$. Each set of squared deviations sums to $4+1+0+1+4=10$; for $Y$, the same total is $4+1+1+0+4=10$. Therefore,

$$
r = \frac{9}{\sqrt{10\cdot10}} = 0.9.
$$

These five observations show a strong positive linear association. That is a description of this sample, not proof that the preparation reading causes the response. The points also do not lie perfectly on a line, since $r$ is less than one.

The chapter excerpt describes testing whether a population correlation is zero using a $t$ statistic derived from the observed $r$ and sample size $N$. The standard test statistic is

$$
t = r\sqrt{\frac{N-2}{1-r^2}},\qquad df=N-2.
$$

For this example, $N=5$, so $df=3$. Since $r^2=0.81$,

$$
t=0.9\sqrt{\frac{3}{0.19}}\approx 3.576.
$$

Under the usual two-sided test, this corresponds to a $p$ value of about $0.037$ for 3 degrees of freedom. The arithmetic can be checked from the formula; the rounded $p$ value is approximate. This result is evidence against a zero population linear correlation under the test's assumptions. It is not a measure of effect importance, proof of causation, or a guarantee that the relationship will replicate.

## The test has assumptions, and a small sample has limits

The formula and reference $t$ distribution are used for inference about a population correlation under assumptions, not as a universal certificate of association. In the usual setting, paired observations are independent across units, and the data are compatible with a bivariate normal model for the exact test. Strong outliers can heavily affect Pearson's $r$. A nonlinear relationship, restricted range, dependence among repeated measurements, or influential observations can make the summary or test misleading.

The test asks whether the population linear correlation is zero, against a specified alternative, often a two-sided alternative. A small $p$ value does not give the probability that the null hypothesis is true. A large $p$ value does not establish independence or rule out a nonlinear relationship. Report the coefficient, sample size, uncertainty where appropriate, and a plot, rather than treating a threshold as the whole result.

In biological work, sample units need particular care. Measurements from the same animal, patient, litter, or experimental batch may not be independent. Treating repeated measurements as independent can make inferential results unreliable. The simple correlation test does not model that structure; an analysis designed for the repeated or clustered data may be needed.

## Correlation and regression answer different questions

Correlation is symmetric: swapping $X$ and $Y$ leaves $r$ unchanged. Regression is directional. A regression model specifies an outcome and predictors, such as modeling response $Y$ as a function of preparation reading $X$. A straight-line regression can estimate a predicted outcome and a slope in the units of $Y$ per unit of $X$. More advanced regression methods can handle multiple predictors, different outcome types, or nonlinear terms.

That flexibility does not make regression automatically causal. A model can describe or predict conditional associations without establishing what would happen under an intervention. Causal interpretation depends on study design, assumptions, and analysis choices beyond the fact that a regression was fitted. The chapter's framing is a useful reminder that these techniques span many kinds of problems, rather than one universal recipe.

## A practical workflow for analysis

1. Define the observational unit and confirm that each pair belongs together.
2. Inspect the data, missingness, units, and plausible ranges. Verify any surprising values against their source.
3. Plot $X$ against $Y$. Look for curvature, clusters, changing spread, and influential observations.
4. Choose a summary that matches the question and data. Pearson's $r$ is for linear association; a different method may suit a different structure.
5. If testing, state the null and alternative, check the assumptions, and report $N$, $r$, and the test result with context.
6. Keep association, prediction, and causal claims distinct. Explain what the study design supports.

This workflow applies to many settings, from checking whether two laboratory measurements move together to exploring relationships among clinical variables. In each case, a scatterplot and a clear description of the units can save an analysis from a polished but incorrect conclusion.

## Exercises with short answers

**1.** A dataset has Pearson $r=0.02$. Can you conclude that its variables are independent?  
**Answer:** No. It indicates little observed linear association, but a nonlinear relationship may remain.

**2.** If you exchange the roles of $X$ and $Y$, does Pearson's $r$ change?  
**Answer:** No. Correlation is symmetric. A regression model's outcome and predictor roles, however, are directional.

**3.** In the worked example, what is the test's degrees of freedom?  
**Answer:** $N-2=5-2=3$.

A useful summary is this: correlation compresses a pattern into a measure of linear direction and strength. Regression builds a directional model for an outcome. Neither replaces looking at the data, and neither alone turns association into causation.

---

Published 2026-11-06.

**Source:** Biostatistics For Dummies, 2nd Edition, section **Chapter 15 Introducing Correlation and Regression**.

This is an original explanatory note; the source book is not redistributed.
