Assignment
========================================================


```r
opts_chunk$set(echo=TRUE)
```

## Loading and preprocessing the data


```r
Sys.setlocale(category = "LC_TIME", locale = "C")
```

```
## [1] "C"
```

```r
rawdata<-read.csv("activity.csv", head=TRUE, na.strings="NA")
rawdata$date2<-as.Date(rawdata$date, format="%Y-%m-%d")
```

## What is mean total number of steps taken per day?


```r
datagraph<-tapply(rawdata$steps, rawdata$date2, sum)
datagraph2<-data.matrix(datagraph)
hist(datagraph2,xlab = "steps taken each day", main="histogram")
```

![plot of chunk plot histogram](figure/plot histogram.png) 

```r
mean(datagraph2[,1],na.rm =TRUE)
```

```
## [1] 10766
```

```r
median(datagraph2[,1],na.rm =TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern?


```r
library(reshape2)
datagraphi<-rawdata[,c("steps","date2","interval")]
datagraphi1<-melt(datagraphi, id=c("interval","date2"), measure.vars="steps", na.rm=TRUE)
datagraphi2<-dcast(datagraphi1, interval~variable, mean)
with(datagraphi2, plot(interval, steps, pch=1))
```

![plot of chunk pattern](figure/pattern.png) 

```r
a<-max(datagraphi2$steps)
```

## Imputing missing values


```r
sum(is.na(rawdata$steps))
```

```
## [1] 2304
```

```r
fill<-median(datagraph2[,1],na.rm =TRUE)
```

## Are there differences in activity patterns between weekdays and weekends?

```r
rawdata$time <- strptime(paste(rawdata$date, sapply(rawdata$interval, formatC, width = 4, flag = 0)), format = "%Y-%m-%d %H %M")
rawdata$weektime <- as.factor(ifelse(weekdays(rawdata$time) %in% c("Saturday","Sunday"),"weekend", "weekday"))
datagraphw<-rawdata[,c("steps","interval", "weektime")]
split<-split(datagraphw, datagraphw$weektime)
datagraphw1<-melt(split[1], id=c("interval", "weektime"), measure.vars="steps", na.rm=TRUE)
datagraphw2<-acast(datagraphw1, interval~variable, mean)
datagraphw3<-melt(split[2], id=c("interval", "weektime"), measure.vars="steps", na.rm=TRUE)
datagraphw4<-acast(datagraphw3, interval~variable, mean)
plot(datagraphw2,pch=1, xlab="weekday", ylab="Number of steps")
```

![plot of chunk week](figure/week1.png) 

```r
plot(datagraphw4,pch=1, xlab="weekend", ylab="Number of steps")
```

![plot of chunk week](figure/week2.png) 

