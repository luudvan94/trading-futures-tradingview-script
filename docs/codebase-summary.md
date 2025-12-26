# Codebase Summary - TradingView Pine Script Trading Repository

## Repository Structure

```
/Users/luuvan/Dev/trading-futures-tradingview-script/
├── docs/                           # Documentation (this folder)
│   ├── project-overview-pdr.md      # Project overview & requirements
│   ├── codebase-summary.md          # This file
│   ├── code-standards.md            # Pine Script coding standards
│   ├── system-architecture.md       # System design & integration
│   └── deployment-guide.md          # Setup & deployment instructions
│
├── .claude/                         # Claude Code configuration
│   ├── skills/                      # Reusable skill modules
│   ├── workflows/                   # Development workflows
│   ├── scripts/                     # Automation scripts
│   └── settings.json                # Configuration
│
├── plans/                           # Planning & reports
│   └── reports/                     # Analysis reports
│
├── Pine Scripts (49 total)          # Root level .txt files
│   ├── Strategies/                  # Trading strategies (22)
│   │   ├── es-futures-no-repaint-v1.3.txt
│   │   ├── es-futures-no-repaint-v1.2.txt
│   │   ├── es-futures-no-repaint-v1.1.txt
│   │   ├── es-futures-no-repaint-v1.0.txt
│   │   ├── ESH2023-5M.txt
│   │   ├── ESH2023.txt
│   │   ├── ESM2024.txt
│   │   ├── ES1M-BEST.txt
│   │   ├── ESU2023_future_no_repaint.txt
│   │   ├── ESU2023-10M.txt
│   │   ├── best-strategy-es-1m.txt
│   │   ├── nq-est-futres.txt
│   │   ├── nqu2023-1m.txt
│   │   ├── NQU2023-10M.txt
│   │   ├── SPX-options.txt
│   │   ├── SPX-options-new-logic.txt
│   │   ├── spx-30m-options.txt
│   │   ├── spx-30m.txt
│   │   ├── trade-futures.txt
│   │   ├── simple-entry-100win.txt
│   │   ├── GOLD_UZ_OZ.txt
│   │   └── no-repain-test-futures.txt
│   │
│   ├── Indicators/                  # Visual indicators (26)
│   │   ├── color-trend-lite.txt
│   │   ├── Lux-Trailing-BUY_SELL.txt
│   │   ├── best-indicator.txt
│   │   ├── best-chart.txt
│   │   ├── supper-high-low-live.txt
│   │   ├── swing.txt
│   │   ├── win-99.txt
│   │   ├── 1001-pattern.txt
│   │   ├── ai-futures-v1.txt
│   │   ├── ai-trend.txt
│   │   ├── ai-prediction-rsi.txt
│   │   ├── ai-prediction
│   │   ├── alert-options-trading-spy.txt
│   │   ├── SuperTrend-ATR-RSI.txt
│   │   ├── ready-call-put.txt
│   │   ├── sideway-zone.txt
│   │   ├── footprint-delta.txt
│   │   ├── highest-lowest-visual.txt
│   │   ├── machine-learning-options.txt
│   │   ├── vwap_trading_futures.txt
│   │   ├── Volume-profile.txt
│   │   ├── vp.txt
│   │   ├── vp-v2.txt
│   │   ├── spy.txt
│   │   ├── 2m-market-moving.txt
│   │   └── no-repain-futures.txt
│   │
│   └── Variants/                    # Alternative versions
│       ├── es-futures-repaint-30s.txt (backtesting only)
│       ├── es-futures-no-repaint.txt
│       ├── es-futures (file without extension)
│       ├── nq-future (file without extension)
│       ├── ai-prediction (file without extension)
│       ├── futures-2024 (directory)
│       ├── price_action (directory)
│       └── [other variants]
│
└── .git/                            # Version control
    └── [git history & branches]
```

## File Statistics

| Category | Count | Tokens | Avg Size |
|---|---|---|---|
| Total Files | 60 | 372,794 | ~6,213 |
| Pine Scripts | 49 | ~340,000 | ~6,939 |
| Config/Docs | 11 | ~32,794 | ~2,981 |

## Top 5 Files by Complexity (Token Count)

1. **spx-30m.txt** - 23,241 tokens (78,565 chars) - SPX 30M options strategy
2. **spx-30m-options.txt** - 22,948 tokens (77,699 chars) - Alternative SPX 30M
3. **nq-future** - 17,758 tokens (60,073 chars) - NQ futures strategy
4. **trade-futures.txt** - 17,735 tokens (60,035 chars) - Multi-instrument futures
5. **ESH2023-5M.txt** - 15,078 tokens (45,700 chars) - ES historical 5M strategy

## Key File Descriptions

### Production Strategies (Live Trading - Non-Repainting)

**es-futures-no-repaint-v1.3.txt** (RECOMMENDED)
- Purpose: ES 1M futures, non-repainting, live trading safe
- Indicators: VWMA, ATR, Moving Averages, Bollinger Bands
- Parameters: IN=5, OUT=3 (configurable)
- Stop/Take: 0.07% stop, 0.05% take-profit
- Accuracy: 80%+
- Time window: 6:30 AM - 12:30 PM UTC-8

**best-strategy-es-1m.txt**
- Purpose: ES 1M alternative approach, simplified logic
- Signals: IN (entry), OUT (exit)
- Timeframe: 1 minute
- Accuracy: 75%+

**nq-est-futres.txt**
- Purpose: NQ multi-timeframe futures strategy
- Parameters: IN=15, OUT=12 (OR IN=8, OUT=5)
- Timeframes: 1M, 5M, 10M analysis
- Accuracy: 90%

**SPX-options.txt**
- Purpose: SPX options trading (CALL/PUT signals)
- Timeframe: 15M recommended
- Signals: CALL, PUT with entry/exit
- Accuracy: 85%+
- Note: Requires bot for execution (fast-moving)

**spx-30m-options.txt**
- Purpose: SPX 30M options with enhanced logic
- Timeframe: 30M
- Signals: Real-time CALL/PUT detection
- Note: One of largest scripts, most complex

**GOLD_UZ_OZ.txt**
- Purpose: GOLD 1H commodity trading
- Signals: IN/OUT for directional trades
- Accuracy: 70%+

### Beginner-Friendly Indicators

**color-trend-lite.txt** (RECOMMENDED FOR LEARNING)
- Purpose: Simple trend identification for newcomers
- Signals: BLUE (trend up), RED (trend down)
- Logic: Uses price action without complex math
- Perfect for: Manual trading decisions
- Complexity: Minimal

### Advanced Indicators

**Lux-Trailing-BUY_SELL.txt**
- Purpose: Trailing stop visualization
- Signals: BUY, SELL with exit recommendations
- Use case: Protecting gains while trading

**best-indicator.txt**
- Purpose: Weak market condition detection
- Signals: GC (Great Call - exit PUT), GP (Great PUT - exit CALL)
- Timeframe: 5M recommended
- Use case: Adaptive trading in volatile markets

**supper-high-low-live.txt**
- Purpose: Real-time support/resistance levels
- Instruments: Works on SPX, ES, ETH, etc.
- Use case: Visual level confirmation

**win-99.txt**
- Purpose: Market crash detection and trading
- Logic: Identifies crash conditions for high-probability trades
- Use case: Specific market regime trading

### Volume & Pattern Analysis

**1001-pattern.txt**
- Purpose: Pattern recognition engine
- Patterns: Multiple candlestick patterns
- Use case: Entry/exit confirmation

**footprint-delta.txt**
- Purpose: Order flow and delta analysis
- Use case: Institutional flow detection

**Volume-profile.txt, vp.txt, vp-v2.txt**
- Purpose: Volume distribution visualization
- Use case: Finding high-volume nodes and clusters
- Versions: v-v2 is latest

**machine-learning-options.txt**
- Purpose: ML-based prediction model for options
- Use case: Probabilistic signal filtering

### Special Purpose Files

**es-futures-repaint-30s.txt**
- Purpose: BACKTESTING ONLY - 30s ES strategy
- Warning: Repaints on historical bars, not safe for live trading
- Use case: Strategy optimization and research

**ai-futures-v1.txt, ai-trend.txt, ai-prediction-rsi.txt**
- Purpose: AI/ML-enhanced signal generation
- Complexity: Advanced
- Use case: Research and experimentation

## Pine Script Characteristics

### Version
- Declared as: `//@version=5` (Pine Script v5)
- Some older variants may use v4

### Common Structure
```pine
// Header with license (Mozilla Public License 2.0)
// @version=5
// indicator() or strategy() declaration with settings:
//   - overlay=true/false
//   - pyramiding=0/1/5
//   - default_qty_type
//   - default_qty_value

// Input definitions (parameters)
input(type, default, title, minval, maxval)

// Core logic
// - Moving averages (EMA, VWMA, SMA)
// - Oscillators (RSI, CCI, MACD)
// - Volatility (ATR, Bollinger Bands)
// - Price action (Support/Resistance)

// Signal generation
// if condition => alert.trigger()
// plotshape() for visual signals
// plotcandle() for enhanced bars

// Risk management (strategies only)
// strategy.entry(id, direction, qty)
// strategy.exit(id, stop_loss, take_profit)
```

### Core Indicators Used Across Scripts
- **Moving Averages**: EMA, VWMA, SMA (periods: 5-200)
- **Momentum**: RSI (14-period standard), CCI, MACD
- **Volatility**: ATR (14-period), Bollinger Bands (20-period)
- **Trends**: Supertrend, VWAP, Price channels
- **Volume**: Volume Profile, On-Balance Volume
- **Price Action**: Support/Resistance, Pivots, Swing points

### Signal Naming Conventions
```
Entry Signals:
- "IN" or "ENTRY" = Long entry signal
- "CALL" = Bullish option signal
- "BUY" = Manual entry confirmation

Exit Signals:
- "OUT" or "EXIT" = Position close signal
- "PUT" = Bearish option signal
- "SELL" = Position reversal signal

Market Conditions:
- "GC" = Great Call (strong bullish)
- "GP" = Great PUT (strong bearish)
- "WEAK" = Weak market condition
- "STRONG" = Strong market condition
```

## Timeframe Support

Scripts typically support these TradingView timeframes:

**Ultra Short-Term**
- 1M, 3M, 5M - ES/NQ futures primary
- 30s (repainting variants)

**Short-Term**
- 8M, 10M, 15M - Options and confirmation

**Medium-Term**
- 30M, 1H - GOLD and swing trades

**Long-Term**
- 4H, Daily - Market context

## Testing & Backtesting

### Backtesting Approach
1. ES strategies: Test on 1M/5M timeframes, 6-month historical data
2. NQ strategies: Test on 1M, 5M, 10M with 3-month data
3. Options: Limited backtesting available in TradingView
4. Walk-forward testing recommended before live deployment

### Accuracy Claims
- **Verified**: ES 70-90%, NQ 90%
- **Unverified**: Others require independent validation
- Note: Historical accuracy ≠ Future performance

### Files for Backtesting Only
- `es-futures-repaint-30s.txt` - Repainting on 30s
- Others flagged in README

## Integration Points

1. **TradingView Alerts → Webhooks** - Send IN/OUT signals to Tradovate
2. **WordPress Plugin** - Alert logging and distribution
3. **Binance Direct** - For futures trading without Tradovate
4. **Tradier/Schwab APIs** - For options trading

## Dependencies

**No external libraries required** - Pure Pine Script using built-in TradingView functions.

### Required TradingView Features
- Strategy testing (Pro+ subscription recommended)
- Alert functionality
- Request.security() for multi-timeframe
- Webhook capability for automation

## Documentation References

- Full PDR: `./docs/project-overview-pdr.md`
- Coding standards: `./docs/code-standards.md`
- System architecture: `./docs/system-architecture.md`
- Deployment guide: `./docs/deployment-guide.md`

---

**Document Version**: 1.0
**Generated from**: repomix-output.xml
**Last Updated**: 2025-12-26
**Repository Size**: ~372K tokens, ~1.2M chars
