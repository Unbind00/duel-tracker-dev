# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Duel Tracker is a two-player Yu-Gi-Oh! life point tracker PWA. The production repo is at https://github.com/Unbind00/duel-tracker.

## Development

There is no build step. Open `index.html` directly in a browser, or serve it locally:

```bash
python -m http.server 8080
# or
npx serve .
```

No package manager, no dependencies, no tests.

## Architecture

The entire app lives in a single file: **`index.html`** contains all CSS, HTML, and JavaScript inline. There are two supporting files:
- `manifest.json` — PWA manifest (fullscreen portrait app)
- `sw.js` — Service worker for offline caching

### Layout

The app is split into two regions:
- **Sidebar** (left, 124px wide) — dice roll, coin flip, duel log, install link, reset button. The top section of the sidebar is `rotate(180deg)` so Player 2 can read their buttons from the opposite side of the device.
- **Panels** (right, flex) — two stacked player panels. The P2 panel (top) is CSS `rotate(180deg)` so both players face their own panel when the device lies flat between them.

### JavaScript State

All state is plain global variables (no framework):
```js
var lp = { 1: 8000, 2: 8000 };  // current life points
var gameLog = [];                 // array of { player, type, change, result }
var activePlayer = 1;
var mode = 'sub';    // P1 edit mode: 'sub' | 'add' | 'half'
var modeP2 = 'sub';  // P2 edit mode
var p1Input = '';    // P1 numpad string buffer
var p2Input = '';    // P2 numpad string buffer
```

### Overlays / Sheets

All UI panels are always in the DOM and shown/hidden via CSS classes:
- `.overlay` / `.overlay-p2` — LP edit sheets (slide from bottom / top)
- `.log-overlay` / `.log-overlay-p2` — duel log sheets
- `.reset-overlay` — reset confirmation modal
- `.toast-overlay` — dice/coin result toast

Each overlay closes when clicking its backdrop (checked via `e.target === overlay element`).

### Color Scheme

- Player 1: `#00c9b1` (teal)
- Player 2: `#9b5de5` (purple)
- Background: `#0a0a0a`
- Fonts: `Share Tech Mono` (UI text), `Rajdhani` (numbers/titles)
