# System Design Interview Master Guide

# Module 2: Building Blocks of Distributed Systems (26–70)

# Topic: Load Balancing (26–35)

> **Interview Focus:** Load balancing is one of the most important concepts in distributed systems. Senior engineers should understand algorithms, architecture, failure handling, scalability, and real-world implementation.

---

# 26. What is a Load Balancer?

## Definition

A **Load Balancer** distributes incoming client requests across multiple backend servers to improve:

- Scalability
- Availability
- Reliability
- Performance

---

## Without Load Balancer

```
             Users

               |

          Application Server

               |

           Database
```

Problem:

- Single point of failure
- Cannot handle high traffic
- Difficult scaling

---

## With Load Balancer

```
                Users

                  |

            Load Balancer

                  |

       ----------------------

       |          |          |

    Server A   Server B   Server C

```

---

## Responsibilities of Load Balancer

### 1. Traffic Distribution

Routes requests to healthy servers.

---

### 2. Health Monitoring

Detects failed servers.

---

### 3. Failover

Removes unhealthy servers.

---

### 4. SSL Termination

Handles HTTPS encryption/decryption.

---

### 5. Routing

Routes requests based on:

- URL
- Headers
- Cookies
- IP

---

## Real-World Examples

- AWS Elastic Load Balancer
- NGINX
- HAProxy
- Google Cloud Load Balancer
- Azure Load Balancer

---

# 27. Why Do We Need Load Balancing?

## Problem: Single Server

Example:

```
100,000 requests/sec

        |

     One Server

```

Server overloads.

---

## Solution

Distribute traffic:

```
100,000 requests/sec


        |

   Load Balancer


        |

 ---------------------

 |        |           |

Server1 Server2 Server3

```

---

## Benefits

### Scalability

Add more servers.

---

### High Availability

Server failures do not stop service.

---

### Better Performance

Reduces server load.

---

### Maintenance

Servers can be removed without downtime.

---

# 28. Layer 4 vs Layer 7 Load Balancer

Load balancers operate at different network layers.

---

# Layer 4 Load Balancer

Works at:

```
Transport Layer
(TCP/UDP)
```

---

Uses:

- IP address
- Port number

Example:

```
192.168.1.10:443
```

---

Architecture:

```
Client

 |

L4 Load Balancer

 |

Backend Servers

```

---

## Advantages

- Very fast
- Low latency
- Handles millions of connections

---

## Disadvantages

Cannot inspect:

- HTTP path
- Headers
- Cookies

---

## Example

TCP traffic distribution.

---

# Layer 7 Load Balancer

Works at:

```
Application Layer
(HTTP/HTTPS)
```

---

Understands:

- URL
- Headers
- Cookies
- HTTP methods

---

Example:

```
GET /users

↓

User Service


GET /payments

↓

Payment Service
```

---

## Advantages

- Smart routing
- Content-based routing
- Authentication support

---

## Disadvantages

- More processing overhead

---

# L4 vs L7 Comparison

| Feature | L4 | L7 |
|-|-|-|
| Layer | Transport | Application |
| Protocol | TCP/UDP | HTTP/HTTPS |
| Routing | IP + Port | URL + Headers |
| Speed | Faster | Slower |
| Intelligence | Low | High |
| SSL Handling | Limited | Yes |

---

## Interview Answer

> L4 load balancers operate at the TCP layer and provide high-performance traffic distribution, while L7 load balancers understand application-level information and enable intelligent routing.

---

# 29. Round Robin Algorithm

## Definition

Requests are distributed sequentially among servers.

---

Example:

Servers:

```
A
B
C
```

Requests:

```
Request 1 → A

Request 2 → B

Request 3 → C

Request 4 → A

```

---

## Implementation

```
index = (index + 1) % servers
```

---

## Advantages

- Simple
- Fast
- No server monitoring required

---

## Disadvantages

Assumes all servers have equal capacity.

---

## Problem Example

Server A:

```
16 CPU
```

Server B:

```
4 CPU
```

Both receive same traffic.

---

# 30. Weighted Round Robin

## Definition

Servers receive traffic based on capacity.

---

Example:

Servers:

```
Server A
Weight = 3


Server B
Weight = 1
```

Traffic:

```
A receives 75%

B receives 25%

```

---

## Use Case

Different server capacities.

---

Example:

```
Large Instance

+

Small Instance
```

---

## Advantages

- Better resource utilization
- Supports heterogeneous servers

---

# 31. Least Connection Algorithm

## Definition

Routes traffic to the server having the fewest active connections.

---

Example:

Current connections:

```
Server A = 100

Server B = 30

Server C = 50

```

New request:

```
Go to Server B
```

---

## Advantages

Works well when:

- Requests have different durations
- Long-running connections exist

---

## Example

WebSocket servers.

---

## Disadvantage

Requires tracking connection count.

---

# 32. Consistent Hashing

## Problem

Traditional hashing:

```
server = hash(user_id) % number_of_servers
```

Problem:

Adding/removing servers changes mapping.

---

Example:

Before:

```
hash(user1)%3 = Server A
```

After adding server:

```
hash(user1)%4 = Server C
```

Many users move.

---

# Consistent Hashing Solution

Maps servers and keys on a virtual ring.

```
              Server A


       Key1          Key2


Server B                 Server C

```

---

## Benefits

Only a small portion of data moves.

---

## Used In

- Redis Cluster
- Cassandra
- Distributed caches
- CDN

---

## Interview Answer

> Consistent hashing minimizes redistribution of keys when servers are added or removed, making it suitable for distributed systems.

---

# 33. Health Checks

## Definition

Mechanism to determine whether servers are healthy.

---

## Types

## 1. Active Health Check

Load balancer periodically sends requests.

Example:

```
GET /health

Response:

200 OK
```

---

## 2. Passive Health Check

Monitors real traffic failures.

Example:

```
500 errors increase

↓

Remove server
```

---

## Health Check Endpoint

Example:

```
GET /health

Response:

{
 status:"healthy",
 database:"connected",
 cache:"available"
}

```

---

## Importance

Prevents routing traffic to failed servers.

---

# 34. Session Affinity (Sticky Sessions)

## Definition

Routes the same user to the same server.

---

Example:

```
User A

   |

Server 1


User A again

   |

Server 1

```

---

## Why Needed?

Stateful applications.

Example:

Session stored in memory.

---

## Implementation

### Cookie Based

```
SESSION_ID=SERVER1
```

---

### IP Hash

Same client IP → Same server

---

## Problems

- Uneven traffic
- Difficult scaling
- Server failure loses session

---

## Better Approach

Store session externally:

```
Application

      |

    Redis

      |

Session Data

```

---

# 35. Global Load Balancing

## Definition

Distributes traffic across multiple geographic regions.

---

Example:

```
User India

    |

India Region


User USA

    |

US Region

```

---

## Architecture

```
                 Users

                   |

        Global Load Balancer

          /              \

     US Region       Asia Region

```

---

## Routing Strategies

### Geo Routing

Nearest region.

---

### Latency Based Routing

Lowest network latency.

---

### Failover Routing

Backup region.

---

## Examples

- AWS Route53
- Cloudflare Load Balancing
- Google Cloud Load Balancer

---

# 36. Load Balancer Failure Handling

## Problem

Load balancer itself can fail.

It becomes:

```
Single Point Of Failure

```

---

# Solution 1: Multiple Load Balancers

```
             Users

                |

        ----------------

        LB1          LB2

        ----------------

             Servers

```

---

# Solution 2: DNS Failover

DNS points users to healthy load balancer.

---

# Solution 3: Anycast Networking

Same IP advertised globally.

---

## Failure Detection

Methods:

- Health checks
- Heartbeat monitoring
- Automatic failover

---

# Production Load Balancer Architecture

```
                         Users

                           |

                 Global Load Balancer

                           |

                 Regional Load Balancer

                           |

              --------------------------

              |            |           |

          Service A    Service B   Service C

              |

          Databases / Cache

```

---

# Real-World Examples

## Netflix

Uses:

- Global traffic routing
- Multiple regions
- Automatic failover

---

## Amazon

Uses:

- Elastic Load Balancing
- Auto Scaling Groups

---

## Uber

Uses:

- Regional routing
- Service-level load balancing

---

# Senior Interview Discussion Points

## Question:

"How would you design a system handling 1 billion requests/day?"

Answer:

```
Users

↓

Global Load Balancer

↓

Regional Load Balancer

↓

API Gateway

↓

Multiple Stateless Services

↓

Cache

↓

Database

```

---

# Load Balancer Design Checklist

Discuss:

✅ Traffic distribution algorithm

✅ Health checks

✅ Failover strategy

✅ Session handling

✅ SSL termination

✅ Auto scaling

✅ Geographic routing

✅ Monitoring

✅ Logging

---

# Module Summary

- Load balancers distribute traffic across multiple servers.
- Layer 4 works at TCP level and provides high performance.
- Layer 7 works at HTTP level and enables intelligent routing.
- Round Robin is simple but ignores server capacity.
- Weighted Round Robin handles different server capacities.
- Least Connection works well for long-running requests.
- Consistent hashing minimizes data movement in distributed systems.
- Health checks prevent routing traffic to unhealthy servers.
- Stateless systems avoid sticky sessions by storing state externally.
- Global load balancing enables multi-region high availability.
- Load balancers require redundancy to avoid becoming a single point of failure.