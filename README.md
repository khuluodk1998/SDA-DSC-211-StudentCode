# Monthly Payment Obligation Risk Prediction
## التنبؤ بمخاطر التأخر في سداد الالتزامات الشهرية

**SDAIA · SDA-DSC-211 | Applied Machine Learning Training Project**  
**SDAIA Academy on GitHub:** [@SDAIAAcademy](https://github.com/SDAIAAcademy)

**Student repository:** [SDA-DSC-211-StudentCode](https://github.com/khuluodk1998/SDA-DSC-211-StudentCode)  
**Training template:** [SDA-DSC-211 Student Template](https://github.com/almiyead-rgb/sda-dsc-211-student-template)

> **Project scope:** This is a proposed **monthly household-payment risk** use case demonstrated with the course's **synthetic application dataset**. The notebooks have not been shown to contain real electricity, water, rent or installment-payment records. The validation evidence therefore describes the training experiment, **not verified accuracy on real monthly-bill data**.

## 1. Project idea | فكرة المشروع

Many individuals manage recurring financial obligations such as **electricity, water, rent, car installments, loan repayments, internet bills and other monthly expenses**. The project explores whether a machine-learning classifier could help estimate the **risk of a late or missed payment**, so that individuals can plan ahead and receive timely, supportive budgeting reminders.

The aim is **financial awareness and educational decision support**, not automatic denial of services, credit decisions or penalties.

### Problem statement

- **Predicted outcome:** `1` = a monthly obligation is paid late or missed; `0` = paid on time (proposed application target).
- **Decision time:** Before the payment due date, using only information already available at that time.
- **Unit of prediction:** A person–month or obligation–month, to be specified when suitable payment data are collected.
- **Intended use:** Demonstrate risk ranking, model validation, responsible threshold selection and understandable risk explanations.
- **Potential inputs in a future real-data adaptation:** Income, total monthly obligations, rent, electricity and water bills, installment amounts, previous *known* payment delays, and disposable income. These are **proposed features**, not a verified list of columns in the existing notebooks.

## 2. Why this matters | أهمية المشروع

Recognizing possible cash-flow pressure before due dates could support better monthly budgeting, expense prioritization and proactive reminders. However, a prediction is not proof that someone will default, and model outputs must be checked for fairness, calibration and changing conditions.

## 3. Course workflow | مراحل المعسكر

| Day | Topic | Connection to monthly-payment risk |
|---|---|---|
| **01** | Logistic Regression, XGBoost and LightGBM | Compare classifiers for detecting higher-risk late-payment cases; inspect ROC-AUC and Average Precision (AP). |
| **02** | Leakage prevention, time ordering, customer separation, mature labels | Prevent future payment information from leaking into earlier predictions and make validation more realistic. |
| **03** | OOF-based threshold, cost and review capacity, regional audit | Choose how many predicted high-risk cases can responsibly be reviewed or receive reminders. |
| **04** | Global/local explanations, SHAP, calibration and stability | Explain risk signals without claiming causation; check whether predicted probabilities match later outcomes. |
| **05 — if required** | Final model/ensemble decision and delivery | Decide whether added complexity offers meaningful benefit and prepare final submission. |

**Progress note:** The student reported completing four training days. The final day and its outputs must be checked against the trainer's latest submission instructions; do not treat them as complete automatically.

## 4. Daily decisions and homework | الواجبات اليومية

### Day 01 — Model comparison

**Decision:** I provisionally consider **XGBoost** for the monthly-payment-risk teaching example because it can model nonlinear interactions between financial obligations and available resources. This is **not yet a measured winner**: the final choice must be based on the actual comparison with Logistic Regression and LightGBM. ROC-AUC assesses ranking across thresholds, while **AP** helps reveal performance on the less common late-payment class. The comparison sample must remain separate from early stopping.

**Evidence to complete from the Day 01 run:** `[model name, ROC-AUC, AP, competing model values, sample size]`.

### Day 02 — Honest validation

**Potential leakage example:** Actual final payment status or payment date cannot be used before the due-date decision point. The course experiment uses time ordering, customer separation and a strict **90-day label-maturity** rule. Imputation is fitted within training folds; the inner stopping set guides training duration without reusing the final comparison sample.

**Measured course-run comparisons shared during the project:**
- **AP difference, leaky random − clean random:** `+0.6879`.
- **AP difference, clean random − honest fixed:** `−0.0044`.

These differences demonstrate how evaluation protocol changes the observed scores; they do **not** prove one model universally superior. The compared protocols may use different populations, and fold-level statistics should not automatically be interpreted as confidence intervals.

### Day 03 — Threshold and capacity

For reminder or review prioritization, choosing `0.5` by default may not balance missed-risk cost and available review capacity. A lower threshold flags more cases but can increase false alarms and workload; a higher threshold reduces workload but risks missing more late payments. The threshold should be selected from **out-of-fold (OOF) development predictions**, not training predictions or held-out challenge data. Regional false-positive-rate gaps must be checked with group denominators before claiming equitable performance.

**Evidence still needed:** `[selected threshold]`, `[loss at chosen and reference thresholds]`, `[flagged / available review slots]`, `[regional FPR values and denominators]`.

### Day 04 — Interpretability and responsible use

Global explanations show patterns across evaluated cases; local SHAP explanations describe one prediction relative to the stated background and model-output unit. Neither demonstrates that an expense or personal attribute **causes** delayed payment. Correlated features may redistribute attribution. Calibration must be evaluated using a **separate period** and actual sample counts. The stability interval describes only the resampling uncertainty covered by the method, not all future economic or population changes. A review zone consumes period capacity and should be reported as `[review cases / available slots]`.

**Evidence still needed:** `[global top feature with value]`, `[one local example and SHAP output unit/background]`, `[calibration result and n]`, `[stability interval]`, `[review-zone count and capacity]`.

### Day 05 — Final decision (if part of submission)

Compare a single model with any proposed ensemble using out-of-sample results, operational complexity and the trainer's required Worth-It Gate. Do not claim an ensemble improves performance until the measured results support it.

## 5. Evaluation and safeguards | التقييم والضوابط

- **Class imbalance:** Accuracy alone can look strong when most obligations are paid on time; AP, ROC-AUC, precision, recall and operating-threshold results provide a more useful evaluation.
- **No future information:** Features must be known at the decision time; real payment outcomes must mature before being used as labels.
- **OOF selection:** Development thresholds are selected on predictions from models that did not train on those observations; final challenge data stay untouched.
- **Calibration:** A high discrimination score does not guarantee well-calibrated probabilities.
- **Fairness and privacy:** Examine regional and other relevant error gaps; do not expose personal financial records or use predictions for automatic adverse decisions.
- **Synthetic-data limitation:** Results cannot establish performance in real household-payment settings without a new representative dataset and external evaluation.

## 6. Requested deliverables | المخرجات المطلوبة

| Deliverable | Description | Status |
|---|---|---|
| GitHub repository | Organized code, documentation and outputs | Repository exists; completeness not verified |
| Colab notebooks | Executed notebooks for each training day | Four days reported completed |
| Model comparison and validation report | Scores, fold comparisons and leakage audit | Requires exported source files |
| Decision Card | Threshold, expected loss, cost and capacity | Requires Day 03 measured values |
| Interpretability Report | Global/local explanation, calibration and stability | Requires Day 04 measured values |
| Ensemble Decision | Final single-model vs ensemble choice | Pending evidence |
| Model Card | Purpose, data, metrics, limitations and responsible use | To finalize with real run outputs |
| `submission.csv` | Required submission predictions, preserving the expected format | Not yet verified |
| Execution evidence | Reproducible, runnable code and artifacts | Not yet verified |
| Five-slide presentation | Evidence-based final summary | To prepare after results are verified |

## 7. Proposed repository structure | تنظيم المستودع

```text
SDA-DSC-211-StudentCode/
├── README.md
├── notebooks/                # Daily Colab notebooks
├── artifacts/                # Exported plots, tables and metrics
├── reports/                  # Decision Card, interpretability report, Model Card
├── submission/               # Final CSV and submission bundle
└── slides/                   # Final five-slide presentation
```

The structure is a **target organization**, not a statement that every folder already exists.

## 8. Reproducing the learning exercise | إعادة التشغيل

1. Open the relevant notebook in Google Colab and run it in order using the course-provided synthetic dataset.
2. Inspect model results, leakage checks, split audits, OOF coverage and explanations.
3. Export the requested data tables, metrics, plots and prediction files rather than relying on screenshots alone.
4. Replace all bracketed evidence markers in this README with **measured values and denominators** from the run.
5. Complete the official final checks required by the training template before submission.

## 9. Future real-world development | التطوير المستقبلي

To apply this idea to genuine monthly payment obligations, a future phase would need an approved payment dataset, a precise late-payment definition and time horizon, privacy controls, careful customer/time splits, calibrated probabilities, and monitoring for data drift and group disparities. The current educational experiment is a **methodology demonstration**, not a deployed personal-finance service.

---

**References:** [Student repository](https://github.com/khuluodk1998/SDA-DSC-211-StudentCode) · [Official training template](https://github.com/almiyead-rgb/sda-dsc-211-student-template)
