---
title: Angular Signals
tags: [angular, signals, reactive, state-management, change-detection, typescript]
related: [Angular, RxJS, TypeScript-JavaScript]
sources: [Angular/RxJS and Angular Signals Fundamentals.md]
updated: 2026-04-16
---

# Angular Signals

A reactive primitive built into Angular (v16+) for managing state. A signal is a **data value + change notification** — when the value changes, Angular automatically knows which templates and computed values need updating.

> Available as developer preview from Angular v16. Continually improved in subsequent releases.

---

## Signals vs variables

```typescript
// Variables — NOT reactive
let x = 5;
let y = 3;
let z = x + y;   // z = 8
x = 10;
console.log(z);  // still 8 — z doesn't react to x changing

// Signals — reactive
const x = signal(5);
const y = signal(3);
const z = computed(() => x() + y());   // z = 8
x.set(10);
console.log(z());  // 13 — z automatically recomputed
```

---

## Creating signals

```typescript
import { signal, computed, effect } from '@angular/core';

// Number signal — type inferred
quantity = signal(1);

// Typed signal — use generic when type can't be inferred
products = signal<Product[]>([]);

// Object signal
selectedProduct = signal<Product>({ id: 5, name: 'Hammer', price: 12 });

// Array signal
qtyAvailable = signal([1, 2, 3, 4, 5, 6]);
```

A signal created with `signal()` is a **WritableSignal** — it can be set or updated. **An initial value is required.**

---

## Reading signals

**Read by calling the signal as a function** — "open the box":

```typescript
// In TypeScript
console.log(this.quantity());           // 1
const price = this.selectedProduct().price;

// In templates — reads the current value AND registers as a dependency
{{ quantity() }}
<div>{{ selectedProduct().name }}</div>
<option *ngFor="let opt of qtyAvailable()">{{ opt }}</option>
```

Reading a signal in a template **automatically registers it as a dependency**. Angular re-renders only the affected template portion when the signal changes — no manual subscription needed.

> **Common mistake:** `{{ quantity }}` in a template shows the signal object, not its value. Always add `()`.

---

## Modifying signals

```typescript
// set() — replace with a new value
this.quantity.set(5);

// update() — derive new value from current value
this.quantity.update(qty => qty * 2);

// For object/array signals — always create a new value
// (mutating in place does NOT trigger change notification)
this.cartItems.update(items => [...items, newItem]);  // ✓ spread to new array
this.cartItems().push(newItem);                        // ✗ mutates in place — signal not notified
```

**Key rule:** Always use `.set()` or `.update()` to modify a signal. Direct mutation bypasses the change notification.

---

## Computed signals

Derived read-only signals that automatically recompute when their dependencies change:

```typescript
import { computed } from '@angular/core';

// Simple calculation
exPrice = computed(() => this.selectedProduct().price * this.quantity());

// Conditional
color = computed(() => this.exPrice() > 50 ? 'green' : 'blue');

// Cart total
cartCount = computed(() =>
  this.cartItems().reduce((total, item) => total + item.quantity, 0)
);

subtotal = computed(() =>
  this.cartItems().reduce((total, item) =>
    total + item.quantity * item.product.price, 0)
);

deliveryFee = computed(() => this.subtotal() < 50 ? 5.99 : 0);

tax = computed(() => Math.round(this.subtotal() * 0.1075 * 100) / 100);

totalPrice = computed(() =>
  this.subtotal() + this.deliveryFee() + this.tax()
);
```

**Computed signal rules:**
- Read-only — cannot call `.set()` on a computed signal
- Recomputed only when dependent signals change AND the computed value is read (lazy)
- **Memoized** — stores the last result; reuses it until dependencies change
- Side-effect free — do not write to other signals inside `computed()`

---

## Signal effects

Run code whenever dependent signals change:

```typescript
import { effect } from '@angular/core';

// Inline in constructor
constructor() {
  effect(() => console.log('Quantity changed:', this.quantity()));
}

// Declarative (as a class variable)
logEffect = effect(() => console.log('Extended price:', this.exPrice()));
```

**Effect rules:**
- Runs at least once immediately on creation
- Re-runs whenever any of its dependent signals change
- **Scheduled** — does not run synchronously inline. Angular batches and runs effects when the current task completes
- Avoid changing signal values inside effects — can cause circular updates
- Primary use: **debugging** and triggering side effects (logging, syncing, animations)

```typescript
// Good — debug logging
effect(() => console.log('Cart updated:', this.cartItems()));

// Avoid — writing to signals in effects
effect(() => this.total.set(this.subtotal() + this.tax()));  // use computed() instead
```

---

## When to use signals vs observables

| | Signals | RxJS Observables |
|---|---|---|
| **What it models** | Current state (a value that can change) | Events and data streams over time |
| **Holds a value** | Yes — always has a current value | No — values are emitted, then gone |
| **Template binding** | `{{ mySignal() }}` — direct, no pipe needed | `{{ myObs$ \| async }}` |
| **Subscription** | None required — template auto-detects | `subscribe()` or `async` pipe |
| **Change detection** | Fine-grained — re-renders only affected bindings | Zone.js marks whole component dirty |
| **Best for** | Local state, derived state, cart totals, UI flags | HTTP requests, routing, form events, complex async |

**Rule of thumb:**
- Use **signals** for state that your component owns and displays
- Use **RxJS** for async operations (HTTP, routing, user input streams)
- Use **both together** for the best of each

---

## toSignal — observable → signal

```typescript
import { toSignal } from '@angular/core/rxjs-interop';

@Injectable({ providedIn: 'root' })
export class ProductService {
  private http = inject(HttpClient);
  private url = 'api/products';

  // Observable for the HTTP layer
  private products$ = this.http.get<Product[]>(this.url).pipe(
    shareReplay(1),
    catchError(err => this.handleError(err))
  );

  // Signal for the template layer — auto-subscribes, auto-unsubscribes
  products = toSignal(this.products$, { initialValue: [] as Product[] });
}
```

`toSignal`:
- Automatically subscribes to the observable
- Always holds the most recently emitted value
- Automatically unsubscribes when the context (component/service) is destroyed
- Requires an `initialValue` for the signal to have a value before the observable emits

---

## toObservable — signal → observable

```typescript
import { toObservable } from '@angular/core/rxjs-interop';

// When you need RxJS operators on signal state
private quantitySignal = signal(1);
quantity$ = toObservable(this.quantitySignal);

// Then use RxJS operators
quantityDebounced$ = this.quantity$.pipe(debounceTime(300));
```

> **Note:** `toObservable` may not emit every intermediate value if a signal changes multiple times before the scheduler runs. Use a `Subject` when you need every notification.

---

## Error handling with signals

Signals don't have an error channel — errors must be handled before reaching the signal:

```typescript
// Option 1: Handle in the observable pipeline
private products$ = this.http.get<Product[]>(this.url).pipe(
  catchError(err => of([] as Product[]))   // return replacement value on error
);
products = toSignal(this.products$, { initialValue: [] as Product[] });

// Option 2: Result wrapper pattern — expose both data and error as signals
interface Result<T> { data: T | undefined; error?: string; }

private productsResult$ = this.http.get<Product[]>(this.url).pipe(
  map(p => ({ data: p } as Result<Product[]>)),
  catchError(err => of({ data: [], error: err.message } as Result<Product[]>))
);
private productsResult = toSignal(this.productsResult$, {
  initialValue: { data: [] } as Result<Product[]>
});

products      = computed(() => this.productsResult().data);
productsError = computed(() => this.productsResult().error);
```

---

## Signals in services (shared state)

Put signals in services to share state across components without `@Input`/`@Output` chains or a Subject:

```typescript
@Injectable({ providedIn: 'root' })
export class CartService {
  // Writable signal — internal state
  cartItems = signal<CartItem[]>([]);

  // Computed signals — derived state
  cartCount = computed(() =>
    this.cartItems().reduce((t, i) => t + i.quantity, 0)
  );

  // Methods to modify state — always use signal methods
  addToCart(product: Product): void {
    this.cartItems.update(items => [...items, { product, quantity: 1 }]);
  }

  updateQuantity(item: CartItem, qty: number): void {
    this.cartItems.update(items =>
      items.map(i => i.product.id === item.product.id
        ? { ...i, quantity: qty }
        : i
      )
    );
  }

  removeFromCart(item: CartItem): void {
    this.cartItems.update(items =>
      items.filter(i => i.product.id !== item.product.id)
    );
  }
}
```

Any component that injects this service reads `cartItems()` in its template — Angular re-renders automatically when the signal changes.

---

## Signals with input properties

Signals don't yet directly work with `@Input()` properties. Workaround using a setter:

```typescript
// Instead of: @Input() cartItem!: CartItem;
// Use a private signal + setter:

private item = signal<CartItem>(undefined!);

@Input()
set cartItem(ci: CartItem) {
  this.item.set(ci);
}

// Now define computed signals that depend on item
exPrice = computed(() => this.item().product.price * this.item().quantity);
```

> As of Angular 17+, `input()` signals provide a cleaner API. Check Angular docs for the latest.

---

## Best practices

| Do | Don't |
|---|---|
| Use `signal()` for any state that could change | Use plain variables for reactive state |
| Always use `.set()` or `.update()` to modify | Mutate arrays/objects in-place |
| Use `computed()` for derived state | Recalculate in `ngOnChanges` or subscriptions |
| Put shared signals in services | Store them only in components |
| Use `effect()` for debugging and side effects | Modify other signals inside `effect()` |
| Use `toSignal()` for HTTP observable → template | Subscribe manually and store in a variable |
| Use `toObservable()` + RxJS for complex async on signals | Mix signals and observables carelessly |

---

## Quick reference

```typescript
// Create
quantity = signal(1);
products = signal<Product[]>([]);

// Read
this.quantity()             // in TypeScript
{{ quantity() }}            // in template

// Write
this.quantity.set(5);
this.quantity.update(q => q + 1);

// Derive
total = computed(() => this.price() * this.quantity());

// React
effect(() => console.log(this.quantity()));

// Bridge
products = toSignal(this.products$, { initialValue: [] });
obs$     = toObservable(this.mySignal);
```

---

## See also
- [[Angular]] — Angular framework overview, components, services
- [[RxJS]] — Observables, operators, reactive service pattern
- [[TypeScript-JavaScript]] — TypeScript generics used in signal types
