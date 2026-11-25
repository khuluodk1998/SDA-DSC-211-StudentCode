<div align="center">

# 💳 Monthly Payment Obligation Risk Prediction
### التنبؤ بمخاطر التأخر في سداد الالتزامات الشهرية

**An educational machine-learning project exploring risk-aware financial decisions**  
**SDA-DSC-211 · SDAIA Academy · Google Colab**

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Google Colab](https://img.shields.io/badge/Google-Colab-F9AB00?logo=googlecolab&logoColor=white)](https://colab.research.google.com/)
[![Training](https://img.shields.io/badge/SDAIA-Academy-176B57)](https://github.com/SDAIAAcademy)
[![Project](https://img.shields.io/badge/Status-Educational%20Prototype-blue)](#limitations)

**[My GitHub Repository](https://github.com/khuluodk1998/SDA-DSC-211-StudentCode) · [SDAIA Academy](https://github.com/SDAIAAcademy) · [Course Template](https://github.com/almiyead-rgb/sda-dsc-211-student-template)**

</div>

---

## 📌 Overview

Many people manage recurring commitments such as **electricity, water, rent, car installments, and loan payments**. My project idea is to explore whether machine learning could help **identify potential late-payment risk before a payment is due**, allowing early, human-led review rather than an automatic penalty.

**Important distinction:** the actual five-day SDA-DSC-211 experiments use **synthetic Tamweel Lite loan-application data**, *not* real monthly utility or rent transactions. Their label is a **90-day application outcome**, and the model uses features available at application time. The monthly-obligations problem is the **proposed application** of the techniques learned. Performance on actual household bills has **not** been measured.

### 🎯 Project goals

- Compare interpretable and boosted models to predict a rare positive outcome.
- Prevent data leakage and evaluate predictions using realistic time-ordered validation.
- Choose a review threshold that balances missed-event costs with operational capacity.
- Explain predictions, inspect calibration and regional error differences.
- Export reproducible model artifacts, predictions, and evidence for final review.

## 📊 Key results from my saved training runs

| Result | Measured value | What I learned |
|:---|---:|:---|
| Leaky random AP (Day 2) | **0.9988** | Leakage can make a model look unrealistically accurate. |
| Clean random AP (Day 2) | **0.3110** | Performance fell substantially once leakage was removed. |
| Honest forward OOF coverage (Day 2) | **5,039 / 10,000 (50.39%)** | Warm-up rows had no eligible OOF prediction. |
| Capacity-aware threshold (Day 3) | **0.658347** | A threshold needs to consider review workload. |
| Simulated cost: default → constrained (Day 3) | **2,403 → 2,639** | Capacity compliance increased simulated loss by **236 units**. |
| Regional FPR gap (Day 3) | **0.6484 percentage points** | Region-level differences require denominator-aware review. |
| Brier score before → after calibration (Day 4) | **0.11303 → 0.06711** | Improved on a separate Day 4 evaluation period (**1,733 rows**). |
| Final selected model (Day 5) | **Logistic Regression** | The ensemble gate retained the single model. |
| Final model AP, 3-fold mean ± SD (Day 5) | **0.39166 ± 0.02981** | Model comparison is descriptive, not proof of universal superiority. |
| Final challenge review flags (Day 5) | **300 / 2,500 (12%)** | **30** of **330** eligible flags were removed to respect the batch cap. |

> These are **Tamweel Lite course-run outputs**, not measured accuracy for household bills. The Day 1 uploaded notebook did not retain numerical comparison output, so I do not claim a Day 1 winning score. Day 5 challenge labels were unavailable: no challenge AP, cost or fairness score is claimed.

## 🧭 Project workflow

```text
Synthetic application data (Tamweel Lite)
                  │
                  ▼
       Application-time features
                  │
                  ▼
      Leakage checks + data cleaning
                  │
                  ▼
   Forward, customer-separated validation
                  │
                  ▼
       Compare candidate models
                  │
                  ▼
       Select and calibrate model
                  │
                  ▼
  Apply cost + review-capacity constraints
                  │
                  ▼
    Predictions, audit reports and bundle
```

## 🧪 My five-day learning journey

| Day | Focus | Experiment / observation |
|:---|:---|:---|
| **01** | Baselines and boosting | Compared **Logistic Regression**, **XGBoost** and **LightGBM**; learned to use AP alongside ROC-AUC for imbalanced outcomes. |
| **02** | Leakage-safe validation and tuning | Compared unsafe random controls with forward, customer-separated OOF evaluation; completed **8 Optuna trials** (best search AP ≈ **0.333269**). |
| **03** | Cost-sensitive threshold decisions | Used **FN cost = 10**, **FP cost = 1**, and **12% per-period review capacity**. The constrained policy flagged **526 / 5,039** OOF rows, versus **1,005** at threshold 0.5. |
| **04** | Explainability and calibration | Used permutation importance and global/local SHAP, checked calibration and a review zone; learned that explanations are model-specific, not causal. |
| **05** | Model selection, policy and export | Compared single models and ensembles; retained **Logistic Regression** and exported a **2,500-row** challenge submission with **300** flags. |

## 🤖 Models and evaluation

### Models explored

**Logistic Regression**, **XGBoost**, **LightGBM**, and in Day 5 three ensemble approaches (**equal-weight**, **weighted**, **stacking**).

### Final model comparison — Day 5

| Candidate | Mean Average Precision (AP) |
|:---|---:|
| **Logistic Regression — selected** | **0.39166** |
| Weighted ensemble | 0.38942 |
| Stacking ensemble | 0.38314 |
| Equal-weight ensemble | 0.37170 |
| XGBoost | 0.35263 |
| LightGBM | 0.34549 |

The final gate decided **`KEEP SINGLE → Logistic`**. More complexity was not justified by the observed results. These are three forward-fold comparisons; fold dependence and different evaluation contexts limit the conclusion.

### Validation decisions

- **No future information:** predictions use only fields known at application time; a later payment/outcome date would leak the answer.
- **Time and customer separation:** forward folds prevent future-to-past evaluation, and customer grouping reduces overlap.
- **90-day maturity:** an application is eligible only when its outcome has had enough time to be observed.
- **Training-only preprocessing:** imputation and tuning are fitted inside training, with an inner early-stopping set separate from outer comparison data.
- **AP + ROC-AUC:** AP helps evaluate detection of the minority positive class where accuracy may be misleading.
- **OOF thresholding:** use out-of-fold development predictions, not over-optimistic training predictions or hidden challenge labels.

## 🎛️ Decision policy, explanation and fairness

**Day 3 threshold study.** The selected development threshold **0.658347** met review caps across the three periods (**137/195**, **183/200**, **206/207**). It reduced the number of flagged OOF cases, at an additional simulated cost of **236 units** compared with the infeasible default policy. The regional FPR gap was **0.6484 percentage points**; this alone does not establish fairness without the region-specific negative denominators and sample uncertainties.

**Day 4 interpretation.** Global importance describes population-level patterns; local SHAP describes a single case relative to a background reference, with the Day 4 SHAP configuration reported in **log-odds units**. Features including `bureau_score` and `dti` were influential, but correlated features and noncausal explanations limit reason statements. On **1,733 rows (139 positives)**, Brier improved **0.113027 → 0.067112** and ECE **0.146871 → 0.022486**. The paired customer-cluster percentile interval for Brier change was **[−0.054177, −0.037409]**; it does not capture all future uncertainty. Review-zone candidate counts exceeded capacity in **2024Q3 (109/100)** and **2024Q4 (122/107)**.

**Day 5 version boundary.** The final selected model is **Logistic Regression**. The **Day 4 SHAP explanations belong to the earlier Day 4 model** and must not be presented as explanations of the final model without rerunning the interpretation and calibration checks. Day 5 calibration-fit diagnostics used **836 rows / 78 positives** and are **not independent holdout evidence**. Raw and sigmoid Brier scores were **0.076473** and **0.078058**, so I do **not** claim an independently verified calibration improvement for the final model.

## 🗂️ Repository structure

This is the **required submission layout** from the final checker, not a claim that all files have already been uploaded:

```text
SDA-DSC-211-StudentCode/
├── README.md
├── requirements-colab.txt
├── constraints.txt
├── data/
│   ├── data_contract.json
│   ├── data_manifest.json
│   ├── tamweel_train.csv
│   └── tamweel_challenge.csv
├── notebooks/
│   ├── 01_baseline_boosting.ipynb
│   ├── 02_validation_tuning.ipynb
│   ├── 03_cost_sensitive_decision.ipynb
│   ├── 04_explain_calibrate.ipynb
│   ├── 05_final_model.ipynb
│   └── 99_final_submission_check.ipynb
├── tamweel/
│   ├── __init__.py
│   └── inference.py
├── scripts/
│   ├── inference.py
│   └── rebuild_final.py
├── artifacts/
│   ├── final_model/
│   ├── final_policy.json
│   ├── final_metrics.json
│   └── ... day-specific CSV, JSON and PNG results
├── evidence/
│   ├── day1/
│   ├── day2/
│   ├── day3/
│   └── day4/
├── reports/
│   ├── MODEL_CARD.md
│   └── ENSEMBLE_DECISION.md
└── submission/
    └── submission.csv
```

## ▶️ How to run

1. Open the individual notebooks in **Google Colab**, in order from Day 1 to Day 5. Use the course-pinned dependencies in `requirements-colab.txt` and `constraints.txt`.
2. Run and **save executed outputs**. Keep each `.ipynb` under its required `notebooks/` filename; a combined notebook is not a substitute for the individual files.
3. Export each day's **actual** evidence ZIP and place its contents under `evidence/day1/` through `evidence/day4/`.
4. Run Day 5 in **LIVE** mode with the course's permitted data, export the model, policy, `submission.csv`, reports, and all supporting files. Preserve directories when uploading to GitHub.
5. Run `99_final_submission_check.ipynb` against a ZIP of the **project root**, not just the notebook or a ZIP inside another ZIP. Record the final Git commit SHA/tag through the private submission channel.

`replay_final.py` is intended to reproduce saved predictions, while `rebuild_final.py` retrains and recalibrates. A result such as **`BUNDLE_BYTES_VERIFIED`** confirms bundled file hashes only — **not** acceptance or a grade.

## 📝 Daily answers | الإجابات اليومية

### Day 1 — Model choice and decision time

My learning goal was to identify risk from fields available **at application time** and compare Logistic Regression, XGBoost and LightGBM using ROC-AUC and **AP**, which is especially useful for rare positives. The available Day 1 copy lacks saved comparison scores, so I cannot honestly cite a Day 1 winner. I would evaluate on a chronological, customer-separated set and check fold variability before confirming a candidate. The later final selection was Logistic Regression, not a reconstructed Day 1 result. A comparison set must stay separate from early stopping so its scores remain a fair comparison.

### Day 2 — Leakage-safe validation

I used time ordering, customer separation, and a strict **90-day label maturity** rule. A post-application outcome or actual payment date would be leaked information because it is unavailable when the decision is made. AP dropped from **0.9988** (leaky random) to **0.3110** (clean random), about **0.6879** difference using unrounded run values. Clean random versus honest fixed differed by about **−0.0044** AP. These protocols evaluate different populations with only three folds, so they do not establish universal superiority. I fitted imputation inside training, reserved inner stopping from outer evaluation, ran only **8 search trials**, and obtained OOF predictions for **5,039 / 10,000** eligible rows (the remaining **4,961** were warm-up rows).

### Day 3 — Threshold and capacity

I selected the **0.658347** threshold because it respects the **12% per-period review limit**. Compared with threshold 0.5, it reduced flags from **1,005** to **526** out of **5,039** OOF observations, with simulated loss increasing from **2,403** to **2,639**. This is the measured cost–capacity trade-off, not proof of optimality everywhere. The regional FPR difference was about **0.6484 percentage points**; denominator sizes and sampling variation need review. Accuracy can be inflated by majority negatives, while OOF predictions support threshold selection without reusing training scores or challenge labels.

### Day 4 — Explanation and calibration

Global feature importance summarizes overall model behavior, while local SHAP explains a specific example against a reference background in **log-odds units** for this explainer. Correlation makes feature attributions difficult to interpret independently, and reason statements are not causal proof. On a separate **1,733-row period with 139 positives**, Brier changed **0.113027 → 0.067112** and ECE **0.146871 → 0.022486**. The customer-cluster resampling interval for Brier difference **[−0.054177, −0.037409]** does not cover new environments or distribution drift. The review zone exceeded capacity in **2024Q3 (109 vs 100)** and **2024Q4 (122 vs 107)**. These are explanations for the **Day 4 model**, not automatically for the Day 5 model.

### Day 5 — Final model and evidence

I kept **Logistic Regression**: mean AP **0.39166**, fold SD **0.02981**, ahead of weighted ensemble **0.38942** and the other tested candidates. Three-fold forward OOF scores support the decision only for this experiment, not universal model superiority. Final calibration diagnostics were evaluated on the **836 calibration-fit rows (78 positives)**, not an untouched challenge-label set, and sigmoid Brier **0.078058** was higher than raw **0.076473**. In the **2,500-row** challenge, the batch cap was **300 (12%)**: **330** threshold-eligible, **30** removed. I would monitor AP, drift, calibration, capacity and region-level FPR with actual denominators. The final model needs new explanation checks because Day 4 examined a different model. This synthetic experiment must **not** be used for real credit approvals, penalties or restrictions.

## ⚠️ Limitations and responsible use

- **Training data are fictional loan applications**, not real utility bills or rent records. Applying this method to monthly obligations needs its own lawful dataset and new evaluation.
- **Three forward folds**, search-budget constraints, OOF warm-up exclusions, model-specific SHAP, and calibration-fit diagnostics limit interpretation.
- Synthetic region checks do not establish real-world fairness. No **challenge AP, loss or FPR** is reported without challenge labels.
- No production deployment, lending decision, financial penalty, or automated restriction is justified by these experiments.

## ✅ Submission status

The saved Day 5 export reported **`REPLAY_MATCH`** and **`BUNDLE_BYTES_VERIFIED`**, but also **`PROJECT_WORK_REQUIRED`**. At that time the daily evidence, separately executed notebooks, and final presentation were not all available in the bundle. The combined notebook is helpful for review, but **does not replace** the six notebook paths required by the checker. Final verification must be rerun after assembling the actual project files.

## 🙏 Acknowledgments

This project was developed as part of the **SDA-DSC-211 training programme** through [**SDAIA Academy — @SDAIAAcademy**](https://github.com/SDAIAAcademy), using the [official student template](https://github.com/almiyead-rgb/sda-dsc-211-student-template). The methods and results are presented as an educational experiment, and not as production validation.

**#SDAIAAcademy**
