# Network Intrusion Detection on CIC-IDS-2017

Random Forest vs XGBoost for flow-based intrusion detection, built for my MSc
Cyber Security Engineering "AI for Cyber Security" module. Trained and compared
both models on the full CIC-IDS-2017 dataset (2.83M network flows).

## Results

| Task   | Model                  | Accuracy | Macro-F1 | MCC    | FNR    |
|--------|------------------------|---------:|---------:|-------:|-------:|
| Binary | Random Forest          | 0.9990   | 0.9982   | 0.9964 | 0.0022 |
| Binary | **XGBoost (selected)** | 0.9992   | 0.9985   | 0.9970 | 0.0005 |
| Multi  | XGBoost                | 0.9991   | 0.9421   | 0.9970 | -      |

**XGBoost selected** - it misses only 43 of 85,176 test attacks vs Random Forest's
187 (77% fewer), and trains ~7x faster.

## Contents

```
AICS_CICIDS2017.ipynb   Main notebook (self-contained)
models/                 Trained models, encoders, scaler
figures/                EDA + evaluation plots
results/                Metrics (json + csv)
DATASET/                CIC-IDS-2017 CSVs (Git LFS)
requirements.txt
```

## Run

```bash
pip install -r requirements.txt
```
Open the notebook, set `DATA_DIR` to the dataset folder, and run all cells.

**Tools:** Python, scikit-learn, XGBoost, pandas, NumPy, matplotlib, seaborn.
