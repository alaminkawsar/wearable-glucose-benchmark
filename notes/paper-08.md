# Paper 08 — Rajeswari & Vijayakumar (2024)

## Citation
Rajeswari, S. V. K. R., & Vijayakumar, P. (2024). **Development of sensor system and data analytic framework for non-invasive blood glucose prediction.** *Scientific Reports*, 14, 9206. https://doi.org/10.1038/s41598-024-59744-7

- Article: https://www.nature.com/articles/s41598-024-59744-7
- DOI: https://doi.org/10.1038/s41598-024-59744-7
- Published: 22 April 2024

> Verification note: This note is based on the article page/abstract and accessible portions of the Methods and conclusion. Detailed model tables, feature lists and data-flow/split descriptions should be checked directly against the full article and its supplementary materials. Where those details could not be independently verified here, they are flagged.

## 1. Study overview

The paper introduces **niGLUC-2.0v**, a custom non-invasive glucose-monitoring system using near-infrared (NIR) sensing with two measurement configurations: a finger prototype and a wrist prototype. The optical setup uses a 940 nm emitter and a detector covering 900–1700 nm. The study recruited 101 diabetic and non-diabetic volunteers and reports a data-analytic framework with ridge regression (RR).

The authors focus on challenges they describe as ambiguities in non-invasive sensing, including skin color, ambient light, and pressure at the measurement site. The device is tested under stable/sitting conditions.

**Important scope distinction:** despite the wrist prototype, the article describes a custom hardware device tested in stable conditions. It is not a free-living wrist-wearable dataset study and is not directly comparable with continuous smartwatch sensor datasets.

## 2. Research objective and contribution

The paper's objectives are to:
1. Design and build a custom NIR-based glucose sensing system.
2. Create finger and wrist measurement prototypes.
3. Investigate factors that may interfere with optical measurements, including ambient light, pressure and skin-color variation.
4. Develop a data-analytic framework for glucose prediction.
5. Compare prototype measurements against invasive glucometer reference measurements.
6. Evaluate performance using numerical metrics, Bland–Altman analysis and Clarke Error Grid (CEG).

The authors emphasize the combination of hardware design and data analysis rather than a new deep-learning architecture.

## 3. Sensor and hardware

### niGLUC-2.0v
- Optical emitter wavelength: **940 nm**.
- Detector wavelength range: **900–1700 nm**.
- Two prototype configurations:
  - Finger sensor.
  - Wrist sensor.
- Data are recorded as voltage-derived optical readings and visualized through LabVIEW.
- The study discusses hardware design intended to mitigate measurement ambiguities.

### Measurement-site distinction
The finger and wrist configurations are separately evaluated. Their results should not be assumed interchangeable: tissue geometry, contact, vascular characteristics and signal acquisition differ by site.

**Verify in full text/supplement:**
- Exact emitter/detector geometry and hardware components.
- Sensor contact/measurement geometry.
- Sampling rate and measurement duration.
- Exact number of recorded voltage features/readings per measurement.
- How the finger and wrist data are synchronized and whether they are collected for all participants.

## 4. Participants and cohort

The paper reports **101 volunteers**, described as diabetic and non-diabetic:
- 57 male.
- 44 female.
- Age range reported in accessible text: approximately 25–78 years in one passage, while the conclusion/methods descriptions also reference an age range of 20–90 years. **This apparent range inconsistency should be checked against the cohort table.**
- Glucose measurements spanning **80–488 mg/dL** are reported in the accessible Methods text.
- Inclusion criteria and exclusions are described, including exclusions related to hypoglycemic episodes with unconsciousness and seizure disorders.

### Cohort audit
- 101 participants is larger than several small PPG studies in our collection, but still does not by itself establish robust participant-independent validation.
- The number of reference readings per participant and the distribution of readings across glucose ranges are crucial.
- Diabetes status, repeated measurements, participant characteristics and acquisition conditions may be correlated with the target.

**Verify in full text:** cohort table, diabetes-status breakdown, participant-level reading counts, age distribution, skin-color characterization and glucose-range distribution.

## 5. Reference measurement and acquisition protocol

### Reference glucose
- Reference values are collected using an invasive finger-prick glucometer.
- The article describes new lancets and strips for each sample.
- Reference glucose measurements are recorded and compared against the prototype measurements.

### Collection conditions
The accessible text describes:
- Fasting measurements.
- Postprandial measurements.
- Random glucose measurements.
- Finger and wrist prototype measurements.
- Participants in a stable/sitting position to minimize motion artifact.

The authors describe a single sample being represented by **100 data points recorded in an Excel sheet**, though the precise definition of these 100 points (raw acquisition samples, feature records, or repeated values) needs careful verification.

### Task definition
The task appears to be **concurrent glucose estimation**, where prototype measurements are compared with contemporaneous invasive reference readings. The paper does not establish a future forecasting task in the accessible text.

## 6. Signal normalization and preprocessing

The accessible methods describe normalization intended to remove dark-current effects.

Recorded quantities:
- HV: niGLUC-2.0v reading.
- LV: LED value.
- DC: dark-current value, obtained with the device switched off.

Normalization is described as:

\[
N=\left(\frac{HV-DC}{LV-DC}\right)\times100
\]

The normalized value is used to account for the dark current.

### Preprocessing audit
**Verify in full text:**
- Exact data preprocessing sequence.
- Whether normalized signals are averaged or aggregated into per-reading features.
- Any outlier handling, smoothing or filtering.
- Whether preprocessing parameters are fitted globally or per measurement.
- Whether the normalized data are split into train/test before model fitting.
- Whether multiple voltage readings from the same measurement are retained as independent rows.

## 7. Data-analytic framework and models

The paper describes exploratory data analysis, visualization, integration and machine-learning methods. **Ridge Regression (RR)** is explicitly reported as the model associated with the abstract's headline results.

The abstract reports:
- Finger prototype with RR: accuracy 99.02%, MAE 0.15, MSE 0.22.
- Wrist prototype with RR: accuracy 99.96%, MAE 0.06, MSE 0.006.

The exact operational definition of “accuracy” in a regression task, the metric units, and the exact features/inputs should be verified from the full methods and tables before interpreting these values.

### Model and analysis details to verify
- Exact model list and which models were tested for each prototype.
- Features used as model input.
- Whether EDA refers to exploratory data analysis or electrodermal activity (the accessible context uses it in the data-analysis sense).
- Ridge regularization parameters and selection procedure.
- Feature scaling and normalization.
- Training/validation/test procedure.
- Whether results are computed from held-out data or in-sample predictions.
- Whether the model is calibrated separately for finger and wrist.

## 8. Experimental conditions and ambiguity tests

The study specifically examines several conditions that can affect optical measurements.

### A. Ambient light
The device is tested in ambient-light ON and OFF conditions. The authors report that non-invasive measurements were close to reference readings under both conditions.

**Audit:** determine whether these are distinct test samples, repeated measurements from the same participants, and whether any ambient-light conditions are represented in model training.

### B. Sensor pressure
The authors describe testing measurements with and without pressure on the finger/wrist prototypes. They report that pressure did not materially influence the device in their experimental configuration.

**Audit:** verify the pressure protocol, pressure levels, participant-level pairing and statistical test.

### C. Skin color
An adjustment factor is proposed to account for skin-color variation and is evaluated across intervals, then applied to voltage-derived readings.

**Audit:**
- How is skin color measured or categorized?
- Is the adjustment factor pre-specified or derived from data?
- Is the adjustment factor estimated per participant, per subgroup, or globally?
- Does adjustment use reference glucose labels from the same participant?
- Does skin-color adjustment generalize to unseen participants?
- Are subgroup performance results reported with sufficient sample sizes?

### D. Motion
Measurements are obtained in a stable sitting position to minimize motion artifacts. The article identifies testing under motion as a limitation and future work.

This is a major limitation for the wearable-glucose benchmark: wrist-based hardware evaluated at rest does not establish performance during everyday movement.

## 9. Reported results

### Abstract headline results

| Configuration | Model | Reported accuracy | MAE | MSE |
|---|---|---:|---:|---:|
| Finger | Ridge Regression | 99.02% | 0.15 | 0.22 |
| Wrist | Ridge Regression | 99.96% | 0.06 | 0.006 |

**Metric units and definitions require verification.** These reported MAE/MSE values cannot be compared directly with RMSE/MAE values reported in mg/dL or mmol/L elsewhere without resolving units, scale, and the definition of accuracy.

### Bland–Altman
The abstract reports:
- 98% of data points within ±1.96 standard deviations.

Verify whether this refers to the finger prototype, wrist prototype, both, and the number of observations used. Bland–Altman limits of agreement should be assessed in the original glucose scale and ideally examined for bias across the glucose range.

### Clarke Error Grid
The abstract reports:
- **100% of data points in Zone A** of the Clarke Error Grid.

This is the authors' reported outcome. It is not equivalent to independent clinical validation or proof of safe use.

### Statistical significance
The abstract reports statistical analysis with **p < 0.05**.

The exact null hypothesis, test, pairing, unit of analysis and sample size must be extracted before interpreting this as evidence of accuracy.

## 10. Evaluation protocol: critical audit

The exact data partition and validation unit were not independently verified from the accessible article text. This is one of the most important questions for the benchmark.

### A. Train/test split and independent evaluation
- Does the study report a held-out test set?
- Are measurements split randomly at the sample level?
- Are repeated measurements from the same person distributed across training and test?
- Is evaluation performed on unseen participants?
- Is the evaluation independent of model selection?

With 101 volunteers and potentially repeated fasting, postprandial and random readings, sample-level partitioning could produce participant overlap and optimistic estimates.

### B. Repeated measures and sample dependence
- Are repeated readings from the same participant treated as independent observations?
- Is the number of unique participants in each evaluation set reported?
- Does statistical inference use the participant as the independent unit?

**Benchmark action:** report per-participant performance and participant-aware uncertainty, rather than relying only on pooled reading-level metrics.

### C. Participant-specific calibration
Determine whether any participant-specific correction, skin-color factor, calibration or adjustment uses reference measurements from that participant.

If calibration uses labeled data from a test participant, the evaluation should be explicitly classified as a personalized/calibrated protocol—not unseen-person generalization.

### D. Feature selection and preprocessing leakage
- Were normalization, feature construction, adjustments and model selection performed using all samples?
- Were any parameters optimized against the test set?
- Was the skin-color adjustment factor estimated independently of test data?

**Benchmark action:** fit data-dependent processing, adjustment and model selection on training data only, unless a calibration protocol is explicitly being evaluated.

### E. Glucose-range coverage
The reported range (80–488 mg/dL) is broad, but the distribution matters:
- How many observations fall at low glucose?
- How many observations fall at very high glucose?
- Are high glucose readings concentrated in a small number of participants?
- Is performance stable across the range?

**Benchmark action:** report glucose-range-stratified results and participant-level range coverage.

### F. Measurement conditions
The study emphasizes rest, ambient light, pressure and skin color. It does not establish robust measurement under free-living movement in the accessible text.

**Benchmark action:** treat stationary measurement and free-living wearable sensing as separate settings.

## 11. Methodological concerns and hypotheses

These are **questions to audit—not confirmed flaws**.

### 1. Extremely high reported accuracy
The abstract reports 99.02% and 99.96% accuracy for a regression task, which requires clarification of how “accuracy” is defined.

- Is this R² expressed as a percentage?
- Is it a tolerance-based percentage?
- Is it a classification-like metric?
- Does it correspond to a separate test set?

The headline percentages should not be interpreted as conventional regression accuracy until the definition is established.

### 2. Participant-level independence
The 101-participant cohort and repeated readings could create a large reading count but a much smaller effective independent sample size.

- Verify the split unit.
- Verify participant overlap.
- Determine whether repeated measures are pooled.

### 3. Metric-scale ambiguity
The abstract reports MAE and MSE without clearly establishing the scale in the accessible abstract.

- Verify whether values are mg/dL, normalized values, another unit, or transformed targets.
- Confirm the target scale used during training and evaluation.
- Confirm whether inverse transformation was applied before metrics.

### 4. Calibration and adjustment
Skin-color correction and other adjustments may improve measurement but must be defined as part of the protocol.

- Is calibration performed using a single reference reading?
- Does the correction use participant-specific data?
- Is correction available at test time?
- Is calibration burden reported?

### 5. Stationary-only testing
Measurements were taken in a stable sitting position to minimize motion artifacts. The authors themselves identify this limitation and propose motion testing in future work.

This means the study is not evidence of reliable continuous free-living wrist glucose monitoring.

### 6. Independent validation
The accessible article text does not establish an external independent cohort.

- Determine whether the evaluation is internal.
- Determine whether any independent cohort or acquisition session is used.
- Do not interpret internal testing as external validation.

### 7. Statistical inference
A p-value <0.05 does not establish predictive accuracy or clinical significance.

- Identify the exact statistical test.
- Verify participant/sample-level independence.
- Verify paired comparisons.
- Examine bias and limits of agreement.

## 12. Relevance to our wearable-glucose benchmark

| Dimension | Paper characteristics | Benchmark relevance |
|---|---|---|
| Hardware | Custom niGLUC-2.0v | Custom-device study |
| Optical technology | 940 nm NIR emitter; 900–1700 nm detector | Different from common RGB/green wrist PPG |
| Sites | Finger and wrist | Useful site comparison |
| Cohort | 101 diabetic and non-diabetic volunteers | Larger participant cohort than many proof-of-concept papers |
| Reference | Invasive finger-prick glucometer | Reference measurement |
| Protocol | Fasting, postprandial, random; stable sitting | Controlled/resting concurrent estimation |
| Glucose range | 80–488 mg/dL reported | Range coverage needs distributional audit |
| Model | Ridge Regression reported | Simple linear-model reference |
| Preprocessing | Dark-current normalization | Calibration/preprocessing protocol audit |
| Adjustment | Skin-color adjustment factor | Potential calibration and subgroup protocol |
| Ambiguities tested | Ambient light and sensor pressure | Useful experimental robustness tests |
| Motion | Stable sitting; motion limitation | Not free-living |
| Metrics | Accuracy, MAE, MSE, Bland–Altman, CEG | Definitions and scales require verification |
| Results | Finger 99.02%; wrist 99.96%; 100% CEG Zone A reported | Not directly comparable without protocol/metric audit |
| Data availability | Upon reasonable request | Limits independent reproducibility |

## 13. Reproduction and verification checklist

- [ ] Extract the full cohort table (demographics, diabetes status, skin color, participant-level sample counts).
- [ ] Resolve the reported age-range inconsistency.
- [ ] Identify unique participants and samples per participant.
- [ ] Extract exact hardware geometry and acquisition details.
- [ ] Determine what the 100 recorded data points per sample represent.
- [ ] Extract the complete normalization and preprocessing sequence.
- [ ] Extract skin-color adjustment definition and estimation procedure.
- [ ] Extract exact model inputs and Ridge Regression implementation.
- [ ] Extract training/test split, validation and evaluation unit.
- [ ] Confirm whether participant overlap exists across partitions.
- [ ] Confirm whether any test-participant calibration is used.
- [ ] Extract units and definitions of “accuracy,” MAE and MSE.
- [ ] Extract full Bland–Altman analysis and CEG counts for finger and wrist separately.
- [ ] Extract the exact statistical tests and null hypotheses.
- [ ] Extract ambient-light and pressure experimental designs.
- [ ] Determine whether external validation exists.
- [ ] Attempt data access through the corresponding author, if appropriate.
- [ ] Re-evaluate using participant-independent splits if data can be obtained.

## 14. Suggested interpretation for our research paper

This paper is a custom-device NIR study that is useful for analyzing **measurement-site comparisons, simple regression models, optical calibration/adjustment, and robustness testing under controlled conditions**.

It should not be directly ranked against free-living smartwatch glucose-estimation studies. The reported finger and wrist prototype metrics need to be interpreted only after resolving metric definitions, target scales, sample partitioning and participant independence.

The most useful methodological lessons for our benchmark are:
1. Treat finger and wrist configurations as distinct measurement systems.
2. Distinguish concurrent estimation from forecasting.
3. Report participant-level rather than only reading-level performance.
4. Treat calibration and skin-color correction as explicit protocols.
5. Test preprocessing and feature adjustments without test-set leakage.
6. Report numerical error and clinical metrics with transparent definitions.
7. Separate stationary controlled sensing from free-living wearable performance.

## 15. Source

- Full article: https://www.nature.com/articles/s41598-024-59744-7
- DOI: https://doi.org/10.1038/s41598-024-59744-7
