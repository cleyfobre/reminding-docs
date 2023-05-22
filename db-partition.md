## Partitions

### What is partitions?

- a technique in MySQL to improve performance and manage large tables by dividing them into smaller, more manageable pieces called partitions.

- Create the partitioned table
  - Use the CREATE TABLE statement with the PARTITION BY clause to define the partitions. Below is an example using RANGE partitioning on the "created_at" column
  ```
	CREATE TABLE orders (
		order_id INT NOT NULL,
		customer_id INT NOT NULL,
		total_amount DECIMAL(10,2) NOT NULL,
		created_at DATETIME NOT NULL
	)
	PARTITION BY RANGE (YEAR(created_at)) (
		PARTITION p0 VALUES LESS THAN (2000),
		PARTITION p1 VALUES LESS THAN (2010),
		PARTITION p2 VALUES LESS THAN (2020),
		PARTITION p3 VALUES LESS THAN MAXVALUE
	);
  ```

- Once the partitioned table is created, you can insert and query data just as you would with a regular table. 
  - MySQL will automatically route rows to the appropriate partitions and optimize queries to read data from specific partitions, where possible.

- Periodically, you may need to add, remove, or modify partitions using the ALTER TABLE statement.
```
ALTER TABLE orders
ADD PARTITION (PARTITION p4 VALUES LESS THAN (2030));
```

### Reading information about partitions

- Here's an example query to show the list of partitioned tables and their partition details
```
SELECT
    TABLE_SCHEMA,
    TABLE_NAME,
    PARTITION_NAME,
    SUBPARTITION_NAME,
    PARTITION_ORDINAL_POSITION,
    SUBPARTITION_ORDINAL_POSITION,
    PARTITION_METHOD,
    SUBPARTITION_METHOD,
    PARTITION_EXPRESSION,
    SUBPARTITION_EXPRESSION,
    PARTITION_DESCRIPTION
FROM
    information_schema.PARTITIONS
WHERE
    PARTITION_NAME IS NOT NULL
    AND TABLE_SCHEMA = 'your_database_name'
ORDER BY
    TABLE_SCHEMA,
    TABLE_NAME,
    PARTITION_ORDINAL_POSITION,
    SUBPARTITION_ORDINAL_POSITION;
```