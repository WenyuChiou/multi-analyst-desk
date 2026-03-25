# ETF Mispricing Analyst

A team of 4 AI analysts + 1 chief strategist that finds ETF mispricing using game theory and produces actionable credit spread trades.

## Why this exists

Most AI trading tools give you a single opinion — one model, one perspective, one bias. This plugin deploys a **virtual analyst team** where specialists argue with each other, and a chief strategist makes the final call by weighing their disagreements. The result is analysis that's harder to fool and more honest about uncertainty.

## What makes it different

**Game-theoretic pricing** — Instead of relying on technical indicators alone, each analyst updates hypothesis probabilities (war, recession, Fed pivot, tech bubble, etc.) and the framework calculates Nash equilibrium prices for each ETF. The gap between market price and equilibrium price is your mispricing signal.

**Analysts that disagree** — The chief strategist doesn't average opinions. When the quant analyst sees IV overpricing as an opportunity but the event analyst flags unresolved geopolitical risk, the strategist picks a side and explains why. You see the debate, not just the conclusion.

**Built-in skepticism** — Every analyst must argue against their own position (Devil's Advocate), flag contradictions between data sources, calibrate confidence with explicit evidence counts, and write a "what if I'm wrong" contingency. Consensus triggers a warning, not comfort.

**Bilingual intelligence** — Searches English sources (Reuters, Bloomberg, CNBC) and Chinese financial media (鉅亨網, 華爾街見聞, 金十數據) simultaneously. Two language ecosystems catch what one misses.

**Credit spread only** — All recommendations are strictly Bull Put or Bear Call spreads with specific strikes, DTE, and Kelly sizing. No debit spreads, no naked options, no individual stocks.

## The team

| Analyst | Focus | Game theory role |
|---------|-------|-----------------|
| **Macro** | Economic cycle, Fed policy, yield curve | Updates recession / soft-landing / Fed-pivot probabilities |
| **Sector** | ETF rotation, relative strength, breadth | Validates equilibrium targets against sector flows |
| **Quant** | IV rank, options Greeks, momentum | Technically verifies mispricing gaps, runs Kelly sizing |
| **Event** | Geopolitical risk, economic calendar, binary events | Updates war / oil-shock probabilities from live news |
| **Chief Strategist** | Orchestrates all 4, resolves conflicts | Synthesizes final weights, produces trade plan |

## What you get

**Pre-market (daily)** — Full 4-analyst report: news scan, GTMVF equilibrium prices, ETF mispricing table, hypothesis updates, trade recommendations with strikes and sizing.

**Intraday (hourly)** — Quick anomaly check: is this move signal or noise? Should you act or wait?

**Post-market (daily)** — P&L review, position management (stop/hold/roll), next-day action plan with time-based triggers.

## Installation

```bash
# Claude Code
claude plugin install WenyuChiou/etf-mispricing-analyst

# Manual
cp -r skills/* /path/to/your/project/.skills/skills/
cp -r scheduled-tasks/* /path/to/your/project/.skills/scheduled-tasks/
```

## Configuration

Open `skills/trade-analyst/SKILL.md` and customize:

- `$YOUR_CAPITAL` — Your trading capital
- ETF universe — Default: SPY, QQQ, IWM, XLE, XLF, GLD, TLT, SMH, XLU
- DTE — Default: 30 days
- Language — Default: Traditional Chinese (繁體中文), easy to switch

## Data format

The analysts read from `docs/data/warroom_data.json`. See `examples/warroom_data_sample.json` for the expected structure. At minimum you need:

```json
{
  "market": { "items": { "SPY": { "price": 653.18, "change_pct": -0.34 } } },
  "gtmvf": { "assets": { "SPY": { "gap_pct": 6.2, "gap_zone": "uncertain" } } }
}
```

If you don't have a data pipeline, the analysts will use WebSearch to gather live market data directly.

## Evaluation

Includes 3 test scenarios in `skills/trade-analyst/evals/evals.json` covering geopolitical crisis, post-market stop-loss management, and intraday Fed signal verification. Tested at **91% pass rate** across 35 assertions.

## How the analysis flows

```
Your question
    |
    v
[WebSearch] EN + Chinese financial sources
    |
    v
[Data read] Market prices, VIX, positions, GTMVF gaps
    |
    v
[4 analysts] Each independently analyzes + updates hypothesis probabilities
    |
    v
[Chief strategist] Weighs disagreements, picks sides, explains reasoning
    |
    v
Credit spread recommendations with strikes, DTE, Kelly sizing
```

## License

MIT
