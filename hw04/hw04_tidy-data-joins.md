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
| `spread()`       | wide format :left\_right\_arrow: |

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

``` r
## Alternative ways to call gather()
## gather(subject, grade, biology:french)
## gather(subject, grade, -c(student))
## gather(subject, grade, biology, chemistry, french)
```

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

Activity 2: Exploring life expectancy
-------------------------------------

### Tibble

A tibble with one row per year and columns for life expectancy for two or more countries.

``` r
lifeExp.tbl <- gapminder %>%
               filter(country %in% c('Australia', 'Canada', 'Denmark')) %>% 
               select(year, country, lifeExp) %>% 
               gather(measure, value, lifeExp)

knitr::kable(lifeExp.tbl)
```

|  year| country   | measure |   value|
|-----:|:----------|:--------|-------:|
|  1952| Australia | lifeExp |  69.120|
|  1957| Australia | lifeExp |  70.330|
|  1962| Australia | lifeExp |  70.930|
|  1967| Australia | lifeExp |  71.100|
|  1972| Australia | lifeExp |  71.930|
|  1977| Australia | lifeExp |  73.490|
|  1982| Australia | lifeExp |  74.740|
|  1987| Australia | lifeExp |  76.320|
|  1992| Australia | lifeExp |  77.560|
|  1997| Australia | lifeExp |  78.830|
|  2002| Australia | lifeExp |  80.370|
|  2007| Australia | lifeExp |  81.235|
|  1952| Canada    | lifeExp |  68.750|
|  1957| Canada    | lifeExp |  69.960|
|  1962| Canada    | lifeExp |  71.300|
|  1967| Canada    | lifeExp |  72.130|
|  1972| Canada    | lifeExp |  72.880|
|  1977| Canada    | lifeExp |  74.210|
|  1982| Canada    | lifeExp |  75.760|
|  1987| Canada    | lifeExp |  76.860|
|  1992| Canada    | lifeExp |  77.950|
|  1997| Canada    | lifeExp |  78.610|
|  2002| Canada    | lifeExp |  79.770|
|  2007| Canada    | lifeExp |  80.653|
|  1952| Denmark   | lifeExp |  70.780|
|  1957| Denmark   | lifeExp |  71.810|
|  1962| Denmark   | lifeExp |  72.350|
|  1967| Denmark   | lifeExp |  72.960|
|  1972| Denmark   | lifeExp |  73.470|
|  1977| Denmark   | lifeExp |  74.690|
|  1982| Denmark   | lifeExp |  74.630|
|  1987| Denmark   | lifeExp |  74.800|
|  1992| Denmark   | lifeExp |  75.330|
|  1997| Denmark   | lifeExp |  76.110|
|  2002| Denmark   | lifeExp |  77.180|
|  2007| Denmark   | lifeExp |  78.332|

### Scatterplot

A scatterplot of life expectancy for one country against that of another using the new data shape created above.

``` r
ggplot(lifeExp.tbl, aes(x=year, y=value)) + 
  geom_point(aes(colour=country)) +
  geom_smooth(se=FALSE, method='loess', aes(colour=country)) +
  theme_bw() +
  labs(title='Life Expectancy Trends') +
  scale_x_continuous('Year') +
  scale_y_continuous('Life Expectancy') +
  scale_color_discrete("Country") +
  theme(legend.position="top",
        plot.title=element_text(hjust=0.5))
```

![](hw04_tidy-data-joins_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-6-1.png)
