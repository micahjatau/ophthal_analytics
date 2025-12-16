# Ophthalmology Data & EMR Analytics Portfolio

This repository collects a series of reproducible R projects built from real clinical and service datasets in ophthalmology and digital health. Each project is organised as an R Markdown workflow that starts from messy, real world data and ends with interpretable tables, plots and narrative reports.

The work is structured into five projects:

1. **Project 1 – Akurba Outreach: WHO Visual Acuity Cleaning**  
   - Focus: Cleaning community outreach data, harmonising visual acuity formats and deriving WHO aligned visual acuity categories for the better eye.  
   - Output: `01_data_clean_qc_WHO_2.html` and narrative report `akurba_WHO_cleaning_IMRAD.md`.

2. **Project 2 – Akurba Outreach: Prevalence and Pattern of Eye Disease**  
   - Focus: Estimating the prevalence of visual impairment, describing diagnostic patterns, and fitting a simple logistic model with age and sex as predictors.  
   - Output: `02_prevalence_and_patterns.html`, summary CSVs in `data/`, and narrative report `akurba_prevalence_IMRAD.md`.

3. **Project 3 – Akurba Outreach: Referral Rule and Model**  
   - Focus: Implementing WHO based visual acuity thresholds and diagnostic categories to define referral need, then evaluating a simple rule based model against manual referral decisions.  
   - Output: Referral flags in the cleaned dataset, ROC curves, and model performance metrics.

4. **Project 4 – Trabeculectomy Outcomes: IOP and Visual Acuity Trajectories**  
   - Focus: Cleaning a trabeculectomy dataset, converting visual acuity to logMAR, modelling intraocular pressure trajectories over time, assessing visual acuity stability, and defining surgical success based on IOP and medication burden.  
   - Output: Cleaned dataset, descriptive tables, paired tests, linear mixed models for IOP, and KM style survival plots for surgical success.

5. **Project 5 – EMRAM Audit: Stages 0 to 4**  
   - Files:  
   - Focus: Cleaning an EMRAM Stage 0–4 audit checklist, mapping yes/partial/no responses to numeric scores, enforcing the absence of clinical decision support, calculating stage and domain level maturity, and generating summary plots.  
   - Output: `emram_clean.csv`, stage and domain summaries, and a narrative report in `09_emram_report.html`.

## Folder structure

A typical layout is:

```text
.
├── project1_clean_qc
├── 09_emram_report.Rmd
├── project3_referral_model
├── project4_dashboard
├── project5_emram
└── README.md
```
---

## How to run

1. Open the project in RStudio.

2. Ensure the required packages are installed (tidyverse, janitor, broom, lme4, survival, survminer, etc.).

3. Knit the Rmd files 

Each document assumes that the outputs from previous steps are available in the data/ directory.

## Intended audience

Clinicians and trainees interested in learning practical R for ophthalmology and digital health.

Data scientists who want to see realistic examples of messy clinical data cleaning, WHO category derivation, and EMR maturity scoring.

Recruiters or supervisors who want concrete evidence of end to end analytical workflows rather than isolated code snippets.

## Limitations

These projects use local datasets with modest sample sizes and were designed primarily for skills demonstration and service evaluation. They should not be interpreted as definitive epidemiological estimates for the wider population.