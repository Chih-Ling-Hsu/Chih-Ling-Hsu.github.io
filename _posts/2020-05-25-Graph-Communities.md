---
title: 'Community Detection in Graphs'
layout: post
tags:
  - Graph
  - Data-Mining
  - Clustering
category: Notes
mathjax: true
---

In a graph structure, what can be called a community?
The notion of community structure captures the tendency of nodes to be organized into groups and members within a community are more similar among each other.
Typically, a community in graphs/networks is a set of nodes with more/better/stronger connections between its members, than to the rest of the network. 
However, there is no widely accepted single definition.
It depends heavily on the application domain and the properies of the graph.


<!--more-->

<!-- 
- Label Propagation
- Clusters and Communities in Graphs
  - Evaluation Measures
- Spectral Graph Clustering
  - Spectra of affinity matrix
  - Spectra of graph Laplacian
  - Spectra of normalized Laplacian
  - Spectra of Modularity Matrix -->


## Finding Local Communities with Diffusion

To detect communities in a graph, we can utilize the idea of [Label Propagation](../../../2020/05/24/Label-Propagation).
More specifically, with label propagation, we define the the community of a given node $i$ in the graph $G$ by

$$
Y_{\text{diffusion}} = \sum_{k=0}^{\infty} \alpha^k M^k Y_0 = \alpha (M Y_0) + \alpha^2 (M^2 Y_0) + \alpha^3 (M^3 Y_0) + ...
$$

- $Y_0 \in \mathbb{R}^{n \times 1}$ is a binary vector whose $i$-th entry is set to $1$ while other entries are set to $0$
- $M =$ [random walk matrix](../../../2020/05/20/random-walks#random-walk-matrix)
- $\alpha$ is a constant providing decaying weight such that $\sum_{k=0}^{\infty} \alpha^k = 1$.

In $Y_{\text{diffusion}}$, the $j$-th entry indicates the probability of node $j$ belongs to the community of node $i$.

## Finding Local Communities with Label Spreading

Similar to the idea of diffusion, the label spreading algorithm can be described in the following steps:

**Step 1.** Initialize $Y_0$ such that entries of the **labelled nodes** are set to $1$ while other entries are set to $0$

**Step 2.** Label Spreading

$$
Y_{t+1} := \alpha L Y_t + (1-\alpha) Y_0
$$ 

- $Y_0 \in \mathbb{R}^{n \times 1}$ is a binary vector whose $i$-th entry is set to $1$ if node $i$ is a labelled node while other entries are set to $0$
- $L =$ [Normalized Laplacian Matrix](../../../2020/05/20/random-walks#graph-spectra--graph-laplacian)
- $\alpha$ is a constant, usually $0 < \alpha < 1$

**Step 3.**  Go back to **Step 2** until $Y_t$ converges

## Community Evaluation Measures

How would you define a "good community"?

A community corresponds to a group of nodes with more intra-cluster edges (the number of edges within a group) than inter-cluster edges (the number of edges between different groups).
In general, the evaluation measures for communites are listed below:

- Evaluation based on internal connectivity
- Evaluation based on external connectivity 
- Evaluation based on internal and external connectivity 
- Evaluation based on network model 

Suppose we are given n undirected graph $G = (V, E)$, $\|V\| = n$, $\|E\| = m$, and with the following notations,

- $S$ is the set of nodes in the cluster 
- $n_s = \|S\|$ is the number of nodes in $S$ 
- $m_s = \|{(u,v) \mid u \in S, v \in S}\|$ is the number of edges in $S$
- $c_s = \|{(u,v) \mid u \in S, v \notin S}\|$ is the number of edges on the boundary of $S$
- $f(S)$ represent the clustering quality of set $S$ 

we are going to explain each type of evaluation measure successively.
In general, by computing scores of each type of evaluation measure for ground-truth communities (Yang and Leskovec 2012), **Conductance** and **Triangle participation ratio** give the best performance in identifying 230 ground-truth communities from social, collaboration and information networks.

### Internal Connectivity 

- **Internal density**

$$
f(S) = \frac{m_s}{\frac{1}{2}n_s(n_s-1)}
$$

- **Average degree**: Average internal degree of nodes in $S$

$$
f(S) = \frac{2m_s}{n_s}
$$

- **Fraction over median degree (FOMD)**: fraction of nodes in $S$ with internal degree greater than $d_m$, where $d_m =$ median degree of the whole graph and $\text{deg}_S(u) = \|$ {$(u, v \mid v \in S)$} $\|$

$$
f(S) = \frac{\Big\|\Big\{u \mid u \in S, 
\text{deg}_S(u) > d_m \Big\}\Big\|}{n_s}
$$

- **Triangle participation ratio**: Fraction of nodes in $S$ that belong to a triangle, where $n_T(a, S) = \|$ {$(b, c) \mid (a, b) \in E, (a, c) \in E, (b, c) \in E, b \in S, c \in S$} $\|$ is the number of triangles in $S$ that contains a node $a$.

$$
f(S) = \frac{\Big\|\Big\{a \mid a \in S, 
n_T(a) \neq 0 \Big\}\Big\|}{n_s}
$$


### External Connectivity

- **Expansion**: The number of edges per node that point outside $S$

$$
f(S) = \frac{c_s}{n_s}
$$

- **Cut ratio**: Fraction of existing edges out of all possible edges leaving $S$

$$
f(S) = \frac{c_s}{n_s (n-n_s)}
$$



### External and Internal Connectivity

- **Conductance**: The fraction of total edge volume that points outside $S$

$$
f(S) = \frac{c_s}{2m_s + c_s}
$$

- **Normalized cut**

$$
f(S) = \frac{c_s}{2m_s + c_s} + \frac{c_s}{2(m - m_s) +c_s}
$$

- **Average out degree fraction**: The average fraction of edges of nodes in $S$ that point outside $S$

$$
f(S) = \frac{1}{n_s} \sum_{u \in S}\frac{\|\{(u, v) | (u, v) \in E, v \notin S\}\|}{\text{deg}(u)}
$$



### Evaluation based on network model

**Modularity Q**: Measures the difference between the number of edges in $S$ and the expected number of edges in a random graph model with the same degree sequence


$$
Q = \frac{1}{4m} s^TBs
$$

- $s$ is the column vector whose elements are the $s_i=1$ if node $i$ belongs to $S$ and $0$ otherwise
- $B_{ij} = A_{ij} - \frac{\text{deg}(i) \text{deg}(j)}{2m}$ for all pairs of vertices $(i, j)$, where $A$ is the adjacency matrix



## Spectral Graph Clustering for Community Detection

To perform spectral clustering in order to dectect communites in a graph, we first need to build up a Similarity Matrix / Affinity Matrix $A$ such that

- $A_{ij}$ is the similarity or affinity between node $i$ and $j$
- All values are non-negative $A_{ij} \geq 0, \forall i, j$
- Highest values would be on the diagonal of $A$
- $A$ is a symmetric matrix (assume our notion of similarity as symmetric)
- $A$ is a positive semi-definite matrix
  - $x^TAx \geq 0$ for every nonzero column vector $x$
  - $A$ has $n$ real non-negative eigenvalues 


However, this produces a dense matrix instead of a sprase matrix (e.g., an adjacency matrix is usually sparse) that is not convenient for computation.
As a result, it is suggested to make $A$ a sparse matrix by either doing thresholding (set entries that have values lower than a threshold to zero) or keeping only the $k$ largest value for each node.

With this similarity matrix $A$, the following Spectral Partitioning Algorithms are proposed:

| Spectral Partitioning | Optimized measure | Look at the eigenvector(s) associated to ... |
| - | - | - |
| Spectra of $A$ | [Average degree/weight](./#internal-connectivity) | the largest eigenvalues
| Spectra of $D-A$ (Laplacian) | [Ratio cut](./#external-connectivity) | the smallest eigenvalues
| Spectra of $D^{-\frac{1}{2}}A D^{-\frac{1}{2}}$ (normalized Laplacian) | [Conductance](./#external-and-internal-connectivity) | the largest eigenvalues
| Spectra of $B$ (Modularity Matrix) | [Modularity](./evaluation-based-on-network-model) | the largest eigenvalues





<!-- 
| Spectral Partitioning | Spectra of $A$ | Spectra of $D-A$ (Laplacian) | Spectra of $D^{-\frac{1}{2}}A D^{-\frac{1}{2}}$ (normalized Laplacian) | Spectra of $B$ (Modularity Matrix) |
| - | - | - | - | - |
| Optimized measure | [Average degree/weight](./#internal-connectivity) | [Ratio cut](./#external-connectivity) | [Conductance](./#external-and-internal-connectivity) | [Modularity](./evaluation-based-on-network-model) |
| Look at the eigenvector(s) associated to ... | the largest eigenvalues | the $2^{nd}$ smallest eigenvalue (_Fiedler vector_) | the largest eigenvalues | the largest eigenvalues -->


### $2$-way Spectral Partitioning

Take spectra of Laplacian as an example, the procedure of spectral partitioning on a graph $G$ is described below:

**Step 1.** **Pre-processing**: Build Laplacian matrix $L$ of the graph

**Step 2.** **Decomposition**: Find eigenvalues $\lambda$ and eigenvectors $x$ of the matrix $L$

**Step 3.** **Grouping**: Sort the _Fiedler vector_ $x_2$ and identify 2 clusters by splitting the sorted vector into two parts 
- _Fiedler vector_: the eigenvector associated with the $2^{nd}$ smallest eigenvalue ($\lambda_1 = 0$ and $\lambda_2 \neq 0$ is $G$ is connected)
- Splitting point: naïve approach is to split at $0$ or median value


![](https://imgur.com/UNP3l0t.png)

For example, using the Fiedler vector $x_2$, we can partition the graph into 2 clusters by setting spliting point at $0$.

### $k$-Way Spectral Clustering

So far only bisection is considered, but the next question is: **How do we partition a graph into k clusters?**
There are two basic approaches:

- **Recursive bi-partitioning**: Recursively apply bi-partitioning algorithm in a hierarchical divisive manner.   However, this approach is inefficient and also unstable
- **Cluster multiple eigenvectors**: Build a reduced space from multiple eigenvectors and perform clustering in this space.

The second approach is more commonly used, especially in recent papers.
By combining the eigenvectors with top $k$ eigenvalues, we construct a $n \times k$ matrix.
This process can be seen as transforming $n$ points in $n$-dimensional space to $n$ points in $k$-dimensional space, and we expect that in this $k$-dim eigenspace the data points are more distinct and easier to separate (e.g., check $x_2$ in the above figure).
Therefore, we can then perform simple clustering (e.g., k-means) to find $k$ clusters in this eigenspace.

So now the next question is: **How to select $k$?**
The **eigengap heuristic** suggests that to generate stable clustering result, the number of clusters is usually given by the value $k$ that maximizes the [eigengap](../../../2020/05/20/random-walks#spectral-gap):

$$
k = \arg \max_k \Delta_k = \arg \max_k \|\lambda_k - \lambda_{k+1}\|
$$

Why? **As long as the eigenvalue $\lambda_i$ is low, it assumes that there is little cuts for the partitioning in the associated eigenvector $x_i$**.
Hence, if all eigenvalues $\lambda_1, ..., \lambda_k$ are very small but $\lambda_{k+1}$ is relatively large, then it is assumed that to partition one more cluster, the number of cuts required is suddenly grown a lot larger.
Therefore, the larger this eigengap is, the closer the eigenvectors of the ideal case and hence the better spectral clustering works.


## References

- Jure Leskovec, A. Rajaraman and J. D. Ullman, "Mining of massive datasets"  Cambridge University Press, 2012
- David Easley and Jon Kleinberg "Networks, Crowds, and Markets: Reasoning About a Highly Connected World" (2010).
- John Hopcroft and Ravindran Kannan "Foundations of Data Science" (2013).
- Andersen et al. Local Graph Partitioning using PageRank Vectors, 2006
- Kloster et al. Heat Kernel Based Community Detection, 2016
- Zhou et al. Learning with local and global consistency, NIPS 2004
- Yang, J., & Leskovec, J. (2012, December). Community-affiliation graph model for overlapping network community detection. In 2012 IEEE 12th international conference on data mining (pp. 1170-1175). IEEE.

