# GEM / Dual Momentum

Most strategies in Botmarley trade a single pair on triggers. A **rotation** strategy is different: it watches a basket of assets, ranks them by momentum, and holds the strongest one. When nothing is trending up, it sits in cash.

This is the idea behind Gary Antonacci's GEM (Global Equities Momentum), adapted to crypto. It's called *dual* momentum because it uses two rules together.

## The two rules

**Relative momentum.** Each rebalance, measure how much each asset has moved over a lookback window, and hold the one that moved the most. You're always in the strongest horse.

**Absolute momentum.** Before committing, check that the winner is actually rising. If even the best asset in the basket is falling, hold cash instead. This is the rule that keeps you out of broad downturns, where every asset is red and "the best of a bad bunch" still loses money.

Together: hold the strongest asset, but only if it's genuinely going up.

## Defining one

A rotation strategy uses a `[rotation]` table instead of `[[actions]]`:

```toml
[meta]
name = "GEM Dual Momentum"
description = "Rank BTC/ETH/SOL, hold the strongest, sit in cash when none is trending up."

[rotation]
# Assets to rank and rotate between.
universe = ["BTC_USDC", "ETH_USDC", "SOL_USDC"]

# Momentum lookback in 1m candles. 1440 = one day.
momentum_period = 1440

# Rebalance cadence in 1m candles. 1440 = once per day ("lazy").
rebalance_every = 1440

# Absolute-momentum gate, as a percentage. The winner must beat this or
# the strategy holds cash. 0.0 is classic GEM.
min_momentum = 0.0
```

## The settings

| Field | What it does |
|-------|--------------|
| `universe` | The assets to rank. Two or more. |
| `momentum_period` | Lookback for the momentum measure, in 1m candles. Bigger = slower, less twitchy. |
| `rebalance_every` | How often to re-rank and possibly switch, in 1m candles. |
| `min_momentum` | The absolute gate, in percent. Raise it to demand stronger trends before buying in. |

Momentum is the rate of change over `momentum_period`: how far the price has moved as a percentage. The same measure the rest of Botmarley calls ROC.

## How it trades

At each rebalance point, Botmarley ranks the universe by momentum. If the top asset clears the `min_momentum` gate and it's not what you already hold, it sells the current holding and buys the new winner. If nothing clears the gate, it sells to cash and waits. Between rebalances it does nothing, which is the point. GEM is a low-turnover, "lazy" strategy. A daily rebalance on crypto is a reasonable starting point.

## Backtest it first

Rotation strategies backtest the same way as any other. Run one across a couple of different periods before trusting it. GEM on equities has historically delivered steady single-digit-to-low-double-digit annual returns with shallower drawdowns than buy-and-hold, but crypto is a different, far more volatile market. Treat the equity numbers as inspiration, not a forecast, and let your own backtests tell you what to expect here.

## Status

Rotation strategies run in backtesting today. Live multi-asset execution is on the roadmap.
