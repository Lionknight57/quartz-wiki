---
title: Design Patterns
tags: [concepts, patterns, oop]
related: [SOLID Principles, Dependency Injection, CSharp, Entity Framework]
sources: [Design patterns.md]
updated: 2026-04-12
---

# Design Patterns

Reusable solutions to commonly occurring problems in software design (GoF — Gang of Four, 1994).

> The raw source (`Design patterns.md`) is primarily visual (slides). Images are at:
> `../raw/Design patterns_files/media/`

## Categories

### Creational — how objects are created

| Pattern | Purpose |
|---|---|
| **Factory Method** | Delegate instantiation to subclasses |
| **Abstract Factory** | Create families of related objects |
| **Builder** | Construct complex objects step by step |
| **Singleton** | Single instance per application |
| **Prototype** | Clone existing objects |

### Structural — how objects are composed

| Pattern | Purpose |
|---|---|
| **Adapter** | Translate one interface to another |
| **Decorator** | Add behaviour to objects dynamically |
| **Facade** | Simplified interface to a subsystem |
| **Composite** | Tree structures of objects |
| **Proxy** | Placeholder for another object |

### Behavioural — how objects communicate

| Pattern | Purpose |
|---|---|
| **Observer** | Notify dependents of state changes |
| **Strategy** | Swap algorithms at runtime |
| **Command** | Encapsulate requests as objects |
| **Iterator** | Traverse collections without exposing internals |
| **Template Method** | Define skeleton of algorithm in base class |
| **Chain of Responsibility** | Pass request along a chain of handlers |

---

## Null Object pattern

**Problem:** Methods returning `null` force callers to guard with `if (result != null)` everywhere. Information about *why* it's null is lost.

**Solution:** Return a "null object" — an instance that implements the interface but does nothing (or carries error information).

```csharp
public interface IPurchaseReport
{
    IPurchaseReport CreateFailedPurchase();
    IPurchaseReport CreateNotSignedIn();
    IPurchaseReport CreateNotRegistered(string username);
    IPurchaseReport CreateProductNotFound(string username, string productName);
    IPurchaseReport CreateNotEnoughMoney(string username, string productName, decimal price);
    IPurchaseReport CreateReport(string username, string productName, decimal price);
}
```

**Benefits:**
- Simplifies control flow on the caller — no null checks
- Special Case carries additional information back to the caller
- Control flow is flat — all operations flow through same steps
- Information required to complete operation is carried by the returned object

![Null Object pattern diagram](../../raw/Design patterns_files/media/image5.png)

---

## Special Case pattern

A variant of Null Object where the returned object carries meaningful error context.

Example: instead of `null` when a user isn't logged in, return a `NotLoggedInReport` that implements `IPurchaseReport` and contains the reason.

**Result path through layers:**

As execution progresses through layers (Presentation → Application → Domain → Repository), each layer can detect an error and return a special case object — carrying more and more information as it travels back up.

![Special Case path diagram](../../raw/Design patterns_files/media/image2.png)

---

## Factory pattern

Encapsulates object creation. The `IPurchaseReportFactory` example:

```csharp
// Wired up at composition root
var ui = new ApplicationServices(
    new DomainServices(
        new UserRepository(),
        new PurchaseReportFactory(),
        new ProductRepository()
    ),
    reportFactory
);
```

---

## Dependency Injection

See [[Dependency-Injection]] for full coverage. DI is the mechanism that makes patterns like Factory and Strategy composable.

---

## See also
- [[Dependency-Injection]] — IoC containers, constructor injection
- [[SOLID-Principles]] — principles that motivate many patterns
- [[CSharp]] — C# implementations
- [[Entity-Framework]] — Repository pattern in practice
