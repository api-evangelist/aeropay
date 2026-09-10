---
name: aeropay-payout-and-refund
description: Move money from the merchant back to the consumer — either a payout (a fresh credit) or a reversal of a prior debit — and understand which one is undoable.
api: Aeropay v2 API
base_url: https://api.aeropay.com/v2
generated: '2026-09-10'
method: generated
source: https://dev.aero.inc/docs/payout-transaction-overview
operations:
  - POST /v2/token
  - POST /v2/payoutTransaction
  - POST /v2/reverseTransaction
  - GET /v2/transaction/{transactionUUID}/refunds
  - GET /v2/transaction/{transactionId}
---

# Send money back to a user

Two different operations, two different consequence profiles. Pick deliberately.

## Payout — a fresh credit

`POST /v2/payoutTransaction` with a **merchant**-scoped token. Pays an amount from the merchant to a named
user. Use it for withdrawals and rewards — money that was never debited in the first place.

Accepts an `Idempotency-Key`. Send one.

**A payout has no documented reversal.** `POST /v2/reverseTransaction` states its id "is only for
AeroTransactions", and `AP1304` "Transaction type cannot be refunded" exists, but Aeropay never states
whether a payout can be undone. Treat a payout as **irreversible** until Aeropay says otherwise. If an agent
is deciding autonomously, this is the operation to escalate to a human.

Watch for `AP305` account blocked for delinquent activity, `AP402` no bank account for merchant, and
`AP900` external API error.

## Reversal — undoing a prior debit

`POST /v2/reverseTransaction` with the **Aeropay `Transaction.id`** of the original debit.

Two behaviours, decided by timing and not by you:

- **Within the same business day, before batching** — the payment is voided outright. No money moves in
  either direction. The webhook topic is `transaction_voided`.
- **After batching** — a reverse-direction transaction is created. It takes 2-3 business days. The webhook
  topic is `transaction_refunded`.

Pass an `amount` for a partial refund; omit it to refund in full. Accepts an `Idempotency-Key`, and Aeropay
is explicit that without one it "does not guarantee protection against duplicate refunds on retries" — on
this operation in particular, send it.

You may issue **multiple independent reversals** against one transaction. Aeropay changed this: concurrent
partial refunds used to be silently merged into a single record and no longer are. Each call produces its
own record with its own uuid. The collective total may not exceed the original — `AP1300` when the original
is already fully covered, `AP1302` when the amount would exceed it, `AP1303` when it is not positive.

You may supply your own `uuid` on the request; `AP314` means it collides with an existing reversal.

RTP transactions cannot be voided — `AP1305` tells you to schedule a refund instead.

## Check what has already been refunded

`GET /v2/transaction/{transactionUUID}/refunds` returns two arrays, both always present and empty rather
than absent:

- `refunds` — reversals that already have a reversal transaction. Shape matches a standard transaction.
- `queuedRefunds` — requested but not yet processed, each with `id`, `referenceId`, `amount` and a `status`
  of `queued` or `abandoned`.

The `id` on a queued item is the uuid assigned at `POST /v2/reverseTransaction` time, and it **becomes** the
id of the resulting reversal transaction. The same value follows the refund through both states, so you can
join across them safely.

**Call this before issuing another reversal.** It is the only way to know how much of the original is still
refundable, and it is cheaper than discovering it through `AP1302`.
