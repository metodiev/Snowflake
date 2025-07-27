# Prompt 2: Snowflake SQL Basics – SELECT, INSERT, UPDATE, DELETE

---

## Objectives

Learn how to use the four foundational DML (Data Manipulation Language) SQL commands in Snowflake:

- SELECT – Retrieve data from a table
- INSERT – Add new data into a table
- UPDATE – Modify existing records
- DELETE – Remove data from a table

---

##  Prerequisites

Ensure the following before executing queries:

- You're connected to a Snowflake session.
- A **database**, **schema**, and **warehouse** are selected.
- Use this script to create a sample table:

```sql
CREATE OR REPLACE TABLE employees (
    id INT,
    first_name STRING,
    last_name STRING,
    department STRING,
    salary FLOAT
);

```
### SELECT – Retrieve Data
```sql
-- Select all columns
SELECT * FROM employees;

-- Select specific columns
SELECT first_name, department FROM employees;

-- Filtering with WHERE clause
SELECT * FROM employees
WHERE department = 'Engineering';

-- Sorting
SELECT * FROM employees
ORDER BY salary DESC;

-- Limiting results
SELECT * FROM employees
LIMIT 5;
```

### INSERT – Add New Rows

```sql
-- Insert a single row
INSERT INTO employees (id, first_name, last_name, department, salary)
VALUES (1, 'Alice', 'Johnson', 'Engineering', 95000);

-- Insert multiple rows
INSERT INTO employees (id, first_name, last_name, department, salary)
VALUES 
    (2, 'Bob', 'Smith', 'Marketing', 72000),
    (3, 'Carol', 'Williams', 'Engineering', 98000);
```

### UPDATE – Modify Existing Data

```sql
-- Give all engineers a raise
UPDATE employees
SET salary = salary * 1.10
WHERE department = 'Engineering';

-- Change department of an employee
UPDATE employees
SET department = 'Product'
WHERE id = 2;
```

### DELETE – Remove Rows

```sql
-- Delete a single employee
DELETE FROM employees
WHERE id = 3;

-- Delete all employees in Marketing
DELETE FROM employees
WHERE department = 'Marketing';
```