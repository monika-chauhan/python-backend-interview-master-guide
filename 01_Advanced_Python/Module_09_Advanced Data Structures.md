# Module 9: Advanced Data Structures (Questions 91–95)

# Q91. Implement an LRU Cache

## Answer

An **LRU (Least Recently Used) Cache** is a cache eviction policy where the **least recently accessed item is removed first** when the cache reaches its capacity.

LRU caches are widely used in:

- Web browsers
- Database query caching
- Redis
- Operating systems
- API response caching

---

## Example

Capacity = **3**

```
put(1)

Cache:
1

----------------

put(2)

Cache:
2 1

----------------

put(3)

Cache:
3 2 1

----------------

get(1)

Cache:
1 3 2

----------------

put(4)

Cache:
4 1 3

2 is removed
```

---

# Naive Solution

Use a dictionary.

Problem:

Finding the least recently used element requires scanning.

```
Time Complexity

get()

O(n)

put()

O(n)
```

Too slow.

---

# Optimal Solution

Use:

- HashMap (Dictionary)
- Doubly Linked List

```
Dictionary

Key

↓

Node

↓

Doubly Linked List

Most Recent

↓

Least Recent
```

---

## Why Both?

Dictionary

```
O(1)

Lookup
```

Doubly Linked List

```
O(1)

Delete

Move

Insert
```

---

# Data Structure

```
Head

↓

A ⇄ B ⇄ C

↑

Tail

Most Recent

Least Recent
```

---

## Implementation

```python
class Node:

    def __init__(self, key, value):

        self.key = key
        self.value = value

        self.prev = None
        self.next = None


class LRUCache:

    def __init__(self, capacity):

        self.capacity = capacity
        self.cache = {}

        self.head = Node(0, 0)
        self.tail = Node(0, 0)

        self.head.next = self.tail
        self.tail.prev = self.head

    def remove(self, node):

        prev = node.prev
        nxt = node.next

        prev.next = nxt
        nxt.prev = prev

    def insert(self, node):

        node.next = self.head.next
        node.prev = self.head

        self.head.next.prev = node
        self.head.next = node

    def get(self, key):

        if key not in self.cache:
            return -1

        node = self.cache[key]

        self.remove(node)
        self.insert(node)

        return node.value

    def put(self, key, value):

        if key in self.cache:

            self.remove(self.cache[key])

        node = Node(key, value)

        self.insert(node)

        self.cache[key] = node

        if len(self.cache) > self.capacity:

            lru = self.tail.prev

            self.remove(lru)

            del self.cache[lru.key]
```

---

## Complexity

| Operation | Complexity |
|------------|------------|
| get() | O(1) |
| put() | O(1) |

---

## Python Built-in

```python
from functools import lru_cache

@lru_cache(maxsize=128)

def fibonacci(n):

    if n < 2:
        return n

    return fibonacci(n-1) + fibonacci(n-2)
```

---

## Senior Interview Tip

The optimal LRU Cache solution combines a **dictionary** for O(1) lookups with a **doubly linked list** for O(1) insertion, deletion, and eviction.

---

# Q92. OrderedDict Internals

## Answer

`OrderedDict` is a specialized dictionary from the `collections` module that preserves insertion order and supports efficient reordering operations.

> **Note:** Since Python **3.7**, the built-in `dict` also preserves insertion order. `OrderedDict` remains useful because it provides APIs such as `move_to_end()` and customizable order-sensitive equality.

---

## Example

```python
from collections import OrderedDict

od = OrderedDict()

od["A"] = 1
od["B"] = 2
od["C"] = 3

print(od)
```

Output

```
OrderedDict([
('A',1),
('B',2),
('C',3)
])
```

---

## Internal Structure

```
Dictionary

↓

Key

↓

Node

↓

Doubly Linked List
```

```
Head

↓

A ⇄ B ⇄ C

↓

Tail
```

---

## move_to_end()

```python
od.move_to_end("A")
```

Result

```
B

↓

C

↓

A
```

---

## popitem()

```python
od.popitem(last=False)
```

Removes the oldest item.

---

## Complexity

| Operation | Complexity |
|------------|------------|
| Insert | O(1) |
| Delete | O(1) |
| Lookup | O(1) |
| move_to_end | O(1) |

---

## Interview Tip

Modern `dict` preserves insertion order, but `OrderedDict` is still valuable when frequent reordering or order-aware operations are required.

---

# Q93. Heap Implementation

## Answer

A **Heap** is a complete binary tree that satisfies the heap property.

---

## Min Heap

```
        2
      /   \
     5     8
    / \
   9  12
```

Every parent is **less than or equal to** its children.

---

## Max Heap

```
       20
      /  \
    10    15
```

Every parent is **greater than or equal to** its children.

---

## Python Implementation

Python provides a **Min Heap** using the `heapq` module.

```python
import heapq

heap = []

heapq.heappush(heap, 10)
heapq.heappush(heap, 5)
heapq.heappush(heap, 20)

print(heapq.heappop(heap))
```

Output

```
5
```

---

## Heap Operations

```
Insert

↓

Bubble Up

-----------------

Delete Root

↓

Bubble Down
```

---

## Priority Queue Example

```python
import heapq

tasks = []

heapq.heappush(tasks, (2, "Low"))
heapq.heappush(tasks, (1, "High"))
heapq.heappush(tasks, (3, "Very Low"))

while tasks:
    print(heapq.heappop(tasks))
```

Output

```
(1, 'High')

(2, 'Low')

(3, 'Very Low')
```

---

## Complexity

| Operation | Complexity |
|------------|------------|
| Insert | O(log n) |
| Delete Min | O(log n) |
| Peek | O(1) |
| Build Heap | O(n) |

---

## Use Cases

- Dijkstra's Algorithm
- Priority Queues
- Task Scheduling
- Huffman Coding
- Top-K Problems

---

## Interview Tip

Python's `heapq` implements only a **min-heap**. To simulate a max-heap, store negated numeric priorities or wrap comparisons appropriately.

---

# Q94. Trie Implementation

## Answer

A **Trie (Prefix Tree)** is a tree-based data structure optimized for storing and searching strings by prefix.

---

## Example

Words

```
cat

car

can
```

Trie

```
        root

          |

          c

          |

          a

     /    |    \

    t     r     n
```

---

## Node Structure

```python
class TrieNode:

    def __init__(self):

        self.children = {}
        self.is_word = False
```

---

## Trie Implementation

```python
class Trie:

    def __init__(self):

        self.root = TrieNode()

    def insert(self, word):

        node = self.root

        for char in word:

            node = node.children.setdefault(
                char,
                TrieNode()
            )

        node.is_word = True

    def search(self, word):

        node = self.root

        for char in word:

            if char not in node.children:
                return False

            node = node.children[char]

        return node.is_word

    def starts_with(self, prefix):

        node = self.root

        for char in prefix:

            if char not in node.children:
                return False

            node = node.children[char]

        return True
```

---

## Complexity

| Operation | Complexity |
|------------|------------|
| Insert | O(L) |
| Search | O(L) |
| Prefix Search | O(L) |

`L` = length of the word.

---

## Applications

- Autocomplete
- Spell Checker
- Search Suggestions
- Dictionary Lookup
- IP Routing

---

## Interview Tip

Tries trade higher memory usage for extremely efficient prefix searches.

---

# Q95. Bloom Filter Implementation

## Answer

A **Bloom Filter** is a **space-efficient probabilistic data structure** used to test whether an element is **possibly in a set** or **definitely not in the set**.

It is commonly used in:

- Databases
- Distributed caches
- Web crawlers
- Spam filters
- Storage systems

---

## Key Properties

- No false negatives.
- False positives are possible.
- Very memory efficient.
- Deletion is not supported in the basic Bloom Filter.

---

## Visualization

Initially

```
Bit Array

0 0 0 0 0 0 0 0
```

Insert

```
apple
```

Hash functions

```
h1 → 1

h2 → 4

h3 → 7
```

Bit Array

```
0 1 0 0 1 0 0 1
```

---

## Simplified Implementation

```python
class BloomFilter:

    def __init__(self, size):

        self.size = size
        self.bits = [0] * size

    def _hashes(self, item):

        return [
            hash(item) % self.size,
            hash(item + "1") % self.size,
            hash(item + "2") % self.size
        ]

    def add(self, item):

        for index in self._hashes(item):
            self.bits[index] = 1

    def contains(self, item):

        return all(
            self.bits[index]
            for index in self._hashes(item)
        )
```

---

## Example

```python
bf = BloomFilter(20)

bf.add("apple")

print(bf.contains("apple"))

print(bf.contains("banana"))
```

Output

```
True

False
```

A result of `True` means "possibly present"; `False` means "definitely not present."

---

## Complexity

| Operation | Complexity |
|------------|------------|
| Insert | O(k) |
| Lookup | O(k) |
| Memory | O(m) |

Where:

- `k` = number of hash functions
- `m` = size of the bit array

---

## Advantages

- Extremely memory efficient.
- Constant-time insert and lookup.
- Excellent for membership testing.

---

## Limitations

- False positives are possible.
- Basic Bloom Filters do not support deletion.
- Accuracy decreases as the filter becomes saturated.

---

## Real-World Applications

- Google Bigtable
- Apache Cassandra
- Apache HBase
- Redis modules
- Chrome Safe Browsing
- Web crawler URL deduplication

---

## Senior Interview Summary

### Data Structure Selection Guide

| Data Structure | Best Use Case | Time Complexity |
|----------------|---------------|-----------------|
| LRU Cache | Fast caching with eviction | O(1) get / put |
| OrderedDict | Ordered mappings with efficient reordering | O(1) operations |
| Heap | Priority queues, Top-K, scheduling | O(log n) insert/delete |
| Trie | Prefix search and autocomplete | O(L) |
| Bloom Filter | Fast membership testing with low memory | O(k) |

### Key Takeaways

- **LRU Cache:** Dictionary + Doubly Linked List for O(1) operations.
- **OrderedDict:** Useful when order manipulation (`move_to_end`, `popitem`) is required.
- **Heap:** Python's `heapq` is a min-heap; negate priorities for max-heap behavior.
- **Trie:** Optimized for prefix searches and autocomplete.
- **Bloom Filter:** Provides memory-efficient membership checks with possible false positives but no false negatives.

---

**End of Module 9 (Questions 91–95)**