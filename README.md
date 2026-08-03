# AutoContent API (autocontent-api)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

AutoContent API is an AI-powered content generation platform that enables developers and content teams to programmatically produce podcasts, explainer videos, video shorts, deep research reports, infographics, and quizzes from diverse input sources including URLs, PDFs, YouTube videos, plain text, and social data feeds. Built on NotebookLM-style AI technology, it provides REST API endpoints with a credit-based pricing model and integrations with Make.com, Zapier, and WordPress.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/autocontent-api/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/autocontent-api/refs/heads/main/apis.yml)

## Scope

- **Type:** Index

## Tags

- AI
- Audio
- Content Generation
- Podcasts
- Video
- Generative AI
- Text to Speech
- Automation

## Timestamps

- **Created:** 2025-05-02
- **Modified:** 2026-04-19

## APIs

### Podcast Generation API

Generate AI-powered podcast episodes from URLs, PDFs, YouTube videos, plain text, or social media feeds. Produces MP3 audio with metadata using NotebookLM-style AI with support for standard voices and custom voice cloning. Consumes 10 credits per episode.

- **Human URL:** [https://autocontentapi.com](https://autocontentapi.com)
- **Base URL:** `https://api.autocontentapi.com`

#### Tags

- AI
- Podcast
- Audio
- Content Generation

#### Properties

- [Documentation](https://autocontentapi.com/docs)
- [Authentication](https://autocontentapi.com/docs)
- [Postman Collection](collections/autocontent-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/autocontent-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Video Generation API

Programmatically produce explainer videos and short-form vertical video content (9:16 format) from text, URLs, and other source content. Explainer videos consume 50 credits; video shorts consume 400 credits. Output is delivered as MP4 video files.

- **Human URL:** [https://autocontentapi.com](https://autocontentapi.com)
- **Base URL:** `https://api.autocontentapi.com`

#### Tags

- AI
- Video
- Content Generation

#### Properties

- [Documentation](https://autocontentapi.com/docs)
- [Postman Collection](collections/autocontent-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/autocontent-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Deep Research API

Performs multi-step AI reasoning that browses the live web, reads reputable sources, and synthesizes comprehensive research reports. Supports output as structured JSON, HTML blog posts, and study guides. Consumes 100-200 credits per research session.

- **Human URL:** [https://autocontentapi.com](https://autocontentapi.com)
- **Base URL:** `https://api.autocontentapi.com`

#### Tags

- AI
- Research
- Content Generation

#### Properties

- [Documentation](https://autocontentapi.com/docs)
- [Postman Collection](collections/autocontent-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/autocontent-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Infographics and Quizzes API

Transform source content into visual infographics and interactive quiz formats. Consumes 10-30 credits per asset. Supports diverse input types and produces structured HTML and visual media output.

- **Human URL:** [https://autocontentapi.com](https://autocontentapi.com)
- **Base URL:** `https://api.autocontentapi.com`

#### Tags

- AI
- Infographics
- Quizzes
- Content Generation

#### Properties

- [Documentation](https://autocontentapi.com/docs)
- [Postman Collection](collections/autocontent-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/autocontent-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [Website](https://autocontentapi.com)
- [Documentation](https://autocontentapi.com/docs)
- [Sign Up](https://autocontentapi.com)
- [Pricing](https://autocontentapi.com/pricing)
- [Rate Limits](undefined)
- [Features](undefined)
- [Use Cases](undefined)
- [Integrations](undefined)
- [L L Ms Txt](https://autocontentapi.com/llms.txt)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
