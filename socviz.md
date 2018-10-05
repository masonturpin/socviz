Working through socviz by Kieran Healy
================
Mason Turpin
October 3, 2018

Chapter 3
=========

Here's one of the first plots in the book. Note the use of the "scales" library to edit the x axis.

``` r
library(gapminder)
library(tidyverse)
```

    ## -- Attaching packages ---------------------------------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 2.2.1     v purrr   0.2.5
    ## v tibble  1.4.2     v dplyr   0.7.6
    ## v tidyr   0.8.1     v stringr 1.3.1
    ## v readr   1.1.1     v forcats 0.3.0

    ## Warning: package 'dplyr' was built under R version 3.5.1

    ## -- Conflicts ------------------------------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(socviz)
library(scales)
```

    ## Warning: package 'scales' was built under R version 3.5.1

    ## 
    ## Attaching package: 'scales'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     discard

    ## The following object is masked from 'package:readr':
    ## 
    ##     col_factor

``` r
gapminder
```

    ## # A tibble: 1,704 x 6
    ##    country     continent  year lifeExp      pop gdpPercap
    ##    <fct>       <fct>     <int>   <dbl>    <int>     <dbl>
    ##  1 Afghanistan Asia       1952    28.8  8425333      779.
    ##  2 Afghanistan Asia       1957    30.3  9240934      821.
    ##  3 Afghanistan Asia       1962    32.0 10267083      853.
    ##  4 Afghanistan Asia       1967    34.0 11537966      836.
    ##  5 Afghanistan Asia       1972    36.1 13079460      740.
    ##  6 Afghanistan Asia       1977    38.4 14880372      786.
    ##  7 Afghanistan Asia       1982    39.9 12881816      978.
    ##  8 Afghanistan Asia       1987    40.8 13867957      852.
    ##  9 Afghanistan Asia       1992    41.7 16317921      649.
    ## 10 Afghanistan Asia       1997    41.8 22227415      635.
    ## # ... with 1,694 more rows

``` r
p <- ggplot(gapminder, aes(gdpPercap, lifeExp)) + 
  geom_point(alpha = .3) + 
  geom_smooth(method = 'gam') +
  scale_x_log10(labels = scales::dollar)
p
```

![](socviz_files/figure-markdown_github/unnamed-chunk-1-1.png)

The following graph sucks! It's got all these lines! But it's a good illustration of how aesthetics work. Notice how things you put in aes() affect variables, while things you put in geom\_point() affect the whole graph. Compare the first and second graphs.

``` r
p <- ggplot(gapminder, aes(gdpPercap, lifeExp, color = continent)) + 
  geom_point(alpha = .3) + 
  geom_smooth(method = 'gam') +
  scale_x_log10(labels = scales::dollar)
p
```

![](socviz_files/figure-markdown_github/unnamed-chunk-2-1.png)

``` r
p <- ggplot(gapminder, aes(gdpPercap, lifeExp)) + 
  geom_point(alpha = .3, color = "purple") + 
  geom_smooth(method = 'gam') +
  scale_x_log10(labels = scales::dollar)
p
```

![](socviz_files/figure-markdown_github/unnamed-chunk-2-2.png)

Below are two options for the final product I would choose. Note the aes() in geom\_point.

``` r
p <- ggplot(gapminder, aes(gdpPercap, lifeExp)) + 
  geom_point(alpha = .3) + 
  geom_smooth(method = 'gam') +
  scale_x_log10(labels = scales::dollar) +
  labs(x = "GDP Per Capita", y = "Life Expectancy in Year",
       title = "Economic Growth and Life Expectancy",
       subtitle = "Data points are country-years",
       caption = "Source: Gapminder") +
  theme_classic()
p
```

![](socviz_files/figure-markdown_github/unnamed-chunk-3-1.png)

``` r
p <- ggplot(gapminder, aes(gdpPercap, lifeExp)) + 
  geom_point(aes(color = continent)) + 
  geom_smooth(method = 'gam') +
  scale_x_log10(labels = scales::dollar) +
  labs(x = "GDP Per Capita", y = "Life Expectancy in Year",
       title = "Economic Growth and Life Expectancy",
       subtitle = "Data points are country-years",
       caption = "Source: Gapminder") +
  theme_classic()
p
```

![](socviz_files/figure-markdown_github/unnamed-chunk-3-2.png)

Chapter 4
=========

Here's the first plot from chapter 4. An introduction to facetting.

``` r
p <- ggplot(gapminder, aes(year, gdpPercap)) + 
  geom_line(color = 'gray70', aes(group = country)) +
  geom_smooth(size = 1.1, method = 'loess', se = FALSE) +
  scale_y_log10(labels = scales::dollar) +
  facet_wrap(~ continent, ncol = 5) + 
  labs(x = 'Year', y = 'GDP per Capita',
       title = 'GDP per Capita in 5 Continents') +
  theme_minimal()
p
```

![](socviz_files/figure-markdown_github/unnamed-chunk-4-1.png)

Some more facetting.

    ## `geom_smooth()` using method = 'gam'

    ## Warning: Removed 18 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 18 rows containing missing values (geom_point).

![](socviz_files/figure-markdown_github/unnamed-chunk-5-1.png)

This next bit was challenging. Bar charts always give me the most trouble in ggplot, but eventually we got to this facetted version to describe religion in different regions of the US. ![](socviz_files/figure-markdown_github/unnamed-chunk-6-1.png)

This graph includes a nice example of subsetting within a ggplot and shows the utility of changing alpha and using fill in density plots.

``` r
oh_wi <- c("OH", "WI")
p <- ggplot(subset(midwest, subset = state %in% oh_wi), aes(x = area, fill = state, color = state)) +
  geom_density(alpha = .4) +
  theme_minimal()
p
```

![](socviz_files/figure-markdown_github/unnamed-chunk-7-1.png)

Also, one of my more repetitive ggplot2 hangups comes from forgetting stat = 'identity' in bar charts. If your data is already in the summary form of your choice, this setting allows you to graph the data as it comes without ggplot doing any of its default calculations like switching to counts.

Also, you can just use geom\_col(), which assumes stat = 'identity', instead of geom\_bar(). I will likely never remember this.

Here's an example. Note that stat and position are not aesthetics and the theme() comes *after* theme\_minimal().

``` r
p <- ggplot(titanic, aes(x = fate, y = percent, fill = sex)) +
  geom_bar(stat = 'identity', position = 'dodge') +
  theme_minimal() +
  theme(legend.position = 'top')
p
```

![](socviz_files/figure-markdown_github/unnamed-chunk-8-1.png)

This is just a very nice plot to look at. Note guides(fill = FALSE) removes the legend that would normally be generated.

``` r
p <- ggplot(oecd_sum, aes(x = year, y = diff, fill = hi_lo)) +
  geom_col(position = 'identity') +
  guides(fill = FALSE) +
  labs(x = "Year", y = "Difference in Each Year",
       title = "The US Life Expectancy Gap", 
       subtitle = "Difference Between US and OECD Average Life Expectancy, 1960 - 2015", 
       caption = "Source: OECD. Chart inspired by Christopher Ingraham
       Washington Post, December 27th, 2017") +
  theme_minimal()
p
```

    ## Warning: Removed 1 rows containing missing values (geom_col).

![](socviz_files/figure-markdown_github/unnamed-chunk-9-1.png)

Chapter 5
=========
