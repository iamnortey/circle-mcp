# Install Circle MCP for Claude Code

> **Version:** 1.0.0
> **Date:** 2026-03-13
> **Time to complete:** 10-15 minutes
> **Difficulty:** Requires terminal comfort (git, npm, basic CLI)

---

## What This Is

Circle MCP is the AI operating interface for Circle.so communities. Once connected, you can query spaces, posts, members, comments, and topics — detect unanswered questions, get community health snapshots, and create or update content — all through natural language in Claude Code.

This guide walks you through installing Circle MCP locally and connecting it to Claude Code using your own Circle API token.

**This is the self-serve local install path.** You do not need anyone else to set this up for you.

---

## What You Need Before Starting

| Requirement | How to Check |
|-------------|-------------|
| **Git** | Run `git --version` in your terminal |
| **Node.js >= 18** | Run `node --version` — must show v18 or higher |
| **npm** | Comes with Node.js. Run `npm --version` to confirm |
| **Claude Code** | Run `claude --version` in your terminal |
| **Circle admin access** | You must be an admin of a Circle.so community |

If you're missing Node.js, install it from [nodejs.org](https://nodejs.org/) (LTS version recommended).

If you're missing Claude Code, install it from [claude.ai/download](https://claude.ai/download) or via `npm install -g @anthropic-ai/claude-code`.

---

## Step 1: Get Your Circle API Token

You need an API token from your Circle community. This token lets the MCP server read and write data in your community on your behalf.

1. Log in to your Circle community as an admin
2. Click the **gear icon** (Settings) in the left sidebar, or navigate to **Admin > Settings**
3. Click **API** in the settings menu
4. Click **Generate new token** (or copy an existing token if you have one)
5. Copy the token — you'll need it in Step 3

**Keep your token secure:**
- Do not commit it to version control
- Do not share it in public channels
- If compromised, revoke it in Circle and generate a new one
- The token has admin-level access to your community

---

## Step 2: Clone and Build

Open your terminal and run these commands:

```bash
# Clone the repository
git clone https://github.com/iamnortey/circle-mcp-server.git

# Enter the project directory
cd circle-mcp-server

# Install dependencies
npm install

# Build the project
npm run build
```

**Optional but recommended — verify the build:**

```bash
npm run validate
```

This runs type-checking, builds, and runs 95 offline tests. No API token needed. If it passes, your build is good.

---

## Step 3: Connect to Claude Code

You need the **absolute path** to the built server file. Get it by running this in the `circle-mcp-server` directory:

```bash
echo "$(pwd)/dist/index.js"
```

Copy that path. Now add the MCP server to Claude Code:

```bash
claude mcp add --transport stdio \
  --env CIRCLE_API_TOKEN=your-circle-api-token-here \
  circle -- node /absolute/path/to/circle-mcp-server/dist/index.js
```

**Replace two things:**
- `your-circle-api-token-here` — paste your Circle API token from Step 1
- `/absolute/path/to/circle-mcp-server/dist/index.js` — paste the path from the `echo` command above

**Example with a real path (macOS):**

```bash
claude mcp add --transport stdio \
  --env CIRCLE_API_TOKEN=sk_live_abc123def456 \
  circle -- node /Users/yourname/circle-mcp-server/dist/index.js
```

**Scope options:**
- The command above adds Circle MCP to your **local** scope (current project, just you). This is the default and recommended for getting started.
- To make it available across all your projects, add `--scope user`:

```bash
claude mcp add --transport stdio --scope user \
  --env CIRCLE_API_TOKEN=your-circle-api-token-here \
  circle -- node /absolute/path/to/circle-mcp-server/dist/index.js
```

---

## Step 4: Verify the Connection

### Check the server is registered

```bash
claude mcp list
```

You should see `circle` in the list of configured servers.

### Check server details

```bash
claude mcp get circle
```

This shows the server configuration and status.

### Check from inside Claude Code

Start Claude Code and type:

```
/mcp
```

You should see the Circle server listed with its 16 tools available.

---

## Step 5: Run Your First Prompts

Start Claude Code (or use an existing session — you may need to restart it after adding the MCP server). Try these:

### Community health check

```
Give me a health snapshot of my Circle community
```

**Expected result:** Your community name, total spaces, total posts, and total members.

If you see your community data, **you are connected and working.**

### List your spaces

```
List all spaces in my Circle community
```

### Find unanswered posts

```
Are there any unanswered posts in my community?
```

### Search for content

```
Search for posts about onboarding
```

### Create a draft post

```
Create a draft post in the Announcements space titled "Test Post" with the body "Testing Circle MCP integration"
```

---

## Troubleshooting

### "CIRCLE_API_TOKEN is required"

**Cause:** The token was not passed to the server.

**Fix:** Re-add the server with the correct `--env` flag:

```bash
claude mcp remove circle
claude mcp add --transport stdio \
  --env CIRCLE_API_TOKEN=your-actual-token \
  circle -- node /absolute/path/to/circle-mcp-server/dist/index.js
```

### 401 Unauthorized on tool calls

**Cause:** Token is invalid, expired, or does not have admin access.

**Fix:**
1. Go to Circle Admin > Settings > API
2. Generate a new token
3. Remove and re-add the MCP server with the new token

### Token works remotely but fails locally (or vice versa)

**Cause:** If you updated your Circle API token in a remote deployment (e.g., Railway environment variables), that change does **not** propagate to your local Claude Code configuration. Local MCP configs store the token independently.

**Fix:** Update the token in both places. For Claude Code, remove and re-add the server with the new token.

### "Cannot find module" or "MODULE_NOT_FOUND"

**Cause:** The project was not built, or the path is wrong.

**Fix:**
1. Make sure you ran `npm run build` in the `circle-mcp-server` directory
2. Verify the path: `ls /your/path/to/circle-mcp-server/dist/index.js` — it should exist
3. Use the absolute path, not a relative one

### Circle server not showing in `/mcp`

**Cause:** Server was added after starting Claude Code, or there's a config error.

**Fix:**
1. Restart Claude Code (exit and reopen)
2. Run `claude mcp list` to confirm the server is registered
3. Run `claude mcp get circle` to check for configuration errors

### 403 Forbidden on tool calls

**Cause:** Your Circle API token does not have sufficient permissions.

**Fix:** Ensure your token was created with an admin account. Community member tokens will not work.

### Tools are listed but calls fail silently

**Cause:** Node.js version too old.

**Fix:** Run `node --version`. Must be v18 or higher. Upgrade at [nodejs.org](https://nodejs.org/).

### Comment creation returns 401

**This is a known Circle API limitation**, not a configuration error. Circle's Admin API v2 returns 401 for comment creation with admin tokens. Read operations and post creation/update work normally. Create comments through the Circle web interface instead.

---

## What You Now Have

- 13 read tools: list and inspect spaces, posts, members, comments, topics, space groups, community metadata, search, unanswered post detection, health snapshots
- 3 write tools: create posts, update posts, create comments (with Circle API caveat)
- All operations are non-destructive — no delete, archive, or moderation actions are exposed

## What Is Not Available (v0.3.0)

- No delete, archive, or moderation operations
- No member management (invite, ban, roles)
- No space or community settings changes
- No event or course tools (planned for a future version)
- No historical analytics or trend data

---

## Scope and Boundaries

- **Your token, your community.** This server connects to the Circle community associated with your API token. Each Circle community requires its own token.
- **Local execution.** The server runs on your machine. No data passes through third-party infrastructure.
- **Self-serve path.** This is the standard self-install flow. No managed setup or external assistance required.
- **Hosted/managed deployments** exist as advanced options for teams or agency use cases. They are not needed for personal use.

---

## Remove or Reconfigure

### Remove the server

```bash
claude mcp remove circle
```

### Update your token

```bash
claude mcp remove circle
claude mcp add --transport stdio \
  --env CIRCLE_API_TOKEN=your-new-token \
  circle -- node /absolute/path/to/circle-mcp-server/dist/index.js
```

### Check what's configured

```bash
claude mcp list
claude mcp get circle
```

---

## Next Steps

- **More prompt ideas:** See [PROMPT_STARTER_PACK.md](../product/PROMPT_STARTER_PACK.md)
- **Full tool reference:** See the [server README](../../app/circle-mcp-server/README.md)
- **Report issues:** [github.com/iamnortey/circle-mcp-server/issues](https://github.com/iamnortey/circle-mcp-server/issues)
