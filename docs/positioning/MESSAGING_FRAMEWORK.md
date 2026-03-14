# Messaging Framework

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Status:** Internal — not for public distribution
> **Purpose:** Canonical positioning language for all products in the portfolio. Start here before writing any buyer-facing content, outreach, or GTM material.

---

## Part 1: Company Thesis

### The Problem (Universal)

Every SaaS platform has a dashboard. None of them talk to each other. None of them talk to your AI assistant.

Operators — community managers, course creators, consultants, agency teams — spend hours every week as human middleware: clicking through admin panels, copying data into AI conversations, getting recommendations, then going back to the dashboard to act on them.

AI can reason, synthesize, and create. But it can't see inside the tools where work actually lives.

### The Thesis

**We build AI operating interfaces for SaaS systems of record.**

An AI operating interface gives AI assistants structured, safe, intelligent access to the platforms where operators do real work — so they can inspect, analyze, and act through conversation instead of dashboards.

This is not about replacing SaaS tools. It's about making them accessible to the thinking layer (Claude, and future AI clients) — so operators stop being the middleware between their tools and their AI.

### Portfolio Narrative

| Layer | What It Means |
|-------|--------------|
| **Systems of record** | The SaaS platforms where business data lives (Circle, Kajabi, Notion, HubSpot, etc.) |
| **AI operating interface** | The product we build — structured read/write access with built-in intelligence |
| **Thinking layer** | The AI client the operator already uses (Claude Code, Claude Desktop, future clients) |

We sit in the middle. We make systems of record accessible to the thinking layer. Each product in the portfolio connects one system of record.

### Portfolio Architecture

```
Thinking Layer (Claude Code / Claude Desktop / future AI clients)
        ↓
AI Operating Interfaces (our products)
        ├── Circle MCP     → Circle.so
        ├── [Kajabi MCP]   → Kajabi        (future)
        └── [Next]         → TBD           (future)
        ↓
Systems of Record (SaaS platforms)
```

---

## Part 2: Messaging Pillars

These pillars apply to every product in the portfolio. They are the non-negotiable themes that appear in all buyer-facing content.

### Pillar 1: Dashboard Fatigue

**The core pain we name.**

Operators lose hours to tab-switching, manual exports, and copy-pasting between dashboards and AI. We call this dashboard fatigue — and every product we build eliminates it for a specific system of record.

*Use when:* Opening any pitch, email, social post, or landing page. Lead with this.

### Pillar 2: Operator-First

**Who we build for.**

We build for operators — the people who run things day-to-day. Community managers, consultants, educators, founders. Not developers building integrations. Not IT teams evaluating enterprise software. Operators who need leverage.

*Use when:* Describing our audience. Never say "users." Say "operators."

### Pillar 3: Intelligence, Not Just Access

**What makes us more than a connector.**

Every product includes derived intelligence — aggregated insights that go beyond raw API access. Health snapshots, gap analysis, detection of neglected items. Things you can't get from any single dashboard view.

*Use when:* Differentiating from API wrappers, connectors, or generic integrations.

### Pillar 4: Non-Destructive by Design

**Why operators can trust us.**

Our products create and read. They don't delete, archive, or moderate. Write operations default to draft status. This is a deliberate design choice — operators need to trust the tool before they give it more power.

*Use when:* Addressing trust and safety concerns.

### Pillar 5: Privacy-Preserving

**Where the data stays.**

Our products run locally. No data passes through third-party infrastructure. The operator's API token stays in their environment. There is no intermediary between the operator, the AI client, and the system of record.

*Use when:* Addressing data security concerns or differentiating from cloud-hosted competitors.

---

## Part 3: Terminology Guide

### Approved Terms (Buyer-Facing)

| Use This | Instead Of | Why |
|----------|------------|-----|
| AI operating interface | MCP server, connector | Describes the value, not the protocol |
| operating layer | API wrapper | Implies intelligence, not passthrough |
| community intelligence (Circle) | data access | Emphasizes derived insights |
| operator | user | Elevates the audience identity |
| dashboard fatigue | tab-switching | Names the systemic pain |
| system of record | SaaS platform | Positions the target platform as authoritative |
| thinking layer | AI assistant | Positions AI as the reasoning layer operators already use |

### Terms to Minimize in Buyer-Facing Copy

| Term | When Acceptable |
|------|-----------------|
| MCP | Technical docs, architecture sections, developer-facing content, product names |
| connector | Never in primary positioning — implies dumb pipe |
| wrapper | Never — implies no value-add |
| copilot | Overused, commoditized |
| powered by [AI brand] | Makes product feel dependent — AI clients are distribution, not the engine |

### Terms to Avoid Entirely in Public Copy

| Term | Why |
|------|-----|
| cognitive infrastructure | Too abstract for operators |
| cognitive middleware | Same |
| nervous system | Metaphor overreach |
| agentic operations | Jargon |
| language-driven operations | Jargon |
| post-dashboard operator | Invented category, not earned yet |

### Terms That Are Always Fine

| Term | Context |
|------|---------|
| Circle MCP / [Product] MCP | Product names — MCP is part of the brand |
| Claude Code / Claude Desktop | AI client names |
| tools | Technical reference sections |
| server | Architecture and technical detail sections |

---

## Part 4: Product-Level Messaging — Circle MCP

This section instantiates the portfolio framework for the first product. Use this as the template for future verticals.

### Product Identity

**Circle MCP** is the AI operating interface for Circle.so communities.

It gives Claude structured, safe, intelligent access to Circle community data and operations — so operators can inspect, analyze, and act through conversation instead of the Circle admin dashboard.

### Positioning Hierarchy

Lead with the highest level that fits the context.

| Level | Statement | Use When |
|-------|-----------|----------|
| **Category** | AI operating interface for Circle.so | Headlines, first impressions, strategic conversations |
| **Capability** | Community intelligence and operations through natural language | Explaining to non-technical audiences |
| **Mechanism** | Production-grade MCP server with 16 validated tools | Technical docs, developer communities, architecture discussions |

### Audience Messaging

#### Community Operators (Primary)

**Pain:** "I spend hours in the Circle admin dashboard doing things AI should handle."

**Promise:** Operate your Circle community through Claude. Health checks, content audits, unanswered post detection, and content creation — without opening the admin dashboard.

**Proof:** 16 tools validated against live communities. Non-destructive. Draft-first content workflows.

#### Consultants & Agencies

**Pain:** "I audit client communities by clicking through menus for hours. I can't scale this."

**Promise:** Audit any Circle community in minutes. Health snapshots, content gaps, neglected questions — and present findings in the same session.

**Proof:** Community health tool aggregates member counts, post volume, space activity, and unanswered questions into one operator briefing.

#### Educators & Cohort Builders

**Pain:** "I manage course content, answer student questions, and run the community — too many dashboards."

**Promise:** Draft lessons, check for unanswered questions, and manage course spaces from Claude. Posts default to draft for review.

**Proof:** Safe write operations with draft-first workflow. Unanswered post detection scoped by space.

#### AI-Native Founders

**Pain:** "I don't have a community manager, but the community needs to feel alive."

**Promise:** Keep your community active without hiring. Health checks, content creation, and engagement monitoring from one conversation.

**Proof:** Point-in-time health snapshots, content gap analysis, and draft post creation — operator-grade, not demos.

#### Technical Evaluators

**Pain:** "I need to know this is production-grade."

**Promise:** Strict TypeScript. Zod validation. Retry with backoff. Zero-retry mutation safety. 95 offline tests + 19 live API tests. 2 runtime dependencies.

**Proof:** ARCHITECTURE.md, test suite, npm package internals.

### Competitive Position

1. **First AI operating interface for Circle.so** — no direct competitor
2. **Intelligence, not just access** — derived tools go beyond raw API calls
3. **Operator-first** — built for community professionals, not integration developers
4. **Non-destructive by design** — create and update only
5. **Privacy-preserving** — runs locally, no third-party intermediary

### One-Liners

**Social:**
> Circle MCP: the AI operating interface for Circle.so. Operate your community through conversation — health checks, content audits, and content creation without the dashboard.

**Cold email opener:**
> What if you could audit a Circle community in 3 minutes instead of 3 hours?

**Conference slide:**
> Your AI assistant can reason about anything — except what's inside your SaaS tools. Circle MCP fixes that for Circle.so.

**README tagline:**
> The AI operating interface for Circle.so communities.

**Investor/partner:**
> We build the operating layer between AI and SaaS systems of record. Circle.so is the first. AI can think — but it can't act on your business tools without us.

---

## Part 5: Product-Level Template (For Future Verticals)

When launching a new product (e.g., Kajabi MCP), instantiate this template:

```
### Product Identity
[Product Name] is the AI operating interface for [Platform].

### Positioning Hierarchy
| Level | Statement |
|-------|-----------|
| Category | AI operating interface for [Platform] |
| Capability | [Domain] intelligence and operations through natural language |
| Mechanism | Production-grade MCP server with [N] validated tools |

### Audience Messaging
[Define 3-5 audience segments with Pain / Promise / Proof]

### Competitive Position
[5 differentiators specific to this vertical]

### One-Liners
[Social, email, conference, README, investor — one each]
```

The pillars (Part 2), terminology (Part 3), and company thesis (Part 1) stay the same across all products. Only Part 4 changes per vertical.

---

## Part 6: Usage Rules

1. **Never claim features that don't exist.** Every capability mentioned must be implemented and validated.
2. **Never hide limitations.** Honest boundaries build more trust than polished marketing.
3. **Lead with the operator's pain, not the product's features.** Dashboard fatigue → solution → proof.
4. **Use "AI operating interface" in headlines and first impressions.** Drop to technical language only in technical sections.
5. **Keep product names as two words.** "Circle MCP" — not "the Circle MCP Server" in buyer-facing copy.
6. **No guru tone.** No hype. No "game-changer." No "revolutionize." Practical, sharp, grounded.
7. **Reuse this framework.** Every new product gets a Part 4. Parts 1-3 and 5-6 are shared.
