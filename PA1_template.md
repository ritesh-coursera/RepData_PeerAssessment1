---
title: "ReproResearch_Proj1"
author: "Ritesh"
date: "January 27, 2018"
output: 
  html_document:
    keep_md: true
    self_contained: true
---



# Week2 Assignment

This is an R Markdown document containing the assignments for the Repoducibale Research (Week 2). 

```r
#loading required libraries.
suppressWarnings(library(dplyr,quietly = TRUE,warn.conflicts = FALSE))
suppressWarnings(library(ggplot2,quietly = TRUE,warn.conflicts = FALSE))
suppressWarnings(library(Hmisc,quietly = TRUE,warn.conflicts = FALSE))
```


##Task 1: Code for reading in the dataset and/or processing the data


```r
#Reading the input file in dataframe X
x <- read.csv("C:/Users/User/Documents/Coursera/Reproducible Research/Week 2 project/repdata%2Fdata%2Factivity/activity.csv")

#grouping the dataframe X by Date
grp1 = group_by(x,date)

#summarizing steps by Date. Now our dataframe is ready for further processing
grp2 = summarise(grp1,SumByDay = sum(steps,na.rm = TRUE))
```


##Task 2: Histogram of the total number of steps taken each day


```r
#Creating Histogram
ggplot(data = grp2, mapping = aes(x = SumByDay)) + geom_histogram()
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/Task%202-1.png)<!-- -->


##Task 3: Mean and median number of steps taken each day


```r
summarise(grp1,meanByDay = mean(steps,na.rm = TRUE),medianByDay = median(steps,na.rm = TRUE))
```

```
## # A tibble: 61 x 3
##    date       meanByDay medianByDay
##    <fct>          <dbl>       <dbl>
##  1 2012-10-01   NaN              NA
##  2 2012-10-02     0.438           0
##  3 2012-10-03    39.4             0
##  4 2012-10-04    42.1             0
##  5 2012-10-05    46.2             0
##  6 2012-10-06    53.5             0
##  7 2012-10-07    38.2             0
##  8 2012-10-08   NaN              NA
##  9 2012-10-09    44.5             0
## 10 2012-10-10    34.4             0
## # ... with 51 more rows
```


##Task 4: Time series plot of the average number of steps taken


```r
#grouping the dataframe X by interval
grp3 = group_by(x,interval)

#Finding mean of steps grouped by interval. Now dataframe is ready for processing
grp4 <- summarise(grp3,meanByIntAndDay = mean(steps,na.rm = TRUE))

#Time series plot with interval against average number of steps per interval
ggplot(grp4,aes(interval,meanByIntAndDay)) + geom_line()
```

![](PA1_template_files/figure-html/Task%204-1.png)<!-- -->


##Task 5: The 5-minute interval that, on average, contains the maximum number of steps


```r
subset(grp4,grp4$meanByIntAndDay ==max(grp4$meanByIntAndDay))
```

```
## # A tibble: 1 x 2
##   interval meanByIntAndDay
##      <int>           <dbl>
## 1      835             206
```


##Task 6: Code to describe and show a strategy for imputing missing data


```r
# copying original dataframe to a new dataset
y = x
#Imputing the average of all steps to missed values
y$steps <- impute(y$steps,mean)
```

##Task 7: Histogram of the total number of steps taken each day after missing values are imputed


```r
#Grouping dataframe y by date and summarizing the steps
grp5 = group_by(y,date);grp6 = summarise(grp5,SumByDay = sum(steps))

# finding mean and median by date
summarise(grp5,meanByDay = mean(steps,na.rm = TRUE),medianByDay = median(steps,na.rm = TRUE))
```

```
## # A tibble: 61 x 3
##    date       meanByDay medianByDay
##    <fct>          <dbl>       <dbl>
##  1 2012-10-01    37.4          37.4
##  2 2012-10-02     0.438         0  
##  3 2012-10-03    39.4           0  
##  4 2012-10-04    42.1           0  
##  5 2012-10-05    46.2           0  
##  6 2012-10-06    53.5           0  
##  7 2012-10-07    38.2           0  
##  8 2012-10-08    37.4          37.4
##  9 2012-10-09    44.5           0  
## 10 2012-10-10    34.4           0  
## # ... with 51 more rows
```

```r
#Histogram of the total number of steps taken each day
ggplot(data = grp6, mapping = aes(x = SumByDay)) + geom_histogram()
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/Task%207-1.png)<!-- -->

##Task 8: Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends


```r
#Adding a new column "day" for weekday and weekend
y$day <- ifelse(weekdays(as.Date(y$date,"%Y-%m-%d")) %in% c("Saturday", "Sunday"), "weekend", "weekday")

# Grouping y by interval and day fields. Next finding mean of steps. 
grptest =  group_by(y,interval,day);grptest1 =  summarise(grptest,meanByIntAndDay = mean(steps))

#Plotting the time series plot "faceted" on day column
ggplot(data = grptest1) + 
geom_line(mapping = aes(x = interval, y = meanByIntAndDay)) + 
facet_wrap(~ day, nrow = 2)
```

![](PA1_template_files/figure-html/Task%208-1.png)<!-- -->

