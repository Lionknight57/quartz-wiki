---
title: ASP.NET Core
tags: [framework, backend, csharp, dotnet, web]
related: [CSharp, Entity Framework, Dependency Injection, Angular, Async Programming]
sources: [Asp.net/Asp.net.md, Asp.net/ASP.netCore MVC.md, Asp.net/ASP 2.Net.md, Asp.net/Razor.md, Asp.net/SignalR.md, Asp.net/Rx .net.md, Asp.net/Dependency Injection.md, Asp.net/Summary.md, Asp.net/What's New in ASP.NET Core 6.0.md, Medium/2026-05/Medium Daily Digest - 2026-05-19.md, Medium/2026-05/Medium Daily Digest - 2026-05-21.md, Medium/2026-05/Medium Daily Digest - 2026-05-24.md, Medium/2026-05/Medium Daily Digest - 2026-05-30.md]
updated: 2026-05-30
---

# ASP.NET Core

Microsoft's open-source, cross-platform web framework for building web apps, REST APIs, and real-time applications with C#. Runs on Windows, macOS, and Linux via .NET Core.

---

## Architecture

ASP.NET Core uses a **middleware pipeline** — each request flows through a series of middleware components in order.

```
Request → Middleware 1 → Middleware 2 → ... → Endpoint
                                              ↓
Response ← Middleware 1 ← Middleware 2 ← ... ←
```

**Host setup (Program.cs):**
```csharp
var builder = WebApplication.CreateBuilder(args);
// Register services
builder.Services.AddControllers();
builder.Services.AddDbContext<AppDbContext>();

var app = builder.Build();
// Configure middleware pipeline
app.UseHttpsRedirection();
app.UseRouting();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

`CreateDefaultBuilder` sets up:
- Kestrel web server
- IIS integration
- Logging
- `IConfiguration` (appsettings.json, environment variables, command-line args)

---

## MVC Pattern

**Model** — data and business logic  
**View** — Razor templates (`.cshtml`) for rendering HTML  
**Controller** — handles HTTP requests, calls services, returns responses

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly IProductService _service;
    public ProductsController(IProductService service) => _service = service;

    [HttpGet]
    public async Task<IActionResult> GetAll() =>
        Ok(await _service.GetAllAsync());

    [HttpGet("{id}")]
    public async Task<IActionResult> GetById(int id)
    {
        var product = await _service.GetByIdAsync(id);
        return product is null ? NotFound() : Ok(product);
    }

    [HttpPost]
    public async Task<IActionResult> Create(ProductDto dto)
    {
        var created = await _service.CreateAsync(dto);
        return CreatedAtAction(nameof(GetById), new { id = created.Id }, created);
    }
}
```

---

## Razor Views

Razor is a templating syntax that mixes C# with HTML. Used in MVC views and Razor Pages.

```csharp
@using MyApp.Models          // import namespace
@model Employee              // declare typed model

@{
    Layout = "~/Views/_Base.cshtml";   // master layout
    ViewBag.Title = "Employee Info";   // dynamic data
}

<h2>@Model.FullName</h2>

@using (Html.BeginForm())
{
    @Html.AntiForgeryToken()
    @Html.ValidationSummary()
    <div>@Html.TextBoxFor(x => x.FirstName)</div>
    <div>@Html.TextBoxFor(x => x.FamilyName)</div>
    <div>
        @Html.DropDownListFor(x => x.IsFullTime, new[] {
            new SelectListItem { Text = "Full Time", Value = "true" },
            new SelectListItem { Text = "Part Time", Value = "false" }
        })
    </div>
}
```

**Razor syntax quick reference:**

| Syntax | Purpose |
|---|---|
| `@Model.Property` | Output a model value |
| `@{ }` | Statement block |
| `@* comment *@` | Razor comment |
| `@if (condition) { }` | Conditional |
| `@foreach (var x in list) { }` | Loop |
| `@Html.TextBoxFor(x => x.Name)` | Input for model property |
| `@Html.AntiForgeryToken()` | CSRF protection token |
| `@Html.ValidationSummary()` | Show validation errors |

---

## Middleware

Common built-in middleware (order matters):

```csharp
app.UseExceptionHandler("/Error");
app.UseHsts();
app.UseHttpsRedirection();
app.UseStaticFiles();          // serve wwwroot files
app.UseRouting();
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
```

**Custom middleware:**
```csharp
app.Use(async (context, next) =>
{
    // before
    await next.Invoke();
    // after
});
```

### The 5-zone pipeline model (.NET 10)

Instead of memorising a 15-line incantation, group middleware into 5 zones that always run in this order. When a new middleware comes along, decide which zone it belongs to first — order within a zone is usually constrained by which middleware produces the context the next one needs.

| Zone | Purpose | Members |
|---|---|---|
| **1. Outer wrapper** | Catches everything; enforces HTTPS at the protocol level | `UseExceptionHandler`, `UseHsts`, `UseHttpsRedirection` |
| **2. Cheap rejections** | Fail fast before expensive work | `UseForwardedHeaders`, `UseRateLimiter`, `UseRequestTimeouts` |
| **3. Static / cached** | Short-circuit anonymous content | `UseResponseCompression`, `MapStaticAssets` |
| **4. Identity & access** | Order non-negotiable | `UseRouting`, `UseCors`, `UseAuthentication`, `UseAuthorization`, `UseAntiforgery`, `UseOutputCache` |
| **5. Endpoint layer** | Custom middleware + route mapping | `UseMiddleware<T>`, `MapControllers`, `MapHealthChecks` |

**Production-safe template (.NET 10):**

```csharp
var app = builder.Build();

// ZONE 1: Outer wrapper. Catches everything.
app.UseExceptionHandler("/error");
app.UseHsts();
app.UseHttpsRedirection();

// ZONE 2: Cheap rejections. Fail fast before expensive work.
app.UseForwardedHeaders();
app.UseRateLimiter();
app.UseRequestTimeouts();           // .NET 10 — sets deadline early

// ZONE 3: Static and cached responses. Skip the pipeline when possible.
app.UseResponseCompression();
app.MapStaticAssets();              // .NET 10 — replaces UseStaticFiles, build-time fingerprinted

// ZONE 4: Identity and access. Order is non-negotiable.
app.UseRouting();
app.UseCors();
app.UseAuthentication();
app.UseAuthorization();
app.UseAntiforgery();
app.UseOutputCache();

// ZONE 5: Custom middleware and endpoints.
app.UseMiddleware<RequestEnrichmentMiddleware>();
app.MapControllers();
app.MapHealthChecks("/health").AllowAnonymous();

app.Run();
```

### The rules behind each ordering (with the incident that taught it)

- **`UseExceptionHandler` must be first.** It wraps everything below it in a try-catch. Anything above it leaks raw stack traces to clients. *Incident: a forwarded-headers bug threw above a custom handler; raw stack traces went to real users in production for 20 minutes.*
- **`UseForwardedHeaders` before `UseRateLimiter`.** The limiter reads client IP; without forwarded headers it sees the load balancer IP, treating every user behind a corporate proxy as one user. *Incident: 200 employees at an enterprise customer shared one egress IP, burned through the 100/min limit in seconds, locked out for a day.*
- **`UseCors` before `UseAuthentication`.** Browser preflight `OPTIONS` requests don't carry credentials — if auth runs first, preflight fails and every cross-origin call breaks. *Incident: SPA went from working to all 401s overnight after CORS was moved below auth.*
- **`UseAuthentication` before `UseAuthorization`.** Authorization reads `HttpContext.User`; without authentication first it sees an empty `ClaimsPrincipal` and rejects authenticated users as anonymous. *Incident: swapping these two lines caused ~30% of authenticated users to randomly get 401s for 4 hours.*
- **`UseStaticFiles` / `MapStaticAssets` before auth.** Otherwise every CSS, image, and favicon goes through JWT validation and DB lookups for claims — to serve a 2KB image nobody needs to be authenticated to see.
- **`UseOutputCache` placement.** After auth for per-user cached responses (cache key includes identity); before auth (zone 3) for purely anonymous public content. Decide deliberately based on what you cache.

### 5-point Program.cs audit

Open Program.cs now and check:

1. Is `UseExceptionHandler` the first line after `app = builder.Build()`?
2. Is `UseForwardedHeaders` above every middleware that reads client IP (rate limiter, logging, audit)?
3. Is `UseCors` above `UseAuthentication`?
4. Is `UseAuthentication` above `UseAuthorization`?
5. Is `UseOutputCache` placed deliberately (per-user vs anonymous), not by accident?

Each fix takes 30 seconds; the collective effect is a pipeline you can defend in code review.

> Source: Abe Jaber — "The Only Middleware Pipeline I Trust in .NET 10 (After 4 Incidents)", CodeToDeploy, 2026-05.

---

## HttpClient: the `using` block that exhausts sockets

Standard `IDisposable` discipline is the wrong move for `HttpClient`. Wrapping each instance in `using` causes TCP sockets to pile up in `TIME_WAIT` (~240s on Windows, ~60s on Linux). At 200 req/s with a 240s wait, you accumulate 48,000 limbo sockets and exhaust the ~16,000 ephemeral ports your OS has. Every subsequent connection fails with `SocketException`.

```csharp
// WRONG — looks like correct IDisposable usage; kills production at scale
using var client = new HttpClient();
var response = await client.GetAsync(url);
```

**The "obvious fix" (static HttpClient) creates a worse problem.** It eliminates socket exhaustion but caches the DNS resolution for the lifetime of the application — so blue-green deploys, Azure Traffic Manager failovers, and Kubernetes service updates route traffic to decommissioned servers indefinitely.

**Correct: `IHttpClientFactory` with `AddHttpClient`.** The factory pools and rotates `HttpMessageHandler` instances on a 2-minute timer, which forces DNS re-resolution without opening fresh sockets per request.

```csharp
builder.Services.AddHttpClient<IPaymentsClient, PaymentsClient>(c =>
{
    c.BaseAddress = new Uri("https://payments.internal/");
})
.ConfigurePrimaryHttpMessageHandler(() => new SocketsHttpHandler
{
    PooledConnectionLifetime = TimeSpan.FromMinutes(2)   // belt-and-suspenders DNS refresh
});
```

**Trap to avoid: injecting a typed client into a singleton service.** The DI container resolves the singleton once → resolves the typed client once → resolves the handler once → handler never rotates → DNS never refreshes. You've recreated the static-HttpClient problem with extra steps. Consumers of typed clients must be **scoped or transient**.

**For unavoidable singleton consumers** (hosted services, background workers), skip the factory and configure `SocketsHttpHandler` directly:

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionLifetime  = TimeSpan.FromMinutes(2),    // forces DNS refresh
    PooledConnectionIdleTimeout = TimeSpan.FromMinutes(1),  // reaps idle conns
    MaxConnectionsPerServer   = 50                          // prevents one slow downstream from eating the budget
};
var client = new HttpClient(handler) { BaseAddress = new Uri("https://...") };
```

**Health check (run on a server right now):** count TIME_WAIT sockets to the downstream you suspect. A persistently high count points at this exact pattern.

> Source: Abe Jaber — ".NET 10 HttpClient: The `using` Block That Leaks Sockets and Kills Your API", Stackademic, 2026-05.

---

## AGENTS.md for .NET 10 — three-layer hierarchy for Codex / agent coding

`AGENTS.md` (the Codex/agent equivalent of `CLAUDE.md`) closes the gap between what an agent can read in your source and what it can only learn at runtime. In a vertical-slice .NET 10 API, **a single root file is not enough** — Codex working on Orders has no signal that Payments has different rules.

**Layered structure:**

| File | Scope | Purpose |
|---|---|---|
| `coding-standards.md` | Shared contract | Universal rules that apply everywhere. Each rule has an inline comment giving the one reason Codex cannot infer it from file text. |
| `AGENTS.md` (root) | What Codex must never touch | A wall, not a guidebook. Short. Absolute. Every line carries weight because it's short. |
| `src/<slice>/AGENTS.md` | Per-slice rules | Opens with a reference to `coding-standards.md`, then adds slice-only constraints: which files Codex may touch, which domain objects it must not generate, where this slice's authority stops. |

The slice layer is the one most articles miss for .NET — it's what makes the hierarchy actually work for vertical slices. Orders' file declares its entry point, its domain-events directory, its external dependencies. Payments' file mirrors the structure with different boundaries.

**Why every layer repeats the same three rules:** they map to behaviour Codex cannot reason about from static file text — runtime invariants invisible until production. AGENTS.md is the only place in the repo where you can encode that knowledge before the agent starts working.

> Source: Abe Jaber — "AGENTS.md for .NET 10: The Setup That Stops Codex From Breaking Production", CodeToDeploy, 2026-05.

---

## JsonSerializerOptions — cache it, don't recreate it

Every `new JsonSerializerOptions(...)` you build on a hot path discards the type/converter metadata cache and rebuilds it on the next call. Under traffic, that becomes meaningful CPU and p99 jitter — not a steady latency lift but spiky tails under concurrency.

**The trap (looks harmless):**

```csharp
public string Serialize<T>(T value) =>
    JsonSerializer.Serialize(value, new JsonSerializerOptions
    {
        PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
        WriteIndented        = false
    });
```

**Why it hurts**: the options instance owns the converter/type metadata cache. A fresh instance per call = rebuild that cache every call. Microsoft's guidance is blunt: if you use the same options repeatedly, **reuse them — it's safe across multiple threads**. An options instance can also become "locked" (read-only) after first use, and locked instances cache converter/type metadata aggressively.

**The fix — one cached singleton:**

```csharp
public static class JsonDefaults
{
    public static readonly JsonSerializerOptions Web = new()
    {
        PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
        WriteIndented        = false
    };
}

// Hot path:
var json = JsonSerializer.Serialize(value, JsonDefaults.Web);
```

Build at startup, **never mutate after first use**. If you genuinely need two policies (e.g. internal vs. public API), cache two named instances — don't generate infinite variants.

**DI-friendly variant** (if you avoid statics):

```csharp
builder.Services.AddSingleton(new JsonSerializerOptions
{
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
});
```

**Enforce with `CA1869`.** This Roslyn analyser exists specifically to flag `new JsonSerializerOptions(...)` on hot paths. Promote it to error in CI so it stops reappearing in code reviews:

```xml
<!-- Directory.Build.props or .csproj -->
<PropertyGroup>
  <AnalysisLevel>latest-recommended</AnalysisLevel>
</PropertyGroup>
```

```xml
<!-- .editorconfig -->
dotnet_diagnostic.CA1869.severity = error
```

**ASP.NET Core note**: the framework configures its own JSON options once at startup for the model binder/output formatter pipeline, so the built-in serialization path is already safe. This trap shows up when you manually serialize — in services, message producers, custom controllers, background workers, log formatters.

**Measurable signal**: after caching, look for **p99 smoothing under concurrency** rather than average-latency drops. The bug shows up as jitter, not steady cost.

> Source: Abe Jaber — ".NET 10 JSON: The JsonSerializerOptions Mistake That Burns CPU and Wrecks p99", CodeToDeploy, 2026-05. Tagged CA1869.

---

## Dependency Injection

ASP.NET Core has DI built in. Register services in `Program.cs`:

```csharp
builder.Services.AddTransient<IMyService, MyService>();   // new instance per request
builder.Services.AddScoped<IDbContext, AppDbContext>();    // one per HTTP request
builder.Services.AddSingleton<ICache, MemoryCache>();     // one for app lifetime
```

Inject via constructor:
```csharp
public class OrderService
{
    public OrderService(IDbContext db, ICache cache) { ... }
}
```

See [[Dependency-Injection]] for full lifetime comparison.

---

## Configuration

```csharp
// appsettings.json
{
  "ConnectionStrings": { "Default": "Server=..." },
  "AppSettings": { "MaxItems": 100 }
}

// Access in code
var conn = builder.Configuration.GetConnectionString("Default");
var max = builder.Configuration.GetValue<int>("AppSettings:MaxItems");

// Strongly typed options
builder.Services.Configure<AppSettings>(
    builder.Configuration.GetSection("AppSettings"));
```

---

## SignalR — Real-time Communication

ASP.NET Core SignalR enables real-time two-way communication between server and client (chat, live dashboards, notifications).

**Transport negotiation** (SignalR picks the best available):
1. **WebSockets** — full duplex, preferred
2. **Server-Sent Events (SSE)** — server-to-client only
3. **Long Polling** — fallback for older browsers

**Hub** — server-side class that manages connections and sends messages:

```csharp
public class ChatHub : Hub
{
    public async Task SendMessage(string user, string message) =>
        await Clients.All.SendAsync("ReceiveMessage", user, message);
}
```

```csharp
// Program.cs
builder.Services.AddSignalR();
app.MapHub<ChatHub>("/chatHub");
```

```javascript
// JavaScript client
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .build();

connection.on("ReceiveMessage", (user, message) => {
    console.log(`${user}: ${message}`);
});

await connection.start();
await connection.invoke("SendMessage", "Ken", "Hello!");
```

**Hub protocols** (serialisation format): JSON (default), MessagePack (binary, smaller)

**Scaling out** (multiple servers): Use Redis backplane to sync client state across servers. Enable sticky sessions on the load balancer for non-WebSocket transports.

---

## REST API conventions

```csharp
// Return types
return Ok(data);               // 200
return Created(uri, data);     // 201
return NoContent();            // 204
return BadRequest(errors);     // 400
return Unauthorized();         // 401
return NotFound();             // 404
```

**Model validation** (automatic with `[ApiController]`):
```csharp
public class CreateProductDto
{
    [Required] public string Name { get; set; }
    [Range(0.01, 9999)] public decimal Price { get; set; }
}
```

---

## OpenTelemetry in .NET 10 — production traps

Three gaps the OTel quickstart doesn't cover, each with a fix.

### Trap 1 — single span per request (the empty rectangle)

Default setup creates one `Activity` per HTTP request. Everything inside the handler (cache lookups, DB calls, aggregations) collapses into the parent — you see an 800ms span with no child spans.

**Fix:** declare an `ActivitySource` per service, wrap each unit of work:

```csharp
public sealed class OrderService
{
    public static readonly ActivitySource ActivitySource = new("OrderService");

    public async Task<OrderResponse> GetOrderAsync(int id, CancellationToken ct)
    {
        using var activity = ActivitySource.StartActivity("OrderService.GetOrder");
        activity?.SetTag("order.id", id);

        using (ActivitySource.StartActivity("Cache.Lookup"))
        {
            var cached = await _cache.GetStringAsync($"order:{id}", ct);
            if (cached is not null) { activity?.SetTag("cache.hit", true); return Deserialize(cached); }
        }

        Order order;
        using (ActivitySource.StartActivity("Db.LoadOrder"))
            order = await _repo.GetByIdAsync(id, ct);

        PricingDetails pricing;
        using (ActivitySource.StartActivity("Pricing.Resolve"))
            pricing = await _pricing.GetForOrderAsync(order, ct);

        return MapResponse(order, pricing);
    }
}
```

Register the source:
```csharp
.WithTracing(t => t.AddSource("OrderService").AddAspNetCoreInstrumentation()...)
```

### Trap 2 — async context loss (orphaned fire-and-forget spans)

`Task.Run` schedules work on the thread pool; `Activity.Current` is already disposed by the time the async work executes, making those spans invisible.

**Fix:** capture `Activity.Current?.Context` before the parent ends:

```csharp
var parentContext = Activity.Current?.Context ?? default;
_ = Task.Run(async () =>
{
    using var auditActivity = OrderService.ActivitySource.StartActivity(
        "Audit.Record", ActivityKind.Internal, parentContext);
    try { await _auditService.RecordAsync(order); }
    catch (Exception ex)
    {
        auditActivity?.SetStatus(ActivityStatusCode.Error, ex.Message);
        auditActivity?.AddException(ex);
        throw;
    }
});
```

For repeated use, push context propagation into a `BackgroundTaskQueue` hosted service rather than handling it per-call.

### Trap 3 — head-based sampling silently drops errors

At 1% sampling and 0.05% error rate, you record an error trace roughly every 16 minutes. By the time you open the dashboard, the evidence is gone.

**Fix A — force-record errors in the app:**
```csharp
catch (Exception ex)
{
    activity?.SetStatus(ActivityStatusCode.Error, ex.Message);
    activity?.ActivityTraceFlags |= ActivityTraceFlags.Recorded; // force this trace kept
    throw;
}
```

**Fix B (production-grade) — tail-based sampling in the OTel collector:**
```yaml
processors:
  tail_sampling:
    decision_wait: 5s
    policies:
      - name: errors
        type: status_code
        status_code: { status_codes: [ERROR] }
      - name: slow
        type: latency
        latency: { threshold_ms: 500 }
      - name: baseline
        type: probabilistic
        probabilistic: { sampling_percentage: 1 }
```

App emits everything; collector keeps errors + slow traces + 1% baseline.

### Production-safe registration

```csharp
builder.Services.AddOpenTelemetry()
    .WithTracing(t => t
        .AddSource("OrderService", "PaymentService")
        .AddAspNetCoreInstrumentation(o =>
        {
            o.RecordException = true;
            o.Filter = ctx => !ctx.Request.Path.StartsWithSegments("/health");
        })
        .AddHttpClientInstrumentation()
        .AddEntityFrameworkCoreInstrumentation()
        .SetSampler(new ParentBasedSampler(new TraceIdRatioBasedSampler(0.05)))
        .AddOtlpExporter(o => o.Endpoint = new Uri(config["OTEL_ENDPOINT"]!)));
```

### Instrumentation checklist

- One `ActivitySource` per service; one `Activity` per logical unit of work
- Capture `Activity.Current.Context` before any `Task.Run` or fire-and-forget
- `ParentBased` sampler in app; tail sampling in collector for errors and slow requests
- Tag spans with filterable attributes: `order.id`, `cache.hit`, `db.table`
- Audit one production trace per month by hand, looking for missing spans

> Source: Abe Jaber — "OpenTelemetry in .NET 10: The Instrumentation Gap" (CodeToDeploy, May 2026)

---

## See also
- [[CSharp]] — language fundamentals
- [[Entity-Framework]] — data access layer for ASP.NET apps
- [[Dependency-Injection]] — DI lifetime scopes
- [[Angular]] — common frontend partner to ASP.NET Core APIs
- [[Async-Programming]] — async controllers and services
