# Privacy Risk Assessment for Record Linkage Models via Membership Inference Attacks

**COMP3850 PACE Capstone Project — Macquarie University**  
**Six-person team project | Machine Learning · Privacy · NLP**

## Project Overview

### Context
Machine-learning systems trained on sensitive data can unintentionally reveal information about the records used during training. In privacy-sensitive record linkage, strong predictive performance is only part of the problem: the model should also be evaluated for potential privacy leakage.

This capstone investigated whether an attacker could infer if a particular record had been part of a record-linkage model's training data using **Membership Inference Attacks (MIA)**.

### Actions
As part of a six-person PACE team, my work focused on the **machine-learning and evaluation components**. I worked with a teammate to fix and validate the core MVP notebook pipeline, including model-training and evaluation cells, reviewed experiment outputs, contributed to the membership-inference attack design, and helped document and interpret the final model-evaluation results.

The project used:
- paired **768-dimensional BERT embeddings**
- Siamese representation learning with a **768 → 256 → 50** encoder
- a calibrated Logistic Regression linkage head
- a raw-difference MLP comparison model
- **5 shadow models** for membership-inference evaluation
- Logistic Regression and Random Forest attack classifiers
- ROC-AUC, accuracy, F1, confusion matrices and classification reports

### Results
| Evaluation | Result |
|---|---:|
| Target model ROC-AUC | **0.89** |
| Target model accuracy | **87.3%** |
| Target model F1 | **0.87** |
| Logistic Regression attack AUC | **0.5059** |
| Random Forest attack AUC | **0.5041** |

The target model showed strong record-linkage performance, while the tested membership-inference classifiers performed close to random guessing. This is a **narrow result for the tested configuration**, not evidence that the model is immune to all privacy attacks.

### Growth & Next Steps
The next stage would be to test whether the result holds under stronger attack settings, additional datasets and different model architectures. Useful extensions include differential privacy, broader privacy/utility comparisons, automated experiment tracking and a reproducible cloud evaluation pipeline.

---

## Problem Statement

> Can a record-linkage model maintain strong predictive performance while resisting membership-inference attacks that try to identify which records were used for training?

The system was evaluated in a controlled research setting using synthetic/de-identified data and pre-computed embeddings only.

## Technical Approach

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

### Target Model
A **Siamese Autoencoder** was used to compare paired 768-dimensional BERT embeddings. The encoder compressed each embedding from **768 → 256 → 50 dimensions**, with a calibrated logistic-regression head used for the linkage decision. A raw-difference **MLP classifier** was also evaluated as a comparison model.

### Membership-Inference Pipeline
1. Train shadow models on held-out partitions to approximate the target model's behaviour.
2. Collect member and non-member outputs.
3. Derive attack features such as confidence, entropy, margins and posterior probabilities.
4. Train Logistic Regression and Random Forest attack classifiers.
5. Compare attack performance with random guessing using ROC-AUC and classification metrics.

## Evaluation Details

The project measured both **utility** and **privacy**. It used a privacy release gate of **target AUC ≥ 0.85** and **attack AUC < 0.55**.

### Raw-Difference MLP

![Confusion matrix](results/confusion_matrix.png)

The held-out raw-difference MLP achieved approximately **82% accuracy** on the linkage classification task.

## Repository Structure

```text
privacy-risk-ml-capstone/
├── README.md
├── requirements.txt
├── notebooks/
│   └── privacy_risk_mia_pipeline.ipynb
├── models/
│   ├── README.md
│   └── saved model artifacts
└── results/
    ├── README.md
    ├── confusion_matrix.png
    ├── metrics.json
    └── evaluation outputs
```

## Tech Stack

**Python · TensorFlow/Keras · scikit-learn · pandas · NumPy · SciPy · Transformers/BERT embeddings · Jupyter Notebook**

Models and methods include Siamese neural networks, autoencoders, MLPs, calibrated Logistic Regression, Random Forests and shadow-model membership-inference attacks.

## Run Locally

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook
```

The repository intentionally excludes the original source dataset, so full retraining requires access to the same approved data/embeddings used in the university project. The notebook and saved artifacts document the modelling and evaluation workflow.

## Key Takeaways

- Model utility and privacy risk should be evaluated together.
- Shadow-model attacks provide a practical framework for testing membership leakage.
- Privacy results should be interpreted within the tested attack configuration rather than generalised too broadly.
- Reproducible ML work benefits from clear dependencies, saved artifacts and documented evaluation outputs.
- Privacy testing belongs in the ML evaluation lifecycle, not as an afterthought.

## Limitations

- The project used synthetic/de-identified data and pre-computed BERT embeddings rather than production clinical data.
- Results apply only to the tested architecture, splits and attack configuration.
- Attack AUC near 0.50 does not prove immunity to stronger or different privacy attacks.
- The public repository cannot reproduce full training without the original approved dataset.

## Ethics & Data Handling

No raw personal or identifiable patient data is included in this repository. The university project used synthetic or de-identified research data in a controlled scope. Large source datasets are intentionally excluded from this public portfolio repository.
