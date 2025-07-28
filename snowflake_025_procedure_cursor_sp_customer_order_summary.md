Loop through customers, and for each one, loop through their orders and calculate total revenue.

```sql
CREATE OR REPLACE PROCEDURE sp_customer_order_summary()
RETURNS STRING
LANGUAGE SQL
AS
$$
DECLARE
  cur_customer CURSOR FOR
    SELECT customer_id FROM customers;

  cur_order CURSOR FOR
    SELECT order_id, amount FROM orders WHERE customer_id = :customer_id;

  customer_id STRING;
  order_id STRING;
  amount NUMBER;
  total NUMBER;
BEGIN
  FOR record_customer IN cur_customer DO
    customer_id := record_customer.customer_id;
    total := 0;

    FOR record_order IN (SELECT order_id, amount FROM orders WHERE customer_id = :customer_id) DO
      order_id := record_order.order_id;
      amount := record_order.amount;
      total := total + amount;
    END FOR;

    -- Log result or insert into summary table
    INSERT INTO customer_order_summary(customer_id, total_revenue)
    VALUES (customer_id, total);
  END FOR;

  RETURN 'Customer order summary completed.';
END;
$$;
```

- Outer cursor loops through all customers

- Inner (nested) loop goes through orders for the current customer

- Computes total revenue per customer

- Stores the result in a summary table

## Procedure 2: Multi-Level Nested Cursors with Region → Customer → Orders

### Analyze total sales for each customer grouped by region, using deeply nested cursors.

```sql
CREATE OR REPLACE PROCEDURE sp_region_customer_sales_summary()
RETURNS STRING
LANGUAGE SQL
AS
$$
DECLARE
  region_name STRING;
  customer_id STRING;
  order_amount NUMBER;
  total_sales NUMBER;

  cur_region CURSOR FOR
    SELECT DISTINCT region FROM customers;

  cur_customer CURSOR(region STRING) FOR
    SELECT customer_id FROM customers WHERE region = region;

  cur_orders CURSOR(cust_id STRING) FOR
    SELECT amount FROM orders WHERE customer_id = cust_id;

BEGIN
  FOR region_row IN cur_region DO
    region_name := region_row.region;

    FOR customer_row IN cur_customer(region_name) DO
      customer_id := customer_row.customer_id;
      total_sales := 0;

      FOR order_row IN cur_orders(customer_id) DO
        total_sales := total_sales + order_row.amount;
      END FOR;

      -- Save per-customer sales
      INSERT INTO region_customer_sales(region, customer_id, total_amount)
      VALUES (region_name, customer_id, total_sales);
    END FOR;
  END FOR;

  RETURN 'Region-customer sales summary completed.';
END;
$$;
```

- Outer cursor: regions

- Middle cursor: customers in that region

- Inner cursor: orders for each customer

- Inserts a row into region_customer_sales for every customer

## Procedure 3: Cursor with Dynamic SQL Inside Cursor

Loop through a list of tables (e.g., partitioned sales tables), and for each one, run a dynamic aggregation query.

```sql
CREATE OR REPLACE PROCEDURE sp_dynamic_table_scan()
RETURNS STRING
LANGUAGE SQL
AS
$$
DECLARE
  table_name STRING;
  sql_text STRING;
  total_sales NUMBER;

  cur_tables CURSOR FOR
    SELECT table_name FROM my_table_registry WHERE table_type = 'SALES_PARTITION';

BEGIN
  FOR table_row IN cur_tables DO
    table_name := table_row.table_name;

    sql_text := 'SELECT SUM(amount) FROM ' || IDENTIFIER(table_name);
    EXECUTE IMMEDIATE sql_text INTO total_sales;

    -- Store result in summary log
    INSERT INTO table_sales_summary(table_name, total_sales)
    VALUES (table_name, total_sales);
  END FOR;

  RETURN 'Scanned all partitioned sales tables.';
END;
$$;
```

- Loops through a registry of sales-related tables

- Builds and runs a dynamic SELECT SUM(amount) for each table

- Inserts results into a summary table

- Demonstrates cursor + dynamic SQL interaction