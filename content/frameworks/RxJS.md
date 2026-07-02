---
title: RxJS
tags: [rxjs, angular, reactive, observables, signals, typescript]
related: [Angular, TypeScript-JavaScript, Async-Programming]
sources: [Angular/RxJS.md, Angular/RxJs in Practice.md, Angular/RxJS and Angular Signals Fundamentals.md]
updated: 2026-04-16
---

# RxJS

Reactive Extensions for JavaScript. A library for composing asynchronous and event-based programs using observable sequences. Used heavily by Angular for HTTP, routing, forms, and custom event streams.

> Originally developed as Rx.NET (Microsoft). Implementations exist for Java, Python, Ruby. RxJS is the JavaScript/TypeScript flavour.

---

## Core concepts

| Term | Description |
|---|---|
| **Observable** | A blueprint for a stream — defines what to emit, not when. Does nothing until subscribed. |
| **Observer** | The consumer — three callbacks: `next`, `error`, `complete` |
| **Subscription** | The live connection between Observable and Observer — call `.unsubscribe()` to stop |
| **Operator** | A pure function that takes an Observable and returns a new Observable |
| **Subject** | Both an Observable and an Observer — multicasts to multiple subscribers |
| **Scheduler** | Fine-grained control over when emissions are delivered |

**Observable lifecycle:**
```
Observable emits:  next → next → next → (error | complete)
Observer handles:  next()  next()  next()  error() | complete()
```

---

## Creating observables

```typescript
import { of, from, fromEvent, interval, timer } from 'rxjs';

// Single values
of(1, 2, 3)                          // emits 1, 2, 3 then completes

// From array/iterable/Promise
from([10, 20, 30])                   // emits each item
from(fetch('/api/data'))             // wraps a Promise

// From DOM events
fromEvent(button, 'click')           // emits on each click

// Time-based
interval(1000)                       // emits 0,1,2... every 1s
timer(2000, 1000)                    // wait 2s, then emit every 1s

// HTTP (Angular) — always declare observables with $
products$ = this.http.get<Product[]>(this.url);
```

**Convention:** name observable variables with a `$` suffix — `products$`, `selectedId$`.

---

## Subscribing

```typescript
// Manual subscribe — must unsubscribe to avoid memory leaks
const sub = products$.subscribe({
  next: (data) => this.products = data,
  error: (err) => console.error(err),
  complete: () => console.log('done')
});

// Clean up
sub.unsubscribe();

// Preferred in templates — async pipe auto-unsubscribes
<div *ngFor="let p of products$ | async">{{ p.name }}</div>
```

---

## Operators

Operators are pure functions that transform the stream. Always import from `'rxjs'` or `'rxjs/operators'`. Chain them with `.pipe()`.

```typescript
products$.pipe(
  map(products => products.filter(p => p.active)),
  tap(p => console.log('stream value:', p)),  // side effects only — doesn't transform
  take(5)
).subscribe(...)
```

### Transformation

| Operator | What it does |
|---|---|
| `map(fn)` | Transform each emitted value |
| `pluck('key')` | Extract a property from each emitted object (deprecated — use `map`) |
| `scan(acc, val)` | Accumulate over time (like `reduce` but emits each step) |
| `reduce(acc, val)` | Accumulate to single value on complete |

### Filtering

| Operator | What it does |
|---|---|
| `filter(pred)` | Only pass values matching predicate |
| `take(n)` | Take first n values then complete |
| `takeUntil(obs$)` | Complete when another observable emits |
| `first()` | Emit first value then complete |
| `distinctUntilChanged()` | Skip duplicate consecutive values |
| `debounceTime(ms)` | Wait for silence of ms before emitting |
| `throttleTime(ms)` | Emit at most once per ms window |

### Combination

| Operator | What it does |
|---|---|
| `merge(obs1, obs2)` | Blend multiple streams — emit from any as they arrive |
| `concat(obs1, obs2)` | Sequential — wait for obs1 to complete before obs2 starts |
| `combineLatest([obs1, obs2])` | Emit array of latest values whenever any source emits |
| `forkJoin([obs1, obs2])` | Wait for all to complete, emit array of final values (like Promise.all) |
| `withLatestFrom(obs2)` | When source emits, combine with latest from obs2 |
| `startWith(value)` | Prepend an initial value before the source emits |
| `zip(obs1, obs2)` | Pair emissions by index — waits for matching emission from each |

### Higher-order mapping (flattening)

These operators take each emitted value, map it to an inner observable, and handle the resulting stream of streams.

| Operator | Behaviour | Use when |
|---|---|---|
| `switchMap(fn)` | Cancels previous inner obs, switches to new | Search typeahead — only care about latest |
| `concatMap(fn)` | Queues — waits for each inner to complete | Order matters, no parallel (save operations) |
| `mergeMap(fn)` | Runs all inner obs in parallel | Independent parallel requests |
| `exhaustMap(fn)` | Ignores new values while inner is active | Prevent double-submit on button click |

```typescript
// Search typeahead — classic switchMap pattern
searchTerm$.pipe(
  debounceTime(400),
  distinctUntilChanged(),
  switchMap(term => this.api.search(term))
).subscribe(results => this.results = results);

// Load related data for each item — mergeMap
selectedProduct$.pipe(
  mergeMap(product =>
    from(product.supplierIds).pipe(
      mergeMap(id => this.http.get<Supplier>(`/api/suppliers/${id}`)),
      toArray()
    )
  )
)
```

### Error handling

```typescript
products$.pipe(
  catchError(err => {
    console.error(err);
    return EMPTY;             // return empty observable = stream completes quietly
    // OR: return of([]);     // return replacement value
    // OR: return throwError(() => err);  // rethrow
  }),
  retry(3),                   // retry up to 3 times before erroring
  finalize(() => this.loading = false)  // always runs (like finally)
)
```

---

## Subjects

A Subject is both an Observable and an Observer — it can emit values manually and be subscribed to. Used to bridge event sources to observables.

```typescript
// Subject — any subscriber only gets values emitted AFTER they subscribe
private actionSubject = new Subject<string>();
action$ = this.actionSubject.asObservable(); // expose read-only

triggerAction(value: string) {
  this.actionSubject.next(value);  // emit manually
}
```

| Subject type | Behaviour |
|---|---|
| `Subject` | No initial value; new subscribers miss past emissions |
| `BehaviorSubject(init)` | Holds current value; new subscribers immediately get latest |
| `ReplaySubject(n)` | Replays last n emissions to new subscribers |
| `AsyncSubject` | Only emits the last value, on complete |

**Best practice:** Keep the Subject private; expose only `.asObservable()` to prevent external emission.

---

## Multicasting / caching

```typescript
// shareReplay(1) — cache last emission; prevent duplicate HTTP calls
products$ = this.http.get<Product[]>(this.url).pipe(
  shareReplay(1)
);

// share() — multicast without replay
shared$ = source$.pipe(share());
```

Use `shareReplay(1)` on any observable that would trigger an HTTP request on each subscription (e.g. multiple components using `async` pipe on the same service stream).

---

## Angular Signals vs RxJS Observables

Angular introduced Signals (v16+) as a lighter alternative to observables for state management.

| | RxJS Observables | Angular Signals |
|---|---|---|
| **What it observes** | Events and data over time | State (variable values) |
| **Holds a value?** | No — values are emitted, not stored | Yes — always holds the current value |
| **When to use** | HTTP, routing, user events, complex async | Component/service state, computed values, change detection |
| **Template binding** | `async` pipe or `subscribe` | Direct — `{{ mySignal() }}` |
| **Change detection** | Zone.js marks whole component dirty | Fine-grained — only re-renders affected expressions |

### Using RxJS + Signals together (Better Together pattern)

```typescript
import { toSignal, toObservable } from '@angular/core/rxjs-interop';

// Observable → Signal (for template binding with fine-grained change detection)
private products$ = this.http.get<Product[]>(this.url).pipe(shareReplay(1));
products = toSignal(this.products$, { initialValue: [] });

// Signal → Observable (when you need operators on state)
private quantitySignal = signal(1);
quantity$ = toObservable(this.quantitySignal);

// Computed signal
subtotal = computed(() => this.quantity() * this.price());
```

**Pattern:** Use RxJS for the HTTP layer and complex async chains. Use Signals for the UI layer and derived state. Bridge with `toSignal` / `toObservable`.

> **Note:** `toObservable` may not emit every signal change (it's scheduled). Use a `Subject` if you need every notification.

---

## Reactive service pattern

```typescript
@Injectable({ providedIn: 'root' })
export class ProductService {
  private http = inject(HttpClient);
  private url = 'api/products';

  // Declarative — no manual subscribe
  products$ = this.http.get<Product[]>(this.url).pipe(
    shareReplay(1),
    catchError(this.handleError)
  );

  // Action stream — react to filter changes
  private categorySubject = new BehaviorSubject<string>('all');
  selectedCategory$ = this.categorySubject.asObservable();

  // Combined data stream
  filteredProducts$ = combineLatest([
    this.products$,
    this.selectedCategory$
  ]).pipe(
    map(([products, category]) =>
      category === 'all' ? products : products.filter(p => p.category === category)
    )
  );

  setCategory(cat: string) {
    this.categorySubject.next(cat);
  }
}
```

**Key reactive service principles:**
- No mutable state — use streams instead of `this.products = [...] `
- No manual `subscribe` in services — expose observables
- `async` pipe in templates — auto-unsubscribes, no memory leaks
- `shareReplay(1)` for caching — prevents duplicate HTTP calls

---

## Best practices

| Do | Don't |
|---|---|
| Use `async` pipe in templates | `subscribe()` in components (manual unsubscribe required) |
| `catchError` in the HTTP pipeline | Let errors propagate uncaught |
| `shareReplay(1)` for cached data | Subscribe multiple times to the same HTTP observable |
| Keep Subjects private, expose `.asObservable()` | Let external code call `.next()` on a Subject |
| Use `switchMap` for user-triggered requests | Use `mergeMap` for search (concurrent requests pile up) |
| `tap()` for debug logging only | Use `tap()` to produce side effects in prod |
| Place `take` / `takeUntil` last in the pipeline | Place `take` in the middle (may skip error handling) |

---

## Testing with marble diagrams

RxJS includes a `TestScheduler` for testing async code synchronously using marble syntax.

```
Marble syntax:
  -   = 10ms frame
  a   = emitted value 'a'
  |   = complete
  #   = error
  ()  = synchronous emissions

Example:
  source:   --a--b--c--|
  map(x=>x*2): --a--b--c--|  (with doubled values)
```

```typescript
const testScheduler = new TestScheduler((actual, expected) => {
  expect(actual).toEqual(expected);
});

testScheduler.run(({ cold, expectObservable }) => {
  const source = cold('--a--b|', { a: 1, b: 2 });
  const result = source.pipe(map(x => x * 10));
  expectObservable(result).toBe('--a--b|', { a: 10, b: 20 });
});
```

---

## See also
- [[Angular]] — RxJS is built in to Angular's HTTP, router, and reactive forms
- [[Async-Programming]] — async/await vs observables comparison
- [[TypeScript-JavaScript]] — TypeScript generics used heavily in RxJS typing
