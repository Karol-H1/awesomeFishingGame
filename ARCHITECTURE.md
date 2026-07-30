# Architecture

## Stack
- **Phaser 3** (loaded via CDN, currently v3.70.0) — handles the game
  loop, rendering, input, and physics.
- **Matter Physics** (Phaser's Matter.js integration) — handles the
  boat's and rock's collision bodies. We migrated to this from
  Phaser's other physics system, Arcade Physics, specifically to get
  rotated rectangular hitboxes (see "Boat hitbox" below).
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
- **`create()`** places the background image, spawns the boat as a
  Matter Physics sprite, and — importantly — sets the **physics world
  bounds** to be inset from the canvas edges by the land-border
  thickness. Matter models this as static invisible walls around that
  inset rectangle, which is what stops the boat from ever driving onto
  land — no manual clamping code needed.
- **`update()`** reads WASD state every frame, computes a normalized
  velocity vector (so diagonal movement isn't faster than
  straight-line movement), applies it, and rotates the boat to face
  its direction of travel. It also redraws the hitbox debug outline
  when that view is toggled on.

## Boat hitbox
- `boat.setRectangle(HITBOX_W, HITBOX_H)` gives the boat a rectangular
  **Matter** body sized to the hull (constants at the top of the file,
  next to the texture size they're derived from, so they're easy to
  keep in sync if the art changes). Because Matter tracks body angle
  natively, this rectangle rotates together with the sprite — solving
  the limitation we had under Arcade Physics, where hitboxes were
  always axis-aligned regardless of how the sprite was rotated.
- `boat.setFixedRotation()` stops Matter's own physics (e.g. a
  glancing bump off the rock) from spinning the body on its own. We
  still fully control rotation ourselves each frame in `update()`
  (`boat.rotation = Math.atan2(vy, vx)`) — fixed rotation only blocks
  *physics-driven* spin, not our manual assignment.
- Press **H** at runtime to toggle a red outline showing the current
  hitbox. It's drawn from `boat.body.vertices`, which Matter keeps
  updated in world space as the body rotates — so the outline visibly
  turns with the boat, which is the whole point of this migration.
- One trade-off worth knowing: Matter velocity is expressed **per
  simulation step**, not per second like Arcade's. We divide our
  desired `BOAT_SPEED` (in px/sec) by 60 before calling
  `setVelocity()` to compensate — see the comment in `update()`.

## Health bar
- `boat.health` is a plain number property on the sprite (0-100), not
  a separate class — simplest thing that works at this scale.
- The bar itself is a `Graphics` object cleared and redrawn every
  frame in `drawHealthBar()`, positioned from `boat.x`/`boat.y` plus a
  fixed offset. It's deliberately **not** parented to the boat sprite
  or rotated with it, so it stays level and readable regardless of
  which way the boat is facing.
- Width is capped to `HEALTH_BAR_WIDTH` (currently reuses `HITBOX_W`)
  so it can never visually exceed the boat's own length, per the
  design constraint.

## Sinking / game over / restart
- `sinkBoat()` swaps the boat's texture to `sunkTexture` (fisherman +
  ripples, no hull) via `setTexture()`. This is why `createSunkTexture()`
  reuses the exact same canvas size and `seatX`/fisherman coordinates
  as `createCanoeTexture()` — same anchor, so the swap doesn't cause a
  visual jump. It's the cheapest way to fake "part of the sprite
  disappearing" without needing separate layered sprites.
- `this.gameOver` is a simple flag checked at the top of `update()`
  (early return) to disable movement/rotation once true. The
  hitbox-view and health-bar graphics are also cleared once at sink
  time rather than kept redrawing an irrelevant state.
- Restart uses Phaser's built-in `this.scene.restart()`, which reruns
  the full scene lifecycle (`preload` → `create`) as if starting fresh.
  This is why sinking doesn't need any manual "reset all the state"
  code — a restart just throws away the old scene and builds a new
  one from scratch.

## Rock hazard
- The rock is a **static** Matter body (`matter.add.image(...)` +
  `setCircle()` + `setStatic(true)`) — it never moves, so it needs no
  velocity or rotation handling, only a body to collide with.
- Its hitbox is a circle (`setCircle(ROCK_RADIUS)`), a good, cheap fit
  for a round rock — no need for a rotated body here since it never
  turns.
- We listen for `this.matter.world.on('collisionstart', ...)` and,
  inside the pair list, check whether the boat's and rock's bodies are
  both involved. `collisionstart` fires once when contact begins (not
  every physics step of overlap, unlike Arcade's `collider` callback),
  so it's naturally a good fit for "you just hit the rock" rather than
  continuous damage.
- Damage still has a cooldown (`HIT_COOLDOWN_MS`) as a safety net, in
  case the boat jitters against the rock and re-triggers
  `collisionstart` in quick succession.

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
