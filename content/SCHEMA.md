# Wiki Schema

This file defines the conventions for the `Learn/wiki/` knowledge base.
The LLM reads this before every edit. The human reads it to understand how things are organised.

## Directory structure

```
wiki/
  SCHEMA.md         ← this file
  index.md          ← full catalog of all wiki pages (one-line summaries)
  log.md            ← append-only ingest/query log
  overview.md       ← high-level synthesis of the whole knowledge base

  concepts/         ← fundamental ideas that cut across languages/frameworks
  languages/        ← one page per programming language
  frameworks/       ← one page per framework or library
  tools/            ← dev tools, IDEs, CLIs, cloud services
  cloud/            ← cloud platforms and infrastructure
  databases/        ← database technologies and ORM patterns
  patterns/         ← design patterns, architecture patterns
```

## Page frontmatter

Every wiki page must start with:

```yaml
---
title: <page title>
tags: [<category>, ...]
related: [<WikiLink>, ...]
sources: [<raw filename>, ...]
updated: YYYY-MM-DD
---
```

## Cross-references

Use Obsidian wiki links: `[[Page Name]]`
Use section anchors when relevant: `[[Page Name#Section]]`

## Image references

Images from raw exports live in `Learn/raw/<Section>_files/`.
Reference them from wiki pages as: `../raw/<Section>_files/media/imageN.png`

## Conventions

- Strip all `onenote://` links — replace with `[[WikiLink]]` if the target exists, otherwise plain text
- Never include API keys, credentials, or personal tokens
- Each section in a content page ends with a **See also** block of wiki links
- Complexity notation: O(1), O(n), O(log n), O(n²)

## Source mapping

Raw files live in `Learn/raw/`. The key sections and their wiki page targets:

| Raw file | Wiki page |
|---|---|
| csharp.md, Dynamic CSharp.md | [[CSharp]] |
| Design patterns.md | [[Design-Patterns]] |
| Data Structures.md | [[Data-Structures]] |
| Angular.md, Angular 2.md, AngularJS.md | [[Angular]] |
| React/ | [[React]] |
| Entity Framework*.md | [[Entity-Framework]] |
| Git.md, Git/ | [[Git]] |
| Claude.md | [[Claude-Code]] |
| MCP/MCP.md | [[MCP]] |
| Async Programming.md, Asynchronous Programming*.md | [[Async-Programming]] |
| Dependancy injection.md | [[Dependency-Injection]] |
| Solid.md | [[SOLID-Principles]] |
| Unit Testing/, Jasmine.md, NSubstitute.md | [[Unit Testing]] |
| TypeScript & Javascript/ | [[TypeScript-JavaScript]] |
| Python/ | [[Python]] |
| Aws/ | [[AWS]] |
| Azure/ | [[Azure]] |
| Kubernates/ | [[Kubernetes]] |
| DataBase/, Oracle/ | [[Databases]] |
| Graph Dbs/ | [[Graph Databases]] |
| Micro Services.md | [[Microservices]] |
| OAuth.md | [[Authentication]] |
| Rest.md | [[REST API]] |
| NodeJs/ | [[Node.js]] |
| Kafka/ | [[Kafka]] |
| Maui/ | [[MAUI]] |
