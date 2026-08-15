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
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Healthcare price transparency platform with a public Consumer Pricing API for
cash and insurer-negotiated rates on shoppable services, personalized member
out-of-pocket estimates, and a hosted MCP server for AI agents.

**Website:** https://turquoise.health/  
**Developer docs:** https://turquoise.health/api/docs/  
**OpenAPI:** https://turquoise.health/api/docs/openapi.json  
**llms.txt:** https://turquoise.health/api/docs/llms.txt  
**GitHub:** https://github.com/turquoisehealth  
**Blog:** https://turquoise.health/resources/blog/  
**LinkedIn:** https://www.linkedin.com/company/turquoise-health/  
**X / Twitter:** https://twitter.com/TurquoiseHC  

## APIs

- **Turquoise Consumer Pricing API** — `https://api.turquoise.health`. OpenAPI 3.1.0,
  15 operations across providers, payers, networks, Standard Service Packages, prices and
  personalized estimates. See [openapi/](openapi/).
- **Turquoise Connector (MCP)** — `https://consumer-mcp.turquoise.health/mcp`. Hosted,
  remote, streamable HTTP, Public Preview. Five workflow-shaped tools. See [mcp/](mcp/).
- **Standard Service Packages (SSP)** — the addressing scheme for the whole API, served
  through its `/v3/packages` operations. No separate host.

## Authentication

One OAuth 2.0 client-credentials token authenticates **both** the REST API and the MCP
server. Token endpoint `https://api.turquoise.health/oauth/token`; the request body is
JSON and requires `organization_id` alongside `client_id`/`client_secret`. MCP calls need
the `read:mcp` scope; personalized estimates need `read:eligibility` plus a signed BAA.
See [authentication/](authentication/) and [scopes/](scopes/).

## Notable

- **PHI-bearing surface.** `POST /v3/personalized-estimates` runs a consented X12 270/271
  eligibility check. Demo accounts may not send live patient data. See
  [sandbox/](sandbox/) and [conventions/](conventions/).
- **Real discovery documents.** RFC 8414 and RFC 9728 OAuth metadata are served
  anonymously on the MCP host — see [well-known/](well-known/).
- **Measured absences.** No status page, no changelog, no deprecation policy, no
  `security.txt`, no agent card, no event/webhook surface, no idempotency contract, and
  no client SDK in any language. Each is recorded where it belongs rather than omitted.

## Artifacts

| Area | File |
|---|---|
| Contract | [openapi/](openapi/), [overlays/](overlays/) |
| Agents | [mcp/](mcp/), [skills/](skills/), [llms/](llms/), [agentic-access/](agentic-access/) |
| Runtime semantics | [conventions/](conventions/), [errors/](errors/), [data-model/](data-model/), [rate-limits/](rate-limits/) |
| Access | [authentication/](authentication/), [scopes/](scopes/), [sandbox/](sandbox/), [plans/](plans/) |
| Trust | [security/](security/), [conformance/](conformance/), [well-known/](well-known/) |
| Operations | [lifecycle/](lifecycle/), [packages/](packages/), [finops/](finops/) |
