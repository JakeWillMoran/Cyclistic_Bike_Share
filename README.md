# Cyclistic Bike Share
## Who are Cyclistic?
Cyclistic is a **bike-share program** based in **Chicago (US)** that features more than 5,800 bicycles and 600 docking stations. 

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or ful-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members. 

Cyclistic’s finance analysts have concluded that **annual members are much more profitable than casual riders**. Although the pricing flexibility helps Cyclistic attract more customers, Moreno (Director of Marketing) believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, Moreno believes there is a solid opportunity to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their needs. 

Moreno has set a clear goal: Design marketing strategies aimed at **converting casual riders into annual members**. In order to do that, however, the team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. Moreno and her team are interested in analyzing the Cyclistic historical bike trip data to identify trends.

\* *Cyclistic Bike Share is a fictional company, and all associated data has been produced by Google, through the 'Google Data Analytics Professional Certificate'. The use of this data and subsequent analysis is to serve as an exercise for data analysis.* \*

## The Data
`12 raw data files` have been provied: one for each month in 2023. 

Within each dataset we have a `ride_id` (*each individual ride has a unique ride ID*), the `rideable_type` used (*classic or electric bikes*), the `start_time` and `end_time` of each ride, the start and end locations of each ride: `start_station_name`, `start_station_id`, `end_station_name`, `end_station_id`, `start_lat`, `start_lng`, `end_lat`, `end_lng`, and the associated type of user: `member_casual`.

### Cleaning the data
The first thing I did was transform the data and make sure it was clean. I decided to use `SQL` (*Bigquery*) at this step, however later on I will utilize `R` also. 

Using the `UNION ALL` function in SQL, I merged all 12 tables into 1, containing approximately 5.5 million rows. This table will remain untouched and serve as my raw data file.

From here I can start studying the table and look for any anomolies, mistakes, and/or missing values:
#### Checkign for duplicate ride ID's
Since each individual ride has its own unique ID, there shouldn't be any duplicates. Using SQL, I didn't find any duplicates: Click 'more info' below to see how I did this.
<details>
<summary>More info</summary>
<br>
Using `SELECT COUNT(DISTINCT(ride_id))` to return the number of unique values, and `SELECT COUNT(ride_id)` to return the total number of values, I can see if there is a difference. As both values were the same, I knew that each ride_id was unique.
</details>
