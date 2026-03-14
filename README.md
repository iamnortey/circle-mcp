# Circle MCP

**Community intelligence and operations copilot for Circle.so, powered by Claude.**

Inspect, diagnose, and operate your Circle community through natural language. Run community audits, detect unanswered questions, assess onboarding health, identify content gaps, and publish content — all from Claude Code or Claude Desktop.

**16 tools** | **13 read + 3 write** | **Non-destructive** | **v0.3.1** | **MIT License**

---

## Why It Matters

Circle operators spend hours clicking through admin menus to answer basic questions: *Which spaces are empty? Are members getting answers? What does our community structure actually look like?*

Circle MCP replaces that with natural language. Ask Claude, get answers. No scripting, no dashboard switching, no manual exports.

This is not just an API wrapper. The server includes derived intelligence tools — community health snapshots, unanswered post detection, content gap analysis — that aggregate multiple API calls into actionable operator insights.

---

## Who It's For

- **Community managers** running daily health checks and content operations
- **Consultants and agencies** auditing client communities and delivering insights
- **Educators** managing course spaces, responding to student questions, and publishing lesson content
- **Developer advocates** monitoring community engagement and building MCP-based workflows
- **AI-native founders** keeping their community alive without a dedicated community team

**Requirements:** Git, Node.js >= 18, terminal comfort, and a Circle Admin API token.

---

## Flagship Workflows

These workflows have been validated against live Circle communities:

### Community Architecture Audit

> "List my Circle spaces grouped by space groups. Identify any structural or onboarding issues."

Produces a full inventory of spaces, space groups, types, URLs, and icons. Surfaces empty spaces, unclear naming, and structural gaps.

### Onboarding Audit

> "Inspect my onboarding-related spaces and recommend improvements."

Reviews Getting Started spaces, welcome flows, and introductory content. Identifies where new members might get stuck or lose context.

### Community Health Snapshot

> "Generate a community health snapshot and highlight engagement risks."

Aggregates member counts, post volume, space activity, and unanswered questions into a single operator briefing.

### Unanswered Post Detection

> "Detect unanswered posts and prioritize what needs operator attention."

Scans all spaces (or a specific space) for posts with zero comments. Surfaces neglected questions and engagement gaps.

### Content Gap Analysis

> "Identify empty or underutilized spaces that weaken the member experience."

Cross-references space structure against post activity and engagement. Finds content deserts and recommends where to invest effort.

### Seed Content Planning

> "Based on the community audit, suggest 5 seed posts for the spaces that need content most."

Uses audit insights to recommend specific content for underserved spaces. Pairs with the write tools to draft posts immediately.

### Safe Content Operations

> "Create a draft post in the Announcements space titled 'Q2 Update' with our recent milestones."

Create posts, update existing content, and add comments — all through Claude. Posts default to draft status for review before publishing.

---

## Quick Start

### 1. Get your Circle API token

Log in to your Circle community as an admin. Go to **Settings** > **API** > **Generate new token**.

### 2. Clone and build

```bash
git clone https://github.com/iamnortey/circle-mcp-server.git
cd circle-mcp-server
npm install && npm run build
```

### 3. Connect to Claude

| Client | Guide |
|--------|-------|
| **Claude Code** (recommended) | [Claude Code Install Guide](docs/self-serve/CLAUDE_CODE_INSTALL.md) |
| **Claude Desktop** | [Claude Desktop Install Guide](docs/self-serve/CLAUDE_DESKTOP_INSTALL.md) |

### 4. Verify the connection

```
Give me a health snapshot of my Circle community
```

If you see your community name and stats, you're connected.

---

## Tools

16 tools across four tiers:

| Tier | Tools | Purpose |
|------|-------|---------|
| **Core Read** (v0.1.0) | `list_spaces`, `get_space`, `list_posts`, `get_post`, `list_members`, `search` | Community data access |
| **Extended Read** (v0.2.0) | `list_comments`, `get_comment`, `list_topics`, `get_community`, `list_space_groups` | Full API surface |
| **Intelligence** (v0.2.0) | `detect_unanswered_posts`, `community_health` | Aggregated operator insights |
| **Write** (v0.3.0) | `create_post`, `update_post`, `create_comment` | Content management |

All tools are prefixed with `circle_`. No delete operations are exposed. See [Tool Reference](docs/tools.md) for full parameters and examples.

---

## Example Prompts

**Operator diagnostics:**
> "Show me all spaces grouped by space group. Which spaces have no recent posts?"

**Engagement monitoring:**
> "Are there unanswered posts in the Q&A space? Prioritize by age."

**Content audit:**
> "Search for posts about onboarding. What content exists and where are the gaps?"

**Member insights:**
> "How many members joined this month? Show me the most recent signups."

**Content creation:**
> "Create a draft post in Announcements titled 'Weekly Update' summarizing this week's activity."

**Community briefing:**
> "Give me a summary I can present in a client meeting: health metrics, unanswered posts, and recent activity."

See [Prompt Starter Pack](docs/product/PROMPT_STARTER_PACK.md) for 30+ ready-to-use workflows.

---

## Validation Status

Circle MCP has been validated in production with both Claude Code and Claude Desktop:

- **Read path:** Community health snapshots, full space inventories with icons and URLs, unanswered post detection, content gap analysis, onboarding audits, and strategic recommendations — all confirmed working against live Circle communities.
- **Write path:** Post creation (draft and published) and post updates confirmed working. Draft-first workflow verified as safe for production use. Comment creation is exposed but subject to a known Circle API limitation (admin tokens return 401 — see Limitations below).
- **Claude Desktop:** Successfully loaded and authenticated. All 16 tools visible and operational.
- **Test suite:** 95 offline tests + 19 live API tests passing.

---

## Limitations (v0.3.1)

- No destructive operations (delete, archive, bulk removal)
- No moderation (hide, flag, remove content)
- No member management (ban, invite, change roles)
- No space or community settings changes
- No events or courses (planned for v0.4)
- No historical analytics (snapshots are point-in-time)
- No file or image uploads

**Write response caveat:** Circle's API may not fully echo rendered body content in the immediate response payload after a successful write. The post is created/updated correctly, but the API response may omit or truncate the body field. This is a Circle API behavior, not a server defect. Verify by fetching the post after creation if you need to confirm the full rendered content.

**Comment creation caveat:** The `circle_create_comment` tool is included in v0.3.0, but Circle's Admin API v2 returns 401 ("You cannot perform this action") for comment creation with admin tokens. The endpoint exists and the tool handles this error gracefully with a clear message and workaround (create comments via the Circle web interface). Read operations for comments (`circle_list_comments`, `circle_get_comment`) work normally. This is a Circle API permission restriction, not a server defect.

---

## Troubleshooting

### "Authentication failed" or empty results

Your Circle Admin API token may be expired or incorrect.

1. Generate a new token in Circle Admin > Settings > API
2. Update your configuration:
   - **Claude Code:** `claude mcp remove circle` then re-add with the new token
   - **Claude Desktop:** Edit `claude_desktop_config.json` and replace the token value, then restart Claude Desktop

### Stale token after environment changes

If you update your Circle API token in a remote deployment (e.g., Railway environment variables), that change does **not** propagate to your local Claude Desktop or Claude Code configuration. Local MCP configs store the token independently. You must update the token in both places.

### Tools not appearing

Ensure the build completed successfully (`npm run build` should produce a `dist/` directory). Check that the path in your Claude configuration points to the correct `dist/index.js` file.

### Slow responses

The Circle Admin API occasionally has latency spikes. The server handles this with a 30-second timeout and automatic retries for read operations. If it persists, check [Circle platform status](https://status.circle.so).

---

## How It Works

You bring your own Circle Admin API token. The MCP server runs locally on your machine via stdio transport. No data passes through third-party infrastructure. Each Circle community requires its own token.

```
Claude Code / Claude Desktop
        | stdio
Circle MCP Server (local)
        | HTTPS
Circle Admin API v2
```

---

## Documentation

| Document | Purpose |
|----------|---------|
| [Claude Code Install Guide](docs/self-serve/CLAUDE_CODE_INSTALL.md) | Step-by-step Claude Code setup with troubleshooting |
| [Claude Desktop Install Guide](docs/self-serve/CLAUDE_DESKTOP_INSTALL.md) | Step-by-step Claude Desktop setup with troubleshooting |
| [Client Quickstart](docs/product/CLIENT_QUICKSTART.md) | Overview of all setup paths |
| [Prompt Starter Pack](docs/product/PROMPT_STARTER_PACK.md) | 30+ ready-to-use prompts for operator workflows |
| [Tool Reference](docs/tools.md) | Full parameter reference for all 16 tools |
| [Server README](https://github.com/iamnortey/circle-mcp-server#readme) | Technical reference (architecture, resilience, internals) |
| [Documentation Index](docs/README.md) | Full docs navigation |

---

## Advanced Options

### Remote HTTP Transport (Experimental)

For team deployments or agency setups where multiple clients share an endpoint. Deploy to Railway, Render, or any Node.js host and run `npm run start:http`. See [deployment docs](docs/internal/deployment/) for details.

The HTTP transport has no authentication layer and should not be exposed to untrusted networks.

### Smithery Marketplace

This server is listed on [Smithery](https://smithery.ai/servers/iamnortey/circle-so-mcp). Smithery manages configuration and launches the server via stdio.

### Guided Setup Services

Need help installing or operationalizing Circle MCP? Guided setup, training, and managed deployment services are available. See [SERVICE_OFFER.md](docs/product/SERVICE_OFFER.md) for details.

---

## Technical Details

- **Language:** TypeScript (strict mode)
- **Runtime:** Node.js >= 18 (native `fetch`)
- **MCP SDK:** `@modelcontextprotocol/sdk` v1.12.1
- **Validation:** Zod v3.24 with `.strict()` schemas
- **Transport:** stdio (primary, production-tested), Streamable HTTP (experimental)
- **Test suite:** 95 offline tests + 19 live API tests
- **Dependencies:** 2 runtime, 3 dev

---

## Roadmap

- **v0.4:** Events, courses, and expanded write tools
- **v0.5:** Webhook subscriptions and real-time event streaming
- **Future:** npm global install, multi-community support, file uploads, moderation tools

See [Roadmap](docs/roadmap.md) for details.

---

## License

MIT — see [LICENSE](LICENSE) for details.

## Contributing

Issues and pull requests welcome at [github.com/iamnortey/circle-mcp-server](https://github.com/iamnortey/circle-mcp-server).
