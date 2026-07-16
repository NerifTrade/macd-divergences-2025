# Pine Indicator Library

TradingView Pine Script indicator library.

This repository currently contains:

- `MACD-Divergences-2025.pine`
- `0800-Open-Cross.pine`
- `NERIF-MACD-v10-16-Relations.pine`

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

## 3. NERIF MACD v10 - 16 Relations + MACD Base

TradingView Pine Script indicator that combines a standard MACD display with cross-point relationship tracking.

## What This Indicator Does

`NERIF MACD v10 - with 16 Relations + MACD Base` plots the MACD base components and then tracks every MACD gold cross and dead cross. When a new cross happens, it compares the new cross point with the previous cross point and classifies the relationship into one of 16 possible relation labels.

The indicator can draw:

- MACD histogram
- MACD fast line
- Signal slow line
- Zero line
- Gold cross dot
- Dead cross dot
- Relation label between two cross events
- Optional connecting line between cross-event price points

This is useful when you want to study how price and MACD momentum behave from one cross event to the next.

## Core Logic

1. Calculate MACD:
   - Fast EMA: default `12`
   - Slow EMA: default `26`
   - Signal EMA: default `9`
   - Histogram: `macd - signal`

2. Detect cross type:
   - Gold cross: `ta.crossover(macd, signal)`
   - Dead cross: `ta.crossunder(macd, signal)`

3. Store previous cross data:
   - Previous cross type: `G` or `D`
   - Previous MACD value
   - Previous close price
   - Previous bar index

4. When a new cross appears:
   - Current cross type is converted to `G` or `D`
   - Previous type and current type form the relation group:
     - `GG`: gold cross to gold cross
     - `GD`: gold cross to dead cross
     - `DD`: dead cross to dead cross
     - `DG`: dead cross to gold cross

5. Compare MACD and close:
   - `macdHigher = currMacd > prevMacd`
   - `closeHigher = currClose > prevClose`
   - These two comparisons create four possible states inside each relation group.

## 16 Relation Labels

Each relation has two parts:

- The first two letters describe the cross sequence.
- The final four letters describe whether MACD and close are higher or lower compared with the previous cross point.

| Relation group | Meaning |
| --- | --- |
| `GG` | Previous cross was gold, current cross is gold |
| `GD` | Previous cross was gold, current cross is dead |
| `DD` | Previous cross was dead, current cross is dead |
| `DG` | Previous cross was dead, current cross is gold |

Inside each group:

| Suffix | Meaning in this script |
| --- | --- |
| `LHLH` | MACD is higher, close is higher |
| `LHHL` | MACD is higher, close is not higher |
| `HLHL` | MACD is not higher, close is not higher |
| `HLLH` | MACD is not higher, close is higher |

Example:

- `GG-LHLH` means the previous cross and current cross are both gold crosses, while current MACD and current close are both higher than the previous cross point.
- `GD-LHHL` means the previous cross was gold and the current cross is dead, while MACD is higher but close is not higher than the previous cross point.

## Visual Output

| Element | Meaning |
| --- | --- |
| Histogram | MACD histogram |
| Blue line | MACD fast line |
| Orange line | Signal slow line |
| Green dot | Gold cross |
| Red dot | Dead cross |
| Colored label | Classified cross-to-cross relation |
| Colored line | Optional line connecting previous and current cross price points |

The relation colors are configurable:

- `GG`: teal
- `GD`: orange
- `DD`: red
- `DG`: purple

## Important Notes

- This script is an indicator, not a strategy.
- It does not call `strategy.entry()`, `strategy.exit()`, or `strategy.close()`.
- It cannot produce TradingView Strategy Tester results unless converted into a `strategy()` script with explicit trading rules.
- The source file comments appear to have encoding issues, but the Pine logic itself is readable.
- The indicator stores and manages created lines and labels with arrays, keeping up to `300` objects to avoid overloading the chart.
- The script uses the current chart close price, not Heikin Ashi close unless the chart itself is set to Heikin Ashi.

## Practical Use

This indicator is useful for reviewing MACD cross structure:

- See whether each new gold/dead cross happens at a higher or lower MACD value.
- Compare whether price close confirms or contradicts the MACD movement.
- Use the 16 relation labels to classify market behavior between cross points.
- Combine the labels with trend filters, higher timeframe context, support/resistance, or your own entry rules before making trading decisions.

## File

- `NERIF-MACD-v10-16-Relations.pine`

## TradingView Usage

1. Open TradingView Pine Editor.
2. Paste the contents of `NERIF-MACD-v10-16-Relations.pine`.
3. Add it to chart.
