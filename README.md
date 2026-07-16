# Pine Indicator Library

TradingView Pine Script indicator library.

This repository currently contains:

- `MACD-Divergences-2025.pine`
- `0800-Open-Cross.pine`

These scripts are indicators, not automated trading strategies. They do not place orders, calculate position sizing, or produce TradingView Strategy Tester backtest results by themselves.

## 1. MACD Divergences 2025

TradingView Pine Script indicator for MACD divergence markers using Heikin Ashi close data.

## What This Indicator Does

`MACD Divergences 2025` is a TradingView Pine v5 overlay indicator that marks possible MACD divergence points directly on the price chart.

The script calculates a standard MACD line, signal line, and histogram, then compares the current Heikin Ashi close and MACD signal value against the previous qualified MACD trigger point. When the relationship between price movement and momentum movement disagrees, the script plots a marker on the chart.

This is an indicator, not a strategy. It does not place orders, calculate position size, or produce backtest performance by itself.

## Core Logic

1. Calculate MACD:
   - Fast moving average: default `12`
   - Slow moving average: default `26`
   - Signal smoothing: default `9`
   - MA type can be `EMA` or `SMA`

2. Load Heikin Ashi close:
   - Uses `ticker.heikinashi(syminfo.tickerid)`
   - Pulls the Heikin Ashi close from the current chart timeframe with `request.security()`

3. Detect bullish-side trigger:
   - MACD crosses above the signal line
   - Signal line is below `0`
   - The script compares the current Heikin Ashi close and signal value with the previous trigger point
   - A red cross is plotted when the divergence condition is met

4. Detect bearish-side trigger:
   - Signal line crosses above MACD
   - Signal line is above `0`
   - The script compares the current Heikin Ashi close and signal value with the previous trigger point
   - A blue circle is plotted when the divergence condition is met

## Marker Meaning

| Marker | Code condition | Meaning |
| --- | --- | --- |
| Red cross | `condnd1` | Possible divergence after MACD crosses above signal while signal is below zero |
| Blue circle | `hcondnd1` | Possible divergence after signal crosses above MACD while signal is above zero |

The markers are placed with `plotshape()` above the bar.

## Important Notes

- The script uses Heikin Ashi close data, so signals may differ from normal candle close data.
- The script uses `request.security()` on the Heikin Ashi ticker for the current timeframe.
- This version is Pine Script v5.
- The color inputs for MACD and histogram are defined, but the script currently only plots the divergence markers. It does not plot the MACD panel lines.
- Because this is an overlay indicator, the markers appear on the price chart instead of a separate oscillator panel.
- A marker is not a buy or sell order. It is only a visual signal that needs further confirmation.

## Practical Use

This indicator can be used to scan for possible momentum divergence around MACD cross events:

- Use the red cross as a warning that momentum and Heikin Ashi price movement may be diverging after a bullish MACD cross below the zero line.
- Use the blue circle as a warning that momentum and Heikin Ashi price movement may be diverging after a bearish MACD cross above the zero line.
- Combine the signal with trend filters, support/resistance, market structure, volume, or your own strategy rules before making trading decisions.

## File

- `MACD-Divergences-2025.pine`

## Notes

- Pine version: v5
- Script type: `indicator`
- Uses `request.security()` with `ticker.heikinashi(syminfo.tickerid)`
- Plots bullish and bearish divergence markers with `plotshape()`

## TradingView Usage

1. Open TradingView Pine Editor.
2. Paste the contents of `MACD-Divergences-2025.pine`.
3. Add it to chart.

## 2. 08:00 Open Cross

TradingView Pine Script indicator that marks the 08:00 candle based on Malaysia time (`Asia/Kuala_Lumpur`) and draws a cross reference on the chart.

## What This Indicator Does

`08:00 Open Cross` draws two lines when the chart reaches 08:00 Malaysia time:

- A horizontal line at the 08:00 candle open price.
- A vertical line at the 08:00 candle time.

The indicator deletes the previous pair of lines before drawing the newest pair. This means the chart only keeps the latest 08:00 cross reference instead of filling the chart with historical lines.

## Core Logic

1. Detect the 08:00 candle:
   - Uses `hour(time, "Asia/Kuala_Lumpur") == 8`
   - This checks the bar time using Malaysia time, which is UTC+8

2. Delete previous lines:
   - If the old horizontal line exists, delete it with `line.delete(h)`
   - If the old vertical line exists, delete it with `line.delete(v)`

3. Draw the new horizontal line:
   - Starts at the current bar index
   - Uses the current candle `open` price
   - Extends to the right side of the chart with `extend.right`

4. Draw the new vertical line:
   - Draws from the current bar low to high
   - Extends vertically both ways with `extend.both`

## Marker Meaning

| Line | Code object | Meaning |
| --- | --- | --- |
| Horizontal line | `h` | The 08:00 candle open price |
| Vertical line | `v` | The 08:00 time marker |

Both lines use the selected `Cross Color`, defaulting to orange.

## Important Notes

- The script depends on chart bars that include an 08:00 Malaysia time candle.
- On higher timeframes, the exact 08:00 bar may not exist or may represent a wider candle window.
- The script currently checks only `hour == 8`, not `minute == 0`. On intraday timeframes smaller than 1 hour, every bar from 08:00 to 08:59 can satisfy the condition. If you want only the exact 08:00 candle, add a minute check: `minute(time, "Asia/Kuala_Lumpur") == 0`.
- This is a visual reference indicator. It does not generate buy/sell orders or backtest results.

## Practical Use

This indicator can be used to track the market reaction around the Malaysia-time 08:00 open reference:

- Use the horizontal line as the key 08:00 open price level.
- Use the vertical line to quickly identify the 08:00 session point.
- Combine the level with breakout, rejection, liquidity sweep, or session-open rules if you want to build a full trading strategy later.

## File

- `0800-Open-Cross.pine`

## TradingView Usage

1. Open TradingView Pine Editor.
2. Paste the contents of `0800-Open-Cross.pine`.
3. Add it to chart.
