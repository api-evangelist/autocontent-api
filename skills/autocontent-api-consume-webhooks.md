---
name: autocontent-api-consume-webhooks
description: >-
  Register an AutoContent Platform webhook destination and verify every delivery correctly —
  signature over the raw bytes, event-id match, timestamp window, dedupe — before parsing anything.
api: AutoContent Platform API v1
base_url: https://api.autocontentapi.com/v1
generated: '2026-09-04'
method: generated
source: >-
  Grounded in openapi/autocontent-api-platform-v1-openapi.json and
  https://autocontentapi.com/developers/webhooks.
operations:
  - createWebhook
  - listWebhooks
  - deleteWebhook
  - getGeneration
  - getProject
  - getSource
---

# Receive and verify AutoContent webhooks

## 1. Register a destination

`createWebhook` — `POST /webhooks` — scope `platform.write`, `Idempotency-Key` required.
`listWebhooks` (`GET /webhooks`) also requires the **write** scope; there is no read-only view.
`deleteWebhook` (`DELETE /webhooks/{id}`) revokes one. A destination cap exists —
`webhook_limit_exceeded` — but the number is not published.

## 2. Verify before you parse

This order matters. Do all four, on the **raw request bytes**, before `JSON.parse`.

1. Compute `HMAC-SHA256` over the exact string `<unix timestamp>.<raw body>` using your webhook
   secret, and compare against `x-autocontent-signature`.
2. Require the `x-autocontent-event-id` header to equal `body.id`.
3. Reject any timestamp outside a ±300 second window.
4. Deduplicate by event id.

The TypeScript SDK does all of this in `AutoContent.webhooks.constructEvent({ rawBody, signature,
eventId, secret })`.

## 3. Handle it

Return `2xx` quickly, once receipt is durable. Process idempotently, outside the request path.
Treat the event as a **signal**, not a payload: when you need the complete object, fetch the current
account-owned resource with `getGeneration`, `getProject` or `getSource`.

## 4. What you will receive

Enumerated by the provider:

- `project.needs_review`, `project.ready`
- `source.ready`, `source.failed`

Documented only as outcome categories, with no published type strings:

- Generation: succeeded, partially succeeded, failed, cancelled
- Content Loop: configuration warnings, pauses, and "six terminal Run outcomes"

Because those strings are not published, **do not build an exhaustive switch** on Generation or
Content Loop event types. Branch on the outcomes you know and fall through to a fetch-the-resource
default.

The legacy Content API's webhook routes (`/content/Webhook`, `/content/Webhooks`,
`/content/RemoveWebhook`) have **no documented signing scheme**. Nothing in this skill applies to
them.
