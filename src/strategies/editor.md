# Strategy Editor

A strategy is a set of rules that decide when to buy and when to sell. Botmarley stores each one as a TOML file, and you can edit it two ways: a visual builder, or the raw TOML with live validation.

## The shape of a strategy

Most strategies are **per-pair trigger** strategies. They watch one trading pair and fire actions when conditions are met. A strategy file has a `[meta]` block and one or more `[[actions]]`:

```toml
[meta]
name = "EMA(9) Cross Above EMA(21)"
description = "Classic trend-following entry with a small DCA grid, take profit at +2%"
max_open_positions = 1

# Entry: buy 100 USDC when the 9 EMA crosses above the 21 EMA on the 1h timeframe
[[actions]]
type = "open_long"
amount = "100 USDC"
  [[actions.triggers]]
  indicator = "ema_9"
  operator = "cross_above"
  target = "ema_21"
  timeframe = "1h"

# Take profit: sell the whole position at +2%
[[actions]]
type = "sell"
amount = "100%"
  [[actions.triggers]]
  type = "pos_price_change"
  value = "+2%"
```

## Actions

| Action | What it does |
|--------|--------------|
| `open_long` | Open a new position |
| `buy` | Add to an existing position (DCA) |
| `sell` | Close some or all of a position |

`amount` is either a fixed quote amount (`"100 USDC"`) or a percentage (`"100%"`).

## Triggers

A trigger is the condition that makes an action fire. The common one is **technical**: compare an indicator to a value or another indicator.

- `indicator` and `target` name the two sides (e.g. `ema_9` vs `ema_21`, or `rsi_14` vs a number).
- `operator` is `cross_above`, `cross_below`, `greater_than`, `less_than`, or `equal`.
- `timeframe` is the candle size the indicator runs on (`1m`, `1h`, `1d`, and so on).

There are also price-change triggers (move relative to entry) and time-based triggers (after N candles). The full indicator list is in the [Indicators Reference](indicators.md).

## Universal by design

A trigger strategy never names a specific pair. The same rules run against whatever pair you point them at, so you can backtest one strategy across BTC, ETH, and SOL without rewriting it.

## Rotation strategies

There's a second kind of strategy that ranks a whole basket of assets and rotates between them instead of trading one pair. That's covered separately in [GEM / Dual Momentum](gem.md).
