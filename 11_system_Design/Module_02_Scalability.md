# System Design Interview Master Guide

# Module 2: Scalability Fundamentals (Questions 26–40)

> **Interview Focus:** Senior engineers are expected to understand not only definitions but also **when to choose each architecture style, trade-offs, scalability limitations, and real-world production usage.**

---

# 1. Vertical Scaling vs Horizontal Scaling

## Vertical Scaling (Scale Up)

Increasing the resources of a single machine.

Example:

```
Before:

8 CPU
32 GB RAM


After:

64 CPU
256 GB RAM
```

---

## Advantages

- Simple implementation
- No application changes required
- Good for small systems

---

## Disadvantages

- Hardware limit exists
- Expensive
- Single point of failure

---

## Example

A PostgreSQL database server upgraded from:

```
16 cores → 64 cores
```

---

# Horizontal Scaling (Scale Out)

Adding more machines to handle load.

Example:

```
Before:

        Server


After:

Server 1
Server 2
Server 3
Server 4
```

---

## Advantages

- Unlimited scaling
- Better fault tolerance
- High availability

---

## Disadvantages

- More complexity
- Requires distributed design
- Data synchronization challenges

---

## Example

Netflix:

```
Millions of users

↓

Thousands of application servers
```

---

## Comparison

| Vertical Scaling | Horizontal Scaling |
|---|---|
| Bigger machine | More machines |
| Simple | Complex |
| Limited | Highly scalable |
| Expensive | Cost effective |
| Single failure point | Fault tolerant |

---

## Interview Answer

> Vertical scaling improves capacity of a single server, while horizontal scaling adds more servers. Large-scale distributed systems generally prefer horizontal scaling.

---

# 2. Stateless vs Stateful Services

## Stateless Service

A service that does not store user-specific session information locally.

Every request contains required information.

Example:

```
Request:

JWT Token
User ID
Payload


Server processes request
```

---

## Architecture

```
              Load Balancer

        ┌────────┼────────┐

        API     API      API

```

Any server can handle any request.

---

## Advantages

- Easy horizontal scaling
- Easy deployment
- Better fault tolerance

---

## Example

REST APIs

```
GET /users/123
Authorization: JWT
```

---

# Stateful Service

Service maintains internal state.

Example:

- WebSocket connection
- Gaming server
- Chat session

---

Architecture:

```
User

↓

Server A

(Session stored here)
```

---

## Problems

- Difficult scaling
- Session migration required
- Server failure loses state

---

## Solution

Externalize state:

```
Application

↓

Redis

↓

Database
```

---

## Comparison

| Stateless | Stateful |
|-|-|
| No local state | Stores state |
| Easy scaling | Hard scaling |
| Better availability | More complexity |
| REST APIs | WebSockets |

---

## Interview Answer

> Stateless services are preferred in distributed systems because they allow easy horizontal scaling. Stateful systems require careful state management.

---

# 3. Monolithic Architecture

## Definition

A single application containing all business functionality.

---

Architecture:

```
                Client

                  |

          Monolithic Application

                  |

              Database
```

---

Example Modules:

```
User Module

Payment Module

Order Module

Inventory Module
```

All inside one application.

---

## Advantages

- Simple development
- Easy deployment
- Easy debugging
- Lower operational cost

---

## Disadvantages

- Hard scaling
- Large codebase
- Tight coupling
- Slow releases

---

## When to Use

Good for:

- Startups
- Small teams
- MVP products

---

# 4. Microservices Architecture

## Definition

Application is divided into independently deployable services.

---

Architecture:

```
                  API Gateway

                      |

     --------------------------------

     User       Order       Payment

   Service    Service      Service

     |          |            |

   DB         DB           DB
```

---

## Characteristics

Each service:

- Owns business logic
- Owns database
- Deploys independently

---

## Advantages

### Independent Scaling

Example:

Payment traffic increases:

```
Scale Payment Service only
```

---

### Independent Deployment

Deploy one service without affecting others.

---

## Disadvantages

- Distributed complexity
- Network failures
- Data consistency challenges
- Monitoring complexity

---

## Interview Answer

> Microservices improve scalability and team independence by splitting applications into independently deployable services, but introduce distributed system complexity.

---

# 5. Service-Oriented Architecture (SOA)

## Definition

Architecture where applications communicate through reusable services.

---

Architecture:

```
Application

     |

Enterprise Service Bus

     |

Services
```

---

## SOA Characteristics

- Larger services
- Shared infrastructure
- Enterprise focused

---

## Microservices vs SOA

| SOA | Microservices |
|-|-|
| Large services | Small services |
| Enterprise systems | Cloud applications |
| ESB communication | Lightweight APIs |
| Shared database possible | Independent databases |

---

# 6. Event-Driven Architecture

## Definition

Architecture where components communicate using events.

---

Example:

User places order.

```
Order Created Event

          |

      Kafka Topic

          |

--------------------------------

Payment Service

Inventory Service

Notification Service
```

---

## Components

### Producer

Creates events.

Example:

Order Service

---

### Broker

Stores and distributes events.

Examples:

- Kafka
- RabbitMQ

---

### Consumer

Processes events.

Example:

Email Service

---

## Advantages

- Loose coupling
- High scalability
- Async processing

---

## Disadvantages

- Event ordering
- Debugging complexity
- Event consistency

---

## Use Cases

- Banking
- E-commerce
- Notifications
- Analytics

---

# 7. Serverless Architecture

## Definition

Cloud provider manages infrastructure.

Examples:

- AWS Lambda
- Azure Functions
- Google Cloud Functions

---

Architecture:

```
Request

↓

API Gateway

↓

Lambda Function

↓

Database
```

---

## Advantages

- No server management
- Automatic scaling
- Pay per execution

---

## Disadvantages

- Cold start
- Execution limits
- Vendor lock-in

---

## Best Use Cases

- APIs
- Event processing
- Scheduled jobs

---

# 8. Modular Monolith

## Definition

A monolith organized into independent modules.

---

Example:

```
Application

 |
 |-- User Module

 |-- Payment Module

 |-- Order Module

 |-- Inventory Module

```

---

## Difference from Traditional Monolith

Traditional:

```
Everything tightly coupled
```

Modular:

```
Clear boundaries
```

---

## Advantages

- Easier than microservices
- Good maintainability
- Lower operational complexity

---

## Migration Path

Many companies follow:

```
Monolith

↓

Modular Monolith

↓

Microservices
```

---

# 9. Distributed Systems

## Definition

A system where multiple computers work together as one system.

---

Examples:

- Google Search
- Netflix
- Amazon

---

Architecture:

```
Machine 1

Machine 2

Machine 3

Machine 4
```

---

## Challenges

### Network Failure

Messages can fail.

---

### Data Consistency

Different nodes may have different data.

---

### Latency

Network communication is slower.

---

### Partial Failure

One component can fail while others work.

---

## Distributed System Principles

- Replication
- Partitioning
- Consensus
- Fault tolerance

---

# 10. CAP Theorem

## Definition

Distributed databases cannot guarantee all three simultaneously:

```
C - Consistency

A - Availability

P - Partition Tolerance
```

---

# Consistency

Every read receives the latest write.

Example:

Bank balance.

---

# Availability

Every request receives a response.

Example:

Social media feed.

---

# Partition Tolerance

System continues working despite network failures.

---

## CAP Trade-offs

### CP System

Consistency + Partition tolerance

Examples:

- MongoDB (configured)
- HBase

---

### AP System

Availability + Partition tolerance

Examples:

- Cassandra
- DynamoDB

---

## Interview Answer

> Since network partitions are unavoidable in distributed systems, real systems usually choose between consistency and availability during failures.

---

# 11. PACELC Theorem

CAP only considers partition failures.

PACELC adds normal operation trade-offs.

---

Meaning:

```
If Partition:

Choose Availability or Consistency


Else:

Choose Latency or Consistency
```

---

## Example

During normal operation:

```
Low latency

vs

Strong consistency
```

---

## Real Example

DynamoDB:

- Prefers availability
- Low latency

---

# 12. Consistency Models

## Strong Consistency

Every read gets latest value.

Example:

Bank transaction.

---

## Eventual Consistency

Data becomes consistent eventually.

Example:

Instagram likes.

---

## Causal Consistency

Related operations maintain ordering.

Example:

Message replies.

---

## Sequential Consistency

Operations appear in global order.

---

## Comparison

| Model | Example |
|-|-|
| Strong | Banking |
| Eventual | Social media |
| Causal | Messaging |
| Sequential | Distributed systems |

---

# 13. Availability

## Definition

Percentage of time system is operational.

---

Formula:

```
Availability =

Uptime /

(Uptime + Downtime)
```

---

## SLA Examples

### 99%

3.65 days downtime/year


### 99.9%

8.76 hours/year


### 99.99%

52 minutes/year

---

## Improving Availability

- Replication
- Failover
- Load balancing
- Health checks

---

# 14. Reliability

## Definition

Ability to perform correctly over time.

---

Metrics:

### MTBF

Mean Time Between Failures

---

### MTTR

Mean Time To Recovery

---

Improve reliability:

- Testing
- Monitoring
- Backup
- Disaster recovery

---

# 15. Fault Tolerance

## Definition

Ability to continue working despite failures.

---

Example:

Database primary fails.

System switches:

```
Primary DB

      X

Replica DB

      ↓

New Primary
```

---

## Techniques

### Replication

Multiple copies.

---

### Failover

Automatic recovery.

---

### Retry Mechanism

Handle temporary failures.

---

### Circuit Breaker

Prevent cascading failures.

---

### Graceful Degradation

Provide limited functionality.

---

# Senior Interview Architecture Decision Guide

| Requirement | Recommended Choice |
|---|---|
| Small application | Monolith |
| Growing application | Modular Monolith |
| Large enterprise | Microservices |
| Async workflows | Event-driven |
| Simple APIs | Stateless services |
| Real-time communication | Stateful services |
| Variable traffic | Serverless |
| Massive scale | Distributed architecture |

---

# Module Summary

- Vertical scaling increases server power; horizontal scaling increases server count.
- Stateless services are preferred for scalable APIs.
- Monoliths are simple but become difficult to scale.
- Microservices provide independent scaling but introduce distributed complexity.
- Event-driven systems enable asynchronous and loosely coupled architectures.
- Serverless removes infrastructure management but has limitations.
- Modular monoliths provide a middle ground between monolith and microservices.
- Distributed systems require careful handling of consistency, availability, and failures.
- CAP and PACELC explain trade-offs in distributed database design.
- Availability, reliability, and fault tolerance are key goals of production-grade systems.