---
title: 'Spark Programming Practice on Hadoop Platform'
layout: post
tags:
  - Data-Mining
  - Spark
  - Hadoop
  - Python
category: Programming
mathjax: true
---

The goal of this document is to practice Spark programming on Hadoop platform with the following problems. 

1. In the text file (`Youvegottofindwhatyoulove.txt`), show the **top 30 most frequent occurring words** and their **average occurrences in a sentence**   According to the result, what are the characteristics of these words?
2. Implement a program to calculate the **average amount in credit card trip for different number of passengers** which are from one to four passengers in **2017.09** NYC Yellow Taxi trip data. In NYC Taxi data, the "Passenger_count" is a driver-entered value. Explain also how you **deal with the data loss issue**.
3. For each of the above task 1 and 2, **compare the execution time on local worker and yarn cluster**. Also, give some discussions on your observation.  

<!--more-->

## Spark Programming

Apache Spark™ is a unified analytics engine for large-scale data processing.   To deploy Spark program on Hadoop Platform, you may choose either one program language from Java, Scala, and Python.   In this document, I will use `Python` Language to implement Spark programs for answering the questions mentioned above.


```python
%pyspark

from pyspark import SparkContext 
from pyspark.sql import SQLContext
import pyspark.sql.functions as F
from pyspark.sql.types import *
```

Here are some useful links for submitting spark programs and spark programing in Python:

- [Launching Applications with spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)
- [Spark RDD Programming Guide](https://spark.apache.org/docs/latest/rdd-programming-guide.html#rdd-operations)
- [Spark SQL, DataFrames and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)
- [pyspark Package Documnet API](https://spark.apache.org/docs/latest/api/python/pyspark.html)



## Q1. Implement a program to calculate the average occurrences of each word in a sentence.

The source code that is used for answering this question is written in `q1.py` and the **execution commands** are listed as belows.

1. **On Local Worker**

```sh
$ hadoop fs -rm -r HW3/output/*
$ time pyspark q1.py --master local[*]
$ rm -r ./output/q1
$ hadoop fs -copyToLocal HW3/output/q1 ./output/
$ cat ./output/q1/part-* | head -30
```

2. **On Yarn Cluster**

```sh
$ hadoop fs -rm -r HW3/output/*
$ time pyspark q1.py --master yarn \
                     --deploy-mode cluster
$ rm -r ./output/q1
$ hadoop fs -copyToLocal HW3/output/q1 ./output/
$ cat ./output/q1/part-* | head -30
```


Note that the followings are required in advance.

- Make input/output directories (`HW3/`, `HW3/input`, and `HW3/output`) on HDFS.
- Copy the input file (`Youvegottofindwhatyoulove.txt`) to input directory on HDFS (`HW3/input`).
- Make an ouput directory in local file system (`./output`)


### A. Show the top 30 most frequent occurring words and their average occurrences in a sentence.

Here we will walk through the program workflow and also the answers to this question.


In the beginning, we load the input file and count the number of centences.
```python
%pyspark

conf = SparkConf().setAppName("HW3 Q1")
sc = SparkContext(conf=conf)
text_file = sc.textFile("HW3/input/Youvegottofindwhatyoulove.txt")
sents = text_file.filter(lambda line: len(line)>0)
N = sc.broadcast(sents.count())
```

Then we can count the words in file and sort these words by their counts in descending order.

```python
%pyspark

counts = text_file.flatMap(lambda line: line.split(' ')) \
             .filter(lambda word: len(word)>0 and word != '\x00') \
             .map(lambda word: (word, 1)) \
             .reduceByKey(lambda a, b: a + b) \
             .map(lambda x: (x[1], x[0])) \
             .sortByKey(ascending=False) \
             .map(lambda x: (x[1], x[0], x[0]/N))
```

So now we can list the top 30 frequent words in the input file along with their counts.

```python
%pyspark

print("Word\tFrequencies\tOccurrence per Sentence")
print("----\t-----------\t----------------------")
for w, cnt, occur in counts.take(30):
    print("{}\t{}\t\t\t{}".format(w, cnt, freq))
```

```
Word	Frequencies Occurrence per Sentence
----	----------- -----------------------
the	91          0.8666666666666667
I	85          0.8095238095238095
to	71          0.6761904761904762
and	49          0.4666666666666667
was	47          0.44761904761904764
a	46          0.4380952380952381
of	40          0.38095238095238093
that	38          0.3619047619047619
in	33          0.3142857142857143
is	29          0.2761904761904762
you	27          0.2571428571428571
it	27          0.2571428571428571
my	25          0.23809523809523808
had	22          0.20952380952380953
t	19          0.18095238095238095
It	18          0.17142857142857143
with	18          0.17142857142857143
have	17          0.1619047619047619
for	17          0.1619047619047619
And	17          0.1619047619047619
all	16          0.1523809523809524
your	14          0.13333333333333333
out	14          0.13333333333333333
as	14          0.13333333333333333
what	14          0.13333333333333333
from	13          0.12380952380952381
be	13          0.12380952380952381
on	12          0.11428571428571428
me	12          0.11428571428571428
at	11          0.10476190476190476
```

### B. According to the result, what are the characteristics of these words?

Almost all of the top frequent words are stop words.   According to Wikipedia, "Stop words" usually refers to the most common words in a language, for instance, short function words such as "the", "is", "at", "which", and "on". 

Note that the 15th frquent word "t" is actually the acronym of "not", which is also a stop word.

Another characteristic that is noteworthy for these frequent words is that these words are all no longer than `4` characters.   It makes sense that we tend to use short words frequently in articles.

## Q2. Implement a program to calculate the **average amount in credit card trip for different number of passengers**

In this implementation, we are going to use **2017.09** [NYC Yellow Taxi trip data](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) and consider the cases from one to four passengers.

The source code that is used for answering this question is written in `q2.py` and the **execution commands** are listed as belows.

1. **On Local Worker**

```sh
$ hadoop fs -rm -r HW3/output/*
$ time pyspark q2.py --packages com.databricks:spark-csv_2.10:1.5.0 \
                     --master local[*]
$ rm -r ./output/q2
$ hadoop fs -copyToLocal HW3/output/q2 ./output/
$ cat ./output/q2/part-*
```

2. **On Yarn Cluster**

```sh
$ hadoop fs -rm -r HW3/output/*
$ time pyspark q2.py --packages com.databricks:spark-csv_2.10:1.5.0 \
                    --master yarn \
                    --deploy-mode cluster
$ rm -r ./output/q2
$ hadoop fs -copyToLocal HW3/output/q2 ./output/
$ cat ./output/q2/part-*
```
Note that the followings are required in advance.

- Make input/output directories (`HW3/`, `HW3/input`, and `HW3/output`) on HDFS.
- Copy the input file (`yellow_tripdata_2017-09.csv`) to input directory on HDFS (`HW3/input`).
- Make an ouput directory in local file system (`./output`)



### A. Deal with Data Loss Issue

In NYC Taxi data, the "Passenger_count" is a driver-entered value.   So some of the records may miss the "Passenger_count" value since the driver didn't report it.

Again we load our data first.

```python
%pyspark

conf = SparkConf().setAppName("HW3 Q2")
sc = SparkContext(conf=conf)

# Load DataFrame
sqlContext = SQLContext(sc)
df = sqlContext.read.format('com.databricks.spark.csv')\
                    .options(header='true')\
                    .load('HW3/input/yellow_tripdata_2017-09.csv')

# Show Schema
df.printSchema()
```

```
root
 |-- VendorID: string (nullable = true)
 |-- tpep_pickup_datetime: string (nullable = true)
 |-- tpep_dropoff_datetime: string (nullable = true)
 |-- passenger_count: string (nullable = true)
 |-- trip_distance: string (nullable = true)
 |-- RatecodeID: string (nullable = true)
 |-- store_and_fwd_flag: string (nullable = true)
 |-- PULocationID: string (nullable = true)
 |-- DOLocationID: string (nullable = true)
 |-- payment_type: string (nullable = true)
 |-- fare_amount: string (nullable = true)
 |-- extra: string (nullable = true)
 |-- mta_tax: string (nullable = true)
 |-- tip_amount: string (nullable = true)
 |-- tolls_amount: string (nullable = true)
 |-- improvement_surcharge: string (nullable = true)
 |-- total_amount: string (nullable = true)

```

We can see that there are totally `8945459` records in September 2017.

```python
%pyspark

df.count()
```

```
8945459
```

In these records, we print a table to show the proportion of records with different number of passengers.


```python
%pyspark

df.groupby('passenger_count').count().show(15)
```

```
+---------------+-------+
|passenger_count|  count|
+---------------+-------+
|              7|     27|
|              3| 379793|
|              8|     34|
|              0|  14658|
|              5| 419504|
|              6| 268073|
|              9|     35|
|              1|6361291|
|              4| 173538|
|              2|1328506|
+---------------+-------+

```




The proportion of records with `1` passenger is `6361291/8945459=0.71`, which is really a high proportion.
That is the reason why I am going to **replace the missing values in `passenger_count` with `1`**.


```python
%pyspark

# Deal with missing values
df = df.withColumn("passenger_count", 
                    F.when((df["passenger_count"] == "0"), 1) \
                            .otherwise(df["passenger_count"]))
```

To make sure no missing values left in the columns we need (`passenger_count` and `total_amount`), we check it by the follwoing command.

```python
%pyspark

df.filter((df["passenger_count"] == "0") | \
          df["passenger_count"] == "") | \
          df["passenger_count"].isNull() | \
          F.isnan(df["passenger_count"]) | \
          (df["total_amount"] == "") | \
          df["total_amount"].isNull() | \
          F.isnan(df["total_amount"])).count()
```

```
0
```

### B. Report the **average amount in credit card trip** for different number of passengers

To answer the question, we select only the credit card trips.

```python
%pyspark

df = df.filter(df["payment_type"] == "1") \
       .select("passenger_count", "total_amount", "trip_distance")

df.show(5)
```

```
+---------------+------------+-------------+
|passenger_count|total_amount|trip_distance|
+---------------+------------+-------------+
|              1|         4.8|          .40|
|              2|        7.55|          .90|
|              1|        11.3|         1.50|
|              1|         8.8|          .90|
|              1|       31.56|         6.34|
+---------------+------------+-------------+
only showing top 5 rows

```

So that we can count the amounts of credit card trips for different number of `passenger_count`.


```python
%pyspark

average_amounts = df.groupby('passenger_count')\
                    .agg({"total_amount": "average", 
                          "trip_distance": "average"})
average_amounts.show(15)
```

```
+---------------+------------------+------------------+
|passenger_count| avg(total_amount)|avg(trip_distance)|
+---------------+------------------+------------------+
|              7| 65.42318181818182| 6.298181818181818|
|              3|18.156634185770997|3.1448369322168106|
|              8|             67.45| 5.343461538461539|
|              5|18.214794738199902|3.2324018079526002|
|              6|18.039043013855164| 3.194390377953453|
|              9| 68.78840000000001|6.5527999999999995|
|              1| 17.95413006221656| 3.087874226428824|
|              4|18.317789057005374|3.1764583112617832|
|              2|18.773330411916394| 3.287393844707793|
+---------------+------------------+------------------+
```

That is, the conclusion comes to

- `1`-passengers credit card trips: `17.95` dollars / `3.08` miles per trip in average.
- `2`-passengers credit card trips: `18.77` dollars / `3.28` miles per trip in average.
- `3`-passengers credit card trips: `18.16` dollars / `3.14` miles per trip in average.
- `4`-passengers credit card trips: `18.32` dollars / `3.17` miles per trip in average.

Note that the average amount for `2`-passengers credit card trips is much larger (`0.82`, `0.62`, and `0.46` dollars larger than `1`-, `3`-, and `4`-passengers credit card trips respectively).

However, if you also check the `trip_distance` values, you'll see that the average amounts are positive related to their average distance as expected.

## Q3. Discuss the Execution Time on local worker and yarn cluster

When we set `--master local[*]`, we run Spark locally with as many worker threads as logical cores on your machine (in this document we calculate the execution time with `--master local[1]` and `--master local[8]`).   On the other hand, when `--master yarn --deploy-mode cluster` is set, we connect to a `YARN` cluster in `cluster` mode where the cluster location will be found based on the `HADOOP_CONF_DIR` or `YARN_CONF_DIR` variable.


For the first implementation (calculate the average occurrences of each word in a sentence in the attached article `Youvegottofindwhatyoulove.txt`), the execution time in different deploy mode is shown in the table below.

|  | real | user | sys |
| - | - | - | - |
| on local worker (1 core) | 0m8.002s | 0m12.581s | 0m2.445s |
| on local worker (8 cores) | 0m21.041s | 0m16.944s | 0m1.255s |
| on yarn cluster | 0m30.280s | 0m35.420s | 0m6.248s |

Observe the table above, it can be seen that

1. **It takes much larger real time (wall clock time) to execute this program on yarn cluster than to execute this program on local worker**.
    - I think it is because the input file (`Youvegottofindwhatyoulove.txt`) is so small (`24KB`) that **the runtime overheads when we run Spark distributedly in a cluster dominate the execution time**.
    - This is also the reason why the amount of CPU time spent inside/outside the kernel of your local kernel is larger when we execute the program **on yarn cluster** than **on local worker**.
2. When executing multithreadedly, it also takes more real time (wall clock time) with **8 cores** than only using **1 core**.
    - It is because of the similar reason that the input file so small that the context swich overhead dominates the execution time.



For the second implementation (calculate the average amount in credit card trip for different number of passengers which are from one to four passengers in 2017.09 NYC Yellow Taxi trip data. In NYC Taxi data), the execution time in different deploy mode is shown in the table below.

|  | real | user | sys |
| - | - | - | - |
| on local worker (1 core) | 1m52.185s | 1m55.268s | 0m10.355s |
| on local worker (8 cores) | 0m48.385s | 3m56.662s | 0m16.940s |
| on yarn cluster | 1m9.823s | 0m8.067s | 0m2.187s |

According to the table above, the conclusions are

1. **The real time (wall clock time) for executing the program on yarn cluster is much smaller than executing the program on local worker (1 core).**
2. However, the real time (wall clock time) for executing the program **on local worker (8 cores)** is even smaller than executing the program **on yarn cluster**, which may means that the input file (`yellow_tripdata_2017-09.csv`, `753MB`) in this implementation is not large enough to make Spark engine shows its scaling advantage over multi-threading.
3. Expectedly, the amount of CPU time spent inside/outside the kernel of your local kernel is larger when we execute the program **on local worker** since the computation all took place on local machine.
