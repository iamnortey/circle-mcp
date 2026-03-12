# GitHub Positioning Refresh — Report

> Date: 2026-03-12
> Version: v0.3.0 — Safe Content Operations

---

## Core Positioning Shift

**Before:** "Community intelligence and safe content operations for Circle.so — powered by the Model Context Protocol."

**After:** "Your Circle.so operator copilot for Claude Desktop — understand, manage, and grow your community with natural language."

The previous positioning described the product technically. The new positioning describes what it does for the user. The shift from "MCP server" to "operator copilot" communicates that this is a productivity tool, not just a protocol implementation.

---

## What Changed in the Public README

### 1. Title and subtitle

The subtitle was rewritten from a technical classification to a value statement. The user now understands in one line: this tool helps you operate your Circle.so community from Claude.

### 2. Opening paragraph and tagline

Added the tagline: **"Draft with Claude. Operate in Circle.so via MCP."**

The opening paragraph now leads with the user pain (clicking through the Circle admin UI) and the solution (ask Claude instead). Previously it led with protocol details.

### 3. New "What It Helps You Do" section

Four outcome-oriented blocks replace feature descriptions:
- Understand your community faster
- Find gaps and neglected content
- Create and update content from Claude
- Operate more efficiently

Each block describes a real workflow improvement, not a tool name.

### 4. Expanded "Who It's For" section

Four audience segments with pain-based framing:
- **Community operators** — spend too much time in the admin UI
- **Agencies and Circle experts** — need to audit client communities fast
- **Educators and cohort builders** — manage learning communities and lesson content
- **Founders and solo operators** — need leverage without a dedicated community manager

Each segment explains who they are, what hurts, and how the tool helps.

### 5. New "What It Does Not Do Yet" section

Explicit honesty about current limitations:
- No moderation, no destructive actions, no member management
- No space/community settings control
- Comment creation restricted by Circle API
- No historical analytics, no events/courses yet

This section builds trust with technically savvy evaluators.

### 6. New "Use Cases" section

Four scenario-based use-case blocks with example prompts:
- Agencies auditing a client community
- Educators managing a learning community
- Founders keeping a community alive
- Operators improving community quality

Each block shows real Claude prompts that demonstrate concrete workflows.

### 7. Removed sections

- **"Why It Exists"** — replaced by the stronger opening and use-case framing
- **"Known Limitations" (14-item list)** — consolidated into the more readable "What It Does Not Do Yet" section. Technical limitations remain in the private repo README for developers.
- **Read/write tool behavior notes** — moved down to reduce noise before the value sections

### 8. Section reordering

The README now flows: Value → Audience → Use Cases → Architecture → Tools → Technical Details → Getting Started

Previously it was: What → Why → Architecture → Tools → Resilience → Stack → Status → Limitations → Getting Started

---

## What Changed in the Private Repo

### README title and subtitle

- Title: `Circle MCP Server` → `Circle.so MCP Server`
- Subtitle: Rewritten to: "Implementation source of truth — community intelligence and safe content operations for Claude Desktop via MCP."

The private repo README retains its full operator/developer documentation. Only the framing at the top was refreshed.

### GitHub description

Updated to: "Circle.so MCP server (source) — community intelligence and safe content operations for Claude Desktop (16 tools, v0.3.0)"

The `(source)` marker distinguishes it from the public repo in search results.

---

## GitHub Metadata Changes

| Repo | Field | New Value |
|------|-------|-----------|
| `circle-mcp` (public) | Description | "Circle.so operator copilot for Claude Desktop — community intelligence and safe content operations via MCP (16 tools, v0.3.0)" |
| `circle-mcp-server` (private) | Description | "Circle.so MCP server (source) — community intelligence and safe content operations for Claude Desktop (16 tools, v0.3.0)" |

Topics remain unchanged from the previous polish pass — already high-signal.

---

## Product Description — Final Recommended Copy

### One-liner (GitHub description)

> Circle.so operator copilot for Claude Desktop — community intelligence and safe content operations via MCP

### Tagline (README)

> Your Circle.so operator copilot for Claude Desktop — understand, manage, and grow your community with natural language.

### Elevator pitch (30 seconds)

> Circle.so MCP connects your Circle community to Claude Desktop. Ask questions about your spaces, posts, members, and engagement. Find unanswered posts and content gaps. Create and update posts without leaving Claude. It is an operator copilot that turns Circle.so into a queryable, manageable knowledge source — so you can run your community faster.

---

## User Segments Emphasized

| Segment | Core Pain | How This Helps |
|---------|-----------|----------------|
| Community operators | Too much time in admin UI | Instant answers via Claude |
| Agencies / Circle experts | Slow client audits | Structured community view in minutes |
| Educators / cohort builders | Managing lesson content across tabs | Draft and publish from Claude |
| Founders / solo operators | No dedicated community manager | Leverage through automation |

---

## Optional Future Refinements

1. **Social preview image** — 1280x640 Open Graph image for link previews
2. **Shields.io badges** — version, license, Node.js, CI status
3. **Video demo** — short screen recording showing Claude + Circle.so workflow
4. **Landing page** — link from GitHub homepage field once one exists
5. **npm publish** — after v0.4.0 stabilizes
6. **GitHub Discussions** — enable on public repo for community feedback
