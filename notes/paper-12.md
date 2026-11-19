# Paper 12 — Aziz et al. (2023)

## Citation
Aziz, S., Ahmed, A., Abd-Alrazaq, A., Qidwai, U., Farooq, F., & Sheikh, J. (2023). **Estimating Blood Glucose Levels Using Machine Learning Models with Non-Invasive Wearable Device Data.** *Studies in Health Technology and Informatics*, 305, 283–286. https://doi.org/10.3233/SHTI230484

- PubMed: https://pubmed.ncbi.nlm.nih.gov/37387018/
- DOI: https://doi.org/10.3233/SHTI230484
- PMID: 37387018
- Publication date: 29 June 2023

> **Verification note:** This note is grounded in the PubMed abstract and bibliographic record. The detailed article text was not independently extracted in this session. Specific devices, sensor modalities, target units, preprocessing, model names, validation design and metric definitions remain unverified and are explicitly flagged below.

## 1. Overview

The paper investigates whether linear and nonlinear machine-learning models can estimate blood glucose levels (BGL) using non-invasive wearable-device data.

The authors frame the study around exploring relationships between wearable-derived digital metrics and markers of glycemic health. The dataset is described as including digital metrics and diabetic-status information collected using traditional methods.

The study reports data from **13 participants**, separated into two groups described in the abstract as young and adult. The experimental workflow includes data collection, feature engineering, model selection/development and evaluation.

### Abstract-reported performance
- RMSE: **0.181–0.271**
- MAE: **0.093–0.142**

The abstract does not specify the units or scale for these metrics. They must not be interpreted as mg/dL, mmol/L, normalized units or percentage error without verification.

## 2. Research objective and contribution

### Objective
Investigate the accuracy of linear and nonlinear machine-learning models for estimating BGL from non-invasive wearable-device data.

### Contributions described in the abstract
1. Use wearable-device data to investigate BGL estimation.
2. Combine wearable digital metrics with information related to diabetic status collected through traditional means.
3. Explore feature engineering and machine-learning model development.
4. Compare linear and nonlinear modeling approaches.
5. Report RMSE and MAE ranges.

### Scope
This appears to be an early-stage, small-sample feasibility study. The 13-participant sample is particularly important when interpreting model performance and generalizability.

The exact target definition is not yet established: continuous regression, a transformed/normalized target, or a prediction task involving another definition of glucose-related status must be verified from the article.

## 3. Participants and dataset

### Confirmed from abstract
- **13 participants.**
- Participants were divided into two groups described as young and adult.
- Wearable-device data were collected.
- The dataset also included diabetic-status information collected using traditional means.

### Details to verify
- Exact age-group definitions and group sizes.
- Age, sex, BMI and demographic characteristics.
- Diabetes status and distribution.
- Inclusion/exclusion criteria.
- Recruitment and collection setting.
- Number of measurements per participant.
- Total number of rows, windows or instances.
- Participant-level glucose range and distribution.
- Whether repeated measurements were collected.
- Whether each participant contributed equally.
- Missing-data patterns.

### Sample-size interpretation
The 13 participants—not the number of windows, feature rows or repeated observations—are the key initial unit for judging the breadth of the cohort.

If multiple records per participant were used, the effective independent sample size may be substantially smaller than the number of rows used for modeling.

## 4. Wearable sensors and data modalities

The abstract identifies non-invasive wearable-device data but does not specify device models or sensor modalities.

### Verify in the full paper
- Wearable device(s) and manufacturer/model.
- Sensor modalities (e.g., PPG, heart rate, activity, accelerometry, temperature or other signals).
- Sampling rates.
- Signal collection duration.
- Device placement.
- Whether data were recorded continuously or during discrete sessions.
- Whether digital metrics are raw sensor signals, device-derived metrics or summary statistics.
- Whether signals from multiple devices are combined.
- Data synchronization and timestamp handling.

**Do not assume PPG was used solely because the research area concerns wearable glucose estimation.**

## 5. Reference glucose and target construction

The abstract mentions diabetic-status information collected through traditional means, but does not describe the reference glucose protocol or target construction in detail.

### Verify
- Reference glucose instrument or laboratory method.
- Whether the target is continuous glucose concentration.
- Glucose units.
- Measurement frequency.
- Timing relative to wearable acquisition.
- Whether reference readings were concurrent with sensor data.
- Whether diabetic status is a predictor, a target, a cohort descriptor or a grouping variable.
- How glucose labels are aligned to wearable records.
- Whether the target was transformed or normalized.
- Whether the study is estimation, classification or forecasting in practice.

This distinction is essential because the title and abstract refer to BGL estimation, but the precise label construction and temporal task cannot be inferred from the abstract alone.

## 6. Feature engineering

Feature engineering is identified as part of the experimental workflow.

### Details to extract
- Raw versus engineered features.
- Feature list and definitions.
- Signal-level and demographic/context features.
- Aggregation windows.
- Feature scaling.
- Feature selection.
- Correlation analysis.
- Whether demographic and diabetic-status variables are included as model inputs.
- Whether feature selection is performed before splitting or within training folds.
- Whether feature engineering uses any information from target or test records.

### Benchmark relevance
Feature engineering and feature selection can be particularly influential in a small dataset. Any data-dependent step must be considered part of the model pipeline and should be fitted within the training partition.

## 7. Models

The abstract states that both **linear and nonlinear models** were investigated.

### Model details to verify
- Exact model names.
- Linear-model baselines.
- Nonlinear-model families.
- Hyperparameter settings.
- Feature sets used for each model.
- Training procedure.
- Model-selection criteria.
- Tuning search.
- Regularization.
- Whether models are participant-specific or population-level.
- Whether model comparisons use identical data partitions.

### Benchmark interpretation
The paper may provide a useful small-cohort baseline comparison, but model names and performance should be extracted from the full text before reproducing or incorporating individual model results into the benchmark.

## 8. Evaluation and validation protocol

The abstract does not provide the evaluation protocol.

### Verify
- Train/test split or cross-validation method.
- Number of folds.
- Split unit: row, window, recording, session or participant.
- Whether participants overlap between training and test sets.
- Whether data are stratified by age group, diabetic status or glucose.
- Whether evaluation is chronological.
- Whether an independent test set exists.
- Whether hyperparameter tuning uses a validation set or nested CV.
- Whether normalization, imputation and feature selection are train-only.
- Whether repeated or overlapping windows cross partition boundaries.

### Critical participant-independence audit
With only 13 participants, sample-level splitting could substantially inflate performance if records from the same participant occur in both training and testing.

**Benchmark action:** identify participant-level grouping and, if the data are available, compare:
- Original protocol.
- Participant-grouped evaluation.
- Leave-one-subject-out (LOSO), if feasible.
- Chronological within-participant evaluation, if the task supports it.

Results from these protocols answer different questions and should not be presented as interchangeable.

## 9. Reported results

### Abstract-reported metrics
| Metric | Reported range | Units/scale |
|---|---:|---|
| RMSE | 0.181–0.271 | Not specified in abstract |
| MAE | 0.093–0.142 | Not specified in abstract |

The abstract says that linear and nonlinear models both achieved high accuracy but does not provide individual model-to-metric mapping.

### Extract from full text
- Model-specific RMSE.
- Model-specific MAE.
- MSE, if reported.
- R², if reported.
- MARD, if reported.
- Units and scale for all metrics.
- Per-model feature sets.
- Per-model validation protocol.
- Participant-level results.
- Results by age group.
- Results by diabetic status.
- Glucose-range performance.
- Confidence intervals or variability.
- Statistical comparison between models.

### Interpretation caution
The numerical results cannot be compared directly with studies reporting errors in mg/dL or mmol/L until the target scale and metric definitions are reconciled.

A small RMSE/MAE may reflect a normalized or transformed target, rather than a low absolute glucose error in clinically interpretable units.

## 10. Methodological audit

These are questions and hypotheses to verify—not confirmed flaws.

### A. Small participant cohort
- 13 participants is a small cohort for generalizable population-level modeling.
- The number of independent participants is a more relevant starting point than the number of engineered feature rows.

**Benchmark action:** report participant count and participant-level results; use participant-aware uncertainty estimates.

### B. Participant leakage
If participants contribute multiple records, a random record-level split may put the same person in training and test sets.

**Benchmark action:** verify the split unit; compare grouped splits if data are accessible.

### C. Age-group design
The abstract describes two groups as young and adult, but their definitions and sample sizes are not established.

**Benchmark action:** extract group definitions and assess whether group membership affects the model, sampling, split or reporting. Avoid interpreting group comparisons without adequate participant counts.

### D. Diabetic-status information
The abstract indicates that diabetic-status information collected using traditional means is included in the dataset.

**Audit questions:**
- Is status used as a model input?
- Is it used to stratify or characterize participants?
- Is it a target or predictor?
- Does the model rely on status information to estimate glucose?
- Are performance results reported with and without status?

**Benchmark action:** if status is an input, compare wearable-only, status-only and combined-feature models where the data permit.

### E. Target scale and metric ambiguity
The abstract's RMSE and MAE values are reported without units or target-scale context.

**Benchmark action:** verify the target units, transformations and exact metric calculation. Do not convert or compare until confirmed.

### F. Feature-selection and preprocessing leakage
In a small sample, preprocessing and feature selection can have a large effect.

**Benchmark action:** ensure imputation, scaling, feature selection and any learned preprocessing are performed only on training data (or within each CV training fold).

### G. Model-selection bias
The study investigates model selection/development, but the abstract does not explain how performance was selected or validated.

**Benchmark action:** identify whether model/hyperparameter selection was separate from final evaluation. If feasible, use nested CV or a dedicated validation set.

### H. Temporal task definition
The abstract does not establish whether estimates are concurrent with glucose references or forecast future glucose.

**Benchmark action:** extract signal-window timestamps, target timestamps, label alignment and any forecast horizon.

### I. Demographic/generalizability
A 13-person sample and two age groups may limit conclusions about broader populations.

**Benchmark action:** extract demographic and health characteristics, and report the limitations of the cohort rather than extrapolating to diverse populations.

### J. Clinical interpretability
RMSE and MAE alone do not establish clinical acceptability.

**Benchmark action:** if glucose is continuous, consider error-grid analysis, glucose-range stratification, bias, participant-level errors and uncertainty—but only if compatible with the target and data.

## 11. Reproduction checklist

- [ ] Extract exact participant count and demographics.
- [ ] Extract young/adult group definitions and group sizes.
- [ ] Extract diabetic-status distribution.
- [ ] Extract wearable-device models.
- [ ] Identify sensor modalities and sampling rates.
- [ ] Extract collection duration and measurement protocol.
- [ ] Extract reference glucose instrument and units.
- [ ] Reconstruct target-label alignment.
- [ ] Extract the complete feature list and feature engineering procedure.
- [ ] Extract exact linear and nonlinear models.
- [ ] Extract model-specific results.
- [ ] Extract preprocessing and normalization.
- [ ] Verify train/test or CV split.
- [ ] Verify participant overlap.
- [ ] Verify preprocessing and feature-selection fold-locality.
- [ ] Verify hyperparameter selection and final evaluation independence.
- [ ] Confirm metric units and target scale.
- [ ] Extract participant-level performance.
- [ ] Extract group-specific results, if reported.
- [ ] Determine whether forecasting is actually performed.
- [ ] Compare against simple baselines, if data are accessible.

## 12. Benchmark relevance

| Dimension | Paper characteristics | Benchmark relevance |
|---|---|---|
| Study type | Small-cohort feasibility/model comparison | Useful methodological contrast |
| Participants | 13 | Small independent cohort |
| Groups | Young and adult (definitions to verify) | Group structure audit |
| Inputs | Non-invasive wearable digital metrics | Modalities not specified in abstract |
| Additional information | Diabetic status from traditional means | Input/stratification role to verify |
| Feature engineering | Included in workflow | Pipeline leakage audit |
| Models | Linear and nonlinear approaches | Baseline comparison |
| Task | BGL estimation, exact target definition to verify | Task-definition audit |
| RMSE | 0.181–0.271 | Scale and units unverified |
| MAE | 0.093–0.142 | Scale and units unverified |
| Evaluation | Not specified in abstract | Split and validation audit |
| Reproducibility | Abstract-level details insufficient | Full text required |

## 13. Suggested interpretation for our research paper

Aziz et al. (2023) provides an early feasibility example of applying linear and nonlinear machine-learning models to non-invasive wearable-device data for BGL estimation. The study reports 13 participants and RMSE/MAE ranges, but the small cohort and the lack of evaluation details in the abstract make the participant-level generalizability and reproducibility protocol important points for further examination.

For our benchmark, this paper is especially relevant to:
1. The distinction between the number of observations and the number of independent participants.
2. Participant-aware evaluation in small wearable datasets.
3. The role of demographic or diabetic-status variables alongside wearable features.
4. Fold-local feature engineering and preprocessing.
5. Reconciling metric units and transformed targets before comparing reported errors.
6. Separating feasibility results from unseen-participant generalization.

Until the split unit, target scale and full methods are verified, the reported metrics should be recorded as abstract-level findings rather than directly comparable benchmark results.

## 14. Source

- PubMed: https://pubmed.ncbi.nlm.nih.gov/37387018/
- DOI: https://doi.org/10.3233/SHTI230484
