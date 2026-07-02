---
title: Entity Framework
tags: [dotnet, orm, entityframework, ef-core, data-access, migrations]
related: [CSharp, Databases-SQL, ASP-NET, Dependency Injection, LINQ]
sources: [Entity Framework2.md, Entity Framework Core 2 Getting Started.md, Entity Framework in the Enterprise.md, Entity Framework/Entity Framework.md]
updated: 2026-04-12
---

# Entity Framework

Entity Framework (EF) is Microsoft's official ORM (Object-Relational Mapper) for .NET. It sits between your domain classes and the database — handling connections, command generation, query translation, change tracking, and result materialisation.

```
Domain Classes  →  DbContext (EF)  →  Relational DB
      ↑                   ↑
   LINQ queries      SaveChanges()
```

**Why EF?**
- Focus on domain model, not SQL/connections/commands
- Consistent query syntax via LINQ
- Change tracking — EF detects modifications automatically
- First-class Microsoft .NET support

---

## EF6 vs EF Core

| | EF6 | EF Core |
|---|---|---|
| Platform | .NET Framework only | .NET Core / .NET 5+ / cross-platform |
| Visual designer | Yes (EDMX) | No |
| Stability | Production-ready, mature | Actively evolving (feature-complete from EF Core 3+) |
| When to use | Existing .NET Framework apps | All new development |

> **Rule:** Use EF Core for all new projects. EF6 is maintained but not gaining new features.

---

## Setting up EF Core

```bash
# Install NuGet packages
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools   # for migrations CLI
```

---

## DbContext

`DbContext` is the central class. It wraps the database, exposes `DbSet<T>` collections for each entity, and manages the unit of work (tracks changes, issues SQL on `SaveChanges`).

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Customer> Customers { get; set; }
    public DbSet<Order> Orders { get; set; }

    // EF Core — configure via OnConfiguring or DI
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer("Server=.;Database=MyApp;Trusted_Connection=True;");

    // Or override OnModelCreating for Fluent API configuration
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Customer>()
            .HasMany(c => c.Orders)
            .WithOne(o => o.Customer)
            .HasForeignKey(o => o.CustomerId);
    }
}
```

**Register in ASP.NET Core DI (preferred):**
```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("Default")));
```

---

## Defining the model

EF infers the schema from your classes. Use **Data Annotations** or **Fluent API** to override conventions.

### Data Annotations

```csharp
public class Customer
{
    public int Id { get; set; }              // Convention: primary key

    [Required]
    [MaxLength(100)]
    public string Name { get; set; }

    [Column("email_address")]               // Override column name
    public string Email { get; set; }

    public ICollection<Order> Orders { get; set; } = new List<Order>();
}

public class Order
{
    public int Id { get; set; }
    public DateTime OrderDate { get; set; }

    public int CustomerId { get; set; }     // Convention: foreign key
    public Customer Customer { get; set; }  // Navigation property
}
```

### Fluent API (in OnModelCreating)

```csharp
modelBuilder.Entity<Customer>(entity =>
{
    entity.ToTable("Customers");
    entity.Property(c => c.Name).IsRequired().HasMaxLength(100);
    entity.HasIndex(c => c.Email).IsUnique();
});
```

Fluent API takes precedence over Data Annotations. Prefer Fluent API for complex mappings — keeps domain classes clean.

---

## CRUD operations

```csharp
using var context = new AppDbContext();

// CREATE
var customer = new Customer { Name = "Ken", Email = "ken@example.com" };
context.Customers.Add(customer);
context.SaveChanges();   // INSERT executed here

// READ — all
var customers = context.Customers.ToList();

// READ — filtered
var ken = context.Customers.FirstOrDefault(c => c.Name == "Ken");

// READ by primary key (uses cache before hitting DB)
var byId = context.Customers.Find(1);

// UPDATE — EF tracks the loaded entity
ken.Email = "ken2@example.com";
context.SaveChanges();   // UPDATE executed here

// DELETE
context.Customers.Remove(ken);
context.SaveChanges();   // DELETE executed here

// DELETE without loading (disconnected)
context.Entry(new Customer { Id = 1 }).State = EntityState.Deleted;
context.SaveChanges();
```

**Add multiple:**
```csharp
context.Customers.AddRange(customer1, customer2, customer3);
context.SaveChanges();
```

**Raw SQL when needed:**
```csharp
context.Database.ExecuteSqlCommand("exec DeleteCustomerById {0}", id);
var results = context.Customers.FromSqlRaw("SELECT * FROM Customers WHERE Active = 1").ToList();
```

---

## Querying with LINQ

EF translates LINQ to SQL at query execution time (deferred execution).

```csharp
// All — both syntaxes are equivalent
var all = context.Customers.ToList();

// Filtered
var actives = context.Customers
    .Where(c => c.IsActive)
    .OrderBy(c => c.Name)
    .ToList();

// Projection — don't load full entity when you only need some fields
var names = context.Customers
    .Where(c => c.IsActive)
    .Select(c => new { c.Id, c.Name })
    .ToList();

// Aggregates
int count = context.Orders.Count(o => o.CustomerId == 1);
decimal total = context.Orders.Sum(o => o.Total);
```

> **Important:** The database connection is held open for the duration of an enumeration. Call `.ToList()` early to close the connection and materialise results into memory.

---

## Loading related data

### Eager loading — `Include`

Loads related entities in the same query (JOIN).

```csharp
var customers = context.Customers
    .Include(c => c.Orders)
    .ToList();

// Multi-level
var customers = context.Customers
    .Include(c => c.Orders)
        .ThenInclude(o => o.OrderItems)
    .ToList();
```

### Explicit loading

Load related data for a specific entity after the fact.

```csharp
var customer = context.Customers.Find(1);

// Load a collection
context.Entry(customer).Collection(c => c.Orders).Load();

// Load a reference
context.Entry(order).Reference(o => o.Customer).Load();
```

### Lazy loading

Loads related data automatically when a navigation property is accessed. Requires:
- `Microsoft.EntityFrameworkCore.Proxies` package
- `optionsBuilder.UseLazyLoadingProxies()`
- Navigation properties marked `virtual`

```csharp
// With lazy loading enabled — Orders loaded on first access
var customer = context.Customers.Find(1);
var count = customer.Orders.Count;  // triggers SQL here
```

> **Prefer eager loading** in most cases — lazy loading can cause N+1 query problems.

---

## Change tracking

EF tracks every entity loaded from the database. On `SaveChanges()`, it generates the minimal SQL needed.

| State | Meaning |
|---|---|
| `Added` | Will INSERT on SaveChanges |
| `Modified` | Will UPDATE on SaveChanges |
| `Deleted` | Will DELETE on SaveChanges |
| `Unchanged` | No SQL generated |
| `Detached` | Not tracked by this context |

```csharp
// Check state
var state = context.Entry(customer).State;

// Manually set state (useful for disconnected scenarios)
context.Entry(customer).State = EntityState.Modified;
context.SaveChanges();
```

**Tracking related objects:** When you add a tracked entity to a navigation collection, EF tracks the child too — even if you didn't explicitly call `Add` on the child.

---

## Migrations

Migrations keep the database schema in sync with your model using versioned change scripts.

```bash
# Create a migration (after changing your model)
dotnet ef migrations add InitialCreate

# Apply pending migrations to the database
dotnet ef database update

# Generate SQL script (for production deployments)
dotnet ef migrations script

# Roll back to a specific migration
dotnet ef database update MigrationName

# Remove the last unapplied migration
dotnet ef migrations remove
```

**Migration file:** EF generates a `.cs` file with `Up()` (apply) and `Down()` (rollback) methods. Review these before applying to production.

```csharp
public partial class AddCustomerEmail : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.AddColumn<string>(
            name: "Email",
            table: "Customers",
            nullable: true);
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropColumn(name: "Email", table: "Customers");
    }
}
```

**Apply migrations at startup (for dev/test):**
```csharp
using var scope = app.Services.CreateScope();
var db = scope.ServiceProvider.GetRequiredService<AppDbContext>();
db.Database.Migrate();
```

---

## Repository pattern with EF

In enterprise applications, wrap EF in a Repository + Unit of Work to decouple data access from business logic and enable testability.

```csharp
// Repository interface
public interface ICustomerRepository
{
    Task<Customer> GetByIdAsync(int id);
    Task<IEnumerable<Customer>> GetAllAsync();
    void Add(Customer customer);
    void Remove(Customer customer);
}

// EF implementation
public class CustomerRepository : ICustomerRepository
{
    private readonly AppDbContext _context;
    public CustomerRepository(AppDbContext context) => _context = context;

    public async Task<Customer> GetByIdAsync(int id)
        => await _context.Customers.FindAsync(id);

    public async Task<IEnumerable<Customer>> GetAllAsync()
        => await _context.Customers.ToListAsync();

    public void Add(Customer customer) => _context.Customers.Add(customer);
    public void Remove(Customer customer) => _context.Customers.Remove(customer);
}

// Unit of Work — saves across multiple repositories in one transaction
public class UnitOfWork : IUnitOfWork
{
    private readonly AppDbContext _context;
    public ICustomerRepository Customers { get; }
    public IOrderRepository Orders { get; }

    public UnitOfWork(AppDbContext context)
    {
        _context = context;
        Customers = new CustomerRepository(context);
        Orders = new OrderRepository(context);
    }

    public async Task<int> SaveAsync() => await _context.SaveChangesAsync();
}
```

**Register in DI:**
```csharp
services.AddScoped<IUnitOfWork, UnitOfWork>();
```

---

## Async operations

Always use async methods in web applications to avoid blocking threads.

```csharp
// Async CRUD
var customers = await context.Customers.ToListAsync();
var customer = await context.Customers.FindAsync(id);
var ken = await context.Customers.FirstOrDefaultAsync(c => c.Name == "Ken");

context.Customers.Add(newCustomer);
await context.SaveChangesAsync();
```

---

## Performance tips

| Issue | Solution |
|---|---|
| Loading too many columns | Use `.Select()` projections |
| N+1 queries | Use `.Include()` eager loading |
| Tracking overhead on read-only queries | `.AsNoTracking()` |
| Slow bulk inserts | Use `AddRange()` + single `SaveChanges()`, or `ExecuteBulkInsert` (EFCore.BulkExtensions) |
| Repeated queries for same data | Load once, cache in-memory |

```csharp
// No-tracking read — faster for read-only queries
var customers = context.Customers
    .AsNoTracking()
    .Where(c => c.IsActive)
    .ToList();
```

---

## See also
- [[CSharp]] — LINQ operators that EF uses for query building
- [[Databases-SQL]] — the SQL EF generates under the hood
- [[ASP-NET]] — DI registration and scoped DbContext lifetime
- [[Dependency-Injection]] — DbContext is registered as Scoped by default
- [[Testing]] — mock repositories with NSubstitute/Moq for unit tests
