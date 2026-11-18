# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users
Primary audience is an open decision: the owner expressed no preference between Lone Soceyety fans, the owner and friends, or general web players. Confirmed in practice: the owner plays it on an Android phone, installed from Chrome as a full-screen app.

## Product Purpose
Lone.Tatrix is a falling-block puzzle game (10×20 board, the seven standard four-block shapes) presented by Lone Soceyety. It is meant to be both, equally: a genuinely good game to play, and a way to carry the Lone Soceyety name to everyone who plays it. Success is a game people enjoy and come back to, with the Lone Soceyety presentation seen along the way.

## Positioning
The neighbouring products are generic block-stacking games. Lone.Tatrix differs in being a Lone Soceyety–presented game with its own hacker / digital-rain identity, built to be played by touch on a phone browser rather than ported from a keyboard or console game.

## Operating Context
- Played on phones in portrait, mostly one-handed, in short sessions that get interrupted (leaving to the home screen and coming back is normal; the game saves and resumes on the pause screen).
- Installed as a full-screen app from Chrome (web app manifest, service worker), and also playable in a browser tab. Works offline after the first visit.
- Hosted on GitHub Pages from the `main` branch: https://kremzr.github.io/Lone_Soceyety_Tetris/. Anything pushed to `main` reaches players within about a minute.
- Desktop keyboard controls exist for testing.

## Capabilities and Constraints
- Gameplay: 7-bag randomizer; automatic fall that speeds up each level (new level every 10 lines); ghost piece; next-3 preview; hold (once per drop); rotation with wall kicks; half-second lock delay; game over when a new piece cannot spawn.
- Scoring: 1 / 2 / 3 / 4 lines = 100 / 300 / 500 / 800 × level. No points for dropping. Best score is saved on the device (localStorage).
- Touch controls: tap = rotate; swipe left/right = move one column, with more columns for a longer swipe; drag down = soft drop that follows the finger (one row per cell of finger travel, with no automatic speed-up); fast flick down = hard drop; tap the HOLD box = hold. Gestures are tuned so a tap is never read as a swipe (about 20px and 300ms thresholds). The owner explicitly asked for soft drop to give precise, slow control.
- Screens: main menu, game, pause overlay, game over, How to Play.
- Extras: Web Audio synthesized sound (no audio files) with a saved mute; vibration on line clears; glitch effect on game over and 4-line clears; line clears dissolve into falling code characters.
- Binding technical constraint: a single self-contained `index.html` with inline CSS and JavaScript and no frameworks. Supporting files are only `assets/logo.png`, `icons/`, `manifest.json` and `sw.js`.
- The only external dependency is the Share Tech Mono font from Google Fonts, with a Courier New fallback.
- Original artwork only: no film logos, names, characters, or recreations of a specific existing game's look. The word "Tetris" is not used in the interface (4-line clears are called "QUAD").
- Not marked binding (current state, which can change): the phone-first portrait layout, and the way the logo is presented.

## Brand Commitments
- Name: "Lone.Tatrix", shown as LONE.TATRIX in the title.
- Presenter: Lone Soceyety. The logo is `assets/logo.png` (yellow and red script with a red eye, transparent background). It currently appears as "Presented by:" + logo on the main menu, in a corner during play, and on the game-over screen. The original brief said to use the logo exactly as provided, without redrawing it. That presentation was not re-marked as binding during init.
- Binding (confirmed during init): the green digital-rain look, meaning a black background, neon green (#00FF41 family), monospace type, and a hacker / terminal mood.
- App icon: the letters "LT" built from glowing blocks (`icons/`).

## Evidence on Hand
- The shipped game: `index.html`; the logo: `assets/logo.png`; app icons: `icons/`.
- None exist: player counts, reviews, testimonials, press, or leaderboard data. Future work must not invent any of these.

## Product Principles
1. Play comes first: controls must feel precise and fair on a phone, and any visual effect gives way to input clarity.
2. The brand rides along without getting in the way: Lone Soceyety is present on every screen, but never covers the board or the controls.
3. Interruptions are part of play: leaving and coming back must never lose a game.
4. Keep it light: one file, no build step, instant load, works offline.
5. Original work: the digital-rain mood is the game's own, never a copy of a film or of an existing game.
