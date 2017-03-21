---
title: 'Machine Learning on Spark using Java'
layout: post
tags:
  - Java
  - Spark
  - DataScience
category: Programming
---


## References
- [Download Apache Spark](http://spark.apache.org/downloads.html)
    - MLlib is a built-in library of Spark
    - Spark supports Python, Scala, and Java
- [Spark Programming Guide](http://spark.apache.org/docs/latest/programming-guide.html)
- [Sark Configuration](http://spark.apache.org/docs/latest/configuration.html)
- [Spark Java Examples](https://github.com/apache/spark/tree/master/examples/src/main/java/org/apache/spark/examples)
- [MLlib Guide](http://spark.apache.org/docs/latest/ml-guide.html)
- [Apache Spark Machine Learning Tutorial](https://www.mapr.com/blog/apache-spark-machine-learning-tutorial)
- [Spark Java API doc](http://spark.apache.org/docs/latest/api/java/index.html)
- [Sampling Large Datasets using Spark](http://www.bigsynapse.com/sampling-large-datasets-using-spark)

<!--more-->

![](https://i.imgur.com/xUqgoIp.png)

## Startup
### A Simple Application (Word Count)

**Sample Code**

```java
/* SimpleApp.java */
/**
    * Illustrates a wordcount in Java
    */
import org.apache.log4j.Logger;
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.FlatMapFunction;
import org.apache.spark.api.java.function.Function;
import org.apache.spark.api.java.function.Function2;
import org.apache.spark.api.java.function.PairFunction;
import scala.Tuple2;

import java.util.Arrays;
import java.util.List;

public class WordCount {
    private static Logger logger = Logger.getLogger(WordCount.class);

    public static void main(String[] args) throws Exception {
        String inputFile = args[0];
        String outputPath = args[1];

        // Create a Java Spark Context.
        SparkConf conf = new SparkConf().setAppName("Simple Project");
        JavaSparkContext sc = new JavaSparkContext(conf);

        // Load our input data.
        JavaRDD<String> input = sc.textFile(inputFile);

        // Split up into words.
        JavaRDD<String> words = input .flatMap(new FlatMapFunction<String, String>() {
                public Iterable<String> call(String x) {
                    return Arrays.asList(x.split(" "));
                }
            });

        // Transform into <word, one> pair.
        JavaPairRDD<String, Integer> word_one = words .mapToPair(new PairFunction<String, String, Integer>() {
                @Override
                public Tuple2<String, Integer> call(String s) throws Exception {
                    return new Tuple2<>(s, 1);
                }
            }).cache();

        List<Tuple2<String, Integer>> result;

        JavaPairRDD<String, Integer> counts_apporache_1 = word_one .reduceByKey(new Function2<Integer, Integer, Integer>() {
                @Override
                public Integer call(Integer v1, Integer v2) throws Exception {
                    return v1 + v2;
                }
            });
        result = counts_apporache_1.collect();
        counts_apporache_1.saveAsTextFile(outputPath);
        for(Tuple2 r : result)
            logger.info(r);
    }
}
```
```xml
<!--pom.xml-->
<project>
    <groupId>edu.berkeley</groupId>
    <artifactId>simple-project</artifactId>
    <modelVersion>4.0.0</modelVersion>
    <name>Simple Project</name>
    <packaging>jar</packaging>
    <version>1.0</version>
    <dependencies>
    <dependency> <!-- Spark dependency -->
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_2.11</artifactId>
        <version>2.1.0</version>
    </dependency>
    </dependencies>
</project>
```
- Note that `SparkConf().setAppName` in main java class must match `project->name` in pom.xml.
- Note that `project->dependencies` in pom.xml must contain all libraries we import in our java classes.

### Canonical Maven directory structure
```sh
$ find .
./pom.xml
./src
./src/main
./src/main/java
./src/main/java/SimpleApp.java
```

### `Package` the application using Maven.
```sh
# Package a JAR containing your application
$ mvn package
...
[INFO] Building jar: {..}/{..}/target/simple-project-1.0.jar
```
### Prepare input file and output environment
```sh
# Make input directory
$ hadoop fs -mkdir test/input
# Copy input file(s) from local to remote
$ hadoop fs -copyFromLocal ./input/data test/input
# Remove output directory to prevent conflicts 
$ hadoop fs -rm -r test/output
```
### Execute it with `spark-submit`
```sh
# Use spark-submit to run your application
$ spark-submit \
  --class "SimpleApp" \
  --master local[4] \
  target/simple-project-1.0.jar
```
- Note that you fill up your main java class name after ````--class````
- Note that we run with `local[4]`, meaning _4 threads_ - which represents “minimal” parallelism.

### View and download the output files
```sh
# List the output files
$ hadoop fs -ls test/output
# View the output files
$ hadoop fs -cat test/output/part-*
```
```sh
# Download output files from remote to local
for i in `seq 0 10`;
do
hadoop fs -copyToLocal test/output/part-0000$i ./
done
```
- Note that you can modify `seq 0 10` as your need.



## MapReduce Functions
![](https://i.imgur.com/1DcypPa.png)
### Map
#### `map`
![](https://i.imgur.com/akLHsqO.png)
#### `flatmap`
![](https://i.imgur.com/fe3HLyv.png)
#### `mapToPair`
![](https://i.imgur.com/z6mrWIF.png)
#### `flapMapToPair`
![](https://i.imgur.com/rbVSpqb.png)
#### `mapValues`
![](https://i.imgur.com/448dt9i.png)

### Filter
#### `filter`
![](https://i.imgur.com/9xyDZI6.png)

### Reduce
#### `reduce`
![](https://i.imgur.com/1qPpXhS.png)
#### `reduceByKey`
![](https://i.imgur.com/f2k4WT6.png)

### Others
#### `groupBy`
![](https://i.imgur.com/ANUMMTP.png)
#### `sortByKey`
![](https://i.imgur.com/wD2bu1P.png)
#### `distinct`
![](https://i.imgur.com/XSo0DKb.png)




