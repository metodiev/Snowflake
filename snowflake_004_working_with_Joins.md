# Working with JOINs in Snowflake SQL

---

## Objectives

Learn how to combine data from multiple tables using different types of SQL JOINs:

- `INNER JOIN`
- `LEFT JOIN`
- `RIGHT JOIN`
- `FULL OUTER JOIN`
- `SELF JOIN`

---

##  Prerequisites

Create the following sample tables to work with JOIN operations:

```sql
-- Employees table
CREATE OR REPLACE TABLE employees (
    id INT,
    first_name STRING,
    department_id INT,
    salary FLOAT
);

-- Departments table
CREATE OR REPLACE TABLE departments (
    department_id INT,
    department_name STRING
);

```

### Let's insert some data

```sql
-- Departments
INSERT INTO departments (department_id, department_name) VALUES
(1, 'Engineering'),
(2, 'Marketing'),
(3, 'Product');

-- Employees
INSERT INTO employees (id, first_name, department_id, salary) VALUES
(1, 'Alice', 1, 95000),
(2, 'Bob', 2, 72000),
(3, 'Carol', 1, 88000),
(4, 'Dave', NULL, 60000);

```
### 1. INNER JOIN

```sql
SELECT e.first_name, d.department_name
FROM employees e
INNER JOIN departments d
  ON e.department_id = d.department_id;

```
### 2. LEFT JOIN (LEFT OUTER JOIN)

```sql
SELECT e.first_name, d.department_name
FROM employees e
LEFT JOIN departments d
  ON e.department_id = d.department_id;
```


### 3. RIGHT JOIN (RIGHT OUTER JOIN)
```sql
SELECT e.first_name, d.department_name
FROM employees e
RIGHT JOIN departments d
  ON e.department_id = d.department_id;
```


### 4. FULL OUTER JOIN
```sql
SELECT e.first_name, d.department_name
FROM employees e
FULL OUTER JOIN departments d
  ON e.department_id = d.department_id;
```


### 5. SELF JOIN
```sql
-- Add manager_id column (if needed)
-- ALTER TABLE employees ADD COLUMN manager_id INT;

-- Example of a self join
SELECT e.first_name AS employee, m.first_name AS manager
FROM employees e
LEFT JOIN employees m
  ON e.manager_id = m.id;
```

| Join Type         | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| `INNER JOIN`      | Rows with matching keys in both tables only                  |
| `LEFT JOIN`       | All rows from the left table; matched rows from the right    |
| `RIGHT JOIN`      | All rows from the right table; matched rows from the left    |
| `FULL OUTER JOIN` | All rows from both tables, matched and unmatched             |
| `SELF JOIN`       | A table joined with itself (e.g. manager/employee structure) |
