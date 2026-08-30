---
featured: false
category: templates-and-starter-kits
title: 'AEON / SPACE: Cinematic Space Agency Template'
description: >-
  A cinematic, content-driven space agency website template built with Astro 6,
  React 19, Tailwind CSS 4, and Framer Motion. It combines an immersive landing
  page with typed Markdown archives for missions, reports, news, and launches.
image: '@assets/projects/aeon-space-cinematic-space-agency-template/image.png'
startDate: 2026-05-02
endDate: 2026-05-03
skills:
  - astro
  - react
  - Tailwind CSS
  - Framer Motion
  - Typescript
demoLink: https://portal.astro.build/themes/aeon-space/
sourceLink: https://github.com/lauroguedes/aeon-space-agency
contentSidebar:
  discriminant: false
---
AEON / SPACE is a fictional space agency website template built around a simple idea: a space brand should feel larger than a landing page. It needs missions, discoveries, launch schedules, technical reports, and enough visual structure to make that content feel connected.

I built the project as an editorial website rather than a collection of disconnected marketing sections. The home page introduces the agency through a cinematic hero, active missions, a featured discovery, animated statistics, and the next scheduled departure. Each of those sections is backed by structured content that also powers its own archive and detail pages.

The result is useful as a starting point for fictional agencies, science publications, aerospace projects, game worlds, mission archives, and narrative product sites. The content is fictional, but the architecture is designed like a real publishing system.

## Design direction

The visual direction combines deep navy surfaces, warm cream sections, restrained blue and gold accents, and typography inspired by aerospace interfaces. The home hero uses a full-bleed sky image, animated stars, orbital rings, angled desktop navigation, and a centered agency symbol.

Below the hero, the visual language becomes more editorial. Mission cards use status labels and strong imagery. Reports pair long-form content with data-inspired graphics. Departure rows resemble a launch manifest, while the countdown gives the next launch a live operational state.

The site supports light and dark themes without treating light mode as an inverted afterthought. Theme-specific variables control backgrounds, typography, panels, overlays, and image treatment. The brand also has separate wordmark and symbol assets for light and dark contexts.

## Tech stack

AEON / SPACE uses [Astro 6](https://astro.build/) as the application framework and [TypeScript](https://www.typescriptlang.org/) throughout the codebase. Most pages render as static HTML, while [React 19](https://react.dev/) powers the parts that need client-side state or continuous updates.

[Tailwind CSS 4](https://tailwindcss.com/) handles styling through the Vite integration. Project-specific colors, typography, theme variables, and motion behavior live in `src/styles/global.css`, which keeps the design system close to the generated utility classes.

[Framer Motion 12](https://motion.dev/) drives hero entrances, section reveals, navigation transitions, hover states, and animated statistics. Lucide React provides interface icons, and Sharp handles image processing during builds.

{% table %}
- Area
- Technology
---
- Framework
- Astro 6
---
- Interactive islands
- React 19
---
- Styling
- Tailwind CSS 4
---
- Motion
- Framer Motion 12
---
- Content
- Astro Content Collections
---
- Language
- TypeScript
---
- Image processing
- Sharp
---
- Deployment
- Static, Vercel, or Cloudflare Workers
{% /table %}

## Content architecture

The main content lives in Markdown rather than component files. Astro Content Collections validate every entry against a Zod schema before the build succeeds.

The project defines five collections:

- `missions` stores status, vehicle, destination, mission window, icon, order, and cover image.
- `reports` stores publication metadata, imagery, spectral values, highlighted findings, and chart ranges.
- `departures` stores launch dates, UTC times, launch sites, mission windows, and display order.
- `news` stores article metadata, author information, publication dates, and images.
- `pages` stores the standalone About, Science, and Technology sections.

Collection index pages load and sort these entries through shared helpers in `src/lib/content.ts`. Astro then generates a detail route for each Markdown file. Adding a mission or report does not require editing route code or copying a page component.

This structure also keeps the home page synchronized with the archives. The featured discovery comes from the reports collection. Active mission cards come from mission entries. The launch schedule and countdown use the same departure data as the departures archive.

## Static-first with focused React islands

I used React only where browser state improves the experience.

`HeroShell` manages the responsive mobile menu and coordinates the animated hero. `ThemeToggle` persists the selected color mode. `StatsSection` animates values when the section enters the viewport. `LaunchCountdown` reads the departure date and time from frontmatter, calculates the UTC target, and updates once per second.

Everything around those islands remains Astro-rendered HTML. Archive pages, detail pages, navigation content, and long-form Markdown do not need a client-side application runtime.

This split keeps the site visually active without turning the whole project into a React single-page application. It also makes the content readable and routable before JavaScript hydrates.

## Home page composition

The home page is assembled from reusable sections in `src/pages/index.astro`:

- `HeroShell` renders the full-screen introduction, star field, navigation, agency branding, and calls to action.
- `MissionsSection` presents the current missions as image-led status cards.
- `DiscoverySection` pulls in the latest report and renders its spectral analysis data.
- `StatsSection` animates agency metrics when it becomes visible.
- `DepartureSection` shows the next launch, live countdown, and upcoming departure rows.
- `FooterSection` closes the page with the agency directory, coordinates, policies, and full wordmark.

The components consume mapped content instead of reaching into Markdown collections independently. This keeps data loading and sorting in one layer, while components receive small typed objects shaped for presentation.

## Theme and branding system

Brand assets live in `public/brand/` as wordmark and symbol variants for light and dark surfaces. `BrandLogo.tsx` places both variants in the same layout slot and switches their opacity based on the active theme or an explicit tone.

That explicit tone is important in the hero. The hero image has its own contrast requirements, so its logo should not change just because the rest of the page changes theme. Other areas can use the automatic theme-aware mode.

The broader design tokens are defined as Tailwind theme variables and CSS custom properties. Rebranding the template mainly involves four places:

1. Replace the assets in `public/brand/`.
1. Change colors and typography in `src/styles/global.css`.
1. Update navigation, hero copy, statistics, and footer metadata in `src/data/site.ts`.
1. Replace the Markdown and images in `src/content/` and `public/images/`.

The project does not bury agency-specific content across dozens of components. That matters for a template, because customization should not require reverse-engineering the original implementation.

## Responsive and motion behavior

The desktop hero uses angled navigation around the central agency symbol. On smaller screens, that navigation becomes a compact menu with animated open and close states. Content grids collapse into readable single-column layouts, and typography scales through responsive clamps rather than fixed desktop sizes.

Motion respects the user's reduced-motion preference. Framer Motion runs inside `MotionConfig` with `reducedMotion="user"`, and CSS disables the star animation when `prefers-reduced-motion` is active.

The sticky header used on subpages reacts to scroll direction. It leaves the viewport while the reader moves down and returns when they scroll up. This preserves screen space without making navigation difficult to recover.

## Deployment strategy

The default `npm run build` produces a platform-neutral static site in `dist/`. That is the simplest deployment path and works with ordinary static hosting.

Vercel and Cloudflare use explicit build targets:

```bash
npm run build:vercel
npm run build:cloudflare
```

`astro.config.mjs` reads `ASTRO_DEPLOY_TARGET` and loads an adapter only for the requested platform. The regular build does not become coupled to Vercel or Cloudflare configuration.

For Cloudflare, the project uses the official Workers adapter and includes `wrangler.jsonc`, local preview, and deployment scripts. `prerenderEnvironment: 'node'` keeps static prerendering compatible with Node-oriented dependencies while the deployed output targets the Worker runtime.

A local setup takes the standard Astro flow:

```bash
git clone https://github.com/lauroguedes/aeon-space-agency.git
cd aeon-space-agency
npm install
npm run dev
```

The development server runs at `http://localhost:4321`.

## Best practices worth noting

Typed content schemas are the strongest part of the architecture. A departure cannot silently omit its launch time, and a report cannot provide the wrong number of spectral values. Invalid Markdown fails during development or the production build instead of producing a partially broken page.

The adapter selection is deliberately narrow. Platform-specific deployment support is available, but it does not affect the default static build. This keeps the template portable and makes each deployment target explicit.

The React boundary is also intentionally small. Interactive components have a clear reason to hydrate, while the editorial parts remain Astro components and Markdown. That makes the code easier to follow and avoids sending a large application runtime for static content.

Theme-aware branding is handled as a reusable component rather than repeated conditional markup. Wordmarks and symbols follow the same API, and fixed-tone contexts can opt out of automatic switching.

## Challenges

The hardest visual problem was balancing a cinematic home page with content that still feels structured. Large imagery and motion can quickly overpower navigation and text. The final design uses restrained animation, strong section boundaries, and a smaller editorial rhythm after the opening hero.

The content model also needed to serve two contexts at once. A mission entry has enough data for its detail page, but the home page only needs a title, summary, status, icon, and URL. Mapping collection entries into presentation-specific objects prevents components from becoming coupled to every frontmatter field.

Dates required consistent UTC handling. Departure frontmatter stores a date and time separately, while sorting and countdown behavior need one timestamp. Shared formatting helpers normalize those values so the archive order and live countdown use the same interpretation.

Supporting static hosting, Vercel, and Cloudflare without maintaining separate configurations was another constraint. Conditional adapter loading solved that while keeping the normal build platform-neutral.

## Conclusion

AEON / SPACE is both a design experiment and a reusable Astro template. The visual layer creates the feeling of a fictional agency, while the content model makes missions, reports, news, and departures practical to maintain.

The project is a useful example of Astro's main strength: static content by default, with focused interactive islands where they add something concrete.
