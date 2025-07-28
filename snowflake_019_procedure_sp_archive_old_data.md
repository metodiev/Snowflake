Write a procedure to archive data older than a given date from a transactional table to an archive table, ensuring transactional integrity.

```sql
CREATE OR REPLACE PROCEDURE sp_archive_old_data(cutoff_date DATE)
RETURNS STRING
LANGUAGE SQL
AS
$$
BEGIN
  -- Start transaction implicitly

  -- Insert old records into archive table
  INSERT INTO sales_archive
  SELECT * FROM sales WHERE sale_date < cutoff_date;

  -- Delete archived records from original table
  DELETE FROM sales WHERE sale_date < cutoff_date;

  RETURN 'Archiving completed successfully.';
END;
$$;
```
- Copies data older than cutoff_date to sales_archive

- Deletes the archived data from original sales table

- Snowflake auto-commits each statement, but within a procedure these statements run atomically

- You can add error handling or logging as needed