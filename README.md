# Turquoise Health

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Turquoise Health is a healthcare price transparency platform. Its public developer surface is the Consumer Pricing API — a documented OpenAPI 3.1.0 REST API at api.turquoise.health covering providers, payers, networks, Standard Service Packages, and cash and insurer-negotiated rates for shoppable services, plus personalized out-of-pocket estimates computed by layering a member's live X12 270/271 eligibility and accumulators onto a negotiated rate. The same OAuth 2.0 client-credentials token also authenticates a hosted Model Context Protocol server at consumer-mcp.turquoise.health, which exposes five workflow-shaped pricing tools to AI agents. Turquoise also sells platform tiers for market intelligence, contract intelligence and precision contracting to providers, payers and life sciences organizations, generates CMS-compliant machine-readable files, and publishes free public tools including the Medicare Pricer, payer transparency scores and a hospital transparency tracker.

**Areas:** Healthcare · Price Transparency · Hospital Rates · Payer Rates · Machine-Readable Files · FHIR · Health Insurance · Negotiated Rates · Out-of-Pocket Costs · MRF · Consumer Pricing · MCP

## Turquoise Health on APIs.io

Everything profiled for Turquoise Health is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/turquoise-health/](https://apis.io/providers/turquoise-health/)** — or go straight to one of the APIs or properties below.

### APIs (3)

- [Turquoise Connector (MCP)](https://apis.io/apis/turquoise-health/turquoise-connector-mcp/) — Hosted Model Context Protocol server exposing Turquoise's consumer pricing to AI agents over streamable HTTP. Five workflow-shaped tools — find_entity,…
- [Standard Service Packages (SSP)](https://apis.io/apis/turquoise-health/standard-service-packages-ssp/) — Turquoise's open library of Standard Service Packages, which gather all medical services, materials and fees associated with a healthcare procedure and…
- [Turquoise Health Consumer Pricing API](https://apis.io/apis/turquoise-health/turquoise-health-consumer-pricing-api/) — The Consumer Pricing API from Turquoise Health — 15 operation(s) for consumer pricing.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [Turquoise Consumer Pricing MCP](https://apis.io/servers/turquoise-health/turquoise-consumer-pricing-mcp/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Turquoise Health Agentic Access](https://apis.io/agentic-access/turquoise-health/turquoise-health-agentic-access/)

#### JSON-LD (1)

Linked-data contexts for the provider's vocabulary.

- [Turquoise Health Context](https://apis.io/jsonld/turquoise-health/turquoise-health-context/)

#### Overlays (1)

OpenAPI overlays, including conversational phrasing for each operation.

- [Turquoise Health Consumer Pricing Overlay](https://apis.io/overlays/turquoise-health/turquoise-health-consumer-pricing-overlay/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Turquoise Health Authentication](https://apis.io/security/turquoise-health/turquoise-health-authentication/)
- [Turquoise Health Domain Security](https://apis.io/security/turquoise-health/turquoise-health-domain-security/)
- [Turquoise Health Trust Center](https://apis.io/security/turquoise-health/turquoise-health-trust-center/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Turquoise Health Scopes](https://apis.io/scopes/turquoise-health/turquoise-health-scopes/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Turquoise Health Plans Pricing](https://apis.io/plans/turquoise-health/turquoise-health-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Turquoise Health Rate Limits](https://apis.io/rate-limits/turquoise-health/turquoise-health-rate-limits/)

#### FinOps (1)

What the API costs to run, by operation.

- [Turquoise Health Finops](https://apis.io/finops/turquoise-health/turquoise-health-finops/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://turquoise.health/)
- [Documentation](https://turquoise.health/api/docs/)
- [Getting started](https://turquoise.health/api/docs/start-building/)
- [API reference](https://turquoise.health/api/docs/api-reference/)
- [Pricing](https://turquoise.health/plans/providers)
- [Sign up](https://turquoise.health/signup/?signupContext=api)
- [Blog](https://turquoise.health/resources/blog/)
- [Compliance](https://app.vanta.com/turquoise.health/trust/4eadhio8ef1h3zwumb77kp)
- [Terms of service](https://turquoise.health/legal/terms-of-service)
- [Privacy policy](https://turquoise.health/legal/privacy-policy)
- [GitHub](https://github.com/turquoisehealth)

## API Evangelist rating

**Kin Score 55.0** — strong · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 75.0 |
| Contract quality | 59.0 |
| Contract governance | 18.2 |
| Operational transparency | 2.6 |
| Developer ergonomics | 61.3 |
| Access clarity | 77.6 |

**Agent readiness 54.6** — agent-ready (gated down from agent-native)

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | verified |
| Auth Clarity | served (partial credit) |
| Idempotency | — |
| Error Semantics | verified |
| OpenAPI Examples | partial |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | — |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | served (partial credit) |
| Protected Resource Metadata | verified |
| Dynamic Client Registration | yes |
| Agentic Commerce | — |
| Pagination Ratio | partial |
| Operation Distinctiveness | distinct |

**Regulatory alignment 32.8** — Health

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/turquoise-health/](https://apis.io/providers/turquoise-health/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Turquoise Health: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/turquoise-health/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
