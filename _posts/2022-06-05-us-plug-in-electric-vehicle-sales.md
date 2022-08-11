---
title: "US PEV Sales"
category: posts
excerpt: "What are the best-selling plug-in electric vehicles?"
---

## 1. Introduction

Electric vehicles are getting more and more popular recent years, while I'm very interested in it, I know little about it. This project focus on exploratory data analysis and corresponding data visualization of the USA sales data of **Plugin-in Electric Vehicles (PEVs)**, hope it may help people like me to have an overall understanding of PEVs Market in the USA.

First and foremost, we need to know the corresponding acronyms.

There are four types of electric vehicles: 
- `Battery Electric Vehicles (BEVs)`: All-electric/battery electric vehicles, see [link](https://en.wikipedia.org/wiki/Battery_electric_vehicle).
- `Plug-in Hybrid Electric Vehicles (PHEVs)`: A combination of gasoline and electric vehicles, see [link](https://en.wikipedia.org/wiki/Plug-in_hybrid).
- `Hybrid Electric Vehicles (HEVs)`: A type of vehicle that uses both an electric engine and a conventional internal combustion engine, see [link](https://en.wikipedia.org/wiki/Hybrid_electric_vehicle).
- `Fuel Cell Electric Vehicles (FCEVs)`: Vehicles powered by hydrogen, see [link](https://afdc.energy.gov/vehicles/fuel_cell.html).

[Plug-In Electric Vehicles (PEVs)](https://en.wikipedia.org/wiki/Plug-in_electric_vehicle) consists of Battery Electric Vehicles (BEVs) and Plug-in Hybrid Electric Vehicles (PHEVs), is projected as the most lucrative segments. It's also known as [New Energey Vehicle (NEV)](https://en.wikipedia.org/wiki/New_energy_vehicles_in_China) in China. 


## 2. Ask

It's not a business task, just a practice of exploratory data analysis using Python. The goal of this project is to have an overall understanding of the U.S. PEV sales data.


## 3. Prepare

<a id="data_used"></a>
### 3.1 Data used

The data file is [us-plug-in-ev-sales.csv](https://github.com/eeliuqin/data-analysis/blob/main/us-plug-in-ev-sales.csv), it contains monthly/quarterly sales quantities of PEVs, from 2010 till now. It has the following columns:

- `Vehicle`: The model name of the vehicle
- `Type`: BEV or PHEV
- `Maker`: The automaker of the vehicle
- `Year`: Four digits of the calendar year
- `Jan` - `Dec`: Sold quantities of each month

I collected it from the following websites:

- [CarSalesBase](https://carsalesbase.com/)
- [GoodCarBadCar](https://www.goodcarbadcar.net/)
- [Inside EVs](https://insideevs.com/)
- [CleanTechnica](https://cleantechnica.com/)

**Is the data source reliable?**

It is not guaranteed to be accurate and reliable. But GoodCarBadCar claims it is commonly sourced by other media outlets, like CNET, Forbes and New York Times. And the sum between 2010-2019 in the csv file basically matches with [U.S. Department of Energy: U.S. PEV Sales by Model](https://afdc.energy.gov/data/10567), which might imply the data is reliable to some extent. 

**Is the data complete?**

We can find plenty of 0s, the reasons are:

- Different vehicles have different market introduction date. For example, the Tesla Model 3 doesn't have sales data in 2010-2016 because it was released in 2017.
- Some vehicles only have quarterly data, i.e., only March, June, September and December have none 0 values.
- Some vehicles only have yearly data, i.e, only December have none 0 values.
- Some vehicles are discontinued(reference [link](https://evadoption.com/ev-models/discontinued-electric-vehicles-evs/)).
- 2022 has sales data in January, February and March only.

The data is not perfect, but we still can get some insights by analyzing it, let's start our journey.


### 3.2 Importing the data


```python
# import required libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

%matplotlib inline

# read the csv
sales = pd.read_csv("us-plug-in-ev-sales.csv")

# check first 5 rows
sales.head()
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
      <th>Vehicle</th>
      <th>Type</th>
      <th>Maker</th>
      <th>Year</th>
      <th>Jan</th>
      <th>Feb</th>
      <th>Mar</th>
      <th>Apr</th>
      <th>May</th>
      <th>Jun</th>
      <th>Jul</th>
      <th>Aug</th>
      <th>Sep</th>
      <th>Oct</th>
      <th>Nov</th>
      <th>Dec</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2015</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>49</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2016</td>
      <td>327</td>
      <td>243</td>
      <td>332</td>
      <td>326</td>
      <td>361</td>
      <td>353</td>
      <td>349</td>
      <td>346</td>
      <td>312</td>
      <td>348</td>
      <td>394</td>
      <td>589</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2017</td>
      <td>387</td>
      <td>400</td>
      <td>414</td>
      <td>301</td>
      <td>294</td>
      <td>324</td>
      <td>218</td>
      <td>129</td>
      <td>85</td>
      <td>17</td>
      <td>38</td>
      <td>270</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2018</td>
      <td>145</td>
      <td>199</td>
      <td>214</td>
      <td>189</td>
      <td>267</td>
      <td>238</td>
      <td>220</td>
      <td>240</td>
      <td>230</td>
      <td>210</td>
      <td>180</td>
      <td>265</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2019</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>253</td>
      <td>856</td>
      <td>726</td>
      <td>678</td>
      <td>593</td>
      <td>434</td>
      <td>462</td>
      <td>621</td>
      <td>746</td>
    </tr>
  </tbody>
</table>
</div>



Nothing obvious, but it's long data, to make it tidy, we might need to combine Year and Jan - Dec to a single date column later. 

Next, let's explore the data to prepare for data cleaning and manipulation.


### 3.3 Exploring the data

**Data size**


```python
sales.shape
```




    (267, 16)



**Check missing values**


```python
sales.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 267 entries, 0 to 266
    Data columns (total 16 columns):
     #   Column   Non-Null Count  Dtype 
    ---  ------   --------------  ----- 
     0   Vehicle  267 non-null    object
     1   Type     267 non-null    object
     2   Maker    267 non-null    object
     3   Year     267 non-null    int64 
     4   Jan      267 non-null    int64 
     5   Feb      267 non-null    int64 
     6   Mar      267 non-null    int64 
     7   Apr      267 non-null    int64 
     8   May      267 non-null    int64 
     9   Jun      267 non-null    int64 
     10  Jul      267 non-null    int64 
     11  Aug      267 non-null    int64 
     12  Sep      267 non-null    int64 
     13  Oct      267 non-null    int64 
     14  Nov      267 non-null    int64 
     15  Dec      267 non-null    int64 
    dtypes: int64(13), object(3)
    memory usage: 33.5+ KB


There is no NA value, as Data Source section has mentioned, the missing value has been replaced by 0.
Sales data of each month is numeric, which is convenient for calculation.

**Find duplicates**


```python
sales[sales.duplicated(['Vehicle', 'Year'])]
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
      <th>Vehicle</th>
      <th>Type</th>
      <th>Maker</th>
      <th>Year</th>
      <th>Jan</th>
      <th>Feb</th>
      <th>Mar</th>
      <th>Apr</th>
      <th>May</th>
      <th>Jun</th>
      <th>Jul</th>
      <th>Aug</th>
      <th>Sep</th>
      <th>Oct</th>
      <th>Nov</th>
      <th>Dec</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>



The dataset has no duplicates.

**Unique vehicles and makers**

Let's check how many unique vehicles and auto makers there are.


```python
# count the number of unique models
print('# of Vehicles:', len(sales['Vehicle'].unique()))

# count the number of makers
print('# of Makers:', len(sales['Maker'].unique()))
```

    # of Vehicles: 54
    # of Makers: 22


**Range of year**
Next, let's check the year range, and its distribution.


```python
sales.groupby('Year').size()
```




    Year
    2010     1
    2011     4
    2012     5
    2013    15
    2014    21
    2015    28
    2016    31
    2017    42
    2018    43
    2019    44
    2020    12
    2021    11
    2022    10
    dtype: int64



The year range is 2010-2022, 2017-2019 has most data. Data during 2010-2012 may not have data of all available vehicles because it's difficult to track data years ago. Data during 2020-2022 is far from complete. Refer to BEV [Models Currently Available in the US](https://evadoption.com/ev-models/bev-models-currently-available-in-the-us/) there are 26 BEVs, so PEVs should be more than 26 at least.

**Preview of 2010-2012 data**
Let's check what 2010-2012 data looks like.


```python
sales[sales['Year'] <= 2012].head()
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
      <th>Vehicle</th>
      <th>Type</th>
      <th>Maker</th>
      <th>Year</th>
      <th>Jan</th>
      <th>Feb</th>
      <th>Mar</th>
      <th>Apr</th>
      <th>May</th>
      <th>Jun</th>
      <th>Jul</th>
      <th>Aug</th>
      <th>Sep</th>
      <th>Oct</th>
      <th>Nov</th>
      <th>Dec</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>60</th>
      <td>Chevrolet Volt</td>
      <td>PHEV</td>
      <td>Chevrolet</td>
      <td>2011</td>
      <td>321</td>
      <td>281</td>
      <td>608</td>
      <td>493</td>
      <td>481</td>
      <td>561</td>
      <td>125</td>
      <td>302</td>
      <td>723</td>
      <td>1108</td>
      <td>1139</td>
      <td>1529</td>
    </tr>
    <tr>
      <th>61</th>
      <td>Chevrolet Volt</td>
      <td>PHEV</td>
      <td>Chevrolet</td>
      <td>2012</td>
      <td>603</td>
      <td>2046</td>
      <td>2289</td>
      <td>1462</td>
      <td>1680</td>
      <td>1760</td>
      <td>1849</td>
      <td>2831</td>
      <td>2851</td>
      <td>2961</td>
      <td>1519</td>
      <td>2533</td>
    </tr>
    <tr>
      <th>136</th>
      <td>Mercedes B250e</td>
      <td>PHEV</td>
      <td>Mercedes</td>
      <td>2011</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>7</td>
      <td>4</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>137</th>
      <td>Mercedes B250e</td>
      <td>PHEV</td>
      <td>Mercedes</td>
      <td>2012</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>161</th>
      <td>Mitsubishi I EV</td>
      <td>BEV</td>
      <td>Mitsubishi</td>
      <td>2011</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>76</td>
    </tr>
  </tbody>
</table>
</div>



Mitsubishi I EV in 2011 had data in December only, because its retail sales began at that time, it's not yearly data. We will leave it as it is.
Nex, we also need to check what 2020-2022 data looks like.

**Preview of 2020-2022 data**


```python
sales[sales['Year'] >= 2020].head()
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
      <th>Vehicle</th>
      <th>Type</th>
      <th>Maker</th>
      <th>Year</th>
      <th>Jan</th>
      <th>Feb</th>
      <th>Mar</th>
      <th>Apr</th>
      <th>May</th>
      <th>Jun</th>
      <th>Jul</th>
      <th>Aug</th>
      <th>Sep</th>
      <th>Oct</th>
      <th>Nov</th>
      <th>Dec</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2020</td>
      <td>0</td>
      <td>0</td>
      <td>1711</td>
      <td>0</td>
      <td>0</td>
      <td>1161</td>
      <td>0</td>
      <td>0</td>
      <td>2296</td>
      <td>0</td>
      <td>0</td>
      <td>1064</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2021</td>
      <td>0</td>
      <td>0</td>
      <td>3474</td>
      <td>0</td>
      <td>0</td>
      <td>1648</td>
      <td>0</td>
      <td>0</td>
      <td>326</td>
      <td>0</td>
      <td>0</td>
      <td>1981</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2022</td>
      <td>0</td>
      <td>0</td>
      <td>2028</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>BMW i3</td>
      <td>BEV</td>
      <td>BMW</td>
      <td>2020</td>
      <td>21</td>
      <td>27</td>
      <td>16</td>
      <td>34</td>
      <td>75</td>
      <td>79</td>
      <td>216</td>
      <td>191</td>
      <td>203</td>
      <td>216</td>
      <td>177</td>
      <td>247</td>
    </tr>
    <tr>
      <th>26</th>
      <td>BMW i3</td>
      <td>BEV</td>
      <td>BMW</td>
      <td>2021</td>
      <td>109</td>
      <td>109</td>
      <td>122</td>
      <td>173</td>
      <td>166</td>
      <td>173</td>
      <td>144</td>
      <td>138</td>
      <td>144</td>
      <td>66</td>
      <td>66</td>
      <td>66</td>
    </tr>
  </tbody>
</table>
</div>



Audi A3 e-tron had quarterly data only.
2022 is not over yet, when is this dataset's latest date?


```python
sales[sales['Year'] == 2022].describe()
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
      <th>Year</th>
      <th>Jan</th>
      <th>Feb</th>
      <th>Mar</th>
      <th>Apr</th>
      <th>May</th>
      <th>Jun</th>
      <th>Jul</th>
      <th>Aug</th>
      <th>Sep</th>
      <th>Oct</th>
      <th>Nov</th>
      <th>Dec</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>10.0</td>
      <td>10.000000</td>
      <td>10.000000</td>
      <td>10.00000</td>
      <td>10.0</td>
      <td>10.0</td>
      <td>10.0</td>
      <td>10.0</td>
      <td>10.0</td>
      <td>10.0</td>
      <td>10.0</td>
      <td>10.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>2022.0</td>
      <td>174.800000</td>
      <td>174.800000</td>
      <td>11605.20000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.0</td>
      <td>436.881067</td>
      <td>436.881067</td>
      <td>19833.70771</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>min</th>
      <td>2022.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.00000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2022.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>161.50000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2022.0</td>
      <td>0.500000</td>
      <td>0.500000</td>
      <td>1801.00000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2022.0</td>
      <td>87.000000</td>
      <td>87.000000</td>
      <td>7731.00000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2022.0</td>
      <td>1399.000000</td>
      <td>1399.000000</td>
      <td>50834.00000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



In summary, some vehicles have quarterly data only, which will affect the monthly sales analysis. We will need to deal with it later.

For sales data in 2022, we only have data in January-March, so the other months can be deleted.

And as we've mentioned in the Data Source section, sales data prior to 2019 is close to complete, we will focus on that part, and make 2020-2022 data as a supplement of analyzing vehicles in above list.


## 4. Process

**Split yearly/quarterly data**

First and foremost, we need to divide Yearly data(`Dec` is not 0) evenly to 12 months. And to avoid skewed monthly sales, let's divide the quarterly data in `Mar`, `Jun`, `Sep` and `Dec` evenly to the other months of the same quarter.

Here we define yearly data as if it meets the following conditions:
- The only non 0 value is in `Dec`.
- It's not the first year that vehicle had data.


```python
# get min year of each vehicle
sales_min_year = sales[['Vehicle', 'Year']].groupby(['Vehicle']).min().reset_index()
sales = pd.merge(sales, sales_min_year, on='Vehicle')
sales['First_Year'] = sales['Year_x'] == sales['Year_y']
sales.drop(columns=['Year_y'], inplace=True)
sales.rename(columns={'Year_x': 'Year'}, inplace=True)

# divide yearly data
def divide_year(row):
    if not row['First_Year'] and (row['Jan':'Nov'] == 0).all() and row['Dec'] > 0:
        year_sum = row['Dec']
        row['Jan':'Nov'] = int(round(year_sum/12, 0))
        row['Dec'] = year_sum - row['Jan'] * 11
    return row

# divide quarterly data
def divide_quarter(row):
    if (row['Jan':'Feb'] == 0).all() and row['Mar'] > 0:
        quarter_sum = row['Mar']
        row['Jan':'Feb'] = int(round(quarter_sum/3, 0))
        row['Mar'] = quarter_sum - row['Jan'] * 2
    if (row['Apr':'May'] == 0).all() and row['Jun'] > 0:
        quarter_sum = row['Jun']
        row['Apr':'May'] = int(round(quarter_sum/3, 0))
        row['Jun'] = quarter_sum - row['Apr'] * 2
    if (row['Jul':'Aug'] == 0).all() and row['Sep'] > 0:
        quarter_sum = row['Sep']
        row['Jul':'Aug'] = int(round(quarter_sum/3, 0))
        row['Sep'] = quarter_sum - row['Jul'] * 2
    if (row['Oct':'Nov'] == 0).all() and row['Dec'] > 0:
        quarter_sum = row['Dec']
        row['Oct':'Nov'] = int(round(quarter_sum/3, 0))
        row['Dec'] = quarter_sum - row['Oct'] * 2
    return row

sales = sales.apply(divide_year, axis=1)
sales = sales.apply(divide_quarter, axis=1)
sales[sales['Year'] >= 2020].head()
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
      <th>Vehicle</th>
      <th>Type</th>
      <th>Maker</th>
      <th>Year</th>
      <th>Jan</th>
      <th>Feb</th>
      <th>Mar</th>
      <th>Apr</th>
      <th>May</th>
      <th>Jun</th>
      <th>Jul</th>
      <th>Aug</th>
      <th>Sep</th>
      <th>Oct</th>
      <th>Nov</th>
      <th>Dec</th>
      <th>First_Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2020</td>
      <td>570</td>
      <td>570</td>
      <td>571</td>
      <td>387</td>
      <td>387</td>
      <td>387</td>
      <td>765</td>
      <td>765</td>
      <td>766</td>
      <td>355</td>
      <td>355</td>
      <td>354</td>
      <td>False</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2021</td>
      <td>1158</td>
      <td>1158</td>
      <td>1158</td>
      <td>549</td>
      <td>549</td>
      <td>550</td>
      <td>109</td>
      <td>109</td>
      <td>108</td>
      <td>660</td>
      <td>660</td>
      <td>661</td>
      <td>False</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Audi A3 e-tron</td>
      <td>BEV</td>
      <td>Audi</td>
      <td>2022</td>
      <td>676</td>
      <td>676</td>
      <td>676</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>25</th>
      <td>BMW i3</td>
      <td>BEV</td>
      <td>BMW</td>
      <td>2020</td>
      <td>21</td>
      <td>27</td>
      <td>16</td>
      <td>34</td>
      <td>75</td>
      <td>79</td>
      <td>216</td>
      <td>191</td>
      <td>203</td>
      <td>216</td>
      <td>177</td>
      <td>247</td>
      <td>False</td>
    </tr>
    <tr>
      <th>26</th>
      <td>BMW i3</td>
      <td>BEV</td>
      <td>BMW</td>
      <td>2021</td>
      <td>109</td>
      <td>109</td>
      <td>122</td>
      <td>173</td>
      <td>166</td>
      <td>173</td>
      <td>144</td>
      <td>138</td>
      <td>144</td>
      <td>66</td>
      <td>66</td>
      <td>66</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>



**Convert wide data to tidy data**

Additionaly, let's convert the wide data to [tidy data](https://www.jstatsoft.org/article/view/v059i10) to prepare for aggregate later.


```python
sales_long = pd.melt(sales, id_vars=['Vehicle', 'Type', 'Maker', 'Year'], value_vars=['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'])
sales_long.rename(columns={'variable': 'Month', 'value': 'Qty'}, inplace=True)
```

**Delete data of April-December, 2022**

Finally, we can delete data during Apr-Dec of 2022 since they are all 0s.


```python
sales_long = sales_long[(sales_long['Year'] < 2022) | (sales_long['Year'
                        ] == 2022) & sales_long['Month'
                        ].str.contains('Jan|Feb|Mar')]
sales_long.sample(5, random_state=1)
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
      <th>Vehicle</th>
      <th>Type</th>
      <th>Maker</th>
      <th>Year</th>
      <th>Month</th>
      <th>Qty</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2984</th>
      <td>Cadillac ELR</td>
      <td>PHEV</td>
      <td>Cadillac</td>
      <td>2015</td>
      <td>Dec</td>
      <td>135</td>
    </tr>
    <tr>
      <th>1117</th>
      <td>Cadillac ELR</td>
      <td>PHEV</td>
      <td>Cadillac</td>
      <td>2017</td>
      <td>May</td>
      <td>0</td>
    </tr>
    <tr>
      <th>136</th>
      <td>Mercedes B250e</td>
      <td>PHEV</td>
      <td>Mercedes</td>
      <td>2011</td>
      <td>Jan</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2311</th>
      <td>Nissan Leaf</td>
      <td>BEV</td>
      <td>Nissan</td>
      <td>2011</td>
      <td>Sep</td>
      <td>1031</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Cadillac ELR</td>
      <td>PHEV</td>
      <td>Cadillac</td>
      <td>2013</td>
      <td>Jan</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>


## 5. Analyze and Share

Let's split the data into 2 sections: 2010-2019 (more complete) and 2020-2022 (as a supplement).


### 5.1 2010-2019 sales data


#### 5.1.1 Sales by model


```python
# focus on sales data prior 2019
sales_till_2019 = sales[sales['Year'] <= 2019].copy()

# get sum of 12 months
sales_till_2019['Sum'] = sales_till_2019.loc[:, 'Jan':'Dec'].sum(axis=1)
sales_till_2019.sample(5, random_state=1)
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
      <th>Vehicle</th>
      <th>Type</th>
      <th>Maker</th>
      <th>Year</th>
      <th>Jan</th>
      <th>Feb</th>
      <th>Mar</th>
      <th>Apr</th>
      <th>May</th>
      <th>Jun</th>
      <th>Jul</th>
      <th>Aug</th>
      <th>Sep</th>
      <th>Oct</th>
      <th>Nov</th>
      <th>Dec</th>
      <th>First_Year</th>
      <th>Sum</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>221</th>
      <td>Tesla Model S</td>
      <td>BEV</td>
      <td>Tesla</td>
      <td>2016</td>
      <td>850</td>
      <td>1550</td>
      <td>3990</td>
      <td>800</td>
      <td>1200</td>
      <td>3700</td>
      <td>1954</td>
      <td>2852</td>
      <td>4350</td>
      <td>700</td>
      <td>1100</td>
      <td>5850</td>
      <td>False</td>
      <td>28896</td>
    </tr>
    <tr>
      <th>82</th>
      <td>Ford C-Max Energi</td>
      <td>PHEV</td>
      <td>Ford</td>
      <td>2015</td>
      <td>395</td>
      <td>498</td>
      <td>715</td>
      <td>553</td>
      <td>715</td>
      <td>667</td>
      <td>693</td>
      <td>723</td>
      <td>719</td>
      <td>695</td>
      <td>639</td>
      <td>579</td>
      <td>False</td>
      <td>7591</td>
    </tr>
    <tr>
      <th>266</th>
      <td>Volvo XC90 PHEV</td>
      <td>PHEV</td>
      <td>Volvo</td>
      <td>2019</td>
      <td>95</td>
      <td>105</td>
      <td>155</td>
      <td>100</td>
      <td>120</td>
      <td>170</td>
      <td>110</td>
      <td>125</td>
      <td>175</td>
      <td>188</td>
      <td>172</td>
      <td>157</td>
      <td>False</td>
      <td>1672</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Cadillac ELR</td>
      <td>PHEV</td>
      <td>Cadillac</td>
      <td>2016</td>
      <td>67</td>
      <td>91</td>
      <td>104</td>
      <td>95</td>
      <td>45</td>
      <td>94</td>
      <td>15</td>
      <td>6</td>
      <td>6</td>
      <td>3</td>
      <td>5</td>
      <td>3</td>
      <td>False</td>
      <td>534</td>
    </tr>
    <tr>
      <th>151</th>
      <td>Mercedes GLE 550e</td>
      <td>PHEV</td>
      <td>Mercedes</td>
      <td>2018</td>
      <td>44</td>
      <td>70</td>
      <td>181</td>
      <td>93</td>
      <td>83</td>
      <td>75</td>
      <td>85</td>
      <td>90</td>
      <td>42</td>
      <td>28</td>
      <td>35</td>
      <td>140</td>
      <td>False</td>
      <td>966</td>
    </tr>
  </tbody>
</table>
</div>




```python
# selected X11/CSS4 colors, do not include those close to white
colors = [
    'blue',
    'cadetblue',
    'chartreuse',
    'chocolate',
    'coral',
    'cornflowerblue',
    'crimson',
    'cyan',
    'darkblue',
    'darkcyan',
    'darkgoldenrod',
    'darkgreen',
    'darkkhaki',
    'darkmagenta',
    'orange',
    'darkolivegreen',
    'darkorchid',
    'darkred',
    'darksalmon',
    'darkseagreen',
    'darkslateblue',
    'darkturquoise',
    'deeppink',
    'deepskyblue',
    'dodgerblue',
    'firebrick',
    'forestgreen',
    'gainsboro',
    'gray',
    'gold',
    'goldenrod',
    'green',
    'greenyellow',
    'indianred',
    'indigo',
    'khaki',
    'lawngreen',
    'lime',
    'magenta',
    'maroon',
    'olive',
    'olivedrab',
    'orangered',
    'orchid',
    'peru',
    'pink',
    'powderblue',
    'purple',
    'rebeccapurple',
    'red',
    'rosybrown',
    'royalblue',
    'saddlebrown',
    'salmon'
]

# create a pivot table that with each vehicle in its own column
pivot = pd.pivot_table(data=sales_till_2019, index=['Year'], columns=['Vehicle'], values='Sum')

# create a stacked bar chart
ax = pivot.plot.bar(stacked=True, rot=0, figsize=(12,12), color=colors)
ax.set_title('U.S. PEV Sales by Model (2010-2019)', fontsize=16)
plt.legend(bbox_to_anchor=(1.3, 1), fontsize=8)
plt.show()
```


    
![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_33_0.png)
    


The Nissan Leaf and the Chevrolet Volt were the first 2 PEV models to enter the U.S. market, and were also the mainstream PEV models during 2011-2014. In 2015

Seems the Tesla Model 3 has very high sales numbers since 2018, is it the most selled PEV? So many models make the stacked bar not easy to read. To answer that questions, let's get the list of 10 top selling PEVs.

<a id="top_10_models"></a>
#### 5.1.2 Top 10 models


```python
sales_long_till_2019 = sales_long[sales_long['Year'] <= 2019].copy()
vehicle_grouped = sales_long_till_2019[['Vehicle','Type','Qty']].groupby(['Vehicle', 'Type'])
top_10_model = vehicle_grouped.sum().reset_index().sort_values(by=['Qty'], ascending=False).head(10)
top_10_model
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
      <th>Vehicle</th>
      <th>Type</th>
      <th>Qty</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>44</th>
      <td>Tesla Model 3</td>
      <td>BEV</td>
      <td>300471</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Tesla Model S</td>
      <td>BEV</td>
      <td>159766</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Chevrolet Volt</td>
      <td>PHEV</td>
      <td>157652</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Nissan Leaf</td>
      <td>BEV</td>
      <td>143558</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Toyota Prius PHEV</td>
      <td>PHEV</td>
      <td>101756</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Tesla Model X</td>
      <td>BEV</td>
      <td>69462</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Ford Fusion Energi</td>
      <td>PHEV</td>
      <td>68557</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Chevrolet Bolt</td>
      <td>BEV</td>
      <td>59125</td>
    </tr>
    <tr>
      <th>5</th>
      <td>BMW i3</td>
      <td>BEV</td>
      <td>41988</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Ford C-Max Energi</td>
      <td>PHEV</td>
      <td>39857</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.figure(figsize=(16,6))
ax = sns.barplot(y="Vehicle", x="Qty", data=top_10_model, orient='h', palette="tab20b")
ax.set_title('U.S. Top 10 Selling PEVs (2010-2019 cumulative)', fontsize=16)
plt.show()
```


![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_36_0.png)    
 


Next, let's track the sales of top 10 models quarterly.


```python
from datetime import datetime

def fetch_quarter(row):
    year = str(row.Year)[-2:]
    datetime_object = datetime.strptime(row.Month, "%b")
    month_number = datetime_object.month
    quarter = str(((month_number-1) // 3) + 1)
    return f"{year}Q{quarter}"

sales_long_till_2019["Quarter"] = sales_long_till_2019.apply(fetch_quarter, axis=1)

# grouped by quarter
vehicle_q_grouped = sales_long_till_2019[['Vehicle','Quarter','Qty']].groupby(['Vehicle', 'Quarter'])
vehicle_q_grouped = vehicle_q_grouped.sum().reset_index()

#get the grouped data of the top 10 vehicles
top_10_q_grouped = vehicle_q_grouped[vehicle_q_grouped['Vehicle'].isin(top_10_model['Vehicle'])].copy()
top_10_q_grouped.sort_values(by=['Quarter'], inplace=True)

# create an area plot
pivot = pd.pivot_table(data=top_10_q_grouped, index=['Quarter'], columns=['Vehicle'], values='Qty')
ax = pivot.plot.area(figsize=(12,8))
ax.set_title('U.S. Top 10 Selling PEVs (2010-2019 quarterly)', fontsize=16)
plt.show()
```


![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_38_0.png)    

    


According to the bar plot and area plot of top 10 vehicles, the Tesla Model 3 was the best-selling plug in electric vehicle and its sales increased greatly in just half year later after it came out.

The sales of other vehicles had small cyclical changes and were left far behind the Tesla Model 3. However, the Tesla Model S, while No.2 in sales, has dipped a bit since the Tesla Model 3 came out.

We've known all 3 Tesla models that launched before 2019 were in the top 10 list, does that also mean Tesla had the highest market share? Let's visualize the sales by maker with another bar chart.

<a id="sales_by_maker"></a>
#### 5.1.3 Sales by maker


```python
pivot_maker = pd.pivot_table(data=sales_till_2019, index=['Year'], columns=['Maker'], values='Sum')
ax = pivot_maker.plot.bar(stacked=True, rot=0, figsize=(12,8), color=colors)
ax.set_title('U.S. PEV Sales by Maker (2010-2019 cumulative)', fontsize=16)
plt.legend(bbox_to_anchor=(1, 1), fontsize=10)
plt.show()
```


![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_41_0.png)    
    


Overall, PEVs sales was increased year over year, even though 2019 had slightly less sales than 2018.
Sales in 2010 were too low to be noticeable.
Between 2011-2012, Chevrolet and Nissan dominated the market. As the time went, more and more automakers joined the competition.
Between 2015-2017, the sales of Tesla were stable and close to Nissan, but since then, the proportion of Tesla had greatly increased.

Next, let's get the list of top 10 makers.

<a id="top_10_makers"></a>
#### 5.1.4 Top 10 makers


```python
# grouped by maker
maker_grouped = sales_long_till_2019[['Maker','Qty']].groupby('Maker')
top_sales = maker_grouped.sum().reset_index().sort_values(by=['Qty'], ascending=False)
qty_sum = top_sales['Qty'].sum()

# calculate the percentage of the top 10 makers
top_sales['Maker_Percentage'] = round(top_sales['Qty'] / qty_sum * 100, 1)
top_10_maker = top_sales.head(10)

# calculate the percentage and sales of the rest makers
rest_share = 100 - top_10_maker['Maker_Percentage'].sum()
rest_sum = qty_sum - top_10_maker['Qty'].sum()
rest_maker = pd.DataFrame([['All the Other Makers', rest_sum, rest_share]])
rest_maker.columns = list(top_10_maker.columns)

# concat them
top_10_rest_maker = pd.concat([top_10_maker, rest_maker])
top_10_rest_maker
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
      <th>Maker</th>
      <th>Qty</th>
      <th>Maker_Percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>18</th>
      <td>Tesla</td>
      <td>529699</td>
      <td>37.2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Chevrolet</td>
      <td>222464</td>
      <td>15.6</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Nissan</td>
      <td>143558</td>
      <td>10.1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Ford</td>
      <td>116976</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Toyota</td>
      <td>104036</td>
      <td>7.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>BMW</td>
      <td>93285</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Honda</td>
      <td>35059</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Fiat</td>
      <td>27211</td>
      <td>1.9</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Volkswagen</td>
      <td>18279</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Kia</td>
      <td>17728</td>
      <td>1.2</td>
    </tr>
    <tr>
      <th>0</th>
      <td>All the Other Makers</td>
      <td>115094</td>
      <td>8.1</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.figure(figsize=(16,6))
ax = sns.barplot(y="Maker", x="Qty", data=top_10_maker, orient='h', palette="Paired")
ax.set_title('U.S. Top 10 Selling PEV Makers (2010-2019 cumulative)', fontsize=16)
plt.show()
```


![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_44_0.png)   
    


A pie chart can display each maker's share more clearly.


```python
# draw a pie chart
plt.figure(figsize=(8, 8))
palette_color = sns.color_palette('Paired')
plt.pie(top_10_rest_maker.Maker_Percentage, labels=top_10_rest_maker.Maker,
        startangle=0, colors=palette_color, autopct='%1.1f%%')
plt.title('Share of Top 10 Selling PEV Makers vs. the Rest (2010-2019 cumulative)', fontsize=16)
plt.show()
```


![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_46_0.png)   
    


Tesla still ranked the first, followed by Chevrolet. Their sum sales were more than 50% of the total sales.
The PEV market was dominated by the 10 makers. Their sum was around 92%, while the rest of makers was 8% only.   

In the top 10 list, the share of Tesla was even higher than the sum of the makers who ranked 2nd, 3rd and 4th. So more specially, the PEV market was dominated by Tesla.

Is Tesla popular from the beginning? How does its sales change over time? To answer questions like that, a line chart can show the trend more clearly, this time, let's divide the automakers to 2 groups: Tesla and Non Tesla.

<a id="tesla_vs_non"></a>
#### 5.1.5 Tesla vs. Non Tesla


```python
# divide the makers to 2 groups, Tesla and Non Tesla
def get_maker_group(maker):
    if 'Tesla' in maker:
        return 'Tesla'
    else:
        return 'Non Tesla'
    
sales_long_till_2019['Maker_Group'] = sales_long_till_2019['Maker'].apply(get_maker_group)
tesla_maker_grouped = sales_long_till_2019[['Quarter', 'Maker_Group',
        'Qty']].groupby(['Quarter', 'Maker_Group']).sum().reset_index()

# show the trend by line plots
plt.figure(figsize=(16,6))
ax = sns.lineplot(x='Quarter', y='Qty', hue='Maker_Group', data=tesla_maker_grouped)
ax.set_title('U.S. PEV Sales: Tesla vs. Non Tesla (2010-2019)', fontsize=16)
plt.xticks(rotation=45)
plt.show()
```


![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_48_0.png)   
    


From the above line plot, we can see that before 2018, the sales of both Tesla and Non Tesla vehicles were increasing gradully, and Tesla had less share than Non Tesla. The turning point occurred in 2018 Q2, Tesla's sales rose rapidly and higher than Non Tesla in just one quarter. Tesla peaked at 2018 Q4 but in the next quarter, it fell quickly to even a little bit lower than Non Tesla. Then it resumed its uptrend and kept higher market share than Non Tesla makers.

In the top 10 models list, we can see there are 6 BEVs and 4 PHEVs, does it imply BEV is more popular than PHEV? If so, that might be one of the reasons why Tesla is so popular. Let's track their quarterly sales by line plots.

<a id="bev_vs_phev"></a>
#### 5.1.6 BEV vs. PHEV


```python
# grouped by Type: BEV or PHEV
type_grouped = sales_long_till_2019.groupby(['Quarter', 'Type']).sum().reset_index()

# show the trend by line plots
plt.figure(figsize=(16,6))
ax = sns.lineplot(x='Quarter', y='Qty', hue='Type', data=type_grouped, palette=['red','blue'])
ax.set_title('U.S. PEV Sales: BEV vs. PHEV (2010-2019)', fontsize=16)
plt.xticks(rotation=45)
plt.show()
```


![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_50_0.png)    
    


Before 2017 Q2, sales of BEV and PHEV were very close, after that, BEV had higher sales and the curve was very similar to Tesla's sales curve. More specially, the high sales of BEV was thanks to the launch of the Tesla Model 3.

In short, Tesla is not popular because it is a BEV, but conversely, BEV is popular because of Tesla.

<a id="sales_by_month"></a>
#### 5.1.7 Sales by month

We already know the sales per year, did the monthly sales make any difference? Before we split the sales by month, we need to do some manipulation.

We can delete the 2010 sales data since it only had one small value in December.


```python
sales_current = sales_long_till_2019[sales_long_till_2019['Year'] > 2010]
month_grouped = sales_current[['Year','Month','Qty']].groupby(['Month', 'Year']).sum().reset_index()
month_grouped['month_num'] = pd.DatetimeIndex(pd.to_datetime(month_grouped['Month'], format='%b')).month
# sort by month
month_grouped.sort_values(by=['Year', 'month_num'], inplace=True)

fig, axs = plt.subplots(2,1, figsize=(16,12))
ax.grid(False)

# By month
ax1 = sns.barplot(
    x='Month',
    y='Qty',
    data=month_grouped,
    palette='coolwarm',
    hue='Year',
    ax=axs[0]
    )
ax1.set_title('U.S. PEV Sales by Month (2010-2019)', fontsize=16)

# By year
ax2 = sns.barplot(
    x='Year',
    y='Qty',
    data=month_grouped,
    palette='Spectral',
    hue='Month',
    ax=axs[1]
    )
ax2.set_title('U.S. PEV Sales by Year (2010-2019)', fontsize=16)

plt.show()
```


![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_53_0.png)   



In general, sales increase year by year in almost all months.
And the 2nd half-year has more sales than 1st half-year, and December sales are usually the highest.
However, sales of 2019 was down since June. That might explain why 2019 had less sales than 2018.

<a id="sup_sales"></a>
### 5.2 Supplement: 2020-2022 sales data

We've known the sales data of 2020-2022 is not as complete as 2010-2019, only a few vehicles have data during 2020-2022, and 2022 has the first quarter data only.

First of all, let's get the list of those vehicles.


```python
sales_selected = sales_long[sales_long['Year'] >= 2020]
sales_selected['Vehicle'].unique()
```




    array(['Audi A3 e-tron', 'BMW i3', 'BMW i8', 'Chevrolet Bolt',
           'Chevrolet Volt', 'Mitsubishi Outlander Plug In', 'Nissan Leaf',
           'Tesla Model 3', 'Tesla Model S', 'Tesla Model X', 'Tesla Model Y',
           'Volkswagen e-Golf'], dtype=object)



All the 3 Tesla models are in the list, with a new model Tesla Model Y. Let's track their sales quarterly.

<a id="all_tesla"></a>
#### 5.2.1 2010-2022 Tesla sales


```python
sales_tesla = sales_long[sales_long['Vehicle'].str.contains('Tesla')].copy()
sales_tesla["Quarter"] = sales_tesla.apply(fetch_quarter, axis=1)

# grouped by model
selected_tesla_grouped = sales_tesla[['Vehicle', 'Quarter', 'Qty']].groupby(['Quarter', 'Vehicle']).sum().reset_index()

# show the trend by line plots
plt.figure(figsize=(16,6))
ax = sns.lineplot(x='Quarter', y='Qty', hue='Vehicle', data=selected_tesla_grouped)
ax.set_title('U.S. Tesla Sales (2010-2022)', fontsize=16)
plt.xticks(rotation=45)
plt.show()
```


![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_58_0.png)    
    


Similar to what we've found in the area plot of Top 10 Selling PEVs, the sales of the Tesla Model S and X have been relatively stable and low, while the sales of the Tesla Model 3 are high and its fluctuation is also obvious.

The Tesla Model 3 sets new sales record in 2020 Q3. However, after the Tesla Model Y came out, the sales of Model 3 decreased as Y increased, and Y even exceeded 3 since 2021. Model Y has a tendency to replace Model 3 as the most popular Tesla vehicle, let's wait and see.

We've known Chevrolet and Nissan ranked 2nd and 3rd of the top selling PEV makers in 2010-2019, do they have any changes in recent 3 years?

<a id="all_chev_nissan"></a>
#### 5.2.2 2010-2022 Chevrolet and Nissan sales


```python
sales_others = sales_long[sales_long['Maker'].str.contains('Chevrolet|Nissan')].copy()
sales_others["Quarter"] = sales_others.apply(fetch_quarter, axis=1)

# grouped by model
others_grouped = sales_others[['Vehicle', 'Quarter', 'Qty']].groupby(['Quarter', 'Vehicle']).sum().reset_index()

# show the trend by line plots
plt.figure(figsize=(16,6))
ax = sns.lineplot(x='Quarter', y='Qty', hue='Vehicle', data=others_grouped)
ax.set_title('U.S. Chevrolet and Nissan Sales (2010-2022)', fontsize=16)
plt.xticks(rotation=45)
plt.show()
```


![png](/figs/us-plug-in-electric-vehicle-sales_files/us-plug-in-electric-vehicle-sales_60_0.png)    



Nissan Leaf is relatively stable, even though we don't see an uptrend. The Chevrolet Volt has been discontinued (see [link](https://evadoption.com/ev-models/discontinued-electric-vehicles-evs/)) in 2019, the Chevrolet Spark is earlier.
As for the Chevrolet Bolt, Q2 2021 hit its all-time peak with more than 11,000 units sold. However, its sales have since declined rapidly to close to zero, which is probably due to [recall](https://www.chevrolet.com/electric/bolt-recall). Once its sales resume, it may be a strong competitor for Tesla, let's wait and see.

<a id="summary"></a>
## 6. Summary

- Overall, PEV sales have grown year over year and peaked in 2018 before declining slightly in 2019.
- The Tesla Model 3 was the best-selling plug-in electric vehicle in 2010-2019, followed by the Tesla Model S and Chevrolet Volt.
- Initial sales of BEVs and PHEVs were similar, then the Tesla Model 3 came out and boosted BEV sales significantly.
- In 2010-2019, the PEV market was dominated by the top 10 selling automakers with a total market share of 92%. Among them, Tesla ranked first with 37.2%.
- The Tesla Model Y has a tendency to replace the Model 3 as the most popular Tesla vehicle.
- The Chevrolet Bolt is a strong competitor to Tesla cars.
