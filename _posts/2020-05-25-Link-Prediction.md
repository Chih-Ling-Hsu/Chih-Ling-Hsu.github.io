---
title: 'Link Prediction'
layout: post
tags:
  - Graph
  - Data-Mining
category: Notes
mathjax: true
---


What is Link Prediction?
Given a snapshot of a network at time $t$, predict edges added in the interval $(t,t')$

<!-- Type of Predictions:
- Link existance (binary classification problem)
- Link weight (regression problem). E.g., predicting movie rating for users
- Link type (multi-class classification problem) -->


<!--more-->


## Link Prediction by Proximity Scoring

A simplest way to predict a link is to use proximity scoring.
That is, select top $n$ node pairs (or above some threshold) as new links.
So here the main issue would be: How to come up with a good similarity function?

### Similarity Functions for Local-Neighborhood

- **Number of common neighbors**

$$
\text{sim}(v_i,v_j) = \|N(v_i) \cap N(v_j)\|
$$

- **Jaccard’s coefficient**

$$
\text{sim}(v_i,v_j) = \frac{\|N(v_i) \cap N(v_j)\|}{\|N(v_i) \cup N(v_j)\|}
$$

- **Adamic/Adar Score**: One of the strongest link predictors

$$
\text{sim}(v_i,v_j) = \sum_{v \in N(v_i) \cap N(v_j)} \frac{1}{\log \|N(v)\|}
$$

### Path-based Similarity Functions for Non-Local-Neighborhood

- **Shortest path**: $\text{path}^s (v_i, v_j)$ indicates a path from node $v_i$ to node $v_j$ with a path length $s$

$$
\text{sim}(v_i,v_j) = -\min_s\{\text{path}^s (v_i, v_j) > 0\}
$$

- **Katz Score**: proportional to the sum of the lengths of all the paths between the two node, where longer path lengths are penalized

$$
\text{sim}(v_i,v_j) = \sum_{l=1}^{\infty} \beta^l \|\text{paths}^{l} (v_i, v_j)\| = \sum_{l=1}^{\infty} (\beta A)_{ij}^l = (I - \beta A)^{-1} - I
$$

- **Expected number of random walk steps**: this is similar to label propagation with absorbing states
  - Hitting time (for undirected graphs): $\text{sim}(v_i,v_j) = -H_{ij}$
  - Commute time (for directed graphs): $\text{sim}(v_i,v_j) = -(H_{ij} + H_{ji})$

- **Proximity score calculated by [SimRank](../../../2020/05/22/PageRank#simrank-random-walk-with-restarts)**


$$
\text{sim}(v_i,v_j) = \text{SimRank}_{v_i}(v_j) = \text{SimRank}_{v_j}(v_i)
$$

<!-- 
 -->

## Link Prediction as a Binary Classification Task

With supervised learning, we can treat node pairs with existing links as positive samples, node pairs without existing links as negative samples, and use a classification model to predict links that will appear in the future.
To achieve so, we treat the similarity scores as features, but also make sure there is no correlated features.
In addition, we should select features that are discriminative (e.g., Katz score is discriminative if it has different values for positive and negative examples)

However, link prediction is a highly imbalanced classification problem.
The number of positive examples (existing edges) is much larger than the number of megative examples.
This is an essential issue to solve in order to do a great job on link prediction with a machine learning model.


<!-- 
## Network Embedding

### Low-Rank Approximations

Eigen-decomposition
|
V
Truncated Singular Value Decomposition (SVD)

(map the graph into a low-dim eigen space and then calculate sim score)

### Graph Representation Learning

map each node in a network into a low-dimensional space

1. Define an encoder that maps from nodes to embeddings
2. Define a [node similarity function](../../../2020/05/25/Link-Prediction#similarity-functions-for-local-Neighborhood)
3. Optimize the parameters of the encoder so that:

$$
\text{sim}(u, v) \approx \mathbb{z}_v^T\mathbb{z}_u
$$ 

The most promintnet papers: DeepWalk, Node2vec
-->

## References

- Jure Leskovec, A. Rajaraman and J. D. Ullman, "Mining of massive datasets"  Cambridge University Press, 2012
- David Easley and Jon Kleinberg "Networks, Crowds, and Markets: Reasoning About a Highly Connected World" (2010).
- John Hopcroft and Ravindran Kannan "Foundations of Data Science" (2013).
- D. Liben-Nowell and J. Kleinberg. The link prediction problem for social networks. Journal of the American Society for Information Science and Technology, 58(7):1019?1031, 2007 
- R. Lichtenwalter, J.Lussier, and N. Chawla. New perspectives and methods in link prediction. KDD 10: Proceedings of the 16th ACM SIGKDD, 2010 
- M. Al Hasan, V. Chaoji, S. Salem, M. Zaki, Link prediction using supervised learning. Proceedings of SDM workshop on link analysis, 2006 
- M. Rattigan, D. Jensen. The case for anomalous link discovery. ACM SIGKDD Explorations Newsletter. v 7, n 2, pp 41-47, 2005 
- M. Al. Hasan, M. Zaki. A survey of link prediction in social networks. In Social Networks Data Analytics, Eds C. Aggarwal, 2011.
