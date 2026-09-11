# Conference-to-Journal Project Proposal

## Diagnosing Cross-Cohort Failure in Lung CT Foundation Models and Radiomics: Calibration, Segmentation Dependence and Selective Prediction

**Proposal date:** 12 September 2026  
**Study type:** Retrospective multi-cohort benchmarking and robustness analysis  
**Primary modality:** Pretreatment NSCLC CT  
**Primary hardware assumption:** NVIDIA RTX 2000 Ada, 16 GB VRAM  
**Planned publication path:** Focused conference paper followed by a substantially extended journal article  
**Estimated duration:** 10-12 weeks for conference core; 12-18 additional weeks for journal extension

## Abstract

Radiomics and CT foundation models offer complementary approaches to learning imaging biomarkers from small cancer cohorts. A July 2026 preprint has already compared multiple foundation encoders, radiomic representations, prediction heads and segmentation regimes across LUNG1 and LUNG2, making a simple accuracy comparison insufficiently novel. This project instead asks why models fail under cross-cohort shift and whether those failures can be detected and mitigated without external outcome labels. The conference study will compare standardized three-dimensional radiomics with frozen Curia representations for NSCLC histology prediction from LUNG1 to NSCLC-Radiogenomics. It will measure discrimination, calibration, segmentation sensitivity, acquisition-stratified robustness and selective prediction under a locked external protocol. The journal extension will add further foundation models, automatic and segmentation-free input regimes, representation-shift diagnostics, unsupervised target-aware normalization, a second clinical endpoint, and an additional robustness cohort or institution where feasible. The journal version will cite and clearly differentiate the conference paper, use independent new experiments and broaden the clinical and statistical evidence. Both stages are designed for frozen-encoder feature extraction on a 16 GB GPU rather than computationally prohibitive foundation-model pretraining.

## 1. Motivation

Medical CT datasets are often too small for reliable end-to-end training. Radiomics provides explicit shape, intensity and texture variables, while foundation models provide transferable learned representations. Their relative performance depends on task, segmentation quality, CT acquisition, cohort and classifier. The practical question is no longer simply whether a foundation model beats radiomics. A useful study must reveal when each representation fails, how much performance changes across cohorts, whether confidence remains calibrated and whether uncertainty can identify unsafe transfer.

The project is deliberately staged:

- The **conference paper** answers one focused question using two cohorts, one primary task and a controlled set of models.
- The **journal article** introduces new methods, models, data or endpoints and substantially expands the scientific conclusion.

This design reduces initial execution risk while preserving a credible journal extension.

## 2. Prior work and novelty boundary

A July 2026 preprint, *Foundation Models vs. Radiomics for Lung Computed Tomography*, already benchmarks Curia, Curia-2, DINOv3, 2D/3D radiomics, seven prediction heads, three segmentation regimes and five tasks using LUNG1 for development and LUNG2 for external evaluation. It reports that segmentation and classifier choices affect tasks differently. Therefore, reproducing that grid with minor model changes would be incremental.

The proposed novelty is a **failure-diagnosis protocol**, comprising:

1. Locked probability calibration and its degradation across cohorts.
2. Acquisition- and morphology-stratified worst-group performance.
3. Controlled segmentation perturbation rather than only segmentation presence/absence.
4. Representation-shift measurements linked to individual prediction failures.
5. Selective prediction evaluated at source-locked coverage thresholds.
6. Resource-normalized comparison of predictive benefit, latency, memory and preprocessing burden.

The journal extension must add meaningful evidence beyond these conference contributions. A new encoder alone is insufficient.

## 3. Clinical task and scope

### Conference primary task

Predict pretreatment NSCLC histological category from CT using a harmonized label shared by LUNG1 and NSCLC-Radiogenomics. The preferred endpoint is adenocarcinoma versus squamous-cell carcinoma, subject to a label audit and minimum class counts. Other or unspecified histologies will be excluded from the confirmatory endpoint and retained for clearly labelled secondary analyses.

Histology is selected because it is clinically interpretable and available in both cohorts. The project predicts the recorded histological category; it does not replace pathology.

### Journal secondary task

Add one endpoint with a different statistical structure, preferably:

- Overall-survival modelling with censoring using Cox or discrete-time survival heads; or
- Stage grouping, if stage definitions can be harmonized defensibly.

The endpoint must be fixed after a data dictionary audit. Avoid two-year survival classification that discards censored observations unless used only as a sensitivity analysis.

## 4. Aim and objectives

### Overall aim

Determine how radiomic and frozen foundation-model representations behave under clinically relevant CT cohort shift, and develop transparent diagnostics for calibration, segmentation dependence and unreliable transfer.

### Conference objectives

1. Harmonize LUNG1 and NSCLC-Radiogenomics imaging, masks, histology labels and acquisition metadata.
2. Compare 3D radiomics, frozen Curia embeddings and combined features under identical patient partitions and classifiers.
3. Quantify internal-to-external discrimination and calibration degradation.
4. Measure sensitivity to controlled errors in tumour segmentation.
5. Evaluate source-locked selective prediction and worst-group performance.
6. Release a reproducible conference benchmark and analysis protocol.

### Journal-extension objectives

1. Add Curia-2 and at least one independent CT representation such as FM-LCT or CT-CLIP, subject to weights and licensing.
2. Replace oracle manual tumour masks with automatic and lung-only/segmentation-free regimes.
3. Introduce representation-shift diagnostics and a source-only or unlabeled-target adaptation method.
4. Add survival or stage as a second endpoint.
5. Add a third robustness cohort, test-retest dataset or institutional cohort.
6. Expand statistical, subgroup, failure-case and computational-cost analyses.

## 5. Research questions and hypotheses

### Conference questions

| Question | Prespecified hypothesis or analysis |
|---|---|
| C-RQ1. Which representation transfers best for histology? | Compare radiomics, Curia and their fusion using patient-macro external AUROC; no universal winner is assumed. |
| C-RQ2. Does internal calibration survive external shift? | H1: All model families will show worse calibration externally than internally. |
| C-RQ3. Which representation is more sensitive to segmentation error? | H2: Shape-heavy radiomics will degrade more under boundary perturbation, while learned features may be more sensitive to crop displacement or lost context. |
| C-RQ4. Can source uncertainty identify external errors? | Compare risk-coverage curves using calibrated confidence, ensemble disagreement and distance-to-source representations. |
| C-RQ5. Are overall metrics hiding subgroup failures? | Report worst-group and distributional results by slice thickness, contrast status where available, tumour volume and histology class. |

### Journal questions

| Question | Planned extension |
|---|---|
| J-RQ1. Do conclusions generalize across foundation encoders? | Add Curia-2, FM-LCT/CT-CLIP and a conventional supervised 3D backbone. |
| J-RQ2. Can useful transfer be retained without expert tumour masks? | Compare manual, automatic, lung-only and segmentation-free inputs. |
| J-RQ3. Can unlabeled target information improve robustness? | Evaluate prespecified intensity normalization, feature alignment or conformal calibration variants without external outcome-label selection. |
| J-RQ4. Are findings task-specific? | Add survival or stage and compare representation rankings. |
| J-RQ5. Are embeddings stable to repeat scans and acquisition perturbations? | Add test-retest or controlled reconstruction/resampling experiments. |

## 6. Datasets and data availability

### 6.1 NSCLC-Radiomics / LUNG1

- 422 subjects and CT examinations.
- Pretreatment CT.
- Manual primary gross-tumour-volume contours in DICOM RTSTRUCT/SEG.
- Clinical variables including diagnosis/histology, stage and survival information.
- Approximately 35.8 GB from TCIA.
- CC BY-NC 3.0 according to the current TCIA collection page.

**Role:** Training, model selection, source calibration, source policy selection and internal testing.

**Known issue:** TCIA reports incomplete slices for several named cases. These must be identified and excluded or handled prospectively.

### 6.2 NSCLC-Radiogenomics / LUNG2

- 211 subjects.
- CT and PET/CT.
- Semantic tumour annotations and segmentations.
- Clinical, survival, gene-mutation and RNA-sequencing information.
- Approximately 98 GB of imaging data in the current collection description.
- Imaging and related files available through TCIA under applicable terms.

**Role:** Locked external testing for the conference paper and a secondary source for journal analyses.

**Important limitation:** Only a subset may have a usable primary-tumour segmentation and complete harmonized histology. The analytic sample will be smaller than 211.

### 6.3 Additional journal resources

Select at least one:

- **RIDER Lung CT:** Small test-retest CT resource for representation repeatability rather than clinical discrimination.
- **NSCLC-Radiomics Interobserver1:** Approximately 22 cases with multiple contours for segmentation sensitivity and feature robustness.
- **TCGA-LUAD and TCGA-LUSC:** 69 and 37 imaging subjects respectively. Because collection identity is almost perfectly aligned with histology, these collections cannot independently prove histology generalization; use them only for carefully designed stress tests or non-histology endpoints.
- **Institutional cohort:** Preferred journal addition if deidentified pretreatment CT, pathology and acquisition metadata can be obtained under ethics approval.

The journal plan should not promise a third clinical cohort until access and outcome comparability are confirmed.

## 7. Dataset audit and inclusion strategy

Before modelling, create a locked data dictionary covering:

- Patient, study and series identifiers.
- Histology terminology and mapping rules.
- CT timing relative to treatment.
- Contrast status.
- Slice thickness, pixel spacing, kernel and manufacturer.
- Tumour contour type and availability.
- Stage version and categories.
- Survival time, event status and missingness.
- Multiple CT series per patient and selection rule.

Include one pretreatment diagnostic CT series per patient using a source-independent deterministic selection rule. Exclude missing geometry, unresolvable contours, non-primary lesions and cases lacking the primary outcome. Do not choose series based on downstream model performance.

Publish a flow diagram with collection-level counts, eligible patients, exclusions, outcome counts and final partition sizes.

## 8. Imaging and radiomics preprocessing

1. Convert DICOM pixels to Hounsfield units.
2. Preserve original metadata and orientation.
3. Resample to 1 mm isotropic spacing in the main pipeline.
4. Clip and normalize using fixed lung and mediastinal HU windows.
5. Create a tumour bounding box with a prespecified context margin.
6. Retain the entire tumour for radiomics; avoid resampling that truncates large lesions.
7. Extract IBSI-aligned PyRadiomics features using a versioned parameter file.
8. Include original shape, first-order and texture families; add wavelet features only in a prespecified secondary set.
9. Remove constant/near-constant features and highly correlated variables using source training data only.
10. Standardize and select features within each training fold.
11. Apply ComBat only as a registered experiment, with fitting rules that prevent target-outcome leakage.

### Controlled segmentation perturbations

Generate physically interpretable mask perturbations:

- Erosion and dilation of approximately 1, 2 and 3 mm.
- Boundary jitter.
- Crop-centre displacement.
- Removal of selected peripheral tumour regions.
- Automatic-mask replacement in the journal stage.

Ensure perturbed masks remain valid. Compare each prediction with the same patient's manual-mask prediction.

## 9. Representation models

### Conference representation set

1. **Radiomics3D:** Standardized PyRadiomics features.
2. **Curia:** Frozen volumetric or slice-aggregated embeddings following the released preprocessing and checkpoint documentation.
3. **Fusion:** Concatenated, training-standardized radiomic and Curia features with regularized feature selection.
4. **Conventional control:** Frozen or lightly trained 3D ResNet features to separate foundation pretraining from architecture alone.

### Journal representation set

Add:

- Curia-2 when public weights and a reproducible inference path are available.
- FM-LCT or CT-CLIP as an independent lung/CT-specific representation.
- Automatic-segmentation and lung-only embeddings.
- A learned shift-aware fusion or gating model whose complexity is justified by source validation.

Foundation-model pretraining is outside scope. Encoders remain frozen for the conference study. Parameter-efficient adaptation may be tested in the journal extension, with strict source-only model selection.

## 10. Prediction heads and uncertainty

Use deliberately simple heads suited to small cohorts:

- Logistic regression with elastic-net regularization.
- CatBoost or XGBoost with a tightly bounded search.
- A shallow multilayer perceptron as a nonlinear control.

For the primary comparison, select one common head in advance based on source validation, then compare representation families under that head. Report a representation-by-head matrix as secondary evidence.

Calibrate probabilities using logistic recalibration or temperature scaling on a patient-disjoint source calibration set. Train a small three-member head ensemble or bootstrap ensemble to estimate disagreement without repeatedly running the expensive encoder.

### Shift indicators

Compute source-only or unsupervised measures:

- Mahalanobis distance to the source representation distribution.
- Local density or k-nearest-neighbour distance.
- Classifier ensemble disagreement.
- Maximum calibrated probability.
- Acquisition metadata novelty score.

Evaluate whether these measures rank external errors and calibration failures. Avoid calling them complete epistemic uncertainty.

## 11. Conference experimental design

### Source partition

Partition LUNG1 patients into:

- 60% representation-head training.
- 15% hyperparameter validation.
- 10% calibration.
- 5% selective-policy selection.
- 10% untouched internal test.

Use grouped nested cross-validation as a sensitivity analysis if class counts in a single split are insufficient. Every preprocessing and feature-selection step occurs inside the relevant training fold.

### External test

Lock the complete LUNG2 analytic cohort until models, calibrators, perturbations, thresholds and analyses are frozen. Do not select a model using external AUROC. The primary result is performance at the predetermined configuration, with alternative configurations labelled exploratory.

### Conference primary comparison

Patient-level external AUROC for adenocarcinoma versus squamous-cell carcinoma, comparing Radiomics3D, Curia and fusion under the same head.

### Key supporting analyses

- AUPRC and class-specific sensitivity/specificity.
- Calibration degradation.
- Segmentation-perturbation degradation.
- Worst-group performance.
- Risk-coverage behaviour.
- Compute and preprocessing cost.

## 12. Journal extension design

The journal article will retain the conference protocol and add substantial new evidence:

1. At least two additional feature extractors.
2. Automatic tumour segmentation and segmentation-free/lung-mask regimes.
3. A new shift-aware method, such as uncertainty-gated representation fusion or unlabeled-target feature alignment.
4. A second endpoint with task-appropriate statistics.
5. At least one additional robustness dataset or institutional cohort.
6. Test-retest and preprocessing stability.
7. Expanded uncertainty, subgroup and failure analysis.
8. A reproducible computational-cost audit.

The journal version will rerun the conference baselines under the expanded common protocol. It will cite the conference article and include a table explicitly distinguishing reused elements from new contributions.

## 13. Leakage prevention

- Partition by patient before image, mask or feature generation.
- Keep repeated studies and contours from one patient together.
- Fit radiomics feature filters, scalers, ComBat parameters and calibration on permitted source data only.
- Do not harmonize histology labels after viewing model errors.
- Do not use external outcomes to choose preprocessing, crop margin, model, threshold or selective coverage.
- Record possible foundation-model pretraining overlap with every test cohort. If overlap cannot be excluded, describe the evaluation as downstream transfer rather than completely unseen external testing.
- Avoid fitting dimensionality reduction on pooled source and external data in the main analysis.
- Treat manual masks as privileged inputs. Report automatic and segmentation-free deployment conditions separately.
- Freeze code commit, configuration and statistical plan before unlocking external results.

## 14. Evaluation metrics

### Classification

- AUROC with patient-cluster 95% confidence interval.
- AUPRC with class prevalence.
- Sensitivity, specificity, balanced accuracy, macro-F1 and Matthews correlation coefficient.
- Confusion matrix at a source-selected operating threshold.

### Calibration

- Brier score.
- Log loss.
- Calibration intercept and slope.
- Reliability diagram.
- Expected calibration error with prespecified bins.
- Decision-curve analysis as an exploratory assessment using justified threshold ranges.

### Domain shift

- Absolute and relative internal-to-external performance change.
- Worst-group AUROC/Brier score across adequately sized acquisition groups.
- Representation Maximum Mean Discrepancy or another prespecified shift statistic.
- Association between shift score and prediction error.

### Segmentation dependence

- Change in prediction probability and correctness after each perturbation.
- Feature intraclass correlation across masks.
- Rank stability of radiomic and foundation features.
- Proportion of patients whose classification changes.

### Selective prediction

- Risk-coverage curves.
- External accuracy and calibration at source-locked 95%, 90%, 80% and 70% coverage.
- Referral rate by tumour size, histology, source and acquisition group.
- Error among referred cases and retained cases.

### Survival extension

- Harrell and Uno concordance indices.
- Time-dependent AUC.
- Integrated Brier score.
- Calibration at prespecified time points.
- Appropriate handling of censoring.

## 15. Baselines and ablations

### Baselines

1. Clinical variables only.
2. Tumour volume only.
3. Radiomics with logistic regression.
4. Radiomics with CatBoost.
5. Curia with logistic regression.
6. Curia with CatBoost.
7. Conventional 3D ResNet embeddings.
8. Radiomics plus Curia fusion.
9. Majority-class and prevalence baselines.

### Conference ablations

- Lung versus mediastinal versus dual window.
- Tumour-only versus tumour-plus-context crop.
- Manual mask versus controlled perturbations.
- Raw versus calibrated probabilities.
- Single head versus head ensemble.
- Confidence versus representation-distance referral.

### Journal ablations

- Curia versus Curia-2 versus FM-LCT/CT-CLIP.
- Manual versus automatic versus lung-only/segmentation-free input.
- Fixed fusion versus uncertainty-gated fusion.
- Source-only preprocessing versus permitted unsupervised target-aware adaptation.
- No harmonization versus registered harmonization.
- Histology versus survival representation ranking.

## 16. Statistical analysis

- Estimate 95% confidence intervals with at least 2,000 patient bootstrap replicates.
- Compare AUROCs using paired bootstrap differences or DeLong where assumptions and pairing are appropriate.
- Compare Brier scores and perturbation effects using paired patient bootstrap intervals.
- Use Holm correction within prespecified secondary comparison families.
- Report effect sizes and confidence intervals alongside p-values.
- Use hierarchical or mixed-effects exploratory analysis for repeated perturbations within patients.
- Perform a precision analysis using observed source variance before external evaluation.
- Avoid claiming statistical independence for slices, patches, perturbations or random seeds.
- Report class counts and missingness for every cohort and subgroup.

## 17. Explainability and failure analysis

The project focuses on representation robustness; XAI is supportive rather than the central novelty.

- Use SHAP for radiomics and simple heads.
- Use feature-group permutation for radiomics families.
- Use Grad-CAM/Integrated Gradients or attention rollout when supported by the encoder.
- Compare image attribution with tumour masks and surrounding context.
- Examine whether explanations remain stable under mask and intensity perturbation.
- Review high-confidence external errors, large shift-score cases and disagreement between radiomics and foundation models.
- Avoid inferring biological causation from feature attribution.

The journal extension may include blinded expert review if a radiologist collaborator is available.

## 18. Compute, memory and storage requirements

### Conference stage

| Stage | Estimated requirement |
|---|---:|
| LUNG1/LUNG2 download and working storage | Approximately 200-350 GB |
| DICOM conversion, resampling and mask audit | 12-30 CPU-hours |
| 3D radiomics extraction | 8-24 CPU-hours |
| Curia embedding extraction | 8-24 GPU-hours, depending on input strategy |
| Conventional 3D feature extraction | 4-12 GPU-hours |
| Prediction heads, calibration and bootstrap analysis | 4-12 CPU/GPU-hours |
| Perturbation and shift experiments | 15-40 GPU-hours |
| Conference total | Approximately 40-100 GPU-hours, or 2-4 continuous GPU-days |

### Journal extension

| Stage | Additional requirement |
|---|---:|
| Two additional foundation encoders | Approximately 20-60 GPU-hours |
| Automatic segmentation and inference | Approximately 20-60 GPU-hours |
| Second endpoint and extra cohort | Approximately 20-50 GPU-hours |
| Shift-aware method and expanded ablations | Approximately 30-80 GPU-hours |
| Journal extension total | Approximately 100-250 additional GPU-hours, or 4-11 continuous GPU-days |
| Complete conference plus journal programme | Approximately 6-15 continuous GPU-days |

Frozen feature extraction is feasible on a 16 GB GPU with model-specific resizing, patching and mixed precision. Billion-parameter Curia-2 variants or full-volume end-to-end fine-tuning may exceed memory. Select an available model size, keep encoders frozen and train lightweight heads. Full foundation-model pretraining is infeasible and scientifically unnecessary.

Recommended host resources:

- 16 GB GPU VRAM.
- 64 GB system RAM preferred; 32 GB is workable with streaming.
- 500 GB free SSD recommended for raw data, resampled volumes, masks and cached embeddings.
- Reliable backup for code, manifests and derived tabular features.

Benchmark a small batch before committing to a model. Record peak VRAM, cases per minute and preprocessing time; update estimates from measured throughput.

## 19. Implementation stack and reproducibility

- Python, PyTorch and MONAI.
- SimpleITK, pydicom and nibabel.
- PyRadiomics with a published YAML parameter file.
- scikit-learn, XGBoost and/or CatBoost.
- lifelines, scikit-survival or pycox for the journal survival task.
- Captum and SHAP.
- SciPy, statsmodels and bootstrap utilities.
- Curia official repository and weights; additional encoders only from traceable releases.
- MLflow or structured local experiment logging.

Store one row per patient/model/input regime in the primary result table. Publish split manifests, exclusion reasons, preprocessing parameters, radiomics definitions, embedding dimensions, model hashes, thresholds and analysis scripts. Do not redistribute source images or restricted annotations.

## 20. Timeline

### Conference stage: 10-12 weeks

| Weeks | Milestone |
|---|---|
| 1-2 | Literature comparison, data access, licensing and label audit |
| 3-4 | DICOM/mask preprocessing and cohort flow diagram |
| 5 | Radiomics and clinical baselines |
| 6 | Curia extraction and common prediction heads |
| 7 | Calibration and locked source policy |
| 8 | Segmentation perturbations and shift measures |
| 9 | Locked external evaluation |
| 10 | Statistics and failure analysis |
| 11-12 | Conference manuscript and reproducibility package |

### Journal extension: additional 12-18 weeks

| Weeks | Milestone |
|---|---|
| 1-3 | Additional encoder and cohort access |
| 4-6 | Automatic/segmentation-free input regimes |
| 7-9 | Shift-aware method and target-unlabeled experiments |
| 10-11 | Second clinical endpoint |
| 12-13 | Test-retest and expanded subgroup analysis |
| 14-15 | Final statistical analysis and expert review |
| 16-18 | Journal rewrite, reporting checklists and submission |

## 21. Risks and mitigation

| Risk | Mitigation |
|---|---|
| Direct overlap with the July 2026 benchmark | Focus on failure diagnosis, locked calibration, perturbation sensitivity, selective prediction and cost; cite and reproduce comparable settings. |
| Curia-2 weights or memory requirements | Use released Curia model sizes and frozen encoders; substitute FM-LCT/CT-CLIP with documented rationale. |
| Incomplete harmonized histology | Audit before protocol lock; restrict primary endpoint to comparable categories and report exclusions. |
| Manual masks create an unrealistic workflow | Make this explicit in the conference paper; add automatic and segmentation-free regimes in the journal. |
| Small external classes | Use confidence intervals, reduce classifier complexity and avoid excessive subgroup claims. |
| Pretraining contamination | Search model documentation; disclose overlap and avoid claiming completely unseen data when unverifiable. |
| Domain adaptation leaks outcomes | Use source-only selection; isolate any target calibration patients from target testing. |
| Conference paper leaves too little journal novelty | Reserve additional encoders, automatic segmentation, second endpoint, extra cohort and shift-aware method for the journal protocol. |
| Excessive compute | Cache embeddings, cap heads and ablations, avoid full-encoder fine-tuning. |

## 22. Ethics and clinical interpretation

Seek an institutional determination for secondary analysis of public deidentified imaging. Follow TCIA and model licenses, including noncommercial conditions where applicable. Securely store clinical tables and remove unnecessary identifiers from derived results.

The model predicts recorded histology or prognosis from retrospective images. It does not replace tissue diagnosis, determine treatment or demonstrate clinical benefit. Dataset shift can represent acquisition, geography, referral patterns, treatment era and annotation practices. Discuss these limitations rather than interpreting every performance difference as scanner causation.

Selective prediction is a retrospective simulation. Referred cases require a defined human pathway before the method could be tested prospectively.

## 23. Expected outcomes

### Conference deliverables

- Harmonized LUNG1/LUNG2 cohort and split specification.
- Controlled radiomics/Curia/fusion comparison.
- Calibration and segmentation-perturbation benchmark.
- External worst-group and risk-coverage results.
- Compact conference manuscript and reproducible code.

### Journal deliverables

- Multi-foundation-model evaluation.
- Automatic and segmentation-free robustness evidence.
- Shift-aware fusion or adaptation method.
- Second clinical endpoint and additional robustness dataset.
- Full computational, subgroup and failure analysis.
- Substantially rewritten journal article.

A valuable result may show that a simpler radiomics or clinical model is better calibrated and more stable than a foundation representation. Publication value comes from a rigorous explanation of model behaviour, not from requiring the foundation model to win.

## 24. Conference-to-journal publication strategy

The conference article should remain focused enough to make one defensible claim:

> Internal ranking of radiomics and foundation representations does not reliably describe external utility; calibration, segmentation perturbation and shift-aware case referral reveal clinically relevant failures.

The journal version should answer the broader question:

> Across encoders, input regimes, tasks and cohorts, which representation and uncertainty strategy provides reliable transfer when expert tumour masks or target labels are unavailable?

The journal submission must cite the conference paper, disclose it to the editor and include a contribution-difference table. Reused datasets and methods should be referenced transparently. New text should be written around the expanded research question rather than copied mechanically. Check the chosen journal's current conference-extension and similarity policy before submission.

Potential venue categories are medical-imaging AI conferences for the first stage and journals in medical image analysis, biomedical informatics, computer-aided diagnosis or digital health for the extension. Venue selection should consider methodological depth, clinical validation expectations, open-access fees and current review timelines.

Follow CLAIM 2024 and STARD-AI 2025. A survival extension should also follow appropriate prediction-model reporting guidance.

## 25. Recent and foundational references

1. Neukirch N, Maurer M, Strodthoff N. **Foundation Models vs. Radiomics for Lung Computed Tomography: A Benchmark of Feature Extractors, Classification Heads, and Segmentation Choices.** 2026 preprint. [arXiv:2607.01001](https://arxiv.org/abs/2607.01001)
2. Dancette C et al. **Curia: A Multi-Modal Foundation Model for Radiology.** 2025 preprint. [arXiv:2509.06830](https://arxiv.org/abs/2509.06830); [official repository](https://github.com/raidium-med/curia)
3. Saporta A et al. **Curia-2: Scaling Self-Supervised Learning for Radiology Foundation Models.** 2026 preprint. [arXiv:2604.01987](https://arxiv.org/abs/2604.01987)
4. Gao C et al. **A Lung CT Vision Foundation Model Facilitating Disease Diagnosis and Medical Imaging.** Nature Communications, 2025/2026 online record. [Article](https://www.nature.com/articles/s41467-025-66620-z)
5. Pai S et al. **Foundation Model for Cancer Imaging Biomarkers.** Nature Machine Intelligence, 2024. [DOI](https://doi.org/10.1038/s42256-024-00807-9)
6. Shi Z et al. **FM-LCT: Foundation Model for Quantitative Imaging Analysis in Lung Cancer.** [Official repository](https://github.com/zhenweishi/FM-LCT)
7. Hamamci IE et al. **CT-CLIP: Generalist Foundation Models from a Multimodal Dataset for 3D Computed Tomography.** [Official repository](https://github.com/ibrahimethemhamamci/CT-CLIP)
8. TCIA. **NSCLC-Radiomics collection, version 4.** [Collection page](https://www.cancerimagingarchive.net/collection/nsclc-radiomics/)
9. TCIA. **NSCLC-Radiogenomics collection.** [Collection page](https://wiki.cancerimagingarchive.net/display/Public/NSCLC%2BRadiogenomics)
10. Imaging Data Commons. **Reproducible radiomics and foundation-feature pipelines.** [Repository](https://github.com/ImagingDataCommons/idc-radiomics-reproducibility)
11. Tejani AS et al. **Checklist for Artificial Intelligence in Medical Imaging: 2024 Update.** Radiology: Artificial Intelligence, 2024. [DOI](https://doi.org/10.1148/ryai.240300)
12. Sounderajah V et al. **The STARD-AI Reporting Guideline for Diagnostic Accuracy Studies Using Artificial Intelligence.** Nature Medicine, 2025. [Article](https://www.nature.com/articles/s41591-025-03953-8)

---

### Conference contribution statement

This study compares radiomic and frozen CT foundation representations through locked cross-cohort calibration, segmentation-perturbation and selective-prediction analyses, revealing failure modes hidden by aggregate discrimination metrics.

### Journal contribution statement

This extended study establishes how encoder choice, tumour localization, task, acquisition shift and uncertainty interact across NSCLC cohorts, and evaluates practical transfer strategies when manual masks and target outcome labels are unavailable.
