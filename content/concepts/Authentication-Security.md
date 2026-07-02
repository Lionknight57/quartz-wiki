---
title: Authentication & Security
tags: [security, authentication, authorization, oauth, jwt, pci]
related: [ASP-NET, CSharp, Angular]
sources: [Authentication.md, OAuth.md, PCI.md]
updated: 2026-04-12
---

# Authentication & Security

## Core concepts

| Term | Meaning |
|---|---|
| **Authentication** | Proving identity — *who are you?* |
| **Authorization** | Determining access — *what are you allowed to do?* |
| **Accounting** | Tracking what was done — audit logs |

**Principle of Least Privilege** — grant only the minimum permissions needed.  
**Separation of Duties** — no single person/system has end-to-end control over critical processes.

---

## Authentication categories

| Factor | Examples |
|---|---|
| Something you **know** | Password, PIN |
| Something you **have** | Smart card, PKI certificate, hardware key |
| Something you **are** | Fingerprint, facial recognition, retinal scan |

Multi-factor authentication (MFA) combines two or more factors.

---

## Authorization types

| Type | How it works |
|---|---|
| **Central/Explicit** | Permissions granted directly to user/resource |
| **Decentral/Implicit** | Permissions inferred from group/role membership |
| **RBAC** (Role-Based Access Control) | Permissions assigned to roles, users assigned to roles |

---

## Security tokens

A security token is a protected data structure containing **claims** — facts about the issuer and the subject. Tokens are:
- **Signed** — tamper-proof and authentic
- **Time-limited** — contain an expiration

**Token evolution:**

| Format | Encoding | Notes |
|---|---|---|
| SAML 1.1/2.0 | XML | Enterprise standard, verbose, complex |
| SWT (Simple Web Token) | Form-URL encoded | Symmetric signatures only |
| **JWT** (JSON Web Token) | JSON | Modern standard; symmetric and asymmetric signing |

---

## JWT — JSON Web Tokens

Three base64url-encoded parts separated by dots: `header.claims.signature`

**Header:** metadata — algorithm used
```json
{ "typ": "JWT", "alg": "HS256" }
```

**Claims payload:**
```json
{
  "iss": "http://myIssuer",       // issuer
  "aud": "http://myResource",     // audience
  "sub": "alice",                 // subject
  "iat": 1348819380,              // issued at
  "exp": 1348823380,              // expiration
  "scope": "read search"          // custom claims
}
```

**Producing a JWT (.NET):**
```csharp
var token = new JwtSecurityToken(
    issuer: "http://myIssuer",
    audience: "http://myResource",
    claims: GetClaims(),
    signingCredentials: GetKey(),
    notBefore: DateTime.UtcNow,
    expires: DateTime.UtcNow.AddHours(1));

var tokenString = new JwtSecurityTokenHandler().WriteToken(token);
```

**Consuming/validating a JWT (.NET):**
```csharp
var validationParams = new TokenValidationParameters
{
    ValidIssuer = "http://myIssuer",
    ValidAudience = "http://myResource",
    IssuerSigningKey = GetSigningKey()
};
var handler = new JwtSecurityTokenHandler();
var principal = handler.ValidateToken(tokenString, validationParams, out _);
```

JWT advantages: easy to create, transmit, parse, and validate. Mandatory in OpenID Connect.

---

## OAuth 2.0

Open protocol for delegated authorisation — lets a third-party app obtain limited access to a resource on behalf of a user, without sharing credentials.

**RFC 6749 (2012)** — the current standard.

**Roles:**
| Role | Description |
|---|---|
| **Resource Owner** | The user who owns the data |
| **Client** | The app requesting access |
| **Authorization Server** | Issues tokens after authenticating the user |
| **Resource Server** | Hosts the protected API/data |

**OAuth 2.0 flow (Authorization Code — most common):**
```
1. Client redirects user to Authorization Server
2. User authenticates and grants consent
3. Authorization Server returns authorization code to client
4. Client exchanges code for access token (server-to-server)
5. Client uses access token to call Resource Server
```

**OAuth 2.0 is for authorisation, NOT authentication.** It answers "what can this app access?" not "who is this user?".

---

## OpenID Connect (OIDC)

Built on top of OAuth 2.0 — adds authentication. Returns an **ID token** (a JWT containing user identity claims) in addition to the access token.

```
OAuth 2.0 → authorisation (access token)
OIDC       → authentication (id token) + authorisation (access token)
```

Common OIDC claims in the ID token: `sub` (user ID), `name`, `email`, `picture`.

---

## Modern vs legacy security stacks

| Era | Stack |
|---|---|
| Enterprise / intranet | SAML, Kerberos, LDAP, WS-Federation |
| Mobile / modern web | OAuth 2.0, OpenID Connect, JWT |

The mobile revolution drove the shift — SOAP/SAML don't work well on mobile. JSON-based tokens became the "common denominator".

---

## PCI DSS (Payment Card Industry Data Security Standard)

Compliance framework for organisations handling credit card data.

Key activities:
- **Control testing** — documenting assigned PCI controls to satisfy audit requirements
- **Evidence documentation** — must meet standards defined in the Good Evidence Guide
- **Audit trail** — detailed logs of who accessed cardholder data and when

PCI applies whenever your application stores, processes, or transmits cardholder data.

---

## ASP.NET Core auth patterns

```csharp
// JWT bearer authentication setup
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidIssuer = "https://myapp.com",
            ValidAudience = "https://myapi.com",
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(config["Jwt:Key"]))
        };
    });

// In pipeline
app.UseAuthentication();
app.UseAuthorization();
```

```csharp
// Protect endpoints
[Authorize]                            // any authenticated user
[Authorize(Roles = "Admin")]           // role-based
[Authorize(Policy = "CanEdit")]        // policy-based
[AllowAnonymous]                       // explicitly public
```

---

## See also
- [[ASP-NET]] — authentication middleware setup
- [[CSharp]] — `System.Security.Claims`, `ClaimsPrincipal`
- [[Angular]] — JWT handling in HTTP interceptors
