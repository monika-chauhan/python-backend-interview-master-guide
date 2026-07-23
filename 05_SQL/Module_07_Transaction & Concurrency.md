# SQL Interview Master Guide

# Module 7: Transactions & Concurrency (Questions 191–220)

---

# 191. What is a Transaction?

## Definition

A **Transaction** is a sequence of one or more SQL operations executed as a single logical unit of work.

Either:

- All operations succeed (**COMMIT**)
- All operations fail (**ROLLBACK**)

---

## Example

```sql
BEGIN;

UPDATE accounts
SET balance = balance - 1000
WHERE id = 1;

UPDATE accounts
SET balance = balance + 1000
WHERE id = 2;

COMMIT;
```

---

## Interview Answer

> A transaction groups multiple database operations into a single unit that guarantees either complete success or complete rollback, ensuring data consistency.

---

# 192. ACID Properties

## A – Atomicity

All operations succeed or none succeed.

---

## C – Consistency

Database remains valid before and after the transaction.

---

## I – Isolation

Concurrent transactions do not interfere incorrectly.

---

## D – Durability

Committed changes survive crashes.

---

## Interview Answer

> ACID ensures reliable transaction processing through Atomicity, Consistency, Isolation, and Durability.

---

# 193. Isolation Levels

SQL Standard Isolation Levels:

1. Read Uncommitted
2. Read Committed
3. Repeatable Read
4. Serializable

---

## Interview Answer

> Isolation levels define how concurrent transactions interact and balance consistency against concurrency.

---

# 194. Dirty Read

## Definition

Reading uncommitted data from another transaction.

---

## Example

Transaction A:

```sql
UPDATE accounts
SET balance = 100;
```

(Not committed)

Transaction B:

```sql
SELECT balance
FROM accounts;
```

Reads **100** even if Transaction A later rolls back.

---

## Interview Answer

> A dirty read occurs when a transaction reads uncommitted data that may later be rolled back.

---

# 195. Non-repeatable Read

## Definition

A row changes between two reads in the same transaction.

---

## Example

Transaction A:

```sql
SELECT balance
FROM accounts;
```

Transaction B:

```sql
UPDATE accounts
SET balance = 200;
COMMIT;
```

Transaction A reads again and gets a different value.

---

## Interview Answer

> A non-repeatable read occurs when the same row returns different values within a transaction due to another committed transaction.

---

# 196. Phantom Read

## Definition

A repeated query returns additional or missing rows.

---

## Example

```sql
SELECT *
FROM orders
WHERE amount > 1000;
```

Another transaction inserts a matching row.

Repeating the query returns an extra row.

---

## Interview Answer

> Phantom reads occur when rows matching a query appear or disappear during a transaction.

---

# 197. Serializable Isolation

## Definition

Highest isolation level.

Transactions behave as if executed one after another.

---

## Advantages

- Prevents dirty reads
- Prevents non-repeatable reads
- Prevents phantom reads

---

## Drawback

Lower concurrency.

---

## Interview Answer

> Serializable isolation provides the strongest consistency by preventing all concurrency anomalies, though it may reduce throughput.

---

# 198. MVCC (Multi-Version Concurrency Control)

## Definition

Instead of locking readers, PostgreSQL stores multiple row versions.

---

## Illustration

```text
Old Version

↓

New Version
```

Readers access a snapshot while writers create new versions.

---

## Benefits

- High concurrency
- Readers don't block writers
- Writers don't block readers (in most cases)

---

## Interview Answer

> MVCC allows concurrent reads and writes by maintaining multiple versions of rows, minimizing locking contention.

---

# 199. Row Lock

Locks individual rows.

---

## Example

```sql
SELECT *
FROM accounts
WHERE id = 1
FOR UPDATE;
```

---

## Interview Answer

> Row locks prevent concurrent modifications to specific rows while allowing access to other rows.

---

# 200. Table Lock

Locks the entire table.

---

## Example

```sql
LOCK TABLE accounts
IN EXCLUSIVE MODE;
```

---

## Interview Answer

> Table locks restrict access to the entire table and are generally used only when row-level locking is insufficient.

---

# 201. Deadlock

## Definition

Two transactions wait indefinitely for each other.

---

## Example

Transaction A:

Locks Row 1 → waits for Row 2

Transaction B:

Locks Row 2 → waits for Row 1

---

## Interview Answer

> A deadlock occurs when transactions hold resources while waiting for resources held by each other.

---

# 202. Detect Deadlocks

PostgreSQL automatically detects deadlocks.

One transaction is terminated.

---

## Error

```text
ERROR: deadlock detected
```

---

## Interview Answer

> PostgreSQL periodically detects deadlock cycles and aborts one transaction to break the deadlock.

---

# 203. Prevent Deadlocks

Best Practices

- Lock resources in the same order
- Keep transactions short
- Commit quickly
- Avoid unnecessary locks

---

## Interview Answer

> Consistent lock ordering and short-lived transactions are the most effective ways to prevent deadlocks.

---

# 204. Optimistic Locking

## Idea

Assume conflicts are rare.

---

## Example

```text
Version = 5
```

Update succeeds only if version remains unchanged.

---

## SQL Example

```sql
UPDATE accounts
SET balance = 500,
    version = version + 1
WHERE id = 1
  AND version = 5;
```

---

## Interview Answer

> Optimistic locking detects conflicts during updates using version numbers instead of holding locks.

---

# 205. Pessimistic Locking

## Idea

Lock first, update later.

---

## Example

```sql
SELECT *
FROM accounts
WHERE id = 1
FOR UPDATE;
```

---

## Interview Answer

> Pessimistic locking acquires locks before modification to prevent concurrent conflicts.

---

# 206. Advisory Locks

Application-controlled locks.

---

## Example

```sql
SELECT pg_advisory_lock(12345);
```

Release:

```sql
SELECT pg_advisory_unlock(12345);
```

---

## Use Cases

- Scheduled jobs
- Distributed workers
- Singleton tasks

---

## Interview Answer

> Advisory locks provide application-level synchronization without locking specific database rows.

---

# 207. Savepoints

## Definition

Partial rollback points inside a transaction.

---

## Example

```sql
BEGIN;

SAVEPOINT sp1;

UPDATE accounts
SET balance = balance - 100;

ROLLBACK TO SAVEPOINT sp1;

COMMIT;
```

---

## Interview Answer

> Savepoints allow partial rollback within a transaction without aborting the entire transaction.

---

# 208. Rollback

Cancels all uncommitted changes.

---

## Example

```sql
ROLLBACK;
```

---

## Interview Answer

> `ROLLBACK` undoes all changes made during the current transaction since the last commit or savepoint.

---

# 209. Commit

Makes changes permanent.

---

## Example

```sql
COMMIT;
```

---

## Interview Answer

> `COMMIT` permanently saves all transaction changes and releases associated locks.

---

# 210. Long Running Transactions

## Problems

- Hold locks longer
- Delay VACUUM
- Increase MVCC bloat
- Reduce concurrency

---

## Interview Answer

> Long-running transactions negatively impact concurrency, storage cleanup, and overall database performance.

---

# 211. Lock Monitoring

PostgreSQL Views

```sql
SELECT *
FROM pg_locks;
```

Also use

```sql
pg_stat_activity
```

---

## Interview Answer

> Lock monitoring helps identify blocking sessions and diagnose lock contention in production.

---

# 212. Connection Pooling

## Definition

Reuse database connections instead of creating new ones.

---

## Benefits

- Faster connections
- Lower overhead
- Better scalability

---

## Interview Answer

> Connection pooling improves performance by reusing existing database connections across requests.

---

# 213. PgBouncer

Lightweight PostgreSQL connection pooler.

---

## Pool Modes

- Session
- Transaction
- Statement

---

## Interview Answer

> PgBouncer reduces connection overhead and enables PostgreSQL to support many more client connections efficiently.

---

# 214. Pool Size

Should balance:

- Database capacity
- CPU cores
- Workload

---

## Rule of Thumb

Avoid creating hundreds of unnecessary database connections.

---

## Interview Answer

> Pool size should be tuned according to workload and database resources to maximize throughput without overwhelming the server.

---

# 215. Transaction Retry

Useful for:

- Deadlocks
- Serialization failures
- Temporary conflicts

---

## Pseudocode

```python
for attempt in range(3):
    try:
        transaction()
        break
    except SerializationFailure:
        retry()
```

---

## Interview Answer

> Applications should retry transient transaction failures such as deadlocks or serialization conflicts using controlled retry logic.

---

# 216. Distributed Transactions

A transaction spanning multiple databases or services.

---

## Challenges

- Network failures
- Partial commits
- Coordination

---

## Interview Answer

> Distributed transactions coordinate updates across multiple systems while preserving consistency, but they increase complexity and latency.

---

# 217. Two Phase Commit (2PC)

## Phase 1

Prepare

All participants vote.

---

## Phase 2

Commit or Rollback

Coordinator decides.

---

## Drawbacks

- Blocking
- Coordinator failure
- Poor scalability

---

## Interview Answer

> Two-Phase Commit guarantees atomic distributed transactions but introduces blocking and coordination overhead.

---

# 218. Saga Pattern

Alternative to 2PC in microservices.

---

## Example

```
Reserve Inventory

↓

Create Order

↓

Charge Payment

↓

Ship Product
```

Failure triggers compensating actions.

---

## Interview Answer

> The Saga Pattern manages distributed transactions using a sequence of local transactions with compensating actions instead of global locks.

---

# 219. Idempotency

## Definition

Repeated execution produces the same result.

---

## Example

Payment API

```
Request ID: abc123
```

Duplicate requests do not charge twice.

---

## Interview Answer

> Idempotency ensures that retrying the same request does not produce duplicate side effects, making distributed systems more reliable.

---

# 220. Banking Transaction Design

## Example

Money Transfer

```sql
BEGIN;

UPDATE accounts
SET balance = balance - 1000
WHERE id = 1;

UPDATE accounts
SET balance = balance + 1000
WHERE id = 2;

INSERT INTO transaction_log(...);

COMMIT;
```

---

## Production Considerations

- ACID compliance
- Row-level locking (`FOR UPDATE`)
- Idempotency keys
- Audit logs
- Retry on transient failures
- Deadlock handling
- Fraud checks
- Monitoring and alerts

---

## Interview Answer

> A banking transaction should guarantee atomic fund transfers using ACID transactions, row-level locking, audit logging, idempotency, retry mechanisms, and robust error handling to ensure consistency and reliability.

---

# Module Summary

- Transactions ensure reliable execution of multiple SQL operations using the ACID principles.
- Isolation levels control concurrency behavior and prevent anomalies such as dirty reads, non-repeatable reads, and phantom reads.
- PostgreSQL uses MVCC to provide high concurrency by maintaining multiple versions of rows instead of locking readers.
- Row locks, table locks, optimistic locking, pessimistic locking, and advisory locks address different synchronization requirements.
- Deadlocks occur when transactions wait on each other and should be prevented through consistent lock ordering and short transactions.
- Savepoints, `COMMIT`, and `ROLLBACK` provide fine-grained transaction control and recovery.
- Connection pooling with tools like PgBouncer improves scalability by reducing connection overhead.
- Distributed transactions can be implemented using Two-Phase Commit (2PC) or, more commonly in microservices, the Saga Pattern.
- Idempotency is essential for safely retrying operations such as payment processing and banking transactions.
- Production-grade transaction design requires balancing consistency, concurrency, fault tolerance, and performance.