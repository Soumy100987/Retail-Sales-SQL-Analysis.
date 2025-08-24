
# Retail-Sales-SQL-Analysis.
A SQL-based data analytics project on retail sales. Features database creation, data cleaning, and  queries for insights on revenue trends, customer demographics, time-based sales, and top-performing categories. and profitability analysis.

📊 Retail Sales Analysis Using SQL

-- ========================================================
-- Project: Retail Sales Analysis
-- Database: MySQL
-- Author: SOUMYA SHRIVASTAVA
-- ========================================================

-- 1. Create Database
CREATE DATABASE retailsales_analysis;
USE retailsales_analysis;

-- 2. Create Table
CREATE TABLE retail_sales (
    transactions_id INT,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(15),
    age INT,
    category VARCHAR(15),
    quantity INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);

-- 3. View all data
SELECT * FROM retail_sales;

-- ========================================================
-- QUERIES
-- ========================================================



-- Q1: Find total NULL values in the table

SELECT COUNT(*) AS null_count
FROM retail_sales
WHERE transactions_id IS NULL
   OR sale_date IS NULL
   OR sale_time IS NULL
   OR customer_id IS NULL
   OR gender IS NULL
   OR age IS NULL
   OR category IS NULL
   OR quantity IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;


-- Q2: Revenue by Customer Age Group

SELECT CASE
           WHEN age BETWEEN 18 AND 25 THEN '18-25'
           WHEN age BETWEEN 26 AND 35 THEN '26-35'
           WHEN age BETWEEN 36 AND 50 THEN '36-50'
           ELSE '50+'
       END AS age_group,
       SUM(total_sale) AS total_revenue
FROM retail_sales
WHERE age IS NOT NULL
GROUP BY age_group
ORDER BY total_revenue DESC;



-- Q3: Retrieve all sales made on '2022-11-05'

SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';



-- Q4: Transactions where category is 'Clothing' AND quantity > 3 in November 2022

SELECT *
FROM retail_sales
WHERE category = 'clothing'
  AND quantity > 3
  AND DATE_FORMAT(sale_date, '%Y-%m') = '2022-11';
  

-- Q5: Total sales and order count by category

SELECT category,
       SUM(total_sale) AS net_sale,
       COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;


-- Q6: Average age of customers who purchased from 'Beauty' category

SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'beauty';


-- Q7: Retrieve all transactions where total_sale > 1000

SELECT *
FROM retail_sales
WHERE total_sale > 1000;


-- Q8: Total transactions by gender in each category

SELECT category,
       gender,
       COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category;


-- Q9: Best-selling month in each year (based on average sale)

SELECT *
FROM (
         SELECT YEAR(sale_date) AS year,
                MONTH(sale_date) AS month,
                AVG(total_sale) AS avg_sale,
                RANK() OVER (PARTITION BY YEAR(sale_date)
                             ORDER BY AVG(total_sale) DESC) AS rnk
         FROM retail_sales
         GROUP BY YEAR(sale_date), MONTH(sale_date)
     ) AS t1
WHERE rnk = 1;



-- Q10: Top 5 customers based on total sales

SELECT customer_id,
       SUM(total_sale) AS total_sale
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sale DESC
LIMIT 5;


-- Q11: Unique customers in each category

SELECT category,
       COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY category;


-- Q12: Orders count by shift (Morning, Afternoon, Evening)

SELECT CASE
           WHEN HOUR(sale_time) < 12 THEN 'Morning'
           WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
           ELSE 'Evening'
       END AS shift,
       COUNT(*) AS total_orders
FROM retail_sales
GROUP BY shift;


-- Q13: Total sales revenue

SELECT SUM(total_sale) AS total_revenue
FROM retail_sales;


-- Q14: Total revenue by category

SELECT category,
       SUM(total_sale) AS category_revenue
FROM retail_sales
GROUP BY category
ORDER BY category_revenue DESC;



-- Q15: Most profitable category (Sales - COGS)

SELECT category,
       SUM(total_sale - cogs) AS total_profit
FROM retail_sales
GROUP BY category
ORDER BY total_profit DESC;



-- Q16: Month & Year with the highest sales

SELECT YEAR(sale_date) AS year,
       MONTH(sale_date) AS month,
       SUM(total_sale) AS monthly_sales
FROM retail_sales
GROUP BY year, month
ORDER BY monthly_sales DESC
LIMIT 10;



-- Q17: Average spent by gender

SELECT gender,
       AVG(total_sale) AS avg_spent
FROM retail_sales
GROUP BY gender;
