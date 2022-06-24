---
layout: post
author: Ethan Guyant
title:  Data Manipulation - Pandas
categories: Python
description: An overview of various data manipulation functions and methods utilizing pandas dataframes.
---
### Contents   
[Transforming DataFrames](#transforming-dataframes)     
[Slicing and Indexing DataFrames](#slicing-and-indexing-dataframes)     

---

<br>

### Sorting (`.sort_values`)
* The sorting order is controlled by the `ascending` key word argument and is set to `ascending=True` by default, pass in `ascending=False` to sort in a descending order
```python
>>> df.sort_values('Col C')
  Col A  Col B  Col C
5     C      6    5.0
4     B      5    6.0
2     A      3    7.0
1     A      2    8.0
0     A      1    9.0
3     B      4    NaN
>>> df.sort_values('Col A', ascending=False)
  Col A  Col B  Col C
5     C      6    5.0
3     B      4    NaN
4     B      5    6.0
0     A      1    9.0
1     A      2    8.0
2     A      3    7.0
```
* The Dataframe can be sorted by multiple columns by passing a list `[ ]` of columns to sort by in the desired sorting order
  * Each column can be sorted differently by passing a list of `True` or `False` to the ascending keyword argument
```python
>>> df.sort_values(['Col A', 'Col C'], ascending=[False, True])
  Col A  Col B  Col C
5     C      6    5.0
4     B      5    6.0
3     B      4    NaN
2     A      3    7.0
1     A      2    8.0
0     A      1    9.0
```
* Position of any `NaN` values in the column utilized for sorting is controlled by the keyword argument `na_position` (default=last)
```python
>>> df.sort_values('Col C', na_position='first')
  Col A  Col B  Col C
3     B      4    NaN
5     C      6    5.0
4     B      5    6.0
2     A      3    7.0
1     A      2    8.0
0     A      1    9.0
```
* It can be seen in the above example that the returned result from `.sort_value()` maintain there initial index values. This behavior can be changed by using the `ignore_index` keyword argument (default: `ignore_index=False`)
  * Setting `ignore_index=True` will result in the returned axis being labeled 0,1, .., n-1
```python
>>> df.sort_values('Col A', ascending=False, ignore_index=True)
  Col A  Col B  Col C
0     C      6    5.0
1     B      4    NaN
2     B      5    6.0
3     A      1    9.0
4     A      2    8.0
5     A      3    7.0
```
* Also it is important to note that `df.sort_values(....)` does not hold or maintain the sorted values in `df`
```python
>>> df.sort_values('Col A', ascending=False, ignore_index=True)
  Col A  Col B  Col C
0     C      6    5.0
1     B      4    NaN
2     B      5    6.0
3     A      1    9.0
4     A      2    8.0
5     A      3    7.0
>>> df
  Col A  Col B  Col C
0     A      1    9.0
1     A      2    8.0
2     A      3    7.0
3     B      4    NaN
4     B      5    6.0
5     C      6    5.0
```
* To modify this behavior `df.sort_values(...)` can be assigned to another variable or the `inplace` keyword argument can be set to `True`
```python
>>> df2 = df.sort_values('Col A', ascending=False, ignore_index=True)
>>> df2
  Col A  Col B  Col C
0     C      6    5.0
1     B      4    NaN
2     B      5    6.0
3     A      1    9.0
4     A      2    8.0
5     A      3    7.0
>>> df.sort_values('Col A', ascending=False, ignore_index=True, inplace=True)
>>> df
  Col A  Col B  Col C
0     C      6    5.0
1     B      4    NaN
2     B      5    6.0
3     A      1    9.0
4     A      2    8.0
5     A      3    7.0
```
### Subsetting Columns
* Selection of a single column within a DataFrame can be done by using the name of the DataFrame followed by square brackets (`[]`) encompassing the name of the column
```python
>>> df['Col A']
0    C
1    B
2    B
3    A
4    A
5    A
Name: Col A, dtype: object
```
* The selection of multiple columns request two sets of square brackets (`[[]]`)
  * Each set of brackets performs a separate tak
   * The outer bracket subsets the DataFrame
   * The inner bracket creates a lits of column names to subset
```python
>>> df[columns]
  Col A  Col C
0     C    5.0
1     B    NaN
2     B    6.0
3     A    9.0
4     A    8.0
5     A    7.0
>>> df[['Col A', 'Col C']]
  Col A  Col C
0     C    5.0
1     B    NaN
2     B    6.0
3     A    9.0
4     A    8.0
5     A    7.0
```
>Using two sets of square brackets is equivalent to providing a separate list of column names as a variable and using it to subset the DataFrame
### Subsetting Rows
* A common method to subset rows is to apply a logical condition to filter against
  * The logical condition returns `True` or `False` values for each row of the DataFrame
  * Enclosing the logical condition in square brackets `[]` subsets the rows of the DataFrame, remaining rows are where the logical conditions evaluates to `True`
```python
>>> df['Col B'] >=3
0     True
1     True
2     True
3    False
4    False
5     True
Name: Col B, dtype: bool
>>> df[df['Col B'] >= 3]
  Col A  Col B  Col C
0     C      6    5.0
1     B      4    NaN
2     B      5    6.0
5     A      3    7.0
```
---

<br>

## Aggregating DataFrames


---

<br>

## Slicing and Indexing DataFrames


---

<br>

## Joining DataFrames


---

<br>

## Visualizing DataFrames
