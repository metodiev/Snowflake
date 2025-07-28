Create a procedure to dynamically generate and execute an SQL query that pivots monthly sales data by region.

```sql
CREATE OR REPLACE PROCEDURE sp_pivot_monthly_sales_by_region()
RETURNS VARIANT
LANGUAGE SQL
AS
$$
DECLARE
  sql_text STRING;
BEGIN
  -- Dynamically get distinct regions
  LET region_list = (
    SELECT LISTAGG(DISTINCT region, ', ') WITHIN GROUP (ORDER BY region)
    FROM sales
  );

  -- Build the pivot columns with aggregation
  LET pivot_columns = (
    SELECT LISTAGG(
      'SUM(CASE WHEN region = ''' || region || ''' THEN amount ELSE 0 END) AS "' || region || '"',
      ', '
    ) WITHIN GROUP (ORDER BY region)
    FROM (SELECT DISTINCT region FROM sales)
  );

  -- Build dynamic SQL for monthly aggregation pivoted by region
  sql_text := '
    SELECT
      TO_CHAR(sale_date, ''YYYY-MM'') AS sale_month,
      ' || pivot_columns || '
    FROM sales
    GROUP BY sale_month
    ORDER BY sale_month
  ';

  -- Execute dynamic SQL and return results
  RETURN TABLE(EXECUTE IMMEDIATE :sql_text);
END;
$$;
```

- Retrieves distinct regions dynamically from sales

- Constructs pivot columns using CASE WHEN for each region

- Aggregates monthly sales (YYYY-MM) per region

- Executes dynamic SQL and returns the pivoted result set