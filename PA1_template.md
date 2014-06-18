# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
- Load the data (i.e. read.csv())

```r
        setwd("c:/users/qs/desktop/coursera/RepData_PeerAssessment1")
        filename<-unzip("./activity.zip")
        activity<-read.csv(filename,as.is=TRUE)
```
        
- Process/transform the data (if necessary) into a format suitable for your analysis

```r
        activity$date<-as.Date(activity$date,"%Y-%m-%d")
        activity$interval<-as.character(activity$interval)
```

## What is mean total number of steps taken per day?
- Make a histogram of the total number of steps taken each day

```r
        stepsPerDay<-tapply(activity$steps,activity$date,sum,na.rm=TRUE)

        hist(stepsPerDay,xlab="steps per day",main="")
```

![plot of chunk histogram1](figure/histogram1.png) 

- Calculate and report the mean and median total number of steps taken per day

```r
        mean(stepsPerDay)
```

```
## [1] 9354
```

```r
        median(stepsPerDay)
```

```
## [1] 10395
```



## What is the average daily activity pattern?
- Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
                intervalSteps<-tapply(activity$steps,activity$interval,mean,na.rm=TRUE)
                intervalSteps<-intervalSteps[order(as.numeric(names(intervalSteps)))]
                plot(as.numeric(names(intervalSteps)),intervalSteps,xlab="interval",ylab="steps per interval",type="l")
```

![plot of chunk DailyActivity](figure/DailyActivity.png) 
- Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
        names(intervalSteps[intervalSteps==max(intervalSteps)])
```

```
## [1] "835"
```


## Imputing missing values
**Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.**

- Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
                sum(is.na(activity$steps)) 
```

```
## [1] 2304
```
- Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
1. To replace NA, first calculate the mean for this 5-minute interval excluding the NA number
2. Replace NA with the mean of this interval.
        
- Create a new dataset that is equal to the original dataset but with the missing data filled in.
        

```r
        intervalmean<-tapply(activity$steps,activity$interval,function(x) mean(x,na.rm=TRUE))
        for (i in 1:length(activity[,1]))
        {
                if (is.na(activity[i,"steps"]))
                        activity[i,"steps"]<-intervalmean[activity[i,"interval"]]
        }
```
-Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day.                 
Do these values differ from the estimates from the first part of the assignment? 
The histogram of imputed data has less values  at steps 0-5000 interval than that of the data with NA.
        
What is the impact of imputing missing data on the estimates of the total daily number of steps?
The mean and medium values are increased.
        
1. Make a histogram of the total number of steps taken each day

```r
        stepsPerDay2<-tapply(activity$steps,activity$date,sum)
        hist(stepsPerDay2)
```

![plot of chunk histogram2](figure/histogram2.png) 
2.- Calculate and report the mean and median total number of steps taken per day

```r
        mean(stepsPerDay2)
```

```
## [1] 10766
```

```r
        median(stepsPerDay2)
```

```
## [1] 10766
```

```r
        diff<-sum(stepsPerDay2)-sum(stepsPerDay)
        print(diff)
```

```
## [1] 86130
```
        
        

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

- Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.
    

```r
        isWeekDay<-weekdays(activity$date)
        for (i in 1:length(isWeekDay)){
        if ((isWeekDay[i]=="Saturday")|(isWeekDay[i]=="Sunday"))
                activity$weekday[i]<-"weekend"
                else activity$weekday[i]<-"weekday"}
        activity$weekday<-as.factor(activity$weekday)
```

- Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:
        

```r
                intervalSteps2<-tapply(activity$steps,list(activity$interval,activity$weekday),mean)
                intervalSteps2<-intervalSteps2[order(as.numeric(rownames(intervalSteps2))),]
                par(mfcol=c(2,1),mar=c(4,4,2,2))
                plot(as.numeric(rownames(intervalSteps2)),intervalSteps2[,1],type="l",xlab="interval",ylab="steps",main="weekday",ylim=c(1,250))
                par(mar=c(4,4,2,2))
                plot(as.numeric(rownames(intervalSteps2)),intervalSteps2[,2],type="l",xlab="interval",ylab="steps",main="weekend",ylim=c(1,250))
```

![plot of chunk DailyActivity2](figure/DailyActivity2.png) 
