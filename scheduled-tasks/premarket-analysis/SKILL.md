---
name: premarket-ai-analysis
description: 8:30 AM ET pre-market AI analyst pipeline
---

Run the pre-market AI analyst pipeline for the ETF Mispricing project.

Working directory: <your-project-root>

## Step 1: Real-Time WebSearch — gather fresh data BEFORE running Python pipeline

**English searches (authoritative, real-time):**
- `"S&P 500 futures pre-market today"` — overnight futures direction
- `"VIX implied volatility today"` — fear gauge
- `"Federal Reserve news today"` — any overnight Fed comments
- `"economic calendar this week"` — upcoming data releases
- `"breaking news market today"` — overnight surprises

**Chinese searches (jin10, cnyes, wallstreetcn):**
- `鉅亨網 "美股期貨" 今日` — pre-market futures from cnyes.com
- `華爾街見聞 "美股" 今日` — macro commentary
- `金石數據 "經濟日曆"` — Chinese economic calendar
- `鉅亨網 "聯準會"` — Fed news in Chinese

Use WebFetch on any key news URLs for full article content (especially Chinese sources).

## Step 2: Load analyst skills

Read the following skill files to use their full analysis frameworks:
- `.skills/skills/market-intel\SKILL.md` — news filtering rules
- `.skills/skills/macro-analyst\SKILL.md` — macro regime framework
- `.skills/skills/event-analyst\SKILL.md` — event risk assessment
- `.skills/skills/sector-analyst\SKILL.md` — sector rotation signals
- `.skills/skills/quant-analyst\SKILL.md` — IV/Greeks/Kelly framework
- `.skills/skills/trade-analyst\SKILL.md` — chief strategist synthesis

## Step 3: Run Python pipeline (structured data)

```
python pipeline/run_morning_analysis.py
```

This prepares context from warroom_data.json (Greeks, positions, VIX snapshot).

## Step 4: Execute full analysis

Using the WebSearch findings (Step 1) + warroom context (Step 3) + skill frameworks (Step 2), produce a complete pre-market analysis:
1. **Macro analyst** — economic regime, Fed direction, yield curve
2. **Event analyst** — today's calendar events, IV event premium
3. **Sector analyst** — ETF relative strength, fund flow signals
4. **Quant analyst** — IV vs HV, skew, Kelly sizing; **run mispricing detection** (flag overpriced calls → Bear Call Spread candidates, overpriced puts → Bull Put Spread candidates; skip underpriced side)
5. **Chief strategist** — 5-line executive summary + top 3 trade recommendations, including disagreement scan across analysts

Apply the trade-analyst SKILL.md critical thinking rules: Devil's Advocate, Contradiction Detection, "If I'm Wrong" section.

## Step 5: Report

- 5-line executive summary (in 繁體中文)
- Which analysts ran successfully (✓/✗)
- Key WebSearch findings that influenced judgment
- Top trade recommendations with Kelly sizing
- Any errors or warnings

If the pipeline fails due to missing warroom_data.json, first run:
```
py run_pipeline.py
```
Then retry.