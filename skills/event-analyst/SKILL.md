---
name: event-analyst
description: "Event-driven analyst skill. Tracks economic calendar events, geopolitical risks, earnings seasons, and options expiration effects on IV. Identifies optimal credit spread entry timing around post-event IV crush. Uses multi-source validation (English + Chinese sources). Triggers on: FOMC, NFP, CPI, earnings releases, geopolitical developments, and any question about upcoming market-moving events."
---

# Event-Driven Analyst

## Configuration
- LANGUAGE: English (set to "zh-TW" for Traditional Chinese)
- CAPITAL: $10,000 (customize to your portfolio size)
- If any data source is unavailable, state what's missing and analyze with available data only.


## Core Responsibilities
The event analyst uses a multi-source validation system to track economic events, geopolitical risks, earnings seasons, and options expiration. It assesses event impact on implied volatility (IV) and identifies **the optimal timing to open Credit Spreads (during post-event IV crush)**.

## 1. Multi-Source Event Discovery

### A. English Economic Calendar
- WebSearch: `"economic calendar this week {today_date}"` (include current date on every search)
- WebSearch: `"FOMC meeting schedule 2026"`, `"Federal Reserve rate decision"`, `"Fed calendar 2026"`
- WebSearch: `"NFP jobs report 2026"`, `"CPI inflation data"`, `"GDP release schedule"`
- WebFetch: `investing.com/economic-calendar` (structured calendar data)

### B. Chinese Economic Calendar (Traditional Chinese sources)
- WebSearch: `"金石數據 經濟日曆"`
- WebSearch: `"鉅亨網 聯準會會議"`
- WebSearch: `"華爾街見聞 經濟數據"`

### C. Geopolitical Events
- WebSearch: `"geopolitical risk {today_date}"`, `"current geopolitical risks"`, `"Russia Ukraine"`, `"US China trade"`
- WebSearch: `"sanctions updates 2026"`, `"military conflict"`, `"election schedule"`
- WebSearch: `"華爾街見聞 地緣政治"`, `"鉅亨網 國際政治"`

### D. Cross-Source Validation Rules
**Economic event validation**:
- Must be confirmed by at least **2 independent calendars** (e.g., investing.com, CNBC calendar, Chinese economic calendar)
- Single-source news credibility = 2/5 or below; not listed on the schedule

**Geopolitical event validation**:
- Must be confirmed by at least **3 independent sources** (English 2 + Chinese 1, or vice versa)
- Military escalation requires immediate validation; credibility ≥ 3/5 before use as a trading reference
- Misinformation filter: apply the same noise filter as opportunity-detector

## 2. Economic Calendar — Dynamic Search Version

**No more hardcoded dates. Execute a search at every analysis:**

### Major Economic Data Releases
1. **FOMC Rate Decision** (Federal Open Market Committee)
   - Frequency: 8 times per year (6–7 week intervals)
   - Impact level: 5/5 — highest volatility trigger
   - ETF impact: SPY, IVV, QQQ (all equity ETFs), TLT (bonds inverse)
   - Typical IV premium: +18–25%

2. **Non-Farm Payrolls (NFP)**
   - Release: first Friday of each month
   - Impact level: 4/5
   - ETF impact: SPY, IWM (small/mid-caps), IVV
   - Typical IV premium: +8–12%

3. **Consumer Price Index (CPI)**
   - Release: typically mid-month
   - Impact level: 4/5
   - ETF impact: TLT (most sensitive to rates), SPY, GLD (inflation gauge)
   - Typical IV premium: +12–18%

4. **Producer Price Index (PPI)**
   - Release: typically one day after CPI
   - Impact level: 3/5
   - ETF impact: similar to CPI but lower intensity
   - Typical IV premium: +8–12%

5. **Gross Domestic Product (GDP)**
   - Release: quarter-end (late March, June, etc.)
   - Impact level: 3–4/5
   - ETF impact: SPY, IVV (economy-sensitive)
   - Typical IV premium: +10–15%

6. **Unemployment Rate and Initial Jobless Claims**
   - Release: weekly (Thursdays)
   - Impact level: 2–3/5 (lower than NFP)
   - ETF impact: SPY, IWM
   - Typical IV premium: +5–8%

7. **ISM Manufacturing PMI**
   - Release: first business day of each month
   - Impact level: 3/5
   - ETF impact: XLV (industrials), SPY
   - Typical IV premium: +8–10%

## 3. Geopolitical Risk Analysis

### Current Geopolitical Risk Monitoring

Search for active geopolitical risks using WebSearch. Apply this generic impact framework based on the type of event detected:

**Impact Assessment Framework**:
| Event Type | Risk Level | XLE Impact | GLD Impact | TLT Impact | Credibility Required |
|---------|---------|-----------|-----------|-----------|------------------|
| Military escalation (any region) | 4–5/5 | +3–7% oil | +2–4% rise | +1–3% rise | 3+ sources |
| Ceasefire / de-escalation | 3/5 | −2–4% oil | −1–2% drop | −1% drop | 2+ sources |
| Trade restriction / sanctions | 3–4/5 | varies | +1–2% | +0.5–1% | 2+ sources |
| Energy supply disruption | 4–5/5 | +5–10% oil | +2–3% | neutral | 3+ sources |
| Geopolitical stabilization | 2–3/5 | −1–3% oil | −1–2% | −0.5% | 2+ sources |

**Search for current active risks with**: `"geopolitical risk today"`, `"military conflict latest"`, `"oil supply disruption"`, `"trade war sanctions update"`

### Geopolitical Search Workflow
1. English sources: Google News, Reuters, Bloomberg, CNN (geopolitical risk keywords)
2. Chinese sources: 華爾街見聞, 鉅亨網, 金十數據 (geopolitical, conflict-related keywords)
3. Financial market reaction: WebFetch relevant commodities news to confirm market awareness

### Credibility Scoring
- **5/5**: Official statements (government, military) + multi-country confirmation
- **4/5**: Major media coverage (Reuters, Bloomberg, AP News) + multi-source confirmation
- **3/5**: Single major media report (credible but not fully confirmed)
- **2/5**: Social media or single expert opinion
- **1/5**: Unverified rumors or speculation

**Single-source information credibility is always ≤ 2/5 and cannot be used for trading decisions.**

## 4. Event Premium Analysis

### Definition and Calculation
**Event Premium = (IV_event - IV_baseline) / IV_baseline × 100%**

Where:
- `IV_event`: current IV in the pre-event window (T−1 to T−3 days)
- `IV_baseline`: average IV over the past 30 days, excluding major event periods

### Steps to Obtain Current IV
1. **WebSearch**: `"{ETF} implied volatility today"` (e.g., "SPY implied volatility today")
2. **WebFetch**: visit investing.com, CBOE official site for latest IV data
3. **Record timestamp**: ensure IV data is synchronized with search time

### IV Premium Assessment Criteria

| Event Premium Level | Assessment | Trading Signal |
|---------------------|------------|----------------|
| **> 20%** | Over-priced, premium is rich | **Sell signal** — open credit spread |
| **10–20%** | Fairly priced | Neutral — wait or small position |
| **< 10%** | Under-priced, lower risk | Wait — credit-spread-only system does not buy options |

### Trade Timing Map
- **Premium > 20%**: do **not** open new positions before the event (entry cost too high)
- **T+1 to T+3 days post-event**: IV drops quickly → **optimal entry window** → sell credit spread, collect theta decay
- **Premium falls to <10%**: IV normalizes, return to daily operating mode

## 5. CREDIT SPREAD ONLY — Event Strategy

### Strict Rule: This system **only sells option premium via Credit Spreads**.
**Never buy Straddles, Strangles, Debit Spreads, or any net-debit strategy.**

### Replacement Table (Common Mistakes → Correct Approach)
| Incorrect Suggestion | Correct Credit-Spread Replacement |
|------------------|--------------------------|
| Buy Straddle | **Post-event IV crush → Sell Bull Put Spread + Bear Call Spread** |
| Buy Strangle | **Wait for post-event IV drop, then open credit spread** |
| Buy Call Spread | **None — use Bear Call Spread (sell call + buy higher call)** |
| Buy Put Spread | **None — use Bull Put Spread (sell put + buy lower put)** |

### Credit Spread Strategy by Event Lifecycle

#### Phase T−7 to T−3: Observation Period (Do Not Open New Positions)
- **Reason**: IV premium too high (typically >20%), entry cost elevated
- **Action**: monitor news, track IV changes, prepare for post-event entry
- **Avoid**: no new credit spreads during this period

#### Phase T−0 (Event Day): Stop Trading
- **Reason**: slippage risk too high, cannot price accurately
- **Action**: monitor real-time data, analyze event outcome
- **Portfolio**: hold existing positions unchanged

#### Phase T+1 to T+3: Optimal Entry Window (Best Window for Credit Spreads)
- **Reason**: IV crush occurs → premium falls 20–30% → collect post-crush theta decay
- **Strategy A**: Sell Bull Put Spread
  - Example: SPY $500/$495 Put spread (30 DTE)
  - Lower premium collected but more limited risk
- **Strategy B**: Sell Bear Call Spread
  - Example: QQQ $450/$455 Call spread (30 DTE)
  - Directional bias to the upside while collecting premium
- **Strategy C**: Iron Condor — not recommended
  - Note: higher complexity; prefer simple Bull Put or Bear Call unless market is very low-vol

#### Phase T+7 and Beyond: Return to Normal Operations
- IV reverts to baseline
- Trade in daily 30-DTE monthly spread mode
- No event premium consideration

## 6. Binary Risk Events

### Definition
Binary event = a market event that could cause >3% ETF movement in a single day

### Binary Event Scoring and Credit Spread Recommendation Table

| Event Type | Risk Level | Typical Move | T−7 to T−3 (Observe) | T+1 to T+3 (Entry Strategy) | Credibility |
|---------|---------|--------|-----------------|------------------|----------|
| Surprise FOMC decision | 5/5 | ±2–4% | Observe, no new positions | Sell Bull Put + Bear Call Spreads | Must confirm |
| Geopolitical escalation | 4–5/5 | ±2–3% (XLE, GLD) | Protect positions (hold) | Sell spreads after IV crush | 3+ sources |
| Strong/weak NFP | 3–4/5 | ±1–2% | Observe | Bull Put Spread (SPY/IWM) | 2+ sources confirmed |
| CPI surprise | 3–4/5 | ±1–2% (TLT sensitive) | Observe | Bear Call Spread (TLT) | 2+ sources confirmed |
| Black swan event | 5/5 | ±3–5% | **Avoid selling premium** | If IV crush, small-scale spreads | Immediate confirmation |

### Special Handling for Black Swan Events
- Definition: completely unexpected events (coup, natural disaster, technology crash, etc.)
- **Response**: **Avoid opening new short premium positions during black swan events**
- If positions exist: consider stop-loss or hedge (buy protective put)
- Post-event: wait 2–3 days for market to stabilize before opening new positions

### Event Information Recording Format
Each binary event should record:
- **Event date**: YYYY-MM-DD
- **Event name**: English and Chinese
- **Credibility level**: 1–5/5 (number of sources)
- **Expected volatility**: ±X% (sample basis)
- **Affected ETFs**: [ETF1, ETF2, ...]
- **Information sources**: [Source A (credibility 4), Source B (credibility 4), ...]
- **Timestamp**: search time (HH:MM ET)

## 7. Earnings Season Impact

### Earnings Season Schedule by ETF
1. **Tech Earnings Season (QQQ, XLK)**
   - Timing: early April, mid-July, mid-October, early January
   - IV spike: +25–35%
   - Key companies: NVDA, MSFT, AAPL, GOOGL, META
   - Strategy: sell spreads at T+1–3 (after IV crush)

2. **Financial Earnings Season (IVV, VFV, XLF)**
   - Timing: January–April, July, October
   - IV spike: +15–25%
   - Sensitivity: closely tied to FOMC dates
   - Strategy: coordinate with FOMC dates

3. **Energy Earnings Season (XLE, DBC)**
   - Timing: mid-April, late July, late October
   - IV spike: +20–30% (oil-price sensitive)
   - Geopolitical overlay: may add +5–10% if active conflict affects oil
   - Strategy: monitor active geopolitical risks alongside earnings

4. **Consumer Earnings Season (XLY, XLP)**
   - Timing: late April, late July, late October
   - IV spike: +12–20%
   - Economic sensitivity: consumer confidence data
   - Strategy: secondary priority

5. **SPY Broad Impact (whole market)**
   - Timing: January–April, July, October periods
   - IV baseline rise: +15–20%
   - Multiple earnings overlapping: IV may reach +25–35%
   - Strategy: credit spread rules apply broadly

### Earnings IV Crush Strategy
- **T−3 days before release**: observe, record current IV baseline
- **T+1–2 days post-release**: IV drops 15–25% → **optimal credit spread entry moment**
- **Holding period**: 30 DTE, target close at 30–50% of theta decay profit

## 8. Expiration Risk Management

### Monthly Options Expiration Schedule
- **Expiration date**: third Friday of each month (US VIX options / index options)
- **Advance action**: start reducing or adjusting positions 2–4 days before expiration

### Gamma Risk and Price Jumps
- **Gamma definition**: sensitivity of Delta to price changes
- **High Gamma period**: the week before expiration, especially for ATM (At The Money) contracts
- **Risk**: small price moves cause large Delta changes, making hedging difficult

### Max Pain
- **Definition**: the price level at which the maximum number of options expire worthless
- **Query**: WebSearch `"SPY max pain"`, `"QQQ max pain"` + expiration period
- **Impact**: there may be notable price pinning near Max Pain before expiration
- **Strategy**: watch for Max Pain resistance/support zones 2–3 days before expiration

### Pre-Expiration Trading Rules
1. **4 days before expiration**
   - Reduce new directional positions
   - Hedge high-Gamma risk
   - Prepare to close or roll positions

2. **1–2 days before expiration**
   - Stop opening new small positions
   - Prioritize closing ATM/ITM contracts
   - Avoid overnight positions

3. **Expiration day**
   - US market closes 1 hour early (15:00 ET)
   - All open contracts automatically exercised or expire
   - Recommendation: close all positions the day before expiration

### Options Expiration Interaction with Credit Spreads
- **New positions**: avoid opening new credit spreads within 3 days of expiration
- **Existing positions**: gradually close or roll 30-DTE spreads 1–2 weeks before expiration
- **Roll strategy**: when position profits 50%, close current month and open same strike next month

## 9. Holiday and Liquidity Risk

### US Market Holidays (Affect Volume and Bid-Ask Spreads)
- **Memorial Day**: Last Monday in May → early close or market closed
- **Independence Day**: July 4 (weekend-adjusted) → market closed
- **Thanksgiving**: 4th Thursday in November → open with early close
- **Christmas**: December 25 (weekend-adjusted) → market closed
- **New Year's**: January 1 (weekend-adjusted) → market closed

Use WebSearch for the current year's exact holiday schedule: `"NYSE holiday schedule [year]"`

### Risks During Low-Liquidity Periods
- **Volume decline**: 50–70% relative to normal levels
- **Wider bid-ask spreads**: 3–5× normal spread
- **Unfavorable options pricing**: slippage increases significantly
- **Conclusion**: **avoid opening new positions within 3–5 days before a holiday**

### Pre/Post-Holiday Trading Rules
1. **5 days before the holiday**
   - Reduce position size by 30–50%
   - Prioritize closing losing or marginal positions
   - Reduce overnight exposure

2. **Holiday week**
   - No new positions
   - Monitor existing position Greeks
   - Prepare re-entry strategy for post-holiday

3. **First day after the holiday**
   - Enter cautiously, watch for gap risk
   - Volume and volatility need 1–2 days to recover
   - Wait for IV normalization before opening positions

### European Holiday Impact on US Markets
- **Christmas holiday** (12/25–1/1): European markets closed, US market liquidity low
- **Easter** (2026: 4/5): European markets closed, US futures volatility increases
- **Observe but not a strict rule**: US market open, but watch for overnight futures gaps

### Economic Calendar vs. Holiday Conflicts
- Fewer economic data releases during holiday weeks
- If data is released, IV premium rises (liquidity already scarce)
- Strategy: avoid bridging economic data releases across holiday periods

## 10. Data Verification and Noise Filtering

### Economic Event Verification Workflow
1. **Initial search**: execute multi-source search (English + Chinese)
2. **Source check**: record all sources and their credibility levels for each event
3. **Cross-validation**:
   - Economic calendar events: at least **2 independent calendar sources** confirming time and data
   - If dates conflict (e.g., one source says 3/26, another says 3/28): use majority source or official Fed website
   - If sources are insufficient: credibility = 2/5, cannot be used for trading
4. **Timestamp recording**: each search result must record query time (HH:MM ET) to ensure data freshness

### Geopolitical Event Verification Workflow
1. **Urgency screening**: immediately search 3+ independent sources
   - English source requirements: Reuters, Bloomberg, AP News, CNN (credibility 4–5)
   - Chinese source requirements: 華爾街見聞, 鉅亨網, 金十數據 (credibility 3–4)
   - Social media or speculation: automatically credibility = 1–2/5 (not usable)

2. **Source diversity**:
   - Multiple articles from a single publisher = still counts as a single source
   - True "multi-source" = completely independent news organizations
   - Official statements (government, military) count as 2 sources

3. **Market reaction validation** (ensure market awareness):
   - WebFetch relevant commodity price movements (oil, gold, US Treasuries)
   - Confirm whether market is reacting as the news would predict
   - If market reaction doesn't match news: downgrade credibility, may be old news or overstated

### Misinformation Filter Framework (same as opportunity-detector)
1. **Source check**: is it from a known credible institution?
2. **Timing check**: is the news publication time reasonable? (cannot be a future date or outdated information)
3. **Fact check**: is the news content consistent with official statements?
4. **Market check**: do financial markets reflect this information?
5. **Multi-source confirmation**: at least 3 independent sources reporting the same event?

### Verification Checklist Before Output
Before completing each event analysis, ensure:
- [ ] Economic calendar events confirmed by **2+ calendar sources**
- [ ] Geopolitical events confirmed by **3+ news sources** (credibility ≥ 3/5)
- [ ] IV data timestamp within **24 hours** (intraday freshness)
- [ ] Timestamps fully recorded (query date and time)
- [ ] Misinformation filtered (single-source or low-credibility stale news excluded)
- [ ] ETF impact assessment has basis (reference to similar past events)

## 11. Output Format and Deliverables

### Event Risk Calendar
```
| Date | Event Name | Event Type | Affected ETFs | Risk Level | Current IV Premium | Credibility | Recommended Strategy | Sources |
|------|-----------|-----------|--------------|-----------|-------------------|------------|---------------------|---------|
| [date] | FOMC Decision | Economic | SPY, QQQ, TLT | 5/5 | +22% | 5/5 (official) | T+1-3: sell spreads | Federal Reserve |
| [date] | NFP | Economic | SPY, IWM | 4/5 | +10% | 5/5 (BLS) | T+1-3: Bull Put Spread | BLS, Trading Economics |
| [date] | Active geopolitical risk | Geopolitical | XLE, GLD | 4/5 | +15% (GLD) | 4/5 (3+ sources) | Wait, open T+1 after event | Major newswires |
```

### IV Premium Summary
```
## Current IV Premium Status (query time: [timestamp])

### SPY
- Current IV: [fetch via WebSearch]
- Baseline IV (30-day avg): [calculate or estimate]
- Event Premium: [calculate]
- Driver: [identify via WebSearch]
- Signal: sell signal if premium >20%, neutral if 10-20%
- Recommendation: wait for post-event T+1-3 window if premium >20%

### QQQ
- Current IV: [fetch via WebSearch]
- Baseline IV: [calculate or estimate]
- Event Premium: [calculate]
- Driver: [identify via WebSearch]
- Recommendation: [based on premium level]

### TLT
- Current IV: [fetch via WebSearch]
- Baseline IV: [calculate or estimate]
- Event Premium: [calculate]
- Driver: [identify — rate decisions most impactful]
- Recommendation: small position if premium <15%, otherwise wait

### XLE
- Current IV: [fetch via WebSearch]
- Baseline IV: [calculate or estimate]
- Event Premium: [calculate]
- Driver: [identify — geopolitical risk and oil moves are key]
- Note: if active geopolitical event, IV may spike >30% → wait for T+1

### GLD
- Current IV: [fetch via WebSearch]
- Baseline IV: [calculate or estimate]
- Event Premium: [calculate]
- Driver: [identify — safe-haven demand, inflation expectations]
- Recommendation: [based on premium level]
```

### Recommended Entry Timing for Credit Spreads
```
## Post-Event Credit Spread Entry Plan

| ETF | Event | Event Date | Expected IV Crush Date | Recommended Strategy | Recommended Entry Date |
|-----|-------|-----------|----------------------|---------------------|----------------------|
| SPY | FOMC/Economic data | [event date] | T+1 to T+3 | Bull Put Spread (5% OTM, $5 wide) | [event date +1] |
| QQQ | Earnings season | [earnings date] | T+1 to T+3 | Bear Call Spread (5% OTM, $5 wide) | [earnings date +1] |
| XLE | Active geopolitical risk | [TBD] | Risk event +3 days | Bull Put Spread (7% OTM, $3 wide) | [event +3 days] |
| TLT | FOMC/Rate decision | [event date] | T+1 to T+2 | Small Bull Put Spread | [event date +1] |

Recommended parameters:
- OTM distance: 5-8% below/above current price
- DTE: 30 days (new position) or 21 days (fast entry)
- Profit target: 20-30% of premium received (close at 5-10 DTE)
- Max loss per spread: capped at spread width × number of contracts
```

### Daily Monitoring Checkpoints
1. **Pre-market (08:00 ET)**: check previous day's new events, update event calendar, IV snapshot
2. **Intraday (11:00 ET)**: verify news sources, update credibility ratings, check geopolitical changes
3. **Intraday (14:00 ET)**: confirm economic data releases, assess IV reaction, evaluate credit spread entry opportunities
4. **Post-close (16:30 ET)**: compile event calendar update, propose next-day recommendations, prepare next-day alerts

## 12. WebSearch and WebFetch Query Templates

### English Economic Calendar Queries
```
WebSearch("economic calendar this week {today}")
WebSearch("FOMC meeting schedule 2026")
WebSearch("NFP jobs report next week")
WebSearch("CPI inflation data release")
WebSearch("GDP economic growth forecast")
WebFetch("investing.com/economic-calendar")
WebFetch("tradingeconomics.com/calendar")
```

### Chinese Economic Calendar Queries
```
WebSearch("金石數據 經濟日曆")
WebSearch("鉅亨網 聯準會會議日程")
WebSearch("華爾街見聞 經濟數據發布")
```

### Geopolitical Queries
```
WebSearch("geopolitical risk {today}")
WebSearch("geopolitical conflict latest news")
WebSearch("Russia Ukraine conflict")
WebSearch("US China trade war")
WebSearch("Middle East tensions")
WebSearch("energy supply disruption")
WebSearch("華爾街見聞 地緣政治")
WebSearch("鉅亨網 國際政治")
```

### IV and Options Data Queries
```
WebSearch("{ETF} implied volatility today")
WebSearch("SPY options IV historical")
WebSearch("QQQ vega exposure earnings")
WebFetch("cboe.com/vix")
WebFetch("investing.com/indices/volatility-s-p-500")
```

### Options Expiration and Max Pain Queries
```
WebSearch("options expiration calendar [current year]")
WebSearch("{ETF} max pain this week")
WebSearch("third Friday options expiration [current month year]")
```

## 13. Integration with Other Skills

### Collaboration Flow
1. **Event Detection**: event-analyst scans multiple sources and identifies high-credibility events
2. **Credit Spread Recommendations**: based on IV crush signals, proposes specific entry timings
3. **Portfolio Monitoring**: tracks how events affect Greeks on existing positions

### Automated Scheduling
- Run event-analyst as part of your daily pre-market scheduled task (08:00 ET)
- If a major event is detected (risk level ≥ 4/5), flag it prominently in the report
- Re-evaluate IV automatically 1–3 days after the event to recommend entries

### Skill Dependencies
- **Upstream**: market-intel provides validated news input
- **Downstream**: trade-analyst uses event risk assessment to weight analyst outputs
- **Parallel**: opportunity-detector may independently flag IV anomalies — coordinate checks

## 14. Multi-Scenario Framework — Event-Driven Probability Updates

### Core Task

The Event Analyst updates scenario probability weights based on geopolitical events and economic data releases. The scenarios are user-configurable (see trade-analyst SKILL.md). Common scenario mapping:

| Scenario Type | Your Role |
|------|---------|
| **Geopolitical / Supply Shock (H1)** | Adjust up on escalation (military action, sanctions); adjust down on de-escalation (ceasefire, diplomacy) |
| **Commodity / Supply Disruption (H4)** | Adjust up on OPEC cuts, supply chain disruptions; adjust down on supply normalization |
| **Base Case / Soft Landing (H0)** | Adjust up when major risk events fail to materialize (ceasefire, sanction relief) |
| **Recession (H5)** | Adjust up when economic data consistently misses (NFP miss + GDP miss) |

### Event → Scenario Probability Mapping

This framework applies to any event scenario:

```
Step 1: Classify the event
  - Geopolitical escalation → primarily affects H1, H4
  - Economic data surprise → primarily affects H0, H2, H5
  - Policy shock (tariffs, sanctions) → affects H0, H3, H5
  - Market structure event (OpEx, liquidity crisis) → affects confidence across all scenarios

Step 2: Quantify probability adjustment
  - High-credibility event (credibility ≥4/5): adjust ±0.05-0.10
  - Medium-credibility event (credibility 3/5): adjust ±0.02-0.05
  - Low-credibility event (credibility ≤2/5): do not adjust — observe only

Step 3: ETF target price implications
  - If geopolitical scenario ↑ → XLE target ↑, GLD target ↑, SPY target ↓
  - If supply shock ↑ → XLE target ↑, TLT target ↑ (flight to safety)
  - If base case ↑ → SPY/QQQ target ↑, GLD/TLT target ↓
```

### Scenario Update Output Format

```
[Multi-Scenario Update — Event]
- Key event this period:
  - [event name] (credibility: X/5, sources: N)
  - Event decay assessment: T+0 impact / T+3 residual
- Scenario probability adjustments:
  - H1 (Geopolitical): [weight] → [new weight] (Reason: [specific event])
  - H4 (Supply Shock): [weight] → [new weight] (Reason: [supply chain change])
  - H0 (Base Case): [weight] → [new weight] (Reason: [did risk materialize?])
- ETF target price implications:
  - XLE: scenario target $X vs event-driven estimate $Y → [aligned / too high / too low]
  - GLD: scenario target $X vs safe-haven demand signal → [aligned / too high / too low]
- Credibility constraint on confidence:
  - Single source → probability adjustment confidence ×0.3
  - Multi-source confirmed → probability adjustment confidence ×1.0
```

### Generalized: Applies to Any Event Scenario

| Event Category | Participants | Affected Hypotheses | Probability Adjustment Logic |
|----------------|-------------|--------------------|-----------------------------|
| Geopolitical conflict | Nations, militaries, alliances | H1, H4 | Escalation → H1/H4↑; de-escalation → H0↑ |
| Tariffs / trade war | US, China, EU | H0, H3, H5 | Tariff increase → H3/H5↑; negotiations → H0↑ |
| Central bank policy | Fed, ECB, BOJ | H0, H2, H5 | Dovish → H2↑; hawkish → H5↑ |
| Tech bubble | AI companies, investors | H3 | Earnings miss → H3↑; continued growth → H0↑ |
| Natural disaster / black swan | Global | All | Confidence sharply reduced, observe |

## 15. Critical Thinking Framework

### Devil's Advocate — Required for Every Event Impact Assessment

After assessing each event's impact, force a counter-argument:

```
[MY CALL] Dovish FOMC → SPY rises → open Bull Put Spread
[COUNTER-ARGUMENT]
1. Market may have already priced in a dovish outcome (CME FedWatch pricing 80% rate cut)
2. "Buy the rumor, sell the news" — even if results meet expectations, SPY may actually fall
3. Historically, post-FOMC price action often reverses by T+3
[CONCLUSION] If already priced in, post-event direction may be opposite to expectations
[ADJUSTMENT] Delay entry to T+2, wait for price action to confirm direction
```

### Overreaction Detection

The event analyst's biggest mistake is **over-amplifying small events**:

| Overreaction Type | Characteristic | How to Avoid |
|------------------|----------------|--------------|
| Confirmation bias | Already have a view, only look at supporting events | Simultaneously search for counter-evidence |
| Recency bias | Recent events are given too much weight | Compare to similar events from 6 months ago |
| Narrative bias | Stringing unrelated events into a "logical chain" | Each causal link needs independent validation |
| Frequency bias | Same event repeated by multiple sources creates false sense of importance | Check whether sources are truly independent |

### Impact Decay Analysis

Not all events have lasting impact. Every event must assess decay speed:

```
Example: Geopolitical escalation event (generic)
T+0: XLE +3%, GLD +2% (peak shock)
T+1: XLE +1%, GLD +0.5% (60% decay)
T+3: XLE +0.2%, GLD +0.1% (nearly absorbed)
T+7: back to pre-event levels (unless escalation continues)

Conclusion: if >50% shock persists at T+3, this is a structural event
if >70% decay by T+1, this is a one-time noise event — do not adjust strategy
```

### Contradiction Detection

Actively identify contradictions between event signals:

```
⚠️ Event contradiction:
- Dovish FOMC (bullish equities) but CPI above expectations (bearish equities) → two events point in opposite directions
- Geopolitical escalation (GLD should rise) but GLD falling → market does not believe conflict will persist
- Most likely explanation: [analysis]
- If explanation is wrong: [specific risk]
```

### "What If I'm Wrong" Mandatory Paragraph

Each event analysis must end with:

```
### What If the Event Impact Judgment Is Wrong
- **Error scenario**: [e.g., "FOMC interpreted as hawkish rather than dovish"]
- **Market reaction**: [if judgment is reversed, what happens to SPY/QQQ/GLD]
- **Existing position risk**: [which positions would be hurt]
- **Stop-loss plan**: [at what price level or under what conditions to close]
```

### Known Unknowns

Each report lists factors currently unresolvable that could change the judgment:
```
### Known Unknowns
1. Extent of disagreement within the Fed on rate cuts (need to wait for FOMC minutes)
2. Whether China will roll out another stimulus round (affects global risk appetite)
3. Whether any active geopolitical conflict has back-channel diplomacy (news could reverse suddenly)
→ These unknowns cap overall confidence at 3/5
```

## Language Configuration

Output in the configured LANGUAGE. Default to English if not set.
