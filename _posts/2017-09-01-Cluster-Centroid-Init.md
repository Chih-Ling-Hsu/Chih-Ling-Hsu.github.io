---
title: 'Cluster Center Initialization Algorithms (CCIA)'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

In iterative clustering algorithms, the procedure adopted for choosing initial cluster centers is extremely important as it has a direct impact on the formation of final clusters.   **It is dangerous to select outliers as initial centers, since they are away from normal samples.**

Cluster Center Initialization Algorithms (CCIA) is a density-based multi-scale data condensation.   This procedure is applicable to clustering algorithms for continuous data.   In CCIA, we assume that an individual attribute may provide some information about initial cluster center.

<!--more-->

The CCIA procedure is in below.

1. **Estimating the density at a point**
2. **Sorting the points based on the density criterion**
    - For each dimension (attribute), divide the normal-distribution curve into $K$ partitions. (The area under each partition is equal.)
3. **Selecting a point according to the sorted list**
    - For each dimension (attribute), take the $j^{th}$representative-point $Z_j$ for each partition interval $j \in (1,K)$
    - The area from $-\inf$ to $Z_j$ equals to $(2j-1)/2K$
    - For example, if $K=3$ and $j=1$, then we can find that when the left-area is $(2\times1-1)/(2\times3)=1/6$, the $j^{th}$ representative is at $Z_{score} = -0.9672$ by looking up the CDF(Cumulative Distribution Function) table.
4. **Pruning all points lying within a disc about a selected point with radius inversely proportional to the density at that point.**

![](https://ars.els-cdn.com/content/image/1-s2.0-S0167865504000996-gr1.jpg)


CCIA generates _K'_ clusters which may be greater than the desired number of clusters _K_. In this situation our aim is to merge some of the similar clusters so as to get _K_ clusters.

## Cluster Center Proximity Index (CCPI)

To evaluate the performance of CCIA, here we introduce **Cluster Center Proximity Index (CCPI)**.

$$
CCPI = \frac{1}{K \times m}\sum_{s=1}^{K}\sum_{j=1}^{m}~\biggl|~\frac{f_{sj}-C_{sj}}{f_{sj}}\biggr|
$$

where $f_{sj}$ is the $j_{th}$ attribute value of the desired $s_{th}$ cluster center and $C_{sj}$ is the $j_{th}$ attribute value of the initial $s_{th}$ cluster center.

The CCPI of different data set using CCIA and random initialization is shown as follows.

| Data set | CCIA | Random |
| --- | --- | --- |
| Fossil data | 0.0021 | 0.3537 |
| Iris data | 0.0396 | 0.8909 |
| Wine data | 0.1869 | 0.3557 |
| Ruspini data | 0.0361 | 1.2274 |
| Letter image recognition data | 0.0608 | 0.1572 |

Despite the fact that CCIA performs better in the above data sets, note that CCIA is not always better than using random initialization.

## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [Khan, S. S., & Ahmad, A. (2004). Cluster center initialization algorithm for K-means clustering. _Pattern recognition letters_, _25_(11), 1293-1302.](https://www.sciencedirect.com/science/article/pii/S0167865504000996)