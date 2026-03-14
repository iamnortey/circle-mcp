# Client Quickstart — Circle MCP

> **Version:** 1.2.0
> **Last Updated:** 2026-03-13
> **Purpose:** Get connected and running your first community query in under 15 minutes

Circle MCP is the AI operating interface for Circle.so communities. Once connected, you can run community audits, detect unanswered questions, assess onboarding health, and publish content — all through natural language in Claude.

---

## What You Need

| Requirement | Where to Get It |
|-------------|----------------|
| **Circle Admin API token** | Circle Admin > Settings > API ([instructions below](#get-your-circle-api-token)) |
| **Node.js >= 18** | [nodejs.org](https://nodejs.org/) |
| **Claude Code** or **Claude Desktop** | [claude.ai/download](https://claude.ai/download) |

---

## Get Your Circle API Token

1. Log in to your Circle community as an admin
2. Go to **Settings** (gear icon) > **API**
3. Click **Generate new token** (or copy an existing one)
4. Save the token — you'll need it during setup

Keep your token secure. Do not commit it to version control or share it publicly.

---

## Choose Your Setup Path

| Path | Best For | Guide |
|------|----------|-------|
| **Claude Code** (recommended) | Technical users, developers, operators comfortable with terminal | [Full Claude Code install guide](../self-serve/CLAUDE_CODE_INSTALL.md) |
| **Claude Desktop** | Users who prefer the Claude Desktop app | [Full Claude Desktop install guide](../self-serve/CLAUDE_DESKTOP_INSTALL.md) or [setup below](#option-b-claude-desktop-setup) |
| **Remote/hosted** (advanced) | Teams, managed deployments, multi-client setups | [See below](#option-c-remote-setup-advanced) |

---

## Option A: Claude Code Setup (Recommended)

**For the complete step-by-step guide, see [CLAUDE_CODE_INSTALL.md](../self-serve/CLAUDE_CODE_INSTALL.md).**

Quick version:

```bash
# Clone and build
git clone https://github.com/iamnortey/circle-mcp-server.git
cd circle-mcp-server
npm install && npm run build

# Get the absolute path to the built server
echo "$(pwd)/dist/index.js"

# Add to Claude Code (replace token and path)
claude mcp add --transport stdio \
  --env CIRCLE_API_TOKEN=your-token \
  circle -- node /absolute/path/to/circle-mcp-server/dist/index.js
```

Verify by starting Claude Code and running `/mcp` to see the Circle tools.

---

## Option B: Claude Desktop Setup

### Step 1: Clone and Build

```bash
git clone https://github.com/iamnortey/circle-mcp-server.git
cd circle-mcp-server
npm install
npm run build
```

### Step 2: Configure Claude Desktop

Open your Claude Desktop config file:

- **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

Add the Circle MCP server:

```json
{
  "mcpServers": {
    "circle": {
      "command": "node",
      "args": ["/absolute/path/to/circle-mcp-server/dist/index.js"],
      "env": {
        "CIRCLE_API_TOKEN": "your-circle-admin-api-token"
      }
    }
  }
}
```

Replace `/absolute/path/to/` with the actual path on your machine (use `pwd` in the `circle-mcp-server` directory to find it).

### Step 3: Restart Claude Desktop

Close and reopen Claude Desktop. The Circle tools will appear in the tool list.

### Step 4: Verify

Ask Claude:

> "Give me a health snapshot of my Circle community"

If you see your community name and stats, you are connected.

---

## Option C: Remote Setup (Advanced)

Remote HTTP endpoints are useful for team deployments or agency setups where multiple clients need to share an MCP connection.

**This is an advanced path.** Most users should use the local setup above.

If you have a hosted instance available (self-deployed or provided to you), configure it as:

```bash
# Claude Code
claude mcp add circle --transport http https://your-endpoint.example.com/mcp
```

```json
// Claude Desktop
{
  "mcpServers": {
    "circle": {
      "url": "https://your-endpoint.example.com/mcp"
    }
  }
}
```

Note: The HTTP transport is experimental. The local stdio path is the primary, production-tested option.

---

## Your First 5 Commands

Once connected, try these:

### 1. Health Check

> "Give me a community health snapshot"

Returns: community name, total spaces, total posts, total members.

### 2. See Your Spaces

> "List all spaces in my community"

Returns: every space with name, type, and URL.

### 3. Find Unanswered Posts

> "Are there any unanswered posts in my community?"

Returns: posts with zero comments — potential questions that need attention.

### 4. Search for Content

> "Search for posts about onboarding"

Returns: matching posts, members, comments, and spaces.

### 5. Create a Post

> "Create a draft post in the Announcements space titled 'Weekly Update' with the body 'This week we launched our new AI integration.'"

Returns: the created post (as a draft — ready for you to review before publishing).

---

## Understanding the Response

Every tool returns two formats:

1. **Human-readable text** — Claude shows this in the conversation
2. **Structured data** — machine-parseable JSON for further processing

You do not need to worry about the structured data unless you are building automation. Claude uses both automatically.

---

## Common Questions

### How do I find a space ID?

Ask Claude: *"List all spaces in my community"* — the response includes space IDs.

### How do I find a post ID?

Ask Claude: *"List recent posts in [space name]"* — the response includes post IDs.

### Can I delete posts or members?

No. Circle MCP Server v0.3.0 includes only non-destructive operations. You can create and update posts, but delete, archive, and moderation tools are not yet available.

### What if I get an authentication error?

Your Circle Admin API token may be expired or incorrect. Generate a new token in Circle Admin > Settings > API and update your configuration.

### I updated my token in Railway/remote but it still fails locally

Updating a token in a remote deployment (e.g., Railway environment variables) does **not** propagate to your local Claude Desktop or Claude Code configuration. Local MCP configs store the token independently. You must update the token in both places.

### What if a tool call is slow?

The Circle Admin API occasionally has latency spikes. Circle MCP handles this with a 30-second timeout and automatic retries for read operations. If it persists, check the Circle platform status.

### How many API calls does this use?

Circle enforces a rate limit of 2,000 requests per 5 minutes per IP. Most tool calls use 1-2 API calls. The health snapshot and unanswered post detection use 3-5 calls each. Normal usage stays well within limits.

---

## What Is Available

### Read Tools (13)

- List and get spaces, posts, members, comments, topics, space groups
- Community metadata
- Cross-community search
- Unanswered post detection
- Community health snapshot

### Write Tools (3)

- Create a post in any space
- Update an existing post (title, body, status, settings)
- Create a comment on a post (known limitation: Circle's Admin API returns 401 with admin tokens — the tool handles this gracefully and suggests using the Circle web interface instead)

**Write response caveat:** Circle's API may not fully echo rendered body content in the immediate response after a successful write. The post is created/updated correctly, but the response may omit the body field. Verify by fetching the post afterward if you need to confirm the full rendered content.

### What Is Not Available (v0.3.0)

- No delete, archive, or moderation operations
- No member management (invite, ban, roles)
- No space or community settings changes
- No event or course tools (planned for v0.4)
- No historical analytics or trend data
- No file or image uploads

---

## Next Steps

- **More prompt ideas:** See [PROMPT_STARTER_PACK.md](./PROMPT_STARTER_PACK.md)
- **Full Claude Code guide:** See [CLAUDE_CODE_INSTALL.md](../self-serve/CLAUDE_CODE_INSTALL.md)
- **Full tool reference:** See the [server README](../../app/circle-mcp-server/README.md)
- **Report issues:** [github.com/iamnortey/circle-mcp-server/issues](https://github.com/iamnortey/circle-mcp-server/issues)
