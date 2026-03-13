# GitHub Listing Polish — Recommendation Report

> Date: 2026-03-11
> Version: v0.3.0 — Safe Content Operations
> Author: Director (automated governance)

---

## 1. Naming Decision

### Recommendation: Keep Current Names

| Repo | Current Name | Recommendation |
|------|-------------|----------------|
| Private (source) | `circle-mcp-server` | **Keep** |
| Public (product) | `circle-mcp` | **Keep** |

### Reasoning

The current naming is strong and should not be changed:

- **`circle-mcp`** follows the dominant MCP ecosystem convention (`{platform}-mcp`). It is clean, readable, and memorable.
- **`circle-mcp-server`** clearly communicates that this is the implementation source, distinguishing it from the public-facing project.
- **`circle-so-mcp`** was evaluated but rejected. Adding "so" to the slug creates friction without meaningful discoverability gain. The platform brand "Circle.so" is better surfaced through descriptions, topics, and README content rather than repo slugs.
- **Renaming carries risk**: existing links, release URLs, and git remotes would break. The continuity cost outweighs any marginal naming benefit.

### Discoverability Strategy

Instead of renaming, we ensure "Circle.so" appears prominently in:

- Repository descriptions (both repos)
- GitHub topics (`circle-so` tag on both)
- README titles and subtitles
- Release notes and product copy

This means someone searching GitHub for "Circle.so MCP" or "circle-so mcp" will find both repos through description and topic matching.

---

## 2. GitHub Metadata — Recommended State

### Private Repo (`circle-mcp-server`)

| Field | Previous | Updated |
|-------|----------|---------|
| Description | "MCP server for Circle.so Admin API v2 — read-only community tools for spaces, posts, members, and search" | "Circle.so MCP server — community intelligence and safe content operations for Claude Desktop (16 tools, v0.3.0)" |
| Topics | api-integration, circle-so, claude, mcp, model-context-protocol, typescript | circle-so, mcp, model-context-protocol, claude, claude-desktop, community-management, typescript, mcp-server, circle-api, community-intelligence |

### Public Repo (`circle-mcp`)

| Field | Previous | Updated |
|-------|----------|---------|
| Description | "MCP server for Circle.so — read-only community intelligence tools for Claude Desktop" | "Circle.so MCP server for Claude Desktop — community intelligence and safe content operations (16 tools, v0.3.0)" |
| Topics | ai-tools, circle-so, claude, community-tools, mcp, model-context-protocol | ai-tools, circle-so, mcp, model-context-protocol, claude, claude-desktop, community-management, mcp-server, circle-api, community-intelligence |

### Key Improvements

1. **Removed "read-only"** — descriptions were outdated; v0.3.0 includes write tools
2. **Added "Claude Desktop"** — explicit mention improves discoverability for the primary use case
3. **Added tool count and version** — communicates maturity at a glance
4. **Added high-signal topics** — `claude-desktop`, `mcp-server`, `community-intelligence`, `circle-api` improve search matching
5. **Removed generic topics** — `api-integration` and `community-tools` replaced with more specific alternatives

---

## 3. Product Positioning — Recommended Copy

### One-line description (for GitHub)

> Circle.so MCP server for Claude Desktop — community intelligence and safe content operations (16 tools, v0.3.0)

### Tagline (for README subtitle)

> Community intelligence and safe content operations for Circle.so — powered by the Model Context Protocol.

### Value proposition (for docs/marketing)

> Connect your Circle.so community to Claude Desktop. Query spaces, posts, members, and engagement data through natural language. Create and update content safely. Get derived analytics like unanswered post detection and community health snapshots — all through the Model Context Protocol.

### What it does today

- Read access to Circle.so community data (spaces, posts, members, comments, topics, search)
- Derived intelligence workflows (unanswered post detection, community health snapshots)
- Safe post creation and updates (non-destructive, no delete operations)
- Permission-aware comment handling with actionable error guidance
- Claude Desktop integration via stdio transport

### What it does not do yet

- No event or course management
- No delete or archive operations
- No file/image uploads
- No webhook subscriptions
- No multi-community support

---

## 4. Distribution Recommendation

### Current recommendation: GitHub-only

| Channel | Status | Rationale |
|---------|--------|-----------|
| **GitHub** | **Primary** | Clone, build, configure in Claude Desktop. Simple, reliable. |
| **npm** | **Not yet** | Premature. API surface is still evolving (v0.4, v0.5 planned). npm publish should wait until the tool set stabilizes. |
| **MCP registry** | **Future** | When an official MCP server registry exists, this project should be listed. |

### Installation path today

1. Clone from GitHub
2. `npm install && npm run build`
3. Add to Claude Desktop config with API token
4. Restart Claude Desktop

### When to consider npm

- After v0.4.0 (events/courses) ships and the API surface feels stable
- When the project has external users beyond the author
- When the `circle-mcp-server` package name is available on npm

---

## 5. README Improvements — Summary

### Public repo (`circle-mcp`) README changes

1. **Title**: "Circle MCP Server" → "Circle.so MCP Server"
2. **Subtitle**: Rewritten to lead with value proposition, not technical classification
3. **Badge line**: Added tool count, read/write split, version, and Claude Desktop readiness
4. **What It Does**: Tightened prose, removed redundancy
5. **Getting Started**: Added full installation and Claude Desktop config instructions (previously just listed prerequisites)
6. **Distribution section**: New section documenting GitHub as primary channel
7. **Who It's For**: New section clarifying target audience

### Private repo (`circle-mcp-server`)

- GitHub description and topics updated (see Section 2 above)
- README already contains operator-grade setup instructions — no content changes needed

---

## 6. Optional Future Polish

These are low-priority improvements that could be done later:

1. **Add homepage URL** to both repos once a landing page exists (e.g., learn.ninobyte.com/circle-mcp)
2. **Create a GitHub release on the public repo** — currently only the private repo has releases
3. **Add a social preview image** (1280x640 Open Graph image) to the public repo for better link previews
4. **Add shields.io badges** to the README (version, license, Node.js version, test status)
5. **Consider GitHub Discussions** on the public repo for community feedback
6. **Add CONTRIBUTING.md** when ready for external contributions
7. **Publish to npm** after v0.4.0 stabilizes the API surface
