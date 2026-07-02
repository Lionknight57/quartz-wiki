---
title: Async Programming
tags: [concepts, dotnet, concurrency, csharp]
related: [CSharp, Data-Structures, Entity Framework]
sources: [Async Programming.md, Asynchronous Programming in .NET.md, Asynchronous Programming.md, Medium/2026-05/Medium Daily Digest - 2026-05-19.md]
updated: 2026-05-20
---

# Async Programming

## Core model in .NET — Task-based Asynchronous Pattern (TAP)

Introduced in C# 5.0 (2012). The `async`/`await` keywords make asynchronous code read like synchronous code.

```csharp
// Without async — callback hell
httpClient.GetStringAsync(url).ContinueWith(t => {
    var result = t.Result;
    // process result
});

// With async/await — reads linearly
public async Task<string> GetDataAsync()
{
    string result = await httpClient.GetStringAsync(url);
    return result;
}
```

---

## Task vs Thread

| | Task | Thread |
|---|---|---|
| Abstraction | High-level | Low-level |
| Managed by | Thread pool | OS |
| Cost | Lightweight | Expensive (1MB+ stack) |
| Return value | `Task<T>` | Not directly |
| Cancellation | `CancellationToken` | `Thread.Abort()` (deprecated) |

---

## Key types

### `Task`
Represents an ongoing operation with no return value.
```csharp
Task t = DoWorkAsync();
await t;
```

### `Task<T>`
Represents an ongoing operation that produces a result.
```csharp
Task<int> task = ComputeAsync();
int result = await task;
```

### `ValueTask<T>`
Lighter weight than `Task<T>` for operations that frequently complete synchronously.

---

## async/await rules

- A method marked `async` must return `Task`, `Task<T>`, or `void` (avoid `async void` except for event handlers)
- `await` can only appear inside an `async` method
- `await` does NOT block the calling thread — it suspends the method and returns control to the caller
- The continuation runs when the awaited task completes

---

## Common patterns

### Run multiple tasks in parallel
```csharp
var task1 = FetchUserAsync(id);
var task2 = FetchOrdersAsync(id);
await Task.WhenAll(task1, task2);
var user = task1.Result;
var orders = task2.Result;
```

### Cancellation
```csharp
var cts = new CancellationTokenSource(TimeSpan.FromSeconds(30));
var result = await LongOperationAsync(cts.Token);
```

### ConfigureAwait
```csharp
// In library code, don't capture synchronisation context
var data = await GetDataAsync().ConfigureAwait(false);
```

---

## Common mistakes

| Mistake | Problem | Fix |
|---|---|---|
| `task.Result` or `task.Wait()` | Deadlock in sync contexts | Use `await` |
| `async void` | Exceptions unobservable | Return `Task` |
| Not passing CancellationToken | Operations can't be cancelled | Thread token through |
| `await` inside `lock` | Compiler error | Use `SemaphoreSlim` |

---

## Task coordination

### Task.WhenAll — wait for all
```csharp
var tasks = new[] { FetchUserAsync(id), FetchOrdersAsync(id) };
await Task.WhenAll(tasks);
```
All tasks run concurrently; awaiting completes when the last one finishes. Any exception propagates as `AggregateException`.

### Task.WhenAny — first to complete (timeout pattern)
```csharp
var dataTask    = FetchDataAsync();
var timeoutTask = Task.Delay(TimeSpan.FromSeconds(30));

var winner = await Task.WhenAny(dataTask, timeoutTask);
if (winner == timeoutTask)
    throw new TimeoutException();

var result = await dataTask;   // safe — already completed
```

### Task.FromResult — precomputed / cached result
```csharp
// Returns an already-completed Task — no thread involved
public Task<int> GetCachedValueAsync() => Task.FromResult(_cachedValue);
```
Use when an async interface must be satisfied but the result is synchronously available. Pairs well with `ValueTask<T>` — prefer `ValueTask.FromResult` in hot paths.

---

## Continuations

`.ContinueWith()` schedules work after a task completes. Prefer `async/await` — continuations are lower-level but useful for conditional chaining:

```csharp
var task = FetchDataAsync();

// Run only if the task completed successfully
task.ContinueWith(t => Process(t.Result),
    TaskContinuationOptions.OnlyOnRanToCompletion);

// Run only on failure
task.ContinueWith(t => LogError(t.Exception),
    TaskContinuationOptions.OnlyOnFaulted);
```

**`Task.Factory.StartNew` + async:** if you nest an `async` lambda, the outer task completes as soon as the inner `await` is hit — it does NOT wait for the async work. You must `Unwrap()` to get the true inner task:

```csharp
// BAD — outer task completes immediately
var t = Task.Factory.StartNew(async () => await LongWorkAsync());

// GOOD — unwrap to get the real task
var t = Task.Factory.StartNew(async () => await LongWorkAsync()).Unwrap();
// or equivalently:
var t = Task.Run(async () => await LongWorkAsync());  // Run unwraps automatically
```

---

## Parallel programming

The `Parallel` class (in `System.Threading.Tasks`) runs loops across multiple threads automatically:

```csharp
// Parallel.For — CPU-bound iteration
Parallel.For(0, items.Length, i => {
    Process(items[i]);
});

// Parallel.ForEach — over any IEnumerable
Parallel.ForEach(customers, customer => {
    SendEmail(customer);
});

// Parallel.Invoke — run independent methods concurrently
Parallel.Invoke(
    () => StepA(),
    () => StepB(),
    () => StepC()
);
```

**Breaking early:**
```csharp
Parallel.For(0, items.Length, (i, state) => {
    if (ShouldStop(items[i])) state.Break();
    Process(items[i]);
});
```

**Thread safety:** use `ConcurrentBag<T>`, `ConcurrentQueue<T>`, or `Interlocked` when aggregating results — never write to shared non-thread-safe collections from parallel loops.

> `Parallel` is for **CPU-bound** work. For I/O-bound work, use `async/await` with `Task.WhenAll` instead.

---

## Progress reporting — IProgress<T>

Report incremental progress from background work back to the UI thread:

```csharp
// Caller creates IProgress and handles updates
var progress = new Progress<int>(percent =>
    progressBar.Value = percent);   // runs on UI thread

await DoWorkAsync(progress);

// Background method accepts IProgress
async Task DoWorkAsync(IProgress<int> progress)
{
    for (int i = 0; i <= 100; i += 10)
    {
        await Task.Delay(100);
        progress?.Report(i);   // null check — caller may not care
    }
}
```

`Progress<T>` automatically marshals the callback onto the synchronization context that created it (typically the UI thread) — no `Dispatcher.Invoke` needed.

---

## UI thread — returning from background work

In WPF/WinForms, UI controls can only be updated from the UI thread:

```csharp
// From a background thread, marshal back to UI
await Task.Run(() => {
    var data = FetchData();
    Application.Current.Dispatcher.Invoke(() => {
        DataGrid.ItemsSource = data;   // UI update on UI thread
    });
});

// With async/await — continuation runs on UI thread automatically
var data = await FetchDataAsync();   // awaited; resumes on UI thread
DataGrid.ItemsSource = data;          // safe — no Dispatcher needed
```

Using `async/await` without `ConfigureAwait(false)` restores the synchronisation context automatically — the simpler pattern.

---

## TaskCompletionSource — bridging non-async APIs

Wrap legacy callback-based or event-based code as awaitable tasks:

```csharp
public Task<string> ReadFileWithCallbackAsync(string path)
{
    var tcs = new TaskCompletionSource<string>();

    FileHelper.ReadAsync(path,
        onSuccess: content => tcs.SetResult(content),
        onError:   ex      => tcs.SetException(ex));

    return tcs.Task;   // caller awaits this
}

// Usage
var content = await ReadFileWithCallbackAsync("data.txt");
```

Also useful for manually controlling task completion in tests.

---

## Async streams — IAsyncEnumerable<T>

Process data as it arrives — streaming, without buffering everything first:

```csharp
// Producer — yield items asynchronously
async IAsyncEnumerable<int> GetNumbersAsync()
{
    for (int i = 0; i < 10; i++)
    {
        await Task.Delay(100);   // simulate async fetch
        yield return i;
    }
}

// Consumer — await foreach
await foreach (var number in GetNumbersAsync())
{
    Console.WriteLine(number);
}
```

**Use cases:** streaming API responses, reading large files in chunks, database cursors, real-time event feeds.

---

## How async/await works internally

The compiler transforms every `async` method into a **state machine** — a struct implementing `IAsyncStateMachine`. Each `await` point becomes a state:

```
State 0: start, begin awaiting task1
State 1: task1 done, begin awaiting task2
State 2: task2 done, return result
```

When the awaited task completes, the state machine resumes at the correct state — on the appropriate thread (captured synchronisation context, or thread pool if `ConfigureAwait(false)`).

**Practical implications:**
- `async` methods have overhead (allocation, state capture) — prefer `ValueTask` in hot paths
- Every `await` is a potential thread switch point
- Exceptions are captured and re-thrown when the task is awaited
- `async void` can't be awaited so exceptions go unobserved — always use `Task`

---

## Producer–consumer queues: what `Channel<T>` hides

`System.Threading.Channels.Channel<T>` is the polished abstraction for async producer-consumer pipelines. It looks simple precisely because it solves several lower-level coordination problems that you only notice when you try to build one yourself.

**What a plain `Queue<T>` doesn't give you:**
- Async waiting (a consumer must busy-loop if the queue is empty)
- Backpressure (bounded capacity that blocks fast producers)
- Completion semantics ("no more items will ever arrive")
- Cancellation that races cleanly with item arrival
- Fairness across multiple waiting consumers

**Minimal async queue from primitives** — `Queue<T>` + `SemaphoreSlim`:

```csharp
public class AsyncQueue<T>
{
    private readonly Queue<T> _items = new();
    private readonly SemaphoreSlim _signal = new(0);   // count = items available

    public void Enqueue(T item)
    {
        lock (_items) _items.Enqueue(item);
        _signal.Release();                              // wake one waiter
    }

    public async Task<T> DequeueAsync(CancellationToken ct = default)
    {
        await _signal.WaitAsync(ct);                    // sleep until item or cancel
        lock (_items) return _items.Dequeue();
    }
}
```

The invariant: **semaphore count == items waiting in the queue**. Break that pairing (e.g. forget `Release()` on one path) and the queue stalls silently.

**What you still don't have at this point** — and what `Channel<T>` adds for you:

| Concern | Custom queue | `Channel<T>` |
|---|---|---|
| Bounded capacity / backpressure | DIY with a second semaphore | `Channel.CreateBounded(capacity)` |
| Multi-consumer fairness | Undefined | Documented FIFO wake-up |
| Completion signal | DIY flag + wake-all | `writer.Complete()` + `ReadAllAsync` exits cleanly |
| Cancellation race vs. enqueue | Easy to get wrong | Handled |
| Error propagation | DIY | `writer.Complete(exception)` re-throws on readers |

**Direct-handoff variant** (`TaskCompletionSource<T>` instead of a semaphore): waiting consumers are stored as promises; an arriving producer completes one promise directly without buffering. Lower buffering and custom scheduling, but easier to get wrong — most production code converges back on `Channel<T>`.

**When a custom queue still makes sense:** domain-specific scheduling (priority, per-tenant fairness), tight integration with another lifecycle (e.g. a SignalR hub), or instrumentation that `Channel<T>` doesn't expose.

> Source: Gulam Ali H. — "Channels Feel Simple Because They Hide Complexity in C#", CodeToDeploy, 2026-04.

---

## See also
- [[CSharp#async-await-c-50]] — language feature overview
- [[CSharp#Performance techniques]] — `ValueTask`, `IAsyncEnumerable`, `ArrayPool`
- [[Entity-Framework]] — async DB queries (`ToListAsync`, `SaveChangesAsync`)
- [[Data-Structures]] — `ConcurrentQueue<T>`, `Channel<T>` for async pipelines
