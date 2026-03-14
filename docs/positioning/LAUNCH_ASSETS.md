# Launch Assets — Circle MCP

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Status:** Internal — ready-to-use copy blocks for GTM
> **Purpose:** Pre-written launch copy for social, outreach, community posts, and landing pages

---

## GitHub Repository Description (One-Liner)

> The AI operating interface for Circle.so — community intelligence and operations through natural language.

---

## Social Launch Posts

### Twitter/X — Primary Launch

> Circle MCP: the AI operating interface for Circle.so.
>
> Ask Claude to audit your community structure. Find unanswered posts. Check engagement health. Create content. All through conversation.
>
> 16 tools. Non-destructive. Production-validated.
>
> Open source: github.com/iamnortey/circle-mcp

### Twitter/X — Problem-First

> Community operators are human middleware.
>
> Copy from Circle dashboard → paste into AI → get answer → go back to Circle → take action.
>
> Circle MCP eliminates the round trip. Your AI assistant can now see inside your Circle community and act on it directly.

### Twitter/X — For the AI-Native Crowd

> If you run a Circle.so community and use Claude, you should know about Circle MCP.
>
> It gives Claude structured access to your community — health checks, content audits, unanswered post detection, and safe content creation. All through conversation.
>
> No more dashboard fatigue.

### LinkedIn — Professional Announcement

> **Introducing Circle MCP — the AI operating interface for Circle.so communities.**
>
> Community operators spend hours every week switching between admin dashboards and AI assistants. Circle MCP bridges that gap.
>
> With 16 validated tools, Circle MCP lets you operate your Circle community through natural language:
>
> - Run community health audits in seconds
> - Detect unanswered posts before they go stale
> - Surface content gaps and engagement risks
> - Create and update content directly from Claude
>
> It runs locally on your machine. Your data stays between you, Claude, and Circle's API. Non-destructive by design — create and update only, no delete operations.
>
> Built for community managers, consultants, agencies, educators, and AI-native founders who want operator-grade community intelligence without the dashboard tax.
>
> Open source under MIT license.
>
> github.com/iamnortey/circle-mcp

---

## Community Forum Posts

### Circle Community Post (Meta — For Circle's Own Community)

**Title:** I built an AI operating interface for Circle.so — here's what it does

**Body:**

Hey everyone,

I've been building Circle MCP — an open-source tool that connects your Circle community to Claude (via Claude Code or Claude Desktop).

Instead of clicking through the admin dashboard to check community health, find unanswered posts, or audit your space structure, you can ask Claude in natural language and get structured answers.

**What it can do today (v0.3.1):**
- Community health snapshots (member counts, post volume, engagement risks)
- Unanswered post detection across all spaces
- Full space and content inventory
- Content gap analysis
- Create and update posts (drafts first, for safety)
- Search across your entire community

**What it can't do yet:**
- No delete or moderation operations
- No events or courses (coming in v0.4)
- No multi-community support yet

It runs locally on your machine — your API token and data stay in your environment. MIT licensed.

If you manage a Circle community and use Claude, I'd love your feedback.

GitHub: github.com/iamnortey/circle-mcp

### MCP/AI Community Post (For Dev/AI Communities)

**Title:** Circle MCP — production-grade MCP server for Circle.so with 16 tools

**Body:**

I've shipped Circle MCP, an MCP server that gives Claude structured access to Circle.so communities.

**Technical highlights:**
- 16 tools: 13 read + 3 write (non-destructive)
- Strict TypeScript + Zod `.strict()` validation on all inputs
- Retry with exponential backoff (read path) + zero-retry mutations (write path)
- Dual response format: text + structuredContent
- Dual transport: stdio (production) + Streamable HTTP (experimental)
- 95 offline tests + 19 live API tests
- 2 runtime dependencies

**Derived intelligence tools** (not just API passthrough):
- `community_health` — aggregates members, posts, spaces, unanswered questions into a single briefing
- `detect_unanswered_posts` — scans all spaces for posts with zero comments

Safe mutation pattern: `mutate<T>()` with zero retries, flat endpoint/payload, and MCP annotations (`readOnlyHint: false`, `destructiveHint: false`).

GitHub: github.com/iamnortey/circle-mcp-server
npm: circle-so-mcp

---

## Email Templates

### Cold Outreach — Community Consultant

**Subject:** Audit a Circle community in 3 minutes

**Body:**

Hi [Name],

Quick question — how long does it take you to audit a client's Circle community today? Review space structure, check for unanswered posts, assess content gaps, put together a health snapshot?

I built Circle MCP, an AI operating interface that connects Circle.so to Claude. It turns that multi-hour dashboard session into a 3-minute conversation:

- "Show me all spaces grouped by space group. Which ones are empty?"
- "Detect unanswered posts and prioritize by age."
- "Generate a community health snapshot I can present to my client."

It runs locally on your machine (your data stays private), and it's open source.

If you manage Circle communities for clients, I'd love to show you a quick demo.

Best,
Isaac

### Cold Outreach — Agency

**Subject:** Circle community ops without the dashboard tax

**Body:**

Hi [Name],

I noticed [Agency] manages communities on Circle.so. Your team is probably spending hours each week clicking through admin dashboards — checking health, finding unanswered posts, publishing content across multiple communities.

Circle MCP eliminates that dashboard tax. It gives Claude direct, read-and-write access to Circle communities:

- Daily health checks in seconds, not minutes
- Unanswered post detection before SLA breaches
- Content creation from conversation (drafts first, for safety)

One AI conversation instead of five admin tabs.

Open source, runs locally, non-destructive by design. Happy to walk your team through a setup.

Best,
Isaac

---

## Landing Page Copy Blocks

### Hero Section

**Headline:** Stop managing your Circle community through dashboards.

**Subheadline:** Circle MCP is the AI operating interface for Circle.so. Audit, monitor, and operate your community through natural language — from Claude.

**CTA:** Get Started (GitHub) | See What It Can Do

### Problem Section

**Headline:** Dashboard fatigue is real.

**Body:** You check the admin panel. Switch to analytics. Open another tab for content. Copy a number into your AI assistant. Get a recommendation. Go back to Circle to act on it.

You're the middleware between your tools and your AI.

Circle MCP eliminates that loop. It gives your AI assistant direct access to your Circle community — so you operate through conversation instead of clicking through menus.

### Capability Section

**Headline:** What you can do with Circle MCP.

| Capability | What It Replaces |
|------------|-----------------|
| Community health snapshot | 15 minutes of clicking through admin stats |
| Unanswered post detection | Manual scan of every space |
| Content gap analysis | Spreadsheet of spaces vs. activity |
| Space structure audit | Screenshot walk-through of admin panels |
| Content creation | Open Circle → navigate to space → click New Post → type |

### Trust Section

**Headline:** Built for operators who care about reliability.

- **Production-validated** — tested against live Circle communities
- **Non-destructive** — create and update only, no delete operations
- **Privacy-first** — runs locally, no third-party data routing
- **Transparent** — open source, MIT licensed, honest about limitations

---

## Pitch Deck Talking Points

### Slide 1 — The Problem

> Every SaaS platform has a dashboard. None of them talk to AI. Community operators are human middleware — copying data between dashboards and AI conversations.

### Slide 2 — The Solution

> Circle MCP is the AI operating interface for Circle.so. It gives AI assistants structured, safe, intelligent access to community data and operations.

### Slide 3 — How It Works

> Operator asks Claude a question → Circle MCP translates it into Circle API calls → Results come back as structured insights → Operator takes action from the same conversation.

### Slide 4 — Traction

> - 16 validated tools (13 read + 3 write)
> - Production-tested against live Circle communities
> - First MCP server for Circle.so — no direct competitor
> - Open source, MIT licensed

### Slide 5 — The Bigger Vision

> Circle.so is the first system of record. The pattern — AI operating interfaces for SaaS tools — applies to every platform where operators lose time to dashboard fatigue: Kajabi, Notion, HubSpot, Stripe.

---

## Asset Checklist

| Asset | Status | Location |
|-------|--------|----------|
| GitHub README (reframed) | Done | `/README.md` |
| Messaging Framework | Done | `/docs/positioning/MESSAGING_FRAMEWORK.md` |
| Launch Assets (this doc) | Done | `/docs/positioning/LAUNCH_ASSETS.md` |
| Twitter launch posts | Ready | This document |
| LinkedIn announcement | Ready | This document |
| Community forum posts | Ready | This document |
| Cold email templates | Ready | This document |
| Landing page copy blocks | Ready | This document |
| Pitch deck talking points | Ready | This document |
