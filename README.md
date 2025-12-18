# 台指期震盪洗盤偵測指標 (TXF Choppy Market Detector)

## 專案簡介 (Introduction)
本專案為 TradingView Pine Script v5 開發的技術指標腳本，專為 **台指期 (TXF)** 當沖交易設計。

台指期市場特性常伴隨急拉急殺後的無趨勢盤整（俗稱「雙巴盤」或「洗盤」）。本腳本旨在透過 **波動率分析** 與 **趨勢效率指標**，過濾掉無效的趨勢訊號，並在盤整區間內偵測主力掃單（Stop Hunting）造成的假突破與假跌破行為。

## 核心原理與演算法 (Core Algorithms & Logic)

本策略基於 **均值回歸 (Mean Reversion)** 理論，結合以下兩大核心算法：

### 1. 市場狀態判定：Choppiness Index (CHOP)
我們使用 **Choppiness Index** 來量化市場的「混亂程度」。該指標源自 E.W. Dreiss 的研究，基於 **分形維度 (Fractal Dimension)** 概念。

*   **演算法公式**：
    $$ CI = 100 \times \frac{\log_{10}(\sum_{i=1}^{n} ATR_1)}{\log_{10}(n) + \log_{10}(\frac{High_{n} - Low_{n}}{ATR_{sum}})} $$
    *(註：Pine Script 實作上簡化為路徑長度與價格位移的比率)*

*   **邏輯應用**：
    *   當 `CHOP > Threshold` (預設 50) 時，判定市場為 **非趨勢狀態 (Choppy/Sideways)**。
    *   在此狀態下，趨勢跟隨策略（Trend Following）容易失效，應轉為震盪操作思維。
    *   **視覺化**：腳本將背景標示為灰色，提示交易者進入「垃圾時間」或「震盪區」。

### 2. 洗盤點位偵測：Bollinger Bands (布林通道)
利用標準差 (Standard Deviation) 建構價格的動態邊界。

*   **參數**：預設長度 20，標準差倍數 2.0。
*   **洗盤 (Whipsaw) 判定邏輯**：
    僅在 **市場處於震盪狀態 (is_choppy = true)** 時啟動偵測：
    
    *   **誘多 (Bull Trap / Washout High)**：
        $$ High > UpperBand \quad \text{AND} \quad Close < UpperBand $$
        *解釋：價格盤中突破上軌（誘發突破買單），但收盤無力站穩，跌回通道內。視為頂部假突破。*
        
    *   **誘空 (Bear Trap / Washout Low)**：
        $$ Low < LowerBand \quad \text{AND} \quad Close > LowerBand $$
        *解釋：價格盤中跌破下軌（誘發追空賣單），但收盤拉回通道內。視為底部假跌破。*

## 腳本功能 (Features)

1.  **動態背景變色**：自動識別盤整盤（灰色背景），輔助交易者避開趨勢策略的虧損期。
2.  **反轉訊號標籤**：
    *   🔴 **洗盤誘多**：標示於 K 棒上方。
    *   🟢 **洗盤誘空**：標示於 K 棒下方。
3.  **即時資訊面板**：右下角顯示當前市場狀態與 CHOP 數值，便於量化監控。
4.  **警報系統 (Alerts)**：已內建 `alertcondition`，可串接 TradingView App 推送或 Webhook 自動下單。

## 參數設定 (Settings)

| 參數類別 | 參數名稱 | 預設值 | 說明 |
| :--- | :--- | :--- | :--- |
| **布林通道** | Length | 20 | 均線計算週期 |
| | Multiplier | 2.0 | 標準差倍數 |
| **CHOP 指標** | Length | 14 | 震盪指標計算週期 |
| | Threshold | 50.0 | 震盪判定門檻 (越高越嚴格，建議範圍 45-60) |

## 安裝與使用 (Installation)

1.  複製 `TXF_Choppy_Detector.pine` 中的程式碼。
2.  開啟 TradingView 圖表 -> 下方 "Pine Editor"。
3.  貼上程式碼並點擊 "Add to chart"。
4.  **建議週期**：5 分鐘 (5m) 或 15 分鐘 (15m)。

## 免責聲明 (Disclaimer)
本腳本僅供技術分析研究與輔助參考，不代表任何金融商品之買賣建議。使用者應自行承擔交易風險。

---
*Created by [Your Name]*# pine-script-txf
