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

## Next up (pick based on what you want most)
- [ ] Replace procedural graphics with real hand-drawn/pixel art
- [ ] Add a subtle bobbing/rocking animation to the canoe while idle
- [ ] Add paddle-splash particle effect when moving
- [ ] Add ambient sound (water, birds) and a paddle sound, plus a
      "thud" sound/flash when hitting the rock
- [ ] Make the lake shape irregular/natural instead of a rectangle
- [ ] Add fishing mechanic (cast line, wait, catch fish)
- [ ] Add more hazards/obstacles (more rocks, lily pads, other boats) —
      now easier to make accurate since hitboxes can be rotated rectangles
- [ ] Decide what happens at 0% health beyond restart (score? best time?)
- [ ] Tune Matter movement feel (currently direct velocity control, same
      snappy feel as the old Arcade setup — could add momentum/drift)
- [ ] Add a simple UI/HUD (e.g. fish caught counter, numeric health)
- [ ] Split single-file HTML into `/src` modules once complexity grows

## Notes / decisions log
- Chose Arcade Physics `collideWorldBounds` + inset world bounds over
  manual position-clamping — simpler and Phaser-native.
- Kept movement as direct velocity (not acceleration + drag) for MVP
  simplicity and predictability. Could revisit for a more "boat-like"
  momentum feel later.
