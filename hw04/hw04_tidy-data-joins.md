Homework 04: Tidy data and joins
================
Amanda Cheung

Putting my data wrangling skills to the test!

#### Gapminder and tidyveryse

Load gapminder and tidyverse.

``` r
suppressPackageStartupMessages(library(gapminder))
suppressPackageStartupMessages(library(tidyverse))
```

General data reshaping and relationship to aggregation
------------------------------------------------------

**Problem:** You have data in one “shape”, but you wish it were in another...

**Solution:** Reshape your data! Use `gather()` and `spread()` from `tidyr` for simple reshaping!

Activity 1: `tidyr` cheatsheet
------------------------------

A minimal cheatsheet. Check out the [R studio version](https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf), for a more comprehensive one.

### Main functions: `gather()` and `spread()`

| `tidyr` function | action                           |
|------------------|----------------------------------|
| `gather()`       | long format :arrow\_up\_down:    |
| `spready()`      | wide format :left\_right\_arrow: |

### Example data

``` r
student <- c('A', 'B', 'C')
biology <- c(88, 90, 75)
chemistry <- c(95, 85, 90)
french <- c(83, 77, 85) 

ex.data <- data.frame(student, biology, chemistry, french) 

knitr::kable(ex.data)
```

| student |  biology|  chemistry|  french|
|:--------|--------:|----------:|-------:|
| A       |       88|         95|      83|
| B       |       90|         85|      77|
| C       |       75|         90|      85|

### `gather()`

Gather columns into rows.

![](ex_gather.png)

``` r
knitr::kable(ex.data %>% 
               gather(key=subject, value=grade, biology:french))
```

| student | subject   |  grade|
|:--------|:----------|------:|
| A       | biology   |     88|
| B       | biology   |     90|
| C       | biology   |     75|
| A       | chemistry |     95|
| B       | chemistry |     85|
| C       | chemistry |     90|
| A       | french    |     83|
| B       | french    |     77|
| C       | french    |     85|

### `spread()`

Spread rows into columns.

![](ex_spread.png)

``` r
knitr::kable(ex.data %>% 
               gather(key=subject, value=grade, biology:french) %>% 
               spread(key=subject, value=grade))
```

| student |  biology|  chemistry|  french|
|:--------|--------:|----------:|-------:|
| A       |       88|         95|      83|
| B       |       90|         85|      77|
| C       |       75|         90|      85|
