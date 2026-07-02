---
title: Claude on Mobile
tags: [claude, mobile, ios, android, voice, shortcuts, telegram, mcp, automation]
related: [Claude-Code, AI-Agents, AI-ML]
sources: []
updated: 2026-04-14
---

# Claude on Mobile

All the ways to connect Claude to your phone — from the official apps to terminal workarounds, messaging bots, health data, and automation.

---

## Official mobile apps

Claude has native apps for iOS and Android with full feature parity as of March 2026.

| Feature | iOS | Android | Notes |
|---|---|---|---|
| Core chat | ✅ | ✅ | All models (Haiku / Sonnet / Opus) |
| Voice mode | ✅ | ✅ | Free for all tiers since early 2026 |
| Vision (camera/image) | ✅ | ✅ | Upload or take photo inline |
| Claude Code (native) | ✅ | ✅ | iOS Oct 2025, Android Mar 2026 |
| Health data | ✅ | ✅ | Beta, US only — see below |
| Projects | View only | View only | Cannot create new projects on mobile |
| MCP config | ❌ | ❌ | Configure via web, syncs to mobile |
| Computer Use | ❌ | ❌ | Mac desktop only |
| Artefacts library | Limited | Limited | Full interface on web/desktop |

**Download:**
- App Store: search "Claude by Anthropic"
- Google Play: search "Claude by Anthropic"

---

## Voice mode

Full duplex voice conversation — hands-free or push-to-talk.

| Option | Description |
|---|---|
| Continuous listening | Hands-free, always on |
| Push-to-talk | Better for noisy environments |
| Five voice styles | Buttery, Airy, Mellow, Glassy, Rounded |

Powered by ElevenLabs TTS. No typing required — treat it like a phone call with Claude.

**Use cases:** Talking through code problems while driving, quick questions without opening a laptop, voice journaling into the wiki via Claude.

---

## Health data integration (Beta)

Claude can read health metrics from Apple Health (iOS) and Health Connect (Android 14+).

**Supported sources:**
- Apple Health — activity, workouts, vitals, body measurements
- Health Connect (Android 14+) — equivalent fitness/health data
- HealthEx and Function Health (third-party integrations)

**Key constraints:**
- US only as of April 2026
- Opt-in — you explicitly grant access per data type
- Data is not used to train Claude models
- Analysis happens in your conversation context, not stored by Anthropic

**Example prompts:**
- "Analyse my sleep patterns from last month and identify trends"
- "Am I hitting my step goals? What's the weekly average?"
- "Compare my resting heart rate this month vs last month"

---

## Claude Code on mobile

### Official (easiest)
The Claude app on iOS and Android includes Claude Code natively. Open the app → start a Claude Code session. Limitations: no terminal access, no file system browsing outside the session.

### Remote control via Claude Code Channels (best for power users)
Connect your desktop Claude Code session to your phone via Telegram or Discord:

1. On desktop: install the Telegram or Discord MCP server in your Claude Code config
2. Authenticate with your bot token
3. Start a Claude Code session — Claude can now send and receive messages via the messaging app
4. Full remote control from your phone's Telegram/Discord client

This gives you desktop Claude Code (file access, all tools, all MCP) controllable from anywhere.
See the **Messaging app integrations** section below for setup steps.

### Android — Termux (terminal access)
Run Claude Code CLI directly on Android:

```bash
# 1. Install Termux from F-Droid (NOT Play Store — outdated version)
# 2. Inside Termux:
pkg update && pkg install nodejs git

# 3. Install Claude Code
npm install -g @anthropic-ai/claude-code

# 4. Add API key
export ANTHROPIC_API_KEY=your_key_here

# 5. Run
claude
```

> **Note:** The native installer has Android ABI incompatibilities — use the alias/npm method above. See [claude-code-termux](https://github.com/Ishabdullah/claude-code-termux) for a setup script.

**Add Tailscale for remote access:**
```
Desktop Claude Code session (tmux) → Tailscale VPN → SSH from Termux
```
This lets your phone SSH into your desktop Claude Code session with session persistence via tmux.

### iOS — SSH clients
iOS has no Termux equivalent. Options:
- **Blink Shell** — SSH/MOSH client, connects to remote Claude Code session
- **Termius** — cross-platform SSH client
- **iSH** — Alpine Linux emulator, very limited (no full Claude Code support)

Best iOS approach: run Claude Code on a remote server or desktop, SSH in from Blink.

---

## Messaging app integrations

### Telegram (official — Claude Code Channels)

Anthropic ships an official Telegram MCP server as part of Claude Code Channels.

```
Claude Code desktop session
    ↓ Telegram MCP server
    ↓ Messages forwarded to your Telegram
```

**Setup:**
1. Install Bun (required — Node/Deno may fail): `npm install -g bun`
2. Add the Telegram MCP to your Claude Code MCP config
3. Authenticate with your Telegram bot token
4. Claude can send messages, reply, react with emoji, and receive your responses

**Use case:** Start a long Claude Code task on desktop, get notified and respond via Telegram from your phone. Full remote control without needing to be at your computer.

### Discord (official — Claude Code Channels)
Same Channels framework as Telegram. Claude Code ↔ Discord DMs or channels.

### iMessage
Included in the official Claude Code Channels research preview.

### WhatsApp
Not officially supported. Community implementations exist (Clawdbot, custom MCP connectors) but require self-hosting.

---

## iOS Shortcuts automation

Claude has native **App Intent** support on iOS — no API key needed, uses your Claude app.

**Built-in: "Ask Claude" shortcut**
1. Open Shortcuts app on iPhone
2. Add action → search "Ask Claude"
3. Wire it to any trigger (Siri, home screen button, NFC tag, Focus mode)

**Example shortcuts:**
- "Hey Siri, ask Claude to summarise my clipboard" → paste result back to clipboard
- NFC tag on desk → triggers daily briefing prompt
- Morning Focus mode → runs "Summarise my calendar and priorities" on Claude

**Siri integration:**
- "Hey Siri, ask Claude [anything]" works natively via Shortcuts
- Claude responds via the Shortcuts notification or Speak Text action

**Widgets:**
- Claude home screen widget for quick text input without opening the app

---

## Android automation (Tasker)

**Direct HTTP approach** (simple):
```
Tasker HTTP Request task:
  Method: POST
  URL: https://api.anthropic.com/v1/messages
  Headers: x-api-key: %ANTHROPIC_KEY
  Body: { "model": "claude-sonnet-4-6", "messages": [...] }
```

**Tasker MCP Server** (advanced):
- Community MCP server that exposes Tasker tasks to Claude
- Claude can trigger Tasker automations and read results
- GitHub: [tasker-mcp](https://mcp.pizza/mcp-server/0WAn/tasker-mcp)
- Compatible with Claude, OpenAI, and Gemini

**Example automations:**
- Phone detects you've arrived home → Claude summarises your unread messages
- Battery drops below 20% → Claude pauses any long running sessions
- Time trigger at 8am → Claude Code Channels sends daily digest processing notification

---

## Push notifications

**Official (2026):** Claude now sends push notifications to your phone when:
- A long-running task finishes
- Claude needs your permission to proceed
- An error occurs requiring input

Works via the Claude mobile app — no setup required.

**Community: ntfy.sh** (for Claude Code sessions):
```
Claude Code PostToolUse hook
    ↓ calls ntfy.sh HTTP endpoint
    ↓ ntfy app on phone receives push notification
```

Setup:
1. Install ntfy app on phone, subscribe to a private topic
2. Add a Claude Code hook:
```json
{
  "PostToolUse": [{
    "matcher": "",
    "hooks": [{
      "type": "command",
      "command": "curl -d 'Claude needs input' ntfy.sh/your-private-topic"
    }]
  }]
}
```

**Happy** (community mobile client): Smart notifications — alerts when code is ready for review, when Claude hits an error, or when a long task completes.

---

## SMS and voice calls via Twilio

Build a phone number that talks to Claude:

**Voice calls:**
```
Caller dials Twilio number
    ↓ Twilio Programmable Voice
    ↓ Claude API (with function calling)
    ↓ Claude responds via TTS
    ↓ Caller hears Claude's answer
```

**SMS bot:**
```
SMS to Twilio number
    ↓ Twilio webhook → your server
    ↓ Claude API
    ↓ Response sent back as SMS
```

**Official Twilio MCP server (Alpha):**
- Anthropic + Twilio co-developed MCP server
- Exposes SMS, voice, and WhatsApp as Claude tools
- Claude can send/receive messages as part of an agentic workflow
- Requires Twilio account + API keys

**No-code option:** Zapier can connect Twilio SMS to Claude without writing code.

---

## Mobile device control MCP

**claude-in-mobile** (GitHub: AlexGladkov/claude-in-mobile):
- Claude controls Android devices via ADB
- Claude controls iOS Simulator via `simctl`
- Take screenshots, interact with UI, input text, manage apps
- Useful for automated mobile testing — "test this screen, click the login button, tell me what happens"

**Setup:**
```bash
# Android: requires ADB connected device or emulator
adb devices  # verify device connected

# Add MCP to Claude Code config
# Claude can then call browser_screenshot, browser_tap, etc.
```

---

## Building mobile apps with Claude API

### React Native / Expo
Claude Code works natively with Expo SDK 52 (React Native 0.76):
- Ask Claude to scaffold components, fix layout bugs, write tests
- NativeWind (Tailwind) for styling
- Zustand for state management
- Expo Router for navigation

### Swift (iOS native)
Claude Code integrates with Xcode — describe the UI or behaviour, Claude writes the Swift code. Works with SwiftUI and UIKit.

### Kotlin / Jetpack Compose (Android native)
Claude Code integrates with Android Studio — Jetpack Compose UI generation, ViewModel patterns, Hilt DI wiring.

**Anthropic SDK:**
No official Swift or Kotlin SDK — call the REST API directly:
```swift
// Swift example
let url = URL(string: "https://api.anthropic.com/v1/messages")!
var request = URLRequest(url: url)
request.setValue("your-key", forHTTPHeaderField: "x-api-key")
request.setValue("2023-06-01", forHTTPHeaderField: "anthropic-version")
// ... POST with messages body
```

For TypeScript/React Native, use the official `@anthropic-ai/sdk` npm package directly.

---

## Limitations summary

| Limitation | Details |
|---|---|
| **Project creation** | Cannot create new Projects on mobile — web/desktop only |
| **Computer Use** | Mac desktop only — not accessible via mobile |
| **MCP configuration** | Configure on web (claude.ai), syncs to mobile — can't add from app |
| **Health data** | US only, Android requires version 14+ |
| **Full artefacts library** | Dedicated UI only on web/desktop |
| **Claude Code CLI** | iOS has no native terminal — requires SSH to remote or use the Claude app |
| **Termux (Android)** | Native installer broken — npm alias method required |

---

## Quick reference: which approach for what

| Goal | Best approach |
|---|---|
| Chat with Claude on phone | Official Claude app |
| Hands-free conversation | Voice mode in Claude app |
| Run Claude Code from phone | Claude Code Channels (Telegram/Discord MCP) or SSH + tmux |
| Automate with Siri/Shortcuts | iOS Shortcuts + "Ask Claude" App Intent |
| Android automation | Tasker HTTP → Claude API |
| Get notified when tasks finish | Claude app push notifications or ntfy.sh hook |
| Message Claude from Telegram | Claude Code Channels + Telegram MCP |
| Phone number that talks to Claude | Twilio + Claude API |
| Test mobile apps with Claude | claude-in-mobile MCP |
| Build a mobile app using Claude | Claude Code + Expo/React Native or Xcode/Android Studio |

---

## See also
- [[Claude-Code]] — Claude Code Channels (Telegram/Discord), `/rc` remote control, hooks
- [[AI-Agents]] — agent patterns relevant to mobile automation pipelines
