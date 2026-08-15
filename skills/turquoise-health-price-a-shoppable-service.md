---
name: Price a shoppable healthcare service
description: >-
  Find what a shoppable procedure costs near a patient, using Turquoise Health's cash and
  insurer-negotiated rates, and rank providers by price. Use this when the patient has not
  given insurance member details, or when you only need a market price rather than their
  personal out-of-pocket cost.
api: openapi/turquoise-health-consumer-pricing-openapi.yml
base_url: https://api.turquoise.health
operations:
  - v3_list_packages
  - v3_list_providers
  - v3_list_networks
  - v3_query_prices
  - v3_compare_prices
  - v3_get_price
generated: '2026-08-14'
method: generated
source: >-
  Grounded in operationIds verified in
  openapi/turquoise-health-consumer-pricing-openapi.yml plus the conventions in
  conventions/turquoise-health-conventions.yml.
---

# Price a shoppable healthcare service

## Authenticate first

Every request needs a bearer token. Mint one with the OAuth 2.0 client-credentials flow —
note the body is **JSON**, not form-encoded, and `organization_id` is required alongside
the usual client pair:

```
POST https://api.turquoise.health/oauth/token
Content-Type: application/json

{"grant_type":"client_credentials","client_id":"…","client_secret":"…","organization_id":"…"}
```

Send `Authorization: Bearer <access_token>` on every call. Cache the token — do not mint
one per request. On a `401`, mint a new token and retry once.

## 1. Resolve the service to a package (`v3_list_packages`)

Prices are addressed by **Standard Service Package**, not by raw CPT code. Resolve the
patient's plain-language service first:

```
GET /v3/packages?search=colonoscopy&min_score=0.6
```

Use `search` for plain language and `anchor_code` when you already have a billing code.
Read `items[].id` (e.g. `RA005`) — that is your `package_id`.

If several packages come back with close `context.score` values, **ask the patient which
one** rather than picking the top hit. Screening and diagnostic versions of the same
procedure are different packages with different prices.

## 2. Decide the payment basis

Two bases, and you must have one before any dollar figure exists:

- `{"type": "cash"}` — self-pay / uninsured.
- `{"type": "negotiated", "network_id": "…"}` — insured. Resolve the network first with
  `v3_list_networks` (`GET /v3/networks?search=Cigna National OAP`). Network ids are
  string-wrapped 64-bit integers and can be negative — keep them as strings.

## 3. Get the local price distribution (`v3_compare_prices`)

Before naming a single provider, get the shape of the market so you can say whether a
price is high or low:

```
POST /v3/prices/compare
{"package_id":"RA005","pricing":{"type":"cash"},"location":{"zip":"80202"}}
```

Returns `count` and `stats` with `min`, `max`, `avg`, `median`, `q1`, `q3` — each a `Money`.
If `count` is `0`, `stats` is `null`; say there is not enough local data rather than
inventing a range.

## 4. Rank providers (`v3_query_prices`)

```
POST /v3/prices/query
{"package_id":"RA005","pricing":{"type":"cash"},"location":{"zip":"80202"}}
```

Send exactly **one** location form or you will get `conflicting_parameters`:

- `{"zip": "80202"}` — resolves the ZIP to its centroid, then runs a radius search
- `{"near": {"lat": 39.745961, "lng": -104.971559, "radius_m": 25000}}`
- `{"within": {"state": "CO"}}` or `{"within": {"cbsa": "Denver-Aurora-Centennial, CO"}}`
  or `{"within": {"zip_codes": "80202,80203"}}`

Each item is a `ProviderPackagePrice` with the full `provider` inlined, a `package` stub,
`pricing`, and `total` as a `Money`. Use `total.minor_units` for arithmetic and
`total.amount` for display.

## 5. Explain one price (`v3_get_price`)

To show what is actually included:

```
GET /v3/prices/{price_id}?expand=line_items
```

`line_items[]` carries `code`, `code_type`, `fee_type` (`base_code`, `facility_fee`,
`professional_fee`, `optional_fee`), `description` and `association_rate`.

**`association_rate` is a probability (0-1), not a certainty.** Package composition is
probabilistic. Say "usually includes" for a high rate and "may include" for a low one.
Never present a low-rate line item as guaranteed.

## Paginate

`page_size` (default 25, max 250) and `cursor`. Follow `page.next_cursor` until it is
`null`. `page.total` is the full filtered count, not the page count.

## Handle these correctly

| Signal | What it means | Do |
|---|---|---|
| `NoDataReason: no_data` on a 200 | No price for that combination | Say so; suggest widening the location or trying cash |
| `NoDataReason: permission_denied` on a 200 | Your org is not entitled to that data | Do **not** tell the patient there is no price — this is an entitlement issue |
| `422` + `location_ambiguous` | Location too vague | Ask for a ZIP |
| `422` + `unresolvable_location` | Bad ZIP/state/CBSA | Ask the patient to confirm |
| `400` + `invalid_id_format` | Wrong id shape | Re-resolve via the list endpoint |
| `400` + `conflicting_parameters` | More than one location form | Send exactly one |
| `429` + `rate_limited` | Rate limited | Back off and retry — no `Retry-After` header is published, so use exponential backoff |
| `500` + `search_unavailable` | Semantic search is down | Fall back to `name` / `npi` / `type` filters |

## Do not

- Do not quote a price without a payment basis and a location. There is no such thing as
  a single national price for a package.
- Do not present a `compare` average as "your price" — it is a market statistic.
- Do not treat `id` values as durable. Provider, payer, network and package ids are
  upstream-derived and may change when reference data reloads. Re-resolve by NPI or SSP
  code across sessions.
- Do not call this flow when the patient wants their real out-of-pocket cost — use the
  personalized-estimate skill instead.
