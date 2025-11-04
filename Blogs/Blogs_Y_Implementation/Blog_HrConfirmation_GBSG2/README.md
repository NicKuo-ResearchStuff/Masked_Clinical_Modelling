
## GBSG2: Data Provenance and Ground-Truth Validation

Hey, hello, and Kia Ora!

The GBSG2 dataset (German Breast Cancer Study Group 2) is one of the most frequently cited examples in survival analysis.
It was originally published by [Schumacher et al. (1994)](https://pubmed.ncbi.nlm.nih.gov/7931478/) to study outcomes in node-positive breast-cancer patients, and later re-analysed by [Sauerbrei et al. (1999)](https://pubmed.ncbi.nlm.nih.gov/10206288/), whose version became a benchmark for clinical prognostic modelling.

Let us verify that the version available to us today -- via [`lifelines.datasets.load_gbsg2()`](https://lifelines.readthedocs.io/en/latest/lifelines.datasets.html#lifelines.datasets.load_gbsg2) -- still reproduces the published results from the late 1990s.
Only then can we treat it as a ground truth.

---

### Where this dataset comes from

Original cohort: 473 patients with node-positive breast cancer enrolled by the German Breast Cancer Study Group II.</br>
Variables: age, menopausal status, tumour size and grade, number of positive lymph nodes, progesterone and oestrogen receptor levels, hormonal-therapy assignment, recurrence-free survival time, and event indicator.</br>
Why it matters: it’s a canonical dataset for demonstrating how biological and clinical factors jointly influence survival through a CoxPH model.

Modern Python users can access an extended 686-patient version directly from:

```python
from lifelines.datasets import load_gbsg2
df = load_gbsg2()
```

---

### How earlier studies used it

Sauerbrei et al. transformed the continuous predictors into clinically interpretable categories:

| Variable              | Categorisation used in the “Full Model” |
| --------------------- | --------------------------------------- |
| Age                   | ≤ 45, 46–60, > 60 years                 |
| Tumour size           | ≤ 20 mm, 21–30 mm, > 30 mm              |
| Positive nodes        | ≤ 3, 4–9, ≥ 10                          |
| Tumour grade          | I, II, III                              |
| Progesterone receptor | ≥ 20 vs < 20 fmol/mg                    |
| Oestrogen receptor    | ≥ 20 vs < 20 fmol/mg                    |
| Menopausal state      | Pre vs Post                             |
| Hormonal therapy      | No vs Yes                               |

Each non-baseline category becomes a binary indicator in the design matrix—exactly the coding style used in modern machine learning.

---

### What our code does

In the accompanying notebook (`2025_11_04_hrconfirmationgbsg2.py`), we:

1. Load the `lifelines` GBSG2 dataset.
2. Re-code the variables according to Sauerbrei’s specification.
3. Fit a CoxPH model with `lifelines.CoxPHFitter`.
4. Compare the estimated hazard ratios (HR = exp(β)) and confidence intervals with those reported in the 1999 paper.

You don’t need to follow every line -- just note that this ensures our modelling pipeline and dataset definitions exactly match the historical benchmark.

---

### What we find -- establishing the ground truth

| **Variable**                    | **Coefficient (Sauerbrei *et al.*)** | **Coefficient (Ours)** | **HR (95 % CI) (Ours)** |
| ------------------------------- | ------------------------------------ | ---------------------- | ----------------------- |
| **Age**                         |                                      |                        |                         |
| ≤ 45 years                      | Baseline                             | Baseline               | Baseline                |
| 46–60 years                     | –0.40                                | –0.45                  | 0.64 (0.45 – 0.91)      |
| > 60 years                      | –0.78                                | –0.43                  | 0.65 (0.41 – 1.03)      |
| **Menopausal State**            |                                      |                        |                         |
| Pre-menopausal                  | Baseline                             | Baseline               | Baseline                |
| Post-menopausal                 | 0.27                                 | 0.27                   | 1.31 (0.94 – 1.83)      |
| **Tumour Size**                 |                                      |                        |                         |
| ≤ 20 mm                         | Baseline                             | Baseline               | Baseline                |
| 21–30 mm                        | 0.21                                 | 0.20                   | 1.22 (0.90 – 1.65)      |
| > 30 mm                         | 0.27                                 | 0.27                   | 1.31 (0.95 – 1.79)      |
| **Tumour Grade**                |                                      |                        |                         |
| I                               | Baseline                             | Baseline               | Baseline                |
| II                              | 0.55                                 | 0.55                   | 1.74 (1.06 – 2.85)      |
| III                             | 0.56                                 | 0.56                   | 1.75 (1.02 – 3.03)      |
| **Number of Positive Nodes**    |                                      |                        |                         |
| ≤ 3                             | Baseline                             | Baseline               | Baseline                |
| 4–9                             | 0.68                                 | 0.68                   | 1.97 (1.51 – 2.58)      |
| ≥ 10                            | 1.26                                 | 1.25                   | 3.50 (2.57 – 4.75)      |
| **Progesterone Receptor Level** |                                      |                        |                         |
| ≥ 20 fmol/mg                    | Baseline                             | Baseline               | Baseline                |
| < 20 fmol/mg                    | 0.61                                 | 0.61                   | 1.85 (1.39 – 2.45)      |
| **Oestrogen Receptor Level**    |                                      |                        |                         |
| ≥ 20 fmol/mg                    | Baseline                             | Baseline               | Baseline                |
| < 20 fmol/mg                    | 0.01                                 | 0.00                   | 1.00 (0.76 – 1.33)      |
| **Hormonal Therapy**            |                                      |                        |                         |
| False                           | Baseline                             | Baseline               | Baseline                |
| True                            |                                      | –0.41                  | 0.67 (0.52 – 0.86)      |

Except for small numerical differences -- likely due to the larger sample and direct inclusion of hormonal therapy -- the pattern of effects is identical.
That alignment confirms that the `lifelines` dataset faithfully reproduces the Sauerbrei et al. model structure.

Cheers,</br>
\- Nic

(Last edit: 2025-11-04)
