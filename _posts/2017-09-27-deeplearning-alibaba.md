---
title: 'Paper Notes: Deep Learning at Alibaba'
layout: post
tags:
  - Deep-Learning
category: Notes
mathjax: true
---


This notes is taken from the paper

> [Rong Jin. _Deep Learning at Alibaba_. ICJAI 2017](https://www.ijcai.org/proceedings/2017/0002.pdf)

In this keynote, the presenter discussed the limitations of the existing deep learning techniques and shared some solutions that Alibaba chosed to address these problems.


<!--more-->

So what are the limitations of the existing deep learning techniques?

1. **How to deal with high-dimensional but sparse data?**
	- Since we need to learn either a conventional layer or a fully connected layer that maps the input data into a lower dimensional representation, this causes computational challenge.
2. **It is usually assumed that there is small difference between the source and the target domain, however, this is not always the case in practice.**
	- A simple fine tune based approach did not work well when the differences is large.
3. **How to compress a large complicated deep learning model into a smaller one  without losing its prediction power?**
	- Use discrete weights.
4. **There is still no exciting results from the exploration on reinforcement learning techniques in combinatorial optimization.**

## Deep Learning with Very High Dimensional Inputs 

Online recommender system
- takes into account user profiles, the context information of the scenarios, and the real-time feedback collected from individual users.
- ensures the diversity of displayed items in order to maximize user experience.
- Generalized linear models (e.g. logistic regression) are widely used.
	- Generalized linear model often failed to yield accurate prediction for items with limited sales since feature engineering is usually well designed for those popular items but _not_ for items with limited sales.
- Most features are represented in one-hot encoding and thus creates high-dimensional sparse vectors.

Most deep learning approaches are not designed to handle billions of input features.  Alibaba address this challenge by introducing a **random coding scheme** that maps the high dimensional input vector into one with relatively low dimension.   The main advantages of using a random coding scheme are

- It dramatically reduces the dimensionality of the input vectors.
- It is computationally efficient.
- With a high probability, it preserves the geometric relationship among high dimensional vectors.

Using the encoded dense vectors, Alibaba applies a multilayer non-linear transformation to generate appropriate vector representation for users and items. These learned representations will finally be fed into a linear prediction model to estimate the click through rate.

![](https://i.imgur.com/YgITPlW.png)

Experiments to verify the proposed deep learning framework shows a large amounts of improvement on CVR and GMV compared to directly using the linear model.

The key difference between the proposed framework and a generalized linear model is that a linear model is unable to account for the nonlinear interaction between any user and any item.   It may be this difference that limits the performance of generalized linear model.

## Deep Transfer Learning

For the real-world challenges Alibaba deals with, there is often a significant gap between source domain and target domain, making it difficult for a simple fine tune approach to deliver the desirable performance.

To address this challenge, Alibaba proposed to learn an explicit transform from limited data, that **directly maps deep feature learned from the source domain to compact features in the target domain**.

![](https://i.imgur.com/NpsI2pA.png)

A CNN model is learned from the source domain to output a vector representation for each image. A transformation layer is then learned to transfer the feature vectors output from the pre-trained CNN network into a vector representation for the target domain.

Instead of any simple fine tune based approaches, Alibaba uses **hard triplet mining strategy**.

A triplet loss based deep learning is developed to learn the optimal transform that effectively combines the strength of metric learning with the power of CNN.

We define the triplet loss as follows

$$
[∥f(x^a_i) − f(x^p_i)∥^2_2 + 1 − ∥f(x^a_i) − f(x^n_i)∥^2_2]_+
$$

- $[z]_+$ is the hinge loss that outputs _one_ when $z > 0$ and _zero_ otherwise.
- $x^a_i$ is a feature vector.
- $x^p_i$ is a positive instance of $x^a_i$ (with the same label).
- $x^n_i$ is a **negative instance from the KNN** of $x^a_i$ (with different label) that have smaller distance than those of the positive instances.

As a result, the overall objective is to minimize the sum of the triplet loss over all the possible triples that can be formed, which is shown as below:

$$
\sum_{i=1}^N[∥f(x^a_i) − f(x^p_i)∥^2_2 + 1 − ∥f(x^a_i) − f(x^n_i)∥^2_2]_+
$$

To further improve the learning efficiency, they iteratively **update the set of hard triplets in each epoch**.   The empirical studies have shown that this approach is significantly more effective than randomly sample a subset of triplets to form the the objective function.

## Learning a Deep Network with Discrete Weights

The complexity of deep neural network, both in terms of computation and storage requirements, has made it difficult to run deep learning algorithms for scenarios with limited memory and computational resources.

To model the quantized neural network as a optimization problem with discrete constraints, the speaker presents a **unified framework for low-bits quantized neural networks** that leverage the alternating direction method of multipliers (**ADMM**) [Boyd et al., 2011], which was originally designed for convex optimization.

Low-bits quantized neural network can be formulated as a constrained optimization problem:

$$
\min_W f(W)
\\
s.t. W \in C
$$

- $W = \{W_1,W_2,··· ,W_L\}$ and $W_i$ is the parameter of the $i^{th}$ layer in the network.
- $f(W)$ is the loss function of a normal neural network
- $C$ is a discrete set including numbers that are either zero or powers of two.

The advantage of restricting the weights to zero or powers of two is that an expensive floating-point multiplication operation can then be replaced by a sequence of cheaper and faster binary bit shift operations.

Since batch normalization would lead to a scale invariance in the weight parameters, we need to introduce a **scaling factor** $\alpha > 0$ (strictly positive) to the constraints.   The optimization problem can then be rewritten as

$$
\min_W f(W)+I_c(G)
\\
s.t. W = G
$$

- $G$ handles the discrete weights and the consensus constraint $W = G$ to ensure the resulting weight $W$ will be discrete.
- $I_c$ is introduced to represent the indicator function that outputs _zero_ if its input $W \in C$ and $\infty$ otherwise.


## Learning to Optimize

Many combinatorial optimization problems are NP-hard. For example, 

- traveling salesman problem
- bin packing
- vehicle routing problem

Solving combinatorial optimization in practice often relies on **handcrafted heuristics** that help find approximate but competitive solutions efficiently.

The combinatorial optimization problem Alibaba met was the <u>3D bin packing problem</u>.   There are three key decisions which are heuristically made during the packing procedure:

1. the packing order of items
2. the location where to place items
3. the orientation of items to be placed.

To solve this problem, the presenter showed that appropriate **heuristics can be learned by a pointer network and reinforcement learning method**.

### Pointer Network

The pointer network Alibaba used consists of two recurrent neural network (RNN) modules (encoder and decoder).

![](https://i.imgur.com/MTrOI5p.png)

The input of this network is a sequence of dimensions of items to be packed, while the output is the sequence of packing (e.g. <u>order of packing items</u>).

In the figure above, we can see that the chosen items would not get attention feedback any longer.

### Reinforcement Learning

To evaluate the output sequence (the output of network) we consider **the surface area**, which should be as small as possible.

This is because that we can obtain the smallest bin that can pack all the items, given a sequence of packed items.

Now define the surface area as

$$
F(o|s) = WL+LH+WH
$$

- $s$ is the input sequence and $o$ is the output sequence.
- $W$, $H$, $L$ denotes the width, height, and length of the smallest bin respectively.
- $F$ is the surface area, which is used to evaluate the performance of $o$.

So the training objective for a given $s$ is the expected surface area, which is given as below

$$
J(\theta | s) = \varepsilon_{o~p_{\theta}(\cdot | s)}F(o|s)
$$

- $p(o \vert s)$ is the probability of choosing the packing sequence $o$ given the input $s$


And the total training objective is defined as

$$
J (\theta) = E_{s∼S} J(θ|s)
\\
where~s~is~sampled~from~a~distribution~S.
$$

## References
- [Wikipedia - Hinge Loss](https://en.wikipedia.org/wiki/Hinge_loss)
- [The DIY Guide to Seq2Seq](https://github.com/jxieeducation/DIY-Data-Science/blob/master/research/seq2seq.md)
- [Sequence to sequence model: Introduction and concepts](https://medium.com/towards-data-science/sequence-to-sequence-model-introduction-and-concepts-44d9b41cd42d)
- [Wikipedia - Reinforcement Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [增強式學習導論](https://chairco.com.tw/posts/2017/05/Reinforcement%20Learning%20introduction.html)
