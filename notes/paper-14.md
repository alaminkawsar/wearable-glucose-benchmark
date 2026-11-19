# Paper 14 — Ahmed et al. (2023)

## Citation
Ahmed, A., Aziz, S., Abd-alrazaq, A., Farooq, F., Househ, M., & Sheikh, J. (2023). **The Effectiveness of Wearable Devices Using Artificial Intelligence for Blood Glucose Level Forecasting or Prediction: Systematic Review.** *Journal of Medical Internet Research*, 25, e40259. https://doi.org/10.2196/40259

- Full text: https://www.jmir.org/2023/1/e40259
- DOI: https://doi.org/10.2196/40259
- PMID: 36917147
- PMCID: PMC10131991
- PROSPERO registration: CRD42022303175
- Published: 14 March 2023
- Preprint: https://preprints.jmir.org/preprint/40259 (published 13 June 2022)

## 1. Overview

This paper is a systematic review of machine-learning/AI-based blood glucose forecasting or prediction using non-invasive wearable devices in populations with diabetes or suspected diabetes.

The review focuses on wearable systems that use non-invasive sensing and AI/ML for diabetes-related blood-glucose analysis, with particular emphasis on forecasting or prediction.

The authors:
- Search multiple bibliographic databases and Google Scholar.
- Apply predefined inclusion/exclusion criteria.
- Perform study selection and extraction independently with reviewer cross-checking.
- Synthesize results narratively.
- Assess methodological quality using an adapted QUADAS-2 framework.

**12 studies** were included in the final review.

## 2. Objective and contribution

### Objective
Systematically review the effectiveness of AI-enabled non-invasive wearable devices for blood-glucose forecasting or prediction, with a methodological quality assessment.

### Contributions
1. Systematic evidence synthesis of wearable-device/AI studies targeting glucose forecasting or prediction.
2. Characterization of wearable hardware, sensor types, placement, gateways and data-hosting approaches.
3. Overview of modeling approaches and evaluation metrics.
4. Quality/risk-of-bias assessment adapted from QUADAS-2.
5. Identification of methodological and translational gaps in the field.

### Important conceptual point
The authors acknowledge that forecasting (using past observations) and prediction (incorporating additional associated information such as activity, diet or medication) are often used interchangeably in the literature. They **do not separate included studies according to these definitions**.

This is an important limitation for a benchmark intended to distinguish concurrent estimation, personalized prediction and future forecasting.

## 3. Search and review methodology

### Registration and framework
- PROSPERO registration: **CRD42022303175**.
- Review reports adherence to PRISMA guidance.
- Narrative synthesis.
- Quality assessment adapted from QUADAS-2.

### Search sources
The article reports searches across six electronic databases:
- MEDLINE.
- PsycInfo.
- Embase.
- IEEE Xplore.
- ACM Digital Library.
- Web of Science.

Google Scholar was used to identify additional grey literature. Forward and backward reference-list checking was also described.

### Search eligibility timeframe
- English-language publications.
- Published from 2015 onward.
- Peer-reviewed articles and proposals, as described in the eligibility criteria.

### Inclusion criteria
The review included studies that:
- Were in English and published in 2015 or later.
- Were peer-reviewed articles or proposals.
- Included people with or suspected of having diabetes (without restrictions on age, gender or ethnicity).
- Used commercial, medical or prototype wearable devices.
- Used devices that could be worn/used by an individual rather than requiring clinical staff or hospital-only operation.
- Used non-invasive wearable methods.
- Were empirical studies examining blood-glucose levels in diabetes.
- Used AI/ML.

### Exclusion criteria
- Non-English publications, publications before 2015, and non-peer-reviewed material.
- Nondiabetes-related populations.
- Studies without AI as an intervention.
- Non-wearable devices, including implants.
- Studies using only statistical measures without AI/ML.
- Sensors infused inside the body.

## 4. Study selection

### Selection flow
- **3,872** studies initially identified.
- **12** studies included after filtering according to the predefined criteria.

The exact details of deduplication, each screening stage and exclusion counts should be extracted from the PRISMA diagram if reproducing the review.

### Review workflow
Two reviewers performed study selection and data extraction independently, followed by cross-checking.

### Reproduction checklist for review methods
- [ ] Retrieve the PRISMA flow diagram.
- [ ] Extract full search strings for all databases.
- [ ] Extract search dates.
- [ ] Extract screening-stage counts.
- [ ] Extract duplicate-removal process.
- [ ] Extract disagreements and resolution method.
- [ ] Extract full data-extraction form.
- [ ] Extract complete QUADAS-2 adaptation and scoring rules.

## 5. Included-study characteristics

The included studies represented a mixture of prototypes and commercial wearable systems, placements, sensing approaches and ML methods.

### Wearable types
Examples reported in the review include:
- Finger-mounted prototypes.
- Hand-mounted systems.
- Foot-mounted systems.
- Wrist-worn prototypes.
- Commercial smart wristbands.
- Smartwatches.
- Custom optical wearable devices.

### Placement
Reported measurement sites included:
- Finger.
- Hand.
- Foot.
- Wrist.

### Sensing technologies
The review describes approaches including:
- Photoplethysmography (PPG).
- Near-infrared (NIR).
- Electrodermal activity (EDA)/galvanic skin response (GSR).
- Accelerometry.
- Temperature.
- ECG.
- Chemical sensors.
- Other prototype-specific sensing systems.

### Device and system architecture
The reviewed systems used various:
- Gateways (e.g., smartphone or PC).
- Hosts (e.g., cloud, smartphone, Raspberry Pi or other computing systems).
- Transfer methods (e.g., Bluetooth, Bluetooth Low Energy, wired communication or Internet-based transmission).

### Examples of device configurations reported in the review table
| Study | Wearable/placement | Sensing approach (as summarized in review) |
|---|---|---|
| Hina et al. | Finger prototype | NIR, PPG |
| Alfian et al. | Wristband | BLE-connected devices/sensors |
| Alarcon-Paredes et al. | Hand prototype | PPG, GSR |
| Islam et al. | Finger prototype | Raspberry Pi camera |
| Kularathne et al. | Foot prototype | Tilt switch, load cell, temperature/humidity |
| Joshi et al. | Finger prototype | NIR |
| Zhou et al. | Wristwatch | NIR, ECG |
| Mahmud et al. | Wristwatch/wearable | PPG, temperature, GSR |
| Bent et al. | Empatica E4 wristband | PPG, ACC, EDA/GSR, infrared thermopile |
| Lee et al. | Wristwatch prototype | PPG, NIR |
| Shrestha et al. | Wristband prototype | Metal-oxide chemical sensors |
| Li et al. | Wrist prototype | NIR |

**Note:** The table above is a summary of the review's study-level device descriptions, not an independently re-audited set of original papers. Use the primary articles for detailed protocol verification.

## 6. Modeling approaches and metrics

### Machine-learning approaches
The review reports a mix of classical and more modern ML approaches.

- Classical ML methods were used in approximately half of the included studies.
- Ensemble/boosted-tree approaches, including random forest, were prominent.

### Metrics
Reported evaluation approaches included:
- Clarke Grid Error (CGE/CEG).
- RMSE.
- Other performance metrics depending on the individual study.

### Metric frequency in the review
- Clarke grid error: **7 studies (58%)**.
- RMSE: **5 studies (42%)**.

The review notes that studies frequently used multiple metrics.

## 7. Quality assessment

### Tool
The authors used an **adapted version of QUADAS-2** to assess methodological quality/risk of bias.

The assessment considered domains adapted for the context of wearable AI glucose research.

### Main reported findings
- In patient-selection, more than one-third of the studies (reported as six studies, 38.5%) were rated as high risk of bias related to participant sampling, including lack of appropriate sampling across diverse subgroups.
- Incomplete coverage of data-processing steps was noted in studies, particularly for conversion of wearable-acquired data into model inputs.
- The authors highlighted small sample sizes in many studies as a concern for training/testing and overfitting.
- The index-test domain was rated low risk in most studies (11, 92%), based on the authors' assessment of reporting and methodological detail.
- Reference-standard risk was generally assessed as low, although one study did not adequately specify details.
- Flow/time-frame concerns were rated low risk in most studies (11, 92%), according to the review's assessment.
- Cloud-based data hosting was reported in approximately half the studies (6, 50%).

### Interpretation caution
A risk-of-bias assessment is dependent on the chosen instrument, adaptation, evidence extracted and reviewer's judgment. A low QUADAS-style rating does not itself demonstrate participant-independent generalization, prospective forecasting, external validation or clinical utility.

## 8. Principal findings and conclusions

### Principal findings
The authors conclude that non-invasive wearable devices paired with ML have potential for glucose forecasting/prediction, but the evidence base remains small.

They emphasize:
- The variety of prototype and commercial systems.
- The use of PPG and NIR on wrist-worn systems.
- The predominance of classical ML in many studies.
- The importance of clinically meaningful evaluation.
- The need for validation of commercial devices.

### Clinical-range evaluation
The review specifically emphasizes that average accuracy can be misleading when glucose values are largely in a normal range. Performance should be evaluated where it is clinically important, including glycemic-event ranges.

### Commercial-device validation
The authors argue that commercially available wearable devices need further validation and that access to raw sensor data is important for researchers.

### Safety and regulatory context
The review notes that the included non-invasive wearable approaches should not be treated as validated medical-grade replacements for established glucose-monitoring systems. Claims of replacing invasive or semi-invasive monitoring require appropriate clinical and safety validation.

## 9. Methodological audit and relevance to our benchmark

This is a secondary evidence source. The audit below focuses on its usefulness and limitations as a review and on the methodological questions it highlights for our primary-paper benchmark.

### A. Forecasting, prediction and concurrent estimation are not separated
The review acknowledges that terminology is used inconsistently and does not classify the included studies by the temporal/task distinctions.

**Benchmark action:** classify every primary study ourselves into:
- Concurrent glucose estimation.
- Personalized/calibrated estimation.
- Within-person future forecasting.
- Unseen-participant prediction.
- Binary/event detection.
- Other tasks.

### B. Participant-level independence
The review's quality assessment is not a substitute for auditing the split unit in each primary paper.

**Benchmark action:** extract whether each primary study split:
- Participants.
- Sessions.
- Days.
- Records.
- Windows.
- Segments.

Record participant overlap and compare protocols where possible.

### C. Small sample sizes
The review identifies small cohorts as a concern.

**Benchmark action:** record the number of independent participants separately from recordings, windows, segments and CGM observations. Prefer participant-aware uncertainty estimates.

### D. Patient-selection bias and population diversity
The review reports high-risk sampling judgments in a substantial subset of included studies.

**Benchmark action:** extract recruitment, age, sex, diabetes type, health status, glucose range, skin tone where reported, and the degree of cohort diversity. Avoid extrapolating from convenience or controlled cohorts.

### E. Data-processing transparency
The review highlights gaps in preprocessing and data conversion.

**Benchmark action:** document:
- Raw sensor acquisition.
- Synchronization.
- Filtering.
- Window construction.
- Feature engineering.
- Imputation.
- Normalization.
- Feature selection.
- Data-dependent preprocessing locality.

### F. Reference standard
The review considers reference standards, but original papers must still be audited for exact instrument, label timing and synchronization.

**Benchmark action:** extract the glucose-reference method, timestamp, lag assumptions, label window and target construction from each primary article.

### G. Evaluation metrics
CEG and RMSE are common, but the review also notes that accuracy in normal glucose ranges may be misleading.

**Benchmark action:** harmonize units and target scale before comparisons. Include participant-level numerical error, glucose-range stratification and clinical metrics where appropriate and data support them.

### H. External validation
The review identifies further validation as necessary, especially for commercially available devices.

**Benchmark action:** distinguish:
- Internal record-level testing.
- Participant-independent testing.
- Temporal testing.
- External cohort/device validation.
- Prospective evaluation.

### I. Review-era limitations
This review was published in March 2023 and its search was restricted to studies meeting its eligibility/time criteria.

**Benchmark action:** use it as a historical/systematic-review baseline, but update the primary-study corpus through the current literature cutoff and verify each paper directly.

### J. QUADAS-2 adaptation
The review used an adapted diagnostic-quality framework.

**Benchmark action:** do not inherit the review's risk labels uncritically. For our methodological benchmarking purpose, supplement conventional quality assessment with explicit leakage, temporal task, participant independence, personalization, external-validation and reproducibility checks.

## 10. Reproduction checklist

### Review reproduction
- [ ] Obtain the registered PROSPERO protocol.
- [ ] Extract all search strings and database-specific queries.
- [ ] Extract search dates and date limits.
- [ ] Reproduce database and Google Scholar searches.
- [ ] Reproduce forward/backward citation searches.
- [ ] Reconstruct the screening process.
- [ ] Reconstruct the PRISMA flow.
- [ ] Reproduce extraction tables.
- [ ] Reproduce the adapted QUADAS-2 assessment.
- [ ] Verify reviewer independence and disagreement resolution.

### Use for our benchmark
- [ ] Retrieve all 12 primary studies.
- [ ] Check primary-paper details against the review's extraction.
- [ ] Update the search beyond the review's cutoff.
- [ ] Classify task type and temporal semantics.
- [ ] Extract participant and split units.
- [ ] Audit leakage and preprocessing.
- [ ] Extract external/temporal validation.
- [ ] Harmonize metrics and units.
- [ ] Extract glucose-range and participant-level results.
- [ ] Record raw-data/code availability.
- [ ] Separate regression, forecasting and event-detection studies.

## 11. Benchmark relevance

| Dimension | Review characteristics | Benchmark relevance |
|---|---|---|
| Study type | Systematic review | Secondary evidence synthesis |
| Registration | PROSPERO CRD42022303175 | Review transparency |
| Search | Six databases + Google Scholar and citation searching | Literature coverage |
| Initial records | 3,872 | Screening scope |
| Included studies | 12 | Small evidence base |
| Wearable types | Commercial and prototype devices | Hardware diversity |
| Placement | Finger, hand, foot, wrist | Device/site stratification |
| Sensor types | PPG, NIR, EDA/GSR, ACC, ECG, temperature and others | Modality taxonomy |
| ML | Classical and modern approaches | Model taxonomy |
| Metrics | CEG, RMSE and other metrics | Reporting heterogeneity |
| Quality assessment | Adapted QUADAS-2 | Risk-of-bias synthesis |
| Main concern | Patient-selection and processing/reporting limitations | Methodological audit |
| Task taxonomy | Forecasting/prediction terminology not separated | Need for explicit task definitions |
| Reproducibility | Secondary source; primary studies need reinspection | Benchmark source and audit guide |

## 12. Suggested interpretation for our research paper

Ahmed et al. (2023) offers a useful systematic-review baseline for non-invasive wearable glucose forecasting/prediction research. It identifies 12 studies from an initial 3,872 records, describes the diversity of wearable sensor configurations and ML methods, and evaluates study quality using an adapted QUADAS-2 framework.

The review's most useful contributions to our methodological benchmark are its characterization of the field's heterogeneity, its attention to participant selection and processing transparency, and its emphasis on clinically meaningful evaluation rather than headline accuracy alone.

However, for our research question, we should extend the review's synthesis with an explicit task taxonomy and a more granular evaluation-protocol audit. In particular, we should independently classify concurrent estimation, personalized estimation, future forecasting, unseen-participant evaluation and event detection; record the unit of splitting; audit temporal and preprocessing leakage; and distinguish internal, participant-independent, temporal, external and prospective validation.

The review should therefore be cited as a **systematic-review reference and historical synthesis**, not as a source of independently verified split/leakage judgments for every primary study.

## 13. Source

- Full text: https://www.jmir.org/2023/1/e40259
- DOI: https://doi.org/10.2196/40259
- PROSPERO: CRD42022303175
