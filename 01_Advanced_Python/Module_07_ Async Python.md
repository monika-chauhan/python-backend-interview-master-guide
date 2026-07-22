# Module 7: Async Python (Questions 73–82)

# Q73. What is `asyncio`?

## Answer

`asyncio` is Python's built-in library for writing **concurrent, asynchronous programs** using the `async` and `await` keywords.

Unlike multithreading, `asyncio` uses **cooperative multitasking**, where tasks voluntarily yield control while waiting for I/O operations (network requests, database calls, file operations, etc.).

It is ideal for:

- Web servers
- HTTP clients
- Chat applications
- Microservices
- Real-time systems
- Network programming

---

## Why Do We Need asyncio?

Imagine downloading 100 web pages.

### Synchronous Approach

```python
download(page1)
download(page2)
download(page3)
...
```

Each download blocks the next one.

Total time:

```
1 sec × 100 = 100 seconds
```

---

### Asynchronous Approach

```python
await download(page1)
await download(page2)
await download(page3)
```

While one request waits for the network, another request executes.

Total time:

```
≈ Maximum individual request time
```

---

## Example

```python
import asyncio

async def hello():
    print("Hello")
    await asyncio.sleep(2)
    print("World")

asyncio.run(hello())
```

Output

```
Hello
(wait 2 seconds)
World
```

---

## Key Components

- Event Loop
- Coroutine
- Task
- Future
- Awaitable
- Scheduler

---

## Advantages

- Lightweight
- No thread overhead
- Excellent for I/O-bound workloads
- High scalability

---

## Limitations

- Not suitable for CPU-intensive work
- Requires async-compatible libraries

---

## Interview Follow-up

**Q:** Does `asyncio` create new threads?

**Answer:** No. By default, all coroutines run on a **single thread** managed by the event loop.

---

# Q74. Event Loop Internals

## Answer

The **Event Loop** is the heart of `asyncio`.

It continuously checks:

- Which tasks are ready?
- Which tasks are waiting?
- Which I/O operations have completed?

The loop schedules tasks efficiently.

---

## Simplified Workflow

```
Create Coroutine
        │
        ▼
Event Loop
        │
        ▼
Task starts
        │
        ▼
await encountered
        │
        ▼
Task pauses
        │
        ▼
Another task executes
        │
        ▼
I/O completes
        │
        ▼
Original task resumes
```

---

## Example

```python
import asyncio

async def worker(name):
    print(name, "started")
    await asyncio.sleep(2)
    print(name, "finished")

async def main():
    await asyncio.gather(
        worker("A"),
        worker("B")
    )

asyncio.run(main())
```

Output

```
A started
B started
(wait)
A finished
B finished
```

---

## Responsibilities

- Schedule tasks
- Resume paused coroutines
- Monitor I/O events
- Handle timers
- Execute callbacks

---

## Senior Interview Tip

The event loop performs **cooperative scheduling**. A coroutine runs until it reaches an `await`; only then can another coroutine run.

---

# Q75. Coroutine vs Thread

## Answer

Both enable concurrency but differ fundamentally.

| Feature | Coroutine | Thread |
|----------|-----------|--------|
| Managed By | Event Loop | OS Scheduler |
| Switching | Cooperative | Preemptive |
| Memory | Very low | High |
| Speed | Faster context switch | Slower |
| Best For | I/O-bound | CPU-bound / blocking I/O |

---

## Coroutine Example

```python
async def fetch():
    await asyncio.sleep(1)
```

---

## Thread Example

```python
from threading import Thread

def fetch():
    print("Running")

Thread(target=fetch).start()
```

---

## Advantages of Coroutines

- Thousands of concurrent tasks
- Lower memory usage
- Minimal context-switch overhead

---

## Interview Question

**Can coroutines run in parallel?**

No. A single event loop executes one coroutine at a time. True parallelism requires multiple threads or processes.

---

# Q76. `await` vs `yield`

## Answer

Although both pause execution, they serve different purposes.

| await | yield |
|--------|--------|
| Async programming | Generator programming |
| Waits for awaitable | Produces next value |
| Used with `async def` | Used with `def` |
| Works with event loop | Works with iterators |

---

## await Example

```python
async def demo():
    await asyncio.sleep(1)
```

---

## yield Example

```python
def numbers():
    yield 1
    yield 2
```

---

## Key Difference

`yield`

```
Produces data.
```

`await`

```
Waits for asynchronous completion.
```

---

## Interview Follow-up

**Can you use `yield` and `await` together?**

Yes, inside an **async generator**.

---

# Q77. Task vs Future

## Answer

Both represent asynchronous operations.

However:

- **Future** represents a result that will become available later.
- **Task** is a Future that wraps and executes a coroutine.

---

## Future

```python
future = asyncio.Future()
```

A Future does not execute anything by itself.

---

## Task

```python
task = asyncio.create_task(coroutine())
```

A Task schedules the coroutine immediately.

---

## Relationship

```
Future
   ▲
   │
 Task
```

Every Task is a Future, but not every Future is a Task.

---

## Example

```python
async def work():
    await asyncio.sleep(1)
    return 10

async def main():
    task = asyncio.create_task(work())
    result = await task
    print(result)

asyncio.run(main())
```

Output

```
10
```

---

## Interview Tip

Use `create_task()` for concurrent execution of coroutines. Futures are more common in low-level library implementations.

---

# Q78. `asyncio.gather()` vs `asyncio.wait()`

## Answer

Both run multiple asynchronous tasks, but they behave differently.

| gather() | wait() |
|-----------|---------|
| Returns results | Returns done/pending sets |
| Preserves order | No guaranteed order |
| Raises first exception by default | Configurable completion behavior |
| High-level API | Lower-level API |

---

## gather()

```python
await asyncio.gather(task1(), task2())
```

Returns

```python
[result1, result2]
```

---

## wait()

```python
done, pending = await asyncio.wait(tasks)
```

Returns two sets.

---

## Example

```python
await asyncio.gather(
    worker1(),
    worker2()
)
```

versus

```python
done, pending = await asyncio.wait(
    tasks,
    return_when=asyncio.FIRST_COMPLETED
)
```

---

## When to Use

Use `gather()` when all task results are needed.

Use `wait()` when you need finer control over completion conditions or cancellation.

---

# Q79. Async Generators

## Answer

An async generator combines:

- `async`
- `yield`

It produces values asynchronously.

---

## Example

```python
import asyncio

async def numbers():

    for i in range(5):
        await asyncio.sleep(1)
        yield i

async def main():

    async for num in numbers():
        print(num)

asyncio.run(main())
```

Output

```
0
1
2
3
4
```

---

## Use Cases

- Streaming APIs
- Live sensor data
- Log processing
- Message queues

---

## Benefits

- Lazy evaluation
- Non-blocking
- Low memory usage

---

# Q80. Async Iterators

## Answer

Async iterators are used with:

```python
async for
```

They implement:

- `__aiter__()`
- `__anext__()`

---

## Example

```python
import asyncio

class Counter:

    def __init__(self):
        self.value = 0

    def __aiter__(self):
        return self

    async def __anext__(self):

        if self.value >= 5:
            raise StopAsyncIteration

        await asyncio.sleep(1)
        self.value += 1
        return self.value

async def main():

    async for item in Counter():
        print(item)

asyncio.run(main())
```

Output

```
1
2
3
4
5
```

---

## Difference

| Iterator | Async Iterator |
|-----------|----------------|
| `for` | `async for` |
| `__next__()` | `__anext__()` |
| `StopIteration` | `StopAsyncIteration` |

---

# Q81. Handling Timeouts and Cancellation

## Answer

Long-running async tasks may need to be cancelled or limited by time.

---

## Timeout Example

```python
import asyncio

async def slow():
    await asyncio.sleep(5)

async def main():

    try:
        await asyncio.wait_for(slow(), timeout=2)
    except asyncio.TimeoutError:
        print("Timeout!")

asyncio.run(main())
```

Output

```
Timeout!
```

---

## Cancellation Example

```python
import asyncio

async def worker():

    try:
        while True:
            print("Working...")
            await asyncio.sleep(1)

    except asyncio.CancelledError:
        print("Cancelled")

async def main():

    task = asyncio.create_task(worker())

    await asyncio.sleep(3)

    task.cancel()

    await task

asyncio.run(main())
```

Output

```
Working...
Working...
Working...
Cancelled
```

---

## Best Practices

- Always clean up resources in `finally` blocks.
- Catch `CancelledError` only when cleanup is required, then re-raise if appropriate.
- Use `wait_for()` to prevent indefinitely hanging operations.

---

# Q82. Build an Async Web Crawler

## Answer

A simple asynchronous web crawler can fetch multiple URLs concurrently using `aiohttp` and `asyncio`.

```python
import asyncio
import aiohttp

urls = [
    "https://example.com",
    "https://python.org",
    "https://httpbin.org/get"
]

async def fetch(session, url):
    async with session.get(url) as response:
        text = await response.text()
        print(f"{url} -> {len(text)} bytes")
        return text

async def main():

    async with aiohttp.ClientSession() as session:

        tasks = [
            fetch(session, url)
            for url in urls
        ]

        await asyncio.gather(*tasks)

asyncio.run(main())
```

---

## Workflow

```
URLs
   │
   ▼
Create Tasks
   │
   ▼
Event Loop
   │
   ▼
Concurrent HTTP Requests
   │
   ▼
Responses Received
   │
   ▼
Process Results
```

---

## Production Enhancements

- Limit concurrency with `asyncio.Semaphore`
- Retry failed requests with exponential backoff
- Respect `robots.txt`
- Set request timeouts
- Use custom headers and user agents
- Persist results to a database or queue
- Handle redirects and HTTP errors gracefully

---

## Complexity

If there are **N** URLs:

- Time: Approximately **O(max(request latency))** when sufficient concurrency is available (rather than the sum of latencies).
- Memory: **O(N)** for tracking tasks and results.

---

## Senior Interview Discussion Points

- Explain why `asyncio` outperforms threads for I/O-bound workloads.
- Mention that `aiohttp.ClientSession` should be reused instead of creating a session per request.
- Discuss concurrency limiting (`Semaphore`) to avoid overwhelming remote servers.
- Highlight proper timeout, cancellation, and exception handling for robust crawlers.

---

# Async Python Interview Deep Dive

These are common **Senior Python Backend Interview** follow-up questions related to `asyncio` and asynchronous web crawlers.

---

# 1. Explain why `asyncio` outperforms threads for I/O-bound workloads.

## Short Answer

`asyncio` is faster for **I/O-bound applications** because it avoids the overhead of creating and switching between operating system threads. Instead, it uses a **single-threaded event loop** that efficiently schedules thousands of lightweight coroutines.

---

## Understanding I/O-bound Workloads

I/O-bound tasks spend most of their time waiting for external resources, such as:

- HTTP requests
- Database queries
- Reading files
- Writing files
- Network communication
- Message queues

Example:

```
Request API
↓

Wait 500ms

↓

Receive Response
```

During those 500 ms, the CPU is mostly idle.

---

## Using Threads

Suppose we need to fetch 10,000 URLs.

```
Thread 1 → Waiting
Thread 2 → Waiting
Thread 3 → Waiting
...
Thread 10000 → Waiting
```

Problems:

- Each thread consumes memory (typically several MB of stack space).
- Context switching between threads is expensive because the OS scheduler must save and restore thread state.
- The OS scheduler determines when threads run, introducing overhead.

---

## Using asyncio

```
Coroutine A
↓

await socket.recv()

↓

Event Loop

↓

Coroutine B

↓

Coroutine C

↓

Coroutine D
```

When a coroutine reaches an `await`, it voluntarily yields control back to the event loop. While it waits for I/O, the event loop immediately runs another coroutine.

This cooperative scheduling avoids unnecessary CPU work and allows a single thread to manage thousands of concurrent operations.

---

## Memory Comparison

Approximate memory usage:

| Threads | asyncio Coroutines |
|----------|--------------------|
| 10,000 threads → several GB | 10,000 coroutines → tens of MB |

---

## Performance Comparison

| Feature | Threads | asyncio |
|----------|----------|----------|
| Context Switching | OS-managed | Event loop-managed |
| Memory Usage | High | Low |
| Scalability | Limited | Very High |
| Best For | CPU-bound or blocking libraries | I/O-bound workloads |

---

## Interview Answer (2 Minutes)

> `asyncio` performs better for I/O-bound workloads because coroutines don't block the thread while waiting for network or disk operations. When a coroutine executes `await`, the event loop schedules another ready coroutine instead of leaving the CPU idle. This avoids the memory and context-switch overhead of thousands of OS threads, allowing a single thread to efficiently handle a very large number of concurrent I/O operations.

---

# 2. Why should `aiohttp.ClientSession` be reused?

## Short Answer

A single `ClientSession` should be reused because it maintains a **connection pool**, enabling HTTP connection reuse and reducing the overhead of repeatedly creating TCP (and TLS) connections.

---

## Bad Practice

```python
async def fetch(url):

    async with aiohttp.ClientSession() as session:

        async with session.get(url) as response:
            return await response.text()
```

Problems:

- New TCP connection for every request.
- New TLS handshake for HTTPS.
- No connection pooling.
- Increased latency.
- Higher CPU usage.

---

## Recommended Approach

```python
async with aiohttp.ClientSession() as session:

    tasks = [
        fetch(session, url)
        for url in urls
    ]

    await asyncio.gather(*tasks)
```

Only one session is created and shared across requests.

---

## Internal Architecture

```
ClientSession
      │
      ▼
Connection Pool
      │
      ├── TCP Connection 1
      ├── TCP Connection 2
      ├── TCP Connection 3
      └── Keep-Alive Connections
```

The session automatically reuses existing connections when possible.

---

## Benefits

- Connection pooling
- Keep-alive support
- Lower latency
- Reduced CPU usage
- Better scalability

---

## Interview Answer

> `ClientSession` should be long-lived because it owns a connection pool. Reusing the session allows HTTP keep-alive and connection reuse, reducing the cost of establishing new TCP and TLS connections for every request. Creating a new session per request is inefficient and can significantly hurt performance.

---

# 3. Why use `Semaphore`?

## Problem

Suppose we launch 100,000 requests simultaneously.

```python
await asyncio.gather(*tasks)
```

Potential issues:

- Overwhelms the remote server.
- Exceeds the operating system's file descriptor limit.
- Causes connection failures.
- Triggers rate limiting (HTTP 429).

---

## Solution

Limit concurrency using `Semaphore`.

```python
import asyncio

semaphore = asyncio.Semaphore(20)

async def fetch(url):

    async with semaphore:
        print(url)
```

Only 20 coroutines can execute the protected section concurrently.

---

## Workflow

```
1000 URLs
      │
      ▼
Semaphore(20)
      │
      ▼
20 Active Requests
      │
      ▼
One Finishes
      │
      ▼
Next Request Starts
```

---

## Choosing a Limit

Examples:

```
Semaphore(10)

Semaphore(20)

Semaphore(100)
```

The optimal value depends on:

- API rate limits
- Network bandwidth
- CPU resources
- Server capacity

---

## Benefits

- Prevents server overload
- Respects API rate limits
- Avoids exhausting client resources
- Improves crawler stability

---

## Interview Answer

> A semaphore limits the number of concurrent requests. Instead of launching every request at once, it ensures only a fixed number run simultaneously. This prevents overwhelming remote servers, avoids hitting operating system limits, and reduces the chance of receiving rate-limit responses such as HTTP 429.

---

# 4. Timeout, Cancellation, and Exception Handling

A production crawler must handle slow servers, failures, and shutdowns gracefully.

---

## A. Timeouts

Without a timeout:

```python
await session.get(url)
```

A request may hang indefinitely.

Use a timeout:

```python
try:
    await asyncio.wait_for(
        fetch(session, url),
        timeout=5
    )
except asyncio.TimeoutError:
    print("Request timed out")
```

This ensures slow requests do not block overall progress.

---

## B. Cancellation

Tasks should respond cleanly to cancellation.

```python
async def worker():

    try:
        while True:
            await asyncio.sleep(1)

    except asyncio.CancelledError:
        print("Cleaning up")
        raise
```

Re-raising `CancelledError` after cleanup allows cancellation to propagate correctly.

---

## C. Exception Handling

A failed request should not terminate the entire crawl.

```python
async def fetch(session, url):

    try:
        async with session.get(url) as response:
            return await response.text()

    except Exception as e:
        print(e)
```

Alternatively, when using `gather()`:

```python
results = await asyncio.gather(
    *tasks,
    return_exceptions=True
)
```

This collects exceptions alongside successful results instead of failing the entire operation immediately.

---

## Production Checklist

✔ Reuse a single `ClientSession`

✔ Limit concurrency with `Semaphore`

✔ Configure request timeouts

✔ Retry transient failures (preferably with exponential backoff)

✔ Handle cancellations gracefully

✔ Log exceptions without crashing the crawler

✔ Close sessions cleanly using `async with`

✔ Respect API rate limits and `robots.txt`

---

# Senior Interview Summary

If asked how you would design a production-grade asynchronous web crawler, mention the following points:

1. Use `asyncio` because it efficiently handles thousands of concurrent I/O operations with low memory overhead.
2. Reuse a single `aiohttp.ClientSession` to benefit from connection pooling and HTTP keep-alive.
3. Limit concurrent requests using `asyncio.Semaphore` to avoid overloading remote servers and exhausting local resources.
4. Configure timeouts to prevent hanging requests.
5. Handle `CancelledError` properly, performing cleanup before re-raising when appropriate.
6. Catch and log request failures, or use `asyncio.gather(..., return_exceptions=True)` so one failing request does not terminate the entire crawl.
7. Implement retries with exponential backoff and respect rate limits and `robots.txt`.

These practices demonstrate an understanding of both asynchronous programming and production-ready system design.

**End of Module 7 (Questions 73–82)**