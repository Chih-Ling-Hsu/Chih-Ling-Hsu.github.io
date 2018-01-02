---
title: 'Data Manipulation and Visualization Using Elasticsearch and Kibana'
layout: post
tags:
  - Elasticsearch
  - NLP
  - Python
category: Programming
mathjax: true
---

Elasticsearch is a **distributed**, **real-time**, search and analytics platform.

Using a restful API, Elasticsearch saves data and indexes it automatically. It assigns types to fields and that way a search can be done smartly and quickly using filters and different queries.

It’s uses **JVM** in order to be as fast as possible. It distributes indexes in “shards” of data. It replicates shards in different nodes, so it’s distributed and clusters can function even if not all nodes are operational. Adding nodes is super easy and that’s what makes it so scalable.

ES uses **Lucene** to solve searches. This is quite an advantage with comparing with, for example, Django query strings. A **restful API** call allows us to perform searches using json objects as parameters, making it much more flexible and giving each search parameter within the object a different weight, importance and or priority.

<!--more-->

The final result ranks objects that comply with the search query requirements. You could even use **synonyms**, **autocompletes**, **spell suggestions** and **correct typos**. While the usual query strings provides results that follow certain logic rules, ES queries give you a **ranked list** of results that may fall in different criteria and its order depend on how they comply with a certain rule or filter.

The main point is **scalability** and getting results and insights very fast. In most cases using Lucene could be enough to have all you need.

**Useful Links:**

- [Official Guide](https://www.elastic.co/guide/en/elasticsearch/reference/current/_basic_concepts.html)
- [Download Page](https://www.elastic.co/downloads)

## Setup ElasticSearch Server

Follow the [installation instruction on official web page](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/_installation.html), then you should be able to start a single node of ElasticSearch.

If you need to run as master/slave in order to create a cluster, then you should modify the settings in `elasticsearch.yml`.

With the ElasticSearch server running, you can now deal with your data using elastic search.

By default, elasticsearch server would be running at `http://localhost:9200`.

## ElasticSearch Using Python

Here we would use[`elasticsearch-py`](https://www.elastic.co/guide/en/elasticsearch/client/python-api/current/index.html)([Python Elasticsearch Client](https://elasticsearch-py.readthedocs.io/en/master/))

```shell
$ pip install elasticsearch
```

### Create an ElasticSearch Client

First, we need to make sure ES is up and running:

```python
# make sure ES is up and running
import requests, sys
try:
    res = requests.get('http://localhost:9200')
except Exception as e:
    print("[Error] Elastic Search is not running")
```

Then we can connect to our cluster:

```python
#connect to our cluster
from elasticsearch import Elasticsearch
es = Elasticsearch([{'host': 'localhost', 'port': 9200}])
```

### Upload Data to Server

Upload the data using elasticsearch client:

**1. from `json` file**

```python
import json

# Specify your parameters here
FILE_PATH = "path_to_my_json_file"
INDEX = "my_index"
DOC_TYPE = "my_doc_type"

# Read data from file
data = json.loads(open(FILE_PATH).read())

# Upload data to elastic search server
if isinstance(data, dict):    # JSON Object
    es.index(index=INDEX, doc_type=DOC_TYPE, body=data)
else:    # JSON Array
    for record in data:
        es.index(index=INDEX, doc_type=DOC_TYPE, body=record)
```

**2. from `csv` file**

```python
import json
import pandas as pd

# Specify your parameters here
FILE_PATH = "path_to_my_csv_file"
INDEX = "my_index"
DOC_TYPE = "my_doc_type"
HEADER_INDEX = 0

# Read data from file
df = pd.read_csv(FILE_PATH, header=HEADER_INDEX)
data = json.loads(df.to_json(orient='records'))

# Upload data to elastic search server
if isinstance(data, dict):    # JSON Object
    es.index(index=INDEX, doc_type=DOC_TYPE, body=data)
else:    # JSON Array
    for record in data:
        es.index(index=INDEX, doc_type=DOC_TYPE, body=record)
```

The data would be uploaded to url

```shell
http://localhost:9200/[INDEX]/[DOC_TYPE]/
```

### Query the Uploaded Data

Here is a simple example using [airline data](http://stat-computing.org/dataexpo/2009/the-data.html) in 2000.   Suppose we have already upload this data to `http://localhost:9200/airline/all`.

We want to retrieve the records which has exactly zero departure delay:

```python
body = {
  "query": {
    "match": {
      "DepDelay": 0
    }
  }
}
es.search(index="airline", doc_type="all", body=body)
```

Then we get the following response:

```
{"_shards": {"failed": 0, "successful": 5, "total": 5},
 "hits": {"hits": [
     {"_id": "AV5L8ptgYzpnPPxiI-4u",
    "_index": "airline",
    "_score": 1.0,
    "_source": {"ActualElapsedTime": 388.0,
     "AirTime": 360.0,
     "ArrDelay": 31.0,
     "ArrTime": 1128.0,
     "CRSArrTime": 1057,
     "CRSDepTime": 700,
     "CRSElapsedTime": 357,
     "CancellationCode": None,
     "Cancelled": 0,
     "CarrierDelay": None,
     "DayOfWeek": 7,
     "DayofMonth": 2,
     "DepDelay": 0.0,
     ...
     "Year": 2000},
    "_type": "all"},
   ...],
  "max_score": 1.0,
  "total": 7863},
 "timed_out": False,
 "took": 520}
```

For other types of query, please check [Official Document for Query API](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html).

### Data Type Mapping

The [PUT mapping API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html) allows you to add a new type to an existing index, or add new fields to an existing type.

## Setup Kibana Server

Follow the [installation instruction on official web page](https://www.elastic.co/guide/en/kibana/current/install.html), then you should be able to start running Kibana.

Note that the ElasticSearch server should also be running so that we can do data visualization on Kibana interface since Kibana queries data from ElasticSearch server.

For default, Kibana runs on `http://localhost:5601` and the elasticsearch server url it would use for requesting is `http://localhost:9200`.

![](https://i.imgur.com/WFZcDN5.png)


On this interface, you should first specify the `Index Pattern` in Management page, which determines which data you want to view on Kibana.   For example, I can use `air*` as an index pattern to view data with index prefix "air" (e.g., index="airline").

![](https://i.imgur.com/WovuDRY.png)

## Data Visualization Using Kibana

### Discover

You can use [String Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl-query-string-query.html#query-string-syntax) to filter out the data you want, and save the filtered result.

For example, to retrieve the records which has departure delay less than 5 minutes, we can use this string query:

```
DepDelay: (<5)
```

### Visualization

Except for the whole data, you can also use the filtered result that are saved to do visualization.

### Dashboard

To add plots up to your dashboard, you should first create the plot in Visualization section and save it.   After that, you can add this saved plot to your dashboard and control the data to feed to this plot.

## References
- [Python + Elasticsearch. First steps.](https://tryolabs.com/blog/2015/02/17/python-elasticsearch-first-steps/)
- [How to Install Elasticsearch Cluster](https://neil-tutorial.blogspot.tw/2016/01/elasticsearch-cluster.html)
- [Integrating Hadoop and Elasticsearch](https://db-blog.web.cern.ch/blog/prasanth-kothuri/2016-05-integrating-hadoop-and-elasticsearch-%E2%80%93-part-2-%E2%80%93-writing-and-querying)
- [Lucene - Query String Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl-query-string-query.html#query-string-syntax)