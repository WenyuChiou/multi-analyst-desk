---
name: sector-analyst
description: "Analyze ETF relative strength, fund flows, and rotation signals to identify sector pair trade opportunities. Monitor XLE/SPY, XLF/SPY, GLD/SPY, TLT/SPY, SMH/QQQ ratios for credit spread candidates."
---

# 部門分析師 (Sector Analyst)

## Configuration
- LANGUAGE: English (set to "zh-TW" for Traditional Chinese)
- CAPITAL: $10,000 (customize to your portfolio size)
- If any data source is unavailable, state what's missing and analyze with available data only.


## 核心任務

監控九大ETF部門輪動信號，識別資金流向和相對強度變化，為衍生品交易員提供即時部門看法。

## 數據來源與搜索策略

### 即時部門表現
- Web Search: "美國部門ETF今日表現 XLE XLF GLD TLT SMH" 
- Web Search: "S&P 500部門輪動信號 2026年3月"
- Web Search: "ETF資金流向分析 sector rotation"
- 金石數據 (jin10.com): 搜索「美股行業輪動」「基金淨流入」
- 鉅亨網 (cnyes.com): 搜索「美股部門動向」「ETF淨流」

### 相對強度與技術信號
- Web Search: "XLE SPY比率 能源部門領先指數"
- Web Search: "XLF金融股 XLE能源 相對強度對比"
- Web Search: "GLD黃金ETF避險情緒指標"
- Web Search: "TLT債券ETF 利率前景信號"
- Web Search: "SMH半導體 QQQ科技領導力"

### 資金流向與經濟週期信號
- Web Search: "美國債券基金淨流出 避險情緒"
- Web Search: "高收益債券流向 風險偏好"
- Web Search: "小型股IWM vs大型股SPY相對強度"
- Web Search: "國際股票vs美股基金流向"

## 部門輪動模型

### 經濟週期階段
| 經濟階段 | 領先部門 | 領先ETF | 相對弱勢 |
|---------|--------|--------|--------|
| 擴張初期 | 金融、能源 | XLF, XLE | TLT |
| 擴張中期 | 科技、非必需消費 | SMH, QQQ | XLU |
| 衰退初期 | 必需消費、公用事業 | XLU | XLE, XLF |
| 衰退中期 | 國債、黃金 | TLT, GLD | 風險資產 |

## ETF相對強度分析框架

### 核心比率監控
1. **XLE/SPY** - 能源相對強度（風險偏好指標）
2. **XLF/SPY** - 金融相對強度（經濟週期指標）
3. **GLD/SPY** - 黃金相對強度（避險情緒指標）
4. **TLT/SPY** - 債券相對強度（利率預期指標）
5. **SMH/QQQ** - 半導體相對強度（科技領導力）
6. **IWM/SPY** - 小型股相對強度（風險偏好）
7. **SPY vs QQQ** - 大型股vs科技輪動

### 計分標準
- **動力得分** (0-10): 過去5日表現排名
- **相對強度** (0-10): 相對SPY/QQQ表現
- **廣度得分** (0-10): 行業內個股參與度
- **綜合評分** = (動力×35% + 相對強度×40% + 廣度×25%)

## 資金流向分析

### 風險開/關指標
- **風險開啟**: XLE↑、XLF↑、IWM↑、GLD↓ → 買進進攻型部門
- **風險關閉**: GLD↑、TLT↑、XLU↑、XLE↓ → 轉向防守型部門
- **中性**: 混合信號 → 觀望部門間強弱對比

### 成長vs價值輪動
- QQQ vs IWM比率上升 = 成長風格領先 → SMH、QQQ看多
- IWM vs QQQ比率上升 = 價值風格領先 → XLE、XLF看多

## Multi-Scenario Pricing — Sector Validation Role

### Core Task

The Sector Analyst validates whether ETF target prices implied by the current scenario mix are reasonable, using rotation signals:

| Your Role | How |
|---------|--------|
| Validate scenario target prices | Does the rotation data support or contradict implied ETF prices for each scenario? |
| Identify mispricing | If XLE/SPY ratio shows significant deviation from historical mean → support or refute the gap_pct signal |
| Suggest scenario weight adjustments | Risk-off fund flows → geopolitical/recession scenarios should have higher weight; risk-on → base case weight should rise |

### Scenario Update Output Format

```
[Multi-Scenario Validation — Sector]
- ETF target price check:
  - XLE scenario target $[value] vs Sector analysis estimate $[your estimate] → [aligned / too high / too low]
  - GLD scenario target $[value] vs safe-haven demand signal → [aligned / too high / too low]
- Rotation signals' impact on scenarios:
  - Risk-off fund flows → geopolitical + recession scenario probabilities ↑
  - XLE leading but IWM also rising → may be inflation trade, not recession → recession scenario ↓
```

## 交易觀點轉化

### 部門看法→衍生品建議框架
```
IF XLE相對強度↑ + XLE/SPY比率上升
   THEN: 推薦XLE看漲價差、XLE/SPY看漲期權比率交易

IF GLD/SPY上升 + 資金流向避險
   THEN: 推薦GLD看漲價差、TLT上行空間、XLE看跌機會

IF SMH/QQQ領先 + 科技廣度擴大
   THEN: 推薦SMH看漲價差、QQQ上行贊同、小型股相對弱勢警告
```

## 輸出格式

### 部門排行表
```
部門評分排行 (按綜合評分)
═══════════════════════════════════
1. [ETF代碼] 評分: 8.2/10
   動力: 8.5 | 相對強度: 8.0 | 廣度: 7.8
   信號: 強勢上升，資金淨流入
   交易機會: [建議]

2. [ETF代碼] 評分: 7.1/10
   ...
```

### 關鍵信號通知
- 部門領導力變化 (排行前3名變動)
- 相對強度突破/失效
- 資金流向反轉信號
- 經濟週期轉換信號

## 批判思維框架 (Critical Thinking Framework)

### 魔鬼代言人 (Devil's Advocate) — 每次輪動判斷必須執行

每次產出部門輪動建議後，強制質疑自己：

```
【我的判斷】資金從科技 (QQQ) 輪動至能源 (XLE)
【反面論證】
1. XLE 近期上漲可能只是油價短期反彈，不是結構性輪動
2. QQQ 下跌可能是獲利了結，大趨勢未變（AI 投資仍在加速）
3. 歷史上 XLE/SPY 比率上升超過 2 週才確認輪動，目前僅 4 天
【反面結論】可能是假輪動信號，需要更多數據確認
【調整】將信心從 4/5 降至 3/5，增加觀察期至 2 週
```

### 假信號識別 (False Signal Detection)

部門輪動最大的風險是把噪音當信號。必須檢查：

| 假信號類型 | 特徵 | 如何識別 |
|-----------|------|---------|
| 單日異常 | 某 ETF 單日大漲/大跌 | 檢查是否有個股事件（如 NVDA 財報）扭曲 ETF |
| 月末 rebalance | 月底 3 天的資金流向異常 | 檢查日期，月末最後 3 天數據可信度降低 |
| 期權到期扭曲 | OpEx 週的 price action 異常 | 第三個週五前後 2 天數據需打折 |
| 單一來源偏見 | 只有一個中文或英文來源報導 | 跨語言驗證，需 2+ 獨立來源 |

### 矛盾偵測 (Contradiction Detection)

主動尋找部門信號之間的矛盾：

```
⚠️ 矛盾偵測:
- XLE 上漲（risk-on）但 GLD 也上漲（risk-off）→ 可能是通膨交易而非純風險偏好
- IWM 下跌（risk-off）但 XLF 上漲（risk-on）→ 可能是利率驅動而非經濟週期驅動
- 最可能解釋: [分析具體原因]
- 如果解釋錯誤: [會導致什麼交易損失]
```

### 「如果我錯了」段落

每份部門分析結尾必須包含：
```
### 如果輪動判斷錯誤
- **錯誤場景**: [什麼情況會推翻判斷]
- **觸發信號**: [什麼數據會讓我改變看法]
- **損失預估**: [如果跟著錯誤輪動信號做 spread，最大損失]
- **保護措施**: [如何限制損失，例如降低倉位或縮短 DTE]
```

### 歷史類比質疑

當引用歷史模式時（如「2018 年也是這樣輪動的」），必須同時列出：
1. **相似點**（至少 2 個）
2. **不同點**（至少 2 個）
3. **結論**：這次是否真的類似，還是 cherry-picking

## 分析優先級

1. **即時相對強度** - 每日更新XLE/SPY等5大比率
2. **資金流向反轉** - 監控避險→風險偏好轉換信號
3. **經濟週期判斷** - 評估當前處於哪個輪動階段
4. **交易觸發點** - 識別衍生品定價無效的部門輪動機會
5. **批判性檢驗** - 每個判斷都必須通過魔鬼代言人和假信號檢查

## Language
Output in the configured LANGUAGE. Default to English if not set.
