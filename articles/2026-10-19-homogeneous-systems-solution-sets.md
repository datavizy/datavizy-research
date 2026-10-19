# Homogeneous Systems: The Shape of Every Linear Solution

A lab instrument records three measurements, but the model says only how changes in three hidden parameters combine to produce the readings. If the readings are all zero, which parameter combinations remain possible? And if the readings are not zero, how can we describe every parameter combination that works, rather than just one? Homogeneous linear systems give a compact answer to both questions.

A homogeneous system has the form $AX=0$, where $A$ is a matrix, $X$ is an unknown column vector, and the right-hand side is the zero vector. The key idea is that its solutions describe the directions in which a model can change without changing its output. Those directions also reveal the full solution set of any consistent system with the same coefficient matrix.

## The zero solution is only the beginning

For any matrix $A$, setting $X=0$ gives $A0=0$. This is the trivial solution, and it exists whether or not there are other solutions. Nonzero solutions are called nontrivial. They exist precisely when the columns of $A$ have a nonzero linear combination that sums to zero, or equivalently, when the equation $AX=0$ has a free variable.

A small example makes the relationship concrete. Consider

$$
A=\begin{bmatrix}1&2&-1\\2&4&-2\end{bmatrix},
\qquad
X=\begin{bmatrix}x\\y\\z\end{bmatrix}.
$$

The system is $x+2y-z=0$ and $2x+4y-2z=0$. The second equation is twice the first, so row reduction leaves just $x+2y-z=0$. Let $y=s$ and $z=t$ be free variables. Then $x=-2s+t$, giving

$$
X=s\begin{bmatrix}-2\\1\\0\end{bmatrix}+t\begin{bmatrix}1\\0\\1\end{bmatrix}.
$$

This expression checks directly: multiplying the first direction by $A$ gives $(-2+2, -4+4)^T=(0,0)^T$, and multiplying the second gives $(1-1, 2-2)^T=(0,0)^T$. Therefore every displayed combination is a solution. Row reduction also shows that every solution has this form. The zero solution occurs at $s=t=0$; all other parameter choices give nontrivial solutions.

There are infinitely many solutions here because two parameters can vary freely over the real numbers. More generally, a homogeneous system has either just the trivial solution or infinitely many solutions. A free variable can take infinitely many values, and each choice determines the pivot variables. There is no possibility of exactly two or seven solutions over the real numbers.

## One particular solution plus all the zero directions

Now change the right-hand side, keeping the coefficient matrix fixed:

$$
AX=B,\qquad B=\begin{bmatrix}3\\6\end{bmatrix}.
$$

A particular solution is $P_0=(3,0,0)^T$, since $AP_0=(3,6)^T$. The homogeneous solutions from the example can be added to it:

$$
X=P_0+s\begin{bmatrix}-2\\1\\0\end{bmatrix}+t\begin{bmatrix}1\\0\\1\end{bmatrix}.
$$

For an arithmetic check, take $s=1$ and $t=2$. This gives $X=(1,1,2)^T$. Then $AX=(1+2-2, 2+4-4)^T=(1,2)^T$, which is not $B$. That signals an error: the proposed particular solution and the directions must be checked against the original matrix. In fact, for the stated $A$, $AP_0=(3,6)^T$ is correct, but the first component of $A(1,1,2)^T$ is $1+2-2=1$, not $3$. The direction vectors are valid; the mistake is in adding their coordinates. Let us redo that sum before relying on the conclusion.

The equation in this example is $x+2y-z=0$, and its coefficient matrix must be $\begin{bmatrix}1&2&-1\\2&4&-2\end{bmatrix}$. Its null directions are $(-2,1,0)^T$ and $(1,0,1)^T$, as checked above. But $P_0=(3,0,0)^T$ gives $AP_0=(3,6)^T$, and adding the directions preserves that output. For $s=1,t=2$, the sum is $(3,0,0)+(-2,1,0)+2(1,0,1)=(3,1,2)^T$, not $(1,1,2)^T$. Multiplication gives $(3+2-2,6+4-4)^T=(3,6)^T$. The corrected arithmetic confirms the formula.

This deliberately careful check illustrates a useful habit: verify a candidate solution by multiplying it through, especially when translating free-variable formulas into vectors. The general result is simple. If $AP_0=B$ and $AZ=0$, then

$$
A(P_0+Z)=AP_0+AZ=B+0=B.
$$
So every homogeneous solution gives a valid direction from a particular solution to another solution of $AX=B$.

The converse matters just as much. If $Q$ is any other solution of $AX=B$, then

$$
A(Q-P_0)=AQ-AP_0=B-B=0.
$$
Thus $Q-P_0$ solves the homogeneous system, and $Q=P_0+(Q-P_0)$. Taken together, these statements show that the complete solution set of a consistent inhomogeneous system is one particular solution plus the complete solution set of its associated homogeneous system. In compact notation, it is $P_0+\operatorname{Null}(A)$.

The excerpt’s theorem states this relationship for any chosen particular solution. It also draws an important consequence: if $AX=B$ is consistent, it has the same number of solutions as $AX=0$. If the homogeneous system has only the zero solution, the particular solution is unique. If the homogeneous system has nontrivial solutions, adding them produces infinitely many solutions to the consistent inhomogeneous system.

## Reading free variables from row reduction

Row reduction is the practical route from equations to solution directions. In reduced row-echelon form, a pivot column identifies a leading variable. A nonpivot column is free, meaning its variable can be chosen independently. For a homogeneous system, assign values to the free variables and solve the pivot equations. Setting one free variable to $1$ and the others to $0$ produces a direction vector; repeating this for each free variable gives a spanning set for all solutions.

For an $m\times n$ matrix with rank $r$, there are $n-r$ free variables. This is the rank-nullity count: the null space has dimension $n-r$. In particular, if $r=n$, there are no free variables and the zero solution is unique. If $n>r$, at least one variable is free and there are infinitely many solutions. These statements assume ordinary linear algebra over the real numbers. Over a finite field, a free variable still gives multiple solutions, but not infinitely many.

A subtle point in the selected passage concerns overdetermined and underdetermined systems. Counting rows and columns of the original matrix can mislead when equations are redundant. The passage defines the terms using the reduced form: more columns than rows means underdetermined, while more rows than columns means overdetermined. For a homogeneous system in the underdetermined case, a free variable ensures infinitely many solutions. In the overdetermined case, the reduced form has a zero row; changing the corresponding right-hand-side entry to a nonzero value creates an inconsistent system. This does not say that every inhomogeneous right-hand side is inconsistent. It says that at least one choice is.

## Where the idea earns its keep

In a physical model, $AX=0$ can describe parameter changes that leave measured outputs unchanged. In a network, it can encode flows that balance at every node. In data analysis, null directions expose combinations of predictors that cannot be distinguished by the observed design matrix. In inverse problems, they explain why different underlying states may generate identical measurements.

That last use is especially practical. If two parameter settings produce the same observations, their difference lies in the null space of the measurement matrix. A model with a large null space cannot uniquely recover every parameter from those observations without additional assumptions or data. Row reduction can reveal exact non-identifiability in an idealized linear model; real measurements may add noise and numerical conditioning concerns, which require methods beyond exact symbolic reasoning.

## Assumptions and common traps

The solution-set argument uses linearity: matrix multiplication distributes over addition and respects subtraction. It does not require $A$ to be square or invertible. It does require consistency before claiming that an inhomogeneous solution set is a particular solution plus null-space directions. If no particular solution exists, there is no solution set to describe that way.

Do not confuse the trivial solution with the only solution. Every homogeneous system has the trivial solution, but free variables can produce many others. Do not assume that a nonzero right-hand side guarantees a unique solution; uniqueness depends on the null space, not on whether $B$ is zero. Finally, distinguish exact algebra from numerical computation. Rounded measurements and floating-point row reduction can make a nearly zero pivot look nonzero or the reverse. In applied work, report tolerances and inspect conditioning rather than treating every computed zero as exact.

## Try it yourself

1. If $A$ is a $4\times 6$ matrix with rank $4$, how many free variables does $AX=0$ have? **Answer:** $6-4=2$, so the homogeneous system has infinitely many real solutions.
2. Suppose $AX=B$ has one solution and $AZ=0$ for a nonzero vector $Z$. Name two distinct solutions to $AX=B$. **Answer:** $P_0$ and $P_0+Z$. They are distinct because $Z\ne0$.
3. A homogeneous system has a unique solution. What can you say about a consistent system with the same coefficient matrix? **Answer:** Its solution is unique, because the only available null-space direction is zero.

The central lesson is a useful way to think about linear systems: a particular solution sets the location, while the homogeneous system supplies every direction in which that solution can move without changing the output. Once those directions are known, the entire family of solutions is visible.

---

Published 2026-10-19.

**Source:** Interactive Linear Algebra -- Conrad Plaut; -- 2026 -- CRC Press, section **2.5 Homogeneous Systems**.

This is an original explanatory note; the source book is not redistributed.
