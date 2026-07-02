---
title: AI Agents
tags: [ai, agents, agentic, llm, langchain, langgraph, autogen, crewai, managed-agents, orchestration]
related: [AI-ML, Claude-Code, Obsidian]
sources: [Medium/2026-03/Medium Daily Digest - 2026-03-22.md, Medium/2026-03/Medium Daily Digest - 2026-03-23.md, Medium/2026-04/Medium Daily Digest - 2026-04-07.md, Medium/2026-04/Medium Daily Digest - 2026-04-08.md, Medium/2026-04/Medium Daily Digest - 2026-04-11.md, Medium/2026-04/Medium Daily Digest - 2026-04-13.md, Medium/2026-04/Medium Daily Digest - 2026-04-14.md, Medium/2026-04/Medium Daily Digest - 2026-04-15.md, Medium/2026-06/Medium Daily Digest - 2026-06-08.md, Medium/2026-06/Medium Daily Digest - 2026-06-12.md, Medium/2026-06/Medium Daily Digest - 2026-06-15.md, Medium/2026-06/Medium Daily Digest - 2026-06-16.md, Medium/2026-06/Medium Daily Digest - 2026-06-18.md, Medium/2026-06/Medium Daily Digest - 2026-06-19.md, Medium/2026-06/Medium Daily Digest - 2026-06-23.md]
updated: 2026-06-24
---

# AI Agents

An **AI agent** is a system that uses an LLM as its reasoning engine, combined with tools, memory, and a control loop, to autonomously complete multi-step tasks. Unlike a chatbot (one response per prompt), an agent **plans → acts → observes → iterates** until a goal is achieved.

```
User Goal
    ↓
Agent (LLM)
    ↓ decides which tool to call
Tool Execution (search, code runner, API, file system)
    ↓ returns result
Agent (LLM) — observes, decides next step
    ↓
... repeat until done
    ↓
Final response to user
```

---

## Core components of an agent

| Component | What it does |
|---|---|
| **LLM (Reasoning engine)** | Decides what to do next based on current context |
| **Tools** | Functions the agent can call (web search, code exec, file I/O, APIs) |
| **Memory** | Short-term (context window) + long-term (files, vector DB, or just Markdown) |
| **Control loop** | The "reason → act → observe" cycle that keeps running until done |
| **System prompt / instructions** | Defines the agent's role, constraints, and available tools |

---

## Anthropic Managed Agents

Anthropic's structured framework for building multi-agent systems within Claude Code.

**Key idea:** Declaratively define agents, their roles, and how they hand off tasks — instead of manually wiring subagents with raw prompts.

```
Managed Agent definition:
  - name: research-agent
  - role: gather information from web and files
  - tools: [web_search, read_file]
  - handoff_to: [summary-agent]

  - name: summary-agent
  - role: synthesize research into structured output
  - tools: [write_file]
```

**Why it matters:**
- Promises **10× faster** development of complex agentic systems
- Handles inter-agent communication and context passing automatically
- Reduces the "context window filling up mid-feature" problem
- Native Claude Code integration — no external framework needed

> "Anthropic Just Dropped Managed Agents (10x Faster AI Development)" — Ai studio  
> "Anthropic Launches Claude Managed Agents (That Make Agentic AI Workflows Real)" — Joe Njenga

---

## Harness Engineering — the two-agent pattern

Both Anthropic and OpenAI independently arrived at the same architecture for complex features:

```
Agent A — Planner / Orchestrator
  → breaks feature into discrete tasks
  → sends one task at a time to Agent B
  → holds the full feature list + progress

Agent B — Executor
  → receives a single, self-contained task
  → implements it with full focused context
  → returns structured result to Agent A

Agent A
  → integrates result
  → marks task complete, moves to next
```

**Why this solves the context problem:**
- Agent B only ever sees the context for its current task — the window never fills up
- Agent A holds state across the whole feature, but doesn't do any implementation
- Results are structured hand-offs, not raw conversation dumps
- OpenAI's experiment: **zero manually-written code** in a complete feature using this pattern

**The "Zero Context Loss" principle:** Keep each agent's context minimal and purpose-specific. The moment you need to explain the whole codebase to an agent, you've broken the pattern.

> "Anthropic's Harness Engineering: Two Agents, One Feature List, Zero Context…" — Rick Hightower  
> "OpenAI's Harness Engineering Experiment: Zero Manually-Written Code" — Rick Hightower

---

## Agent frameworks comparison

As of early 2026, the field has consolidated around a few key approaches:

| Framework | Approach | Key strength |
|---|---|---|
| **LangChain / LangGraph** | Graph-based workflow, nodes + edges | Fine-grained control over agent flow |
| **AutoGen** (Microsoft) | Multi-agent conversation threads | Natural multi-agent dialogue |
| **CrewAI** | Role-based crews of agents | Easy definition of agent personas/teams |
| **ByteDance framework** | (emerging) | High-throughput pipeline execution |
| **Hermes** | Competing with OpenClaw | Built-in setup guide, full autonomy |
| **LangChain Deep Agents** | 2026 release — long-horizon tasks | Handles extended multi-step chains |

**"5 Agent Frameworks. One Pattern Won."** (Yanli Liu, 2026): The convergent winning pattern across all frameworks is:
1. A planner agent that creates a task list
2. Worker agents that execute individual tasks
3. A memory/state store that persists results between steps
4. An evaluator that checks if the goal was met

The specific framework matters less than getting this structure right.

---

## Claude Code subagents

Claude Code's built-in mechanism for parallel agent execution.

**How it works:**
```
Main Agent (orchestrator)
  ├── Spawns Subagent A in git worktree /feature-a
  ├── Spawns Subagent B in git worktree /feature-b
  └── Spawns Subagent C in git worktree /tests
  
Each subagent:
  - Gets a self-contained prompt with full context for its task
  - Works on isolated repo copy (no conflicts)
  - Returns structured result to main agent

Main agent:
  - Waits for all subagents
  - Merges results / resolves conflicts
  - Reports to user
```

**Key patterns from the digests:**
- The "multi-brain" pattern: one Claude instance per concern (architecture, implementation, testing)
- Second Brain middleware reduces token usage by **5×** via open-source caching layer
- `/skillify` — hidden internal skill that installs/manages other skills for subagents

> "Claude Code Subagents and Main-Agent Coordination: A Complete Guide" — Rick Hightower  
> "The Claude Code Feature I Ignored for Months Was the Most Important One" — Colby McHenry  
> "Claude Code's Second Brain Cuts Token Usage by 5x" — Agent Native

---

## Agent memory architecture

A key 2026 insight: **you don't need a vector database for agent memory**.

**"The Markdown File That Beat a $50M Vector Database"** (Micheal Lanham):
- For most agent use cases, a structured Markdown file outperforms a vector DB
- No setup complexity, no embedding costs, human-readable, version-controllable
- The agent reads the relevant sections using grep/search before acting
- Works especially well when the memory has clear structure (headers, tables, lists)

**Memory patterns:**

| Pattern | When to use |
|---|---|
| **In-context** | Small, short-lived state — just put it in the prompt |
| **Markdown file** | Persistent memory, human-readable, structured knowledge |
| **Vector DB** | Large unstructured corpora where semantic search is needed |
| **External API** | When memory must be shared across multiple agents/sessions |

**CLAUDE.md and AGENTS.md:** Persistent instruction files the agent reads every session. Prevents "dumb AI code" — the agent always has context about constraints, conventions, and project state.

---

## Agentic coding frameworks (Claude Code ecosystem)

The Claude Code skill ecosystem has several frameworks for structured agentic development:

| Framework | What it does |
|---|---|
| **Superpowers** | Extends Claude Code with advanced capabilities (widely cited as major quality boost) |
| **BMAD** | Business/product spec → implementation tasks workflow |
| **SpecKit** | Structured specification framework for AI-guided development |
| **GSD** (Get Stuff Done) | Rapid execution framework for well-defined tasks |
| **AutoResearch** | Karpathy-style agent that synthesises multiple sources into a wiki page |

---

## Tools for agents

**CLIs commonly used with AI agents in 2026:**

| Tool | Purpose |
|---|---|
| **ripgrep (rg)** | Fast search across large codebases |
| **jq** | JSON processing in agent pipelines |
| **gh** | GitHub API — PRs, issues, code review automation |
| **curl / httpie** | HTTP requests to external services |
| **sqlite-utils** | Lightweight persistent storage for agent state |
| **llm (Simon Willison)** | CLI for calling LLMs from shell scripts |
| **mcp-cli** | Test and debug MCP servers locally |

**MCP servers for agents:**
- Databases (MongoDB, PostgreSQL) — direct DB access
- GitHub — full repo control
- Google Calendar / Gmail — personal automation
- DevOps tools (Jira, Linear, Slack) — team workflow integration

> "10 Must-have CLIs for your AI Agents in 2026" — unicodeveloper  
> "Top 10 MCP Servers To Speed Up DevOps Processes" — Neel Shah

---

## Language choice for agent backends

**Go vs Python for AI agent development** (2026 discussion):

| Python | Go |
|---|---|
| LangChain, LangGraph, AutoGen all native | No native agent frameworks |
| Huge ML/AI library ecosystem | Better performance and concurrency |
| Slower, higher memory use | Faster startup, lower resource cost |
| Best for prototyping and experimentation | Best for production agent services at scale |
| Default for most LLM API SDKs | Manual HTTP calls to LLM APIs |

**Verdict:** Python wins for experimentation and framework access; Go wins when you need to run many parallel agent pipelines in production. Most teams prototype in Python, then migrate performance-critical paths to Go.

---

## Agentic design patterns

### Routing
Classify the input, route to the appropriate specialist agent:
```
User request → Classifier agent → Route to:
  ├── Code agent (coding questions)
  ├── Research agent (factual questions)  
  └── Writing agent (content tasks)
```

### Parallelisation
Split independent subtasks, run in parallel, aggregate results:
```
Feature request → Planner → [SubagentA, SubagentB, SubagentC] → Aggregator → Output
```

### Reflection / self-critique
Agent generates output → second agent (or same agent) critiques it → revises:
```
Draft → Critique agent → "Missing error handling" → Revise → Final
```

### Tool use loop
Standard ReAct (Reason + Act) loop:
```
Think: "I need to know the current date"
Act: call_tool("get_date")
Observe: "2026-04-13"
Think: "Now I can answer the question"
Answer: ...
```

### Designing agent loops (the Steinberger model)

Peter Steinberger's June 2026 post — "You shouldn't be prompting coding agents anymore. You should be designing loops that prompt your agents" — cleared 2M views and reframed how to think about repetitive agent work:

> A loop is a small program that prompts the agent for you, reads what it produced, decides whether it is done, and if not, prompts it again. You stop being the thing inside the loop typing prompts. You write the loop, and the model becomes a subroutine.

**The shift:** instead of a human re-prompting an agent at each step, a wrapper script owns the loop — call agent → read output → check a completion condition → re-prompt if not done. The agent becomes a callable subroutine inside *your* control flow rather than the other way around. The on-ramp is small: the simplest useful version is one line of bash; a more durable version is a single slash command that encodes the same check-and-reprompt logic.

This is the same shape as the ReAct loop above, but scoped one level up — instead of the agent looping over tool calls within a task, an external script loops over entire agent invocations across a task.

> Source: Marco Kotrotsos — "WTF Is a Agentic Coding Loop? And How to Build One." (Autocomplete: Real World AI, 2026-06-09, 12 min read), quoting @steipete

---

## Self-maintaining knowledge base

The most practical agentic system pattern visible in the Medium digests — an agent that automatically updates a knowledge base from new inputs:

```
New content (article, email, note)
    ↓ Triggered by hook or scheduled job
    ↓ Agent reads new content
    ↓ Identifies relevant wiki pages
    ↓ Extracts key facts/insights
    ↓ Appends or updates wiki pages
    ↓ Commits to git
```

This is exactly what the `/medium` and `/wiki` skills implement. The agent is Claude Code; the knowledge base is `Learn/wiki/`; the trigger is the user running `/medium <url>`.

> "I used Karpathy's LLM Wiki to build a knowledge base that maintains itself" — Balu Kosuri  
> "Andrej Karpathy Stopped Using AI to Write Code. He's Using It to Build a Second Brain Instead" — Nikhil  
> See also: [[Obsidian]] — the vault structure this pattern runs in

---

## MemPalace — viral memory architecture

An open-source AI memory system that gained **22K GitHub stars in 48 hours** (April 2026). Addresses what Kristopher Dunham calls "the same fatal flaw in every AI agent you've ever built" — no persistent memory across sessions.

**Core idea:** Instead of a vector database, MemPalace uses a structured in-memory graph that the agent can read and write. The "palace" metaphor — each memory has a *location* in a mental map, making retrieval spatial rather than semantic.

**Why it resonated:**
- Zero infrastructure — no vector DB, no embeddings, no embedding costs
- Agent-native — designed for LLM agents, not humans
- Hierarchical — memories have parent/child relationships, not just flat key-value
- Snapshottable — entire memory state can be serialised to JSON and restored

**Pattern:**
```
Session start → load MemPalace snapshot
    ↓
Agent works, calling remember(key, value) and recall(key)
    ↓
Session end → save MemPalace snapshot to file
    ↓
Next session → restore from snapshot
```

**Contrast with other patterns:**
| Approach | Best for | Downside |
|---|---|---|
| MemPalace | Agent memory, session persistence | Limited to structured knowledge |
| Markdown file | Human-readable, git-friendly | No query API |
| Vector DB | Large unstructured corpora | Infrastructure overhead |
| In-context only | Short sessions | Lost on session end |

> "MemPalace: The Viral AI Memory System That Got 22K Stars in 48 Hours (An Honest…)" — Kristopher Dunham

**April 2026 variant — "MemPalace By Mila Jovovich":** A separate implementation claiming 96.6% recall using raw verbatim text storage (no embeddings, no API calls). Mandar Karhade's skeptical analysis suggests results may be benchmark-specific and the "too good to be true" framing is warranted — worth watching but verify independently before adopting.

> "MemPalace By Mila Jovovich: 96.6% Recall With Zero API Calls (Too Good To Be True?)" — Mandar Karhade, MD. PhD. (543 claps)

---

## Hermes — OpenClaw competitor

**Hermes** emerged in April 2026 as a new full-autonomy agent framework directly competing with OpenClaw:

- Full autonomous operation — no per-step approvals
- Built-in setup guide and configuration system
- Competes on the same "agent that can operate a computer" niche as OpenClaw
- Community-driven with active GitHub presence

> "Hermes: The Only AI Agent That Truly Competes With OpenClaw" — Marco Rodrigues

---

## Agno + ClaudeAgent — running Claude Code as an HTTP-addressable specialist

**Agno** is a multi-agent framework and production runtime: define agents/teams/workflows in Python, expose them through **AgentOS** — a FastAPI service with session persistence, tracing, and a control-plane UI at `os.agno.com`. It's deliberately framework-agnostic; its Claude Agent SDK integration registers Claude Code as a first-class HTTP endpoint *alongside* native Agno orchestration.

**The pitch:** combine Claude Code's filesystem depth with Agno's URLs/persistence/governance so anyone — or an automated pipeline — triggers a coding task with a plain HTTP request, no terminal required.

### Three doors on one AgentOS server

| Door | Answered by | Typical job |
|---|---|---|
| `/agents/...` | `ClaudeAgent` (Claude Code) | Work the codebase — read/edit files, run tests/shell, apply `CLAUDE.md` rules |
| `/workflows/...` | Agno `Workflow` | Research reports with a **fixed recipe** (same steps every run) |
| `/teams/...` | Agno `Team` | Research questions where a **leader** picks the right helper (finance vs. web vs. news) |

**They do not share a chat thread** — a `ClaudeAgent` cannot be plugged inside a `Workflow` or `Team` (Agno documents this as unsupported). Pick the right door per task.

### Defining a `ClaudeAgent` — one per "job you want a separate button for"

Each registered agent gets its own settings and its own URL (`POST /agents/{id}/runs`):

| Setting | Purpose |
|---|---|
| `name` / `id` / `description` | Display name, optional explicit endpoint id (defaults to slug of name), UI/API metadata |
| `cwd` | Which project folder Claude Code works in |
| `allowed_tools` / `disallowed_tools` | Hard whitelist — e.g. a Security Auditor gets `Read`-only, a Code Developer gets `Read+Edit+Bash` |
| `max_budget_usd` / `max_turns` | Cost and step caps per request |
| `system_prompt` | What this specialist is supposed to do |
| `permission_mode` | `default` (writes prompt for approval) · `acceptEdits` (also auto-approves edits + filesystem commands like `mkdir`/`rm`/`mv`) · `plan` (read-only exploration) · `bypassPermissions` (isolated containers only) |
| `mcp_servers` | Register custom Agent-SDK MCP tools |
| `db` | Agno DB handle (`SqliteDb`, `PostgresDb`...) — persists runs so reusing `session_id` continues the same thread |

**Example pattern:** a `Security Auditor` (`Read`-only, audits + searches the web) and a `Code Developer` (edits files, runs tests) registered as two separate endpoints on the same server — each gets its own URL that a coworker, script, or pipeline can call directly.

> Source: Alex Yevseyevich — "Integrating Claude Code with the Agno Multi-Agent Framework" (2026-05-24, 56 min deep-dive)

---

## Graphify — codebase knowledge graph for agents

Addresses a specific agent failure mode: an agent asked "where is the authentication flow implemented?" starts reading random files, burns thousands of tokens, misses dependencies, hallucinates architecture, and forgets context after every session. **Graphify** (`safishamsi/graphify`) is an open-source skill that converts a project into a queryable knowledge graph once, so the agent queries the structure instead of repeatedly re-scanning raw files from scratch each session.

**Pattern it represents:** structured, persistent project knowledge as a *graph* rather than flat Markdown or a vector DB — closer to the Markdown-file memory pattern described above in spirit (no embeddings, no infra) but specifically shaped for codebase structure (files, symbols, dependencies) rather than general facts.

> Source: Vijayasekhar Deepak (2026-06-15, 110 claps)

---

## AI agent tools by GitHub stars (June 2026 roundup)

NocoBase's survey of the most-starred open-source AI assistant/agent tools, split by use case:

| Tool | Stars | Category | Best for |
|---|---|---|---|
| Ruflo | 58.6K | Multi-agent orchestration | Coordinating specialist agents (dev/test/security/docs) on top of Claude Code/Codex |
| CopilotKit | 34.4K | Embedded agent UI | Building copilots/generative UI into an existing product |
| NanoClaw | 29.8K | Lightweight assistant | Agents in containers, chat-platform integration (WhatsApp/Telegram/Slack/Discord/Gmail) |
| NocoBase | 22.7K | No-code enterprise platform | Natural-language data model/workflow/permission generation |
| Leon | 17.3K | Personal assistant | Privacy-focused, local or self-hosted |
| DeepChat | 6K | Desktop assistant | Multi-model (OpenAI/Gemini/Anthropic/DeepSeek/Ollama), MCP + Skills + ACP |
| GitHub Copilot for Xcode | 6.1K | IDE-native assistant | Swift/iOS/macOS development |
| Everywhere | 6K | Screen-aware desktop assistant | Perceives current app/screen context without screenshots |

**Read on this list:** the spread across categories (orchestration, embedding, chat-platform, no-code, personal, IDE-native) shows agent tooling has fragmented by deployment surface rather than converging on one shape — consistent with the "5 frameworks, one pattern" finding above, where the underlying planner/worker/memory/evaluator pattern repeats but the packaging varies by where the agent needs to live.

> Source: NocoBase — "Top 8 Open Source AI Assistant Tools by GitHub Stars" (2026-06-16, 14 min read)

---

## AI agent tools roundup — by category (June 2026, expanded)

A follow-up NocoBase survey drawn from GitHub's official `ai-agent` topic, profiling each by *positioning* (what kind of tool) and *suitable scenario* (individual / team / enterprise). Notable that several top entries are **knowledge-management or multi-tool workbenches**, not agent frameworks per se:

| Tool | Stars | Category | Best for |
|---|---|---|---|
| Cherry Studio | 47.2K | AI productivity studio | One place for 300+ assistants + unified access to many frontier LLMs |
| CowAgent | 45.2K | Assistant + agent harness | Long-running personal/IM assistant (WeChat etc.), task planning, Skills |
| SiYuan | 44.4K | Knowledge infrastructure (TS/Go) | Privacy-first local PKM — block refs, backlinks, embedded SQL; a *knowledge layer* for agents |
| Nanobot | 44.1K | Lightweight personal agent | Own-your-agent core with WebUI, tools, memory, MCP, model routing |
| CopilotKit | 34.7K | Frontend framework for agents | Embedding copilots / generative UI (React, Angular, mobile, Slack) |
| OpenClaude | 28.6K | AI coding agent CLI | Terminal-first coding; cloud + local providers (Ollama, Gemini, Codex), MCP, slash commands |
| AionUi | 28.1K | Local AI coworking desktop | Managing many CLI agents (Claude Code, Codex, Hermes, OpenCode, Gemini CLI) in one workbench |
| Google Workspace CLI | 27K | Productivity bridge | Gives agents CLI+JSON access to Drive/Gmail/Calendar/Sheets/Docs (mind OAuth scopes) |
| Agent-Reach | 26.3K | External info access | CLI that lets agents read Twitter/Reddit/YouTube/GitHub for research |
| OpenCLI | 24.1K | Web→CLI wrapper | Turns websites/browser sessions/Electron apps into agent-callable commands |
| NocoBase | 22.7K | No-code enterprise platform | NL-generated data models/workflows/permissions; configurable "AI Employees" |

**Read on this list:** the field is fragmenting by *deployment surface* — coding CLIs, desktop workbenches, web-wrappers, knowledge bases, no-code platforms — rather than converging. Tools like SiYuan and Cherry Studio reinforce that **durable knowledge/context is becoming a first-class agent concern**, echoing the memory-architecture and knowledge-graph debates above ([[Claude-Code]] CodeGraph, Graphify).

> Source: NocoBase — "14 Open Source AI Agent Tools with the Most GitHub Stars" (2026-06-23, 14 min, 54 claps)

---

## Claude + n8n — the "brain and hands" orchestration pattern

A practical, low-infra pattern for business automation: **Claude is the persistent reasoning layer (the brain); n8n is the reliable body that acts (the hands).** n8n handles the mechanical, repetitive work — polling inboxes, writing to Google Sheets, firing webhooks, sending Slack messages — while Claude handles anything that needs judgment: classifying ambiguous data, catching nuance, generating human-quality content.

**Data flow:** `raw data → n8n → Claude (with system prompt) → n8n routes the output to its destination`. n8n calls Claude directly via the Anthropic API; the loop closes when n8n takes Claude's structured output and routes it.

| Workflow | What it does | Result |
|---|---|---|
| Weekly digest | Pulls Google Analytics + Notion + CSV every Sunday 9pm → Claude writes a 300-word summary | ~$0.04/run |
| Inbox triage | Classifies Gmail as urgent/informational/spam, drafts replies | 10 emails: 40 min → 6 min |
| Content brief generator | Topic in → Claude returns 600-word brief (audience, SEO keywords, structure) | Output 2 → 5 pieces/week |
| Competitor snapshot | Monthly automated competitor scan | (in refinement) |

**Infra:** self-hosted n8n on a $6/mo DigitalOcean droplet; Claude API $12–18/mo. **Key insight:** *"You can't automate something you haven't described precisely"* — being forced to define each workflow explicitly improved overall task quality. This is the same brain/hands split as [[Claude-Code]]'s tool-use loop, scaled out to cross-app business processes rather than a single coding session.

> Source: Mubashar Alima — "Claude as the Brain, n8n as the Hands: How I Built a Self-Managing Business Workflow" (2026-06-19, 5 min, 9 claps)

---

## Agentic engineering — beyond spec-driven development

The emerging framing for 2026: agentic engineering is the step *past* "vibe coding" toward a professional methodology — **the human defines the intent, the agents execute, the human verifies the output.** The shift is away from hand-writing detailed specs and toward designing a repeatable loop of intent → agent execution → human verification, where the engineer's leverage moves from writing code to specifying outcomes precisely and reviewing results. This dovetails with the [[#Designing agent loops (the Steinberger model)|loop-design]] pattern (you write the loop, the model becomes the subroutine) and the harness-engineering split above.

> Source: pramodaiml — "Beyond Spec-Driven Development: The Agentic Engineering Playbook That's Replacing How We Build" (2026-06-18, 20 min, 269 claps)

---

## See also
- [[Claude-Code]] — Claude Code's Managed Agents and subagent system
- [[AI-ML]] — underlying LLM concepts that power agents
- [[Obsidian]] — vault integration for agent memory and knowledge bases
