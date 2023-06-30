---
layout: post
author: Ethan Guyant
title:  "Power BI Row Context: Understanding the Power of Context in Calculations"
category: Deep Dive
tags: [Microsoft 365, Power BI, Power Platform, Power BI Fundamentals Series]
description: Explore the concept of context within Power BI. What is evaluation context, row context, and filter context. This part one of a series will dive into Power BI's row context to better understand how and when it is invoked, and the implications of it.
image: /assets/img/post_img/powerbi_fundamentals_part1.jpg
image_by: Florian Müller
image_by_link: https://unsplash.com/@flo93m?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---
## Contents
* [Understanding Row Context](#understanding-row-context)
* [Creating a Calculated Column](#creating-a-calculated-column)
* [Limitation](#limitation)
  * [Replacing a Calculated Column with a Measure](#replacing-a-calculated-column-with-a-measure)

<br>

---

<br>

In order to facilitate analysis and visualization in Power BI, a data model must first be created. The data model will consist of individual data tables, relationships between the tables, and calculations to aid in the analysis. Calculations typically come in the form of either calculated columns or measures.

A few key differences between calculated columns and measures include:

| Calculated Columns | Measures | 
| :----------------: | :------: |
| Resolves as a scalar value | Resolves as an aggregate value |
| Persists in each row of a table | Displays as a data point |
| Increases the size of the data model | Does not impact the size of the data model |

When a DAX expression is evaluated the values the expression can access are limited by the evaluation context. When a calculated column or measure is evaluated there are two fundamental types of evaluation context: (1) Filter Context, and (2) Row Context. 

This post is the first of a series focused on the key fundamentals of Power BI. This first post will focus on the concept of row context. Parts 2-4 in the series will focus on iterator functions, filter context, and context transition.

<br>

---

<br>

## Understanding Row Context
The row context limits a DAX expression during evaluation to only the current row, the reference to each specific row is defined by the row context. However, it should be noted that all standard aggregation functions (e.g. `SUM()`, `COUNT()`, `MAXIMUM()`, `MINIMUM()`) will override this default behavior and impose a filter context on the entire table.

Row context exists when:
* Creating calculated columns within a table
* Creating iterators, see Part 2 for more details on iterator functions

Using Excel as an example, multiplying the values in two cells can be achieved with the formula `=A2 * B2`. The key difference is that when performing calculations in Excel the formula contains a reference to a *cell* defined by column `A` and row `2` however, in DAX there is no reference to a cell. Rather a calculation is carried out column by column `[A] * [B]`. DAX operates on columns and tables and it is the row context that provides the required row information for the calculation to be carried out.

The above multiplication `[A] * [B]` performs the desired calculation row-by-row through the entire table. This row-by-row functionality makes the row context similar to an iterator. 

>The row context means iterating over the table or for each row the calculation being conducted is limited to just the current row.

See Part 2 for more details on iterator functions.

<br>

---

<br>

## Creating a Calculated Column
As mentioned above, row context is invoked while creating a new calculated column. As an example, a new `SalesAmount` calculated column will be added to a SalesOrderDetail table. The example data and Power BI file can be found here (<a class="social-list__link" href="https://github.com/EMGuyant/power-bi-key-fundamentals"><i class="fab fa-github"></i> GitHub</a>).

`SalesAmount = SalesOrderDetail[OrderQty] * SalesOrderDetail[UnitPrice] * (1 - SalesOrderDetail[UnitPriceDiscount])`

While entering the above formula, after starting to type the column names in the formula bar (e.g. `OrderQty`, `UnitPrice`, and `UnitPriceDiscount`) it can be seen that IntelliSense can recognize and reference the corresponding columns in the `SalesOrderDetail` table (e.g. `SalesOrderDetail[OrderQty]`) 

![Creating a calculated column gif](/assets/img/2022-09-30-power-bi-row-context/row_context_001.gif){: .post__img}

Within the `SalesAmount` expression, only the column names are specified, however, since this is creating a calculated column the row context is available. This means that during the evaluation of the expression `SalesOrderDetail[OrderQty]` does not reference the entire column but rather the `OrderQty` value for the specific row within the scope of the calculation. This allows the new `SalesAmount` column to use the correct values when it is calculated row-by-row. 

Viewing the new `SalesAmount` column shows that the values are calculated for each row in the table. This is because row context means to iterate over each row of the table and perform the calculation for the specific row that is in the current scope.

![Table of NetSaleAmount Values](/assets/img/2022-09-30-power-bi-row-context/calculated_column_salescolumn.png){: .post__img}

The new calculated column can then be utilized in creating a new measure, `TotalSalesAmount`

`TotalSalesAmount = SUM(SalesOrderDetail[SalesAmount])`

![New measure using SalesAmount](/assets/img/2022-09-30-power-bi-row-context/total_measures.gif){: .post__img}

This new measure can then be included in visuals to summarize the sales data, for example, visualize the total sales by product color or by product name.

![Total Sales and Sum of SalesAmount](/assets/img/2022-09-30-power-bi-row-context/total_sales_measure.png){: .post__img}

The tables above show that the new `TotalSalesAmount` measure for each product color/name and the sum of the `SalesAmount` calculated column are equal. This highlights that the new measure and the default aggregation (e.g. sum) will produce the same values.

>Although a default aggregation of `SalesAmount` can be used to produce the same values as the measure `TotalSalesAmount` it is best practice to use a measure for aggregation and not use default aggregations of a column.

<br>

---

<br>

## Limitation
The method of adding a new calculated column is beneficial for aiding in understanding the impacts and use of the row context. However, a limitation to the approach of creating a column similar to `SalesAmount` is that each column created in a data model table increases the data model size because calculated columns are created and stored as columns in the data model when loaded (i.e. the .pbix file is opened or data is refreshed). Due to this, a column like `SalesAmount` normally would not be created in the data model, rather a measure can be used which does not increase the size of the overall data model. Measures do not increase the size of the data model because they are calculated in real-time, that is when the measure is used in a visual (table or chart).

>Best practice is to use measures whenever possible.

There are certain situations where a measure cannot be used and a calculated column is required. For example, a calculated column is required when the resulting value is to be used as an axis on a visual.

### Replacing a Calculated Column with a Measure
Replacing the `SalesAmount` calculated column with a measure may seem straightforward. Looking at the two formulas below, it is not unreasonable to think that the reference to `SalesOrderDetail[SalesAmount]` within the `TotalSalesAmount` measure could simply be replaced with `SalesOrderDetail[OrderQty] * SalesOrderDetail[UnitPrice] * (1 - SalesOrderDetail[UnitPriceDiscount])` (the formula of the `SalesAmount` calculated column).

`SalesAmount = SalesOrderDetail[OrderQty] * SalesOrderDetail[UnitPrice] * (1 - SalesOrderDetail[UnitPriceDiscount])`
`TotalSalesAmount = SUM(SalesOrderDetail[SalesAmount])`

However, if you create a new measure (e.g. `TotalSalesAmount2`) and start typing `OrderQty` IntelliSense will not provide it as an option to select as it did previously when creating the calculated column.

![TotalSales2 new measure](/assets/img/2022-09-30-power-bi-row-context/measure_totalsales2.gif){: .post__img}

The table columns are not available because by default a measure *does not* have row context. Without the row context Power BI does not know which specific `OrderQty` value should be referenced. For creating a measure that requires row context an iterator function will have to be used.

An iterator function can retrieve values from other columns in a table based on the row context, then perform an operation on the column values row-by-row, and finally aggregate the results.

Check out [Power BI Iterators: Unleashing the Power of Iteration in Power BI Calculations](https://ethanguyant.com/blog/2022-10-11-power-bi-iterators/){: .post__link} for an exploration of iterators.

<br>

---

<br>

If you enjoy what you read and find it helpful please check back, and check back often. Follow me on <a class="post__link" href="https://medium.com/@emguyant"><i class="fab fa-medium"></i> Medium</a> while giving a clap to the article! Also don't forget to subscribe to the <a class="post__link" href="https://medium.com/inquisitive-nature"><b>INQUISITIVE NATURE</b></a> publication.