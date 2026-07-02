---
title: Dependency Injection
tags: [concepts, patterns, oop, dotnet]
related: [SOLID Principles, Design Patterns, CSharp, Angular]
sources: [Dependancy injection.md]
updated: 2026-04-12
---

# Dependency Injection

A technique where a class receives its dependencies from the outside rather than creating them itself. The cornerstone of testable, loosely-coupled code.

## The problem without DI

```csharp
public class OrderService
{
    private readonly EmailSender _emailSender = new EmailSender(); // hard dependency
    private readonly SqlRepository _repo = new SqlRepository();    // hard dependency

    public void PlaceOrder(Order order)
    {
        _repo.Save(order);
        _emailSender.Send(order.CustomerEmail, "Order placed");
    }
}
```
- Cannot swap `EmailSender` for a test double
- Cannot change persistence without touching `OrderService`

## With DI

```csharp
public class OrderService
{
    private readonly IEmailSender _emailSender;
    private readonly IOrderRepository _repo;

    public OrderService(IEmailSender emailSender, IOrderRepository repo)
    {
        _emailSender = emailSender;
        _repo = repo;
    }
}
```
- Testable — inject fakes/mocks in tests
- Replaceable — swap `SqlRepository` for `InMemoryRepository`

---

## IoC Container

An Inversion of Control container handles wiring up dependencies automatically.

### .NET built-in DI (`Microsoft.Extensions.DependencyInjection`)

```csharp
// Registration (Startup / Program.cs)
services.AddScoped<IOrderRepository, SqlOrderRepository>();
services.AddTransient<IEmailSender, SmtpEmailSender>();
services.AddSingleton<ICache, MemoryCache>();
```

### Lifetimes

| Lifetime | Created | Disposed | Use for |
|---|---|---|---|
| `Transient` | Every request | End of request | Lightweight, stateless services |
| `Scoped` | Once per HTTP request | End of request | DB contexts, unit-of-work |
| `Singleton` | Once per app | App shutdown | Caches, config, thread-safe services |

---

## DI in Angular

Angular has its own DI system built into the framework.

```typescript
@Injectable({ providedIn: 'root' })
export class UserService {
  constructor(private http: HttpClient) {}
}

@Component({...})
export class UserComponent {
  constructor(private userService: UserService) {}
}
```

See [[Angular]] for Angular-specific DI patterns.

---

## Composition Root

The single place in the application where all dependencies are wired up. Everything else receives dependencies through its constructor.

```csharp
// Composition root (Main / Startup)
var ui = new ApplicationServices(
    new DomainServices(
        new UserRepository(),
        new PurchaseReportFactory(),
        new ProductRepository()
    )
);
```

---

## See also
- [[SOLID-Principles#dip-dependency-inversion-principle]] — DI is the mechanism that implements DIP
- [[Design-Patterns#factory-pattern]] — factories as alternative to DI in some cases
- [[Angular]] — Angular DI system
- [[Testing]] — mocking dependencies in tests
