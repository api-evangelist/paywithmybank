---
name: Refund a settled transaction
description: Look up a Trustly (PayWithMyBank) transaction and issue a refund back to the consumer bank account.
api: openapi/paywithmybank-openapi-original.yml
operations:
  - get-transaction
  - post-transactions-refund
---

# Refund a settled transaction (Trustly / PayWithMyBank)

Base URL: `https://api.trustly.one/api/v1` (production) or
`https://sandbox.trustly.one/api/v1` (sandbox).

## Auth
HTTP Basic: `accessId` / `accessKey`. Include an `Idempotency-Key` header so a
retried refund is not applied twice.

## Steps
1. **`get-transaction`** — `GET /transactions/{transactionId}`. Confirm the
   transaction is in a refundable (captured/completed) state.
2. **`post-transactions-refund`** — `POST /transactions/{transactionId}/refund`.
   Issue a full or partial refund; supply the refund amount in the request body.

## Errors
`BaseException` envelope (`domain`, `code`, `message`, ...). A refund on an
un-captured or already-refunded transaction returns `400`. See
`errors/paywithmybank-problem-types.yml`.

## Events
A `Refresh`/`Update` event notification signals the resulting state change; do not
assume synchronous settlement. See `asyncapi/paywithmybank-webhooks.yml`.
