---
title: 'Bayes Classification'
layout: post
tags:
  - Data-Mining
  - Classification
category: Notes
mathjax: true
---


Bayes classification is a probabilistic framework for solving classification problems.

<!--more-->

## Bayes Theorem
In probability theory and statistics, Bayes’ theorem (alternatively Bayes’ law or Bayes' rule) describes the probability of an event, based on prior knowledge of conditions that might be related to the event. 

$$
P(A|B)=\frac{P(B|A)P(A)}{P(B)}
$$

where \\(A\\) and \\(B\\) are events and \\(P(B) \neq 0\\) 

For example, given: 

- A doctor knows that meningitis causes stiff neck 50% of the time
- Prior probability of any patient having meningitis is 1/50,000
- Prior probability of any patient having stiff neck is 1/20

If a patient has stiff neck(\\(S\\)), what’s the probability he/she has meningitis(\\(M\\))?

$$
P(M|S)=\frac{P(S|M)P(M)}{P(S)}=\frac{0.5*1/5000}{1/20}=0.0002
$$


## Bayesian Classifier
Bayesian inference is a method of statistical inference in which Bayes' theorem is used to update the probability for a hypothesis as more evidence or information becomes available.   Bayesian updating is particularly important in the dynamic analysis of a sequence of data. 

Bayesian inference derives the posterior probability as a consequence of two antecedents, a prior probability and a "likelihood function" derived from a statistical model for the observed data. Bayesian inference computes the posterior probability according to Bayes' theorem:

$$
P(H|E)=\frac{P(E|H)*P(H)}{P(E)}
$$

Where \\(H\\) is the unknown record's hypothesis of class to be tested, \\(E\\) is the evidence associated with \\(H\\) (attributes of the unknown record), and **P(H\|E)** is the posterior probability of \\(H\\) conditioned on \\(E\\). 


## Naïve Bayes Classifier
In machine learning, naive Bayes classifiers are a family of simple probabilistic classifiers based on applying Bayes' theorem with strong (naive) independence assumptions between the features.

**Characteristics of Naïve Bayes Classifier**

- Handle missing values by ignoring the instance during probability estimate calculations
- Robust to isolated noise points
- Robust to irrelevant attributes
- **Independence assumption** may not hold for some attributes

### Approach
Consider each attribute and class label as random variables

Given a record with attributes **(A1, A2,…,An)**, our goal is to predict class **C**. More specifically, we want to **find the value of C that maximizes P(C\| A1, A2,…,An )**.

- **Step 1.** Compute the posterior probability **P(C \| A1, A2, …, An)** for all values of **C** using the Bayes theorem

	$$
	P(C=C_{j} | A1, A2, …, An) = \frac{P(A1, A2, …, An | C)P(C)}{P(A1, A2, …, An)}
	$$

- **Step 2.** Estimate **P(A1, A2, …, An \| C)** for all values of **C** by assuming independence among attributes Ai when class is given

	$$    
	P(A1, A2, …, An |C=C_{j}) = P(A1| C) P(A2| C)… P(An| C)
	$$

	But how to compute conditioncal probability **P(Ai\| C)** ? (Note that if one of the conditional probability is zero, then the entire expression becomes zero)

	- Original
		- \\(N_{c}\\) = the number of records that belong to class C
		- \\(N_{ic}\\) = the number of records that consist of attribute value \\(Ai\\) and belong to class C

	$$
	P(Ai|C)=\frac{N_{ic}}{N_{c}}
	$$

	- Laplace
		- \\(N_{c}\\) = the number of records that belong to class C
		- \\(N_{ic}\\) = the number of records that consist of attribute value \\(Ai\\) and belong to class C
		- \\(l_{C}\\) = the number of classes

	$$
	P(Ai|C)=\frac{N_{ic}+1}{N_{c}+l_{C}}
	$$

	- M-Estimate
		- \\(N_{c}\\) = the number of records that belong to class C
		- \\(N_{ic}\\) = the number of records that consist of attribute value \\(Ai\\) and belong to class C
		- \\(p\\) = prior probability, which is usually set as uniform priors.
		- \\(m\\) = a parameter, which is also known as pseudocount (virtual examples) and is used for additive smoothing. It prevents the probabilities from being 0. m is generally chosen to be small.

	$$
	P(Ai|C)=\frac{N_{ic}+m}{N_{c}+mp}
	$$

- **Step 3.** Choose value of **C** that maximizes **P(A1, A2, …, An\|C) P(C)** is equivalent to choosing value of **C** that maximizes **P(C \| A1, A2, …, An)**

### Example

Given:

- P(Outlook = sunny \| play = yes) = 2/9
- P(Outlook = sunny \| play = no) = 3/5
- P(Temperature = cool \| play = yes) = 3/9
- P(Temperature = cool \| play = no) = 1/5
- P(Humidity = high \| play = yes) = 3/9
- P(Humidity = high \| play = no) = 4/5
- P(Windy = true \| play = yes) = 3/9
- P(Windy = true \| play = no) = 3/5
- P(play = yes) = 9/14
- P(play = no) = 5/14

For a new day consisting of the below attributes, what would it be classified? (play=yes or play=no?)

| Outlook | Temperature | Humidity | Windy | Play |
| ------- | ----------- | -------- | ----- | ---- |
| sunny   | cool        | high     | true  | ?    |

Let \\(E\\) be the attributes of the unknown records (Outlook=sunny, Temperature=cool, Humidity=high, Windy=true), then

$$
P(play = yes | E) = \frac{P(E | play = yes)P(play = yes)}{P(E)} = \frac{(2/9)(3/9)(3/9)(3/9)(9/14)}{P(E)}=\frac{0.0053}{P(E)}
$$

$$
P(play = no | E) = \frac{P(E | play = no)P(play = no)}{P(E)} = \frac{(3/5)(1/5)(4/5)(3/5)(5/14)}{P(E)}=\frac{0.0206}{P(E)}
$$

$$
P(play = no | E) > P(play = yes | E)
$$

In conclusion, for the new day, **play=no** is more likely than play=yes. 

## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [A Tutorial on Bayesian classifier A Tutorial on  Bayesian classifier with WEKA](http://web.ydu.edu.tw/~alan9956/docu/refer/BayesWEKA.pdf)
- [Wikipedia - Bayesian inference](https://en.wikipedia.org/wiki/Bayesian_inference)
- [Wikipedia - Bayes' theorem](https://en.wikipedia.org/wiki/Bayes%27_theorem)