---
title: 'Vector Quantization and Its Variants'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

The goal of Vector Quantization(VQ) is to perform compression and decompression using a given codebook.   So, annother question is, _"How to generate a codebook?"_

<!--more-->

The answer can be _"by clustering"_, for instance, by repeated 2-means ($2 \rightarrow 4 \rightarrow 8 \rightarrow ... \rightarrow 256$).

However, we should also use the trained codebook to test some other images not in the training(clustering) process.   This shows whether the codebook is good or bad.

## Vector Quantization (VQ)

According to Wikipedia, Vector quantization (VQ) is a classical quantization technique from signal processing that allows the modeling of probability density functions by the distribution of prototype vectors.   It works by dividing a large set of points (vectors) into groups having approximately the same number of points closest to them and each group is represented by its centroid point, as in k-means and some other clustering algorithms.


### Pre-processing: Codebook Generation

- Global codebook: using a training set of images
- Local codebook: use the image itself as the training set

The commonly used Linde-Buzo-Gray(LBG) algorithm to create  codebook is in fact k-means.


- **Input**: The 128x128=16384 blocks of an image (or, the 128x128xT blocks for T images). 
  - Note that each block is 4-by-4 and hence has 4x4=16 pixels (each pixel has an integer value in the range 0~255 to indicate the pixel’s brightness).
- **Output**: The k=256 “typical blocks” called as “code-vectors” or “cluster centers”,
  - Each “cluster center” is still 4-by-4, so still has 4x4=16 pixels (and each pixel is always an integer value in the range 0~255) 
- **Method**: a clustering method to cluster the input blocks into k clusters.

### Encoding: Image Compression

For each block in the image, find its nearest neighbor in the codebook.

For example, if there are 10000 real numbers in the original file (8 * 10000 bits, if $0 \leq x \leq 255~\forall x \in file$)

$$
\{176, 183, 172, ...\}
$$

and the codebook is consists of 

$$
\{Center_{A} = 170, Center_{B} = 180\}
$$

then we can compress the original file into

$$
\{B, B, A, ...\}
$$

which is called the _Index File_ (1 * 10000 bits).

In summary, the compression ratio is $1/8 = 0.125$.

### Decoding: Reconstruct Image

However, note that VQ is a compression method with "error" bits.   **Blocking-effect** (e.g., Lena's shoulder) is one of its drawbacks.

## Analogy between VQ and Clustering

| VQ | Clustering |
| - | - |
| k = 256 CodeVectors | k = 256 ClusterCenters |
| Codebook = {$CodeVector_i$ } $_{i=1}^{256}$ | { $ClusterCenter_i$ } $_{i=1}^{256}$  |
| IndexFile = {A,B,B,A, ...} | Membership = {$x_1 \rightarrow$ Cluster A, $x_2 \rightarrow$ Cluster B, ...} |
| Store IndexFile & Codebook | Record Clustering Result

## Side-Matched Vector Quantization (SMVQ)

Side-Matched Vector Quantization (SMVQ) is proposed by Kim.   The goal of SMVQ is to provide better visual image quality than VQ.

**Strength**:
- Visual quality is better
- Less memory is needed. Compression ratio is also better than VQ’s.

**Weakness**:
- Time Consuming because the encoding process cannot be parallelized.

### Encode Seed Blocks

- Seed Blocks: Topmost and leftmost blocks of size 4x4.
- Encoded by VQ.
- Use universal codebook, find similar block with Euclidean distance


For example, given

$$
a~seed~block=
\begin{bmatrix}
3 & 3 \\
6 & 3 \\
\end{bmatrix}
\\
universal~codebook:
\begin{bmatrix}
0 & 0 \\
0 & 0 \\
\end{bmatrix}
,
\begin{bmatrix}
1 & 1 \\
1 & 1 \\
\end{bmatrix}
,
\begin{bmatrix}
2 & 2 \\
2 & 2 \\
\end{bmatrix}
,
\begin{bmatrix}
3 & 3 \\
3 & 3 \\
\end{bmatrix}
,...
$$


then the minimum distance will be

$$
\|
\begin{bmatrix}
3 & 3 \\
6 & 3 \\
\end{bmatrix} - 
\begin{bmatrix}
3 & 3 \\
3 & 3 \\
\end{bmatrix}
\|
= \sqrt{0^2+0^2+3^2+0^2}
$$

which means that we will use

$$
\begin{bmatrix}
3 & 3 \\
3 & 3 \\
\end{bmatrix}
$$

to represent this seed block.

### Encode Remaining Blocks

- Remaining blocks are also 4x4 each.
- Encoded by SMVQ
- Generate sub-codebook, which is a subset of original codebook and no need to store.

For example, given an image with 4 blocks (1 remaining block and 3 seed blocks encoded by univerval codewords)

$$
\begin{bmatrix}
1 & 0 \\
0 & 0 \\
\end{bmatrix}
\begin{bmatrix}
1 & 1 \\
3 & 1 \\
\end{bmatrix}
\\
\begin{bmatrix}
4 & 2 \\
4 & 4 \\
\end{bmatrix}
\begin{bmatrix}
3 & 3 \\
4 & 2 \\
\end{bmatrix}
\\
\downarrow Encode
\\
\begin{bmatrix}
0 & 0 \\
0 & 0 \\
\end{bmatrix}
\begin{bmatrix}
1 & 1 \\
1 & 1 \\
\end{bmatrix}
\\
\begin{bmatrix}
4 & 4 \\
4 & 4 \\
\end{bmatrix}
\begin{bmatrix}
x & y \\
z & k \\
\end{bmatrix}
$$

Then in **Step 1.**, let a candidate codeblock be

$$
\begin{bmatrix}
x & y \\
z & k \\
\end{bmatrix}
$$

we need to find $n$ candidates (e.g., $n=2$) which minimizes

$$
D(\begin{bmatrix}
x & y \\
z & k \\
\end{bmatrix})
= \|x-1\|+\|y-1\|+\|x-4\|+\|z-4\|
$$

(if $D$ is equal for several candidates, the candidate with smallest index wins.)

Thus we have these 2 candidate codeblock:

$$
\begin{bmatrix}
2 & 2 \\
2 & 2 \\
\end{bmatrix},
\begin{bmatrix}
3 & 3 \\
3 & 3 \\
\end{bmatrix}
$$

In **Step 2.**, among the candidates we choose the block that is most similar to the original block

$$
\begin{bmatrix}
3 & 3 \\
4 & 2 \\
\end{bmatrix}
$$

That is, the following candidate codeblock is chosen

$$
\begin{bmatrix}
3 & 3 \\
3 & 3 \\
\end{bmatrix}
$$

since

$$
\|
\begin{bmatrix}
3 & 3 \\
4 & 2 \\
\end{bmatrix} - 
\begin{bmatrix}
3 & 3 \\
3 & 3 \\
\end{bmatrix}
\|
<
\|
\begin{bmatrix}
3 & 3 \\
4 & 2 \\
\end{bmatrix} - 
\begin{bmatrix}
2 & 2 \\
2 & 2 \\
\end{bmatrix}
\|
$$




## References

- [Vector Quantization - Wiki](https://en.wikipedia.org/wiki/Vector_quantization)