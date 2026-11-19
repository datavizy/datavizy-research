# From Arrays to Evidence: Building a Reproducible Visualization Workflow

A CSV file lands in your inbox with two columns, `dose` and `response`. You can make a scatter plot in a few lines, but the plot is only useful if the values were interpreted correctly, the axes are labeled, and someone else can regenerate the same figure later. That small gap between “it ran” and “we can trust and reuse it” is where scientific computing habits matter.

A scientific Python workflow often connects three ideas: represent data in well-defined structures, summarize or transform those data carefully, and communicate the result with a plot whose meaning is visible. Python’s numerical and plotting ecosystem makes these steps convenient, but convenience does not guarantee correct analysis. Reproducible work comes from making assumptions, transformations, and outputs explicit.

## Arrays are contracts about shape and type

A Python list can hold values, but numerical libraries need more information to perform operations efficiently and predictably. A NumPy array has a data type and a shape. Those properties are not decoration: they determine how an operation behaves and whether it is valid.

For example, an array with shape `(4,)` is one-dimensional and contains four values. An array with shape `(2, 2)` has two axes, each of length two. Both contain four elements, but they do not represent the same structure. A two-column measurement table might be represented as an $n \times 2$ array, while separate `x` and `y` vectors each have shape `(n,)`.

Three useful checks are `size`, `ndim`, and `shape`. `size` is the total number of elements; `ndim` is the number of axes; and `shape` gives the length of each axis. These checks catch common mistakes before they turn into plausible-looking results. Reshaping changes the arrangement, not the number of values, so a reshape is possible only when the old and new shapes have equal products. A vector of 12 values can become `(3, 4)`, but not `(3, 5)`.

Array creation methods express different intentions. `arange` uses a step size, while `linspace` asks for a specified number of evenly spaced samples over an interval. That distinction matters near endpoints: a step-based sequence is not a promise to include a chosen endpoint. For a grid or a fixed number of plotting coordinates, decide which meaning you need instead of choosing whichever function looks familiar.

Operations also need clear interpretation. Adding arrays of matching shape is element-wise. Multiplication with `*` is element-wise too. Matrix multiplication is a different operation, commonly written with `@` in modern Python. A transpose swaps axes, and a reduction such as a sum can operate across a selected axis. If a matrix has rows as samples and columns as variables, summing across columns within each row differs from summing each column across samples. State what each axis represents before interpreting a reduction.

## A complete worked example: summarize and plot paired measurements

Suppose a lab records five paired measurements. Each pair is the instrument reading before and after a calibration adjustment:

| Sample | Before | After |
|---|---:|---:|
| A | 10 | 12 |
| B | 12 | 13 |
| C | 9 | 11 |
| D | 15 | 16 |
| E | 14 | 18 |

The point of this example is descriptive comparison, not a claim that calibration caused a change. We can encode the values and calculate each paired difference, defined as `after - before`:

```python
import numpy as np

before = np.array([10, 12, 9, 15, 14], dtype=float)
after = np.array([12, 13, 11, 16, 18], dtype=float)
difference = after - before

print(difference)
print("mean difference:", difference.mean())
print("minimum:", difference.min())
print("maximum:", difference.max())
```

The differences are `[2, 1, 2, 1, 4]`. Their sum is $2 + 1 + 2 + 1 + 4 = 10$, so their mean is $10 / 5 = 2$. The minimum is 1 and the maximum is 4. These calculations are directly checkable, but they describe only these five pairs. They do not estimate uncertainty, establish a population effect, or explain why values differ.

A scatter plot can show the pairing: put the before reading on the horizontal axis and the after reading on the vertical axis. The points are `(10, 12)`, `(12, 13)`, `(9, 11)`, `(15, 16)`, and `(14, 18)`. Equal values on both axes would lie on the reference line $y=x$. A point above it has a higher after reading than before. That visual comparison is descriptive; it is not a causal argument.

```python
import matplotlib.pyplot as plt

fig, ax = plt.subplots()
ax.scatter(before, after)
ax.set_xlabel("Before reading")
ax.set_ylabel("After reading")
ax.set_title("Paired instrument readings")
ax.grid(alpha=0.2)
fig.savefig("paired-readings.png", dpi=180)
plt.close(fig)
```

The labels identify what the axes measure, the title describes the displayed data, and the saved file is an artifact that can be inspected or included in a report. For a more complete analysis, preserve sample identifiers, document units, assess measurement conditions, and choose an appropriate inferential method if the research question requires one.

## Plots are arguments, not ornaments

Matplotlib supports many graphical forms, including lines, scatter plots, histograms, and heat maps. Select a plot by asking what structure in the data should be visible. Scatter plots show paired numeric values and can reveal clusters, curvature, or unusual observations. Histograms show the distribution of one numeric variable, with counts grouped into bins. A histogram does not preserve the pairing between two variables.

Axis labels should include units when available. A title should tell readers what is shown, not overstate what the data prove. A scatter plot can reveal association patterns, but a visual pattern alone does not establish causation. A histogram can show concentration and spread, but its appearance depends partly on binning. Color can encode a group, but the legend or labeling must explain that encoding. Avoid adding visual detail that implies distinctions the data do not support.

A plotting routine that saves directly to an image file is useful on servers and in automated pipelines. A headless renderer can create a figure without opening a desktop window. That makes batch jobs practical, but it also means the output should be reviewed in a viewer: successful file creation is not a guarantee that labels, ranges, or the chosen chart are appropriate.

## Reproducibility begins before the plot

A repeatable analysis needs more than a script. Record the Python and dependency versions, keep source data unchanged, and make transformations explicit. A project-local virtual environment helps isolate dependencies from other projects. It does not make the entire environment magically permanent, so record or pin dependencies according to the project’s maintenance needs.

Random data require special care. A call to a random-number generator produces different values across runs unless a seed or explicit random generator state is controlled. If a random sample is part of a demonstration or simulation, record the seed and generation method. If randomness is not part of the scientific design, do not introduce it merely to make a plot look populated.

Data cleaning should be inspectable too. Missing values, blank strings, infinities, and invalid numeric text are not interchangeable. Decide how each should be treated, count exclusions, and report them. Silently dropping rows can change the dataset in ways that matter, especially if missingness is systematic. Keep a record of the number of input rows and the number used in each calculation or plot.

## Practical applications and limits

The same workflow supports quality-control checks, exploratory research, teaching demonstrations, and reporting. A scatter plot can help inspect whether two measurements move together. A histogram can quickly reveal a long tail or several concentrations that deserve investigation. Array operations can make a calculation concise and efficient once the meaning of each dimension is settled.

But an exploratory visualization is not a complete statistical analysis. A plot does not supply a sampling design, uncertainty interval, test of a hypothesis, or causal identification strategy. It can help formulate questions and detect data issues. Answering stronger questions requires a method suited to the design, assumptions checked against the data, and uncertainty communicated appropriately.

There are also engineering limits. Numeric conversion may fail for text values or locale-specific number formats. A finite numeric value can still be erroneous, such as a temperature recorded in the wrong unit. A chart may be technically valid but misleading because its scale, labels, or aggregation hide important context. Reproducibility requires both repeatable computation and responsible interpretation.

## Exercises with short answers

1. An array contains 24 values. Can it be reshaped to `(4, 6)`? **Yes.** Both shapes contain 24 elements.
2. A histogram has 80 observations across its bins. Does that show 80 independent experimental units? **Not necessarily.** Independence comes from the study design, not the chart.
3. Two columns have values that increase together in a scatter plot. Does that establish that one causes the other? **No.** The plot shows an observed association pattern, not a causal mechanism.
4. A script drops rows containing nonfinite values. What should a report include? **At minimum, the handling rule and the number of omitted rows, so readers can understand which observations contributed.**

The central habit is simple: make the structure of the data visible, make each operation match its intended meaning, and make the resulting figure traceable to its inputs and environment. That is how a short plotting script becomes a useful piece of scientific work.

---

Published 2026-11-19.

**Source:** Python data science cookbook - over 60 practical recipes, section **Chapter 2. Python Environments**.

This is an original explanatory note; the source book is not redistributed.
