---
name: autocontent-api-run-content-loop
description: >-
  Turn a proven one-off AutoContent Generation into a scheduled Content Loop with hard per-run and
  per-month USD ceilings, then monitor and stop it.
api: AutoContent Platform API v1
base_url: https://api.autocontentapi.com/v1
generated: '2026-09-04'
method: generated
source: >-
  Grounded in openapi/autocontent-api-platform-v1-openapi.json and
  https://autocontentapi.com/developers/concepts/content-loops.
operations:
  - createContentLoop
  - listContentLoops
  - getContentLoop
  - updateContentLoop
  - runContentLoop
  - listContentLoopRuns
  - getContentLoopRun
  - recordContentLoopRunFeedback
  - archiveContentLoop
---

# Schedule recurring content with a Content Loop

A Content Loop is a **standing authorization to spend**. Create one only after the equivalent
one-off Generation has produced output you are happy with — the provider says so explicitly.

## 1. Create the Loop

`createContentLoop` — `POST /content-loops` — requires **both** `platform.write` and
`platform.generate`, and an `Idempotency-Key`. That double scope requirement is the point: a Loop
can spend money without you in the room.

Always set both ceilings:

- `max_cost_per_run_usd`
- `max_cost_per_month_usd`

Breaching either returns `content_loop_budget_exceeded`. Hitting the account Loop cap returns
`content_loop_limit_exceeded`.

The `input` is the same shape as a Generation draft — `{ "type": "knowledge" }` or
`{ "type": "trend", "lookback_days": 7 }` — plus a `schedule` with `frequency`, `day_of_week`,
`local_time` and an IANA `timezone`.

## 2. Watch it

- `listContentLoops` / `getContentLoop` — configuration and current status (`revision` increments
  on change).
- `listContentLoopRuns` (`GET /content-loops/{id}/runs`) and `getContentLoopRun` — every Run
  carries `trigger`, `status`, `billing_period_id` and the full reserved/settled/released money
  triple.
- Each Run produces an ordinary Generation; `ContentLoopRun.generation_id` links to it, and that
  Generation carries `content_loop_run_id` back.

A trend input that finds nothing worth using fails the Run with `no_qualified_trend`; a knowledge
input with nothing new fails with `no_qualified_knowledge`. Neither is a bug.

## 3. Fire one now

`runContentLoop` — `POST /content-loops/{id}/run` — scope `platform.generate`, `Idempotency-Key`
required. Use for a manual catch-up run; it still bills against the same ceilings.

## 4. Feed back and stop

`recordContentLoopRunFeedback` — `POST /content-loop-runs/{id}/feedback` — topic feedback for a Run.

`archiveContentLoop` — `DELETE /content-loops/{id}` — requires both scopes and an `Idempotency-Key`.
It stops future Runs. Runs already accepted are **not** described as reversible, and no unarchive
operation is published. Archiving the parent Project (`archiveProject`) archives its Loops too — the
widest-blast write on this API.
