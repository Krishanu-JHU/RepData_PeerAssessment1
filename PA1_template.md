---
title: "ReproData Course Assignment 1"
output:
  html_document: 
  keep_md: true
---
##  **Title: Personal Activity Monitoring Device Data Analysis**

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

## Loading and pre-processing the data

###  Read the data

```r
library(knitr)
library(dplyr)
library(tidyverse)
activity <- read.table("activity.csv", header= TRUE, sep=",")
```


##  What is mean total number of steps taken per day?

###  Calculate the total number of steps taken per day

```r
SPD <- aggregate(steps ~ date, activity, sum)
```
###  Make a histogram of the total number of steps taken each day

```r
hist(SPD$steps, col="green")
abline(v = median(SPD$steps), col = "magenta", lwd = 4)
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)

###  Calculate and report the median of the total number of steps taken per day

```r
medianSPD = median(SPD$steps)
medianSPD
```

```
## [1] 10765
```

###  Calculate and report the mean of the total number of steps taken per day

```r
meanSPD = mean(SPD$steps)
meanSPD
```

```
## [1] 10766.19
```


## What is the average daily activity pattern?

###  Make a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
ASPI <- aggregate(steps ~ interval, activity, mean, na.rm=TRUE)
```


```r
ggplot(data = ASPI)+
  aes(x=interval,y=steps)+
  geom_line()+
  labs(title= "Average Number of Steps Per Interval")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png)

###  Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
            

```r
result1 <-
  ASPI %>%
  summarize(mInterval = n(),
            msteps = max(steps))
  print(result1)
```

```
##   mInterval   msteps
## 1       288 206.1698
```

          
##  Imputing missing values

###  Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)



```r
missing <- sum(is.na(activity))
print(missing)
```

```
## [1] 2304
```

###  Devise a strategy for filling in all of the missing values in the dataset. Create a new dataset that is equal to the original dataset but with the missing data filled in. 



```r
activity$steps <- as.numeric(activity$steps) 
activity <- activity %>%
        mutate(steps = replace(steps,
                                is.na(steps),
                                mean(steps, na.rm=TRUE)))       
summary(activity)
```

```
##      steps            date              interval     
##  Min.   :  0.00   Length:17568       Min.   :   0.0  
##  1st Qu.:  0.00   Class :character   1st Qu.: 588.8  
##  Median :  0.00   Mode  :character   Median :1177.5  
##  Mean   : 37.38                      Mean   :1177.5  
##  3rd Qu.: 37.38                      3rd Qu.:1766.2  
##  Max.   :806.00                      Max.   :2355.0
```

###   Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?



```r
SPD1 <- aggregate(steps ~ date, activity, sum)
```


```r
hist(SPD1$steps, col="blue")
abline(v = median(SPD$steps), col = "green", lwd = 4)
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png)


```r
medianSPD1 = median(SPD1$steps)
medianSPD1
```

```
## [1] 10766.19
```


```r
meanSPD1 = mean(SPD1$steps)
meanSPD1
```

```
## [1] 10766.19
```


```r
SummaryEstimates <- cbind(medianSPD,medianSPD1, meanSPD, meanSPD1)
SummaryEstimates
```

```
##      medianSPD medianSPD1  meanSPD meanSPD1
## [1,]     10765   10766.19 10766.19 10766.19
```


##  Are there differences in activity patterns between weekdays and weekends?

###  Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
activity$date <- as.Date(activity$date)
weekdays1 <- c('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday')
activity$wDay <- factor((weekdays(activity$date) %in% weekdays1), 
         levels=c(FALSE, TRUE), labels=c('weekend', 'weekday')) 
```
   
###  Make a panel plot containing a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).     


```r
ASPI1 <- aggregate(steps ~ interval + wDay, activity, mean, na.rm=TRUE)
```


```r
ggplot(data = ASPI1)+
  aes(x=interval,y= steps, group = wDay, col = wDay)+
  geom_line()+
  facet_grid(wDay~.)
```

![plot of chunk unnamed-chunk-18](figure/unnamed-chunk-18-1.png)

```r
  labs(title= "Average Steps Per Interval by Days") 
```

```
## $title
## [1] "Average Steps Per Interval by Days"
## 
## attr(,"class")
## [1] "labels"
```

