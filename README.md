# More Good Reviews — Cursor Plugin & Agent Skills

[Agent Skills](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) and a [Cursor](https://cursor.com) marketplace plugin for [More Good Reviews](https://moregoodreviews.com). Each skill uses the **project MCP Server** for reputation reports, action lists, and review outreach.

**Primary audience:** agencies and operators managing one or many client projects in Cursor. Single-business users can also install the plugin or use Claude with a copied skill.

## Skills

| Skill | Use when | What it does |
|-------|----------|--------------|
| [reputation-report](skills/reputation-report/SKILL.md) | Reputation report, review summary, ratings trend, health check | Read-only report over a configurable period: review volume, ratings, location and source breakdowns, reply coverage, outreach health, and prioritized next steps |
| [reputation-actions](skills/reputation-actions/SKILL.md) | What to do, how to improve, what to fix, priorities | Ranked to-do list (not a report, sends nothing): recommends the next highest-impact actions |
| [review-pipeline](skills/review-pipeline/SKILL.md) | Who to ask for reviews, weekly ask list, send review requests | Builds an eligible-customer queue (default never asked) and schedules requests after confirmation |

## Requirements

Connect the **More Good Reviews Project API MCP** server before using a skill. Optional: **Agency MCP** for reseller admin (separate URL from **Agency → MCP Server**); reputation skills still require **project** MCP per client.

## Cursor

Install the **More Good Reviews** plugin from the Cursor marketplace (this repository).

**Local development:** sync this repo to `~/.cursor/plugins/local/more-good-reviews` (folder name must match plugin id `more-good-reviews` — Cursor labels local imports from the folder name, e.g. `mgr-cursor` shows as “Mgr Cursor”). Enable local plugin imports, then reload the window.

```bash
rsync -a --delete --exclude .git ./ ~/.cursor/plugins/local/more-good-reviews/
```

Slash commands: `/reputation-report`, `/reputation-actions`, `/review-pipeline`.

Documentation: [MCP Server skills](https://docs.moregoodreviews.com/platform/mcp-server/skills) (GitBook).

### MCP connections in Cursor

The plugin’s `mcp.json` ships **one** HTTP server on purpose: Cursor plugin bundles use a **fixed URL**. Per-project URLs (`…/mcp/p/…`), white-label hosts, and agency URLs (`…/mcp/a/…`) are **not** auto-listed—you add them yourself in Cursor (same URLs you copy from the More Good Reviews console).

| Connector | URL source in MGR | In Cursor |
|-----------|-------------------|-----------|
| **Default project** (bundled) | `https://api.moregoodreviews.com/mcp` — OAuth may ask which project to authorize | Enable **more-good-reviews** from the plugin → **Sign in** |
| **Scoped project** (one business, recommended for agencies) | Project **Settings → MCP Server** — full **MCP Server URL** (`…/mcp/p/…`) | **Add another MCP server** (see below) — one entry per client |
| **Agency admin** (optional) | **Agency → MCP Server** — full URL (`…/mcp/a/…`) | **Add another MCP server** — separate from project MCP |

**1. Connect the bundled project MCP**

1. Open **Settings → Tools & MCP** (or **Customize**).
2. Turn on **more-good-reviews** (from the More Good Reviews plugin).
3. Choose **Sign in** / connect and complete More Good Reviews OAuth.

**2. Add more MCP servers (scoped project or agency)**

Use this whenever the bundled URL is not enough—multiple clients, a fixed scoped URL, or agency tools.

1. In More Good Reviews, copy the full URL from **Settings → MCP Server** (project) or **Agency → MCP Server** (agency). Do not guess hosts on white-label accounts.
2. In Cursor, open **Settings → Tools & MCP**.
3. Choose **Add new MCP server** (or **New MCP Server**).
4. Set type **HTTP** / **Streamable HTTP** and paste the **entire** URL.
5. Give the entry a clear name (e.g. client business name or “MGR Agency”).
6. Save, then **Sign in** and approve access for that URL.

Reputation skills use **project** MCP only. With several project servers connected, say which client you mean in chat, or disable extras you are not using.

Agency MCP is for reseller admin (projects, clients, team)—not for review feeds; keep a **project** MCP connected for each client you manage in skills.

## Claude (manual install)

- **Claude Code** — copy a folder from `skills/<name>/` into `~/.claude/skills/` (personal) or `.claude/skills/` (project).
- **claude.ai** — zip a skill folder and upload under **Settings → Capabilities**.
- **Claude API** — upload via the Skills API (`/v1/skills`).

Copy the whole skill directory (including `SKILL.md`), not the file alone.

## Customizing

Each skill has sensible defaults in **Configurable inputs** at the bottom of its `SKILL.md`. Edits in your copy are independent; re-copying from this repo overwrites local changes.

## Contributing

Open an issue or pull request on GitHub.
