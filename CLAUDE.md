# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

No build step needed. Open `index.html` directly or serve it with any static server:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

On Windows: `start index.html`

## Architecture

Three files, no dependencies, no bundler:

- `index.html` — DOM structure: a `300×600` `<canvas id="board">` for the board, a `120×120` `<canvas id="next-canvas">` for the preview, and a `#overlay` div toggled for PAUSE / GAME OVER states.
- `style.css` — Dark/retro aesthetic. Layout via flexbox.
- `game.js` — All game logic (~300 lines, `'use strict'`, ES6+).

### Key data model (`game.js`)

- `board`: `ROWS × COLS` (20×10) matrix; cells hold `0` (empty) or `1–7` (piece color index).
- `current` / `next`: objects `{ type, shape, x, y }` where `shape` is a 2D matrix.
- `PIECES[1–7]`: piece shapes defined as square matrices; each cell contains its own color index so `merge()` stamps the color directly into `board`.

### Core function relationships

```
init() → createBoard(), spawn() → requestAnimationFrame(loop)
loop(ts) → accumulates dt → lockPiece() or current.y++ → draw()
lockPiece() → merge() → clearLines() → spawn()
spawn() → collide() on first frame → endGame() if blocked
```

- `collide(shape, ox, oy)` — pure function, used by movement, rotation, ghost, and game-over detection.
- `rotateCW(shape)` — transpose + reverse; returns a new matrix (does not mutate).
- `tryRotate()` — attempts kicks at offsets `[0, -1, 1, -2, 2]` before giving up.
- `ghostY()` — scans downward from `current.y` until `collide` returns true.
- `drawBlock(context, x, y, colorIndex, size, alpha)` — shared renderer for board, ghost (alpha=0.2), and next-piece preview.

### Tunable constants

| Constant | Default | Note |
|---|---|---|
| `COLS` / `ROWS` | 10 / 20 | Must match `canvas` `width`/`height` (`COLS×BLOCK`, `ROWS×BLOCK`) |
| `BLOCK` | 30 | Pixel size per cell |
| `LINE_SCORES` | `[0,100,300,500,800]` | Points for 1–4 lines, multiplied by level |
| `dropInterval` | 1000 ms | Speed formula: `max(100, 1000 − (level−1) × 90)` |
