<!-- PROJECT LOGO -->
<br />
<p align="center">
  <a>
    <img src="https://github.com/gerryc-0/WILTY-QML/blob/main/WILTY_project_image.png" 
         alt="WILTY Icon" width="400">
  </a>

</p>

# WILTY Predicting Panel Accuracy: Classical vs Quantum ML

Predicting whether the Would I Lie To You? panel correctly identifies a lie or the truth. A Classical vs Quantum ML comparison across logistic regression, SVM, KNN, random forest, and XGBoost baselines, variational quantum classifiers, and a quantum kernel SVM, benchmarked from noiseless simulation through to real IBM quantum computers.

**Module:** COMP47950: Quantum Machine Learning

---

## Overview

This project benchmarks classical and quantum machine learning pipelines against the same binary classification task: predicting whether the *Would I Lie To You?* (WILTY) panel correctly identifies a lie or the truth, using only player history and game-context features.

Three pipelines are compared end-to-end:

| Pipeline | Description |
|---|---|
| **Classical** | Five models (Logistic Regression, SVM, KNN, Random Forest, XGBoost) with grid-search tuning and filter-based feature selection |
| **Simulated QML** | Variational Quantum Classifiers (VQC) with multiple ansatz configurations + a Quantum Kernel SVM (QSVM), trained via noiseless statevector simulation |
| **Hardware QML** | Trained VQC weights deployed for inference on a real IBM quantum device (`ibm_fez`), shot-based |

---

## Key Methods

### Classical Baseline
- Feature engineering on player history, opponent detection rates, and episode context
- Aggregated feature importance ranking across Random Forest, Logistic Regression, and SVM to select top-5 features
- Final model: SVM (`RBF` kernel)

### Quantum Models
- **Encoding:** Angle encoding (ZZFeatureMap) over 5 qubits corresponding to the top-5 classical features
- **VQC configurations:** EfficientSU2 ansatz (4/6/8 layers), custom data-motivated ansatz with heavier entanglement between qubits 0–1 (`player_fool_rate` × `opponent_avg_detection_rate`), and alternative feature set experiments
- **QSVM:** Quantum kernel SVM trained with COBYLA optimiser on 100 training samples
- **Optimiser:** COBYLA (gradient-free, suitable for noisy settings)
- **Training:** Noiseless statevector simulation; hardware run is inference-only

### Hardware Inference
- Executed on IBM quantum hardware via the IBM Quantum free tier
- Shot-based measurement (1024 shots per sample)
- Compared against noiseless simulation to quantify decoherence and gate-error impact

---

## Results Summary

| Model | Test Accuracy | Macro F1 |
|---|---|---|
| Majority baseline | 0.562 | 0.360 |
| SVM RBF (best classical) | 0.615 | 0.549 |
| VQC best noiseless (angle + EfficientSU2, 4L) | 0.630 | n/a |
| VQC retrained (150 iter) | 0.555 | 0.523 |
| QSVM COBYLA (100 samples) | 0.622 | 0.610 |
| VQC hardware (`ibm_fez`) | 0.562 | 0.360 |

> **Note:** The hardware VQC collapsed to majority-class prediction. Accuarcy score masked this, macro-f1 score tells the truth. The QSVM matched the classical SVM on accuracy and exceeded it on f1.

---

## Setup

```bash
pip install qiskit qiskit-machine-learning scikit-learn pandas numpy matplotlib
```

Tested with:
- Python 3.10+
- Qiskit 1.x
- qiskit-machine-learning 0.7.x
- scikit-learn 1.3+

To reproduce hardware results you will need an [IBM Quantum](https://quantum.ibm.com/) account and API token.

---

## Acknowledgements

Dataset sourced from the open WILTY dataset repository. Project completed as part of COMP47950 Quantum Machine Learning.
