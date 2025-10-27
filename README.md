<!-- Logo and Branding -->
<p align="left">
  <img src="Supporting_Images/WFig001_MCMLogo.png" alt="Masked Clinical Modelling Logo" width="300"/>
</p>

---

# About Masked Clinical Modelling (MCM)

Masked Clinical Modelling (MCM) [1, 2] is a research framework developed at the  
Centre for Big Data Research in Health (CBDRH), UNSW Sydney, extending the  
[Health Gym](https://github.com/NicKuo-ResearchStuff/Health_Gym_AI) initiative.

It introduces a BERT-inspired generative framework for creating and augmenting survival analysis datasets,  
balancing data realism with clinical utility — ensuring that models trained on synthetic data  
preserve key survival metrics such as hazard ratios and calibration.

* What it provides: a flexible modelling pipeline for conditional synthesis and augmented survival data generation.
* What it improves: downstream CoxPH discrimination (C-index) and calibration consistency.
* What it enables: privacy-preserving, subgroup-targeted data generation for equitable model training.

---

<!-- Side-by-side layout -->
<table>
<tr>
<td width="60%">

**Highlights of MCM**

* Dual-purpose framework: supports both standalone synthetic data generation and targeted augmentation.
* Utility-oriented: preserves clinical interpretability (hazard ratios, calibration slopes).
* Attention-guided architecture: predicts masked clinical features using contextual cues.
* Evaluation-ready: seamlessly integrates with standard survival models (*e.g.,* CoxPH).
* Open science: fully reproducible notebooks and Colab links for demonstration and benchmarking.

</td>
<td width="40%">
  <img src="Supporting_Images/WFig002_MCMSurivival.png" alt="MCM Framework Overview" width="300"/>
</td>
</tr>
</table>

---

<table>
<tr>
<td width="60%">

**Methodology Overview**

* Masking Phase: randomly hide a subset of clinical features (*e.g.,* age, SBP, AF, CHF).
* Attention Phase: an Attention Filter (AF) identifies relevant observed features for reconstruction.
* Reconstruction Phase: a two-layer MLP predicts the missing values, minimising MSE.

This mechanism allows MCM to:
- Impute incomplete clinical records,
- Synthesise new virtual patients, and
- Augment minority subgroups for improved risk model calibration.

</td>
<td width="40%">
  <img src="Supporting_Images/WFig003_MCMIdeas.png" alt="Masked Clinical Modelling Pipeline" width="300"/>
</td>
</tr>
</table>

---

<!-- Side-by-side layout: text and illustration -->
<table>
<tr>
<td width="60%">

**Worked Examples of the Models**

* [MCM: A First Look at Survival-Aware Synthetic Data](https://github.com/NicKuo-ResearchStuff/Masked_Clinical_Modelling/tree/main/Blogs/Blogs0a1_HandsOn(MedInfoPaper)):</br>
  A high-level walkthrough of MCM, showing how masked learning reconstructs survival data to generate realistic, hazard-consistent synthetic cohorts..

For a detailed step-by-step breakdown of the implementation process, see the [Implementation Series](https://github.com/NicKuo-ResearchStuff/Masked_Clinical_Modelling/tree/main/Blogs).</br>
Latest update: 2025-10-27.
  
</td>
<td width="40%">
  <img src="Supporting_Images/WFig004_MCMWorked.png" alt="Health + Data Illustration" width="300"/>
</td>
</tr>
</table>

---

# Team

The MCM is a series of research projects of the  
Centre for Big Data Research in Health (CBDRH), UNSW Sydney.  

- Concept created by: Nic Kuo, Blanca Gallego, & Louisa Jorm  
- Implementation: Nic Kuo  

For any questions or interest in collaboration, please reach out to Nic at [n.kuo@unsw.edu.au](mailto:n.kuo@unsw.edu.au)

---

# References

[1]: [Kuo N.I-H., Gallego B., Jorm L.R. “Masked Clinical Modelling: A Framework for Synthetic and Augmented Survival Data Generation.” *MEDINFO 2025* (IOS Press).](https://ebooks.iospress.nl/doi/10.3233/SHTI250917)

[2]: [Kuo N.I-H., Gallego B., Jorm L.R. “Attention-Based Synthetic Data Generation for Calibration-Enhanced Survival Analysis.” *arXiv preprint arXiv:2503.06096* (2025). (To appear in JBI 2025 Special Issue)](https://arxiv.org/html/2503.06096v1)

(Last edit: 2025-10-27)
