---
title: 'A Monothetic Clustering Method'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Monothetic Clustering is often used in Taxonomy.   For example, when you see a strange animal, how do you know if it's never reported before? You may need to ask $N$ True-or-False questions.

- Q1. animal? (yes/no)
- Q2. with legs? (yes/no)
- ...

So $Monthetic$ means that every time we only use **a single attribute(variable)** to cluster.

<!--more-->

![](https://i.imgur.com/FRuzEvW.png)

As the example above, since $v_2$ and $v_7$ ask the question that all animals answer the same, we can remove $v_7$ (or remove $v_2$).   On the other hand, if all animals answer complemently for two questions, we can also remove one of them.

## Measure of Association

Measure of association between 2 variables $x$ and $y$ can be written as

$$
M(x, y) = M_{xy} = \|Count_{(x,y)=(1,1)} * Count_{(x,y)=(0,0)} - Count_{(x,y)=(1,0)} * Count_{(x,y)=(0,1)}\|
$$

For example, 

- $M(v_1,v_2)$ = \|2*2 - 2*2\| = 0$
- $M(v_4,v_5)$ = \|4*2 - 2*0\| = 8$

Note that the measure sum $Sum_i$ for variable $v_i$ is

$$
Sum_i = \sum_{i \neq j} M_{ij}
$$

and the variable with the largest $Sum_i$ will be used for clustering.

## Bisection

Assume there are 8 data points $\{A, B, C, D, E, F, G\}$, each with 6 attributes $\{v_i, v_2, ..., v_6\}$.

For the first iteration, we compute

$$
Sum_i = \sum_{i \neq j} M_{ij}
$$

for $i \in \{1,2,...,6\}$.

and choose $i$ with the largest $Sum_i$, say, $Sum_5$ is the largest, then we separate data points into 2 clusters accoding to their answer on $v_5$.

Suppose the separated clusters are

- $\{A,B,C,D\}$
- $\{E,F,G\}$

Then for each cluster $C$ generated, we compute

$$
Sum_i = \sum_{i \neq j} M_{ij}
$$

for $i \in \{1,2,...,6\} - \{5\}$.

and choose $i$ with the largest $Sum_i$ to separate cluster $C$.