# Paper 03 — Pikulin et al. (2024)

## Bibliographic information

**Title:** Enhanced blood glucose levels prediction with a smartwatch  
**Authors:** Sean Pikulin, Irad Yehezkel, Robert Moskovitch  
**Journal:** PLOS ONE, 19(7): e0307136  
**Publication date:** July 18, 2024  
**DOI:** https://doi.org/10.1371/journal.pone.0307136  
**Article:** https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0307136  
**Dataset:** Gludict — Wearable Sensors and CGM (Figshare)  
https://figshare.com/articles/dataset/Glucdict_-_Wearable_Sensors_and_CGM/25939312  
**Dataset DOI:** https://doi.org/10.6084/m9.figshare.25939312.v1

---

## 1. Executive summary

Pikulin, Yehezkel, and Moskovitch propose Gludict, a framework for forecasting glucose levels using recent CGM history and wearable smartwatch sensor data. The study collected data from 12 participants over approximately 10 days using a Mobvoi TicWatch Pro 2020, Dexcom G6 CGM, and smartphone-based activity logging.

The study investigates observation-window length, prediction horizon, the contribution of smartwatch sensors and manually documented eating/drinking activities, and model choice. Models include Rocket, Prophet, LSTM, ResNet, and Inception. The best-performing reported approach is Rocket using glucose history, heart rate, and step-detector data, with a 60-minute observation window. The authors report RMSE values in the approximate range of 4–10 mg/dL across prediction horizons for their best combination.

**Benchmark relevance:** This paper is especially useful for examining personalized forecasting, temporal-window design, overlapping sliding windows, normalization, model selection, and the distinction between personalized and population-level generalization. The paper's results should not be directly compared with subject-independent wearable-estimation studies because it trains and evaluates models separately for each participant.

## 2. Research objectives

The paper aims to:
1. Forecast glucose at future horizons of 5, 10, 15, 30, 45, and 60 minutes.
2. Investigate observation windows of 30, 60, and 90 minutes.
3. Assess whether manually recorded activities (eating/drinking) improve forecasting.
4. Test whether smartwatch sensor signals improve prediction beyond CGM history alone.
5. Determine which smartwatch signals or combinations are most useful.
6. Compare model performance across several time-series models.

The core emphasis is on a personalized model that uses each participant's own CGM and wearable records.

## 3. Dataset and study design

### Participants
- Total: 12 participants.
- Participants were mostly students (9 of 12).
- Age range: 22–63 years.
- 5 female and 7 male participants.
- One participant had Type 1 diabetes.
- Weight range: 53–87 kg.
- Height range: 162–185 cm.

### Data collection
- Duration: approximately 10 days per participant.
- Smartwatch: Mobvoi TicWatch Pro 2020, worn on the right wrist.
- CGM: Dexcom G6.
- Smartphone: Android phone running the activity logging app.
- Recruitment period: October 26, 2021 to February 7, 2022.
- Participants were asked to upload daily data using a smartwatch app.

### Important population limitation
This is a small dataset with only one participant identified as having T1D. Most participants were not recruited as a clinical diabetes cohort. The study therefore primarily demonstrates individualized short-term glucose forecasting in this limited population, not clinical validation for diabetes management.

## 4. Sensor modalities

### Smartwatch signals

The authors developed a Wear OS app to record sensor data. The paper lists:

| Signal | Description |
|---|---|
| Heart rate (HR) | Beats per minute |
| Step detector | Event when a step is taken |
| Accelerometer | Triaxial acceleration, including gravity |
| Gyroscope | Rotation rate around device axes |
| Gravity sensor | Orientation/gravity vector |
| Geomagnetic sensor | Changes in Earth's magnetic field |

Sensor values were logged when a value changed, resulting in irregular event-based sampling.

### CGM
- Dexcom G6.
- Glucose readings at approximately 5-minute intervals.

### Manual activity logging
Participants used a smartphone app named GlucoDataSaver to record:
- Eating
- Drinking

Each record included activity type and start/end times. Participants could also enter activities retrospectively. The authors note that participants found manual logging burdensome and many activities were not recorded.

## 5. Data processing and preprocessing

### Event consolidation
Smartwatch sensor streams were irregularly logged: a sensor record was stored when a value changed. To create a consolidated time series, the authors carried the previous sensor values forward and updated the relevant sensor at the event timestamp.

This creates a dense representation during periods when the device was being worn and monitored.

### Resampling
- Different sources had different sampling rates.
- The smartwatch streams were consolidated into a uniform 1-second time series.
- Piecewise Aggregate Approximation (PAA) was used to reduce the sensor stream to one measurement per second.
- For most smartwatch signals, the per-second value was based on a mean.
- Step detector was represented as the number of steps within the second.

### Activity encoding
Activity was represented at one-second resolution using the activity label active during that second:
- 0: no activity logged
- 1: eating
- 2: drinking

### Normalization
- Min-Max normalization was used.
- The paper describes temporal-variable normalization into a range from 0 to 1.

**Reproduction audit:** The paper's description of Min-Max normalization should be checked against the implementation to establish whether scaler parameters were fit separately within the nested training partitions or on the full participant time series. This is important because even in personalized modeling, preprocessing fitted on data from the test period can introduce temporal information leakage.

## 6. Window construction and forecasting task

### Observation windows
The authors experimented with:
- 30 minutes
- 60 minutes
- 90 minutes

### Prediction horizons
They evaluated:
- 5 minutes
- 10 minutes
- 15 minutes
- 30 minutes
- 45 minutes
- 60 minutes ahead

### Sliding-window approach
- A sliding observation window was used to construct model inputs.
- The input included CGM history and selected smartwatch/activity features.
- The prediction target was a glucose value at a specified future horizon.
- Windows were generated from periods when smartwatch and smartphone data were monitored.

### Important methodological questions
1. How much overlap exists between adjacent windows?
2. Were windows randomly partitioned in the nested CV or were inner/outer folds temporally separated?
3. If windows were randomly partitioned, could nearly identical neighboring windows appear in both training and validation/test sets?
4. Was the evaluation intended to represent prediction for a future segment of the same participant, or interpolation across a participant's recorded days?
5. How were periods without smartwatch monitoring treated, and were there gaps across which windows were generated?

These questions are central to a fair assessment of forecasting performance. The paper reports nested cross-validation, but the exact split construction and temporal separation should be verified in the implementation/supplementary details before labeling the protocol as leakage-prone.

## 7. Models

The study evaluates:

### Rocket
- Random convolutional kernels transform time-series inputs into a feature space.
- Transformed features are fed to a linear model (typically Ridge Regression).
- The authors vary the number of kernels.
- Tested kernel counts include 100, 1,000, 10,000, 100,000, and 200,000.
- The 100,000-kernel setting reportedly performs best.

### Prophet
- Additive time-series forecasting model.
- Tuned using changepoint, seasonality, holiday and seasonality-mode parameters.

### LSTM
- Recurrent neural network model for sequential inputs.
- Tuned using epoch count, batch size, and learning rate.

### ResNet
- Deep residual convolutional architecture.
- Tuned using neural-network training parameters.

### Inception
- CNN architecture with parallel convolutional kernels of different sizes.
- Tuned using neural-network training parameters.

## 8. Evaluation protocol

### Personalized modeling
- Each participant's data was modeled separately.
- Models were trained and tested separately for each participant.
- Participants were not pooled into a single population model for the main personalized evaluation.

### Nested cross-validation
- The authors report using nested cross-validation for evaluation and hyperparameter tuning.
- Hyperparameter selection was performed within the participant-specific modeling process.

### Reproduction audit
Nested cross-validation is a positive methodological feature, but its effectiveness depends on fold construction. The benchmark should verify:
- Whether outer folds are temporally separated.
- Whether inner folds are temporally separated.
- Whether windows that overlap in time can cross fold boundaries.
- Whether scaler fitting and any data-dependent preprocessing are confined to each training fold.
- Whether the evaluation is prospective (training on earlier periods and testing on later periods) or uses shuffled windows from the participant's full recording.

**The paper's personalized evaluation should not be interpreted as unseen-participant generalization.** The model has access to the participant's data within its own CV training folds.

## 9. Evaluation metrics

- RMSE (mg/dL)
- Clarke Error Grid Analysis (EGA)

The paper uses RMSE to quantify numerical error and EGA to examine the clinical implications of prediction errors. EGA is useful as a supplementary visualization, but it should not by itself be interpreted as demonstrating clinical safety.

## 10. Results

### Observation-window comparison
The authors compare 30-, 60-, and 90-minute observation windows for horizons of 5, 10, 15, 30, 45, and 60 minutes.

Reported conclusion:
- 60-minute observation window performs best across prediction horizons in the overall comparison.
- A 90-minute window may perform better than 30 minutes for short horizons up to 15 minutes, but performance trends differ for longer horizons.
- The authors ultimately select 60 minutes for subsequent experiments.

### Model comparison
With a 60-minute observation window:
- Rocket outperformed the tested LSTM, ResNet, Inception, and Prophet approaches in the reported experiment.
- Rocket with 100,000 kernels achieved the best reported model performance.

### Feature/modality combinations
The study evaluates combinations of CGM history, activities, HR, step detector, and other wearable signals.

Reported findings:
- CGM-only forecasting performed worse than combinations incorporating wearable physiological/activity signals.
- Adding manually recorded activities did not improve results and sometimes performed worse than glucose-only.
- Glucose + HR + step detector was the best reported temporal-variable combination.
- The contribution of the manually documented eating/drinking variables was not consistently beneficial.

### Reported RMSE
The authors report RMSE values in the approximate range of 4–10 mg/dL for their best Rocket model (glucose + HR + step detector, 60-minute observation window), depending on prediction horizon.

**Note:** Exact horizon-specific numerical values should be extracted from the original figures or supplementary materials before using them in a formal comparison table. They are not transcribed here to avoid guessing values from plotted figures.

### Clarke Error Grid
The authors report that most predictions were in Zone A, with a smaller proportion in Zone B, and very few in Zone D for the analyzed horizons.

Clinical grid results should be considered in context of:
- Small participant sample.
- Personalized rather than population-generalized evaluation.
- Uncertainty about temporal split details.
- Limited representation of diabetes in the cohort.

## 11. Strengths

1. Real-world wearable and CGM data collected over approximately 10 days.
2. Personal forecasting task rather than only contemporaneous estimation.
3. Multiple prediction horizons from 5 to 60 minutes.
4. Multiple observation-window lengths.
5. Several model families compared.
6. Nested cross-validation is reported for evaluation and tuning.
7. Wearable sensor feature combinations investigated.
8. Manual activity logging assessed rather than assumed useful.
9. Comparison with a prior forecasting method and Prophet.
10. Public dataset availability via Figshare.

## 12. Methodological concerns and audit hypotheses

These are questions for verification, not claims that the paper is necessarily flawed.

### 12.1 Personalized versus population generalization
The models are trained/tested separately for each participant. This measures personalized predictive performance and does not answer whether a model generalizes to a new individual.

**Benchmark experiment:** Compare personalized, pooled population, and hybrid calibration approaches.

### 12.2 Small sample size
Only 12 participants were studied, and only one participant had T1D. Many sliding windows do not increase the number of independent participants.

**Benchmark experiment:** Report participant-level results and participant-aware uncertainty; avoid treating all windows as independent.

### 12.3 Temporal leakage through overlapping windows
Sliding windows can share many input observations. If randomly partitioned across folds, neighboring windows may be very similar and may share source observations.

**Benchmark experiment:** Compare shuffled-window nested CV with strict chronological nested evaluation; enforce embargo/gap intervals between training and testing windows where necessary.

### 12.4 Temporal validation
The paper reports nested CV, but the exact temporal structure of the folds needs verification.

**Benchmark experiment:** Train on earlier data, test on later data within each participant. Include a gap where overlapping windows or target labels could cross the boundary.

### 12.5 Normalization
Min-Max normalization is reported. Confirm that extrema are estimated from training folds only.

**Benchmark experiment:** Compare train-only scaling against scaling fitted on the full participant record, where the latter is included only as a leakage demonstration and not a valid protocol.

### 12.6 Baselines
The paper compares several model families, including Rocket and Prophet, and a prior method. However, a broader benchmark can still examine transparent, simple baselines such as:
- Last-value persistence.
- Linear trend extrapolation.
- CGM-only Ridge regression.
- Moving average.
- Personal mean.
- Wearable-only models.

### 12.7 Activity logging quality
The authors acknowledge incomplete manual activity logging. This makes negative results for activity features difficult to interpret as evidence that activity information is intrinsically unhelpful.

**Benchmark experiment:** Evaluate the modality with missingness explicitly modeled; do not equate missing activity entries with absence of the underlying activity.

### 12.8 Device and population scope
The dataset is small and has limited clinical representation.

**Benchmark experiment:** Treat this dataset as a personalized forecasting benchmark, not as evidence of clinical generalizability.

### 12.9 Clinical interpretation
The authors describe the predictions as clinically safe based on error-grid results. The benchmark should use more cautious language: EGA is an evaluation tool, and clinical safety requires broader evidence and prospective validation.

## 13. Relevance to wearable-glucose-benchmark

This paper contributes an important **personalized forecasting** perspective. Unlike Paper 01 and Paper 02, which are more directly relevant to wearable-based glucose estimation/generalization across individuals, Paper 03 explicitly incorporates recent CGM history and forecasts future values.

It is particularly useful for benchmark questions about:
- Observation-window length.
- Prediction horizon.
- Overlapping sliding windows.
- Nested CV.
- Personalized performance.
- CGM history versus wearable signal contribution.
- Model comparison, including Rocket.
- Activity-feature quality and missingness.

## 14. Proposed benchmark experiments inspired by this paper

- **B3-E1:** Reproduce the personalized forecasting setup.
- **B3-E2:** Reproduce 30-, 60-, and 90-minute observation windows.
- **B3-E3:** Reproduce 5-, 10-, 15-, 30-, 45-, and 60-minute prediction horizons.
- **B3-E4:** Compare shuffled-window CV with chronological CV.
- **B3-E5:** Add a gap/embargo around temporal split boundaries to prevent overlapping windows from crossing.
- **B3-E6:** Compare CGM-only, CGM+HR, CGM+steps, CGM+HR+steps, and other modality combinations.
- **B3-E7:** Compare persistence, linear trend, Ridge, Rocket, Prophet, LSTM, and CNN baselines.
- **B3-E8:** Compare training-fold-only normalization against full-record normalization in a controlled leakage demonstration.
- **B3-E9:** Report per-participant results and participant-level uncertainty.
- **B3-E10:** Evaluate how activity missingness affects performance and interpretability.

## 15. Reproducibility checklist

- [ ] Download the Figshare dataset.
- [ ] Verify participant IDs and data completeness.
- [ ] Reconstruct sensor event consolidation and forward-fill behavior.
- [ ] Reproduce 1-second resampling/PAA.
- [ ] Reconstruct activity encoding.
- [ ] Verify Min-Max scaler fitting within folds.
- [ ] Reconstruct window lengths and prediction horizons.
- [ ] Verify nested CV split construction and temporal ordering.
- [ ] Ensure overlapping windows cannot leak across strict temporal split boundaries.
- [ ] Reproduce Rocket kernel-count comparison.
- [ ] Reproduce modality combinations.
- [ ] Extract exact horizon-specific RMSE from figures/supplement.
- [ ] Reproduce Clarke Error Grid results where possible.
- [ ] Report per-participant performance and uncertainty.

## 16. Research assessment

**Role in the benchmark:** A valuable reference for personalized glucose forecasting, but not a direct test of generalization to unseen participants.

Its reported strong results should be interpreted alongside its task definition, small cohort, personalized evaluation, overlapping-window design, and temporal CV details. It provides a strong basis for testing how evaluation choices influence forecasting performance, especially when windows overlap and when the target is a future glucose value.

## 17. References

- PLOS ONE article: https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0307136
- DOI: https://doi.org/10.1371/journal.pone.0307136
- Figshare dataset: https://figshare.com/articles/dataset/Glucdict_-_Wearable_Sensors_and_CGM/25939312
- Dataset DOI: https://doi.org/10.6084/m9.figshare.25939312.v1
