---
title: MCP — Model Context Protocol
tags: [tools, mcp, api, ai, dotnet, typescript, oauth, security]
related: [Claude Code, AI-Agents, Authentication-Security, ASP.NET]
sources: [MCP/MCP.md, Medium/2026-04/Medium Daily Digest - 2026-04-21.md]
updated: 2026-04-21
---

# MCP — Model Context Protocol

An open standard developed by Anthropic that lets AI assistants connect to external data sources and tools in a standardized way. MCP is the **new front-end for agentic AI** — it removes layers of abstraction between a user's question, the data, and the answer.

> For using MCP as a client (connecting Claude Code to servers), see [[Claude-Code#MCP — Model Context Protocol]].
> This page covers **building and deploying** MCP servers.

---

## Architecture

MCP follows a client-server model:

```
MCP Host (Claude Desktop, VS Code Copilot, Claude Code)
  └─ MCP Client  ←─ one per server connection (isolated, not shared)
       └─ MCP Server  ←─ JSON-RPC 2.0 over stdio or HTTP
            └─ External APIs, databases, file systems, services
```

**Why not just wrap an existing API?** MCP is not just an API. APIs are built around data shapes; MCP is built around user intent expressed as natural language. A well-designed MCP server simplifies parameters, handles fuzzy input, and provides context that lets the model reason correctly — rather than exposing raw API endpoints the model has to guess at.

---

## MCP Primitives

Three things an MCP server can expose:

| Primitive | Initiated by | Purpose |
|---|---|---|
| **Resources** | User / application | Data the user can attach to a conversation (documents, calendars, config) |
| **Tools** | Model | Actions Claude can call autonomously (submit form, query API, calculate) |
| **Prompts** | User | Pre-built instruction templates with arguments; like user-invocable tools |

---

## Resources

Resources let users "chat with their data." Each resource has:

```json
{
  "uri": "hrm://calendars/work",
  "name": "work-calendar",
  "title": "Company Work Calendar (human + LLM facing)",
  "description": "Hint for the model on how to use this resource (LLM facing only)",
  "mimeType": "application/json"
}
```

**Lifecycle:** `resources/list` → client lists available resources; `resources/read` → client reads a specific one by URI.

### Resource templates

For large or dynamic collections (product catalogs, employee records), static listing is impractical. Resource templates parameterise the URI so clients can load on demand:

```
URI template: hrm://documents/{documentId}
```

The model fills in the parameter and reads the specific resource. Note: Claude Desktop does **not** support resource templates (2026); VS Code Copilot does.

### Content types

Resources can return:
- **Text** (`TextResourceContents`) — JSON, plain text, markdown
- **Blob** (`BlobResourceContents`) — Base64-encoded binary (PDFs, images)

---

## Tools

Tools are model-initiated actions. Key properties:

```json
{
  "name": "requestTimeOff",
  "description": "...",
  "inputSchema": { ... },   // JSON Schema — auto-generated from C# types
  "outputSchema": { ... }   // for structured content responses
}
```

### Tool design — context engineering

> "Context engineering: delivering the right information at the right time in the right format."

**Common mistake:** wrapping a raw API endpoint as a tool. The model will fail on:
- Opaque IDs it can't know (`timeOffTypeId: "abc-123"`)
- Technical enums requiring domain knowledge
- Missing context (what absence types are available?)

**Design principles:**

| Anti-pattern | Fix |
|---|---|
| Raw ID parameters | Resolve IDs server-side; expose user-friendly enums |
| Timestamps/exact times | Accept half-day/full-day enums; translate to times internally |
| No error context | Wrap errors with descriptive messages the model can interpret |
| All data at once | Return only relevant context; use vector search for policy docs |
| Embedding entire PDFs | Convert to plain text (10× token reduction); use semantic search |

**Error handling:** return `isError: true` in the tool result content — the MCP SDK wraps thrown exceptions automatically.

### Content block types

Tools can return mixed content arrays:

| Type | Use |
|---|---|
| Text | Structured JSON, plain text summaries |
| Embedded resource | Inline resource data (calendar, document) — model can access without user attaching |
| Linked resource | Pointer to a resource URI — hint to client to read on demand (not supported in Claude Desktop) |
| Image / Audio | Binary media (not covered here) |

### Token cost awareness

Each token costs money. Budget your tool responses:

```
PDF as Base64     → ~13,000 tokens
PDF as plain text → ~1,300 tokens  (10× reduction)
Semantic search excerpt → <1,000 tokens  (best)
```

---

## Prompts

Prompts are user-initiated templates. They accept arguments and can return:
- Pre-filled text for the user to edit (VS Code behaviour — better UX)
- Attached resources plus instruction blocks (Claude Desktop behaviour)

```csharp
[McpServerPrompt, Description("Suggest best days to take time off")]
public static async Task<IList<PromptMessage>> SuggestTimeOff(string employeeId) { ... }
```

**Roles:** prompt messages carry a `role` attribute (`assistant` or `user`), hinting to the client how to format them. VS Code uses this to structure the prompt; Claude Desktop attaches everything flat.

**Argument gotcha:** all prompt arguments are strings in the spec — do your own type conversion and validation server-side.

---

## Transport modes

| | stdio | Streamable HTTP |
|---|---|---|
| **Runs** | Local machine | Local or remote |
| **Authentication** | Custom only | OAuth 2.1 supported |
| **Stateful streaming** | Yes (stream) | Optional (SSE) |
| **Config** | Command + args in client config | URL endpoint |
| **Use when** | Developer tools, local access, low latency | Enterprise, auth required, shared server |

**Stateless vs stateful HTTP:** serverless platforms (Azure Functions, AWS Lambda, Google Cloud Run) require stateless mode — they spin up per request. Stateful SSE is needed for elicitations and advanced streaming. Cloudflare Workers supports stateful via Durable Objects (TypeScript/JS only).

**Logging with stdio:** write logs to `stderr`, not `stdout`. Only JSON-RPC messages go to stdout; anything else causes parse errors in the client. Use `.NET` logging with `AddConsole` and `LogToStandardErrorThreshold = Trace`.

---

## Building an MCP server (C# / .NET)

### Scaffold

```bash
dotnet new console -n MyMcpServer
dotnet add package ModelContextProtocol --prerelease
dotnet add package Microsoft.Extensions.Hosting
```

### Minimal stdio server

```csharp
var builder = Host.CreateEmptyApplicationBuilder(settings: null);
builder.Services.AddMcpServer()
    .WithStdioServerTransport()
    .WithTools<MyToolsClass>()
    .WithResources<MyResourcesClass>()
    .WithPrompts<MyPromptsClass>();

builder.Logging.AddConsole(o => o.LogToStandardErrorThreshold = LogLevel.Trace);

var app = builder.Build();
await app.RunAsync();
```

### Switch to HTTP (ASP.NET Core)

```bash
dotnet add package ModelContextProtocol.AspNetCore
# Add <Project Sdk="Microsoft.NET.Sdk.Web"> to .csproj
```

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddMcpServer()
    .WithHttpTransport()
    .WithTools<MyToolsClass>();

var app = builder.Build();
app.MapMcp("/mcp");
app.MapGet("/healthz", () => "healthy");
await app.RunAsync();
```

### TypeScript — tool schema with Zod

```typescript
server.tool("add", { a: z.number(), b: z.number() }, async ({ a, b }) => ({
  content: [{ type: "text", text: String(a + b) }]
}));
```

---

## MCP Inspector

Browser-based testing tool for MCP servers:

```bash
# stdio server
npx @modelcontextprotocol/inspector dotnet run

# HTTP server (pass config file)
npx @modelcontextprotocol/inspector --config mcp.json --server my-server
```

Features: ping, list resources/tools/prompts, call tools, inspect raw JSON-RPC request/response history, test OAuth flows.

**Recommended:** wrap in a `package.json` with npm scripts so the team runs the same commands:
```json
{ "scripts": { "dev": "npx @modelcontextprotocol/inspector ...", "start": "dotnet run" } }
```

---

## Client configuration

### VS Code (`.vscode/mcp.json`)
```json
{
  "servers": {
    "my-server": {
      "command": "dotnet",
      "args": ["run", "--project", "src/MyMcpServer"],
      "env": { "MY_KEY": "${input:myKey}" }
    }
  }
}
```

### Claude Desktop (`claude_desktop_config.json`)
```json
{
  "mcpServers": {
    "my-server": {
      "command": "dotnet",
      "args": ["run", "--project", "C:/full/path/to/MyMcpServer"]
    }
  }
}
```

Log files for each server appear in the same folder as `claude_desktop_config.json`.

**Windows:** if `npx` fails to resolve, prefix with `cmd /c`.

---

## Distribution

| Method | Requires | Notes |
|---|---|---|
| **NuGet** | .NET SDK | Discoverable in registry; add `.mcp/server.json` metadata |
| **NPM / npx** | Node.js | Most common; works across platforms |
| **Docker** | Docker runtime | Platform-agnostic; larger footprint |
| **Standalone executable** | Nothing | Self-contained binary; distribute manually |

The packaging tech doesn't have to match the server's tech stack — the Azure MCP Server is written in C# but published as NPM, Docker, and NuGet.

---

## Security — OAuth 2.1

### Why OAuth for MCP

stdio has no built-in auth. Streamable HTTP supports OAuth 2.1, enabling:
- Passwordless login (user never types password into the AI tool)
- Federated identity / SSO (Microsoft Entra, Google, Okta)
- Delegated access — server acts **on behalf of** the user, not as a system identity

### OAuth flow overview

```
1. Client sends request → Server responds 401 + WWW-Authenticate header
   WWW-Authenticate: Bearer resource_metadata="https://server/.well-known/oauth-protected-resource"

2. Client fetches Protected Resource Metadata (PRM) document
   → Contains: authorization server URL, scopes

3. Client registers (Dynamic Client Registration or static client ID)

4. Authorization flow: user logs in in browser, grants consent
   → Client gets authorization code

5. Client exchanges code for JWT Bearer token

6. All subsequent requests include: Authorization: Bearer <token>

7. Server validates JWT (issuer, audience, signature)
```

### CORS

Browser-based clients (MCP Inspector) require CORS headers:
```csharp
builder.Services.AddCors(o => o.AddDefaultPolicy(p => p
    .WithOrigins("https://inspector.example.com")
    .AllowCredentials()
    .WithHeaders("mcp-*", "content-type", "authorization")));
app.UseCors();
```

### Dynamic vs Static client registration

| | Dynamic (DCR) | Static |
|---|---|---|
| **Client registers at runtime** | Yes | No — pre-registered |
| **Supported by** | MCP spec default | Most OAuth providers |
| **Microsoft Entra** | No | Yes |

If your OAuth server doesn't support DCR (Entra, most enterprise providers), configure a static client ID in the MCP client.

### Accessing auth context (C#)

```csharp
[McpServerTool]
public async Task<string> RequestTimeOff(RequestContext context)
{
    var user = context.User;            // ASP.NET Core ClaimsPrincipal
    var email = user.Identity?.Name;   // map to your user store
    // Use OBO flow to call downstream APIs on behalf of user
}
```

### On Behalf Of (OBO) flow

The MCP server receives the user's token → exchanges it for a delegated token → forwards to back-end APIs. The back-end API sees the authenticated user, not a system identity. This closes the "any employee can access any other employee's data" security hole.

---

## Hosting remote MCP servers

### Azure Functions

```csharp
// Stateless mode required for serverless
builder.Services.AddMcpServer()
    .WithHttpTransport(o => o.Stateless = true);
```

Use `azd up` with Azure Bicep IaC for one-command provisioning. Add a `/api/healthz` health check endpoint.

**Proxy header forwarding** (required when behind Azure Functions runtime proxy):
```csharp
app.UseForwardedHeaders(new() { ForwardedHeaders = ForwardedHeaders.All });
```

### Platform comparison

| Platform | Stateful HTTP | Auth support | Notes |
|---|---|---|---|
| Azure Functions | No (stateless only) | Full OAuth | Good .NET fit |
| AWS Lambda | No | Full OAuth | Same stateless constraint |
| Google Cloud Run | No | Full OAuth | Same |
| Cloudflare Workers | Yes (Durable Objects) | OAuth | JS/TS only |
| Any VPS / container | Yes | Full OAuth | Full control |

---

## Client compatibility matrix (2026)

| Feature | Claude Desktop | VS Code Copilot | MCP Inspector |
|---|---|---|---|
| Resources (static) | Yes | Yes | Yes |
| Resource templates | No | Yes | Yes |
| Embedded resources | Partial (no PDF) | Yes | Yes |
| Linked resources | No | Yes | Yes |
| Prompts | Attached flat | Slash command + editable | Yes |
| OAuth / auth | Connectors feature | Built-in Entra | Manual flow |

---

## Critical perspective — "MCP is Dead" (April 2026)

From Nick Babich's "MCP is Dead" (368 claps, 19 responses — UX Planet):

A widely-read contrarian take arguing that MCP adds unnecessary complexity when used inside Claude Code specifically. The argument:

- MCP was designed for **external system integration** (databases, APIs, services outside the AI tool)
- Using MCP for things Claude Code can do natively (read files, run commands, call APIs) adds a translation layer for no benefit
- The overhead of spinning up an MCP server process for local development tasks outweighs the value
- **Proposed alternative:** use Claude Code's built-in tools directly (Read, Bash, Write) and only reach for MCP when you genuinely need to connect to an external service that Claude Code can't access natively

**Nuanced reading:** this is a UX critique, not a technical one. The argument holds for local tooling — using MCP to wrap file reads or local scripts is overengineering. MCP remains the right choice for:
- External APIs requiring auth (GitHub, Figma, Gmail, databases)
- Services that need to be shared across a team (`.mcp.json` in git)
- Remote server deployments
- Cross-client integrations (VS Code Copilot + Claude Desktop sharing the same server)

> "MCP is a bridge to external systems. Claude Code's tools are the right choice for local work." — synthesis from the discussion

---

## See also
- [[Claude-Code#MCP — Model Context Protocol]] — using MCP as a client in Claude Code
- [[Authentication-Security]] — OAuth2, JWT, CORS fundamentals
- [[ASP-NET]] — ASP.NET Core middleware and hosting
- [[AI-Agents]] — agentic patterns that consume MCP servers
