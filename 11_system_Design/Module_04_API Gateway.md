# System Design Interview Master Guide

# Module 2: Building Blocks of Distributed Systems

# Topic: API Gateway (Questions 36–45)

> **Interview Focus:** API Gateway is a critical component in modern distributed architectures. Senior engineers should understand its role, responsibilities, failure handling, security, and difference from load balancers.

---

# 36. What is API Gateway?

## Definition

An **API Gateway** is a single entry point for clients to access backend services in a distributed system.

It sits between:

```
Client

   |

API Gateway

   |

Microservices
```

---

## Without API Gateway

Client directly communicates with multiple services:

```
             Mobile App

          /      |       \

     User     Order    Payment

    Service  Service   Service
```

Problems:

- Client knows internal architecture
- Authentication duplicated
- Difficult API management
- More network calls

---

## With API Gateway

```
                  Client

                    |

              API Gateway

                    |

       ----------------------------

       |            |             |

    User        Order         Payment

   Service     Service       Service

```

---

## Real-World Examples

- Netflix API Gateway
- Amazon API Gateway
- Kong Gateway
- NGINX Gateway
- Apigee

---

# Why Do We Need API Gateway?

## 1. Single Entry Point

Clients interact with one endpoint.

Example:

```
https://api.company.com
```

instead of:

```
user.company.com

order.company.com

payment.company.com
```

---

## 2. Security Layer

Handles:

- Authentication
- Authorization
- Token validation

---

## 3. Traffic Management

Handles:

- Rate limiting
- Throttling
- Routing

---

## 4. Protocol Transformation

Example:

Client:

```
REST API
```

Backend:

```
gRPC
```

Gateway converts between them.

---

# 37. API Gateway Responsibilities

A production API Gateway performs:

---

# 1. Request Routing

Routes requests to correct services.

Example:

```
GET /users/10

        |

        Gateway

        |

   User Service
```

---

Example:

```
/api/users/*

        ↓

User Service


/api/orders/*

        ↓

Order Service

```

---

# 2. Authentication

Validates:

- JWT tokens
- OAuth tokens
- API keys
- Session cookies

---

# 3. Authorization

Checks:

- User permissions
- Roles
- Resource access

Example:

```
Admin → Delete User

User → View Profile

```

---

# 4. Rate Limiting

Controls request volume.

Example:

```
1000 requests/minute/user
```

---

# 5. Request Transformation

Example:

Incoming:

```
{
"name":"Monika"
}
```

Converted to backend format.

---

# 6. Response Aggregation

Combines multiple service responses.

Example:

Mobile app dashboard:

```
User Profile

+

Orders

+

Recommendations
```

Gateway calls:

```
User Service

Order Service

Recommendation Service
```

and returns one response.

---

# 7. Logging and Monitoring

Tracks:

- Request latency
- Errors
- Traffic
- User activity

---

# 38. Authentication at Gateway

## Why Authenticate at Gateway?

Without gateway:

Every service implements:

```
JWT Validation

OAuth

Token Checking

```

Duplicate logic.

---

With gateway:

```
Client

 |

JWT Token

 |

API Gateway

 |

Validate Token

 |

Microservices

```

---

## Authentication Flow

```
1. User logs in

        |

2. Identity Provider generates JWT

        |

3. Client sends JWT

        |

4. Gateway validates token

        |

5. Request forwarded

```

---

## Example JWT Validation

Gateway checks:

```
Signature

Expiry

Issuer

Audience

Claims

```

---

## Benefits

- Centralized security
- Less duplication
- Consistent policies

---

# 39. Rate Limiting

## Definition

Restricting the number of requests a client can make.

---

## Why Rate Limit?

Protect from:

- Abuse
- DDoS attacks
- Resource exhaustion

---

# Common Algorithms

---

# 1. Fixed Window

Example:

```
100 requests/minute
```

Counter resets every minute.

---

Problem:

Traffic spike at boundary.

---

# 2. Sliding Window

More accurate.

Tracks requests over moving time window.

---

# 3. Token Bucket

Most commonly used.

---

Example:

Bucket:

```
100 tokens

```

Each request consumes:

```
1 token
```

Tokens refill over time.

---

## Architecture

```
Client

 |

Gateway

 |

Redis Counter

 |

Backend Service

```

---

## Interview Answer

> Rate limiting is usually implemented at the gateway using algorithms like token bucket with distributed counters stored in Redis.

---

# 40. Request Routing

## Definition

Forwarding requests to appropriate backend services.

---

## Types

---

## Path Based Routing

Example:

```
/users

↓

User Service


/orders

↓

Order Service
```

---

## Host Based Routing

Example:

```
api.user.com

↓

User Service


api.payment.com

↓

Payment Service

```

---

## Header Based Routing

Example:

```
Version: v2

↓

New Service

```

---

## Load Balancing

Gateway can distribute requests:

```
Gateway

 |

----------------

Server 1

Server 2

Server 3

```

---

# 41. API Versioning

## Why Version APIs?

APIs evolve.

Old clients may break if response changes.

---

# Method 1: URL Versioning

Example:

```
GET /api/v1/users

GET /api/v2/users

```

---

# Method 2: Header Versioning

Example:

```
Accept-Version: v2
```

---

# Method 3: Query Parameter

Example:

```
/users?version=2
```

---

## Best Practice

Support old versions during migration.

Example:

```
v1 → deprecated

v2 → active

```

---

# 42. Circuit Breaker

## Problem

Failure propagation.

Example:

```
Payment Service Down


Order Service keeps calling


System overloads

```

---

## Circuit Breaker Solution

Stops repeated failures.

---

## States

```
          Failure

CLOSED -------------> OPEN


          Timeout

OPEN -------------> HALF OPEN


Success

HALF OPEN ---------> CLOSED

```

---

# Closed State

Normal traffic.

---

# Open State

Requests blocked.

---

# Half Open State

Test requests allowed.

---

## Example

Netflix Hystrix pattern.

---

## Benefits

- Prevent cascading failures
- Improve resilience
- Faster recovery

---

# 43. Retry Strategy

## Purpose

Handle temporary failures.

Examples:

- Network timeout
- Temporary service unavailable

---

## Retry Example

```
Attempt 1

Failure

Wait 100ms


Attempt 2

Failure

Wait 500ms


Attempt 3

Success

```

---

# Retry Strategies

## Fixed Retry

Same delay.

```
1 sec
1 sec
1 sec

```

---

## Exponential Backoff

Delay increases:

```
1s

2s

4s

8s

```

---

## Add Jitter

Random delay prevents synchronized retries.

---

## Avoid Retry For

Permanent failures:

```
400 Bad Request

Invalid Authentication

```

---

# 44. Timeout Handling

## Why Timeout?

Avoid waiting forever.

---

Example:

```
Order Service

waiting

Payment Service

(no response)

```

---

## Timeout Types

---

## Connection Timeout

Time to establish connection.

---

## Read Timeout

Time waiting for response.

---

## Request Timeout

Maximum request duration.

---

## Best Practice

Every external call should have timeout.

---

Example:

```
Payment API timeout:

3 seconds

```

---

# 45. API Gateway vs Load Balancer

| Feature | API Gateway | Load Balancer |
|-|-|-|
| Purpose | API management | Traffic distribution |
| Layer | Usually L7 | L4/L7 |
| Authentication | Yes | Limited |
| Rate Limiting | Yes | Limited |
| Routing | Business routing | Server routing |
| Transformation | Yes | No |
| API Versioning | Yes | No |
| Security Policies | Yes | Limited |

---

# Architecture Comparison

## Load Balancer

```
Users

 |

Load Balancer

 |

Application Servers

```

Purpose:

Distribute traffic.

---

## API Gateway

```
Users

 |

API Gateway

 |

Microservices

```

Purpose:

Manage APIs.

---

# Real Production Architecture

```
                    Users

                      |

              Global Load Balancer

                      |

                API Gateway

                      |

        --------------------------------

        |              |               |

    User Service   Order Service   Payment Service

        |              |               |

       DB             DB              DB

```

---

# Senior Interview Scenario

## Question:

"Design an API layer for millions of users."

Answer:

```
Client

↓

CDN

↓

Load Balancer

↓

API Gateway

↓

Authentication

↓

Rate Limiting

↓

Circuit Breaker

↓

Microservices

↓

Database + Cache

```

---

# Production Best Practices

## Security

- JWT validation
- OAuth2
- TLS termination
- API keys

---

## Performance

- Response caching
- Compression
- Connection pooling

---

## Reliability

- Circuit breaker
- Retry with backoff
- Timeout handling

---

## Observability

Monitor:

- Request count
- Latency
- Error rate
- Service health

---

# Module Summary

- API Gateway provides a single entry point for distributed systems.
- It handles authentication, routing, rate limiting, versioning, and security policies.
- Load balancers distribute traffic, while API gateways manage APIs.
- Authentication is commonly centralized at the gateway using JWT/OAuth.
- Rate limiting protects systems from abuse and overload.
- Circuit breakers prevent cascading failures.
- Retry strategies handle temporary failures using exponential backoff.
- Timeouts prevent resource exhaustion.
- A production architecture usually combines Global Load Balancer + API Gateway + Microservices.