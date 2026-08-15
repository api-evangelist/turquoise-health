---
name: Produce a personalized out-of-pocket estimate
description: >-
  Compute what a specific insured member will actually owe for a shoppable procedure, by
  running a consented eligibility check and applying their live deductible and
  out-of-pocket accumulators. Use only when the patient has given their insurance details
  AND you are operating under a signed BAA with production access. This flow handles PHI.
api: openapi/turquoise-health-consumer-pricing-openapi.yml
base_url: https://api.turquoise.health
operations:
  - v3_list_packages
  - v3_list_networks
  - v3_list_providers
  - v3_list_personalized_estimates
  - v3_compare_personalized_estimates
required_scopes:
  - read:eligibility
generated: '2026-08-14'
method: generated
source: >-
  Grounded in operationIds verified in
  openapi/turquoise-health-consumer-pricing-openapi.yml plus the published error table and
  HIPAA guidance at https://turquoise.health/api/docs/personalized-estimates.md
---

# Produce a personalized out-of-pocket estimate

## Before you start — this flow handles PHI

`member_eligibility.first_name`, `last_name`, `date_of_birth` and `member_id` are
Protected Health Information under HIPAA.

- The **demo environment does not accept live patient data.** No PHI may be exchanged in
  it. If you are on demo credentials, do not run this flow with a real patient.
- Production access requires a **signed Business Associate Agreement** with Turquoise.
- Your token must carry the `read:eligibility` scope. Without it you get `403`
  `insufficient_scope`.
- Setting `consent_attested: true` is your representation that you obtained the patient's
  consent. **Never set it unless the patient actually consented in this conversation.**
- Do not log, echo back, or persist the eligibility block beyond what your BAA permits.

## 1. Resolve the package and the network

Same as the market-pricing flow: `v3_list_packages` for the service (`package_id`), then
`v3_list_networks` for the patient's plan (`network_id`). Optionally `v3_list_providers`
to pin `provider_id` when the patient already has a facility in mind.

Cost-share calculation is live for a **named subset** of networks — Aetna POS, Anthem CA
PPO, Anthem NY (Empire) PPO, ~30 BCBS plans, Cigna National OAP, and United Healthcare
Choice Plus. Negotiated rates cover far more networks than personalized estimates do. If
the patient's plan is outside that set, fall back to the market-pricing skill and say
plainly that you can give the negotiated rate but not their personal share.

## 2. Collect the member details

Ask for, and confirm back, all four:

| Field | Meaning |
|---|---|
| `first_name`, `last_name` | The patient's legal name, as on the insurance card |
| `date_of_birth` | ISO date, `1990-01-01` |
| `member_id` | The ID printed on the insurance card |
| `consent_attested` | Must be `true` — your attestation of consent |

## 3. Call the endpoint (`v3_list_personalized_estimates`)

```
POST /v3/personalized-estimates
Authorization: Bearer <token>
Content-Type: application/json

{
  "package_id": "RA005",
  "pricing": {"type": "negotiated", "network_id": "-3776001016975145508"},
  "provider_id": "5756",
  "location": {"zip": "80202"},
  "member_eligibility": {
    "first_name": "…", "last_name": "…",
    "date_of_birth": "…", "member_id": "…",
    "consent_attested": true
  }
}
```

Omit `provider_id` to get every provider and compare. Use
`v3_compare_personalized_estimates` (`POST /v3/personalized-estimates/compare`) when the
patient wants their personal cost ranked across providers rather than at one facility.

## 4. Handle the 202 — this is expected, not an error

**The first call for a given patient returns `202`**, "Eligibility check in progress, retry
after a short delay." The live X12 270/271 check with the payer takes a few seconds.

Wait a few seconds and retry the identical request. Subsequent calls return `200` quickly
because the eligibility response is cached. Tell the patient you are checking their
benefits rather than sitting silent. There is no `Retry-After` header — poll with a short
backoff, and give up after a reasonable number of attempts rather than hammering.

## 5. Read the estimate

`items[]`, one per provider:

- `total_allowed_amount` — the negotiated rate. **Not** what the patient pays.
- `member_cost_share.total` — **this is the number to show the patient.**
- The three components sum to that total: `amount_towards_deductible`,
  `amount_towards_copayment`, `amount_towards_coinsurance`.
- `is_deductible_met`, `is_out_of_pocket_max_met` — booleans describing state at or after
  this service.

Every money value is the standard `Money` shape: string `amount`, integer `minor_units`,
`currency`.

## 6. Explain it with `benefits_summary`

Returned once alongside `items[]`, this is why the number looks the way it does:

- `remaining_deductible` / `total_deductible` — "$800 of $2,000 met"
- `remaining_out_of_pocket_max` / `total_out_of_pocket_max`
- `deductible_accumulator_type`, `out_of_pocket_accumulator_type` — one of `embedded`,
  `aggregate`, `individual`, `zero`
- `benefit_categories[]` — the per-category rules that applied. `coinsurance` is a **rate**
  (`0.20` = 20%), plus `copayment` and a category `deductible`.
- `limitations[]` — plan limits such as visit or dollar caps. Empty when none apply.

Always pair the number with the reason. "You'd owe about $680 — $500 of that goes toward
your remaining deductible, and $180 is your 20% coinsurance on the rest" is the answer.
"$680" alone is not.

## 7. Handle member-matching errors — show these to the patient

Turquoise writes these messages to be shown to the patient so they can retry with
corrected details. Most are `422`s. Surface the message and offer a retry; do not treat
them as system failures.

| Code | HTTP | Say |
|---|---|---|
| `invalid_member_id` | 422 | Member ID doesn't match the payer's format — check the card |
| `invalid_dob` | 422 | Couldn't find the insurance with that date of birth |
| `invalid_member_name` | 422 | Couldn't find the insurance with that name |
| `member_not_found` | 422 | No member matched those details |
| `no_active_coverage` | 422 | Member found, but no active coverage |
| `unsupported_tpa` | 422 | Plan is managed by a third-party administrator Turquoise doesn't support yet |

And these are **not** the patient's fault — retry later, do not ask them to re-enter
anything:

| Code | HTTP | Do |
|---|---|---|
| `unavailable_payer` | 500 | Payer temporarily unavailable; retry later |
| `eligibility_service_error` | 500 | Retry later |
| `pricing_unavailable` | 503 | Retry later |
| `authorization_unavailable` | 503 | Retry later |
| `internal_server_error` | 500 | Retry later |

## Do not

- Do not set `consent_attested: true` on your own initiative, ever.
- Do not run this on demo credentials with a real patient's details.
- Do not present `total_allowed_amount` as what the patient owes — that is the negotiated
  rate, and confusing the two is the single most damaging error in this flow.
- Do not treat a `202` as a failure or retry with different data.
- Do not describe the result as a guarantee. It is an estimate based on accumulators at
  the moment of the check; claims in flight can change it.
