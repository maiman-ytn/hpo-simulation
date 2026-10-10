# Factors Influencing Hyperparameter Optimization Performance in Educational Classification Models: A Monte Carlo Simulation Study

This repository organizes a completed thesis-based Monte Carlo simulation study evaluating hyperparameter optimization (HPO) techniques in Educational Data Mining (EDM) classification models.

---

## 📌 Project Overview & Origin

This project originates from the Master's thesis:
> **Maiman, Y.** (2024). *Comparison of Hyperparameter Tuning Techniques in Educational Classification Models by Monte-Carlo Simulation*. Master of Education Thesis, Department of Educational Research and Psychology, Faculty of Education, Chulalongkorn University.

The research focuses on a 36-condition Monte Carlo simulation evaluating the impacts of sample size ($n$), continuous predictor count ($n_{con}$), variable relationship complexity, and hyperparameter search-space boundaries (`importance_hp` vs. `all_hp`) across four HPO algorithms (BO, GA, HPB, PSO) on Random Forest (RF) and Support Vector Machine (SVM) models.

---

## 🔬 Monte Carlo Simulation Design

The simulation study evaluates predictive accuracy, stability (Coefficient of Variation: CV), tuning duration, and time efficiency across 100 Monte Carlo replications per experimental condition.

| Experimental Factor | Levels / Configurations | Description |
| :--- | :--- | :--- |
| **Sample Size ($n$)** | $50, 200, 500$ | Course-level (small), department-level (medium), and institutional-level (large). |
| **Predictor Ratios ($n_{con}$)** | $3:7, 5:5, 7:3$ | Number of continuous features ($n_{con} \in \{3, 5, 7\}$) out of 10 total predictors. |
| **Relationship Complexity** | Linear vs. Non-linear | Linear logistic models vs. models with 6 non-linear interaction terms. |
| **Search Space Definition** | `importance_hp` vs. `all_hp` | Restricted influential parameter set vs. comprehensive parameter set. |
| **HPO Algorithms** | BO, GA, HPB, PSO | Bayesian Optimization, Genetic Algorithm, Hyperband, Particle Swarm Optimization. |
| **Classifiers & Kernels** | RF, SVM (Linear / Radial) | Random Forest (`ranger`) and Support Vector Machines (`e1071`). |
| **Class Imbalance Control** | Standardized ROSE | Inner-fold training sets are standardized using Random Over-Sampling Examples (ROSE). |

### Meta-Level Explainable AI (XAI) Analysis
To analyze benchmark outcomes, **Random Forest regression meta-models** were fitted using experimental design factors as predictors. **DALEX permutation-based feature importance** (measured via Root Mean Square Error / Mean Dropout Loss) was then applied to quantify the relative impact of each factor on overall accuracy, stability, and time efficiency.

*Note: This meta-XAI analysis evaluates the influence of experimental design factors on algorithm performance, which is distinct from analyzing feature importance of the simulated dataset predictors.*

---

## 📋 Simulation-Derived Practical Guidelines

The guidelines below provide condition-specific recommendations for EDM practitioners based on synthesis of simulation results:

| Practitioner Objective | Recommended HPO Algorithm | Classifier & Kernel | Search Space Definition | Practical Rationale & Context |
| :--- | :--- | :--- | :--- | :--- |
| **1. Maximize Accuracy** | **BO** or **HPB** | **Random Forest** / **SVM (Radial)** | `importance_hp` | Concentrates evaluation budget on key parameters, achieving peak accuracy under larger sample sizes ($n \ge 200$). |
| **2. Time-Efficiency Trade-off** | **PSO** ($n \ge 200$) / **GA** ($n = 50$) | **Random Forest** | `all_hp` | Delivers optimal balance between accuracy gains and computational runtime. |
| **3. Maximize Stability & Low CV** | **BO** or **HPB** | **SVM (Linear)** | `importance_hp` / `all_hp` | Yields minimal variance across replications ($\mathrm{CV} \le 0.08$ at $n=500$) for reliable deployment. |

---

## 📁 Repository Organization

```text
educational-hpo-benchmark/
├── README.md                            <- Project overview and guidelines
├── LICENSE                              <- MIT License
├── phase1_simulation/                   <- Monte Carlo simulation package
│   ├── README.md                        <- Simulation execution details
│   ├── 01_data_generation.R           <- Synthetic dataset generator
│   ├── 02_hpo_simulation.R            <- Primary HPO simulation entry point
│   ├── 03_xai_dalex_analysis.R        <- Meta-level XAI analysis script
│   └── R/                             <- Helper functions library
│       └── simulation_functions.R
└── results/                             <- Aggregated benchmark output directory
    ├── README.md
    └── Master_Simulation_Results.csv
```

---

## 🚀 Getting Started

### Prerequisites
* **R Environment:** Version >= 4.2 (Tested on R 4.3.3).
* **Required R Packages:** `simstudy`, `mlr3`, `mlr3learners`, `mlr3tuning`, `mlr3mbo`, `mlr3hyperband`, `GA`, `psoptim`, `ROSE`, `DALEX`, `dplyr`, `tidyr`, `ggplot2`.

### Running a Simulation Scenario
To execute a single test condition:
```r
source("phase1_simulation/R/simulation_functions.R")
source("phase1_simulation/01_data_generation.R")
```

---

## 📄 License & Citation

This project is licensed under the **MIT License** - see the LICENSE file for details.

### Citation
For Phase 1 simulation research and methodology, please cite the Master's thesis:

```bibtex
@mastersthesis{maiman2024hyperparameter,
  author    = {Maiman, Yotin},
  title     = {Comparison of Hyperparameter Tuning Techniques in Educational Classification Models by Monte-Carlo Simulation},
  school    = {Chulalongkorn University},
  address   = {Bangkok, Thailand},
  year      = {2024},
  type      = {Master of Education Thesis}
}
```
