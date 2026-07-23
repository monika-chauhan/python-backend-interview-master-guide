# SQL Interview Master Guide

# Module 5: Indexing (Questions 111–150)

---

# 111. What is an Index?

## Definition

An **Index** is a data structure that allows the database to locate rows efficiently without scanning the entire table.

Think of it like the index of a book—you don't read every page to find a topic.

---

## Example

```sql
CREATE INDEX idx_employee_email
ON employees(email);
```

---

## Benefits

- Faster SELECT queries
- Faster JOIN operations
- Faster ORDER BY
- Faster GROUP BY

---

## Drawbacks

- Extra storage
- Slower INSERT/UPDATE/DELETE
- Maintenance overhead

---

## Interview Answer

> An index is a data structure that improves query performance by allowing the database to quickly locate rows, at the cost of additional storage and slower write operations.

---

# 112. Clustered Index

## Definition

A Clustered Index determines the physical order of rows on disk.

A table can have **only one** clustered index.

---

## Characteristics

- Data stored in index order
- Fast range scans
- Usually on Primary Key

---

## Example (SQL Server)

```sql
CREATE CLUSTERED INDEX idx_emp_id
ON employees(id);
```

---

## Interview Answer

> A clustered index defines the physical storage order of table data, making range queries highly efficient.

---

# 113. Non-Clustered Index

## Definition

A separate structure containing indexed values and pointers to table rows.

A table can have multiple non-clustered indexes.

---

## Example

```sql
CREATE INDEX idx_salary
ON employees(salary);
```

---

## Interview Answer

> A non-clustered index stores indexed values separately from the table and points to the corresponding rows.

---

# 114. B-Tree Index

## Definition

The default index type in most databases.

---

## Structure

```text
        Root
      /      \
   Internal  Internal
   /   \      /   \
 Leaf Leaf  Leaf Leaf
```

---

## Best For

- Equality (`=`)
- Range (`>`, `<`)
- ORDER BY
- BETWEEN

---

## Interview Answer

> B-Tree indexes organize keys in a balanced tree, providing efficient lookups, inserts, deletes, and range scans in O(log n) time.

---

# 115. Hash Index

## Definition

Uses a hash function to map keys.

---

## Best For

- Equality lookups

```sql
WHERE id = 10
```

---

## Not Suitable For

- Range queries
- ORDER BY

---

## Interview Answer

> Hash indexes provide very fast equality lookups but cannot efficiently support range queries or sorting.

---

# 116. GIN Index (PostgreSQL)

## Definition

Generalized Inverted Index for indexing composite values.

---

## Use Cases

- Full-text search
- JSONB
- Arrays
- Documents

---

## Example

```sql
CREATE INDEX idx_tags
ON products
USING GIN(tags);
```

---

## Interview Answer

> GIN indexes are optimized for indexing arrays, JSONB, and full-text search data types in PostgreSQL.

---

# 117. GiST Index

## Definition

Generalized Search Tree.

---

## Use Cases

- Geospatial data (PostGIS)
- Range types
- Nearest-neighbor search

---

## Interview Answer

> GiST indexes support complex data types and custom search strategies such as geometric and spatial queries.

---

# 118. BRIN Index

## Definition

Block Range Index.

---

## Best For

Very large tables where data is naturally ordered.

---

## Example

```text
Timestamp

Log tables

Time-series data
```

---

## Interview Answer

> BRIN indexes summarize ranges of blocks, making them extremely space-efficient for large sequential datasets.

---

# 119. Bitmap Index

## Definition

Uses bitmaps for each distinct value.

---

## Best For

- Data warehouses
- Low-cardinality columns

---

## Example

```text
Gender

Status

Country
```

---

## Interview Answer

> Bitmap indexes are efficient for low-cardinality columns and analytical workloads but are less suitable for high-write OLTP systems.

---

# 120. Partial Index

## Definition

Indexes only rows matching a condition.

---

## Example

```sql
CREATE INDEX idx_active_users
ON users(email)
WHERE active = TRUE;
```

---

## Benefits

- Smaller index
- Faster maintenance

---

## Interview Answer

> Partial indexes improve performance and reduce storage by indexing only a subset of table rows.

---

# 121. Composite Index

## Definition

Index on multiple columns.

---

## Example

```sql
CREATE INDEX idx_name_city
ON employees(last_name, city);
```

---

## Leftmost Prefix Rule

Can be used for:

- last_name
- last_name, city

Cannot efficiently use only:

- city

---

## Interview Answer

> Composite indexes index multiple columns and are most effective when query predicates match the leftmost indexed columns.

---

# 122. Covering Index

## Definition

Contains all columns required by a query.

---

## Example

```sql
CREATE INDEX idx_cover
ON employees(department, salary);
```

Query:

```sql
SELECT department, salary
FROM employees;
```

---

## Interview Answer

> A covering index satisfies a query entirely from the index without accessing the table.

---

# 123. Expression Index

## Definition

Indexes the result of an expression.

---

## Example

```sql
CREATE INDEX idx_lower_email
ON users(LOWER(email));
```

---

## Interview Answer

> Expression indexes improve performance for queries using computed expressions instead of raw column values.

---

# 124. Functional Index

Similar to Expression Index.

---

## Example

```sql
CREATE INDEX idx_date
ON orders(DATE(order_time));
```

---

## Interview Answer

> Functional indexes store the output of functions, enabling efficient searches on computed values.

---

# 125. Index Selectivity

## Definition

Measures how uniquely an index identifies rows.

---

## High Selectivity

```text
Email

Passport

UUID
```

---

## Low Selectivity

```text
Gender

Boolean

Status
```

---

## Interview Answer

> High-selectivity indexes filter rows efficiently, while low-selectivity indexes may provide limited performance benefits.

---

# 126. Index Cardinality

## Definition

Number of distinct values.

---

## High Cardinality

```
Email

Employee ID
```

---

## Low Cardinality

```
Gender

Yes/No
```

---

## Interview Answer

> Cardinality represents the number of unique values in an indexed column and strongly influences optimizer decisions.

---

# 127. Explain Index Scan

Uses an index to locate matching rows.

---

## Example

```text
Index

↓

Matching Keys

↓

Table Rows
```

---

## Interview Answer

> An Index Scan reads index entries to locate matching rows, avoiding a full table scan.

---

# 128. Bitmap Scan

Combines multiple indexes.

---

## Example

```sql
salary > 50000

AND

city='Delhi'
```

---

## Interview Answer

> Bitmap scans merge multiple index results into a bitmap before fetching matching rows efficiently.

---

# 129. Sequential Scan

Reads every row.

---

## Example

```text
Row1

↓

Row2

↓

Row3
```

---

## Used When

- Small tables
- No index
- Large result set

---

## Interview Answer

> A Sequential Scan reads every table row and may be faster than using an index when a large portion of the table is required.

---

# 130. Index Only Scan

Reads only the index.

---

## Requirement

All required columns exist in the index.

---

## Interview Answer

> An Index Only Scan avoids accessing the table by retrieving all required data directly from the index.

---

# 131. Why Index Slows INSERT?

Every insert updates:

- Table
- Every related index

---

## Complexity

More indexes

↓

More writes

↓

Slower INSERT

---

## Interview Answer

> Inserts become slower because each index must also be updated whenever new rows are inserted.

---

# 132. Index Maintenance

Maintenance includes:

- REINDEX
- VACUUM
- Statistics update
- Monitoring usage

---

## Interview Answer

> Regular index maintenance prevents fragmentation, removes dead entries, and ensures optimal query performance.

---

# 133. Multi-column Index

Equivalent to Composite Index.

---

## Example

```sql
CREATE INDEX idx_city_salary
ON employees(city, salary);
```

---

## Interview Answer

> Multi-column indexes optimize queries filtering or sorting on multiple related columns.

---

# 134. Prefix Index

Indexes only the first portion of a column.

---

## MySQL Example

```sql
CREATE INDEX idx_name
ON users(name(20));
```

---

## Interview Answer

> Prefix indexes reduce storage by indexing only the leading characters of large string columns.

---

# 135. Unique Index

Prevents duplicate values.

---

## Example

```sql
CREATE UNIQUE INDEX idx_email
ON users(email);
```

---

## Interview Answer

> A Unique Index enforces uniqueness while also improving lookup performance.

---

# 136. Explain Cost

Optimizer estimates:

- CPU
- Disk I/O
- Memory
- Network
- Sorting

Lower cost wins.

---

## Interview Answer

> Query cost is an estimate of resources required to execute a query and guides the optimizer in selecting execution plans.

---

# 137. EXPLAIN ANALYZE

Shows:

- Actual execution plan
- Actual timing
- Rows processed
- Actual cost

---

## Example

```sql
EXPLAIN ANALYZE
SELECT *
FROM employees;
```

---

## Interview Answer

> `EXPLAIN ANALYZE` executes the query and reports the real execution plan, timings, and row counts.

---

# 138. Index Fragmentation

Occurs after:

- Deletes
- Updates
- Frequent inserts

---

## Effect

- More disk reads
- Slower scans

---

## Interview Answer

> Fragmentation reduces index efficiency by scattering pages across storage.

---

# 139. Fill Factor

Leaves free space on index pages.

---

## Example

```
100%

↓

Full Pages

80%

↓

20% Free
```

---

## Interview Answer

> Fill Factor reserves space in index pages to reduce page splits during future inserts.

---

# 140. VACUUM (PostgreSQL)

Removes dead tuples.

---

## Benefits

- Reclaims space
- Updates visibility maps
- Improves performance

---

## Interview Answer

> `VACUUM` cleans dead rows left by MVCC without locking the table for normal operations.

---

# 141. VACUUM FULL

Completely rewrites the table.

---

## Benefits

- Shrinks files
- Removes bloat

---

## Drawback

Requires an exclusive lock.

---

## Interview Answer

> `VACUUM FULL` reclaims maximum space but locks the table while rewriting it.

---

# 142. REINDEX

Rebuilds indexes.

---

## Example

```sql
REINDEX TABLE employees;
```

---

## Interview Answer

> `REINDEX` recreates indexes to eliminate fragmentation and improve performance.

---

# 143. CLUSTER

Physically reorders table rows using an index.

---

## Example

```sql
CLUSTER employees
USING idx_salary;
```

---

## Interview Answer

> `CLUSTER` reorganizes table data according to an index, improving locality for sequential access.

---

# 144. Index Bloat

Unused or fragmented index pages.

---

## Causes

- Frequent updates
- Deletes
- MVCC

---

## Interview Answer

> Index bloat wastes storage and degrades performance by increasing unnecessary page reads.

---

# 145. Monitoring Index Usage

PostgreSQL Views

```sql
pg_stat_user_indexes
```

Important Metrics

- Index scans
- Tuple reads
- Tuple fetches

---

## Interview Answer

> Monitoring index usage helps identify unused or inefficient indexes that can be removed or optimized.

---

# 146. Slow Indexes

Common Causes

- Low selectivity
- Wrong column order
- Outdated statistics
- Fragmentation
- Too many indexes

---

## Interview Answer

> An index may perform poorly due to low selectivity, improper design, or outdated optimizer statistics.

---

# 147. Choosing the Correct Index

Consider:

- WHERE clauses
- JOIN columns
- ORDER BY
- GROUP BY
- Cardinality
- Query frequency

---

## Interview Answer

> Index selection should be driven by query patterns, data distribution, and workload characteristics.

---

# 148. Index Best Practices

- Index frequently searched columns
- Avoid indexing every column
- Use composite indexes wisely
- Remove unused indexes
- Monitor with `EXPLAIN ANALYZE`
- Keep statistics updated

---

## Interview Answer

> Effective indexing balances read performance against write overhead while matching real application query patterns.

---

# 149. Production Indexing

Best Practices

- Test indexes in staging
- Monitor query plans
- Avoid duplicate indexes
- Use partial indexes where appropriate
- Schedule maintenance
- Review index usage regularly

---

## Interview Answer

> Production indexing requires continuous monitoring, workload analysis, and maintenance to sustain long-term performance.

---

# 150. Index Interview Scenarios

### Find Slow Query

```sql
EXPLAIN ANALYZE
SELECT *
FROM employees
WHERE email = 'alice@example.com';
```

---

### Optimize JOIN

```sql
CREATE INDEX idx_orders_customer
ON orders(customer_id);
```

---

### Speed Up ORDER BY

```sql
CREATE INDEX idx_salary
ON employees(salary);
```

---

### Optimize Composite Filter

```sql
WHERE city='Delhi'
AND salary>50000
```

```sql
CREATE INDEX idx_city_salary
ON employees(city, salary);
```

---

### Speed Up Case-Insensitive Search

```sql
CREATE INDEX idx_lower_email
ON users(LOWER(email));
```

---

## Interview Answer

> Real-world indexing scenarios involve identifying slow queries, analyzing execution plans, and designing indexes that match filtering, joining, sorting, and aggregation patterns while minimizing write overhead.

---

# Module Summary

- Indexes accelerate data retrieval by reducing the need for full table scans, with B-Tree being the default choice for most workloads.
- Specialized index types such as Hash, GIN, GiST, BRIN, Bitmap, Partial, Composite, Covering, and Functional indexes solve different performance problems.
- Query execution plans include Sequential Scan, Index Scan, Bitmap Scan, and Index Only Scan, each chosen based on estimated cost.
- Index design depends on selectivity, cardinality, query patterns, and workload characteristics.
- Every additional index improves reads but increases the cost of INSERT, UPDATE, and DELETE operations.
- PostgreSQL maintenance commands (`VACUUM`, `VACUUM FULL`, `REINDEX`, `CLUSTER`) help control fragmentation and index bloat.
- `EXPLAIN` and `EXPLAIN ANALYZE` are essential tools for understanding optimizer decisions and validating indexing strategies.
- Effective production indexing requires continuous monitoring, regular maintenance, and aligning indexes with real application access patterns.