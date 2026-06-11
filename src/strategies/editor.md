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

## Volatility-scaled thresholds (×ATR)

A fixed `+2%` take-profit that works on calm BTC barely moves the needle on a pair that swings 8% a day, and the reverse over-trades. Instead of hand-tuning a percentage per pair, a price-change threshold can scale with **volatility** using ATR (Average True Range).

In the editor, every price-change value has a unit selector next to it:

- **%** — a plain fixed percentage, the classic behaviour.
- **×ATR** — the number you type is a multiplier `k`. The real threshold is `k × ATR(period) ÷ price`, so one strategy adapts itself across pairs and market regimes.

Picking ×ATR reveals an **ATR period** field (default 14) and a **recompute live** toggle. In TOML the value reads `k*atr_period`:

```toml
# Take profit at 0.6 × ATR(14) above entry — tighter on calm pairs, wider on wild ones
[[actions]]
type = "sell"
amount = "100%"
  [[actions.triggers]]
  type = "pos_price_change"
  value = "0.6*atr_14"
```

The sign of `k` sets direction (a DCA buy uses a negative multiplier, a take-profit a positive one), exactly like a `%` value. The ATR runs on the trigger's own **Timeframe**, so `atr_14` on a 1d trigger is the daily ATR.

**Frozen at entry (default).** The threshold is resolved once, when the position opens: `k × ATR(closed bars up to the entry tick) ÷ entry price`. It then stays fixed for the life of that position, so the target doesn't drift tick to tick. During warm-up — before there are enough closed bars to compute the ATR — the trigger stays inert and won't fire.

**Recompute live (opt-in).** Tick the *recompute live* box (TOML token `_live`, e.g. `-3*atr_14_live`) and the ATR is re-read at the current tick every time the trigger is checked, so a DCA or take-profit adapts to the current pace instead of the pace at entry.

You can mix units freely: a strategy can have a `%` entry and a `×ATR` exit, or different units on different triggers.

> **Tolerance sign matters.** On a *Follow* trigger (`pos_price_change_follow`), a profit-take (positive activation) needs a **negative** tolerance, and a dip-buy (negative activation) needs a **positive** one. Same-sign activation and tolerance disables the trailing behaviour — the action fires immediately on activation. The editor shows a warning when the signs disable the trail.

## Universal by design

A trigger strategy never names a specific pair. The same rules run against whatever pair you point them at, so you can backtest one strategy across BTC, ETH, and SOL without rewriting it.

## Universe: running on more than one pair

Every strategy can declare a **universe**: the set of pairs it trades. The editor shows this as a row of checkboxes above the actions.

- Leave it empty (or pick one pair) and the strategy is single-pair. You choose the pair when you backtest or go live.
- Pick two or more and the strategy goes multi-pair on a **shared cash pool**. One pool of capital scans every pair on a unified timeline, and whichever pair's signal fires first claims a free slot and the cash. It's first-come, not split evenly: a selective strategy can keep its full firepower idle and deploy it wherever the best setup appears.

`max_open_positions` caps the **total** open positions across all pairs. An optional **Max Per Pair** caps how many positions any single pair can hold at once, so one pair can't hog the whole pool. In TOML it's a `[universe]` block:

```toml
[universe]
pairs = ["BTC_USDC", "ETH_USDC", "BNB_USDC"]
max_per_pair = 1   # at most one position per pair; max_open_positions still bounds the total
```

The Max Per Pair field appears once you select two or more pairs. Multi-pair is first-class for every strategy, not a separate strategy type.

## Allocation: signals or rotation

The editor's **Allocation** section decides how capital moves across the universe:

- **Signals** (the default) runs the per-pair triggers you build in the Actions section.
- **Rotation** ignores triggers and instead ranks the universe by momentum, holding the single strongest pair. This is GEM / dual momentum, covered in [GEM / Dual Momentum](gem.md).

Switching Allocation to Rotation hides the Actions section and reveals the momentum settings. Both kinds of strategy live in this one editor and save to the same `[universe]` / `[allocation]` format.
