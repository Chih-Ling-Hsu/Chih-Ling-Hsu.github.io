---
title: 'Centrality Measures of Graph Nodes'
layout: post
tags:
  - Graph
  - Data-Mining
category: Notes
mathjax: true
---


Suppose we are given a graph data $G = (N, E)$, which contains $\|N\| = n$ nodes and $\|E\| = m$ edges.
One big question we often ask is: "Which vertices are important?"
Intuitively, we would consider a "star" (the central node that connects a lot of other nodes) as an obviously important case and also consider nodes in a "circle" are equivalently important.
In general, we can use cantrality measures to rank the nodes in a graph.
There are many centrality measures and page rank is currently the most prominent approach that deals with directed graphs.

<!--more-->



## Degree Centrality

Degree centrality is defined as the **number of nearest neighbours**.

$$
C_{D}(i) = k(i)
$$

However, we can not compare degree centralities of two nodes from two graphs since some grpahs can be much denser than others.
As a result, the normalized degree (by the maximal possible degree) centrality is proposed:

$$
C_D^*(i) = \frac{1}{n-1} C_D(i)
$$

Still, there exists a problem that it does not take graph topology into account.
That is, we may mis-rank the node that connects two dense components but is calculated as degree two.

## Closeness Centrality

Closeness centrality is defined by **how close a node is to all other nodes in the network**.

$$
C_C(i) = \frac{1}{\sum_j d(i, j)}
$$

However, with closeness centrality it is hard to compare two networks with different scales.
Hence, the normalized closeness centrality, which actually represents the (inverse) average distance to all the nodes, is proposed.

$$
C^*_C(i) = (n-1) C_C(i)
$$

The remaining issue for normalized closeness centrality is thatit can not apply to diconnected graphs.   It is only for connected components.




## Betweenness Centrality

Betweenness centrality is defined by **how many pairs of nodes have a shortest path through you**.

$$
C_B(v) = \sum_{s \neq v \neq t \in V} \frac{\sigma_{st}(v)}{\sigma_{st}}
$$

- $\sigma_{st}$: number of shortest paths between $s$ and $t$
- $\sigma_{st}(v)$ number of shortest paths between $s$ and $t$ via $v$

Similarly, the normalized version of betweenness centrality is defined as


$$
C^*_B(v) = \frac{C_B(v)}{(n-1)(n-2)/2}
$$


## "Importance" Centrality

The basic idea of "importance" centrality is that importance of a node depends on the importance of its neighbors.
In other words, we update the centrality of node $i$ recursively using the equation below, starting with value "1" at each node.

$$
v_i \leftarrow \sum_j A_{ij} v_j
$$

- $v_i$: the centrality value of node $i$
- $A_{ij}$: the $ij$-th entry of the adjacency matrix $A$


Likewise, we consider a normalized version


$$
v_i \leftarrow \frac{1}{\lambda} \sum_j A_{ij} v_j
$$

which can be written in matrix terms:

$$
Av = \lambda v
$$

That is, the importance of the nodes is described by $v$, which is Principal Eigenvector of $A$.
This centrality measure is also called "EigenVector Centrality".

## References

- Jure Leskovec, A. Rajaraman and J. D. Ullman, "Mining of massive datasets"  Cambridge University Press, 2012
- David Easley and Jon Kleinberg "Networks, Crowds, and Markets: Reasoning About a Highly Connected World" (2010).
- John Hopcroft and Ravindran Kannan "Foundations of Data Science" (2013).