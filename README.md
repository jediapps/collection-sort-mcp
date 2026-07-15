# Jedi Collection Sort — MCP Server

**The first Shopify collection-sort app with a native [MCP](https://modelcontextprotocol.io) server.** Manage how products are ordered in your Shopify collections by chatting with Claude — or any MCP-capable client.

[Jedi ‑ Collection Sort Pro](https://apps.shopify.com/jedi-collection-sort) automatically sorts products in Shopify collections: push down sold-out and out-of-stock items, surface bestsellers and new arrivals, AI multi-parameter sort, A/B testing, and analytics. This MCP server lets an AI assistant read and control that sorting for your store.

Things you can say once connected:

- *"Which of my collections haven't been sorted in the last week?"*
- *"Assign the 'New arrivals first' strategy to the Summer collection and sort it now."*
- *"Show me the layer configuration of my Default strategy."*
- *"Turn off sorting on the Clearance collection."*

## Tools

| Tool | What it does |
|---|---|
| `list_strategies` | List the store's sorting strategies (id, title, sort frequency, layer count) |
| `get_strategy` | Get one strategy including its full layer configuration |
| `list_collections` | List collections managed by Collection Sort, with strategy, sorting status, and last-sorted time (`active_only` filter) |
| `get_collection_config` | Get one collection's sorting config: assigned strategy with layers, status, and any pending sort job |
| `set_collection_sorting` | Assign a strategy to a collection and/or activate or deactivate sorting; changing the strategy of an active collection queues a re-sort |
| `sort_collection_now` | Queue an on-demand sort — idempotent, with a short cooldown after a completed sort |
| `list_sort_jobs` | List queued and processing sort jobs, newest first |

All tools are bound to the authenticated shop; a connection can only ever see and manage its own store.

## Requirements

1. Install [Jedi ‑ Collection Sort Pro](https://apps.shopify.com/jedi-collection-sort) from the Shopify App Store.
2. A paid plan (from $2.99/month) — the MCP server and public API are included in every paid tier.

## Connect from claude.ai / Claude Desktop (OAuth)

1. In the app, open **Settings → Integrations** and copy your **MCP server URL**, then generate a **connect code** (one-time, expires in 10 minutes).
2. In claude.ai: **Settings → Connectors → Add custom connector**, paste the MCP server URL.
3. Claude opens the consent page — enter your connect code to link your store.

The server is a full OAuth 2.1 authorization server (dynamic client registration, PKCE, refresh-token rotation), so any OAuth-capable MCP client works the same way.

## Connect from Claude Code / header-capable clients (API key)

Generate an API key in **Settings → Integrations** (shown once, stored hashed), then:

```bash
claude mcp add collection-sort --transport http <your MCP server URL>/mcp \
  --header "Authorization: Bearer jcs_..."
```

## Transport & security

- Streamable HTTP transport, stateless — works with any MCP client that supports remote servers.
- OAuth access and refresh tokens, API keys, and connect codes are stored hashed and are revocable from the app; everything is cleaned up on app uninstall.
- Rate limited (60 requests/min per key) with idempotent sort triggering, so agent retry loops can't queue duplicate work.

## REST API

The same operations are available as a REST API (`/public-api/v1`) with Bearer API-key auth — strategies, collections, sort triggering, and job status. See the [documentation](https://docs.push-down-products.jediapps.com) for endpoints and error codes.

## Support

- Docs & FAQ: [docs.push-down-products.jediapps.com](https://docs.push-down-products.jediapps.com)
- App listing & support: [apps.shopify.com/jedi-collection-sort](https://apps.shopify.com/jedi-collection-sort)

---

This repository documents the hosted MCP server operated by [Jedi Apps](https://apps.shopify.com/partners/gamenow) as part of Jedi ‑ Collection Sort Pro. The server itself runs as part of the app's infrastructure — there is nothing to install from this repo.
