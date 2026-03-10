---
name: rocket-ride
description: "Casino crash game 'Rocket Ride' for Stake Engine by HypeGames. Single-file HTML5 prototype (index.html, ~2740 lines). Use for ANY work on the rocket-ride repo. Next major task: evolve into 'Rocket Ride: Triple Launch' — multi-lane crash game with 1-3 simultaneous rockets, unique mechanic for Stake Engine submission."
---

# Rocket Ride — Session Handoff & Game Design

## QUICK START

```bash
cd /home/claude
git clone https://github.com/2211bogdan2012-blip/rocket-ride.git
cd rocket-ride
# Open index.html in browser — zero dependencies
```

**Repo:** https://github.com/2211bogdan2012-blip/rocket-ride
**Branch:** main
**Latest commit:** `1fb835f` — bulletproof tooltip fix
**File:** `index.html` (~2740 lines, single file, zero deps except Google Fonts)
**Studio:** HypeGames

---

## CURRENT STATE (v1.1) — FULLY WORKING PROTOTYPE

### Architecture (single index.html)
```
CSS (~450 lines)
  Grid layout (canvas 1fr + sidebar 280px), responsive @900px breakpoint
  Orbitron / JetBrains Mono / Manrope fonts
  Stake dark theme (--bg-primary: #0a0e17, --accent-green: #00e701)
  Global tooltip (position:fixed, z-index:9999)
  Rules modal, toggle buttons, stat rows

HTML (~180 lines)
  top-bar (logo, sound toggle, balance)
  canvas-area (canvas, multiplier overlay, event banner, win popup, round result)
  history-bar (badge elements with data-tip, global tooltip div)
  sidebar (mode tabs, bet input, presets, cashout input, auto/turbo buttons, stats)
  bottom-bar (RTP tag, provably fair tag, seed display, info button)
  rules-modal (full game rules)

JS (~2100 lines)
  CONFIG          — all constants, modes, event weights
  MathEngine      — crash point (96% RTP), event selection, round generation, provably fair seed
  SoundEngine     — Web Audio synthesis (9 sounds: bet, launch, tick, cashout, crash, miniboss, golden, debris, win)
  GameRenderer    — Canvas 2D class
    _renderStars()        — parallax starfield, diagonal streaks, speed lines
    _renderAmbientComets()— 3 shooting stars per 2s cycle (all rounds)
    _renderGraph()        — sliding window multiplier curve, grid, labels, event markers, auto-cashout line
    _renderRocket()       — 22px rocket with angle lerp, golden variant
    _renderObstacles()    — UFO bosses (purple dome), flaming skulls (horns+fire aura), fiery asteroids (7-layer flames, lava cracks)
    _renderCollectibles() — spinning gold coins (3D flip, $ symbol)
    _renderParticles()    — exhaust trail + explosions (capped at 300)
    addExplosion()        — multi-phase with particle cap
    addTrailParticle()    — directional exhaust from rocket tail
  State           — phase, tick, multiplier, balance, history, streak, etc.
  DOM             — cached getElementById refs (28 elements)
  UI functions    — updateBalance, updateMultiplier, updateActionButton, updateStats, showEventBanner, showWinPopup, showRoundResult, addHistory, updateStreakIndicator
  Game logic      — handleAction, startRound, launchFlight (with FAILED LAUNCH), gameTick, checkEvents, cashOut, crash
  Global tooltip  — mouseenter/leave event delegation on historyBar
  Keyboard        — Space (bet/cashout), 1-5 (presets), Q (auto), T (turbo), S (sound), M (max)
  Init            — renderLoop, updateIdleDisplay, rules modal, seed display
```

### Math Engine (VERIFIED — 96.00% RTP)
```
Crash point:     max(1.00, floor(0.96 / Math.random() * 100) / 100)
Growth curve:    e^(0.06 * tick * 0.05) = e^(0.003 * tick)
Tick interval:   50ms
House edge:      4% (P(crash=1.00) = P(r >= 0.96) ≈ 4-5%)

FAILED LAUNCH:   crashTick < 15 (~8.5% of rounds)
  → Player never gets cashout button → always loss
  → Shows "FAILED LAUNCH" text + centered explosion
  → RTP unaffected (verified at all cashout targets 1.05x-50x)

Events:          VISUAL ONLY — no payout impact
  base_crash:    56.29% weight
  mini_boss:     11.74% (trigger if crashPoint >= 2.0)
  debris:        8.00%  (trigger if crashPoint >= 1.5)
  boss_rage:     7.97%  (trigger if crashPoint >= 3.0)
  golden_rocket: 16.00% (trigger if crashPoint >= 1.5)
  Sum = 1.0000

Payout:          totalCost x cashoutMultiplier, capped at winCap
  totalCost = betAmount x modeMultiplier

Modes:
  base:           1x  cost, $5,000 cap
  business_class: 10x cost, $50,000 cap
  vip_executive:  25x cost, $125,000 cap
  ceo_mode:       100x cost, $500,000 cap

Provably fair:   32-char hex seed per round (crypto.getRandomValues)
                 Displayed in footer, click to copy, shown in history tooltips
```

### Completed Features
- Math: 96.00% RTP, verified 500K rounds across all modes and targets
- Canvas: graph with sliding window, rocket with angle lerp, parallax stars
- Visuals: UFO bosses, flaming skulls, fiery asteroids, gold coins, ambient comets
- UI: Stake dark theme, sidebar with mode tabs/bet/presets/cashout/auto/turbo/stats
- Sound: Web Audio synthesis, 9 sounds, pitch scaling with multiplier
- History: color-coded badges, global tooltip (position:fixed), streak indicator
- Auto-cashout with validation (min 1.01, gold border)
- Auto-bet with balance safety check
- Failed Launch mechanic for instant crashes
- Hotkeys (Space, 1-5, Q, T, S, M)
- Provably fair seed per round
- Game rules modal
- Performance: cached maxMult O(1), particle cap 300
- Event markers on graph (off-by-one fixed)
- Mobile responsive (900px breakpoint)

---

## NEXT MAJOR TASK: ROCKET RIDE — TRIPLE LAUNCH

### Why Not Pure Crash Clone
Stake already has Crash (Stake Original, RTP 99%, top-5 most played).
Submitting another crash game = rejection (cannibalizes their own product).
Stake Engine accepts "slots, wheel-based, card-based, or something completely original."
Multi-lane crash is a NEW MECHANIC that doesn't exist anywhere.

### Core Concept
- 1-3 rockets launch simultaneously, each with INDEPENDENT crash points
- Player distributes their bet across rockets (or goes all-in on one)
- Each rocket has its own CASHOUT button
- Each rocket crashes independently
- Visual: 3 parallel curves on one graph, 3 colored rockets
- Player selects rocket count (1/2/3) before each round

### Why 3 Max (Not Unlimited)
- UI: each rocket needs BET + CASHOUT + amount input. 5+ = unreadable on mobile
- Cognitive: player can't track 5+ curves and make split-second cashout decisions
- Industry precedent: Aviator = 2 bets max. We do 3 = more than anyone, still usable
- Dual bet (Aviator-style) is standard and explicitly allowed in iGaming

### Game Modes
```
1 Rocket  = Classic mode (like standard crash)
2 Rockets = Hedge mode (safe + aggressive)
3 Rockets = Portfolio mode (diversified strategy)
```

### Math Design
```
Each rocket: independent crash point from same formula (0.96/r)
RTP per rocket = 96%
Combined RTP = 96% (independent events, no correlation)

Total bet = sum of all rocket bets
Each rocket: payout = that rocket's bet x its cashout multiplier
Win cap per rocket (not total)

Example:
  Player puts $5 on Rocket A, $3 on Rocket B, $2 on Rocket C
  Rocket A crashes at 1.5x (player cashed out at 1.4x -> win $7)
  Rocket B crashes at 8.2x (player cashed out at 5.0x -> win $15)
  Rocket C crashes at 1.0x (failed launch -> loss $2)
  Net: spent $10, returned $22, profit $12
```

### UI Layout (Desktop)
```
+----------------------------------+-----------+
| TOP BAR: Logo | Balance | Sound  |           |
+----------------------------------+           |
|                                  | SIDEBAR   |
|   CANVAS: 3 curves + rockets    |           |
|   History badges at top          | [1][2][3] | <- rocket count
|                                  |           |
|   Multiplier overlays:           | Rocket A  |
|   A: 2.34x  B: 1.87x  C: 5.12x | $[5] [CO] |
|                                  |           |
|                                  | Rocket B  |
|                                  | $[3] [CO] |
|                                  |           |
|                                  | Rocket C  |
|                                  | $[2] [CO] |
|                                  |           |
|                                  | [LAUNCH]  |
|                                  | Stats     |
+----------------------------------+           |
| BOTTOM BAR: RTP | Seed | Rules   |           |
+----------------------------------+-----------+
```

### Rocket Visual Differentiation
```
Rocket A (blue):   trail = cyan particles,    curve = #3b82f6
Rocket B (orange): trail = orange particles,  curve = #f97316
Rocket C (purple): trail = purple particles,  curve = #a855f7
Each crashed rocket = explosion in its color
Active rocket = bright, crashed = dimmed/greyed
```

### Implementation Plan
```
Phase 1: Multi-rocket math engine
  - generateRound() returns array of {crashPoint, crashTick, seed} per rocket
  - Each rocket independent RNG
  - Verify: 96% RTP per rocket across 500K sim

Phase 2: State refactor
  - State.rockets = [{betAmount, crashPoint, crashTick, cashedOut, cashedOutAt, phase}]
  - State.rocketCount = 1|2|3
  - Remove old single-rocket State fields

Phase 3: Sidebar UI
  - Rocket count selector [1][2][3] at top
  - Per-rocket panels: bet input + 1/2/2x buttons + cashout input + cashout button
  - Single LAUNCH button starts all rockets
  - Individual cashout per rocket

Phase 4: Renderer overhaul
  - 3 curves on one graph (color-coded, different line styles)
  - 3 rockets at different positions on their curves
  - Per-rocket particles/trails in rocket color
  - Multiplier labels per rocket (color-coded)

Phase 5: Game logic refactor
  - gameTick updates all active rockets
  - Each rocket can be cashed out independently
  - Round ends when ALL rockets are cashed out or crashed
  - FAILED LAUNCH per-rocket (some may fail, others fly)

Phase 6: Auto-cashout per rocket, advanced auto-bet
  - Per-rocket auto-cashout targets
  - Martingale / Anti-Martingale per rocket
  - Stop on profit / stop on loss (session-level)

Phase 7: Polish
  - 1/2 and 2x buttons per rocket bet
  - Provably fair verification UI (reveal seeds after round)
  - Sound differentiation per rocket
  - Win/loss summary per round (table: Rocket A won, B crashed, C won)

Phase 8: Stake Engine considerations
  - singleRoundWin pattern: one round = all rockets resolved
  - Total bet = sum of rocket bets (single debit)
  - Total win = sum of rocket payouts (single credit)
  - Math books: pre-compute 100K rounds with N rocket crash points each
```

### Features To Also Add (from competitive analysis)
- 1/2 and 2x buttons next to each bet input (Stake standard)
- Advanced auto-bet: increase on win/loss %, stop on profit/loss, number of bets
- Provably fair verification panel (server seed revealed after round)
- Expanded crash history (table view, not just badges)

### Stake Engine Submission Checklist
- [ ] Original mechanic (multi-lane crash — unique IP)
- [ ] RTP certified (96% per rocket, independently verified)
- [ ] Provably fair (server seed + client seed + nonce)
- [ ] Game rules document
- [ ] Responsive (desktop + mobile)
- [ ] No Stake branding conflicts
- [ ] singleRoundWin pattern (stateless rounds)
- [ ] 10% GGR commercial model understood
- [ ] 24-hour approval process ready

---

## GIT & DEPLOYMENT

```bash
# Working directory after clone
cd /home/claude/rocket-ride

# Standard commit
git add -A && git commit -m "description" && git push origin main

# Output for user
cp index.html /mnt/user-data/outputs/rocket-ride.html
# If outputs dir broken: cp to /mnt/user-data/out2/ first
```

### Commit History (latest first)
```
1fb835f fix: bulletproof tooltip (position:fixed, JS positioned, never clipped)
21979c7 feat: final polish — hotkeys, provably fair, rules, comets, drift fix
11ffb7e fix: 4 red bugs + 4 yellow optimizations from deep audit
667dc2b fix: MATH + UI overhaul (events visual-only, normalized weights, mode payout fix)
3c3a8fe feat: polish pass — streaks, tooltips, crash anim, mobile, round counter
e995dfc CRITICAL: fix math + add launch sequence + simplify debris
```

## RULES FOR DEVELOPMENT

1. Single file `index.html`, zero external dependencies (Google Fonts only)
2. All constants in `CONFIG` object at top of JS
3. RTP 96% — `max(1.00, floor(0.96 / Math.random() * 100) / 100)` — DON'T CHANGE
4. Events are VISUAL ONLY — never affect payout
5. FAILED LAUNCH for crashTick < 15 — no cashout possible
6. Particle cap: 300 max
7. maxMult cached (reset on new round)
8. Git push after every meaningful change
9. Test math changes with 500K+ round simulation before committing
10. Verify JS syntax: `new Function(scriptContent)` before pushing
