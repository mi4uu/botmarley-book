# Live Trading

Live trading runs a strategy against the real market and places real orders. The decision logic is identical to backtesting; the difference is that orders go to the exchange.

## Before you start

- Add your exchange API keys in settings. Use keys scoped to trading only, never withdrawal.
- Test the strategy in [backtesting](../backtesting/overview.md) first, across more than one period.
- Start with a small amount.

## Exchanges

| Exchange | Candles | Live orders |
|----------|---------|-------------|
| Binance | Yes | Yes |
| Kraken | Yes | Yes |

Both run through the same trading engine. The strategy doesn't know or care which exchange it's on, so the decisions are the same either way; only the order placement differs.

## Running a session

Start a session from a strategy. You get a live candlestick chart with trade markers, the current strategy state, and controls to pause and resume. Pausing stops new entries while leaving open positions alone.

## Notifications

Connect Telegram in settings to get a message when a trade fires or a session changes state. Handy when you're not watching the screen.

## Routing through a tunnel

If your exchange account is locked to a fixed IP, Botmarley can send its API calls through an SSH SOCKS5 tunnel so they always leave from the same address. See [Production Deployment](../deployment/production.md) for how that's set up.
