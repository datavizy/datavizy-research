# Choose the Measurement Before You Choose the Plot

A spreadsheet column contains the values `1`, `2`, `3`, and `4`. Is it a measurement? A rating? A code for four regions? The plot you choose depends on the answer. Treating every number as a quantity can produce a polished chart that quietly says something false.

A useful first step in data analysis is to ask what a value means, not just what characters appear in a cell. The section of *Hands-On Data Science and Python Machine Learning* that motivates this discussion moves from basic programming and running scripts to a refresher on data types and descriptive statistics. Its central practical point is that data type matters: numerical, categorical, and ordinal data have different meanings, and analytical techniques should respect those meanings. This article develops that idea into a workflow for summaries, plots, and small reproducible programs.

## Numbers do not always measure quantities

Numerical data represent amounts or counts. A sensor temperature, a duration, and the number of visits to a clinic are all numerical, though they differ in their possible values. Discrete numerical data are counts or other values from a separated set, commonly integers. Continuous numerical data can, in principle, take arbitrarily fine values within a range. A recorded continuous measurement is still limited by instrument precision and rounding.

Categorical data identify groups. A device model, a color, or a region code can be represented by text or numbers, but the labels themselves do not imply arithmetic. If a researcher encodes three device models as 1, 2, and 3, the mean code of 2 is not an average device model. Relabeling the models as 10, 20, and 30 would change that mean without changing the underlying observations.

Ordinal data have an order, but the spacing between successive levels may not be meaningful or equal. For example, ratings of poor, fair, good, and excellent have a natural ordering. Encoding them as 1, 2, 3, and 4 preserves that ordering, but does not establish that the perceived gap between poor and fair equals the gap between good and excellent. An ordinal variable can sometimes be treated as numerical for a particular analysis, but that choice requires a defensible interpretation, not merely numeric storage.

The boundary between discrete and continuous depends partly on the measurement process. Age recorded as completed years is integer-valued in the dataset; age recorded in days or seconds has finer resolution. The underlying quantity does not change, but the recorded representation does. In practice, document the units, resolution, and coding rules alongside the column name.

## A worked example: inspect readings before summarizing

Suppose a lab records the temperature rise of a small test component, in degrees Celsius, after a fixed five-minute run. The CSV contains these readings:

`1.2, 1.5, 1.5, 1.8, 9.0`

These are continuous numerical measurements as represented by the instrument, even though they are recorded to one decimal place. The value 9.0 is unusually large relative to the others. It could be a genuine result, a transcription mistake, or evidence of a different operating condition. A summary can describe the data, but cannot decide which explanation is correct.

The arithmetic mean is the sum divided by the number of observations:

$$
\bar{x} = \frac{1.2 + 1.5 + 1.5 + 1.8 + 9.0}{5}
= \frac{15.0}{5} = 3.0\text{ °C}.
$$

The sorted values are `1.2, 1.5, 1.5, 1.8, 9.0`, so the median, the middle observation, is $1.5$ °C. The mode, a most frequently occurring value, is also $1.5$ °C. Here, the mean is higher than the median because the single large value contributes heavily to the sum. That is a clue to inspect the observation, not a license to delete it.

For a sample variance, use the deviations from the sample mean and divide their squared sum by $n-1$:

$$
s^2 = \frac{(-1.8)^2 + (-1.5)^2 + (-1.5)^2 + (-1.2)^2 + 6.0^2}{4}
= \frac{45.18}{4} = 11.295\text{ °C}^2.
$$

The sample standard deviation is $s = \sqrt{11.295} \approx 3.361$ °C. This calculation uses the sample-variance convention, not division by $n$. It describes spread around the mean, and its units return to degrees Celsius after taking the square root. The large value inflates both mean and standard deviation. Median and a plot of the individual observations provide useful context, but none of these summaries proves that the reading is erroneous.

## Match the plot to the question

A histogram groups numerical observations into bins and shows how many fall in each bin. It can reveal clusters, gaps, skew, and possible unusual values. Its appearance depends on binning, so a different bin width can make structure more or less visible. A histogram also discards the identity and ordering of individual rows.

A scatter plot displays paired numerical measurements. Each point corresponds to an observation, with one variable on the horizontal axis and another on the vertical axis. It can reveal association, curvature, clusters, and outliers. Association is not evidence of causation: a scatter plot alone does not control for confounding, establish direction, or explain a mechanism.

For categories, use a display that treats labels as groups, such as a count or proportion chart, rather than feeding arbitrary category codes into a numerical histogram. For ordinal data, preserve the stated order when displaying levels. A scatter plot is appropriate when both selected columns are numeric and paired row by row. A one-column histogram is appropriate for a numerical variable when a distribution overview is useful.

Axes and titles are part of the analysis. State what each variable measures and include units when known. A plot titled “Results” with axes labeled `x` and `y` forces readers to guess. A clear title and labels do not make a weak analysis strong, but they make its scope easier to inspect.

## From an interactive experiment to a repeatable script

The source section also distinguishes notebooks, standalone scripts, and interactive prompts as ways to run Python. They serve different stages of work. A notebook is useful for exploration and explanation; an interactive prompt supports quick checks; a script can make a repeatable task easy to run again. The same principle applies to visualization: make the transformation explicit, keep the input file identifiable, and save a labeled output rather than relying on a screenshot of a transient display.

A small command-line plotting tool can help create a first-pass scatter plot or histogram directly from CSV. That plot is descriptive and exploratory. It does not calculate a hypothesis test, assess uncertainty, fit a predictive model, or establish a causal effect. Those questions need additional methods and assumptions.

## Assumptions and common traps

A mean is most interpretable when numerical values have meaningful distances and units. It is sensitive to extreme values. A median depends on order and is often less affected by a single extreme observation, but it does not describe every feature of a distribution. The mode can be useful for repeated values or categories, though a dataset may have multiple modes or none that is especially informative.

Always ask how missing, malformed, or nonfinite values are handled. A summary computed after dropping incomplete rows describes the remaining observations, not automatically the intended population. Record the number excluded and consider whether their absence is systematic. A plotting utility that omits rows with missing or nonfinite selected values should make that omission visible in its output report.

Other frequent mistakes include averaging category codes, interpreting ordinal steps as equal intervals without justification, comparing measurements with incompatible units, and choosing histogram bins only because they make a preferred pattern appear. Check raw rows and data provenance before treating an unusual point as an error. A chart is a view of the data under chosen rules, not a replacement for those rules.

## Practice: classify, calculate, inspect

1. A file stores the names of four experiment groups as codes 1 through 4. Are the codes numerical measurements?
2. A five-point satisfaction response ranges from “very dissatisfied” to “very satisfied.” What kind of data is it?
3. For `2, 4, 4, 10`, calculate the mean and median.
4. What can a scatter plot of study time and quiz score show, and what can it not establish by itself?

### Short answers

1. No. They are categorical labels unless the codes represent measured quantities.
2. Ordinal, because the levels have order but equal spacing is not guaranteed.
3. Mean: $(2+4+4+10)/4=5$. Median: the average of the middle values, $(4+4)/2=4$.
4. It can show a pattern of association and unusual points. It cannot, by itself, show that study time caused a score change.

A reliable analysis starts with meaning, then chooses a summary or visualization that fits that meaning. Keep the calculation visible, label the output, and treat a plot as an invitation to investigate rather than a verdict.

---

Published 2026-11-10.

**Source:** Hands-On Data Science and Python Machine Learning, section **0 1 2 3 4 5 6 7 8 9**.

This is an original explanatory note; the source book is not redistributed.
