# Cyclistic Bike Share
### <ins>About Cyclistic</ins>

Cyclistic is a **bike-share program** based in **Chicago (US)** that features more than 5,800 bicycles and 600 docking stations. 

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or ful-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members. 

Cyclistic’s finance analysts have concluded that **annual members are much more profitable than casual riders**. Although the pricing flexibility helps Cyclistic attract more customers, Moreno (Director of Marketing) believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, Moreno believes there is a solid opportunity to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their needs. 

Moreno has set a clear goal: Design marketing strategies aimed at **converting casual riders into annual members**. In order to do that, however, the team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. Moreno and her team are interested in analyzing the Cyclistic historical bike trip data to identify trends.

\* *Cyclistic Bike Share is a fictional company, and all associated data has been produced by Google, through the [Google Data Analytics Professional Certificate](https://www.coursera.org/professional-certificates/google-data-analytics). The use of this data and subsequent analysis is to serve as an exercise for data analysis. <ins>Moreover, the purpose of this project isn't to display perfect analysis, but to highlight my analytical and technical skills.</ins>* \* 

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
CREATE TABLE "new_table_name" AS

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

FROM "table_name"
WHERE DATETIME_DIFF(ended_at, started_at, MINUTE)!=0 
  AND DATETIME_DIFF(ended_at, started_at, MINUTE)>0
```

This created a new table that now included the additional following variables: `date`, `month`, `day_of_the_week`, and `ride_time`. <br />
*The `WHERE` and `DATETIME_DIFF` queries at the end excluded any rows in which the start and end timestamps were the same, or where the start time was greater than the end time (impossible and therefore invalid)*

# Results/findings
\* *All visuals were produced using `Tableau`* \*
### General results & discussion
- ##### Members took significantly more rides than casual users in 2023 whereas on average, casual riders had significantly longer ride times compared to members. <br />

Total number of rides      |  Average ride time
:-------------------------:|:-------------------------:
![Dashboard 1](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/53ffc217-8df5-49c7-a9e3-3aa4dde000ae) | ![Dashboard 3](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/3dcd58ee-249e-436e-8020-06f7dc738fb2)

- ##### The total number of rides for members peaked in August whereas the total number of rides for casual riders peaked in July. Members average ride time stayed relatively consistent throughout the year, rising slightly into the summer months whereas casual riders had a drop in March and a steeper incline into the summer months <br />
- ##### The total number of rides for members was higher throughout the working week whereas the total number of rides for casual riders was higher on the weekend. Also, casual riders relatively had a greater average ride time on the weekend <br />
- ##### Casual riders number of rides steadily rose from 5am to 5pm whereas members number of rides had peaks at 8am and 5pm. There were no clear trends for hourly average ride times. <br />
Total number of rides      |  Average ride time
:-------------------------:|:-------------------------:
 ![Dashboard 2](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/7641a74d-f7c7-46e1-b718-3e6093ba2913) | ![Dashboard 4](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/9e91ccbc-9eed-4e92-ad79-5fea5663c71e)

- #### Members had a greater number of rides for both classic and electric bikes. Casual riders had significantly longer ride times on classic bikes than members.
Total number of rides      |  Average ride time
:-------------------------:|:-------------------------:
 ![Dashboard 10](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/1807aa10-762e-4258-b487-539654854036) |  ![Dashboard 11](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/9de97cb0-b8ff-4198-a65e-0635f95e8119)

- #### Casual riders tended to opt for electric bikes more often than classic bikes however they spent significantly more time riding classic bikes. Type usage varied slightly by months and, day of the week, and time of day.
Total number of rides (Casual riders)     |  Average ride time (Casual riders)
:-------------------------:|:-------------------------:
![casual users (tot) (1)](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/b6a2ad93-a5ec-483d-b393-ad00abfab3b2) |   ![Casual users (avg) (1)](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/f2e24c42-d8da-4c31-b192-46c897741648)

- #### Members typically used classic bikes as often as electric bikes except for monthly variations. Similar to casual riders, members spent more time on average riding classic bikes, with not much variation around timings (*except for early morning hourly average - see discussion*)
Total number of rides (Members)     |  Average ride time (Members)
:-------------------------:|:-------------------------:
 ![members (tot)](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/ac567815-5e86-4c61-be89-3fc032c17766) |  ![members (avg)](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/9dce80f8-180f-4d5e-a2fe-7eb8d2736ebf)

- #### There was no clear difference in preference for start and end locations between members or casual riders. The city was a hotspot for activty in both groups.
![Chicago Activity](https://github.com/JakeWillMoran/Cyclistic_Bike_Share/assets/161477623/870707df-da28-4723-9522-4a0f68e668b7)

### Discusion 
- As casual riders consist of only 30% of Cyclistic users, this first result is to be expected. Interestingly, despite not using Cyclistic as often, it appears that casual riders are overall spending more time using Cyclistic bikes. One hypothesis is that casual riders are using Cyclistic bikes more for leisure and are therefore spending more time on the bikes. On the other hand, members may be using Cyclistic bikes more often for work and therefore their journeys are more rushed and are less likely to vary. Casual riders having a steeper incline in average ride time into the summer months further supports this hypothesis that casual riders are using Cyclistic bikes for more leisurely purposes. Furthermore, the increase in member activity in the working week supports this, on top of the fact their bike usage is peaking around common working commute times (8am and 5pm). The hourly average ride times amongst both groups suggests that both groups on average are riding for the longest time early in the morning. This will need to be explored in future analyses.
- It appears that both groups opt in for using electric bikes more often and spend more time on average riding classic bikes, however casual users appear to spend on average a *significantly* longer time on classic bikes. This may be due to the typical use of the bikes: electric bikes are often used for assistance rather than leisure, meaning the users may spend less time on average using them. Regardless of the reason of use, casual riders spend longer times on average riding classic bikes, further supporting the hypothesis that they lean towards using Cyclistic bikes for leisure. The peaks in usage that we observe for casual riders numbers of rides at 8am and 5pm may further suggest that electric bikes are typically used for commuting to and from work, even amongst casual users.
- The geographical analysis shows us that casual riders and members don't appear to have a clear preference for where they collect and drop off the Cyclistic bikes.

### Limitations
- We did not have user specific data: user demographics would allow us to gain insights into the types of people who are using Cyclistic casually or as annual members. For instance, we could compare age and employment types between the groups.
- Given the imbalance between total numbers of members and casual riders, the total number of rides of 2023 may be misleading as there are simply a lot more members than casual riders: it doesn't necessarily support the idea that if someone is a member, they are more likely to use Cyclistic more frequently. It is simply showing overall figures.  
- The geographical analysis is limited as it only observes the locations in which the users collect and return their bikes: the docking stations. We therefore do not have data on their journey: where specifically they are spending most of their time on the bikes. This information would give us insights into how both groups use the bikes differently. Moreover, given the missing data for the start and end stations, we could not perform an analysis on this, as it wouldn't accurately reflect the whole population of users. With this data we could've uncovered which stations (instead of general areas in Chicago) both groups tend to use the most (the mode). This information would prove beneficial in the marketing strategies.

### Key takeaways & recommendations
#### 1. How do annual members and casual riders use Cyclistic bikes differently?
- Casual riders on average spend significantly longer periods of time riding Cyclistic bikes (77% greater average ride time).
- Annual members use Cyclistic bikes more often throughout the working week and at common working hours, whereas casual riders use Cyclistic bikes more in the summer months and at the weekend.
- Both groups ride electric bikes more often than classic bikes however casual riders on average ride classic bikes for longer periods of time (230% greater average ride time compared to members).
#### 2. Why would casual riders buy Cyclistic annual memberships?
- I believe we need more data to answer this quesiton. The main reason casual riders would become members is to save money or possibly the convenience of not having the purchase day passes, however this is dependent on their individual frequency of use and other user specific data, of which we don't currently have. 
#### 3. How can Cyclistic use digital media to influence casual riders to become members?
- As it appears that casual riders are typically using Cyclistic for leisure, this should be one primary focus in terms how to influence casual riders to become members. For example, we could increase the advertising of Cyclistic as a bike share company that provides bikes for enjoyment and relaxation, alongside the benefits of becoming a member. On top of this, classic bikes should be a main focus in these efforts, as casual riders spend significantly longer periods of time on classic bikes (*on average*).
- Alternatively, casual riders may not have thought to use Cyclistic bikes for commuting to and from work, or are unaware of the money they would save by becoming members (*dependant on an individual's bike use*). Evidently, from the member data we have observed, members appear to use the Cyclistic primarily for this purpose. We could therefore also advertsie the diverse uses of Cyclistic to casual riders along side the money they may save by using Cyclistic to commute to and from work.
- Overall I believe we need more user specific data to be able to advertise effectively to the right audience.
</br >

Thank you for taking the time to read through my data analysis exercise.</br >

`Jake`
