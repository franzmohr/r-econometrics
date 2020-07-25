---
title: "Producing a labour market dashboard using Eurostat data"
author: "Franz X. Mohr"
date: '2020-04-19'
tag:
  - euroarea
  - labour
  - eurostat
---

<!--more-->

Yeah, I know. It's pretty obvious which countries contribute most to euro area GDP growth: the larger economies. But let's do this exercise anyways. We will use the `eurostat` package to obtain data from Eurostat and calculate a euro area member state's contribution to overal euro area GDP growth.


```r
library(dplyr)
library(eurostat)
```

```
## Warning: package 'eurostat' was built under R version 3.6.3
```

```r
library(ggplot2)
library(zoo)
```

### Data

The `eurostat` package comes with a list of euro area countries. This is very useful when you only want to download data for euro area countries.


```r
ctr <- "AT"
```

We use real GDP in million EUR.


```r
unemp <- get_eurostat(id = "une_rt_m",
                      filters = list(s_adj = c("NSA", "SA"),
                                     geo = "AT"),
                      stringsAsFactors = FALSE) %>%
  filter(!is.na(values)) # Drop NAs
```

### Unemployment


```r
temp <- unemp %>%
  filter(s_adj == "NSA",
         sex == "T",
         age %in% c("Y25-74", "Y_LT25"),
         unit == "THS_PER",
         time >= "2019-01-01")

ggplot(temp, aes(x = time, y = values, fill = age)) +
  geom_col()

temp <- unemp %>%
  filter(s_adj == "NSA",
         sex == "T",
         age %in% c("Y25-74", "Y_LT25"),
         unit == "PC_ACT",
         time >= "2019-01-01")

ggplot(temp, aes(x = time, y = values, colour = age)) +
  geom_line()
```

<img src="figure/unnamed-chunk-4-1.png" title="plot of chunk unnamed-chunk-4" alt="plot of chunk unnamed-chunk-4" width="50%" /><img src="figure/unnamed-chunk-4-2.png" title="plot of chunk unnamed-chunk-4" alt="plot of chunk unnamed-chunk-4" width="50%" />

