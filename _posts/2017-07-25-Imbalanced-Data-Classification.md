---
title: 'Association Analysis of Sequence Data'
layout: post
tags:
  - Data-Mining
  - Classification
category: Notes
mathjax: true
---

Most of data in the real-word are imbalance in nature. Imbalanced class distribution is a scenario where the number of observations belonging to one class is significantly lower than those belonging to the other classes.   This happens because Machine Learning Algorithms are usually designed to improve accuracy by reducing the error. Thus, they do not take into account the class distribution / proportion or balance of classes.


> **Accuracy Paradox**
> Accuracy Paradox is the case where your accuracy measures tell the story that you have excellent accuracy (such as 90%), but the accuracy is only reflecting the underlying class distribution.



<!--more-->

## Overview

There are options and approaches to deal with imbalanced dataset:

1. **Collect More Data**
    - Not applicable in many cases.
2. **Change Performance Metrics For Choosing the Model**
    - *Kappa (or [Cohen’s kappa](https://en.wikipedia.org/wiki/Cohen%27s_kappa))*: Classification accuracy normalized by the imbalance of the classes in the data.
    - *ROC Curves*: Like precision and recall, accuracy is divided into sensitivity and specificity and models can be chosen based on the balance thresholds of these values.
3. **Resample Dataset**
    - Use under-sampling or over-sampling or both to even-up the classes.
    - [Undersampling techniques](#undersampling-techniques) includes T-Link, Nearmiss, ...
    - [Oversampling techniques](#oversampling-techniques) includes cluster-based method, generating synthetic samples (SMOTE), ...
4. **Penalized Models (Cost-Sensitive Learning)**
    - Penalized classification imposes an additional cost on the model for making classification mistakes on the minority class during training. 
    - These penalties can bias the model to pay more attention to the minority class.
    - For example, Weka has a [CostSensitiveClassifier](http://weka.sourceforge.net/doc.dev/weka/classifiers/meta/CostSensitiveClassifier.html) that can wrap any classifier and apply a custom penalty matrix for miss classification. (Note that setting up the penalty matrix can be complex.)
5. **Algorithmic Ensemble Techniques**
    - Modify existing classification algorithms to make them appropriate for imbalanced data sets.
    - *Bootstrap Aggregating*: Generating multiple different bootstrap training samples with replacement. And training the algorithm on each bootstrapped algorithm separately and then aggregating the predictions at the end.
    ![](https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/03/16142914/ICP5.png)

Here we are going to focus on Resampling Techniques since it is widely used for tackling class imbalance problem.


## UnderSampling Techniques

Undersampling aims to balance class distribution by eliminating the number of majority class examples.

### Random Under-Sampling

>Random Undersampling (RUS) aims to balance class distribution by randomly eliminating majority class examples. 

Advantage of this method is:
- Help improve run time and storage problems by reducing the number of training data samples when the training data set is huge.

Disadvantages of this method are:
- It can discard potentially useful information which could be important for building rule classifiers.
- The sample chosen by random under sampling may be a biased sample.

### Clustering based under-sampling

> A clustering technique is employed to resample the original training set into a smaller set of representative training examples, represented by weighted cluster centers or selective samples in each clusters.

Advantage of this method is:
- Compared to random under-sampling, cluster-based under-sampling can effectively avoid the important information loss of majority class.

Paper Related:
- [Cluster-based majority under-sampling approaches for class imbalance learning](http://sci2s.ugr.es/keel/pdf/specific/articulo/yen_cluster_2009.pdf)
- [Learning pattern classification tasks with imbalanced data sets](http://ro.uow.edu.au/cgi/viewcontent.cgi?article=1806&context=infopapers)
- [A supervised learning approach for imbalanced data sets](http://ro.uow.edu.au/cgi/viewcontent.cgi?article=10491&context=infopapers)

### Tomek Link (T-Link)

> Tomek links remove unwanted overlap between classes where majority class links are removed until all minimally distanced nearest neighbor pairs are of the same class.

Let $x$ be an instance of class A and $y$ an instance of class B.
Let d(x, y) be the distance between x and y.

$$
(x, y)~is~a~T-Link,~if~for~any~instance~z,\\
d(x, y) < d(x, z)~or~d(x, y) < d( y, z)    
$$

If any two examples are T-Link then one of these examples is a noise or otherwise both examples are located on the boundary of the classes.
T-Link method can be used as a method of guided undersampling where the observations from the majority class are removed.

Paper Related:
- [Classification of Imbalance Data using Tomek Link (T-Link) Combined with Random Under-sampling (RUS) as a Data Reduction Method](https://datamining.imedpub.com/classification-of-imbalance-data-using-tomek-linktlink-combined-with-random-undersampling-rus-as-a-data-reduction-method.pdf)

Document Related:
- [Pthon Library: imblearn](http://contrib.scikit-learn.org/imbalanced-learn/auto_examples/under-sampling/plot_tomek_links.html)

### Nearmiss Method

>  “NearMiss-1” selects the majority class samples whose average distances to three closest minority class samples are the smallest.
> 
> “NearMiss-2” selects the majority class samples whose average distances to three farthest minority class samples are the smallest.
> 
> “NearMiss-3” take out a given number of the closest majority class samples for each minority class sample. 

Document Related:
- [Pthon Library: imblearn](http://contrib.scikit-learn.org/imbalanced-learn/generated/imblearn.under_sampling.NearMiss.html)

### One-Sided Selection

> One-Sided Selection removes _noise examples_, _borderline examples_, and _redundent examples_.

The majority samples are roughly divided into 4 groups:
- _Noise examples_: The examples that are extremely close to any minority example.
- _Borderline examples_: The examples that are close to the boundary between positive and negative regions.   These examples are unreliable.
- _Redundent examples_: The examples whose part can be taken over by other examples, which means they can not provide any other useful information.
- _Safe examples_: The examples that are worth being kept for future classification tasks.



Paper Related:
- [Addressing the Curse of Imbalanced Training Sets: One-Sided Selection](http://sci2s.ugr.es/keel/pdf/algorithm/congreso/kubat97addressing.pdf)

Document Related:
- [Pthon Library: imblearn](http://contrib.scikit-learn.org/imbalanced-learn/auto_examples/under-sampling/plot_one_sided_selection.html)

## OverSampling Techniques

Oversampling aims to balance class distribution by increasing the number of minority class examples.

### Random Over-Sampling

> Random Over-Sampling (ROS) increases the number of instances in the minority class by randomly replicating them in order to present a higher representation of the minority class in the sample.

Advantages of this method are:
- Unlike under sampling this method leads to no information loss.
- Outperforms under sampling

Disadvantage of this method is:
- It increases the likelihood of overfitting since it replicates the minority class events.
    
### Cluster-Based Oversampling

> In this case, the K-means clustering algorithm is independently applied to minority and majority class instances. This is to identify clusters in the dataset. Subsequently, each cluster is oversampled such that all clusters of the same class have an equal number of instances and all classes have the same size.

In 2004, Cluster-based oversampling (CBOS) is proposed by Jo & Japkowicz. CBOS attempts to even out the between-class imbalance as well as the within-class imbalance. There may be subsets of the examples of one class that are isolated in the feature-space from other examples of the same class, creating a within-class imbalance. Small subsets of isolated examples are called small disjuncts. Small disjuncts often cause degraded classifier performance, and CBOS aims to eliminate them without removing data.

Paper Related:
- [Concept-learning in the presence of between-class and within-class imbalances](https://pdfs.semanticscholar.org/b8cd/37b014778de6853c24fe709cd14f97d3f653.pdf)
- [Class imbalances versus small disjuncts](http://delivery.acm.org/10.1145/1010000/1007737/p40-japkowicz.pdf?ip=140.110.90.232&id=1007737&acc=ACTIVE%20SERVICE&key=AF37130DAFA4998B%2ED4BA753E6EB970F3%2E4D4702B0C3E38B35%2E4D4702B0C3E38B35&CFID=786979403&CFTOKEN=26396549&__acm__=1500367766_b21d16da7e54afab0f702bfa081860e9)
    

### Synthetic Minority Oversampling Technique (SMOTE)

> SMOTE produces synthetic minority class samples by selecting some of the nearest minority neighbors of a minority sample which is named S, and generates new minority class samples along the lines between S and each nearest minority neighbor.

Paper Related:
- [SMOTE: Synthetic Minority Over-sampling Technique](https://www.jair.org/media/953/live-953-2037-jair.pdf)

Document Related:
- [Pthon Library: imblearn](http://contrib.scikit-learn.org/imbalanced-learn/generated/imblearn.over_sampling.SMOTE.html)

### The adaptive synthetic sampling approach (ADASYN)

> ADASYN algorithm builds on the methodology of SMOTE.   It uses a weighted distribution for different minority class examples according to their level of difficulty in learning, where more synthetic data is generated for minority class examples that are harder to learn.

The key idea of ADASYN algorithm is to use a density distribution $r^i$ as a criterion to automatically decide the number of synthetic samples that need to be generated for each minority data example.



Paper Related:
- [ADASYN: Adaptive Synthetic Sampling Approach for Imbalanced. Learning.](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.309.942&rep=rep1&type=pdf)

Document Related:
- [Pthon Library: imblearn](http://contrib.scikit-learn.org/imbalanced-learn/generated/imblearn.over_sampling.ADASYN.html)


## References
- [How to handle Imbalanced Classification Problems in machine learning?](https://www.analyticsvidhya.com/blog/2017/03/imbalanced-classification-problem/)
- [8 Tactics to Combat Imbalanced Classes in Your Machine Learning Dataset](http://machinelearningmastery.com/tactics-to-combat-imbalanced-classes-in-your-machine-learning-dataset/)
- [Oversampling and undersampling in data analysis](https://www.wikiwand.com/en/Oversampling_and_undersampling_in_data_analysis)
- [Learning pattern classification tasks with imbalanced data sets](http://ro.uow.edu.au/cgi/viewcontent.cgi?article=1806&context=infopapers)
- [Cluster-based under-sampling approaches for imbalanced data distributions](http://sci2s.ugr.es/keel/pdf/specific/articulo/yen_cluster_2009.pdf)
- [數據嗨客 | 第6期：不平衡數據處理](https://kknews.cc/zh-tw/tech/ylapn.html)
- [Addressing the Curse of Imbalanced Training Sets: One-Sided Selection](http://sci2s.ugr.es/keel/pdf/algorithm/congreso/kubat97addressing.pdf)
- [Experimental Perspectives on Learning from Imbalanced Data](http://delivery.acm.org/10.1145/1280000/1273614/p935-van_hulse.pdf?ip=140.110.90.232&id=1273614&acc=ACTIVE%20SERVICE&key=AF37130DAFA4998B%2ED4BA753E6EB970F3%2E4D4702B0C3E38B35%2E4D4702B0C3E38B35&CFID=786979403&CFTOKEN=26396549&__acm__=1500367484_4f2f4585c98ddab06a2eda5245585b78)
- [A Multiple Resampling Method for Learning from Imbalanced Data Sets](https://pdfs.semanticscholar.org/5665/69859435e974a8651c7836a8dcfbb1597a42.pdf)