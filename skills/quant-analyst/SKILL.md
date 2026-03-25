---
name: quant-analyst
description: "ETF 選擇權量化分析師 skill。專注於 IV/HV 分析、skew、vol term structure、Greeks 管理、Kelly sizing、mispricing 偵測。在任何涉及量化指標、期權定價、波動率分析、持倉希臘值管理的場景下使用。排程任務的量化分析環節必須使用此 skill。即使用戶只問 VIX 或波動率，也應觸發此 skill 提供專業量化視角。"
---

# 量化分析師 (Quant Analyst) Skill

## 概述
本 skill 提供 ETF 選擇權的專業量化分析，包括隱含波動率 (IV)、實現波動率 (HV)、波動率偏斜 (skew)、期限結構、希臘值管理、Kelly 凱利公式調整、及 mispricing 偵測。

**觸發條件：**
- 任何涉及波動率、期權定價、Greeks、Kelly sizing、波動率期限結構的分析
- 排程任務中的量化指標計算
- 即使簡單問題（如「VIX 今天多少」）也應提供量化背景分析

---

## 1. 多源數據收集與交叉驗證

### 資料來源清單

#### 英文來源：
- **WebSearch**: "VIX term structure today", "{ETF} implied volatility options", "options skew index", "IV percentile"
- **WebFetch**: 
  - vixcentral.com — VIX、VIX3M、VIX6M、VIX9M term structure
  - marketchameleon.com — IV percentile rank、historical volatility comparison
  - cboe.com — CBOE VIX methodology、skew data

#### 中文來源：
- **WebSearch**: 金石數據 "波動率分析"、鉅亨網 "期權隱含波動率"、CMoney "期權 IV"

### 數據交叉驗證規則

**必須遵守：**
1. IV/HV 數據必須來自至少 2 個獨立來源
2. 如果來源間差異 >5%，**必須標記為 "不確定" (Uncertain)**，查詢第三個來源
3. VIX 數據若與 warroom_data.json 差距 >2 點，標記為 "數據可能過時"
4. 記錄數據時間戳，使用時註明數據新鮮度 (時間戳距現在不超過 15 分鐘)

---

## 2. IV vs HV 分析

### 隱含波動率 (IV) 取得

**步驟 1：** WebSearch "{ETF symbol} implied volatility today"
- 查找 CBOE、marketchameleon、thinkorswim 等來源
- 記錄 IV（以 % 表示）、data timestamp

**步驟 2：** WebFetch marketchameleon.com (IV Percentile)
- 提取當前 IV percentile rank（例：45th percentile）
- 表示該 IV 在過去 52 週內排名

### 實現波動率 (HV) 計算

- **20 日 HV**: 計算過去 20 個交易日 log returns 標準差 × √252
- **30 日 HV**: 計算過去 30 個交易日 log returns 標準差 × √252
- **比較結果**: IV vs 20d-HV、IV vs 30d-HV

### 分析框架

| 指標 | 計算方式 | 解讀 |
|------|--------|------|
| IV Rank | (Current IV - 52w Low) / (52w High - 52w Low) × 100 | <30: 低估, 50-70: 正常, >80: 高估 |
| IV vs 20d HV | IV - HV(20d) | >0: IV 高估, <0: IV 低估 |
| IV vs 30d HV | IV - HV(30d) | 趨勢判斷、mean reversion 機會 |

---

## 3. 波動率偏斜 (Skew) 分析

### 數據來源

**WebSearch**: "{ETF} options skew analysis", "put/call IV skew {symbol}"
- 查找 CBOE SKEW index（適用於 SPY）
- 提取 put-side IV 與 call-side IV 差異

**WebFetch**: vixcentral.com (SKEW data)
- 記錄 SKEW 值（>100: 負 skew、<100: 低 skew）

### 偏斜量化指標

```
Put IV - Call IV = Skew 差異
正值：看跌單邊高估（市場恐懼）
負值：看漲單邊高估（市場過度樂觀）
```

### 交易信號

- **Skew >120**: 市場極度恐懼，put 高估 → Bull Put Spread 優選
- **Skew 80-100**: 市場中立
- **Skew <80**: 市場樂觀，call 高估 → Bear Call Spread 優選

---

## 4. 波動率期限結構 (Vol Term Structure)

### 關鍵比率計算

**數據取得：** WebFetch vixcentral.com
- VIX（30 日）
- VIX3M（3 個月）
- VIX6M（6 個月）

**比率計算：**

| 比率 | 計算 | 期限結構含義 |
|------|-----|-----------|
| Term Ratio | VIX3M / VIX | >1.0: contango（正向結構）, <1.0: backwardation（反向結構） |
| 6M Ratio | VIX6M / VIX | >1.0: long-term 高波動, <1.0: 短期市場恐懼 |
| Curve Slope | (VIX6M - VIX) / 180 | 期限結構陡峭度 |

### 結構判斷

- **Contango** (VIX3M > VIX): 市場預期波動率下降，低波波環境
  - 適合：Bull Put Spread（時間衰減有利）
- **Backwardation** (VIX3M < VIX): 市場預期近期波動率升高，市場不確定
  - 適合：謹慎，減少持倉或採用寬鬆距離

---

## 5. 希臘值 (Greeks) 管理

### 讀取來源

**warroom_data.json** 中的 portfolio Greeks：
- 每次執行前讀取當前持倉的累積 Delta、Gamma、Theta、Vega

### 管理閾值

| 希臘值 | 安全範圍 | 警告 | 調整行動 |
|--------|--------|------|--------|
| **Delta** | ±0.15 | ±0.20 以上 | 對沖或平倉部分部位 |
| **Gamma** | <0.02 | >0.03 | 減少新增高 gamma 部位 |
| **Theta** | >0.01/天 | <0 | 檢查是否過度 long vega |
| **Vega** | ±0.10 | ±0.15 以上 | 調整波動率對沖 |

### 希臘值調整邏輯

1. **每日檢查** warroom_data.json portfolio Greeks
2. **Gamma 過高** (>0.03): 減少新增短期期權、優先平倉高 gamma
3. **Delta 偏離** (±0.20+): 買進/賣出對應期權重新平衡
4. **Theta 為負**: 檢查是否承受過高 vega 風險，考慮套利

---

## 6. 自適應 Kelly 凱利公式

### Kelly 基礎公式

```
Kelly % = (Win Rate × Avg Win - Loss Rate × Avg Loss) / Avg Win
最大持倉 % = Kelly % × Risk Capital
```

### 讀取實際數據

**backtest_results.json** (歷史回測):
- win_rate: 平均勝率（例：55%）
- avg_win: 平均勝利報酬（例：1.2%）
- avg_loss: 平均虧損（例：1.0%）

**計算示例：**
```
假設 win_rate=55%, avg_win=1.2%, avg_loss=1.0%
Kelly = (0.55×1.2 - 0.45×1.0) / 1.2 = 0.325 = 32.5%
```

### VIX 風險調整表

| VIX 範圍 | 調整係數 | 實際 Kelly % | 含義 |
|---------|--------|-----------|------|
| 15-25 | 1.0× | 100% Kelly | 低波環境，可用全 Kelly |
| 25-30 | 0.7× | 70% Kelly | 中等波動，降低倉位 |
| 30-35 | 0.5× | 50% Kelly | 高波動，謹慎運作 |
| >35 | 0.25× | 25% Kelly | 極端恐懼，最小倉位 |

### 執行規則

1. 計算基礎 Kelly %
2. 查詢當日 VIX
3. 應用調整係數
4. 實際倉位 = Capital × Adjusted Kelly %

---

## 7. GTMVF 博弈論職責 — 量化驗證 Mispricing

### 核心任務

Quant 分析師負責用技術面和量化指標**驗證 GTMVF 的 mispricing 判斷**：

| 你的職責 | 具體做法 |
|---------|--------|
| 技術面驗證 gap_pct | GTMVF 說 SPY 高估 14.6% — 技術面支持嗎？(偏離 20MA 多少 SD？RSI？) |
| IV 交叉驗證 | 如果 GTMVF 說 XLE 低估 31%，但 XLE IV 極高 → 市場定價了你沒看到的風險 |
| 均值回歸概率 | gap_pct 預計多久回歸？(基於歷史波動率和當前動量) |
| 動量確認/否定 | GTMVF 用 60 日動量過濾，動量對齊 confidence ×1.2，反向 ×0.3 — 你驗證這個 |

### 概率更新輸出格式

```
【GTMVF 量化驗證 — Quant】
- SPY gap +14.6% (高估):
  - 技術面: 偏離 20MA [+/-X] SD → [支持/反駁] GTMVF 判斷
  - 動量: 60 日動量 [上/下] → [確認/否定] 回歸方向
  - IV 信號: IV Rank [X]th → 市場[已/未] price in 風險
  - 量化信心: [X]% 此 mispricing 會在 30 天內回歸

- XLE gap -31.3% (低估):
  - 技術面: XLE 偏離 20MA [+/-X] SD → [支持/反駁]
  - 但 ⚠️ 油價體制覆寫: Brent 漲幅 >30% → XLE 目標被 GTMVF 動態上調
  - 量化信心: [X]% 此 mispricing 可交易
```

## 8. Mispricing 偵測 (Black-Scholes Benchmark)

### 理論價格計算

**用於 SPY、QQQ、GLD、TLT 等主要 ETF：**
- 輸入：S (現貨價格)、K (履約價)、T (到期時間)、r (無風險利率)、σ (IV)
- 使用 Black-Scholes 公式計算理論 call/put 價格

### 定價偏差判斷（CREDIT SPREAD ONLY）

⚠️ **所有 mispricing 交易建議必須是信用價差。Mispricing 偵測告訴你「哪邊高估」，然後你賣掉高估的那邊。**

| 定價偏差 | 判斷 | 信用價差策略 | 邏輯 |
|--------|------|-----------|------|
| Call 高估 (實價 > 理論價 3%+) | Call premium 過貴 | **Bear Call Spread** — 賣高估 Call + 買保護 Call | 收取高估的 call premium ✓ |
| Put 高估 (實價 > 理論價 3%+) | Put premium 過貴 | **Bull Put Spread** — 賣高估 Put + 買保護 Put | 收取高估的 put premium ✓ |
| Call 低估 (實價 < 理論價 3%+) | Call premium 便宜 | **不做** — 低估 = 買入機會但我們不做 debit | 觀察，等 IV 回升再賣 ⏸ |
| Put 低估 (實價 < 理論價 3%+) | Put premium 便宜 | **不做** — 低估 = 買入機會但我們不做 debit | 觀察，等 IV 回升再賣 ⏸ |

**核心邏輯：Mispricing 偵測只用於找「高估」的一邊來賣。低估 = 觀望，因為利用低估需要買入（debit），違反我們的策略。**

### Mispricing 強度評分

| 偏差幅度 | 評分 | 行動 |
|---------|------|------|
| 3-5% | 輕微 mispricing | 記錄但不急於行動 |
| 5-10% | 中度 mispricing | 值得考慮開倉，正常 Kelly |
| >10% | 顯著 mispricing | 優先考慮，但先問「為什麼市場這樣定價？」可能是市場知道你不知道的事 |

**⚠️ 批判思維：>10% 的 mispricing 往往有原因（即將到來的事件、流動性差、結構性變化）。不要假設市場是錯的。**

### 資料來源

- WebSearch: "{ETF} options pricing today", "options implied volatility"
- 提取 bid-ask midpoint 做為市場價
- 計算與理論價差異

---

## 8. 相關性矩陣 (Correlation Matrix)

### 監控配對

**主要交易配對：**
- SPY vs QQQ (大型股 vs 科技股)
- GLD vs TLT (黃金 vs 長期債券)
- XLE vs SPY (能源 vs 大盤)

### 資料取得

**優先順序：**
1. warroom_data.json 中的 correlation matrix（如有）
2. WebSearch: "SPY QQQ correlation", "GLD TLT correlation"
3. 計算過去 20 日 log returns 相關係數

### 相關性應用

| 配對 | 相關性 | 交易建議 |
|------|-------|--------|
| SPY-QQQ | >0.85 | 配對交易空間小，單邊優選 |
| SPY-QQQ | 0.7-0.85 | 配對交易有機會，套利可行 |
| SPY-QQQ | <0.7 | 強烈配對交易信號，相關性回復優先 |
| GLD-TLT | >0.3 | 避免同時做多，波動率分散差 |
| GLD-TLT | <-0.1 | 風險分散良好，可並行運作 |

---

## 9. ⚠️ 信用價差 (CREDIT SPREAD ONLY) 強制規則

### 核心原則

**所有推薦交易必須是信用價差結構。NEVER 建議以下交易：**
- ❌ Straddle / Strangle（雙邊期權）
- ❌ Naked Call / Naked Put（裸選擇權）
- ❌ Debit Spread（借記價差）
- ❌ 任何單邊期權賭注

### 允許的策略

✅ **Bull Put Spread** (看漲時)
- 賣 OTM Put + 買更低 Put → 淨收權利金

✅ **Bear Call Spread** (看跌時)
- 賣 OTM Call + 買更高 Call → 淨收權利金

✅ **Call Ratio Spread** (高度看漲)
- 賣 2× Call @ K1 + 買 1× Call @ K2 → 淨收權利金，但需嚴格 Delta 管理

### 驗證清單

推薦策略時檢查：
- [ ] 策略名稱包含 "Spread"？
- [ ] 是否有限制最大虧損？
- [ ] 是否收取淨權利金？
- [ ] 是否符合 Credit Spread 定義？

**如果任何選項答案為 No，則 STOP 並重新設計交易。**

---

## 10. 數據正確性驗證 (Data Correctness Check)

### 驗證程序

**每次輸出前必須執行：**

1. **VIX 交叉驗證**
   - WebSearch 得到的 VIX vs warroom_data.json VIX
   - 差距 >2 點 → 標記 "⚠️ VIX 數據可能過時，相差 {差距} 點"

2. **IV 數據來源確認**
   - IV 來自幾個來源？ (必須 ≥2)
   - 來源間差異 >{5%}？ → 標記 "⚠️ IV 數據不確定，來源差異 {差異}%，建議查詢第三來源"

3. **時間戳檢查**
   - 資料採集時間是否 <15 分鐘前？
   - 如果 >15 分鐘 → 標記 "⚠️ 數據於 {時間} 採集，已 {分鐘} 分鐘，建議刷新"

4. **warroom_data.json 對齐**
   - 是否存在 warroom_data.json？
   - 其中的 Greeks、現倉數據是否與推薦相符？
   - 如不符 → 標記並提示用戶檢查持倉

### 數據新鮮度標記

在輸出的每個數據點旁邊加上：
```
📊 VIX 19.5 (13:42 ET, 3 min ago) ✓ Fresh
```

---

## 11. 輸出格式：信號儀表板

### 標準輸出結構


### 信號儀表板表格

```
┌─────────────────────────────────────────────────────────────────────┐
│ 📊 ETF 量化信號儀表板                         時間: 2026-03-24 14:30 |
├─────────────────────────────────────────────────────────────────────┤
│ 指標            │ 當前值        │ 狀態      │ 建議            │ 信心度 │
├─────────────────────────────────────────────────────────────────────┤
│ SPY IV Rank     │ 45th %ile      │ 中性      │ 無特定偏向      │ 60%   │
│ SPY IV vs 20HV  │ +1.8%          │ 略高估    │ Bull Put        │ 70%   │
│ QQQ IV Rank     │ 62nd %ile      │ 略高估    │ 謹慎看漲        │ 65%   │
│ SKEW (SPY)      │ 118            │ 恐懼      │ Put 高估        │ 75%   │
│ VIX Term        │ Contango 1.15  │ 正向      │ 時衰有利        │ 80%   │
│ VIX Adj Kelly   │ 22% (0.7×)     │ 中波動   │ 倉位 22% 資本   │ 70%   │
├─────────────────────────────────────────────────────────────────────┤
```

### Kelly 配置建議

```
資本: $YOUR_CAPITAL
基礎 Kelly: 32.5% ($10,725)
VIX 調整 (VIX=26): 0.7× 倍數
→ 實際 Kelly: 22.75% ($7,508)

建議初始倉位: $7,000 - $8,000
(保留部分現金應對突發機會)
```

### Greeks 再平衡檢查

```
當前持倉 Greeks (from warroom_data.json):
- Δ (Delta):     +0.08  ✓ 在 ±0.15 範圍內
- Γ (Gamma):     +0.015 ✓ <0.02 安全
- Θ (Theta):     +$12/day ✓ 正常
- ν (Vega):      +0.05  ✓ <0.10 安全

📋 行動: 無需調整，持倉健康
```

### 數據新鮮度指示

```
📊 數據採集於 2026-03-24 14:27 ET (3 分鐘前) ✓ Fresh
├─ VIX (CBOE):        19.5  [13:25 ET]    ✓ 新鮮
├─ SPY IV (MC):       16.8% [13:30 ET]    ✓ 新鮮
├─ QQQ IV (ThinkorSwim): 17.2% [13:32 ET] ✓ 新鮮
├─ SKEW (CBOE):       118   [13:28 ET]    ✓ 新鮮
├─ Portfolio Greeks:    [讀自 warroom_data.json 14:20] ⚠️ 4 分鐘
└─ 備註: 全部資料 <15 分鐘，可信度高

⚠️ VIX 驗證: WebSearch 19.5 vs warroom_data.json 19.6 (差距 0.1 點) ✓ 一致
```

---

## 12. 執行檢查清單

**每次使用此 Skill 前：**

- [ ] 已通過 WebSearch 或 WebFetch 取得最新 IV 數據？
- [ ] IV 數據來自至少 2 個獨立來源，差異 <5%？
- [ ] VIX 與 warroom_data.json 對齐（差距 <2 點）？
- [ ] 已讀取當前持倉 Greeks 從 warroom_data.json？
- [ ] 推薦的交易策略是 Bull Put Spread 或 Bear Call Spread？
- [ ] 已標記所有數據時間戳和新鮮度？
- [ ] 已檢查所有定價與理論值差異 >3%？
- [ ] 已應用 VIX 調整後的 Kelly 公式？

**如有任何 ❌，停止並補充缺失資訊。**

---

## 13. 工具對應表

| 任務 | 使用工具 | 備註 |
|------|--------|------|
| VIX term structure | WebFetch vixcentral.com | 最權威來源 |
| IV percentile | WebFetch marketchameleon.com | 實時更新 |
| 期權 skew | WebSearch + WebFetch CBOE | 美股選擇權標準 |
| 中文波動率文章 | WebSearch 金石、鉅亨網、CMoney | 參考意見 |
| 實時期權報價 | WebFetch thinkorswim 或實時 API | 需驗證多源 |
| 持倉 Greeks | 讀取 warroom_data.json | 本地數據 |
| 回測結果 | 讀取 backtest_results.json | 歷史勝率 |

**禁止：** ❌ 不使用 mcp__paper-search-mcp__search_google_scholar（已廢棄）

---

## 14. 常見場景觸發

### 場景 1: 「VIX 今天多少」
→ 除了回答 VIX 數字，**必須提供**：
- VIX rank (vs 52週)
- Term structure (Contango/Backwardation)
- 對交易的量化含義

### 場景 2: 「SPY 期權推薦」
→ **必須執行完整流程**：
1. IV Rank + IV vs HV
2. Skew 分析
3. Mispricing 檢測
4. Kelly 倉位計算
5. Greeks 檢查
6. 信用價差推薦 (Bull Put / Bear Call)

### 場景 3: 「調整現有持倉」
→ **必須檢查**：
1. 從 warroom_data.json 讀取當前 Greeks
2. 判斷是否超過 Delta ±0.15 或 Gamma >0.02
3. 推薦再平衡交易（必須信用價差）
4. 計算新 Greeks 目標

### 場景 4: 「持倉風險管理」
→ **自動執行**：
1. 讀取 warroom_data.json
2. 檢查 VIX（可能需要刷新）
3. 應用 VIX 調整 Kelly
4. 計算是否需要平倉部分部位
5. 標記異常 Greeks

---

## 15. 品質保證

### 信心度評分

推薦時提供 **信心度 (Confidence)**：
- **90-100%**: 多個量化指標一致，資料新鮮
- **70-89%**: 大部分指標一致，至少 2 個資料來源
- **50-69%**: 指標混合或資料稍有陳舊
- **<50%**: ❌ 不提供推薦，要求補充資訊

### 不確定情況處理

如果遇到以下情況，**必須停止並標記**：
```
⚠️ 不確定信號，原因：
- IV 來自不同來源差異 7%（超過 5% 閾值）
- warroom_data.json 與 WebSearch VIX 差距 3.2 點（超過 2 點閾值）
- 資料採集於 25 分鐘前（超過 15 分鐘新鮮度）

建議：請重新查詢最新資料或人工確認
```

---

## 16. 批判思維框架 (Critical Thinking Framework)

### 模型懷疑主義 (Model Skepticism)

量化分析師最大的風險是 **過度信任模型**。每次輸出前必須問自己：

```
【模型假設檢查】
1. Black-Scholes 假設常態分佈 — 但市場有 fat tails，我的 mispricing 判斷在極端情況下是否仍成立？
2. IV Rank 基於過去 52 週 — 如果市場結構已改變（如 0DTE 盛行），歷史 rank 是否仍有效？
3. Kelly 公式假設獨立試驗 — 但連續虧損可能有相關性（系統性風險），我是否 oversize？
```

**規則：每份報告至少指出 1 個模型假設可能失效的場景。**

### 魔鬼代言人 (Devil's Advocate) — 量化版

每次產出量化信號後，強制從反面質疑：

```
【量化信號】SPY IV Rank 75th percentile → 高估 → 賣 credit spread
【反面論證】
1. IV 高可能是合理的：如果本週有 FOMC + CPI 雙重事件，IV 應該高
2. IV Rank 基於過去 52 週，但如果去年是極低波動年，75th 可能只是「回到正常」
3. 歷史 mean reversion 的前提是沒有結構性變化，但 0DTE options 改變了 vol surface
【結論】信心從 80% 降至 65%，需要交叉確認事件日曆
```

### 數據質疑 (Data Skepticism)

不要盲目信任數據源，每個數據點都要問：

| 質疑維度 | 具體問題 | 行動 |
|---------|--------|------|
| 時效性 | 這個數據是盤中實時還是昨日收盤？ | 標記時間戳，>15 分鐘打折 |
| 代表性 | 這個 IV 是 ATM 還是某個特定 strike？ | 確認是 30-delta IV 還是 ATM IV |
| 計算方法 | 不同來源的 IV 計算方式可能不同 | 注明來源的計算方法差異 |
| 倖存者偏差 | 回測勝率只看了成功的 ETF？ | 檢查是否包含了表現差的 ETF |

### 矛盾偵測 (Contradiction Detection)

量化指標之間經常矛盾，必須主動識別：

```
⚠️ 量化矛盾:
- IV Rank 高（賣信號）但 Vol Term Structure 是 backwardation（買信號/恐慌）
- 解釋 1: 短期事件推高 VIX，但長期仍看穩 → 可以賣，但縮小倉位
- 解釋 2: 市場嗅到系統性風險 → 不應賣 vol，等待信號澄清
- 最可能: [選擇並解釋]
- 如果錯誤的後果: [具體損失預估]
```

### 「如果模型錯了」強制段落

每份量化報告結尾必須包含：

```
### 如果量化信號錯了
- **模型失效場景**: [例如「如果 VIX 突破 40，Black-Scholes 的常態假設完全失效」]
- **歷史先例**: [列出類似情況下模型失敗的例子，如 2020 年 3 月]
- **最大損失**: [按當前推薦計算的最壞情況]
- **安全閥**: [什麼條件下自動停止交易，例如「VIX >35 時停止所有新倉」]
```

### 過度擬合警告 (Overfitting Warning)

如果某個量化策略的回測結果 **太好**（勝率 > 80%），自動觸發警告：
```
⚠️ 過度擬合風險: 勝率 85% 可能來自：
1. 回測期間恰好是低波動期（2017、2019）
2. 只看了有利的 ETF（倖存者偏差）
3. 參數過度最佳化（Kelly, strike 選擇）
建議: 降低倉位 30% 作為安全邊際
```

## 備註

此 Skill 遵循以下原則：
- ✅ 信用價差優先（Bull Put / Bear Call Spread）
- ✅ 多源數據交叉驗證
- ✅ Greeks 管理嚴格
- ✅ Kelly 公式自適應 VIX
- ✅ 所有推薦附帶信心度評分
- ✅ 所有數據附帶時間戳和新鮮度標記
- ✅ 自動檢查 mispricing 和定價偏差
- ✅ **批判思維：每個信號都經過魔鬼代言人和模型懷疑主義檢驗**
- ✅ **矛盾偵測：主動識別指標間的矛盾並解釋**

---

**最後更新:** 2026-03-24
**維護者:** the trader (量化交易員)
**版本:** 2.0 (完整重寫)
