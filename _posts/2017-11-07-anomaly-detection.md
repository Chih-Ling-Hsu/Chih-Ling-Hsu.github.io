---
title: 'Data Mining - Anomaly Detection'
layout: post
tags:
  - Data-Mining
  - Statistics
category: Notes
mathjax: true
---

Anomalies, or say outliers, are the set of data points that are considerably different than the remainder of the data.   Common applications of anomaly detection are credit card fraud detection, telecommunication fraud detection, network intrusion detection, fault detection, and so on.

The working assumption of anomaly detection is:

> There are considerably more “normal” observations than “abnormal” observations (outliers/anomalies) in the data.


<!--more-->

As the result, the general steps can be

1. **Build a profile of the “normal” behavior**
    
    Profile can be patterns or summary statistics for the overall population

2. **Use the “normal” profile to detect anomalies**
    
    Anomalies are observations whose characteristics differ significantly from the normal profile


And the types of anomaly detection schemes can be graphical-based, statistical-based, distance-based, or model-based.


## Graphical-based

Graphical-based methods to detect anomalies includes boxplot (1-D), scatter plot (2-D), spin plot (3-D), using **convex hull** to detect extreme values, etc.   However, these methods are _time consuming_ and also _subjective_.

## Statistical-based

Statistical-based anomaly detection methods **assume a parametric model describing the distribution of the data (e.g., normal distribution)**.   The general approach is to apply a statistical test that depends on 

1. Data distribution
2. Parameter of distribution (e.g., mean, variance)
3. Number of expected outliers (confidence limit)


### Grubbs’ Test

Grubbs’ Test detects outliers in univariate data.   Assume data comes from normal distribution, we detect one outlier at a time, **remove the outlier if the following null hypothesis is rejected**, and repeat.

- $H_0$: There is no outlier in data
- $H_A$: There is at least one outlier

We reject $H_0$ if

$$
G > \frac{(N-1)}{N}\sqrt{\frac{t^2_{(\alpha/N,N-2)}}{N-2+t^2_{(\alpha/N, N-2)}}}
$$

$$
where~G=Grubbs'~test~statistics=\frac{max_{i=1}^N|X_i-\bar{X}|}{s}
$$

In the equations above, $s$ denotes the standard~deviation of $X$, and $t^2_{(\alpha/N,N-2)}$ denotes the upper critical value of the $t$-distribution with $(N − 2)$ degrees of freedom and a significance level of $(\alpha/N)$.

### Likelihood Approach

Assume the data set contains samples from a mixture of two probability distributions --- $M$ (majority distribution) and $A$ (anomalous distribution) --- and thus the data distribution of the dataset can be formulated into the following equation:

$$
Data~distribution = D = (1 – \lambda) M + \lambda A
$$

$$
where~M~is~a~probability~distribution~estimated~from~data
\\
and~A~is~initially~assumed~to~be~uniform~distribution
$$

The steps of likelihood approach are as follows:

1. Initially ($t=0$), we assume all the data points belong to $M_0$
2. Let $L_t(D)$ be the log likelihood of $D$ at time $t$
    
    $$
    L_t(D) = \prod_{i=1}^{N} P_D(x_i)=\Bigg((1-\lambda)^{|M_t|} \prod_{x \in M_i}P_{M_t}(x_i)\Bigg)\Bigg(\lambda^{A_t} \prod_{x \in A_i}P_{A_t}(x_i)\Bigg)
    $$
3. For each point $x_t$ that belongs to $M_t$, move it to $A_t$
    - Compute the new log likelihood $L_{t+1} (D)$
    - If $L_t(D) – L_{t+1} (D) > c$  (some threshold), then $x_t$ is declared as an anomaly and moved permanently from $M$ to $A$

### Limitations

The limitations of statistical approaches are

1. Most of the tests are for a single attribute.
2. Data distribution may not be known in many cases.
3. It may be difficult to estimate the true distribution for high dimensional data.

## Distance-based

In distance-based anomaly detection methods, data is represented as **a vector of features**, and the 3 major approaches are nearest-neighbor based, density based, and clustering based.

### Nearest-neighbor Based Approach

First, compute the distance between every pair of data points.   Then, remove the outliers according to one of the definitions below:

- Data points for which there are **fewer than $p$ neighboring points** within a distance $D$.
- The top $n$ data points whose **distance to the $k^{th}$ nearest neighbor** is the greatest.
- The top $n$ data points whose **average distance to the $k$ nearest neighbors** is the greatest .

### Density Based: Local Outlier Factor (LOF) Approach

Basic idea of LOF is to **compare the local density of a point with the densities of its neighbors**.   For example, In the image below, point $A$ has a much lower density than its neighbors, so it would have a large LOF value, and thus being removed as an outlier.

![](https://i.imgur.com/1a47Hav.png)


The steps of Local Outlier Factor (LOF) Approach to detect outliers are as belows.

1. For each point, compute the density of its local neighborhood

2. Compute local outlier factor (LOF) of a sample $p$ as the average of the ratios of the density of sample $p$ and the density of its nearest neighbors

$$
LOF_{k}(p)=\frac{\sum_{q \in N_k(p)}\frac{lrd(q)}{lrd(p)}}{|N_k(p)|}
\\
lrd(p)=local~reachability~density~(p)=1/\Bigg(\frac{\sum_{q \in N_k(p)}dist(p,q)}{|N_k(p)|}\Bigg)
$$
    
3. Outliers are points with largest LOF values

### Clustering Based Approach

The steps of clustering based approach to detect outliers are as belows.

1. Cluster the data into groups of different density
2. Choose **points in small cluster** as **candidate outliers**
3. Compute the distance between candidate points and non-candidate clusters. 
4. If any candidate points are **far from all other non-candidate points**, they are outliers

![](https://i.imgur.com/sHXz5GT.png)

## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [Wikipedia - Local outlier factor](https://en.wikipedia.org/wiki/Local_outlier_factor)
- [Grubbs' test for outliers](https://en.wikipedia.org/wiki/Grubbs%27_test_for_outliers)

