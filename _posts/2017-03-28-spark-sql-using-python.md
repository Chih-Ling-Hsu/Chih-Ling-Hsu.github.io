---
title: 'Spark SQL Using Python'
layout: post
tags:
  - Python
  - Spark
  - SQL
category: Programming
mathjax: false
---

For SQL users, Spark SQL provides state-of-the-art SQL performance and maintains compatibility with Shark/Hive. In particular, like Shark, Spark SQL supports all existing Hive data formats, user-defined functions (UDF), and the Hive metastore. With features that will be introduced in Apache Spark 1.1.0, Spark SQL beats Shark in TPC-DS performance by almost an order of magnitude.

<!--more-->

## Used Versions

- Spark version: 1.5.0
- Python version: 2.6.6

## Load Data

```shell
$ pyspark --packages com.databricks:spark-csv_2.10:1.5.0
```

Then pyspark would begin to prepare your spark environment.   You can import the libraries neede and load your data as it is done.

```python
>>> from pyspark.sql import SQLContext, DataFrameWriter
>>> from pyspark.sql.types import *
```

```python
>>> sqlContext = SQLContext(sc)
>>> df = sqlContext.read.format('com.databricks.spark.csv').options(header='true').load('remoteFolder/input/qacct_2011.csv')
```

If you want to check the data you have loaded, you can use the following commands.

### Infered Scema

```python
>>> df.printSchema()
```

```shell
root
 |-- Q_SVR: string (nullable = true)
 |-- T_DATE: string (nullable = true)
 |-- LOGIN_NAME: string (nullable = true)
 |-- Q_NAME: string (nullable = true)
 |-- JOB_NAME: string (nullable = true)
 |-- JOB_ID: string (nullable = true)
 |-- JOB_TYPE: string (nullable = true)
 |-- MIN_CPU: string (nullable = true)
 |-- MAX_CPU: string (nullable = true)
 |-- REAL_CPU: string (nullable = true)
 |-- Q_TIME: string (nullable = true)
 |-- ELP_TIME: string (nullable = true)
 |-- CPU_TIME: string (nullable = true)
 |-- SU: string (nullable = true)
 |-- YM: string (nullable = true)
 |-- SUBMIT_DATE: string (nullable = true)
 |-- SAT_NO: string (nullable = true)
 |-- HOG_NO: string (nullable = true)
 |-- CPU_NO: string (nullable = true)
 |-- WALL_NO: string (nullable = true)
 |-- AFT_SU: string (nullable = true)
 |-- AFT_CPU: string (nullable = true)
 |-- AFT_TIME: string (nullable = true)
 |-- ELP_TIME_R: string (nullable = true)
```

### View Rows

```python
>>> df.show(10)
```

```shell
+-----+-------------------+----------+-----------+--------+------+--------+-------+-------+--------+------+--------+--------+-----+-------+-------------------+------+------+------+-------+------+-------+--------+----------+
|Q_SVR|             T_DATE|LOGIN_NAME|     Q_NAME|JOB_NAME|JOB_ID|JOB_TYPE|MIN_CPU|MAX_CPU|REAL_CPU|Q_TIME|ELP_TIME|CPU_TIME|   SU|     YM|        SUBMIT_DATE|SAT_NO|HOG_NO|CPU_NO|WALL_NO|AFT_SU|AFT_CPU|AFT_TIME|ELP_TIME_R|
+-----+-------------------+----------+-----------+--------+------+--------+-------+-------+--------+------+--------+--------+-----+-------+-------------------+------+------+------+-------+------+-------+--------+----------+
|axxx5|2011-08-01_00:00:00|       cxd|interactive|ls60_cmd| axxi3|       S|      1|      1|       1|     0|      19|   19.45| .004|2011/08|2011-07-31_23:59:41|    04|    04|    00|     00|     0|      0|       0|        19|
|axxx5|2011-08-01_00:00:00|  sxxxxxx4|interactive|ls60_cmd| axxi1|       S|      1|      1|       1|     0|      82|   82.69|.0171|2011/08|2011-07-31_23:58:38|    04|    04|    00|     00|     0|      0|       0|        82|
|axxx5|2011-08-01_00:00:00|       man|interactive|ls60_cmd| axxi1|       S|      1|      1|       1|     0|      36|   36.13|.0075|2011/08|2011-07-31_23:59:24|    04|    04|    00|     00|     0|      0|       0|        36|
|axxx5|2011-08-01_00:00:00|      root|interactive|ls60_cmd| axxi5|       S|      1|      1|       1|     0|      86|   86.51|.0179|2011/08|2011-07-31_23:58:34|    04|    04|    00|     00|     0|      0|       0|        86|
|axxx5|2011-08-01_00:00:00|      chem|interactive|ls60_cmd| axxi5|       S|      1|      1|       1|     0|      10|   10.39|.0021|2011/08|2011-07-31_23:59:50|    04|    04|    00|     00|     0|      0|       0|        10|
|axxx5|2011-08-01_00:00:00|       gxm|interactive|ls60_cmd| axxi2|       S|      1|      1|       1|     0|      40|   40.85|.0083|2011/08|2011-07-31_23:59:20|    04|    04|    00|     00|     0|      0|       0|        40|
|axxx5|2011-08-01_00:00:00|       man|interactive|ls60_cmd| axxi2|       S|      1|      1|       1|     0|      25|   25.75|.0052|2011/08|2011-07-31_23:59:35|    04|    04|    00|     00|     0|      0|       0|        25|
|axxx5|2011-08-01_00:00:00|  sxxxxxx4|interactive|ls60_cmd| axxi2|       S|      1|      1|       1|     0|      87|   87.56|.0181|2011/08|2011-07-31_23:58:33|    04|    04|    00|     00|     0|      0|       0|        87|
|axxx5|2011-08-01_00:00:00|      root|interactive|ls60_cmd| axxi2|       S|      1|      1|       1|     0|      68|   68.72|.0142|2011/08|2011-07-31_23:58:52|    04|    04|    00|     00|     0|      0|       0|        68|
|axxx5|2011-08-01_00:00:00|       man|interactive|ls60_cmd| axxi5|       S|      1|      1|       1|     0|      29|   29.83| .006|2011/08|2011-07-31_23:59:31|    04|    04|    00|     00|     0|      0|       0|        29|
+-----+-------------------+----------+-----------+--------+------+--------+-------+-------+--------+------+--------+--------+-----+-------+-------------------+------+------+------+-------+------+-------+--------+----------+
only showing top 10 rows
```

## Create Table

### Create Table Using Dataframe

This command create table using [infered schema](#infered-scema).

```python
>>> df.registerTempTable("qacctall")
```

### Create Empty Table Using Specified Schema

Given SQL statement as

```SQL
CREATE TABLE qacctdate (subT_DATE String, COUNT_USER FLOAT, COUNT_NUM FLOAT, SUMQ_TIME Float, SUMELP_TIME Float, SUMCPU_TIME FLOAT)
```
We must decompose the whole statement into several steps:

**1. Specify Your Schema**

```python
>>> schema = StructType([
    StructField("subT_DATE", StringType(), True),
    StructField("COUNT_USER", FloatType(), True),
    StructField("COUNT_NUM", FloatType(), True),
    StructField("SUMQ_TIME", FloatType(), True),
    StructField("SUMELP_TIME", FloatType(), True),
    StructField("SUMCPU_TIME", FloatType(), True)
    ])
```

**2. Create Empty Table**

```python
>>> df2 = sqlContext.createDataFrame([], schema)
>>> df2.registerTempTable("qacctdate")
```


- pyspark.sql.types.**StructType**(fields=None)
    - Struct type, consisting of a list of StructField.
    - This is the data type representing a Row.
-  pyspark.sql.types.**StructField**(name, dataType, nullable=True, metadata=None)
    - A field in StructType.
    - Parameters:	
        - **_name_** – string, name of the field.
        - **_dataType_** – DataType of the field.
        - **_nullable_** – boolean, whether the field can be null (None) or not.
        - metadata – a dict from string to simple type that can be toInternald to JSON automatically

If you want to use any other data types on spark, please refer to [Spark SQL and DataFrame Guide](https://spark.apache.org/docs/1.5.0/sql-programming-guide.html#data-types).



### Create Table Using Another Table

Given SQL statement as

```SQL
CREATE TABLE new_table_name AS
    SELECT column1, column2,...
    FROM existing_table_name
    WHERE ....;
```

For example, 

```SQL
CREATE TABLE qacctdateorder
    SELECT *
    FROM qacctdate
    ORDER BY subT_DATE;
```

We can simply use the following command to execute it on spark.

**Step 1. Select rows**

```python
>>> df_ordered = sqlContext.sql("SELECT * FROM qacctdate ORDER BY subT_DATE")
>>> df_ordered.show(5)
```
```shell
+----------+----------+---------+---------+-----------+--------------------+
| subT_DATE|COUNT_USER|COUNT_NUM|SUMQ_TIME|SUMELP_TIME|         SUMCPU_TIME|
+----------+----------+---------+---------+-----------+--------------------+
|2011-08-01|      33.0|   1996.0|5296074.0|  4873626.0|1.0732683480000006E7|
|2011-08-01|      33.0|   1996.0|5296074.0|  4873626.0|1.0732683480000006E7|
|2011-08-02|      32.0|   4928.0|3163158.0|1.3153414E7|2.6667715799999997E7|
|2011-08-02|      32.0|   4928.0|3163158.0|1.3153414E7|2.6667715799999997E7|
|2011-08-03|      24.0|   5014.0|9473140.0|1.8178348E7|      7.8896286476E8|
+----------+----------+---------+---------+-----------+--------------------+
only showing top 5 rows
```

**Step 2. Create Another Table**

```python
>>> df_ordered.registerTempTable("qacctdateorder")
```

## Execute SQL Statements

For all SQL statements, please refer to [Supported syntax of Spark SQL](https://docs.datastax.com/en/datastax_enterprise/4.6/datastax_enterprise/spark/sparkSqlSupportedSyntax.html)

### Select

Given SQL statement as
```SQL
FROM qacctall
    SELECT substr(T_DATE, 1, 10), count(Distinct LOGIN_NAME), count(*), sum(Q_TIME), sum(ELP_TIME), sum(CPU_TIME)
    GROUP BY substr(T_DATE, 1, 10);
```

We can simply use the following command to execute it on spark.


```python
>>> df_select = sqlContext.sql("SELECT substr(T_DATE, 1, 10), count(Distinct LOGIN_NAME), count(*), sum(Q_TIME), sum(ELP_TIME), sum(CPU_TIME)  FROM qacctall GROUP BY substr(T_DATE, 1, 10)")
>>> df_select.show(10)
```
```shell

+----------+---+----+---------+-----------+--------------------+
|       _c0|_c1| _c2|      _c3|        _c4|                 _c5|
+----------+---+----+---------+-----------+--------------------+
|2011-11-30| 58|7682|1793154.0|5.8867728E7| 4.248628650000001E7|
|2011-09-30| 59| 636|   2376.0|2.5517608E7|3.1475379480000004E7|
|2011-10-30| 44| 432|  94204.0|6.4102362E7| 4.377035656000001E7|
|2011-10-31| 63|1300|  57584.0|4.7405606E7|1.5582149653999996E9|
|2011-08-30| 38| 454|3106736.0|1.1878624E7|     1.06372894726E9|
|2011-08-31| 40|1860|1533592.0|1.5382988E7|3.1321356601000004E9|
|2011-12-01| 57|8070|2161710.0|5.7086352E7|3.5904708620000005E7|
|2011-12-02| 56|7552| 812038.0|2.3611752E7|1.2171202900000008E7|
|2011-12-03| 49|1772|1550372.0| 7.316791E7| 5.957460229999995E7|
|2011-12-04| 39| 444| 601936.0|  2.24238E7|1.2607418140000004E7|
+----------+---+----+---------+-----------+--------------------+
only showing top 10 rows
```

New column names can be specified.
```python 
>>> df_select = sqlContext.sql("SELECT substr(T_DATE, 1, 10) AS subT_DATE, count(Distinct LOGIN_NAME) AS COUNT_USER, count(*) AS COUNT_NUM, sum(Q_TIME) AS SUMQ_TIME, sum(ELP_TIME) AS SUMELP_TIME, sum(CPU_TIME) AS SUMCPU_TIME FROM qacctall GROUP BY substr(T_DATE, 1, 10)")
>>> df_select.show(10)
```
```shell
+----------+----------+---------+---------+-----------+--------------------+
| subT_DATE|COUNT_USER|COUNT_NUM|SUMQ_TIME|SUMELP_TIME|         SUMCPU_TIME|
+----------+----------+---------+---------+-----------+--------------------+
|2011-11-30|        58|     7682|1793154.0|5.8867728E7| 4.248628650000001E7|
|2011-09-30|        59|      636|   2376.0|2.5517608E7|3.1475379480000004E7|
|2011-10-30|        44|      432|  94204.0|6.4102362E7| 4.377035656000001E7|
|2011-10-31|        63|     1300|  57584.0|4.7405606E7|1.5582149653999996E9|
|2011-08-30|        38|      454|3106736.0|1.1878624E7|     1.06372894726E9|
|2011-08-31|        40|     1860|1533592.0|1.5382988E7|3.1321356601000004E9|
|2011-12-01|        57|     8070|2161710.0|5.7086352E7|3.5904708620000005E7|
|2011-12-02|        56|     7552| 812038.0|2.3611752E7|1.2171202900000008E7|
|2011-12-03|        49|     1772|1550372.0| 7.316791E7| 5.957460229999995E7|
|2011-12-04|        39|      444| 601936.0|  2.24238E7|1.2607418140000004E7|
+----------+----------+---------+---------+-----------+--------------------+
only showing top 10 rows
```

### INSERT INTO SELECT
Given SQL statement as

```SQL
INSERT INTO table2 (column1, column2, column3, ...)
SELECT column1, column2, column3, ...
FROM table1
WHERE condition;
```

For example,

```SQL
FROM qacctall
INSERT OVERWRITE TABLE qacctdate
  SELECT substr(T_DATE, 1, 10), count(Distinct LOGIN_NAME), count(*), sum(Q_TIME), sum(ELP_TIME), sum(CPU_TIME)
  GROUP BY substr(T_DATE, 1, 10);
```

We must decompose the whole statement into several steps:

**1. SELECT rows FROM table "qacctall"**

```python
>>> df_rows = sqlContext.sql("SELECT substr(T_DATE, 1, 10) AS subT_DATE, count(Distinct LOGIN_NAME) AS COUNT_USER, count(*) AS COUNT_NUM, sum(Q_TIME) AS SUMQ_TIME, sum(ELP_TIME) AS SUMELP_TIME, sum(CPU_TIME) AS SUMCPU_TIME FROM qacctall GROUP BY substr(T_DATE, 1, 10)")
```

**2. Make sure the schema of the selected rows is the same as that of the table to be inserted.**

Simple check:
```python
>>> df_table = sqlContext.sql("SELECT * FROM qacctdate")
>>> df_rows.schema == df_table.schema
```

If `False` is shown, then modify the schema of the selected rows to be the same as the table.
```python
>>> df_rows = df_rows.select(
        df_rows.subT_DATE,
        df_rows.COUNT_USER.cast("float"), 
        df_rows.COUNT_NUM.cast("float"), 
        df_rows.SUMQ_TIME.cast("float"),
        df_rows.SUMELP_TIME.cast("float"), 
        df_rows.SUMCPU_TIME.cast("float")
    )
>>> df_rows = sqlContext.createDataFrame(df_rows.collect(), df_table.schema)
```

For more detail, please refer to [How to Change Schema of a Spark SQL DataFrame?](./how-to-change-schema-of-a-spark-sql-dataframe)

**3. INSERT into table "qacctdate"**

```python
>>> df_writer = DataFrameWriter(df_rows)
>>> df_writer.insertInto("qacctdate")
```

- pyspark.sql.DataFrameWriter.**insertInto**(_tableName_, _overwrite=False_)[source]
    - Inserts the content of the DataFrame to the specified table.
    - It requires that the schema of the class:DataFrame is the same as the schema of the table.

### Union

Generally, Spark sql can not insert or update directly using simple sql statement, unless you use Hive Context.   Alternatively, we can use `unionAll` to achieve the same goal as `insert`.

For example,

```SQL
FROM qacctall
INSERT OVERWRITE TABLE qacctdate
  SELECT substr(T_DATE, 1, 10), count(Distinct LOGIN_NAME), count(*), sum(Q_TIME), sum(ELP_TIME), sum(CPU_TIME)
  GROUP BY substr(T_DATE, 1, 10);
```

We can simply use the following command to execute it on spark.

```python
>>> df_rows = sqlContext.sql("SELECT substr(T_DATE, 1, 10) AS subT_DATE, count(Distinct LOGIN_NAME) AS COUNT_USER, count(*) AS COUNT_NUM, sum(Q_TIME) AS SUMQ_TIME, sum(ELP_TIME) AS SUMELP_TIME, sum(CPU_TIME) AS SUMCPU_TIME FROM qacctall GROUP BY substr(T_DATE, 1, 10)")
>>> sqlContext.sql("SELECT * FROM qacctdate").unionAll(df_rows).registerTempTable("qacctdate")
```

## Export Table

For details about different types of data sources available in SparkSQL, please refer to [Spark SQL - Data Sources](https://www.tutorialspoint.com/spark_sql/spark_sql_data_sources.htm)

### Save to Parquet File

Parquet is a columnar format, supported by many data processing systems.

```python
>>> df = sqlContext.sql("SELECT * FROM qacctall")
>>> df.write.format('parquet').save('./qacctall')
```

```shell
INFO ParquetRelation: Listing hdfs://YOUR_ACCOUNT_HOME/qacctall on driver
```

And when the next time you want to load this table, you can simply follow these commands:

```python
>>> df = sqlContext.load("./qacctall", "parquet")
>>> df.registerTempTable("qacctall");
```

### Save to JSON Datasets

```python
df.write.format('json').save('./qacctall')
```

```shell
INFO JSONRelation: Listing hdfs://YOUR_ACCOUNT_HOME/qacctall on driver
```

And when the next time you want to load this table, you can simply follow these commands:

```python
>>> df = sqlContext.load("./qacctall", "json")
>>> df.registerTempTable("qacctall");
```

## References

- [Spark SQL and DataFrame Guide](https://spark.apache.org/docs/1.5.0/sql-programming-guide.html)
- [pyspark.sql module](https://spark.apache.org/docs/1.5.0/api/python/pyspark.sql.html)
- [Analytics with Apache Spark Tutorial Part 2 : Spark SQL](http://www.mammatustech.com/apache-spark-course-quick-start-real-time-data-analytics/apache-spark-introduction-part2-sparksql)
- [Supported syntax of Spark SQL](https://docs.datastax.com/en/datastax_enterprise/4.6/datastax_enterprise/spark/sparkSqlSupportedSyntax.html)
- [Compatibility with Apache Hive](https://spark.apache.org/docs/1.5.0/sql-programming-guide.html#compatibility-with-apache-hive)
- [CSV Data Source for Apache Spark 1.x](https://github.com/databricks/spark-csv)
- [Stack Overflow - Persisting Spark Dataframe](http://stackoverflow.com/questions/40251672/persisting-spark-dataframe)


