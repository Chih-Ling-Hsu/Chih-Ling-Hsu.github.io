---
title: 'Connect to Elastic Cloud with R Client'
layout: post
tags:
  - ElasticSearch
  - NLP
  - R
category: Programming
mathjax: false
---


`Elastic` (part of the [rOpenSci project](http://ropensci.org/)) is a general purpose R interface to Elasticsearch.

- [Github Page](https://github.com/ropensci/elastic)
- [Tutorial](https://ropensci.org/tutorials/elastic_tutorial.html)

<!--more-->

```
Scott Chamberlain (NA). elastic: General Purpose
  Interface to 'Elasticsearch'. R package version
  0.8.0.9100. https://github.com/ropensci/elastic
```


After you install this library following the instructions, you can import the library as follows.

```
library("elastic")
```


## Setup ElasticSearch Server

Go to Elastic Cloud and create a cluster. (Free trial for 14 days available.)

Note that we need the **HTTPS endpoint** from the *Overview* page.

![](https://i.imgur.com/gMJAxFU.png)

## Connect to the cluster

Note that you should specify the connection information to connect with authentication.

- **es_host**: The endpoint of the cluster, without prefixes such as 'http'. (i.e., `xxxxx.us-east-1.aws.found.io`)
- **es_path**: In this case we can just leave it blank.
- **es_user**: The user name for cluster authentication.The default user name of Elastic Cloud is `elastic`.
- **es_pwd**: The password for cluster authentication.You can find it on the *Security* page.
- **es_port**: The port number of the cluster on the server. The default port is `9243` on Elastic Cloud. 
- **es_transport_schema**: the transport protocal, use `https` here.

```r
 connect(es_host = "aea56252e39a17de2c3f908d64a82ad9.us-east-1.aws.found.io", es_path = "", es_user="elastic", es_pwd = "g8QHIaXkRPqLEKvdyEiCrKV1", es_port = 9243, es_transport_schema  = "https")
```

```
transport:  https 
host:       aea56252e39a17de2c3f908d64a82ad9.us-east-1.aws.found.io 
port:       9243 
path:       NULL 
username:   elastic 
password:   (secret)
errors:     simple 
headers (names):  NULL 
```{: #well}


## Upload Data

### First, we need to load some data.

Public Library of Science (PLOS) data is a dataset inluded in the elastic package is metadata for PLOS scholarly articles. 

```r
plosdat <- system.file("examples", "plos_data.json", package = "elastic")
```


### Then, upload the data we've just loaded.

we use the function `docs_bulk` to upload the data `plosdat`

```r
invisible(docs_bulk(plosdat))
```

## Manipulate the uploaded data

Search the `plos` index, limit to `1` result.

```r
Search(index = "plos", size = 1)$hits$hits
```

```
[[1]]
[[1]]$`_index`
[1] "plos"

[[1]]$`_type`
[1] "article"

[[1]]$`_id`
[1] "0"

[[1]]$`_score`
[1] 1

[[1]]$`_source`
[[1]]$`_source`$id
[1] "10.1371/journal.pone.0007737"

[[1]]$`_source`$title
[1] "Phospholipase C-β4 Is Essential for the Progression of the Normal Sleep Sequence and Ultradian Body Temperature Rhythms in Mice"
```{: #well}

Search the `plos` index, and the `article` document type. Query for `antibody`, limit to `1` result.


```r
Search(index = "plos", type = "article", q = "antibody", size = 1)$hits$hits
```

```
[[1]]
[[1]]$`_index`
[1] "plos"

[[1]]$`_type`
[1] "article"

[[1]]$`_id`
[1] "568"

[[1]]$`_score`
[1] 4.165291

[[1]]$`_source`
[[1]]$`_source`$id
[1] "10.1371/journal.pone.0085002"

[[1]]$`_source`$title
[1] "Evaluation of 131I-Anti-Angiotensin II Type 1 Receptor Monoclonal Antibody as a Reporter for Hepatocellular Carcinoma"
```{: #well}



For more details/examples for the manipulation of R client using `elastic` library, please refer to [its tutorial](https://ropensci.org/tutorials/elastic_tutorial.html).



