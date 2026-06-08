# Indicators Reference

Triggers compare indicators to values or to other indicators. Each indicator takes a period and runs on a timeframe you choose. On a 1-minute base, an indicator on the `1h` timeframe uses hourly candles built from the 1m data.

## Available indicators

| Name | TOML key | What it measures |
|------|----------|------------------|
| Simple Moving Average | `sma_N` | Average close over N periods |
| Exponential Moving Average | `ema_N` | Weighted average, more recent prices count more |
| Relative Strength Index | `rsi_N` | Momentum, 0–100, overbought/oversold |
| Bollinger Bands | `bb_*` | Volatility bands around a moving average |
| MACD | `macd` | Trend and momentum from two EMAs |
| On-Balance Volume | `obv` | Volume flow direction |
| OBV SMA | `obv_sma_N` | Smoothed OBV |
| TTM Trend | `ttm_trend` | Trend persistence |
| Stochastic RSI | `stoch_rsi` | RSI run through a stochastic oscillator |
| Rate of Change | `roc_N` | Momentum as a percentage move over N periods |
| Average True Range | `atr_N` | Volatility in price terms |
| Volume SMA | `vol_sma_N` | Smoothed volume |
| Price | `price` | The raw close price |

## Reading a key

`ema_21` is a 21-period EMA. On the `1h` timeframe that's 21 hours. `roc_90` is the percentage change over the last 90 periods, which is the momentum measure the [GEM rotation](gem.md) strategy ranks on.

## Operators

| Operator | Fires when |
|----------|-----------|
| `cross_above` | The indicator crosses up through the target |
| `cross_below` | The indicator crosses down through the target |
| `greater_than` | The indicator is above the target |
| `less_than` | The indicator is below the target |
| `equal` | The indicator equals the target |

`cross_above` and `cross_below` fire once on the crossing. `greater_than` and `less_than` are true for as long as the condition holds, so pair them with a `max_count` if you only want them to fire once.
