---
title: 'Data Mining - Preprocessing for Association Analysis'
layout: post
tags:
  - Data-Mining
  - Association-Analysis
category: Notes
mathjax: true
---

Before we do association analysis, we need to handle the following 2 issues:

- Categorical Attributes
- Continuous Attributes

<!--more-->

## Handling Categorical Attributes

To apply association analysis formulation, we need asymmetric binary variables, such as

$$
(Gender=Male, Browser=IE, Buy=No)
$$

Which can be seen as 3 items in a transaction:

$$
(A, B, C)
$$

That's the reason why we need to transform categorical attribute into asymmetric binary variables by introducing a new “item” for each distinct attribute-value pair.   For example, `Browser=IE` should be a different item than `Browser=Chrome` and `Browser=Mozilla`. So we should transform

| Browser | Buy |
| - | - |
| IE | Yes |
| Chrome | Yes |
| Mozilla | No |

into

| Browser=IE | Browser=Chrome | Browser=Mozilla | Buy |
| - | - | - | - |
| Yes | No | No | Yes |
| No | Yes | No | Yes |
| No | No | Yes | No |


## Handling Continuous Attributes

When there is any numerical attribute, the problem is to discretize individual numerical attribute into interesting intervals.   Each interval is represented as a Boolean attribute.

Size of the discretized intervals affect support & confidence.

- If intervals too small
	- may not have enough support
	- e.g. {Refund = No, (Income = $51,250)} \\(\rightarrow\\) {Cheat = No}
- If intervals too large
	- may not have enough confidence
	- e.g. {Refund = No, (0K \\(\leq\\) Income \\(\leq\\) 1B)} \\(\rightarrow\\) {Cheat = No}


### Discretization-based method

**The _equi-sized_ approach** is to simply partition the continuous domain into intervals with equal length.   **The _equi-depth_ approach** basically partitions the data values into intervals with equal size along the ordering of the data.   For more information, please refer to [Handling Continuous Attributes with Discretization-based Methods](../../../2017/07/01/Handling-Continuous-Attributes-with-Discretization-based-Methods).

### Statistics-based method

Assume \\(\mu\\) is the segment of population covered by the rule \\(R\\) and \\(\mu'\\) is the segment of population **not** covered by the rule.   We can say that rule \\(R\\) is interesting if 

$$
\mu' > \mu + \Delta
$$

Where \\(\Delta\\) is the difference that is large enough to determine interestingness.

**Statistical Hypothesis Testing**

Since research/alternative hypothesis (\\(H_1\\)) is

$$
\mu' > \mu + \Delta
$$

So the null hypotheis (\\(H_2\\)) is

$$
\mu' \leq \mu + \Delta
$$

Which can also be

$$
\mu' = \mu + \Delta
\\
\mu' - \mu - \Delta = 0
$$

As a result, we only need to prove that 

$$
Z = \frac{\mu'-\mu-\Delta}{\sqrt{\frac{s_1^2}{n_1^2}+\frac{s_2^2}{n_2^2}}} > 1.64 = at~the~95\%~confidence~level
$$

so we can reject the null hypothesis in favor of the alternative.

### Non-discretization based method

Min-Apriori is an algorithm for finding association rules in data with continuous attributes.   It counts support of itemsets using non-discretization method.   For example, if there are 3 documents

| | \\(W_1\\) | \\(W_2\\) | \\(W_3\\) |
| - | - | - | - |
| \\(D_1\\) | 2 | 2 | 0 |
| \\(D_1\\) | 0 | 0 | 1 |
| \\(D_1\\) | 2 | 3 | 0 |

With Min-Apriori, we do not need to discretize the count of words. Instead, we normalize the word vectors and then use the new definition of support

$$
support(C) = \sum_{i \in T}{min_{j \in C}D(i,j)}
$$

Where \\(C\\) is a set of words and \\(T\\) is the set of documents.

## Multi-level Association Rule

The issues about concept hierarchy:

1. Rules at lower levels may not have enough support to appear in any frequent itemsets.
2. Rules at lower levels of the hierarchy are overly specific.

For example, $\{skim milk\} \rightarrow \{white bread\}$, $\{2\% milk\} \rightarrow \{wheat bread\}$, and $\{skim milk\} \rightarrow \{wheat bread\}$ are also indicative of $\{milk\} \rightarrow \{bread\}$

To deal with the issues, two approaches are introduces:

### Augment transactions with higher level items

Before doing association analysis, extend current association rule formulation by augmenting each transaction with higher level items.

However, this approach increases dimensionality of the data.   And too many frequent patterns involving items from the higher levels would be found since items that reside at higher levels have much higher support counts.

### Generate frequent patterns from highest level to lowest level

Generate frequent patterns at highest level first.   Then, generate frequent patterns at the next highest level, and so on.

The disadvantages of this approach are:

1. More passes over the data leads to dramatically increased I/O requirements.
2. May miss some potentially interesting cross-level association patterns.


## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [Data Mining, Rough Sets and Granular Computing](https://books.google.com.tw/books?id=Y5aqCAAAQBAJ&lpg=PA146&ots=OVWzdv3Vci&dq=discretization%20Srikant%20%26%20Agrawal&hl=zh-TW&pg=PA147#v=onepage&q=discretization%20Srikant%20&%20Agrawal&f=false)
- [Murphy的書房 - 假設檢定 Hypothesis Testing](http://murphymind.blogspot.tw/2011/12/hypothesis-testing.html)