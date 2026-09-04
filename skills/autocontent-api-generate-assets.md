---
name: autocontent-api-generate-assets
description: >-
  Create an AutoContent Platform Project from a website, ground it with Sources, get an
  authoritative USD quote, and accept the work under a hard cost ceiling — the end-to-end path from
  nothing to finished content Assets.
api: AutoContent Platform API v1
base_url: https://api.autocontentapi.com/v1
generated: '2026-09-04'
method: generated
source: >-
  Grounded in openapi/autocontent-api-platform-v1-openapi.json (verbatim, fetched from
  https://autocontentapi.com/openapi/autocontent-platform-v1.json) and
  https://autocontentapi.com/developers.
operations:
  - createProject
  - getProject
  - updateProject
  - createSource
  - getSource
  - listAssetTypes
  - listModels
  - previewGeneration
  - createGeneration
  - getGeneration
  - listAssets
  - getAsset
---

# Generate finished Assets with the AutoContent Platform API

Authenticate every call with `Authorization: Bearer <acp_ key or OAuth access token>`.
Requests and responses are `snake_case`. USD amounts are **two-decimal strings**, never floats.

## 1. Create the Project and wait for it to be ready

`createProject` — `POST /projects` — scope `platform.write`, **`Idempotency-Key` required**.
Send one public `website_url`. The response is `202`; the Project starts analysing.

Poll `getProject` (`GET /projects/{id}`) using the `poll_after_seconds` the resource returns.

- `status: needs_review` — the inferred brand profile needs confirming. Call `updateProject`
  (`PATCH /projects/{id}`, `Idempotency-Key` required) with `confirmed: true` and any corrections
  to `name` / `description`.
- `status: ready` — go on.

Do not generate against a Project that is not ready. You will get `project_not_ready` (422).

## 2. Add Sources if the website alone is not enough grounding

`createSource` — `POST /projects/{id}/sources` — `Idempotency-Key` required. Accepts a URL, text,
or a multipart file. Poll `getSource` until `status: ready`; `source_not_ready` (422) is what you
get for jumping ahead.

For a one-off file, set `keep_as_project_asset: false`. That Source stays out of Collections and
**expires after 24 hours** unless a Generation claims it.

## 3. Discover what you can actually ask for

`listAssetTypes` — `GET /asset-types` — returns live availability, the stable option schema, the
capabilities and the catalog version for each asset type. `listModels` — `GET /models` — returns
pinnable models and their native option schemas.

Read these. Do not hardcode asset types, models or prices; a retired pin fails with
`model_retired`, an unknown type with `unknown_asset_type`.

## 4. Preview — this is the only authoritative price

`previewGeneration` — `POST /generations/preview` — scope `platform.generate`. **Non-consuming.**
It creates no Generation, no reservation and no provider effect.

```json
{
  "project_id": "prj_...",
  "input": { "type": "knowledge" },
  "attachment_source_ids": ["src_..."],
  "assets": [{ "asset_type": "lead_magnet" }, { "asset_type": "podcast_episode" }]
}
```

`attachment_source_ids` is a **top-level** field, separate from `input.source_ids`. Up to 20 unique
ready Sources owned by the same Project.

Never calculate or cache a price yourself. Preview immediately before create.

## 5. Accept under an explicit ceiling

`createGeneration` — `POST /generations` — scope `platform.generate`, **`Idempotency-Key`
required**. Send the identical draft plus `max_cost_usd` set to the preview's `total_cost_usd`.

- Quote above your ceiling → `max_cost_exceeded` (400). Nothing is spent. Re-preview; raise the
  ceiling only on an explicit decision, never automatically.
- Not enough prepaid balance → `payment_required` (402). No Generation, no reservation, no provider
  effect. Add funds, refetch usage, re-preview the unchanged draft, then ask again.

Acceptance freezes the Project, Source, brand, Voice, Avatar, model, option and catalog identities,
so later edits cannot rewrite this work.

## 6. Wait, then collect

Poll `getGeneration` (`GET /generations/{id}`) or consume the signed webhook — see
`autocontent-api-consume-webhooks`. Terminal outcomes are succeeded, partially succeeded, failed
and cancelled.

Then read the Assets: `listAssets` / `getAsset`. Reconcile spend from `settled_cost_usd` and
`released_cost_usd` on the Generation and each Asset — `reserved_cost_usd` is not what you were
charged.

## Taking it back

`cancelGeneration` (`POST /generations/{id}/cancel`) cancels **undispatched** work only. Once a
Generation is dispatched to a provider there is no undo, and no time window is published. Preview is
the rehearsal; cancel is not a safety net.
