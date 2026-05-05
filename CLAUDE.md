# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A **design handoff bundle** from Claude Design. The files are HTML/CSS/JS prototypes — not production code. The goal is to implement them pixel-perfectly in a real production stack (React, Next.js, etc.).

**Primary design file:** `georgia-landing-april26.html` — read it top to bottom before touching anything else. The other HTML files (`-bundle-src`, `-standalone-src`) are alternate exports of the original design handoff.

There is no build system, package.json, or test suite. Open `georgia-landing-april26.html` directly in a browser to preview. Deployment is static via Vercel — `vercel.json` rewrites `/` to `/georgia-landing-april26.html`.

## Design intent — read before making any change

This page's primary purpose is to **demonstrate creative and original capability to potential clients**. The unusual aesthetic (retro hardware chassis, CRT screen, LED marquee, holographic stickers) is the product — it is not a bug or a rough draft.

**Never change the visual design.** Bug fixes, SEO corrections, accessibility improvements, and performance tweaks are all welcome, but they must leave the rendered output pixel-identical. If a fix would alter layout, typography, color, animation, or any visible element, stop and ask first.

## Design system

All design tokens are CSS custom properties at the top of the `<style>` block (~line 40):

| Variable | Role |
|---|---|
| `--bg` | Page background (`#0a0a0a`) |
| `--chassis` / `--chassis-2` / `--chassis-3` | Device body shades |
| `--wood` | Wood frame accent (`#b5651d`), width `--wood-w: 28px` |
| `--teal` / `--teal-glow` | Accent color (`#08ECF3`) |
| `--gradient` | Brand gradient: amber → red → teal |
| `--wa-green` | WhatsApp CTA green |
| `--text-main` / `--text-mid` / `--text-soft` | Text hierarchy |

**Fonts:** Epilogue (200/300/400/600/700) + IBM Plex Mono (300/400) via Google Fonts.  
**External CDN deps:** `qrcode-generator@1.4.4` for the WhatsApp QR code.

## Page structure (inside `georgia-landing-april26.html`)

```
#loader           — animated percentage counter overlay, fades out on load
#page
  .device         — simulates a physical hardware chassis (wood flanks via ::before/::after)
    .panel-header — logo · language toggle (PT/EN) · WhatsApp button
    .panel-led    — scrolling marquee ticker (75s CSS animation, 14× repeated)
    .panel-hero-copy — holographic sticker tagline + mobile WhatsApp zone + QR code
    .divisoria    — separator bar with decorative screw details
    .panel-section  — SERVICES: CRT TV display, dark/light mode toggle
    .divisoria
    .panel-section  — FLOW: 8-step animated process, play/stop controls
    .panel-cta    — call-to-action + holographic badge sticker
    .panel-footer — footer, privacy/terms links (open modal overlay)
  .desk-line      — outside chassis: "br" · gradient line · "mmxxvi"

#modalOverlay     — privacy policy / terms of use modal (not separate pages)
```

Single responsive breakpoint at `@media (max-width: 680px)`.

## JavaScript architecture (~400 lines, embedded in `<script>`)

Five main concerns, all vanilla ES6:

1. **Loader** — easing function (power 1.8) animates a 0→100% counter over 850ms, then fades out and reveals the page.
2. **SFX** — Web Audio API sound engine. `click()`, `press()`, `toggle()`, `blip()` synthesize procedural sounds for every interaction. No audio files — all generated at runtime.
3. **i18n** — `window.I18N` object with `pt` and `en` keys; `data-i18n` attributes on elements; `localStorage` persists the choice. Language toggle also updates `<html lang>` and canonical URL params. All visible copy (services, flow steps, marquee text, WhatsApp URL) lives here.
4. **Dynamic rendering** — `buildLed()`, `renderServices()`, `renderFlow()` generate DOM from JS data arrays on `DOMContentLoaded` and re-render on every language switch.
5. **Interactions** — dark/light CRT toggle (`.mode-dark` / `.mode-light` class swap), flow play/stop animation (steps cycle every 350ms), QR code SVG generation, modal open/close.

## Key visual effects (CSS-only)

- **CRT screen:** `border-radius: 28% / 12%` oval bezel, scanline `repeating-linear-gradient`, rolling-band `@keyframes crt-roll 7.5s`, `feTurbulence` SVG noise filter, subtle flicker animation.
- **Holographic sticker:** `hue-rotate`/`brightness`/`saturate` keyframe every 6s over a multi-stop pastel gradient; `mix-blend-mode: overlay` foil layers; `clip-path` corner peel effect.
- **LED indicators:** `radial-gradient` bulb simulation, `@keyframes led-pulse 2.8s` bright↔dim.
- **Device chassis:** `::before`/`::after` pseudo-elements for wood flanks; `radial-gradient` bevel at each corner; heavy multi-layer `box-shadow` for depth.

## Implementation notes for production

- The i18n dictionary is the source of truth for all copy in both languages.
- `clamp()` is used throughout for fluid type sizing — preserve this for responsive fidelity.
- Favicons alternate by `prefers-color-scheme` (`favicon-light.svg` / `favicon-dark.svg`).
- Sticker elements use `skewX`, `rotate`, and pseudo-element foil shimmer — replicate with CSS transforms, not images.
- All CRT effects are pure CSS keyframes/filters — safe to port directly to a production stylesheet.
