# ETF Mispricing Analyst

A team of 4 AI analysts + 1 chief strategist that detects ETF mispricing using game theory and produces actionable credit spread trades.

## What It Does

Most AI trading tools give you a single opinion. This plugin deploys a **virtual analyst team** where four specialists independently analyze the market from different angles — macro, sector, quant, and event — then argue with each other. A chief strategist weighs their disagreements and makes the final call. You see the debate, not just the conclusion.

The core engine is GTMVF (Game-Theoretic Market Valuation Framework): each analyst updates probability weights for macroeconomic hypotheses (soft landing, recession, geopolitical shock, Fed pivot), and the framework calculates Nash equilibrium prices for each ETF. The gap between market price and equilibrium price is your mispricing signal.

## Analysts

| Analyst | Role | Game Theory Contribution |
|---------|------|--------------------------|
| **Macro Analyst** | Economic cycle, Fed policy, yield curve, inflation/growth dynamics | Updates H0 (soft landing), H2 (Fed pivot), H5 (recession) hypothesis probabilities |
| **Sector Analyst** | ETF rotation, relative strength, fund flows, breadth analysis | Validates equilibrium price targets against sector flow signals |
| **Quant Analyst** | IV rank, options Greeks, Kelly sizing, momentum confirmation | Technically verifies mispricing gaps; calculates position sizes |
| **Event Analyst** | Geopolitical risk, economic calendar, binary events, IV premium | Updates H1 (war/geopolitical) and H4 (oil shock) probabilities from live news |
| **Chief Strategist** | Orchestrates all 4 analysts, resolves conflicts | Disagreement-driven synthesis — picks sides, explains reasoning |

## Trading Sessions

### Pre-Market (8:30 AM ET daily)
Full 4-analyst report: overnight news scan, GTMVF equilibrium price calculation, ETF mispricing table, hypothesis probability updates, and 3+ trade recommendations with specific strikes, DTE, and Kelly-sized contracts.

### Intraday (Hourly during market hours)
Quick anomaly snapshot: VIX move detection, noise vs. signal classification, portfolio Greeks check, and a simple go/no-go action call. Under 80 lines — built for speed.

### Post-Market (4:30 PM ET daily)
End-of-day synthesis: P&L review vs. morning predictions, overnight risk assessment, next-day action plan, and a "what I was wrong about" postmortem from the chief strategist.

## Key Features

**GTMVF Game Theory Engine** — Instead of technical indicators alone, the framework models market participants as rational actors in a multi-player game. Each ETF gets an equilibrium price E[X] based on probability-weighted scenarios. Gap > 5% = tradeable mispricing signal.

**Disagreement-Driven Synthesis** — The chief strategist does not average opinions. When the quant analyst sees IV overpricing as an opportunity but the event analyst flags unresolved geopolitical risk, the strategist picks a side and explains why. Consensus triggers a warning, not comfort.

**Built-In Skepticism** — Every analyst must argue the opposite of their own position (Devil's Advocate), flag contradictions between data sources, calibrate confidence on a 1–5 scale with explicit evidence counts, and write a "what if I'm wrong" contingency section.

**Bilingual Intelligence** — Simultaneous searches in English (Reuters, Bloomberg, CNBC) and Chinese financial media (鉅亨網, 華爾街見聞, 金十數據). Two language ecosystems catch what one misses — especially for Asia-Pacific macro signals.

**Credit Spread Only** — All recommendations are strictly Bull Put or Bear Call spreads. No debit spreads, no naked options, no individual stocks. Strikes are calculated from specific per-ETF rules, not vague placeholders.

## Examples

### Example 1: Run pre-market analysis

```
Run premarket analysis
```

What the team produces:

```
【Regime】bearish | 【Confidence】3/5 | 【VIX】27 | 【Action】Reduce SPY, watch GLD
Top rec: GLD Bull Put $282/$279, 30DTE, 2 contracts, Kelly 0.45
Risk alert: SPY spread approaching stop-loss — handle first
Disagreement: Quant sees IV overpricing (bullish), but FOMC this week — Event > Quant
Date: 2026-03-24 | Next check: 14:00 ET

GTMVF Mispricing Table:
ETF   | Price  | E[X]   | gap_pct | Zone      | Signal
------|--------|--------|---------|-----------|----------
SPY   | $534   | $498   | +7.2%   | uncertain | Overvalued
QQQ   | $448   | $410   | +9.3%   | uncertain | Overvalued
GLD   | $296   | $310   | -4.5%   | noise     | Neutral
XLE   | $88    | $105   | -16.2%  | strong    | Undervalued
TLT   | $87    | $91    | -4.4%   | noise     | Neutral

Macro: Slowdown regime. 2Y/10Y inverted at -42bp. Fed held at 4.25-4.50%.
Event: FOMC Wednesday. IV premium elevated — do not sell premium into the event.
Sector: Risk-off flows. GLD/SPY ratio rising. XLE lagging despite oil at $82.
Quant: SPY IV Rank 71st percentile. Skew 118 (fear). Term structure contango 1.18x.

Trade Rec #1: GLD Bull Put $282/$279, 30DTE
Reason: Undervalued vs equilibrium; market underpricing gold as recession hedge
Kelly: 0.45 x capital → 2 contracts. Max loss: $600 (1.1% portfolio)

Trade Rec #2: Hold — no new SPY position until post-FOMC
Reason: Event analyst overrides quant signal. FOMC event risk > IV premium opportunity.
```

### Example 2: Find mispriced ETFs

```
What ETFs are mispriced right now?
```

What the GTMVF engine produces:

```
Running GTMVF analysis...

Current hypothesis weights (updated from today's news):
  H0 Soft landing:    0.18  (down — PMI declined 3rd consecutive month)
  H1 Geopolitical:    0.35  (steady — Middle East tensions stable)
  H2 Fed pivot:       0.20  (up — dovish comments from Fed Governor Waller)
  H4 Oil shock:       0.15  (steady — Brent $82, no supply disruption)
  H5 Recession:       0.28  (up — jobless claims rising for 4th week)

ETF Mispricing Summary:
  QQQ: +9.3% overvalued (uncertain zone) → Bear Call Spread candidate
  SPY: +7.2% overvalued (uncertain zone) → Bear Call Spread candidate (50% confidence)
  XLE: -16.2% undervalued (strong zone) → Bull Put Spread candidate if oil-regime risk accepted
  GLD: -4.5% undervalued (noise zone) → no trade
  TLT: -4.4% undervalued (noise zone) → no trade

⚠️ Consensus warning: Macro + Event + Quant all flagging overvaluation.
  Historical note: High-consensus overvaluation calls have appeared at turning points.
  Chief Strategist reduces position size by 30% and confidence by 0.5.

Recommended: QQQ Bear Call $475/$480, 30DTE, 1 contract (reduced size — consensus caution)
```

### Example 3: Post-market review

```
Give me the postmarket review
```

What the end-of-day synthesis produces:

```
Post-Market Brief — 2026-03-24
P&L today: +$187 (+0.34% portfolio). GLD Bull Put gained $220; SPY spread held. No stops hit.
Overnight risk: FOMC tomorrow 2pm ET. VIX at 26.8 — elevated. Futures down 0.3%.
Tomorrow setup: Hold all positions through FOMC. Reduce any new opens to half size.
What I was wrong about: Predicted XLE sector rotation — did not materialize. XLE/SPY flat.
Action: Review XLE position at open. Consider rolling if still uncertain zone post-FOMC.

Today's Calls vs Reality:
  Morning prediction   | Actual result
  ---------------------|-------------------------
  SPY bearish          | SPY -0.4%    CORRECT
  GLD neutral to bull  | GLD +0.8%    CORRECT
  XLE rotation signal  | XLE flat     WRONG — no rotation materialized
  FOMC uncertainty     | VIX stayed elevated    CONFIRMED
```

## What Gets Produced

Every pre-market report follows this structure:

```
5-line executive summary (regime, VIX, confidence, top action, next check)
└── GTMVF mispricing table (all ETFs in universe)
└── 4 analyst perspectives
    ├── Macro: economic regime, Fed direction, yield curve
    ├── Sector: ETF rotation, relative strength, fund flows
    ├── Quant: IV rank, skew, term structure, Greeks
    └── Event: calendar events, geopolitical risk, IV premium
        Each analyst includes: Devil's Advocate, contradiction flags, "if I'm wrong"
└── Chief strategist synthesis
    ├── Disagreement scan (which analysts conflict and why)
    ├── Dynamic analyst weighting (by current market regime)
    └── "If I chose the wrong side" contingency
└── 2-3 trade recommendations
    ├── Specific strikes (per-ETF calculation rules, not placeholders)
    ├── DTE, Kelly sizing, max loss, profit target
    └── Management plan: 14 DTE / 7 DTE / 3 DTE checkpoints
```

## Installation

```bash
claude plugin add github:WenyuChiou/etf-mispricing-analyst
```

Or manually:

```bash
# Copy skills
cp -r skills/* /path/to/your/project/.skills/skills/

# Copy scheduled tasks
cp -r scheduled-tasks/* /path/to/your/project/.skills/scheduled-tasks/
```

## Requirements

- **Claude Code** with WebSearch and file read access
- **Optional**: A `warroom_data.json` file with live market data (Greeks, positions, GTMVF gap_pct values). If absent, analysts use WebSearch for all market data.
- **Optional**: A Python data pipeline for automated data refresh (see `examples/warroom_data_sample.json` for the expected format)

No API keys required. All market data is gathered via WebSearch from public sources.

## Configuration

Open `skills/trade-analyst/SKILL.md` and customize:

- `$YOUR_CAPITAL` — Your trading capital (used for Kelly sizing)
- **ETF universe** — Default: SPY, QQQ, IWM, XLE, XLF, GLD, TLT, SMH, XLU
- **DTE** — Default: 30-day expiration
- **Language** — Default: Traditional Chinese (繁體中文); change to English by editing the output language instruction at the bottom of each skill

## Scheduled Tasks

| Task | Time | Output |
|------|------|--------|
| `premarket-ai-analysis` | 8:30 AM ET | Full 4-analyst report + GTMVF table + trade recs |
| `intraday-ai-analysis` | Hourly (market hours) | Quick anomaly check + portfolio snapshot |
| `postmarket-ai-analysis` | 4:30 PM ET | P&L review + overnight risk + next-day plan |

## Evaluation

Includes 3 test scenarios in `skills/trade-analyst/evals/evals.json`:
1. Premarket geopolitical crisis (Iran-Israel tensions + VIX 27)
2. Post-market crisis management (SPY -1.8% + spread near stop-loss)
3. Intraday Fed signal (dovish comment verification + noise vs. signal)

**91% pass rate** across 35 assertions (iteration 5).

## Analysis Flow

```
User prompt
    |
    v
[WebSearch] English + Chinese financial sources (simultaneous)
    |
    v
[Data read] Market prices, VIX, Greeks, GTMVF gap_pct from warroom_data.json
    |
    v
[4 analysts] Each independently analyzes + updates hypothesis probabilities
    |
    v
[Chief strategist] Identifies disagreements, weights analysts by regime, picks sides
    |
    v
Credit spread recommendations with specific strikes, DTE, Kelly sizing
```

## License

MIT
