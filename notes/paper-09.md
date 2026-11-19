# Paper 09 — Shi et al. (2023)

## Citation
Shi, B., Dhaliwal, S. S., Soo, M., Chan, C., Wong, J., Lam, N. W. C., Zhou, E., Paitimusa, V., Loke, K. Y., Chin, J., Chua, M. T., Liaw, K. C. S., Lim, A. W. H., Insyirah, F. F., Yen, S.-C., Tay, A., & Ang, S. B. (2023). **Assessing Elevated Blood Glucose Levels Through Blood Glucose Evaluation and Monitoring Using Machine Learning and Wearable Photoplethysmography Sensors: Algorithm Development and Validation.** *JMIR AI*, 2, e48340. https://doi.org/10.2196/48340

- PubMed: https://pubmed.ncbi.nlm.nih.gov/38875549/
- Full text: https://pmc.ncbi.nlm.nih.gov/articles/PMC11041426/
- DOI: https://doi.org/10.2196/48340
- PMCID: PMC11041426
- PMID: 38875549
- Published: 27 October 2023

> Scope note: This note uses the PubMed abstract and the accepted/published metadata available in this session. PMC full-text access was blocked during extraction. Detailed feature lists, exact device specifications, data split mechanics, and full tables are therefore flagged for verification rather than inferred.

## 1. Overview

The study develops a machine-learning system to detect **elevated blood glucose (defined as ≥7.8 mmol/L)** using wearable photoplethysmography (PPG) and participant demographic information.

It is a **binary/threshold-based elevated-glucose detection and screening study**, rather than continuous glucose regression or future glucose forecasting.

The authors recruit 500 participants, collect glucometer reference measurements and wrist-worn wearable signals, engineer PPG features, and evaluate multiple machine-learning models using 10-fold cross-validation.

The best reported classifier is a radial-basis-function (RBF) Support Vector Machine (SVM).

## 2. Research objective

### Primary objective
Develop a non-invasive machine-learning approach to recognize elevated blood glucose levels (≥7.8 mmol/L) and assess potential diabetes risk using repeated measurements.

### Contribution
- Wearable wrist-based PPG acquisition in a large screening cohort.
- Reference measurements using a conventional glucometer.
- Measurement protocol involving sugary-drink consumption for most participants.
- Extensive PPG feature engineering.
- Selection of features sensitive to glucose changes.
- Machine-learning classification of elevated glucose.
- Explainable AI analysis of feature contributions.

## 3. Task definition

### Target
- **Elevated glucose:** ≥7.8 mmol/L.
- The task is classification/detection, not glucose-value regression.

### Important interpretation
The threshold of 7.8 mmol/L defines the study's target classification. This does not automatically mean the model diagnoses diabetes; elevated glucose at a measurement time and a clinical diagnosis of diabetes are distinct concepts.

### Benchmark implications
This paper's performance metrics (accuracy, sensitivity, specificity, precision and F-score) cannot be directly compared to glucose-regression metrics such as RMSE, MAE, MARD or R².

It is a related but distinct benchmark task.

## 4. Participants and cohort

The study reports:
- **500 participants recruited.**
- Mean age: **38.73 years**.
- Age standard deviation: **10.61 years**.
- Mean BMI: **24.4 kg/m²**.
- BMI standard deviation: **5.1 kg/m²**.
- Study location: KK Women's and Children's Hospital, Singapore.

### Cohort limitations to investigate
- Distribution of elevated and non-elevated glucose labels.
- Distribution of participants across age, sex, BMI and clinical risk.
- Whether each participant contributed one or multiple labeled records.
- Whether participant demographics were included as predictors.
- How repeated measurements from the same participant were represented in the dataset.

The sample count is 500 participants, but the exact number of records used for model development and the unit of each fold must be confirmed in the full paper.

## 5. Measurement protocol

### Reference glucose
- Conventional glucometer: **Accu-Chek Performa**.
- Glucometer readings are used as ground truth.

### Wearable sensing
- Wrist-worn wearable PPG sensor.
- The study uses wearable-derived PPG measurements along with demographic predictors.

### Sugary-drink protocol
For most participants, measurements were obtained before and after consuming **75 g of sugary drink**.

This protocol creates a glucose-response context and may provide observations under differing glucose states within participants.

### Critical repeated-measurement question
The study explicitly refers to evaluation of diabetic risk based on repeated measurements. The precise relationship between participants, pre/post-drink measurements, and CV folds is therefore especially important.

**Verify:**
- Exact number and timing of measurements per participant.
- Whether pre- and post-drink observations are separate rows.
- How reference labels were assigned.
- Whether post-drink timing was standardized.
- Whether all participants underwent the same protocol.
- Whether repeated measurements were grouped by participant during cross-validation.

## 6. Sensor and data

### Wearable modality
- Wrist-worn PPG.

### Predictors
The abstract states that machine-learning models were trained using:
- Demographic data.
- Critical features extracted from PPG measurements.

The authors report extensive feature engineering and feature selection to identify PPG features sensitive to glucose changes.

**Not independently verified here:**
- Device brand/model.
- LED wavelengths.
- Sampling frequency.
- PPG channel configuration.
- Recording duration.
- Exact feature names and dimensions.
- Signal preprocessing, filtering and segmentation.

These should be extracted from the full article before attempting reproduction.

## 7. Feature engineering and selection

The authors describe:
- Extensive feature engineering on PPG sensor data.
- Identification/selection of features sensitive to glucose changes.
- Subsequent explainable AI analysis to understand feature contributions.

The exact features, selection algorithm and XAI method were not independently verified in this session.

### Verification checklist
- [ ] Extract full feature list and definitions.
- [ ] Extract waveform segmentation and cycle extraction details.
- [ ] Extract signal preprocessing.
- [ ] Identify feature selection algorithm and selection thresholds.
- [ ] Identify whether selection is performed within training folds.
- [ ] Extract the XAI method and its interpretation.
- [ ] Determine whether demographics are included in all model comparisons or only some.
- [ ] Determine how PPG and demographic predictors are combined.

## 8. Models and classification pipeline

The paper reports multiple machine-learning models, with **RBF-kernel Support Vector Machine (SVM)** performing best.

### Best reported classifier
- RBF SVM.

### Model inputs
- Demographic information.
- Critical engineered PPG features.

### Evaluation
- 10-fold cross-validation.

The exact model list, hyperparameters, tuning process and preprocessing pipeline should be extracted from the full text.

### Model comparison questions
- Which algorithms were evaluated?
- Were demographic-only models evaluated?
- Were PPG-only models evaluated?
- Were combined PPG+demographic models evaluated?
- Were all model inputs and partitions identical?
- Was hyperparameter selection nested within CV or otherwise separated from evaluation?

## 9. Evaluation protocol

The abstract states that models were evaluated using **10-fold cross-validation**.

The precise fold assignment unit is not established by the abstract.

### Critical audit: participant independence

If multiple measurements were collected from the same participant and randomly assigned to folds, observations from that participant could occur in both training and test folds.

This could inflate performance for a task that aims to screen new people.

### Questions to verify
1. Are folds split at the participant level?
2. Are pre- and post-drink observations from the same person kept together?
3. If participants contributed multiple observations, are all records grouped in one fold?
4. Is feature selection performed within each training fold?
5. Is preprocessing fitted independently on training data?
6. Is hyperparameter tuning separated from the outer evaluation?
7. Are all folds stratified by the elevated-glucose class?
8. Are results averaged across folds or pooled across all out-of-fold predictions?

### Benchmark action
Compare:
- Random record-level CV (if reproducing the original protocol).
- Participant-grouped CV.
- Participant-grouped CV with stratification where feasible.

If the screening goal is to identify elevated glucose in unseen people, participant-disjoint evaluation is particularly important.

## 10. Reported results

### Best model: RBF-kernel SVM

The paper reports:
- **Accuracy: 84.7%**
- **Sensitivity: 81.05%**
- **Specificity: 88.3%**
- **Precision: 87.51%**
- **Geometric mean: 84.54%**
- **F-score: 84.03%**

These are the authors' reported 10-fold CV results.

### Metric interpretation
- **Accuracy:** overall classification correctness, affected by class prevalence.
- **Sensitivity:** proportion of elevated-glucose cases identified.
- **Specificity:** proportion of non-elevated cases identified.
- **Precision:** proportion of positive predictions that are elevated.
- **F-score:** combines precision and recall.
- **Geometric mean:** balances sensitivity and specificity.

### Important missing context to verify
- Class distribution.
- Per-fold results.
- Confidence intervals.
- Participant-level performance.
- Whether metrics are calculated from pooled out-of-fold predictions or fold averages.
- Threshold selection for classifier outputs.
- Performance at different operating thresholds.
- Calibration of predicted probabilities.

## 11. Explainability

The study reports use of an explainable AI approach to understand contributions of selected features to predictions.

### Relevance
Explainability can help identify whether the classifier is:
- Using physiologically plausible PPG features.
- Relying on demographic variables.
- Exploiting measurement-condition or participant-specific artifacts.
- Relying on correlated features.

### Verification checklist
- [ ] Identify the XAI method.
- [ ] Extract the most important features.
- [ ] Determine whether explanation analysis is global, local or both.
- [ ] Verify whether feature attribution is calculated on training, validation or test observations.
- [ ] Assess stability of feature rankings across CV folds.
- [ ] Check for correlated features that may make attribution unstable.
- [ ] Examine whether selected features have plausible physiological interpretations.

Explainability does not establish causality or physiologic specificity.

## 12. Methodological audit

These are **audit hypotheses and verification questions**, not confirmed defects.

### A. Repeated-measurement leakage
- Most participants undergo measurements before and after sugary-drink consumption.
- Repeated measurements from a participant may be correlated.
- If records are split independently, a model could learn participant-specific signal characteristics.

**Benchmark action:** test participant-grouped CV and report performance on unseen participants.

### B. Temporal and protocol information
- The pre/post-drink protocol may encode time since glucose challenge.
- Measurements at different time points may not be independent.
- The target threshold could be strongly affected by measurement timing.

**Benchmark action:** document measurement time, protocol, reference-label alignment and whether timing is a predictor.

### C. Classification versus continuous estimation
- The model predicts elevated glucose status at a threshold.
- It does not establish continuous glucose estimation accuracy.
- Threshold detection performance depends on class prevalence and the distribution of values near the threshold.

**Benchmark action:** classify this study separately from regression/forecasting studies in the paper synthesis.

### D. Participant and demographic dependence
- Demographics are included as predictors.
- BMI, age or other characteristics could contribute to the prediction.
- A classifier could partly recognize risk profiles rather than estimate glucose from PPG.

**Benchmark action:** compare:
1. Demographic-only.
2. PPG-only.
3. PPG + demographics.

### E. Feature selection leakage
- Selected PPG features are described as glucose-sensitive.
- The selection process should be evaluated relative to CV folds.

**Benchmark action:** implement fold-local feature selection and compare with global selection only as a reproduction of the original procedure if justified.

### F. Class imbalance
- Elevated-glucose prevalence is important for interpreting accuracy, precision and sensitivity.
- Accuracy alone may be misleading if the target classes are imbalanced.

**Benchmark action:** report prevalence, confusion matrices, sensitivity, specificity, precision, F1, G-mean, balanced accuracy and AUC where appropriate.

### G. Model threshold
- The classification threshold determines sensitivity and specificity.
- A threshold optimized on evaluation folds can produce optimistic performance.

**Benchmark action:** define threshold selection within training/validation data and report operating-point sensitivity/specificity.

### H. Calibration and clinical screening utility
- Classification metrics do not show whether probability estimates are calibrated.
- Screening usefulness depends on false-negative/false-positive tradeoffs.

**Benchmark action:** evaluate calibration and threshold-specific performance, if probabilities are available, and frame the study as screening rather than diagnosis.

### I. External validation
- 10-fold CV is internal evaluation.
- The abstract does not report external validation.

**Benchmark action:** distinguish internal cross-validation from independent cohort/device validation.

## 13. Reproduction checklist

- [ ] Extract complete cohort characteristics.
- [ ] Extract the number of observations and measurement occasions per participant.
- [ ] Extract device brand/model, wavelength, sampling rate and recording duration.
- [ ] Extract pre/post-drink protocol and timing.
- [ ] Extract glucose label definition and class distribution.
- [ ] Extract signal preprocessing and PPG feature list.
- [ ] Extract feature selection procedure.
- [ ] Extract model list and hyperparameter search.
- [ ] Extract CV fold assignment and participant grouping.
- [ ] Extract per-fold metrics and averaging method.
- [ ] Extract XAI methodology and top feature rankings.
- [ ] Evaluate demographic-only, PPG-only and combined models.
- [ ] Evaluate participant-grouped CV.
- [ ] Evaluate calibration and threshold selection.
- [ ] Extract or reproduce confusion matrices.
- [ ] Determine whether external validation was performed.

## 14. Relevance to the wearable-glucose benchmark

| Dimension | Paper characteristics | Benchmark relevance |
|---|---|---|
| Task | Elevated-glucose classification | Separate from continuous glucose regression |
| Target | ≥7.8 mmol/L | Threshold-based screening |
| Cohort | 500 participants recruited | Larger screening cohort |
| Population | Mean age 38.73 ± 10.61; BMI 24.4 ± 5.1 | Cohort context |
| Reference | Accu-Chek Performa | Glucometer ground truth |
| Protocol | Most participants measured before/after 75 g sugary drink | Repeated measurements and temporal dependence |
| Wearable | Wrist-worn PPG | Direct wearable relevance |
| Inputs | PPG features + demographics | Modality/demographic ablation |
| Feature engineering | Extensive, selected glucose-sensitive features | Feature selection and reproducibility |
| Model | RBF SVM best | Classical ML benchmark |
| Evaluation | 10-fold CV | Split unit needs verification |
| Accuracy | 84.7% | Classification metric |
| Sensitivity | 81.05% | Screening operating point |
| Specificity | 88.3% | Screening operating point |
| Precision | 87.51% | Class prevalence dependent |
| G-mean | 84.54% | Balanced classification measure |
| F-score | 84.03% | Classification summary |
| Explainability | XAI feature contribution analysis | Interpretability and feature audit |
| External validation | Not established by abstract | Generalization gap to investigate |

## 15. Suggested interpretation for our research paper

This study is an important **wearable PPG-based elevated-glucose screening study** with a comparatively large cohort and a repeated-measurement glucose challenge protocol.

Its task should be separated from continuous glucose-estimation and forecasting papers. It is valuable for the benchmark's broader methodological discussion—especially repeated-measurement dependence, participant-level CV, feature selection, demographic confounding, class prevalence and evaluation of screening operating points.

The central methodological question is whether the reported 10-fold cross-validation evaluates performance on genuinely unseen participants or on measurements from participants already represented in training.

The most useful benchmark experiments are:
1. Participant-grouped evaluation.
2. PPG-only vs demographic-only vs combined inputs.
3. Fold-local feature selection.
4. Class-imbalance and threshold sensitivity.
5. Per-participant classification performance.
6. Probability calibration and screening operating-point analysis.

## 16. Source

- PubMed: https://pubmed.ncbi.nlm.nih.gov/38875549/
- PMC full text: https://pmc.ncbi.nlm.nih.gov/articles/PMC11041426/
- DOI: https://doi.org/10.2196/48340
- Accepted manuscript: https://preprints.jmir.org/preprint/48340/accepted
