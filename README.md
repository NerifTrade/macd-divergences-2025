# MACD Divergences 2025

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
