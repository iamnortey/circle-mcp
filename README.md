# Circle.so MCP Server

> Your Circle.so operator copilot for Claude Desktop — understand, manage, and grow your community with natural language.

**16 tools** | **13 read + 3 write** | **Claude Desktop ready** | **v0.3.0**

---

## What It Does

Circle.so MCP connects your Circle community to Claude Desktop. Instead of clicking through the Circle admin UI to find answers, you ask Claude — and it queries your community data, surfaces insights, and creates content on your behalf.

**Draft with Claude. Operate in Circle.so via MCP.**

Ask things like:
- *"What spaces do I have and which ones are most active?"*
- *"Are there any unanswered posts in my community?"*
- *"Give me a health snapshot — how many members, posts, spaces?"*
- *"Create an announcement post in the Welcome space"*
- *"Update the title of post #12345"*
- *"Search for discussions about onboarding"*
- *"Show me who joined this month"*

The server translates natural language into structured Circle Admin API calls, handles pagination and retries, and returns clean results — so you stay in Claude instead of context-switching to the Circle dashboard.

---

## What It Helps You Do

### Understand your community faster

See your spaces, posts, members, comments, and topics without manually browsing the Circle UI. Get a point-in-time health snapshot that aggregates member counts, post counts, and space activity into a single view.

### Find gaps and neglected content

Detect unanswered posts — questions that have zero comments. Identify spaces that may need attention. Surface content that is published but getting no engagement.

### Create and update content from Claude

Draft posts in Claude and publish them directly to any space. Update titles, bodies, and settings on existing posts. Use Claude to help you write onboarding copy, lesson outlines, or community announcements — then push them to Circle without leaving your workflow.

### Operate more efficiently

Stop clicking through admin menus to answer simple questions. Use Claude as a command layer over your Circle community. Combine community intelligence with content operations in one conversational interface.

---

## Who It's For

### Community operators

You run a Circle.so community and spend too much time in the admin UI answering basic questions: *Which posts have no replies? How many members joined this week? What does my space structure look like?* This tool gives you those answers in Claude Desktop, instantly.

### Agencies and Circle experts

You manage client communities and need to audit, assess, or improve them quickly. Instead of manually crawling a client's Circle instance, you connect via MCP and get a structured view of their spaces, content gaps, and community health — in minutes, not hours.

### Educators and cohort builders

You use Circle.so for courses, learning communities, or academy-style products. You need to draft lesson posts, update announcements, check which discussions need responses, and keep your learning spaces active. This tool lets you do that from Claude without switching between tabs.

### Founders and solo operators

You are building a community-powered product and don't have a dedicated community manager. You need leverage. This tool helps you see what needs attention, publish updates faster, and avoid letting your community go quiet — all from one conversation in Claude.

---

## What It Does Not Do Yet

Honesty matters. Here is what v0.3.0 does **not** cover:

- **No moderation workflows** — cannot hide, flag, or remove content
- **No destructive actions** — no delete, archive, or bulk removal operations
- **No member management** — cannot ban, invite, change roles, or update member profiles
- **No space or community settings** — cannot create spaces, change permissions, or modify community config
- **No guaranteed comment creation** — the Circle Admin API restricts comment creation with admin tokens on some setups (returns 401)
- **No historical analytics** — health snapshots are point-in-time, not trend data
- **No event or course tools** — planned for v0.4
- **No file/image uploads** — posts are text/HTML only

These limitations are by design for safety (non-destructive) or by Circle API constraints. They are on the roadmap.

---

## Use Cases

### For agencies auditing a client community

> *"Show me all spaces in this community"* → See the full structure.
> *"Which posts have no comments?"* → Find neglected content.
> *"Give me a health snapshot"* → Get member/post/space counts in one view.
> *"Search for posts about onboarding"* → Find what exists before creating new content.

### For educators managing a learning community

> *"List posts in the Week 1 space"* → Review lesson content.
> *"Create a new post in Announcements"* → Push a cohort update.
> *"Update the body of post #456"* → Fix a lesson post without opening Circle.
> *"Are there unanswered questions in the Q&A space?"* → Find students who need help.

### For founders keeping a community alive

> *"What happened in my community this week?"* → Quick health check.
> *"Create a welcome post for new members"* → Draft and publish from Claude.
> *"Show me recent posts sorted by latest"* → See what is getting attention.
> *"Find members who joined recently"* → Understand growth.

### For operators improving community quality

> *"Detect unanswered posts across all spaces"* → Find gaps systematically.
> *"List topics in my community"* → Understand content taxonomy.
> *"Get community metadata"* → See branding, locale, and settings.
> *"Search for discussions about pricing"* → Audit how a topic is being discussed.

---

## Architecture

```
┌─────────────────────────────────────────────┐
│              Claude Desktop                  │
│         (or any MCP client)                  │
└──────────────┬──────────────────────────────┘
               │ stdio (JSON-RPC)
┌──────────────▼──────────────────────────────┐
│           MCP Server Layer                   │
│  ┌────────────────────────────────────────┐  │
│  │    16 Registered Tool Handlers        │  │
│  │  (validation, response formatting)     │  │
│  └──────────────┬─────────────────────────┘  │
│  ┌──────────────▼─────────────────────────┐  │
│  │         Circle API Client              │  │
│  │  (typed methods, pagination support)   │  │
│  └──────────────┬─────────────────────────┘  │
│  ┌──────────────▼─────────────────────────┐  │
│  │          HTTP Layer                    │  │
│  │  (auth, timeout, retry/backoff,       │  │
│  │   mutation path with zero-retry)       │  │
│  └────────────────────────────────────────┘  │
└──────────────────────────────────────────────┘
               │ HTTPS
┌──────────────▼──────────────────────────────┐
│         Circle Admin API v2                  │
│        (app.circle.so)                       │
└──────────────────────────────────────────────┘
```

**Key design decisions:**

- **Zero external HTTP dependencies** — uses Node.js native `fetch` (Node 18+)
- **Strict TypeScript** throughout — no `any` types, Zod schema validation on all inputs
- **Fail-fast configuration** — missing API token produces an actionable error immediately
- **Structured + text responses** — every tool returns both human-readable text and machine-parseable `structuredContent`
- **Non-destructive write tools** — create and update operations only, no delete/archive (v0.3.0)

See [ARCHITECTURE.md](./ARCHITECTURE.md) for the full design breakdown.

## Tools

Sixteen tools organized in four tiers:

### Core Read Tools (v0.1.0)

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `circle_list_spaces` | List community spaces | pagination, 7 sort options |
| `circle_get_space` | Get a single space by ID | `space_id` (required) |
| `circle_list_posts` | List posts with filtering | space, status, text search, sort |
| `circle_get_post` | Get a single post with full body | `post_id` (required) |
| `circle_list_members` | List community members | pagination, status filter |
| `circle_search` | Search across the community | `query` (required), type filter |

### Extended Read Tools (v0.2.0)

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `circle_list_comments` | List comments on a post | `post_id`, `space_id`, pagination |
| `circle_get_comment` | Get a single comment by ID | `comment_id` (required) |
| `circle_list_topics` | List topics/tags in the community | pagination |
| `circle_get_community` | Get community metadata | _(no params)_ |
| `circle_list_space_groups` | List space groups with space IDs | pagination |

### Derived Intelligence Tools (v0.2.0)

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `circle_detect_unanswered_posts` | Find posts with zero comments | `space_id` (optional filter) |
| `circle_community_health` | Point-in-time community health snapshot | _(no params)_ |

### Write Tools (v0.3.0)

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `circle_create_post` | Create a new post in a space | `space_id`, `name`, `body` (required) |
| `circle_update_post` | Update an existing post by ID | `post_id` (required), all other fields optional |
| `circle_create_comment` | Create a comment on a post | `post_id`, `body` (required) |

See [docs/tools.md](./docs/tools.md) for the complete tool reference.

## Resilience

| Feature | Behavior |
|---------|----------|
| **Retry with backoff** | Transient failures (429, 5xx) retried up to 2 times with exponential delay |
| **Retry-After support** | Respects Circle's `Retry-After` header on rate-limit responses |
| **Response truncation** | Large payloads capped at 100K characters with structured fallback |
| **Timeout protection** | 30-second request timeout prevents hanging |
| **Actionable errors** | Every error includes specific guidance (e.g., "Verify your API token") |
| **Mutation safety** | Write operations use zero-retry policy to prevent duplicates |
| **Permission-aware handling** | Comment 401 returns structured workaround instead of raw error |

## Technical Stack

| Component | Technology |
|-----------|------------|
| Runtime | Node.js ≥ 18 (native `fetch`) |
| Language | TypeScript (strict mode) |
| Protocol | MCP (Model Context Protocol) v2025-03-26 |
| Transport | stdio (JSON-RPC over stdin/stdout) |
| Validation | Zod with `.strict()` schemas |
| External API | Circle Admin API v2 |
| Testing | 95 offline tests + 19 live API tests |
| CI | GitHub Actions (Node 18/20/22 matrix) |

## Release Status

**Current version: v0.3.0** — Safe Content Operations

This is a stable, validated release supporting 16 tools (13 read + 3 write). The server has been tested against live Circle communities with 95 offline tests and 19 live API tests.

### Roadmap

| Version | Scope |
|---------|-------|
| v0.1.0 | Read-only tools (spaces, posts, members, search) |
| v0.2.0 | Community intelligence (comments, topics, community, space groups, derived analytics) |
| v0.3.0 | Safe content operations (create/update posts, create comments) — **current** |
| v0.4 | Events, courses, and expanded write tools |
| v0.5 | Webhook subscriptions via MCP resources |

## Getting Started

### Prerequisites

- **Node.js** ≥ 18.0.0
- **Circle Admin API token** — obtain from Circle Admin → Settings → API
- **Claude Desktop** (or any MCP-compatible client)

### Installation

```bash
git clone https://github.com/iamnortey/circle-mcp.git
cd circle-mcp/app/circle-mcp-server
npm install
npm run build
```

### Claude Desktop Configuration

Add to your Claude Desktop config (`~/Library/Application Support/Claude/claude_desktop_config.json` on macOS):

```json
{
  "mcpServers": {
    "circle": {
      "command": "node",
      "args": ["/path/to/circle-mcp/app/circle-mcp-server/dist/index.js"],
      "env": {
        "CIRCLE_API_TOKEN": "your-circle-admin-api-token"
      }
    }
  }
}
```

Restart Claude Desktop. The Circle.so tools will appear automatically.

## Distribution

This project is distributed via **GitHub** as the primary channel. Clone the repository and run the server locally with Claude Desktop.

**npm distribution** is planned for a future release once the API surface stabilizes further.

## License

MIT
