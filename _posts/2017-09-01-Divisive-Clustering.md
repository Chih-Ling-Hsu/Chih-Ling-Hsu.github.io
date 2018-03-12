---
title: 'Divisive Method for Hierarchical Clustering and Minimum Spanning Tree Clustering'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Divisive clustering **starts with one, all-inclusive cluster**.   At each step, it **splits a cluster until each cluster contains a point** (or there are k clusters).

<!--more-->

## Divisive Clustering Example

The following is an example of Divisive Clustering.

| Distance | a | b | c | d | e |
| - | - | - |- |- | - |
| a | 0 | 2|6|10|9|
|b|2|0|5|9|8|
|c|6|5|0|4|5|
|d|10 |9|4|0|3|
|e|9|8|5|3|0|


**Step 1.** Split whole data into 2 clusters

1. Who hates other members the most? (in Average)
    - $a$ to others: $mean(2,6,10,9)=6.75 ~ \rightarrow a$ goes out! (Divide $a$ into a new cluster)
    - $b$ to others: $mean(2,5,9,8)=6.0$
    - $c$ to others: $mean(6,5,4,5)=5.0$
    - $d$ to others: $mean(10,9,4,3)=6.5$
    - $e$ to others: $mean(9,8,5,3)=6.25$
2. Everyone in the old party asks himself: _"In average, do I hate others in old party more than hating the members in the new party?"_
    - If the answer is "No", then he will also go to the new party.

    |  | $\alpha=$distance to the old party | $\beta=$distance to the new party | $\alpha-\beta$ |
    | - | - | - | - |
    | b | $\frac{5+9+8}{3}=7.33$ | 2 | $>0$ ($b$ also goes out!) |
    | c | $\frac{5+4+5}{3}=4.67$ | 6 | $<0$ |
    | d | $\frac{9+4+3}{3}=5.33$ | 10 | $<0$ |
    | e | $\frac{8+5+3}{3}=5.33$ | 9 | $<0$ |

3. Everyone in the old party ask himself the same question as above again and again until everyone got the answer "Yes". 

    |  | $\alpha=$distance to the old party | $\beta=$distance to the new party | $\alpha-\beta$ |
    | - | - | - | - |
    | c | ... | ... | $<0$ |
    | d | ... | ... | $<0$ |
    | e | ... | ... | $<0$ |
    
**Step 2.** Choose a current cluster and split it as in **Step 1.**

1. Choose a current cluster
    - If split the cluster with the largest number of members, then the cluster $\{c,d,e\}$ will be split.
    - If split the cluster with the largest diameter, then the cluster $\{c,d,e\}$ will be split.
    
        | cluster | diameter |
        | - | - |
        | {a,b} | 2 |
        | {c,d,e} | 5 |

2. Split the chosen cluster as in **Step 1.**

**Step 3.** Repeat **Step 2.** until each cluster contains a point (or there are k clusters)


## Minimum Spanning Tree Clustering

Building MST (Minimum Spanning Tree) is a method for constructing hierarchy of clusters.

It starts with a tree that consists of a point $p$.   In successive steps, look for the closest pair of points $(p, q)$  such that $p$ is in the current tree but $q$ is not.   With this closest pair of points $(p, q)$, add $q$ to the tree and put an edge between $p$ and $q$.

![](https://i.imgur.com/kZdrQAi.png)

The procedure of constructing hierarchy of clusters using MST would be as follows:

```python
Compute a MST for the proximity graph
repeat
    Split a cluster by breaking the inconsistent edge.
until Only singleton clusters remain
```

Note that the inconsistent edge is the link of the largest distance (smallest similarity).

The definition of inconsistency varies. For example, we can also use **local** inconsistency remove edges significantly larger than their neighborhood edges.

| ![](https://i.imgur.com/FivjUQl.png) | ![](https://i.imgur.com/viy1vqP.png) |
| - | - |


## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [Gower, J. C., & Ross, G. J. (1969). Minimum spanning trees and single linkage cluster analysis. Applied statistics, 54-64.](http://www.jstor.org/stable/2346439)
- [Jana, P. K., & Naik, A. (2009, December). An efficient minimum spanning tree based clustering algorithm. In Methods and Models in Computer Science, 2009. ICM2CS 2009. Proceeding of International Conference on (pp. 1-5). IEEE.](http://ieeexplore.ieee.org/abstract/document/5397966/)
- [Lecture 24 - Clustering and Hierarchical Clustering Old Kiwi - Rhea](https://www.projectrhea.org/rhea/index.php/Lecture_24_-_Clustering_and_Hierarchical_Clustering_Old_Kiwi)