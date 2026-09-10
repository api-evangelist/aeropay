---
name: aeropay-webhooks-and-reconciliation
description: Subscribe to Aeropay's nine webhook topics, verify and handle deliveries, and reconcile settled money with the CSV, totals and batch reports.
api: Aeropay v2 API
base_url: https://api.aeropay.com/v2
generated: '2026-09-10'
method: generated
source: https://dev.aero.inc/docs/webhooks-1
operations:
  - POST /v2/token
  - POST /v2/createWebhookSigningKey
  - POST /v2/webhook
  - GET /v2/webhook
  - DELETE /v2/webhook
  - POST /v2/transactionSearch
  - GET /v2/reports/transactions/CSV
  - GET /v2/reports/transactions/totals
  - GET /v2/reports/transactions/batches
---

# Know what actually happened

An Aeropay `POST /v2/transaction` returning 200 tells you the request was accepted. Everything that matters
afterwards — settlement, ACH returns, recovery — arrives asynchronously. This skill is how you find out.

## Set up signing first

`POST /v2/createWebhookSigningKey` with a merchant-scoped token, **before** you register any endpoint.
Verification is documented at https://dev.aero.inc/docs/webhook-security. Also allowlist Aeropay's delivery
origins: sandbox `3.223.196.167` and `34.235.82.59`, production `54.237.135.163` and `54.81.239.48`.

## Subscribe

`POST /v2/webhook` with `{"topic": ..., "url": ...}`. The same call creates **or updates** the subscription
for that topic, so it is safe to re-run. `GET /v2/webhook` reads back a topic; `DELETE /v2/webhook` removes
by topic and/or webhook id and returns the details of everything it deleted.

`AP1104` means the topic string is unrecognised. The nine valid topics:

| Topic | Rails | When |
|---|---|---|
| `transaction_completed` | ACH, RfP, RTP | Approved by Aeropay. Batches at the next window (<24h); stays `pending` until 3 business days after creation. |
| `transaction_voided` | ACH, RfP, RTP | Stopped before batching. No money moved. |
| `transaction_refunded` | ACH, RfP, RTP | Refunded after batching. Not sent for a pre-batch void. |
| `transaction_declined` | ACH, RfP, RTP | Declined. **Carries the NACHA return code.** |
| `transaction_resolved` | ACH | A previously declined transaction was recovered. |
| `preauthorized_transaction_created` | ACH | A preauth was created. |
| `user_suspended` | — | Payload is a userId. |
| `user_active` | — | A suspended user was reactivated. |
| `merchant_reputation_updated` | — | After a successful `POST /v2/merchantReputation`. |

## Handle the delivery

Answer **HTTP 200 within 3000ms** or Aeropay retries — up to 5 attempts with exponential backoff and jitter.
Do your work asynchronously; acknowledge first.

There is no event replay API. If you are down longer than the retry budget, missed events are recovered by
contacting your Aeropay CSM. Build reconciliation (below) rather than relying on the webhook alone.

Payloads carry `payloadVersion: "2.0"` and a `merchantUserReputation` enum (`standard` | `vip` | `blocked`,
or `null` when unknown). It sits at the top level of `data` on every topic **except** `transaction_refunded`,
where it is at `data.transaction.merchantUserReputation` and is absent from the `refundTransaction` and
`queuedRefunds` entries. Read it from the right place per topic.

`transaction_resolved` re-delivers your **original declined transaction** with the same payload shape as
`transaction_completed` — it is not a new transaction. Match it on the original id.

## Reconcile

Three report operations, all merchant-scoped:

- `GET /v2/reports/transactions/CSV` — transactions as a CSV file.
- `GET /v2/reports/transactions/totals` — `chargeTotal`, `tipTotal`, `feeTotal`, `rewardsTotal`,
  `payoutsTotal`, `refundsTotal`, `netTotal`, `returns`.
- `GET /v2/reports/transactions/batches` — ACH batches and their transactions for a time range. This is the
  one that ties Aeropay activity to a bank deposit.

For ad-hoc lookups use `POST /v2/transactionSearch` — the **only** paginated operation in the API. Send
`page`, `perPage`, `sortBy`, `orderBy` (all typed as strings in the contract, not integers), plus
`timezone`, `startDate` / `endDate` in `MM-DD-YYYY HH:mm:ss`, a `searchType`, and `filters.paymentType` or
`filters.referenceId`. Read the `paging` object in the response for totals.

Set your own `referenceId` on every transaction at creation time. It is the only field that joins Aeropay's
ledger to yours, and `filters.referenceId` is what makes it searchable.

## Reading statuses

`pending` = approved and guaranteed, treat as success. `processed` = batched and debited. `void` = stopped
before batching (if it was a payout, re-credit the user's wallet). `resolved` = declined then recovered.
`declined` = ACH return; check `returnCode` against `errors/aeropay-decline-codes.yml`.
