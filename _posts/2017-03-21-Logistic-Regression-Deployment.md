---
title: 'Logistic Regression Deployment on Airline Data Using Spark Java'
layout: post
tags:
  - Java
  - Spark
  - DataScience
  - Regression
category: Programming
---

<!--more-->

## Preparing Data 
- For detailed, please check https://hackmd.io/s/SkEYWCnjg
- **Notice 1.** that the **target** class should be at the **last** column.
- **Notice 2.** the header column should be removed from the input dataset.
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>Month</th>
      <th>DayofMonth</th>
      <th>DayOfWeek</th>
      <th>DepTime</th>
      <th>CRSDepTime</th>
      <th>ArrTime</th>
      <th>CRSArrTime</th>
      <th>UniqueCarrier</th>
      <th>FlightNum</th>
      <th>...</th>
      <th>TaxiIn</th>
      <th>TaxiOut</th>
      <th>Cancelled</th>
      <th>CancellationCode</th>
      <th>Diverted</th>
      <th>CarrierDelay</th>
      <th>WeatherDelay</th>
      <th>NASDelay</th>
      <th>SecurityDelay</th>
      <th>LateAircraftDelay</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2000</td>
      <td>1</td>
      <td>28</td>
      <td>5</td>
      <td>1647.0</td>
      <td>1647</td>
      <td>1906.0</td>
      <td>1859</td>
      <td>HP</td>
      <td>154</td>
      <td>...</td>
      <td>15</td>
      <td>11</td>
      <td>0</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2000</td>
      <td>1</td>
      <td>29</td>
      <td>6</td>
      <td>1648.0</td>
      <td>1647</td>
      <td>1939.0</td>
      <td>1859</td>
      <td>HP</td>
      <td>154</td>
      <td>...</td>
      <td>5</td>
      <td>47</td>
      <td>0</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2000</td>
      <td>1</td>
      <td>30</td>
      <td>7</td>
      <td>NaN</td>
      <td>1647</td>
      <td>NaN</td>
      <td>1859</td>
      <td>HP</td>
      <td>154</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2000</td>
      <td>1</td>
      <td>31</td>
      <td>1</td>
      <td>1645.0</td>
      <td>1647</td>
      <td>1852.0</td>
      <td>1859</td>
      <td>HP</td>
      <td>154</td>
      <td>...</td>
      <td>7</td>
      <td>14</td>
      <td>0</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2000</td>
      <td>1</td>
      <td>1</td>
      <td>6</td>
      <td>842.0</td>
      <td>846</td>
      <td>1057.0</td>
      <td>1101</td>
      <td>HP</td>
      <td>609</td>
      <td>...</td>
      <td>3</td>
      <td>8</td>
      <td>0</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 29 columns</p>
</div>

## Application Infrastructure

### Canonical Maven directory structure
```sh
$ find .
./pom.xml
./src
./src/main
./src/main/java
./src/main/java/LogisticRegression.java
```

### Main Program
```java
/*
*
LogisticRegression.java
*
*/
public class LogisticRegression {
  public static void main(String[] args) {
    String inputFile = args[0];
    String outputPath = args[1];
    SparkConf conf = new SparkConf().setAppName("LogisticRegression");
    SparkContext sc = new SparkContext(conf);
    
     // Load and parse the data
    int minPartition = 1;
    RDD<String> input = sc.textFile(inputFile, minPartition);
    JavaRDD<String> data = input.toJavaRDD(); 
        
    JavaRDD<LabeledPoint> parsedData = data.map(line -> {
      String[] features = line.split(",");
      double[] v = new double[features.length-1];
      for (int i = 0; i < features.length - 1; i++) {
        v[i] = Double.parseDouble(features[i]);
      }
      return new LabeledPoint(Double.parseDouble(features[features.length-1]), Vectors.dense(v));
    });

    // Split initial RDD into two... [60% training data, 40% testing data].
    JavaRDD<LabeledPoint>[] splits = parsedData.randomSplit(new double[] {0.6, 0.4}, 11L);
    JavaRDD<LabeledPoint> training = splits[0].cache();
    JavaRDD<LabeledPoint> test = splits[1];

    // Run training algorithm to build the model.
    LogisticRegressionModel model = new LogisticRegressionWithLBFGS()
      .setNumClasses(10)
      .run(training.rdd());

    // Compute raw scores on the test set.
    JavaRDD<Tuple2<Object, Object>> predictionAndLabels = test.map(
      new Function<LabeledPoint, Tuple2<Object, Object>>() {
        public Tuple2<Object, Object> call(LabeledPoint p) {
          Double prediction = model.predict(p.features());
          return new Tuple2<Object, Object>(prediction, p.label());
        }
      }
    );

    // Save Prediction result
    predictionAndLabels.saveAsTextFile(outputPath);

    // Get evaluation metrics.
    MulticlassMetrics metrics = new MulticlassMetrics(predictionAndLabels.rdd());
    double precision = metrics.precision();
    System.out.println("Precision = " + precision);

    // Save and load model
    model.save(sc, outputPath+"/model/LogisticRegressionModel");

    sc.stop();
  }
}
```

### Dependencies
```xml
...
  <properties>
    <java.version>1.8</java.version>
    <spark.version>1.5.0</spark.version>
  </properties>
  <dependencies>
    <!-- Spark core -->
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-core_2.10</artifactId>
      <version>${spark.version}</version>
      <!--<scope>provided</scope>-->
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-mllib_2.10</artifactId>
      <version>${spark.version}</version>
    </dependency>
  </dependencies>
...
```

## Deploy Application To Train The Model
```shell
cd LogisticRegression
mvn package
hadoop fs -rm -r test/output
spark-submit  --class "LogisticRegression"  target/spark-sample-0.0.1.jar test/input test/output
hadoop fs -copyToLocal test/output/model/LogisticRegressionModel ./Model
```


### `Package` the application using Maven.
```sh
# Package a JAR containing your application
$ mvn package
...
[INFO] Building jar: {..}/{..}/target/spark-sample-0.0.1.jar
```
### Prepare input file and output environment
```sh
# Make input directory
$ hadoop fs -mkdir test/input
# Copy input file(s) from local to remote
$ hadoop fs -copyFromLocal ./input/logistic_input test/input
# Remove output directory to prevent conflicts 
$ hadoop fs -rm -r test/output
```

### Execute it with `spark-submit`
```sh
# Use spark-submit to run your application
$ spark-submit \
  --class "LogisticRegression" \
  target/spark-sample-0.0.1.jar
```

### Check Precision
The application would split the input data into 2 parts (60% train, 40% test) and print out the precision of the trained model.
```sh
INFO Precision = 0.81475
```

## Fetched The Trained Model

If you want to use this model in your next step, you can simply load it.
```
LogisticRegressionModel sameModel = LogisticRegressionModel.load(sc, "test/output/model/LogisticRegressionModel");
```

Or you can also copy the trained model from remote to local.
```sh
hadoop fs -copyToLocal test/output/model/LogisticRegressionModel ./Model
```