# hw04-Tidy data and joins

This is my repo for Stat 545A Homework04: Tidy data and joins. The goal of this homework is to solidify our data wrangling skills by working some realistic problems in the grey area between data aggregation and data reshaping. In this assginment, I explored activity #5 from **Data Reshaping Prompts** and combination of activity #2 & #3 from **Join Prompts**

Rstudio has a handy [**Data Wrangling**](https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf) cheatsheet that provides immediate help

For data reshaping, I explored following functions from __*tidyr*__ with __*gapminder*__ data:

tidyr Function | Definition
----------------|------------------
   `gather`      | Take multiple columns into a new pair of variables
   `spread`       |  Take one columns and scatter it across multiple columns
   `unite`       |  Unite values from multiple columns

For data joining, I explored the following function could from __*tidyr*__ with __*nycflight13*__ data:

join function  | Definition
---------------|----------------------
 `inner_join`  |  matche pairs of observations whenever their keys are equal
 `left_join`  |   left_join(x, y), matching rows from y to x
 `full_join`  |  full_join(x, y): Return all rows and all columns from both x and y.

Here are links to different documents in this repo:
* [Tidy data and joins.Rmd](https://github.com/STAT545-UBC-students/hw04-huangjieying/blob/master/hw04_Tidy%20data%20and%20joins.Rmd)
* [Tidy data and joins.md](https://github.com/STAT545-UBC-students/hw04-huangjieying/blob/master/hw04_Tidy_data_and_joins.md)
