# Paper 05 — Chellamani et al. (2025)

## Bibliographic information

**Title:** A Deep Sparse Capsule Network for Non-Invasive Blood Glucose Level Estimation Using a PPG Sensor  
**Authors:** Narmatha Chellamani, Saleh Ali Albelwi, Manimurugan Shanmuganathan, Palanisamy Amirthalingam, Emad Muteb Alharbi, Hibah Qasem Salman Alatawi, Kousalya Prabahar, Jawhara Bader Aljabri, Anand Paul  
**Journal:** Sensors, 25(6), 1868 (2025)  
**Publication date:** March 18, 2025  
**DOI:** https://doi.org/10.3390/s25061868  
**Article:** https://pmc.ncbi.nlm.nih.gov/articles/PMC11945921/  
**Publisher:** https://www.mdpi.com/1424-8220/25/6/1868

---

## 1. Executive summary

This paper proposes a Deep Sparse Capsule Network (DSCNet) for non-invasive blood glucose level (BGL) estimation from photoplethysmography (PPG). The authors describe a PPG data-collection setup and a pipeline involving signal smoothing, feature extraction, and deep-learning prediction. They use Savitzky–Golay and moving-average filters for preprocessing and discuss autoregressive (AR), Kaiser–Teager energy (KTE), spectral entropy (SE), and related features. The study also introduces an enhanced DSCNet with self-attention.

The paper reports very strong numerical performance for its DSCNet model, including RMSE 0.062, MAE 0.050, MSE 0.058, MAPE 3.022, and R² 0.98 in its main test table. However, the article contains inconsistencies in units, sample descriptions, and comparative metrics that should be resolved from the original tables, data, and code before these numbers are treated as directly comparable glucose-estimation results.

**Benchmark relevance:** This paper is useful for studying PPG preprocessing, feature extraction, capsule architectures, attention, sample-level splits, and reporting consistency. It is also a case where unusually strong reported performance should motivate a careful audit of data splitting, label association, unit conventions, and independent sample counts—not an assumption that the result is invalid.

## 2. Objectives

The authors aim to:
1. Estimate glucose levels non-invasively from PPG signals.
2. Develop a Deep Sparse Capsule Network.
3. Improve PPG preprocessing using Savitzky–Golay and moving-average filters.
4. Extract signal features for prediction.
5. Add self-attention to improve the proposed network.
6. Compare the model with several other ML/DL approaches.
7. Implement a PPG-based measurement prototype.

## 3. Data and acquisition

### Dataset / acquisition described by the paper
- The article describes a PPG acquisition frequency of 115.2 Hz.
- Each segment is described as 1,700 samples.
- It describes two segments per participant (fasting and non-fasting).
- The data-collection algorithm describes 835 subjects.
- A glucose measurement is associated with a fasting and/or non-fasting session.
- The overall experiment is described as approximately 15 minutes, with PPG collection sessions of approximately 3 minutes.
- The protocol describes fasting measurement and a non-fasting measurement after breakfast; for some participants, a 75-g glucose drink is described.

### Glucose categories
The paper describes glucose categories in mmol/L:
| Category | Range (mmol/L) | Description |
|---|---:|---|
| C1 | 3.9–6.1 | Normal |
| C2 | 6.2–7.8 | Prediabetic |
| C3 | 7.9–11 | Diabetic |

**Audit note:** The article's narrative, algorithm, sample tables, and segment counts should be reconciled to establish the exact participant count, number of independent PPG sessions, and number of paired glucose labels. The paper describes both a sample size of 835 and a 1,700-sample segment representation; these terms should not be conflated.

## 4. Sensor and reference measurements

### PPG
- Photoplethysmography is the primary non-invasive input.
- Sampling frequency: 115.2 Hz (as described).
- The PPG sensor/hardware prototype is discussed, including a Raspberry Pi-based implementation.

### Reference BGL
- Fasting and non-fasting glucose reference measurements are associated with the PPG recordings.
- The paper describes categories and values in mmol/L.

The exact reference measurement instrument, sampling/measurement protocol, timing tolerance between PPG and reference measurement, and label aggregation should be verified for reproducibility.

## 5. Preprocessing

The authors identify noise and artifacts from:
- Motion
- Power-line interference
- Muscle artifacts
- High-frequency noise
- Low-amplitude signal
- Baseline wandering

### Filters
1. **Moving average filter:** used to smooth the signal and reduce baseline fluctuations/noise.
2. **Savitzky–Golay filter:** used to smooth PPG while preserving waveform morphology and higher-frequency characteristics.

### Reproduction details to extract
- Exact Savitzky–Golay polynomial order and window length.
- Exact moving-average window length.
- Filter order and whether filters are applied online or offline.
- Whether filtering is applied independently to each recording.
- Whether filtering occurs before or after segmentation.
- Whether filter boundary behavior is consistent across train/test.
- Whether any signal quality exclusion is applied.

The paper's description of filtering should be converted into executable, explicit preprocessing parameters before reproduction.

## 6. Feature extraction

The paper describes a feature vector based on PPG signal characteristics, including:
- Local/frame-level features.
- Global/window-level features.
- Statistical signal fluctuations.
- Autoregressive (AR) coefficients.
- Kaiser–Teager energy (KTE).
- Spectral entropy (SE).
- Additional extracted characteristics described in the full feature-extraction section.

The exact feature definitions and parameter settings should be extracted from the full text, equations, and code. Feature extraction is a data-dependent transformation and must be performed within a clearly defined training/test pipeline if any parameters are learned from data.

## 7. Model: Deep Sparse Capsule Network (DSCNet)

### Capsule network
The paper describes a capsule-based network that uses vector representations and dynamic routing to model feature relationships. Capsules represent features using vectors rather than relying solely on scalar activations, and the network uses capsule layers to encode relationships between features.

The architecture description includes:
- A convolutional feature extraction stage.
- ReLU/related activation.
- Primary capsule layer.
- Capsule transformation and routing.
- Higher-level capsules.
- Sparse capsule-network components.

### Enhanced model: DSCNet with self-attention
The authors add self-attention to the DSCNet architecture and report improvements over the baseline DSCNet across the metrics presented.

**Reproduction audit:** The architecture's exact layer dimensions, routing iterations, sparsity settings, attention implementation, loss function, optimizer, and training configuration should be verified from the code and full architecture details rather than inferred from the conceptual description.

## 8. Evaluation protocol

### Train/test partition
The article describes an 80:20 training/testing split in its data-collection algorithm.

### Protocol questions that require verification
1. Is the split performed at participant level, session level, or PPG segment level?
2. Are fasting and non-fasting sessions from the same participant allowed to occur in both train and test?
3. If there are multiple PPG windows per session, can related windows cross partitions?
4. Is the validation set independent of the test set, and how was model selection performed?
5. Were preprocessing or feature-selection parameters fit on training data only?
6. Does the test set represent unseen participants or merely held-out samples from the same participants?
7. Are the reported results calculated on the same target scale as the reference glucose values?

The 80:20 split alone does not establish participant-independent evaluation.

## 9. Metrics

The paper reports:
- MAE
- MSE
- RMSE
- MAPE
- MARD
- R²

The main reported DSCNet-with-self-attention test results include:
| Metric | Reported value |
|---|---:|
| RMSE | 0.062 |
| MAE | 0.050 |
| MSE | 0.058 |
| MAPE | 3.022 |
| R² | 0.98 |
| MARD | 10.81 (reported elsewhere in the article) |

**Important:** The paper mixes or appears to mix different numerical scales and units in its text and tables. The above values should not be converted into mg/dL or mmol/L or directly compared with the RMSE values from other papers until the output units, target scaling, and metric calculations are reconciled. The paper also reports training/test values and comparison-table values that need consistency checks.

## 10. Reported results

### Baseline DSCNet versus DSCNet with self-attention
The paper reports improvement after adding self-attention:
- RMSE: 0.085 to 0.062.
- MAE: 0.067 to 0.050.
- MSE: 0.072 to 0.058.
- MAPE: 4.50 to 3.22 in one comparison (the main reported result elsewhere is 3.022).
- R²: 0.95 to 0.98.

### Training and testing
The paper reports:
| Metric | Training | Testing |
|---|---:|---:|
| RMSE | 0.076 | 0.062 |
| MAE | 0.061 | 0.050 |
| MSE | 0.068 | 0.058 |
| MAPE | 4.18 | 3.022 |
| R² | 0.97 | 0.98 |

These values, scales, and the apparently better test than training performance warrant checking the underlying metric implementation, normalization, partitioning, and reporting conventions.

### Comparative models
The paper compares DSCNet with models such as DNN, 1D-CNN, XGBoost, CatBoost, and FGSVR and reports DSCNet as best in its comparison.

**Comparative-table audit:** Some values in the comparative discussion appear inconsistent in units or magnitude (for example, mixing very small normalized-scale values with much larger values). Comparisons should be reproduced from raw predictions on a common glucose scale before drawing conclusions.

## 11. Strengths

1. Explicitly addresses PPG noise and signal preprocessing.
2. Includes multiple signal-processing approaches.
3. Proposes capsule-network architecture.
4. Investigates self-attention.
5. Provides multiple metrics.
6. Includes a hardware-oriented acquisition/deployment concept.
7. Provides a test comparison against several model families.

## 12. Methodological audit hypotheses

These are questions to investigate, not confirmed flaws.

### 12.1 Participant-level splitting
An 80:20 split is insufficiently informative without knowing the split unit.

**Benchmark experiment:** Compare random segment/session splitting against grouped participant splitting.

### 12.2 Repeated measures
Fasting and non-fasting samples from a participant may share physiological and demographic characteristics.

**Benchmark experiment:** Keep all recordings from one participant in a single test partition for subject-independent evaluation.

### 12.3 Small effective sample size
The number of segments/windows may be much larger than the number of independent participants.

**Benchmark experiment:** Report participant-level metrics and bootstrap at the participant level.

### 12.4 Label timing
Verify the exact time difference between PPG recording and glucose reference measurement and whether PPG segments are aligned to a label obtained during or after recording.

**Benchmark experiment:** Explicitly vary alignment/tolerance and document the measurement protocol.

### 12.5 Scale and units
The paper reports values such as RMSE 0.062 and MAE 0.050, but the article also discusses glucose in mmol/L and uses inconsistent-looking comparative numbers.

**Benchmark experiment:** Recalculate all metrics from raw predictions and labels in mg/dL and mmol/L; document the scale used for training and evaluation.

### 12.6 Reporting consistency
Reported MAPE values differ slightly across parts of the article, and comparative table values appear to use different scales.

**Benchmark experiment:** Reproduce metrics from code, check equations and units, and publish a normalized-to-original-scale conversion pipeline.

### 12.7 Model complexity and baselines
A capsule network with attention is a complex model.

**Benchmark experiment:** Compare it under the same split and preprocessing against simple baselines (mean, linear regression, Ridge, RF, XGBoost) and a standard CNN.

### 12.8 Clinical claims
High R² or small scaled RMSE values do not establish clinical accuracy. Clinical grid, error distribution, and original-scale performance need verification and independent validation.

## 13. Relevance to wearable-glucose-benchmark

This paper provides a PPG-only model/processing case study and is useful for:
- PPG preprocessing and filtering.
- Feature engineering.
- Capsule networks and attention.
- Sample versus participant split.
- Repeated fasting/non-fasting measurements.
- Metric scaling and reporting reproducibility.
- Comparing complex architecture against simpler baselines.

It is not a direct multimodal wrist-wearable study like Papers 01 and 02, but it can inform the methodological benchmark's model and evaluation audit.

## 14. Proposed benchmark experiments inspired by this paper

- **B5-E1:** Reproduce preprocessing (Savitzky–Golay and moving average) with exact settings.
- **B5-E2:** Reproduce the PPG feature set.
- **B5-E3:** Compare DSCNet and DSCNet with self-attention.
- **B5-E4:** Compare participant-grouped and random sample splits.
- **B5-E5:** Keep all sessions from one participant in a single partition.
- **B5-E6:** Compare DSCNet against simple and conventional ML baselines.
- **B5-E7:** Recalculate metrics on the original glucose scale.
- **B5-E8:** Audit unit consistency and target normalization.
- **B5-E9:** Report participant-level uncertainty and performance distributions.
- **B5-E10:** Test label timing and recording-to-reference alignment.

## 15. Reproducibility checklist

- [ ] Obtain the exact PPG dataset and participant/session identifiers.
- [ ] Verify the participant count and number of independent glucose labels.
- [ ] Verify the PPG acquisition frequency (115.2 Hz).
- [ ] Reconstruct the 1,700-point segment and recording duration.
- [ ] Verify fasting/non-fasting acquisition and label timing.
- [ ] Reproduce Savitzky–Golay filter settings.
- [ ] Reproduce moving-average filter settings.
- [ ] Reproduce feature definitions (AR, KTE, SE, etc.).
- [ ] Reconstruct DSCNet.
- [ ] Reconstruct self-attention variant.
- [ ] Verify the split unit and 80:20 split.
- [ ] Ensure participant/session identities do not cross grouped partitions.
- [ ] Recalculate all metrics on original glucose units.
- [ ] Reconcile reported metric discrepancies.
- [ ] Compare with transparent baseline models.
- [ ] Report participant-level results.

## 16. Research assessment

**Role in the benchmark:** A useful architecture-focused PPG case study that warrants careful data-split and metric-reproducibility auditing.

The paper's reported performance is unusually strong and the reported values appear to use different numerical scales in parts of the article. Rather than treating this as evidence of a flaw, the benchmark should make the evaluation protocol, split unit, original target scale, metric implementation, and participant-level uncertainty explicit. These checks could make this paper a valuable example of why reproducible evaluation requires more than reporting a single high-performing model.

## 17. References

- PMC full text: https://pmc.ncbi.nlm.nih.gov/articles/PMC11945921/
- DOI: https://doi.org/10.3390/s25061868
- Publisher article: https://www.mdpi.com/1424-8220/25/6/1868
