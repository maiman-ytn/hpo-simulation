# Phase 1: Controlled Monte Carlo Simulation Study

This subfolder contains the complete implementation, data-generating mechanisms, leakage-free evaluation pipelines, meta-XAI analytical scripts, and unit tests for **Paper 1**:

> **Factors Influencing Hyperparameter Optimization Performance in Educational Classification Models: A Monte Carlo Simulation Study**  
> Target Journal: *Journal of Educational Data Mining (JEDM)*

---

## 📌 Executive Summary & Purpose

The objective of this phase is to evaluate the performance, stability, and computational efficiency of four automated hyperparameter optimization (HPO) algorithms (**Bayesian Optimization [BO]**, **Genetic Algorithm [GA]**, **Hyperband [HPB]**, and **Particle Swarm Optimization [PSO]**) across **Random Forest (RF)** and **Support Vector Machines (SVM)** classifiers. 

The evaluation is conducted within a controlled $36$-condition synthetic simulation grounded in the **ADEMP** (Aims, Data-generating mechanisms, Evaluated methods, Performance measures) formal framework (*Morris et al., 2019; Siepe et al., 2024*).

---

## 🔬 ADEMP Simulation Framework

### 1. Data-Generating Mechanisms (DGM)
Synthetic educational classification datasets are generated via `simstudy` under a $3 \times 3 \times 2 \times 2$ full factorial experimental design ($36$ unique simulation conditions):

1. **Sample Size ($n$):** Institutional data scales:
   - $n = 50$: Course-level / small seminar prediction tasks.
   - $n = 200$: Department-level / degree-cohort tasks.
   - $n = 500$: Faculty-level / institutional retention tasks.
2. **Continuous Feature Count ($n_{con}$):** Out of $10$ total attributes:
   - $n_{con} = 3$ ($3$ continuous, $7$ categorical): Demographic & survey dominated.
   - $n_{con} = 5$ ($5$ continuous, $5$ categorical): Balanced composition.
   - $n_{con} = 7$ ($7$ continuous, $3$ categorical): LMS engagement & continuous score dominated.
3. **Predictor Odds-Ratio Influence Level:**
   - `high`: Strong predictor-outcome association.
   - `medium`: Moderate predictor-outcome association.
4. **Relationship Complexity:**
   - `linear`: Logistic regression model with additive linear predictors.
   - `interaction`: Logistic regression model incorporating $6$ non-linear pairwise continuous-categorical interaction terms.
5. **Class Imbalance:** Target $Y \in \{0, 1\}$ generated with a natural $20:80$ to $30:70$ class imbalance ratio reflecting academic failure/dropout skews.

### 2. Evaluated Methods (31 Unique Methods)
- **24 Main Tuned Combinations:**
  - $3$ Learners: Random Forest (`ranger`), Linear SVM (`e1071`), Radial SVM (`e1071`).
  - $2$ Search Spaces: `importance_hp` (restricted high-influence grid) vs. `all_hp` (all-inclusive grid).
  - $4$ HPO Optimizers: BO (`mlr3mbo`), GA (`GA`), HPB (`mlr3hyperband`), PSO (`psoptim`).
- **3 Untuned Baselines:** Default RF, Default Linear SVM, Default Radial SVM.
- **4 Dedicated RF Sensitivity Methods:** Evaluated on legacy search spaces to isolate ensemble stability.

### 3. Leakage-Free Validation Scheme
To eliminate **pre-split resampling data leakage** (a major vulnerability in previous EDM literature), the evaluation pipeline implements a strict **Nested Cross-Validation** protocol using `mlr3pipelines`:
- **Outer Loop:** Stratified 10-fold cross-validation repeated across $100$ Monte Carlo replications ($3,600$ evaluation runs). Outer test sets ($30\%$ holdout) remain completely untouched at their natural $20:80$ imbalance.
- **Inner Loop:** 5-fold cross-validation for HPO parameter search.
- **Inner-Fold ROSE Resampling:** Random Over-Sampling Examples (ROSE) is executed **strictly within inner training folds** via `po("classbalancing")` encapsulated inside a `GraphLearner`.

---

## 📂 Subfolder Layout

```text
phase1_simulation/
├── README.md                            <- Technical documentation (this file)
│
├── R/                                   <- Modular R source functions
│   ├── 01_dgm_simstudy.R                <- Synthetic data generation functions
│   ├── 02_hpo_pipelines.R               <- Leakage-free GraphLearner wrappers (mlr3pipelines)
│   ├── 03_meta_xai_dalex.R              <- RF meta-models & DALEX permutation importance
│   └── 04_generate_ggplot2_figures.R   <- Publication-quality ggplot2 visualization script
│
├── scripts/                             <- Main executable script
│   └── Phase1_MonteCarlo_Simulation_Corrected.R <- Full 3,600-run Monte Carlo execution entry point
│
├── data/                                <- Output datasets
│   └── processed_results/
│       └── master_simulation_3600runs.csv  <- Full aggregated simulation benchmark results
│
├── figures/                             <- Rendered publication figures
│   ├── fig1_meta_xai_importance.png / .pdf
│   ├── fig2_accuracy_stability_by_scale.png / .pdf
│   └── fig3_time_efficiency_tradeoff.png / .pdf
│
└── tests/                               <- Verification unit tests
    └── test_leakage_free_pipeline.R     <- Test confirming test-set purity and zero leakage
```

---

## 🚀 Execution & Reproducibility Guide

### Step 1: Install Dependencies
```R
install.packages(c(
  "simstudy", "ROSE", "mlr3", "mlr3tuning", "mlr3pipelines", 
  "mlr3learners", "ranger", "e1071", "DALEX", "ggplot2", 
  "patchwork", "ggrepel", "data.table", "future", "testthat"
))
```

### Step 2: Verify Leakage-Free Pipeline (Unit Test)
Run the verification test to confirm that outer test sets remain untouched at natural imbalance ratios:
```bash
Rscript tests/test_leakage_free_pipeline.R
```

### Step 3: Execute Full Monte Carlo Simulation
To run the full 36-condition Monte Carlo simulation ($3,600$ total evaluation trials):
```bash
Rscript scripts/Phase1_MonteCarlo_Simulation_Corrected.R
```

### Step 4: Generate Publication Figures
To produce high-resolution 300 DPI PNG and vector PDF figures for manuscript inclusion:
```bash
Rscript R/04_generate_ggplot2_figures.R
```

---

## 📊 Meta-Level Explainable AI (Meta-XAI) & Multiplicity Control

- **Meta-Models:** Random Forest regression models (`ranger`) fitted on aggregated benchmark results to predict Balanced Accuracy, CV, and Tuning Duration from experimental design factors.
- **Surrogate Adequacy Taxonomy:** Evaluated under a 6-tier adequacy framework requiring dataset-cohort cross-fitted RMSE $\le .010$ (Accuracy) and $\le .10$ (Log-time).
- **DALEX Permutation Importance:** Reported in raw **Squared-Loss / RMSE Loss Units**. Negative estimates resulting from sampling noise are preserved rather than truncated to zero.
- **Multiplicity Control:** Hypothesis testing conducted across **12 Primary Planned Contrasts** and **121 Secondary Contrasts** with Family-Wise Error Rate (FWER) controlled via **Holm Adjustment** ($\alpha = .04$ primary, $\alpha = .01$ secondary).

---

## 📄 License

This simulation framework is released under the **MIT License**.
