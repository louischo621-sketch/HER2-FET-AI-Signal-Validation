# AI-Assisted HER2 FET Signal Validation and Calibration Curve Construction

本專題建立一套 AI 輔助 HER2 FET 生物感測訊號驗證流程，目的不是直接進行臨床診斷，而是判斷哪些感測通道具有穩定且可用於濃度檢量線建構的訊號品質。

## Project Overview

EDL-FET / FET 生物感測器在量測 HER2 濃度時，不同晶片與不同 Channel 可能受到初始偏移、雜訊、漂移與非單調響應影響。若直接將所有通道納入檢量線，可能會降低濃度響應的一致性。

因此，本研究結合：

- Physics-guided teacher rules
- 1-D CNN waveform learning
- Physical feature extraction
- Leave-One-Experiment-Out validation
- HER2 calibration curve comparison

建立一套可解釋的訊號品質驗證方法。

## Dataset

本研究使用 9 份 HER2 FET 實驗 CSV 資料。每份實驗包含 9 個 Channel，其中 Channel 9 作為參考或機台穩定性觀察通道，因此不納入主要 HER2 感測分析。

主要分析資料：

- 9 experiments
- 8 sensing channels per experiment
- Total: 72 analyzed channels
- Concentration stages: PBS, 1, 5, 10, 15, 25 ng/mL HER2

原始 CSV 檔因資料管理限制未公開於本 repository，僅提供分析流程、程式與主要結果。

## Methodology

1. Load HER2 FET CSV files
2. Exclude Channel 9 reference channel
3. Segment PBS and HER2 concentration stages
4. Extract steady-state signal response
5. Compute physical features such as signal drop, slope, SNR, monotonicity, and R2
6. Generate physics-guided teacher pseudo-labels
7. Train a dual-input 1-D CNN model
8. Evaluate the model using Leave-One-Experiment-Out validation
9. Compare calibration curves before and after signal validation

## Model Design

The proposed model contains two input branches:

- Waveform branch: learns time-series signal patterns from normalized current responses
- Feature branch: learns physics-based signal descriptors extracted from each channel

The final output is an AI valid probability, indicating whether a channel is suitable for calibration curve construction.

## Evaluation Results

The model was evaluated against physics-guided teacher pseudo-labels.

| Metric | Value |
|---|---:|
| Accuracy | 0.958 |
| Precision (Valid) | 0.833 |
| Recall (Valid) | 0.909 |
| F1-score | 0.870 |
| MCC | 0.846 |
| Cohen's kappa | 0.845 |
| ROC-AUC | 0.934 |
| PR-AUC | 0.848 |

Confusion matrix result:

- Teacher Invalid / AI Invalid: 59
- Teacher Invalid / AI Valid: 2
- Teacher Valid / AI Invalid: 1
- Teacher Valid / AI Valid: 10

## Calibration Curve Comparison

| Method | Channels | Linear R2 | Log R2 | Estimated LOD |
|---|---:|---:|---:|---:|
| All channels | 72 | 0.837 | 0.920 | 41.156 |
| Teacher valid | 11 | 0.925 | 0.958 | 11.588 |
| CNN valid | 12 | 0.886 | 0.910 | 18.177 |
| Consensus valid | 10 | 0.913 | 0.963 | 11.645 |

After signal validation, the consensus-valid channels achieved a higher logarithmic calibration R2 and a lower estimated LOD compared with all channels. This indicates that combining physical rules with AI-based signal validation can improve the consistency and sensitivity of HER2 calibration curve construction.

## Repository Structure

```text
HER2-FET-AI-Signal-Validation/
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
    └── her2_fet_ai_validation_paper_figures_human_fig04.zip
## Conclusion

本研究證明，AI 並非用來取代 HER2 檢量線，而是作為檢量線建立前的訊號品質驗證工具。透過 physics-guided rules 與 1-D CNN 分類模型，本研究可自動辨識較可靠的 FET 感測通道，降低低品質訊號對檢量線的干擾，進而提升 HER2 濃度分析的一致性與可解釋性。

## Notes

本專題僅用於學術研究與 FET 訊號品質驗證。目前結果不應解讀為臨床 HER2 診斷效能，也不能直接作為乳癌診斷或治療判斷依據。
