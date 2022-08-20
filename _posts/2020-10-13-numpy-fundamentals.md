---
layout: post
permalink: /:categories/:title
title:  Numpy Fundamentals
date:   2021-10-13 16:41:02 -0500
category: Quick Start
tags: Python Numpy
description: Overview of the numpy python library.
image: /assets/img/post_img/library.jpg
image_by: Jaredd Craig
image_by_link: https://unsplash.com/es/@jareddc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---

## Numpy Arrays
*Similar to a List with the added ability to perform calculations over entire arrays*
```python
import numpy as np
```
* `np.array()`
  * Input: list object
  * Allows for element wise calculations (vector arithmetic)
  * Can perform calculations readily because there is an assumption that all elements are of the same type (e.g. an array of integers, or an array of strings)
  * Subsetting
    * Get Elements from an Array
      * Square brackets `array_name[index]`
      * Returns the value(s) specified by `index`
    * Get all elements that meet a condition
      * `array_name condition(>25)`
        * Returns a boolean array which can then be used with in square brackets
    ```python
    >>> arr1 = np.array([21, 20, 25, 24, 23])
    >>> arr1[1]
    20
    >>> arr1 > 23
    array([False, False,  True,  True, False])
    >>>
    >>> arr1[arr1 > 23]
    array([25, 24])
    ```

## Numpy 2D Arrays
`np.ndarray`
* An improved list of lists
  * Easily perform calculations
  * More advanced subsetting
  `arrary[row, col]`


## Basic Statistics
*Numpy provides basic statistic methods (e.g. `.mean()`, `median()`) which can be more efficient than build in python function since a numpy arrays consist of only a single data type*
```python
#[row, column]
np.mean(<array_name>[:, :])
#All rows 1 column of the array
np.mean(<array_name>[:, 0])
```
* Basic Statistics Methods
  * `np.medain()`
  * `np.corrcoef()`
  * `np.std()`
  * `np.sum()`
 * `np.sort()`
* Generate Data with Numpy
  * `np.random.normal()`: generates data from a random normal distribution
