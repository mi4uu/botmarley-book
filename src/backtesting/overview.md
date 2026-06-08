# Backtesting

A backtest runs your strategy against historical candles and reports what would have happened. It's the cheapest way to find out whether an idea has legs before you put money behind it.

## Running one

From any strategy, choose **Backtest**, then set:

- **Pair** to test against (the strategy itself is pair-agnostic).
- **Date range** within the data you've downloaded.
- **Starting capital** in USD.

Run it and you get PnL, win rate, drawdown, an equity curve, and the full list of trades with the trigger that caused each one.

## How close is it to live trading?

Botmarley works hard to make backtest results match live behaviour, because a backtest you can't trust is worse than none. The same evaluation and portfolio code runs in both, and the backtest executor models the things that move real fills:

- **Fees** are deducted per trade, not bolted on at the end. The default is 0.1% (Binance spot taker).
- **Lot-size filters** (minimum quantity and step size) match the real exchange, so a backtest can't fill an order the exchange would reject.
- **Cross-pair pricing** is handled, so a pair quoted in something other than USD (for example an ETH-quoted pair) values correctly.
- **Slippage** is off by default, so fills land at the candle close. You can switch it on with the `BACKTEST_SLIPPAGE_BPS` environment variable when you want a more conservative result.

## What a backtest can't tell you

It uses 1-minute candles, so it doesn't see what happened inside each minute. It assumes your order filled at the modelled price. And a strategy tuned to look perfect on past data often falls apart on new data. Test across several periods and several pairs, and treat a single great run with suspicion.
