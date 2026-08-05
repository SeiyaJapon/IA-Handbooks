# ML Review Skill

Review **classical machine learning** systems: datasets, features, labels, leakage, metrics, training/evaluation splits, model validation, training/serving consistency. This is the non-LLM side of AI.

## When to use

Use this skill when:

- The system uses **classical ML**: classification, regression, clustering, recommendation, ranking, time series, anomaly detection.
- A model has been **trained on data** with a defined training set.
- The team is debating **features, labels, splits, metrics, leakage, drift**.
- A model is being **deployed** and the team needs train/serve consistency.

## When not to use

Do not use this skill when:

- The system uses an **LLM**. Use `ai-integration`, `prompt-engineering`, `rag-design`, `ai-agents`, or `llm-ops`.
- The work is **data engineering** without a model. Use `data-pipeline-architecture`.
- The work is **production monitoring** generally. Use `observability`. ML-specific monitoring (drift, model quality) lives here.
- The system is a deterministic algorithm that the team is calling "ML" loosely. Use the relevant `software-design` skill.

## Inputs to inspect first

Before reviewing or designing classical ML, inspect:

- **The problem.** Classification (binary, multi-class, imbalanced)? Regression? Ranking? Clustering?
- **The dataset.** Source, size, freshness, label quality, class balance.
- **The split.** Train, validation, test. Are they strictly held out? Time-based or random? Group-based?
- **Features.** What are they? Are any derived from the target or future information?
- **Metrics.** What is being optimised? Does it match the business objective?
- **Baseline.** Is there a simple baseline (heuristic, majority class, last-value)?
- **Training pipeline.** Reproducibility, seeds, environment, dependencies.
- **Serving pipeline.** Same preprocessing as training? Train/serve skew detection?
- **Monitoring.** Drift detection, model quality in production, retraining triggers.

If any of these is unknown, ML judgements are guessing.

## Hard rules

- **Test data is strictly held out.** No use during feature selection, hyperparameter tuning, or model selection.
- **No leakage.** Features cannot be derived from the target. Features cannot use information not available at prediction time.
- **Metrics match the objective.** Accuracy on an imbalanced classification problem is a smell.
- **Baseline first.** Without a baseline, "the model works" is opinion.
- **Train and serve apply the same preprocessing.** Train/serve skew is a silent quality killer.
- **Reproducibility.** Seeds fixed, environment captured, training reruns produce the same model.
- **Production drift is monitored.** Input distribution, prediction distribution, observed labels (when available).
- **Complexity is justified.** Deep ensembles or boosted stacks need evidence that simpler models failed.

## Concerns this skill covers

- Feature engineering and data preparation.
- Train/validation/test splits, including time-based and group-based variants.
- Data leakage (target leakage, train/test contamination, future-information leakage).
- Class imbalance.
- Metric selection (accuracy, precision/recall, F1, AUC, MAP, NDCG, MAE, RMSE).
- Baseline comparison.
- Overfitting and underfitting signals.
- Reproducibility (seeds, environment, data versioning).
- Training/serving consistency.
- Production monitoring: drift, quality, retraining triggers.
- Model versioning and rollback.

## How to work

1. **Confirm classical ML, not LLM.**
2. **Inspect the dataset and the split.** Most ML failures live here.
3. **Inspect features for leakage.** Target leakage, time leakage, group leakage.
4. **Inspect metrics against business objective.**
5. **Inspect baseline.** If absent, that is the priority.
6. **Inspect train/serve consistency.** Same preprocessing? Same feature pipeline?
7. **Inspect production monitoring.** Drift, quality, retraining.
8. **Recommend the smallest change** that closes the active gap.

## Output

Return findings as:

- Concrete data, leakage, metric, baseline, train/serve, monitoring issues.
- Recommended changes (split rework, feature removal, metric change, baseline addition, drift detector).
- Escalations when the issue is data engineering, observability, or product objective.

## Escalation

- Data ingestion, transformation, batch processing for ML training data: `data-pipeline-architecture`.
- Production observability beyond ML-specific drift: `observability`.
- The system uses an LLM rather than classical ML: `ai-integration`, `prompt-engineering`, `rag-design`, `ai-agents`, or `llm-ops`.
- Whether AI is the right tool at all: `ai-systems`.
- Privacy or compliance of training data and inference: `security-review`, `compliance-patterns`.
- Database shape for feature stores: `database-design`.

## Operational checks (legacy, kept as a checklist)

- Test set used during feature selection or hyperparameter tuning; data leakage.
- Features computed using future information; not available at prediction time.
- Accuracy used as the metric for a heavily imbalanced classification problem.
- No baseline model; cannot assess whether ML helps.
- Training and inference pipelines apply different preprocessing; silent mismatch.
- No model versioning; cannot reproduce or roll back predictions.
- No drift detection in production; model degrades silently.
- Model complexity without evidence that simpler models failed.
