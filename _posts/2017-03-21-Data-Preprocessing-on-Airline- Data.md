---
title: 'Data Preprocessing on Airline Data'
layout: post
tags:
  - Python
  - Sklearn
  - Pandas
  - DataScience
category: Programming
---

**Data Source and Variable Definition:**

[Statistical Computing Statistical Graphics](http://stat-computing.org/dataexpo/2009/the-data.html)
We are going to use flight information for _2000_.

<!--more-->

**Python Libraries to be used:**


```python
import pandas as pd
from IPython.display import display
from sklearn.preprocessing import OneHotEncoder
from sklearn.preprocessing import LabelEncoder
import numpy as np
```

## Load Dataset

#### pandas.read_csv()
_Useful parameters_:
- sep : str, default ‘,’
- header : int or list of ints. Row number(s) to use as the column names, and the start of the data.
- index_col : int or sequence or False, default None. Column to use as the row labels of the DataFrame.


```python
df = pd.read_csv('data/air2000_test.csv', header=0, index_col=0)
df.head()
```




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



## Dealing with Missing Data


```python
# count the number of missing values per column
display(df.isnull().sum())
```


    Year                    0
    Month                   0
    DayofMonth              0
    DayOfWeek               0
    DepTime                40
    CRSDepTime              0
    ArrTime                42
    CRSArrTime              0
    UniqueCarrier           0
    FlightNum               0
    TailNum                 0
    ActualElapsedTime      42
    CRSElapsedTime          0
    AirTime                42
    ArrDelay               42
    DepDelay               40
    Origin                  0
    Dest                    0
    Distance                0
    TaxiIn                  0
    TaxiOut                 0
    Cancelled               0
    CancellationCode     1000
    Diverted                0
    CarrierDelay         1000
    WeatherDelay         1000
    NASDelay             1000
    SecurityDelay        1000
    LateAircraftDelay    1000
    dtype: int64


### Eliminating Samples or Features with Missing Values
One of the easiest ways to deal with missing data is to simply remove the corresponding features (columns) or samples (rows) from the dataset entirely. We can call the dropna() method of Dataframe to eliminate rows or columns:


```python
# drop columns with ALL NaN
df_drop_col = df.dropna(axis=1, thresh=1)
df_drop_col.head()
```




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
      <th>AirTime</th>
      <th>ArrDelay</th>
      <th>DepDelay</th>
      <th>Origin</th>
      <th>Dest</th>
      <th>Distance</th>
      <th>TaxiIn</th>
      <th>TaxiOut</th>
      <th>Cancelled</th>
      <th>Diverted</th>
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
      <td>233.0</td>
      <td>7.0</td>
      <td>0.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>15</td>
      <td>11</td>
      <td>0</td>
      <td>0</td>
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
      <td>239.0</td>
      <td>40.0</td>
      <td>1.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>5</td>
      <td>47</td>
      <td>0</td>
      <td>0</td>
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
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
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
      <td>226.0</td>
      <td>-7.0</td>
      <td>-2.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>7</td>
      <td>14</td>
      <td>0</td>
      <td>0</td>
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
      <td>244.0</td>
      <td>-4.0</td>
      <td>-4.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>3</td>
      <td>8</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 23 columns</p>
</div>




```python
# drop rows with ANY NaN
df_drop_col_row = df_drop_col.dropna(axis=0, thresh=df_drop_col.shape[1])
df_drop_col_row.head()
```




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
      <th>AirTime</th>
      <th>ArrDelay</th>
      <th>DepDelay</th>
      <th>Origin</th>
      <th>Dest</th>
      <th>Distance</th>
      <th>TaxiIn</th>
      <th>TaxiOut</th>
      <th>Cancelled</th>
      <th>Diverted</th>
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
      <td>233.0</td>
      <td>7.0</td>
      <td>0.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>15</td>
      <td>11</td>
      <td>0</td>
      <td>0</td>
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
      <td>239.0</td>
      <td>40.0</td>
      <td>1.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>5</td>
      <td>47</td>
      <td>0</td>
      <td>0</td>
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
      <td>226.0</td>
      <td>-7.0</td>
      <td>-2.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>7</td>
      <td>14</td>
      <td>0</td>
      <td>0</td>
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
      <td>244.0</td>
      <td>-4.0</td>
      <td>-4.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>3</td>
      <td>8</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2000</td>
      <td>1</td>
      <td>2</td>
      <td>7</td>
      <td>849.0</td>
      <td>846</td>
      <td>1148.0</td>
      <td>1101</td>
      <td>HP</td>
      <td>609</td>
      <td>...</td>
      <td>267.0</td>
      <td>47.0</td>
      <td>3.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>8</td>
      <td>24</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 23 columns</p>
</div>



## Split Target Class From Attributes



```python
X = df_drop_col_row.drop('ArrDelay', 1)
y = [int(arrDelay<=0) for arrDelay in df_drop_col_row['ArrDelay']]
#cols = df_drop_col_row.columns
#new_cols = ['ArrDelay'] + list(set(cols)-set(['ArrDelay']))
#X = df_drop_col_row[new_cols]
X.head()
```




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
      <th>CRSElapsedTime</th>
      <th>AirTime</th>
      <th>DepDelay</th>
      <th>Origin</th>
      <th>Dest</th>
      <th>Distance</th>
      <th>TaxiIn</th>
      <th>TaxiOut</th>
      <th>Cancelled</th>
      <th>Diverted</th>
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
      <td>252.0</td>
      <td>233.0</td>
      <td>0.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>15</td>
      <td>11</td>
      <td>0</td>
      <td>0</td>
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
      <td>252.0</td>
      <td>239.0</td>
      <td>1.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>5</td>
      <td>47</td>
      <td>0</td>
      <td>0</td>
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
      <td>252.0</td>
      <td>226.0</td>
      <td>-2.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>7</td>
      <td>14</td>
      <td>0</td>
      <td>0</td>
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
      <td>255.0</td>
      <td>244.0</td>
      <td>-4.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>3</td>
      <td>8</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2000</td>
      <td>1</td>
      <td>2</td>
      <td>7</td>
      <td>849.0</td>
      <td>846</td>
      <td>1148.0</td>
      <td>1101</td>
      <td>HP</td>
      <td>609</td>
      <td>...</td>
      <td>255.0</td>
      <td>267.0</td>
      <td>3.0</td>
      <td>ATL</td>
      <td>PHX</td>
      <td>1587</td>
      <td>8</td>
      <td>24</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 22 columns</p>
</div>



## Dealing with categorical Dara
One-Hot Encoding is to create a new dummy feature column for each unique value in the nominal feature. To perform this transformation, we can use the OneHotEncoder from Scikit-learn:


```python
print('Shape of input before one-hot: {}'.format(X.shape))
```

    Shape of input before one-hot: (958, 22)
    

### Select categorical columns

1. Recognize non-numeric columns as categorical columns
2. Manually select some numeric columns (ex. 'Year', 'Month') as categorical columns


```python
# Recognize non-numeric columns as categorical columns
cols = X.columns
num_cols = X._get_numeric_data().columns
catego_cols = list(set(cols) - set(num_cols))

# Add other categorical columns
catego_cols.extend(['Year', 'Month', 'DayofMonth', 'DayOfWeek', 'FlightNum'])#, 'Origin', 'Dest'])

print('Categorical Columns: {}'.format(catego_cols))
```

    Categorical Columns: ['Origin', 'Dest', 'TailNum', 'UniqueCarrier', 'Year', 'Month', 'DayofMonth', 'DayOfWeek', 'FlightNum']
    

### Encode categorical columns

First, convert string to interger since The input to OneHotEncoder transformer should be a matrix of integers.


```python
# encode label first
catego_le = LabelEncoder()

for i in catego_cols:
    X[i] = catego_le.fit_transform(X[i].values)
    classes_list = catego_le.classes_.tolist()
    
X.head()
```




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
      <th>CRSElapsedTime</th>
      <th>AirTime</th>
      <th>DepDelay</th>
      <th>Origin</th>
      <th>Dest</th>
      <th>Distance</th>
      <th>TaxiIn</th>
      <th>TaxiOut</th>
      <th>Cancelled</th>
      <th>Diverted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>27</td>
      <td>4</td>
      <td>1647.0</td>
      <td>1647</td>
      <td>1906.0</td>
      <td>1859</td>
      <td>0</td>
      <td>3</td>
      <td>...</td>
      <td>252.0</td>
      <td>233.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>1587</td>
      <td>15</td>
      <td>11</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>0</td>
      <td>28</td>
      <td>5</td>
      <td>1648.0</td>
      <td>1647</td>
      <td>1939.0</td>
      <td>1859</td>
      <td>0</td>
      <td>3</td>
      <td>...</td>
      <td>252.0</td>
      <td>239.0</td>
      <td>1.0</td>
      <td>0</td>
      <td>0</td>
      <td>1587</td>
      <td>5</td>
      <td>47</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>0</td>
      <td>30</td>
      <td>0</td>
      <td>1645.0</td>
      <td>1647</td>
      <td>1852.0</td>
      <td>1859</td>
      <td>0</td>
      <td>3</td>
      <td>...</td>
      <td>252.0</td>
      <td>226.0</td>
      <td>-2.0</td>
      <td>0</td>
      <td>0</td>
      <td>1587</td>
      <td>7</td>
      <td>14</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>5</td>
      <td>842.0</td>
      <td>846</td>
      <td>1057.0</td>
      <td>1101</td>
      <td>0</td>
      <td>13</td>
      <td>...</td>
      <td>255.0</td>
      <td>244.0</td>
      <td>-4.0</td>
      <td>0</td>
      <td>0</td>
      <td>1587</td>
      <td>3</td>
      <td>8</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>6</td>
      <td>849.0</td>
      <td>846</td>
      <td>1148.0</td>
      <td>1101</td>
      <td>0</td>
      <td>13</td>
      <td>...</td>
      <td>255.0</td>
      <td>267.0</td>
      <td>3.0</td>
      <td>0</td>
      <td>0</td>
      <td>1587</td>
      <td>8</td>
      <td>24</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 22 columns</p>
</div>



Then we can convert categorical columns using OneHotEncoder.


```python
# find the index of the categorical feature
catego_cols_idx = []
for str in catego_cols:
    catego_cols_idx.append(X.columns.tolist().index(str))

# give the column index you want to do one-hot encoding
ohe = OneHotEncoder(categorical_features = catego_cols_idx)

# fit one-hot encoder
onehot_data = ohe.fit_transform(X.values).toarray()
print('Shape of input after one-hot: {}'.format(onehot_data.shape))
```

    Shape of input after one-hot: (958, 449)
    


```python
data = pd.DataFrame(onehot_data, index=X.index)
data.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>439</th>
      <th>440</th>
      <th>441</th>
      <th>442</th>
      <th>443</th>
      <th>444</th>
      <th>445</th>
      <th>446</th>
      <th>447</th>
      <th>448</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1859.0</td>
      <td>259.0</td>
      <td>252.0</td>
      <td>233.0</td>
      <td>0.0</td>
      <td>1587.0</td>
      <td>15.0</td>
      <td>11.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1859.0</td>
      <td>291.0</td>
      <td>252.0</td>
      <td>239.0</td>
      <td>1.0</td>
      <td>1587.0</td>
      <td>5.0</td>
      <td>47.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1859.0</td>
      <td>247.0</td>
      <td>252.0</td>
      <td>226.0</td>
      <td>-2.0</td>
      <td>1587.0</td>
      <td>7.0</td>
      <td>14.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1101.0</td>
      <td>255.0</td>
      <td>255.0</td>
      <td>244.0</td>
      <td>-4.0</td>
      <td>1587.0</td>
      <td>3.0</td>
      <td>8.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1101.0</td>
      <td>299.0</td>
      <td>255.0</td>
      <td>267.0</td>
      <td>3.0</td>
      <td>1587.0</td>
      <td>8.0</td>
      <td>24.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 449 columns</p>
</div>



## Append Target Class Back to Dataset

Note that the **target** class should be at the **last** column.


```python
data['ArrDelay'] = y
data.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>440</th>
      <th>441</th>
      <th>442</th>
      <th>443</th>
      <th>444</th>
      <th>445</th>
      <th>446</th>
      <th>447</th>
      <th>448</th>
      <th>ArrDelay</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>259.0</td>
      <td>252.0</td>
      <td>233.0</td>
      <td>0.0</td>
      <td>1587.0</td>
      <td>15.0</td>
      <td>11.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>291.0</td>
      <td>252.0</td>
      <td>239.0</td>
      <td>1.0</td>
      <td>1587.0</td>
      <td>5.0</td>
      <td>47.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>247.0</td>
      <td>252.0</td>
      <td>226.0</td>
      <td>-2.0</td>
      <td>1587.0</td>
      <td>7.0</td>
      <td>14.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>255.0</td>
      <td>255.0</td>
      <td>244.0</td>
      <td>-4.0</td>
      <td>1587.0</td>
      <td>3.0</td>
      <td>8.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>299.0</td>
      <td>255.0</td>
      <td>267.0</td>
      <td>3.0</td>
      <td>1587.0</td>
      <td>8.0</td>
      <td>24.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 450 columns</p>
</div>



## Export Preprocessed Data

Note that we set **headre=False** to avoid mapreduce function mistaken header as a data row.


```python
data.to_csv('logistic_input', header=False)
```
