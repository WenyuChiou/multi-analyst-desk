---
name: intraday-ai-analysis
description: Intraday AI analyst snapshot — real-time market check during market hours
---

Run an intraday market analysis for the ETF Mispricing War Room.

Working directory: <your-project-root>

## Step 1: Real-Time WebSearch — current market conditions

**English searches (real-time, primary):**
- `"S&P 500 today intraday"` — current price and % change
- `"VIX today"` — current fear gauge
- `"market news today [date]"` — breaking intraday news
- Any relevant ETF: `"QQQ intraday"`, `"GLD today"`, `"XLE today"` as needed

**Chinese searches (supplementary):**
- `金石數據 "美股" 即時` — real-time from jin10.com
- `華爾街見聞 "盤中" 今日` — intraday commentary from wallstreetcn.com
- `鉅亨網 "美股" 即時行情` — real-time quotes from cnyes.com
- `財經M平方 "市場" 今日` — MacroMicro intraday signals

Use WebFetch on key article URLs for full content when a major development is detected.

## Step 2: Load relevant skills (as needed)

- `.skills/skills/opportunity-detector/SKILL.md` — noise vs signal classification
- `.skills/skills/market-intel/SKILL.md` — news credibility rules
- `.skills/skills/trade-analyst/SKILL.md` — intraday adjustment framework (80-line limit)
- `.skills/skills/quant-analyst/SKILL.md` — mispricing detection (load if anomaly detected or on first intraday check)

## Step 3: Load portfolio state (optional)

If you maintain a local portfolio data file, read it for current Greeks, P&L, and positions.

**If no file is available**: skip this step. Proceed with WebSearch-only analysis and any positions described manually. Note missing portfolio data in the report.

## Step 4: Intraday analysis (MAX 80 lines per trade-analyst spec)

Apply the opportunity-detector skill:
1. **Anomaly check** — VIX move >10%? Any ETF >2% intraday? Breaking news?
2. **Noise vs signal** — Use opportunity-detector's Step 3 framework
3. **Mispricing detection** — Run quant-analyst mispricing scan: flag overpriced calls → Bear Call Spread candidates, overpriced puts → Bull Put Spread candidates; skip underpriced side (credit-spread-only rule)
4. **Portfolio impact** — Check current Greeks against any detected moves
5. **Action needed?** — Close early, adjust, or hold?

If no anomalies: confirm all positions nominal and stand down.

## Step 5: Report (concise — MAX 80 lines)

- 1-line market status (VIX, SPY/QQQ direction)
- Any anomaly detected (noise or signal?)
- Portfolio P&L snapshot
- Recommended action (or "no action needed")
- Next check time

Only report errors or significant signal changes. Skip detailed reporting if everything is nominal.
