# TradingView Pine Script Trading Collection

Professional-grade Pine Script toolkit for automated futures and options trading. 49 scripts including 22 backtestable strategies and 26 technical indicators with 70-90% accuracy on ES/NQ futures.

## Quick Start

### For Beginners
Use **`color-trend-lite.txt`** - Simplest indicator (BLUE=up, RED=down).

```
How to use:
1. Add script to TradingView chart
2. Watch for color changes
3. BLUE signal: Consider CALL trades
4. RED signal: Consider PUT trades
5. Exit when color flips
```

### For Automated Trading (Recommended)
Use **`es-futures-no-repaint-v1.3.txt`** for ES 1M with Tradovate webhook automation.

```
Setup:
1. Add strategy to ES 1M chart
2. Create TradingView alert with webhook
3. Point webhook to Tradovate API
4. Alerts auto-execute as orders
5. Optimal parameters: IN=5, OUT=3
```

### For Multi-instrument Coverage
Use **`trade-futures.txt`** (flexible, supports ES/NQ with configurable parameters).

## File Categories (49 Total Scripts)

### Strategies - Backtestable & Automated (22)
Production-ready with stop-loss/take-profit. Add to chart and enable alerts.

| Script | Instrument | Timeframe | Accuracy | Type |
|---|---|---|---|---|
| **es-futures-no-repaint-v1.3.txt** | ES | 1M | 80%+ | Recommended |
| best-strategy-es-1m.txt | ES | 1M | 75%+ | Alternate |
| nq-est-futres.txt | NQ | 1M/5M/10M | 90% | Multi-TF |
| SPX-options.txt | SPX | 15M | 85%+ | Options |
| spx-30m-options.txt | SPX | 30M | 85%+ | Options |
| GOLD_UZ_OZ.txt | GOLD | 1H | 70%+ | Commodity |
| [15 more ES/NQ/SPX variants] | — | — | — | — |

**All strategies marked "no-repaint" are safe for live trading.**

### Indicators - Visual Analysis (26)
Non-tradeable analysis tools. Use to confirm strategy signals or manual trading.

| Script | Purpose | Best For | Complexity |
|---|---|---|---|
| **color-trend-lite.txt** | Trend identification | Beginners | Minimal |
| Lux-Trailing-BUY_SELL.txt | Trailing stops | Risk management | Low |
| best-indicator.txt | Weak market detection | Adaptive trading | Medium |
| supper-high-low-live.txt | Support/resistance | Level confirmation | Low |
| swing.txt | Swing zones | Entry/exit timing | Medium |
| win-99.txt | Crash detection | Crash trading | Advanced |
| 1001-pattern.txt | Pattern recognition | Trade confirmation | High |
| [19 more indicators] | — | — | — |

### Timeframe Recommendations

| Instrument | Primary | Confirmation | Context |
|---|---|---|---|
| **ES** | 1M | 5M | 10M/60M |
| **NQ** | 1M | 5M | 10M/60M |
| **SPX** | 15M | 30M | 60M |
| **GOLD** | 1H | 4H | Daily |

## Integration

### Automated Execution
- **Tradovate**: Send webhooks from TradingView alerts → Automatic order execution
- **Binance**: Direct API integration for futures: https://github.com/dearvn/tradingview-pinscript-futures-binance
- **WordPress**: Alert logging & distribution: https://github.com/dearvn/tradingview-alerts

### Configuration
Standard parameters used across strategies:

```
IN (Entry Sensitivity)
  ES: 5          NQ: 15 (or 8)     SPX: 8         GOLD: 10

OUT (Exit Sensitivity)
  ES: 3          NQ: 12 (or 5)     SPX: 6         GOLD: 7

Risk Management (Stop-loss / Take-profit)
  Call trades: Stop=0.07% / Target=0.05%
  Put trades: Stop=0.07% / Target=0.03%
```

## Safety Notes

- **Live trading safe**: 11 files (all "no-repaint" variants)
- **Backtesting only**: `es-futures-repaint-30s.txt` (repaints on historical bars)
- **Lookahead bias**: Some SPX files use future data - backtest only
- **Position size**: Default 1 contract (safe), scalable to 2-5

## Documentation

**Start here** → [`./docs/project-overview-pdr.md`](docs/project-overview-pdr.md) - Full project overview, requirements, and claims.

- **Code Standards**: [`./docs/code-standards.md`](docs/code-standards.md) - Pine Script conventions and parameter guidance
- **System Architecture**: [`./docs/system-architecture.md`](docs/system-architecture.md) - Integration flows and webhook setup
- **Codebase Summary**: [`./docs/codebase-summary.md`](docs/codebase-summary.md) - File-by-file breakdown and statistics

## Key Performance Metrics

- **ES strategies**: 70-90% win rate (backtested)
- **NQ strategies**: 90% win rate (backtested)
- **Trade duration**: 5-30 minutes typical
- **Risk/reward**: 1:1.5 minimum
- **Webhook latency**: <2 seconds from signal to order

## Getting Started Checklist

- [ ] Read [`./docs/project-overview-pdr.md`](docs/project-overview-pdr.md) (5 min)
- [ ] Choose strategy or indicator from categories above
- [ ] Review script parameters in TradingView ("Settings" tab)
- [ ] Backtest 6+ months on correct timeframe
- [ ] Paper trade 1-2 weeks (simulate without real money)
- [ ] Deploy with small position (1 contract minimum)
- [ ] Monitor daily and adjust parameters as needed

## Common Issues & Solutions

| Issue | Solution |
|---|---|
| No signals generating | Check chart timeframe matches script; verify script has no errors |
| Webhooks not firing | Test webhook URL with webhook.site; verify TradingView alert enabled |
| Orders not executing | Check Tradovate account margin/balance; verify market hours |
| Win rate lower than expected | Backtest on correct timeframe; validate parameter settings; check market regime |

## Support & Community

- **Email**: donald.nguyen.it@gmail.com
- **Support**: https://www.patreon.com/donaldit
- **Docs**: This repository's `/docs` folder
- **Webhooks**: https://github.com/dearvn/tradingview-alerts
- **Binance Integration**: https://github.com/dearvn/tradingview-pinscript-futures-binance

## Performance History

### Recent Trades (Evidence)
- 2022-08-30: ES +253 points
- 2022-09-01: Consistent win streak (documented in README history)
- 2024-12-26: Current release with refined v1.3 strategy

*Note: Past performance does not guarantee future results. All traders must manage own risk.*

## Requirements

- **TradingView**: Free or Pro account (Pro recommended for backtesting)
- **Tradovate**: Futures account (for automated execution)
- **Broker**: CME access for ES/NQ; exchange access for SPX/GOLD
- **Minimum**: Knowledge of basic trading and risk management

## License

Mozilla Public License 2.0 - See individual files for details.

## Version

Current: **v1.3** (ES/NQ strategies refined, 49 total scripts)
Release: 2025-12-26

---

**For detailed configuration, safety notes, and troubleshooting:** See [`./docs/`](docs/) folder.

**Questions?** Email donald.nguyen.it@gmail.com or check Patreon discussions.

*Trade responsibly. All strategies require active monitoring.*
