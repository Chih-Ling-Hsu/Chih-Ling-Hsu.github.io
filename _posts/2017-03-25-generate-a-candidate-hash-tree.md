---
title: 'Generate a Candidate Hash Tree'
layout: post
tags:
  - Data-Mining
  - Association-Analysis
category: Notes
mathjax: true
---

To generate a candidate hash tree, the followings are required.

- Hash function
- Max leaf size - if number of candidate itemsets exceeds max leaf size, split the node

<!--more-->

## Insertion of Itemset Candidates
The insertion of an itemset \\(X\\) is shown as below:

1. Let \\(k\\) be the current layer of the hash tree (initially \\(k=1\\))
2. Perform Hash function on the \\(k^{th}\\) item in the itemset \\(X\\) and get \\(n\\)
3. Traverse to the \\(n^{th}\\) node of the current layer
4. If the \\(n^{th}\\) node of the current layer is a leaf node, insert the itemset \\(X\\) to this leaf node; If not, increment the value of \\(k\\) and jump back to step 1.
5. Determine if this leaf node is full. If not, the insertion is completed; Else, split the node with the same rule as above.

For example, given:

- Hash function : \\(n = hash(X[k]) = X[k]~mod~3\\)
	- Hash(1,4,7) \\( \rightarrow \\) 1 (left subtree)
	- Hash(2,5,8) \\( \rightarrow \\) 2 (middle subtree)
	- Hash(3,6,9) \\( \rightarrow \\) 3 (right subtree)
- Max leaf size : 3

Suppose you have 15 $3$-itemset candidates to be inserted:

{1 4 5}, {1 2 4}, {4 5 7}, {1 2 5}, {4 5 8}, {1 5 9}, {1 3 6}, {2 3 4}, {5 6 7}, {3 4 5}, {3 5 6}, {3 5 7}, {6 8 9}, {3 6 7}, {3 6 8}


Then the generated hash tree would be:

![](https://i.imgur.com/mzebkKn.png)
![](https://i.imgur.com/jH1F9mg.png)


## Subset Operation Using Hash Tree

To identify all $3$-itemset candidates that belong to a transaction $t$, we hash the transaction $t$ from the root node of the generated candidate hash tree

2. Let \\(k\\) be the current layer of the hash tree (initially \\(k=1, \text{Identified Set} = \varnothing\\))
2. Perform Hash function on the \\(k^{th}\\) item in the itemset \\(X\\) and get \\(n\\)
3. Visit the \\(n^{th}\\) node of the current layer
4. If the \\(n^{th}\\) node of the current layer is a leaf node, add this leaf node to \\(\text{Identified Set}\\); If not, increment the value of \\(k\\) and jump back to step 1.

![](https://i.imgur.com/rBIYVo2.png)



## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [Apriori中的hash tree](http://www.rritw.com/a/JAVAbiancheng/j2ee/2012/0602/167448.html)
