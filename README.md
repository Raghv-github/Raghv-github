# Breast Cancer Survival Analysis (GBSG)

Recurrence-free survival analysis of 686 breast cancer patients from the German Breast Cancer Study Group (GBSG) trial, combining classical statistics and machine learning:

- **Kaplan–Meier** estimates and **log-rank** tests
- **Cox Proportional Hazards** regression
- **Survival Gradient Boosting** (`scikit-survival`)

## Dataset

[Breast Cancer Dataset (Royston & Altman)](https://www.kaggle.com/datasets/utkarshx27/breast-cancer-dataset-used-royston-and-altman) – `gbsg.csv`, 686 patients, no missing values, 299 events and 387 censored observations.

| Column | Description |
| ------ | ----------- |
| `pid` | Patient identifier |
| `age` | Age (years) |
| `meno` | Menopausal status (0 = pre, 1 = post) |
| `size` | Tumour size (mm) |
| `grade` | Tumour grade |
| `nodes` | Number of positive lymph nodes |
| `pgr` | Progesterone receptors (fmol/L) |
| `er` | Estrogen receptors (fmol/L) |
| `hormon` | Hormonal therapy (0 = no, 1 = yes) |
| `rfstime` | Recurrence-free survival time (days) |
| `status` | Event (0 = censored, 1 = recurrence or death) |

See [`data/README.md`](data/README.md) for how to get the file.

## Project structure

```
.
├── notebooks/
│   └── breast_cancer_survival_analysis.ipynb
├── images/                 # figures exported from the notebook
├── data/
│   └── README.md           # data download instructions
├── requirements.txt
├── .gitignore
├── LICENSE
└── README.md
```

## Getting started

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt

jupyter notebook notebooks/breast_cancer_survival_analysis.ipynb
```

> `scikit-survival` can be tricky to install with pip on some systems. If it fails, use conda: `conda install -c sebp scikit-survival`.

## Methods and results

### Kaplan–Meier

Median recurrence-free time for the whole cohort is **1,807 days**.

![Overall KM](images/km_overall.png)

**Hormonal therapy** – the log-rank test shows a significant difference between treated and untreated patients (χ² = 8.56, 1 df, p < 0.005).

![KM by hormonal therapy](images/km_by_hormonal_therapy.png)

| Menopausal status | Tumour grade |
| :---: | :---: |
| ![KM by menopause](images/km_by_menopausal_status.png) | ![KM by grade](images/km_by_tumour_grade.png) |

Age and lymph-node curves use cumulative thresholds (e.g. `nodes > 5` includes `nodes > 10`), so the groups overlap by design.

| Age thresholds | Positive lymph nodes |
| :---: | :---: |
| ![KM by age](images/km_by_age.png) | ![KM by nodes](images/km_by_nodes.png) |

### Cox Proportional Hazards

All covariates except `pid` were included (686 patients, 299 events). Concordance 0.69 (in-sample), likelihood-ratio test 101.86 on 8 df.

| Covariate | Hazard ratio | 95% CI | p |
| --------- | :---: | :---: | :---: |
| `nodes` | 1.05 | 1.04 – 1.07 | < 0.005 |
| `pgr` | 1.00 | 1.00 – 1.00 | < 0.005 |
| `grade` | 1.32 | 1.08 – 1.63 | 0.01 |
| `hormon` | 0.71 | 0.55 – 0.92 | 0.01 |
| `size` | 1.01 | 1.00 – 1.02 | 0.05 |
| `meno` | 1.31 | 0.91 – 1.87 | 0.14 |
| `age` | 0.99 | 0.97 – 1.01 | 0.31 |
| `er` | 1.00 | 1.00 – 1.00 | 0.71 |

![Cox coefficients](images/cox_coefficients.png)

Hazard ratio > 1 means higher risk. More positive nodes and higher grade increase risk, while hormonal therapy is associated with about a 29% lower hazard.

> `pgr` and `er` show a hazard ratio of 1.00 because they are measured on a very large scale (0 – 2,380 fmol/L); a log-transform would make their effects easier to read.

### Survival Gradient Boosting

`GradientBoostingSurvivalAnalysis(n_estimators=200, learning_rate=0.05, max_depth=3, random_state=42)` on an 80/20 train/test split gave a **test C-index of 0.655**.

| Feature | Importance |
| ------- | :---: |
| `nodes` | 0.294 |
| `pgr` | 0.212 |
| `age` | 0.189 |
| `size` | 0.140 |
| `er` | 0.110 |
| `hormon` | 0.028 |
| `grade` | 0.027 |
| `meno` | 0.000 |

![Feature importance](images/gb_feature_importance.png)

Lymph-node count ranks first in both models. Predicted survival curves for individual test patients:

![Predicted survival curves](images/gb_predicted_survival.png)

### Model comparison

Both models were scored on the same held-out 20% (138 patients):

| Model | Test C-index |
| ----- | :---: |
| Cox Proportional Hazards | **0.663** |
| Survival Gradient Boosting | 0.655 |

The simpler Cox model is slightly ahead. With 686 patients and 299 events, the extra flexibility of boosting did not help, and the 0.008 gap is well within what a single split can produce by chance.

## Limitations

- Single 80/20 split on 686 patients, so the C-index estimates (0.663 vs 0.655) are noisy and the models cannot be ranked reliably; cross-validation and hyperparameter tuning would help.
- The proportional-hazards assumption of the Cox model was not formally tested (`cph.check_assumptions`).
- Age and node thresholds are overlapping groups, not independent strata.
- Results are observational/educational and not medical advice.

## Possible next steps

1. **Cross-validation** – replace the single split with 5-fold (or repeated) CV and report mean ± std C-index for both models.
2. **Check the Cox assumptions** – run `cph.check_assumptions(df_model)` and stratify or add interactions if the proportional-hazards assumption fails.
3. **Feature engineering** – log-transform `pgr`, `er` and `nodes`; treat `grade` as categorical.
4. **Hyperparameter tuning** – `GridSearchCV` over `n_estimators`, `learning_rate`, `max_depth`, `subsample`.
5. **More models** – Random Survival Forest, regularised Cox (`CoxnetSurvivalAnalysis`), and compare with time-dependent AUC and the integrated Brier score.
6. **Permutation importance** – the built-in boosting importances can be biased; permutation importance on the test set is more reliable.
7. **Calibration** – check that predicted survival probabilities match observed ones (e.g. at 2 and 5 years).

## Tech stack

Python · pandas · NumPy · matplotlib · seaborn · lifelines · scikit-survival · scikit-learn

## License

MIT – see [LICENSE](LICENSE).
