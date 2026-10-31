# Counterweight

A balancing-plank arcade puzzle. Drop coloured blocks from a gantry onto a
wooden beam that pivots freely on its trestle. Three or more blocks of one
colour touching for a moment melt away and lighten their side — which may
tip the other way. Lose all three hearts (blocks falling into the void) or
let the plank lean past 35° for more than 1.5 seconds and the game is over.

Self-contained: one `index.html`, no server, no external asset. Physics by
[Matter.js](https://brm.io/matter-js/) (v0.20.0, MIT, inlined with its
license notice; see `THIRD-PARTY-LICENSES.md`).

## Blocks

- **Square** — one coloured block, mass 1.
- **Bar** (from level 2) — two squares of one colour, rotate it before dropping.
- **Lead** (from level 3) — grey, mass 3, never melts on its own; it is carried
  away (+50) when a group melts while touching it.
- **Balloon** (from level 4) — anchored by a string, it pulls its side *up*
  for 8 seconds, then flies away.

## Rules

- A group of 3+ colour units touching for 0.4 s melts: **10 × n²** points.
- Each extra group within 2 s multiplies the chain: ×2, ×3, ×4…
- Perfect balance (under 2° with 10+ resting blocks): **+200**, then +50/s.
- One level every 10 melts: hesitation auto-drop shrinks 3.5 s → 1.2 s,
  colours go 3 → 5, leads appear (1 in 8), the plank shortens by ~20%,
  and from level 5 gusts of wind (announced 2 s ahead) drift falling blocks.

## Controls

| Action | Keyboard | Touch | Gamepad |
| --- | --- | --- | --- |
| Move the gantry | ← / → or A / D | drag | left stick |
| Rotate a bar | ↑ / W | second finger | X |
| Drop | ↓ / S / Space | release | A |
| Pause | P / Esc | ❚❚ button | Start |

Everything is readable without colour: each colour also carries its own
symbol (circle, triangle, square, diamond, cross). UI in English, French and
Spanish (auto-detected, switchable in the menus). A “reduce motion” option
disables screen shake and slow-motion effects.

## Play

Open `index.html` in any browser — that's the whole game. Add `?debug=1` to
the URL for an overlay (FPS, angle, torque, level) plus a few test hooks.

## Development

`index.html` is the source: the game script it contains is written to be
loaded and exercised directly (a Node `vm` harness spins the exact shipped
file). Every tuning value lives in the `SETTINGS` object at the top of the
script. The balance model is deliberate: the plank is a kinematic body whose
tilt integrates the real torque of resting blocks (weight × lever arm) with
pivot dead-zone friction and viscous damping — no rigid constraint jitter,
settled piles genuinely sleep and never collapse on their own.

## Credits & licence

- Concept: Cédric Bernadac, with Claude
- Game made by Z-Code
- Physics: Matter.js by Liam Brummitt (MIT)

MIT licence — see `LICENSE`. Delivery notes: `NOTES.md`.
