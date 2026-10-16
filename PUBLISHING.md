# Publishing the Blockworks plugin for Grok

Two routes exist, and they lead to different listings. Do both.

## 1. Grok Build marketplace (pull request)

The catalog lives in [xai-org/plugin-marketplace](https://github.com/xai-org/plugin-marketplace). A PR adds one entry to `.grok-plugin/marketplace.json` that points at this repository, pinned to a commit.

1. Push the release commit to `main` here and get its SHA:

   ```
   git ls-remote https://github.com/Blockworks-Projects/grok-plugin.git HEAD
   ```

2. Fork `xai-org/plugin-marketplace`, branch from `main`, and append this entry to the `plugins` array in `.grok-plugin/marketplace.json`. Replace the `sha` with the full 40-character commit SHA from step 1:

   ```json
   {
     "name": "blockworks",
     "description": "Crypto research and market data from Blockworks: search research, news and governance documents, and query asset, protocol and market datasets.",
     "category": "productivity",
     "source": {
       "source": "url",
       "url": "https://github.com/Blockworks-Projects/grok-plugin.git",
       "sha": "0000000000000000000000000000000000000000"
     },
     "homepage": "https://docs.blockworks.com/mcp/overview",
     "keywords": ["blockworks", "blockworks research", "blockworks data", "blockworks mcp"],
     "domains": ["blockworks.com", "app.blockworks.com", "docs.blockworks.com"]
   }
   ```

3. Regenerate the component index and validate. CI runs exactly these:

   ```
   python3 scripts/generate-plugin-index.py
   python3 scripts/validate-catalog.py
   python3 scripts/generate-plugin-index.py --check
   ```

4. Open the PR, fill in the template, and wait for CI and code-owner review.

Review checks that the source is the official org (it is: `Blockworks-Projects`), that the SHA is a real public commit, that keywords and domains are brand-scoped (they are), and that the plugin runs no local code and no hooks (it does not).

To ship an update later, push to `main` here, then open a PR that bumps the `sha` and regenerates the index. Do not open a second entry.

## 2. Grok Bot and Cursor marketplace (web form)

Submit at [x.ai/bot/marketplace/submit-a-plugin](https://x.ai/bot/marketplace/submit-a-plugin). The form asks for:

| Field | Value |
| --- | --- |
| Organization name | Blockworks |
| Organization handle | `blockworks` |
| Website URL | https://blockworks.com |
| Logotype URL | https://raw.githubusercontent.com/Blockworks-Projects/grok-plugin/main/assets/logo.png (1:1 PNG) |
| Listing destination | Grok Bot and Cursor (and Grok, once it opens) |
| MCP server URL or public GitHub repository | https://github.com/Blockworks-Projects/grok-plugin |
| Brief description | Crypto research and market data from Blockworks: search research, news and governance documents, and query asset, protocol and market datasets. |
| Testing instructions | See below |

Suggested testing instructions:

> Create a Blockworks API key at https://app.blockworks.com/account/api. Install the plugin. On the first tool call a Blockworks sign-in page opens; paste the API key and submit. Then ask: "Show the top 10 assets by market cap today." The agent should call `tabular_catalog` and then `tabular_data` and return a table with dataset name and date. Then ask: "Show the daily price of ETH for the last 90 days." The agent should call `timeseries_catalog` and then `timeseries_data`. `search_documents` requires an Enterprise key with the `ai_toolkit_permission` permission; other datasets work with a free key where the catalog shows tier `public`.

Submitting means agreeing to the publisher terms at cursor.com/marketplace-publisher-terms. Test the plugin locally and in-app before ticking the confirmation box.
