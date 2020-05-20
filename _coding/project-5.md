---
title: "Melbourne Housing Market: Multiple Linear Regression"
excerpt: "Predict house market value with Python."
collection: coding
tags:
  - Python
  - Data science
---

# Abstract
For this project, as for the NYC Airbnb analysis, I used Python and the scikit-learn library to predict the price of houses based on the independent variables. Because there were more variables in this dataset and because several factors were linearly correlated to the price, a multiple linear regression model performed well.

The notebook analysis can also be seen <a href="https://www.kaggle.com/wguesdon/what-is-my-house-market-value" target="_blank">here</a>.  

<img src="https://i.imgur.com/dXIK3yX.jpg" width="600px">

Photo by [Breno Assis](https://unsplash.com/photos/r3WAWU5Fi5Q) on Unsplash

## 1. Dataset

https://www.kaggle.com/anthonypino/melbourne-housing-market

* Suburb: Suburb
* Address: Address
* Rooms: Number of rooms
* Price: Price in Australian dollars
* Method: S - property sold; SP - property sold prior; PI - property passed in; PN - sold prior not disclosed; SN - sold not disclosed; NB - no bid; VB - vendor bid; W - withdrawn prior to auction; SA - sold after auction; SS - sold after auction price not disclosed. N/A - price or highest bid not available.
* Type: br - bedroom(s); h - house,cottage,villa, semi,terrace; u - unit, duplex; t - townhouse; dev site - development site; o res - other residential.
* SellerG: Real Estate Agent
* Date: Date sold
* Distance: Distance from CBD in Kilometres
* Regionname: General Region (West, North West, North, North east ...etc)
* Propertycount: Number of properties that exist in the suburb.
* Bedroom2 : Scraped # of Bedrooms (from different source)
* Bathroom: Number of Bathrooms
* Car: Number of carspots
* Landsize: Land Size in Metres
* BuildingArea: Building Size in Metres
* YearBuilt: Year the house was built
* CouncilArea: Governing council for the area
* Lattitude: Self explanitory
* Longtitude: Self explanitory

Dataset downloaded from Kaggle on the 2020/01/05

## 2. Exploratory Data Analysis

### 2.1 Explore the dataset structure


```python
# Version v01-02
# Import all libraries
import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)
import numpy as np # linear algebra
import matplotlib.pyplot as plt # ploting the data
import seaborn as sns # ploting the data
import math # calculation
```


```python
# Set up color blind friendly color palette
# The palette with grey:
cbPalette = ["#999999", "#E69F00", "#56B4E9", "#009E73", "#F0E442", "#0072B2", "#D55E00", "#CC79A7"]
# The palette with black:
cbbPalette = ["#000000", "#E69F00", "#56B4E9", "#009E73", "#F0E442", "#0072B2", "#D55E00", "#CC79A7"]

# sns.palplot(sns.color_palette(cbPalette))
# sns.palplot(sns.color_palette(cbbPalette))

sns.set_palette(cbPalette)
#sns.set_palette(cbbPalette)
```


```python
# Load the dataset
#price_less = pd.read_csv('Data/MELBOURNE_HOUSE_PRICES_LESS.csv')
price_less = pd.read_csv('../input/melbourne-housing-market/MELBOURNE_HOUSE_PRICES_LESS.csv')
# price_full = pd.read_csv('Data/Melbourne_housing_FULL.csv')
```


```python
price_less.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 63023 entries, 0 to 63022
    Data columns (total 13 columns):
    Suburb           63023 non-null object
    Address          63023 non-null object
    Rooms            63023 non-null int64
    Type             63023 non-null object
    Price            48433 non-null float64
    Method           63023 non-null object
    SellerG          63023 non-null object
    Date             63023 non-null object
    Postcode         63023 non-null int64
    Regionname       63023 non-null object
    Propertycount    63023 non-null int64
    Distance         63023 non-null float64
    CouncilArea      63023 non-null object
    dtypes: float64(2), int64(3), object(8)
    memory usage: 6.3+ MB



```python
# price_full.info()
```


```python
price_less.head(10)
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
      <th>Suburb</th>
      <th>Address</th>
      <th>Rooms</th>
      <th>Type</th>
      <th>Price</th>
      <th>Method</th>
      <th>SellerG</th>
      <th>Date</th>
      <th>Postcode</th>
      <th>Regionname</th>
      <th>Propertycount</th>
      <th>Distance</th>
      <th>CouncilArea</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Abbotsford</td>
      <td>49 Lithgow St</td>
      <td>3</td>
      <td>h</td>
      <td>1490000.0</td>
      <td>S</td>
      <td>Jellis</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Abbotsford</td>
      <td>59A Turner St</td>
      <td>3</td>
      <td>h</td>
      <td>1220000.0</td>
      <td>S</td>
      <td>Marshall</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Abbotsford</td>
      <td>119B Yarra St</td>
      <td>3</td>
      <td>h</td>
      <td>1420000.0</td>
      <td>S</td>
      <td>Nelson</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Aberfeldie</td>
      <td>68 Vida St</td>
      <td>3</td>
      <td>h</td>
      <td>1515000.0</td>
      <td>S</td>
      <td>Barry</td>
      <td>1/04/2017</td>
      <td>3040</td>
      <td>Western Metropolitan</td>
      <td>1543</td>
      <td>7.5</td>
      <td>Moonee Valley City Council</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Airport West</td>
      <td>92 Clydesdale Rd</td>
      <td>2</td>
      <td>h</td>
      <td>670000.0</td>
      <td>S</td>
      <td>Nelson</td>
      <td>1/04/2017</td>
      <td>3042</td>
      <td>Western Metropolitan</td>
      <td>3464</td>
      <td>10.4</td>
      <td>Moonee Valley City Council</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Airport West</td>
      <td>4/32 Earl St</td>
      <td>2</td>
      <td>t</td>
      <td>530000.0</td>
      <td>S</td>
      <td>Jellis</td>
      <td>1/04/2017</td>
      <td>3042</td>
      <td>Western Metropolitan</td>
      <td>3464</td>
      <td>10.4</td>
      <td>Moonee Valley City Council</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Airport West</td>
      <td>3/74 Hawker St</td>
      <td>2</td>
      <td>u</td>
      <td>540000.0</td>
      <td>S</td>
      <td>Barry</td>
      <td>1/04/2017</td>
      <td>3042</td>
      <td>Western Metropolitan</td>
      <td>3464</td>
      <td>10.4</td>
      <td>Moonee Valley City Council</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Airport West</td>
      <td>1/26 Highridge Cr</td>
      <td>3</td>
      <td>h</td>
      <td>715000.0</td>
      <td>SP</td>
      <td>Nelson</td>
      <td>1/04/2017</td>
      <td>3042</td>
      <td>Western Metropolitan</td>
      <td>3464</td>
      <td>10.4</td>
      <td>Moonee Valley City Council</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Albanvale</td>
      <td>1 Jackson Cct</td>
      <td>6</td>
      <td>h</td>
      <td>NaN</td>
      <td>PI</td>
      <td>hockingstuart</td>
      <td>1/04/2017</td>
      <td>3021</td>
      <td>Western Metropolitan</td>
      <td>1899</td>
      <td>14.0</td>
      <td>Brimbank City Council</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Albert Park</td>
      <td>18 Mills St</td>
      <td>3</td>
      <td>h</td>
      <td>1925000.0</td>
      <td>S</td>
      <td>Cayzer</td>
      <td>1/04/2017</td>
      <td>3206</td>
      <td>Southern Metropolitan</td>
      <td>3280</td>
      <td>3.0</td>
      <td>Port Phillip City Council</td>
    </tr>
  </tbody>
</table>
</div>




```python
# price_full.head(10)
```


```python
# Determine the number of missing values for every column
price_less.isnull().sum()
```




    Suburb               0
    Address              0
    Rooms                0
    Type                 0
    Price            14590
    Method               0
    SellerG              0
    Date                 0
    Postcode             0
    Regionname           0
    Propertycount        0
    Distance             0
    CouncilArea          0
    dtype: int64




```python
# Exclude rows with missing prices
data_filtered = price_less.loc[price_less['Price'] > 0]
data_filtered.isnull().sum()
```




    Suburb           0
    Address          0
    Rooms            0
    Type             0
    Price            0
    Method           0
    SellerG          0
    Date             0
    Postcode         0
    Regionname       0
    Propertycount    0
    Distance         0
    CouncilArea      0
    dtype: int64




```python
# price_full.isnull().sum()
```

### 2.2 Explore the continous variables


```python
data = data_filtered.copy()
```


```python
data.describe()
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
      <th>Rooms</th>
      <th>Price</th>
      <th>Postcode</th>
      <th>Propertycount</th>
      <th>Distance</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48433.000000</td>
      <td>4.843300e+04</td>
      <td>48433.000000</td>
      <td>48433.000000</td>
      <td>48433.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>3.071666</td>
      <td>9.978982e+05</td>
      <td>3123.210332</td>
      <td>7566.399913</td>
      <td>12.702761</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.944708</td>
      <td>5.934989e+05</td>
      <td>125.534940</td>
      <td>4457.405884</td>
      <td>7.550030</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>8.500000e+04</td>
      <td>3000.000000</td>
      <td>39.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.000000</td>
      <td>6.200000e+05</td>
      <td>3051.000000</td>
      <td>4280.000000</td>
      <td>7.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>3.000000</td>
      <td>8.300000e+05</td>
      <td>3103.000000</td>
      <td>6567.000000</td>
      <td>11.700000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>4.000000</td>
      <td>1.220000e+06</td>
      <td>3163.000000</td>
      <td>10412.000000</td>
      <td>16.700000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>31.000000</td>
      <td>1.120000e+07</td>
      <td>3980.000000</td>
      <td>21650.000000</td>
      <td>55.800000</td>
    </tr>
  </tbody>
</table>
</div>




```python
data.columns
```




    Index(['Suburb', 'Address', 'Rooms', 'Type', 'Price', 'Method', 'SellerG',
           'Date', 'Postcode', 'Regionname', 'Propertycount', 'Distance',
           'CouncilArea'],
          dtype='object')




```python
data['Price'].describe()
```




    count    4.843300e+04
    mean     9.978982e+05
    std      5.934989e+05
    min      8.500000e+04
    25%      6.200000e+05
    50%      8.300000e+05
    75%      1.220000e+06
    max      1.120000e+07
    Name: Price, dtype: float64




```python
x = 'Price'
sns.set_palette("muted")
sns.distplot(data[x])
plt.ioff()
sns.set_palette(cbPalette)
```


![png](/images/what_is_my_house_market_value/output_26_0.png)



```python
# Log transform the Price variable to approach a normal distribution
x = np.log10(data["Price"])
sns.set_palette("muted")
sns.distplot(x)
plt.ioff()
sns.set_palette(cbPalette)
```


![png](/images/what_is_my_house_market_value/output_27_0.png)



```python
# data["Price"] = np.log10(data.loc[:, "Price"].values)
```


```python
# data["Price"] = np.log10(data.loc[:, "Price"].values)
```


```python
x = 'Rooms'
sns.set_palette("muted")
sns.distplot(data[x])
plt.ioff()
sns.set_palette(cbPalette)
```


![png](/images/what_is_my_house_market_value/output_30_0.png)



```python
x = 'Propertycount'
sns.set_palette("muted")
sns.distplot(data[x])
plt.ioff()
sns.set_palette(cbPalette)
```


![png](/images/what_is_my_house_market_value/output_31_0.png)



```python
x = 'Distance'
sns.set_palette("muted")
sns.distplot(data[x])
plt.ioff()
sns.set_palette(cbPalette)
```


![png](/images/what_is_my_house_market_value/output_32_0.png)


### 2.1 Explore the categorical variables


```python
data.head()
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
      <th>Suburb</th>
      <th>Address</th>
      <th>Rooms</th>
      <th>Type</th>
      <th>Price</th>
      <th>Method</th>
      <th>SellerG</th>
      <th>Date</th>
      <th>Postcode</th>
      <th>Regionname</th>
      <th>Propertycount</th>
      <th>Distance</th>
      <th>CouncilArea</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Abbotsford</td>
      <td>49 Lithgow St</td>
      <td>3</td>
      <td>h</td>
      <td>1490000.0</td>
      <td>S</td>
      <td>Jellis</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Abbotsford</td>
      <td>59A Turner St</td>
      <td>3</td>
      <td>h</td>
      <td>1220000.0</td>
      <td>S</td>
      <td>Marshall</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Abbotsford</td>
      <td>119B Yarra St</td>
      <td>3</td>
      <td>h</td>
      <td>1420000.0</td>
      <td>S</td>
      <td>Nelson</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Aberfeldie</td>
      <td>68 Vida St</td>
      <td>3</td>
      <td>h</td>
      <td>1515000.0</td>
      <td>S</td>
      <td>Barry</td>
      <td>1/04/2017</td>
      <td>3040</td>
      <td>Western Metropolitan</td>
      <td>1543</td>
      <td>7.5</td>
      <td>Moonee Valley City Council</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Airport West</td>
      <td>92 Clydesdale Rd</td>
      <td>2</td>
      <td>h</td>
      <td>670000.0</td>
      <td>S</td>
      <td>Nelson</td>
      <td>1/04/2017</td>
      <td>3042</td>
      <td>Western Metropolitan</td>
      <td>3464</td>
      <td>10.4</td>
      <td>Moonee Valley City Council</td>
    </tr>
  </tbody>
</table>
</div>




```python
# data.shape
```


```python
# data.columns
```


```python
#  https://www.datacamp.com/community/tutorials/categorical-data
data['Suburb'].value_counts().count()
```




    370




```python
#  https://www.datacamp.com/community/tutorials/categorical-data
data['Address'].value_counts().count()
```




    44739




```python
# https://seaborn.pydata.org/generated/seaborn.countplot.html
title = 'Count of properties per Region'
sns.countplot(y = data['Regionname'])
plt.title(title)
plt.ioff()
```


![png](/images/what_is_my_house_market_value/output_39_0.png)



```python
# https://seaborn.pydata.org/generated/seaborn.countplot.html
title = ''
sns.countplot(y = data['Type'])
plt.title(title)
plt.ioff()
```


![png](/images/what_is_my_house_market_value/output_40_0.png)



```python
#  https://www.datacamp.com/community/tutorials/categorical-data
# data['Method'].value_counts()
```


```python
title = ''
sns.countplot(y = data['Method'])
plt.title(title)
plt.ioff()
```


![png](/images/what_is_my_house_market_value/output_42_0.png)



```python
#  https://www.datacamp.com/community/tutorials/categorical-data
# price_less['CouncilArea'].value_counts().count()
```


```python
# price_less['CouncilArea'].value_counts()
```


```python
title = ''
plt.figure(figsize=(20,10))
sns.countplot(y = price_less['CouncilArea'])
plt.title(title)
plt.ioff()
```


![png](/images/what_is_my_house_market_value/output_45_0.png)



```python
# title = ''
# plt.figure(figsize=(20,10))
# sns.countplot(y = price_less['SellerG'])
# plt.title(title)
# plt.ioff()
```


```python
data['SellerG'].value_counts().count()
```




    422




```python
data['Postcode'].value_counts().count()
```




    221



## 3. Visualization

## 3.1 Price vs continous variables


```python
data.head()
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
      <th>Suburb</th>
      <th>Address</th>
      <th>Rooms</th>
      <th>Type</th>
      <th>Price</th>
      <th>Method</th>
      <th>SellerG</th>
      <th>Date</th>
      <th>Postcode</th>
      <th>Regionname</th>
      <th>Propertycount</th>
      <th>Distance</th>
      <th>CouncilArea</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Abbotsford</td>
      <td>49 Lithgow St</td>
      <td>3</td>
      <td>h</td>
      <td>1490000.0</td>
      <td>S</td>
      <td>Jellis</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Abbotsford</td>
      <td>59A Turner St</td>
      <td>3</td>
      <td>h</td>
      <td>1220000.0</td>
      <td>S</td>
      <td>Marshall</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Abbotsford</td>
      <td>119B Yarra St</td>
      <td>3</td>
      <td>h</td>
      <td>1420000.0</td>
      <td>S</td>
      <td>Nelson</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Aberfeldie</td>
      <td>68 Vida St</td>
      <td>3</td>
      <td>h</td>
      <td>1515000.0</td>
      <td>S</td>
      <td>Barry</td>
      <td>1/04/2017</td>
      <td>3040</td>
      <td>Western Metropolitan</td>
      <td>1543</td>
      <td>7.5</td>
      <td>Moonee Valley City Council</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Airport West</td>
      <td>92 Clydesdale Rd</td>
      <td>2</td>
      <td>h</td>
      <td>670000.0</td>
      <td>S</td>
      <td>Nelson</td>
      <td>1/04/2017</td>
      <td>3042</td>
      <td>Western Metropolitan</td>
      <td>3464</td>
      <td>10.4</td>
      <td>Moonee Valley City Council</td>
    </tr>
  </tbody>
</table>
</div>




```python
data.columns
```




    Index(['Suburb', 'Address', 'Rooms', 'Type', 'Price', 'Method', 'SellerG',
           'Date', 'Postcode', 'Regionname', 'Propertycount', 'Distance',
           'CouncilArea'],
          dtype='object')




```python
# see https://seaborn.pydata.org/generated/seaborn.scatterplot.html
sns.set_palette("muted")
x = 'Rooms'
# x = np.log10(data["Rooms"])
# y = 'Price'
y = np.log10(data["Price"])

title = ''
f, ax = plt.subplots(figsize=(8, 6))
sns.scatterplot(x=x, y=y, data=data)
plt.title(title)
plt.ioff()
sns.set_palette(cbPalette)
```


![png](/images/what_is_my_house_market_value/output_53_0.png)



```python
sns.set_palette("muted")
x = "Distance"
y = np.log10(data["Price"])

title = ''
f, ax = plt.subplots(figsize=(8, 6))
sns.scatterplot(x=x, y=y, data=data)
plt.title(title)
plt.ioff()
sns.set_palette(cbPalette)
```


![png](/images/what_is_my_house_market_value/output_54_0.png)



```python
sns.set_palette("muted")
x = np.log10(data["Price"] / data["Rooms"])
y = np.log10(data["Price"])

title = ''
f, ax = plt.subplots(figsize=(8, 6))
sns.scatterplot(x=x, y=y, data=data)
plt.title(title)
plt.ioff()
sns.set_palette(cbPalette)
```


![png](/images/what_is_my_house_market_value/output_55_0.png)



```python
sns.set_palette("muted")
x = np.log10(data['Propertycount'])
# x = np.log10(data['Propertycount'] / data["Rooms"])
y = np.log10(data["Price"])

title = ''
f, ax = plt.subplots(figsize=(8, 6))
sns.scatterplot(x=x, y=y, data=data)
plt.title(title)
plt.ioff()
sns.set_palette(cbPalette)
```


![png](/images/what_is_my_house_market_value/output_56_0.png)


## 3.2 Price vs categorical variables


```python
data.head()
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
      <th>Suburb</th>
      <th>Address</th>
      <th>Rooms</th>
      <th>Type</th>
      <th>Price</th>
      <th>Method</th>
      <th>SellerG</th>
      <th>Date</th>
      <th>Postcode</th>
      <th>Regionname</th>
      <th>Propertycount</th>
      <th>Distance</th>
      <th>CouncilArea</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Abbotsford</td>
      <td>49 Lithgow St</td>
      <td>3</td>
      <td>h</td>
      <td>1490000.0</td>
      <td>S</td>
      <td>Jellis</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Abbotsford</td>
      <td>59A Turner St</td>
      <td>3</td>
      <td>h</td>
      <td>1220000.0</td>
      <td>S</td>
      <td>Marshall</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Abbotsford</td>
      <td>119B Yarra St</td>
      <td>3</td>
      <td>h</td>
      <td>1420000.0</td>
      <td>S</td>
      <td>Nelson</td>
      <td>1/04/2017</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Aberfeldie</td>
      <td>68 Vida St</td>
      <td>3</td>
      <td>h</td>
      <td>1515000.0</td>
      <td>S</td>
      <td>Barry</td>
      <td>1/04/2017</td>
      <td>3040</td>
      <td>Western Metropolitan</td>
      <td>1543</td>
      <td>7.5</td>
      <td>Moonee Valley City Council</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Airport West</td>
      <td>92 Clydesdale Rd</td>
      <td>2</td>
      <td>h</td>
      <td>670000.0</td>
      <td>S</td>
      <td>Nelson</td>
      <td>1/04/2017</td>
      <td>3042</td>
      <td>Western Metropolitan</td>
      <td>3464</td>
      <td>10.4</td>
      <td>Moonee Valley City Council</td>
    </tr>
  </tbody>
</table>
</div>




```python
data.columns
```




    Index(['Suburb', 'Address', 'Rooms', 'Type', 'Price', 'Method', 'SellerG',
           'Date', 'Postcode', 'Regionname', 'Propertycount', 'Distance',
           'CouncilArea'],
          dtype='object')




```python
y="Type"
x=np.log10(data["Price"])

title = ""
f, ax = plt.subplots(figsize=(8, 6))
sns.boxplot(x=x, y=y, data=data, notch=True, showmeans=True,
           meanprops={"marker":"s","markerfacecolor":"white", "markeredgecolor":"black"})
plt.title(title)
plt.ioff()
```


![png](/images/what_is_my_house_market_value/output_60_0.png)



```python
y="Regionname"
x=np.log10(data["Price"])
# x="Price"

title = ""
f, ax = plt.subplots(figsize=(8, 6))
sns.boxplot(x=x, y=y, data=data, notch=True, showmeans=True,
           meanprops={"marker":"s","markerfacecolor":"white", "markeredgecolor":"black"})
plt.title(title)
plt.ioff()
```


![png](/images/what_is_my_house_market_value/output_61_0.png)



```python
y="Method"
x=np.log10(data["Price"])
# x="Price"

title = ""
f, ax = plt.subplots(figsize=(8, 6))
sns.boxplot(x=x, y=y, data=data, notch=True, showmeans=True,
           meanprops={"marker":"s","markerfacecolor":"white", "markeredgecolor":"black"})
plt.title(title)
plt.ioff()
```


![png](/images/what_is_my_house_market_value/output_62_0.png)



```python
sns.set_palette("muted")
y="CouncilArea"
x=np.log10(data["Price"])
# x="Price"

title = ""
f, ax = plt.subplots(figsize=(25, 10))
sns.boxplot(x=x, y=y, data=data, notch=False, showmeans=True,
           meanprops={"marker":"s","markerfacecolor":"white", "markeredgecolor":"black"})
plt.title(title)
plt.ioff()
sns.set_palette(cbPalette)
```


![png](/images/what_is_my_house_market_value/output_63_0.png)


## 4. Models

### 4.1 Data Preprocessing


```python
# https://stackoverflow.com/questions/31468176/setting-values-on-a-copy-of-a-slice-from-a-dataframe
data['Price/Rooms'] = (data.loc[:, "Price"] / data.loc[:, "Rooms"])
```


```python
data.columns
```




    Index(['Suburb', 'Address', 'Rooms', 'Type', 'Price', 'Method', 'SellerG',
           'Date', 'Postcode', 'Regionname', 'Propertycount', 'Distance',
           'CouncilArea', 'Price/Rooms'],
          dtype='object')




```python
data.drop(['Date', 'Address'], axis=1, inplace=True)
```


```python
data.head()
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
      <th>Suburb</th>
      <th>Rooms</th>
      <th>Type</th>
      <th>Price</th>
      <th>Method</th>
      <th>SellerG</th>
      <th>Postcode</th>
      <th>Regionname</th>
      <th>Propertycount</th>
      <th>Distance</th>
      <th>CouncilArea</th>
      <th>Price/Rooms</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Abbotsford</td>
      <td>3</td>
      <td>h</td>
      <td>1490000.0</td>
      <td>S</td>
      <td>Jellis</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
      <td>496666.666667</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Abbotsford</td>
      <td>3</td>
      <td>h</td>
      <td>1220000.0</td>
      <td>S</td>
      <td>Marshall</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
      <td>406666.666667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Abbotsford</td>
      <td>3</td>
      <td>h</td>
      <td>1420000.0</td>
      <td>S</td>
      <td>Nelson</td>
      <td>3067</td>
      <td>Northern Metropolitan</td>
      <td>4019</td>
      <td>3.0</td>
      <td>Yarra City Council</td>
      <td>473333.333333</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Aberfeldie</td>
      <td>3</td>
      <td>h</td>
      <td>1515000.0</td>
      <td>S</td>
      <td>Barry</td>
      <td>3040</td>
      <td>Western Metropolitan</td>
      <td>1543</td>
      <td>7.5</td>
      <td>Moonee Valley City Council</td>
      <td>505000.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Airport West</td>
      <td>2</td>
      <td>h</td>
      <td>670000.0</td>
      <td>S</td>
      <td>Nelson</td>
      <td>3042</td>
      <td>Western Metropolitan</td>
      <td>3464</td>
      <td>10.4</td>
      <td>Moonee Valley City Council</td>
      <td>335000.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Encoding categorical data
# https://pbpython.com/categorical-encoding.html
data = pd.get_dummies(data, columns=['Suburb', 'Rooms', 'Type',  'Method', 'SellerG', 'Regionname', 'CouncilArea'], drop_first=True)
```


```python
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 48433 entries, 0 to 63020
    Columns: 854 entries, Price to CouncilArea_Yarra Ranges Shire Council
    dtypes: float64(3), int64(2), uint8(849)
    memory usage: 42.7 MB



```python
# Split the dataset
X = data.drop('Price', axis=1).values
y = data['Price'].values
y = np.log10(y)
```

### 4.2 Multiple Linear Regression


```python
# Splitting the dataset into the Training set and Test set
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.3, random_state = 0)

# Fitting Multiple Linear Regression to the Training set
from sklearn.linear_model import LinearRegression
lr = LinearRegression()
lr.fit(X_train, y_train)

# Predicting the Test set results
y_pred = lr.predict(X_test)
```


```python
# Compare predicted and actual values
# https://towardsdatascience.com/a-beginners-guide-to-linear-regression-in-python-with-scikit-learn-83a8f7ae2b4f
# https://stackoverflow.com/questions/19100540/rounding-entries-in-a-pandas-dafaframe
df = pd.DataFrame({'Actual': np.round(y_test, 2), 
                   'Predicted': np.round(y_pred, 2)})
df.head(10)
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
      <th>Actual</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5.84</td>
      <td>5.80</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5.66</td>
      <td>5.74</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5.86</td>
      <td>5.90</td>
    </tr>
    <tr>
      <th>3</th>
      <td>5.94</td>
      <td>5.90</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5.97</td>
      <td>5.94</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5.60</td>
      <td>5.66</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6.14</td>
      <td>6.17</td>
    </tr>
    <tr>
      <th>7</th>
      <td>5.91</td>
      <td>5.87</td>
    </tr>
    <tr>
      <th>8</th>
      <td>5.85</td>
      <td>5.86</td>
    </tr>
    <tr>
      <th>9</th>
      <td>5.89</td>
      <td>5.86</td>
    </tr>
  </tbody>
</table>
</div>




```python
# https://towardsdatascience.com/a-beginners-guide-to-linear-regression-in-python-with-scikit-learn-83a8f7ae2b4f
# https://www.theanalysisfactor.com/assessing-the-fit-of-regression-models/
from sklearn.metrics import mean_squared_error
from sklearn import metrics
from sklearn.metrics import r2_score

print('Price mean:', np.round(np.mean(y), 2))  
print('Price std:', np.round(np.std(y), 2))
print('RMSE:', np.round(np.sqrt(metrics.mean_squared_error(y_test, lr.predict(X_test))), 2))
print('R2 score train:', np.round(r2_score(y_train, lr.predict(X_train), multioutput='variance_weighted'), 2))
print('R2 score test:', np.round(r2_score(y_test, lr.predict(X_test), multioutput='variance_weighted'), 2))
```

    Price mean: 5.94
    Price std: 0.22
    RMSE: 0.05
    R2 score train: 0.95
    R2 score test: 0.95


## 5. Conclusions
* Price is clearly proportional to the Distance and Rooms number variables
* Multiple linear regression performs well on this dataset

## 6. References

* https://www.kaggle.com/lpuglisi/visualizing-melbourne-real-estate 
* https://www.kaggle.com/anthonypino/price-analysis-and-linear-regression
* https://www.kaggle.com/emanueleamcappella/random-forest-hyperparameters-tunings
* https://www.datacamp.com/community/tutorials/categorical-data
* https://towardsdatascience.com/a-beginners-guide-to-linear-regression-in-python-with-scikit-learn-83a8f7ae2b4f
* https://www.theanalysisfactor.com/assessing-the-fit-of-regression-models/
* https://pbpython.com/categorical-encoding.html
