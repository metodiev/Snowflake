#### Write a stored procedure that takes a date range as input and returns daily aggregated sales data, handling cases where no sales exist for a day.

```sql
CREATE OR REPLACE PROCEDURE sp_get_daily_sales_agg(start_date DATE, end_date DATE)
RETURNS TABLE (sale_date DATE, total_sales NUMBER)
LANGUAGE SQL
AS
$$
WITH date_range AS (
    SELECT DATEADD(day, seq4(), :start_date) AS sale_date
    FROM TABLE(GENERATOR(ROWCOUNT => DATEDIFF(day, :start_date, :end_date) + 1))
),
sales_agg AS (
    SELECT 
        CAST(sale_date AS DATE) AS sale_date,
        SUM(amount) AS total_sales
    FROM sales
    WHERE sale_date BETWEEN :start_date AND :end_date
    GROUP BY sale_date
)
SELECT 
    d.sale_date,
    COALESCE(s.total_sales, 0) AS total_sales
FROM date_range d
LEFT JOIN sales_agg s ON d.sale_date = s.sale_date
ORDER BY d.sale_date;
$$;
```

- Generates all dates in the specified range using GENERATOR and seq4()

- Aggregates sales per day from the sales table

- Left joins the date range with aggregated sales to include days with zero sales

- Returns a table with dates and total sales