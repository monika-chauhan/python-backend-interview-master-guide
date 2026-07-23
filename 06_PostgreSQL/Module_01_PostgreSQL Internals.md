# SQL Interview Master Guide

# Module 8: PostgreSQL Internals (Questions 221–250)

---

# 221. PostgreSQL Architecture

## Definition

PostgreSQL follows a **client-server architecture** consisting of multiple background processes, shared memory, and storage components.

---

## Architecture Diagram

```text
                  Client
                     │
              PostgreSQL Server
                     │
       ┌─────────────┴─────────────┐
       │                           │
 Background Processes        Backend Process
       │                           │
       └─────────────┬─────────────┘
                     │
               Shared Memory
                     │
          ┌──────────┴──────────┐
          │                     │
    Shared Buffers         WAL Buffers
                     │
               Data Files
```

---

## Major Components

- Postmaster
- Backend Process
- Shared Buffers
- WAL
- Checkpointer
- Background Writer
- Autovacuum
- WAL Writer

---

## Interview Answer

> PostgreSQL uses a client-server architecture where backend processes execute queries while shared memory and background processes manage caching, durability, and maintenance.

---

# 222. WAL (Write-Ahead Logging)

## Definition

Before modifying data files, PostgreSQL first writes changes to the **Write-Ahead Log (WAL).**

---

## Why?

Crash Recovery

```
Write WAL

↓

Flush WAL

↓

Modify Data Page
```

---

## Benefits

- Crash recovery
- Replication
- Durability

---

## Interview Answer

> WAL guarantees durability by recording changes before data pages are written to disk, enabling crash recovery and replication.

---

# 223. Checkpoints

## Definition

A checkpoint flushes dirty pages from memory to disk.

---

## Process

```
Dirty Buffers

↓

Checkpoint

↓

Disk
```

---

## Benefits

- Faster recovery
- Reduced WAL replay

---

## Drawbacks

Frequent checkpoints increase disk I/O.

---

## Interview Answer

> Checkpoints periodically write modified pages to disk, reducing recovery time after crashes.

---

# 224. Buffer Manager

## Definition

Manages cached database pages in memory.

---

## Flow

```
Query

↓

Shared Buffers

↓

Disk (if needed)
```

---

## Interview Answer

> The Buffer Manager minimizes disk I/O by serving frequently accessed pages from shared memory.

---

# 225. Shared Buffers

## Definition

Primary PostgreSQL cache.

---

## Recommendation

Typically

```
20–40%

of system RAM
```

---

## Interview Answer

> Shared Buffers store frequently accessed data pages, reducing expensive disk reads.

---

# 226. Background Writer

## Purpose

Writes dirty buffers gradually.

---

## Advantages

- Smoother I/O
- Faster checkpoints

---

## Interview Answer

> The Background Writer continuously flushes dirty pages, reducing the workload during checkpoints.

---

# 227. Autovacuum

## Definition

Automatically performs:

- VACUUM
- ANALYZE

---

## Prevents

- Dead tuples
- Table bloat
- XID wraparound

---

## Interview Answer

> Autovacuum automatically reclaims storage, updates statistics, and prevents transaction ID wraparound.

---

# 228. MVCC Internals

## Definition

Each UPDATE creates a new row version.

---

## Example

```
Version 1

↓

Version 2

↓

Version 3
```

Old versions remain until vacuum removes them.

---

## Interview Answer

> PostgreSQL implements MVCC by storing multiple row versions, allowing readers and writers to operate concurrently.

---

# 229. Tuple Versioning

Every row contains hidden metadata:

- xmin
- xmax

---

## Meaning

```
xmin

Transaction that created row

xmax

Transaction that deleted row
```

---

## Interview Answer

> Tuple versioning uses transaction metadata to determine row visibility for each transaction snapshot.

---

# 230. Visibility Map

Tracks pages where all tuples are visible.

---

## Benefits

- Faster VACUUM
- Index Only Scan

---

## Interview Answer

> The Visibility Map identifies pages that are fully visible, allowing PostgreSQL to optimize scans and maintenance.

---

# 231. Free Space Map (FSM)

Tracks free space in pages.

---

## Benefits

New inserts reuse existing pages.

---

## Interview Answer

> The Free Space Map helps PostgreSQL efficiently locate pages with available space for new rows.

---

# 232. TOAST

## Definition

The Oversized-Attribute Storage Technique.

Stores large values outside the main table.

---

## Example

```
Large TEXT

Large JSON

BYTEA
```

---

## Interview Answer

> TOAST automatically stores large column values outside the main table to keep heap pages compact.

---

# 233. Heap Storage

## Definition

Tables are stored as heaps.

Rows have no guaranteed physical order.

---

## Structure

```
Table

↓

Pages

↓

Tuples
```

---

## Interview Answer

> PostgreSQL stores tables as unordered heaps composed of fixed-size pages containing tuples.

---

# 234. Page Layout

Default page size

```
8 KB
```

---

## Structure

```
Header

↓

Line Pointers

↓

Tuple Data

↓

Free Space
```

---

## Interview Answer

> PostgreSQL stores data in 8 KB pages containing headers, tuple pointers, row data, and free space.

---

# 235. Transaction IDs (XIDs)

Every transaction receives a unique ID.

---

## Used For

- MVCC
- Visibility
- Vacuum

---

## Interview Answer

> Transaction IDs uniquely identify transactions and are fundamental to PostgreSQL's MVCC implementation.

---

# 236. XID Wraparound

Transaction IDs are finite.

Eventually they wrap around.

---

## Danger

Old rows may appear visible incorrectly.

---

## Solution

```
VACUUM FREEZE
```

---

## Interview Answer

> XID wraparound occurs when transaction IDs overflow, requiring freezing to preserve data visibility.

---

# 237. Vacuum Freeze

Marks old tuples as permanently visible.

---

## Benefits

Prevents XID wraparound.

---

## Interview Answer

> `VACUUM FREEZE` updates old tuples so they no longer depend on aging transaction IDs.

---

# 238. Catalog Tables

System metadata stored in:

```
pg_class

pg_index

pg_attribute

pg_database

pg_namespace
```

---

## Interview Answer

> Catalog tables store metadata describing databases, tables, columns, indexes, and other PostgreSQL objects.

---

# 239. Planner

## Definition

Chooses execution plans.

---

## Considers

- Statistics
- Cost
- Indexes
- Joins

---

## Interview Answer

> The Planner evaluates alternative execution strategies and selects the most cost-effective plan.

---

# 240. Executor

Runs the execution plan.

---

## Flow

```
Planner

↓

Execution Plan

↓

Executor

↓

Result
```

---

## Interview Answer

> The Executor performs the physical operations defined by the execution plan to produce query results.

---

# 241. Statistics Collector

Collects

- Query counts
- Buffer hits
- Table scans
- Index usage

---

## Interview Answer

> The Statistics Collector gathers runtime metrics that support monitoring and optimizer decisions.

---

# 242. Logical Replication

Replicates SQL changes.

---

## Supports

- Selected tables
- Cross-version upgrades
- Partial replication

---

## Interview Answer

> Logical replication transfers individual data changes, enabling selective replication and version upgrades.

---

# 243. Streaming Replication

Primary continuously streams WAL records.

---

## Architecture

```
Primary

↓

WAL

↓

Standby
```

---

## Interview Answer

> Streaming replication continuously sends WAL records from the primary server to standby servers.

---

# 244. Failover

If primary fails

↓

Standby promoted

↓

Clients reconnect

---

## Interview Answer

> Failover promotes a standby server to primary after a failure, minimizing downtime.

---

# 245. Hot Standby

Allows read-only queries on replicas.

---

## Benefits

- Reporting
- Analytics
- Load balancing

---

## Interview Answer

> Hot Standby enables read-only queries on replica servers while they continue receiving WAL updates.

---

# 246. Physical Replication

Replicates entire database cluster.

---

## Uses

WAL records.

---

## Characteristics

- Binary replication
- Exact copy
- Fast

---

## Interview Answer

> Physical replication copies the entire database cluster by replaying WAL records on standby servers.

---

# 247. Partitioning

Splits a table into smaller partitions.

---

## Types

- Range
- List
- Hash

---

## Example

```
Orders

↓

2023

2024

2025
```

---

## Benefits

- Faster queries
- Easier maintenance

---

## Interview Answer

> Partitioning improves scalability by dividing large tables into smaller, manageable partitions.

---

# 248. Table Inheritance

Child tables inherit columns.

---

## Example

```sql
CREATE TABLE employee
(
id INT,
name TEXT
);

CREATE TABLE manager
(
department TEXT
)
INHERITS(employee);
```

---

## Interview Answer

> Table inheritance allows child tables to inherit columns from parent tables, though declarative partitioning is generally preferred for partitioning use cases.

---

# 249. Extensions

Popular PostgreSQL Extensions

- PostGIS
- pg_stat_statements
- pg_trgm
- citext
- uuid-ossp
- pgcrypto

---

## Interview Answer

> Extensions add powerful capabilities such as geospatial processing, query monitoring, text search, and cryptographic functions without modifying the PostgreSQL core.

---

# 250. PostgreSQL Roadmap

Recent Focus Areas

- Better parallel execution
- Logical replication improvements
- JSON enhancements
- MERGE support
- Incremental sorting
- Performance improvements
- Security enhancements
- Cloud-native scalability

---

## Interview Answer

> PostgreSQL continues to evolve with improvements in performance, replication, JSON support, SQL compliance, cloud readiness, and extensibility while maintaining stability and reliability.

---

# Module Summary

- PostgreSQL follows a client-server architecture with backend processes, shared memory, WAL, and background workers.
- WAL, checkpoints, and the Buffer Manager work together to ensure durability, crash recovery, and efficient caching.
- MVCC enables high concurrency through tuple versioning, transaction IDs, visibility rules, and background cleanup with Autovacuum.
- Internal storage is organized into heap pages, visibility maps, free space maps, and TOAST for large values.
- The Planner and Executor cooperate to generate and execute optimized query plans using collected statistics.
- Replication options include Streaming Replication, Physical Replication, Logical Replication, Hot Standby, and automated failover for high availability.
- Partitioning, table inheritance, and extensions allow PostgreSQL to scale and support diverse enterprise workloads.
- A deep understanding of PostgreSQL internals helps engineers diagnose performance issues, optimize production systems, and design highly available database architectures.