# Module 6: Concurrency (Questions 61–72)

# Q61. Threading vs Multiprocessing

## Answer

Both **threading** and **multiprocessing** are techniques for achieving concurrency in Python, but they differ significantly in architecture, memory usage, and use cases.

---

## Threading

A thread is a lightweight unit of execution within a process.

- Multiple threads share the same memory space.
- Communication between threads is fast.
- Subject to the Global Interpreter Lock (GIL) in CPython.
- Best suited for I/O-bound tasks.

### Example

```python
import threading
import time

def worker(name):
    print(f"{name} started")
    time.sleep(2)
    print(f"{name} finished")

t1 = threading.Thread(target=worker, args=("Thread-1",))
t2 = threading.Thread(target=worker, args=("Thread-2",))

t1.start()
t2.start()

t1.join()
t2.join()
```

---

## Multiprocessing

A process has its own independent memory space and Python interpreter.

- No shared memory by default.
- Each process has its own GIL.
- True parallel execution on multiple CPU cores.
- Best suited for CPU-bound tasks.

### Example

```python
from multiprocessing import Process
import time

def worker():
    print("Working")
    time.sleep(2)

p1 = Process(target=worker)
p2 = Process(target=worker)

p1.start()
p2.start()

p1.join()
p2.join()
```

---

## Comparison

| Feature | Threading | Multiprocessing |
|----------|-----------|-----------------|
| Memory | Shared | Separate |
| GIL | Shared | One GIL per process |
| Parallelism | No (CPU-bound) | Yes |
| Communication | Easy | IPC required |
| Startup Cost | Low | Higher |
| Best For | I/O-bound | CPU-bound |

---

## Interview Tip

> Use threading when tasks spend most of their time waiting for I/O. Use multiprocessing when tasks spend most of their time performing CPU-intensive computation.

---

# Q62. Explain the GIL in Detail

## Answer

The **Global Interpreter Lock (GIL)** is a mutex in CPython that allows **only one thread to execute Python bytecode at a time** within a single process.

---

## Why Does the GIL Exist?

CPython uses **reference counting** for memory management.

Without synchronization:

```
Thread A → ref_count += 1

Thread B → ref_count -= 1
```

If both modify the same object simultaneously, memory corruption may occur.

The GIL prevents this by allowing only one thread to execute Python bytecode at any given moment.

---

## Visualization

```
Thread A
      │
      ▼
     GIL
      │
      ▼
Execute Python Bytecode

----------------------------

Thread B
      │
Waiting for GIL
```

---

## Important Clarification

The GIL does **not** prevent concurrency.

While one thread waits for network or disk I/O, the GIL is released, allowing another thread to run.

---

## When Does the GIL Become a Bottleneck?

CPU-intensive workloads such as:

- Image processing
- Matrix multiplication
- Compression
- Encryption
- Scientific computation

---

## When Is It Not a Problem?

- HTTP requests
- Database queries
- File I/O
- Socket communication

---

## Interview Answer

> The GIL is a process-wide lock in CPython that ensures only one thread executes Python bytecode at a time. It simplifies memory management and protects reference counting, but it limits true parallel execution for CPU-bound Python code.

---

# Q63. Why Doesn't Multithreading Improve CPU-bound Performance?

## Answer

CPU-bound programs spend nearly all their time executing Python instructions rather than waiting for I/O.

Because of the GIL:

```
Thread A → Running

Thread B → Waiting

Thread C → Waiting
```

Only one thread executes Python bytecode at a time.

---

## Example

```python
def square():
    total = 0
    for i in range(100_000_000):
        total += i * i
```

Running multiple threads does **not** utilize multiple CPU cores effectively.

---

## Why Can It Even Be Slower?

Additional overhead includes:

- Thread creation
- Context switching
- GIL acquisition/release
- Scheduler overhead

---

## Interview Answer

> Multithreading does not speed up CPU-bound workloads in CPython because the GIL allows only one thread to execute Python bytecode at a time. Multiple threads compete for the GIL, introducing scheduling overhead without achieving true parallelism.

---

# Q64. When Should Multiprocessing Be Preferred?

## Answer

Choose multiprocessing when:

- Heavy numerical computation
- Machine learning preprocessing
- Image or video processing
- Data analysis
- CPU-intensive algorithms

---

## Example

```python
from multiprocessing import Pool

def square(n):
    return n * n

with Pool() as pool:
    result = pool.map(square, range(10))

print(result)
```

---

## Why It Works

Each process:

- Has its own Python interpreter.
- Has its own GIL.
- Can execute on a separate CPU core.

---

## Interview Tip

Use multiprocessing when CPU utilization is the limiting factor and the overhead of inter-process communication is acceptable.

---

# Q65. Thread Safety

## Answer

A program is **thread-safe** if multiple threads can access shared resources without causing inconsistent or corrupted state.

---

## Unsafe Example

```python
counter = 0

def increment():
    global counter
    counter += 1
```

Multiple threads may overwrite each other's updates.

---

## Safe Version

```python
import threading

lock = threading.Lock()
counter = 0

def increment():
    global counter

    with lock:
        counter += 1
```

---

## Best Practices

- Minimize shared mutable state.
- Use locks only when necessary.
- Prefer immutable objects where possible.

---

# Q66. Race Conditions

## Answer

A race condition occurs when the final result depends on the timing of concurrent execution.

---

## Example

Initial value:

```
counter = 5
```

Two threads execute:

```
Read 5

Increment

Write 6
```

Expected:

```
7
```

Actual:

```
6
```

One update is lost.

---

## Solution

Protect critical sections with synchronization primitives such as `Lock`.

---

# Q67. Deadlock

## Answer

A deadlock occurs when two or more threads wait indefinitely for resources held by each other.

---

## Example

```
Thread A

Lock 1
↓

Waiting for Lock 2

-------------------

Thread B

Lock 2
↓

Waiting for Lock 1
```

Neither thread can continue.

---

## Prevention

- Acquire locks in a consistent order.
- Avoid nested locks when possible.
- Use timeouts.
- Reduce lock scope.

---

## Interview Tip

Deadlocks are easier to prevent than recover from.

---

# Q68. Producer-Consumer Problem

## Answer

One or more producers generate data.

One or more consumers process it.

A thread-safe queue coordinates them.

---

## Example

```python
from queue import Queue
import threading

queue = Queue()

def producer():

    for i in range(5):
        queue.put(i)

def consumer():

    while True:
        item = queue.get()
        print(item)
        queue.task_done()

threading.Thread(target=producer).start()
threading.Thread(target=consumer, daemon=True).start()

queue.join()
```

---

## Why Queue?

`Queue` provides built-in synchronization, avoiding explicit locking for many producer-consumer scenarios.

---

# Q69. ThreadPoolExecutor vs ProcessPoolExecutor

## Answer

Both are part of the `concurrent.futures` module.

---

## ThreadPoolExecutor

```python
from concurrent.futures import ThreadPoolExecutor
```

- Uses threads.
- Best for I/O-bound tasks.
- Shares memory.

---

## ProcessPoolExecutor

```python
from concurrent.futures import ProcessPoolExecutor
```

- Uses processes.
- Best for CPU-bound tasks.
- True parallelism.

---

## Comparison

| Feature | ThreadPoolExecutor | ProcessPoolExecutor |
|----------|--------------------|---------------------|
| Workers | Threads | Processes |
| Memory | Shared | Separate |
| GIL | Shared | One per process |
| Best For | I/O-bound | CPU-bound |

---

# Q70. Explain Locks, RLocks, and Semaphores

## Lock

A `Lock` allows only one thread into a critical section.

```python
lock = threading.Lock()

with lock:
    print("Critical section")
```

---

## RLock (Reentrant Lock)

The same thread can acquire an `RLock` multiple times.

Useful when nested function calls need the same lock.

```python
lock = threading.RLock()
```

---

## Semaphore

A semaphore limits the number of threads that may access a resource concurrently.

```python
sem = threading.Semaphore(3)

with sem:
    print("Access granted")
```

Only three threads can enter simultaneously.

---

## Comparison

| Primitive | Concurrent Access |
|------------|-------------------|
| Lock | 1 thread |
| RLock | 1 thread (reentrant) |
| Semaphore(5) | Up to 5 threads |

---

# Q71. Concurrent Futures

## Answer

`concurrent.futures` provides a high-level API for asynchronous execution with thread and process pools.

---

## Example

```python
from concurrent.futures import ThreadPoolExecutor

def square(n):
    return n * n

with ThreadPoolExecutor() as executor:

    futures = [
        executor.submit(square, i)
        for i in range(5)
    ]

    for future in futures:
        print(future.result())
```

Output

```
0
1
4
9
16
```

---

## Benefits

- Simpler than managing threads manually.
- Automatic worker pool management.
- Supports both thread and process executors.

---

# Q72. How Does the Scheduler Switch Threads?

## Answer

Thread scheduling is primarily handled by the **operating system scheduler**, while the **GIL** controls which thread may execute Python bytecode in CPython.

---

## Simplified Workflow

```
Thread A
     │
     ▼
Running

Time Slice Ends
     │
     ▼

OS Scheduler

     │
     ▼

Thread B
```

---

## Interaction with the GIL

For CPU-bound Python code:

1. The OS schedules a thread.
2. The thread attempts to acquire the GIL.
3. If successful, it executes Python bytecode.
4. After a periodic interval or when it blocks (for example, on I/O), it releases the GIL.
5. Another ready thread may then acquire the GIL and continue execution.

---

## Context Switching

During a thread switch, the operating system saves:

- CPU registers
- Program counter
- Stack pointer
- Execution state

It then restores the state of the next scheduled thread.

This context-switching overhead is one reason why excessive threading can reduce performance for CPU-bound workloads.

---

## Interview Answer

> The operating system scheduler decides which thread gets CPU time. In CPython, even after a thread is scheduled by the OS, it must acquire the GIL before executing Python bytecode. Threads voluntarily release the GIL during blocking I/O, and the interpreter also periodically switches between CPU-bound threads to provide fairness. This combination of OS scheduling and GIL arbitration determines which thread runs at any moment.

---

## Senior Interview Summary

### Use **Threading** when:
- HTTP requests
- Database access
- File I/O
- Network communication
- Waiting on external services

### Use **Multiprocessing** when:
- Image processing
- Machine learning
- Data science
- Video encoding
- Heavy numerical computation

### Remember

- GIL limits CPU-bound multithreading in CPython.
- Threading is excellent for I/O-bound workloads.
- Multiprocessing enables true parallelism across CPU cores.
- Protect shared state with synchronization primitives (`Lock`, `RLock`, `Semaphore`).
- Use `ThreadPoolExecutor` and `ProcessPoolExecutor` for simpler, production-ready concurrency management.

---

**End of Module 6 (Questions 61–72)**