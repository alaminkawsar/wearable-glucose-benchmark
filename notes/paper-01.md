# Paper 01 — Research Note

## Development of Non-Invasive Continuous Glucose Prediction Models Using Multi-Modal Wearable Sensors in Free-Living Conditions (2025)

> Detailed reading and methodological audit note for `wearable-glucose-benchmark`. Critiques below are hypotheses to verify, not conclusions.

## 1. Bibliographic information

| Field | Details |
|---|---|
| Title | Development of Non-Invasive Continuous Glucose Prediction Models Using Multi-Modal Wearable Sensors in Free-Living Conditions |
| Authors | Thilini S. Karunarathna and Zilu Liang |
| Publication | *Sensors*, 25(10), 3207 |
| Date | 20 May 2025 |
| DOI | https://doi.org/10.3390/s25103207 |
| PubMed | https://pubmed.ncbi.nlm.nih.gov/40431999/ |
| Full text | https://pmc.ncbi.nlm.nih.gov/articles/PMC12115526/ |
| Dataset | BIG IDEAs Lab Glycemic Variability and Wearable Device Data |
| Dataset portal | https://physionet.org/content/big-ideas-glycemic-wearable/1.1.2/ |
| Task | CGM glucose estimation/regression using wearable and demographic features |
| Best reported model | XGBoost regression |

## 2. Executive summary

The study explores continuous glucose estimation from passively collected multimodal wearable signals in free-living conditions. It uses the BIG IDEAs dataset, combining CGM readings, Empatica E4 wristband signals, and demographic information.

The authors engineer 236 features from physiological, behavioral, circadian, and demographic information. A Random-Forest-based feature-importance procedure selects 30 features. Four regressors are evaluated: Linear Regression, Ridge Regression, Random Forest Regression, and XGBoost.

XGBoost performs best in the reported experiment, with R² 0.73, RMSE 11.9 mg/dL, NRMSE 0.52, MARD 7.1%, and 99.4% of predictions in Clarke Error Grid Zones A+B.

**Research opportunity:** investigate how performance changes under participant-independent and chronological evaluation, and compare the models with simple population and personal baselines. Verify the reported split and participant overlap before drawing conclusions about leakage.

## 3. Problem and motivation

The authors seek to estimate glucose without requiring manual logging of food, activity, or sleep. Wearables passively collect signals such as cardiovascular, electrodermal, temperature, and movement-related information.

The practical motivation is that physiological state, behavior, and daily rhythms may contain information correlated with glucose dynamics.

**Important distinction:** the wrist wearable is not directly measuring glucose. It estimates glucose using indirect physiological and behavioral correlates.

### Task definition to clarify
- **Concurrent estimation:** estimate glucose corresponding to the wearable-input time.
- **Forecasting:** predict a future glucose value.
- **Personalized estimation:** estimate for an individual using calibration data.

Do not assume this is future forecasting unless the target and feature timestamps establish that.

## 4. Dataset

The study uses the BIG IDEAs Lab Glycemic Variability and Wearable Device Data dataset.

- Approximately 26,000 CGM measurements.
- 16 participants, approximately 35–65 years old.
- Participants had high-normal or prediabetic HbA1c levels.
- Data collection lasted approximately 8–10 days.
- Dexcom G6 CGM recorded glucose approximately every 5 minutes.
- Empatica E4 wristband recorded wearable signals.
- Data were collected in free-living/minimally controlled conditions, with standardized breakfast on some days.

### Sensors and sources

| Source | Signal | Sampling/detail | Potential information |
|---|---|---|---|
| Empatica E4 | BVP/PPG | 64 Hz | Cardiovascular dynamics |
| Empatica E4 | HR | 1 Hz | Cardiac state |
| Empatica E4 | EDA | 4 Hz | Electrodermal/autonomic activity |
| Empatica E4 | Skin temperature | 4 Hz | Peripheral temperature |
| Empatica E4 | ACC | 32 Hz, 3 axes | Movement/activity |
| Dexcom G6 | Interstitial glucose | About every 5 min | Reference/target |
| Participant data | Demographics | Includes biological sex | Population differences |

**Population limitation:** the cohort is not representative of all people with diabetes or the full range of glucose dynamics. The authors note limited representation of extreme glycemic conditions.

Dataset: https://physionet.org/content/big-ideas-glycemic-wearable/1.1.2/

## 5. Methodology: pipeline

### Step 1 — Data preparation and synchronization
The authors load CGM data, wearable recordings, and timestamps. Missing timestamps and duplicate timestamps are handled. Accelerometer axes are combined into a vector-magnitude representation.

**Verify:** exact timestamp alignment, whether epochs precede or overlap target timestamps, missing/duplicate handling, and whether features contain any information after the target time.

### Step 2 — Filtering and outlier handling

| Signal | Filtering | Plausibility range reported |
|---|---|---|
| BVP | Fourth-order bandpass, 0.5–5 Hz | −500 to 500 (a.u.) |
| ACC | Low-pass, 10 Hz | 0 to 68 m/s² |
| EDA | Low-pass, 0.5 Hz | 0.01 to 100 µS |
| HR | — | 25 to 240 bpm |
| Skin temperature | — | 30 to 40 °C |

Values outside stated ranges are treated as invalid/missing.

### Step 3 — Segmentation
The signals are synchronized and segmented into **5-minute epochs preceding a glucose reading**.

Examples of samples per epoch:
- HR at 1 Hz: 300 samples.
- EDA at 4 Hz: 1,200 samples.
- ACC at 32 Hz: 9,600 samples.

Epochs with more than 50% missing values in at least one signal are discarded.

**Audit questions:** Are epochs overlapping? How are boundaries defined? Could windows cross train/test boundaries? Is all feature information available before the target?

### Step 4 — Missing-value handling
The authors describe epoch-level imputation:
- Mean when distribution is approximately normal (absolute skewness below 0.5).
- Median otherwise.

Feature cleaning also removes features with excessive missingness, overflow/constant values, duplicates, and low variance; rows with infinite values or excessive missing data are excluded.

**Audit:** verify that any learned preprocessing and cleaning decisions are performed within training folds. Assess whether epoch-level imputation is appropriate for the intended task.

### Step 5 — Feature engineering

The authors construct **236 features** across four groups.

#### A. Physiological features
- HR and skin temperature: 31 time-domain, 14 frequency-domain, and 42 nonlinear features per signal (87 per signal, as described).
- HRV: 13 features derived from BVP, including RMSSD, SDSD, and pNN20.
- EDA: 42 tonic/phasic features, including statistics and energy-type measures.

#### B. Behavioral features
Three ACC metrics over the preceding two-hour window:
- `ACC_2h_min`
- `ACC_2h_max`
- `ACC_2h_mean`

#### C. Circadian features
- Minutes from midnight.
- Sine transformation.
- Cosine transformation.

The sine/cosine representations encode time-of-day cyclically.

#### D. Demographic features
Biological sex is encoded as a binary feature in the described setup.

### Step 6 — Feature cleaning
The paper describes removing features with excessive missingness, overflow or constant values, duplicates, and low variance. Rows with infinite or excessive missing data are also excluded.

**Audit:** were cleaning decisions fitted only on training data?

### Step 7 — Feature selection
A Random-Forest-based importance procedure ranks the 236 features and selects the **top 30**.

Verify:
- Exact importance implementation.
- Selection within each training fold.
- Whether selection is repeated for each split/repetition.
- Whether test data are excluded from selection.

### Step 8 — Model training

| Model | Role |
|---|---|
| Linear Regression | Simple linear model |
| Ridge Regression | Regularized linear model |
| Random Forest Regression | Nonlinear tree ensemble |
| XGBoost Regression | Gradient-boosted trees |

Hyperparameter tuning and repeated evaluation are described. Inspect the precise splitting and tuning details rather than assuming subject-independent evaluation.

### Step 9 — Evaluation
The study uses:
- R²
- RMSE
- NRMSE
- MARD
- Clarke Error Grid
- Bland–Altman analysis
- SHAP analysis

## 6. Reported results

### Regression performance

| Model | R² | RMSE (mg/dL) | NRMSE | MARD (%) |
|---|---:|---:|---:|---:|
| Linear Regression | 0.13 ± 0.01 | 21.3 ± 0.3 | 0.93 ± 0.00 | 13.7 ± 0.1 |
| Ridge Regression | 0.13 ± 0.01 | 21.3 ± 0.3 | 0.93 ± 0.00 | 13.7 ± 0.1 |
| Random Forest | 0.53 ± 0.01 | 15.6 ± 0.3 | 0.68 ± 0.01 | 9.7 ± 0.1 |
| **XGBoost** | **0.73 ± 0.01** | **11.9 ± 0.3** | **0.52 ± 0.01** | **7.1 ± 0.1** |

These are reported values (Table 2); verify table details and uncertainty notation in the original publication before citing.

### Interpretation
- The linear models perform similarly.
- Random Forest improves over the linear models.
- XGBoost is the best of the four in the reported setup.
- XGBoost RMSE is reported as 11.9 mg/dL.

These results characterize the study's evaluation protocol; they do not establish performance on entirely unseen participants, future periods, or another dataset.

### Clarke Error Grid

| Model | Zone A | Zone B | Zone D | Zones A+B |
|---|---:|---:|---:|---:|
| LR | 77.0% | 22.2% | 0.8% | 99.2% |
| Ridge | 77.0% | 22.2% | 0.8% | 99.2% |
| Random Forest | 89.1% | 10.3% | 0.6% | 99.4% |
| **XGBoost** | **94.2%** | **5.2%** | **0.6%** | **99.4%** |

Error-grid results alone do not establish clinical readiness. Independent validation, population coverage, calibration, and task definition remain important.

### Feature importance and SHAP
The authors report biological sex, circadian features, behavioral features, and tonic EDA features as important. Tonic EDA features are prominent among the selected physiological features.

Feature importance and SHAP associations are not causal evidence; these signals may reflect physiological, behavioral, environmental, or population-level factors.

## 7. Critical methodological audit

These are **questions/hypotheses to verify**, not accusations or confirmed flaws.

### Concern 1 — Participant overlap and potential subject leakage

**Question:** Are epochs from the same participant present in both training and testing?

Wearable data may contain stable characteristics, including resting heart rate, skin conductance, temperature, activity habits, circadian routines, and individual glucose distributions. A random epoch-level split may put the same participant in both sets, potentially making the evaluation easier than testing entirely unseen individuals.

This does not necessarily imply direct glucose-label memorization; models may learn participant signatures or glucose distributions.

**Verify:**
- [ ] Unit of splitting: epoch, session, day, or participant?
- [ ] Is the split random?
- [ ] Are participants grouped?
- [ ] Are the same participants in train and test?
- [ ] Is participant ID a feature?
- [ ] Are there identity proxies?
- [ ] Is subject-independent evaluation reported?

**Proposed experiment:** random epoch-level evaluation versus GroupKFold and Leave-One-Subject-Out (LOSO).

### Concern 2 — Temporal context and target alignment

Do not claim that the study uses only 5-minute information: it also describes historical accelerometer features from the preceding two hours.

Investigate:
- [ ] Which features use a 5-minute epoch?
- [ ] Which features use historical context?
- [ ] Maximum lookback for each feature?
- [ ] Is information available before the target timestamp?
- [ ] Concurrent estimation or future forecasting?
- [ ] Overlapping windows?
- [ ] Could windows cross train/test boundaries?

**Proposed experiment:** short, medium, long, and multi-scale context under the same split protocol.

### Concern 3 — Baselines and personalization

The model comparison includes four ML regressors, but a benchmark should also compare:
- Population mean.
- Population median.
- Time-of-day baseline.
- Personal mean from calibration data.
- Personal median from calibration data.
- Linear Regression.
- Ridge Regression.
- Random Forest.
- XGBoost.
- Temporal model.

Personal baselines must use only training or permitted calibration data, never test labels.

### Concern 4 — Population generalizability

The dataset focuses on high-normal/prediabetic participants and has limited extreme glycemic conditions.

Investigate:
- [ ] Generalization to people with diabetes.
- [ ] Performance across glucose ranges.
- [ ] Hypoglycemic/hyperglycemic coverage.
- [ ] Variation across participants.
- [ ] Error concentration in particular ranges.

### Concern 5 — External validation

The study uses one dataset and notes the need for additional validation.

If a compatible dataset is available:
1. Train on Dataset A.
2. Evaluate rigorously within Dataset A.
3. Test on Dataset B without retraining.
4. Document sensor, population, sampling, and preprocessing differences.

Cross-dataset validation may be difficult due to incompatibilities.

### Concern 6 — Feature importance versus causality

The selected features may capture associations rather than direct physiological glucose signals.

Ablations:
- Demographics only.
- Circadian features only.
- Activity features only.
- EDA only.
- Cardiovascular features only.
- Physiological features.
- Physiological + behavioral features.
- Full feature set.

Ask whether feature-group contributions persist under subject-independent evaluation.

### Concern 7 — Estimation versus forecasting

Clarify:
- Does the model estimate glucose at the wearable-input time?
- Does it predict a future value?
- If future prediction, what is the horizon?
- Is historical CGM used?
- Is the model wearable-only?

These are distinct tasks.

## 8. Strengths

- Free-living data.
- Passive sensing without manual meal/activity logs as model inputs.
- Multimodal feature engineering.
- Comparison of multiple classical ML regressors.
- Feature importance and SHAP interpretation.
- Clinical error-grid analysis.
- Bland–Altman analysis.
- Public dataset for independent investigation.
- Practical wearable-device motivation.

## 9. Benchmark experiments

| Experiment | Protocol | Objective |
|---|---|---|
| E0 | Reproduce original random/epoch protocol as closely as possible | Reference performance |
| E1 | GroupKFold by participant | Held-out-participant generalization |
| E2 | LOSO | Generalization to each unseen participant |
| E3 | Chronological within-person split | Future-period generalization |
| E4 | Population and personal baselines | Test whether complex models add value |
| E5 | Multiple temporal contexts | Test effect of historical context |
| E6 | Modality/feature-group ablation | Incremental modality value |
| E7 | External dataset test, if compatible | Transfer/generalization |
| E8 | Per-participant and glucose-range analysis | Heterogeneity and error distribution |

### E0 — Reproduction
Reproduce the original protocol as precisely as possible. Document discrepancies. This is a reference point, not necessarily a realistic generalization test.

### E1 — GroupKFold
Keep each participant entirely in train or test within a fold. Preprocessing, imputation, feature selection, and tuning must be fitted using training data only.

### E2 — LOSO
Leave one participant out for testing and train using the remaining participants, repeating for all participants if feasible.

### E3 — Chronological within-person
Train on earlier periods and evaluate on later periods. Prevent future data from entering training or preprocessing.

### E4 — Baselines
Compare population mean/median, time-of-day, and personal calibration baselines. Define permitted calibration carefully.

### E5 — Temporal context
Compare context lengths using a consistent evaluation protocol.

### E6 — Modality ablation
Compare modality groups and combinations; fit feature selection and preprocessing within training folds.

### E7 — External validation
If a second dataset is sufficiently compatible, test without using external test data for model selection.

### E8 — Per-participant and glucose-range analysis
Report participant-level RMSE/MAE, participant distributions, error by glucose range, confidence intervals (preferably participant-resampled), and paired protocol comparisons.

## 10. Leakage-prevention rules

- [ ] Split data before fitting preprocessing or selecting features.
- [ ] Keep participants entirely separate in subject-independent evaluation.
- [ ] Keep chronological test periods after training periods.
- [ ] Prevent overlapping windows from crossing split boundaries.
- [ ] Fit normalization using training data only.
- [ ] Fit imputation using training data only.
- [ ] Fit feature selection using training data only.
- [ ] Tune using training/validation data only.
- [ ] Do not use test labels for personal baseline construction.
- [ ] Do not include future information in features.
- [ ] Document the split and unit of analysis.

## 11. Statistical analysis recommendations

Report:
- Overall RMSE and MAE.
- R², interpreted carefully.
- MARD, with limitations.
- Clinical error-grid results where appropriate.
- Participant-level metrics.
- Median and spread of participant performance.
- Confidence intervals.
- Paired protocol comparisons.
- Error by glucose range.

Avoid treating thousands of epochs from a small number of participants as thousands of independent people. Resample at the participant level where appropriate.

## 12. Expected contribution to `wearable-glucose-benchmark`

This paper could serve as a benchmark case study for:
1. Quantifying how performance changes with evaluation protocol.
2. Testing participant-independent generalization.
3. Testing chronological generalization.
4. Comparing complex models with simple baselines.
5. Studying temporal context.
6. Testing feature-group contributions.
7. Analyzing participant and glucose-range variation.
8. Assessing reproducibility.

### Central research question

> How much of the reported performance of multimodal wearable-based glucose estimation remains under evaluation on unseen individuals, future time periods, and against simple population and personalized baselines?

This is a stronger and more testable framing than presuming the paper is flawed.

## 13. Reading and verification checklist

### Paper reading
- [ ] Abstract and Introduction.
- [ ] Dataset and participants.
- [ ] Sensor specifications.
- [ ] Preprocessing and filtering.
- [ ] Epoch alignment and target definition.
- [ ] Feature engineering.
- [ ] Feature selection.
- [ ] Train/test split.
- [ ] Hyperparameter tuning.
- [ ] Metrics and results.
- [ ] Clarke Error Grid.
- [ ] Bland–Altman analysis.
- [ ] SHAP/feature importance.
- [ ] Discussion and limitations.

### Reproduction feasibility
- [ ] Download dataset.
- [ ] Review dataset documentation.
- [ ] Verify participant IDs and timestamps.
- [ ] Check code availability.
- [ ] Reproduce preprocessing.
- [ ] Reproduce feature construction.
- [ ] Reproduce feature selection.
- [ ] Reproduce baseline models.
- [ ] Reproduce original split.
- [ ] Implement subject-independent split.
- [ ] Implement chronological split.
- [ ] Implement baseline comparisons.
- [ ] Save results and configurations.

### Before moving to Paper 02
- [ ] Can I explain the pipeline without the paper?
- [ ] Do I understand what the model predicts?
- [ ] Have I verified the exact split?
- [ ] Have I verified participant overlap?
- [ ] Do I understand the temporal context?
- [ ] Have I separated reported facts from hypotheses?
- [ ] Have I recorded questions to investigate rather than treating them as proven flaws?

## 14. Research assessment

**Priority for `wearable-glucose-benchmark`: High.**

This paper is a strong first benchmark case study because it has multimodal wearable data, engineered features, feature selection, classical ML models, strong reported results, and an accessible dataset.

The central audit is not to assume leakage, but to verify participant overlap and quantify performance under alternative evaluation protocols.

**Most informative initial comparison:** original/reproduced evaluation → subject-independent evaluation → simple baseline comparison.

If feasible, add chronological within-person evaluation and modality ablation.

## 15. References

- Original paper: https://pmc.ncbi.nlm.nih.gov/articles/PMC12115526/
- Publisher: https://www.mdpi.com/1424-8220/25/10/3207
- DOI: https://doi.org/10.3390/s25103207
- PubMed: https://pubmed.ncbi.nlm.nih.gov/40431999/
- Dataset: https://physionet.org/content/big-ideas-glycemic-wearable/1.1.2/

> This is a working research note. Confirm exact methodology, table values, split implementation, and feature-selection behavior from the original paper, supplementary material, and code before using them as claims in a manuscript.
