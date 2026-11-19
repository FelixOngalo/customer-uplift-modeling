# Uplift Modeling in R: Targeting the Customers a Campaign Actually Persuades

Most campaigns target customers who are likely to buy. That wastes money on people who would have bought anyway and can even annoy others. **Uplift modeling** targets the customers whose behaviour the campaign *changes*. This project builds uplift models from scratch in R on a simulated randomised campaign with known individual effects, evaluates them with Qini curves, and turns them into a profit-maximising targeting policy.

## Question

A retailer ran a randomised campaign on 20,000 customers: half were contacted, half were not. Contact costs $0.60 and a conversion is worth $20. Does the campaign pay off on average, which customers should be contacted, and how much profit does a good targeting model add over the usual "propensity to buy" approach?

## Key findings

*(Fill in after running the scripts. Suggested: the average effect and whether it covers the contact cost; Qini coefficient and correlation with the true uplift for the response model versus the uplift models; the most profitable policy, its profit per customer, and the share of the oracle's profit it earns; the best campaign size; whether the bootstrap separates the uplift models from the response model and from each other.)*

## What is in it

- **Simulated randomised campaign** with known true uplift per customer: persuadables (new, lapsed), sleeping dogs (heavy loyal buyers who are put off by contact) and indifferent customers. Messy raw data: duplicates, impossible ages, missing spend, inconsistent region labels.
- **Experiment checks**: standardised mean differences between arms, average treatment effect with a confidence interval, and the break-even uplift.
- **Four targeting models**, all from base R: a response model, S-learner, T-learner and transformed-outcome regression.
- **Evaluation without ground truth**: Qini curves and Qini coefficients from the randomised data, plus decile calibration of predicted versus observed uplift.
- **Evaluation with ground truth** (simulation only): rank correlation with the true uplift, true uplift in the top and bottom 20%.
- **Targeting policies**: fixed-budget and break-even-threshold rules, profit per customer measured both from the truth and from the randomised data alone, compared with treat-all, treat-none and an oracle.
- **Campaign-size curve**: profit against the share of customers contacted.
- **Bootstrap** intervals for the Qini coefficients and their differences.
- **A Quarto report** with the method, charts and limitations.

## Tools

R, dplyr, tidyr, tibble, ggplot2, knitr, Quarto. Logistic regression via base R `glm.fit`; no modelling packages needed.

## Reproduce

```r
install.packages(c("dplyr", "tidyr", "tibble", "ggplot2", "knitr"))
source("R/00_simulate_data.R")
source("R/01_clean_explore.R")
source("R/02_fit_uplift_models.R")
source("R/03_targeting_policy.R")
quarto::quarto_render("reports/report.qmd")
```

Run from the project root (open `uplift-modeling.Rproj`). The seeds are fixed, so results are reproducible.

## Project structure

```
uplift-modeling/
├── R/
│   ├── uplift_functions.R        # simulator, cleaning, learners, Qini, policy value
│   ├── 00_simulate_data.R
│   ├── 01_clean_explore.R
│   ├── 02_fit_uplift_models.R
│   └── 03_targeting_policy.R
├── data/
│   ├── raw/                      # campaign_experiment.csv (generated)
│   └── clean/                    # .rds results (generated)
├── reports/report.qmd
├── uplift-modeling.Rproj
└── README.md
```

## Limitations

- Simulated data with a structure close to the fitted logistic models; real uplift is smaller and noisier.
- Assumes a randomised test. Observational data needs confounding adjustment first.
- Qini estimates on a few thousand customers have wide intervals.
- Fixed margin and cost; no fatigue, unsubscribe or long-run effects.
- Models are not tuned; regularised or tree-based learners may rank differently.
