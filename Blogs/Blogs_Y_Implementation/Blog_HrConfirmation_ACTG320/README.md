## ACTG320: Data Provenance and Ground-Truth Validation

Hey, hello, and Kia Ora!

The ACTG320 trial is a landmark randomised study in HIV care. In [Hammer et al.(1997)](https://www.nejm.org/doi/full/10.1056/NEJM199709113371101), investigators tested whether adding indinavir (a protease inhibitor) to a two-drug antiretroviral backbone reduced the risk of AIDS progression or death among adults with CD4 ≤ 200 cells/mm³ and prior zidovudine (ZDV) exposure. Later, [Cole & Stuart (2010)](https://pmc.ncbi.nlm.nih.gov/articles/PMC2915476/) generalised those trial effects to a broader U.S. HIV-infected population, introducing population covariates (notably race) for transportability.

In this post, we verify that the open ACTG dataset shipped with [`sksurv`](https://scikit-survival.readthedocs.io/en/stable/) reproduces the ACTG320 treatment hazard ratios (HRs) reported by Hammer et al.. Only after re-establishing those results do we treat the dataset as ground truth.

---

### Where this dataset comes from

Original cohort: Adults with HIV-1, CD4 ≤ 200 cells/mm³, prior ZDV; randomized to two-drug vs three-drug therapy (the third being indinavir).
Primary endpoint: Time to AIDS-defining event or death (Hammeret al.also analyzeddeath-onlyas a secondary outcome).
Why it matters: ACTG320 is a canonical example of how treatment, immune status (CD4 strata), and functional status influence time-to-event outcomes—ideal for teaching Cox models and for validating survival-aware synthetic data pipelines.

Python users can access the open variant via [scikit-survival](https://scikit-survival.readthedocs.io/en/stable/api/generated/sksurv.datasets.load_aids.html):

```python
from sksurv.datasets import load_aids
X, y = load_aids()  # returns covariates and a structured array with time/event info
```

Note on counts: Hammeret al.report 1,156 participants; the `sksurv` variant we use contains 1,151.

---

### How earlier studies used it
Hammer et al. focused on treatment arm and CD4 strata (≤50 vs 51–200 cells/mm³) as theprimaryclinical drivers of progression risk.
Cole & Stuart extended inference to a target population, adding variables (*e.g.,* race) needed for generalisation/transportability.

For our ground-truth confirmation, we adhere to Hammer et al.’s clinical focus (treatment, CD4 strata). For our multivariable check, we also include routinely captured covariates available in `sksurv`.

---

### What our code does

In the accompanying script [`2025-11-05_HrConfirmationACTG320.py`](https://github.com/NicKuo-ResearchStuff/Masked_Clinical_Modelling/blob/main/Blogs/Blogs_Y_Implementation/Blog_HrConfirmation_ACTG320/2025_11_05_HrConfirmationACTG320.ipynb), we:

1. Load the `sksurv` ACTG dataset (the ACTG320 variant).
2. Engineer variables to match our appendix:

   CD4 strata: ≤50 vs 51–200 (`CD4_51_200` = 1 for 51–200).
   Functional Impairment from Karnofsky Performance Scale (KPS):
     `100→0`, `90→1`, `80→2`, `70→3` (higher = worse).
   Harmonise Sex (0=Female, 1=Male), Treatment (0=two-drug, 1=three-drug), Months prior ZDV, Age.
3. Replicate Hammer’s univariate treatment effect overall and by CD4 strata using CoxPH.
4. Fit a multivariable CoxPH including Age, Sex, CD4 stratum, Treatment, Functional impairment, Months prior ZDV—the six-variable model from your appendix—to show coherent effect directions and magnitudes.

---

### What we find -- establishing the ground truth

Univariate CoxPH for Treatment (three-drug vs two-drug)</br>
(replicating Hammer et al. Table 2)

| Population           | HR (95% CI) -- Hammer et al. | HR (95% CI) -- Ours |
| ------------------------ | ------------------------------- | ---------------------- |
| All patients         | 0.50 (0.33, 0.76)               | 0.50 (0.33, 0.77)      |
| CD4 ≤ 50 cells/mm³   | 0.49 (0.30, 0.82)               | 0.58 (0.36, 0.95)      |
| CD4 51–200 cells/mm³ | 0.51 (0.24, 1.10)               | 0.38 (0.17, 0.88)      |


Overall HR is virtually identical (0.50), confirming the core treatment effect.</br>
CD4 ≤ 50: effect remains protective and statistically significant in both.</br>
CD4 51–200: our HR is 0.38 (significant) vs Hammer’s 0.51 (non-significant); this is the main difference, likely reflecting small sample/definition discrepancies between the released `sksurv` variant and the original trial extract.

---

### Extending the model -- six covariates

To reflect the richer variables available in `sksurv`, we also fit a multivariable CoxPH:

| Variable                     | HR (95% CI) — Ours |
| -------------------------------- | ---------------------- |
| Age                          | 1.02 (1.00, 1.04)      |
| Sex (Male vs Female)         | 0.93 (0.53, 1.62)      |
| CD4 51–200 (vs ≤50)          | 0.41 (0.26, 0.64)      |
| Treatment (3-drug vs 2-drug) | 0.51 (0.33, 0.77)      |
| Functional Impairment        | 1.93 (1.53, 2.44)      |
| Months prior ZDV             | 1.00 (0.99, 1.01)      |

Treatment remains protective after adjustment.</br>
Higher CD4 (51–200) is protective vs ≤50, as expected.</br>
Functional impairment (worse KPS) shows a strong risk gradient.</br>
Age has a modest risk increase per year.</br>
Sex and prior ZDV months show no strong association in this specification.</br>

Cheers,</br>
\- Nic

(Latest update: 2025-11-05)
