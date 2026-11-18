# CLAUDE.md

Lone.Tatrix: a mobile-first falling-block puzzle game with a neon green digital-rain theme, presented by Lone Soceyety. It's live on GitHub Pages at https://kremzr.github.io/Lone_Soceyety_Tetris/ and can be installed as a full-screen app.

Product context (users, purpose, binding constraints) is in `PRODUCT.md`. Read it before design work.

## Files

| Path | What it is |
|---|---|
| `index.html` | The whole game: inline CSS + JS, no frameworks, no build step |
| `assets/logo.png` | Lone Soceyety logo (4042×2458, transparent). Use it exactly as provided; never redraw or alter it |
| `icons/` | App icons ("LT" built from glowing blocks): 192, 512, maskable 512, apple-touch 180 |
| `manifest.json` | Web app manifest: `display: fullscreen`, portrait, black theme |
| `sw.js` | Service worker: network-first with a cache fallback, so it works offline |
| `.nojekyll` | Makes GitHub Pages serve the files as-is |

## Hard rules

- Keep everything in a single `index.html` with no frameworks, bundlers or npm dependencies. The only external resource is the Share Tech Mono font from Google Fonts (Courier New is the fallback).
- Keep the visual identity: black background, neon green `#00FF41` family, monospace type, hacker/terminal mood. Piece colours stay within the green family, plus a distinct glyph per piece.
- Use original artwork only: no film logos, names or characters, and no recreation of an existing game's look. Don't use the word "Tetris" in the UI (a 4-line clear is "QUAD").
- Never cover the board or the controls with branding. "Presented by:" + logo appears on the menu, in the side column during play (it hides automatically when the side column is too short), and on game over.
- The game is played by touch on phones in portrait. Test at phone sizes (iPhone SE 320×568 up to Pixel 7).

## index.html structure

The script is one IIFE, split by banner comments:
`CONSTANTS` → `PIECES` → `SOUND` → `STATE` → `GAME FLOW` → `FX helpers` → `HUD` → `RENDERING` → `DIGITAL RAIN BACKGROUND` → `MAIN MENU: falling block shapes` → `MAIN LOOP` → `INPUT: TOUCH / POINTER GESTURES` → `INPUT: KEYBOARD` → `BUTTONS` → `MENU INTRO` → `BOOT`.

Key facts:
- **Board:** `COLS=10`, `ROWS=20`, plus `BUF=2` hidden rows on top (`TOTAL=22`). Board row `r` is drawn at screen row `r - BUF`. Pieces spawn in the first visible rows.
- **Pieces:** SRS orientations. Rotation states come from rotating the matrix (`SHAPES[type][rot]` = list of `[col,row]`). Wall kicks use the SRS tables `KICKS` / `KICKS_I`, with y pointing **up**, so they are applied as `y - ky`.
- **Randomizer:** 7-bag (`nextFromBag`); the queue always holds ≥4 pieces and shows 3.
- **Gravity:** `gravityMs(level) = max(55, 1000·0.84^(level-1))`. Level = `floor(lines/10)+1`.
- **Lock:** a 500ms lock delay, reset by a move or rotation up to 15 times; reaching a new lowest row resets the counter. Locking entirely inside the hidden rows ends the game.
- **Scoring:** `[0,100,300,500,800][lines] × level` (the level *before* the clear), with no drop points. Best score is in localStorage key `lt_best`.
- **Line clear:** `G.clearing` runs `CLEAR_MS=380`: a flash for `FLASH_MS`, then the rows dissolve into code-character particles, then `finishClear()` removes the rows and spawns the next piece.
- **State machine:** `G.state` is one of `menu | playing | paused | over`.
- **Save/resume:** `saveGame()` stores the whole game in localStorage (`lt_save`) when the game pauses, when the page is hidden, and on `pagehide`/`freeze`. At boot, `loadSave()` → `restoreGame()` reopens it **paused**. The save is cleared by game over, `startGame()` (Restart / Play Again) and `toMenu()`. This exists because a home-screen shortcut reloads the page.
- **Rendering:** the board is a canvas scaled by devicePixelRatio, drawn with pre-rendered cell sprites (`makeSprite`). Sprites are rebuilt in `layout()` on resize and after fonts load. The digital rain is a separate full-screen canvas, throttled (~20fps in game, ~25 on the menu) and dimmed with CSS opacity.
- **Layout:** `layout()` sizes the cell as `min(width-fit, height-fit)`. The side column (HOLD, NEXT ×3, mini logo) gets any spare width. It uses `--vh` from `visualViewport`, plus safe-area insets.

## Touch controls (tuned with the owner; don't regress)

Pointer events are attached to `#playfield`; `touch-action: none` and global touchmove `preventDefault` block scrolling, zoom and pull-to-refresh.
- **Tap:** movement < `TAP_DIST` (20px) and duration < `TAP_TIME` (300ms) → rotate clockwise. A longer press does nothing.
- **Gesture axis:** locked once the finger has moved 20px: horizontal, `down` or `up` (up does nothing).
- **Horizontal:** columns = `sign·(1 + floor((|dx|-20)/cell))`, applied live. A swipe blocked at the wall clamps, so swiping back responds at once.
- **Down = soft drop that follows the finger 1:1** (one row per cell of travel), with **no** automatic soft-drop gravity. The owner asked for slow, precise placement, so don't bring back a fast-gravity soft drop for touch.
- **Hard drop** only on a real flick: release velocity over the last ~110ms ≥ `FLICK_VEL` (1.3 px/ms) and dy > 50px.
- A gesture is ignored once its piece has locked (`g.piece !== G.pieceId`).
- **HOLD:** `pointerdown` on `#holdBox`.
- **Keyboard (desktop testing):** ←/→ with auto-repeat (DAS 170 / ARR 50), ↑/X rotate, Z rotate counter-clockwise, ↓ soft drop, Space hard drop, C/Shift hold, P/Esc pause.

## Running and testing

```bash
python3 -m http.server 8765   # then open http://localhost:8765/index.html
```

There's no test suite in the repo. Test with Playwright (pre-installed; `require('playwright')`), using phone emulation such as `devices['Pixel 7']` or `devices['iPhone SE']`.
- `window.__LT` exposes the state and helpers for tests: `G, SHAPES, nextFromBag, collide, rotate, tryMove, hardDrop, holdPiece, spawn, startGame, gravityMs, pauseGame, resumeGame, lockPiece`.
- For gesture timing, dispatch `PointerEvent`s **inside the page** at ~16ms steps. CDP touch events arrive ~80ms apart in headless mode, so every swipe looks slow and flick tests fail.
- To simulate leaving the app: override `document.hidden` to true, dispatch `visibilitychange`, close the page, then open a new page in the same context.
- Check installability with CDP `Page.getInstallabilityErrors`, and offline mode with `context.setOffline(true)` + reload.

## Deploying

GitHub Pages serves the `main` branch root. A push to `main` goes live in ~45s. The installed app picks up changes the next time it opens online, because the service worker fetches network-first. When you add a file the game needs offline, add it to `CORE` in `sw.js` and bump `CACHE`.

## Git workflow

- Develop on `claude/lone-tatrix-game-akjs3r`. The owner usually wants game changes committed and pushed to **`main`** too, so they go live. Push to `main` only when asked; for doc-only changes, ask first.
- Commit messages: a summary line plus a short body explaining why.
