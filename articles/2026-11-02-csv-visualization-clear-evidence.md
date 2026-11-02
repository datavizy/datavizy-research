# From CSV to Clear Evidence: Choosing and Automating Data Visualizations

A spreadsheet lands on your desk with 240 monthly observations, a column of dates, and a column of revenue. Someone asks whether revenue rises with advertising spend. The file looks tidy, but an input check and scatter plot turn up three blank rows, one value entered as `n/a`, and a single unusually large advertising spend. A chart can make the pattern visible. It cannot, by itself, explain the data or establish why the pattern exists.

That distinction is central to scientific visualization: a figure is a tool for examining and communicating evidence, not a substitute for checking how the evidence was assembled. Python offers tools for different stages of that work, from automated spreadsheet reporting to static figures, interactive exploration, geospatial displays, and statistical graphics. The practical challenge is to match the tool and chart to the question, then make the workflow repeatable enough that the next file gets the same scrutiny.

## Start with the question, not the library

Suppose the question is, “Do higher advertising expenditures tend to accompany higher monthly revenue?” A scatter plot is a reasonable first view because each point represents a paired observation. A histogram answers a different question: how are values of one variable distributed? Neither chart proves a business relationship. A scatter plot can suggest association, but time trends, seasonality, omitted variables, measurement error, and selection effects can all influence its appearance.

Begin by identifying the observational unit and the variables. Is one row a day, a customer, or a region? Are the columns numeric, and do their units agree with the intended interpretation? Check the time period, duplicate records, missing values, and impossible values before charting. A plotted point is only as meaningful as the row it represents.

A useful chart also makes its encoding legible. Label axes with variable names and, where helpful, units. Choose scales that do not exaggerate small differences. State the time window or population in the title or caption. If the figure is meant for comparison, keep scales and definitions consistent across panels. These choices are part of analysis, not decoration added at the end.

## Pick a tool for the job

The visualization landscape described in the selected chapter spans several kinds of work. Excel remains common in financial reporting, and Python libraries such as `openpyxl` and `xlsxwriter` can create and format workbooks, while `openpyxl` can also read existing workbooks. `pandas` can prepare tabular data for export. This combination is useful when stakeholders need a familiar workbook, or when a repeated report must populate a template and include charts. The spreadsheet is a delivery format, however, not a guarantee that the underlying calculations or definitions are correct.

For static figures, Matplotlib provides detailed control over charts, axes, labels, and layout. Seaborn builds on Matplotlib with higher-level interfaces for statistical graphics. They can support publication figures, historical comparisons, and distribution views. Static graphics are easy to archive and include in documents, but they do not let a reader zoom or inspect a point by hovering.

When exploration benefits from interaction, Bokeh and Plotly can create browser-based views with features such as zooming and hover details. Dash uses Plotly to build interactive web applications in Python. These tools can help users explore time windows or inspect values, but interaction does not repair weak data or turn exploratory patterns into confirmed results. A dashboard needs clear definitions and sensible defaults just as a static chart does.

Geospatial tools such as GeoPandas and Plotly can show measurements by region. Maps are appropriate when location is genuinely relevant, but a geographic display can mislead when areas differ in size, population, or the number of observations. Statistical libraries such as SciPy and statsmodels support more advanced analysis and can help visualize quantities such as intervals or distributions. A confidence interval or test result needs its assumptions and interpretation explained; placing it on a chart does not make it self-explanatory.

A small, reproducible plotting command can be useful between raw data and a full report. The accompanying `csv-visualize` utility exports a labeled scatter plot or histogram from a CSV file. It is deliberately limited: it helps inspect numeric columns quickly, but it does not perform regression, financial calculations, or statistical inference.

## Worked example: check the plot’s inputs by hand

Consider a small file with two columns, `spend` and `revenue`, in thousands of dollars:

```csv
spend,revenue
1,2
2,3
3,5
4,6
```

The four points are $(1,2)$, $(2,3)$, $(3,5)$, and $(4,6)$. A scatter plot places spend on the horizontal axis and revenue on the vertical axis. Before interpreting the shape, check a simple descriptive summary. The mean spend is

$$
\bar{x} = \frac{1+2+3+4}{4} = \frac{10}{4} = 2.5.
$$

The mean revenue is

$$
\bar{y} = \frac{2+3+5+6}{4} = \frac{16}{4} = 4.
$$

The deviations from the means are $x_i-\bar{x}=(-1.5,-0.5,0.5,1.5)$ and $y_i-\bar{y}=(-2,-1,1,2)$. Their products are $3, 0.5, 0.5, 3$, which sum to $7$. The sample covariance is therefore

$$
s_{xy} = \frac{7}{4-1} = \frac{7}{3} \approx 2.33.
$$

For a check on the correlation, the sum of squared spend deviations is $5$, while the sum of squared revenue deviations is $10$. Pearson’s sample correlation is the cross-product sum divided by the square root of those two sums:

$$
r = \frac{7}{\sqrt{5\cdot10}} = \frac{7}{\sqrt{50}} \approx 0.990.
$$

The arithmetic is consistent with a strong positive linear association in these four points. But four constructed observations do not justify a prediction about future months or a causal claim that spending increases revenue. The calculation summarizes this sample; it does not address seasonality, other influences, or uncertainty in a larger population. A graph makes the paired values easier to inspect, while the arithmetic makes one summary check explicit.

To export the scatter plot with the utility, save the data as `measurements.csv` and run:

```bash
bash csv-visualize.sh --file measurements.csv --x spend --y revenue --output spend-revenue.svg
```

The utility defaults to a scatter plot. Its JSON response reports the output path, plot kind, number of observations used, and rows omitted. If the file also had a row with a blank revenue value, that row would be omitted from a scatter plot because both selected numeric values must be finite. The report helps account for what was plotted, but it does not explain why a row was incomplete.

## Make automated figures reproducible

Reproducibility depends on more than rerunning a command. Preserve the input data or a stable reference to it, record how it was cleaned, and keep the command and software environment with the output. Give files informative names. For recurring reports, decide whether a new output should be created each run or whether a controlled process should replace an earlier artifact. The utility discussed here refuses to overwrite an existing output, which reduces accidental replacement but means each run needs a fresh filename.

A plot should also be checked after export. Confirm that the axis labels are visible, that the units make sense, and that the plotted observation count matches expectations. A successful file write does not verify the scientific appropriateness of the chart. Inspect outliers and omissions against source records, especially when a small number of rows could change the visual story.

Static and interactive plots serve complementary purposes. An interactive view can help an analyst locate unusual observations; a fixed, captioned figure can make a reviewed result easier to distribute and preserve. A practical workflow may use both, with the same definitions and data checks feeding each output. For Excel-based reporting, automated workbook generation can make recurring layouts consistent. The review still needs to verify formulas, data types, and how missing values are represented.

## Assumptions and common traps

A scatter plot treats each row as a pair of numeric values. That is not the same as treating rows as independent, representative samples. Repeated measurements from one subject, related observations over time, or clustered regional data require analysis that respects that structure. A visually tight relationship can arise from a shared time trend or a third variable. Correlation describes a particular kind of association, not a cause-and-effect mechanism.

Histograms summarize one numeric variable by grouping values into bins. The chosen binning affects the display: coarse bins can hide structure, while fine bins can make random variation look like meaningful detail. A histogram does not preserve which individual observation had which value. When comparing groups, use comparable bin choices and explain the sample sizes.

Missing and nonfinite observations deserve explicit attention. Dropping incomplete rows may be convenient for a plot, but missingness can be systematic. For example, a measurement may be absent more often during a particular period. If so, the points that remain may not represent the full dataset. Record how many rows were excluded, inspect why, and consider whether the analysis requires a different treatment. Never silently convert an invalid value into a plausible measurement.

Finally, avoid over-reading precision. A chart with many pixels does not imply a large sample, reliable measurement, or narrow uncertainty. A visually striking pattern is a reason to ask a sharper question and check a suitable analysis, not to skip that work.

## Applications beyond finance

These principles apply wherever numeric data need inspection: laboratory measurements, manufacturing quality checks, public health summaries, and environmental monitoring. A histogram can flag a suspicious distribution or an instrument range issue. A scatter plot can reveal a nonlinear pattern that a single correlation number might obscure. A map can show geographic variation when regions and denominators are handled carefully. A workbook can deliver a repeatable report to colleagues who need tabular detail as well as figures.

The tool landscape is broad because communication needs differ. Static graphics suit fixed reports; interactive graphics support exploration; spreadsheets fit familiar reporting workflows; statistical software helps quantify questions that visual inspection alone cannot answer. Choose based on the audience, the analytical question, and the evidence required. The most credible visualization is not necessarily the most elaborate one. It is the one whose data, encodings, and limits readers can understand.

## Exercises with short answers

1. A scatter plot has an upward pattern. Does this establish that the horizontal variable causes the vertical variable to rise?

   **Answer:** No. The figure shows an association in the plotted data. Confounding, time trends, selection, and other explanations remain possible.

2. A histogram changes shape when you change the number of bins. Is one display automatically correct?

   **Answer:** No. Bin choices affect visible detail. Inspect reasonable alternatives and describe the choice, especially when the conclusion depends on it.

3. Your plotting tool reports 12 omitted rows. What should you do next?

   **Answer:** Check which fields were incomplete or nonfinite and whether omissions cluster by group or time. Decide whether the remaining observations still address the question.

Visualization makes data easier to examine, but reliability comes from the full chain: a well-defined question, checked inputs, suitable encodings, reproducible production, and honest interpretation. Python can automate much of the production. It cannot decide what the data mean without careful analysis.

---

Published 2026-11-02.

**Source:** Data Visualization in Finance with Python_ A Comprehensive -- Schwartz, Alice & Van Der Post, Hayden -- 2024 -- Reactive Publishing, section **CHAPTER 5: VISUALIZATION TOOLS**.

This is an original explanatory note; the source book is not redistributed.
