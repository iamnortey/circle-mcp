# Install Circle MCP for Claude Desktop

> **Version:** 1.0.0
> **Date:** 2026-03-13
> **Time to complete:** 10-15 minutes
> **Difficulty:** Requires basic terminal comfort (git, npm) and ability to edit a JSON config file

---

## What This Path Is

Circle MCP is the AI operating interface for Circle.so communities. Once connected, you can query spaces, posts, members, comments, and topics — detect unanswered questions, get community health snapshots, and create or update content — all through natural language in Claude Desktop.

This guide walks you through installing Circle MCP locally and connecting it to **Claude Desktop** using your own Circle API token.

**This is the self-serve local install path.** You do not need anyone else to set this up for you.

---

## When to Choose This Path vs Claude Code

| If you... | Use this guide |
|-----------|---------------|
| Use the Claude Desktop app | **Yes — this guide** |
| Prefer a visual chat interface | **Yes — this guide** |
| Are comfortable editing a JSON config file | **Yes — this guide** |
| Use Claude Code (terminal-based Claude) | No — use [CLAUDE_CODE_INSTALL.md](./CLAUDE_CODE_INSTALL.md) instead |

Both paths install the same MCP server with the same 16 tools. The only difference is how you configure your Claude client.

---

## What You Need Before Starting

| Requirement | How to Check |
|-------------|-------------|
| **Git** | Run `git --version` in your terminal |
| **Node.js >= 18** | Run `node --version` — must show v18 or higher |
| **npm** | Comes with Node.js. Run `npm --version` to confirm |
| **Claude Desktop** | The Claude Desktop app must be installed on your machine |
| **Circle admin access** | You must be an admin of a Circle.so community |

If you're missing Node.js, install it from [nodejs.org](https://nodejs.org/) (LTS version recommended).

If you're missing Claude Desktop, download it from [claude.ai/download](https://claude.ai/download).

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

## Step 3: Find Your Absolute Path

Claude Desktop needs the **absolute path** to the built server file. Get it by running this command inside the `circle-mcp-server` directory:

```bash
echo "$(pwd)/dist/index.js"
```

**Copy the output.** It will look something like:

- **macOS:** `/Users/yourname/circle-mcp-server/dist/index.js`
- **Windows (Git Bash):** `/c/Users/yourname/circle-mcp-server/dist/index.js`
- **Windows (native):** `C:\Users\yourname\circle-mcp-server\dist\index.js`

You'll paste this path into the config file in the next step.

---

## Step 4: Edit the Claude Desktop Config File

### Find the config file

Claude Desktop stores its configuration in a JSON file. The location depends on your operating system:

| Platform | Config File Path |
|----------|-----------------|
| **macOS** | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| **Windows** | `%APPDATA%\Claude\claude_desktop_config.json` |

**macOS — open the file:**

```bash
# Open in your default text editor
open ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

If the file doesn't exist yet, create it:

```bash
# Create the directory if needed
mkdir -p ~/Library/Application\ Support/Claude

# Create the file
touch ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

**Windows — open the file:**

1. Press `Win + R`
2. Type `%APPDATA%\Claude` and press Enter
3. Open `claude_desktop_config.json` in a text editor (Notepad, VS Code, etc.)

If the file doesn't exist, create a new file named `claude_desktop_config.json` in that folder.

### Add the Circle MCP server

If the file is **empty or doesn't exist**, paste this entire block:

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

If the file **already has content** (other MCP servers configured), add the `circle` entry inside the existing `mcpServers` object:

```json
{
  "mcpServers": {
    "existing-server": {
      "...": "..."
    },
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

**Replace two things:**
- `/absolute/path/to/circle-mcp-server/dist/index.js` — paste the path from Step 3
- `your-circle-admin-api-token` — paste your Circle API token from Step 1

**Example with real values (macOS):**

```json
{
  "mcpServers": {
    "circle": {
      "command": "node",
      "args": ["/Users/yourname/circle-mcp-server/dist/index.js"],
      "env": {
        "CIRCLE_API_TOKEN": "sk_live_abc123def456"
      }
    }
  }
}
```

**Save the file.**

---

## Step 5: Restart Claude Desktop

**You must fully restart Claude Desktop for the new configuration to take effect.**

1. Quit Claude Desktop completely (not just close the window)
   - **macOS:** `Cmd + Q` or Claude menu > Quit Claude
   - **Windows:** Right-click the system tray icon > Exit, or `Alt + F4`
2. Wait a few seconds
3. Reopen Claude Desktop

Simply closing the window is not enough — the app must fully quit and restart to load the new MCP configuration.

---

## Step 6: Verify the Connection

After restarting Claude Desktop, check that the Circle MCP tools are available.

### Check the tool list

Look for the MCP tools indicator in Claude Desktop. You should see Circle's 16 tools available. The exact location of the indicator depends on your Claude Desktop version — look for a tools icon, hammer icon, or MCP status indicator in the chat interface.

### Run a test prompt

Type this in a new conversation:

```
Give me a health snapshot of my Circle community
```

**Expected result:** Your community name, total spaces, total posts, and total members.

If you see your community data, **you are connected and working.**

---

## Step 7: Run Your First Prompts

Try these to explore what Circle MCP can do:

### Community health check

```
Give me a community health snapshot
```

Returns: community name, total spaces, total posts, total members.

### List your spaces

```
List all spaces in my Circle community
```

Returns: every space with name, type, and URL.

### Find unanswered posts

```
Are there any unanswered posts in my community?
```

Returns: posts with zero comments — potential questions that need attention.

### Search for content

```
Search for posts about onboarding
```

Returns: matching posts, members, comments, and spaces.

### Create a draft post

```
Create a draft post in the Announcements space titled "Test Post" with the body "Testing Circle MCP integration"
```

Returns: the created post (as a draft — ready for you to review before publishing).

---

## Troubleshooting

### Circle tools not appearing after restart

**Cause:** Config file not found, not valid JSON, or Claude Desktop didn't fully restart.

**Fix:**
1. Verify the config file exists at the correct path:
   - macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Windows: `%APPDATA%\Claude\claude_desktop_config.json`
2. Validate your JSON (use [jsonlint.com](https://jsonlint.com/) or paste into VS Code — it highlights syntax errors)
3. Common JSON mistakes:
   - Missing comma between server entries
   - Trailing comma after the last entry
   - Unmatched braces `{}`
   - Using single quotes instead of double quotes
4. Fully quit and reopen Claude Desktop (not just close the window)

### "CIRCLE_API_TOKEN is required"

**Cause:** The token was not included in the config, or the `env` block is malformed.

**Fix:** Open the config file and verify the `env` section:

```json
"env": {
  "CIRCLE_API_TOKEN": "your-actual-token-here"
}
```

Make sure the token is a non-empty string and the JSON is valid. Save and restart Claude Desktop.

### "Cannot find module" or "MODULE_NOT_FOUND"

**Cause:** The path to `dist/index.js` is wrong, or the project was not built.

**Fix:**
1. Verify the file exists: `ls /your/path/to/circle-mcp-server/dist/index.js`
2. If it doesn't exist, run `npm run build` in the `circle-mcp-server` directory
3. Ensure you're using the **absolute path**, not a relative one
4. On Windows, use forward slashes in the JSON: `"C:/Users/..."` (not `"C:\\Users\\..."`)

### 401 Unauthorized on tool calls

**Cause:** Token is invalid, expired, or does not have admin access.

**Fix:**
1. Go to Circle Admin > Settings > API
2. Generate a new token
3. Update the token in your Claude Desktop config file
4. Save and restart Claude Desktop

### Token works remotely but fails locally (or vice versa)

**Cause:** If you updated your Circle API token in a remote deployment (e.g., Railway environment variables), that change does **not** propagate to your local Claude Desktop configuration. The token in `claude_desktop_config.json` is independent of any remote environment.

**Fix:** Update the token in your Claude Desktop config file, save, and restart Claude Desktop.

### 403 Forbidden on tool calls

**Cause:** Your Circle API token does not have sufficient permissions.

**Fix:** Ensure your token was created with an admin account. Community member tokens will not work.

### Tools are listed but calls fail silently

**Cause:** Node.js version too old.

**Fix:** Run `node --version` in your terminal. Must be v18 or higher. Upgrade at [nodejs.org](https://nodejs.org/).

### Comment creation returns 401

**This is a known Circle API limitation**, not a configuration error. Circle's Admin API v2 returns 401 for comment creation with admin tokens. Read operations and post creation/update work normally. Create comments through the Circle web interface instead.

### Config file changes not taking effect

**Cause:** Claude Desktop was not fully restarted.

**Fix:**
- **macOS:** Use `Cmd + Q` to quit (not just close the window), then reopen
- **Windows:** Right-click the system tray icon > Exit, then reopen
- Closing the window may leave the app running in the background — you must fully quit

### Wrong config file location

**Cause:** Editing the wrong file or the file is in the wrong directory.

**Fix:** Verify the exact path:
- **macOS:** Open Finder, press `Cmd + Shift + G`, paste `~/Library/Application Support/Claude/`
- **Windows:** Press `Win + R`, type `%APPDATA%\Claude`, press Enter

The file must be named exactly `claude_desktop_config.json`.

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

## Update Your Token

If you need to change your Circle API token:

1. Open the Claude Desktop config file (see [Step 4](#step-4-edit-the-claude-desktop-config-file) for the path)
2. Replace the value of `CIRCLE_API_TOKEN` with your new token
3. Save the file
4. Restart Claude Desktop

## Remove Circle MCP

1. Open the Claude Desktop config file
2. Remove the entire `"circle": { ... }` block from `mcpServers`
3. Save the file
4. Restart Claude Desktop

---

## Next Steps

- **More prompt ideas:** See [PROMPT_STARTER_PACK.md](../product/PROMPT_STARTER_PACK.md)
- **Full tool reference:** See the [server README](../../app/circle-mcp-server/README.md)
- **Report issues:** [github.com/iamnortey/circle-mcp-server/issues](https://github.com/iamnortey/circle-mcp-server/issues)
