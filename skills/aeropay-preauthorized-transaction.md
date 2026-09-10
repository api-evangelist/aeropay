---
name: aeropay-preauthorized-transaction
description: Authorize a bank payment now and capture it later — create a preauth, adjust or cancel it before capture, then capture it into a real transaction.
api: Aeropay v2 API
base_url: https://api.aeropay.com/v2
generated: '2026-09-10'
method: generated
source: https://dev.aero.inc/docs/preauth-transaction-overview
operations:
  - POST /v2/token
  - POST /v2/preauthTransaction
  - GET /v2/preauthTransaction/{preauthTransactionId}
  - PATCH /v2/preauthTransaction/{preauthTransactionId}
  - DELETE /v2/preauthTransaction/{preauthTransactionId}
  - GET /v2/preauthTransactions
  - POST /v2/capturePreauthTransaction
---

# Authorize now, capture later

Use this for deliveries and preorders — anywhere the final amount is not known when the customer commits.
The user and bank-link steps are identical to `aeropay-standard-transaction`; start there and come back at
step 6.

## Create the authorization

`POST /v2/preauthTransaction` with a **`userForMerchant`**-scoped token. Returns a transaction object whose
`id` identifies the preauth from here on.

Send an `Idempotency-Key`. This is one of only four Aeropay operations that accept one.

Preauths have **no default expiration**, but the expiry is configurable per merchant — so there is a
merchant-specific outer bound Aeropay does not publish. Ask your Solutions Engineer what yours is before
you build a scheduler around it.

## Adjust it

`PATCH /v2/preauthTransaction/{preauthTransactionId}` changes the amount up or down, and can add tip
attributes. **The updated amount may not exceed the original authorized amount.**

This PATCH carries **no** `Idempotency-Key` — a retried adjustment has no replay protection.

## Cancel it

`DELETE /v2/preauthTransaction/{preauthTransactionId}`. This is the reversal path, and its window is
**before capture**. Once captured you get `AP312` "Preauth already captured"; if it is otherwise ineligible,
`AP313`.

Do not use `POST /v2/reverseTransaction` here — Aeropay says explicitly that reverseTransaction takes an
Aeropay `Transaction.id` and is only for AeroTransactions. Cancelling a preauth is the DELETE.

## Capture it

`POST /v2/capturePreauthTransaction` with the preauth `id`. Returns a normal Aeropay transaction.

**This is the riskiest call in the API for an agent.** It moves money and, alone among the money-movement
operations, it declares no `Idempotency-Key` header — so a retry after a timeout has nothing stopping it
from capturing twice. Confirm with `GET /v2/preauthTransaction/{id}` before retrying, never retry blindly.

Once captured, the resulting transaction falls under the standard reversal rules: same-business-day void,
then a 2-3 business day refund.

## List them

`GET /v2/preauthTransactions` with a merchant-scoped token returns the merchant's authorizations. Optional
`id` and `level` query parameters narrow it. Note this collection is **not paginated** — only
`POST /v2/transactionSearch` paginates.

## Errors worth handling

`AP009` unauthorized (wrong token scope), `AP312` already captured, `AP313` ineligible for capture,
`AP310` credit transaction declined, `AP302` insufficient funds, `AP1000` invalid API. Full list in
`errors/aeropay-error-codes.yml`. Remember these arrive inside an HTTP 200.

Subscribe to `preauthorized_transaction_created` to confirm creation asynchronously.
