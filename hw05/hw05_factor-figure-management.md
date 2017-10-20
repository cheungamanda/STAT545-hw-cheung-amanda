STAT545 Homework 05: Factor and Figure Management
================

The final homework assignment for STAT545 (until STAT547...), let's do this! :neckbeard:

### Tidyveryse, forcats, knitr

Load tidyverse, forcats, and knitr.

``` r
suppressPackageStartupMessages(library(tidyverse))
suppressPackageStartupMessages(library(forcats))
suppressPackageStartupMessages(library(knitr))
```

### Locate Gapminder data

``` r
(gap_tsv <- system.file("gapminder.tsv", package = "gapminder"))
```

    ## [1] "/Library/Frameworks/R.framework/Versions/3.4/Resources/library/gapminder/gapminder.tsv"

Factor Management
-----------------

### Gapminder version:

### 1. Factorise

Import gapminder data using `read_tsv()`, which reads tab-delimited data.

``` r
gapminder <- read_tsv(gap_tsv)
```

    ## Parsed with column specification:
    ## cols(
    ##   country = col_character(),
    ##   continent = col_character(),
    ##   year = col_integer(),
    ##   lifeExp = col_double(),
    ##   pop = col_integer(),
    ##   gdpPercap = col_double()
    ## )

``` r
str(gapminder, give.attr = FALSE)
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    1704 obs. of  6 variables:
    ##  $ country  : chr  "Afghanistan" "Afghanistan" "Afghanistan" "Afghanistan" ...
    ##  $ continent: chr  "Asia" "Asia" "Asia" "Asia" ...
    ##  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
    ##  $ pop      : int  8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
    ##  $ gdpPercap: num  779 821 853 836 740 ...

Strings are not converted to factors, so let's convert `country` and `continent` to factors!

``` r
gapminder <- gapminder %>%
  mutate(country = factor(country),
         continent = factor(continent))
## as_factor() could also be used in lieu of factor()
## as_factor() is actually safer, since it doesn't change the order of the levels
## however, not important right now since we'll be reordering them later!

str(gapminder)
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    1704 obs. of  6 variables:
    ##  $ country  : Factor w/ 142 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ continent: Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
    ##  $ pop      : int  8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
    ##  $ gdpPercap: num  779 821 853 836 740 ...

Ta da! We now see that `country` is a factor with 142 levels, and `continent` is a factor with 5 levels.

### 2. Drop Oceania

Filter the Gapminder data to remove observations associated with the `continent` of Oceania. Additionally, remove unused factor levels. Provide concrete information on the data before and after removing these rows and Oceania; address the number of rows and the levels of the affected factors.

Before removing Oceania and unused factor levels...

``` r
str(gapminder)
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    1704 obs. of  6 variables:
    ##  $ country  : Factor w/ 142 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ continent: Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
    ##  $ pop      : int  8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
    ##  $ gdpPercap: num  779 821 853 836 740 ...

``` r
nrow(gapminder)
```

    ## [1] 1704

``` r
levels(gapminder$continent)
```

    ## [1] "Africa"   "Americas" "Asia"     "Europe"   "Oceania"

``` r
nlevels(gapminder$continent)
```

    ## [1] 5

``` r
class(gapminder$continent)
```

    ## [1] "factor"

``` r
summary(gapminder$continent)
```

    ##   Africa Americas     Asia   Europe  Oceania 
    ##      624      300      396      360       24

Drop Oceania and unused factor levels.

``` r
new_gap <- gapminder %>%
  filter(continent != "Oceania") %>% 
  droplevels()
```

After removing Oceania and unused factor levels...

``` r
str(new_gap)
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    1680 obs. of  6 variables:
    ##  $ country  : Factor w/ 140 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ continent: Factor w/ 4 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
    ##  $ pop      : int  8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
    ##  $ gdpPercap: num  779 821 853 836 740 ...

``` r
nrow(new_gap)
```

    ## [1] 1680

``` r
levels(new_gap$continent)
```

    ## [1] "Africa"   "Americas" "Asia"     "Europe"

``` r
nlevels(new_gap$continent)
```

    ## [1] 4

``` r
class(new_gap$continent)
```

    ## [1] "factor"

``` r
summary(new_gap$continent)
```

    ##   Africa Americas     Asia   Europe 
    ##      624      300      396      360

### 3. Reorder the levels of `country` or `continent`

Use the forcats package to change the order of the factor levels, based on a principled summary of one of the quantitative variables. Consider experimenting with a summary statistic beyond the most basic choice of the median.

Order gapminder countries by maximum population in descending order. This will display the top 10 countries with the greatest maximum population.

``` r
fct_reorder(gapminder$country, gapminder$pop, max, .desc = TRUE) %>% 
  levels() %>%
  head(10) %>% 
  kable()
```

|               |
|:--------------|
| China         |
| India         |
| United States |
| Indonesia     |
| Brazil        |
| Pakistan      |
| Bangladesh    |
| Nigeria       |
| Japan         |
| Mexico        |

Order gapminder countries by maximum population. This will display the top 10 countries with the smallest maximum population.

``` r
fct_reorder(gapminder$country, gapminder$pop, max) %>% 
  levels() %>%
  head(10) %>%
  kable()
```

|                       |
|:----------------------|
| Sao Tome and Principe |
| Iceland               |
| Djibouti              |
| Equatorial Guinea     |
| Bahrain               |
| Comoros               |
| Montenegro            |
| Reunion               |
| Swaziland             |
| Trinidad and Tobago   |

Let's look at the importance of reordering factor levels! Here is a plot of gapminder European countries in 2002 to population.

``` r
gap_euro <- gapminder %>% filter(year == 2002, continent == "Europe")
ggplot(gap_euro, aes(x = country, y = pop)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](hw05_factor-figure-management_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-10-1.png)

From this plot, we can see that Germany has the greatest population, but it's hard to see the top 10 countries.

``` r
ggplot(gap_euro, aes(x = fct_reorder(country, pop), y = pop)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](hw05_factor-figure-management_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-11-1.png)

Ahhh, much better! Now we can easily differentiate the countries with the greatest population.

### `arrange()`

#### Explore the effects of arrange(). Does merely arranging the data have any effect on, say, a figure?

Arrange population in `gap_euro` which was created above (gapminder European countries in 2002).

``` r
gap_euro2 <- gapminder %>% 
  filter(year == 2002, continent == "Europe") %>% 
  arrange(pop)
```

Let's see how the data has changed before and after `arrange()`.

``` r
## before arrange()
gap_euro %>% head() %>% kable()
```

| country                | continent |  year|  lifeExp|       pop|  gdpPercap|
|:-----------------------|:----------|-----:|--------:|---------:|----------:|
| Albania                | Europe    |  2002|   75.651|   3508512|   4604.212|
| Austria                | Europe    |  2002|   78.980|   8148312|  32417.608|
| Belgium                | Europe    |  2002|   78.320|  10311970|  30485.884|
| Bosnia and Herzegovina | Europe    |  2002|   74.090|   4165416|   6018.975|
| Bulgaria               | Europe    |  2002|   72.140|   7661799|   7696.778|
| Croatia                | Europe    |  2002|   74.876|   4481020|  11628.389|

``` r
gap_euro %>% levels() %>% head()
```

    ## NULL

``` r
head(levels(gap_euro$country)) %>% kable()
```

|             |
|:------------|
| Afghanistan |
| Albania     |
| Algeria     |
| Angola      |
| Argentina   |
| Australia   |

``` r
## after arrange()
gap_euro2 %>% head() %>% kable()
```

| country                | continent |  year|  lifeExp|      pop|  gdpPercap|
|:-----------------------|:----------|-----:|--------:|--------:|----------:|
| Iceland                | Europe    |  2002|   80.500|   288030|  31163.202|
| Montenegro             | Europe    |  2002|   73.981|   720230|   6557.194|
| Slovenia               | Europe    |  2002|   76.660|  2011497|  20660.019|
| Albania                | Europe    |  2002|   75.651|  3508512|   4604.212|
| Ireland                | Europe    |  2002|   77.783|  3879155|  34077.049|
| Bosnia and Herzegovina | Europe    |  2002|   74.090|  4165416|   6018.975|

``` r
gap_euro2 %>% levels() %>% head()
```

    ## NULL

``` r
head(levels(gap_euro2$country)) %>% kable()
```

|             |
|:------------|
| Afghanistan |
| Albania     |
| Algeria     |
| Angola      |
| Argentina   |
| Australia   |

Before `arrange()`, countries were listed in alphabetical order.

After `arrange()`, countiries were listed in increasing order of maximum population.

Let's see how the figure has changed before and after `arrange()`.

``` r
## before arrange()
ggplot(gap_euro, aes(x = country, y = pop)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](hw05_factor-figure-management_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-14-1.png)

``` r
## after arrange()
ggplot(gap_euro2, aes(x = country, y = pop)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](hw05_factor-figure-management_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-14-2.png)

No change! Arranging the data does not have any effect on a figure.

#### Explore the effects of reordering a factor and factor reordering coupled with arrange(). Especially, what effect does this have on a figure?

``` r
## before arrange()
gap_euro_reorder <- gapminder %>% 
  filter(year == 2002, continent == "Europe") %>% 
  mutate(country = fct_reorder(country, pop))

gap_euro_reorder %>% head() %>% kable() ## row order
```

| country                | continent |  year|  lifeExp|       pop|  gdpPercap|
|:-----------------------|:----------|-----:|--------:|---------:|----------:|
| Albania                | Europe    |  2002|   75.651|   3508512|   4604.212|
| Austria                | Europe    |  2002|   78.980|   8148312|  32417.608|
| Belgium                | Europe    |  2002|   78.320|  10311970|  30485.884|
| Bosnia and Herzegovina | Europe    |  2002|   74.090|   4165416|   6018.975|
| Bulgaria               | Europe    |  2002|   72.140|   7661799|   7696.778|
| Croatia                | Europe    |  2002|   74.876|   4481020|  11628.389|

``` r
gap_euro_reorder %>% levels() %>% head() ## level order
```

    ## NULL

``` r
head(levels(gap_euro_reorder$country)) %>% kable() ## level order
```

|                        |
|:-----------------------|
| Iceland                |
| Montenegro             |
| Slovenia               |
| Albania                |
| Ireland                |
| Bosnia and Herzegovina |

``` r
## after arrange()
gap_euro_reorder2 <- gapminder %>% 
  filter(year == 2002, continent == "Europe") %>% 
  mutate(country = fct_reorder(country, pop)) %>% 
  arrange(pop)

gap_euro_reorder2 %>% head() %>% kable() ## row order
```

| country                | continent |  year|  lifeExp|      pop|  gdpPercap|
|:-----------------------|:----------|-----:|--------:|--------:|----------:|
| Iceland                | Europe    |  2002|   80.500|   288030|  31163.202|
| Montenegro             | Europe    |  2002|   73.981|   720230|   6557.194|
| Slovenia               | Europe    |  2002|   76.660|  2011497|  20660.019|
| Albania                | Europe    |  2002|   75.651|  3508512|   4604.212|
| Ireland                | Europe    |  2002|   77.783|  3879155|  34077.049|
| Bosnia and Herzegovina | Europe    |  2002|   74.090|  4165416|   6018.975|

``` r
gap_euro_reorder2 %>% levels() %>% head() ## level order
```

    ## NULL

``` r
head(levels(gap_euro_reorder2$country)) %>% kable() ## level order
```

|                        |
|:-----------------------|
| Iceland                |
| Montenegro             |
| Slovenia               |
| Albania                |
| Ireland                |
| Bosnia and Herzegovina |

Before `arrange()`, the row order did not change and countries were listed in alphabetical order. Level order was changed to countries listed in increasing order of maximum population.

After `arrange()`, the row order and level order were changed to countries were listed in increasing order of maximum population.

Let's see how the figure has changed before and after `arrange()`.

``` r
## before arrange()
ggplot(gap_euro_reorder, aes(x = country, y = pop)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](hw05_factor-figure-management_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-16-1.png)

``` r
## after arrange()
ggplot(gap_euro_reorder2, aes(x = country, y = pop)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](hw05_factor-figure-management_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-16-2.png)

No change! Arranging the data does not have any effect on a figure that has reordered factors.

TALK ABOUT ERROR MESSAGES

File I/O
--------

Experiment with one or more of `write_csv()/read_csv()` (and/or TSV friends), `saveRDS()/readRDS()`, `dput()/dget()`. Create something new, probably by filtering or grouped-summarization of Gapminder. Fiddle with the factor levels, i.e. make them non-alphabetical (see previous section). Explore whether this survives the round trip of writing to file then reading back in.

``` r
## country level summary of maximum population 
gap_max_pop <- gapminder %>%
  filter(year == 2002) %>% 
  group_by(country, continent) %>% 
  summarise(max_pop = max(pop)) %>% 
  ungroup()
gap_max_pop
```

    ## # A tibble: 142 x 3
    ##        country continent   max_pop
    ##         <fctr>    <fctr>     <dbl>
    ##  1 Afghanistan      Asia  25268405
    ##  2     Albania    Europe   3508512
    ##  3     Algeria    Africa  31287142
    ##  4      Angola    Africa  10866106
    ##  5   Argentina  Americas  38331121
    ##  6   Australia   Oceania  19546792
    ##  7     Austria    Europe   8148312
    ##  8     Bahrain      Asia    656397
    ##  9  Bangladesh      Asia 135656790
    ## 10     Belgium    Europe  10311970
    ## # ... with 132 more rows

Reorder the data!

``` r
head(levels(gap_max_pop$country)) ## alphabetical order
```

    ## [1] "Afghanistan" "Albania"     "Algeria"     "Angola"      "Argentina"  
    ## [6] "Australia"

``` r
gap_max_pop <- gap_max_pop %>% 
  mutate(country = fct_reorder(country, max_pop))
head(levels(gap_max_pop$country)) ## increasing order of max pop
```

    ## [1] "Sao Tome and Principe" "Iceland"               "Djibouti"             
    ## [4] "Equatorial Guinea"     "Comoros"               "Bahrain"

``` r
head(gap_max_pop) ## row order not changed, level order changed
```

    ## # A tibble: 6 x 3
    ##       country continent  max_pop
    ##        <fctr>    <fctr>    <dbl>
    ## 1 Afghanistan      Asia 25268405
    ## 2     Albania    Europe  3508512
    ## 3     Algeria    Africa 31287142
    ## 4      Angola    Africa 10866106
    ## 5   Argentina  Americas 38331121
    ## 6   Australia   Oceania 19546792

Write the data out!

``` r
write_csv(gap_max_pop, "gap_max_pop.csv")
saveRDS(gap_max_pop, "gap_max_pop.rds") ## save R object to binary file
dput(gap_max_pop, "gap_max_pop-dput.txt")
```

Read the data!

``` r
gap_max_pop_csv <- read_csv("gap_max_pop.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   country = col_character(),
    ##   continent = col_character(),
    ##   max_pop = col_double()
    ## )

``` r
gap_max_pop_RDS <- readRDS("gap_max_pop.rds")
gap_max_pop_dget <- dget("gap_max_pop-dput.txt")
```

Create a tibble!

``` r
gap_max_pop_csv <- gap_max_pop_csv %>% mutate(country = factor(country))
gap_max_pop_RDS <- gap_max_pop_RDS %>% mutate(country = factor(country))
gap_max_pop_dget <- gap_max_pop_dget %>% mutate(country = factor(country))

country_levels <- tibble(original = head(levels(gap_max_pop$country)))
country_levels <- country_levels %>%
  mutate(via_csv = head(levels(gap_max_pop_csv$country)),
         via_rds = head(levels(gap_max_pop_RDS$country)),
         via_dput = head(levels(gap_max_pop_dget$country)))
kable(country_levels)
```

| original              | via\_csv    | via\_rds              | via\_dput             |
|:----------------------|:------------|:----------------------|:----------------------|
| Sao Tome and Principe | Afghanistan | Sao Tome and Principe | Sao Tome and Principe |
| Iceland               | Albania     | Iceland               | Iceland               |
| Djibouti              | Algeria     | Djibouti              | Djibouti              |
| Equatorial Guinea     | Angola      | Equatorial Guinea     | Equatorial Guinea     |
| Comoros               | Argentina   | Comoros               | Comoros               |
| Bahrain               | Australia   | Bahrain               | Bahrain               |

The original, post-reordering country factor levels are restored using the `saveRDS() / readRDS()` and `dput() / dget()` strategy but revert to alphabetical ordering using `write_csv() / read_csv()`.

Visualization design
--------------------

Remake at least one figure or create a new one, in light of something you learned in the recent class meetings about visualization design and color. Maybe juxtapose your first attempt and what you obtained after some time spent working on it. Reflect on the differences. If using Gapminder, you can use the country or continent color scheme that ships with Gapminder. Consult the guest lecture from Tamara Munzner and everything here.

Remake figure from above!

``` r
ggplot(gap_euro, aes(x = fct_reorder(country, pop), y = pop)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](hw05_factor-figure-management_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-22-1.png)

Clean up your repo!
-------------------

But I want to do more!
----------------------

``` r
country <- c('Argentina', 'Austria', 'Bangladesh', 'Chad', 
             'Ecuador', 'Gambia', 'Italy', 'Nepal', 
             'New Zealand', 'Switzerland', 'Taiwan', 'United Kingdom')
capital_city <- c('Buenos Aires', 'Vienna', 'Dhaka', "N'Djamena", 
                  'Quito', 'Banjul', 'Rome', 'Kathmandu',
                  'Wellington', 'Bern', 'Taipei', 'London')

country_data <- data.frame(country, capital_city)

kable(country_data)
```

| country        | capital\_city |
|:---------------|:--------------|
| Argentina      | Buenos Aires  |
| Austria        | Vienna        |
| Bangladesh     | Dhaka         |
| Chad           | N'Djamena     |
| Ecuador        | Quito         |
| Gambia         | Banjul        |
| Italy          | Rome          |
| Nepal          | Kathmandu     |
| New Zealand    | Wellington    |
| Switzerland    | Bern          |
| Taiwan         | Taipei        |
| United Kingdom | London        |
