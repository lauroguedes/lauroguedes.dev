---
featured: true
title: Wallai
description: >-
  Generate unique, AI-powered wallpapers for mobile and desktop using 🍌Nano
  Banana 2 and the Laravel AI SDK. Choose from 18 curated styles, customize your
  prompt, and download high-resolution images ready to use.
image: '@assets/projects/wallai/image.png'
startDate: 2026-03-02
endDate: 2026-03-08
skills:
  - laravel
  - livewire
  - alpine js
  - mary ui
  - laravel ai sdk
sourceLink: https://github.com/lauroguedes/wallai
---
# Wallai: AI-Powered Wallpaper Generator

WallAI is a web application that generates high-resolution wallpapers for mobile and desktop using AI image generation. Users pick from 21 curated visual styles (ranging from Cyberpunk Cityscape to Botanical Watercolor), write a short description or let the app auto-generate a prompt, then receive a wallpaper tailored to their device's aspect ratio. The result can be downloaded directly or viewed in a phone or monitor mockup before saving.

## Tech Stack

The project is built on [**Laravel 12**](https://laravel.com/) with [**Livewire 4**](https://livewire.laravel.com/) handling all frontend reactivity without a single line of custom JavaScript. UI components come from [**MaryUI**](https://mary-ui.com/) and [**DaisyUI**](https://daisyui.com/), styled with [**Tailwind CSS v4**](https://tailwindcss.com/). Image generation and prompt engineering run through the [**Laravel AI SDK**](https://github.com/laravel/ai) (`laravel/ai`), with [Google Gemini](https://ai.google.dev/) as the primary provider. Background job processing is managed by [**Laravel Horizon**](https://laravel.com/docs/horizon), backed by [Redis](https://redis.io/) queues. Tests are written with [**Pest 4**](https://pestphp.com/).

## Architecture

The core generation pipeline is split into three layers. First, the `PromptGenerator` agent (a simple Laravel AI agent) takes the user's raw description and converts it into a more creative, well-formed image prompt. Second, the `ImagePromptAgent` receives that prompt along with the selected style and device type, then produces a rich structured JSON output using Laravel AI's `HasStructuredOutput` contract. This JSON defines everything from subject arrangement and scene environment to camera lens, aperture, ISO, lighting direction, and even a list of negative prompts. Third, `WallpaperService` flattens that structured object into a natural-language prompt string, passes it to `Image::of()` from the AI SDK, applies portrait or landscape orientation, and stores the result in public storage.

The entire generation cycle is dispatched as a queued `GenerateWallpaper` job. Two dedicated Horizon queues handle mobile and desktop workloads independently, allowing concurrent generation for both. Job state is tracked in Redis Cache, keyed by session and job ID, so the Livewire component can poll for results without blocking the UI.

```
sequenceDiagram
    actor User
    participant LC as Livewire Component
    participant WS as WallpaperService
    participant Q as Horizon Queue
    participant PG as PromptGenerator Agent
    participant IPA as ImagePromptAgent
    participant AI as Laravel AI SDK
    participant Gemini as Gemini API
    participant Store as Public Storage
    participant Cache as Redis Cache

    User->>LC: Select style + enter description
    LC->>WS: dispatchGeneration(sessionId, prompt, style, deviceType)
    WS->>Cache: increment pending_jobs:{sessionId}
    WS->>Q: GenerateWallpaper::dispatch() → wallpapers-{mobile|desktop}
    WS-->>LC: jobId

    loop Poll for result
        LC->>Cache: getJobResult(jobId)
        Cache-->>LC: status: pending
    end

    Q->>WS: handle(WallpaperService)
    WS->>PG: prompt(message with style + device context)
    PG->>Gemini: generate creative prompt (fallback: OpenAI)
    Gemini-->>PG: refined prompt text
    PG-->>WS: prompt string

    WS->>IPA: prompt(refined prompt, style, deviceType)
    IPA->>Gemini: structured output request (HasStructuredOutput schema)
    Gemini-->>IPA: JSON (subject, scene, lighting, camera, negative_prompts)
    IPA-->>WS: structured response

    WS->>WS: flattenStructuredPrompt(structured)
    WS->>AI: Image::of(engineeredPrompt).portrait|landscape().generate()
    AI->>Gemini: image generation request
    Gemini-->>AI: raw image bytes
    AI-->>WS: ImageResponse

    WS->>Store: Storage::disk(public).put(path, image)
    Store-->>WS: stored path + url

    WS->>Cache: put wallpaper_job:{jobId} → completed
    WS->>Cache: append to wallpapers:{sessionId}:{deviceType}
    WS->>Cache: decrement pending_jobs:{sessionId}

    LC->>Cache: getJobResult(jobId)
    Cache-->>LC: status: completed + wallpaper data
    LC-->>User: gallery updated with new wallpaper

```

## Best Practices Worth Noting

**Structured output as prompt engineering** is one of the more interesting decisions here. Rather than sending a free-text prompt directly to the image model, `ImagePromptAgent` forces the LLM to first reason about the image in a rich schema covering subject, scene, lighting, camera, and quality settings. This schema is then serialized into a detailed natural-language prompt. The benefit is reproducibility and consistency: each style produces prompts with a predictable shape, and the negative prompt list is always enforced.

**Provider fallback** is declared at the class level via `#[Provider(['gemini', 'openai'])]` on the `PromptGenerator` agent, meaning OpenAI is used automatically if Gemini is unavailable. It is a clean way to handle provider reliability without infrastructure-level changes.

**Session-scoped storage** avoids any authentication requirement. Wallpapers are stored per browser session with a one-day TTL in Cache, and files sit under `wallpapers/{sessionId}/` on the public disk. This keeps the app stateless from a user perspective while still giving each visitor a private gallery.

**Custom Livewire exception hook** (`HandleExceptions`) intercepts any exception thrown inside a Livewire component, reports it to the application logger, and displays a toast notification instead of exposing Livewire's default error modal. It detects whether the component uses MaryUI's `Toast` trait before acting, making it opt-in by design.

## Challenges

The main technical challenge was coordinating the two-step AI pipeline within a queue context. The `ImagePromptAgent` uses structured output with a detailed JSON schema: getting the model to consistently honour all schema constraints (especially `aspect_ratio` and negative prompts) required careful instruction engineering and explicit schema descriptions on every field.

Concurrency was another consideration. Multiple jobs writing to the same session's wallpaper list in Cache could cause race conditions, which is handled with a Cache lock (`Cache::lock(...)->block(...)`) inside the job's completion handler. It is a lightweight but reliable approach given the Redis backend.

## Conclusion

WallAI is a focused, well-structured example of integrating a modern AI generation pipeline into a Laravel application. The two-agent architecture (one for creative prompt expansion, one for structured prompt engineering) produces noticeably better image results than a naive single-prompt approach. Combined with Horizon-managed queues, session-based storage, and reactive Livewire components, it shows how much can be built with the Laravel ecosystem without reaching for a separate frontend framework.
