# SQL Senior Interview Master Guide
## Most Asked SQL & PostgreSQL Interview Questions

---

# 1. Explain SQL Query Execution Order

Although SQL is written in one order, the database executes it in a different logical order.

## SQL Query

```sql
SELECT department,
       COUNT(*)
FROM employees
WHERE salary > 50000
GROUP BY department
HAVING COUNT(*) > 5
ORDER BY COUNT(*) DESC
LIMIT 10;
```

---

## Logical Execution Order

```text
FROM

↓

JOIN

↓

WHERE

↓

GROUP BY

↓

HAVING

↓

SELECT

↓

DISTINCT

↓

ORDER BY

↓

LIMIT/OFFSET
```

---

## Step-by-Step

### 1. FROM

Load table(s).

### 2. JOIN

Join required tables.

### 3. WHERE

Filter individual rows.

### 4. GROUP BY

Create groups.

### 5. HAVING

Filter groups.

### 6. SELECT

Return requested columns.

### 7. DISTINCT

Remove duplicates.

### 8. ORDER BY

Sort results.

### 9. LIMIT

Return requested rows.

---

## Interview Answer

> SQL executes logically in the order: FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT. Understanding this explains why aliases from SELECT cannot be referenced in WHERE.

---

# 2. Difference between WHERE and HAVING

| WHERE | HAVING |
|--------|---------|
| Filters rows | Filters groups |
| Before GROUP BY | After GROUP BY |
| Cannot use aggregate functions | Can use aggregate functions |

### Example

```sql
SELECT department,
       COUNT(*)
FROM employees
WHERE salary > 50000
GROUP BY department
HAVING COUNT(*) > 5;
```

---

## Interview Answer

> WHERE filters individual rows before grouping, whereas HAVING filters grouped results after aggregation.

---

# 3. EXISTS vs IN

## EXISTS

```sql
SELECT *
FROM customers c
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.id
);
```

### Characteristics

- Stops after first match
- Best for correlated queries
- Efficient on large datasets

---

## IN

```sql
SELECT *
FROM customers
WHERE id IN (
    SELECT customer_id
    FROM orders
);
```

### Characteristics

- Compares against complete result set
- Good for small lists

---

## Comparison

| EXISTS | IN |
|----------|----|
| Large datasets | Small datasets |
| Stops early | Reads entire set |
| Correlated queries | Static lists |

---

## Interview Answer

> EXISTS is generally faster for large correlated datasets because it stops after finding the first match, whereas IN compares against the complete result set.

---

# 4. UNION vs UNION ALL

## UNION

```sql
SELECT city FROM customers
UNION
SELECT city FROM suppliers;
```

Removes duplicates.

---

## UNION ALL

```sql
SELECT city FROM customers
UNION ALL
SELECT city FROM suppliers;
```

Keeps duplicates.

---

## Comparison

| UNION | UNION ALL |
|--------|-----------|
| Removes duplicates | Keeps duplicates |
| Uses sort/hash | No duplicate removal |
| Slower | Faster |

---

## Interview Answer

> UNION removes duplicate rows, making it slower, while UNION ALL simply concatenates results and is more efficient.

---

# 5. DELETE vs TRUNCATE vs DROP

| DELETE | TRUNCATE | DROP |
|----------|-----------|------|
| Removes rows | Removes all rows | Removes entire table |
| Can use WHERE | No WHERE | Deletes schema |
| Logged row-by-row | Minimal logging | Removes object |
| Can rollback (inside txn) | Usually yes (PostgreSQL) | Can rollback (PostgreSQL txn) |

---

## Interview Answer

> DELETE removes selected rows, TRUNCATE quickly removes all rows while preserving the table structure, and DROP removes the table itself.

---

# 6. ROW_NUMBER() vs RANK() vs DENSE_RANK()

### Example Data

| Salary |
|---------|
| 100 |
| 100 |
| 90 |

---

### ROW_NUMBER()

```
1
2
3
```

---

### RANK()

```
1
1
3
```

---

### DENSE_RANK()

```
1
1
2
```

---

## Interview Answer

> ROW_NUMBER assigns unique numbers, RANK leaves gaps after ties, and DENSE_RANK removes those gaps.

---

# 7. LAG() and LEAD()

## LAG()

Previous row.

```sql
SELECT salary,
       LAG(salary)
OVER(ORDER BY salary)
FROM employees;
```

---

## LEAD()

Next row.

```sql
SELECT salary,
       LEAD(salary)
OVER(ORDER BY salary)
FROM employees;
```

---

## Use Cases

- Trend analysis
- Growth calculations
- Previous value comparison

---

## Interview Answer

> LAG retrieves data from a previous row, while LEAD retrieves data from a following row without using self-joins.

---

# 8. select_related() vs prefetch_related() (Django ORM)

## select_related()

- SQL JOIN
- ForeignKey
- OneToOne

```python
Book.objects.select_related("author")
```

---

## prefetch_related()

- Multiple queries
- ManyToMany
- Reverse ForeignKey

```python
Book.objects.prefetch_related("tags")
```

---

## Comparison

| select_related | prefetch_related |
|----------------|------------------|
| JOIN | Separate query |
| FK/O2O | M2M/Reverse FK |
| Faster for single objects | Better for collections |

---

## Interview Answer

> select_related performs SQL joins for single-valued relationships, whereas prefetch_related executes separate queries and joins data in Python for multi-valued relationships.

---

# 9. B-Tree, Hash, GIN, GiST Indexes

## B-Tree

Default index.

Supports:

- =
- >
- <
- BETWEEN
- ORDER BY

---

## Hash

Optimized for equality lookups.

```
WHERE id = 100
```

---

## GIN

For

- JSONB
- Arrays
- Full-text search

---

## GiST

For

- Geospatial
- Range queries
- PostGIS

---

## Interview Answer

> B-Tree is the default general-purpose index, Hash supports equality lookups, GIN is ideal for JSONB and full-text search, and GiST is designed for complex data types like spatial objects.

---

# 10. EXPLAIN ANALYZE

Example

```sql
EXPLAIN ANALYZE
SELECT *
FROM employees
WHERE salary > 50000;
```

Shows

- Actual execution time
- Actual rows
- Buffers
- Loops

---

## Interview Answer

> EXPLAIN ANALYZE executes the query and reports actual runtime statistics, making it essential for diagnosing performance issues.

---

# 11. Sequential Scan vs Index Scan

## Sequential Scan

Reads every row.

Best for:

- Small tables
- Large result sets

---

## Index Scan

Uses index.

Best for:

- Highly selective queries

---

## Interview Answer

> Sequential scans are efficient when most rows are required, while index scans are preferred when retrieving a small subset of rows.

---

# 12. Composite Index Design

Example

```sql
CREATE INDEX idx_orders
ON orders(customer_id,
          order_date);
```

Supports

```sql
WHERE customer_id=10

AND order_date>'2025-01-01'
```

---

## Leftmost Prefix Rule

Works

```
customer_id

customer_id + order_date
```

Doesn't efficiently support

```
order_date
```

alone.

---

## Interview Answer

> Composite indexes should follow query filter order and the leftmost prefix rule to maximize index usage.

---

# 13. Query Optimization for Millions of Rows

Best Practices

- Partitioning
- Composite indexes
- Cursor pagination
- Read replicas
- Materialized views
- Batch processing
- Avoid SELECT *
- Analyze execution plans

---

## Interview Answer

> Optimizing very large datasets requires efficient indexing, partitioning, query tuning, batching, and minimizing unnecessary I/O.

---

# 14. MVCC and Concurrent Transactions

PostgreSQL creates multiple versions of rows.

```
Old Version

↓

New Version
```

Readers access snapshots.

Writers create new versions.

---

## Benefits

- No reader/writer blocking
- High concurrency
- Better throughput

---

## Interview Answer

> PostgreSQL uses MVCC to allow concurrent reads and writes by maintaining multiple row versions rather than locking readers.

---

# 15. ACID Properties and Isolation Levels

## ACID

Atomicity

Consistency

Isolation

Durability

---

## Isolation Levels

- Read Uncommitted
- Read Committed
- Repeatable Read
- Serializable

---

## Interview Answer

> ACID guarantees reliable transaction processing, while isolation levels control how concurrent transactions interact.

---

# 16. Deadlocks and Resolution

Example

```
Txn A

locks Row1

waits Row2

Txn B

locks Row2

waits Row1
```

---

## Prevention

- Lock resources consistently
- Short transactions
- Retry logic

---

## Interview Answer

> Deadlocks occur when transactions wait on each other's locks. PostgreSQL detects them automatically and aborts one transaction.

---

# 17. WAL and Crash Recovery

Process

```
Write WAL

↓

Flush WAL

↓

Write Data Page
```

---

## Benefits

- Durability
- Crash recovery
- Replication

---

## Interview Answer

> PostgreSQL writes changes to WAL before updating data files, allowing recovery by replaying WAL after a crash.

---

# 18. VACUUM and Autovacuum

VACUUM

- Removes dead tuples

Autovacuum

- Automatic VACUUM
- Automatic ANALYZE

---

## Interview Answer

> VACUUM reclaims storage and maintains MVCC efficiency, while Autovacuum performs this maintenance automatically.

---

# 19. JSON vs JSONB

| JSON | JSONB |
|-------|--------|
| Text | Binary |
| No indexing | GIN indexing |
| Slower | Faster |
| Preserves formatting | Optimized storage |

---

## Interview Answer

> JSONB is preferred in production because it supports indexing and significantly faster queries.

---

# 20. Table Partitioning

Types

- Range
- Hash
- List

Example

```
Orders

2024

2025

2026
```

---

## Benefits

- Faster queries
- Easier maintenance

---

## Interview Answer

> Partitioning divides large tables into smaller pieces, improving query performance and manageability.

---

# 21. Cursor vs Offset Pagination

Offset

```sql
LIMIT 20 OFFSET 100000;
```

Cursor

```sql
WHERE id>100000
LIMIT 20;
```

---

## Comparison

| Cursor | Offset |
|----------|---------|
| Fast | Slower |
| Stable | Can skip rows |
| Scalable | Simpler |

---

## Interview Answer

> Cursor pagination scales much better than OFFSET because it avoids scanning skipped rows.

---

# 22. Designing a Scalable Reporting / Weekly Sales API

Architecture

```text
API

↓

Service Layer

↓

Redis Cache

↓

Read Replica

↓

Aggregated SQL
```

Example

```sql
SELECT
DATE_TRUNC('week',order_date),
SUM(amount)
FROM orders
GROUP BY 1;
```

---

## Interview Answer

> Reporting APIs should execute aggregation in SQL, use read replicas, cache results, and isolate reporting workloads from transactional traffic.

---

# 23. Read Replicas and Sharding

Read Replica

```
Primary

↓

Replica
```

Purpose

- Read scaling

---

Sharding

```
Shard 1

Shard 2

Shard 3
```

Purpose

- Horizontal scaling

---

## Interview Answer

> Read replicas improve read throughput, while sharding distributes data across multiple databases to increase write scalability.

---

# 24. PostgreSQL Internals

## Planner

Chooses best execution plan.

---

## Executor

Runs the plan.

---

## Buffer Manager

Caches pages in memory.

---

Flow

```
SQL

↓

Planner

↓

Executor

↓

Shared Buffers

↓

Disk
```

---

## Interview Answer

> PostgreSQL's Planner selects the optimal execution strategy, the Executor performs it, and the Buffer Manager minimizes disk I/O by caching data pages.

---

# Final Interview Tips

For senior backend and database interviews:

- Explain concepts first, then provide a practical example.
- Discuss trade-offs (performance, consistency, scalability).
- Mention PostgreSQL internals such as MVCC, WAL, Planner, and Buffer Manager where relevant.
- Use production scenarios (millions of rows, replication, partitioning, caching, monitoring).
- When discussing optimization, reference `EXPLAIN ANALYZE`, indexing strategy, partition pruning, and query rewrites.

These topics cover many of the SQL and PostgreSQL questions commonly asked for Senior Backend, Django, and Product-based company interviews.