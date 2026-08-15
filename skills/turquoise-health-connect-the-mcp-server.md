---
name: Connect to the Turquoise Consumer Pricing MCP server
description: >-
  Reach Turquoise Health's hosted MCP server from an agent or backend — mint the right
  token, call the five pricing tools, and know which REST operations each one composes.
  Use this when you want conversational pricing without orchestrating the REST API
  yourself.
api: mcp/turquoise-health-mcp.yml
mcp_endpoint: https://consumer-mcp.turquoise.health/mcp
required_scopes:
  - read:mcp
operations:
  - v3_list_packages
  - v3_list_providers
  - v3_list_networks
  - v3_list_payers
  - v3_query_prices
  - v3_compare_prices
  - v3_get_price
  - v3_list_personalized_estimates
generated: '2026-08-14'
method: generated
source: >-
  Grounded in https://turquoise.health/api/docs/mcp-reference.md and
  https://turquoise.health/api/docs/llms.txt, with tool-to-operation bindings from
  mcp/turquoise-health-tool-crosswalk.yml. Tool input schemas are not reproduced here
  because tools/list is OAuth-gated (probed 401, 2026-08-14).
---

# Connect to the Turquoise Consumer Pricing MCP server

Turquoise ships a **hosted, remote** MCP server. There is no package to install — an agent
POSTs to the endpoint directly.

```
https://consumer-mcp.turquoise.health/mcp
```

Transport is **streamable HTTP**. The server is in **Public Preview**.

## Two ways in

**Interactive client** (Claude Code, Claude Desktop, Codex, Cursor) — add the server URL
and sign in through the browser with a Turquoise account. No token to manage; the server
scopes calls to that user's organization. [Sign up for a demo
account](https://turquoise.health/signup/?signupContext=api) first.

**Production agent or backend** — mint a token yourself:

```
POST https://api.turquoise.health/oauth/token
Content-Type: application/json

{"grant_type":"client_credentials","client_id":"…","client_secret":"…","organization_id":"…"}
```

Send it as `Authorization: Bearer <access_token>` on every `/mcp` request, along with
`Accept: application/json, text/event-stream`.

**The token must carry the `read:mcp` scope.** The same token authenticates both the MCP
server and the REST API.

| Response | Meaning |
|---|---|
| `200` | Auth OK; token valid with correct scope |
| `401` | Missing, expired, or wrong-audience token |
| `403` | Token valid but not granted the correct scope |

Discovery metadata is served anonymously if you need it:
`/.well-known/oauth-authorization-server` (RFC 8414) and
`/.well-known/oauth-protected-resource/mcp` (RFC 9728). Note that neither advertises
`read:mcp` — the scope is documented in prose only, so hard-code it.

## The five tools

These are **workflow-shaped, not one-per-endpoint**. Each resolves plain-language names and
composes several v3 REST calls server-side.

| Tool | What it does | REST it composes |
|---|---|---|
| `find_entity` | Find services, providers, plans or payers by name, exact ID, or relationship | `v3_list_packages`, `v3_list_providers`, `v3_list_networks`, `v3_list_payers` |
| `compare_prices` | Price ranges and ranked provider lists for a service in an area | `v3_compare_prices`, `v3_query_prices` |
| `provider_cost_detail` | One provider's price, its expected billing components, and how it compares locally | `v3_query_prices`, `v3_get_price?expand=line_items`, `v3_compare_prices` |
| `explain_pricing` | How a price was derived and how to read it | none — static methodology content |
| `estimate_out_of_pocket` | What a member will personally owe, via a consented eligibility check | `v3_list_personalized_estimates` |

## The `needs` field — read this before you build

The pricing tools require a **payment basis** (`cash` or `insurance`) and a **location**
before they will return a dollar figure. When something is missing, the tool returns a
`needs` field naming what to ask the patient for, **instead of a price**.

Treat `needs` as the tool telling you what question to ask next. Do not fabricate a
default location or assume cash — ask.

## Example call

```
POST https://consumer-mcp.turquoise.health/mcp
authorization: Bearer $TOKEN
accept: application/json, text/event-stream
content-type: application/json

{"jsonrpc":"2.0","id":1,"method":"tools/call",
 "params":{"name":"compare_prices",
           "arguments":{"service":"MRI with contrast","zip_code":"78701","payment":"cash"}}}
```

## `estimate_out_of_pocket` is gated further

It runs a consented member eligibility check, so it additionally requires:

- the **`read:eligibility`** scope, and
- for real patient data, **production access under a signed BAA**.

Everything in the personalized-estimate skill applies — PHI handling, the consent
attestation, and the `202`-then-retry pattern. Do not call this tool with real member
details on a demo account.

## When to drop to REST instead

Use the REST API directly when you need pagination control, the full `line_items`
composition of a package, provider-type taxonomy, or the personalized-estimate *compare*
variant — none of which has a dedicated tool. See
`mcp/turquoise-health-tool-crosswalk.yml` for the seven REST operations with no MCP tool.
