# FLChain: Data Provenance and Ground-Truth Validation

Hey, hello, and Kia Ora!

The FLChain dataset comes from a landmark population study by [Dispenzieri et al. (2012)](https://pubmed.ncbi.nlm.nih.gov/22677072/), which examined whether non-clonal serum immunoglobulin free light chains (FLCs) predict overall survival in the general population.</br>
The cohort included 15,859 adults aged ≥ 50 years from Olmsted County, Minnesota (1995–2003) with follow-up through 2009.</br>
Their findings established FLC levels—particularly the highest decile of total FLC—as strong independent predictors of mortality.</br>

In this post, we verify that the open dataset distributed via [`sksurv`](https://scikit-survival.readthedocs.io/en/stable/api/generated/sksurv.datasets.load_flchain.html) reproduces the multivariable CoxPH results reported by Dispenzieri et al.</br>
Only after re-establishing those results do we treat it as ground truth for survival-aware data synthesis and augmentation.

---

## Where this dataset comes from

Original cohort: 15,859 community-dwelling adults ≥ 50 years from Olmsted County, Minnesota.</br>
Variables: age, sex, serum creatinine, κ and λ free light chains, FLC ratio (κ / λ), total FLC (κ + λ), and clinical outcomes including death and cause of death.</br>
Primary endpoint: All-cause mortality.

Python users can access the same variant through scikit-survival:

```python
from sksurv.datasets import load_flchain
X, y = load_flchain()
```

Note on counts: the `sksurv` release includes 7,874 participants.

---

## How earlier studies used it

Dispenzieri et al. fitted a four-variable multivariable Cox proportional-hazards model with:
* Top decile of total FLC (top 10 % vs others)
* Creatinine (continuous, mg/dL)
* Age (per 10-year brackets)
* Sex (female baseline)

They reported hazard ratios (HR) in Table 2 of their paper, showing that the highest FLC decile more than doubled mortality risk after adjusting for age, sex, and creatinine.

---

## What we found

Replication CoxPH (4 predictors, matching Dispenzieri et al. Table 2)

| Variable                             | HR (95 % CI) — Dispenzieriet al.| HR (95 % CI) — Ours |
| ------------------------------------ | ----------------------------------- | ------------------- |
| Total FLC (Top Decile vs Others) | 2.07 (1.91 – 2.24)                  | 2.14 (1.92 – 2.39)  |
| Creatinine (per unit)            | 1.26 (1.20 – 1.31)                  | 1.24 (1.16 – 1.32)  |
| Age (per 10 years)               | 2.85 (2.76 – 2.95)                  | 2.61 (2.50 – 2.72)  |
| Sex (Male vs Female)             | 1.34 (1.25 – 1.43)                  | 1.29 (1.18 – 1.41)  |

Our hazard-ratio estimates closely mirror those of Dispenzieri et al., confirming that the `sksurv` variant faithfully reproduces the original multivariable relationships.

---

## Extending the model

To explore the dataset’s broader potential, we fit an extended CoxPH including κ, λ, decile of FLC, and MGUS status:

| Variable                     | HR (95 % CI) — Ours |
| ---------------------------- | ------------------- |
| Age (per 10 years)       | 2.55 (2.44 – 2.67)  |
| Sex (Male vs Female)     | 1.30 (1.19 – 1.42)  |
| Creatinine               | 1.05 (0.95 – 1.16)  |
| Top FLC Decile           | 1.39 (1.21 – 1.60)  |
| Decile of Total FLC      | 1.05 (1.03 – 1.07)  |
| Serum FLC Kappa          | 1.03 (0.96 – 1.10)  |
| Serum FLC Lambda         | 1.13 (1.07 – 1.20)  |
| MGUS (Diagnosed vs None) | 1.14 (0.69 – 1.88)  |


Cheers,
– Nic

(Latest update: 2025-11-06)
