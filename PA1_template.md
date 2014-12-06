---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---

## Needed libraries

```r
library(lubridate)
library(dplyr)
```

## Loading and preprocessing the data

```r
data <- read.csv("activity.csv")
data[,2] <- ymd(data[,2])
```

## What is mean total number of steps taken per day?

```r
daily_totals <- aggregate(data$steps, by = list(Date = data$date), FUN = sum, na.rm = T)
hist(daily_totals[,2], breaks = 8, col = "steelblue", main = "Total Steps per Day", xlab = "Total 
    Steps per Day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 
### Compute the mean and median

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0    6778   10400    9354   12810   21190
```

## What is the average daily activity pattern?

```r
interval_totals <- aggregate(data$steps, by = list(Interval = data$interval), FUN = mean, na.rm = T)
plot(interval_totals[,1], interval_totals[,2], type = "l", col = "steelblue", main = "Average Steps Per Interval", xlab = "Interval", ylab = "Steps")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 
### Which interval had the highest average activity?

```
##   Interval        x
## 1      835 206.1698
```

## Inputing missing values

```r
is_na <- is.na(data[,1])
na_count <- sum(is_na)
```


## Are there differences in activity patterns between weekdays and weekends?
