---
title: 'Brief Introduction to Popular Data Mining Algorithms'
layout: post
tags:
  - Python
  - scikit-learn
  - Data Mining
category: Programming
mathjax: true
---


In this article, I will introduce a regression algorithm, linear regression, classical classifiers such as decision trees, naïve Bayes, and support vector machine, and unsupervised clustering algorithms such as k-means, and reinforcement learning techniques, the cross-entropy method, to give only a small glimpse of the variety of machine learning techniques that exist, and we will end this list by introducing neural networks.

<!--more-->

```python
%matplotlib inline
import warnings
warnings.filterwarnings('ignore')
```

## A popular open source package

Machine learning is a popular and competitive field, and there are many open source packages that implement most of the classic machine learning algorithms. One of the most popular is `scikit-learn` (http://scikit-learn.org), a widely used open source library used in Python.

scikit-learn offers libraries that implement most classical machine-learning classifiers, regressors and clustering algorithms such as support vector machines (SVM), nearest neighbors, random forests, linear regression, k-means, decision trees and neural networks, and many more machine learning algorithms.

## Linear regression

Regression algorithms are a kind of supervised algorithm that use features of the input data to predict a value, for example the cost of a house given certain features such as size, age, number of bathrooms, number of floors, location, and so on.

Regression analysis tries to find the value of the parameters for the function that best fits an input dataset. In a linear regression algorithm, the goal is to minimize a cost function by finding appropriate parameters for the function on the input data that best approximate the target values. A cost function is a function of the error, which is how far we are from getting a correct result. A typical cost function used is the mean squared error, where we take the square of the difference between the expected value and the predicted result. The sum over all the input examples gives the error of the algorithm and it represents the cost function.

### Import Dataset

Here we use the [diabetes database from scikit-learn](http://scikit-learn.org/stable/datasets/index.html#diabetes-dataset).


```python
from sklearn import datasets
import numpy as np

# Load the diabetes dataset
diabetes = datasets.load_diabetes()

# Use only one feature
X = diabetes.data[:, np.newaxis, 2]
y = diabetes.target
```

### Fit Model


```python
from sklearn.linear_model import LinearRegression

slr = LinearRegression()
slr.fit(X, y)

y_pred = slr.predict(X)

print('Slope (w_1): %.2f' % slr.coef_[0])
print('Intercept/bias (w_0): %.2f' % slr.intercept_)
```

    Slope (w_1): 949.44
    Intercept/bias (w_0): 152.13
    

### Predict with Model


```python
import matplotlib.pyplot as plt
# To simplify our codes, predefine a function to visualize to regression line and data scatter plot.
def lin_regplot(X, y, model):
    plt.scatter(X, y, c='blue')
    plt.plot(X, model.predict(X), color='red', linewidth=2)    
    return 

lin_regplot(X, y, slr)
plt.xlabel('Body mass index')
plt.ylabel('disease progression')
plt.show()
```


![](https://i.imgur.com/PUFeIvc.png)



## Decision trees

Another widely used supervised algorithm is the decision tree algorithm. A decision tree algorithm creates a classifier in the form of a "tree". A decision tree is comprised of decision nodes where tests on specific attributes are performed, and leaf nodes that indicate the value of the target attribute. Decision trees are a type of classifier that works by starting at the root node and moving down through the decision nodes until a leaf is reached.

### Import Datase

Here we use the [iris plants database from scikit-learn](http://scikit-learn.org/stable/datasets/index.html#iris-plants-database)


```python
from sklearn.datasets import load_iris
from sklearn import tree

# Load the iris dataset
iris = load_iris()

X = iris.data
y = iris.target
```

### Fit Model


```python
from sklearn import tree

# criterion : impurity function
# max_depth : maximum depth of tree
# random_state : seed of random number generator
clf = tree.DecisionTreeClassifier(criterion='entropy', 
                              max_depth=3, 
                              random_state=0)
clf = clf.fit(X, y)
```

### Predict with Model


```python
from sklearn.metrics import accuracy_score
y_pred = clf.predict(X)
print('Misclassified samples: %d' % (y != y_pred).sum())
print('Accuracy: %.2f' % accuracy_score(y, y_pred))


for pairidx, pair in enumerate([[0, 1], [0, 2], [0, 3],
                                [1, 2], [1, 3], [2, 3]]):
    # We only take the two corresponding features
    X = iris.data[:, pair]
    y = iris.target
    
    plot_decision_boundaries(X, y, clf)    

plt.suptitle("Decision surface of a decision tree using paired features")
plt.legend()
plt.show()
```

    Misclassified samples: 4
    Accuracy: 0.97
    


![](https://i.imgur.com/Ceom63Z.png)



## K-means

Clustering algorithms, as we have already discussed, are a type of unsupervised machine learning method. The most common clustering technique is called k-means lustering and is a clustering technique that groups every element in a dataset by grouping them into k distinct subsets (hence the k in the name). K-means is a relatively simple procedure, and consists of choosing random k points that represent the distinct centers of the k subsets, which are called centroids. We then select,
for each centroid, all the points closest to it. This will create k different subsets. At this point, for each subset, we will re-calculate the center. We have again, k new centroids, and we repeat the steps above, selecting for each centroid, the new subsets of points closest to the centroids. We continue this process until the centroids stop
moving.

It is clear that for this technique to work, we need to be able to identify a metric that allows us to calculate the distance between points. This procedure can be summarized as follows:

1. Choose initial k-points, called the centroids.
2. To each point in the dataset, associate the closest centroid.
3. Calculate the new center for the sets of points associated to a particular centroid.
4. Define the new centers to be the new centroids.
5. Repeat steps 3 and 4 until the centroids stop moving.

### Import Dataset

Here we use the [iris plants database from scikit-learn](http://scikit-learn.org/stable/datasets/index.html#iris-plants-database)


```python
from sklearn.datasets import load_iris
from sklearn import tree

# Load the iris dataset
iris = load_iris()

X = iris.data
y = iris.target
```

### Fit Model


```python
from sklearn.cluster import KMeans

est = KMeans(n_clusters=3)
est = est.fit(X)
```

### View Clustering Result


```python
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

np.random.seed(5)
fig = plt.figure(1, figsize=(4,3))
ax = Axes3D(fig, rect=[0, 0, .95, 1], elev=48, azim=134)
labels = est.labels_

ax.scatter(X[:, 3], X[:, 0], X[:, 2],
           c=labels.astype(np.float), edgecolor='k')

ax.w_xaxis.set_ticklabels([])
ax.w_yaxis.set_ticklabels([])
ax.w_zaxis.set_ticklabels([])
ax.set_xlabel('Petal width')
ax.set_ylabel('Sepal length')
ax.set_zlabel('Petal length')
ax.set_title('3 clusters')
ax.dist = 12

# Plot the ground truth
fig = plt.figure(2, figsize=(4,3))
ax = Axes3D(fig, rect=[0, 0, .95, 1], elev=48, azim=134)

for name, label in [('Setosa', 0),
                    ('Versicolour', 1),
                    ('Virginica', 2)]:
    ax.text3D(X[y == label, 3].mean(),
              X[y == label, 0].mean(),
              X[y == label, 2].mean() + 2, name,
              horizontalalignment='center',
              bbox=dict(alpha=.2, edgecolor='w', facecolor='w'))
# Reorder the labels to have colors matching the cluster results
y = np.choose(y, [1, 2, 0]).astype(np.float)
ax.scatter(X[:, 3], X[:, 0], X[:, 2], c=y, edgecolor='k')

ax.w_xaxis.set_ticklabels([])
ax.w_yaxis.set_ticklabels([])
ax.w_zaxis.set_ticklabels([])
ax.set_xlabel('Petal width')
ax.set_ylabel('Sepal length')
ax.set_zlabel('Petal length')
ax.set_title('Ground Truth')
ax.dist = 12

fig.show()
```


![](https://i.imgur.com/wbu9noC.png)


![](https://i.imgur.com/RQWf47c.png)



## Naïve Bayes

Naïve Bayes is different from many other machine learning algorithms. Probabilistically, what most machine learning techniques try to evaluate is the probability of a certain event Y given conditions X, which we denote by p(Y|X).

For example, given the picture representing a digit (that is, a picture with a certain distribution of pixels), what is the probability that that number is 5? If the pixels' distribution is such that it is close to the pixel distribution of other examples that were labeled as 5, the probability of that event will be high, otherwise the probability will be low.

Here the code example uses Gaussian Naive Bayes.

### Create Datasets


```python
from sklearn.datasets import make_circles

dataset = make_circles(noise=0.2, factor=0.5, random_state=1)
X, y = dataset
```


```python
from sklearn.preprocessing import StandardScaler

X = StandardScaler().fit_transform(X)
```

### Fit model


```python
from sklearn.preprocessing import StandardScaler
from sklearn.naive_bayes import GaussianNB

clf = GaussianNB()
clf = clf.fit(X, y)
```

### Predict with Model


```python
y_pred = clf.predict(X)
from sklearn.metrics import accuracy_score
print('Accuracy: %.2f' % accuracy_score(y, y_pred))
plot_decision_regions_cmp(X, y, clf, "Naïve Bayes")
```

    Accuracy: 0.90
    

![](https://i.imgur.com/QEpx1wB.png)



## Support vector machines

Support vector machines is a supervised machine learning algorithm mainly used for classification. The advantage of support vector machines over other machine learning algorithms is that not only does it separate the data into classes, but it does so finding a separating hyper-plane (the analog of a plane in a space with more than three dimensions) that maximizes the margin separating each point from the hyper-plane. In addition, support vector machines can also deal with the case when the data is not linearly separable. There are two ways to deal with non-linearly separable data, one is by introducing soft margins, and another is by introducing the so-called kernel trick.

Here the code example would use **Support Vector Classifier with RBF kernel**.

### Create Dataset


```python
from sklearn.datasets import make_circles

dataset = make_circles(noise=0.2, factor=0.5, random_state=1)
X, y = dataset
```


```python
from sklearn.preprocessing import StandardScaler

X = StandardScaler().fit_transform(X)
```

### Fit Model


```python
from sklearn.svm import SVC

clf = SVC(gamma=2, C=1)
clf = clf.fit(X, y)
```

### Predict with Model


```python
y_pred = clf.predict(X)
from sklearn.metrics import accuracy_score
print('Accuracy: %.2f' % accuracy_score(y, y_pred))
plot_decision_regions_cmp(X, y, clf, "RBF SVC")
```

    Accuracy: 0.94
    


![](https://i.imgur.com/6wOZjiX.png)



## The cross-entropy method

So far, we have introduced supervised and unsupervised learning algorithms. The cross-entropy method belongs, instead, to the reinforcement learning class of algorithms, which will be discussed in great detail in Chapter 7, Deep Learning for
Board Games and Chapter 8, Deep Learning for Computer Games of this book. The crossentropy method is a technique to solve optimization problems, that is, to find the best parameters to minimize or maximize a specific function.

In general, the cross-entropy method consists of the following phases:

1. Generate a random sample of the variables we are trying to optimize. For deep learning these variables might be the weights of a neural network.
2. Run the task and store the performance.
3. Identify the best runs and select the top performing variables.
4. Calculate new means and variances for each variable, based on the top performing runs, and generate a new sample of the variables.
5. Repeat steps until a stop condition is reached or the system stops improving.

## Neural Network

Here the code example uses **Multi-Layer Perception Classifier**.

### Import dataset


```python
from sklearn.datasets import load_iris
from sklearn import tree

# Load the iris dataset
iris = load_iris()

X = iris.data
y = iris.target
```

### Fit Model

Now, load the classifier we just need, and tune the parameters using the data.


```python
from sklearn.neural_network.multilayer_perceptron import MLPClassifier
mlp = MLPClassifier(random_state=1)
mlp = mlp.fit(X, y)
```

Now, since the weights are initialized randomly, the `random_state` value is simply there to force the initialization to always use the same random values in order to get consistent results across different trials. It is completely irrelevant to understanding the process. The `fit` function is the important method to call, it is the method that will find the best weights by training the algorithm using the data and labels provided, in a supervised fashion.

### Predict with Model

Now we can check our predictions and compare them to the actual result. Since the function `predict_proba` outputs the probabilities, while `predict` outputs the class with the highest probability, we will use the latter to make the comparison, and we will use one of sikit-learn helper modules to give us the accuracy:


```python
y_pred = mlp.predict(X)
from sklearn.metrics import accuracy_score
print('Accuracy: %.2f' % accuracy_score(y, y_pred))
```

    Accuracy: 0.97
    

And that's it. Of course, as we mentioned, it is usually better to split our data between training data and test data, and we can also improve on this simple code by using some regularization of the data. Scikit-learn provides some helper functions for this as well.

### Plot the result

We can draw some pictures to show the data and how the neural net divides the
space into three regions to separate the three types of flowers (since we can only
draw two-dimensional pictures we will only draw two features at the time).

The below graph shows how the algorithm tries to separate the flowers based only on the petal width and the sepal width, without having normalized the data:


```python
import numpy
from matplotlib.colors import ListedColormap
import matplotlib.pyplot as plt

markers = ('s', '*', '^')
colors = ('blue', 'green', 'red')
cmap = ListedColormap(colors)

data = iris.data[:,[1,3]]
x_min, x_max = data[:, 0].min() - 1, data[:, 0].max() + 1
y_min, y_max = data[:, 1].min() - 1, data[:, 1].max() + 1
resolution = 0.01

# fit data with only petal width and sepal width
data = iris.data[:,[1,3]]
labels = iris.target
mlp.fit(data, labels)


# draw background color
x_min, x_max = data[:, 0].min() - 1, data[:, 0].max() + 1
y_min, y_max = data[:, 1].min() - 1, data[:, 1].max() + 1
resolution = 0.01
x, y = numpy.meshgrid(numpy.arange(x_min, x_max, resolution), numpy.arange(y_min, y_max, resolution))
Z = mlp.predict(numpy.array([x.ravel(), y.ravel()]).T)
Z = Z.reshape(x.shape)
plt.pcolormesh(x, y, Z, cmap=cmap)
plt.xlim(x.min(), x.max())
plt.ylim(y.min(), y.max())


# plot the data
classes = ["setosa", "versicolor", "verginica"]
for index, cl in enumerate(numpy.unique(labels)):
    plt.scatter(data[labels == cl, 0], data[labels == cl, 1],c=cmap(index), marker=markers[index], \
                s=50, label=classes[index], edgecolor='black')
    plt.xlabel('petal length [not standardized]')
    plt.ylabel('sepal length [not standardized]')
    plt.legend(loc='upper left')

    
# Show plot
plt.show()
```


![](https://i.imgur.com/QLW9PA7.png)



### Some Preprocessing techniques

Using `sklearn`, we can split the data and we normalize it, which means we subtract the mean and scale the data to unit variance. Then we fit our algorithm on the training data and we test on the test data:


```python
from sklearn.cross_validation import train_test_split
from sklearn.preprocessing import StandardScaler

# Load the iris dataset
iris = load_iris()
X = iris.data
y = iris.target

# Split test dataset from train dataset
data_train, data_test, labels_train, labels_test = train_test_split(X, y, test_size=0.5, random_state=1)

# Normalization
scaler = StandardScaler()
scaler.fit(X)
data_train_std = scaler.transform(data_train)
data_test_std = scaler.transform(data_test)
data_train_std = data_train
data_test_std = data_test
```

## Appendix

The self-defined plot functions are written here.


```python
def plot_decision_boundaries(X, y, clf, n_classes=3, plot_colors="bry", plot_step=0.01):
    
    # Train
    clf = tree.DecisionTreeClassifier().fit(X, y)

    # Plot the decision boundary
    plt.subplot(2, 3, pairidx + 1)

    x_min, x_max = X[:, 0].min() - 1, X[:, 0].max() + 1
    y_min, y_max = X[:, 1].min() - 1, X[:, 1].max() + 1
    xx, yy = np.meshgrid(np.arange(x_min, x_max, plot_step),
                         np.arange(y_min, y_max, plot_step))

    Z = clf.predict(np.c_[xx.ravel(), yy.ravel()])
    Z = Z.reshape(xx.shape)
    cs = plt.contourf(xx, yy, Z, cmap=plt.cm.Paired)

    plt.xlabel(iris.feature_names[pair[0]])
    plt.ylabel(iris.feature_names[pair[1]])
    plt.axis("tight")

    # Plot the training points
    for i, color in zip(range(n_classes), plot_colors):
        idx = np.where(y == i)
        plt.scatter(X[idx, 0], X[idx, 1], c=color, label=iris.target_names[i],
                    cmap=plt.cm.Paired)

    plt.axis("tight")  
```


```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.colors import ListedColormap

def plot_decision_regions_cmp(X, y, clf, name, h=0.01):

    # just plot the dataset first
    cm = plt.cm.RdBu
    cm_bright = ListedColormap(['#FF0000', '#0000FF'])
    ax = plt.subplot(1, 2, 1)
    ax.set_title("Input data")

    # Plot the data points
    ax.scatter(X[:, 0], X[:, 1], c=y, cmap=cm_bright, edgecolors='k')
    
    # Set min/max
    x_min, x_max = X[:, 0].min() - .5, X[:, 0].max() + .5
    y_min, y_max = X[:, 1].min() - .5, X[:, 1].max() + .5
    xx, yy = np.meshgrid(np.arange(x_min, x_max, h), np.arange(y_min, y_max, h))
    ax.set_xlim(xx.min(), xx.max())
    ax.set_ylim(yy.min(), yy.max())
    ax.set_xticks(())
    ax.set_yticks(())


    # Plot the prediction result
    ax = plt.subplot(1, 2, 2)

    # Plot the decision boundary
    if hasattr(clf, "decision_function"):
        Z = clf.decision_function(np.c_[xx.ravel(), yy.ravel()])
    else:
        Z = clf.predict_proba(np.c_[xx.ravel(), yy.ravel()])[:, 1]

    # Put the result into a color plot
    Z = Z.reshape(xx.shape)
    ax.contourf(xx, yy, Z, cmap=cm, alpha=.8)

    # Plot also the training points
    ax.scatter(X[:, 0], X[:, 1], c=y, cmap=cm_bright, edgecolors='k')

    ax.set_xlim(xx.min(), xx.max())
    ax.set_ylim(yy.min(), yy.max())
    ax.set_xticks(())
    ax.set_yticks(())
    ax.set_title(name)
    
    plt.tight_layout()
    plt.show()
```

## References
- [“Python Deep Learning,” by Valentino Zocca, Gianmario Spacagna, Daniel Slater, Peter Roelants.](https://www.amazon.com/Python-Deep-Learning-Valentino-Zocca/dp/1786464454)