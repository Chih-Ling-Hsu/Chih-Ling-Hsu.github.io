---
title: 'Cluster Validaty and Cluster Number Selection'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Numbers of cluster validity measures have been proposed to help us not only with the validation of our clustering result but also with cluster number selection.

For fuzzy clustering, we can optimize our clustering results with some validity measure such as Partition Coefficient, Partition Entropy, XB-index, and Overlaps Separation Measure.

For hard clustering, we can use measures such as DB index and Dunn index.

And for hierarchical clustering, we can select the best $k$ by stopping at the "Big Jump" of distances while performing agglomerative clustering.

<!--more-->

## Cluster Number Selection for Topic Fuzzy k-means


In *Pale Bezdek IEEE-T-Fuzzy-Systems* "On cluster validity for the fuzzy c-means model" (1995), the author proposed several index, including 

1. [PC (Partition Coefficient)](#pc-partition-coefficient)
2. [PE (Partition Entropy)](#pe-partition-entropy)

To see experiments on PC & PE, you can also refer to "Fuzzy clustering based on k-nearest neighbors Rule" (2001) in *Fuzzy Sets & Systems Vol 120*.

On the other hand, in *PAMI* "A Valid measure for fuzzy clustering" (1991), the authors used [XB-index](#xb-index-xie-beni) to decide the number of clusters.

Lastly, in this section we will also introduce [Overlaps Separation Measure (OS)](#os-overlaps-separation-measure), which is proposed in *PR Vol.37* "On cluster validity index for estimation of the optimal number of fuzzy clusters" (2004).


### PC (Partition Coefficient)

Given $n$ points and $k$ clusters, the definition of Partition Coefficient is
 
$$
PC = PC |_{[u], k} = \frac{\sum_{j = 1}^{k} \sum_{i=1}^{n} u_{i,j}^2}{n}
$$

with the assumption that

$$
\sum_{j = 1}^{k} u_{i,j}^2 \leq \sum_{j = 1}^{k} u_{i,j} = 1
$$

for any specific data point $i$.

If PC is large, then FkM is like hard clustering (well separated).   So with a graph of different PC over different $K$, we would **use $k$ with the largest PC**, which is at an apparent knee (or say, a peak).

### PE (Partition Entropy)

On the other hand, the definition of Partition Entropy is

$$
PE = PE |_{[u], k} = -\frac{1}{n} \sum_{j = 1}^{k} \sum_{i=1}^{n} u_{i,j} \times log(u_{i,j})
$$

If PE is small, then FkM is like hard clustering (well separated).   So we would **use $k$ with a small PE**, which is at an apparent knee (or say, a valley).

Note that if $k$ is close to $n$, PC decreases but PE increases.

One of the weaknesses of using PC & PE is that they only consider memberships.  That is, the structure of clusters are ignored.

Another weakness is that using PC or PE to find the best $k$ is not that obvious because it uses "knee" rather than Max or min.   So it is recommended to perform FkM using several different $q$.   If many result give the same knee, then this knee might be trustworthy.


### XB-index (Xie & Beni Index)

The compactness($C$) & separation ($S$) of clustering result can be expressed as

$$
XB = \frac{C}{S} = \frac{\sum_{j = 1}^{k} \sum_{i=1}^{n} u_{i,j}^2 \|x_i-y_j\|^2}{n \times \bigg({min}_{\tilde{j} \neq j}\|y_{\tilde{j}} - y_j\|\bigg)}
$$

given $n$ points and $k$ clusters.

A problem of implementation is that $S$ will have a yendency to eventually decrease when $k$ gets close to $n$.   So when XB-index is used, we need to determine $k_{max}$ in advanced ($k_{max} \ll n$) and then **choose $k$ that minimizes XB-index**.   (For example, let $k_{max} = n/3$, which very likely would not reach the starting point of decreasing tendency.)

### OS (Overlaps Separation Measure)


Given the number of cluster $k$ and the clustering result $R_k$, Overlaps Separation Measure is defined as

$$
OS(k) = \frac{Overlap(k, R_k)}{Sep(k, R_k)}
$$

and our objective is to find a $k$ that minimizes $OS(k)$.

For example, if we have 3000 data points ($n=3000$), let $q = 2$ in FkM, and the weight function for any point $x_i$ is defined as

- $w(x_i) = 0.1$ (`S`) if $0.8 \leq u_{ij}$ for a cluster $j$
- $w(x_i) = 0.4$ (`M`) if $0.7 \leq u_{ij} \leq 0.8$ for a cluster $j$
- $w(x_i) = 0.7$ (`L`) if $0.6 \leq u_{ij} \leq 0.7$ for a cluster $j$
- $w(x_i) = 1.0$ (`XL`) otherwise.

where `S`, `M`, `L` and `XL` represents different level of fuzziness for this point $x_i$.

The fuzzy score $f$ at a given membership degree $U_{threshold}$  between two fuzzy clusters $A$ and $B$ is defined as

$$
f(U_{threshold}: A, B) = \sum_{i =1}^{3000} \delta(x_i, U_{threshold}: A, B)
$$

where

$$
\delta(x_i, U_{threshold}: A, B)= 
\begin{cases}
w(x_i)& if~u_{iA} \geq U_{threshold}~\text{and}~u_{iB} \geq U_{threshold}\\
0.0& \text{otherwise}
\end{cases}
$$

And the overlap function depending a certain $U_{threshold}$ is

$$
Overlap(k, R_k) \vert_{U_{threshold}} = \frac{1}{k} \sum_{A,B \in R_k\\A \neq B} f(U_{threshold}: A, B)
$$

So our **overlap function** is defined as

$$
Overlap(k, R_k) = \sum_{U_{threshold} \in \{0.1, 0.25, ...\}} Overlap(k, R_k) \vert_{U_{threshold}}
$$

On the other hand, the **separation function** is defined as

$$
Sep(k, R_k) = 1 - \min_{A,B \in R_k\\A \neq B}\bigg(\max_{1 \leq i \leq n}\Big(\min(u_{iA}, u_{iB})\Big)\bigg)
$$

The interpretation is that if $\max\Big(\min(u_{iA}, u_{iB})\Big) = 0.35$, then there exists a data point $x_i$ who invest at least 35% in Cluster $A$ and also invest at least 35% in Cluster $B$, and thus $A$ and $B$ are not wellseparated.

So when $Sep(k, R_k)$ is large, then at least 2 out of $k$ clusters are separated because $\min\bigg(\max\Big(\min(u_{iA}, u_{iB})\Big)\bigg) \approx 0$


### Comparisons

In *PR vol 37* "On Cluster Validity index for estimation of the optimal number of fuzzy cluster" (2004), we can see that experients have been conducted on different indices for cluster number validity.   The experimental result is shown in the table below.

| data set | PC | PE | XB | CWB* | SV** | OS |
| - | - | - | - | - | - | - |
| X30 (k=3) | 3|3|3|3|3|3|
| BENSAID (k=3) | 3|**2**|3|**6**|**2**|3|
| man made overlap dots (k=3) |**2**|**2**|3|**5**|**2**|3|
| start field (k=8~10) |**2**|**2**|**6**|7|**2**|8|
| IRIS (k=3)|**2**|**2**|**2**|3|**4**|**2**|

*CWB (PR Vol.19, 1998) - M.R. Rezaee, B.P.F. Lelieveldt, J.H.C. Reiber "A new cluster validity index for the fuzzy c-mean"

**SV (IEICE Trans. Inform. Syst., 2001) - D.J. Kim, Y.W. Park, D.J. Park "A novel validity index for determination of the optimal number of clusters"


## Cluster Number Selection for Hard Clustering

Besides clustering validation measures used in Fuzzy Clustering, there are also several measures we can use in Hard Clustering.

### DB Index (1979)

DB Index, which is proposed by Davis-Bouldin in "a cluster separation measure" (1997), is an internal evaluation scheme for evaluating clustering algorithms.

$$
DB (k) = \frac{1}{k}\sum_{i=1}^{k} R_i^{(t)}
$$

where 


$$
R_i^{(t)} = \max_{j=\{1,2,...,k\}-\{i\}} \frac{S_i + S_j}{d_{i,j}^{(t)}}
$$

given the **scatter level $S_i$** for Cluster $C_i$ and the **distance $d_{i,j}^{(t)}$** between $C_i$ and $C_j$.

$$
S_i = \frac{1}{\|C_i\|} \sum_{x \in C_i} \|x - y_i\|
$$

$$
d_{i,j}^{(t)} = \|y_i - y_j\|_{L_t} = \bigg(\sum_{d=1}^{D} \|C_{i,d}-C_{j,d}\|^t\bigg)^{1/t}
$$

- $y_i$ is the center of $C_i$
- $D$ is the dimension of each data point

When we use DB-index as the objective function, we aim to get a clustering result that minimizes DB-index.

However, in *PR vol 30* "Cluster validation using graph theoreotic concepts" (1997), the authors proposed that we can use **DB index with Minimum Spanning Tree (MST)**.

$$
{DB}^{MST}(k) = \frac{1}{k}\sum_{i=1}^k R_i^{MST}
$$

where 

$$
R_{ij}^{MST} = \max_{j=\{1,2,...,k\}-\{i\}} \frac{S_i^{MST} + S_j^{MST}}{d_{i,j}}
$$

given $S_i^{MST}$ as the length of largest edge in the MST constructed using the cluster $C_i$.


### Dunn Index (1974)

The Dunn index (DI) (introduced by J. C. Dunn in 1974) is also a metric for evaluating clustering algorithms.

The objective us to maximize $D_k$

$$
D_k = \min_{1\leq i< j\leq k} \frac{\|\overrightarrow{C_i}-\overrightarrow{C_j}\|_{D_{mean}}}{\max_{l=1,2,...,k} S_{l}}
$$

given the **scatter level $S_l$** for Cluster $C_l$.

$$
S_l = \frac{1}{\|C_l\|} \sum_{x \in C_l} \|x - y_i\|
$$

However, DI is sensitive to noise.   So in *PR vol 30 p.847-857* (1997), Pal & Biswas **used Minimum Spanning Tree (MST) to improve DI** by replacing $D_k$ with $D_k^{MST}$.

$$
D_k^{MST} = \min_{1\leq i< j\leq k} \frac{\|\overrightarrow{C_i}-\overrightarrow{C_j}\|_{D_{mean}}}{\max_{l=1,2,...,k} S_{l}^{MST}}
$$

given $S_l^{MST}$ as the length of largest edge in the MST constructed using the cluster $C_l$.

### GAP (2001)

In *Journal of Royal Statistics Society B vol 63* "Estimating the number of clusters in a data set via the gap statistics" (2001), the authors proposed a new measure, Gap-Statistics (GAP).

First, let $C_i$ be the $i^{th}$ cluster, we define that

$$
W_k = \sum_{i = 1}^{k} \frac{1}{2}\frac{\sum_{x_p, x_q \in C_i} dist(\overrightarrow{x_p},\overrightarrow{x_q})}{\|C_i\|}
$$


where the distance function $dist(\cdot)$ can be any distance (e.g., euclidean distance, $dist(\overrightarrow{x_p},\overrightarrow{x_q})=\|\overrightarrow{x_p}-\overrightarrow{x_q}\|_{l2}^2$)


So if we have a data set $X$, we perform the following steps for $k \in$ { $k_{min}, ..., k_{max}$ }.

1. Split $X$ into $k$ groups and calculate $log(W_k)$
2. Generate several groups (say, 10 groups) artificial data $\tilde{X}^{(1)}, \tilde{X}^{(1)}, ...\tilde{X}^{(10)}$. 
3. Split $\tilde{X}$ into $k$ groups and calculate $log(\tilde{W}_k^{(i)})$ for each group $\tilde{X}^{(i)}$.
4. $Gap(k) = \Big(\frac{1}{10}\sum_{i=1}^{10}log(\tilde{W}_k^{(i)})\Big) - log(W_k)$
5. Let $\tilde{\sigma}_{k}$ be the standard deviation of {$log(\tilde{W}_k^{(1)}), log(\tilde{W}_k^{(2)}), ..., log(\tilde{W}_k^{(10)})$}.
6. Let $\tilde{\sigma}_{k} = \sqrt{1+\frac{1}{10}} \times \tilde{\sigma}_{k}$.


Follow these steps, we calculate Gap-Statistics in user-assigned range { $k_{min}, ..., k_{max}$ } and find smallest $k$ satisfying

$$
Gap(k) \geq Gap(k+1) - \tilde{\sigma}_{k+1}
$$

Note that the artificial data can be generated in two different ways.   The common way is to **sample a value $\tilde{x}_d$ from uniform distribution $U(\min_{x \in X}(x_{d}), \max_{x \in X}(x_{d}))$ in each dimension $d$**.   However in this way the hyper-rectangle where we sample the data from is align to the coordinates, which is usually not similar to the actual distribution.   The other way is to generate the hyper-rectangle that aligns to the principle compomnents in data $X$ and sample uniformly from this space.   In this way, the performance would be much better than the common way.

In experiments, Gap-Statistics outperformed hartigan (1975), KL (1985), CH (1974), and Silhouette (1990).


## References
- [On cluster validity index for estimation of the optimal number of fuzzy clusters](https://www.sciencedirect.com/science/article/pii/S0031320304001645#tbl1)
- [Davies–Bouldin index - Wikipedia](https://en.wikipedia.org/wiki/Davies%E2%80%93Bouldin_index)
- [A Valid measure for fuzzy clustering](http://cs.haifa.ac.il/hagit/courses/seminars/segmentation-III/Xie91.pdf)
- [Dunn index - Wikipedia
](https://en.wikipedia.org/wiki/Dunn_index)
