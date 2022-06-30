---
layout: post
author: Ethan Guyant
title:  Exploratory Data Analysis - Pandas
categories: Python, Pandas
description: An overview of various data manipulation functions and methods utilizing pandas dataframes.
---

### Contents   
[Transforming DataFrames](#transforming-dataframes)   

---

<br>

### Dataframes have helpful exploratory methods
* `.head()` displays the first 5 rows of a DataFrame by default
  * A value can be passed into `.head()` to specify the number of rows to show
```python
>>> df.head()
  Col A  Col B  Col C
0     A      1    9.0
1     A      2    8.0
2     A      3    7.0
3     B      4    NaN
4     B      5    6.0
>>> df.head(2)
  Col A  Col B  Col C
0     A      1    9.0
1     A      2    8.0
```
* `info()` displays the names of columns, the data type of the column, and whether the column contains missing values
```python
>>> df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 6 entries, 0 to 5
Data columns (total 3 columns):
 #   Column  Non-Null Count  Dtype  
---  ------  --------------  -----  
 0   Col A   6 non-null      object 
 1   Col B   6 non-null      int64  
 2   Col C   5 non-null      float64
dtypes: float64(1), int64(1), object(1)
memory usage: 184.0+ bytes
```
* `.shape` is a DataFrame attribute which contains a tuple which stores the number of rows and columns in the DataFrame
```python
>>> df.shape
(6, 3)
```
* `describe()` computes various summary statistics for numerical columns contained within the DataFrame
```python
>>> df.describe()
          Col B     Col C
count  6.000000  5.000000
mean   3.500000  7.000000
std    1.870829  1.581139
min    1.000000  5.000000
25%    2.250000  6.000000
50%    3.500000  7.000000
75%    4.750000  8.000000
max    6.000000  9.000000
```

---