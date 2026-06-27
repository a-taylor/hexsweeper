# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

Open `index.html` directly in a browser — no build step, no server required. For local development with live reload, any static file server works:

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

There are no dependencies, package.json, or build tools.

## Architecture

Everything lives in a single `index.html` file with three sections: CSS (in `<style>`), HTML structure, and a `<script>` block. The script is organised into clearly labelled regions:

**HEX GRID MATH** — Pure functions for flat-top axial hex geometry. The coordinate system uses axial `(q, r)` pairs stored as string keys `"q,r"` in the `cells` object. Key functions:
- `axialToPixel(q, r, R)` — converts axial coords to canvas pixel centre
- `pixelToAxial(px, py, R)` — inverse, used for click hit-testing (routes through `axialRound` to snap to nearest hex)
- `hexRing(rad)` — walks a ring of radius `rad` to enumerate all cells at that distance from origin
- `HEX_DIRS` — the 6 neighbour direction vectors in axial space

**GAME STATE** — Module-level `let` variables hold all mutable state: `cells`, `gameState` (`idle | playing | won | lost`), `firstClick`, `flagCount`, `totalMines`, `gridRadius`. Mines are placed on the first click (via `placeMines`) to guarantee a safe first reveal. `floodReveal` is an iterative BFS that expands from zero-adjacency cells.

**RENDERING** — `render()` is a full redraw (no dirty tracking). It reads `cells`, `hoveredKey`, `revealAnims`, and `gameState` to decide what to draw. The canvas is sized by `resize()` which calculates the largest `HEX_R` that fits the viewport, then sets `offsetX/offsetY` to centre the grid. `revealAnims` drives a per-cell fade-in by incrementing a `0→1` progress value each frame and scheduling another `requestAnimationFrame` until all animations complete.

**INPUT** — Mouse click/contextmenu for reveal/flag. Touch is handled separately: a 400ms long-press flags, a short tap dispatches a synthetic `click` event.

## Key design decisions

- The grid is a **hexagonal region** (all cells within `gridRadius` rings of origin), not a rectangular grid. `DIFFICULTIES` maps easy/medium/hard to `{ radius, mineCount }`.
- The first click and all its neighbours are always mine-free — `placeMines` excludes the clicked cell plus its 6 neighbours from the candidate pool.
- `cells` is a flat object (not a 2D array) keyed by `"q,r"` strings. All neighbour lookups filter by `k in cells` to handle boundary cells naturally.
- `HEX_R` (pixel radius) is recalculated on every `resize()` call and on `newGame()`, so the grid always fills the available viewport.
