# Quick Start

This walks you from a fresh install to your first backtest in a few minutes. If you haven't installed yet, do that first ([Installation](installation.md)).

## 1. Download some market data

Open the **Market Data** page. Pick a pair (say BTC/USDC) and download recent history. Botmarley stores everything as 1-minute candles and builds longer timeframes from those, so you only ever fetch 1m.

## 2. Pick a strategy

Open the **Strategies** page. You'll see built-in and example strategies. Start with one of the examples, like the EMA crossover. You can read exactly what it does in the [Strategy Editor](../strategies/editor.md).

## 3. Run a backtest

From a strategy, hit **Backtest**. Choose the pair and date range, set your starting capital, and run it. You'll get back PnL, win rate, drawdown, an equity curve, and every trade it would have made. Details in [Backtesting](../backtesting/overview.md).

## 4. Go live (when you're ready)

Once a strategy holds up across different periods and pairs, you can run it live. Add your exchange API keys in settings, then start a live session with start, pause, and resume controls. See [Live Trading](../trading/overview.md).

Start small. A backtest that looks great can still lose money live if the strategy is overfitted.
