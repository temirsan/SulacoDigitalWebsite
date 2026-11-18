# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Portfolio website for **Sulaco Digital** — a mobile app development brand by Temir Atambayev (Senior Android Engineer). Showcases Spectral (wallpaper generator, released on Google Play) and a teaser for Impulse Reader (placeholder, not yet built).

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

- `index.html` — Home page: hero with two phones floating directly on the backdrop, stats row, then a 12-column grid of panels (`#portfolio`): Spectral card, Impulse Reader teaser (`#impulse`), experience, skills, education, featured post, drafts, contact
- `about.html` — Founder bio, skills grid, experience timeline, education
- `blog.html` — Featured post + an "In drafts" list of unwritten posts (unlinked, undated by design; never give them dates or `#` links)
- `blog/designing-for-dark-mode.html` — Long-form blog article (the only fully written post)
- `apps/spectral/index.html` — Spectral product page (hero, auto-scrolling gallery, overlay demo, pattern demo, a three-card supporting grid, CTA band). The two demos are the feature section: they share one heading scale, and the supporting grid lists only what the demos and gallery don't already show (don't re-add a static overlay spotlight, or cards for the generator, live preview or Pro). The overlay demo is one phone that auto-advances through the overlays with a diagonal mask wipe (Web Animations on a registered `--wipe` property), plus a swatch picker and Pause button; it stops offscreen, and under reduced motion there is no autoplay and picks crossfade. It uses `frames/overlay_standard_*.webp` and `frames/overlay_glass_*.webp` (600px wide) (the original PNG exports are not kept in the repo). Below it, the pattern demo shows five generator patterns on a foldable that opens like a book (CSS 3D: a cover rotating on its hinge, `.is-open` on the stage, one stacked `.fold` per pattern), using `frames/pattern_closed_*.webp` and `frames/pattern_open_*.webp`; same autoplay, Pause and reduced-motion rules as the overlay demo
- `apps/spectral/privacy.html`, `apps/spectral/terms.html` — Spectral legal pages
- `404.html` — Not-found page (root-absolute links so it works at any depth; the host serves it for unknown URLs)
- `robots.txt`, `sitemap.xml` — Search basics for `https://sulacodigital.com`; add new pages to the sitemap
- `assets/og.jpg` — 1200×630 link-preview image used by every page's Open Graph tags (absolute URLs, since scrapers can't resolve relative ones)
- `PRODUCT.md` — Product truth: audiences, positioning, confirmed facts (Spectral pricing, profile URLs) and things that must not be fabricated
- `assets/site.css` — Single shared stylesheet for all pages
- `frames/` — App screenshot images. Actively-referenced screenshots are WebP (resized to their actual max on-page display width, then compressed ~90% smaller than the original PNG exports — see `git log` around the "loading delay" fix for context).

There is no `apps/impulse/`; Impulse Reader is intentionally only a teaser on the landing page until the app exists.

## Design Language

- Dark theme; palette in OKLCH (deep indigo-black ink, lavender accent at hue 295)
- Backdrop: a fixed `.backdrop` layer (first child of `<body>` on every page) with a purple-blue glow and a dot matrix that brightens in slow drifting waves. Pure CSS in `site.css`: the dot grid is a static mask, and two colour bands behind it move on `transform` only. Keep those band layers only as large as their drift needs (smaller again under 600px): oversized layers exceed phone GPU texture limits and the dots disappear on mobile. The user asked for this look (after the Gemini Android app), so keep it; it is the one deliberate ambient animation. No per-component glow blobs
- Panels (`.panel`): tinted surface + masked gradient hairline; no backdrop blur (nothing sits behind them)
- Typography: **Bricolage Grotesque** (`--font-display`) for headings, stat numerals and the logo name; **Inter** (`--font-sans`) for body, nav, buttons and labels; **JetBrains Mono** only for data and code (dates, spec rows, `<code>`)
- Logo: inline SVG `.nav-logo-mark`, two D shapes (one flipped) that read as an S, in `--accent-hi` / `--accent-lo`; the favicon is the same mark on a dark tile, served as files (`assets/favicon.svg`, `assets/apple-touch-icon.png`) and linked with a `?v=` query; bump the number when the icon changes so browsers drop their cached copy. The name is set as "Sulaco" bold + "Digital" light
- Neutrals (ink, surface, border, dim/mute text) derive from `--accent-h` at low chroma, so changing the hue re-tints the whole site
- Deliberately avoided (removed in the de-slop pass): eyebrow/kicker labels above headings, pulsing status dots, gradient-clipped or outlined text, coloured glow shadows, the bold-line + italic-dim-line headline on every page (kept once, on the home h1 only), duplicated stat rows
- Accent hue is exposed as `--accent-h` CSS custom property; whole palette derives from it
- Responsive breakpoints: `@media (max-width: 960px)` (layout collapse) and `@media (max-width: 600px)` (mobile tweaks)

All pages share `assets/site.css` via `<link>` and the same nav and site footer (the Spectral, legal and article pages included); content sits in a `<main>` landmark. Page-specific CSS is inlined in each HTML's `<style>` block (hero variants, article styles, legal-page styles). No CSS preprocessor or component system — edit `assets/site.css` directly for global changes; copy/paste the `.backdrop` div, nav and footer markup if you add a new page.

## JavaScript

Inline `<script>` blocks per page, kept minimal:

- `apps/spectral/index.html` — Auto-scrolling gallery: clones a strip of screenshots until it spans 3× viewport, then pans back and forth at constant speed via `requestAnimationFrame`. Pauses on hover or via a Pause button (for touch/keyboard); skipped entirely under `prefers-reduced-motion: reduce`.
- Every page — a one-line `<head>` script adds `.js` to `<html>` only when `IntersectionObserver` exists; the reveal CSS depends on it, so browsers without the observer simply show everything.
- Every page — a shared scroll-reveal snippet: an `IntersectionObserver` adds `.is-visible` to `.reveal` elements the first time they enter the viewport, then unobserves them. Pure CSS otherwise drives the animation (see `.reveal` in `assets/site.css`); the snippet is duplicated inline per page rather than factored into a shared file, consistent with the no-build-step architecture.

## Motion

- `--ease: cubic-bezier(0.16, 1, 0.3, 1)` is the shared easing token for hover/transition polish; prefer it over bare `ease`/`linear` on new transitions.
- `.reveal` / `.is-visible` (in `assets/site.css`) is the scroll-reveal utility described above. Content is visible by default: only `.js .reveal:not(.is-visible)` is hidden, so a failed script never blanks the page. Stagger multiple siblings with an inline `style="transition-delay:.08s;"` (matches the codebase's existing convention of ad hoc inline styles) rather than adding new nth-child CSS.
- `@media (prefers-reduced-motion: reduce)` in `assets/site.css` turns off animations and limits transitions to colour, opacity and visibility at 0.15s (movement stops, state feedback stays), and forces `.reveal` elements visible; it uses `!important` so it always wins. The Spectral gallery auto-scroll script additionally checks `matchMedia('(prefers-reduced-motion: reduce)')` and skips the `requestAnimationFrame` loop entirely, since that motion is JS-driven and the CSS override can't reach it.
- The hidden state is scoped to `:not(.is-visible)`, so a revealed element carries no reveal `transform` and component hover lifts like `.app-card:hover` apply normally. Don't add a `transform` to `.reveal.is-visible`; it would outrank those hover rules.

## Accessibility conventions

- Tap targets are at least 44px. Buttons and icons get a real `min-height`/size; inline text links (`.read-link`, `.back-link`, `.legal-back`, `.h-link`) keep their look and get a larger hit area from an absolutely positioned `::after`. `@media (pointer: coarse)` adds padding to footer links.
- Every `<img>` carries `width` and `height` attributes (intrinsic size) plus CSS `height: auto`, so pages don't jump while images load. Omitting `height: auto` stretches the image.
- Colours use `var(--accent-h)` for hue; don't hard-code a hue number.
