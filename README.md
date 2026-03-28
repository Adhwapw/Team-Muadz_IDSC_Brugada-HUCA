# Deep Ensemble Learning for Brugada Syndrome Screening
### CNN-LSTM Representation with XGBoost Decision Layer

> **IDSC 2026** — International Data Science Challenge  
> Team: **Muadz** | Universitas Singaperbangsa Karawang

---

## Overview

This repository contains the full pipeline for automated Brugada Syndrome detection from 12-lead ECG signals. We propose a two-stage hybrid ensemble: a CNN-LSTM network acts as a deep feature extractor, and XGBoost serves as the final meta-classifier trained on those learned representations.

Brugada Syndrome is responsible for up to **20% of sudden cardiac deaths** in patients with structurally normal hearts. Our model aims to assist clinicians by flagging high-risk ECG patterns automatically — functioning as a **digital second opinion**, not a replacement for clinical judgment.

---

## Team

| Name | Institution |
|---|---|
| Adhwa Pranaja Widyadana | Universitas Singaperbangsa Karawang |
| Azka Reza Aditya | Universitas Singaperbangsa Karawang |
| Wildan Mufti Brawijaya | Universitas Singaperbangsa Karawang |

---

## Model Architecture

```
ECG Input (12-lead)
    │
    ▼
┌─────────────────────────────┐
│  Preprocessing              │
│  - StandardScaler           │
│  - Stratified split         │
│  - WFDB signal loading      │
└─────────────────────────────┘
    │
    ▼
┌─────────────────────────────┐
│  CNN (Spatial Features)     │
│  - Conv1D x2                │
│  - Filters: 64 / 128        │
│  - Kernel: 5 / 3            │
│  - MaxPooling1D             │
└─────────────────────────────┘
    │
    ▼
┌─────────────────────────────┐
│  LSTM (Temporal Patterns)   │
│  - Units: 128               │
│  - Dropout: 0.3             │
│  - Dense: 64-dim output     │
└─────────────────────────────┘
    │
    │  64-dim feature vector
    │  extracted from Dense layer
    ▼
┌─────────────────────────────┐
│  XGBoost (Meta-Classifier)  │
│  - Input: 64-dim features   │
│  - scale_pos_weight: 3.78   │
│  - Gradient boosting        │
└─────────────────────────────┘
    │
    ▼
Output: Brugada / Normal
(threshold = 0.49)
```

**Loss Function:** Focal Loss — down-weights easy majority-class samples and focuses training on hard Brugada cases to handle the 3.78:1 class imbalance.

---

## Dataset

We exclusively use the **Brugada-HUCA Dataset** from PhysioNet.

| Property | Value |
|---|---|
| Total recordings | 365 |
| Brugada | 77 |
| Normal | 288 |
| Class ratio | 3.78:1 (Normal:Brugada) |
| Format | WFDB (12-lead ECG) |
| Source | Hospital Universitario Central de Asturias, Spain |

**Download the dataset:**
1. Go to [https://physionet.org/content/brugada-huca/1.0.0/](https://physionet.org/content/brugada-huca/1.0.0/)
2. Create a free PhysioNet account
3. Sign the data use agreement
4. Download and place in the `/data` directory

> ⚠️ External labeled datasets are strictly prohibited per IDSC 2026 rules. Only the official PhysioNet datasets are used.

---

## Results

### Final Test Set (N=73, Brugada=15, Normal=58)

| Metric | Score |
|---|---|
| Accuracy | 84.9% |
| AUC-ROC | 78.9% |
| F1-Score (Brugada) | 0.560 |
| Sensitivity | 46.7% |
| Specificity | 94.8% |
| Decision Threshold | 0.49 |

### 5-Fold Stratified Cross-Validation

| Metric | Mean | Std |
|---|---|---|
| AUC-ROC | 0.745 | ±0.072 |
| Sensitivity | 0.623 | ±0.154 |

---

## How to Run

### Option 1 — Google Colab (Recommended)

1. Open the notebook in Colab:

   [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](YOUR_COLAB_LINK_HERE)

2. Run the first cell to install dependencies:
```bash
!pip install wfdb scikit-learn xgboost tensorflow numpy pandas matplotlib
```

3. Upload the Brugada-HUCA dataset to your Google Drive and update the `DATA_PATH` variable in the notebook.

4. Run all cells sequentially.

### Option 2 — Local

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
cd YOUR_REPO_NAME

# Install dependencies
pip install -r requirements.txt

# Run the notebook
jupyter notebook CNN__LSTM__XGBoost.ipynb
```

---

## Dependencies

```
wfdb
numpy
pandas
scikit-learn
xgboost
tensorflow >= 2.x
matplotlib
seaborn
jupyter
```

---

## Repository Structure

```
├── CNN__LSTM__XGBoost.ipynb   # Main notebook (full pipeline)
├── requirements.txt            # Python dependencies
├── README.md                   # This file
└── figures/
    ├── viz_fig1_eval.png        # Confusion matrix + ROC curve
    ├── viz_fig2_cv.png          # Cross-validation performance
    ├── viz_fig3_learning.png    # Learning curves
    └── viz_fig4_metrics.png     # Final test metrics
```

---

## Ethical AI Statement

- **No data leakage:** Stratified K-Fold cross-validation was strictly implemented.
- **Class imbalance handling:** Focal Loss in CNN-LSTM + `scale_pos_weight` in XGBoost.
- **Threshold calibration:** Adjusted to 0.49 to prioritize Sensitivity over Precision.
- **Limitations acknowledged:** Single-center dataset, 46.7% sensitivity — not suitable for standalone clinical deployment without multi-center validation.
- **Intended use:** Screening aid to assist clinicians, not a replacement for clinical judgment.

---

## Citation

If you use this work, please cite the dataset:

```
Martínez-Rodrigo, A., et al. (2023). Brugada Syndrome ECG Database
(Brugada-HUCA) (version 1.0.0). PhysioNet.
https://doi.org/10.13026/8qm8-g327
```

```
Goldberger, A., et al. (2000). PhysioBank, PhysioToolkit, and PhysioNet:
Components of a new research resource for complex physiologic signals.
Circulation, 101(23), e215–e220.
```

---

## License

This project is submitted as part of IDSC 2026. Code is open for academic and research purposes.
