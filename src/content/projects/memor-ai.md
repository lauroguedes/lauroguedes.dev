---
featured: true
category: saas
title: MemorAi
description: >-
  Built to solve a problem I had myself, content I consumed in English never
  became material I actually studied. The build became my deep dive into running
  AI agents in production.
image: '@assets/projects/memor-ai/image.png'
startDate: 2026-07-01
endDate: 2026-08-06
skills:
  - Laravel
  - PHP
  - AI SDK
  - Agents
  - Pipeline
  - Inertia JS
  - Vue JS
demoLink: https://getmemorai.app/
contentSidebar:
  discriminant: false
---
MemorAi is a mobile-first PWA I designed and built end to end: product, architecture, backend, frontend, infrastructure and observability. It is a private SaaS, so this write-up focuses on how it was engineered rather than on what it does commercially.

## Stack

- **Backend:** PHP 8.5, Laravel 13, Horizon for queues, Filament v5 for the internal admin panel, Pest for tests. **Frontend:** Inertia v3 with Vue 3, TypeScript, Tailwind CSS v4 and Wayfinder for typed routes, plus a separate Astro 7 marketing site that consumes the app only through a public HTTP contract.
- **Auth and billing:** Fortify, Sanctum, Socialite (Google OAuth) and Cashier for Stripe.
  **Infrastructure:** Cloudflare R2 for object storage, Redis-backed queues, ffmpeg/ffprobe for media processing, Nightwatch for production monitoring.
- **AI:** the Laravel AI SDK across multiple providers, covering text generation, structured output, tool calling, transcription, text to speech and image generation.

## Architecture highlights

### A pipeline of specialized agents

The heart of the system is a chain of AI agents, each with a single responsibility, an explicit input/output contract and its own failure behaviour. Every stage is a queued job, so each step is independently retryable and every failure is attributable to a specific stage instead of to "the AI".

Decomposing the work this way was the most valuable decision in the project. Small focused prompts consistently outperformed one large instruction blob, and the queue boundaries turned a long-running process into something observable and resumable.

### Prompts as versioned content

Each agent loads its instructions from a Markdown `SKILL.md` file, interpolated with runtime context by a shared trait. Prompts are content, not code: iterating on one is a Markdown diff that reads clearly in review, rather than a change buried inside a PHP class.

### Cross-provider failover

Agents run against a primary provider with a transparent fallback to a second one, selected entirely through configuration. The SDK's built-in failover only retries on rate limit and overload errors, so a malformed schema response or a transient gateway fault would surface straight to the user. A dedicated trait widens that net and retries once against the fallback provider. The trade-off, two full round trips before an unrecoverable error surfaces, is documented in the code rather than left implicit.

Provider portability turned out to be a real design constraint. Providers disagree about JSON schema strictness, and some reject tool calling combined with structured output entirely, so provider capability is pinned per agent and switching the pipeline is a configuration change.

### Guardrails around non-deterministic output

Anywhere a model chooses between existing records, a service owns the candidate set, feeds it to the model's search tool and then re-validates the answer server-side. A record the model never saw can never be selected, so a hallucinated response is ignored and logged instead of corrupting user data.

I also removed an earlier heuristic fallback that produced plausible but wrong results. A fallback that is usually wrong is worse than no fallback at all.

### Honest state machines

An early bug taught me to be strict about terminal states. A run marked successful while producing nothing also silenced the failure handler, so the UI showed success while a failure notification went out. Statuses now carry exactly one meaning each, and every unhappy termination stamps the stage that actually failed, instead of relying on a heuristic that inferred it from partial metadata.

### Instrumentation

Every model invocation is logged with provider, model, token counts, duration and cost, all in integer cents. A request scoped context object propagates the owning entity IDs into queued jobs, so usage is correctly attributed even deep inside asynchronous work. The admin panel turns that into per stage and per user breakdowns.

### Validation at the boundary

Uploads pass through a single gate that runs before any storage write or database row is created, checking type, size, quota and media duration in a fixed order. Rejections are typed exceptions, so three ingestion channels render consistent, contextual errors from one source of truth. Media that exceeds provider request limits is split with ffmpeg and processed in order.

### Security posture

Ownership enforced through policies with no IDOR surface; sensitive columns unreachable through user facing form requests, with tests asserting it; OAuth restricted to an allowlist with encrypted tokens, throttled routes and a linking flow that refuses identities owned by another account; signature verified webhooks with idempotent side effects; strict security headers.

### Localization discipline

Every user facing string, including exception messages, mail and bot replies, goes through the translation layer, with a second locale kept complete as a rule rather than as a cleanup task.

## What I took away

- Decompose AI work the way you decompose code. Narrow contracts are debuggable and retryable; one giant prompt is neither.
- Treat prompts as versioned content so iteration becomes a normal review workflow.
- Guardrail the model instead of trusting it. Bounding the blast radius matters more than the prompt.
- Design status values that cannot lie, because downstream handlers read them.
- Instrument cost per invocation early, so unit economics are arithmetic and not a guess.
- Validate once, at the boundary, with typed errors that carry the remediation path.

The application ships with a Pest suite of 137 test files covering the pipeline, billing, storage limits and dedicated security cases.
