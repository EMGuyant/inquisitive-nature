---
layout: post
author: Ethan Guyant
title:  Introduction to the Pandas Library
category: Quick Start
tags: [Python, Pandas]
description: An overview of various data manipulation functions and methods utilizing pandas dataframes.
image: /assets/img/post_img/introduction-pandas.jpg
image_by: Sincerely Media
image_by_link: https://unsplash.com/@sincerelymedia?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---
### Contents
* [Overview](#overview)   
* [Installation and Use](#installing-and-using-pandas)  
* [Pandas Data Structures](#pandas-data-structures)   
  * [Index](#pandas-index)
  * [Series](#pandas-series)
  * [DataFrame](#pandas-dataframe)
* [Summary](#summary)
   

---

<br>

## Overview
Pandas is a fast, powerful, flexible and easy to use open source data analysis and manipulation tool, built on top of the Python programming language ([pandas](https://pandas.pydata.org/){: .post__link}). Pandas excels at working with tabular data which can be stored using pandas `DataFrames`. `DataFrames` are multidimensional arrays which contain row and column labels and can store various data types including missing data. In addition to providing `DataFrame` as an advantageous data storage interface, pandas also offers numerous data operations that will be familiar to users of databases and spreadsheets.
>Pandas `Series` and `DataFrame` objects build upon the NumPy array structure and provide flexibility (e.g. attaching data labels), data cleaning operations (e.g. working with missing data), and data transformation operations (e.g. groupings).

### Types of Data
* Tabular data with heterogeneous data type columns
* Time series data (ordered and unordered)
* Matrix data with row and column labels
* Observation or statistical datasets

### Task Pandas Excels At
* Handling of missing data (`NaN`)
* The addition or deletion of columns from DataFrames (size mutable)
* Data alignment, can be explicit with a set of labels or automatic (ignoring labels)
* Group by functionality for aggregating and transforming data
* Label-based slicing, indexing, and subsetting of datasets
* Merging and Joining datasets

>A data science project typically includes an iterative cycle of data cleaning, data modeling, data analysis, and formatting of result suitable for communicating results - Pandas is an excellent tool for each of these tasks.

For more details on how Pandas can be used for these tasks see:
* [Data Subsetting - Pandas](/quick%20start/pandas-data-selection.html){: .post__link}
* [Data Transformation - Pandas](/quick%20start/pandas-data-transform-agg.html){: .post__link}
* [Exploratory Data Analysis - Pandas](/quick%20start/pandas-eda.html){: .post__link}

---

<br>

## Installing and Using Pandas   
<br>

### Install from PyPI  
Pandas can be installed via pip from [PyPI](https://pypi.org/project/pandas/){: .post__link} using:
```python
~$ pip install pandas
```

Further detail and other installation instructions, including as part of the [Anaconda](https://docs.continuum.io/anaconda/){: .post__link} distribution, can be found in the [Pandas Documentation](https://pandas.pydata.org/docs/getting_started/install.html){: .post__link}

### Import Pandas
Following the installation of Pandas it can be imported, typically Pandas in imported under the alias `pd`.
```python
>>> import pandas as pd
```

---

<br>

## Pandas Data Structures
There are three basic fundamental Pandas data structures, `Series`, `DataFrame`, and `Index`. Pandas offers numerous useful tools, method, and functionality on top of these fundamental structures.
* Series: 1D labeled homogeneously-typed array of indexed data
* DataFrame: General 2D labeled, size-mutable tabular structure with potentially heterogeneously-typed columns
* Index: Can be viewed as a *immutable array* or an *ordered set*

>Conceptually it can be helpful to think of Pandas data structures as flexible containers of lower dimensional data (e.g. a DataFrame is a container for Series), and objects can be added to or removed from the containers.


### Pandas Index
A `Index` object is the basic object for storing axis (row and column) labels for all pandas object. The `Index` object is an immutable sequence.

Both the `Series` and `DataFrame` objects contain an explicit index which can be utilized to reference and modify the data that higher level objects contain.

The general syntax for constructing a `Index` is <br>
`>>> pd.Index(data=None, dtype=None, copy=False, name=None, tupleize_cols=True, **kwargs)`

**Parameters:**
* data: array-like (1D)
 * Can contain a `Series`, arrays, constants, dataclass or list-like objects
* dtype: NumPy dtype, default of object
  * If dtype is None pandas identifies the best fit and when a dtype is provide pandas coerces to the specified dtype
* copy: boolean value to copy input data
* name: name to be stored in the index
* tupleize_col: boolean value, when `True` pandas attempts to create a multi-index

Construct an `Index` object of integers
```python
>>> index = pd.Index([1, 3, 5, 7, 9])
>>> index
Int64Index([1, 3, 5, 7, 9], dtype='int64')
```
Values of an `Index` object can be selected using standard Python indexing notation
```python
>>> index[3]
7
>>> index[:4]
Int64Index([1, 3, 5, 7], dtype='int64')
```
`Index` objects are immutable, meaning the values cannot be modified
```python
>>> index[3] = 17
Traceback (most recent call last):
  File "<pyshell#72>", line 1, in <module>
    index[3] = 17
  File "C:\Users\emguy\AppData\Local\Programs\Python\Python38-32\lib\site-packages\pandas\core\indexes\base.py", line 4277, in __setitem__
    raise TypeError("Index does not support mutable operations")
TypeError: Index does not support mutable operations
```
Pandas object are aimed at facilitating cross dataset operations (e.g. joins). The `Index` object aids in this by following typical conventions used by Python's built-in `set` data structure facilitating unions, intersections, differences, and other combinations to be computed in a familiar way.
```python
>>> index_1 = pd.Index(['A', 'B', 'A', 'C', 'C'])
>>> index_2 = pd.Index(['E', 'A', 'C', 'F', 'G'])
>>>
>>> index_1.intersection(index_2)
Index(['A', 'C'], dtype='object')
>>>
>>> index_1.union(index_2)
Index(['A', 'A', 'B', 'C', 'C', 'E', 'F', 'G'], dtype='object')
>>>
>>> index_1.symmetric_difference(index_2)
Index(['B', 'E', 'F', 'G'], dtype='object')
```

For more information on `Index` attributes (e.g. `.size`), methods (e.g. `.fillna()`), and examples visit [Pandas Documentation](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html?highlight=dataframe#pandas.DataFrame){: .post__link}

---

<br>

### Pandas Series
A `Series` object contains two main components, the sequence of indices and the sequences of values which can be accessed with the `.index` and `.values` attributes. A `Series` supports integer and label indexing and has a wide variety of methods for performing index based operations.

The general syntax for constructing a `Series` is <br>
`>>> pd.Series(data=None, index=None, dtype=None, name=None, copy=False)`

**Parameters:**
* data: array-like, iterable, dict or scalar value
 * The data that is stored in the series, if dict argument order is maintained
* index: array-like or index (1d)
  * Hashable values that must be the same length as *data*
    * Will default to RangeIndex (0, 1, ..., n)
    * If *data* is a dict-like and no index is provided the keys of the data will utilized for the index
* dtype: data type of the output series, if not specified *data* will be used to infer dtype
* copy: boolean value to copy input data

A `Series` can be created from a list or an array
```python
>>> import pandas as pd
>>> data_series = pd.Series([0.0, 0.2, 0.4, 0.6, 0.8, 1.0])
>>> data_series
0    0.0
1    0.2
2    0.4
3    0.6
4    0.8
5    1.0
dtype: float64
>>>
>>> data_series.index
RangeIndex(start=0, stop=6, step=1)
>>> data_series.values
array([0.0 , 0.2, 0.4, 0.6, 0.8, 1.0 ])
```
The elements of a `Series` can be accessed using the common square bracket `[ ]` notation. For more details see: [Data Subsetting - Pandas](/quick%20start/pandas-data-selection.html){: .post__link}
```python
>>> data_series[1]
0.2
>>> data_series[2:4]
2    0.4
3    0.6
dtype: float64
```
The pandas `Series` object has an explicitly defined index for each element of the series. This index does not have to be an integer and does not need to be contiguous or sequential. The explicitly defined index functionality provide flexibility and is a major difference between a NumPy array (implicit index) and a pandas `Series` (explicit index). 
```python
>>> data_series = pd.Series([0.0, 0.2, 0.4, 0.6, 0.8, 1.0], index=['A', 'B', 'C', 'D', 'E', 'F'])
>>> data_series
A    0.0
B    0.2
C    0.4
D    0.6
E    0.8
F    1.0
dtype: float64
>>> data_series['C':'E']
C    0.4
D    0.6
E    0.8
dtype: float64
>>> data_series = pd.Series([0.0, 0.2, 0.4, 0.6, 0.8, 1.0], index=[0, 2, 4, 6, 8, 10])
>>> data_series
0     0.0
2     0.2
4     0.4
6     0.6
8     0.8
10    1.0
```
The explicit index of the `Series` provides similarities to a dictionary data structure, where typed keys are mapped to typed values. A Pandas `Series` can be created from a Python dictionary, by default the index will be constructed from the dictionary's sorted keys. Similar to a dictionary, the selection of an element can be done using square bracket `[ ]` notation, however unlike a dictionary the `Series` supports slicing operations
```python
>>> capitals_dict = {'Alaska': 'Juneau',
		     'Arizona': 'Phoenix',
		     'Arkansas': 'Little Rock',
		     'California': 'Sacramento'}
>>> capitals = pd.Series(capitals_dict)
>>> capitals
Alaska             Juneau
Arizona           Phoenix
Arkansas      Little Rock
California     Sacramento
dtype: object
>>> capitals['Arkansas']
'Little Rock'
>>> capitals['Alaska':'Arkansas']
Alaska           Juneau
Arizona         Phoenix
Arkansas    Little Rock
dtype: object
```

For more information on `Series` attributes (e.g. `.index`), methods (e.g. `.count()`), and examples visit [Pandas Documentation](https://pandas.pydata.org/docs/reference/api/pandas.Series.html?highlight=series#pandas.Series){: .post__link}

---

<br>

### Pandas DataFrame
A `DataFrame` is a two-dimensional, size-mutable, tabular data object. The `DataFrame` is essentially a dictionary-like container for a `Series` which contains labeled rows and columns
>Conceptually a `DataFrame` is a sequence of `Series` objects which share the same index.

The general syntax for constructing a `DataFrame` is <br>
`>>> pd.DataFrame(data=None, index=None, columns=None, dtype=None, copy=False)`

**Parameters:**
* data: ndarray, iterable, dict or DataFrame
  * Can contain a `Series`, arrays, constants, dataclass or list-like objects
* index: array-like or index 
  * Will default to RangeIndex if there is not index information in the input data and no index is provided
* columns: labels used in the resulting `DataFrame` which defaults to RangeIndex (0, 1, ..., n)
* dtype: data type of the output, only a single dtype is allowed
* copy: boolean value to copy input data

Creating a `DataFrame` from two `Series`
```python
>>> capitals_dict = {'Alaska': 'Juneau',
		     'Arizona': 'Phoenix',
		     'Arkansas': 'Little Rock',
		     'California': 'Sacramento'}
>>> capitals = pd.Series(capitals_dict)
>>> capitals
Alaska             Juneau
Arizona           Phoenix
Arkansas      Little Rock
California     Sacramento
dtype: object
>>>
>>> pop_dict = {'Alaska': 31275, 'Arizona': 1608139, 'Arkansas': 202591, 'California':524943}
>>> cap_pop = pd.Series(pop_dict)
>>> cap_pop
Alaska          31275
Arizona       1608139
Arkansas       202591
California     524943
dtype: int64
>>>
>>> capital_info = pd.DataFrame({'Name':capitals, 'Population':cap_pop})
>>> capital_info
                   Name  Population
Alaska           Juneau       31275
Arizona         Phoenix     1608139
Arkansas    Little Rock      202591
California   Sacramento      524943
```
Similar to the `Series` object the `DataFrame` has index and values attribute and also columns attribute. The columns attribute is an Index object which contains the column names.
```python
>>> capital_info.index
Index(['Alaska', 'Arizona', 'Arkansas', 'California'], dtype='object')
>>> capital_info.values
array([['Juneau', 31275],
       ['Phoenix', 1608139],
       ['Little Rock', 202591],
       ['Sacramento', 524943]], dtype=object)
>>> capital_info.columns
Index(['Name', 'Population'], dtype='object')
```
A `DataFrame` can also be constructed from a list of dictionary objects
```python
>>> AK = {'Capital Name': 'Juneau', 'Capital Population': 31275}
>>> AZ = {'Capital Name': 'Phoenix', 'Capital Population': 1608139}
>>> AR = {'Capital Name': 'Little Rock', 'Capital Population': 202591}
>>> CA = {'Capital Name': 'Sacramento', 'Capital Population': 524943}
>>> df_capital_info = pd.DataFrame([AK, AZ, AR, CA], index=['Alaska', 'Arizona', 'Arkansas', 'California'])
>>> df_capital_info
           Capital Name  Capital Population
Alaska           Juneau               31275
Arizona         Phoenix             1608139
Arkansas    Little Rock              202591
California   Sacramento              524943
```
A `DataFrame` can be thought of as a specialized dictionary where the `DataFrame` maps a column name (key) to a `Series` of columnar data (value). Using the `capital_info` DataFrame the selection of Population returns the `Series` object which contain the column of Population Data
```python
>>> capital_info['Population']
Alaska          31275
Arizona       1608139
Arkansas       202591
California     524943
Name: Population, dtype: int64
```

For more information on `DataFrame` attributes (e.g. `.index`), methods (e.g. `.count()`), and examples visit [Pandas Documentation](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html?highlight=dataframe#pandas.DataFrame){: .post__link}

---

<br>

## Summary
Pandas offers a powerful and flexible data analytics tool which excels at the various stages of a data science project including data cleaning, data modeling, data analysis, and formatting of final results for visualizations. The pandas library offers three fundamental data structures: `Index`, `Series`, and `DataFrame`. Each of these data structures come with numerous tools, attributes, and methods. In general the pandas data structures can be thought of as flexible containers of lower dimensional data that allow for objects to be added to or removed from the container. 

More details and examples can be found in the [Pandas Documentation](https://pandas.pydata.org/docs/index.html){: .post__link} or for more information on subsetting of `Series` and `DataFrames`, data transformation tools, and exploratory analysis with pandas please visit the articles below:

* [Data Subsetting - Pandas](/quick%20start/pandas-data-selection.html){: .post__link}
* [Data Transformation - Pandas](/quick%20start/pandas-data-transform-agg.html){: .post__link}
* [Exploratory Data Analysis - Pandas](/quick%20start/pandas-eda.html){: .post__link}