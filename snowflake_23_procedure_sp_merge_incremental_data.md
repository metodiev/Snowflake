Create a procedure to merge incremental data into a target table using the MERGE statement with update, insert, and delete logic.

- You have a staging table (e.g., sales_staging) with incremental data

- A target table (e.g., sales_target) needs to be updated accordingly

- The staging table contains a column named op_type with values:

- INSERT' for new records

- 'UPDATE' for modifications

- 'DELETE' for deletions

```sql
CREATE OR REPLACE PROCEDURE sp_merge_incremental_data()
RETURNS STRING
LANGUAGE SQL
AS
$$
DECLARE
  sql_text STRING;
BEGIN
  sql_text := '
    MERGE INTO sales_target AS tgt
    USING (
      SELECT * FROM sales_staging
    ) AS src
    ON tgt.sale_id = src.sale_id

    WHEN MATCHED AND src.op_type = ''UPDATE'' THEN
      UPDATE SET
        tgt.customer_id = src.customer_id,
        tgt.product_id = src.product_id,
        tgt.amount = src.amount,
        tgt.sale_date = src.sale_date

    WHEN MATCHED AND src.op_type = ''DELETE'' THEN
      DELETE

    WHEN NOT MATCHED AND src.op_type = ''INSERT'' THEN
      INSERT (sale_id, customer_id, product_id, amount, sale_date)
      VALUES (src.sale_id, src.customer_id, src.product_id, src.amount, src.sale_date);
  ';

  EXECUTE IMMEDIATE sql_text;

  RETURN 'Merge completed successfully.';
END;
$$;
```

- Uses MERGE to handle all three operations (INSERT, UPDATE, DELETE)

- Uses a control column op_type in the staging table to determine the operation

- Assumes sale_id is the primary key

- Easily extendable for other datasets by parameterizing table/column names
