# Paper 10 — Khan et al. (2026)

## Citation
Khan, K., Malik, L., Khan, A. Q., Abbasi, S. F., & Arvanitis, T. N. (2026). **Non invasive blood glucose estimation using green light photoplethysmography and machine learning.** *Frontiers in Digital Health*, 8, 1705086. https://doi.org/10.3389/fdgth.2026.1705086

- Full text (PMC): https://pmc.ncbi.nlm.nih.gov/articles/PMC12953498/
- PubMed: https://pubmed.ncbi.nlm.nih.gov/41783114/
- DOI: https://doi.org/10.3389/fdgth.2026.1705086
- PMID: 41783114
- PMCID: PMC12953498
- Published: 17 February 2026

## 1. Study overview

The study investigates non-invasive glucose estimation using green-light fingertip PPG. The authors develop a custom acquisition setup using a low-cost pulse sensor interfaced with an Arduino Uno, collect paired PPG and capillary glucose readings from 80 participants, engineer waveform features, and evaluate conventional regression models.

The study uses controlled measurements in fasting and post-glucose states, rather than free-living or continuous wearable data.

The best reported model is Random Forest Regression (RFR), with:
- **R² = 0.92**
- **MAE = 4.8 mg/dL**
- **RMSE = 6.1 mg/dL**
- **MSE = 37.2 (mg/dL)²**

The study is a useful signal-processing and feature-engineering case study. Its evaluation design—particularly the split unit and the timing of feature selection/scaling—requires careful audit.

## 2. Research objective

### Primary objective
Explore whether green-light PPG signals collected from the fingertip can be used to estimate capillary blood glucose levels using machine-learning regression.

### Contributions
1. Custom green-light PPG acquisition setup.
2. Paired PPG and glucometer readings in fasting and post-glucose states.
3. Signal filtering and pulse segmentation.
4. Statistical, waveform and derivative-based feature extraction.
5. Correlation-based feature selection and feature engineering.
6. Comparison of multiple regression models.
7. Analysis of prediction bias across glucose ranges.

## 3. Study task

### Task type
**Concurrent glucose estimation (regression).**

The model estimates a capillary glucose value associated with a PPG recording, rather than predicting a future glucose value at an explicit forecast horizon.

### Measurement context
- Fasting measurement following approximately 8 hours of fasting.
- Second measurement after a glucose drink.
- PPG and glucometer readings paired at both sessions.

This is not continuous glucose prediction, and the study does not demonstrate free-living monitoring or future forecasting.

## 4. Participants and cohort

- **80 participants**
- Age: **18–50 years**
- 45 participants aged 18–30 (reported 56.3%).
- 35 participants aged 31–50 (reported 43.8%).
- 42 male (reported 52%).
- 38 female (reported 47%).
- 70 healthy participants (87.5%).
- 10 participants with pre-existing conditions, including diabetes with controlled glucose or prediabetes (12.5%).
- All participants were Pakistani nationals.
- Cohort skin tones described as medium to dark brown, approximately Fitzpatrick types IV–V.
- 44 participants were students and 36 were professionals.

### Cohort limitations
- Relatively small cohort.
- Limited representation of diabetic participants.
- Narrower skin-tone range.
- Age limited to 18–50 years.
- Participants with very high glucose values were excluded/limited.
- No separate performance analysis for diabetes/pre-diabetes subgroups.

## 5. Data collection and acquisition protocol

### Acquisition environment
- University laboratory.
- Data collection conducted over approximately 6–7 months.
- Indoor setting with ambient light minimized.
- Room temperature approximately 22–24°C.
- Participants rested seated for 5 minutes before measurement.
- Recorded hand held at heart level.
- Hand supported on a stable shelf to reduce movement.
- Session length approximately 40–45 minutes per participant.

### Fasting protocol
- Approximately 8 hours fasting.
- Capillary glucose measured by finger-prick glucometer.
- Simultaneous 15-second PPG recording.

### Post-glucose protocol
- Participants consumed **15–20 g Glaxose-D Instant Energy Powder**.
- PPG and reference glucose measurements repeated approximately **15–20 minutes** after glucose intake.
- Diabetic participants were not included in this post-glucose phase to avoid unsafe glucose elevations.

### Record count
- 80 participants.
- Two PPG profiles per participant (fasting and post-glucose, with diabetic participants excluded from the post-glucose phase—this makes the actual per-person record structure important to verify).
- The authors report **160 PPG profiles and 160 glucose measurements**.
- A few fasting values outside a predefined inclusion range were excluded.
- Reported glucose range: **65–160 mg/dL**.
- Mean glucose: 93 ± 22 mg/dL fasting and 113 ± 30 mg/dL post-glucose.

### Important record-structure verification
The paper reports 160 records but also says diabetic participants were excluded from post-glucose measurements. This should be reconciled with the participant-level record counts and the stated total. The exact final number after exclusions and the number of paired observations from each person should be extracted from the data flow.

## 6. Hardware and PPG sensor

### Sensor
- Pulse Sensor SEN-11574 (SparkFun).
- Green LED nominally **565 nm**.
- Photodetector described as optimized for approximately 525 nm.
- APDS-9008 photodetector.
- MCP6001 operational amplifier.
- Onboard amplification.
- Analog bandpass filtering.
- USB-powered 5 V setup, reported current below 4 mA.

### Acquisition hardware
- Arduino Uno R3.
- 10-bit ADC.
- USB serial interface.
- PLX-DAQ Excel interface for logging.

### Signal duration and sampling
- **15-second continuous PPG recording per session**.
- Sampling reported at approximately **127 Hz**.

### Hardware caveat
The sensor module's onboard analog filtering is described as 0.5–3 Hz in the hardware table, while the software preprocessing later uses 0.5–5 Hz. This distinction should be noted when reproducing the system.

## 7. Signal preprocessing

### Detrending
The raw PPG signal is detrended to remove baseline drift and low-frequency variation.

### Digital filtering
- Second-order Butterworth bandpass filter.
- High-pass cutoff: **0.5 Hz**.
- Low-pass cutoff: **5 Hz**.
- Sampling rate: approximately 127 Hz.

The authors chose this passband based on the signal spectrogram and the physiological cardiac signal range.

### Reported signal quality
The paper reports:
- SNR before filtering: **9.37 dB**.
- SNR after filtering: **20.34 dB**.
- Reported improvement: **10.97 dB**.

The SNR calculation defines signal power as spectral energy in the 0.5–5 Hz band and noise as remaining spectral components.

### Audit questions
- Is the digital filter applied to each 15-second recording independently?
- Is `filtfilt` used, and if so, how are edge effects handled?
- How are artifacts identified?
- Are all recordings processed identically?
- Does any signal processing use information beyond the target time?

## 8. Pulse segmentation

An enhanced moving-window approach is used to identify systolic peaks and diastolic valleys.

- Window size informed by resting HR of approximately 60–100 BPM.
- Valley points used to delimit pulse intervals.
- Individual pulses extracted.
- **Three consecutive pulses** selected for detailed feature analysis.

This means each 15-second recording is reduced to a small set of pulse segments for subsequent analysis.

### Reproducibility questions
- How are the three pulses selected?
- Are they the first three detected pulses, a fixed subset, or selected for quality?
- Is selection deterministic?
- Are pulses averaged within a recording?
- Does the method select pulses based on quality or glucose-related information?

## 9. Feature extraction

Features are computed from:
- Original PPG.
- First derivative: VPG (velocity photoplethysmogram).
- Second derivative: APG (acceleration photoplethysmogram).

### 9.1 Statistical features
Reported statistical features include:
- Minimum.
- Maximum.
- Root mean square (RMS).
- Signal energy.
- Mean.
- Standard deviation.
- Variance.

### 9.2 Waveform features
- Systolic peak.
- Diastolic peak.
- Inter-peak interval.
- Pulse width.
- Pulse rate.
- Trough interval.

### 9.3 APG features
Ratios associated with APG waves:
- b/a
- c/a
- d/a
- e/a
- (b-c-d-e)/a
- (b-e)/a
- (b-c-d)/a
- (c+d-b)/a

The a, b, c, d, and e APG components are extracted from three consecutive waveforms using MATLAB's `findpeaks`, and averaged for further analysis.

## 10. Feature preprocessing and selection

### Outlier removal
- Outliers identified and removed through visualization using scatter plots.

**Audit question:** were these decisions made before or after the train/test partition, and did test-set observations influence outlier removal?

### Scaling
- Min-Max scaling.

### Feature selection
- Pearson correlation analysis.
- Features with lower correlations are removed based on the heatmap/relationship to glucose.
- The paper identifies features including systolic peak, diastolic peak, maximum, pulse rate, trough interval, mean, RMS, standard deviation, variance and signal energy.

### Feature engineering
The paper combines metrics across PPG, VPG and APG:
- Averaged maximum.
- Averaged mean.
- Averaged SD.
- Averaged variance.
- Systolic-to-diastolic peak ratio.
- SD-to-mean difference.

### Critical audit
The paper describes feature preprocessing, scaling, correlation-based feature selection and feature engineering before training. The precise data flow relative to the train/test partition is crucial:

- Were Min-Max scaling parameters fitted on the training set only?
- Was Pearson feature selection calculated on the training data only?
- Was feature engineering or feature selection performed using the full dataset?
- Was outlier removal performed before the split?
- Were feature-selection decisions made independently for each fold or split?

These procedures should be training-only to avoid test-data leakage.

## 11. Regression models

The authors compare:
- Decision Tree.
- XGBoost.
- K-Nearest Neighbor (KNN).
- Random Forest Regression (RFR).
- Support Vector Machine (SVM).
- Linear Regression is also discussed in the results.

### Best model
**Random Forest Regression (RFR)**.

Reported configuration:
- `n_estimators = 100`
- `max_depth = 10`
- `min_samples_split = 2`
- `min_samples_leaf = 1`
- `max_features = "sqrt"`
- `bootstrap = True`
- `random_state = 42`

### Implementation
- Python.
- Google Colab.
- scikit-learn 1.0.2.
- XGBoost 1.6.0.

The paper reports that Linear Regression performed poorly, while XGBoost had acceptable performance. RFR achieved the best performance.

## 12. Evaluation protocol

### Reported split
- **50/50 train/test split**.
- 160 samples reported.
- 80 records for training.
- 80 records for testing.
- Random sampling.
- No separate validation set.
- The authors state the test set was kept independent.

### Critical participant-independence question

Each participant reportedly contributed two recordings (fasting and post-glucose). A random split of records does not necessarily ensure that both recordings from a participant are assigned to the same partition.

The paper describes the test set as independent and states the model performed on subjects not included in training. **The exact split implementation and participant grouping should be verified**, because these claims are only compatible if participants were grouped during splitting or if the final record structure otherwise avoids overlap.

### Audit questions
1. Was the random split performed at the record level or participant level?
2. Were the two recordings from each participant kept in the same partition?
3. How many unique participants were in the train and test partitions?
4. Were participants with pre-existing conditions represented in both?
5. Was feature selection performed before the split?
6. Were scaling parameters learned using all records?
7. Was outlier removal performed using all records?
8. Were hyperparameters selected based on test performance?
9. Is the reported result based on one split or repeated random splits?
10. Was any model selection or tuning performed without a validation set?

## 13. Reported results

### Best model: Random Forest Regression

| Metric | Reported value |
|---|---:|
| R² | 0.92 |
| MAE | 4.8 mg/dL |
| RMSE | 6.1 mg/dL |
| MSE | 37.2 (mg/dL)² |

### Glucose-range bias

The authors report results across three glucose categories:

| Glucose range | Test samples | Mean predicted | Mean actual | Mean difference |
|---|---:|---:|---:|---:|
| <90 mg/dL | 24 | 75.62 | 80.73 | −5.11 |
| 90–120 mg/dL | 30 | 103.42 | 107.10 | −3.68 |
| ≥120 mg/dL | 26 | 127.86 | 133.47 | −5.61 |

The mean differences are reported as:
- Below 90 mg/dL: −5.11 ± 0.80 mg/dL.
- 90–120 mg/dL: −3.68 ± 1.24 mg/dL.
- ≥120 mg/dL: −5.61 ± 0.75 mg/dL.

### Important statistical interpretation
The reported SDs of the differences (0.80, 1.24, 0.75 mg/dL) appear unusually small relative to the overall RMSE of 6.1 mg/dL and the small test sample size. The exact computation of these values and the statistical unit should be verified from the tables and code if available. Do not assume they represent the standard deviation of all individual prediction errors in each bin without checking.

### Bland–Altman
The paper reports a Bland–Altman analysis with:
- Mean bias approaching zero overall.
- Most points within the 95% agreement range.
- A slight negative trend at higher glucose levels (approximately 120–140 mg/dL).

### Error grid
- The authors state that future studies will incorporate Clarke or Parkes error-grid analysis.
- Error-grid analysis was **not reported as a current evaluation metric** in this study.

## 14. Methodological audit

These are audit hypotheses/questions—not confirmed defects.

### A. Potential participant leakage
- 80 participants.
- Two recordings per participant.
- 160 recordings reported.
- Random 50/50 sample split.

If random splitting is record-level, a participant's fasting recording could be in training and their post-glucose recording in test. The model could exploit person-specific physiological characteristics or acquisition traits.

**Benchmark experiment:** compare random record split with participant-grouped split.

### B. Split-unit ambiguity
The paper's assertion that the test data are independent and its description of a random 50/50 split should be reconciled with the paired-record structure.

**Benchmark action:** explicitly reconstruct split IDs and verify unique participants in both partitions.

### C. Feature-selection leakage
- Pearson correlation-based feature selection.
- Min-Max scaling.
- Outlier removal.
- Feature engineering.

The timing of these steps relative to the train/test split is not fully established from the description.

**Benchmark experiment:** implement all data-dependent preprocessing and feature selection inside training partitions.

### D. Small cohort and effective sample size
- 80 people.
- 160 reported measurements.
- Some participants may have contributed only fasting data.

The independent unit is the participant, not the PPG pulse or measurement record.

**Benchmark action:** report participant-level errors and participant-aware confidence intervals.

### E. Narrow glucose range
- Reported range: 65–160 mg/dL.
- Limited high-glucose observations.
- Few values outside typical ranges.
- Diabetic participants limited in the post-glucose protocol.

The study does not establish accuracy in severe hyperglycemia or hypoglycemia.

**Benchmark action:** stratify by glucose range and disclose coverage.

### F. Controlled stationary conditions
- Stable sitting.
- Arm supported.
- Ambient light minimized.
- Room temperature controlled.
- Short 15-second PPG.

The study does not demonstrate performance during motion, exercise, changing temperature, stress, or free-living conditions.

**Benchmark action:** classify this as controlled short-duration measurement, not continuous wearable monitoring.

### G. Skin-tone representation
- Cohort predominantly medium/dark brown skin tones (Fitzpatrick IV–V).
- Limited diversity in pigmentation.

Although this provides some coverage of darker skin phenotypes, the narrow cohort range does not establish performance across all skin tones.

**Benchmark action:** skin-tone subgroup analysis and external validation across phenotype distributions.

### H. Reference and target alignment
- Capillary glucometer reference.
- 15-second PPG recording.
- Pre- and post-glucose measurements.

Verify synchronization, whether the glucose reading precedes/follows the PPG recording, and whether the reference is truly simultaneous.

### I. No dedicated validation set
- One reported train/test partition.
- No separate validation set.
- Hyperparameter/model selection details need verification.

**Benchmark action:** use nested CV or a train/validation/test protocol for model selection, with participant grouping.

### J. Metrics and clinical interpretation
- RMSE, MAE and R².
- Bias analysis.
- No Clarke/Parkes error-grid analysis reported.

Numerical accuracy on a restricted glucose range does not establish clinical utility.

**Benchmark action:** add range-stratified analysis, error grid where appropriate, uncertainty, and participant-level evaluation.

## 15. Reproduction checklist

- [ ] Verify the final number of paired observations and records per participant.
- [ ] Extract full participant demographics and health-status distribution.
- [ ] Extract all glucose values and exclusions.
- [ ] Confirm the hardware and analog filter details.
- [ ] Confirm the actual sampling rate and 15-second recording length.
- [ ] Reproduce detrending and 0.5–5 Hz Butterworth filter.
- [ ] Reproduce pulse segmentation and three-pulse selection.
- [ ] Reproduce PPG/VPG/APG features.
- [ ] Reproduce Pearson feature selection.
- [ ] Reproduce Min-Max scaling and feature engineering.
- [ ] Determine whether feature selection/scaling/outlier removal was train-only.
- [ ] Verify random train/test split unit.
- [ ] Verify participant overlap between train and test.
- [ ] Reproduce the RFR configuration.
- [ ] Extract complete results for all models.
- [ ] Recalculate metrics independently from prediction vectors.
- [ ] Verify glucose-range bias and Bland–Altman calculations.
- [ ] Compare random record split and participant-grouped split.
- [ ] Report participant-level metrics and uncertainty.

## 16. Relevance to the wearable-glucose benchmark

| Dimension | Paper characteristics | Benchmark relevance |
|---|---|---|
| Task | Concurrent regression | Separate from forecasting |
| Hardware | Pulse Sensor SEN-11574 | Low-cost custom/commodity sensor |
| Wavelength | Green LED, nominal 565 nm | Single-wavelength PPG |
| Acquisition | Fingertip | Not wrist/free-living |
| Sampling | Approximately 127 Hz | Short waveform analysis |
| Duration | 15 seconds | Very short measurement |
| Cohort | 80 participants | Small cohort |
| Record structure | Fasting + post-glucose, 160 reported | Repeated-record dependence |
| Reference | Capillary glucometer | Point reference |
| Glucose range | 65–160 mg/dL | Restricted coverage |
| Features | PPG/VPG/APG, statistical and waveform features | Signal feature benchmark |
| Feature selection | Pearson correlation | Selection leakage audit |
| Models | DT, XGBoost, KNN, RF, SVM | Classical ML comparison |
| Best model | RF | Simple ensemble baseline |
| Split | Random 50/50, 80 train/80 test | Split-unit ambiguity |
| Metrics | R² 0.92; MAE 4.8; RMSE 6.1 | Reported strong internal performance |
| Validation | No separate validation set | Model-selection audit |
| External validation | Not reported | Generalization remains untested |
| Environment | Controlled indoor/stationary | Not free-living wearable evidence |

## 17. Suggested interpretation for our research paper

This study is useful as a **controlled green-PPG feature-engineering benchmark**, particularly for pulse segmentation, PPG/VPG/APG features, correlation-based feature selection and conventional regression models.

The reported performance should not be interpreted as unseen-participant generalization until the train/test split is verified. The paper's paired fasting and post-glucose measurements create a particularly important evaluation-design question: whether records from the same participant are separated between train and test.

Its short recordings, controlled setting, narrow glucose range and lack of external validation also limit claims about continuous or free-living wearable monitoring.

The strongest benchmark experiments inspired by this paper are:
1. Reproduce the random record-level split, if confirmed.
2. Compare against participant-grouped evaluation.
3. Perform fold-local feature selection and scaling.
4. Evaluate PPG-only and engineered-feature variants.
5. Report participant-level RMSE/MAE and uncertainty.
6. Stratify by glucose range.
7. Treat controlled measurement and free-living monitoring as different settings.

## 18. Source

- Full text: https://pmc.ncbi.nlm.nih.gov/articles/PMC12953498/
- PubMed: https://pubmed.ncbi.nlm.nih.gov/41783114/
- DOI: https://doi.org/10.3389/fdgth.2026.1705086
