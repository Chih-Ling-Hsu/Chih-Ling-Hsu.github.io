---
title: 'Simple Examples with Spark Streaming'
layout: post
tags:
  - Spark
  - Java
  - Streaming
  - SQL
category: Programming
---


Types of queries one wants on answer on a data stream:

- Sampling data from a stream
    - Construct a random sample
- Queries over sliding windows
    - Number of items of type x in the last k elements of the stream
- Filtering a data stream
    - Select elements with property x from the stream
- Counting distinct elements
    - Number of distinct elements in the last k elements of the stream
- Estimating moments
    - Estimate average/std deviation of last k elements
- Finding frequent elements

<!--more-->

## Quick Example: WordCount

In this example we would **read files written in a directory** as a stream of data.

### Step 1. Create A Java Project
#### Canonical Maven directory structure

```sh
$ find .
./pom.xml
./src
./src/main
./src/main/java
./src/main/java/JavaNetworkWordCount.java
```

The content of [`./pom.xml`](https://hackmd.io/s/B1zXag5pl#project-dependencies) and [`./src/main/java/JavaNetworkWordCount.java`](https://hackmd.io/s/B1zXag5pl#main-class) are provided in below.

#### Main Class

```java
/* JavaNetworkWordCount.java */

import ...

/**
 * Counts words in UTF8 encoded, '\n' delimited text received from the network every second.
 */
public final class JavaNetworkWordCount {
  private static final Pattern SPACE = Pattern.compile(" ");

  public static void main(String[] args) throws Exception {
    
    SparkConf sparkConf = new SparkConf().setAppName("JavaNetworkWordCount");
    JavaStreamingContext ssc = new JavaStreamingContext(sparkConf, Durations.seconds(1));    
    
    String dataDirectory = "remoteFolder/streaming";
    JavaDStream<String> lines =ssc.textFileStream(dataDirectory);

    JavaDStream<String> words = lines.flatMap(x -> Arrays.asList(SPACE.split(x)));
    JavaPairDStream<String, Integer> wordCounts = words.mapToPair(s -> new Tuple2<>(s, 1))
        .reduceByKey((i1, i2) -> i1 + i2);

    wordCounts.print();
    ssc.start();
    ssc.awaitTermination();
  }
}
```

Spark Streaming will monitor the directory `dataDirectory`(= `"remoteFolder/streaming"`) and process any files created in that directory (files written in nested directories not supported).

Note that

- The files must have the same data format.
- The files must be created in `dataDirectory` by atomically moving into or renaming.

#### Project Dependencies

```xml
<!--pom.xml-->
<project>
  <groupId>org.nchc.spark</groupId>
  <artifactId>spark-sample</artifactId>
  <modelVersion>4.0.0</modelVersion>
  <name>JavaNetworkWordCount</name>
  <packaging>jar</packaging>
  <version>0.0.1</version>
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
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-streaming_2.10</artifactId>
      <version>${spark.version}</version>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement>
      <plugins>
        <plugin>
      <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.1</version>
          <configuration>
            <source>${java.version}</source>
            <target>${java.version}</target>
          </configuration>
    </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```

- Note that `SparkConf().setAppName` in main java class must match `project->name` in pom.xml.
- Note that `project->dependencies` in pom.xml must contain all libraries we import in our java classes.


### Step 2. `Package` The Project using Maven

```sh
# Package a JAR containing your application
$ mvn clean package
...
[INFO] Building jar: {..}/{..}/target/spark-sample-0.0.1.jar
```

### Step 3. Prepare Streaming Data

Create the directory named the same as variable `dataDirectory` you set in your project.

```shell
$ hadoop fs -mkdir remoteFolder/streaming
```

Then create a file named `hello` with data you want to receive as a stream.

```shell
$ vi hello
hello world
```

### Step 4. Run The Project

Open a terminal, named "Terminal A", to execute the following commnd.

```shell
$ spark-submit  --class "JavaNetworkWordCount"  JavaNetworkWordCount/target/spark-sample-0.0.1.jar
```


### Step 5. Move Files into Streaming Directory

Open another terminal, named "Terminal B" to execute the following commnds.

```shell
$ hadoop fs -rm remoteFolder/streaming/hello
$ hadoop fs -copyFromLocal hello remoteFolder/streaming
$ hadoop fs -cat remoteFolder/streaming/*
```

Then in "Terminal A" you would see the following information show on screen.

```shell
...
-------------------------------------------
Time: 1357008430000 ms
-------------------------------------------
(hello,1)
(world,1)
...
```

If you didn't see, execute the same commands repeatedly until you see.

```shell
$ hadoop fs -rm remoteFolder/streaming/hello
$ hadoop fs -copyFromLocal hello remoteFolder/streaming
$ hadoop fs -cat remoteFolder/streaming/*
```

## DataFrame and SQL Operations

You can easily use DataFrames and SQL operations on streaming data.

### Step 1. Create A Java Project
#### Canonical Maven directory structure

```sh
$ find .
./pom.xml
./src
./src/main
./src/main/java
./src/main/java/JavaRow.java
./src/main/java/JavaSQLNetworkWordCount.java
```

The content of [`./pom.xml`](https://hackmd.io/s/B1zXag5pl#project-dependencies-add-spark-sql),  [`./src/main/java/JavaNetworkWordCount.java`](https://hackmd.io/s/B1zXag5pl#classes), and [./src/main/java/JavaRow.java](https://hackmd.io/s/B1zXag5pl#classes) are provided in below.

#### Classes

```java
/* JavaRow.java */

/** Java Bean class for converting RDD to DataFrame */
public class JavaRow implements java.io.Serializable {
  private String word;

  public String getWord() {
    return word;
  }

  public void setWord(String word) {
    this.word = word;
  }
}
```


```java
/* JavaSQLNetworkWordCount.java */

import ...

/**
 * Counts words in UTF8 encoded, '\n' delimited text received from the network every second.
 */
public final class JavaSQLNetworkWordCount {
  private static final Pattern SPACE = Pattern.compile(" ");
  private static final Pattern COMMA = Pattern.compile(",");

  public static void main(String[] args) throws Exception {

    // Create the context with a 5 second batch size
    SparkConf sparkConf = new SparkConf().setAppName("JavaSQLNetworkWordCount");
    JavaStreamingContext ssc = new JavaStreamingContext(sparkConf, Durations.seconds(5));
    
    
    String dataDirectory = "remoteFolder/streaming";
    JavaDStream<String> lines =ssc.textFileStream(dataDirectory);
    JavaDStream<String> words = lines.flatMap(x -> Arrays.asList(SPACE.split(x)));

    words.foreachRDD( (rdd, time) ->  {

          // Get the singleton instance of SQLContext
          SQLContext sqlContext = SQLContext.getOrCreate(rdd.context());

          // Convert RDD[String] to RDD[case class] to DataFrame
          JavaRDD<JavaRow> rowRDD = rdd.map(word -> {
            JavaRow record = new JavaRow();
            record.setWord(word);
            return record;
          });
          DataFrame wordsDataFrame = sqlContext.createDataFrame(rowRDD, JavaRow.class);

          // Register as table
          wordsDataFrame.registerTempTable("words");

          // Do word count on table using SQL and print it
          DataFrame wordCountsDataFrame =
              sqlContext.sql("select word, count(*) as total from words group by word");

          wordCountsDataFrame.show();
          return null;
      }
    );

    ssc.start();
    ssc.awaitTermination();
  }
}
```

Spark Streaming will monitor the directory `dataDirectory`(= `"remoteFolder/streaming"`) and process any files created in that directory (files written in nested directories not supported).

Note that

- The files must have the same data format.
- The files must be created in `dataDirectory` by atomically moving into or renaming.

#### Project Dependencies (add spark-sql)

```xml
<!--pom.xml-->

<project>
  <groupId>org.nchc.spark</groupId>
  <artifactId>spark-sample</artifactId>
  <modelVersion>4.0.0</modelVersion>
  <name>JavaSQLNetworkWordCount</name>
  <packaging>jar</packaging>
  <version>0.0.1</version>
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
      <artifactId>spark-streaming_2.10</artifactId>
      <version>${spark.version}</version>
    </dependency>
    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-sql_2.10</artifactId>
      <version>${spark.version}</version>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement>
      <plugins>
        <plugin>
      <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.1</version>
          <configuration>
            <source>${java.version}</source>
            <target>${java.version}</target>
          </configuration>
    </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```

- Note that `SparkConf().setAppName` in main java class must match `project->name` in pom.xml.
- Note that `project->dependencies` in pom.xml must contain all libraries we import in our java classes.


### Step 2. `Package` The Project using Maven

```sh
# Package a JAR containing your application
$ mvn clean package
...
[INFO] Building jar: {..}/{..}/target/spark-sample-0.0.1.jar
```

### Step 3. Prepare Streaming Data

Create the directory named the same as variable `dataDirectory` you set in your project.

```shell
$ hadoop fs -mkdir remoteFolder/streaming
```

Then create a file named `hello` with data you want to receive as a stream.

```shell
$ vi hello
hello world
```

### Step 4. Run The Project

Open a terminal, named "Terminal A", to execute the following commnd.

```shell
$ spark-submit  --class "JavaSQLNetworkWordCount"  JavaSQLNetworkWordCount/target/spark-sample-0.0.1.jar
```


### Step 5. Move Files into Streaming Directory

Open another terminal, named "Terminal B" to execute the following commnds.

```shell
$ hadoop fs -rm remoteFolder/streaming/hello
$ hadoop fs -copyFromLocal hello remoteFolder/streaming
$ hadoop fs -cat remoteFolder/streaming/*
```

Then in "Terminal A" you would see the following information show on screen.

```shell
+-----+-----+
| word|total|
+-----+-----+
|hello|    1|
|world|    1|
+-----+-----+
```

If you didn't see, execute the same commands repeatedly until you see.

```shell
$ hadoop fs -rm remoteFolder/streaming/hello
$ hadoop fs -copyFromLocal hello remoteFolder/streaming
$ hadoop fs -cat remoteFolder/streaming/*
```


## References
- [Spark Java API](https://spark.apache.org/docs/1.5.0/api/java/index.html)
- [Spark Streaming Programming Guide](https://spark.apache.org/docs/1.5.0/streaming-programming-guide.html)