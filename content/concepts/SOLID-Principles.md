---
title: SOLID Principles
tags: [concepts, patterns, oop]
related: [Design Patterns, Dependency Injection, CSharp]
sources: [Solid.md]
updated: 2026-04-12
---

# SOLID Principles

Five principles of object-oriented design that make software more maintainable, flexible, and testable.

---

## S — Single Responsibility Principle

> A class should have one, and only one, reason to change.

Each class should do one thing. If a class handles both business logic and email sending, it has two reasons to change.

```csharp
// Bad — two responsibilities
public class Order
{
    public void Calculate() { ... }
    public void SendConfirmationEmail() { ... }  // should not be here
}

// Good
public class Order { public void Calculate() { ... } }
public class OrderEmailService { public void SendConfirmation(Order o) { ... } }
```

---

## O — Open/Closed Principle

> Software entities should be open for extension, closed for modification.

Add new behaviour by adding new code (new class, new implementation), not by modifying existing code.

```csharp
// Extend by adding new shapes, not by modifying AreaCalculator
public abstract class Shape { public abstract double Area(); }
public class Circle : Shape { ... }
public class Rectangle : Shape { ... }
```

---

## L — Liskov Substitution Principle

> Objects of a subtype should be substitutable for objects of the supertype without altering correctness.

If `S` extends `T`, you should be able to use `S` anywhere `T` is expected.

Classic violation: Square extends Rectangle but breaks when you set width/height independently.

---

## I — Interface Segregation Principle

> Clients should not be forced to depend on interfaces they do not use.

Prefer many small, focused interfaces over one large "fat" interface.

```csharp
// Bad
interface IWorker { void Work(); void Eat(); void Sleep(); }

// Good
interface IWorkable { void Work(); }
interface IFeedable { void Eat(); }
interface ISleepable { void Sleep(); }
```

---

## D — Dependency Inversion Principle

> High-level modules should not depend on low-level modules. Both should depend on abstractions.

Program to interfaces, not implementations.

```csharp
// Bad — high-level depends on low-level
public class OrderService { private SqlRepository _repo = new SqlRepository(); }

// Good — both depend on IOrderRepository abstraction
public class OrderService { private IOrderRepository _repo; }
public class SqlRepository : IOrderRepository { ... }
public class InMemoryRepository : IOrderRepository { ... }
```

DIP is implemented mechanically by [[Dependency-Injection]].

---

## See also
- [[Dependency-Injection]] — DI is the mechanism that implements DIP
- [[Design-Patterns]] — patterns that express these principles
- [[CSharp]] — C# language features that enable SOLID code
- [[Testing]] — SOLID code is inherently more testable
