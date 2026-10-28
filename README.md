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

Aeropay is a Chicago-based fintech operating a pay-by-bank network that moves money directly between consumer bank accounts and merchants over ACH, Request for Payment (RfP) and RTP rails, without cards. The product suite is Aerosync (branded bank linking and account connectivity), Pay (merchant acceptance of bank payments), Payout (real-time merchant-to-consumer credits) and Guard (risk decisioning and fraud prevention). Integrators use the Aeropay v2 REST API — a 32-operation OpenAPI 3.0.0 contract covering token issuance, user creation and MFA confirmation, bank linking through an aggregator widget, standard, preauthorized, payout and reversal transactions, transaction search, payment links, webhook subscriptions, reporting and merchant reputation — alongside first-party Aerosync bank-linking SDKs for web, React Native, Android, iOS and Flutter. Aeropay publishes a documentation llms.txt, a remote MCP server for the API reference, a nine-topic webhook catalog, a 91-entry error glossary, a NACHA ACH return-code glossary and a substantial sandbox exception-scenario harness. It is SOC 2 compliant and audits its ACH operations annually against NACHA standards.

**Areas:** Payments · Pay by Bank · ACH · Open Banking · Fintech · Bank linking · Financial Services · Real-Time Payments · Webhook · Payouts · MCP · Risk & fraud

## Aeropay on APIs.io

Everything profiled for Aeropay is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/aeropay/](https://apis.io/providers/aeropay/)** — or go straight to one of the APIs or properties below.

### APIs (9)

- [Aeropay Webhooks](https://apis.io/apis/aeropay/aeropay-webhooks/) — Outbound HTTPS webhook deliveries covering nine topics across the ACH, RfP and RTP rails — transaction_completed, transaction_voided, transaction_refunded,…
- [Aeropay API MCP Server](https://apis.io/apis/aeropay/aeropay-mcp/) — A remote Model Context Protocol server hosted by Aeropay at https://dev.aero.inc/mcp, documented for Cursor, Windsurf and Claude Desktop. Anonymous…
- [Aeropay Authentication API](https://apis.io/apis/aeropay/aeropay-authentication-api/) — The Authentication API from Aeropay — 1 operation(s) for authentication.
- [Aeropay Bank Connection API](https://apis.io/apis/aeropay/aeropay-bank-connection-api/) — The Bank Connection API from Aeropay — 4 operation(s) for bank connection.
- [Aeropay Create & Manage Transactions API](https://apis.io/apis/aeropay/aeropay-create-manage-transactions-api/) — The Create & Manage Transactions API from Aeropay — 12 operation(s) for create & manage transactions.
- [Aeropay Merchant Management API](https://apis.io/apis/aeropay/aeropay-merchant-management-api/) — The Merchant Management API from Aeropay — 2 operation(s) for merchant management.
- [Aeropay Reports API](https://apis.io/apis/aeropay/aeropay-reports-api/) — The Reports API from Aeropay — 3 operation(s) for reports.
- [Aeropay User Management API](https://apis.io/apis/aeropay/aeropay-user-management-api/) — The User Management API from Aeropay — 2 operation(s) for user management.
- [Aeropay Webhooks API](https://apis.io/apis/aeropay/aeropay-webhooks-api/) — The Webhooks API from Aeropay — 2 operation(s) for webhooks.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Aeropay Webhooks](https://apis.io/asyncapis/aeropay/aeropay-webhooks-asyncapi/)

#### Event channels (9)

Individual events and webhooks the provider emits.

- [merchant_reputation_updated](https://apis.io/channels/aeropay/merchant-reputation-updated/)
- [preauthorized_transaction_created](https://apis.io/channels/aeropay/preauthorized-transaction-created/)
- [transaction_completed](https://apis.io/channels/aeropay/transaction-completed/)
- [transaction_declined](https://apis.io/channels/aeropay/transaction-declined/)
- [transaction_refunded](https://apis.io/channels/aeropay/transaction-refunded/)
- [transaction_resolved](https://apis.io/channels/aeropay/transaction-resolved/)
- [transaction_voided](https://apis.io/channels/aeropay/transaction-voided/)
- [user_active](https://apis.io/channels/aeropay/user-active/)
- [user_suspended](https://apis.io/channels/aeropay/user-suspended/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [api-aeropay](https://apis.io/servers/aeropay/api-aeropay/)

#### Overlays (1)

OpenAPI overlays, including conversational phrasing for each operation.

- [Aeropay V2 Overlay](https://apis.io/overlays/aeropay/aeropay-v2-overlay/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Aeropay Authentication](https://apis.io/security/aeropay/aeropay-authentication/)
- [Aeropay Domain Security](https://apis.io/security/aeropay/aeropay-domain-security/)
- [Aeropay Trust Center](https://apis.io/security/aeropay/aeropay-trust-center/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Aeropay Plans Pricing](https://apis.io/plans/aeropay/aeropay-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Aeropay Rate Limits](https://apis.io/rate-limits/aeropay/aeropay-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.aeropay.com/)
- [Documentation](https://dev.aero.inc/docs/getting-started)
- [Getting started](https://dev.aero.inc/docs/api-quick-start)
- [API reference](https://dev.aero.inc/reference/post_v2-token)
- [Pricing](https://www.aeropay.com/demo)
- [Sign up](https://portal.aeropay.com/login)
- [Support](https://www.aeropay.com/personal/support)
- [Changelog](https://dev.aero.inc/docs/release-notes)
- [Blog](https://www.aeropay.com/blog)
- [Compliance](https://www.aeropay.com/trust-center)
- [Terms of service](https://www.aeropay.com/legal/terms-of-service)
- [Privacy policy](https://www.aeropay.com/legal/privacy-policy)
- [GitHub](https://github.com/Aeropay-inc)

## API Evangelist rating

**Kin Score 47.6** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 75.0 |
| Contract quality | 54.3 |
| Contract governance | 18.2 |
| Operational transparency | 18.4 |
| Developer ergonomics | 58.9 |
| Access clarity | 47.4 |

**Agent readiness 42.6** — agent-native

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | — |
| Reversibility Documented | documented (partial credit) |
| MCP Server | platform (partial credit) |
| Auth Clarity | bearer (partial credit) |
| Idempotency | verified |
| Error Semantics | documented (partial credit) |
| OpenAPI Examples | verified |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | yes |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | paginated |
| Operation Distinctiveness | — |

**Regulatory alignment 29.9** — Banking & Open Finance

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/aeropay/](https://apis.io/providers/aeropay/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Aeropay: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/aeropay/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
