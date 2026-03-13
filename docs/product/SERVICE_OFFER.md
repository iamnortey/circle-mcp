# Service Offer — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Define the productized service package for Circle MCP guided setup and training

---

## Overview

Circle MCP is a community intelligence and operations copilot for Circle.so, powered by Claude. For v1 (the current release), the primary commercial motion is a **productized service**: guided setup, configuration, and training for Circle operators, consultants, and agencies who want AI-powered community audits, engagement monitoring, and content operations.

This document defines what that service includes, who it is for, and how it is delivered.

---

## The Offer

### AI-Powered Community Intelligence for Circle.so

> We set up Circle MCP Server for your community, train your team on what it can do, and hand over a working AI community operations layer — so you can run health checks, find content gaps, and publish content from Claude instead of clicking through the Circle admin dashboard.

---

## What Is Included

### 1. Setup and Configuration

| Deliverable | Detail |
|-------------|--------|
| Server installation | Clone, build, and configure Circle MCP Server for the client's environment |
| API token setup | Guide client through Circle Admin API token generation and secure storage |
| Claude Desktop config | Configure the client's Claude Desktop to connect to their Circle community |
| Verification | Run health check and tool validation to confirm the connection is live |
| Remote endpoint (optional) | Deploy hosted HTTP endpoint on Railway for remote/team access |

### 2. Training Session

| Deliverable | Detail |
|-------------|--------|
| Live walkthrough | 60-minute guided session covering all 16 tools and common workflows |
| Prompt starter pack | Delivered copy of [PROMPT_STARTER_PACK.md](./PROMPT_STARTER_PACK.md) with ready-to-use prompts |
| Workflow templates | Demonstrate 3-5 workflows tailored to the client's community use case |
| Q&A | Answer questions about capabilities, limitations, and best practices |

### 3. Documentation Handoff

| Deliverable | Detail |
|-------------|--------|
| Client quickstart guide | Personalized version of [CLIENT_QUICKSTART.md](./CLIENT_QUICKSTART.md) with their specific paths and tokens referenced |
| Tool reference | Link to the full [16-tool reference](../tools.md) |
| Limitations guide | Honest documentation of what v0.3.0 does and does not cover |
| Support channel | Defined point of contact for post-setup questions |

---

## Who This Is For

### Best Fit

- Community consultants who manage client Circle communities
- Agencies with 3+ Circle communities under management
- Community strategists at companies with active Circle.so usage
- Educators running courses or cohorts on Circle.so

### Not a Fit

- Non-technical operators who cannot generate a Circle Admin API token
- Technical users comfortable with self-serve setup (see the [install guides](../self-serve/) — you may not need this service)
- Operators who need destructive operations (delete, archive, moderation)
- Communities not on Circle.so

---

## Delivery Model

### Standard Package

| Phase | Duration | Description |
|-------|----------|-------------|
| Pre-call | 15 min | Client generates Circle Admin API token and installs Node.js |
| Setup | 30 min | Install, configure, and verify the MCP server connection |
| Training | 60 min | Live walkthrough of tools, workflows, and prompt templates |
| Handoff | 15 min | Deliver documentation, confirm access, set up support channel |
| **Total** | **~2 hours** | |

### Premium Package (Adds Remote Deployment)

| Phase | Duration | Description |
|-------|----------|-------------|
| All Standard phases | ~2 hours | As above |
| Remote deployment | 30 min | Deploy hosted HTTP endpoint on Railway, configure env vars, validate |
| Team onboarding | 30 min | Configure multiple team members' Claude Desktop instances |
| **Total** | **~3 hours** | |

---

## Pricing Guidance

Pricing is at the discretion of the service provider. These are reference points:

| Package | Suggested Range | Basis |
|---------|----------------|-------|
| Standard | $500 – $1,000 | ~2 hours delivery + documentation |
| Premium | $1,000 – $2,000 | ~3 hours delivery + remote deployment + team onboarding |
| Retainer (optional) | $200 – $500/month | Ongoing support, prompt updates, version upgrades |

Value justification: the tool replaces 5-10 hours/week of manual Circle admin work for active operators. ROI is clear within the first month for any community professional billing $100+/hour.

---

## Objection Handling

| Objection | Response |
|-----------|----------|
| "Can't I just set this up myself?" | Absolutely — the project is open source on GitHub with full documentation. The service exists for operators who want it done right the first time, with training and ongoing support. |
| "What if the Circle API changes?" | The server is actively maintained. Version updates include API compatibility fixes. Retainer clients receive priority updates. |
| "Does this work with [other AI tool]?" | Circle MCP works with any MCP-compatible client. Today that includes Claude Desktop and Claude Code. As more clients adopt MCP, the server works with them automatically. |
| "What about data security?" | The API token stays in your environment. Local setup means no data leaves your machine. The hosted endpoint is encrypted in transit (HTTPS) and the token is stored as an encrypted environment variable. |
| "Only 3 write tools?" | By design. v0.3.0 focuses on safe, non-destructive operations. Create and update posts are fully operational. Comment creation is included but subject to a Circle API limitation (admin tokens may return 401 — the tool handles this gracefully). Delete, archive, and moderation tools are on the roadmap with appropriate safety patterns. |

---

## What This Does Not Include

- Custom tool development or feature requests
- Circle.so community strategy consulting (content strategy, engagement planning)
- Circle Admin API token management or rotation beyond initial setup
- Ongoing monitoring or alerting (operator responsibility)
- Integrations with non-MCP tools (Zapier, Make, custom APIs)

---

## Success Criteria

A successful engagement means:

1. Client can run `circle_community_health` and get accurate results
2. Client can create a post from Claude Desktop
3. Client can find unanswered posts in their community
4. Client has the prompt starter pack and knows how to use at least 5 workflows
5. Client knows what the tool does and does not do (no surprises)
