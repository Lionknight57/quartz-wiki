# Wiki Log

Append-only record of ingests, queries, and lint passes.
Format: `## [YYYY-MM-DD] <operation> | <description>`

---

## [2026-06-27] lint | 35 pages checked, 2 genuine issues fixed (rest false positives)

Checked: frontmatter completeness, `[[WikiLink]]` resolution, image paths on disk, `sources` existence in raw, `updated` staleness (>180d).

**Fixed:**
- Broken path-style WikiLinks `[[reading/Medium-Reading|…]]` → `[[Medium-Reading|…]]` in `ai/AI-ML.md` and `tools/Claude-Code.md` (basename links don't resolve with a folder prefix).
- `index.md` stats: "Last ingest" 2026-04-19 → 2026-06-27; raw Medium count 91 → 159.

**False positives (no action — by design):**
- `index.md`, `log.md`, `SCHEMA.md` have no frontmatter block — meta files, intentional.
- "Broken WikiLinks" in `SCHEMA.md`, `log.md`, `overview.md`, `Obsidian.md` — WikiLink-syntax examples (`[[Page Name]]`, `[[image.png]]`), "to be ingested" forward refs (React, MAUI, Kubernetes, …), and prose mentions of the word "WikiLinks".
- `[[MAUI]]` in `CSharp.md` See-also — forward ref to an un-ingested page.
- `sources` "not found in raw" — external URL sources (towardsai.net/…, parazettel.com/…) and vault-root `Clippings/…` / `Asp.net/…` paths that live outside `Learn/raw`. Expected.
- `Obsidian.md` missing image `../assets/x.png` — placeholder in an image-syntax example.

No stale `updated` dates (>180d): none. No orphaned pages: every page on disk is linked from `index.md`.

## [2026-06-27] auto-ingest | Medium digests 2026-06-26 + 2026-06-27

Digests: Medium/2026-06/Medium Daily Digest - 2026-06-26.md, Medium/2026-06/Medium Daily Digest - 2026-06-27.md (both new raw files created)
Articles fetched (Playwright; browser NOT logged into Medium subscription — only free/non-member articles fully readable): 5 of ~12 high-priority
- The Curly Brace — The One C# Question That Filters Out 80% of "Senior" Developers (06-26) [free] → synthesized into CSharp.md
- Hari Prasad Nattuva — Shared Libraries Slowly Destroyed Our .NET Microservices (06-26) [free] → synthesized into CSharp.md
- Serkan Özbey Kurucu — Why I Switched from Swagger UI to Scalar in .NET (06-26) [free] → synthesized into CSharp.md
- The Curly Brace — The Most Expensive 4 Lines of C# I've Ever Seen (06-27) [free] → synthesized into CSharp.md
- Mike Written — I Installed the 144K-Star Karpathy CLAUDE.md File (06-27) [free] → synthesized into Claude-Code.md
Member-only (preview only, backlog noted): Basic Claude Knowledge, 10 Best Free MCP Servers, Stop Wasting Time on Claude Prompts, How to Master Claude (8 Habits), GLM-5.2 (both articles), Smart Enums in C#.

Wiki pages updated:
- languages/CSharp.md (+4 sections: "LINQ deferred execution — the multiple-enumeration trap", "Exception handling — the swallowed exception", "Scalar replaces Swagger UI (.NET 9/10)", "Shared libraries — the microservices anti-pattern"; sources + date → 2026-06-27)
- tools/Claude-Code.md (+1 subsection: one-week field test of the Karpathy four-rule CLAUDE.md, under the existing skills-explosion section; sources + date → 2026-06-27)
- reading/Medium-Reading.md (+2 digest sections; sources + date → 2026-06-27)

New wiki pages created: none
Backlog items added: 24 (5 synthesized/ticked, 7 member-only high, 12 medium)
Note: re-run /medium-auto after logging into Medium in the Playwright browser to capture full content for the 7 member-only high-priority articles.

## [2026-06-19] auto-ingest | Medium digests 2026-06-18 + 2026-06-19

Digests: Medium/2026-06/Medium Daily Digest - 2026-06-18.md, Medium/2026-06/Medium Daily Digest - 2026-06-19.md (both new files created)
Articles fetched (WebFetch — no browser connected, used WebFetch fallback): 6
- Milvus — 7 Best Open-Source Tools for Claude Code Context Management (2026-06-19) [full] → synthesized
- Marco Kotrotsos — Turn Claude Desktop Into an AI Workspace (2026-06-19) [partial] → synthesized
- GaoDalie AI — How To Build Claude Dynamic Workflows… (2026-06-19) [partial, paywalled] → corroborated existing section
- Mubashar Alima — Claude as the Brain, n8n as the Hands (2026-06-19) [full] → synthesized
- pramodaiml — Beyond Spec-Driven Development: The Agentic Engineering Playbook (2026-06-18) [thesis only, paywalled] → synthesized
- aftab001x — You Shouldn't Be Prompting AI Anymore… Design Loops (2026-06-18) [redirect/login loop, not fetched — already covered by Steinberger loop section]

Wiki pages updated:
- tools/Claude-Code.md (+2 sections: "Context management tooling (open-source)" [the 7 Milvus tools], "Claude Desktop as a workspace (code mode)"; +1 corroborating source on Dynamic workflows; sources + date → 2026-06-19)
- ai/AI-Agents.md (+2 sections: "Claude + n8n — brain/hands orchestration", "Agentic engineering — beyond spec-driven development"; sources + date → 2026-06-19)
- reading/Medium-Reading.md (+2 dated sections, +27 backlog items; sources + date → 2026-06-19)

New wiki pages created: none
Backlog items added: 27 (6 High processed/already-covered, rest queued)
Notes: Playwright MCP unavailable and no Chrome browser connected; used WebFetch as fallback. Most Medium articles were accessible; a few member-only stories returned only the thesis/intro. The 06-18 featured highlight (Abhishek Agarwal — "I Copied Anthropic's Exact AI Setup") had no resolvable article URL in the digest body — logged to backlog, not fetched.

---

## [2026-05-30] auto-ingest | Medium digest 2026-05-30

Digest: Medium/2026-05/Medium Daily Digest - 2026-05-30.md
Articles fetched: 6 (High priority)
Wiki pages updated: tools/Claude-Code.md (+3 sections: CLAUDE.md behavioral API 10-pattern framework, top plugins 2026, memory 4-layer architecture + agentic loop), languages/CSharp.md (+ToLookup() section), frameworks/ASP-NET.md (+OpenTelemetry production traps section)
New wiki pages created: none
Backlog items added: 11 (4 High already processed, 7 Medium queued)

---

## [2026-05-29] auto-ingest | Medium digest 2026-05-29 + backlog catchup 2026-05-25→29

Digests: Medium/2026-05/Medium Daily Digest - 2026-05-29.md (new file created; 20-28 already existed)
Articles fetched (Playwright, full content): 5
- The PyCoach — We Built 70+ Claude Skills. These Are The Best (2026-05-26) [already in wiki]
- Kristopher Dunham — Claude Code Has Four Agent Modes. Most Developers Only Use One. (2026-05-26) [already in wiki]
- Anubhav — Inside Claude Code's Leak: 8 Compaction Modes, 3 Memory Tiers, 44 Flags (2026-05-24) [already in wiki]
- Adi Insights — My CLAUDE.md Was Eating 8,000 Tokens. Here's How I Fixed It. (2026-05-22) [already in wiki]
- Leandro Bernardo — I Built Karpathy's LLM Wiki Twice (2026-05-23) [backlog item, not synthesized — md vs code tradeoffs]

Wiki pages updated:
- tools/Claude-Code.md (updated: sources list + date to 2026-05-29; all 2026-05-20→28 content already synthesized by prior run)
- reading/Medium-Reading.md (+20 new backlog items: 2026-05-25→29 Claude Code + .NET + AI items)

New wiki pages created: none
Backlog items added: ~20 (2026-05-25→29 dates)

Standouts from today's digest (2026-05-29):
- "Claude Code Without Subscription: A Proxy That Actually Works" — backlogged HIGH
- "5 DI Anti-patterns in .NET" — backlogged HIGH
- "Ollama + .NET setup guide" — backlogged HIGH

---

## [2026-05-28] auto-ingest | Medium digests 2026-05-25 → 2026-05-28 (batch)

Digests saved: Medium/2026-05/Medium Daily Digest - 2026-05-{25..28}.md (4 files)
Articles fetched (Playwright, full content): 4
- The PyCoach — We Built 70+ Claude Skills. These Are The Best (2026-05-26)
- Kristopher Dunham — Claude Code Has Four Agent Modes. Most Developers Only Use One. (2026-05-26)
- Maulik Patel — dotnet-claude-kit: make Claude Code a .NET 10 expert (2026-05-27)
- Andrew Zhu — Why You Should Completely Avoid Ollama in 2026 (2026-05-27)

Wiki pages updated:
- tools/Claude-Code.md (+2 sections: "4 delegation patterns" with full agent teams/routines detail; "dotnet-claude-kit" brief entry; expanded "Notable skills" table with Context Mode, Sycophancy Skill, Morning Intelligence, etc.)
- languages/CSharp.md (+2 sections: "dotnet-claude-kit" full install guide with Roslyn MCP tools; "Collection interfaces" List<T>/IList<T>/IEnumerable<T>)
- tools/GPU-and-Hardware-for-AI.md (+1 section: "Local Inference Runtimes 2026" with Ollama 2026 status — performance issues, proprietary format history, alternatives)
- reading/Medium-Reading.md (+1 section "From digests 2026-05-25 → 2026-05-28", ~33 new backlog items)

New wiki pages created: none

Standouts:
- dotnet-claude-kit (github.com/codewithmukesh/dotnet-claude-kit) — directly actionable for the DataScraper .NET project
- Claude Code 4 modes: Agent Teams (experimental, env var) and Routines (cloud, 3 triggers) are now fully documented
- Ollama 2026: 30-70% slower than llama.cpp, proprietary format period, cloud service failures — update local inference stack if needed

---

## [2026-05-19] auto-ingest | Medium digests 2026-05-13 → 2026-05-18 (batch)

Digests saved: Medium/2026-05/Medium Daily Digest - 2026-05-{13..18}.md (6 files)
Articles fetched (Playwright, full content): 3
- Daniel Prindii — How I organise Obsidian (2026-05-13)
- Yanli Liu — The 9 Sections Every DESIGN.md Needs (2026-05-15)
- Yanli Liu — Anthropic's Engineer Said Kill Markdown (2026-05-17)
- (partial) IAKH Studio — CLAUDE.md Best Practices Complete Guide for 2026

Wiki pages updated:
- tools/Claude-Code.md (+3 sections: DESIGN.md (9 sections), HTML vs Markdown decision tree, CLAUDE.md best-practices refresh, token-savings quick wins)
- tools/Obsidian.md (+1 section: Vault organisation — Daniel Prindii reference layout)
- reading/Medium-Reading.md (+1 grouped section, ~65 new backlog items across 6 sub-themes: Claude Code/AI Agents, SDD debate, .NET, Local Inference, Dev Tools, Career)

New wiki pages created: none

Standouts for future synthesis:
- Roan Monteiro's two Obsidian + LLM-wiki + Claude Code ecosystem articles (5/14) directly mirror this vault's pattern — worth dedicated section if expanded
- Spec-Driven Development debate (5/13–5/18) has 6 articles across multiple authors — pattern is forming; defer to dedicated wiki page once direction stabilises
- DESIGN.md + Kill-Markdown form a coherent "structured-context engineering" thread with CLAUDE.md
- .NET 11 + Claude .NET production patterns (5/15, 5/18) worth pulling into CSharp wiki page when fetched

---

## [2026-06-05] auto-ingest | Medium digests 2026-05-31 → 2026-06-05 (batch)

Digests saved (6):
- Medium/2026-05/Medium Daily Digest - 2026-05-31.md
- Medium/2026-06/Medium Daily Digest - 2026-06-{01..05}.md  (new 2026-06/ subdir)

Articles fetched: 0 (per-article Playwright synthesis deferred — connectivity intermittent)
Wiki pages updated: reading/Medium-Reading.md (+1 grouped section "From digests 2026-05-31 → 2026-06-05" with ~55 backlog items across 5 sub-themes)
New wiki pages created: none

**Headline shifts:**
- **Claude Opus 4.8 dropped** (06-01) — Han HELOIR YAN's "Anthropic Absorbing Your Harness" reading (1.3K claps)
- **Local-AI tipping point** — Abhishek's "24B Model Broke Anthropic's Pricing" + Cody Sandahl's "Local LLMs go round-for-round with Claude/ChatGPT"
- **TurboQuant on Apple Silicon** (Kashif Mehmood, 523 claps; Shreyansh Jain, 966 claps) — 32B on Mac mainstream
- **Claude Cowork ships 11 official Anthropic plugins** (Tort Mario)
- **Skills replacing MCP** (Daniel Valev: 11 MCP → 6 Skills; Abhishek's pattern continues)
- **Boris's Claude Code workflow** (creator's own setup) surfaced via Ketan Damle

Note: hook reminder claimed "3 pending since 2026-05-30" — actual count was 6 (digest dates 05-31 → 06-05). Stale hook count, no missed digests.

---

## [2026-05-12] auto-ingest | Medium digests 2026-05-06 → 2026-05-12 (batch)

Digests saved: Medium/2026-05/Medium Daily Digest - 2026-05-{06..12}.md (7 files)
Articles fetched: 0 (per-article Playwright synthesis deferred; Playwright connectivity intermittent)
Wiki pages updated: reading/Medium-Reading.md (+1 grouped section with ~55 backlog items across 7 sub-themes)
New wiki pages created: none

Standouts for future synthesis:
- Sumit Pandey — "A Single CLAUDE.md File Went Viral" (91k★ repo)
- Vikas Sah — Agent Hooks (7 patterns) + Autonomous Code Review Bot (Hooks + GH Actions)
- Marco Kotrotsos — Claude Desktop hidden workspace (16 min)
- Han HELOIR YAN — Anthropic confirms Opus regression (16 min, 962 claps)
- Daniel Avila — "save 85% Claude tokens with one setting" (MCP token-cost)
- Roan Brasil Monteiro — Obsidian vs Notion 3.0 as LLM harness (14 min)
- Faisal haque — "Give Claude Permanent Memory" 2026 playbook
- Allohvk — "Adventures in Claude Code land" (30 min, 213 claps)
- Krati Varshney — Microsoft Agent Framework in .NET 10
- Abhishek Agarwal — Anthropic doubled Claude Code limits / Skills replacing MCP servers

---

## [2026-05-05] manual-ingest | Claude OS article (Rohan Mistry)

Article: "I Built Claude OS — A System That Turns Claude into an Execution Engine" — Rohan Mistry (Towards AI, 7 min, 206 claps)
Wiki pages updated: tools/Claude-Code.md (+1 section "Claude OS — modular adoption")
Source: pub.towardsai.net/i-built-claude-os-...-2193d43603b7
Repo: github.com/rohanmistry231/Claude-OS

---

## [2026-05-05] auto-ingest | Medium digest 2026-05-05

Digest: Medium Daily Digest - 2026-05-05 (saved to Medium/2026-05/)
Articles fetched: 0 (per-article Playwright synthesis deferred)
Wiki pages updated: reading/Medium-Reading.md (+13 backlog items, 1 new section)
New wiki pages created: none
Backlog items added: 13 (9 High, 4 Medium)

---

## [2026-05-08] auto-ingest | Medium digests 2026-05-06, 2026-05-07, 2026-05-08

Digests: Medium Daily Digest - 2026-05-06, 2026-05-07, 2026-05-08 (saved to Medium/2026-05/)
Articles fetched: 0 (Playwright MCP not available in this session — per-article synthesis deferred)
Wiki pages updated: reading/Medium-Reading.md (+30 backlog items across 3 new sections)
New wiki pages created: none
Backlog items added: 30 (23 High, 4 Medium, several Low logged in raw digests only)

Notable items flagged for follow-up (relevant to current vault setup):
- Mark Chen "I Found a Full LLM Wiki App. So I Built the Smaller Thing I Actually Needed" (2026-05-08) — directly relevant to LLM-wiki pattern
- Evgeni Rusev "Second Brain with Obsidian + Claude Code" (2026-05-07) — directly relevant to this Work_Vault setup
- Allohvk "Adventures in Claude Code land" (30 min, 2026-05-08) — deep architecture dive
- Karpathy "autoresearch" guide (2026-05-06) — fits the wiki/agent direction
- MCP C# SDK v1.0 + MS Agent Framework on .NET 10 (2026-05-06) — DataScraper-relevant

---

## [2026-05-04] auto-ingest | Medium digest 2026-05-04

Digest: Medium Daily Digest - 2026-05-04 (saved to Medium/2026-05/)
Articles fetched: 0 (per-article Playwright synthesis deferred)
Wiki pages updated: reading/Medium-Reading.md (+10 backlog items, 1 new section)
New wiki pages created: none
Backlog items added: 10 (8 High, 2 Medium)

---

## [2026-05-03] auto-ingest | Medium digest 2026-05-03

Digest: Medium Daily Digest - 2026-05-03
Articles fetched: 0 (per-article Playwright synthesis deferred)
Wiki pages updated: reading/Medium-Reading.md (+8 backlog items, 1 new section)
New wiki pages created: none
Backlog items added: 8 (7 High, 1 Medium)

---

## [2026-05-02] manual-ingest | LeCun AMI Labs / world models article

Article: "Yann LeCun Quit Meta 5 Months Ago to Bet $1.03B Against LLMs" — Chew Loong Nian (Towards AI)
Wiki pages updated: ai/AI-ML.md (+1 section "World models — Yann LeCun's anti-LLM bet (AMI Labs, 2026)")
Source: pub.towardsai.net/yann-lecun-quit-meta-5-months-ago-to-bet-1-03b-against-llms-...-673be1325347

---

## [2026-05-02] auto-ingest | Medium digest 2026-05-02

Digest: Medium Daily Digest - 2026-05-02
Articles fetched: 0 (per-article Playwright synthesis deferred)
Wiki pages updated: reading/Medium-Reading.md (+10 backlog items, 1 new section)
New wiki pages created: none
Backlog items added: 10 (7 High, 3 Medium)

---

## [2026-05-01] auto-ingest | Medium digest 2026-05-01

Digest: Medium Daily Digest - 2026-05-01
Articles fetched: 0 (per-article Playwright synthesis deferred)
Wiki pages updated: reading/Medium-Reading.md (+13 backlog items, 1 new section)
New wiki pages created: none
Backlog items added: 13 (6 High, 7 Medium)

Note: 04-28/04-29/04-30 digest files were already populated before this run; only 05-01 was new.

---

## [2026-04-12] init | Wiki created from OneNote Learn notebook

Created initial wiki structure from ~80 OneNote sections exported to `Learn/raw/`.
Applied LLM-wiki pattern (Karpathy, 2026).

Pages created:
- SCHEMA.md
- index.md
- log.md
- overview.md
- concepts/Data-Structures.md
- concepts/Design-Patterns.md
- concepts/Async-Programming.md
- concepts/Dependency-Injection.md
- concepts/SOLID-Principles.md
- languages/CSharp.md
- tools/Claude-Code.md
- tools/Git.md
- frameworks/Angular.md
- frameworks/Entity-Framework.md

Raw sources: Learn/raw/ (OneNote export via onenote-export.ps1)

---

## [2026-04-25] auto-ingest | Medium digests 2026-04-22 to 2026-04-25

Digests: Medium Daily Digest - 2026-04-22, 2026-04-23, 2026-04-24, 2026-04-25
Articles fetched: 0 (Playwright MCP unavailable — full article synthesis skipped)
Wiki pages updated: reading/Medium-Reading.md
New wiki pages created: none
Backlog items added: 33 (19 High, 14 Medium across Claude Code, .NET, AI Models, PKM/Obsidian)

---

## [2026-04-27] auto-ingest | Medium digest 2026-04-27

Digest: Medium Daily Digest - 2026-04-27 (Apr 26 already ingested previously)
Articles fetched: 0 (Playwright MCP unavailable — full article synthesis skipped)
Wiki pages updated: reading/Medium-Reading.md
New wiki pages created: none
Backlog items added: 11 (3 in Claude Code & AI Agents, 8 in AI Models & Local Inference)
Notes: Apr 23–26 raw digests pre-existing with content; only Apr 27 raw digest written this run.

Notes:
- 2026-04-22 raw file was missing (backlog already contained its articles); raw file created
- Dominant theme: Claude Code efficiency (CLAUDE.md, token limits, rate limits, top 1% patterns)
- Notable high-priority items: "CLAUDE.md Best Practices" 883 claps, "How to Never Hit Claude's Usage Limit Again" 679 claps, "Becoming a top 1% Claude Code user" 141 claps
- New open-source model coverage: Kimi K2.6 (Moonshot AI) beating Claude on coding benchmarks

---

## [2026-04-12] ingest | Git

Ingested 4 raw sources: Git.md, Git/Advanced Git Techniques.md, Git/Advanced Git Tips and Tricks.md, GitLab2.md
Created: tools/Git.md
Covers: object model, core commands, stash, squash, tags, config levels, hooks, submodules, GitLab CI/CD

---

## [2026-04-12] ingest | Angular

Ingested 8 raw sources: Angular 2.md, Angular/Angular Core Module.md, Angular/Angular Best Practices.md, Angular/RxJS and Angular Signals Fundamentals.md, Angular/NgRx.md, Angular/Routing.md, AngularJS.md, Migrating Angjs to Angular2.md
Created: frameworks/Angular.md
Covers: architecture, components, data binding, directives, pipes, services, DI, RxJS, signals, NgRx, routing, CLI, best practices, AngularJS→Angular migration vocabulary

---

## [2026-04-12] ingest | AI-ML (additional sources)

Ingested: AI/AI Essentials.md, AI/AI and Language Models.md, AI/Generative AI Foundations.md, AI/Prompt Engineering.md, AI/Machine Learning.md
Created/Updated: ai/AI-ML.md
Covers: ML fundamentals, neural networks, transformers, LLMs, hallucination, prompt engineering, chatbots

---

## [2026-04-12] ingest | ASP.NET

Ingested: ASP.NET Core raw sources
Created: frameworks/ASP.NET.md
Covers: middleware pipeline, controllers, Razor Pages, minimal APIs, SignalR, configuration

---

## [2026-04-12] ingest | Authentication & Security

Ingested: Auth/Authentication.md, Auth/OAuth.md, Auth/PCI.md
Created: frameworks/Authentication-Security.md
Covers: OAuth2, PKCE, JWT/PASETO, ASP.NET Identity, CORS, OWASP Top 10

---

## [2026-04-12] ingest | AWS & Azure

Ingested: AWS.md, Azure raw sources
Created: cloud/AWS-Azure.md
Covers: core services (EC2, S3, Lambda, App Service, Cosmos DB), IAM, storage, compute comparisons

---

## [2026-04-12] ingest | C# (additional sources)

Ingested: CSharp/Advanced C#.md, CSharp/C#12 Features.md
Updated: languages/CSharp.md
Added: C# 12 features, advanced generics, source generators, record types

---

## [2026-04-12] ingest | CSS & HTML

Ingested: CSS/Flexbox.md, CSS/Grid.md, HTML5.md
Created: languages/CSS-HTML.md
Covers: Flexbox, Grid, responsive design, HTML5 APIs

---

## [2026-04-12] ingest | Testing

Ingested: Testing/xUnit.md, Testing/Moq.md, Testing/Integration Testing.md
Created: concepts/Testing.md
Covers: xUnit, Moq, FluentAssertions, AAA pattern, integration tests, test doubles

---

## [2026-04-12] ingest | Databases SQL

Ingested: SQL raw sources
Created: databases/Databases-SQL.md
Covers: SELECT/JOIN/GROUP BY, indexing, transactions, stored procedures, CTEs

---

## [2026-04-12] ingest | Databases NoSQL

Ingested: MongoDB.md, Redis.md, CosmosDB.md
Created: databases/Databases-NoSQL.md
Covers: MongoDB CRUD/aggregation, Redis data structures, CosmosDB partitioning, vector databases

---

## [2026-04-12] ingest | Entity Framework

Ingested: Entity Framework/Entity Framework.md (image-heavy source)
Created: frameworks/Entity-Framework.md
Covers: EF Core vs EF6, DbContext, data annotations, Fluent API, CRUD, LINQ querying, eager/lazy/explicit loading, change tracking, migrations, repository pattern, async methods, performance tips

---

## [2026-04-12] ingest | Git (additional sources)

Ingested: Git/Git.md, Git/mastering Git.md
Updated: tools/Git.md
Added: reset modes (--soft/--mixed/--hard), git rm --cached, git mv, git restore

---

## [2026-04-12] ingest | Kafka & Spark

Ingested: Kafka/Kafka.md, Kafka with Python.md, Spark/Apache Spark.md
Created: tools/Kafka-Spark.md
Covers: Kafka architecture (brokers/topics/partitions/offsets/consumer groups), delivery guarantees, Kafka Connect, ksqlDB, Python producer/consumer; Spark Driver/Executor model, DataFrame API, Structured Streaming, Delta Lake, Kafka→Spark pipeline

---

## [2026-04-13] ingest | Medium Reading Backlog

Ingested: 85 Medium Daily Digest files (2026-01-01 through 2026-04-13)
Created: reading/Medium-Reading.md
Covers: curated reading list organised into 9 sections — Claude Code & AI Agents, AI Models & Local Inference, Frontend, Backend & .NET, Developer Tools, Security, System Design, Career, Open Source & Self-Hosting

---

## [2026-04-13] ingest | Claude Code (extended from Medium digests)

Updated: tools/Claude-Code.md
Sources: 8 Medium digests (2026-03 to 2026-04)
Added sections: Auto Mode, Channels, Managed Agents, Git Worktree parallel agents, /btw /fork /rewind context hygiene, daily 5-part workflow model, cost reduction strategies, harness engineering (two-agent pattern), notable community skills, Obsidian integration, structured prompting (anti-vibe-coding)

---

## [2026-04-13] ingest | Obsidian

Ingested: Medium Daily Digest - 2026-04-01.md, 2026-04-09.md, 2026-04-11.md, 2026-04-12.md
Created: tools/Obsidian.md
Covers: vault/note/WikiLink concepts, why Obsidian for developers, second brain pattern (capture→process→organise→express), Karpathy's LLM Wiki pattern, Claude Code + Obsidian workflow, self-maintaining knowledge base, useful plugins, PARA organisation, frontmatter conventions

---

## [2026-04-14] ingest | Claude on Mobile

Researched via web search (2025-2026 sources)
Created: tools/Claude-Mobile.md
Covers: iOS/Android apps, voice mode, health data integration, Claude Code on mobile (official + Termux + SSH + /rc remote control), iOS Shortcuts, Android Tasker, Telegram/Discord via Claude Code Channels, Twilio SMS/voice, push notifications (ntfy.sh), claude-in-mobile MCP for device control, building mobile apps with the Claude API, limitations table

---

## [2026-04-13] ingest | AI Agents

Ingested: Medium Daily Digest - 2026-03-22.md, 2026-03-23.md, 2026-04-07.md, 2026-04-08.md, 2026-04-11.md, 2026-04-13.md
Created: ai/AI-Agents.md
Covers: agent architecture (LLM + tools + memory + control loop), Anthropic Managed Agents, harness engineering (two-agent Planner/Executor pattern), framework comparison (LangChain/LangGraph/AutoGen/CrewAI/Hermes), Claude Code subagents, agent memory (Markdown vs vector DB insight), agentic design patterns (routing/parallelisation/reflection/ReAct), self-maintaining knowledge base pattern, Go vs Python for agent backends

---

## [2026-04-16] ingest | Angular Signals

Ingested 1 raw source: Angular/RxJS and Angular Signals Fundamentals.md
Created: frameworks/Angular-Signals.md
Covers: signal() creation and reading ("open the box"), set()/update() modification, computed() derived signals (memoized, lazy, read-only), effect() side effects, signals vs observables comparison table, toSignal/toObservable interop, error handling patterns (pipeline + Result wrapper), shared signals in services, input property workaround, shopping cart example patterns, best practices table, quick reference

---

## [2026-04-16] ingest | RxJS

Ingested 3 raw sources: Angular/RxJS.md, Angular/RxJs in Practice.md, Angular/RxJS and Angular Signals Fundamentals.md
Created: frameworks/RxJS.md
Covers: Observable/Observer/Subject lifecycle, creation functions, all operator categories (transformation/filtering/combination/higher-order mapping), switchMap vs concatMap vs mergeMap vs exhaustMap, error handling, multicasting + shareReplay, Angular Signals vs RxJS comparison, toSignal/toObservable interop, reactive service pattern, marble diagram testing, best practices

---

## [2026-04-16] auto-ingest | Medium digest 2026-04-16

Digest: Medium Daily Digest - 2026-04-16
Articles fetched: 0 (no Playwright available; synthesised from titles/snippets)
Wiki pages updated:
  - tools/Claude-Code.md — added UI redesign note, Observability/monitoring section (OpenTelemetry, 8 metrics), multi-agent app section, personal assistant pattern
  - tools/Obsidian.md — added PKM in the AI age section, Obsidian plugin ecosystem shift section (Bases replacing Dataview)
  - ai/AI-ML.md — added LLM GPU fit tool section (VRAM reference table), Nemotron 3 Nano section
New wiki pages created: none
Backlog items added: 11 (5 Claude Code, 2 Obsidian/PKM, 3 AI Models, 1 productivity)

---

## [2026-04-15] auto-ingest | Medium digest 2026-04-15

Digest: Medium Daily Digest - 2026-04-15
Articles fetched: 0 (no Playwright available; synthesised from titles/snippets)
Wiki pages updated:
  - tools/Claude-Code.md — added CLAUDE.md real-world patterns section (Japanese firm case study, Karpathy's 10K-download file), added Community tooling section (rate limit tracker, 6 underrated repos)
  - languages/CSharp.md — added Static analysis section (Nullable, Roslyn analyzers, SonarAnalyzer, TreatWarningsAsErrors)
  - ai/AI-ML.md — added Local LLMs vs Cloud section, added Gemma 4 models table
  - ai/AI-Agents.md — added MemPalace variant note (Mila Jovovich, 96.6% recall, verbatim text, skeptical review)
New wiki pages created: none
Backlog items added: 11 (5 Claude Code & AI Agents, 5 AI Models, 1 Backend .NET)

---

## [2026-04-14] auto-ingest | Medium digest 2026-04-14

Digest: Medium Daily Digest - 2026-04-14
Articles fetched: 0 (no Playwright available; synthesised from titles/snippets)
Wiki pages updated:
  - tools/Claude-Code.md — added Claude Advisor Tool (new feature), enhanced cost reduction table (49x input tokens, 75% output tokens)
  - ai/AI-Agents.md — added MemPalace memory architecture section
  - tools/Obsidian.md — added "LLM Wiki as a RAG replacement" section (Karpathy pattern 2026 momentum)
  - languages/CSharp.md — added Useful .NET packages section (Mapster, Polly, MediatR, Hangfire, Scrutor), added Performance techniques section (Span<T>, ArrayPool, Interlocked, ValueTask, IAsyncEnumerable)
New wiki pages created: none
Backlog items added: 13 (8 Claude Code & AI Agents, 3 AI Models, 2 Backend .NET)

---

## [2026-04-21] auto-ingest | Medium digest 2026-04-21

Digest: Medium Daily Digest - 2026-04-21
Articles fetched: 0 (no Playwright; synthesised from titles/snippets via Gmail MCP)
Wiki pages updated:
  - tools/MCP.md — added "MCP is Dead" critical perspective (368 claps): MCP is for external integrations, not local tasks; use Claude Code's native tools for local work
  - tools/Claude-Code.md — added Anthropic's internal skills guide: description triggers, action verbs, testing approach, distribution levels
  - tools/Obsidian.md — added Steph Ango (Obsidian CEO) vault structure lessons (flat, minimal, daily notes anchor), 2026 5-plugin rebuild (Bases/Canvas replacing Dataview/Excalidraw), Graphify knowledge graph tool
  - languages/CSharp.md — added LINQ IQueryable vs IEnumerable silent switch (materialisation trap), 5 practical .NET patterns (structured logging, async discipline, specific exceptions, IOptions<T>, nullable types)
New wiki pages created: none
Backlog items added: 10 (2 Claude Code/MCP, 3 Obsidian/PKM, 2 Backend .NET, 2 AI Models, 1 Tools)

---

## [2026-04-20] lint | 30 pages checked, 13 issues found → 12 fixed

Pages checked: 30 | Issues found: 13 | Fixed: 12 | Remaining: 1 (minor)

Fixed:
- ai/AI-ML.md — `[[Databases (NoSQL)]]` → `[[Databases-NoSQL]]` (broken WikiLink)
- concepts/Dependency-Injection.md — `[[Unit Testing]]` → `[[Testing]]` (wrong page name)
- concepts/SOLID-Principles.md — `[[Unit Testing]]` → `[[Testing]]` (wrong page name)
- overview.md — `[[Unit Testing]]` → `[[Testing]]` (wrong page name)
- databases/Databases-NoSQL.md — removed `[[Node-MongoDB]]` (page doesn't exist; marked as to-be-ingested)
- databases/Databases-NoSQL.md — removed Node-MongoDB from related frontmatter
- cloud/Cloud-AWS-Azure.md — removed `[[Kubernetes-Docker]]` (page doesn't exist; marked as to-be-ingested)
- cloud/Cloud-AWS-Azure.md — removed Kubernetes-Docker from related frontmatter
- tools/Kafka-Spark.md — removed Node-MongoDB from related frontmatter

Remaining (low severity, Obsidian fuzzy-matches correctly):
- SCHEMA.md references [[Unit Testing]] — historical mapping note, not a live WikiLink

---

## [2026-04-20] ingest | Async Programming (extended)

Sources: Async Programming.md, Asynchronous Programming in .NET.md (re-read for missing content)
Updated: concepts/Async-Programming.md
Added sections: Task.WhenAll (any exception → AggregateException), Task.WhenAny + timeout pattern, Task.FromResult (precomputed/cached), Continuations (ContinueWith with OnlyOnRanToCompletion/OnlyOnFaulted), Task.Factory.StartNew + Unwrap gotcha, Parallel class (For/ForEach/Invoke/Break/thread-safety), IProgress<T> (UI-thread marshalling pattern), UI thread (Dispatcher.Invoke vs async/await resumption), TaskCompletionSource (bridging callback APIs), IAsyncEnumerable<T> async streams, How async/await works internally (state machine, ValueTask for hot paths)

---

## [2026-04-20] ingest | Anthropic Free Courses (clipping)

Source: Claude_files/Anthropic Is Giving Away 13 Free Courses That Others Charge Thousands For.md
Author: Usman Writes, published 2026-04-13
Updated: tools/Claude-Code.md
Added: "Free Anthropic learning resources" section — 6 developer courses table (Building with Claude API, Intro/Advanced MCP, Claude Code in Action, Agent Skills, Vertex AI), 6 general courses table, GitHub open-source courses (Jupyter notebooks), recommended developer learning path with order

---

## [2026-04-20] auto-ingest | Medium digest 2026-04-20

Digest: Medium Daily Digest - 2026-04-20
Articles fetched: 0 (no Playwright; synthesised from titles/snippets via Gmail MCP)
Wiki pages updated:
  - tools/Claude-Code.md — added MEMORY.md deep dive (types, what to store/not, index truncation limit), Claude Routines (new Anthropic automation feature), 4-file system for reliable agent coding (SPEC/PLAN/PROGRESS/REVIEW)
  - languages/CSharp.md — added DI via source generation (.NET) — compile-time registration, no reflection, Jab/StrongInject packages
New wiki pages created: none
Backlog items added: 7 (5 Claude Code, 1 Obsidian/PKM, 1 Backend .NET)

---

## [2026-04-19] ingest | AI Fundamentals (developer practical guide)

Source: AI/AI Fundementals.md (Pluralsight course transcript)
Updated: ai/AI-ML.md
Added sections:
  - AI as pattern matching — the developer mental model (deterministic vs probabilistic, what AI is/isn't doing, why failures are predictable)
  - Training vs inference — key operational split with cost/timing table
  - Tokens and context windows — token definition, context window size table, production implications
  - Embeddings — vectors, semantic relationships, KING-QUEEN example, vector databases
  - Temperature and sampling parameters — temperature/top-k/top-p explained with practical guidance per use case
  - Confidence scores ≠ accuracy — threshold decisions belong to you, calibration requirement
  - Error compounding in AI pipelines — 0.9^3 = 73% math, agent design implications
  - Preprocessing requirements — strict format requirements, data representativeness > correctness
  - Explainability problem — black box issue, regulated industry constraints
  - Feedback loops — recommendation echo chambers, credit model bias reinforcement
  - Production realities — what works/doesn't, monitoring (drift/decay/P95-P99), deployment (shadow/canary), cost management (cache/batch), AI-specific security (prompt injection/model extraction/PII leakage)

---

## [2026-04-19] ingest | Python

Ingested 1 raw source: Python/Core Python Getting Started.md (Pluralsight course notes)
Created: languages/Python.md
Covers: setup/REPL, syntax (significant whitespace, docstrings, integer division //), imports (import/from/as, __name__ guard, shebang), types/objects (everything is object, is vs ==, LEGB scope), built-in collections (tuples — immutable/unpacking/swap; strings — join/partition/f-strings; range/enumerate; lists — slicing/sort/comprehension; dicts — iteration/update; sets — algebra operations), control flow (if/elif/while/for/ternary), functions (defaults/args/kwargs/lambda), exception handling (try/except/else/finally/raise), comprehensions (list/dict/set/generator), classes (inheritance/super/__init__/__str__/__repr__/class vars), standard library highlights (os/sys/json/datetime/re/pathlib)

---

## [2026-04-19] ingest | Blockchain

Ingested 3 raw sources: BlockChain/BlockChain.md, BlockChain/Principles and Practices.md, BlockChain/summary.md (Pluralsight course notes + AI-generated overview)
Created: concepts/Blockchain.md
Covers: core properties (decentralization, immutability, transparency), block structure (hash/number/prev-hash/transactions), why tampering is infeasible (chain recalculation cost), cryptographic foundations (hashing — SHA family, HMAC, digital signatures — RSA flow Alice→Bob), Proof of Work mechanics, consensus mechanisms (PoW/PoS/DPoS + Ethereum's 99.95% energy reduction), public vs private blockchain comparison, smart contracts (Solidity/Rust), use cases (DeFi, supply chain, identity, AML/KYC, voting, healthcare, IP)

---

## [2026-04-19] ingest | REST API

Ingested 1 raw source: Rest.md (ASP.NET Web API course, 2017)
Created: concepts/REST-API.md
Covers: REST definition + 6 architectural constraints, Uniform Interface + HATEOAS, URI design (nouns not verbs), HTTP methods with correct status codes, GET/POST/PUT/PATCH/DELETE patterns in ASP.NET, JsonPatchDocument for PATCH, sorting (?sort=field/-field), filtering (query params to WHERE), paging (Skip/Take + X-Pagination header + prev/next links), data shaping (field selection via ExpandoObject + reflection, association include), ETags caching (If-None-Match/If-Match/304/412), CacheCow.Server, 3 versioning strategies (URI/content-negotiation/custom-header), JSON content negotiation config

---

## [2026-04-19] ingest | TypeScript & JavaScript

Ingested 3 raw sources: Typescript & Javascript/TypeScript.md, Typescript & Javascript/JavaScript 6.md, Typescript & Javascript/Express Server.md
Created: languages/TypeScript-JavaScript.md
Covers: TypeScript overview + benefits, tsconfig.json configuration, type system (primitives/union/enum/tuple/Partial), type inference and control flow narrowing, function annotations (optional/default/arrow), interfaces (structural typing, duck typing), classes (accessors, static, readonly, inheritance), modules (export/import/alias), type declaration files (@types), ES6+ (let/const/spread/destructuring/template literals), Express server setup + error middleware + REST handler pattern, Sequelize ORM (CRUD + eager loading joins)

---

## [2026-04-19] ingest | Data Structures

Source: Data Structures.md (OneNote export — image-heavy, text extracted)
Updated: concepts/Data-Structures.md
Added sections:
  - Hash table properties (stability, uniformity, security, fill/growth factor)
  - AVL Tree — self-balancing BST, balance factor, always O(log n)
  - Sets — Union/Intersection/Difference/Symmetric Difference; HashSet<T> C# methods
  - Sorting algorithms — Bubble/Insertion/Selection/Merge/Quick Sort comparison table
  - String Search — Boyer-Moore-Horspool overview and when to use vs KMP
  - Big O Notation — notation table, what it measures, best vs worst case
  - Collection Concurrency — lock/Monitor/ReaderWriterLockSlim/ConcurrentXxx comparison
  - Complexity Reference — extended with AVL Tree and HashSet<T> rows, Notes column

---

## [2026-04-19] auto-ingest | Medium digest 2026-04-19

Digest: Medium Daily Digest - 2026-04-19
Articles fetched: 0 (no Playwright; synthesised from titles/snippets via Gmail MCP)
Wiki pages updated:
  - ai/AI-ML.md — added Opus 4.7 release (adaptive thinking, manual budgets removed, critical take), Qwen 30B on Raspberry Pi milestone
  - tools/Obsidian.md — added image management section (attachment folder, naming, wikilink vs markdown), Claude Code + Obsidian second brain pattern
  - languages/CSharp.md — added 5 API design decisions for longevity (interfaces, parameter objects, sealed, Result<T>), FrozenDictionary for read-optimised lookup tables
New wiki pages created: none
Backlog items added: 9 (2 AI Models, 2 Obsidian/PKM, 3 Backend .NET, 1 Tools, 1 AI Models)

---

## [2026-04-18] auto-ingest | Medium digests 2026-04-17 + 2026-04-18

Digests: Medium Daily Digest - 2026-04-17, Medium Daily Digest - 2026-04-18
Articles fetched: 0 (no Playwright; synthesised from titles/snippets via Gmail MCP)
Wiki pages updated:
  - tools/Claude-Code.md — added: .claude/ folder structure, /ultraplan command, /team-onboarding, context nerf fix, feature specs for agents, three generations of agent orchestration
  - languages/CSharp.md — added: async/await heap allocation fix (.NET 11), 5 advanced C# features (structs/nullability/records), Clean Architecture + CQRS pattern
New wiki pages created: none
Backlog items added: 13 (6 Claude Code, 2 AI Models, 4 Backend .NET, 1 tools)

---

## [2026-04-17] auto-ingest | Medium digest 2026-04-17

Digest: Medium Daily Digest - 2026-04-17
Articles fetched: 0 (no Playwright available; synthesised from titles/snippets)
Wiki pages updated: none (no new content synthesized — processed in 2026-04-18 batch)
New wiki pages created: none
Backlog items added: 10 (6 Claude Code & AI Agents, 2 AI Models, 1 Backend .NET, 1 API Design)

---

## [2026-04-17] ingest | MCP — Model Context Protocol (course transcript)

Source: MCP/MCP.md — Pluralsight course transcript (building MCP servers, C#/.NET + TypeScript)
Created: tools/MCP.md
Covers:
  - Architecture (client-server, JSON-RPC 2.0, one client per server)
  - Three primitives: Resources (+ templates), Tools, Prompts — with lifecycle and content types
  - Transport: stdio vs streamable HTTP (stateful vs stateless), logging to stderr
  - Building servers: C#/.NET scaffolding, ASP.NET Core HTTP hosting, TypeScript/Zod
  - Tool design & context engineering: user-friendly params, error handling, token cost (PDF→plain text→vector search)
  - MCP Inspector: testing, npm scripts wrapper
  - Client config: VS Code mcp.json, Claude Desktop claude_desktop_config.json
  - Distribution: NuGet, NPM, Docker, standalone executable
  - Remote hosting: Azure Functions (stateless), Cloudflare Workers, platform comparison table
  - Security: OAuth 2.1 full flow (PRM document, DCR vs static, JWT Bearer, OBO delegation), CORS
  - Client compatibility matrix (Claude Desktop vs VS Code vs Inspector)

---

## [2026-04-17] ingest | Advanced Claude Code (course transcript)

Source: Claude_files/Advanced Claude Code.md — Karoly Nyisztor course transcript
Updated: tools/Claude-Code.md
Added:
  - MCP: scopes (local/project/user), .mcp.json, GitHub token setup, Figma desktop vs remote, token limits, design-to-code workflow
  - Subagents: custom definition (frontmatter + system prompt), /agents, async subagents (Ctrl+B, /tasks), built-in subagents (Explore/Plan/general-purpose), troubleshooting guide
  - Git Worktrees: full command set (add/list/remove/prune), Claude Desktop auto-worktrees, .worktreeinclude
  - Enterprise/Team: auth methods (Console/Pro/Bedrock/Vertex/Foundry), 3-category permission model, settings hierarchy, permission rules JSON, org-wide CLAUDE.md with @ imports, .claude/rules/ scoped rules, monitoring (Console analytics, OpenTelemetry)
  - Skills: SKILL.md anatomy with frontmatter fields, progressive disclosure pattern, script bundling, hooks in skill frontmatter
  - Hooks: expanded event table, hook creation via /hooks, JSON stdin, CLAUDE_PROJECT_DIR, exit code 2 blocking, security practices, parallel execution, hooks in subagent frontmatter

---

## [2026-04-17] ingest | C# 15 Union Types (clipping)

Source: Clippings/C 15's Newest Feature Is More Dangerous Than It Looks.md
Author: Gulam Ali H., published 2026-04-14
Updated: languages/CSharp.md
Added: Union types section (C# 15) — what they are, when to use, when not to, overhype analysis; updated version history table to include C# 13–15

---

## [2026-04-22] ingest | GPU and Hardware for AI

Ingested raw sources: Learn/raw/AI/COURSE Getting Started with Tensorflow 2.0.md, Learn/raw/Tensor Flow.md, Learn/raw/AI/COURSE Getting Started with Tensorflow 2.0_distilled.md, Medium Daily Digest - 2026-04-16.md, Medium Daily Digest - 2026-04-21.md
Created: tools/GPU-and-Hardware-for-AI.md
Covers: VRAM estimation, quantisation, hardware landscape (NVIDIA, Apple, Raspberry Pi), TensorFlow hardware tools.

---
## [2026-04-22] auto-ingest | Medium digest 2026-04-22

Digest: Medium Daily Digest - 2026-04-22
Articles fetched: 0 (Playwright not available in session — full content fetch skipped)
Wiki pages updated:
  - tools/Claude-Code.md (+2 sections: Claude Hardware Buddy, Claude Code for Creating Diagrams)
  - ai/AI-ML.md (+1 section: Agent harness components with 3-camp framework and 7-component breakdown)
New wiki pages created: none
Backlog items added: 11 (7 High + 4 Medium)
Note: full article fetches not completed — run `/medium <url>` manually for the 7 High priority articles

## [2026-04-26] auto-ingest | Medium digest 2026-04-26

Digest: Medium Daily Digest - 2026-04-26
Articles fetched: 0 (lightweight pass — no Playwright fetches)
Wiki pages updated: reading/Medium-Reading.md (+10 backlog items across Claude Code/AI Agents, AI Models, Backend & .NET, Security)
New wiki pages created: none
Backlog items added: 10

---

## [2026-04-28] auto-ingest | Medium digest 2026-04-28

Digest: Medium Daily Digest - 2026-04-28 (Theo Stowell — Two Claude x Obsidian Tweaks)
Articles fetched: 2 of 10 High — Theo Stowell (free mirror at parazettel.com) initially; then user logged into Medium in the Playwright browser, enabling fetch of Chew Loong Nian's Opus 4.7 vs 4.6 piece.
Wiki pages updated:
  - tools/Claude-Code.md (+1 expanded "Obsidian integration" subsection: Obsidian CLI setup pattern + internetVin terminal plugin feature table)
  - ai/AI-ML.md (rewrote "Claude Opus 4.7" section: full 7-benchmark comparison table, default-effort swap finding with code example, per-workload use-case guidance, kept Dunlop critique as counterpoint)
  - reading/Medium-Reading.md (+13 backlog items under new "From digest 2026-04-28" section; 2 marked synthesised)
New wiki pages created: none
Backlog items added: 13 (10 High + 3 Medium)
Note: SessionStart hook flagged 5 digests pending (2026-04-23 to 2026-04-27) but those were already processed in earlier sessions; only 2026-04-28 was actually new.
Note: Medium membership is now logged into the Playwright browser session — future /medium-auto runs can fetch member-only stories directly without paywall workarounds.

---

## [2026-04-30] auto-ingest | Medium digest 2026-04-30

Digest: Medium Daily Digest - 2026-04-30 (Shashwat — Antigravity Collapse)
Articles fetched: 0 (lightweight pass — no Playwright fetches)
Wiki pages updated:
  - tools/Claude-Code.md (+2 sections: DESIGN.md as fifth file extending SPEC/PLAN/PROGRESS/REVIEW; 30-minute session decay command toolkit from Kotrotsos)
  - reading/Medium-Reading.md (+12 backlog items: 7 Claude Code/AI Agents, 4 AI Models, 1 Backend .NET; sources frontmatter updated)
New wiki pages created: none
Backlog items added: 12
Notable themes: Antigravity (Google's AI IDE) collapse coverage, Yanli Liu's RAG-vs-LLMWiki-vs-Gbrain memory architecture comparison (702 claps), DESIGN.md pattern as upstream of SPEC.md, Kimi K2.6 vs Opus 4.7 coding-model war.

Note: SessionStart hook flagged 7 digests pending (2026-04-23 to 2026-04-29) but those raw files already exist in Work_Vault from prior sessions. The check-medium.js script reads from the legacy `TravidMap/Learn/raw/Medium` path which is missing those files — the flag is a false positive. Only 2026-04-30 was genuinely new. Hook script needs path fix to point at `Work_Vault/Learn/raw/Medium`.

---

## [2026-04-29] auto-ingest | Medium digest 2026-04-29

Digest: Medium Daily Digest - 2026-04-29 (Sathish Raju — OpenClaw → Hermes Agent)
Articles fetched: 0 (lightweight pass — no Playwright fetches; high-volume backlog already covers similar topics from prior digests)
Wiki pages updated:
  - reading/Medium-Reading.md (+22 backlog items: 4/28 + 4/29 articles; sources frontmatter updated)
New wiki pages created: none
Backlog items added: 22 (across Claude Code/AI Agents cluster — Obsidian-Claude integrations, LLM Wiki evaluations, spec-driven dev frameworks, MCP roundups, agentic-coding state-of-the-field)
Notable themes this digest: heavy LLM-Wiki + Obsidian + Claude convergence (Shuyi Wang's 27-min LLM Wiki evaluation, Kanika BK's 24×7 Obsidian-Claude agent build, Minervee's "AI Employee" pattern, Anubhav's 6-month Claude Code tuning report). GLM-5.1 dethrones Opus on SWE-Bench. Spec-driven dev framework comparison covers 15 systems from Spec-Kit (90K stars) down.
Note: SessionStart hook flagged 6 digests pending (2026-04-23 to 2026-04-28) but raw files for those dates already existed from prior sessions; only 2026-04-29 was genuinely new.

---

## [2026-05-20] auto-ingest | Medium digests 2026-05-19 + 2026-05-20

Digests: Medium Daily Digest - 2026-05-19, Medium Daily Digest - 2026-05-20
Articles fetched: 4 (Abe Jaber middleware, Gulam Ali channels, Shashwat CLAUDE.md rules, Addepalle Claude Code 20%)
Wiki pages updated:
  - frameworks/ASP-NET.md (+5-zone middleware model with 4 incident postmortems, 5-point audit checklist; sources + updated bumped)
  - concepts/Async-Programming.md (+producer-consumer from primitives section; SemaphoreSlim queue, Channel<T> comparison table, direct-handoff pattern)
  - tools/Claude-Code.md (+"80% most tutorials skip" section: /btw, /sandbox, Chrome ext, writer/reviewer split, kitchen-sink antipattern; +CLAUDE.md 10-rules teardown grouped into execution/memory/Karpathy)
  - reading/Medium-Reading.md (+18 backlog items across 5-19 and 5-20; 4 marked synthesized)
New wiki pages created: none
Backlog items added: 18
Notable themes: .NET 10 middleware ordering with real incident data, primitive-level concurrency under polished APIs, Claude Code workflow features past the first-week basics (/btw, /sandbox, Chrome ext), CLAUDE.md as memory not docs, Gemma 4 + consumer GPU local-AI thread continues.

---

## [2026-05-22] auto-ingest | Medium digests 2026-05-21 + 2026-05-22

Digests: Medium Daily Digest - 2026-05-21, Medium Daily Digest - 2026-05-22
Articles fetched: 4 (Abe Jaber AGENTS.md, Abe Jaber HttpClient leak, Adi Insights CLAUDE.md tokens, Shashwat 10 settings)
Wiki pages updated:
  - frameworks/ASP-NET.md (+HttpClient socket-leak section with IHttpClientFactory traps; +AGENTS.md three-layer hierarchy for vertical-slice .NET 10; sources + updated bumped)
  - tools/Claude-Code.md (+CLAUDE.md token budget section with degradation curve, path-scoped rules, audit/prune guidance; +default-settings-to-audit section covering 2026 nerf workarounds — thinking budget, MCP overhead, model routing, filesystem hooks)
  - reading/Medium-Reading.md (+25 backlog items across both digests; 4 marked synthesized)
New wiki pages created: none
Backlog items added: 25
Notable themes: continued Claude Code workflow tuning (CLAUDE.md token economy, default-config reversal), .NET 10 production traps (HttpClient + DI lifetime, vertical-slice AGENTS.md hierarchy), Spec Kit going GA, Qwen3.6-27B as Claude-coding-tier free model.

---

## [2026-05-24] auto-ingest | Medium digests 2026-05-23 + 2026-05-24

Digests: Medium Daily Digest - 2026-05-23, Medium Daily Digest - 2026-05-24
Articles fetched: 4 (Kotrotsos HTML-for-docs, Abe Jaber JsonSerializerOptions, Shashwat Auto-Obsidian, Anubhav Claude Code leak)
Wiki pages updated:
  - tools/Obsidian.md (+HTML vs Markdown decision rule for AI-generated artifacts with CLAUDE.md instruction; +Shashwat's 4-layer auto-vault architecture mapped onto the current vault — capture/storage/synthesis/output loops)
  - frameworks/ASP-NET.md (+JsonSerializerOptions caching section with CA1869 enforcement, DI-friendly variant, p99-smoothing measurable signal)
  - tools/Claude-Code.md (+"Under the hood — April 2026 source-map leak" section: 8 compaction layers in firing order, 3 memory tiers with most-specific-CLAUDE.md-wins precedence, hidden flags + Tengu/Capybara/Fennec/Numbat codenames)
  - reading/Medium-Reading.md (+24 backlog items across both digests; 4 marked synthesized)
New wiki pages created: none
Backlog items added: 24
Notable themes: HTML-as-rendered-output for human-facing AI artifacts (directly triggered by user question in the same session about HTML in Obsidian vaults), .NET 10 perf trap series continues from Abe Jaber, full architectural reveal of Claude Code harness via accidental sourcemap leak, second LLM-Wiki-vs-RAG framing piece (Jahangir, after Yanli Liu's earlier RAG/LLM-Wiki/Gbrain comparison).

## [2026-06-07] auto-ingest | Medium digest 2026-06-06

Digest: Medium Daily Digest - 2026-06-06
Articles fetched: 2 (High priority)
Wiki pages updated: tools/Obsidian.md (+1 section: "CLAUDE.md as the cure for context rot (COG framework, Mar–Apr 2026)")
New wiki pages created: none
Backlog items added: 15 (2 marked read, 13 pending)

## [2026-06-07] lint | 31 pages checked, 6 issue groups found — all fixed

Issues found and resolved:
- Broken `[[WikiLinks]]` using spaced/dotted titles instead of hyphenated filenames — fixed ~40 occurrences across index.md, overview.md, SCHEMA.md, CSharp.md, Angular.md, Data-Structures.md, Design-Patterns.md, Dependency-Injection.md, SOLID-Principles.md, Async-Programming.md, ASP-NET.md, Entity-Framework.md, Testing.md, Git.md, REST-API.md, MCP.md, Cloud-AWS-Azure references, Data Structures references
- Orphaned page `languages/CSS-HTML.md` (existed on disk, not in index, not linked anywhere) — added to index.md Languages section
- Broken image paths: 9 references in Design-Patterns.md, Claude-Code.md, Git.md used `../raw/...` (resolves to non-existent `wiki/raw/`) instead of `../../raw/...` (resolves to `Learn/raw/`) — fixed all 9
- Stale page count in index.md Stats block (said 29, actually 31) — corrected
- (False positive retracted: `updated: YYYY-MM-DD` in SCHEMA.md is a template example inside a code block, not real frontmatter — no fix needed)

No stale `updated:` dates (>180 days) and no missing `sources:` files found.

## [2026-06-07] auto-ingest | Medium digest 2026-06-07

Digest: Medium Daily Digest - 2026-06-07
Articles fetched: 4 (High priority)
Wiki pages updated: tools/Claude-Code.md (+3 sections: Dynamic workflows, claude-code-guide, Code Review), tools/Obsidian.md (+1 section: Dataview + Claude dashboard build)
New wiki pages created: none
Backlog items added: 9 (4 marked read/synthesized, 5 pending)

## [2026-06-08] auto-ingest | Medium digest 2026-06-08

Digest: Medium Daily Digest - 2026-06-08
Articles fetched: 6 (High priority)
Wiki pages updated: tools/Claude-Code.md (+4 sections: claude-code-setup config-audit plugin, Blender MCP, Workflow & ultracode deeper mechanics, Claude Cowork plugins), tools/Obsidian.md (+1 section: Web Clipper + nightly ingest pattern), ai/AI-Agents.md (+1 section: Agno + ClaudeAgent HTTP-addressable specialist)
New wiki pages created: none
Backlog items added: 12 (6 marked read/synthesized, 6 pending)

## [2026-06-17] auto-ingest | Medium digests 2026-06-09 → 2026-06-17 (batch)

Digests saved (9): Medium/2026-06/Medium Daily Digest - 2026-06-{09..17}.md

Articles fetched: 9 (1 headline/#1 article per digest — scope reduced from full High-priority fetch to keep the catch-up tractable; remaining High+Medium items logged to backlog for manual `/medium <url>` follow-up)

Wiki pages updated:
- tools/Claude-Code.md (+6 sections: Competitors (OpenCode), cmux — open-source terminal for parallel sessions, A concrete 4-prompt version (Josh Dubowsky), Skills ecosystem explosion (mid-2026), Open-source AI assistant tools by GitHub stars (June 2026 roundup), Enterprise adoption friction — the Microsoft case)
- ai/AI-Agents.md (+3 sections: Designing agent loops (the Steinberger model), Graphify — codebase knowledge graph for agents, AI agent tools by GitHub stars (June 2026 roundup))
- ai/AI-ML.md (+1 section: Dedicated local-inference hardware — NVIDIA RTX Spark)

New wiki pages created: none

Backlog items added: 118 across 9 new "From digest" sections in reading/Medium-Reading.md (9 marked read/synthesized, 109 pending)

Partial/failed fetches (content behind paywall or friend-link gating): 06-10 cmux (paywalled past intro), 06-11 RTX Spark (specs only), 06-12 agentic loop (paywalled mid-list), 06-15 Graphify (friend-link still gated), 06-17 Microsoft article (partial) — suggest `/medium <url>` for a full manual re-fetch if deeper detail is needed.

Secrets finding (flagged, not actioned): `Learn/raw/CLAUDE.md` (git-tracked, committed in 73610d6) contains plaintext API keys — 2 Moonshot/Kimi keys, 1 Moonshot token assigned to `ANTHROPIC_AUTH_TOKEN`, 1 Anthropic key, 1 OpenRouter key. Surfaced to user for remediation decision (rotate/scrub history/gitignore); no key values reproduced here.

## [2026-06-22] auto-ingest | Medium digests 2026-06-20 → 2026-06-22 (batch, no Playwright)

Digests saved (3): Medium/2026-06/Medium Daily Digest - 2026-06-{20,21,22}.md

Articles fetched: 0 — Playwright MCP was disconnected this session, so no full-article content fetch or wiki synthesis was possible. Only headline/summary text and the article list from each digest email were captured.

Wiki pages updated: none

New wiki pages created: none

Backlog items added: 41 across 3 new "From digest" sections in reading/Medium-Reading.md, all unread/pending (0 synthesized) — each section flagged with a note that Playwright was unavailable

Note: re-run `/medium-auto` once the Playwright MCP connection is restored to fetch and synthesize the headline articles (Anthropic Skills/Cowork-plugins ship on 06-20, AI-code-gen restriction trend on 06-21, Pi vs. Claude Code on 06-22) — or use `/medium <url>` for individual articles in the meantime.

## [2026-06-24] auto-ingest | Medium digests 2026-06-23 → 2026-06-24

Digests saved (2): Medium/2026-06/Medium Daily Digest - 2026-06-{23,24}.md

Articles fetched: 4 in full (CodeGraph, Stop Recreating .NET Solutions, 5 Hidden .NET Features, 14 Open Source AI Agent Tools) + 6 partial (member-only — intro/preview only: Claude HUD, RTK 63k, 29k Claude plugins, Obsidian multi-agent vault, 30 agentic concepts, 7 GitHub repos)

Wiki pages updated:
- tools/Claude-Code.md (+2 sections: CodeGraph — queryable knowledge graph over MCP; Claude HUD — status-bar plugin)
- languages/CSharp.md (+2 sections: 5 underused .NET features (TimeProvider, Keyed DI, Process, Rate Limiting, BackgroundService); Stop copy-pasting solutions — dotnet new templates)
- ai/AI-Agents.md (+1 section: AI agent tools roundup by category — expanded NocoBase survey)
- tools/Obsidian.md (+1 section: Vault as a shared brain — multi-agent consensus, Claude + Codex + Gemini)

New wiki pages created: none

Backlog items added: 28 across 2 new "From digest" sections in reading/Medium-Reading.md (5 marked synthesized, 23 pending)

Partial/paywalled fetches (member-only past preview): 06-24 "7 GitHub Repos That Make Claude Code 10x Better", "Finally, a Useful Claude Code Status Bar Enhancement Plugin!" (Claude HUD), "30 Core Agentic Engineering Concepts"; 06-23 "63k Stars Rust Tool" (RTK), "29,369 Claude Plugins in 1 Place", "Your Vault as a Shared Brain" — concepts captured from free previews; suggest `/medium <url>` for full detail. (Browser is not logged into Medium this session — log in via the Playwright window to fetch member-only articles in full.)

## [2026-06-25] auto-ingest | Medium digest 2026-06-25

Digest saved: Medium/2026-06/Medium Daily Digest - 2026-06-25.md

Articles fetched: 1 in full (Why Senior .NET Developers Never Use string.Split) + 5 partial (member-only — preview only: 4-rule CLAUDE.md, Claude Computer Use, Claude Code Chief of Staff, 60–95% fewer tokens/Headroom, 13 Claude Code commands)

Wiki pages updated:
- languages/CSharp.md (enhanced Performance techniques: Span vs Memory async-boundary deep dive, ArrayPool clearArray for secrets, GC generational/LOH primer, when-to-use judgment)
- tools/Claude-Code.md (+2 sections: Computer Use escalation order (connector → browser → computer use); HQ/ "Chief of Staff" personal-workspace variant; +1 row: Headroom context compressor in the context-management table)

New wiki pages created: none

Backlog items added: 12 in 1 new "From digest" section in reading/Medium-Reading.md (1 marked synthesized, 11 pending)

Partial/paywalled fetches (member-only past preview): "This 4-Rule Markdown File Has 91,000 GitHub Stars" (the 4 rules themselves are paywalled), "Claude Can Now Click, Type, and Navigate Your Computer Apps", "I Turned Claude Code Into My Chief of Staff", "60–95% Fewer Tokens with Claude Code and Codex", "13 Claude Code Commands" — concepts captured from free previews; browser still not logged into Medium this session, so member-only bodies were unavailable. Suggest logging in via the Playwright window (or `/medium <url>`) for full detail.

## [2026-06-27] lint | 31 pages checked, 11 broken links fixed (8 benign remain)

Ran full lint (frontmatter completeness, wikilink resolution, source existence, staleness).

Fixed (casing — links now resolve to real kebab-case pages):
- [[Claude Code]] → [[Claude-Code]] (AI-ML, Git, overview ×2, SCHEMA)
- [[Entity Framework]] → [[Entity-Framework]] (ASP-NET, Async-Programming, CSharp ×2, Databases-SQL, Design-Patterns, overview, SCHEMA)
- [[TypeScript]] → [[TypeScript-JavaScript]] (Angular, overview, SCHEMA)
- [[Claude Code#MCP — Model Context Protocol]] → [[Claude-Code#...]] (MCP ×2)
- [[Tools-Misc]] (no such page) → [[Claude-Code]] (Medium-Reading: related frontmatter + See also)

Result: 28/31 content pages fully clean. Remaining flags are intentional/benign:
- CSharp [[MAUI]] — deliberate "to be ingested" roadmap marker (tracked in index.md)
- CSharp source "Clippings/C 15's Newest Feature…md" — exists at vault root Clippings/ (outside Learn/raw lint scope); filename uses a curly apostrophe
- Claude-Code `[[wikilinks]]` and Obsidian [[WikiLinks]]/[[Note Name]]/![[image.png]] — wikilink *syntax examples* in prose/code, not real links

No staleness issues (>180d): all pages current. No missing frontmatter.

Systemic note (not auto-fixed): many pages' `related:` frontmatter use display-form names (e.g. `Claude Code`, `ASP.NET`) rather than page slugs (`Claude-Code`, `ASP-NET`). Harmless for rendering but inconsistent — candidate for a future normalization pass.
