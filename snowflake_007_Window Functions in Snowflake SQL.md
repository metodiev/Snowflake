# Window Functions in Snowflake SQL

---

##  Objectives

- Understand what window functions are and how they differ from aggregate functions.
- Learn common window functions like:
  - `ROW_NUMBER()`
  - `RANK()`
  - `DENSE_RANK()`
  - `NTILE()`
  - `LAG()` and `LEAD()`
- Use the `OVER()` clause with `PARTITION BY` and `ORDER BY`.

---

## Prerequisites

We will use the following table:

```sql
CREATE OR REPLACE TABLE sales (
    sales_id INT,
    salesperson STRING,
    region STRING,
    amount FLOAT,
    sale_date DATE
);

INSERT INTO sales VALUES
(1, 'Alice', 'North', 1000, '2024-01-10'),
(2, 'Bob', 'South', 1500, '2024-01-11'),
(3, 'Alice', 'North', 700, '2024-01-15'),
(4, 'Dave', 'East', 900, '2024-01-12'),
(5, 'Carol', 'South', 1200, '2024-01-20'),
(6, 'Bob', 'South', 1600, '2024-01-25');
```

## ROW_NUMBER()
Assigns a unique sequential number to rows within a partition.

```sql
SELECT
  salesperson,
  region,
  amount,
  ROW_NUMBER() OVER (PARTITION BY region ORDER BY amount DESC) AS rank_in_region
FROM sales;
```

## RANK() and DENSE_RANK()
Assign ranks with possible ties.

RANK() skips ranks after ties.

DENSE_RANK() does not skip ranks.

```sql
SELECT
  salesperson,
  region,
  amount,
  RANK() OVER (PARTITION BY region ORDER BY amount DESC) AS rank,
  DENSE_RANK() OVER (PARTITION BY region ORDER BY amount DESC) AS dense_rank
FROM sales;
```

## NTILE()
Divides rows into a specified number of groups.

```sql
SELECT
  salesperson,
  region,
  amount,
  NTILE(2) OVER (PARTITION BY region ORDER BY amount DESC) AS quartile
FROM sales;
```


## LAG() and LEAD()
Access previous or next row data without a self join.

```sql
SELECT
  salesperson,
  sale_date,
  amount,
  LAG(amount, 1) OVER (PARTITION BY salesperson ORDER BY sale_date) AS previous_sale,
  LEAD(amount, 1) OVER (PARTITION BY salesperson ORDER BY sale_date) AS next_sale
FROM sales;
```

| Function       | Description                            | Usage Example                                                  |
| -------------- | -------------------------------------- | -------------------------------------------------------------- |
| `ROW_NUMBER()` | Unique sequential number per partition | `ROW_NUMBER() OVER (PARTITION BY region ORDER BY amount DESC)` |
| `RANK()`       | Rank with gaps for ties                | Same as above, but ties cause gaps                             |
| `DENSE_RANK()` | Rank without gaps                      | Similar to `RANK()` but no gaps                                |
| `NTILE(n)`     | Divides rows into `n` groups           | `NTILE(4) OVER (...)`                                          |
| `LAG()`        | Access previous row’s value            | `LAG(amount, 1) OVER (...)`                                    |
| `LEAD()`       | Access next row’s value                | `LEAD(amount, 1) OVER (...)`                                   |




