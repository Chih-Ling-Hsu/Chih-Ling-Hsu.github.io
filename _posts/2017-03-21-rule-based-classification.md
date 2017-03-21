---
title: 'Rule Based Classification'
layout: post
tags:
  - Study
  - DataScience
  - Classification
category: Notes
mathjax: true
---

Rule-Based Classifier classify records by using a collection of “if…then…” rules.

$$
(Condition) \rightarrow Class~Label
$$

$$
(Blood Type=Warm) \wedge (Lay Eggs=Yes) \rightarrow Birds
\\
(Taxable Income < 50K) \vee (Refund=Yes) \rightarrow Evade=No
$$

<!--more-->

- The Left Hand Side is **rule antecedent** or **condition**
- The Right Hand Side is **rule consequent**
- **Coverage** of a rule - Fraction of records that satisfy the antecedent of a rule
- **Accuracy** of a rule - Fraction of records that satisfy both the antecedent and consequent of a rule

![](https://i.imgur.com/bgql2S1.png)

## Advantages of Rule-Based Classifiers
- As highly expressive as decision trees
- Easy to interpret
- Easy to generate
- Can classify new instances rapidly
- Performance comparable to decision trees


## Characteristics of Rule-Based Classifier

If we convert the result of decision tree to classification rules, these rules would be mutually exclusive and exhaustive at the same time.

- **Mutually exclusive** rules
    - Classifier contains mutually exclusive rules if the rules are independent of each other
    - Every record is covered by **at most** one rule
- **Exhaustive** rules
    - Classifier has exhaustive coverage if it accounts for every possible combination of attribute values
    - Each record is covered by **at least** one rule

These rules can be _simplified_.   However, simplified rules may no longer be mutually exclusive since a record may trigger more than one rule. Simplified rules may no longer be exhaustive either since a record may not trigger any rules.

**Solution to make the rule set mutually exclusive:**
- Ordered rule set
- Unordered rule set – use voting schemes

**Solution to make the rule set exhaustive:**
- Use a default class

## Ordered Rule Set

An ordered rule set is known as a decision list.   Rules are rank ordered according to their **priority**.   For example, when a test record is presented to the classifier, it is assigned to the class label of the highest ranked rule it has triggered.   If none of the rules fired, it is assigned to the default class.

That is, if more than one rule is triggered, need **conflict resolution**:

- **Size ordering** - assign the highest priority to the triggering rules that has the “toughest” requirement (i.e., with the most attribute test)
- **Class-based ordering** - decreasing order of prevalence or misclassification cost per class
- **Rule-based ordering** (decision list) - rules are organized into one long priority list, according to some measure of **rule quality** or by experts 

## Building Rules Through Direct Method

Direct Method extract rules directly from data.   **Sequential Covering** such as `CN2` Algorithm and `RIPPER` Algorithm are common direct methods for building classification rules.

Take `Ripper` method as example.   For **2-class problem**, choose one of the classes as positive class, and the other as negative class, learn rules for positive class, and negative class will be default class.   For **multi-class problem**, order the classes according to increasing class prevalence (fraction of instances that belong to a particular class), and learn the rule set for _smallest class first_, treat the rest as negative class.   Repeat with next smallest class as positive class


### Sequential Covering

1. Start from an empty rule
2. Grow a rule using the **Learn-One-Rule** function (**Rule Growing**)
3. **Remove** training records covered by the rule (**Instance Elimination**)
4. Repeat Step (2) and (3) until **stopping criterion** is met 
5. (Optional) **Rule Pruning**

### Rule Growing

**General-to-Specific Strategy (`Ripper` Algorithm)**

1. Start from an **empty rule**: `{}` $\rightarrow$ class
2. Add conjuncts that **maximizes FOIL’s information gain** measure:

$$
Gain(R_{0}, R_{1}) = t ( log\frac{p_{1}}{p_{1}+n_{1}} – log\frac{p_{0}}{p_{0} + n_{0}} )
$$

- $R_{0}$:  `{}` $\rightarrow$ class (initial rule)
- $R_{1}$:  `{A}` $\rightarrow$ class (rule after adding conjunct)
- $t$: number of positive instances covered by both $R_{0}$ and $R_{1}$
- $p_{0}$: number of positive instances covered by $R_{0}$
- $n_{0}$: number of negative instances covered by $R_{0}$
- $p_{1}$: number of positive instances covered by $R_{1}$
- $n_{1}$: number of negative instances covered by $R_{1}$

**Specific-to-General Strategy (`CN2` Algorithm)**

1. Start from an **empty conjunct**: `{}`
2. Add conjuncts that **minimizes the entropy measure**: `{A}`, `{A,B}`, …
3. Determine the rule consequent by taking **majority** class of instances covered by the rule

### Instance Elimination

- Why do we need to eliminate instances?
Otherwise, the next rule is identical to previous rule
- Why do we remove positive instances?
Ensure that the next rule is different
- Why do we remove negative instances?
Prevent underestimating accuracy of rule

### Rule Evaluation

Assume $n$ is Number of instances covered by rule, $n_{c}$ is Number of instances covered by rule, $k$ is Number of classes, and $p$ is Prior probability.

$$
Accuracy=\frac{n_{c}}{n}
$$

$$
Laplace=\frac{n_{c}+1}{n+k}
$$

$$
M-estimate=\frac{n_{c}+kp}{n+k}
$$

### Stopping Criterion
Compute the gain, if gain is not significant, discard the new rule.

### Rule Pruning
Rule Pruning is similar to post-pruning of decision trees.

**Reduced Error Pruning :** 

1. Remove one of the conjuncts in the rule 
2. Compare error rate on validation set before and after pruning
3. If error improves, prune the conjunct


**Measure for pruning in Ripper method**

Delete any final sequence of conditions that maximizes $v$.

$$
v = \frac{p-n}{p+n}
\\
p = number~of~positive~examples~covered~by~the~rule~in~ the~validation~set
\\
n = number~of~negative~examples~covered~by~the~rule~in~ the~validation~set
$$


## Building Rules Through Indirect Method
Indirect mothods extract rules from other classification models (e.g. decision trees, neural networks, etc.)

### Indirect Method: C4.5rules 

**Step 1. Obtain Rule Set**

C4.5rules extract rules from an **unpruned** decision tree.

For each rule, r: $A \rightarrow y$, 

1. consider an alternative rule $r’$: $A’ \rightarrow y$ where $A’$ is obtained by removing one of the conjuncts in $A$
2. Compare the [pessimistic generalization error](../../../2017/03/19/Data-Science-classification#underfitting-and-overfitting) for $r$ against all $r’$s
3. Prune if one of the $r’$s has lower pessimistic error rate

Repeat until we can no longer improve generalization error

**Step 2. Class-Based Ordering**

Instead of ordering the rules, Class-Based Ordering orders **subsets** of rules.
- Each subset is a collection of rules with the same rule consequent (class)
- Compute description length of each subset
    -  $g$ : a tuning parameter that takes into account the presence of redundant attributes in a rule set (default value = 0.5)
    -  $L(error)$ : length needed for misclassified example
    -  $L(model)$ : length needed for the whole model

$$
Description~length = L(error) + g*L(model)
$$




## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [[筆記] 03.NOV.11 Data Mining 上課筆記](http://123android.blogspot.tw/2011/11/111103-data-mining.html)
- [Machine Learning and Data Mining: 12 Classification Rules](https://www.slideshare.net/pierluca.lanzi/machine-learning-and-data-mining-12-classification-rules)