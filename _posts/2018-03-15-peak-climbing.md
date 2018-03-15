---
title: 'Peak-Climbing Data Clustering'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Peak-climbing is also called "mode-seeking" or "valley-seeking".

<!--more-->

### Method

With **Grid (Mesh)**, summarise the number of points for each block (cell).

![](https://i.imgur.com/Btxl7lx.png)

For example, there are $6\times 6$ cells, each cell has at most $8=3^2-1$ neighbors.

Start with any cell.   Find the $Max$ of your neighbors and shoot the $Max$ by an arrow (which means "_yield_") if the $Max$ is larger than you.   Repeat this procedure with all cells.

In the end, a "_peak_" is a cell that many cells shoot to.   View a "_peak_" as a center of clusters and the member of this cluster is the cell that points to this "_peak_".

For example, we can find that there are two clusters in the matrix above.   One is at the upper-left and the other is at the lower-right (separated by the symbol "\|").


### Strength

- No need to decide the number of cluster ($k$).

### Limitations

- The number of cluster ($k$) is fixed.
- When the number of cell is too large, the number of peaks will also grow; when the number of cell is too small (e.g., $3 \times 3$), then there may be only 1 peak.
- Not appropriate when the data is high-dimensional.
    - 2-dim: $3^2 -1=8$ neighbors
    - $d$-dim: $3^d- 1=242$ neighbors