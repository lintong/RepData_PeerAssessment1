# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

Read the CSV data directly from the activity.zip file

```r
data <- read.csv(unz("activity.zip", "activity.csv"))
```

Convert the date column to POSIXct

```r
data$date <- as.POSIXct(data$date)
```

Perform a basic inspection of the data

```r
head(data)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```


## What is mean total number of steps taken per day?

Load the required libraries

```r
library(plyr)
library(ggplot2)
```

Generate an aggregate of the steps by day

```r
steps_by_day <- aggregate(data$steps, by = list(data$date), sum, na.rm=TRUE) 
```

Rename the column headers into something human readable

```r
names(steps_by_day) <- c("date","steps")
```

Create and display the plot

```r
steps_by_day_plot <- ggplot(steps_by_day,aes(x = steps)) +
       ggtitle("Steps by day") +
       xlab("steps") + ylab("count") +
       geom_histogram(binwidth = 200)
steps_by_day_plot
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 

Calculate the mean and the median

```r
options(scipen=0, digits=2)
mean_steps_by_day   <- mean(steps_by_day$steps , na.rm = TRUE)
median_steps_by_day <- median(steps_by_day$steps , na.rm = TRUE)
```
The mean steps by day is 9354.23 whilst the median steps by day is 10395

## What is the average daily activity pattern?


Generate a dataset containing the means steps per interval and rename the output columns

```r
mean_interval_steps <- aggregate(data$steps, by = list(data$interval), mean, na.rm=TRUE)
names(mean_interval_steps) <- c("interval","steps")
```

Determine the 5 minute interval with the most steps and determine the max steps

```r
max_steps                <- max(mean_interval_steps$steps);
interval_with_most_steps <- mean_interval_steps[which.max(mean_interval_steps$steps),c("interval")]
```

Plot the output

```r
mean_interval_steps_plot <- ggplot(mean_interval_steps,aes(interval,steps)) + 
                            geom_line() + 
                            annotate("text", x = interval_with_most_steps, y= max_steps, label = "Interval with most steps")
mean_interval_steps_plot 
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 

The 5 minute interval with the most steps is 835

## Imputing missing values

Calculate the number of missing values

```r
sum_missing_values <- sum(is.na(data$steps))
```
The number of missing values is 2304


## Are there differences in activity patterns between weekdays and weekends?
