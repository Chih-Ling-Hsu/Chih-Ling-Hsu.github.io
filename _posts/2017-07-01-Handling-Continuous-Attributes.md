---
title: 'Handling Continuous Attributes with Discretization-based Methods'
layout: post
tags:
  - Data-Mining
  - Association-Analysis
category: Notes
mathjax: true
---

Size of the discretized intervals affect support & confidence.

- If intervals too small
	- may not have enough support
	- e.g. {Refund = No, (Income = 51,250)} $\rightarrow$ {Cheat = No}
- If intervals too large
	- may not have enough confidence
	- e.g. {Refund = No, (0K $\leq$ Income $\leq$ 1B)} $\rightarrow$ {Cheat = No}

When there is any numerical attribute, the problem is to discretize individual numerical attribute into interesting intervals.   Each interval is represented as a Boolean attribute.

<!--more-->

## The _equi-sized_ Approach
The _equi-sized_ approach is to simply partition the continuous domain into intervals with equal length.

## The _equi-depth_ Approach

The _equi-depth_ approach basically partitions the data values into intervals with equal size along the ordering of the data.

1. For a given bucket width $W$, the _sorted values_ are divided into approximately equal buckets
2. **Different bucket has different values**: If upper bound of a bucket is equal to the lower bound of the next bucket, then the first value of the next bucket is reassined into the previous bucket.
3. **Bucket width sould be less than $W$**: If a bucket with width $\geq W$ has more than 1 value, then the bucket is split in two, one containing all the last value, the other containing all other values.


### _Srikant & Agrawal's_ Approach

Another _equi-depth_ approach proposed by Skrikant and Agrawal is based on the measure of the partial completeness over itemsets.   The intuition behind this measure is that **the information lost due to partitioning should be as small as possible**.

1. Discretize attribute using _equi-depth_ partitioning
2. Use **partial completeness measure** to determine number of partitions
3. Merge adjacent intervals as long as support is less than max-support

In step 2, we can determine number of intervals ($N$) given partial completeness level ($K$)

Given assumptions as below:

- $C$: frequent itemsets obtained by considering all ranges of attribute values
- $P$: frequent itemsets obtained by considering all ranges over the partitions

We know that $P$ is $K$-complete with respect to $C$ if 

$ \forall X \in C$, $\exists X’ \in P \subset C$ such that:

1. $X’$ is a generalization of $X$ and $support (X’) \leq K \times support(X)~~~~(K \geq 1)$
2. $ \exists Y’ \subset X’$ such that $support (Y’) \leq K \times support(Y),~\forall Y \subset X $

<img src="discrete.png"></img>

As the number of intervals increases, $K$ increases and at the same time information loss rises.   As a result, we can determine number of intervals by choosing a proper $K$.

## Interestingness Measure

For $S: X \rightarrow Y$, and its generalization $S’: X’ \rightarrow Y’$, Rule $S$ is $R$-interesting with respect to its ancestor rule $S’$ if

$$
R \times E_{E'}(Support(S)) \leq Support(S)
\\
or
\\
R \times E_{E'}(Confidence(S)) \leq Confidence(S)
$$

Given

$$
E_{E'}(Support(S))=\frac{P(s_{1})}{P(s_{1}')}\times\frac{P(s_{2})}{P(s_{2}')}\times\cdots\times\frac{P(s_{k})}{P(s_{k}')}\times Support(S'),
\\
\forall s_{1},s_{2}, ... , s_{k}\in S~and~\forall s_{1}',s_{2}', ... , s_{k}'\in S'
\\
and
\\
E_{E'}(Confidence(S))=\frac{P(y_{1})}{P(y_{1}')}\times\frac{P(y_{2})}{P(y_{2}')}\times\cdots\times\frac{P(y_{k})}{P(y_{k}')}\times Confidence(S'),
\\
\forall y_{1},y_{2}, ... , y_{k}\in Y~and~\forall y_{1}',y_{2}', ... , y_{k}'\in Y'
$$

## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)