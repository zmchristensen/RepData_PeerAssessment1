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
library(ggplot2)
```

## Loading and preprocessing the data

```r
data <- read.csv("activity.csv")
data[,2] <- ymd(data[,2])
```

## What is mean total number of steps taken per day?

```r
## Aggregate the daily totals
daily_totals <- aggregate(data$steps, by = list(Date = data$date), FUN = sum, na.rm = T)

## Create a histogram of Total Steps per Day 
hist(daily_totals[,2], breaks = 8, col = "steelblue", main = "Total Steps per Day", xlab = "Total Steps per Day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

```r
## Compute the mean and median
summary(daily_totals$x)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0    6778   10400    9354   12810   21190
```

## What is the average daily activity pattern?

```r
## Compute the mean steps per interval
interval_means <- aggregate(data$steps, by = list(interval = data$interval), FUN = mean, na.rm = T)

## Create a plot of average steps vs. interval
plot(interval_means[,1], interval_means[,2], type = "l", col = "steelblue", main = "Average Steps Per Interval", xlab = "Interval", ylab = "Steps")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

```r
## Which interval had the highest average activity?
arrange(interval_means, desc(x))[1,]
```

```
##   interval        x
## 1      835 206.1698
```

## Inputing missing values

```r
## Determine which rows contain missing values
is_na <- is.na(data[,1])
```


```
## [1] "There are 2304 missing values"
```


```r
## Join the data and the interval averages so missing values can be 'copied' over
updated_data <- inner_join(data, interval_means)
```

```
## Joining by: "interval"
```

```r
updated_data[is_na,2] <- updated_data[is_na,4]

## Aggregate the daily totals
updated_daily_totals <- aggregate(updated_data$steps, by = list(Date = updated_data$date), FUN = sum, na.rm = T)

## Create a histogram of Total Steps per Day 
hist(updated_daily_totals[,2], breaks = 8, col = "steelblue", main = "Total Steps per Day", xlab = "Total Steps per Day")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png) 

```r
## Compute the mean and median
summary(updated_daily_totals[,2])
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##      41    9819   10770   10770   12810   21190
```

## Are there differences in activity patterns between weekdays and weekends?

```r
## Determine which days are on the weekend
day <- weekdays(updated_data[,3])
weekends <- day == "Saturday" | day == "Sunday"

## Combine the data and days, the rename 'day' to either 'Weekday' or 'Weekend'
week_data <- data.frame(updated_data, day, stringsAsFactors = FALSE)
week_data[!weekends,5] <- "Weekday"
week_data[weekends,5] <- "Weekend"

## Average the steps per interval for 'Weekend' and 'Weekday'
week_totals <- aggregate(week_data$steps, by = list(Day = week_data[,5], Interval = week_data$interval), FUN = mean, na.rm = T)

## Create 2-panel line chart of average steps per interval for each day type
qplot(Interval, x, data = week_totals, geom = "line", facets = Day ~ .)
```

![plot of chunk dayDifferences](figure/dayDifferences-1.png) 

