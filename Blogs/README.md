# MCM Implementation Series

<img src="Supporting_Images/WFig09_McmImplementationSeries.png" alt="Health + Data Illustration" width="300"/>

Hey, hello, and Kia Ora!

Welcome to the Implementation Series of Masked Clinical Modelling (MCM) -- where we turn concepts into code and ideas into reproducible pipelines.

This is where we walk through the inner mechanics of MCM step by step: how masking, attention, and reconstruction come together to generate survival-aware synthetic and augmented data that preserve key clinical signals.

---

## What is this series?

If you’ve seen the main MCM overview, you already know the philosophy behind the framework.
Here, we zoom in to the hands-on layer -- showing exactly how real survival tables become masked inputs, how the model learns to reconstruct missing features, and how those outputs are validated for realism, calibration, and fairness.

---

## Posts in the Series

### [Implementation 01: Data 101: WHAS500 Data Preparation](https://github.com/NicKuo-ResearchStuff/Masked_Clinical_Modelling/tree/main/Blogs/Blogs_Z_Implementation/Implementation01)

This is a walkthrough of the WHAS500 heart-attack survival cohort, where we show its data characteristics.

### [Implementation 02: Data 102: WHAS500: Classic Survival Analysis and Ground-Truth Validation](https://github.com/NicKuo-ResearchStuff/Masked_Clinical_Modelling/tree/main/Blogs/Blogs_Z_Implementation/Implementation02)

In this post, we replicate UCLA’s WHAS500 CoxPH results to verify that the dataset introduced in Implementation 01 is consistent and reliable.

(Last Edit: 2025-10-17)
