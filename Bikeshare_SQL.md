# Bikeshare EDA / PostgreSQL

# 📘Context

A bike-sharing company (DivvyBikes) requested a report on their trends and bike activities. I analysed their data by comparing it to a competitor's benchmark dataset. I used SQL to access all the necessary datasets in PostgreSQL and presented the insights using Excel pivot tables and graphs. Overall, I reported on their seasonality, usage demand and customer segment to aid their operations.

# 📈Trends and Descriptive Analysis

## Question 1: How many trips were there in each month of each year?

***Answer:*** The highlighted section is how many trips there was in each month from years 2017 - 2019.

<a href="url"><img src="https://i.imgur.com/mtZOHVd.png" align="center" height="428.5" width="251.5" ></a>
<a href="url"><img src="https://i.imgur.com/N3Q47VD.png" align="center" height="295.6" width="539.3" ></a>


***SQL Method:***
1. Union all years for Baywheels (2017, 2018, 2019) as a CTE called **b_allyears**
2. Union all years for Divvybikes (2017, 2018, 2019) as a CTE called **d_allyears**
3. Union both CTE tables: b_allyears and d_allyears with the following columns:
	- Select only the month and year of start_time using the Date Part function
	- Count the bike_id to get the total number of trips
4. Finally, Group the NEW table by year, month and company_name.

***PostgreSQL Query:***

```sql
WITH
b_allyears (bike_id, start_time, end_time, start_station_id, end_station_id, user_type, company_name) as 
				(
				select 
bike_id, start_time, end_time, start_station_id, end_station_id, user_type, 'Baywheels' as company_name 
				from baywheels_2017
			union
				Select
bike_id, start_time, end_time, start_station_id, end_station_id, user_type, 'Baywheels' as company_name
				from baywheels_2018
			union
				Select
bike_id, start_time, end_time, start_station_id, end_station_id, user_type, 'Baywheels' as company_name
				from baywheels_2019
				),

d_allyears (bike_id, start_time, end_time, start_station_id, end_station_id, user_type, company_name) as 
				(
				select 
bikeid, start_time, end_time, start_station_id, end_station_id, user_type, 'Divvybikes' as company_name
				from divvybikes_2017
			union
				Select
bikeid, start_time, end_time, start_station_id, end_station_id, user_type, 'Divvybikes' as company_name
				from divvybikes_2018
			union
				Select
bikeid, start_time, end_time, start_station_id, end_station_id, user_type, 'Divvybikes' as company_name
				from divvybikes_2019
				)

SELECT
	company_name,
	date_part('year', start_time) as year,
	date_part('month', start_time) as month,
	count(bike_id)

FROM 
(select *
from b_allyears
UNION
select *
from d_allyears) as db_union
group by year, month, company_name
```

## Question 2: Which organisations are showing the most growth in bike rentals?

***Answer:*** The Baywheels growth rate has been predominantly positive compare to DivvyBikes.
***SQL Method:***
1. The same dataset was pulled from Question 1 SQL Query. A growth rate calculation was created on excel to create a new ‘Growth rate’ column.

***Excel Growth Calculation:***

<a href="url"><img src="https://i.imgur.com/tmxm4J1.png" align="center" height="423.3" width="269" ></a>
<a href="url"><img src="https://i.imgur.com/qPvkK4u.png" align="center" height="278" width="520.3" ></a>


## Question 3: Is there a difference in growth between holiday and commuting activity?

***Answer:*** Yes, there's negative growth between the Christmas holiday period (Oct, Nov, Dec) and is usually back to growth after January.
	
***Comments:*** Bay wheels had a very low start point on June 2017, which suggests that it launched around June 2017. It seems to have a study growth over the years but during the December holiday period, there's always a sudden drop, then it picks up again after January or February. On the other hand, Divvy bikes shows consistency throughout the years with huge peaks around tax season (June - August) before dropping down for Christmas Holiday season. Divvy bikes seems to be around for longer due to its consistency of usage.

- ***SQL Method:***
1. Union all years for Baywheels (2017, 2018, 2019) as a CTE called **b_allyears**
2. Union all years for Divvybikes (2017, 2018, 2019) as a CTE called **d_allyears**
3. Union both CTE tables: b_allyears and d_allyears with the following columns:
	- Select only the month and year of start_time using the Date Part function
	- Count the bike_id to get the total number of trips
4. Finally, Group the NEW table by year, month and company_name.

***PostgreSQL Query:***

    ```sql
    WITH
    b_allyears (bike_id, start_time, end_time, start_station_id, end_station_id, user_type, company_name) as 
    				(
    				select 
    bike_id, start_time, end_time, start_station_id, end_station_id, user_type, 'Baywheels' as company_name 
    				from baywheels_2017
    			union
    				Select
    bike_id, start_time, end_time, start_station_id, end_station_id, user_type, 'Baywheels' as company_name
    				from baywheels_2018
    			union
    				Select
    bike_id, start_time, end_time, start_station_id, end_station_id, user_type, 'Baywheels' as company_name
    				from baywheels_2019
    				),

    d_allyears (bike_id, start_time, end_time, start_station_id, end_station_id, user_type, company_name) as 
    				(
    				select 
    bikeid, start_time, end_time, start_station_id, end_station_id, user_type, 'Divvybikes' as company_name
    				from divvybikes_2017
    			union

    				Select
    bikeid, start_time, end_time, start_station_id, end_station_id, user_type, 'Divvybikes' as company_name
    				from divvybikes_2018
    			union
    				Select
    bikeid, start_time, end_time, start_station_id, end_station_id, user_type, 'Divvybikes' as company_name
    				from divvybikes_2019
    				)

    SELECT
    	company_name,
    	date_part('year', start_time) as year,
    	date_part('month', start_time) as month,
    	count(bike_id)

    FROM 
    (select *
    from b_allyears
    UNION
    select *
    from d_allyears) as db_union
    group by year, month, company_name;
    ```

# 🌎Geospatial Analysis

## Question 4: What was the longest journey? What do we know about it?

***Answer:*** The company that has the longest journey was Bay wheels with a total of 69 Kilometres travelled. We know that this trip was performed with Bike ID 4780. It began from Montgomery St BART Station (Market St at 2nd St) and ended at Palm St at Willow St. The user was a Subscriber with Bay wheels and it took them approximately 2 hours to complete the journey.

***SQL Method:***
1. Due to the HUGE query size, I had to break it down into two different queries to lower loading time. I ran two different SQL with the same structure but for different companies (Baywheels and Divvybikes). Both of these queries are similar but different aliases.
2. Union all years for Baywheels (2017, 2018, 2019) as a CTE called **b_allyears**
3. Now I need to find out the correct coordinates for the start_station_id and end_station_id to measure the distance of the trips.
4. To include the data from the ‘stations’ table, I did an INNER JOIN with b_allyears and baywheels_stations on the matching key, start station id. This gave me the coordinates for the start station.
5. Another INNER JOIN was performed with the previous table to get the coordinates for the end_station_id. This join was matched by end station id. This concludes CTE **bay_all.**
6. The final query will use the new CTE table bay_all to calculate the distance between the starting and ending coordinates, and the result will become a new column ‘distance’.
7. To prevent the query from destroying my laptop, I limited the query to 5 rows with an order by distance desc (This will give me the important figure to answer the question: Longest Journey + more information about the trip itself (station names, time, user type).

***PostgreSQL Query:***

**Bay Wheels: (Processing time: 9 mins)**

    ```sql
    WITH 
    bay_all as 
    (
    	SELECT 
    	b_1.bike_id, 
    	b_1.start_time, 
    	b_1.end_time, 
    	b_1.start_station_id,
    	b_1.start_station_name, 
    	b_1.end_station_id, 
    	b_stations_2.name as end_station_name, 
    	b_1.user_type, 
    	b_1.company_name,
    	b_1.station_id, 
    	b_1.latitude as start_latitude, 
    	b_1.longitude as start_longitude,
    	b_stations_2.latitude as end_latitude,
    	b_stations_2.longitude as end_longitude,
    	b_1.docks
    		FROM
    			(SELECT 
    bike_id, 
    start_time, 
    end_time, 
    Start_station_id,
     end_station_id, 
    user_type, 
    company_name,
    			id as station_id, latitude, 
    longitude, name as start_station_name,
    docks
    				
    				FROM
    (SELECT 
    bike_id, start_time, end_time, start_station_id, end_station_id, user_type, 'Baywheels' as company_name 
    				FROM baywheels_2017
    				union
    				Select
    bike_id, start_time, end_time, start_station_id, end_station_id, user_type, 'Baywheels' as company_name
    				FROM baywheels_2018
    				union
    				Select
    bike_id, start_time, end_time, start_station_id, end_station_id, user_type, 'Baywheels' as company_name
    				from baywheels_2019) as b_allyears
    						
    INNER JOIN baywheels_stations as b_stations
    					ON b_allyears.start_station_id = b_stations.id) as b_1
    													
    						INNER JOIN baywheels_stations as b_stations_2
    						ON  b_1.end_station_id = b_stations_2.id)

    	
    SELECT
    bike_id, 
    	start_time, 
    	end_time, 
    	start_station_name,
    	end_station_name, 
    	user_type, 
    	company_name,
    	docks,
    calculate_distance(start_latitude, start_longitude,
    				   end_latitude, end_longitude,
    				   'K') as distance 
    from bay_all
    ORDER BY distance desc
    LIMIT 5
    ```

    **Divvy Bikes: (Processing time: 40 mins)**

    ```sql
    SELECT 
    			d_1.bike_id, 
    			d_1.start_time, 
    			d_1.end_time, 
    			d_1.start_station_id, 
    			d_1.start_station_name,
    			d_1.end_station_id, 
    			d_stations_2.name as end_station_name, 
    			d_1.user_type, 
    			d_1.company_name,
    			calculate_distance(d_1.latitude, d_1.longitude,
    				   d_stations_2.latitude, d_stations_2.longitude,
    				   'K') as distance
    			
    		FROM
    			(SELECT
    			div_allyears.bikeid as bike_id, div_allyears.start_time, div_allyears.end_time, div_allyears.start_station_id, d_station.name as start_station_name, div_allyears.end_station_id, div_allyears.user_type, div_allyears.company_name,
    			d_station.id as station_id, d_station.latitude, d_station.longitude, d_station.docks
    		
    				FROM (select 
    				bikeid, start_time, end_time, start_station_id, end_station_id, user_type, 'Divvybikes' as company_name
    				from divvybikes_2017
    				union
    				select
    				bikeid, start_time, end_time, start_station_id, end_station_id, user_type, 'Divvybikes' as company_name
    				from divvybikes_2018
    				union
    				select
    				bikeid, start_time, end_time, start_station_id, end_station_id, user_type, 'Divvybikes' as company_name
    				from divvybikes_2019) as div_allyears
    				
    							INNER JOIN divvy_stations as d_station
    							ON div_allyears.start_station_id = d_station.id) as d_1
    													INNER JOIN divvy_stations as d_stations_2
    													ON d_1.end_station_id = d_stations_2.id

    ORDER BY distance desc
    limit 5;
    ```

## Question 5: How often do bikes need to be relocated?

**Answer:** For the year 2019, Baywheels has an average relocation rate of 14% from all trips made throughout the year.

    Divvy bikes has an average relocation rate of 7% from all trips made throughout the year.

**SQL method:**
1. Due to the HUGE query size, I had to break it down into two different queries to lower loading time. I ran two different SQL with the same structure but for different companies (Baywheels and Divvybikes). Both of these queries are similar but different aliases.
2. Create a CTE of three tables, one to list all the necessary columns, another to add a lag function and the third one to label every relocation as 1.
3. The final query is used to part the columns by year/month and do a sum on the relocation column to get the total relocation amount per month.

***PostgreSQL Query:***

   **2019 Bay Wheels:**

    ```sql
    WITH
    bay_relocation AS (select 
    		bike_id,
    		start_time,
    		end_time,
    		start_station_id,
    		end_station_id
    			
    from baywheels_2019
    order by bike_id, start_time asc
    ),

    bay_partition AS  (Select
    		bike_id,
    		start_time,
    		end_time,
    		start_station_id,
    		end_station_id,
    		lag(end_station_id,1) OVER (partition by bike_id)as expected_start_station
    		
    	
    FROM
    bay_relocation),

    bay_relocation2 AS 
    (
    	SELECT *,

    CASE
    	WHEN expected_start_station = start_station_id THEN 0
    	ELSE 1
    	END as relocation

    from bay_partition

    )

    SELECT 
    date_part('year', start_time) as year,
    date_part('month', start_time) as month,
    SUM(relocation) as bikes_relocated

    FROM
    bay_relocation2
    group by year, month
    ```

    **2019 Divvybikes:**

    ```sql
    WITH

    divvy_relocation AS (select 
    		bikeid,
    		start_time,
    		end_time,
    		start_station_id,
    		end_station_id
    			
    from divvybikes_2019
    order by bikeid, start_time asc
    ),

    divvy_partition AS  (Select
    		bikeid,
    		start_time,
    		end_time,
    		start_station_id,
    		end_station_id,
    		lag(end_station_id,1) OVER (partition by bikeid)as expected_start_station
    		
    	
    FROM
    divvy_relocation),

    divvy_relocation2 AS 
    (
    	SELECT *,

    CASE
    	WHEN expected_start_station = start_station_id THEN 0
    	ELSE 1
    	END as relocation

    from divvy_partition

    )

    SELECT 

    date_part('year', start_time) as year,
    date_part('month', start_time) as month,
    SUM(relocation) as bikes_relocated

    FROM
    divvy_relocation2
    group by year, month
    ```

## Question 6: How far is a typical journey?

***Answer:*** From 2017 - 2019, the typical journey for Baywheels is between 1.6km to 1.70 km. And for Divvybikes is between 1.97km to 2.16 km

***SQL Method:***
1. I re-structured the SQL query for Question 4 where we found the longest distance between stations. This is what I did:
2. To get the coordinates for both start and end station, I joined divvybikes_2019/2018/2017 with divvy_stations table (Twice).
3. First join was done through the start_station_id and the second with the end_station_id.
4. I create a CTE with the table that has the end coordinates and start coordinates.
5. Another CTE(d_dist) is created to calculate the distance from the previous CTE (d_no_dist).
6. Final query just gives me the average distance for the year 2019/2018/2017. (I ran this query separately for each year)
7. The same query was used for Baywheels.

***PostgreSQL Query:***

   **Divvybikes:**

    ```sql
    WITH
    d_no_dist (company_name, start_lat, start_lon, end_lat, end_lon ) AS 
    (SELECT 
    	company_name,
    	start_lat,
    	start_lon,
    	latitude as end_lat,
    longitude as end_lon
    			
    		FROM 
    		(SELECT
    			'Divvybikes' as company_name,
    			start_station_id,
    			end_station_id,
    			latitude as start_lat,
    			longitude as start_lon
    				FROM divvybikes_2019 as d
    					INNER JOIN divvy_stations as d_station
    					ON d.start_station_id = d_station.id ) as d_start
    						
    																			INNER JOIN divvy_stations as d_stations_2
    						ON d_start.end_station_id = d_stations_2.id),
    														
    d_dist as 
    (SELECT *,
    	calculate_distance(start_lat,start_lon, end_lat, end_lon,'K') as distance
    	from d_no_dist)
    	
    	SELECT
    company_name, 
    avg(distance)
    	
    	FROM d_dist
    	group by company_name;
    ```

    **Baywheels:**

    ```sql
    WITH
    b_no_dist (company_name, start_lat, start_lon, end_lat, end_lon ) as 
    (SELECT 
    	company_name,
    	start_lat,
    	start_lon,
     	latitude as end_lat,
    	longitude as end_lon
    FROM 
    					
    (select
    		'baywheels' as company_name,
    		start_station_id,
    		end_station_id,
    		latitude as start_lat,
    		longitude as start_lon
    					
    FROM baywheels_2017 as b
    				INNER JOIN baywheels_stations as b_station
    				ON b.start_station_id = b_station.id ) as b_start
    						
    																		INNER JOIN baywheels_stations as b_stations_2
    					ON b_start.end_station_id = b_stations_2.id),
    																								

    	b_dist as
     (SELECT *,
    	calculate_distance(start_lat,start_lon, end_lat, end_lon,'K') as distance
    	from b_no_dist)

    	
    	SELECT
    company_name, 
    avg(distance)
    	
    	FROM b_dist
    	GROUP BY company_name;
    ```
