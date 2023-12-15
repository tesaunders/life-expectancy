life-expectancy
================

``` r
library(readr)
library(dplyr)
```


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

``` r
library(ggplot2)
```

``` r
life_exp <- read_csv("../data/DP_LIVE_11122023092440528.csv")
```

    Rows: 8449 Columns: 8
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr (6): LOCATION, INDICATOR, SUBJECT, MEASURE, FREQUENCY, Flag Codes
    dbl (2): TIME, Value

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
health_spend <- read_csv("../data/SHA_11122023093838475.csv")
```

    Rows: 23231 Columns: 21
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr (15): HF, Financing scheme, HC, Function, HP, Provider, MEASURE, Measure...
    dbl  (6): TIME, Year, PowerCode Code, Reference Period Code, Reference Perio...

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
exp2020 <- life_exp |> 
  filter(TIME == 2020 & SUBJECT == "TOT") |> 
  group_by(LOCATION) |> 
  summarise(
    years = Value
  )
```

``` r
# Plot per capita government spending on health

spend2020_govt <- health_spend |> 
  filter(
    `Financing scheme` == "Government/compulsory schemes",
    Provider == "All providers",
    Measure == "Per capita, current prices, current PPPs",
  ) |> 
  group_by(LOCATION) |> 
  summarise(
    total = sum(Value)
  )

full <- full_join(spend2020_govt, exp2020,
          by = "LOCATION")

ggplot(full, aes(total, years)) +
  geom_point() +
  theme_classic()
```

![](../figs/govt-expenditure-percapita-1.png)
