# Notebook

The full Google Colab notebook is provided in this folder.

If GitHub cannot preview the `.ipynb` file due to large outputs, please download the raw notebook file and open it with Google Colab or Jupyter Notebook.

Main workflow:
1. Load 9 HER2 FET CSV experiments
2. Exclude Channel 9 reference channel
3. Segment PBS and HER2 concentration stages
4. Extract steady-state signal features
5. Generate physics-guided teacher labels
6. Train dual-input 1-D CNN
7. Evaluate by Leave-One-Experiment-Out validation
8. Compare HER2 calibration curves before and after signal validation
