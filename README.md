# 🚀 Rocket Launch

**Standalone crash casino game in a single HTML file.** Zero dependencies, opens in any browser.

<p align="center">
  <strong>Stake-style dark UI • 96% RTP • 5 game mechanics • Canvas 2D rendering</strong>
</p>

## Play

```bash
open index.html
# or
python3 -m http.server 3001
```

## Game Mechanics

🎰 **Base Crash** (52%) — Multiplier grows, cash out before it crashes  
⚔️ **Mini-Boss** (11.7%) — Enemy spawns, 1.2×–2.5× bonus  
☄️ **Debris** (8%) — Asteroid field, −20%–50% penalty  
🔥 **Boss Rage** (7.9%) — Epic boss, 1.5×–5.0× bonus  
🚀 **Golden Rocket** (16%) — Gold transform, 2.0×–10.0× bonus  

## Bet Modes

| Mode | Cost | Win Cap |
|------|------|---------|
| Base | 1× | $5,000 |
| Business Class | 10× | $50,000 |
| VIP Executive | 25× | $125,000 |
| CEO Mode | 100× | $500,000 |

## Controls

- **Space** — Bet / Cash out
- Auto-cashout at target multiplier
- Auto-bet for continuous play

## Tech

Single HTML file (~1200 lines): Canvas 2D, Web Audio synthesis, CSS Grid, Google Fonts (Orbitron + JetBrains Mono). Provably fair math: `crashPoint = 0.96 / random()` → **96% RTP**.

---

Built for [Stake Engine](https://engine.stake.com) by NEXTUP MEDIA / Flame Generation LLC.
