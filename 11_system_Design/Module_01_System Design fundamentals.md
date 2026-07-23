# System Design Interview Master Guide

# Module 1: System Design Fundamentals (Questions 1–25)

> **Interview Note:** System Design interviews evaluate how you think about building **large-scale, reliable, maintainable systems**. Senior engineers are expected to discuss requirements, scalability, availability, performance, security, trade-offs, and operational concerns.

---

# 1. Architecture Basics

## What is System Architecture?

System architecture describes the **high-level structure of a software system**, including:

- Components
- Services
- Databases
- Communication patterns
- Data flow
- Infrastructure

---

## Example

A scalable e-commerce system:

```
                 Users
                   │
             Load Balancer
                   │
              API Gateway
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
    User Service Product    Order
                  Service   Service
        │          │          │
        └──────────┼──────────┘
                   │
              Database Layer
```

---

## Architecture Layers

### 1. Presentation Layer

Responsible for:

- UI
- API Gateway
- Authentication

---

### 2. Business Layer

Contains:

- Business rules
- Services
- Workflows

---

### 3. Data Layer

Handles:

- Databases
- Cache
- Storage

---

### 4. Infrastructure Layer

Includes:

- Servers
- Kubernetes
- Networking
- Monitoring

---

# 2. What is System Design?

## Definition

System design is the process of designing a software system that satisfies:

- Functional requirements
- Non-functional requirements
- Scalability needs
- Reliability requirements

---

## Example

Question:

> Design YouTube.

You need to decide:

- Video storage
- Streaming architecture
- Database design
- CDN usage
- Recommendation system
- Scaling strategy

---

## System Design Process

```
Requirements

↓

Capacity Estimation

↓

High-Level Architecture

↓

Database Design

↓

API Design

↓

Scaling Strategy

↓

Trade-offs
```

---

## Interview Answer

> System design is the process of defining architecture, components, data flow, APIs, and infrastructure required to build scalable and reliable software systems.

---

# 3. Difference Between HLD and LLD

## High-Level Design (HLD)

Focuses on system architecture.

Questions answered:

- What services exist?
- How do components communicate?
- What databases are used?
- How does scaling work?

---

Example:

Design Uber:

```
User Service

Driver Service

Location Service

Payment Service
```

---

## Low-Level Design (LLD)

Focuses on internal implementation.

Questions answered:

- Classes
- Interfaces
- Design patterns
- Object relationships

---

Example:

```
class Driver:
    id
    location
    status

class Ride:
    driver
    passenger
    fare
```

---

## Comparison

| HLD | LLD |
|-|-|
| Architecture | Code structure |
| Services | Classes |
| Databases | Objects |
| APIs | Methods |
| Scalability | Maintainability |

---

## Interview Answer

> HLD defines the overall system architecture, while LLD focuses on class-level design and implementation details.

---

# 4. Functional vs Non-Functional Requirements

## Functional Requirements

Describe what the system should do.

Examples:

- User login
- Upload image
- Send message
- Search products

---

## Non-Functional Requirements

Describe system qualities.

Examples:

- Scalability
- Availability
- Security
- Performance
- Reliability

---

## Example

Netflix

Functional:

- Play video
- Search movies

Non-functional:

- 99.99% availability
- Low latency
- Global scalability

---

## Interview Answer

> Functional requirements define features, while non-functional requirements define quality attributes and system constraints.

---

# 5. How to Gather Requirements During an Interview?

Before designing, clarify:

## Step 1: Identify Users

Example:

```
Who uses the system?

Customers?
Admins?
Businesses?
```

---

## Step 2: Identify Core Features

Example:

Twitter:

- Create tweet
- Follow users
- Like tweet
- Timeline

---

## Step 3: Identify Scale

Ask:

- Number of users?
- Requests per second?
- Data size?
- Geographic distribution?

---

## Step 4: Identify Constraints

Ask:

- Real-time?
- Strong consistency?
- Availability requirement?

---

## Interview Framework

```
Users

↓

Features

↓

Scale

↓

Constraints

↓

Architecture
```

---

## Interview Answer

> I start by clarifying users, core use cases, expected scale, consistency requirements, latency requirements, and operational constraints before proposing architecture.

---

# 6. How to Estimate Scale?

Scale estimation helps choose architecture.

Estimate:

- Users
- Traffic
- Storage
- Bandwidth
- Compute requirements

---

## Example

Social media application:

Users:

```
100 Million
```

Daily active users:

```
20 Million
```

Requests:

```
500 Million/day
```

---

# 7. Back-of-the-Envelope Calculations

## Purpose

Quickly estimate system requirements.

---

## Common Assumptions

### Time

```
1 day = 86,400 seconds
≈ 100,000 seconds
```

---

### Storage

```
1 KB × 1 Billion records

≈ 1 TB
```

---

### Million Conversion

```
1 Million = 10^6
1 Billion = 10^9
```

---

## Interview Answer

> Back-of-the-envelope estimation provides quick calculations to understand capacity requirements before designing the system.

---

# 8. QPS Calculation

## QPS

Queries Per Second.

---

## Formula

```
QPS = Total Requests / Seconds
```

---

## Example

10 million requests/day.

Seconds:

```
86,400
```

QPS:

```
10,000,000 / 86,400

≈ 116 requests/sec
```

---

## Peak Traffic

Usually:

```
Peak QPS = Average QPS × 2 to 5
```

---

## Interview Answer

> QPS estimation helps determine server capacity, load balancing requirements, and scaling needs.

---

# 9. Storage Estimation

## Formula

```
Storage

=

Number of Records

×

Size per Record
```

---

## Example

User data:

```
100 Million users

Each record = 1 KB
```

Storage:

```
100 Million × 1 KB

≈ 100 GB
```

---

## Include

- Indexes
- Replication
- Backups

---

## Real Example

Database storage:

```
Primary Data

+

Indexes

+

Replica Copies

+

Backup Storage
```

---

# 10. Bandwidth Estimation

## Formula

```
Bandwidth

=

Requests/sec

×

Response Size
```

---

## Example

1000 requests/sec

Response:

100 KB

Bandwidth:

```
1000 × 100 KB

=100 MB/sec
```

---

## Importance

Used for:

- CDN
- Network planning
- API optimization

---

# 11. Latency Estimation

## Latency

Time taken to complete a request.

---

## Components

```
Network

+

Load Balancer

+

Application

+

Database

+

External APIs
```

---

## Example

API latency:

```
Network     20 ms

Backend     50 ms

Database    30 ms

Total      100 ms
```

---

## Latency Optimization

Techniques:

- Cache
- CDN
- Database indexing
- Async processing
- Connection pooling

---

# 12. CAP Theorem

A distributed system cannot guarantee all three simultaneously:

```
Consistency

Availability

Partition Tolerance
```

---

## Examples

Banking:

Prefer:

```
Consistency
```

Social media:

Prefer:

```
Availability
```

---

# 13. Scalability

## Definition

Ability to handle increasing load.

---

## Types

### Vertical Scaling

Increase machine power.

Example:

```
8 CPU

↓

32 CPU
```

---

### Horizontal Scaling

Add more machines.

Example:

```
Server 1

Server 2

Server 3
```

---

## Interview Answer

> Horizontal scaling is preferred for large systems because it provides better fault tolerance and unlimited growth.

---

# 14. Availability

## Definition

System uptime percentage.

---

## Examples

99%

≈ 3.65 days downtime/year

99.9%

≈ 8.7 hours/year

99.99%

≈ 52 minutes/year

---

# 15. Reliability

Ability to operate correctly over time.

---

## Achieved Through

- Replication
- Monitoring
- Backup
- Failover

---

# 16. Fault Tolerance

System continues working despite failures.

---

Examples:

- Database replica
- Multiple servers
- Retry mechanism

---

# 17. Load Balancing

Distributes requests among servers.

---

Algorithms:

- Round Robin
- Least Connections
- Weighted Routing

---

Architecture:

```
Users

↓

Load Balancer

↓

Servers
```

---

# 18. Caching

Stores frequently accessed data.

---

Types:

- Browser cache
- CDN cache
- Redis cache
- Application cache

---

Benefits:

- Lower latency
- Reduced database load

---

# 19. Database Scaling

Methods:

## Vertical Scaling

Bigger machine.

## Replication

Multiple copies.

## Partitioning

Split data.

## Sharding

Distribute data across databases.

---

# 20. Availability vs Consistency Trade-off

Example:

Bank transfer:

Need:

```
Strong consistency
```

Instagram likes:

Can accept:

```
Eventual consistency
```

---

# 21. Synchronous vs Asynchronous Processing

## Synchronous

User waits.

Example:

Login

---

## Asynchronous

Background processing.

Example:

Email sending

---

Architecture:

```
Request

↓

Queue

↓

Worker

↓

Process
```

---

# 22. Message Queues

Examples:

- Kafka
- RabbitMQ
- AWS SQS

---

Use Cases:

- Background jobs
- Event processing
- Notifications

---

# 23. API Design Basics

Good APIs:

- RESTful
- Versioned
- Secure
- Idempotent

---

Example:

```
GET /users/{id}

POST /orders

DELETE /orders/{id}
```

---

# 24. System Design Interview Approach

Recommended flow:

```
1. Requirements

2. Scale Estimation

3. API Design

4. Database Design

5. High Level Architecture

6. Deep Dive

7. Bottlenecks

8. Trade-offs
```

---

# 25. Common System Design Mistakes

Avoid:

❌ Jumping into architecture immediately

❌ Ignoring requirements

❌ No scalability discussion

❌ No failure handling

❌ No security discussion

❌ No trade-offs

❌ Overengineering

---

# Senior Interview Checklist

For every system design question discuss:

## Requirements

- Users
- Features
- Constraints

## Scale

- QPS
- Storage
- Bandwidth
- Latency

## Architecture

- Services
- Database
- Cache
- Queue

## Scalability

- Load balancing
- Replication
- Sharding

## Reliability

- Failover
- Monitoring
- Backup

## Security

- Authentication
- Authorization
- Encryption

## Trade-offs

- Consistency vs Availability
- Cost vs Performance

---

# Module Summary

- System design is about creating scalable, reliable, and maintainable systems.
- HLD focuses on architecture, services, databases, and communication patterns.
- LLD focuses on classes, interfaces, and object interactions.
- Requirements gathering is the first and most important step.
- Back-of-the-envelope estimation helps determine infrastructure needs.
- QPS, storage, bandwidth, and latency calculations guide architectural decisions.
- Senior engineers must balance scalability, reliability, performance, security, and cost while making design choices.