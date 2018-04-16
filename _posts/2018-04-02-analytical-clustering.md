---
title: 'Analytical Data Clustering'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Analytical clustering is a quick and automatic way by preserving certain features of the input data. The method is analytical, deterministic, unsupervised, automatic, and noniterative.

<!--more-->

## 1-Dimensional 2-Class Clustering

For example, if we want to find 2 clusters among 3000 data points, the goal is to find

- the 2 representatives $X_A, X_B$
- the 2 percentages $P_A, P_B,$ ($P_A+P_B = 100\%$)
    - cluster $A$ will include $3000\times P_A$ points 
    - cluster $B$ will include $3000\times P_B$ points

Note that we will first obtain the cluster representatives ($X_A, X_B$) and then obtain their percentages ($P_A, P_B$).

Clustering can be viewed as a method for quantification, that is, represent all data with smaller information.

When see it as a quantification, what we need to do is to solve $4$ unknown variables.   So we can do clustering with $4$ assumptions that 

- $P_A+P_B = 100\%$
- $P_AX_A +P_BX_B = \overline{X} = \frac{1}{3000}(X_1+X_2+...+X_{3000})$
- $P_AX_A^{2} +P_BX_B^{2} = \overline{X^{2}} = \frac{1}{3000}(X_1^{2}+X_2^{2}+...+X_{3000}^{2})$
- $P_AX_A^{3} +P_BX_B^{3} = \overline{X^{3}} = \frac{1}{3000}(X_1^{3}+X_2^{3}+...+X_{3000}^{3})$

which can be used to solved the $4$ variables.

## 2-Dimensional 2-Class Clustering

Now we need to find

- the 2 representatives $(X_A, Y_A), (X_B, Y_B)$
- the 2 percentages $P_A, P_B$ ($P_A+P_B = 100\%$)
    - cluster $A$ will include $3000\times P_A$ points 
    - cluster $B$ will include $3000\times P_B$ points

Assume that

$$
X_i = r_icos\theta_i
\\
Y_i = r_isin\theta_i
\\
where~i \in \{A,B\}
$$

the following $6$ formulas will be used to solve these variables:

- $P_A+P_B = 100\%$
- $P_AX_A+P_BX_B=\overline{X}$
- $P_AY_A+P_BY_B=\overline{Y}$
- $P_Ar_A+P_Br_B=\overline{r}$
- $P_A\theta_A+P_B\theta_B=\overline{\theta}$
- Principle axis of $\{(X_A, Y_A), (X_B, Y_B)\}$ = Principle axis of data

To make the computation easier, we first perform a transformation so that $\overline{X}=0$ and $\overline{Y}=0$.   Note that $\overline{xy}, \overline{x^2}, \overline{y^2}, \overline{r}, \overline{\theta}$ are all known values.

Because

- $P_AX_A+P_BX_B=\overline{X}=0$
- $P_AY_A+P_BY_B=\overline{Y}=0$

therefore

- $X_A=\frac{-P_B}{P_A}X_B$
- $Y_A=\frac{-P_B}{P_A}Y_B$

indicating that

$$
r_A = \sqrt{X_A^2+Y_A^2}=\frac{P_B}{P_A}\sqrt{X_B^2+Y_B^2}=\frac{P_B}{P_A}r_B
\\
\because \overline{r}=P_Ar_A+P_Br_B=~P_A\bigg(\frac{P_B}{P_A}r_A\bigg)+P_Br_B = 2P_Br_B
\\
\therefore r_B = \frac{\overline{r}}{2P_B}
$$

And the angle of the principle axis will be

$$
\theta_A = \frac{1}{2}tan^{-1}\bigg(\frac{2\overline{xy}}{\overline{x^2}- \overline{y^2}}\bigg)
\\
\theta_B = \theta_A+\pi
~(\because~Principle~Axis~is~reserved)
\\
\because \overline{\theta} = P_A\theta_A+P_B\theta_B = (1-\theta_B)\theta_A+P_B(\theta_A + \pi)
\\
\therefore P_B=(\overline{\theta}-\theta_A)/\pi
$$

The principle axis is reserved before and after clustering. That is, we are minimizing the squared length of the projection from data point to the principle axis.

## 3-Dimensional 2-Class Clustering

Analytical 2-class Clustering in 3-dim data is to solve 8 unknown variables ($P_A, P_B, X_A, X_B, Y_A, Y_B, Z_A, Z_B$) that uses one of the following method to preserve the properties before and after clustering.

### Method 1. Preserve $\{P; \overline{x}, \overline{y}, \overline{z}, \overline{\|xy\|}, \overline{\|xz\|}, \overline{\|yz\|}, \overline{\|xyz\|}\}$ before and after clustering.

$$
P_A=P_A = \frac{1}{2} \pm \frac{1}{2}\sqrt{\frac{\sqrt{\delta^2+8\delta}-\delta-2}{2}}
\\
where~\delta=\frac{\overline{\|xyz\|}^2}{\overline{\|xy\|}\overline{\|xz\|}\overline{\|yz\|}}
\\
x_A = \pm\sqrt{\frac{P_B}{P_A}\frac{\overline{\|xy\|}~\overline{\|xz\|}}{\overline{\|yz\|}}}
\\
y_A = \pm\sqrt{\frac{P_B}{P_A}\frac{\overline{\|xy\|}~\overline{\|yz\|}}{\overline{\|xz\|}}}
\\
z_A = \pm\sqrt{\frac{P_B}{P_A}\frac{\overline{\|xz\|}~\overline{\|yz\|}}{\overline{\|xy\|}}}
$$


### Method 2. Preserve $\{P; \overline{x}, \overline{y}, \overline{z}, \overline{xy}, \overline{xz}, \overline{yz}, \overline{xyz}\}$ before and after clustering.

$$
P_A = \frac{1}{2} \pm \frac{1}{2}\sqrt{\frac{(\overline{xyz})^2}{(\overline{xyz})^2+4\overline{xy}\overline{xz}\overline{yz}}}
\\
x_A = \pm\sqrt{\frac{P_B}{P_A}\frac{\overline{xy}~\overline{xz}}{\overline{yz}}}
\\
y_A = \pm\sqrt{\frac{P_B}{P_A}\frac{\overline{xy}~\overline{yz}}{\overline{xz}}}
\\
z_A = \pm\sqrt{\frac{P_B}{P_A}\frac{\overline{xz}~\overline{yz}}{\overline{xy}}}
$$

### Method 3. Preserve $\{P; \overline{x}, \overline{y}, \overline{z}, \overline{x^2}, \overline{y^2}, \overline{z^2}, \overline{r}\}$ before and after clustering, where $r=\frac{\sum_{k=1}^{N}\sqrt{x_k^2+y_k^2+z_k^2}}{N}$.


$$
P_A = \frac{1}{2} \pm \frac{1}{2}\sqrt{1-\frac{(\overline{r})^2}{\overline{x^2}+\overline{y^2}+\overline{z^2}}}
\\
x_A = \pm\sqrt{\frac{P_B}{P_A}\overline{x^2}}
\\
y_A = \pm\sqrt{\frac{P_B}{P_A}\overline{y^2}}
\\
z_A = \pm\sqrt{\frac{P_B}{P_A}\overline{z^2}}
$$

### Method 4. Preserve $\{P; \overline{x}, \overline{y}, \overline{z}, \overline{x^2}, \overline{y^2}, \overline{z^2}, \overline{xyz}\}$ before and after clustering.

$$
P_A = \frac{1}{2} \pm \frac{1}{2}\sqrt{\frac{(\overline{xyz})^2}{(\overline{xyz})^2+4\overline{x^2}\overline{y^2}\overline{z^2}}}
\\
x_A = \pm\sqrt{\frac{P_B}{P_A}\overline{x^2}}
\\
y_A = \pm\sqrt{\frac{P_B}{P_A}\overline{y^2}}
\\
z_A = \pm\sqrt{\frac{P_B}{P_A}\overline{z^2}}
$$

## 3-Dimensional 2-Class Clustering

Use the lookup table in [this reserach paper](https://ac.els-cdn.com/0031320396000337/1-s2.0-0031320396000337-main.pdf?_tid=64d0375a-b8ba-4762-9494-c25ed0961bde&acdnat=1523853435_4537709f8d5116676f23959ee7abc8a7) to solve variables.

## Multi-class Clustering

**Step 1.** Do analytical clustering for $k$ = 2.

**Step 2.** Draw vertical line (A [line segment](https://www.wikiwand.com/en/Line_segment) bisector passes through the [midpoint](https://www.wikiwand.com/en/Midpoint) of the segment) to split the two clusters according to their cluster centers.

**Step 3.** For each splitted part of data points, do **Step 1.** and **Step 2.** recursively.

**Step 4.** Merge 2 close clusters that have the same boundary cut.

## Applications

The applications in 3-Dimensions are

- Color Image Sharpening
- Color Image Compression

Also, for $d$-Dimensions, the applications are

- Texture Analysis ($d$ = 6~8)
- Politics Investigation (say, $d$ = 10)
- Vector Quantization (say, $d$ = 16)


### Strengths

- Automatic and fast
- Less sensitive to noise
- No need to do cluster center initialization
- Non-iterative
- Similar results to that of k-means
- Can be used as the center initialization for k-means (a method of CCIA)

### Weakness

- Complicated and tedius
- When $k \geq 5$ ($k$ is the number of classes), there is no formula solution (in this case we can only do clustering iteration-by-iteration)


## References
- [Lin, J. C., & Tsai, W. H. (1994). Feature-preserving clustering of 2-D Data for two-class problems using analytical formulas: An automatic and fast approach. _IEEE transactions on pattern analysis and machine intelligence_, _16_(5), 554-560.](http://ieeexplore.ieee.org/abstract/document/291439/)
- [Lin, J. C. (1996). Multi-class clustering by analytical two-class formulas. _International journal of pattern recognition and artificial intelligence_, _10_(04), 307-323.](http://www.worldscientific.com/doi/abs/10.1142/S0218001496000220)
- [J.C. Lin and W.J. Lin (1996). ”Analytical two-class clustering formulas for high dimensional data,” Pattern Recognition, Vol. 29, No. 11, pp. 1919-1930.](https://ac.els-cdn.com/0031320396000337/1-s2.0-0031320396000337-main.pdf?_tid=64d0375a-b8ba-4762-9494-c25ed0961bde&acdnat=1523853435_4537709f8d5116676f23959ee7abc8a7)
