# From Scores to Decisions: Reading Classification Metrics Without Losing the Plot

A classifier can give the same score to two observations and still leave you with a hard choice: which one gets flagged? A probability score is not yet a decision. To act on it, you choose a threshold, turn scores into labels, and compare those labels with what actually happened. Each step answers a different question, and mixing them up can make a model look more decisive than it is.

The selected section of *Applied Data Science Using PySpark* follows this path from a model's $\hat{y}$ values, through a sigmoid and a cutoff, to a confusion matrix and several evaluation measures. It also introduces threshold-based curves and ranking by deciles. The central lesson is practical: a final label depends on a threshold, while many useful evaluations examine scores across multiple thresholds.

## A score, a probability, and a decision are not interchangeable

A linear model may produce a score such as

$$
z = \beta_0 + \beta_1x_1 + \cdots + \beta_px_p.
$$

For logistic classification, the sigmoid maps that score to a value between zero and one:

$$
\sigma(z)=\frac{1}{1+e^{-z}}.
$$

The result can be interpreted as an estimated positive-class probability when the model and its calibration support that interpretation. A threshold $t$ then creates a predicted class:

$$
\hat{c}=\begin{cases}1 & p\ge t,\\0 & p<t.\end{cases}
$$

The source section uses a cutoff of $0.5$, with equality assigned to class 1. It notes that the cutoff can be changed. That choice is not a cosmetic setting: lowering $t$ generally flags more cases as positive, while raising it generally flags fewer. The exact changes in false positives and false negatives depend on the data and scores.

A small arithmetic check helps keep the stages straight. For $z=0$, $\sigma(0)=1/(1+1)=0.5$. For $z=\ln(9)$, $e^{-z}=1/9$, so $\sigma(z)=1/(1+1/9)=0.9$. At cutoff $0.5$, both become class 1; at cutoff $0.8$, only the second does. The score-to-probability mapping and the cutoff decision are distinct operations.

The section's example reports sigmoid outputs of $0.5, 0.5, 1.0,$ and $1.0$, followed by four positive predictions under the $0.5$ rule. Those are rounded values. In particular, the sigmoid of a very small positive score is slightly above $0.5$, not exactly $0.5$, and the sigmoid of a large finite score is below 1, not exactly 1. Rounding can hide this distinction, so a reproducible analysis should record score precision and the threshold rule.

## The confusion matrix turns decisions into counts

Once predictions exist, pair each predicted label with its observed target. For a binary target, the four outcomes are:

| Actual class | Predicted 1 | Predicted 0 |
|---|---:|---:|
| 1 | True positive (TP) | False negative (FN) |
| 0 | False positive (FP) | True negative (TN) |

Rows and columns can be displayed in either orientation, so label both axes rather than relying on convention. A false positive is an actual negative incorrectly flagged positive. A false negative is an actual positive that the model missed.

The source gives TP = 3, TN = 0, FP = 1, and FN = 0. These counts total four observations. They are consistent with four predicted positives, of which three are actual positives and one is an actual negative. From the counts, accuracy is

$$
\frac{TP+TN}{TP+TN+FP+FN}=\frac{3+0}{4}=0.75.
$$

The error rate is $1-0.75=0.25$, or equivalently $(FP+FN)/4=1/4$. Precision is $TP/(TP+FP)=3/4=0.75$. Recall, also called sensitivity or true positive rate, is $TP/(TP+FN)=3/3=1$. The F1 score is the harmonic mean:

$$
F_1=2\frac{\text{precision}\cdot\text{recall}}{\text{precision}+\text{recall}}
=2\frac{0.75\cdot1}{0.75+1}=\frac{1.5}{1.75}\approx0.857.
$$

This rounds to about $0.86$, as in the source. The example also shows why accuracy alone is incomplete: the classifier predicts every case positive, so it catches all three positives but correctly identifies no negatives. If missing a positive is costly, recall may matter most. If false alarms are costly, precision deserves attention. If both error types matter, F1 can summarize a tradeoff, though it ignores true negatives and does not encode the actual costs.

Accuracy can also mislead when one class is rare. A model that always predicts the common class can achieve high accuracy while failing to find the cases a researcher cares about. Always compare a metric with class frequencies and the intended decision.

## Threshold curves examine more than one operating point

A confusion matrix and its metrics describe predictions at a particular threshold. Receiver operating characteristic (ROC) analysis varies the threshold and plots true positive rate against false positive rate, where

$$
\text{TPR}=\frac{TP}{TP+FN},\qquad
\text{FPR}=\frac{FP}{FP+TN}=1-\text{specificity}.
$$

The ROC area under the curve (AUC) summarizes ranking performance across thresholds. A diagonal reference corresponds to chance-level ranking under common assumptions, with area $0.5$; perfect ranking has area 1. AUC does not identify a useful operational threshold, guarantee calibrated probabilities, or establish causal effects. In deployments where negative cases vastly outnumber positive ones, a small false-positive rate can still produce many false alarms.

A precision-recall (PR) curve plots precision against recall at varying thresholds. It often provides a clearer view when positives are uncommon, because it focuses on positive predictions and positive cases. Its baseline depends on positive-class prevalence: a no-skill ranking has expected precision near that prevalence. PR summaries have conventions that vary by implementation, so state whether the reported area is trapezoidal PR area or average precision. The source describes its PR baseline and area informally; do not assume every library computes exactly the same summary.

The section's ROC and PR workflows use score values across thresholds, rather than only the final hard predictions. The distinction matters: computing AUC from class labels alone discards ranking information. Also inspect whether a framework expects raw margins, class probabilities, or another score column, and verify positive-class encoding. An evaluator's column name is not a substitute for checking the contents.

## Deciles and KS turn scores into ranked groups

A decile analysis sorts observations by score, then divides the ordered records into ten groups. It can answer questions such as how many positive outcomes appear in the top tenth, or how observed rates change across ranked groups. This is useful in campaign targeting and other workflows where a limited number of cases can be acted on. Ties at group boundaries need an explicit and reproducible policy; otherwise, a small change in ordering may alter bucket membership.

The Kolmogorov-Smirnov statistic in this setting is the maximum separation between cumulative score-ranked distributions of positives and negatives. If a table reports cumulative percentages, the absolute difference is commonly the quantity of interest. The source describes a signed spread and takes its maximum; depending on ordering and sign convention, taking the maximum signed difference may not equal the maximum absolute separation. Define the convention before comparing KS values. Empty classes also make the corresponding cumulative distribution undefined.

Comparing training and test ranking summaries can expose a performance gap, but similar values do not prove a model is stable in every meaningful sense. Sampling uncertainty, time drift, subgroup behavior, calibration, and leakage require separate checks. Decile groups are a descriptive view, not a guarantee of future performance.

## A careful evaluation workflow

A dependable workflow preserves the boundary between model score, threshold choice, and evaluation:

1. Confirm which class is positive and what the score represents.
2. Choose a threshold using a stated operational goal, preferably on validation data rather than the final test set.
3. Apply the threshold consistently, including its equality rule.
4. Report the confusion counts alongside chosen metrics.
5. Compare threshold-independent ranking summaries with threshold-specific performance, and include class prevalence.
6. Evaluate on data that reflect the intended use, then examine relevant subgroups and time periods.

The source section introduces several useful tools, including accuracy, precision, recall, F1, ROC/PR curves, KS, and deciles. These tools answer related but different questions. No single number certifies a classifier as good. The right report makes the tradeoffs visible and explains what the data and evaluation procedure can support.

## Assumptions and common pitfalls

The formulas above assume a binary target with clearly identified positive and negative classes. Precision is undefined if there are no predicted positives; recall is undefined if there are no actual positives. Software may return a convention such as zero or a warning, so check library behavior rather than treating every output as mathematically defined.

Use a threshold selected without peeking at the test outcomes. If you tune it repeatedly on the test set, the reported test performance becomes part of the tuning process. Keep preprocessing, model fitting, and evaluation boundaries reproducible. Record the random seed and data split when applicable, but remember that a fixed seed does not remove sampling uncertainty.

A sigmoid output is not automatically a well-calibrated probability. AUC can be strong even when predicted probabilities are systematically too high or too low. If decisions depend on probability magnitudes, assess calibration separately. Finally, association and predictive accuracy do not establish that changing an input will cause an outcome to change.

## Exercises with short answers

1. A classifier has TP = 18, FP = 6, FN = 2, and TN = 74. What are precision and recall? **Answer:** Precision is $18/(18+6)=0.75$; recall is $18/(18+2)=0.90$.
2. If the threshold rises and all else stays fixed, which count can only stay the same or decrease: false positives or true negatives? **Answer:** False positives cannot increase under the usual rule that a larger score makes a positive prediction. True negatives cannot decrease.
3. Why report prevalence with PR performance? **Answer:** The no-skill precision baseline depends on the fraction of actual positives, so a PR summary needs that context.

A score becomes useful only when its meaning, threshold, and consequences are clear. Count the outcomes, inspect the tradeoffs, and keep the evaluation honest. That is how a tidy metric becomes a trustworthy piece of evidence.

---

Published 2026-10-30.

**Source:** Applied Data Science Using PySpark - Learn the End-to-End -- Ramcharan Kakarla & Sundar Krishnan & Sridhar Alla, section **6.4.2.3 y-hat prediction to probability to final prediction**.

This is an original explanatory note; the source book is not redistributed.
