# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

*   Load the data and transform into a format suitable for my analysis

```r
activity = read.csv("activity.csv")
stepSum <- aggregate(steps~date,data=activity,sum,na.rm=TRUE)
```

## What is mean total number of steps taken per day?

*   Make a histogram of the total number of steps taken each day

```r
hist(stepSum$steps)
```

![](PA1_template_files/figure-html/histogram_of_steps-1.png)

*   Calculate and report the **mean** and **median** total number of steps taken per day

```r
mean(stepSum$steps)
```

```
## [1] 10766.19
```

```r
median(stepSum$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

*   Make a time series plot (i.e. `type = "l"`) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
stepsTS <- aggregate(steps~interval,data=activity,mean,na.rm=TRUE)
plot(steps~interval,data=stepsTS,type="l")
```

![](PA1_template_files/figure-html/timeseries_plot-1.png)

*   Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
stepsTS[which.max(stepsTS$steps),]$interval
```

```
## [1] 835
```

## Imputing missing values

*   Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with `NA`s)

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

*   Use a strategy for filing in all of the missing values with the mean for that 5-minute interval. 

```r
fillMissing <- function(interval){
    stepsTS[stepsTS$interval==interval,]$steps
}
```

*   Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activityNew <- activity  
for(i in 1:nrow(activityNew)){
    if(is.na(activityNew[i,]$steps)){
        activityNew[i,]$steps<-fillMissing(activityNew[i,]$interval)
    }
}
```

*   Make a histogram of the total number of steps taken each day

```r
stepSum2 <- aggregate(steps~date,data=activityNew,sum)
hist(stepSum2$steps)
```

![](PA1_template_files/figure-html/hitogram_no_missing-1.png)

*   Calculate and report the **mean** and **median** total number of steps taken per day. 

```r
mean(stepSum2$steps)
```

```
## [1] 10766.19
```

```r
median(stepSum2$steps)
```

```
## [1] 10766.19
```

*   Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

The **mean** value is the **same** as the missing one because we used the mean value to fill those missing entries. The median value is **a little** different, because it depends on **where the missing values locate**

## Are there differences in activity patterns between weekdays and weekends?

*   Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
activityNew$day <- ifelse(as.POSIXlt(as.Date(activityNew$date))$wday%%6==0,"weekend","weekday")
activityNew$day <- factor(activityNew$day,levels=c("weekday","weekend"))
```

*   Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:

```r
stepsTS2=aggregate(steps~interval+day,activityNew,mean)
library(lattice)
xyplot(steps~interval|factor(day),data=stepsTS2,aspect=1/2,type="l")
```

![](PA1_template_files/figure-html/panel_plot-1.png)
