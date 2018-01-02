---
title: 'Mining Association Rules on New York City Bike Dataset'
layout: post
tags:
  - Data Mining
  - Python
category: Programming
mathjax: true
---


What   we   want   to   do here   is  to design 3 mining tasks with their definitions of transactions and   find   some   rules   behind them.

For   each   task,   we   should
- Try **at   least   two   discretization   methods** (divided   by 10, divided   by   20, ...)
- Try **at   least two   algorithms** (Apriori, FP-growth, ...) to   find   association   rules.
- List the interesting  rules.
- **Compare**   the   differences   between   them.

<!--more-->

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.basemap import Basemap
import copy
import json
import math
from collections import OrderedDict
import warnings
warnings.filterwarnings('ignore')
%matplotlib inline
```

## Dataset

[New   York   Citi   Bike   Trip   Histories](https://www.citibikenyc.com/system-data)

We'll   use   [`201707-citibike-tripdata.csv.zip`](https://s3.amazonaws.com/tripdata/201707-citibike-tripdata.csv.zip)   in   this   homework   only.

### A. Schema

We have already preprocessed this dataset into the following 2 data frames:

**1. Every Station's Information**
- `Station ID`
- `Station Name`
- `Station Latitude`
- `Station Longitude`


```python
df_loc = pd.read_csv("data/station_information.csv")
df_loc.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>station id</th>
      <th>station name</th>
      <th>station latitude</th>
      <th>station longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>539</td>
      <td>Metropolitan Ave &amp; Bedford Ave</td>
      <td>40.715348</td>
      <td>-73.960241</td>
    </tr>
    <tr>
      <th>1</th>
      <td>293</td>
      <td>Lafayette St &amp; E 8 St</td>
      <td>40.730207</td>
      <td>-73.991026</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3242</td>
      <td>Schermerhorn St &amp; Court St</td>
      <td>40.691029</td>
      <td>-73.991834</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2002</td>
      <td>Wythe Ave &amp; Metropolitan Ave</td>
      <td>40.716887</td>
      <td>-73.963198</td>
    </tr>
    <tr>
      <th>4</th>
      <td>361</td>
      <td>Allen St &amp; Hester St</td>
      <td>40.716059</td>
      <td>-73.991908</td>
    </tr>
  </tbody>
</table>
</div>



**2. Every Station's Flow Data**
- `Station ID`
- `Time`: One day is splitted into 48 segments in this case. (Every 30 minutes)
- `In Flow Count`: The number of trips move to the station
- `Out Flow Count`: The number of trips move from the station


```python
df_flow = pd.read_csv("data/station_flow.csv")
df_flow['time'] = pd.to_datetime(df_flow['time'], format='%Y-%m-%d %H:%M:%S')
df_flow.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>station id</th>
      <th>time</th>
      <th>in_flow_count</th>
      <th>out_flow_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>72</td>
      <td>2017-07-01 00:00:00</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>72</td>
      <td>2017-07-01 10:00:00</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>72</td>
      <td>2017-07-01 10:30:00</td>
      <td>7.0</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>72</td>
      <td>2017-07-01 11:00:00</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>72</td>
      <td>2017-07-01 12:00:00</td>
      <td>2.0</td>
      <td>6.0</td>
    </tr>
  </tbody>
</table>
</div>



### B. Algorithms

- **Apriori**
    - Github repository: https://github.com/ymoch/apyori
    - How to Use: Install with pip: `pip install apyori`
    ```
    The MIT License (MIT)

    Copyright (c) 2016 Yu Mochizuki

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in all
    copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
    SOFTWARE.
    ```


```python
from apyori import apriori

def apriori_find_association_rules(dataset, minsup, minconf):
    records = list(apriori(dataset, min_support=minsup, min_confidence=minconf))
    return records

def apriori_show_mining_results(records):
    ap = []
    for record in records:
        converted_record = record._replace(ordered_statistics=[x._asdict() for x in record.ordered_statistics])
        ap.append(converted_record._asdict())
    
    #print("Frequent Itemsets:\n------------------")
    #for ptn in ap:
    #    print('({})  support = {}'.format(", ".join(ptn["items"]), round(ptn["support"], 3)))
    #print()
    print("Rules:\n------")
    for ptn in ap:
        for rule in ptn["ordered_statistics"]:
            head = rule["items_base"]
            tail = rule["items_add"]
            if len(head) == 0 or len(tail) == 0:
                continue
            confidence = rule["confidence"]
            print('({}) ==> ({})  confidence = {}'.format(', '.join(head), ', '.join(tail), round(confidence, 3)))
    print()
```

- **FP-Growth**
    - Github repository: https://github.com/evandempsey/fp-growth
    - How to Use: Install with pip: `pip install pyfpgrowth`
    ```
    Copyright (c) 2016, Evan Dempsey
    All rights reserved.

    Permission to use, copy, modify, and/or distribute this software for any
    purpose with or without fee is hereby granted, provided that the above
    copyright notice and this permission notice appear in all copies.

    THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES
    WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF
    MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR
    ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES
    WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN
    ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF
    OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
    ```


```python
import pyfpgrowth

def fp_find_association_rules(dataset, minsup, minconf):
    patterns = pyfpgrowth.find_frequent_patterns(dataset, minsup*len(dataset))
    rules = pyfpgrowth.generate_association_rules(patterns, minconf)
    return (patterns, rules)

def fp_show_mining_results(ap, N):
    (patterns, rules) = ap
    #print("Frequent Itemsets:\n------------------")
    #for key, val in patterns.items():
    #    print('{}  support = {}'.format(key, round(val/N, 3)))
    #print()
    print("Rules:\n------")
    for key, val in rules.items():
        head = key
        tail = val[0]
        confidence = val[1]
        if len(tail) == 0:
            continue
        print('({}) ==> ({})  confidence = {}'.format(', '.join(head), ', '.join(tail), round(confidence, 3)))
    print()
```

## Task 1: Find Rules between `in-flow` and `out-flow` of Station `519`

The first task is that we want to find that if there is any association rules between in-flow counts and out-flow counts of station `519`.

For example,

$$
\{High~inflow~count\} \rightarrow \{High~outflow~count\}
$$

### A. Define Transactions

A   transaction   consists of
- `in-flow` for `station id = 519`
- `out-flow` for `station id = 519`


```python
dat = df_flow[df_flow['station id'] == 519][['in_flow_count', 'out_flow_count']]
dat.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>in_flow_count</th>
      <th>out_flow_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>244329</th>
      <td>3.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>244330</th>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>244331</th>
      <td>1.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>244332</th>
      <td>4.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>244333</th>
      <td>7.0</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>



Let's do some **observations** on the transaction dataset.

1. Make sure there is no missing values.
2. See the minimum/maximum of flow counts values
3. See the distribution of flow counts values.


```python
pd.isnull(dat).sum()
```




    in_flow_count     0
    out_flow_count    0
    dtype: int64




```python
print("Min: {}\nMax: {}".format(dat.values.min(), dat.values.max()))
```

    Min: 0.0
    Max: 116.0



```python
fig, axes = plt.subplots(nrows=2, ncols=2, figsize=(15,5))

ax = plt.subplot(2, 2, 1)
dat['in_flow_count'].hist(bins=25)
ax.set_title("in flow count")

ax = plt.subplot(2, 2, 2)
dat['out_flow_count'].hist(bins=25)
ax.set_title("out flow count")

ax = plt.subplot(2, 2, 3)
ax.set_yscale('log')
dat['in_flow_count'].hist(bins=25)
ax.set_title("in flow count (log scale)")

ax = plt.subplot(2, 2, 4)
ax.set_yscale('log')
dat['out_flow_count'].hist(bins=25)
ax.set_title("out flow count (log scale)")

fig.tight_layout()
```


![](https://i.imgur.com/CMVDI75.png)

It is found that the flow counts values **highly concentrates** in the interval `0~20`.

I'm going to use the `pandas` functions [**`cut`**](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.cut.html)  and [**`qcut`**](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.qcut.html) to split intervals of in/out flow counts.

```
cut
- `bins`: int, sequence of scalars, or IntervalIndex
    - If bins is an int, it defines the number of equal-width bins in the range of x.
    - If bins is a sequence it defines the bin edges allowing for non-uniform bin width.
- `labels` : array or boolean, default None
    - Used as labels for the resulting bins. 
 

qcut
- `q`: integer or array of quantiles
    - If bins is an int, it defines the number of quantiles. 10 for deciles, 4 for quartiles, etc. 
    - If bins is an array, it defines the array of quantiles, e.g. [0, .25, .5, .75, 1.] for quartiles.
- `labels` : array or boolean, default None
    - Used as labels for the resulting bins. 
```

- **Discretization Method (1). `equi-sized` approach**

    With `equi-sized` approach, I partition the continuous domain into intervals with equal length.
    Here I also define their labels:

    > - `"level-1" = size 0%~20%`
    > - `"level-2" = size 20%~40%`
    > - `"level-3" = size 40%~60%`
    > - `"level-4" = size 60%~80%`
    > - `"level-5" = size 80%~100%`


```python
dat_1 = copy.deepcopy(dat)
```


```python
dat_1['in_flow_count'] = pd.cut(dat_1['in_flow_count'], bins = 5, \
                                labels = ["in.level-1", "in.level-2", "in.level-3", \
                                          "in.level-4", "in.level-5"]).astype(str)
pd.cut(dat['in_flow_count'], bins = 5).value_counts()
```




    (-0.091, 18.2]    1263
    (18.2, 36.4]       141
    (36.4, 54.6]        48
    (54.6, 72.8]        34
    (72.8, 91.0]         2
    Name: in_flow_count, dtype: int64



| Label | Interval | Depth (Number of Instances) |
| - | - | - |
| in.level-1 | (-0.091, 18.2] | 1263 |
| in.level-2 | (18.2, 36.4] | 141 |
| in.level-3 | (36.4, 54.6] | 48 |
| in.level-4 | (54.6, 72.8] | 34 |
| in.level-5 | (72.8, 91.0] | 2 |


```python
dat_1['out_flow_count'] = pd.cut(dat_1['out_flow_count'], bins = 5, \
                                labels = ["out.level-1", "out.level-2", "out.level-3", \
                                          "out.level-4", "out.level-5"]).astype(str)
pd.cut(dat['out_flow_count'], bins = 5).value_counts()
```




    (-0.116, 23.2]    1317
    (23.2, 46.4]       111
    (46.4, 69.6]        40
    (69.6, 92.8]        18
    (92.8, 116.0]        2
    Name: out_flow_count, dtype: int64



| Label | Interval | Depth (Number of Instances) |
| - | - | - |
| out.level-1 | (-0.116, 23.2] | 1317 |
| out.level-2 | (23.2, 46.4] | 111 |
| out.level-3 | (46.4, 69.6] | 40 |
| out.level-4 | (69.6, 92.8] | 18 |
| out.level-5 | (92.8, 116.0] | 2 |

- **Discretization Method (2). `equi-depth` approach**
    
    With `equi-depth` approach, I partition the data values into intervals with equal size along the ordering of the data.
    
    Here I also define their labels:

    > - `"zero" = quantile 0~20`
    > - `"extreme-low" = quantile 20~40`
    > - `"low" = quantile 40~60`
    > - `"medium" = quantile 60~80`
    > - `"high" = quantile 80~100`


```python
dat_2 = copy.deepcopy(dat)
```


```python
dat_2['in_flow_count'] = pd.qcut(dat_2['in_flow_count'], q = 5, \
                                labels = ["in.zero", "in.extreme-low", "in.low", \
                                          "in.medium", "in.high"]).astype(str)
pd.qcut(dat['in_flow_count'], q = 5).value_counts()
```




    (-0.001, 1.0]    403
    (3.0, 7.0]       315
    (7.0, 14.0]      283
    (14.0, 91.0]     278
    (1.0, 3.0]       209
    Name: in_flow_count, dtype: int64



| Label | Interval | Depth (Number of Instances) |
| - | - | - |
| in.zero | (-0.001, 1.0] | 403 |
| in.extreme-low | (1.0, 3.0] | 209 |
| in.low | (3.0, 7.0] | 315 |
| in.medium | (7.0, 14.0] | 283 |
| in.high | (14.0, 91.0] | 278 |


```python
dat_2['out_flow_count'] = pd.qcut(dat_2['out_flow_count'], q = 5, \
                                labels = ["out.zero", "out.extreme-low", "out.low", \
                                          "out.medium", "out.high"]).astype(str)
pd.qcut(dat['out_flow_count'], q = 5).value_counts()
```




    (-0.001, 1.0]    423
    (3.0, 7.0]       307
    (7.0, 14.0]      292
    (14.0, 116.0]    284
    (1.0, 3.0]       182
    Name: out_flow_count, dtype: int64



| Label | Interval | Depth (Number of Instances) |
| - | - | - |
| out.zero | (-0.001, 1.0] | 423 |
| out.extreme-low | (1.0, 3.0] | 182 |
| out.low | (3.0, 7.0] | 307 |
| out.medium | (7.0, 14.0] | 292 |
| out.high | (14.0, 116.0] | 284 |

### B. Association Rule Mining

Using Apriori and FP-Growth algorithms, we want to discover the relationship between `in-flow counts` and `out-flow counts` of station `519`.

The support threshold and confidence threshold are determined by the quality and quantity of rules found.   That is, number of rules found should not be too small or too large and the rules found should have confidence as high as possible.

To compare the differences, I use the same support/confidence threshold to mine rules in transaction datasets with different discretization approach.

- First, we mine the association rules of the transaction dataset that is discretized with **`equi-sized` approach**.
    - support threshold = 0.1
    - confidence threshold = 0.2


```python
%%time
print("Apriori\n********")
ap = apriori_find_association_rules(dat_1.values.tolist(), 0.1, 0.2)
```

    Apriori
    ********
    CPU times: user 1.88 ms, sys: 540 µs, total: 2.42 ms
    Wall time: 2.38 ms



```python
apriori_show_mining_results(ap)
```

    Rules:
    ------
    (in.level-1) ==> (out.level-1)  confidence = 0.998
    (out.level-1) ==> (in.level-1)  confidence = 0.957
    



```python
%%time
print("FP-Growth\n*********")
fp = fp_find_association_rules(dat_1.values.tolist(), 0.1, 0.2)
```

    FP-Growth
    *********
    CPU times: user 9.18 ms, sys: 958 µs, total: 10.1 ms
    Wall time: 9.31 ms



```python
fp_show_mining_results(fp, dat_1.shape[0])
```

    Rules:
    ------
    (in.level-1) ==> (out.level-1)  confidence = 0.998
    (out.level-1) ==> (in.level-1)  confidence = 0.957
    


- Next, we mine the association rules of the transaction dataset that is discretized with **`equi-depth` approach**.
    - support threshold = 0.1
    - confidence threshold = 0.2


```python
%%time
print("Apriori\n********")
ap = apriori_find_association_rules(dat_2.values.tolist(), 0.1, 0.2)
```

    Apriori
    ********
    CPU times: user 3.49 ms, sys: 1.94 ms, total: 5.43 ms
    Wall time: 4.91 ms



```python
apriori_show_mining_results(ap)
```

    Rules:
    ------
    (in.high) ==> (out.high)  confidence = 0.878
    (out.high) ==> (in.high)  confidence = 0.859
    (in.medium) ==> (out.medium)  confidence = 0.53
    (out.medium) ==> (in.medium)  confidence = 0.514
    (in.zero) ==> (out.zero)  confidence = 0.821
    (out.zero) ==> (in.zero)  confidence = 0.783
    



```python
%%time
print("FP-Growth\n*********")
fp = fp_find_association_rules(dat_2.values.tolist(), 0.1, 0.2)
```

    FP-Growth
    *********
    CPU times: user 9.77 ms, sys: 641 µs, total: 10.4 ms
    Wall time: 9.97 ms



```python
fp_show_mining_results(fp, dat_2.shape[0])
```

    Rules:
    ------
    (in.high) ==> (out.high)  confidence = 0.878
    (out.high) ==> (in.high)  confidence = 0.859
    (in.medium) ==> (out.medium)  confidence = 0.53
    (out.medium) ==> (in.medium)  confidence = 0.514
    (in.zero) ==> (out.zero)  confidence = 0.821
    (out.zero) ==> (in.zero)  confidence = 0.783
    


### C. Observations

The table below shows the rules found, sorted by their confidence.

| Discretization Method | Rules Found | Confidence |
| - | - | - | - |
| Method (1). `equi-sized` | (in.level-1) <==> (out.level-1) | ==> 0.998; <== 0.957 |
| Method (2). `equi-depth` | (in.high) <==> (out.high) | ==> 0.878; <== 0.859 |
| Method (2). `equi-depth` | (in.zero) <==> (out.zero) | ==> 0.821; <== 0.783 |
| Method (2). `equi-depth` | (in.medium) <==> (out.medium) | ==> 0.530; <== 0.514 |


Using `equi-sized` approach, we can find only `2` rules, which can be conclude as
1. In every 30 minutes, in-flow count of `0~18` indicates out-flow count of `0~23`, and vice versa. (confidence > 95%)

Using `equi-depth` approach, we can find `6` rules, which can be conclude as
1. In every 30 minutes, in-flow count of `15~91` indicates out-flow count of `15~116`, and vice versa. (confidence > 85%)
2. In every 30 minutes, in-flow count of `0` indicates out-flow count of `0`, and vice versa. (confidence > 78%)
3. In every 30 minutes, in-flow count of `8~14` indicates out-flow count of `8~14`, and vice versa. (confidence > 50%)


### D. Comparisons

Rules found by `equi-depth` approach cover all ranges of the flow count except the interval `1~7`.   **With `equi-depth` approach, we can find much more rules.**   I think it is because using `equi-sized` approach, number of instances in the buckets are **too imbalance** (e.g., there are only 2 instances that contains `level-5` in-flow count).


| Top Rules | Discretization Method (1). `equi-sized` approach | Discretization Method (2). `equi-depth` approach |
| - | - | - |
| confidence > 90% | in-flow count of `0~18` <==> out-flow count of `0~23` |  |
| confidence > 80% |  | in-flow count of `15~91` <==> out-flow count of `15~116` |
| confidence > 70% |  | in-flow count of `0` <==> out-flow count of `0` |
| confidence > 60% |  |  |
| confidence > 50% |  | in-flow count of `8~14` <==> out-flow count of `8~14` |

However, the top 1 rule found by 2 discretization approach are actually very different. The top 1 rule found with **`equi-sized` approach** says that **low in-flow tends to co-occur with low out-flow**, while and the top 1 rule found by **`equi-depth` approach** says that **high in-flow tends to co-occur with high out-flow**.

<br>
<br>

| Execution time (ms) | Discretization Method (1). `equi-sized` approach | Discretization Method (2). `equi-depth` approach |
| - | - | - |
| Apriori | 2.42 | 5.43 |
| FP-Growth | 10.1 | 10.4 |

Surprisingly, FP-Growth runs much slower than Apriori in this case.   I think it is because that this transaction dataset is so small (only contains data from station `519`) that constructing FP-trees becomes a significant overhead in FP-Grwoth Algorithm.   Also, I think it is resonable that with `equi-depth` approach, which found more rules, cost more time in excution.

## Task 2: Find Rules between `Time of a Day` and `Flows` of station `519`

The second task is to find that if there is any association rules between the time of a day and the flow counts of station `519`.

For example,

$$
\{Afternoon\} \rightarrow \{High~flow~count\}
$$

### A. Define Transactions

A transaction consists of
- `time of a day`
- `flow count` of station 519 (sum up in-flow count and out-flow count)


```python
dat = df_flow[df_flow['station id'] == 519][['time', 'in_flow_count', 'out_flow_count']]
dat['flow_count'] = dat['in_flow_count'] + dat['out_flow_count']
dat['time'] = ["{:02d}:{:02d}".format(dt.hour, dt.minute) for dt in dat['time']]
dat = dat[['time', 'flow_count']]
dat.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>time</th>
      <th>flow_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>244329</th>
      <td>00:00</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>244330</th>
      <td>00:30</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>244331</th>
      <td>10:00</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>244332</th>
      <td>10:30</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>244333</th>
      <td>11:00</td>
      <td>11.0</td>
    </tr>
  </tbody>
</table>
</div>



- **Discretization Method (1). Every 2 hours**

    I split one day into `12` intervals (every 2 hours as a bucket).


```python
dat_1 = copy.deepcopy(dat)
```


```python
dat_1['time'] = ["{:02d}:00~{:02d}:00".format(math.floor(dt.hour/2)*2, math.floor(dt.hour/2)*2+2) \
                 for dt in dat_1['time']]
dat_1['time'] = dat_1['time'].astype(str)
```

Note that I use the `equi-depth` approach to deal with the flow counts. (Intervals for `low`, `medium`, and `high` flow counts are shown as belows.)


```python
dat_1['flow_count'] = pd.qcut(dat_1['flow_count'], q = 3, \
                                labels = ["low", "medium", "high"]).astype(str)

pd.qcut(dat['flow_count'], q = 3).value_counts()
```




    (-0.001, 5.0]    521
    (17.0, 185.0]    486
    (5.0, 17.0]      481
    Name: flow_count, dtype: int64



- **Discretization Method (2). `Morning`, `Noon`, `Afternoon`, `Evening`, or `Night`**

    > - 00:00 ~ 06:00: `Night`
    > - 06:00 ~ 11:00: `Morning`
    > - 11:00 ~ 13:00: `Noon`
    > - 13:00 ~ 16:00: `Afternoon`
    > - 16:00 ~ 22:00: `Evening`
    > - 22:00 ~ 24:00: `Night`


```python
dat_2 = copy.deepcopy(dat)
```


```python
mapping = ["Night"]*6 + ["Morning"]*5 + ["Noon"]*2 + ["Afternoon"]*3 + ["Evening"]*6 + ["Night"]*2
dat_2['time'] = [mapping[math.floor(dt.hour)] for dt in dat_2['time']]
```

Note that I use the `equi-depth` approach again to deal with the in/out flow counts.    (Intervals for `low`, `medium`, and `high` flow counts are shown as belows.)


```python
dat_2['flow_count'] = pd.qcut(dat_2['flow_count'], q = 3, \
                                labels = ["low", "medium", "high"]).astype(str)

pd.qcut(dat['flow_count'], q = 3).value_counts()
```




    (-0.001, 5.0]    521
    (17.0, 185.0]    486
    (5.0, 17.0]      481
    Name: flow_count, dtype: int64



### B. Association Rule Mining

Using Apriori and FP-Growth algorithms, we want to discover the relationship between `which time of a day` and `flow counts` of station `519`.

The support threshold and confidence threshold are determined by the quality and quantity of rules found.   That is, number of rules found should not be too small or too large and the rules found should have confidence as high as possible.

To compare the differences, I use the same support/confidence threshold to mine rules in transaction datasets with different discretization approach.

- First, we mine the association rules of the transaction dataset that is discretized with **`Every 2 hors` approach**.
    - support threshold = 0.05
    - confidence threshold = 0.6


```python
%%time
print("Apriori\n********")
ap = apriori_find_association_rules(dat_1.values.tolist(), 0.05, 0.6)
```

    Apriori
    ********
    CPU times: user 3.14 ms, sys: 707 µs, total: 3.85 ms
    Wall time: 3.42 ms



```python
apriori_show_mining_results(ap)
```

    Rules:
    ------
    (00:00~02:00) ==> (low)  confidence = 0.903
    (02:00~04:00) ==> (low)  confidence = 0.992
    (04:00~06:00) ==> (low)  confidence = 0.855
    (08:00~10:00) ==> (high)  confidence = 0.637
    (12:00~14:00) ==> (medium)  confidence = 0.661
    (16:00~18:00) ==> (high)  confidence = 0.774
    (18:00~20:00) ==> (high)  confidence = 0.645
    



```python
%%time
print("FP-Growth\n*********")
fp = fp_find_association_rules(dat_1.values.tolist(), 0.05, 0.6)
```

    FP-Growth
    *********
    CPU times: user 9.18 ms, sys: 381 µs, total: 9.56 ms
    Wall time: 9.28 ms



```python
fp_show_mining_results(fp, dat_1.shape[0])
```

    Rules:
    ------
    (00:00~02:00) ==> (low)  confidence = 0.903
    (12:00~14:00) ==> (medium)  confidence = 0.661
    (16:00~18:00) ==> (high)  confidence = 0.774
    (18:00~20:00) ==> (high)  confidence = 0.645
    (02:00~04:00) ==> (low)  confidence = 0.992
    (04:00~06:00) ==> (low)  confidence = 0.855
    (08:00~10:00) ==> (high)  confidence = 0.637
    


- Next, we mine the association rules of the transaction dataset that is discretized with **`Morning/Afternon/...` approach**.
    - support threshold = 0.05
    - confidence threshold = 0.6


```python
%%time
print("Apriori\n********")
ap = apriori_find_association_rules(dat_2.values.tolist(), 0.05, 0.6)
```

    Apriori
    ********
    CPU times: user 3.34 ms, sys: 1.33 ms, total: 4.67 ms
    Wall time: 3.73 ms



```python
apriori_show_mining_results(ap)
```

    Rules:
    ------
    (Night) ==> (low)  confidence = 0.837
    (low) ==> (Night)  confidence = 0.797
    (Noon) ==> (medium)  confidence = 0.621
    



```python
%%time
print("FP-Growth\n*********")
fp = fp_find_association_rules(dat_2.values.tolist(), 0.05, 0.6)
```

    FP-Growth
    *********
    CPU times: user 9.44 ms, sys: 578 µs, total: 10 ms
    Wall time: 9.61 ms



```python
fp_show_mining_results(fp, dat_2.shape[0])
```

    Rules:
    ------
    (Noon) ==> (medium)  confidence = 0.621
    (Night) ==> (low)  confidence = 0.837
    (low) ==> (Night)  confidence = 0.797
    


### C. Observations

The table below shows the rules found, sorted by their confidence.

| Discretization Method | Rules Found | Confidence |
| - | - | - | - |
| Method (1). `Every 2 hours` | (02:00~04:00) ==> (low) | 0.992 |
| Method (1). `Every 2 hours` | (00:00~02:00) ==> (low) | 0.903 |
| Method (1). `Every 2 hours` | (04:00~06:00) ==> (low) | 0.855 |
| Method (2). `Morning/Afternon/...` | (Night) ==> (low) | 0.837 |
| Method (2). `Morning/Afternon/...` | (low) ==> (Night) | 0.797 |
| Method (1). `Every 2 hours` | (16:00~18:00) ==> (high) | 0.774 |
| Method (1). `Every 2 hours` | (12:00~14:00) ==> (medium) | 0.661 |
| Method (1). `Every 2 hours` | (18:00~20:00) ==> (high) | 0.645 |
| Method (1). `Every 2 hours` | (08:00~10:00) ==> (high) | 0.637 |
| Method (2). `Morning/Afternon/...` | (Noon) ==> (medium) | 0.621 |


Using "`Every 2 hours`" approach, we can find `7` rules, which can be conclude as
1. At `00 ~ 06` o'clock, the flow counts tend to be `lower than 5` in every half hour. (confidence > 85%)
2. At `12 ~ 14` o'clock, the flow counts tend to be in the interval `(5, 17]` in every half hour. (confidence > 65%)
3. At `16 ~ 20` o'clock, the flow counts tend to be `more than 17` in every half hour (confidence > 64%), especially at `16 ~ 18` o'clock (confidence > 75%).
4. At `08 ~ 10` o'clock, the flow counts tend to be in the interval `more than 17` in every half hour. (confidence > 60%)

Using "`Morning/Afternon/...`" approach, we can find `3` rules, which can be conclude as
1. At night (`22 ~ 24` and `00 ~ 06` o'clock), the flow counts tend to be `lower than 5` in every half hour, and vice versa. (confidence > 79%)
2. At noon (`11 ~ 13` o'clock),  the flow counts tend to be in the interval `(5, 17]` in every half hour. (confidence > 60%)


### D. Comparisons

With "`Every 2 hours`" approach, we can find more rules.  I think it is becuase with this approach, we've got more buckets and it happens to match the scenario that flow counts change rapidly at the scale of hour.


| Top Rules | Discretization Method (1). "`Every 2 hours`" approach | Discretization Method (2). "`Morning/Afternon/...`" approach |
| - | - | - |
| confidence > 90% | (02:00~04:00) ==> `less than 5` flow counts per 30 minutes<br>(00:00~02:00) ==> `less than 5` flow counts per 30 minutes |  |
| confidence > 80% | (04:00~06:00) ==> `less than 5` flow counts per 30 minutes | (Night) ==> `less than 5` flow counts per 30 minutes |
| confidence > 70% | (16:00~18:00) ==> `more than 17` flow counts per 30 minutes | `less than 5` flow counts per 30 minutes ==> (Night) |
| confidence > 60% | (12:00~14:00) ==> `5 ~ 17` flow counts per 30 minutes<br>(18:00~20:00) ==> `more than 17` flow counts per 30 minutes<br>(08:00~10:00) ==> `more than 17` flow counts per 30 minutes | (Noon) ==> `5 ~ 17` flow counts per 30 minutes |

If you check the rules carefully, you would find that the rules found by Method (1) almost cover the rules found by Method (2).   However, we would say that the rules found by Method (2) is more instinctive at first sight.

<br>
<br>

| Execution time (ms) | Discretization Method (1). "`Every 2 hours`" approach | Discretization Method (2). "`Morning/Afternon/...`" approach |
| - | - | - |
| Apriori | 3.85 | 4.67 |
| FP-Growth | 9.56 | 10.0 |

FP-Growth runs much slower than Apriori in this case.   I think it is again because that this transaction dataset is so small (only contains data from station `519`) that constructing FP-trees becomes a significant overhead in FP-Grwoth Algorithm.

## Task 3: Find Rules between `Station Locations` and Their  `Daily Flows`

The third task is to find if there is any association rules between the location of a station and its daily flow counts.

For example,

$$
\{40 < longitude \leq 40.5 \} \rightarrow \{High~daily~flow~count\}
$$

### A. Define Transactions

A transaction consists of
- `station latitude`
- `station longitude`
- `daily flow counts` (sum up everyday in-flow count and out-flow count)

Note that I use _daily flow count_ instead of _flow count in every half hour_ to avoid generating only rules about zero or low flow count. 


```python
dat = pd.merge(df_flow, df_loc, on=['station id'], how='left')
dat['flow_count'] = dat['in_flow_count'] + dat['out_flow_count']
dat['day'] = [dt.day for dt in dat['time']]
dat = dat.groupby(['station latitude', 'station longitude', "day"], as_index=False) \
            .agg({'flow_count': 'sum'})
dat = dat[['station latitude', 'station longitude', 'flow_count']]
dat.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>station latitude</th>
      <th>station longitude</th>
      <th>flow_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>40.6554</td>
      <td>-74.010628</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>40.6554</td>
      <td>-74.010628</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>40.6554</td>
      <td>-74.010628</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>40.6554</td>
      <td>-74.010628</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>40.6554</td>
      <td>-74.010628</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



- **Discretization Method (1). `equi-sized` approach**

    With `equi-sized` approach, I partition the continuous domain into intervals with equal length.


```python
pd.cut(dat['station latitude'], bins = 5).value_counts()
```




    (40.715, 40.745]    5425
    (40.685, 40.715]    4805
    (40.745, 40.774]    3968
    (40.655, 40.685]    2945
    (40.774, 40.804]    2480
    Name: station latitude, dtype: int64




```python
pd.cut(dat['station longitude'], bins = 5).value_counts()
```




    (-74.012, -73.985]    7626
    (-73.985, -73.957]    7347
    (-73.957, -73.93]     3875
    (-74.04, -74.012]      651
    (-74.067, -74.04]      124
    Name: station longitude, dtype: int64




```python
dat_1 = copy.deepcopy(dat)
```


```python
dat_1['station latitude'] = pd.cut(dat_1['station latitude'], bins = 5).astype(str)
dat_1['station latitude'] = "latitude = " + dat_1['station latitude']
dat_1['station longitude'] = pd.cut(dat_1['station longitude'], bins = 5).astype(str)
dat_1['station longitude'] = "longitude = "+ dat_1['station longitude'] 
```

Note that I use the `equi-depth` approach to deal with the flow counts.    (Intervals for ·`extreme-low`, `low`, `medium`, `high`, and `extreme-high` flow counts are shown as belows.)


```python
dat_1['flow_count'] = pd.qcut(dat_1['flow_count'], q = 5, \
                             labels = ["extreme-low", "low", "medium", "high", "extreme-high"]).astype(str)
pd.qcut(dat['flow_count'], q = 5).value_counts()
```




    (-0.001, 51.0]     3943
    (51.0, 96.0]       3939
    (163.0, 286.0]     3923
    (286.0, 1532.0]    3919
    (96.0, 163.0]      3899
    Name: flow_count, dtype: int64



- **Discretization Method (2). `equi-depth` approach**

    With `equi-depth` approach, I partition the data values into intervals with equal size along the ordering of the data.


```python
pd.qcut(dat['station latitude'], q = 5).value_counts()
```




    (40.735, 40.762]    3937
    (40.691, 40.715]    3937
    (40.654, 40.691]    3937
    (40.762, 40.804]    3906
    (40.715, 40.735]    3906
    Name: station latitude, dtype: int64




```python
pd.qcut(dat['station longitude'], q = 5).value_counts()
```




    (-73.976, -73.957]    3937
    (-73.998, -73.987]    3937
    (-74.068, -73.998]    3937
    (-73.957, -73.93]     3906
    (-73.987, -73.976]    3906
    Name: station longitude, dtype: int64




```python
dat_2 = copy.deepcopy(dat)
```


```python
dat_2['station latitude'] = pd.qcut(dat_2['station latitude'], q = 5).astype(str)
dat_2['station latitude'] = "latitude = " + dat_2['station latitude']
dat_2['station longitude'] = pd.qcut(dat_2['station longitude'], q = 5).astype(str)
dat_2['station longitude'] = "longitude = "+ dat_2['station longitude'] 
```

Note that I use the `equi-depth` approach again to deal with the flow counts.        (Intervals for ·`extreme-low`, `low`, `medium`, `high`, and `extreme-high` flow counts are shown as belows.)


```python
dat_2['flow_count'] = pd.qcut(dat_2['flow_count'], q = 5, \
                             labels = ["extreme-low", "low", "medium", "high", "extreme-high"]).astype(str)
pd.qcut(dat['flow_count'], q = 5).value_counts()
```




    (-0.001, 51.0]     3943
    (51.0, 96.0]       3939
    (163.0, 286.0]     3923
    (286.0, 1532.0]    3919
    (96.0, 163.0]      3899
    Name: flow_count, dtype: int64



### B. Association Rule Mining

Using Apriori and FP-Growth algorithms, we want to discover the relationship between `station locations` and their `daily flow counts`.

The support threshold and confidence threshold are determined by the quality and quantity of rules found.   That is, number of rules found should not be too small or too large and the rules found should have confidence as high as possible.

To compare the differences, I use the same support/confidence threshold to mine rules in transaction datasets with different discretization approach.

- First, we mine the association rules of the transaction dataset that is discretized with **`equi-sized` approach**.
    - support threshold = 0.08
    - confidence threshold = 0.4


```python
%%time
print("Apriori\n********")
ap = apriori_find_association_rules(dat_1.values.tolist(), 0.08, 0.4)
```

    Apriori
    ********
    CPU times: user 30.2 ms, sys: 2.86 ms, total: 33 ms
    Wall time: 30.7 ms



```python
apriori_show_mining_results(ap)
```

    Rules:
    ------
    (extreme-high) ==> (latitude = (40.715, 40.745])  confidence = 0.529
    (extreme-high) ==> (longitude = (-74.012, -73.985])  confidence = 0.64
    (high) ==> (longitude = (-74.012, -73.985])  confidence = 0.526
    (latitude = (40.715, 40.745]) ==> (longitude = (-74.012, -73.985])  confidence = 0.514
    (latitude = (40.745, 40.774]) ==> (longitude = (-73.985, -73.957])  confidence = 0.492
    (low) ==> (longitude = (-73.985, -73.957])  confidence = 0.469
    (medium) ==> (longitude = (-73.985, -73.957])  confidence = 0.488
    



```python
%%time
print("FP-Growth\n*********")
fp = fp_find_association_rules(dat_1.values.tolist(), 0.08, 0.4)
```

    FP-Growth
    *********
    CPU times: user 166 ms, sys: 2.63 ms, total: 169 ms
    Wall time: 168 ms



```python
fp_show_mining_results(fp, dat_1.shape[0])
```

    Rules:
    ------
    (medium) ==> (longitude = (-73.985, -73.957])  confidence = 0.488
    (high) ==> (longitude = (-74.012, -73.985])  confidence = 0.526
    (low) ==> (longitude = (-73.985, -73.957])  confidence = 0.469
    (latitude = (40.745, 40.774]) ==> (longitude = (-73.985, -73.957])  confidence = 0.492
    (latitude = (40.715, 40.745]) ==> (longitude = (-74.012, -73.985])  confidence = 0.514
    


- Next, we mine the association rules of the transaction dataset that is discretized with **`equi-depth` approach**.
    - support threshold = 0.08
    - confidence threshold = 0.4


```python
%%time
print("Apriori\n********")
ap = apriori_find_association_rules(dat_2.values.tolist(), 0.08, 0.4)
```

    Apriori
    ********
    CPU times: user 29.9 ms, sys: 1.09 ms, total: 31 ms
    Wall time: 30.4 ms



```python
apriori_show_mining_results(ap)
```

    Rules:
    ------
    (extreme-high) ==> (latitude = (40.735, 40.762])  confidence = 0.451
    (latitude = (40.735, 40.762]) ==> (extreme-high)  confidence = 0.449
    (extreme-low) ==> (latitude = (40.654, 40.691])  confidence = 0.403
    (latitude = (40.654, 40.691]) ==> (extreme-low)  confidence = 0.404
    



```python
%%time
print("FP-Growth\n*********")
fp = fp_find_association_rules(dat_2.values.tolist(), 0.08, 0.4)
```

    FP-Growth
    *********
    CPU times: user 159 ms, sys: 2.69 ms, total: 162 ms
    Wall time: 162 ms



```python
fp_show_mining_results(fp, dat_2.shape[0])
```

    Rules:
    ------
    (extreme-high) ==> (latitude = (40.735, 40.762])  confidence = 0.451
    (latitude = (40.735, 40.762]) ==> (extreme-high)  confidence = 0.449
    (extreme-low) ==> (latitude = (40.654, 40.691])  confidence = 0.403
    (latitude = (40.654, 40.691]) ==> (extreme-low)  confidence = 0.404
    


It is strange that number of rules found by Apriori is greater than number of rules found by FP-Growth using the transaction dataset discretized by `equi-sized` approach.   I later confirm that the rules found by Apriori are all correct.   Hence **in the following discussion, I am going to use the mining result of Apriori**.

So why did this implementation of FP-Growth found less rules?

Check out the frequent itemset found by FP-Growth, I found that this implementation of FP-growth missed some 1-frequent itemsets.   For example, it showed that itemset `('extreme-high', 'latitude = (40.715, 40.745]')` is frequent, but it didn't show that itemset `('extreme-high')` is frequent while showing that itemset `('latitude = (40.715, 40.745]')` is frequent.

This is the reason why using this implementation of FP-Growth cannot find rule `(extreme-high) ==> (latitude = (40.715, 40.745])`

### C. Observations

Note the following function `show_discretization_result()` is defined in the [appendix](#Appendix).


```python
show_discretization_result()
```


![](https://i.imgur.com/fpKkpDk.png)


The table below shows the rules found, sorted by their confidence.

| Discretization Method | Rules Found | Confidence |
| - | - | - | - |
| Method (1). `equi-sized` | (extreme-high) ==> (longitude = (-74.012, -73.985]) | 0.640 |
| Method (1). `equi-sized` | (extreme-high) ==> (latitude = (40.715, 40.745]) | 0.529 |
| Method (1). `equi-sized` | (high) ==> (longitude = (-74.012, -73.985]) | 0.526 |
| Method (1). `equi-sized` | (latitude = (40.715, 40.745]) ==> (longitude = (-74.012, -73.985]) | 0.514 |
| Method (1). `equi-sized` | (latitude = (40.745, 40.774]) ==> (longitude = (-73.985, -73.957]) | 0.492 |
| Method (1). `equi-sized` | (medium) ==> (longitude = (-73.985, -73.957])  | 0.488 |
| Method (1). `equi-sized` | (low) ==> (longitude = (-73.985, -73.957]) | 0.469 |
| Method (2). `equi-depth` | (latitude = (40.736, 40.762]) <==> (extreme-high) | ==> 0.451; <== 0.449 |
| Method (2). `equi-depth` | (latitude = (40.654, 40.691]) <==> (extreme-low) | ==> 0.404; <== 0.403 |


Using `equi-sized` approach, we can find `7` rules, which can be conclude as
1. `Extreme-high` daily flow count ( `more than 286` per day) tend to occur at the area of latitude `40.715 ~ 40.745` and longitude `-74.012 ~ -73.985`. (confidence >52%)
2. `High` daily flow counts (`164 ~ 286` per day) tend to occur at the area of latitude `40.715 ~ 40.745`. (confidence >50%)
3. Stations at latitude `40.715 ~ 40.745` tend to locate at longitude `-74.012 ~ -73.985`. (confidence >50%)
4. Stations at latitude `40.745 ~ 40.774` tend to locate at longitude `-73.985 ~ -73.957`. (confidence $\simeq$50%)
5. `Medium` and `low` daily flow counts (`52 ~ 163` per day) tend to occur at the area of longitude `-73.985 ~ -73.957`. (confidence $\simeq$50%)

Using `equi-depth` approach, we can find `4` rules, which can be conclude as
1. Stations at latitude `40.736 ~ 40.762` tend to have `extreme-high` daily flow count ( `more than 286` per day). (confidence $\simeq$45%)
2. Stations at latitude `40.654 ~ 40.691` tend to have `extreme-low` daily flow count ( `more than 286` per day). (confidence >40%)

In general, stations at latitude `40.715 ~ 40.762`(_Manhattan_ & _Long Island City, Brooklyn_) tend to have `high` to `extreme-high` daily flow counts, while stations at latitude `40.715 ~ 40.762`(_Williamsburg, Brooklyn & Red Hook, Brooklyn_) tend to have `low` to `extreme-low` daily flow counts.

### D. Comparisons




| Top Rules | Discretization Method (1). `equi-sized` approach | Discretization Method (2). `equi-depth` approach |
| - | - | - |
| confidence > 60% | `more than 286` flow counts per day ==> (longitude = (-74.012, -73.985])	 |  |
| confidence > 55% |  |  |
| confidence > 50% | `more than 286` flow counts per day ==> (latitude = (40.715, 40.745])<br>`164 ~ 286` flow counts per day ==> (longitude = (-74.012, -73.985])<br>(latitude = (40.715, 40.745]) ==> (longitude = (-74.012, -73.985]) |  |
| confidence > 45% | (latitude = (40.745, 40.774]) ==> (longitude = (-73.985, -73.957])<br>`97 ~ 163` flow counts per day ==> (longitude = (-73.985, -73.957])<br>`52 ~ 96` flow counts per day ==> (longitude = (-73.985, -73.957]) | (latitude = (40.736, 40.762]) ==> `more than 286` flow counts per day |
| confidence > 40% |  | (latitude = (40.736, 40.762]) <== `more than 286` flow counts per day<br>(latitude = (40.654, 40.691]) <==> `less than 52` flow counts per day |


**With `equi-sized` approach, we can find more rules and higher confidence.**   I think it is because using `equi-depth` approach would make all splitted areas have the same density of stations, while a station is actually built according to the population or say, estimated flow count, of that area.   As the result, the number of rules mined with `equi-depth` approach and their confidence would likely to be low.


<br>
<br>

| Execution time (ms) | Discretization Method (1). `equi-sized` approach | Discretization Method (2). `equi-depth` approach |
| - | - | - |
| Apriori | 33 | 31 |
| FP-Growth | 169 | 162 |

Surprisingly, FP-Growth still runs much slower than Apriori in this case.   This time we have much bigger transaction dataset, however, the execution time of FP-Growth is still much larger.   In my opinion, the reason may be that this implementation of FP-Growth is not so well-written to be efficient.

## Appendix

The functions for `show_discretization_result()` used in **section C of Task 3** are defined here.


```python
def plot_stations_map(ax, stns, parallels_val, meridians_val):
    # determine range to print based on min, max lat and lon of the data
    lat = list(stns['station latitude'])
    lon = list(stns['station longitude'])
    siz = [(2)**(x/1000) for x in stns['flow_count']]
    margin = 0.01 # buffer to add to the range
    lat_min = min(lat) - margin
    lat_max = max(lat) + margin
    lon_min = min(lon) - margin
    lon_max = max(lon) + margin

    # create map using BASEMAP
    m = Basemap(llcrnrlon=lon_min,
                llcrnrlat=lat_min,
                urcrnrlon=lon_max,
                urcrnrlat=lat_max,
                lat_0=(lat_max - lat_min)/2,
                lon_0=(lon_max - lon_min)/2,
                projection='lcc',
                resolution = 'f',)

    m.drawcoastlines()
    m.fillcontinents(lake_color='aqua')
    m.drawmapboundary(fill_color='aqua')
    m.drawrivers()
    
    m.drawparallels(parallels_val,labels=[False,True,True,False])
    meridians = m.drawmeridians(meridians_val,labels=[True,False,False,True])
    for x in meridians:
        try:
            meridians[x][1][0].set_rotation(45)
        except:
            pass

    # convert lat and lon to map projection coordinates
    lons, lats = m(lon, lat)

    # plot points as red dots
    ax.scatter(lons, lats, marker = 'o', color='r', zorder=5, alpha=0.6, s=1)
```


```python
def show_discretization_result():
    fig, axes = plt.subplots(nrows=1, ncols=2, figsize=(15,15))

    ax = plt.subplot(1, 2, 1)
    ax.set_title("Equi-Sized")
    parallels_val = np.array([40.655, 40.685, 40.715, 40.745, 40.774, 40.804])
    meridians_val = np.array([-73.93, -73.957, -73.985, -74.012, -74.04, -74.067])
    plot_stations_map(ax, dat, parallels_val, meridians_val)

    ax = plt.subplot(1, 2, 2)
    ax.set_title("Equi-Depth")
    parallels_val = np.array([40.655, 40.691, 40.715, 40.735, 40.762, 40.804])
    meridians_val = np.array([-73.93, -73.957, -73.976, -73.987, -73.998, -74.068])
    plot_stations_map(ax, dat, parallels_val, meridians_val)
```
