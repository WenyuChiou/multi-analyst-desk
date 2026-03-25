---
name: postmarket-ai-analysis
description: 4:30 PM ET post-market AI analyst pipeline
---

Run the post-market AI analyst pipeline for the ETF Mispricing project.

Working directory: <your-project-root>

## Step 1: Real-Time WebSearch — gather end-of-day context BEFORE Python pipeline

**English searches:**
- `"S&P 500 close today"` — final close prices and % change
- `"VIX close today"` — end-of-day fear gauge
- `"market recap today [date]"` — daily summary from CNBC/Bloomberg
- `"Federal Reserve news today"` — any intraday Fed commentary
- `"S&P 500 after hours today"` — after-hours movement

**Chinese searches (jin10, cnyes, wallstreetcn):**
- `華爾街見聞 "美股收盤" 今日` — closing summary
- `鉅亨網 "美股" 今日收盤` — market close recap
- `金石數據 "盤後" 今日` — after-hours data
- `華爾街見聞 "隔夜風險"` — overnight risk assessment

Use WebFetch on key news URLs for full content (especially Chinese sources).

## Step 2: Load analyst skills

Read skill files for the post-market analysis frameworks:
- `.skills/skills/market-intel\SKILL.md` — news validation rules
- `.skills/skills/trade-analyst\SKILL.md` — chief strategist synthesis + critical thinking
- `.skills/skills/quant-analyst\SKILL.md` — Greeks/Kelly review

## Step 3: Run Python pipeline

```
python pipeline/run_evening_analysis.py
```

If warroom_data.json is stale (no 4pm data), first run:
```
py run_pipeline.py --push
```

## Step 4: Run reflection scoring

```
python pipeline/run_reflection.py
```

If run_reflection.py does not exist, use:
```
python pipeline/ai_analysts/reflection.py --daily
```

## Step 5: Execute post-market synthesis

Using WebSearch findings (Step 1) + warroom context + skill frameworks, produce:
1. **P&L review** — today's actual vs morning predictions
2. **Overnight risk assessment** — what could move markets before tomorrow open
3. **Tomorrow's pre-market setup** — key levels, events, ETF signals
4. **Quant analyst — mispricing scan** — run mispricing detection on tomorrow's watchlist: flag overpriced calls → Bear Call Spread candidates, overpriced puts → Bull Put Spread candidates; skip underpriced side (credit-spread-only rule)
5. **Analyst weight update** — did today's data confirm or contradict any analyst's framework?

Apply trade-analyst critical thinking: "If I Was Wrong" postmortem on today's calls.

## Step 6: Report

- 5-line overnight brief (in 繁體中文)
- Which post-market analysts ran successfully (✓/✗)
- P&L summary and reflection scores
- Key WebSearch findings that differed from the morning plan
- Tomorrow's top priority action items