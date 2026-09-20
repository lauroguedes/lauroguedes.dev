---
featured: false
category: templates-and-starter-kits
title: Landpack Astro Template
description: >-
  An open-source Astro landing page template for technical projects, with typed
  content, light and dark themes, developer documentation, and AI-assisted customization.
image: '@assets/projects/landpack-astro-template/image.png'
startDate: 2026-09-19
endDate: 2026-09-19
skills:
  - Astro
  - TypeScript
  - CSS
  - Lumos for Astro
demoLink: https://landpack.lauroguedes.dev/
sourceLink: https://github.com/lauroguedes/landpack
contentSidebar:
  discriminant: false
---
Landpack is the landing page template I wanted for open-source projects that have outgrown a README but do not need a CMS or another application to maintain. It is built with Astro 7, Lumos for Astro, and plain CSS, and it produces a static site that can be deployed to ordinary static hosting.

The live demo uses Laravel SSO as sample content. The identity-provider copy, commands, screenshots, and integration examples show what a complete customization can look like, but Landpack does not install Laravel or require PHP.

## Content that stays outside components

Most project-specific content lives in YAML, JSON, and Markdown. The main project file controls identity, hero content, links, screenshots, calls to action, and SEO metadata. Separate collections hold installation steps, integration examples, features, and frequently asked questions.

Astro Content Collections validate that data before the build completes. A missing image, invalid option, or incomplete entry becomes a development error instead of a partially rendered production section.

This separation also makes the template easier to replace. Rebranding the example project does not require hunting through presentation components for product names and links.

## Built for technical products

Landpack includes the parts that developer-focused project pages usually need:

- installation commands with copy controls
- code tabs for several languages or client libraries
- shareable URLs that reveal a selected integration tab
- Markdown answers for longer FAQ content
- paired screenshots for light and dark themes
- a responsive mobile navigation drawer
- local fonts and optimized local images

The header leaves the viewport while the reader scrolls down and returns when they scroll up. Interactive diagrams support pointer, touch, and keyboard input, while reduced-motion preferences disable unnecessary animation.

## Theme and presentation system

Light and dark modes have their own design tokens, code highlighting, and screenshot assets. The theme choice persists between visits, and project screenshots can define separate crops when the two source images need different framing.

Hero backgrounds, screenshot frames, the project stamp, creator credit, and the faded footer wordmark are all configurable. The supplied defaults create a complete design without locking a new project to the visual identity of the example.

## Customization with an AI coding agent

The repository includes a portable customization skill and a complete project map. An AI coding agent with local file access can read that guide, inspect a project link or written brief, and update the template's content, branding, assets, and SEO without changing its overall structure.

This is a repository workflow, not a hosted generation service. The agent edits the same source files a developer would edit, and the result remains reviewable in Git.

## Build, SEO, and deployment

The default build is static and does not require a database, CMS, API token, React, or Vue. Landpack includes a sitemap, `robots.txt`, canonical and social metadata, a regenerable social card, and a built-output SEO check.

The project documents deployment to Vercel and Cloudflare, but the generated `dist/` folder works with any static host. A production URL is supplied through `PUBLIC_SITE_URL` so canonical links, the sitemap, and social metadata point to the correct domain.

For this portfolio entry I verified the lockfile installation, Astro diagnostics, production build, and SEO structure. All four checks completed successfully.

## Why it stays simple

Landpack is intentionally a website template rather than a platform. There is no dashboard, remote content service, or deployment account to maintain. The content is versioned with the project, the output is static, and every part of the page remains available to change.
