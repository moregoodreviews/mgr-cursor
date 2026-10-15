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

Install the **More Good Reviews** plugin from the Cursor marketplace (this repository). Connect the bundled project MCP under **Settings → Tools & MCP**, then sign in to More Good Reviews.

- **Several client projects:** add one MCP entry per business using each project’s **Settings → MCP Server** URL.
- **Agency admin:** add **Agency → MCP Server** as a separate MCP entry.

Slash commands: `/reputation-report`, `/reputation-actions`, `/review-pipeline`.

Documentation: [MCP Server skills](https://docs.moregoodreviews.com/platform/mcp-server/skills) (GitBook).

## Claude (manual install)

- **Claude Code** — copy a folder from `skills/<name>/` into `~/.claude/skills/` (personal) or `.claude/skills/` (project).
- **claude.ai** — zip a skill folder and upload under **Settings → Capabilities**.
- **Claude API** — upload via the Skills API (`/v1/skills`).

Copy the whole skill directory (including `SKILL.md`), not the file alone.

## Customizing

Each skill has sensible defaults in **Configurable inputs** at the bottom of its `SKILL.md`. Edits in your copy are independent; re-copying from this repo overwrites local changes.

## Contributing

Open an issue or pull request on GitHub.
