# Paper 11 — Wang et al. (2025)

## Citation
Wang, M., Liu, X., Han, W., Lin, X., Chen, X., Zhao, S., Zhuang, Z., Zhang, L., & Su, P. (2025). **Design of a Wearable Finger PPG-Based Blood Glucose Monitor.** *Annals of Biomedical Engineering*, 53(10), 2580–2593. https://doi.org/10.1007/s10439-025-03809-9

- PubMed: https://pubmed.ncbi.nlm.nih.gov/40685463/
- DOI: https://doi.org/10.1007/s10439-025-03809-9
- PMID: 40685463
- Online publication: 20 July 2025
- Journal issue: October 2025, volume 53, issue 10, pages 2580–2593

> **Verification note:** This note is based on the PubMed abstract and bibliographic information retrieved in this session. The publisher's detailed full text and supplementary material were not independently extracted. Cohort size, data partitioning, preprocessing, detailed architecture and numerical regression metrics are therefore marked for verification. Do not treat unspecified details as established facts.

## 1. Overview

The authors design a wearable, non-invasive finger-based glucose-monitoring device using dual-wavelength photoplethysmography (PPG) and a multilayer perceptron (MLP).

The optical setup uses:
- Red visible light: **660 nm**
- Infrared light: **880 nm**

The device acquires finger PPG at both wavelengths. The authors train an MLP using dual-wavelength PPG intensity measurements associated with glucose levels and examine the effect of MLP training parameters on prediction performance.

They also report a **24-hour monitoring test** intended to demonstrate tracking of daily glucose fluctuations.

## 2. Research objective and contribution

### Objective
Develop and evaluate a wearable finger PPG-based non-invasive device for blood glucose level (BGL) monitoring.

### Contributions described in the abstract
1. Wearable finger-based glucose-monitoring hardware.
2. Dual-wavelength PPG acquisition at 660 nm and 880 nm.
3. MLP regression from dual-wavelength PPG intensity.
4. Analysis of MLP training-parameter effects.
5. Evaluation using Clarke Error Grid Analysis.
6. A 24-hour monitoring test.

### Scope
This is a custom finger-sensing system. The reported 24-hour monitoring experiment may be relevant to temporal tracking, but the abstract does not establish whether the device is continuously worn, how often measurements are taken, whether the 24-hour data are independent of model development, or whether the monitoring test uses reference glucose measurements throughout.

## 3. Sensor and hardware

### Optical sensing
- Measurement site: finger.
- Red LED wavelength: **660 nm**.
- Infrared LED wavelength: **880 nm**.
- PPG acquired at two wavelengths.

The authors describe the device as a wearable pulse-oximeter-based system.

### Hardware details to verify
- Sensor/device model.
- LED and detector configuration.
- Optical geometry and finger contact.
- Sampling rate.
- ADC and acquisition hardware.
- PPG recording duration per reference glucose measurement.
- Whether the two channels are acquired simultaneously or sequentially.
- Signal quality controls and contact-pressure handling.
- Device form factor and continuous-monitoring interface.

## 4. Dataset and participants

The PubMed abstract does **not** provide cohort size, demographics or participant-level data distribution.

### Verify in the full paper
- Number of participants.
- Diabetes status (healthy, prediabetes, T1D, T2D or mixed).
- Age and sex distribution.
- Participant recruitment and inclusion/exclusion criteria.
- Number of glucose references.
- Number of PPG recordings and recordings per participant.
- Glucose range and distribution.
- Whether glucose excursions are induced by meals, glucose challenges, medication, exercise or naturally occurring events.
- Whether repeated measurements are collected across days.
- Whether the cohort used for training is the same cohort as the 24-hour test.

**Do not infer cohort size or diversity from the 24-hour monitoring claim.**

## 5. Reference glucose and experimental protocol

The abstract does not establish the reference glucose instrument or protocol.

### Critical items to verify
- Reference measurement type (capillary glucometer, venous blood or other).
- Reference device and calibration.
- Timing and synchronization with PPG.
- Reference measurement frequency.
- Whether measurements are concurrent or delayed.
- How reference readings are associated with PPG samples.
- Whether reference values are available during the 24-hour test.
- Whether any calibration measurements are used for each participant.

The task should be classified as concurrent estimation, calibrated/personalized estimation, or forecasting only after the timestamp and target definitions are established.

## 6. Signal processing

The abstract does not provide the preprocessing pipeline.

### Verify
- Filtering.
- Baseline correction.
- Signal normalization.
- Pulse segmentation.
- Artifact removal.
- Channel synchronization.
- Window length and overlap.
- Whether raw PPG intensity, pulse waveforms, or derived features are used.
- Whether preprocessing parameters are fixed or data-derived.
- Whether preprocessing is fitted using training data only.

## 7. Model

### Multilayer perceptron (MLP)

The authors use a multilayer perceptron to estimate glucose from dual-wavelength PPG intensity.

The abstract states that they investigate the effect of MLP training parameters on prediction accuracy.

### Architecture and training details to verify
- Input dimensionality.
- Raw intensity versus engineered feature inputs.
- Hidden-layer count and size.
- Activation functions.
- Output layer and regression target.
- Loss function.
- Optimizer.
- Learning rate.
- Batch size.
- Epochs.
- Early stopping.
- Weight initialization.
- Regularization.
- Hyperparameter search.
- Whether training parameter comparisons use the same split.
- Whether tuning and final testing are separated.

## 8. Evaluation protocol

The abstract does not establish:
- Dataset size.
- Training/test split.
- Cross-validation.
- Split unit.
- Participant independence.
- External validation.
- The sample count underlying the reported error-grid result.

### Split-unit audit
If multiple measurements from the same participant are collected, random sample-level splitting may permit subject overlap between training and testing.

**Benchmark questions:**
1. Are partitions participant-disjoint?
2. Are repeated measurements from a participant kept in the same partition?
3. Is the 24-hour test independent of training and hyperparameter selection?
4. Was the 24-hour test performed on a new participant, a training participant, or a participant with calibration data?
5. Is the evaluation chronological?
6. Are preprocessing and model selection training-only?
7. Is any participant-specific calibration used?

## 9. Reported results

### Clarke Error Grid
The authors report:

- **99.33% of estimated BGLs in Clarke Error Grid Zones A and B.**

This is the principal numerical result in the abstract.

### 24-hour monitoring
The authors report a 24-hour monitoring test and describe it as validating the device's capability to track daily glucose fluctuations.

### Results to extract from full text
- Number of predictions in the CEG.
- Zone A percentage.
- Zone B percentage.
- Other zones, if any.
- RMSE.
- MAE.
- MARD.
- R².
- Bias.
- Bland–Altman limits, if reported.
- Results by participant.
- Results by glucose range.
- Results for the 24-hour monitoring test.
- Reference glucose coverage during the 24-hour test.
- Number of independent test participants.
- Uncertainty or confidence intervals.

### Interpretation caution
A high proportion in CEG Zones A+B is not equivalent to independent clinical validation. Error-grid outcomes depend on glucose distribution, test independence, target timing, sample count and reference protocol.

## 10. Methodological audit

These are hypotheses/questions for verification, not confirmed flaws.

### A. Participant-level independence
- The abstract does not describe the split.
- Repeated measurements may be collected.
- The 24-hour monitoring test may or may not be independent.

**Benchmark action:** identify participant IDs across training, testing and monitoring data; compare random-sample and participant-grouped protocols if data permit.

### B. 24-hour test independence
A 24-hour monitoring test is potentially valuable, but its evidentiary value depends on:
- Whether it is held out from model development.
- Whether the participant is unseen.
- Whether reference glucose measurements are available across the test.
- Whether calibration is required.
- Whether model parameters are fixed.
- Whether the test is a genuine prospective evaluation or retrospective replay.

**Benchmark action:** classify the monitoring test as external, prospective, held-out temporal, personalized/calibrated or illustrative only—based on the protocol.

### C. Temporal evaluation
The abstract's use of “continuous monitoring” does not establish future forecasting.

**Benchmark action:** distinguish:
- Concurrent estimation.
- Personalized calibrated estimation.
- Chronological within-person testing.
- Future forecasting at a specified horizon.

### D. Data leakage and preprocessing
- Verify whether normalization, feature selection and hyperparameter selection occur before or after splitting.
- Verify whether any reference glucose values from the test participant are used for calibration.
- Verify whether the 24-hour monitoring sequence is used in model tuning.

**Benchmark action:** ensure all data-dependent preprocessing and tuning are confined to training data, unless explicitly evaluating a calibration protocol.

### E. Model tuning
The paper specifically analyzes MLP training parameters.

**Audit:** hyperparameter exploration should be separated from final evaluation. If test data were used to choose the best training parameters, performance could be optimistic.

**Benchmark action:** use nested CV or a dedicated validation set within the training data, and report the final held-out test result separately.

### F. Hardware and measurement-site specificity
- Fingertip PPG.
- Dual wavelength.
- Custom wearable device.

These are different from free-living wrist-based multimodal wearables.

**Benchmark action:** stratify literature and comparison results by measurement site, hardware configuration, optical wavelength and controlled versus free-living setting.

### G. Glucose-range coverage
CEG performance may be affected by glucose-range coverage.

**Benchmark action:** extract the glucose distribution, report per-range performance and evaluate whether low and high glucose values are represented sufficiently.

### H. Reference and synchronization
PPG is a physiological signal, while glucose references may be intermittent.

**Benchmark action:** extract the exact label timestamp, reference timing, signal window and alignment method.

### I. Clinical metric interpretation
99.33% in Zones A+B is encouraging as a reported result, but:
- It does not reveal numerical error magnitude.
- It does not establish bias.
- It does not establish calibration across individuals.
- It does not establish robustness under movement or environmental changes.
- It does not independently establish clinical safety.

**Benchmark action:** report numerical errors, participant-level results, range-stratified performance, error-grid results and uncertainty together.

## 11. Reproduction checklist

- [ ] Extract full cohort and demographics.
- [ ] Extract participant-level recording counts.
- [ ] Extract diabetes-status distribution.
- [ ] Extract glucose range and distribution.
- [ ] Extract reference device and measurement protocol.
- [ ] Extract sensor configuration and acquisition specifications.
- [ ] Extract sampling rate and signal duration.
- [ ] Reconstruct preprocessing and pulse/window construction.
- [ ] Extract exact MLP architecture.
- [ ] Extract training parameters and tuning procedure.
- [ ] Verify split unit and participant overlap.
- [ ] Verify preprocessing and tuning leakage.
- [ ] Extract full numerical results and error-grid breakdown.
- [ ] Extract 24-hour monitoring-test protocol.
- [ ] Determine whether 24-hour data are independent and prospective.
- [ ] Determine whether calibration is used.
- [ ] Extract participant-level and glucose-range performance.
- [ ] Compare with simple baselines if data are accessible.

## 12. Benchmark relevance

| Dimension | Paper characteristics | Benchmark relevance |
|---|---|---|
| Task | Non-invasive BGL estimation | Regression; task timing needs verification |
| Hardware | Wearable finger PPG device | Custom wearable |
| Optical channels | 660 nm red + 880 nm IR | Dual-wavelength optical representation |
| Measurement site | Finger | Distinct from wrist |
| Model | MLP | Neural-network baseline |
| Training analysis | MLP training parameters investigated | Tuning and model-selection audit |
| Evaluation | CEG A+B reported | Clinical metric, but protocol needs verification |
| Main result | 99.33% in CEG A+B | Reported result |
| Monitoring | 24-hour monitoring test | Potential chronological evaluation |
| Split | Not specified in abstract | Participant and temporal independence must be verified |
| Cohort | Not specified in abstract | Full-text verification required |
| Reference | Not specified in abstract | Alignment and reference protocol audit |
| Reproducibility | Abstract-level details insufficient | Full text and supplemental extraction needed |

## 13. Suggested interpretation for our research paper

This paper is a **dual-wavelength fingertip wearable PPG case study** with a conventional MLP and a reported 24-hour monitoring experiment. It is relevant to our discussion of hardware, wavelength selection, model tuning and temporal monitoring.

Its 99.33% Clarke Error Grid A+B result should be treated as a reported finding rather than evidence of unseen-person or prospective generalization until the cohort, split, calibration, and 24-hour test details are verified.

The most important audit questions for our benchmark are:
1. Is the 24-hour test independent of model development?
2. Is the test participant unseen, or does the protocol use personalization?
3. Are preprocessing and hyperparameter selection separated from testing?
4. Does the device estimate glucose contemporaneously or predict future values?
5. How does performance vary by participant and glucose range?
6. What numerical error metrics accompany the error-grid result?

## 14. Source

- PubMed: https://pubmed.ncbi.nlm.nih.gov/40685463/
- DOI: https://doi.org/10.1007/s10439-025-03809-9
