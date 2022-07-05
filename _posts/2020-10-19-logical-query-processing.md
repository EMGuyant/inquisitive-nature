---
layout: post
author: Ethan Guyant
title:  Logical Query Processing Order
description: A SQL query declares what data is expected to be returned. The impact of this is that the oder a query is written is not the order that the query is process. This article aims to explain the differences in the order SQL is written vs the order in which it is processed.
image: /assets/img/post_img/logical-sql.jpg
image_by: AltumCode
image_by_link: https://unsplash.com/@altumcode?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---
## Logical Query Process

To better understand the logical processing order a general form query is
provided below. In this query the numbers (`#.#`) indicate the logical 
processing order. It is also
important to note that the logical processing order does differ from the typed
order of the query and can differ from the physical processing order.

```sql
(5) SELECT (5.2) DISTINCT (7) TOP(<top_specification>) (5.1) <select_list>
(1) FROM (1-J) <left_table> <join_type> JOIN <right_table> ON <on_predicate>
         (1-A) <left_table> <apply_type> APPLY <right_input_table> AS <alias>
         (1-P) <left_table> PIVOT(<pivot_specification>) AS <alias>
         (1-U) <left_table> UNPIVOT(<unpivot_specifcation>) AS <alias>
(2) WHERE <where_predicate>
(3) GROUP BY <group_by_specification>
(4) HAVING <having_predicate>
(6) ORDER BY <order_by_list>
(7) OFFSET <offset_specification> ROWS FETCH NEXT <fetch_specifications> ROWS 
ONLY; 
```

## Logical vs Typed vs Physical processing Order
*The differences between the logical processing order, the typed order, and the
physical processing order are key to further you understanding of SQL queries.*

* **Logical Processing Order:** conceptual understanding of the query order
  which defines the correct result set
* **Typed Order:** the order in which the query is written
* **Physical Processing Order:** the processing order of the elements in the
  physical-query execution plan
  * The component that generates the execution in SQL Server is the query
    optimizer, and determines things such as the order the tables are accessed,
    which indexes to use, or which join algorithms to apply.
