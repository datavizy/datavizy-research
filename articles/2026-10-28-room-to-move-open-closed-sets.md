# Room to Move: Open and Closed Sets on the Real Line

Suppose a sensor reading is exactly at the upper limit of an accepted range. Is it still inside the range? The answer depends on whether that endpoint is included. But there is a deeper question: if we nudge the reading slightly, do we stay inside? That small-neighborhood idea leads to open sets, closed sets, and a useful way to reason about limits and statistical results.

This article develops the real-line ideas in the selected section of *Mathematics of Machine Learning*. It focuses on how the definitions work, why unions and intersections behave differently, and what nested intervals tell us about the completeness of the real numbers. These are mathematical foundations, not recipes for making statistical conclusions.

## Openness means every point has breathing room

A subset $A$ of the real numbers is open when, for every $x \in A$, some positive radius $\varepsilon$ has the property

$$
(x-\varepsilon, x+\varepsilon) \subseteq A.
$$

In plain language, around each point in the set, we can draw a small open interval that stays entirely inside it. The radius may depend on the point. Openness does not require one universal radius to work everywhere.

Take $A=(2,8)$. At the point $x=3$, a radius of $0.5$ works, since $(2.5,3.5)$ remains inside $(2,8)$. At $x=7.9$, that radius is too large, but $\varepsilon=0.05$ works. Points close to an endpoint have less room, yet every point strictly between the endpoints still has some room.

More generally, if $a<x<b$, choose

$$
\varepsilon=\frac{1}{2}\min(x-a,b-x).
$$

The nearest endpoint is at distance $\min(x-a,b-x)$. Halving that distance ensures the neighborhood cannot reach either endpoint. This verifies that every interval $(a,b)$ is open.

The empty set is open too: there are no points in it for which the condition could fail. This is a useful reminder that mathematical definitions apply literally, even when the everyday picture feels less intuitive.

## Closedness is about the complement

A set $A$ is closed if its complement in $\mathbb{R}$, written $\mathbb{R}\setminus A$, is open. For $[2,8]$, the complement is $(-\infty,2)\cup(8,\infty)$. Each point in that complement has a small neighborhood that stays outside $[2,8]$, so the complement is open and $[2,8]$ is closed.

This definition is not the same as saying that a set contains all of its boundary points, although that is a helpful equivalent characterization in the usual real-line setting. The complement definition makes the logic especially clear: being closed is a property of everything outside the set.

A set can be open, closed, both, or neither. For example, $(2,8]$ is neither. It is not open because no neighborhood around 8 stays inside the set. It is not closed because its complement, $(-\infty,2]\cup(8,\infty)$, is not open: no neighborhood around 2 stays in that complement. By contrast, $\mathbb{R}$ and the empty set are both open and closed. These examples prevent a common false assumption: “not open” does not mean “closed,” and “not closed” does not mean “open.”

## Arbitrary unions and finite intersections

The selected section's union and intersection result has an important asymmetry. Any union of open sets is open, including a union over an infinite or uncountable index collection. If a point belongs to the union, it belongs to at least one of the open sets. That set supplies a neighborhood around the point, and the neighborhood also belongs to the union.

For example, the union of the intervals $(-1,1)$ and $(0,4)$ is $(-1,4)$, which is open. The proof does not depend on there being just two intervals. It depends on choosing one set that contains the point under consideration.

For closed sets, any intersection is closed. The reason follows from De Morgan's law:

$$
\mathbb{R}\setminus\bigcap_{\gamma\in\Gamma}A_\gamma
=\bigcup_{\gamma\in\Gamma}(\mathbb{R}\setminus A_\gamma).
$$

If every $A_\gamma$ is closed, each complement is open. Their union is open, so the complement of the intersection is open. By definition, the intersection is closed.

Do not reverse these claims without checking the size of the collection. A finite intersection of open sets is open: around a point shared by finitely many sets, choose the smallest of their available positive radii. But an infinite intersection can fail to be open. For instance, $(-1/n,1/n)$ is open for every positive integer $n$, while their intersection is just $\{0\}$, which is not open. Similarly, a finite union of closed sets is closed, but an infinite union need not be. The sets $[1/n,1]$ are closed, and their union is $(0,1]$, which is not closed in $\mathbb{R}$.

## Nested intervals and a completeness guarantee

The section also states Cantor's axiom: a nested sequence of closed intervals has nonempty intersection. More precisely, if $I_n=[a_n,b_n]$ and $I_{n+1}\subseteq I_n$ for every $n$, with each interval nonempty, then

$$
\bigcap_{n=1}^{\infty} I_n\ne\varnothing.
$$

Here is a short derivation for bounded nested intervals. Their left endpoints never decrease, and every left endpoint is at most every later right endpoint. The set of left endpoints is bounded above, so the completeness of $\mathbb{R}$ gives its least upper bound $x=\sup_n a_n$. For any fixed $k$, all left endpoints are at most $b_k$: earlier intervals contain $I_k$, and later intervals are contained in $I_k$. Therefore $x\le b_k$. Also $a_k\le x$. So $x\in[a_k,b_k]$ for every $k$, which places $x$ in the intersection.

The assumption that the intervals are nonempty matters. If empty intervals were allowed, the conclusion would fail immediately. Boundedness also matters for the stated least-upper-bound argument; an unbounded nested sequence such as $[n,\infty)$ has empty intersection. The result expresses a special completeness property of the real line, not a rule that every nested family in every number system must have a common point.

For contrast, rational endpoints can form nested brackets that narrow toward $\pi$. Their intersection as subsets of $\mathbb{R}$ can be $\{\pi\}$, but that intersection contains no rational number. Thus the corresponding intervals, viewed as subsets of $\mathbb{Q}$, have empty intersection. The gap is not in the brackets; it is in the number system. The rationals do not contain every limit that their sequences can approach.

## Why this matters in statistics and computing

Open and closed sets help make precise what happens when values approach a boundary. In optimization, a sequence of parameter estimates may converge to a limit, and whether the allowed parameter region includes that limit matters. In numerical analysis, a method may generate increasingly narrow intervals intended to enclose a solution. Nested closed intervals provide a mathematical guarantee of at least one common real value under the stated conditions.

Statistical reporting has related boundary questions. A confidence interval may be displayed with endpoints, while software computes approximate quantities from finite data and finite resampling. It is important not to confuse the mathematical properties of a set with the evidential meaning of a statistical interval. A bootstrap interval is a computational summary under a resampling procedure; its endpoints do not, by themselves, establish causation or significance.

A reproducible workflow should therefore record the data handling, algorithm, settings, and output. A small command-line summary can make those details visible, but it cannot repair a sampling design that does not match the research question. Mathematics clarifies what a procedure guarantees, and careful engineering clarifies what the program actually did.

## Pitfalls worth checking

First, test neighborhoods at boundary points. A point at an included endpoint of a closed interval still has no two-sided neighborhood contained in that interval, so the interval is not open. Second, track the collection size: arbitrary unions preserve openness, but only finite intersections are guaranteed to preserve it. Third, state the ambient space. A complement depends on whether we mean the real line or, for example, the rationals. Finally, distinguish an approximation from a proof: many numerical values can get close to a limit without the limit belonging to the space being studied.

## Exercises with short answers

1. Is $(-3,5]$ open, closed, both, or neither in $\mathbb{R}$? **Answer:** Neither. The included endpoint 5 prevents openness, and the missing endpoint $-3$ prevents closedness.

2. Is the intersection of any collection of closed sets closed? **Answer:** Yes. Its complement is a union of open complements, which is open.

3. Must a nested sequence of nonempty closed intervals have a common point in $\mathbb{R}$? **Answer:** Yes, provided the intervals are nested as specified. For bounded intervals, the supremum argument above proves it.

The central habit is simple: when a statement concerns openness, ask what neighborhood fits around each point. When it concerns closedness, inspect the complement. Those two checks turn endpoint intuition into reasoning that scales from one interval to whole collections of sets.

---

Published 2026-10-28.

**Source:** Mathematics of Machine Learning_ Master linear algebra, -- Tivadar Danka -- 2025, section **11.1.1 Open and closed sets**.

This is an original explanatory note; the source book is not redistributed.
