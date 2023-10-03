Viz and Eda2
================

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
library(patchwork)

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

This results in a dataframe with 2190 observations on six variables.

## Plot 1. Labeling!

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp (Degrees C)",
    color = "Location",
    caption = "Max vs mnin daily temp in three locations; data from rnoaa"
  )
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

# Scales! Helps to control tick marks

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp (Degrees C)",
    color = "Location",
    caption = "Max vs mnin daily temp in three locations; data from rnoaa"
  ) +
  scale_x_continuous(
    breaks = c(-15, 0, 15),
    labels = c("-15 C", "0", "15") +
  scale_y_continuous(
    position = "right",
    trans = "sqrt")
  )
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

You don’t have to do the transformation as part of the plotting, You can
do it as a step in cleaning your data.

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp (Degrees C)",
    color = "Location",
    caption = "Max vs mnin daily temp in three locations; data from rnoaa"
  ) +
  scale_x_continuous(
    breaks = c(-15, 0, 15),
    labels = c("-15 C", "0", "15")) +
  scale_y_continuous(
    position = "right",
    limits = c(20,30))
```

    ## Warning: Removed 1227 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

Better to set limits when clenaing data instead of adding it here.

# Setting color

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp (Degrees C)",
    color = "Location",
    caption = "Max vs mnin daily temp in three locations; data from rnoaa"
  ) +
  scale_color_hue(h = c(100, 300))
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp (Degrees C)",
    color = "Location",
    caption = "Max vs mnin daily temp in three locations; data from rnoaa"
  ) +
  viridis::scale_color_viridis(discrete = TRUE)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

viridis works best for color_blindness and printing in gray scale. Best
to use viridis color palette.

# Themes (just controlling thematic stuff; mnoving things around)

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp (Degrees C)",
    color = "Location",
    caption = "Max vs mnin daily temp in three locations; data from rnoaa"
  ) +
  viridis::scale_color_viridis(discrete = TRUE) +
  theme_bw() +
  theme(legend.position = "bottom")
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-8-1.png" width="90%" />

Theme bw resets how the plot looks so you want to do more thematic
adjustment after theme_bw()

theme_classic() - no gridlines; you should have gridlines
theme_minimal()

# Set preferences at the beginning for theme

library(tidyverse)

knitr::opts_chunk\$set( fig.width = 6, fig.asp = .6, out.width = “90%” )

theme_set(theme_minimal() + theme(legend.position = “bottom”))

options( ggplot2.continuous.colour = “viridis”, ggplot2.continuous.fill
= “viridis” )

scale_colour_discrete = scale_colour_viridis_d scale_fill_discrete =
scale_fill_viridis_d

# Data argument

only on smooth line

``` r
weather_df |> 
  ggplot(aes(x = date, y = tmax)) +
  geom_point(aes(color=name)) +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

smooth line for each subset color name

``` r
weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

can put a diff data frame on top of your graph. useful if you have to do
a more complex summary

``` r
nyc_weather_df <-
  weather_df |> 
  filter(name == "CentralPark_NY")

hawaii_weather_df <-
  weather_df |> 
  filter(name == "Molokai_HI")

ggplot(nyc_weather_df, aes(x = date, y = tmax)) +
  geom_point() +
  geom_line(data = hawaii_weather_df)
```

<img src="viz2_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />

# facet

facet_grid(row ~ columns)

``` r
weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  facet_grid(. ~ name)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-12-1.png" width="90%" />

# patchwork; package puts ggplot graphs next to each other

``` r
ggp_temp_scatter <-
  weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  theme(legend.position = "none")


ggp_prcp_density =
  weather_df |> 
  filter(prcp > 25) |> 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = .5) +
  theme(legend.position = "none")

  
ggp_temp_scatter + ggp_prcp_density
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-13-1.png" width="90%" />

``` r
ggp_tmax_date =
  weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_smooth(se = FALSE) +
  theme(legend.position = "bottom")

ggp_temp_scatter + ggp_prcp_density / ggp_tmax_date
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).
    ## Removed 17 rows containing missing values (`geom_point()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-13-2.png" width="90%" />

## data manipulation

name variable is a character they are strings factor variables are
categorical variabes that have an order this is important bc ggplot has
to figure out on x axis and order of colors; every time you need to plot
characters ggplot makes it factor but orders according to alphabetical
order; in order to get a diff order have to do the following steps

``` r
weather_df |> 
  mutate(
    name = fct_relevel(name, c("Molokai_HI"), "CentralPark_NY", "Waterhole_WA")
  ) |> 
  ggplot(aes(x = name, y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_boxplot()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-14-1.png" width="90%" />

``` r
weather_df |> 
  mutate(
    name = fct_reorder(name, tmax)
  ) |> 
  ggplot(aes(x = name, y = tmax, fill = name)) +
  geom_violin()
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `name = fct_reorder(name, tmax)`.
    ## Caused by warning:
    ## ! `fct_reorder()` removing 17 missing values.
    ## ℹ Use `.na_rm = TRUE` to silence this message.
    ## ℹ Use `.na_rm = FALSE` to preserve NAs.

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

<img src="viz2_files/figure-gfm/unnamed-chunk-14-2.png" width="90%" />

# complicated FAS plot

sketch out plot you want to make first on paper. figure out how to tidy
appropriately

``` r
litters_df = 
  read_csv("data/FAS_litters.csv") |>
  janitor::clean_names() |>
  separate(group, into = c("dose", "day_of_tx"), sep = 3)
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pups_df = 
  read_csv("data/FAS_pups.csv") |>
  janitor::clean_names() |>
  mutate(
    sex = 
      case_match(
        sex, 
        1 ~ "male", 
        2 ~ "female"))
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
fas_df = 
  left_join(pups_df, litters_df, by = "litter_number")

fas_df |> 
  select(dose, day_of_tx, starts_with("pd")) |> 
  pivot_longer(
    pd_ears:pd_walk,
    names_to = "outcome",
    values_to = "pn_day"
  ) |> 
  drop_na() |> 
  mutate(outcome = fct_reorder(outcome, pn_day)) |> 
  ggplot(aes(x = dose, y = pn_day)) +
  geom_violin() +
  facet_grid(day_of_tx ~ outcome)
```

<img src="viz2_files/figure-gfm/unnamed-chunk-15-1.png" width="90%" />
