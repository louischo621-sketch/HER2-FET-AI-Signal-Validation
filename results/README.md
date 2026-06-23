# Results

This folder contains the main output figures and tables generated from the HER2 FET signal validation workflow.

Recommended files:
- `confusion_matrix.png`: comparison between physics-guided teacher labels and CNN predictions
- `ai_probability_threshold.png`: number of channels retained under different AI probability thresholds
- `representative_waveforms.png`: examples of valid, invalid, and disagreement signal patterns
- `calibration_linear.png`: HER2 linear concentration calibration curve before and after signal validation
- `calibration_log.png`: HER2 logarithmic concentration calibration curve before and after signal validation
- `metrics_table.csv`: model evaluation metrics including Accuracy, Precision, Recall, F1-score, MCC, Cohen's kappa, ROC-AUC, and PR-AUC
- `calibration_summary.csv`: comparison of channel number, R², and estimated LOD under different filtering strategies
