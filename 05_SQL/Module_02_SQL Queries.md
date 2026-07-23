# SQL Interview Master Guide

# Module 2: SQL Queries (Questions 21–50)

---

# 21. SELECT Execution Order

## Logical Execution Order

Although we write SQL in one order, the database processes it logically in another order.

```sql
SELECT name, COUNT(*)
FROM employees
WHERE salary > 50000
GROUP BY name
HAVING COUNT(*) > 1
ORDER BY name
LIMIT 10;
```

### Execution Order

```text
1. FROM
2. JOIN
3. WHERE
4. GROUP BY
5. HAVING
6. SELECT
7. DISTINCT
8. ORDER BY
9. LIMIT/OFFSET
```

---

## Interview Answer

> SQL is logically executed in the order: FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT.

---

# 22. WHERE vs HAVING

| WHERE | HAVING |
|--------|---------|
| Filters rows | Filters groups |
| Before GROUP BY | After GROUP BY |
| Cannot use aggregates | Can use aggregates |

---

## WHERE Example

```sql
SELECT *
FROM employees
WHERE salary > 50000;
```

---

## HAVING Example

```sql
SELECT department,
       COUNT(*)
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;
```

---

## Interview Answer

> `WHERE` filters individual rows before grouping, while `HAVING` filters grouped results after aggregation.

---

# 23. GROUP BY

## Purpose

Groups rows with the same values for aggregation.

---

## Example

```sql
SELECT department,
       AVG(salary)
FROM employees
GROUP BY department;
```

---

## Common Aggregate Functions

- COUNT()
- SUM()
- AVG()
- MIN()
- MAX()

---

## Interview Answer

> `GROUP BY` groups rows so aggregate functions can calculate values for each group.

---

# 24. ORDER BY

## Purpose

Sorts query results.

---

## Example

```sql
SELECT *
FROM employees
ORDER BY salary DESC;
```

---

## Multiple Columns

```sql
ORDER BY department ASC,
         salary DESC;
```

---

## Interview Answer

> `ORDER BY` sorts query results in ascending or descending order using one or more columns.

---

# 25. LIMIT vs OFFSET

## LIMIT

Restricts returned rows.

```sql
SELECT *
FROM employees
LIMIT 10;
```

---

## OFFSET

Skips rows.

```sql
SELECT *
FROM employees
LIMIT 10 OFFSET 20;
```

---

## Pagination

```sql
Page 1

LIMIT 10 OFFSET 0

Page 2

LIMIT 10 OFFSET 10
```

---

## Drawback

Large OFFSET values become slower because skipped rows still need processing.

---

## Interview Answer

> `LIMIT` restricts the number of returned rows, while `OFFSET` skips a specified number of rows and is commonly used for pagination.

---

# 26. DISTINCT

## Purpose

Removes duplicate rows.

---

## Example

```sql
SELECT DISTINCT department
FROM employees;
```

---

## Multiple Columns

```sql
SELECT DISTINCT department,
                city
FROM employees;
```

---

## Interview Answer

> `DISTINCT` eliminates duplicate records from the result set.

---

# 27. UNION

## Purpose

Combines results and removes duplicates.

---

## Example

```sql
SELECT name FROM employees

UNION

SELECT name FROM contractors;
```

---

## Rules

- Same number of columns
- Compatible data types

---

## Interview Answer

> `UNION` combines result sets while removing duplicate rows.

---

# 28. UNION ALL

## Difference

Keeps duplicates.

---

## Example

```sql
SELECT name
FROM employees

UNION ALL

SELECT name
FROM contractors;
```

---

## Performance

Faster than UNION because no duplicate elimination occurs.

---

## Interview Answer

> `UNION ALL` combines result sets without removing duplicates, making it more efficient than `UNION`.

---

# 29. INTERSECT

## Purpose

Returns common rows.

---

## Example

```sql
SELECT name
FROM employees

INTERSECT

SELECT name
FROM managers;
```

---

## Interview Answer

> `INTERSECT` returns only rows present in both result sets.

---

# 30. EXCEPT

## Purpose

Returns rows from the first query that are absent in the second.

---

## Example

```sql
SELECT name
FROM employees

EXCEPT

SELECT name
FROM managers;
```

---

## Interview Answer

> `EXCEPT` returns rows from the first query that do not appear in the second.

---

# 31. CASE WHEN

## Purpose

Conditional logic in SQL.

---

## Example

```sql
SELECT name,
       CASE
           WHEN salary > 100000 THEN 'High'
           WHEN salary > 50000 THEN 'Medium'
           ELSE 'Low'
       END AS salary_band
FROM employees;
```

---

## Interview Answer

> `CASE` provides conditional expressions within SQL statements, similar to `if-else` logic in programming languages.

---

# 32. COALESCE

## Purpose

Returns the first non-NULL value.

---

## Example

```sql
SELECT COALESCE(phone,
                mobile,
                'Not Available')
FROM users;
```

---

## Interview Answer

> `COALESCE` returns the first non-NULL expression from its argument list.

---

# 33. NULLIF

## Purpose

Returns NULL if two expressions are equal.

---

## Example

```sql
SELECT NULLIF(10,10);
```

Returns:

```
NULL
```

---

## Common Use

Avoid divide-by-zero.

```sql
SELECT salary / NULLIF(hours,0)
FROM employees;
```

---

## Interview Answer

> `NULLIF` returns NULL when two expressions are equal, making it useful for preventing divide-by-zero errors.

---

# 34. IFNULL vs COALESCE

| IFNULL | COALESCE |
|---------|----------|
| MySQL | ANSI SQL |
| Two arguments | Multiple arguments |

---

## Example

```sql
IFNULL(phone,'NA')
```

```sql
COALESCE(phone,mobile,'NA')
```

---

## Interview Answer

> `IFNULL` replaces NULL using two arguments, whereas `COALESCE` is ANSI-compliant and supports multiple expressions.

---

# 35. EXISTS

## Purpose

Checks whether a subquery returns at least one row.

---

## Example

```sql
SELECT *
FROM customers c
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.id
);
```

---

## Benefits

Stops after finding the first matching row.

---

## Interview Answer

> `EXISTS` returns TRUE if the subquery produces at least one row and is often more efficient than `IN` for large datasets.

---

# 36. IN

## Purpose

Matches against a list or subquery.

---

## Example

```sql
SELECT *
FROM employees
WHERE department IN (
    'IT',
    'HR'
);
```

---

## Interview Answer

> `IN` checks whether a value exists in a specified list or subquery result.

---

# 37. ANY

## Purpose

Compares a value against any value returned by a subquery.

---

## Example

```sql
SELECT *
FROM employees
WHERE salary > ANY (
    SELECT salary
    FROM managers
);
```

---

## Interview Answer

> `ANY` returns TRUE if the comparison succeeds for at least one value returned by the subquery.

---

# 38. ALL

## Purpose

Comparison against all values.

---

## Example

```sql
SELECT *
FROM employees
WHERE salary > ALL (
    SELECT salary
    FROM interns
);
```

---

## Interview Answer

> `ALL` returns TRUE only if the comparison is satisfied for every value returned by the subquery.

---

# 39. Correlated Subquery

## Definition

Depends on values from the outer query.

---

## Example

```sql
SELECT name
FROM employees e
WHERE salary >
(
    SELECT AVG(salary)
    FROM employees
    WHERE department = e.department
);
```

---

## Interview Answer

> A correlated subquery references columns from the outer query and executes once for each outer row.

---

# 40. Scalar Subquery

## Definition

Returns exactly one value.

---

## Example

```sql
SELECT name,
(
    SELECT MAX(salary)
    FROM employees
)
FROM employees;
```

---

## Interview Answer

> A scalar subquery returns a single value that can be used wherever an expression is allowed.

---

# 41. Derived Table

## Definition

A subquery used in the FROM clause.

---

## Example

```sql
SELECT *
FROM
(
    SELECT department,
           AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
) d;
```

---

## Interview Answer

> A derived table is a temporary result set created by a subquery in the FROM clause.

---

# 42. Common Table Expressions (CTE)

## Syntax

```sql
WITH dept_salary AS
(
    SELECT department,
           AVG(salary) avg_sal
    FROM employees
    GROUP BY department
)
SELECT *
FROM dept_salary;
```

---

## Advantages

- Readability
- Reusability
- Recursive queries

---

## Interview Answer

> A CTE defines a named temporary result set that improves query readability and organization.

---

# 43. Recursive CTE

## Example

```sql
WITH RECURSIVE nums(n) AS
(
    SELECT 1

    UNION ALL

    SELECT n+1
    FROM nums
    WHERE n < 5
)
SELECT *
FROM nums;
```

---

## Use Cases

- Organizational hierarchy
- Tree traversal
- Graph traversal
- Folder structures

---

## Interview Answer

> Recursive CTEs repeatedly reference themselves to process hierarchical or recursive data structures.

---

# 44. LATERAL JOIN

## Definition

Allows a subquery to reference columns from preceding tables.

---

## Example

```sql
SELECT u.name,
       p.*
FROM users u
CROSS JOIN LATERAL
(
    SELECT *
    FROM posts
    WHERE posts.user_id=u.id
    LIMIT 1
) p;
```

---

## Interview Answer

> A LATERAL JOIN allows subqueries in the FROM clause to reference columns from earlier tables in the same query.

---

# 45. VALUES Clause

## Purpose

Creates inline rows.

---

## Example

```sql
VALUES

(1,'Alice'),

(2,'Bob');
```

---

## Insert

```sql
INSERT INTO users(id,name)

VALUES

(1,'Alice'),

(2,'Bob');
```

---

## Interview Answer

> The `VALUES` clause provides literal row values for INSERT statements or inline result sets.

---

# 46. generate_series()

(PostgreSQL)

## Example

```sql
SELECT *
FROM generate_series(
    1,
    10
);
```

---

## Dates

```sql
SELECT *
FROM generate_series(
    '2024-01-01'::date,
    '2024-01-10',
    '1 day'
);
```

---

## Use Cases

- Calendar generation
- Test data
- Reporting

---

## Interview Answer

> `generate_series()` is a PostgreSQL set-returning function used to generate sequences of numbers, dates, or timestamps.

---

# 47. CROSS JOIN

## Definition

Returns the Cartesian product of two tables.

---

## Example

```sql
SELECT *

FROM colors

CROSS JOIN sizes;
```

---

## Result

```
3 colors

×

4 sizes

=

12 rows
```

---

## Interview Answer

> `CROSS JOIN` combines every row from one table with every row from another table.

---

# 48. SELF JOIN

## Purpose

Joins a table with itself.

---

## Example

```sql
SELECT e.name,
       m.name
FROM employees e

LEFT JOIN employees m

ON e.manager_id=m.id;
```

---

## Use Cases

- Employee-manager
- Categories
- Hierarchies

---

## Interview Answer

> A SELF JOIN relates rows within the same table, commonly used for hierarchical relationships.

---

# 49. CROSS APPLY

(SQL Server)

## Purpose

Executes a table-valued function for each row.

---

## Example

```sql
SELECT *

FROM users

CROSS APPLY get_orders(users.id);
```

---

## PostgreSQL Equivalent

```
LATERAL JOIN
```

---

## Interview Answer

> `CROSS APPLY` invokes a table-valued function for each row of the outer query and returns matching results.

---

# 50. OUTER APPLY

(SQL Server)

## Difference

Returns rows even when the applied function returns no results.

---

## Similar To

```
LEFT JOIN

+

CROSS APPLY
```

---

## Example

```sql
SELECT *

FROM users

OUTER APPLY get_orders(users.id);
```

---

## PostgreSQL Equivalent

```
LEFT JOIN LATERAL
```

---

## Interview Answer

> `OUTER APPLY` behaves like a LEFT JOIN for table-valued functions, returning outer rows even when the applied query produces no matches.

---

# Module Summary

- SQL follows a logical execution order starting with `FROM` and ending with `LIMIT`.
- `WHERE` filters rows before grouping, while `HAVING` filters grouped results.
- `GROUP BY`, `ORDER BY`, `DISTINCT`, `LIMIT`, and `OFFSET` are fundamental query-building constructs.
- Set operators (`UNION`, `UNION ALL`, `INTERSECT`, `EXCEPT`) combine or compare result sets.
- Conditional functions such as `CASE`, `COALESCE`, and `NULLIF` simplify business logic within SQL.
- Subqueries include scalar, correlated, and derived-table patterns, while CTEs improve readability and support recursion.
- Advanced features such as `LATERAL JOIN`, `generate_series()`, `CROSS JOIN`, and `SELF JOIN` solve specialized querying problems.
- SQL Server's `CROSS APPLY` and `OUTER APPLY` correspond closely to PostgreSQL's `LATERAL JOIN` capabilities.