# From Scores to Decisions: Reading Classification Models Carefully

A model can assign an observation a score of 0.51 and label it positive, while assigning another a score of 0.49 and labeling it negative. The observations look nearly alike, yet the decision flips. That small threshold choice can change how many alerts, diagnoses, or review cases your system produces. Understanding the path from model score to probability to class label is therefore more than a plotting detail. It is part of defining what the model is for.

The section considered here follows that path: a model produces a raw prediction, a sigmoid can map a binary score to a probability-like value, and a cutoff turns that value into a class. It then evaluates the resulting classifications with a confusion matrix and discusses threshold-independent ranking views such as ROC and precision-recall curves, as well as deciles and the Kolmogorov-Smirnov statistic. The ideas connect, but they answer different questions. Keeping those questions separate makes evaluation more useful.

## Three stages, three meanings

For a binary classifier, let $z$ be a raw model score, often called a logit. The sigmoid function is

$$
\sigma(z) = \frac{1}{1 + e^{-z}}.
$$

It maps any finite real input to a number strictly between 0 and 1. If the model was trained to produce logits, applying the sigmoid transforms them to probability-scale scores. That transformation alone does not prove that the scores are calibrated probabilities. Calibration asks whether, among observations assigned a score near 0.7, roughly 70 percent are positive in the relevant population.

A class decision requires another choice. With cutoff $c$, predict positive when $\sigma(z) \ge c$, and negative otherwise. The common choice $c=0.5$ is a convention, not a universal optimum. For the sigmoid, a cutoff of 0.5 corresponds to $z=0$, because $\sigma(0)=0.5$. A score just above zero therefore crosses the decision boundary, even if it is only slightly positive.

This detail helps interpret the example in the selected passage. A raw value of $0.0000021$ gives a sigmoid value of about $0.500000525$, not exactly 0.5. Rounded to one decimal place it appears as 0.5, but with the rule “greater than or equal to 0.5,” it is positive. A raw score of 22.45 gives a sigmoid value extremely close to 1. Rounding intermediate values can obscure a threshold decision, so retain adequate precision until after classification.

## A worked example, from threshold to confusion matrix

Suppose a screening model returns these scores for six observations. Treat the scores as probability estimates for this example, and use a cutoff of 0.5.

| Observation | Actual class | Score | Predicted class |
|---|---:|---:|---:|
| A | 1 | 0.90 | 1 |
| B | 1 | 0.70 | 1 |
| C | 1 | 0.40 | 0 |
| D | 0 | 0.60 | 1 |
| E | 0 | 0.30 | 0 |
| F | 0 | 0.10 | 0 |

The rule gives three positive predictions, A, B, and D. Comparing each with its actual class yields two true positives (TP), two true negatives (TN), one false positive (FP), and one false negative (FN). In a confusion matrix, actual classes form rows and predicted classes form columns:

|  | Predicted 0 | Predicted 1 |
|---|---:|---:|
| Actual 0 | TN = 2 | FP = 1 |
| Actual 1 | FN = 1 | TP = 2 |

Now compute the common threshold-based measures. Accuracy is the fraction classified correctly:

$$
\text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN}
= \frac{2+2}{6} = \frac{2}{3} \approx 0.667.
$$

The misclassification rate is $1-\text{Accuracy}=1-2/3=1/3\approx0.333$. Precision asks what fraction of predicted positives were truly positive: $TP/(TP+FP)=2/3\approx0.667$. Recall, also called sensitivity or true positive rate, asks what fraction of all actual positives were found: $TP/(TP+FN)=2/3\approx0.667$. The F1 score is the harmonic mean of precision and recall:

$$
F_1 = \frac{2PR}{P+R} = \frac{2(2/3)(2/3)}{2/3+2/3} = \frac{2}{3}.
$$

These arithmetic checks are useful because metric names can sound abstract while the denominators differ. Precision conditions on predicted positives; recall conditions on actual positives. Neither number alone tells the full story.

## A cutoff expresses a tradeoff

Lowering the cutoff usually labels more cases positive. That can increase recall, but it may also increase false positives and reduce precision. Raising the cutoff usually has the opposite effect. The exact pattern depends on the scores and labels, and ties at a cutoff matter because the classification rule includes equality.

Choose a cutoff in light of the costs and purpose of the decision. If missing a positive case is especially costly, recall may deserve priority. If false alarms consume scarce investigation time, precision may matter more. The relevant costs may also vary with the number of cases a team can handle. A threshold should be selected using appropriate validation data and domain requirements, not by treating 0.5 as a law of nature.

Accuracy can mislead when one class is much more common than the other. For example, a classifier that always predicts the majority class may achieve high accuracy while finding none of the minority class. A confusion matrix makes that failure visible. Reporting the class counts and the metric definitions alongside results gives readers a better chance of spotting it.

## ROC and precision-recall curves answer ranking questions

A receiver operating characteristic (ROC) curve examines performance over a range of cutoffs. Its vertical coordinate is true positive rate, or recall, and its horizontal coordinate is false positive rate, $FP/(FP+TN)$, equal to $1-\text{specificity}$. Each cutoff produces a point. The area under the ROC curve (ROC AUC) summarizes ranking performance across thresholds: informally, how well positive cases tend to rank above negative cases. A diagonal reference line corresponds to chance-level ranking under common assumptions, with area 0.5. AUC is not accuracy at a chosen threshold, and it does not establish that predicted probabilities are calibrated.

Precision-recall (PR) curves also vary the threshold, plotting precision against recall. They can be especially informative when positive cases are rare, because precision directly reflects the share of predicted positives that are correct. The baseline precision for an uninformative ranking is related to the positive-class prevalence, not invariably 0.5. A random ranking can fluctuate around that baseline, especially in a small dataset. This is why a PR curve should be read with the class balance in view.

Curves are useful for comparing score rankings, but they do not make operational decisions for you. Select a working cutoff separately, and report its confusion matrix and relevant threshold-based metrics. Also use held-out data for final evaluation. Choosing a threshold and assessing it on the same data can give an overly optimistic impression.

## Deciles and KS: useful summaries with caveats

A decile analysis sorts observations by score and divides them into ten ranked groups. It can show how many positives occur among the highest-scored cases, which is helpful for prioritizing a limited campaign or review queue. Deciles discard some score detail, and groups can be awkward when many observations have tied scores or the sample size is small. State how ties and group sizes are handled.

The Kolmogorov-Smirnov (KS) statistic, in this setting, is the maximum absolute separation between the cumulative score-ranked distributions of positive and negative cases. It summarizes how distinctly the two groups are ordered by score. Its value depends on the sample and the construction of the cumulative distributions; it does not establish that a model will remain stable in a new population. Comparing training and test KS values can be a diagnostic, but similar values alone do not prove stability. Check uncertainty, data leakage, time variation, and performance on genuinely relevant future data.

## Practical pitfalls and a reproducible routine

First, distinguish model outputs. A raw score, sigmoid-transformed score, calibrated probability, and final class label are not interchangeable. Second, check the positive-class convention. Swapping which label counts as positive changes precision, recall, and curve interpretation. Third, check matrix orientation in plotting libraries: some put actual labels on rows and predicted labels on columns, while other displays can be configured differently. Label axes explicitly.

A dependable workflow records the score definition, positive class, cutoff, data split, and metric formulas. Preserve unrounded scores for threshold calculations. Plot ROC or PR curves when ranking across thresholds matters, and pair them with confusion-matrix counts at the operational cutoff. If the data are imbalanced, report class prevalence and consider PR performance rather than relying on accuracy or ROC AUC alone. These habits make the evaluation easier to reproduce and harder to misread.

## Exercises with short answers

1. A model gives score 0.48 and cutoff 0.5. What class does the stated rule assign? **Answer:** Negative.
2. If $TP=18$, $FP=6$, and $FN=9$, what are precision and recall? **Answer:** Precision is $18/(18+6)=0.75$; recall is $18/(18+9)=2/3\approx0.667$.
3. Why might a PR curve be more revealing than accuracy on rare-event data? **Answer:** It shows the precision-recall tradeoff for the positive class, while accuracy can look high simply because most cases are negative.

A classifier is not one number. It is a scoring system, a decision rule, and an evaluation context. Make each part explicit, and the resulting plots and metrics become evidence you can interpret rather than decorations you merely report.

---

Published 2026-11-11.

**Source:** Applied Data Science Using PySpark - Learn the End-to-End -- Ramcharan Kakarla & Sundar Krishnan & Sridhar Alla, section **6.4.2.3 y-hat prediction to probability to final prediction**.

This is an original explanatory note; the source book is not redistributed.
