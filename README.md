# Retail Sales Analysis (SQL Project)

## Project Overview

**Project Title**: Retail Sales Analysis  
**Level**: Beginner  
**Database**: `retail_db`

This project demonstrates SQL skills and techniques used by data analysts to explore, clean, and analyze retail sales data. It involves:

- Setting up a retail sales database
- Performing data cleaning
- Running exploratory analysis
- Answering specific business questions using SQL

---

## Objectives

1. **Database Setup** – Create and populate the `retail_sales` table with the provided sales data.
2. **Data Cleaning** – Identify and remove records with missing values.
3. **Exploratory Data Analysis (EDA)** – Understand the dataset structure and contents.
4. **Business Analysis** – Solve real-world business questions using SQL queries.

---

## Project Structure

### 1. Database Setup

```sql
DROP TABLE IF EXISTS retail_sales;

CREATE TABLE retail_sales (
    transaction_id    INT PRIMARY KEY,
    sale_date         DATE,
    sale_time         TIME,
    customer_id       INT,
    gender            VARCHAR(15),
    age               INT,
    category          VARCHAR(15),
    quantity          INT,
    price_per_unit    FLOAT,
    cogs              FLOAT,
    total_sale        FLOAT
);
```

---

### 2. Data Exploration & Cleaning

**Check sample rows:**

```sql
SELECT * FROM retail_sales
LIMIT 10;
```

**Record count:**

```sql
SELECT COUNT(*) FROM retail_sales;
```

**Check for NULL values:**

```sql
SELECT *
FROM retail_sales
WHERE transaction_id IS NULL
   OR sale_date IS NULL
   OR sale_time IS NULL
   OR gender IS NULL
   OR category IS NULL
   OR quantity IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;
```

**Delete rows with NULLs:**

```sql
DELETE FROM retail_sales
WHERE transaction_id IS NULL
   OR sale_date IS NULL
   OR sale_time IS NULL
   OR gender IS NULL
   OR category IS NULL
   OR quantity IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;
```

---

## Business Analysis Queries & Answers

**Q1. Retrieve all columns for sales made on `2022-11-05`:**

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

**Q2. Transactions where category is `Clothing` and quantity ≥ 4 in Nov 2022:**

```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
  AND quantity >= 4;
```

**Q3. Total sales for each category:**

```sql
SELECT
    category,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY category
ORDER BY total_sales DESC;
```

**Q4. Average age of customers who purchased from `Beauty`:**

```sql
SELECT
    ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

**Q5. Transactions where total_sale > 1000:**

```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

**Q6. Total transactions by gender in each category:**

```sql
SELECT
    category,
    gender,
    COUNT(transaction_id) AS total_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

**Q7. Best selling month in each year (by average sale):**

```sql
SELECT
    year,
    month,
    avg_sale
FROM (
    SELECT
        EXTRACT(YEAR FROM sale_date)  AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale)               AS avg_sale,
        RANK() OVER (
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY 1, 2
) t1
WHERE rank = 1;
```

**Q8. Top 5 customers by total sales:**

```sql
SELECT
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

**Q9. Unique customers per category:**

```sql
SELECT
    category,
    COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY category;
```

**Q10. Orders by time-of-day shift:**

```sql
WITH hourly_sales AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT
    shift,
    COUNT(*) AS total_orders
FROM hourly_sales
GROUP BY shift
ORDER BY total_orders DESC;
```

---

## Findings

- **Customer Demographics:** Customers come from varied age groups; categories like Clothing and Beauty are common.
- **High-Value Sales:** Multiple transactions exceed 1000 in value.
- **Seasonality:** Certain months have higher average sales.
- **Top Customers:** A small set of customers contribute significantly to total revenue.

---

## How to Use

1. Clone this repository.
2. Load the dataset into PostgreSQL.
3. Run the SQL script `retail_sales_analysis.sql` in order:
   - Table creation
   - Data cleaning
   - Analysis queries

---

## Author

Ajay Sharma — Created as part of my SQL learning portfolio.
