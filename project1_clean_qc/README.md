# Project 1 – Akurba Outreach Data Cleaning and Quality Audit

This project uses anonymised data from a World Sight Day outreach in Akurba (Lafia, Nigeria) to demonstrate an end-to-end data cleaning and feature engineering workflow for clinical outreach data. The final output is an analysis-ready dataset with a clinically motivated `referral_needed` flag that can be used for epidemiological analysis, logistic regression modelling, and dashboarding.

---

## Objectives

- Turn a raw outreach CSV into a clean, well-documented dataset.
- Engineer clinically meaningful features from visual acuity (eye-specific VA, better-eye category).
- Define a reproducible, rule-based `referral_needed` flag using diagnosis and visual acuity.
- Summarise referral patterns by age group and diagnostic category.

---

## Workflow

The main workflow is implemented in **`01_data_clean_qc.Rmd`** and follows these steps:

1. **Import & standardise**
   - Import the raw anonymised CSV.
   - Standardise variable names for consistent use in R.

2. **Data quality checks**
   - Inspect dataset dimensions and basic structure.
   - Assess missingness, age ranges, and potential duplicates.
   - Sanity-check visual acuity and diagnostic fields.

3. **Variable recoding**
   - Recode sex into a clean `Male` / `Female` factor.
   - Create an `age_group` factor (Child, Adult, Middle-aged, Elderly).

4. **Visual acuity feature engineering**
   - Convert Snellen VA strings for each eye into an ordinal severity rank.
   - Derive `better_eye_rank` and a categorical `better_eye_cat`:
     - `Normal_mild`
     - `Moderate`
     - `Severe`
     - `Very_poor`

5. **Referral flag definition**
   - Define `referral_needed` based on:
     - High-risk diagnostic categories:
       - cataract-related  
       - glaucoma-related  
       - corneal disorders  
       - trauma-related  
       - others (serious conditions grouped in this bucket)
     - Visual acuity threshold:
       - better eye 6/18 or worse (moderate or worse impairment)  
     - Missing / unassessable VA:
       - default to referral.

6. **Summary tables and plots**
   - Referral rate by `age_group`.
   - Referral rate by `Diagnostic.Category`.
   - Distribution of `better_eye_cat`.

7. **Export**
   - Save a cleaned dataset with key engineered fields to:
     - `data_processed/akurba_with_referral.csv`

---

## Key Outputs

- **Code / notebook**
  - `01_data_clean_qc.Rmd` (or `.ipynb`): full cleaning, QC, and feature-engineering workflow.

- **Data**
  - `data_raw/akurba_cleaned_anonymised (1).csv` – input anonymised outreach data.
  - `data_processed/akurba_with_referral.csv` – cleaned dataset with:
    - `age_group`
    - visual acuity ranks and categories
    - diagnostic categories
    - `referral_needed` flag

- **Figures**
  - `figures/` – plots summarising:
    - referral patterns by age group and diagnosis
    - distribution of better eye visual acuity

---

## How to Run

1. Clone this repository:

   ```bash
   git clone https://github.com/<your-username>/ophthal-outreach-analytics.git
   cd ophthal-outreach-analytics/project1_clean_qc
