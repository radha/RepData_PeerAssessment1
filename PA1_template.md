# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

```r
activities<-read.csv('activity.csv')
```

## What is mean total number of steps taken per day?

```r
library(dplyr)
by_day<-group_by(activities, date)
steps_by_day<-summarize(by_day, steps = sum(steps, na.rm = T))
hist(steps_by_day$steps, main = 'histogram of steps per day', xlab = 'steps')
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

```r
mean(steps_by_day$steps, na.rm = T)
```

```
## [1] 9354.23
```

```r
median(steps_by_day$steps, na.rm = T)
```

```
## [1] 10395
```
## What is the average daily activity pattern?

```r
by_interval<-group_by(activities, interval)
steps_by_interval<-summarize(by_interval, steps = mean(steps, na.rm = T))
plot(steps_by_interval$interval, steps_by_interval$steps, type = 'l', xlab = '5 minute interval', ylab = 'average steps per day')
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

The 5 minute interval with the maximum number of steps (averaged over all days)

```r
steps_by_interval[steps_by_interval$steps==max(steps_by_interval$steps),]$interval
```

```
## [1] 835
```

## Imputing missing values

Total missing values

```r
sum(is.na(activities$steps))
```

```
## [1] 2304
```

Fill missing values in the dataset with the mean for that 5-minute interval

```r
merged = merge(activities, steps_by_interval, by="interval", suffixes=c(".orig", ".mean"))
activities$steps = ifelse(is.na(merged$steps.orig), merged$steps.mean, merged$steps.orig)
```

Histograms of total steps per day after imputing missing values

```r
by_day<-group_by(activities, date)
steps_by_day<-summarize(by_day, steps = sum(steps))
hist(steps_by_day$steps, main = 'histogram of steps per day', xlab = 'steps')
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png) 

```r
mean(steps_by_day$steps, na.rm = T)
```

```
## [1] 10766.19
```

```r
median(steps_by_day$steps, na.rm = T)
```

```
## [1] 10351.62
```

The mean and median are very close to the original estimates with the missing values.

## Are there differences in activity patterns between weekdays and weekends?


```r
activities$wday<-weekdays(as.Date(activities$date))
activities$wday<-ifelse( activities$wday %in% c("Saturday", "Sunday"), "Weekend", "Weekday" )
by_interval_wday<-group_by(activities, interval, wday)
steps_by_interval<-summarize(by_interval_wday, steps = mean(steps, na.rm = T))
library(lattice)
xyplot(steps~interval | factor(wday), data=steps_by_interval, type="l", layout=c(1,2))
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 

There seems to be more activities on weekends.
