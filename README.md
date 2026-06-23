# 乳癌遠端轉移風險預測模型  
## —檢量線建構與訊號驗證輔助模型

本專題以 HER2 EDL-FET 生物感測電流訊號為研究基礎，建立一套結合訊號品質驗證與 HER2 濃度檢量線建構的輔助模型。研究重點並非直接以 AI 取代檢量線或進行臨床診斷，而是透過機器學習與物理規則判斷哪些感測通道具有穩定、可解釋且適合用於濃度分析的訊號品質。

## 專題概述

EDL-FET 生物感測器在量測 HER2 濃度時，不同晶片與不同通道可能受到初始偏移、雜訊、漂移、低響應與非單調變化影響。若未經篩選便將所有通道納入檢量線，容易使濃度響應混入非目標因素，降低檢量線的一致性與可信度。

因此，本研究結合以下方法建立訊號驗證流程：

- 物理規則導向之 teacher pseudo-label
- 1-D CNN 時序波形學習
- 物理特徵擷取
- Leave-One-Experiment-Out 跨實驗驗證
- HER2 線性與對數濃度檢量線比較

本模型的角色是作為檢量線建立前的訊號品質驗證工具，協助篩選具有定量價值的 FET 感測通道，降低雜訊與異常訊號對 HER2 濃度判讀造成的干擾。

## 資料集

本研究使用 9 份 HER2 FET 實驗 CSV 資料。每份實驗包含 9 個通道，其中 Channel 9 主要作為參考或機台穩定性觀察通道，因此不納入主要 HER2 感測分析。

主要分析資料如下：

- 實驗數量：9 份
- 每份實驗納入通道：8 個感測通道
- 總分析通道數：72 條
- HER2 濃度階段：PBS、1、5、10、15、25 ng/mL

原始 CSV 檔因資料管理限制未公開於本 repository，本 repository 僅提供分析流程、程式、模型輸出與主要成果圖表。

## 研究方法

1. 讀取 9 份 HER2 FET CSV 實驗資料
2. 排除 Channel 9 參考通道
3. 切分 PBS 與不同 HER2 濃度階段
4. 擷取各濃度階段之穩態訊號響應
5. 計算訊號特徵，例如 signal drop、slope、SNR、monotonicity 與 R²
6. 建立 physics-guided teacher pseudo-label
7. 訓練雙輸入 1-D CNN 模型
8. 使用 Leave-One-Experiment-Out 方式進行跨實驗驗證
9. 比較訊號驗證前後之 HER2 濃度檢量線表現

## 模型設計

本研究使用雙分支 1-D CNN 架構：

- 波形分支：學習標準化後的 FET 電流時序訊號
- 特徵分支：學習由物理意義擷取出的訊號描述特徵

模型輸出為 AI valid probability，用以表示該通道是否適合納入 HER2 濃度檢量線建構。

## 模型評估結果

模型以 physics-guided teacher pseudo-label 作為比較基準，評估 AI 對有效與無效通道的辨識能力。

| 評估指標 | 數值 |
|---|---:|
| Accuracy | 0.958 |
| Precision（Valid） | 0.833 |
| Recall（Valid） | 0.909 |
| F1-score | 0.870 |
| MCC | 0.846 |
| Cohen's kappa | 0.845 |
| ROC-AUC | 0.934 |
| PR-AUC | 0.848 |

混淆矩陣結果如下：

- Teacher Invalid / AI Invalid：59
- Teacher Invalid / AI Valid：2
- Teacher Valid / AI Invalid：1
- Teacher Valid / AI Valid：10

此結果顯示模型能有效重現 physics-guided teacher 對訊號品質的判斷，且對有效通道具有良好的辨識能力。

## 檢量線比較

| 方法 | 通道數 | 線性 R² | 對數 R² | 估計 LOD |
|---|---:|---:|---:|---:|
| 全部通道 | 72 | 0.837 | 0.920 | 41.156 |
| Teacher 有效 | 11 | 0.925 | 0.958 | 11.588 |
| CNN 有效 | 12 | 0.886 | 0.910 | 18.177 |
| 共識有效 | 10 | 0.913 | 0.963 | 11.645 |

經訊號驗證後，共識有效通道在對數檢量線中取得最高 R²，且估計 LOD 明顯低於全部通道。此結果顯示，結合物理規則與 AI 訊號分類，有助於提升 HER2 濃度檢量線的一致性與低濃度辨識能力。

## Repository Structure

```text
project-root/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── data/
│   └── README.md
│
├── notebooks/
│   ├── README.md
│   └── HER2_FET_AI_signal_validation.ipynb
│
└── results/
    ├── README.md
    ├── fig04_representative_waveforms_raw_drop.png
    ├── fig05a_calibration_linear_scale.png
    ├── fig05b_calibration_log_scale.png
    └── her2_fet_ai_validation_paper_figures_human_fig04.zip
```text
project-root/
...
```

## 結論

本研究證明，AI 並非用來取代 HER2 檢量線，而是作為檢量線建立前的訊號品質驗證工具。透過 physics-guided rules 與 1-D CNN 分類模型，本研究可自動辨識較可靠的 FET 感測通道，降低低品質訊號對檢量線的干擾，進而提升 HER2 濃度分析的一致性與可解釋性。

## 注意事項

本專題僅用於學術研究與 FET 訊號品質驗證。目前結果不應解讀為臨床 HER2 診斷效能，也不能直接作為乳癌診斷、遠端轉移判定或治療決策依據。
