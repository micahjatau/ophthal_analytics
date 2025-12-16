# Project 2 – Prevalence and Pattern of Eye Diseases in Akurba Outreach  

_A component of the FUTH Lafia Ophthalmology Data Analysis Series_ :contentReference[oaicite:0]{index=0}  

This project analyses data from the Akurba Community Eye Outreach, focusing on the **prevalence of visual impairment** and the **distribution of major eye disease categories** among attendees.  
It builds directly on Project 1 (data cleaning) and prepares the ground for subsequent modelling and referral pattern studies.

---

## Overview

The goals of this project were to:

- Estimate the prevalence of visual impairment using **WHO aligned categories** (better eye VA).  
- Describe the distribution of diagnoses  
  - refractive error  
  - cataract  
  - glaucoma  
  - corneal disease  
  - other ocular conditions.  
- Assess variations by **age** and **sex**.  
- Demonstrate basic statistical modelling using **logistic regression**.

All analyses were performed in **R (tidyverse)** using a fully anonymised dataset.

---

## Key Outputs

### Cleaned and categorised dataset

- WHO VA categories  
- Diagnostic groups  
- Age bands  
- Visual impairment flags  

### Summary statistics

- Overall visual impairment prevalence  
- Age and sex stratified estimates  
- Diagnostic category distributions  

### Visualisations

- Prevalence bar charts  
- Diagnosis distribution plots  

### Regression model (illustrative)

- Explores how age and sex predict visual impairment  
- Model is **exploratory only** because of small sample size and quasi separation  

---

## How to Navigate This Project

For readers who only want results, see:

- **`Report.md`** – full narrative report including introduction, methods, results and discussion.

For users who want the code, look at:

- **`02_prevalence_and_patterns.Rmd`** – main analysis script  
- **`data/`** – cleaned datasets and summary outputs  

---

## Summary of Findings

- Visual impairment was **high** among attendees, particularly in older adults.  
- **Refractive error** and **cataract** accounted for the majority of diagnoses.  
- Patterns mirrored established regional data in Nigeria and similar low and middle income settings.  
- Logistic regression showed **age** as the strongest predictor of impairment, with minimal effect from sex.  

Full methodological details, statistical outputs and interpretation are available in the full report.
