# ProjectHotel
End to end data dashboard develop from database development, extraction via SQL, and dashboard creation.
----
The following was how data was extracted in sql.
The last Query was what was exported as an excel file which was later imported into Power BI for the visualisation of the data

SQL queries below

-----
-- Joining Three Tables from 2018 to 2020
SELECT * FROM projecthotel.hotel2018
union
SELECT * FROM projecthotel.hotel2019
union
SELECT * FROM projecthotel.hotel2020

-- answering our first question, Is our hotel revenue growing by year? – we have two hotel types so it would be good to segment revenue by hotel type

With hotels as ( SELECT * FROM projecthotel.hotel2018 
union 
SELECT * FROM projecthotel.hotel2019
 union 
 SELECT * FROM projecthotel.hotel2020)
 SELECT * FROM hotels

-- There is no revenue column so we need to create one with the query below

With hotels as ( SELECT * FROM projecthotel.hotel2018 
union 
SELECT * FROM projecthotel.hotel2019
 union 
 SELECT * FROM projecthotel.hotel2020)
 SELECT stays_in_weekend_nights + stays_in_week_nights FROM hotels
 
 -- To get the revenue we mutiply the selected statement with the daily rates which is the adr
 
 With hotels as ( SELECT * FROM projecthotel.hotel2018 
union 
SELECT * FROM projecthotel.hotel2019
 union 
 SELECT * FROM projecthotel.hotel2020)
 SELECT (stays_in_weekend_nights + stays_in_week_nights) * adr AS Revenue FROM hotels
 
 -- Now that we have the revenue, we look to find out if the revenue is growing or not with the following query
 
  With hotels as ( SELECT * FROM projecthotel.hotel2018 
union 
SELECT * FROM projecthotel.hotel2019
 union 
 SELECT * FROM projecthotel.hotel2020)
 SELECT arrival_date_year, (stays_in_weekend_nights + stays_in_week_nights) * adr AS Revenue 
 FROM hotels
 order by arrival_date_year, 'Revenue'
 
 -- We have our revenue sorted from lowest to highest but its still difficult to see if revenue is growing or not,
 -- so we sum up the revenue and group it by year to get the total revenue by year with the query below
 
   With hotels as ( SELECT * FROM projecthotel.hotel2018 
union 
SELECT * FROM projecthotel.hotel2019
 union 
 SELECT * FROM projecthotel.hotel2020)
 SELECT arrival_date_year, sum((stays_in_weekend_nights + stays_in_week_nights) * adr) AS Revenue
 FROM hotels
 GROUP BY arrival_date_year
 
 -- Per the request of our stakeholders, they also want to know the hotel type and their revenue, i find that from the query below
 
 With hotels as ( SELECT * FROM projecthotel.hotel2018 
union 
SELECT * FROM projecthotel.hotel2019
 union 
 SELECT * FROM projecthotel.hotel2020)
 SELECT arrival_date_year, hotel,
 ROUND (sum((stays_in_weekend_nights + stays_in_week_nights) * adr), 2) AS Revenue
 FROM hotels
 GROUP BY arrival_date_year, hotel
 
 -- I found out there are other tables with discounts and costs that can influence the total revenue from the hotels
 -- I join theses tables to our previous table in the following query
 
 With hotels as ( SELECT * FROM projecthotel.hotel2018 
union 
SELECT * FROM projecthotel.hotel2019
 union 
 SELECT * FROM projecthotel.hotel2020)
 SELECT * 
 FROM hotels as h
 LEFT JOIN projecthotel.hotelmarketsegment as hms
 ON  h.market_segment = hms.market_segment
 LEFT JOIN projecthotel.hotelmealcost as hmc
 ON h.meal = hmc.meal
 order by arrival_date_year
 
 
 
