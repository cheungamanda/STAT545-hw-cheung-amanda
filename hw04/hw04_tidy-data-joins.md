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
## Creating a data frame about students' grades
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

### Tibble: One row per year and columns for life expectancy for two or more countries

``` r
lifeExp.tbl <- gapminder %>%
  filter(country %in% c('Australia', 'Canada', 'Denmark')) %>% 
  select(year, country, lifeExp) %>% 
  spread(country, lifeExp) ## Spread rows into columns of country and their respective life expectancy

knitr::kable(lifeExp.tbl)
```

|  year|  Australia|  Canada|  Denmark|
|-----:|----------:|-------:|--------:|
|  1952|     69.120|  68.750|   70.780|
|  1957|     70.330|  69.960|   71.810|
|  1962|     70.930|  71.300|   72.350|
|  1967|     71.100|  72.130|   72.960|
|  1972|     71.930|  72.880|   73.470|
|  1977|     73.490|  74.210|   74.690|
|  1982|     74.740|  75.760|   74.630|
|  1987|     76.320|  76.860|   74.800|
|  1992|     77.560|  77.950|   75.330|
|  1997|     78.830|  78.610|   76.110|
|  2002|     80.370|  79.770|   77.180|
|  2007|     81.235|  80.653|   78.332|

### Scatterplot: Life expectancy for one country against that of another using the new data shape created above

``` r
ggplot(lifeExp.tbl, aes(x=Canada, y=Australia)) + 
  geom_point(aes(colour=factor(year))) +
  geom_abline(alpha=0.25) +
  theme_bw() +
  labs(title='Canada vs. Australia Life Expectancy Trends') +
  scale_color_discrete("Year") +
  theme(plot.title=element_text(hjust=0.5))
```

![](hw04_tidy-data-joins_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-6-1.png)

The life expectancy between Canada and Australia are quite similar. The line where life expectancy is the same for both countries is showed. Points above this line indicate the country on the y-axis (Australia) has a higher life expectancy than the country on the x-axis (Canada) at a specific point in time (and vice versa).

``` r
ggplot(lifeExp.tbl, aes(x=Canada, y=Denmark)) + 
  geom_point(aes(colour=factor(year))) +
  geom_abline(alpha=0.25) +
  theme_bw() +
  labs(title='Canada vs. Denmark Life Expectancy Trends') +
  scale_color_discrete("Year") +
  theme(plot.title=element_text(hjust=0.5))
```

![](hw04_tidy-data-joins_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-7-1.png)

``` r
ggplot(lifeExp.tbl, aes(x=Australia, y=Denmark)) + 
  geom_point(aes(colour=factor(year))) +
  geom_abline(alpha=0.25) +
  theme_bw() +
  labs(title='Australia vs. Denmark Life Expectancy Trends') +
  scale_color_discrete("Year") +
  theme(plot.title=element_text(hjust=0.5))
```

![](hw04_tidy-data-joins_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-8-1.png)

Activity 3: Maximum life expectancy
-----------------------------------

### Tibble: Maximum life expectancy for all possible combinations of continent and year. One row per year and one variable for each continent.

``` r
maxlifeExp.tbl <- gapminder %>%
  group_by(year, continent) %>%
  summarize(maxlifeExp = max(lifeExp)) %>%
  spread(continent, maxlifeExp) %>%
  arrange(year)

knitr::kable(maxlifeExp.tbl)
```

|  year|  Africa|  Americas|    Asia|  Europe|  Oceania|
|-----:|-------:|---------:|-------:|-------:|--------:|
|  1952|  52.724|    68.750|  65.390|  72.670|   69.390|
|  1957|  58.089|    69.960|  67.840|  73.470|   70.330|
|  1962|  60.246|    71.300|  69.390|  73.680|   71.240|
|  1967|  61.557|    72.130|  71.430|  74.160|   71.520|
|  1972|  64.274|    72.880|  73.420|  74.720|   71.930|
|  1977|  67.064|    74.210|  75.380|  76.110|   73.490|
|  1982|  69.885|    75.760|  77.110|  76.990|   74.740|
|  1987|  71.913|    76.860|  78.670|  77.410|   76.320|
|  1992|  73.615|    77.950|  79.360|  78.770|   77.560|
|  1997|  74.772|    78.610|  80.690|  79.390|   78.830|
|  2002|  75.744|    79.770|  82.000|  80.620|   80.370|
|  2007|  76.442|    80.653|  82.603|  81.757|   81.235|

### Scatterplot: Life expectancy for one continent against that of another using the new data shape

It is easier to make a scatterplot comparing one continent against another with the new data shape.

``` r
ggplot(maxlifeExp.tbl, aes(x=Africa, y=Europe)) + 
  geom_point(aes(colour=factor(year))) +
  geom_abline(alpha=0.25) +
  theme_bw() +
  labs(title='Africa vs. Europe Life Expectancy Trends') +
  scale_color_discrete("Year") +
  theme(plot.title=element_text(hjust=0.5))
```

![](hw04_tidy-data-joins_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-10-1.png)

Europe has a higher life expectancy than Africa.

``` r
ggplot(maxlifeExp.tbl, aes(x=Asia, y=Americas)) + 
  geom_point(aes(colour=factor(year))) +
  geom_abline(alpha=0.25) +
  theme_bw() +
  labs(title='Asia vs. Americas Life Expectancy Trends') +
  scale_color_discrete("Year") +
  theme(plot.title=element_text(hjust=0.5))
```

![](hw04_tidy-data-joins_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-11-1.png)

The life expectancy in Asia becomes greater than the Americas over time.

Activity 4: A table giving the country with both the lowest and highest life expectancy for all continents and reshape it so you have one row per year
------------------------------------------------------------------------------------------------------------------------------------------------------

``` r
continent.tbl <- gapminder %>%
  select(year, continent, country, lifeExp) %>%
  group_by(year, continent) %>%
  filter(min_rank(desc(lifeExp)) < 2 | min_rank(lifeExp) < 2) %>% 
  arrange(year, continent, lifeExp) %>% 
  ## Paste the min and max life expectancy with the corresponding country for each continent
  summarize(country_combined = paste(country, lifeExp, collapse=", ")) %>% 
  spread(continent, country_combined)

knitr::kable(continent.tbl)
```

|  year| Africa                                | Americas                     | Asia                             | Europe                           | Oceania                              |
|-----:|:--------------------------------------|:-----------------------------|:---------------------------------|:---------------------------------|:-------------------------------------|
|  1952| Gambia 30, Reunion 52.724             | Haiti 37.579, Canada 68.75   | Afghanistan 28.801, Israel 65.39 | Turkey 43.585, Norway 72.67      | Australia 69.12, New Zealand 69.39   |
|  1957| Sierra Leone 31.57, Mauritius 58.089  | Haiti 40.696, Canada 69.96   | Afghanistan 30.332, Israel 67.84 | Turkey 48.079, Iceland 73.47     | New Zealand 70.26, Australia 70.33   |
|  1962| Sierra Leone 32.767, Mauritius 60.246 | Bolivia 43.428, Canada 71.3  | Afghanistan 31.997, Israel 69.39 | Turkey 52.098, Iceland 73.68     | Australia 70.93, New Zealand 71.24   |
|  1967| Sierra Leone 34.113, Mauritius 61.557 | Bolivia 45.032, Canada 72.13 | Afghanistan 34.02, Japan 71.43   | Turkey 54.336, Sweden 74.16      | Australia 71.1, New Zealand 71.52    |
|  1972| Sierra Leone 35.4, Reunion 64.274     | Bolivia 46.714, Canada 72.88 | Afghanistan 36.088, Japan 73.42  | Turkey 57.005, Sweden 74.72      | New Zealand 71.89, Australia 71.93   |
|  1977| Sierra Leone 36.788, Reunion 67.064   | Haiti 49.923, Canada 74.21   | Cambodia 31.22, Japan 75.38      | Turkey 59.507, Iceland 76.11     | New Zealand 72.22, Australia 73.49   |
|  1982| Sierra Leone 38.445, Reunion 69.885   | Haiti 51.461, Canada 75.76   | Afghanistan 39.854, Japan 77.11  | Turkey 61.036, Iceland 76.99     | New Zealand 73.84, Australia 74.74   |
|  1987| Angola 39.906, Reunion 71.913         | Haiti 53.636, Canada 76.86   | Afghanistan 40.822, Japan 78.67  | Turkey 63.108, Switzerland 77.41 | New Zealand 74.32, Australia 76.32   |
|  1992| Rwanda 23.599, Reunion 73.615         | Haiti 55.089, Canada 77.95   | Afghanistan 41.674, Japan 79.36  | Turkey 66.146, Iceland 78.77     | New Zealand 76.33, Australia 77.56   |
|  1997| Rwanda 36.087, Reunion 74.772         | Haiti 56.671, Canada 78.61   | Afghanistan 41.763, Japan 80.69  | Turkey 68.835, Sweden 79.39      | New Zealand 77.55, Australia 78.83   |
|  2002| Zambia 39.193, Reunion 75.744         | Haiti 58.137, Canada 79.77   | Afghanistan 42.129, Japan 82     | Turkey 70.845, Switzerland 80.62 | New Zealand 79.11, Australia 80.37   |
|  2007| Swaziland 39.613, Reunion 76.442      | Haiti 60.916, Canada 80.653  | Afghanistan 43.828, Japan 82.603 | Turkey 71.777, Iceland 81.757    | New Zealand 80.204, Australia 81.235 |

Join, merge, look up
--------------------

**Problem:** You have two data sources and you need info from both in one new data object.

**Solution:** Perform a join, which borrows terminology from the database world, specifically SQL.

Activity 1: Create a second data frame, complementary to Gapminder. Join this with (part of) Gapminder using a `dplyr` join function and make some observations about the process and result. Explore the different types of joins.
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

A second data frame with one row per country, and capital city and language spoken as variables.

``` r
country <- c('Argentina', 'Austria', 'Bangladesh', 'Chad', 
             'Ecuador', 'Gambia', 'Italy', 'Nepal', 
             'New Zealand', 'Switzerland', 'Taiwan', 'United Kingdom')
capital_city <- c('Buenos Aires', 'Vienna', 'Dhaka', "N'Djamena", 
                  'Quito', 'Banjul', 'Rome', 'Kathmandu',
                  'Wellington', 'Bern', 'Taipei', 'London')
language_spoken <- c('Spanish', 'German, Hungarian', 'Bengali',
                     'French, Arabic', 'Spanish', 'English',
                     'Italian', 'Nepali', 'English', 'German, French, Italian, Romansh',
                     'Mandarin Chinese', 'English')

country_data <- data.frame(country, capital_city, language_spoken) 

knitr::kable(country_data)
```

| country        | capital\_city | language\_spoken                 |
|:---------------|:--------------|:---------------------------------|
| Argentina      | Buenos Aires  | Spanish                          |
| Austria        | Vienna        | German, Hungarian                |
| Bangladesh     | Dhaka         | Bengali                          |
| Chad           | N'Djamena     | French, Arabic                   |
| Ecuador        | Quito         | Spanish                          |
| Gambia         | Banjul        | English                          |
| Italy          | Rome          | Italian                          |
| Nepal          | Kathmandu     | Nepali                           |
| New Zealand    | Wellington    | English                          |
| Switzerland    | Bern          | German, French, Italian, Romansh |
| Taiwan         | Taipei        | Mandarin Chinese                 |
| United Kingdom | London        | English                          |

### `left_join()`

``` r
left_join(gapminder, country_data)
```

    ## Joining, by = "country"

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 1,704 x 8
    ##        country continent  year lifeExp      pop gdpPercap capital_city
    ##          <chr>    <fctr> <int>   <dbl>    <int>     <dbl>       <fctr>
    ##  1 Afghanistan      Asia  1952  28.801  8425333  779.4453         <NA>
    ##  2 Afghanistan      Asia  1957  30.332  9240934  820.8530         <NA>
    ##  3 Afghanistan      Asia  1962  31.997 10267083  853.1007         <NA>
    ##  4 Afghanistan      Asia  1967  34.020 11537966  836.1971         <NA>
    ##  5 Afghanistan      Asia  1972  36.088 13079460  739.9811         <NA>
    ##  6 Afghanistan      Asia  1977  38.438 14880372  786.1134         <NA>
    ##  7 Afghanistan      Asia  1982  39.854 12881816  978.0114         <NA>
    ##  8 Afghanistan      Asia  1987  40.822 13867957  852.3959         <NA>
    ##  9 Afghanistan      Asia  1992  41.674 16317921  649.3414         <NA>
    ## 10 Afghanistan      Asia  1997  41.763 22227415  635.3414         <NA>
    ## # ... with 1,694 more rows, and 1 more variables: language_spoken <fctr>

Returns all rows from `gapminder`, and all columns from `gapminder` and `country_data`. Countries that do not appear in `country_data` have an `NA` for `capital_city` and `language_spoken`. Mutating join.

``` r
str(left_join(country_data, gapminder))
```

    ## Joining, by = "country"

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## 'data.frame':    144 obs. of  8 variables:
    ##  $ country        : chr  "Argentina" "Argentina" "Argentina" "Argentina" ...
    ##  $ capital_city   : Factor w/ 12 levels "Banjul","Bern",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ language_spoken: Factor w/ 9 levels "Bengali","English",..: 9 9 9 9 9 9 9 9 9 9 ...
    ##  $ continent      : Factor w/ 5 levels "Africa","Americas",..: 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ year           : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp        : num  62.5 64.4 65.1 65.6 67.1 ...
    ##  $ pop            : int  17876956 19610538 21283783 22934225 24779799 26983828 29341374 31620918 33958947 36203463 ...
    ##  $ gdpPercap      : num  5911 6857 7133 8053 9443 ...

Returns all rows from `country_data`, and all columns from `country_data` and `gapminder`. Only countries that appear in `country_data` are included. Mutating join.

### `inner_join()`

``` r
inner_join(gapminder, country_data)
```

    ## Joining, by = "country"

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 144 x 8
    ##      country continent  year lifeExp      pop gdpPercap capital_city
    ##        <chr>    <fctr> <int>   <dbl>    <int>     <dbl>       <fctr>
    ##  1 Argentina  Americas  1952  62.485 17876956  5911.315 Buenos Aires
    ##  2 Argentina  Americas  1957  64.399 19610538  6856.856 Buenos Aires
    ##  3 Argentina  Americas  1962  65.142 21283783  7133.166 Buenos Aires
    ##  4 Argentina  Americas  1967  65.634 22934225  8052.953 Buenos Aires
    ##  5 Argentina  Americas  1972  67.065 24779799  9443.039 Buenos Aires
    ##  6 Argentina  Americas  1977  68.481 26983828 10079.027 Buenos Aires
    ##  7 Argentina  Americas  1982  69.942 29341374  8997.897 Buenos Aires
    ##  8 Argentina  Americas  1987  70.774 31620918  9139.671 Buenos Aires
    ##  9 Argentina  Americas  1992  71.868 33958947  9308.419 Buenos Aires
    ## 10 Argentina  Americas  1997  73.275 36203463 10967.282 Buenos Aires
    ## # ... with 134 more rows, and 1 more variables: language_spoken <fctr>

Returns all rows from `gapminder` that appear in `country_data`, and all columns from both data frames. Similar to `left_join(country_data, gapminder)`. Mutating join.

``` r
str(inner_join(country_data, gapminder))
```

    ## Joining, by = "country"

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## 'data.frame':    144 obs. of  8 variables:
    ##  $ country        : chr  "Argentina" "Argentina" "Argentina" "Argentina" ...
    ##  $ capital_city   : Factor w/ 12 levels "Banjul","Bern",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ language_spoken: Factor w/ 9 levels "Bengali","English",..: 9 9 9 9 9 9 9 9 9 9 ...
    ##  $ continent      : Factor w/ 5 levels "Africa","Americas",..: 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ year           : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp        : num  62.5 64.4 65.1 65.6 67.1 ...
    ##  $ pop            : int  17876956 19610538 21283783 22934225 24779799 26983828 29341374 31620918 33958947 36203463 ...
    ##  $ gdpPercap      : num  5911 6857 7133 8053 9443 ...

Returns all rows from `country_data` that appear in `gapminder`, and all columns from both data frames. Similar to `left_join(country_data, gapminder)` and `inner_join(gapminder, country_data)`. Mutating join.

### `full_join()`

``` r
full_join(gapminder, country_data)
```

    ## Joining, by = "country"

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 1,704 x 8
    ##        country continent  year lifeExp      pop gdpPercap capital_city
    ##          <chr>    <fctr> <int>   <dbl>    <int>     <dbl>       <fctr>
    ##  1 Afghanistan      Asia  1952  28.801  8425333  779.4453         <NA>
    ##  2 Afghanistan      Asia  1957  30.332  9240934  820.8530         <NA>
    ##  3 Afghanistan      Asia  1962  31.997 10267083  853.1007         <NA>
    ##  4 Afghanistan      Asia  1967  34.020 11537966  836.1971         <NA>
    ##  5 Afghanistan      Asia  1972  36.088 13079460  739.9811         <NA>
    ##  6 Afghanistan      Asia  1977  38.438 14880372  786.1134         <NA>
    ##  7 Afghanistan      Asia  1982  39.854 12881816  978.0114         <NA>
    ##  8 Afghanistan      Asia  1987  40.822 13867957  852.3959         <NA>
    ##  9 Afghanistan      Asia  1992  41.674 16317921  649.3414         <NA>
    ## 10 Afghanistan      Asia  1997  41.763 22227415  635.3414         <NA>
    ## # ... with 1,694 more rows, and 1 more variables: language_spoken <fctr>

Returns all rows and columns from `gapminder` and `country_data`. Countries that do not appear in `country_data` have an `NA` for `capital_city` and `language_spoken`. Similar to `left_join(gapminder, country_data)`. Mutating join.

``` r
str(full_join(country_data, gapminder))
```

    ## Joining, by = "country"

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## 'data.frame':    1704 obs. of  8 variables:
    ##  $ country        : chr  "Argentina" "Argentina" "Argentina" "Argentina" ...
    ##  $ capital_city   : Factor w/ 12 levels "Banjul","Bern",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ language_spoken: Factor w/ 9 levels "Bengali","English",..: 9 9 9 9 9 9 9 9 9 9 ...
    ##  $ continent      : Factor w/ 5 levels "Africa","Americas",..: 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ year           : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp        : num  62.5 64.4 65.1 65.6 67.1 ...
    ##  $ pop            : int  17876956 19610538 21283783 22934225 24779799 26983828 29341374 31620918 33958947 36203463 ...
    ##  $ gdpPercap      : num  5911 6857 7133 8053 9443 ...

Similar result as `full_join(gapminder, country_data)`. Countries that appear in `country_data` are displayed first, followed by the rest of the `gapminder` data. Mutating join.

### `anti_join()`

``` r
anti_join(gapminder, country_data)
```

    ## Joining, by = "country"

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 1,560 x 6
    ##        country continent  year lifeExp      pop gdpPercap
    ##         <fctr>    <fctr> <int>   <dbl>    <int>     <dbl>
    ##  1 Afghanistan      Asia  1952  28.801  8425333  779.4453
    ##  2 Afghanistan      Asia  1957  30.332  9240934  820.8530
    ##  3 Afghanistan      Asia  1962  31.997 10267083  853.1007
    ##  4 Afghanistan      Asia  1967  34.020 11537966  836.1971
    ##  5 Afghanistan      Asia  1972  36.088 13079460  739.9811
    ##  6 Afghanistan      Asia  1977  38.438 14880372  786.1134
    ##  7 Afghanistan      Asia  1982  39.854 12881816  978.0114
    ##  8 Afghanistan      Asia  1987  40.822 13867957  852.3959
    ##  9 Afghanistan      Asia  1992  41.674 16317921  649.3414
    ## 10 Afghanistan      Asia  1997  41.763 22227415  635.3414
    ## # ... with 1,550 more rows

Returns all countries from `gapminder` that are not in `country_data`. Only `gapminder` columns appear. Filtering join.

``` r
str(anti_join(country_data, gapminder))
```

    ## Joining, by = "country"

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## 'data.frame':    0 obs. of  3 variables:
    ##  $ country        : Factor w/ 12 levels "Argentina","Austria",..: 
    ##  $ capital_city   : Factor w/ 12 levels "Banjul","Bern",..: 
    ##  $ language_spoken: Factor w/ 9 levels "Bengali","English",..:

Returns all countries from `country_data` that are not in `gapminder`. Since all countries appear in 'gapminder', a blank data frame is returned. Filtering join.

### `semi_join()`

``` r
semi_join(gapminder, country_data)
```

    ## Joining, by = "country"

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 144 x 6
    ##      country continent  year lifeExp      pop gdpPercap
    ##       <fctr>    <fctr> <int>   <dbl>    <int>     <dbl>
    ##  1 Argentina  Americas  1952  62.485 17876956  5911.315
    ##  2 Argentina  Americas  1957  64.399 19610538  6856.856
    ##  3 Argentina  Americas  1962  65.142 21283783  7133.166
    ##  4 Argentina  Americas  1967  65.634 22934225  8052.953
    ##  5 Argentina  Americas  1972  67.065 24779799  9443.039
    ##  6 Argentina  Americas  1977  68.481 26983828 10079.027
    ##  7 Argentina  Americas  1982  69.942 29341374  8997.897
    ##  8 Argentina  Americas  1987  70.774 31620918  9139.671
    ##  9 Argentina  Americas  1992  71.868 33958947  9308.419
    ## 10 Argentina  Americas  1997  73.275 36203463 10967.282
    ## # ... with 134 more rows

Returns all rows from `gapminder` that are appear in `country_data`. Only `gapminder` columns appear. Filtering join.

``` r
str(semi_join(country_data, gapminder))
```

    ## Joining, by = "country"

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## 'data.frame':    12 obs. of  3 variables:
    ##  $ country        : Factor w/ 12 levels "Argentina","Austria",..: 1 2 3 4 5 6 7 8 9 10 ...
    ##  $ capital_city   : Factor w/ 12 levels "Banjul","Bern",..: 3 11 4 7 8 1 9 5 12 2 ...
    ##  $ language_spoken: Factor w/ 9 levels "Bengali","English",..: 9 5 1 3 9 2 6 8 2 4 ...

Returns all rows from `country_data` that are appear in `country_data`. Only `country_data` columns appear. Filtering join.

Activity 3: `merge()` and `match()`
-----------------------------------

### `merge()`

This function merges two data frames by matching columns or row names. The `all` argument can be used to mimic `dplyr` joins.

``` r
## Similar to full_join(gapminder, country_data)
str(merge(gapminder, country_data, all=TRUE))
```

    ## 'data.frame':    1704 obs. of  8 variables:
    ##  $ country        : Factor w/ 142 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ continent      : Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ year           : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp        : num  28.8 30.3 32 34 36.1 ...
    ##  $ pop            : int  8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
    ##  $ gdpPercap      : num  779 821 853 836 740 ...
    ##  $ capital_city   : Factor w/ 12 levels "Banjul","Bern",..: NA NA NA NA NA NA NA NA NA NA ...
    ##  $ language_spoken: Factor w/ 9 levels "Bengali","English",..: NA NA NA NA NA NA NA NA NA NA ...

``` r
## Similar to inner_join(gapminder, country_data)
str(merge(gapminder, country_data, all=FALSE))
```

    ## 'data.frame':    144 obs. of  8 variables:
    ##  $ country        : Factor w/ 142 levels "Afghanistan",..: 5 5 5 5 5 5 5 5 5 5 ...
    ##  $ continent      : Factor w/ 5 levels "Africa","Americas",..: 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ year           : int  1967 2002 1962 1957 1972 1977 1987 2007 1997 1992 ...
    ##  $ lifeExp        : num  65.6 74.3 65.1 64.4 67.1 ...
    ##  $ pop            : int  22934225 38331121 21283783 19610538 24779799 26983828 31620918 40301927 36203463 33958947 ...
    ##  $ gdpPercap      : num  8053 8798 7133 6857 9443 ...
    ##  $ capital_city   : Factor w/ 12 levels "Banjul","Bern",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ language_spoken: Factor w/ 9 levels "Bengali","English",..: 9 9 9 9 9 9 9 9 9 9 ...

``` r
## Similar to left_join(gapminder, country_data)
str(merge(gapminder, country_data, all.x=TRUE))
```

    ## 'data.frame':    1704 obs. of  8 variables:
    ##  $ country        : Factor w/ 142 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ continent      : Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ year           : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp        : num  28.8 30.3 32 34 36.1 ...
    ##  $ pop            : int  8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
    ##  $ gdpPercap      : num  779 821 853 836 740 ...
    ##  $ capital_city   : Factor w/ 12 levels "Banjul","Bern",..: NA NA NA NA NA NA NA NA NA NA ...
    ##  $ language_spoken: Factor w/ 9 levels "Bengali","English",..: NA NA NA NA NA NA NA NA NA NA ...

``` r
## Similar to inner_join(gapminder, country_data)
str(merge(gapminder, country_data, all.x=FALSE))
```

    ## 'data.frame':    144 obs. of  8 variables:
    ##  $ country        : Factor w/ 142 levels "Afghanistan",..: 5 5 5 5 5 5 5 5 5 5 ...
    ##  $ continent      : Factor w/ 5 levels "Africa","Americas",..: 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ year           : int  1967 2002 1962 1957 1972 1977 1987 2007 1997 1992 ...
    ##  $ lifeExp        : num  65.6 74.3 65.1 64.4 67.1 ...
    ##  $ pop            : int  22934225 38331121 21283783 19610538 24779799 26983828 31620918 40301927 36203463 33958947 ...
    ##  $ gdpPercap      : num  8053 8798 7133 6857 9443 ...
    ##  $ capital_city   : Factor w/ 12 levels "Banjul","Bern",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ language_spoken: Factor w/ 9 levels "Bengali","English",..: 9 9 9 9 9 9 9 9 9 9 ...

### `match()`

This function returns a vector of the postions of the first matches between two data frames.

``` r
match(country_data$country, gapminder$country)
```

    ##  [1]   49   73   97  265  445  553  769 1069 1093 1477 1501 1597

Argentina is first in `country_data`. This shows that Argentina is first found on row 49 in `gapminder`. This function does not join or merge data frames, but provides insight into the position of matching data. It can be used to choose approrpiate join functions and show missing variables with `NA`.
