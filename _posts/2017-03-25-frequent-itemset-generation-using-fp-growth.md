---
title: 'Frequent Itemset Generation Using FP-Growth'
layout: post
tags:
  - Data-Mining
  - Association-Analysis
category: Notes
mathjax: true
---

FP-Growth uses FP-tree (Frequent Pattern Tree), a compressed representation of the database.   Once an FP-tree has been constructed, it uses a recursive divide-and-conquer approach to mine the frequent itemsets.

<!--more-->

## Build Tree
> How to construct a FP-Tree?

1. Create the root node (null)
2. Scan the database, get the frequent itemsets of length 1, and sort these $1$-itemsets in decreasing support count.
3. Read a transaction at a time.   Sort items in the transaction acoording to the last step.
4. For each transaction, insert items to the FP-Tree from the root node and increment occurence record at every inserted node.
5. Create a new child node if reaching the leaf node before the insersion completes.
6. If a new child node is created, link it from the last node consisting of the same item.

![](https://i.imgur.com/nty7dVx.png)


## Mining Tree
> How to mine the frequent itemsets using the FP-Tree?

FP-Growth finds all the frequent itemsets ending with a particular suffix by employing a divide-and-conquer strategy to split the problem into smaller subproblems.

1. Using the pointer in the header table, decompose the FP-Tree into multiple subtrees, each represent a subproblem (ex. finding frequent itemsets ending in $e$)
2. For each subproblem, traverse the corresponding subtree bottom-up to obtain **conditional pattern bases** for the subproblem recursively.

For example, we want to **find frequent itemsets ending in $e$**. First decompose the FP-Tree to obtain the predix paths ending with $e$, so that we can conclude that the conditional pattern bases for $e$ are

$$
\{(a:1, c:1, d:1), (a:1, d:1), (b:1, c:1)\}
$$

Then we can divide the problem into 3 subproblems (with minimum support count $2$)

- find frequent itemsets ending in $ae$
- find frequent itemsets ending in $ce$
- find frequent itemsets ending in $de$

and solve them recursively until the conditional FP tree contains only an empty node.

(Note that we do not need to find frequent itemsets ending in $be$ since the support count of $be$ is $1$ㄝ, which is smaller than the support count threshold.)

![](https://i.imgur.com/QXBcLWn.png)

So let's go back to the original problem: **How to mine the frequent itemsets using the FP-Tree?**   Using FP-Growth, we can obtain a list of frequent itemsets ordered by their corresponding suffix:

![](https://i.imgur.com/UPuHDDl.png)

## Benefits of using FP-Tree structure

- No need to generate itemset candidates
- No need to scan the database frequently. (Only 2 pass is needed.)


## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [FP - growth / FP 演算法簡介](https://www.slideshare.net/waynechung944/fp-growth-intro)