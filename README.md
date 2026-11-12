# Lone.Tatrix

A mobile-first falling-block puzzle game with a neon digital-rain look. Presented by Lone Soceyety.

Everything is in `index.html` (inline CSS + JS, no frameworks). The logo is `assets/logo.png`.

## Play
Open `index.html` in a browser, or serve the folder (e.g. `python3 -m http.server`) and open it on your phone in portrait mode.
For best results on a phone, use "Add to Home Screen".

## Controls
| Touch | Action |
|---|---|
| Tap board | Rotate clockwise (with wall kicks) |
| Swipe left / right | Move 1 column (longer swipe = more columns) |
| Swipe down & hold | Soft drop |
| Fast flick down | Hard drop |
| Tap HOLD box | Hold / swap piece (once per drop) |

Keyboard: arrows move, Up / X rotate, Z rotate counter-clockwise, Down soft drop, Space hard drop, C / Shift hold, P / Esc pause.

## Rules
10×20 board, 7-bag randomizer, ghost piece, next-3 preview, hold.
Scoring: 1 / 2 / 3 / 4 lines = 100 / 300 / 500 / 800 × level. New level every 10 lines, and the blocks fall faster.
Best score and the mute setting are saved in localStorage.

## Play online
Hosted on GitHub Pages: https://kremzr.github.io/Lone_Soceyety_Tetris/

## Install as an app
On Android Chrome open the link, tap ⋮ → **Install app** (or **Add to Home screen** → Install). It opens full screen, portrait, and also works offline.
On iPhone Safari: Share → **Add to Home Screen**.
