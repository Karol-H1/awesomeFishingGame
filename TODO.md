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

## Done (v13)
- [x] Title screen (new `TitleScene`, runs before `MainScene`): game
      title, tagline, pulsing "Play" button, click or press-any-key to
      start. HTML hint bar hidden until gameplay actually begins.
- [x] Verified by serving the file over a local HTTP server rather
      than opening it directly — the file:// preview used throughout
      this session turned out to be a permanently cached snapshot for
      this path (state and scene never reset across "reloads"), which
      made the title screen impossible to observe until switched to
      a real served page.

## Done (v14)
- [x] Multiplayer (shared presence): Firebase Realtime Database syncs
      every connected player's boat position/rotation/health live, no
      server of our own to host. Random per-browser player ID
      persisted in localStorage; own boat writes to `/players/{id}`
      ~10x/sec, remote boats render from the same shared path (canoe
      sprite + health bar + a "Player" label, no physics body yet).
      onDisconnect() cleans up a player's entry if their tab closes.
      Fish/sharks/hooks/upgrades stay local per-player for now — this
      is the foundation the next feature (battling) builds on.
- [x] Verified with two simultaneous browser tabs (forced distinct
      player IDs via localStorage, since tabs on the same origin
      normally share it) served over the local HTTP server, same
      workaround as v13's file:// caching issue. Moving the boat in
      one tab showed up live as a labeled remote boat with an updating
      health bar in the other, in both directions.

## Done (v15)
- [x] Battling: hooking another player's boat instead of a fish deals
      10% damage and steals up to 2 of their fish. Attacker detects the
      hit client-side (their hook checking distance to each already-
      synced remote boat, same as the fish-catch check); one hit per
      cast, hook turns back immediately, nothing rides home on it.
      Victim's own fish count is now synced too (alongside position/
      health/sunk) purely so attackers know how much is worth stealing.
- [x] "Hits" are delivered via a per-victim Firebase inbox rather than
      the attacker writing directly to the victim's record — the
      attacker pushes `{fishStolen}`, the victim applies the damage/
      fish loss to their own authoritative state and shows "Ouch"
      above their boat, then deletes the processed entry. Keeps every
      player owning writes to their own state, and means a hit lands
      correctly even if the victim was offline at the moment of impact.
- [x] Verified live with two browser tabs acting as separate players:
      point-blank hit dealt exactly 10% damage; forcing the hit-detection
      tick directly confirmed the fish steal is exactly
      `min(2, victim's last-synced fish count)`; repeated hits (used
      while debugging) correctly stacked damage and drained the
      victim's fish count to exactly 0 without going negative; every
      hit set the victim's "Ouch" timer. Testing surfaced two quirks
      worth remembering for next time, neither of which is a bug in
      the feature itself:
      (1) a backgrounded/non-fronted browser tab in the preview tool
      stops ticking Phaser's update loop entirely (so its own periodic
      Firebase sync pauses) but *keeps processing incoming Firebase
      events*, since those ride the WebSocket connection rather than
      requestAnimationFrame — worth remembering when a "player" seems
      unresponsive during two-tab testing;
      (2) two tabs of the same browser truly share one `localStorage`,
      so setting a distinct player ID on one tab and then immediately
      switching to set a different ID on another, before the first has
      reloaded and locked its ID into memory, silently overwrites both
      to the same value — set-and-reload one tab fully before touching
      the next.

## Done (v16)
- [x] Spacebar auto-aim: casts the hook at the nearest swimming
      fish/shark or other player's (non-sunk) boat that's within hook
      range, doing nothing if none qualify. Refactored the click
      handler's cast logic into a shared `castHookToward()` so both
      input methods stay in sync automatically.
- [x] Verified by full code read-through rather than a live browser
      test — the Browser preview pane was hidden/non-composited
      client-side for this entire session (see the v15 note on this),
      so Phaser's loop never ran long enough to click through a game
      session. The new code is a thin wrapper reusing the already
      hand-tested cast/catch/hit-detection path, so risk is low, but
      worth an actual playtest before fully trusting it.

## Done (v17)
- [x] Mobile support: a touch device (`IS_TOUCH_DEVICE`, detected via
      `navigator.maxTouchPoints`/`ontouchstart`) gets a virtual joystick
      (bottom-left, direction only, not analog speed) and a hook button
      (bottom-right, casts via the same nearest-thing-in-range auto-aim
      as spacebar) instead of WASD/click. Both are Phaser objects drawn
      on the canvas, not HTML overlays. Desktop/mouse is unaffected —
      neither control exists there, and click-to-aim is now gated to
      `!IS_TOUCH_DEVICE` specifically so a stray tap while dragging the
      joystick can't also fire a cast.
- [x] Landscape-only: a full-screen "please rotate your device" prompt
      blocks portrait via a CSS media query scoped to touch-primary
      devices (`hover: none` + `pointer: coarse`), so a narrow desktop
      window is never affected. Since WIDTH/HEIGHT are only computed
      once at load, and a phone will almost always open the page in
      portrait first, rotating to landscape triggers a full page
      reload (rather than trying to live-resize the running game) so
      the world ends up the right shape.
- [x] Added a `<meta viewport>` tag and `touch-action: none` so mobile
      browsers don't pinch-zoom/scroll/pull-to-refresh during play.
      `activePointers: 3` added to the Phaser config so the joystick
      and hook button work as genuinely simultaneous touches.
- [x] Verified what this environment could actually verify: the
      desktop/mouse experience is unchanged (regression-tested live —
      WASD, click-to-cast, upgrade buttons all still work); the new
      joystick math (direction, magnitude clamping, deadzone, no
      divide-by-zero at dead center) and the hook button's wiring
      (no-op with nothing in range, casts correctly with something in
      range) were verified directly via the debug scene handle; the
      rotate-prompt overlay was confirmed to render and cover the
      screen correctly. **Not verified**: an actual touchscreen device
      or genuine touch/mobile emulation — this testing tool only
      resizes the viewport, it doesn't set `navigator.maxTouchPoints`
      or the touch media features, so `IS_TOUCH_DEVICE` and the
      rotate-prompt media query itself could never actually be
      triggered here. This needs a real phone (or real device-emulation
      dev tools) test before fully trusting it.

## Done (v18)
- [x] Fixed the v17 mobile layout looking oversized/constrained on a
      real phone (reported directly from a phone screenshot: joystick
      and hook button each ate roughly a third of the screen height,
      border+HUD left only a thin strip of visible lake). Root cause:
      every fixed-pixel size in the file (border thickness, buttons,
      joystick, hitboxes, hook reach...) was tuned looking at a normal
      desktop window, so on a much shorter phone-landscape screen the
      same absolute sizes ate a far bigger fraction of it.
- [x] Added `UI_SCALE` — computed once from `HEIGHT` against an 800px
      reference, capped at 1 so a normal desktop window is completely
      unaffected — and applied it to every spatial constant: border/
      beach thickness, hitboxes, hook range/catch radii, upgrade
      buttons and their internal icon/label/cost layout, the fish-count
      HUD box, joystick and hook button sizing, and the boat's spawn
      offset. Baked pixel art (boat/rock/fish/shark/hook textures) is
      deliberately left at full resolution — several have hand-tuned
      absolute offsets that would distort if scaled — and instead each
      sprite is shown via `.setScale(UI_SCALE)`, with the matching
      Matter physics body (hitbox, rock's collision circle) scaled by
      the same factor separately so the invisible hitbox still lines up.
      The lake background's pine-tree decoration needed its own fixed
      offsets (reach, spacing) scaled the same way, since a thinner
      border ring would otherwise make the trees overflow past it.
- [x] Verified live: at a genuinely short window height (844x390,
      matching a real phone's landscape resolution — this environment
      can control window size even without genuine touch emulation,
      and UI_SCALE only depends on height), border+HUD now occupy the
      same ~19% of screen height as they do on a normal desktop window
      (previously ~37% unscaled), movement/hook-casting still function
      correctly at the smaller scale, and the joystick/hook button
      (manually instantiated via the debug scene handle, since
      IS_TOUCH_DEVICE can't be forced here) render at a proportional,
      no-longer-dominating size. Border decoration (pine trees) checked
      visually for overflow at this size — none found. Normal desktop
      window height confirmed to produce UI_SCALE exactly 1 (byte-for-
      byte the original constants), so this shouldn't have touched the
      desktop experience at all.

## Done (v19)
- [x] Restart button: appears (and only responds to clicks/taps once
      actually game over) beneath the "Your boat sank!" message,
      alongside the existing R key — added because mobile players have
      no keyboard and couldn't restart at all after sinking. Works for
      everyone (mouse click or touch tap), not touch-gated like the
      joystick/hook button, since it's a plain convenience rather than
      a replacement for a desktop control.
- [x] Verified live: forced the boat to sink via the debug scene
      handle, confirmed the button renders in the right place beneath
      the message, and clicking it correctly restarts the scene (fresh
      health/position, fish pool refreshed, message/button hidden
      again). No console errors.

## Done (v20)
- [x] Fixed cross-device position sync (reported from a phone: "on
      mobile the player is restricted to the top left of the full lake
      compared to desktop"). Root cause: each player's lake is sized to
      their own window (a phone's ~796x317 vs a desktop's ~1180x750),
      but positions were synced as **absolute pixels** — so a phone
      player roaming their whole lake landed in the top-left corner of
      a desktop player's bigger one, and a desktop player was usually
      outside the phone's lake entirely and invisible. Positions now
      travel as a fraction of the lake (0..1 per axis) via
      `toLakeFraction()`/`fromLakeFraction()`, so "40% across, 60%
      down" means the same place for everyone regardless of screen size.
- [x] Hooks are now synced (`hookOut`/`hookNx`/`hookNy`): a cast in
      flight renders on every other player's screen as hook sprite plus
      rope line, same as the caster sees it. `sinkBoat()` explicitly
      clears `hookOut` so a hook caught mid-flight doesn't hang frozen
      on other screens forever. What's *on* the hook isn't synced —
      a remote hook carrying a fish/shark still looks bare.
- [x] Hint bar (WASD/click/H/R) hidden on touch devices — every control
      it lists is keyboard-or-mouse only, and it overlapped the lake on
      a short screen.
- [x] Verified live with two clients that happened to have genuinely
      different lake sizes (644x564 vs 796x317 — the phone-vs-desktop
      case exactly). Caster's hook at local pixel y=533 published as
      fraction 0.7908 and rendered on the other client at y=300, which
      is exactly `WATER_TOP + 0.7908 * LAKE_H` for that client; boat
      positions matched to the pixel the same way. Under the old
      scheme y=533 would have drawn at y=533 on a lake ending at
      y=366 — 167px below the water, invisible, which is the reported
      bug. Also confirmed the write path carries the hook (8 sampled
      syncs with `hookOut` true, tracking out and back), and confirmed
      visually that a remote boat renders its rope and hook.

## Next up (pick based on what you want most)
- [ ] Get real-phone confirmation that v18's scale fix, v19's restart
      button, and v20's cross-device sync + visible hooks all work in
      practice (this session could only simulate window dimensions and
      differing lake sizes, not genuine touch)
- [ ] Playtest v16 (spacebar) and v17 (mobile controls) on a real
      touch device — neither has had a genuine touch-input playtest yet
- [ ] Sync fish/sharks so all players share one pool instead of each
      having an independent copy
- [ ] Smooth/interpolate remote boat movement between network updates
      instead of snapping to the latest position
- [ ] Lock down the Firebase Realtime Database security rules (it's
      currently in open test mode) before sharing outside a small group
- [ ] More upgrades beyond the current four
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
