# Multi-Analyst Desk

**A team of AI specialists that think independently, disagree productively, and trade with conviction.**

> Deploy a virtual analyst desk — macro, sector, quant, event, and a chief strategist — that debates the market in real time and delivers credit spread recommendations you can act on immediately.

---

## The Problem with Single-Model Analysis

Every AI trading tool gives you one opinion. One model reads the same data, runs the same logic, and returns the same answer — every time.

Real trading desks do not work that way.

When macro sees a Fed pivot signal but quant flags stretched IV, those analysts argue. The friction is the value. Consensus without conflict is a warning sign, not a green light.

Single-model AI collapses that disagreement into a point estimate. You get a recommendation without the reasoning, confidence without the calibration, and no way to know what the model got wrong.

---

## The Solution

**Multi-Analyst Desk** runs four independent specialist AIs in parallel. Each analyst reads the same market, reaches their own conclusion, and defends it against the others. A chief strategist synthesizes the conflict — not by averaging, but by picking a side and explaining why.

The result: **structured disagreement as a trading signal**.

- Analysts are dynamically weighted by market regime (macro leads in risk-off; quant leads in IV-driven regimes)
- Every analyst must argue the opposite position (Devil's Advocate)
- Every analyst flags where their data might be wrong
- Consensus between analysts triggers a **position-size reduction**, not confidence

The engine powering the analysis is **GTMVF** (Game-Theoretic Market Valuation Framework): each ETF receives a Nash equilibrium price derived from probability-weighted macro scenarios. A gap of more than 5% between market price and equilibrium price is your actionable mispricing signal.

---

## How It Works

```
                    +----------------------------------+
                    |          User Prompt             |
                    +--------------+-------------------+
                                   |
                                   v
                    +----------------------------------+
                    |          MARKET INTEL            |
                    |   Real-time WebSearch            |
                    |   English + Chinese sources      |
                    |   Cross-validated (1-5 scale)    |
                    +--------------+-------------------+
                                   |
         +-------------------------+--------------------------+
         |                         |                          |
         v                         v                          v
+------------------+   +------------------+   +-----------------+
|  MACRO ANALYST   |   | SECTOR ANALYST   |   |  QUANT ANALYST  |
| Fed, yield curve |   | ETF rotation     |   | IV/HV, Greeks   |
| CPI, GDP, DXY    |   | sector flows     |   | Kelly, skew     |
| H0/H2/H5 update  |   | relative strength|   | GTMVF verify    |
+--------+---------+   +--------+---------+   +--------+--------+
         |                      |                       |
         |        +-------------+-------------+         |
         |        |      EVENT ANALYST        |         |
         |        |  FOMC, NFP, CPI calendar  |         |
         |        |  geopolitical risk        |         |
         |        |  H1/H4 probability updates|         |
         |        +-------------+-------------+         |
         |                      |                       |
         +----------------------+-----------------------+
                               |
                               v
            +------------------------------------------+
            |            TRADE ANALYST                  |
            |          Chief Strategist                 |
            |                                           |
            |  1. Scan analyst disagreements            |
            |  2. Weight analysts by market regime      |
            |  3. Pick a side and explain why           |
            |  4. Reduce position size on consensus     |
            +------------------+------------------------+
                               |
                               v
            +------------------------------------------+
            |         OPPORTUNITY DETECTOR              |
            |   Signal vs. noise classification         |
            |   Intraday anomaly detection              |
            +------------------+------------------------+
                               |
                               v
            +------------------------------------------+
            |    Credit Spread Recommendations          |
            |    Strike . DTE . Kelly Size              |
            |    14/7/3 DTE management checkpoints      |
            +------------------------------------------+
```

**Data flow:**
1. **Gather** — Market Intel pulls live English + Chinese financial sources simultaneously
2. **Analyze** — Four analysts work independently; each updates GTMVF hypothesis probabilities
3. **Disagree** — Chief Strategist identifies analyst conflicts and weights by current regime
4. **Synthesize** — Specific credit spread recommendations with explicit reasoning for the side chosen
5. **Filter** — Opportunity Detector classifies output as actionable signal or stand-down

---

## Skills

| Skill | Role | What It Produces |
|-------|------|------------------|
| **trade-analyst** | Chief Strategist — orchestrates all analysts and owns the final call | Pre-market, intraday, and post-market reports with credit spread recommendations |
| **macro-analyst** | Economic regime and Fed policy specialist | GTMVF hypothesis updates (H0 soft landing, H2 Fed pivot, H5 recession); ETF regime implications |
| **sector-analyst** | ETF rotation and relative strength specialist | Sector flow signals; XLE/SPY, GLD/SPY, TLT/SPY ratio analysis; rotation triggers |
| **quant-analyst** | Volatility and options pricing specialist | IV rank, HV, skew, term structure; Kelly position sizing; GTMVF mispricing verification |
| **event-analyst** | Economic calendar and geopolitical risk specialist | Event-driven IV crush timing; FOMC/NFP/CPI risk windows; H1 geopolitical and H4 oil shock updates |
| **market-intel** | Real-time intelligence gatherer | Cross-validated news from English + Chinese sources; credibility scores; structured ETF impact assessments |
| **opportunity-detector** | Intraday anomaly detector and noise filter | Signal vs. noise classification; stand-down calls; fast-turnaround credit spread opportunities |

### Scheduled Tasks

| Task | Trigger | Output |
|------|---------|--------|
| **premarket-ai-analysis** | 8:30 AM ET | Full 4-analyst report, GTMVF table, hypothesis updates, 3+ trade recs |
| **intraday-ai-analysis** | Hourly (market hours) | Anomaly snapshot, portfolio Greeks check, go/no-go call, under 80 lines |
| **postmarket-ai-analysis** | 4:30 PM ET | P&L review vs. morning predictions, overnight risk, next-day plan, postmortem |

---

## Examples

### Example 1 — Pre-Market Briefing

**Type this:**
```
Run premarket analysis
```

**What you get back:**
```
REGIME: bearish  |  CONFIDENCE: 3/5  |  VIX: 27  |  ACTION: Reduce SPY, watch GLD
Top rec: GLD Bull Put $282/$279, 30 DTE, 2 contracts, Kelly 0.45
Risk alert: SPY spread approaching stop-loss -- handle first
Disagreement: Quant sees IV overpricing (bullish), but FOMC this week -- Event overrides Quant
Date: 2026-03-24  |  Next check: 14:00 ET

GTMVF Mispricing Table:
ETF   | Price | E[X]  | Gap    | Zone      | Signal
------|-------|-------|--------|-----------|----------
SPY   | $534  | $498  | +7.2%  | uncertain | Overvalued
QQQ   | $448  | $410  | +9.3%  | uncertain | Overvalued
GLD   | $296  | $310  | -4.5%  | noise     | Neutral
XLE   | $88   | $105  | -16.2% | strong    | Undervalued
TLT   | $87   | $91   | -4.4%  | noise     | Neutral

Hypothesis Weights (updated from today's news):
  H0 Soft landing:   0.18  (down -- PMI declined 3rd consecutive month)
  H1 Geopolitical:   0.35  (steady -- Middle East tensions stable)
  H2 Fed pivot:      0.20  (up -- dovish comment from Fed Governor Waller)
  H4 Oil shock:      0.15  (steady -- Brent $82, no supply disruption)
  H5 Recession:      0.28  (up -- jobless claims rising 4th consecutive week)

Macro: Slowdown regime. 2Y/10Y inverted at -42bp. Fed held at 4.25-4.50%.
Event: FOMC Wednesday. IV premium elevated -- do not sell premium into the event.
Sector: Risk-off flows. GLD/SPY ratio rising. XLE lagging despite oil at $82.
Quant: SPY IV Rank 71st percentile. Skew 118 (fear). Term structure 1.18x contango.

Trade Rec 1: GLD Bull Put $282/$279, 30 DTE
  Reason: Undervalued vs. equilibrium; gold underpriced as recession hedge
  Kelly: 0.45 -> 2 contracts. Max loss: $600 (1.1% portfolio)

Trade Rec 2: Hold -- no new SPY position until post-FOMC
  Reason: Event Analyst overrides Quant signal. FOMC event risk > IV premium opportunity.
```

---

### Example 2 — Intraday Opportunity Scan

**Type this:**
```
What ETFs are mispriced right now?
```

**What you get back:**
```
Running GTMVF analysis -- 2026-03-24 11:42 ET

ETF Mispricing Summary:
  QQQ: +9.3% overvalued (uncertain zone) --> Bear Call Spread candidate
  SPY: +7.2% overvalued (uncertain zone) --> Bear Call Spread candidate (50% confidence)
  XLE: -16.2% undervalued (strong zone)  --> Bull Put Spread if oil-regime risk accepted
  GLD: -4.5% undervalued (noise zone)    --> no trade
  TLT: -4.4% undervalued (noise zone)    --> no trade

Signal Classification: TRADEABLE (QQQ), CONDITIONAL (SPY), STAND DOWN (GLD, TLT)

WARNING -- Consensus: Macro + Event + Quant all flagging overvaluation.
  Historical pattern: High-consensus calls have appeared near turning points.
  Chief Strategist reduces position size 30% and confidence by 0.5.

Recommended: QQQ Bear Call $475/$480, 30 DTE, 1 contract (reduced size -- consensus caution)
```

---

### Example 3 — Post-Market Review

**Type this:**
```
Give me the postmarket review
```

**What you get back:**
```
Post-Market Brief -- 2026-03-24

P&L Today: +$187 (+0.34% portfolio)
  GLD Bull Put: +$220 (held through session)
  SPY spread: flat (no stop hit)

Overnight Risk: FOMC tomorrow 2 PM ET. VIX at 26.8 -- elevated. Futures -0.3%.
Tomorrow Setup: Hold all positions through FOMC. Cut new opens to half size.

What I Was Wrong About:
  Predicted XLE sector rotation into energy -- did not materialize.
  XLE/SPY ratio flat all day. No catalyst emerged.
  Action: Review XLE position at open. Roll or close if still uncertain zone post-FOMC.

Morning Predictions vs. Actual Results:
  Prediction              | Actual          | Verdict
  ------------------------|-----------------|--------
  SPY bearish             | SPY -0.4%       | CORRECT
  GLD neutral to bullish  | GLD +0.8%       | CORRECT
  XLE rotation signal     | XLE flat        | WRONG
  FOMC uncertainty holds  | VIX stayed high | CONFIRMED

Next Check: 8:30 AM ET pre-market
```

---

## Installation

```bash
claude plugin add github:WenyuChiou/multi-analyst-desk
```

All 7 skills and 3 scheduled task templates install automatically.

---

## Scheduling

Set up automated daily analysis with three scheduled tasks.

**Pre-market (8:30 AM ET)** — Full analyst team report before market open:
```bash
claude schedule add premarket-ai-analysis --cron "30 8 * * 1-5" --timezone "America/New_York"
```

**Intraday (hourly, market hours)** — Quick anomaly check during the session:
```bash
claude schedule add intraday-ai-analysis --cron "0 9-16 * * 1-5" --timezone "America/New_York"
```

**Post-market (4:30 PM ET)** — End-of-day synthesis and next-day plan:
```bash
claude schedule add postmarket-ai-analysis --cron "30 16 * * 1-5" --timezone "America/New_York"
```

All three run automatically on weekdays (Monday through Friday). No babysitting required.

---

## Configuration

Open `skills/trade-analyst/SKILL.md` and update these parameters:

| Parameter | Default | What to Change |
|-----------|---------|----------------|
| `$YOUR_CAPITAL` | placeholder | Your total trading capital in USD (used for Kelly position sizing) |
| ETF universe | SPY, QQQ, IWM, XLE, XLF, GLD, TLT, SMH, XLU | Add or remove ETFs to match your portfolio |
| DTE | 30 days | Target days-to-expiration for credit spreads |
| Kelly range | 0.25–0.75 | Minimum and maximum Kelly fraction |
| Output language | English | Change the output language directive in each skill file |

**Optional: live data file**

Drop a `warroom_data.json` file in your project root to feed live Greeks, portfolio positions, and pre-computed GTMVF gap values directly to the analysts. When present, analysts read from it first before falling back to WebSearch. See `examples/warroom_data_sample.json` for the expected schema.

---

## Requirements

**Required:**
- [Claude Code](https://claude.ai/code) CLI
- WebSearch access enabled in Claude Code settings

**Optional:**
- `warroom_data.json` — live market data file updated by your own data pipeline. Without it, all analysts gather data via WebSearch from public sources automatically.

No brokerage API keys required. All market data is gathered from public financial sources.

---

## Report Structure

Every pre-market report follows this fixed structure:

```
5-line executive summary
  regime, VIX, confidence, top action, next check

GTMVF Mispricing Table
  all ETFs: price, equilibrium, gap, zone, signal

4 Analyst Perspectives (independent)
  Macro:  economic regime, Fed direction, yield curve
  Sector: ETF rotation, relative strength, fund flows
  Quant:  IV rank, skew, term structure, Greeks
  Event:  calendar events, geopolitical risk, IV premium timing
  Each includes: Devil's Advocate, contradiction flags, if-I-am-wrong section

Chief Strategist Synthesis
  Disagreement scan (which analysts conflict and why)
  Dynamic analyst weighting (by current market regime)
  Contingency plan if the chosen side is wrong

2-3 Trade Recommendations
  Specific strikes (per-ETF calculation rules)
  DTE, Kelly sizing, max loss, profit target
  Management checkpoints: 14 DTE / 7 DTE / 3 DTE
```

---

## Evaluation

The trade-analyst skill includes 3 test scenarios in `skills/trade-analyst/evals/evals.json`:

1. **Pre-market geopolitical crisis** — elevated geopolitical tensions + VIX 27
2. **Post-market crisis management** — SPY -1.8% + spread near stop-loss
3. **Intraday Fed signal** — dovish Fed comment verification + noise vs. signal classification

**91% pass rate** across 35 assertions (iteration 5).

---

## License

MIT
