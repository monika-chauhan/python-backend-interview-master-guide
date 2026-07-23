# Django Interview Master Guide

# Module 9: Caching (Questions 131–145)

---

# 131. Why Caching?

## Definition

**Caching** is the process of storing frequently accessed data in a fast storage layer (memory) so that future requests can be served without repeatedly performing expensive operations such as database queries or API calls.

Instead of computing the same result multiple times, the application retrieves it from the cache.

---

## Without Cache

```
Client

↓

Django

↓

Database

↓

Response
```

Every request hits the database.

---

## With Cache

```
Client

↓

Django

↓

Redis Cache

↓

(Cache Hit?)

↓

Yes → Response

↓

No

↓

Database

↓

Store in Cache

↓

Response
```

---

## Benefits

- Faster response time
- Reduced database load
- Better scalability
- Lower infrastructure cost
- Improved user experience

---

## What Can Be Cached?

- Database queries
- API responses
- HTML pages
- Templates
- Computed values
- User sessions

---

## Interview Answer

> Caching stores frequently used data in memory to reduce database access and computation, significantly improving application performance and scalability.

---

# 132. Django Cache Framework

## Definition

Django provides a unified caching API that supports multiple cache backends.

---

## Supported Backends

- Local Memory
- Redis
- Memcached
- Database
- File System

---

## Configuration Example

```python
CACHES = {
    "default": {
        "BACKEND": "django.core.cache.backends.redis.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
    }
}
```

---

## Basic Usage

```python
from django.core.cache import cache

cache.set("name", "Alice", timeout=300)

value = cache.get("name")
```

---

## Advantages

- Backend-independent API
- Easy switching between cache providers
- Supports multiple cache aliases

---

## Interview Answer

> Django's Cache Framework provides a consistent API for interacting with different caching backends, allowing applications to switch cache providers with minimal code changes.

---

# 133. Local Memory Cache

## Definition

Stores cached data in the memory of the current Django process.

---

## Configuration

```python
CACHES = {
    "default": {
        "BACKEND": "django.core.cache.backends.locmem.LocMemCache",
    }
}
```

---

## Advantages

- Extremely fast
- No external service required
- Simple setup

---

## Disadvantages

- Not shared across processes
- Cache is lost on application restart
- Unsuitable for multi-server deployments

---

## Best Use Cases

- Development
- Single-process applications
- Temporary caching

---

## Interview Answer

> Local Memory Cache stores data in the application's memory, providing very fast access but lacking sharing across processes or servers.

---

# 134. Redis Cache

## Definition

Redis is an in-memory data store commonly used for distributed caching.

---

## Architecture

```
Client

↓

Django

↓

Redis

↓

Memory

↓

Response
```

---

## Configuration

```python
CACHES = {
    "default": {
        "BACKEND": "django.core.cache.backends.redis.RedisCache",
        "LOCATION": "redis://localhost:6379/1",
    }
}
```

---

## Features

- Extremely fast
- Distributed
- Persistent (optional)
- TTL support
- Pub/Sub
- Data structures

---

## Enterprise Uses

- API caching
- Session storage
- Rate limiting
- Celery broker
- Leaderboards

---

## Interview Answer

> Redis is a high-performance in-memory data store widely used for distributed caching, session storage, queues, and real-time data processing.

---

# 135. Memcached

## Definition

Memcached is a distributed in-memory key-value cache optimized for simple caching.

---

## Characteristics

- Memory only
- No persistence
- Simple key-value storage
- Very fast

---

## Configuration

```python
CACHES = {
    "default": {
        "BACKEND": "django.core.cache.backends.memcached.PyMemcacheCache",
        "LOCATION": "127.0.0.1:11211",
    }
}
```

---

## Redis vs Memcached

| Redis | Memcached |
|--------|------------|
| Persistent (optional) | Memory only |
| Rich data structures | Key-value only |
| Pub/Sub | No |
| Queues | No |
| More features | Simpler |

---

## Interview Answer

> Memcached is a lightweight distributed cache optimized for high-speed key-value lookups, whereas Redis offers richer data structures and additional capabilities.

---

# 136. Per View Cache

## Definition

Caches the entire response of a specific view.

---

## Example

```python
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)
def home(request):
    ...
```

---

## Flow

```
Request

↓

Cache

↓

Hit?

↓

Yes

↓

Return Cached Response

↓

No

↓

Execute View

↓

Cache Response

↓

Return
```

---

## Best For

- Home pages
- Product listings
- Read-heavy APIs

---

## Interview Answer

> Per-view caching stores the complete response of a view for a specified duration, reducing repeated processing for identical requests.

---

# 137. Template Cache

## Definition

Caches a section of a Django template.

---

## Example

```html
{% load cache %}

{% cache 600 sidebar %}
    ...
{% endcache %}
```

---

## Benefits

- Cache expensive template fragments
- Dynamic pages with static sections
- Reduce rendering time

---

## Interview Answer

> Template fragment caching stores reusable portions of templates, improving rendering performance while allowing the rest of the page to remain dynamic.

---

# 138. Low Level Cache

## Definition

Provides programmatic control over caching.

---

## Example

```python
from django.core.cache import cache

cache.set("users", users, 300)

users = cache.get("users")
```

---

## Additional Methods

```python
cache.delete(key)

cache.get_or_set(key, default)

cache.clear()
```

---

## Use Cases

- API responses
- Expensive computations
- Query results
- Configuration values

---

## Interview Answer

> The low-level cache API allows developers to cache arbitrary Python objects using methods such as `set()`, `get()`, and `delete()`.

---

# 139. Cache Invalidation

## Definition

Removing or updating stale cache entries.

---

## Why?

Without invalidation:

```
Database Updated

↓

Old Cache

↓

Incorrect Data
```

---

## Example

```python
cache.delete("users")
```

---

## Strategies

- Time-based expiration (TTL)
- Manual invalidation
- Event-driven invalidation
- Versioned keys

---

## Interview Answer

> Cache invalidation ensures cached data remains consistent with the underlying data source by removing or refreshing outdated entries.

---

# 140. Cache Stampede

## Definition

A cache stampede occurs when many requests simultaneously attempt to rebuild an expired cache entry.

---

## Problem

```
Cache Expires

↓

1000 Requests

↓

1000 Database Queries
```

---

## Solutions

- Distributed locks
- Stale-while-revalidate
- Early refresh
- Cache warming
- Randomized expiration times

---

## Interview Answer

> A cache stampede occurs when many requests regenerate the same expired cache simultaneously. It can be mitigated using locks, stale cache strategies, or proactive cache refreshes.

---

# 141. Distributed Cache

## Definition

A shared cache accessible by multiple application servers.

---

## Architecture

```
App Server 1

↓

Redis Cluster

↑

App Server 2

↑

App Server 3
```

---

## Advantages

- Shared cache
- Horizontal scalability
- Consistent cached data
- High availability

---

## Common Technologies

- Redis Cluster
- Memcached Cluster

---

## Interview Answer

> A distributed cache allows multiple application instances to share cached data, improving scalability and consistency in distributed systems.

---

# 142. Cache Warming

## Definition

Pre-populating cache before user requests.

---

## Example

```
Deployment

↓

Background Job

↓

Load Popular Products

↓

Store in Redis

↓

Ready for Users
```

---

## Benefits

- Fewer cold-cache misses
- Faster initial response
- Lower database spikes after deployment

---

## Interview Answer

> Cache warming proactively loads frequently accessed data into the cache before it is requested, reducing latency and database load after deployments or restarts.

---

# 143. Cache Aside Pattern

## Definition

The application manages the cache explicitly.

---

## Flow

```
Application

↓

Check Cache

↓

Hit?

↓

Yes

↓

Return

↓

No

↓

Database

↓

Store in Cache

↓

Return
```

---

## Example

```python
value = cache.get("user")

if value is None:
    value = User.objects.get(pk=1)
    cache.set("user", value, 300)
```

---

## Advantages

- Simple
- Flexible
- Most common caching pattern

---

## Interview Answer

> In the Cache-Aside pattern, the application first checks the cache and falls back to the database on a cache miss, updating the cache afterward.

---

# 144. Write Through Cache

## Definition

Writes are performed to the cache and the database together.

---

## Flow

```
Application

↓

Cache

↓

Database

↓

Success
```

---

## Advantages

- Cache always contains fresh data
- Simplifies read operations

---

## Disadvantages

- Higher write latency
- Extra work for data that may never be read

---

## Interview Answer

> In the Write-Through pattern, every write updates both the cache and the database simultaneously, keeping cached data synchronized.

---

# 145. Read Through Cache

## Definition

The cache itself retrieves data from the database on a cache miss.

---

## Flow

```
Application

↓

Cache

↓

Hit?

↓

Yes

↓

Return

↓

No

↓

Cache Loads Database

↓

Store

↓

Return
```

---

## Difference from Cache Aside

| Cache Aside | Read Through |
|--------------|--------------|
| Application loads missing data | Cache layer loads missing data |
| Simpler application logic | Requires cache infrastructure support |

---

## Use Cases

- Enterprise caching systems
- Managed cache services
- High-throughput applications

---

## Interview Answer

> In the Read-Through pattern, the cache automatically retrieves and stores data from the underlying data source when a requested item is missing, hiding cache management from the application.

---

# Module Summary

- Caching improves application performance by reducing database queries and repeated computations.
- Django's Cache Framework provides a unified API supporting Local Memory, Redis, Memcached, database, and file-based caches.
- Local Memory Cache is suitable for development and single-process deployments, while Redis and Memcached support distributed caching.
- Per-view, template fragment, and low-level caching address different caching requirements.
- Effective cache invalidation is essential for maintaining data consistency.
- Cache stampedes can be mitigated through locking, stale cache strategies, and proactive refreshes.
- Distributed caches enable multiple application instances to share cached data.
- Cache warming reduces cold-start latency after deployments.
- Cache-Aside, Write-Through, and Read-Through are common caching patterns used in enterprise systems depending on consistency, performance, and operational requirements.