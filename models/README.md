# Model Artifacts

This folder contains trained artifacts produced by the capstone experiments. They are included for portfolio transparency and inspection; they are **not** presented as production-ready models.

## Files

- `snn_classifier_model.keras` — Siamese-network classifier artifact used in the linkage workflow.
- `mlp_raw_diff_classifier.keras` — comparison MLP trained on absolute embedding differences.
- `target_encoder.keras` — encoder from the target privacy-evaluation pipeline.
- `target_head.joblib` — calibrated classification head used with target-model features.
- `shadow_encoder.keras` — encoder trained for the shadow-model attack workflow.
- `shadow_head.joblib` — corresponding shadow classification head.
- `attack_lr.joblib` — logistic-regression membership-inference attack model.
- `attack_scaler.joblib` — scaler used for attack-model features.

## Important Notes

These artifacts depend on the same preprocessing, feature construction and library versions used in the notebook. The raw/de-identified source data is intentionally not published, so the repository should be treated as a reproducible **project record** rather than a one-command retraining package.
