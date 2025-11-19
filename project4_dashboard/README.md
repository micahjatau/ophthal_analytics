# Project 4: Trabeculectomy Outcomes – IOP, Visual Acuity and Medication Burden

This project is a retrospective audit of trabeculectomy outcomes in glaucoma patients at a tertiary eye care centre. It focuses on three things:

1. How intraocular pressure (IOP) changes after surgery at key follow up timepoints.
2. Whether visual acuity (VA) is broadly preserved.
3. How postoperative medications affect definitions of surgical success.

All analysis is done in R and organised as a small, reproducible pipeline suitable for a clinical data science portfolio.

---

## 1. Objectives

1. Clean and anonymise a trabeculectomy dataset into an analysis ready format.
2. Convert mixed format visual acuity entries to logMAR and standardise IOP fields.
3. Model IOP trajectory from baseline to:
   - Pre operative
   - Day 1 post operative
   - Six months
   - One year
4. Evaluate visual acuity stability at six months and one year.
5. Define and estimate surgical success using combined IOP and medication criteria:
   - IOP success (IOP ≤ 21 mmHg and at least 30% reduction from baseline)
   - Complete success (IOP success and off medication)
   - Qualified success (IOP success but still on medication)
6. Produce tables, plots and inferential tests that could serve as the core of a short audit or manuscript.

A more narrative clinical write up is provided in `REPORT.md`.

---

## 2. Data

- Source: Trabeculectomy cases from a single centre glaucoma service.
- Unit of analysis: each row represents one operated eye.
- Key variables:
  - Demographics: age, sex, operated eye.
  - Clinical:
    - IOP at pre operative visit and follow up timepoints (day 1, six months, one year; sparse two year data).
    - VA at corresponding timepoints, recorded in mixed formats.
    - Date fields: surgery date and visit dates.
    - Free text medication fields for pre operative and current glaucoma drops.

Identifiers such as patient names and hospital numbers are removed. A new anonymised `Patient_ID` is created.

After cleaning, the main analysis dataset has:

- 33 eyes
- Core variables for IOP, VA, surgery type and medication status

---

## 3. Repository structure

Suggested layout:

- `data/`
  - `trab_raw.csv` (not tracked if it contains identifiers)
  - `trab_clean.csv` – cleaned, anonymised dataset used for analysis
  - optional derivatives such as `trab_clean_km.csv` for survival analysis

- R Markdown:
  - `04_trab_cleaning.Rmd`
    - Loads the raw dataset
    - Drops identifiers
    - Cleans dates
    - Normalises IOP and VA
    - Creates derived variables (logMAR VA, IOP drops, success flags)
    - Saves `data/trab_clean.csv`
  - `05_trab_outcomes.Rmd`
    - Summarises IOP and VA over time
    - Defines and tabulates success metrics
    - Describes medication burden and complete vs qualified success
    - Runs paired tests and linear mixed models
  - `06_trab_survival.Rmd`
    - Builds a time to event dataset
    - Fits Kaplan–Meier survival curves for surgical success

- Rendered reports:
  - `04_trab_cleaning.html`
  - `05_trab_outcomes.html`
  - `06_trab_survival.html`

- Documentation:
  - `README.md` – this file
  - `REPORT.md` – full clinical style write up (Introduction, Methods, Results, Discussion, Conclusion)

---

## 4. Key data transformations

### 4.1 Anonymisation

- Names and hospital numbers are dropped.
- A new `Patient_ID` is generated in the format `PX001`, `PX002`, etc.
- Only derived, non identifiable variables are carried into `trab_clean.csv`.

### 4.2 Visual acuity to logMAR

VA is stored in mixed forms such as:

- Dot based Snellen: `"6.6"`, `"6.60"` meaning `6/6`, `6/60`.
- Qualitative entries: `"PL"`, `"HM"`, `"CF"`, `"NPL"`, `nil`.

A helper function `va_to_logmar()`:

1. Cleans and normalises strings.
2. Maps qualitative categories to approximate logMAR values.
3. Converts dot notation to `6/x` format.
4. Computes `log10(denominator / 6)` for typical `6/x` entries.
5. Returns `NA` for unrecognised values.

Derived variables include:

- `va_preop_logmar`
- `va_6mo_logmar`
- `va_1yr_logmar`

VA change is calculated as:

- `va_change_6mo = va_6mo_logmar - va_preop_logmar`
- `va_change_1yr = va_1yr_logmar - va_preop_logmar`

Change categories:

- Improved: change ≤ −0.10
- Stable: absolute change < 0.10
- Worsened: change ≥ 0.10

Stored as `va_status_6mo` and `va_status_1yr`.

### 4.3 IOP cleaning and success flags

- Free text and missing IOP entries are cleaned using a helper that:
  - Converts `"na"`, `"n/a"`, blanks, `"nil"` to `NA`.
  - Coerces the remaining values to numeric.

Absolute and percentage drops:

```r
iop_drop_6mo  = iop_preop - iop_6mo
iop_drop_1yr  = iop_preop - iop_1yr

iop_pct_drop_6mo = iop_drop_6mo / iop_preop
iop_pct_drop_1yr = iop_drop_1yr / iop_preop
IOP based success:

r
Copy code
success_6mo_30pct = if_else(
  !is.na(iop_6mo) & iop_6mo <= 21 & iop_pct_drop_6mo >= 0.30,
  1L, 0L
)

success_1yr_30pct = if_else(
  !is.na(iop_1yr) & iop_1yr <= 21 & iop_pct_drop_1yr >= 0.30,
  1L, 0L
)

### 4.4 Medication coding and success type

Medication fields are free text:

Pre-Op medication:

"n/a" means unknown or not recorded.

Current medication:

"nil" means no current glaucoma drops.

Two helper functions reduce these to:

meds_preop_any – 1 if on any pre operative medication, 0 if known to be on none, NA if unknown.

meds_current_any – 1 if on medication at last follow up, 0 if explicitly off, NA if unclear.

An indicator med_free_last is 1 where the patient is recorded as off treatment at last follow up.

Complete and qualified success:

r
Copy code
complete_success_last  = if_else(
  success_1yr_30pct == 1L & med_free_last == 1L, 1L, 0L
)

qualified_success_last = if_else(
  success_1yr_30pct == 1L & med_free_last == 0L, 1L, 0L
)

---

## 5. Analysis outline and headline findings

Details are in REPORT.md. In brief:

Sample: 33 operated eyes. Mean age about 45 years. Most underwent trabeculectomy alone, some had combined trabeculectomy with cataract extraction.

IOP trajectory:

Mean pre operative IOP about 24.6 mmHg.

Around 10.8 mmHg at day 1, 13.8 mmHg at six months, and 18.8 mmHg at one year among those with data.

Paired tests show a statistically significant drop in IOP from baseline to six months.

Linear mixed models confirm that timepoint is a strong predictor of IOP, while age and surgery type do not show clear additional effects in this small sample.

Visual acuity:

LogMAR VA is broadly stable at six months.

Some eyes improve, some worsen, but mean change is small and not statistically significant.

Medication burden and success:

A majority of eyes with documented current treatment are off glaucoma medication at last follow up.

Under a strict combined definition (IOP threshold, percentage drop and medication status), only a small number meet complete or qualified success, partly reflecting small sample size and missing data.

These results are intended to demonstrate end to end handling of messy clinical data, longitudinal outcome definition, and basic modelling in R, rather than to provide definitive clinical benchmarks.

---

## 6. How to reproduce

Set the working directory to the project root in R or RStudio.

Install required packages:

r
Copy code
install.packages(c("tidyverse", "lubridate", "scales", "lme4", "lmerTest", "survival", "survminer"))
Knit the R Markdown files in sequence:

04_trab_cleaning.Rmd

05_trab_outcomes.Rmd

06_trab_survival.Rmd (optional)

Inspect the HTML outputs and data/trab_clean.csv.

---

## 7. Limitations

Small sample size and incomplete follow up, especially at one year.

Pre operative medication data are partly unknown and coded from free text.

Visual field progression, optic nerve imaging and complications are not available in structured form.

The success definitions are strict and may differ from other studies, though they are explicitly documented here.

See REPORT.md for a more detailed narrative of the findings and their implications.