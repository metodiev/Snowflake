# Grouping and Aggregation in Snowflake SQL (`GROUP BY`, `HAVING`)

---

##  Objectives

- Use aggregation functions:
  - `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`
- Group rows using `GROUP BY`
- Filter grouped results using `HAVING`
- Apply conditional logic within grouping

---

##  Prerequisites

We’ll continue using the `employees` table:

```sql
CREATE OR REPLACE TABLE employees (
    emp_id INT,
    name STRING,
    department STRING,
    salary NUMBER(10,2)
);

INSERT INTO employees VALUES
(1, 'Alice', 'Engineering', 120000),
(2, 'Bob', 'Sales', 80000),
(3, 'Carol', 'HR', 60000),
(4, 'Dave', 'Engineering', 95000),
(5, 'Eve', 'Sales', 70000);
```

### Basic Aggregation with GROUP BY

```sql
SELECT
  department,
  COUNT(*) AS employee_count,
  AVG(salary) AS avg_salary,
  MAX(salary) AS highest_salary
FROM employees
GROUP BY department;
```

### Filtering Aggregates with HAVING

```sql
SELECT
  department,
  AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 80000;
```

### Combining GROUP BY with ORDER BY

```sql
SELECT
  department,
  COUNT(*) AS emp_count
FROM employees
GROUP BY department
ORDER BY emp_count DESC;
```

### Conditional Aggregation (Using CASE Inside Aggregates)

```sql
SELECT
  department,
  COUNT(*) AS total_employees,
  SUM(CASE WHEN salary >= 100000 THEN 1 ELSE 0 END) AS high_earners
FROM employees
GROUP BY department;
```


| Clause           | Description                              | Example                                |
| ---------------- | ---------------------------------------- | -------------------------------------- |
| `GROUP BY`       | Groups rows by column values             | `GROUP BY department`                  |
| `HAVING`         | Filters aggregated results               | `HAVING AVG(salary) > 80000`           |
| `COUNT()`        | Counts number of rows                    | `COUNT(*)`                             |
| `SUM()`          | Adds numeric values                      | `SUM(salary)`                          |
| `AVG()`          | Averages numeric values                  | `AVG(salary)`                          |
| `MIN()`, `MAX()` | Gets lowest/highest values               | `MIN(salary)`, `MAX(salary)`           |
| `CASE WHEN`      | Enables conditional logic in aggregation | `SUM(CASE WHEN ... THEN ... ELSE ...)` |




