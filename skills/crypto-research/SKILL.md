---
name: crypto-research
description: Answer crypto questions with Blockworks data. Use when the user asks about a crypto asset, protocol, exchange, fund, token unlock, governance proposal, market metric such as price, TVL, fees, revenue, volume or supply, recent industry developments, or their own watchlists.
---

# Crypto research with Blockworks

The Blockworks MCP server gives you read-only data tools plus one feedback tool. Pick the tool from the kind of question.

| Question | Tool |
| --- | --- |
| Explanations, news, governance, diligence, anything you must cite | `search_documents` |
| Lists, rankings, attributes, point-in-time snapshots | `tabular_catalog`, then `tabular_data` |
| Values over time: price, volume, TVL, fees, revenue, supply | `timeseries_catalog`, then `timeseries_data` |
| "What happened" in a window, for an asset or in a category, with the source documents | `monitoring_developments` |
| The arc of a story: related developments grouped over time | `monitoring_events` |
| Category slugs for the monitoring filters | `monitoring_categories` |
| What the user has told Blockworks to watch, and the view id that scopes a monitoring read | `monitoring_views` |
| The user's watchlists and the assets on them | `watchlists`, then `watchlist_assets` |
| A tool error, empty result, wrong data or missing capability you hit | `submit_feedback` |

## Rules

1. Call the catalog tool before the data tool. Use the model slug, field names and operators exactly as the catalog returns them.
2. Series keys in time-series models are ids, not tickers. Resolve a name or ticker through the matching tabular model first.
3. Put concrete dates in `search_documents` queries ("in August 2026", "since 2026-08-01"). A query with no date returns mostly old documents.
4. Ask one thing in each `search_documents` call. Several focused calls give better results than one broad call.
5. To compute a change over a period, call `timeseries_data` with `bounds: true`.
6. Check `totalRows` from `tabular_data`, `monitoring_developments` and `monitoring_events` before you say you saw every row.
7. Monitoring filters take category slugs from `monitoring_categories`, not names. Always pass `start` and `end` when the question is time-sensitive.
8. A `FORBIDDEN` result from a monitoring tool means the account is not entitled to the monitoring product. Relay it and stop; do not retry or try the other monitoring tools.
9. Cite the title and url of every document you use, including the source documents a development carries. Do not state facts that the data or documents do not support.
10. Report friction with `submit_feedback` before you answer: an error, an empty or surprising result, an identifier you had to guess, or data you could not find. Include the `request_id` of each result involved. You do not need to ask the user first, and the report must not include personal or sensitive data from the conversation.
