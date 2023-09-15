---
layout: post
author: Ethan Guyant
title:  "Time Travel in Power BI: Mastering Time Intelligence Functions"
category: Quick Start
tags: [Microsoft 365, Power BI, Power Platform, Power BI Fundamentals Series, Power BI Functions]
description: Buckle up, data enthusiasts! Time is the key to unlocking valuable insights. So, get ready to warp through time and transform your Power BI reports into data narratives. Discover the incredible power of Time Intelligence functions and take your analysis to the next level!
image: /assets/img/post_img/time-intelligence.jpg
image_by: Djim Loic
image_by_link: https://unsplash.com/@loic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---

## The Tools to Time Travel: An Introduction

<br>

In Power BI, time intelligence functions are your handy time machines. They enable you to extract useful insights from your data by manipulating time periods. These functions range from calculating sales year-to-date (YTD) to comparing data from previous years. If you want to unlock the true potential of your Power BI reports, you need to harness the might of Time Intelligence functions. In this post, we will unpack six time intelligence functions: DATESYTD, TOTALYTD, SAMEPERIODLASTYEAR, DATEADD, DATESINPERIOD, and DATESBETWEEN.

For those of you eager to start experimenting and diving deeper there is a Power BI report loaded with the sample data used in this post ready for you. So don’t just read, dive in and get hands-on with DAX Functions in Power BI. Check it out here: [GitHub — Power BI DAX Function Series: Mastering Data Analysis](https://github.com/EMGuyant/powerbi-dax-functions-series){: .post__link}.

<br>

---

<br>

## The Twin Siblings: DATESYTD and TOTALYTD

<br>

First stop on our journey, the twin siblings, DATESYTD and TOTALYTD.

DATESYTD is the Einstein of the Power BI world, it uses the concept of relative time to help us compute values from the start of the year to the last date in the data. The syntax is as simple as the concept:

`DATESYTD(<dates>[, <year_end_date>])`

Here, `<dates>` is a column containing dates, and `<year_end_date>` is an optional parameter to specify the year-end date, with a default value of December 31st.

On the other hand, TOTALYTD takes it up a notch. It summarizes data for the same period in a given year. The syntax of this function is:

`TOTALYTD(<expression>, <dates>[, <filter>])`

In this syntax, `<expression>` is what you want to calculate, `<dates>` is a column containing dates, and `<filter>` is a filter to restrict the calculation over time.  TOTALYTD is DATESYTD, but with additional calculation power.

Here are example DAX formulas to calculate the total sales amount from the start of the year till the current date.

```text
YTD Sales DATESYTD =
CALCULATE(
    SUM(Sales[Amount]),
    DATESYTD(Dates[Date])
)
```

And here is how you can calculate the same using TOTALYTD:

```text
YTD Sales TOTALYTD =
TOTALYTD(SUM(Sales[Amount]), Dates[Date])
```

While both functions return the same result in this case, the key difference lies in their flexibility. DATESYTD just provides a set of dates, but TOTALYTD goes a step further to calculate an expression over those dates. Notice `YTD Sales DATESYTD` uses `DATESYTD` in combination with `CALCULATE` in order to achieve the same outcome as `YTD Sales TOTALYTD`.

![DATESYTED and TOTALYTD Calculations](/assets/img/2023-07-18-powerbi-time-intelligence/datesytd_totalytd.png){: .post__img}

>Don't get confused, TOTALYTD may look like it calculates the year-to-date *total*, but in reality it only changes the context. It can be used to return year-to-date anything, it all depends on what you define as the `<expression>` parameter.

For more information on these two head over to the Microsoft documentation for [DATESYTD](https://learn.microsoft.com/en-us/dax/datesytd-function-dax){: .post__link} and [TOTALYTD](https://learn.microsoft.com/en-us/dax/totalytd-function-dax){: .post__link}.

<br>

---

<br>

## The Cousins: SAMEPERIODLASTYEAR and DATEADD

<br>

Next up the cousins, SAMEPERIODLASTYEAR and DATEADD. SAMEPERIODLASTYEAR is the function that does exactly as the name suggests and always knows what happened "this time last year".  This is perfect for spotting trends, analyzing seasonality, or measuring growth. The syntax couldn't be more straightforward:

`SAMEPERIODLASTYEAR(<dates>)`

Here, `<dates>` is a column that contains dates. Think of this function as equivalent to you Facebook memories, reminding you of what happened exactly one year ago.

DATEADD is the flexible function that allows you to go back (or forward) any number of intervals you choose. This handy function's syntax is:

`DATEADD(<dates>, <number_of_intervals, <interval>)`

Here, `<dates>` is your date column, `<number_of_intervals>` is the number of intervals to move (can be negative for moving backwards), and `<interval>` is the interval to use (DAY, MONTH, QUARTER, or YEAR).  This function lets you journey backward or forward in time with ease!

The example for this duo calculates the total sales for the same period in the previous year. The DAX formula for this example is:

```text
Sales LY SAMEPERIODLASTYEAR =
CALCULATE(
    SUM(Sales[Amount]),
    SAMEPERIODLASTYEAR(Dates[Date])
)
```

Similarly, using DATEADD, you can achieve the same result by subtracting one year from the current date:

```text
Sales LY DATEADD =
CALCULATE(
    SUM(Sales[Amount]),
    DATEADD(Dates[Date], -1, YEAR)
)
```

While both of these functions seem to do the same job, the difference again lies in their flexibility. SAMEPERIODLASTYEAR only takes you back one year, while DATEADD give you the liberty to move as far back or forward as you want, making it a more flexible option overall.

![DATESYTED and TOTALYTD Calculations](/assets/img/2023-07-18-powerbi-time-intelligence/sameperiodlastyear_dateadd.png){: .post__img}

Visit the Microsoft documentation for [SAMEPERIODLASTYEAR](https://docs.microsoft.com/en-us/dax/sameperiodlastyear-function-dax){: .post__link} and [DATEADD](https://docs.microsoft.com/en-us/dax/dateadd-function-dax){: .post__link} for a deeper dive.

<br>

---

<br>

## The Mysterious Pair: DATESINPERIOD and DATESBETWEEN

<br>

Last but not least, let's explore the mysterious pair DATESINPERIOD and DATESBETWEEN. DATESINPERIOD has your back if you need to calculate data for a specific period. It returns a table that contains a column of dates that starts from a specific date, extends by a specified interval, and stops at the end of the last interval.

Its syntax is:

`DATESINPERIOD(<dates>, <start_date>, <number_of_intervals>, <interval>)`

Here, `<dates>` is a column containing dates, `<start_date>` is the start date for the calculation, `<number_of_intervals>` is the number of intervals to include, and `<interval>` is the interval to use (DAY, MONTH, QUARTER, or YEAR).

DATESBETWEEN, however, is the function you'd use to fetch data between two specific dates. It returns a table that contains a column of all dates between two specified dates. The syntax is as simple as:

`DATESBETWEEN(<dates>, <start_date>, <end_date>)`

Here, `<dates>` is a column that contains dates, `<start_date>` is the start date for the calculation, and `<end_date>` is the end date for the calculation. It's like ordering a specific range of books from a library catalog. You get exactly what you want, nothing more, nothing less!

Let's look at a practical example of these functions.  Say you want to calculate the 3-month rolling average sales, both these functions can help solve this in their own way.

Using DATAINPERIOD the DAX formula is:

```text
Sales 3 Month Rolling Average DATESINPERIOD =
CALCULATE(
    AVERAGE(Sales[Amount]),
    DATESINPERIOD(Dates[Date], LASTDATE(Dates[Date]), -3, MONTH)
)
```

This formula calculates the average sales for the previous three months from the last date in the data. The same calculation with DATESBETWEEN would like like:

```text
Sales 3 Month Rolling Average DATESBETWEEN =
CALCULATE(
    AVERAGE(Sales[Amount]),
    DATESBETWEEN(
        Dates[Date],
        EDATE(LASTDATE(Dates[Date]), -3),
        LASTDATE(Dates[Date])
    )
)
```

This formula also returns the average sales for the previous three months from the last date in the data. The key difference is that with DATESBETWEEN, you explicitly specify the start and end dates, providing a high degree of precision when needed.

![DATESYTED and TOTALYTD Calculations](/assets/img/2023-07-18-powerbi-time-intelligence/datesinperiod_datesbetween.png){: .post__img}

You can learn more about these functions at the Microsoft documentation for [DATESINPERIOD](https://docs.microsoft.com/en-us/dax/datesinperiod-function-dax){: .post__link} and [DATESBEETWEEN](https://docs.microsoft.com/en-us/dax/datesbetween-function-dax){: .post__link}.

<br>

---

<br>

## Stepping Out of the Time Capsule

<br>

Power BI's Time Intelligence functions are akin to a time-traveling journey. They empower you to traverse through your data - past, present, and future. Whether you're revisiting the past year with SAMEPERIODLASTYEAR, leaping through your data with DATEADD, or meticulously exploring specific date ranges with DATESBETWEEN, the power is all yours. So fasten your seat belts, prepare your time capsules, and commandeer your data journey. Happy data crunching!

And remember, as Albert Einstein once said, "Anyone who has never made a mistake has never tried anything new." So, don't be afraid of making mistakes; practice makes perfect.

Continuously experiment with Time Intelligence functions, explore new DAX functions, and challenge yourself with real-world data scenarios.

<br>

---
<br>

If this sparked your curiosity, keep that spark alive and check back frequently. 

Or even better, don't let the conversation end here. <a class="post__link" href="https://medium.com/@emguyant"><i class="fab fa-medium"></i>Follow me on Medium</a> to continue the conversation by commenting on the post and show your support through shares, and applause. 

Be sure not to miss a post by <a class="post__link" href="https://medium.com/@emguyant/subscribe"><i class="fab fa-medium"></i>subscribing here</a>, with each new post comes an opportunity to learn something new.

Eager for a deeper exploration? Consider venturing further by <a class="post__link" href="https://medium.com/@emguyant/membership"><i class="fab fa-medium"></i>joining Medium</a>, with a Medium membership you gain unlimited access to a world brimming with insights.

Thank you for reading! Stay curious, and until next time, happy learning.
