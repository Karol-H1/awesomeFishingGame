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
- Palette: blue water, green land, brown wooden canoe, small red-shirted
  fisherman.
- Land border includes small pine trees for texture/readability.

## Out of scope for MVP (future ideas)
- Fishing mechanic (casting, catching fish, a fish population/economy).
- Obstacles on the lake (rocks, other boats, lily pads).
- Day/night cycle or weather affecting water appearance.
- Sound effects (paddle splash, ambient lake sounds) and music.
- Camera/zoom, or a larger world with scrolling instead of a single screen.
- Proper lake-shaped collision (currently a rectangle inset from the
  screen edges, not a natural lake silhouette).
- Score, objectives, or a win condition.

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
