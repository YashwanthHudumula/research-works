# Cross-Patient and Cross-Dataset Personalized CGM Forecasting with Budget-Constrained Adaptation

**Publication-oriented project proposal · 12 September 2026**  
**Scope:** Retrospective adult type 1 diabetes (T1D), 30- and 60-minute glucose forecasting.  
**Resources:** One NVIDIA RTX 2000 Ada GPU with 16 GB VRAM; approximately 14 weeks.

## Abstract

Continuous glucose monitoring (CGM) forecasting models must accommodate differences between individuals, devices and study populations. Recent personalized and foundation-model approaches make generic claims of personalization insufficient as a research contribution. This project will investigate whether a compact population model, adapted with a tightly controlled amount of historical data from a new patient, improves forecasting on an independently held-out dataset. A temporal convolutional network will learn population dynamics, followed by regularized adaptation of a small residual prediction head using 1, 3 or 7 days of patient history. The study will distinguish generalization to unseen patients, transfer to unseen datasets, and improvement after personalization. Comparators will include persistence, linear extrapolation, gradient boosting, recurrent networks, an unadapted population model, and conventional fine-tuning. Entire source cohorts will be excluded from training when used for external evaluation, including their copies within consolidated datasets. The primary endpoint will be patient-macro 60-minute root mean squared error after three days of adaptation, with clinical error profiles and negative-transfer rates as key secondary outcomes. Expected outputs are a reproducible benchmark, evidence on the value and limitations of short-history personalization, and an independently publishable manuscript. Performance improvements are hypotheses, not promised results.

## 1. Background and motivation

Personalized glucose prediction already has substantial prior art. A 2024 study combined CGM, activity and treatment information in a personalized deep-learning framework [1]. CGM-LSM demonstrated large-scale CGM pretraining and evaluation on OhioT1DM [2]. A January 2026 preprint explicitly compared subject-held-out evaluation with fine-tuning and reduced amounts of patient-specific training data [3]. These developments rule out presenting either “use a Transformer” or “fine-tune on a patient” as sufficient novelty.

MetaboNet offers a timely opportunity to study transfer across source cohorts: its 2026 preprint describes 3,135 subjects and 1,228 patient-years across public and agreement-restricted components [4]. These totals describe the consolidated resource, not an immediately available analytic sample. The scientific opportunity is to examine where personalization helps under actual cohort shift, under equal information and computation budgets.

CGM measures interstitial glucose. Forecasting agreement with CGM is not proof of blood-glucose accuracy, treatment effectiveness or improved clinical outcomes.

## 2. Explicit research gap and positioning

**Proposed gap:** There remains a testable need for a source-cohort-disjoint, patient-level evaluation of inexpensive personalization that fixes adaptation duration, prevents retrospective leakage, compares against strong conventional fine-tuning, and reports the distribution of benefit and harm across patients.

This is a proposed evaluation and method contribution, not a claim that cross-dataset transfer or personalization has never been studied. Before protocol registration, inspect the full text, supplements and code of [2–4] and update the literature matrix through the actual search date. If an identical protocol exists, narrow the contribution to reproducibility, negative transfer and adaptation cost rather than claiming a first.

| Relevant work | Established direction | Distinction to test here |
|---|---|---|
| Personalized multimodal forecasting, 2024 [1] | Patient-specific learning | Same CGM-only inputs and fixed adaptation budgets across independent cohorts |
| CGM-LSM, 2025 [2] | Pretrained transferable representations | Compact adaptation with auditable pretraining provenance and resource accounting |
| Patient-specific adverse-event prediction, 2026 preprint [3] | Fine-tuning and limited patient data | Dataset-level exclusion, identical test periods and negative-transfer analysis |
| MetaboNet, 2026 preprint [4] | Harmonized data access | Treat constituent cohorts as domains; do not mistake duplicated data for external validation |

## 3. Problem statement, aim and objectives

Given CGM history available at time t and at most b days of earlier data from a previously unseen patient, predict glucose at t+30 and t+60 minutes. Future meals, insulin doses and CGM values are unavailable. The model must transfer across source datasets without accessing their evaluation outcomes.

**Aim:** Establish whether budget-constrained personalization improves external-dataset forecasting reliably enough to justify its data and compute cost.

**Objectives:**

1. Build an auditable common schema and source-level overlap register.
2. Establish strong patient-disjoint and dataset-disjoint CGM-only baselines.
3. Develop a regularized residual adaptation method requiring few trainable parameters.
4. Quantify benefit after 0, 1, 3 and 7 days, using matched evaluation periods.
5. Measure clinical error patterns, heterogeneity, adaptation failures and deployment cost.
6. Release permitted code, split specifications, provenance records and a manuscript.

## 4. Research questions and hypotheses

| Question | Prespecified hypothesis or analysis |
|---|---|
| RQ1: Does three-day adaptation improve unseen-dataset forecasts? | H1: Lower patient-macro 60-minute RMSE than the identical frozen backbone. |
| RQ2: Is constrained adaptation preferable to full fine-tuning? | H2: Lower negative-transfer frequency and lower adaptation cost; accuracy differences are tested separately. |
| RQ3: How much history is useful? | Estimate paired 0/1/3/7-day learning curves; do not assume monotonic gains. |
| RQ4: Who benefits or deteriorates? | Exploratory interactions with source, baseline variability, missingness, device and available demographics. |
| RQ5: Are numerical gains clinically aligned? | Compare low-glucose errors and clinically risky error-grid regions; no claim of clinical benefit from RMSE alone. |

The sole confirmatory comparison is H1 at 60 minutes with three-day adaptation on the prespecified external cohort. A 5% relative RMSE reduction may be used as a planning target, not an established clinical minimum important difference.

## 5. Novelty and expected contributions

- A reproducible protocol separating unseen-patient, unseen-cohort and personalized evaluation.
- A compact residual adaptation head with shrinkage toward the population predictor, evaluated against simpler alternatives.
- A matched-period personalization curve that separates extra historical information from differences in test difficulty.
- Patient-level negative-transfer and subgroup analyses, with uncertainty around effect sizes.
- Reproducible compute and data-access accounting suitable for a single workstation.

The combination must demonstrate value empirically. If the proposed head does not beat conventional fine-tuning, the benchmark and failure analysis remain useful, but the manuscript should not claim architectural superiority.

## 6. Datasets and access

| Resource | Role and characteristics | Access and practical limitation |
|---|---|---|
| DiaTrend [5] | Initial development candidate; 54 T1D participants, longitudinal CGM and pump records | Hosted on Synapse [6]. Check account, access conditions and license before download. Basal data exist for only 17 participants; use CGM-only primary inputs. |
| OhioT1DM [7] | Small reproducibility and transfer benchmark; 12 participants across releases | Obtain through the official dataset process and applicable agreement. Do not assume anonymous unrestricted download. Small patient count limits inference. |
| Adult T1DEXI [8] | Preferred independent external cohort; exercise context adds a useful distribution shift | Listed in the official Jaeb public-data catalog. Follow dataset terms and attribution requirements; audit actual usable CGM and participant counts. Do not substitute pediatric T1DEXIP. |
| MetaboNet [4] | Optional expansion using explicitly selected source cohorts | Public subset plus agreement-restricted components. Public files do not imply access to all reported subjects. Original and harmonized copies are the same underlying data. |

**Minimum feasible design:** Develop on DiaTrend and reserve T1DEXI as the locked external cohort, subject to access and suitability. Add OhioT1DM for replication. With at least three independent cohorts, perform leave-one-dataset-out analyses as secondary experiments. If only one cohort is available, the cross-dataset claim is infeasible; change the scope explicitly rather than using a random split as a substitute.

Week 1–2 access audit records version, download date, source study, patient identifiers, time coverage, sensor cadence, license and exclusions. Dataset access has not been obtained as part of this proposal. Aggregate counts above are source descriptions, not final enrollment. Select the external cohort for suitability before examining model performance.

## 7. Inclusion and preprocessing

Primary population: adults with confirmed T1D where age and diagnosis are available. Treat unknown age separately; exclude pregnancy or inpatient cohorts where identifiable. For the main matched-budget cohort require at least 14 calendar days and sufficient valid history for seven-day adaptation plus subsequent evaluation. Document the selection bias this introduces. Report shorter-history patients separately.

1. Convert glucose to mg/dL using a documented unit conversion; retain original units. Resolve duplicate timestamps deterministically, flag device replacement and timezone ambiguity, and preserve source IDs.
2. Form a five-minute grid only when compatible with native sampling. Assign bins using measurements already available by the bin endpoint. Do not upsample a 15-minute sensor to fabricate five-minute targets.
3. Input window: previous six hours (72 five-minute steps); compare two and 24 hours in ablations. Include glucose, missingness mask, elapsed time since valid observation, and time-of-day sine/cosine only when local time is reliable.
4. Permit forward fill for at most 10 minutes in input history; retain masks. Reject windows with longer gaps or more than 10% missing input samples in the main analysis. Vary this threshold in sensitivity analyses.
5. Never impute outcome values. Require observed horizon targets; require complete observed future segments for trajectory or event analyses. Preserve genuine low values and device boundary flags rather than removing physiologically important extremes.
6. Fit scaling on source training patients only. Any personal centering must use only the allowed adaptation prefix and be identical across methods.
7. Sample patients uniformly and training windows at a 15-minute stride to control domination by long records. Evaluate all eligible five-minute origins. Report eligible wear time and exclusions by cohort.

These are study design choices, to freeze after a source-only feasibility pilot. They are not universal clinical quality criteria.

## 8. Methodology and architecture

### Population model

Use a causal temporal convolutional network (TCN) with residual blocks, kernel width 3, dilations 1/2/4/8/16/32 and width 64. A final representation feeds a direct 12-output head for the next hour. Train with mean squared error over observed targets, patient-balanced batches and AdamW. Select learning rate, regularization and early stopping on source validation patients. Target less than two million parameters; measure the implemented count.

```text
Past CGM + masks + elapsed time + valid clock features
                         |
                 causal TCN encoder
                         |
               population forecast head
                         +
             regularized personal residual head
                         |
                  12 future CGM values
```

### Budget-constrained personalization

Freeze the encoder and population head. Fit a zero-initialized low-rank residual head, rank 4–8, on the first b calendar days of the new patient's record. Its output corrects the population trajectory. Penalize adapter weights toward zero:

`L_adapt = MSE(adapted forecast, observed future CGM) + lambda * ||adapter||²`.

Choose lambda, rank, optimizer and a maximum of 200 updates using only source pseudo-new-patient experiments. Use all eligible labeled windows contained completely within the permitted prefix. When history is insufficient, retain the population model and report the fallback frequency. No patient-specific optimization against the external evaluation suffix is allowed.

The primary model is frozen after prefix adaptation. Rolling daily updates are a separate exploratory protocol, with update labels available only after their forecast horizon has elapsed.

### Optional treatment inputs

Add documented past bolus, basal and carbohydrate records only in a matched-subset secondary experiment. Distinguish no recorded event from missing logging. No future treatment, back-entered meals or full-day aggregates are permitted. The main contribution must remain feasible with CGM alone.

## 9. Baseline models

1. Persistence and clipped linear trend extrapolation from recent CGM; fit clipping limits on training data.
2. Ridge autoregression and gradient-boosted trees using identical causal lag features.
3. Compact LSTM or GRU with matched input duration and training budget.
4. Population TCN without adaptation.
5. Population TCN with only an output bias correction, then ordinary head fine-tuning.
6. Population TCN with full-network fine-tuning under the same adaptation prefix.
7. Patient-only compact model trained solely on that prefix, as a data-limited comparator.
8. CGM-LSM inference/adaptation only if usable checkpoints, license and pretraining provenance can be established [2]. Architecture reimplementation is not equivalent to using the published pretrained model.

All baselines receive the same forecast origins, available information and selection opportunities. Do not compare published headline scores directly against locally measured scores under different splits.

## 10. Experimental design and leakage prevention

**Internal development:** Divide source patients 70/15/15 into training, validation and internal test groups with a fixed seed. For very small cohorts use grouped folds instead. Tune on training/validation only. Source validation patients simulate adaptation without contributing to population training.

**External primary experiment:** Hold out the entire T1DEXI cohort before development, including any copy within MetaboNet. Fit every preprocessing parameter and hyperparameter on source data. Each external patient contributes only their declared adaptation prefix; later observations are evaluation outcomes.

**Matched evaluation:** Use the first 1, 3 or 7 days as nested adaptation prefixes, but score every budget on the same suffix beginning after day 7 plus a seven-hour purge (six-hour input plus one-hour target support). Thus different adaptation budgets are not tested on different clinical periods. A secondary early-deployment analysis can start after each budget, clearly labeled as a different estimand.

**Boundary rules:** Construct splits on raw records before generating windows. Require all window input and target timestamps to belong to the allowed partition, using purges where needed. Do not use centered smoothing, bidirectional imputation, whole-record normalization, future demographics inferred from outcomes, or random window splits. Test inputs may include earlier observed test CGM because this is rolling forecasting; test outcomes may not update the model in the frozen protocol.

Keep an overlap register across source studies and harmonizations. Remove suspected duplicates conservatively when identity cannot be resolved. Foundation-model pretraining on the target cohort invalidates a clean zero-shot external claim. Freeze the analysis plan, code version and model-selection rule before unlocking external results.

## 11. Metrics and clinical interpretation

**Primary:** Compute 60-minute RMSE separately per patient, then average equally across external patients. Report the paired difference versus the unadapted backbone in mg/dL and percent.

**Secondary numerical:** 30-minute RMSE, MAE, bias, full-trajectory error and per-dataset macro scores; sample-weighted scores only as supplementary results.

**Clinical context:** Report errors stratified by observed future glucose <70, 70–180 and >180 mg/dL, plus <54 mg/dL where supported [9]. Include dangerous overprediction during true lows, low-event sensitivity and false alerts per patient-day for a prespecified forecast-threshold rule. Use Clarke error-grid regions as a descriptive comparator, acknowledging that paired glucose measurement grids are not validated evidence of forecasting benefit. Report time-in/below-range estimation error only over defined complete future trajectories, not as an inferred treatment effect.

**Personalization:** Fraction of patients whose RMSE worsens; distribution of paired changes; adaptation seconds, parameter count, peak VRAM and inference latency. Define meaningful worsening as >5% relative RMSE for a secondary sensitivity analysis, not a validated clinical threshold.

## 12. Ablations and explainability

Prespecified ablations: adaptation budget; residual head versus ordinary head; with/without shrinkage; frozen versus full encoder; two/six/24-hour history; masks versus no masks; patient-balanced versus window-balanced training; CGM-only versus matched treatment-input subset. Evaluate one change at a time before selected interactions.

Use temporal occlusion and integrated gradients on representative good, bad and negative-transfer cases. Check attribution stability across seeds and small plausible perturbations. Explain contributions of recent level and trend; do not interpret attribution as physiological causation. Attention visualizations, if an alternative Transformer is used, are not sufficient explanations.

Prediction intervals are optional supporting analyses: calibrate horizon-specific residual quantiles on source validation patients and assess external coverage and width. They are not required for this forecasting project and do not create a claim of guaranteed coverage under shift.

## 13. Statistical analysis and sample adequacy

Average each method's patient-level metric across three training seeds, then calculate paired effects. Obtain 95% confidence intervals with 2,000 patient-cluster bootstrap replicates, retaining all records for each sampled patient. Where multiple source cohorts are reported, show cohort-specific results; three cohorts do not justify precise population-of-datasets inference.

Use a paired patient-level permutation test for H1, with two-sided alpha 0.05. Apply Holm adjustment within prespecified secondary comparison families. Treat subgroup interactions and architecture searches as exploratory. Seeds and overlapping windows are not independent sample units.

Before external access is unlocked, estimate the variance of paired patient differences from source validation data and simulate confidence-interval widths for plausible external patient counts. Publish a precision-based justification; millions of windows do not substitute for independent patients. Report missing metrics explicitly when patients have no eligible low events.

## 14. Compute requirements and estimated time

Assume the stated 16 GB GPU, mixed precision, batches of 64–128, 32 GB host RAM minimum (64 GB preferred) and 100–200 GB free SSD for source files and caches. Actual capacity depends on GPU power limits, CPU throughput and data size. No large foundation-model pretraining is planned.

| Stage | Planning estimate |
|---|---|
| Data conversion and audit | 4–12 CPU-hours, plus human inspection |
| Pilot run, 50k–100k training windows | 15–60 GPU-minutes |
| Compact final fit, 0.2–0.5 million sampled windows, up to 30 epochs | 0.5–2 GPU-hours |
| Main baselines, three seeds, bounded tuning and adaptation sweeps | 30–70 GPU-hours |
| Selected ablations, external replication and evaluation | 18–50 GPU-hours |
| Total core study | Approximately 48–120 GPU-hours (2–5 continuous GPU-days) |
| Larger multi-cohort extension | Approximately 120–240 GPU-hours total |

These are unmeasured engineering estimates, not hardware benchmarks. Budget roughly 10–90 GPU-hours for 20–45 full-fit equivalents at the stated per-fit range, with the remainder for adapters and evaluation. Measure 1,000 training steps first and extrapolate: `steps per epoch × epochs × measured seconds per step`, adding 30% contingency. Cap search at 12 source-only trials. Stream windows instead of duplicating overlapping arrays. If time exceeds budget, reduce trials and training stride density while preserving test coverage, strong baselines and independent evaluation.

## 15. Implementation stack and reproducibility

Python; PyTorch with a compatible CUDA build; NumPy, pandas or Polars and PyArrow; scikit-learn and XGBoost; Optuna for bounded tuning; Captum for attribution; SciPy/statsmodels for analysis; Matplotlib for figures. Pin tested versions in an environment lock file after a workstation smoke test. Track configurations, seeds and checkpoints locally with MLflow or structured logs.

Deliver schema validators, time-boundary and overlap checks, patient split manifests, preprocessing configurations, a single-command evaluation entry point, metric definitions and a model card. Redistribute only data and identifiers permitted by the source terms.

## 16. Timeline

| Weeks | Work and decision gate |
|---|---|
| 1–2 | Literature matrix, access requests, provenance audit; confirm two independent usable cohorts |
| 3–4 | Causal preprocessing, exclusions, source-only pilot and protocol registration |
| 5–6 | Baselines and population model; freeze tuning budget |
| 7–8 | Adapter and source pseudo-new-patient validation |
| 9–10 | Freeze analysis, run external evaluation and matched-budget experiments |
| 11–12 | Ablations, statistics, subgroup and failure analyses |
| 13–14 | Manuscript, reproducibility package and supervisor/clinical review |

Access delays can extend calendar time without increasing GPU time. Start with schema and baseline work while applications are pending.

## 17. Risks, mitigations and ethics

| Risk | Mitigation |
|---|---|
| Source overlap disguised as external validation | Cohort register and conservative removal of harmonized duplicates |
| Insufficient long histories | Report selection flow and a shorter-budget supplementary cohort |
| Negative transfer | Shrinkage, source-selected adaptation limits, explicit worsening rates and fallback |
| Device or treatment-policy confounding | CGM-only primary model, device/source strata and cautious causal language |
| Weak novelty | Direct comparison with 2026 work and focus on demonstrable protocol differences |
| Small subgroups | Confidence intervals and suppression of unstable comparisons |

Obtain the institution's ethics determination for secondary analysis; public availability alone does not establish exemption. Follow access agreements, use encrypted storage, restrict access and avoid reidentification. Publish no identifiable traces or prohibited participant-level records. Assess demographic missingness and underrepresentation. This is retrospective prediction research, not an insulin-dosing or clinical alert system; prospective validation is required before clinical use.

## 18. Expected outcomes and publication strategy

Expected deliverables are a cohort audit, a leakage-controlled benchmark, adaptation learning curves, paired patient effect plots, a clinical error analysis, resource measurements and one manuscript. A well-supported null finding would show when extra patient history or complexity does not help.

Frame a conference submission around the compact method and controlled external evaluation; frame a journal submission around broader cohort validation, negative transfer and reproducibility. Candidate venue families include biomedical engineering conferences and diabetes technology or biomedical informatics journals. Select the specific venue after reviewing current scope, author guidance and fees; no deadline or acceptance is assumed. Report using TRIPOD+AI [10]. If a conference paper precedes a journal extension, disclose it and add substantial new validation rather than republishing the same study.

## References and resource links

Sources checked on 12 September 2026. This is a targeted literature check, not a systematic review. Preprints are explicitly marked; older references establish data provenance.

1. Kalita D, Sharma H, Panda JK, Mirza KB. **Platform for precise, personalised glucose forecasting through continuous glucose and physical activity monitoring and deep learning.** Medical Engineering & Physics, 2024. [DOI](https://doi.org/10.1016/j.medengphy.2024.104241).
2. Luo J et al. **A large sensor foundation model pretrained on continuous glucose monitor data for diabetes management.** npj Health Systems, 2025. [Article](https://www.nature.com/articles/s44401-025-00039-y); [official code](https://github.com/JHU-CDHAI/cgmlsm). Verify checkpoint availability separately from code availability.
3. **Tailoring Adverse Event Prediction in Type 1 Diabetes with Patient-Specific Deep Learning Models.** 2026 preprint. [arXiv:2601.14917](https://arxiv.org/abs/2601.14917).
4. Wolff MK et al. **MetaboNet: The Largest Publicly Available Consolidated Dataset for Type 1 Diabetes Management.** 2026 preprint. [arXiv:2601.11505](https://arxiv.org/abs/2601.11505); [resource](https://metabo-net.org/).
5. **DiaTrend: A dataset from advanced diabetes technology to enable development of novel analytic solutions.** Scientific Data 10, 556, 2023. [DOI](https://doi.org/10.1038/s41597-023-02469-5).
6. **DiaTrend data repository.** [Synapse project syn38187184](https://www.synapse.org/Synapse:syn38187184).
7. **OhioT1DM dataset and blood glucose prediction challenges.** [Official investigator site](https://webpages.charlotte.edu/rbunescu/data/ohiot1dm/homepage.html).
8. Jaeb Center for Health Research. **Adult T1DEXI public dataset.** [Dataset DOI](https://doi.org/10.25934/PR00008428); [official diabetes catalog](https://public.jaeb.org/datasets/diabetes).
9. American Diabetes Association Professional Practice Committee. **6. Glycemic Goals, Hypoglycemia, and Hyperglycemic Crises: Standards of Care in Diabetes—2026.** Diabetes Care, 2026. [DOI](https://doi.org/10.2337/dc26-s006).
10. Collins GS et al. **TRIPOD+AI statement: updated guidance for reporting clinical prediction models that use regression or machine learning methods.** BMJ 385:e078378, 2024. [DOI](https://doi.org/10.1136/bmj-2023-078378).
