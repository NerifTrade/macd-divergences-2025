# Pine Indicator Library

TradingView Pine Script indicator library.

This repository currently contains:

- `MACD-Divergences-2025.pine`
- `0800-Open-Cross.pine`
- `NERIF-MACD-v10-16-Relations.pine`
- `Upside-Strength-Exhaustion-v1.1.pine`
- `Volume-Recent-vs-Previous-v1.0.pine`
- `MA-16.pine`
- `Heikin-ma50.pine`
- `Large-Pullback-Risk-Model-BTC-4H-Tuned.pine`
- `Wyckoff-JOC-Jump-Over-Creek.pine`

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

## 4. Upside Strength Exhaustion v1.1

TradingView Pine v6 overlay indicator for detecting possible upside strength exhaustion.

## What This Indicator Does

`Upside Strength Exhaustion v1.1` tries to identify when price is already in the upper area of its recent range, but the quality of the upside move is weakening.

It combines several conditions into an exhaustion score:

- Price position near the high area of the lookback range.
- Recent volume weaker than previous volume.
- Recent upside efficiency weaker than previous upside efficiency.
- Optional MACD histogram weakness.
- Optional RSI weakness.
- Optional upper-wick rejection.

The indicator then marks the chart with three signal levels: watch, strong exhaustion, and critical exhaustion.

## Core Logic

1. Calculate recent price range:
   - Highest high over `lookbackHighLength`
   - Lowest low over `lookbackHighLength`
   - Price position inside that high-low range
   - Price is considered high when it is above the configured `priceHighThreshold`

2. Measure volume weakness:
   - Recent average volume over `compareWindow`
   - Previous average volume over the prior `compareWindow`
   - `volumeRatio = recentVolumeAverage / previousVolumeAverage`
   - Volume is weak when the ratio is below `volumeWeakThreshold`

3. Measure upside efficiency:
   - Recent upside move is compared with the previous upside move
   - Each upside move is divided by its average volume
   - Efficiency is weak when recent upside efficiency is below the configured `efficiencyWeakThreshold`

4. Optional weakness filters:
   - MACD weakness: MACD histogram is falling
   - RSI weakness: RSI is falling
   - Upper-wick weakness: upper wick is at least `35%` of the candle range

5. Build exhaustion score:
   - Each active weakness condition adds to the score
   - Higher score means stronger evidence of upside exhaustion

## Signal Levels

| Signal | Chart marker | Condition summary |
| --- | --- | --- |
| Watch | Yellow circle with `W` | Score is at least `2` and price is in the high area |
| Strong | Orange square with `S` | Score is at least `3`, price is high, volume is weak, and upside efficiency is weak |
| Critical | Red label with `C` | Score is at least `4`, price is high, volume is weak, efficiency is weak, and momentum/rejection weakness is present |

The background color also changes:

- Yellow background: watch condition
- Orange background: strong exhaustion
- Red background: critical exhaustion

## Visual Output

| Element | Meaning |
| --- | --- |
| `HH` line | Highest high over the lookback range |
| `LL` line | Lowest low over the lookback range |
| Yellow threshold line | Price high-area threshold |
| `W` marker | Early upside exhaustion watch |
| `S` marker | Stronger exhaustion warning |
| `C` marker | Critical exhaustion warning |
| Diagnostic table | Shows score, price position, and volume ratio |

## Important Notes

- This script is Pine Script v6.
- This is an indicator, not a strategy.
- It does not create trades or generate Strategy Tester results.
- It is designed to warn about possible upside exhaustion, not to automatically short the market.
- The signal can appear while price is still trending up, because exhaustion does not always mean immediate reversal.
- The uploaded source currently ends after the first diagnostic table rows. If the original script had more table rows, they were not included in the uploaded attachment.

## Practical Use

This indicator can be used as a risk-warning layer:

- Use `W` as an early caution signal when price is high and several weakness conditions appear.
- Use `S` as a stronger warning that volume and upside efficiency are both weakening.
- Use `C` as a high-risk exhaustion warning when momentum or wick rejection also confirms weakness.
- Combine with trend structure, support/resistance, liquidity levels, or your own exit rules before making trading decisions.

## File

- `Upside-Strength-Exhaustion-v1.1.pine`

## TradingView Usage

1. Open TradingView Pine Editor.
2. Paste the contents of `Upside-Strength-Exhaustion-v1.1.pine`.
3. Add it to chart.

## 5. Volume Recent vs Previous v1.0

TradingView Pine v6 indicator for comparing recent average volume against the previous equal-length volume window.

## What This Indicator Does

`Volume Recent vs Previous v1.0` checks whether the latest `N` bars are trading with weaker volume than the previous `N` bars.

It is designed as a simple volume weakening detector:

- Recent volume average: current `N` bars
- Previous volume average: the `N` bars before the recent window
- Volume ratio: recent average volume divided by previous average volume
- Weak volume signal: ratio is below the selected threshold

This indicator is plotted in a separate panel because `overlay=false`.

## Core Logic

1. Set comparison length:
   - `volCompareN` defaults to `30`
   - This means the script compares the latest 30 bars against the prior 30 bars

2. Calculate recent volume:
   - `recentVolAvg = ta.sma(volume, volCompareN)`
   - This is the average volume of the current window

3. Calculate previous volume:
   - `previousVolAvg = ta.sma(volume[volCompareN], volCompareN)`
   - This is the average volume of the previous window

4. Calculate ratio:
   - `volumeRatio = recentVolAvg / previousVolAvg`
   - If previous volume is zero or unavailable, ratio becomes `na`

5. Detect weak volume:
   - `volumeWeak = volumeRatio < weakThreshold`
   - Default threshold is `0.80`
   - A ratio below `0.80` means recent average volume is less than 80% of the previous window average

## Visual Output

| Element | Meaning |
| --- | --- |
| Aqua line | Recent `N`-bar average volume |
| Orange line | Previous `N`-bar average volume |
| Purple ratio | Recent / previous volume ratio, shown in the data window |
| Red dashed horizontal line | Weak ratio threshold |
| Red `V` marker | Recent volume is weak |
| Red background | Weak volume condition is active |
| Table | Shows `N`, recent average, previous average, and ratio |

## Alert

The script includes this alert condition:

- `Recent Volume Weak`

The alert triggers when recent `N`-bar average volume is weaker than the previous `N`-bar average volume based on the configured threshold.

## Important Notes

- This script is Pine Script v6.
- This is an indicator, not a strategy.
- It does not create buy/sell trades or Strategy Tester results.
- Weak volume does not automatically mean price must reverse. It only says recent participation is lower than the previous comparison window.
- The signal is most useful when combined with price structure, trend context, breakout/rejection zones, or exhaustion indicators.

## Practical Use

This indicator can be used as a market participation filter:

- Confirm whether an upside move is losing volume support.
- Check whether a breakout has weaker volume than the previous phase.
- Use the red `V` marker as a warning that recent activity has dropped below the selected threshold.
- Combine it with momentum, range, support/resistance, or your own trading rules before making decisions.

## File

- `Volume-Recent-vs-Previous-v1.0.pine`

## TradingView Usage

1. Open TradingView Pine Editor.
2. Paste the contents of `Volume-Recent-vs-Previous-v1.0.pine`.
3. Add it to chart.

## 6. MA 16

TradingView Pine v3 overlay indicator for checking whether price has stayed fully above or fully below two long simple moving averages for a defined number of bars.

## What This Indicator Does

`MA 16` plots two simple moving averages and then checks whether the recent candles are consistently positioned above or below both moving averages.

Default settings:

- `MA-1`: 240-period SMA
- `MA-2`: 480-period SMA
- `check period`: 16 bars

The core idea is simple:

- If the low of every checked candle is above both moving averages, the script marks an upward triangle.
- If the high of every checked candle is below both moving averages, the script marks a downward triangle.

This helps identify periods where price is fully separated from the two long moving averages.

## Core Logic

1. Calculate two moving averages:
   - `sma1 = sma(close, length1)`
   - `sma2 = sma(close, length2)`

2. Plot both moving averages:
   - `MA-1` uses the first length, default `240`
   - `MA-2` uses the second length, default `480`

3. Check bullish condition:
   - For each bar in the check window, the candle low must be above both `sma1` and `sma2`
   - The current candle low must also be above both moving averages
   - When true, the script plots an upward triangle

4. Check bearish condition:
   - For each bar in the check window, the candle high must be below both `sma1` and `sma2`
   - The current candle high must also be below both moving averages
   - When true, the script plots a downward triangle

## Signal Meaning

| Signal | Condition | Meaning |
| --- | --- | --- |
| Green upward triangle | Recent lows are all above both moving averages | Price is strongly above both long MAs |
| Red downward triangle | Recent highs are all below both moving averages | Price is strongly below both long MAs |

## Important Notes

- This script uses Pine Script v3.
- This is an indicator, not a strategy.
- It does not place trades or produce Strategy Tester results.
- The input `src` is defined but the current MA calculation uses `close` directly.
- `dropn()` and `dropCandles` are defined but not actively used because the related line is commented out.
- The signal can continue appearing on many bars while the condition remains true.
- Because the default moving average lengths are long, the indicator needs enough historical bars before signals are meaningful.

## Practical Use

This indicator can be used as a trend-position filter:

- Use the green triangle to identify candles fully above both long moving averages.
- Use the red triangle to identify candles fully below both long moving averages.
- Combine it with momentum, volume, pullback, breakout, or risk rules before making trading decisions.

## File

- `MA-16.pine`

## TradingView Usage

1. Open TradingView Pine Editor.
2. Paste the contents of `MA-16.pine`.
3. Add it to chart.

## 7. Heikin-ma50

TradingView Pine v3 overlay indicator that combines Heikin Ashi candle structure with a 50-period simple moving average.

## What This Indicator Does

`Heikin-ma50` marks strong Heikin Ashi candles when they are fully separated from the selected moving average.

Default settings:

- Moving average length: `50`
- Candle body threshold: `2%`

The script pulls Heikin Ashi OHLC values from the current symbol and timeframe, plots a simple moving average on the normal close price, then marks candles that meet bullish or bearish Heikin Ashi strength conditions.

## Core Logic

1. Load Heikin Ashi data:
   - `ha_open`
   - `ha_high`
   - `ha_close`
   - `ha_low`

2. Calculate moving average:
   - `sma1 = sma(close, length1)`
   - Default `length1` is `50`

3. Bullish signal condition:
   - MA is below the Heikin Ashi close
   - Heikin Ashi low is at or above Heikin Ashi open
   - Heikin Ashi candle body is larger than the selected percentage threshold
   - MA is below the Heikin Ashi low
   - When all are true, the script plots a blue flag above the bar

4. Bearish signal condition:
   - MA is above the Heikin Ashi close
   - Heikin Ashi high is at or below Heikin Ashi open
   - Heikin Ashi candle body is negative and larger than the selected percentage threshold
   - MA is above the Heikin Ashi high
   - When all are true, the script plots an orange diamond below the bar

## Signal Meaning

| Signal | Condition | Meaning |
| --- | --- | --- |
| Blue flag | Strong bullish Heikin Ashi candle above MA | Bullish strength above the moving average |
| Orange diamond | Strong bearish Heikin Ashi candle below MA | Bearish strength below the moving average |

## Important Notes

- This script uses Pine Script v3.
- This is an indicator, not a strategy.
- It does not place trades or produce Strategy Tester results.
- The script uses Heikin Ashi OHLC data through `security(heikinashi(tickerid), period, ...)`.
- The moving average is calculated from normal `close`, not `ha_close`.
- The signal requires the full Heikin Ashi candle to be separated from the moving average.
- `dropn()` is defined but not used in the active logic.

## Practical Use

This indicator can be used as a Heikin Ashi trend-strength marker:

- Use the blue flag to identify strong bullish Heikin Ashi candles above the MA.
- Use the orange diamond to identify strong bearish Heikin Ashi candles below the MA.
- Adjust `check rate%` to require larger or smaller Heikin Ashi candle bodies.
- Combine it with trend structure, support/resistance, volume, or exit rules before making trading decisions.

## File

- `Heikin-ma50.pine`

## TradingView Usage

1. Open TradingView Pine Editor.
2. Paste the contents of `Heikin-ma50.pine`.
3. Add it to chart.

## 8. Large Pullback Risk Model BTC 4H Tuned

TradingView Pine v6 overlay indicator for assessing large pullback risk on BTC 4H after bullish extension.

## What This Indicator Does

`Large Pullback Risk Model BTC 4H Tuned` builds a 0-100 pullback risk score for BTC 4H conditions.

The model is not trying to predict an exact top. It is designed to identify when a BTC bullish move is mature, extended, volatile, and showing early momentum or NERIF cooling signals.

It combines:

- EMA trend structure
- Distance from EMA400
- ATR percentile rank
- RSI strength
- Price position inside the recent impulse range
- MACD histogram weakness
- NERIF expansion, cooling, or weakening state

## Core Logic

1. Trend filter:
   - Bull trend requires close above EMA400
   - EMA100 must be above EMA200
   - EMA200 must be above EMA400
   - Bear trend uses the opposite EMA structure

2. BTC-tuned impulse range:
   - Uses `swingLookback`, default `540` bars
   - Finds the recent swing low and swing high
   - Calculates the impulse percentage move
   - Calculates where current price sits inside that impulse

3. Pullback zones:
   - `0.236`
   - `0.382`
   - `0.500`
   - `0.618`

4. Momentum checks:
   - MACD histogram falling for consecutive bars
   - MACD weakening while still above zero
   - NERIF bull expansion, cooling, weakening, bear expansion, or sideways state

5. Risk scoring:
   - Extension score: distance from EMA400
   - Volatility score: ATR percentile rank
   - RSI score: RSI above 50
   - Position score: price position in the impulse
   - MACD score: momentum weakening
   - NERIF score: NERIF cooling or weakening

## Risk Score Weights

| Component | Weight |
| --- | --- |
| EMA400 extension | `26%` |
| ATR rank | `15%` |
| RSI | `13%` |
| Impulse position | `16%` |
| MACD weakness | `15%` |
| NERIF state | `15%` |

The final score is clamped between `0` and `100`.

## Risk Levels

| Risk score | Risk level |
| --- | --- |
| `75+` | Extreme |
| `60-74.9` | High |
| `40-59.9` | Moderate |
| `25-39.9` | Low |
| Below `25` | Very Low |

## Visual Output

| Element | Meaning |
| --- | --- |
| Aqua line | BTC EMA fast, default EMA100 |
| Blue line | BTC EMA mid, default EMA200 |
| Purple line | BTC EMA slow, default EMA400 |
| Pullback zone lines | 0.236, 0.382, 0.500, 0.618 retracement levels |
| Orange background | Risk score is High |
| Red background | Risk score is Extreme |
| Risk table | Shows score, level, trend, NERIF state, momentum, EMA400 distance, ATR rank, RSI, impulse, and zones |

## Alerts

The script includes three alert conditions:

- `BTC Pullback Risk High`: triggers when risk score enters the High zone.
- `BTC Pullback Risk Extreme`: triggers when risk score enters the Extreme zone.
- `BTC NERIF Bull Expansion Cooling`: triggers when NERIF bullish expansion is cooling during a BTC bull trend.

## Important Notes

- This script is Pine Script v6.
- This is an indicator, not a strategy.
- It does not create trades or produce Strategy Tester results.
- The defaults are tuned for BTC 4H, so other markets or timeframes may need different settings.
- A High or Extreme risk score does not mean price must immediately reverse. It means pullback risk has increased based on this model.
- The model is mainly useful after bullish extension, not as a standalone short signal.

## Practical Use

This indicator can be used as a risk dashboard:

- Monitor whether BTC 4H is becoming extended above EMA400.
- Watch whether ATR rank and RSI are elevated during a mature impulse.
- Use NERIF and MACD cooling as early warnings that bullish expansion may be weakening.
- Use the pullback zone lines as possible reference areas if a retracement starts.
- Combine with market structure, liquidity levels, support/resistance, and position management rules before making trading decisions.

## File

- `Large-Pullback-Risk-Model-BTC-4H-Tuned.pine`

## TradingView Usage

1. Open TradingView Pine Editor.
2. Paste the contents of `Large-Pullback-Risk-Model-BTC-4H-Tuned.pine`.
3. Add it to BTC 4H chart.

## 9. Wyckoff - JOC (Jump Over Creek)

TradingView Pine v6 overlay indicator for detecting a Wyckoff-style Jump Over Creek breakout.

## What This Indicator Does

`Wyckoff - JOC (Jump Over Creek)` looks for a strong breakout above the recent range high, supported by increased volume and a larger-than-average candle body.

In Wyckoff terms, the "creek" is the resistance area above the trading range. A Jump Over Creek means price pushes above that resistance with strength.

This script defines that strength using three conditions:

- Price closes above the recent range high.
- Volume is higher than its moving average by a selected multiplier.
- Candle body is larger than its average body size by a selected multiplier.

## Core Logic

1. Define the creek high:
   - `creekHigh = ta.highest(high, lookback)`
   - Default lookback is `50` bars
   - This represents the recent resistance level

2. Check volume expansion:
   - `volMA = ta.sma(volume, volLength)`
   - `volCond = volume > volMA * volMult`
   - Default volume multiplier is `1.5`

3. Check candle body strength:
   - `body = math.abs(close - open)`
   - `avgBody = ta.sma(math.abs(close - open), volLength)`
   - `bodyCond = body > avgBody * bodyMult`
   - Default body multiplier is `1.2`

4. Confirm JOC:
   - `joc = priceCond and volCond and bodyCond`
   - All three conditions must be true

## Signal Meaning

| Element | Meaning |
| --- | --- |
| Blue line | Creek High, the recent resistance level |
| Green `JOC` label | Price closed above Creek High with volume expansion and strong candle body |

## Alert

The script includes one alert condition:

- `JOC`

The alert message says that a JOC signal appeared, meaning price broke resistance with increased volume.

## Important Notes

- This script is Pine Script v6.
- This is an indicator, not a strategy.
- It does not create trades or produce Strategy Tester results.
- `creekHigh` uses the highest high of the current lookback window, so the breakout condition is strict.
- A JOC signal does not guarantee continuation. It only marks a breakout with volume and candle-body confirmation.
- In real Wyckoff analysis, JOC is usually interpreted together with trading range context, spring, test, sign of strength, and backup action.

## Practical Use

This indicator can be used as a breakout confirmation tool:

- Watch the blue Creek High line as the recent resistance area.
- Use the green `JOC` label to identify strong breakout bars.
- Confirm the signal with market structure, retest behavior, volume profile, or higher timeframe trend.
- Use it as a signal component, not a complete trading system by itself.

## File

- `Wyckoff-JOC-Jump-Over-Creek.pine`

## TradingView Usage

1. Open TradingView Pine Editor.
2. Paste the contents of `Wyckoff-JOC-Jump-Over-Creek.pine`.
3. Add it to chart.
