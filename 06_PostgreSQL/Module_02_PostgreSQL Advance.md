# SQL Interview Master Guide

# Module 9: Advanced PostgreSQL (Questions 251–280)

---

# 251. JSON vs JSONB

## JSON

- Stores exact JSON text
- Preserves formatting and key order
- Parsed every time it's queried
- Slower queries
- Faster inserts

Example:

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    profile JSON
);
```

---

## JSONB

- Binary representation
- Removes whitespace
- Does not preserve key order
- Supports indexing (GIN)
- Faster querying

Example:

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    profile JSONB
);
```

---

## Comparison

| JSON | JSONB |
|-------|--------|
| Text | Binary |
| Preserves formatting | No |
| Slower search | Faster search |
| No indexing | Supports GIN indexes |
| Faster insert | Slightly slower insert |

---

## Interview Answer

> JSON stores raw JSON text, whereas JSONB stores a decomposed binary format optimized for querying and indexing. In production, JSONB is generally preferred.

---

# 252. Full Text Search

## Definition

Allows searching natural language documents efficiently.

---

## Example

```sql
SELECT *
FROM articles
WHERE to_tsvector(content)
@@ to_tsquery('postgres');
```

---

## Features

- Language-aware
- Stemming
- Ranking
- Stop-word removal

---

## Index

```sql
CREATE INDEX idx_search
ON articles
USING GIN(to_tsvector('english', content));
```

---

## Interview Answer

> PostgreSQL Full Text Search provides fast, language-aware text searching using tsvector, tsquery, and GIN indexes.

---

# 253. PostGIS

## Definition

Extension for Geographic Information Systems (GIS).

---

## Features

- Distance calculation
- Maps
- Geospatial indexing
- Polygon search

---

## Example

```sql
SELECT *
FROM restaurants
WHERE ST_DWithin(
    location,
    ST_Point(77.59,12.97),
    1000
);
```

---

## Interview Answer

> PostGIS extends PostgreSQL with geospatial data types and functions for location-based applications.

---

# 254. Arrays

## Definition

PostgreSQL supports array columns.

---

## Example

```sql
CREATE TABLE products (
    tags TEXT[]
);
```

Insert

```sql
INSERT INTO products
VALUES ('{electronics,mobile}');
```

Search

```sql
SELECT *
FROM products
WHERE 'mobile' = ANY(tags);
```

---

## Interview Answer

> PostgreSQL arrays allow storing multiple values in a single column and support specialized operators and indexing.

---

# 255. Composite Types

## Definition

Custom structured data types.

---

## Example

```sql
CREATE TYPE address AS (
    city TEXT,
    state TEXT
);
```

---

## Interview Answer

> Composite types group multiple related fields into a reusable structured type.

---

# 256. ENUM

## Definition

Stores predefined values.

---

## Example

```sql
CREATE TYPE status AS ENUM (
'NEW',
'PAID',
'SHIPPED'
);
```

---

## Advantages

- Faster
- Type safety
- Validation

---

## Interview Answer

> ENUM types restrict values to a predefined set, improving data integrity and readability.

---

# 257. UUID

## Definition

Universally Unique Identifier.

---

## Example

```sql
CREATE EXTENSION
IF NOT EXISTS "uuid-ossp";

SELECT uuid_generate_v4();
```

---

## Advantages

- Globally unique
- Safe for distributed systems

---

## Interview Answer

> UUIDs provide globally unique identifiers that avoid key collisions across distributed systems.

---

# 258. Triggers

## Definition

Automatically execute code when table events occur.

---

## Example

```sql
CREATE TRIGGER log_update
AFTER UPDATE
ON employees
FOR EACH ROW
EXECUTE FUNCTION log_changes();
```

---

## Interview Answer

> Triggers automatically execute predefined logic in response to INSERT, UPDATE, DELETE, or TRUNCATE events.

---

# 259. Stored Procedures

## Definition

Database routines that can manage transactions.

---

## Example

```sql
CALL transfer_money();
```

---

## Interview Answer

> Stored procedures encapsulate business logic inside the database and can explicitly control transactions.

---

# 260. Functions

## Definition

Reusable SQL or PL/pgSQL code returning a value.

---

## Example

```sql
CREATE FUNCTION tax(amount NUMERIC)
RETURNS NUMERIC
AS $$
BEGIN
    RETURN amount * 0.18;
END;
$$ LANGUAGE plpgsql;
```

---

## Interview Answer

> Functions encapsulate reusable logic and return computed values that can be used inside SQL queries.

---

# 261. Materialized View

## Definition

Stores query results physically.

---

## Example

```sql
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT month,
       SUM(amount)
FROM sales
GROUP BY month;
```

Refresh

```sql
REFRESH MATERIALIZED VIEW monthly_sales;
```

---

## Interview Answer

> Materialized views improve performance by storing precomputed query results that can be refreshed periodically.

---

# 262. Generated Columns

## Definition

Automatically computed columns.

---

## Example

```sql
CREATE TABLE products(
price NUMERIC,
tax NUMERIC GENERATED ALWAYS AS
(price*0.18) STORED
);
```

---

## Interview Answer

> Generated columns automatically derive values from other columns, eliminating redundant calculations.

---

# 263. LISTEN / NOTIFY

## Purpose

Database pub-sub messaging.

---

## Example

Listener

```sql
LISTEN orders;
```

Publisher

```sql
NOTIFY orders,
'New Order';
```

---

## Use Cases

- Event-driven apps
- Cache invalidation
- Notifications

---

## Interview Answer

> LISTEN/NOTIFY provides lightweight asynchronous messaging between PostgreSQL sessions.

---

# 264. Logical Decoding

## Definition

Converts WAL into readable change events.

---

## Uses

- CDC (Change Data Capture)
- Kafka
- Debezium

---

## Interview Answer

> Logical decoding extracts row-level changes from WAL for replication and streaming data pipelines.

---

# 265. Foreign Data Wrapper (FDW)

## Definition

Query external databases like local tables.

---

## Example

```sql
CREATE EXTENSION postgres_fdw;
```

---

## Sources

- PostgreSQL
- MySQL
- Oracle
- CSV

---

## Interview Answer

> Foreign Data Wrappers allow PostgreSQL to access external data sources using SQL.

---

# 266. Table Partitioning

## Definition

Splits large tables into smaller partitions.

---

## Benefits

- Faster queries
- Easier maintenance
- Better scalability

---

## Interview Answer

> Partitioning improves performance by dividing very large tables into manageable pieces.

---

# 267. Range Partition

## Example

```sql
PARTITION BY RANGE(order_date);
```

Partitions

```
2024

2025

2026
```

---

## Best For

- Time-series data
- Logs
- Orders

---

## Interview Answer

> Range partitioning organizes data based on continuous value ranges such as dates or numbers.

---

# 268. Hash Partition

## Definition

Uses a hash function.

---

## Example

```sql
PARTITION BY HASH(customer_id);
```

---

## Best For

Even data distribution.

---

## Interview Answer

> Hash partitioning distributes rows evenly across partitions using a hash algorithm.

---

# 269. List Partition

## Definition

Partitions by discrete values.

---

## Example

```sql
PARTITION BY LIST(country);
```

---

## Best For

Country

Region

Status

---

## Interview Answer

> List partitioning groups rows according to predefined values.

---

# 270. Parallel Index Build

## Definition

Creates indexes using multiple CPU cores.

---

## Benefits

- Faster index creation
- Reduced maintenance time

---

## Interview Answer

> Parallel index build speeds up index creation by utilizing multiple worker processes.

---

# 271. Parallel Vacuum

Allows VACUUM to process indexes in parallel.

---

## Benefits

- Faster cleanup
- Better maintenance

---

## Interview Answer

> Parallel VACUUM improves maintenance performance by cleaning multiple indexes concurrently.

---

# 272. TimescaleDB

## Definition

Time-series database extension.

---

## Features

- Hypertables
- Compression
- Continuous aggregates

---

## Use Cases

- IoT
- Monitoring
- Metrics
- Financial data

---

## Interview Answer

> TimescaleDB extends PostgreSQL with optimized storage and querying capabilities for time-series workloads.

---

# 273. Citus

## Definition

Distributed PostgreSQL.

---

## Features

- Horizontal scaling
- Sharding
- Distributed queries

---

## Interview Answer

> Citus enables PostgreSQL to scale horizontally across multiple nodes while preserving SQL capabilities.

---

# 274. pgAudit

## Definition

Audit extension.

---

## Tracks

- SELECT
- INSERT
- UPDATE
- DELETE
- DDL

---

## Interview Answer

> pgAudit provides detailed logging of database activity for compliance and security auditing.

---

# 275. pgcrypto

## Definition

Cryptographic extension.

---

## Example

```sql
SELECT crypt(
'password',
gen_salt('bf')
);
```

---

## Features

- Hashing
- Encryption
- Random UUIDs

---

## Interview Answer

> pgcrypto adds cryptographic functions such as hashing, encryption, and secure random generation.

---

# 276. Row Level Security (RLS)

## Definition

Restricts access at the row level.

---

## Example

```sql
CREATE POLICY employee_policy
ON employees
USING (
department='HR'
);
```

---

## Interview Answer

> Row Level Security enforces access control so users can only view or modify authorized rows.

---

# 277. Column Level Security

## Definition

Restricts access to specific columns.

---

## Example

Hide salary

Allow

```
Name

Department
```

Only HR can view

```
Salary
```

---

## Interview Answer

> Column-level security protects sensitive attributes by granting access to selected columns only.

---

# 278. Data Encryption

## Types

### Encryption at Rest

- Disk encryption
- Cloud-managed encryption

### Encryption in Transit

- SSL/TLS

### Column Encryption

- pgcrypto

---

## Interview Answer

> Production PostgreSQL systems typically use encryption at rest, TLS for data in transit, and column-level encryption for sensitive information.

---

# 279. Backup & Restore

## Backup

```bash
pg_dump mydb > backup.sql
```

Restore

```bash
psql mydb < backup.sql
```

---

## Physical Backup

```bash
pg_basebackup
```

---

## Interview Answer

> PostgreSQL supports logical backups with pg_dump and physical backups with pg_basebackup for disaster recovery.

---

# 280. PITR (Point In Time Recovery)

## Definition

Restore the database to an exact timestamp.

---

## Requirements

- Base backup
- WAL archive

---

## Recovery Flow

```text
Base Backup

↓

Replay WAL

↓

Desired Time
```

---

## Use Cases

- Accidental DELETE
- Corruption
- Disaster recovery

---

## Interview Answer

> Point-in-Time Recovery restores a PostgreSQL database to a specific moment by combining a base backup with archived WAL files.

---

# Module Summary

- PostgreSQL provides advanced data types such as JSONB, Arrays, Composite Types, ENUMs, and UUIDs for flexible schema design.
- Full Text Search and PostGIS extend PostgreSQL for text search and geospatial applications.
- Triggers, Functions, Stored Procedures, Generated Columns, and Materialized Views enable server-side business logic and performance optimization.
- LISTEN/NOTIFY and Logical Decoding support event-driven architectures and Change Data Capture (CDC).
- Foreign Data Wrappers allow querying external data sources as if they were local tables.
- Partitioning strategies (Range, Hash, List) improve scalability and query performance for large datasets.
- Extensions such as TimescaleDB, Citus, pgAudit, and pgcrypto enhance PostgreSQL for time-series workloads, distributed databases, auditing, and encryption.
- Enterprise security relies on Row Level Security, Column Level Security, encryption, auditing, and robust backup strategies.
- Disaster recovery is achieved through logical backups, physical backups, WAL archiving, and Point-in-Time Recovery (PITR).