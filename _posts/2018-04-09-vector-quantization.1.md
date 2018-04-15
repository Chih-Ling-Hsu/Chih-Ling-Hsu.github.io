---
title: 'Vector Quantization'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

The goal of VQ is to perform compression and decompression using a given codebook.   So, annother question is, _"How to generate a codebook?"_

<!--more-->

The answer can be _"by clustering"_, for instance, by repeated 2-means ($2 \rightarrow 4 \rightarrow 8 \rightarrow ... \rightarrow 256$).

However, we should also use the trained codebook to test some other images not in the training(clustering) process.   This shows whether the codebook is good or bad.

## Codebook Generation

- Global codebook: using a training set of images
- Local codebook: use the image itself as the training set

The commonly used Linde-Buzo-Gray(LBG) algorithm to create  codebook is in fact k-means.


## Analogy

| VQ | Clustering |
| - | - |
| k = 256 CodeVectors | k = 256 ClusterCenters |
| Codebook = {CodeVector$_i$}$_{i=1}^{256}$ | {ClusterCenter$_i$}$_{i=1}^{256}$ |
| IndexFile = {A,B,B,A, ...} | Membership = {$x_1 \rightarrow$ Cluster A, $x_2 \rightarrow$ Cluster B, ...} |
| Store IndexFile & Codebook | Record Clustering Result


## References

- 