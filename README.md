# Signado MCP

Warm lead discovery for Claude Code, Codex, ChatGPT, and Grok Bot.

This repository is the public listing page for Signado's hosted MCP endpoint. It does not contain product source code. Connect your existing workspace, then ask your assistant to list warm leads, inspect LinkedIn engagement context, find emails, and save AI outreach drafts back to Campaigns.

## Endpoint

```text
https://mcp.signado.io/mcp
```

## Connect Claude Code

```bash
claude mcp add --transport http signado https://mcp.signado.io/mcp
```

Then run `/mcp`, choose `signado`, and authenticate in your browser.

Claude Code reads Signado's OAuth client details automatically.

## Connect Codex

```bash
codex mcp add signado --url https://mcp.signado.io/mcp
codex mcp login signado
```

Approve the browser prompt with your existing Signado account and choose the
workspace to connect. Codex reads Signado's OAuth client details automatically.

## Connect Claude web

In Settings → Connectors, add a custom connector named **Signado** for
`https://mcp.signado.io/mcp`. Keep authentication on **Always required**. Under
OAuth client, keep **Use Anthropic's hosted client metadata (Recommended)**.
Under Advanced, keep **Streamable HTTP**, add the connector, then approve the
Signado sign-in.

## Connect ChatGPT

In Settings → Plugins, enable developer mode if needed, then select **+ → New
Plugin**. Name it **Signado**, enter `https://mcp.signado.io/mcp`, and choose
OAuth. Under Advanced OAuth settings, keep **Client Identifier Metadata
Document (CIMD)** as the registration method. Create the plugin, then sign in
with Signado and approve access.

## Connect Grok Bot

Send this message to your bot:

```text
Add a custom MCP server called signado at https://mcp.signado.io/mcp
```

Grok Bot adds the connector and shows a connect card. Select **Authorize**, sign
in with Signado, choose the workspace to connect, and approve access. No API key
or auth header is needed.

## Tools and credit costs

The connector advertises 15 tools. `qualify` and `catchup_source` are hidden from the listing but remain callable as compatibility aliases, for 17 callable tools in total.

### Read and export

| Tool | What it does | Cost |
|---|---|---:|
| `ping` | Checks the connection and selected workspace. | 0 credits |
| `list_warm_leads` | Lists warm leads with ICP scores, companies, LinkedIn engagement context, and a `best_of_run` boolean. | 0 credits |
| `get_best_of_run` | Returns stored Best of run selections from completed Discovery runs. Mechanical runs rank by composite score, always keep the top lead, then keep scores of at least 60 up to five. The exact quote and run boundary remain; verdict, why, and angle are nullable legacy fields from historical model-selected runs. | 0 credits |
| `get_warm_lead_detail` | Loads one warm lead's complete LinkedIn engagement history on demand. | 0 credits |
| `export_leads` | Exports warm leads as structured CSV rows for a date window. | 0 credits |
| `list_templates` | Lists system templates, workspace templates, and authoring rules. | 0 credits |

### Discovery sources

| Tool | What it does | Cost |
|---|---|---:|
| `preview_source` | Optionally previews a keyword, creator, Company page, or post source. Completed empty previews retain their fixed charge; a Company page with no recent original posts is a valid empty result. | 1 credit |
| `add_source` | Adds keyword, creator, or Company page monitoring with an optional Daily or Custom schedule, or saves a LinkedIn post PAUSED for Once-only discovery. Post scope is Commenters, Reactors, or Both (default Both); post creation never accepts or seeds a recurring schedule. | 0 credits to add, then normal discovery credits when run |
| `set_source_schedule` | Changes an existing keyword, creator, or Company page source to Daily or to selected ISO weekdays at one local time in an IANA timezone. Edits affect only future runs. | 0 credits |
| `run_source_once` | Runs an active/paused keyword, creator, or Company page, or a paused post, once. Post Both requires at least two lead slots. Zero-engagement runs settle unused lead credits. | 0 credits to queue, then credits for leads actually found |
| `cancel_source_run` | Cancels an in-flight one-time run. Leads already found stay; unspent credits come back automatically. | 0 credits |
| `catchup_source` | Legacy alias of `run_source_once`, kept for back-compat. | Same as `run_source_once` |
| `remove_source` | Archives a source while keeping the history in place. | 0 credits |

`add_source` keyword mode:

- `keyword_mode` is optional and only valid when `source_type = "keyword"`.
- Omit it, or pass `"post_engagers"`, to find people commenting on matching LinkedIn posts.
- Pass `"post_authors"` to find the people who wrote matching LinkedIn posts, including zero-comment posts when the author is person-shaped.
- `source_type = "competitor_creator"` does not accept `keyword_mode`; creator sources still monitor engagers on that creator's posts.
- `source_type = "company"` requires `profile_url` to be a LinkedIn `/company/...` URL and does not accept `keyword_mode`. It remains a Company Page source in returned source and Warm Leads metadata.
- Company-account comments are filtered; people commenting from personal profiles, including company employees, remain eligible warm leads.

Custom schedules use ISO weekday numbers (`1` = Monday through `7` = Sunday),
one `local_time` for every selected weekday, and an IANA timezone such as
`Europe/London`. Both `add_source` and `set_source_schedule` show a no-write
plan first and require `confirm: true` to change monitoring. Schedule edits
recompute the next future run and never trigger a retroactive run. Post sources
remain Once-only and reject Daily or Custom schedules. The saved UTC due instant
remains canonical if an MCP runtime cannot render a PostgreSQL-supported zone
alias locally; the tool returns a localization warning instead of failing after
the schedule was saved.

Creator and Company page sources use the same profile-source credit
infrastructure: 1 credit for Preview or a Daily profile-post scan, 5 credits per
new warm lead, and the plan-configured re-engagement rate. They reuse the
existing creator ledger actions and refund rules without changing their public
source type. A valid scan with no recent posts keeps the scan charge; a hard
full-chain failure is reported and refunded rather than silently pausing.

`add_source` post mode:

- Set `source_type = "post"` and provide `post_url`.
- `post_engagement_scope` is `"commenters"`, `"reactors"`, or `"both"` (default).
- Post sources are saved paused and are Once-only. Optionally call `preview_source`, then call `run_source_once` with a confirmed credit ceiling.
- The reactor capability belongs only to post sources; keyword and creator sources cannot inherit it.

Examples:

```json
{ "source_type": "keyword", "keyword": "website redesign", "keyword_mode": "post_engagers", "confirm": false }
```

```json
{ "source_type": "keyword", "keyword": "website redesign", "keyword_mode": "post_authors", "confirm": false }
```

```json
{ "source_type": "post", "post_url": "https://www.linkedin.com/feed/update/urn:li:activity:1234567890", "post_engagement_scope": "both", "confirm": false }
```

```json
{ "source_type": "company", "profile_url": "https://www.linkedin.com/company/mondaydotcom/", "confirm": false }
```

```json
{ "source_name": "Competitor scans", "schedule": { "mode": "custom", "weekdays": [1, 2, 3, 4, 5], "local_time": "08:00", "timezone": "Europe/London" }, "confirm": false }
```

### Enrichment and outreach

| Tool | What it does | Cost |
|---|---|---:|
| `find_email` | Finds emails for selected leads and writes results back to Warm Leads. | 5 credits per contact |
| `qualify` | Deprecated compatibility name for re-scoring one warm lead with stored person, post, and company evidence. It reruns Luna without refreshing provider evidence. | 1 credit for one contact |
| `draft_message` | Creates campaign-backed AI drafts for selected warm leads. | 3 credits per contact |
| `save_template` | Saves a workspace template with custom variables for future drafts. | 0 credits |

Every paid response includes `credits_charged`, `balance_after`, and `session_spend_total`.

## Security model

- OAuth sign-in through your browser.
- Existing Signado account required.
- No API keys to create, paste, rotate, or store.
- Access is scoped to the workspace you choose during sign-in.
- The same app permissions and row-level security apply to tool calls.

## Verified clients

- Claude Code 2.1.252: a fresh 2026-09-01 connection without an explicit client ID completed OAuth, listed 14 advertised tools, and passed `ping`.
- Codex 0.149.1: a fresh 2026-09-01 connection without an explicit client ID completed OAuth, listed 14 advertised tools, and passed `ping`.
- Claude web: a fresh 2026-09-01 connection using **Use Anthropic's hosted client metadata** completed OAuth, listed 14 advertised tools, and passed `ping`.
- ChatGPT Plugins: a fresh 2026-09-01 CIMD connection completed OAuth, listed 14 advertised tools, and passed `ping`.

- Grok Bot desktop 0.66.0: a fresh 2026-10-02 connection, added by asking the bot in chat to add a custom MCP server called signado at `https://mcp.signado.io/mcp` with OAuth sign-in and no auth header, completed OAuth from the connect card, listed 15 advertised tools, and passed `ping` and `list_warm_leads`.

The current 15-tool source contract requires the same client acceptance after an MCP deployment. The four 14-tool checks above are dated historical receipts.

## Links

- Canonical page: https://signado.io/mcp
- Setup guide: https://signado.io/help/mcp/mcp-overview
- Troubleshooting: https://signado.io/help/mcp/mcp-connection-issues
- Start Free: https://app.signado.io/sign-up
- Discover Signado: https://signado.io
- See more on [Claude Market's MCP directory](https://www.claudemarket.ai/mcp)

## Trial and pricing

New accounts choose Starter, Growth, or Scale for a 7-day free trial with the selected plan's full credits and MCP access. A card is required, and you can cancel anytime. Existing Free accounts are grandfathered and keep their credits.
