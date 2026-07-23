# Generative AI Interview Master Guide

# Module 3: Vector Databases (Questions 51–75)

---

# 51. What is a Vector Database?

## Definition

A **Vector Database** is a specialized database designed to store, index, and search high-dimensional vector embeddings efficiently.

Instead of searching by exact values, it retrieves data based on **semantic similarity**.

---

## Traditional Database

```text
SELECT *
FROM users
WHERE id = 10;
```

Exact match.

---

## Vector Database

```text
Query

↓

Embedding

↓

Nearest Vectors

↓

Relevant Documents
```

---

## Applications

- RAG
- Semantic Search
- Recommendation Systems
- Image Search
- Chatbots
- Similarity Search

---

## Interview Answer

> A Vector Database stores embedding vectors and performs fast similarity search using Approximate Nearest Neighbor (ANN) algorithms, enabling semantic retrieval at scale.

---

# 52. Why FAISS?

## What is FAISS?

FAISS (**Facebook AI Similarity Search**) is an open-source library developed by Meta for efficient similarity search over millions or billions of vectors.

---

## Features

- Extremely fast
- GPU support
- Multiple ANN indexes
- Billion-scale search

---

## Advantages

- Open source
- High performance
- Research-friendly

---

## Limitations

- No built-in REST API
- No authentication
- No persistence management
- No distributed clustering

---

## Interview Answer

> FAISS is ideal for high-performance local vector search, but production systems often require additional infrastructure for persistence, replication, and distributed access.

---

# 53. Pinecone

## Overview

Managed cloud vector database.

---

## Features

- Fully managed
- Auto scaling
- Metadata filtering
- Serverless options
- High availability

---

## Architecture

```text
Application

↓

Embedding Model

↓

Pinecone

↓

Top-K Results
```

---

## Advantages

- Easy deployment
- No infrastructure management
- Production ready

---

## Drawbacks

- Paid service
- Vendor dependency

---

## Interview Answer

> Pinecone is a managed vector database that provides scalable semantic search without requiring infrastructure management.

---

# 54. Weaviate

## Overview

Open-source vector database with built-in AI capabilities.

---

## Features

- GraphQL API
- Hybrid Search
- Modules for LLM integration
- Horizontal scaling

---

## Advantages

- Rich ecosystem
- Schema support
- Strong filtering

---

## Interview Answer

> Weaviate combines vector search, metadata filtering, and AI modules, making it suitable for enterprise semantic search applications.

---

# 55. ChromaDB

## Overview

Lightweight vector database.

---

## Features

- Python-first
- Easy setup
- Ideal for RAG prototypes
- Local persistence

---

## Best For

- Development
- Small projects
- AI experiments

---

## Limitations

- Limited distributed capabilities

---

## Interview Answer

> ChromaDB is a simple vector database commonly used for local development and rapid RAG prototyping.

---

# 56. Milvus

## Overview

Distributed open-source vector database.

---

## Features

- Massive scalability
- GPU acceleration
- Multiple index types
- Kubernetes support

---

## Architecture

```text
Application

↓

Milvus Cluster

↓

Distributed Storage
```

---

## Interview Answer

> Milvus is designed for enterprise-scale vector search with distributed architecture, GPU support, and horizontal scalability.

---

# 57. Qdrant

## Overview

Modern vector search engine.

---

## Features

- Payload filtering
- HNSW indexing
- REST API
- High performance

---

## Advantages

- Easy deployment
- Production ready
- Good Rust implementation

---

## Interview Answer

> Qdrant provides efficient vector search with metadata filtering and HNSW indexing, making it popular for production AI applications.

---

# 58. pgvector

## Definition

PostgreSQL extension for vector search.

---

## Example

```sql
CREATE EXTENSION vector;
```

Store embedding

```sql
embedding VECTOR(1536)
```

Similarity

```sql
ORDER BY embedding <-> query_vector
```

---

## Advantages

- SQL support
- ACID
- Existing PostgreSQL ecosystem

---

## Limitations

- Less optimized than dedicated vector databases at very large scale

---

## Interview Answer

> pgvector enables vector search inside PostgreSQL, allowing structured data and embeddings to coexist in the same database.

---

# 59. HNSW (Hierarchical Navigable Small World)

## Definition

State-of-the-art Approximate Nearest Neighbor algorithm.

---

## Idea

```text
Top Layer

↓

Middle Layer

↓

Bottom Layer

↓

Nearest Neighbor
```

---

## Advantages

- Very fast
- High recall
- Excellent scalability

---

## Complexity

Search

```
O(log N)
```

Approximate.

---

## Interview Answer

> HNSW builds a multi-layer graph structure that enables extremely fast approximate nearest-neighbor searches with high accuracy.

---

# 60. IVF (Inverted File Index)

## Working

```
Vectors

↓

Clusters

↓

Search Relevant Cluster

↓

Nearest Neighbor
```

---

## Advantages

- Faster than brute force
- Reduced search space

---

## Drawbacks

May miss some neighbors.

---

## Interview Answer

> IVF partitions vectors into clusters and searches only relevant clusters, significantly reducing search time.

---

# 61. Product Quantization (PQ)

## Purpose

Compress vectors.

Example

```
1536 dimensions

↓

Compressed representation
```

---

## Benefits

- Lower memory
- Faster search
- Billion-scale indexing

---

## Trade-off

Slight accuracy reduction.

---

## Interview Answer

> Product Quantization compresses vectors into smaller codes, reducing memory usage while maintaining efficient approximate search.

---

# 62. ANN vs Exact Search

## Exact Search

```
Compare

Every Vector
```

Accuracy

100%

Slow.

---

## ANN

Search subset.

Accuracy

~95–99%

Much faster.

---

## Comparison

| ANN | Exact |
|------|-------|
| Fast | Slow |
| Approximate | Perfect |
| Production | Small datasets |

---

## Interview Answer

> ANN sacrifices a small amount of accuracy to achieve dramatic improvements in search speed, making it the standard for production vector search.

---

# 63. Index Building

## Process

```text
Documents

↓

Embeddings

↓

Index Creation

↓

Persist
```

---

## Considerations

- Index type
- Build time
- Memory
- Update frequency

---

## Interview Answer

> Index building organizes embedding vectors into optimized data structures for efficient nearest-neighbor retrieval.

---

# 64. Metadata Filtering

Example

```
Department

=

Finance

AND

Year

=

2025
```

Applied before or alongside vector search.

---

## Benefits

- Better precision
- Lower latency

---

## Interview Answer

> Metadata filtering narrows candidate documents before similarity search, improving efficiency and relevance.

---

# 65. Sharding

Split vectors across multiple nodes.

```text
Shard 1

↓

Shard 2

↓

Shard 3
```

---

## Benefits

- Horizontal scaling
- Higher throughput

---

## Interview Answer

> Sharding distributes vector data across multiple machines to support large datasets and increased query throughput.

---

# 66. Replication

Copies data across multiple nodes.

```text
Primary

↓

Replica

↓

Replica
```

---

## Benefits

- High availability
- Fault tolerance
- Read scaling

---

## Interview Answer

> Replication ensures reliability by maintaining multiple copies of vector data across different nodes.

---

# 67. Persistence

Ensures vectors survive restarts.

Methods

- Disk storage
- Snapshots
- WAL
- Cloud storage

---

## Interview Answer

> Persistence stores vector indexes durably so they can be recovered after failures or restarts.

---

# 68. Query Optimization

Techniques

- HNSW
- Metadata filters
- Hybrid Search
- Re-ranking
- Caching

---

## Interview Answer

> Vector query optimization reduces latency using efficient indexes, filtering, caching, and approximate search algorithms.

---

# 69. Vector Index Maintenance

Tasks

- Rebuild indexes
- Delete stale vectors
- Update modified documents
- Defragment storage

---

## Interview Answer

> Regular maintenance keeps vector indexes accurate, compact, and performant as data evolves.

---

# 70. Scaling Vector Databases

Strategies

- Horizontal scaling
- Sharding
- Replication
- Distributed indexing
- Load balancing

---

## Interview Answer

> Large-scale vector databases scale horizontally by distributing indexes across multiple nodes while maintaining efficient retrieval.

---

# 71. Backup

Methods

- Snapshot
- Incremental backup
- Cloud object storage
- Disaster recovery plans

---

## Interview Answer

> Production vector databases require regular backups of both vectors and metadata to ensure recoverability.

---

# 72. Cost Optimization

Strategies

- Compress vectors
- Archive unused data
- Choose optimal dimensions
- Batch indexing
- Auto scaling

---

## Interview Answer

> Cost optimization balances storage, compute, and retrieval quality using compression, efficient indexing, and lifecycle management.

---

# 73. Real Production Architecture

```text
                User
                  │
             REST API
                  │
          Authentication
                  │
          Embedding Model
                  │
          Vector Database
        (Pinecone/Qdrant/Milvus)
                  │
          Metadata Database
           (PostgreSQL)
                  │
          Re-ranking Model
                  │
               LLM
                  │
            Final Response
```

### Components

- API Layer
- Embedding Service
- Vector Database
- PostgreSQL Metadata
- Redis Cache
- Cross Encoder
- LLM
- Monitoring

---

## Interview Answer

> Production RAG systems separate embeddings, metadata, retrieval, re-ranking, and generation into independent scalable services.

---

# 74. Monitoring

Metrics

- Query latency
- Recall
- Index size
- CPU
- Memory
- Disk
- Cache hit ratio
- Failed searches

---

## Tools

- Prometheus
- Grafana
- Datadog
- OpenTelemetry

---

## Interview Answer

> Monitoring tracks retrieval quality, infrastructure health, and latency to ensure consistent production performance.

---

# 75. Common Mistakes

### Common Pitfalls

❌ Using keyword search only

❌ Very large chunks

❌ No metadata filtering

❌ No re-ranking

❌ Ignoring embedding drift

❌ No backups

❌ Wrong ANN index selection

❌ Oversized embedding dimensions

❌ Mixing embedding models without re-indexing

❌ No monitoring

---

## Best Practices

- Choose the right vector database for your scale.
- Use HNSW for most production workloads.
- Combine vector search with metadata filtering.
- Implement Hybrid Search (BM25 + Vector Search).
- Re-rank top results using a Cross Encoder.
- Periodically refresh embeddings to avoid embedding drift.
- Monitor latency, recall, and index health.
- Plan for backups, replication, and disaster recovery.
- Optimize vector dimensions and storage costs.
- Keep metadata in a relational database such as PostgreSQL while storing embeddings in a vector database.

---

# Module Summary

- Vector databases enable efficient semantic retrieval by storing and searching embedding vectors.
- FAISS is ideal for local high-performance search, while Pinecone, Weaviate, Milvus, Qdrant, ChromaDB, and pgvector address different production needs.
- HNSW is the most widely adopted ANN indexing algorithm, while IVF and Product Quantization improve scalability and memory efficiency.
- Approximate Nearest Neighbor (ANN) search provides excellent performance with minimal accuracy trade-offs compared to exact search.
- Metadata filtering, hybrid search, and re-ranking significantly improve retrieval quality.
- Production systems rely on sharding, replication, persistence, monitoring, backups, and cost optimization for reliability and scalability.
- A well-designed RAG architecture separates retrieval, metadata, ranking, and generation into independent, scalable components.