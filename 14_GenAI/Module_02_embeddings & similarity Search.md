# Generative AI Interview Master Guide

# Module 2: Embeddings & Semantic Search (Questions 26–50)

---

# 26. What are Embeddings?

## Definition

Embeddings are **dense numerical vector representations** of data (text, images, audio, etc.) that capture their semantic meaning.

Instead of storing text as words, embeddings convert it into a list of floating-point numbers.

Example:

```
"I love Python"

↓

[0.23, -0.81, 1.42, ..., 0.55]
```

Similar meanings produce similar vectors.

---

## Why Embeddings?

Traditional computers understand numbers, not language.

Embeddings convert unstructured data into mathematical representations so similarity can be measured.

---

## Applications

- Semantic Search
- Recommendation Systems
- RAG
- Clustering
- Duplicate Detection
- Question Answering

---

## Interview Answer

> Embeddings are dense vector representations that encode semantic meaning, allowing machines to compare text, images, or audio using mathematical similarity rather than exact keyword matching.

---

# 27. Why Embeddings instead of Keywords?

## Keyword Search

```
Query:
Car

Document:
Automobile
```

Keyword search fails because "car" ≠ "automobile".

---

## Embedding Search

```
Car

↓

Vector

↓

Automobile

↓

High similarity
```

Embeddings understand semantic relationships.

---

## Advantages

- Synonym detection
- Better ranking
- Context understanding
- Natural language search

---

## Interview Answer

> Keyword search matches exact words, while embeddings capture semantic meaning, allowing retrieval of conceptually similar content even when wording differs.

---

# 28. Dense vs Sparse Embeddings

## Dense Embeddings

```
[0.42, -0.81, 0.25, ...]
```

Characteristics

- Every dimension has values
- Deep learning generated
- Semantic understanding

Examples

- OpenAI Embeddings
- BGE
- E5

---

## Sparse Embeddings

```
Dog → 1

Cat → 0

Animal → 3
```

Characteristics

- Mostly zeros
- Based on word occurrence
- Keyword focused

Example

- BM25
- TF-IDF

---

## Comparison

| Dense | Sparse |
|--------|---------|
| Semantic | Lexical |
| Neural | Statistical |
| Synonyms | Exact words |
| Better for AI Search | Better for keywords |

---

## Interview Answer

> Dense embeddings capture semantic relationships, whereas sparse embeddings represent token frequencies or lexical features. Hybrid search often combines both.

---

# 29. Sentence Embeddings

Sentence embeddings represent an entire sentence as a single vector.

Example

```
"The weather is beautiful today."

↓

768-dimensional vector
```

Applications

- Semantic search
- Document retrieval
- Clustering

---

## Interview Answer

> Sentence embeddings encode the meaning of an entire sentence into a fixed-length vector, enabling semantic comparison between complete sentences.

---

# 30. Cosine Similarity

Measures angle between two vectors.

Formula

```
Cos(A,B)

=

(A·B)

/

|A||B|
```

Range

```
1 → identical

0 → unrelated

-1 → opposite
```

---

## Why Used?

Magnitude doesn't matter.

Only direction matters.

---

## Interview Answer

> Cosine similarity is the most common similarity metric for embeddings because it measures semantic alignment independent of vector magnitude.

---

# 31. Euclidean Distance

Measures straight-line distance.

Formula

```
√((x₁−x₂)² + ...)
```

Smaller distance = More similar.

---

## Limitations

Sensitive to vector magnitude.

---

## Interview Answer

> Euclidean distance measures geometric distance between vectors but is generally less effective than cosine similarity for high-dimensional embeddings.

---

# 32. Dot Product

Formula

```
A · B
```

Used in

- Transformers
- Attention
- Vector databases

---

## Comparison

| Dot Product | Cosine |
|--------------|---------|
| Depends on magnitude | Normalized |
| Faster | More interpretable |

---

## Interview Answer

> Dot product measures vector alignment while considering magnitude and is widely used in attention mechanisms and vector search.

---

# 33. Manhattan Distance

Formula

```
|x₁−x₂|

+

|y₁−y₂|
```

Also called

L1 Distance.

---

## Use Cases

- Grid movement
- Some ML algorithms

---

## Interview Answer

> Manhattan distance measures absolute coordinate differences and is useful for certain optimization and clustering problems.

---

# 34. Embedding Dimensions

Dimension = Length of embedding vector.

Examples

```
384

768

1024

1536

3072
```

Higher dimensions

Pros

- Richer representation

Cons

- More memory
- Slower search

---

## Interview Answer

> Embedding dimensions determine the amount of information stored in each vector. Higher dimensions improve expressiveness but increase storage and computation costs.

---

# 35. Chunking Strategies

Chunking splits large documents into smaller pieces before embedding.

---

## Strategies

### Fixed Size

```
500 words
```

---

### Sentence Based

```
Sentence

↓

Sentence
```

---

### Paragraph Based

Each paragraph.

---

### Semantic Chunking

Split where meaning changes.

---

### Recursive Chunking

Preferred for RAG.

---

## Interview Answer

> Chunking divides documents into manageable units that fit within model context limits while preserving semantic coherence for retrieval.

---

# 36. Chunk Size

Typical sizes

```
256

512

768

1024 tokens
```

Small chunks

Pros

- Better precision

Cons

- More embeddings

Large chunks

Pros

- Better context

Cons

- Lower retrieval precision

---

## Interview Answer

> Chunk size should balance retrieval precision with contextual completeness, typically ranging from 300–800 tokens depending on the use case.

---

# 37. Chunk Overlap

Example

```
Chunk 1

0-500

Chunk 2

450-950
```

Overlap preserves context across chunk boundaries.

Typical overlap

10–20%

---

## Interview Answer

> Chunk overlap prevents important context from being lost when relevant information spans multiple chunks.

---

# 38. Metadata Filtering

Metadata

```
Author

Department

Date

Language

Document Type
```

Example

```
Search

+

department=HR
```

---

## Benefits

- Faster search
- Better accuracy

---

## Interview Answer

> Metadata filtering narrows the search space before vector similarity, improving relevance and reducing latency.

---

# 39. Hybrid Search

Combines

```
Keyword Search

+

Vector Search
```

Architecture

```
BM25

+

Embeddings

↓

Rank Fusion
```

---

## Advantages

- Exact keywords
- Semantic understanding

---

## Interview Answer

> Hybrid search combines lexical retrieval with semantic vector search to achieve higher recall and precision than either approach alone.

---

# 40. Semantic Search

Instead of matching words, semantic search matches meaning.

Example

Query

```
How to reset password?
```

Document

```
Forgot login credentials?
```

Still retrieved.

---

## Interview Answer

> Semantic search retrieves documents based on meaning rather than exact keyword matches by comparing embedding similarity.

---

# 41. Keyword Search vs Vector Search

| Keyword | Vector |
|----------|---------|
| Exact words | Meaning |
| BM25 | Embeddings |
| Fast | More accurate |
| Misses synonyms | Handles synonyms |

---

## Interview Answer

> Keyword search excels at exact matches, while vector search understands semantics. Production systems often combine both.

---

# 42. Embedding Models

Popular models

- OpenAI Embeddings
- BAAI BGE
- E5
- Sentence Transformers
- Cohere Embed
- Jina Embeddings

Selection Criteria

- Quality
- Cost
- Latency
- Multilingual support
- Dimension size

---

## Interview Answer

> Embedding models transform data into vector space. Selection depends on accuracy, latency, multilingual capability, and deployment requirements.

---

# 43. Cross Encoder vs Bi-Encoder

## Bi-Encoder

```
Query

↓

Embedding

Document

↓

Embedding

↓

Cosine Similarity
```

Fast.

Suitable for retrieval.

---

## Cross Encoder

```
Query

+

Document

↓

Transformer

↓

Score
```

Slower.

More accurate.

---

## Comparison

| Bi-Encoder | Cross Encoder |
|-------------|---------------|
| Fast | Slower |
| Retrieval | Re-ranking |
| Offline embeddings | Online scoring |

---

## Interview Answer

> Bi-encoders generate independent embeddings for efficient retrieval, while cross-encoders jointly evaluate query-document pairs for more accurate ranking.

---

# 44. Re-ranking

Workflow

```
Vector Search

↓

Top 50

↓

Cross Encoder

↓

Top 5
```

Improves final ranking quality.

---

## Interview Answer

> Re-ranking refines initial retrieval results using a more accurate but computationally expensive model such as a cross-encoder.

---

# 45. Embedding Drift

Definition

Embedding quality changes over time because

- New terminology
- Model upgrades
- Domain changes

Example

Medical terms evolve.

Old embeddings become outdated.

---

## Interview Answer

> Embedding drift occurs when stored vectors no longer accurately represent evolving data or vocabulary, requiring periodic regeneration.

---

# 46. Updating Embeddings

When to Update

- New documents
- Model changes
- Schema changes
- Major content edits

Strategies

- Incremental updates
- Batch re-indexing
- Background processing

---

## Interview Answer

> Embeddings should be regenerated whenever source content or the embedding model changes to maintain retrieval accuracy.

---

# 47. Multilingual Embeddings

Support multiple languages in the same vector space.

Example

```
Hello

↓

Hola

↓

Bonjour
```

High similarity.

---

## Benefits

- Global search
- Cross-language retrieval

---

## Interview Answer

> Multilingual embeddings map semantically equivalent text from different languages into a shared vector space.

---

# 48. Image Embeddings

Convert images into vectors.

Applications

- Reverse image search
- Similar product recommendation
- Face recognition
- Visual search

Example Models

- CLIP
- ViT

---

## Interview Answer

> Image embeddings encode visual features into vectors, enabling similarity search and multimodal applications.

---

# 49. Audio Embeddings

Represent speech or sound as vectors.

Applications

- Speaker recognition
- Audio search
- Voice assistants
- Music recommendation

---

## Interview Answer

> Audio embeddings capture acoustic characteristics that enable semantic search and comparison of audio signals.

---

# 50. Best Practices

## Retrieval Pipeline

```
Document

↓

Cleaning

↓

Chunking

↓

Embedding

↓

Vector DB

↓

Hybrid Search

↓

Re-ranking

↓

LLM
```

---

## Best Practices

- Use semantic chunking where possible.
- Keep chunk sizes between **300–800 tokens** for most RAG systems.
- Add **10–20% overlap** to preserve context.
- Store rich metadata for filtering.
- Combine BM25 with vector search (Hybrid Search).
- Re-rank top results using a cross-encoder.
- Monitor embedding drift and refresh embeddings periodically.
- Choose embedding models based on quality, latency, and multilingual support.
- Optimize vector dimensions to balance accuracy and storage.
- Cache frequently accessed embeddings and retrieval results.

---

## Interview Answer

> A production-grade semantic search system combines high-quality embeddings, effective chunking, metadata filtering, hybrid retrieval, re-ranking, and periodic embedding maintenance to maximize accuracy and scalability.

---

# Module Summary

- Embeddings convert unstructured data into dense vectors that capture semantic meaning.
- Dense embeddings are ideal for semantic search, while sparse embeddings excel at keyword matching.
- Cosine similarity is the most widely used metric for comparing embedding vectors.
- Chunking strategy, chunk size, and overlap significantly influence RAG performance.
- Hybrid search combines keyword-based retrieval (BM25) with vector search for better recall and precision.
- Bi-encoders are optimized for fast retrieval, whereas cross-encoders provide more accurate re-ranking.
- Embedding drift requires periodic monitoring and regeneration of vectors.
- Modern systems support multilingual, image, and audio embeddings for multimodal search.
- Production semantic search pipelines integrate embeddings, vector databases, metadata filtering, re-ranking, and LLMs to deliver accurate, scalable retrieval.    