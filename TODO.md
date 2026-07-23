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
- [x] Clear front/back on the canoe: asymmetric hull, fisherman facing
      the bow, paddle extending forward
- [x] Sand beach band added between water and grass
- [x] Explicit hitbox on the boat, sized to the hull (not the paddle),
      with an H-key debug toggle to view it

## Next up (pick based on what you want most)
- [ ] Replace procedural graphics with real hand-drawn/pixel art
- [ ] Add a subtle bobbing/rocking animation to the canoe while idle
- [ ] Add paddle-splash particle effect when moving
- [ ] Add ambient sound (water, birds) and a paddle sound
- [ ] Make the lake shape irregular/natural instead of a rectangle
- [ ] Add fishing mechanic (cast line, wait, catch fish)
- [ ] Add obstacles (rocks, lily pads, other boats) and wire up actual
      collision now that the boat has a hitbox
- [ ] Revisit hitbox approach (AABB vs. rotated/circular) once
      obstacles exist and diagonal facing starts to matter
- [ ] Add a simple UI/HUD (e.g. fish caught counter)
- [ ] Split single-file HTML into `/src` modules once complexity grows

## Notes / decisions log
- Chose Arcade Physics `collideWorldBounds` + inset world bounds over
  manual position-clamping — simpler and Phaser-native.
- Kept movement as direct velocity (not acceleration + drag) for MVP
  simplicity and predictability. Could revisit for a more "boat-like"
  momentum feel later.
