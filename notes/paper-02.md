# Paper 02 — Schmelter et al. (2026)

## Bibliographic information

**Title:** Noninvasive Interstitial Glucose Estimation Using Wearables and Machine Learning in Healthy Individuals and Individuals With Obesity: Observational Cohort Study

**Authors:** Franziska Schmelter, Xinyu Huang, Annika Heidmann, Paul Beier, Friederike Miriam Rundfeldt, Christina Plöger, Artur Piet, Md Abid Hasan, Yuanheng Zhang, Lennart Jablonski, Oliver Witt, Torsten Schröder, Stefanie Derer, Chris Easthope Awai, Marcin Grzegorzek, Christian Sina.

**Journal:** JMIR mHealth and uHealth, 2026;14:e91724  
**DOI:** https://doi.org/10.2196/91724  
**Article:** https://mhealth.jmir.org/2026/1/e91724  
**Publication date:** September 30, 2026

---

## 1. Executive summary

This observational study evaluates noninvasive interstitial glucose (IG) estimation from wearable sensor signals collected in free-living conditions over 14 days. It enrolled 74 participants split into healthy controls (HC; 34) and metabolically at-risk (MR; 40) individuals. Participants simultaneously wore an Empatica EmbracePlus research wristband, a Fitbit Sense 2 smartwatch, and an Abbott FreeStyle Libre 3 CGM reference sensor.

The study develops an LSTM-based estimator using handcrafted sensor features and evaluates it using leave-one-subject-out (LOSO) cross-validation. It investigates cohort-specific and combined-cohort models, prediction horizons, a second model architecture (iTransformer), sensor-modality ablations, SHAP feature attribution, and performance relationships with HbA1c.

**Why this paper matters for the benchmark:** It is a useful counterpoint to Paper 01 because it explicitly uses LOSO, describes fold-specific scaling, reports modality ablations, and tests a consumer smartwatch. Its evaluation is not free from questions, however: the temporal prediction task and window construction need careful interpretation, reported cohort “generalization” should not be confused with external validation, and the benchmark can investigate the role of demographics/context and baselines.

## 2. Research objectives

The authors aim to:
1. Evaluate wearable-based IG estimation in real-world conditions with habitual diets and mixed meals.
2. Examine performance in healthy and metabolically at-risk cohorts.
3. Assess whether a model trained across the cohorts can generalize between the population groups.
4. Demonstrate feasibility using a consumer smartwatch, rather than only a research-grade wearable.
5. Evaluate sensor contributions and alternative prediction horizons.

This paper is primarily a feasibility and algorithm-validation study, not a benchmark of evaluation protocols.

## 3. Dataset and study design

### Participants
- Total: 74 participants.
- Healthy controls: 34 participants (reported modeling subset: 33 after exclusion).
- Metabolically at-risk: 40 participants.
- 54 women (73%) and 20 men (27%).
- Age range: 19–84 years.
- HC mean age: 24.53 ± 3.68 years.
- MR mean age: 55.38 ± 15.08 years.
- HC mean BMI: 22.36 ± 2.16 kg/m².
- MR mean BMI: 35.38 ± 4.91 kg/m².
- MR group included overweight individuals with comorbidities and individuals with obesity.

### Data collection
- Participants wore devices continuously for 14 days.
- First and last days were excluded during preprocessing, resulting in an analysis period of approximately 12 days.
- Study was conducted in free-living conditions; participants were instructed to behave normally.
- Food diaries were collected but were not used as model inputs in this analysis.

### Reference
- Abbott FreeStyle Libre 3 CGM.
- Glucose/IG reference measurements approximately every 5 minutes.
- The paper reports approximately 900,606 IG measurements across the longitudinal dataset (context of its literature comparison).

## 4. Sensor modalities

### Empatica EmbracePlus
| Signal | Sampling frequency |
|---|---:|
| Skin temperature (STEMP) | 1 Hz |
| Electrodermal activity (EDA) | 4 Hz |
| Triaxial acceleration (taACC) | 32 Hz |
| Sleep assessment | ~0.017 Hz |
| PPG/BVP-derived interbeat interval and pulse rate | ~0.017 Hz |

### Fitbit Sense 2
- Pulse rate: 0.2 Hz, as described in the paper.

### CGM
- Abbott FreeStyle Libre 3, approximately 5-minute IG values.

The study also collected demographics, anthropometrics, fasting blood/urine and metabolic phenotyping. Those measurements characterize the cohorts but should not automatically be interpreted as inputs to the wearable estimator unless explicitly stated.

## 5. Preprocessing

### Data formatting
- Wearable streams were obtained in AVRO, CSV, and JSON formats.
- Timestamp information was standardized using Unix timestamps in GMT.
- Demographic/anthropometric information included age, sex, height, weight, and BMI.
- Data were merged and converted into analysis arrays.

### Data cleaning
- The first and last CGM collection days were excluded to account for sensor stabilization and uneven recording boundaries.
- Analysis was constrained to a defined interval spanning approximately the second through thirteenth study days.
- Participant-specific outlier detection: values more than 4 SD from that participant's mean were marked invalid and replaced with local mean.
- The authors report that less than 0.2% of data points were affected.

### Alignment and interpolation
- An artificial regular timestep array was created.
- Continuous signals such as EDA, acceleration, temperature and pulse rate were linearly interpolated.
- CGM labels were evaluated with alternative interpolation, including nearest-neighbor interpolation.
- The precise implications of interpolation around the target time and the prediction horizon should be independently verified when reproducing the pipeline.

### Segmentation
- Nonoverlapping windows.
- Each sample/window was constructed from the sensor period immediately preceding the corresponding glucose label.
- Segmentation was performed independently within each participant's time series.
- Prediction horizon (PH) settings investigated: 15, 30, and 60 minutes.
- The primary analysis emphasized PH=30 minutes.

**Important interpretation:** The paper describes a PH-based windowing scheme and the window immediately preceding the label. The exact target timestamp convention and whether the PH represents a forecast offset or label/window alignment should be carefully verified from the equations and supplementary materials before implementation. Avoid treating “30-minute PH” as unambiguously forecasting 30 minutes into the future without reconstructing the indexing.

## 6. Feature engineering

### Feature structure
- Sensor data were divided into consecutive one-minute subwindows.
- For each signal, 22 features per modality were retained.
- Five contextual/demographic features were added.
- Empatica representation: 159 features per minute in a 30-minute observation window.
- Fitbit representation: 27 features per minute/window representation, as reported.
- Feature families included statistical/time-domain descriptors, frequency/amplitude measures, morphology-related features, temporal/circadian context and demographic/anthropometric variables.
- No dimensionality reduction or feature selection was applied before the LSTM.

The supplementary table is needed to reproduce the exact feature list. The counts and categories in this note are based on the main paper.

## 7. Models

### Primary model: LSTM
- Four stacked LSTM layers with progressively decreasing hidden dimensions.
- Output from the final timestep passed through two fully connected layers, with a nonlinear tanh between them, and a scalar output.
- Empirically selected architecture.

### Alternative model: iTransformer
- Evaluated as an architecture comparison at the primary PH.
- Uses variable-wise temporal embeddings and attention across variable tokens.

### Training
- PyTorch 2.7.1+cu128; Python 3.11.13.
- GPU reported: NVIDIA RTX 5090, 32 GB RAM.
- 70 epochs.
- Batch size: 32.
- Adam optimizer; learning rate 0.001.
- MAE/L1 loss.
- Hyperparameter grid search performed on validation data within each LOSO fold.

## 8. Evaluation protocol and leakage controls

### LOSO
- Leave-one-subject-out cross-validation.
- One participant held out for testing in each fold.
- Hyperparameter selection on validation partition within each fold.

### Scaling
- StandardScaler applied to input features and glucose target.
- Scaling fit using training participants in the fold only.
- Fold-specific target scaling reversed for reporting in original mg/dL units.

This is a strong protocol choice for evaluating transfer to an unseen participant, assuming all other data-dependent operations and validation decisions remain confined to training/validation data.

### Caveats to audit during reproduction
- Verify whether the validation participant/grouping is strictly subject-disjoint from the fold's training subjects.
- Confirm that all feature engineering, interpolation, imputation, outlier thresholds and feature transformations are independent of held-out data where required.
- Audit the exact timestamp/window alignment and PH definition.
- Clarify whether the bootstrapping procedure resamples at participant level or at prediction level; the paper's wording should be checked against supplementary implementation details.
- The distinction between pooled cohort training/testing under LOSO and external validation on a separately collected dataset should remain explicit.

## 9. Metrics

- MAE (mg/dL)
- MAPE
- RMSE (mg/dL)
- Clarke Error Grid Analysis (CEGA)
- 95% confidence intervals for MAE and RMSE via 1,000 bootstrap resamples, as reported.

Clinical error-grid results should be interpreted as descriptive analysis rather than evidence of clinical readiness.

## 10. Reported results

### Empatica, cohort-specific and combined models (30-minute PH)

| Cohort/model | MAE (mg/dL) | MAPE | RMSE (mg/dL) | Zone D |
|---|---:|---:|---:|---:|
| HC | 13.23 ± 1.86 | 0.14 ± 0.02 | 17.41 ± 2.38 | 2.0% |
| MR | 19.75 ± 8.11 | 0.18 ± 0.06 | 25.53 ± 9.28 | 1.4% |
| Combined | 15.98 ± 7.19 | 0.15 ± 0.05 | 21.04 ± 8.32 | 1.7% |

- HC modeling subset: 33 participants; one participant excluded due to problematic data collection.
- Combined cohort model was evaluated with LOSO.
- Performance was stratified by cohort in the combined analysis.

### Fitbit Sense 2, combined model
- MAE: 18.91 ± 10.76 mg/dL.
- MAPE: 0.18 ± 0.11.
- RMSE: 23.49 ± 11.46 mg/dL.
- Fitbit MR cohort included 38 participants due to missing data from two MR participants.

### Prediction horizons
**Empatica:**
| PH | RMSE (mg/dL) | MAE (mg/dL) |
|---|---:|---:|
| 15 min | 21.69 ± 8.30 | — |
| 30 min | 21.04 ± 8.32 | 15.98 ± 7.19 |
| 60 min | 22.68 ± 9.01 | — |

**Fitbit:**
| PH | RMSE (mg/dL) |
|---|---:|
| 15 min | 26.10 ± 15.30 |
| 30 min | 23.49 ± 11.46 |
| 60 min | 24.50 ± 11.75 |

### LSTM versus iTransformer
- Empatica: LSTM reported to outperform iTransformer.
- Fitbit: the article's narrative and values should be reconciled carefully: it reports LSTM RMSE 23.49 ± 11.46 versus iTransformer RMSE 22.68 ± 8.40 mg/dL, while claiming LSTM remained competitive and had lower MAE.
- Avoid repeating the blanket claim that LSTM outperformed both architectures across every device/metric without checking the full supplementary table.

### Modality ablation
Single-modality removal reported increases in RMSE:
| Removed modality | RMSE increase (mg/dL) |
|---|---:|
| PPG | 5.82 |
| taACC | 8.43 |
| EDA | 4.17 |
| STEMP | 2.35 |

Selected paired removals:
| Removed modalities | RMSE increase (mg/dL) |
|---|---:|
| EDA + PPG | 9.13 |
| STEMP + taACC | 7.61 |
| PPG + taACC | 10.25 |

The paper reports taACC and PPG as especially influential, with EDA and temperature contributing as well.

### SHAP
- SHAP calculated for the full dataset analysis described as n=39,314 samples.
- Top 30 features: 3 demographic (10%), 2 temporal/context (6.7%), and 25 wearable-derived (83.3%).
- Among sensor features, acceleration-derived features were most common (12/30; 40%), followed by BVP-related (6/30; 20%), temperature (4/30; 13.3%), and EDA (3/30; 10%).
- The paper emphasizes demographic and temporal features as influential.

Feature attribution is descriptive: it does not establish causal physiological mechanisms.

## 11. Interpretation

### Strengths
1. A comparatively substantial longitudinal cohort (74 participants).
2. Two wearable platforms evaluated in parallel.
3. Free-living collection over approximately two weeks.
4. LOSO evaluation, appropriate for unseen-subject generalization.
5. Fold-specific scaling is explicitly described.
6. PH comparison (15, 30, 60 minutes).
7. Modality ablation and SHAP analyses.
8. Consumer smartwatch feasibility evaluated.
9. No food logs used as estimator inputs.

### Limitations and questions for independent audit
1. **Cohort composition and confounding:** HC and MR groups differ substantially in age and BMI. Cohort-level performance differences may reflect multiple confounded variables.
2. **Generalization scope:** LOSO is within the collected study cohort. This is not external validation on a separately recruited dataset, device generation, or site.
3. **Temporal task definition:** The precise label/window/PH relationship must be reconstructed to ensure the reported task is correctly characterized as estimation or forecasting.
4. **Baselines:** The main model comparison focuses on LSTM and iTransformer. The benchmark should independently evaluate simple population baselines, linear/ridge models, tree models, and personal baselines under consistent splits.
5. **Modality availability:** Fitbit provides a much narrower sensor set than EmbracePlus; comparison is not strictly a controlled device-only change.
6. **Demographic/context contribution:** Strong demographic and temporal feature importance raises questions about how much performance is attributable to context versus moment-to-moment physiological signals. Test with ablations.
7. **Statistical unit:** Hundreds of thousands of readings do not equate to hundreds of thousands of independent observations. Participant-level uncertainty and comparisons are essential.
8. **SHAP interpretation:** Attribution should not be equated with physiological causality.
9. **Clinical interpretation:** CEGA and RMSE alone do not demonstrate clinical safety or suitability for treatment decisions.

These are benchmark questions, not accusations of methodological error. The paper's actual protocol should be verified before any criticism is finalized.

## 12. Relevance to wearable-glucose-benchmark

This paper is particularly useful as a benchmark reference for:
- Subject-independent evaluation (LOSO).
- Fold-specific normalization.
- Forecast/observation-window alignment.
- Different horizons (15, 30, 60 minutes).
- Modality ablation.
- Demographic/context ablation.
- Research-grade versus consumer device comparison.
- Evaluation across participant subgroups.
- Uncertainty reporting.

### Proposed experiments inspired by this paper
- **B2-E1:** Reproduce the LOSO protocol.
- **B2-E2:** Compare LOSO with random epoch split and chronological within-person split where applicable.
- **B2-E3:** Compare simple baselines against LSTM and iTransformer.
- **B2-E4:** Reconstruct the timestamp/PH semantics and test multiple horizons under a strict forecasting protocol.
- **B2-E5:** Sensor modality ablations.
- **B2-E6:** Demographic/context-only versus sensor-only versus combined features.
- **B2-E7:** Test cohort-stratified metrics and participant-level variability.
- **B2-E8:** External validation on an independent dataset if compatible data are available.

## 13. Benchmark reproducibility checklist

- [ ] Obtain exact feature definitions (supplementary Table S3).
- [ ] Obtain full supplementary tables (S4–S6) and figures.
- [ ] Verify the exact LOSO fold and validation design.
- [ ] Reconstruct time alignment and PH indexing.
- [ ] Verify interpolation implementation and its label-time implications.
- [ ] Ensure preprocessing and scaling are fit on training data only.
- [ ] Reproduce 15-, 30-, and 60-minute settings.
- [ ] Reproduce the LSTM baseline before implementing iTransformer.
- [ ] Report MAE, RMSE, MAPE, and CEGA.
- [ ] Compute uncertainty with participant-aware resampling.
- [ ] Reproduce modality ablations.
- [ ] Separate model performance from clinical claims.

## 14. Research assessment

**Role in the review/benchmark:** A strong methodological reference, not an obvious example of participant leakage. It demonstrates that LOSO, fold-specific scaling, horizon analysis, modality ablations, and a consumer-device comparison can be incorporated into wearable glucose estimation research.

The benchmark should use this paper to establish a comparatively rigorous protocol and investigate remaining questions—especially baseline adequacy, temporal-task semantics, contextual feature contribution, statistical independence, and external validation.

## 15. References and links

- Article: https://mhealth.jmir.org/2026/1/e91724
- DOI: https://doi.org/10.2196/91724
- Preprint: https://preprints.jmir.org/preprint/91724
