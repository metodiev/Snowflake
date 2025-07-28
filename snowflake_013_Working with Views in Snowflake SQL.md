# Working with Views in Snowflake SQL

---

##  Objectives

- Understand the purpose of **views** in Snowflake
- Learn how to:
  - Create regular views (`CREATE VIEW`)
  - Create materialized views (`CREATE MATERIALIZED VIEW`)
  - Replace or drop views
  - Query views like tables
- Explore performance and use-case differences between regular and materialized views

---

##  What Is a View?

A **view** is a named SQL query stored in the database. It acts like a virtual table.  
Views don't store data themselves but instead pull it dynamically from the source tables.

---

##  Sample Table

We’ll use this dataset for our examples:

```sql
CREATE OR REPLACE TABLE orders (
    order_id INT,
    customer_id INT,
    region STRING,
    amount NUMBER(10,2),
    order_date DATE
);

INSERT INTO orders VALUES
(1, 101, 'North', 120.50, '2024-06-01'),
(2, 102, 'South', 89.99, '2024-06-02'),
(3, 101, 'North', 500.00, '2024-06-10'),
(4, 103, 'East', 250.00, '2024-06-12'),
(5, 104, 'South', 300.00, '2024-06-15');
```

### Creating a Regular View

```sql
CREATE OR REPLACE VIEW north_orders AS
SELECT *
FROM orders
WHERE region = 'North';
```

You can do a simple select with the following query
```sql
SELECT * FROM north_orders;
```

### Creating a Materialized View
Materialized views store results physically, allowing faster access.

```sql
CREATE OR REPLACE MATERIALIZED VIEW sales_summary AS
SELECT region, COUNT(*) AS total_orders, SUM(amount) AS total_sales
FROM orders
GROUP BY region;
```

And you can do a select with below statement

```sql
SELECT * FROM sales_summary;
```

###  Replacing and Dropping Views

```sql

-- Replace a view
CREATE OR REPLACE VIEW north_orders AS
SELECT order_id, amount FROM orders WHERE region = 'North';

-- Drop a regular view
DROP VIEW IF EXISTS north_orders;

-- Drop a materialized view
DROP MATERIALIZED VIEW IF EXISTS sales_summary;
```

### Using Views in Joins
You can use views in joins, subqueries, and reports:

```sql
SELECT o.order_id, o.amount, c.customer_name
FROM north_orders o
JOIN customers c ON o.customer_id = c.customer_id;
```

| Feature        | Regular View                | Materialized View                 |
| -------------- | --------------------------- | --------------------------------- |
| Storage        | No                          | Yes (stores result set)           |
| Performance    | Query runs every time       | Faster reads, good for dashboards |
| Use Case       | Flexible, always up to date | Frequent, repeatable queries      |
| Syntax         | `CREATE VIEW`               | `CREATE MATERIALIZED VIEW`        |
| Manual Refresh | N/A                         | Automatic, not real-time          |
| Updatable?     | No                          | No                                |
