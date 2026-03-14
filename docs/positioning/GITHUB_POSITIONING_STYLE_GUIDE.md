# GitHub Positioning Style Guide

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Status:** Internal — applies to all repos in this portfolio
> **Purpose:** Default voice, structure, and terminology rules for GitHub project presentation

---

## Default Voice

Every repo in this portfolio should sound like it was built by someone who:

- ships real infrastructure, not weekend experiments
- builds for operators, not just developers
- cares about the problem more than the technology
- earns trust through honesty about limitations
- values clarity over cleverness

The tone is: **precise, practical, premium, anti-hype.**

Not corporate. Not casual. Not academic. Just clear.

---

## README Structure

Lead with the problem and the value. Drop into technical detail later.

### Recommended Section Order

```
1. Product name + one-line positioning statement
2. One-paragraph explanation of what it does and why
3. Stats bar (version, capabilities, key differentiators)
4. ---
5. Why It Matters (the problem, in the operator's words)
6. Who It's For (roles, not personas — be specific)
7. What You Can Do (validated workflows, not feature lists)
8. Quick Start (minimal steps to first value)
9. Tools / Capabilities (technical reference)
10. Example Prompts / Usage (concrete, copy-pasteable)
11. Validation Status (proof, not promises)
12. Limitations (honest, trust-building)
13. Troubleshooting
14. How It Works (architecture for the curious)
15. Documentation links
16. Advanced Options
17. Technical Details (stack, dependencies)
18. Roadmap
19. License + Contributing
```

### The Opening Pattern

The first three elements a visitor sees determine whether they stay or leave.

**Line 1 — Product name.** Just the name. No tagline in the H1.

**Line 2 — One-line positioning statement.** Bold. Describes the value category, not the implementation.

**Line 3 — One-paragraph description.** What it does, framed as operator outcomes. End with what it replaces (the old way).

**Line 4 — Stats bar.** Version first, then key capabilities, then trust signals.

#### Strong opening:

```markdown
# Circle MCP

**The AI operating interface for Circle.so communities.**

Inspect, diagnose, and operate your Circle community through natural language.
Run community audits, detect unanswered questions, assess onboarding health,
identify content gaps, and publish content — without switching between dashboards.

**v0.3.1** | **16 tools** | **Non-destructive writes** | **Works with Claude Code & Claude Desktop** | **MIT License**
```

#### Weak opening:

```markdown
# Circle MCP Server

An MCP server for Circle.so that provides 16 tools for reading and writing
community data through the Circle Admin API v2.

Built with TypeScript, Zod validation, and the MCP SDK.
```

The weak version leads with protocol, describes implementation, and tells you nothing about why it exists or who it's for.

---

## Language Rules

### Lead with the problem

Before describing what the product does, name the pain it eliminates. Operators recognize their own frustrations faster than they evaluate feature lists.

| Weak | Strong |
|------|--------|
| "16 tools for Circle.so data access" | "Community operators lose hours to dashboard clicking" |
| "Provides API access via MCP" | "Eliminates the dashboard tax" |
| "Reads and writes Circle data" | "Inspect, diagnose, and operate through conversation" |

### Use operator language

Write for the person doing the work, not the person evaluating the architecture.

| Weak | Strong |
|------|--------|
| users | operators |
| data access | community intelligence |
| tool calls | natural language operations |
| API surface | data surface |
| content management | content operations |

### Keep technical language in technical sections

MCP, stdio, JSON-RPC, Zod — all fine in install guides, architecture docs, and technical reference sections. Not fine in the opening paragraph or the "Why It Matters" section.

### Product name vs technical name

| Context | Use |
|---------|-----|
| Buyer-facing copy, headlines, descriptions | **Circle MCP** (product name) |
| Technical docs, architecture, install guides | **Circle MCP Server** (technical name, acceptable) |
| Never | "the Circle MCP server product", "our MCP solution" |

---

## Terms to Prefer

| Term | When |
|------|------|
| AI operating interface | Headlines, first impressions, positioning |
| operator | Always (instead of "user") |
| dashboard fatigue | Naming the pain |
| thinking layer | Describing AI clients (Claude, etc.) |
| system of record | Describing the target SaaS platform |
| content operations | Describing write capabilities |
| community intelligence | Describing derived insights |
| non-destructive | Describing safety posture |
| infrastructure-grade | Describing quality level |

## Terms to Minimize

| Term | When Acceptable |
|------|-----------------|
| MCP / MCP server | Technical sections, install guides, architecture |
| connector | Never in primary positioning |
| wrapper | Never |
| copilot | Never — commoditized |
| powered by [AI brand] | Never — positions product as dependent |
| smart assistant | Never — vague |

## Terms to Avoid

| Term | Why |
|------|-----|
| cognitive infrastructure | Too abstract |
| cognitive middleware | Same |
| agentic operations | Jargon |
| nervous system | Metaphor overreach |
| workflow intelligence platform | Buzzword soup |
| game-changer / revolutionary | Hype |
| 10x / unlock / supercharge | Growth-hack language |

---

## Limitations Section

Every repo should have an honest limitations section. This is not a weakness — it's a trust signal.

Rules:
- List what the product does NOT do
- Be specific (not "some features are limited")
- If a limitation is caused by a third-party API, say so clearly
- If a limitation is by design (e.g., no delete operations), explain why
- Put limitations above the fold, not buried at the bottom

---

## Validation Section

Claims must be backed by evidence. Include a validation section that specifies:

- What was tested (read path, write path, specific clients)
- How it was tested (offline tests, live API tests)
- Test counts (specific numbers, not "comprehensive")
- Known caveats from testing

Never say "fully tested" or "production-ready" without specific evidence.

---

## Review Checklist

Use this before publishing or updating any repo in the portfolio.

### First Impression (< 10 seconds)

- [ ] Does the opening line describe value, not implementation?
- [ ] Can a non-technical operator understand what this does within 5 seconds?
- [ ] Is the product name used consistently (not switching between product and technical names)?
- [ ] Does the stats bar lead with version and capabilities, not internal metrics?

### Positioning (< 30 seconds)

- [ ] Does "Why It Matters" name the pain before describing the solution?
- [ ] Does "Who It's For" list specific operator roles, not vague categories?
- [ ] Are workflows described as outcomes, not feature lists?
- [ ] Is MCP/protocol language absent from the top half of the README?

### Technical Credibility

- [ ] Is there a clear architecture section for technical evaluators?
- [ ] Are test counts specific and honest?
- [ ] Is the limitations section present and forthright?
- [ ] Are technology choices documented with rationale?

### Terminology

- [ ] Zero uses of "copilot", "wrapper", "connector" in buyer-facing copy?
- [ ] Zero uses of "powered by [AI brand]"?
- [ ] "operator" used instead of "user" throughout?
- [ ] MCP only appears in technical sections?

### Trust Signals

- [ ] Validation section with specific test counts?
- [ ] Honest limitations section?
- [ ] MIT license clearly stated?
- [ ] Privacy/security posture described?

### Attribution

- [ ] No AI co-authorship attribution in commits?
- [ ] Sole contributor is the project maintainer?
- [ ] No "built with AI" badges or disclaimers?
