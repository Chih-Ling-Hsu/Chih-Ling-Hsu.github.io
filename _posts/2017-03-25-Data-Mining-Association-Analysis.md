---
title: 'Data Mining - Association Analysis'
layout: post
tags:
  - Data-Mining
  - Association-Analysis
category: Notes
mathjax: true
---


Association analysis is useful for discovering interesting relationships hidden in large data sets.   The uncovered relationships can be represented in the form of **association rules** or sets of frequent items.

For example, given a table of market basket transactions

| TID | Items                        |
| --- | ---------------------------- |
| 1   | {Bread, Milk}                |
| 2   | {Bread, Diapers, Beer, Eggs} |
| 3   | {Milk, Diapers, Beer, Cola}  |
| 4   | {Bread, Milk, Diapers, Beer} |
| 5   | {Bread, Milk, Diapers, Cola} |

The follwing rule can be extracted from the table:

$$
\{Milk, Diaper\} \rightarrow \{Beer\}
$$



A common strategy adopted by many association rule mining algorithms is to decompose the problem into 2 major subtasks:

**1. Frequent Itemset Generation**

Find all the itemsets that satisfy the _minsup_ threshold.

**2. Rule Generation**

<!--more-->


Extract all the high-confidence rules (_strong rules_) from the frequent itemsets found in the previous step.


![](https://i.imgur.com/RiwE2Oe.png)

## Definitions
### Support Count

$$
\sigma(X) = \left|\{ {t_{i}|X \subset t_{i}, t_{i} \in T}\} \right|
$$

- $I=\{i_{1}, i{2}, ..., i_{N}\}$ is the set of all items
- $T=\{t_{1}, t_{2}, ..., t_{N}\}$ is the set of all transactions
- Each $t_{i}$ is a transaction which conatins a subset of items chosen from $I$
- $X$ is a subset of $t_{i}$

### Association Rule
An associasion rule is an implication expression of the form $X \rightarrow Y$, where $X$ and $Y$ are disjoint itemsets ($X \cap Y= \varnothing$).

The strength of an association rule can be measured in terms of its **support** and **confidence**.   A rule that has very low support may occur simply by chance.   Confidence measures the reliability of the inference made by a rule.

- **Support** of an association rule $X \rightarrow Y$
    - $\sigma(X)$ is the support count of $X$ 
    - $N$ is the count of the transactions set $T$.

$$
s(X \rightarrow Y) = \frac{\sigma(X \cup Y)}{N}
$$

- **Confidence** of an association rule $X \rightarrow Y$
    - $\sigma(X)$ is the support count of $X$ 
    - $N$ is the count of the transactions set $T$.
    
$$
conf(X \rightarrow Y) = \frac{\sigma(X \cup Y)}{\sigma(X)}
$$

- **Interest** of an association rule $X \rightarrow Y$
    - $P(Y) = s(Y)$ is the support of $Y$ (fraction of baskets that contain $Y$)
    - If interest of a rule is close to 1, then it is uninteresting.
        - $I(X \rightarrow Y)=1 \rightarrow X$ and $Y$ are independent 
        - $I(X \rightarrow Y)>1 \rightarrow X$ and $Y$ are positive correlated
        - $I(X \rightarrow Y)<1 \rightarrow X$ and $Y$ are negative correlated
    
$$
I(X \rightarrow Y) = \frac{P(X,Y)}{P(X) \times P(Y)}
$$


For example, given a table of market basket transactions:

| TID | Items                        |
| --- | ---------------------------- |
| 1   | {Bread, Milk}                |
| 2   | {Bread, Diaper, Beer, Eggs} |
| 3   | {Milk, Diaper, Beer, Coke}  |
| 4   | {Bread, Milk, Diaper, Beer} |
| 5   | {Bread, Milk, Diaper, Coke} |

We can conclude that

$$
s(\{Milk, Diaper\} \rightarrow \{Beer\}) = 2/5 = 0.4
$$

$$
conf(\{Milk, Diaper\} \rightarrow \{Beer\}) = 2/3 = 0.67
$$

$$
I(\{Milk, Diaper\} \rightarrow \{Beer\}) = \frac{2/5}{3/5 \times 3/5} = 18/5 = 3.6
$$

## Frequent Itemset Generation
A lattice structure can be used to enumerate the list of all possible itemsets:

![](https://i.imgur.com/04r1a8L.png)

However, the cost of frequent itemset generation is large.   Given $d$ items, there are $2^d$ possible candidate itemsets.   There are several ways to reduce the computational complexity of frequent itemset generation:

1. Reduce the number of candidate itemsets ($M$): [The _Apriori_ Principle](#frequent-itemset-generation-using-apriori)
2. Reduce the number of comparison while counting supports:
    By using more advanced data structures, we can reduce the number of comparisons for matching each candidate itemset against every transaction.

![](https://i.imgur.com/GR5EA5x.png)

Factors Affecting Complexity:

- **Choice of minimum support threshold**
 lowering support threshold results in more frequent itemsets.   This may increase number of candidates and max length of frequent itemsets
- **Dimensionality (number of items) of the data set**
 More space is needed to store support count of each item.
- **Number of transactions**
 Since Apriori makes multiple passes, run time of algorithm may increase with number of transactions.
- **Average transaction width**
 This may increase max length of frequent itemsets and traversals of hash tree.

### Frequent Itemset Generation Using [Apriori](./apriori)
**The Apriori Principle**:

If an itemset is frequent, then all of its subsets must also be frequent.
Conversely, if an subset is infrequent, then all of its supersets must be infrequent, too.

![](https://i.imgur.com/5j1It8G.png)

**Aprioir Algorithm:**

- Generate frequent itemsets of length k (initially k=1)
- Repeat until no new frequent itemsets are identified
    - Generate length (k+1) candidate itemsets from length k frequent itemsets
    - Prune length (k+1) candidate itemsets that contain subsets of length k that are infrequent
    - Count the support of each candidate
    - Eliminate length (k+1) candidates that are infrequent

```python
k=1
F[1] = all frequent 1-itemsets
while F[k] != Empty Set:
    k += 1
    ## Candidate Itemsets Generation and Pruning
    C[k] = candidate itemsets generated by F[k-1]

    ## Support Counting
    for transaction t in T:
        C[t] = subset(C[k], t)     # Identify all candidates that belong to t
        for candidate itemset c in C[t]:
            support_count[c] += 1  # Increment support count

    F[k] = {c | c in C[k] and support_count[c]>=N*minsup}

return F[k] for all values of k
```

### Frequent Itemset Generation Using ECLAT
Instead of horizontal data layout, ECLAT uses vertical data layout to store a list of transaction ids for each item.

![](https://i.imgur.com/99QaH9Y.png)

- Advantage: very fast support counting
- Disadvantage: intermediate tid-lists may become too large for memory

### Frequent Itemset Generation Using [FP-Growth](./frequent-itemset-generation-using-fp-growth)
FP-Growth uses FP-tree (Frequent Pattern Tree), a compressed representation of the database.   Once an FP-tree has been constructed, it uses a recursive divide-and-conquer approach to mine the frequent itemsets.

**Build Tree**
> How to construct a FP-Tree?

1. Create the root node (null)
2. Scan the database, get the frequent itemsets of length 1, and sort these $1$-itemsets in decreasing support count.
3. Read a transaction at a time.   **Sort** items in the transaction acoording to the last step.
4. For each transaction, insert items to the FP-Tree from the root node and increment occurence record at every inserted node.
5. Create a new child node if reaching the leaf node before the insersion completes.
6. If a new child node is created, **link** it from the last node consisting of the same item.

**Mining Tree**
> How to mine the frequent itemsets using the FP-Tree?

FP-Growth finds all the frequent itemsets ending with a particular suffix by employing a divide-and-conquer strategy to split the problem into smaller subproblems.

1. Using the pointer in the header table, decompose the FP-Tree into multiple subtrees, each represent a subproblem (ex. finding frequent itemsets ending in $e$)
2. For each subproblem, traverse the corresponding subtree bottom-up to obtain **conditional pattern bases** for the subproblem recursively.


**Benefits of using FP-Tree structure:**

- No need to generate itemset candidates
- No need to scan the database frequently. (Only 2 pass is needed.)

## Rule Generation
Given a frequent itemset $L$, find all non-empty subsets $f \subset L$ such that $f \rightarrow L – f$ satisfies the minimum [confidence](#association-rule) requirement.

If \|$L$\| = $k$, then there are $2k – 2$ candidate association rules (ignoring $L \rightarrow \varnothing – f$ and $\varnothing \rightarrow L$)

So how to efficiently generate rules from frequent itemsets?

### Confidence-Based Pruning
In general, confidence does not have an anti-monotone property.   But **confidence of rules generated from the same itemset** has an **anti-
monotone** property:

$$
L = \{A,B,C,D\}
\\
c(ABC \rightarrow D) \geq c(AB \rightarrow CD) \geq c(A \rightarrow BCD)
$$

If we compare rules generated from the same frequent itemset $Y$, the following theorem holds for the confidence measure:

> If a rule $X \rightarrow Y$ does not satisfy the confidence threshold, then any rule $X' \rightarrow Y-X'$ (where $X' \subset X$), must not satisfy the confidence threshold as well.

![](https://i.imgur.com/L2jXSFl.png)


### Rule Generation in Apriori Algorithm
Candidate rule is generated by merging two rules that share the same prefix in the rule consequent.

```python
F = frequent k-itemsets for k>=2
for itemset f in F:
    H[1] = 1-itemsets in f 
    rules += ap_genrules(f, H[1])
return rules

def ap_genrules(f, H):
    k = size of itemset f
    m = size of itemsets in H
    if k>m+1:
        H[m+1] = (m+1)-itemsets in f
        for h in H[m+1]:
            conf = support_count[f]/support_count[f-h]
            if conf >= minconf:
                output the rule (f-h) -> h
            else:
                H[m+1] = H[m+1] - h
        ap_genrules(f, H[m+1])
```

## Compact Representation of Frequent Itemsets
In practice, the number of frequent itemsets produced from a transaction data set can be very large. It is useful to identify a small representation set of itemsets from which all other frequent itemsets can be derived.

To reduce the number of rules we can post-process them and only output:

- Maximal frequent itemsets
    - No immediate superset is frequent
    - Gives more pruning

- Closed itemsets
    - No immediate superset has the same count
    - Stores not only frequent information, but exact counts


### Maximal Frequent Itemsets
> An itemset is **maximal frequent** if none of its immediate supersets is frequent.

![](https://i.imgur.com/iSDbt5C.png)

For example, the frequent itemsets in above Figure can be derived into 2 groups:

- Frequent itemsets that begin with item $a$, and may contain items $c$, $d$, or $e$.
- Frequent itemsets that begin with items $b$, $c$, $d$, or $e$.

Maximal frequent itemsets provide a valuable representation for data sets that can produce very long, frequent itemsets, as there are exponentially many frequent itemsets in such data.

Despite providing a compact representation, maximal frequent itemsets do not contains the support information of their subsets.

### Closed Frequent Itemsets
> An itemset is **closed** if none of its immediate supersets has the same support as the itemset.

> An itemset is **closed frequent** if it is closed and its support is greater than or equal to _minsup_.

Algorithms are available to explicitly extract closed frequent itemsets from a given data set.   With this compact representation, we can count support of the frequent itemsets efficciently:

```python
C = the set of closed frequent itemsets
k_max = the maximun size of closed frequent itemsets
F[k_max] = a set of frequent itemsets of size k_max

k = k_max - 1
while k > 0:
    F[k] = frequent itemsets of size k
    for f in F[k]:
        if f not in C:            
            X = a set of frequent itemsets such that each member ff is in F[k+1] and also a subset of f
            support[f] = max(support[ff] in X)
```

## Multiple Minimum Support

Using a single minimum aupport threshold may not be effective since many real data sets have skewed support distribution.

- If _minsup_ is set too high, we could miss itemsets involving interesting rare items. (e.g., expensive products)
- If _minsup_ is set too low, it is computationally expensive and the number of itemsets is very large.

![](https://i.imgur.com/jztdahq.png)

> But how to apply multiple minimum supports?

$$
MS(i) = minimum~support~for~item~i
\\
MS({A, B}) = min(MS(A), MS(B))
$$

For example,  given

- MS(Milk)=5%
- MS(Coke) = 3%
- MS(Broccoli)=0.1%
- MS(Salmon)=0.5%

MS({Milk, Broccoli}) = min (MS(Milk), MS(Broccoli)) = 0.1%

> However, with multiple minimum support, support is no longer anti-monotone.

Need to modify Apriori such that $C_{k+1}$(Candidate itemsets of size $(k+1)$) is generated from $L_{k}$(set of items whose support is $\geq MS(k) = k^{th}$ smallest minimum support) instead of $F_{k}$ (set of frequent $1$-itemset candidates.)

## Evaluation of Association Patterns

Association rule algorithms tend to produce too many rules.   Many of them are uninteresting or redundant.

**Objective Evaluation**

An objective measure is a data-driven approach for evaluating the quality of association patterns.   It is domain-independent and requires minimal input from the users.   Patterns that involve a set of **mutually independent** items or **cover very few transactions** are considered [uninteresting](#association-rule) because they may capture spurious relationships in the data.   Such patterns can be eliminated by applying an [**objective interestingness measure**](#objective-measures-of-interestingness).

An objective measure is usually computed based on **contingency table**.   For example, the table below is a 2-way contingency table for variable $A$ and $B$.

|           | $B$      | $\bar{B}$ |          |
| --------- | -------- | --------- | -------- |
| $A$       | $f_{11}$ | $f_{10}$  | $f_{1+}$ |
| $\bar{A}$ | $f_{01}$ | $f_{00}$  | $f_{0+}$ |
|           | $f_{+1}$ | $f_{+1}$  | $N$      |

- $f_{11}=N \times P(A,B)$ denotes the number of transaction that contains $A$ and $B$.
- $f_{10}=N \times P(A, \bar{B})$ denotes the number of transaction that contains $A$ but not $B$.
- $f_{1+}=N \times P(A)$ denotes the support count for $A$.
- $f_{+1}=N \times P(B)$ denotes the support count for $B$.

The pitfall of confidence can be traced to the fact that the measure ignores the support of the itemset in the rule consequent (e.g. $P(B)$ in the above case).

**Subjective Evaluation**

A pattern is considered subjectively uninteresting unless it reveals unexpected information about the data.   Unlike Objective measures, which rank patterns based on statistics computed from data, subjective measures rank patterns according to user’s interpretation.


### Objective Measures of Interestingness

- **Interest** of an association rule $X \rightarrow Y$
    - $P(Y) = s(Y)$ is the support of $Y$ (fraction of baskets that contain $Y$)
    - If interest of a rule is close to 1, then it is uninteresting.
        - $I(X \rightarrow Y)=1 \rightarrow X$ and $Y$ are independent 
        - $I(X \rightarrow Y)>1 \rightarrow X$ and $Y$ are positive correlated
        - $I(X \rightarrow Y)<1 \rightarrow X$ and $Y$ are negative correlated
    
$$
I(X \rightarrow Y) = \frac{P(X,Y)}{P(X) \times P(Y)}
$$

- **Lift** of an association rule $X \rightarrow Y$
    - $P(Y$\|$X)=\frac{P(X,Y)}{P(X)}=\frac{f_{11}}{f_{1+}}$
    - $P(Y)=f_{+1}$

$$
Lift(X \rightarrow Y) = \frac{P(Y|X)}{P(Y)}
$$


There are lots of measures proposed in the literature.   Some measures are good for certain applications, but not for others:

![](https://i.imgur.com/kj1MR0m.png)

### Properties of Good Objective Measure

**Inversion Property**

An objective measure $M$ is invariant under the inversion operation if its value remains the same when exchanging the frequent counts $f_{11}$ with $f_{10}$ and $f_{10}$ with $f_{01}$.

**Null Addition Property**

An objective measure $M$ is invariant under the null addition operation if it is not affected by increaing $f_{00}$, while all other frequencies in the contingency table stay the same.

**Scaling Invariance Property**

An objective measure $M$ is invariant under the row/column scaling operation.


## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [Apriori and Eclat algorithm in Association Rule Mining](https://www.slideshare.net/wanaezwani/apriori-and-eclat-algorithm-in-association-rule-mining)