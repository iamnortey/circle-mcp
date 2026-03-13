# Launch Announcements — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Lightweight public-facing launch copy for multiple channels

---

## Usage Notes

- These are drafts — adapt tone and length to the platform
- All claims reflect the actual live product state (v0.3.0, 16 tools, live endpoint)
- Do not overstate maturity — position as live, validated, premium, and guided
- Include the GitHub link in every post
- Optional: include a screenshot or short demo clip for visual platforms

---

## 1. LinkedIn Post

```
I built an open-source MCP server that connects Circle.so communities to Claude.

What it does:
→ 16 tools for community operations — health checks, content creation, member insights, unanswered post detection
→ Ask Claude "are there unanswered posts?" and get structured answers in seconds
→ Create announcements and content from a conversation — no admin dashboard clicking
→ Runs locally or via a hosted endpoint — your choice

Who it is for:
→ Circle consultants and agencies: audit a client community in 5 prompts instead of 2 hours
→ Community operators: daily health checks, content ops, and engagement monitoring from Claude
→ Educators: find unanswered student questions, post lessons, manage course spaces

Technical highlights:
→ TypeScript, strict Zod validation, zero external HTTP dependencies
→ Dual transport: stdio for local, Streamable HTTP for remote
→ Non-destructive: create and update only, no delete operations
→ Retry with backoff, rate-limit handling, mutation safety

The server is live, validated, and open source. I am offering guided setup and training for Circle professionals who want to add AI-powered community intelligence to their workflow.

GitHub: https://github.com/iamnortey/circle-mcp

If you work with Circle.so and want to see what this looks like in practice — DM me for a quick demo.

#MCP #CircleSo #CommunityOperations #AI #OpenSource
```

---

## 2. X / Twitter Post

### Main Post

```
I built an open-source MCP server for Circle.so communities.

16 tools. Health checks, content creation, unanswered post detection, member insights — all from Claude.

Live, validated, dual transport (local + remote).

For Circle consultants, operators, and educators who want AI leverage.

GitHub: https://github.com/iamnortey/circle-mcp
```

### Thread (optional follow-ups)

```
Thread:

1/ What it does: connects your Circle community to Claude. Ask natural language questions, get structured answers, create content — no admin dashboard.

2/ For consultants: audit a client community in 5 prompts. Health snapshot, unanswered posts, space inventory, content gaps — all in one conversation.

3/ For operators: daily health checks, find questions that need answers, publish announcements — from Claude instead of the Circle UI.

4/ Technical: TypeScript, Zod validation, retry with backoff, non-destructive writes, zero external HTTP deps. Production-grade, not a prototype.

5/ It is live and open source. I offer guided setup for professionals who want it running fast.

DM me for a demo or check the repo: https://github.com/iamnortey/circle-mcp
```

---

## 3. GitHub / GitHub Discussions Announcement

### Title: Circle MCP Server v0.3.0 — AI-Powered Community Operations for Circle.so

```markdown
## Circle MCP Server v0.3.0

An MCP server that connects Circle.so communities to Claude and any MCP-compatible AI client.

### What's New in v0.3.0

- **16 tools** (13 read + 3 write) — spaces, posts, members, comments, topics, search, community health, unanswered post detection, content creation, and content updates
- **Dual transport** — stdio for local Claude Desktop, Streamable HTTP for remote/team access
- **Live hosted endpoint** — connect from Claude Desktop or Claude Code without running your own server
- **Non-destructive writes** — create and update posts, create comments. No delete or archive operations.
- **Production resilience** — retry with exponential backoff, rate-limit handling, response truncation, mutation safety

### Who It's For

- Circle consultants and agencies: audit communities, find content gaps, push announcements
- Community operators: daily health checks, unanswered post detection, content workflows
- Educators: find student questions, post lessons, manage course spaces
- AI-native founders: run community operations from Claude instead of hiring a community manager

### Getting Started

**Local (stdio):**
```bash
git clone https://github.com/iamnortey/circle-mcp-server.git
cd circle-mcp-server
npm install && npm run build
```

**Remote (HTTP):**
Add to Claude Desktop config:
```json
{
  "mcpServers": {
    "circle": {
      "url": "https://circle-mcp-server-production.up.railway.app/mcp"
    }
  }
}
```

### Documentation

- [Client Quickstart](docs/product/CLIENT_QUICKSTART.md)
- [Prompt Starter Pack](docs/product/PROMPT_STARTER_PACK.md)
- [Tool Reference](docs/tools.md)

### Guided Setup Available

I offer professional setup and training for Circle operators, consultants, and agencies who want this running quickly with workflows tailored to their community. DM or open an issue if interested.

### Feedback Welcome

This is v0.3.0. If you try it, I want to hear what works, what breaks, and what you wish it did. Open an issue or reach out directly.
```

---

## 4. Founder Note / Plain-Text Launch Note

This format works for email newsletters, personal blogs, or direct sharing with a curated list.

```
Subject: I shipped Circle MCP Server — connecting Circle.so communities to Claude

---

I have been building Circle MCP Server for the past few months. It is now live.

What it is:
An open-source MCP server that connects Circle.so communities to Claude. You type natural language — it translates into Circle Admin API calls. 16 tools covering community health, content operations, member insights, and engagement monitoring.

Why I built it:
Circle operators spend hours clicking through admin dashboards doing repetitive work — health checks, finding unanswered posts, creating content, reviewing spaces. Most of this can be done faster with AI if the right infrastructure exists. It did not exist for Circle. So I built it.

Where it is now:
- Live on Railway with a public endpoint
- Open source on GitHub
- 95 tests passing, 19 live API tests validated
- Dual transport: local and remote
- Ready for Smithery marketplace submission

What I am doing with it:
I am offering guided setup and training for Circle professionals — consultants, agencies, operators, and educators — who want AI-powered community operations.

If you work with Circle.so, I would love to show you what this looks like. A 15-minute demo is all it takes.

GitHub: https://github.com/iamnortey/circle-mcp
DM me on LinkedIn or reply to this message.

— Isaac
```

---

## 5. Short Community Post

For posting inside Circle communities, Slack groups, Discord servers, or other operator communities.

```
Hey everyone — I built an open-source tool that connects Circle.so communities to Claude (via the MCP protocol).

You can:
• Run community health checks in one prompt
• Find unanswered posts across all spaces
• Create and update posts from Claude
• Search your community for any topic
• Get member, comment, and space data without the admin dashboard

16 tools, live hosted endpoint, works locally too.

GitHub: https://github.com/iamnortey/circle-mcp

If you manage a Circle community and want to try it, DM me — happy to walk you through a quick demo.
```

---

## Tone Calibration

| Do | Do Not |
|----|--------|
| State concrete capabilities (16 tools, health checks, unanswered detection) | Claim "revolutionary" or "game-changing" |
| Say "live and validated" | Say "production-ready at scale" |
| Offer demos and guided setup | Claim zero-touch onboarding |
| Be specific about who benefits | Claim it is for "everyone" |
| Mention open source and GitHub | Hide the fact that setup requires configuration |
| Acknowledge v0.3.0 maturity | Pretend it is a complete platform |
