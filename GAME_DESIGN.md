# Lake Canoe — Game Design Doc

## Concept
A small top-down 2D game. The player controls a wooden canoe with a
fisherman aboard, paddling freely around a lake. The screen itself is
the boundary of the world — land rings the edge of the lake, keeping
the boat contained.

## Core mechanics (MVP)
- Player controls a canoe with **WASD**.
- Movement is free in any direction (not grid-based), at a constant speed.
- The canoe rotates to visually face the direction it's moving.
- The lake is the playable area; a land border around the screen edge
  is solid and cannot be entered.

## Visual style
- Simple, flat, hand-drawn-looking shapes (procedurally drawn, no
  external image assets yet).
- Palette: blue water, sand beach, green land, brown wooden canoe,
  small red-shirted fisherman, gray rock.
- Land border, from water outward: **beach** (sand) → **grass** (with
  pine trees near the outer edge).
- The canoe's front (bow) is always identifiable: the hull is
  asymmetric (sharper point at the bow), and the fisherman sits facing
  the bow (with a small nose nub confirming which way he's looking).

## Health
- Boat starts at 100 health (percentage-based).
- A health bar floats just below the boat at all times, sized to
  never exceed the boat's own length. It doesn't rotate with the boat
  — it stays level, which is the norm for readability in most games.
- Bar color shifts green → orange → red as health drops.

## Sinking / game over
- At 0 health, the boat "sinks": its texture swaps so the hull
  disappears, leaving just the fisherman floating in a couple of
  ripples. Controls are disabled at this point.
- A "Your boat sank! Press R to restart" message appears.
- Pressing **R** restarts the scene from scratch (fresh health,
  position, and rock state). This works at any time, not just after
  sinking, as a manual reset.

## Hazards
- A rock sits in the middle of the lake. Colliding with it costs the
  boat 5% health, with a brief cooldown per hit so leaning on the rock
  doesn't drain health every single frame.
- The boat now spawns off-center (bottom-left area of the lake) rather
  than exactly at the rock's position.

## Player hitbox
- The boat has a rectangular hitbox sized to the hull, running on
  **Matter Physics** — so it's a true rotated rectangle that turns
  together with the boat, not the flat axis-aligned box we had under
  the original Arcade Physics setup.
- Press **H** in-game to toggle a visual outline of the hitbox; you
  can watch it rotate with the boat in real time.

## Hook
- Click anywhere to cast the hook from the boat toward the clicked
  point. It travels out, then automatically returns to the boat's
  current position (which may have moved while the hook was out).
- Only one hook can be active at a time — clicking while it's out or
  returning does nothing.
- Range-limited: a cast can't reach further than a fixed distance from
  the boat. Clicking beyond that distance casts to the edge of the
  range in the direction clicked, rather than being ignored. A faint
  circle around the boat shows the current range, and moves with it.
- Catches fish (see below). Damaging other players' boats is not yet
  implemented.

## Fish
- Up to 10 fish swim in the lake at a time. Each fish is anchored to
  a fixed spawn point and continuously circles it, at a radius about
  1/32 the width of the lake.
- Fish are drawn as dark, translucent silhouettes so they read as
  shapes moving under the water's surface, not surface objects.
- Catching: while the hook is travelling out toward a click, if it
  passes within a small radius of a swimming fish, that fish is
  caught — it stops circling, turns fully opaque and warm-colored
  ("comes above water"), and the hook immediately turns around,
  carrying the fish back to the boat. Only one fish can be hooked per
  cast.
- Once delivered to the boat, the fish is removed and the player's
  fish count (see HUD below) goes up by one. A repeating timer spawns
  a replacement fish (respecting the 10-fish cap) so the lake's
  population recovers over time.

## HUD
- The top land border is taller than the other three sides
  specifically to hold the HUD.
- A small box in the top-left corner shows "Fish: N" — the running
  count of fish the player has caught.
- A wider box in the top-right corner reserves space for an upgrade
  shop (spend caught fish on upgrades). It's currently just an empty
  labeled box — no upgrades exist yet.

## Out of scope for MVP (future ideas)
- Upgrade shop: actual upgrades purchasable with caught fish, and the
  buying interaction itself (the box is reserved, nothing is wired up).
  Increasing hook range is the first planned upgrade — the range is
  intentionally short right now (125px) so there's room to grow.
- Other players'/boats to damage with the hook (multiplayer or AI).
- Obstacles on the lake (rocks, other boats, lily pads).
- Day/night cycle or weather affecting water appearance.
- Sound effects (paddle splash, ambient lake sounds) and music.
- Camera/zoom, or a larger world with scrolling instead of a single screen.
- Proper lake-shaped collision (currently a rectangle inset from the
  screen edges, not a natural lake silhouette).
- Score, objectives, or a proper win condition.
- More hazards (multiple rocks, moving obstacles).
- Anything beyond "sink and restart" at 0% health (e.g. a death
  animation, a delay before the restart prompt appears, a score/best-
  time tracker).

## Controls
| Key | Action |
|-----|--------|
| W   | Move up |
| A   | Move left |
| S   | Move down |
| D   | Move right |
| Click | Cast the hook toward the clicked point |
| H   | Toggle hitbox debug view |
| R   | Restart the game |

Diagonal movement (e.g. W+D) is supported and normalized so diagonal
speed matches straight-line speed. Controls are disabled once the
boat has sunk (health hits 0) until the player restarts.

## Screen / world size
- Canvas: 800x600
- Land border thickness: 50px on left/right/bottom, 100px on top
  (reserved for the HUD)
- Playable water area: 700x450, offset toward the bottom of the canvas

This doc is the source of truth for scope. When we add a feature, it
should get a line here first so we don't lose track of what "done" means.
