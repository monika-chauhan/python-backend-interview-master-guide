# SQL Interview Master Guide

# Module 4: Aggregation & Window Functions (Questions 81–110)

---

# 81. COUNT()

## Definition

`COUNT()` returns the number of rows.

---

## Syntax

```sql
SELECT COUNT(*)
FROM employees;
```

Count Non-NULL values

```sql
SELECT COUNT(email)
FROM employees;
```

Count Distinct

```sql
SELECT COUNT(DISTINCT department)
FROM employees;
```

---

## Interview Answer

> `COUNT()` returns the number of rows or non-NULL values. `COUNT(*)` counts all rows, while `COUNT(column)` ignores NULL values.

---

# 82. SUM()

## Definition

Calculates the total of a numeric column.

---

## Example

```sql
SELECT SUM(salary)
FROM employees;
```

Grouped Sum

```sql
SELECT department,
       SUM(salary)
FROM employees
GROUP BY department;
```

---

## Interview Answer

> `SUM()` calculates the total value of a numeric column and is commonly used with `GROUP BY`.

---

# 83. AVG()

## Definition

Returns the arithmetic average.

---

## Example

```sql
SELECT AVG(salary)
FROM employees;
```

---

## Interview Answer

> `AVG()` calculates the average of non-NULL numeric values.

---

# 84. MIN()

Returns the smallest value.

```sql
SELECT MIN(salary)
FROM employees;
```

---

## Interview Answer

> `MIN()` returns the minimum value in a column.

---

# 85. MAX()

Returns the largest value.

```sql
SELECT MAX(salary)
FROM employees;
```

---

## Interview Answer

> `MAX()` returns the highest value in a column.

---

# 86. GROUPING SETS

## Definition

Calculates multiple GROUP BY combinations in one query.

---

## Example

```sql
SELECT department,
       city,
       SUM(salary)
FROM employees
GROUP BY GROUPING SETS
(
    (department),
    (city),
    ()
);
```

---

## Advantages

- One query
- Better performance
- Multiple reports together

---

## Interview Answer

> `GROUPING SETS` allows multiple grouping combinations in a single aggregation query.

---

# 87. ROLLUP

## Definition

Generates hierarchical subtotals.

---

## Example

```sql
SELECT department,
       city,
       SUM(salary)
FROM employees
GROUP BY ROLLUP
(
    department,
    city
);
```

---

## Output

```text
Department → City → Grand Total
```

---

## Interview Answer

> `ROLLUP` creates hierarchical subtotals followed by a grand total.

---

# 88. CUBE

## Definition

Produces every possible grouping combination.

---

## Example

```sql
GROUP BY CUBE
(
    department,
    city
);
```

---

## Output

```text
Department

City

Department+City

Grand Total
```

---

## Interview Answer

> `CUBE` computes aggregates for every possible grouping combination.

---

# 89. Window Functions

## Definition

Window functions calculate values across related rows **without collapsing them**.

---

## Syntax

```sql
function()

OVER(
    PARTITION BY ...

    ORDER BY ...
)
```

---

## Difference

| GROUP BY | Window Function |
|-----------|-----------------|
| Reduces rows | Keeps rows |
| Aggregate result | Row-wise result |

---

## Interview Answer

> Window functions perform calculations across a window of rows while preserving individual rows in the result set.

---

# 90. ROW_NUMBER()

Assigns a unique sequential number.

---

## Example

```sql
SELECT name,
       ROW_NUMBER()
       OVER(
           ORDER BY salary DESC
       ) AS rn
FROM employees;
```

---

## Interview Answer

> `ROW_NUMBER()` assigns a unique sequence number to each row within a window.

---

# 91. RANK()

Rows with equal values receive the same rank.

---

## Example

```sql
RANK()

OVER(
ORDER BY salary DESC
)
```

---

## Result

```
100 → Rank 1

90 → Rank 2

90 → Rank 2

80 → Rank 4
```

---

## Interview Answer

> `RANK()` assigns equal ranks to tied rows while leaving gaps in subsequent rankings.

---

# 92. DENSE_RANK()

Similar to RANK but without gaps.

---

## Example

```
100 → 1

90 → 2

90 → 2

80 → 3
```

---

## Interview Answer

> `DENSE_RANK()` assigns equal ranks to ties without skipping rank values.

---

# 93. NTILE()

Divides rows into equal buckets.

---

## Example

```sql
SELECT name,

NTILE(4)

OVER(
ORDER BY salary
)

FROM employees;
```

---

## Use Cases

- Quartiles
- Percentiles
- Data segmentation

---

## Interview Answer

> `NTILE()` divides rows into approximately equal groups based on the specified number of buckets.

---

# 94. LAG()

Returns the previous row.

---

## Example

```sql
SELECT sales,

LAG(sales)

OVER(
ORDER BY month
)

FROM revenue;
```

---

## Interview Answer

> `LAG()` retrieves a value from a previous row within the same window.

---

# 95. LEAD()

Returns the next row.

---

## Example

```sql
SELECT sales,

LEAD(sales)

OVER(
ORDER BY month
)

FROM revenue;
```

---

## Interview Answer

> `LEAD()` retrieves a value from a following row within the same window.

---

# 96. FIRST_VALUE()

Returns the first row in the window.

---

## Example

```sql
FIRST_VALUE(salary)

OVER(
ORDER BY salary DESC
)
```

---

## Interview Answer

> `FIRST_VALUE()` returns the first value in the ordered window frame.

---

# 97. LAST_VALUE()

Returns the last row in the frame.

---

## Example

```sql
LAST_VALUE(salary)

OVER(
ORDER BY salary
ROWS BETWEEN UNBOUNDED PRECEDING
AND UNBOUNDED FOLLOWING
)
```

---

## Interview Answer

> `LAST_VALUE()` returns the last value in the current window frame.

---

# 98. NTH_VALUE()

Returns the nth value.

---

## Example

```sql
NTH_VALUE(salary,3)

OVER(
ORDER BY salary DESC
)
```

---

## Interview Answer

> `NTH_VALUE()` returns the value from the specified position within the window frame.

---

# 99. Running Total

## Example

```sql
SELECT month,

sales,

SUM(sales)

OVER(
ORDER BY month
)

AS running_total

FROM revenue;
```

---

## Result

```
Jan 100

Feb 250

Mar 400
```

---

## Interview Answer

> Running totals are calculated using cumulative window aggregates ordered by a sequence column.

---

# 100. Moving Average

## Example

```sql
SELECT month,

AVG(sales)

OVER(
ORDER BY month

ROWS BETWEEN 2 PRECEDING
AND CURRENT ROW
)
FROM revenue;
```

---

## Interview Answer

> A moving average computes the average across a sliding window of rows.

---

# 101. Top N Per Group

## Example

```sql
SELECT *

FROM
(
SELECT *,

ROW_NUMBER()

OVER(
PARTITION BY department

ORDER BY salary DESC
)

AS rn

FROM employees

)t

WHERE rn<=3;
```

---

## Interview Answer

> Window functions combined with `ROW_NUMBER()` are the preferred solution for Top-N-per-group queries.

---

# 102. Percentile

## PostgreSQL

```sql
SELECT percentile_cont(0.5)

WITHIN GROUP(
ORDER BY salary
)

FROM employees;
```

---

## Interview Answer

> Percentiles indicate the value below which a specified percentage of observations fall.

---

# 103. Median

## PostgreSQL

```sql
SELECT percentile_cont(0.5)

WITHIN GROUP(
ORDER BY salary
)

FROM employees;
```

---

## Interview Answer

> The median represents the middle value of an ordered dataset and is commonly calculated using percentile functions.

---

# 104. PARTITION BY

## Purpose

Divides rows into independent groups.

---

## Example

```sql
ROW_NUMBER()

OVER(

PARTITION BY department

ORDER BY salary DESC
)
```

---

## Interview Answer

> `PARTITION BY` splits data into independent partitions before applying window functions.

---

# 105. Frame Clause

Defines which rows belong to the current window.

---

## Example

```sql
ROWS BETWEEN

2 PRECEDING

AND CURRENT ROW
```

---

## Interview Answer

> A frame clause determines which subset of rows within a partition participates in the window calculation.

---

# 106. RANGE vs ROWS

| ROWS | RANGE |
|------|--------|
| Physical rows | Logical value range |
| Faster | Can include ties |

---

## Example

```sql
ROWS BETWEEN

2 PRECEDING

AND CURRENT ROW
```

---

## Interview Answer

> `ROWS` operates on physical row positions, while `RANGE` groups rows based on ordered values.

---

# 107. Window Performance

## Optimization

- Index ORDER BY columns
- Index PARTITION BY columns
- Reduce partitions
- Avoid unnecessary sorting
- Use EXPLAIN

---

## Interview Answer

> Window function performance depends largely on sorting and partitioning. Proper indexing and minimizing unnecessary sorts significantly improve execution speed.

---

# 108. Recursive Window Query

Window functions and recursive CTEs can be combined.

---

## Example

```sql
WITH RECURSIVE hierarchy AS
(
...
)

SELECT *,

ROW_NUMBER()

OVER(
ORDER BY id
)

FROM hierarchy;
```

---

## Use Cases

- Organization trees
- Bill of Materials
- Category hierarchy

---

## Interview Answer

> Recursive CTEs generate hierarchical data, while window functions can further rank or aggregate the recursive results.

---

# 109. Weekly Sales Aggregation

## Example

```sql
SELECT DATE_TRUNC(
'week',
sale_date
),

SUM(amount)

FROM sales

GROUP BY
DATE_TRUNC(
'week',
sale_date
);
```

---

## Interview Answer

> Weekly aggregation groups transactions by calendar week using date functions and aggregate functions.

---

# 110. Interview Scenarios

## Scenario 1

Top 5 Highest Paid Employees Per Department

```sql
ROW_NUMBER()

PARTITION BY department
```

---

## Scenario 2

Running Revenue

```sql
SUM()

OVER(
ORDER BY date
)
```

---

## Scenario 3

Month-over-Month Growth

```sql
LAG()
```

---

## Scenario 4

Find Duplicate Records

```sql
COUNT(*)

GROUP BY

HAVING COUNT(*)>1
```

---

## Scenario 5

Second Highest Salary

```sql
DENSE_RANK()
```

---

## Interview Answer

> Product companies frequently assess practical applications of aggregation and window functions, including ranking, running totals, moving averages, duplicate detection, Top-N queries, and time-series analysis.

---

# Module Summary

- Aggregate functions (`COUNT`, `SUM`, `AVG`, `MIN`, `MAX`) summarize groups of rows.
- `GROUPING SETS`, `ROLLUP`, and `CUBE` enable advanced multidimensional reporting in a single query.
- Window functions perform calculations while preserving individual rows, making them ideal for analytics.
- Ranking functions (`ROW_NUMBER`, `RANK`, `DENSE_RANK`, `NTILE`) solve ordering and Top-N problems.
- Navigation functions (`LAG`, `LEAD`, `FIRST_VALUE`, `LAST_VALUE`, `NTH_VALUE`) compare rows within a partition.
- Running totals, moving averages, percentiles, and medians are common analytical queries powered by window functions.
- `PARTITION BY` defines logical groups, while frame clauses (`ROWS` and `RANGE`) determine which rows participate in each calculation.
- Understanding execution costs, indexing, and sorting is essential for optimizing window function performance in production databases.