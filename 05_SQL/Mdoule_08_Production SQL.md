# SQL Interview Master Guide

# Module 10: Production SQL (Questions 281–320)

---

# 281. Design Database for E-commerce

## Core Tables

```text
Users
 ├── user_id (PK)
 ├── name
 └── email

Products
 ├── product_id (PK)
 ├── name
 ├── price
 └── stock

Orders
 ├── order_id (PK)
 ├── user_id (FK)
 ├── status
 └── created_at

Order_Items
 ├── order_id (FK)
 ├── product_id (FK)
 ├── quantity
 └── price

Payments
 ├── payment_id (PK)
 ├── order_id (FK)
 └── status
```

### Best Practices

- Normalize transactional tables.
- Index `user_id`, `product_id`, `created_at`.
- Use soft delete for products.
- Partition orders by date.
- Store immutable order-item prices.

### Interview Answer

> Separate users, products, orders, order items, and payments. Use proper indexing, normalization, partitioning, and transactions to support high-volume workloads.

---

# 282. Design Banking Database

## Core Tables

- Customers
- Accounts
- Transactions
- Beneficiaries
- Audit Logs

### Rules

- Double-entry accounting
- ACID transactions
- Row-level locking (`FOR UPDATE`)
- Immutable transaction history
- Audit trail

### Interview Answer

> Banking databases prioritize consistency, auditability, and fault tolerance using ACID transactions, immutable ledgers, and strict locking.

---

# 283. Design Inventory System

## Tables

- Products
- Warehouses
- Inventory
- Purchase Orders
- Sales Orders
- Stock Movements

### Best Practices

- Track every inventory movement.
- Never overwrite stock history.
- Use optimistic locking for concurrent updates.

### Interview Answer

> Inventory systems maintain current stock and complete movement history to ensure traceability and consistency.

---

# 284. Design Employee System

## Tables

- Employees
- Departments
- Designations
- Attendance
- Payroll
- Leave Requests

### Best Practices

- Normalize employee data.
- Store historical salary changes separately.
- Audit sensitive updates.

### Interview Answer

> Employee management systems separate HR entities while preserving historical records for payroll and compliance.

---

# 285. Audit Tables

## Purpose

Track all data modifications.

### Example

```text
audit_log
---------
table_name
record_id
action
old_value
new_value
changed_by
changed_at
```

### Interview Answer

> Audit tables preserve a complete history of database changes for compliance, debugging, and accountability.

---

# 286. Soft Delete

## Example

```sql
ALTER TABLE users
ADD COLUMN deleted_at TIMESTAMP;
```

Instead of

```sql
DELETE FROM users;
```

Use

```sql
UPDATE users
SET deleted_at = NOW();
```

### Benefits

- Recovery
- Auditing
- Referential integrity

### Interview Answer

> Soft deletes mark records as deleted instead of removing them permanently, enabling recovery and historical analysis.

---

# 287. Event Sourcing

## Concept

Store events instead of current state.

```text
Create Account

↓

Deposit ₹1000

↓

Withdraw ₹200
```

Current balance is derived from events.

### Interview Answer

> Event sourcing stores every state change as an immutable event, enabling replay, auditing, and historical reconstruction.

---

# 288. CDC (Change Data Capture)

## Definition

Capture database changes in real time.

### Common Tools

- Debezium
- Logical Decoding
- Kafka Connect

### Interview Answer

> CDC streams inserts, updates, and deletes to downstream systems for synchronization and analytics.

---

# 289. Multi-Tenant Database

## Approaches

### Shared Database, Shared Schema

- Cheapest
- Tenant ID column

### Shared Database, Separate Schemas

- Better isolation

### Separate Database per Tenant

- Strong isolation
- Higher operational cost

### Interview Answer

> Choose the tenancy model based on isolation, scalability, compliance, and operational complexity.

---

# 290. Read Replica

```text
Primary

├── Write

├── Replica 1

├── Replica 2
```

### Uses

- Read scaling
- Reporting
- Analytics

### Interview Answer

> Read replicas offload read traffic from the primary database while writes continue on the primary.

---

# 291. Sharding

## Definition

Split data across multiple database servers.

### Example

```text
Shard 1 → Customer 1–1M

Shard 2 → Customer 1M–2M

Shard 3 → Customer 2M–3M
```

### Challenges

- Cross-shard joins
- Rebalancing
- Transactions

### Interview Answer

> Sharding horizontally partitions data across multiple databases to improve scalability and throughput.

---

# 292. Archiving

Move old data to archive storage.

### Benefits

- Smaller active tables
- Faster queries
- Reduced storage costs

### Interview Answer

> Archiving keeps operational tables small while preserving historical data for reporting and compliance.

---

# 293. Partition Strategy

Choose partition key based on access patterns.

### Examples

- Date
- Region
- Tenant
- Customer ID

### Interview Answer

> Effective partitioning aligns with query patterns to maximize partition pruning and maintenance efficiency.

---

# 294. Billion Row Table

### Best Practices

- Partitioning
- Composite indexes
- Read replicas
- Batch processing
- Compression
- Vacuum tuning
- Cursor pagination

### Interview Answer

> Billion-row tables require partitioning, optimized indexing, efficient pagination, and regular maintenance to remain performant.

---

# 295. High Availability

## Components

- Primary
- Standby
- Automatic failover
- Load balancer

### Interview Answer

> High availability minimizes downtime using replication, redundancy, and automated failover mechanisms.

---

# 296. Disaster Recovery

### Components

- Automated backups
- WAL archiving
- PITR
- Replica promotion
- Recovery testing

### Interview Answer

> Disaster recovery ensures business continuity through backups, replication, and validated recovery procedures.

---

# 297. Connection Pool Optimization

### Guidelines

- Use PgBouncer
- Tune pool size
- Avoid idle connections
- Close unused sessions

### Interview Answer

> Connection pooling improves scalability by efficiently reusing database connections.

---

# 298. Database Monitoring

### Metrics

- CPU
- Memory
- Disk I/O
- Active connections
- Lock waits
- Query latency
- Cache hit ratio

### Tools

- Prometheus
- Grafana
- pg_stat_statements

### Interview Answer

> Continuous monitoring helps detect bottlenecks before they impact production systems.

---

# 299. Slow Query Debugging

### Process

1. Identify slow query
2. Run `EXPLAIN ANALYZE`
3. Check indexes
4. Rewrite query
5. Validate improvements

### Interview Answer

> Slow queries are diagnosed using execution plans, indexing analysis, and query optimization.

---

# 300. SQL Production Interview Scenarios

Typical topics:

- Deadlocks
- Lock contention
- Missing indexes
- Slow joins
- Replication lag
- Large migrations

### Interview Answer

> Production SQL interviews focus on diagnosing real-world scalability and performance issues.

---

# 301. Weekly Sales Consolidation API Design

### Flow

```text
API

↓

Service Layer

↓

Aggregated SQL

↓

Redis Cache

↓

Response
```

### SQL

```sql
SELECT
    DATE_TRUNC('week', order_date) AS week,
    SUM(amount) AS total_sales
FROM orders
GROUP BY week;
```

### Interview Answer

> Aggregate sales at the database layer, cache results, and expose them through a stateless API.

---

# 302. Optimizing ORM Queries in Django

### Techniques

- `select_related()`
- `prefetch_related()`
- `only()`
- `defer()`
- Bulk operations
- Query annotations
- Avoid N+1 queries

### Interview Answer

> Efficient ORM usage minimizes database round trips and retrieves only the required data.

---

# 303. Designing Reporting Queries

### Guidelines

- Materialized views
- Read replicas
- Aggregation tables
- Partition pruning

### Interview Answer

> Reporting workloads should be isolated from OLTP traffic using replicas and precomputed summaries.

---

# 304. Handling Millions of Records

### Best Practices

- Batch processing
- Streaming
- Partitioning
- Cursor pagination
- Compression

### Interview Answer

> Large datasets require memory-efficient processing and optimized storage strategies.

---

# 305. Cursor vs Offset Pagination in APIs

| Cursor | Offset |
|----------|---------|
| Fast | Slower on large tables |
| Stable | Can skip/duplicate rows |
| Scalable | Simpler implementation |

### Interview Answer

> Cursor pagination is preferred for high-scale APIs because it avoids scanning skipped rows.

---

# 306. Database Migration with Zero Downtime

### Strategy

1. Add nullable column
2. Deploy application
3. Backfill data in batches
4. Enable new column
5. Remove old column later

### Interview Answer

> Zero-downtime migrations use backward-compatible schema changes and phased deployments.

---

# 307. Scaling PostgreSQL for High Traffic

### Techniques

- Read replicas
- Partitioning
- Connection pooling
- Caching
- Horizontal scaling (Citus)
- Query optimization

### Interview Answer

> Scaling PostgreSQL combines vertical optimization with horizontal techniques such as replicas and distributed extensions.

---

# 308. Read/Write Splitting

```text
Application

├── Writes → Primary

└── Reads → Replicas
```

### Interview Answer

> Read/write splitting reduces primary database load while maintaining write consistency.

---

# 309. Index Strategy for Large Tables

### Guidelines

- Composite indexes
- Partial indexes
- Covering indexes
- Remove unused indexes
- Monitor index usage

### Interview Answer

> Indexes should match query patterns while minimizing maintenance overhead.

---

# 310. Real-Time Analytics with PostgreSQL

### Stack

```text
Application

↓

CDC

↓

Kafka

↓

Analytics DB
```

or

- Materialized Views
- TimescaleDB
- Citus

### Interview Answer

> Real-time analytics combines streaming pipelines with optimized analytical storage.

---

# 311. Partition Pruning

## Definition

PostgreSQL scans only relevant partitions.

### Example

```sql
WHERE order_date >= '2025-01-01'
```

Only matching partitions are accessed.

### Interview Answer

> Partition pruning improves query performance by eliminating unnecessary partitions during execution.

---

# 312. Hot vs Cold Data

### Hot Data

- Frequently accessed
- SSD
- Cached

### Cold Data

- Archived
- Infrequently accessed
- Lower-cost storage

### Interview Answer

> Separating hot and cold data improves performance while reducing storage costs.

---

# 313. Backup Validation

### Checklist

- Restore test
- Data integrity check
- Recovery time measurement

### Interview Answer

> A backup is only useful if it can be successfully restored and verified.

---

# 314. Disaster Recovery Drills

### Steps

- Simulate failure
- Restore backup
- Promote replica
- Validate application

### Interview Answer

> Regular disaster recovery drills ensure recovery procedures work before real incidents occur.

---

# 315. Query Timeout Strategies

### Methods

- `statement_timeout`
- Application-level timeout
- Query cancellation

### Interview Answer

> Timeouts prevent runaway queries from exhausting database resources.

---

# 316. Lock Contention Analysis

### Tools

```sql
SELECT * FROM pg_locks;
```

```sql
SELECT * FROM pg_stat_activity;
```

### Solutions

- Short transactions
- Better indexing
- Reduced lock scope

### Interview Answer

> Analyze blocking sessions and reduce lock duration to improve concurrency.

---

# 317. PostgreSQL Monitoring Tools

### Common Tools

- pg_stat_statements
- pgAdmin
- Prometheus
- Grafana
- Datadog
- New Relic
- pganalyze

### Interview Answer

> Monitoring tools provide visibility into query performance, resource usage, and database health.

---

# 318. Production Incident Case Study

## Scenario

An API latency increases from **200 ms** to **8 seconds**.

### Investigation

- Check dashboards
- Identify slow query
- Run `EXPLAIN ANALYZE`
- Detect missing index
- Deploy index concurrently
- Verify latency reduction

### Interview Answer

> Production incidents should be handled systematically using monitoring, execution-plan analysis, and incremental remediation.

---

# 319. Database Design Trade-offs

| Decision | Benefit | Trade-off |
|-----------|---------|-----------|
| Normalize | Consistency | More joins |
| Denormalize | Faster reads | Data duplication |
| Partition | Scalability | Operational complexity |
| Sharding | Horizontal scale | Cross-shard challenges |
| Indexing | Faster queries | Slower writes |

### Interview Answer

> Database design requires balancing performance, consistency, scalability, maintainability, and operational complexity.

---

# 320. End-to-End Database System Design

## Example Architecture

```text
                Clients
                    │
             API Gateway
                    │
           Django / FastAPI
                    │
          Connection Pool (PgBouncer)
                    │
              PostgreSQL Primary
             /                  \
     Read Replica         Read Replica
             │                  │
         Redis Cache        Reporting
                    │
               Kafka / CDC
                    │
            Analytics Platform
                    │
        Backup + WAL Archive + PITR
```

### Key Features

- Normalized OLTP schema
- Read/write splitting
- Read replicas
- Redis caching
- Partitioned large tables
- Connection pooling
- WAL archiving
- Automated backups
- Monitoring & alerting
- High availability and failover
- Zero-downtime deployments

### Interview Answer

> A production-grade database architecture combines optimized schema design, caching, replication, partitioning, monitoring, backup, and disaster recovery to deliver scalability, reliability, and high availability.

---

# Module Summary

- Production database design focuses on scalability, consistency, availability, and operational simplicity.
- Real-world systems use partitioning, replication, sharding, connection pooling, and caching to handle millions or billions of records.
- High availability depends on replicas, automated failover, backup validation, and disaster recovery planning.
- Large-scale APIs benefit from cursor pagination, optimized ORM usage, efficient indexing, and reporting isolation.
- Zero-downtime migrations require phased schema changes and backward-compatible deployments.
- Monitoring, slow-query analysis, lock contention investigation, and incident response are essential operational skills.
- Modern PostgreSQL deployments integrate with Redis, Kafka, CDC pipelines, TimescaleDB, Citus, and cloud-native infrastructure.
- Senior-level SQL interviews often evaluate the ability to design complete database systems while explaining trade-offs, scalability strategies, and production best practices.