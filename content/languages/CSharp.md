---
title: C#
tags: [language, dotnet, csharp]
related: [Async Programming, Dependency Injection, SOLID Principles, Entity Framework, Design Patterns, LINQ]
sources: [csharp.md, Dynamic CSharp.md, Asynchronous Programming in .NET.md, Async Programming.md, c#/Advanced Collections.md, c#/Concurrent Collections.md, c#/Threading.md, c#/Extension Methods.md, c#/Tips and Traps.md, c#/CSharp 9.0.md, c#/Basic.md, c#/Api In CSharp.md, Linq.md, Medium/2026-04/Medium Daily Digest - 2026-04-14.md, Medium/2026-04/Medium Daily Digest - 2026-04-15.md, Clippings/C 15's Newest Feature Is More Dangerous Than It Looks.md, Medium/2026-04/Medium Daily Digest - 2026-04-18.md, Medium/2026-04/Medium Daily Digest - 2026-04-19.md, Medium/2026-04/Medium Daily Digest - 2026-04-20.md, Medium/2026-04/Medium Daily Digest - 2026-04-21.md, Medium/2026-05/Medium Daily Digest - 2026-05-26.md, Medium/2026-05/Medium Daily Digest - 2026-05-27.md, Medium/2026-05/Medium Daily Digest - 2026-05-28.md, Medium/2026-05/Medium Daily Digest - 2026-05-30.md, Medium/2026-06/Medium Daily Digest - 2026-06-23.md, Medium/2026-06/Medium Daily Digest - 2026-06-24.md, Medium/2026-06/Medium Daily Digest - 2026-06-25.md, Medium/2026-06/Medium Daily Digest - 2026-06-26.md, Medium/2026-06/Medium Daily Digest - 2026-06-27.md]
updated: 2026-06-27
---

# C#

Microsoft's primary language for .NET development. Statically typed, object-oriented with functional features added progressively.

## Version history

| Version | Year | Key feature |
|---|---|---|
| 1.0 | 2002 | Initial release |
| 2.0 | 2005 | Generics (`List<T>`, `Dictionary<K,V>`) |
| 3.0 | 2007 | LINQ, lambda expressions, extension methods |
| 4.0 | 2010 | `dynamic` keyword |
| 5.0 | 2012 | `async` / `await` |
| 6.0 | 2014 | String interpolation, null-conditional `?.` |
| 7.0 | 2017 | Tuples, pattern matching, `out` variables |
| 8.0 | 2019 | Nullable reference types, `switch` expressions |
| 9.0 | 2020 | Records, init-only properties |
| 10.0 | 2021 | Global usings, file-scoped namespaces |
| 11.0 | 2022 | Raw string literals, required members |
| 12.0 | 2023 | Primary constructors, collection expressions |
| 13.0 | 2024 | `params` collections, new `Lock` type, `\e` escape sequence |
| 14.0 | 2025 | `field` keyword, implicit span conversions |
| 15.0 | 2026 | **Union types** (see section below) |

---

## Core concepts

### Generics (C# 2.0+)
```csharp
List<int> numbers = new List<int>();
Dictionary<string, User> users = new Dictionary<string, User>();
```
Type-safe collections without boxing/unboxing. See [[Data-Structures]] for complexity of generic collection types.

### LINQ (C# 3.0+)
```csharp
var adults = people.Where(p => p.Age >= 18)
                   .OrderBy(p => p.Name)
                   .Select(p => p.Name);
```
Query syntax works over any `IEnumerable<T>`. Also supports `IQueryable<T>` for DB queries (see [[Entity-Framework]]).

#### ToLookup() — one key → many values

`ILookup<TKey, TValue>` is the underused alternative to `Dictionary<TKey, List<T>>` for grouping. It is **immediately executed** (unlike `GroupBy()` which is deferred) and **immutable** (read-only after creation).

```csharp
// Instead of manually building Dictionary<int, List<Employee>>
ILookup<int, Employee> lookup = employees.ToLookup(e => e.DepartmentId);

// Access — never throws on missing keys, returns empty collection
var hr = lookup[1];    // all dept-1 employees
var none = lookup[999]; // empty, no KeyNotFoundException
```

**ToLookup() vs GroupBy():**

| | `GroupBy()` | `ToLookup()` |
|---|---|---|
| Execution | Deferred (lazy) | Immediate (eager) |
| Mutability | Enumerable | Immutable, cached |
| Best for | Single pass, LINQ pipelines | Repeated grouped lookups |
| Missing key | N/A | Returns empty collection |

**When to reach for ToLookup():** repeated access by key, read-heavy grouping, caching grouped results. Classic use cases: notifications by user, products by category, orders by customer, logs by severity.

```csharp
// Performance win — build lookup once, access many times
var lookup = employees.ToLookup(e => e.DepartmentId);
foreach (var dept in departments)
{
    var staff = lookup[dept.Id]; // O(1) indexed access, not O(n) scan
}
```

### Dynamic (C# 4.0+)
```csharp
dynamic obj = GetSomeObject();
obj.SomeMethod(); // resolved at runtime, not compile time
```
Bypasses static type checking. Useful for interop with COM, JSON, or reflection-heavy code.

### Async / Await (C# 5.0+)
See [[Async-Programming]] for full detail.
```csharp
public async Task<string> GetDataAsync()
{
    var result = await httpClient.GetStringAsync(url);
    return result;
}
```

### Null-Conditional Operator (C# 6.0+)
```csharp
string name = user?.Address?.City;  // null if any link is null
int? length = user?.Name?.Length;
```

---

## Collections

| Type | Description | Complexity |
|---|---|---|
| `List<T>` | Dynamic array | O(1) access, O(n) insert middle |
| `Dictionary<K,V>` | Hash map | O(1) lookup |
| `Stack<T>` | LIFO | O(1) push/pop |
| `Queue<T>` | FIFO | O(1) enqueue/dequeue |
| `HashSet<T>` | Unique values | O(1) contains |
| `SortedList<K,V>` | Sorted key-value | O(log n) insert |
| `ConcurrentDictionary<K,V>` | Thread-safe map | O(1) avg |

---

## Roslyn compiler

The open-source C# compiler platform. Enables:
- Code analysis and refactoring
- Source generators (compile-time code generation)
- IDE tooling (Intellisense, diagnostics)

---

## Design patterns in C#

See [[Design-Patterns]] and [[Dependency-Injection]].

Common patterns:
- **Null Object / Special Case** — avoids null reference exceptions by returning a "do nothing" or informative object instead of null
- **Factory** — creates objects without exposing instantiation logic
- **Repository** — abstracts data access behind an interface

---

## LINQ

Language-Integrated Query — query collections using a unified syntax (works on arrays, lists, Entity Framework, XML, etc.).

**Two syntaxes — equivalent:**
```csharp
// Query syntax (SQL-like)
var result = from p in products
             where p.Price > 10
             orderby p.Name
             select p.Name;

// Method syntax (lambda chains — more common)
var result = products
    .Where(p => p.Price > 10)
    .OrderBy(p => p.Name)
    .Select(p => p.Name);
```

**Common operators:**

| Operator | Description |
|---|---|
| `Where(pred)` | Filter |
| `Select(proj)` | Project / transform |
| `OrderBy` / `OrderByDescending` | Sort |
| `GroupBy(key)` | Group into `IGrouping<K,V>` |
| `Join` | Inner join two sequences |
| `GroupJoin` | Left outer join |
| `First` / `FirstOrDefault` | First matching element |
| `Single` / `SingleOrDefault` | Exactly one match |
| `Any(pred)` / `All(pred)` | Existential / universal check |
| `Count` / `Sum` / `Min` / `Max` / `Average` | Aggregates |
| `Distinct` | Remove duplicates |
| `Take(n)` / `Skip(n)` | Pagination |
| `ToList()` / `ToArray()` / `ToDictionary()` | Materialise |

**Deferred execution** — LINQ queries don't execute until enumerated (`.ToList()`, `foreach`). Calling `.ToList()` forces immediate execution.

```csharp
// Join example
var results = from o in orders
              join c in customers on o.CustomerId equals c.Id
              select new { o.OrderDate, c.Name };

// GroupBy example
var byCategory = products
    .GroupBy(p => p.Category)
    .Select(g => new { Category = g.Key, Count = g.Count() });
```

---

## Collections

### Choosing the right collection

| Collection | Best for | Key characteristic |
|---|---|---|
| `T[]` Array | Fixed-size, fast index access | Size immutable after creation |
| `List<T>` | General purpose ordered list | Dynamic size; slow inserts at start |
| `LinkedList<T>` | Frequent insert/remove at any position | O(1) insert, no random access |
| `Dictionary<K,V>` | Fast key lookup | O(1) average; keys must be unique |
| `HashSet<T>` | Unique elements, fast membership test | O(1) contains |
| `SortedDictionary<K,V>` | Sorted key lookup | O(log n) |
| `Queue<T>` | FIFO | Enqueue / Dequeue |
| `Stack<T>` | LIFO | Push / Pop |
| `ImmutableList<T>` | Read-only snapshots, thread-safe reads | Any "modification" returns a new list |

### Read-only vs immutable

```csharp
IReadOnlyList<T>   // can still be mutated by the owner — just hides mutation API
ImmutableList<T>   // truly immutable — any "add/remove" returns new collection
```

### Concurrent collections (thread-safe)

All implement `IProducerConsumerCollection<T>` with `TryAdd` / `TryTake`:

| Type | Thread-safe equivalent of |
|---|---|
| `ConcurrentDictionary<K,V>` | `Dictionary<K,V>` |
| `ConcurrentQueue<T>` | `Queue<T>` |
| `ConcurrentStack<T>` | `Stack<T>` |
| `ConcurrentBag<T>` | Unordered bag |

**Avoiding race conditions with `ConcurrentDictionary`:**
```csharp
// BAD — two separate operations, race possible
if (dict.ContainsKey(key)) dict[key] = newValue;

// GOOD — atomic single operation
dict.AddOrUpdate(key, newValue, (k, old) => newValue);
dict.GetOrAdd(key, k => ComputeValue(k));
```

Use `TryGetValue` / `TryRemove` to safely check-and-retrieve atomically.

---

## yield — lazy sequences

`yield` lets a method produce one item at a time instead of building a full list upfront. The method pauses at each `yield return`, hands the value to the caller, then resumes from exactly that point on the next request.

```csharp
IEnumerable<int> CountUp()
{
    yield return 1;
    yield return 2;
    yield return 3;
}

foreach (var n in CountUp())
    Console.WriteLine(n); // 1, 2, 3
```

**How it works:** The compiler turns the method into a hidden state machine. Each call to `MoveNext()` (what `foreach` calls internally) runs the method body until the next `yield return`, saves the position, and returns the value. The method body never runs until something iterates it.

**`yield break`** — exits the sequence early:
```csharp
IEnumerable<int> Until10(IEnumerable<int> source)
{
    foreach (var n in source)
    {
        if (n >= 10) yield break;
        yield return n;
    }
}
```

**Why it matters — lazy evaluation:**
```csharp
// Without yield — allocates ALL million ints before you see any
var all = Enumerable.Range(1, 1_000_000).ToList();

// With yield — produces one int at a time, stops when you stop asking
IEnumerable<int> Infinite()
{
    int i = 0;
    while (true) yield return i++;
}

var first10 = Infinite().Take(10); // only 10 items ever produced
```

Key benefits:
- **Memory** — never holds more than one item at a time
- **Performance** — stops producing the moment the caller stops consuming
- **Infinite sequences** — safe to model endless streams (events, sensor data, paged APIs)

**Common pattern — paged API results:**
```csharp
async IAsyncEnumerable<Order> GetAllOrdersAsync()
{
    int page = 1;
    while (true)
    {
        var batch = await api.GetOrdersAsync(page++);
        if (batch.Count == 0) yield break;
        foreach (var order in batch)
            yield return order;
    }
}
```

> Pair with `IAsyncEnumerable<T>` (C# 8+) for async lazy streams — see Performance techniques section.

---

## Threading

Threads enable parallelism and responsive UIs. All threads in a process share the heap.

```csharp
Thread t = new Thread(DoWork);
t.Start();
t.Join();   // wait for t to finish

Thread.Sleep(500);   // pause current thread (ms)
Thread.Yield();      // yield to threads on same processor
```

**Common uses:**
- Keep UI responsive while background work runs
- Efficient CPU use when I/O would block
- Parallel computation
- Handle simultaneous requests

**Thread safety:** Shared data accessed from multiple threads needs synchronisation:
```csharp
private readonly object _lock = new object();
lock (_lock) { /* critical section */ }
```

> **Prefer `Task` / `async-await` over raw `Thread`** for most modern code — see [[Async-Programming]].

**Passing data to a thread:**
```csharp
Thread t = new Thread(() => ProcessOrder(orderId));
t.Start();
```

---

## Extension Methods

Add methods to existing types without modifying or subclassing them. Implements the Open/Closed principle (see [[SOLID-Principles]]).

```csharp
public static class StringExtensions
{
    public static bool IsNullOrEmpty(this string value) =>
        string.IsNullOrEmpty(value);

    public static string Truncate(this string value, int maxLength) =>
        value.Length <= maxLength ? value : value[..maxLength] + "...";
}

// Usage — reads like an instance method
"Hello World".Truncate(5);   // "Hello..."
```

**Rules:**
- Must be in a `static class`
- Method must be `static`
- First parameter is the extended type, prefixed with `this`

**Best practices:**
- Use sparingly — prefer instance methods when you own the type
- Target specific types, not broad interfaces
- Put in a separate assembly / namespace
- Keep names clear and avoid dependencies

---

## C# tips & traps

**String null/empty checking:**
```csharp
string.IsNullOrEmpty(s)        // null or ""
string.IsNullOrWhiteSpace(s)   // null, "", or whitespace only
```

**String interpolation:**
```csharp
$"Hello {name}, you are {age} years old"
$"{price:C2}"      // currency: $9.99
$"{value:N2}"      // number:   1,234.56
$"{pct:P1}"        // percent:  75.0%
```

**`StringBuilder` for many concatenations:**
```csharp
var sb = new StringBuilder();
foreach (var item in items) sb.AppendLine(item.ToString());
string result = sb.ToString();
```

**Safe casting:**
```csharp
var x = obj as MyType;          // returns null if wrong type
if (obj is MyType typed) { }    // pattern matching — preferred
```

**Tuples to return multiple values:**
```csharp
(string Name, int Age) GetInfo() => ("Ken", 30);
var (name, age) = GetInfo();
```

**Local functions:**
```csharp
int Factorial(int n)
{
    return Inner(n);
    int Inner(int x) => x <= 1 ? 1 : x * Inner(x - 1);
}
```

**`CallerMemberName` attribute:**
```csharp
void Log([CallerMemberName] string method = "") =>
    Console.WriteLine($"Called from: {method}");
```

**`BitConverter` — base types to bytes:**
```csharp
byte[] bytes = BitConverter.GetBytes(42);
int back = BitConverter.ToInt32(bytes, 0);
```

---

## Useful .NET packages (hidden gems)

From Gulam Ali H.'s "I Was Writing Too Much Code Until I Found These .NET Packages":

| Package | What it does | Use case |
|---|---|---|
| **Mapster** | Object-to-object mapping | Replace manual DTO mappings; faster than AutoMapper |
| **FluentValidation** | Rule-based model validation | Replaces if/else validation chains |
| **Polly** | Resilience and transient fault handling | Retry policies, circuit breakers for HTTP calls |
| **Serilog** | Structured logging | Replaces Console.WriteLine and basic ILogger |
| **MediatR** | Mediator pattern / CQRS | Decouple command/query handlers from controllers |
| **Bogus** | Fake data generation | Test data, seed data for dev environments |
| **Hangfire** | Background job scheduling | Replace manual `Task.Run` fire-and-forget jobs |
| **Scrutor** | Assembly scanning for DI | Auto-register services by convention |

> "I Was Writing Too Much Code Until I Found These .NET Packages" — Gulam Ali H. (CodeToDeploy)

---

## Performance techniques

From Gulam Ali H.'s "5 C# Techniques You Start Using When Performance Actually Matters" — beyond the standard async/await:

### `Span<T>` and `Memory<T>` — zero-copy slicing
```csharp
// Instead of creating substrings (allocates)
string sub = input.Substring(0, 10);

// Use Span — no allocation
ReadOnlySpan<char> span = input.AsSpan(0, 10);
```
Key for high-throughput parsing (network protocols, file formats). Avoids GC pressure on hot paths.

### `ArrayPool<T>` — pool and reuse buffers
```csharp
var pool = ArrayPool<byte>.Shared;
byte[] buffer = pool.Rent(4096);
try { /* use buffer */ }
finally { pool.Return(buffer); }
```
Avoids repeated large array allocations. Critical in server code handling many requests.

#### Going deeper: `Span` vs `Memory` (the async boundary) + a GC primer

The reason `string.Split(',')[0]` quietly hurts at scale: every call allocates a new array *plus* a string per segment. At 10k req/s the GC earns its salary and p99 latency pays. The fix isn't tuning GC settings — it's not allocating in the first place.

- **`Span<T>` is a `ref struct`** → guaranteed stack-only, never promoted to the heap, allocated/freed essentially free. That same constraint means it **cannot be used in `async` methods, cannot be a class field, and cannot cross an `await`** — code that tries won't compile.
- **`Memory<T>` is the async-safe counterpart** — same zero-copy idea without the ref-struct restriction, so it can live in async methods, be stored as a field, and be passed across boundaries. **The pattern: carry `Memory<T>` through async code, then drop to `.Span` at the point you actually process the data.**

```csharp
// Zero-allocation first-segment with Span (sync hot path)
public ReadOnlySpan<char> GetFirstSegment(string input)
{
    ReadOnlySpan<char> span = input.AsSpan();
    int comma = span.IndexOf(',');
    return span.Slice(0, comma);   // points into the original string — no new alloc
}

// Async-safe: Memory across the await, Span at the work site
public async Task ReadFileAsync(string path)
{
    byte[] buffer = ArrayPool<byte>.Shared.Rent(4096);
    try
    {
        using var stream = File.OpenRead(path);
        var memory = new Memory<byte>(buffer);
        int read;
        while ((read = await stream.ReadAsync(memory)) > 0)
            ProcessChunk(memory.Span.Slice(0, read));   // sync, zero-alloc
    }
    finally { ArrayPool<byte>.Shared.Return(buffer, clearArray: true); }
}
```

`ArrayPool` note: `Return(buffer, clearArray: true)` zeroes the buffer before it re-enters the pool — **do this whenever the buffer held passwords, tokens, or other sensitive data**.

**Why it matters (GC generational model):** Gen 0 = short-lived, collected often & cheaply; Gen 2 = long-lived, expensive; the **Large Object Heap (LOH)** holds any single object **>85 KB** and isn't compacted by default → repeated large allocations fragment it. Goal: keep objects dying in Gen 0 (if they must exist at all) and keep the LOH out of hot paths. Span/Memory/ArrayPool are the tools that get you there.

**When to reach for this:** hot paths only — request parsing, message processing, stream reading, tight-loop string work (thousands of calls/sec). For a startup helper or a single config value, the plain `string` API is fine — knowing *when* matters as much as *how*.

> Source: Sunita Rawat — "Why Senior .NET Developers Never Use string.Split — And What They Use Instead" (CodeToDeploy, 2026-06-25, 7 min, 89 claps)

### `Interlocked` — lock-free atomic operations
```csharp
private int _counter = 0;

// Lock-free increment — thread-safe without lock {}
Interlocked.Increment(ref _counter);
Interlocked.CompareExchange(ref _value, newVal, expected);
```
Use instead of `lock` for simple counter/flag updates. Higher throughput under contention.

### `ValueTask` — avoid heap allocation for sync-fast paths
```csharp
// Task always allocates on heap
public async Task<int> GetCachedAsync(string key) { ... }

// ValueTask — no allocation if result is already available
public async ValueTask<int> GetCachedAsync(string key)
{
    if (_cache.TryGetValue(key, out var cached)) return cached;
    return await FetchFromDbAsync(key);
}
```
Use `ValueTask` when the async method often completes synchronously (cache hits, etc.).

### `IAsyncEnumerable<T>` — stream results, don't buffer
```csharp
await foreach (var item in GetItemsAsync())
{
    Process(item);
}

private async IAsyncEnumerable<Item> GetItemsAsync()
{
    await foreach (var row in db.QueryAsync(...))
        yield return row;
}
```
Instead of loading all results into a `List<T>` before processing — stream them one by one.

> "5 C# Techniques You Start Using When Performance Actually Matters" — Gulam Ali H. (CodeToDeploy)

---

## Union types (C# 15)

From Gulam Ali H.'s "C# 15's Newest Feature Is More Dangerous Than It Looks" (2026-04-14):

### The problem they solve

Many APIs naturally produce **multiple valid outcomes** — and C# historically had no native way to express a closed set:

```csharp
// Before: object (loses type safety), inheritance boilerplate, or third-party OneOf
abstract class LoginResult { }
sealed class Success : LoginResult { }
sealed class InvalidPassword : LoginResult { }
sealed class LockedAccount : LoginResult { }
```

### What they are

A union represents a value that can be **one of a fixed set of types** — the compiler knows the allowed shapes:

```csharp
public sealed record Success(User User);
public sealed record InvalidPassword;
public sealed record LockedAccount;

public union LoginResult(Success, InvalidPassword, LockedAccount);
```

Consumers pattern-match cleanly:

```csharp
return result switch
{
    Success s        => $"Welcome {s.User.Name}",
    InvalidPassword  => "Wrong password",
    LockedAccount    => "Account locked"
};
```

### Where they genuinely help

- **API return types** — `Task<LoginResult>` expresses finite outcomes; reader knows valid shapes without checking docs
- **Pattern matching** — integrates cleanly with `switch` expressions
- **Domain state machines** — Draft / Submitted / Approved / Rejected: mutually exclusive, compiler-enforced
- **Parsers, validation, SDK responses** — anywhere outcomes are finite and well-known

### Where they're overhyped

| Overhype | Reality |
|---|---|
| "Replaces inheritance" | No — inheritance = shared behavior; unions = finite alternatives. Different tools |
| "Transforms C# like Go/Rust" | Targeted feature, not a paradigm shift |
| "Replaces all result wrappers" | Teams using `Result<T>`, `ErrorOr<T>`, or `OneOf` already have stable patterns |
| "Cleans up bad APIs" | A bad API with union types is still a bad API |

**Verbosity creep** — for large systems, you may end up with many tiny record types (`PaymentSucceeded`, `PaymentFailed`, `PaymentPendingReview`…), nested unions across layers, and serialization/model-binding complexity.

**Open practical concerns** — performance (allocations, boxing), JSON converters, ASP.NET model binding, reflection support, debugger experience. These determine real adoption, not launch-day enthusiasm.

### When to use / when not to

| Good candidates | Weak candidates |
|---|---|
| Service result types | Every CRUD method |
| Validation outcomes | Replacing all exceptions |
| Parsers | Replacing all inheritance |
| Domain workflows / state machines | Trivial methods (for style points) |
| SDK response models | |

> "Good engineers don't ask 'is this feature new?' — they ask 'is this the right tool for this problem?'"  
> — Gulam Ali H., "C# 15's Newest Feature Is More Dangerous Than It Looks" (2026-04-14)

---

## Static analysis for cleaner .NET

From Michael Maurice's "7 Brutally Practical Ways I Used Static Analysis to Write Cleaner .NET":

| Technique | Tool / Approach |
|---|---|
| **Nullable analysis** | Enable `<Nullable>enable</Nullable>` in .csproj — compiler warns on null ref risks |
| **Roslyn analyzers** | Add `Microsoft.CodeAnalysis.NetAnalyzers` — hundreds of built-in rules |
| **EditorConfig + style rules** | Enforce naming conventions, spacing, expression-body members across the team |
| **SonarAnalyzer.CSharp** | Security and code smell detection beyond Roslyn defaults |
| **Code metrics** | Watch cyclomatic complexity — refactor when a method exceeds ~10 |
| **Treat warnings as errors** | `<TreatWarningsAsErrors>true</TreatWarningsAsErrors>` — makes analysis actionable, not advisory |
| **Custom Roslyn analyzer** | Write domain-specific rules (e.g. "never call this deprecated API") |

**Quick setup in .csproj:**
```xml
<PropertyGroup>
  <Nullable>enable</Nullable>
  <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
  <AnalysisLevel>latest</AnalysisLevel>
</PropertyGroup>
```

> "7 Brutally Practical Ways I Used Static Analysis to Write Cleaner .NET" — Michael Maurice (71 claps)

---

## async/await heap allocation fix (.NET 11)

From Krati Varshney's "async/await Has Been Lying to You Since C# 5. .NET 11 Is Finally Fixing It." (6 min, 150 claps):

Every `await` in C# 5–10 allocates a state machine object on the heap — even when the awaited operation completes synchronously. In high-throughput code (e.g. hot API paths hitting cache), this creates constant GC pressure.

**.NET 11 fix:** the runtime detects synchronous completion and avoids the heap allocation entirely for those cases. The code you write stays identical — the JIT handles the optimisation.

**In the meantime (C# 8+):** use `ValueTask` for methods that frequently complete synchronously:
```csharp
// Task — always allocates
public async Task<int> GetAsync(string key) { ... }

// ValueTask — no allocation when result is cached
public async ValueTask<int> GetAsync(string key)
{
    if (_cache.TryGetValue(key, out var v)) return v;  // sync path, no alloc
    return await FetchFromDbAsync(key);
}
```

---

## 5 advanced C# features for real-world code

From Gulam Ali H.'s "5 Advanced C# Features That Actually Improve Real-World Code" (5 min, 119 claps):

### 1. Avoid hidden struct copies
Passing structs by value copies every field. Use `in` (read-only ref) or `ref` to pass large structs without copying:
```csharp
// BAD — copies entire struct on every call
void Process(MyLargeStruct s) { ... }

// GOOD — ref, no copy
void Process(in MyLargeStruct s) { ... }   // read-only
void Process(ref MyLargeStruct s) { ... }  // read-write
```

### 2. Enforce nullability at compile time
Enable nullable reference types to catch null bugs before runtime:
```xml
<!-- .csproj -->
<Nullable>enable</Nullable>
```
```csharp
string? nullable = GetName();    // explicitly nullable
string nonNull = GetName()!;     // assert non-null (use sparingly)
```

### 3. `readonly struct` for immutable value types
```csharp
readonly struct Point(double X, double Y)
{
    public double Distance => Math.Sqrt(X * X + Y * Y);
}
```
Guarantees no mutation; compiler can optimise away defensive copies.

### 4. Pattern matching in `switch` expressions
```csharp
string Describe(object obj) => obj switch
{
    int n when n > 0  => "positive int",
    int n             => "non-positive int",
    string s          => $"string: {s}",
    null              => "null",
    _                 => "unknown"
};
```

### 5. `record` types for value equality
```csharp
record Point(double X, double Y);  // immutable, value equality, deconstruct
var p1 = new Point(1, 2);
var p2 = new Point(1, 2);
Console.WriteLine(p1 == p2);  // true — structural equality
```

---

## Clean Architecture + CQRS (.NET 10)

From Michael Maurice's "Clean Architecture With .NET 10 And CQRS - Project Setup" (7 min):

**Layer structure:**
```
src/
  Domain/          ← entities, value objects, domain events (no dependencies)
  Application/     ← use cases, CQRS commands/queries, interfaces
  Infrastructure/  ← EF Core, external APIs, file system (implements Application interfaces)
  API/             ← controllers or minimal API endpoints (depends on Application only)
```

**CQRS with MediatR:**
```csharp
// Command
public record CreateVideoCommand(string Title, string Url) : IRequest<Guid>;

// Handler
public class CreateVideoHandler(IVideoRepository repo) : IRequestHandler<CreateVideoCommand, Guid>
{
    public async Task<Guid> Handle(CreateVideoCommand cmd, CancellationToken ct)
    {
        var video = new Video(cmd.Title, cmd.Url);
        await repo.AddAsync(video, ct);
        return video.Id;
    }
}

// Controller
[HttpPost]
public async Task<IActionResult> Create(CreateVideoCommand cmd)
    => Ok(await _mediator.Send(cmd));
```

**Key rule:** Domain has no dependencies. Application depends only on Domain. Infrastructure implements Application interfaces. API depends only on Application.

The `$50,000 refactoring that could have been avoided` — the article frames CQRS setup correctly from the start as cheaper than retrofitting it later.

---

## API design decisions for long-lived code

From Gulam Ali H.'s "5 C# API Design Decisions That Decide Whether Your Code Ages Well" (208 claps):

| Decision | Wrong approach | Right approach |
|---|---|---|
| **Return types** | Return concrete types (`List<T>`) | Return interfaces (`IEnumerable<T>`, `IReadOnlyList<T>`) — callers can't depend on implementation details |
| **Method signatures** | Lots of parameters | Introduce a parameter object/record — adding fields later doesn't break callers |
| **Nullability** | `string name` (nullable by convention) | Enable `<Nullable>enable</Nullable>` — express intent explicitly |
| **Exceptions vs results** | Throw for control flow | Return `Result<T>` or `OneOf<T, Error>` for expected failure paths; throw only for truly unexpected states |
| **Sealing classes** | Leave everything inheritable | `sealed` by default, `virtual` only when designed for extension — prevents fragile base class problems |

```csharp
// BAD — concrete return type locks callers in
public List<Video> GetVideos() => _db.Videos.ToList();

// GOOD — callers only depend on the iteration contract
public IReadOnlyList<Video> GetVideos() => _db.Videos.ToList();

// BAD — adding a parameter breaks all callers
public Video FindVideo(string id, bool includeDisabled) { ... }

// GOOD — extend without breaking
public Video FindVideo(VideoQuery query) { ... }
public record VideoQuery(string Id, bool IncludeDisabled = false);
```

---

## FrozenDictionary — read-only fast lookup (.NET 8+)

From Abe Jaber's "How to Use .NET FrozenDictionary for Faster Lookups":

`FrozenDictionary<K,V>` is an immutable dictionary optimised for **read-heavy workloads** — faster lookups than `Dictionary<K,V>` because it can use perfect hashing at construction time.

```csharp
using System.Collections.Frozen;

// Build once at startup
FrozenDictionary<string, Country> countryLookup =
    countries.ToFrozenDictionary(c => c.Code);

// Reads are faster than Dictionary — no lock, optimal hash
var country = countryLookup["JP"];
```

**When to use:**
- Configuration loaded at startup (country codes, enum mappings, feature flags)
- Static lookup tables that never change at runtime
- High-throughput read paths where `Dictionary` contention or overhead is measurable

**When NOT to use:** any data that needs to be updated at runtime — `FrozenDictionary` is write-once; rebuilding it is expensive.

**Vs `ImmutableDictionary`:** `FrozenDictionary` is faster for reads; `ImmutableDictionary` supports structural sharing for incremental updates.

---

## DI via source generation (.NET)

From Karthikeyan NS's "A Cleaner Way to Handle Dependency Injection in .NET" (4 min, 106 claps):

Source generators can automatically register services at compile time, eliminating manual `services.Add*()` boilerplate. The approach uses attributes on classes to declare their own registration:

```csharp
// Instead of manually registering in Program.cs:
// builder.Services.AddScoped<IMyService, MyService>();

// Use an attribute:
[RegisterScoped]          // or [RegisterSingleton], [RegisterTransient]
public class MyService : IMyService { ... }
```

A source generator reads these attributes at compile time and generates the registration code automatically. No reflection at runtime — pure compile-time code generation.

**Benefits:**
- Removes the DI registration file that grows with every new service
- Fails at compile time if a service is missing rather than at runtime
- Works with the existing `IServiceCollection` — no new DI container
- Pairs well with Scrutor (assembly scanning) for teams that prefer convention over attribute

**Key packages:** `Microsoft.Extensions.DependencyInjection.SourceGeneration` or community packages like `Jab` or `StrongInject`.

---

## LINQ — IQueryable vs IEnumerable silent switch

From Krati Varshney's "LINQ's .Where() Has Been Executing Differently Than You Think" (5 min):

The most common LINQ performance trap: calling `.ToList()`, `.AsEnumerable()`, or similar materialisation operators mid-chain silently switches from `IQueryable<T>` (translated to SQL) to `IEnumerable<T>` (executed in memory).

```csharp
// BAD — the Where() after AsEnumerable() runs in C#, not in the DB
// Loads ALL users into memory first, then filters
var expensiveUsers = db.Users
    .AsEnumerable()                  // ← switches to in-memory here
    .Where(u => u.Age > 30)          // ← this runs in C#, not SQL
    .ToList();

// GOOD — everything translates to SQL, DB does the filtering
var efficientUsers = db.Users
    .Where(u => u.Age > 30)          // ← this translates to WHERE clause
    .ToList();
```

**How to spot it:**
- Any method not supported by your LINQ provider (EF Core, LINQ to SQL) silently forces materialisation
- Complex string operations, custom methods, local variable captures can all trigger it
- Enable SQL logging to see whether a `WHERE` clause appears in the generated query

**Rule:** keep all filter/sort/projection operations on `IQueryable<T>` before calling `.ToList()`, `.FirstOrDefault()`, etc.

> See [[Async-Programming#Common patterns]] for async LINQ equivalents (`ToListAsync`, `FirstOrDefaultAsync`).

---

## 5 practical .NET patterns (experienced developers)

From Gulam Ali H.'s "What Experienced .NET Developers Do Differently, 5 Practical Patterns" (188 claps):

| Pattern | Rookie approach | Experienced approach |
|---|---|---|
| **Exception handling** | Catch `Exception` everywhere | Catch specific exceptions; let unexpected ones propagate |
| **Async all the way** | Mix sync and async (`task.Result`) | Await consistently; never block async code with `.Result` |
| **Null handling** | `if (x != null)` chains | Enable nullable reference types; use null-coalescing and pattern matching |
| **Configuration** | Hardcoded strings/settings | Strongly-typed options with `IOptions<T>` |
| **Logging** | `Console.WriteLine` or `Debug.WriteLine` | Structured logging with `ILogger<T>`, log levels, no string interpolation in log args |

**Structured logging (the most impactful change):**
```csharp
// BAD — string interpolation evaluated even when log level is off
_logger.LogDebug($"Processing order {orderId} for user {userId}");

// GOOD — message template; values only evaluated if level is active
_logger.LogDebug("Processing order {OrderId} for user {UserId}", orderId, userId);
```

The structured pattern also enables log aggregation (Seq, Splunk, ELK) to filter and query on `OrderId` as a property — not just as text in a string.

---

## dotnet-claude-kit

Free, open-source plugin by Mukesh Murugan (`github.com/codewithmukesh/dotnet-claude-kit`) that turns Claude Code into a **.NET 10 / C# 14 expert** with built-in skills, slash commands, specialist agents, and a Roslyn MCP server.

**The problem it solves:** out of the box, Claude Code doesn't know your .NET team rules — it may suggest `DateTime.Now` instead of `TimeProvider`, add redundant repository layers over EF Core, or read entire large files when a targeted lookup would cost 80% fewer tokens.

### Architecture

```
You type a slash command or chat
↓
Commands (16) orchestrate workflows
↓
Agents (10 specialists) + Skills (47 playbooks) + Rules (10 always-on)
↓
Roslyn MCP / CWM.RoslynNavigator (15 tools) — reads solution like the compiler
```

### Installation (3 steps)

```bash
# Step 1 — install Roslyn MCP server (once per machine)
dotnet tool install -g CWM.RoslynNavigator

# Step 2 — install plugin in Claude Code
/plugin marketplace add codewithmukesh/dotnet-claude-kit
/plugin install dotnet-claude-kit

# Step 3 — initialize your project (generates project-specific CLAUDE.md)
cd your-dotnet-solution
/dotnet-init
```

**Alternative (for contributors/offline):**
```bash
git clone https://github.com/codewithmukesh/dotnet-claude-kit.git
cd your-dotnet-project
claude --plugin-dir /path/to/dotnet-claude-kit
```

### Key slash commands

| Command | What it does |
|---|---|
| `/dotnet-init` | Scans `.sln`, generates project-specific `CLAUDE.md`. Greenfield: scaffolds `src/`/`tests/`, `Directory.Build.props` |
| `/scaffold` | Scaffold first feature after init |
| `/health-check` | Run health checks across the solution |
| `/review-pr` | Review PR with .NET-aware context |
| `/fix-build` | Fix compiler/build errors |

### Roslyn MCP tools

Without MCP, Claude reads entire `.cs` files. With Roslyn Navigator it asks structured queries:

| Tool | What it asks |
|---|---|
| `find_symbol` | Where is `OrderService` defined? |
| `find_references` | Who calls `CreateOrder`? |
| `get_diagnostics` | Any compiler diagnostics in this project? |
| `detect_antipatterns` | Detect anti-patterns in the solution |
| `find_dead_code` | Find unreachable code |
| `get_dependency_graph` | Show project references |

**Result:** faster answers and significantly lower token use on large solutions — especially valuable on legacy .NET monorepos.

### What "after kit" code looks like

The kit steers Claude away from these common .NET anti-patterns:

| Before kit | After kit |
|---|---|
| `DateTime.Now` | `TimeProvider` (testable, injection-friendly) |
| Repository layer over EF Core | Direct `DbContext` usage — EF Core IS the repository |
| `throw new Exception(...)` | `Result<T>` return types for expected failures |
| Controller methods with business logic | Minimal API endpoint groups with `TypedResults` and validation filters |

> Still review every diff like a junior teammate's PR — the kit reduces rework, it doesn't remove your judgment.

> Source: Maulik Patel — "dotnet-claude-kit: make Claude Code a .NET 10 expert (beginner guide)" (2026-05-27)  
> See also [[Claude-Code#dotnet-claude-kit]] for installation via Claude Code CLI.

---

## Collection interfaces: List\<T\> vs IList\<T\> vs IEnumerable\<T\>

| Interface | Capabilities | Use when |
|---|---|---|
| `IEnumerable<T>` | Iterate only, deferred execution | Returning from LINQ queries; method accepts any sequence |
| `IList<T>` | Index access + Count, but no concrete mutation guarantee | Accepting a list-like structure without requiring ArrayList behaviour |
| `List<T>` | Full mutable list — Add, Remove, Sort, Count, index | Local variables, implementation details |

**Rules of thumb:**
- Accept the most abstract type in method parameters (`IEnumerable<T>` or `IReadOnlyList<T>`) — lets callers pass arrays, LINQ results, or lists
- Return concrete types from private methods, abstract types from public APIs
- `IReadOnlyList<T>` is often better than `IList<T>` for return types — communicates intent and prevents accidental mutation

```csharp
// Accept abstract — callers can pass anything
public decimal CalculateTotal(IEnumerable<OrderLine> lines)
    => lines.Sum(l => l.Price * l.Quantity);

// Private implementation uses concrete for performance
private readonly List<OrderLine> _lines = new();
```

> Source: Compile & Conquer — "List<T> vs IList<T> vs IEnumerable<T>" (2026-05-28)

---

## 5 underused .NET features that solve everyday problems

Quietly useful platform features that don't get release-announcement attention but remove real friction:

| Feature | Replaces | Why it matters |
|---|---|---|
| **`TimeProvider`** | `DateTime.UtcNow` directly | Inject it instead of calling the clock — tests use a fake clock instead of depending on wall time. Makes time-dependent logic (token expiry, scheduling) deterministic. |
| **Keyed DI** (.NET 8+) | factory classes / `switch` over implementations | Register multiple impls of one interface under string keys; resolve the right one by key. |
| **`Process` / `ProcessStartInfo`** | reimplementing external tooling | Orchestrate Python, Git, Docker, etc. and capture stdout — let .NET drive existing tools rather than rebuilding them. |
| **Built-in Rate Limiting** | custom throttling middleware | `AddRateLimiter` + `AddFixedWindowLimiter` protects a public API from abuse in minutes. |
| **`BackgroundService`** | long work inside the request pipeline | Hosted service keeps the API responsive while jobs run independently. |

```csharp
// TimeProvider — inject the clock so it can be faked in tests
public class TokenService(TimeProvider timeProvider)
{
    public bool IsExpired(DateTimeOffset expiryDate)
        => timeProvider.GetUtcNow() > expiryDate;
}

// Keyed DI — multiple implementations, no factory/switch
builder.Services.AddKeyedScoped<IPaymentService, StripePaymentService>("stripe");
builder.Services.AddKeyedScoped<IPaymentService, PaypalPaymentService>("paypal");

public class CheckoutService([FromKeyedServices("stripe")] IPaymentService paymentService) { }

// Built-in rate limiting
builder.Services.AddRateLimiter(options =>
    options.AddFixedWindowLimiter("api", o => {
        o.PermitLimit = 100;
        o.Window = TimeSpan.FromMinutes(1);
    }));

// BackgroundService for long-running work
public class Worker : BackgroundService
{
    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        while (!stoppingToken.IsCancellationRequested)
        {
            // process jobs...
            await Task.Delay(5000, stoppingToken);
        }
    }
}
builder.Services.AddHostedService<Worker>();
```

> Source: Kavathiyakhushali — "5 Hidden .NET Features Most Developers Still Aren't Using" (2026-06-23, 3 min, 74 claps)

---

## Stop copy-pasting solutions — use `dotnet new` templates

The anti-pattern: copy your last solution, rename projects, fix namespaces, strip old features, bump packages — "a recycled accident," not a clean start. `dotnet new` is powered by the **Microsoft Template Engine**; turn your preferred setup into a real, repeatable template instead.

```bash
dotnet new install MySolution.Templates      # install a template package (NuGet, .nupkg, or folder)
dotnet new mysolution-api -n MyNewApi        # generate a clean solution named MyNewApi
```

**Anatomy of a template** — build the solution exactly how you want future projects to look, then add a `.template.config/template.json` at the root next to the `.sln`:

```
TemplateSolution/
├── TemplateSolution.sln
├── src/{Api, Application, Domain, Infrastructure}/
├── tests/{UnitTests, ArchitectureTests}/
└── .template.config/template.json
```

Key `template.json` fields: `sourceName` (the literal string the engine finds-and-replaces with the user's `-n`/`--name` value — pick one that appears in the `.sln`, project names, and namespaces), `shortName` (the CLI invocation name), `identity`, `name`, `classifications`, `tags` (`language`, `type`), and `postActions` (e.g. NuGet restore, actionId `210D431B-A78B-4D2F-B762-4ED3E3EA9025`). Set `$schema` so editors give validation/IntelliSense from the JSON Schema Store.

**Discipline:** don't templatize too early — build manually a few times first ("if you've built one solution, you have an experiment, not a template"). Include only repeatable foundations, never secrets, real connection strings, or environment-specific config. The real payoff isn't speed — it's **consistency**: decide once, improve over time, and stop the slow drift where every solution ends up subtly different.

> Source: Saeed Ghanavat — "Stop Recreating .NET Solutions From Scratch" (2026-06-24, 7 min, 27 claps)

---

## LINQ deferred execution — the multiple-enumeration trap

A classic interview filter: how many times does this hit the database?

```csharp
var pending = db.Orders.Where(o => o.Status == OrderStatus.Pending);
Console.WriteLine($"Processing {pending.Count()} orders");
foreach (var order in pending)
    await ProcessAsync(order);
```

**Answer: two queries.** The first line runs nothing — it builds an `IQueryable<Order>`, "the recipe, not the meal." The DB is only hit when something *enumerates* the recipe:

- `pending.Count()` → enumeration #1 → `SELECT COUNT(*) ... WHERE Status = 'Pending'`
- `foreach (... in pending)` → enumeration #2 → `SELECT * ... WHERE Status = 'Pending'`

Operators that **add to the recipe** (lazy): `Where`, `Select`, `OrderBy`, `Take`. Operators that **cook it** (enumerate): `Count()`, `ToList()`, `First()`, `Any()`, `foreach`.

**Why it's not just trivia:** a reporting page that enumerates the same `IQueryable` three times (count badge, totals row, table) triples query volume under load. Worse, the data can change *between* enumerations — your log says 500, your loop processes 503, and the reconciliation report is off by numbers nobody can explain.

**The fix — materialize once, reuse:**
```csharp
var pending = await db.Orders
    .Where(o => o.Status == OrderStatus.Pending)
    .ToListAsync();              // ONE query, one consistent snapshot
Console.WriteLine($"Processing {pending.Count} orders");  // free — List property
foreach (var order in pending) { ... }                    // free — same snapshot
```

**The trap inside the fix:** `ToListAsync()` on a 2-million-row table pulls 2M rows into memory. The senior move is *sequencing* — filter and project on the `IQueryable` first, materialize last: "Recipe first. Cook once. Cook small."
```csharp
var summaries = await db.Orders
    .Where(o => o.Status == OrderStatus.Pending)   // SQL WHERE
    .Select(o => new { o.Id, o.Total })            // SQL SELECT 2 cols
    .Take(500)                                     // SQL TOP(500)
    .ToListAsync();                                // NOW cook it
```

ReSharper/Rider flag "possible multiple enumeration"; EF Core logs each command so duplicate queries are visible — but warnings only help people who understand *why*. Distinct from [[#LINQ — IQueryable vs IEnumerable silent switch]] (that trap is about *where* the query runs; this one is about *how many times* it runs).

> Source: The Curly Brace — "The One C# Question That Filters Out 80% of 'Senior' Developers" (2026-06-26, 4 min, 101 claps)

---

## Exception handling — the swallowed exception

The most expensive code is the code that fails silently. An empty `catch` in a payment retry handler caused six hours of downtime and 38,000 lost orders — while every dashboard stayed green, because the API kept returning `200 OK`.

**The villains that pass code review every day:**
```csharp
catch (Exception ex) { /* TODO: handle later */ }   // catches, handles nothing, tells no one
catch (Exception ex) { _logger.LogError(ex, "Payment failed"); }  // logs, then returns as if it succeeded — the lie just got a paper trail
catch (Exception ex) { throw ex; }                  // resets the stack trace — crime scene erased
try { return int.Parse(input); } catch { return 0; }  // exceptions as flow control — orders of magnitude slower than int.TryParse
```

**`throw;` good, `throw ex;` evidence tampering** — `throw;` preserves the original stack trace; `throw ex;` makes the exception appear to originate from your catch block.

**The one rule that replaces all the advice:** *Can this code actually do something about this failure?*

- **Yes** (retry, fall back to cache, return a domain error) → catch the **specific** exception. Use an **exception filter** (`when`) to decide whether to catch *without unwinding the stack*:
  ```csharp
  catch (HttpRequestException ex) when (IsTransient(ex))
  {
      await Task.Delay(_backoff.Next());  // retry
  }
  ```
- **No** → let it propagate to the layer that *can* act — global exception middleware that logs with full context, **increments a metric**, and returns an honest 500:
  ```csharp
  app.UseExceptionHandler(builder => builder.Run(async ctx =>
  {
      var ex = ctx.Features.Get<IExceptionHandlerFeature>()?.Error;
      Log.Error(ex, "Unhandled exception {TraceId}", ctx.TraceIdentifier);
      PaymentFailures.Inc();              // the metric that would have caught the outage
      ctx.Response.StatusCode = 500;
      await ctx.Response.WriteAsJsonAsync(new { traceId = ctx.TraceIdentifier });
  }));
  ```

"A 500 is your system telling the truth." Broad `catch (Exception)` is correct at **top-level boundaries only** (request middleware, queue consumers, background loops, `Main`) — there you catch broadly, log loudly, emit a metric, dead-letter, and continue. Catch-and-continue-as-if-fine anywhere in business logic is how you get a customer email instead of an alert.

**Audit grep (severity order):** empty `catch` / lone `// TODO`; `throw ex;`; `catch (Exception)` in business logic that returns normally; logged exceptions with **no metric** attached (logs are for diagnosis, metrics are for detection).

> Source: The Curly Brace — "The Most Expensive 4 Lines of C# I've Ever Seen" (2026-06-27, 4 min, 61 claps)

---

## Scalar replaces Swagger UI (.NET 9/10)

.NET 9 removed Swagger UI from the default Web API template — but **nothing is broken**. Clarify the terms first: **OpenAPI** is the *specification*; **Swagger UI** is just one tool that renders it. The old `Swashbuckle` package did two jobs (generate the OpenAPI doc + render UI) and is no longer maintained by its original author, so Microsoft dropped it from the template.

**What changed:** ASP.NET Core now generates the OpenAPI document itself, no extra package — `AddOpenApi()` + `MapOpenApi()`. (.NET 10 defaults to OpenAPI 3.1.) Only the UI is left for you to pick.

**Scalar** is a modern API-reference UI that reads the same OpenAPI document — clean/fast on large APIs, built-in API client (send requests from the docs, no Postman), auto-generated multi-language code samples, good search, proper dark mode. Setup is two lines:
```csharp
builder.Services.AddOpenApi();
app.MapScalarApiReference();      // browse at /scalar
```

Swagger UI isn't dead — Swashbuckle lives on as a community package if you want it back. Scalar is simply a nicer default.

> Source: Serkan Özbey Kurucu — "Why I Switched from Swagger UI to Scalar in .NET" (2026-06-26, 3 min, 12 claps)

---

## Shared libraries — the microservices anti-pattern

A team with ~20 .NET microservices noticed duplicated DTOs, helpers, logging setup, and event contracts, so they consolidated everything into shared NuGet packages (`Company.Common`, `Company.Contracts`, `Company.Logging`, …). It felt right for a few months, then turned into a "mini-monolith":

- **Version sprawl** — one team adds a property to an event contract, publishes a new version, some services upgrade, others don't. Soon four versions of the same package are in production and nobody can answer "which version are we supposed to use?"
- **Blast-radius rebuilds** — a two-line fix to one utility method forces rebuilding, regression-testing, and redeploying 15 services, most of which don't even use it.
- **Transitive dependency conflicts** — one package wants Serilog vN, another wants vN-1; `Newtonsoft.Json 12` vs `13`. More time spent on upgrade coordination than building features.

**The resolution — share stable infrastructure, duplicate volatile domain code:**

| Still shared (stable contracts, rarely change) | No longer shared (copy instead) |
|---|---|
| OpenTelemetry setup | DTOs |
| Authentication middleware | Domain models |
| Health checks | Event contracts |
| Third-party SDKs | Validation logic, utility classes |

Counterintuitively, the small duplication that came back costs less than maintaining another shared package. "If a service needs twenty lines from another, we copy it." The principle: **microservices are supposed to be independently deployable — shared libraries quietly take that independence away.** (A pragmatic counterweight to DRY; cf. the "shared kernel" boundary in DDD.)

> Source: Hari Prasad Nattuva — "Shared Libraries Slowly Destroyed Our .NET Microservices" (2026-06-26, 3 min, 40 claps)

---

## See also
- [[Async-Programming]] — Task, async/await, TPL
- [[Dependency-Injection]] — IoC containers, DI in .NET
- [[SOLID-Principles]] — OOP design principles
- [[Entity-Framework]] — ORM built on C#
- [[Design-Patterns]] — GoF patterns with C# examples
- [[Claude-Code]] — dotnet-claude-kit plugin for AI-assisted .NET development
- [[MAUI]] — cross-platform UI with C#
