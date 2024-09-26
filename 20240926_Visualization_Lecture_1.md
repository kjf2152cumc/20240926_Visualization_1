Visualization Lecture
================
Kaleb J. Frierson
2024-09-26

- [Lecture Notes](#lecture-notes)
- [Coding](#coding)

# Lecture Notes

Exploratory data analysis can mean a lot of things. You’re getting to
know your data! Everything you have done after importing and tidying
data up until you do official testing/analyses.

Looking at data is critical. You will make dozens of graphics for each
dataset. You will be the only person who views a lot of them. A small
subset are for others, but visualizing your data is crucial throughout
your work.

ggplot makes good graphics with relative ease (compared to base R
graphics). gg = grammar of graphics. Conceptual framework for how you
want to construct plots. Requires some learning about what that grammar
is. But once you know it, the infrastructure is the same.

Learning ggplot: lots of online materials. Google is helpful! David
Robinson: “Just discovered that I’ve visited this ggplot2 question 29
times in the last two years.”

Basic graph components: data, aesthetic mappings, geoms

Advanced graph components: facets, scales, statistics

A graph is built by combining these components. Components are
consistent across graph types!

# Coding

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = case_match(
      id, 
      "USW00094728" ~ "CentralPark_NY", 
      "USW00022534" ~ "Molokai_HI",
      "USS0023B17S" ~ "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: /Users/kalebfrierson/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2024-09-26 10:18:50.866904 (8.651)

    ## file min/max dates: 1869-01-01 / 2024-09-30

    ## using cached file: /Users/kalebfrierson/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2024-09-26 10:19:03.825499 (3.932)

    ## file min/max dates: 1949-10-01 / 2024-09-30

    ## using cached file: /Users/kalebfrierson/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2024-09-26 10:19:07.74187 (1.036)

    ## file min/max dates: 1999-09-01 / 2024-09-30

``` r
weather_df
```

    ## # A tibble: 2,190 × 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2021-01-01   157   4.4   0.6
    ##  2 CentralPark_NY USW00094728 2021-01-02    13  10.6   2.2
    ##  3 CentralPark_NY USW00094728 2021-01-03    56   3.3   1.1
    ##  4 CentralPark_NY USW00094728 2021-01-04     5   6.1   1.7
    ##  5 CentralPark_NY USW00094728 2021-01-05     0   5.6   2.2
    ##  6 CentralPark_NY USW00094728 2021-01-06     0   5     1.1
    ##  7 CentralPark_NY USW00094728 2021-01-07     0   5    -1  
    ##  8 CentralPark_NY USW00094728 2021-01-08     0   2.8  -2.7
    ##  9 CentralPark_NY USW00094728 2021-01-09     0   2.8  -4.3
    ## 10 CentralPark_NY USW00094728 2021-01-10     0   5    -1.6
    ## # ℹ 2,180 more rows

Making first plot, note that unless you assign geometry you won’t get
data on the plot.

``` r
ggp_weather_scatterplot = 
  ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point()

ggp_weather_scatterplot
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](20240926_Visualization_Lecture_1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->
Alpha can change shading of points. Color by name adds a key. Geom
smooth fits a smooth line and defaults with standard error which you can
remove with SE = FALSE.

``` r
weather_df |> 
  ggplot(aes(x=tmin, y=tmax, color = name)) +
  geom_point(alpha = .3, size = .8) + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](20240926_Visualization_Lecture_1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->
Where you define aesthetics matters:

``` r
weather_df |> 
  ggplot(aes(x=tmin, y=tmax)) +
  geom_point(aes(color = name), alpha = .3, size = .8) + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](20240926_Visualization_Lecture_1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->
