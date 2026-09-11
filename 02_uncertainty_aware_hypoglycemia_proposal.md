# Uncertainty-Aware Hypoglycemia Prediction with Calibrated Event Risk and Explicit Alarm-Burden Evaluation

**Independent publication-oriented project proposal · 12 September 2026**  
**Scope:** Retrospective adult type 1 diabetes (T1D); advance prediction of CGM-defined hypoglycemic events.  
**Resources:** One NVIDIA RTX 2000 Ada GPU with 16 GB VRAM; approximately 16 weeks.

## Abstract

Hypoglycemia prediction requires more than accurate glucose forecasts: a useful warning system must identify impending events with adequate lead time while limiting false alarms and communicating unreliable predictions. This project will develop a compact ensemble for direct prediction of CGM-defined hypoglycemia onset within 30 minutes, with 60-minute prediction as a secondary task. Patient-disjoint calibration will convert model scores into assessed event-risk probabilities. A prespecified alarm policy will be evaluated on a completely held-out cohort under a fixed false-alarm operating target, and an experimental uncertainty-based deferral policy will be assessed with explicit accounting for missed events during deferral. Comparators will include trend rules, gradient boosting, deterministic neural models, Monte Carlo dropout and forecast-derived event scores. Evaluation will separate probability calibration, event discrimination, episode-level sensitivity, lead time and alarm burden. Conformal classification will be examined as a secondary uncertainty representation, with no claim of distribution-free safety under temporal dependence or cohort shift. The expected contribution is an independently reproducible study of whether uncertainty improves practical event-warning behavior beyond ordinary calibrated probabilities. The work is retrospective and cannot establish prevention of hypoglycemia or readiness for clinical deployment.

## 1. Background and motivation

CGM creates opportunities to anticipate low-glucose episodes, but overlapping prediction windows, rare events and repeated alerts can make apparently strong classifier performance clinically misleading. The 2026 ADA Standards distinguish glucose below 70 mg/dL from level 2 hypoglycemia below 54 mg/dL; level 3 is defined by the need for assistance, not a CGM threshold [1]. This proposal therefore targets sensor-defined events and does not label a low CGM reading as a confirmed severe clinical episode.

Recent work already applies uncertainty methods to glucose prediction. Tan and McBeth's March 2026 preprint compares Monte Carlo dropout and evidential output heads in recurrent and Transformer models on HUPA-UCM [2]. A 2026 IEEE Access article combines physiological constraints, conformal risk control and deferral [3]. Consequently, adding dropout, evidential regression or conformal prediction alone is not a convincing novelty claim.

The proposed emphasis is event-level evaluation under external shift: does uncertainty add useful warning behavior after the underlying risk score has already been calibrated, and what are the consequences when the model declines to make a confident prediction?

## 2. Explicit research gap

**Proposed gap:** A need remains for controlled comparisons of calibrated direct event prediction and uncertainty-based deferral across independent CGM cohorts, using fixed alarm rules, episode-level matching, realistic lead-time requirements and complete accounting for abstained monitoring periods.

The gap is an empirical proposition to verify against full texts and code, not a declaration that uncertainty-aware hypoglycemia prediction is new. In weeks 1–2, build a comparison matrix for [2–4] and newer papers covering datasets, patient separation, calibration data, event definitions, false-alarm denominators and deferral handling. If existing work already answers the proposed question, reposition around reproducibility or a distinct external population.

| Existing direction | Proposed distinction |
|---|---|
| Uncertainty in numerical glucose prediction [2] | Direct onset-risk calibration and episode-level alert evaluation |
| Conformal risk control and deferral [3] | Explicit missed-event accounting during deferral and comparison with calibrated score-only rules |
| Personalized adverse-event models [4] | Frozen population event model; no patient-specific forecasting model is required |

This project is independent of any personalized forecasting proposal. It has its own models, split files, endpoints, code and manuscript; no output from another project is a prerequisite.

## 3. Problem statement and outcome definition

At each eligible five-minute time t, estimate the probability that a **new CGM-defined hypoglycemia episode begins in (t, t+30 minutes]**, using only the preceding six hours of observed information. The 60-minute horizon is secondary. Current lows and ongoing episodes are handled as detection states and excluded from onset-risk evaluation.

**Operational episode definition:** A low episode starts at the first of at least three consecutive observed five-minute readings below 70 mg/dL, representing 15 minutes under a five-minute-bin convention. It ends after three consecutive readings at or above 70 mg/dL. Apply this rule on regular five-minute streams; do not imply that three samples span 15 minutes between their timestamps. Merge interruptions shorter than the recovery criterion. Prespecify a sensitivity analysis requiring four readings (15 minutes between first and last timestamps), and another using any single observed threshold crossing.

Label onset retrospectively at the first qualifying low reading, not when persistence becomes confirmed. Require outcome observation through H+10 minutes to confirm an onset near the horizon boundary. Missing or indeterminate outcomes are not negative labels. An analogous <54 mg/dL definition is a secondary endpoint, labeled **level-2-threshold CGM episodes**, not clinically verified level 3 hypoglycemia [1].

## 4. Aim, objectives, research questions and hypotheses

**Aim:** Determine whether calibrated uncertainty improves external-cohort hypoglycemia warning performance without hiding missed events or excessive alarm burden.

**Objectives:**

1. Create auditable onset labels and eligible monitoring-time denominators.
2. Train compact probabilistic event models with strict patient separation.
3. Compare probability calibration, ensemble disagreement and conformal prediction sets.
4. Evaluate frozen alert thresholds on an independent dataset.
5. Quantify event sensitivity, false alarms, lead time, deferral and subgroup reliability.
6. Publish reproducible code, an evaluation protocol and one complete manuscript.

| Research question | Hypothesis or prespecified analysis |
|---|---|
| RQ1: Does an ensemble improve probability quality? | H1: Lower external Brier score than the matched deterministic network after equivalent calibration. |
| RQ2: Does the final warning pipeline improve event detection? | H2: Higher 30-minute event sensitivity than calibrated gradient boosting at source-selected alarm operating points. |
| RQ3: Does uncertainty add value beyond probability alone? | Compare risk/coverage and full-stream alert behavior against probability-only selection at matched retained coverage. |
| RQ4: Does calibration transfer across datasets? | Quantify calibration degradation; no assumption of preserved external calibration. |
| RQ5: Who experiences unreliable or missed predictions? | Explore low-event burden, nighttime, sensor gaps and available demographic strata. |

H2 is the primary clinical-performance comparison. H1 is a key secondary comparison. A useful result must report both sensitivity and external alarm burden; exceeding the operating budget prevents claiming a sensitivity improvement “at the budget.”

## 5. Novelty and contributions

- An onset-risk benchmark separating window discrimination from actual episode warnings.
- A controlled test of whether ensemble uncertainty adds value after probability calibration.
- An external-cohort protocol with locked thresholds, matched warning rules and no target-label tuning.
- An analysis of uncertainty deferral that retains missed events and monitoring gaps in the accounting.
- A single-GPU implementation with clinical error review and reproducible resource measurements.

No new theorem, guaranteed safety, clinical effectiveness or first-ever uncertainty method is claimed. A null result showing that calibration or simple trend rules match a complex uncertainty method is scientifically useful.

## 6. Datasets and access/availability

| Dataset | Planned role | Availability and limitations |
|---|---|---|
| DiaTrend [5,6] | Initial model development and source calibration | 54 participants, longitudinal CGM and treatment records; Synapse-hosted. Check account requirements and data terms. Count events after preprocessing; nominal participant count does not establish event adequacy. |
| Adult T1DEXI [7] | Preferred locked external cohort | Official Jaeb catalog provides an access route. Follow its terms and attribution rules. Exercise-related context provides a useful shift but can limit generalization beyond that setting. |
| OhioT1DM [8] | Additional small-cohort replication | Official access process and agreement apply. Too small to be the sole basis for precise safety or subgroup claims. |
| MetaboNet [9] | Optional additional source cohorts | 2026 preprint reports 3,135 subjects and 1,228 patient-years across public and restricted components. Select only approved, nonoverlapping source cohorts. |

The core plan uses CGM alone, avoiding dependence on complete insulin or meal logs. DiaTrend's incomplete basal coverage makes treatment features a secondary matched-subset study. No access or download is assumed to have been completed for this proposal.

**Feasibility gate:** Confirm at least two independent suitable cohorts and estimate event counts on development data before committing to model complexity. If source data cannot support distinct calibration and policy-selection groups, expand through an approved independent source or use source-only grouped cross-fitting. Do not borrow external test labels. If external events are sparse, report descriptive estimates with wide intervals and reduce claims.

## 7. Inclusion and preprocessing strategy

Primary analysis includes adults with documented T1D and at least 10 calendar days of CGM with 70% observed coverage as a research eligibility rule. Report shorter or sparser histories separately, with exclusion counts. Unknown-age records are not silently classified as adults. Do not exclude individuals simply because they have no low events: they contribute to calibration and alarm-burden estimation.

Normalize units, resolve duplicate timestamps, retain sensor/source identifiers, and use a causal five-minute grid when supported by native cadence. Analyze incompatible sampling frequencies separately rather than fabricating event persistence. Flag device limits, suspected artifacts and timestamp discontinuities; use source-only artifact rules and a sensitivity analysis retaining flagged observations.

Inputs contain six-hour CGM history, recent slopes, missingness masks, elapsed time since measurement and reliable local clock features. Use at most 10-minute forward fill in inputs, never in labels; reject windows with longer gaps or more than 10% missing inputs in the main protocol. Keep exclusions and unavailable monitoring time visible.

Fit scaling on training patients only. Retain all positives and the natural negative prevalence for calibration, policy selection and testing. If negative downsampling is needed in training, record inclusion probabilities and use inverse-probability weighting; validate calibration on untouched natural-prevalence data. Do not apply SMOTE to overlapping time-series windows.

## 8. Methodology and architecture

### Core probabilistic model

Use a compact causal TCN: residual blocks with dilations 1/2/4/8/16/32, width 64 and dropout 0.1–0.3. A shared representation produces two sigmoid outputs for onset within 30 and 60 minutes. Each output uses its own endpoint label. Train with binary cross-entropy and patient-balanced sampling; use an optional source-tuned consistency penalty if 30-minute risk exceeds 60-minute risk. Target fewer than two million parameters per member.

Train three independently initialized members sequentially on the same training patients, with independently sampled patient-balanced minibatches. Average their probabilities. Between-member variance provides a model-disagreement score, not a complete or uniquely identifiable measurement of epistemic uncertainty.

```text
Six hours of CGM, masks, elapsed time and reliable clock features
                              |
                   Three compact TCN models
                              |
               Mean event risk + disagreement
                              |
                 Held-out probability calibration
                              |
             Locked alarm policy / experimental deferral
                              |
            Episode matching, lead time and burden analysis
```

### Probability calibration

Fit a logistic recalibration mapping on held-out source calibration patients, separately per horizon, using the logit of ensemble mean probability. Compare uncalibrated and calibrated scores. Consider temperature scaling as a simpler alternative; choose the calibrator using development data before the calibration split is touched. Use isotonic calibration only if source event support is adequate and selected prospectively.

Use a common monotonic calibrator for member probabilities when studying calibrated member spread. Clearly distinguish discrimination, calibration and uncertainty ranking: a monotonic recalibrator can improve Brier score without improving AUROC or alert ranking.

### Experimental deferral and conformal analyses

The primary pipeline predicts on every eligible origin and uses no deferral. In a secondary policy, defer when disagreement exceeds a source-selected cutoff. Report a probability-only ambiguity rule as a control at the same retained coverage.

For retrospective system accounting, a deferred origin produces no model warning. Events lacking another qualifying warning count as missed in full-stream sensitivity. Also report results for a separately specified simple trend-rule fallback, including all its false alarms. Do not assume a clinician resolves every deferral or that deferral is automatically safe.

Optional split-conformal classification uses nonconformity `1 − probability of the observed class` and an untouched source conformal-calibration partition. Use the finite-sample quantile with rank `ceil((n+1)(1−alpha))`, treating insufficient calibration support conservatively. Evaluate 90% and 95% sets, ambiguous sets, empty sets and class-conditional coverage. Patient-balanced or block-sampled calibration reduces domination by long records but does not establish exchangeability. Report this as empirical coverage analysis; marginal conformal coverage does not guarantee per-patient sensitivity, alarm burden or coverage under external shift.

## 9. Baselines

1. Current glucose threshold as a detection reference; label its lack of advance warning clearly.
2. Linear glucose extrapolation and a low-glucose/rate-of-fall rule, tuned only on source policy data.
3. Logistic regression and gradient-boosted trees using causal CGM summary and lag features.
4. Deterministic TCN with the same architecture and calibration procedure.
5. Matched recurrent model with Monte Carlo dropout, approximately 20 inference passes.
6. Three-member TCN ensemble without recalibration and with recalibration.
7. Compact quantile glucose forecaster whose outputs produce an event score. If using only marginal quantiles, treat threshold crossing as a score requiring calibration; do not multiply marginal probabilities as if future samples were independent.
8. Evidential regression comparator informed by [2], if full implementation and time permit. Its output uncertainty is not assumed trustworthy without calibration checks.

Use identical eligible origins, labels and policy rules. Reproduce the relevant portions of [3] only when sufficiently specified; label adaptations rather than implying an exact reproduction.

## 10. Experimental design and train/validation/test strategy

Assign source patients approximately 55/15/15/15% to training, hyperparameter validation, probability calibration and alarm-policy selection. Use fixed patient-level allocation and assess event adequacy without inspecting external performance. With few participants, use grouped cross-fitting confined to source data to produce out-of-fold calibration and policy predictions; keep an untouched external cohort in all cases.

The entire external cohort is excluded from representation learning, preprocessing fitting, calibration, threshold selection and early stopping. Exclude its MetaboNet copies as well. Remove duplicate source records and conservatively treat unresolved suspected overlap. Do not reuse test results to select the winning model; choose the primary ensemble configuration on source validation.

Create partitions before windows. Keep input and outcome support wholly within their permitted partition. For chronological partitions, purge at least six hours plus the maximum horizon plus 10-minute event confirmation. Keep adjacent parts of an episode in the same partition. No centered filters, whole-record normalization, future insulin/meal information or random window splits.

Run three seeds for single-model comparators. Ensemble member seeds are components of one ensemble, not three independent experiment replications. If repeating ensembles, train three separate member groups and report the additional compute. Primary uncertainty intervals remain patient-cluster based.

**External recalibration extension:** Only if prespecified, use a separate group of target calibration patients and evaluate on different target patients. Report this as supervised target calibration, not frozen external generalization. Avoid making personalization a dependency of the project.

## 11. Alarm policy and clinical metrics

An alarm is issued when calibrated risk exceeds the source-selected threshold. Use a 30-minute refractory period after an alarm; do not issue onset warnings during an ongoing low episode. Freeze all thresholds and refractory rules before external evaluation. Select the threshold yielding the highest source event sensitivity subject to at most **0.5 false alarms per eligible patient-day**, preferably using an upper patient-bootstrap confidence bound. This is a research operating target, not a clinically established acceptable burden. If no useful policy meets it, report that result.

For 30-minute forecasting, a true advance warning must occur 5–30 minutes before onset; 60-minute evaluation permits 5–60 minutes. Match each alarm to at most one event and each event to at most one earliest qualifying alarm. Unmatched alarms are false alarms; report duplicate warnings separately where applicable. An alert issued less than five minutes before onset is a late warning, not a successful advance prediction.

**Primary endpoint:** Episode sensitivity on the locked external dataset at the source-selected operating point. Report achieved external false-alarm rate beside it. Do not tune external thresholds to force 0.5/day. A test-set threshold sweep is descriptive only.

**Other clinical metrics:** Alarm precision; false alarms per 24 hours of eligible monitoring; total alarms/day; median and interquartile warning lead time; missed episodes; late-warning rate; nighttime performance (00:00–06:00 local time only when trustworthy); level-2-threshold event sensitivity; alarm refractory time; and percent total wear time excluded for data quality. Report both pooled event sensitivity and patient-macro sensitivity among patients with events. Event-free patients remain in alarm and calibration analyses.

**Probability metrics:** AUPRC with event-window prevalence, AUROC as secondary, Brier score, log loss, calibration intercept/slope and reliability plots. Report expected calibration error only with bin definitions and sensitivity to binning.

**Uncertainty metrics:** Retained coverage versus error, full-stream event sensitivity versus deferral rate, error enrichment among high-disagreement origins, and conformal set coverage/size. Stratify cautiously by cohort, event class and available demographics. Do not call retained-subset performance overall safety.

## 12. Ablations and explainability

Prespecified ablations: one versus three ensemble members; uncalibrated versus calibrated scores; probability-only versus disagreement-based selection; CGM level/trend summaries versus the full sequence; two versus six-hour history; missingness inputs; dropout versus ensemble uncertainty; single-crossing versus persistent-event labels; 15/30/60-minute refractory periods; source-only versus separately labeled target recalibration.

Use SHAP for tree models and temporal occlusion or integrated gradients for the neural model. Review false alarms, missed events, confident errors and high-uncertainty episodes with a diabetes-domain collaborator. Present recent glucose and trends beside risk and disagreement; test explanation stability across seeds. Attributions describe model behavior, not causes of hypoglycemia, and do not justify treatment recommendations.

## 13. Statistical analysis and sample-size justification

Use 2,000 paired patient-cluster bootstrap replicates, retaining each patient's complete episodes and monitoring stream. Compute 95% confidence intervals for sensitivity differences, false-alarm rates, lead time and Brier-score differences. Use paired patient-label permutation for the primary comparison where appropriate, and Holm correction for secondary confirmatory families. Treat subgroup and ablation tests as exploratory.

Episodes within a patient and overlapping windows are dependent. Neither window counts nor ensemble members determine the effective patient sample size. Use source event rates and patient-level variability to simulate expected interval widths before external analysis. As a rough illustration only, 100 independent events at 80% sensitivity yield a binomial standard error of about 4 percentage points; clustering makes precision worse. Aim for at least 100 external episodes distributed over at least 30 patients as a feasibility target, then justify adequacy through cluster simulation. This is not a guaranteed power calculation.

If <54 mg/dL episodes or subgroup events are rare, provide counts and intervals without confirmatory superiority claims. A higher AUPRC alone does not establish a better alarm policy. Report when improved calibration fails to improve event detection.

## 14. Compute requirements and estimated time

Assume mixed precision, batch size 64–128, fewer than two million parameters per member, 32–64 GB system RAM and 100–200 GB free SSD. Train ensemble members sequentially so three models do not require simultaneous training memory. Cache predictions for cheap calibration and alarm-policy sweeps.

| Stage | Planning estimate |
|---|---|
| Data harmonization, labels and episode audit | 6–16 CPU-hours plus manual review |
| One compact fit: 0.2–0.5 million sampled windows, up to 30 epochs | 0.5–2 GPU-hours |
| One three-member ensemble | 1.5–6 GPU-hours |
| Bounded development, neural baselines and main ensembles | 24–60 GPU-hours |
| Selected ablations, dropout inference and replication | 24–60 GPU-hours |
| Core total including contingency | Approximately 60–144 GPU-hours (2.5–6 continuous GPU-days) |
| Expanded multi-cohort study or repeated full ensembles | Approximately 144–288 GPU-hours total |

These are unmeasured planning ranges. About 30–50 fit equivalents imply 15–100 GPU-hours before evaluation and contingency. Benchmark 1,000 steps locally, record throughput and peak VRAM, and extrapolate using actual steps and epochs. Reserve 30% contingency. Limit source-only tuning to approximately 12 trials and use early stopping. No large foundation-model pretraining is needed. Compute days exclude access delays, labeling review and writing; expect several calendar weeks of intermittent experiments.

## 15. Implementation stack

Python and PyTorch; NumPy, pandas/Polars and PyArrow; scikit-learn and XGBoost; Optuna; Captum/SHAP; SciPy and statsmodels; Matplotlib; local MLflow or structured experiment logs. Implement conformal scores and episode matching transparently with documented unit tests for edge cases. Pin versions only after confirming compatibility with the installed GPU driver and CUDA build.

Important verification cases include episodes crossing midnight, incomplete confirmation near record end, gaps within an episode, overlapping alert windows, late warnings, refractory periods and events during deferral. Save patient split manifests, label configuration, model hashes, calibration parameters and locked alert thresholds. Release synthetic fixtures to permit testing without redistributing restricted CGM data.

## 16. Timeline

| Weeks | Work and milestone |
|---|---|
| 1–2 | Recent-literature matrix, access and event-feasibility audit |
| 3–4 | Episode definitions, causal preprocessing and evaluation tests |
| 5–6 | Trend/tree/neural baselines and source-only protocol registration |
| 7–8 | Ensemble training and probability calibration |
| 9–10 | Freeze policy; uncertainty and deferral experiments on source data |
| 11–12 | Locked external evaluation and cohort replication |
| 13–14 | Statistics, clinical error review and ablations |
| 15–16 | Manuscript, reproducibility package and reporting checklist |

If data access is delayed, continue source-label validation and baseline implementation. If source calibration support is insufficient, simplify the model and calibration procedure before external testing.

## 17. Risks and mitigations

| Risk | Mitigation |
|---|---|
| Few events despite many windows | Count episodes/patients first; precision analysis; descriptive rare-event outcomes |
| Excellent AUROC but excessive alarms | Episode matching, AUPRC, fixed policy and alarms per eligible day |
| Apparent safety from rejecting hard cases | Count missed events during deferral; report full monitoring coverage |
| Shift breaks calibration | Frozen external assessment and separately labeled target recalibration |
| Sensor compression lows or artifacts | Flagging and sensitivity analyses; CGM-defined labels, no claim of verified blood hypoglycemia |
| Treatment modifies observed outcomes | Acknowledge observed-care prediction; an unmatched alert may coincide with a treated/prevented event that records cannot establish |
| Cohort overlap | Source-level provenance and exclusion of harmonized copies |
| Existing 2026 methods reduce novelty | Direct baseline comparisons and precise event-policy contribution |

## 18. Ethics, privacy and clinical boundaries

Seek institutional secondary-data ethics review or a documented exemption determination. Follow source agreements and attribution terms, encrypt local data, limit access and publish only permitted artifacts. Do not infer that deidentification eliminates all reidentification risk. Avoid sharing individual traces or small demographic cells without authorization.

The project does not deliver live alerts, insulin recommendations or automated treatment. Retrospective prediction cannot prove that a warning prevents a low episode. Interventions and missing logs can influence labels and false-alarm interpretation. A prospective evaluation would need a separately approved protocol, a defined response workflow and assessment of alarm fatigue and harms.

## 19. Expected outcomes and publication strategy

Deliver an event-label specification, an independently reproducible warning benchmark, calibration and alarm-burden plots, explicit deferral accounting, a clinical failure-case analysis, a model card and one manuscript. The central result should state whether uncertainty adds measurable value over a calibrated deterministic comparator, including null or negative findings.

For a methods-oriented conference, focus on controlled uncertainty comparisons and external event-policy evaluation. For a journal, expand cohort diversity, statistical precision and clinical interpretation. Consider biomedical AI conference tracks and diabetes technology or biomedical informatics journals after checking current author guidance, scope and fees. No venue deadline or acceptance is assumed. Use TRIPOD+AI for reporting [10]; clearly distinguish exploratory analyses from the preregistered comparison. Any later journal extension should disclose a prior conference version and add substantial evidence.

## References and resource links

Sources checked on 12 September 2026. This is a targeted check rather than a systematic review. Data references may predate 2024; recent methodological sources establish the current novelty context.

1. American Diabetes Association Professional Practice Committee. **6. Glycemic Goals, Hypoglycemia, and Hyperglycemic Crises: Standards of Care in Diabetes—2026.** Diabetes Care, 2026. [DOI](https://doi.org/10.2337/dc26-s006).
2. Tan HS, McBeth R. **Uncertainty quantification in neural network-based glucose prediction for diabetes.** 2026 preprint, v2 dated 28 March 2026; earlier title: *Uncertainty-aware Blood Glucose Prediction from Continuous Glucose Monitoring Data*. [arXiv:2603.04955v2](https://arxiv.org/abs/2603.04955v2).
3. Maqsood S, Sarwar MA, Belousovienė E. **Trustworthy Glucose Forecasting With Physiology-Constrained Neural Dynamics, Conformal Risk Control, and Risk-Sensitive Reinforcement Learning.** IEEE Access, 2026. [DOI](https://doi.org/10.1109/ACCESS.2026.3672553). Relevant prior art; inspect the complete publisher version and code before finalizing the comparison protocol.
4. **Tailoring Adverse Event Prediction in Type 1 Diabetes with Patient-Specific Deep Learning Models.** 2026 preprint. [arXiv:2601.14917](https://arxiv.org/abs/2601.14917).
5. **DiaTrend: A dataset from advanced diabetes technology to enable development of novel analytic solutions.** Scientific Data 10, 556, 2023. [DOI](https://doi.org/10.1038/s41597-023-02469-5).
6. **DiaTrend data repository.** [Synapse project syn38187184](https://www.synapse.org/Synapse:syn38187184).
7. Jaeb Center for Health Research. **Adult T1DEXI public dataset.** [Dataset DOI](https://doi.org/10.25934/PR00008428); [official catalog](https://public.jaeb.org/datasets/diabetes).
8. **OhioT1DM dataset and blood glucose prediction challenges.** [Official investigator site](https://webpages.charlotte.edu/rbunescu/data/ohiot1dm/homepage.html).
9. Wolff MK et al. **MetaboNet: The Largest Publicly Available Consolidated Dataset for Type 1 Diabetes Management.** 2026 preprint. [arXiv:2601.11505](https://arxiv.org/abs/2601.11505); [resource](https://metabo-net.org/).
10. Collins GS et al. **TRIPOD+AI statement: updated guidance for reporting clinical prediction models that use regression or machine learning methods.** BMJ 385:e078378, 2024. [DOI](https://doi.org/10.1136/bmj-2023-078378).
