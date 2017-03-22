---
title: 'Instance Based Classification'
layout: post
tags:
  - DataScience
  - Classification
category: Notes
mathjax: true
---

Basic Idea of Instance-Based Classification:

- Store the training records 
- Use training records to predict the class label of unseen cases

<!--more-->

## Nearest-Neighbor Classifier

Nearest-Neighbor Classifier requires three things:

**1. The set of stored records**
	
Scaling issues: Attributes may have to be scaled to prevent distance measures from being dominated by one of the attributes

**2. [Distance Metric](../../2017/03/19/Data-Science-data-exploration#distance-metrics) to compute distance between records**

Problem with Euclidean measure: High dimensional data may encounter curse of dimensionality.   To solve this problem, we can produce counter-intuitive results by normalizing the vectors to unit length.


**3. The value of k, the number of nearest neighbors to retrieve**

- If k is too small, sensitive to noise points
- If k is too large, neighborhood may include points from other classes


With the above three things, Nearest-Neighbor Classifier classifies an unknown record following the below steps:

1. Compute distance between the unknown record and other training records
2. Identify k nearest neighbors 
3. Use class labels of nearest neighbors to determine the class label of unknown record
	- by taking majority vote
	- by weighting the vote according to distance \\(w = \frac{1}{d^{2}}\\)

Unlike eager learners such as decision tree induction and rule-based systems, k-NN classifiers does not build models explicitly.   As a result, classifying unknown records are relatively expensive.


## PEBLS: Parallel Examplar-Based Learning System

PEBLS (Parallel Exemplar-Based Learning System) is a nearest-neighbor
learning system (k=1) designed for applications where the instances have
symbolic feature values.  PEBLS has been applied to the prediction of
protein secondary structure and to the identification of DNA promoter
sequences. 

### Distance Between Nominal Attribute Values

For nominal features, distance between two nominal values is computed using **modified value difference metric (MVDM)**

$$
d(V_{1}, V_{2})=\sum_{i}\left|\frac{n_{1_{i}}}{n_{1}}-\frac{n_{2_{i}}}{n_{2}}\right|
$$

Where \\(n_{1}\\) is the number of records that consists of nominal attribute value \\(V_{1}\\) and \\(n_{1_{i}}\\) is the number of records whose target label is class \\(i\\).

<img src="pebls_tab.png"></img>

Use the above table as an example, we can calculate distance between different nomunal attribute values:

- d(Status=Single, Status=Married) =  | 2/4 – 0/4 | + | 2/4 – 4/4 | =  1
- d(Status=Single, Status=Divorced) =  | 2/4 – 1/2 | + | 2/4 – 1/2 | =  0
- d(Status=Married, Status=Divorced) =  | 0/4 – 1/2 | + | 4/4 – 1/2 | =  1
- d(Refund=Yes, Refund=No) = | 0/3 – 3/7 | + | 3/3 – 4/7 | = 6/7

### Distance Between Records

$$
\delta(X,Y)= w_{X}w_{Y}\sum_{i=1}^{d}d(X_{i}, Y_{i})
$$

Each record \\(X\\) is assigned a **weight factor** \\(w_{X}\\), which represents the reliability of the certain record.

$$
w_{X} = \frac{N_{X_{predict}}}{N_{X_{predict~correct}}}
$$

where \\(N_{X_{predict}}\\) is the number of times X is used for prediction and \\(N_{X_{predict~correct}}\\) is the number of times the prediction using \\(X\\) is correct.

We can see that if \\(w_{X}>1\\), then \\(X\\) is not reliable for making predictions.   High \\(w_{X}>1\\) would result in high distance, which makes it less possible to use \\(X\\) to make predictions.


## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)