---
title: "Tidy"
permalink: /R/Tidy
excerpt: "Basics of Data Wrangling>"
collection: R
---


    library(tidyverse)

##### **What is tidy data?**

There are three interrelated rules which make a dataset tidy:

    1. Each variable must have its own column
    2. Each observation must have its own row
    3. Each value must have its own cell

These three rules are interrelated because it is impossible to only
satisfy two of the three.

##### **Pivoting**

There are two general ways in which a dataset can be untidy:

    1. One variable might be spread over multiple columns
    2. One observation might be scattered across multiple rows

In order to tidy such datasets we will use `pivot_longer()` and
`pivot_wider()`

###### Longer Pivoting

Longer pivoting a dataset is needed when columns not variables but
*values* of a variable. Lets take as an example table4a:

    table4a

    ## # A tibble: 3 × 3
    ##   country     `1999` `2000`
    ##   <chr>        <dbl>  <dbl>
    ## 1 Afghanistan    745   2666
    ## 2 Brazil       37737  80488
    ## 3 China       212258 213766

The columns 1999 and 2000 are not variables but observations of the
variable *years*. To tidy our dataset we need to **pivot** these two
columns using three parameters:

    1: The set of columns that are **values** and not variables. In this case: `1999` and `2000` 
    2: The name of the variable to move the column **names** to. In this case: `years`
    3: The name of the variable to move the column **values** to

Together those parameters generate the call to `pivot_longer()`:

    tidy4a <- table4a %>%
      pivot_longer(
        c(`1999`, `2000`),
        names_to = "year",
        values_to = "cases"
        )

`year` and `cases` do not exist in table4a so we put their names in
quotes.

We can use pivot\_longer() to tidy table4b in a similar fashion. The
only difference is the variable stored in the cell values:

    tidy4b <- table4b %>% 
      pivot_longer(
        c(`1999`, `2000`), 
        names_to = "year", 
        values_to = "population")

To join the tidied versions of table4a and table4b into a single tibble,
we need to use `dplyr::left_join()` :

    left_join(tidy4a, tidy4b)

    ## Joining with `by = join_by(country, year)`

    ## # A tibble: 6 × 4
    ##   country     year   cases population
    ##   <chr>       <chr>  <dbl>      <dbl>
    ## 1 Afghanistan 1999     745   19987071
    ## 2 Afghanistan 2000    2666   20595360
    ## 3 Brazil      1999   37737  172006362
    ## 4 Brazil      2000   80488  174504898
    ## 5 China       1999  212258 1272915272
    ## 6 China       2000  213766 1280428583

###### Wider Pivoting

Wider pivoting a dataset is needed when an observation is scattered
across multiple rows, or two columns are “stacked” into one. An example
can be seen in `table2`, where an observation is a country in a year,
but each observation is spread across two rows:

    table2

    ## # A tibble: 12 × 4
    ##    country      year type            count
    ##    <chr>       <dbl> <chr>           <dbl>
    ##  1 Afghanistan  1999 cases             745
    ##  2 Afghanistan  1999 population   19987071
    ##  3 Afghanistan  2000 cases            2666
    ##  4 Afghanistan  2000 population   20595360
    ##  5 Brazil       1999 cases           37737
    ##  6 Brazil       1999 population  172006362
    ##  7 Brazil       2000 cases           80488
    ##  8 Brazil       2000 population  174504898
    ##  9 China        1999 cases          212258
    ## 10 China        1999 population 1272915272
    ## 11 China        2000 cases          213766
    ## 12 China        2000 population 1280428583
