# Generative AI Interview Master Guide

# Module 8: LangChain & LlamaIndex (Questions 196–220)

> **Interview Note:** LangChain and LlamaIndex are among the most frequently asked frameworks in GenAI interviews. Senior engineers are expected to understand **how these frameworks work internally**, when to use each, production architecture, observability, performance tuning, and enterprise best practices.

---

# 196. LangChain Architecture

## Definition

LangChain is an open-source framework for building applications powered by LLMs. It provides modular components for prompt management, retrieval, memory, tool integration, agents, and orchestration.

---

## High-Level Architecture

```text
                 User
                   │
                   ▼
             Prompt Template
                   │
                   ▼
             Runnable Chain
                   │
     ┌─────────────┼──────────────┐
     ▼             ▼              ▼
 Retriever      Memory         External Tools
     │             │              │
     └─────────────┼──────────────┘
                   ▼
                LLM Model
                   │
                   ▼
            Output Parser
                   │
                   ▼
              Final Response
```

---

## Core Components

- Models
- Prompts
- Chains
- Agents
- Tools
- Memory
- Retrievers
- Vector Stores
- Output Parsers
- Callbacks
- LangSmith

---

## Interview Answer

> LangChain provides modular building blocks for developing LLM-powered applications by combining prompts, retrieval, memory, tools, and orchestration into reusable pipelines.

---

# 197. Chains

## Definition

A **Chain** connects multiple components into a sequential workflow.

---

## Example

```text
User Question
        │
Prompt Template
        │
LLM
        │
Output Parser
        │
Answer
```

---

## Types

- LLMChain (legacy concept)
- Retrieval Chain
- Sequential Chain
- Runnable Chain

---

## Benefits

- Reusable workflows
- Better modularity
- Easier debugging

---

## Interview Answer

> Chains execute multiple operations sequentially, allowing complex workflows to be composed from smaller reusable components.

---

# 198. Agents

## Definition

LangChain Agents decide dynamically which tools to use based on the user's request.

---

## Workflow

```text
User

↓

Reason

↓

Choose Tool

↓

Execute

↓

Observe

↓

Repeat

↓

Answer
```

---

## Common Agent Types

- ReAct Agent
- OpenAI Functions Agent
- Tool Calling Agent

---

## Interview Answer

> Agents use an LLM to reason about a task and dynamically select tools instead of following a fixed workflow.

---

# 199. Tools

## Definition

Tools allow LangChain to interact with external systems.

---

## Examples

- Google Search
- SQL Database
- Python REPL
- Weather API
- REST APIs
- Vector Databases

---

## Example Flow

```text
Question

↓

Need Database?

↓

SQL Tool

↓

Result

↓

LLM
```

---

## Interview Answer

> Tools extend LLM capabilities by enabling interaction with external APIs, databases, search engines, and custom business logic.

---

# 200. Memory

## Definition

Memory stores conversation context between interactions.

---

## Types

### Buffer Memory

Stores complete conversation.

---

### Summary Memory

Stores summarized history.

---

### Vector Memory

Stores long-term knowledge.

---

## Storage

- Redis
- PostgreSQL
- Vector Database

---

## Interview Answer

> Memory enables conversational continuity by preserving previous interactions or summarizing them for future use.

---

# 201. Retrievers

## Definition

Retrievers fetch relevant documents for RAG applications.

---

## Flow

```text
Question

↓

Embedding

↓

Vector Search

↓

Top Documents

↓

LLM
```

---

## Types

- Vector Retriever
- BM25 Retriever
- Hybrid Retriever
- Parent-Child Retriever

---

## Interview Answer

> Retrievers identify the most relevant documents using semantic or lexical search before passing them to the LLM.

---

# 202. Vector Stores

Supported Stores

- FAISS
- Pinecone
- Qdrant
- ChromaDB
- Weaviate
- Milvus
- pgvector

---

## Responsibilities

- Store embeddings
- Similarity search
- Metadata filtering

---

## Interview Answer

> Vector stores persist embeddings and provide efficient similarity search for retrieval-augmented generation.

---

# 203. Prompt Templates

## Definition

Reusable prompt structures with placeholders.

---

## Example

```python
You are a {role}.

Question:

{question}
```

---

## Benefits

- Consistency
- Reusability
- Easier maintenance

---

## Interview Answer

> Prompt templates separate static instructions from dynamic user input, improving maintainability and consistency.

---

# 204. Output Parsers

## Definition

Convert raw LLM responses into structured outputs.

---

## Example

LLM Output

```text
Name: Alice
Age: 30
```

↓

JSON

```json
{
  "name":"Alice",
  "age":30
}
```

---

## Benefits

- Machine readable
- Validation
- Easier integration

---

## Interview Answer

> Output parsers transform natural language responses into structured formats such as JSON or Python objects.

---

# 205. Runnable Interface

## Definition

The Runnable interface is LangChain's modern execution model.

---

## Features

- Composable
- Streaming
- Async
- Batch processing

---

## Example

```text
Prompt

↓

Runnable

↓

LLM

↓

Parser
```

---

## Interview Answer

> The Runnable interface standardizes how components are composed and executed in LangChain, supporting synchronous, asynchronous, batch, and streaming workflows.

---

# 206. Callbacks

## Purpose

Track execution events.

---

## Events

- LLM started
- Tool called
- Token generated
- Error occurred

---

## Applications

- Logging
- Monitoring
- Debugging

---

## Interview Answer

> Callbacks provide hooks into the execution lifecycle for logging, monitoring, and custom processing.

---

# 207. Streaming

Instead of waiting for the complete response,

Return tokens as they are generated.

---

## Benefits

- Better UX
- Lower perceived latency

---

## Interview Answer

> Streaming delivers generated tokens incrementally, making long responses appear much faster to users.

---

# 208. LangSmith

## Definition

LangSmith is LangChain's observability and debugging platform.

---

## Features

- Prompt tracing
- Agent visualization
- Cost analysis
- Latency monitoring
- Dataset evaluation
- Experiment tracking

---

## Architecture

```text
Application

↓

LangSmith

↓

Trace

↓

Metrics

↓

Dashboard
```

---

## Interview Answer

> LangSmith provides end-to-end observability for LangChain applications by tracing prompts, tools, chains, and agent executions.

---

# 209. LlamaIndex

## Definition

LlamaIndex is a framework focused on connecting LLMs with enterprise data sources.

---

## Strengths

- RAG
- Index management
- Retrieval optimization
- Document processing

---

## Architecture

```text
Documents

↓

Load

↓

Parse

↓

Index

↓

Retrieve

↓

LLM
```

---

## Interview Answer

> LlamaIndex specializes in indexing, retrieving, and querying enterprise knowledge for LLM applications.

---

# 210. Document Loaders

Load data from different sources.

---

## Supported Sources

- PDF
- Word
- HTML
- SQL
- SharePoint
- Confluence
- APIs
- S3
- Azure Blob

---

## Interview Answer

> Document loaders ingest data from diverse sources before parsing and indexing.

---

# 211. Node Parsers

## Definition

Split documents into structured nodes.

---

## Workflow

```text
Document

↓

Chunk

↓

Metadata

↓

Node
```

---

## Benefits

- Better retrieval
- Rich metadata
- Flexible indexing

---

## Interview Answer

> Node parsers transform raw documents into searchable units while preserving metadata and document relationships.

---

# 212. Query Engines

## Definition

Query engines coordinate retrieval and answer generation.

---

## Pipeline

```text
Question

↓

Retrieve Nodes

↓

LLM

↓

Answer
```

---

## Interview Answer

> Query engines orchestrate retrieval, prompt construction, and response generation.

---

# 213. Router Query Engine

## Purpose

Route queries to different indexes.

---

## Example

```text
Finance Question

↓

Finance Index

Medical Question

↓

Medical Index
```

---

## Benefits

- Better scalability
- Domain specialization

---

## Interview Answer

> Router Query Engines intelligently select the most relevant index based on the incoming query.

---

# 214. Response Synthesizer

Combines retrieved documents into one coherent response.

---

## Responsibilities

- Summarization
- Ranking
- Citation generation
- Context merging

---

## Interview Answer

> Response synthesizers merge information from multiple retrieved documents into a coherent and grounded answer.

---

# 215. Multi Index Search

Search across multiple indexes.

---

## Example

```text
Legal Index

HR Index

Finance Index

↓

Merge Results
```

---

## Benefits

- Enterprise search
- Cross-domain retrieval

---

## Interview Answer

> Multi-index search retrieves information from multiple knowledge bases and combines the results into a unified response.

---

# 216. Production Deployment

Typical Architecture

```text
User

↓

API Gateway

↓

LangChain/LlamaIndex

↓

Retriever

↓

Vector Database

↓

LLM

↓

Response
```

---

## Components

- Kubernetes
- Redis
- PostgreSQL
- Vector DB
- Monitoring
- API Gateway

---

## Interview Answer

> Production deployments separate retrieval, orchestration, inference, and storage into independently scalable services.

---

# 217. Performance

Optimization Techniques

- Better chunking
- Hybrid search
- Re-ranking
- Context compression
- Caching
- Async execution
- Batch embeddings

---

## Interview Answer

> Performance improvements focus on reducing retrieval latency, optimizing prompt size, and minimizing unnecessary LLM calls.

---

# 218. Cost Optimization

Strategies

- Semantic cache
- Smaller embedding models
- Context compression
- Batch processing
- Prompt optimization
- Incremental indexing

---

## Interview Answer

> Cost optimization reduces embedding generation, retrieval, and inference expenses while maintaining acceptable response quality.

---

# 219. Observability

Metrics

- Latency
- Token usage
- Cost
- Retrieval accuracy
- Tool usage
- Errors
- Cache hit rate

---

## Tools

- LangSmith
- Prometheus
- Grafana
- OpenTelemetry

---

## Interview Answer

> Observability provides detailed visibility into application behavior, enabling rapid debugging and continuous optimization.

---

# 220. Interview Questions

### Q1. When would you choose LangChain over LlamaIndex?

**Answer:**

Choose LangChain when building agent-based applications, workflows, and tool orchestration. Choose LlamaIndex when the primary focus is enterprise RAG and document retrieval.

---

### Q2. Can LangChain and LlamaIndex be used together?

**Answer:**

Yes. A common production architecture uses LlamaIndex for document ingestion and retrieval while LangChain orchestrates prompts, agents, tools, and workflows.

---

### Q3. What are LangChain's biggest limitations?

- High abstraction
- Frequent API changes
- Complex debugging
- Performance overhead

---

### Q4. What are LlamaIndex's strengths?

- Excellent RAG support
- Efficient indexing
- Rich retrieval options
- Enterprise document management

---

### Q5. How would you build a production RAG system?

**Expected Discussion**

- Document ingestion
- Chunking
- Embeddings
- Vector database
- Hybrid retrieval
- Re-ranking
- Prompt templates
- LLM inference
- Response validation
- Monitoring
- LangSmith observability

---

# LangChain vs LlamaIndex

| Feature | LangChain | LlamaIndex |
|---------|-----------|------------|
| Primary Focus | LLM Applications & Agents | Enterprise RAG & Retrieval |
| Agents | ✅ Excellent | Limited |
| Chains | ✅ Excellent | Basic |
| Memory | ✅ Built-in | Limited |
| Retrieval | Good | Excellent |
| Indexing | Basic | Excellent |
| Document Processing | Good | Excellent |
| Multi-Agent Workflows | Strong | Limited |
| Tool Integration | Excellent | Moderate |
| Best Use Case | AI Agents, Automation | Knowledge Search, RAG |

---

# Production Architecture

```text
                 User
                   │
             API Gateway
                   │
          LangChain Orchestrator
                   │
      ┌────────────┼────────────┐
      ▼            ▼            ▼
 Prompting     Tool Calls   LlamaIndex
                                   │
                            Document Loaders
                                   │
                            Node Parsers
                                   │
                             Vector Database
                                   │
                               Retriever
                                   │
                                 LLM
                                   │
                             Output Parser
                                   │
                           LangSmith Tracing
                                   │
                              Final Response
```

---

# Module Summary

- LangChain is a comprehensive framework for building LLM-powered applications with chains, agents, memory, tools, and orchestration.
- LlamaIndex specializes in document ingestion, indexing, retrieval, and enterprise RAG workflows.
- LangChain excels in agent-based systems, while LlamaIndex provides advanced retrieval and indexing capabilities.
- Core LangChain components include Chains, Agents, Tools, Retrievers, Vector Stores, Prompt Templates, Output Parsers, Runnables, and Callbacks.
- LangSmith enables production-grade observability with tracing, evaluation, and performance monitoring.
- Production systems often combine LangChain for orchestration with LlamaIndex for retrieval and indexing.
- Performance optimization relies on efficient retrieval, caching, hybrid search, batching, and prompt optimization.
- Enterprise deployments require observability, scalability, security, and cost optimization from the beginning.