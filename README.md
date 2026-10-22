# Blockworks plugin for Grok

Crypto research and market data from Blockworks, inside Grok Build, Grok Bot and Cursor. The plugin connects the agent to the Blockworks MCP server and adds a skill, a command and an agent that teach it how to use the server well.

## What it includes

- **MCP server** (`.mcp.json`, mirrored in `mcp.json` for Cursor-style loaders): connects to the hosted Blockworks MCP server at `https://mcp.blockworks.com/mcp` over streamable HTTP. The plugin contains only this address. It does not contain or run any server code on your machine.
- **Skill** `crypto-research`: tells the agent which tool answers which kind of question.
- **Command** `/blockworks:research <topic>`: writes a short, cited research brief on an asset, protocol or topic.
- **Agent** `crypto-analyst`: handles multi-part research questions that need numbers and sources.

The plugin ships two manifests with identical metadata: `.grok-plugin/plugin.json` for Grok Build and `.cursor-plugin/plugin.json` for Grok Bot and Cursor.

## Tools

The data tools are read-only. They do not trade, move funds or change any data. The one tool that writes, `submit_feedback`, sends a problem report to Blockworks and changes nothing else.

| Tool | What it does |
| --- | --- |
| `search_documents` | Finds research, news, governance, diligence and transcript documents |
| `tabular_catalog` | Lists row-and-column datasets and their fields |
| `tabular_data` | Reads filtered, sorted rows from one dataset |
| `timeseries_catalog` | Lists time-series datasets, granularities and metrics |
| `timeseries_data` | Reads metric values over a time window |
| `monitoring_categories` | Lists the category and subcategory slugs the monitoring filters take |
| `monitoring_developments` | Reads individual industry developments from Blockworks' monitoring coverage, with their source documents |
| `monitoring_events` | Reads events: related developments grouped into a story over time |
| `monitoring_views` | Lists the saved monitoring views the user can see, and their recent matches |
| `watchlists` | Lists the user's watchlists |
| `watchlist_assets` | Reads the assets on one watchlist |
| `submit_feedback` | Sends a report about a tool error, wrong or missing data, or a missing capability to Blockworks |

## Get an API key

You need a Blockworks API key to use the plugin. Create one at [app.blockworks.com/account/api](https://app.blockworks.com/account/api).

## Sign in

The server uses OAuth, and your API key is the credential.

1. The first time the agent calls a Blockworks tool, it opens a Blockworks sign-in page in your browser.
2. Enter your Blockworks API key on that page and submit it.
3. The page sends you back to the client. The client then sends your API key as the bearer token on each request to the server.

The server gives the client a token that lasts 30 days. If you revoke your API key, the next tool call fails and the client asks you to sign in again.

**Known limit:** the sign-in page does not check your API key. If you enter a wrong key, sign-in still completes. The error appears at the first tool call as `UNAUTHORIZED: API key invalid or expired`. To fix it, disconnect the Blockworks server in your client, then sign in again with the correct key.

## Access tiers

What you can read depends on the entitlements of your API key.

- **`search_documents`** needs an Enterprise account with the `ai_toolkit_permission` API permission. It allows 30 requests per minute for each user.
- **Monitoring**: the four `monitoring_*` tools read the Blockworks monitoring product, which an account is entitled to separately. Without that entitlement they return `FORBIDDEN`.
- **Watchlists**: `watchlists` and `watchlist_assets` read only the signed-in user's own and team-shared watchlists.
- **Datasets**: each dataset in `tabular_catalog` and `timeseries_catalog` shows an access tier. A `public` dataset needs no entitlement. The `unpaid`, `paid` and `permissive` tiers need a key with the matching entitlement. A `permissive` dataset also names the permission it needs.

The catalogs list only the datasets that Blockworks publishes to its public API.

## Example prompts

- "What happened with EigenLayer governance in August 2026? Cite your sources."
- "Show the top 10 assets by market cap today."
- "Show the daily price of ETH for the last 90 days."
- "How did Uniswap fees change from Q1 2026 to Q2 2026?"
- "What happened in DeFi governance this week? Use the monitoring tools and cite the sources."
- "How did the assets on my watchlist do over the last 30 days?"
- "/blockworks:research Hyperliquid"

## Troubleshooting

| Error | Cause | Fix |
| --- | --- | --- |
| `UNAUTHORIZED: API key invalid or expired` | The key is wrong, revoked or expired | Disconnect the server in your client and sign in again with a valid key |
| `FORBIDDEN: this endpoint requires an Enterprise account with the ai_toolkit_permission API permission` | `search_documents` needs Enterprise access | Upgrade your plan, or use the dataset tools only |
| `FORBIDDEN: your subscription tier does not grant access to this dataset` | The dataset's access tier is above your key's entitlements | Pick a dataset your tier allows. Check the tier in the catalog. |
| `RATE_LIMITED: ...` | Too many requests in a short time | Wait, then try again |
| `search_documents` returns old documents | The query has no dates | Put concrete dates in the question, such as "in August 2026" |
| `FORBIDDEN` from a `monitoring_*` tool | The account is not entitled to the monitoring product | Use the other tools; contact Blockworks to add monitoring to the account |

## Data and privacy

The plugin sends your questions and tool arguments only to the Blockworks MCP server at `mcp.blockworks.com`. It runs no local code, no hooks and no scripts. Your API key goes only to Blockworks. See the Blockworks [Terms](https://blockworks.com/terms) and [Privacy Policy](https://blockworks.com/privacy-policy).

## Install

### Grok Build

After the listing goes live in the xAI marketplace:

```
grok plugin install blockworks --trust
```

Or type `/marketplace` inside Grok Build, find **Blockworks** and press `i`.

This repository is also a marketplace of its own (`.grok-plugin/marketplace.json`), so it can be added directly as a plugin source before the listing is live. Run `grok plugin --help` for the marketplace and local-install options in your version, and `grok inspect` to confirm the plugin's skill, command, agent and MCP server are discovered.

### Grok Bot

Open **Connect apps** in the Grok Bot sidebar, find **Blockworks** and sign in with your API key when prompted.

### Cursor

Install from the Cursor marketplace once listed. To test a local copy, clone this repository into `~/.cursor/plugins/local/blockworks` and reload the window.

## Publishing

- **Grok Build**: open a pull request to [xai-org/plugin-marketplace](https://github.com/xai-org/plugin-marketplace) that adds a remote-source entry pointing at this repository, pinned to a full commit SHA. See `PUBLISHING.md`.
- **Grok Bot and Cursor**: submit this repository at [x.ai/bot/marketplace/submit-a-plugin](https://x.ai/bot/marketplace/submit-a-plugin). See `PUBLISHING.md`.
