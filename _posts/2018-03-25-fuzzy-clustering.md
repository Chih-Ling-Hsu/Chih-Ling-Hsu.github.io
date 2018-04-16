---
title: 'Fuzzy Clustering and Fuzzy k-Means'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Fuzzy clustering is the opposite of "Hard Clustering" (i.e., "Crispy Clustering").

For example, every data point $x$ would claim its percentage belongness to every cluster $C_i$ ($1 \leq i \leq K$ where $K$ is the number of clusters).   However, the report will be too long as in this type of clustering representation.

<!--more-->

**Let $u_{ij}$ be the probability of that $x_i$ belongs to Cluster $j$** and $\{V_j\}_{j=1}^K$ are the centers of the $K$ clusters.   Our objective is to minimize

$$
\sum_{i=1}^{N} \sum_{j=1}^K \big(u_{ij}\big)^q \|x_i-V_j\|^2
$$

which turns out to be TSSE (Total Sum of Squared Error) if $u_{ij}$ is defined as a binary number (0 or 1).

## Fuzzy k-means

Fuzzy k-means (FKM) is also called fuzzy C-means (FCM). This clustering method is stated as below:

**Step 1.** Guess $K$ initial cluster centers {$V_j$}$_{j=1}^K$ randomly given $N$ data points.
**Step 2.** Update membership coefficients {$u_{ij}$}$_{i=1,j=1}^{N, K}$

$$
u_{ij} = \frac{\bigg(\|x_i-V_j\|^{-2}\bigg)^{\frac{1}{q-1}}}{\sum_{l=1}^{K}\bigg(\|x_i-V_l\|^{-2}\bigg)^{\frac{1}{q-1}}}
\\
where~\sum_{i, j} u_{ij} = 1
$$

**Step 3.** Update centers.

$$
V_{j}^{(new)} = \frac{\sum_{i}(u_{ij})^q \cdot x_i}{\sum_{i}u_{ij}}
$$

**Step 4.** Repeat **Step 2.** and **Step 3.** until no big changes among $\{u_{ij}\}_{i=1,j=1}^{N, K}$


Note that it is common that $q>1$ (e.g., $q=2$) so that this iterative clustering will be easier to converge.   On the other hand, when $q \approx 1$ (i.e., $q=0.001, \frac{1}{q-1}=1000$), the clustering result would be similar to the behaviour of Hard Clustering.

### When Fuzzy k-means almost becomes Hard k-means


If $q=1^+$ (e.g., $q$=0.001), then FkM almost becomes Hard k-means.

For example, when $q$=0.001 and thus $\frac{1}{q-1}=1000$.   Assume there are 3 clusters currently, and 

- $\| x_i-v_1 \| = \frac{1}{\sqrt{50}}$
- $\| x_i-v_2 \| = \frac{1}{\sqrt{49}}$
- $\| x_i-v_3 \| = \frac{1}{\sqrt{48}}$


then

- $\| x_i-v_1 \|^{-2} = 50$
- $\| x_i-v_2 \|^{-2} = 49$
- $\| x_i-v_3 \|^{-2} = 48$

since $u_{ij}$ is positively relative to $\bigg[\|x_i-v_j\|^{-2}\bigg]^\frac{1}{q-1}$, thus

- $u_{i1} = \frac{50^{1000}}{50^{1000}+49^{1000}+48^{1000}} = \frac{1}{1+10^{-9}+10^{-18}} \approx 1$
- $u_{i2} = \frac{49^{1000}}{50^{1000}+49^{1000}+48^{1000}} = \frac{10^{-9}}{1+10^{-9}+10^{-18}} \approx 10^{-9}$
- $u_{i3} = \frac{48^{1000}}{50^{1000}+49^{1000}+48^{1000}} = \frac{10^{-18}}{1+10^{-9}+10^{-18}} \approx 10^{-18}$

The other disadvantage is that when $q=1^+$, FkM converges slowly.

## Reference

- [Chuang, K. S., Tzeng, H. L., Chen, S., Wu, J., & Chen, T. J. (2006). Fuzzy c-means clustering with spatial information for image segmentation. _computerized medical imaging and graphics_, _30_(1), 9-15.](https://www.sciencedirect.com/science/article/pii/S0895611105000923)
