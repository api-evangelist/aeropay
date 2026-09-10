---
name: aeropay-standard-transaction
description: Take a payment from a consumer's bank account to an Aeropay merchant — mint the two token scopes, create and verify the user, link a bank through Aerosync, then create the transaction and confirm it by webhook.
api: Aeropay v2 API
base_url: https://api.aeropay.com/v2
sandbox_url: https://api.sandbox-pay.aero.inc/v2
generated: '2026-09-10'
method: generated
source: https://dev.aero.inc/docs/standard-transaction-overview
operations:
  - POST /v2/token
  - POST /v2/user
  - POST /v2/confirmUser
  - GET /v2/aggregatorCredentials
  - POST /v2/linkAccountFromAggregator
  - GET /v2/bankAccounts
  - POST /v2/transaction
  - GET /v2/transaction/{transactionId}
---

# Take a standard Aeropay payment

Moves funds from an end user to the merchant. Aeropay's OpenAPI declares **no `operationId` on any
operation**, so every step below is addressed by method + path, which is how the contract and the
reference pages identify them.

## Before you start

- You need merchant `apiKey`, `apiSecret` and a numeric merchant `id`. They are **environment-specific** and
  carry no prefix — you cannot tell a sandbox key from a production one by looking at it. The wrong one
  returns `AP002`.
- Sandbox credentials are not self-serve. Email `support@aeropay.com` or book a demo.
- Read `conventions/aeropay-conventions.yml` first. The single most important rule: **Aeropay returns most
  errors inside an HTTP 200.** Branching on the status code alone will read a declined payment as a success.

## Step 1 — Mint a merchant-scoped token

`POST /v2/token` with `{"scope": "merchant", "apiKey": ..., "apiSecret": ..., "id": <merchantId>}`.

Returns a JWT. **It expires in 30 minutes** and there is no refresh flow — re-mint, do not cache past the TTL.
Send it as `authorization: Bearer {{token}}` on every subsequent call.

## Step 2 — Create the user

`POST /v2/user` with `firstName`, `lastName`, `phoneNumber` (US only, E.164 with `+1`), `email`.

Returns a `userId` **and an `mfaType`**. If `mfaType` is `sms` or `email`, an OTP was sent and you must do
step 3. If it is `null`, skip to step 4.

This write is **irreversible** — the API has no delete-user operation.

## Step 3 — Confirm the user

`POST /v2/confirmUser` with `userId`, `code`, `merchantId`. In sandbox, `000000` bypasses the challenge.

Once confirmed the user is a *returning* user at your merchant. On later sessions skip steps 2 and 3
entirely: mint a `userForMerchant` token and go straight to `GET /v2/bankAccounts`.

## Step 4 — Mint a userForMerchant token

`POST /v2/token` again, this time with `"scope": "userForMerchant"` **and** `userId`. Everything from here
acts as the user, not the merchant. Using the wrong scope returns `AP006`.

## Step 5 — Link a bank account

`GET /v2/aggregatorCredentials` returns a one-time widget URL and token. Launch Aerosync with it (see
`components/aeropay-components.yml`); the user authenticates with their own bank. The widget's `onSuccess`
returns `connectionId`, `clientName` and `aeroPassUserUuid`.

`POST /v2/linkAccountFromAggregator` with the `connectionId` attaches the account.

There is **no headless path** — routing and account numbers cannot be submitted through the API. And the
web widget will not load until an Aeropay Solutions Engineer has allowlisted your origin URLs.

If the user already linked a bank at another merchant, `GET /v2/bankAccounts` returns it without a
reconnect — that is the Aeropay network user, keyed on `aeroPassUserUuid`.

## Step 6 — Create the transaction

`POST /v2/transaction` with `amount` (`{"amount": <integer cents>, "currency": "USD"}`) and `merchantId`.
Optionally `bankAccountId` — omit it and the user's default account is used. Optionally `referenceId` for
your own reconciliation, and `attributes` for an invoice number or tip.

**Send an `Idempotency-Key` header.** It is optional, and Aeropay says plainly that without it "the API
does not apply this behavior and does not guarantee protection against duplicate refunds on retries."
Reusing a key with a different body returns `AP1400`; a key still in flight returns `AP1401`. The key is
retrievable for 1 day via `GET /v2/transaction/idempotency/{idempotencyKey}`.

## Step 7 — Read the result correctly

A `200` here means the request was accepted, not that it succeeded. Check the body:

- `{"transaction": {...}}` with `status: "pending"` — **this is success.** Aeropay guarantees pending funds;
  you may deliver goods. Settlement follows at the next batch window.
- `{"error": {"code": "APnnn", ...}}` or `{"success": false, "code": "APnnn", ...}` — this is a failure.
  Resolve the code against `errors/aeropay-error-codes.yml` (109 codes). The ones you will actually hit:
  `AP302` insufficient funds, `AP307` risk engine declined, `AP109` delinquent account, `AP304` amount
  exceeds limit, `AP400` no bank account linked, `AP314` transaction already exists.

## Step 8 — Wait for the webhook, not the response

An ACH return arrives **days** after the 200. Subscribe to `transaction_declined` and `transaction_completed`
(`POST /v2/webhook`) — the decline payload carries a NACHA `returnCode` (`R01` insufficient funds, `R02`
account closed, `R07` revoked authorization; all 70 are in `errors/aeropay-decline-codes.yml`). Aeropay
retries a failed delivery 5 times with exponential backoff if your endpoint does not answer 200 within
3000ms. There is no event replay API — a longer outage means calling your CSM.

## If you need to undo it

`POST /v2/reverseTransaction`. **Same business day**, before batching, it voids outright and no money moves.
After that it creates a reverse-direction refund that takes 2-3 business days. Partial amounts are allowed,
and a transaction may carry several independent reversals so long as they do not collectively exceed the
original (`AP1300` / `AP1302` when they would).
