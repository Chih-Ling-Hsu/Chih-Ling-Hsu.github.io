---
title: "Kleinberg's Model of Small-Worlds"
layout: post
tags:
  - Graph
  - Data-Mining
category: Notes
mathjax: true
---

Kleinberg's model presents the infinite family of **navigable Small-World networks** that generalizes Watts-Strogatz model.
Moreover, with Kleinberg's model it is shown that **short paths not only exist but can be found with limited knowledge of the global network**. Decentralized search algorithms can find short paths with high probability.

<!--more-->

> **Navigable Networks** are peculiar in that comparing with searching the whole graph, routing methods can efficiently determine a relatively short path between any two nodes.

The intuition of Kleinberg's model is to make links not random but inversely proportional to the "distance".
Let's assume the dimensionality is 2, the generation of Kleinberg's basic model is described as below: 

| Illustration | Generation process |
| - | - |
| ![Kleinberg (2004)](https://imgur.com/zbcko13.png) | 1. Build a $2$-dimensional grid (lattice)<br><br>2. Add long-range random links between any two nodes $u$ and $v$ with a probability proportional to $d(u, v)^{-r}$, where $r$ is the clustering exponent and $d(x, y)$ is the Manhattan distance between node $x$ and node $y$ |

In general, in a Kleinberg's model, there are 2 types of links:

- **Short range links**: Neighborhood lattice
- **Long range links**: Probability for a node $u$ to have a node $v$ as a long range contact is defined as

$$
P(u \rightarrow v) = \frac{\frac{1}{d(u, v)^r}}{\sum_{\forall i \neq u} \frac{1}{d(u, i)^r}} = \frac{1}{Z}\frac{1}{d(u, v)^r}  \propto \frac{1}{d(u, v)^r}
$$

In other words, long range edges are added to the network that tend to favor nodes closer in distance rather than farther.
Recall that [random graphs have diameter of $O(\log n)$](../../../2020/05/15/Gnp#path-lengths-of-erdos-renyi) where $n$ is the size of the graph, in Kleinberg's model search time is polylogarithmic, while in Watts-Strogatz it is exponential.

|  | Kleinberg's Model | Watts-Strogatz Model | Erdos-Renyi Model |
| - | - | - | - |
| Navigable? | Yes<br>$T = O\big((\log n)^\beta\big)$ | No<br>$T = O\big(n^{\alpha}\big)$ | No<br>$T = O\big(n^{\alpha}\big)$ |
| Search Time | $O\big((\log n)^2\big)$ | $O\big(n^{\frac{2}{3}}\big)$ | $O\big(n\big)$


## Greedy routing in the Kleinberg model

The Kleinberg model of a network is effective at demonstrating the effectiveness of greedy small-world routing. 
Let's use a $1$-dimensional graph as an example and suggest its largest shortest path as $1.0$.
For a node $u$ to select another node $v$ (which is $x$ miles away from $u$) to link with, the probability would be $\frac{1}{x \log n}$, which is proportional to $\frac{1}{x}$.

![Girdzijauskas (2005)](https://imgur.com/g9NHlHA.png)

So if we create $q$ long-range links between $u$ and any arbitray node, the search cost to route from any node $u$ to a targeting node $v$ would be

$$T = O(\frac{(\log n)^2}{q})$$


- $T = O(\log n)$, if $q = O(\log n)$
- $T = O\big((\log n)^2\big)$, if $q =1$

### Scenario with $q = O(\log n)$ long-range links per node

Given a node $u$, we partition the remaining peers into $\log n$ sets {$A_1$, $A_2$, $A_3$, ... , $A_{\log n}$}, where $A_i$ consists of all nodes whose distance from $u$ is between $\frac{1}{2}^{i}$ and $\frac{1}{2}^{i-1}$.
Now each long range contact of $u$ is nearly equally likely to belong to any set $A_i$.
So given $q = O(\log n)$, **on average each node will have a long-range link in each set $A_i$**.

<img src="https://imgur.com/8FdAx49.png" alt="Girdzijauskas (2005)" style="width:500px">

As a result, for a source node $s$ and a target node $t$ in the limiting case $d(u, v) = \log n$, the decentralized search can be explained in the following steps:

1. First select the long-range link to the node $x^{(1)}$ in the set $A_{1}$ of $u$.   Now the distance becomes $d(x^{(1)}, t) = \frac{1}{2} d(u, v)$
2. Continue to select the $i^{th}$ node to hop in the same way and reduce the distance to $d(x^{(i)}, t) = \big(\frac{1}{2}\big)^{i} d(u, v)$
3. At the $(\log n)$-th step we can reach $x^{(i)} = t$ and $d(x^{(i)}, t) = 0$

So the total search time is $T = O(\log n)$.

### Scenario with $q = 1$ long-range link per node

Next, we assume we only have $q=1$ long-range link per node, then for each node $u$, **the probability for its long-range link to belong to the set $A_{1}$ is $\frac{1}{\log n}$**.
Therefore, for a source node $s$ and a target node $t$ in the limiting case $d(u, v) = \log n$, the decentralized search can be explained in the following steps:

1. Spend $O(\log n)$ steps to find a long-range link to a node $x^{(1)}$, which is in the set $A_{1}$ of the previous node.   Now the distance becomes $d(x^{(1)}, t) = \frac{1}{2} d(u, v)$
2. Continue to find the node $x^{(i)}$ in the same way and reduce the distance to $d(x^{(i)}, t) = \big(\frac{1}{2}\big)^{i} d(u, v)$
3. At the $(\log n)$-th step we can reach $x^{(i)} = t$ and $d(x^{(i)}, t) = 0$

So the total search time is $T = O\big((\log n)^2\big)$.

## Clustering Exponent Tuning

Basically, when the clustering exponent $r$ becomes bigger, the Kleinberg model would create more short edges as long-range links.
Assume we have a $d$-dimensional Kleinberg model, the performance of decentralized algorithms would vary according to the value of $r$:

![Kleinberg (2000)](https://imgur.com/IJCdfjM.png)

### When $r = 0$

The long-range contacts are chosen uniformly, resulting in a graph that is similar to Watts-Strogatz random graph.
Therefore, although the short paths exist between every pair of vertices, there is no decentralized algorithm capable of finding these paths efficiently.

### When $r < d$

The long-range contacts are almost long edges since $r$ is small.
Therefore, a decentralized algorithm can quickly approach the neighborhood of target, but then slows down untill finally reaching the target.

### When $r > d$

The long-range contacts are almost short edges since $r$ is big.
Therefore, a decentralized algorithm can quickly find the target if it is in its neighborhood, but would take more time if it is not.

### When $r = d$

When $r = d$, decentralized algorithms exhibit optimal performance.
More specifically, given $q$ as the number of long-rang links per node, the time compexlity of the search time can be elaborated as the following:

|  | $q = 1$ | $q \leq \log n$ | $q = \log n$ | 
| - | - | - | - |
| Search time ($T$) | $O\big((\log n)^2\big)$ | $O(\frac{(\log n)^2}{q})$ | $O(\log n)$

Most of the structured P2P (peer-to-peer) systems utilize the Chord's model, which is one of the logarithmic-like approaches and is similar to Kleinberg's model with $q=\log n$ and $r = d = 1$.

## Real-World Examples of Kleinberg's Model

Here we show some of the real-world networks that apply Kleinberg's Model:

### P2P System

<img src="https://imgur.com/5dtYzog.png" style="width:300px">

- **Back dots**: Peers mapped on the ring (using a uniform hash function)
- **Red dots**: Resources mapped on the ring (using a uniform hash function)
- **Dashed arrows**: Connectivity establishment (based on Kleinberg's Principles)

### Geographic routing in LiveJournal network

Using the social network that comprises 1,312,454 bloggers in the LiveJournal online community (www.livejournal.com) in February 2004, Liben-Nowell et al. analyzed the navigation seach in this "small-world".
In the LiveJournal system, each blogger also explicitly
provides a profile, including his or her geographic location, topical interests, and a list of other bloggers whom he or she considers to be a friend.


![Liben-Nowell (2005)](https://imgur.com/l8oaimM.png)

They performed a simulated version of the message-forwarding experiment in the LiveJournal social network, using only geographic information to choose the next message holder in a chain.
In addition, a **rank-based friendship** is considered: when examining a friend $v$ of $u$, the relevant quantity is the number of people who live closer to $u$ than $v$ does.
Formally, they define the rank of $v$ with respect to $u$ as

$$
\text{rank}_u (v) = \|\{w: d(u, w) < d(u, v)\}\|
$$

Under the rank-based friendship model, they model the probability that $u$ and $v$ are geographic friends by

$$
P(u \rightarrow v) \propto \frac{1}{\big(\text{rank}_u (v)\big)^\alpha}
$$

For a $d$-dimensional space with equally spaced pairs, the choice of $\alpha = d$ is supposed to be optimal, so $\alpha = 2$ should be optimal on this 2D geographic map.
However, in this case $\alpha = 1$ is the optimal choice since our aim is to partition the nodes into equally-distributed parts with the designed probability rule.

## References
- Jure Leskovec, A. Rajaraman and J. D. Ullman, "Mining of massive datasets"  Cambridge University Press, 2012
- David Easley and Jon Kleinberg "Networks, Crowds, and Markets: Reasoning About a Highly Connected World" (2010).
- John Hopcroft and Ravindran Kannan "Foundations of Data Science" (2013).
- [Wikipedia: Small-world routing](https://en.wikipedia.org/wiki/Small-world_routing)
- Kleinberg, J. (2004). The small-world phenomenon and decentralized search. SiAM News, 37(3), 1-2.
- Martel, C., & Nguyen, V. (2004, July). Analyzing Kleinberg's (and other) small-world models. In Proceedings of the twenty-third annual ACM symposium on Principles of distributed computing (pp. 179-188).
- Girdzijauskas, S., Datta, A., & Aberer, K. (2005, April). On small world graphs in non-uniformly distributed key spaces. In 21st International Conference on Data Engineering Workshops (ICDEW'05) (pp. 1187-1187). IEEE.
- Kleinberg, J. Navigation in a small world. Nature 406, 845 (2000).
- Liben-Nowell, D., Novak, J., Kumar, R., Raghavan, P., & Tomkins, A. (2005). Geographic routing in social networks. Proceedings of the National Academy of Sciences, 102(33), 11623-11628.