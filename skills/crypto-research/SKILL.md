---
name: crypto-research
description: Answer crypto questions with Blockworks data. Use when the user asks about a crypto asset, protocol, exchange, fund, token unlock, governance proposal, or market metric such as price, TVL, fees, revenue, volume or supply.
---

# Crypto research with Blockworks

The Blockworks MCP server gives you five read-only tools. Pick the tool from the kind of question.

| Question | Tool |
| --- | --- |
| Explanations, news, events, governance, diligence, anything you must cite | `search_documents` |
| Lists, rankings, attributes, point-in-time snapshots | `tabular_catalog`, then `tabular_data` |
| Values over time: price, volume, TVL, fees, revenue, supply | `timeseries_catalog`, then `timeseries_data` |

## Rules

1. Call the catalog tool before the data tool. Use the model slug, field names and operators exactly as the catalog returns them.
2. Series keys in time-series models are ids, not tickers. Resolve a name or ticker through the matching tabular model first.
3. Put concrete dates in `search_documents` queries ("in August 2026", "since 2026-08-01"). A query with no date returns mostly old documents.
4. Ask one thing in each `search_documents` call. Several focused calls give better results than one broad call.
5. To compute a change over a period, call `timeseries_data` with `bounds: true`.
6. Check `totalRows` from `tabular_data` before you say you saw every row.
7. Cite the title and url of every document you use. Do not state facts that the data or documents do not support.
