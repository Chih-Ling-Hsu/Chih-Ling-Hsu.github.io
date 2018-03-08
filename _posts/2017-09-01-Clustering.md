---
title: 'Data Mining - Basic Cluster Analysis'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

>   “The validation of clustering structures is the most difficult and frustrating part of cluster analysis. 
   Without a strong effort in this direction, cluster analysis will remain a black art accessible only to those true believers who have experience and great courage.”
> 
> -- _Algorithms for Clustering Data_, Jain and Dubes


Clustering Analysis is finding groups of objects such that the objects in a group will be similar (or related) to one another and different from (or unrelated to) the objects in other groups such that

- Intra-cluster distances are minimized
- Inter-cluster distances are maximized

<!--more-->

Clustering can be classified to **partitional clustering** and **hierarchical clustering**.   Partitional clustering creates a division data objects into non-overlapping subsets (clusters) such that each data object is in exactly one subset; Hierarchical clustering creates a set of nested clusters organized as a hierarchical tree .

## Types of Clusters

- **Well-separated clusters**: A cluster is a set of points such that any point in a cluster is closer (or more similar) to **every** other point in the cluster than to any point not in the cluster. 

- **Center-based clusters**: A cluster is a set of objects such that an object in a cluster is closer (more similar) to the “center” of a cluster, than to the center of any other cluster

- **Contiguous clusters** (Nearest neighbor or Transitive): A cluster is a set of points such that a point in a cluster is closer (or more similar) to **one or more** other points in the cluster than to any point not in the cluster.

- **Density-based clusters**: A cluster is a dense region of points, which is separated by low-density regions, from other regions of high density. Used when the clusters are irregular or intertwined, and when noise and outliers are present. 

- **Property or Conceptual**: Finds clusters that share some common property or represent a particular concept.

- **Described by an Objective Function**: Finds clusters that minimize or maximize an objective function. It maps the clustering problem to a different domain and solve a related problem in that domain. Therefore **clustering is equivalent to breaking the graph into connected components, one for each cluster**, and we want to minimize the edge weight between clusters and maximize the edge weight within clusters 


## K-means and its variants

K-means is a partitional clustering approach where number of clusters, $K$, must be specified.   Each cluster is associated with a centroid (center point) and each point is assigned to the cluster with the closest centroid.

The basic algorithm is decribed as follows:

```python
Choose initial K-points, called the centroids.
repeat
    Form K clusters by assigning all points to the closest centroids.
    Recompute the centroid of each cluster.
until The centroids do not change
```

Note that

- Initial centroids are often chosen randomly.
- The centroid is typically the mean of the points in the cluster.
- Except for number of clusters, the **measure of "Closeness"** should also be specified. (Usually measured by *Euclidean distance*, *cosine similarity*, *orcorrelation*).
- To evaluate the clustering performance, the most common measure is **Sum of Squared Error (SSE)**

$$
SSE = \sum_{i=1}^K\sum_{x \in C_i} dist^2(m_i, x)
\\
where~x~is~a~data~point~in~C_i~and
\\
m_i~is~the~centroid~of~C_i
$$


The advantages of K-means are

1. simple
2. math-supported
3. small TSSE (Total Sum of Squared Error)
4. not slow (given a good initial)

On the contrary, the weakness of K-means are

1. depends on initials
2. outliers (noises) will affect to results


### Limitations of K-means

K-means has problems when clusters are of 
- differing Sizes
- differing Densities
- Non-globular shapes

K-means also has problems when the data contains outliers.

![](https://i.imgur.com/HJvSvUP.png)
![](https://i.imgur.com/mnPsnOJ.png)
![](https://i.imgur.com/jZWfvEE.png)

### K-means always converge

**Reason 1.** Cluster $n$ points into $K$ clusters $\rightarrow$ finite number of possible clustering results.
**Reason 2.** Each iterstion in k-means causes the lower TSSE (Total Sum of Squared Error).   Hence, there's never a loop (cycle).

Combine **Reason 1. & 2.**, the hypothesis can be proved.

### Handling Empty Clusters

Empty clusters can be obtained if no points are allocated to a cluster during the assignment step.   If this happens, we need to choose a replacement centroid otherwise SSE would be larger than neccessary.

- Choose the point that contributes most to SSE
- Choose a point from the cluster with the highest SSE
- If there are several empty clusters, the above can be repeated several times.

### Initial Centroids Problem

Poor **initial centroids** affect a lot to the clustering.   The followings are the techniques to address this problem:

- Multiple runs
- Sample and use hierarchical clustering to determine initial centroids
- Select more than K initial centroids and then select among these initial centroids
- [Cluster Center Initialization Algorithms](../../../2017/09/01/Cluster-Centroid-Init)
- [Postprocessing](#reduce-the-sse-using-post-processing)
- [Bisecting K-means](#bisecting-k-means)

### Reduce the SSE Using Post-processing

- **Split a cluster**: Split ‘loose’ clusters, i.e., clusters with relatively high SSE
- **Disperse a cluster**: Eliminate small clusters that may represent outliers
- **Merge two clusters**: Merge clusters that are ‘close’ and that have relatively low SSE

### Bisecting K-means

Bisecting K-means algorithm is a variant of K-means that can produce a partitional or a hierarchical clustering.

```python
Initialize list_of_clusters consisting of a cluster containing all points
repeat
    Select a cluster from list_of_clusters
    for i=1 to number_of_iteration:
        Bisect the selected cluster using basic K-means
    Add the two clusters from the bisection with the lowaest SSE to list_of_clusters
until list_of_clusters contains K clusters
```



## Hierarchical clustering

Hierarchical clustering produces a set of nested clusters organized as a hierarchical tree, which can be visualized as a dendrogram.
![](https://i.imgur.com/xM5Ov9T.png)

The advantages of hierarchical clustering is that it does not have to assume any particular number of clusters since **any desired number of clusters can be obtained by ‘cutting’ the dendogram at the proper level**.   Also, the clusters may correspond to meaningful taxonomies (e.g., animal kingdom, phylogeny reconstruction, …)

However, once a decision is made to combine two clusters / divide a cluster, it cannot be undone. Also, no objective function is directly minimized using hierarchical clustering.

There are two methods to do hierarchical clustering:

1. [**Agglomerative Clustering**](../../../2017/09/01/Agg-Clustering)
    - Start with the points as individual clusters.   
    - At each step, it merges the closest pair of clusters until only one cluster (or k clusters) left.
2. [**Divisive Clustering**](../../../2017/09/01/Divisive-Clustering)
    - Starts with one, all-inclusive cluster.   
    - At each step, it splits a cluster until each cluster contains a point (or there are k clusters).

In the process of Agglomerative Clustering, when you merge two clusters $A$ & $B$ to get a new cluster $R = A \cup B$, how do you compute the distance
$$
D(R, Q)
$$
Given $R$ and other cluster $Q$ ($Q \neq A; Q \neq B$)?

If each data point is of 16 dimensions and we use Euclidean distance, then

- 16 substractions
- 15 additions
- 16 multiplications
- and a square root

are needed.   So the number of operations is _dimension dependent_.

In other word, you have a $n$-by-$n$ distance matrix initially.   In each step, you merge two clusters (e.g., $A$ & $B$) to get a new cluster $R$.   The number of clusters decrease by 1.   We thus update the distance metrics with certain formulas:
- If $D = D_{min}$ (single affinity), 
$$
D(R, Q) = min\{D(A,Q), D(B,Q)\}
$$
- If $D = D_{max}$ (complete affinity), 
$$
D(R, Q) = Max\{D(A,Q), D(B,Q)\}
$$
- If $D = D_{avg}$ (group average), 
$$
D(R, Q) = \frac{1}{\|R\|\|Q\|}\sum_{r 
\in R, q \in Q}\|r-q\|
\\= \frac{1}{\|R\|\|Q\|}\left[\sum_{r 
\in A, q \in Q}\|r-q\|+\sum_{r 
\in B, q \in Q}\|r-q\|\right]
\\= \frac{1}{\|R\|}\left[\frac{\|A\|}{\|A\|\|Q\|}\sum_{r 
\in A, q \in Q}\|r-q\|+\frac{\|B\|}{\|B\|\|Q\|}\sum_{r 
\in B, q \in Q}\|r-q\|\right]
\\= \frac{\|A\|}{\|R\|}D(A,Q) + \frac{\|B\|}{\|R\|}D(B,Q)
$$
- If $D = D_{center}$, 
$$
\because A \cup B = R ~ \therefore \|A\|\bar{a}+\|B\|\bar{b} = \|R\|\bar{r}
\\
\bar{r} = \frac{\|A\|}{\|R\|}\bar{a}+\frac{\|B\|}{\|R\|}\bar{b} = \bar{a}+\frac{\|B\|}{\|R\|}(\bar{b}-\bar{a})
\\
(\because \frac{\|A\|}{\|R\|}+\frac{\|B\|}{\|R\|} = 1)
\\
\bar{r}~is~on~the~line~connecting~\bar{a}~and~\bar{b}
D_{AQ}=\|\bar{r}-\bar{a}\|
\\
D(R, Q)^2 =\frac{\|A\|}{\|R\|}D(A,Q)^2 + \frac{\|B\|}{\|R\|}D(B,Q)^2 - \frac{\|A\|}{\|R\|}\frac{\|B\|}{\|R\|}D(A,B)^2
$$

At the first step of Hierarchical methods to combine/divide clusters, 

- [**Agglomerative method**](../../../2017/09/01/Agg-Clustering) has $C^n_2=\frac{n(n-1)}{2}$ possible choices. ($\Theta(n^2)$)
- [**Divisive method**](../../../2017/09/01/Divisive-Clustering) has $\frac{(2^n-2)}{2}=2^{n-1}-1$ possible choices. ($\Theta(2^n)$)

given $n$ points.   Note that the computation cost of Divisive method will be higher than that of Agglomerative method when $n \geq 5$.


## Density-based clustering

DBSCAN is a density-based algorithm, where $density$ = number of points within a specified radius ($Eps$), given a specified number of points ($MinPts$) as the threshold.

**Core Point** - A point is a core point if it has more than a specified number of points ($MinPts$) within $Eps$.   These are points that are at the interior of a cluster.

**Border Point** - A border point has fewer than $MinPts$ within $Eps$, but is in the neighborhood of a core point

**Noise Point** - A noise point is any point that is not a core point or a border point. 

![](https://i.imgur.com/q9CNDv2.png)

```C
DBSCAN(DB, dist, eps, minPts) {
   C = 0                                              /* Cluster counter */
   for each point P in database DB {
      if label(P) ≠ undefined then continue           /* Previously processed in inner loop */
      Neighbors N = RangeQuery(DB, dist, P, eps)      /* Find neighbors */
      if |N| < minPts then {                          /* Density check */
         label(P) = Noise                             /* Label as Noise */
         continue
      }
      C = C + 1                                       /* next cluster label */
      label(P) = C                                    /* Label initial point */
      Seed set S = N \ {P}                            /* Neighbors to expand */
      for each point Q in S {                         /* Process every seed point */
         if label(Q) = Noise then label(Q) = C        /* Change Noise to border point */
         if label(Q) ≠ undefined then continue        /* Previously processed */
         label(Q) = C                                 /* Label neighbor */
         Neighbors N = RangeQuery(DB, dist, Q, eps)   /* Find neighbors */
         if |N| ≥ minPts then {                       /* Density check */
            S = S ∪ N                                 /* Add new neighbors to seed set */
         }
      }
   }
}

RangeQuery(DB, dist, Q, eps) {
   Neighbors = empty list
   for each point Q in database DB {                  /* Scan all points in the database */
      if dist(P, Q) ≤ eps then {                      /* Compute distance and check epsilon */
         Neighbors = Neighbors ∪ {Q}                  /* Add to result */
      }
   }
   return Neighbors
}
```

DBSCAN is resistant to noise and can handle clusters of different shapes and sizes.   However, it does not work well if the data is high-dimensional or with varying densities.

### Selection of DBSCAN Parameters

If clusters in the data are in of similar densities, then for points in a cluster, their $k^{th}$ nearest neighbors are at roughly the same distance.   That is, noise points have the $k^{th}$ nearest neighbor at farther distance.

So, for each point, we can plot sorted distance of the point to its $k^{th}$ nearest neighbor, given $k$ as its $MinPts$.

![](https://i.imgur.com/TQdahjm.png)

With references to these plots, we can determine the best $MinPts$ and $Eps$.

## Cluster Validity

For cluster analysis, the analogous question is how to evaluate the “goodness” of the resulting clusters?   However, 

> “Clusters are in the eye of the beholder”

Then why do we want to evaluate them?

- Determining the clustering tendency of a set of data, i.e., distinguishing whether non-random structure actually exists in the data. 
- Comparing the results of a cluster analysis to externally known results, e.g., to externally given class labels.
- Evaluating how well the results of a cluster analysis fit the data without reference to external information. That is, use only the data
- Comparing the results of two different sets of cluster analysis to determine which is better.
- Determining the ‘correct’ number of clusters.

We can further distinguish whether we want to evaluate the **entire clustering** or just **individual clusters**.

However, no matter what the measure is, we need a framework to interpret.   For example, if our measure of evaluation has the value, 10, is that good, fair, or poor?

Statistics provide a framework for cluster validity:   **The more “atypical” a clustering result is, the more likely it represents valid structure in the data**.   We can compare the values of an index that result from random data or clusterings to those of a clustering result.   If the value of the index is unlikely, then the cluster results are valid.

![](https://i.imgur.com/FEx6vHS.png)

### Internel Measures

Internal Index is used to measure the goodness of a clustering structure without respect to external information. (e.g., Sum of Squared Error, SSE)

SSE is good for comparing two clusterings or two clusters (average SSE).   It can also be used to estimate the number of clusters.

![](https://i.imgur.com/QMjVaNH.png)

Note that 

$$
TSS = WSS + BSS
\\
where~TSS~is~Total~Sum~of~Squres
$$


**1. Cluster Cohesion**

Cohesion is measured by the **within cluster sum of squares (WSS)**, which shows how closely related are objects in a cluster.

$$
WSS = \sum_i\sum_{x \in C_i}(x-m_i)^2
\\
where~C_i~is~the~i^{th}~cluster,
\\
m_i~is~the~centroid~of~C_i
$$

**2. Cluster Separation**

Separation is measured by the **between cluster sum of squares (BSS)**, which shows how distinct or well-separated a cluster is from other clusters.

$$
BSS = \sum_i \left| C_i \right| (m-m_i)^2
\\
where~C_i~is~the~i^{th}~cluster,
\\
m_i~is~the~centroid~of~C_i,
\\
m~is~the~mean~of~all~points
$$

**3. Silhouette Coefficient**

Silhouette Coefficient combine ideas of both cohesion and separation, but for individual points, as well as clusters and clusterings.

Let $a(i)$ be the average dissimilarity of $i$ with all other data within the same cluster.   We can interpret $a(i)$ as **how well $i$ is assigned to its cluster** (the smaller the value, the better the assignment).

Let $b(i)$ be the lowest average dissimilarity of $i$ to any other cluster, of which $i$ is not a member. The cluster with this **lowest average dissimilarity is said to be the "neighbouring cluster" of $i$** because it is the next best fit cluster for point $i$. 

$$
a(i) = \frac{1}{\left|C\right|} \sum_{j \in C} dist(i, j)
\\
b(i) = min_{C' \neq C}(\frac{1}{\left| C' \right|} \sum_{j \in C'} dist(i, j))
\\
where~C~is~the~cluster~of~point~i
$$

We now define a silhouette coefficient of point $i$ as

$$
s(i) = \frac{b(i) - a(i)}{\max(a(i),b(i))}
$$

Which can be also written as:

$$
s(i) = \begin{cases}
  1-a(i)/b(i), & \mbox{if } a(i) < b(i) \\
  0,  & \mbox{if } a(i) = b(i) \\
  b(i)/a(i)-1, & \mbox{if } a(i) > b(i) \\
\end{cases}
$$

It is clear that the value of a silhouette coefficient is between -1 to 1 (typically between 0 and 1), and the closer it is to 1 the better.


### External Measures

External Index is used to measure the extent to which cluster labels match externally supplied class labels. (e.g., entropy)

### Relative Measures

Relative Index is used to compare two different clusterings or clusters.   It is often an external or internal index is used for this function, e.g., SSE or entropy.


## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [Wikipedia - DBSCAN Algorithm](https://en.wikipedia.org/wiki/DBSCAN)
- [Wikipedia - Silhouette (clustering)](https://en.wikipedia.org/wiki/Silhouette_(clustering))
- [Khan, S. S., & Ahmad, A. (2004). Cluster center initialization algorithm for K-means clustering. _Pattern recognition letters_, _25_(11), 1293-1302.](https://www.sciencedirect.com/science/article/pii/S0167865504000996)