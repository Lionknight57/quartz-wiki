---
title: Claude Code
tags: [tools, ai, agentic, claude-code, skills, mcp, agents]
related: [Git, AI-Agents, AI-ML, Obsidian]
sources: [Claude.md, Medium/2026-03/Medium Daily Digest - 2026-03-22.md, Medium/2026-03/Medium Daily Digest - 2026-03-25.md, Medium/2026-03/Medium Daily Digest - 2026-03-28.md, Medium/2026-04/Medium Daily Digest - 2026-04-07.md, Medium/2026-04/Medium Daily Digest - 2026-04-08.md, Medium/2026-04/Medium Daily Digest - 2026-04-10.md, Medium/2026-04/Medium Daily Digest - 2026-04-11.md, Medium/2026-04/Medium Daily Digest - 2026-04-13.md, Medium/2026-04/Medium Daily Digest - 2026-04-14.md, Medium/2026-04/Medium Daily Digest - 2026-04-15.md, Medium/2026-04/Medium Daily Digest - 2026-04-16.md, Claude_files/Advanced Claude Code.md, Medium/2026-04/Medium Daily Digest - 2026-04-17.md, Medium/2026-04/Medium Daily Digest - 2026-04-18.md, Medium/2026-04/Medium Daily Digest - 2026-04-20.md, Claude_files/Anthropic Is Giving Away 13 Free Courses That Others Charge Thousands For.md, Medium/2026-04/Medium Daily Digest - 2026-04-21.md, Medium/2026-04/Medium Daily Digest - 2026-04-22.md, Medium/2026-04/Medium Daily Digest - 2026-04-23.md, Medium/2026-04/Medium Daily Digest - 2026-04-24.md, Medium/2026-04/Medium Daily Digest - 2026-04-25.md, Medium/2026-04/Medium Daily Digest - 2026-04-28.md, Medium/2026-04/Medium Daily Digest - 2026-04-30.md, parazettel.com/articles/a-couple-of-claude-related-tweaks, towardsai.net/i-built-claude-os-2193d43603b7, Medium/2026-05/Medium Daily Digest - 2026-05-13.md, Medium/2026-05/Medium Daily Digest - 2026-05-15.md, Medium/2026-05/Medium Daily Digest - 2026-05-17.md, Medium/2026-05/Medium Daily Digest - 2026-05-18.md, Medium/2026-05/Medium Daily Digest - 2026-05-19.md, Medium/2026-05/Medium Daily Digest - 2026-05-20.md, Medium/2026-05/Medium Daily Digest - 2026-05-21.md, Medium/2026-05/Medium Daily Digest - 2026-05-22.md, Medium/2026-05/Medium Daily Digest - 2026-05-23.md, Medium/2026-05/Medium Daily Digest - 2026-05-24.md, Medium/2026-05/Medium Daily Digest - 2026-05-25.md, Medium/2026-05/Medium Daily Digest - 2026-05-26.md, Medium/2026-05/Medium Daily Digest - 2026-05-27.md, Medium/2026-05/Medium Daily Digest - 2026-05-28.md, Medium/2026-05/Medium Daily Digest - 2026-05-29.md, Medium/2026-05/Medium Daily Digest - 2026-05-30.md, Medium/2026-06/Medium Daily Digest - 2026-06-07.md, Medium/2026-06/Medium Daily Digest - 2026-06-08.md, Medium/2026-06/Medium Daily Digest - 2026-06-09.md, Medium/2026-06/Medium Daily Digest - 2026-06-10.md, Medium/2026-06/Medium Daily Digest - 2026-06-13.md, Medium/2026-06/Medium Daily Digest - 2026-06-14.md, Medium/2026-06/Medium Daily Digest - 2026-06-16.md, Medium/2026-06/Medium Daily Digest - 2026-06-17.md, Medium/2026-06/Medium Daily Digest - 2026-06-18.md, Medium/2026-06/Medium Daily Digest - 2026-06-19.md, Medium/2026-06/Medium Daily Digest - 2026-06-23.md, Medium/2026-06/Medium Daily Digest - 2026-06-24.md, Medium/2026-06/Medium Daily Digest - 2026-06-25.md, Medium/2026-06/Medium Daily Digest - 2026-06-27.md]
updated: 2026-06-27
---
	
# Claude Code

Anthropic's agentic, terminal-based AI coding partner. Distinct from the Claude.ai chat interface.

> Raw source: `../raw/Claude.md` — rich content with screenshots in `../raw/Claude_files/media/`
> ⚠️ The raw file contains API keys — these are excluded from the wiki.

---

## What makes it different

| Claude (chat) | Claude Code |
|---|---|
| Conversational | Agentic — completes multi-step tasks |
| No file access | Reads/writes files, runs commands |
| Single response | Plans, executes, iterates |
| Manual copy-paste | Code generated directly in your repo |

Claude Code = task agent that organises a plan into multiple steps and uses tools to perform actions.

![Claude Code unique features](../../raw/Claude_files/media/image3.png)

---

## Modes

| Mode | Purpose |
|---|---|
| **Plan Mode** | Explore codebase, design architecture, create task list — no edits |
| **Ask Mode** | Question and answer — no edits |
| **Edit (Auto)** | Full autonomous editing |

**When to use Plan Mode:**
- Planning new projects: architecture, tasks, timeline
- Multi-step implementation across many files
- Code exploration before changing anything
- Interactive development planning

![Claude Code modes](../../raw/Claude_files/media/image16.png)

---

## Key commands

| Command | Description |
|---|---|
| `/doctor` | Show installation and environment info |
| `/context` | Show current context in tokens |
| `/export` | Export current context |
| `/skills` | List available skills |
| `/memory` | View/manage memory |
| `/continue` | Restart after interruption |
| `/exit` | Exit Claude Code |
| `Esc` | Stop current operation |

---

## Skills

Reusable, shareable prompt modules that encode domain knowledge and team standards. Claude automatically applies a skill when the request matches its description — no explicit invocation needed (though you can force it).

**Priority hierarchy (highest wins):**
1. Enterprise/managed skills (system-level, admin-deployed)
2. Personal skills (`~/.claude/skills/`)
3. Project skills (`.claude/skills/`, shared via git)
4. Plugin skills

When two skills share a name, higher priority wins. View loaded skills with `/skills` or ask "what skills are available?".

![Skills storage](../../raw/Claude_files/media/image5.png)

### Skill anatomy

Each skill lives in its own directory with at minimum a `SKILL.md` file:

```markdown
---
name: code-documentation
description: Generates comprehensive code documentation following JSDoc standards. Use when writing documentation, adding docstrings, or documenting APIs.
allowed-tools: Read, Grep, Glob    # optional — restrict tools when active
user-invocable: false              # hide from /command menu (still auto-invoked)
disable-model-invocation: true     # hide from Claude completely (manual only)
---

Document all public functions using JSDoc format.
Include: @param types and descriptions, @returns, @throws, @example.
Explain WHY, not just WHAT. Keep descriptions under 2 sentences.
```

**Description is the most important field** — it must answer: *what does this skill do* and *when should Claude use it?* Vague descriptions ("helps with documentation") don't trigger reliable auto-invocation.

### Advanced skill patterns

**Progressive disclosure** — keep `SKILL.md` under 500 lines; put detailed reference in linked files Claude loads only when needed:

```markdown
# In SKILL.md
For security checks, see [SECURITY.md](SECURITY.md).
For performance patterns, see [PERFORMANCE.md](PERFORMANCE.md).
```

**Bundle scripts** — execute pre-written scripts rather than generating the logic inline. Token savings: only the script output is consumed, not the script itself:

```markdown
# In SKILL.md
Run `scripts/validate-accessibility.sh` and report findings.
```

**Scope with `allowed-tools`** — read-only skills that should never write:
```yaml
allowed-tools: Read, Grep, Glob
```

**Hooks in skill frontmatter** — scoped to the skill's lifecycle (auto-cleaned up when skill finishes):
```yaml
hooks:
  PostToolUse:
    - command: echo "$(date) - Created $CLAUDE_TOOL_INPUT_PATH" >> .claude/skill-log.txt
      matcher: Write
  once: true   # run hook only once per session even if skill invoked multiple times
```

---

## Hooks

User-defined shell commands that execute automatically at specific points in Claude Code's lifecycle. Unlike skills or prompt instructions, hooks are **deterministic** — they always run regardless of what Claude decides.

**Use hooks when actions must always happen, not just when the LLM chooses.**

**Lifecycle events:**

| Hook | When it fires | Best for |
|---|---|---|
| `SessionStart` | Session begins | Setup, initialization, logging |
| `UserPromptSubmit` | Prompt submitted, before Claude processes | Validate/inject context, block request types |
| `PermissionRequest` | Before showing a permission dialog | Auto-allow or auto-deny without interaction |
| `PreToolUse` | Before any tool call | Block dangerous operations (exit code 2 = block) |
| `PostToolUse` | After tool completes successfully | Format, lint, post-process changes |
| `Notification` | Claude sends a notification | React to wait/permission events |
| `Stop` | Claude finishes responding | Enforce completion conditions |
| `SubagentStop` | Subagent task completes | React to subagent work independently |
| `SessionEnd` | Session ends | Cleanup, teardown, final logging |
| `PreCompact` | Before context compaction | Save state before compaction |

![Hooks lifecycle](../../raw/Claude_files/media/image11.png)

### Creating hooks (interactive)

```
/hooks → PostToolUse → Add matcher: Edit|Write → Add hook command
```

Hooks receive context as JSON via stdin:
```bash
# Example: log every file Claude modifies
jq -r '"[\(.timestamp)] \(.tool_name): \(.tool_input.path)"' >> "$CLAUDE_PROJECT_DIR/.claude/file-changes.log"
```

`CLAUDE_PROJECT_DIR` always points to the project root regardless of working directory.

**`PreToolUse` blocking:** return exit code 2 to prevent the tool from executing. Claude receives your stdout as feedback explaining why it was blocked.

### Security best practices

- Always validate and sanitize hook inputs — never trust incoming data blindly
- Quote all variables in shell scripts to prevent injection
- Block path traversal: check for `..` in file paths
- Use absolute paths via `CLAUDE_PROJECT_DIR`
- Skip sensitive files: never touch `.env`, `.git`, key files

### Advanced patterns

**Execution:** multiple hooks on the same event run **in parallel**, not sequentially — they're also deduplicated (identical commands won't run twice). For sequential logic, combine into a single script.

**Timeout:** default 60 seconds. Set to 30 or lower to prevent runaway hooks.

**Settings change safety:** direct edits to hook settings in `.json` files don't take effect immediately — Claude Code requires you to review changes via `/hooks` menu first. This prevents malicious hook injections.

**Hooks in subagent frontmatter** — scoped to the subagent's lifecycle:
```yaml
hooks:
  PostToolUse:
    - command: echo "$(date) READ: $CLAUDE_TOOL_INPUT_PATH" >> analysis.log
      matcher: Read
  Stop:
    - command: echo "=== Analysis complete ===" >> analysis.log
```

Supported events in component (skill/subagent) frontmatter: `PreToolUse`, `PostToolUse`, `Stop`.

### Skills ecosystem explosion (mid-2026)

In January 2026, Andrej Karpathy posted online frustrations with AI coding agents. Within 48 hours, developer Forrest Chang turned the rant into a single `CLAUDE.md`-style skill — four behavioral rules, zero code — and pushed it to GitHub. By June 2026 it had **144,000 stars**, the fastest-growing AI workflow repo ever created:

1. Don't make assumptions — ask
2. Don't over-engineer — match existing complexity
3. Don't touch code you weren't asked to touch
4. Don't invent libraries that don't exist

**Why it matters beyond one repo:** Skills went from "niche developer feature" to what Simon Willison called "maybe a bigger deal than MCP" in about six weeks — driven by two things landing together: Karpathy's viral post, and Cowork (Anthropic's desktop agent) going generally available in April 2026, which exposed skills to non-developers for the first time. As of May 2026 there are community repos with 100+ skills across 15 professions (PMs, data scientists, marketers, writers) — none of whom need to write code, since a functioning skill can be 20 lines. The same `SKILL.md` format now works across Claude Code, Cursor, Gemini CLI, and Codex CLI — a cross-model standard Anthropic created but doesn't control.

**Installing skills (three paths):** Claude.ai/Cowork → Settings → Skills → upload `.skill` file or paste `SKILL.md`; Claude Code → drop the file into the project or use `/install-skill <repo-path>`; from GitHub → most repos ship a `.skill` package, drag into Cowork or the desktop app.

> Source: Mike Written | AI Trends 24 — "A GitHub Repo With One File Just Hit 144,000 Stars. It's a Claude Skill." (2026-06-08)

**Does it actually work? (one-week field test)** A follow-up first-person test of the four-rule file reported four observable behavior changes, each mapping to a rule:

| Rule | Observed change |
|---|---|
| Think before coding | Claude stopped guessing on ambiguous instructions ("add error handling") — it asks "logged, raised, or silently handled?" first instead of picking one and hoping |
| Simplicity first | Same data-processing task went from a custom class + 3 helpers + abstraction layer (no file) to a 12-line function. Smaller diffs, fewer files touched |
| Surgical changes only | Stopped "improving" nearby functions it wasn't asked to touch — smaller blast radius means faster, more trusting review |
| No hallucinated libraries | Flagged an uncertain library method for verification instead of confidently inventing one (weakest-confirmed of the four on a one-week sample) |

**What it does *not* do:** it's a behavioral constraint, not a capability boost — Claude isn't smarter at hard problems or your domain logic, just better at "staying inside the lines you actually drew." The meta-lesson: the instructions you give do more work than people realize; the most viral AI tool of the month was four sentences of precise behavioral instruction, not a new model.

> Source: Mike Written | AI Trends 24 — "I Installed the 144K-Star Karpathy CLAUDE.md File — Here's What Changed" (2026-06-27, 6 min)

---

## MCP — Model Context Protocol

Standard way to connect external tools and data sources to Claude Code. Each server is a separate one-to-one connection — Claude Code instantiates a new MCP client per server (no reuse).

MCP servers expose tools that Claude Code can call, enabling integrations with:
- Databases (MongoDB, PostgreSQL)
- APIs (Gmail, Google Calendar)
- Dev tools (GitHub, Figma, Jira)
- Custom internal systems

![MCP overview](../../raw/Claude_files/media/image14.png)

### Adding MCP servers

```bash
# Add with explicit scope
claude mcp add --scope project github --env GITHUB_TOKEN=$GITHUB_TOKEN -- npx -y @modelcontextprotocol/server-github

# List, inspect, remove
claude mcp list
claude mcp get github
claude mcp remove github
```

### MCP scopes

| Scope | Where stored | Shared? |
|---|---|---|
| `local` (default) | Project-specific user settings | No — you only |
| `project` | `.mcp.json` in project root | Yes — commit to git |
| `user` | Central `~/.claude` config | Yes — across all your projects |

**`.mcp.json`** (project scope) can be committed so the whole team gets the integration — each person provides their own token via env var.

On **Windows**, wrap with `cmd /c` if NPX doesn't resolve: `cmd /c npx -y ...`

### GitHub MCP

Create a Fine-grained Personal Access Token on GitHub with:
- Contents: Read-only
- Pull requests: Read-only (add Write if Claude should open PRs)

Store as `GITHUB_TOKEN` env var — never hard-code in config. Verify with `/mcp` inside Claude Code.

### Figma MCP

Two options:

| Server | Plan needed | How it works |
|---|---|---|
| **Desktop** | Dev Mode (paid plan) | Reads your current Figma selection in real time |
| **Remote** | Any (free = 6 calls/month) | Link-based — paste Figma URLs into prompts |

Desktop server: enable Dev Mode (Shift+D in Figma) → click "Set up Figma MCP" → add over HTTP with `--transport http`.

**Token limits:** Claude warns at 10K tokens per MCP tool call; hard limit 25K. For large files:
```bash
# Raise the per-call limit (use sparingly — burns context window)
export FIGMA_MCP_MAX_NODES=50000
```
Select smaller nodes (card, not whole page) to stay within limits naturally.

### Design-to-code workflow (GitHub + Figma)

1. Select frame in Figma desktop (enables real-time MCP read)
2. Prompt: "Using my current Figma selection, update the hero section to match the updated design"
3. Claude reads frame properties (gradient, colors, spacing) → edits CSS exactly
4. Prompt: "Show me what changed in this branch" → Claude uses GitHub MCP to diff vs main

---

## Subagents

Claude Code can spawn subagents to work in parallel on independent tasks. Each subagent has its own clean context window — this preserves the main agent's context and enables specialization.

**Coordination patterns:**
- Main agent orchestrates, subagents execute
- Each subagent gets a self-contained prompt with full context
- Results come back to main agent for synthesis

### Custom subagent definition

Subagents are Markdown files stored in `.claude/agents/` (project-level, shared via git) or `~/.claude/agents/` (user-level, personal).

```markdown
---
name: code-reviewer
description: Reviews code for quality, security, and accessibility. Use PROACTIVELY when code changes are made.
tools: Read, Grep, Glob          # read-only — cannot modify
model: claude-sonnet-4-6         # optional model override
---

You are a senior code reviewer. Focus on:
- Security vulnerabilities
- Accessibility issues (WCAG)
- Performance anti-patterns
Return prioritized findings with severity levels.
```

**Frontmatter fields:**

| Field | Purpose |
|---|---|
| `name` | How to refer to the subagent |
| `description` | How Claude decides when to auto-invoke. Use "PROACTIVELY" or "must be used" to increase auto-invocation |
| `tools` | Comma-separated list — limits what the subagent can do |
| `model` | Optional — override model for this subagent |

List all available agents with `/agents`.

### Invocation

```
# Automatic — Claude decides based on description match
Review this code for security issues

# Explicit — always works
Use the code-reviewer subagent to check the site for accessibility issues

# Background (async)
Use the content-reviewer subagent in the background to check the main page
# Then press Ctrl+B or include "in the background" in the prompt
```

Manage background tasks with `/tasks`. Press `Esc` to interrupt any running agent.

### Async subagents

- Run independently while main agent continues other work
- Wake the main thread when complete with their findings
- Best for: long-running isolated tasks (full codebase review, large doc generation)
- Not suitable for: tasks that need user input, or tasks that depend on each other

### Built-in subagents

| Name | Purpose | Notes |
|---|---|---|
| `general-purpose` | Multi-step tasks needing explore + modify | Default delegation target |
| `Explore` | Read-only codebase search | Supports thoroughness: `quick` / `medium` / `very thorough` |
| `Plan` | Research before presenting a plan | Auto-used in Plan Mode |

Override any built-in by creating a custom subagent with the same name.

### Troubleshooting

- **Not auto-invoked:** add "PROACTIVELY" or "must be used" to description; make description more specific
- **Errors:** verify tool permissions match what the subagent needs; check system prompt clarity
- **Context pollution:** if the task doesn't need isolation, keep it in the main agent

---

## 4 delegation patterns

Most developers only use pattern 1. The fix for almost every "Claude Code feels slow/weird" complaint is to stop doing all the work in one window.

| Pattern | Best for | Avoid when |
|---|---|---|
| **Interactive session** | Supervised coding, exploration, pair-programming | Long/messy refactors that flood the context window |
| **Subagent** | Noisy work like tests, log parsing, dependency research | Multiple workers need to coordinate with each other |
| **Agent Team** | Parallel work across independent boundaries | Same-file edits or tight cross-cutting dependencies |
| **Routine** | Recurring autonomous work on a schedule or trigger | Tasks that need constant human judgment mid-execution |

### Pattern 1 — Interactive session
Default mode. Single context window. LSP integration catches type errors immediately after edits. **Problem:** at ~20 min, the window fills. Agent re-reads files already read, forgets decisions, quality degrades. Fix: stop doing all work in one window.

### Pattern 2 — Subagent
Separate instance with own context window, system prompt, and tool whitelist. Verbose exploration stays out of your main thread. Define in `.claude/agents/` (project) or `~/.claude/agents/` (user).

```yaml
---
name: test-runner
description: Runs the test suite and reports only failing tests with their error messages
tools: Bash, Read, Grep
model: haiku   # route cheap tasks to cheaper models
---
You are a test-running specialist. Run the full test suite, parse output,
return ONLY failing tests with file paths and error messages. Do not fix.
```

**Key rules:**
- `description` is the routing contract — write it like an API, not a personality blurb
- `tools` is a hard whitelist — limits what the subagent can do regardless of what it tries
- Architecture: hub-and-spoke only — spokes cannot talk to each other
- **Start here:** pick the most context-polluting task in your workflow (running tests, reading logs, legacy search) and make that your first subagent

### Pattern 3 — Agent Teams (experimental)
Shipped Feb 2026 as research preview. Breaks hub-and-spoke: multiple instances run **in parallel** with a shared task list (JSON on disk) and peer-to-peer mailbox for direct agent-to-agent negotiation. Agents unblock dependent cards on a shared kanban automatically.

```json
// ~/.claude/settings.json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

**Token cost:** documented behavior (not worst case) — significantly more than a standard session because each teammate has its own context window. A weekend of experimentation can dent your quota. **Start with subagents** — they do the same job sequentially, often better, at a tenth of the cost.

**Use agent teams when:** task genuinely parallelizes, cost of waiting matters more than cost of tokens, work crosses boundaries needing real specialist negotiation.

**Current limitations:** no session resumption, one team per session, no nested teams, split-pane mode broken inside VS Code integrated terminal.

### Pattern 4 — Routines (cloud-hosted)
Launched April 14, 2026. Claude Code runs on Anthropic's cloud — laptop closed, no terminal open, Wi-Fi optional. Available on Pro/Max/Team/Enterprise with Claude Code web enabled. Set up at `claude.ai/code/routines` or via `/schedule` CLI command.

**Three trigger types:**

| Trigger | Description |
|---|---|
| **Scheduled** | Cron expression, minimum 1-hour interval. "Every Sunday at 3 AM, audit for deprecated deps and open a PR if tests pass." |
| **API** | Unique HTTP endpoint with bearer token. Observability platform detects anomaly → POSTs stack trace → routine fires, investigates, drafts fix. |
| **GitHub** | React to repo events: PR opened, push, release. Routine runs smoke checks, posts architectural review, flags security concerns. |

**Safety guardrails:**
- Runs in "Trusted" network environment; outbound requests blocked with `403 x-deny-reason: host_not_allowed` unless allowlisted
- PRs from webhook-triggered routines constrained to `claude/` prefixed branches — cannot push to main
- ⚠️ A green routine status only means the session started without an infrastructure error — not that the task succeeded. Inspect the transcript or verify downstream effects independently

**Prompt injection risk:** any routine that parses external input (Sentry payloads, PR descriptions from forks, GitHub issue bodies) is a prompt injection surface. Scope permissions narrowly, sandbox aggressively, audit diffs.

### The shift in work
Anthropic internal survey (132 engineers): ~27% of delegated Claude Code tasks were work that **wouldn't have happened otherwise** — documentation, test coverage, papercut UI fixes, internal dashboards. Not faster execution of existing work — new work that the marginal cost of human effort had previously priced out of existence.

> Source: Kristopher Dunham — "Claude Code Has Four Agent Modes. Most Developers Only Use One." (2026-05-26)

---

## Claude Agent SDK

Previously called "Claude Code SDK". For building custom agents programmatically.

```typescript
import { ClaudeAgentSDK } from '@anthropic-ai/claude-agent-sdk';
```

Docs: `https://platform.claude.com/docs/en/agent-sdk/overview`

---

## Models (2026)

| Model | ID | Use |
|---|---|---|
| Claude Opus 4.6 | `claude-opus-4-6` | Most capable |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` | Balanced |
| Claude Haiku 4.5 | `claude-haiku-4-5-20251001` | Fast/cheap |

---

## Competitors

| Tool | Type |
|---|---|
| GitHub Copilot + VS Code | IDE integration |
| Cursor | Agentic IDE |
| Windsurf | Agentic IDE |
| OpenAI Codex CLI | TUI |
| Google Gemini CLI | TUI |
| Aider | TUI |
| OpenCode / AntiGravity | TUI |

**OpenCode + Claude Code combo (Dr. Lofi Dewanto, June 2026):** after GitHub Copilot's price increase, some developers run OpenCode alongside Claude Code rather than choosing one — the appeal is letting multiple LLMs (Claude, GPT, Gemini, DeepSeek) review each other's output as a lightweight form of spec-driven development ("a bug Opus can't solve might be fixed by GPT"). OpenCode's UI exposes planning mode / build mode / task overview more visibly than Claude Code's CLI, which some find more approachable for SDD-style work — though this may just reflect unfamiliarity with Claude Code's equivalent Plan Mode.

> Source: Dr. Lofi Dewanto — "OpenCode is really great! A Single CLI for all LLMs" (2026-06-09, 114 claps)

---

---

## New features (2026)

### Auto Mode
Auto Mode replaced the old permission model. Claude Code now operates autonomously without prompting for approvals at each step. Previously required manually selecting permission modes; Auto Mode is the default autonomous setting.

### Channels
Native team collaboration feature in Claude Code. Allows multiple agents / users to operate in shared channels — positioned as an alternative to OpenClaw's multi-agent workspace.

### Managed Agents
Anthropic's structured agentic workflow system. Enables declarative multi-agent coordination — define agents, their roles, and how they hand off tasks. Promises 10× faster development of complex agentic systems compared to manually wiring subagents.

### Claude Advisor Tool
A new Claude Code feature that routes cheap model requests through an Opus-level "advisor" for guidance before responding — giving smaller models Opus-quality reasoning on hard problems without paying full Opus prices on every token.

- Cheap model (Haiku / Sonnet) does the work
- Advisor (Opus) is consulted when the task is complex
- Result: near-Opus quality at a fraction of the cost
- Not a new model — it's an architectural routing pattern built into Claude Code

> "Anthropic Just Made Cheap Models Think Like Opus (Claude Advisor Tool Is Wild)" — Joe Njenga

### Git Worktrees — parallel development

Each worktree shares the same `.git` directory (full history, commits, remotes) but has its own isolated file state and branch checkout. Run separate Claude Code sessions in each — context never bleeds between them.

**One rule:** each branch can only be checked out in one worktree at a time.

```bash
# Create worktree with new branch
git worktree add -b feature/services-redesign ../novatech-feature-services

# Create worktree on existing branch
git worktree add ../novatech-bugfix bugfix/responsive-nav

# List all worktrees
git worktree list

# Remove (deletes directory, preserves branch)
git worktree remove novatech-bugfix

# Clean up stale references (if directory was manually deleted)
git worktree prune

# Delete branch after merging
git branch -d bugfix/responsive-nav
```

Use **descriptive directory names** that indicate the task (`novatech-feature-services`, `novatech-bugfix`).

**Claude Desktop automatic worktrees:** the desktop app can create worktrees automatically per session (stored in `~/.claude-worktrees/` by default, configurable in Settings → Claude Code → Worktree location). Create a `.worktreeinclude` file in the repo root to copy gitignored files (like `.env`) into new worktrees:

```
# .worktreeinclude — gitignore-style patterns
.env
.env.local
```

### Claude Code UI redesign (April 2026)
Anthropic shipped a significant visual redesign — described as "feels like a new tool":
- Cleaner terminal UI with improved readability
- Reorganised command palette
- Better visual separation between agent steps and output
- Noticeable enough that long-time users immediately noticed the change

> "Anthropic Just Redesigned Claude Code (And It Feels Like a New Tool)" — Joe Njenga (105 claps)

---

### /btw, /fork, /rewind — context hygiene
| Command | Purpose |
|---|---|
| `/btw` | Add a side-note to context without interrupting the flow |
| `/fork` | Branch the conversation — explore an alternative without losing the main thread |
| `/rewind` | Roll back to an earlier point in the conversation |

These are "context hygiene" commands — keep your working context clean without starting over.

---

## Daily workflow (5-part model)

From Rick Hightower's "Claude Code 2026: The Daily Operating System":

1. **Morning brief** — start with a 10-minute routine: review pending tasks, set intent for the session
2. **Context loading** — load relevant files and CLAUDE.md into context before asking anything
3. **Plan before code** — use Plan Mode for any non-trivial task; get Claude to write the plan first
4. **Iterative execution** — work in small committed steps; commit frequently so Claude can reference history
5. **Session close** — use `/export` or memory tools to capture what was done for the next session

### A concrete 4-prompt version (Josh Dubowsky)

A leaner alternative to managing tasks in a to-do app: run four scripted Claude prompts at fixed points in the day instead of maintaining tags/projects/filters. Each prompt has a deliberate constraint that does the actual work:

| When | Prompt does | The constraint that makes it work |
|---|---|---|
| ~8am | Reviews open threads, picks the one to move forward today | "...without guilt" — forces a real cut instead of trying to fit everything in |
| ~11am | Converts "I'm stuck" into a single next action | "Do not solve the whole thing" — stops a 7-point strategic plan when you just need the next 15 minutes |
| ~2pm | Honesty check against the morning's plan | Explicitly asks whether you're "on track, behind, or **rationalising**" — names self-deception directly |
| ~5pm | Closes the day: finished / open / let go | "What can I let go of entirely" — productivity advice is usually about adding; this is about subtracting so tomorrow doesn't start carrying tonight's open loops |

**Underlying claim:** the to-do app itself (tags, projects, weekly-review rituals) isn't doing more than five plain lines of text would — the meta-layer of maintaining a system is often mistaken for doing the work it was meant to track. This pattern is to-do-list-specific but the same idea (replace an app's organizational ritual with a structured prompt run at a fixed time) generalizes to other recurring personal workflows.

> Source: Josh Dubowsky — "The Claude Workflow That Replaced My To-Do List" (2026-05-28, 162 claps)

---

## Cost reduction strategies

The Medium digests surface several approaches to reduce Claude Code token spend:

| Strategy | Saving |
|---|---|
| Use MiniMax m2.7 for routine tasks | ~90% cost vs Opus 4.6 |
| Prefix caching (prompt caching) | Up to 90% on repeated context |
| `/fork` to avoid context bloat | Keeps token count low |
| Use Haiku 4.5 for simple subtasks | 10–20× cheaper than Opus |
| Summarise long conversations before continuing | Reduces tokens per turn |
| Keep CLAUDE.md tight — no noise | Less context = lower cost per call |
| Selective context loading (only load needed files) | 49× fewer input tokens |
| Constrain output format (JSON/structured, no prose) | Up to 75% fewer output tokens |
| Use Claude Advisor Tool for routing | Opus quality at Haiku price |

> "Stop Burning $100/Month on AI Tokens: 4 Simple Hacks" — HabibWahid  
> "49x Fewer Tokens While Coding With Claude. No Prompt Engineering Required." — Ruqaiya Beguwala  
> "I Cut Claude Code's Output Tokens by 75%. Why Did Nobody Tell Me?" — Alex Dunlop

---

## Harness engineering

Anthropic and OpenAI both independently arrived at a **two-agent harness** pattern for complex features:

```
Agent A (Planner / Orchestrator)
  → breaks feature into tasks
  → sends tasks to Agent B

Agent B (Executor)
  → implements tasks with full context for that task only
  → returns result to Agent A

Agent A
  → integrates results
  → manages the feature list
```

**Why this works:** Each agent only sees the context it needs. The "Zero Context Loss" problem (context window filling up mid-feature) is solved by keeping agents focused and handing off structured results rather than raw conversation.

> From Rick Hightower: "Anthropic's Harness Engineering: Two Agents, One Feature List, Zero Context…"

---

## Notable skills (community)

| Skill | What it does |
|---|---|
| **Karpathy coding skill** (144K★) | Four rules fixing common failure modes: don't assume, don't over-engineer, don't touch untouched code, don't invent libraries |
| **Boris Cherny's 42 tips** | Packaged collection of Claude Code best practices from Anthropic's own engineer |
| **Superpowers** | Forces Claude to plan first, work in isolation, write tests before code, then self-review twice — major quality boost |
| **Context Mode** | Filters junk before it reaches Claude's memory + keeps a running session log. Solves "Claude gets weird after 30 min." Sessions that died at 30-min now run for hours |
| **AutoResearch** | Karpathy-style research agent that synthesises multiple sources |
| **Skillify** | Hidden internal skill — installs/manages other skills |
| **LLM Wiki** | Maintains a self-updating knowledge base (Karpathy pattern) |
| **Sycophancy Skill** | Forces Claude to argue the strongest case *against* your idea before considering yours. Prohibits "great question" energy. Anti-sycophancy at the source |
| **Morning Intelligence** | 15-question interview captures your role/focus → writes a master prompt → paste into a Routine for daily scheduled news briefings |
| **Email Tasks** | Connected to Gmail, reads inbox for last N hours, surfaces only what needs a reply/decision — skips receipts/notifications |
| **Notebook LM Connector** | Runs inside Claude Chrome extension, automates NotebookLM (create notebooks, add sources, generate mindmaps) |
| **Thumbnail Generator** | Paste article link → Claude suggests compositions → calls Gemini → evaluates images via CV — batch batch candidates at once |

> Source: The PyCoach — "We Built 70+ Claude Skills. These Are The Best" (2026-05-26, 618 claps)

---

## Obsidian integration

Claude Code can read and write Obsidian vaults directly. Key patterns:
- Use Claude Code to maintain wiki pages (the LLM-wiki pattern)
- Run Claude Code inside the vault folder so it has full context of your notes
- Use memory files (`MEMORY.md`) to persist session context across conversations
- Claude Code can auto-update wiki pages from new sources (see [[Obsidian]])

> "The TL;DR of Claude Code Inside Obsidian" — Theo Stowell

### Obsidian CLI — make Claude vault-native

Obsidian now ships an official command-line interface (`obsidian` command). Without it, agents fall back to generic filesystem commands (`grep`, `mkdir`, `touch`) which are slower, more token-heavy, and unaware of vault semantics (templates, daily notes, links).

**Setup pattern (Theo Stowell, Apr 2026):**
1. Web-clip the Obsidian CLI help page (`https://obsidian.md/help/cli`) into the vault so the agent has a local reference.
2. Add a directive in your base `CLAUDE.md`: *"Be CLI-first — use the `obsidian` command for any vault operation. Fall back to filesystem commands only when `obsidian` can't do the task."*
3. Result: agent uses Obsidian-native commands (write to top of daily note, create from template, etc.) instead of token-heavy file manipulation.

### internetVin terminal plugin — Claude inside the vault

A community plugin purpose-built for running Claude Code inside Obsidian. Not yet in the official Community Plugin store — install via **BRAT** from the GitHub repo: `internetvin/internetvin-terminal`.

| Feature | Why it matters |
|---|---|
| Zero-config `claude` launch | Other terminal plugins need shell-path wrangling; this one just works |
| Inherits theme fonts | Looks integrated, not a foreign panel |
| `ALT/OPT + BACKSPACE` deletes word | Standard editor behavior — most terminals don't do this |
| `[[wikilinks]]` resolve to file paths | Reference notes inline in prompts without copy-pasting paths |
| Split-pane workflow | Note on one side, agent on the other — no context switching to a separate terminal |

> Source: Theo Stowell, "Two Claude x Obsidian Tweaks That Are Actually Useful" (Obsidian Observer, 2026-04-23)

---

## Structured prompting (not "vibe coding")

"Vibe coding" = throwing vague prompts and hoping for the best. Better approach:

1. **Give context first** — relevant files, constraints, what already exists
2. **State the outcome, not the steps** — "make the tests pass" not "edit line 42"
3. **Use CLAUDE.md** — persistent project instructions Claude reads every session
4. **Review the plan** — always read Plan Mode output before approving execution
5. **One concern per prompt** — mixing concerns leads to mixed results
6. **Commit often** — every working state should be a commit; Claude uses history

> "Stop 'Vibe Coding': 6 Strategies to Actually Get Good Results with Claude Code"

---

## Observability and monitoring

From Reza Rezvani's "The New Claude Code Monitoring: What Our Team Data Revealed":

**Stack:** OpenTelemetry + Claude Code's built-in telemetry hooks.

**8 key metrics to watch in production:**

| Metric | What it reveals |
|---|---|
| Token usage per session | Cost hotspots — which agents/prompts burn most tokens |
| Tool call frequency | Which tools are called most; unused tools add context overhead |
| Error rate by tool | Flaky tools that need retry logic or replacement |
| Session duration | Long sessions = context filling up; trigger for `/fork` |
| Re-prompt rate | How often Claude asks for clarification — a proxy for prompt quality |
| Subagent spawn count | Whether parallelisation is actually happening |
| Cache hit rate | Prompt caching effectiveness — high hits = good CLAUDE.md structure |
| Task completion rate | % of agent sessions that reach the goal vs. abandon |

**OpenTelemetry setup:**
```json
// .claude/settings.json — enable telemetry export
{
  "telemetry": {
    "enabled": true,
    "endpoint": "http://localhost:4317"
  }
}
```

> "The New Claude Code Monitoring: What Our Team Data Revealed" — Reza Rezvani (9 min)

---

## Running multiple agents (app-based)

Alex Dunlop's approach for running 10+ Claude Code agents simultaneously without juggling terminal windows — using a dedicated multi-agent management app:
- Launch and monitor multiple Claude Code sessions from a single UI
- Each agent gets its own context/worktree
- Visual status across all running agents
- The app (unnamed in the digest snippet) fills the gap that Claude Code's native terminal UI doesn't address for large parallel workloads

> "I Run 10 Claude Code Agents Easily Using This App" — Alex Dunlop (219 claps)

### cmux — open-source terminal for parallel sessions

A purpose-built, now-open-source terminal for running multiple Claude Code (and Codex) sessions side by side. Targets a specific pain point: running 5-6 sessions across crowded Warp tabs makes titles unreadable, and when an agent gets stuck, macOS's native notification just says "Claude is waiting for your input" with no indication of *which* session — forcing a click-through of every tab to find the stuck one. cmux is built around giving each session enough visible context that notifications are actually actionable.

> Source: Chimin — "This Terminal Built for Claude Code Is Now Open Source!" (2026-06-10, 261 claps)

---

## Personal assistant pattern

From nardaimonia's "How to build a Claude Code personal assistant that saves you hours every week":

**Core idea:** Set up Claude Code once with hooks and skills targeting your most repetitive work — then it runs automatically.

**High-value automation targets:**
- Morning briefing: summarise overnight emails, PRs, and calendar
- Daily standup draft: pull yesterday's git commits → write standup notes
- End-of-day summary: what changed, what's pending, what to pick up tomorrow
- Recurring report generation from data files
- Auto-triage of GitHub notifications

**Pattern:**
```
SessionStart hook → runs morning brief
UserPromptSubmit hook → context-aware shortcuts
PostToolUse hook → auto-format or validate after file edits
CronJob → daily summary at 5pm
```

> "How to build a Claude Code personal assistant that saves you hours every week" — nardaimonia (167 claps)

**Variant — the `HQ/` "Chief of Staff" folder:** instead of aiming Claude Code at a *repo*, aim it at your *week*. A single `HQ/` directory holds a `CLAUDE.md` that teaches Claude who you are and what you're shipping this quarter, **six skills**, and MCP connections to calendar, email, and Slack. A `/standup` command then replaces the ~200 words of context you'd otherwise paste each session — it already knows yesterday's open threads, today's meetings (from the real calendar), and decisions you parked. The author's key caveat: the folder is "the easy 20%" — whether the setup *compounds* or quietly dies in a week depends on the upkeep discipline, not the initial scaffold.

> Source: Anubhav — "I Turned Claude Code Into My Chief of Staff (One Folder, 6 Skills)" (Data Science Collective, 2026-06-25, 12 min, 115 claps) — member-only; pattern from the free preview.

---

## Computer Use — Claude's escalation order (Cowork)

Claude's **Computer Use** lets it control the keyboard and mouse in any app you allow — but in the Cowork app Claude deliberately treats it as a *last resort*, following a fixed escalation order for any task:

1. **Connector** (Gmail, Google Drive, etc.) — if one exists, Claude uses it: fastest and most reliable path.
2. **Chrome browser** — when no connector covers the tool, Claude drives the browser.
3. **Computer Use** — only when 1 and 2 can't accomplish the task does it fall back to raw screen/keyboard/mouse control.

Practical implication: even if you explicitly prompt "use computer use for this," Claude will still prefer a connector or the browser if one can do the job — so set up connectors for the tools you automate most.

> Source: The PyCoach — "Claude Can Now Click, Type, and Navigate Your Computer Apps. Here Are My Best Use Cases" (Artificial Corner, 2026-06-25, 5 min, 291 claps) — member-only; escalation order from the free preview.

---

## CLAUDE.md — real-world patterns

The CLAUDE.md file is the most leveraged single file in the Claude Code ecosystem. Two 2026 case studies:

### Japanese tax firm (600 claps)
A Japanese tax accountant replaced a significant amount of institutional process knowledge with a single CLAUDE.md file:
- Captured all firm-specific rules, client preferences, and recurring task procedures
- Claude Code reads it every session — effectively hired as the "most important team member"
- Zero training overhead for new tasks — the file is the institutional memory
- Lesson: CLAUDE.md is not just a dev tool — it's a knowledge management system for any domain

> "The Most Important Employee at a Japanese Firm Was a Markdown File" — Kaitai Dong (600 claps)

### Andrej Karpathy's CLAUDE.md (10K+ downloads)
Karpathy's personal CLAUDE.md became widely downloaded after publication. Key patterns:
- Strict persona instructions ("you are an expert X")
- Explicit anti-patterns ("never do Y")
- Preferred code style and architecture guidelines
- Tool-specific instructions (which linter, formatter, test runner to use)

> "What Is Andrej Karpathy's CLAUDE.md File? And Why 10,000+ Developers Downloaded It" — Ai studio

**Best practices for CLAUDE.md:**
- Put it in the project root — Claude reads it every session automatically
- Keep it focused — remove anything Claude would do correctly by default
- Include project-specific constraints, not general programming advice
- Version-control it — it's living documentation of your AI collaboration style

---

## Community tooling (2026)

### AI coder rate limit tracker
A community tool that tracks rate limits across Claude Code, Cursor, Gemini CLI, and other AI coding tools in one dashboard. Addresses the common pain of hitting daily limits mid-session without warning.

> "Someone Just Built the Tool Every AI Coder Was Silently Begging For" — Ruqaiya Beguwala

### 6 underrated community repos
From Joe Njenga's roundup — repos that are widely unknown but provide significant quality-of-life improvements:
1. **Persistent subconscious** — gives Claude Code background context that persists across sessions
2. Community skill packs extending the built-in skill system
3. Custom MCP server collections for workflow integration
4. Token optimization and selective context loading utilities
5. Session export/import and continuity tools
6. Alternative model configuration (use Gemma/Qwen/local models with Claude Code)

> "6 Claude Code FREE Repos I Wish I Found Earlier (That You Don't Know Exist)" — Joe Njenga (200 claps)

### Open-source AI assistant tools by GitHub stars (June 2026 roundup)

NocoBase's roundup of the most-starred open-source AI assistant tools splits into enterprise platforms and personal tools. The one built specifically *for* Claude Code/Codex workflows:

| Tool | Stars | What it does |
|---|---|---|
| **Ruflo** (`ruvnet/ruflo`) | 58.6K | Multi-agent collaboration platform for Claude Code and Codex — coordinates specialized agents (dev/test/security/docs/architecture/DevOps) via a plugin system + vector memory |
| **NanoClaw** | 29.8K | Lightweight assistant, agents run in containers, connects to WhatsApp/Telegram/Slack/Discord/Gmail |
| **CopilotKit** | 34.4K | Frontend framework for embedding AI copilots/generative UI into existing product interfaces |
| **NocoBase** | 22.7K | No-code platform — natural-language data model/workflow/permission generation for enterprise internal systems |
| **Leon** | 17.3K | Privacy-focused personal AI assistant, runs locally or self-hosted |
| **DeepChat** | 6K | Desktop assistant, multi-model (OpenAI/Gemini/Anthropic/DeepSeek/Ollama), MCP + Skills + ACP |
| **GitHub Copilot for Xcode** | 6.1K | Native Xcode AI assistant for Swift/iOS/macOS — completion, chat, review, Agent Mode |
| **Everywhere** | 6K | Desktop assistant with screen-context awareness — perceives current app/screen, no screenshots needed |

Of these, **Ruflo** is the direct analogue to Claude Code's own Managed Agents/subagent system — same multi-specialist-agent idea, packaged as a standalone orchestration layer that sits on top of Claude Code/Codex rather than inside them.

> Source: NocoBase — "Top 8 Open Source AI Assistant Tools by GitHub Stars" (2026-06-16, 14 min read)

---

## Enterprise and team deployment

### Authentication options

| Method | Best for |
|---|---|
| **Claude Console** | Teams using Anthropic API with per-token billing + analytics dashboard |
| **Claude Pro/Max** | Individuals or small teams; unified web + Claude Code subscription |
| **Amazon Bedrock** | AWS shops — uses IAM, routes through AWS infrastructure |
| **Google Vertex AI** | GCP shops — GCP credentials, Google Cloud billing |
| **Microsoft Foundry** | Azure shops — Entra ID, Azure billing |

For Console: invite users as *Claude Code role* (Claude Code API keys only) or *Developer role* (broader API access).

### Permission model

Tools fall into three categories:
- **Read-only** (Read, Glob, Grep, ls) — run without approval
- **Bash commands** — require approval first time; permission persists for the session
- **File modifications** — require approval; **resets every session** (deliberate safety measure)

### Settings hierarchy (highest priority wins)

```
Enterprise managed policies   ← IT admin, cannot be overridden by users
  └─ CLI arguments
      └─ .claude/settings.local.json   (personal, gitignored)
          └─ .claude/settings.json     (project, shared via git)
              └─ ~/.claude/settings.json   (user-level)
```

### Permission rules in settings.json

```json
{
  "permissions": {
    "allow": ["Bash(npm run *)", "Bash(git *)", "Read(**)", "Edit(**/*.ts)"],
    "deny":  ["Bash(rm -rf *)", "Bash(curl *)", "Edit(**/.env)"]
  }
}
```

Rule precedence: `deny` > `ask` > `allow`. View active rules with `/permissions`.

### Organization-wide CLAUDE.md

Stored in system-level paths (requires admin privileges). Deployed via MDM (Jamf), Group Policy, Ansible, Puppet, or Chef. Every user gets it automatically on session start.

**`@` import syntax** for splitting into separate standards files (max 5 levels deep):
```markdown
@/etc/claude/standards/coding-style.md
@/etc/claude/standards/security.md
```

**`.claude/rules/` directory** for project-level modular rules — all `.md` files auto-loaded alongside `CLAUDE.md`. Scope rules to specific paths with YAML frontmatter:
```yaml
---
paths: ["src/api/**/*.ts"]
---
Always validate input at API boundaries. Return RFC 7807 problem details on error.
```
Rules without `paths` apply universally.

### Monitoring and observability

| Tool | Available to | What it shows |
|---|---|---|
| `/cost` command | API (pay-per-token) users | Session cost, API duration, lines modified |
| Console analytics dashboard | Claude API orgs | Daily active users, costs per user, code acceptance rate, lines written |
| OpenTelemetry export | All deployment types | Token usage, tool call frequency, cache hit rate, session duration, completion rate |

OpenTelemetry config in `.claude/settings.json`:
```json
{
  "telemetry": { "enabled": true, "endpoint": "http://localhost:4317" }
}
```
Feeds into Prometheus, Grafana, Datadog, etc. User prompts are redacted by default; only prompt length is recorded.

### Enterprise adoption friction — the Microsoft case

A cautionary case study on rolling Claude Code into a large org without cost controls. Microsoft's **Experiences & Devices** division (Windows/Outlook/Teams/Surface) quietly gave engineers access to Claude Code. Satisfaction was **91%**, and engineers reportedly chose it over Microsoft's own AI tooling, with productivity up. In May 2026 Microsoft told thousands of those engineers to stop using it — reporting points to cost ("then the bills arrived"), not capability, as the driver of the ban, despite Microsoft having invested $13B in OpenAI and claiming AI already writes 30% of its code internally.

**The lesson for enterprise rollout:** the cost monitoring described above (`/cost`, Console analytics, OpenTelemetry token tracking) isn't optional polish once a tool sees real adoption — without per-team budget visibility from day one, a high-satisfaction rollout can still get killed purely on the finance side, independent of whether the tool is working.

> Source: Pragnesh Ghoda — "Microsoft Just Banned Its Own Engineers From Using AI. That Should Terrify Every Tech Company." (Level Up Coding, 2026-06-17, 271 claps)

---

## .claude/ folder structure (2026 best practice)

From Youssef Hosni's "How to Structure .Claude/ Folder for Maximum Efficiency" (27 min, 437 claps):

```
.claude/
  CLAUDE.md                  ← project context (read every session)
  settings.json              ← shared permissions, hooks, MCP servers
  settings.local.json        ← personal overrides (gitignored)
  agents/                    ← custom subagent definitions
    code-reviewer.md
    doc-generator.md
  skills/                    ← project-level skills
    security-review/
      SKILL.md
  rules/                     ← modular rules (auto-loaded with CLAUDE.md)
    api-standards.md         ← can scope to paths via YAML frontmatter
    testing-standards.md
  commands/                  ← custom slash commands
  hooks/                     ← hook scripts (called from settings.json)
  memory/                    ← persistent memory files (MEMORY.md index)
  file-changes.log           ← audit trail (written by PostToolUse hook)
```

**Key principle:** keep `CLAUDE.md` focused on project-specific constraints. Use `.claude/rules/` for modular standards — each file auto-loads alongside `CLAUDE.md`.

---

## /ultraplan — extended planning mode

From ZIRU's "Why 90% of Claude Code Users Are Missing Its Most Powerful Feature" (6 min):

`/ultraplan` activates a deeper planning mode than standard Plan Mode. Described as making the local terminal "feel like a senior architect is reviewing the plan":
- Produces more thorough architecture analysis before any code changes
- Breaks complex features into smaller, validated milestones
- Explicitly considers dependencies and failure modes
- Best for non-trivial multi-file features, not quick fixes

> "The planning tool that made my local terminal feel like a senior architect was in the room" — ZIRU

---

## /team-onboarding command

From Joe Njenga's "I Tested Claude Code /team-onboarding (And It Fixes Team Setup Chaos)" (4 min, 140 claps):

`/team-onboarding` generates a guided onboarding document from your project's `CLAUDE.md`, skills, and settings — helping new team members ramp up without manual documentation:
- Reads existing project configuration and generates a "how to work with Claude Code on this project" guide
- Surfaces all configured skills, hooks, and MCP servers with explanations
- Reduces the setup friction for teammates joining a Claude Code–enabled project

---

## Claude Code performance — the context nerf

From Alex Dunlop's "Claude Code Insane Nerf. AMD Noticed (Here's How You Fix It)." (5 min, 164 claps, based on 6,852 sessions):

Claude Code was observed stopping "thinking" mid-session — effectively self-limiting reasoning quality as context grew. AMD engineers noticed the pattern and documented the fix:

**Symptoms:** Claude becomes less thorough, gives shorter responses, misses edge cases — usually in longer sessions as context fills.

**Root cause:** implicit context pressure — Claude economises reasoning as the token budget shrinks.

**Fixes:**
- Use `/fork` to branch into a fresh context before tackling complex sub-tasks
- Use `/compact` to summarise history before it fills the window
- Break long sessions into committed milestones: commit → new session → continue
- Keep CLAUDE.md tight — every unnecessary line costs reasoning budget

### The 30-minute session decay

Marco Kotrotsos's "Your Claude Code Sessions Are Dying at 30 Minutes" (Apr 2026) traces the same nerf to a measurable inflection: useful reasoning quality drops sharply around the 30-minute / ~80K-token mark on most Claude Code sessions. His recovery toolkit:

| Command | When to use |
|---|---|
| `/clear` | Starting a fresh sub-task that doesn't need prior history |
| `/compact` | Preserving thread but cutting context to a summary |
| `/rewind` | Roll back to before the current rabbit-hole started |
| `/btw` | Side-note that doesn't pollute the main conversation |
| Subagents | Delegate the next sub-task to a clean-context agent |

**Rule of thumb:** if the session is over 30 min and Claude is asking clarifying questions it wouldn't have asked at minute 5, the context is the problem — not the prompt.

---

## Feature specs for coding agents

From Rico Fritzsche's "How to Write Feature Specs That Coding Agents Can Actually Implement" (8 min, 147 claps):

Coding agents need **deterministic contracts**, not vague stories. Key principles:

| Vague (fails) | Deterministic (works) |
|---|---|
| "Add search functionality" | "Add a GET /search?q= endpoint returning `{results: Video[], total: int}`" |
| "Make it faster" | "P95 response time < 200ms on the /api/videos endpoint" |
| "Handle errors gracefully" | "Return HTTP 422 with `{error: string, field: string}` on validation failure" |

**Spec structure that agents implement reliably:**
1. **Given/When/Then** — explicit preconditions, trigger, expected outcome
2. **Interface first** — define the contract (API shape, types) before behaviour
3. **Failure cases explicit** — name every error state with its expected response
4. **No ambiguous adjectives** — never "fast", "clean", "appropriate"

> "Why Agentic Coding Depends on Deterministic Contracts" — Rico Fritzsche

---

## Three generations of agent orchestration

From Rick Hightower's "From Claude Code Skills to Adversarial Subagent Orchestrators to the Claude..." (16 min):

| Generation | Pattern | Key characteristic |
|---|---|---|
| **Gen 1 — Skills** | Single agent + skill library | Skills encode domain knowledge; Claude decides when to apply |
| **Gen 2 — Subagent orchestration** | Orchestrator + specialist subagents | Parallelism, context isolation, role-based delegation |
| **Gen 3 — Adversarial subagents** | Critic + builder pattern | One subagent builds, another actively tries to break it — red team built in |

**Adversarial pattern:** the "critic" subagent receives the builder's output and attempts to find flaws, security issues, or missing cases. The orchestrator iterates until the critic is satisfied. Quality gate is embedded in the workflow rather than added later.

---

## MEMORY.md — persistent cross-session memory

From Youssef Hosni's "Claude Code - MEMORY.md: Everything you need to know" (10 min, 518 claps):

`MEMORY.md` is Claude Code's persistent memory system — a markdown file that Claude reads at the start of every session to remember context from previous conversations.

**How it works:**
```
~/.claude/projects/<project-hash>/memory/MEMORY.md   ← project-specific memory
~/.claude/memory/MEMORY.md                           ← global memory (all projects)
```

**What to store in MEMORY.md:**
- User preferences and work style
- Recurring project-specific decisions
- Things you've explicitly asked Claude to remember
- Context that would otherwise be lost between sessions

**What NOT to store:**
- Code patterns (derive from current codebase)
- Git history (use `git log`)
- Temporary task state (use TodoWrite instead)
- Anything already in CLAUDE.md

**Memory types** (match to the right file):
- `user.md` — who you are, your role, expertise
- `feedback.md` — corrections, confirmed approaches (what to avoid/repeat)
- `project.md` — ongoing work, goals, decisions
- `reference.md` — where to find things in external systems

> "The MEMORY.md index (MEMORY.md) is loaded every session — keep entries under 200 lines or they get truncated."

---

## Claude Hardware Buddy (April 2026)

Anthropic open-sourced a BLE (Bluetooth Low Energy) hardware companion device for Claude. Follows the pattern of MCP → Agent Skills → hardware integration — extending Claude's surface area beyond the terminal into the physical world. Details are still emerging but it positions Anthropic in the ambient/wearable AI space alongside competitors like Rabbit and Humane.

> "Anthropic Just Open-Sourced the Claude Hardware Buddy" — Marco Kotrotsos (7 min, 20 claps, Apr 2026)

---

## Claude Code for Creating Diagrams

Claude Code's usefulness extends well beyond code generation — it can create diagrams directly from descriptions or existing code:

- **Mermaid diagrams** — flowcharts, sequence diagrams, ER diagrams, Gantt charts — output as fenced code blocks that render in GitHub, Obsidian, and most markdown previewers
- **Architecture diagrams** — describe a system, get a Mermaid `graph` or `sequenceDiagram` representation
- **Workflow from code** — point Claude Code at existing code and ask it to generate a sequence diagram showing the call flow

```
# Example prompts
"Generate a Mermaid sequence diagram for the login flow in auth.service.ts"
"Create an ER diagram showing the relationships between these entity classes"
"Draw a flowchart of what happens when a user submits the checkout form"
```

The output is diagram-as-code — version-controllable, diff-able, and editable as text. Treat it like any other generated code: review, adjust, commit.

> "Claude Code for Creating Diagrams" — Nick Babich (UX Planet, 5 min, 136 claps)

---

## Claude Routines (April 2026)

See [[#4 delegation patterns]] → Pattern 4 for full detail on trigger types (scheduled/API/GitHub), safety guardrails, and prompt injection risks.

**Claude Routines** = cloud-hosted autonomous agent runs. Laptop closed, terminal not open, Wi-Fi optional. Set up at `claude.ai/code/routines` or via `/schedule`.

- Scheduled: cron, 1-hour minimum interval
- API: bearer-token webhook endpoint
- GitHub: PR/push/release events

> "Anthropic Just Launched Claude Routines" — Ai studio (103 claps, Apr 2026)

---

## 4-file system for reliable AI agent coding

From Kristopher Dunham's "Stop Vibe Coding: The 4-File System That Turns AI Agents Into Reliable..." (8 min, 167 claps):

AI coding agents are capable of reading your entire codebase — but without structure, they produce unreliable output. The 4-file system gives agents deterministic contracts to work from:

| File | Purpose | Content |
|---|---|---|
| `SPEC.md` | What to build | Feature requirements, acceptance criteria, interfaces |
| `PLAN.md` | How to build it | Step-by-step implementation plan, dependencies, order |
| `PROGRESS.md` | Where we are | Completed steps, current step, blockers |
| `REVIEW.md` | What to check | Quality gates, tests to pass, definition of done |

**Why it works:** agents fail because they vibe-code from vague instructions. These 4 files give the agent the same deterministic contracts you'd give a human engineer. Each file is updated as work progresses, keeping the agent oriented across multiple sessions.

**Pairs with CLAUDE.md:** the 4-file system handles the current task; CLAUDE.md handles project-wide context.

### DESIGN.md — the fifth file (Apr 2026)

Marco Kotrotsos's "DESIGN.md: The Complete Guide" (10 min) extends the SPEC/PLAN/PROGRESS/REVIEW pattern with a separate **DESIGN.md** that captures the *why* before the *what*:

- `DESIGN.md` — the architectural narrative: rationale, trade-offs considered, options rejected and why, the shape of the solution before any interface is fixed
- Sits **upstream of SPEC.md** — design decisions feed into the spec, not the other way round
- Most agent failures trace back to a missing or vague design — agents pattern-match local code instead of working from the intended architecture

**When to write one:** any feature that touches more than two files, introduces a new abstraction, or has more than one reasonable implementation. Skip for one-off bug fixes.

> "DESIGN.md: The Complete Guide" — Marco Kotrotsos (Autocomplete. Real World AI, 10 min, Apr 2026)

---

## Anthropic's internal skills guide

From Ruqaiya Beguwala's "I Read Anthropic's Internal Guide on Building Claude Skills" (8 min, 133 claps):

Key takeaways from Anthropic's own internal documentation on Skills:

**Structure:**
- Skills are markdown files with YAML frontmatter + instruction body
- The `description` field is the trigger — Claude reads it to decide when to invoke the skill automatically
- Keep skills focused on one job; don't try to make a "super skill" that does everything

**Triggers (how Claude decides to use a skill):**
- Description must answer "when should I use this?" not just "what does this do?"
- Action verbs in the description significantly improve auto-invocation ("use when creating", "apply whenever reviewing")
- Test triggers by asking Claude "what skills are available and when would you use each?"

**Testing:**
- Test with `/skills` to confirm the skill loaded
- Test auto-invocation: describe a scenario and see if Claude suggests the skill without you mentioning it
- Test explicit invocation: call the skill by name to verify the instructions produce the expected output
- Edge case: test with deliberately ambiguous requests to see if Claude correctly decides not to use the skill

**Distribution:**
- Project-level (`.claude/skills/`) — committed to git, shared with team
- User-level (`~/.claude/skills/`) — personal, follows you across projects
- Enterprise-level — deployed via MDM/Group Policy, cannot be overridden by users

> See the existing [[Claude-Code#Skills]] and [[Claude-Code#Skill anatomy]] sections for the full technical detail.

---

## Free Anthropic learning resources

From Usman Writes' "Anthropic Is Giving Away 13 Free Courses That Others Charge Thousands For" (Apr 2026):

**Platform:** [anthropic.skilljar.com](https://anthropic.skilljar.com/) — free account, certificates included, no credit card.

### Developer courses (most relevant)

| Course | What it covers |
|---|---|
| **Building with the Claude API** | 8+ hrs: system prompts, tool use, context window management, streaming, error handling, architecture patterns — the flagship |
| **Introduction to MCP** | Python: build MCP servers/clients from scratch, the 3 core primitives (tools, resources, prompts) |
| **Advanced MCP** | Sampling, notifications, file system access, transport mechanisms, stateless scaling for production |
| **Claude Code in Action** | Context management, custom commands, GitHub automation, hooks, Claude Code SDK — prereq: CLI + basic Git |
| **Introduction to Agent Skills** | Build/configure/distribute Skills in Claude Code; sub-agents, context isolation |
| **Claude with Google Cloud Vertex AI** | Vertex AI integration, enterprise GCP patterns |

### General / non-developer

| Course | Who it's for |
|---|---|
| **Claude 101** | Absolute beginners — prompting fundamentals, daily productivity |
| **AI Fluency: Framework & Foundations** | 4D AI Fluency framework — applies to *any* AI, not just Claude |
| **AI Fluency for Students** | Learning, research, career planning with AI |
| **AI Fluency for Educators** | Teaching with AI, assessment in an AI-assisted world |
| **Teaching AI Fluency** | Formal curriculum design for instructor-led AI literacy programs |
| **AI Fluency for Nonprofits** | Mission-driven constraints, budget-aware AI adoption |

### GitHub open-source courses

[github.com/anthropics/courses](https://github.com/anthropics/courses) — Jupyter notebooks, hands-on, runs locally:

- **Anthropic API Fundamentals** — API keys, model parameters, multimodal, streaming
- **Prompt Engineering Interactive Tutorial** — step-by-step techniques that move the needle
- **Real World Prompting** — complex real-world prompt design (also on AWS Workshop and Vertex)
- **Prompt Evaluations** — writing production evals to measure prompt quality at scale
- **Tool Use** — implementing tool use in Claude workflows

### Recommended learning path (developers)

1. **Building with the Claude API** — foundational
2. **Introduction to MCP** → **Advanced MCP** — integration layer
3. **Claude Code in Action** — daily workflow
4. GitHub: **Prompt Engineering** + **Tool Use** — alongside the above

> "These courses were written by the people who built the systems being taught. The production-level depth reflects real engineering decisions, not a content creator's interpretation of a changelog." — Usman Writes

---

## Claude OS — modular adoption (Rohan Mistry, April 2026)

**Claude OS** is a six-file reference repo at [github.com/rohanmistry231/Claude-OS](https://github.com/rohanmistry231/Claude-OS) catalogues 100+ commands, 25 MCP servers + 10 memory systems, 40+ plugins, 25 frameworks/tools, and 25 production workflows. **Nothing auto-installs** — it is a reference, not a runtime. Every layer is independently adoptable.

### What's in the six files

| File | Contents |
|---|---|
| `commands.md` | 100+ slash commands, CLI flags, keyboard shortcuts, "prompt codes" (activation phrases like `ULTRATHINK`, `BEASTMODE`) |
| `mcp-servers.md` | 25 MCP servers + 10 memory systems with full install configs, tiered Tier-1/2/3 |
| `claude-plugins.md` | 40+ plugins across 6 tiers; role-based starter packs (Developer / Designer / PM / Security / Data / DevOps) |
| `tools.md` | 25 frameworks, vector DBs, deployment platforms; 5 ready-to-use stack combos |
| `workflows.md` | 25 production workflows (Builder-Validator, RAG, multi-agent, code-review loop, etc.) |
| `agent-frameworks.md` | 25 agent frameworks (LangGraph, CrewAI, AutoGen, Claude Agent SDK, LlamaIndex Agents) with 2026 benchmarks + decision guide |

### Author's "do-three-things-today" minimum

1. **Three plugins:**
   ```
   claude skills add juliusbrussee/caveman
   /plugin install superpowers@superpowers-marketplace
   /plugin install context7@claude-plugins-official
   ```
   - **Caveman** (~28k★) — caveman-style responses cut output tokens 65–75% with zero accuracy loss
   - **Superpowers** (~121k★) — forces plan-before-build, test-before-ship
   - **Context7** (~54k★) — pulls live, version-specific docs to kill hallucinated APIs
2. **Memory MCP** — stops every session starting blank:
   ```json
   { "mcpServers": { "memory": { "command": "npx", "args": ["-y", "@modelcontextprotocol/server-memory"] } } }
   ```
   Morning pattern: *"Load project context for [name]. Retrieve: architecture decisions, coding standards, current sprint tasks."*
3. **`/init`** in your project — generates `CLAUDE.md` and persistent memory.

### MCP server tiers (full table)

| Tier | Servers |
|---|---|
| **Tier 1 (install first)** | Filesystem, GitHub, PostgreSQL, Memory, Brave Search, Puppeteer, Fetch, SQLite, Slack, Notion |
| **Tier 2 (enterprise)** | Figma, Linear, Supabase, Redis, Kubernetes, Stripe, Jira, Vercel, MongoDB, AWS |
| **Tier 3** | Gmail, Google Calendar, Discord, GitLab, Docker |

10 memory systems documented including the official Memory MCP, mem0, OmniMem, and Memora (fully local, zero cloud).

### Prompt codes (community-discovered, not in official docs)

Type at the start of any message — these are activation phrases, not slash commands:

| Code | Effect |
|---|---|
| `MEGAPROMPT` | Expands rough idea into a full spec before executing |
| `/autoprompt` | Improves your prompt before running |
| `/rolelock` | Locks Claude into an expert role for the session |
| `BEASTMODE` | Maximum effort, no shortcuts |
| `L99 XRAY` | Maximum reasoning depth + root-cause analysis |
| `ULTRATHINK` | Extended reasoning before any response |
| `STEELMAN` | Argues the strongest version of your idea first |
| `CRITIC MODE` | Finds every flaw before proceeding |
| `SYSTEMS` | Maps full system dependencies before touching code |
| `FIRSTPRINCIPLES` | Reduces problem to fundamentals before solving |

### Underused commands worth knowing

`/remote-control` (control local session from phone via claude.ai) · `/fork` (branch conversation, try risky things safely) · `/usage-report` (HTML analytics: tokens by project, most-used commands) · `/checkpoint` (save state before a major change) · `/scope` (limit Claude to specific dirs/files) · `/memory-dump` (export everything Claude knows about the project) · `/explain-last` · `/replay` · `/diff-review` (annotates every git-diff change) · `/security-scan` · `/perf-profile`.

### Builder-Validator workflow (no framework, two Claude calls, opposing goals)

```python
builder = claude.complete(
    system="Senior developer. Write the best implementation.",
    prompt=task
)
validator = claude.complete(
    system="Security auditor. Find every bug, edge case, vulnerability.",
    prompt=f"Review:\n{builder.output}"
)
# Loop until validator approves
```
Builder maximises quality; validator's only job is to find flaws. Catches more bugs than any plugin because of the goal asymmetry.

### Agent framework decision (with 2026 benchmarks)

| Framework | Benchmark / signal | Use when |
|---|---|---|
| **LangGraph** | 87% task success; used by Klarna, Replit, Uber, LinkedIn | Complex production workflow |
| **CrewAI** | 82% task success; 60M executions/month, 44.6k★ | Fastest to working demo |
| **AutoGen** | #1 GAIA across all difficulty levels | Research + code-executing agents |
| **Claude Agent SDK** | Zero framework overhead | Claude-only stack |
| **LlamaIndex Agents** | Data-heavy retrieval focus | RAG-heavy workloads |

> **Senior-engineer rule:** *"95% of agentic tasks don't need multi-agent systems. A well-prompted single Claude instance with 3 tools will outperform a complex 5-agent setup that nobody understands."* Build simple; add complexity only when simple demonstrably fails.

### Tool-stack quick decisions

- **RAG-heavy?** LlamaIndex (else LangChain)
- **Production memory?** Qdrant (else Chroma for local dev — `pip install`, zero setup)
- **Backend in one place?** Supabase
- **Local embeddings?** Ollama (`curl -fsSL https://ollama.com/install.sh | sh && ollama pull llama3.2`) — use for embeddings + simple tasks, keep Claude for reasoning that needs it.

### Reported production numbers (per article)

- Fountain — 50% faster delivery
- Rakuten — feature delivery 24 days → 5 days
- Ramp — incident investigation cut 80%

### Modular-adoption discipline

Each MCP server costs context tokens at session start; unused plugins clutter `/plugin list`. Pick the smallest set that solves a current pain. The repo is *forkable reference*, not a framework — copy only the entries you'll use this week.

> See also [[#MCP — Model Context Protocol]] for MCP architecture · [[#Skills]] for the Skills system · [[#CLAUDE.md — real-world patterns]] for `/init` outputs · [[#Cost reduction strategies]] for token-budget tactics.

---

## DESIGN.md — the visual counterpart to CLAUDE.md

On 2026-04-21 Google open-sourced the `DESIGN.md` spec (Apache 2.0) — a markdown file describing a design system in a way LLMs read natively. VoltAgent's `awesome-design-md` collection of 423 brand systems (Stripe, Linear, Notion, Vercel, Airbnb, Spotify…) hit 70k GitHub stars in five weeks.

**Why it exists:** AI agents are excellent at code but terrible at *staying visually consistent across pages*. Three failure modes the file prevents:

| Failure mode | What it looks like |
|---|---|
| **Bootstrap Default** | Generic white + blue Tailwind look; brand disappears |
| **Color Roulette** | Same red is decorative on one page, error indicator on the next |
| **Style Drift** | Same prompt produces rounded corners Monday, squares Tuesday |

**Place** `DESIGN.md` in project root next to `CLAUDE.md`. The agent picks it up automatically.

### The 9 sections (grouped into three clusters)

| Cluster | Section | What it fixes |
|---|---|---|
| Foundation | 1. Visual Theme & Atmosphere | Sets the *feel* — "technical and luxurious, precise and warm" |
| Foundation | 2. Color Palette & Roles | Kills Color Roulette — colors assigned *semantic roles* ("Blurple for primary actions and trust signals, never decorative") |
| Foundation | 3. Typography Rules | Font families, 14+ size levels, weights, OpenType features |
| Components | 4. Component Stylings | Button states (hover, active, disabled, loading), card specs, inputs, badges |
| Components | 5. Layout Principles | Base spacing unit (Stripe = 8px), grid, max width, radius scale |
| Components | 6. Depth & Elevation | Five-level shadow system, blue-tinted not neutral gray |
| Guardrails | 7. Do's and Don'ts | Anti-hallucination layer ("never use border-radius above 8px", "never pill-shaped buttons") |
| Guardrails | 8. Responsive Behavior | Breakpoints + per-component collapsing strategies + touch-target rules |
| Guardrails | 9. Agent Prompt Guide | Quick-reference codes + ready-to-use prompts — fixes the cold-start problem |

### Three setup paths (all under 15 minutes)
- **A. Grab a brand:** `curl -o DESIGN.md https://raw.githubusercontent.com/VoltAgent/awesome-design-md/main/design-md/stripe.com/DESIGN.md`
- **B. Generate from your site:** [Google Stitch](https://stitch.withgoogle.com) extracts a DESIGN.md from any URL
- **C. Write your own:** start with just 4 sections — Theme, Colors, Component Stylings, Do's/Don'ts

### Honest limits
- **Token cost:** a full DESIGN.md is ~30k tokens per query. Trim to the 4 essentials and keep the rest as on-demand reference for large projects.
- **No runtime enforcement:** agent *tries* to follow it; add CSS linting for hard guarantees.
- **Stale snapshots:** no auto-sync with live CSS — manual updates lag.
- **Markdown vs JSON tokens:** JSON design tokens are ~80% cheaper but lose intent. The whole point of DESIGN.md is the *prose* ("never decorative") that JSON can't carry.

The deeper insight: **the bottleneck in AI-assisted UI was never the model's ability to write CSS — it was design context.** Same pattern as [[#CLAUDE.md — real-world patterns]] for code.

> Source: Yanli Liu — "The 9 Sections Every DESIGN.md Needs" (Generative AI, 2026-05-06, 622 claps).

---

## HTML vs Markdown for agent output

On 2026-05-17 Anthropic's Claude Code engineering lead told developers to stop outputting Markdown — the post hit 4.4M views in 16 hours. Yanli Liu's response: **format should follow reader, not faction.**

### The token math
For a 2000-word report (current API pricing):

| Format | Tokens (relative) |
|---|---|
| Plain Markdown | 1× |
| Lean semantic HTML | ~2× |
| Full HTML + CSS + embedded SVG | ~3–5× |

At individual scale the cost gap is pennies; at enterprise scale (thousands of reports/day) HTML burns real money.

### The decision tree — who reads the output?

| Reader | Use |
|---|---|
| **Another agent** (chained pipelines, structured tool I/O) | Markdown — cheapest, most parseable |
| **Human, skim-and-go** (status updates, summaries) | Markdown — fast scan |
| **Human, navigate and act** (dashboards, code reviews with severity colors, design specs with live swatches) | HTML — collapsible sections, color coding, interactivity |

> "A Markdown file you scroll past is a file that doesn't exist."

### Risks Team Markdown was right about
- **Security:** AI-generated JavaScript in HTML output is an exfiltration vector
- **Noisy diffs:** HTML breaks code review
- **Token cost:** real at enterprise scale

**Practical wiring:** add a routing rule in your CLAUDE.md — "for outputs another agent consumes: Markdown. For outputs a human will *read and act on*: HTML. For everything else: Markdown."

> Source: Yanli Liu — "Anthropic's Engineer Said Kill Markdown. Here's What He Actually Meant." (Generative AI, 2026-05-13).

---

## CLAUDE.md best practices — 2026 refresh

Reinforces what [[#CLAUDE.md — real-world patterns]] already says, with sharper rules:

**The ruthless filter** — for every line:
> "Would removing this cause Claude to make mistakes?"
> If no → delete it.

**Targets:**
- Under **300 lines** total — shorter is better
- Three-layer mental model: project context → coding/style rules → workflow conventions
- Use `/init` as the starting point, then prune

**What to keep:**
- Build, test, lint commands the agent should default to
- Project-specific naming conventions and file layout
- Non-obvious constraints (e.g., "never run migrations against the prod replica")
- Pointers to deeper docs the agent should fetch on demand

**What to cut:**
- Self-evident practices ("write clean code", "add comments")
- Instructions Claude already follows correctly without being told
- Overly specific schema/database details unrelated to most tasks

**Sub-CLAUDE.md for monorepos:** drop a CLAUDE.md in each package root — Claude composes them with the root file.

### CLAUDE.md vs Skills (when to use which)

| Aspect | CLAUDE.md | Skills (SKILL.md) |
|---|---|---|
| When loaded | Every session, automatically | On demand or when relevant |
| Best for | Universal project rules | Occasional specialized workflows |
| Risk of overuse | Bloats context, buries key rules | Minimal — loaded selectively |
| Example content | Code style, build commands | Database migration guide, deployment runbook |

**Debug signal:** if Claude keeps making the same mistake despite a rule being in CLAUDE.md, the file is probably too long and the rule is being diluted — trim before adding more.

> Source: IAKH Studio — "CLAUDE.md Best Practices: The Complete Guide for 2026".

---

## Token savings — quick wins

Captured from May 2026 digests:

- **One setting saves up to 85% of Claude tokens** (Dan Avila, 2026-05-17) — worth auditing your current setup against. Common culprits: oversized CLAUDE.md, unused MCP servers loading at session start, full file reads when grep would do.
- **Cheaper backends:** Claude Code can be wired to Ollama + OpenRouter for ~99% cost reduction on routine tasks (Kram254, 2026-05-13).
- **CLI alternatives that have converged:** Codex CLI, Gemini CLI, OpenCode now share most of Claude Code's UX — pick by underlying model cost/quality, not feature gap (Richard Hightower, 2026-05-17).

---

## Beyond the basics — the 80% most tutorials skip

Most Claude Code guides cover the same five things: CLAUDE.md, Plan Mode, Context7, parallel sessions, `/clear` between tasks. Past those, there's a smaller set of habits that compound quietly into a different speed of working.

### CLAUDE.md as memory, not documentation

Run `/init` once and most developers never open the file again. The point is the opposite: end every meaningful session with **"Update CLAUDE.md with everything important you learned today."** After a month the file becomes the onboarding document you always meant to write. Senior engineers reading it find things they didn't know about their own codebase.

Do not `.gitignore` it — share it. Teammates' sessions get smarter when yours does.

### `/btw` — non-blocking side queries

Claude Code introduced `/btw` ("by the way") to handle the mid-task interrupt cleanly. Mid-refactor you suddenly need to know something unrelated; before `/btw` you either broke Claude's momentum or opened a fresh session and lost the context. Now: type `/btw`, an overlay appears, you ask, Claude answers, you close it, Claude continues exactly where it was.

Not just convenience — it's state preservation. Interrupting the main thread forces re-parsing and increases context drift. Non-blocking queries keep the primary execution trace clean.

### `/sandbox` — quiet the permission firehose

Every new file, every novel command, every slightly out-of-scope tool call triggers a permission prompt. Over a two-hour session it's a dripping faucet of interruptions that erodes flow.

`/sandbox` applies file and network isolation; teams using it report ~**84% fewer permission prompts**. Use it in trusted projects you know well. Keep full prompting for production-facing or externally connected work where the friction is the point. Isolation isn't about removing guardrails — it's about applying them predictably.

### Claude in Chrome — close the feedback loop

The most common waste pattern: Claude builds UI, you run it, something's slightly off, you describe the wrongness in text, Claude guesses, repeat. You're acting as Claude's eyes.

The Chrome extension makes Claude do its own visual verification: build → open in Chrome → snapshot → catch errors → fix — before you've looked up from what you were doing. Shifts the loop from *describe → guess → iterate* to *generate → verify → auto-correct*. In beta on paid plans.

### Plan Mode as a design review, not a loading screen

Approving the plan is the least useful thing you can do with Plan Mode. The plan is the **starting point of an argument**. When Claude produces a plan, find something to disagree with — a step that assumes a pattern that doesn't fit your codebase, an edge case it ignored, a misframed problem. Push back; let Claude revise. The final plan beats both what you'd have designed alone and what Claude would have designed unchallenged.

Useful prompt when reviewing a plan:

> "Argue against this plan. Where is it weakest? What edge case would break step N? What pattern in our codebase contradicts step M?"

### Writer / reviewer split across two sessions

Run two Claude Code sessions in parallel — one writes, one reviews. The instance that just wrote the code is the worst possible reviewer of it: it's attached to its own decisions and rationalises edge cases instead of surfacing them. A fresh instance with no commitment reads the code like a skeptical colleague.

Push further: one Claude writes failing tests (committed before any implementation), a completely separate Claude writes code to pass them. Neither knows what the other is optimising for. Test quality and implementation quality both go up — this is what test-driven development was always supposed to be.

### The kitchen-sink session — the silent killer

The "Claude gets weird in long sessions" complaint almost always traces to a kitchen-sink session: you started on one task, asked something unrelated mid-stream, got distracted, came back, and an hour later four half-solved problems are crowding the context window.

Rule of thumb: **run `/clear` when topic, dependency scope, or risk profile changes.** Treat it like closing a terminal tab and opening a fresh one. Two seconds. Never seen an exception.

> Source: Addepalle Nikhil Varma — "You Are Using Claude Code at 20% of Its Power. Here Is the Other 80%", Towards AI, 2026-05.

---

## CLAUDE.md teardown — 10 rules that prevent the common failures

Distilled rules that fall into three groups. Treat CLAUDE.md as the **permanent DNA of the project** — Claude reads it at session start; if it's missing, the agent defaults to whatever architecture it has seen most often in training data.

**Execution protocols (1–4):**

1. **Lock in the exact stack.** Without it, Claude suggests incompatible abstractions. List language version, framework version, package manager, test runner.
2. **Scope guard.** Forbid unrequested refactors. Without this, a request to fix one DB query becomes a re-import-and-rename sweep that breaks unrelated code.
3. **Hard wall around destructive actions.** Claude has raw filesystem access. Explicitly list commands that require confirmation: `rm -rf`, DB drops, force pushes, prod migrations.
4. **Cut conversational filler.** Ban phrases like "Great question!", "Certainly, I can help with that!". Cognitive friction compounds across hundreds of iterations.

**Persistent memory architecture (5–7):**

5. **External state log.** Force the agent to maintain architectural decisions in a file; otherwise it forgets between sessions.
6. **Break hallucination loops.** Record approaches that failed and why, so Tuesday's failure isn't repeated on Friday.
7. **Session checkpoints.** Force a "state of play" write before context fills up, so you can resume after closing the laptop.

**Output constraints — the Karpathy rules (8–10):**

8. **Don't over-engineer trivial problems.** Match solution complexity to problem complexity.
9. **Forbid API hallucination.** Make the agent verify a library method exists before calling it; default behaviour is to invent plausible-sounding methods rather than admit ignorance.
10. **Force a summary after multi-file refactors.** You should not have to manually hunt down what changed.

> Source: Shashwat — "10 Rules Claude Users and Devs Forget", Tech and AI Guild, 2026-05.
> See also [[#CLAUDE.md best practices (consolidated)]] above for the size/layering guidance.

---

## CLAUDE.md token budget — fixing the 8K-token file

A 340-line CLAUDE.md can consume **~8,000 tokens at session start, before any prompt is typed** — roughly 20% of the context window gone to documentation that loads regardless of what you're doing this session. Most of it is content the file does not need to carry.

**Context budget breakdown** at session start (approximate):

| Component | Tokens |
|---|---|
| System prompt + CLAUDE.md files | 2–5K |
| Auto memory (`MEMORY.md`) | 1–3K |
| Conversation history | grows from there |
| Tool outputs (file reads, bash output) | 10–20K per large file read |

**Context rot is real.** More context isn't automatically better — accuracy and recall degrade as token count grows. Curating *what* is in context matters as much as *how much* space is available.

### Degradation curve (rules of thumb)

| Fill | Effect |
|---|---|
| < 70% | Normal |
| 70% | Precision begins degrading |
| 70–90% | Run `/compact` proactively, ideally with a focus hint |
| 85%+ | Hallucinations noticeably increase (referencing renamed files, suggesting ruled-out approaches) |
| 90%+ | `/clear` as last resort |

By the time responses *feel* slightly off, you're probably already past 70%.

### Audit and prune — what to keep in the root file

Hard target: **< 200 lines** (official guidance) — longer files dilute adherence as well as cost tokens. A 50-line core works fine for most projects.

**Keep in the root CLAUDE.md** (loads every session, regardless of task):
- Build, test, run, lint commands
- Naming conventions and high-level project layout
- "Always do X" rules Claude must hold in every session
- Non-obvious constraints (e.g. "never run migrations against the prod replica")

**Move out of the root** — these are paying for themselves once and reloading every time:
- Architecture explanations Claude can infer from directory structure
- Git commit conventions you've only had to correct once in months
- Onboarding context written for a hypothetical new engineer
- Deployment checklists that only matter when you're actually deploying
- Multi-step procedures that only matter for one part of the codebase

### Path-scoped rules

Put a `CLAUDE.md` in subdirectories — `src/auth/CLAUDE.md`, `migrations/CLAUDE.md`. These load **only when Claude reads files in that directory**, so architectural notes load precisely when they're relevant and never otherwise. Slice-specific rules belong here, not in the root.

### Auto memory carries the learning

Two mechanisms persist knowledge across sessions:
- **CLAUDE.md** — instructions *you* write for persistent context
- **MEMORY.md / auto memory** — notes Claude writes itself based on your corrections and preferences

Auto memory loads the first 200 lines / 25KB of `MEMORY.md` at session start — still a budget to watch, but you're not the one writing it by hand. Let the file you maintain shrink as auto memory absorbs the corrections.

### `/compact` with a focus hint

When the bar climbs into the 70–90% band, compact with intent instead of clearing blindly:

```
/compact Focus on the auth migration plan and the database schema changes.
```

This preserves the work that matters and discards the file-read noise that ate the budget.

### Two questions before adding a line to CLAUDE.md

- "Will Claude need this in **every** session, or only when working in *one* part of the codebase?"
- "If I delete this line, will Claude make a mistake within the next week?"

Lines that fail either test belong in a path-scoped file, a skill, or `MEMORY.md` — not the root.

> Source: Adi Insights and Innovations — "My CLAUDE.md Was Eating 8,000 Tokens. Here's How I Fixed It.", Towards AI, 2026-05. Reduction reported: 340 lines → 47 lines, ~87% token drop on the root file.

---

## Default settings to audit (the 2026 "nerf" workarounds)

Anthropic silently changed several Claude Code defaults in early 2026 that made the agent feel weaker on complex tasks. The model is the same; the configuration changed. A handful of settings reverse most of the regression:

- **Force deep thinking** — Claude now auto-decides per turn how much compute to allocate. On tasks it tags as "easy," it bypasses the thinking step entirely. Force the higher reasoning budget for complex tasks rather than relying on the dynamic default.
- **Permission architecture** — the default install confirms nearly every tool call (one developer logged 47 prompts in a morning). `/sandbox` and a curated allowlist in `.claude/settings.json` collapse this to a handful of genuine decisions. (See [[#`/sandbox` — quiet the permission firehose]].)
- **Model routing** — Opus on a regex question burns ~5× the Sonnet cost. Route by task complexity, not by default model.
- **MCP server load cost** — each connected MCP server adds **~18K tokens of overhead per turn**. Five idle servers = ~90K tokens consumed before you type. Disconnect MCP servers you aren't actively using this session.
- **Filesystem hooks** — if you manually run Prettier / Black / formatters after Claude writes code, your loop is wasting turns. Wire formatters as a `PostToolUse` hook in `.claude/settings.json` so they run automatically on file write.
- **Cap large file reads** — telling Claude to read a 10K-line server log consumes massive context and degrades reasoning. Grep first, read targeted line ranges second.

> Source: Shashwat — "10 Claude Code Settings You Need to Fix Today", Tech and AI Guild, 2026-05. Thematic complement to [[#CLAUDE.md token budget]] above — the same "your environment is the problem, not the model" framing.

---

## Under the hood — what the April 2026 source-map leak revealed

In April 2026 a sourcemap shipped accidentally inside an npm release of `@anthropic-ai/claude-code` (Anthropic confirmed it was a packaging mistake, not a security incident; the package was pulled within hours but mirrors had cloned it). The leak doesn't reveal a model or fine-tuning data — only the **client harness** — but the harness is exactly the part that explains why long sessions feel the way they do. Mental model for what's running on your machine:

### 8 compaction layers (in firing order)

Long agent conversations hit token limits constantly: file reads, bash output, error traces. Naive "summarise the whole thing" rewrites the cached prefix and invalidates the prompt cache, so latency and cost blow up. The harness avoids that with a cascade of cheaper mechanisms that fire **before** real auto-compact is needed:

1. **Pre-flight trim** — drop the cheapest stale entries before each turn
2. **Cached microcompact** — replay a prior summary if signals are unchanged
3. **Time-based microcompact** — invalidate older transcript chunks on a timer
4. **Tool-output truncation** — clip oversized bash/read outputs aggressively
5. **Post-flight trim** — same as 1, after the turn, if the budget is tight
6. **Cache rotation** — re-anchor the cache prefix so the next turn hits cache
7. **Auto-compact** — fork a subagent to write a 9-section summary; the subagent inherits the parent's cache key so the prefix-shared context is near-zero cost
8. **Manual `/clear` / `/compact`** — last resort when the cascade exhausts

The dispatcher counts only **auto-compact** failures toward the circuit breaker (3 → give up). The cheaper mechanisms are allowed to throw without permanently crippling the session.

**Why the 9 sections matter**: the auto-compact subagent emits exactly 9 fixed sections. The parser **fails closed** — 8 sections is treated as a bug, not "good enough" with empty defaults. That's why a compacted session sometimes errors instead of silently dropping context.

### 3 memory tiers

Anthropic explicitly rejected vector databases for memory — vector retrieval is opaque, needs an embedding model per read, and quietly rewards recency. Files, grep, and a markdown index preserve structure as long as you keep them.

| Tier | Scope | Backed by |
|---|---|---|
| **1 — Conversation state** | This turn / this session | In-memory transcript + cache |
| **2 — Session memory** | Carry-over across sessions | `MEMORY.md` + auto-memory edits |
| **3 — Project memory** | Permanent project context | `CLAUDE.md` files with strict precedence: most-specific file wins (`subdir/CLAUDE.md` > `CLAUDE.md` > `~/.claude/CLAUDE.md`) |

The "most-specific wins" rule is what makes [[#Path-scoped rules|path-scoped CLAUDE.md files]] work — rules in `src/auth/CLAUDE.md` override the root file when Claude is reading files under that directory.

### Hidden flags & internal codenames

The harness ships with **44+ feature flags**, many never documented:

- **Already-active flags** sit in the source and run on every install — they're how A/B-style behaviour changes (the "March 2026 nerf") roll out without a changelog. Surfacing them is a manual codebase audit.
- **Not-yet-wired flags** exist but aren't routed into the customer build — preview rails for features Anthropic is staging.

**Internal model codenames** that leaked: **Tengu** = Claude Code itself. **Capybara** = the Mythos variant. **Fennec** = Opus 4.6. **Numbat** = an unreleased model still in testing. These show up in source-level routing logic.

### Why this matters for builders

The 8 compaction layers, the 3-tier memory split, and the flag-driven behaviour exist because of the *shape of the agent problem*, not the specific model. Anthropic could swap the underlying model tomorrow and the harness would still do the same 8 things, store across the same 3 tiers, ship behind the same kinds of flags. If you're building an agent harness yourself, this is a preview of where your stack ends up by 2027.

> Source: Anubhav — "Inside Claude Code's Leak: 8 Compaction Modes, 3 Memory Tiers, 44 Flags Anthropic Never Talked About", Data Science Collective, 2026-05.

---

## dotnet-claude-kit

Open-source plugin by Mukesh Murugan that turns Claude Code into a **.NET 10 / C# 14 expert**. See [[CSharp#dotnet-claude-kit]] for full installation guide and commands.

**Architecture (4 layers):**
```
You type a slash command or chat
↓
Commands (16) orchestrate workflows
↓
Agents (10 specialists) + Skills (47 playbooks) + Rules (10 always-on)
↓
Roslyn MCP (15 tools) reads your solution like the compiler, not like grep
```

**Install (3 steps):**
1. `dotnet tool install -g CWM.RoslynNavigator` — installs the Roslyn MCP server
2. In Claude Code: `/plugin marketplace add codewithmukesh/dotnet-claude-kit` then `/plugin install dotnet-claude-kit`
3. `cd` into your .NET solution → `/dotnet-init` — generates project-specific CLAUDE.md

**Why the Roslyn MCP matters:** without it, Claude reads entire `.cs` files (hundreds/thousands of tokens). With Roslyn Navigator it asks structured questions: "Where is `OrderService` defined?", "Who calls `CreateOrder`?", "Any compiler diagnostics?" — faster answers, lower token use on large solutions.

> Source: Maulik Patel — "dotnet-claude-kit: make Claude Code a .NET 10 expert" (2026-05-27)  
> GitHub: `github.com/codewithmukesh/dotnet-claude-kit`

---

## CLAUDE.md as a behavioral API — 10-pattern framework

From Mouez Yazidi's "Mastering CLAUDE.md: 10 Proven Ways to Reduce Claude Code Drift" (Towards AI, May 2026, 223 claps):

The root insight: **CLAUDE.md is not documentation — it's the control plane.** Treat it like a behavioral API: tight at the top, strict in the middle, outcome-focused, versioned.

| # | Pattern | Key rule |
|---|---|---|
| 1 | **Under 200 lines** | Attention decays. Review monthly, delete ~30% of rules. If a rule hasn't triggered in weeks, move to a reference file. |
| 2 | **First 30 lines set the lens** | Opening tokens set the latent frame for everything that follows. Stack identity + non-negotiables at the top. |
| 3 | **Hard rules vs preferences** | Hard rules use `never`/`always`/`must`. Preferences use `prefer`/`avoid`/`lean toward`. Never mix them — LLMs flatten ambiguity. |
| 4 | **Anti-patterns over instructions** | Negative constraints are where reliability lives. Document what consistently breaks, not just what you want. |
| 5 | **Success criteria are testable** | Rules tell Claude how to work; success criteria tell it what to optimize for. If it can't be a CI assertion, it's not precise enough. |
| 6 | **Progressive disclosure** | CLAUDE.md as routing layer — `When doing X → load @path/to/detail.md`. Don't force the model to load all context for every task. |
| 7 | **Scoped files** | Drop `CLAUDE.md` in subdirectories. Nearest-scope wins. Retrieval team's rules shouldn't be in the API team's context. |
| 8 | **Fallback protocols** | Define structured failure explicitly: `if confidence < 0.75 → return {"status": "insufficient_context"}`. Hallucinations are unbounded confidence. |
| 9 | **Role activation** | 3–4 roles tied to concrete disciplines: *Retrieval logic → act as Search Engineer*, *Agent graphs → act as Systems Architect*. |
| 10 | **Version it** | Changelog + `@deprecated` tags. Stale rules actively mislead. Tie to release cycle. Add a CI check that flags deprecated patterns in generated code. |

```markdown
# Project | Context v3.2 | Updated: YYYY-MM-DD
One-line system description.

## Non-Negotiables
- Hard constraint 1 (never/always)

## Hard Rules
- never return raw LLM strings; always wrap in validated models

## Preferences
- prefer async-first over sync

## Anti-Patterns
- never hardcode embedding model versions; read from env/config

## Success Criteria
Production-ready must:
- pass all CI checks with zero false-positive citations

## Fallback Protocols
- if confidence < 0.75 → return {"status": "insufficient_context"}

## Role Activation
- Retrieval logic → Search Engineer
- API endpoints → Backend Engineer

## Context Routing
- Building agent nodes → @agents/node-patterns.md

## @deprecated
- `old_module.py` → replaced by `new_module.py`
```

---

## Top Claude Code plugins (2026 ecosystem)

From IAKH Studio — "10 Claude Code Plugins You Must Install" (May 2026). 9,000+ plugins now available across ClaudePluginHub, Claude-Plugins.dev, and Anthropic Marketplace.

| Plugin                  | Installs | What it does                                                                                                                             | Install                                              |
| ----------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| **Frontend Design**     | 96.4K    | Fights generic AI-looking UIs — bolder typography, distinctive aesthetics, no default Inter/purple gradients                             | `/skill install frontend-design@anthropic`           |
| **Context7**            | 71.8K    | Injects live version-accurate docs into context — kills hallucinated APIs on fast-moving frameworks                                      | `/plugin install context7@context7`                  |
| **Ralph Loop**          | 57K      | Stop-hook pattern for autonomous multi-task sessions — write PRD → Claude implements, commits, repeats until done                        | `/plugin install ralph-loop@claude-plugins-official` |
| **Code Review**         | 50K      | Multi-agent PR review in parallel across tests/types/error handling/quality — findings include confidence scores                         | `/plugin install code-review@anthropic`              |
| **Playwright MCP**      | 28.1K    | Natural language browser testing — drive real Chrome, visual verification without writing test scripts                                   | `/plugin install playwright@microsoft`               |
| **Security Guidance**   | 25.5K    | Scans every file edit *before* it happens for 9 vulnerability patterns (injection, XSS, eval, pickle, etc.) — blocks with fix suggestion | `/plugin install security-guidance@anthropic`        |
| **Chrome DevTools MCP** | 20K      | Full live browser state (network, console, performance) from your existing logged-in Chrome session                                      | `/plugin install chrome-devtools@chrome`             |
| **Figma MCP**           | 18.1K    | Direct read access to Figma files (not screenshots) — reads frames, components, layout data                                              | `/plugin install figma@figma`                        |
| **Linear**              | 9.5K     | Pull tickets, plan work, update issue status without leaving terminal                                                                    | `/plugin install linear@linear`                      |
| **Firecrawl**           | —        | Web scraping/crawling — renders JS, returns clean markdown/JSON for AI agents                                                            | `/plugin install firecrawl@firecrawl-dev`            |

**Starter 3 (Claude Code's biggest gaps):**
1. **Security Guidance** — install first, always
2. **Context7 or Firecrawl** — live docs or web data
3. **Memory system** (MemClaw, Memory MCP, or similar) — persistence across sessions

> Plugin context cost: each connected plugin adds tool definitions to Claude's context window. Install only what you actively use; audit with `/plugin list`.

---

## Memory architecture — 4 layers

From Faisal Haque — "Give Claude Permanent Memory" (AI in Plain English, May 2026, 219 claps):

Anthropic's rolling 2025–2026 release built a 4-layer memory system. Each layer trades control for convenience:

| Layer | Available to | How it works | Control |
|---|---|---|---|
| **Chat Memory Synthesis** | All users | Auto 24hr synthesis of conversation history → injected into new sessions. Explicitly say "Remember that I prefer X" to update immediately. | Automatic |
| **Project Memory Spaces** | Pro/Max/Team/Enterprise | Each Claude Project has its own isolated memory — separate from global chat memory. Past-chat search available. | Scoped |
| **CLAUDE.md file-based** | Claude Code users | Loaded unconditionally at session start — no retrieval delay. Hierarchy: global → project → directory (most specific wins). Auto Memory (v2.1.59+) lets Claude write its own notes. | Manual/hybrid |
| **API Memory Tool** | API/developers | Client-side R/W filesystem at `/memories`. Claude makes tool calls (create/read/update/delete) that your app executes locally. Full control: you own storage and schema. | Programmatic |

**API Memory Tool pattern (Python):**
```python
memory_tool = {
    "name": "memory",
    "description": "Read and write persistent memory files",
    "input_schema": {
        "type": "object",
        "properties": {
            "command": {"type": "string", "enum": ["read", "create", "str_replace", "insert", "delete"]},
            "path": {"type": "string"},
            "file_text": {"type": "string"},
        },
        "required": ["command", "path"]
    }
}
# Bootstrap: load notes.md as system context; update on session end
# Pair with context compaction for long-running agents
```

**Structural limits:**
- Project memory and standalone chat memory are **siloed** — pick one home per workflow
- Memory captures preferences/facts, not decisions/reasoning chains — use CLAUDE.md or API Memory Tool for architecture decisions
- Chat search is paywalled on free tier (synthesis available; retrieval requires Pro+)
- Memory doesn't cross AI providers (import/export to ChatGPT/Gemini now available)

**Action plan:**
- Casual user: `Settings → Capabilities → enable memory`. Tell Claude 5 things in your next session.
- Claude Code: create `~/.claude/CLAUDE.md` (global), keep under 200 lines. Run `/memory` to audit auto-accumulated notes.
- API developer: scaffold `/memories/progress.md` + `/memories/preferences.md`; update on session end; pair with context compaction.

---

## Agentic loop — three phases

The Claude Code context window is the operating system. Every command, mode, skill, subagent, and hook is a tool for managing what lives in that window at a given moment.

**Three phases, repeated until done:**
1. **Gather context** — read files, search, ask questions
2. **Take action** — write code, run commands, edit files
3. **Verify** — run tests, check linter, screenshot UI ← *this is the phase most people skip*

> "Giving Claude a way to verify its work is the single highest-leverage thing you can do." — Anthropic internal teams

**Verifier progression (start simple, add as needed):**
- Unit tests (`pytest`, `vitest`) — cheapest universal verifier
- Type checker + linter (`mypy --strict`, `tsc --noEmit`, `ruff`, `eslint`)
- Browser MCP — visual verification for UI
- Logs via MCP or `tail -f` — behavior verification for services

The cheapest version: add one line to every prompt: `"After making changes, run [test command] and show me the results."`

**Context budget at session start (~8K tokens before first prompt):**

| Component | Tokens (approx) |
|---|---|
| Auto memory (`MEMORY.md`, first 200 lines) | ~680 |
| Global `CLAUDE.md` | ~320 |
| Project `CLAUDE.md` | ~1,800 |
| System prompt + skills | ~3,000–5,000 |

Each active MCP server adds ~18K tokens overhead. Five idle servers = ~90K tokens burned before you type.

**Per-turn cost:** reading a typical source file (~2,400 tokens) + imports + test file + hook output + `npm test` result = ~9,000 tokens per turn. At 200K context, 10 turns gets you to ~50%.

**Diagnostic commands:**
- `/context` — shows everything in the window broken down by category with token counts
- `/memory` — shows what CLAUDE.md and rules actually loaded (diagnosis for "Claude ignores my instructions")

> Source: Rick Hightower — "Your AI Coding Agent Forgets Things. Manage the Context Window to 10x Results" (Towards AI, May 2026, 144 claps)

---

## Context management tooling (open-source)

A bigger context window isn't the fix — **context *quality* beats context *size***. The failure mode (Milvus calls it "context defocus") is high-signal information getting buried under low-signal noise: terminal logs, verbose tool output, repeated file reads. Seven open-source tools attack this at different layers:

| Tool | Layer | What it does |
|---|---|---|
| **RTK** | Noise reduction | CLI proxy that rewrites shell commands through hooks and returns only essential output — 60–90% token reduction on command results |
| **Context Mode** | Noise reduction | Isolates large tool outputs (test logs, DOM snapshots, API payloads) into a local SQLite/FTS sandbox; passes only summaries to Claude |
| **code-review-graph** | Repo navigation | Tree-sitter pre-builds a structural map (functions, classes, deps, test relationships) in SQLite so Claude queries structure instead of re-reading files |
| **claude-context** | Repo navigation | Chunks the repo, embeds it, stores in Milvus for semantic code retrieval via MCP — targeted search replaces blind file exploration |
| **Token Savior** | Bloat control | MCP server that sends code in layers — symbol summaries first, full files only when needed (mirrors how developers actually read code) |
| **Caveman** | Bloat control | Skill that strips filler/over-explanation from responses so future turns inherit less bloat |
| **memsearch** | Memory | Stores decisions/context as human-readable Markdown indexed in Milvus — cross-session memory without re-explanation |
| **Headroom** | Noise reduction | Open-source context *compressor* (by Netflix engineer Tejas Chopra) — strips low-value bytes (verbose API/JSON responses, stack traces) the model would otherwise re-reason over every turn. Matters because output costs ~5× input and each turn re-sends the whole transcript. Claimed 60–95% fewer tokens with Claude Code/Codex. |

**Recommended adoption order:** noise reduction (RTK / Context Mode) → repo navigation (code-review-graph / claude-context) → bloat control (Token Savior / Caveman) → institutional memory (memsearch). Note this overlaps the [[AI-Agents]] memory-architecture debate — `memsearch` and `claude-context` are the embed-and-retrieve camp; the Markdown-file memory pattern is the no-infra alternative.

> Source: Milvus — "7 Best Open-Source Tools for Claude Code Context Management" (2026-06-19, 10 min, 67 claps)

---

## CodeGraph — a queryable knowledge graph of your codebase (June 2026)

[CodeGraph](https://github.com/colbymchenry/codegraph) (MIT, ~32k stars) attacks the same "exploration tax" as the repo-navigation tools above, but with an **explicit call graph** rather than embeddings. The insight: most agent tokens aren't spent reasoning — they're spent *finding* where relevant code lives (reading dozens of files, tracing imports). CodeGraph pre-builds a map so the model gets answers, not leads.

**How it works:**
- **tree-sitter** parses every source file into ASTs (fault-tolerant — produces partial ASTs even with syntax errors, and fast enough for tens of thousands of files).
- Language queries extract **symbols** (functions, classes, methods, interfaces) and **edges** (calls, imports, inheritance, implementations) into a local **SQLite** file (`.codegraph/codegraph.db`) with FTS5 search.
- Exposed to agents via a single **MCP** tool call ("what calls this function?", "trace the impact of changing this module?") — no file-by-file exploration, no agent loops.
- Installer auto-detects and configures Claude Code, Cursor, Codex CLI, OpenCode, Hermes Agent, Gemini CLI, Antigravity, Kiro.

**vs Cursor's indexing:** Cursor uses vector embeddings → *probabilistic* similarity-ranked leads the AI must then verify by reading files. CodeGraph returns *deterministic* structural relationships → definitive answers. **vs Gemini Code Assist / Sourcegraph / Copilot:** those are cloud-hosted or server-heavy; CodeGraph is local-first, zero-config, no data leaves the machine.

**Benchmark** (Claude Opus 4.7, `claude -p` headless, 7 projects/7 languages, only variable = graph available): avg **57% fewer tokens, 71% fewer tool calls, 46% faster, 35% lower cost**. Savings scale with codebase size — VS Code's ~10k-file TS monorepo dropped 2.8M→601k tokens (78%); Excalidraw 90%. Rust benefits disproportionately (Tokio 82% cost cut — its `mod`/`use`/`pub use` re-export hierarchies are punishing to traverse). Small projects see less (OkHttp 13%, Gin 34%).

**Other features:** native route recognition across 14 web frameworks (annotation/decorator/DSL/file-convention) — maps `POST /api/users` straight to the handler through middleware; three-layer auto-sync (OS file watchers → 2s debounced batching → staleness flags + content-hash reconciliation) keeps the index current with zero "rebuild" step. Install: `curl -fsSL .../install.sh | sh` then `codegraph init -i`. Languages: TS, Python, Rust, Java, Go, Swift, Kotlin, C/C++, C#, Ruby, PHP, Dart, Svelte, Vue.

**Limitation:** it's an *enhancer, not a replacement* — it cuts exploration cost but contributes nothing to reasoning-heavy tasks ("why is this query slow?"). The broader thesis: the next frontier in AI coding may be **engineering efficiency (a map), not model intelligence**. See the embed-vs-graph debate in [[AI-Agents]] and the existing `code-review-graph` / `claude-context` entries above.

> Source: KD Agentic — "CodeGraph: The Open-Source Knowledge Graph That Makes AI Coding Tools Dramatically Cheaper" (2026-06-24, 8 min, 60 claps)

---

## Claude HUD — status bar enhancement plugin (June 2026)

**Claude HUD** is a Claude Code plugin that surfaces what the agent is doing *without typing a command*. It renders two status lines right below the input field showing **context-window usage, usage quota (the 5-hour rate-limit window), active tool calls, sub-agent status, and Todo progress** in real time — directly addressing the two classic blind spots: response quality silently dropping as context fills, and getting rate-limited mid-task with no warning.

> Source: Chimin (githubdaily) — "Finally, a Useful Claude Code Status Bar Enhancement Plugin!" (2026-06-24, 7 min) — member-only; details from the free preview.

---

## Claude Desktop as a workspace (code mode)

Claude Desktop's **Code tool** turns the app from a stateless chat box into a persistent workspace. Instead of isolated Q&A, you switch to code mode, point it at a **local folder**, and Claude can create and iterate on files that persist across prompts — building cumulative, multi-step workflows that would be tedious or impossible in plain chat. The minimal on-ramp: "switch to code, point to a local folder, start prompting." See also Kotrotsos's companion piece "20 Non-Coding Uses for Claude's Code Mode" (in the [[Medium-Reading|backlog]]).

> Source: Marco Kotrotsos — "Turn Claude Desktop Into an AI Workspace" (2026-06-19, 7 min, 314 claps)

---

## Dynamic workflows — orchestration scripts for walk-away runs (May–June 2026)

A research-preview feature (toggle in `/config` → Dynamic workflows row, or say "ultracode") that changes *who holds the plan*. With a subagent, Claude orchestrates turn-by-turn and the plan lives in its context. With a skill, Claude follows your written instructions. With a **dynamic workflow**, Claude writes a real orchestration script (JavaScript) that partitions the task, runs up to 16 subagents in parallel (1,000 cap per run) in the background, and only returns the final answer — your session stays free.

**Two triggers, not interchangeable:**
- Put "workflow" in a single prompt → runs just that task as a workflow, rest of session normal (the "weekday setting")
- `/effort ultracode` → whole session decides for itself when to fan out (the "Saturday setting" — turn off again with `/effort high` before routine work, or small tasks start costing like big ones)

**The only skill that matters: specifying the bar.** No human is in the loop mid-run, so your "definition of done" *is* the quality ceiling. "Build a SaaS" gives reviewers nothing to check; "build a SaaS where a user can sign up, subscribe via Stripe test mode, cancel, and existing tests pass" gives every agent a target — write prompts like acceptance tests.

**The clean test for whether a task suits a workflow — partitionable AND verifiable:**
- *Partitionable*: splits into pieces that don't depend on each other (migrations, audits, bug sweeps, research, multi-module builds)
- *Verifiable*: there's an objective pass/fail (tests passing is strongest; adversarial reviewers refuting each other is built in)
- Anything sequential or taste-based (most writing, product strategy, "should we do X or Y") gets nothing from fan-out — keep those in a normal conversational session
- Rule of thumb: if a senior engineer would say "report back when the tests pass," it's a workflow; if they'd say "let's sit down and work through this," it's not

**Operational habits that separate a useful run from an expensive mess:**
- Try `/deep-research` first — it ships with the same rhythm (parallel searches, cross-checked claims, voted/filtered report) and teaches the feel in ~10 minutes
- **Allowlist tools before walking away** — subagents auto-accept file edits, but shell/web/MCP calls outside your allowlist still pause for approval and stall the whole run
- Scope a 5-file sample, read the actual token bill, then multiply for the real job — don't point it at 500 files cold
- Route mechanical/bounded stages to a cheaper model in your prompt; save the strongest model for planning and consolidation
- Watch live with `/workflows` — drill into any agent's prompt/findings, `p` to pause, `x` to stop one, `r` to restart it
- `s` inside `/workflows` saves a working script as a reusable `/yourcommand` (lives in `.claude/workflows/` or `~/.claude/workflows/`)
- No mid-run human gate exists — if you want checkpoints, chain *separate* workflow runs (e.g., map → write → test → cleanup) rather than one giant one

> Source: Marco Kotrotsos — "I Spent a Saturday Letting Claude Code Build Whatever It Wanted" (May 30, 2026) — describes Jarred Sumner's port of Bun (~750K lines Zig→Rust, 99.8% tests passing, 11 days) as the template: chained workflows, each partitionable with a hard pass/fail bar.
> Corroborated by: GaoDalie AI — "How To Build Claude Dynamic Workflows Better Than 99% of People" (2026-06-19, 6 min, 109 claps) — confirms the June 2, 2026 announcement: Claude generates JavaScript orchestration scripts and runs up to 1,000 subagents in parallel for large-scale tasks.

## claude-code-guide — the built-in self-help subagent

A **built-in subagent** (not a project file, not configured by you) specializing in three things: the Claude Code CLI itself, the Agent SDK, and the Claude API. Invoke it directly:

```
Use the claude-code-guide agent to explain how hooks work
```

Runs with `Glob`, `Grep`, `Read`, `WebFetch`, `WebSearch` — deliberately **cannot edit or write files**. Its only job is to answer questions about how Claude Code works, so you don't have to break context and go search docs manually.

> Tip: for richer explanations, switch `output-style` to `Learning` or `Explanatory` in `/config` while using it.

> Source: Daniel Avila — "Claude Code has a hidden Agent for answering questions about itself" (Mar 9, 2026)

## Code Review — Anthropic's multi-agent PR reviewer (research preview, Teams/Enterprise)

Built to address the "200% problem": AI assistants tripled developer code output (≈100 → 300 lines/day) without growing review capacity, so PRs increasingly get skimmed rather than read. Code Review deploys **four specialized agents per PR** in parallel — Security, Performance, Maintainability, Logic — then a coordinating agent merges/dedupes/prioritizes findings and posts color-coded flags directly to the GitHub PR (red = high severity, yellow = needs deeper human look, purple = recurring/historical bug pattern). Humans still approve or reject; the tool focuses judgment rather than replacing it.

**Reported metrics:** substantive review comments rose from 16% → 54% of all comments; catches issues in 84% of large PRs (>1,000 lines) vs. 31% of small ones (<50 lines) — exactly where human reviewers struggle most; <1% of AI findings judged incorrect by engineers.

**Pricing:** $15–25 per PR (e.g. ~$18.7K–31K/month for a team shipping 50 PRs/day) with monthly budget caps, repo-level permissions, and analytics dashboards to manage cost.

> Source: Faisal haque — "The Code Review Crisis Nobody Saw Coming" (Mar 10, 2026)

## claude-code-setup — official config-audit plugin

Anthropic's first-party `/plugin install claude-code-setup@claude-plugins-official` scans your project's existing Claude Code configuration and produces *specific* recommendations across four areas: hooks, skills, MCP servers, and subagents — not a generic checklist, but findings based on what your project actually contains.

**One real audit (single-author blog workspace, "not a complex codebase"):**
- `settings.local.json` had **accumulated 59 permission entries** over months — many one-off debugging commands approved under time pressure and never revisited (`where jq`, `netstat`, a one-time directory listing)
- **Two entries contained a hardcoded OpenAI API key inline** (`Bash(OPENAI_API_KEY="sk-proj-..." uv run ...)`) — redundant with a broader `Bash(uv run:*)` rule, so pure security liability with zero functional value
- Cleanup brought the list from 59 → 33 entries

**What it added unprompted:**
- **API-key protection hook** — `PreToolUse` hook blocks any edit attempt to `.env` or `*API_KEY*`-matching files, even if you explicitly ask Claude to edit them
- **Word-count hook** — `PostToolUse` warns (doesn't block) when a draft falls outside a configured target range; caught two articles that "felt complete" but ran 15% short
- **Editorial review subagent** in `.claude/agents/` — checks drafts against a house-style checklist (structure, paragraph length, title quality, SEO metadata) and returns READY TO PUBLISH / NEEDS REVISION
- Installed the **context7 MCP server** for live library/SDK doc lookups

**The takeaway:** configuration is not a one-time setup task. Settings accumulate silently — a permission approved mid-debug six months ago is still there, unreviewed, possibly a liability. Run an audit like this every few months even on "simple" projects.

> Source: Mark Chen — "What a Claude Code Plugin Found in My Own Configuration" (2026-05-24)

---

## Blender MCP — 3D content generation from natural language

Anthropic shipped an official **Blender MCP connector**, letting Claude Code drive Blender (the open-source 3D suite) directly — model, texture, light, and render scenes from plain-language prompts instead of manual node graphs and keyframing.

**What it's good at:** straightforward procedural generation — "create a low-poly forest scene," basic material assignment, simple animation rigs, batch operations across many objects (renaming, repositioning, applying a modifier to a selection).

**What it's bad at:** anything requiring genuine artistic judgment — composition, lighting mood, character expression nuance — and complex multi-step workflows where Blender's UI state matters (the MCP loses track of viewport context across long sessions).

**Setup:** requires Blender running locally with the MCP add-on enabled; Claude connects over the same MCP transport as any other server.

**Safety note:** Blender scripts execute Python in-process — treat generated scripts with the same scrutiny as any agent-written shell command before running them on files you care about.

> Source: Kristopher Dunham — "Claude Drives Blender Through MCP Now. Here's What Actually Changes." (2026-06-05ish)

---

## Workflow & ultracode — deeper mechanics (May 2026)

Extends [[#Dynamic workflows — orchestration scripts for walk-away runs (May–June 2026)]] with the underlying architecture. Both features shipped **2026-05-28** alongside Opus 4.8; require Claude Code ≥ 2.1.154.

### The three layers

| Layer | What it is | Key property |
|---|---|---|
| **Harness** | The wrapper around the model — reads/writes files, runs commands, holds state. Claude Code itself. | Main loop runs turn-by-turn, full history in context |
| **Subagent** | Named, isolated Claude instance — own system prompt, context, tools. Launched via the Agent tool; only the final answer returns. | **Protects context quality, doesn't add intelligence.** Can't launch another subagent by default — nesting is blocked |
| **Workflow** | A JS orchestration script the runtime executes in the background, fanning work across dozens–hundreds of subagents | **State lives in script variables, not the model's context** — only the verified final answer comes back |

`ultracode` is a session switch (maximum effort / `xhigh`) that lets Claude decide on its own, without asking, whether a task deserves a workflow.

### The four script primitives

| Primitive | Behavior |
|---|---|
| `agent(prompt, opts?)` | Launches one subagent; returns text or (with a schema) validated structured data |
| `parallel(thunks)` | Runs concurrently — **a barrier**: waits for every thread before continuing. Use only when a later stage genuinely needs all results at once (dedup, early exit, "the remaining findings") |
| `pipeline(items, ...stages)` | Streams each item through all stages independently, **no barrier** — item A can be in stage 3 while B is still in stage 1. Default choice; waiting on the slowest thread is wasted time |
| `workflow()` | Runs another workflow inline — composition limited to one nesting level |

**Hard requirement — determinism:** `Date.now()` and `Math.random()` throw inside a workflow because the runtime journals every `agent()` call so an interrupted run can resume exactly where it stopped. Close Claude Code mid-run → a new session restarts from scratch. Workflow also **accepts no input mid-run** — only approval prompts can pause it; for human sign-off between stages, run separate workflows per stage.

### Four named verification patterns (sit on the fan-out → reduce → synthesize skeleton)

| Pattern | What it does |
|---|---|
| **Adversarial verification** | Several independent "skeptics" try to disprove each finding; only what survives a majority vote passes |
| **Perspective-diverse verify** | Each verifier gets a *different* lens (correctness / security / performance) — diversity catches what redundancy misses |
| **Judge panel** | Generate several attempts from different angles, evaluate with parallel judges, synthesize from the winner while stitching in the losers' best elements |
| **Loop-until-dry** | Spawn finders repeatedly until several consecutive rounds turn up nothing new — dedup must run **against everything seen so far**, not just confirmed results, or rejected findings resurface forever |

The built-in `/deep-research` is the reference example: 5 phases — break question into 5 search angles → 5 parallel searchers → dedupe URLs + extract ~15 sources' claims → 3-voice adversarial verification per claim → one agent writes the cited report. Claims that don't survive the vote never reach the report.

### Cost — the boundary of protection

Hard ceiling: **1,000 agents per run, max 16 in parallel** — an agent-count limit, not a cost limit. No CLI token-budget cap exists; the only lever is an in-script budget variable, empty until you set it. Anthropic reports ~90% improvement over single-agent on an internal eval (token consumption alone explaining ~80% of variance) — but it's an internal, unbenchmarked number. Real-world horror stories circulating: a degenerative loop burning ~1.7M tokens for no output; a single prompt spinning up 22 agents and consuming 7.2M tokens. **Without a hard safeguard, nothing stops a run for you** — watch `/workflows`, be ready to press `x`.

### When yes / when no

**Reach for it when:** breadth (full-repo audit/sweep wider than one context), independent verification (high-stakes work needing adversarial self-check), or scale beyond one context (large migrations/ports — the Bun Zig→Rust port: ~750K–960K lines, ~11 days, hundreds of agents, "not yet in production," and reviewers challenged the claimed 99.8% test-pass rate since some tests were generated alongside the code).

**Stay single-agent when:** the task fits in one agent (single bug, one file); you need human input mid-task; the work is repetitive and predictably priced (a dedicated subagent is simply cheaper).

> Source: Jaroslaw Wasowski — "From One Agent to an Orchestra — How Workflow and ultracode Work in Claude Code" (2026-05-30, 12 min)

---

## Claude Cowork plugins — department specialists for the desktop app

**Cowork** is Anthropic's agentic desktop application — Claude running as a coworker on your machine rather than a browser tab. **Plugins** turn that generic coworker into a domain specialist by bundling three things into a single install:

| Component | What it is |
|---|---|
| **Skills** | Domain knowledge as Markdown — Claude pulls them in automatically, like a colleague quietly applying experience (e.g. an `account-research` skill that knows how to vet a company before a sales call) |
| **Commands** | Explicit slash-triggered workflows chaining skills into multi-step recipes with approval checkpoints (`/forecast`, `/onboarding`) |
| **MCP connectors** | Pre-wired links to where the real work lives — Google Drive, Gmail, Slack, DocuSign, Salesforce, HubSpot, Workday, dozens more |
| **Sub-agents** | Specialized helpers inside a larger workflow — e.g. a code reviewer in Engineering, a brand-voice auditor in Marketing |

### Cowork plugins vs. Claude Code plugins

Same underlying file format (Cowork's docs literally point to the Claude Code plugins reference) — but **separate install pipelines and registries**. Cowork installs through the desktop app's Customize menu; Claude Code installs via `/plugin` or `.claude/settings.json`. A plugin tagged for both surfaces (e.g. Slack) must be installed in each environment separately — files are portable, installations are not. Plugins install **at the user level**, not per-Project — once installed, available across all Cowork tasks/projects.

### Role-based plugin tour (from Anthropic's open-source `knowledge-work-plugins` repo)

Every plugin works in two modes: **standalone** (paste notes/CSVs, no integrations needed — usable day one) and **supercharged** (deeper when tools are connected). Internally they reference categories (`~~CRM`, `~~HRIS`) rather than specific products, so the workflow doesn't care whether you run Salesforce or HubSpot.

| Role          | Signature commands                                                                        | Connects to                                                                                                 |
| ------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **Sales**     | `/call-summary`, `/forecast`, `/pipeline-review`                                          | HubSpot/Salesforce, Gong/Fireflies, Clay/ZoomInfo                                                           |
| **Marketing** | `/draft-content`, `/campaign-plan`, `/brand-review`, `/seo-audit`, `/email-sequence`      | —                                                                                                           |
| **Legal**     | contract review, NDA triage, compliance — configured via a `legal.local.md` playbook file | — (explicitly disclaims giving legal advice; default playbook is U.S.-only — non-U.S. teams must customize) |
| **HR**        | `/draft-offer`, `/onboarding`, `/performance-review`, `/comp-analysis`, `/people-report`  | Workday/BambooHR/Rippling, Greenhouse/Lever/Ashby, Pave/Radford                                             |

> Source: Rick Hightower — "Claude Cowork Plugins: How Anthropic Quietly Turned Its AI Into a Specialist for Every Department" (2026-05-20, 14 min)

---

## See also
- [[Git]] — Claude Code integrates deeply with git (worktrees, PRs)
- [[AI-Agents]] — Managed Agents, harness engineering, agentic frameworks
- [[Obsidian]] — Claude Code + Obsidian vault integration
- [[AI-ML]] — underlying model concepts
- [[MCP]] — building MCP servers in depth
- [[CSharp]] — dotnet-claude-kit full guide
