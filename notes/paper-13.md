# Paper 13 — Mohamed et al. (2025)

## Citation
Mohamed, Y. M., Mancera, J., Brandenberg, A., Fischli, S., & Havranek, M. M. (2025). **Personalized machine learning models for noninvasive hypoglycemia detection in people with type 1 diabetes using a smartwatch: Insights into feature importance during waking and sleeping times.** *PLOS ONE*, 20(6), e0325956. https://doi.org/10.1371/journal.pone.0325956

- Full text: https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0325956
- PMID: 40560992
- DOI: https://doi.org/10.1371/journal.pone.0325956
- GitHub: https://github.com/YMM-0/smartwatch_hypo_detection.git
- Data: https://zenodo.org/records/15309060
- Published: 25 June 2025

## 1. Overview

This study investigates personalized, non-invasive detection of hypoglycemic events in adults with type 1 diabetes using physiological and activity data collected from a consumer-grade smartwatch.

The authors use Apple Watch Series 8 predictor data and a blinded Dexcom G6 continuous glucose monitor (CGM) to define hypoglycemia outcomes. They build personalized XGBoost models using each participant's own data, with separate models for waking and sleeping periods.

The outcome is **binary event detection**, not continuous glucose estimation:
- Positive class: glucose below **4 mmol/L for at least 15 minutes**.
- Negative class: no qualifying hypoglycemic event.

The study is particularly relevant to personalized evaluation, time-series split design, temporal leakage prevention, event detection and feature interpretability.

## 2. Objective and contributions

### Objective
Explore whether consumer-grade smartwatch physiological and activity data can support noninvasive hypoglycemia detection in adults with T1D, and investigate whether feature importance differs between waking and sleeping states.

### Contributions
1. Personalized hypoglycemia-detection models.
2. Separate daytime and nighttime models.
3. A custom day/night-based train/test procedure intended to avoid random time-series leakage.
4. XGBoost-based binary classification.
5. SHAP-based feature-importance analysis.
6. Evaluation of detection during events and within the first 15 minutes.

## 3. Participants and study design

### Recruitment
- **18 adults with T1D** recruited at Lucerne Cantonal Hospital.
- Recruitment period: May 2023 to June 2024.
- Initial population: 12 males, 6 females.
- Age at recruitment: 36.7 ± 13.2 years.
- Participants used multiple daily injections (MDI) or conventional subcutaneous insulin infusion (CSII); hybrid closed-loop systems were not used.

### Exclusion criteria
Reported exclusions included comorbidities, medications affecting heart rate, severe hypoglycemia unawareness (Clarke score ≥4), and regular strenuous physical activity. The full list is provided in the supplementary inclusion/exclusion table.

### Final analytical cohort
- 18 recruited.
- 4 excluded because technical problems caused data loss.
- 3 additional participants excluded because they had no or only very short (<15 minutes) hypoglycemic events.
- **11 participants included in final analysis** (9 males, 2 females).
- Age: 39 ± 12 years.
- Weight: 85 ± 17 kg.
- BMI: 26.2 ± 2.9.
- HbA1c: 7.1% ± 0.46%.
- Diabetes duration: 15.6 ± 5.5 years.

### Important implications
The analyzed cohort is small and selected for having qualifying hypoglycemic events and sufficient data. The final sex distribution is imbalanced. Results should not be interpreted as population-wide generalization.

## 4. Devices and data collection

### Reference: CGM
- **Dexcom G6**, used blinded for outcome recording.

### Predictor device: smartwatch
- **Apple Watch Series 8**.

### Smartwatch predictor variables
- Heart rate (HR).
- Heart rate variability (HRV).
- Step count.
- Active energy expenditure.
- Blood oxygen saturation (BOS).
- Respiratory rate (RR).
- Time of day.

Measurement intervals were dictated by the watch/device system, so sampling was not necessarily regular or continuous for every predictor.

### Additional manually recorded information
Participants kept diaries documenting:
- Mealtimes.
- Meal content.
- Insulin usage.
- Capillary glucose readings.
- Sleeping times.

Participants were also asked to measure capillary glucose at least four times daily and when hypoglycemia was suspected. Other CGM systems besides the blinded study CGM were not permitted.

### Collection duration
- Approximately **10 days per participant**, corresponding to the maximum Dexcom G6 collection period.
- Participants continued their usual daily activities.
- Data from sources were merged using timestamps.
- The resulting dataset was represented at **1-minute intervals**.

## 5. Outcome and target construction

The CGM was used to define the binary target.

- **Positive:** glucose <4 mmol/L for at least 15 minutes.
- **Negative:** otherwise.

The task is therefore event detection/classification based on a CGM-derived hypoglycemia label—not continuous glucose regression.

### Temporal implications
The target represents qualifying hypoglycemia rather than a numeric glucose estimate. The paper also assesses whether a hypoglycemic event is detected within its first 15 minutes.

When interpreting the first-15-minute analysis, distinguish detection during an already occurring event from prediction of an event before it begins. The study's wording and operational definition should not be conflated with forecasting glucose at a fixed future horizon.

## 6. Predictors and feature engineering

The paper reports **15 input variables in total**:
- 13 derived from smartwatch data.
- 2 derived from manual documentation: mealtime and short-acting insulin usage.

### Imputation
The methods describe:
- CGM data backfilled for up to 5 minutes.
- HRV, RR and BOS forward-filled according to their measurement frequency.

The stated approach was intended to avoid the possibility of leakage from future predictor observations.

### Feature engineering
Reported engineered features include:
- Lagging features and rolling averages.
- HRV difference from the previous measurement, where available.
- Time-of-day represented by sine and cosine transformations.
- Time since the last meal.
- Insulin-on-board estimates.

### Manual meal/insulin features
Mealtimes and short-acting insulin usage were recorded simply as “1”, without composition or dosage detail.

Insulin-on-board was estimated using a simplified decay representation:
- 100%.
- 75%.
- 50%.
- 25%.

These levels were applied over the four hours following short-acting insulin usage.

Two participants (P03 and P13) with incomplete meal records were modeled without mealtime-derived features.

### Audit considerations
- The simplified meal and insulin representation may not capture actual carbohydrate intake, dose, insulin type, timing precision or pharmacokinetics.
- Missingness and device-specific sampling could affect feature distributions.
- Data imputation must be interpreted in the context of the exact time-indexed data pipeline.

## 7. Model

### XGBoost
- Python version: 3.9.13.
- Model: XGBoost tree-based ensemble.
- Binary classification.
- Separate waking and sleeping models.

### Imbalance handling
Random undersampling was applied to the training dataset. The test dataset remained imbalanced.

Undersampling was chosen over oversampling techniques such as SMOTE to avoid disrupting temporal structure and introducing redundant synthetic records.

### Repeated training
The undersampling procedure was repeated using **10 different random states** to improve reproducibility of the sampling-based modeling process.

### Standardization and regularization
Data were standardized before model training. L1 and L2 regularization terms were set to 10.

### Modeling state
Separate models were used for waking and sleeping periods because:
- Physiological parameter distributions may differ.
- Activity features are more relevant during waking periods.
- Some predictors (e.g., RR and BOS) were more available at night.
- The authors wanted state-specific feature relevance and model behavior.

## 8. Evaluation protocol

### Personalized day/night split
The authors explicitly recognize that time-series rows are not i.i.d. and that random row splitting could produce leakage and overoptimistic performance.

They therefore developed a custom method:

1. Each day/night was assigned an identifier using the participant's waking and sleeping times.
2. Data were separated into daytime and nighttime datasets.
3. Days/nights containing one or more hypoglycemic events were identified.
4. Iteratively, an event-containing day or night was selected as the test set.
5. Remaining days/nights formed the training data.
6. Each test day/night was evaluated in its own iteration.
7. Results across iterations were averaged to obtain the participant's personalized-model result.

The authors note that strict temporal splitting was considered but was difficult because hypoglycemia could occur toward the end of the participation period and the event occurrence was uncertain.

### Number of models
- Daytime modeling: 11 participants.
- Nighttime modeling: 6 participants initially had nighttime events, but nighttime modeling could not be performed for two participants due to inconsistent sleep times and/or sleep restrictions.
- Reported total: **17 personalized predictive models (11 daytime and 6 nighttime)**.

The paper's reported nighttime results should be interpreted in light of this feasibility and availability limitation.

### Evaluation metrics
- AUROC.
- Sensitivity.
- Specificity.
- Detection of an event within the first 15 minutes.

Sensitivity and specificity were calculated at an optimal threshold selected using Youden's J statistic.

## 9. Reported results

### Overall performance
- Average AUROC: **0.74 ± 0.08**.
- Average specificity: **0.76 ± 0.18**.
- Average sensitivity: **0.71 ± 0.15**.

### Hypoglycemia event detection
Among **101 modeled hypoglycemic events**:
- 60.4% were identified by the model at any time during the event.
- 36.6% were identified within the first 15 minutes.

### Daytime feature importance
Average absolute SHAP values indicated:
- Time-of-day features: **29.9%**.
- Activity features: **28.5%**.
- Cardiac features: **24.0%**.
- Manual features: **17.6%**.

### Nighttime feature importance
- Cardiac features: **42.2%**.
- Time-of-day features: **37.5%**.
- Respiratory features: **15.2%**.
- Manual features: reported as minimally important.

### Feature-category definitions
- Cardiac: HR and HRV.
- Time: sine/cosine hour-of-day features.
- Activity: step count and active-energy features.
- Manual: time since last meal and insulin on board.
- Respiratory: RR and related nighttime inputs.

## 10. Methodological audit

These are interpretation and benchmarking considerations—not necessarily confirmed flaws.

### A. Personalized evaluation vs unseen-person generalization
The models are trained and tested using the same participant's data.

**What this answers:** whether an individual's historical data can support an individualized model evaluated on held-out day/night units.

**What it does not answer:** whether a model trained on one group of participants generalizes to a new, unseen participant without calibration.

**Benchmark action:** classify this as personalized within-participant evaluation. Do not compare its results directly with LOSO or participant-independent population models as if they answer the same question.

### B. Temporal leakage
The authors explicitly avoid random row splitting and use day/night test units.

**Strength:** this is a meaningful design choice for a time-series dataset with repeated and correlated observations.

**Audit questions:**
- Do all feature windows respect the held-out day/night boundary?
- Could rolling windows or imputation cross between test and training day/night units?
- Are the test days/nights selected only after event labels are known, and how does this affect the estimand?
- Does the iterative procedure provide an unbiased estimate of performance over the participant's complete time sequence?

The custom procedure is more appropriate than random row splitting for this context, but it is not equivalent to a strict chronological future-period evaluation.

### C. Event-conditioned test selection
Test units are selected based on the presence of hypoglycemia.

This may be useful for evaluating event detection, but the test-set composition and the estimand differ from an ordinary continuous chronological test period.

**Benchmark action:** describe the protocol accurately as iterative held-out event-containing day/night evaluation. Report event-level and day/night-level denominators where possible.

### D. Personalized model calibration
The approach requires a participant's own data for training.

**Benchmark action:** report the amount of personal data required before a model can be used, how model performance changes as training data accumulate, and whether calibration is operationally feasible.

### E. Small and selected cohort
- 11 final participants.
- Participants without qualifying hypoglycemia or adequate data were excluded.
- Final cohort sex imbalance.

**Benchmark action:** participant-level uncertainty and individual performance distributions are more informative than a single pooled metric. Generalizability to a broader T1D population remains limited.

### F. Class imbalance
Undersampling was applied only to the training data, while test data remained imbalanced.

**Strength:** preserving the test distribution is important for practical evaluation.

**Audit action:** report the prevalence of hypoglycemia in each test unit and interpret sensitivity, specificity and AUROC accordingly. For event detection, precision, false alarms per day and event-level detection rates may also be useful.

### G. Threshold selection
Sensitivity and specificity were evaluated using the threshold determined by Youden's J statistic.

**Audit questions:**
- Was the threshold selected within training/validation data only?
- Was it re-estimated for each iteration?
- Was it applied to the held-out test set without further tuning?

Threshold-selection locality is important for interpreting sensitivity and specificity.

### H. Imputation
The authors describe backfilling CGM labels up to five minutes and forward filling selected smartwatch predictors.

**Audit:** confirm the exact operation and target-label alignment. The stated intention is to avoid future predictor leakage; the final implementation should be inspected if code is reproduced.

### I. Feature availability and missingness
Apple Watch sampling is device-driven and some features are sparse.

The authors report limited HRV measurement frequency during daytime and differences in respiratory and oxygen-saturation feature availability.

**Benchmark action:** quantify missingness and feature availability by waking/sleeping state and participant. Consider missingness indicators and evaluate alternative feature availability regimes if data permit.

### J. Interpretation of SHAP feature importance
SHAP importance is averaged across participant models.

**Caution:** feature importance is model-dependent and correlational. It does not establish physiological causality or a universally optimal sensor configuration.

**Benchmark action:** report participant-level variation, not just average feature-category contribution.

### K. Hypoglycemia detection vs glucose prediction
The outcome is a binary event label derived from CGM. It is not an estimate of the numeric glucose concentration.

**Benchmark action:** keep event-detection papers in a separate synthesis category from regression studies and future-glucose forecasting papers.

### L. Event detection vs forecasting
Detecting a hypoglycemic event during its onset or after it has begun is different from predicting an event before glucose reaches the threshold.

The reported 36.6% detection within the first 15 minutes is an event-detection result, not necessarily a 15-minute glucose forecast.

**Benchmark action:** explicitly distinguish retrospective/current-event detection, warning/detection latency and prospective forecasting.

## 11. Reproduction checklist

- [ ] Retrieve Zenodo dataset.
- [ ] Retrieve and inspect GitHub implementation.
- [ ] Verify the 18 recruited and 11 analyzed participant flow.
- [ ] Verify final participant characteristics.
- [ ] Verify exact Apple Watch feature definitions and sampling.
- [ ] Verify CGM label alignment and event definition.
- [ ] Reconstruct 1-minute merge procedure.
- [ ] Reconstruct imputation and feature engineering.
- [ ] Verify day/night segmentation.
- [ ] Reconstruct custom iterative train/test procedure.
- [ ] Verify test-unit boundaries for all rolling/lagged features.
- [ ] Verify undersampling locality.
- [ ] Verify standardization locality.
- [ ] Verify threshold-selection locality.
- [ ] Reproduce daytime and nighttime models separately.
- [ ] Reproduce AUROC, sensitivity and specificity.
- [ ] Reproduce event-level and first-15-minute detection results.
- [ ] Reproduce SHAP analysis.
- [ ] Report participant-level variability.
- [ ] Evaluate false alarms per day and event-level sensitivity, if feasible.
- [ ] Compare with simple personalized baselines.
- [ ] Keep this task distinct from continuous glucose regression and forecasting.

## 12. Benchmark relevance

| Dimension | Study characteristics | Benchmark relevance |
|---|---|---|
| Task | Binary hypoglycemia detection | Separate task category |
| Target | CGM <4 mmol/L for ≥15 minutes | Event definition |
| Cohort | 11 analyzed adults with T1D | Small personalized cohort |
| Devices | Apple Watch Series 8 + blinded Dexcom G6 | Consumer wearable predictors, CGM target |
| Inputs | HR, HRV, activity, BOS, RR, time, manual meal/insulin | Multimodal physiological/context features |
| Temporal representation | 1-minute merged dataset | Time-series structure |
| Model | XGBoost | Tree ensemble |
| Personalization | Participant-specific models | Within-person learning |
| Day/night | Separate models | Physiological-state stratification |
| Split | Iterative held-out event-containing day/night | Temporal-aware, personalized evaluation |
| AUROC | 0.74 ± 0.08 | Classification performance |
| Sensitivity | 0.71 ± 0.15 | Detection |
| Specificity | 0.76 ± 0.18 | Detection |
| Event detection | 60.4% during event; 36.6% within first 15 min | Event/latency analysis |
| Interpretability | SHAP | Feature-category analysis |
| Code | GitHub available | Reproducibility |
| Data | Zenodo available | Reproducibility |

## 13. Suggested interpretation for our research paper

Mohamed et al. (2025) is not directly comparable to continuous wearable glucose-estimation studies because it formulates the problem as personalized binary hypoglycemia detection rather than glucose regression or fixed-horizon forecasting. It is nevertheless an important methodological reference.

The study demonstrates a practical evaluation design for repeated wearable time-series data: personalized modeling, separate waking and sleeping models, and iterative held-out day/night evaluation rather than random row-level splitting.

For our methodological benchmark, its strongest relevance is:
1. The importance of defining the target task precisely.
2. The distinction between personalized and unseen-participant generalization.
3. The risks of random splitting in repeated time-series data.
4. Event-based test selection and the estimand it creates.
5. Leakage locality for imputation, rolling features, standardization, undersampling and threshold selection.
6. Feature-availability and missingness issues in consumer wearables.
7. Reporting event-level detection and latency alongside AUROC.

It should be included in the literature synthesis under **personalized wearable hypoglycemia detection**, rather than pooled directly with continuous glucose regression results.

## 14. Sources

- Full article: https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0325956
- GitHub: https://github.com/YMM-0/smartwatch_hypo_detection.git
- Dataset: https://zenodo.org/records/15309060
