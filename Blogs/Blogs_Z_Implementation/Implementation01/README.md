# WHAS500 Data Preparation

<img src="Supporting_Images/WFig08_Whas500Introduction.png" width="600"/>

Hey, hello, and Kia Ora!

This post discusses [WHAS500](https://scikit-survival.readthedocs.io/en/stable/api/generated/sksurv.datasets.load_whas500.html), a classic heart-attack survival cohort which we used in our [MedInfo paper implementation](https://github.com/NicKuo-ResearchStuff/Masked_Clinical_Modelling/blob/main/Blogs/Blogs0a1_HandsOn(MedInfoPaper)/2025-10-15_(2025_03_12)_MCM_WHAS500(Small)_MedInfo2025.ipynb).

WHAS500 is a 500-patient subset of the Worcester Heart Attack Study (WHAS), see page 11 in [the original paper^](https://download.e-bookshelf.de/download/0000/5709/18/L-G-0000570918-0002357449.pdf), originated from a population-based study of outcomes following acute myocardial infarction. It’s widely used for time-to-event (survival) examples and ships with `scikit-survival`, making it ideal for reproducible demos.

The original paper:</br>
*Hosmer, D., Lemeshow, S., May, S.: “Applied Survival Analysis: Regression Modeling of Time to Event Data.” John Wiley & Sons, Inc. (2008)*

---

## How we load the data

```python
from sksurv.datasets import load_whas500

seed_everything()

# Load WHAS500: x = predictors, y = (event, time)
x, y = load_whas500()

import pandas as pd
import copy

# Build a single DataFrame and keep the predictors we need
df_x = pd.DataFrame(x)
df_y = pd.DataFrame(y, columns=["fstat", "lenfol"])
combined_df = pd.concat([df_x, df_y], axis=1)

Selected_predictors = [
    "age", "bmi", "gender",
    "hr",
    "sysbp", "diasbp",
    "cvd", "afb", "chf",
    "miord", "mitype",
    "fstat", "lenfol"
]

combined_df = combined_df[Selected_predictors]

# Clean, readable column names used across the blog
combined_df.rename(columns={
    "age": "Age",
    "bmi": "BMI",
    "gender": "Sex",
    "hr": "Heart Rate",
    "sysbp": "SysBP",
    "diasbp": "DiasBP",
    "cvd": "CVD (M)",
    "afb": "AF (M)",
    "chf": "CHF (M)",
    "miord": "MI Order",
    "mitype": "MI Type",
    "lenfol": "Duration",
    "fstat": "Event"
}, inplace=True)

# Final working copy
my_df = copy.copy(combined_df)
```

---

## A Descriptive snapshot of WHAS500

| Variable                                      | Type    | Category/Statistic         |                          Value/Proportion |
| --------------------------------------------- | ------- | -------------------------- | ----------------------------------------: |
| Age                                       | Numeric | Mean ± SD / Median (Q1–Q3) |       69.84 ± 14.36 / 72.00 (59.00–82.00) |
| BMI                                       | Numeric | Mean ± SD / Median (Q1–Q3) |        26.45 ± 4.15 / 25.67 (23.42–28.89) |
| Sex                                       | Binary  | Male / Female              |                          Baseline / 38.8% |
| Heart Rate                                | Numeric | Mean ± SD / Median (Q1–Q3) |       83.61 ± 19.06 / 80.51 (71.52–92.97) |
| Systolic Blood Pressure (SysBP)           | Numeric | Mean ± SD / Median (Q1–Q3) |   149.16 ± 35.85 / 146.91 (120.30–171.26) |
| Diastolic Blood Pressure (DiasBP)         | Numeric | Mean ± SD / Median (Q1–Q3) |       76.28 ± 30.94 / 78.86 (53.86–95.62) |
| History of Cardiovascular Disease (CVD)   | Binary  | False / True               |                          Baseline / 80.2% |
| History of Atrial Fibrillation (AF)       | Binary  | False / True               |                          Baseline / 11.8% |
| History of Congestive Heart Failure (CHF) | Binary  | False / True               |                          Baseline / 29.6% |
| Myocardial Infarction Order (MI Order)    | Binary  | First / Recurrent          |                          Baseline / 31.6% |
| Myocardial Infarction Type (MI Type)      | Binary  | non Q-wave / Q-wave        |                          Baseline / 29.4% |
| Event                                     | Binary  | Occurred                   |                                     43.0% |
| Duration                                  | Numeric | Mean ± SD / Median (Q1–Q3) | 882.44 ± 705.67 / 631.50 (296.50–1363.50) |

---
## Variables used in the MEDINFO paper

While WHAS500 contains more fields, in the MEDINFO paper we only use this subset:

* Age, Sex, BMI, SysBP (systolic blood pressure), AF (M) (history of atrial fibrillation), CHF (M) (history of congestive heart failure)

```python
medinfo_cols = ["Age", "Sex", "BMI", "SysBP", "AF (M)", "CHF (M)", "Event", "Duration"]
df_medinfo = my_df[medinfo_cols].copy()
```

These variables balance clarity and clinical relevance while keeping the example small enough for transparent demonstration.

---


## Wrapping Up

Now that we’ve slightly explored the WHAS500 dataset, we’re ready to see how it has been used in previous research.

In the next post, we’ll revisit how this dataset appeared in classic survival-analysis examples, and show how we reproduce those results step by step.

Cheers,</br>
\- Nic

(Last edit: 2025-10-16)
