---
title: 'Spark MLlib Programming Practice with Airline Dataset'
layout: post
tags:
  - Data-Mining
  - Spark
  - Python
category: Programming
mathjax: true
---


In this document, I will build a predictive framework for predicting whether each flight in `2006` will be cancelled or not by using the data from `2000` to `2005` as training data.

Items to be delivered in this document includes:

1. Show the predictive framework you designed. What features do you extract? What algorithms do you use in the framework?
2. Explain the validation method you use.
3. Explain the evaluation metric you use and show the effectiveness of your framework (i.e., use confusion matrix)
4. Show the validation results and give a summary of results. 

<!--more-->

## I. Analytic Tool

`Apache Spark™` is an unified analytics engine for large-scale data processing.   To deploy Spark program on Hadoop Platform, you may choose either one program language from Java, Scala, and Python.   In this document, I will use `Python` Language to implement Spark programs.

`MLlib`/`ML` is Spark’s machine learning (ML) library. Its goal is to make practical machine learning scalable and easy. At a high level, it provides tools such as:

1. **ML Algorithms**: common learning algorithms such as classification, regression, clustering, and collaborative filtering
2. **Featurization**: feature extraction, transformation, dimensionality reduction, and selection
3. **Pipelines**: tools for constructing, evaluating, and tuning ML Pipelines
4. **Persistence**: saving and load algorithms, models, and Pipelines
5. **Utilities**: linear algebra, statistics, data handling, etc.

Here are some useful links for the programming guide of Spark’s machine learning (ML) library and its model evaluation metrics:

- [Machine Learning Library (MLlib) Guide](https://spark.apache.org/docs/latest/ml-guide.html)
- [Spark ML Programming Guide](https://spark.apache.org/docs/1.3.0/ml-guide.html)
- [pyspark.ml package](https://spark.apache.org/docs/latest/api/python/pyspark.ml)
- [pyspark.mllib package](https://spark.apache.org/docs/latest/api/python/pyspark.mllib)
- [Extracting, transforming and selecting features](https://spark.apache.org/docs/2.1.0/ml-features.html)
- [Feature Extraction and Transformation - RDD-based API](https://spark.apache.org/docs/2.1.0/mllib-feature-extraction.html)
- [Evaluation Metrics - RDD-based API](https://spark.apache.org/docs/2.2.0/mllib-evaluation-metrics.html)
- [ML Tuning: model selection and hyperparameter tuning](https://spark.apache.org/docs/2.2.0/ml-tuning.html)
- [Create a custom Transformer in PySpark ML](https://stackoverflow.com/questions/32331848/create-a-custom-transformer-in-pyspark-ml)

```python
%pyspark

from pyspark import SparkContext 
from pyspark.sql import SQLContext
import pyspark.sql.functions as F
from pyspark.sql.types import *
from pyspark.ml.classification import LogisticRegression
from pyspark.mllib.util import MLUtils
from pyspark.ml.feature import OneHotEncoder, StringIndexer, StandardScaler, Imputer, VectorAssembler, SQLTransformer
from pyspark.mllib.evaluation import BinaryClassificationMetrics, MulticlassMetrics
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder, CrossValidatorModel
from pyspark.ml.evaluation import BinaryClassificationEvaluator
from pyspark.ml import Pipeline, PipelineModel
from pyspark.ml.linalg import Vectors
```



## II. Dataset

[Airline on-time performance dataset](http://stat-computing.org/dataexpo/2009/) consists of flight arrival and departure details for all commercial flights within the USA, from October 1987 to April 2008. This is a large dataset: there are nearly **120 million records** in total, and takes up 1.6 gigabytes of space compressed and **12 gigabytes** when uncompressed. 


### A. Supplement Data

If you need further information, the [supplement data](http://stat-computing.org/dataexpo/2009/supplemental-data.html) describes the locations of US airports, listing of carrier codes, information about individual planes, and meteorological data.


### B. Flight Data

The Schema of the flight data is as follows

- Year int,
- Month int,
- DayofMonth int,
- DayOfWeek int,
- DepTime  int,
- CRSDepTime int,
- ArrTime int,
- CRSArrTime int,
- UniqueCarrier varchar(5),
- FlightNum int,
- TailNum varchar(8),
- ActualElapsedTime int,
- CRSElapsedTime int,
- AirTime int,
- ArrDelay int,
- DepDelay int,
- Origin varchar(3),
- Dest varchar(3),
- Distance int,
- TaxiIn int,
- TaxiOut int,
- Cancelled int,
- CancellationCode varchar(1),
- Diverted varchar(1),
- CarrierDelay int,
- WeatherDelay int,
- NASDelay int,
- SecurityDelay int,
- LateAircraftDelay int

Here the flight dataset from `2000` to `2006` is downloaded to the directory `../data/` using the shell command `wget`.


```sh
%sh

mkdir ../data
mkdir ../data/train
mkdir ../data/test

for i in 2000 2001 2002 2003 2004 2005
do
  wget -nv -O ../data/train/$i.csv.bz2 http://stat-computing.org/dataexpo/2009/$i.csv.bz2
  bzip2 -d ../data/train/$i.csv.bz2
done

wget -nv -O ../data/test/2006.csv.bz2 http://stat-computing.org/dataexpo/2009/2006.csv.bz2
bzip2 -d ../data/test/2006.csv.bz2
```


## III. Program Workflow & Execution Commands

The program workflow is shown in the figure below, which will be elaborated in the next section.

![](https://i.imgur.com/fyoKt5K.png)


To execute the program `main.py` (which includes both training and testing phase), the following command is used.

```sh
$ pyspark main.py --packages com.databricks:spark-csv_2.10:1.5.0 \
                  --driver-memory=2g --executor-memory=7g \
                  --num-executors=8 --executor-cores=4 \
                  --conf "spark.storage.memoryFraction=1" \
                  --conf "spark.akka.frameSize=200" \
                  --conf "spark.default.parallelism=100" \
                  --conf "spark.core.connection.ack.wait.timeout=600" \
                  --conf "spark.yarn.executor.memoryOverhead=2048" \
                  --conf "spark.yarn.driver.memoryOverhead=400"
```

Note that the followings are required in advance.
- Make input/output directories on HDFS.

```
$ hadoop fs -mkdir HW4/input
$ hadoop fs -mkdir HW4/input/train
$ hadoop fs -mkdir HW4/input/test
$ hadoop fs -mkdir HW4/output
$ hadoop fs -mkdir HW4/output/model
$ hadoop fs -mkdir HW4/output/preprocessor
$ hadoop fs -mkdir HW4/output/results
```

- Copy the input files (airline dataset) to input directory on HDFS (`HW4/input/train` and `HW4/input/test`).

```
$ hadoop fs -copyFromLocal ../data/train/*.csv HW4/input/train
$ hadoop fs -copyFromLocal ../data/test/*.csv HW4/input/test
```

- Make a logging ouput directory in local file system (`./log`)

```
$ mkdir log
```

## IV. The Predictive Framework

Here I will build a predictive framework for predicting whether each flight in `2006` will be cancelled or not by using the data from `2000` to `2005` as training data items.


In this framework, I utilize Spark's `Pipelines` and `PipelineModels`, which help to ensure that training and test data go through identical feature processing steps.


### A. Training Phase

In the training phase, only training data can be seen.   So we will use `k-fold` cross validation to help select the best parameters and also evaluate the performance of the model we trained.

The overview of our training process in shown in the figure below.

<img style="width:350px" src="https://i.imgur.com/KSj2sHQ.png">


**Step 1. Load the Training Data**



In the flight dataset, some columns are not appropriate to use as attributes if we want to predict whether a flight will be cancelled or not.    For example, if we use `CancellationCode` as an attribute, then we can always succesfully "predict" if a flight is cancelled or not.   Actually, to prevent **data leakage problem**, we should not use any attributes that cannot be known before the flight takes place.

That is why in this step we first select some columns.   Afterwards, we also determine which columns are categorical columns and which are not.   To deal with missing values, the imputation method we use is stated as below.

- Categorical Attributes: use the `highest-occurences` value of each attribute
- Numerical Attributes: use the `mean` value of each attribute

```python
%pyspark

# List numerical features & categorical features
target_col = "Cancelled"
use_cols = ['Year', 'Month', 'DayofMonth', 'DayOfWeek', 'CRSDepTime', 
            'CRSArrTime', 'UniqueCarrier', 'FlightNum', 'TailNum', 
            'CRSElapsedTime', 'Origin', 'Dest', 'Distance', 
            'TaxiIn', 'TaxiOut', 'Cancelled']
cate_cols = ["Month", 'DayOfWeek', 'UniqueCarrier', 'FlightNum', 
            'TailNum', 'Origin', 'Dest']
num_cols = list(set(use_cols) - set(cate_cols) - set([target_col]))

# Load Training Data
df = load(opt.train)  

def load(path):
    # Load DataFrame
    df = sqlContext.read.format('com.databricks.spark.csv')\
                    .options(header='true')\
                    .load(path)

    # Select useful columns (drop columns that should not be known 
    # before the flight take place) 
    df = df.select(use_cols)

    # Impute numerical features
    for col in num_cols:
        df = df.withColumn(col, df[col].cast('double'))
        mu = df.select(col).agg({col:'mean'}).collect()[0][0]
        df = df.withColumn(col, F.when(df[col].isNull(), mu)\
                           .otherwise(df[col]))
    df = df.withColumn('label', df[target_col].cast('double'))
    df = df.filter(df['label'].isNotNull())

    # Impute categorical features
    for col in cate_cols:
        frq = df.select(col).groupby(col).count()\
                            .orderBy('count', ascending=False) \
                            .limit(1).collect()[0][0]
        df = df.withColumn(col, F.when((df[col].isNull() | 
                                       (df[col] == '')), frq) \
                                .otherwise(df[col]))

    # Assure there is no missing values
    for col in num_cols + cate_cols + ['label']:
        assert df.filter(df[col].isNull()).count() == 0, 
                        "Column '{}' exists NULL value(s)".format(col)
        assert df.filter(df[col] == '').count() == 0, 
                        "Column '{}' exists empty string(s)".format(col)

    return df
```

**Step 2. Create a Pre-Processor to Extract Features from Training Data**

To pre-process both numerical attributes and categorical attributes, the following feature extraction/transformation processes are used successively.

1. `StringIndexer`: StringIndexer encodes a string column of labels to a column of label indices.
2. `OneHotEncoder`: One-hot encoding **maps a column of label indices to a column of binary vectors, with at most a single one-value**. This encoding allows algorithms which expect continuous features, such as Logistic Regression, to use categorical features.
3. `VectorAssembler`: VectorAssembler is a transformer that **combines a given list of columns into a single vector column**.
4. `StandardScaler`: StandardScaler transforms a dataset of Vector rows, **normalizing each feature** to have unit standard deviation and/or zero mean. It takes parameters:
    - _withStd_: True by default. Scales the data to unit standard deviation.
    - *withMean*: False by default. Centers the data with mean before scaling. It will build a dense output, so this does not work on sparse input and will raise an exception.

After defining these feature extractor/tranformer, we create a `PipelineModel` by concatenate them and apply it on the training data to extract features.

```python
%pyspark

# Pre-Process
preprocessor = gen_preprocessor(df)   
df = preprocessor.transform(df) 

# Save Pre-Processor for later usage
preprocessor.save("{}/preprocessor".format(opt.output))

def gen_preprocessor(df):
    # String Indexing for categorical features
    indexers = [StringIndexer(inputCol=col, 
                              outputCol="{}_idx".format(col)) \
                              for col in cate_cols]
    
    # One-hot encoding for categorical features
    encoders = [OneHotEncoder(inputCol="{}_idx".format(col), 
                              outputCol="{}_oh".format(col)) \
                              for col in cate_cols]

    # Concat Feature Columns
    assembler = VectorAssembler(inputCols = num_cols + \
                            ["{}_oh".format(col) for col in cate_cols], 
                            outputCol = "_features")
    
    # Standardize Features
    scaler = StandardScaler(inputCol='_features', 
                            outputCol='features', 
                            withStd=True, withMean=False)

    preprocessor = Pipeline(stages = indexers + encoders + \
                                     [assembler, scaler]).fit(df)

    return preprocessor
```




**Step 3. Train Logistic Regression Model**

In our predictive framework, the model we use is `Logistic Regression Classifier`, which is widely used to predict a binary response.   In statistics, the logistic model is a statistical model with input (independent variable) a continuous variable and output (dependent variable) a binary variable, where a unit change in the input multiplies the odds of the two possible outputs by a constant factor.   For binary classification problems, the algorithm outputs a binary logistic regression model.

In `spark.ml`, two algorithms have been implemented to solve logistic regression: _mini-batch gradient descent_ and _L-BFGS_.    **L-BFGS** is used in our predictive framework for faster convergence.

Besides the fact that we have decided the model to be used, we also need to find its best parameters for a given task.

We tackle this _tuning_ task using `CrossValidator`, which takes an _Estimator_ (i.e., logistic regression in this case), a set of _ParamMaps_ (i.e., regularization parameter (>= 0) of logistic regression model in this case), and an _Evaluator_ (i.e. area under precision-recall curve in this case).

`CrossValidator` begins by splitting the dataset into a set of folds which are used as separate training and test datasets.   For example, with `k=3` folds, CrossValidator will generate 3 (training, test) dataset pairs, each of which uses 2/3 of the data for training and 1/3 for testing.   For each ParamMap, `CrossValidator` trains the given Estimator and evaluates it using the given Evaluator.    Note that we use `k=10` in our predictive framework (10-fold cross validation).

```python
%pyspark

# Logistic Regression Classifier
lr = LogisticRegression(maxIter=10^5)
    
# Train the 10-fold Cross Validator
cvModel = CrossValidator(estimator=Pipeline(stages = [lr]),
            estimatorParamMaps=ParamGridBuilder() \
                                .addGrid(lr.regParam, [0.1, 0.01]) \
                                .build(),
            evaluator=BinaryClassificationEvaluator(metricName='areaUnderPR'),
            numFolds=10).fit(df)

# Save the best model for later usage
cvModel.bestModel.save("{}/model".format(opt.output))
```



**Step 4.Evaluate the Model Trained with 10-fold Cross Validation**

After the `Logistic Regression Classifier` is trained and the best parameters are chosen, we can evaluate the performance of this model on the training data.

The evaluation metrics we use are

- area under ROC (receiver operating characteristic curve)
- area under Precision-Recall curve
- confusion matrix
- overall precision
- overall recall
- overall f1 score
- precision, recall, and f1 score for each class

<img style="width:300px" src="https://i.imgur.com/yqsVGVt.png">

```python
%pyspark

# Evaluate on Training Set
predictionAndLabels = cvModel.transform(df)
log = evaluate(predictionAndLabels)
with open('{}/train.json'.format(opt.log), 'w') as f:
    json.dump(log, f)
    
def evaluate(predictionAndLabels):
    log = {}

    # Show Validation Score (AUROC)
    evaluator = BinaryClassificationEvaluator(metricName='areaUnderROC')
    log['AUROC'] = "%f" % evaluator.evaluate(predictionAndLabels)    
    print("Area under ROC = {}".format(log['AUROC']))

    # Show Validation Score (AUPR)
    evaluator = BinaryClassificationEvaluator(metricName='areaUnderPR')
    log['AUPR'] = "%f" % evaluator.evaluate(predictionAndLabels)
    print("Area under PR = {}".format(log['AUPR']))

    # Metrics
    predictionRDD = predictionAndLabels.select(['label', 'prediction']) \
                            .rdd.map(lambda line: (line[1], line[0]))
    metrics = MulticlassMetrics(predictionRDD)

    # Confusion Matrix
    print(metrics.confusionMatrix().toArray())

    # Overall statistics
    log['precision'] = "%s" % metrics.precision()
    log['recall'] = "%s" % metrics.recall()
    log['F1 Measure'] = "%s" % metrics.fMeasure()
    print("[Overall]\tprecision = %s | recall = %s | F1 Measure = %s" % \
            (log['precision'], log['recall'], log['F1 Measure']))

    # Statistics by class
    labels = [0.0, 1.0]
    for label in sorted(labels):
        log[label] = {}
        log[label]['precision'] = "%s" % metrics.precision(label)
        log[label]['recall'] = "%s" % metrics.recall(label)
        log[label]['F1 Measure'] = "%s" % metrics.fMeasure(label, 
                                                           beta=1.0)
        print("[Class %s]\tprecision = %s | recall = %s | F1 Measure = %s" \
                  % (label, log[label]['precision'], 
                    log[label]['recall'], log[label]['F1 Measure']))

    return log
```


### B. Testing Phase

In the testing phase, I will use the logistic regression classifier trained on the training data (flights in `2001~2005`) to make predictions with the testing data (flights in `2006`).

The overview of our training process in shown in the figure below.


![](https://i.imgur.com/cmmSNaw.png)


Note that we will use the same pre-processor to make sure the testing data go through the same feature processing steps.

**Step 1. Load Testing Data**

Here we load the testing data and select the same columns as we use in the training phase.

Note that we impute the numerical attributes and categorical attributes by the `mean` values and `highest-occurrences` values in the testing data (instead of that in the training data) respectively.

```python
%pyspark

# Load Testing Data
df = load(opt.test) 
```

**Step 2. Extract Features by Applying the Pre-Processor on Testing Data**

Here we use the same preprocessor we use in the training phase to pre-process the testing data.

However, the problem is that there may be unseen labels that did not appear in training data.   To deal with it, we simply filter out those rows and apply the preprocessor on the testing data afterwards.

```python
%pyspark

# Pre-Process
preprocessor = PipelineModel.load(opt.preprocessor)
df = preprocess(df, preprocessor)

def preprocess(df, preprocessor):
    dic = {x._java_obj.getInputCol(): 
            [lab for lab in x._java_obj.labels()] \
            for x in preprocessor.stages \
            if isinstance(x, StringIndexerModel)}

    # Filter out unseen labels 
    for col in cate_cols:
        df = df.filter(F.col(col).isin(dic[col]))
    
    # Assure there is no unseen values
    for col in cate_cols:
        assert df.filter(F.col(col).isin(dic[col]) == False).count() == 0, 
                "Column '{}' exists unseen label(s)".format(col)
    
    df = preprocessor.transform(df)
    return df
```

**Step 3. Make Predictions Using our Logistic Regrssion Classifier**

Next, we predict whether a flight will be cancelled or not in `2006` using the logistic regression classifier we trained on the trainig data (flights in `2001~2005`).

```python
%pyspark

# Logistic Regression Classification
cvModel = PipelineModel.load(opt.model)
predictionAndLabels = cvModel.transform(df)

# Save Prediction Results
predictionAndLabels.write.format('json')\
                   .save("{}/results".format(opt.output))
```

**Step 4. Evaluate the Testing Performance**

Lastly, we use the following metrics to evaluate the testing performance.

- area under ROC (receiver operating characteristic curve)
- area under Precision-Recall curve
- confusion matrix
- overall precision
- overall recall
- overall f1 score
- precision, recall, and f1 score for each class

```python
%pyspark

# Evaluate on Testing Set
log = evaluate(predictionAndLabels)
with open('{}/test.json'.format(opt.log), 'w') as f:
    json.dump(log, f)
```



## V. Validation Method, Evaluation Metric, and Results

In this section, I will show how well our predictive framework works on both training data and testing data.

### A. Model Validation on Training Set

The validation method we used in our predictive framework is `10-fold Cross Validation`.

Cross-validation is a model validation technique for assessing how the results of a statistical analysis will generalize to an independent data set. It is mainly used in settings where the goal is prediction, and one wants to estimate how accurately a predictive model will perform in practice.

The result of model validation on training data is shown as below.

<img style="width:45%" src="res/cm.train.1.png"><img style="width:45%" src="res/cm.train.2.png">

By checking the confusion matrix, we can see that

1. There is a data imbalence probelm that the number of Class 0.0(`Not Cancelled`) is about `43` times larger than that of Class 1.0(`Cancelled`).
2. Among all `Cancelled` flights, about `69%` are correctly predicted as `Cancelled`.
3. Among all flights being predicted as `Cancelled`, more than `99%` are truely `Cancelled` flights.

|  | Precision | Recall | F1 Score | Area under ROC | Area under PR |
| - | - | - | - | - | - |
| Overall | 0.992945546078 | 0.992945546078 | 0.992945546078 | 0.981035 | 0.817182 |
| Class 0.0 <br>(`Not Cancelled`) | 0.992847084173 | 0.999987728664 | 0.996404613385 |
| Class 0.0 <br>(`Cancelled`) | 0.999223200859 | 0.686622491843 | 0.813940596166 |

Given the evaluation metrics, the validation results show that

1. The overall precision and recall are both good (> 0.99)
2. The Class 0.0(`Not Cancelled`) precision and recall are both good as well (> 0.99)
3. The Class 1.0(`Not Cancelled`) precision is good (> 0.99) but iits recall is a little worse (0.68) due to the data imbalence problem.

<!--```sh
[CRSElapsedTime] # missing values: 270
[TailNum] # missing values: 56387
```-->

### B. Evaluation on Testing Set

<img style="width:45%" src="https://i.imgur.com/AOerFBN.png"><img style="width:45%" src="https://i.imgur.com/8xmKsSo.png">

By checking the confusion matrix, we can see that

1. There is a data imbalence probelm that the number of Class 0.0(`Not Cancelled`) is about `58` times larger than that of Class 1.0(`Cancelled`).
2. Among all `Cancelled` flights, about `75%` are correctly predicted as `Cancelled`.
3. Among all flights being predicted as `Cancelled`, more than `99%` are truely `Cancelled` flights.


|  | Precision | Recall | F1 Score | Area under ROC | Area under PR |
| - | - | - | - | - | - |
| Overall | 0.995517407226 | 0.995517407226 | 0.995517407226 | 0.979634 | 0.826068 |
| Class 0.0 <br>(`Not Cancelled`) | 0.995478844317 | 0.999982376073 | 0.997725528213 |
| Class 0.0 <br>(`Cancelled`) | 0.9985983131 | 0.734367717185 | 0.846338994256 |

Given the evaluation metrics, the evaluation results show that

1. The overall precision and recall are both good (> 0.99)
2. The Class 0.0(`Not Cancelled`) precision and recall are both good as well (> 0.99)
3. The Class 1.0(`Not Cancelled`) precision is good (> 0.99) but iits recall is a little worse (0.73) due to the data imbalence problem.



<!--
|  | UniqueCarrier | FlightNum | TailNum | Origin | Dest |
| - | - | - | - | - | - |
| Number of Unseen Labels | 304764 | 28921 | 482600 | 7267 | 7289 |
```sh
[CRSElapsedTime] # missing values: 4
[UniqueCarrier] # unseen labels: 304764
[FlightNum] # unseen labels: 28921
[TailNum] # unseen labels: 482600
[Origin] # unseen labels: 7267
[Dest] # unseen labels: 7289
```-->

## VI. Discussion

In the process of building up the predictive framework in this document, the first trouble that I came across was to understand the difference between `ml` package and `mllib` package.   I was using `mllib` packages, which is RDD-based, until I realize that `ml` packages, which is Dataset-based, actually provides a uniform set of **high-level** APIs.    I later decided to use `mlllib` packages, which can help create and tune practical machine learning pipelines.

Another problem I met was the version problem.   I was originally testing my program on my own computer, which uses Spark 2.2.0, with a small subset of the trainig/testing data.    By Spark version 2.2.0, the selection of feature transformer is much wiser (e.g., `Imputer`, `SQLTransformer`) and the functionalities of `PipelineModel` class is more complete (e.g., `save()`, `load()`).   So it really caused me troubles when I need to run my program on a platform that uses Spark 1.5.0.   I need to seek for alternatives for some functionalities that Spark 1.5.0 hasn't provided yet.

The last issue I encountered was the "Exexcutor Lost Error".

```sh
ERROR cluster.YarnScheduler: Lost executor ... on ...: 
                             remote Rpc client disassociated
```

Initially I was thinking it of a random error, so I just execute my program again.   However, this error was reproduced again and again.   I eventually found out that it is because that we were not leaving sufficient memory for YARN itself and containers were being killed because of it.   Thus, we should solve this issue by introducing some configuration parameters `spark.akka.frameSize` and `spark.executor.memoryOverhead`.

`spark.akka.frameSize` controls maximum message size (in MB) to allow in "control plane" communication (defualt: `128`). It generally only applies to map output size information sent between executors and the driver. We should increase this because we are running jobs with many thousands of map and reduce tasks for the sake of large amount data we are using.

```sh
--conf spark.akka.frameSize=200
```

`spark.executor.memoryOverhead` controls the amount of off-heap memory to be allocated per executor, in MiB unless otherwise specified (default: `driverMemory*0.1`, with minimum `384`). This is memory that accounts for things like VM overheads, interned strings, other native overheads, etc. This tends to grow with the executor size (typically 6-10%).   So that's why we should set this value a lot higher than default to prevent executor lost error.

```sh
--conf spark.yarn.executor.memoryOverhead=2048
```



## VII. References

- [Logistic regression - Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)
- [Ngoc Tran - Precision, Recall, Sensitivity and Specificity](https://newbiettn.github.io/2016/08/30/precision-recall-sensitivity-specificity/)
- [Cross-validation (statistics) - Wikipedia](https://en.wikipedia.org/wiki/Cross-validation_(statistics))
- [Predicting Breast Cancer Using Apache Spark Machine Learning Logistic Regression](https://mapr.com/blog/predicting-breast-cancer-using-apache-spark-machine-learning-logistic-regression/)