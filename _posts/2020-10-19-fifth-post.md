---
layout: post
author: Ethan Guyant
title:  Logical Query Processing Order
description: A SQL query declares what data is expected to be returned. The impact of this is that the oder a query is written is not the order that the query is process. This article aims to explain the differences in the order SQL is written vs the order in which it is processed.

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

# h1 Header 1
## h2 Header 2
### h3 Header 3
#### h4 Header 4
##### h5 Header 5
###### h6 Header 6

## Basic setup

Jekyll requires blog post files to be named according to the following format:

`YEAR-MONTH-DAY-filename.md`

Where `YEAR` is a four-digit number, `MONTH` and `DAY` are both two-digit numbers, and `filename` is whatever file name you choose, to remind yourself what this post is about. `.md` is the file extension for markdown files.

The first line of the file should start with a single hash character, then a space, then your title. This is how you create a "*level 1 heading*" in markdown. Then you can create level 2, 3, etc headings as you wish but repeating the hash character, such as you see in the line `## File names` above.

## Basic formatting

You can use *italics*, **bold**, `code font text`, and create [links](https://www.markdownguide.org/cheat-sheet/). Here's a footnote [^1]. Here's a horizontal rule:

---

## Lists

Here's a list:

- item 1
- item 2

And a numbered list:

1. item 1
1. item 2

## Boxes and stuff

> This is a quotation

...and...


## Code

You can format text and code per usual 

General preformatted text:

    # Do a thing
    do_thing()

Python code and output:

```python
# Prints '2'
print(1+1)
```

    2

Formatting text as shell commands:

```shell
echo "hello world"
./some_script.sh --option "value"
wget https://example.com/cat_photo1.png
```

Formatting text as YAML:

```yaml
key: value
- another_key: "another value"
```


## Tables

| Column 1 | Column 2 |
|-|-|
| A thing | Another thing |
| Another Thing | And Another Thing |


## Footnotes



[^1]: This is the footnote.