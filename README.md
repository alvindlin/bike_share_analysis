# Cyclistic Bikeshare Analysis (SQL & Tableau)

## Project Overview

For the Capstone Project of the Google Data Analytics course on Coursera, this case study was performed on a fictional bike-share company called Cyclistic. The data was cleaned, processed and analyzed in PostgreSQL prior to importing it into Excel and Tableau for visualization. The full Tableau dashboard can be found [here](https://public.tableau.com/views/CyclisticBikeshare-GoogleDataAnalyticsCapstoneProject/DashboardFinalV2?:language=en-US&publish=yes&:display_count=n&:origin=viz_share_link). 

The company was found in 2016 in Chicago and has a fleet of 5,824 bicycles that are geotracked and locked across 692 stations across Chicago. Customers who purchase a single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members. The director of marketing has a clear goal: design marketing strategies aimed at converting casual riders into annual members. In order to do that, the marketing analysis team needs to better understand how annual members and casual riders differ. This dashboard will present the trends between casual and annual members in the last year. 

The full dataset can be accessed [here](https://divvy-tripdata.s3.amazonaws.com/index.html). The data has been made available by Motivate International Inc. under this [license](https://ride.divvybikes.com/data-license-agreement). For this study, I used the data from May 2021 to April 2022. 

## Analysis and Challenges

About 5.75 million bike rentals over 12 months were used in this analysis. My goals were to find the difference in behavior between casual and annual members for the following statistics: ride length, number of rides (monthly and weekday), types of bike rentals and the popular bike stations. With this many data points, the data had to be validated for completeness. Analysis log can be found [here](https://github.com/alvindlin/bike_share_analysis/tree/main).

Upon exploring the data, the "Start Station" and "End Station" columns had a total of about 14% of its data to be missing (roughly 800,000 rows). This is well above 5% tolerance range, so I decided to exclude using that data. For this reason, I could not find the popular bike stations. The rest of the other columns I needed to use were complete and had no null or missing values. After data validation, I extracted the following columns for analysis: type of bike rented, time started, time ended and type of member. 

## Results

### 1. Overview
Annual Members made up a majority of the bike rentals.

![Overview](https://user-images.githubusercontent.com/107506192/182290657-2d487684-5b8f-43d1-a3c0-6002fefe52b7.png)

### 2. Ride Length 
Casual Riders had overall longer rides. Over twice as long average ride times than Annual Members throughout all months and days of the week. 

<img width="875" alt="Ride length" src="https://user-images.githubusercontent.com/107506192/182290718-3c37cedd-9410-47a4-a7ca-623dcbafe574.png">

### 3. Total Number of Rides
Ridership for both Annual Members and Casual Riders peak during June to August.  These are the only months where Casual Riders surpass Annual Members in ridership  Casual Riders have the highest number of rides on Saturdays and Sundays whereas Annual Members have the highest number of rideso n Tuesdays and Wednesdays. 

![Total Rides](https://user-images.githubusercontent.com/107506192/182291094-d2962309-948c-43d9-93b8-3916c328139b.png)

### 4. Types of Bikes
Annual Members - A majority of them use Classic Bikes and not a single one of them used Docked Bikes.

Casual Members - Almost half of them use Classic and the other half uses Electric. A small percentage uses docked. 

![Bike types](https://user-images.githubusercontent.com/107506192/182291399-6d9cc896-9a77-4d98-9837-ffd47f48378f.png)

## Proposed Strategies
My Recommendations for the marketing team are as follows:
1. Annual membership deals for the months leading into summer
2. Exclusive deals for members on weekends
