# MCM + CKD B002 — Part pre-5:</br> Understanding Calibration Before We Measure It

<img src="https://github.com/NicKuo-ResearchStuff/Masked_Clinical_Modelling/blob/main/Supporting_Images/WFig18_AlShamsiCkdEmr_B002P06_Fig01.png" width="600"/>

Hey, hello, and Kia Ora!

Before we jump into the intended Part 5, let us include a Part pre-5.</br>
This post documents how I see epidemiological calibration from my perspective, as someone with an AI/ML backgroud.

We will discuss:</br>
* What is calibration, formally?
* How do we get from LPH → survival → predicted risk?
* Why do we talk about 25th / 50th / 75th percentile follow-up?

---

## 1. From CoxPH to Predicted Risk: The Softmax Analogy

Say we have a pre-trained CoxPH model,</br>
then for each individual $i$, the model learns a linear predictor (also called the xlog-partial-hazard) where:

$\eta_i = X_i^\top \beta,$

or in our implementation:

$\text{LPH}_i = (X_i - \mu)^\top \beta,$

where:
* $X_i$ = vector of covariates (age, BP, eGFR, etc.),
* $\beta$ = estimated regression coefficients,
* $\mu$ = mean of the covariates in the training set (for centring).

In CoxPH, the individual hazard is:

$h_i(t) = h_0(t) , \exp(\text{LPH}_i),$

so $\text{LPH}_i$ is literally the log-relative hazard compared to the baseline.

From here we obtain an individual survival probability at time (t):

$S_i(t) = [S_0(t)]^{\exp(\text{LPH}_i)}$,

where $S_0(t)$ is the baseline survival.

Finally, we convert survival into a predicted risk (probability of the event by time $t$):

$\hat{r}_i(t) = 1 - S_i(t).$

For deep learning folks, let's think of</br>
$\hat{r}_i(t)$ as the CoxPH analogue of a softmax output:</br>
* it lies in ([0,1]),
* it’s monotonic in the hidden representation (LPH),
* and it represents a predicted probability of the event by time $t$.

And we use $\hat{r}_i(t)$ for calibration.

---

## 2. Observed Events: Where “Labels” Come From

In survival analysis, we do not have static labels like “class 0 / class 1”.

Instead, we look at whether the event has happened by a particular time $t$.</br>
For each individual $i$:
$y_i(t) \in {0,1},$

where:
* $y_i(t) = 1$: the event has occurred by time $t$,
* $y_i(t) = 0$: the event has not occurred by time $t$ (either still alive / event-free, or censored before $t$).

Note how:</br>
The observed event rate is derived directly from the real data.

For AI/ML readers, imagine:
* In supervised learning, you usually have a true label for each sample.
* In survival analysis, at each time point $t$, you recompute who has had the event.
* Calibration becomes a kind of self-supervised post-training evaluation:
  * We don’t retrain the model.
  * We just group people by predicted risk and count how many actually had the event.

---

## 3. Formal Definition: What Does “Calibrated” Mean?

Fix a time horizon $t$.</br>
Let $\hat{r}_i(t) \in [0,1]$ be the predicted risk for individual $i$.
Let $y_i(t) \in {0,1}$ be the observed event indicator by time $t$.

A prediction model is perfectly calibrated at time $t$ if:

$\text{P}\big(Y(t) = 1 ,\big|, \hat{r}(t) = r\big) = r
\quad \text{for all } r \in [0,1].$

In plain English:</br>
* Among people whom the model says “you have a 20% risk by time $t$”,
* about 20% should actually experience the event by time $t$.

Calibration cares about matching predicted probabilities to observed frequencies.</br>

---

## 4. Empirical Calibration: Bin-Based Approximation

Here’s how we turn the formal definition into something we can compute.

1. Compute predicted risk</br>
   $\hat{r}_i(t) = 1 - S_i(t), \quad i = 1,\dots,n.$

2. Sort individuals by predicted risk and split them into</br>
   $K$ groups (bins), often $K=20$.

3. For each bin of people with similar predicted risk, we compute:

   * Mean predicted risk</br>
     $\bar{\hat{r}}_{k}(t) =$
     
     $\frac{1}{|B_{k}|} \sum_{i \in B_k} \hat{r}_i(t)$

   * Observed event rate (derived from the real data)</br>
     $\bar{y} _{k}(t) =$

     $\frac{1}{|B_{k}|} \sum_{i \in B_k} y_i(t)$

At this point, we have 20 points:

$\big(\bar{\hat{r}}_k(t), \bar{y}_k(t)\big), \quad k = 1,\dots,K,$

which form an empirical calibration curve:</br>
predicted probability on the y-axis, observed event rate on the x-axis.</br>
(watch out here, some people might use the y-axis for the observed instead of the predicted)

---

## 5. Calibration Slope and $D_{21}$

To simplify the calibration curve into a single number, we regress:

$\bar{y}_k(t) \approx \beta(t) , \bar{\hat{r}}_k(t),$

using a **linear regression through the origin**.

* $\beta(t) = 1$ → perfect calibration
* $\beta(t) < 1$ → under-prediction (predicted risks too low)
* $\beta(t) > 1$ → over-prediction (predicted risks too high)

We can summarise this miscalibration using:

$D_{21}(t) = \left|1 - \beta(t)\right|.$

* $D_{21}(t) = 0$ is ideal.
* Larger values mean worse calibration.

---

## 6. Recap: What is LPH, and How Does LPH → Survival → Risk Work?

```text
                ┌────────────────────────────────────────┐
                │      1. Linear Predictor (LPH)         │
                │  LPH_i = (X_i – μ)ᵀ β                  │
                │  • Derived from patient covariates     │
                │  • Represents log-relative hazard      │
                └────────────────────────────────────────┘
                                 │
                                 ▼
                ┌────────────────────────────────────────┐
                │     2. Relative Hazard Contribution    │
                │  h_i(t) = h_0(t) * exp(LPH_i)          │
                │  • LPH scales the baseline hazard      │
                │    up or down for each patient.        │
                └────────────────────────────────────────┘
                                 │
                                 ▼
                ┌─────────────────────────────────────────┐
                │        3. Survival Function S_i(t)      │
                │  S_i(t) = [S_0(t)] ^ exp(LPH_i)         │
                │  • Combine baseline survival S₀(t) with │
                │    patient-specific relative hazard.    │
                │  • Produce individual survival curves.  │
                └─────────────────────────────────────────┘
                                 │
                                 ▼
                ┌─────────────────────────────────────────┐
                │     4. Predicted Risk at Time t         │
                │  r_i(t) = 1 − S_i(t)                    │
                │  • Converts survival into cumulative    │
                │    event probability.                   │
                │  • This r_i(t) is what calibration      │
                │    uses as the y-axis.                  │
                └─────────────────────────────────────────┘
```

Cheers,</br>
\- Nic

(Last Edit: 2025-11-24)
