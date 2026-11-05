# A Collatz Map, a Matrix, and the Shape of a Graph

Start with the number 5. Apply the familiar rule: if the number is even, halve it; if it is odd, triple it and add one. The resulting trail is $5\to16\to8\to4\to2\to1\to4\to\cdots$. A tiny arithmetic rule has produced a path that eventually joins a loop. Now imagine recording the same rule in a matrix, then asking what its eigenvalues and Jordan blocks say about the paths. That is the inviting bridge between the Collatz problem and linear algebra.

The project in *Exploring Linear Algebra: Labs and Projects* asks readers to build finite Collatz matrices, investigate their characteristic equations, and explore zero-eigenvalue Jordan blocks and cycle lengths. This article develops those questions from the graph structure. It uses the standard Collatz rule, $f(k)=3k+1$ for odd $k$ and $f(k)=k/2$ for even $k$. The excerpt’s mathematical typesetting has lost fraction and case formatting; this is the conventional interpretation of its displayed rule.

## From an arithmetic rule to a finite graph

For a chosen positive integer $n$, take the vertices to be $1,2,\ldots,n$. The matrix $A_n$ is defined by

$$
(A_n)_{ij}=\begin{cases}1,&i=f(j),\\0,&\text{otherwise.}\end{cases}
$$

Column $j$ describes the step out of vertex $j$: if $f(j)$ is still among the vertices, that column has a 1 in row $f(j)$. If $f(j)>n$, there is no corresponding row, so the column is all zero. This finite graph is therefore a truncated view of the infinite Collatz trajectory, not a claim that numbers above $n$ cease to exist.

For example, with $n=5$, the transitions are $1\to4$, $2\to1$, $3\to10$, $4\to2$, and $5\to16$. Only the first, second, and fourth transitions stay inside the graph. In the vertex order $1,2,3,4,5$,

$$
A_5=\begin{pmatrix}
0&1&0&0&0\\
0&0&0&1&0\\
0&0&0&0&0\\
1&0&0&0&0\\
0&0&0&0&0
\end{pmatrix}.
$$

The nonzero columns encode $1\to4$, $2\to1$, and $4\to2$. The two zero columns record exits from the finite graph. The vertices $1,4,2$ form a directed cycle; the remaining vertices leave the graph in one step.

## Why cycles control the characteristic polynomial

A finite directed graph in which each vertex has at most one outgoing edge is called a functional graph, allowing for vertices whose edges leave the chosen set. Following edges from any vertex either exits the graph or eventually repeats a vertex. Once a vertex repeats, the repeated portion is a directed cycle. The other vertices form paths feeding into cycles or into exits.

This structure gives a useful general formula. Suppose the cycles entirely contained in the graph have lengths $\ell_1,\ldots,\ell_c$. Then

$$
\det(xI-A_n)=x^{n-(\ell_1+\cdots+\ell_c)}\prod_{r=1}^{c}(x^{\ell_r}-1).
$$

Here the product is 1 if there are no cycles. To see why, reorder vertices so that cycle vertices come first and arrange the remaining vertices by their distance from a cycle or exit. The matrix becomes block triangular. Each cycle block is a cyclic permutation matrix, whose characteristic polynomial is $x^{\ell}-1$. The off-cycle part is nilpotent: repeated multiplication advances along a finite path and eventually reaches a cycle or an exit. Its diagonal contribution to the characteristic polynomial is a power of $x$. The exponent counts vertices not on the cycles.

For $A_5$, the only cycle has length 3. Thus

$$
\det(xI-A_5)=x^{5-3}(x^3-1)=x^5-x^2.
$$

The arithmetic checks against the matrix structure: there are three cycle vertices, two other vertices, and the cycle contributes the eigenvalues satisfying $x^3=1$. Under the alternative convention $\det(A_n-xI)$, an overall sign may differ for odd dimension. State the convention when comparing characteristic polynomials.

This formula is a general graph fact, not a special theorem about the Collatz conjecture. The project’s invitation to investigate characteristic equations can be answered by looking first for cycles and counting vertices. In particular, the characteristic polynomial does not reveal every detail of the incoming paths.

## Zero eigenvalues and Jordan blocks

The exponent of $x$ in the formula is the algebraic multiplicity of the eigenvalue zero. It is not automatically the number of Jordan blocks for zero. The number of such blocks is instead the dimension of the nullspace, $\dim\ker(A_n)$.

For a matrix whose columns are either zero or standard basis vectors, the rank is the number of distinct in-range images $f(j)$. Indeed, each nonzero column is the basis vector $e_{f(j)}$, and distinct basis vectors are linearly independent. Therefore

$$
\text{number of zero-eigenvalue Jordan blocks}=n-\#\{f(j):1\le j\le n,\ f(j)\le n\}.
$$

For $A_5$, the distinct in-range images are $4,1,2$. The rank is 3, so the nullity is $5-3=2$. The characteristic polynomial has zero with algebraic multiplicity 2, and there are two zero-eigenvalue Jordan blocks. In this example both blocks have size 1. In general, the nullity counts blocks, while the algebraic multiplicity counts their combined sizes. Confusing those two counts is an easy way to make an incorrect Jordan-block conjecture.

A useful computational strategy is to record two quantities separately for each $n$: the number of vertices not on cycles, which gives the zero algebraic multiplicity, and the number of distinct in-range images, which gives the rank. Their difference from $n$ gives the number of zero blocks directly.

## What cycle-length conjecture is reasonable?

Under the standard Collatz rule, $1\to4\to2\to1$ is a cycle of length 3. Since all three vertices are included once $n\ge4$, every such finite graph has a cycle of length at least 3. The familiar Collatz conjecture says every positive starting integer eventually reaches 1. If that conjecture is true, there are no other positive-integer cycles, so the maximum cycle length in every $G_n$ for $n\ge4$ is 3. For $n=2$ or $n=3$, the displayed cycle is not wholly present, and there is no cycle in the truncated graph.

That is a conjectural conclusion about the Collatz dynamics, not a result established by checking a finite range. A computer can verify that no longer cycle appears among vertices up to a chosen bound. It cannot prove that no cycle exists at larger integers. When exploring, distinguish “largest cycle observed up to $n$” from “maximum cycle length for all positive integers.” The first is a reproducible computational result; the second needs a proof or a conjecture.

## A reproducible exploration workflow

A good small project can combine a mathematical argument with an auditable computation:

1. Specify the map, vertex set, and matrix convention. Record what happens when $f(j)>n$.
2. Generate edges by evaluating $f(j)$ for every vertex. Find cycles by following each edge until a vertex repeats or the path exits.
3. Compute the characteristic polynomial for modest $n$, then compare it with the cycle formula. A mismatch often signals a row-versus-column convention error or a missing edge.
4. Calculate the rank from distinct in-range images and compare $n-\operatorname{rank}(A_n)$ with a direct nullspace calculation.
5. Save the tested range, software version, code, and results. This makes a computational observation reproducible without dressing it up as a proof.

The finite matrices are sparse: each column has at most one nonzero entry. That makes graph-based calculation more economical than dense matrix algebra for large $n$. It also makes the assumptions visible. This is a deterministic map; there is no sampling uncertainty in the generated edges. The uncertainty lies in extrapolating finite evidence to an infinite claim.

## Where this way of thinking is useful

The same matrix-and-graph perspective appears in state-transition systems, deterministic simulations, and discrete-time models. A transition matrix can encode which state follows a given state, while cycles describe recurring behavior and transient paths describe states that feed into it. In data work, this structure can help validate a transition table, identify recurrent states, and separate cycles from one-way exits.

The Collatz example is especially useful because it makes a limitation hard to miss: a finite visualization can be compelling and still leave an infinite conjecture unresolved. Reproducible computation helps establish exactly what was checked. Graph structure then turns those checks into interpretable quantities, such as cycle lengths, ranks, and nullities.

## Exercises with short answers

**1. For $n=4$, what are the in-range edges and characteristic polynomial?**

The edges are $1\to4$, $2\to1$, and $4\to2$; vertex 3 exits to 10. The length-3 cycle uses three of four vertices, so $\det(xI-A_4)=x(x^3-1)$.

**2. Why does the zero algebraic multiplicity not always equal the number of zero Jordan blocks?**

Algebraic multiplicity is the sum of the sizes of all zero-eigenvalue Jordan blocks. The number of blocks is $\dim\ker(A_n)$, so a block larger than 1 makes the two counts differ.

**3. Does finding no cycle longer than 3 for $n\le10{,}000$ prove the maximum is 3?**

No. It verifies a finite range only. The claim for all positive integers depends on resolving the Collatz conjecture, not on a bounded search.

---

Published 2026-11-05.

**Source:** Exploring Linear Algebra_ Labs and Projects with -- Arangala, Crista -- 2024 -- CRC Press, section **34 Project 3: The Collatz Problem**.

This is an original explanatory note; the source book is not redistributed.
