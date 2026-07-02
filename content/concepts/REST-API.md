---
title: REST API
tags: [concepts, api, rest, http, aspnet, web-api]
related: [ASP.NET, Authentication-Security, TypeScript-JavaScript]
sources: [Rest.md]
updated: 2026-04-19
---

# REST API

REST (Representational State Transfer) is an **architectural style** for networked systems, coined by Roy Fielding. It is not a standard — using Web API / ASP.NET doesn't automatically make an API RESTful.

> Pragmatism is not a swear word. Don't get lost in adhering to every constraint at all costs.

---

## Architectural constraints

A truly RESTful API must satisfy these 6 constraints:

| Constraint | What it means |
|---|---|
| **Uniform Interface** | Resources identified by URIs; standard verbs; self-descriptive messages; HATEOAS |
| **Statelessness** | All state needed to handle the request is contained within the request itself — no server-side session |
| **Client-Server** | UI and data concerns are separated; client doesn't know how data is stored |
| **Cacheable** | Every response must declare itself cacheable or not |
| **Layered System** | Client cannot tell whether it's talking directly to the server or through proxies/load balancers |
| **Code on Demand** | (Optional) Server can extend client functionality via scripts |

### Uniform Interface — the key constraint

- **Resource identification** — resources are identified by URIs; the representation (JSON) is separate from the resource
- **Manipulation via representations** — client has enough info to modify or delete a resource
- **Self-descriptive messages** — message includes enough information to describe how to process it (Content-Type, Accept headers)
- **HATEOAS** — responses include links to related actions; API is self-documenting; clients don't hard-code URIs

---

## URI design

Use **nouns** to describe resources, **HTTP verbs** to act on them:

```
# Good — nouns, hierarchical
GET  api/expensegroups
GET  api/expensegroups/1
GET  api/expensegroups/1/expenses
GET  api/expensegroups/1/expenses/1

# Bad — verbs in URI
GET  api/getExpenseGroup
POST api/createExpense
```

**URI for a computed resource** — use the noun of the computed concept:
```
# Prefer
GET api/expensegroups/1/total

# Over
GET api/expensegrouptotals/1   ← resource doesn't really exist
```

---

## HTTP methods and status codes

| Method | Use | Success | Not Found | Bad Input | Error |
|---|---|---|---|---|---|
| `GET` | Retrieve | 200 OK | 404 | 400 | 500 |
| `POST` | Create | 201 Created | — | 400 | 500 |
| `PUT` | Full replace | 200 OK | 404 | 400 | 500 |
| `PATCH` | Partial update | 200 OK | 404 | 400 | 500 |
| `DELETE` | Remove | 204 No Content | 404 | 400 | 500 |

**General codes:**
- `401` Unauthorized — not authenticated
- `403` Forbidden — authenticated but no permission
- `405` Method Not Allowed

### POST — Create

```csharp
[HttpPost]
public IHttpActionResult Post([FromBody] ExpenseGroupDto expenseGroup)
{
    if (expenseGroup == null) return BadRequest();
    var result = _repository.InsertExpenseGroup(...);
    if (result.Status == RepositoryActionStatus.Created)
        return Created(Request.RequestUri + "/" + newGroup.Id, newGroup);
    return BadRequest();
}
```

### PUT — Full update

```csharp
[HttpPut]
public IHttpActionResult Put(int id, [FromBody] ExpenseGroupDto expenseGroup)
{
    if (expenseGroup == null) return BadRequest();
    var result = _repository.UpdateExpenseGroup(...);
    if (result.Status == RepositoryActionStatus.Updated) return Ok(updatedGroup);
    if (result.Status == RepositoryActionStatus.NotFound) return NotFound();
    return BadRequest();
}
```

### PATCH — Partial update

PATCH uses `JsonPatchDocument` — a sequence of operations (replace, copy, remove) applied to the resource:

```json
[
  { "op": "replace", "path": "/title", "value": "New Title" },
  { "op": "copy",    "from": "/title", "path": "/description" }
]
```

```csharp
[HttpPatch]
public IHttpActionResult Patch(int id, [FromBody] JsonPatchDocument<ExpenseGroupDto> patchDoc)
{
    var eg = _factory.CreateExpenseGroup(_repository.GetExpenseGroup(id));
    patchDoc.ApplyTo(eg);
    var result = _repository.UpdateExpenseGroup(_factory.CreateExpenseGroup(eg));
    ...
}
```

Content-Type for PATCH: `application/json-patch+json`

### DELETE

```csharp
[HttpDelete]
public IHttpActionResult Delete(int id)
{
    var result = _repository.DeleteExpenseGroup(id);
    if (result.Status == RepositoryActionStatus.Deleted) return StatusCode(HttpStatusCode.NoContent);
    if (result.Status == RepositoryActionStatus.NotFound) return NotFound();
    return BadRequest();
}
```

---

## Sorting

Pass sort field via query string. Use `System.Linq.Dynamic` for dynamic ordering:

```
GET api/expensegroups?sort=title
GET api/expensegroups?sort=expensegroupstatusid,title   # multiple fields
GET api/expensegroups?sort=-title                        # descending (prefix -)
```

```csharp
[HttpGet]
public IHttpActionResult Get(string sort = "id")
{
    var groups = _repository.GetExpenseGroups().ApplySort(sort);
    return Ok(groups.ToList().Select(eg => _factory.CreateExpenseGroup(eg)));
}
```

---

## Filtering

Map query string parameters to WHERE clauses in the repository:

```
GET api/expensegroups?sort=title&status=open
```

```csharp
public IHttpActionResult Get(string sort = "id", string status = null, string userId = null)
{
    int statusId = -1;
    if (status != null) {
        switch (status.ToLower()) {
            case "open":      statusId = 1; break;
            case "confirmed": statusId = 2; break;
            case "processed": statusId = 3; break;
        }
    }
    return Ok(expenseGroups
        .ApplySort(sort)
        .Where(eg => statusId == -1 || eg.ExpenseGroupStatusId == statusId)
        .Where(eg => userId == null || eg.UserId == userId)
        .ToList().Select(...));
}
```

---

## Paging

Always page large collections. Return paging metadata in the **`X-Pagination` response header**, not in the body (body is the resource).

```
GET api/expensegroups?sort=id&page=1&pageSize=5
```

```csharp
public IHttpActionResult Get(string sort = "id", int page = 1, int pageSize = 5)
{
    var totalCount = expenseGroups.Count();
    var totalPages  = (int)Math.Ceiling((double)totalCount / pageSize);

    var prevLink = page > 1
        ? urlHelper.Link("ExpenseGroupsList", new { page = page - 1, pageSize, sort }) : null;
    var nextLink = page < totalPages
        ? urlHelper.Link("ExpenseGroupsList", new { page = page + 1, pageSize, sort }) : null;

    var paginationHeader = new {
        currentPage = page, pageSize, totalCount, totalPages,
        previousPageLink = prevLink, nextPageLink = nextLink
    };
    HttpContext.Current.Response.Headers.Add("X-Pagination",
        JsonConvert.SerializeObject(paginationHeader));

    return Ok(expenseGroups
        .Skip(pageSize * (page - 1))
        .Take(pageSize)
        .ToList().Select(...));
}
```

---

## Data shaping

Two forms:

### 1 — Field selection (`?fields=id,title`)

Return only requested fields using `ExpandoObject` + reflection:

```csharp
public object CreateDataShapedObject(ExpenseDto expense, List<string> fields)
{
    if (!fields.Any()) return expense;

    var result = new ExpandoObject() as IDictionary<string, object>;
    foreach (var field in fields)
    {
        var value = expense.GetType()
            .GetProperty(field, BindingFlags.IgnoreCase | BindingFlags.Public | BindingFlags.Instance)
            .GetValue(expense, null);
        result.Add(field, value);
    }
    return result;
}
```

Usage: `GET api/expenses?fields=id,title,amount`

### 2 — Include associations (`?includeExpenses=true`)

Eager-load related data based on query parameter:

```csharp
IQueryable<ExpenseGroup> groups = includeExpenses
    ? _repository.GetExpenseGroupsWithExpenses()
    : _repository.GetExpenseGroups();
```

---

## Caching with ETags

ETags let clients avoid downloading unchanged resources:

```
# First request
GET  api/expenses/1  →  200 + body + ETag: W/"123456789"

# Subsequent GET — client sends the ETag
GET  api/expenses/1
If-None-Match: W/"123456789"
→  304 Not Modified  (no body — saves bandwidth)

# Conditional PUT — prevent lost updates
PUT  api/expenses/1
If-Match: W/"123456789"
→  412 Precondition Failed  (if resource changed since client fetched it)
```

**ASP.NET Web API:** `CacheCow.Server` NuGet package handles ETags automatically:
```csharp
config.MessageHandlers.Add(new CacheCow.Server.CachingHandler(config));
```

**Output caching** (non-REST but practical): `Strathweb.CacheOutput.WebApi2` — caches the response on the server, reducing DB hits. Not strictly REST (bypasses HTTP cache semantics) but reduces load.

---

## API versioning

Three strategies for non-breaking evolution:

| Strategy | How | Pros | Cons |
|---|---|---|---|
| **URI versioning** | `/api/v2/expenses/1` | Simple, visible, cacheable | URI should identify a resource, not a version |
| **Content negotiation** | `Accept: application/vnd.myservice.v2+json` | Purist REST | Complex for clients |
| **Custom header** | `api-version: 2` | Clean URI, explicit | Not standard HTTP |

**URI versioning** is most common in practice despite the theoretical objection.

**Custom header + content type routing** in Web API:

```csharp
// Route constraint that checks api-version header or Accept vnd type
public bool Match(HttpRequestMessage request, ...) {
    var version = GetVersionFromCustomRequestHeader(request)
                ?? GetVersionFromCustomContentType(request);
    return ((version ?? DefaultVersion) == AllowedVersion);
}
```

---

## Content negotiation (format)

Force JSON output in ASP.NET Web API:

```csharp
// Option 1 — make JSON handle text/html requests (browsers)
config.Formatters.JsonFormatter.SupportedMediaTypes
    .Add(new MediaTypeHeaderValue("text/html"));

// Option 2 — remove XML formatter entirely
config.Formatters.XmlFormatter.SupportedMediaTypes.Clear();

// Improve JSON output formatting
config.Formatters.JsonFormatter.SerializerSettings.Formatting
    = Newtonsoft.Json.Formatting.Indented;
config.Formatters.JsonFormatter.SerializerSettings.ContractResolver
    = new CamelCasePropertyNamesContractResolver();
```

---

## See also
- [[ASP-NET]] — ASP.NET Core minimal APIs and controllers
- [[Authentication-Security]] — OAuth2 / OpenID Connect for API security
- [[TypeScript-JavaScript]] — Express REST API in Node.js
