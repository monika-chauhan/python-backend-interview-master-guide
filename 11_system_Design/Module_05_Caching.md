# System Design Interview Master Guide

# Module 2: Building Blocks of Distributed Systems

# Topic: Caching (Questions 46–65)

> **Interview Focus:** Caching is one of the most important scalability techniques. Senior engineers should understand cache architecture, consistency challenges, Redis internals, failure scenarios, and production optimization.

---

# 46. Why Caching?

## Definition

Caching stores frequently accessed data in a faster storage layer to reduce:

- Latency
- Database load
- Infrastructure cost

---

## Without Cache

```
Client

 |

Application

 |

Database

```

Every request hits database.

Problems:

- High latency
- Database overload
- Expensive queries

---

## With Cache

```
Client

 |

Application

 |

Cache

 |

Database

```

Flow:

1. Check cache
2. Return data if available
3. Query database if missing

---

## Benefits of Caching

## 1. Lower Latency

Memory access:

```
Redis:
~1ms

Database:
10-100ms
```

---

## 2. Reduce Database Load

Example:

100,000 requests/sec

Without cache:

```
100,000 DB queries
```

With cache:

```
95,000 cache hits

5,000 DB queries
```

---

## 3. Improve Scalability

Allows system to handle more traffic.

---

## 4. Better User Experience

Faster response time.

---

# 47. Cache Levels

Caching can exist at multiple layers.

---

# Level 1: Client-Side Cache

Stored on user device.

Examples:

- Browser cache
- Mobile app cache
- Local storage

---

Architecture:

```
User Browser

      |

 Local Cache

      |

Backend API

```

---

## Advantages

- Very fast
- Reduces network calls

---

## Disadvantages

- Difficult invalidation
- Data may become stale

---

# Level 2: CDN Cache

Content Delivery Network caches static content near users.

Examples:

- Images
- Videos
- CSS
- JavaScript

---

Architecture:

```
User India

    |

 CDN Edge Server

    |

Origin Server

```

---

Benefits:

- Lower latency
- Reduces server load

---

Example:

Netflix uses CDN for video delivery.

---

# Level 3: Application Cache

Cache inside application layer.

Examples:

- Redis
- Memcached

---

Architecture:

```
Application

     |

 Redis Cache

     |

Database

```

---

Used for:

- Sessions
- User profiles
- API responses

---

# Level 4: Database Cache

Database internally caches frequently accessed pages.

Examples:

- PostgreSQL shared buffers
- MySQL buffer pool

---

Architecture:

```
Application

     |

Database Cache

     |

Disk Storage

```

---

# Cache Hierarchy

```
Fastest

↓

CPU Cache

↓

Browser Cache

↓

CDN

↓

Redis

↓

Database Cache

↓

Disk

Slowest

```

---

# 48. Redis Architecture

## What is Redis?

Redis is an in-memory distributed key-value database.

---

Architecture:

```
Application

      |

 Redis Server

      |

 Memory

      |

 Persistence Layer

```

---

## Data Model

Example:

```
Key:

user:1001


Value:

{
"name":"Monika",
"age":25
}

```

---

## Redis Data Structures

Supports:

### String

```
SET name Monika
```

---

### Hash

```
user:1

name
email
age

```

---

### List

Queues.

---

### Set

Unique values.

---

### Sorted Set

Leaderboards.

---

## Common Uses

- Cache
- Session storage
- Rate limiting
- Pub/Sub
- Distributed locks

---

# 49. Cache Aside Pattern

Also called:

**Lazy Loading Cache**

---

## Flow

```
Application

     |

Check Cache

     |

Cache Hit?

   /      \

 Yes       No

 |          |

Return    Query DB

             |

        Update Cache

```

---

## Example

Request:

```
GET user/10
```

---

Step 1:

Check Redis:

```
user:10

```

---

Step 2:

If missing:

Query DB.

---

Step 3:

Store result:

```
Redis.set(user:10)

```

---

## Advantages

- Simple
- Application controls caching

---

## Disadvantages

- Cache miss penalty
- Possible stale data

---

## Most Common Production Pattern

Used by:

- Netflix
- Amazon
- Uber

---

# 50. Write Through Cache

## Definition

Data is written to cache and database together.

---

Flow:

```
Application

     |

 Cache

     |

Database

```

---

Example:

User updates profile.

```
Update Redis

+

Update Database

```

---

## Advantages

- Cache always fresh
- No cache miss after writes

---

## Disadvantages

- Higher write latency
- More complex

---

# 51. Write Behind Cache

Also called:

Write Back Cache

---

## Flow

```
Application

     |

Cache

     |

Async Worker

     |

Database

```

---

Example:

User likes counter:

```
Redis:

likes = 1,000,000


Later:

Batch update DB

```

---

## Advantages

- Very fast writes
- Reduced DB load

---

## Disadvantages

Risk of data loss.

---

## Use Cases

- Analytics
- Counters
- Logging

---

# 52. Cache Invalidation

## Definition

Removing or updating stale cache data.

---

## Problem

Database:

```
User Name = John

```

Cache:

```
User Name = Mike

```

Cache is stale.

---

# Strategies

## 1. TTL Expiration

Example:

```
Cache expires after 10 minutes

```

---

## 2. Explicit Delete

After update:

```
UPDATE database

DELETE cache

```

---

## 3. Update Cache

Write new value directly.

---

## Interview Quote

> Cache invalidation is one of the hardest problems in distributed systems.

---

# 53. Cache Stampede

## Definition

Many requests hit cache simultaneously after expiration.

---

Example:

Popular product:

```
product:100

TTL expires

```

Suddenly:

```
10,000 users request

```

All hit database.

---

## Solution

### Locking

Only one request refreshes cache.

---

### Early Refresh

Refresh before expiration.

---

### Random TTL

Avoid simultaneous expiration.

---

# 54. Cache Penetration

## Definition

Requests for data that does not exist.

---

Example:

User requests:

```
user:999999999

```

Database:

```
No record

```

Every request hits DB.

---

## Solutions

### Cache Empty Result

Store:

```
user:999999999 = NULL

TTL = 5 min

```

---

### Bloom Filter

Check existence before DB.

---

# 55. Cache Avalanche

## Definition

Many cache entries expire at the same time.

---

Example:

```
1 Million products

TTL = 1 hour

```

After 1 hour:

All expire.

---

Result:

Database overload.

---

## Solutions

- Random TTL
- Cache warming
- Stagger expiration

---

# 56. Distributed Cache

## Definition

Cache shared across multiple application servers.

---

Architecture:

```
        Server 1
           |
        Server 2
           |
        Server 3

           |

       Redis Cluster

```

---

## Why Needed?

Because local cache is not shared.

---

Example:

Server A:

```
user:1 exists

```

Server B:

```
user:1 missing

```

---

Distributed cache solves this.

---

# 57. Redis Cluster

## Definition

Redis Cluster provides horizontal scaling.

---

Architecture:

```
Redis Node 1

Redis Node 2

Redis Node 3

```

---

## Data Distribution

Uses hash slots.

Total:

```
16384 slots
```

---

Example:

```
user:1

hash(key)

↓

slot 5000

↓

Node 2

```

---

## Benefits

- Scalability
- High availability
- Automatic partitioning

---

# 58. Redis Persistence

Redis stores data in memory.

Persistence protects against restart.

---

## Method 1: RDB

Redis Database Snapshot.

Example:

```
Every 5 minutes

Create snapshot

```

---

Advantages:

- Faster recovery

Disadvantages:

- Possible data loss

---

## Method 2: AOF

Append Only File.

Stores every write.

Example:

```
SET user:1 John

SET user:2 Mike

```

---

Advantages:

- Better durability

Disadvantages:

- Larger files

---

## Comparison

| RDB | AOF |
|-|-|
| Snapshot | Every write |
| Faster | Safer |
| Less storage | More storage |

---

# 59. Hot Key Problem

## Definition

A single cache key receives massive traffic.

---

Example:

```
product:iphone

10 million users

```

---

Problem:

One Redis node overloaded.

---

## Solutions

### Key Replication

Create copies:

```
product:iphone:1

product:iphone:2

```

---

### Local Cache

Keep popular data locally.

---

### Request Distribution

Spread traffic.

---

# 60. Cache Eviction Policies

When memory is full, Redis removes keys.

---

## LRU

Least Recently Used.

Removes least accessed data.

---

## LFU

Least Frequently Used.

Removes least frequently accessed.

---

## TTL Based

Remove expired keys.

---

## Redis Policies

Examples:

```
allkeys-lru

volatile-lru

allkeys-lfu

noeviction

```

---

# 61. LRU Cache Design

## Requirement

Implement cache with:

- O(1) get
- O(1) put

---

## Data Structures

Use:

1. HashMap
2. Doubly Linked List

---

Architecture:

```
HashMap

Key → Node


Doubly Linked List

Most Recent

     |

Least Recent

```

---

## Operations

## GET

1. Find node in hashmap
2. Move node to front

Complexity:

```
O(1)
```

---

## PUT

1. Insert/update node
2. Remove least recently used if full

Complexity:

```
O(1)
```

---

## Python Example

```python
from collections import OrderedDict


class LRUCache:

    def __init__(self, capacity):
        self.cache = OrderedDict()
        self.capacity = capacity


    def get(self, key):
        if key not in self.cache:
            return -1

        self.cache.move_to_end(key)

        return self.cache[key]


    def put(self, key, value):

        self.cache[key] = value

        self.cache.move_to_end(key)

        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)
```

---

# Production Caching Architecture

```
                 Users

                    |

                 CDN Cache

                    |

              Load Balancer

                    |

              Application

                    |

             Redis Cluster

                    |

              Database

```

---

# Senior Interview Discussion Points

## Question:

"How would you cache a product catalog for 100 million users?"

Answer:

```
CDN

+

Redis Distributed Cache

+

Cache Aside Pattern

+

TTL Expiration

+

Cache Warming

+

Hot Key Handling

+

Monitoring

```

---

# Caching Checklist

Discuss:

✅ Cache level selection

✅ Cache strategy

✅ TTL design

✅ Invalidation strategy

✅ Failure handling

✅ Hot key handling

✅ Cache consistency

✅ Monitoring

---

# Module Summary

- Caching reduces latency and database pressure.
- Cache exists at multiple levels: client, CDN, application, and database.
- Redis is the most common distributed application cache.
- Cache Aside is the most widely used production pattern.
- Write Through keeps cache and database synchronized.
- Write Behind improves write performance but risks data loss.
- Cache invalidation is a major distributed system challenge.
- Stampede, penetration, and avalanche are common cache failures.
- Redis Cluster provides distributed caching at scale.
- LRU, LFU, and TTL policies manage cache memory.
- LRU Cache design uses HashMap + Doubly Linked List for O(1) operations.