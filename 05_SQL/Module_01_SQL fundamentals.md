# SQL Interview Master Guide

# Module 1: SQL Fundamentals (Questions 1–20)

---

# 1. What is SQL?

## Definition

**SQL (Structured Query Language)** is the standard language used to communicate with Relational Database Management Systems (RDBMS). It allows users to create, retrieve, update, delete, and manage data stored in relational databases.

Popular RDBMS:
- PostgreSQL
- MySQL
- Oracle
- SQL Server
- SQLite

---

## Basic Example

```sql
SELECT * FROM employees;
```

---

## SQL Capabilities

- Create databases and tables
- Insert data
- Update records
- Delete records
- Query data
- Manage users and permissions
- Control transactions

---

## Interview Answer

> SQL is the standard language used to interact with relational databases. It supports data definition, manipulation, querying, transaction management, and access control.

---

# 2. SQL vs NoSQL

| SQL | NoSQL |
|------|--------|
| Relational | Non-relational |
| Tables | Documents, Key-Value, Graph, Column |
| Fixed Schema | Flexible Schema |
| ACID | BASE (typically) |
| Vertical Scaling | Horizontal Scaling |
| Complex JOINs | Limited JOIN support |

---

## SQL Examples

- PostgreSQL
- MySQL
- Oracle
- SQL Server

## NoSQL Examples

- MongoDB
- Cassandra
- DynamoDB
- Redis

---

## When to Use

### SQL

- Banking
- ERP
- E-commerce
- Financial systems

### NoSQL

- Chat applications
- Logging
- Caching
- IoT
- Social media feeds

---

## Interview Answer

> SQL databases use structured schemas and support ACID transactions, making them ideal for transactional applications. NoSQL databases provide flexible schemas and horizontal scalability, making them suitable for large-scale distributed workloads.

---

# 3. Types of SQL Commands (DDL, DML, DCL, TCL, DQL)

| Category | Purpose | Examples |
|----------|---------|----------|
| DDL | Define database structure | CREATE, ALTER, DROP, TRUNCATE |
| DML | Modify data | INSERT, UPDATE, DELETE |
| DQL | Retrieve data | SELECT |
| DCL | Access control | GRANT, REVOKE |
| TCL | Transaction control | COMMIT, ROLLBACK, SAVEPOINT |

---

## Example

```sql
CREATE TABLE users (...);   -- DDL

INSERT INTO users VALUES (...); -- DML

SELECT * FROM users; -- DQL

GRANT SELECT ON users TO app_user; -- DCL

COMMIT; -- TCL
```

---

## Interview Answer

> SQL commands are categorized into DDL for schema management, DML for data modification, DQL for querying, DCL for permissions, and TCL for transaction management.

---

# 4. Primary Key vs Unique Key

| Primary Key | Unique Key |
|--------------|------------|
| Only one per table | Multiple allowed |
| Cannot be NULL | Can contain NULL (DB-dependent) |
| Identifies each row | Enforces uniqueness |

---

## Example

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(255) UNIQUE
);
```

---

## Interview Answer

> A Primary Key uniquely identifies each row and cannot be NULL, whereas a Unique Key enforces uniqueness but is not necessarily the table's primary identifier.

---

# 5. Candidate Key vs Alternate Key

## Candidate Key

A column (or combination of columns) capable of uniquely identifying rows.

Example:

```
Employee ID

Email

Passport Number
```

All are candidate keys.

---

## Alternate Key

Candidate keys not chosen as the Primary Key.

---

## Interview Answer

> Candidate Keys are all possible unique identifiers for a table, while Alternate Keys are candidate keys that were not selected as the Primary Key.

---

# 6. Foreign Key

## Definition

A Foreign Key establishes a relationship between two tables.

---

## Example

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id)
    REFERENCES customers(id)
);
```

---

## Benefits

- Referential integrity
- Prevent orphan records
- Maintain relationships

---

## Interview Answer

> A Foreign Key enforces referential integrity by ensuring values reference valid rows in another table.

---

# 7. Composite Key

## Definition

A Primary Key consisting of multiple columns.

---

## Example

```sql
PRIMARY KEY(student_id, course_id)
```

---

## Use Cases

- Enrollment systems
- Order items
- Mapping tables

---

## Interview Answer

> A Composite Key combines multiple columns to uniquely identify each row when a single column is insufficient.

---

# 8. Surrogate Key vs Natural Key

| Surrogate Key | Natural Key |
|---------------|-------------|
| Artificial | Business data |
| Auto-generated | Existing value |
| Stable | Can change |

---

## Example

```text
Surrogate: User ID = 1001

Natural: Email = john@example.com
```

---

## Interview Answer

> Surrogate Keys are system-generated identifiers, while Natural Keys originate from business data such as email addresses or passport numbers.

---

# 9. NULL vs Empty String

| NULL | Empty String |
|------|--------------|
| Unknown value | Zero-length text |
| Not equal to anything | Actual value |
| Checked using IS NULL | Compared using = '' |

---

## Example

```sql
SELECT *
FROM users
WHERE phone IS NULL;
```

---

## Interview Answer

> NULL represents missing or unknown data, whereas an empty string is a valid string containing zero characters.

---

# 10. Constraints in SQL

## Types

- PRIMARY KEY
- FOREIGN KEY
- UNIQUE
- CHECK
- DEFAULT
- NOT NULL

---

## Example

```sql
CREATE TABLE users (

id INT PRIMARY KEY,

email VARCHAR(255) UNIQUE,

age INT CHECK(age >= 18),

country VARCHAR(50) DEFAULT 'India'
);
```

---

## Interview Answer

> Constraints enforce data integrity by restricting what data can be stored in database tables.

---

# 11. CHECK Constraint

## Definition

Restricts values according to a condition.

---

## Example

```sql
CHECK (salary > 0)
```

---

## Interview Answer

> CHECK constraints ensure inserted or updated data satisfies specified logical conditions.

---

# 12. DEFAULT Constraint

## Definition

Automatically inserts a default value when none is provided.

---

## Example

```sql
status VARCHAR(20)
DEFAULT 'ACTIVE'
```

---

## Interview Answer

> DEFAULT assigns predefined values when no explicit value is supplied during insertion.

---

# 13. AUTO_INCREMENT vs SERIAL

| AUTO_INCREMENT | SERIAL |
|---------------|---------|
| MySQL | PostgreSQL |
| Generates sequential IDs | Uses underlying sequence |

---

## MySQL

```sql
id INT AUTO_INCREMENT
```

---

## PostgreSQL

```sql
id SERIAL
```

---

## Interview Answer

> AUTO_INCREMENT is MySQL's mechanism for generating sequential values, whereas PostgreSQL traditionally uses SERIAL or IDENTITY columns backed by sequences.

---

# 14. TRUNCATE vs DELETE vs DROP

| DELETE | TRUNCATE | DROP |
|----------|-----------|------|
| Removes rows | Removes all rows | Removes table |
| Can use WHERE | No WHERE | Deletes structure |
| Can rollback (within transaction support) | DB-dependent | Removes metadata |

---

## Example

```sql
DELETE FROM users WHERE id=1;

TRUNCATE TABLE users;

DROP TABLE users;
```

---

## Interview Answer

> DELETE removes selected rows, TRUNCATE quickly removes all rows while keeping the table, and DROP permanently removes the table definition and its data.

---

# 15. CREATE vs ALTER

## CREATE

Creates a new database object.

```sql
CREATE TABLE users (...);
```

---

## ALTER

Modifies an existing object.

```sql
ALTER TABLE users
ADD phone VARCHAR(20);
```

---

## Interview Answer

> CREATE defines new database objects, whereas ALTER modifies existing objects without recreating them.

---

# 16. INSERT vs UPSERT

## INSERT

```sql
INSERT INTO users(id,name)
VALUES(1,'John');
```

Fails if the key already exists.

---

## UPSERT (PostgreSQL)

```sql
INSERT INTO users(id,name)
VALUES(1,'John')
ON CONFLICT(id)
DO UPDATE
SET name=EXCLUDED.name;
```

---

## Interview Answer

> INSERT only adds new rows, while UPSERT inserts or updates depending on whether a conflict occurs.

---

# 17. UPDATE with JOIN

## PostgreSQL

```sql
UPDATE employees e

SET department_name = d.name

FROM departments d

WHERE e.department_id = d.id;
```

---

## Use Cases

- Synchronizing data
- Bulk updates
- Denormalized tables

---

## Interview Answer

> UPDATE with JOIN updates rows in one table using matching data from another table, reducing the need for multiple statements.

---

# 18. DELETE with JOIN

## PostgreSQL

```sql
DELETE FROM orders o

USING customers c

WHERE o.customer_id = c.id

AND c.status='INACTIVE';
```

---

## Use Cases

- Delete orphan records
- Cleanup inactive users
- Cascading cleanup

---

## Interview Answer

> DELETE with JOIN removes rows based on matching records in related tables.

---

# 19. What is SQL Injection?

## Definition

SQL Injection is a security vulnerability where malicious SQL is injected through user input.

---

## Vulnerable Example

```python
query = f"""
SELECT *
FROM users
WHERE username='{username}'
"""
```

Input:

```text
' OR 1=1 --
```

Generated SQL

```sql
SELECT *
FROM users
WHERE username='' OR 1=1;
```

Returns every user.

---

## Impact

- Data theft
- Authentication bypass
- Data deletion
- Privilege escalation

---

## Interview Answer

> SQL Injection occurs when untrusted user input is concatenated into SQL statements, allowing attackers to execute arbitrary SQL commands.

---

# 20. How to Prevent SQL Injection?

## 1. Parameterized Queries

```python
cursor.execute(
    "SELECT * FROM users WHERE id=%s",
    [user_id]
)
```

---

## 2. ORM

```python
User.objects.filter(id=user_id)
```

---

## 3. Input Validation

Validate user input before processing.

---

## 4. Least Privilege

Grant only necessary database permissions.

---

## 5. Avoid Dynamic SQL

Never concatenate raw user input into SQL strings.

---

## Interview Answer

> SQL Injection is prevented by using parameterized queries or ORMs, validating inputs, avoiding dynamic SQL construction, and applying the principle of least privilege to database accounts.

---

# Module Summary

- SQL is the standard language for relational databases, supporting schema management, querying, transactions, and security.
- SQL commands are categorized into DDL, DML, DQL, DCL, and TCL based on their purpose.
- Primary, Unique, Candidate, Alternate, Composite, Surrogate, and Foreign Keys are fundamental concepts for data integrity and relationships.
- Constraints such as NOT NULL, CHECK, DEFAULT, UNIQUE, and FOREIGN KEY ensure valid and consistent data.
- Understanding the differences between DELETE, TRUNCATE, DROP, CREATE, ALTER, INSERT, and UPSERT is essential for database operations.
- UPDATE and DELETE can leverage JOINs to efficiently modify related data.
- SQL Injection is one of the most critical database security vulnerabilities and should be prevented using parameterized queries, ORM abstractions, input validation, and proper permission management.