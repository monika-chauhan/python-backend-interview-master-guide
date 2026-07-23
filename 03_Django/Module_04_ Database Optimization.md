# Django Interview Master Guide

# Module 4: Database Optimization (Questions 46–65)

---

# 46. N+1 Query Problem

## Definition

The **N+1 Query Problem** occurs when an application executes:

- **1 query** to fetch parent objects
- **N additional queries** to fetch related objects

This results in excessive database calls and poor performance.

---

## Example

```python
employees = Employee.objects.all()

for employee in employees:
    print(employee.department.name)
```

If there are **100 employees**, Django executes:

```
1 Query -> Employees

100 Queries -> Departments

Total = 101 Queries
```

---

## Solution

```python
employees = Employee.objects.select_related("department")
```

Now only **1 SQL query** is executed.

---

## For ManyToMany

```python
Course.objects.prefetch_related("students")
```

---

## Interview Answer

> The N+1 query problem occurs when fetching related objects individually for each parent record. It is solved using `select_related()` for single-valued relationships and `prefetch_related()` for multi-valued relationships.

---

# 47. How to Detect N+1?

## Methods

### 1. Django Debug Toolbar

Shows:

- SQL queries
- Duplicate queries
- Execution time

---

### 2. Logging SQL

```python
LOGGING = {
    "loggers": {
        "django.db.backends": {
            "level": "DEBUG",
        }
    }
}
```

---

### 3. Django Test

```python
self.assertNumQueries(2)
```

---

### 4. Database Logs

- PostgreSQL
- MySQL
- Cloud SQL Insights

---

## Signs

- Hundreds of similar queries
- Slow page rendering
- High DB CPU

---

## Interview Answer

> N+1 queries can be detected using Django Debug Toolbar, SQL logging, database monitoring tools, and query count assertions in tests.

---

# 48. Django Debug Toolbar

## What is it?

A development tool for profiling Django applications.

---

## Shows

- SQL queries
- Cache usage
- Templates
- Signals
- Middleware
- Headers
- Request timing

---

## Installation

```bash
pip install django-debug-toolbar
```

---

## Middleware

```python
MIDDLEWARE = [
    ...
    "debug_toolbar.middleware.DebugToolbarMiddleware",
]
```

---

## Benefits

- Detect N+1
- Find duplicate SQL
- Profile requests
- Optimize performance

---

## Interview Answer

> Django Debug Toolbar provides detailed insights into request processing, including executed SQL queries, timing, templates, cache usage, and middleware, making it invaluable for performance tuning during development.

---

# 49. Database Indexing

## Definition

An index is a data structure that speeds up lookups by avoiding full table scans.

---

## Without Index

```
Scan

1

2

3

...

10 Million Rows
```

---

## With Index

```
B-Tree

↓

Binary Search

↓

Target Row
```

---

## Example

```python
email = models.EmailField(db_index=True)
```

---

## Benefits

- Faster SELECT
- Faster ORDER BY
- Faster WHERE

---

## Cost

- Slower INSERT
- Slower UPDATE
- Extra storage

---

## Interview Answer

> Indexes improve read performance by allowing the database to locate rows efficiently, but they introduce additional storage overhead and slightly slower write operations.

---

# 50. Composite Index

Indexes multiple columns together.

---

## Example

```python
class Meta:
    indexes = [
        models.Index(
            fields=["department", "salary"]
        )
    ]
```

---

## SQL

```sql
CREATE INDEX idx_dept_salary
ON employee(department, salary);
```

---

## Best For

```
WHERE department = ?

AND salary > ?
```

---

## Interview Answer

> A composite index spans multiple columns and is most effective when queries filter or sort using those columns in the indexed order.

---

# 51. Unique Index

Guarantees uniqueness.

---

## Example

```python
email = models.EmailField(unique=True)
```

---

## Benefits

- Prevent duplicates
- Fast lookups

---

## SQL

```sql
CREATE UNIQUE INDEX
```

---

## Interview Answer

> A unique index enforces uniqueness while also improving lookup performance for the indexed column.

---

# 52. Explain `Meta.indexes`

Defines custom indexes.

---

## Example

```python
class Employee(models.Model):

    class Meta:
        indexes = [
            models.Index(fields=["salary"]),
            models.Index(fields=["department", "salary"]),
        ]
```

---

## Types

- Index
- UniqueConstraint
- Functional indexes (database support dependent)

---

## Interview Answer

> `Meta.indexes` allows developers to define custom database indexes, including single-column and composite indexes, to optimize query performance.

---

# 53. Query Optimization

## Best Practices

### Use `select_related()`

Avoid N+1.

---

### Use `prefetch_related()`

Reduce many-to-many queries.

---

### Retrieve Only Needed Fields

```python
.only()

.defer()

.values()
```

---

### Add Proper Indexes

Optimize filtering and sorting.

---

### Filter in Database

Avoid filtering in Python.

---

### Use `exists()`

Instead of:

```python
len(qs) > 0
```

Use:

```python
qs.exists()
```

---

### Use Bulk Operations

```python
bulk_create()

bulk_update()
```

---

## Interview Answer

> Query optimization involves reducing database round trips, selecting only required fields, using efficient joins, adding appropriate indexes, leveraging bulk operations, and pushing filtering and aggregation into the database.

---

# 54. Explain Query Planner

The query planner determines the most efficient execution strategy.

---

## Chooses

- Index Scan
- Sequential Scan
- Hash Join
- Nested Loop
- Merge Join

---

## PostgreSQL Example

```
SELECT

↓

Planner

↓

Execution Plan

↓

Database
```

---

## Factors

- Table statistics
- Indexes
- Row estimates
- Join order

---

## Interview Answer

> The database query planner analyzes SQL and selects the most efficient execution strategy based on indexes, statistics, estimated row counts, and available join algorithms.

---

# 55. Explain `.explain()`

Displays the execution plan for a QuerySet.

---

## Example

```python
Employee.objects.filter(
    salary__gt=50000
).explain()
```

---

## Output (Simplified)

```
Index Scan

Cost

Rows

Width
```

---

## Why Use It?

- Verify index usage
- Detect table scans
- Analyze join strategy
- Troubleshoot slow queries

---

## Interview Answer

> Django's `.explain()` method returns the database execution plan, helping developers understand how a query will be executed and whether indexes are being used efficiently.

---

# 56. Pagination Optimization

Avoid loading all rows.

---

## Django

```python
Paginator(queryset, 20)
```

---

## Large Tables

Avoid:

```python
COUNT(*)
```

when possible, as it can be expensive on very large datasets.

---

## Best Practices

- Cursor Pagination
- Indexed ordering
- Limit selected fields

---

## Interview Answer

> Efficient pagination retrieves only a subset of records, orders by indexed columns, and avoids expensive operations on very large datasets whenever possible.

---

# 57. Offset vs Cursor Pagination

## Offset Pagination

```python
?page=100
```

SQL

```sql
OFFSET 9900
LIMIT 100
```

---

### Pros

- Simple
- Random page access

### Cons

- Slow on large offsets
- Susceptible to duplicate or skipped records during concurrent inserts/deletes

---

## Cursor Pagination

```python
?id > 1000
```

---

### Pros

- Fast
- Stable
- Scalable

### Cons

- No arbitrary page numbers

---

## Interview Answer

> Offset pagination is simple but inefficient for large datasets, whereas cursor pagination uses an indexed cursor to achieve better performance and consistency.

---

# 58. Connection Pooling

Reuses database connections.

---

## Without Pool

```
Request

↓

New Connection

↓

Close
```

---

## With Pool

```
Pool

↓

Reuse Existing Connection
```

---

## Benefits

- Lower latency
- Better throughput
- Reduced connection overhead

---

## Tools

- PgBouncer
- ProxySQL
- Cloud SQL connection pools

---

## Interview Answer

> Connection pooling reuses existing database connections, reducing connection setup overhead and improving application throughput.

---

# 59. Read Replica

Separates read and write workloads.

---

## Architecture

```
Application

↓

Primary Database

↓

Replication

↓

Read Replica

↓

SELECT Queries
```

---

## Usage

- Reads → Replica
- Writes → Primary

---

## Benefits

- Reduced primary load
- Better scalability
- Higher read throughput

---

## Interview Answer

> A read replica is a replicated database used primarily for read operations, allowing the primary database to focus on writes.

---

# 60. Database Sharding

Splits data across multiple databases.

---

## Example

```
Shard 1

User 1-1M

Shard 2

User 1M-2M

Shard 3

User 2M-3M
```

---

## Benefits

- Horizontal scaling
- Higher storage capacity
- Better throughput

---

## Challenges

- Cross-shard joins
- Data rebalancing
- Operational complexity

---

## Interview Answer

> Sharding distributes data across multiple database instances, enabling horizontal scaling at the cost of increased architectural complexity.

---

# 61. Partitioning

Splits a large table into smaller partitions.

---

## Types

### Range

```
2023

2024

2025
```

---

### Hash

```
Hash(id)
```

---

### List

```
Country

US

IN

UK
```

---

## Benefits

- Faster scans
- Easier maintenance
- Improved pruning for eligible queries

---

## Interview Answer

> Partitioning divides a large table into smaller logical partitions while preserving a single table interface, improving maintenance and query performance.

---

# 62. Optimizing Millions of Records

## Best Practices

- Add indexes
- Batch processing
- Bulk inserts
- Bulk updates
- Partitioning
- Read replicas
- Sharding (if required)
- Cursor pagination
- Asynchronous processing
- Cache frequently accessed data

---

## Avoid

```python
for obj in queryset:
```

over millions of rows without chunking.

---

## Use

```python
queryset.iterator(chunk_size=1000)
```

---

## Interview Answer

> Large datasets are optimized using indexing, bulk operations, batching, streaming iterators, partitioning, caching, read replicas, and asynchronous processing to minimize memory usage and database load.

---

# 63. Batch Processing

Process records in small chunks.

---

## Example

```python
for employee in Employee.objects.iterator(
    chunk_size=1000
):
    process(employee)
```

---

## Benefits

- Lower memory usage
- Better scalability
- Faster processing

---

## Interview Answer

> Batch processing handles records in manageable chunks, reducing memory consumption and enabling efficient processing of very large datasets.

---

# 64. Transactions

A transaction is a group of operations that either all succeed or all fail.

---

## Example

```python
from django.db import transaction

with transaction.atomic():
    order.save()
    payment.save()
```

---

## ACID Properties

- Atomicity
- Consistency
- Isolation
- Durability

---

## Interview Answer

> Transactions ensure that multiple database operations execute as a single unit of work, preserving data integrity through the ACID principles.

---

# 65. Atomic Operations

Use `transaction.atomic()` to guarantee all-or-nothing execution.

---

## Example

```python
from django.db import transaction

with transaction.atomic():

    withdraw()

    deposit()
```

If any operation raises an exception, the entire transaction is rolled back.

---

## Nested Transactions

Django uses **savepoints** inside nested `atomic()` blocks (when supported by the database), allowing partial rollback to the savepoint while preserving the outer transaction.

---

## Benefits

- Prevent partial updates
- Maintain consistency
- Safe financial operations
- Reliable inventory updates

---

## Interview Answer

> Atomic operations ensure that a sequence of database changes either commits completely or rolls back entirely, preventing partial updates and maintaining consistency.

---

# Module Summary

- The **N+1 query problem** is solved using `select_related()` and `prefetch_related()`.
- Performance issues can be identified using **Django Debug Toolbar**, SQL logging, query-count tests, and database monitoring.
- Proper **indexing** (single, composite, and unique indexes) significantly improves read performance.
- `Meta.indexes` provides explicit control over database indexing strategies.
- Query optimization includes efficient joins, selective field retrieval, bulk operations, and database-side filtering.
- The database **query planner** chooses efficient execution strategies, and `.explain()` reveals those execution plans.
- **Cursor pagination** is preferred over offset pagination for large datasets.
- **Connection pooling** reduces connection overhead, while **read replicas** improve read scalability.
- **Sharding** distributes data across databases, and **partitioning** divides large tables for improved manageability and performance.
- Large-scale systems benefit from batching, streaming iterators, transactions, and atomic operations to ensure scalability and data integrity.