# Solar-Storm-Sentinel
Detection of Halo Coronal Mass Ejections Using LSTM Neural Networks

This project focuses on forecasting solar-wind–related ASPEX instrument parameters and detecting possible CME (Coronal Mass Ejection) disturbances using threshold-based anomaly logic. Data are from ASPEX CDF files, each containing multiple engineering and detector variables. Due to the fact that there are no officially labeled CMEs in the dataset, the model works by forecasting normal behavior and flagging unusually high variations as potential CME-related anomalies.

1. Overview

The model uses multivariate ASPEX time-series data as input and provides forecasts of future values for the same set of features. These forecasts allow the identification of abnormal spikes or disturbances. Since the ground truth labels are not available in the dataset, threshold-based flags have been used to perform CME detection.

The pipeline uses:
- Loading of CDF files
- Cleaning fill values: ASPEX uses -1e31.
- The selection of useful features
- Scaling and windowing
- Training an LSTM forecasting model
- Threshold-based anomaly detection


2. Features Used

The ASPEX instrument variables used as input features for training are the following:
- trig_counts
- coin_trig_counts
- peak_det_counts
- rej_counts
- fpga_temp_mon
- ESA1_HV_set
- ESA2_HV_set
- spacecraft_xvel

The eight features were selected as they show clear variations during disturbances and are available across files.


3. Data Preprocessing

- The preprocessing pipeline does the following:
- Load multiple ASPEX CDF files.
- Extract the required features.
- Remove fill values (any value equal to -1.0e31).
- Trim all features to the same length to align them.
- Normalize them using MinMaxScaler in the range from 0 to 1.
- Create sliding input sequences of length 50 time steps.
- The target for forecasting is the next time step for one selected feature like trig_counts.

After processing, the final shapes are:
- Input (X): number_of_sequences, 50 time steps, 8 features
- Output (y): number_of_sequences


4. Model Description

The forecasting model is a neural network composed of LSTM layers.

Input:
A sequence of 50 time steps, each with 8 features.

Output:
A single value that represents the next time step of the main forecasting feature. For example, the next trig_counts value.
The model learns the normal temporal pattern of ASPEX data. Once trained, its predictions help identify periods where the observed values strongly deviate from expected behavior.


5. CME Detection

Because CME event labels are not available in the dataset, the detection of CMEs relies on threshold-based rules. Typical thresholds are computed using percentiles. 
For example:
- trig_counts over the 90th percentile
- coin_trig_counts above the 90th percentile
- fpga_temp_mon above the 85th percentile

If any of these conditions are met, the sample is marked as a potential CME disturbance. The labels are smoothed using a rolling window so that small spikes do not trigger false detections. After smoothing, the flagged time intervals correspond to possible CME event windows.


6. Overview of Workflow

- Load ASPEX CDF files.
- Extract the required features.
- Clean and trim feature arrays.
- Normalize using MinMaxScaler.
- Generate 50-step input sequences for LSTM.
- Train the LSTM model to predict the next value.
- Employ thresholds to detect CME-like anomalies.
- Visualize predicted vs actual values and anomaly windows.


7. Output Provided

The project produces:
- The scaled multivariate training data
- LSTM model training results
- Predictions for each feature 
- Threshold-based anomaly or CME detection results 
- Plots of anomaly intervals and model forecasts 
