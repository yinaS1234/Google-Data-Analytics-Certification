# CASE STUDY: Bellabeat Data Analysis-Google Data Analytics Capstone
##### Author: Yina Qiao

##### Date: July 6, 2022

##### [Tableau Dashboard](https://public.tableau.com/app/profile/yina7051/viz/BellabeatCaseStudy_16567238920690/Dashboard1#1)

##### [Tableau Story Presentation to Skateholders](https://public.tableau.com/app/profile/yina7051/viz/BellabeatCaseStudy_16567238920690/Story1)

Langauage used: R


_The case study follows the six steps of data analysis process:_

### [Ask](#1-ask)
### [Prepare](#2-prepare)
### [Process](#3-process)
### [Analyze](#4-analyze)
### [Share](#5-share)
### [Act](#6-act)

## Introduction
Bellabeat, a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the
global smart device market. I've been asked to focus on one of Bellabeat’s products and analyze smart device data to gain insight into how consumers are using their smart devices. These insights I discover will help guide company marketing strategy.

## 1. Ask

The task is to analyze smart device usage data in order to
reveal new growth opportunities for Bellabeat.By looking at these trends, I can provide recommendations for Bellabeat marketing strategy.

Key Stakeholder:
*Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer
*Sando Mur: Mathematician and Bellabeat’s cofounder; key member of the Bellabeat executive team
*Bellabeat marketing analytics team


## 2. Prepare
###   Data Source
-Publicly available on Kaggle: https://www.kaggle.com/arashnic/fitbit FitBit Fitness Tracker Data consists of 18 CSV files
-Data sources consists of 30 FitBit users fitness tracker data from Mobius
-Generated by respondents from a survey via Amazon Mechanical Turk between 12 March 2016 to 12 May 2016.
-Data collected includes physical activity recorded in minutes, heart rate, sleep monitoring, daily activity and steps

### Data Limitations:

-Data was collected in 2016. Users's daily activities, sleeping habits, diet, food consumptions may not be relevant now

-Only 30 users is available.  Sample sizes equal to or greater than 30 are often considered sufficient for the CLT to hold. For more accurate analysis, a larger sample size is prefered. 

-Data was collected from a survey, unable to ascertain its integrity and accuracy.


### Is the data ROCCC (Reliable, Original, Comprehensive, Current, and Cited)?
A good data source should follow ROCCC guidline.

-Reliable — LOW — Not reliable as it only has 30 users data

-Original — LOW — Third party provider (Amazon Mechanical Turk)

-Comprehensive — LOW — The sample size is small and most data is recorded during certain days of the week

-Current — LOW — Not current, and may not be relevant

-Cited — LOW — Collection party unknown


## 3. Process

This process aims to clean data to make sure it is accurate, relevant, complete and free of outlier by the following steps:

   - Explore and observe data

   - Identify and treat missing, null values and duplicates


   - Transform data — format data type, consistent column names, create one merged file

   - Perform preliminary statistical analysis


#### install various R packages
```
install.packages("tidyverse")
install.packages("ggplot2")
install.packages("ggpubr")

library(tidyverse)
library(ggplot2)
library(ggpubr)
```



#### exploring data. import the following dataframes for our analysis
```
dailyActivity <- read_csv("bellebeat data/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
View(dailyActivity)

dailyCalories <- read_csv("bellebeat data/Fitabase Data 4.12.16-5.12.16/dailyCalories_merged.csv")
View(dailyCalories)

dailyIntensities <- read_csv("bellebeat data/Fitabase Data 4.12.16-5.12.16/dailyIntensities_merged.csv")
View(dailyIntensities)

dailySteps <- read_csv("bellebeat data/Fitabase Data 4.12.16-5.12.16/dailySteps_merged.csv")
View(dailySteps)

sleepDay <- read_csv("bellebeat data/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
View(sleepDay)

weightLogInfo <- read_csv("bellebeat data/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv")
View(weightLogInfo)
```

#### closer look at data, check the max, min, mean,number of rows, columns, data type
```
summary(dailyActivity)
str(dailyActivity)

summary(dailyCalories)
str(dailyCalories)

summary(dailyIntensities)
str(dailyIntensities)

summary(dailySteps)
str(dailySteps)

summary(sleepDay)
str(sleepDay)

summary(weightLogInfo)
str(weightLogInfo)
```
<img width="473" alt="Screen Shot 2022-07-07 at 11 04 45 AM" src="https://user-images.githubusercontent.com/108648130/177840310-7be141ca-6a63-4014-97bc-86c77bb09493.png">

<img width="502" alt="Screen Shot 2022-07-07 at 11 05 06 AM" src="https://user-images.githubusercontent.com/108648130/177840316-b684f47f-53ab-4453-8d48-633aaa493957.png">

#### check distinct entries in all columns

```
sapply(dailyActivity, function(x) length(unique(x)))
sapply(dailyCalories, function(x) length(unique(x)))
sapply(dailyIntensities, function(x) length(unique(x)))
sapply(dailySteps, function(x) length(unique(x)))
sapply(sleepDay, function(x) length(unique(x)))
sapply(weightLogInfo, function(x) length(unique(x)))

```

<img width="440" alt="Screen Shot 2022-07-07 at 11 08 10 AM" src="https://user-images.githubusercontent.com/108648130/177840632-e2c51e10-ebbd-4f17-905e-14ad2e4d892c.png">

<img width="492" alt="Screen Shot 2022-07-07 at 11 08 30 AM" src="https://user-images.githubusercontent.com/108648130/177840635-d717ee98-4ad6-4140-99e6-fbaa1d0dd353.png">

There are 33 unique IDs in dailyActivity, 24 unique IDs in sleepDay, 8 unique IDs in weightlogInfor

#### Data cleaning and manipulation

-1.Clean_names() function makes sure that the column names are unique and consistent, contains only characters, numbers, and underscores in the names.

-2.Remove_empty() remove null value in all the rows and columns

-3.Identify then remove duplicates

-4.Convent data variable from character to date in format "%m/%d/%Y" and rename as date

-5. Add weekday column

-6. Create one merge file, clean merge file and then export to tableau for viz


After above data exporation, file dailyActivity, sleepDay and weightLogInfor will be used for this exercise.


```
daily_activity <- clean_names(dailyActivity) %>% 
  remove_empty(which = c("rows")) %>% 
  remove_empty(which = c("cols")) %>% 
  rename(date=activity_date)

duplicated(daily_activity)
```


```
>duplicated(daily_activity)

  [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [11] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [21] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [31] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [41] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [51] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [61] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [71] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [81] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [91] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[101] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[111] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[121] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[131] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[141] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[151] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[161] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[171] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[181] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[191] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[201] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[211] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[221] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[231] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[241] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[251] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[261] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[271] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[281] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[291] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[301] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[311] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[321] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[331] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[341] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[351] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[361] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[371] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[381] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[391] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[401] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[411] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[421] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[431] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[441] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[451] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[461] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[471] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[481] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[491] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[501] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[511] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[521] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[531] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[541] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[551] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[561] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[571] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[581] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[591] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[601] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[611] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[621] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[631] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[641] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[651] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[661] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[671] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[681] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[691] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[701] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[711] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[721] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[731] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[741] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[751] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[761] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[771] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[781] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[791] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[801] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[811] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[821] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[831] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[841] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[851] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[861] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[871] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[881] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[891] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[901] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[911] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[921] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[931] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
```

The result shows no ducplicates in dailyActivity

```
daily_activity$date <- as.Date(daily_activity$date, format="%m/%d/%Y")
daily_activity$weekday <- wday(daily_activity$date, label=TRUE)
```

```
sleep_day <- clean_names(sleepDay) %>%
  remove_empty(which = c("rows")) %>% 
  remove_empty(which = c("cols"))
duplicated(sleep_day)  
```


```
> duplicated(sleep_day)  
  [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [11] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [21] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [31] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [41] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [51] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [61] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [71] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [81] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [91] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[101] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[111] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[121] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[131] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[141] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[151] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[161] FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[171] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[181] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[191] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[201] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[211] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[221] FALSE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE
[231] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[241] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[251] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[261] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[271] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[281] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[291] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[301] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[311] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[321] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[331] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[341] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[351] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[361] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[371] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[381]  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[391] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[401] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[411] FALSE FALSE FALSE
```

The"TRUE" shows there is 3 duplicates, use distinct() to treat it and  extract out the unique values.


```
sleep_Day <- distinct(sleep_day) %>% 
  rename(date=sleep_day)

sleep_Day$date <-as.Date(sleep_Day$date, format="%m/%d/%Y")
```
```
weight_Log <- clean_names(weightLogInfo) %>% 
  remove_empty(which = c("rows")) %>% 
  remove_empty(which = c("cols"))

duplicated(weight_Log)
```
```
> duplicated(weight_Log)
 [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[11] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[21] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[31] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[41] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[51] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[61] FALSE FALSE FALSE FALSE FALSE FALSE FALSE
```

```
weight_Log$date <- as.Date(weight_Log$date, format="%m/%d/%Y")

```


Create a one merge file, merge daily_activity,sleep_Day, weight_Log  by "id","date"


```
merge_data1 <-merge(daily_activity,sleep_Day,by=c("id","date"), all=TRUE)
merge_data <- merge(merge_data1,weight_Log,by=c("id","date"), all=TRUE)
```


Use the above steps to clean and explore the merge_data file


```
duplicated(merge_data)
str(merge_data)
sapply(merge_data, function(x) length(unique(x)))
```


```
> duplicated(merge_data)
  [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [11] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [21] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [31] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [41] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [51] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [61] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [71] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [81] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [91] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[101] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[111] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[121] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[131] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[141] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[151] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[161] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[171] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[181] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[191] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[201] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[211] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[221] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[231] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[241] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[251] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[261] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[271] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[281] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[291] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[301] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[311] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[321] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[331] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[341] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[351] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[361] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[371] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[381] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[391] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[401] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[411] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[421] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[431] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[441] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[451] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[461] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[471] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[481] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[491] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[501] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[511] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[521] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[531] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[541] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[551] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[561] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[571] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[581] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[591] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[601] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[611] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[621] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[631] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[641] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[651] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[661] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[671] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[681] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[691] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[701] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[711] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[721] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[731] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[741] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[751] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[761] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[771] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[781] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[791] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[801] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[811] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[821] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[831] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[841] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[851] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[861] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[871] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[881] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[891] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[901] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[911] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[921] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[931] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
> str(merge_data)
'data.frame':	940 obs. of  25 variables:
 $ id                        : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
 $ date                      : Date, format: "2016-04-12" ...
 $ total_steps               : num  13162 10735 10460 9762 12669 ...
 $ total_distance            : num  8.5 6.97 6.74 6.28 8.16 ...
 $ tracker_distance          : num  8.5 6.97 6.74 6.28 8.16 ...
 $ logged_activities_distance: num  0 0 0 0 0 0 0 0 0 0 ...
 $ very_active_distance      : num  1.88 1.57 2.44 2.14 2.71 ...
 $ moderately_active_distance: num  0.55 0.69 0.4 1.26 0.41 ...
 $ light_active_distance     : num  6.06 4.71 3.91 2.83 5.04 ...
 $ sedentary_active_distance : num  0 0 0 0 0 0 0 0 0 0 ...
 $ very_active_minutes       : num  25 21 30 29 36 38 42 50 28 19 ...
 $ fairly_active_minutes     : num  13 19 11 34 10 20 16 31 12 8 ...
 $ lightly_active_minutes    : num  328 217 181 209 221 164 233 264 205 211 ...
 $ sedentary_minutes         : num  728 776 1218 726 773 ...
 $ calories                  : num  1985 1797 1776 1745 1863 ...
 $ weekday                   : Ord.factor w/ 7 levels "Sun"<"Mon"<"Tue"<..: 3 4 5 6 7 1 2 3 4 5 ...
 $ total_sleep_records       : num  1 2 NA 1 2 1 NA 1 1 1 ...
 $ total_minutes_asleep      : num  327 384 NA 412 340 700 NA 304 360 325 ...
 $ total_time_in_bed         : num  346 407 NA 442 367 712 NA 320 377 364 ...
 $ weight_kg                 : num  NA NA NA NA NA NA NA NA NA NA ...
 $ weight_pounds             : num  NA NA NA NA NA NA NA NA NA NA ...
 $ fat                       : num  NA NA NA NA NA NA NA NA NA NA ...
 $ bmi                       : num  NA NA NA NA NA NA NA NA NA NA ...
 $ is_manual_report          : logi  NA NA NA NA NA NA ...
 $ log_id                    : num  NA NA NA NA NA NA NA NA NA NA ...
> sapply(merge_data, function(x) length(unique(x)))
                        id                       date 
                        33                         31 
               total_steps             total_distance 
                       842                        615 
          tracker_distance logged_activities_distance 
                       613                         19 
      very_active_distance moderately_active_distance 
                       333                        211 
     light_active_distance  sedentary_active_distance 
                       491                          9 
       very_active_minutes      fairly_active_minutes 
                       122                         81 
    lightly_active_minutes          sedentary_minutes 
                       335                        549 
                  calories                    weekday 
                       734                          7 
       total_sleep_records       total_minutes_asleep 
                         4                        257 
         total_time_in_bed                  weight_kg 
                       243                         35 
             weight_pounds                        fat 
                        35                          3 
                       bmi           is_manual_report 
                        37                          3 
                    log_id 
                        57 

```

Write_csv() export the merge_data into a csv file, ready to upload to tableau public for visualization.

```
write_csv(merge_data, "merge_data.csv")
```




## 4. Analyze


-  [Summary](#summary)
-  [Active Minutes](#active-minutes)
-  [Total Steps](#total-steps)
-  [Sleep](#Sleep)




###. summary record, summary

ggplot(data=merge_data, aes(x=weekday))+
  geom_bar(fill="steelblue")


###. active minutes

###. total steps

###. sleep







## 5. Share 






## 6. Act

Supporting visualizations and key findings


