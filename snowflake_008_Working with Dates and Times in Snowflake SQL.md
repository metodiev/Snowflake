# Prompt 8: Working with Dates and Times in Snowflake SQL

---

##  Objectives

- Understand Snowflake’s date and time data types
- Learn common date/time functions and how to use them:
  - `CURRENT_DATE()`, `CURRENT_TIMESTAMP()`
  - `DATEADD()`, `DATEDIFF()`
  - `DATE_TRUNC()`
  - `TO_DATE()`, `TO_TIMESTAMP()`
  - Extract parts of dates (`YEAR()`, `MONTH()`, `DAY()`, etc.)
- Handle time zones and formatting

---

##  Prerequisites

Assume this sample table:

```sql
CREATE OR REPLACE TABLE events (
    event_id INT,
    event_name STRING,
    event_date DATE,
    event_timestamp TIMESTAMP
);

INSERT INTO events VALUES
(1, 'Launch', '2024-06-01', '2024-06-01 09:00:00'),
(2, 'Meeting', '2024-06-05', '2024-06-05 14:30:00'),
(3, 'Review', '2024-06-10', '2024-06-10 16:45:00');
```

## CURRENT_DATE() and CURRENT_TIMESTAMP()
Return the current date or timestamp.

```sql
SELECT CURRENT_DATE() AS today_date,
       CURRENT_TIMESTAMP() AS current_time;

```

## DATEADD()
Add or subtract intervals from a date or timestamp.

```sql
-- Add 7 days to event_date
SELECT event_name, event_date, DATEADD(day, 7, event_date) AS event_plus_7_days
FROM events;
```

## DATEDIFF()
Calculate the difference between two dates or timestamps.

```sql
-- Days between today and event_date
SELECT event_name, DATEDIFF(day, CURRENT_DATE(), event_date) AS days_until_event
FROM events;
```

## DATE_TRUNC()
Truncate date or timestamp to a specified part (year, month, week, etc.).

```sql
SELECT event_name,
       event_timestamp,
       DATE_TRUNC('month', event_timestamp) AS month_start
FROM events;
```

## TO_DATE() and TO_TIMESTAMP()
Convert strings to date or timestamp types.

```sql
SELECT TO_DATE('2024-07-15', 'YYYY-MM-DD') AS some_date,
       TO_TIMESTAMP('2024-07-15 10:30:00', 'YYYY-MM-DD HH24:MI:SS') AS some_timestamp;
```

## Extract Parts of Date

Get year, month, day, etc.

```sql
SELECT event_name,
       YEAR(event_date) AS year,
       MONTH(event_date) AS month,
       DAY(event_date) AS day
FROM events;
```

## Time Zones

Snowflake stores timestamps in UTC but can display in different time zones.

```sql
SELECT event_name,
       event_timestamp,
       CONVERT_TIMEZONE('UTC', 'America/New_York', event_timestamp) AS event_est
FROM events;
```

| Function                      | Description                             | Example Usage                                            |
| ----------------------------- | --------------------------------------- | -------------------------------------------------------- |
| `CURRENT_DATE()`              | Current date                            | `SELECT CURRENT_DATE();`                                 |
| `CURRENT_TIMESTAMP()`         | Current timestamp                       | `SELECT CURRENT_TIMESTAMP();`                            |
| `DATEADD()`                   | Add/subtract interval                   | `DATEADD(day, 7, event_date)`                            |
| `DATEDIFF()`                  | Difference between dates                | `DATEDIFF(day, date1, date2)`                            |
| `DATE_TRUNC()`                | Truncate to date part                   | `DATE_TRUNC('month', timestamp)`                         |
| `TO_DATE()`, `TO_TIMESTAMP()` | Convert string to date/time             | `TO_DATE('2024-07-15', 'YYYY-MM-DD')`                    |
| `YEAR()`, `MONTH()`, `DAY()`  | Extract parts of a date                 | `YEAR(event_date)`                                       |
| `CONVERT_TIMEZONE()`          | Convert timestamp to different timezone | `CONVERT_TIMEZONE('UTC', 'America/New_York', timestamp)` |



