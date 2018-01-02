---
title: 'Data preprocessing and exploring the New York city bike dataset'
layout: post
tags:
  - Data Mining
  - Python
category: Programming
mathjax: true
---

In this report, I will need   to   do   some   data   preprocessing and   then   get   some   basic information   about   the   dataset, [New   York   Citi   Bike   Trip   Histories](https://www.citibikenyc.com/system-data),    via   tools.

<!--more-->

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.basemap import Basemap
import json
import zipfile
import urllib.request
import itertools
from sklearn import metrics
from geopy.distance import vincenty
from sklearn import linear_model
import warnings
warnings.filterwarnings('ignore')
%matplotlib inline
```

## Dataset

Here we'll   use   [`201707-citibike-tripdata.csv.zip`](https://s3.amazonaws.com/tripdata/201707-citibike-tripdata.csv.zip)   only.

### Schema

The data includes:
- Trip Duration (seconds)
- Start Time and Date
- Stop Time and Date
- Start Station Name
- End Station Name
- Station ID
- Station Lat/Long
- Bike ID
- User Type (`Customer` = 24-hour pass or 3-day pass user; `Subscriber` = Annual Member)
- Gender (`Zero`=unknown; `1`=male; `2`=female)
- Year of Birth

This data has been processed to remove trips that are taken by staff as they service and inspect the system, trips that are taken to/from any of our “test” stations (which we were using more in June and July 2013), and any trips that were below 60 seconds in length (potentially false starts or users trying to re-dock a bike to ensure it's secure).


```python
urllib.request.urlretrieve("https://s3.amazonaws.com/tripdata/201707-citibike-tripdata.csv.zip", "data.zip")

with zipfile.ZipFile("data.zip","r") as zip_ref:
    zip_ref.extractall("./data")
```


```python
from os import walk

for (dirpath, dirnames, filenames) in walk("./data"):
    print(filenames)
```

    ['201707-citibike-tripdata.csv']



```python
df = pd.read_csv("./data/201707-citibike-tripdata.csv")
df.head()
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
      <th>tripduration</th>
      <th>starttime</th>
      <th>stoptime</th>
      <th>start station id</th>
      <th>start station name</th>
      <th>start station latitude</th>
      <th>start station longitude</th>
      <th>end station id</th>
      <th>end station name</th>
      <th>end station latitude</th>
      <th>end station longitude</th>
      <th>bikeid</th>
      <th>usertype</th>
      <th>birth year</th>
      <th>gender</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>364</td>
      <td>2017-07-01 00:00:00</td>
      <td>2017-07-01 00:06:05</td>
      <td>539</td>
      <td>Metropolitan Ave &amp; Bedford Ave</td>
      <td>40.715348</td>
      <td>-73.960241</td>
      <td>3107</td>
      <td>Bedford Ave &amp; Nassau Ave</td>
      <td>40.723117</td>
      <td>-73.952123</td>
      <td>14744</td>
      <td>Subscriber</td>
      <td>1986.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2142</td>
      <td>2017-07-01 00:00:03</td>
      <td>2017-07-01 00:35:46</td>
      <td>293</td>
      <td>Lafayette St &amp; E 8 St</td>
      <td>40.730207</td>
      <td>-73.991026</td>
      <td>3425</td>
      <td>2 Ave  &amp; E 104 St</td>
      <td>40.789210</td>
      <td>-73.943708</td>
      <td>19587</td>
      <td>Subscriber</td>
      <td>1981.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>328</td>
      <td>2017-07-01 00:00:08</td>
      <td>2017-07-01 00:05:37</td>
      <td>3242</td>
      <td>Schermerhorn St &amp; Court St</td>
      <td>40.691029</td>
      <td>-73.991834</td>
      <td>3397</td>
      <td>Court St &amp; Nelson St</td>
      <td>40.676395</td>
      <td>-73.998699</td>
      <td>27937</td>
      <td>Subscriber</td>
      <td>1984.0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2530</td>
      <td>2017-07-01 00:00:11</td>
      <td>2017-07-01 00:42:22</td>
      <td>2002</td>
      <td>Wythe Ave &amp; Metropolitan Ave</td>
      <td>40.716887</td>
      <td>-73.963198</td>
      <td>398</td>
      <td>Atlantic Ave &amp; Furman St</td>
      <td>40.691652</td>
      <td>-73.999979</td>
      <td>26066</td>
      <td>Subscriber</td>
      <td>1985.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2534</td>
      <td>2017-07-01 00:00:15</td>
      <td>2017-07-01 00:42:29</td>
      <td>2002</td>
      <td>Wythe Ave &amp; Metropolitan Ave</td>
      <td>40.716887</td>
      <td>-73.963198</td>
      <td>398</td>
      <td>Atlantic Ave &amp; Furman St</td>
      <td>40.691652</td>
      <td>-73.999979</td>
      <td>29408</td>
      <td>Subscriber</td>
      <td>1982.0</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1735599 entries, 0 to 1735598
    Data columns (total 15 columns):
    tripduration               int64
    starttime                  object
    stoptime                   object
    start station id           int64
    start station name         object
    start station latitude     float64
    start station longitude    float64
    end station id             int64
    end station name           object
    end station latitude       float64
    end station longitude      float64
    bikeid                     int64
    usertype                   object
    birth year                 float64
    gender                     int64
    dtypes: float64(5), int64(5), object(5)
    memory usage: 198.6+ MB


## Preprocess

### Missing Values & Anomaly Detection

There might be some noise in the dataset, like strange stations or null values.   Please detect it and take   proper actions to them.



```python
df.shape
```




    (1735599, 15)




```python
df.isnull().sum()
```




    tripduration                    0
    starttime                       0
    stoptime                        0
    start station id                0
    start station name              0
    start station latitude          0
    start station longitude         0
    end station id                  0
    end station name                0
    end station latitude            0
    end station longitude           0
    bikeid                          0
    usertype                        0
    birth year                 228596
    gender                          0
    dtype: int64



The data is clean that we can simply drop the column `"birth year"` since we are not going to use this feature anyway.

Here I ues the [**`dropna`**](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.dropna.html) function to drop _any_ columns with _any_ missing values.


```python
df = df.dropna(axis=1, how='any')
df.shape
```




    (1735599, 14)



Next, to detect and eliminate strange values, we plot all the histograms on numeric values.


```python
fig, axes = plt.subplots(nrows=9, ncols=2, figsize=(15,15))

i = 1
for col in df.columns:
    if df[col].dtype == np.float64 or df[col].dtype == np.int64:
        ax = plt.subplot(9, 2, i)
        df[col].hist(bins=30)
        ax.set_title(col)
        i += 1
        ax = plt.subplot(9, 2, i)
        df[col].hist(bins=30)
        ax.set_title(col+" (log scale)")
        ax.set_yscale('log')
        i += 1

fig.tight_layout()
plt.show()
```


![](https://i.imgur.com/qyS8osn.png)


There are some values that are strange:

1. The maximum value of trip duration is `2500000 sec(s)`, which is about `28` days.   This is very uncommon for a person to rent a citibike for such a long time.
2. There is a station that is so far away from other stations. (See the rightmost balue of end station latitude/longitude.)

Here I would deal with the first problem by removing any records whose `trip duration` is over `20` days.


```python
df = df[df['trip duration'] <= 24*60*60*20]
```

Now we check if all stations have one-to-one matching among their `id`, `name`, `latitude`, and `longitude`.

- `station id` <---> `station name`


```python
x1 = len(df['start station id'].unique())
y1 = len(df[['start station id', 'start station name']].drop_duplicates())
x2 = len(df['end station id'].unique())
y2 = len(df[['end station id', 'end station name']].drop_duplicates())

x1 == y1 and x2 == y2
```




    True



- `station id` <---> `station latitude`


```python
x1 = len(df['start station id'].unique())
y2 = len(df[['start station id', 'start station latitude']].drop_duplicates())
x2 = len(df['end station id'].unique())
y2 = len(df[['end station id', 'end station latitude']].drop_duplicates())

x1 == y1 and x2 == y2
```




    True



- `station id` <---> `station longitude`


```python
x1 = len(df['start station id'].unique())
y2 = len(df[['start station id', 'start station longitude']].drop_duplicates())
x2 = len(df['end station id'].unique())
y2 = len(df[['end station id', 'end station longitude']].drop_duplicates())

x1 == y1 and x2 == y2
```




    True



We can also draw all stations on map to see if there is any strange location

Here I use the [**`Basemap`**](https://matplotlib.org/basemap/api/basemap_api.html) package of `matplotlib` to draw the map. (See examples [here](https://matplotlib.org/basemap/users/geography.html))


```python
t1 = df[['start station id', 'start station name', 'start station latitude', 'start station longitude']] \
            .drop_duplicates().rename(columns = {'start station id':'station id', \
                                                 'start station name':'station name', \
                                                 'start station latitude':'station latitude', 
                                                 'start station longitude': 'station longitude'})
t2 = df[['end station id', 'end station name', 'end station latitude', 'end station longitude']] \
        .drop_duplicates().rename(columns = {'end station id':'station id', \
                                             'end station name':'station name', \
                                             'end station latitude':'station latitude', \
                                             'end station longitude': 'station longitude'})
df_loc = pd.concat([t1, t2]).drop_duplicates()
```


```python
# Initialize plots
fig, ax = plt.subplots(figsize=(15,15))

# determine range to print based on min, max lat and lon of the data
lat = list(df_loc['station latitude'])
lon = list(df_loc['station longitude'])
text = list(df_loc['station id'])
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

# convert lat and lon to map projection coordinates
lons, lats = m(lon, lat)

# plot points as red dots
ax.scatter(lons, lats, marker = 'o', color='r', zorder=5, alpha=0.6)
for i in range(df_loc.shape[0]):
    plt.text(lons[i], lats[i], text[i])
plt.show()
```


![](https://i.imgur.com/PxJ76Iy.png)

The station "`3254`" and "`3182`" seems weird, however, these 2 stations are actually on an island named "Governors Island".

And the station "`3036`", which is at the upper-right corner of the map, **does not exist** actually, whereas station "`3201`" and "`3192`" are confirmed to exist.

(The way that I confirm the above information is to check the [station list on the official website](http://www.citibikenyc.com/stations/json) & [Googe Map](https://www.google.com.tw/maps/search/Citi+Bike/@40.7318011,-74.0213749,13z/data=!3m1!4b1).)

So we need to **remove** data related to this station.


```python
df = df[df['start station id']!=3036]
df = df[df['end station id']!=3036]
df_loc = df_loc[df_loc['station id']!=3036]
```


```python
df_loc.to_csv("data/station_information.csv", index=None)
```

So now I want to check if all station ids mentioned in this dataframe exists.

### Create Self-defined Features

For future use, we need to calculate in-flow and out-flow for each stations every half hour.   The result data set should contains `station_id`, `time`, `in_flow_count`, `out_flow_count`.

> **in/out flow of a station** are define as the number of trips move to/from the station within the 30 minutes period.
> So one day can be splitted into 48 segments.

To split one day into 48 segemnts, first we need to transform column `"starttime"` and `"stoptime"` into `datetime` format.
(Use the [`to_datetime`](http://pandas.pydata.org/pandas-docs/version/0.20/generated/pandas.to_datetime.html) function, specifying a [format](http://strftime.org/) to match the data.)


```python
# format example: 2017-07-01 00:00:00
df['starttime'] = pd.to_datetime(df['starttime'], format='%Y-%m-%d %H:%M:%S')
df['stoptime'] =pd.to_datetime(df['stoptime'], format='%Y-%m-%d %H:%M:%S')
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 1735598 entries, 0 to 1735598
    Data columns (total 14 columns):
    tripduration               int64
    starttime                  datetime64[ns]
    stoptime                   datetime64[ns]
    start station id           int64
    start station name         object
    start station latitude     float64
    start station longitude    float64
    end station id             int64
    end station name           object
    end station latitude       float64
    end station longitude      float64
    bikeid                     int64
    usertype                   object
    gender                     int64
    dtypes: datetime64[ns](2), float64(4), int64(5), object(3)
    memory usage: 198.6+ MB


Now we need to **annotate** the data according to time segment: (Use [`DatetimeIndex`](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DatetimeIndex.html) to retrieve date&time information)


```python
def gen_time_segment(dt):
    if dt.minute < 30:
        minute = "%02d" % 0
    else:
        minute = "%02d" % 30
    return "{}-{}-{} {}:{}".format(dt.year, dt.month, dt.day, dt.hour, minute)

df['start_seg'] = [gen_time_segment(dt) for dt in df['starttime']]
df['stop_seg'] = [gen_time_segment(dt) for dt in df['stoptime']]

df[['start station id', 'starttime', 'start_seg', 'end station id', 'stoptime', 'stop_seg']].head()
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
      <th>start station id</th>
      <th>starttime</th>
      <th>start_seg</th>
      <th>end station id</th>
      <th>stoptime</th>
      <th>stop_seg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>539</td>
      <td>2017-07-01 00:00:00</td>
      <td>2017-7-1 0:00</td>
      <td>3107</td>
      <td>2017-07-01 00:06:05</td>
      <td>2017-7-1 0:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>293</td>
      <td>2017-07-01 00:00:03</td>
      <td>2017-7-1 0:00</td>
      <td>3425</td>
      <td>2017-07-01 00:35:46</td>
      <td>2017-7-1 0:30</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3242</td>
      <td>2017-07-01 00:00:08</td>
      <td>2017-7-1 0:00</td>
      <td>3397</td>
      <td>2017-07-01 00:05:37</td>
      <td>2017-7-1 0:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2002</td>
      <td>2017-07-01 00:00:11</td>
      <td>2017-7-1 0:00</td>
      <td>398</td>
      <td>2017-07-01 00:42:22</td>
      <td>2017-7-1 0:30</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2002</td>
      <td>2017-07-01 00:00:15</td>
      <td>2017-7-1 0:00</td>
      <td>398</td>
      <td>2017-07-01 00:42:29</td>
      <td>2017-7-1 0:30</td>
    </tr>
  </tbody>
</table>
</div>



Then **group** and **count** the data according to time annotations:

- `in-flow`


```python
inflow = df[['end station id', 'stop_seg']] \
            .groupby(['end station id', 'stop_seg']) \
            .size().reset_index(name='counts') \
            .rename(columns={'end station id':'station id','stop_seg':'time', 'counts':'in_flow_count'})
```

- `out-flow`


```python
outflow = df[['start station id', 'start_seg']] \
            .groupby(['start station id', 'start_seg']) \
            .size().reset_index(name='counts') \
            .rename(columns={'start station id':'station id','start_seg':'time', 'counts':'out_flow_count'})
```

In the end, **merge** the `inflow` and `outflow` dataframes, considering **every time segment at every station**.


```python
station_id_list = list(df_loc['station id'])

# Create combinations of time series and station ids
time_seg_list = list(pd.date_range("2017-07-01 00:00:00", "2017-07-31 23:30:00", freq="30min"))
template = pd.DataFrame(list(itertools.product(station_id_list, time_seg_list)), \
                        columns=["station id", "time"])

# Merge in/out flow information & Add zeros to missing data according to every time segment
dat = pd.merge(inflow, outflow, on=['station id', 'time'], how='outer')
dat['time'] = pd.to_datetime(dat['time'], format='%Y-%m-%d %H:%M')
dat = dat.merge(template, on=["station id", "time"], how="right").fillna(0)
dat.head()
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




```python
dat.to_csv("data/station_flow.csv", index=None)
```

## Query
### How  many  stations  are  there  in  this  dataset,and  what  is  the  average distance   between   them?

$$
average~distance = \frac {\sum_{i!=j} dist(S_i,S_j)}{\frac{N(N-1)}{2}}
$$


```python
print("{} stations are found in this dataset.".format(len(station_id_list)))
```

    633 stations are found in this dataset.


To calculate the distance between stations, I use the function [**`vincenty`**](https://geopy.readthedocs.io/en/1.10.0/#module-geopy.distance) in package `GeoPy`.


```python
# Create dictionaries for station latitude/longitude
lat_dic = {}
lon_dic = {}
for index, row in df_loc.iterrows():
    lat_dic[row['station id']] = row['station latitude']
    lon_dic[row['station id']] = row['station longitude']

# Generate combinations of pairs of station
c = itertools.combinations(station_id_list, 2)

# Calculate the averge distance of pairs of stations
dist = 0
count = 0
for stn1, stn2 in c:
        dist += vincenty((lat_dic[stn1], lon_dic[stn1]), (lat_dic[stn2], lon_dic[stn2])).meters
        count += 1
print("The average distance between different stations is {} (meters)".format(dist/count))
```

    The average distance between different stations is 5393.467456938468 (meters)


### What  are  the  top  3  frequent  stations  pairs `(start station, end station)`  in weekdays, how   about   in   weekends?

$$
(S_i,S_j)~is~not~(S_j,S_i)
$$


```python
# Split the dataframe into weekdays information & weekends information
df_weekdays = df[df['starttime'].dt.dayofweek < 5]
df_weekends = df[df['starttime'].dt.dayofweek >= 5]

# Count and sort station pair frequencies
stn_pair_weekdays = df_weekdays[['start station id', 'end station id']] \
                    .groupby(['start station id', 'end station id']) \
                    .size().reset_index(name='counts') \
                    .set_index(['start station id', 'end station id']) \
                    .sort_values(by='counts', ascending=False)
stn_pair_weekends = df_weekends[['start station id', 'end station id']] \
                    .groupby(['start station id', 'end station id']) \
                    .size().reset_index(name='counts') \
                    .set_index(['start station id', 'end station id']) \
                    .sort_values(by='counts', ascending=False)
            
# Find the top 3 station pairs for weekday & weekend
top_weekday_pair = list(stn_pair_weekdays.head(3).index)
top_weekend_pair = list(stn_pair_weekends.head(3).index)

# Print out the result
print("The top 3 frequent stations pairs in weekdays are: {}, {}, and {}.".format(*top_weekday_pair))
print("The top 3 frequent stations pairs in weekends are: {}, {}, and {}.".format(*top_weekend_pair))
```

    The top 3 frequent stations pairs in weekdays are: (432, 3263), (2006, 2006), and (281, 281).
    The top 3 frequent stations pairs in weekends are: (3182, 3182), (3182, 3254), and (3254, 3182).


### Find  the  top  3  stations  with  highest  average  `out-flow`, and  top  3  highest average   `in-flow`



```python
# Sort the average in/out flow count of each station
average_inflow = dat[['station id', 'in_flow_count']] \
                .groupby(['station id']) \
                .mean() \
                .sort_values(by='in_flow_count', ascending=False)
average_outflow = dat[['station id', 'out_flow_count']] \
                .groupby(['station id']) \
                .mean() \
                .sort_values(by='out_flow_count', ascending=False)
            
# List the top 3 stations
top_inflow = list(average_inflow.head(3).index)
top_outflow = list(average_outflow.head(3).index)

# Print out the result
print("The top 3 stations with highest outflow are: {}, {}, and {}".format(*top_outflow))
print("The top 3 stations with highest inflow are: {}, {}, and {}".format(*top_inflow))
```

    The top 3 stations with highest outflow are: 519, 426, and 514
    The top 3 stations with highest inflow are: 426, 519, and 514


### What  is  the  most  popular  station(highest  average  inflow+outflow)?


```python
# Sum up in/out flow at each time station
dat['flow_count'] = dat['in_flow_count'] + dat['out_flow_count']

# Calculate and sort the average flow count for each station
average_flow = dat[['station id', 'flow_count']] \
                .groupby(['station id']) \
                .mean() \
                .sort_values(by='flow_count', ascending=False)
            
# Find the top 1 station
top_flow = list(average_inflow.head(1).index)

# Print out the result
print("The most popular station is: {}".format(*top_outflow))
```

    The most popular station is: 519


#### a. Draw  the  in-flow($A$)  and  out-flow($B$)  for  that  station  in  a  line  chart

Here I use the [**`plot`**](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.plot.html) function of `pandas`.


```python
# Select station & add information in missing time
small_df = dat[dat['station id'] == 519].sort_values(by='time')
small_df = small_df.sort_values(by='time')

# Plot line chart
small_df.plot(x='time', y=['in_flow_count', 'out_flow_count'], kind='line', figsize=(15,15))
plt.show()
```


![](https://i.imgur.com/RRbXGzU.png)

#### b. Calculate  the  distance  function  between   $A$   and   $B$

Here I use [**`pairwise_distance`**](http://scikit-learn.org/stable/modules/generated/sklearn.metrics.pairwise.pairwise_distances.html) of `scikit-learn`, with metrics as  "euclidean distance".


```python
dist = metrics.pairwise_distances([small_df['in_flow_count']], [small_df['out_flow_count']], metric='euclidean')
print("The euclidean distance between in-flow and out-flow of this station is: {}".format(dist[0][0]))
```

    The euclidean distance between in-flow and out-flow of this station is: 178.5553135585721


#### c. Calculate   the   distance   function   between    $A-mean(A)$    and $B-mean(B)$ , and   draw   them   both.


```python
# Substract the mean
small_df['in_flow_diff'] = small_df['in_flow_count'] - small_df['in_flow_count'].mean()
small_df['out_flow_diff'] = small_df['out_flow_count'] - small_df['out_flow_count'].mean()

# Plot line chart
small_df.plot(x='time', y=['in_flow_diff', 'out_flow_diff'], kind='line', figsize=(15,15))
plt.show()
```


![](https://i.imgur.com/ix4ROU5.png)

Now calculate the euclidean distance between the in-flow and the out-flow again.


```python
dist = metrics.pairwise_distances([small_df['in_flow_diff']], [small_df['out_flow_diff']], metric='euclidean')
print("The euclidean distance between in-flow and out-flow of this station is: {}".format(dist[0][0]))
```

    The euclidean distance between in-flow and out-flow of this station is: 178.27760158193377


#### d. Calculate  the  distance  function  between   $(A-mean(A))/std(A)$ and    $(B-mean(B))/std(B)$ , and   draw   them   both.


```python
# Calculate variance
small_df['in_flow_variance'] = small_df['in_flow_diff'] / small_df['in_flow_count'].std()
small_df['out_flow_variance'] = small_df['out_flow_diff'] / small_df['out_flow_count'].std()

# Plot line chart
small_df.plot(x='time', y=['in_flow_variance', 'out_flow_variance'], kind='line', figsize=(15,15))
plt.show()
```


![](https://i.imgur.com/caYFbvl.png)

Now calculate the euclidean distance between the in-flow and the out-flow again.


```python
dist = metrics.pairwise_distances([small_df['in_flow_variance']], [small_df['out_flow_variance']], metric='euclidean')
print("The euclidean distance between in-flow and out-flow of this station is: {}".format(dist[0][0]))
```

    The euclidean distance between in-flow and out-flow of this station is: 12.27694023559983


#### e. Calculate  the  distance  function  between  $\{A_i−f(i)|A_i \in A\}$  and $\{B_i −f(i)|B_i \in B\}$,and  draw  them  both.

$$
f~is~the~linear~function~that~minimize~\sum(A_i−f(i))^2
$$


First, we need to **fit** the linear regression models $f(i)$ for both $A$ and $B$, using `Linear Regression` in [Generalized Linear Models](http://scikit-learn.org/stable/modules/linear_model.html#ordinary-least-squares).


```python
# Prepare input for linear regression model
small_df.sort_values(by='time', ascending=True)
length = small_df.shape[0]
time = np.arange(length).reshape(length, 1)

# Create and fit the models
reg_A = linear_model.LinearRegression()
reg_A.fit(time, list(small_df['in_flow_count']))
reg_B = linear_model.LinearRegression()
reg_B.fit(time, list(small_df['out_flow_count']))

# Save the prediction results
small_df['in_flow_linear'] = y_pred_A = reg_A.predict(time)
small_df['out_flow_linear'] = y_pred_B = reg_B.predict(time)

# Plot the fit reuslt
fig, axes = plt.subplots(nrows=1, ncols=2, figsize=(15,10))
ax = plt.subplot(1, 2, 1)
plt.scatter(time, small_df['in_flow_count'].values.reshape(length, 1), color='black', alpha=0.1)
plt.plot(time, y_pred_A, color='blue', linewidth=3)
ax = plt.subplot(1, 2, 2)
plt.scatter(time, small_df['out_flow_count'].values.reshape(length, 1), color='black', alpha=0.1)
plt.plot(time, y_pred_B, color='blue', linewidth=3)
```






![](https://i.imgur.com/wOjdyQz.png)


Then we can calculate  $\{A_i−f(i)|A_i \in A\}$  and
$\{B_i −f(i)|B_i \in B\}$, and  draw  them  both.


```python
# Calculate distance to the line drawn by linear model
small_df['in_flow_ols'] = small_df['in_flow_count'] - small_df['in_flow_linear']
small_df['out_flow_ols'] = small_df['out_flow_count'] - small_df['out_flow_linear']

# Plot line chart
small_df.plot(x='time', y=['in_flow_ols', 'out_flow_ols'], kind='line', figsize=(15,15))
plt.show()
```


![](https://i.imgur.com/jFoai85.png)

Now calculate the euclidean distance between the in-flow and the out-flow again.


```python
dist = metrics.pairwise_distances([small_df['in_flow_ols']], [small_df['out_flow_ols']], metric='euclidean')
print("The euclidean distance between in-flow and out-flow of this station is: {}".format(dist[0][0]))
```

    The euclidean distance between in-flow and out-flow of this station is: 178.27208810218377


#### f. Calculate   the   distance   function   between    $Smooth(A)$    and $Smooth(B)$ ,and   draw   them   both.

You   can   choose   any   smoothing   function,just   specify   it, or   simply   use 

$$
A_i = \frac{A_{i−1}+A_i+A_{i+1}}{3}
$$

(or   take   the   average   of   5,7,9...   elements)


Here I use the function [**`rolling`**](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.rolling.html) in `pandas` to take the **average mean**.

- **`window = 9`**, as I take the average of 9 elements.
- **`min_periods = 1`** to avoid `NA` values at the head/tail of the time series.
- **`center = True`** to make it match correctly to the time.


```python
# Calculate the moving averages
small_df['in_flow_smooth'] = small_df['in_flow_count'].rolling(window=9, min_periods=1, center=True).mean()
small_df['out_flow_smooth'] = small_df['out_flow_count'].rolling(window=9, min_periods=1, center=True).mean()

# Plot line chart
small_df.plot(x='time', y=['in_flow_smooth', 'out_flow_smooth'], kind='line', figsize=(15,15))
plt.show()
```


![](https://i.imgur.com/gmqF2Vo.png)


Now calculate the euclidean distance between the in-flow and the out-flow again.


```python
dist = metrics.pairwise_distances([small_df['in_flow_smooth']], [small_df['out_flow_smooth']], metric='euclidean')
print("The euclidean distance between in-flow and out-flow of this station is: {}".format(dist[0][0]))
```

    The euclidean distance between in-flow and out-flow of this station is: 63.427226939887035


### 5. Visualize the flows of citibikes over time

First, annotate each record as in **Early-July**, **Mid-July**, or **Late-July**.


```python
def gen_time_group(dt):
    if dt.day <= 10:
        return "Early-July"
    elif dt.day <= 20:
        return "Mid-July"
    else:
        return "Late-July"
```


```python
# Calculate and sort the average flow count for each station
flow = dat[['station id', 'time', 'flow_count']] 

# Create time group
flow['time_group'] = [gen_time_group(dt) for dt in flow['time']]

# Summarise flow count according to time group
flow = flow.groupby(["station id", "time_group"], as_index=False) \
            .agg({'flow_count': 'sum'})

# Add latitude/logitude columns
flow['latitude'] = [lat_dic[x] for x in flow['station id']]
flow['longitude'] = [lon_dic[x] for x in flow['station id']]

flow.head()
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
      <th>time_group</th>
      <th>flow_count</th>
      <th>latitude</th>
      <th>longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>72</td>
      <td>Early-July</td>
      <td>2369.0</td>
      <td>40.767272</td>
      <td>-73.993929</td>
    </tr>
    <tr>
      <th>1</th>
      <td>72</td>
      <td>Late-July</td>
      <td>2989.0</td>
      <td>40.767272</td>
      <td>-73.993929</td>
    </tr>
    <tr>
      <th>2</th>
      <td>72</td>
      <td>Mid-July</td>
      <td>2683.0</td>
      <td>40.767272</td>
      <td>-73.993929</td>
    </tr>
    <tr>
      <th>3</th>
      <td>79</td>
      <td>Early-July</td>
      <td>1580.0</td>
      <td>40.719116</td>
      <td>-74.006667</td>
    </tr>
    <tr>
      <th>4</th>
      <td>79</td>
      <td>Late-July</td>
      <td>2282.0</td>
      <td>40.719116</td>
      <td>-74.006667</td>
    </tr>
  </tbody>
</table>
</div>



**Then plot every popular stations on map.**

Note that the size of the station point is calculated as

$$
Size = 2^{flow}
$$

$$
flow = \frac{sum~of~flow~count~in~this~time~period}{1000}
$$


```python
def plot_stations_map(ax, stns, noText=False):
    # determine range to print based on min, max lat and lon of the data
    lat = list(stns['latitude'])
    lon = list(stns['longitude'])
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

    # convert lat and lon to map projection coordinates
    lons, lats = m(lon, lat)

    # plot points as red dots
    if noText:
        ax.scatter(lons, lats, marker = 'o', color='r', zorder=5, alpha=0.6, s=1)
        return
    else:
        ax.scatter(lons, lats, marker = 'o', color='r', zorder=5, alpha=0.3, s=siz)
    
    # annotate popular stations
    for i in range(len(siz)):
        if siz[i] >= 2**6:
            plt.text(lons[i], lats[i], text[i])
```


```python
pop_flow = flow[flow['flow_count'] > 2000]
fig, axes = plt.subplots(nrows=1, ncols=4, figsize=(15,15))
ax = plt.subplot(1, 4, 1)
ax.set_title("All Stations")
plot_stations_map(ax, flow, noText=True)
ax = plt.subplot(1, 4, 2)
ax.set_title("Popular Stations in Early July")
plot_stations_map(ax, pop_flow[pop_flow['time_group'] == "Early-July"])
ax = plt.subplot(1, 4, 3)
ax.set_title("Popular Stations in Mid July")
plot_stations_map(ax, pop_flow[pop_flow['time_group'] == "Mid-July"])
ax = plt.subplot(1, 4, 4)
ax.set_title("Popular Stations in Late July")
plot_stations_map(ax, pop_flow[pop_flow['time_group'] == "Late-July"])
```


![](https://i.imgur.com/bsRhWZH.png)

Here are my observations:

1. Few people ride citibikes in Jersey City and Brooklyn, that is, **the main flow of citibike concentrates in Manhattan**.
2. The poular stations in Brooklyn are nearly the same across the whole month.
3. Although the top popular regions (locations with large red circle) look similar, **the top popular stations are not the same across the whole month**. (Check station id carefully you'll see they are not the same.)
4. For popular regions, more and more people ride citibikes in Mid July and Late July.
 
