# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Portfolio website for **Sulaco Digital** — a mobile app development brand by Temir Atambayev (Senior Android Engineer). Showcases Spectral (wallpaper generator, in beta) and a teaser for Impulse Reader (placeholder, not yet built).

## Stack

Static site — plain HTML5, CSS3, and vanilla JavaScript. No build tools, no package manager, no framework.

## Running Locally

```bash
python -m http.server 8000
# or
npx http-server .
```

Open `index.html` in a browser. No build step required.

## Architecture

Multi-page static site:

- `index.html` — Landing page with typographic hero, cycling phone-stack visual, stats row, portfolio (Spectral card + Impulse Reader teaser), contact band
- `about.html` — Founder bio, skills grid, experience timeline, education
- `blog.html` — Featured post + grid of post cards (most are placeholders linking to `#`)
- `blog/designing-for-dark-mode.html` — Long-form blog article (the only fully written post)
- `apps/spectral/index.html` — Spectral product page (hero, auto-scrolling gallery, feature spotlight, supporting grid, CTA band)
- `apps/spectral/privacy.html`, `apps/spectral/terms.html` — Spectral legal pages
- `assets/site.css` — Single shared stylesheet for all pages
- `frames/` — App screenshot PNGs

There is no `apps/impulse/`; Impulse Reader is intentionally only a teaser on the landing page until the app exists.

## Design Language

- Dark theme; palette in OKLCH (deep indigo-black ink, lavender accent at hue 295)
- Layered atmospheric halos via fixed `body::before` / `body::after` radial gradients
- Glass panels (`.panel`): `backdrop-filter: blur(24px) saturate(1.1)` + masked gradient border
- Typography: **Inter Tight** (sans, headings + body) + **JetBrains Mono** (eyebrows, labels, meta)
- Accent hue is exposed as `--accent-h` CSS custom property; whole palette derives from it
- Responsive breakpoints: `@media (max-width: 960px)` (layout collapse) and `@media (max-width: 600px)` (mobile tweaks)

All pages share `assets/site.css` via `<link>`. Page-specific CSS is inlined in each HTML's `<style>` block (hero variants, article styles, legal-page styles). No CSS preprocessor or component system — edit `assets/site.css` directly for global changes; copy/paste the nav and footer markup if you add a new page.

## JavaScript

Inline `<script>` blocks per page, kept minimal:

- `index.html` — Hero phone-stack cycle: swaps the front and back `<img>` `src` through 6 screenshots every 3500ms.
- `apps/spectral/index.html` — Auto-scrolling gallery: clones a strip of screenshots until it spans 3× viewport, then pans back and forth at constant speed via `requestAnimationFrame`. Pauses on hover.

No other pages have interactive JS.
