---
layout: post
author: Ethan Guyant
title:  "Power BI - Key Differences Between Data Connectivity Modes"
category: Quick Start
tags: [Microsoft 365, Power BI, Power Platform, Power BI Fundamentals Series]
description: A key pillar to every Power BI project is the underlying data-model. Power BI uses Import, DirectQuery, or Live Connection connectivity modes to bring data from a wide variety of data sources into the data-model. This post explores these connectivity modes, their use cases, and their limitations.
image: /assets/img/post_img/power-bi-connectivity-modes.jpg
image_by: Etienne Girardet
image_by_link: https://unsplash.com/@etiennegirardet?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---

## Introduction

<br>

Power BI is a data analysis and reporting tool that connects to and consumes data from a wide variety of data sources. Once connected to data sources it provides a power tool for data modeling, data visualization, and report sharing.

All data analysis projects start with first understanding the business requirements and the data sources available. Once determined the focus shifts to data consumption. Or how to load the required data into the analysis solution to provide the required insights.

Part of dataset planning is determining between the various data Power BI connectivity modes. The connectivity modes are methods to connect to or load data from the data sources. The connectivity mode defines how to get the data from the data sources. The selected connectivity mode impacts report performance, data freshness, and the Power BI features available.

The decision is between the default `Import` connectivity mode, `DirectQuery` connectivity mode, `Live Connection` connectivity mode, or using a composite model. This decision can be simple in some projects, where one option is the only workable option due to the requirements. In other projects this decision requires an analysis of the benefits and limitations of each connectivity mode.

>**So which one is the best?**   
   Well...it depends.

Each connectivity type has its use cases and generally one is not better than the other but rather a trade-off decision. When determining which connectivity mode to use it is a balance between the requirements of the report and the limitations of each method.

>Moving from `Import` to `DirectQuery` to `Live Connection` represents offloading the data modeling workload to the data source and results in less data storage within Power BI.

This post will cover each connectivity mode and provides an overview of each method as well as covering their limitations.

<br>

---

<br>

## Overview

<br>

`Import` mode makes an entire copy of a subset of the source data. This data is then stored in-memory and made available to Power BI. `DirectQuery` does not load a copy of the data into Power BI. Rather Power BI stores information about the schema or shape of the data. Power BI then queries the data source making the underlying data available to the Power BI report. `Live Connection` store a connection string to the underlying analysis services and leverages Power BI as a visualization layer.

As mentioned in some projects determining a connectivity mode can be straight forward. In general, when a data source is not equipped to handle a high volume of analytical queries the preferred connectivity mode is `Import` mode. When there is a need for near real-time data then `DirectQuery` or `Live Connection` are the only options that can meet this need.

>Key differences between the connectivity modes include the supported data sources and size of the data-model

For the projects where you must analyze each connectivity mode, keep reading to get a further understanding of the benefits and limitations of each mode.

<br>

---

<br>

## Getting Started

<br>

When establishing a connection to a data source in Power BI you are presented with different connectivity options. The options available depend on the selected data source.

Available data sources can viewed by selecting `Get data` on the top ribbon in Power BI Desktop. Power BI presents a list of common data sources, and the complete list can be viewed by selecting `More...` on the bottom of the list.

Loading or connecting to data can be a different process depending on the selected source. For example, loading a local Excel file you are presented with the following screen.

![Connect to Excel File Screen](/assets/img/2023-04-23-power-bi-connectivity-modes/1_excel_file.png){: .post__img}

From here you can load the data as it is within the Excel file, or Transform your data within the Power Query Editor. Both options import a copy of the data to the Power BI data model.

However, if the SQL Server data source is select you will see a screen similar to that below.

![Connect to SQL Server Screen](/assets/img/2023-04-23-power-bi-connectivity-modes/2_sql_db.png){: .post__img}

Here you will notice you have an option to select the connectivity mode. There are also addition options under Advanced option such as providing a SQL statement to evaluate against the data source.

Lastly, below is an example of what you are presented if you select Analysis Services as a data source.

![Connect to Analysis Services Screen](/assets/img/2023-04-23-power-bi-connectivity-modes/2_analysis_services.png){: .post__img}

Again here you will see the option to set the connectivity mode.

>The available connectivity modes for a source can change depending on what other type of connectivity modes and data sources are present within the data model.

<br>

---

<br>

## Import

<br>

Import data is a common, and default, option for loading data into Power BI. When using `Import` Power BI extracts the data from the data source and stores it in an in-memory storage engine. When possible it is generally recommended to use `Import` mode. `Import` mode takes advantage of the high-performance query engine, creates highly interactive reports offering the full range of Power BI features. The alternative connectivity modes discussed later in this post can be used if the requirements of the report cannot be met due to the limitations of the `Import` connectivity mode.

`Import` models store data using Power BI's column-based storage engine. This storage method differs from row-based storage typical of relational database systems. Row-based storage commonly used by transactional systems work well when the system frequently reads and writes individual or small groups of rows.

However, this type of storage does not perform well with analytical workloads generally needed for BI reporting solutions. Analytical queries and aggregations involve a few columns of the underlying data. The need to efficiently execute these type of queries led to the column-based storage engines which store data in columns instead of rows. Column-based storage is optimized to perform aggregates and filtering on columns of data without having to retrieve the entire row from the data source.

>Columnar storage applies compression to the columns by eliminating the need to store the same physical values multiple times. The storage engine applies compression algorithms to columns depending on the column data type (e.g. value encoding, hash encoding, run-length encoding).

Key considerations when using the `Import` connectivity mode include:

1) Does the imported data needed to get update?

2) How frequent does the data have to be updated?

3) How much data is there?

### `Import` Considerations

* Data size: when using Power BI Pro your dataset limit is 1GB of compressed data. With Premium licenses this limit increases to 10GB or larger.

* Data freshness: when using Power BI Pro you are able to schedule up to 8 refreshes per day. With Premium licenses this increases to 48 or every 30 minutes.

>If the underlying data source is an on-premise source scheduled refreshes require the use of an on-premises data gateway.

* Duplicate Work: when using analysis services all the data modeling may already be complete. However, when using `Import` mode much of the data modeling may have to be redone.

<br>

---

<br>

## DirectQuery

<br>

`DirectQuery` connectivity mode provides a method to directly connect to a data source so there is no data imported or copied into the Power BI dataset. `DirectQuery` can address some of the limitations of `Import` mode. For example for a large datasets the queries are processed on the source server rather than the local computer running Power BI Desktop. Additionally since it provides a direct connection there is less of a need for data refreshes in Power BI. `DirectQuery` report queries are ran when the report is opened or interacted with by the end user.

Like importing data, when using `DirectQuery` with an on-premises data source an on-premises data gateway is required. Although there is no schedule refreshes when using `DirectQuery` the gateway is still required to push the data to the Power BI Service.

While `DirectQuery` can address the limitations presented by `Import` mode, `DirectQuery` comes with its own set of limitations to consider. `DirectQuery` is a workable option when the underlying data source can support interactive query results within an acceptable time and the source system can handle the generated query load. Since with `DirectQuery` analytical queries are sent to the underlying data source the performance of the data source is a major consideration when using `DirectQuery`.

### `DirectQuery` Considerations

* A key limitation when considering `DirectQuery` is that not all data sources available in Power BI support `DirectQuery`.
* If there are changes to the data source the report must be refreshed to show the updated data.
  * Power BI reports use caches of data and due to this there is no guarantee that visuals always display the most recent data. Selecting `Refresh` on the report will clear any caches and refresh all visuals on the page

---

**Example**   
Below is an example of the above limitation. Here we have a Power BI table and card visual of a products table on the left and the underlying SQL database on the right. For this example we will focus on `ProductID` = 680 (HL Road Frame - Black, 58) with an initial `ListPrice` of $1,431.50.

![Data Source Update Limitation](/assets/img/2023-04-23-power-bi-connectivity-modes/4_data_update.gif){: .post__img}

The `ListPrice` is updated to a value of $150,000 in the data source. However, after the update neither the table visual nor the card showing the sum of all list prices updates.

There is generally no change detection or live streaming of the data when using `DirectQuery`.

When we set the `ProductID` slicer to a value of `680` however, we see the updated value. The interaction with the slicer sents a new query to the data soucre returning the updated results displayed in the filtered table.

>Setting the slicer value executes a new query with the addition of a `WHERE` clause in the SQL query and the returned result is displayed.

Clearing the slicer shows the initial table again, without the updated value. Refreshing the report clears all caches and runs all queries required by the visuals on the page.

---

* Power BI Desktop reports must be refreshed to reflect schema changes.
  * Once you publish a report to the Power BI Service selecting `Refresh` only refreshes the visuals in the report.
    * If the underlying schema changes the Power BI  will not automatically update the available field lists.
    * Removing tables or columns from the underlying source can lead to potential query failures when refreshing a report.
    * Updating the data schema requires opening the `.pbix` file in Power BI Desktop, refresh the report, then republish the report.

---

**Example**   
Below is an example the limitation noted above. Here, we have the same Power BI report on the right as the example above and the SQL database on the left. We will start by executing the query which adds a new `ManagerID` column to the `Product` table, sets the value as a random number between 0 and 100, and then selects the top 100 products to view the update.

![Data Source Schema Update Limitation](/assets/img/2023-04-23-power-bi-connectivity-modes/5_schema_update.gif){: .post__img}

After executing the query we can refresh the columns of the `Product` table in SQL Server Management Studio (SSMS) to verify it was created. Howerver, in Power BI we see that the fields available to add to the table visual does not include this new column.

To view schema updates in Power BI the `.pbix` file must be refreshed and the report must be republished.

As noted above if columns or tables are removed from the underlying data source Power BI queries can break. To see this we first remove the `ManagerId` column in the data source, and then refresh the report. After refreshing the report we can see there is an issue with the table visual.

---

* The limit of rows returned by a query is capped at 1 million rows.
* The Power BI data model cannot be switched from `Import` to `DirectQuery` mode.
  * A `DirectQuery` table can be switched to `Import`, however once this is done it cannot be switched back.
* Some Power Query (M Language) features are not supported by `DirectQuery`.
  * Adding unsupported features to the Power Query Editor will result in a `This step results in a query that is not supported by DirectQuery mode` error message.
* Some DAX functions are not supported by `DirectQuery`. 
  * If used results in a `Function <function_name> is not supported by DirectQuery mode` error message.

>The limitations in support of M/DAX features and function is because `DirectQuery` converts M/DAX into the data source's native language (e.g. T-SQL). Any DAX function used must support query folding.

---

**Example**   
For the example report we only need the `ProductID`, `Name`, and `ListPrice` field. However, you can see in the data pane we have all the columns present in the source data. We can modify which column are available in Power BI be editing the query in the Power Query Editor.

![Native Query](/assets/img/2023-04-23-power-bi-connectivity-modes/6_native_query.gif){: .post__img}

After removing the unneeded columns we can view the native query that get executed against the underlying data source and see the `SELECT` statement includes only the required columns (i.e. column not removed by the `Removed Columns` step).

---

<br>

Other implications and considerations of `DirectQuery` include performance and load implications on the data source, data security implications, data-model limitations, and reporting limitations.

### `DirectQuery` Use Cases

With its given limitations `DirectQuery` can still be a suitable option for the following use cases.

* When report requirements include the need for near real-time data.
* The report requires a large dataset, greater than what is supported by `Import` mode.
* The underlying data source defines and applies security rules.
  * When developing a Power BI report with `DirectQuery` Power BI connects to the data source by using the current user's (report developer) credentials
  * `DirectQuery` allows a report viewer's credentials to pass through to the underlying source, which then applies security rules.
* Data sovereignty restrictions are applicable (e.g. data cannot be stored in the cloud).
  * When using `DirectQuery` data is cached in the Power BI Service, however there is no long term cloud storage.

<br>

---

<br>

## Live Connection

<br>

`Live Connection` is a method that lets you build a report in Power BI Desktop without having to build a dataset to under pin the report. The connectivity mode offloads as much work as possible to the underlying analysis services. When building a report in Power BI Desktop that uses `Live Connection` you connect to a dataset that already exists in an analysis service.

Similar to `DirectQuery` when `Live Connection` is used no data is imported or copied into the Power BI dataset. Rather Power BI stores a connection string to the existing analysis services (e.g. SSAS) or published Power BI dataset and Power BI is used as a visualization layer.

### `Live Connection` Considerations

* Can only be used with a limited number of data sources.
  * Existing Analysis Service data model (SQL Server Analysis Services (SSAS) or Azure Analysis Services)
* No data-model customizations are available, any changes required to the data-model need to be done at the data source.
  * Report-Level measures are the one exception to this limitation.
* User identity is passed through to the data source.
  * A report is subject to row-level security and access permissions that are set on the data-model.

<br>

---

<br>

## Composite Models

<br>

Power BI no longer limits you to choosing just `Import` or `DirectQuery` mode. With composite models the Power BI data-model can include data connections from one (or more) `DirectQuery` or `Import` data connections.

A composite model allows you to connect to different types of data sources when creating the Power BI data-model. Within a single `.pbix` file you can combine data from one or more `DirectQuery` sources and/or combine data form `DirectQuery` sources and `Import` data.

Each table within the composite model will list its storage mode which shows whether the table is based on a `DirectQuery` or `Import` source. The storage mode can be viewed and modified on the properties pane of the table.

---

**Example**   
The example below is a Power BI report with a table visual of a products table. We have added the `ManagerID` column to this table, however there is no `Managers` table in the underlying SQL database. Rather this information is contained within a local `product_managers` Excel file. With a composite model we can combined these two different data sources and connectivity modes to create a single report.

![Composite Model](/assets/img/2023-04-23-power-bi-connectivity-modes/7_composite_model.gif){: .post__img}

Initially the report storage mode is `DirectQuery` because to start we only have the `Product` table `DirectQuery` connection.

We use the `Import` connectivity mode to load the `product_mangers` data and create the relationship between the two tables.

You can check the storage mode of each table in the properties pane under Advanced. We can see the `SalesLT Product` table has a `DirectQuery` storage mode and that `Sheet1` has a `Import` storege  mode

Once the data model is a composite model we see the report Storage Mode update to a value of `Mixed`

---
<br>

### Composite Model Considerations

* Security Implications: A query sent to one data source could include data values that have been extracted from a different data source.
  * If the extracted data is confidential the security impacts of this should be considered.
  * Avoid extracting data from one data source via an encrypted connection to then include this data in a query sent to a different source via an unencrypted connection.
  * When a report author includes a table from one data-model in a composite model, a user viewing a report built upon the composite model could query **any** table in the data-model the table was added from.
* Performance Implications: Whenever `DirectQuery` is used the performance of the underlying system should be considered. Ensure that is has the resources required to support the query load due to users interacting with the Power BI report.
  * A visual in a composite model can sent queries to multiple data source with the results from one query being passed to another query from a different source.

<br>

---
<br>

If you enjoy what you read and find it helpful please check back, and check back often. Follow me on <a class="post__link" href="https://medium.com/@emguyant"><i class="fab fa-medium"></i> Medium</a> while giving a clap to the article! Also don't forget to subscribe to the <a class="post__link" href="https://medium.com/inquisitive-nature"><b>INQUISITIVE NATURE</b></a> publication.
