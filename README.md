# HER2 FET AI Signal Validation and Calibration Curve Construction

## Project Title

Breast Cancer Distant Metastasis Risk Prediction Model  
— HER2 FET Signal Validation and Calibration Curve Construction Using Physics-Guided 1-D CNN

## Overview

This project develops an AI-assisted signal validation workflow for HER2 field-effect transistor (FET) biosensor data.  
The goal is not to directly diagnose breast cancer or predict clinical metastasis, but to improve the reliability of HER2 calibration curve construction by filtering low-quality sensing channels.

The proposed method combines physics-guided rules and a 1-D convolutional neural network (1-D CNN) to identify valid FET sensing channels before establishing the HER2 calibration curve.

## Research Motivation

HER2 is an important biomarker related to breast cancer classification, disease evaluation, and targeted therapy selection.  
FET-based biosensors can convert biomolecular binding events into electrical current responses. However, real experimental signals may be affected by channel variation, baseline drift, background noise, sensor batch differences, and measurement conditions.

If all sensing channels are directly used for calibration curve construction, low-response, noisy, or non-monotonic channels may reduce the reliability of the calibration result.

## Dataset

The dataset consists of HER2 FET experimental CSV files.

Each experiment contains multiple sensing channels and sequential measurement stages:

- PBS baseline
- 1 ng/mL HER2
- 5 ng/mL HER2
- 10 ng/mL HER2
- 15 ng/mL HER2
- 25 ng/mL HER2

Channel 9 was excluded from the main analysis because it was treated as a reference or machine-stability channel.

After excluding Channel 9, a total of 72 channel time-series signals were analyzed.

## Methodology

The analysis workflow includes:

1. Load HER2 FET CSV data
2. Exclude Channel 9
3. Segment the signal into PBS and HER2 concentration stages
4. Extract steady-state responses
5. Compute physical features, including:
   - final drop
   - PBS noise
   - signal-to-noise ratio
   - slope
   - monotonicity score
   - linear R²
   - logarithmic R²
6. Generate physics-guided teacher pseudo-labels
7. Train a physics-guided 1-D CNN model
8. Compare Teacher labels and CNN predictions
9. Select consensus valid channels
10. Construct HER2 calibration curves

## Physics-Guided Teacher Rules

A channel is labeled as valid only if it satisfies all of the following conditions:

- final drop > 0.05
- final drop ≥ 3 × PBS standard deviation
- positive linear or logarithmic response slope
- monotonicity score ≥ 0.75
- linear R² or logarithmic R² ≥ 0.75

The teacher label is a physics-guided pseudo-label, not an independent expert ground truth.

## CNN Model

The 1-D CNN model uses both waveform inputs and physical features.

The waveform input consists of three channels:

- raw-normalized signal
- z-score normalized signal
- derivative signal

The physical feature branch includes manually extracted interpretable indicators such as final drop, SNR, R², monotonicity score, and PBS noise.

The model outputs an AI valid score for each sensing channel.

## Results

The main results are summarized as follows:

| Category | Number of Channels |
|---|---:|
| Total channels | 72 |
| Teacher valid channels | 11 |
| CNN valid channels | 12 |
| Consensus valid channels | 10 |
| AI rescue candidates | 2 |
| AI rejection candidates | 1 |

The CNN showed high consistency with the physics-guided teacher labels.

| Metric | Value |
|---|---:|
| Accuracy | 0.958 |
| Precision | 0.833 |
| Recall | 0.909 |
| F1-score | 0.870 |
| MCC | 0.846 |
| Cohen's kappa | 0.845 |
| ROC-AUC | 0.934 |
| PR-AUC | 0.848 |

The HER2 logarithmic calibration curve improved after consensus channel selection.

| Method | Channels | Linear R² | Log R² | Estimated LOD |
|---|---:|---:|---:|---:|
| All channels | 72 | 0.837 | 0.920 | 41.156 |
| Teacher valid | 11 | 0.925 | 0.958 | 11.588 |
| CNN valid | 12 | 0.886 | 0.910 | 18.177 |
| Consensus valid | 10 | 0.913 | 0.963 | 11.645 |

## Conclusion

The results show that the physics-guided 1-D CNN can assist in HER2 FET signal quality validation before calibration curve construction.  
The consensus mechanism between physical rules and CNN prediction helps remove low-response, noisy, and non-monotonic channels, improving the consistency and interpretability of the HER2 calibration curve.

However, this model is currently based on pseudo-labels, limited experimental data, and the same dataset used for signal screening and calibration evaluation. More independent experiments, blank repeated measurements, and expert-labeled data are required for future validation.

## Future Work

Future work will include:

- increasing independent experimental batches
- collecting more blank repeated measurements
- introducing expert-labeled ground truth
- validating the model on different chips and sensing conditions
- extending the workflow to other biomarkers and FET biosensor platforms

## Disclaimer

This project is intended for HER2 FET biosensor signal validation and calibration curve construction.  
It is not a clinical diagnostic system and should not be interpreted as a validated breast cancer metastasis prediction tool.
