---
title: "Reproducible Data: Project 1"
output: html_document
---

This document presents the solution for the cousera Reproducible Data Couse project 1.

###Loading and preprocessing the data.


```r
library(plyr)
data<-read.csv(file="activity.csv")
```
###Question: What is the mean total number of steps per day?

```r
byday <- ddply(data,~date,summarise,mean.steps=mean(steps,na.rm=TRUE),total.steps=sum(steps))
hist(byday$total.steps,ylab="Total number of steps per day",main="Histogram of Data")
```

![plot of chunk count.days](figure/count.days-1.png) 

```r
mean.steps<-mean(byday$total.steps,na.rm=TRUE)
median.steps<-median(byday$total.steps,na.rm=TRUE)
```
The mean number of steps per day is 1.0766189 &times; 10<sup>4</sup>. The median number of steps per day is 10765.

###Question: What is the average daily activity pattern?
Here is a graph of the average number of steps taken at each interval accross the days. 

```r
aa <- ddply(data,~interval,summarise,mean=mean(steps,na.rm=TRUE))
plot(aa$interval,aa$mean,type="l",ylab="mean number of steps for all days",xlab="5 min interval")
```

![plot of chunk computemean](figure/computemean-1.png) 

```r
maxmin<-aa$interval[which(aa$mean==max(aa$mean))]
```

The maximum number of steps occurs duing the 835th 5 minute interval. 

### Imputing missing values

```r
NAcount<-length(which(is.na(data$steps)))
```
There are 2304 missing values in the step count data. 

Here is a plot of the histogram of the total steps taken each day and the mean and meadian total steps for each date. The values and histogram were created by substituing all 2304 missing values with the mean value for that time interval accross the remaining days.  


```r
#For each NA substitute the mean for the same 5 min interval
NewData<-data
for (i in 1:nrow(aa)){
  NewData$steps[which(is.na(NewData$steps) & NewData$interval==aa$interval[[i]])]<-aa$mean[[i]]
}
byday <- ddply(NewData,~date,summarise,mean.steps=mean(steps,na.rm=TRUE),total.steps=sum(steps))
hist(byday$total.steps,ylab="Total number of steps per day",main="Histogram of Data")
```

![plot of chunk substitute.days](figure/substitute.days-1.png) 

```r
mean.steps<-mean(byday$total.steps,na.rm=TRUE)
median.steps<-median(byday$total.steps,na.rm=TRUE)
```
The mean number of steps per day is 1.0766189 &times; 10<sup>4</sup>. The median number of steps per day is 1.0766189 &times; 10<sup>4</sup>. The values are the same as they were for the unsubstituted data. Since we substituted the mean values, this makes sense. 

###Question: Are there differences in activity patterns between weekdays and weekends?
The following graph compares average weekday and weekend patterns. 

```r
NewData$day<-weekdays(as.Date(NewData$date))
NewData$day[which(NewData$day=="Monday")]<-"Weekday"
NewData$day[which(NewData$day=="Tuesday")]<-"Weekday"
NewData$day[which(NewData$day=="Wednesday")]<-"Weekday"
NewData$day[which(NewData$day=="Thursday")]<-"Weekday"
NewData$day[which(NewData$day=="Friday")]<-"Weekday"
NewData$day[which(NewData$day=="Saturday")]<-"Weekend"
NewData$day[which(NewData$day=="Sunday")]<-"Weekend"
NewData$day<-as.factor(NewData$day)

daysteps<-ddply(NewData,.(day,interval),summarise,mean.steps=mean(steps,na.rm=TRUE),total.steps=sum(steps))
library(lattice)
xyplot( mean.steps ~ interval| factor(day), data=daysteps,type="l",layout=c(1,2))
```

![plot of chunk weekdays](figure/weekdays-1.png) 


