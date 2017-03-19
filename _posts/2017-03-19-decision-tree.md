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

Decision Tree Based Classification has the following properties:

- Inexpensive to construct
- Extremely fast at classifying unknown records
- Easy to interpret for small-sized trees
- Accuracy is comparable to other classification techniques for many simple data sets
- Unsuitable for Large Datasets because sorting continuous attributes at each node needs entire data to fit in memory.

<!--more-->

## Hunt's Algorithm
- Let `Dt` be the set of training records that reach a node `t`
- If `Dt` contains records that belong the same class `yt`, then t is a leaf node labeled as `yt`
- If `Dt` is an empty set, then `t` is a leaf node labeled by the default class, `yd`
- If `Dt` contains records that belong to more than one class, use an attribute test to split the data into smaller subsets. Recursively apply the procedure to each subset.

## Specify Attribute Test Condition
**Depending on Number of Splits**
- _Binary Split_ - Use as many partitions as distinct values.
- _Multi-Way Split_ - Divides values into two subsets. Need to find optimal partitioning.

**Depending on Attribute Types**
- Nominal
- Ordinal
- Continuous
	- _Discretization_ to form an ordinal categorical attribute
	- _Binary Decision_: (A < v) or (A $\geq$ v)

## Measures of Node Impurity
Homogeneous class distribution are preferred to **determine the best split (best test condition)**.   Thus, need a measure of node **impurity**.   The following figure is the comparison among different impurity measure for a 2-class problem:

![](https://i.imgur.com/LaAOkSl.png)



### Gini Index

Gini Index of node $t$ can be expressed as follows 

$$
Gini(t)=1-\sum_{j=1}^{C}(p(j|t))^2
\\
p(j|t):probability~of~class~j~to~occur~on~node~t
\\
C:collection~of~classes
$$

Assume $n_{t}$ = number of records at child $t$, $n$ = number of records at node $p$, the **quality of a split** on node $p$ can be

$$
Gini_{split} = \sum_{t=1}^{k}\frac{n_{t}}{n}Gini(t)
$$


### Entropy

Entropy of node $t$ can be expressed as follows.

$$
Entropy(t) = -\sum_{j}^{C}p(j|t)log(p(j|t))
\\
p(j|t):probability~of~class~j~to~occur~on~node~t
\\
C:collection~of~classes
$$

Assume $n_{t}$ = number of records at child $t$, $n$ = number of records at node $p$, the **Information Gain (IG) of a split** on node $p$ can be

$$
Gain_{split} = Entropy(p) - \sum_{t=1}^{k}\frac{n_{t}}{n}Entropy(t)
$$

However, using information gain to decide a split may be in approppriate since it tends to select an attribute with large amount of meaningless values, such as student ID. Thus, **Gain Ratio** should be considered instead as the standardization of Information Gain.

$$
GainRatio_{split} = \frac{Gain_{split}}{SplitInfo}
\\
where SplitInfo = -\sum_{t=1}^{k}\frac{n_{t}}{n}log(\frac{n_{t}}{n})
$$

### Misclassification error

$$
Error(t) = 1-max_{j\in C}(p(j|t))
\\
p(j|t):probability~of~class~j~to~occur~on~node~t
\\
C:collection~of~classes
$$

## Stopping Criteria for Splitting 
- Stop expanding a node when all the records belong to the **same** class
- Stop expanding a node when all the records have **similar** attribute values
- Early termination 


---

## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)