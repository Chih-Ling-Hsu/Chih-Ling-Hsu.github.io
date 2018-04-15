---
title: 'Use Annealing Method to Avoid Local Minimum'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Local minima are still an important unsolved problem for artificial potential field approaches.   In order to overcome this problem, annealing methods are proposed to prevent being trapped in a local minimum point, and allows the computation to continue its trajectory towards the final destination.

<!--more-->

## Deterministic Annealing (DA)

Let $k$ be the number of the clusters, $m$ be the number of iterations, $y_i$ be the $i^{th}$ cluster representative, the membership $u_{x,j}$ is the probability of that $x$ belongs to Cluster $j$

$$
u_{x,j}=u_{x,j}\vert_{\beta}
=\frac{e^{-\beta\|x-y_i\|^2}}{\sum_{i=1}^{k}e^{-\beta\|x-y_i\|^2}}
$$

where

$$
\beta = \frac{1}{T}
\\
\beta \rightarrow 0 \leftrightarrow high~temperature \leftrightarrow High~Fuzziness
\\
\beta \rightarrow T \leftrightarrow low~temperature \leftrightarrow Hard~Clustering
$$

And our objective is to minimize

$$
\sum_x\sum_{j=1}^k u_{x,j}\|x-y_j\|^2
$$

where

$$
y_j = \frac{\sum_x u_{x,j} \times x}{\sum_x u_{x,j}}
\\
\therefore y_j =  \frac{\sum_x x \times \frac{e^{-\beta\|x-y_i\|^2}}{\sum_{i=1}^{k}e^{-\beta\|x-y_i\|^2}}}{\sum_x \frac{e^{-\beta\|x-y_i\|^2}}{\sum_{i=1}^{k}e^{-\beta\|x-y_i\|^2}}}
$$


Since $y_j$ appears in both left & right side, we can only use iterations.


$$
y_j^{(m+1)} = \frac{\sum_x x \times u_{x,j}^{(m)}}{\sum_x u_{x,j}^{(m)}}
$$

### Iterative Algorithm of DA

**Step 0.** Let $\beta \approx 0^+$ (e.g., $\beta = 10^{-4}$), $y_j\vert_{j=1}^k$ be arbitrary.

**Step 1.** Use the following formula to update $y_j\vert_{j=1}^k$ until $y_j\vert_{j=1}^k$ are stable.

$$
y_j^{(m+1)} = \frac{\sum_x x \times u_{x,j}^{(m)}}{\sum_x u_{x,j}^{(m)}}
$$

**Step 2.** Increase $\beta$ (e.g., $\beta_{new} = 1.1 \times \beta_{old}$)

**Step 3.** Go to **Step 1.** if $\beta_{new} < threshold$

The experimental results show that Deterministic Annealing (DA) is better than k-means (since it avoids local minimums) but time-consuming.

## Simulated Annealing (SA)

Simulated annealing (SA) is a probabilistic technique for approximating the global optimum of a given function.   Specifically, it is a metaheuristic to approximate global optimization in a large search space. 

Simulated annealing (SA) is often used when the search space is discrete (e.g., all tours that visit a given set of cities).   For problems where finding an approximate global optimum is more important than finding a precise local optimum in a fixed amount of time, simulated annealing may be preferable to alternatives such as gradient descent.

The objective of Simulated Annealing (SA) is 

$$
\min_P E(P)
\\
P \overset{\delta}{\longrightarrow} P'
$$

- $E$: any kind of error (e.g., TSSE)
- $P$: a partition of data
- $\delta$: a small change of old partition $P$

### Iterative Algorithm of SA

**Step 0.** Let $T = T_{initial}$, $\alpha$ be small (e.g., $\alpha$=0.9), and $P$ be arbitrary.

**Step 1.** For $i \leftarrow 1~to~max\_iteration$ do

1. $P' = \delta(P)$
2. $\triangle = E(P')-E(P)$
3. if $\triangle<0$, then accept $P'$
4. if $\triangle>0$, then accept $P'$ only when $e^{\frac{- \triangle}{T}} \geq$ random number

If $\triangle>0$, then $P'$ is a bad partition.   Notice that if $T$ is large, then it is easier to accept bad partition.   That is, if $T \approx \infty$, then $e^{\frac{- \triangle}{T}} \approx e^{-0^+}$ (e.g., $e^{-0.001}$, which is very close to 0.999999).

**Step 2.** Use $T \leftarrow \alpha \times T$ to lower down the temperature.

**Step 3.** Go to **Step 1.** if $T > T_{final}$


## References

- [Deterministic annealing for clustering: Tutorial and computational aspects](http://ieeexplore.ieee.org/document/7171176/)
- [Optimization by Simulated Annealing](http://science.sciencemag.org/content/220/4598/671)
- [Simulated annealing - Wikipedia](https://en.wikipedia.org/wiki/Simulated_annealing)