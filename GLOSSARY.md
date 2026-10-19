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


<!-- publication:2026-10-13 -->

## 2026-10-13

Source note: [2026-10-13](articles/2026-10-13-scraped-data-to-honest-visual-evidence.md).

### Directed interaction

A relationship represented from one entity to another, such as an author quoting a second user. Direction matters: an interaction from A to B does not imply an equivalent interaction from B to A.

### Operational definition

A precise rule that turns an abstract idea into something observable or countable. Defining forum interaction as a reply or quotation creates a measurable variable, while also limiting what conclusions that variable can support.

### Heat map

A matrix-style visualization in which color encodes a value in each cell. It can reveal patterns in counts or measurements, but readers need labeled axes, a clear scale, and a definition of the encoded quantity.

### Dimensionality reduction

A family of methods that maps data with many features into fewer dimensions, often two for visualization. The reduced view necessarily simplifies the data and may distort some relationships.

### t-SNE

A nonlinear dimensionality-reduction method commonly used to visualize high-dimensional data by emphasizing local neighborhood structure. Its two-dimensional layout is exploratory; apparent global distances or clusters should not be treated as validated categories.

### Feature vector

An ordered list of numeric values representing one observation for analysis. Flattened image pixels can form a feature vector, but this representation may reflect color, alignment, and background as well as the object of interest.

### Percentile bootstrap

A resampling method that repeatedly draws observations with replacement, calculates a statistic for each resample, and uses selected quantiles of those statistics as interval endpoints. Its reliability depends on the sample and whether the resampling scheme matches the data structure.

### Sample standard deviation

A measure of spread calculated from deviations around the sample mean using $n-1$ in the denominator. It estimates variability in a sample and is undefined for a single observation.

### Nonfinite value

A parsed numeric value that is not finite, such as NaN or positive infinity. Many statistical summaries cannot meaningfully use such values, so they should be identified and handled explicitly rather than silently treated as ordinary measurements.

### Reproducible analysis

An analysis whose inputs, transformations, and settings are recorded sufficiently for another person to repeat the procedure and inspect the result. A fixed random seed helps with random procedures but cannot compensate for missing data provenance or undocumented cleaning.


<!-- publication:2026-10-14 -->

## 2026-10-14

Source note: [2026-10-14](articles/2026-10-14-reading-black-scholes-merton-call-greeks.md).

### Black–Scholes–Merton model

A model for pricing European options under assumptions that include constant volatility and a constant risk-free rate. The call formulas discussed here use no dividend yield. Its outputs depend on those assumptions and should not be treated as direct observations of market behavior.

### Greek

A partial derivative of an option's model value with respect to an input such as underlying price, time, volatility, or interest rate. Greeks describe local sensitivity while other inputs are held fixed, so they are approximations for small changes.

### Delta

The derivative of option value with respect to the underlying price. For a standard BSM European call it equals $N(d_1)$. It gives a local price sensitivity, not a constant hedge ratio across all market conditions.

### Gamma

The derivative of delta with respect to the underlying price, or equivalently the second derivative of option value with respect to that price. It indicates how quickly delta changes and can become large near expiry around the strike.

### Theta

A time sensitivity whose sign depends on convention. In the formula presented, theta measures change per year as valuation time advances with maturity fixed. Its value depends on the model inputs and is not universally negative.

### Vega

The derivative of option value with respect to volatility. When volatility is represented as a decimal, a one-percentage-point change is a change of 0.01, so the approximate price response is 0.01 times vega.

### Rho

The derivative of option value with respect to the risk-free rate. With the rate expressed as a decimal, a one-percentage-point rate change corresponds to a 0.01 change in the input and an approximate price response of 0.01 times rho.

### Time to maturity

The remaining life of an option, written $\tau=T-t$ when $t$ is valuation time and $T$ is maturity. It appears in the BSM formulas through terms such as $\sqrt{\tau}$ and must be positive for the displayed Greek formulas.

### Standard normal CDF

The function $N(x)$ gives the probability that a standard normally distributed random variable is less than or equal to $x$. It appears in the BSM call delta and in the discounted-strike component of the call formulas.

### Probability density function

The standard normal density, written $\phi(x)$, gives the density value at $x$. In the BSM call formulas it appears in gamma, theta, and vega, and is distinct from the cumulative probability $N(x)$.


<!-- publication:2026-10-15 -->

## 2026-10-15

Source note: [2026-10-15](articles/2026-10-15-editing-matrices-without-losing-track-of-their-shape.md).

### Matrix

A two-dimensional R object whose elements all have a common atomic type. A matrix is stored as a vector with a dimension attribute, which helps explain why subsetting can simplify it to a vector when one dimension has length one.

### Subsetting

Selecting parts of an object, commonly with square brackets in R. Matrix subsetting uses separate row and column indices, and the order of supplied positive indices controls the order of selected values.

### Negative index

An index value below zero that specifies a position to omit from a selection in R. Negative indices can be combined to exclude multiple positions, but they cannot be mixed with positive indices within the same indexing dimension.

### Dimension dropping

R's default simplification of some subset results when a dimension has length one. A one-column or one-row matrix may become a vector, so code that depends on matrix shape should use `drop = FALSE`.

### Column-major order

The order R uses to traverse matrix elements: it proceeds down rows within a column, then moves to the next column. Replacement values for a multi-cell matrix selection are assigned according to this traversal order.

### Recycling

The reuse of a shorter vector to fill a longer target in R. For replacement, a value may be repeated across selected cells; when the lengths do not align appropriately, warnings or unintended assignments can result.

### Replacement assignment

An operation using the assignment form of indexing, such as `M[2, ] <- values`, to change selected elements. Correctness depends on target positions, traversal order, and the length and meaning of replacement values.

### Ragged row

A CSV record with a different number of fields from the header. It may indicate malformed input or irregular source data; a profile should report it rather than quietly treating its fields as aligned with the declared columns.

### Descriptive statistic

A summary that characterizes observed data, such as a mean, minimum, or maximum. Such a summary alone does not establish statistical significance, explain a causal mechanism, or justify excluding or changing observations.


<!-- publication:2026-10-16 -->

## 2026-10-16

Source note: [2026-10-16](articles/2026-10-16-recommenders-need-more-than-a-good-guess.md).

### Recommender system

A system that selects, ranks, filters, or predicts items using information about users, items, context, or their interactions. Its goal is often to help people find useful options, not merely to reproduce a preference score.

### Explicit feedback

A deliberate user-provided signal, such as a star rating or a like. It is comparatively direct evidence of a stated judgment, but users may provide it for only a small subset of the items they encounter.

### Implicit feedback

A behavior recorded by a system, such as a click, purchase, or viewing duration. It can be plentiful, but its interpretation is ambiguous and should not automatically be treated as an explicit preference rating.

### Collaborative filtering

A family of recommendation methods that uses patterns in interactions among users and items. User-user approaches compare people’s behavior; item-item approaches compare how items are treated across users.

### Content-based recommendation

A method that uses item attributes, such as topic or genre, to identify items related to a user’s observed interests. It depends on useful item descriptions and may repeatedly surface similar kinds of content.

### Cold start

The difficulty of making useful recommendations when a new user, item, or system has little interaction history. Popularity, onboarding questions, or item attributes can help, but each relies on different assumptions.

### Root mean squared error

A prediction metric calculated as the square root of the average squared difference between predicted and observed numeric values. It summarizes error magnitude in the target's units, but does not by itself measure discovery or recommendation variety.

### Coverage

A measure of how much of a possible user or item set a recommender actually serves. A system can have accurate predictions for a narrow set of familiar items while offering limited coverage.

### Missing value

A value that is absent or unavailable in a dataset. In recommendation data, an unrecorded rating is generally not equivalent to a negative rating because the user may never have seen or evaluated the item.


<!-- publication:2026-10-19 -->

## 2026-10-19

Source note: [2026-10-19](articles/2026-10-19-homogeneous-systems-solution-sets.md).

### Homogeneous system

A linear system written $AX=0$, with a zero right-hand-side vector. It always has the trivial solution $X=0$, and it may also have nonzero solutions when the columns of $A$ are linearly dependent.

### Trivial solution

The solution $X=0$ of a homogeneous system. It exists for every coefficient matrix because multiplying any matrix by the zero vector gives the zero vector. The term distinguishes it from any nonzero solution.

### Null space

The set of all vectors $Z$ satisfying $AZ=0$. It is a subspace and describes directions in the input that produce no change in the output. For a consistent equation $AX=B$, it supplies all offsets from any particular solution.

### Particular solution

One specific vector $P_0$ satisfying an inhomogeneous equation $AP_0=B$. If the system is consistent, every solution can be written as $P_0+Z$ for some null-space vector $Z$.

### Free variable

A variable associated with a nonpivot column in a row-echelon form. It can be chosen independently, while pivot variables are determined by the equations. A free variable in a homogeneous system yields nontrivial solutions.

### Pivot column

A column containing a leading entry in row-echelon form. Its variable is a pivot variable, determined by the free variables. The number of pivot columns equals the rank of the matrix.

### Rank

The number of pivot columns in a matrix’s row-echelon form. For an $m\times n$ matrix, rank determines the number of free variables in $AX=0$ through the rank-nullity relation: $n-\operatorname{rank}(A)$.

### Consistency

A system is consistent if at least one vector satisfies all its equations. The description of an inhomogeneous solution set as a particular solution plus null-space directions applies only when the system is consistent.

### Bootstrap resampling

A computational procedure that repeatedly samples observations with replacement from the observed data. Here each resample has the original sample size, and its mean contributes to an empirical distribution of bootstrap means.

### Percentile interval

An interval formed from selected lower and upper quantiles of bootstrap estimates. It summarizes resampling variation under the chosen procedure; by itself it is not a significance test or evidence of causation.
