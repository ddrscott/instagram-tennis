# GBN Tennis Score Card

A single-page tool for generating 1080×1920 Instagram Story / Reel score cards for GBN Spartans tennis matches. Drop in a background photo, two team logos, the final score, and download a ready-to-post PNG.

[**Try it →**](https://ddrscott.github.io/instagram-tennis/) <!-- update this URL after enabling Pages -->

## Features

- Live SVG preview at the exact 1080×1920 export resolution
- Title, subtitle, and score with adjustable colors, outlines, and sizes
- Background image with cover/contain fit and three-anchor vertical focus
- Two logo slots (saved in your browser — upload once)
- Single-knob drop shadow and dim controls
- Export as PNG (Instagram-ready) or SVG (vector, fully self-contained)
- All settings persist in `localStorage` between sessions

## Running locally

The app is a single HTML file with no build step.

```sh
open index.html
```

For PNG-export testing with cross-origin images, serve over HTTP:

```sh
python3 -m http.server 8000
# then open http://localhost:8000/
```

## Deployment

Deployed via **GitHub Pages** from the `main` branch. To enable on a fork:

1. Push to GitHub.
2. Go to **Settings → Pages**.
3. Set **Source** to `Deploy from a branch`, branch `main`, folder `/ (root)`.
4. Wait ~30 seconds; the site will be available at `https://<user>.github.io/instagram-tennis/`.

No workflow file is needed — Pages serves the `index.html` directly.

## Project layout

```
index.html    The entire app (markup, styles, script)
LICENSE       MIT
CLAUDE.md     Notes for AI assistants on architecture and conventions
```

The architecture is intentionally one file: no bundler, no framework, no module system. See `CLAUDE.md` for details on the rendering pipeline, layout math, and the auto-wired persistence pattern.

## Contributing

Pull requests welcome. Some guidelines:

- **Keep it one file.** The simplicity is the point. If you reach for a framework, propose it in an issue first.
- **Use SVG attributes for layout** (`x`, `y`, `width`, `font-size`) rather than CSS — the PNG export depends on SVG-native rendering.
- **New tweakable settings** only need to be (a) added as a DOM control and (b) registered in the `els` object. Persistence and re-render-on-input are wired automatically.
- **Test the PNG export** after changes that touch fonts, filters, or `render()`. The export awaits `document.fonts.ready` before rasterizing — if you add a new font, make sure it's covered.

To propose a change:

1. Fork and create a branch.
2. Make your change in `index.html`.
3. Open the file locally and verify both the live preview and the PNG export.
4. Submit a pull request describing what changed and why.

## License

[MIT](LICENSE)
