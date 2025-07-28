#  Window Functions in Snowflake SQL

---

## Objectives

- Understand what window functions are and why they’re useful
- Learn common window functions: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LAG()`, `LEAD()`, `FIRST_VALUE()`, `LAST_VALUE()`
- Use `OVER()` clause with partitions and ordering
- Solve common use cases like running totals, rankings, and differences between rows

---

##  Sample Table

```sql
CREATE OR REPLACE TABLE sales (
    id INT,
    salesperson STRING,
    region STRING,
    amount NUMBER(10,2),
    sale_date DATE
);

INSERT INTO sales VALUES
(1, 'Alice', 'North', 1000, '2024-07-01'),
(2, 'Bob', 'South', 1500, '2024-07-02'),
(3, 'Alice', 'North', 700, '2024-07-03'),
(4, 'Dave', 'East', 900, '2024-07-04'),
(5, 'Bob', 'South', 1600, '2024-07-05'),
(6, 'Carol', 'South', 1200, '2024-07-06');
```

### Using ROW_NUMBER() to Assign Unique Row Numbers

```sql
SELECT
  salesperson,
  amount,
  ROW_NUMBER() OVER (PARTITION BY region ORDER BY amount DESC) AS rn
FROM sales;
```

### Ranking with RANK() and DENSE_RANK()

```sql
SELECT
  salesperson,
  amount,
  RANK() OVER (PARTITION BY region ORDER BY amount DESC) AS rank,
  DENSE_RANK() OVER (PARTITION BY region ORDER BY amount DESC) AS dense_rank
FROM sales;
```

### Accessing Previous or Next Rows with LAG() and LEAD()

```sql
SELECT
  salesperson,
  amount,
  LAG(amount, 1) OVER (PARTITION BY region ORDER BY sale_date) AS prev_amount,
  LEAD(amount, 1) OVER (PARTITION BY region ORDER BY sale_date) AS next_amount
FROM sales;
```

### Running Total with SUM() Window Function

```sql
SELECT
  salesperson,
  sale_date,
  amount,
  SUM(amount) OVER (PARTITION BY salesperson ORDER BY sale_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM sales;
```

### First and Last Values in a Partition

```sql
SELECT
  salesperson,
  amount,
  FIRST_VALUE(amount) OVER (PARTITION BY region ORDER BY sale_date) AS first_sale,
  LAST_VALUE(amount) OVER (PARTITION BY region ORDER BY sale_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_sale
FROM sales;
```

| Function        | Purpose                                | Example Syntax                                                                           |
| --------------- | -------------------------------------- | ---------------------------------------------------------------------------------------- |
| `ROW_NUMBER()`  | Unique sequential number per partition | `ROW_NUMBER() OVER (PARTITION BY region ORDER BY amount)`                                |
| `RANK()`        | Rank with gaps for ties                | `RANK() OVER (PARTITION BY region ORDER BY amount)`                                      |
| `DENSE_RANK()`  | Rank without gaps                      | `DENSE_RANK() OVER (PARTITION BY region ORDER BY amount)`                                |
| `LAG()`         | Access previous row value              | `LAG(amount, 1) OVER (ORDER BY sale_date)`                                               |
| `LEAD()`        | Access next row value                  | `LEAD(amount, 1) OVER (ORDER BY sale_date)`                                              |
| `SUM()`         | Aggregate sum over a window            | `SUM(amount) OVER (ORDER BY sale_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)` |
| `FIRST_VALUE()` | Get first value in window              | `FIRST_VALUE(amount) OVER (ORDER BY sale_date)`                                          |
| `LAST_VALUE()`  | Get last value in window               | `LAST_VALUE(amount) OVER (ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING)`     |
