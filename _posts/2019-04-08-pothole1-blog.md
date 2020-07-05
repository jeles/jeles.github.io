---
layout: single
title:  "Mapping Pittsburgh Potholes 1: Visualization"
type: post
comments: true
date:   2019-04-08
header:
  teaser: "assets/images/2019-04-08-pothole/2019-04-08_f2.png"

excerpt: "Simple visualization of Pittsburgh's potholes from 311 reports"
---

If you're from a place that has harsh winters, you know about the horrors of potholes. In Pittsburgh, we have a non-emergency reporting service (311) where we can call in can let the city know that there's a hole in the road. Thanks to the [Western Pennsyvlania Regional Data Center](http://www.wprdc.org/), anyone can access the 311 data and see where the potholes are! In this post, I'll just do a simple visualization to create a map of Pittsburgh entirely by its potholes. In future posts, I'll delve much more into what determines how long it takes before potholes get filled.

<br />



## Step 1: Load your libraries and data exploration


```python
#LOAD PRIMARY DATA
import os
import numpy as np
import pandas as pd
from six.moves import urllib

%matplotlib inline
import matplotlib.pyplot as plt

import seaborn as sns
color = sns.color_palette()
sns.set_style('darkgrid')

import geopandas
from shapely.geometry import *

TDat = pd.read_csv('C:/Users/james/Desktop/Computer Club/PittsburghPotholeData/data311.csv')

TDat.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>REQUEST_ID</th>
      <th>CREATED_ON</th>
      <th>REQUEST_TYPE</th>
      <th>REQUEST_ORIGIN</th>
      <th>STATUS</th>
      <th>DEPARTMENT</th>
      <th>NEIGHBORHOOD</th>
      <th>COUNCIL_DISTRICT</th>
      <th>WARD</th>
      <th>TRACT</th>
      <th>PUBLIC_WORKS_DIVISION</th>
      <th>PLI_DIVISION</th>
      <th>POLICE_ZONE</th>
      <th>FIRE_ZONE</th>
      <th>X</th>
      <th>Y</th>
      <th>GEO_ACCURACY</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>203364.0</td>
      <td>2017-12-15T14:53:00</td>
      <td>Street Obstruction/Closure</td>
      <td>Call Center</td>
      <td>1</td>
      <td>DOMI - Permits</td>
      <td>Central Northside</td>
      <td>1.0</td>
      <td>22.0</td>
      <td>4.200322e+10</td>
      <td>1.0</td>
      <td>22.0</td>
      <td>1.0</td>
      <td>1-7</td>
      <td>-80.016716</td>
      <td>40.454144</td>
      <td>EXACT</td>
    </tr>
    <tr>
      <th>1</th>
      <td>200800.0</td>
      <td>2017-11-29T09:54:00</td>
      <td>Graffiti</td>
      <td>Control Panel</td>
      <td>1</td>
      <td>Police - Zones 1-6</td>
      <td>South Side Flats</td>
      <td>3.0</td>
      <td>16.0</td>
      <td>4.200316e+10</td>
      <td>3.0</td>
      <td>16.0</td>
      <td>3.0</td>
      <td>4-24</td>
      <td>-79.969952</td>
      <td>40.429243</td>
      <td>APPROXIMATE</td>
    </tr>
    <tr>
      <th>2</th>
      <td>201310.0</td>
      <td>2017-12-01T13:23:00</td>
      <td>Litter</td>
      <td>Call Center</td>
      <td>1</td>
      <td>DPW - Street Maintenance</td>
      <td>Troy Hill</td>
      <td>1.0</td>
      <td>24.0</td>
      <td>4.200324e+10</td>
      <td>1.0</td>
      <td>24.0</td>
      <td>1.0</td>
      <td>1-2</td>
      <td>-79.985859</td>
      <td>40.459716</td>
      <td>EXACT</td>
    </tr>
    <tr>
      <th>3</th>
      <td>200171.0</td>
      <td>2017-11-22T14:54:00</td>
      <td>Water Main Break</td>
      <td>Call Center</td>
      <td>0</td>
      <td>Pittsburgh Water and Sewer Authority</td>
      <td>Banksville</td>
      <td>2.0</td>
      <td>20.0</td>
      <td>4.200320e+10</td>
      <td>5.0</td>
      <td>20.0</td>
      <td>6.0</td>
      <td>4-9</td>
      <td>-80.034210</td>
      <td>40.406969</td>
      <td>EXACT</td>
    </tr>
    <tr>
      <th>4</th>
      <td>193043.0</td>
      <td>2017-10-12T12:46:00</td>
      <td>Guide Rail</td>
      <td>Call Center</td>
      <td>1</td>
      <td>DPW - Construction Division</td>
      <td>East Hills</td>
      <td>9.0</td>
      <td>13.0</td>
      <td>4.200313e+10</td>
      <td>2.0</td>
      <td>13.0</td>
      <td>5.0</td>
      <td>3-19</td>
      <td>-79.876582</td>
      <td>40.451226</td>
      <td>EXACT</td>
    </tr>
  </tbody>
</table>
</div>



<br />

Ok, so you'll notice that we don't see "Potholes" in the header for "REQUEST_TYPE". Let's explore request types and see what are the most prevalent request:

<br />


```python
#get a barplot of the top 10 311 calls

top10 = TDat['REQUEST_TYPE'].value_counts()[0:9]
top10.index.values[4] = 'Abandoned Vehicle' #I'm cheating a bit here to fix some formatting
plt.figure(figsize=(10,5))
sns.barplot(top10.index, top10.values, alpha=0.8)
plt.title('Top 10 311 Calls in Pittsburgh')
plt.ylabel('Number of Occurrences', fontsize=12)
plt.xlabel('Call', fontsize=12)

plt.xticks(plt.xticks()[0]-.1, top10.index, rotation=45)
plt.tight_layout()
plt.show()


```


![png]({{ "/assets/images/2019-04-08-pothole/2019-04-08_f1.png" }})

<br />

Surprise, surprise! Potholes ARE the most common report by far.

<br />

## Step 2: And lastly, plot all pothole longitudes and latitudes and you'll see a clean outline of Pittsburgh:


```python
#Now maybe do a group by potholes and display

#Remove NaN and focus our longitude and latitude ranges to the pittsburgh region
subDf = TDat[['REQUEST_TYPE','X','Y']].dropna()
subDf = subDf[(subDf.X >-80.1) & (subDf.X < -79.85)]
subDf = subDf[(subDf.Y >40.36) & (subDf.Y < 40.5)]

fig = plt.figure(figsize=(7,7))
ax = fig.add_subplot(1, 1, 1) # nrows, ncols, index

ax.plot(-79.96,40.47,'ro',alpha=1) #inoccuous point so we can output the legend
ax.legend(['Pothole'],fontsize = 12) #inoccuous point so we can output legend
ax.plot(subDf.X[subDf.REQUEST_TYPE=='Potholes'].values,subDf.Y[subDf.REQUEST_TYPE=='Potholes'].values,'r.',alpha=.03)
ax.plot(-79.9959,40.4406,'ko')

ax.annotate('Pittsburgh',(-79.9959,40.4406),xytext=(-79.994,40.445),fontsize=16)
ax.set_aspect('equal')
ax.set_facecolor((1,1,1))

plt.title('A Map of Pittsburgh by Potholes', fontsize = 16)
plt.ylabel('Latitude', fontsize=12)
plt.xlabel('Longitude', fontsize=12)

```






![png]({{ "/assets/images/2019-04-08-pothole/2019-04-08_f2.png" }})


## Look at those beautiful 3 rivers! Like I said, this is just a first stop on this exploration, but take a minute to appreciate how Pittsburgh is both beautiful and a terrifying place to drive.
