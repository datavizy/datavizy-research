# Eigenvectors: The Directions a Matrix Leaves Recognizable

Imagine rotating a map of measurements, stretching it, and shearing it. Most arrows drawn on the map come out pointing somewhere new. A few special arrows keep their direction: the transformation only lengthens, shortens, or reverses them. Those arrows are eigenvectors, and the amount of scaling is the corresponding eigenvalue. This simple geometric idea opens a useful route into principal component analysis, noise filtering, and data compression.

## One transformation, special directions

For a square matrix $A$, a nonzero vector $v$ is an eigenvector when

$$
Av = \lambda v,
$$

where $\lambda$ is its eigenvalue. The matrix does not equal the scalar. Rather, applying the matrix to this particular vector has the same result as scaling the vector by $\lambda$.

If $\lambda > 1$, the vector grows in length; if $0 < \lambda < 1$, it shrinks. A negative eigenvalue reverses its direction as well as scaling it. An eigenvalue of zero maps its eigenvector to the zero vector. The eigenvector itself must be nonzero, or the equation would be true for every matrix and tell us nothing.

This is a directional description of a linear transformation. A general vector can be rotated or sheared into a different direction. Eigenvectors identify directions on which the transformation acts especially simply. Not every matrix has enough real eigenvectors to form a basis, and some matrices have complex eigenvalues. For principal component analysis, however, the central matrix is a real symmetric covariance matrix. Such a matrix has real eigenvalues and an orthonormal basis of eigenvectors, which makes the geometric story particularly clean.

## Why the determinant finds the eigenvalues

Rearrange the eigenvalue equation:

$$
Av - \lambda v = 0
$$

and write the vector term as a matrix product:

$$
(A - \lambda I)v = 0,
$$

where $I$ is the identity matrix of the same size as $A$. The expression $\lambda I$ is a matrix, so this subtraction is defined. The equation says that $v$ belongs to the null space of $A - \lambda I$.

Because $v$ must be nonzero, the shifted matrix must have a nontrivial null space. A square matrix has a nontrivial null space precisely when it is singular, and a square matrix is singular precisely when its determinant is zero. Thus eigenvalues satisfy the characteristic equation

$$
\det(A - \lambda I) = 0.
$$

This equation is a polynomial in $\lambda$. Its roots give the eigenvalues; solving $(A-\lambda I)v=0$ for each root gives the associated eigenvectors. For large matrices, numerical libraries use stable computational methods rather than expanding and solving the polynomial by hand. The determinant equation is valuable for understanding why eigenvalues exist, but it is not usually the best numerical algorithm.

## A worked example, with every step checked

Consider the symmetric matrix

$$
A = \begin{bmatrix}4 & 2 \\ 2 & 4\end{bmatrix}.
$$

Its characteristic equation is

$$
\det(A-\lambda I)
= \det\begin{bmatrix}4-\lambda & 2 \\ 2 & 4-\lambda\end{bmatrix}
= (4-\lambda)^2 - 4 = 0.
$$

Factoring gives $(2-\lambda)(6-\lambda)=0$, so the eigenvalues are $2$ and $6$. Check the arithmetic by expanding: $(4-\lambda)^2-4 = \lambda^2-8\lambda+12 = (\lambda-2)(\lambda-6)$.

For $\lambda=6$, subtract $6I$:

$$
A-6I = \begin{bmatrix}-2 & 2 \\ 2 & -2\end{bmatrix}.
$$

The equation $(A-6I)v=0$ requires the two coordinates of $v$ to be equal. Choose $v_1=(1,1)^T$. Direct multiplication checks the result:

$$
Av_1 = (6,6)^T = 6v_1.
$$

For $\lambda=2$, the shifted matrix is

$$
A-2I = \begin{bmatrix}2 & 2 \\ 2 & 2\end{bmatrix},
$$

so the coordinates must sum to zero. Choose $v_2=(1,-1)^T$. Then

$$
Av_2 = (2,-2)^T = 2v_2.
$$

The two directions are perpendicular: $(1,1)\cdot(1,-1)=0$. Their unit-length versions are $(1,1)^T/\sqrt{2}$ and $(1,-1)^T/\sqrt{2}$. This example also passes two useful checks: the eigenvalues sum to the trace, $6+2=8=4+4$, and their product equals the determinant, $6\cdot2=12=16-4$.

## From special directions to principal components

In a dataset with many measured features, pairwise correlations show relationships two variables at a time. They do not, by themselves, summarize the main directions of variation across all features at once. PCA does this by examining the covariance matrix, whose entries describe variances and covariances among the features.

For a covariance matrix, eigenvectors give directions in feature space and eigenvalues give the variance along those directions. PCA orders the components from the largest eigenvalue downward. A scree plot displays those eigenvalues, often in descending order. A steep first value followed by much smaller values suggests that one direction captures substantially more variance than the others. Several large values suggest several important directions. This is a description of variation, not proof that the data contain a particular number of natural groups or causes.

Our example matrix could be a covariance matrix. Its total variance is the trace, $8$, and the two eigenvalues account for $6$ and $2$ units of variance. The first component therefore accounts for $6/8=0.75$, or 75 percent, of the total variance. The second accounts for $2/8=0.25$, or 25 percent. These fractions add to 100 percent. This calculation assumes the matrix really is a covariance matrix and that the features have been prepared in a meaningful, consistent way.

That preparation matters. Covariance depends on measurement units: a feature recorded in thousands can dominate one recorded in fractions. Standardizing features before PCA changes the question from variance in original units to variation relative to each feature's scale. Whether to standardize depends on the scientific question, not on a universal rule. PCA also describes linear directions, so curved patterns may not be represented well by a small set of components.

## Filtering and compression, with a warning label

Eigenvectors and eigenvalues also suggest ways to simplify data. In an idealized noise-reduction workflow, one decomposes a suitable matrix, keeps directions associated with larger eigenvalues, and reconstructs the data after setting selected small-eigenvalue directions to zero. This can reduce random variation when the signal is concentrated in a few directions and noise contributes relatively little variance.

That is an assumption, not a guarantee. A low-variance direction may contain an important rare event, a scientifically meaningful small effect, or a feature needed for a later analysis. A threshold chosen only for a tidy plot can erase useful information. Validate any filtering against the task and preserve the unfiltered data.

The same idea underlies a basic form of dimension reduction for compression: represent observations using a smaller set of directions that capture much of their variation, then reconstruct an approximation. The approximation loses information, and the retained components determine the trade-off between size and fidelity. Singular value decomposition (SVD) is commonly used for practical data compression; the conceptual aim of retaining dominant directions is related, but an implementation need not explicitly compute a covariance eigendecomposition.

These ideas appear in many fields, including signal processing, optimization, graph analysis, and multivariate statistics. The shared theme is to find structure across multiple features by asking which directions a transformation treats simply.

## Assumptions and common traps

First, an eigenvector's length is arbitrary. If $v$ is an eigenvector, then any nonzero multiple $cv$ is also one, since $A(cv)=cAv=c\lambda v$. Software often returns unit-length vectors, but their signs can differ between runs or libraries without changing the direction.

Second, eigenvalues can be repeated. When a symmetric matrix has a repeated eigenvalue, its associated eigenspace can have multiple valid orthonormal bases. Comparing individual eigenvectors across analyses may then be misleading; compare the subspace instead.

Third, a large eigenvalue measures variance in a direction, not importance for every objective. PCA does not use outcome labels, establish causation, or test statistical significance. Its results can also be sensitive to outliers, missing-data handling, and choices such as centering and scaling.

Finally, numerical calculations use finite precision. Tiny negative eigenvalues can sometimes arise from rounding when working with a theoretically positive semidefinite covariance matrix. Their size and origin should be checked before treating them as meaningful negative variance.

## Try it yourself

1. If $Av=3v$, what does the matrix do to $v$? **Answer:** It scales $v$ by three without changing its direction.
2. Why must $\det(A-\lambda I)=0$ for an eigenvalue? **Answer:** A nonzero eigenvector lies in the null space of $A-\lambda I$, making that matrix singular.
3. In the worked covariance example, what fraction of variance is represented by the second component? **Answer:** $2/(6+2)=0.25$, or 25 percent.
4. Does retaining the first component prove it is the most scientifically important feature? **Answer:** No. It captures the most variance under this representation, which is not the same as scientific importance or causal effect.

Eigenvectors turn a complicated transformation into a collection of directional scaling rules. That makes them more than an abstract algebraic curiosity: they provide a compact way to describe structure, while reminding us to distinguish mathematical variance from the meaning we assign to it.

---

Published 2026-10-23.

**Source:** PRACTICAL LINEAR ALGEBRA FOR DATA SCIENCE, section **2 matrix. The two vectors in the left plot (**.

This is an original explanatory note; the source book is not redistributed.
