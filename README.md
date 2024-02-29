# Cyclistic Bike Share
### <ins>About Cyclistic</ins>

Cyclistic is a **bike-share program** based in **Chicago (US)** that features more than 5,800 bicycles and 600 docking stations. 

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or ful-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members. 

Cyclistic’s finance analysts have concluded that **annual members are much more profitable than casual riders**. Although the pricing flexibility helps Cyclistic attract more customers, Moreno (Director of Marketing) believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, Moreno believes there is a solid opportunity to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their needs. 

Moreno has set a clear goal: Design marketing strategies aimed at **converting casual riders into annual members**. In order to do that, however, the team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. Moreno and her team are interested in analyzing the Cyclistic historical bike trip data to identify trends.

\* *Cyclistic Bike Share is a fictional company, and all associated data has been produced by Google, through the [Google Data Analytics Professional Certificate](https://www.coursera.org/professional-certificates/google-data-analytics). The use of this data and subsequent analysis is to serve as an exercise for data analysis. <ins>Moreover, the purpose of this project isn't to display perfect analysis, but to highlight my analytical and technical skills, and my logical way of thinking</ins>* \* 

# The Task 
#### 1. How do annual members and casual riders use Cyclistic bikes differently?
#### 2. Why would casual riders buy Cyclistic annual memberships?
#### 3. How can Cyclistic use digital media to influence casual riders to become members?

# Preparation
`12 raw data files` were provied: one for each month in 2023. 

Within each dataset we have the following variables: `ride_id` (*each individual ride has a unique ride ID*), the `rideable_type` used (*classic or electric bikes*), the `started_at` and `ended_at` timestamp of each ride, the start and end locations of each ride: `start_station_name`, `start_station_id`, `end_station_name`, `end_station_id`, `start_lat`, `start_lng`, `end_lat`, `end_lng`, and the associated type of user: `member_casual`.

### <ins>Cleaning the data</ins>

My first step in the process was merging all 12 data files into 1 table so that I could begin cleaning the data; I decided to use `SQL` (*Bigquery*) at this stage. 

Using the `CREATE TABLE` and `UNION ALL` functions in SQL, I merged all 12 tables into 1, containing approximately 5.5 million rows. This table will remain untouched so to speak, and serve as my raw data file.

From here I can start studying the data and look for any anomolies, duplicates, and/or missing values:
#### 1. Checking for duplicates :heavy_check_mark:
Since each individual ride has its own unique ID, there shouldn't be any duplicates. In SQL, I used `COUNT(DISTINCT(ride_id))` to return the total number of unique ride IDs, and then running `COUNT(ride_id)` I could see the overall total number of IDs. As both values were the same, I knew that each ride ID was unique and therefore, there were no duplicates 

*The other variables may have contained duplicates however given the nature of their data this was not an issue and therefore they did not need to be checked.*

#### 2. Checking for null values :heavy_check_mark:
Here I checked for any null/missing values: Using `COUNT(*)` and `WHERE ("column 1 name") IS NULL OR ("column 2 name") IS NULL etc.` I selected every column and returned a value based on the number of null values for each variable. The results showed that there were no null values except for the station names and id's (*over 1 million missing*). As I still had the complete start and end co-ordinates this wasn't much of a concern. 

*I decided to exclude the station names and id's from the subsequent analysis, given the extent of null values. This would however need to be raised to stakeholders as there is clearly an issue with data collection for these variables.*

#### 3. Checking for mistakes/anomalies :heavy_check_mark:
Running `DISTINCT(rideable_type)` and `DISTINCT(member_casual)` as separate queries, returned the following: `classic_bike`, `electric_bike`, and `casual`, `member`. This verified that there were no extra, missing, or inncorrect values for these variables.

I then looked at the length of the time related variables, to make sure they were all in the same format. I did this by using the `LEN` function, to return the range of characters present in each column. As each column only returned one value, I knew they were in the correct format. 

### <ins>Transforming the data</ins>
As my data was now clean, I could transform the table into a format that would allow for a more efficient and effective analysis. I input the following query:

```sql
CREATE TABLE melodic-eye-414510.cyclistic_bike_share.cleaned_cyclistic_2023 AS

SELECT 
ride_id,
rideable_type,
started_at,
ended_at,
EXTRACT(DATE FROM started_at) AS date, 
EXTRACT(MONTH FROM started_at) AS month,
EXTRACT(TIME FROM started_at) AS start_time,
FORMAT_DATE('%A', DATE(started_at)) AS day_of_week, 
DATETIME_DIFF(ended_at, started_at, SECOND) AS ride_time,
start_lat, 
start_lng,
end_lat,
end_lng,
member_casual 

FROM melodic-eye-414510.cyclistic_bike_share.merged_cyclistic_2023
WHERE DATETIME_DIFF(ended_at, started_at, MINUTE)!=0 
  AND DATETIME_DIFF(ended_at, started_at, MINUTE)>0
```

This created a new table that now included the additional following variables: `date`, `month`, `day_of_the_week`, and `ride_time`. <br />
*The `WHERE` and `DATETIME_DIFF` queries at the end excluded any rows in which the start and end timestamps were the same, or where the start time was greater than the end time (impossible and therefore invalid)*

# Results/findings
\* *All visuals were produced using `Tableau`* \*
### Key findings
#### 1. Members took significantly more rides than casual users in 2023. <br />
<img src="https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/b1ec4285-b43d-4a39-b52b-a8a13c3b24c7" width=40% height=40%> <br />

#### 2. The total number of rides for members peaked in August whereas the total number of rides for casual riders peaked in July. <br />
![Monthly number of rides in 2023 ](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/9c276ae9-c762-48f3-9c44-c14b11a15a26) <br />

#### 3. The total number of rides for members was higher throughout the working week whereas the total number of rides for casual riders was higher on the weekend <br />
![Number of rides per day of the week in 2023](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/03d3e354-e5eb-400c-8c5f-1c7209b10df6)

#### 4. Casual riders number of rides steadily rose from 5am to 5pm whereas members number of rides had peaks at 8am and 8pm <br />
![Hourly number of rides in 2023](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/819e2c85-f084-49f9-996e-56aaa402c5af)

#### 5. On average, casual riders had significantly longer ride times compared to members <br />
<img src= "https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/6ff7779e-fec8-4fa3-8d76-9b9132f99667)" width=40% height=40%> <br />

#### 6. Whereas members average ride time stayed relatively consistent throughout the year, rising slightly into the summer months; casual riders had a drop in March and a steeper incline into the summer months <br />
![Monthly average ride time in 2023 ](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/51e970ad-7423-4ae7-9fe4-9c050265845c)

#### 7. Casual riders relatively had a greater average ride time on the weekend
![Average ride time per day fo the week in 2023 ](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/81643d10-b0fb-4b1a-a958-ccafcccb90d3)

#### 8. There was no clear trend with average ride time per hour of the day. Casual rider data appears anomolous. 
![Hourly average ride time in 2023 ](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/7a0e938a-9f87-41f7-8ffa-e317f4f1fa90)



