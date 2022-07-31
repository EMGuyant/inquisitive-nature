---
layout: post
author: Ethan Guyant
title:  Data Subsetting - Pandas
category: Quick Start
tags: Python Pandas
description: An overview of various data manipulation functions and methods utilizing pandas dataframes.
image: /assets/img/post_img/subsetting-pandas.jpg
image_by: Brands&People
image_by_link: https://unsplash.com/@brandsandpeople?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---
### Contents   
[Transforming DataFrames](#transforming-dataframes)     
[Slicing and Indexing DataFrames](#slicing-and-indexing-dataframes)     

---

<br>

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

## Slicing and Indexing DataFrames
Coming Soon...

---


