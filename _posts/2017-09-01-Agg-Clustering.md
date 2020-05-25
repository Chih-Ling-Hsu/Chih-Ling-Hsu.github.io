---
title: 'Agglomerative Method for Hierarchical Clustering'
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


## MIN similarity

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


## MAX similarity

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

given $R$ and an another cluster $Q$ ($Q \neq A; Q \neq B$).

## Other methods driven by an objective function

For example, Ward’s Method uses squared error.   In Ward's Method, similarity of two clusters is based on the increase in squared error when two clusters are merged.   It is similar to group average if distance between points is distance squared.

- Strengths:
    - Less susceptible to noise and outliers
    - Hierarchical analogue of K-means (Can be used to initialize K-means)
- Limitations:
    - Biased towards globular clusters

## Using Hierarchical Agglomerative Clustering to Deal with Noisy Image

Suppose the matrix below is a block in a gray-scale image.


$$
\begin{bmatrix}
22 & 33 & 44\\
239 & x & 235 \\
238 & 237 & 236
\end{bmatrix}
$$

If a pixel whose gray value is $x$, then how to check if $x$ is a noise and how to impute it?


**Step 1. Hierarchical agglomerative clustering on 8 neighbors**

Suppose we have defined a threshold called "Big Jump".   We can use it to determine whether an agglomerative merge should be done or not.   The number of clusters is determined automatically hence.

For example, in this case we will obtain 2 clusters $C_1$ and $C_2$ (along with their clsuter centers $y_1$ and $y_2$) after performing hierarchical clustering on all 8 neighbors of $x$.

- $C_1$ = { $22,33,44$ }, $y_1 = 33$
- $C_2$ = { $235, 236, 237, 238, 239$ }, $y_2 = 237$

$C_1$ and $C_2$ will not be merged since the distance between them is too large.

**Step 2. Is $x$ a noise? How far away is $x$ from $y_1$? from $y_2$?**

$$
\|x-y_1\| < \theta~~~\text{or}~~~\|x-y_2\| < \theta
\\
\rightarrow x \notin Noise
\\
~
\\
\theta~\text{: noise-tolerance threshold}
$$


**Step 3. If $x$ is a noise, then replace $x$ by $y_1$ or $y_2$.**

For example, in this case we replace $x$ with $y_2$ since $\|C_2\| > \|C_1\|$.

If more than 1 cluster has the largest number of members, use the one with more $N$/$E$/$S$/$W$ member(s).

$$
\begin{bmatrix}
22 & (N=33)  & 44\\
(W=239) & x & (E=235) \\
238 & (S=237) & 236
\end{bmatrix}
$$

### Experimental Results and Discussions

Say that we want to do experiemnts with this approach with the experiment settings as

- percentage of contanminated pixels: 10%, 25%, or 50%.
- standard deviation of the contanmination distribution: 20, 40, or 80.
- The agglomerative jump threshold: 15 or 25.
- Noise-tolerance threshold: 20, 36.

<!--| Factor | Level | Average RMS |
| - | - | - |
|  |  | 10.71
|  |  | 14.82-->

And from the experimental results (which you can find in the paper), it is concluded that the factor **"noise-tolerance"** is significant and the agglomerative jump is not so significant.

On the other hand, if k-means is used instead of agglomerative clustering, then the imputed image looks bad; if median filter (always use the median of neighbors to replace $x$) is used then the imputed image looks smooth but blurry.

Another benefit is that this algorithm can also be used for edge detection.   However, it does not perform well if there exists 2 adjacent noises with similar values.   In this case, the algorithm will interpret them as a cluster and leave them untouched.

## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [Abreu, E., Lightstone, M., Mitra, S. K., & Arakawa, K. (1996). A new efficient approach for the removal of impulse noise from highly corrupted images. IEEE transactions on image processing, 5(6), 1012-1025.](http://ieeexplore.ieee.org/abstract/document/503916/)