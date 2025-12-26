# Frequently Asked Questions (FAQ)

> Documentation generated from Q&A session on 2025-12-26

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Pine Script Basics](#2-pine-script-basics)
3. [Prop Firm Trading](#3-prop-firm-trading)
4. [Repainting Explained](#4-repainting-explained)
5. [Ticker Compatibility](#5-ticker-compatibility)
6. [Signal Generation](#6-signal-generation)
7. [TradingView Setup](#7-tradingview-setup)
8. [Script Quality](#8-script-quality)
9. [Testing & Validation](#9-testing--validation)
10. [Strategy Improvement](#10-strategy-improvement)

---

## 1. Project Overview

### What is this project about?

A **TradingView Pine Script collection for algorithmic futures and options trading**.

**Contents:**
- 49 Pine Script files
- 22 Trading Strategies (backtestable algorithms)
- 26 Technical Indicators (visual analysis tools)

**Target Instruments:**

| Instrument | Coverage | Accuracy Claim |
|------------|----------|----------------|
| ES (S&P 500 E-mini) | Primary focus, 48 files | 70-90% |
| NQ (Nasdaq-100) | 3 specialized files | 90% |
| SPX/SPY (Options) | 8 files | N/A |
| GOLD | 1 file | N/A |

**Architecture:**
```
TradingView (Signals) → Webhook Alerts → Tradovate (Execution)
                              ↓
                       WordPress Plugin
```

---

## 2. Pine Script Basics

### What is Pine Script?

**Pine Script** is TradingView's proprietary programming language for creating custom technical indicators and trading strategies.

| Aspect | Details |
|--------|---------|
| Type | Domain-specific language (DSL) |
| Platform | TradingView only (cloud-executed) |
| Current Version | v5 (backwards compatible with v4) |
| Purpose | Technical analysis, signals, backtesting |

**Core Capabilities:**

1. **Indicators** (`indicator()`) - Visual overlays and oscillators
2. **Strategies** (`strategy()`) - Backtestable trading logic
3. **Alerts** - Webhook triggers for automation

**In This Repository:** All 49 `.txt` files contain Pine Script code saved as plain text for easy TradingView import.

---

## 3. Prop Firm Trading

### Can I use this for prop firm challenges?

**Yes, with caveats.** The system generates signals for ES/NQ futures day trading, but prop firm challenges have specific constraints.

**What Works:**

| Feature | Prop Firm Fit |
|---------|---------------|
| ES, NQ futures | Most prop firms trade these |
| 1M, 5M, 10M timeframes | Day trading compatible |
| IN/OUT, BUY/SELL signals | Clear entry/exit |
| Non-repainting scripts | Critical for live trading |

**What's Missing:**

| Constraint | Risk |
|------------|------|
| Daily drawdown (2-5% max) | No built-in DD protection |
| Max drawdown (5-10% total) | Requires manual risk mgmt |
| Position sizing | Must configure manually |

**Recommended Scripts:**

| Script | Risk Level |
|--------|------------|
| `es-futures-no-repaint-v1.3.txt` | Medium |
| `color-trend-lite.txt` | Low |
| `ES1M-BEST.txt` | Low |

**Critical Warning:** Do NOT automate during challenge phase - most prop firms prohibit it.

**Prop Firm Compatibility:**

| Prop Firm | Compatible Instruments |
|-----------|----------------------|
| Apex Trader Funding | ES, NQ (covered) |
| Topstep | ES, NQ (covered) |
| My Funded Futures | ES, NQ (covered) |

---

## 4. Repainting Explained

### What is repainting?

**Repainting** occurs when a trading indicator changes its historical signals after the fact. A "BUY" signal that appeared yesterday might disappear or move today.

**Visual Example:**

```
NON-REPAINTING (Safe):
Bar 1    Bar 2    Bar 3    Bar 4    Bar 5
         BUY              SELL
[Same signals tomorrow - locked in]

REPAINTING (Dangerous):
LIVE:    Bar 2=BUY, Bar 4=SELL
NEXT DAY: Bar 3=BUY, Bar 5=SELL  ← Signals MOVED!
```

**Why It's Dangerous:**
- Backtest shows 95% win rate with perfect entries
- Live trading: signal appears → you enter → signal disappears
- You're stuck in a losing trade that "never signaled"

**Safe Scripts in This Repo:**
- `es-futures-no-repaint-v1.0.txt` through `v1.3.txt`
- `GOLD_UZ_OZ.txt`

**Dangerous Scripts:**
- `es-futures-repaint-30s.txt` (backtest only)
- Some SPX files with `barmerge.lookahead_on=true`

### Why keep repainting scripts if dangerous?

**Repainting isn't useless - it's useless for live trading.** Legitimate uses:

| Use Case | Why Helpful |
|----------|-------------|
| Pattern discovery | See "ideal" entries |
| Strategy prototyping | Fast iteration |
| Educational | Learn indicator behavior |
| Debugging | Compare repaint vs non-repaint |

The `es-futures-repaint-30s.txt` file is a development artifact showing the evolution from prototype to production.

---

## 5. Ticker Compatibility

### Can I use these scripts for any ticker?

**No.** These scripts are specifically optimized for ES/NQ futures. Using them on other tickers will likely fail.

**Why Ticker-Specific:**

| Factor | ES/NQ Optimized | Other Tickers |
|--------|-----------------|---------------|
| Volatility | ATR tuned for ES | Wrong scaling |
| Spread | Assumes 0.25 point | May be wider |
| Volume profile | US market hours | Different patterns |
| Parameters | IN=5, OUT=3 for ES | Won't translate |

**Compatibility Matrix:**

| Ticker | Dedicated Script | Will Work? |
|--------|-----------------|------------|
| ES | 15+ scripts | Yes |
| NQ | 3 scripts | Yes |
| MES/MNQ | No | Maybe (test first) |
| GOLD/GC | 1 script | Yes |
| CL, RTY, 6E | No | No |
| Forex/Crypto | No | No |

### Why more ES scripts than other tickers?

**The author trades ES primarily.** More scripts = more iteration = higher confidence.

```
ES Evolution:
├── es-futures (original)
├── es-futures-no-repaint.txt
├── es-futures-no-repaint-v1.0.txt
├── es-futures-no-repaint-v1.1.txt
├── es-futures-no-repaint-v1.2.txt
└── es-futures-no-repaint-v1.3.txt  ← 6 iterations

GOLD:
└── GOLD_UZ_OZ.txt  ← 1 version
```

**Quality comes from iteration, not file count.**

---

## 6. Signal Generation

### How are trade signals generated?

Signals are generated through a **5-layer process**:

```
LAYER 1: TREND DETECTION
├── WMA13, WMA48, WMA200 crossovers
├── Ichimoku Cloud
├── SuperTrend + CCI(28)
└── isUpTrend / isDownTrend flags

LAYER 2: SUPPORT/RESISTANCE
├── Pivot High/Low (5-bar lookback)
├── VWMA Bands (Bollinger-like)
└── Dynamic S/R levels

LAYER 3: MOMENTUM CONFIRMATION
├── RSI(11) vs RSI-MA(24)
├── MACD(5,13,8) histogram
└── Bar color analysis (Heikin-Ashi)

LAYER 4: ENTRY CONDITIONS
├── 10+ long conditions (OR logic)
└── 7+ short conditions (OR logic)

LAYER 5: FILTERS
├── Bar direction validation
├── MACD/RSI trend confirmation
└── Time filter (6:30 AM - 12:30 PM UTC-8)
```

**Key Indicators Used:**

| Indicator | Settings | Role |
|-----------|----------|------|
| WMA | 13, 48, 200 | Trend direction |
| VWMA Bands | 11-period, 3.0 mult | Support/resistance |
| CCI | 28-period | SuperTrend input |
| RSI | 5 and 11-period | Momentum |
| MACD | 5/13/8 and 12/26/9 | Entry timing |

**Only when ALL layers align does a signal fire.**

---

## 7. TradingView Setup

### How do I use these scripts in TradingView?

**Step 1: Copy Script**
- Open desired `.txt` file
- Select all (Cmd+A) and copy (Cmd+C)

**Step 2: Open Pine Editor**
1. Go to tradingview.com
2. Open chart (ES1!, NQ1!, etc.)
3. Click "Pine Editor" at bottom

**Step 3: Paste and Add**
1. Delete existing code
2. Paste script
3. Click "Save" → Name it
4. Click "Add to chart"

**Step 4: Configure**

| Setting | Value |
|---------|-------|
| Symbol | ES1!, MES1!, NQ1! |
| Timeframe | 1M (for v1.3) |
| Hours | 6:30 AM - 12:30 PM UTC-8 |

**Step 5: Set Alerts (Optional)**
1. Right-click chart → Add Alert
2. Condition: Your script
3. Trigger: "Any alert() function call"

### Can I use TradingView Free plan?

**Yes, with limitations:**

| Feature | Free | Pro ($12.95) |
|---------|------|--------------|
| Load scripts | Yes | Yes |
| Run backtest | Yes | Yes |
| Indicators/chart | 2 | 5 |
| Alerts | 1 | 20 |
| Seconds timeframes | No | No |

**Free plan works for:** Learning, backtesting, manual signal watching

**Need upgrade for:** Multiple alerts, webhook automation

---

## 8. Script Quality

### Can I improve quality without actual trading?

**Yes, up to ~70%.** Remaining 30% requires live execution.

**What You CAN Improve:**

1. **Backtesting** - Strategy Tester metrics
2. **Parameter Optimization** - Test IN/OUT values
3. **Walk-Forward Analysis** - Out-of-sample testing
4. **Paper Trading** - Simulated execution
5. **Code Quality** - Fix repainting, add filters

**What Requires Real Money:**
- Slippage effects
- Execution latency
- Emotional discipline
- Liquidity impact
- Market regime changes

**Improvement Potential:**
```
BACKTEST:  ████████████████░░░░  70% of edge
PAPER:     ██████████████████░░  85% of edge
LIVE:      ████████████████████  100% truth
```

**Expect 15-25% performance degradation in live trading.**

---

## 9. Testing & Validation

### How do I know if these strategies work?

**Step 1: Run Backtest**

| Metric | Minimum | Good | Excellent |
|--------|---------|------|-----------|
| Net Profit | Positive | >10% | >30% |
| Win Rate | >45% | >55% | >65% |
| Profit Factor | >1.2 | >1.5 | >2.0 |
| Max Drawdown | <20% | <10% | <5% |
| Total Trades | >100 | >200 | >500 |

**Step 2: Test Multiple Periods**
- 2022 (Bear market)
- 2023 (Recovery)
- 2024 (Bull market)

All 3 profitable = Robust strategy

**Step 3: Check Overfitting Red Flags**

| Red Flag | Meaning |
|----------|---------|
| 90%+ win rate | Probably repainting |
| Works only on specific dates | Curve-fitted |
| Huge profit on 10 trades | Lucky, not valid |

**Step 4: Paper Trade Forward**
- Weeks 1-2: Watch signals
- Weeks 3-4: Paper trade
- Weeks 5-8: Compare to backtest

Paper ≈ Backtest (±15%) → Strategy works

---

## 10. Strategy Improvement

### How do I improve these strategies?

**Improvement Framework:**
```
1. IDENTIFY WEAKNESS - Where does it lose?
2. HYPOTHESIZE CAUSE - Why does it fail?
3. PROPOSE FIX - What change helps?
4. BACKTEST FIX - Does it improve?
5. PAPER TRADE - Does it work forward?
```

**Common Improvements:**

| Problem | Solution |
|---------|----------|
| Too many losses | Add confirmation filter |
| Wins too small | Widen take profit |
| Big drawdowns | Add daily loss limit |
| Too few trades | Loosen entry conditions |
| Choppy results | Add volatility filter |

**Example Filters to Add:**

```pine
// Trend filter
ema200 = ta.ema(close, 200)
if longCondition and close > ema200
    strategy.entry("call", strategy.long)

// Volatility filter
atr = ta.atr(14)
highVol = atr > ta.sma(atr, 50) * 0.8
if longCondition and highVol
    strategy.entry("call", strategy.long)

// Time filter (avoid first 30 min)
after930 = hour >= 10 or (hour == 9 and minute >= 30)
if longCondition and after930
    strategy.entry("call", strategy.long)
```

**Validation Checklist:**

- [ ] 100+ trades in backtest
- [ ] Profitable in 2022, 2023, 2024
- [ ] Win rate > 50%
- [ ] Profit factor > 1.3
- [ ] Max drawdown < 15%
- [ ] Paper traded 2+ weeks
- [ ] No repainting verified
- [ ] Works on out-of-sample data

**All boxes checked → Strategy probably works.**

---

## Quick Reference

### Best Scripts by Use Case

| Use Case | Script | Timeframe |
|----------|--------|-----------|
| ES Day Trading | `es-futures-no-repaint-v1.3.txt` | 1M |
| NQ Day Trading | `nqu2023-1m.txt` | 1M |
| Gold Scalping | `GOLD_UZ_OZ.txt` | 1M |
| Learning | `color-trend-lite.txt` | Any |
| SPX Options | `SPX-options.txt` | 15M |

### Signal Meanings

| Visual | Meaning |
|--------|---------|
| Green "BUY" triangle | Long entry |
| Red "SELL" triangle | Short entry |
| Blue/White lines | Support/Resistance |
| Yellow line | VWMA basis |

### Minimum Requirements

| Goal | TradingView Plan | Monthly Cost |
|------|------------------|--------------|
| Learning | Free | $0 |
| Manual trading | Free/Essential | $0-13 |
| Webhook automation | Pro | $25 |
| Professional | Premium | $50 |

---

## Unresolved Questions

1. **Accuracy claims** - 70-90% documented but no recent 2024 live verification
2. **Parameter optimization** - Why IN/OUT values differ by asset (ES: 5/3, NQ: 15/12)?
3. **Lookahead bias** - Some SPX files use `barmerge.lookahead_on=true` - intentional?
4. **Integration code** - Tradovate bot referenced but not in repo
