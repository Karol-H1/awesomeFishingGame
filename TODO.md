# TODO

## Done (MVP v1)
- [x] Project skeleton (Phaser 3 + JS, single-file HTML)
- [x] Procedurally-drawn lake background with land border + trees
- [x] Procedurally-drawn canoe + fisherman sprite
- [x] WASD free movement, normalized diagonal speed
- [x] Canoe rotates to face movement direction
- [x] Boat is confined to the water area (can't enter land)

## Done (v2)
- [x] Fisherman redrawn seated inside the hull silhouette
- [x] Clear front/back on the canoe: asymmetric hull + fisherman
      facing the bow
- [x] Sand beach band added between water and grass
- [x] Explicit hitbox on the boat, sized to the hull, with an H-key
      debug toggle to view it

## Done (v3)
- [x] Removed the paddle/rod stick jutting out of the boat
- [x] Floating health bar under the boat, capped to the boat's length,
      color-shifts as health drops
- [x] Rock hazard in the middle of the lake; colliding costs 5% health
      per hit (with a cooldown so resting on it doesn't melt health)
- [x] Boat now spawns off-center so it doesn't start on top of the rock

## Done (v4)
- [x] Boat "sinks" at 0 health: hull disappears (texture swap),
      fisherman remains floating in ripples
- [x] Controls disabled once sunk
- [x] Game-over message + press R to restart (full scene restart)

## Done (v5)
- [x] Migrated boat/rock physics from Arcade to Matter
- [x] Boat hitbox is now a true rotated rectangle (rotates with the sprite)
- [x] H-key debug view now draws the actual rotated hitbox polygon
- [x] Fixed velocity units for Matter (per-step, not per-second)

## Done (v6)
- [x] Hook mechanic: click to cast toward the cursor, auto-returns to
      the boat's current position; one hook active at a time; no range
      limit yet

## Done (v7)
- [x] Fish: up to 10 swimming at once, each circling a fixed point at
      a radius ~1/32 the lake's width, drawn as translucent underwater
      silhouettes
- [x] Catching: hook passing near a swimming fish snags it, turns it
      opaque, and immediately heads back to the boat with it
- [x] Fish population tops back up over time (capped at 10) as fish
      are caught and delivered
- [x] Fish-count HUD box in the top-left corner

## Done (v8)
- [x] Fixed fish texture facing backwards (head/tail were swapped
      relative to the direction of travel)
- [x] Top land border enlarged (50px -> 100px) to reserve HUD space
- [x] Shop box reserved in the top-right corner (no upgrades wired up yet)
- [x] Hook range limited to a fixed distance from the boat, with clicks
      beyond it clamped to the edge instead of ignored
- [x] Faint circle around the boat shows the current hook range

## Done (v9)
- [x] Halved hook range (250px -> 125px) — deliberately short so
      "increase hook range" has room to be a real upgrade later
- [x] Slowed boat speed (200 -> 140 -> 100 -> 75) and hook cast/return
      speed (500 -> 300px/sec), both deliberately modest for the same reason
- [x] Fixed bug: casting toward where the boat came from let the hook
      trail outside the range circle as the boat moved away, since the
      range was only checked once at cast time. Now checked every
      frame — the hook turns back the instant it hits the range edge.
- [x] Fish now render below the boat (explicit depth ordering) instead
      of on top of it when they overlap
- [x] Game now scales to fill the browser window (Phaser Scale Manager,
      FIT + CENTER_BOTH) instead of rendering at a fixed 800x600 px
      block. Internal resolution and all coordinates are unchanged;
      Phaser translates pointer input automatically.
- [x] FIT still letterboxed on wide monitors (pillarboxed to preserve
      4:3). Replaced it: WIDTH/HEIGHT are now set from the actual
      window size at load, so the world itself matches the screen's
      shape and genuinely fills it edge to edge, no bars. Sizing is
      still one-shot at load, not live-reactive to later window resizes.

## Done (v10)
- [x] Upgrade shop: row of labeled icon buttons along the top-right of
      the HUD, bought with caught fish. Three upgrades so far — Repair
      (restores full health, only when damaged), Boat Speed (+15px/sec,
      repeatable), Hook Range (+25px, repeatable, range circle updates
      immediately). Buttons dim when unaffordable/unavailable. Defined
      as a list of {cost, apply} entries so adding more later is a
      one-entry change, and the row stays right-aligned automatically.
- [x] Clicks anywhere in the top HUD strip no longer cast the hook,
      so clicking the shop can't also fire a cast.

## Done (v11)
- [x] Hook Speed upgrade (5 fish, +50px/sec, repeatable) — 4th shop
      button, HOOK_SPEED converted to mutable this.hookSpeed
- [x] Sharks: swim identically to fish (same texture/behavior, share
      the fish array via an isShark flag), capped at 2 concurrently
      (independent of the 10-fish cap), spawn timer every 30s. Catching
      one swaps it to a much larger red shark sprite; delivering it to
      the boat deals 20% damage and stuns the boat (locked controls,
      red tint) for 1 second — no fish awarded. hitRock()'s damage
      logic factored into a shared applyDamage() used by both.
- [x] Verified end-to-end via injected scene access: shark cap holds
      at 2, catch swaps texture/size correctly, delivery damages and
      stuns, boat position genuinely frozen during the stun window and
      resumes after, swimming sharks are pixel-identical to fish.

## Done (v12)
- [x] "Shark Attack" callout above the boat for the duration of the
      shark stun — shows/hides in lockstep with the same stun check
      already driving the tint, positioned relative to the boat each
      frame like the health bar.

## Next up (pick based on what you want most)
- [ ] More upgrades beyond the current four
- [ ] Add other players'/boats the hook can damage
- [ ] Replace procedural graphics with real hand-drawn/pixel art
- [ ] Add a subtle bobbing/rocking animation to the canoe while idle
- [ ] Add paddle-splash particle effect when moving
- [ ] Add ambient sound (water, birds) and a paddle sound, plus a
      "thud" sound/flash when hitting the rock
- [ ] Make the lake shape irregular/natural instead of a rectangle
- [ ] Add more hazards/obstacles (more rocks, lily pads, other boats) —
      now easier to make accurate since hitboxes can be rotated rectangles
- [ ] Decide what happens at 0% health beyond restart (score? best time?)
- [ ] Tune Matter movement feel (currently direct velocity control, same
      snappy feel as the old Arcade setup — could add momentum/drift)
- [ ] Add a numeric health readout to the HUD
- [ ] Split single-file HTML into `/src` modules once complexity grows

## Notes / decisions log
- Chose Arcade Physics `collideWorldBounds` + inset world bounds over
  manual position-clamping — simpler and Phaser-native.
- Kept movement as direct velocity (not acceleration + drag) for MVP
  simplicity and predictability. Could revisit for a more "boat-like"
  momentum feel later.
