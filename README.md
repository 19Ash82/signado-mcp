# Signado MCP

Warm lead discovery for Claude Code and Codex.

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

## Connect Codex

```bash
codex mcp add signado --url https://mcp.signado.io/mcp
codex mcp login signado
```

Approve the browser prompt with your existing Signado account and choose the workspace to connect.

## Tools and credit costs

### Read and export

| Tool | What it does | Cost |
|---|---|---:|
| `ping` | Checks the connection and selected workspace. | 0 credits |
| `list_warm_leads` | Lists warm leads with ICP scores, companies, and LinkedIn engagement context. | 0 credits |
| `export_leads` | Exports warm leads as structured CSV rows for a date window. | 0 credits |
| `list_templates` | Lists system templates, workspace templates, and authoring rules. | 0 credits |

### Discovery sources

| Tool | What it does | Cost |
|---|---|---:|
| `preview_source` | Previews posts and engagers for a keyword or creator before activation. | 1 credit |
| `add_source` | Adds a monitoring source: track a competitor or creator LinkedIn profile, or a LinkedIn keyword. Shows projected daily credit cost before activation and requires confirmation. | 0 credits to add, then 3 credits per new warm lead |
| `remove_source` | Archives a source while keeping the history in place. | 0 credits |

### Enrichment and outreach

| Tool | What it does | Cost |
|---|---|---:|
| `find_email` | Finds emails for selected leads and writes results back to Warm Leads. | 5 credits per contact |
| `draft_message` | Creates campaign-backed AI drafts for selected warm leads. | 7 credits per contact |
| `save_template` | Saves a workspace template with custom variables for future drafts. | 0 credits |

Every paid response includes `credits_charged`, `balance_after`, and `session_spend_total`.

## Security model

- OAuth sign-in through your browser.
- Existing Signado account required.
- No API keys to create, paste, rotate, or store.
- Access is scoped to the workspace you choose during sign-in.
- The same app permissions and row-level security apply to tool calls.

## Verified clients

- Claude Code: verified against the production endpoint with all 10 tools listed and paid preview charging exactly 1 credit.
- Codex 0.138.0: verified add, login, consent, 10 tools listed, and `ping` success.

## Links

- Canonical page: https://signado.io/mcp
- Setup guide: https://signado.io/help/mcp/mcp-overview
- Start Free: https://app.signado.io/sign-up
