# AI for Cyber Security -- Network Intrusion Detection on CIC-IDS-2017

Machine-learning intrusion detection comparing **XGBoost** and **Random Forest**
on the CIC-IDS-2017 dataset, framed as **binary** (benign vs. attack, primary)
and **multi-class** (attack-family identification, extension).

This repository is the complete coursework deliverable: a runnable notebook,
the supporting source modules, trained models with load-and-test code, all
figures, and the written report.

---

## Contents

```
.
|-- AICS_CICIDS2017.ipynb        # Main notebook -- runs top to bottom
|-- requirements.txt            # Pinned, free/open-source dependencies
|-- README.md
|-- models/                     # Trained models (XGBoost + Random Forest binary, XGBoost multiclass), encoders, scaler
|-- figures/                    # EDA + evaluation figures + workflow diagrams (PNG)
|-- results/                    # results.json + summary_table.csv
`-- data/                       # Put the 8 CIC-IDS-2017 CSVs here (see below)
```

---

## Quick start

```bash
# 1. (optional) create a virtual environment
python -m venv .venv && source .venv/bin/activate    # Windows: .venv\Scripts\activate

# 2. install dependencies
pip install -r requirements.txt

# 3a. run everything from the notebook
jupyter notebook AICS_CICIDS2017.ipynb     # then Run All

# 3b. or run the pipeline from the command line
python src/run_pipeline.py

# 4. load the saved models and reproduce the reported test metrics
python load_and_test.py
```

---

## Data

The project **auto-detects** its data source. You have two ways to use the real
CIC-IDS-2017 CSVs (the 8 `*.pcap_ISCX.csv` capture files):

**Option A -- point the notebook at your folder (no copying).**
In the first data cell of `AICS_CICIDS2017.ipynb`, set:

```python
DATA_DIR = "DATASET"
```

(The `r"..."` raw-string is important on Windows so backslashes are not treated
as escapes.) Run all cells -- it will print `REAL MODE`.

**Option B -- copy the 8 CSVs into the project's `data/` folder** and leave
`DATA_DIR = "data"`.

For the command-line scripts, set an environment variable instead:

```bash
# macOS/Linux
export AICS_DATA_DIR="/path/to/DATASET"
# Windows PowerShell
$env:AICS_DATA_DIR = "C:\...\DATASET"
python src/run_pipeline.py
python load_and_test.py
```

**Fast first run:** in the notebook set `DEV_SUBSAMPLE = 200_000` to train on a
stratified subsample while you check everything works, then set it back to
`None` for the full, report-quality run.

If no real CSVs are found, the code falls back to a faithful **synthetic** sample
that reproduces CIC-IDS-2017's structure and documented pathologies, so the
notebook always runs. The mode in use is printed loudly.

---

## Reproducibility

* A single random seed (`SEED = 42`) is set for NumPy, the split and every model.
* The split is a **stratified 60/20/20** train/validation/test; duplicates are
  removed **before** splitting so no record crosses partitions.
* Cross-validation is **5-fold stratified**, scored on macro-F1.
* `load_and_test.py` rebuilds the identical test split and re-scores the saved
  models, confirming the reported numbers.

---

## Headline results (sample mode)

Two complementary tree ensembles (bagging vs boosting) under one
leakage-controlled protocol:

| Task   | Model         | Accuracy | Bal-Acc | Macro-F1 | MCC   | FNR   |
|--------|---------------|---------:|--------:|---------:|------:|------:|
| Binary | Random Forest | 0.952    | 0.892   | 0.919    | 0.844 | 0.208 |
| Binary | **XGBoost** (selected) | 0.976 | 0.946 | 0.960 | 0.922 | 0.103 |
| Multi  | Random Forest | 0.932    | 0.392   | 0.426    | 0.788 | --     |
| Multi  | XGBoost       | 0.968    | 0.466   | 0.491    | 0.904 | --     |

XGBoost leads on every metric and trains ~10x faster. **It is selected as the
final model** because it roughly halves the false-negative rate (0.103 vs 0.208)
-- missed attacks are the costliest IDS error. On the multi-class task, high
accuracy (0.97) coexists with low macro-F1 (0.49) -- the imbalance signature,
with rare classes (Bot, WebAttack) at zero recall. See the report for the full
per-class and threshold analysis.

---

## Uploading to GitHub

```bash
# from inside the project folder
git init
git add .
git commit -m "AI for Cyber Security: CIC-IDS-2017 intrusion detection (XGBoost vs Random Forest)"
git branch -M main

# create an empty repo on github.com first, then:
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

Notes:
* `.gitignore` excludes the large raw dataset and notebook checkpoints.
* The trained `models/*.joblib` files are small enough to commit. If you prefer
  not to, add `models/` to `.gitignore` and let users regenerate them by running
  the notebook.
* If you do commit large files later, consider
  [Git LFS](https://git-lfs.com/).

---

## License / attribution

CIC-IDS-2017 is provided by the Canadian Institute for Cybersecurity; cite
Sharafaldin et al. (2018) when using it. All code here is for academic
coursework. Libraries used (NumPy, pandas, scikit-learn, XGBoost, Matplotlib,
seaborn, joblib) are free and open-source.
