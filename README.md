# Privacy Risk Assessment for Record Linkage Models via Membership Inference Attacks

**COMP3850 PACE Capstone Project — Data Science & Cybersecurity**  
Macquarie University · Group 61 · 2025

## Project Overview

Machine-learning systems trained on sensitive data can leak information about the records used during training. This capstone investigates that risk in a privacy-preserving record-linkage setting by testing whether an attacker can infer if a specific record was part of a model's training data.

The project combines **record linkage**, **deep learning**, and **membership inference attacks (MIA)** to evaluate both predictive utility and privacy resilience.

## Problem Statement

> Can a record-linkage model maintain strong predictive performance while resisting membership-inference attacks that try to identify which records were used for training?

The system was evaluated in a controlled research setting using synthetic/de-identified data and pre-computed embeddings only.

## Technical Approach

### Target model

A **Siamese Autoencoder** was used to compare paired 768-dimensional BERT embeddings. The encoder compressed each embedding from **768 → 256 → 50 dimensions**, with a calibrated logistic-regression head used for the linkage decision. A raw-difference **MLP classifier** was also evaluated as a comparison model.

### Shadow-model attack pipeline

1. Train shadow models on held-out partitions to approximate the target model's behaviour.
2. Collect member and non-member outputs.
3. Derive attack features such as confidence, entropy, margins and posterior probabilities.
4. Train Logistic Regression and Random Forest attack classifiers.
5. Compare attack performance with random guessing using ROC-AUC and classification metrics.

```text
Paired BERT embeddings
        ↓
Siamese representation learning
        ↓
Record-linkage classifier
        ↓
Target model outputs
        ↓
Shadow-model simulation
        ↓
Attack feature construction
        ↓
Membership-inference classifiers
        ↓
Privacy evaluation
```

## Evaluation

The project measured both **utility** and **privacy**.

| Metric | Target Model | Membership-Inference Attack |
|---|---:|---:|
| ROC-AUC | 0.89 | ~0.50 |
| Accuracy | 87.3% | 51.1% |
| F1 Score | 0.87 | ~0.50 |

The saved attack metrics are close to random guessing:

- Logistic Regression attack AUC: **0.5059**
- Random Forest attack AUC: **0.5041**

Under this experiment configuration, the evaluated attacks did not show measurable membership leakage while the linkage model maintained strong predictive performance. This is a narrow experimental result, not a claim that the model is immune to all privacy attacks.

The project used a privacy release gate of **target AUC ≥ 0.85** and **attack AUC < 0.55**.

### Raw-difference MLP result

![Confusion matrix](results/confusion_matrix.png)

The held-out raw-difference MLP achieved approximately **82% accuracy** on the linkage classification task.

## Repository Structure

```text
privacy-risk-ml-capstone/
├── README.md
├── .gitignore
├── requirements.txt
├── notebooks/
│   └── privacy_risk_mia_pipeline.ipynb
├── models/
│   ├── README.md
│   ├── snn_classifier_model.keras
│   ├── mlp_raw_diff_classifier.keras
│   ├── target_encoder.keras
│   ├── target_head.joblib
│   ├── shadow_encoder.keras
│   ├── shadow_head.joblib
│   ├── attack_lr.joblib
│   └── attack_scaler.joblib
└── results/
    ├── README.md
    ├── confusion_matrix.png
    ├── final_test_predictions_raw_mlp.csv
    ├── metrics.json
    ├── attack_per_record.csv
    ├── attack_per_record_on_target.csv
    ├── p_trg_in.npy
    └── p_trg_out.npy
```

## Tech Stack

**Python · TensorFlow/Keras · scikit-learn · pandas · NumPy · SciPy · Transformers/BERT embeddings · Jupyter Notebook**

Models and methods include Siamese neural networks, autoencoders, MLPs, calibrated Logistic Regression, Random Forests and shadow-model membership-inference attacks.

## Environment Setup

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook
```

The repository intentionally excludes the original source dataset, so full retraining requires access to the same approved data/embeddings used in the university project. The notebook and saved artifacts are included to document the complete modelling and evaluation workflow.

## My Contribution

This was a six-person PACE capstone project spanning the Data Science and Cybersecurity streams.

My work focused on the **machine-learning and evaluation components**. I worked with a teammate to fix and validate the core MVP notebook pipeline, including model-training and evaluation cells, reviewed experiment outputs, contributed to the membership-inference attack design, and helped document and interpret the final model-evaluation results.

## Key Takeaways

- Model utility and privacy risk should be evaluated together.
- Shadow-model attacks provide a practical framework for testing membership leakage.
- Privacy results should be interpreted within the tested attack configuration rather than generalised too broadly.
- Reproducible ML work benefits from clear environment dependencies, saved artifacts and documented evaluation outputs.
- Privacy testing belongs in the ML evaluation lifecycle, not as an afterthought.

## Limitations

- The project used synthetic/de-identified data and pre-computed BERT embeddings rather than production clinical data.
- Results apply only to the tested architecture, splits and attack configuration.
- Attack AUC near 0.50 does not prove immunity to stronger or different privacy attacks.
- The public repository cannot reproduce full training without the original approved dataset.

## Future Improvements

- evaluate stronger and more diverse membership-inference attacks
- test differential privacy and additional regularisation strategies
- add automated experiment tracking with MLflow or Azure Machine Learning
- build a reproducible cloud-based evaluation pipeline
- compare privacy/utility trade-offs across additional model families

## Ethics & Data Handling

No raw personal or identifiable patient data is included in this repository. The university project used synthetic or de-identified research data in a controlled scope. Large source datasets are intentionally excluded from this public portfolio repository.
