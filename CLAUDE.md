# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is a **design handoff bundle** from Claude Design. The files are HTML/CSS/JS prototypes — not production code. The goal is to implement them pixel-perfectly in a real production stack (React, Next.js, etc.).

**Primary design file:** `georgia-landing-TV_9.html` — read it top to bottom before touching anything else. The other HTML files (`-bundle-src`, `-standalone-src`) are alternate exports of the same design.

There is no build system, package.json, or test suite. Open any `.html` file directly in a browser to preview.

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
**External dependency:** `qrcode-generator@1.4.4` via CDN for the WhatsApp QR code.

## Page structure (inside `georgia-landing-TV_9.html`)

```
#loader          — animated percentage counter overlay, hides on load
#page
  .device        — simulates a physical hardware chassis
    panel header — logo · language toggle · WhatsApp button
    LED marquee  — scrolling ticker strip
    hero copy    — sticker-style rotated tagline block
    .crt-screen  — services list in a retro TV frame (dark/light toggle)
    flow section — 8-step process, play/stop animated
    CTA          — holographic badge sticker
    footer
```

## JavaScript architecture (~600 lines, embedded in `<script>`)

Four main concerns, all vanilla ES6:

1. **Loader** — easing function animates a 0→100% counter, then fades out.
2. **i18n** — dictionary object with `pt` and `en` keys; `data-i18n` attributes on elements; `localStorage` persists the choice. Language toggle also updates `<html lang>` and canonical URL params.
3. **Dynamic rendering** — services list and 8-step flow are built from JS data arrays into the DOM on `DOMContentLoaded`.
4. **Interactions** — dark/light CRT toggle (`.mode-dark` / `.mode-light` class swap), flow play/stop animation, QR code SVG generation.

## Implementation notes for production

- The device chassis is a visual metaphor — in a real implementation, decide whether to keep the "hardware" framing or flatten to a standard layout.
- Sticker elements use `skewX`, `rotate`, and pseudo-element foil shimmer — replicate with CSS transforms, not images.
- CRT scanline/vignette/rolling effects are pure CSS filters and keyframes — safe to port directly.
- The i18n dictionary (lines ~2390–2464) is the source of truth for all copy in both languages.
- `clamp()` is used throughout for fluid type sizing — preserve this for responsive fidelity.
- Favicons alternate by `prefers-color-scheme` (light: `favicon-light.svg`, dark: `favicon-dark.svg`).
