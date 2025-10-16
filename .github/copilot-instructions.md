## Purpose
Quick guidance for AI coding agents working on this repo (a small static website: iRDC). This file highlights concrete patterns, file locations, and debugging shortcuts that make an agent productive here.

## Big picture
- Static, client-side interactive reference card. Primary entry: `index.html` which embeds SVGs and loads small HTML fragments from `equations/` when areas are clicked.
- All interactivity and wiring is implemented inline in `index.html` (no build step): the page loads `img/rdc_front.svg` and `img/rdc_back.svg`, injects them into the DOM, and maps SVG element ids to local HTML fragments.

## Key files & layout
- `index.html` — main page. Contains popup UI (element id: equationPopup), MathJax integration, and mapping/initialization code (`initializeClickableArea(...)`).
- `img/rdc_front.svg`, `img/rdc_back.svg` — svg artwork with invisible hotspots (rect/path) whose ids use the `prc-<n>` naming convention.
- `equations/` — folder of HTML fragments (by category). Example: `equations/number-density/eq01_weighted_atomic_mass.html`.
- `README.md` — author notes describing how hotspots are created with Inkscape.

## Concrete wiring pattern
- On load, `index.html` fetches `img/rdc_front.svg` and inserts it into the DOM container with id `rdcFront`.
- The script then calls `initializeClickableArea(svgElement, areaId, contentUrl, column)` many times to map hotspot ids to fragments and a placement column (left/middle/right).
- `initializeClickableArea` sets hover styles and adds click handlers that call `loadContent(filename, event, column)` which fetches the fragment and calls `showPopup(...)`. `showPopup` inserts the fragment and runs `MathJax.typesetPromise()` to render LaTeX.

## Project-specific conventions
- Hotspot ids must use the prefix `prc-<n>` (e.g. `prc-1`). `index.html` depends on exact ids.
- Fragment filenames use underscores and the format `eqXX_<name>.html`. Maintain exact filenames; paths in `index.html` are literal.
- `column` values are `left`, `middle`, or `right` and affect popup placement.

## How to add or update an equation (exact steps)
1. Create or edit the HTML fragment under `equations/<category>/`. Fragments may contain LaTeX.
2. In `img/rdc_front.svg` (or back), add an invisible rect/path in Inkscape and set its `id` to `prc-<n>`.
3. Add an `initializeClickableArea(svgElement, 'prc-<n>', 'equations/.../eqXX_name.html', '<column>')` call in `index.html`.

## Local preview & developer workflow
- No build step — static site. Quick local preview from the repo root:

```bash
python3 -m http.server 8000
# then open http://localhost:8000/ in your browser
```

- VS Code Live Server also works for live reload during edits.

## Debugging tips & gotchas (from the code)
- If a hotspot doesn't work, open the browser console — `initializeClickableArea` logs "Initializing clickable area:" and warns when an id isn't found.
- MathJax: fragments rely on `MathJax.typesetPromise()`; ensure the fragment loaded before measuring or relying on rendered size.
- Popup-placement bug: `index.html` currently uses a bitwise operator in a placement check: `if (column == "left" | column == "middle")`. Replace `|` with `||` to fix logic.
- Popup placement measures `popup.offsetWidth`/`popup.offsetHeight`; ensure content is in the DOM before measuring.

## External integrations
- MathJax is loaded from CDN: `https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js`.
## Purpose
Quick guidance for AI coding agents working on this repo (a small static website: iRDC). This file highlights concise, actionable patterns, file locations, and debugging shortcuts to get productive quickly.

## Big picture
- Static, client-side interactive reference card. Primary entry: `index.html` which embeds SVGs and loads small HTML fragments from `equations/` when hotspots are clicked.
- All interactivity lives inline in `index.html`: it fetches `img/rdc_front.svg` and `img/rdc_back.svg`, injects the SVGs, and maps SVG element ids to local HTML fragments which are shown in a popup.

## Key files & layout
- `index.html` — main page. Contains the popup UI, MathJax include, SVG injection, and mapping/initialization code (notably `initializeClickableArea(...)`).
- `img/rdc_front.svg`, `img/rdc_back.svg` — artwork with invisible hotspots (rect/path). Hotspot ids use the `prc-<n>` naming convention.
- `equations/` — HTML fragments by category (e.g. `equations/number-density/eq01_weighted_atomic_mass.html`). Fragments may include LaTeX.
- `README.md` — notes on the Inkscape workflow used to create hotspots.

## Concrete wiring pattern (how to change things)
- On load, `index.html` fetches `img/rdc_front.svg` and inserts it into the container with id `rdcFront`.
- The embedded script then calls `initializeClickableArea(svgElement, areaId, contentUrl, column)` repeatedly to map `prc-<n>` ids to fragment URLs and a placement column (`left|middle|right`).
- `initializeClickableArea` sets hover effects and registers click handlers that call `loadContent(filename, event, column)`. `loadContent` fetches the fragment and calls `showPopup(...)`, which inserts content then calls `MathJax.typesetPromise()`.

## Project-specific conventions
- Hotspot ids: must be `prc-<n>` (e.g. `prc-1`) — `index.html` references these directly.
- Fragment filenames use underscores and the `eqXX_<name>.html` pattern. Use exact relative paths — `index.html` contains literal paths.
- `column` parameter: one of `left`, `middle`, `right` and influences popup placement.

## How to add or update an equation (exact steps)
1. Create or edit an HTML fragment in `equations/<category>/` (may include LaTeX).
2. Add an invisible rect/path in `img/rdc_front.svg` (or back) with `id="prc-<n>"` using Inkscape.
3. In `index.html`, add an `initializeClickableArea(svgElement,'prc-<n>','equations/.../eqXX_name.html','<column>')` call.

## Local preview & common workflows
- No build step — static files. Quick local preview from repo root:

```bash
python3 -m http.server 8000
# open http://localhost:8000/
```

- VS Code Live Server works for live reload during edits.

## Debugging tips & gotchas (from the code)
- If a hotspot doesn't work, open the browser console — `initializeClickableArea` logs initialization and warns when an id isn't found.
- MathJax rendering: fragments rely on `MathJax.typesetPromise()`; ensure fragment fetched successfully before measuring or expecting rendered size.
- Popup-placement bug: current code uses `if (column == "left" | column == "middle")` (bitwise `|`). Replace with `||` to correct the logic.
- Popup positioning reads `popup.offsetWidth`/`popup.offsetHeight` — ensure content is appended to DOM before measuring.

## External integrations
- MathJax CDN: `https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js`. No other remote dependencies are required for local use.

## Example edits an agent can make immediately
- Add mapping for a new hotspot in `index.html`:

  initializeClickableArea(svgElement,'prc-53','equations/new-category/eq53_new_equation.html','right');

- Fix the `|` -> `||` popup-placement bug in `index.html` and visually validate by serving locally.

## Final notes
- Keep edits minimal and test in a local static server. Use exact relative paths as present in `index.html` when adding or renaming fragments.
- If you'd like, I can also apply the `|`->`||` fix and run a local preview and report back — tell me which you'd prefer.
