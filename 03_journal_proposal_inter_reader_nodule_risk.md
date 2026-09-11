# Journal Project Proposal

## Inter-Reader-Aware 3D Pulmonary Nodule Risk Estimation under Institutional Shift with Calibrated Selective Prediction and Quantitative Explainability

**Proposal date:** 12 September 2026  
**Study type:** Retrospective, multi-dataset medical-imaging AI development and external testing  
**Primary modality:** Thoracic computed tomography (CT)  
**Primary hardware assumption:** NVIDIA RTX 2000 Ada, 16 GB VRAM  
**Estimated duration:** 16-20 weeks  
**Intended output:** Full journal article and reproducible research package

## Abstract

Pulmonary-nodule malignancy models commonly collapse several radiologists' assessments into a single binary label. This removes clinically meaningful disagreement and can make model confidence appear more certain than the reference data justify. This project proposes a 3D, inter-reader-aware framework that predicts the distribution of radiologist malignancy assessments for a pulmonary nodule, separates reader-related uncertainty from model disagreement, and evaluates both under institutional dataset shift. The model will be developed using LIDC-IDRI and externally tested on LNDb without target-label tuning. A compact 3D encoder will operate on resampled nodule-centred CT volumes. An ordinal distribution head will predict ratings on the 1-5 malignancy-suspicion scale, while a segmentation branch will learn from soft multi-reader masks. A sequentially trained deep ensemble will estimate between-model disagreement. Probability calibration and selective-prediction thresholds will be selected exclusively with source validation patients. Evaluation will include ordinal accuracy, probability calibration, distributional agreement, segmentation quality, risk-coverage behaviour, external degradation, and quantitative explanation localization and faithfulness. The study will explicitly describe the outcome as radiologist-assessed nodule malignancy risk rather than pathology-confirmed lung-cancer diagnosis. The anticipated contribution is a journal-level evaluation of how reader disagreement, model uncertainty and institutional shift interact in 3D pulmonary-nodule assessment.

## 1. Background and motivation

LIDC-IDRI contains thoracic CT scans and annotations from up to four experienced radiologists. For nodules at least 3 mm in diameter, readers supplied outlines and ratings for malignancy suspicion, subtlety, texture, spiculation, margin and other characteristics. Inter-reader disagreement is substantial, including disagreement about whether a lesion is a nodule, its boundary and its malignancy rating. Treating an arithmetic mean or majority vote as an error-free label hides that variation.

Several recent papers have already addressed pieces of this problem. Multi-reader consensus segmentation, label-distribution learning, calibrated malignancy prediction and external LIDC-to-LNDb testing have all appeared by 2026. A publishable journal contribution therefore cannot rest only on using multiple annotations, Monte Carlo dropout, Grad-CAM, a 3D network or a second dataset. The proposed study combines these elements around a narrower scientific question: whether distinct uncertainty sources can be measured, calibrated and used for safe case referral when a model moves from LIDC-IDRI to an independently collected cohort.

The project also corrects limitations of the author's earlier conference work: it uses original volumetric images, patient-disjoint partitions, a clearly defined reference standard, clinically appropriate segmentation metrics, uncertainty calibration, quantitative explainability and locked external testing.

## 2. Current research gap and positioning

The proposed gap is not “inter-reader uncertainty has never been studied.” Current literature already includes:

- Measurement of substantial disagreement among LIDC-IDRI readers.
- Networks that learn a radiologist's assessment together with peer assessments.
- Multi-annotator consensus segmentation.
- A 2026 conference study using ambiguity-weighted label-distribution learning on LIDC-IDRI.
- A 2026 calibration-aware malignancy framework evaluated on LIDC-IDRI and LNDb.
- Recent LIDC/LNDb segmentation models emphasizing external robustness.

The remaining journal-level opportunity is a controlled, three-part analysis:

1. **Reader uncertainty:** Preserve the full ordinal distribution of individual reader ratings and soft spatial disagreement between their contours.
2. **Model uncertainty:** Estimate between-model disagreement separately from predicted reader variation.
3. **Domain uncertainty:** Measure how both quantities and their calibration change from LIDC-IDRI to LNDb.

The practical output is a selective-prediction policy that refers uncertain cases while reporting exactly which cases and patient groups are withheld. The system is evaluated as retrospective decision support, not as an autonomous diagnostic device.

## 3. Problem statement

Given a 3D CT crop containing a pulmonary nodule, predict:

1. The probability distribution over radiologist malignancy-suspicion ratings 1-5.
2. The expected ordinal malignancy score and a derived low/high-suspicion probability.
3. A nodule segmentation and boundary-uncertainty map.
4. Reader-related predictive variation and between-model disagreement.
5. Whether the prediction should be returned or deferred under a source-selected coverage policy.

The primary outcome is the distribution of available radiologist ratings. It is not a pathological diagnosis. Ratings 1 and 2 may be grouped as lower suspicion and 4 and 5 as higher suspicion for secondary binary evaluation; rating 3 remains indeterminate and must not be silently discarded from the primary analysis.

## 4. Aim and objectives

### Aim

Develop and externally test an inter-reader-aware 3D pulmonary-nodule risk model that remains transparent about label disagreement and model unreliability under institutional shift.

### Objectives

1. Construct an auditable patient- and nodule-level LIDC-IDRI/LNDb dataset from original volumetric images and reader annotations.
2. Preserve individual reader ratings and contours rather than reducing them immediately to hard consensus labels.
3. Develop a compact 3D ordinal distribution model with a soft multi-reader segmentation branch.
4. Decompose predicted uncertainty into within-prediction reader variation and between-model disagreement.
5. Evaluate probability and ordinal calibration internally and on a locked external dataset.
6. Test selective prediction at prespecified retained-coverage levels.
7. Quantitatively evaluate explanation localization, faithfulness and stability.
8. Report performance and failure modes by nodule size, texture, reader agreement and available acquisition characteristics.
9. Release permitted code, configurations, split manifests and synthetic verification cases.

## 5. Research questions and hypotheses

| Research question | Prespecified hypothesis or analysis |
|---|---|
| RQ1. Does learning the complete reader-rating distribution improve ordinal prediction? | H1: The proposed model will reduce external Earth Mover's Distance between predicted and observed rating distributions relative to mean-label regression and hard-label classification. |
| RQ2. Does soft multi-reader segmentation improve risk prediction? | H2: Adding the segmentation branch will improve external ordinal performance or calibration relative to the same encoder without segmentation supervision. |
| RQ3. Does model disagreement identify unreliable external predictions? | H3: Higher ensemble disagreement will be associated with higher external ordinal error after controlling descriptively for observed reader disagreement. |
| RQ4. Can selective prediction reduce retained-case error? | H4: Source-selected uncertainty referral will reduce external retained-case error as coverage decreases, while referral burden and subgroup coverage are reported. |
| RQ5. Does uncertainty remain calibrated under institutional shift? | Estimate the change in calibration, coverage and uncertainty-error association from LIDC-IDRI to LNDb; preserved calibration is not assumed. |
| RQ6. Are explanations spatially and behaviourally credible? | Compare localization, deletion/insertion faithfulness and perturbation stability with gradient-based and occlusion methods. |

H1 is the primary confirmatory comparison. Remaining hypotheses form a prespecified secondary family.

## 6. Expected novelty and contributions

- A 3D malignancy-suspicion model trained against full ordinal reader distributions.
- Joint use of soft spatial consensus and ordinal reader disagreement without treating either as pathology.
- Explicit separation of predicted reader variability and ensemble disagreement.
- Locked LIDC-IDRI-to-LNDb external evaluation of calibration and selective prediction.
- Coverage-aware reporting that counts all deferred cases and examines unequal referral across subgroups.
- Quantitative XAI evaluation using reader segmentations, faithfulness tests and stability tests.
- A reproducible protocol addressing patient leakage, annotation matching and source overlap.

Novelty must be claimed at the level of this complete study design. Individual components such as label-distribution learning, deep ensembles and Grad-CAM have extensive prior art.

## 7. Datasets and access

### 7.1 LIDC-IDRI: development source

LIDC-IDRI contains 1,018 thoracic CT cases collected through multiple institutions and annotations generated through a two-stage process involving four thoracic radiologists. Nodules of at least 3 mm may contain multiple reader contours and 1-5 ratings for malignancy suspicion and eight additional characteristics.

**Access:** Public through The Cancer Imaging Archive (TCIA), subject to TCIA usage and citation requirements. Use original DICOM and XML annotations rather than a Kaggle image derivative.

**Planned role:** Model training, hyperparameter selection, source calibration, source policy selection and internal testing using patient-disjoint groups.

**Important limitation:** Reader malignancy ratings are subjective imaging assessments. LIDC-IDRI includes limited pathology information for some cases, but the complete rating dataset is not a pathology-confirmed cancer cohort. Pathology-linked cases may be examined separately when provenance is reliable; they will not redefine the main outcome.

### 7.2 LNDb: locked external source

LNDb contains 294 CT scans collected at Centro Hospitalar Universitário de São João in Portugal between 2016 and 2018. Nodules of at least 3 mm were segmented and characterized using a protocol adapted from LIDC-IDRI. Up to five radiologists contributed across the project, although the number of readings varies by scan. Fifty-eight scans and their annotations were originally held out for the challenge.

**Access:** Public research access through the LNDb/Grand Challenge resources and related Zenodo releases, subject to applicable terms.

**Planned role:** External testing only. No external outcome labels may influence preprocessing selection, architecture, calibration or referral thresholds.

**Important limitation:** Annotation structure and reader count differ from LIDC-IDRI. A harmonization document must specify which ratings and contours are comparable. External results should be stratified by the number of available readers.

### 7.3 Feasibility gate

Before modelling, produce a cohort table containing:

- Patients and CT series obtained.
- Nodules before and after matching reader annotations.
- Nodules with one, two, three and four or more ratings.
- Nodule diameter and texture distributions.
- Scanner, slice-thickness and reconstruction metadata availability.
- Cases with missing or unusable volumes.
- Potential source duplicates or version conflicts.

The primary multi-reader analysis should require at least three reader ratings per nodule. A secondary all-eligible analysis may include fewer ratings with uncertainty reflecting the smaller empirical sample. Final counts must be reported after preprocessing; collection-level totals cannot be presented as analytic sample sizes.

## 8. Inclusion, exclusion and preprocessing

### Inclusion

- A unique CT examination with resolvable patient identifier.
- A nodule at least 3 mm according to the source annotation convention.
- A valid 3D location and at least one usable reader contour.
- At least three malignancy ratings for the primary inter-reader analysis.
- Sufficient image coverage around the nodule for the selected crop.

### Exclusion

- Unresolvable annotation-to-series mapping.
- Corrupted or incomplete image geometry.
- Duplicate series retained elsewhere in the same partition.
- Images for which physical coordinates cannot be reconstructed reliably.
- Nodules crossing a partition boundary through duplicate identifiers.

### Preprocessing

1. Load DICOM using physical coordinates and retain original metadata.
2. Convert pixel values to Hounsfield units using rescale slope/intercept.
3. Apply a prespecified lung window, with mediastinal-window input tested as an ablation.
4. Resample to 1 mm isotropic spacing for the main analysis; preserve original spacing for sensitivity analysis.
5. Match contours belonging to the same nodule using source identifiers and spatial-overlap rules validated on a manual sample.
6. Generate a soft segmentation target equal to the fraction of readers marking each voxel.
7. Generate 64³ and 96³ voxel nodule-centred crops; select one on source validation data.
8. Normalize intensity using fixed HU bounds, not test-set statistics.
9. Apply physically plausible training augmentation: small rotation, translation, scaling, Gaussian noise and intensity shift.
10. Avoid horizontal flips unless laterality and spatial conventions are explicitly handled.

All derived files retain patient, scan, nodule and source identifiers. Splits are assigned before crops or augmented samples are generated.

## 9. Proposed methodology

### 9.1 Shared 3D encoder

Use a compact 3D ResNet-18/34 or similarly sized MONAI backbone. The main architecture should remain below approximately 40 million parameters. A self-supervised medical 3D initialization may be added as an ablation, but large-scale pretraining is outside scope.

### 9.2 Soft segmentation branch

A lightweight decoder predicts a voxel-wise probability corresponding to the fraction of readers including the voxel in their contour. The segmentation loss combines soft Dice and binary cross-entropy. An auxiliary boundary loss may be included only if it improves source validation performance under a frozen selection rule.

### 9.3 Ordinal distribution head

The rating head predicts probabilities `p1...p5` for the five ordered malignancy-suspicion categories. Train against the empirical distribution of reader ratings.

Primary loss:

`L_rating = EMD(predicted distribution, observed reader distribution) + lambda_nll * cross_entropy`

Earth Mover's Distance respects the ordering of the ratings: confusing 1 with 2 is less severe than confusing 1 with 5. The predicted expected rating is `sum(k * pk)` for `k=1...5`.

Total loss:

`L_total = L_rating + lambda_seg * L_segmentation`

Tune `lambda_nll` and `lambda_seg` exclusively on source validation patients.

### 9.4 Uncertainty decomposition

Train three independently initialized models sequentially. For a given nodule:

- **Reader-related variation:** Expected variance of the predicted ordinal distribution within each model.
- **Between-model disagreement:** Variance of expected ratings across ensemble members.
- **Total predictive variation:** Sum of the within-model and between-model components, reported as an operational decomposition rather than proof of uniquely identifiable aleatoric and epistemic uncertainty.

Calibrate the ensemble mean on a held-out source calibration group using ordinal logistic recalibration or a source-selected temperature method. Do not fit a calibrator on external outcomes in the main experiment.

### 9.5 Selective prediction

Define referral thresholds using the source policy-selection group at retained coverage levels of 95%, 90%, 80% and 70%. Compare:

- Referral by between-model disagreement.
- Referral by total predictive variation.
- Referral by maximum class probability.
- Random referral at identical coverage.

Report performance on retained cases and all-case referral burden. A deferred case is not counted as correct. Assess whether small, subsolid or high-disagreement nodules are referred disproportionately.

### 9.6 Quantitative explainability

Evaluate Grad-CAM, Integrated Gradients and 3D occlusion sensitivity on the same fixed test nodules.

Metrics:

- Fraction of positive attribution energy inside the union and intersection reader masks.
- Pointing-game accuracy.
- Deletion and insertion curves.
- Explanation similarity after small, prediction-preserving perturbations.
- Association between explanation dispersion and boundary disagreement.

Qualitative figures should include correctly predicted, incorrectly predicted, ambiguous, externally shifted and confidently wrong examples. Explanations describe model sensitivity and must not be called causal evidence.

## 10. Baselines

1. Mean-reader-score regression with radiomic features and ridge regression.
2. Radiomics with ordinal logistic regression and gradient boosting.
3. Hard-label 2D DenseNet or EfficientNet based on the earlier conference approach.
4. Hard-label 3D ResNet using mean-rating thresholds.
5. 3D expected-score regression using mean squared error.
6. 3D ordinal classification using cumulative logits.
7. Label-distribution model without segmentation supervision.
8. Proposed model without uncertainty ensemble.
9. Proposed model with consensus hard mask instead of soft reader masks.
10. A reproducible calibration-aware external baseline modeled after relevant 2026 work when implementation details permit.

All baselines use the same patient groups, CT crops and outcome availability. Published headline metrics must not be compared directly with local results generated under a different split.

## 11. Experimental design and leakage prevention

### Source development

Create patient-disjoint LIDC-IDRI groups:

- 65% representation training.
- 10% hyperparameter validation.
- 10% probability calibration.
- 5% selective-policy selection.
- 10% untouched internal test.

If reader and outcome counts make a single split unstable, use grouped five-fold cross-validation for source method comparison, followed by a separately frozen calibration/policy protocol. The locked LNDb test remains unchanged.

### External testing

After selecting preprocessing, architecture, ensemble size, calibration and policy thresholds, fit the final source model without accessing LNDb outcome labels. Run one locked external evaluation. Any later target recalibration must use separate target calibration patients and be reported as supervised adaptation, not external generalization.

### Leakage controls

- Split by patient before extracting nodules and slices.
- Keep all annotations and repeat series for one patient in one partition.
- Fit intensity normalization, feature selection and radiomics harmonization on source training data only.
- Do not use test masks to select the nodule crop in a deployment simulation; report oracle-mask experiments separately from coordinate- or detector-based inputs.
- Track LUNA16 as a derivative of LIDC-IDRI and never treat it as an independent external dataset.
- Exclude model checkpoints whose pretraining includes the external test data when a clean external claim is required, or disclose the contamination risk.
- Freeze the statistical analysis plan before external evaluation.

## 12. Evaluation metrics

### Primary metric

Patient-macro Earth Mover's Distance between predicted and observed reader-rating distributions on LNDb. Average nodule metrics within patient first, then average patients equally.

### Ordinal and discrimination metrics

- Ordinal MAE and RMSE of expected rating.
- Quadratic weighted kappa.
- Negative log-likelihood.
- Accuracy within one rating category.
- For derived low/high suspicion: AUROC, AUPRC, sensitivity, specificity, balanced accuracy and macro-F1.
- Indeterminate-case performance with rating 3 retained.

### Calibration and uncertainty

- Multiclass/ordinal Brier score.
- Calibration intercept and slope for derived high-suspicion probability.
- Reliability plots and classwise calibration.
- Expected calibration error with documented binning.
- Uncertainty-error Spearman association.
- Risk-coverage and calibration-coverage curves.
- External change in coverage at locked referral thresholds.

### Segmentation

- Soft and consensus Dice.
- Intersection over Union.
- 95th-percentile Hausdorff distance.
- Average symmetric surface distance.
- Performance by nodule diameter and texture.
- Model-to-reader performance relative to inter-reader variability.

### External robustness

- Absolute and relative performance degradation from internal to external testing.
- Worst-group performance by slice thickness, size, texture and reader-agreement level when sample sizes support it.
- Confidence intervals for every primary and key secondary estimate.

## 13. Ablation studies

1. 2D versus 3D input.
2. 64³ versus 96³ crops.
3. Hard consensus labels versus mean score versus rating distribution.
4. No segmentation branch versus hard-mask branch versus soft-reader branch.
5. Single model versus three-model ensemble.
6. Uncalibrated versus calibrated probabilities.
7. No referral versus alternative referral scores.
8. Lung window versus lung plus mediastinal windows.
9. Original versus isotropic spacing.
10. Manual-mask, consensus-coordinate and automatic-localization input regimes.
11. Inclusion versus exclusion of rating-3 nodules.
12. Three-reader versus all-eligible analytic cohorts.

Ablations will be prioritized and capped before external testing. Avoid a combinatorial search over every interaction.

## 14. Statistical analysis

- Report 95% confidence intervals from at least 2,000 patient-cluster bootstrap replicates.
- Compare the primary model with the strongest baseline using paired patient-cluster bootstrap differences and a paired permutation test.
- Use Holm correction for the prespecified secondary comparison family.
- Compare AUROCs with a paired method that respects patient clustering or bootstrap the paired AUROC difference.
- Evaluate uncertainty-error relationships with patient-cluster bootstrap intervals.
- Use mixed-effects exploratory models to examine nodule-level predictors while including patient and dataset effects.
- Treat subgroup analyses as exploratory unless event and patient counts support prespecified inference.
- Report all missing metrics, failed runs and excluded nodules.

Multiple nodules, readers and slices from the same patient are not independent observations. Training seeds are repetitions of an algorithm, not independent clinical samples.

## 15. Compute and storage plan

Assumptions: RTX 2000 Ada 16 GB, mixed precision, gradient accumulation, 32 GB host RAM minimum and 64 GB preferred.

| Stage | Estimated requirement |
|---|---:|
| LIDC-IDRI and LNDb download/storage | Approximately 150-300 GB raw and derived data |
| DICOM/XML conversion and annotation matching | 12-30 CPU-hours plus manual audit |
| Crop and soft-mask generation | 4-12 CPU-hours |
| One compact 3D model | Approximately 1-4 GPU-hours |
| One three-member ensemble | Approximately 4-12 GPU-hours |
| Main source development and bounded tuning | 30-70 GPU-hours |
| Baselines and selected ablations | 40-100 GPU-hours |
| XAI, calibration and external evaluation | 15-35 GPU-hours |
| Total core study | Approximately 100-220 GPU-hours, or 4-9 continuous GPU-days |
| Expanded five-fold journal study | Approximately 220-400 GPU-hours, or 9-17 continuous GPU-days |

Actual wall time depends on CPU decoding, GPU power limits, crop size and ensemble design. Benchmark 1,000 training steps first and extrapolate from measured seconds per step. Cache resampled crops and predictions. Train ensemble members sequentially. If compute is constrained, preserve external testing and statistical rigor while reducing hyperparameter trials and ablation breadth.

## 16. Implementation stack and reproducibility

- Python 3.11 or a fully pinned compatible version.
- PyTorch and MONAI.
- SimpleITK, pydicom and nibabel for medical-image handling.
- NumPy, pandas/Polars and PyArrow.
- PyRadiomics for conventional feature baselines.
- scikit-learn, XGBoost or CatBoost.
- Captum for attribution.
- SciPy and statsmodels for analysis.
- MLflow or structured experiment records.
- Container or locked environment file after local compatibility testing.

Release code for annotation matching, patient splits, crop generation, model fitting, calibration, selective prediction and metrics. Restricted or large source data should not be redistributed. Provide synthetic CT-like fixtures and tests for annotation matching, coordinate transforms and patient separation.

## 17. Ethics, privacy and clinical boundaries

Obtain an institutional determination for secondary use of public deidentified medical images. Follow TCIA, LNDb and repository licenses and attribution requirements. Store data on encrypted media where possible and restrict access to the research team. Do not publish identifying metadata or unapproved patient-level traces.

The model predicts radiologist suspicion, which can reproduce reader bias and does not establish cancer pathology. Selective referral is an offline simulation without a defined clinician response pathway. No treatment, screening or biopsy decision should be based on this research model. Prospective evaluation, pathology linkage and human-factors assessment would be required before clinical use.

## 18. Risks and mitigation

| Risk | Mitigation |
|---|---|
| Novelty overlap with 2025-2026 studies | Center the paper on uncertainty decomposition, locked external calibration, subgroup referral and quantitative XAI; maintain a living comparison matrix. |
| Subjective malignancy labels | Use precise terminology, preserve rating distributions and separate limited pathology-linked analyses. |
| Different reader counts across datasets | Stratify by reader count; use uncertainty estimates that reflect empirical support; conduct matched-reader sensitivity analyses. |
| Annotation matching errors | Automated spatial checks plus blinded manual audit of a random sample. |
| External calibration failure | Report failure honestly; add separately labelled target recalibration only as a secondary experiment. |
| Selective prediction hides difficult groups | Report coverage and error by size, texture and available demographics; count every deferred case. |
| Small GPU | Cropped 3D inputs, mixed precision, sequential ensembles and capped tuning. |
| Excessive study scope | Make distributional external prediction primary; segmentation and XAI remain supporting contributions. |

## 19. Timeline and milestones

| Weeks | Milestone |
|---|---|
| 1-2 | Systematic targeted literature matrix; download and licensing audit |
| 3-4 | DICOM/XML harmonization, reader matching and cohort flow diagram |
| 5 | Source-only preprocessing pilot and protocol registration |
| 6-7 | Radiomics, 2D and hard-label 3D baselines |
| 8-10 | Distribution model and soft segmentation branch |
| 11 | Ensemble, calibration and referral policy selection |
| 12 | Freeze model and analysis plan |
| 13 | Locked LNDb external evaluation |
| 14-15 | Ablations, XAI and statistical analysis |
| 16-18 | Manuscript and reproducibility package |
| 19-20 | Clinical review, revision and submission preparation |

## 20. Expected outcomes

1. A reproducible, patient-disjoint LIDC-IDRI/LNDb data pipeline.
2. Evidence on whether reader-distribution learning transfers between institutions.
3. Quantification of calibration loss and uncertainty behaviour under shift.
4. A transparent selective-prediction analysis with subgroup referral rates.
5. Quantitative evidence about explanation localization and faithfulness.
6. A full journal manuscript, including negative or null findings where applicable.

Success is not defined as achieving a predetermined accuracy. A strong null result showing that ensemble uncertainty or soft consensus fails to transfer can still provide a valuable journal contribution when the evaluation is rigorous.

## 21. Publication strategy

The paper should be written as a new journal study and cite the author's earlier conference paper as preliminary work. The introduction and cover letter should explain the differences: original volumetric data, corrected reference-standard terminology, inter-reader modeling, 3D learning, patient-disjoint partitions, independent external testing, calibration, uncertainty, statistical analysis and quantitative XAI.

The intended venue category is a peer-reviewed journal in medical imaging AI, biomedical informatics or computer-aided diagnosis. A methods-focused journal will expect reproducibility and controlled experiments; a clinically oriented journal will additionally expect pathology linkage, expert evaluation or stronger clinical workflow evidence. Select the journal only after checking current scope, article type, fees and conference-extension policy.

Follow CLAIM 2024 and STARD-AI 2025. Use “development,” “internal testing” and “external testing” consistently. Include a participant/scan/nodule flow diagram, full acquisition summary, model card and limitations section.

## 22. Recent and foundational references

1. Armato SG III et al. **The Lung Image Database Consortium (LIDC) and Image Database Resource Initiative (IDRI): A Completed Reference Database of Lung Nodules on CT Scans.** Medical Physics, 2011. [Article](https://pmc.ncbi.nlm.nih.gov/articles/PMC3041807/)
2. Armato SG III et al. **The Lung Image Database Consortium: An Evaluation of Radiologist Variability in the Identification of Lung Nodules on CT Scans.** Academic Radiology, 2007. [Article](https://pmc.ncbi.nlm.nih.gov/articles/PMC2290739/)
3. Koo HJ et al. **Measuring Interobserver Disagreement in Rating Diagnostic Characteristics of Pulmonary Nodules Using LIDC-IDRI.** Journal of Digital Imaging, 2017. [Article](https://www.sciencedirect.com/science/article/pii/S1076633217300090)
4. Pedrosa J et al. **LNDb v4: Pulmonary Nodule Annotation from Medical Reports.** Scientific Data, 2024. [Article](https://www.nature.com/articles/s41597-024-03345-6)
5. LNDb Challenge. **Dataset description and annotation protocol.** [Official data page](https://lndb.grand-challenge.org/Data/)
6. Zhu W et al. **MS-Net: Learning to Assess the Malignant Status of a Lung Nodule by a Radiologist and Her Peers.** Medical Physics, 2023. [Article](https://pmc.ncbi.nlm.nih.gov/articles/PMC10338807/)
7. **Multi-Annotator Consensus Network with Adaptive Preprocessing for Lung Nodule Segmentation.** 2025. [Article](https://etasr.com/index.php/ETASR/article/view/12408)
8. **Dynamic Ambiguity-Weighted Label Distribution Learning for Lung Nodule Malignancy Score Estimation from CT Images.** ISADES 2026 conference proceedings. [Record](https://eurekamag.com/research/109/096/109096424.php)
9. **LungMate: Calibrated Malignancy Risk Estimation for Pulmonary Nodules from Chest CT under Dataset Shift.** 2026. [Article](https://www.sciencedirect.com/science/article/pii/S1110016826002486)
10. **GLANCE: Continuous Global-Local Exchange with Consensus Fusion for Robust Nodule Segmentation.** 2026. [Article](https://pmc.ncbi.nlm.nih.gov/articles/PMC12827329/)
11. Tejani AS et al. **Checklist for Artificial Intelligence in Medical Imaging: 2024 Update.** Radiology: Artificial Intelligence, 2024. [DOI](https://doi.org/10.1148/ryai.240300)
12. Sounderajah V et al. **The STARD-AI Reporting Guideline for Diagnostic Accuracy Studies Using Artificial Intelligence.** Nature Medicine, 2025. [Article](https://www.nature.com/articles/s41591-025-03953-8)

---

### Proposed one-sentence contribution statement

This study externally evaluates a 3D pulmonary-nodule model that preserves radiologist rating and contour disagreement, distinguishes that disagreement from ensemble uncertainty, and measures how both affect calibration, referral and explanation reliability under institutional CT shift.
