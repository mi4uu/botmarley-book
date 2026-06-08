# Botmarley

Botmarley is a self-hosted cryptocurrency trading bot that runs on your own hardware. You write trading strategies, test them against historical data, and run them live on Kraken and Binance. No cloud service holds your keys, and nobody else touches your funds.

You install it, you run it, you own it.

## What you can do with it

- **Build strategies** in a visual editor or as raw TOML, with live validation.
- **Backtest** against historical candle data before you risk real money.
- **Trade live** on Kraken and Binance, with start, pause, and resume controls.
- **Track your portfolio** value over time in USD and BTC.
- **Rotate between assets** with momentum strategies like GEM (see [GEM / Dual Momentum](strategies/gem.md)).
- **Get notified** on Telegram when trades fire or sessions change state.

## How this manual is laid out

If you just want it running, start with [Installation](getting-started/installation.md) and [Quick Start](getting-started/quick-start.md). Want to see it first? There's a [live demo](demo.md) you can click around without installing anything.

From there, the [Strategies](strategies/editor.md) section covers how to define trading rules, the [Backtesting](backtesting/overview.md) section covers testing them, and [Live Trading](trading/overview.md) covers running them for real.

When you're ready to host it properly, [Production Deployment](deployment/production.md) and [Licensing & KeyMarley](deployment/licensing.md) cover the server side.

## A note on safety

Backtest results are not a promise of future returns. Test every strategy, start small, and never run money you can't afford to lose. Botmarley gives you the tools; the decisions are yours.
