---
title: Databases — NoSQL
tags: [database, nosql, redis, elasticsearch, cosmosdb, graph, mongodb]
related: [Databases-SQL, Entity Framework, Cloud-AWS-Azure]
sources: [DataBase/NoSQL.md, DataBase/CouchDB.md, DataBase/Couchbase.md, DataBase/Redis.md, DataBase/ElasticSearch.md, DataBase/IndexDB.md, DataStax.md, Graph Dbs/CosmosDB.md, Graph Dbs/Gremlin.md]
updated: 2026-04-12
---

# Databases — NoSQL

NoSQL databases are non-relational, schema-free, distributed data stores designed for scale and flexibility.

**Common traits:**
- Non-relational, schema-free
- **Eventual consistency** (not ACID) — data propagates across nodes over time
- Distributed across commodity servers — no single point of failure
- Developed at and optimised for "web scale" (Google, Amazon, Facebook)

---

## CAP Theorem

Any distributed system can guarantee only 2 of 3:

| Property | Meaning |
|---|---|
| **Consistency** | All nodes see the same data at the same time |
| **Availability** | Every request gets a response |
| **Partition Tolerance** | System works even if network splits nodes |

NoSQL systems typically choose **AP** (Available + Partition Tolerant) at the cost of strong consistency. Use eventual consistency where brief staleness is acceptable (catalogue data, social feeds), not where precision is critical (bank balances, inventory).

**Compromises in NoSQL:**
- Eventual consistency (not immediate)
- Write buffering
- Only primary keys can be indexed (in many systems)
- Queries may require custom programming (no ad-hoc SQL)
- Tooling is less mature than relational

---

## NoSQL types

| Type | Examples | Best for |
|---|---|---|
| **Document** | MongoDB, CouchDB, Couchbase | JSON documents, flexible schema |
| **Key-Value** | Redis, DynamoDB | Caching, sessions, simple lookups |
| **Column Family** | Cassandra, DataStax | Wide rows, time-series, analytics |
| **Graph** | Cosmos DB (Gremlin), Neo4j | Highly connected data, social graphs |
| **Search** | Elasticsearch | Full-text search, log analytics |

---

## Redis

**RE**mote **DI**ctionary **S**ervice — open-source in-memory key-value store. Extremely fast because data lives in RAM. Optionally persisted to disk.

**Supported value types:** Strings, Lists, Sets, Sorted Sets, Hashes, Streams

```bash
# Start server
redis-server

# CLI
redis-cli

# Basic commands
SET greeting "Hello"
GET greeting
DEL greeting

# Expiry (TTL)
SET session:abc123 "user:42" EX 3600   # expires in 1 hour
TTL session:abc123

# List
LPUSH queue job1 job2
RPOP queue

# Hash
HSET user:1 name "Ken" email "ken@example.com"
HGET user:1 name
HGETALL user:1
```

**Use cases:** Session storage, caching, rate limiting, pub/sub messaging, job queues, leaderboards.

**Replication:** Master-replica setup. Built-in LUA scripting (like stored procedures).

> Note: No official Windows version — use WSL or Docker on Windows.

---

## Elasticsearch

Distributed, full-text search engine built on Apache Lucene. NoSQL document store with powerful querying.

**Hierarchy:** Cluster → Nodes → Indexes → Shards → Documents

```json
// A document
{
  "id": 1,
  "name": "Server Alpha",
  "color": "blue",
  "hostname": "default"
}
```

**Why Elasticsearch:**
- Index millions of documents and search in milliseconds
- Full-text search across entire document content (not just exact fields)
- Aggregations and trend analysis
- Multi-tenant capable
- RESTful HTTP interface

```bash
# Index a document
PUT /products/_doc/1
{ "name": "Widget", "description": "A useful widget" }

# Search
GET /products/_search
{ "query": { "match": { "description": "useful" } } }

# Aggregation
GET /orders/_search
{ "aggs": { "by_category": { "terms": { "field": "category" } } } }
```

**Architecture:** Documents → Shards (distributed across nodes) → Replicas (redundancy)

---

## Cosmos DB (Azure)

Microsoft's globally distributed, multi-model NoSQL database. Supports multiple APIs:

| API | Data model | Use when |
|---|---|---|
| **Core (SQL)** | JSON documents | General document store |
| **MongoDB API** | BSON documents | Migrate from MongoDB |
| **Gremlin API** | Graph | Highly connected data |
| **Table API** | Key-value | Replace Azure Table Storage |
| **Cassandra API** | Column family | Wide-row data |

Cosmos DB is designed for global distribution — replicate data across any Azure region with one click.

---

## Graph databases

Graph databases model data as **vertices** (nodes) and **edges** (relationships), each with properties.

```
Vertex: Person { name: "Ken", age: 30 }
Edge:   KNOWS { since: 2020 }  →  Vertex: Person { name: "Alice" }
```

**Gremlin** — Apache TinkerPop graph traversal language (used by Cosmos DB, JanusGraph):

```groovy
// Find all people Ken knows
g.V().has('name', 'Ken').out('KNOWS')

// Traversal methods
.in()    // vertices with edges pointing IN to current vertex
.out()   // vertices with edges pointing OUT from current vertex
.both()  // both directions
.inE()   // edges coming in
.outE()  // edges going out
.count() // count traversal results

// Example: friends of friends
g.V().has('name', 'Ken').out('KNOWS').out('KNOWS').values('name')
```

**Use cases:** Social networks, recommendation engines, fraud detection, knowledge graphs.

---

## IndexedDB (Browser)

Client-side NoSQL database built into browsers. Stores structured data with indexes, supports transactions.

```javascript
const request = indexedDB.open('MyDB', 1);

request.onupgradeneeded = (e) => {
    const db = e.target.result;
    db.createObjectStore('users', { keyPath: 'id' });
};

request.onsuccess = (e) => {
    const db = e.target.result;
    const tx = db.transaction('users', 'readwrite');
    tx.objectStore('users').add({ id: 1, name: 'Ken' });
};
```

Larger capacity than localStorage (~50–250MB+). Use for offline-first apps.

---

## See also
- [[Databases-SQL]] — relational alternative; use when data is highly structured and relational
- Node.js + MongoDB — to be ingested (see sync list)
- [[Cloud-AWS-Azure]] — DynamoDB (AWS), Cosmos DB (Azure)
- [[CSharp]] — `StackExchange.Redis` for Redis in .NET
