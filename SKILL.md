---
name: rocket-launch
description: "Standalone HTML casino crash game 'Rocket Launch' for Stake Engine. Use this skill for ANY work on the rocket-launch repo: gameplay changes, UI polish, math tuning, adding mechanics, sound design, animations, bug fixes. Trigger on mentions of 'crash game', 'rocket launch html', 'казино html игра', 'stake game html', or any request to modify the standalone crash game prototype."
---

# Rocket Launch — Standalone HTML Edition

Single-file HTML5 casino crash game. Canvas rendering, Web Audio sounds, full math engine, Stake-style dark UI. Zero dependencies — opens in any browser.

## НАСТРОЙКА ОКРУЖЕНИЯ

```bash
cd /home/claude
git clone https://2211bogdan2012-blip:ghp_TOKEN@github.com/2211bogdan2012-blip/rocket-launch.git
cd rocket-launch
```

Для просмотра: `python3 -m http.server 3001` или просто открыть `index.html`.

## АРХИТЕКТУРА (single file: index.html)

```
index.html (~1200 lines)
├── CSS (~300 lines)
│   ├── Layout: CSS Grid (canvas + sidebar)
│   ├── Fonts: Orbitron (display) + JetBrains Mono (numbers) + Manrope (body)
│   ├── Theme: Stake-style dark (#0a0e17 bg, #00e701 green, #ff3b5c red, #ffd700 gold)
│   └── Components: top-bar, canvas-area, sidebar, bottom-bar
│
├── HTML (~120 lines)
│   ├── Top bar (logo + balance)
│   ├── Canvas area (game canvas + multiplier overlay + history + event banner + win popup)
│   └── Sidebar (mode tabs, bet input, action button, auto-cashout, auto-bet, stats)
│
└── JavaScript (~600 lines)
    ├── CONFIG — all constants (house edge, growth rate, modes, event weights)
    ├── MathEngine — crash point generation, event selection, round generation
    ├── SoundEngine — Web Audio synthesis (bet, launch, tick, cashout, crash, etc.)
    ├── GameRenderer — Canvas 2D (stars, rocket, trail, particles, obstacles, collectibles)
    ├── State — game state object
    ├── DOM — cached element references
    ├── UI functions — updateBalance, updateMultiplier, showEventBanner, etc.
    ├── Game logic — startRound, launchFlight, gameTick, checkEvents, cashOut, crash
    └── Render loop — requestAnimationFrame
```

## МАТЕМАТИЧЕСКАЯ МОДЕЛЬ

### RTP = 96%

Crash point: `max(1.00, floor(0.96 / random * 100) / 100)`
- ~4% instant crash (1.00×) = house edge
- Exponential distribution: E[1/crash] = 0.04

### Growth: e^(0.003 * tick), tick = 50ms

`multiplierToTick(m) = ln(m) / 0.003`
`tickToMultiplier(t) = e^(0.003 * t)`

### 5 механик:

| Mechanic       | Weight | RTP Share | Trigger Range | Effect              |
|---------------|--------|-----------|---------------|---------------------|
| base_crash    | 52.04% | 52%       | —             | Чистый crash        |
| mini_boss     | 11.74% | 11.7%     | 2.0×–8.0×     | +1.2×–2.5× bonus   |
| debris        | 8.00%  | 8%        | 1.5×–4.0×     | −20%–50% penalty   |
| boss_rage     | 7.93%  | 7.9%      | 3.0×–12.0×    | +1.5×–5.0× bonus   |
| golden_rocket | 16.00% | 16%       | 1.5×–6.0×     | +2.0×–10.0× bonus  |

### 4 bet modes:

| Mode           | Cost | Win Cap   |
|---------------|------|-----------|
| base          | 1×   | 5,000     |
| business_class| 10×  | 50,000    |
| vip_executive | 25×  | 125,000   |
| ceo_mode      | 100× | 500,000   |

## STAKE ENGINE СОВМЕСТИМОСТЬ

### ✅ Совместимо
- Stateless rounds — каждый раунд независим
- Pre-computed outcomes — результат ДО анимации
- RTP 96% — в пределах нормы
- Original theme — собственный IP
- No Stake branding

### ⚠️ CRITICAL: "Early cashout" вопрос
Stake Engine запрещает "early cashouts, progression systems". Наш cash out ВИЗУАЛЬНЫЙ (singleRoundWin: EndRound до анимации), но Stake review team может трактовать кнопку CASH OUT как "early cashout". Два варианта:
1. **Текущий** — cashout визуальный, объяснить review team что исход pre-computed
2. **Fallback** — убрать кнопку cashout, сделать "watch & win" (игрок видит результат)

### Для production Stake Engine нужно:
- Math books (100K jsonl.zst) через math-sdk Python
- Lookup tables (CSV)
- Svelte 5 + PixiJS 8 frontend (не vanilla HTML)
- stake-engine npm для RGS API

## ТЕКУЩИЙ СТАТУС

### Готово ✅
Math engine 96% RTP, Canvas рендеринг, Stake-style UI, 5 механик, 4 bet modes, Web Audio, auto-cashout, auto-bet, history badges, event banners, win popup, screen shake, particles, keyboard (Space), session P/L, countdown.

### TODO 🔧
- [ ] Multiplier graph/curve
- [ ] Better rocket visuals
- [ ] Turbo mode
- [ ] Mobile touch
- [ ] Bet presets
- [ ] Sound toggle
- [ ] Parallax starfield

## КРИТИЧЕСКИЕ ПРАВИЛА

1. **Один файл** — index.html, zero deps (кроме Google Fonts)
2. **CONFIG** — все константы там, не хардкодить
3. **RTP 96%** — `0.96 / Math.random()`, не менять
4. **Win cap** — `Math.min(win, cap)` ПОСЛЕ бонусов
5. **Git**: `git add -A && git commit -m "msg" && git push origin main`

## GIT

- GitHub: https://github.com/2211bogdan2012-blip/rocket-launch
- Branch: main
