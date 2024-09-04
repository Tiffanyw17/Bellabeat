## CASE STUDY: Analyzing Bellabeat Fitness Data 
**Analysis by: Tiffany White**

**Date: September 1st, 2024**
#
_We will analyze following the six step data analysis process:_

### [Ask](#step-1-ask)
###  [Prepare](#step-2-prepare)
###  [Process](#step-3-processing)
###  [Analyze](#step-4-analyzing)
###  [Share](#step-5-sharing)
###  [Act](#step-6-acting)


Urška Sršen and Sando Mur founded Bellabeat in 2013 to create beautifully designed, health-focused smart products for women. Bellabeat’s marketing strategy includes traditional media and extensive digital marketing through Google Search, social media, YouTube, and Google Display Network ads. Sršen aims to leverage consumer data insights to further enhance their marketing strategy and drive growth. By gathering data on activity, sleep, stress, and reproductive health, Bellabeat empowers women with valuable insights into their health and daily habits.

## Step 1: Ask

## Business Task 
To identify potential opportunities for growth and provide recommendations for the Bellabeat marketing strategy improvement based on trends in smart device usage.

**Questions to explore for the analysis:**

1. What are the emerging patterns in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How might these patterns impact Bellabeat’s customer base??

## Step 2: Preparing

The data being used in this case study can be found here: [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit) CC0: Public Domain, dataset made available through [Mobius](https://www.kaggle.com/arashnic)

The data is stored and uploaded in R Studio. This Kaggle dataset includes personal fitness tracker data from thirty Fitbit users who consented to share minute-level data on physical activity, heart rate, and sleep monitoring. It contains detailed information on daily activity, steps, and heart rate, offering insights into users’ habits.

The data set contains 18 CSV files organized in long format. Here's what we initially know about the data:

1. Data from 30 Fitbit users who consented to share personal tracker data, including minute-level physical activity, heart rate, and sleep monitoring.
2. Data collected by a third party using Amazon Mechanical Turk.
3. Dataset includes multiple fields: daily activity intensity, calories used, daily steps, sleep time, and weight records.
4. Data spans from March 2016 to May 2016, which may not reflect current user habits.

### Loading packages

```
library(tidyverse)
library(ggplot2)
library(dplyr)
library(tidyr)
library(janitor)
library(lubridate) 
```

## Step 3: Processsing 
[Back to top](#introduction)
### Importing the datasets

```
# Read the dataframes
activity <- read_csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
calories <- read_csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/dailyCalories_merged.csv")
intensities <- read_csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/hourlyIntensities_merged.csv")
sleep <- read_csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
weight <- read_csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv")
```

With all the necessary dataframes imported, I am ready to begin data cleaning. I'll review each dataframe to familiarize myself with the data and identify any errors.


```
Activity Data
```


(![first image](https://github.com/user-attachments/assets/3d03059a-1b5f-4380-887c-80e5f73a1230)


```
Weight Data
```

![image 2](https://github.com/user-attachments/assets/2bea8c65-f329-40bd-a183-d7e7aab3b160)



```
The column names 
```
I verified all my column names were correct for both activity and weight

Actvity-

'Id''ActivityDate''TotalSteps''TotalDistance''TrackerDistance'
'LoggedActivitiesDistance''VeryActiveDistance''ModeratelyActiveDistance''
LightActivDistance''SedentaryActiveDistance''VeryActiveMinutes''FairlyActiveMinutes'
'LightlyActiveMinutes''SedentaryMinutes''Calories'

Weight-

'Id''Date''WeightKg''WeightPounds''Fat''BMI''IsManualReport''LogId


 As I closely examined the timestamp data, I encountered a few issues that could potentially affect the integrity of the analysis. To address these concerns, I will be converting the timestamps to date-time format.

```
# intensities
intensities$ActivityHour=as.POSIXct(intensities$ActivityHour, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
intensities$time <- format(intensities$ActivityHour, format = "%H:%M:%S")
intensities$date <- format(intensities$ActivityHour, format = "%m/%d/%y")
# activity
activity$ActivityDate=as.POSIXct(activity$ActivityDate, format="%m/%d/%Y", tz=Sys.timezone())
activity$date <- format(activity$ActivityDate, format = "%m/%d/%y")
# sleep
sleep$SleepDay=as.POSIXct(sleep$SleepDay, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
sleep$date <- format(sleep$SleepDay, format = "%m/%d/%y")
```

With the dates now properly formatted and all the dataframes double-checked for errors, I’m ready to dive into the data and start digging into the details.
<br>
<br>
## Step 4: Analyzing 
[Back to top](#introduction)

First up in the analysis phase, my initial focus will be on assessing how many participants across each category.

```
# Finding number of participants in each category
n_distinct(activity$Id)  
n_distinct(calories$Id)   
n_distinct(intensities$Id)
n_distinct(sleep$Id)
n_distinct(weight$Id)
```

33 <br>
33 <br>
33 <br>
24 <br>
8 <br>

In summarizing the data, it becomes clear that we have 33 participants represented in the activity, calories, and intensities datasets, 24 participants in the sleep dataset, and a notably smaller group of just 8 participants in the weight dataset. The limited representation in the weight dataset highlights the need for additional data to support recommendations or draw definitive conclusions.

```
# checking for significant change in weight
weight%>%
group_by(Id)%>%
summarise(min(WeightKg),max(WeightKg))
```
![Screenshot 2024-09-04 105323](https://github.com/user-attachments/assets/461774ac-07d9-42fa-a3d8-698fbd6cd643)


Since there aren’t any significant weight changes among these 8 participants and the sample size is pretty small, I’ve decided to drop this dataset from the analysis.

Let’s move on and look at the summaries for the other datasets:

```
# activity
activity %>%  
  select(TotalSteps,
         TotalDistance,
         SedentaryMinutes, Calories) %>%
  summary()

# active minutes per category
activity %>%
  select(VeryActiveMinutes, FairlyActiveMinutes, LightlyActiveMinutes) %>%
  summary()

# calories
calories %>%
  select(Calories) %>%
  summary()
# sleep
sleep %>%
  select(TotalSleepRecords, TotalMinutesAsleep, TotalTimeInBed) %>%
  summary()
# weight
weight %>%
  select(WeightKg, BMI) %>%
  summary()


```
```
(Summary)
```

```
```
![Screenshot 2024-09-04 104024](https://github.com/user-attachments/assets/fd5ce7a5-bded-44cc-a90d-70e017228be5)

### What has been confirmed from the summaries:
* Participants are getting about 7 hours of sleep each night on average.
* On average, participants burn around 97 calories per hour.
* The average step count is 7,638 steps per day, which is a bit below the CDC’s recommended 10,000 steps daily.
* Most participants are only lightly active.
* On average, participants spend about 16.5 hours a day being sedentary.

  ### Merging Data

I’ll perform an inner join between the activity and sleep datasets, matching them up by the Id and date columns. I will do this to merge them before we start visualizing the data.
```
merged_data <- merge(sleep, activity, by = c('Id', 'date'))
head(merged_data) 
```
![Screenshot 2024-09-04 162826](https://github.com/user-attachments/assets/c8531a78-9b99-4033-9d59-a1312d1a6d43)

After merging the dataset I can now create the visualization 
## Step 5: Share 
[Back to top](#introduction)
<br>

```
ggplot(data = activity, aes(x = TotalSteps, y = Calories)) + geom_point() + geom_smooth() + labs(title = "Total Steps vs. Calories")
```

![9tes4t4b](https://github.com/user-attachments/assets/f0a5622b-ac1d-401f-bcb8-2b6b8b9b002e)


 As expected, the scatter chart shows a clear connection between steps and calories burned. Basically, the more steps participants take, the more calories they burn.

```
ggplot(data = sleep, aes(x = TotalMinutesAsleep, y = TotalTimeInBed)) + geom_point() + labs(title = "Total time asleep vs Total time in bed")
```

![q9523wbp](https://github.com/user-attachments/assets/7577f618-9dab-43b6-a642-9a7ba8aa4e3f)

There’s a positive link between the total time spent asleep and the time spent in bed. To help users boost their sleep quality. A good feature for better consistency Bellabeat might want to add a feature that lets users customize their sleep schedule.

```
ggplot(data = merged_data, mapping = aes(x = SedentaryMinutes, y = TotalMinutesAsleep)) + 
  geom_point() + labs(title= "Sleep Duration and Sedentary Time")
```

![7hv1h9c3](https://github.com/user-attachments/assets/60e0b97c-45cf-43b2-8fc9-663d693a6fe6)


```
cor(merged_data$TotalMinutesAsleep,merged_data$SedentaryMinutes)
```

-0.59939400560339

The graph shows a negative correlation between sedentary minutes and total sleep time. In other words, the less active participants are, the less sleep they tend to get.



## Step 6: Act 
[Back to top](#introduction)
<br>



### Conclusion and Solution: 

By harnessing data related to activity, sleep, stress, and reproductive health, Bellabeat has empowered its users with actionable insights into their daily habits and overall well-being. With this foundation in mind, there are several opportunities for Bellabeat to further enhance its offerings and marketing strategy, after analyzing this data here's what I  recommend:

1. Promote Healthy Eating Habits:
To support users aiming to lose weight, Bellabeat could introduce meal suggestions within the app, offering ideas for low-calorie breakfast, lunch, and dinner options. This could help users make healthier choices and achieve their weight loss goals.

2. Encourage More Active Lifestyles:
Since most users are lightly active, Bellabeat could implement a progression system in the app, encouraging participants to gradually increase their activity levels. The goal would be to help users transition to becoming at least fairly active.

3. Enhance Sleep Quality:
Improving sleep quality is another area where Bellabeat can make a difference. By adding app notifications that remind users to get enough rest and reduce sedentary time, Bellabeat could help users develop better sleep habits.

4. Step Up Daily Activity:
The data shows that participants average 7,638 steps per day, which is below the CDC’s recommended amount. The CDC notes that taking at least 8,000 steps daily is associated with a significantly lower risk of mortality. Bellabeat could encourage users to meet or exceed this step count by highlighting the associated health benefits.
