---
title: 'Graph-Theoretical Method for Clustering'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

In general, there are two steps in Graph Methods.

**Step 1.** Construct a graph to connect all data (e.g., Minimal Spanning Tree, Relative Neighborhood Graph, Gabrial Graph, Delaunay Triangles, ...)

**Step 2.** Delete some edges which are too long (inconsistent edges)

<!--more-->

## Construct a Graph

The step 1 is to construct a graph that connects all data points.

### Minimal Spanning Tree (MST)

When we construct MST, we 

1. Begin from any point, say point $A$, Tree $T_1=\{\overline{AB}\}$ where point $B$ is the nearest neighbor of $A$.
2. For all remaining steps, Tree $T_k$ choose data point that is not in $T_{k-1}$ and at the same time the nearest point to $T_{k-1}$.   Insert the shortest edge from this point to $T_{k-1}$.

### Relative Neighborhood Graph (RNG)

Another example is Relative Neighborhood Graph (RNG).   RNG connects two data points $x_i$, $x_j$ by $\overline{x_i x_j}$ if there is no other data points in their **Lune Area**.   The **Lune Area** for $x_i$, $x_j$ contains two disks:
 - $Disk_1$ uses $x_i$ as center and $Disk_2$ uses $x_j$ as center
 - $\|x_i-x_j\|$ is the radius of $Disk_1$ and $Disk_2$.

That is,

$$
\overline{x_ix_j} \in RNG \leftrightarrow \|x_i-x_j\| < Max\{\|x_i-x_k\|, \|x_j-x_k\|\}
\\
\forall k \neq i, k \neq j
$$

![](https://i.imgur.com/OWWnhSC.png)


### Gabrial Graph

Gabrial Graph sketches a disk that uses $\overline{x_ix_j}$ as the diameter of the circle.

![](https://i.imgur.com/rXjCxRH.png)


Connect $x_i$ and $x_j$ by $\overline{x_ix_j}$ if there is no other data point $x_k$ in the disk.   That is,

$$
\overline{x_ix_j} \in Gabrial Graph \leftrightarrow \|x_i-x_j\|^2 < \|x_i-x_k\|^2 + \|x_j-x_k\|^2
\\
\forall k \neq i, k \neq j
$$


### Delaunay Triangulation (DT)

Connecting the centers of circumcircles produces the Voronoi disgram (in red).   A circle circumscribing any Delaunay triangle does not contain any other input points in its interior.

<img src="https://i.imgur.com/zF2X8cN.png" style="width:50%">
<img src="https://i.imgur.com/m2dwd8E.png" style="width:50%">


Whenever the Voronoi cells for a pair of points share a boundary component, we join them with an edge.   That is, if 2 points $x_i$ and $x_j$ share a boundary wall, then connect them and get $\overline{x_ix_j}$.

Generate Delaunay lines (in black) so that all points are connected with triangles.   In the plane, the Delaunay triangulation maximizes the minimum angle.



## References
- [Wikipedia - Delaunay Triangulation](https://en.wikipedia.org/wiki/Delaunay_triangulation)
- [Delaunay Triangulations: How Mathematicians Connect The Dots](https://www.forbes.com/sites/kevinknudson/2016/06/13/delaunay-triangulations-how-mathematicians-connect-the-dots/2/#722666e161d3)