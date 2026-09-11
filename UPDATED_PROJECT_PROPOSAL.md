# Updated Project Proposal

## Working title

**Agreement Is Not Validity: A Multi-Dataset and Multi-Model Evaluation of SHAP-LIME Concordance in Tabular Health Machine Learning**

## 1. Project summary

This project will evaluate when SHAP and LIME produce similar or different explanations for tabular health-prediction models. The revised study will treat agreement as an empirical property to be measured, not as proof that an explanation is correct. It will compare local and aggregated-global explanations across multiple public datasets, model families, data structures, and random repetitions.

The study will correct the main weaknesses of the previous experiment by:

- separating training, validation, and test data before any fitted preprocessing;
- preserving the natural class prevalence in validation and test sets;
- expanding from three datasets to at least six real datasets plus controlled synthetic datasets;
- comparing multiple model families without making the stacking ensemble a separate contribution;
- measuring uncertainty in agreement rather than proposing an unsupported universal threshold;
- testing explanation stability and faithfulness in addition to SHAP-LIME agreement;
- generating every table and figure directly from versioned result files.

## 2. Background and rationale

SHAP and LIME are frequently presented as interchangeable post-hoc explanation tools, although their objectives, perturbation mechanisms, and assumptions differ. A high correlation between their feature rankings can show consistency, but it does not establish explanation fidelity or clinical truth. Conversely, disagreement may result from data correlation, categorical encoding, model nonlinearity, local neighborhood construction, sampling variability, or genuine differences between the explanation methods.

The scientific need is therefore not a single cutoff for acceptable agreement. A more defensible contribution is a reproducible benchmark that identifies the conditions associated with agreement, disagreement, instability, and jointly misleading explanations.

## 3. Aim

To characterize the agreement, stability, and empirical faithfulness of SHAP and LIME explanations for tabular health machine-learning models across diverse datasets and controlled data conditions.

## 4. Research questions

1. How much do SHAP and LIME agree on local feature rankings for the same observations?
2. How much do their aggregated-global rankings agree within each dataset and model?
3. How stable is the measured agreement across data splits, explanation seeds, background samples, and perturbation budgets?
4. How are agreement and stability associated with feature correlation, feature type, dimensionality, sample size, class imbalance, and model nonlinearity?
5. Does high SHAP-LIME agreement correspond to greater empirical faithfulness, or can the methods agree while identifying unfaithful rankings?
6. How much does the choice of SHAP explainer, particularly model-specific SHAP versus KernelSHAP, change the conclusion?

## 5. Hypotheses

- **H1:** SHAP-LIME concordance will vary substantially across observations, datasets, and model families.
- **H2:** Greater feature dependence and mixed categorical-continuous structure will be associated with lower concordance and/or wider uncertainty.
- **H3:** Tree ensembles with strong interactions will show lower local concordance than logistic regression.
- **H4:** Increasing explanation sample sizes will improve stability but will not necessarily eliminate systematic disagreement.
- **H5:** High inter-method agreement will not always imply high faithfulness on controlled synthetic tasks.

These are testable hypotheses, not conclusions to be assumed in advance.

## 6. Study design

### 6.1 Overall design

The project will use two complementary dataset groups:

1. **Real public health datasets** to measure practical behavior under realistic preprocessing and feature structures.
2. **Synthetic and semi-synthetic datasets** with known informative, redundant, correlated, noisy, and interaction features to test whether agreement corresponds to recovery of known signal.

The primary analysis will be a multi-dataset benchmark. Controlled perturbation experiments will support causal statements about particular data characteristics.

### 6.2 Real dataset plan

| Dataset | Approximate size | Structure and task | Availability | Planned role |
|---|---:|---|---|---|
| Pima Indians diabetes | 768 x 8 | Continuous clinical measurements; binary diabetes outcome | Already available locally; provenance and license must be documented | Small biomarker benchmark |
| CDC Diabetes Health Indicators | 253,680 x 21 | Survey, behavioral, demographic, and health indicators; binary diabetes outcome | [UCI dataset 891](https://archive.ics.uci.edu/dataset/891/cdc+diabetes+health+indicators); locally available | Large, imbalanced survey benchmark |
| Early Stage Diabetes Risk Prediction | 520 x 16 | Primarily binary symptoms plus age; binary outcome | [UCI dataset 529](https://archive.ics.uci.edu/dataset/529/early+stage+diabetes+risk+prediction) | Categorical/binary diabetes benchmark |
| Diabetes 130-US Hospitals | 101,766 x 47 | Mixed clinical and administrative variables; 30-day readmission | [UCI dataset 296](https://archive.ics.uci.edu/dataset/296/diabetes+130-us+hospitals+for+years+1999+2008) | Large mixed-type clinical benchmark |
| Heart Disease | 303 x 13 for the Cleveland subset | Mixed clinical variables; disease presence | [UCI dataset 45](https://archive.ics.uci.edu/dataset/45/heart+disease) | Small mixed-type benchmark |
| Chronic Kidney Disease | 400 x 24 | Laboratory and categorical variables with missing data; CKD outcome | [UCI dataset 336](https://archive.ics.uci.edu/dataset/336/chronic+kidney+disease) | Missingness and mixed-type benchmark |
| Breast Cancer Wisconsin Diagnostic | 569 x 30 | Continuous image-derived measurements; binary diagnosis | [UCI dataset 17](https://archive.ics.uci.edu/dataset/17/breast+cancer+wisconsin+diagnostic) | Higher-dimensional continuous benchmark |

The 442-row scikit-learn diabetes progression dataset should not be called the “Frankfurt Hospital Diabetes Dataset.” It is a regression dataset. It will be excluded from the primary binary-classification analysis. It may be used in a separately labeled regression sensitivity analysis without median-binarizing its target.

### 6.3 Dataset inclusion criteria

A real dataset will be included when it:

- is publicly accessible under a documented license or terms of use;
- has a clearly defined supervised prediction target;
- contains at least five usable input features;
- has sufficient metadata to define feature types and missing values;
- can be analyzed without attempting to identify individuals;
- permits a reproducible download or checksum-verified local copy.

### 6.4 Synthetic benchmark design

Generate datasets in which the true predictive structure is known. Vary one factor at a time:

- sample size: 500, 5,000, and 50,000;
- feature count: 10, 25, and 50;
- pairwise correlation: 0.0, 0.3, 0.6, and 0.9;
- class prevalence: 50%, 20%, and 10%;
- relationship: linear, nonlinear additive, and interaction-dominant;
- irrelevant noise features: 0%, 25%, and 50%;
- redundant proxy features: absent or present.

Use a fractional factorial design rather than every possible combination. Pre-register approximately 24-36 synthetic conditions with 10 random replications per condition.

## 7. Prediction models

Use three primary model families:

1. Regularized logistic regression as an interpretable linear baseline.
2. Random forest as a bagged nonlinear model.
3. XGBoost or LightGBM as a boosted nonlinear model.

Do not use a five-model stacking ensemble or neural network in the primary experiment. They add computational and methodological complexity without helping answer the explanation-agreement questions. A stacking model may be included later as a clearly labeled secondary sensitivity analysis.

Model hyperparameters will be selected within training data using a small, pre-specified search space. Performance optimization is secondary; each model only needs to achieve reasonable discrimination without obvious overfitting.

## 8. Leakage-free analysis protocol

### 8.1 Data splitting

- Create stratified train, validation, and test partitions from the untouched original data.
- Use five independent split seeds for the primary analysis.
- For small datasets, use repeated nested stratified cross-validation instead of relying on one small test set.
- When patient identifiers are present, use group-aware splitting so the same patient cannot occur in multiple partitions.
- Never preprocess, select features, resample, tune thresholds, or tune models using the final test observations.

### 8.2 Preprocessing

All fitted operations must live inside a scikit-learn or imbalanced-learn pipeline:

- training-fitted imputation;
- training-fitted scaling for models that require it;
- training-fitted categorical encoding;
- training-only feature selection, if feature selection is used;
- training-only resampling in sensitivity analyses.

Primary analyses should prefer class weights over SMOTE. This preserves the observed feature distribution and avoids making synthetic observations part of the explanation reference distribution. If SMOTE is evaluated, it must be applied only inside training folds and reported as a sensitivity analysis.

Validation and test sets must retain their natural prevalence. PPV, NPV, calibration, and decision thresholds must be calculated on naturally distributed observations.

### 8.3 Feature representation

- Keep an explicit mapping from transformed columns back to original clinical variables.
- Aggregate one-hot encoded columns to their original variable before comparing feature rankings.
- Treat ordinal survey features consistently and document whether they are ordinal-coded or one-hot encoded.
- Do not recover LIME feature identities using substring matching. Use LIME's feature-index mapping.

## 9. Explanation protocol

### 9.1 Unit of analysis

The primary unit will be the individual test observation. For each observation and method:

- obtain an attribution for every original feature;
- rank features by absolute attribution magnitude;
- retain attribution signs separately for directional-agreement analysis.

Aggregated-global rankings, calculated from mean absolute local attributions, will be secondary summaries.

### 9.2 SHAP

- Use LinearSHAP for logistic regression and TreeSHAP for tree models in the primary benchmark.
- Draw background/reference observations from training data only.
- Fix and record background sampling seeds.
- Evaluate KernelSHAP on a computationally manageable subset as an explainer-choice sensitivity analysis.
- Report the SHAP perturbation/dependence assumption used.

### 9.3 LIME

- Build the explainer using training data only.
- Use the same test observations used for SHAP.
- Explain all original features after correct grouping of encoded columns.
- Pre-specify the kernel width and discretization behavior.
- Use 2,000 perturbations per observation in the primary run.
- Evaluate 500, 1,000, 2,000, and 5,000 perturbations in a robustness subset.

### 9.4 Sampling budget

- Pilot: 50 explained observations per dataset-model pair.
- Primary real-data analysis: up to 200 observations per held-out split.
- Large-dataset confirmatory analysis: increase to 500 observations for selected dataset-model pairs only if ranking estimates have not stabilized.
- Use the same observations for both methods within every paired comparison.

## 10. Outcomes and statistical analysis

### 10.1 Primary outcome

Distribution of observation-level Kendall's tau-b between absolute SHAP and LIME feature rankings.

Report the median, interquartile range, bootstrap 95% confidence interval, and proportion of observations with negative, weak, moderate, and strong concordance. Category labels are descriptive and must not be turned into a universal deployment rule.

### 10.2 Secondary agreement outcomes

- global Kendall's tau-b;
- Spearman rank correlation;
- top-3 and top-5 Jaccard overlap;
- rank-biased overlap, where feasible;
- attribution-sign agreement among shared top-k features;
- model- and dataset-level variance components.

### 10.3 Stability outcomes

Repeat explanations across background samples and explanation seeds. Report:

- within-method rank stability;
- confidence intervals for SHAP-LIME concordance;
- probability that each feature appears in the top-k;
- sensitivity to the number of explained observations and LIME perturbations.

Do not describe a larger number of bootstrap iterations as increasing the underlying sample's statistical power. Bootstrap iterations improve numerical precision of the uncertainty estimate; they do not create new independent observations.

### 10.4 Faithfulness and validity checks

Agreement alone is not validity. Include:

- top-k feature deletion or masking tests, performed with a clinically defensible reference value;
- comparison with known informative features in synthetic datasets;
- recovery of known interaction and redundant-proxy structures;
- optional comparison with permutation importance as an additional reference, not as ground truth;
- sanity tests in which model labels or model parameters are randomized.

### 10.5 Explanatory modeling

Use a hierarchical or mixed-effects analysis in which agreement is modeled as a function of:

- feature correlation;
- categorical-feature fraction;
- feature count;
- sample size;
- prevalence;
- model family;
- model discrimination;
- explanation configuration.

Treat dataset and data split as grouping factors. With only a small number of real datasets, emphasize effect sizes, uncertainty, and controlled synthetic evidence rather than strong universal claims.

## 11. Model evaluation

Report AUROC, area under the precision-recall curve, balanced accuracy, Brier score, and calibration slope/intercept. Choose classification thresholds using training/validation data only.

Prediction performance is a model-quality check, not the main novelty. Avoid claiming that small AUROC differences prove superiority unless they are supported by an appropriate repeated evaluation and uncertainty analysis.

## 12. Reproducibility and quality controls

- Pin package versions and record Python, CUDA, operating-system, CPU, and GPU information.
- Create a machine-readable dataset manifest containing URL, DOI, license, download date, file checksum, row count, feature count, target definition, and prevalence.
- Use a single configuration file for datasets, models, seeds, and explanation budgets.
- Save split indices and never regenerate them silently.
- Save raw attributions in a structured format before producing summary tables.
- Generate manuscript tables and figures directly from saved results.
- Add automated checks for sample counts, prevalence, non-overlapping splits, feature mappings, rank ranges, and impossible mean/standard-deviation combinations.
- Provide a one-command reproduction path and a reduced smoke-test configuration.
- Keep an experiment log containing run ID, configuration hash, source commit, start/end time, and success/failure status.

## 13. Ethics and reporting

The proposed datasets are public and de-identified, so direct participant recruitment is not involved. Institutional rules may nevertheless require an exemption determination; this should be checked before submission.

Clinical language must remain appropriately limited. The study evaluates explanation methods on retrospective benchmarks; it does not validate a clinical decision-support system for deployment. Sensitive attributes should be retained only when scientifically justified, and subgroup performance and explanation behavior should be reported where sample sizes permit.

Follow relevant prediction-model and AI-reporting guidance applicable at the time of manuscript preparation. Record all deviations from the pre-specified protocol.

## 14. Expected contribution

The intended contribution is a reproducible empirical framework showing:

- how SHAP-LIME concordance varies at both local and global levels;
- which data and model properties are associated with disagreement;
- how much measured concordance depends on explanation sampling choices;
- whether high agreement corresponds to faithful recovery of predictive signal;
- why concordance should be reported with uncertainty rather than reduced to an unsupported universal threshold.

## 15. Feasibility assessment

### 15.1 Data availability: high feasibility

Five proposed additions are directly downloadable from UCI under documented terms, and three existing datasets are already present locally. No proprietary electronic health record is required. The largest proposed files are only tens of megabytes. Raw and processed data, attributions, models, and figures should remain comfortably below a few gigabytes.

The main data work is not downloading; it is documenting provenance, defining defensible targets, cleaning mixed categorical values, preventing repeated-patient leakage in the 130-US Hospitals dataset, and preserving original prevalence.

**Data feasibility rating: 9/10.**

### 15.2 Hardware availability: strong

Planned experiment workstation, based on the information supplied by the researcher:

- Intel Core i9-13900K;
- 24 CPU cores: 8 performance cores and 16 efficient cores;
- 32 CPU threads, with a maximum turbo frequency up to 5.8 GHz;
- NVIDIA RTX 2000 Ada Generation desktop workstation GPU;
- 16 GB GDDR6 GPU memory with ECC, assuming the desktop RTX 2000 Ada model;
- 3,072 CUDA cores and approximately 12 FP32 TFLOPS;
- 64 GB system RAM.

The GPU form factor should also be confirmed. This proposal assumes the desktop RTX 2000 Ada Generation card with 16 GB memory; a laptop RTX 2000 Ada variant may have different memory and performance specifications.

This workstation is more than sufficient for logistic regression, random forest, XGBoost/LightGBM, TreeSHAP, LinearSHAP, and moderate-scale LIME experiments on the proposed tabular datasets. The 16 GB GPU memory provides ample capacity for these models and permits larger GPU prediction batches. However, LIME and KernelSHAP will remain substantially CPU- and prediction-throughput-bound, so the larger GPU will not reduce every stage proportionally.

The available 64 GB system RAM is appropriate for concurrent explanation jobs, cached models, and intermediate attribution arrays. Start with 6-8 concurrent CPU-bound jobs during the pilot, monitor peak memory and CPU temperature, and increase toward 10-12 only if the system remains stable. Avoid allowing every library to create its own full set of worker threads, because nested parallelism can reduce performance despite the high core count. Use fast local SSD storage for temporary attribution files and model checkpoints.

**Hardware feasibility rating: 10/10 for the proposed study, assuming the desktop 16 GB RTX 2000 Ada variant.**

### 15.3 Estimated computation time

These are planning ranges, not guarantees. Actual time should be recalibrated after the pilot.

| Stage | Proposed scope | Estimated wall-clock time on the planned workstation |
|---|---|---:|
| Automated data checks and preprocessing smoke test | Seven real datasets, one split | 5-15 minutes |
| Pilot model training | Three datasets, two models, one split | 5-30 minutes |
| Pilot explanations | 50 observations, 1,000 LIME perturbations | 20 minutes-90 minutes |
| Full primary model fitting | Seven datasets, three models, five splits, modest tuning | 1.5-6 hours |
| TreeSHAP and LinearSHAP | All primary real-data runs | 30 minutes-2 hours |
| Primary LIME experiment | Up to 200 observations, 2,000 perturbations, all dataset-model-split pairs | 6-24 hours |
| Explanation robustness subset | Multiple perturbation budgets and seeds on three representative datasets | 4-12 hours |
| KernelSHAP sensitivity subset | Selected datasets/models only | 4-20 hours |
| Synthetic experiments | Fractional design with 10 replications | 3-12 hours |

The full computational study is therefore likely to require **approximately one to three days of unattended wall-clock processing** on this workstation, using its 64 GB system RAM with checkpointed jobs, batched prediction, and conservative parallelization. Running KernelSHAP across every dataset, model, split, and observation could still extend this to a week or more and is not recommended.

### 15.4 Why explanation generation is the bottleneck

The primary LIME design alone can require approximately:

`7 datasets x 3 models x 5 splits x 200 observations x 2,000 perturbations = 210 million perturbed prediction rows.`

Batch prediction and parallel processing can reduce wall-clock time, but the study must avoid running so many simultaneous jobs that memory pressure or thermal throttling makes the workstation unstable. Cache fitted models and checkpoint explanation outputs after every dataset-model-split combination.

### 15.5 Human-time estimate

| Work package | Estimated active time |
|---|---:|
| Protocol finalization and literature update | 1-2 weeks |
| Clean pipeline and automated validation tests | 1-2 weeks |
| Pilot and protocol adjustment | 3-5 days |
| Full experiments and failed-run recovery | 1-2 weeks elapsed |
| Analysis, figures, and internal consistency audit | 1 week |
| Manuscript rewrite and co-author review | 2-3 weeks |

A realistic end-to-end schedule is **six to ten weeks**, depending on available daily time and whether the first pilot exposes additional data-cleaning problems.

### 15.6 Overall feasibility

**Overall feasibility: high, provided the scope is staged.**

The project is feasible with public data and the planned workstation. The stronger CPU and 16 GB workstation GPU remove hardware capacity as a major concern. The strongest risk is methodological scope creep rather than unavailable data or inadequate hardware. The project should proceed only through staged gates.

## 16. Staged execution plan and decision gates

### Phase 0: protocol freeze

Deliverables:

- final dataset list and target definitions;
- analysis plan;
- seed list;
- primary and sensitivity outcomes;
- compute budget;
- data manifest.

**Gate:** no model training until row counts, prevalence, licenses, and patient/group identifiers are verified.

### Phase 1: leakage-proof pipeline

Deliverables:

- reusable preprocessing pipelines;
- saved, non-overlapping split indices;
- automated leakage and prevalence tests;
- one-command smoke test.

**Gate:** test partitions must remain untouched and retain natural prevalence.

### Phase 2: small pilot

Run Pima, BRFSS, and Diabetes 130-US Hospitals with logistic regression and XGBoost. Explain 50 observations per pair.

**Gate:** proceed only if attribution files are reproducible, feature grouping is correct, and runtime extrapolation fits the available compute budget.

### Phase 3: primary benchmark

Run the full real-dataset analysis using pre-specified settings. Do not change the primary protocol after viewing results unless the change is documented as a protocol amendment.

### Phase 4: controlled and robustness experiments

Run synthetic conditions, explanation-budget sensitivity, explainer-choice sensitivity, and sanity checks.

### Phase 5: audit and manuscript

Before writing conclusions:

- reproduce all summary tables from raw attribution files;
- run automated consistency checks;
- inspect extreme or contradictory results manually;
- distinguish confirmatory results from exploratory findings;
- have a second person review the analysis code if possible.

## 17. Go/no-go criteria for a journal paper

Proceed to a full manuscript when:

- all primary results are produced by a leakage-free pipeline;
- at least six real datasets complete successfully;
- the local and global agreement analyses are reproducible across saved splits;
- synthetic experiments provide evidence about explanation validity, not just agreement;
- uncertainty estimates are stable at the selected explanation budget;
- code and tables reproduce from a clean environment;
- claims are limited to what the design can establish.

If these criteria are not met, publish the code and benchmark protocol as a smaller reproducibility or methods resource rather than making clinical deployment claims.

## 18. Publication positioning

The manuscript should be positioned as an XAI evaluation and reliability study, not as another diabetes-classification performance paper. Journal selection should occur only after the corrected results are known.

For a high-selectivity applied-AI journal, the real-data benchmark, controlled synthetic evidence, uncertainty analysis, and open reproducibility package should all be completed. If only the real-data proof of concept is completed, a narrower health-informatics or applied-data-science journal will be more realistic.

## 19. Immediate next actions

1. Archive the current results as non-confirmatory and do not reuse their performance estimates.
2. Create the dataset manifest and verify every raw dataset before modeling.
3. Implement and test the leakage-proof split-first pipeline.
4. Run the three-dataset pilot and record actual time per explanation.
5. Recalculate the full compute plan from the pilot measurements.
6. Freeze the primary protocol before running the complete benchmark.
