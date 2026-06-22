# Data Description

本研究使用 9 份 HER2 FET 實驗資料，共分析 72 條感測通道時間序列。

每份 CSV 主要包含以下欄位：

- `Interval`：量測時間
- `Channel`：感測通道編號
- `DeltaId`：汲極電流響應

每份實驗依序包含以下量測階段：

1. PBS baseline
2. 1 ng/mL HER2
3. 5 ng/mL HER2
4. 10 ng/mL HER2
5. 15 ng/mL HER2
6. 25 ng/mL HER2

Channel 9 為參考或機台穩定性監測通道，因此不納入主要 HER2 感測分析。

基於實驗資料使用與分享限制，本 repository 不公開原始 CSV，僅提供資料格式、分析程式及模型輸出結果。
