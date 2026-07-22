# Module 10: Production Python (Questions 96–100)

# Q96. How do you structure a large Python project?

## Answer

A large Python project should be **modular, scalable, maintainable, and testable**. The goal is to separate concerns so that each component has a single responsibility.

A good project structure improves:

- Readability
- Team collaboration
- Code reuse
- Testing
- Deployment
- Scalability

---

# Typical Enterprise Structure

```text
ecommerce/

│
├── app/
│   ├── api/
│   │   ├── v1/
│   │   ├── middleware/
│   │   ├── routes/
│   │   └── dependencies.py
│   │
│   ├── core/
│   │   ├── config.py
│   │   ├── security.py
│   │   ├── constants.py
│   │   └── logging.py
│   │
│   ├── models/
│   │
│   ├── schemas/
│   │
│   ├── services/
│   │
│   ├── repositories/
│   │
│   ├── database/
│   │
│   ├── utils/
│   │
│   ├── workers/
│   │
│   └── main.py
│
├── tests/
│
├── docs/
│
├── scripts/
│
├── Dockerfile
├── docker-compose.yml
├── pyproject.toml
├── requirements.txt
└── README.md
```

---

# Layered Architecture

```
Client
    │
    ▼
API Layer
    │
    ▼
Service Layer
    │
    ▼
Repository Layer
    │
    ▼
Database
```

Each layer has a single responsibility.

---

# Responsibilities

### API Layer

- Request validation
- Authentication
- Routing
- Response formatting

---

### Service Layer

Contains business logic.

Example

```python
class OrderService:

    def place_order(self):
        ...
```

---

### Repository Layer

Handles data access.

Example

```python
class UserRepository:

    def get_user():
        ...
```

---

### Database Layer

Responsible only for database connectivity.

---

# Advantages

- Loose coupling
- Easy testing
- Better scalability
- Easier maintenance
- Reusable modules

---

# Common Mistakes

❌ Business logic inside API routes

❌ Database queries inside controllers

❌ Circular imports

❌ Huge utility files

---

# Interview Answer

> I prefer a layered architecture with separate API, service, repository, and data access layers. Business logic stays in services, persistence in repositories, configuration in a dedicated core package, and shared utilities are isolated. This improves maintainability, testing, and scalability for large teams.

---

# Q97. How do you design reusable Python packages?

## Answer

Reusable packages should have:

- Clear responsibilities
- Stable APIs
- Minimal dependencies
- Good documentation
- High test coverage

---

## Example Structure

```text
payment_sdk/

│
├── payment_sdk/
│   ├── __init__.py
│   ├── client.py
│   ├── exceptions.py
│   ├── config.py
│   ├── auth.py
│   ├── utils.py
│   └── models.py
│
├── tests/
│
├── pyproject.toml
│
└── README.md
```

---

## Good API Design

Instead of exposing many internal classes:

```python
from payment_sdk.client import PaymentClient

client = PaymentClient(...)
```

Avoid exposing internal implementation details.

---

## Package Design Principles

### Single Responsibility

One package should solve one problem.

---

### Loose Coupling

Avoid tight dependencies.

---

### Dependency Injection

```python
class PaymentService:

    def __init__(self, gateway):
        self.gateway = gateway
```

---

### Semantic Versioning

```
1.0.0

Major

Minor

Patch
```

---

## Best Practices

- Type hints
- Docstrings
- Logging
- Configuration via environment variables
- Backward compatibility
- Unit tests

---

## Interview Tip

Treat a reusable package like a public API. Minimize breaking changes and hide internal implementation details behind a stable interface.

---

# Q98. Logging Best Practices in Production

## Answer

Logging helps monitor applications, diagnose issues, audit behavior, and troubleshoot production failures.

---

## Avoid

```python
print("Error")
```

`print()` is not suitable for production because it lacks log levels, timestamps, and structured output.

---

## Use the logging Module

```python
import logging

logger = logging.getLogger(__name__)

logger.info("Application started")
logger.warning("Cache miss")
logger.error("Database error")
```

---

## Log Levels

| Level | Use |
|---------|-----|
| DEBUG | Detailed diagnostic information |
| INFO | Normal application events |
| WARNING | Unexpected but recoverable situations |
| ERROR | Operation failed |
| CRITICAL | Application cannot continue |

---

## Structured Logging

Prefer machine-readable formats such as JSON.

Example:

```json
{
  "timestamp":"2026-07-22T10:00:00Z",
  "level":"INFO",
  "request_id":"abc123",
  "user_id":"42",
  "message":"Order created"
}
```

---

## Correlation IDs

Include a unique request or trace ID in every log entry.

```
Request

↓

Trace ID

↓

Every Log
```

This simplifies debugging across distributed services.

---

## Don't Log

- Passwords
- Tokens
- Credit card numbers
- Personal Identifiable Information (PII)
- Encryption keys

---

## Best Practices

✔ Structured logging

✔ Log rotation

✔ Centralized log aggregation (e.g., ELK, Splunk)

✔ Correlation IDs

✔ Appropriate log levels

✔ Avoid duplicate logs

✔ Sanitize sensitive data

---

## Interview Answer

> In production, I use Python's `logging` module with structured logs, appropriate log levels, correlation IDs, and centralized log aggregation. Sensitive information is never logged, and logs are designed to support debugging, monitoring, and auditing.

---

# Q99. Exception Hierarchy Design for Enterprise Applications

## Answer

Large applications should define a custom exception hierarchy instead of raising generic exceptions everywhere.

---

## Poor Design

```python
raise Exception("Invalid user")
```

This makes error handling difficult and lacks semantic meaning.

---

## Better Design

```python
class AppError(Exception):
    """Base exception for the application."""

class ValidationError(AppError):
    pass

class AuthenticationError(AppError):
    pass

class AuthorizationError(AppError):
    pass

class DatabaseError(AppError):
    pass

class PaymentError(AppError):
    pass
```

---

## Usage

```python
raise ValidationError("Email is invalid")
```

---

## Benefits

- Easier exception handling
- More meaningful errors
- Better API responses
- Improved logging
- Consistent behavior

---

## Mapping Exceptions

```
ValidationError

↓

HTTP 400

-----------------

AuthenticationError

↓

HTTP 401

-----------------

AuthorizationError

↓

HTTP 403

-----------------

NotFoundError

↓

HTTP 404

-----------------

DatabaseError

↓

HTTP 500
```

---

## Best Practices

- Create a common base exception.
- Add context (error codes, metadata) where appropriate.
- Preserve original exceptions using `raise ... from ...`.
- Avoid swallowing exceptions silently.

---

## Interview Tip

A well-designed exception hierarchy enables consistent error handling across services and simplifies mapping internal failures to user-facing responses.

---

# Q100. How do you optimize a Python backend service handling millions of requests?

## Answer

Optimizing a high-scale backend requires improvements across the application stack rather than focusing on a single component.

---

# High-Level Architecture

```text
                    Load Balancer
                          │
          ┌───────────────┴───────────────┐
          │                               │
     API Instance 1                  API Instance 2
          │                               │
          └───────────────┬───────────────┘
                          │
                    Redis Cache
                          │
                    Message Queue
                          │
                 Worker Processes
                          │
                     PostgreSQL
```

---

# 1. Use Asynchronous I/O

Use asynchronous frameworks such as **FastAPI**, **aiohttp**, or async features in Django where appropriate for I/O-bound operations.

Benefits:

- Better utilization of resources.
- More concurrent requests per process.
- Lower latency for I/O-heavy workloads.

---

# 2. Database Optimization

- Create proper indexes.
- Avoid N+1 query problems.
- Batch database operations.
- Use connection pooling.
- Optimize SQL queries.
- Cache frequently accessed data.

---

# 3. Caching

Use Redis or Memcached for:

- Frequently accessed data
- API responses
- Authentication/session data
- Expensive computations

```
Request

↓

Redis

↓

Cache Hit?

↓

Yes → Return

↓

No

↓

Database
```

---

# 4. Horizontal Scaling

Run multiple application instances behind a load balancer.

```
Client

↓

Load Balancer

↓

Server 1

Server 2

Server 3
```

This improves throughput and resilience.

---

# 5. Background Processing

Move long-running tasks out of the request path.

Examples:

- Sending emails
- Image processing
- Report generation
- Notifications

Use task queues such as Celery, RQ, or similar systems.

---

# 6. Efficient Serialization

Use fast JSON libraries where appropriate.

Example:

```python
import orjson

payload = orjson.dumps(data)
```

---

# 7. Optimize Python Code

- Profile before optimizing.
- Use efficient algorithms and data structures.
- Reduce unnecessary allocations.
- Reuse expensive resources (database connections, HTTP sessions).
- Use vectorized libraries for numerical work when applicable.

---

# 8. Observability

Monitor:

- Response latency
- Throughput
- Error rates
- CPU usage
- Memory usage
- Database performance

Use metrics, distributed tracing, and centralized logging.

---

# 9. Rate Limiting

Protect the service from abuse.

Examples:

```
100 Requests

↓

1 Minute

↓

Per User
```

---

# 10. Security

- Authentication and authorization
- Input validation
- HTTPS
- Secrets management
- Protection against SQL injection and XSS
- Regular dependency updates

---

## Performance Optimization Checklist

| Layer | Optimization |
|---------|-------------|
| API | Async I/O, request validation |
| Application | Efficient algorithms, connection reuse |
| Database | Indexes, pooling, query optimization |
| Cache | Redis/Memcached |
| Infrastructure | Load balancing, horizontal scaling |
| Background Jobs | Task queues |
| Monitoring | Metrics, logs, traces |
| Security | Rate limiting, authentication |

---

## Senior Interview Answer

> To optimize a Python backend handling millions of requests, I start by profiling the application to identify bottlenecks. For I/O-bound workloads, I use asynchronous frameworks and reuse expensive resources like database pools and HTTP sessions. I optimize database access with indexes, batching, and connection pooling, and reduce load using Redis caching. Long-running tasks are offloaded to background workers, while multiple application instances run behind a load balancer for horizontal scalability. Finally, I implement structured logging, metrics, distributed tracing, rate limiting, and comprehensive monitoring to ensure both performance and operational reliability.

---

# Senior Interview Summary

### Enterprise Python Design Principles

- Use a layered architecture (API → Service → Repository → Database).
- Build reusable packages with clear interfaces and semantic versioning.
- Use structured logging with correlation IDs and centralized aggregation.
- Design a meaningful custom exception hierarchy.
- Scale backend services using async I/O, caching, optimized databases, background workers, and horizontal scaling.
- Measure performance continuously using profiling, metrics, logs, and traces.

---

**End of Module 10 (Questions 96–100)**

**🎉 Congratulations! You have completed the complete Python Senior Developer Interview Guide (Questions 1–100).**