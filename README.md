# ML Model Benchmark — Water Quality Prediction

> Final-year BSc dissertation (KV6013, Northumbria University). A four-model benchmark on **8.3 million** UK Environment Agency water-quality samples, testing which ML algorithm best forecasts five core parameters under a strict chronological train/test protocol.

[![Status](https://img.shields.io/badge/status-completed-success)]()
[![Best R²](https://img.shields.io/badge/best%20R²-+0.785-e64d2e)]()
[![Samples](https://img.shields.io/badge/samples-8.3M-blue)]()
[![Python](https://img.shields.io/badge/python-3.10+-blue)]()
[![License](https://img.shields.io/badge/license-MIT-lightgrey)]()

🔗 **[Read the full dissertation walkthrough →](https://nawafbalmutairi.github.io/ml-benchmarking/)**

---

## TL;DR

| | |
|---|---|
| **Question** | Which ML algorithm best forecasts UK water quality from historical signal alone? |
| **Data** | 8.3M samples · 5 parameters · 14 EA regions · 26 years (2000–2025) |
| **Models** | Ridge · Random Forest · MLP · XGBoost |
| **Winner** | **XGBoost** — wins 4/5 targets · R² = **+0.785** on Water Temperature |
| **Lesson** | BOD is structurally unpredictable from lag features alone; the problem is feature engineering, not algorithm choice. |

---

## Results · The Headline

**XGBoost was the only model × target combination to exceed R² = +0.7 across all 20 pairs tested.** Its Water Temperature result of **+0.785** is the dissertation's single best outcome.

### R² heatmap (all 20 combinations)

| Target | Ridge | Random Forest | MLP | **XGBoost** |
|---|---:|---:|---:|---:|
| Nitrate as N | −0.01 | −0.32 | −1.42 | **+0.02** |
| BOD: 5 Day ATU | −0.01 | −3.49 | −0.07 | −0.27 |
| Water Temperature | +0.11 | +0.73 | −3.61 | **+0.79** ★ |
| Dissolved O₂ | +0.36 | +0.18 | +0.46 | **+0.50** |
| pH | −0.01 | +0.16 | +0.08 | **+0.23** |
| **Average R²** | **+0.09** | **−0.55** | **−0.91** | **+0.25** ★ |

XGBoost wins 4 of 5 individual targets. BOD is the only parameter where every model fails — Ridge "wins" with R² = −0.007, which is to say it predicts marginally better than always returning the mean.

---

## Why this benchmark exists

Most water-quality ML papers optimize one model for one target. This dissertation does the opposite: **same data, same preprocessing, same evaluation protocol, four models, five parameters** — a like-for-like comparison the literature doesn't otherwise provide.

The three research questions answered:

1. **RQ1.** Which of the four models is most accurate per parameter? → **XGBoost wins 4 of 5.**
2. **RQ2.** Are some parameters structurally easier to predict? → **Yes; Water Temperature (strong seasonal signal) vs BOD (driven by acute biological events).**
3. **RQ3.** Can the pipeline survive upstream data-source changes? → **Yes; the rebuild after the December 2025 EA API deprecation became a system-design pattern, not a setback.**

---

## Data pipeline (6 stages)

```
EA API (deprecated)  →  Colab ingest  →  PowerShell rename  →  Verify  →  Split  →  Benchmark
   ✕ broken              ⏵ python          ⏵ shell            ⏵ caught 1 bug   ⏵ 27M / 6M     ★ XGBoost wins
```

| Stage | What happens | Volume |
|---|---|---|
| 1. EA API | UK Environment Agency endpoint deprecated Dec 2025 | — |
| 2. Colab ingest | 364 monthly CSVs auto-downloaded via beta endpoint | 26 years × 14 regions |
| 3. PowerShell rename | Standardise filenames by `area__year.csv` | 364 files |
| 4. Verify | Custom script catches year-mismatch in Thames data | 1 silent bug prevented |
| 5. Split | Chronological train (2000–2017) / test (2018–2025) | ~27M train / ~6M test |
| 6. Benchmark | All four models trained with identical preprocessing | 20 model × target pairs |

### Why chronological split, not random?

A random 70/30 split is the ML-tutorial default but it's **wrong** for time-series forecasting. Random splitting allows training on future observations and testing on past ones — a form of data leakage that inflates accuracy in a way that doesn't generalise. The 2018 cutoff respects the arrow of time.

---

## Models compared

| Model | Family | Why included |
|---|---|---|
| **Ridge regression** | Linear · L2-regularised | A transparent floor that non-linear models must justifiably beat. |
| **Random Forest** | Bagged decision trees | Captures non-linearity without explicit feature engineering. |
| **Multi-Layer Perceptron** | Feedforward NN | Tests whether neural representations beat trees on tabular data here. |
| **XGBoost** | Gradient-boosted trees | Industry standard for tabular regression. |

All four received **identical preprocessing** — same lag features, same imputation, same standardisation. Architectural performance is the variable; data prep is not.

---

## Repository structure

```
.
├── README.md                          ← you are here
├── LICENSE                            ← MIT
├── .gitignore                         ← Python ignores
├── requirements.txt                   ← reproducibility
├── notebooks/
│   ├── 01_ingest_ea_beta.ipynb        ← Colab download script
│   ├── 02_clean_and_verify.ipynb      ← year-mismatch check
│   ├── 03_feature_engineering.ipynb   ← lag features, standardisation
│   ├── 04_train_benchmark.ipynb       ← Ridge · RF · MLP · XGBoost
│   └── 05_evaluate_visualize.ipynb    ← R², RMSE, MAE per target
├── scripts/
│   ├── rename_csvs.ps1                ← PowerShell batch rename
│   └── verify_dates.py                ← chronological integrity check
├── dashboards/
│   └── WQ_ML_Benchmark_Dashboard.pbix ← Power BI artefact
├── docs/
│   ├── dissertation.pdf               ← full submitted document
│   └── viva_slides.pptx               ← defence presentation
└── data/
    └── README.md                      ← how to obtain EA data (not redistributed)
```

> **Data licensing note.** UK Environment Agency Water Quality data is published under the Open Government Licence v3.0. The raw CSVs (~3.2 GB) are not redistributed in this repo to keep clone times fast; `notebooks/01_ingest_ea_beta.ipynb` reconstructs the full dataset from source.

---

## Reproduce the result

### Prerequisites

- Python 3.10 or higher
- ~5 GB free disk space (for the downloaded EA CSVs)
- A Google account for Colab (or run locally with a Jupyter server)

### Setup

```bash
# 1. Clone
git clone https://github.com/nawafbalmutairi/ml-water-quality-benchmark.git
cd ml-water-quality-benchmark

# 2. Create environment
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt
```

### Run the pipeline

Either run the notebooks **in order** (01 → 05), or use the scripts directly:

```bash
# Download all 364 CSVs (~25 minutes; respects EA's rate limits)
python -m scripts.download_ea_data --years 2000-2025

# Batch rename (Windows PowerShell)
pwsh scripts/rename_csvs.ps1

# Verify data integrity (catches the Thames year-mismatch bug)
python scripts/verify_dates.py

# Train all four models on all five targets
python -m scripts.benchmark --models ridge,rf,mlp,xgboost --targets all

# Evaluate
python -m scripts.evaluate --output results/
```

Expected runtime end-to-end: **~45 minutes** on an 8-core consumer laptop.

---

## Tech stack

**Language:** Python 3.10
**ML / data:** XGBoost · scikit-learn · PyTorch · pandas · NumPy
**Visualisation:** matplotlib · seaborn · Power BI Desktop
**Engineering:** Google Colab · PowerShell · Git
**Methodology:** Chronological train/test split · R² / RMSE / MAE · five-parameter benchmark

---

## What I'd do differently

Three honest limitations and what would address them:

1. **Feature scope** — only autocorrelative lag features are used. BOD is driven by rainfall, treatment-plant overflows, and agricultural runoff calendars — none of which are in the feature set. Adding Met Office rainfall data would probably move BOD's R² from −0.27 to something useful.
2. **Single chronological split** — one cutoff at 2018 was used. Rolling-origin cross-validation would estimate the stability of these rankings across multiple historical cutoffs.
3. **Default hyperparameters** — Bayesian optimisation or randomised search per model would establish a tighter ceiling. The current numbers represent a *fair* comparison, not necessarily an *optimal* one.

---

## Citation

If you reference this work:

```bibtex
@thesis{almutairi2026water,
  title  = {Comparing Machine Learning Models for Water Quality Prediction},
  author = {Almutairi, Nawaf},
  school = {Northumbria University},
  year   = {2026},
  type   = {BSc Dissertation},
  url    = {https://nawafbalmutairi.github.io/ml-benchmarking/}
}
```

---

## Author

**Nawaf Almutairi** — BSc Computer Science, Northumbria University · Class of 2026

[Portfolio](https://nawafbalmutairi.github.io) · [LinkedIn](https://linkedin.com/in/nawaf-almutairi-907766290/) · [Email](mailto:NawafBAlmutairi@outlook.sa)

Supervised by Dr. Maria Salama.

---

## License

[MIT](LICENSE) — see file for full text.
