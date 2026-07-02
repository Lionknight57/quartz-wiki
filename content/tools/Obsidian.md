---
title: Obsidian
tags: [obsidian, knowledge-management, second-brain, pkm, llm-wiki, notes]
related: [Claude-Code, AI-ML, AI-Agents]
sources: [Medium/2026-04/Medium Daily Digest - 2026-04-01.md, Medium/2026-04/Medium Daily Digest - 2026-04-09.md, Medium/2026-04/Medium Daily Digest - 2026-04-11.md, Medium/2026-04/Medium Daily Digest - 2026-04-12.md, Medium/2026-04/Medium Daily Digest - 2026-04-14.md, Medium/2026-04/Medium Daily Digest - 2026-04-16.md, Medium/2026-04/Medium Daily Digest - 2026-04-19.md, Medium/2026-04/Medium Daily Digest - 2026-04-21.md, Medium/2026-05/Medium Daily Digest - 2026-05-13.md, Medium/2026-05/Medium Daily Digest - 2026-05-14.md, Medium/2026-05/Medium Daily Digest - 2026-05-17.md, Medium/2026-05/Medium Daily Digest - 2026-05-18.md, Medium/2026-05/Medium Daily Digest - 2026-05-23.md, Medium/2026-05/Medium Daily Digest - 2026-05-24.md, Medium/2026-06/Medium Daily Digest - 2026-06-06.md, Medium/2026-06/Medium Daily Digest - 2026-06-07.md, Medium/2026-06/Medium Daily Digest - 2026-06-08.md, Medium/2026-06/Medium Daily Digest - 2026-06-23.md]
updated: 2026-06-24
---

# Obsidian

Obsidian is a local-first Markdown note-taking app that treats your notes as a **personal knowledge graph**. Notes link to each other with `[[WikiLinks]]`, forming a network you can navigate visually. Everything lives in a plain-text vault on your machine — no cloud lock-in.

---

## Core concepts

| Concept | Description |
|---|---|
| **Vault** | A folder of Markdown files — the entire knowledge base |
| **Note** | A single `.md` file |
| **WikiLink** | `[[Note Name]]` — creates a link between notes |
| **Backlinks** | Automatically shows which notes link *to* the current note |
| **Graph view** | Visual map of all notes and their connections |
| **Tag** | `#tag` inline or `tags:` frontmatter — categorises notes |
| **Frontmatter** | YAML block at the top of a note — structured metadata |
| **Canvas** | Infinite whiteboard for spatial note layout |
| **Dataview** | Plugin — query your vault like a database using `LIST` / `TABLE` |
| **Templater** | Plugin — dynamic templates with variables and scripts |

---

## Why Obsidian for developers

- **Plain Markdown** — version-controllable, portable, no proprietary format
- **Local-first** — works offline, your data stays with you
- **Git-friendly** — commit your vault to a repo for history and sync
- **Programmable** — plugins, Dataview queries, Templater scripts, community ecosystem
- **Claude Code integration** — Claude Code reads and writes `.md` files natively; your vault is just a directory
- **LLM-wiki pattern** — run an LLM to maintain structured wiki pages from raw notes (this project's pattern)

---

## Second brain pattern

The "second brain" concept (Tiago Forte / Karpathy) treats your notes system as an extension of your memory:

```
Raw input (articles, ideas, meeting notes)
    ↓
Capture — quick note, no friction
    ↓
Process — connect to existing notes, tag, summarise
    ↓
Organise — folder or tags or both (PARA or flat + links)
    ↓
Express — synthesis, wiki pages, blog posts, decisions
```

**Key principle:** You're not just storing information — you're building a web of connected insights that gets more useful the larger it grows.

---

## Karpathy's LLM Wiki pattern

Andrej Karpathy's approach: use an LLM to maintain a structured wiki from raw source notes. Directly used in this project (`Learn/wiki/`).

```
raw/ (immutable sources — course notes, highlights, article exports)
    ↓ LLM reads and synthesises
wiki/ (LLM-maintained pages — clean, linked, structured)
    ↓
SCHEMA.md (conventions the LLM follows)
index.md  (catalog of all pages)
log.md    (append-only history)
```

**Why it works:**
- Raw notes are messy — OCR'd screenshots, course transcripts, bullet dumps
- LLM converts them into scannable reference pages with tables, code blocks, cross-links
- The wiki page is the *output* the LLM optimises for; raw notes are never edited
- Running `/wiki sync` detects uningested raw files and offers to process them

> "I used Karpathy's LLM Wiki to build a knowledge base that maintains itself" — Balu Kosuri  
> "Andrej Karpathy Stopped Using AI to Write Code. He's Using It to Build a Second Brain Instead" — Nikhil

---

## LLM Wiki as a RAG replacement

Mandar Karhade's April 2026 deep-dive ("Andrej Karpathy Killed RAG. Or Did He?") frames the LLM wiki pattern as a direct alternative to Retrieval Augmented Generation:

| RAG | LLM Wiki |
|---|---|
| Chunk documents → embeddings → vector DB | Raw notes → LLM synthesis → structured Markdown |
| Query → semantic search → inject chunks | Query → read wiki page directly |
| Complex infrastructure | A folder of `.md` files |
| Stale if not re-indexed | Always current (LLM updates wiki when new notes arrive) |
| Good for large unstructured corpora | Good for personal/team knowledge where an LLM can synthesise |

**The key insight:** For personal knowledge bases, you don't need semantic search. The LLM already *knows* what's in the wiki (it wrote it). A well-structured wiki page is better than retrieved chunks because it's **synthesised**, not raw.

**Why 5,000 GitHub stars in 48 hours:** The gist was just a pattern description — no code. The stars represented "yes, I've been doing RAG wrong for my use case."

**Ecosystem momentum (April 2026):**
- Reza Rezvani published a full Claude Code skill: "LLM Wiki Skill: Build a Second Brain With Claude Code and Obsidian" (16 min, full implementation)
- evoailabs: "Why Andrej Karpathy's 'LLM Wiki' is the Future of Personal Knowledge"
- Multiple articles describing implementation variants in Claude Code, Cursor, and standalone scripts

> "Andrej Karpathy Killed RAG. Or Did He? The LLM Wiki Pattern" — Mandar Karhade (580 claps)  
> "LLM Wiki Skill: Build a Second Brain With Claude Code and Obsidian" — Reza Rezvani

---

## Claude Code inside Obsidian

Claude Code works directly with Obsidian vaults because a vault is just a directory of Markdown files.

**Patterns:**
- Open Claude Code in your vault root — it can read all notes as context
- Use Claude Code to create/update wiki pages from raw notes (`/wiki ingest`)
- Use Claude Code memory files (`~/.claude/projects/<path>/memory/`) to persist session context across conversations
- Write skills that interact with your vault (e.g., the `/medium` skill appends to `reading/Medium-Reading.md`)
- Use `CLAUDE.md` at vault root to give Claude persistent instructions about your knowledge structure

**Claude Code + Obsidian workflow:**
```
1. Take raw notes in Obsidian (quick capture, no friction)
2. Run /wiki sync in Claude Code → identifies uningested notes
3. Claude Code reads raw notes, writes structured wiki pages
4. Wiki pages auto-link with [[WikiLinks]] — navigable in Obsidian graph
5. Memory files persist Claude's understanding of your project context
```

> "The TL;DR of Claude Code Inside Obsidian" — Theo Stowell  
> "Using Obsidian to Build a Software Startup" — Theo Stowell

---

## Self-maintaining knowledge base

The next evolution: a knowledge base that updates itself automatically.

```
New article / email / note
    ↓ Claude Code hook or scheduled agent
    ↓ reads new content
    ↓ identifies relevant wiki pages
    ↓ appends or updates them
    ↓ commits changes to git
```

This is what the `/medium` skill does: each new Medium digest → raw file saved → wiki reading backlog updated → all automatic on `/medium <url>`.

> "I used Karpathy's LLM Wiki to build a knowledge base that maintains itself" — Balu Kosuri

---

## Obsidian CEO's vault structure (Steph Ango)

From Theo Stowell's "Lessons From Breaking Down Obsidian's CEO's Personal Vault Template" (Apr 2026):

Steph Ango (Obsidian CEO) published his personal vault template. Key lessons:

- **Flat structure over folders** — Ango uses minimal nesting; links and tags create structure organically rather than folder hierarchies
- **Properties/frontmatter over inline** — all metadata in frontmatter, keeping note bodies clean and focused on content
- **Daily notes as the anchor** — every day starts with a daily note that links out; retrospective and planning happen there
- **No complex queries** — avoids heavy Dataview queries; prefers simple link-following over SQL-like data extraction
- **Templates are minimal** — just enough frontmatter and a title; not over-templated
- **The lesson:** vault structure should reduce friction, not impose discipline. If you spend more time maintaining your system than using it, simplify.

> "The most interesting thing about Ango's vault isn't what's in it — it's how little ceremony it has." — Theo Stowell

---

## 2026 Obsidian plugin stack (5-plugin rebuild)

From Len's "I Rebuilt My Obsidian Workflow With 5 New Plugins (2026 Setup)" (206 claps):

A 2026 minimal stack replacing heavier legacy setups (old Dataview-heavy configurations):

| Plugin | Replaces | Why |
|---|---|---|
| **Bases** | Dataview | Native Obsidian database views — no plugin fragility, built into core |
| **Templater** | (keep) | Still the best template engine with JS scripting |
| **Obsidian Git** | (keep) | Auto-commit backbone |
| **Tasks** | (keep) | Due dates and recurrence, now more stable |
| **Canvas** | Excalidraw (for structure) | Native infinite canvas for mind-mapping and project planning |

**What was removed:** Dataview (replaced by Bases), heavy community theme plugins, folder-note plugins.

> See [[Obsidian#Obsidian CEO's vault structure (Steph Ango)]] for the philosophy behind minimal setups.

---

## Graphify — folder to knowledge graph

From Ana Bildea, PhD's "How to Use Graphify: Turn Any Folder Into a Knowledge Graph" (413 claps):

Graphify converts any file folder into a visual knowledge graph — connections between files based on shared terms, tags, and links. Relevant to Obsidian vaults:

- Works on any directory, not just Obsidian vaults
- Creates semantic connections between notes (even without explicit `[[WikiLinks]]`)
- Visualises clusters of related notes that you didn't know were connected
- Useful for discovering structure in large unorganised note collections before building proper vault hierarchy

**Use case for this vault:** run Graphify on `Learn/raw/` to discover which raw source files cluster together — helps identify good candidates for wiki ingest groupings.

---

## Useful plugins

| Plugin | Purpose | Status (2026) |
|---|---|---|
| **Bases** | Native database views (replaces Dataview) | New — preferred |
| **Dataview** | SQL-like queries over note frontmatter | Being replaced by Bases |
| **Templater** | Dynamic templates with JS scripting | Current |
| **Calendar** | Daily notes calendar view | Current |
| **Obsidian Git** | Auto-commit vault to git on a timer | Current |
| **Advanced Tables** | Markdown table editor | Current |
| **Canvas** | Native infinite canvas | Replacing Excalidraw for structure |
| **Excalidraw** | Embedded whiteboard diagrams in notes | Current (detail diagrams) |
| **Smart Connections** | AI-powered semantic link suggestions | Current |
| **Tasks** | Task management with due dates, recurrence | Current |

---

## PARA organisation

A popular folder structure for Obsidian vaults:

| Folder | Contains |
|---|---|
| **Projects** | Active work with a defined outcome and deadline |
| **Areas** | Ongoing responsibilities without an end date (career, health, finances) |
| **Resources** | Reference material organised by topic (this wiki) |
| **Archive** | Completed projects and outdated material |

Alternatively: **flat + links** — no folders, rely on tags and WikiLinks to create structure organically.

---

## Frontmatter conventions (this vault)

```yaml
---
title: <Page title>
tags: [tag1, tag2]
related: [LinkedPage1, LinkedPage2]
sources: [raw/file1.md, raw/file2.md]
updated: YYYY-MM-DD
---
```

---

## Image management in Obsidian

From Ahmad Sopyan's "How to Manage you're Images in Obsidian Like a Pro":

**Default behaviour:** Obsidian dumps pasted images into the vault root — quickly becomes a mess.

**Fix — set a dedicated attachment folder:**
Settings → Files and links → Default location for new attachments → "In subfolder under current folder" or a fixed path like `assets/` or `_attachments/`.

**Practical patterns:**
- Use `_attachments/` at vault root — keeps images out of the note tree but easy to find
- Prefix images with the note name: `Japan-trip-map.png` (avoids collisions from generic names like `image1.png`)
- For wiki pages, use relative paths: `../assets/image.png` from a subfolder page
- **Obsidian Git users:** add `_attachments/` to your `.gitignore` if images are large — or use Git LFS

**Wikilink vs markdown syntax:**
```
![[image.png]]          ← Obsidian wikilink (only works inside Obsidian)
![alt](../assets/x.png) ← standard markdown (portable, works in GitHub/wiki export)
```
For this vault: use standard markdown syntax in wiki pages so they render correctly outside Obsidian.

---

## Claude Code + Obsidian second brain (2026 pattern)

From Shashwat's "How Claude Code and Obsidian Broke Personal Knowledge Management" (87 claps):

The emerging pattern: use Claude Code as the **labour** and Obsidian as the **structure** — you provide direction, AI does the curation.

**Core flow:**
```
Raw input (article, email, note, meeting)
  ↓ Claude Code reads it
  ↓ Identifies which wiki pages are relevant
  ↓ Appends structured knowledge to the right page
  ↓ Commits to git
```

**Why this works better than traditional PKM:**
- Eliminates the "capture but never process" failure mode — Claude processes immediately
- Obsidian provides the durable, git-backed, searchable store
- Claude Code provides the intelligence layer that decides where things go
- You stay in control by reviewing diffs before committing

**Minimal setup** (as described in the article):
1. CLAUDE.md in your vault root describing the wiki structure and conventions
2. A `/wiki ingest` or `/medium-auto` skill that runs the pipeline
3. SessionStart hook or manual trigger — no complex automation required

> "Deploying your Second Brain without too much effort" — Shashwat

---

## Web Clipper + nightly ingest — the "2-click" capture pattern (James Wilkins, Apr 2026)

A complementary pattern to [[#Claude Code + Obsidian second brain (2026 pattern)]] that solves the *capture* half specifically — getting content into the vault with minimal friction, before an AI pass processes it overnight.

**Two components working together:**
1. **Obsidian Web Clipper** (browser extension) — converts any page into a structured Markdown note with full frontmatter, dropped into an Inbox folder. Five templates (Default, PDF/Paper, Video, GitHub, Social Post), each triggered automatically by URL pattern (`schema:@VideoObject`, `arxiv.org`, `/^https.+github\.com\/[^/]+\/[^/]+/`, etc.) — leave the trigger field blank on the default template and order matters (default stays at the top of the list).
2. **Nightly ingest script** (Python, runs in background) — each night picks up everything in Inbox, calls a cheap model (Gemini Flash, or a local Ollama model like Qwen 3 / Gemma 4 to avoid daytime RAM contention) to generate a ~100-word summary + tags, merges/deduplicates/normalizes tags to lowercase-hyphenated form, sets `Ingested: true`, and moves the note Inbox → Wiki via the **Obsidian CLI** (preserves wikilinks; far less fragile than raw filesystem moves).

**The 2-click experience:** click the Web Clipper icon → it auto-picks the right template → glance at title, click Save → note lands fully tagged in Inbox. By morning it's summarized, tagged, and searchable.

**Two guiding design principles:** (1) *Robustness* — must not break on unfamiliar content types, or you'll stop using it; (2) *Minimal effort* — as few steps as possible between "interesting" and "saved," or the system dies from friction.

**Companion plugins:**
- **Templater** — enforces consistent frontmatter on self-authored notes too (not just clips)
- **Local Image Plus** — downloads and embeds linked media locally instead of live-fetching — helps AI agents "see" images and improves offline access

**Honest framing:** "the best system is one you actually use" — start with just the Article template, prove the loop, then layer on PDF/video pipelines once the basic capture habit sticks.

> Source: James Wilkins — "Seamless Content Ingestion for Claude-Obsidian Second Brain" (Generative AI, 2026-04-23, 11 min, 185 claps)

---

## PKM in the AI age — is it dying?

April 2026 debate: with AI assistants that can recall and synthesise information on demand, does personal knowledge management still matter?

**The "PKM is dying" argument:**
- AI can answer questions from raw notes without you building structure
- The effort of tagging, linking, and organising feels wasted when an LLM can search
- Tools like Notion AI and Claude Code reduce the need for manual curation

**The counter-argument (and where the LLM-wiki pattern lands):**
- AI answers are only as good as the knowledge it has access to
- Unstructured raw notes produce generic answers; curated wiki pages produce specific ones
- The act of organising forces understanding — the PKM process is also the learning process
- LLM-maintained wikis are the synthesis: AI does the labour, you get the structure

**Verdict:** PKM as *manual, high-friction* curation may be declining. PKM as *intentional knowledge architecture* (deciding what matters, how it connects) is more valuable than ever — especially when an LLM maintains the actual pages.

> "Is personal knowledge management dying?" — Danielpourasgharian (118 claps)

---

## Obsidian plugin ecosystem shift (2026)

**Dataview** — the dominant query plugin — is being replaced or supplemented by a new stack:

| Old approach | New approach |
|---|---|
| **Dataview** — SQL-like queries over frontmatter | **Properties** — Obsidian's native built-in frontmatter UI |
| Custom `dataviewjs` scripts | **Bases** — Obsidian's new native database view (2026) |
| Dataview `TABLE` views | **Dataview alternative plugins** (DB Folder, Bases) |

**Why the shift:**
- Dataview queries break silently when frontmatter keys change
- Obsidian's native **Bases** feature (rolled out 2026) handles most Dataview use cases without a plugin
- Bases is maintained by the Obsidian team — no plugin compatibility risk on updates

**Current recommended stack (2026):**

| Plugin | Purpose | Status |
|---|---|---|
| **Bases** | Native database/table views | Built-in (Obsidian 2026) |
| **Properties** | Frontmatter UI editor | Built-in |
| **Templater** | Dynamic templates | Still essential |
| **Obsidian Git** | Auto-commit vault | Still essential |
| **Dataview** | Complex legacy queries | Still works, but declining |
| **Excalidraw** | Embedded diagrams | Still widely used |

> "Obsidian Is Changing: The New Plugin Stack Replacing Dataview" — Len (128 claps)

---

## Vault organisation — a reference layout (Daniel Prindii)

A working vault structure from a long-time Obsidian user (Apr 2026). Useful as inspiration, not dogma — Prindii's own framing: "everyone thinks differently and needs to organise their information in a way that makes sense for them."

### Personal rules
- **Pluralise** tags and categories (inspired by Kepano's rules)
- **PascalCase** when writing tags
- **Capitalise** property values; **lowercase** property names (matches built-in `tags`/`aliases`/`cssclasses` and most plugins)
- Aim for **properties reusable across the vault** — don't invent one-off keys
- Create custom tag prefixes for categories — e.g. `LogIdeas-`, `LogJournal-`, `LogZettel`
- Keep a note listing the **hotkeys you use most** — surfaces what to optimise
- **Templates for everything** — daily notes, zettel notes, projects, journal entries

### Top-level folder layout (three-deep max, Johnny Decimal-ish)

| Folder | Purpose |
|---|---|
| `Capture` | Inbox — quick dumps to be processed later |
| `Clippings` | Notes created via Obsidian Web Clipper |
| `Attachments` | Images/PDFs, sub-foldered by project |
| `Admin` | Daily notes, Bases, templates, personal documents |
| `Archive` | Past projects, rarely-used notes |
| `References` | Read books, people, places, art archive — used to build **Bases** |
| `Zettelkasten` | All zettels in one flat folder by Zettel ID; a **Hub note** links them alphabetically by topic |
| `Projects` | Active projects, each with its own `attachments/` and `archive/` |
| `Sources` | Clippings organised by creator/topic |

### Daily note structure
Four sections, in order:
1. **Notepad** — free-form capture (text, tasks, links, ideas) for later processing
2. **Tasks** — a Tasks-plugin query grouped by project tag
3. **Journal** — work progress, ideas, impressions
4. **Article notes & link archive** — highlights and link drops with 2–3 tags

### Tasks
Don't build a complex system inside Obsidian. **Tasks plugin** + a few project tags is enough. Kanban, TasksNotes, and similar plugins tend to disrupt the daily-note flow.

### Theme
Prindii's working setup: **Minimal** theme, dark **Gruvbox** colour scheme, **Flexoki** accents, fonts iA Writer Quattro V / Avenir / IBM Plex Mono.

> Source: Daniel Prindii — "How I organise Obsidian" (Obsidian Observer, 2026-04-24).

---

## HTML vs Markdown for AI-generated artifacts

A pattern worth knowing if Claude (or any LLM) writes a lot of your day-to-day documents: **default Claude's output to HTML for reading-only artifacts, keep Markdown only for files you'll edit**.

| Artifact type | Format | Why |
|---|---|---|
| Daily briefing, status report, summary, retro, memo, dashboard | **HTML** | Renders in a browser, looks designed, gets opened on phones, can be shared as a link or screenshot |
| Specs, READMEs, CLAUDE.md, wiki pages, vault notes | **Markdown** | They get *edited*; Markdown is the editing format; Obsidian's graph/backlinks/search expect `.md` |
| Code, configuration, anything inside a git repo | **Markdown / code blocks** | Diffs cleanly, plays with tooling |

The case against Markdown-as-default for AI output:
- "Easy to write" no longer matters — Claude writes HTML as easily as Markdown.
- "Easy to convert later" rarely happens. Markdown files Claude writes sit unrendered in folders and get deleted unopened.
- The optimisation target is "easy for the reader right now, on whatever device they have." HTML wins.

**CLAUDE.md instruction that flips the default** (one paragraph, project or global):

> When producing documents for human consumption (reports, briefings, summaries, memos, dashboards), default to a single self-contained HTML file with inline CSS — render-ready, no build step. Use Markdown only for artifacts that will be *edited*: specs, READMEs, CLAUDE.md, wiki pages, vault notes. If unsure: is this for reading, or for editing? Reading → HTML. Editing → Markdown.

**Obsidian vault specifically**: keep wiki/`.md` for everything inside `Learn/wiki/` — the WikiLinks, graph, and search are predicated on Markdown. If you need a rendered HTML view of an Obsidian note, embed raw HTML blocks inline in the `.md` (Obsidian renders them), or use a "publish to HTML" community plugin for one-off exports. Don't store `.html` files in the vault as primary notes — they don't participate in the graph.

> Source: Marco Kotrotsos — "Writing HTML For Documentation Instead of Markdown is a Game-Changer", Medium, 2026-05. Builds on Thariq Shihipar's "unreasonable effectiveness of HTML" framing.

---

## Auto-vault pattern — Obsidian + Claude + n8n (Shashwat's 4-layer architecture)

Vaults stop being useful when they become "very organised graveyards for good ideas." The fix is to **reverse the flow** — the vault briefs you, instead of you searching the vault.

### Four layers

1. **Capture (passive ingestion).** Things land in the vault without you filing them: web clippings, Medium digests, voice memos, daily journal stubs. Pipelines (n8n, scripts, Claude skills) write files; you don't.
2. **Storage with a hard folder cap.** Keep top-level folders to ~5 categories. Stops the vault collapsing under its own weight; everything else becomes tags + frontmatter + WikiLinks. (The vault here already follows this: `Learn/`, `DataScraper/`, `TravidMap/`, etc.)
3. **Synthesis (Claude reads the vault).** A `CLAUDE.md` at the vault root defines focus, current goals, active projects, and *agent behaviour* — e.g.: "Surface connections I have not seen. Challenge my assumptions before agreeing with them. Answer strictly from the vault. Flag when my current beliefs contradict something I saved earlier." Updated for ~5 minutes every Monday.
4. **Automated output loops.** A scheduled job (n8n cron, scheduled Claude agent, Task Scheduler) runs daily and writes a morning briefing into the vault. Once a week, a deeper synthesis pass: "What thesis am I building toward? What contradicts my older beliefs? What perspective is missing?"

### Why it works

- Most knowledge never compounds because it sits in isolation. The synthesis layer is what makes it compound.
- You get a permanent, searchable record of every belief you held and changed.
- The agent "reads your mind" while you execute — you stop being the bottleneck on noticing patterns.

### How this maps onto the current vault

- The `/medium` and `/medium-auto` skills already do step 1 (capture) and a partial step 3 (synthesis → wiki pages).
- `MEMORY.md` + auto-memory cover step 3 at the agent-state layer.
- The missing piece is step 4 — a scheduled "morning briefing" agent that reads recent vault activity and writes a digest. The [[Claude-Code#Schedule / routines]] mechanism plus `Learn/wiki/log.md` give the right hooks.

> Source: Shashwat — "The Automated Obsidian Intelligence Vault That Gets Smarter Every Day", AI in Plain English, 2026-05.

---

## Dataview + Claude dashboard — the concrete build (Shashwat, May 2026)

A hands-on recipe for layer 4 of the [[#Auto-vault pattern — Obsidian + Claude + n8n (Shashwat's 4-layer architecture)|auto-vault pattern]] above: a single `Dashboard.md` note made entirely of **Dataview** queries — it has zero content of its own, just reads structured YAML frontmatter from project/client/daily notes and renders live. Update a project note once; the dashboard reflects it everywhere.

**Precondition:** strict, consistent YAML frontmatter on every note (e.g. `type: project`, `status: active`, `deadline:`, `priority:`, `next_action:`, `completion:`). Inconsistent properties silently break the queries.

**Six-query skeleton** (`TABLE WITHOUT ID … FROM "folder" WHERE … SORT … LIMIT`):
1. **Today's priorities** — unfinished tasks due today/overdue, `LIMIT 10` (a hard cap forces ruthless prioritisation instead of becoming an anxiety list)
2. **Active projects** — surfaces each project's `next_action` so you know the blocker without opening the file
3. **7-day window** — anything (project/task/deliverable) with a deadline in the next 7 days, sorted ascending — act before you're forced to react
4. **Client/relationship health monitor** — sort by `health` then `last_contact` ascending so at-risk relationships you haven't touched recently bubble to the top (an automated CRM review)
5. **Open loops** — convention: prefix throwaway lines in daily notes with `OPEN: …`; a query scrapes yesterday's note for that prefix and resurfaces it today, catching things too small for a formal task but too easy to forget
6. **Numeric rollup** (e.g. revenue pulse) — an inline DataviewJS one-liner that maps/reduces over a folder of notes, e.g. `$= dv.pages('"03 - CLIENTS"').where(p => p.status === "active").map(p => p.mrr).array().reduce((a,b) => a + b, 0)`

**The intelligence layer:** wire Claude Code in via the Filesystem MCP and a scheduled job (n8n at 6am) with a prompt like *"Read my dashboard note and every file it references. Tell me the single most important thing today, what's at risk if I don't act, which relationship needs attention, and one open decision — don't describe the tables, tell me what they mean."* A second convention — writing `UPDATE: [Project Name] completion: 65` in the daily note — lets Claude traverse the vault, find the right file, and patch its YAML automatically, closing the loop without you touching the source note.

> Source: Shashwat — "Build an Autonomous Obsidian Dashboard with Dataview and Claude" (AI in Plain English, May 21, 2026)

---

## CLAUDE.md as the cure for context rot (COG framework, Mar–Apr 2026)

Two complementary pieces converge on the same point: **CLAUDE.md isn't documentation, it's the agent's permanent memory** — and skipping it costs hours.

**The "context-starved" framing (ProdRescue/Devrim, "Level Up Your Claude Code with This CLAUDE.md"):**
- Claude sees your code but not your *decisions* — why constructor injection over field injection, why UUIDs not Longs, why DTOs are immutable
- A bad CLAUDE.md restates what Claude can already infer ("we use Spring Boot and PostgreSQL"); a good one explains the *why* behind a convention so the agent can extrapolate correctly to new cases
- Test: delete CLAUDE.md, give the agent the same task — it reverts to generic "tutorial" patterns immediately. Restore it, same task, correct on the first try. The file is the difference between "helpful autocomplete" and "engineer who knows the codebase"
- Practical structure: short, declarative `NEVER` / `ALWAYS` rules grouped by concern (`# Authentication`, `# Transactions`, `# Error Handling`, `# Database Conventions`) — ~200 lines covering the non-obvious decisions only

**The COG (Cognition + Obsidian + Git) framework (Kristopher Dunham, "Claude Code and Obsidian: Turn Your Note-Taking App from a Graveyard into a Living Second Brain"):**
- Suggested vault skeleton: `00-inbox/` (raw captures) → `01-daily/` → `02-personal/` → `03-professional/` → `04-projects/` → `05-knowledge/` (synthesized permanent notes) — keeps agent search scoped by partition
- **Context rot is real and measurable**: Claude Code's 200K window fills with conversation history + file contents + skills + system prompt; compaction triggers around ~35% capacity and *silently drops* earlier instructions, formatting preferences, edge-case handling
- **The fix is structural, not behavioural**: stop treating the chat as memory. "If you find yourself telling the agent the same thing twice, it belongs in CLAUDE.md, not in the chat." Run `/context` periodically to see what's burning the budget — MCP servers are frequent silent offenders
- Evolution cadence once the loop is running: daily Personal Intelligence Brief (7-day freshness policy, cited sources, confidence levels), weekly deep pattern analysis across the whole vault (8–12 min compute), monthly synthesis into `05-knowledge/`
- Named commands that emerge from a mature setup: `/Connect` (find intersections between two unrelated vault domains), `/Trace` (map how an idea evolved across daily notes over time), `/Graduate` (compile scattered notes into a structured proposal/draft)
- **Explicit warning on `--dangerously-skip-permissions`**: removes all human oversight — the agent can delete directories, modify files, pull and execute arbitrary dependencies. COG docs recommend running it only inside an isolated Docker container with a git diff review step before merging back. For normal daily-gardening use, default permission prompts are "appropriate friction for something this powerful"

**How this maps onto the current vault:**
- This vault's `CLAUDE.md` already encodes the non-obvious bits (auto-medium trigger, wiki conventions, path table) — the "why" test above is a good audit lens: does each line explain a decision Claude couldn't infer from the files themselves?
- The COG folder skeleton (`00-inbox` → `05-knowledge`) is closer to Steph Ango's flat-structure philosophy (see [[Obsidian#Obsidian CEO's vault structure (Steph Ango)]]) than it looks — both partition by *processing stage*, not by topic
- `/Connect`, `/Trace`, `/Graduate` are good shapes for future skills once `/wiki` and `/dream` have more material to work with

> Sources: ProdRescue By Devrim — "Level Up Your Claude Code with This CLAUDE.md" (Stackademic, Apr 2026, 82 claps); Kristopher Dunham — "Claude Code and Obsidian: Turn Your Note-Taking App from a Graveyard into a Living Second Brain" (Mar 2026, 91 claps)

---

## Vault as a shared brain — multi-agent consensus (June 2026)

A pattern that uses the vault as common ground for *three* models rather than one. The premise: no single LLM admits its own blind spots, and each has a characteristic bias —

| Model | Strength | Blind spot |
|---|---|---|
| Claude | Synthesis and architecture | Overly cautious on technical claims |
| Codex (GPT-5.4) | Surgical code analysis, edge cases | Loses system-level context |
| Gemini | Broad ecosystem awareness | Favors the Google stack even when alternatives are better |

**The move isn't to pick one — it's to put all three on the same problem, record what each says in the vault, and apply a *consensus gate* before trusting the result.** The Obsidian vault is the persistent shared workspace where the three models write, debate, and review notes; disagreement surfaces exactly the claims worth double-checking. Built entirely on free tools or subscriptions you likely already hold. Conceptually adjacent to this vault's own LLM-wiki + `/dream` flow, but adds cross-model triangulation as a quality check rather than relying on a single agent's output.

> Source: Roan Brasil Monteiro — "Your Vault as a Shared Brain — Obsidian: Multi-Agent with Claude Octopus, Codex, and Gemini" (2026-06-23, 13 min, 107 claps) — member-only; concept from the free preview.

---

## See also
- [[Claude-Code]] — reads and writes vault files; powers the `/wiki` and `/medium` skills
- [[AI-Agents]] — automated agents can maintain notes continuously
- [[AI-ML]] — LLMs that power the knowledge synthesis
