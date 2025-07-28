# Common Table Expressions (CTEs) in Snowflake SQL

---

## Objectives

- Understand what a Common Table Expression (CTE) is
- Learn the syntax for creating CTEs
- Chain multiple CTEs together
- Use CTEs to simplify complex subqueries and improve readability

---

## What is a CTE?

A **Common Table Expression (CTE)** is a temporary result set that you can reference within a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statement. It improves readability and structure, especially in complex SQL queries.

---

##  Syntax

```sql
WITH cte_name AS (
  SELECT ...
)
SELECT * FROM cte_name;
```


### Sample Table

```sql
CREATE OR REPLACE TABLE sales (
    id INT,
    region STRING,
    salesperson STRING,
    amount NUMBER(10,2)
);

INSERT INTO sales VALUES
(1, 'North', 'Alice', 1000),
(2, 'South', 'Bob', 1500),
(3, 'North', 'Alice', 700),
(4, 'East', 'Dave', 900),
(5, 'South', 'Bob', 1600),
(6, 'South', 'Carol', 1200);
```

### Basic CTE Example

```sql
WITH regional_totals AS (
  SELECT region, SUM(amount) AS total_sales
  FROM sales
  GROUP BY region
)
SELECT * FROM regional_totals;
```

### CTE With Filtering

```sql
WITH high_sales AS (
  SELECT salesperson, SUM(amount) AS total_amount
  FROM sales
  GROUP BY salesperson
)
SELECT * 
FROM high_sales
WHERE total_amount > 2000;
```

### Chaining Multiple CTEs
```sql
WITH sales_by_person AS (
  SELECT salesperson, SUM(amount) AS total_sales
  FROM sales
  GROUP BY salesperson
),
ranked_sales AS (
  SELECT salesperson, total_sales,
         RANK() OVER (ORDER BY total_sales DESC) AS sales_rank
  FROM sales_by_person
)
SELECT * FROM ranked_sales;
```


| Feature       | Description                         | Example                             |
| ------------- | ----------------------------------- | ----------------------------------- |
| `WITH`        | Declares a CTE                      | `WITH name AS (SELECT ...)`         |
| Reusability   | Improves readability and modularity | Used like a temporary named view    |
| Multiple CTEs | Chainable with commas               | `WITH cte1 AS (...), cte2 AS (...)` |
| Integration   | Can be used in any DML query        | `SELECT`, `INSERT`, etc.            |

