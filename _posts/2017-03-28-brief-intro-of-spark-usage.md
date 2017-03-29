---
title: 'Brief Introduction of Spark Usage'
layout: post
tags:
  - Data-Mining
  - Spark
category: Notes
mathjax: false
---

Apache Spark is a fast and general-purpose cluster computing system. It provides high-level APIs in **Java, Scala, Python and R**, and an optimized engine that supports general execution graphs. It also supports a rich set of higher-level tools including Spark SQL for SQL and structured data processing, MLlib for machine learning, GraphX for graph processing, and Spark Streaming.


<!--more-->

- [Spark SQL and DataFrame](#spark-sql-and-dataframe)
- [Machine Learning Library](#machine-learning-library)
- [Graph Processing (GraphX)](#graph-processing-graphx)
- [SparkR (R on Spark)](#sparkr-r-on-spark)
- [Spark Streaming](#spark-streaming)

## Spark SQL and DataFrame

- DataFrames
    - Creating DataFrames
    - DataFrame Operations
    - Running SQL Queries Programmatically
- Data Sources
    - Saving to Persistent Tables
    - Partition Discovery
    - Schema Merging
    - Hive metastore Parquet table conversion
    - JSON Datasets
    - Hive Tables
    - Interacting with Different Versions of Hive Metastore
    - JDBC To Other Databases
- Performance Tuning
    - Caching Data In Memory
- Distributed SQL Engine
    - Running the Thrift JDBC/ODBC server
    - Running the Spark SQL CLI

For more detail, please refer to [Spark SQL and DataFrame Guide](https://spark.apache.org/docs/1.5.0/sql-programming-guide.html)

## Machine Learning Library

### spark-mllib: data types, algorithms, and utilities

- Basic statistics
    - summary statistics
    - correlations
    - stratified sampling
    - hypothesis testing
    - random data generation
- Classification and regression
    - linear models (SVMs, logistic regression, linear regression)
    - naive Bayes
    - decision trees
    - ensembles of trees (Random Forests and Gradient-Boosted Trees)
    - isotonic regression
- Collaborative filtering
    - alternating least squares (ALS)
- Clustering
    - k-means
    - Gaussian mixture
    - power iteration clustering (PIC)
    - latent Dirichlet allocation (LDA)
    - streaming k-means
- Dimensionality reduction
    - singular value decomposition (SVD)
    - principal component analysis (PCA)
- Feature extraction and transformation
- Frequent pattern mining
    - FP-growth
    - association rules
    - PrefixSpan
- Evaluation metrics
- PMML model export
- Optimization (developer)
    - stochastic gradient descent
    - limited-memory BFGS (L-BFGS)

For more detail, please refer to [Machine Learning Library (MLlib) Guide](https://spark.apache.org/docs/1.5.0/mllib-guide.html)

### spark-ml: high-level APIs for ML pipelines

spark-ml programming guide provides an overview of the Pipelines API and major concepts. It also contains sections on using algorithms within the Pipelines API, for example:

- Feature extraction, transformation, and selection
- Decision trees for classification and regression
- Ensembles
- Linear methods with elastic net regularization
- Multilayer perceptron classifier

For more detail, please refer to [Spark ML Programming Guide](https://spark.apache.org/docs/1.5.0/ml-guide.html)

## Graph Processing (GraphX)

- The Property Graph
- Graph Operators
    - Summary List of Operators
    - Property Operators
    - Structural Operators
    - Join Operators
    - Neighborhood Aggregation
        - Aggregate Messages (aggregateMessages)
        - Map Reduce Triplets Transition Guide (Legacy)
        - Computing Degree Information
        - Collecting Neighbors
- Pregel API
- Optimized Representation
- Graph Algorithms
    - PageRank
    - Connected Components
    - Triangle Counting

For more detail, please refer to [GraphX Programming Guide](https://spark.apache.org/docs/1.5.0/graphx-programming-guide.html)

## SparkR (R on Spark)

- Creating DataFrames
    - From local data frames
    - From Data Sources
    - From Hive tables
- DataFrame Operations
    - Selecting rows, columns
    - Grouping, Aggregation
    - Operating on Columns
- Running SQL Queries from SparkR

For more detail, please refer to [SparkR (R on Spark) Programming Guide](https://spark.apache.org/docs/1.5.0/sparkr.html)

## Spark Streaming

- Basics
- Linking
    - Initializing StreamingContext
    - Discretized Streams (DStreams)
    - Input DStreams and Receivers
    - Transformations on DStreams
    - Output Operations on DStreams
    - DataFrame and SQL Operations
    - MLlib Operations
    - Caching / Persistence
    - Checkpointing
    - Deploying Applications
    - Monitoring Applications
- Performance Tuning
    - Reducing the Batch Processing Times
    - Setting the Right Batch Interval
    - Memory Tuning

For more detail, please refer to [Spark Streaming Programming Guide](https://spark.apache.org/docs/1.5.0/streaming-programming-guide.html)