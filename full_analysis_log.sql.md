#Cyclistic Bikeshare Analysis Log - Google Data Analytics Capstone Project, June 2022

Goal: Extract, clean and analyze the last 12 months of cyclist data to see trends and differences in Casual Riders and Annual Members.
Analyses was performed in PostgreSQL.

## Part A: Create Database and Cleaning

### Step A1 - Create Table for Data Import 

Create columns for data import for all 12 months. "April 2022" will be used for this example.

```
CREATE TABLE april_2022
(
	ride_id varchar,
	rideable_type varchar,
	started_at timestamp without time zone,
	ended_at timestamp without time zone,
	start_station_name varchar,
	start_station_id varchar,
	end_station_name varchar,
	end_station_id varchar,
	start_lat numeric,
	start_lng numeric,
	end_lat numeric,
	end_lng numeric,
	member_casual varchar)
;
```

Once tables were created, files are imported from each month in psql. Repeat process until tables for all 12 months are created.

```
\COPY april_2022 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202204-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 371249
\COPY march_2022 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202203-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 284042
\COPY february_2022 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202202-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 115609
\COPY january_2022 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202201-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 103770
\COPY december_2021 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202112-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 247540
\COPY november_2021 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202111-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 359978
\COPY october_2021 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202110-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 631226
\COPY september_2021 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202109-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 756147
\COPY august_2021 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202108-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 804352
\COPY july_2021 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202107-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 822410
\COPY june_2021 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202106-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 729595
\COPY may_2021 FROM '/Users/alvinlin/Desktop/COURSERA/Case Study 1 - Cyclists/202105-divvy-tripdata.csv'with DELIMITER ',' CSV HEADER;
-- RESULT: COPY 531633
```

Combine data from all 12 months. For this analysis, I will only need the following columns: rideable type, member type, start time, end time, start station name and end station name. 

```
CREATE TABLE cyclist_data_compiled
(
	rideable_type varchar,
	member_casual varchar,
	started_at timestamp without time zone,
	ended_at timestamp without time zone,
	start_station_name varchar,
	end_station_name varchar)
;

-- Extract the desired columns from all 12 months.

INSERT INTO cyclist_data_compiled(rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name)

SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM april_2022 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM march_2022 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM february_2022 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM january_2022 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM december_2021 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM november_2021 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM october_2021 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM september_2021 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM august_2021 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM july_2021 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM june_2021 UNION ALL
SELECT rideable_type, member_casual, started_at, ended_at, start_station_name, end_station_name FROM may_2021

ORDER BY started_at DESC
;
```
### Step A2 - Data Validation and Cleaning 

Count number of rows cross check this with the sum of the results when importing tables.
```
SELECT COUNT(*) FROM cyclist_data_compiled
-- Result: 5,757,551 Rows. Import successful, no rows lost. 
```

Check for Null Values or Empty Cells. 
Repeat for each column.

```
SELECT COUNT(*) FROM cyclist_data_compiled WHERE rideable_type IS NULL OR rideable_type = ' ';
-- RESULT: 0
SELECT COUNT(*) FROM cyclist_data_compiled WHERE member_casual IS NULL OR member_casual = ' ';
-- RESULT: 0
SELECT COUNT(*) FROM cyclist_data_compiled WHERE started_at IS NULL;
-- RESULT:0
SELECT COUNT(*) FROM cyclist_data_compiled WHERE ended_at IS NULL;
-- RESULT: 0
SELECT COUNT(*) FROM cyclist_data_compiled WHERE start_station_name IS NULL OR start_station_name = ' ';
-- RESULT: 790,207 (About 13.7% of total rows)
SELECT COUNT(*) FROM cyclist_data_compiled WHERE end_station_name IS NULL OR end_station_name = ' ';
-- RESULT: 843,361 (About 14.7% of total rows)
-- Since the percentage of missing data for start_station_name and end_station_name is higher than 5%,
-- I am excluding these columns to use. 

ALTER TABLE cyclist_data_compiled
DROP COLUMN start_station_name,
DROP COLUMN end_station_name
```

## Part B: Data Analysis

### Step B1 - Calculations with Time Format Columns

Extract year, month, and date

```
ALTER TABLE cyclist_data_compiled
ADD COLUMN month varchar,
ADD COLUMN weekday varchar,
ADD COLUMN year varchar

UPDATE cyclist_data_compiled
	SET month = TO_CHAR(started_at,'Month'),
	weekday = TO_CHAR(started_at,'Day'),
	year = EXTRACT(YEAR FROM started_at)
```

Calculate ride length. Since average ride length and total ride length will be presented in minutes and days, set up columns to calculate ride length in seconds, minutes, and days.

```
ALTER TABLE cyclist_data_compiled
ADD COLUMN ride_length interval,
ADD COLUMN rl_seconds double precision,
ADD COLUMN rl_min numeric,
ADD COLUMN rl_days numeric;

UPDATE cyclist_data_compiled
	SET ride_length = ended_at - started_at;
  
-- Extract seconds from ride length
UPDATE cyclist_data_compiled
	SET rl_seconds = EXTRACT(epoch FROM ride_length);
```
-- Make columns for month and week number for ordering/sorting.

```
ALTER TABLE cyclist_data_compiled
ADD COLUMN month_num numeric,
ADD COLUMN weekday_num numeric;

-- Extract month and week number from started_at column.

UPDATE cyclist_data_compiled
	SET month_num = EXTRACT(MONTH FROM Started_at),
	weekday_num = EXTRACT(DOW FROM Started_at);
```
Check to see if all values are calculated before moving on to step 

```
SELECT * FROM cyclist_data_compiled LIMIT 100;
```
Check to see if month number and weekday numbers were assigned correctly.

```
SELECT month_num, month
FROM cyclist_data_compiled
GROUP BY month_num, month
ORDER BY month_num, month ASC;

-- Weekday number check
SELECT weekday_num, weekday
FROM cyclist_data_compiled
GROUP BY weekday_num, weekday
ORDER BY weekday_num, weekday ASC;
```

### Step B2 - Calculating Summary Statistics
Get the following statistical breakdown broken down by month and by weekday:
> 1. Total Num of Rides
> 2. Casual, and Annual Members
> 3. Average Ride Length and Total Ride Length.

#### Summary Statistics by Month

Create temporary table for analysis.

``` 
CREATE TEMP TABLE sum_month
(month varchar,
rides_total bigint,
rides_casual bigint,
rides_annual bigint,

avg_rl interval,
avg_rl_casual interval,
avg_rl_annual interval,

tot_rl numeric,
tot_rl_casual numeric,
tot_rl_annual numeric,

b_docked integer,
b_docked_casual integer,
b_docked_annual integer,

b_classic integer,
b_classic_casual integer,
b_classic_annual integer,

b_electric integer,
b_electric_casual integer,
b_electric_annual integer);
```
Perform calculations and insert into temp table. 

```
INSERT INTO sum_month
SELECT
month,

-- Total number of rides broken down by annual and casual.
COUNT(ride_length) AS rides_total,
COUNT(member_casual) FILTER(WHERE member_casual LIKE 'casual') AS rides_casual,
COUNT(member_casual) FILTER(WHERE member_casual LIKE 'member') AS rides_annual,

-- Average ride lengths. 
AVG(ride_length) as avg_rl,
AVG(ride_length) FILTER(WHERE member_casual LIKE 'casual') AS avg_rl_casual,
AVG(ride_length) FILTER(WHERE member_casual LIKE 'member') AS avg_rl_annual,

-- Total ride time will be as days.
SUM(rl_days) as tot_rl,
SUM(rl_days) FILTER(WHERE member_casual LIKE 'casual') AS tot_rl_casual,
SUM(rl_days) FILTER(WHERE member_casual LIKE 'member') AS tot_rl_annual,

-- Number of docked, classic and electric rides by annual and casual members.
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'docked_bike') AS b_docked,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'docked_bike' AND member_casual LIKE 'casual') AS b_docked_casual,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'docked_bike' AND member_casual LIKE 'member') AS b_docked_annual,

COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'classic_bike') AS b_classic,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'classic_bike' AND member_casual LIKE 'casual') AS b_classic_casual,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'classic_bike' AND member_casual LIKE 'member') AS b_classic_annual,

COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'electric_bike') AS b_electric,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'electric_bike' AND member_casual LIKE 'casual') AS b_electric_casual,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'electric_bike' AND member_casual LIKE 'member') AS b_electric_annual
FROM cyclist_data_compiled
GROUP BY month_num, month
ORDER BY month_num, month ASC;

-- Check to see result. 

SELECT * FROM sum_month;

-- Round down minutes and days for the average ride lengths

UPDATE sum_month
	SET avg_rl = date_trunc('minute', avg_rl),
	avg_rl_casual = date_trunc('minute', avg_rl_casual),
	avg_rl_annual = date_trunc('minute', avg_rl_annual);
```

Save Results

```
CREATE TABLE summary_by_month
(month varchar,
rides_total bigint,
rides_casual bigint,
rides_annual bigint,

avg_rl interval,
avg_rl_casual interval,
avg_rl_annual interval,

tot_rl numeric,
tot_rl_casual numeric,
tot_rl_annual numeric,

b_docked integer,
b_docked_casual integer,
b_docked_annual integer,

b_classic integer,
b_classic_casual integer,
b_classic_annual integer,

b_electric integer,
b_electric_casual integer,
b_electric_annual integer);

INSERT INTO summary_by_month

SELECT
month, rides_total, rides_casual, rides_annual,
avg_rl, avg_rl_casual, avg_rl_annual,
tot_rl, tot_rl_casual, tot_rl_annual,
b_docked, b_docked_casual, b_docked_annual,
b_classic, b_classic_casual, b_classic_annual,
b_electric, b_electric_casual, b_electric_annual

FROM sum_month;

-- Check results to see if successful. Export as CSV.

SELECT * FROM summary_by_month;
```

Calculate summary statistics broken down by weekday. Similar query as above, except group by Weekday. 

```
CREATE TEMP TABLE sum_week
(weekday varchar,
rides_total bigint,
rides_casual bigint,
rides_annual bigint,

avg_rl interval,
avg_rl_casual interval,
avg_rl_annual interval,

tot_rl numeric,
tot_rl_casual numeric,
tot_rl_annual numeric,

b_docked integer,
b_docked_casual integer,
b_docked_annual integer,

b_classic integer,
b_classic_casual integer,
b_classic_annual integer,

b_electric integer,
b_electric_casual integer,
b_electric_annual integer);
```

Perform calculations and insert into temp table. 

```
INSERT INTO sum_week

SELECT
weekday,

-- Total number of rides broken down by annual and casual.
COUNT(ride_length) AS rides_total,
COUNT(member_casual) FILTER(WHERE member_casual LIKE 'casual') AS rides_casual,
COUNT(member_casual) FILTER(WHERE member_casual LIKE 'member') AS rides_annual,

-- Average ride lengths. 
AVG(ride_length) as avg_rl,
AVG(ride_length) FILTER(WHERE member_casual LIKE 'casual') AS avg_rl_casual,
AVG(ride_length) FILTER(WHERE member_casual LIKE 'member') AS avg_rl_annual,

-- Total ride time.
SUM(rl_days) as tot_rl,
SUM(rl_days) FILTER(WHERE member_casual LIKE 'casual') AS tot_rl_casual,
SUM(rl_days) FILTER(WHERE member_casual LIKE 'member') AS tot_rl_annual,

-- Number of docked, classic and electric rides by annual and casual members.
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'docked_bike') AS b_docked,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'docked_bike' AND member_casual LIKE 'casual') AS b_docked_casual,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'docked_bike' AND member_casual LIKE 'member') AS b_docked_annual,

COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'classic_bike') AS b_classic,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'classic_bike' AND member_casual LIKE 'casual') AS b_classic_casual,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'classic_bike' AND member_casual LIKE 'member') AS b_classic_annual,

COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'electric_bike') AS b_electric,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'electric_bike' AND member_casual LIKE 'casual') AS b_electric_casual,
COUNT(rideable_type) FILTER(WHERE rideable_type LIKE 'electric_bike' AND member_casual LIKE 'member') AS b_electric_annual

FROM cyclist_data_compiled
GROUP BY weekday_num, weekday
ORDER BY weekday_num, weekday ASC;

-- Check to see results

SELECT * FROM sum_week;

-- Round down minutes and days for the avg. and sum of ride lengths.

UPDATE sum_week
	SET avg_rl = date_trunc('minute', avg_rl),
	avg_rl_casual = date_trunc('minute', avg_rl_casual),
	avg_rl_annual = date_trunc('minute', avg_rl_annual);
```

Save results

```
CREATE TABLE summary_by_weekday
(weekday varchar,
rides_total bigint,
rides_casual bigint,
rides_annual bigint,

avg_rl interval,
avg_rl_casual interval,
avg_rl_annual interval,

tot_rl numeric,
tot_rl_casual numeric,
tot_rl_annual numeric,

b_docked bigint,
b_docked_casual bigint,
b_docked_annual bigint,

b_classic bigint,
b_classic_casual bigint,
b_classic_annual bigint,

b_electric bigint,
b_electric_casual bigint,
b_electric_annual bigint);

INSERT INTO summary_by_weekday

SELECT
weekday, rides_total, rides_casual, rides_annual,
avg_rl, avg_rl_casual, avg_rl_annual,
tot_rl, tot_rl_casual, tot_rl_annual,
b_docked, b_docked_casual, b_docked_annual,
b_classic, b_classic_casual, b_classic_annual,
b_electric, b_electric_casual, b_electric_annual

FROM sum_week;

-- Check to see results

SELECT * FROM summary_by_weekday
```

From here, export as CSV then combine both summary tables in Excel. From there, visuals were created in Tableau with excel file


