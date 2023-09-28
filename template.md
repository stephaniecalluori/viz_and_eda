Viz and Eda1
================

can set global specifications for graphs

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(ggridges)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
```

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: /Users/stephaniecalluori/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-09-28 10:20:07.929139 (8.524)

    ## file min/max dates: 1869-01-01 / 2023-09-30

    ## using cached file: /Users/stephaniecalluori/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-09-28 10:20:18.73663 (3.83)

    ## file min/max dates: 1949-10-01 / 2023-09-30

    ## using cached file: /Users/stephaniecalluori/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-09-28 10:20:22.054464 (0.994)

    ## file min/max dates: 1999-09-01 / 2023-09-30

Let’s make a plot!

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="template_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

# Pipes and stuff

useful to set up as pipe so you can do other functions along with it

``` r
ggp_nyc_weather = 
  weather_df |> 
  filter(name =="CentralPark_NY") |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()

ggp_nyc_weather
```

<img src="template_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

# Fancy plot

color applie to points and smooth curve

``` r
ggplot(weather_df, aes(x = tmin, y = tmax, color = name)) +
  geom_point() + 
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="template_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

color applies just to points and not to smooth

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name)) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="template_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />
se false to get rid of error bars

use alpha shading to make points more transparent; to better see all
data points

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name, alpha = 0.3)) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="template_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

Plot with facets

``` r
ggplot(weather_df, aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.3) +
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="template_files/figure-gfm/unnamed-chunk-8-1.png" width="90%" />

automatically places graphs in alaphabetical order; can alter this

diff fancy plot

``` r
ggplot(weather_df, aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp, alpha = 0.3)) +
  geom_smooth() +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 19 rows containing missing values (`geom_point()`).

<img src="template_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

assignment of specific colors; add to component it applies to; don’t put
on ggplot line bc it thinks there’s a variable called blue do this

``` r
weather_df |> 
  filter(name == "CentralPark_NY") |> 
  ggplot(aes(x = date, y = tmax)) +
  geom_point(color = "blue")
```

<img src="template_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

not this

``` r
weather_df |> 
  filter(name == "CentralPark_NY") |> 
  ggplot(aes(x = date, y = tmax, color = "blue")) +
  geom_point()
```

<img src="template_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />

Jeff rarely assigns specific colors adjusts transparency and size
manually

can use geom_hex if too many points are overlaid kind of like a density
plot

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_hex()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_binhex()`).

<img src="template_files/figure-gfm/unnamed-chunk-12-1.png" width="90%" />

line plot can be helpful when you have repeated measures

``` r
weather_df |> 
  filter(name == "Molokai_HI") |> 
  ggplot(aes(x = date, y = tmax)) +
  geom_line() +
  geom_point()
```

    ## Warning: Removed 1 rows containing missing values (`geom_point()`).

<img src="template_files/figure-gfm/unnamed-chunk-13-1.png" width="90%" />

# Univariate plotting

Histogram

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) +
  geom_histogram(position = "dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

<img src="template_files/figure-gfm/unnamed-chunk-14-1.png" width="90%" />

to compare across groups, Jeff recommends a density plot (a smoothed
over histogram)

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) +
  geom_density(alpha = 0.3, adjust = 2)
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density()`).

<img src="template_files/figure-gfm/unnamed-chunk-15-1.png" width="90%" />

defaults are usually pretty good adjust smooths over double checking can
be helpful

boxplots

``` r
ggplot(weather_df, aes(y = tmax, x = name)) +
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_boxplot()`).

<img src="template_files/figure-gfm/unnamed-chunk-16-1.png" width="90%" />

violin plots (essentially make density plot vertical then you mirror it)

``` r
ggplot(weather_df, aes(y = tmax, x = name)) +
  geom_violin()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

<img src="template_files/figure-gfm/unnamed-chunk-17-1.png" width="90%" />

ridge plot creates 3 separate densites stacked onto each other
vertically; helpful when you many groups to compare

``` r
ggplot(weather_df, aes(x = tmax, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.54

    ## Warning: Removed 17 rows containing non-finite values
    ## (`stat_density_ridges()`).

<img src="template_files/figure-gfm/unnamed-chunk-18-1.png" width="90%" />

## saving and embedding plots

create separate directory where you save results

``` r
ggp_weather = 
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()

ggsave("results/ggp_weather.pdf", ggp_weather)
```

    ## Saving 6 x 3.6 in image

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

ggsave by default tries to save most recent plot; beter to explicitly
named the plot use relative path for where to save, then explicitly cite
path you want to save

R makes a plot then sacles to fit into whatever window we’re intersted
in

``` r
ggp_weather
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="template_files/figure-gfm/unnamed-chunk-20-1.png" width="90%" />
makes wider and taller but then shrinks it down to fit into same window
pane in doc R squeeces graph into whatever fits on the page usually does
like width 6

``` r
ggp_weather
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="template_files/figure-gfm/unnamed-chunk-21-1.png" width="90%" />

aspect ratio is width divded by height; 0.6 means make it a little wider
than it is tall
