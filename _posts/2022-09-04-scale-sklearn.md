---
title: "Scale and Standardize Data with Scikit-learn"
category: posts
excerpt: "Data Preprocessing, MinMaxScaler, Normalizer, StandardScaler"
---

Many machine learning algorithms work better when the data is or approximately normally distributed, so it's common to transform the data to achieve a better model performance.  
As one of the most popular machine learning library in Python, 
[scikit-learn](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.preprocessing) provides various methods for data preprocessing and normalization.
I'm going to use a simple dataset to show how the following method works:

- MinMaxScaler
- Normalizer
- StandardScaler

## Import required packages


```python
from sklearn.preprocessing import MinMaxScaler
from sklearn.preprocessing import Normalizer
from sklearn.preprocessing import StandardScaler
from sklearn.preprocessing import scale
import pandas as pd
import numpy as np
from statistics import mean
from statistics import stdev
from IPython.display import set_matplotlib_formats
import warnings

warnings.filterwarnings(action="ignore")
set_matplotlib_formats('svg')

X = [[1, -1,  2],
     [2,  0,  1],
     [2, -1, -1]]

"""
it's not necessary to convert X to a dataframe
here I use dataframe
    - to make columns/rows easier to read
    - to recreate the process with pandas/numpy
"""
X_df = pd.DataFrame(X)
X_df
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>-1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>-1</td>
      <td>-1</td>
    </tr>
  </tbody>
</table>
</div>



## MinMaxScaler

According to [sklearn.preprocessing.MinMaxScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html), it aims to transform **features** by scaling each feature to a given range. 
In other words, it's **column-wise**.


```python
# MinMaxScaler - it transforms columns in X_df
X_mm = MinMaxScaler().fit_transform(X_df)
X_mm
```




    array([[0.        , 0.        , 1.        ],
           [1.        , 1.        , 0.66666667],
           [1.        , 0.        , 0.        ]])



### Formula

The math behind it:

$$\displaystyle X_{std} = \frac{X - X.min}{X.max - X.min}$$

$$\displaystyle X_{scaled} = X_{std} \cdot (max - min) + min$$

where $$\displaystyle min, max$$ = feature_range.


It's the same as below function:


```python
# recreate MinMaxScaler
(X_df - X_df.min()) / (X_df.max() - X_df.min())
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.666667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>



## Normalizer

According to [sklearn.preprocessing.Normalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html#sklearn.preprocessing.Normalizer), it normalize **samples** individually to **unit norm**. 
In other words, it's **row-wise**. 
As for the norm, there are 3 options.

### l1-norm
[L1 norm](https://mathworld.wolfram.com/L1-Norm.html) of a vector is also known as the Manhattan distance or Taxicab norm.
To calculate the norm, we need to take the sum of the absolute vector values and make sure it's 1.


```python
# Normalizer with l1-norm
X_l1 = Normalizer(norm='l1').fit_transform(X_df)
X_l1
```




    array([[ 0.25      , -0.25      ,  0.5       ],
           [ 0.66666667,  0.        ,  0.33333333],
           [ 0.5       , -0.25      , -0.25      ]])



It's the same as the function below:


```python
def normalizer_l1(X):
    # get absolute sum of each row
    norms = np.abs(X).sum(axis=1)
    # reshape it to multiple rows & 1 column
    norms = norms.values.reshape((len(X), 1))
    # to make sure the absolute sum of element is 1
    X /= norms
    return X

normalizer_l1(X_df)
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.250000</td>
      <td>-0.25</td>
      <td>0.500000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.666667</td>
      <td>0.00</td>
      <td>0.333333</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.500000</td>
      <td>-0.25</td>
      <td>-0.250000</td>
    </tr>
  </tbody>
</table>
</div>



### l2-norm

[l2-norm](https://mathworld.wolfram.com/L2-Norm.html) is also known as the Euclidean norm as it is calculated as the Euclidean distance from the origin.
To calculate the norm, we need to get the square root of the sum of the squared vector elements, and make sure it's 1. In other words, the sum of the squared vector elements is 1.


```python
# Normalizer with l2-norm
X_l2 = Normalizer(norm='l2').fit_transform(X_df)
X_l2
```




    array([[ 0.40824829, -0.40824829,  0.81649658],
           [ 0.89442719,  0.        ,  0.4472136 ],
           [ 0.81649658, -0.40824829, -0.40824829]])



It's the same as the function below:


```python
def normalizer_l2(X):
    # get absolute sum of each row
    norms = np.square(X).sum(axis=1)
    # reshape it to multiple rows & 1 column
    norms = norms.values.reshape((len(X), 1))
    # to make sure the sum of squared element is 1
    X /= np.sqrt(norms)
    return X

normalizer_l2(X_df)
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.408248</td>
      <td>-0.408248</td>
      <td>0.816497</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.894427</td>
      <td>0.000000</td>
      <td>0.447214</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.816497</td>
      <td>-0.408248</td>
      <td>-0.408248</td>
    </tr>
  </tbody>
</table>
</div>



### max-norm

The [max-norm](https://mathworld.wolfram.com/L-Infinity-Norm.html) (sometimes also called infinity norm) is simply the maximum absolute vector element.


```python
# Normalizer with max-norm
X_mnorm = Normalizer(norm='max').fit_transform(X_df)
X_mnorm
```




    array([[ 0.5, -0.5,  1. ],
           [ 1. ,  0. ,  0.5],
           [ 1. , -0.5, -0.5]])



It's the same as the function below:


```python
def normalizer_max(X):
    # get absolute sum of each row
    norms = np.max(abs(X), axis=1)
    # reshape it to multiple rows & 1 column
    norms = norms.values.reshape((len(X), 1))
    # to make sure the sum of squared element is 1
    X /= norms
    return X

normalizer_max(X_df)
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.5</td>
      <td>-0.5</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>-0.5</td>
      <td>-0.5</td>
    </tr>
  </tbody>
</table>
</div>



## StandardScaler

According to [sklearn.preprocessing.StandardScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html#sklearn.preprocessing.StandardScaler), it aims to standardize **features** by removing the mean and scaling to unit variance.

In other words, it's **column-wise**.

### Formula

$$\displaystyle \frac{X_{i} - X.mean}{std}$$

where $$\displaystyle std = \frac{\sum_{i=1}^{N}X_{i} - X.mean}{N}$$

**Note:** The denominator of standard deviation is **N**, not the commonly used **N-1**.
Because the [source code](https://github.com/scikit-learn/scikit-learn/blob/36958fb24/sklearn/preprocessing/_data.py#L818) of scikit-learn mentions the algorithm of std is given in paper [Algorithms for computing the sample variance: Analysis and recommendations](http://www.cs.yale.edu/publications/techreports/tr222.pdf), where N is used.


```python
from sklearn.preprocessing import StandardScaler

X_ss = StandardScaler().fit_transform(X_df)

X_ss
```




    array([[-1.41421356, -0.70710678,  1.06904497],
           [ 0.70710678,  1.41421356,  0.26726124],
           [ 0.70710678, -0.70710678, -1.33630621]])



It's the same as the function below:


```python
# recreate StandardScaler
# ddof=0 so the denominator of standard deviation is N
(X_df - X_df.mean()) / X_df.std(ddof=0)
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-1.414214</td>
      <td>-0.707107</td>
      <td>1.069045</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.707107</td>
      <td>1.414214</td>
      <td>0.267261</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.707107</td>
      <td>-0.707107</td>
      <td>-1.336306</td>
    </tr>
  </tbody>
</table>
</div>



And I found [**scale()**](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.scale.html#sklearn.preprocessing.scale) works the same as **StandardScaler**, see below result:


```python
from sklearn.preprocessing import scale

X_scale = scale(X_df)

X_scale
```




    array([[-1.41421356, -0.70710678,  1.06904497],
           [ 0.70710678,  1.41421356,  0.26726124],
           [ 0.70710678, -0.70710678, -1.33630621]])



**NOTE:** The drawback of `scale()` is it's just a function, and we have to use it carefully to avoid data leakage (Ref [Warning](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.scale.html#sklearn.preprocessing.scale) in the bottom). It's recommended using StandardScaler within a Pipeline in order to prevent most risks of data leaking.

## Comparison

I want to compare them in a larger dataset, for instance, the Boston house pricing dataset. To have a better visualization, I will select several features only and see if they can be transformed well by MinMaxScaler, Normalizer, and StandardScaler.


```python
from sklearn.datasets import load_boston
from scipy.stats import skew, norm
import matplotlib.pyplot as plt
import seaborn as sns

# load the dataset
boston = load_boston()
df = pd.DataFrame(boston.data)
df.columns = boston.feature_names
df.head()
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
      <th>CRIM</th>
      <th>ZN</th>
      <th>INDUS</th>
      <th>CHAS</th>
      <th>NOX</th>
      <th>RM</th>
      <th>AGE</th>
      <th>DIS</th>
      <th>RAD</th>
      <th>TAX</th>
      <th>PTRATIO</th>
      <th>B</th>
      <th>LSTAT</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.00632</td>
      <td>18.0</td>
      <td>2.31</td>
      <td>0.0</td>
      <td>0.538</td>
      <td>6.575</td>
      <td>65.2</td>
      <td>4.0900</td>
      <td>1.0</td>
      <td>296.0</td>
      <td>15.3</td>
      <td>396.90</td>
      <td>4.98</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.02731</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>6.421</td>
      <td>78.9</td>
      <td>4.9671</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>396.90</td>
      <td>9.14</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.02729</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>7.185</td>
      <td>61.1</td>
      <td>4.9671</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>392.83</td>
      <td>4.03</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.03237</td>
      <td>0.0</td>
      <td>2.18</td>
      <td>0.0</td>
      <td>0.458</td>
      <td>6.998</td>
      <td>45.8</td>
      <td>6.0622</td>
      <td>3.0</td>
      <td>222.0</td>
      <td>18.7</td>
      <td>394.63</td>
      <td>2.94</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.06905</td>
      <td>0.0</td>
      <td>2.18</td>
      <td>0.0</td>
      <td>0.458</td>
      <td>7.147</td>
      <td>54.2</td>
      <td>6.0622</td>
      <td>3.0</td>
      <td>222.0</td>
      <td>18.7</td>
      <td>396.90</td>
      <td>5.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
# select 4 features that have very different range
df = df[["CRIM", "RM", "AGE", "TAX"]].copy()
col_names = df.columns
df.head()
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
      <th>CRIM</th>
      <th>RM</th>
      <th>AGE</th>
      <th>TAX</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.00632</td>
      <td>6.575</td>
      <td>65.2</td>
      <td>296.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.02731</td>
      <td>6.421</td>
      <td>78.9</td>
      <td>242.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.02729</td>
      <td>7.185</td>
      <td>61.1</td>
      <td>242.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.03237</td>
      <td>6.998</td>
      <td>45.8</td>
      <td>222.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.06905</td>
      <td>7.147</td>
      <td>54.2</td>
      <td>222.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.figure(figsize=(8, 8))

# Original Data
plt.subplot(221)
plt.gca().set_title('Orginal Data')
sns.kdeplot(data=df)

# Normalizer (l2-norm)
plt.subplot(222)
plt.gca().set_title('Normalizer')
df_n = Normalizer().fit_transform(df)
df_n = pd.DataFrame(df_n, columns=col_names)
sns.kdeplot(data=df_n)

# MinMaxScaler
plt.subplot(223)
plt.gca().set_title('MinMaxScaler')
df_mm = MinMaxScaler().fit_transform(df)
df_mm = pd.DataFrame(df_mm, columns=col_names)
sns.kdeplot(data=df_mm)

# StandardScaler
plt.subplot(224)
plt.gca().set_title('StandardScaler')
df_ss = StandardScaler().fit_transform(df)
df_ss = pd.DataFrame(df_ss, columns=col_names)
sns.kdeplot(data=df_ss)

plt.tight_layout()
plt.savefig("compare-scale-sklearn.svg")
```

    
![svg](/figs/scale-sklearn_files/compare-scale-sklearn.svg)
    


Actually Normalizer is for transforming samples, not features, so it shouldn't be applied in this case. What are Normalizer use cases? I cannot find much useful information online, but I will keep an eye on it. 

Both MinMaxScaler and StandardScaler can transform the data to similar scale, distributions in StandardScaler are very close to each other and center around 0, with both negative and positive values. Distributions in MinMaxScaler are more dispersed, but all values are positive, with range [0, 1].

MinMaxScaler vs. StandardScaler, which one should I use? MinMaxScaler may be used when the upper and lower boundaries are well known from domain knowledge, for example, pixel intensities that go from 0 to 255 in the RGB color range (ref [stackoverflow](https://datascience.stackexchange.com/questions/43972/when-should-i-use-standardscaler-and-when-minmaxscaler)).

But overall, StandardScaler is more commonly used, and it can also be applied in the pixel intensities example. So my strategy is to use StandardScaler, if it doesn't work well, try MinMaxScaler and see if it improves.

