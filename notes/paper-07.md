# Paper 07 — Satter et al. (2024)

## Citation
Satter, S., Turja, M. S., Kwon, T.-H., & Kim, K.-D. (2024). **EMD-Based Noninvasive Blood Glucose Estimation from PPG Signals Using Machine Learning Algorithms.** *Applied Sciences*, 14(4), 1406. https://doi.org/10.3390/app14041406

- Article: https://www.mdpi.com/2076-3417/14/4/1406
- DOI: https://doi.org/10.3390/app14041406
- Published: 8 February 2024

## 1. Overview

This study estimates blood glucose using wrist-oriented, multi-wavelength PPG and engineered features. It compares Random Forest (RF), XGBoost, LightGBM, and CatBoost, using conventional PPG waveform features, AC/DC ratios, wavelength ratio-of-ratios, and features derived from empirical mode decomposition (EMD).

The paper's main technical contribution is the addition of EMD-derived features to a multi-wavelength PPG feature set, with feature-importance-based selection. CatBoost is reported as the best-performing algorithm when the selected 50 features (20 conventional PPG features and 30 EMD-derived features) are used.

**This is a particularly relevant study for wearable PPG benchmark research**, but the dataset is privately collected, small at the participant level, and its evaluation split and feature-selection workflow need careful auditing.

## 2. Research question and contribution

The authors aim to estimate blood glucose from PPG without relying on external inputs such as BMI, age or SpO2.

Their main contributions:
1. Use wrist-oriented reflective PPG at three visible wavelengths (RGB).
2. Extract conventional waveform features and AC/DC ratios.
3. Apply empirical mode decomposition to each channel and extract time-/frequency-domain features from IMFs.
4. Combine conventional waveform and IMF features.
5. Compare RF, XGBoost, LightGBM and CatBoost.
6. Use feature importance to reduce feature dimensionality.

## 3. Hardware and sensor

### Sensor configuration
- Sensor system: TMD 3719-based system.
- Optical source: white LED containing three wavelengths:
  - Blue: 465 nm
  - Green: 525 nm
  - Red: 615 nm
- Photodetector.
- Reflective PPG acquisition, described as wrist-oriented.
- Sampling frequency: **24 Hz**.
- Simultaneous PPG recording and reference glucose measurement.
- PPG recording duration reported: **3 minutes**.

The study uses three optical channels. It is not necessarily equivalent to a standard commercial smartwatch's PPG configuration or signal pipeline.

## 4. Participants and dataset

- **34 participants**.
- The authors report a 50:50 male-to-female ratio.
- Reference glucose: CareSens II glucometer.
- Data collection was approved by Kookmin University IRB.
- The study created its own dataset.

### Dataset availability
The authors state that the dataset is not publicly available because further research is underway.

### Reproducibility implications
- The dataset cannot be independently downloaded and reproduced from the paper's data statement.
- Participant count is modest.
- Data distribution, participant-level sample counts, and glucose-range coverage should be extracted from Table 1 and the figures if reproducing the paper's demographics.
- The availability of only a small cohort means sample-level metrics should not be interpreted as population-level generalization.

## 5. Acquisition and segmentation

The PPG was recorded alongside reference glucose data.

### Segment construction
- Signals are segmented into **30-second windows**.
- At 24 Hz, each segment contains **720 samples per channel**.
- RGB signals are represented in a three-dimensional array.
- The authors describe truncating each participant's data to the size of the participant with the smallest data length to create a uniform array.
- Signals from participants are then combined into a standardized order.

### Important audit question
The article describes a 15% test set, but the precise unit of the split needs to be established. Does the test split contain held-out participants, held-out 30-second windows, or a mixture?

The reported segmentation and subsequent combining of subject data make split-unit documentation especially important.

## 6. Preprocessing

### Detrending
- Third-order polynomial detrending to remove baseline drift.

### Filtering
- Second-order low-pass Butterworth filter, cutoff **8 Hz**.
- Second-order high-pass Butterworth filter, cutoff **0.5 Hz**.

These filters are applied to reduce high-frequency noise and low-frequency fluctuations.

### Audit questions
- Is filtering applied independently to each segment or across a longer signal?
- Are segment boundaries handled consistently?
- Is any filtering operation dependent on data beyond the segment's target timestamp?
- Does preprocessing occur before or after the train/test partition?
- Are any preprocessing parameters learned from the entire dataset?

## 7. Feature engineering

### 7.1 Conventional waveform features

The study computes 15 general feature types per wavelength, with autoregressive coefficients contributing additional features:

Features described include:
- Zero-crossing rate (ZCR)
- Autocorrelation (ACR)
- Kurtosis
- Variance
- Mean power spectral density (PSD)
- Kaiser–Teager energy (KTE) statistics:
  - Kurtosis
  - Variance
  - Mean
  - Skewness
- Spectral analysis statistics:
  - Kurtosis
  - Skewness
- Wavelet-analysis mean
- Autoregressive coefficients (Yule–Walker, order 2)
- Skewness
- Sum of absolute differences (SAD)

The authors report **16 features per wavelength**, or 48 waveform features across RGB.

### 7.2 AC/DC features and wavelength ratios

Additional features:
- AC/DC for each of the three wavelengths: 3 features.
- R1, R2, R3: 3 ratio-of-ratios features.

Definitions:
- R1 = green AC/DC divided by red AC/DC.
- R2 = blue AC/DC divided by red AC/DC.
- R3 = green AC/DC divided by blue AC/DC.

Together, these bring the waveform-based feature set to **54 features**.

### 7.3 EMD decomposition

Each RGB signal segment undergoes empirical mode decomposition using the PyEMD library.

EMD decomposes a signal into intrinsic mode functions (IMFs) and a residual. The authors describe the IMFs as representing progressively lower-frequency oscillatory components.

- EMD is applied to each filtered RGB channel.
- The number of IMFs varies between signal segments and wavelengths.
- The authors use a maximum of seven IMFs.
- When fewer than seven IMFs are generated, feature vectors are zero-padded to align dimensions.

### 7.4 IMF-derived features

**20 features per IMF**, including:
- Mean
- Variance
- Standard deviation
- Peak-to-peak amplitude
- Skewness
- Kurtosis
- Dominant frequency
- Total power
- Mean amplitude envelope
- Mean instantaneous frequency
- Zero-crossing rate
- Number of extrema
- Mean PSD
- PSD variance
- Spectral centroid
- Spectral entropy
- Spectral flatness
- Peak-to-spectral-energy ratio
- Spectral band energy
- Spectral slope

With the maximum seven IMFs across three channels:
- 7 IMFs × 20 features × 3 wavelengths = **420 IMF-based features**.

### Feature selection
- RF: Gini-based importance.
- XGBoost and CatBoost: gain-based feature importance.
- LightGBM: split- and gain-based feature importance.

The final combined selected feature set consists of:
- Top 20 waveform-based features.
- Top 30 IMF-based features.
- **50 features total**.

## 8. Models

Four machine-learning regressors are compared:

| Model | Role |
|---|---|
| Random Forest (RF) | Ensemble tree baseline |
| XGBoost | Gradient-boosted trees |
| LightGBM | Gradient-boosted trees |
| CatBoost | Gradient-boosted trees; reported best |

The models are evaluated on different feature configurations:
1. 54 conventional waveform/ratio features.
2. Selected 20 conventional waveform features.
3. Combined top 20 waveform features + top 30 IMF features (50 total).

Hyperparameter adjustment is described, but the exact tuning search, tuning objective, and data partition used during tuning should be verified before reproduction.

## 9. Evaluation protocol

The paper states:
- **15% of the total data was reserved as test data.**
- **Leave-one-out cross-validation (LOOCV)** was used on the training data.
- Results in the reported tables are based on the test set.

### Major audit questions

#### Split unit
The article's description does not, in the text retrieved here, clearly establish whether the 15% test split is participant-level or segment-level.

If 30-second segments from the same person appear in both training and test data, the model may exploit person-specific signal characteristics and acquisition conditions.

#### Feature selection
Feature importance is used to select the top 20 waveform features and top 30 IMF features.

**Verify whether feature selection was performed within the training folds or using the complete dataset/test data.** Feature selection before split or using test labels/information would create optimistic evaluation.

#### LOOCV
- What is the unit left out in LOOCV: segment, measurement, or participant?
- Are multiple windows from a participant distributed across folds?
- Is LOOCV used for model tuning only or for final reported estimates as well?
- Are hyperparameters selected using an independent inner procedure?

#### Preprocessing
Confirm whether filters, EMD, feature extraction and any data-dependent preprocessing are executed independently and appropriately relative to the train/test split.

## 10. Reported results

### Best reported model/configuration
The paper's abstract reports the best result using **CatBoost with the top 50 features** (20 PPG waveform features + 30 IMF features):

- Pearson's r: **0.96**
- MSE: **0.08**
- R²: **0.92**
- MAE: **8.01**
- Reported p-values for models: **<0.001**

The paper's results section reports the following Pearson correlations for the selected 50-feature set:
- RF: 0.94
- XGBoost: 0.95
- LightGBM: 0.93
- CatBoost: 0.96

### Metric interpretation and units
The paper's abstract lists MSE of 0.08 and MAE of 8.01. These values appear to be on different numerical scales, so **the reported units and exact metric calculations should be verified directly from the tables** before attempting comparison or conversion. Do not assume the MSE is in glucose units or convert it without checking the table's units and metric conventions.

### Error grid
The authors report Clarke Error Grid Analysis:
- RF, XGBoost and LightGBM: most points in Zone A, with a small proportion in Zone B.
- CatBoost: authors state that all data points were in Zone A.

This is the paper's reported result, not independent clinical validation.

## 11. Methodological audit

These are potential audit issues, not assertions that the authors made errors.

### A. Participant leakage / sample-level split
- 34 participants, multiple segments per participant.
- The 15% test split's unit is not clearly established in the extracted methods.
- Segment-level random splitting could permit person-specific information to appear in both train and test.

**Benchmark experiment:** reproduce the paper's split if possible, then compare with participant-grouped evaluation (GroupKFold/LOSO).

### B. Feature selection and test-set leakage
- Top 20 waveform and top 30 IMF features are selected based on importance.
- Confirm whether feature selection was nested within the training data.
- If the test set informed the ranking or selection, reported performance may be optimistic.

**Benchmark experiment:** implement fold-local feature ranking and selection.

### C. Small cohort
- 34 participants.
- Sample count and independent participant count must be reported separately.
- A small cohort makes uncertainty and individual variability particularly important.

**Benchmark experiment:** report participant-level metrics and participant-aware bootstrap confidence intervals.

### D. 30-second window dependence
- Short windows are extracted from recordings.
- The degree of overlap between segments and their temporal proximity must be clarified.
- Even if windows do not overlap, adjacent segments may be highly correlated.

**Benchmark experiment:** evaluate random-window, participant-grouped and (where possible) chronological splits.

### E. Private dataset / reproducibility
- Data are not publicly available.
- Full reproduction of the reported results is consequently limited.

**Benchmark action:** label this study as non-public-data evidence and distinguish protocol reproducibility from dataset reproducibility.

### F. Limited external validation
- The article does not report an independently acquired external cohort in the extracted results.
- The error grid is evaluated on the study's test data, not an external clinical cohort.

**Benchmark experiment:** external validation should be considered only where a compatible independent dataset and reference protocol exist.

### G. Task definition and target timing
- Simultaneous PPG and glucometer measurements are described.
- The study appears to address concurrent estimation rather than explicit future forecasting, but the exact label alignment/window timestamp should be confirmed.

**Benchmark action:** clearly classify the task as concurrent estimation unless a future target horizon is explicitly demonstrated.

### H. Glucose range and statistical significance
- Correlation and p-values do not establish clinical accuracy.
- Performance can be strongly affected by the glucose range.
- The p-value for correlation is not a substitute for participant-independent performance evaluation.

**Benchmark action:** report MAE/RMSE, distributions, range coverage, participant-level results and clinically appropriate error-grid metrics.

### I. Error-grid interpretation
- All points in Zone A is a potentially favorable result.
- Error-grid outcomes are sensitive to reference glucose distribution, sample size and test independence.
- Zone A does not by itself establish sensor reliability or clinical utility.

**Benchmark action:** report error-grid results alongside numerical errors, range-stratified performance and independent validation.

## 12. Benchmark relevance

| Dimension | Paper characteristics | Relevance |
|---|---|---|
| Sensor | RGB reflective PPG, wrist-oriented | Strong conceptual relevance to wearable PPG |
| Hardware | TMD 3719-based custom system | Not necessarily comparable to commodity smartwatch sensors |
| Sampling rate | 24 Hz | Low-rate optical signal |
| Cohort | 34 participants | Modest cohort size |
| Reference | CareSens II glucometer | Concurrent reference measurement |
| Recording | 3-minute PPG recordings | Short, controlled recording context |
| Segmentation | 30-second windows | Window dependence/split-unit audit |
| Features | 54 waveform/ratio features | Useful traditional feature benchmark |
| EMD features | 420 candidate features | Useful signal decomposition experiment |
| Feature selection | Top 20 + top 30 | Feature selection leakage risk to audit |
| Models | RF, XGBoost, LightGBM, CatBoost | Classical ML comparison |
| Split | 15% test, LOOCV training | Split-unit and fold details require verification |
| Best model | CatBoost, selected 50 features | Useful reference result, not directly transferable |
| Metrics | r=0.96, MSE=0.08, R²=0.92, MAE=8.01 | Units/protocol need reconciliation |
| Data availability | Not public | Limits direct reproduction |
| External validation | Not identified in extracted text | Generalization remains uncertain |

## 13. Reproduction checklist

- [ ] Extract Table 1 demographics and glucose distribution.
- [ ] Identify participant-level sample counts and session structure.
- [ ] Obtain exact sensor configuration and data acquisition files if possible.
- [ ] Reconstruct 30-second segmentation at 24 Hz.
- [ ] Implement third-order polynomial detrending.
- [ ] Implement the 0.5–8 Hz Butterworth preprocessing (filter order and implementation details).
- [ ] Implement RGB waveform feature extraction.
- [ ] Implement AC/DC and R1/R2/R3 features.
- [ ] Implement PyEMD and the 20-feature-per-IMF pipeline.
- [ ] Confirm IMF count, zero-padding and feature dimensionality.
- [ ] Reproduce feature importance and selection.
- [ ] Confirm the 15% test split unit and LOOCV unit.
- [ ] Confirm hyperparameter search and fold-local preprocessing/selection.
- [ ] Extract all metric values and units from Tables 2–5.
- [ ] Reproduce error-grid proportions.
- [ ] Compare random segment split with participant-grouped evaluation.
- [ ] Report participant-level performance and uncertainty.

## 14. Recommended interpretation for our paper

This study is a useful **feature-engineering benchmark** for wrist-oriented PPG glucose estimation. It provides a substantial conventional and EMD-based feature pipeline, compares multiple tree-based regressors, and highlights the value of multi-wavelength signal representation.

However, its strong reported metrics should not be interpreted as evidence of unseen-person generalization until the test split unit, feature selection procedure and participant independence are established. Its private dataset also limits direct reproducibility.

The strongest contribution to our benchmark design is a controlled comparison of:
- Raw or minimally processed PPG features.
- Conventional engineered features.
- EMD features.
- Combined feature sets.
- Fold-local feature selection.
- Random-segment vs participant-independent evaluation.

## 15. Source

- Full text: https://www.mdpi.com/2076-3417/14/4/1406
- DOI: https://doi.org/10.3390/app14041406
