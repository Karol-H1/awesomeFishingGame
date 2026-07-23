# Architecture

## Stack
- **Phaser 3** (loaded via CDN, currently v3.70.0) — handles the game
  loop, rendering, input, and Arcade Physics.
- **Vanilla JavaScript** — no build step required. Everything currently
  lives in a single `index.html` for simplicity while the project is
  small.

## File structure (current)
```
index.html          - The entire game: HTML shell + Phaser game code
GAME_DESIGN.md       - What the game is and what's in/out of scope
ARCHITECTURE.md      - This file
TODO.md              - Running list of planned work
README.md            - How to run the project
```

As the game grows, the plan is to split `index.html`'s inline script
into separate files, e.g.:
```
index.html
/src
  main.js            - Phaser config + game bootstrap
  scenes/
    MainScene.js
  entities/
    Boat.js
  assets/            - Real art/audio assets, once we add them
```
This split isn't needed yet — a single file is easier to review and
run for an MVP this size. We'll do the split once there's more than
one scene, or once we bring in real image/audio assets and need a
bundler (e.g. Vite) to serve them.

## How the current code works
- **`MainScene`** is the only Phaser Scene.
- **`preload()`** doesn't load any image files. Instead it procedurally
  draws the lake background and the canoe sprite using
  `Phaser.GameObjects.Graphics`, then bakes each into a texture with
  `generateTexture()`. This keeps the MVP asset-free while still
  looking intentional. Swapping these for hand-drawn art later just
  means loading images in `preload()` instead — the rest of the code
  doesn't change.
- **`create()`** places the background image, spawns the boat as an
  Arcade Physics sprite, and — importantly — sets the **physics world
  bounds** to be inset from the canvas edges by the land-border
  thickness. This is what stops the boat from ever driving onto land:
  `setCollideWorldBounds(true)` on the boat plus the inset world
  bounds does the work, no manual clamping code needed.
- **`update()`** reads WASD state every frame, computes a normalized
  velocity vector (so diagonal movement isn't faster than
  straight-line movement), applies it, and rotates the boat to face
  its direction of travel. It also redraws the hitbox debug outline
  when that view is toggled on.

## Boat hitbox
- `boat.body` (an Arcade Physics body) is resized and offset with
  `setSize()`/`setOffset()` to match just the hull, not the paddle
  overhang that extends past the bow. Constants for this live at the
  top of the file (`HITBOX_W`, `HITBOX_H`, `HITBOX_OFFSET_*`) next to
  the texture size they're derived from, so they're easy to keep in
  sync if the art changes.
- **Important limitation:** Arcade Physics bodies are always
  axis-aligned (AABB) — they do not rotate with the sprite. Right now
  this doesn't matter (nothing to collide with yet), but once
  obstacles are added, a diagonally-facing boat will have a hitbox
  wider than its art suggests. Options if that becomes a problem:
  switch to Matter.js physics (supports rotated bodies) or approximate
  with a smaller circular hitbox instead of a rectangle.
- Press **H** at runtime to toggle a red outline showing the current
  hitbox — useful while tuning size/offset or once collectibles/
  obstacles are added.

## Lake background layering
`createLakeTexture()` builds the shoreline from the outside in:
grass (with trees near the outer edge) → sand beach ring → water. Each
layer is just a filled rect drawn after the previous one, sized a bit
smaller each time, so later layers naturally paint over the earlier
ones without needing a "ring" shape. `BORDER` is the full grass+beach
depth (used for the physics water bounds); `BEACH` is how much of that
is sand.

## Adding a new mechanic — suggested pattern
1. Add a line to `GAME_DESIGN.md` describing the mechanic and its scope.
2. If it's a new visual, add a `createXTexture()` method (following the
   existing pattern) or load a real asset if we've moved to using art.
3. If it's a new behavior, wire it into `update()` or, once the file
   gets large, split it into its own class/module.
4. Move the item from `TODO.md` to done, and note anything deferred.
