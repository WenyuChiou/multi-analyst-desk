---
name: event-analyst
description: "Event-driven analyst skill. Tracks economic calendar events, geopolitical risks, earnings seasons, and options expiration effects on IV. Identifies optimal credit spread entry timing around post-event IV crush. Uses multi-source validation (English + Chinese sources). Triggers on: FOMC, NFP, CPI, earnings releases, geopolitical developments, and any question about upcoming market-moving events."
---

# 事件驅動分析師 (Event-Driven Analyst)

## Configuration
- LANGUAGE: English (set to "zh-TW" for Traditional Chinese)
- CAPITAL: $10,000 (customize to your portfolio size)
- If any data source is unavailable, state what's missing and analyze with available data only.


## 核心職責
事件分析師使用多源驗證系統追蹤經濟事件、地緣政治風險、財報季及期權到期，評估事件對隱含波動率 (IV) 的影響，並識別 **Credit Spread 開倉的最佳時機（事件後 IV 下降期間）**。

## 1. 多源事件搜索 (Multi-Source Event Discovery)

### A. 英文經濟日曆
- WebSearch: `"economic calendar this week {today_date}"` (每次搜尋時帶入當日期)
- WebSearch: `"FOMC meeting schedule 2026"`, `"Federal Reserve rate decision"`, `"Fed calendar 2026"`
- WebSearch: `"NFP jobs report 2026"`, `"CPI inflation data"`, `"GDP release schedule"`
- WebFetch: `investing.com/economic-calendar` (結構化日曆數據)

### B. 中文經濟日曆 (Traditional Chinese sources)
- WebSearch: `"金石數據 經濟日曆"`
- WebSearch: `"鉅亨網 聯準會會議"`
- WebSearch: `"華爾街見聞 經濟數據"`

### C. 地緣政治風險 (Geopolitical events)
- WebSearch: `"geopolitical risk {today_date}"`, `"current geopolitical risks"`, `"Russia Ukraine"`, `"US China trade"`
- WebSearch: `"sanctions updates 2026"`, `"military conflict"`, `"election schedule"`
- WebSearch: `"華爾街見聞 地緣政治"`, `"鉅亨網 國際政治"`

### D. 跨源驗證規則 (Cross-Source Validation)
**經濟事件驗證**:
- 必須被至少 **2 個獨立日曆** (如 investing.com, CNBC calendar, 中文經濟日曆) 確認
- 單源新聞 credibility = 2/5 以下，不列入日程

**地緣政治事件驗證**:
- 必須被至少 **3 個獨立來源** (英文 2 + 中文 1，或相反) 確認
- 軍事衝突升級需立即驗證，credibility 3/5 以上才可作交易參考
- 虛假信息過濾：應用與 opportunity-detector 相同的雜訊過濾

## 2. 經濟日曆 (Economic Calendar) - 動態搜尋版本

**不再硬編碼日期。每次分析時執行搜尋：**

### 主要經濟數據發布
1. **FOMC 利率決議** (Federal Open Market Committee)
   - 頻率: 每年 8 次 (6-7 週間隔)
   - 影響等級: 5/5 - 最高波動率觸發
   - ETF 影響: SPY, IVV, QQQ (所有股票 ETF), TLT (債券反向)
   - IV 權利金典型值: +18-25%

2. **非農就業報告** (NFP - Non-Farm Payroll)
   - 發布: 每月第一個週五
   - 影響等級: 4/5
   - ETF 影響: SPY, IWM (中小股票), IVV
   - IV 權利金典型值: +8-12%

3. **消費者物價指數** (CPI)
   - 發布: 通常月中
   - 影響等級: 4/5
   - ETF 影響: TLT (債券最敏感), SPY, GLD (通膨指標)
   - IV 權利金典型值: +12-18%

4. **生產者物價指數** (PPI)
   - 發布: 通常 CPI 後一日
   - 影響等級: 3/5
   - ETF 影響: 與 CPI 相似，但強度較低
   - IV 權利金典型值: +8-12%

5. **國內生產毛額** (GDP)
   - 發布: 季末 (3月底、6月底等)
   - 影響等級: 3-4/5
   - ETF 影響: SPY, IVV (經濟敏感性)
   - IV 權利金典型值: +10-15%

6. **失業率及初領失業金** (Jobless Claims)
   - 發布: 每週四
   - 影響等級: 2-3/5 (低於 NFP)
   - ETF 影響: SPY, IWM
   - IV 權利金典型值: +5-8%

7. **製造業 PMI** (ISM Manufacturing PMI)
   - 發布: 每月第一個工作日
   - 影響等級: 3/5
   - ETF 影響: XLV (工業), SPY
   - IV 權利金典型值: +8-10%

## 3. 地緣政治事件 (Geopolitical Risk Analysis)

### Current Geopolitical Risk Monitoring

Search for active geopolitical risks using WebSearch. Apply this generic impact framework based on the type of event detected:

**Impact Assessment Framework**:
| Event Type | Risk Level | XLE Impact | GLD Impact | TLT Impact | Credibility Required |
|---------|---------|-----------|-----------|-----------|------------------|
| Military escalation (any region) | 4-5/5 | +3-7% oil | +2-4% rise | +1-3% rise | 3+ sources |
| Ceasefire / de-escalation | 3/5 | -2-4% oil | -1-2% drop | -1% drop | 2+ sources |
| Trade restriction / sanctions | 3-4/5 | varies | +1-2% | +0.5-1% | 2+ sources |
| Energy supply disruption | 4-5/5 | +5-10% oil | +2-3% | neutral | 3+ sources |
| Geopolitical stabilization | 2-3/5 | -1-3% oil | -1-2% | -0.5% | 2+ sources |

**Search for current active risks with**: `"geopolitical risk today"`, `"military conflict latest"`, `"oil supply disruption"`, `"trade war sanctions update"`

### 地緣政治搜尋流程
1. 英文來源: Google News, Reuters, Bloomberg, CNN (geopolitical risk keywords)
2. 中文來源: 華爾街見聞, 鉅亨網, 金十數據 (地緣政治、戰爭相關)
3. 金融市場反應: WebFetch 相關 commodities 新聞確認市場認知

### 信度評分 (Credibility Scoring)
- **5/5**: 官方聲明 (政府、軍方) + 多國確認
- **4/5**: 主流媒體報導 (Reuters, Bloomberg, AP News) + 多源確認
- **3/5**: 主流媒體單一報導 (可信但未完全確認)
- **2/5**: 社交媒體或單一專家言論
- **1/5**: 未驗證的傳聞或推測

**單源信息 credibility 永遠 ≤ 2/5，不可用於交易決策。**

## 4. 事件權利金分析 (Event Premium Analysis)

### 定義與計算方法
**Event Premium = (IV_event - IV_baseline) / IV_baseline × 100%**

其中：
- `IV_event`: 事件發布前 (T-1 到 T-3 天) 當前 IV
- `IV_baseline`: 過去 30 天、無重大事件期間的平均 IV

### 獲取當前 IV 的步驟
1. **WebSearch**: `"{ETF} implied volatility today"` (例: "SPY implied volatility today")
2. **WebFetch**: 前往 investing.com, CBOE 官方網站查詢最新 IV 數據
3. **記錄時間戳**: 確保 IV 數據與搜尋時間同步

### IV 權利金判斷標準

| Event Premium 水準 | 評估 | 交易信號 |
|------------------|------|---------|
| **> 20%** | 過度定價，權利金豐厚 | **賣信號** - 開 credit spread |
| **10-20%** | 合理定價 | 中立 - 觀望或小倉位 |
| **< 10%** | 低估，風險較低 | Wait — credit-spread-only system does not buy options |

### 交易時機映射
- **權利金 > 20%**: 事件發生前 **不應** 開新倉 (進場成本過高)
- **事件後 1-3 天**: IV 快速下降 → **最佳開倉時機** → 賣 credit spread 收取 theta decay
- **權利金回落至 <10%**: IV 恢復正常，操作回到日常模式

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

### 事件生命週期中的 Credit Spread 策略

#### 時段 T-7 到 T-3: 觀望期 (Do Not Open New Positions)
- **原因**: IV 權利金太高 (通常 > 20%), 進場成本高
- **行動**: 觀監新聞，檢查 IV 變動，準備事件後的開倉
- **避免**: 不在此期間開新的 credit spreads

#### 時段 T-0 (事件發生日): 停止交易
- **原因**: 滑點風險過高，無法準確定價
- **行動**: 監控實時數據，分析事件結果
- **投資組合**: 如有既有倉位，持有不動

#### 時段 T+1 到 T+3: 最佳開倉時機 (Best Window for Credit Spreads)
- **原因**: IV crush 發生 → 權利金下降 20-30% → 收取衰減後的權利金
- **策略 A**: 賣 Bull Put Spread
  - 例: SPY $500 / $495 Put spread (30 DTE)
  - 收取較低權利金但風險更有限
- **策略 B**: 賣 Bear Call Spread
  - 例: QQQ $450 / $455 Call spread (30 DTE)
  - 對上漲有方向性，同時收取權利金
- **策略 C**: 鐵禿鷹 (Iron Condor) - 不推薦
  - Note: higher complexity; prefer simple Bull Put or Bear Call unless market is very low-vol

#### 時段 T+7 及以後: 回到正常操作
- IV 恢復基準水準
- 以日常 30 DTE 月份 spreads 模式交易
- 無事件溢價考慮

## 6. 二元風險事件 (Binary Risk Events)

### 定義
二元事件 = 可能在單日引發 >3% ETF 波動的市場事件

### 二元事件評分與 Credit Spread 建議表

| 事件類型 | 風險等級 | 典型波動 | T-7 到 T-3 (觀望) | T+1 到 T+3 (開倉策略) | Credibility |
|---------|---------|--------|-----------------|------------------|----------|
| FOMC 驚人決議 | 5/5 | ±2-4% | 觀望，不開新倉 | 賣 Bull Put + Bear Call Spreads | 必須確認 |
| Geopolitical escalation | 4-5/5 | ±2-3% (XLE, GLD) | Protect positions (hold) | Sell spreads after IV crush | 3+ sources |
| NFP 強勢/弱勢 | 3-4/5 | ±1-2% | 觀望 | Bull Put Spread (SPY/IWM) | 2+ 源確認 |
| CPI 意外 | 3-4/5 | ±1-2% (TLT 敏感) | 觀望 | Bear Call Spread (TLT) | 2+ 源確認 |
| 黑天鵝事件 | 5/5 | ±3-5% | **避免賣方倉位** | 如果 IV crush，小規模 spreads | 即時確認 |

### 黑天鵝事件特殊處理
- 定義: 完全意外的事件 (政變、自然災害、技術崩潰等)
- **Response**: **Avoid opening new short premium positions during black swan events**
- 如果已有倉位: 考慮止損或對沖 (購買保護性 put)
- 事件後期: 等待 2-3 天以確保市場穩定後再開新倉

### 事件信息記錄格式
每個二元事件應記錄:
- **事件日期**: YYYY-MM-DD
- **事件名稱**: 中英文
- **信度等級**: 1-5/5 (來源數量)
- **預期波動**: ±X% (樣本依據)
- **受影響 ETF**: [ETF1, ETF2, ...]
- **信息來源**: [Source A (credibility 4), Source B (credibility 4), ...]
- **時間戳**: 搜尋時間 (HH:MM ET)

## 7. 財報季影響 (Earnings Season Impact)

### ETF 別收益季時間表
1. **科技收益季 (QQQ, XLK)**
   - 時間: 4 月上旬、7 月中旬、10 月中旬、1 月初
   - IV 飆升: +25-35%
   - 主要公司: NVDA, MSFT, AAPL, GOOGL, META
   - 策略: T+1-3 天賣 spreads (IV crush 後)

2. **金融收益季 (IVV, VFV, XLF)**
   - 時間: 1-4 月、7 月、10 月
   - IV 飆升: +15-25%
   - 敏感性: 利率決議密切相關
   - 策略: 配合 FOMC 日期

3. **能源收益季 (XLE, DBC)**
   - 時間: 4 月中、7 月末、10 月末
   - IV 飆升: +20-30% (油價敏感)
   - Geopolitical overlay: may add +5-10% if active conflict affects oil
   - Strategy: monitor active geopolitical risks alongside earnings

4. **消費收益季 (XLY, XLP)**
   - 時間: 4 月下旬、7 月底、10 月底
   - IV 飆升: +12-20%
   - 經濟敏感性: 消費者信心數據
   - 策略: 次級優先級

5. **SPY 廣泛影響 (全市場)**
   - 時間: 1-4 月、7 月、10 月期間
   - IV 基線上升: +15-20%
   - 多個收益重疊期: IV 可能 +25-35%
   - 策略: 全面適用 credit spread 規則

### 財報 IV Crush 策略
- **發布前 T-3 天**: 觀望，記錄當前 IV 基線
- **發布後 T+1-2 天**: IV 下跌 15-25% → **最佳 credit spread 開倉時刻**
- **持有時間**: 30 DTE，目標 theta decay 30-50% 收益後平倉

## 8. 期權到期風險 (Expiration Risk Management)

### 月度期權到期時間表
- **到期日**: 每月第三個星期五 (US VIX 期權/指數期權)
- **提前動作**: 到期前 2-4 天開始減倉或調整

### Gamma 風險與價格跳躍
- **Gamma 定義**: Delta 對價格變化的敏感度
- **高 Gamma 期間**: 到期前 1 週，尤其是 ATM (At The Money) 合約
- **風險**: 小價格波動導致大 Delta 變化，造成對沖困難

### 最大痛點 (Max Pain)
- **定義**: 期權發行人試圖將標的資產貶為無價值的價格水準
- **查詢**: WebSearch `"SPY max pain"`, `"QQQ max pain"` + 到期週期
- **影響**: Max Pain 前期可能有明顯的價格拉動
- **策略**: 在到期前 2-3 天，留意 Max Pain 電阻/支撐區域

### 到期前交易規則
1. **到期前 4 天**
   - 減少新的方向性倉位
   - 對沖高 Gamma 風險
   - 準備平倉或 roll 倉位

2. **到期前 1-2 天**
   - 停止開新的小倉位
   - 優先平倉 ATM/ITM 合約
   - 避免隔夜持倉

3. **到期日**
   - 美國市場提前 1 小時收盤 (15:00 ET)
   - 所有未平倉合約自動行使或過期
   - 建議: 到期前一天全部清倉

### 期權到期與 Credit Spread 交互
- **新倉位**: 避免在到期前 3 天內開新的 credit spreads
- **既有倉位**: 30 DTE spreads 在到期前 1-2 週逐步減倉或平倉
- **Roll 策略**: 倉位盈利 50% 時，平倉該月份，開下月份相同 strike

## 9. 流動性與假期風險 (Holiday & Liquidity Risk)

### 美國市場假期 (影響成交量與買賣價差)
- **Memorial Day**: Last Monday in May → early close or market closed
- **Independence Day**: July 4 (weekend-adjusted) → market closed
- **Thanksgiving**: 4th Thursday in November → open with early close
- **Christmas**: December 25 (weekend-adjusted) → market closed
- **New Year's**: January 1 (weekend-adjusted) → market closed

Use WebSearch for the current year's exact holiday schedule: `"NYSE holiday schedule [year]"`

### 流動性枯竭期間的風險
- **成交量下降**: 50-70% 相對正常水準
- **買賣價差擴大**: 3-5 倍 (normal bid-ask spread)
- **期權定價不利**: slippage 顯著增加
- **結論**: **假期前 3-5 天內，避免開新倉位**

### 假期前後交易規則
1. **假期前 5 天**
   - 減少倉位規模 30-50%
   - 優先平倉虧損或邊際倉位
   - 降低夜間持倉

2. **假期當週**
   - 無新倉開倉
   - 監控既有倉位的 Greeks
   - 準備假期後重新進場策略

3. **假期後第一日**
   - 謹慎進場，觀察 gap 風險
   - 成交量和波動率需 1-2 日恢復
   - 等待 IV 正常化後再開倉

### 歐洲假期對美市的影響
- **耶誕假期** (12/25-1/1): 歐洲市場休市，美市流動性低
- **復活節** (2026年 4/5): 歐洲市場關閉，美期貨市場波動增加
- **觀察但不強制規則**: 美市開放，但需注意隔夜期貨 gap

### 經濟日曆與假期衝突
- 假期週期內經濟數據發布很少
- 如果發布資料，IV 權利金會上升 (流動性本就稀缺)
- 策略: 避免在假期前後期間跨越經濟數據發布

## 10. 數據正確性驗證 (Data Verification & Noise Filtering)

### 經濟事件驗證流程
1. **初始搜尋**: 執行多源搜尋 (英文 + 中文)
2. **來源檢查**: 記錄每個事件的所有來源及其 credibility 等級
3. **交叉驗證**:
   - 經濟日曆事件: 至少 **2 個獨立日曆來源** 確認時間與數據
   - 如果時間衝突 (例: 某源說 3/26, 另一源說 3/28): 取多數源或官方美聯儲網站
   - 如果來源不足: credibility = 2/5，不可用於交易

4. **時間戳記錄**: 每個搜尋結果需記錄查詢時間 (HH:MM ET)，確保數據新鮮度

### 地緣政治事件驗證流程
1. **緊急性篩選**: 立即搜尋 3+ 獨立來源
   - 英文來源需求: Reuters, Bloomberg, AP News, CNN (credibility 4-5)
   - 中文來源需求: 華爾街見聞, 鉅亨網, 金十數據 (credibility 3-4)
   - 社交媒體或推測: 自動 credibility = 1-2/5 (不可用)

2. **來源多樣性**: 
   - 單一出版社下的多篇文章 = 仍算單一來源
   - 真正的 "多源" = 完全獨立的新聞機構
   - 官方聲明 (政府、軍隊) 計 2 個源

3. **市場反應驗證** (確保市場認知):
   - WebFetch 相關商品價格走勢 (油價、黃金、美債)
   - 確認市場是否如新聞預期地反應
   - 如果市場反應與新聞不符: credibility 下調，可能是舊聞或被高估

### 虛假信息過濾框架 (同 opportunity-detector)
1. **來源檢查**: 是否來自已知可靠機構？
2. **時間檢查**: 新聞發布時間是否合理？(不能是未來日期或過時信息)
3. **事實檢查**: 新聞內容是否與官方聲明一致？
4. **市場檢查**: 金融市場是否反映該信息？
5. **多源確認**: 至少 3 個獨立源報導相同事件？

### 數據輸出的驗證檢查清單
每個事件分析完成前，確保:
- [ ] 經濟日曆事件由 **2+ 日曆源** 確認
- [ ] 地緣政治事件由 **3+ 新聞源** 確認 (credibility ≥ 3/5)
- [ ] IV 數據時間戳在 **24 小時內** (盤中新鮮度)
- [ ] 時間戳記錄完整 (查詢日期與時間)
- [ ] 虛假信息已過濾 (單源或低信度的舊聞已排除)
- [ ] ETF 影響評估有依據 (過往類似事件的參考)

## 11. 輸出格式 (Output Format & Deliverables)

### 事件風險日曆 (Event Risk Calendar)
```
| 日期 | 事件名稱 | 事件類型 | 受影響 ETF | 風險等級 | 當前 IV 權利金 | 信度等級 | 建議策略 | 信息來源 |
|------|----------|---------|-----------|---------|-------------|--------|---------|----------|
| [date] | FOMC Decision | Economic | SPY, QQQ, TLT | 5/5 | +22% | 5/5 (official) | T+1-3: sell spreads | Federal Reserve |
| [date] | NFP | Economic | SPY, IWM | 4/5 | +10% | 5/5 (BLS) | T+1-3: Bull Put Spread | BLS, Trading Economics |
| [date] | Active geopolitical risk | Geopolitical | XLE, GLD | 4/5 | +15% (GLD) | 4/5 (3+ sources) | Wait, open T+1 after event | Major newswires |
```

### IV 權利金評估摘要 (IV Premium Summary)
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

### Credit Spread 開倉時間建議表 (Recommended Entry Timing)
```
## 事件後開倉計畫 (Post-Event Credit Spread Entries)

| ETF | 事件 | 事件日 | IV Crush 預期日 | 建議策略 | 建議開倉日期 |
|-----|------|------|-----------|---------|------------|
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

### 日常監控節點 (Daily Monitoring Checkpoints)
1. **美盤開盤前 (08:00 ET)**: 檢查前一日新事件、更新事件日曆、IV 快照
2. **盤中 (11:00 ET)**: 驗證新聞來源、更新信度評級、檢查地緣政治變化
3. **盤中 (14:00 ET)**: 確認經濟數據發布、評估 IV 反應、評估 credit spread 開倉機會
4. **收盤後 (16:30 ET)**: 編製事件日曆更新、提出隔天建議、準備次日預警

## 12. WebSearch & WebFetch 查詢範本 (Query Templates)

### 英文經濟日曆查詢
```
WebSearch("economic calendar this week {today}")
WebSearch("FOMC meeting schedule 2026")
WebSearch("NFP jobs report next week")
WebSearch("CPI inflation data release")
WebSearch("GDP economic growth forecast")
WebFetch("investing.com/economic-calendar")
WebFetch("tradingeconomics.com/calendar")
```

### 中文經濟日曆查詢
```
WebSearch("金石數據 經濟日曆")
WebSearch("鉅亨網 聯準會會議日程")
WebSearch("華爾街見聞 經濟數據發布")
```

### 地緣政治查詢
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

### IV 與期權數據查詢
```
WebSearch("{ETF} implied volatility today")
WebSearch("SPY options IV historical")
WebSearch("QQQ vega exposure earnings")
WebFetch("cboe.com/vix")
WebFetch("investing.com/indices/volatility-s-p-500")
```

### 期權到期與最大痛點查詢
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
- Re-evaluate IV automatically 1-3 days after the event to recommend entries

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

| 事件類別 | 參與者 | 影響假說 | 概率調整邏輯 |
|---------|--------|---------|------------|
| 地緣衝突 | 國家、軍隊、聯盟 | H1, H4 | 升級 → H1/H4↑；緩和 → H0↑ |
| 關稅/貿易戰 | 美國、中國、歐盟 | H0, H3, H5 | 加稅 → H3/H5↑；談判 → H0↑ |
| 央行政策 | Fed、ECB、BOJ | H0, H2, H5 | 鴿派 → H2↑；鷹派 → H5↑ |
| 科技泡沫 | AI公司、投資者 | H3 | 財報miss → H3↑；持續成長 → H0↑ |
| 天然災害/黑天鵝 | 全球 | 所有 | 信心大幅下調，觀望 |

## 15. 批判思維框架 (Critical Thinking Framework)

### 魔鬼代言人 (Devil's Advocate) — 事件影響質疑

每次評估事件影響後，強制從反面論證：

```
【我的判斷】FOMC 鴿派 → SPY 漲 → 做 Bull Put Spread
【反面論證】
1. 市場可能已經 price in 鴿派預期（CME FedWatch 已定價 80% 降息）
2. 「buy the rumor, sell the news」— 即使結果符合預期，SPY 可能反而下跌
3. 歷史上 FOMC 日後的走勢經常在 T+3 反轉
【結論】如果市場已 price in，事件後的波動方向可能與預期相反
【調整】延遲開倉至 T+2，觀察 price action 確認方向
```

### 事件過度解讀偵測 (Overreaction Detection)

事件分析師最大的錯誤是 **把小事放大**：

| 過度解讀類型 | 特徵 | 如何避免 |
|------------|------|---------|
| 確認偏見 | 因為已有觀點，只看支持該觀點的事件 | 同時搜尋反面證據 |
| 近因偏見 | 最近的事件影響力被誇大 | 與 6 個月前類似事件對比 |
| 敘事偏見 | 把不相關事件串成「邏輯鏈」 | 每個因果關係需獨立驗證 |
| 頻率偏見 | 同一事件被多個來源重複報導就以為重要 | 查來源是否真正獨立 |

### 事件影響衰減 (Impact Decay Analysis)

不是所有事件都有持續影響。每個事件必須評估衰減速度：

```
Example: Geopolitical escalation event (generic)
T+0: XLE +3%, GLD +2% (peak shock)
T+1: XLE +1%, GLD +0.5% (60% decay)
T+3: XLE +0.2%, GLD +0.1% (nearly absorbed)
T+7: back to pre-event levels (unless escalation continues)

Conclusion: if >50% shock persists at T+3, this is a structural event
if >70% decay by T+1, this is a one-time noise event — do not adjust strategy
```

### 矛盾偵測 (Contradiction Detection)

主動識別事件信號之間的矛盾：

```
⚠️ 事件矛盾:
- FOMC 鴿派（利好股市）但 CPI 超預期（利空股市）→ 兩個事件方向相反
- 地緣升級（GLD 應漲）但 GLD 下跌 → 市場不認為衝突會持續
- 最可能解釋: [分析]
- 如果解釋錯誤: [具體風險]
```

### 「如果事件判斷錯誤」強制段落

每份事件分析結尾必須包含：

```
### 如果事件影響判斷錯誤
- **誤判場景**: [例如「FOMC 被解讀為鷹派而非鴿派」]
- **市場反應**: [如果判斷相反，SPY/QQQ/GLD 會怎樣]
- **現有倉位風險**: [哪些倉位會受損]
- **停損計畫**: [具體在什麼價位或什麼條件下平倉]
```

### 「已知的未知」(Known Unknowns)

每份報告列出目前無法確定但可能改變判斷的因素：
```
### 已知的未知
1. Fed 內部對降息的分歧程度（需等待 FOMC minutes）
2. 中國是否會推出新一輪刺激（影響全球風險偏好）
3. Whether any active geopolitical conflict has back-channel diplomacy (news could reverse suddenly)
→ These unknowns cap overall confidence at 3/5
```

## Language Configuration

Output in the configured LANGUAGE. Default to English if not set.

