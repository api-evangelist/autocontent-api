---
name: autocontent-api-recover-from-errors
description: >-
  Choose the one safe next action after an AutoContent Platform failure, using the canonical error
  code, the correlation id, Retry-After and a retained idempotency key — including the case where
  you do not know whether the write landed.
api: AutoContent Platform API v1
base_url: https://api.autocontentapi.com/v1
generated: '2026-09-04'
method: generated
source: >-
  Grounded in https://autocontentapi.com/developers/errors and components.schemas.ErrorBody in
  openapi/autocontent-api-platform-v1-openapi.json.
operations:
  - previewGeneration
  - createGeneration
  - getGeneration
  - getBillingUsage
  - createPrepaymentSession
---

# Recover safely from AutoContent Platform errors

Every failure is one envelope:

```json
{ "error": { "code": "...", "message": "...", "correlation_id": "corr_...", "doc_url": "...", "details": {} } }
```

Branch on `error.code`. It is a closed 36-value enum published in the OpenAPI — never string-match
`message`. Log `correlation_id` on every failure; it is the only trace id this API gives you, and it
exists only on errors.

## The matrix

| HTTP | Codes | Do this |
| --- | --- | --- |
| 400 | `invalid_request`, `invalid_asset_options`, `invalid_model_options`, `unknown_asset_type`, `model_not_supported`, `model_retired` | Fix the request. Do not retry unchanged. Re-read `/asset-types` and `/models`. |
| 401 / 403 | `unauthorized`, `forbidden`, `consent_required` | Refresh the OAuth token, or use a key carrying the operation's `x-required-scopes`. |
| 402 | `payment_required` | Nothing happened — no Generation, no reservation, no provider effect. |
| 404 | `not_found`, `voice_not_found`, `avatar_not_found` | Check the id. Absent and not-yours look identical. |
| 409 | `idempotency_conflict`, `idempotency_in_progress` | See below. |
| 422 | `project_not_ready`, `source_not_ready`, `source_unreachable`, `source_paywalled`, `input_resolution_failed`, `no_qualified_trend`, `no_qualified_knowledge`, `voice_not_ready`, `avatar_not_ready` | Wait for readiness or change the resources. |
| 429 | `rate_limited`, `provider_operation_limit_exceeded` | Respect `Retry-After` or the authoritative reset. |
| 500 / 502 / 503 | `internal_error`, `artifact_verification_failed` | Retry safe reads, or the identical keyed mutation. |

## Money errors

`max_cost_exceeded` — the quote moved above your `max_cost_usd`. Nothing was spent. Re-run
`previewGeneration` and accept the new total **only** on an explicit decision. Never raise the
ceiling automatically.

`payment_required` — add prepaid USD (`createPrepaymentSession`, scope `platform.billing.write`),
then refetch authoritative usage with `getBillingUsage`, re-preview the **unchanged** draft, and
require a fresh explicit acceptance. Do not shrink the requested work to fit, and do not auto-submit
after Checkout.

`content_loop_budget_exceeded` — the Loop hit its per-run or per-month ceiling. Raise it
deliberately or wait for reset.

## The ambiguous write

If a keyed mutation may have reached the service — a timeout, a dropped connection, an unclear
proxy error — **do not mint a new idempotency key**. Retry the exact method, path, body and key, or
inspect the returned resource.

- `idempotency_in_progress` (409): the original is still running. Poll; if `Retry-After` is present,
  wait that long and retry the identical request with the same key.
- `idempotency_conflict` (409): you reused a key with a different body. Either restore the exact
  original body or mint a new key for what is genuinely new work.

Never automatically replay a consumed one-shot upload stream.

## Where idempotency does not protect you

`Idempotency-Key` is required on 18 of the 32 mutating operations. It is **not** declared on
`archiveProject`, `removeProjectLogo`, `updateCollection`, `deleteCollection`, `removeSource`,
`revokeVoice`, `revokeAvatar`, `cancelGeneration`, `recordAssetFeedback`,
`recordContentLoopRunFeedback`, `revokeApiKey` or `deleteWebhook`. On an ambiguous outcome for any
of those, read the resource back before acting again.

## No rate-limit budget exists

There is no `RateLimit-*` or `X-RateLimit-*` header on this API and no published numeric limit for
the Platform surface. You cannot see a limit approaching — you can only react to a 429. Back off on
`Retry-After` when it is present, and use exponential backoff when it is not.
