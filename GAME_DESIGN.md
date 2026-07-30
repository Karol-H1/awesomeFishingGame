# Awesome Fishing Game — Game Design Doc

## Concept
A small top-down 2D game. The player controls a wooden canoe with a
fisherman aboard, paddling freely around a lake. The screen itself is
the boundary of the world — land rings the edge of the lake, keeping
the boat contained.

## Title screen
- The game boots into a `TitleScene` rather than straight into
  gameplay: a water-blue background with the same ripple styling as
  the lake, the game title, a one-line tagline, and a pulsing "Play"
  button.
- Starting the game: click Play, or press any key. Either transitions
  to `MainScene` (`this.scene.start('MainScene')`).
- The HTML hint bar at the bottom of the screen (control reminders)
  stays hidden until `MainScene` starts, since it describes gameplay
  that doesn't apply yet on the title screen.
- A **Controls** button beneath Play opens a dismissable "How to Play"
  overlay — tap anywhere outside the panel, or its Close button, to
  dismiss. Its contents are picked per device (`IS_TOUCH_DEVICE`):
  desktop sees WASD/Click/Space/H/R, touch sees the joystick and hook
  button instead, since neither set of instructions is useful on the
  other. While the panel is open, both the Play button and "press any
  key to start" are disabled (`this.controlsOpen` guards both), so
  reading the controls can't accidentally start the game out from
  under the player.

## Core mechanics (MVP)
- Player controls a canoe with **WASD**.
- Movement is free in any direction (not grid-based), at a constant speed.
  Starts deliberately modest — "Boat Speed" is a purchasable upgrade
  (see Upgrade shop below).
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
- A "Your boat sank! Press R or tap below to restart" message appears,
  along with a **Restart** button beneath it.
- Pressing **R** restarts the scene from scratch (fresh health,
  position, and rock state). This works at any time, not just after
  sinking, as a manual reset. The Restart button only appears (and
  only responds to taps/clicks) once actually sunk — it exists mainly
  for touch devices, since R has no touch equivalent, but works for
  anyone.

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
  Cast/return speed starts deliberately modest — "Hook Speed" is a
  purchasable upgrade (see Upgrade shop below).
- Only one hook can be active at a time — clicking while it's out or
  returning does nothing.
- Range-limited: a cast can't reach further than a fixed distance from
  the boat. Clicking beyond that distance casts to the edge of the
  range in the direction clicked, rather than being ignored. A faint
  circle around the boat shows the current range, and moves with it.
  Starts deliberately short — "Hook Range" is a purchasable upgrade
  (see Upgrade shop below).
- The range is enforced continuously, not just at cast time: if the
  boat moves away while the hook is out (e.g. casting toward where
  the boat just came from), the hook turns back the instant it hits
  the edge of the range circle, rather than being able to trail
  outside it.
- Catches fish and sharks (see below), and can hit other players'
  boats (see Battling below).
- **Spacebar** casts too, but auto-aims: it targets whichever swimming
  fish/shark or other player's (non-sunk) boat is nearest the player's
  own boat, among only those already within hook range. If nothing
  qualifies, pressing it does nothing. Both input methods share the
  same underlying cast — spacebar just picks the target for you
  instead of needing a precise click.

## Fish
- Up to 10 fish swim in the lake at a time. Each fish is anchored to
  a fixed spawn point and continuously circles it, at a radius about
  1/32 the width of the lake.
- Fish are drawn as dark, translucent silhouettes so they read as
  shapes moving under the water's surface, not surface objects.
- Rendered below the boat, so a fish swimming under the boat is
  partially hidden by it rather than drawing on top.
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

## Sharks
- A trap disguised as a fish: while swimming, a shark is visually and
  behaviorally identical to a regular fish (same silhouette, same
  circling motion) — there's no way to tell them apart until one is
  caught.
- Up to 2 sharks can be out at once, independent of the 10-fish cap.
  A timer tries to spawn one every 30 seconds if under that cap.
- Catching one reveals it: it swaps to a much larger red shark sprite
  (rather than just turning opaque like a regular fish) and rides the
  hook back to the boat the same way.
- Delivery is a punishment, not a reward: when the shark sprite
  reaches the boat, it deals 20% damage and stuns the boat (controls
  locked, boat tinted red) for 1 second. No fish is added to the count.
- While stunned, a "Shark Attack" callout appears above the boat, for
  exactly as long as the stun lasts.

## HUD
- The top land border is taller than the other three sides
  specifically to hold the HUD.
- A small box in the top-left corner shows "Fish: N" — the running
  count of fish the player has caught.

## Upgrade shop
- A row of icon buttons along the top-right of the HUD, bought with
  caught fish. Each button shows an icon, a label, and its fish cost,
  and dims when it can't currently be bought (not enough fish, or —
  for Repair specifically — the boat is already at full health).
- Current upgrades:
  - **Repair** (3 fish): restores the boat to full health. Only
    purchasable when damaged.
  - **Boat Speed** (5 fish): permanently raises boat speed by 15px/sec.
    Repeatable, no cap.
  - **Hook Range** (5 fish): permanently raises the hook's max cast
    range by 25px. Repeatable, no cap; the faint range circle grows
    to match immediately.
  - **Hook Speed** (5 fish): permanently raises hook cast/return speed
    by 50px/sec. Repeatable, no cap.
- The row is right-aligned and lays itself out from however many
  upgrades are defined, so adding more later doesn't need repositioning.
- Clicking anywhere in the top HUD strip (not just on a button) never
  casts the hook, so clicks on the shop can't accidentally do both.

## Multiplayer
- Every player who loads the game shares the same lake and sees every
  other connected player's boat moving in real time — position,
  rotation, and health, synced through Firebase Realtime Database (a
  free hosted service; no server of our own to run or deploy).
- Each browser generates a random player ID on first load and remembers
  it in `localStorage`, so refreshing the page keeps controlling the
  same boat rather than spawning a new one. Your own boat writes its
  state (`nx`, `ny`, `rotation`, `health`, `fishCount`, `sunk`,
  `hookOut`, `hookNx`, `hookNy`) to `/players/{yourId}` about 10
  times/sec; every other player's boat is rendered directly from that
  same shared path.
- **Positions travel as a fraction of the lake (0..1 per axis), not as
  pixels.** Each player's lake is sized to their own window, so a
  phone's is far smaller than a desktop's — a phone lake might be
  796x317 where a desktop's is 1180x750. Sent as raw pixels, a phone
  player roaming their entire lake would appear squashed into the
  top-left corner of a desktop player's larger one, and the desktop
  player would frequently sit outside the phone's lake entirely and
  vanish. Sent as a fraction, "40% across, 60% down" means the same
  spot in the lake for everyone, whatever size their screen is. Each
  client converts to and from its own pixel space on send/receive
  (`toLakeFraction()` / `fromLakeFraction()`).
- Trade-off of that approach: since lakes differ in *aspect ratio* too,
  not just size, relative distances aren't perfectly identical between
  a phone and a desktop player (something dead-centre reads the same to
  both, but "two boat-lengths to the left" doesn't map exactly). The
  alternative — one fixed logical world scaled to fit every screen —
  would be exact, but reintroduces letterbox bars, which was
  specifically rejected earlier (see Screen / world size).
- Remote boats are plain visuals for now (no physics body, can't be
  bumped into) — a canoe sprite, a floating health bar identical in
  style to your own, and a small "Player" label so testers can tell
  remote boats apart from their own (which has no label). A remote
  boat's texture swaps to the sunk sprite once it reports `sunk: true`,
  the same way the local boat does.
- **Hooks are synced too**: a cast in flight is drawn on every other
  player's screen exactly as it is on the caster's — hook sprite plus
  the rope line back to their boat — so you can see an opponent
  reaching for a fish, or for you. A hook is hidden once its owner
  reports `hookOut: false` or sinks (`sinkBoat()` explicitly clears
  `hookOut`, or a hook caught mid-flight would hang frozen on other
  screens forever). What's *on* the hook isn't synced yet — a remote
  hook dragging a fish or shark home looks like a bare hook.
- If a player's tab closes or loses connection, Firebase's
  `onDisconnect()` removes their entry automatically, and their boat
  disappears for everyone else. Restarting your own boat (R, or a
  fresh sink) cleans up and re-registers the same player ID rather than
  leaving a duplicate behind.
- Fish and sharks are **not** synced — every player still has their
  own independent lake population. Fish *count* is synced (see
  Battling below), purely so opponents know how much they stand to
  steal.
- The Firebase project's Realtime Database is currently running in
  test mode (open read/write, no auth) — fine for a small prototype
  among friends, but worth locking down with real security rules
  before sharing the game publicly.

## Battling
- Hooking another player's boat (instead of a fish) deals 10% damage
  and steals up to 2 of their caught fish — piracy, not fishing.
- Detection is entirely client-side on the attacker: since every
  player's boat position is already synced (see Multiplayer above),
  the attacker's own hook checks its distance to each remote boat the
  same way it checks for fish, and turns back the instant it's close
  enough to one — one hit per cast, same as catching a fish, and nothing
  rides back on the hook (a hit is a tag, not a catch).
- Ownership stays with the victim: rather than the attacker directly
  editing the victim's health/fish count in the shared database (which
  would fight with the victim's own periodic self-sync), the attacker
  drops a message in the victim's personal "hits" inbox saying how many
  fish it's taking. The victim applies the damage and fish loss to
  their own state the next time they're connected, and shows the
  "Ouch" callout above their own boat — the same "you own your own
  data" model as everything else each player syncs about themself. A
  sunk boat can't be hit (no health or fish left to take).
- The fish-steal amount is decided from the victim's last-known synced
  fish count, since the attacker only has that, not real-time access
  to the victim's true count — a small window for imprecision under
  simultaneous multi-attacker edge cases, acceptable for a casual game
  rather than something worth a full request/response handshake over.
- If the victim is offline when hit, the message just waits in their
  inbox and gets applied automatically next time they reconnect.

## Mobile / touch controls
- Detected once at load (`IS_TOUCH_DEVICE`, based on
  `navigator.maxTouchPoints`/`ontouchstart`) — a touch device gets a
  virtual joystick and a hook button in place of WASD and click-to-aim;
  a mouse/desktop browser sees neither and plays exactly as before.
- **Joystick** (bottom-left): a translucent base ring plus a stick that
  follows the drag, clamped to the base's radius. Only provides a
  *direction*, the same way WASD does — dragging further doesn't move
  the boat any faster, it's not an analog throttle. A small deadzone
  near the center avoids jitter from an almost-still thumb. Supports
  one active touch at a time; a second finger on the joystick's own
  base is ignored (tracked by that first touch's pointer id) until the
  first lets go.
- **Hook button** (bottom-right): casts using the same nearest-thing-
  in-range auto-aim as the spacebar (see Hook above) — there's no
  precise "point and click" equivalent on a touchscreen, so the button
  just fishes at whatever's closest, fish/shark/other-player boat
  alike. Does nothing if nothing's in range.
- Both are real Phaser objects drawn on the canvas at a fixed screen
  position (same convention as the rest of the HUD), not HTML/CSS
  elements, and support simultaneous multi-touch (joystick held down
  while tapping the hook button) via `activePointers: 3` in the Phaser
  config.
- **Landscape only**: a full-screen "please rotate your device"
  overlay blocks play in portrait, shown via a CSS media query that
  only matches touch-primary devices (`hover: none` and
  `pointer: coarse`), so a narrow desktop browser window never
  triggers it. Since WIDTH/HEIGHT are captured once at load (see
  Screen / world size below) and a phone will almost always load the
  page in portrait first, the page reloads itself the moment the
  device is actually rotated to landscape, rather than trying to
  live-resize the already-running game with the wrong dimensions.
- **Scaling for small screens**: every fixed-pixel "size" constant
  (border thickness, hitboxes, hook reach, buttons, joystick, font
  sizes...) was originally tuned looking at a normal desktop browser
  window. A phone in landscape is much shorter, so without any
  correction those same fixed sizes eat a far bigger fraction of the
  smaller screen — border/HUD crowd out the lake, joystick and buttons
  read as oversized. `UI_SCALE` (computed once from `HEIGHT` against an
  800px reference, capped at 1) scales all of those down together so a
  short screen keeps the same *proportions* a normal desktop window
  already has, rather than the same absolute pixel sizes. Capping at 1
  means a normal-height desktop window is completely unaffected.
- The baked pixel art itself (boat, rock, fish, shark, hook — anything
  `generateTexture()`s a hand-drawn shape) is deliberately **not**
  regenerated at a smaller resolution; several of those have hand-tuned
  absolute offsets internally (e.g. the canoe hull's points) that would
  distort if the drawing math were scaled but the offsets weren't. Instead
  each sprite is displayed via `.setScale(UI_SCALE)`, which shrinks the
  already-correct art uniformly with zero risk of warping it, and the
  matching Matter physics body (hitbox rectangle, rock's collision
  circle) is separately sized by the same factor so the (invisible)
  collision area still lines up with the now-smaller sprite.
- The one exception is the lake background itself (border/beach/grass,
  baked once at load into a full-screen texture) — border thickness has
  to actually change for a shorter screen to show more lake, not just
  look smaller, so `BORDER`/`TOP_BORDER`/`BEACH` are scaled directly.
  The pine-tree decoration drawn into that ring uses fixed pixel offsets
  of its own (how far a tree reaches, its spacing) that had to be scaled
  the same way, otherwise the trees would overflow past a thinner ring
  into the beach at small sizes.

## Out of scope for MVP (future ideas)
- More upgrades beyond the four above.
- Increasing cost per purchase (upgrades are currently a flat price
  every time, not scaling with how many times you've bought them).
- Syncing fish/sharks so all players compete for the same shared pool
  instead of independent copies.
- Syncing what's *on* a remote hook (a caught fish/shark riding it home
  currently shows as a bare hook to other players).
- A single fixed logical world scaled to every screen, so relative
  distances are identical for every player regardless of their screen's
  aspect ratio (see the trade-off noted under Multiplayer).
- Smoothing/interpolating remote boat movement between network updates
  instead of snapping directly to the latest reported position.
- A more authoritative/anti-cheat-resistant battling model (currently
  trusts the attacker's client to report an honest steal amount).
- Live-resizing/re-orienting an already-running game instead of
  reloading (see Mobile / touch controls above).
- Bigger/repositionable touch targets for the upgrade shop specifically
  (currently unchanged from desktop — they're already comfortably
  above typical minimum touch-target sizes, but haven't been tuned for
  a small phone screen specifically).
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
| Click | Cast the hook toward the clicked point — catches fish, or hits another player's boat if it's closer |
| Space | Cast the hook at the nearest catchable thing in range (fish/shark/player) automatically |
| H   | Toggle hitbox debug view |
| R   | Restart the game |
| Restart button | Same as R — click/tap it after sinking to restart |

Diagonal movement (e.g. W+D) is supported and normalized so diagonal
speed matches straight-line speed. Controls are disabled once the
boat has sunk (health hits 0) until the player restarts.

On a touch device, the joystick (bottom-left) and hook button
(bottom-right) replace WASD and Click respectively — see Mobile /
touch controls above. Space and H have no touch equivalent (H is a
debug convenience); R's job is covered instead by the Restart button
that appears after sinking (see Sinking / game over above), which
works by tap or click for anyone, not just touch devices. The HTML
hint bar listing these keys is hidden entirely on touch devices, since
every control it names is keyboard-or-mouse only and it otherwise
overlaps the lake on a short screen.

## Screen / world size
- Game resolution matches the actual browser window size at load time
  (`window.innerWidth`/`innerHeight`), not a fixed canvas. It genuinely
  fills the screen — no letterbox bars, no cropping — because the
  lake, land border, HUD, and every spawn position are already
  computed from WIDTH/HEIGHT rather than hardcoded, so the whole world
  scales to whatever window it loads into.
- Land border thickness: 50px on left/right/bottom, 100px on top
  (reserved for the HUD) — fixed pixel thickness regardless of window size.
- Trade-off: sizing happens once at load. Resizing the browser window
  afterward doesn't live-resize the game; reloading the page does. On
  a touch device that loaded in portrait, rotating to landscape
  triggers exactly that reload automatically (see Mobile / touch
  controls above) so this trade-off doesn't leave the game stuck at
  the wrong shape.

## Backend
- Firebase Realtime Database (free tier), used purely for player-state
  sync — see Multiplayer above. Config lives inline in `index.html`;
  the API key is a client identifier, not a secret, so it's fine to
  ship in the page source, but the database's security rules should be
  tightened before this goes out beyond a small group of friends.

This doc is the source of truth for scope. When we add a feature, it
should get a line here first so we don't lose track of what "done" means.
