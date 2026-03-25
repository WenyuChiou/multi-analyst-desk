# Intraday Opportunity Scan — 2026-03-25 | 12:47 PM ET

---

## Quick Status

```
SPY:  $531.40  (-0.5% from open)  VIX: 23.8 (+1.4 from morning)
DXY:  104.6    (+0.4 from morning — dollar strengthening)
WTI:  $81.90   (-0.6% intraday)
Time: 12:47 PM ET  |  1h 43m to close
```

---

## Opportunity Alert — GLD

```
URGENCY: MEDIUM-HIGH
Signal: Price dislocation + IV spike

GLD:  $293.80  (was $298.00 at open — dropped $4.20 = -1.41%)
      IV Rank: 72%  (was 45% this morning — jumped +27 points in 4 hours)
      IV/HV:   1.38  (was 1.03 — significant expansion)

Mispricing vs. equilibrium:
  Morning E[Price]: $307  |  Gap was: -3.0%
  Current E[Price]: $307  |  Gap now: -4.4%   ← widened
  Regime zone: noise → uncertain  (zone upgraded due to IV spike)
```

**What Happened:** DXY pushed from 104.2 to 104.6 on stronger-than-expected ISM services print at 11:00 AM. This drove a mechanical GLD selloff — algorithmic dollar/gold correlation trade. The underlying equilibrium model has NOT changed (recession hedge thesis intact). This looks like a technical flush, not a fundamental repricing.

**IV Story:** IV rank jumping from 45% to 72% in 4 hours is unusual for GLD in a non-crisis environment. This is premium sellers getting scared and covering. It is also an opportunity: IV mean-reverts faster than price. If GLD stabilizes, IV collapses before price recovers — and that's where the spread profits.

---

## Opportunity Detector Classification

```
Signal Type:   TRADEABLE (conditional)
Category:      IV spike + price dislocation
Confidence:    3/5  (dollar momentum is real; need it to stall)
Time Horizon:  2-4 sessions for IV mean reversion

Conditions to enter:
  ✓ GLD below $295 (current: $293.80 — satisfied)
  ✓ IV Rank above 65% (current: 72% — satisfied)
  ✗ DXY below 104.5 OR showing reversal sign (current: 104.6 — NOT satisfied)
  ✗ GLD not in active downtrend (1-min chart showing continuation — NOT satisfied)
```

**Verdict: STAND DOWN NOW — SET ALERT for $294.50 GLD + DXY below 104.4**

Do not chase into a moving dollar trend. The setup will be better in 30–60 minutes if DXY stalls. Entering now is trading the signal before it's confirmed.

---

## Conditional Trade Setup (Enter when conditions met)

```
SELL GLD Apr 17 287/284 Bull Put Spread × 2
  Sell: GLD Apr 17 287 Put  (target ~$1.20 if IV stays elevated)
  Buy:  GLD Apr 17 284 Put  (target ~$0.55)
  Net credit target: $0.65 per spread  |  Max loss: $235
  Break-even: $286.35  (GLD would need to fall another -2.5% from current)

Why this strike: 287 puts are -2.3% OTM at current price. With IV rank at 72%,
                 you collect more premium than usual for that distance.
                 If IV mean-reverts to 45-50% without GLD moving, spread
                 loses ~30% of value from IV alone — buy to close for $0.46.

Kelly: 0.30 → 2 contracts appropriate. Max loss $235 = 0.43% of $55k portfolio.
```

---

## Portfolio Check — Current Positions

```
Position                     | Entry  | Current | P&L    | Delta  | Status
-----------------------------|--------|---------|--------|--------|--------
XLE Apr25 82/80 Bull Put ×3  | $0.85  | $0.62   | +$69   | +0.28  | On track
TLT Apr25 85/83 Bull Put ×0  | —      | —       | —      | —      | Pending FOMC
```

**XLE Position Update:** XLE trading at $83.20 (-$0.30 from morning). Spread is at 27% of max profit. Delta slightly reduced from 0.32 to 0.28 — WTI -0.6% intraday is minor pressure. Thesis unchanged. No action required.

---

## Noise Classification — Other Movers

```
Ticker | Move    | Reason               | Classification
-------|---------|----------------------|----------------
QQQ    | -0.8%   | ISM beat → rate fear | NOISE — macro repricing, not mispricing
XLE    | -0.3%   | WTI -0.6%            | NOISE — proportional to oil move
TLT    | -0.4%   | yields up on ISM     | NOISE — event-driven; wait for FOMC
IWM    | -0.6%   | risk-off broad       | NOISE — correlation trade, not signal
```

No new signals on SPY, QQQ, TLT, IWM. All moves are proportional to the ISM event and dollar move. Not actionable.

---

## Next Scan

Scheduled: 14:00 ET
Manual trigger: If DXY crosses 105.0 or GLD crosses $291, run immediate scan.

*Generated: 2026-03-25 12:47 PM ET | Opportunity Detector v1.0*
