---
title: 'Divisive Method for Hierarchical Clustering'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Divisive clustering **starts with one, all-inclusive cluster**.   At each step, it **splits a cluster until each cluster contains a point** (or there are k clusters).

<!--more-->


Building MST (Minimum Spanning Tree) is a method for constructing hierarchy of clusters.

It starts with a tree that consists of any point.   In successive steps, look for the closest pair of points $(p, q)$  such that $p$ is in the current tree but $q$ is not.   With this closest pair of points $(p, q)$, add $q$ to the tree and put an edge between $p$ and $q$.

The procedure of constructing hierarchy of clusters using MST would be as follows:

```python
Compute a MST for the proximity graph
repeat
    Split a cluster by breaking the link of the largest distance (smallest similarity).
until Only singleton clusters remain
```

## Illustrative Example

# Divisive Clustering

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


## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)