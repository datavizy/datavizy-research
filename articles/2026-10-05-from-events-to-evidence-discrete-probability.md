# From Events to Evidence: A Practical Primer on Discrete Probability

A sensor records the number of alerts received each hour. Most hours have none, a few have one or two, and an occasional hour has more. Before making a plot or fitting a model, ask a simpler question: what exactly counts as an outcome, and how does probability attach to it? Clear answers are the foundation for interpreting data without confusing a convenient summary with stronger evidence than the data support.

This primer develops the discrete probability ideas in the selected passage: probability spaces, events, conditional probability, independence, random variables, distributions, expectation, and variance. It also introduces the passage's relative entropy formula and shows how to calculate it in a small example. The formulas here concern discrete outcomes. Continuous probability requires different definitions in important places, so the distinctions matter.

## Start with outcomes and events

A discrete probability model begins with a finite or countably infinite set $\Omega$ of possible outcomes. A probability measure $P$ assigns probabilities to events, which are subsets of $\Omega$. It obeys three basic rules:

1. $P(\varnothing)=0$.
2. $P(\Omega)=1$.
3. For any countable collection of pairwise disjoint events $A_1,A_2,\ldots$,

$$
P\left(\bigcup_{n=1}^{\infty} A_n\right)=\sum_{n=1}^{\infty}P(A_n).
$$

The third rule is countable additivity. Disjoint means that no outcome belongs to two of the events at once. It lets us add probabilities when we split an event into non-overlapping cases. It also implies countable subadditivity: for events that may overlap, the probability of their union is no greater than the sum of their probabilities.

For a discrete space, the probabilities of individual outcomes determine the measure. If $p(\omega)=P(\{\omega\})$, then for an event $A$,

$$
P(A)=\sum_{\omega\in A}p(\omega).
$$

This is useful in computation: a program may store a probability table rather than every possible event. But the table must describe a valid distribution: its entries are nonnegative and sum to one.

## Conditioning narrows the question

Suppose a quality-control check flags an item, and we want the probability that it came from a particular production line given that it was flagged. Conditional probability formalizes the change in question. For events $C$ and $D$ with $P(C)>0$,

$$
P(D\mid C)=\frac{P(C\cap D)}{P(C)}.
$$

The condition $P(C)>0$ is essential: division by zero is not defined. Once $C$ is known to have occurred, probabilities are recalculated within that restricted event. In particular, $P(\cdot\mid C)$ is itself a probability measure.

Two events are independent when learning that one occurred does not change the probability of the other. The defining equation is

$$
P(C\cap D)=P(C)P(D).
$$

When $P(C)>0$, this is equivalent to $P(D\mid C)=P(D)$. If either event has probability zero, the product equation still defines independence, even though the corresponding conditional probability may not exist. For a collection of events, mutual independence requires the product rule for every finite subcollection, not just for pairs. Pairwise independence alone does not generally establish mutual independence.

## A random variable turns outcomes into numbers

A random variable is a function $X:\Omega\to\mathbb{R}$. It assigns a number to each outcome, such as the number of alerts in an hour. For a set $B$ of real values, the event $\{X\in B\}$ contains all outcomes mapped into $B$. The distribution of $X$ records the probabilities of these values; in the discrete case, its probability function is

$$
p_X(x)=P(X=x).
$$

The distribution is a probability measure induced by the original model. This viewpoint separates the details of how an outcome was generated from the numerical quantity being analyzed. Two different experiments can produce random variables with the same distribution.

For a discrete random variable, expectation is a probability-weighted average:

$$
E[X]=\sum_x xP(X=x),
$$

provided $\sum_x |x|P(X=x)<\infty$. The absolute-value condition ensures the sum is well behaved, including when $X$ can be negative. The same idea calculates the expectation of a function $g(X)$:

$$
E[g(X)]=\sum_x g(x)P(X=x),
$$
when $\sum_x |g(x)|P(X=x)<\infty$. The $k$th moment is $E[X^k]$ when the corresponding absolute moment is finite. Expectation is not necessarily a value the variable can actually take; it is a long-run weighted average under the model.

Variance measures spread around the mean $\mu=E[X]$:

$$
\operatorname{Var}(X)=E[(X-\mu)^2].
$$

When the second moment exists, this is equivalent to $E[X^2]-(E[X])^2$. Variance is measured in squared units, while the standard deviation, its square root, returns to the variable's units. A finite mean does not by itself guarantee finite variance.

## Worked example: hourly alerts

Consider a simplified model in which the alert count $X$ in an hour is 0, 1, or 2, with probabilities

$$
P(X=0)=0.5,\qquad P(X=1)=0.3,\qquad P(X=2)=0.2.
$$

First check the distribution: $0.5+0.3+0.2=1$, and all entries are nonnegative. The probability of at least one alert is

$$
P(X\geq 1)=P(X=1)+P(X=2)=0.3+0.2=0.5.
$$

The expected count is

$$
E[X]=0(0.5)+1(0.3)+2(0.2)=0+0.3+0.4=0.7.
$$

The mean of 0.7 alerts per hour is a weighted average, not a claim that an individual hour contains 0.7 alerts. For the variance, calculate the second moment:

$$
E[X^2]=0^2(0.5)+1^2(0.3)+2^2(0.2)=0+0.3+0.8=1.1.
$$

Therefore,

$$
\operatorname{Var}(X)=1.1-(0.7)^2=1.1-0.49=0.61.
$$

The standard deviation is $\sqrt{0.61}\approx0.781$ alerts. These calculations describe the stipulated probability model. They do not establish that real alert counts follow it; that would require data and model assessment.

## Relative entropy compares distributions

The selected passage also defines relative entropy for two measures on a finite or countably infinite set. With $P$ as the reference measure and $Q$ as the measure being compared, its expression is

$$
H(Q\mid P)=\sum_{x}Q(x)\log\frac{Q(x)}{P(x)}.
$$

This is also commonly called Kullback-Leibler divergence. It is not a distance in the usual geometric sense: it is generally asymmetric. Terms with $Q(x)=0$ contribute zero by the convention $0\log(0/P(x))=0$. If $Q(x)>0$ where $P(x)=0$, the divergence is infinite. Otherwise, the sum is well-defined as a nonnegative extended value, though it may be infinite.

For a concrete binary calculation, let $Q(1)=0.7$, $Q(0)=0.3$, and let $P(1)=P(0)=0.5$. Using natural logarithms,

$$
H(Q\mid P)=0.7\log(0.7/0.5)+0.3\log(0.3/0.5)
$$

$$
=0.7\log(1.4)+0.3\log(0.6)\approx0.7(0.33647)+0.3(-0.51083)\approx0.08228.
$$

The result is nonnegative, despite one negative summand. Relative entropy appears in large-deviation theory, as the passage notes, and more broadly helps compare probability models. It does not by itself say which model is scientifically correct or establish a causal explanation.

## Where these ideas help, and where care is needed

These definitions support practical work: probability tables validate simulation code; conditional probabilities clarify diagnostic or quality-control questions; distributions and expectations summarize outcomes; and relative entropy quantifies a particular kind of discrepancy between models. In a reproducible analysis, record the outcome definition, sampling unit, assumptions, and treatment of missing values alongside the calculations.

A common pitfall is to treat observed frequencies as exact probabilities. Frequencies are data summaries; probabilities belong to a model, and inference from one to the other involves uncertainty. Another is to confuse independence with mutually exclusive events. If two events are both possible and mutually exclusive, they cannot be independent: their intersection has probability zero, while the product of their positive probabilities is positive. Finally, discrete sums should not be casually carried over to continuous variables. Continuous distributions typically assign zero probability to individual points and use densities and integrals instead.

## Exercises with short answers

1. A discrete variable takes values 0 and 1 with probabilities 0.8 and 0.2. What is its expectation? **Answer:** $0(0.8)+1(0.2)=0.2$.
2. If $P(C)=0.4$ and $P(C\cap D)=0.1$, what is $P(D\mid C)$? **Answer:** $0.1/0.4=0.25$.
3. Can two events with probabilities 0.3 and 0.4 be both independent and mutually exclusive? **Answer:** No. Independence gives intersection probability $0.3(0.4)=0.12$, whereas mutual exclusivity gives zero.
4. In the binary relative-entropy example, why is the 0.3-weighted term negative? **Answer:** Because $0.3/0.5<1$, so its logarithm is negative. The total divergence remains positive.

Discrete probability gives data analysis a precise language for outcomes, uncertainty, and summaries. The language is simple enough to compute with, but strong enough to expose hidden assumptions. That is a useful combination whenever a plot, statistic, or model is meant to say something trustworthy about the world.

---

Published 2026-10-05.

**Source:** Problems from the Discrete to the Continuous_ Probability, Number Theory, Graph Theory, and Combinatorics, section **1 0 < , limn!1**.

This is an original explanatory note; the source book is not redistributed.
