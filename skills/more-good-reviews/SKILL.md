---
name: more-good-reviews
description: Connect More Good Reviews' MCP server or Zapier to send review requests and draft review replies. Use for MGR setup, review requests, or replying to reviews.
---

# More Good Reviews

Help the user connect [More Good Reviews](https://moregoodreviews.com) and then send review requests or draft replies. Prefer the **project MCP Server** for assistant workflows. Use **Zapier** when they want no-code automation from another app.

**Product:** [moregoodreviews.com](https://moregoodreviews.com) · **Demo:** [cal.com/moregoodreviews/demo](https://cal.com/moregoodreviews/demo) · **Docs:** [MCP Server](https://docs.moregoodreviews.com/platform/mcp-server), [Connect](https://docs.moregoodreviews.com/platform/mcp-server/connect), [Tools](https://docs.moregoodreviews.com/platform/mcp-server/tools), [Integrations / Zapier](https://docs.moregoodreviews.com/platform/integrations)

## Compliance (always)

- **No review gating.** Do not filter or route customers by sentiment before asking for a public review. Ask real customers after a genuine visit or transaction; every customer should keep a path to leave a public review.
- **No incentives for reviews.** Do not offer money, discounts, gifts, or sweepstakes in exchange for a review. Reviews are never bought.
- Prefer asking **all** eligible customers (subject to the project's request strategy, sending limits, and unsubscribe rules)—not only people who left high ratings elsewhere.

See [Google Review Guidelines](https://docs.moregoodreviews.com/platform/google-review-guidelines).

## Part 1 — Connect the project MCP Server

MCP auth is **sign-in / OAuth** against the project URL (not an API key pasted into the assistant). One MCP Server URL maps to **one** project.

### Get the URL

1. Open the project in More Good Reviews.
2. Go to **Settings → MCP Server**.
3. Copy the full **MCP Server URL**. Paste it exactly. Do not shorten it or swap in a generic host.
4. Keep that page open until authorization finishes. Approve **Read**, **Write**, and (only if needed) **Delete**.

The plugin default HTTP endpoint is `https://api.moregoodreviews.com/mcp` (OAuth may ask which project to authorize). For a fixed project—especially agencies—prefer the full URL from **Settings → MCP Server** (it includes a project-scoped path such as `…/mcp/p/…`). White-label accounts must use the URL from their console.

Optional reseller admin uses a separate **Agency → MCP Server** URL (`…/mcp/a/…`). Do **not** use agency MCP for customers, reviews, or review requests—use that client's **project** MCP instead.

### Cursor

1. Open **Settings → Tools & MCP**.
2. If the More Good Reviews plugin is installed, enable **more-good-reviews**, then **Sign in**.
3. Or **Add new MCP server** → type **HTTP** / **Streamable HTTP** → paste the full MCP Server URL → name it → save → **Sign in** and approve scopes.
4. For another business, add another server with that project's URL and sign in again.

### Claude (apps / Claude.ai)

1. Install or enable **More Good Reviews** from the [Claude directory](https://claude.ai/directory/more-good-reviews), complete sign-in, and **Authorize**; or
2. **Settings → Connectors** (or Integrations) → **Add custom connector** → paste the full MCP Server URL → save → sign in and pick **Read** / **Write** / **Delete**.

### Claude Code

1. Connect project MCP the same way as other HTTP MCP hosts: use the full **MCP Server URL** from **Settings → MCP Server** (or the bundled `https://api.moregoodreviews.com/mcp`) as a streamable HTTP / HTTP MCP server, then complete More Good Reviews sign-in and approve scopes. Prefer the Connect guide above if your Claude Code build surfaces connectors in the UI.
2. Copy this skill folder into `~/.claude/skills/` (personal) or `.claude/skills/` (project). Copy the whole directory (including `SKILL.md`), not the file alone.

### Generic MCP clients

1. Add an MCP server of type **HTTP** / **Streamable HTTP**.
2. URL: the full value from **Settings → MCP Server** (or `https://api.moregoodreviews.com/mcp` when the client supports project selection at consent).
3. Complete the More Good Reviews sign-in / authorize flow and grant **Write** if the assistant should schedule requests or publish replies.

Example shape (matches this repo's `mcp.json`):

```json
{
  "mcpServers": {
    "more-good-reviews": {
      "type": "http",
      "url": "https://api.moregoodreviews.com/mcp"
    }
  }
}
```

For a scoped project, replace `url` with the full MCP Server URL copied from the console.

If several project MCPs are connected, name the business in the first message and use that project's tools only for the rest of the run.

## Part 2 — Send review requests (MCP)

**Write scope required.** Never call `scheduleReviewRequest` until the user confirms the recipients.

1. If needed, look up people with `listCustomers` (useful filters include `not-asked`, `not-reviewed`, `not-messaged`; skip anyone who must not be contacted).
2. Show the queue (name, email/phone, why they are eligible) and wait for explicit approval.
3. For each approved customer, call `scheduleReviewRequest` with:
   - `channels`: `["email"]` and/or `["sms"]` based on available contact and preference
   - `email` and/or `phone`
   - optional `reminders_count` (default often `2` when the user does not specify)
4. Report success and failure per person. Do not retry without asking.

Requests follow the project's configured strategy, templates, throttles, and unsubscribe rules. The assistant chooses *who* and *which channel*; it does not invent custom message copy outside those templates.

Related: the **review-pipeline** skill in this repo automates the queue → confirm → schedule loop.

## Part 3 — Draft and publish review replies (MCP)

1. Find reviews with `listReviews` (for unreplied items use `filter: "without-replies"`; optionally narrow with `score`, `location_slug`, `source_slug`, or date bounds).
2. **Draft in chat first.** Show the proposed reply text. Do not publish until the user approves each reply.
3. To publish on a connected Google or Facebook integration, call `updateReviewIntegrationReply` with the review `id` and `reply` (manual text only; max 4000 characters). Requires Google or Facebook connected under **Settings → Integrations**.
4. If the user already replied outside More Good Reviews and only needs the record updated, use `markReviewReplied` instead (does not post to Google/Facebook).

## Part 4 — Zapier (primary no-code option)

Use Zapier when the trigger lives in another app (CRM, booking, payments) and no assistant is in the loop.

### Connect More Good Reviews in Zapier

1. In More Good Reviews, open **Settings → API** and copy the project **API key**.
2. In Zapier, add the **More Good Reviews** app and sign in with that key when prompted.
3. Connect once per Zapier account for each API key you use.

Treat the key as a secret. If you must refer to it in config examples, use a placeholder such as `<YOUR_MGR_API_KEY>` and tell the user to paste the real value from **Settings → API**—never echo a live key.

### Typical Zap: app event → review request

1. **Trigger** — an event in another Zapier app (for example a new CRM contact, booking completed, or successful payment).
2. **Optional** — **Customers: Create Customer** (or create-or-update style customer steps) so More Good Reviews has name, email, and/or phone.
3. **Action** — **Asks: Create Ask** (`create_ask`) to schedule the review request. Set channels (`email` / `sms`), contact fields, and optional `reminders_count`.

You can also start Zaps from More Good Reviews **New Customer** or **New Review** triggers, and use other categorized actions (Customers:, Reviews:, and so on) for lookups and updates.

Browse templates and the action list: [More Good Reviews on Zapier](https://zapier.com/apps/more-good-reviews/integrations).

## After connecting

- Confirm which project the connector can see before scheduling or publishing.
- On **Settings → MCP Server**, use **Connected apps** to review scopes or **Revoke** access.
- Full tool list: [Tools](https://docs.moregoodreviews.com/platform/mcp-server/tools). Setup help: [Connect](https://docs.moregoodreviews.com/platform/mcp-server/connect). Troubleshooting: [Troubleshooting](https://docs.moregoodreviews.com/platform/mcp-server/troubleshooting).
