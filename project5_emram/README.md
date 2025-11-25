# Project 5 – EMRAM Maturity Audit (Stages 0 to 4)

This project applies a simplified EMRAM Stage 0 to 4 checklist to an anonymised hospital audit. The goal is to clean the raw checklist, score each stage, generate summary statistics and visuals, and produce a concise narrative report of the hospital’s EMR maturity.

The workflow is implemented in three R Markdown files:

- `cleaning.Rmd` – import and tidy the EMRAM audit tool data  
- `Scoring.Rmd` – compute numeric scores and summary tables  
- `Report.Rmd` – read the summaries and produce a human readable report  

All work is based on the `EMRAM_Stage_0_to_4_Audit_Tool.csv` file, which holds one row per EMRAM stage, with the key criterion, hospital status, and supporting evidence.

---

## Objectives

- Standardise the EMRAM Stage 0 to 4 audit data into a tidy format.  
- Apply a transparent scoring rule to Yes, No, and Partial responses.  
- Summarise maturity by stage and by domain.  
- Determine the highest EMRAM stage achieved, given the dependencies across stages.  
- Produce a narrative report and simple visuals for decision makers.

---

## Data

### Input

The core input is:

- `EMRAM_Stage_0_to_4_Audit_Tool.csv`

with columns:

- `EMRAM Stage` – textual stage label such as `Stage 0` to `Stage 4`  
- `Key Criteria` – description of the criterion for that stage  
- `Hospital Status (Yes/No)` – audit response, with possible values `Yes`, `No`, or `Partial`  
- `Notes / Evidence` – free text explanation or proof of status  

Example:

- Stage 1 – basic ancillary systems present.  
- Stage 2 – clinical data repository with controlled vocabulary present.  
- Stage 3 – nursing and clinical documentation implemented, but CDSS features absent.  
- Stage 4 – CPOE not yet implemented.

These records describe the hospital’s position relative to HIMSS EMRAM Stage 0 to 4 criteria.

### Derived datasets

The scripts generate:

- `data/emram_stage_summary.csv` – per stage counts of Yes, Partial, and No, with derived scores.  
- `data/emram_domain_summary.csv` – domain level summaries based on text tags in the key criteria.  
- `data/emram_final_stage.csv` – a one row file with the final EMRAM stage achieved (`final_stage`).

---

## Workflow

### 1. Cleaning (`cleaning.Rmd`)

Key steps:

1. Load the raw CSV.  
2. Clean column names with `janitor::clean_names()`.  
3. Harmonise stage and response columns into a consistent schema:

   - `stage` – numeric stage 0 to 4 extracted from `EMRAM Stage`  
   - `indicator` – the text from `Key Criteria`  
   - `response` – standardised to `Yes`, `No`, or `Partial`  
   - `evidence` – free text notes

4. Run basic checks on:

   - Stage coverage  
   - Distribution of responses  
   - Presence of missing values

The output of this step is a clean data frame (often named `emram_clean`) with one row per stage criterion and standardised fields.

### 2. Scoring and summaries (`Scoring.Rmd`)

This script:

1. Reads the cleaned EMRAM data.  
2. Defines a numeric score for each response, typically:

   - `Yes` = 1  
   - `Partial` = 0.5  
   - `No` = 0  

3. Computes per stage summaries:

   - `n_items` – number of criteria at that stage  
   - `n_yes`, `n_partial`, `n_no`  
   - `mean_score` – average score across items, between 0 and 1  

   Results are saved to `data/emram_stage_summary.csv`.

4. Tags each criterion with a simple domain label based on text patterns in `indicator`, for example:

   - Infrastructure and ancillary systems  
   - Clinical data repository  
   - Clinical documentation  
   - Decision support and order entry  

   Aggregated domain statistics are written to `data/emram_domain_summary.csv`.

5. Derives a final EMRAM stage:

   - Finds the highest stage where the hospital meets the criterion for that stage.  
   - Ensures that all lower stages are also satisfied, in line with the hierarchical nature of EMRAM.  

   The final stage is written to `data/emram_final_stage.csv` as a single value.

6. Produces basic plots, such as:

   - Bar chart of mean stage score vs stage number.  
   - Domain profile chart showing mean scores by domain.  
   - Indicator level heatmap of compliance.

### 3. Reporting (`Report.Rmd`)

This script:

1. Imports the three summary files:

   - `emram_stage_summary.csv`  
   - `emram_domain_summary.csv`  
   - `emram_final_stage.csv`  

2. Prints summary tables inside the report:

   - Stage level scores and response breakdown.  
   - Domain level maturity profile.

3. Generates visuals for:

   - Mean score by stage.  
   - Domain performance.  

4. Embeds a narrative interpretation of the current stage, highlighting:

   - The highest fully achieved stage.  
   - Stages where key gaps remain, such as missing CPOE or CDSS capabilities.  
   - Priorities for moving from the current stage to the next.

---

## Repository Structure

A suggested layout for this project:

```text
Project5_EMRAM_Audit/
├─ data/
│  ├─ EMRAM_Stage_0_to_4_Audit_Tool.csv
│  ├─ emram_stage_summary.csv
│  ├─ emram_domain_summary.csv
│  └─ emram_final_stage.csv
├─ Rmd/
│  ├─ cleaning.Rmd
│  ├─ Scoring.Rmd
│  └─ Report.Rmd
├─ figures/
│  ├─ stage_scores.png
│  ├─ domain_profile.png
│  └─ indicator_heatmap.png
└─ README.md
Adjust paths to match your actual setup.
```

---

## How to run

1. Open the project folder in RStudio or your preferred IDE.

2. Ensure the required packages are installed:

	- tidyverse

	- janitor

	- readr

	- stringr

	- ggplot2

	- forcats

	- scales

3. Knit the files in this order:

	1. cleaning.Rmd

	2. Scoring.Rmd

	3. Report.Rmd

4. The final EMRAM maturity report will be generated as an HTML file from Report.Rmd, and summary CSV files will be available in the data/ folder.

## Interpretation

The current audit data show:

- Criteria for Stage 1 and Stage 2 marked as Yes.

- Criteria for Stage 3 and Stage 4 marked as No, with notes indicating clinical documentation is present but CDSS and CPOE are not yet implemented.

Under a simple hierarchical rule, the hospital’s EMRAM stage is therefore Stage 2. That corresponds to a setting with a clinical data repository and coded data, but without advanced clinical documentation, CDSS, or CPOE fully in place.


This should be interpreted as an early to intermediate level of EMR maturity, with clear next steps defined by the Stage 3 and Stage 4 criteria.
