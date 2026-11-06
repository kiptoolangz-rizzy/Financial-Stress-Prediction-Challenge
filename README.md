# Liquidity Stress Early Warning — Mobile Money

My solution to the [Zindi Liquidity Stress Early Warning Challenge](https://zindi.africa/competitions/liquidity-stress-early-warning-challenge).

## The challenge

Mobile money is central to everyday financial life across Africa. When financial pressure builds, customers often show warning signs in their transactions before any formal default.

The task is to predict whether a customer will experience **liquidity stress in the 30 days after an observation date** (`liquidity_stress_next_30d`), using six months of mobile money history. That history covers:

- inflows: money received, deposits, bank transfers in
- outflows: cash withdrawals, sends, bill (paybill) and merchant payments
- daily average balance
- transaction counts and account activity
- customer profile: age, gender, region, segment, earning pattern

Each row is one customer snapshot. The submission gives a probability of stress for each test `ID`.

## Approach

- **Model:** LightGBM, validated with 5-fold stratified cross-validation, final predictions averaged over 3 seeds.
- **Features (~520):** for each monthly behaviour, its level, spread, zero-activity months, slope and recent-vs-older ratios (last 1–5 months vs the rest). On top of those, cash-flow features over 1–6 months: net flow, outflow ÷ inflow, cash-out share, and balance cover.
- **Key finding:** stress follows a squeeze over the last 3 months, where outflows catch up with inflows while the balance falls. Customer identity and past stress labels added nothing.

## Results

| Metric (5-fold CV) | Score |
|---|---|
| AUC | **≈ 0.914** |
| Log-loss | 0.239 (baseline 0.423) |

## Data

The competition data is **not included** in this repository. Download it from the challenge's [Data page on Zindi](https://zindi.africa/competitions/liquidity-stress-early-warning-challenge/data) and place these files in the project folder, next to the notebook:

```
Train.csv
Test.csv
SampleSubmission.csv
data_dictionary.csv   (optional, for reference)
```

## Setup

Requires Python 3.11+.

```bash
python -m venv zindi
zindi\Scripts\activate          # Windows
# source zindi/bin/activate     # macOS / Linux
pip install -r requirements.txt
```

## Usage

```bash
jupyter notebook
```

Open `Liquidity_Stress_Prediction.ipynb` and run all cells (about 5 minutes). The notebook writes `submission.csv` in the `ID, Target` format ready to upload to Zindi.

## Project structure

```
├── Liquidity_Stress_Prediction.ipynb   # full pipeline: EDA, features, CV, submission
├── requirements.txt
├── README.md
└── .gitignore                          # excludes data, outputs and .venv
```
