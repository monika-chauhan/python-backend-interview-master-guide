# SQL Interview Master Guide

# Module 3: Joins (Questions 51–80)

---

# 51. INNER JOIN

## Definition

`INNER JOIN` returns only the rows that have matching values in both tables.

---

## Example

```sql
SELECT e.id,
       e.name,
       d.department_name
FROM employees e
INNER JOIN departments d
ON e.department_id = d.id;
```

---

## Result

```text
Employees
+----+--------+---------------+
| id | name   | department_id |
+----+--------+---------------+
| 1  | Alice  | 10            |
| 2  | Bob    | 20            |
| 3  | Carol  | NULL          |
+----+--------+---------------+

Departments
+----+------------------+
| id | department_name  |
+----+------------------+
|10  | IT               |
|20  | HR               |
+----+------------------+

Result
+----+-------+------------------+
| 1  | Alice | IT               |
| 2  | Bob   | HR               |
+----+-------+------------------+
```

---

## Interview Answer

> `INNER JOIN` returns only rows that satisfy the join condition in both tables.

---

# 52. LEFT JOIN

## Definition

Returns all rows from the left table and matching rows from the right table.

---

## Example

```sql
SELECT e.name,
       d.department_name
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.id;
```

---

## Result

```text
Alice  IT
Bob    HR
Carol  NULL
```

---

## Interview Answer

> `LEFT JOIN` preserves all rows from the left table and fills unmatched columns from the right table with NULL values.

---

# 53. RIGHT JOIN

## Definition

Returns all rows from the right table and matching rows from the left table.

---

## Example

```sql
SELECT e.name,
       d.department_name
FROM employees e
RIGHT JOIN departments d
ON e.department_id = d.id;
```

---

## Interview Answer

> `RIGHT JOIN` preserves every row from the right table while returning matching rows from the left table.

---

# 54. FULL OUTER JOIN

## Definition

Returns every row from both tables.

Unmatched rows contain NULLs.

---

## Example

```sql
SELECT *
FROM employees
FULL OUTER JOIN departments
ON employees.department_id = departments.id;
```

---

## Interview Answer

> `FULL OUTER JOIN` combines the results of LEFT and RIGHT JOIN, returning all matching and non-matching rows.

---

# 55. CROSS JOIN

## Definition

Returns the Cartesian product.

---

## Example

```sql
SELECT *

FROM colors

CROSS JOIN sizes;
```

---

## Result

```text
3 colors

×

4 sizes

=

12 rows
```

---

## Interview Answer

> `CROSS JOIN` combines every row of one table with every row of another table.

---

# 56. SELF JOIN

## Definition

A table joined with itself.

---

## Example

```sql
SELECT e.name,
       m.name AS manager
FROM employees e

LEFT JOIN employees m

ON e.manager_id=m.id;
```

---

## Interview Answer

> A SELF JOIN relates rows within the same table, typically for hierarchical relationships such as employees and managers.

---

# 57. NATURAL JOIN

## Definition

Automatically joins columns having the same names.

---

## Example

```sql
SELECT *

FROM employees

NATURAL JOIN departments;
```

---

## Why Avoid?

- Hidden join conditions
- Schema changes may break queries
- Difficult to maintain

---

## Interview Answer

> `NATURAL JOIN` automatically joins columns with identical names but is generally avoided because implicit join conditions reduce readability and maintainability.

---

# 58. Difference between INNER and LEFT JOIN

| INNER JOIN | LEFT JOIN |
|------------|-----------|
| Only matching rows | All left rows |
| Removes unmatched rows | Preserves unmatched left rows |
| Faster in many cases | May return NULLs |

---

## Interview Answer

> `INNER JOIN` returns only matching rows, whereas `LEFT JOIN` returns all rows from the left table regardless of matches.

---

# 59. Anti Join

## Purpose

Return rows that have no match.

---

## Example

```sql
SELECT *

FROM customers c

WHERE NOT EXISTS
(
    SELECT 1

    FROM orders o

    WHERE o.customer_id=c.id
);
```

---

## Interview Answer

> An Anti Join returns rows from one table that do not have corresponding rows in another table.

---

# 60. Semi Join

## Purpose

Return rows that have at least one match.

---

## Example

```sql
SELECT *

FROM customers c

WHERE EXISTS
(
    SELECT 1

    FROM orders o

    WHERE o.customer_id=c.id
);
```

---

## Interview Answer

> A Semi Join returns rows from the outer table if at least one matching row exists in the related table.

---

# 61. Joining Multiple Tables

## Example

```sql
SELECT o.id,

       c.name,

       p.product_name

FROM orders o

JOIN customers c

ON o.customer_id=c.id

JOIN products p

ON o.product_id=p.id;
```

---

## Interview Answer

> Multiple tables can be joined by chaining JOIN clauses using appropriate foreign key relationships.

---

# 62. Join Algorithms

Database engines use different algorithms:

- Nested Loop Join
- Hash Join
- Merge Join

The optimizer selects the most efficient one.

---

## Interview Answer

> The database optimizer chooses different join algorithms depending on indexes, table size, sorting, and estimated costs.

---

# 63. Nested Loop Join

## Working

```text
Outer Table

↓

For every row

↓

Search Inner Table
```

---

## Complexity

```
O(N × M)
```

---

## Best For

- Small tables
- Indexed lookups

---

## Interview Answer

> Nested Loop Join compares each row from one table with matching rows in another table and is efficient when suitable indexes exist.

---

# 64. Merge Join

## Requirement

Both inputs must be sorted.

---

## Flow

```text
Sorted Table A

↓

Sorted Table B

↓

Merge
```

---

## Complexity

```
O(N+M)
```

---

## Interview Answer

> Merge Join efficiently joins sorted datasets by scanning both inputs simultaneously.

---

# 65. Hash Join

## Working

```
Small Table

↓

Build Hash Table

↓

Large Table

↓

Probe Hash Table
```

---

## Best For

- Large datasets
- Equality joins

---

## Complexity

Approximately

```
O(N+M)
```

---

## Interview Answer

> Hash Join builds an in-memory hash table from the smaller dataset and probes it using rows from the larger dataset, making it efficient for equality joins.

---

# 66. How Query Planner Chooses Join?

The optimizer evaluates:

- Statistics
- Table size
- Available indexes
- Estimated rows
- Memory
- Sort order

---

## Example

Small tables

```
Nested Loop
```

Large tables

```
Hash Join
```

Sorted tables

```
Merge Join
```

---

## Interview Answer

> The query planner estimates execution costs using table statistics and selects the join algorithm with the lowest estimated cost.

---

# 67. Join Order Optimization

Changing join order can significantly impact performance.

---

## Example

Instead of

```
Large

↓

Large

↓

Small
```

Use

```
Small

↓

Small

↓

Large
```

---

## Interview Answer

> Query optimizers determine the join order that minimizes intermediate result sizes and overall execution cost.

---

# 68. Cartesian Product

Occurs when no join condition exists.

---

## Example

```sql
SELECT *

FROM employees,

departments;
```

---

## Result

```
100 Employees

×

20 Departments

=

2000 Rows
```

---

## Interview Answer

> A Cartesian Product combines every row from one table with every row from another and is usually caused by missing join conditions.

---

# 69. Star Schema Join

## Architecture

```text
          Product

             │

Customer — Fact Sales — Date

             │

          Store
```

---

## Characteristics

- One fact table
- Multiple dimension tables

---

## Interview Answer

> A Star Schema joins a central fact table with multiple denormalized dimension tables for efficient analytical queries.

---

# 70. Snowflake Schema

## Architecture

```text
Product

↓

Category

↓

Department
```

---

## Characteristics

- Normalized dimensions
- More joins
- Less redundancy

---

## Interview Answer

> A Snowflake Schema normalizes dimension tables, reducing redundancy but increasing the number of joins.

---

# 71. Fact Table

Contains measurable business events.

---

## Example

```
Sales

Amount

Quantity

Discount
```

---

## Interview Answer

> A Fact Table stores measurable business metrics and references related dimension tables.

---

# 72. Dimension Table

Contains descriptive attributes.

---

## Example

```
Customer

Product

Store

Date
```

---

## Interview Answer

> Dimension Tables store descriptive business information used for filtering, grouping, and reporting.

---

# 73. Join Optimization

## Techniques

- Add indexes
- Reduce returned columns
- Filter early
- Join smaller datasets first
- Avoid unnecessary joins
- Use proper join type

---

## Interview Answer

> Join optimization focuses on reducing data processed by using indexes, filtering early, minimizing selected columns, and choosing efficient join strategies.

---

# 74. Explain Join Cost

The optimizer estimates:

```
Disk Reads

+

CPU

+

Memory

+

Sorting

+

Network
```

---

## Lower Cost Wins

Example

```
Nested Loop

Cost 15

Hash Join

Cost 8

Optimizer chooses Hash Join
```

---

## Interview Answer

> Join cost is the estimated resource consumption of a join operation, including I/O, CPU, memory, and sorting overhead.

---

# 75. Explain Hash Build

During a Hash Join:

```
Small Table

↓

Hash Function

↓

Hash Buckets
```

---

## Probe

```
Large Table

↓

Lookup

↓

Match
```

---

## Interview Answer

> The build phase of a Hash Join creates an in-memory hash table from the smaller input so the larger input can efficiently probe for matches.

---

# 76. Explain Join Buffer

Some databases allocate memory to hold rows during joins.

Without indexes

```
Disk Reads

↓

Many Lookups
```

With join buffer

```
Memory

↓

Faster Processing
```

---

## Interview Answer

> A join buffer stores intermediate rows in memory to reduce repeated disk access during join processing, especially when indexes are unavailable.

---

# 77. Why Joins Become Slow?

Common reasons:

- Missing indexes
- Large tables
- Poor join order
- Selecting unnecessary columns
- Functions on indexed columns
- Outdated statistics
- Cartesian products
- Data skew

---

## Interview Answer

> Joins become slow due to missing indexes, inefficient execution plans, large intermediate datasets, and poor query design.

---

# 78. Optimizing Large Joins

## Best Practices

- Index join columns
- Filter before joining
- Partition tables
- Materialized views
- Read replicas
- Denormalization when appropriate
- Use EXPLAIN
- Analyze execution plans

---

## Interview Answer

> Large joins are optimized by indexing join keys, reducing scanned rows, partitioning large tables, and validating execution plans.

---

# 79. Indexes for Joins

Indexes should exist on:

```
Foreign Keys

Join Columns

Frequently Filtered Columns
```

Example

```sql
CREATE INDEX idx_orders_customer

ON orders(customer_id);
```

---

## Interview Answer

> Indexes on join columns significantly reduce lookup time and improve join performance, especially for large tables.

---

# 80. Practical Join Problems

## Common Interview Questions

### Find Employees Without Departments

```sql
SELECT *

FROM employees e

LEFT JOIN departments d

ON e.department_id=d.id

WHERE d.id IS NULL;
```

---

### Find Customers Without Orders

```sql
SELECT *

FROM customers c

WHERE NOT EXISTS
(
    SELECT 1

    FROM orders o

    WHERE o.customer_id=c.id
);
```

---

### Employee and Manager

```sql
SELECT e.name,

       m.name

FROM employees e

LEFT JOIN employees m

ON e.manager_id=m.id;
```

---

### Top Selling Products

```sql
SELECT p.product_name,

       SUM(o.quantity)

FROM products p

JOIN orders o

ON p.id=o.product_id

GROUP BY p.product_name;
```

---

## Interview Answer

> Practical join problems assess understanding of relationships, NULL handling, aggregation, and choosing appropriate join types for real-world business scenarios.

---

# Module Summary

- Joins combine related data across tables, with `INNER`, `LEFT`, `RIGHT`, `FULL OUTER`, `CROSS`, `SELF`, and `NATURAL` joins serving different purposes.
- Semi Joins and Anti Joins are commonly implemented using `EXISTS` and `NOT EXISTS`.
- The database optimizer selects between Nested Loop, Merge, and Hash Join algorithms based on table statistics, indexes, sorting, and estimated costs.
- Join performance depends heavily on execution plans, join order, indexing, filtering early, and minimizing intermediate result sets.
- Star Schemas and Snowflake Schemas are foundational data warehouse designs using fact and dimension tables.
- Understanding join costs, hash build phases, join buffers, and execution plans is essential for diagnosing slow joins.
- Efficient join optimization requires proper indexing, selective queries, updated statistics, and careful query design to avoid unnecessary scans and Cartesian products.