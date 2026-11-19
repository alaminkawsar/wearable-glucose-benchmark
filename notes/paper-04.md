# Paper 04 — Zeynali et al. (2025)

## Bibliographic information

**Title:** Non-invasive blood glucose monitoring using PPG signals with various deep learning models and implementation using TinyML  
**Authors:** Mahdi Zeynali, Khalil Alipour, Bahram Tarvirdizadeh, Mohammad Ghamari  
**Journal:** Scientific Reports, 15, 581 (2025)  
**First online:** January 2, 2025  
**DOI:** https://doi.org/10.1038/s41598-024-84265-8  
**Article:** https://www.nature.com/articles/s41598-024-84265-8  
**Full text (PMC):** https://pmc.ncbi.nlm.nih.gov/articles/PMC11698867/  
**GitHub:** https://github.com/mahdinet1/BGL-Estmiation-from-PPG-Signal

---

## 1. Executive summary

Zeynali et al. investigate non-invasive blood glucose level (BGL) estimation from photoplethysmography (PPG) using deep-learning models and an implementation-oriented TinyML approach. The study uses the VitalDB perioperative dataset for development and testing, and the MUST dataset as an additional test dataset. A key methodological contribution is a segmentation scheme that extracts targeted one-second PPG segments around systolic and diastolic peaks from longer PPG recordings.

The authors resample PPG to 100 Hz, filter it using a third-order Butterworth bandpass filter (0.5–8 Hz), and compare 10-second and targeted 1-second segments. The models are designed to run on resource-constrained embedded hardware, with ResNet34 highlighted as the selected model for the 1-second segments.

The paper reports a test RMSE of 19.7 mg/dL and MAE of 14.8 mg/dL for the selected approach and reports 72.6% in Clarke Error Grid Zone A and 25.9% in Zone B for its test results. The paper also evaluates the model on MUST and discusses deployment on an STM32 microcontroller.

**Benchmark relevance:** This paper is a useful case study for PPG-only estimation, signal segmentation, dataset-level split design, patient/case identity, external testing, and the distinction between large numbers of derived segments and independent participants. Its data and code availability make it a candidate for reproduction and protocol auditing.

## 2. Research objectives

The authors aim to:
1. Estimate glucose using non-invasive PPG signals.
2. Compare several deep-learning models.
3. Develop a targeted one-second segmentation method.
4. Compare one-second and ten-second segmentation.
5. Evaluate model performance using quantitative and clinical error metrics.
6. Test an additional dataset (MUST).
7. Implement the selected model for TinyML/embedded deployment.

## 3. Dataset and study design

### 3.1 VitalDB

The paper uses the VitalDB perioperative biosignal database.

Reported dataset context:
- 6,388 cases/records, as described by the paper.
- PPG and blood-glucose reference data are used for the analysis.
- The dataset contains high-resolution biosignals acquired during perioperative care.
- The authors report 35,358 BGL records before their segment expansion.
- A 70% training, 15% validation, 15% testing partition is described.

**Important terminology:** VitalDB's number of cases/records should not automatically be interpreted as the number of unique people. For a reproducible benchmark, the underlying case and patient identifiers, repeated records, and split unit must be checked.

### 3.2 MUST

- The paper describes MUST as a separate dataset used for testing.
- It reports 67 raw PPG recordings in the dataset description, with a subsequent test description involving 23 subjects.
- PPG recordings were collected at 2,175 Hz in the dataset description.
- Reference glucose measurements and participant metadata are provided.

The exact relationship between the 67 recordings and 23 participants should be verified from the source dataset and label files before reproducing participant-level analyses.

### 3.3 Data sources

- VitalDB: https://vitaldb.net/
- MUST/Mendeley Data: the paper references the MUST dataset collected by the digital systems research team at the University of Science and Technology in Mazandaran.
- GitHub implementation: https://github.com/mahdinet1/BGL-Estmiation-from-PPG-Signal

## 4. Signal modality

### PPG
- Primary input: photoplethysmography (PPG).
- VitalDB PPG was originally sampled at high frequency (the paper discusses 500 Hz in its preprocessing description).
- MUST PPG: reported sampling frequency 2,175 Hz in the dataset overview.
- PPG captures blood-volume-related optical signal changes and cardiovascular waveform characteristics.

### Glucose reference
- Reference BGL values are associated with the signal recording times.
- The paper uses reference measurements from the VitalDB data and an additional MUST test dataset.

**Scope:** This is primarily a PPG-based glucose estimation study, not a multimodal wrist-wearable study with accelerometry, EDA, temperature, and other wearable streams. Its relevance to the wearable benchmark is therefore primarily methodological and signal-processing related.

## 5. Preprocessing

### 5.1 Downsampling
- PPG signals are downsampled to 100 Hz to reduce computation while retaining signal information considered useful for estimation.

### 5.2 Timestamp alignment
- The authors associate glucose reference measurements with the PPG sampling timeline.
- A sampling-index-to-time conversion is used to identify the glucose measurement time within the signal.

### 5.3 Signal cropping
- PPG is cropped around the glucose reference timestamp.
- The authors use an interval extending 8 minutes before and 8 minutes after the reference timestamp (16-minute context).
- The paper states that glucose was assumed to remain stable over this interval in the perioperative setting, based on the dataset context.

**Critical task-definition question:** The assumption that a glucose label is representative of a long interval around the timestamp needs careful interpretation. The signal context spans both before and after the label time in the described procedure. This may be suitable for retrospective estimation under their assumption, but it is not equivalent to a prospective forecast. The benchmark should distinguish the two tasks explicitly.

### 5.4 Missing data
- Records without a complete 8-minute interval on both sides of the measurement time were excluded.
- Intervals with missing data throughout the entire 16-minute crop were removed.
- Individual missing points were forward-filled.

### 5.5 Filtering
- Third-order Butterworth bandpass filter.
- Low cutoff: 0.5 Hz.
- High cutoff: 8 Hz.

### 5.6 Signal-quality cleaning
- The authors report using Nerukit2 for signal cleaning.

## 6. Segmentation

The paper compares two segmentation approaches.

### 6.1 Ten-second segments
- The 8-minute interval before the reference timestamp is segmented into 10-second segments.
- The glucose label associated with the reference interval is assigned to the segments.
- The authors describe a substantial increase in samples due to segmentation.

### 6.2 Targeted one-second segments
- The authors identify systolic and diastolic peaks.
- A one-second interval is extracted around selected peaks.
- The segment is selected to capture key PPG waveform components.
- The procedure is intended to preserve informative waveform content while reducing computational requirements.

### 6.3 Segment expansion
- The paper reports 35,358 glucose records.
- Segmentation expands the dataset to 699,072 segments in the described VitalDB workflow.

**Benchmark concern:** This increase is in the number of derived samples, not the number of independent people or cases. If segments derived from the same case are distributed across training, validation, and test sets, performance may be inflated by case-specific and temporal similarities. The actual partitioning unit must be verified.

## 7. Models

The study evaluates three deep-learning models (as described in the model comparison):
- ResNet-based architecture, with ResNet34 highlighted for the selected targeted 1-second method.
- Two additional deep-learning model architectures are compared in the main tables/figures; exact implementation details and model names should be transcribed from the article and repository before a faithful reproduction.

The authors emphasize the computational footprint and embedded compatibility of the selected model.

### TinyML deployment
- The selected model is adapted for deployment on an STM32 microcontroller.
- Embedded deployment is an engineering contribution distinct from demonstrating clinical validity.

## 8. Evaluation protocol

### VitalDB
- 70% training.
- 15% validation.
- 15% testing, according to the paper.
- The paper discusses 6,388 records/cases and 35,358 reference glucose records.
- Segmenting the data generates hundreds of thousands of derived examples.

### MUST
- Used as an additional test dataset.
- The paper reports testing its ResNet34 one-second model on MUST.
- Dataset description reports 67 raw PPG recordings and a test description refers to 23 subjects.

### Protocol details to verify
1. Are splits made at the patient level, case level, recording level, or segment level?
2. If multiple cases belong to the same patient, can patient identity cross partitions?
3. Are overlapping or adjacent PPG segments derived from the same source recording distributed across partitions?
4. Are all preprocessing steps, including signal cleaning, normalization, peak detection and feature transformations, fit or configured without test data?
5. Is the MUST test truly external to all model selection and hyperparameter decisions?
6. Does the long 16-minute crop use information after the glucose timestamp? If so, this is retrospective estimation, not prospective forecasting.

These are important audit questions. They should be resolved from the article, dataset IDs, code and supplemental material before any claims of leakage or generalization are made.

## 9. Metrics

The paper reports:
- RMSE (mg/dL)
- MAE (mg/dL)
- MSE
- MARD
- R²
- Clarke Error Grid Analysis (CEGA)

The metrics are useful for quantitative and clinical-risk-oriented comparisons, but none independently establishes clinical readiness.

## 10. Reported results

### 10.1 VitalDB test results
The paper reports for its selected approach:
- RMSE: 19.7 mg/dL.
- MAE: 14.8 mg/dL.
- Clarke Error Grid Zone A: 72.6%.
- Zone B: 25.9%.

The paper compares 1-second and 10-second segmentation and presents results for multiple model architectures. The precise model-by-segmentation metrics should be extracted from Tables 4 and 5 in the article before adding them to a benchmark results database.

### 10.2 MUST results
- The paper evaluates its selected ResNet34 one-second model on MUST.
- Quantitative results and CEGA percentages are reported in Tables 6 and 7.
- Exact MUST values should be transcribed directly from the original tables during reproduction rather than inferred from text or figures.

### 10.3 Interpretation
The authors argue that targeted one-second segmentation and model optimization improve accuracy and computational efficiency, and emphasize deployment on a microcontroller.

Performance claims should be interpreted alongside:
- The exact data partitioning unit.
- The potential dependence between derived segments.
- The retrospective 16-minute signal context.
- The limited range and distribution of reference glucose values.
- The distinction between test-set evaluation and truly external validation.

## 11. Strengths

1. Publicly available article and implementation repository.
2. Investigates signal segmentation as a methodological factor.
3. Compares 1-second and 10-second segment strategies.
4. PPG-only study with relatively direct signal-processing focus.
5. Uses multiple quantitative and clinical metrics.
6. Includes an additional dataset (MUST) for testing.
7. Addresses embedded implementation and computational efficiency.
8. Makes deployment constraints part of the research question.

## 12. Methodological concerns and audit hypotheses

These are hypotheses to investigate, not confirmed errors.

### 12.1 Split unit and patient/case leakage
The paper describes 6,388 VitalDB cases/records and a split of 70/15/15. It is important to establish whether partitioning is patient-, case-, recording-, or segment-level.

**Benchmark experiment:** Compare segment-level random splits against grouped splits by patient/case and against chronological evaluation where timestamps support it.

### 12.2 Segment dependence
Hundreds of thousands of segments are derived from a much smaller number of original glucose records and recordings. Adjacent segments from the same record can share physiological and acquisition characteristics.

**Benchmark experiment:** Ensure all segments from a source record/case stay in the same partition and quantify performance under stricter grouping.

### 12.3 Retrospective context
The described 16-minute crop includes 8 minutes before and after the reference time. If post-label data are included, the task is retrospective estimation and cannot be described as forecasting.

**Benchmark experiment:** Compare retrospective estimation with strictly pre-label windows and prospective prediction horizons.

### 12.4 Label stability assumption
The paper assumes glucose remains stable during the perioperative crop. That assumption should be assessed using the available CGM/clinical context and sensitivity analysis.

**Benchmark experiment:** Vary the window length and evaluate label/time alignment; exclude records with substantial glucose changes where data permit.

### 12.5 External test set
MUST is described as a separate test dataset, which is valuable. However, external testing is only independent if data and participant overlap are absent and model selection does not use the external results.

**Benchmark experiment:** Document data provenance, participants, acquisition conditions, preprocessing compatibility, and whether the external test influenced model choices.

### 12.6 Clinical-population and setting differences
VitalDB is perioperative and differs from free-living wearable collection. Signal quality, physiological state, glucose distributions, devices and reference timing can differ substantially.

**Benchmark experiment:** Avoid treating perioperative PPG results as evidence of free-living smartwatch performance.

### 12.7 Metrics and clinical claims
CEGA is useful but should be accompanied by error distributions, participant/case-level uncertainty, glucose-range analysis, and robust validation.

### 12.8 TinyML versus accuracy validation
Embedded feasibility is a separate dimension from generalization and clinical performance.

**Benchmark experiment:** Report computational footprint separately from estimation accuracy and validation quality.

## 13. Relevance to wearable-glucose-benchmark

Paper 04 is less directly aligned with multimodal wrist-wearable estimation than Papers 01 and 02, but is useful for studying PPG preprocessing and evaluation design.

It contributes:
- PPG-only signal estimation.
- Resampling and filtering.
- Peak-aware segmentation.
- Label/window alignment.
- Segment expansion and dependence.
- Grouping by source patient/case.
- Retrospective versus prospective task distinction.
- External testing.
- Embedded deployment.

## 14. Proposed benchmark experiments inspired by this paper

- **B4-E1:** Reproduce 10-second and targeted 1-second segmentation.
- **B4-E2:** Compare random segment split against patient/case-grouped split.
- **B4-E3:** Prevent source recordings and adjacent segments from crossing partitions.
- **B4-E4:** Compare retrospective 16-minute context against pre-label-only windows.
- **B4-E5:** Evaluate different window lengths around the reference timestamp.
- **B4-E6:** Test sensitivity to PPG filtering and resampling.
- **B4-E7:** Evaluate a true external dataset without using it for model selection.
- **B4-E8:** Report patient/case-level metrics and uncertainty.
- **B4-E9:** Compare simple PPG features and classical baselines against deep learning.
- **B4-E10:** Separate computational/deployment metrics from model validation metrics.

## 15. Reproducibility checklist

- [ ] Obtain VitalDB cases and the exact glucose/PPG variables used.
- [ ] Identify the precise VitalDB record and patient IDs.
- [ ] Verify the 70/15/15 partitioning unit.
- [ ] Verify whether patient or case identities cross partitions.
- [ ] Reproduce resampling to 100 Hz.
- [ ] Reproduce the 0.5–8 Hz third-order Butterworth filter.
- [ ] Reproduce the 16-minute crop and its timestamp alignment.
- [ ] Reproduce forward filling and record exclusion rules.
- [ ] Reproduce 10-second segmentation.
- [ ] Reproduce targeted one-second peak segmentation.
- [ ] Reproduce the three model architectures.
- [ ] Extract all model-by-segmentation metrics from original tables.
- [ ] Reproduce CEGA.
- [ ] Verify MUST test data provenance and independence.
- [ ] Reproduce MUST metrics.
- [ ] Audit whether the test set was used for model selection.
- [ ] Document embedded deployment settings separately.

## 16. Research assessment

**Role in the benchmark:** A useful PPG signal-processing and evaluation case study, especially for investigating the effects of segment generation, grouping, retrospective context, and external testing.

The paper's large derived-sample count should not be interpreted as a large independent sample size without inspecting the split unit and source-record structure. Its retrospective context and perioperative setting also mean it should be categorized separately from free-living, future-glucose forecasting and wrist-wearable studies.

## 17. References

- Article: https://www.nature.com/articles/s41598-024-84265-8
- Full text: https://pmc.ncbi.nlm.nih.gov/articles/PMC11698867/
- DOI: https://doi.org/10.1038/s41598-024-84265-8
- GitHub: https://github.com/mahdinet1/BGL-Estmiation-from-PPG-Signal
- VitalDB: https://vitaldb.net/
