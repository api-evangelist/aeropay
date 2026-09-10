# Aeropay

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

Aeropay is a Chicago-based fintech operating a pay-by-bank network that moves money directly between
consumer bank accounts and merchants over ACH, Request for Payment (RfP) and RTP rails, without cards.
Its product suite is Aerosync (branded bank linking), Pay (merchant acceptance), Payout (real-time
merchant-to-consumer credits) and Guard (risk decisioning). Aeropay is SOC 2 compliant and audits its ACH
operations annually against Nacha standards. U.S. bank accounts only.

## What this profile found

| | |
|---|---|
| Machine-readable contract | **Yes** — OpenAPI 3.0.0, 26 paths, 32 operations |
| Where it was found | The ReadMe API registry backing the docs site, `https://dash.readme.com/api/v1/api-registry/dsdmfqmtkajkc6` |
| Production base URL | `https://api.aeropay.com/v2` |
| Sandbox base URL | `https://api.sandbox-pay.aero.inc/v2` |
| Remote MCP server | **Yes** — `https://dev.aero.inc/mcp`, anonymous `tools/list` returns 4 tools |
| Webhooks | 9 documented topics across ACH, RfP and RTP |
| First-party SDKs | 6 Aerosync bank-linking packages (npm x2, Maven Central, pub.dev, Swift PM, CDN) |
| Error surface | 109 AP-prefixed codes plus the full 70-entry Nacha ACH return-code registry |
| `/.well-known/` surface | **None** — 14 paths probed on 8 hosts, zero hits |
| Published pricing | **None** — `/pricing` serves the demo form |
| Status page | **None** — `status.aeropay.com` and `status.aero.inc` do not resolve |

## How the contract was found

The developer portal at `developer.aeropay.com` does not resolve. The live docs are at `dev.aero.inc`, a
ReadMe-hosted site whose `/openapi.json` returns an HTML 404 shell and whose API host answers
`403 {"message":"Missing Authentication Token"}` on every unmatched route — both of which look like "no
spec" on a shallow pass. The real contract was recovered from the ReadMe API registry UUID embedded in the
reference page source. Ownership was confirmed on the spec's own terms: `info.title` is "Aeropay v2 API",
`servers[]` is `api.sandbox-pay.aero.inc` (`aero.inc` is Aeropay's own developer domain), and the
description names `support@aeropay.com`.

Aeropay also publishes two `llms.txt` files — a 98-entry documentation index at `dev.aero.inc/llms.txt`
carrying per-operation error glossaries, and a curated marketing index at `www.aeropay.com/llms.txt`.

## Notable gaps

- **The contract declares no `operationId` on any of its 32 operations**, no `tags`, and an **empty
  `components.securitySchemes`** — so the bearer-token model that 31 operations require is invisible to any
  machine reading the spec alone. `servers[]` names only the sandbox host.
- **Aeropay returns most errors inside an HTTP 200.** Its own glossary opens with "NOTE: All Aeropay errors
  return with an HTTP 200 response." A client branching on status code alone reads a declined payment as a
  success.
- **Idempotency covers 4 of 16 mutating operations** — the four money-movement creates, and it is optional
  even there. `POST /v2/capturePreauthTransaction` moves money and has no idempotency key at all.
- **No API changelog.** The two "Release Notes" pages cover only the Aerosync SDKs, and their most recent
  dated entry is 2025-09-30 while the packages themselves shipped in August 2026. Material contract changes
  (multiple independent reversals, the idempotency surface, `payloadVersion: 2.0` webhooks) are announced
  only inside the affected reference page's prose.
- **No vulnerability disclosure program, no `security.txt`, no status page, no rate limits, no published
  pricing, and no self-serve sandbox** — credentials require an email to support or a sales demo.

## Reversibility

Graded **verified**. `POST /v2/reverseTransaction` voids a transaction outright within the same business
day and creates a 2-3 day reverse-direction refund after batching; `DELETE /v2/preauthTransaction/{id}`
cancels an authorization before capture. Payouts and payment links have **no documented reversal** and are
recorded as irreversible. See `conventions/aeropay-conventions.yml`.

## Artifacts in this repository

`openapi/` `openapi/_original/` `overlays/` `mcp/` `asyncapi/` `llms/` `well-known/` `packages/`
`authentication/` `conventions/` `errors/` `data-model/` `conformance/` `lifecycle/` `changelog/`
`components/` `sandbox/` `security/` `plans/` `rate-limits/` `skills/`

- https://www.aeropay.com/
- https://dev.aero.inc/docs/getting-started
