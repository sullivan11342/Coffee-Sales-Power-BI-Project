# Coffee-Sales-Power-BI-Project
A Dashboard Summary of a local Coffee Shop displaying key performance Indicators and analysis on various topics including total sales, orders, and quantities sold throughout the respective months. 



-- Queries from the data are listed below, performed in SSMS


CREATE DATABASE coffee_shop_sales_db

-- Link to the raw data can be found here:

-- https://drive.google.com/drive/folders/14sSwndELBWmfsBZc5E3dnc4VrgOCemcz

select * from coffee_shop_sales

DESCRIBE coffee_shop_sales 


-- reformatting the date from m/d/yyy to m-d-yyyy as using the system below: 
UPDATE coffee_shop_sales
SET transaction_date = str_to_date(transaction_date, "%m/%d/%Y"); 

-- once that has been completed, we can use the command below to convet the datatype from text type into a date type. 

ALTER TABLE coffee_shop_sales 
MODIFY COLUMN transaction_date DATE

-- Do the same for the transaction_time field.

UPDATE coffee_shop_sales
SET transaction_time = STR_TO_DATE(transaction_time, '%H:%i:%s');

ALTER TABLE coffee_shop_sales
MODIFY COLUMN transaction_time TIME; 

DESCRIBE Coffee_shop_sales; 

-- In the raw data, the name of the column ï»¿transaction_id is odd, and we want to adjust it so that it just reads as "transaction_id". alter
-- Using the program below, this will change that. 

ALTER TABLE coffee_shop_sales 
CHANGE COLUMN ï»¿transaction_id transaction_id INT 

-- Now that we have our data prepared, we will answer the following KPI requirements:

-- 1. Total Sales Analysis 
-- a. Calculate the total sales for each respective month.

-- first, start with getting the total sales using the query below.

SELECT sum(unit_price * transaction_qty) AS Total_Sales FROM coffee_shop_sales

-- next, calculate for each month

SELECT month(transaction_date), round(sum(unit_price * transaction_qty)) AS Total_Sales FROM coffee_shop_sales
group by month(transaction_date)

SELECT round(sum(unit_price * transaction_qty)) AS Total_Sales FROM coffee_shop_sales
WHERE MONTH(transaction_date) = 6 -- March Month



SELECT CONCAT((ROUND(SUM(unit_price * transaction_qty)))/1000, "k") AS Total_Sales FROM coffee_shop_sales
WHERE MONTH(transaction_date) = 3 -- same as above but is just written differently. 

-- notice, using round allows you to get rid of decimals. 


select transaction_date, sum(unit_price) FROM coffee_shop_sales 
GROUP BY transaction_date 

-- b. Determine the month-on-month increase or decrease in sales.

-- Selected Month / current month - May= 5
-- PM - April = 4


SELECT
	Month(transaction_date) AS month, -- Number of Month
    ROUND(SUM(unit_price * transaction_qty)) AS total_sales, -- total sales column
    (SUM(unit_price * transaction_qty) - LAG(SUM(Unit_price * transaction_qty),1)
    OVER (ORDER BY MONTH(transaction_date))) / LAG(SUM(unit_price * transaction_qty), 1)
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage -- Percentage
FROM 
	Coffee_Shop_sales
WHERE month(transaction_date) IN (4,5) -- for months of April (PM) and May (CM)
GROUP BY MONTH (transaction_date)
ORDER BY MONTH (transaction_date); 

-- c. Calculate the difference in sales between the selected month and the previous month.

-- same command as above, use a calculator to show the difference in months between April and May, and what will eventually reflect on powerBI report

SELECT
	Month(transaction_date) AS month, -- Number of Month
    ROUND(SUM(unit_price * transaction_qty)) AS total_sales, -- total sales column
    (SUM(unit_price * transaction_qty) - LAG(SUM(Unit_price * transaction_qty),1)
    OVER (ORDER BY MONTH(transaction_date))) / LAG(SUM(unit_price * transaction_qty), 1)
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage -- Percentage
FROM 
	Coffee_Shop_sales
WHERE month(transaction_date) IN (4,5) -- for months of April (PM) and May (CM)
GROUP BY MONTH (transaction_date)
ORDER BY MONTH (transaction_date); 



-- 2. Total Orders Analysis: 
-- a. Calculate the total number of orders for each respective month.

SELECT count(transaction_id) AS March_Total_Orders FROM coffee_shop_sales
WHERE MONTH(transaction_date) = 3 -- March Month

-- b. Determine the month-on-month increase or decrease in the number of orders.

SELECT
	Month(transaction_date) AS month, -- Number of Month
    ROUND(count(transaction_id)) AS total_number_of_orders, -- total number of orders column
    (count(transaction_id) - LAG(count(transaction_id),1)
    OVER (ORDER BY MONTH(transaction_date))) / LAG(count(transaction_id), 1)
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage -- Percentage
FROM 
	Coffee_Shop_sales
WHERE month(transaction_date) IN (4,5) -- for months of April (PM) and May (CM)
GROUP BY MONTH (transaction_date)
ORDER BY MONTH (transaction_date); 

-- notice how the above query is similar to 1b. above because we are analyzing month over month data.

-- c. Calculate the difference in the number of orders between the selected month and the previous month.

SELECT
	Month(transaction_date) AS month, -- Number of Month
    ROUND(count(transaction_id)) AS total_number_of_orders, -- total number of orders column
    (count(transaction_id) - LAG(count(transaction_id),1)
    OVER (ORDER BY MONTH(transaction_date))) / LAG(count(transaction_id), 1)
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage -- Percentage
FROM 
	Coffee_Shop_sales
WHERE month(transaction_date) IN (4,5) -- for months of Fe (PM) and May (CM)
GROUP BY MONTH (transaction_date)
ORDER BY MONTH (transaction_date); 


-- 3. Total Quantity Sold Analysis: 
-- a. Calculate the total quantity sold for each respective month.
select * from coffee_shop_sales;

SELECT sum(transaction_qty) AS total_quantity_sold FROM coffee_shop_sales
WHERE Month(transaction_date) = 5 -- May Month


-- b. Determine the month-on-month increase or decrease in the total quantity sold.

SELECT
	Month(transaction_date) AS month, -- Number of Month
    ROUND(SUM(unit_price * transaction_qty)) AS total_quantity_sold, 
    (SUM(transaction_qty) - LAG(SUM(transaction_qty),1)
    OVER (ORDER BY MONTH(transaction_date))) / LAG(SUM(transaction_qty), 1)
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage -- Percentage
FROM 
	Coffee_Shop_sales
WHERE month(transaction_date) IN (4,5) -- for months of April (PM) and May (CM)
GROUP BY MONTH (transaction_date)
ORDER BY MONTH (transaction_date); 



-- c. Calculate the difference in the total quantity sold between the selected month and the previous month.

SELECT
	Month(transaction_date) AS month, -- Number of Month
    ROUND(SUM(unit_price * transaction_qty)) AS total_quantity_sold, 
    (SUM(transaction_qty) - LAG(SUM(transaction_qty),1)
    OVER (ORDER BY MONTH(transaction_date))) / LAG(SUM(transaction_qty), 1)
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage -- Percentage
FROM 
	Coffee_Shop_sales
WHERE month(transaction_date) IN (4,5) -- for months of April (PM) and May (CM)
GROUP BY MONTH (transaction_date)
ORDER BY MONTH (transaction_date); 

-- same thing, just subtract 5 from 4. 






-- ------------------------------------------------------------- Charts Requirements:

-- 1. Calendar Heat Map:
-- a. Implement a calendar heat map that dynamically adjusts based on the selected month from a slicer.
select * from coffee_shop_sales

SELECT SUM(transaction_qty * unit_price) AS total_sales,
SUM(Transaction_QTY) AS Total_QTY_Sold,
Count(transaction_id) AS Total_Orders
	FROM coffee_shop_sales
WHERE transaction_date = '2023-05-18'

-- same thing below but using concat and round functions. 

SELECT concat(round(SUM(transaction_qty * unit_price)/1000,1),'k') AS total_sales,
	   concat(round(SUM(Transaction_QTY)/1000,1),'k') AS Total_QTY_Sold,
	   concat(round(Count(transaction_id)/1000,1),'k') AS Total_Orders
	FROM coffee_shop_sales
WHERE transaction_date = '2023-05-18'



-- b. Each day on the calendar will be color-coded to represent sales volume, with darker shades indiciating higher sales.



-- c. Implement toltips to display detailed metrics (Sales, Orders, Quantity) When hovering over a specific day.



-- 2. Sales Analysis by weekdays and weekends:
-- a. Segment sales data into weekdays and weekends to analyze performance variations.

-- Weekends will be saturday and sunday.
-- weekdays will be Monday through Friday

-- We will make use of day numbers. For example, Sunday is given as day number 1. And so on. 

SELECT 
	CASE WHEN DAYOFWEEK(transaction_date) IN (1,7) THEN 'Weekends' 
    ELSE 'Weekdays'
    END AS day_type,
	SUM(unit_price * transaction_qty) AS Total_sales 
FROM coffee_shop_sales
WHERE MONTH(transaction_date) = 5 -- May Month
GROUP BY
	CASE WHEN DAYOFWEEK(transaction_date) IN (1,7) THEN 'Weekends' 
    ELSE 'Weekdays'
    END

-- same thing below just using concat function.

SELECT 
	CASE WHEN DAYOFWEEK(transaction_date) IN (1,7) THEN 'Weekends' 
    ELSE 'Weekdays'
    END AS day_type,
	CONCAT(Round(SUM(unit_price * transaction_qty)/1000,1), 'k') AS Total_sales 
FROM coffee_shop_sales
WHERE MONTH(transaction_date) = 5 -- May Month
GROUP BY
	CASE WHEN DAYOFWEEK(transaction_date) IN (1,7) THEN 'Weekends' 
    ELSE 'Weekdays'
    END


-- b. Provide insights into whether sales patterns differ significantly between weekdays and weekends.


-- 3. Sales Analysis by store location:
-- a. Visualize sales data by different store locations. 
SELECt * from coffee_shop_sales;

SELECT store_location, sum(transaction_qty * unit_price) AS total_sales FROM coffee_shop_sales
WHERE month(transaction_date) = 5
GROUP BY store_location
order by sum(transaction_qty * unit_price) desc



SELECT store_location, CONCAT(ROUND(sum(transaction_qty * unit_price)/1000, 1), 'k') AS total_sales FROM coffee_shop_sales
WHERE month(transaction_date) = 5
GROUP BY store_location
order by sum(transaction_qty * unit_price) desc

-- b. Include month-over-month (MoM) difference metrics based on the selected month in the slicer.



-- c. Highlight MoM sales increase or decrease for each store location to identify trends. 


-- the query below is used to show an average sales on a daily basis. In other words, for May (5) the average total
-- sales per day is around 5055.73. That's all it is. Run the internal query and it will show you the daily sales. 

SELECT AVG(Total_sales) AS Avg_Sales FROM
(
	SELECT sum(transaction_qty * unit_price) AS total_sales FROM coffee_shop_sales 
    WHERE MONTH(transaction_date) = 5
    GROUP BY transaction_date 
    ) AS Internal_query
    
    
    
    SELECT concat(round(AVG(Total_sales)/1000, 1), 'k') AS Avg_Sales FROM
(
	SELECT sum(transaction_qty * unit_price) AS total_sales FROM coffee_shop_sales 
    WHERE MONTH(transaction_date) = 5
    GROUP BY transaction_date 
    ) AS Internal_query
    
    
    -- sales each day in the month of May listed below. Broken up by number. You'll notice if you run just the subquery above, it produces the same results.
    SELECT
		DAY(transaction_date) AS day_of_month,
        SUM(unit_price * transaction_qty) AS total_sales
	FROM coffee_shop_sales
    WHERE MONTH (transaction_date) = 5
    GROUP BY Day(transaction_date) 
    ORDER BY Day(transaction_date)

-- highlight which days are above average in sales, and which days are below average in sales.

SELECT day_of_month,
	CASE 
		WHEN total_sales > avg_sales THEN 'Above Average'
        WHEN total_sales < avg_sales THEN 'Below Average'
        ELSE 'Equal to Average'
	END AS sales_status,
    total_sales
    FROM 
    (	
    SELECT DAY(Transaction_date) AS day_of_month, SUM(unit_price * transaction_qty) AS total_sales, AVG(sum(unit_price * transaction_qty)) OVER () AS avg_sales FROM coffee_shop_sales 
WHERE MONTH(transaction_date) = 5 -- filter for May
GROUP BY DAY(Transaction_date)
	) AS Sales_data
ORDER BY day_of_month; 
    
-- just something to note; the "over ()" in the query above, represents how when running the subquery, the avg_sales column will have the same number repeating. 




----------------------------- sales analysis by product category -------------------------------------

-- Analyze sales performance across different product categories

select * from coffee_shop_sales;

select product_category, sum(transaction_qty * unit_price) from coffee_shop_sales
group by product_category
order by sum(transaction_qty * unit_price) desc;

-- how about for a particular month?
select product_category, sum(transaction_qty * unit_price) from coffee_shop_sales
where month(transaction_date) = 5
group by product_category
order by sum(transaction_qty * unit_price) desc;

-- what if we want to know what the top ten sales are for product type? Just use limit function and switch out 
-- product category with product type. 

select product_type, sum(transaction_qty * unit_price) AS total_sales from coffee_shop_sales
where month(transaction_date) = 5
group by product_type
order by sum(transaction_qty * unit_price) desc
limit 10


-- What if we want to find the particular total sales in a particular hour, on a particular day, in a particular month?

SELECT sum(unit_price * transaction_qty) AS total_sales, sum(transaction_qty) as TOTAL_qty_sold, count(*) FROM coffee_shop_sales
WHERE month(transaction_date) = 5 -- May
AND DAYOFWEEK(transaction_date) = 2 -- this is for Monday
AND HOUR(transaction_time) = 8 -- hour number 8

-- what if we want to see the total sales for each hour within a particular month?

select HOUR(transaction_time), sum(unit_price * transaction_qty) AS total_sales FROM coffee_shop_sales
WHERE month(transaction_date) = 5
GROUP BY hour(transaction_time)
order by hour(transaction_time)

-- what if we want to see total sales by each day of the week? 

SELECT 
	CASE
		WHEN DAYOFWEEK(transaction_date) = 2 THEN 'Monday'
        WHEN DAYOFWEEK(transaction_date) = 3 THEN 'Tuesday'
        WHEN DAYOFWEEK(transaction_date) = 4 THEN 'Wednesday'
        WHEN DAYOFWEEK(transaction_date) = 5 THEN 'Thursday'
        WHEN DAYOFWEEK(transaction_date) = 6 THEN 'Friday'
        WHEN DAYOFWEEK(transaction_date) = 7 THEN 'Saturday'
        ELSE 'Sunday'
	END AS Day_of_week,
    ROUND(SUM(Unit_Price * transaction_qty)) AS Total_Sales FROM coffee_shop_sales
    WHERE Month(transaction_date) = 5 
    GROUP BY
    CASE
		WHEN DAYOFWEEK(transaction_date) = 2 THEN 'Monday'
        WHEN DAYOFWEEK(transaction_date) = 3 THEN 'Tuesday'
        WHEN DAYOFWEEK(transaction_date) = 4 THEN 'Wednesday'
        WHEN DAYOFWEEK(transaction_date) = 5 THEN 'Thursday'
        WHEN DAYOFWEEK(transaction_date) = 6 THEN 'Friday'
        WHEN DAYOFWEEK(transaction_date) = 7 THEN 'Saturday'
        ELSE 'Sunday'
	END; 




NOTE: Data and original project walkthrough brought to you by Data Tutorials. Video available here: https://www.youtube.com/watch?v=hgz0msTZtX8&t=2353s
