# When Correlation Makes Data Smaller: PCA for Signals, Noise, and Features

A thousand measurements can look like a thousand independent facts. But if neighboring samples move together, much of that data may be repeating the same underlying pattern. Principal component analysis (PCA) turns that observation into a practical tool: it finds orthogonal directions that capture the variation in a dataset, often letting us represent, clean, or interpret observations with fewer coordinates.

The examples in this article explore four uses of that idea: compressing smooth signals, suppressing noise, building classification features, and deciding when a fixed wavelet basis may be more practical. The core is always the same, but the goal and the risks differ. PCA can summarize structure; it cannot guarantee that the structure it keeps is the structure we care about.

## PCA as a change of coordinates

Suppose each observation is a vector with $p$ entries. For a short signal window, those entries might be consecutive time samples. Stack $n$ observations as rows of a matrix $X$. After subtracting each column's sample mean, the sample covariance matrix is

$$
S = \frac{1}{n-1}X^\mathsf{T}X.
$$

The eigenvectors of $S$ give orthogonal directions in the original feature space. Their eigenvalues describe the sample variance along those directions. Order the eigenvectors from largest eigenvalue to smallest and place them in a matrix $V$. Projecting the centered observations onto the first $r$ columns gives

$$
Z = XV_r,
$$

where $V_r$ contains the first $r$ eigenvectors. The rank-$r$ reconstruction is

$$
\widehat X = ZV_r^\mathsf{T} = XV_rV_r^\mathsf{T}.
$$

This is not merely a way to rename the columns. If later eigenvalues are small, the corresponding directions contribute little to squared reconstruction error. For centered training data, the total squared error from retaining the first $r$ components is $(n-1)\sum_{j=r+1}^{p}\lambda_j$, where $\lambda_j$ are the ordered eigenvalues. The fraction of total variance retained is $\sum_{j=1}^{r}\lambda_j / \sum_{j=1}^{p}\lambda_j$.

Those statements describe variance in the data used to estimate the covariance. They do not say that every future observation will reconstruct equally well, or that a low-variance direction has no scientific importance.

## Worked example: compressing three-point windows

Consider four centered observations, each containing three consecutive samples from a smooth, repeating signal:

$$
X = \begin{bmatrix}
-1 & -1 & 0\\
-1 & 1 & 0\\
1 & -1 & 0\\
1 & 1 & 0
\end{bmatrix}.
$$

The first two coordinates vary, while the third is constant at zero. Each of the first two columns has mean zero and sum of squares $4$. Their cross-product is zero, since the four products are $1,-1,-1,1$, which sum to zero. Thus

$$
S = \frac{1}{3}X^\mathsf{T}X =
\begin{bmatrix}
4/3 & 0 & 0\\
0 & 4/3 & 0\\
0 & 0 & 0
\end{bmatrix}.
$$

The eigenvalues are $4/3, 4/3, 0$. The first two eigenvectors can be chosen as the first and second coordinate directions. Keeping $r=2$ components reconstructs every row exactly: the discarded direction has zero variance. The retained variance fraction is $(4/3+4/3)/(8/3)=1$, and the reconstruction error is zero.

This example is deliberately tidy. It also exposes a useful detail: equal eigenvalues do not define a unique pair of eigenvectors. Any orthonormal basis for the two-dimensional leading subspace is valid. In real signal data, the eigenvalues will rarely line up so neatly, but the subspace remains the important object when eigenvalues are close.

## Smooth signals can have compact representations

A low-pass filter suppresses rapid changes in a white-noise input. Neighboring output samples consequently tend to be correlated. If we form fixed-length windows from many realizations of that output, their covariance can concentrate much of its variance in a small number of eigenvectors. The eigenvectors can be interpreted as discrete waveforms, and each window is approximated by a weighted sum of those waveforms.

For a chosen rank $r$, the coefficients are the projection of a centered window onto the retained eigenvectors. The reconstruction combines those coefficients with the same basis. This can reduce the number of values needed per window, but a real compression system must also account for the cost of storing or transmitting the basis, the coefficients, and any means or scaling information. A basis learned from one dataset is not automatically available to a decoder or appropriate for a different signal regime.

The practical question is not simply whether the first few eigenvalues are large. It is whether the resulting reconstruction error is acceptable for the task. A small average squared error can still erase a brief transient, a peak, or a weak component that matters scientifically. Inspect reconstructions and task-relevant diagnostics, not only a variance percentage.

## Denoising means choosing what to preserve

PCA can also suppress noise when the desired signal occupies a relatively low-dimensional subspace and noise contributes substantially in other directions. Estimate a basis from repeated observations, project each observation onto a selected set of leading components, and reconstruct it. Components excluded from the reconstruction are removed along with their associated variation.

That last phrase is the catch. PCA does not label a direction as signal or noise. A signal with strong correlation across time may produce concentrated variance, while independent noise may spread its variance across many directions. But colored noise can itself be correlated, and a weak signal feature can sit in a low-variance direction. If signal and noise overlap in the estimated subspace, PCA cannot cleanly separate them.

Finite samples add another limitation: estimated eigenvectors only approximate the underlying population directions. Their accuracy depends on the number and representativeness of observations, the signal-to-noise conditions, and the separation between eigenvalues. A reconstruction that looks smoother is not automatically more accurate. Compare results against known clean signals in simulations when possible, and use independent validation or domain-specific measures when ground truth is unavailable.

## PCA coefficients as classification features

PCA can reveal a difference that is hard to see in raw coordinates. Imagine two classes of four-dimensional, zero-mean observations. In one class, coordinates 1 and 2 tend to have the same sign; in the other, they tend to have opposite signs. Coordinates 3 and 4 are unrelated noise in both classes. A principal direction approximately proportional to $(1,1,0,0)$ captures the shared movement in the first class. A direction proportional to $(1,-1,0,0)$ captures the opposing movement in the second.

PCA is usually unsupervised: it finds directions of high variation without consulting class labels. The example's feature-selection stage is different. One can compute candidate PCA coefficients, then use labels to select coefficients that distinguish classes. This is supervised feature selection, and it must be evaluated carefully. Selecting features on all data before testing a classifier leaks information from the test set. Split data first, fit transformations and select features using training data only, and evaluate once on held-out data or within a correctly nested cross-validation procedure.

High classification accuracy in one simulation does not establish performance on new populations. Class imbalance, distribution shift, sample size, and the chosen classifier all matter. Also, a component explaining little total variance may be highly discriminative. Selecting features solely by eigenvalue size can therefore discard useful class information.

## When a fixed basis may be preferable

PCA bases are learned from data. If a signal's statistics change over time, a basis estimated in one period may not fit another. A decoder may also need the learned basis, which costs storage or communication. These issues matter for non-stationary signals such as speech or video, where local patterns can change substantially.

Wavelet transforms offer a different tradeoff. Their basis is fixed rather than estimated from the current dataset, and their time-frequency structure can represent localized changes. A fixed basis may simplify deployment and can be effective for compression or denoising, although it is not guaranteed to match a particular dataset as closely as PCA. The choice depends on stationarity, storage constraints, reconstruction goals, and the importance of localized events.

## A reproducible workflow and common pitfalls

Start by defining the observation unit. Overlapping windows from one long recording are not independent just because they occupy different rows. If evaluation data are split randomly at the window level, windows from the same recording may appear in both training and test sets, producing overly optimistic results. Split at the recording, subject, or experimental-unit level when that matches the intended generalization question.

Center features using training-set means and apply those same means to validation and test data. Scaling also changes PCA: standardizing columns gives each feature equal variance before analysis, while covariance-based PCA preserves the original units and relative variance. Neither choice is universally correct. Document it and connect it to the measurement design.

Choose the number of components using more than one criterion. Explained variance is useful for compression summaries, but denoising and classification need task-specific validation. Inspect eigenvalue spectra, reconstruction residuals, and stability across resamples or acquisition periods. Nearly tied eigenvalues can make individual eigenvectors unstable even when their joint subspace is stable; sign flips in eigenvectors are also mathematically immaterial.

Finally, record data selection, preprocessing, component count, random seeds for stochastic steps, and software versions. Reproducibility is part of the analysis, not housekeeping added after the result.

## Exercises with short answers

1. If the eigenvalues are $9, 3, 0$, what fraction of sample variance do the first two components retain? **Answer:** $(9+3)/(9+3+0)=1$, or 100 percent.

2. A low-variance component contains a rare but safety-critical spike. Should variance retention alone decide whether to discard it? **Answer:** No. Evaluate a criterion that reflects the spike's scientific or operational importance.

3. Why can PCA denoising remove genuine signal? **Answer:** PCA retains directions by variance, not by knowing which variation is meaningful. Signal may lie in discarded directions or overlap with noise.

PCA is most useful when its geometry matches the scientific question. Correlation can make signals compact, repeated structure can support denoising, and carefully selected coefficients can aid classification. Yet every gain comes with assumptions about data, basis stability, and what counts as important information. Make those assumptions explicit, test the reconstruction or prediction that matters, and keep the analysis reproducible.

---

Published 2026-11-26.

**Source:** Fundamentals of Linear Algebra for Signal Processing -- James Reilly -- 2024, section **Selected body passage**.

This is an original explanatory note; the source book is not redistributed.
