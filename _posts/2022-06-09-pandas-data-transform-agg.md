---
layout: post
author: Ethan Guyant
title:  Data Transformation - Pandas
category: Quick Start
tags: Python Pandas
description: An overview of various data manipulation functions and methods utilizing pandas dataframes.
image: /assets/img/post_img/transformation-pandas.jpg
image_by: Suzanne D. Williams
image_by_link: https://unsplash.com/@scw1217?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---
### Contents  
* [Handling of Missing Data](#handling-of-missing-data)
  * [Missing Data in Pandas](#missing-data-in-pandas)
  * [Null Value Operations](#null-value-operations)
* [Transforming Data](#transforming-data)  
  * [Sorting DataFrame Values](#sorting-dataframe-values)
  * [Add Columns / Rows](#add-columns--rows)
  * [Remove Columns / Rows](#remove-columns--rows)
* [Aggregating DataFrames](#aggregating-dataframes) 
  * [Descriptive Statistics](#descriptive-statistics)
    * [Method: .describe()](#method-describe)
    * [Method: .agg()](#method-agg)
  * [Grouped Summary Statistics](#grouped-summary-statistics)
  * [Pivot Table](#pivot-table)  
* [Joining DataFrames](#joining-dataframes)    
  * [Different Join Types](#different-join-types)
  * [Advanced Merging and Concatenating](#advanced-merging-and-concatenating)
  * [Merging Ordered and Time-Series Data](#merging-ordered-and-time-series-data)

---

<br>

Pandas is a data analysis and manipulation library which provides three fundamental data structures: `Index`, `Series`, and `DataFrame`. For more details on the data structures and an overview of the Pandas library see the previous article - [Introduction to the Pandas Library](/quick%20start/pandas-introduction.html){: .post__link}. 

The focus of this article is to service as a introduction and guide to the basic functions for modifying and manipulating the core data structures, including handling of missing data, `DataFrame` transformation, aggregating data, and joining `DataFrames`

---

<br>

## Handling of Missing Data
<br>
Datasets are typically not clean and ready for use, including that they may contain some level of missing data. How missing data is indicated can vary depending on the data source which can add an additional level of complication when handling the missing data.

Generally, there is no single optimal choice when developing a scheme to indicate the presence of missing data in a data table. Typically the schemes center around using a mask that indicates missing values or implementing a sentinel value that indicates a missing value.

<br>

### Missing Data in Pandas
Within Pandas missing data is indicated by one of the two Python null values: `NaN` (Not a Number) indicating a missing floating-point value or the `None` object, and pandas can convert between the two when needed.

```python
>>> series_1 = pd.Series([10, np.nan, 15, None])
>>> series_1
0    10.0
1     NaN
2    15.0
3     NaN
dtype: float64
```
>For types that do not have a sentinel value (e.g. integer) Pandas automatically type-casts when a missing value is present (e.g. cast an integer to a floating point)

```python
>>> series_2 = pd.Series([0, 1, 2])
>>> series_2
0    0
1    1
2    2
dtype: int64
>>> series_2[1] = None
>>> series_2
0    0.0
1    NaN
2    2.0
dtype: float64
```
>Missing values will propagate through arithmetic operations between pandas objects. Descriptive statistics and certain computational methods ([Series](https://pandas.pydata.org/pandas-docs/dev/reference/series.html#api-series-stats){: .post__link} and [DataFrame](https://pandas.pydata.org/pandas-docs/dev/reference/frame.html#api-dataframe-stats){: .post__link}) all account for missing data (e.g. when summing data missing values will be treated as zero).

<br>

### Null Value Operations
Pandas offer various methods for identifying, removing, and replacing missing values within the data structures.
#### Identifying Null or Missing Values
`pandas.isna(obj)`
* A functions that takes a scalar or array-like object and is used to indicate whether there are missing values.
* Returns a scalar or array of boolean values indicating whether each element is missing
```python
>>> pd.isna('string')
False
>>> pd.isna(np.nan)
True
>>> series = pd.Series([2, 4, np.nan, 8])
>>> pd.isna(series)
0    False
1    False
2     True
3    False
dtype: bool
```

`Series.isna()`
<br>
`DataFrame.isna()`

* A function used to identify missing values within a Series or DataFrame 
* Returns a boolean same-sized object indicating whether an element is a missing value 
  * `None` or `NaN` get mapped to `True` and everything else is mapped to `False`
* `.isnull()` is an alias for `.isna()`
* `.notna()` performs the opposite operation of `.isna()`
  * Non-missing values get mapped to `True`
* Boolean masks, like the object returned by `isna()`, can be used directly as a `Series` or `DataFrame` index
  * For more detail see: [Data Subsetting - Pandas](/quick%20start/pandas-data-selection.html){: .post__link}

>Characters such as an empty string `' '` or `np.inf` are not considered missing values unless `pandas.options.mod.use_inf_as_na = True` is set

```python
>>> # Series Examples
>>> even_series = pd.Series([2, None, 6, np.nan, 10])
>>> even_series.isna()
0    False
1     True
2    False
3     True
4    False
dtype: bool
>>> # DataFrame Example
>>> df
     name   A     B     C     D
0  Oliver  71  83.0   NaN  88.0
1    John  63   NaN  87.0  77.0
2    Jane  86  91.0  92.0  78.0
3  Ashley  98  99.0  79.0  60.0
4   Steve  75  77.0  82.0   NaN
>>> df.isna()
    name      A      B      C      D
0  False  False  False   True  False
1  False  False   True  False  False
2  False  False  False  False  False
3  False  False  False  False  False
4  False  False  False  False   True
>>> # DataFrame - Determine if an element is NOT missing a value (opposite of .isna())
>>> df.notna()
   name     A      B      C      D
0  True  True   True  False   True
1  True  True  False   True   True
2  True  True   True   True   True
3  True  True   True   True   True
4  True  True   True   True  False
```
#### Removing Null or Missing Values
`Series.dropna(axis=0, inplace=False, how=None)`
* A function that returns a new series with missing values removed
  * axis (0 or 'index'): a `Series` has only one axis to drop values from
  * inplace (bool): if `True` the operation of removing missing values is done inplace and `.dropna()` returns None
  * how (str): not currently in use and is kept for compatibility

```python
>>> # Example Series
>>> series = df['B']
>>> series
0    83.0
1     NaN
2    91.0
3    99.0
4    77.0
Name: B, dtype: float64
>>> # Default arguments
>>> series.dropna()
0    83.0
2    91.0
3    99.0
4    77.0
Name: B, dtype: float64
>>> # Reprinting the series verifies the series has not been modified
>>> series
0    83.0
1     NaN
2    91.0
3    99.0
4    77.0
Name: B, dtype: float64
>>> # Set inplace=True to complete the operation inplace
>>> series.dropna(inplace=True)
>>> series
0    83.0
2    91.0
3    99.0
4    77.0
Name: B, dtype: float64
```

`DataFrame.dropna(axis=0, how='any', thresh=None, subset=None, inplace=False)`
* axis {0/'index' or 1/'column'}: determine if missing values are removed from rows or columns
  * 0: drop rows that contain missing values
  * 1: drop columns that contain missing values
* how {'any', 'all'}: determines if a row or column is removed when there is at least one missing value
or all missing values
* thresh (int): require the number of non-missing values required to drop a row or column
* subset (column or sequence labels): labels along an other axis (e.g. when dropping rows a list of columns to consider)
* inplace (bool): boolean value, if `True` the operation of removing missing values is done inplace and returns None

>Dropping missing values from a DataFrame operates on an entire row or column, a single missing value cannot be dropped

```python
>>> # DataFrame Example
>>> df
     name   A     B     C     D
0  Oliver  71  83.0   NaN  88.0
1    John  63   NaN  87.0  77.0
2    Jane  86  91.0  92.0  78.0
3  Ashley  98  99.0  79.0  60.0
4   Steve  75  77.0  82.0   NaN
>>> # Default: Drop all rows with any missing values
>>> df.dropna()
     name   A     B     C     D
2    Jane  86  91.0  92.0  78.0
3  Ashley  98  99.0  79.0  60.0
>>> # Set axis = 1 to drop all columns with missing values
>>> df.dropna(axis=1)
     name   A
0  Oliver  71
1    John  63
2    Jane  86
3  Ashley  98
4   Steve  75
>>> # Set how keyword to drop only row/columns where all values are missing
>>> df['F']=np.nan
>>> df
     name   A     B     C     D   F
0  Oliver  71  83.0   NaN  88.0 NaN
1    John  63   NaN  87.0  77.0 NaN
2    Jane  86  91.0  92.0  78.0 NaN
3  Ashley  98  99.0  79.0  60.0 NaN
4   Steve  75  77.0  82.0   NaN NaN
>>> df.dropna(axis='columns', how='all')
     name   A     B     C     D
0  Oliver  71  83.0   NaN  88.0
1    John  63   NaN  87.0  77.0
2    Jane  86  91.0  92.0  78.0
3  Ashley  98  99.0  79.0  60.0
4   Steve  75  77.0  82.0   NaN
>>> # Set thresh keyword to specify the minimum number of non-missing values
>>> df['F'] = [79, np.nan, 88, np.nan, np.nan]
>>> df
     name   A     B     C     D     F
0  Oliver  71  83.0   NaN  88.0  79.0
1    John  63   NaN  87.0  77.0   NaN
2    Jane  86  91.0  92.0  78.0  88.0
3  Ashley  98  99.0  79.0  60.0   NaN
4   Steve  75  77.0  82.0   NaN   NaN
>>> df.dropna(axis='columns', thresh=3)
     name   A     B     C     D
0  Oliver  71  83.0   NaN  88.0
1    John  63   NaN  87.0  77.0
2    Jane  86  91.0  92.0  78.0
3  Ashley  98  99.0  79.0  60.0
4   Steve  75  77.0  82.0   NaN
```

#### Filling Null or Missing Values
`Series.fillna(value=None, method=None, axis=None, inplace=False, limit=None, downcast=None)`
`DataFrame.fillna(value=None, method=None, axis=None, inplace=False, limit=None, downcast=None)`
* value (scalar, dict, `Series`, `DataFrame`): the value used to replace missing values. Passing in a dict/`Series`/`DataFrame`
of values specifying which value to use for each index (for a `Series`), or column (for a `DataFrame`), values not in the dict/`Series`/`DataFrame` will
not be replaced
* method {'backfill', 'bfill', 'pad', 'ffill', None}: the method used for replacing missing values in reindexed series
  * pad/ffill: propagate the last valid observation forward to the next
  * backfill/bfill: uses the next valid observation to replace missing values
* axis: the axis to replace missing values
  * 0 or 'index' for a `Series`
  * 0/'index' or 1/'column' for a `DataFrame`
* inplace (bool): boolean value, if `True` the operation of removing missing values is done inplace and returns None
* limit (int): 
  * If method is specified, limit sets the maximum number of consecutive missing values to forward or backwards fill
  * If method is not specified, limit sets the maximum number of missing values along the entire axis that will be filled
* downcast (dict): a dictionary of item->dtype of what downcast or string 'infer' which will try to downcast to an appropriate equal type

```python
>>> # Example DataFrame
>>> df
     name   A     B     C     D     F
0  Oliver  71  83.0   NaN  88.0  79.0
1    John  63   NaN  87.0  77.0   NaN
2    Jane  86  91.0  92.0  78.0  88.0
3  Ashley  98  99.0  79.0  60.0   NaN
4   Steve  75  77.0  82.0   NaN   NaN
>>> # Basic fill methods
>>> df.fillna(value=0)
     name   A     B     C     D     F
0  Oliver  71  83.0   0.0  88.0  79.0
1    John  63   0.0  87.0  77.0   0.0
2    Jane  86  91.0  92.0  78.0  88.0
3  Ashley  98  99.0  79.0  60.0   0.0
4   Steve  75  77.0  82.0   0.0   0.0
>>> # Fill NaN using the backfill method - If the last value is NaN it will remain NaN
>>> df.fillna(method='bfill')
     name   A     B     C     D     F
0  Oliver  71  83.0  87.0  88.0  79.0
1    John  63  91.0  87.0  77.0  88.0
2    Jane  86  91.0  92.0  78.0  88.0
3  Ashley  98  99.0  79.0  60.0   NaN
4   Steve  75  77.0  82.0   NaN   NaN
>>> # Fill Nan using the forwardfill method - If the first value is NaN it will remain NaN
>>> df.fillna(method='ffill')
     name   A     B     C     D     F
0  Oliver  71  83.0   NaN  88.0  79.0
1    John  63  83.0  87.0  77.0  79.0
2    Jane  86  91.0  92.0  78.0  88.0
3  Ashley  98  99.0  79.0  60.0  88.0
4   Steve  75  77.0  82.0  60.0  88.0
>>> # Forward fill and set axis to rows (compare with previous example)
>>> df.fillna(method='ffill', axis=1)
     name   A     B     C     D     F
0  Oliver  71  83.0  83.0  88.0  79.0
1    John  63    63  87.0  77.0  77.0
2    Jane  86  91.0  92.0  78.0  88.0
3  Ashley  98  99.0  79.0  60.0  60.0
4   Steve  75  77.0  82.0  82.0  82.0
>>> # Set value to a dictionary of values (unique replacement value for each column)
>>> values = {'A':222, 'B':333, 'C':444, 'D':555, 'F':666}
>>> df.fillna(value=values)
     name   A      B      C      D      F
0  Oliver  71   83.0  444.0   88.0   79.0
1    John  63  333.0   87.0   77.0  666.0
2    Jane  86   91.0   92.0   78.0   88.0
3  Ashley  98   99.0   79.0   60.0  666.0
4   Steve  75   77.0   82.0  555.0  666.0
>>> # Set limit to 1 with no method to fill only the first missing value
>>> df.fillna(value = values, limit=1)
     name   A      B      C      D      F
0  Oliver  71   83.0  444.0   88.0   79.0
1    John  63  333.0   87.0   77.0  666.0
2    Jane  86   91.0   92.0   78.0   88.0
3  Ashley  98   99.0   79.0   60.0    NaN
4   Steve  75   77.0   82.0  555.0    NaN
```
<br>

---

<br>

## Transforming Data
<br>

### Sorting `DataFrame` Values
The two methods covered in this section, `.sort_values()` and `.sort_index()`, allow for efficient sorting of the Pandas `DataFrame` data structure.

`DataFrame.sort_values(by, axis=0, ascending=True, inplace=False, kind='quicksort', na_position='last', ignore_index='False', key=None)`
* by (string or list of strings): Name or list of names to sort by
  * 0 or 'index': `by` may contain index levels and/or column labels
  * 1 or 'columns': `by` may contain column levels and/or index labels
* axis(0/'index' or 1/'columns'): axis to be sorted
* ascending (bool or list of bools), default `True`: If `True` sort values in ascending order, if `False` sort in descending order
* inplace (bool): if `True` perform the operation in-place
* kind ('quicksort', 'mergesort', 'heapsort', 'stable'): sorting algorithm used (see [numpy.sort()](https://numpy.org/doc/stable/reference/generated/numpy.sort.html#numpy.sort){: .post__link} for details)
* na_position ('first' or 'last'): argument specifying where `NaNs` should appear
* ignore_index (bool): If `True` the result axis will be labeled 0, 1, ..., n-1
* key (callable): when passed in the key function is applied to the series values before sorting

```python
>>> # Example DataFrame
>>> df
     name   A     B     C     D     E     F
0  Oliver  71  83.0   NaN  88.0  89.0  79.0
1    John  63   NaN  87.0  77.0  77.0   NaN
2    Jane  86  91.0  92.0  78.0   NaN  88.0
3  Ashley  98  99.0  79.0  60.0  83.0   NaN
4   Steve  75  77.0  82.0   NaN  72.0   NaN
>>> # Default Sorting Options - Sort by Column A
>>> df.sort_values(by='A')
     name   A     B     C     D     E     F
1    John  63   NaN  87.0  77.0  77.0   NaN
0  Oliver  71  83.0   NaN  88.0  89.0  79.0
4   Steve  75  77.0  82.0   NaN  72.0   NaN
2    Jane  86  91.0  92.0  78.0   NaN  88.0
3  Ashley  98  99.0  79.0  60.0  83.0   NaN
>>> # Sort in descending order and ignore index
>>> df.sort_values(by='A', ascending=False, ignore_index=True)
     name   A     B     C     D     E     F
0  Ashley  98  99.0  79.0  60.0  83.0   NaN
1    Jane  86  91.0  92.0  78.0   NaN  88.0
2   Steve  75  77.0  82.0   NaN  72.0   NaN
3  Oliver  71  83.0   NaN  88.0  89.0  79.0
4    John  63   NaN  87.0  77.0  77.0   NaN
>>> # Sort by multiple columns - Sorting occurs in order listed
>>> # Sort ascending can be set for each item in list passed to by
>>> df.sort_values(by=['name', 'A'], ascending=[True, False], ignore_index=True)
     name   A     B     C     D     E     F
0  Ashley  98  99.0  79.0  60.0  83.0   NaN
1    Jane  86  91.0  92.0  78.0   NaN  88.0
2    John  63   NaN  87.0  77.0  77.0   NaN
3  Oliver  71  83.0   NaN  88.0  89.0  79.0
4   Steve  75  77.0  82.0   NaN  72.0   NaN
>>> # Add new column of string values - for key argument example
>>> df['G'] = ['a', 'B', 'C', 'd', 'E']
>>> df
     name   A     B     C     D     E     F  G
0  Oliver  71  83.0   NaN  88.0  89.0  79.0  a
1    John  63   NaN  87.0  77.0  77.0   NaN  B
2    Jane  86  91.0  92.0  78.0   NaN  88.0  C
3  Ashley  98  99.0  79.0  60.0  83.0   NaN  d
4   Steve  75  77.0  82.0   NaN  72.0   NaN  E
>>> df.sort_values(by='G')
     name   A     B     C     D     E     F  G
1    John  63   NaN  87.0  77.0  77.0   NaN  B
2    Jane  86  91.0  92.0  78.0   NaN  88.0  C
4   Steve  75  77.0  82.0   NaN  72.0   NaN  E
0  Oliver  71  83.0   NaN  88.0  89.0  79.0  a
3  Ashley  98  99.0  79.0  60.0  83.0   NaN  d
>>> # Sorting a DataFrame with a key function - function evaluated then column sorted
>>> df.sort_values(by='G', key=lambda col: col.str.lower())
     name   A     B     C     D     E     F  G
0  Oliver  71  83.0   NaN  88.0  89.0  79.0  a
1    John  63   NaN  87.0  77.0  77.0   NaN  B
2    Jane  86  91.0  92.0  78.0   NaN  88.0  C
3  Ashley  98  99.0  79.0  60.0  83.0   NaN  d
4   Steve  75  77.0  82.0   NaN  72.0   NaN  E
>>> # Default sorting with NaNs
>>> df.sort_values(by='F')
     name   A     B     C     D     E     F  G
0  Oliver  71  83.0   NaN  88.0  89.0  79.0  a
2    Jane  86  91.0  92.0  78.0   NaN  88.0  C
1    John  63   NaN  87.0  77.0  77.0   NaN  B
3  Ashley  98  99.0  79.0  60.0  83.0   NaN  d
4   Steve  75  77.0  82.0   NaN  72.0   NaN  E
>>> # Set the na_position argument
>>> df.sort_values(by='F', na_position='first')
     name   A     B     C     D     E     F  G
1    John  63   NaN  87.0  77.0  77.0   NaN  B
3  Ashley  98  99.0  79.0  60.0  83.0   NaN  d
4   Steve  75  77.0  82.0   NaN  72.0   NaN  E
0  Oliver  71  83.0   NaN  88.0  89.0  79.0  a
2    Jane  86  91.0  92.0  78.0   NaN  88.0  C
```
`DataFrame.sort_index(axis=0, level=None, ascending=True, inplace=False, kind='quicksort', na_position='last', sort_remaining=True, ignore_index='False', key=None)`
* axis(0/'index' or 1/'columns'): axis to be sorted
* level (int or level name or lists of ints or names): sort on values in specified index level(s) when not `None`
* ascending (bool or list of bools): If `True` sort values in ascending order, if `False` sort in descending order
* inplace (bool): if `True` perform the operation in-place
* kind ('quicksort', 'mergesort', 'heapsort', 'stable'): sorting algorithm used (see [numpy.sort()](https://numpy.org/doc/stable/reference/generated/numpy.sort.html#numpy.sort){: .post__link} for details)
* na_position ('first' or 'last'): argument specifying where `NaNs` should appear
* sort_remaining (boo): when `True` and sorting by level and index is multilevel, sort by other level as well, in order, after sorting by specified level
* ignore_index (bool): if `True` the result axis will be labeled 0, 1, ..., n-1
* key (callable): when passed in the key function is applied to the series values before sorting

```python
>>> # Set the name field to the DataFrame index
>>> df.set_index('name', inplace=True)
>>> df
         A     B     C     D     E     F  G
name                                       
Oliver  71  83.0   NaN  88.0  89.0  79.0  a
John    63   NaN  87.0  77.0  77.0   NaN  B
Jane    86  91.0  92.0  78.0   NaN  88.0  C
Ashley  98  99.0  79.0  60.0  83.0   NaN  d
Steve   75  77.0  82.0   NaN  72.0   NaN  E
>>> # Default sort_index
>>> df.sort_index()
         A     B     C     D     E     F  G
name                                       
Ashley  98  99.0  79.0  60.0  83.0   NaN  d
Jane    86  91.0  92.0  78.0   NaN  88.0  C
John    63   NaN  87.0  77.0  77.0   NaN  B
Oliver  71  83.0   NaN  88.0  89.0  79.0  a
Steve   75  77.0  82.0   NaN  72.0   NaN  E
>>> # Sort index in descending order
>>> df.sort_index(ascending=False)
         A     B     C     D     E     F  G
name                                       
Steve   75  77.0  82.0   NaN  72.0   NaN  E
Oliver  71  83.0   NaN  88.0  89.0  79.0  a
John    63   NaN  87.0  77.0  77.0   NaN  B
Jane    86  91.0  92.0  78.0   NaN  88.0  C
Ashley  98  99.0  79.0  60.0  83.0   NaN  d
>>> # Create and sort by hierarchial index
>>> df
     name  group   A     B     C     D     E     F  G
0  Oliver      1  71  83.0   NaN  88.0  89.0  79.0  a
1    John      1  63   NaN  87.0  77.0  77.0   NaN  B
2    Jane      2  86  91.0  92.0  78.0   NaN  88.0  C
3  Ashley      2  98  99.0  79.0  60.0  83.0   NaN  d
4   Steve      1  75  77.0  82.0   NaN  72.0   NaN  E
>>> df.set_index(['group', 'name'], inplace=True)
>>> df
               A     B     C     D     E     F  G
group name                                       
1     Oliver  71  83.0   NaN  88.0  89.0  79.0  a
      John    63   NaN  87.0  77.0  77.0   NaN  B
2     Jane    86  91.0  92.0  78.0   NaN  88.0  C
      Ashley  98  99.0  79.0  60.0  83.0   NaN  d
1     Steve   75  77.0  82.0   NaN  72.0   NaN  E
# Sort outer index with default sort_remaining=True
>>> df.sort_index(level='group')
               A     B     C     D     E     F  G
group name                                       
1     John    63   NaN  87.0  77.0  77.0   NaN  B
      Oliver  71  83.0   NaN  88.0  89.0  79.0  a
      Steve   75  77.0  82.0   NaN  72.0   NaN  E
2     Ashley  98  99.0  79.0  60.0  83.0   NaN  d
      Jane    86  91.0  92.0  78.0   NaN  88.0  C
>>> # Set argument sort_remaining=False - compare the order of names in each group with previous example
>>> df.sort_index(level='group', sort_remaining=False)
               A     B     C     D     E     F  G
group name                                       
1     Oliver  71  83.0   NaN  88.0  89.0  79.0  a
      John    63   NaN  87.0  77.0  77.0   NaN  B
      Steve   75  77.0  82.0   NaN  72.0   NaN  E
2     Jane    86  91.0  92.0  78.0   NaN  88.0  C
      Ashley  98  99.0  79.0  60.0  83.0   NaN  d
>>> # Sort by inner group
>>> df.sort_index(level='name')
               A     B     C     D     E     F  G
group name                                       
2     Ashley  98  99.0  79.0  60.0  83.0   NaN  d
      Jane    86  91.0  92.0  78.0   NaN  88.0  C
1     John    63   NaN  87.0  77.0  77.0   NaN  B
      Oliver  71  83.0   NaN  88.0  89.0  79.0  a
      Steve   75  77.0  82.0   NaN  72.0   NaN  E
>>> # Change the sort axis to column labels
>>> df.sort_index(axis=1, ascending=False)
              G     F     E     D     C     B   A
group name                                       
1     Oliver  a  79.0  89.0  88.0   NaN  83.0  71
      John    B   NaN  77.0  77.0  87.0   NaN  63
2     Jane    C  88.0   NaN  78.0  92.0  91.0  86
      Ashley  d   NaN  83.0  60.0  79.0  99.0  98
1     Steve   E   NaN  72.0   NaN  82.0  77.0  75

```

<br>

### Add Columns / Rows
#### Add Columns to a `DataFrame`
Pandas offers a number of methods for adding columns of data to a `DataFrame`. The values of the new column can be given 
as an array or list of the same size as the `DataFrame` and then assigned to the new column by providing the name.

This is a common approach when the new column can be added to the end of the `DataFrame` (i.e. the last column)
```python
>>> df['E'] = [89, 77, 96, 83, 72]
>>> df
     name   A     B     C     D     F   E
0  Oliver  71  83.0   NaN  88.0  79.0  89
1    John  63   NaN  87.0  77.0   NaN  77
2    Jane  86  91.0  92.0  78.0  88.0  96
3  Ashley  98  99.0  79.0  60.0   NaN  83
4   Steve  75  77.0  82.0   NaN   NaN  72
```
`DataFrame.insert(loc, column, value, allow_duplicates=False)`<br>
A method the provides the flexibility to add a column in any position as well as providing options for inserting the column values
* loc (int): the insertion index, must be greater than or equal to 0 and less than or equal to the length of the DataFrame
* column (string, number, or hashable object): label of the inserted column
* value (scalar, series, or array-like): the values to be inserted
* allow_duplicates (bool): boolean value indicating whether to allow duplicates or not

```python
>>> e_values = [89, 77, np.nan, 83, 72]
>>> df.insert(5, 'E', e_values)
>>> df
     name   A     B     C     D     E     F
0  Oliver  71  83.0   NaN  88.0  89.0  79.0
1    John  63   NaN  87.0  77.0  77.0   NaN
2    Jane  86  91.0  92.0  78.0   NaN  88.0
3  Ashley  98  99.0  79.0  60.0  83.0   NaN
4   Steve  75  77.0  82.0   NaN  72.0   NaN
```
`DataFrame.assign(**kwargs)`<br>
A method that assigns new columns to a `DataFrame` and returns a new object with the original columns in addition to the newly defined columns.
* `**kwargs` (dict of {str: callable or Series}): The column names are the key words of the dictionary
  * If values are callable, pandas will compute on the `DataFrame` and assign to the new columns

>Multiple columns can be assigned using `.assign()` and later items in `**kwargs` can reference previously created new columns (i.e. items are computed/assigned in order)

```python
>>> df.assign(mean=df.mean(axis=1))
     name   A     B     C     D     E     F  mean
0  Oliver  71  83.0   NaN  88.0  89.0  79.0  82.0
1    John  63   NaN  87.0  77.0  77.0   NaN  76.0
2    Jane  86  91.0  92.0  78.0   NaN  88.0  87.0
3  Ashley  98  99.0  79.0  60.0  83.0   NaN  83.8
4   Steve  75  77.0  82.0   NaN  72.0   NaN  76.5
>>> # Use assign to compute a new column that depends on a previously created new column
>>> df.assign(G=[65, 55, 56, 62, 68], g=lambda x: x['G'] + 10)
     name   A     B     C     D     E     F   G   g
0  Oliver  71  83.0   NaN  88.0  89.0  79.0  65  75
1    John  63   NaN  87.0  77.0  77.0   NaN  55  65
2    Jane  86  91.0  92.0  78.0   NaN  88.0  56  66
3  Ashley  98  99.0  79.0  60.0  83.0   NaN  62  72
4   Steve  75  77.0  82.0   NaN  72.0   NaN  68  78
```
`DataFrame.loc[]`<br>
A property used to access a group of rows and columns by label(s) or a boolean array.
```python
>>> df.loc[:, 'H'] = [92, np.nan, 88, 79, 90]
>>> df
     name   A     B     C     D     E     F   G   g     H
0  Oliver  71  83.0   NaN  88.0  89.0  79.0  65  75  92.0
1    John  63   NaN  87.0  77.0  77.0   NaN  55  65   NaN
2    Jane  86  91.0  92.0  78.0   NaN  88.0  56  66  88.0
3  Ashley  98  99.0  79.0  60.0  83.0   NaN  62  72  79.0
4   Steve  75  77.0  82.0   NaN  72.0   NaN  68  78  90.0
```

>For for more details and examples of selecting and subsetting a `DataFrame` see the article [DataFrame Subsetting - Pandas](/quick%20start/pandas-data-selection.html){: .post__link}

#### Add Rows to a `DataFrame`
`DataFrame.loc[]`<br>
Similar to above, `.loc[]` can also be utilized to add rows to a `DataFrame`
```python
>>> df.loc[5] = ['Aaron', 88, np.nan, 93, 72, 97, 85, 90, 78, 99]
>>> df
     name   A     B     C     D     E     F   G   g     H
0  Oliver  71  83.0   NaN  88.0  89.0  79.0  65  75  92.0
1    John  63   NaN  87.0  77.0  77.0   NaN  55  65   NaN
2    Jane  86  91.0  92.0  78.0   NaN  88.0  56  66  88.0
3  Ashley  98  99.0  79.0  60.0  83.0   NaN  62  72  79.0
4   Steve  75  77.0  82.0   NaN  72.0   NaN  68  78  90.0
5   Aaron  88   NaN  93.0  72.0  97.0  85.0  90  78  99.0
```
`pandas.concat(objs, axis=0, join='outer', ignore_index=False, keys=None, levels=None, names=None, verify_integrity=False, sort=False, copy=True)`

A function that provides the ability to concatenate pandas objects along a particular axis with optional set logic along the other axes
* objs (sequence or mapping of `Series` or `DataFrame` objects): If mapping is passed, the sorted keys will be used as the `keys` argument unless it is passed in.
* axis (0/'index' or 1/'columns'): the axis to concatenate along
* join ('inner', 'outer'): how to handle indexes on other axes
* ignore_index (bool): If `True` do not use the index value along the concatenation axis, typically helpful when the concatenation axis does not have meaningful indexing information
* keys (sequence): when multiples levels are passed in they should contain tuples, hierarchical indexes are constructed using the passed keys as the outermost level
* levels (list of sequences): unique values to use for constructing a multi-index
* names (list): names for the levels in the hierarchical index
* verify_integrity (bool): If `True` pandas will check whether the new concatenated axis contains duplicates
* sort (bool): sort non-concatenation axis if it is not aligned when join is 'outer'.
* copy (bool): If `False` data is not copied unnecessarily

>For more joining operations see [Joining DataFrames](#joining-dataframes){: .post__link}

```python
>>> # Example DataFrames
>>> df
     name   A     B     C     D     E     F
0  Oliver  71  83.0   NaN  88.0  89.0  79.0
1    John  63   NaN  87.0  77.0  77.0   NaN
2    Jane  86  91.0  92.0  78.0   NaN  88.0
3  Ashley  98  99.0  79.0  60.0  83.0   NaN
4   Steve  75  77.0  82.0   NaN  72.0   NaN
>>> df2
    name   A   B     C   D   E   F
0  Katie  92  91  99.0  87  79  79
1    Bob  88  81   NaN  74  78  78
>>> # Concat with default arguments, append df2 to df
>>> pd.concat([df, df2])
     name   A     B     C     D     E     F
0  Oliver  71  83.0   NaN  88.0  89.0  79.0
1    John  63   NaN  87.0  77.0  77.0   NaN
2    Jane  86  91.0  92.0  78.0   NaN  88.0
3  Ashley  98  99.0  79.0  60.0  83.0   NaN
4   Steve  75  77.0  82.0   NaN  72.0   NaN
0   Katie  92  91.0  99.0  87.0  79.0  79.0
1     Bob  88  81.0   NaN  74.0  78.0  78.0
>>> # Ignore Index when it has no meaningful information, labels will be 0, 1, .., n-1
>>> pd.concat([df, df2], ignore_index=True)
     name   A     B     C     D     E     F
0  Oliver  71  83.0   NaN  88.0  89.0  79.0
1    John  63   NaN  87.0  77.0  77.0   NaN
2    Jane  86  91.0  92.0  78.0   NaN  88.0
3  Ashley  98  99.0  79.0  60.0  83.0   NaN
4   Steve  75  77.0  82.0   NaN  72.0   NaN
5   Katie  92  91.0  99.0  87.0  79.0  79.0
6     Bob  88  81.0   NaN  74.0  78.0  78.0
>>> # Construct hierarchical index
>>> pd.concat([df, df2], keys=['One', 'Two'])
         name   A     B     C     D     E     F
One 0  Oliver  71  83.0   NaN  88.0  89.0  79.0
    1    John  63   NaN  87.0  77.0  77.0   NaN
    2    Jane  86  91.0  92.0  78.0   NaN  88.0
    3  Ashley  98  99.0  79.0  60.0  83.0   NaN
    4   Steve  75  77.0  82.0   NaN  72.0   NaN
Two 0   Katie  92  91.0  99.0  87.0  79.0  79.0
    1     Bob  88  81.0   NaN  74.0  78.0  78.0
>>> # Default 'outer' join - includes all columns in either DataFrame
>>> # Columns outside the intersection will be fill with NaN (Column G below)
>>> pd.concat([df, df2])
     name   A     B     C     D     E     F     G
0  Oliver  71  83.0   NaN  88.0  89.0  79.0   NaN
1    John  63   NaN  87.0  77.0  77.0   NaN   NaN
2    Jane  86  91.0  92.0  78.0   NaN  88.0   NaN
3  Ashley  98  99.0  79.0  60.0  83.0   NaN   NaN
4   Steve  75  77.0  82.0   NaN  72.0   NaN   NaN
0   Katie  92  91.0  99.0  87.0  79.0  79.0  84.0
1     Bob  88  81.0   NaN  74.0  78.0  78.0  69.0
>>> # Set join to 'inner', to return only columns shared by both objects
>>> pd.concat([df, df2], join='inner')
     name   A     B     C     D     E     F
0  Oliver  71  83.0   NaN  88.0  89.0  79.0
1    John  63   NaN  87.0  77.0  77.0   NaN
2    Jane  86  91.0  92.0  78.0   NaN  88.0
3  Ashley  98  99.0  79.0  60.0  83.0   NaN
4   Steve  75  77.0  82.0   NaN  72.0   NaN
0   Katie  92  91.0  99.0  87.0  79.0  79.0
1     Bob  88  81.0   NaN  74.0  78.0  78.0
```
`.concat()` can also be used to add columns to a `DataFrame` by changing the axis parameter
```python
>>> # Horizontally combine objects axis=1 (Add columns)
>>> G=[98, 88, 82, 76, 88]
>>> df3 = pd.DataFrame({'G':G})
>>> pd.concat([df, df3], axis=1)
     name   A     B     C     D     E     F   G
0  Oliver  71  83.0   NaN  88.0  89.0  79.0  98
1    John  63   NaN  87.0  77.0  77.0   NaN  88
2    Jane  86  91.0  92.0  78.0   NaN  88.0  82
3  Ashley  98  99.0  79.0  60.0  83.0   NaN  76
4   Steve  75  77.0  82.0   NaN  72.0   NaN  88
```
### Remove Columns / Rows
`DataFrame.drop(labels=None, axis=0, index=None, columns=None, level=None, inplace=False, errors='raise')`
`.drop()` is a method which removes rows or columns by providing label names and the corresponding axis or by specifying the index or column names.
* labels (single label or list-like): the index or column label to drop
* axis (0/'index' or 1/'column'): drop labels from teh index or columns
* index (single label or list-like): can be used as an alternative to specifying the axis parameter
  * `.drop(labels, axis=0)` is equivalent to `.drop(index=labels)`
* columns (single label or list-list): can be used as an alternative to specifying the axis parameter
  * `.drop(labels, axis=1)` is equivalent to `.drop(columns=labels)`
* level (int or level name): the level from which the labels will be removed
* inplace (bool): if `True` the drop operation occurs in place `.drop()` returns `None` otherwise a copy of the `DataFrame` is returned
* errors ('ignore', 'raise'): if 'ignore' errors will be suppressed and only existing labels are dropped from the `DataFrame`
```python
>>> # Example DataFrame
>>> df
   grouping    name   A     B     C     D     E
0         1  Ashley  88  94.0  75.0  82.0  95.0
1         1    John  63   NaN  87.0  77.0  77.0
2         1  Oliver  71  83.0   NaN  88.0  89.0
3         2   Aaron  88   NaN  93.0  72.0  97.0
4         2    Jane  86  91.0  92.0  78.0   NaN
5         2   Steve  75  77.0  82.0   NaN  72.0
>>> # Drop Columns
>>> df.drop(labels=['B', 'E'], axis=1)
   grouping    name   A     C     D
0         1  Ashley  88  75.0  82.0
1         1    John  63  87.0  77.0
2         1  Oliver  71   NaN  88.0
3         2   Aaron  88  93.0  72.0
4         2    Jane  86  92.0  78.0
5         2   Steve  75  82.0   NaN
>>> df.drop(columns=['B', 'E'])
   grouping    name   A     C     D
0         1  Ashley  88  75.0  82.0
1         1    John  63  87.0  77.0
2         1  Oliver  71   NaN  88.0
3         2   Aaron  88  93.0  72.0
4         2    Jane  86  92.0  78.0
5         2   Steve  75  82.0   NaN
>>> # Drop Rows by Index
>>> df.drop([0, 3])
   grouping    name   A     B     C     D     E
1         1    John  63   NaN  87.0  77.0  77.0
2         1  Oliver  71  83.0   NaN  88.0  89.0
4         2    Jane  86  91.0  92.0  78.0   NaN
5         2   Steve  75  77.0  82.0   NaN  72.0
>>> # Drop columns/rows for MultiIndex DataFrame
>>> df.set_index(['grouping', 'name'], inplace=True)
>>> df
                  A     B     C     D     E
grouping name                              
1        Ashley  88  94.0  75.0  82.0  95.0
         John    63   NaN  87.0  77.0  77.0
         Oliver  71  83.0   NaN  88.0  89.0
2        Aaron   88   NaN  93.0  72.0  97.0
         Jane    86  91.0  92.0  78.0   NaN
         Steve   75  77.0  82.0   NaN  72.0
>>> # Drop a index combination
>>> df.drop(index=(1, 'John'))
                  A     B     C     D     E
grouping name                              
1        Ashley  88  94.0  75.0  82.0  95.0
         Oliver  71  83.0   NaN  88.0  89.0
2        Aaron   88   NaN  93.0  72.0  97.0
         Jane    86  91.0  92.0  78.0   NaN
         Steve   75  77.0  82.0   NaN  72.0
>>> df.drop(index=1, columns='B')
                 A     C     D     E
grouping name                       
2        Aaron  88  93.0  72.0  97.0
         Jane   86  92.0  78.0   NaN
         Steve  75  82.0   NaN  72.0
```

---

<br>

## Aggregating DataFrames
An essential step to gaining insights into a dataset is the ability to effectively summarize the data. Pandas offers a variety of methods ranging from simple calculations (e.g. `.sum()`) to more complex operations using `groupby`
### Descriptive Statistics
Descriptive statistics are operations that summarize the central tendency, dispersion, and shape of a dataset

Basic summary methods include `.count()`, `.sum()`, `.mode()`, `.min()`, `max()`, `.var()`, `.std()`, `.mean()`, `.quantile()`, `.median()`

`DataFrame.sum()`<br>
`DataFrame.mean()`<br>
`DataFrame.median()`<br>
`DataFrame.min()`<br>
`DataFrame.max()`<br>
A few general parameters explored here include
* axis(0, 1): specify the axis (index or columns) the function should be applied on
* skipna (bool): if `True` exclude null values when computing
* level (int or level name): if the axis is hierarchical, count along a specified level,  collapsing into a scalar
* numeric_only (bool): includes only float, int, and boolean columns, when `None` (default) pandas attempts to use everything then only numeric data
* `**kwargs`: any additional keyword arguments
* min_count (int): specify the required number of valid value needed to perform the operation
  * parameter for `.sum()`

```python
>>> # Example DataFrame
>>> df
     name   A     B     C     D     E
0  Ashley  88  94.0  75.0  82.0  95.0
1    John  63   NaN  87.0  77.0  77.0
2  Oliver  71  83.0   NaN  88.0  89.0
3   Aaron  88   NaN  93.0  72.0  97.0
4    Jane  86  91.0  92.0  78.0   NaN
5   Steve  75  77.0  82.0   NaN  72.0
>>> df.mean()
A    78.50
B    86.25
C    85.80
D    79.40
E    86.00
dtype: float64
>>> # Set the axis parameter to 1 to operate on the rows of the DataFrame
>>> df.mean(axis=1)
0    86.80
1    76.00
2    82.75
3    87.50
4    86.75
5    76.50
>>> df.min()
name    Aaron
A          63
B        77.0
C        75.0
D        72.0
E        72.0
dtype: object
>>> # Set the numeric_only parameter to exclude the name column from the operation
>>> df.min(numeric_only=True)
A    63.0
B    77.0
C    75.0
D    72.0
E    72.0
dtype: float64
>>> df.sum(numeric_only=True)
A    471.0
B    345.0
C    429.0
D    397.0
E    430.0
dtype: float64
>>> # Set the min_count parameter to only perform the operation
>>> # when there is at least the specified number of values
>>> df.sum(min_count=5, numeric_only=True)
A    471.0
B      NaN
C    429.0
D    397.0
E    430.0
dtype: float64
```

#### Method: .describe()
A helpful method for generating descriptive statistics is `.describe()`<br>
`DataFrame.describe(percentiles=None, include=None, exclude=None, datatime_is_numeric=False)`
* percentiles (list-like): the percentiles to include in the output, and should be between 0 and 1, default = [.25, .5, .75]
* include ('all', list-like, or None): list of data types to include in the result
* exclude (list-like of dtypes or None): data types to omit from the result
* datetime_is_numeric (bool): boolean value indicating whether datetime data types should be treated as numeric

```python
>>> df.describe()
               A          B          C         D          E
count   6.000000   4.000000   5.000000   5.00000   5.000000
mean   78.500000  86.250000  85.800000  79.40000  86.000000
std    10.445095   7.719024   7.463243   5.98331  11.045361
min    63.000000  77.000000  75.000000  72.00000  72.000000
25%    72.000000  81.500000  82.000000  77.00000  77.000000
50%    80.500000  87.000000  87.000000  78.00000  89.000000
75%    87.500000  91.750000  92.000000  82.00000  95.000000
max    88.000000  94.000000  93.000000  88.00000  97.000000
```

#### Method: .agg()
The `.agg()` (alias of `.aggregate()`) method aggregates values using one or more operations over a specified axis.<br>
`DataFrame.agg(func=None, axis=0, *args, **kwargs)`
* func (function, str, list, or dict): the function(s) to be used for aggregating the data
* axis (0/'index', 1/'columns'): the axis the operation is performed on
  * 'index': the function is applied to each column
  * 'columns': the function is applied to each row
* `*args`: positional arguments passed to `func`
* `**kwargs`: keyword arguments passed to `func`
```python
>>> # Remove the name column and calculate column sum, mean, and max
>>> df.drop('name',axis=1).agg(['sum', 'mean', 'max'])
          A       B      C      D      E
sum   471.0  345.00  429.0  397.0  430.0
mean   78.5   86.25   85.8   79.4   86.0
max    88.0   94.00   93.0   88.0   97.0
```

Pandas offers many other computation and descriptive statistic methods. For more information and example see [Exploratory Data Analysis - Pandas](/quick%20start/pandas-eda.html){: .post__link} or the [Pandas Documentation](https://pandas.pydata.org/docs/reference/frame.html#computations-descriptive-stats)
### Grouped Summary Statistics
The basic descriptive statistics can provide quick insights into the dataset, however to to conditionally aggregate by a label or index requires the use of the `groupby` operation.

The `groupby` operation can be though of as consisting of three stages:
* Split: the breaking up and grouping of the `DataFrame`
* Apply: computing the desired operation(s) for each group
* Combine: merge the results of the of the operation(s)

When using the `.groupby()` method these steps are not explicit providing relief from considering *how* the computation is done and focusing on the operation as a whole 

<br>
`DataFrame.groupby(by=None, axis=0, level=None, as_index=True, sort=True, group_key=True, squeeze=NoDefault.no_default, observed=False, dropna=True)`

Parameters explored here include:
* by (mapping, function, label, or list of labels): determines the grouping
* level (int, level name, sequence of level names): specify a particular level(s) to group by if the axis hierarchical  
* dropna (bool): when `True` missing values will be dropped, when `False` missing values will be treated as the key in groups

```python
>>> # Example DataFrame
>>> df
    A     B     C     D     E class
0  88  94.0  75.0  82.0  95.0   MWF
1  63   NaN  87.0  77.0  77.0   MWF
2  71  83.0   NaN  88.0  89.0   TTH
3  88   NaN  93.0  72.0  97.0   MWF
4  86  91.0  92.0  78.0   NaN   TTH
5  75  77.0  82.0   NaN  72.0   MWF
>>> # Group by Column Value
>>> df.groupby(['class']).mean()
          A     B      C     D      E
class                                
MWF    78.5  85.5  84.25  77.0  85.25
TTH    78.5  87.0  92.00  83.0  89.00
>>> # Group by hierarchical index
>>> # Add second level column and set index
>>> df['time'] = ['M', 'N', 'N', 'M', 'M', 'N']
>>> df.groupby(level='time').mean()
              A     B          C          D          E
time                                                  
M     87.333333  92.5  86.666667  77.333333  96.000000
N     69.666667  80.0  84.500000  82.500000  79.333333
>>> df.groupby(level=0).mean()
          A     B      C     D      E
class                                
MWF    78.5  85.5  84.25  77.0  85.25
TTH    78.5  87.0  92.00  83.0  89.00
>>> # Handling missing values in the by column
>>> df['class']=['MWF', None, None, 'MWF', 'TTH', 'MWF']
>>> df
  class time   A     B     C     D     E
0   MWF    M  88  94.0  75.0  82.0  95.0
1  None    N  63   NaN  87.0  77.0  77.0
2  None    N  71  83.0   NaN  88.0  89.0
3   MWF    M  88   NaN  93.0  72.0  97.0
4   TTH    M  86  91.0  92.0  78.0   NaN
5   MWF    N  75  77.0  82.0   NaN  72.0
>>> df.groupby(by='class').mean()
               A     B          C     D     E
class                                        
MWF    83.666667  85.5  83.333333  77.0  88.0
TTH    86.000000  91.0  92.000000  78.0   NaN
>>> df.groupby(by='class', dropna=False).mean()
               A     B          C     D     E
class                                        
MWF    83.666667  85.5  83.333333  77.0  88.0
TTH    86.000000  91.0  92.000000  78.0   NaN
NaN    67.000000  83.0  87.000000  82.5  83.0
```
### Pivot Table
Similar to `.groupby` the `.pivot_table()` method provide a a multidimensional summary of the dataset. While `.groupby` splits/combines data across a 1D index, `pivot_table` splits/combines the data across a 2D grid. <br>

`pandas.pivot_table(values=None, index=None, columns=None, aggfunc='mean', fill_value=None, margins=False, dropna=True, margins_name='All', observed=False, sort=True)`

Parameters explored here include:
* values: the columns to aggregate - the column to summarize
* index (column, Grouper, array, or a list of them): what to group by columns. If an array, it must be the same length as the `DataFrame`
* columns (column, Grouper, array, or list of them): If an array, it must be the same length as the `DataFrame`
* aggfunc (function, list of functions, dict): if a list of functions the pivot table will contain a hierarchical column whose top level will be the function names, if a dict is passed the key is the column to summarize and value is the function or list of functions
* fill_value (scalar): specify a value to replace missing values in the resulting pivot table

```python
>>> # Example DataFrame
>>> df
  class time   A     B     C     D     E    group
0   MWF    M  88  94.0  75.0  82.0  95.0   group1
1  None    N  63   NaN  87.0  77.0  77.0   group2
2  None    N  71  83.0   NaN  88.0  89.0   group1
3   MWF    M  88   NaN  93.0  72.0  97.0  groups2
4   TTH    M  86  91.0  92.0  78.0   NaN   group2
5   MWF    N  75  77.0  82.0   NaN  72.0   group2
>>> df.pivot_table(values='A', index=['class'])
               A
class           
MWF    83.666667
TTH    86.000000
>>> # Pass a list to the index parameter
>>> df.pivot_table(values='A', index=['class', 'time'])
             A
class time    
MWF   M     88
      N     75
TTH   M     86
TTH   M     86
>>> # Include the summarization of multiple columns
>>> df.pivot_table(values=['A', 'B'], index=['class', 'time'])
             A     B
class time          
MWF   M     88  94.0
      N     75  77.0
TTH   M     86  91.0
>>> # Pass a list of functions to aggfunc
>>> df.pivot_table(values=['A', 'B'], index=['class', 'time'], aggfunc=[np.min, np.max])
           amin       amax      
              A     B    A     B
class time                      
MWF   M      88  94.0   88  94.0
      N      75  77.0   75  77.0
TTH   M      86  91.0   86  91.0
>>> # Pivot on two variables - provide a column name to column
>>> df.pivot_table(values=['A', 'B'], index=['class', 'time'], aggfunc=[np.min, np.max], columns='group')
             amin                        ...   amax                      
                A                     B  ...      A              B       
group      group1 group2 groups2 group1  ... group2 groups2 group1 group2
class time                               ...                             
MWF   M      88.0    NaN    88.0   94.0  ...    NaN    88.0   94.0    NaN
      N       NaN   75.0     NaN    NaN  ...   75.0     NaN    NaN   77.0
TTH   M       NaN   86.0     NaN    NaN  ...   86.0     NaN    NaN   91.0

[3 rows x 10 columns]
>>> # Specify a fill value for NaN in pivot table
>>> df.pivot_table(values=['A', 'B'], index=['class', 'time'], aggfunc=[np.min, np.max], columns='group', fill_value=0.0)
             amin                        ...    amax                      
                A                     B  ...       A      B               
group      group1 group2 groups2 group1  ... groups2 group1 group2 groups2
class time                               ...                              
MWF   M        88      0      88     94  ...      88     94      0       0
      N         0     75       0      0  ...       0      0     77       0
TTH   M         0     86       0      0  ...       0      0     91       0

[3 rows x 12 columns]
```

<br>

---

<br>

## Joining DataFrames
Coming Soon...