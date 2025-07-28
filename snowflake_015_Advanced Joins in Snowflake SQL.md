# Advanced Joins in Snowflake SQL

---

##  Objectives

- Understand different types of joins in Snowflake: INNER, LEFT, RIGHT, FULL, CROSS, and SELF joins
- Learn how to use **JOIN USING** and **NATURAL JOIN**
- Explore anti-joins and semi-joins using `EXISTS` and `NOT EXISTS`
- See examples of complex join conditions and performance tips

---

##  Sample Tables

```sql
CREATE OR REPLACE TABLE customers (
    customer_id INT,
    customer_name STRING,
    region STRING
);

INSERT INTO customers VALUES
(101, 'Alice', 'North'),
(102, 'Bob', 'South'),
(103, 'Carol', 'East'),
(104, 'Dave', 'West');

CREATE OR REPLACE TABLE orders (
    order_id INT,
    customer_id INT,
    amount NUMBER(10,2),
    order_date DATE
);

INSERT INTO orders VALUES
(1, 101, 120.50, '2024-06-01'),
(2, 102, 89.99, '2024-06-02'),
(3, 101, 500.00, '2024-06-10'),
(4, 103, 250.00, '2024-06-12'),
(5, 105, 300.00, '2024-06-15'); -- note: customer_id 105 not in customers
```

### INNER JOIN
Returns matching rows in both tables.

```sql
SELECT c.customer_name, o.order_id, o.amount
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id;
```

### LEFT JOIN
Returns all rows from left table + matching rows from right table.

```sql
SELECT c.customer_name, o.order_id, o.amount
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;
```

### RIGHT JOIN
Returns all rows from right table + matching rows from left table.


```sql
SELECT c.customer_name, o.order_id, o.amount
FROM customers c
RIGHT JOIN orders o ON c.customer_id = o.customer_id;
```

### FULL OUTER JOIN
Returns all rows when there is a match in one of the tables.

```sql
SELECT c.customer_name, o.order_id, o.amount
FROM customers c
FULL OUTER JOIN orders o ON c.customer_id = o.customer_id;
```

### CROSS JOIN
Returns Cartesian product (all combinations).

```sql
SELECT c.customer_name, o.order_id
FROM customers c
CROSS JOIN orders o
LIMIT 10;
```

### SELF JOIN
Join table to itself to compare rows.

```sql
SELECT a.customer_name AS customer_a, b.customer_name AS customer_b
FROM customers a
JOIN customers b ON a.region = b.region AND a.customer_id <> b.customer_id;
```

### JOIN USING and NATURAL JOIN
JOIN USING simplifies join when columns have the same name

```sql
SELECT *
FROM customers c
JOIN orders o USING (customer_id);
```
NATURAL JOIN automatically joins on all columns with the same name

```sql
SELECT *
FROM customers
NATURAL JOIN orders;
```

### Semi-Join and Anti-Join with EXISTS
Find customers with orders (semi-join):

```sql
SELECT customer_name
FROM customers c
WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);

```

Find customers without orders (anti-join):

```sql
SELECT customer_name
FROM customers c
WHERE NOT EXISTS (
  SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);
```


| Join Type       | Description                                      | Example                                   |
| --------------- | ------------------------------------------------ | ----------------------------------------- |
| INNER JOIN      | Returns matching rows only                       | `FROM A INNER JOIN B ON A.id = B.id`      |
| LEFT JOIN       | All from left + matching from right              | `FROM A LEFT JOIN B ON A.id = B.id`       |
| RIGHT JOIN      | All from right + matching from left              | `FROM A RIGHT JOIN B ON A.id = B.id`      |
| FULL OUTER JOIN | All rows from both tables                        | `FROM A FULL OUTER JOIN B ON A.id = B.id` |
| CROSS JOIN      | Cartesian product                                | `FROM A CROSS JOIN B`                     |
| SELF JOIN       | Join table to itself                             | `FROM A a JOIN A b ON ...`                |
| JOIN USING      | Join on columns with same name                   | `JOIN B USING(column_name)`               |
| NATURAL JOIN    | Join automatically on all columns with same name | `FROM A NATURAL JOIN B`                   |
| Semi-Join       | Filter rows based on existence in another table  | Use `EXISTS`                              |
| Anti-Join       | Filter rows not existing in another table        | Use `NOT EXISTS`                          |

