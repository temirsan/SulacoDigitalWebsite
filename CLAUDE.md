# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Portfolio website for **Sulaco Digital** — a mobile app development brand by Temir Atambayev (Senior Android Engineer). Showcases apps: Spectral (wallpaper generator) and Impulse Reader (distraction-free news reader).

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

- `index.html` — Landing page with auto-rotating app screenshot carousel (3s interval, JS-driven)
- `about.html` — Founder bio and skills grid
- `blog.html` — Blog post cards
- `contact.html` — Contact form with social links
- `apps/spectral/` — Spectral app page, privacy policy, terms
- `apps/impulse/` — Impulse Reader app page, privacy policy, terms
- `style.css` — Single shared stylesheet (702 lines) for all pages
- `frames/` — App screenshot PNGs used in carousels

## Design Language

- Dark theme with purple/lavender accents (`#8b5cf6`)
- Radial gradient backgrounds (dark → purple → light)
- Glassmorphism cards (`backdrop-filter: blur`, low-opacity backgrounds)
- Typography: **Playfair Display** (headings) + **Inter** (body)
- Responsive breakpoint: `@media (max-width: 960px)`

All pages share `style.css` via `<link>`. There is no CSS preprocessor or component system — edit `style.css` directly for global changes.

## JavaScript

All JS is inline in `index.html`. The only interactive feature is the image carousel: auto-advance every 3000ms, click-to-navigate dots, uses `translateX` with dynamically measured parent width.