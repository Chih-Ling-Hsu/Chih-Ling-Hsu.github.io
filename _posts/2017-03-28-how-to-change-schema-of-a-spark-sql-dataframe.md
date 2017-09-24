---
title: 'How to Change Schema of a Spark SQL DataFrame?'
layout: post
tags:
  - Python
  - Spark
  - SQL
category: Programming
mathjax: false
---

For the reason that I want to insert `rows selected from a table` (_df_rows_) to another table, I need to make sure that

```
The schema of the rows selected are the same as the schema of the table
```

Since the function `pyspark.sql.DataFrameWriter.insertInto`, which inserts the content of the DataFrame to the specified table, requires that the schema of the class:DataFrame is the same as the schema of the table.


<!--more-->

## Simple check

```python
>>> df_table = sqlContext.sql("SELECT * FROM qacctdate")
>>> df_rows.schema == df_table.schema
```

If `False` is shown, then we need to modify the schema of the selected rows to be the same as the table.

- Schema of selected rows

```python
>>> df_rows.printSchema()
```
```shell
root
 |-- subT_DATE: string (nullable = true)
 |-- COUNT_USER: long (nullable = false)
 |-- COUNT_NUM: long (nullable = false)
 |-- SUMQ_TIME: double (nullable = true)
 |-- SUMELP_TIME: double (nullable = true)
 |-- SUMCPU_TIME: double (nullable = true)
```

- Schema of table to be inserted

```python
>>> df_table.printSchema()
```
```shell
root
 |-- subT_DATE: string (nullable = true)
 |-- COUNT_USER: float (nullable = true)
 |-- COUNT_NUM: float (nullable = true)
 |-- SUMQ_TIME: float (nullable = true)
 |-- SUMELP_TIME: float (nullable = true)
 |-- SUMCPU_TIME: float (nullable = true)
```

## Cast Type of Values If Needed

To modify the schema of the selcted rows, the following command is used:

```python
>>> df_rows = sqlContext.createDataFrame(df_rows.collect(), df_table.schema)
```

However, `TypeError` occurs:

```shell
TypeError: FloatType can not accept object in type <type 'int'>
```

So I need to manually cast the type of values.

For example, the following command raises an error:

```python
>>> df_test = sqlContext.createDataFrame([('2011-11-30', 58, 7682,1793154.0,58867728, 42486286500)],df_table.schema)
```

But this would pass:

```python
>>> df_test = sqlContext.createDataFrame([('2011-11-30', float(58),float(7682),1793154.0,float(58867728), float(42486286500))],df_table.schema)
```

In conclusion, I need to cast type of multiple columns manually:

```python
>>> df_rows = df_rows.select(
        df_rows.subT_DATE,
        df_rows.COUNT_USER.cast("float"), 
        df_rows.COUNT_NUM.cast("float"), 
        df_rows.SUMQ_TIME.cast("float"),
        df_rows.SUMELP_TIME.cast("float"), 
        df_rows.SUMCPU_TIME.cast("float")
    )
```


## Change The Schema

```python
>>> df_rows = sqlContext.createDataFrame(df_rows.collect(), df_table.schema)
```

## Check Result

```python
>>> df_rows.schema == df_table.schema
True
```
