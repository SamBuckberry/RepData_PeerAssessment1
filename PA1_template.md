# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data


```r
dat <- read.csv(unzip(zipfile="activity.zip"))
```

## What is mean total number of steps taken per day?

First, the data was reshaped to calculate the total number of steps taken per day:

```r
library(reshape2)
dateMelt <- melt(dat[ ,1:2], id=c("date"))
steps <- dcast(dateMelt, date ~ variable, sum)
```

1. Histogram of the total number of steps taken per day

```r
hist(steps$steps, xlab="Number of steps per day",
     main="Histogram of number of steps taken per day",
     col="blue", breaks=10)
```

![plot of chunk plotHisogram](figure/plotHisogram.png) 

2. Mean and median total number of steps taken per day

```r
mean(steps$steps, na.rm=TRUE)
```

```
## [1] 10766
```


```r
median(steps$steps, na.rm=TRUE)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

First, the data was melted on the "interval" variable and cast to calculate statistics for each interval. 

```r
intervalMelt <- melt(dat, id=c("interval", "date"))
intervalMean <- dcast(intervalMelt, interval ~ variable, mean, na.rm=TRUE)
```

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
plot(intervalMean$interval, intervalMean$steps, type="l", xlab="Time interval", ylab="Mean number of steps", col="red")
```

![plot of chunk intervalTimeSeriesPlot](figure/intervalTimeSeriesPlot.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
intervalMean[which.max(intervalMean$steps), ]
```

```
##     interval steps
## 104      835 206.2
```

## Imputing missing values

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

Total number of missing values:

```r
sum(is.na(x=dat))
```

```
## [1] 2304
```

Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc. Create a new dataset that is equal to the original dataset but with the missing data filled in

Missing values were imputed by taking the mean for the 5-minute interval over all days using the `impute.mean()` function in the HotDeckImputation package.


```r
library(HotDeckImputation)
melted <- melt(dat, id=c("date", "interval"))
DI <- acast(melted, date ~ interval)
imputed.dat <- impute.mean(DI)
rownames(imputed.dat) <- rownames(DI); colnames(imputed.dat) <- colnames(DI)
head(imputed.dat[1:5, 1:5])
```

```
##                 0      5     10     15      20
## 2012-10-01  1.717 0.3396 0.1321 0.1509 0.07547
## 2012-10-02  0.000 0.0000 0.0000 0.0000 0.00000
## 2012-10-03  0.000 0.0000 0.0000 0.0000 0.00000
## 2012-10-04 47.000 0.0000 0.0000 0.0000 0.00000
## 2012-10-05  0.000 0.0000 0.0000 0.0000 0.00000
```


Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

![plot of chunk imputedHistogram](figure/imputedHistogram.png) 

Mean number of steps taken per day using data with imputed missing values:

```
## [1] 10766
```

Median number of steps taken per day using data with imputed missing values:

```
## [1] 10766
```

The imputation has not shifted the absolute mean value, but has slightly increased the median. 

## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.




```
## Using weekend as id variables
```

![plot of chunk plotActivityWeekdayWeekend](figure/plotActivityWeekdayWeekend.png) 


Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:

