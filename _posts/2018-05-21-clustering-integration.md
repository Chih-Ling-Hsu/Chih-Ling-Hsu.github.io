---
title: 'Aggregation of Clustering Methods'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Since a large number of clustering algorithms exist, aggregating different clustered partitions into a single consolidated one to obtain better results has become an important problem.

<!--more-->

A few aggregation method has proposed, which will be introduced in the following sections.

## Clustering aggregation by probability accumulation (2009)

In probability accumulation algorithm, the construction of correlation matrices takes the cluster sizes of the original clustering result into consideration.

Assume there are $h$ clustering results $\tilde{R_1}, \tilde{R_2}, ..., \tilde{R_h}$ given $n$ data points { $x_1, x_2, ...x_n$ } where each point has $m$ dimensions.

The clustering aggregation method is stated in the following steps.

**Step 1.** Compute Component Matrix $A^{(p)}$, $p = 1, 2, ..., h$

$$
A^{(p)}_{i, i} = 1~~~~~\forall i = 1, 2, ..., n
\\
A^{(p)}_{i, j}= 
\begin{cases}
\frac{1}{1 + \sqrt[m]{\|C_i\|}}& \text{if } C_i~\text{is}~C_j (\text{given}~x_i \in C_i, x_j \in C_j)\\
0& \text{otherwise}
\end{cases}
$$

**Step 2.** Compute the association between all Component Matrix.

$$
\bar{A} = [P-association] = \frac{1}{h}\bigg(\sum_{p=1}^h A^{(p)}\bigg)
$$

**Step 3.** Generate a distance matrix $D$ using the association $\bar{A}$

$$
D = 1 - \bar{A}
$$

**Step 4.** Use distance matrix $D$ and sinlge linkage ($D_{min}$) hierarchical agglomerative method to perform clustering.

**Step 5.** Stop merging clusters at "Big Jump".

Note that the aggregation result will be better if we pre-process data into uniform distribution (zero mean and standard deviation) for each attribute.

For example, if each data point has only 1 dimension ($m = 1$) and we have performed k-means twice.   So now we have 2 clustering results $R_1$ and $R_2$.


In $R_1$, 

$$
C_1 = \{x_1, x_2\}
\\
C_2 = \{x_3, x_4\}
\\
C_3 = \{x_5, x_6, x_7\}
$$
$$
A^{(1)} = \begin{bmatrix}
1 & 1/3  & 0  & 0  & 0  & 0  & 0\\
1/3 & 1   & 0  & 0   & 0  & 0  & 0\\
0   & 0  & 1  & 1/3  & 0  & 0  & 0\\
0  & 0  & 1/3  & 1 & 0  & 0  & 0\\
0  & 0  & 0  & 0  & 1  & 1/4  & 1/4\\
0  & 0  & 0  & 0  & 1/4  & 1  & 1/4\\
0  & 0  & 0  & 0  & 1/4  & 1/4  & 1\\
\end{bmatrix}
$$

In $R_2$,

$$
C_1 = \{x_1, x_3\}
\\
C_2 = \{x_2, x_4, x_5\}
\\
C_3 = \{x_6, x_7\}
$$



$$
A^{(2)} = \begin{bmatrix}
1 & 0  & 1/3  & 0  & 0  & 0  & 0\\
0 & 1   & 0  & 1/4   & 1/4  & 0  & 0\\
1/3   & 0  & 1  & 0  & 0  & 0  & 0\\
0  & 1/4  & 0  & 1 & 1/4  & 0  & 0\\
0  & 1/4  & 0  & 1/4  & 1  & 0  & 0\\
0  & 0  & 0  & 0  & 0  & 1  & 1/3\\
0  & 0  & 0  & 0  & 0  & 1/3  & 1\\
\end{bmatrix}
$$

Therefore,

$$
\bar{A} = \begin{bmatrix}
1 & \frac{1}{2}(1/3)  & \frac{1}{2}(1/3)  & 0  & 0  & 0  & 0\\
\frac{1}{2}(1/3) & 1   & 0  & \frac{1}{2}(1/4)   & \frac{1}{2}(1/4)  & 0  & 0\\
\frac{1}{2}(1/3)   & 0  & 1  & \frac{1}{2}(1/3)  & 0  & 0  & 0\\
0  & \frac{1}{2}(1/4)  & \frac{1}{2}(1/3)  & 1 & \frac{1}{2}(1/4)  & 0  & 0\\
0  & \frac{1}{2}(1/4)  & 0  & \frac{1}{2}(1/4)  & 1  &\frac{1}{2}(1/4)  & \frac{1}{2}(1/4)\\
0  & 0  & 0  & 0  & \frac{1}{2}(1/4)  & 1  & \frac{1}{2}(1/3+1/4)\\
0  & 0  & 0  & 0  & \frac{1}{2}(1/4)  & \frac{1}{2}(1/3+1/4)  & 1\\
\end{bmatrix}
$$

Next, we use $\bar{A}$ to generate a distance matrix

$$
D = \begin{bmatrix}
0 & 1 - \frac{1}{2}(1/3)  & 1 - \frac{1}{2}(1/3)  & 1  & 1  & 1  & 1\\
1 - \frac{1}{2}(1/3) & 0   & 1  & 1 - \frac{1}{2}(1/4)   & 1 - \frac{1}{2}(1/4)  & 1  & 1\\
1 - \frac{1}{2}(1/3)   & 1  & 0  & 1 - \frac{1}{2}(1/3)  & 1  & 1  & 1\\
1  & 1 - \frac{1}{2}(1/4)  & 1 - \frac{1}{2}(1/3)  & 0 & 1 - \frac{1}{2}(1/4)  & 1  & 1\\
1  & 1 - \frac{1}{2}(1/4)  & 1  & 1 - \frac{1}{2}(1/4)  & 0  & 1 - \frac{1}{2}(1/4)  & 1 - \frac{1}{2}(1/4)\\
1  & 1  & 1  & 1  & 1 - \frac{1}{2}(1/4)  & 0  & 1 - \frac{1}{2}(1/3+1/4)\\
1  & 1  & 1  & 1  & 1 - \frac{1}{2}(1/4)  & 1 - \frac{1}{2}(1/3+1/4)  & 0\\
\end{bmatrix}
$$

and perform hierarchical agglomerative clustering using single linkage.

The clustering result then becomes

$$
C_1 = \{x_1, x_2, x_3, x_4\}
\\
C_2 = \{x_5\}
\\
C_3 = \{x_6, x_7\}
$$

<!--
### Experiments

The experiments are performed on the following data sets:

1. 2 moons: $n = 400+100, m = 2$
2. 3 rings $n = 50+250+200, m = 2$
3. 2 Cigars $n = (50+20)+(100+20), m = 2$
4. Iris Data $n = 50+50+50, m = 3$
5. Wine, Glass, ...

Use k-means 10 times or ==50== times, $k$ is sampled randomly from $[10, 30]$.

raw -> 2005 -> 2009
43% -> 35% -> 31%
-->


## Aggregation Method Based on Genetic Algorithm (1996)

Assume the number of data points is $n$ and we want to have $k$ clusters.   We can create a chromosome each time we generate a clustering result (e.g., $[A,A,C,B,A,C,...,A,B]$ is a chromosome representing that $x_1$ is clustered to $A$, $x_2$ is clustered to $A$, $x_3$ is clustered to $C$, etc.).   So for $n$ data points, each chromosome is of length $n$ and will need $k^{n}$ memory space.

The major disadvantage of this method is that it is time-consuming.   On the other hand, cross-over makes meaningless/non-existent clustering result.

## An evolutionary technique based on k-means for optimal clustering (2002)

In _Information Sciences 146.1-4 (2002): 221-237_, it is stated that optimal clustering is actually related to "optimal L'quantification error".   This method uses **all $k$ cluster centers as a chromosome string, which is consist of $k \times d$ dimensions** if a data point $x$ has $d$ dimensions ($x = [x_1,x_2,...x_d]$).

For example, assume $k=3$ (number of clusters), $d=4$ (data dimension), $n=3000$ (number of data points), and the value of any data point $x$ is an integer between $1$ and $3^5 = 243$.

Then firstly we know that the chromosome string is of length $3 \times 4 = 12$ and there will be $(3^5)^{12}$ possible chromosome strings.

Next, we use a "population pool" (e.g., set the size of pool as $50$ in this case) to let the strings being observed right now live in the pool.

And we follow the steps here to obtain better result from numbers of clustering results, which are represented as chromosomes.

**Step 1.** Randomly generate 50 sets of chromosomes.

**Step 2.** Do GA operations (Selection, Crossover, Mutation) on these 50 strings.

**Step 3.** Among the 50 modified strings, for each string we assign $x_1, ...x_{3000}$ to their nearest centers, update $k$ centers, and evaluate the **TSE (total sum of non-squared error)** of this string.

**Step 4.** Delete the string whose TSE is the worst.   Replace it by the best string in old generations so far.  (Replace the second best if the best is already in the current 50 strings.)

**Step 5.** Repeat **Step 2. ~ Step 4.** for pre-assigned number of generations.

**Step 6.** Among strings in the pool, output the string whose TSE is the smallest.


### Selection

Make several copies of good strings (low TSE).   Use them to replace the strings whose TSE are bad.   (Still 50 strings in pool afterwards)

### Crossover

In each generation, we perform crossover with probability $P_{crossover}$.

Crossover is a probabilistic process that exchanges information between 2 randomly selected parent strings $Q_1$ and $Q_2$ to get 2 new strings.   

This paper uses single-point crossover:

1. Randomly choose a number in {$1,2,...,k\cdot (d-1)$} and call it the crossover point.
2. Cut $Q_1$ and $Q_2$ to 2 parts respectively (i.e., cut $Q_1$ into $Q_1^{left}$ and $Q_1^{right}$).
3. Then, replace $Q_1$ by $[Q_1^{left}, Q_2^{right}]$ and replace $Q_2$ by $[Q_2^{left}, Q_1^{right}]$

### Mutation

Next we perform mutation with probability $P_{mutation}$ in each generation.

We randomly grab a string $Q$ from pool.   Let ${TSE}_Q$ be the TSE of string $Q$ and define ${TSE}_{max}$ and ${TSE}_{min}$ as the maximum and minimum TSE of all strings in the current pool respectively.

Among all data points, we find the maximum value $x_d^{max}$ and the minimum value $x_d^{min}$ in each dimension $d$.

Then the new value of dimension $d$ in string $Q$ becomes

$$
Q_d^{updated}= 
\begin{cases}
Q_d + \delta * (x_d^{max} - Q_d)& if~\delta \geq 0\\
Q_d + \delta * (Q_d - x_d^{min})& \text{otherwise}
\end{cases}
$$

where $\delta$ is sampled from $U(-R, R)$ given

$$
R = \frac{TSE_{Q}-TSE_{min}}{TSE_{max}-TSE_{min}}
\\
(\therefore 0 \leq R \leq 1.0)
$$



### Comparison with GA

In the table below we present the TSE of clustering results using different methods:

- k-means, train until it converges.
- GA, after training 1000 generations.
- K-GA, after training 1000 generations.

Note that the value in the brackets in the mean value of TSE.

| Experiment | k-means | GA | K-GA | 
| - | - | - | - |
| n=10, k=2, 2-dim | 2.225498~3.488572<br>(5.383182) | 2.225498 | 2.225498 |
| n=76, k=3, 2-dim | 47.616~61.613<br>(57.713) | 60.033~72.342<br>(66.800*) | 47.616026 |
| n=871, k=6, ?-dim<br>*Indian Vowels (audio)* | 149373~151605<br>(149904) | 383484~395267<br>(390088) | 149356~149378<br>(149369) |
| n=156, k=3, 2-dim<br>*Iris* | 97.205~124.022<br>(107.725) | 124.127458~139.778272<br>(135.405) | 97.1 |
| n=56, k=3, 5-dim<br>*Crude Oil* | 279.48~279.74<br>(279.597091) | 297.05~318.97<br>(278.965150*) | 278.965150<br>(308.155902) |


*For GA, in these cases the solution space is too large ($3^{76}$ and $3^{56}$ respectively).   So it would converge slowly.


On the other hand, for each method the number of generations needed to get lower bound TSE are

| Experiment | GA | K-GA | 
| - | - | - |
| n=10, k=2, 2-dim | 2.4 | 21 |
| n=76, k=3, 2-dim | 3 | - |
| n=871, k=6, ?-dim<br>*Indian Vowels (audio)* | 2115 | - |
| n=156, k=3, 2-dim<br>*Iris* | 358 | 5867 |
| n=56, k=3, 5-dim<br>*Crude Oil* | 6.6 | 4703 |

## Genetic Clustering for unknown $k$ (GCUK-clustering)

According to "Genetic Clustering for Automatic Evolusion of Clusters" (2002), we can perform Genetic Clustering for unknown $k$.

In GCUK-clustering, the chromosomes are made up of real numbers (representing the coordinates of the centres) as well as the don’t care symbol ‘#’.

The terms used in GCUK-clustering are defined here.

- Each chromosome is represented by 10 cluster centers.
- The value of K is assumed to lie in the range $[2, K_{Max}]$. (e.g., $K \in [2, 10]$)
- Cross-over Probability $P_{crossover}$. (e.g., $P_{crossover}=0.8$)
- Mutation Probability $P_{mutation}$. (e.g., $P_{mutation}=0.001$)
- Population Size $\|P\|$. (e.g., $\|P\| = 50$)

We can follow the steps here to get a good clustering result from populations.

**Step 1. Population Initialization**

For $i = 1,2,..., \|P\|$, generate a chromosome ${string}_i$ with the following steps:

1. Sample $K_i$ from $[2, 10]$.
2. Sample $K_i$ data points randomly from data set.
3. Distribute these $K_i$ points to $K_i$ slots out of 10 slots.
4. The remaining $(10-K_i)$ slots are filled with "#" as "don't care"

$$
{string}_i = [\text{#}~~\overrightarrow{x_{99}}~~\overrightarrow{x_1}~~\text{#}~~\text{#}~~\overrightarrow{x_{30}}~~\overrightarrow{x_{23}}~~\overrightarrow{x_{69}}~~\text{#}~~\overrightarrow{x_9}]
$$

**Step 2. Fitness Computation**

For each chromosome ${string}_i$ in population, we 

- extract the $K_i$ centers stored in it, 
- perform clustering by assigning each point to the cluster corresponding to the closet center, 
- and compute DB index $DB_i$.

$$
DB = \frac{1}{k}\sum_{i=1}^{k} R_i^{(t)}
$$

where 


$$
R_i^{(t)} = \max_{j=\{1,2,...,k\}-\{i\}} \frac{S_i + S_j}{d_{i,j}^{(t)}}
$$

given the **scatter level $S_i$** for Cluster $C_i$ and the **distance $d_{i,j}^{(t)}$** between $C_i$ and $C_j$.

$$
S_i = \frac{1}{\|C_i\|} \sum_{x \in C_i} \|x - y_i\|
$$

$$
d_{i,j}^{(t)} = \|y_i - y_j\|_{L_t} = \bigg(\sum_{d=1}^{D} \|C_{i,d}-C_{j,d}\|^t\bigg)^{1/t}
$$

(let $y_i$ be the center of $C_i$, $D$ be the dimension of each data point)


**Step 3. Genetic Operations**

We then perform genetic operations on chromosomes in pool.   The operations include

- Selection
- Single point crossover with probability $P_{crossover}$
- Mutation with probability $P_{mutation}$

Note that our objective here is to minimize DB index ${DB}_i$ ($\forall i = 1, 2, ..., \|P\|$)

**Step 4. Repeat Step 2. and Step 3. for pre-assigned number of generations and then output the best chromosome.**



The experimental result of using GCUK-clustering is presented in the table below.

| Data Set | Experimental Result |
| - | - |
| n= 76, k = 3, 2-dim | Really find out k = 3, and the k obtained centers are also precise. |
| n = 250, k = 5, 2-dim | Really find out k = 5, and the k obtained centers are also precise. |
| n=683, k=2, 9-dim<br>*Wisconsin Breast Cancer* | Really find out k = 2, and the k obtained centers are also similar to the ground truth. |
| n=156, k=3, 2-dim<br>*Iris* | Find out k = 2 (${DB}_{k=2} = 0.39628$ $< {DB}_{k=3} = 0.74682$) |

## References
- [An evolutionary technique based on K-means algorithm for optimal clustering in RN](https://www.sciencedirect.com/science/article/pii/S0020025502002086)
- [Genetic clustering for automatic evolution of clusters and application to image classification](https://www.sciencedirect.com/science/article/pii/S003132030100108X)
