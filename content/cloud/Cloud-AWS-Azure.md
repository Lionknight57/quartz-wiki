---
title: Cloud — AWS & Azure
tags: [cloud, aws, azure, infrastructure, serverless]
related: [Databases-SQL, Databases-NoSQL, Authentication-Security]
sources: [Aws/aws.md, Aws/aws-procedures.md, Azure/The Big Picture.md, Azure/Fundementals of Functions.md, Azure/Identity Management in Azure.md]
updated: 2026-04-12
---

# Cloud — AWS & Azure

Cloud computing delivers compute, storage, networking, and managed services over the internet. Key benefits: **lower cost** (pay as you go, no upfront hardware), **scalability** (scale up/down on demand), **reduced latency** (deploy closer to users globally), **redundancy** (distributed globally).

---

## AWS (Amazon Web Services)

A collection of cloud computing services that can work together or independently.

**Access methods:** Web Console, CLI, SDKs (Python/boto3, Node.js, .NET, etc.)

### Core services

| Service | Category | Purpose |
|---|---|---|
| **EC2** (Elastic Cloud Compute) | Compute | Virtual machines |
| **S3** (Simple Storage Service) | Storage | Object/file storage |
| **RDS** (Relational Database Service) | Database | Managed SQL databases |
| **Route 53** | DNS | Domain and routing management |
| **Lambda** | Serverless | Event-driven functions (no server management) |
| **DynamoDB** | Database | Managed NoSQL (key-value) |
| **Elastic Beanstalk** | PaaS | Deploy web apps without managing infrastructure |
| **VPC** (Virtual Private Cloud) | Networking | Isolated private network in AWS |
| **CloudFront** | CDN | Global content delivery |
| **CloudWatch** | Monitoring | Logs, metrics, alarms |

### EC2 setup steps

1. Choose an AMI (Amazon Machine Image) — OS + software
2. Select instance type — CPU, memory, network performance (small/medium/large families)
3. Configure instance — auto scaling, subnet, IAM role
4. Add storage (EBS volume)
5. Add tags
6. Configure security group (firewall rules)
7. Create or assign key pair for SSH access

### Why deploy globally?

- **Reduced latency** — servers closer to users
- **Increased redundancy** — multiple availability zones survive outages

---

## Azure (Microsoft Cloud)

Microsoft's cloud platform. Organised into service categories: Compute, Data Storage, Management, Integration, Identity, Networking.

### Azure Compute

| Option | Description |
|---|---|
| **Virtual Machines** | Linux/Windows VMs, scale sets, availability sets, managed disks |
| **App Services** | Managed web app hosting — framework choice, autoscale, deployment slots, health monitoring |
| **Azure Kubernetes Service (AKS)** | Managed Kubernetes cluster |
| **Azure Container Instances** | Run containers without managing servers |
| **Azure Functions** | Serverless — event-driven C# / Python / JS functions |
| **Logic Apps** | Low-code workflow automation |
| **Event Grid** | Event-based messaging between services |
| **Service Fabric** | Manages distributed microservices infrastructure |

### Azure Data Storage

| Service | Type |
|---|---|
| Azure SQL | Managed SQL Server |
| PostgreSQL / MySQL / MariaDB | Managed open-source relational |
| Table Storage | Key-value NoSQL |
| Blob Storage | Object storage (files, images, backups) |
| Queues | Message queue |
| Redis Cache | In-memory key-value cache |
| Cosmos DB | Multi-model globally distributed NoSQL |

### Azure Functions

Build a pipeline of small C# functions triggered by events (HTTP, timer, queue message, blob change).

```csharp
[FunctionName("ProcessOrder")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "post")] HttpRequest req,
    [Queue("orders")] IAsyncCollector<string> queue,
    ILogger log)
{
    string body = await new StreamReader(req.Body).ReadToEndAsync();
    await queue.AddAsync(body);
    return new OkObjectResult("Queued");
}
```

Develop in Visual Studio or CLI. Deploy and monitor via Azure Portal.

### Azure Identity Management

**Identity Provider** — Azure Active Directory (Azure AD / Entra ID). Centralised identity for users, apps, and services.

**Four pillars of identity:**
1. **Authentication** — prove who you are
2. **Authorization** — what are you allowed to do
3. **Administration** — manage identities and policies
4. **Auditing** — track who did what

**Role assignment:** Assign roles via groups, not directly to users (easier to manage at scale).

**JIT / JEA (Just-In-Time / Just-Enough-Access):**
- No standing privileges — access is granted on demand and expires
- Reduces attack surface from compromised accounts
- Key principle: grant minimum access for minimum time

**RBAC (Role-Based Access Control):** Permissions assigned to roles, users assigned to roles.

---

## AWS vs Azure quick comparison

| Feature | AWS | Azure |
|---|---|---|
| Virtual machines | EC2 | Virtual Machines |
| Serverless functions | Lambda | Azure Functions |
| Object storage | S3 | Blob Storage |
| Managed Kubernetes | EKS | AKS |
| Managed SQL | RDS | Azure SQL |
| NoSQL | DynamoDB | Cosmos DB |
| CDN | CloudFront | Azure CDN |
| DNS | Route 53 | Azure DNS |
| Identity | IAM | Azure AD / Entra ID |
| Monitoring | CloudWatch | Azure Monitor |

---

## See also
- Kubernetes / Docker — to be ingested (see sync list)
- [[Databases-SQL]] — RDS / Azure SQL
- [[Databases-NoSQL]] — DynamoDB / Cosmos DB
- [[Authentication-Security]] — IAM / Azure AD
