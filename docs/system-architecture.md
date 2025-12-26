# System Architecture - TradingView Pine Script Trading System

## High-Level System Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                     TRADINGVIEW PLATFORM                         │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │           Pine Script Strategies & Indicators             │   │
│  │  - 22 Strategies (ES, NQ, SPX, GOLD, etc.)              │   │
│  │  - 26 Indicators (Technical Analysis Tools)             │   │
│  │  - Real-time Signal Generation                          │   │
│  └──────────────────────────────────────────────────────────┘   │
│                              ↓                                    │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │              Alert & Webhook Engine                       │   │
│  │  - IN/OUT signals                                        │   │
│  │  - CALL/PUT signals                                      │   │
│  │  - Frequency: once per bar                              │   │
│  └──────────────────────────────────────────────────────────┘   │
└────────────────────────────┬─────────────────────────────────────┘
                             │
                    WEBHOOK TRIGGER
                             │
    ┌────────────────────────┼────────────────────────┐
    │                        │                        │
    ▼                        ▼                        ▼
TRADOVATE           WORDPRESS PLUGIN         BINANCE FUTURES
ORDER ROUTING       ALERT LOGGING           DIRECT API
    │                        │                        │
    │                        │                        │
    ▼                        ▼                        ▼
FUTURES              NOTIFICATION         EXCHANGE
EXECUTION            DISTRIBUTION          EXECUTION
```

## Component Architecture

### 1. TradingView Pine Script Layer

#### Purpose
Generate trading signals based on technical analysis of real-time price data.

#### Components

**A. Strategy Scripts (22 total)**
- Backtestable algorithms with entry/exit logic
- Built-in risk management (stop-loss, take-profit)
- Position sizing and pyramiding control
- Use `strategy()` declaration

**Key Strategies:**
```
ES (E-mini S&P 500)         NQ (Nasdaq-100)              Options
├─ es-futures-no-repaint-v1.3   ├─ nq-est-futres          ├─ SPX-options
├─ best-strategy-es-1m          ├─ nqu2023-1m             ├─ spx-30m-options
├─ ES1M-BEST                    ├─ NQU2023-10M            └─ alert-options-trading-spy
└─ [6 ES variants]              └─ [5 NQ variants]

GOLD                         Multi-Instrument            Special
├─ GOLD_UZ_OZ               ├─ trade-futures            ├─ win-99
│                            ├─ simple-entry-100win     └─ no-repain-test-futures
```

**B. Indicator Scripts (26 total)**
- Pure analysis tools (non-tradeable)
- Visual signal overlays
- Use `indicator()` declaration
- Support manual and automated confirmation

**Key Indicators:**
```
Trend Analysis          Pattern Recognition         Advanced
├─ color-trend-lite     ├─ 1001-pattern             ├─ ai-futures-v1
├─ Lux-Trailing         ├─ footprint-delta          ├─ ai-trend
├─ best-indicator       └─ swing                    ├─ ai-prediction-rsi
│                                                    └─ machine-learning-options
Support/Resistance      Volume Analysis
├─ supper-high-low      ├─ Volume-profile
├─ highest-lowest-visual ├─ vp (v1, v2)
└─ sideway-zone         └─ vwap_trading_futures
```

#### Signal Generation Logic

**Standard Entry/Exit Flow:**
```
Market Data (OHLCV)
    ↓
Technical Indicators (EMA, RSI, ATR, etc.)
    ↓
Condition Evaluation
    ├─ Entry Condition? → Alert "IN" / "CALL" / "PUT"
    ├─ Exit Condition?  → Alert "OUT"
    └─ No Condition     → Wait
    ↓
Strategy Entry/Exit (if strategy)
    ├─ strategy.entry("Long/Short", ...)
    └─ strategy.exit(..., stop=level, limit=target)
    ↓
Webhook Trigger
```

**Example Signal Logic (ES 1M):**
```
Entry Condition = (EMA10 > EMA50) AND (RSI < 70) AND (Volume > SMA)
Exit Condition = (Close below SMA) OR (Time elapsed > 20 bars) OR (Stop triggered)

Alert("IN") when Entry = True
Alert("OUT") when Exit = True
```

### 2. Alert & Webhook Engine

#### Purpose
Convert Pine Script alerts into API calls to trading platforms.

#### Architecture

**Alert Types:**
```
Signal              Frequency         Delivery
├─ IN               Once per bar      Webhook
├─ OUT              Once per bar      Webhook
├─ CALL             Once per bar      Webhook + Alert
├─ PUT              Once per bar      Webhook + Alert
└─ Custom           On demand         Manual
```

**Webhook Integration Points:**
1. **TradingView → Tradovate (Primary)**
   - Endpoint: Tradovate Webhook URL
   - Format: JSON with signal name
   - Example: `{"signal": "IN", "instrument": "ES", "timeframe": "1M"}`

2. **TradingView → WordPress Plugin**
   - Endpoint: WordPress REST API
   - Format: Signal name + metadata
   - Reference: https://github.com/dearvn/tradingview-alerts

3. **TradingView → Binance Direct**
   - Endpoint: Binance Futures API
   - Format: Order request JSON
   - Reference: https://github.com/dearvn/tradingview-pinscript-futures-binance

#### Alert Configuration in TradingView

**Step-by-step:**
1. Click Alert icon on chart
2. Select strategy/indicator with signals
3. Set condition: `alert triggered`
4. Enter webhook URL: `https://your-tradovate-webhook.com/api/...`
5. Set frequency: `Once per bar close`
6. Message format: `{{exchange}}:{{ticker}} IN` or `OUT`

### 3. Tradovate Integration Layer

#### Purpose
Execute orders on Tradovate platform based on webhook signals.

#### Architecture

**Webhook → Order Execution Flow:**
```
Webhook Received (IN/OUT/CALL/PUT)
    ↓
Parse Signal & Validate
    ├─ Extract: signal name, timestamp, instrument
    └─ Validate: matching symbol, within trading hours
    ↓
Order Construction
    ├─ IN → Market BUY (or limit order)
    ├─ OUT → Market SELL (close position)
    ├─ CALL → Market BUY (directional)
    └─ PUT → Market SELL (directional)
    ↓
Risk Management Check
    ├─ Position size ≤ max (usually 1 contract for testing)
    ├─ Stop-loss set (via strategy.exit)
    ├─ Account balance sufficient
    └─ Within trading hours (6:30 AM - 12:30 PM UTC-8 for ES)
    ↓
Order Submission
    └─ Tradovate API ExecuteOrder call
    ↓
Order Confirmation
    ├─ Order ID returned
    ├─ Status: Pending → Working → Filled
    └─ Execution price recorded
    ↓
Position Monitoring
    ├─ Monitor stop-loss trigger
    ├─ Monitor take-profit level
    └─ Auto-exit at levels or manual override
```

**Tradovate API Requirements:**
- Account setup with API credentials
- Sufficient margin for position size
- Order routing to CME (ES/NQ futures) or SPX exchange

### 4. WordPress Alert Plugin Layer

#### Purpose
Alternative alert distribution and historical logging.

#### Architecture

**Signal Flow:**
```
TradingView Alert
    ↓
WordPress REST Endpoint
    ├─ Receive alert data
    ├─ Log to database
    └─ Format notification
    ↓
Distribution
    ├─ Email notification
    ├─ SMS/Push alerts
    ├─ Telegram bot
    └─ Discord webhook
    ↓
Dashboard Display
    ├─ Real-time signal log
    ├─ Win/loss tracking
    └─ Performance metrics
```

**Reference:**
- Plugin: https://github.com/dearvn/tradingview-alerts
- Setup: Install on WordPress, configure webhook in TradingView

### 5. Binance Direct Integration Layer

#### Purpose
Trade Binance Futures directly without Tradovate intermediary.

#### Architecture

```
TradingView Signal
    ↓
Webhook to Binance Handler
    ├─ Parse signal
    ├─ Build Binance order
    └─ Authenticate with API keys
    ↓
Binance API Call
    ├─ POST /fapi/v1/order
    ├─ PATCH /fapi/v1/positionSide
    └─ DELETE /fapi/v1/order (for exits)
    ↓
Order Status
    ├─ NEW → PARTIALLY_FILLED → FILLED
    └─ Callback to logging system
```

**Reference:**
- Integration: https://github.com/dearvn/tradingview-pinscript-futures-binance
- Requires: Binance futures account + API keys

### 6. Data Flow Diagrams

#### Real-time Signal Flow (1M ES)

```
Time: 09:31:00
Chart Bar: 1M candle starting at 09:31

09:31:30 - During bar
├─ Price: 5234.50
├─ EMA10: 5233.20
├─ EMA50: 5230.10
├─ RSI: 45
├─ Status: WAITING (not all conditions met)

09:31:59 - Bar close
├─ Close: 5235.00
├─ EMA10: 5233.50
├─ EMA50: 5230.20
├─ RSI: 48
├─ Condition Check: EMA10 > EMA50 ✓ AND RSI < 70 ✓ AND Volume ✓
├─ Result: ENTRY CONDITION MET
├─ Signal: Alert("IN") fired
└─ Webhook: POST to Tradovate

Latency:
  TradingView processing: ~50ms
  Webhook delivery: ~100ms
  Tradovate order routing: ~200ms
  CME execution: ~500-1000ms
  Total: ~1-2 seconds from bar close to execution
```

#### Multi-bar Confirmation Flow

```
Bar 1 (09:30): EMA10 crosses above EMA50 - potential setup
Bar 2 (09:31): RSI < 70 and Volume confirmed - ENTRY SIGNAL
                Alert("IN") → Webhook → Tradovate → BUY
                Status: IN TRADE

Bar 3-20 (09:32-09:50): Hold position, monitor exit
Bar 21 (09:50): Close < EMA50 or 20 bars elapsed → EXIT SIGNAL
                Alert("OUT") → Webhook → Tradovate → SELL
                Status: OUT, profit/loss booked
```

### 7. Risk Management Architecture

#### Position Sizing
```
Strategy Setting: default_qty_value = 1
  ├─ 1 contract per signal (conservative)
  ├─ Scalable to 2-5 for experienced traders
  └─ Risk per trade: Fixed dollar amount

Example: ES 1 contract
  ├─ Point value: $50
  ├─ Stop-loss: 7 points = $350 risk
  ├─ Take-profit: 5 points = $250 reward
  └─ Risk/Reward: 1:0.71 (not ideal, adjust settings)
```

#### Stop-Loss Implementation
```
strategy.exit() enforcement
├─ Stop price: strategy.position_avg_price * (1 - stopPercentage)
├─ Trigger: When price touches stop
├─ Execution: Market order at stop level (slippage possible)
└─ Guarantee: Hard stop via strategy logic

Real-time monitoring
├─ Manual override capability
├─ Trailing stop adjustment (optional)
└─ Emergency exit hotkey (trader responsibility)
```

#### Take-Profit Levels
```
Strategy.exit() with limit
├─ Limit price: strategy.position_avg_price * (1 + takePercentage)
├─ Execution: Limit order filled if price reaches level
├─ Partial fills: OK, position closed when filled
└─ Timeout: Max hold time = 20-30 bars (optional)
```

### 8. Multi-timeframe Analysis Architecture

#### Data Hierarchy
```
Primary Chart: 1M (ES/NQ entry timing)
    ↓
request.security("3M", ...)    - Early confirmation
    ↓
request.security("5M", ...)    - Key resistance
    ↓
request.security("10M", ...)   - Trend direction
    ↓
request.security("60M", ...)   - Broader context
    ↓
request.security("240M", ...)  - Daily bias
```

#### Example: 1M ES with 5M Context
```
Signal: IN on 1M if:
  ├─ 1M: EMA10 > EMA50 AND RSI < 70
  ├─ 5M: EMA10 > EMA50 (confirmation)
  ├─ 10M: Price > SMA200 (trend bias)
  └─ CONDITION: All timeframes aligned

Result: Strong entry with confluence
```

### 9. Scalability & Performance

#### Current Capacity
```
Signals per day: 50-200 (varies by instrument/timeframe)
Concurrent positions: 1-3 (strategy.pyramiding = 1)
Max daily orders: 500+ (TradingView alert limit)
Latency tolerance: < 2 seconds acceptable

Platform limits:
├─ TradingView: 24/7 monitoring (plus plan dependent)
├─ Tradovate: Standard API rate limits
├─ Binance: 1,200 requests/minute per API key
└─ WordPress: Standard server capacity
```

#### Scaling Strategy
1. **Short-term** (current): Single timeframe per instrument
2. **Medium-term** (6mo): Add multiple timeframes with filtering
3. **Long-term** (1yr): Machine learning signal optimization

### 10. Monitoring & Observability

#### Key Metrics
```
Signal Metrics:
├─ Signals generated per hour
├─ Signal accuracy (win % vs loss %)
├─ Average hold time per trade
└─ Win/loss ratio

Execution Metrics:
├─ Webhook delivery success rate (target: >99%)
├─ Order execution latency (target: <2sec)
├─ Slippage per fill
└─ Partial fill %

System Metrics:
├─ TradingView chart uptime
├─ Webhook endpoint availability
├─ Tradovate API response time
└─ Database transaction rate
```

#### Logging Strategy
```
Log Level: INFO
├─ Each signal generated
├─ Each webhook fired
├─ Each order submitted/filled
└─ Each position closed

Log Retention: 1 year
├─ Detailed for recent 3 months
├─ Summarized for older data
└─ Full backups weekly
```

### 11. Testing & Validation Workflow

#### Pre-deployment
```
1. Backtesting (6+ months data)
   ├─ Win rate verification
   ├─ Max drawdown analysis
   └─ Profit factor calculation

2. Paper Trading (1-2 weeks)
   ├─ Live signal generation without real orders
   ├─ Webhook delivery testing
   └─ Latency measurement

3. Small Position Live (1-2 weeks)
   ├─ Real order execution
   ├─ Slippage observation
   ├─ Risk management verification
   └─ Trader comfort assessment

4. Production (ongoing)
   ├─ Daily P&L tracking
   ├─ Win/loss rate monitoring
   └─ Parameter adjustment as needed
```

### 12. Emergency & Recovery Procedures

#### Signal Failure
```
If no signals generated:
1. Check TradingView alert status (green circle icon)
2. Verify script has no errors (red X)
3. Confirm chart timeframe matches script expectation
4. Manual override: Create alert manually
5. Contact: donald.nguyen.it@gmail.com
```

#### Webhook Failure
```
If webhooks not triggering:
1. Check Tradovate webhook URL validity
2. Test webhook with webhook.site (free tool)
3. Verify TradingView alert message format
4. Check firewall/whitelist Tradovate IPs
5. Fallback: Enable email alerts + manual execution
```

#### Order Execution Failure
```
If orders not filling:
1. Check Tradovate account balance/margin
2. Verify market is open (trading hours)
3. Check position limit not exceeded
4. Verify order format matches Tradovate parser
5. Manual execution via Tradovate UI
```

---

## System Reliability & Uptime SLA

```
TradingView:   99.9% (SLA: 45min/month downtime max)
Tradovate:     99.5% (SLA: ~3.6hrs/month downtime max)
Webhook:       99.95% (redundant infrastructure)
Overall:       ~99.3% effective uptime
```

---

**Document Version**: 1.0
**Last Updated**: 2025-12-26
**Architecture Pattern**: Event-driven, Webhook-based, Multi-platform
