---
name: event-analyst
description: "事件驅動分析師 skill。追蹤經濟日曆、地緣政治、財報季、期權到期等事件對 IV 和 ETF 價格的影響。在任何涉及事件風險評估、經濟數據發布、地緣政治分析、IV event premium 的場景下使用。排程任務的事件分析環節必須使用此 skill。即使用戶只問「這週有什麼大事」，也應觸發。"
---

# 事件驅動分析師 (Event-Driven Analyst)

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
- WebSearch: `"geopolitical risk {today_date}"`, `"Iran Israel tensions"`, `"Russia Ukraine"`, `"US China trade"`
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

### 當前主要監控: 伊朗-以色列戰爭 (2026 年進行中)

**影響評估框架**:
| 事件類型 | 風險等級 | 對 XLE 影響 | 對 GLD 影響 | 對 TLT 影響 | Credibility 要求 |
|---------|---------|-----------|-----------|-----------|------------------|
| 伊朗導彈攻擊 | 5/5 | +3-5% 油價 | +2-3% 上漲 | +1-2% 上漲 | 3+ 源 |
| 以色列報復 | 4-5/5 | +2-4% 油價 | +1-2% 上漲 | +0.5-1% 上漲 | 3+ 源 |
| 美國介入 | 5/5 | +5-7% 油價 | +3-4% 上漲 | +2-3% 上漲 | 3+ 源 |
| 中東停火談判進展 | -3/5 (負面) | -2-3% 油價 | -1-2% 下跌 | -1% 下跌 | 2+ 源 |

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
| **< 10%** | 低估，風險較低 | 買信號 (但 the trader 系統不買期權) |

### 交易時機映射
- **權利金 > 20%**: 事件發生前 **不應** 開新倉 (進場成本過高)
- **事件後 1-3 天**: IV 快速下降 → **最佳開倉時機** → 賣 credit spread 收取 theta decay
- **權利金回落至 <10%**: IV 恢復正常，操作回到日常模式

## 5. CREDIT SPREAD ONLY - 事件策略 (the trader 系統的唯一允許策略)

### 嚴格規則: 使用者的系統 **僅銷售期權權利金，通過 Credit Spread**。
**永遠不買 Straddles、Strangles、Call Spreads 或任何 Debit 策略。**

### 舊版本的錯誤替換表
| 舊版本建議 (錯誤) | 新版本替換 (正確 - the trader 系統) |
|------------------|--------------------------|
| 買 Straddle | **事件後 IV crush → 賣 Bull Put Spread + 賣 Bear Call Spread** |
| 買 Strangle | **等待事件後 IV 下降，開 credit spread** |
| 買 Call Spread | **無 - 使用 Bear Call Spread (賣 call + 買更高 call)** |
| 買 Put Spread | **無 - 使用 Bull Put Spread (賣 put + 買更低 put)** |

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
  - 原因: 複雜度高，the trader 偏好簡單 spreads
  - 但如果事件低波動，可考慮

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
| 地緣升級 (伊朗攻擊) | 4-5/5 | ±2-3% (XLE, GLD) | 保護倉位 (可持有) | IV crush 後賣 spreads | 3+ 源確認 |
| NFP 強勢/弱勢 | 3-4/5 | ±1-2% | 觀望 | Bull Put Spread (SPY/IWM) | 2+ 源確認 |
| CPI 意外 | 3-4/5 | ±1-2% (TLT 敏感) | 觀望 | Bear Call Spread (TLT) | 2+ 源確認 |
| 黑天鵝事件 | 5/5 | ±3-5% | **避免賣方倉位** | 如果 IV crush，小規模 spreads | 即時確認 |

### 黑天鵝事件特殊處理
- 定義: 完全意外的事件 (政變、自然災害、技術崩潰等)
- 使用者的應對: **避免在黑天鵝期間開新的賣方倉位**
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
   - 地緣政治疊加: 可能額外 +5-10%
   - 策略: 關注伊朗-以色列局勢

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
- **陣亡將士紀念日**: 5月最後一個週一 (2026年 5/25) → 市場提前收盤或休市
- **獨立日**: 7月 4 日 (若在週末，則 7/3 或 7/5) → 市場休市
- **感恩節**: 11 月第四個週四 (2026年 11/26) → 市場開盤，提前收盤
- **聖誕節**: 12 月 25 日 (若在週末，則 12/24 或 12/28) → 市場休市
- **新年**: 1 月 1 日 (若在週末，則 12/31 或 1/2) → 市場休市

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
| 2026-03-26 | FOMC 決議 | 經濟 | SPY, QQQ, TLT | 5/5 | +22% | 5/5 (官方) | T+1-3: 賣 spreads | Federal Reserve |
| 2026-04-03 | NFP | 經濟 | SPY, IWM | 4/5 | +10% | 5/5 (BLS) | T+1-3: Bull Put Spread | BLS, Trading Economics |
| 2026-04-10 | 伊朗報復威脅 | 地緣 | XLE, GLD | 4/5 | +15% (GLD) | 4/5 (3源) | 觀望，T+1 後開倉 | Reuters, Bloomberg, 華爾街見聞 |
```

### IV 權利金評估摘要 (IV Premium Summary)
```
## 當前 IV 權利金狀況 (查詢時間: 2026-03-24 14:30 ET)

### SPY (標普 500 ETF)
- 當前 IV: 20.5
- 基準 IV (30天平均): 16.8
- 權利金: +22.0%
- 驅動因素: FOMC 決議預期 (2026-03-26)
- 交易信號: **賣信號** - 開 credit spreads
- 建議: 等待事件後 (3/27-3/29) IV 下降, 開 Bull Put Spread ($500/$495)

### QQQ (科技 100 ETF)
- 當前 IV: 24.1
- 基準 IV: 19.2
- 權利金: +25.5%
- 驅動因素: 4 月科技收益季預期
- 交易信號: **賣信號** - 開 credit spreads
- 建議: 持續觀望，4 月初收益發布後 IV crush，開 Bear Call Spread

### TLT (長期債券 ETF)
- 當前 IV: 12.3
- 基準 IV: 11.5
- 權利金: +7.0%
- 驅動因素: 利率風險，低於 FOMC 期間平均
- 交易信號: 中立
- 建議: 可開小倉位，但不急迫

### XLE (能源 ETF)
- 當前 IV: 18.7
- 基準 IV: 14.2
- 權利金: +31.7%
- 驅動因素: 伊朗衝突升級，油價波動加劇
- 交易信號: **過度定價** - 不建議事件前開倉
- 建議: 等待衝突消息確認後，3 天內 IV 會下降，開 Bull Put Spread

### GLD (黃金 ETF)
- 當前 IV: 13.8
- 基準 IV: 12.5
- 權利金: +10.4%
- 驅動因素: 避險情緒，地緣政治溢價
- 交易信號: 中立偏弱買信號
- 建議: 觀望或小規模倉位，地緣風險消退後 IV 會下降
```

### Credit Spread 開倉時間建議表 (Recommended Entry Timing)
```
## 事件後開倉計畫 (Post-Event Credit Spread Entries)

| ETF | 事件 | 事件日 | IV Crush 預期日 | 建議策略 | 建議開倉日期 |
|-----|------|------|-----------|---------|------------|
| SPY | FOMC | 3/26 | 3/27-3/28 | Bull Put Spread $500/$495 | 3/27-3/28 |
| QQQ | 科技收益季起點 | 4/01-4/07 | 4/08-4/10 | Bear Call Spread $450/$455 | 4/09-4/10 |
| XLE | 伊朗衝突確認 | 待定 | 衝突+3 天 | Bull Put Spread $65/$60 | 衝突+3 天 |
| TLT | FOMC | 3/26 | 3/27-3/28 | 小倉位 Bull Put Spread | 3/28 (可選) |

建議參數:
- 距離: 5-8% OTM (Out of The Money)
- DTE: 30 天 (新倉) 或 21 天 (快速進場)
- 目標收益: 20-30% 權利金 (5-10 DTE 後平倉)
- 最大虧損: the trader account risk per spread should be capped
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
WebSearch("Iran Israel latest news")
WebSearch("Russia Ukraine conflict")
WebSearch("US China trade war")
WebSearch("Taiwan strait tensions")
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
WebSearch("options expiration calendar 2026")
WebSearch("{ETF} max pain this week")
WebSearch("third Friday options expiration March 2026")
```

## 13. 與 QuantAlpha 系統的整合 (Integration with QuantAlpha)

### 事件分析與 War Room 的協調
1. **事件檢測**: event-analyst SKILL 定期掃描多源，發現高信度事件
2. **War Room 面板更新**: 將新事件推送到 War Room Dashboard
3. **Credit Spread 推薦**: 基於 IV crush 信號，提出具體開倉建議
4. **Portfolio Monitoring**: 跟蹤既有倉位在事件期間的 Greeks 變化

### 排程任務中的自動化
- event-analyst 應在每日排程任務中執行 (08:00 ET)
- 如果發現重大事件 (風險等級 ≥ 4/5), 立即發送警報
- 事件後 1-3 天內自動重新評估 IV 並建議開倉

### 與其他 Skills 的依賴關係
- **Upstream**: 來自 news-aggregator 或手動警報的事件輸入
- **Downstream**: 向 spread-validator 提交開倉建議，由其驗證風險
- **Parallel**: opportunity-detector 可能獨立識別 IV anomalies，協作檢查

## 14. GTMVF 博弈論職責 — 事件驅動假說概率更新

### 核心任務

Event 分析師負責根據地緣政治事件和經濟數據發布，更新 GTMVF 假說的概率權重：

| 假說 | 你的職責 |
|------|---------|
| **H1 (伊朗戰爭, base=0.40)** | 根據地緣事件升降級調整概率。攻擊升級 → H1↑；停火談判進展 → H1↓ |
| **H4 (油價衝擊, base=0.20)** | 根據能源供應鏈事件調整。OPEC 減產、霍爾木茲海峽封鎖威脅 → H4↑；增產/替代能源突破 → H4↓ |
| **H0 (軟著陸, base=0.20)** | 當重大風險事件未兌現（如停火、制裁緩和）→ H0↑ |
| **H5 (衰退, base=0.25)** | 當經濟數據連續低於預期（NFP miss + GDP miss）→ H5↑ |

### 事件 → 假說概率映射框架

此框架適用於任何事件場景（不僅限於伊朗戰爭）：

```
Step 1: 事件分類
  - 地緣衝突升級 → 主要影響 H1, H4
  - 經濟數據意外 → 主要影響 H0, H2, H5
  - 政策突變 (關稅/制裁) → 影響 H0, H3, H5
  - 市場結構事件 (OpEx, 流動性危機) → 影響所有假說信心水準

Step 2: 量化概率調整
  - 高信度事件 (credibility ≥4/5): 概率調整幅度 ±0.05-0.10
  - 中信度事件 (credibility 3/5): 概率調整幅度 ±0.02-0.05
  - 低信度事件 (credibility ≤2/5): 不調整概率，僅標記觀察

Step 3: ETF 目標價連動
  - 如果 H1↑ → XLE 目標價↑、GLD 目標價↑、SPY 目標價↓
  - 如果 H4↑ → XLE 目標價↑、TLT 目標價↑（避險）
  - 如果 H0↑ → SPY/QQQ 目標價↑、GLD/TLT 目標價↓
```

### 概率更新輸出格式

```
【GTMVF 驗證 — Event】
- 本期關鍵事件:
  - [事件名稱] (credibility: X/5, 來源數: N)
  - 事件影響衰減評估: T+0 impact / T+3 residual
- 假說概率調整建議:
  - H1(伊朗戰爭): 0.40 → 0.[新值] (原因: [具體事件])
  - H4(油價衝擊): 0.20 → 0.[新值] (原因: [供應鏈變化])
  - H0(軟著陸): 0.20 → 0.[新值] (原因: [風險是否消退])
- ETF 目標價影響:
  - XLE: GTMVF 目標 $X vs 事件驅動估值 $Y → [一致/偏高/偏低]
  - GLD: GTMVF 目標 $X vs 避險需求信號 → [一致/偏高/偏低]
- 事件信度對信心的約束:
  - 單源事件 → 概率調整信心 ×0.3
  - 多源確認 → 概率調整信心 ×1.0
```

### 通用化：適用於任何事件場景

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
事件: 伊朗攻擊以色列
T+0: XLE +3%, GLD +2% (衝擊最大)
T+1: XLE +1%, GLD +0.5% (衰減 60%)
T+3: XLE +0.2%, GLD +0.1% (幾乎消化)
T+7: 回到事件前水準 (除非升級)

結論: 如果 T+3 仍有 >50% 衝擊殘留，才是真正的結構性事件
如果 T+1 已衰減 >70%，這是一次性噪音，不應影響策略
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
3. 伊朗衝突是否有秘密外交（消息面可能突然反轉）
→ 這些未知因素使整體信心上限為 3/5
```
