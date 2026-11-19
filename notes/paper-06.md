# Paper 06 — Cheng et al. (2025)

## Citation
Cheng, J., Xie, P., Zhao, H., & Ji, Z. (2025). **Differential Absorbance and PPG-Based Non-Invasive Blood Glucose Measurement Using Spatiotemporal Multimodal Fused LSTM Model.** *Sensors*, 25(17), 5260. https://doi.org/10.3390/s25175260

- Full text: https://pmc.ncbi.nlm.nih.gov/articles/PMC12431362/
- DOI: https://doi.org/10.3390/s25175260
- PMCID: PMC12431362
- PMID: 40942692
- Publication date: 24 August 2025

> Scope note: This note captures verified information from the accessible article abstract and introductory/methods text. Some detailed methods and tables were not independently extractable in this session. Items marked **verify in full text** are deliberately not presented as confirmed.

## 1. Study overview

The study develops a custom non-invasive blood glucose measurement system (NIBGMS) using visible–near-infrared optical sensing, combining differential absorbance measurements with photoplethysmography (PPG). The proposed STMF-LSTM fuses optical spatial information (differential absorbance and PPG) with glucose temporal information.

This is primarily a **custom hardware + optical signal processing + machine-learning study**, rather than a free-living consumer smartwatch dataset study.

## 2. Study goals and contribution

The authors motivate the work with two limitations in prior data-driven non-invasive glucose estimation:
- PPG alone may have limited specificity to glucose.
- Models that rely only on historical glucose may not incorporate physiological/optical information and may lose accuracy over longer prediction horizons.

Their proposed system seeks to combine:
1. Wavelength-specific optical information.
2. Tissue-depth information obtained through different LED–photodetector separations.
3. PPG waveform information.
4. Temporal glucose information in a spatiotemporal recurrent model.

The authors describe this as a potentially low-cost non-invasive monitoring approach. This should be understood as a proof-of-concept, not established clinical capability.

## 3. Sensor and hardware

### Optical sensing configuration
- Measurement site: fingertip, selected for capillary density and accessibility.
- LED wavelengths: **625 nm, 850 nm, and 940 nm**.
- Multiple photodetectors (PDs) with different source–detector separation distances.
- The differing distances are intended to capture optical information associated with different tissue depths.
- PPG and differential absorbance are collected with the custom NIBGMS.

### Relevance of the hardware design
The study is not equivalent to single-channel wrist PPG. It uses a dedicated finger sensor with wavelength and spatial/depth resolution. Performance should not be assumed transferable to conventional consumer wearable optical hardware.

**Verify in full text:** exact PD count, geometry/separation distances, acquisition timing, sampling rates, sensor calibration, raw signal channels, hardware diagrams and reproducibility of the device.

## 4. Participants and data

The abstract reports:
- **18 subjects in the initial data collection:** 15 non-diabetic and 3 individuals with type 2 diabetes.
- **805 samples** in this initial dataset.
- A separate continuous dataset containing **272 samples from four non-diabetic subjects** for validation of the STMF-LSTM model.

### Cohort and sample-size caveats
- The number of subjects is small.
- Only three participants are reported to have T2D in the initial cohort.
- The separate continuous dataset is small and comprises four non-diabetic participants.
- The count of samples is not the count of independent participants.
- The dataset appears to have limited diversity in clinical status and is not representative evidence of population-wide generalization.

**Verify in full text:** demographics, glucose range, reference glucose measurement method, measurement protocol, subject-level distribution of samples, repeated sessions, fasting/postprandial protocol, and whether datasets overlap in participants or acquisition conditions.

## 5. Reference glucose and measurement protocol

The study evaluates optical measurements against glucose references. The abstract establishes the sample counts but detailed reference-device and timing information was not independently verified here.

**Critical items to verify:**
- Reference instrument and calibration procedure.
- Whether reference values were measured simultaneously or near the optical acquisition.
- Exact synchronization and alignment rules.
- Whether reference measurement is blood glucose, capillary glucose, or another reference.
- Label timing relative to the optical signal window.
- Whether any glucose reference is included as model input (autoregressive/historical glucose) versus only target.
- Whether measurement sessions include fasting, glucose challenge, meals, exercise or other protocol conditions.

These details determine whether the task is concurrent estimation, retrospective estimation, or forecasting.

## 6. Modeling approach

### STMF-LSTM
The authors propose a **Spatiotemporal Multimodal Fused LSTM (STMF-LSTM)**:
- Optical spatial information from differential absorbance and PPG.
- Glucose temporal information.
- LSTM-based modeling to represent temporal dependencies.

The abstract states that multimodal fusion is used to improve glucose prediction performance.

**Verify in full text:** exact architecture, input tensors, sequence lengths, feature dimensions, fusion stage, hidden sizes, layer count, loss function, optimizer, training epochs, regularization, implementation and hyperparameter selection.

## 7. Baselines and model comparisons

For preliminary validity of the NIBGMS, the paper reports evaluating:
- Multilayer perceptron (MLP)
- Support vector regression (SVR)
- Random forest regression (RFR)
- Extreme gradient boosting (XGBoost)

The STMF-LSTM is then evaluated on the continuous dataset.

**Verify in full text:** baseline feature inputs, tuning strategy, split protocol, baseline results, whether baselines use identical preprocessing and data partitions, and whether the comparison includes simple mean/persistence or subject-specific baselines.

## 8. Evaluation protocol

Reported evaluation:
- **8-fold cross-validation** for the STMF-LSTM results described in the abstract.
- Continuous dataset: **272 samples from four non-diabetic participants**, described as validation of the developed model.

**Critical evaluation-unit question:** the abstract does not establish whether folds are grouped by participant, measurement session, sequence, or individual sample. This must be checked in the paper.

### Evaluation details to verify
1. Are folds participant-independent?
2. Are all samples from a participant assigned to the same fold?
3. Are repeated measurements or adjacent/overlapping signal windows split across folds?
4. Was the continuous dataset used as a held-out test set, a validation set, or only an additional evaluation?
5. Were model selection and hyperparameter tuning nested within the cross-validation procedure?
6. Were preprocessing, normalization, feature selection and calibration fitted using only training folds?
7. Does any temporal information from the target period enter the input?
8. Is the separate continuous dataset independent at the participant and acquisition level?

## 9. Reported results

### STMF-LSTM
The abstract reports:
- **RMSE: 0.811 mmol/L**
- **Parkes error grid: 100% of predictions in Zones A and B**
- Evaluation context: **8-fold cross-validation**

The abstract does not provide sufficient detail to determine the exact sample-level/participant-level averaging procedure or the full results on the 272-sample continuous dataset.

### Comparability caution
Do not directly compare this RMSE to studies reporting mg/dL until conversion and evaluation protocols are confirmed.

0.811 mmol/L is approximately **14.6 mg/dL** (using 1 mmol/L ≈ 18 mg/dL). However, the numeric conversion does not make the result methodologically comparable to studies with different populations, reference methods, data collection protocols, glucose ranges, target timing, or split units.

**Verify in full text:** MAE, RMSE distribution, R², error grid details, participant-level results, results on the separate continuous data, glucose range and any uncertainty estimates.

## 10. Methodological audit for our benchmark

These are **audit hypotheses/questions**, not confirmed defects.

### A. Participant leakage / split unit
- The reported 8-fold CV needs a clearly documented split unit.
- With repeated optical measurements from a small cohort, random sample-level folds could allow subject-specific characteristics to appear in both train and test.
- A participant-grouped evaluation would better test generalization to unseen people.

**Benchmark action:** if data are available, reproduce the paper's split and compare with GroupKFold/LOSO.

### B. Small cohort and effective sample size
- 18 people in the initial cohort and four in the continuous dataset are small.
- 805 and 272 samples should not be treated as equivalent to hundreds of independent subjects.
- Report results per participant and participant-aware uncertainty.

**Benchmark action:** report the number of participants, samples per participant, and participant-level RMSE/MAE distributions.

### C. Optical hardware specificity
- This is a specialized finger sensor with multiple wavelengths and source–detector distances.
- Its signals may not transfer to a wrist-based consumer wearable.
- Device, geometry, skin/tissue characteristics, contact pressure and optical path differences may be entangled with the target.

**Benchmark action:** classify the paper as custom optical hardware rather than generic wearable data; avoid direct model ranking against smartwatch-only studies without contextual stratification.

### D. Temporal alignment and task definition
- The phrase prediction and use of temporal glucose information warrant close inspection.
- Determine whether the task is contemporaneous estimation or future prediction.
- If past glucose is included, quantify how much performance is attributable to glucose-history information versus optical signals.

**Benchmark action:** compare optical-only, glucose-history-only, and fused inputs; define label timestamp and forecast horizon explicitly.

### E. Independent continuous evaluation
- The continuous dataset of 272 samples from four non-diabetic participants could provide useful additional evidence, depending on its independence and protocol.
- Four participants are still a very limited basis for generalization.

**Benchmark action:** identify whether participants are independent of training data, and report the evaluation as a distinct test cohort only if participant-level independence is established.

### F. Cross-validation and model selection
- An 8-fold procedure is not sufficient by itself to establish rigorous evaluation.
- Check whether model selection, normalization and any preprocessing are fold-local.

**Benchmark action:** document the complete data flow and ensure training-only fitting of preprocessing and tuning.

### G. Baseline strength
- MLP, SVR, RF and XGBoost are useful comparisons, but baseline fairness depends on their inputs, tuning, and split.
- Simple baselines such as population mean, previous reference glucose (if available), or subject calibration may be important depending on the task definition.

**Benchmark action:** add task-appropriate simple baselines and identical split/input controls.

### H. Clinical metric interpretation
- 100% in Parkes zones A+B is encouraging but does not fully characterize numerical accuracy, errors near clinically important thresholds, or uncertainty.
- Error-grid outcomes depend on the glucose range and sample distribution.

**Benchmark action:** report RMSE/MAE, per-person distributions, glucose-range coverage and error-grid proportions together. Avoid interpreting error-grid performance as clinical validation.

## 11. Relevance to the wearable-glucose benchmark

| Dimension | Paper characteristics | Benchmark relevance |
|---|---|---|
| Sensor | Custom fingertip optical device | Hardware-specific; indirect relevance to wrist wearables |
| Modalities | Differential absorbance + PPG + temporal glucose information | Multimodal fusion and input-ablation reference |
| Population | 15 non-diabetic + 3 T2D in initial dataset | Small cohort; limited subgroup coverage |
| Samples | 805 initial samples | Verify independent sample unit |
| Additional evaluation | 272 samples from 4 non-diabetic participants | Independence and participant-level generalization need verification |
| Model | STMF-LSTM | Complex multimodal sequential architecture |
| Baselines | MLP, SVR, RF, XGBoost | Useful classical baselines |
| Evaluation | 8-fold CV reported | Split unit and fold independence are central audit points |
| Metrics | RMSE 0.811 mmol/L; Parkes A+B 100% | Numerical and clinical metrics; scale/protocol comparability cautions |
| Forecasting | Task timing not fully verified | Need to distinguish concurrent estimation from forecasting |

## 12. Reproduction checklist

- [ ] Obtain the full article tables/figures and supplement.
- [ ] Record exact sensor geometry, wavelength channels, sampling rates and acquisition protocol.
- [ ] Identify reference glucose instrument and synchronization.
- [ ] Extract participant demographics, glucose distribution and sample counts per participant.
- [ ] Determine whether the 805 samples are individual readings, segments or aggregated windows.
- [ ] Determine the 8-fold split unit and whether participant overlap occurs.
- [ ] Extract preprocessing and feature/sequence construction.
- [ ] Extract STMF-LSTM architecture and hyperparameters.
- [ ] Extract baseline inputs and tuning protocol.
- [ ] Determine the independent status of the 272-sample continuous dataset.
- [ ] Extract full numerical and error-grid results.
- [ ] If possible, reproduce the paper split and add participant-grouped evaluation.
- [ ] Ablate optical modalities and glucose-history inputs separately.
- [ ] Report participant-level performance and uncertainty.

## 13. Suggested benchmark positioning

This paper is useful as a **multimodal optical sensing case study** and an example of a study where the distinction between sample-level cross-validation and participant-independent evaluation is especially important to inspect.

It should not be treated as direct evidence for the performance of consumer wrist-based wearable glucose estimation. Its hardware, measurement site, optical configuration and data collection setting differ substantially from free-living wrist sensor datasets.

The most relevant methodological questions for our project are:
1. Does cross-validation split at the participant level?
2. How much performance comes from glucose history versus optical sensor input?
3. How independently does the continuous dataset validate the model?
4. How stable is performance across participants and glucose ranges?
5. Can the protocol be reproduced sufficiently to compare against simple baselines?

## 14. Source

- Article: https://pmc.ncbi.nlm.nih.gov/articles/PMC12431362/
- DOI: https://doi.org/10.3390/s25175260

---
