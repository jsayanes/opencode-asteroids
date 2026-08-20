# AGENTS.md

Single-file Asteroids clone. No build, no deps, no tests, no CI.

## Run

Open `index.html` directly, or `npx serve .` then `http://localhost:3000`. There is nothing else to install.

## Architecture

All game logic lives in `game.js` (one ES6+ file, `'use strict'`, loaded via plain `<script src="game.js">` in `index.html` — **no modules, no bundler, do not introduce either**). Canvas is 800×600; the size is hardcoded as `W`/`H` constants in `game.js` AND on the `<canvas>` in `index.html` — both must match.

Source is organized top-down by section banners (`── Input ──`, `── Ship ──`, …) ending in `loop(ts)` + `initGame()` at the bottom. Entities are classes (`Bullet`, `Asteroid`, `Ship`, `Particle`); global game state is module-level `let`s (`ship, bullets, asteroids, particles, score, lives, level, state, deadTimer`).

## Conventions that matter

- **Toroidal space.** All positions wrap via `wrap(v, max)`. Every entity's `update` must wrap; do not add clamping/bouncing.
- **Input.** `keys` = held state (rotate, thrust). `justPressed` + `pressed(code)` = edge-triggered (shoot, restart). New key actions must pick the right pattern. Arrow keys + `Space` are in the `preventDefault` list in the `keydown` listener; add new such keys there or the page scrolls.
- **Asteroid sizes.** Size 3 = large, 2 = medium, 1 = small (small does not split), 0 = mini rápido (super fast, spawned on a timer from a screen edge, does not split). `RADII`, `SPEEDS`, `POINTS` are arrays indexed by size (0 = mini rápido, 8/280/200).
- **dt.** Clamped to 0.05s in `loop` to survive tab refocus; everything is in seconds, not ms.
- **State machine.** `state ∈ 'playing' | 'dead' | 'gameover'`. `dead` is the respawn pause (`deadTimer`), `gameover` waits for `Space` to re-init.
- **Collision.** Bullet vs asteroid: pure radius. Ship vs asteroid: `ship.radius + a.radius * 0.82` (the 0.82 tolerance is intentional, keep it).
- **Localization.** UI/README/comments are in **Spanish** (`NIVEL`, `PUNTAJE`, `ESPERAR`, etc.). Match this when adding HUD text or comments.

## Gotcha: README is ahead of the code

`README.md` advertises power-ups and a "estrella fugaz" (shooting star) asteroid type. **These are not implemented in `game.js`.** Treat the README as aspirational, not authoritative.

## Verification

No test/lint/typecheck. To verify changes: reload `index.html` in a browser and play through (rotate, thrust, shoot, die, level up, game over → restart with Space).
