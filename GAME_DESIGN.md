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

## Hazards
- A rock sits in the middle of the lake. Colliding with it costs the
  boat 5% health, with a brief cooldown per hit so leaning on the rock
  doesn't drain health every single frame.
- The boat now spawns off-center (bottom-left area of the lake) rather
  than exactly at the rock's position.

## Player hitbox
- The boat has an explicit hitbox sized to the hull, ready for
  obstacle/collision work (now in use for the rock).
- Press **H** in-game to toggle a visual outline of the hitbox for
  debugging.
- Known limitation: Arcade Physics hitboxes are axis-aligned and don't
  rotate with the sprite. Fine for now; flagged in `ARCHITECTURE.md`.

## Out of scope for MVP (future ideas)
- Fishing mechanic (casting, catching fish, a fish population/economy).
- Obstacles on the lake (rocks, other boats, lily pads).
- Day/night cycle or weather affecting water appearance.
- Sound effects (paddle splash, ambient lake sounds) and music.
- Camera/zoom, or a larger world with scrolling instead of a single screen.
- Proper lake-shaped collision (currently a rectangle inset from the
  screen edges, not a natural lake silhouette).
- Score, objectives, or a win condition.
- More hazards (multiple rocks, moving obstacles), and what happens at
  0% health (currently nothing — the boat just stops taking damage).

## Controls
| Key | Action |
|-----|--------|
| W   | Move up |
| A   | Move left |
| S   | Move down |
| D   | Move right |

Diagonal movement (e.g. W+D) is supported and normalized so diagonal
speed matches straight-line speed.

## Screen / world size
- Canvas: 800x600
- Land border thickness: 50px on all sides
- Playable water area: 700x500, centered

This doc is the source of truth for scope. When we add a feature, it
should get a line here first so we don't lose track of what "done" means.
