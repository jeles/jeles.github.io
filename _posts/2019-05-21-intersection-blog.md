---
layout: single
title:  "Intersection Safety Prediction Model"
type: post
comments: true
date:   2019-05-21
header:
  teaser: "assets/images/2019-05-21_f2.png"

excerpt: "Model to predict car accident risk of Pittsburgh Intersections"
---

## Preliminary model exploration with Pittsburgh city data

Despite innovations in automotive safety features and traffic control systems, automotive collisions remain one of the most costly and dangerous problems in the United States. Intersections are particularly dangerous and are where over 40% of traffic collisions occur. In my project, **Intersection Safety Prediction and Recommendation System (ISPRS)**, I propose using city data on traffic collisions, intersection safety features, and intersection geometry to predict the number of collisions that will occur at all intersections of a city, determine which features contribute the most to each intersection’s safety rating, and generate recommendations to improve safety at intersections. **ISPRS could be productized as a municipal decision assistance tool or a tool to help insurance companies set premiums.**

For early validation of this model, I have decided to use data from my home city of Pittsburgh, Pennsylvania. The [Western Pennsylvania Regional Data Center](https://data.wprdc.org/dataset/allegheny-county-crash-data) has published information about accidents that have occurred within the city from 2004-2017. From these data, we can see that in Pittsburgh, roughly 43% of accidents occur at traffic intersections, and that the number of accidents occurring at intersections has gone up over that period:

![png]({{ "/assets/images/2019-05-21_f1.png" }})


<br />

Using this data as well as published descriptors of the intersections (totaling **303MB of data processed**), I have created a preliminary Ridge Regression model to predict the number of accidents that have occurred at each intersection in Pittsburgh from 2004-2017. To generate the model, I first used geospatial data of street centerlines to define all intersections in Pittsburgh. This is shown below, with each intersection also showing how many accidents have occurred there from 2004-2017:

![png]({{ "/assets/images/2019-05-21_f2.png" }})

### Import Libraries and Engineering Features
<br />
In the next portion, I engineered features from data describing Pittsburgh intersection. The features that were ultimately selected are listed below:


```python
import os
import numpy as np
import pandas as pd
from six.moves import urllib
from math import sin, cos, asin, sqrt, degrees, radians

%matplotlib inline
import matplotlib.pyplot as plt

import seaborn as sns
color = sns.color_palette()
sns.set_style('darkgrid')

import sklearn
from sklearn.pipeline import Pipeline
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import RidgeCV
from sklearn.metrics import mean_squared_error

allmod = pd.read_csv('C:/Users/james/Desktop/JamesFiles2/prelim_int_data.csv')
allmod.set_index('IntCode',inplace=True)
newdf = allmod.iloc[:,12:23]
newdf['numacc']=allmod.numacc
newdf.head()
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
      <th>numones</th>
      <th>meanspeed</th>
      <th>maxdiffspeed</th>
      <th>numroad</th>
      <th>uniroad</th>
      <th>ZipCode</th>
      <th>StopSign</th>
      <th>StopPerWay</th>
      <th>TrafficLight</th>
      <th>TrafficFlash</th>
      <th>numRelSigns</th>
      <th>numacc</th>
    </tr>
    <tr>
      <th>IntCode</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>25.000000</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>2.0</td>
      <td>['15222']</td>
      <td>0</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>28.333333</td>
      <td>10.0</td>
      <td>6.0</td>
      <td>2.0</td>
      <td>['15222']</td>
      <td>0</td>
      <td>NaN</td>
      <td>1</td>
      <td>0.0</td>
      <td>5</td>
      <td>21</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>30.000000</td>
      <td>10.0</td>
      <td>4.0</td>
      <td>2.0</td>
      <td>['15222']</td>
      <td>0</td>
      <td>NaN</td>
      <td>1</td>
      <td>0.0</td>
      <td>3</td>
      <td>9</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>25.000000</td>
      <td>0.0</td>
      <td>5.0</td>
      <td>2.0</td>
      <td>['15222']</td>
      <td>1</td>
      <td>2.5</td>
      <td>0</td>
      <td>NaN</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0</td>
      <td>30.000000</td>
      <td>10.0</td>
      <td>4.0</td>
      <td>2.0</td>
      <td>['15203']</td>
      <td>0</td>
      <td>NaN</td>
      <td>1</td>
      <td>1.0</td>
      <td>3</td>
      <td>13</td>
    </tr>
  </tbody>
</table>
</div>



<br />
The Pearson's correlation coefficient matrix of these features shows that many of the features--especially presence of a traffic light at the intersection and the number of signs at the intersection--are correlated with the total number of accidents (numacc):

**Pearson's Correlation Coefficient with Number of Accidents at each Intersection:**
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
      <th>Variables</th>
      <th>Rsq</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>7</th>
      <td>Traffic Lights</td>
      <td>0.523356</td>
    </tr>
    <tr>
      <th>9</th>
      <td>No. Relevant Signs</td>
      <td>0.302261</td>
    </tr>
    <tr>
      <th>3</th>
      <td>No. of Roads at Int.</td>
      <td>0.266065</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Mean Speed of Roads</td>
      <td>0.244793</td>
    </tr>
    <tr>
      <th>4</th>
      <td>No. Unique Roads at Int.</td>
      <td>0.219210</td>
    </tr>
    <tr>
      <th>0</th>
      <td>No. of One Ways</td>
      <td>0.211491</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Largest Speed Differential</td>
      <td>0.156348</td>
    </tr>
    <tr>
      <th>6</th>
      <td>No. Stop Signs per Way</td>
      <td>0.015820</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Stop Signs</td>
      <td>-0.038679</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Flashing Traffic Light</td>
      <td>-0.144408</td>
    </tr>
  </tbody>
</table>
</div>


<br />
In addition to these features, some of the categorical features that I have extracted seem to be predictive of the number of collisions. For instance, Zip Code seems to capture some variance in the data, as shown by this map:
![png]({{ "/assets/images/2019-05-21_f4.png" }})

<br />

### Data Cleaning
<br />
Having shown some preliminary validation of our parameter selection, I will move forward with the model. The first step is to clean the data by filling NaNs, label encoding categorical columns, creating a test set, and scaling the data:



```python
#Clean up the data

#Fill NaN values
newdf.StopPerWay.fillna(0, inplace=True)
newdf.TrafficFlash.fillna(2, inplace=True)

#Expand categorical values
newdf = pd.get_dummies(newdf, columns=['ZipCode','TrafficFlash'])
newdf.head()

#Now split the data prior to doing the scaling
train_full, test_full = train_test_split(newdf, test_size=0.2,random_state=42)

trainY = train_full.numacc
train_full.drop(columns=['numacc'], inplace=True)
testY = test_full.numacc
test_full.drop(columns=['numacc'], inplace=True)

#Scale the data
scaler = StandardScaler()
train_full = scaler.fit_transform(train_full)
test_full = scaler.fit_transform(test_full)
```

### Preliminary model training and evaluation

Though I have not examined the full set of features that I initially set out to use, I want to train a model to see how well I can predict the number of collisions in intersections using the current set of features. For this, I have chosen to use a Ridge Regression with cross validation to optimize the regularization hyperparameter. This was selected as a first-pass model as it is not highly prone to overfitting, but it may also not capture complex, non-linear relationships between features:


```python
#fit the model to the data
clf = RidgeCV(alphas=[1e-3, 1e-2, 1e-1, 1, 10]).fit(train_full, trainY)
#clf.score(train_full, trainY)

#Evaluate the fit:
training_predictions = clf.predict(train_full)
mse = mean_squared_error(trainY, training_predictions)
rmse = np.sqrt(mse)
print('Root mean square error of the training set: ', rmse)

#Use our test set to evaluate the model:
test_predictions = clf.predict(test_full)
mse_test = mean_squared_error(testY, test_predictions)
rmse_test = np.sqrt(mse_test)
print('Root mean square error of the test set: ',rmse_test)

```

    Root mean square error of the training set:  7.33686222087941
    Root mean square error of the test set:  8.899874020383058


The our root mean square error for the test set is slightly higher than for the training set, which may suggest that we have overfitted the model. Even with that, the standard deviation of number of accidents is 9.44, which only slightly higher than our predicted outcomes. These details suggest that we need to include additional features in our model to better predict the accident rate.

Therefore, my focus moving forward will be to include new features that focus on **VISIBILITY** at intersections, as well as **DISTRACTIONS** at intersections. Toward the former, I will use geopspatial data and potentially aerial imagery to determine if there are blind spots at intersections that may lead to a higher rate of accidents. For distractions, I will use Google Maps and Yelp or other review platforms to determine if there are popular attractions at particular intersections that may create local traffic pockets or distractions for drivers.

Additionally, future versions of this project will switch from a regression to predict the accident rate to a classifier that will predict a tiered risk level for intersections. This is ultimately a more relevant report for the ISPRS, as it provides additional segmentation of the data for municipal decision-makers to effectively perform their job.
