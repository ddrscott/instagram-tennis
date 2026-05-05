# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Single-file static web app (`index.html`) that generates 1080×1920 Instagram Story / Reel score cards for GBN tennis matches. No build step, no dependencies — just open the file in a browser.

## Running

```sh
open index.html        # local file, no server needed
```

For testing PNG export with cross-origin images, serve over HTTP:

```sh
python3 -m http.server 8000
```

## Architecture

The whole app is one HTML file with three sections: a left control panel, an SVG stage, and a `<script>` that wires them together.

### Rendering pipeline

The SVG (`#card`, `viewBox="0 0 1080 1920"`) is the source of truth. Every input change calls `render()`, which mutates SVG attributes directly — no virtual DOM, no template strings. The on-screen preview and the export use the *same* SVG element.

### Layout math (`render()`, around line 270)

The score block (title → logos → score → subtitle) is positioned as a single unit driven by `blockY` (0–100 slider) clamped between `minBlockTop=120` and a computed `maxBlockTop` so the subtitle always stays inside the 1920px canvas. Block height is derived from `titleSize`, `logoSize`, and `scoreSize` — if you add another element to the block, update `blockHeight` so clamping still works.

### Persistence

- **Logos** (`logoA`, `logoB`) are stored as data URLs under their own localStorage keys — so users upload once.
- **All other settings** (text, colors, sizes, sliders) are stored under `cfg:<elementId>` keys. The persistence loop at the bottom of the script auto-wires every `<input>`/`<select>` registered in the `els` object, skipping file inputs and buttons.
- **To add a new tweakable control:** add the DOM element, register it in `els`, and use it inside `render()`. Persistence and re-render-on-input are automatic. No other plumbing needed.

### Background image

`#bgImage` is positioned via `preserveAspectRatio`. The `bgFit` select toggles `slice` (cover) vs `meet` (contain), and the `bgY` slider maps 0/50/100 → `YMin`/`YMid`/`YMax` for vertical focus. There is no fine-grained Y offset — only three discrete vertical anchors.

### Drop shadow

A single `#shadow` slider (0–100) drives `stdDeviation`, `dy`, *and* `flood-opacity` of the `feDropShadow` filter applied to the entire score group. One knob, three correlated effects — keep it that way unless asked.

### PNG export

`els.download` serializes the live SVG, blob-URLs it, loads it into an `Image`, draws onto a 1080×1920 canvas, and exports PNG. **Critical:** awaits `document.fonts.ready` before rasterizing — otherwise Anton/Oswald fall back to system fonts in the export. Fonts come from Google Fonts (Anton, Oswald, Inter) loaded via `<link>`. If a user reports "fonts wrong in PNG but right on screen," that's the failure mode.

### SVG export

`els.downloadSvg` exports the raw SVG string with `xmlns` + `xmlns:xlink` attributes added. Logos and background are inlined as data URLs (since that's how they're loaded), so the SVG is fully self-contained.

## Conventions

- Keep everything in one file. The simplicity is the point — no bundler, no framework, no module system.
- Use SVG attributes for layout (`x`, `y`, `width`, `font-size`), not CSS. The export depends on SVG-native rendering.
- Default copy is GBN Spartans tennis specific (`"GBN SPARTANS VICTORY!"`, gold accent `#d4af37`). Don't generalize away the team identity unless asked.
