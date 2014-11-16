# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
## 
## The following object is masked from 'package:stats':
## 
##     filter
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
download.file("https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip", destfile="act.zip",method="curl")
unzip("act.zip")
act<-read.csv("activity.csv")
```


## What is mean total number of steps taken per day?

```r
day<- group_by(act, date)
steps<-summarize(day, steps=sum(steps))
hist(steps$steps)
```

![](./PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

```r
mean(steps$steps, na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(steps$steps, na.rm=TRUE)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

```r
interval<- group_by(act, interval)
time<-summarize(interval, steps=mean(steps, na.rm=TRUE))
plot.ts(time$interval,time$steps)
```

![](./PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

```r
time[time$steps==max(time$steps),]
```

```
## Source: local data frame [1 x 2]
## 
##   interval    steps
## 1      835 206.1698
```


## Imputing missing values

```r
sum(is.na(act$steps))
```

```
## [1] 2304
```

```r
act$steps[is.na(act$steps)]<-time$steps
day<- group_by(act, date)
steps<-summarize(day, steps=sum(steps))
hist(steps$steps)
```

![](./PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

```r
mean(steps$steps, na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(steps$steps, na.rm=TRUE)
```

```
## [1] 10766.19
```


## Are there differences in activity patterns between weekdays and weekends?

```r
library(lubridate)
act$date<-ymd(act$date)

wd<-c("Monday","Tuesday","Wednesday","Thursday","Friday")
we<-c("Saturday","Sunday")
act<-mutate(act, day=weekdays(act$date))
act$day[act$day %in% wd]<-"Weekday"
act$day[act$day %in% we]<-"Weekend"
wdint<- group_by(act[act$day=="Weekday",], interval)
weint<- group_by(act[act$day=="Weekend",], interval)
wdtime<-summarize(wdint, steps=mean(steps, na.rm=TRUE))
wetime<-summarize(weint, steps=mean(steps, na.rm=TRUE))

par(mfrow=c(2,1))
plot.ts(wdtime$interval,wdtime$steps)
plot.ts(wetime$interval,wetime$steps)
```

![](./PA1_template_files/figure-html/unnamed-chunk-5-1.png) 
