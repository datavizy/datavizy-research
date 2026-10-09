# Glossary

New concepts are appended as research notes are published.


<!-- publication:2026-10-02 -->

## 2026-10-02

Source note: [2026-10-02](articles/2026-10-02-duplicate-keys-join-multiplication.md).

### Join key

A column or set of columns used to identify corresponding records across tables. A key may repeat on one side when multiple observations belong to the same entity; uniqueness must be assessed according to the intended relationship, not inferred from the column’s label.

### Join cardinality

The relationship between the number of records on each side of a join for a key, commonly described as one-to-one, one-to-many, many-to-one, or many-to-many. Cardinality determines whether enrichment preserves row counts or multiplies them.

### Many-to-many join

A join in which at least one key value occurs multiple times on both sides. For a particular key with $m$ left rows and $n$ right rows, ordinary matching creates $m \times n$ pairs, which can substantially increase output rows.

### Duplicate row

A row whose recorded values match another row over the columns being compared. This is a statement about recorded values, not proof that the rows represent the same real-world event.

### Observation grain

The precise unit represented by one row, such as one purchase, one patient visit, or one station-day measurement. Stating the grain clarifies which repetitions are expected and what a valid unique identifier should distinguish.

### Surrogate identifier

A value assigned to identify a record when a natural, stable identifier is unavailable. It can preserve distinct records with identical measured fields, but a generated row number may depend on input order and should not be mistaken for a durable identity.

### Deduplication

Removing records judged redundant under an explicit rule, such as equality across selected columns or duplication of a known identifier. The rule determines what is considered redundant; deduplicating without understanding the data grain can erase valid observations.

### Unmatched key

A key value in one table for which no corresponding value exists in the other table. Unmatched keys can reflect incomplete coverage, inconsistent formatting, or genuine absence, and should be counted and interpreted rather than hidden.

### Finite numeric observation

A numeric value that is neither missing nor infinite nor NaN. Visualization and statistical routines often cannot use such values directly; handling them explicitly makes the analyzed sample size and omissions easier to report.


<!-- publication:2026-10-05 -->

## 2026-10-05

Source note: [2026-10-05](articles/2026-10-05-from-events-to-evidence-discrete-probability.md).

### Outcome space

The set of all possible outcomes in a probability model, often written as $\Omega$. In the discrete setting discussed here it is finite or countably infinite. Defining the space clearly prevents ambiguity about what the model treats as a possible result.

### Event

A subset of the outcome space. Its probability is the total probability assigned to its outcomes. Events can overlap, be disjoint, or contain one another, and these relationships determine how their probabilities combine.

### Probability measure

A function assigning probabilities to events, with probability zero for the empty event, probability one for the whole space, and countable additivity over disjoint events. For a discrete model, point probabilities determine the measure.

### Countable additivity

The rule that the probability of a countable union of pairwise disjoint events equals the sum of their probabilities. It is a core probability axiom and supports calculations that partition outcomes into non-overlapping cases.

### Conditional probability

The probability of an event $D$ given an event $C$ known to have occurred, defined as $P(C\cap D)/P(C)$ when $P(C)>0$. It restricts attention to outcomes inside $C$ and renormalizes their probabilities.

### Independence

A relationship between events in which their joint probability factors as the product of their probabilities. For two events, $P(C\cap D)=P(C)P(D)$. Mutual independence of a collection requires this factorization for every finite subcollection.

### Random variable

A function from outcomes to real numbers. It makes a numerical feature of a random experiment available for analysis, such as a count or measurement, and induces a distribution over its possible values.

### Expectation

The probability-weighted average of a random variable's values, defined in the discrete case by $E[X]=\sum_x xP(X=x)$ when the absolute expectation is finite. It summarizes a model and need not be a value observed in any one trial.

### Variance

The expected squared deviation from the mean, $E[(X-E[X])^2]$, when defined. It summarizes dispersion in squared units. Its square root, the standard deviation, is expressed in the original units of the variable.

### Relative entropy

A distribution-comparison quantity $H(Q\mid P)=\sum_x Q(x)\log(Q(x)/P(x))$ for discrete measures, with appropriate support and convergence conventions. It is generally asymmetric and is not an ordinary distance.


<!-- publication:2026-10-08 -->

## 2026-10-08

Source note: [2026-10-08](articles/2026-10-08-where-names-live-environments-scope-reproducible-analysis.md).

### Environment

An R structure that associates names with objects and participates in name lookup. The compartment metaphor is helpful for learning, but environments technically hold bindings or references rather than acting as literal boxes containing all object data.

### Binding

An association between a name and an object in an environment. Assigning a value commonly establishes or changes a binding; understanding bindings helps distinguish where a name is defined from the internal contents of the object it refers to.

### Global environment

The primary workspace environment for user-created or user-assigned names in an R session. `ls()` without an environment argument typically lists names there, not every function available through attached packages.

### Local environment

The environment associated with a function call. It contains the function’s arguments and local definitions, allowing common names to be used without automatically replacing identically named objects in the caller’s workspace.

### Lexical scoping

R’s approach to resolving names by considering the environment where code is defined and the connected environments used for lookup. Local definitions take precedence in the relevant lookup context, while unresolved names may be found outside the local call.

### Namespace

A package-related structure that controls function visibility and relationships such as imports. A package’s user-visible names do not necessarily expose all its internal support functions or fully describe its namespace implementation.

### Name masking

A situation in which one available definition of a name takes precedence over another during lookup, often because of the search path or local definitions. Package-qualified calls can make the intended function explicit.

### Pearson correlation

A statistic that summarizes the direction and strength of linear association between paired numerical variables. It ranges from -1 to 1 when defined, but does not by itself establish causation or detect every nonlinear relationship.

### Least-squares line

A line of the form $\hat{y}=b_0+b_1x$ whose coefficients minimize the sum of squared residuals for the observed pairs. Its slope and intercept summarize a linear fit, but do not prove that a linear model is adequate.

### Reproducibility

The ability to inspect and rerun an analysis with its inputs, steps, and relevant context made sufficiently explicit. Avoiding hidden dependence on an interactive global workspace is one practical contribution to reproducibility.


<!-- publication:2026-10-09 -->

## 2026-10-09

Source note: [2026-10-09](articles/2026-10-09-learning-resources-reproducible-plots.md).

### Working directory

The directory a command or program uses as its base for relative file paths. Knowing it helps explain why a data path resolves in one invocation but not another, and it makes project instructions more repeatable.

### Project-local environment

An isolated Python environment created for a particular project, with its own installed packages. It reduces conflicts with other projects and makes dependencies easier to document, though it does not by itself capture every detail of a computational system.

### CSV

A text-based table format in which rows are records and fields are separated by commas. A header row can give field names. CSV is convenient for exchange, but it does not inherently preserve types, units, or all metadata needed to interpret measurements.

### Scatter plot

A graph that represents paired numeric observations as points on horizontal and vertical axes. It can help reveal association, clusters, outliers, or changing spread, but it does not alone establish causality or statistical significance.

### Histogram

A display of a numeric variable in intervals called bins, with bar heights representing counts in those intervals. Its appearance depends partly on bin choices, so binning decisions matter when interpreting a distribution.

### Finite numeric value

A number that can be represented as a finite floating-point value, excluding values such as `NaN`, positive infinity, and negative infinity. The documented utility omits rows with nonfinite values in the fields required for the chosen plot.

### Omitted row

An input CSV row that the plotting utility cannot use because a required plotted field is blank, nonnumeric, or nonfinite. The count reports what happened during plotting; it does not explain the missingness or determine whether exclusion is statistically appropriate.

### Descriptive analysis

A summary or display that characterizes observed data, such as a mean, slope, scatter plot, or histogram. Descriptive results do not automatically support conclusions about statistical significance, causation, or a wider population.

### Ordinary least-squares slope

For a simple linear fit with an intercept, the slope is the sum of cross-products of centered x and y values divided by the sum of squared centered x values. It describes a fitted linear relationship in the analyzed data and is not by itself a causal effect.
