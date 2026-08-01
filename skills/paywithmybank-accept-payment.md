---
name: Accept a Pay-by-Bank payment
description: Establish a Trustly (PayWithMyBank) transaction and capture funds from a consumer bank account.
api: openapi/paywithmybank-openapi-original.yml
operations:
  - post-establish
  - post-transactions-transactionId-capture
  - get-transaction
---

# Accept a Pay-by-Bank payment (Trustly / PayWithMyBank)

Use the Trustly Payments API to move money directly from a consumer's bank
account. Base URL: `https://api.trustly.one/api/v1` (production) or
`https://sandbox.trustly.one/api/v1` (sandbox).

## Auth
HTTP Basic over HTTPS: username = `accessId`, password = `accessKey` (both from
the Merchant Portal). Send an `Idempotency-Key` header on every write so retries
are safe. Client-side Lightbox calls also require a server-generated
`requestSignature` (see `components/`).

## Steps
1. **`post-establish`** — `POST /establish`. Create the transaction with amount,
   currency, and beneficiary/account context. The consumer authorizes via the
   Trustly Lightbox; you receive a `transactionId`.
2. **`get-transaction`** — `GET /transactions/{transactionId}`. Poll or wait for
   the `Authorize`/`Completed` event notification (webhook) to confirm state.
3. **`post-transactions-transactionId-capture`** — `POST /transactions/{transactionId}/capture`.
   Capture the authorized funds.

## Errors
Non-2xx responses return a `BaseException` (`domain`, `code`, `location`,
`message`, `occurredAt`). `401` = bad Basic credentials; `400` = invalid/missing
fields. See `errors/paywithmybank-problem-types.yml`.

## Events
Subscribe to the `Authorize`, `Completed`, `Failed`, and `Expired` event
notifications rather than tight-polling. See `asyncapi/paywithmybank-webhooks.yml`.
