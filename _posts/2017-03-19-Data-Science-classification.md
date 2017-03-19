---
title: 'Data Science - classification'
layout: post
tags:
  - Study
  - Data Science
  - Classification
category: Notes
mathjax: true
---

**Definition**
Classification as the task of learning a **target function** **_f_** that maps each attribute set **_x_** to one of the predicted class labels **_y_**.

**Classification Tasks**
- Predicting tumor cells as benign or malignant
- Classifying credit card transactions as legitimate or fraudulent
- Categorizing news stories as finance, weather, entertainment, sports, etc.

**Classification Techniques**
- Decision Tree based Methods
- Rule-based Methods
- Memory based reasoning
- Neural Networks (NN)
- Naïve Bayes and Bayesian Belief Networks
- Support Vector Machines (SVM)

<!--more-->

---

## Decision Tree
**Decision Tree Based Classification has the following properties**:

- Inexpensive to construct
- Extremely fast at classifying unknown records
- Easy to interpret for small-sized trees
- Accuracy is comparable to other classification techniques for many simple data sets
- Unsuitable for Large Datasets because sorting continuous attributes at each node needs entire data to fit in memory.

### Hunt's Algorithm
- Let `Dt` be the set of training records that reach a node `t`
- If `Dt` contains records that belong the same class `yt`, then t is a leaf node labeled as `yt`
- If `Dt` is an empty set, then `t` is a leaf node labeled by the default class, `yd`
- If `Dt` contains records that belong to more than one class, use an attribute test to split the data into smaller subsets. Recursively apply the procedure to each subset.

### Specify Attribute Test Condition
**Depending on Number of Splits**
- _Binary Split_ - Use as many partitions as distinct values.
- _Multi-Way Split_ - Divides values into two subsets. Need to find optimal partitioning.

**Depending on Attribute Types**
- Nominal
- Ordinal
- Continuous
	- _Discretization_ to form an ordinal categorical attribute
	- _Binary Decision_: (A < v) or (A $\geq$ v)

### Measures of Node Impurity
> Homogeneous class distribution are preferred to **determine the best split (best test condition)**. Thus, need a measure of node **impurity**.

The following figure is the comparison among different impurity measure for a 2-class problem:

![](https://i.imgur.com/LaAOkSl.png)



**1. Gini Index**

Gini Index of node $t$ can be expressed as follows 

$$
Gini(t)=1-\sum_{j=1}^{C}(p(j|t))^2

p(j|t):probability~of~class~j~to~occur~on~node~t

C:collection~of~classes
$$

Assume $n_{t}$ = number of records at child $t$, $n$ = number of records at node $p$, the **quality of a split** on node $p$ can be

$Gini_{split} = \sum_{t=1}^{k}\frac{n_{t}}{n}Gini(t)$


**2. Entropy**

Entropy of node $t$ can be expressed as follows.

$Entropy(t) = -\sum_{j}^{C}p(j|t)log(p(j|t))$

$p(j|t):probability~of~class~j~to~occur~on~node~t$

$C:collection~of~classes$

Assume $n_{t}$ = number of records at child $t$, $n$ = number of records at node $p$, the **Information Gain (IG) of a split** on node $p$ can be

$Gain_{split} = Entropy(p) - \sum_{t=1}^{k}\frac{n_{t}}{n}Entropy(t)$

However, using information gain to decide a split may be in approppriate since it tends to select an attribute with large amount of meaningless values, such as student ID. Thus, **Gain Ratio** should be considered instead as the standardization of Information Gain.

$GainRatio_{split} = \frac{Gain_{split}}{SplitInfo}$

where $SplitInfo = -\sum_{t=1}^{k}\frac{n_{t}}{n}log(\frac{n_{t}}{n})$

**3. Misclassification error**

$Error(t) = 1-max_{j\in C}(p(j|t))$

$p(j|t):probability~of~class~j~to~occur~on~node~t$

$C:collection~of~classes$

### Stopping Criteria for Splitting 
- Stop expanding a node when all the records belong to the **same** class
- Stop expanding a node when all the records have **similar** attribute values
- Early termination 

---

## Rule-Based Classifiers



---

## Instance-Based Classifiers


---

## Bayes Classifiers & Bayesian Classifiers


---

## Artificial Neural Networks (ANN)


---

## Support Vector Machines (SVM)


---

## Ensemble Methods



---

## Practical Issues of Classification
### Underfitting and Overfitting

![](https://i.imgur.com/Ar1ErgU.png)

> **Occam’s Razor** - Given two models of similar generalization errors,  one should prefer the simpler model over the more complex model.
> **Underfitting** - When model is too simple, both training and test errors are large. It occurs when a statistical model or machine learning algorithm cannot capture the underlying trend of the data. For example, when fitting a linear model to non-linear data.
> **Overfitting** - Due to _noise_ or _insufficient training data_, a statistical model describes random error or noise instead of the underlying relationship. As a result, training error no longer provides a good estimate of how well the tree will perform on previously unseen records.


**How To Address Overfitting?**
- Pre-Pruning (Early Stoppping Rule)
	- Stop if number of instances is less than some user-specified threshold
	-  Stop if class distribution of instances are independent of the available features (e.g., using X2 test)
	-   Stop if expanding the current node does not improve impurity measures (e.g., Gini or information gain).
- Post-Pruning
	1. Grow decision tree to its entirety
	2. Trim the nodes of the decision tree in a bottom-up fashion
	3. If generalization error improves after trimming, replace sub-tree by a leaf node.

**Methods for Estimating Generalization Error:**
- Optimistic approach -  Generalization Error = Training Error
- Pessimistic approach -
	- For each leaf node: Generalization Error = (Training Error+0.5) 
	- Total errors: Generalization Error = Training Error + N x 0.5 (_N: number of leaf nodes_)
- Reduced error pruning (REP) -  Use validation data set to estimate generalization error


### Missing Values
Missing values affect decision tree construction in three different ways:

- Affects how **impurity** measures are computed
- Affects how to **distribute** instance with missing value to child nodes
- Affects how a test instance with missing value is **classified**


### Costs of Classification

**Data Fragmentation**
Number of instances gets smaller as you traverse down the tree. As a result, number of instances at the leaf nodes could be too small to make any statistically significant decision.

**Search Strategy**
Finding an optimal decision tree is NP-hard.

The algorithm presented so far uses a **greedy**, **top-down**, **recursive partitioning** strategy to induce a reasonable solution.

**Expressiveness and Decision Boundary**

Decision tree provides expressive representation for learning discrete-valued function, but it is NOT expressive enough for modeling continuous variables.

![](https://i.imgur.com/vpObzLQ.png)

- Decision boundary is **parallel** to axes because test condition involves _a single attribute at-a-time_.
- **Oblique** Decision Trees -  **More expressive** representation. Test condition may _involve multiple attributes_.

**Tree Replication**
Same subtree appears in multiple branches.

![](https://i.imgur.com/EOIqIYX.png)

---

## Model Evaluation
### Metrics for Performance Evaluation
> How to evaluate the performance
 of a model?

Focus on the predictive capability of a model rather than how fast it takes to classify or build models, scalability, etc.

![](https://i.imgur.com/jdcdJRi.jpg)


### Methods for Performance Evaluation
> How to obtain reliable estimate of performance?

Performance of a model may depend on other factors besides the learning algorithm, such as class distribution and size of training/testing set.

**Class distribution**
Consider a 2-class problem. Number of Class 0 examples = 9990, Number of Class 1 examples = 10.

- Accuracy is not Cost-Sensitive. 
- Cost-Sensitive Measures: Precision, Recall, F-measure

**Size of training and test sets**
- Holdout - Reserve 2/3 for training and 1/3 for testing 
- Random subsampling - Repeated holdout
- Bootstrap - Sampling with replacement


### Methods for Model Comparison
> How to compare the relative performance among competing models?

**ROC (Receiver Operating Characteristic)**
ROC curve plots **TP** (on the y-axis) against **FP** (on the x-axis). It  characterizes the trade-off between positive hits and false alarms

![](https://i.imgur.com/yiu1gKv.png)


- How to construct an ROC curve?
    1. Use classifier that produces posterior probability for each test instance P(+|A)
    2. Sort the instances according to P(+|A) in decreasing order
    3. Apply threshold at each unique value of P(+|A)
    4. Count the number of TP, FP, TN, FN at each threshold

- An ROC curve demonstrates several things:
    - It shows the tradeoff between **sensitivity** and **specificity** (any increase in sensitivity will be accompanied by a decrease in specificity).
    - The closer the curve follows the left-hand border and then the top border of the ROC space, the more accurate the test.
    - The closer the curve comes to the 45-degree diagonal of the ROC space, the less accurate the test.
    - The slope of the tangent line at a cutpoint gives the **likelihood ratio** (LR) for that value of the test. You can check this out on the graph above.

**Confidence Interval for Accuracy**
Given x (# of correct predictions) and N (# of test instances), we get acc=x/N, Can we predict p (true accuracy of model)?

- For large test sets (N > 30), acc has a normal distribution with mean p and variance p(1-p)/N
- Confidence Interval for p :
	![](https://i.imgur.com/BG75gfX.png)

---

## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [Wiki - Receiver operating characteristic](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)
- [Plotting and Intrepretating an ROC Curve](http://gim.unmc.edu/dxtests/roc2.htm)