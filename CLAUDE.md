# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A single-page personal portfolio for Mohak Chaudhry — a static HTML/CSS/JS site with no build step, no dependencies, and no package manager. Open `index-8.html` directly in a browser.

## File structure

- `index-8.html` — the entire site: inline CSS, HTML markup, and inline `<script>` at the bottom (~3 040 lines total)
- `Portfolio-jpg/` — 240 sequential JPEG frames (`ezgif-frame-001.jpg` … `ezgif-frame-240.jpg`) used for the hero canvas scrub
- `compressed-desk.mp4` / `desk-explode.mp4` — source video assets (not used at runtime; the frames were extracted from these)

## Architecture

The site is one monolithic HTML file. Everything is co-located in this order:

1. **CSS** (`:root` tokens → global resets → component blocks) — all inside a single `<style>` tag in `<head>`
2. **HTML** — seven sections rendered in sequence: `#hero`, `#journeyBegin`, `#about`, (journey), `#work`, `.toolkit`, `#contact`
3. **JavaScript** — one `<script>` block at the bottom; no modules, no bundler

### Section map

| Plate | Section | Key mechanic |
|-------|---------|--------------|
| 01 | `#hero` (`hero-scrub`) | 420 vh sticky stage; 240-frame canvas scrub synced to scroll via `requestAnimationFrame` lerp |
| 02 | `#journeyBegin` | Full-screen sticky canvas; particle system (`createParticleSystem`) spell out words on scroll |
| 03 | `#about` | Neural-network canvas background (`bridgeNeuralCanvas`); holographic tilt cards (`[data-holo]`); interactive wave-path SVG |
| 04 | Journey timeline | Static content with `.reveal` IntersectionObserver fade-ins |
| 05 | `#work` | Two case-study cards with 3D mouse-tilt (`[data-tilt]`) |
| 06 | Toolkit | Static grid |
| 07 | `#contact` | Particle canvas spelling "HELLO" |

### Key JS systems (all inline, bottom of file)

- **Hero canvas scrub** — preloads all 240 frames; `scrubLoop()` lerps `scrubCurrent` toward `scrubTarget` each rAF tick; `drawFrame()` applies `object-fit: cover` math manually
- **`createParticleSystem(canvas, words, colors, opts)`** — shared factory used by both `#journeyBegin` and `#contact`; each `Particle` steers toward sampled pixel coords of off-screen text rendered to a temp canvas
- **Neural canvas** (`#bridgeNeuralCanvas`) — draws animated nodes/edges; mouse proximity repels nodes
- **Holographic cards** (`[data-holo]`) — CSS perspective + JS `mousemove` tilt; glow follows cursor via `--mx`/`--my` CSS custom properties
- **Wave-path SVG** — quadratic Bézier that deforms toward the cursor on hover; rendered in `<path id="wavePath">`

### CSS design tokens (`--` in `:root`)

| Token | Value |
|-------|-------|
| `--bg` | `#0A0A0A` |
| `--bg-soft` | `#111111` |
| `--fg` | `#E8E6E1` |
| `--fg-soft` / `--fg-mute` | fg at 78 % / 55 % opacity |
| `--accent` | `#E8B84A` (gold) |
| `--accent-glow` | accent at 35 % opacity |
| `--line` / `--line-strong` | fg at 10 % / 25 % opacity |
| `--sans` | Plus Jakarta Sans |
| `--serif` | Instrument Serif |
| `--mono` | JetBrains Mono |
| `--max` | `1280px` |
| `--pad` | `clamp(20px, 4vw, 48px)` |

"Plate" labels (mono, uppercase, 10 px) are section eyebrows — a recurring visual motif throughout the page.

## Development workflow

No build step. To preview:
```
open "index-8.html"
# or serve locally to avoid CORS on the canvas frames:
python3 -m http.server 8080
```

If you add or replace hero frames, filenames must match `Portfolio-jpg/ezgif-frame-NNN.jpg` (zero-padded to 3 digits) and `FRAME_COUNT` (line ~2024) must be updated to match.
