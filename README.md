
# 🔥 10 Advanced SQL Concepts You Should Know

এই ডকুমেন্টটি SQL শেখার জন্য ১০টি গুরুত্বপূর্ণ ও অ্যাডভান্সড কনসেপ্ট নিয়ে তৈরি করা হয়েছে।
প্রতিটি বিষয়ের ব্যাখ্যা বাংলা ভাষায় দেওয়া হয়েছে এবং তার পাশে ইংরেজিতে কোড ও উদাহরণ দেওয়া হয়েছে যেন সবাই বুঝতে পারে।

---

## 1. 📌 Common Table Expressions (CTEs)

CTE (Common Table Expression) হলো একটা ছোট টেবিলের মতো কিছু, যেটা আমরা বড় একটা SQL প্রশ্নের মধ্যে একবার বানাই আর পরে বারবার ব্যবহার করি।

### ✅ Without CTE:
```sql
SELECT name, salary
FROM fresher.People
WHERE name IN (
    SELECT DISTINCT name
    FROM fresher.population
    WHERE country = "Canada" AND city="Toronto"
)
AND salary >= (
    SELECT AVG(salary)
    FROM fresher.salaries
    WHERE gender = "Female"
);
```

### ✅ With CTE:
```sql
WITH toronto_ppl AS (
    SELECT DISTINCT name 
    FROM fresher.population 
    WHERE country = "Canada" AND city = "Toronto"
),
avg_female_salary AS (
    SELECT AVG(salary) AS avgSalary
    FROM fresher.salaries
    WHERE gender = "Female"
)
SELECT name, salary
FROM fresher.People 
WHERE name IN (SELECT name FROM toronto_ppl)
AND salary >= (SELECT avgSalary FROM avg_female_salary);
```

---

## 2. 🔁 Recursive CTEs

Recursive CTE = নিজের ওপর বারবার কাজ করা

### ✅ Example (Manager Hierarchy):
```sql
WITH RECURSIVE org_structure AS (
    SELECT id, manager_id
    FROM fresher.staff_members
    WHERE manager_id IS NULL

    UNION ALL

    SELECT sm.id, sm.manager_id
    FROM fresher.staff_members sm
    INNER JOIN org_structure os ON os.id = sm.manager_id
)
SELECT * FROM org_structure;
```

---

## 3. 🧩 Temporary Functions

```sql
-- Simple CASE logic (no function)
SELECT name,
CASE 
  WHEN tenure < 1 THEN "analyst"
  WHEN tenure BETWEEN 1 AND 3 THEN "associate" 
  WHEN tenure BETWEEN 3 AND 5 THEN "senior"
  WHEN tenure > 5 THEN "vp"
  ELSE "n/a"
END AS seniority
FROM fresher.employees;
```

### ✅ BigQuery Temporary Function:
```sql
CREATE TEMPORARY FUNCTION get_seniority(tenure INT64) AS (
   CASE WHEN tenure < 1 THEN "analyst"
        WHEN tenure BETWEEN 1 AND 3 THEN "associate"
        WHEN tenure BETWEEN 3 AND 5 THEN "senior"
        WHEN tenure > 5 THEN "vp"
        ELSE "n/a"
   END
);
SELECT name, get_seniority(tenure) AS seniority
FROM employees;
```

---

## 4. 🔄 Pivoting Data with CASE WHEN

```sql
SELECT 
    id,
    MAX(CASE WHEN month = 'Jan' THEN revenue END) AS Jan_Revenue,
    MAX(CASE WHEN month = 'Feb' THEN revenue END) AS Feb_Revenue,
    MAX(CASE WHEN month = 'Mar' THEN revenue END) AS Mar_Revenue
FROM fresher.sales_data
GROUP BY id;
```

---

## 5. 🆚 EXCEPT vs. NOT IN

### ✅ EXCEPT:
```sql
SELECT id, name FROM tableA
EXCEPT
SELECT id, name FROM tableB;
```

### ✅ NOT IN:
```sql
SELECT id, name FROM fresher.tableA
WHERE id NOT IN (SELECT id FROM tableB);
```

---

## 6. 🤝 Self Joins

```sql
SELECT a.Name AS Employee
FROM fresher.Employee a
JOIN fresher.Employee b ON a.ManagerId = b.Id
WHERE a.Salary > b.Salary;
```

| Employee | Manager |
| -------- | ------- |
| Joe      | Sam     |
| Henry    | Max     |

---

## 7. 🏅 Rank vs. Dense Rank vs. Row Number

```sql
SELECT Name, Salary,
RANK() OVER (ORDER BY salary DESC) AS Rank_,
DENSE_RANK() OVER (ORDER BY salary DESC) AS Dense_Rank_,
ROW_NUMBER() OVER (ORDER BY salary DESC) AS Row_Number_
FROM fresher.Employee1;
```

| Function     | Output Example   | Meaning |
|--------------|------------------|---------|
| RANK()       | 1, 1, 3          | Skip ranks |
| DENSE_RANK() | 1, 1, 2          | No skip |
| ROW_NUMBER() | 1, 2, 3          | Always unique |

---

## 8. 📉 Calculating Delta Values

```sql
SELECT month, revenue,
LAG(revenue) OVER (ORDER BY month) AS prev_month_revenue,
revenue - LAG(revenue) OVER (ORDER BY month) AS delta
FROM fresher.monthly_sales;
```

```sql
SELECT month, revenue,
LEAD(revenue) OVER (ORDER BY month) AS next_month_revenue,
revenue - LEAD(revenue) OVER (ORDER BY month) AS delta
FROM fresher.monthly_sales;
```

---

## 9. 📈 Calculating Running Totals

```sql
SELECT Month, revenue,
SUM(revenue) OVER (ORDER BY month) AS cumulative
FROM fresher.monthly_sales;
```

---

## 10. 📆 Date-Time Manipulation

### ✅ 1. Extract Year, Month, Day
```sql
SELECT RecordDate,
EXTRACT(YEAR FROM RecordDate) AS year,
EXTRACT(MONTH FROM RecordDate) AS month,
EXTRACT(DAY FROM RecordDate) AS day
FROM fresher.TemperatureLog;
```

### ✅ 2. Date Difference
```sql
SELECT Id, RecordDate,
DATEDIFF(RecordDate, '2014-01-01') AS Days_from_start
FROM fresher.TemperatureLog;
```

### ✅ 3. Date Add / Subtract
```sql
SELECT Id, RecordDate,
DATE_ADD(RecordDate, INTERVAL 7 DAY) AS Plus7days,
DATE_SUB(RecordDate, INTERVAL 3 DAY) AS minus3days
FROM fresher.TemperatureLog;
```

### ✅ 4. Date Truncate (PostgreSQL / BigQuery)
```sql
SELECT Id, RecordDate,
DATE_TRUNC('month', RecordDate) AS MonthStart
FROM TemperatureLog;
```

---

SQL Optimization, Data Manipulation & Transformation (2025)
1. Query Optimization Techniques
Optimization Strategies:
- Indexing
- Proper Join Strategy Selection
- Avoiding Unnecessary Data Retrieval
-- Table Creation
CREATE TABLE fresher.customers2 (
 customer_id INT PRIMARY KEY,
 name VARCHAR(100),
 email VARCHAR(100),
 city VARCHAR(50)
);
-- Sample Data Insert
INSERT INTO fresher.customers2 (customer_id, name, email, city) VALUES
(1, 'Rahim', 'rahim@email.com', 'Dhaka'),
(2, 'Karim', 'karim@email.com', 'Chittagong'),
(3, 'Rafiq', 'rafiq@email.com', 'Khulna'),
(4, 'Hasan', 'hasan@email.com', 'Dhaka'),
(5, 'Nusrat', 'nusrat@email.com', 'Sylhet');
-- Index Creation
CREATE INDEX idx_city ON fresher.customers2(city);
-- Query Using Index
SELECT * FROM fresher.customers2 WHERE city = 'Dhaka';
-- Execution Plan
EXPLAIN SELECT * FROM fresher.customers2 WHERE city = 'Dhaka';
-- Composite Index
CREATE INDEX idx_name_city ON fresher.customers2(name, city);
SELECT * FROM fresher.customers2 WHERE name = 'Rahim' AND city = 'Dhaka';
2. Data Manipulation
-- Table Creation
CREATE TABLE fresher.monthly_sales2 (
 sales_id INT PRIMARY KEY,
 sales_month VARCHAR(10),
 total_sales INT
);
-- Sample Data Insert
INSERT INTO fresher.monthly_sales2 (sales_id, sales_month, total_sales) VALUES
(1, 'January', 1000),
(2, 'February', 1200),
(3, 'March', 900),
(4, 'April', 1500),
(5, 'May', 1300);
-- Window Function
SELECT
 sales_id,
 sales_month,
 total_sales,
 LAG(total_sales) OVER (ORDER BY sales_id) AS previous_month_sales,
 total_sales - LAG(total_sales) OVER (ORDER BY sales_id) AS sales_difference
FROM fresher.monthly_sales2;
-- CTE + Lag
WITH sales_with_lag AS (
 SELECT *, LAG(total_sales) OVER (ORDER BY sales_id) AS prev_sales
 FROM fresher.monthly_sales2
)
SELECT *, total_sales - prev_sales AS sales_diff FROM sales_with_lag;
3. Data Transformation
-- Table Creation
CREATE TABLE fresher.products2 (
 product_id INT PRIMARY KEY,
 product_name VARCHAR(100),
 category VARCHAR(50),
 sales_amount INT
);
-- Sample Data Insert
INSERT INTO fresher.products2 (product_id, product_name, category, sales_amount) VALUES
(1, 'Laptop', 'Electronics', 5000),
(2, 'Smartphone', 'Electronics', 7000),
(3, 'Refrigerator', 'Appliances', 3000),
(4, 'Oven', 'Appliances', 1500),
(5, 'T-shirt', 'Clothing', 800),
(6, 'Jeans', 'Clothing', 1200);
-- Aggregate + CASE
SELECT
 category,
 SUM(sales_amount) AS total_sales,
 CASE
 WHEN SUM(sales_amount) > 10000 THEN 'High Sales'
 WHEN SUM(sales_amount) BETWEEN 5000 AND 10000 THEN 'Moderate Sales'
 ELSE 'Low Sales'
 END AS sales_category
FROM fresher.products2
GROUP BY category;
-- Running Total
SELECT *,
 SUM(sales_amount) OVER (ORDER BY product_id) AS running_total
FROM fresher.products2;
4. Security Considerations
-- Create User
CREATE USER 'report_user'@'localhost' IDENTIFIED BY 'StrongP@ss123'
