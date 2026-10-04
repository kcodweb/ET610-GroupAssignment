# ET610 Group Assignment — Learning Behaviour in OULAD and Moodle

**ET 610: Learning Analytics and Educational Data Mining · Group Assignment**

| Team member | Roll number | Individual contribution |
|---|---|---|
| Karan Bansal | 24B3003 | OULAD data preparation and feature engineering; H1 early-warning prediction models (ablation, cut-off sweep); code repository and reproducibility |
| Aditya Koli | 24B3024 | Moodle preprocessing (User-ID recovery, sessionisation, outcome definition); H3 engagement models; H4 within-learner deadline analysis |
| Keshav Kumar | 24B0354 | H2 first-assessment timeliness analysis; temporal visual analytics and all figures; report writing and structure |

All members: hypothesis selection, interpretation of results and review of the final report.

📄 **Report:** [`Report.pdf`](Report.pdf)  
📓 **Analysis notebook:** [`Analysis.ipynb`](Analysis.ipynb) (saved with outputs)

## Overview

We analyse learner behaviour in two datasets: **OULAD** (32,593 student–module enrolments with daily clicks, assessments and final results) and a **Moodle** event log from one course (172 learners, 146,048 timestamped events, no grades). We test two hypotheses per dataset:

| ID | Dataset | Hypothesis | Method | Result |
|---|---|---|---|---|
| H1 | OULAD | Early behaviour (clicks and assessment) predicts failure/withdrawal better than demographics | Logistic regression & random forest, GroupKFold by student, feature ablation, cut-off sweep | **Supported** — AUC 0.82 vs 0.65 at day 56 (clicks alone 0.77); 0.78 by day 28; 0.80 when trained on earlier runs and tested on 2014J |
| H2 | OULAD | Missing or submitting the first assessment late predicts failure/withdrawal | χ², adjusted logistic regression, robustness checks (banked marks excluded) | **Supported** — missed: OR 20.6, late: OR 2.2 (fully adjusted) |
| H3 | Moodle | Engagement before the final evaluation predicts completing it | Logistic regression controlling for volume and assessment track; LR & RF with repeated CV | **Supported for volume** — OR 1.86 (track-controlled); apparent warning signs vanish once volume is controlled |
| H4 | Moodle | Activity is deadline-driven, but the extra activity is assessment, not study | Wilcoxon signed-rank tests within learners | **Supported** — study falls to ×0.54 in the evaluation window and does not rise beforehand |

## Repository structure

```
ET610-GroupAssignment/
├── Report.pdf             final report
├── Analysis.ipynb         full analysis notebook (executed, with outputs)
├── figures/               figures used in the report
├── data/                  raw data goes here (not committed — see data/README.md)
└── artifacts/             intermediate tables and model results (created by the notebook)
```

| Notebook part | Content |
|---|---|
| 1. OULAD preprocessing | Join OULAD tables, group activity types, daily and weekly click aggregates |
| 2. H1 – early engagement | Cut-off features, feature-set ablation, cut-off sweep, importances |
| 3. H2 – first assessment | First-assessment status; χ² test, adjusted logistic models, robustness checks |
| 4. Moodle preprocessing | Parsing, User-ID recovery, auto-save filtering, 30-minute sessionisation |
| 5. H3 – engagement and completion | Outcome, features, logistic inference, cross-validated ML, permutation test |
| 6. H4 – deadline-driven activity | Per-learner period rates, Wilcoxon signed-rank tests, Synopsis replication |
| 7. Figures | Figures 1–4 of the report |

## How to reproduce

```bash
git clone https://github.com/kcodweb/ET610-GroupAssignment.git
cd ET610-GroupAssignment
pip install -r requirements.txt
# place the raw data as described in data/README.md, then either open the notebook and Run All:
jupyter notebook Analysis.ipynb
# or execute it from the command line:
jupyter nbconvert --to notebook --execute --inplace Analysis.ipynb
```

Run the notebook from the repository root. A full run takes about 5–10 minutes, most of it in the H1 cut-off sweep (random forests at eight cut-offs). Random seeds are fixed.

## Key preprocessing decisions

- **OULAD:** features at cut-off day *c* use only data up to day *c*, and only students still registered at *c* are included; cross-validation folds are grouped by student.
- **Moodle:** 3,334 truncated User IDs recovered from event descriptions; 37,492 system auto-save events excluded from activity counts; sessions split at 30 minutes of inactivity.
- **OULAD banked marks:** 494 first-assessment marks carried over from a previous attempt are excluded from the timeliness analysis (H2).
- **Moodle outcome:** the log has no grades, so the outcome is completing all three sections of the end-of-course evaluation (113 of 169 learners). Course-project learners mostly sat a different set of sections, so H3 controls for project-track membership and is repeated without project learners.
