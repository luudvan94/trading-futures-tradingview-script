# TradingView Pine Script Trading Repository - Project Overview & PDR

## Executive Summary

Collection of 49 TradingView Pine Script files designed for futures and options trading on multiple instruments. Includes 22 production-ready strategies (45%) and 26 indicators (53%) with claimed accuracy rates of 70-90% depending on strategy and timeframe. Automated execution enabled via webhook integration to Tradovate platform.

## Product Purpose

Provide traders with a comprehensive toolkit for:
- Real-time signal generation on ES (S&P 500 E-mini), NQ (Nasdaq-100), SPX/SPY options, and GOLD
- Automated trading execution through TradingView webhooks to Tradovate
- Risk management with stop-loss and take-profit levels
- Visual analysis with non-repainting indicators for reliable backtesting

## Target Users

- Active futures traders on TradingView platform
- Automated trading system operators with Tradovate access
- Options traders seeking real-time CALL/PUT signals
- Institutional traders using TradingView charting infrastructure

## Instruments Covered

| Instrument | Type | Scripts | Primary Timeframes |
|---|---|---|---|
| ES (E-mini S&P 500) | Futures | 11 | 1M, 5M, 10M |
| NQ (Nasdaq-100) | Futures | 8 | 1M, 5M, 10M |
| SPX | Index Options | 5 | 15M, 30M |
| SPY | ETF Options | 3 | Varies |
| GOLD | Commodity | 2 | 1H, 4H |
| Multi-instrument | General | 20 | 1M-30M |

## Functional Requirements

### FR1: Signal Generation
- **IN/OUT signals**: Entry and exit points for directional trades
- **CALL/PUT signals**: Options directional signals
- **BUY/SELL signals**: Manual and automated entry points
- **Real-time delivery**: Signals must be generated within current bar

### FR2: Risk Management
- Configurable stop-loss percentages (0.03-0.07%)
- Configurable take-profit percentages (0.03-0.05%)
- Position-based calculations using strategy.position_avg_price
- Pyramiding control (default pyramiding=1)

### FR3: Multi-timeframe Analysis
- Request.security() for higher timeframe context
- Support for 1M, 3M, 5M, 8M, 10M, 60M timeframes
- EMA, RSI, ATR, CCI, MACD, WMA, VWAP as core indicators

### FR4: Visual Analysis
- Overlay charts with entry/exit levels
- Color-coded trend identification (BLUE=up, RED=down)
- Plotcandle() for enhanced price action visualization
- Support/resistance level mapping

## Non-Functional Requirements

### NF1: Accuracy & Performance
- ES strategies: 70-90% backtested accuracy
- NQ strategies: ~90% backtested accuracy
- Real-time processing with <100ms latency
- No memory leaks in long-running sessions

### NF2: Reliability
- Non-repainting scripts safe for live trading (11 files)
- Repainting scripts for backtesting only (1 file)
- Some SPX files noted with lookahead bias - use with caution
- Graceful handling of market gaps and limit moves

### NF3: Maintainability
- Pine Script v4/v5 compatibility
- Parameterized inputs for easy optimization
- Clear variable naming conventions
- Comprehensive documentation within scripts

### NF4: Scalability
- Support for multiple instruments simultaneously
- Webhook integration handles 100+ alerts per day
- Compatible with Tradovate's order routing capacity

## Integration Points

### TradingView → Tradovate Webhook
```
Flow: TradingView Alert → Webhook URL → Tradovate API → Order Execution
Documentation: See system-architecture.md for detailed flow
```

### WordPress Alert Plugin
- Integration: https://github.com/dearvn/tradingview-alerts
- Purpose: Alternative alert delivery and logging

### Binance Futures Integration
- Integration: https://github.com/dearvn/tradingview-pinscript-futures-binance
- Purpose: Direct Binance API trading without manual intervention

### Schwab API Integration
- Reference: https://github.com/alexgolec/schwab-py
- Purpose: For SPX/SPY options trading with Schwab platform

### Tradier Integration
- API: https://tradier.com/
- Purpose: Options data provider for SPX/SPY strategies

## Acceptance Criteria

### AC1: Signal Quality
- Backtested win rate ≥ 70% on primary instruments
- Consistent signal generation across market conditions
- No false signals during sideways markets (tolerance: <5%)

### AC2: Integration
- Webhooks successfully trigger Tradovate orders
- Alert message format compatible with Tradovate parsing
- Latency from signal to order < 2 seconds

### AC3: Documentation
- Each strategy has clear parameter guidance
- Optimal IN/OUT sensitivity levels documented
- Timeframe recommendations specified

### AC4: Safety
- All live-trading scripts must be non-repainting
- Stop-loss enforcement at strategy level
- Position sizing limited via default_qty_value

## File Categories

### Strategies (Production)
Files with `strategy()` declaration, backtestable, with stop-loss/take-profit:
- `es-futures-no-repaint-v1.3.txt` - Latest ES 1M, non-repainting, recommended
- `best-strategy-es-1m.txt` - ES 1M alternate approach
- `nq-est-futres.txt` - NQ multi-timeframe strategy
- `SPX-options.txt` - SPX 15M options strategy
- `GOLD_UZ_OZ.txt` - Gold 1H strategy

### Indicators (Analysis)
Files with `indicator()` declaration, non-tradeable, visual analysis only:
- `color-trend-lite.txt` - Beginner-friendly trend identification
- `Lux-Trailing-BUY_SELL.txt` - Trailing stop visualization
- `best-indicator.txt` - Market condition detection
- `supper-high-low-live.txt` - Support/resistance discovery
- `swing.txt` - Swing trade entry/exit zones

### Special Purpose
- Repainting files (backtesting only): `es-futures-repaint-30s.txt`
- Pattern recognition: `1001-pattern.txt`, `footprint-delta.txt`
- Machine learning: `machine-learning-options.txt`
- Volume analysis: `Volume-profile.txt`, `vp.txt`, `vp-v2.txt`

## Configuration Parameters

### Standard Strategy Inputs
```
input IN = sensitivity for entry signals (5-15 recommended)
input OUT = sensitivity for exit signals (3-12 recommended)
input stopPerCall = stop-loss % for CALL trades (0.03-0.07)
input takePerCall = take-profit % for CALL trades (0.03-0.05)
input stopPerPut = stop-loss % for PUT trades (0.03-0.07)
input takePerPut = take-profit % for PUT trades (0.03-0.05)
```

### Recommended Sensitivity Settings
- **ES 1M**: IN=5, OUT=3
- **NQ 1M**: IN=15, OUT=12 (OR IN=8, OUT=5)
- **SPX 15M**: IN=8, OUT=6
- **GOLD 1H**: IN=10, OUT=7

## Success Metrics

- Signal generation reliability: 99.5% uptime
- Backtested win rate: 70-90% per instrument
- Average trade duration: 5-30 minutes
- Risk/reward ratio: 1:1.5 minimum
- Monthly ROI target: 5-10% (depending on strategy)

## Future Enhancements

1. Machine learning model integration for signal filtering
2. Multi-leg options strategies (spreads, straddles)
3. Correlation analysis between ES/NQ/SPX
4. Enhanced risk management with portfolio-level stops
5. Real-time performance dashboard
6. Automated parameter optimization engine

## Version History

- **v1.0** (2022-08): Initial NQ 1M strategy, 90% accuracy
- **v1.1** (2022-09): ES 1M strategy added, webhook integration
- **v1.2** (2023-H1): SPX options support, repainting variants
- **v1.3** (2024-12): Current release, 49 total scripts, refined ES/NQ strategies

## Compliance & Risk Disclaimer

- All strategies require proper backtesting before live deployment
- Past performance does not guarantee future results
- Traders must manage own risk and position sizing
- Use of non-repainting scripts mandatory for live trading
- Requires active monitoring and manual intervention capability

## Contact & Support

- Primary: donald.nguyen.it@gmail.com
- Patreon: https://www.patreon.com/donaldit
- Documentation: ./docs/ directory
- Issues/Questions: GitHub repository discussions

---

**Document Version**: 1.0
**Last Updated**: 2025-12-26
**Status**: Active
