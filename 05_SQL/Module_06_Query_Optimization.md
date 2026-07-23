# SQL Interview Master Guide

# Module 6: Query Optimization (Questions 151–190)

---

# 151. SQL Execution Plan

## Definition

An execution plan describes **how the database executes a SQL query**.

It includes:

- Access method
- Join order
- Join algorithm
- Estimated rows
- Cost
- Sorting
- Parallelism

---

## Example

```sql
EXPLAIN
SELECT *
FROM employees
WHERE id = 10;
```

---

## Interview Answer

> An execution plan is the optimizer's blueprint for executing a SQL query, showing access paths, joins, estimated costs, and row counts.

---

# 152. EXPLAIN

## Definition

Displays the estimated execution plan **without executing the query**.

---

## Example

```sql
EXPLAIN
SELECT *
FROM employees
WHERE salary > 50000;
```

---

## Useful Information

- Index Scan
- Sequential Scan
- Estimated Cost
- Estimated Rows

---

## Interview Answer

> `EXPLAIN` shows how the optimizer intends to execute a query based on statistics and estimated costs.

---

# 153. EXPLAIN ANALYZE

## Definition

Executes the query and displays the **actual execution plan**.

---

## Example

```sql
EXPLAIN ANALYZE
SELECT *
FROM employees
WHERE salary > 50000;
```

---

## Shows

- Actual time
- Actual rows
- Buffers
- Loops
- Execution time

---

## Interview Answer

> `EXPLAIN ANALYZE` executes the query and reports actual execution statistics, making it essential for performance tuning.

---

# 154. Query Optimizer

## Definition

The optimizer chooses the most efficient execution strategy.

---

## Considers

- Indexes
- Statistics
- Join order
- Cost
- Available memory

---

## Interview Answer

> The query optimizer evaluates multiple execution plans and selects the one with the lowest estimated cost.

---

# 155. Cost-Based Optimizer (CBO)

## Definition

Selects execution plans using estimated resource costs.

---

## Cost Factors

- Disk I/O
- CPU
- Memory
- Network
- Sorting

---

## Interview Answer

> A Cost-Based Optimizer estimates the resource consumption of alternative plans and chooses the least expensive one.

---

# 156. Sequential Scan

## Definition

Reads every row of the table.

---

## Used When

- Small table
- Large result set
- No useful index

---

## Interview Answer

> A Sequential Scan is often optimal when a significant percentage of the table must be read.

---

# 157. Parallel Query

## Definition

Splits work across multiple CPU cores.

---

## Example

```text
Worker 1

Worker 2

Worker 3

↓

Gather

↓

Result
```

---

## Interview Answer

> Parallel queries improve performance by distributing query execution across multiple worker processes.

---

# 158. Predicate Pushdown

## Definition

Filters rows as early as possible.

---

## Example

Instead of

```text
Read Entire Table

↓

Filter
```

Use

```text
Filter First

↓

Read Fewer Rows
```

---

## Interview Answer

> Predicate pushdown moves filtering closer to the data source, reducing rows processed by later operations.

---

# 159. Join Elimination

## Definition

Optimizer removes unnecessary joins.

---

## Example

Unused joined table

↓

Removed automatically

---

## Interview Answer

> Join elimination avoids unnecessary table access when joined data is not required.

---

# 160. Materialized Views

## Definition

Stores query results physically.

---

## Example

```sql
CREATE MATERIALIZED VIEW sales_summary AS
SELECT department,
       SUM(amount)
FROM sales
GROUP BY department;
```

---

## Advantages

- Fast reporting
- Precomputed aggregation

---

## Interview Answer

> Materialized views persist query results, reducing computation for expensive analytical queries.

---

# 161. Common Performance Mistakes

- SELECT *
- Missing indexes
- Functions on indexed columns
- Large OFFSET
- Unnecessary JOINs
- Correlated subqueries
- Returning too many rows

---

## Interview Answer

> Common SQL performance issues arise from poor indexing, inefficient query design, and unnecessary data retrieval.

---

# 162. Optimizing OR Conditions

## Slow

```sql
WHERE city='Delhi'

OR city='Mumbai'
```

---

## Better

```sql
WHERE city IN
(
'Delhi',
'Mumbai'
)
```

Or

```sql
UNION ALL
```

---

## Interview Answer

> OR conditions may prevent efficient index usage and can sometimes be rewritten using `IN` or `UNION ALL`.

---

# 163. EXISTS vs IN

| EXISTS | IN |
|----------|----|
| Stops at first match | Compares all values |
| Better for large data | Good for small lists |

---

## Example

```sql
WHERE EXISTS
(
SELECT 1
FROM orders
WHERE ...
)
```

---

## Interview Answer

> `EXISTS` is typically more efficient for large correlated lookups, while `IN` performs well with small static lists.

---

# 164. JOIN vs Subquery

## JOIN

```sql
SELECT *
FROM customers c
JOIN orders o
ON c.id=o.customer_id;
```

---

## Subquery

```sql
SELECT *
FROM customers
WHERE id IN
(
SELECT customer_id
FROM orders
);
```

---

## Interview Answer

> Joins are generally preferred for combining related data, while subqueries are useful when expressing filtering or nested logic.

---

# 165. UNION vs UNION ALL

| UNION | UNION ALL |
|--------|-----------|
| Removes duplicates | Keeps duplicates |
| Slower | Faster |

---

## Interview Answer

> `UNION ALL` avoids duplicate elimination and is therefore faster than `UNION`.

---

# 166. COUNT(*) Optimization

## Example

```sql
SELECT COUNT(*)
FROM employees;
```

---

## Optimization

- Covering index
- Partitioning
- Approximate count
- Materialized view

---

## Interview Answer

> Counting rows on very large tables can be expensive, so indexes, partitions, or precomputed summaries may improve performance.

---

# 167. OFFSET Pagination Problem

## Example

```sql
LIMIT 20 OFFSET 100000;
```

---

## Issue

Database still scans skipped rows.

---

## Interview Answer

> Large OFFSET values degrade performance because skipped rows must still be processed.

---

# 168. Cursor Pagination

## Example

```sql
WHERE id > 1000

ORDER BY id

LIMIT 20;
```

---

## Benefits

- Faster
- Stable
- Scalable

---

## Interview Answer

> Cursor pagination uses the last retrieved key instead of OFFSET, making pagination more efficient on large datasets.

---

# 169. Keyset Pagination

Similar to cursor pagination.

---

## Example

```sql
WHERE created_at >
'2025-01-01'

ORDER BY created_at

LIMIT 20;
```

---

## Interview Answer

> Keyset pagination retrieves rows relative to the last seen key, avoiding expensive OFFSET scans.

---

# 170. Batch Processing

## Example

Instead of

```
1 Million Rows
```

Use

```
10,000

↓

10,000

↓

10,000
```

---

## Interview Answer

> Batch processing reduces memory usage and transaction duration by processing data in smaller chunks.

---

# 171. Query Caching

## Definition

Stores frequently executed query results.

---

## Examples

- Redis
- Memcached
- Materialized View

---

## Interview Answer

> Query caching reduces database load by serving repeated requests from memory instead of re-executing SQL.

---

# 172. Statistics

Optimizer depends on statistics.

Includes

- Row count
- Distinct values
- Histograms
- Distribution

---

## Interview Answer

> Accurate statistics enable the optimizer to estimate row counts and choose efficient execution plans.

---

# 173. ANALYZE

## Definition

Updates table statistics.

---

## Example

```sql
ANALYZE employees;
```

---

## Interview Answer

> `ANALYZE` refreshes optimizer statistics, improving execution plan quality.

---

# 174. Auto Vacuum (PostgreSQL)

Automatically

- VACUUM
- ANALYZE

---

## Benefits

- Removes dead tuples
- Updates statistics

---

## Interview Answer

> Autovacuum automatically cleans dead tuples and updates statistics to maintain PostgreSQL performance.

---

# 175. Memory Parameters

Important PostgreSQL memory settings include:

- work_mem
- shared_buffers
- effective_cache_size
- temp_buffers

---

## Interview Answer

> PostgreSQL memory parameters influence sorting, caching, joins, and overall query execution performance.

---

# 176. work_mem

Memory available per sort or hash operation.

---

## Too Small

```
Disk Sort
```

---

## Larger

```
Memory Sort
```

---

## Interview Answer

> `work_mem` determines how much memory each query operation can use before spilling to disk.

---

# 177. shared_buffers

Database cache.

Typical recommendation

```
25%

of RAM
```

---

## Interview Answer

> `shared_buffers` controls how much memory PostgreSQL uses for caching data pages.

---

# 178. effective_cache_size

Represents estimated OS cache.

Used only for planning.

---

## Interview Answer

> `effective_cache_size` helps the optimizer estimate how much data is likely cached in memory.

---

# 179. temp_buffers

Used for temporary tables.

---

## Interview Answer

> `temp_buffers` specifies memory allocated for temporary tables during a session.

---

# 180. Optimizing Large Tables

Best Practices

- Partitioning
- Indexing
- Vacuum
- Analyze
- Batch processing
- Read replicas

---

## Interview Answer

> Large tables are optimized through partitioning, indexing, maintenance, and efficient query design.

---

# 181. Explain Buffer Hits

Indicates data read from memory instead of disk.

---

## Better

```
High Buffer Hits
```

---

## Interview Answer

> High buffer hit ratios indicate efficient caching and reduced disk I/O.

---

# 182. Explain Heap Fetch

Occurs when the index lacks required columns.

---

## Example

```
Index

↓

Heap
```

---

## Interview Answer

> Heap fetches occur when PostgreSQL must access table rows after reading index entries.

---

# 183. Explain Sort

Sorting may occur for

- ORDER BY
- GROUP BY
- DISTINCT

---

## Interview Answer

> Sort operations arrange rows in memory or on disk and can become expensive for large datasets.

---

# 184. Explain Hash Aggregate

Uses hashing instead of sorting.

---

## Example

```sql
GROUP BY department
```

---

## Interview Answer

> Hash Aggregate groups rows using hash tables and is often faster than sort-based aggregation.

---

# 185. Explain Gather

Combines results from parallel workers.

---

## Flow

```
Worker

Worker

Worker

↓

Gather
```

---

## Interview Answer

> The Gather node merges results produced by parallel worker processes.

---

# 186. Explain Parallel Worker

Worker process executing part of a query.

---

## Interview Answer

> Parallel workers divide query execution across multiple CPU cores to improve performance.

---

# 187. Production Tuning

Checklist

- Monitor queries
- Add indexes
- Update statistics
- Tune memory
- Remove unused indexes
- Cache results
- Analyze plans

---

## Interview Answer

> Production tuning requires continuous monitoring, execution-plan analysis, indexing, and configuration optimization.

---

# 188. Monitoring Slow Queries

Tools

- pg_stat_statements
- Logs
- EXPLAIN ANALYZE
- Monitoring dashboards

---

## Interview Answer

> Slow queries should be identified using monitoring tools and optimized through indexing, rewriting, or configuration tuning.

---

# 189. pg_stat_statements

PostgreSQL extension.

Tracks

- Execution count
- Average time
- Total time
- Rows processed

---

## Example

```sql
SELECT *
FROM pg_stat_statements
ORDER BY total_exec_time DESC;
```

---

## Interview Answer

> `pg_stat_statements` records execution statistics for SQL statements, making it invaluable for identifying expensive queries.

---

# 190. Query Tuning Case Study

## Problem

```sql
SELECT *
FROM orders
WHERE customer_id = 100
ORDER BY created_at DESC;
```

Execution Time

```
12 seconds
```

---

## Optimization Steps

1. Use `EXPLAIN ANALYZE`
2. Add a composite index:

```sql
CREATE INDEX idx_orders_customer_created
ON orders(customer_id, created_at DESC);
```

3. Avoid `SELECT *`
4. Return only required columns.
5. Refresh statistics:

```sql
ANALYZE orders;
```

6. Validate with `EXPLAIN ANALYZE`.

Execution Time

```
12 s → 120 ms
```

---

## Interview Answer

> Query tuning is an iterative process involving execution-plan analysis, indexing, query rewriting, statistics maintenance, and validation of performance improvements.

---

# Module Summary

- SQL performance tuning begins with understanding execution plans using `EXPLAIN` and `EXPLAIN ANALYZE`.
- The Cost-Based Optimizer selects execution plans using estimated CPU, I/O, memory, and row-count statistics.
- Common optimizations include predicate pushdown, join elimination, materialized views, and replacing inefficient constructs such as large `OFFSET` pagination.
- Cursor and keyset pagination are preferred for large datasets because they avoid scanning skipped rows.
- PostgreSQL maintenance tools such as `ANALYZE` and `Autovacuum` ensure accurate statistics and healthy storage.
- Memory settings (`work_mem`, `shared_buffers`, `effective_cache_size`, `temp_buffers`) significantly influence query execution performance.
- Reading execution-plan nodes such as Buffer Hits, Heap Fetches, Hash Aggregate, Gather, and Parallel Workers is essential for diagnosing bottlenecks.
- Production query tuning combines monitoring (`pg_stat_statements`), indexing, query rewriting, configuration tuning, and continuous validation to achieve scalable database performance.