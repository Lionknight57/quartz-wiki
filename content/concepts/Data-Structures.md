---
title: Data Structures
tags: [concepts, algorithms, computer-science]
related: [CSharp, Async Programming]
sources: [Data Structures.md]
updated: 2026-04-19
---

# Data Structures

## Arrays

- **Contiguous** memory allocation
- **Indexed** — O(1) random access
- Fixed size (in most languages); dynamic arrays (e.g. `List<T>` in C#) double capacity on resize

**Complexity:**
| Operation | Array | Dynamic Array |
|---|---|---|
| Access | O(1) | O(1) |
| Search | O(n) | O(n) |
| Insert (end) | — | O(1) amortised |
| Insert (middle) | O(n) | O(n) |

---

## Linked Lists

Nodes where each node points to the next (and optionally previous).

### Singly Linked List
- Has a **head** pointer
- Traverse forward only

### Doubly Linked List
- Has **head** and **tail** pointers
- Traverse both directions

### Sorted List
- Maintained in order on insert

**Complexity:**
| Operation | Complexity |
|---|---|
| AddHead / AddTail | O(1) |
| Find / Contains / Remove / Enumerate | O(n) |
| Add (sorted) | O(n) |

---

## Stack

- **LIFO** — Last In, First Out
- Operations: `Push`, `Pop`, `Peek`
- Use cases: undo/redo, call stack, expression parsing, DFS

**Complexity:** Push/Pop/Peek all O(1)

---

## Queue

- **FIFO** — First In, First Out
- Operations: `Enqueue`, `Dequeue`, `Peek`
- Use cases: BFS, job queues, print spoolers

**Complexity:** Enqueue/Dequeue all O(1)

---

## Binary Trees

Each node has at most two children: **left** and **right**.

### Binary Search Tree (BST)
- Left child < parent < right child
- Enables O(log n) search when balanced

### Tree Traversals

**Pre-Order** (Root → Left → Right)
```
visit node → traverse left → traverse right
```

**In-Order** (Left → Root → Right)
```
traverse left → visit node → traverse right
```
> In-order traversal of a BST produces sorted output.

**Post-Order** (Left → Right → Root)
```
traverse left → traverse right → visit node
```

**Complexity:**
| Operation | Average | Worst (unbalanced) |
|---|---|---|
| Insertion | O(log n) | O(n) |
| Traversal | O(n) | O(n) |
| Search | O(log n) | O(n) |

---

## Hash Tables

- Key → hash function → bucket index → value
- O(1) average insert/lookup
- Collisions resolved by chaining or open addressing
- In C#: `Dictionary<TKey, TValue>`

**Good hash function properties:**
- **Stability** — same input always produces same output
- **Uniformity** — distributes keys evenly across buckets (minimises collisions)
- **Security** — for sensitive data: use cryptographic hash (e.g. SHA-256); standard hashes are not collision-resistant
- **Fill factor / growth factor** — when occupancy exceeds the fill factor the table resizes (re-hashes all entries)

---

## AVL Tree

A **self-balancing** Binary Search Tree. After every insert or delete, rotations are applied to keep the tree height O(log n) — preventing the O(n) worst case of an unbalanced BST.

**Balance factor** = height(left subtree) − height(right subtree)  
Must be −1, 0, or +1 at every node. Rotations (left, right, left-right, right-left) restore balance when this invariant is violated.

**Complexity:** always O(log n) for insert, delete, search — no worst-case degradation.

---

## Sets

A collection of **unique** values with no guaranteed order. Supports mathematical set operations.

| Operation | Description | C# method |
|---|---|---|
| **Union** | All elements from both sets | `UnionWith(other)` |
| **Intersection** | Only elements in both sets | `IntersectWith(other)` |
| **Set Difference** | Elements in A but not in B | `ExceptWith(other)` |
| **Symmetric Difference** | Elements in either set but not both | `SymmetricExceptWith(other)` |

**C# implementation:** `HashSet<T>` — backed by a hash table, O(1) average for add/contains/remove.

```csharp
var a = new HashSet<int> { 1, 2, 3 };
var b = new HashSet<int> { 2, 3, 4 };

a.IntersectWith(b);          // a = { 2, 3 }
a.UnionWith(b);              // a = { 1, 2, 3, 4 }
a.ExceptWith(b);             // a = { 1 }
a.SymmetricExceptWith(b);    // a = { 1, 4 }
```

---

## Sorting Algorithms

Two families: **linear** (compare adjacent elements) and **divide and conquer** (split, sort, merge).

| Algorithm | Average | Worst | Space | Notes |
|---|---|---|---|---|
| **Bubble Sort** | O(n²) | O(n²) | O(1) | Simple; rarely used in production |
| **Insertion Sort** | O(n²) | O(n²) | O(1) | Efficient for small or nearly-sorted data |
| **Selection Sort** | O(n²) | O(n²) | O(1) | Minimises swaps; not stable |
| **Merge Sort** | O(n log n) | O(n log n) | O(n) | Stable; can be parallelised |
| **Quick Sort** | O(n log n) | O(n²) | O(log n) | Fast in practice; worst case on sorted input |

**Merge Sort** is the go-to for stable, predictable sort — used in .NET's `Array.Sort` for reference types. **Quick Sort** is used for value types (lower allocation cost, average-case speed).

---

## String Search

**Boyer-Moore-Horspool** — a practical string search algorithm:
- Preprocesses the pattern (not the text)
- Skips ahead multiple characters on mismatch — often faster than O(n·m) naive search
- Average case sub-linear on typical text; worst case O(n·m)
- Better than KMP for most real-world inputs with large alphabets (English text, code)

```
Text:    "the quick brown fox"
Pattern: "brown"
→ align, compare right to left, skip on mismatch using the bad-character shift table
```

---

## Big O Notation

**Big O** = upper bound on the growth rate of an algorithm's resource usage.

| Notation | Name | Example |
|---|---|---|
| O(1) | Constant | Array index access, hash table lookup |
| O(log n) | Logarithmic | Binary search, balanced BST |
| O(n) | Linear | Traversal, linear search |
| O(n log n) | Linearithmic | Merge Sort, efficient sorts |
| O(n²) | Quadratic | Bubble/Insertion/Selection sort |
| O(2ⁿ) | Exponential | Brute-force combinatorics |

**What it measures:** usually CPU operations, but asymptotic analysis also applies to memory, network transfers, compression ratios, and disk I/O.

**Best vs worst case:** Big O typically describes the worst case. Average case is often stated separately (e.g. Quick Sort average O(n log n), worst O(n²)).

---

## Collection Concurrency (.NET)

Multiple threads accessing a shared collection simultaneously causes race conditions. Three approaches:

| Approach | Mechanism | Use when |
|---|---|---|
| Caller synchronisation | `lock (_obj) { ... }` | Simple; wraps any existing collection |
| Monitor locking | `Monitor.Enter` / `Monitor.Exit` | Same as lock, more control |
| Read/Write locking | `ReaderWriterLockSlim` | Many readers, rare writers |
| Concurrent collections | Built-in thread-safe types | High throughput, no manual locking |

**.NET concurrent collection types:**

| Type | Thread-safe equivalent |
|---|---|
| `ConcurrentDictionary<K,V>` | `Dictionary<K,V>` — type-safe |
| `ConcurrentQueue<T>` | `Queue<T>` — FIFO |
| `ConcurrentStack<T>` | `Stack<T>` — LIFO |
| `ConcurrentBag<T>` | Unordered bag — allows duplicates |

> See [[CSharp#Concurrent collections (thread-safe)]] for code examples.

---

## Complexity Reference

| Structure | Access | Search | Insert | Delete | Notes |
|---|---|---|---|---|---|
| Array | O(1) | O(n) | O(n) | O(n) | O(1) insert at end (dynamic array, amortised) |
| Linked List | O(n) | O(n) | O(1) head/tail | O(1) head/tail | O(n) to find mid-list node |
| Stack | O(n) | O(n) | O(1) | O(1) | Push/Pop/Peek |
| Queue | O(n) | O(n) | O(1) | O(1) | Enqueue/Dequeue |
| BST (avg) | — | O(log n) | O(log n) | O(log n) | Degrades to O(n) if unbalanced |
| AVL Tree | — | O(log n) | O(log n) | O(log n) | Always balanced — no worst case |
| Hash Table | — | O(1) avg | O(1) avg | O(1) avg | O(n) worst case (all collisions) |
| HashSet<T> | — | O(1) avg | O(1) avg | O(1) avg | Set operations O(n) |

## See also
- [[CSharp#Collections]] — `List<T>`, `Dictionary<TKey,TValue>`, `Stack<T>`, `Queue<T>`
- [[CSharp#Concurrent collections (thread-safe)]] — `ConcurrentDictionary`, `ConcurrentQueue`
- [[Design-Patterns]] — structural patterns that rely on these structures
- [[Async-Programming]] — concurrent queues
