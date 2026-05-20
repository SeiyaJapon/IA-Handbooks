# ML Review

## Purpose

Review classical machine learning systems for correctness in data handling, model design, evaluation, and production deployment — distinct from LLM-based systems.

## Responsibilities

- Evaluate feature engineering and data preparation
- Assess train/validation/test split strategy and data leakage risk
- Review model selection and complexity relative to the problem
- Evaluate evaluation metrics and their alignment with business objectives
- Review overfitting and underfitting signals
- Assess model versioning and reproducibility
- Evaluate prediction pipeline design for production
- Review monitoring and drift detection strategy

## Instructions

- Check the data split: is test data strictly held out? is there any feature computed using test data?
- Check for data leakage: are features derived from the target, or from data not available at prediction time?
- Check evaluation metrics: do they match the business objective? (accuracy vs precision/recall for imbalanced classes)
- Check class imbalance: is it addressed in training and in the evaluation metric?
- Check the baseline: is there a simple baseline to compare against?
- Check reproducibility: are random seeds fixed? is the training environment reproducible?
- Check prediction pipeline: does it apply the same preprocessing as training?
- Check monitoring: is there a distribution shift or drift detection mechanism in production?

## Heuristics

Treat as stronger concerns when:

- Test set used during feature selection or hyperparameter tuning — data leakage
- Features computed using future information — not available at prediction time in production
- Accuracy used as the metric for a heavily imbalanced classification problem
- No baseline model — cannot assess whether ML actually helps
- Training and inference pipelines apply different preprocessing — silent mismatch
- No model versioning — cannot reproduce or roll back predictions
- No drift detection in production — model degrades silently
- Model complexity (deep ensemble, boosted stacks) without evidence that simpler models failed

Treat as acceptable when:

- No drift detection in early prototype or internal tooling
- Simplified preprocessing in production when the difference is provably negligible

## Rules

- Test data must be strictly held out — no information from it during training or feature selection
- Evaluation metrics must align with the business objective
- Training and inference pipelines must be identical in preprocessing
- Every production model must have a versioned artifact and a reproducibility path

## Activity Traceability

🔧 Loading skill: `ml-review`
