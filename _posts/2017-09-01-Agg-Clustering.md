---
title: 'Data Mining - Agglomerative Clustering'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Agglomerative clustering **start with the points as individual clusters**.   At each step, it **merges the closest pair of clusters until only one cluster (or k clusters) left**.

<!--more-->

```python
Compute the proximity matrix
Let each data point be a cluster
Repeat
	Merge the two closest clusters
	Update the proximity matrix
Until only a single cluster remains
```

Key operation is the computation of the proximity of two clusters. So, the question is

> How to define inter-cluster similarity?


## MIN

<table>
    <tr><td><img src="https://i.imgur.com/fTWaf4Z.png"></td><td><img src="https://i.imgur.com/f51UUhm.png"></td></tr>
</table>

Min similarity assigns

$$
d(A, B) = min(dist(A[i], B[j]))
$$

for all points $i$ in cluster $A$ and $j$ in cluster $B$.


- Strengths:
    - Can handle non-elliptical shapes (_Chainning Effect of "Min" Similarity_)
- Limitations:
    - Sensitive to noise and outliers
    - Any point in sparse area would be isolated


## MAX

<table>
    <tr><td><img src="https://i.imgur.com/DntS4Cs.png"></td><td><img src="https://i.imgur.com/TqZF9wx.png"></td></tr>
</table>

- Strengths:
    - Less susceptible to noise and outliers
- Limitations:
    - Tends to break large clusters
    - Biased towards globular clusters


## Group Average

Group Average compromise between Single and Complete Link (that is, MIN and MAX).

<table>
    <tr><td><img src="https://i.imgur.com/KFOYo0p.png"></td><td><img src="https://i.imgur.com/9OP81iA.png"></td></tr>
</table>

- Strengths:
    - Less susceptible to noise and outliers
- Limitations:
    - Biased towards globular clusters


## Distance Between Centroids

When you merge two clusters $A$ & $B$ to get a new cluster $R = A \cup B$, the distance between centroids will be

$$
D(R, Q)^2 =\frac{\|A\|}{\|R\|}D(A,Q)^2 + \frac{\|B\|}{\|R\|}D(B,Q)^2 - \frac{\|A\|}{\|R\|}\frac{\|B\|}{\|R\|}D(A,B)^2
$$

given $R$ and other cluster $Q$ ($Q \neq A; Q \neq B$).

## Other methods driven by an objective function

For example, Ward’s Method uses squared error.   In Ward's Method, similarity of two clusters is based on the increase in squared error when two clusters are merged.   It is similar to group average if distance between points is distance squared.

- Strengths:
    - Less susceptible to noise and outliers
    - Hierarchical analogue of K-means (Can be used to initialize K-means)
- Limitations:
    - Biased towards globular clusters

## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)