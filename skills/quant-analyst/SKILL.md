---
name: quant-analyst
description: "Quantitative analyst skill for ETF options trading. Specializes in IV/HV analysis, volatility skew, vol term structure, Greeks management, Kelly sizing, and mispricing verification. Triggers on: any volatility analysis, options pricing questions, Greeks management, Kelly position sizing, mispricing detection, or any request about VIX and implied volatility."
---

# Quant Analyst Skill

## Overview
This skill provides professional quantitative analysis for ETF options trading, covering implied volatility (IV), historical volatility (HV), volatility skew, term structure, Greeks management, Kelly formula, and mispricing detection.

**Triggers:**
- Any analysis involving volatility, options pricing, Greeks, Kelly sizing, or vol term structure
- Quantitative metrics in scheduled analysis tasks
- Even simple questions (e.g., "what's VIX today") should include quantitative context

---

## 1. Multi-Source Data Collection and Cross-Validation

### Data Source List

#### English Sources:
- **WebSearch**: "VIX term structure today", "{ETF} implied volatility options", "options skew index", "IV percentile"
- **WebFetch**:
  - vixcentral.com — VIX, VIX3M, VIX6M, VIX9M term structure
  - marketchameleon.com — IV percentile rank, historical volatility comparison
  - cboe.com — CBOE VIX methodology, skew data

#### Chinese Sources (optional):
- **WebSearch**: 金石數據 "波動率分析", 鉅亨網 "期權隱含波動率", CMoney "期權 IV"

### Data Cross-Validation Rules

**Must follow:**
1. IV/HV data must come from at least 2 independent sources
2. If sources differ by >5%, **must flag as "Uncertain"** and query a third source
3. If two sources differ by >2 VIX points, flag as "Data may be stale"
4. Record data timestamps; note data freshness when using (timestamp must be within 15 minutes)

---

## 2. IV vs HV Analysis

### Implied Volatility (IV) Retrieval

**Step 1:** WebSearch "{ETF symbol} implied volatility today"
- Look for CBOE, marketchameleon, thinkorswim sources
- Record IV (as %), data timestamp

**Step 2:** WebFetch marketchameleon.com (IV Percentile)
- Extract current IV percentile rank (e.g., 45th percentile)
- Represents IV's ranking over the past 52 weeks

### Historical Volatility (HV) Calculation

- **20-day HV**: Calculate log returns standard deviation × √252 for past 20 trading days
- **30-day HV**: Calculate log returns standard deviation × √252 for past 30 trading days
- **Compare results**: IV vs 20d-HV, IV vs 30d-HV

### Analysis Framework

| Indicator | Calculation | Interpretation |
|-----------|------------|----------------|
| IV Rank | (Current IV - 52w Low) / (52w High - 52w Low) × 100 | <30: underpriced, 50-70: normal, >80: overpriced |
| IV vs 20d HV | IV - HV(20d) | >0: IV overpriced, <0: IV underpriced |
| IV vs 30d HV | IV - HV(30d) | Trend assessment, mean reversion opportunity |

---

## 3. Volatility Skew Analysis

### Data Sources

**WebSearch**: "{ETF} options skew analysis", "put/call IV skew {symbol}"
- Look for CBOE SKEW index (applicable to SPY)
- Extract put-side IV vs call-side IV differential

**WebFetch**: vixcentral.com (SKEW data)
- Record SKEW value (>100: negative skew, <100: low skew)

### Skew Quantification

```
Put IV - Call IV = Skew differential
Positive: downside skewed overpriced (market fear)
Negative: upside skewed overpriced (market over-optimism)
```

### Trading Signals

- **Skew >120**: Extreme market fear, puts overpriced → Bull Put Spread preferred
- **Skew 80-100**: Market neutral
- **Skew <80**: Market optimism, calls overpriced → Bear Call Spread preferred

---

## 4. Volatility Term Structure

### Key Ratio Calculations

**Data source:** WebFetch vixcentral.com
- VIX (30-day)
- VIX3M (3-month)
- VIX6M (6-month)

**Ratio calculations:**

| Ratio | Calculation | Term Structure Meaning |
|-------|------------|----------------------|
| Term Ratio | VIX3M / VIX | >1.0: contango (normal structure), <1.0: backwardation (inverted) |
| 6M Ratio | VIX6M / VIX | >1.0: long-term high vol, <1.0: short-term market fear |
| Curve Slope | (VIX6M - VIX) / 180 | Term structure steepness |

### Structure Assessment

- **Contango** (VIX3M > VIX): Market expects volatility to decline, low-vol environment
  - Suitable for: Bull Put Spread (time decay favorable)
- **Backwardation** (VIX3M < VIX): Market expects near-term vol to rise, uncertainty elevated
  - Suitable for: Caution — reduce positions or use wider strikes

---

## 5. Greeks Management

### Data Source

**Portfolio data source**: Read from your local portfolio file (e.g., portfolio_data.json) if available. Otherwise, enter your current Greeks manually or skip if no positions exist.
- Read cumulative Delta, Gamma, Theta, Vega for current positions before each run

### Management Thresholds

| Greek | Safe Range | Warning | Adjustment Action |
|-------|-----------|---------|------------------|
| **Delta** | ±0.15 | ±0.20+ | Hedge or partially close position |
| **Gamma** | <0.02 | >0.03 | Reduce adding new high-gamma positions |
| **Theta** | >0.01/day | <0 | Check if over-long vega |
| **Vega** | ±0.10 | ±0.15+ | Adjust volatility hedge |

### Greeks Adjustment Logic

1. **Daily check** of portfolio Greeks from local portfolio data
2. **Gamma too high** (>0.03): Reduce new short-term options, prioritize closing high-gamma
3. **Delta deviation** (±0.20+): Buy/sell corresponding options to rebalance
4. **Theta negative**: Check if bearing excessive vega risk, consider arbitrage

---

## 6. Adaptive Kelly Formula

### Kelly Base Formula

```
Kelly % = (Win Rate × Avg Win - Loss Rate × Avg Loss) / Avg Win
Max position % = Kelly % × Risk Capital
```

### Reading Actual Data

**Historical performance data** (from your own backtest records):
- win_rate: Average win rate (e.g., 55%)
- avg_win: Average winning return (e.g., 1.2%)
- avg_loss: Average loss (e.g., 1.0%)

**Calculation example:**
```
Assume win_rate=55%, avg_win=1.2%, avg_loss=1.0%
Kelly = (0.55×1.2 - 0.45×1.0) / 1.2 = 0.325 = 32.5%
```

### VIX Risk Adjustment Table

| VIX Range | Adjustment Factor | Actual Kelly % | Meaning |
|----------|------------------|---------------|---------|
| 15-25 | 1.0× | 100% Kelly | Low-vol environment, full Kelly usable |
| 25-30 | 0.7× | 70% Kelly | Moderate vol, reduce position size |
| 30-35 | 0.5× | 50% Kelly | High vol, operate cautiously |
| >35 | 0.25× | 25% Kelly | Extreme fear, minimum position |

### Execution Rules

1. Calculate base Kelly %
2. Query current VIX
3. Apply adjustment factor
4. Actual position = Capital × Adjusted Kelly %

---

## 7. Multi-Scenario Pricing — Quantitative Mispricing Verification

### Core Responsibilities

The Quant analyst is responsible for using technical and quantitative indicators to **verify mispricing judgments from the scenario pricing framework**:

| Responsibility | Specific Approach |
|---------------|------------------|
| Technical verification of gap_pct | If the framework says SPY is overvalued — does technical analysis support this? (How many SDs from 20MA? RSI?) |
| IV cross-validation | If the framework says an ETF is undervalued significantly, but IV is extremely high → market may be pricing risks you haven't modeled |
| Mean reversion probability | How long until gap_pct reverts? (Based on historical volatility and current momentum) |
| Momentum confirmation/denial | Verify 60-day momentum alignment: aligned = confidence ×1.2, contrary = ×0.3 |

### Probability Update Output Format

```
[Multi-Scenario Quantitative Verification — Quant]
- [ETF] gap [+/-X%] ([overvalued/undervalued]):
  - Technical: [+/-X] SDs from 20MA → [supports/contradicts] mispricing judgment
  - Momentum: 60-day momentum [up/down] → [confirms/denies] reversion direction
  - IV signal: IV Rank [X]th → market [has/has not] priced in risk
  - Quant confidence: [X]% this mispricing reverts within 30 days

- [ETF] gap [+/-X%] ([overvalued/undervalued]):
  - Technical: [ETF] deviation from 20MA [+/-X] SDs → [supports/contradicts]
  - Commodity regime override (if applicable): [commodity] change >X% → target price dynamically adjusted
  - Quant confidence: [X]% this mispricing is tradeable
```

## 8. Mispricing Detection (Black-Scholes Benchmark)

### Theoretical Price Calculation

**For major ETFs including SPY, QQQ, GLD, TLT:**
- Inputs: S (spot price), K (strike price), T (time to expiry), r (risk-free rate), σ (IV)
- Use Black-Scholes formula to calculate theoretical call/put prices

### Pricing Deviation Assessment (CREDIT SPREAD ONLY)

⚠️ **All mispricing trade recommendations must be credit spread structures. Mispricing detection tells you "which side is overpriced" — then you sell the overpriced side.**

| Pricing Deviation | Assessment | Credit Spread Strategy | Logic |
|------------------|------------|----------------------|-------|
| Call overpriced (market > theoretical 3%+) | Call premium too expensive | **Bear Call Spread** — sell overpriced Call + buy protection Call | Collect overpriced call premium ✓ |
| Put overpriced (market > theoretical 3%+) | Put premium too expensive | **Bull Put Spread** — sell overpriced Put + buy protection Put | Collect overpriced put premium ✓ |
| Call underpriced (market < theoretical 3%+) | Call premium cheap | **Do nothing** — underpriced = buy opportunity but we don't do debit | Observe, wait for IV recovery ⏸ |
| Put underpriced (market < theoretical 3%+) | Put premium cheap | **Do nothing** — underpriced = buy opportunity but we don't do debit | Observe, wait for IV recovery ⏸ |

**Core logic: Mispricing detection is only used to find the "overpriced" side to sell. Underpriced = wait, because exploiting underpriced requires buying (debit), which violates our strategy.**

### Mispricing Strength Score

| Deviation | Score | Action |
|-----------|-------|--------|
| 3-5% | Mild mispricing | Record but no urgency |
| 5-10% | Moderate mispricing | Worth considering entry, normal Kelly |
| >10% | Significant mispricing | Prioritize consideration, but first ask "why is the market pricing this way?" — market may know something you don't |

**⚠️ Critical thinking: >10% mispricing often has a reason (upcoming event, poor liquidity, structural change). Don't assume the market is wrong.**

### Data Sources

- WebSearch: "{ETF} options pricing today", "options implied volatility"
- Extract bid-ask midpoint as market price
- Calculate deviation from theoretical price

---

## 9. Correlation Matrix

### Monitored Pairs

**Key trading pairs:**
- SPY vs QQQ (large-cap vs tech)
- GLD vs TLT (gold vs long-term bonds)
- XLE vs SPY (energy vs broad market)

### Data Retrieval

**Priority order:**
1. Local portfolio data correlation matrix (if available)
2. WebSearch: "SPY QQQ correlation", "GLD TLT correlation"
3. Calculate past 20-day log returns correlation coefficient

### Correlation Application

| Pair | Correlation | Trade Recommendation |
|------|------------|---------------------|
| SPY-QQQ | >0.85 | Limited pair trade space, prefer single-leg |
| SPY-QQQ | 0.7-0.85 | Pair trade viable, arbitrage possible |
| SPY-QQQ | <0.7 | Strong pair trade signal, correlation reversion priority |
| GLD-TLT | >0.3 | Avoid simultaneous long, poor vol diversification |
| GLD-TLT | <-0.1 | Good risk diversification, can run in parallel |

---

## 10. Credit Spread (CREDIT SPREAD ONLY) Mandatory Rules

### Core Principle

**All recommended trades must be credit spread structures. NEVER recommend the following:**
- ❌ Straddle / Strangle (two-sided options)
- ❌ Naked Call / Naked Put (unprotected options)
- ❌ Debit Spread (net debit structure)
- ❌ Any single-leg options bet

### Permitted Strategies

✅ **Bull Put Spread** (when bullish)
- Sell OTM Put + buy lower Put → net credit collected

✅ **Bear Call Spread** (when bearish)
- Sell OTM Call + buy higher Call → net credit collected

✅ **Call Ratio Spread** (highly bullish)
- Sell 2× Call @ K1 + buy 1× Call @ K2 → net credit, but requires strict Delta management

### Verification Checklist

When recommending a strategy, check:
- [ ] Does the strategy name contain "Spread"?
- [ ] Is maximum loss defined?
- [ ] Is net premium collected?
- [ ] Does it meet the Credit Spread definition?

**If any answer is No, STOP and redesign the trade.**

---

## 11. Data Correctness Validation

### Validation Procedure

**Must execute before every output:**

1. **VIX Cross-Validation**
   - VIX from WebSearch vs local portfolio data VIX
   - Difference >2 points → flag "⚠️ VIX data may be stale, difference: {gap} points"

2. **IV Source Confirmation**
   - How many sources did IV come from? (must be ≥2)
   - Source difference >{5%}? → flag "⚠️ IV data uncertain, source difference {diff}%, recommend querying a third source"

3. **Timestamp Check**
   - Was data collected within the last 15 minutes?
   - If >15 minutes → flag "⚠️ Data collected at {time}, {minutes} minutes ago, recommend refresh"

4. **Local Portfolio Data Alignment**
   - Does a local portfolio file exist?
   - Do the Greeks and position data align with the recommendation?
   - If not → flag and prompt user to verify positions

### Data Freshness Tagging

Add the following next to each data point in the output:
```
VIX 19.5 (13:42 ET, 3 min ago) ✓ Fresh
```

---

## 12. Output Format: Signal Dashboard

### Standard Output Structure

### Signal Dashboard Table

```
┌─────────────────────────────────────────────────────────────────────┐
│ ETF Quantitative Signal Dashboard                    Time: HH:MM ET |
├─────────────────────────────────────────────────────────────────────┤
│ Indicator        │ Current Value  │ Status    │ Recommendation │ Conf│
├─────────────────────────────────────────────────────────────────────┤
│ SPY IV Rank      │ 45th %ile      │ Neutral   │ No bias        │ 60%│
│ SPY IV vs 20HV   │ +1.8%          │ Sl. high  │ Bull Put       │ 70%│
│ QQQ IV Rank      │ 62nd %ile      │ Sl. high  │ Cautious bull  │ 65%│
│ SKEW (SPY)       │ 118            │ Fear      │ Put overpriced │ 75%│
│ VIX Term         │ Contango 1.15  │ Normal    │ Decay favorable│ 80%│
│ VIX Adj Kelly    │ 22% (0.7×)     │ Mid-vol   │ 22% of capital │ 70%│
├─────────────────────────────────────────────────────────────────────┤
```

### Kelly Allocation Recommendation

```
Capital: {{YOUR_CAPITAL}} (configure with your total trading capital)
Base Kelly: 32.5% of capital
VIX Adjustment (VIX=26): 0.7× multiplier
→ Actual Kelly: 22.75% of capital

Suggested initial position: 22-23% of capital
(Reserve some cash for unexpected opportunities)
```

### Greeks Rebalancing Check

```
Current Portfolio Greeks (from local portfolio data, if available):
- Δ (Delta):     +0.08  ✓ Within ±0.15 range
- Γ (Gamma):     +0.015 ✓ <0.02 safe
- Θ (Theta):     +$12/day ✓ Normal
- ν (Vega):      +0.05  ✓ <0.10 safe

Action: No adjustment needed, portfolio healthy
```

### Data Freshness Indicators

```
Data collected at [timestamp] ([N] minutes ago) ✓ Fresh
├─ VIX (CBOE):           19.5  [HH:MM ET]    ✓ Fresh
├─ SPY IV (MC):          16.8% [HH:MM ET]    ✓ Fresh
├─ QQQ IV (ThinkorSwim): 17.2% [HH:MM ET]   ✓ Fresh
├─ SKEW (CBOE):          118   [HH:MM ET]    ✓ Fresh
├─ Portfolio Greeks:      [from portfolio_data.json (if available)]
└─ Note: All data <15 minutes, high credibility

VIX Validation: WebSearch [X] vs local portfolio data [X] (difference [Y] points) ✓ Consistent
```

---

## 13. Execution Checklist

**Before each use of this Skill:**

- [ ] Have latest IV data been obtained via WebSearch or WebFetch?
- [ ] Does IV data come from at least 2 independent sources with difference <5%?
- [ ] VIX validated across sources (difference <2 points)?
- [ ] Current portfolio Greeks read from local data (if available)?
- [ ] Does the recommended trade strategy use Bull Put Spread or Bear Call Spread?
- [ ] Are all data timestamps and freshness noted?
- [ ] Have all pricing deviations >3% from theoretical been checked?
- [ ] Has VIX-adjusted Kelly formula been applied?

**If any item is ❌, stop and fill in missing information.**

---

## 14. Tool Mapping

| Task | Tool | Notes |
|------|------|-------|
| VIX term structure | WebFetch vixcentral.com | Most authoritative source |
| IV percentile | WebFetch marketchameleon.com | Real-time updates |
| Options skew | WebSearch + WebFetch CBOE | US equity options standard |
| Chinese vol articles | WebSearch 金石, 鉅亨網, CMoney | Reference opinions (optional) |
| Real-time options quotes | WebFetch thinkorswim or real-time API | Multi-source verification required |
| Portfolio Greeks | Read from local portfolio data file (optional) | Local data |
| Backtest results | Read from historical performance data | Historical win rate |

**Prohibited:** ❌ Do not use deprecated tools

---

## 15. Common Scenario Triggers

### Scenario 1: "What's VIX today"
→ In addition to answering the VIX number, **must provide**:
- VIX rank (vs 52 weeks)
- Term structure (Contango/Backwardation)
- Quantitative trading implications

### Scenario 2: "SPY options recommendation"
→ **Must execute full workflow**:
1. IV Rank + IV vs HV
2. Skew analysis
3. Mispricing detection
4. Kelly position sizing
5. Greeks check
6. Credit spread recommendation (Bull Put / Bear Call)

### Scenario 3: "Adjust existing positions"
→ **Must check**:
1. Read current Greeks from local portfolio data
2. Determine if Delta exceeds ±0.15 or Gamma >0.02
3. Recommend rebalancing trade (must be credit spread)
4. Calculate new Greeks target

### Scenario 4: "Position risk management"
→ **Auto-execute**:
1. Read local portfolio data
2. Check VIX (may need refresh)
3. Apply VIX-adjusted Kelly
4. Calculate whether partial close is needed
5. Flag abnormal Greeks

---

## 16. Quality Assurance

### Confidence Scoring

Provide **Confidence** with each recommendation:
- **90-100%**: Multiple quantitative indicators aligned, fresh data
- **70-89%**: Most indicators aligned, at least 2 data sources
- **50-69%**: Mixed indicators or slightly stale data
- **<50%**: ❌ Do not provide recommendation, request additional information

### Handling Uncertainty

If any of the following occur, **must stop and flag**:
```
⚠️ Uncertain signal, reasons:
- IV sources differ by 7% (exceeds 5% threshold)
- VIX from WebSearch vs local portfolio data differ by 3.2 points (exceeds 2-point threshold)
- Data collected 25 minutes ago (exceeds 15-minute freshness threshold)

Recommendation: Please re-query latest data or confirm manually
```

---

## 17. Critical Thinking Framework

### Model Skepticism

The quant analyst's greatest risk is **over-trusting models**. Before each output, ask:

```
[Model Assumption Check]
1. Black-Scholes assumes normal distribution — but markets have fat tails. Does my mispricing judgment hold in extreme scenarios?
2. IV Rank is based on the past 52 weeks — if market structure has changed (e.g., 0DTE proliferation), is historical rank still valid?
3. Kelly formula assumes independent trials — but consecutive losses may be correlated (systemic risk). Am I oversizing?
```

**Rule: Each report must identify at least 1 scenario where a model assumption may fail.**

### Devil's Advocate — Quant Version

After producing each quantitative signal, force a counter-argument:

```
[Quantitative Signal] SPY IV Rank 75th percentile → overpriced → sell credit spread
[Counter-argument]
1. High IV may be justified: if FOMC + CPI both land this week, IV should be high
2. IV Rank based on past 52 weeks, but if last year was extremely low-vol, 75th may just be "returning to normal"
3. Historical mean reversion assumes no structural change, but 0DTE options have changed the vol surface
[Conclusion] Reduce confidence from 80% to 65%; need to cross-check event calendar
```

### Data Skepticism

Do not blindly trust data sources. For each data point, ask:

| Skepticism Dimension | Specific Question | Action |
|---------------------|------------------|--------|
| Timeliness | Is this data intraday real-time or yesterday's close? | Tag timestamp, discount if >15 min |
| Representativeness | Is this IV ATM or a specific strike? | Confirm if it's 30-delta IV or ATM IV |
| Calculation method | Different sources may calculate IV differently | Note source's calculation methodology |
| Survivorship bias | Does backtest win rate only include successful ETFs? | Check if underperforming ETFs are included |

### Contradiction Detection

Quantitative indicators frequently contradict each other. Must actively identify:

```
⚠️ Quantitative contradiction:
- IV Rank high (sell signal) but Vol Term Structure is backwardation (buy signal / panic)
- Explanation 1: Short-term event pushed up VIX, but long-term still stable → can sell, but reduce size
- Explanation 2: Market smells systemic risk → should not sell vol, wait for signal clarity
- Most likely: [choose and explain]
- If wrong, consequences: [specific loss estimate]
```

### "If the Model is Wrong" Mandatory Paragraph

Each quantitative report must end with:

```
### If Quantitative Signals Are Wrong
- **Model failure scenario**: [e.g., "If VIX breaks 40, Black-Scholes normal assumption completely fails"]
- **Historical precedent**: [List examples of similar model failures, e.g., March 2020]
- **Maximum loss**: [Worst case calculation based on current recommendation]
- **Safety valve**: [Under what conditions to automatically stop trading, e.g., "Stop all new positions when VIX >35"]
```

### Overfitting Warning

If a quantitative strategy's backtest results are **too good** (win rate > 80%), auto-trigger warning:
```
⚠️ Overfitting risk: 85% win rate may stem from:
1. Backtest period coincidentally had low volatility (2017, 2019)
2. Only looked at favorable ETFs (survivorship bias)
3. Parameters over-optimized (Kelly, strike selection)
Recommendation: Reduce position size by 30% as safety margin
```

## Notes

This Skill follows these principles:
- ✅ Credit spreads first (Bull Put / Bear Call Spread)
- ✅ Multi-source data cross-validation
- ✅ Strict Greeks management
- ✅ Kelly formula adaptive to VIX
- ✅ All recommendations include confidence scoring
- ✅ All data includes timestamps and freshness indicators
- ✅ Automatic mispricing and pricing deviation checks
- ✅ **Critical thinking: every signal undergoes devil's advocate and model skepticism review**
- ✅ **Contradiction detection: actively identify contradictions between indicators and explain**

---

**Version:** 2.0
