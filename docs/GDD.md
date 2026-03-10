# Rocket Launch — Game Design Document

## Концепция

Crash-hybrid iGaming игра. Игрок запускает ракету, множитель растёт экспоненциально, нужно нажать CASH OUT до краша. По пути — 5 уникальных механик (мини-боссы, дебрис, золотая ракета и т.д.).

## Целевая аудитория

- Игроки Stake.com, Duelbits, Roobet (18+)
- Любители crash-игр (Aviator, Spaceman, Limbo)
- Высокие ставки: CEO Mode × 100

## Отличия от стандартного Crash

1. **5 механик** — каждый раунд уникален
2. **4 bet modes** — от casual (1×) до whale (100×)
3. **Visual events** — мини-боссы, дебрис, золотая трансформация

## Flow раунда

```
[IDLE] → Bet → [COUNTDOWN 3..2..1] → [FLYING]
                                        ↓
                              Multiplier: e^(0.003t)
                              Events trigger at thresholds
                                        ↓
                        ┌─ CASH OUT → [WIN] → payout
                        └─ Crash hit → [CRASHED] → loss
```

## Монетизация

House edge = 4%. На 100K раундов × $10 avg:
- Wagered: $1,000,000
- GGR: $40,000 (4%)
- RTP: $960,000 (96%)
