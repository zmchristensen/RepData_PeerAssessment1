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
hist(daily_totals[,2], breaks = 8, col = "steelblue", main = "Total Steps per Day", xlab = "Total Steps per Day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 
### Compute the mean and median

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0    6778   10400    9354   12810   21190
```

## What is the average daily activity pattern?

```r
interval_means <- aggregate(data$steps, by = list(interval = data$interval), FUN = mean, na.rm = T)
plot(interval_means[,1], interval_means[,2], type = "l", col = "steelblue", main = "Average Steps Per Interval", xlab = "Interval", ylab = "Steps")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 
### Which interval had the highest average activity?

```
##   interval        x
## 1      835 206.1698
```

## Inputing missing values

```r
is_na <- is.na(data[,1])
na_count <- sum(is_na)

updated_data <- inner_join(data, interval_means)
```

```
## Joining by: "interval"
```

```r
updated_data[is_na,2] <- updated_data[is_na,4]
updated_daily_totals <- aggregate(updated_data$steps, by = list(Date = updated_data$date), FUN = sum, na.rm = T)
hist(updated_daily_totals[,2], breaks = 8, col = "steelblue", main = "Total Steps per Day", xlab = "Total Steps per Day")
```

![plot of chunk missingValues](figure/missingValues-1.png) 

```r
summary(updated_daily_totals[,2])
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##      41    9819   10770   10770   12810   21190
```

## Are there differences in activity patterns between weekdays and weekends?

```r
days <- weekdays(updated_data[,3])
weekdays <- days == "Monday" | days == "Tuesday" | days == "Wednesday" | days == "Thursday" | days == "Friday"

week_data <- data.frame(updated_data, days, stringsAsFactors = FALSE)
week_data[weekdays,5] <- "Weekday"
week_data[!weekdays,5] <- "Weekend"

week_totals <- aggregate(week_data$steps, by = list(Day = week_data[,5], Interval = week_data$interval), FUN = mean, na.rm = T)

weekend <- filter(week_totals, Day == "Weekend")
weekday <- filter(week_totals, Day == "Weekday")
plot(weekend$Interval, weekend$x, type = "l", col = "steelblue", main = "Average Steps Per Interval", xlab = "Interval", ylab = "Steps")
lines(weekday$Interval, weekday$x, col = "seagreen")
legend("topright", lwd = 1, legend = c("Weekend", "Weekday"), col = c("steelblue", "seagreen"))
```

![plot of chunk dayDifferences](figure/dayDifferences-1.png) 

