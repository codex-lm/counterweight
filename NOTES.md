# Delivery notes — Counterweight

## v1.1.0 (2026-10-31, evening) — "everything slides" fix

**Player feedback (Cédric, first session):** *it gets very hard quickly,
everything slides — maybe put things on the bar?*

The complaint was measured, not just felt. A beginner-level bot (completes
colour pairs, recovers past 18°) played three 3-minute games on v1.0.0:

| metric | v1.0.0 | v1.1.0 |
| --- | --- | --- |
| survival | 52–79 s (all defeats: hearts) | 70–97 s |
| median slide of a resting block | 25 px | 8 px |
| P90 slide | 92 px | 51 px |
| beam angle RMS (calm phases) | ~15° | 2–8° |

What changed (all in `SETTINGS` / `buildPlank`):

- **Cleats on the beam** — small fixed wooden stops every ~2.2 blocks plus one
  near each rim. Bays hold two columns; resting stacks get foundations and
  genuinely sleep. Loads parked *inside* bays can even re-balance themselves
  by sliding gently bay-to-bay; only edge-pinned loads keep their torque and
  tip the beam — which is the readable threat the game wants.
- **Softer balance**: wider pivot dead-zone (0.85), heavier beam (inertia
  ×1.25), more damping, and a ~15°/s angular speed cap — no more whiplash
  counter-swings after a big melt.
- **Columns shear later**: static friction 3.4 (was 1.8) — piles hold well
  past the 35° danger threshold instead of streaming off at 20°.
- Tutorial pair now sits inside the left bay; the target lands on it.

Verified: 22/22 Node tests against the shipped file (including new tests:
mechanism defeat at 36°/1.5 s, edge-load tilt threat, cleats holding a column
through a gentle tilt), the 6-minute bot sim (35 melts, 18 natural ×2
cascades, chain ×5, level 4), and a browser session where blocks visibly
settle into regular columns and sleep. Known trade-off: builds are now more
column-shaped than free-form; bridging across bays still works (bars span
cleats nicely).

## v1.0.0 (2026-10-31) — first release

Published as LOB7 edition `br_s_853f1fc5-0711-4210-9827-08ffb218700f`.
Original delivery notes below.

## What was really tested

**Automated, against the exact shipped `index.html`** (Node `vm` loads the
file's two scripts; the game core exports its API — the delivered file is the
tested file). 20 tests, all green:

- rules: 3-block melt +90 (0.4 s stability, not instant), 2 blocks never melt,
  cascade ×2 = 180 within 2 s, bar counts as 2 units, lead carried away +50;
- hearts: colour block −1, lead −2, game over at 0;
- balloon: anchors, pulls for 8 s, lifts off, never costs a heart, cleaned up;
- stability: a 12-block pile rests 10 s with < 3° drift, < 15 px displacement,
  no residual jitter (sleeping works) — no spontaneous collapse;
- defeat: a one-sided row load tips the plank past 35° → defeat by tilt in
  under 14 s; a 6-minute bot game (semi-random newcomer strategy) produced
  42 melts, 22 natural ×2 cascades (max chain ×6), level 5, defeat by tilt
  at 97 s — inside the intended 1.5–4 min first-game window;
- levels every 10 melts with plank shrink; auto-drop at 3.5 s (level 1);
  perfect-balance +200 with 10 resting blocks; best-score persistence; the
  game boots and runs with no localStorage at all; 300 physics steps with 40
  resting blocks stay well under a frame budget.

**In a real browser** (Chromium webview, 1024×700, `?debug=1`): home screen
and attract mode render (pixel-verified warm sky / timber / vivid blocks);
tutorial pre-places its pair and the first melt really happened (~9 s, under
the 15 s bar); keyboard play (hold →, Space); **real pointer events**
(drag to aim, release to drop); pause screen; FR→EN language switch;
game-over screen (reason, score, best, longest cascade) and **replay in
~2 ms**. Seven screenshots archived by the workshop.

**Not tested**: physical phone, physical gamepad, and the audio mix by ear
(all sounds are synthesized WebAudio; the automated webview mutes).

## Chosen settings

All values live in `SETTINGS` at the top of the game script. Key choices:

- The plank is **kinematic, driven by a real 1-DOF torque model** (resting
  blocks' weight × lever arm, block inertia included, 0.5 dead-zone pivot
  friction, 80/ω viscous damping, mechanical stop at 50°). A rigid Matter
  pivot constraint injected permanent micro-jitter into every pile; this way
  piles settle, sleep, and hold.
- Blocks slide only past ~40° (friction 0.85 / static 1.8), so the 35° tilt
  defeat is the readable threat; beyond it things slide off the lips, as
  visible on screen.
- Group contact is **geometric** (face-shared AABB overlap, 2.5 px gap
  tolerance, corner-diagonals rejected) because Matter skips collision
  detection between sleeping bodies — pair events would never fire for
  settled piles.
- Progression ramps reach the spec's "≈6 minutes" targets at level 6.

## Known limits

- The verification webview froze `requestAnimationFrame` (hidden pane), so
  browser time was pumped through the documented `?debug=1` hook `CWTICK`
  and menu buttons were clicked via DOM. Input handlers themselves are real
  keyboard/pointer events.
- The plank shortens at level-ups (10 melts each), not continuously.
- Balloons are capped at one active at a time; gusts affect only falling
  blocks, as specified.
- `?debug=1` exposes `CWDBG` (state probe) and `CWTICK` (manual frame pump)
  on purpose for automated verification.
- Total weight: ~220 KB (index.html 170 KB incl. Matter.js, cover 51 KB).

## Repo layout

`index.html` (the game — source and deliverable), `cover.png`, `README.md`,
`LICENSE`, `THIRD-PARTY-LICENSES.md`, `LOB7-GAME.json`,
`LOB7-ATTRIBUTION.json`, these notes.
