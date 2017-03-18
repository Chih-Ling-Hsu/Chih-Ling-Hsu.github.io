---
title: 'Data Science - data exploration'
layout: post
tags:
  - Study
  - Data Science
  - Introduction to Data Mining`
category: Program
---

<!--more-->

## Data

Definition: Collection of data objects and their attributes

### Types of attributes
*   Nominal: distinctness
*   Oridinal: distinctness & order
*   Interval: distinctness, order & addition
*   Ratio: distinctness, order, addition & multiplication

### Types of data sets
*   Record: data matrix, documents, transactions
*   Graph: web, chemical structures
*   Ordered: spatial/temporal data, sequential data
  
### Data quality problems
*   noise
*   outliers
*   missing values
*   duplicate data

---

## Data Preprocessing
### Aggregation

Combining two or more attributes (or objects) into a single attribute (or object)

#### Purpose
- Data reduction
- Change of scale
- More "stable" data - less variability


### Sampling
The main technique employed for data selection.  
Often used for both the preliminary investigation of the data and the final data analysis.

**Purpose**
- Obtaining the entire set of data of interest is too expensive or time consuming
- Processing the entire set of data of interest is too expensive or time consuming.

**Types of Sampling**
- Simple Random Sampling
- Sampling without replacement
- Sampling with replacement
- Stratified sampling - Split the data into several partitions; then draw random samples from each partition


### Dimensionality Reduction

Curse of Dimensionality: Definitions of density and distance between points, which is critical for clustering and outlier detection, become less meaningful as dimensionality increases.

#### Purpose
- Avoid curse of dimensionality
- Reduce amount of time and memory required by data mining algorithms
- Allow data to be more easily visualized
- May help to eliminate irrelevant features or reduce noise

#### Techniques
- Principle Component Analysis (PCA)
- Singular Value Decomposition (SVD)
- Others: supervised and non-linear techniques


### Feature Subset Selection

Remove redundant features and irrelevant features to reduce dimensionality of data.

#### Techniques
- Brute-force approach
- Embedded approaches -
    Feature selection occurs naturally as part of the data mining algorithm
- Filter approaches - 
    Features are selected before data mining algorithm is run
- Wrapper approaches - 
    Use the data mining algorithm as a black box to find best subset of attributes
 
### Feature Creation

Create new attributes that can capture the important rmation in a data set much more efficiently than the original attributes

#### Methodologies
- Feature Extraction
- Mapping Data to New Space -
    ex. Fourier transform
- Feature Construction - 
    combining features 


### Discretization and Binarization
1.  Discretization Using Class Labels
2.  Discretization Without Using Class Labels



### Attribute Transformation
A function that maps the entire set of values of a given attribute to a new set of replacement values such that each old value can be identified with one of the new values. (ex. Standardization and Normalization)

---

## Similarity and Dissimilarity

![](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_Mb8cBmk1kgS_p.684681_1489297493238_undefined)

### Distance Metrics

A distance that satisfies the following properties is a <u>metric:</u>
- Positive definiteness
    d(p, q) >= 0   for all p and q 
    d(p, q) = 0 only if p = q
- Symmetry
    d(p, q) = d(q, p)   for all p and q
- Triangle Inequality
    d(p, r) <= d(p, q) + d(q, r)   for all points p, q, and r

#### 1. Euclidean Distance
![](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_Mb8cBmk1kgS_p.684681_1489297732788_undefined)

#### 2. Minkowski Distance
![](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_Mb8cBmk1kgS_p.684681_1489297951137_undefined)
*   When p=1, it is Manhattan distance
*   When p=2, it is Euclidean distance
*   When p→∞, it is Chebyshev distance
![](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_Mb8cBmk1kgS_p.684681_1489298637122_undefined)
*   Since Minkowski distance assumes the scales of different dimensions are the same, **standardization is necessary** if scales differ.

#### 3. Mahalanobis Distance
![](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_Mb8cBmk1kgS_p.684681_1489298382429_undefined)
*   S is the covariance matrix of the input data


### Similarity Metrics

Similarities satisfy the following properties:
- Maximum similarity
s(p, q) = 1 only if p = q
- Symmetry
s(p, q) = s(q, p)   for all p and q

#### 1. Similarity Between Binary Vectors
*   Mij = the number of attributes where p = i and q = j
*   Simple Matching (**SMC**)  =  (M11}+ M00) / (M01 + M10 + M11 + M00)
*   **Jaccard Coefficients** = (M11) / (M01 + M10 + M11) 
![](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_Mb8cBmk1kgS_p.684681_1489300190297_undefined)

#### 2. Cosine Siminarity
![](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_Mb8cBmk1kgS_p.684681_1489300159033_undefined)

#### 3. Extended Jaccard Coefficient (Tanimoto)
![](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_Mb8cBmk1kgS_p.684681_1489300977469_圖片4.jpg)

#### 4. Correlation Coefficient/Distance
![](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_Mb8cBmk1kgS_p.684681_1489301219573_undefined)
- To compute correlation, we standardize data objects and then take their dot product

#### 5. Approach for Combining Similarities
- General Approach
![](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_Mb8cBmk1kgS_p.684681_1489301969600_圖片5.png)
- Using Weights
![](https://i.imgur.com/I2XXwRb.png)

---

## Data Exploration

A preliminary exploration of the data to better understand its characteristics.

- Key motivations
    - Helping to select the right tool for preprocessing or analysis
    - People can recognize patterns not captured by data analysis tools 
- [Exploratory Data Analysis(EDA) - Engineering Statistics Handbook](http://www.itl.nist.gov/div898/handbook/index.htm)


### Summary Statistics

Summary statistics are numbers that summarize properties of the data

#### Frequency
- The frequency of an attribute value is the percentage of time the value occurs in the data set 
- Typically used with **categorical** data
#### Mode
- The mode of a an attribute is the most frequent attribute value
- Typically used with **categorical** data
#### Percentile
- the pth percentile of a distribution is a number such that approximately p percent (p%) of the values in the distribution are equal to or less than that number.
- Typically used with **continuous** data
#### Location (Mean and Median)
- The most common measure of the location of a set of points
- Since the mean is very sensitive to outliers, the **median** or a **trimmed mean** is also commonly used.
#### Spread (Range and Variance)
- Range is the difference between the max and min.
- The variance or standard deviation is the most common measure of the spread of a set of points.
![](https://i.imgur.com/4jkH2um.png)
- Since the variance is also sensitive to outliers, so other measures such as AAD, MAD, and interquartile range are often used.


### Visualization

Visualization is the conversion of data into a visual or tabular format so that the characteristics of the data and the relationships among data items or attributes can be analyzed or reported.png)

#### Representation
- The mapping of rmation to a visual format.
- Data objects, their attributes, and the relationships among data objects are translated into graphical elements such as **points, lines, shapes, and colors**.

#### Arrangement
- The placement of visual elements within a display.
![](https://i.imgur.com/uUzpznl.png)

#### Selection
- The elimination or the de-emphasis of certain objects and attributes.
- It may involve the chossing a subset of attributes objects (Dimensionality Reduction)

#### Visualization Techniques
1. **Histograms**
    - 1D - shows the distribution of values of a single variable
    - 2D - Show the joint distribution of the values of two attributes 
    ![](https://i.imgur.com/hml1ADX.png)
2. **Box Plots**
    - Another way of displaying the distribution of values of a single variable
    ![](https://i.imgur.com/y6OsQy4.png)
    - It can be used to compare attributes
    ![](https://i.imgur.com/wTB9iDo.png)
3. **Scatter Plots**
    - Attributes values determine the position. Two-dimensional scatter plots most common, but can have three-dimensional scatter plots.
    ![](https://i.imgur.com/9nZUMVG.png)
4. **Contour Plots**
    - Partition the plane into regions of similar values. The contour lines that form the boundaries of these regions connect points with equal values.
    - Useful when a **continuous attribute** is measured on a **spatial grid**.
    - The most common example is contour maps of elevation, temperature, rainfall, air pressure, etc.
    ![](https://i.imgur.com/Dnd9Yia.png)
5. **Matrix Plots**
    - Plot the data matrix to show **relationships between objects**. Typically, the attributes are normalized to prevent one attribute from dominating the plot.
    - Useful when objects are **sorted according to class**.
    ![](https://i.imgur.com/f0igt2m.png)
    - Plots of **similarity** or **distance** matrices can also be useful for visualizing the relationships between objects. (The Matrix Plot below is the _Correlation Matrix_ Plot of Iris data.)
    ![](https://i.imgur.com/tBkXG59.png)
6. **Parallel Coordinates**
    - Instead of using perpendicular axes, use a set of parallel axes. Thus, **each object is represented as a line**.
    - Used to plot the attribute values of **high-dimensional** data
    ![](https://i.imgur.com/nRmv010.png)
    - Ordering of attributes is important in seeing such groupings
7. **Star Plots**
    ![](https://i.imgur.com/ccPP2rS.png)
8. **Chernoff Faces**
    ![](https://i.imgur.com/jajuDx7.png)


### Online Analytical Processing (OLAP)

An approach that uses a **multidimensional array representation** to answering **multi-dimensional analytical (MDA) queries** swiftly in computing.

- OLAP tools enable users to analyze multidimensional data interactively from multiple perspectives.
- OLAP consists of three basic analytical operations:
    - roll-up (consolidation)
    - drill-down
    - slicing
    - dicing
#### Create a Multidimensional Array
Convert tabular data into a multidimensional array:
- **Step 1.** _Identify_ which attributes are to be the _dimensions_ and which attribute is to be the _target attribute_.
    - The attributes used as dimensions must have _discrete_ values
    - The target value is typically _a count or continuous value_, e.g., the cost of an item, these are values appear as entries in the multidimensional array.
- **Step 2.** find the value of each entry in the multidimensional array by _summing the values (of the target attribute)_ or count of all objects that have the attribute values corresponding to that entry.
![](https://i.imgur.com/bmbTn8v.png)
![](https://i.imgur.com/QOBZPx8.png)

#### OLAP Operation - Data Cube
A data cube is a multidimensional representation of data, together with all possible **aggregates**.   We mean the aggregates that result by **selecting a proper subset of the dimensions** and **summing over all remaining dimensions**.
![](https://i.imgur.com/6MNZPPN.png)
![](https://i.imgur.com/yodwXWl.png)

#### OLAP Operation - Slicing and Dicing
- **Slicing** is selecting a group of cells from the entire multidimensional array by specifying a **_specific_** value for one or more dimensions.
    ![](https://i.imgur.com/ZhdAQf3.png)
- **Dicing** involves selecting a subset of cells by specifying a **_range_** of attribute values. 
    ![](https://i.imgur.com/RSzN0za.png)
- Both operations can also be accompanied by aggregation over some dimensions.

#### OLAP Operation - Roll-up and Drill-down
- **Roll-up** is performed by **_climbing up a concept hierarchy_** for the dimension location.
    ![](https://i.imgur.com/Giglpwz.png)
- **Drill-down** is performed by **_stepping down a concept hierarchy_** for the dimension time.
    ![](https://i.imgur.com/AXhGYaU.png)

---

## References
- [Similarities in Machine Learning](http://tiredapple.pixnet.net/blog/post/4757594)
- [Wiki - Online analytical processing](https://www.wikiwand.com/en/Online_analytical_processing)
- [Data Warehousing - OLAP](https://www.tutorialspoint.com/dwh/dwh_olap.htm)