# Pine Script Code Standards - TradingView Trading Repository

## Language & Version Standards

### Pine Script Version
```pine
//@version=5
```
- **Requirement**: All new code must use Pine Script v5
- **Legacy**: v4 scripts supported but not recommended for new features
- **Compliance**: Strictly follow v5 syntax and function availability

### Licensing Header (Mandatory)
Every Pine Script file must include:
```pine
// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © [Author/binancash]
```

## Script Type Standards

### Strategy Scripts (Production Trading)

**Declaration Format**
```pine
strategy(
    title="[Instrument] - [Timeframe] [Type]",
    overlay=true,  // false for separate panel
    pyramiding=1,  // 0=no pyramiding, 1=single position, 5=aggressive
    default_qty_type=strategy.fixed,
    default_qty_value=1,
    currency=currency.USD
)
```

**Naming Convention**
```
[INSTRUMENT]-[TYPE]-[VERSION].txt

Examples:
- es-futures-no-repaint-v1.3.txt
- nq-est-futres.txt
- SPX-options.txt
- GOLD_UZ_OZ.txt
```

**Parameter Standards**
```pine
// Input declarations - parameterize all key thresholds
stopPerCall = input(0.07, title='Stop Loss Call %') / 100
takePerCall = input(0.05, title='Take Profit Call %') / 100
stopPerPut = input(0.07, title='Stop Loss Put %') / 100
takePerPut = input(0.03, title='Take Profit Put %') / 100

lengthRSI = input.int(14, title='RSI Length', minval=1)
multATR = input.float(3.0, title='ATR Multiplier', minval=0.001, maxval=50)
```

**Mandatory Stop Loss & Take Profit**
```pine
// Calculate position-based exits
longStop = strategy.position_avg_price * (1 - stopPerCall)
longTake = strategy.position_avg_price * (1 + takePerCall)
shortStop = strategy.position_avg_price * (1 + stopPerPut)
shortTake = strategy.position_avg_price * (1 - takePerPut)

// Enforce in exit logic
if strategy.position_size > 0
    strategy.exit("Exit Long", "Long Entry", stop=longStop, limit=longTake)
if strategy.position_size < 0
    strategy.exit("Exit Short", "Short Entry", stop=shortStop, limit=shortTake)
```

### Indicator Scripts (Analysis Only)

**Declaration Format**
```pine
indicator(
    title="[Name] - [Purpose]",
    shorttitle="[SHORT]",
    overlay=true,  // true=on chart, false=separate panel
    timeframe=""   // empty=all timeframes
)
```

**Naming Convention**
```
[PURPOSE]-[DESCRIPTOR].txt

Examples:
- color-trend-lite.txt
- best-indicator.txt
- supper-high-low-live.txt
```

## Signal Generation Standards

### Entry Signal Format

**Long Entry (CALL/IN/BUY)**
```pine
// Condition-based signal
callCondition = (rsi < 30) and (close > ema20) and (volume > avgVolume)

if callCondition
    alert("IN", alert.freq_once_per_bar)
    // For strategy
    strategy.entry("Long Entry", strategy.long)
```

**Short Entry (PUT/IN/SELL)**
```pine
putCondition = (rsi > 70) and (close < ema20) and (volume > avgVolume)

if putCondition
    alert("IN", alert.freq_once_per_bar)
    // For strategy
    strategy.entry("Short Entry", strategy.short)
```

### Exit Signal Format

**Position Close (OUT/EXIT)**
```pine
// Exit when condition met or time-based
exitCondition = (barsSinceEntry > 20) or (close < stopLevel)

if exitCondition
    alert("OUT", alert.freq_once_per_bar)
    // For strategy
    strategy.close_all()
```

### Options Signal Format

**CALL Signal (Bullish)**
```pine
callSignal = condition1 and condition2
if callSignal
    alert("CALL", alert.freq_once_per_bar)
    plotshape(callSignal, title="CALL", style=shape.arrowup, color=color.green)
```

**PUT Signal (Bearish)**
```pine
putSignal = condition1 and condition2
if putSignal
    alert("PUT", alert.freq_once_per_bar)
    plotshape(putSignal, title="PUT", style=shape.arrowdown, color=color.red)
```

## Variable Naming Conventions

### Sensitivity/Input Parameters
```pine
// Use consistent IN/OUT naming for strategy parameters
input_IN = input.int(5, title='IN - Entry Sensitivity', minval=1, maxval=50)
input_OUT = input.int(3, title='OUT - Exit Sensitivity', minval=1, maxval=50)

// For RSI thresholds
rsiOverbought = input.int(70, title='RSI Overbought Level')
rsiOversold = input.int(30, title='RSI Oversold Level')
```

### Moving Averages
```pine
// Format: ema[period]
ema5 = ta.ema(close, 5)
ema10 = ta.ema(close, 10)
ema20 = ta.ema(close, 20)
ema50 = ta.ema(close, 50)
ema200 = ta.ema(close, 200)

// Alternative types
wma10 = ta.wma(close, 10)
vwma20 = ta.vwma(close, 20)
sma50 = ta.sma(close, 50)
```

### Volatility Bands
```pine
// Bollinger Bands format
basis = ta.vwma(close, 20)
dev = ta.stdev(close, 20)
bbUpper = basis + (2 * dev)
bbLower = basis - (2 * dev)

// ATR format
atr14 = ta.atr(14)
stopLong = close - (2 * atr14)
stopShort = close + (2 * atr14)
```

### Condition Variables
```pine
// Use descriptive boolean names
bullishSetup = (close > ema50) and (ema10 > ema50) and (rsi < 70)
bearishSetup = (close < ema50) and (ema10 < ema50) and (rsi > 30)
volumeConfirm = volume > ta.sma(volume, 20)
```

### Multi-timeframe Variables
```pine
// Format: [indicator][timeframe]
open3 = request.security(syminfo.tickerid, "3", open)
open5 = request.security(syminfo.tickerid, "5", open)
open10 = request.security(syminfo.tickerid, "10", open)

// Higher timeframe context
high1h = request.security(syminfo.tickerid, "60", high)
low1h = request.security(syminfo.tickerid, "60", low)
close4h = request.security(syminfo.tickerid, "240", close)
```

## Multi-Timeframe Standards

### Request.Security Usage (Required for Context)
```pine
// Always specify lookahead parameter
// Use barmerge.lookahead_off for live trading (safe)
// Use barmerge.lookahead_on for backtesting/analysis (shows future data)

// SAFE FOR LIVE TRADING
mtf_open = request.security(syminfo.tickerid, "5", open, lookahead=barmerge.lookahead_off)

// BACKTESTING ONLY
mtf_high = request.security(syminfo.tickerid, "1", high, lookahead=barmerge.lookahead_on)
```

### Typical Timeframe Hierarchy
```pine
// 1M chart trading with context from higher timeframes
base_rsi = ta.rsi(close, 14)                              // 1M RSI
context_3m = request.security(syminfo.tickerid, "3", ta.rsi(close, 14))
context_5m = request.security(syminfo.tickerid, "5", ta.rsi(close, 14))
context_60m = request.security(syminfo.tickerid, "60", ta.rsi(close, 14))
```

## Alert & Webhook Standards

### Alert Format (Webhook Compatible)
```pine
// Simple signal name - Tradovate-friendly
if entryCondition
    alert("IN", alert.freq_once_per_bar)

if exitCondition
    alert("OUT", alert.freq_once_per_bar)

// Options trading
if bullishCondition
    alert("CALL", alert.freq_once_per_bar)

if bearishCondition
    alert("PUT", alert.freq_once_per_bar)
```

### Alert Frequency Best Practices
```pine
// Use freq_once_per_bar for live trading
alert("SIGNAL", alert.freq_once_per_bar)

// Use freq_once_per_candle for end-of-bar confirmation
alert("CONFIRMED", alert.freq_once_per_candle)

// Avoid freq_all - too many alerts
// alert("SIGNAL", alert.freq_all)  // DON'T USE
```

## Visual Plotting Standards

### Color Standards
```pine
// Bullish
bullColor = color.green
callColor = color.lime

// Bearish
bearColor = color.red
putColor = color.rgb(255, 0, 0)

// Neutral
trendColor = color.blue
supportColor = color.gray
resistanceColor = color.orange
```

### Shape Standards (Entry/Exit)
```pine
// Entry signals
plotshape(callSignal, style=shape.arrowup, color=color.green, location=location.below)
plotshape(putSignal, style=shape.arrowdown, color=color.red, location=location.above)

// Support/Resistance
plot(supportLevel, color=color.teal, linewidth=2)
plot(resistanceLevel, color=color.orange, linewidth=2)

// Moving averages
plot(ema20, color=color.blue, linewidth=2)
plot(ema50, color=color.purple, linewidth=2)
```

### Line Styles
```pine
// Bands
p_upper = plot(bbUpper, color=color.red, linewidth=1)
p_lower = plot(bbLower, color=color.red, linewidth=1)
fill(p_upper, p_lower, color=color.new(color.red, 90))  // Fill between

// Levels
plot(takeProfit, color=color.green, linewidth=1, style=plot.style_dashed)
plot(stopLoss, color=color.red, linewidth=1, style=plot.style_dashed)
```

## Repainting Standards

### Non-Repainting (Safe for Live Trading)
```pine
// Use lookahead_off in request.security
// Don't reference future bars [1], [-1]
// Signal on close only
if barstate.isclose
    alert("SIGNAL")  // Fire once at bar close
```

### Repainting (Backtesting Only - Mark Clearly)
```
File naming: [name]-repaint-[descriptor].txt
Example: es-futures-repaint-30s.txt

Add disclaimer in script:
// WARNING: This script repaints on historical bars
// Use for BACKTESTING ONLY - NOT SAFE FOR LIVE TRADING
```

### Lookahead Bias Warnings
```pine
// Files with note: "Some SPX files have lookahead bias"
// Mark clearly if using lookahead_on:
mtf_data = request.security(syminfo.tickerid, "5", close, lookahead=barmerge.lookahead_on)
// WARNING: Contains lookahead bias - backtest only
```

## Comment Standards

### Header Comments
```pine
// Brief description of what the indicator/strategy does
// Key parameters explained
// Usage instructions
// Author information
```

### Inline Comments
```pine
// Comment complex calculations
basis = ta.vwma(close, 20)  // Weighted moving average of close prices
dev = mult * ta.stdev(close, 20)  // Standard deviation scaled by multiplier
upper = basis + dev  // Upper Bollinger Band
```

### Section Comments
```pine
//////////////////////////////////////////////////////////////
// MOVING AVERAGES SECTION
//////////////////////////////////////////////////////////////

//////////////////////////////////////////////////////////////
// RISK MANAGEMENT SECTION
//////////////////////////////////////////////////////////////

//////////////////////////////////////////////////////////////
// SIGNAL GENERATION SECTION
//////////////////////////////////////////////////////////////
```

## Parameter Optimization Standards

### Default Recommended Values

**ES Futures (1M)**
```pine
input_IN = input.int(5, title='IN - Entry Sensitivity')
input_OUT = input.int(3, title='OUT - Exit Sensitivity')
stopLossPct = input(0.07, title='Stop Loss %') / 100
takeProfitPct = input(0.05, title='Take Profit %') / 100
```

**NQ Futures (1M)**
```pine
input_IN = input.int(15, title='IN - Entry Sensitivity')  // OR 8
input_OUT = input.int(12, title='OUT - Exit Sensitivity')  // OR 5
```

**SPX Options (15M)**
```pine
input_IN = input.int(8, title='IN - Entry Sensitivity')
input_OUT = input.int(6, title='OUT - Exit Sensitivity')
```

**GOLD (1H)**
```pine
input_IN = input.int(10, title='IN - Entry Sensitivity')
input_OUT = input.int(7, title='OUT - Exit Sensitivity')
```

## Testing Standards

### Required Backtesting
- Minimum 6 months of historical data for ES/NQ
- Minimum 3 months for options
- Walk-forward testing recommended
- Test across different market regimes (trending, ranging, volatile)

### Success Criteria
- Win rate ≥ 70% on primary instruments
- Profit factor ≥ 1.5
- Max drawdown < 20%
- Average trade duration < 30 minutes

## Forbidden Practices

```pine
// DON'T: Reference future bars in live signals
// if close[1] > close  // WRONG - uses past data only

// DON'T: Use alert.freq_all in production
// alert("SIGNAL", alert.freq_all)  // SPAM

// DON'T: Hardcode magic numbers
// if rsi < 27.3 and atr > 1.23  // Bad practice

// DON'T: Skip stop-loss implementation
// strategy.entry()  // MISSING exit logic

// DON'T: Use v4 functions in v5 scripts
// study()  // USE indicator() instead
// strategy.exit with only one parameter  // Incomplete

// DON'T: Deploy repainting scripts for live trading
// lookahead=barmerge.lookahead_on in live signals  // DANGEROUS
```

## Recommended Practices

```pine
// DO: Parameterize everything
myInput = input.int(14, title='My Parameter')

// DO: Use clear variable names
bullishSetup = condition1 and condition2 and condition3

// DO: Add comprehensive comments
// This detects when RSI enters oversold territory
rsiOversold = ta.rsi(close, 14) < 30

// DO: Validate inputs
length = input.int(14, title='RSI Length', minval=1, maxval=100)

// DO: Use lookahead_off for live trading
request.security(..., lookahead=barmerge.lookahead_off)

// DO: Include stop-loss in all strategies
strategy.exit(..., stop=stopLevel, limit=targetLevel)

// DO: Test before deployment
// [Run 6+ months backtest] before enabling alerts
```

## Code Organization Pattern

```pine
// 1. HEADER & LICENSE
// [License header]
// @version=5
// indicator() or strategy() declaration

// 2. INPUTS & PARAMETERS
input_var1 = input(...)
input_var2 = input.int(...)

// 3. CALCULATIONS & INDICATORS
ema20 = ta.ema(close, 20)
rsi14 = ta.rsi(close, 14)
atr14 = ta.atr(14)

// 4. SIGNAL LOGIC
bullCondition = ema20 > ema50 and rsi14 < 70
bearCondition = ema20 < ema50 and rsi14 > 30

// 5. STRATEGY EXECUTION (if strategy)
strategy.entry("Long", strategy.long, when=bullCondition)
strategy.exit("Exit", stop=stopLevel, limit=targetLevel)

// 6. VISUAL OUTPUTS
plot(ema20, color=color.blue)
plotshape(bullCondition, style=shape.arrowup)

// 7. ALERTS
alert("IN", when=bullCondition)
```

## Version Control

### Naming Convention
```
[name]-v[X].[Y].txt

Examples:
- es-futures-no-repaint-v1.3.txt
- es-futures-no-repaint-v1.2.txt
- best-strategy-es-1m.txt (implied v1.0)

Version Number Rules:
- v[MAJOR].[MINOR]
- MAJOR: Breaking changes to signal logic
- MINOR: Parameter adjustments, bug fixes
```

---

**Document Version**: 1.0
**Last Updated**: 2025-12-26
**Compliance**: Mandatory for all new scripts
