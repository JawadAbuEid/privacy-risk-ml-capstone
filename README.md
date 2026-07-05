# Privacy Risk Assessment for Record Linkage Models via Membership Inference Attacks

**COMP3850 Capstone Project — Group 61 (Data Science & Cybersecurity Stream)**
Macquarie University, 2025

## Overview

Record linkage models (used to match records across datasets — e.g. patient records
across hospitals) are often trained on sensitive data. This project asks: *can an
attacker figure out whether a specific record was part of the training set just by
querying the model?* This is called a **Membership Inference Attack (MIA)**, and it's
a real privacy risk for any ML system trained on personal data.

We built a record linkage model, then attacked our own model to measure how much
private information it leaked — and used the results to define a monitoring
framework a sponsor organisation could use to keep future models privacy-safe.

## What we built

**Target model** — a Siamese Autoencoder that learns to compress paired BERT
embeddings (768-dim) and decide whether two records refer to the same entity, backed
by a calibrated logistic regression head and a raw-difference MLP classifier for
comparison.

**Attack pipeline** — trained shadow models that mimic the target model's behaviour,
then used their outputs (posterior probabilities, confidence margins, entropy) to
train attacker classifiers (Logistic Regression, Random Forest) that try to guess
which records were in the original training set.

**Results:**

| Metric | Target Model (linkage task) | Attack Model (membership inference) |
|---|---|---|
| AUC (ROC) | 0.89 | 0.50 |
| Accuracy | 87.3% | 51.1% |
| F1 Score | 0.87 | 0.50 |

An attack AUC of ~0.50 is equivalent to random guessing — meaning the target model
generalised well without measurably leaking which records it was trained on. The
project's release-gate standard, set jointly with the sponsor, was target AUC ≥ 0.85
and attack AUC < 0.55.

![Confusion Matrix](results/confusion_matrix.png)

*Confusion matrix for the raw-difference MLP classifier on the held-out test set
(~82% accuracy).*

## Repo structure

```
├── notebook.ipynb              # Full MVP pipeline: data loading, model training,
│                                # shadow model training, attack simulation, evaluation
├── models/
│   ├── snn_classifier_model.keras       # Siamese autoencoder target model
│   └── mlp_raw_diff_classifier.keras    # MLP classifier on raw embedding differences
├── results/
│   ├── confusion_matrix.png             # Test set confusion matrix
│   └── final_test_predictions_raw_mlp.csv
├── requirements.txt
└── README.md
```

> Note: raw datasets are not included in this repo. All training data was synthetic
> or de-identified clinical text (BERT embeddings only, no raw text), per the
> project's ethics approval and the ACS Code of Ethics / OAIC Privacy Act (1988).

## How it works, briefly

1. **Encode** — two records are each turned into a 768-dim BERT embedding
2. **Compare** — a Siamese autoencoder compresses both embeddings (768→256→50) and
   compares them via contrastive + reconstruction loss to decide match / no-match
3. **Attack** — shadow models are trained on held-out data splits to imitate the
   target model, and their output confidence patterns are used to train an attacker
   that tries to tell "was this record in training?" from the target model's outputs
4. **Evaluate** — if attack performance stays near random guessing, the model passes
   the privacy release gate

## Tech stack

Python, TensorFlow/Keras, scikit-learn, pandas, NumPy

## My role

This was a 6-person team project across Data Science and Cybersecurity streams. My
contributions included fixing and validating the core MVP notebook pipeline
(model training and evaluation cells) alongside a teammate, and documenting the
model evaluation section of the final report.

## Team

Group 61 — COMP3850, Macquarie University
