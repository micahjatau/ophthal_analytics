# Project 4: Trabeculectomy Outcomes – IOP, Visual Acuity and Medication Burden

This project analyses outcomes of trabeculectomy in glaucoma patients using routinely collected clinical data. The focus is on intraocular pressure (IOP) reduction over time, visual acuity (VA) stability and the role of postoperative medication in defining surgical success.

The project is written in R and structured as a small, reproducible analysis pipeline.

---

## 1. Objectives

1. Clean and anonymise a trabeculectomy dataset into an analysis ready format.
2. Model IOP changes from baseline to key follow up timepoints:
   - Pre operative
   - Day 1 post operative
   - Six months
   - One year
   - Two years (where available)
3. Evaluate visual acuity stability in logMAR at six months and one year.
4. Define and estimate surgical success using IOP based criteria and medication status:
   - IOP success
   - Complete success
   - Qualified success
5. Produce summary tables, plots and basic inferential statistics that could form the core of a short audit or manuscript.

---

## 2. Data

Source: Trabeculectomy patients from a single centre glaucoma service.

Key features:

- Each row represents an eye that underwent trabeculectomy.
- Variables include:
  - Demographics: age, sex
  - Clinical data:
    - IOP at pre operative visit and follow up timepoints
    - VA at pre operative visit and follow up timepoints
    - Date fields related to surgery and follow up
    - Pre operative and current glaucoma medications (free text)
  - Free text notes and identifiers, which are removed or anonymised in the analysis.

After cleaning, the core analysis dataset has:

- 33 eyes
- 13 variables

---

## 3. File structure

Within the project folder, the main files for this analysis are:

- `data/trab_clean.csv`  
  Anonymised, cleaned trabeculectomy dataset used by the analysis scripts.

- `04_trab_cleaning.Rmd`  
  Data preparation:
  - Loads the raw CSV
  - Anonymises identifiers
  - Cleans dates, IOP and VA fields
  - Creates the cleaned dataset

- `05_trab_outcomes.Rmd`  
  Main outcomes analysis:
  - Derives IOP and VA outcome variables
  - Defines success metrics
  - Summarises and visualises IOP, VA and medication burden
  - Performs basic inferential testing

- `04_trab_cleaning.html` and `05_trab_outcomes.html`  
  Rendered HTML reports corresponding to the R Markdown files.

You can knit each Rmd to reproduce the outputs.

---

## 4. Data cleaning and transformations

### 4.1 Anonymisation

In `04_trab_cleaning.Rmd`:

- Names and hospital numbers are dropped.
- A new identifier `Patient_ID` is created in the format `PX001`, `PX002` and so on.
- The dataset is saved as `data/trab_clean.csv` for use in downstream scripts.

### 4.2 Date handling

Date columns such as surgery date and visit dates are:

- Parsed using `lubridate::dmy` or similar functions.
- Standardised into proper `Date` objects.
- Used for follow up interval checks if needed.

### 4.3 IOP cleaning

Several IOP columns are present:

- `iop_preop`
- `iop_1dpo`  (day 1 post operative)
- `iop_6mo`
- `iop_1yr`
- `iop_2yr`

Cleaning steps:

- A helper function converts free text to numeric:
  - Values such as `"na"`, `"n/a"`, `"nil"`, blanks and similar are set to `NA`.
  - Valid numeric strings are converted with `as.numeric`.
- The same logic is applied to all IOP timepoints so that comparisons are consistent.

### 4.4 Visual acuity conversion to logMAR

VA is recorded in a mixed format, including:

- Snellen style stored as dot notation, for example `"6.6"`, `"6.60"` which represent `"6/6"` and `"6/60"`.
- Qualitative categories such as `"PL"`, `"HM"`, `"CF"`, `"NPL"`.

A custom function `va_to_logmar()`:

1. Normalises input to lower case and trims white space.
2. Maps qualitative values to approximate logMAR values:
   - PL, HM, CF, NPL, NLP and similar categories.
3. Converts dot format to Snellen:
   - `"6.60"` becomes `"6/60"`.
4. Extracts numerator and denominator and converts Snellen to logMAR using:
   - `log10(denom / 6)` for typical `6/x` entries.
5. Returns `NA` for unrecognised or obviously invalid values.

Derived columns:

- `va_preop_logmar`
- `va_6mo_logmar`
- `va_1yr_logmar`
- Other follow up timepoints as available.

### 4.5 Visual acuity change categories

VA change at six months and one year is calculated as:

- `va_change_6mo  = va_6mo_logmar - va_preop_logmar`
- `va_change_1yr  = va_1yr_logmar - va_preop_logmar`

Change is categorised using a threshold of `±0.10` logMAR:

- Improved: change ≤ −0.10
- Stable: absolute change < 0.10
- Worsened: change ≥ 0.10

These are stored as:

- `va_status_6mo`
- `va_status_1yr`

---

## 5. IOP outcomes and success definitions

### 5.1 IOP drop and percentage reduction

For each eye:

```r
iop_drop_6mo  = iop_preop - iop_6mo
iop_drop_1yr  = iop_preop - iop_1yr
iop_drop_2yr  = iop_preop - iop_2yr

iop_pct_drop_6mo = iop_drop_6mo / iop_preop
iop_pct_drop_1yr = iop_drop_1yr / iop_preop
````

This keeps the baseline as the reference and allows both absolute and relative changes to be summarised.

### 5.2 IOP based success

A strict IOP success definition is used:

* IOP at follow up timepoint is less than or equal to 21 mmHg
* and at least 30 per cent reduction from baseline

Binary indicators:

```r
success_6mo_30pct = if_else(
  !is.na(iop_6mo) &
  iop_6mo <= 21 &
  iop_pct_drop_6mo >= 0.30, 1L, 0L
)

success_1yr_30pct = if_else(
  !is.na(iop_1yr) &
  iop_1yr <= 21 &
  iop_pct_drop_1yr >= 0.30, 1L, 0L
)
```

These variables are used to estimate:

* Proportion of eyes that meet IOP success criteria at six months
* Proportion of eyes that still meet criteria at one year

---

## 6. Medication burden and success type

The dataset contains two main medication columns:

* `Pre-Op medication`
  Free text describing glaucoma medications before surgery.
  `"n/a"` indicates unknown or not documented.

* `Current medication`
  Free text describing medications at last follow up.
  `"nil"` indicates that the patient is not currently on glaucoma medication.

Two helper functions are used:

* `med_preop_any(x)`
  For pre operative medication:

  * Treats `"na"`, `"n/a"`, blanks and similar as `NA` (unknown)
  * Text patterns such as `"no med"`, `"no medication"`, `"none"` are treated as zero medication
  * All other non missing values are coded as being on at least one medication

* `med_current_any(x)`
  For current medication:

  * Treats `"na"`, `"n/a"`, blanks as `NA`
  * `"nil"`, `"no medication"`, `"none"` and similar explicit statements of no treatment are coded as zero medication
  * Remaining non missing entries are coded as on medication

Derived variables:

* `meds_preop_any`
  Indicator of any pre operative medication (1) versus none (0) or unknown (NA)

* `meds_current_any`
  Indicator of current medication status at last follow up

* `med_free_last`
  Indicator equal to 1 if the eye is documented as not on medication at last follow up

### 6.1 Complete and qualified success

To link IOP outcomes with medication burden, success at one year is divided into:

* Complete success

  * Meets the IOP success definition at one year
  * and is off glaucoma medication at last follow up

* Qualified success

  * Meets the IOP success definition at one year
  * and remains on glaucoma medication at last follow up

These are encoded as:

```r
complete_success_last  = if_else(
  success_1yr_30pct == 1L & med_free_last == 1L, 1L, 0L
)

qualified_success_last = if_else(
  success_1yr_30pct == 1L & med_free_last == 0L, 1L, 0L
)
```

A summary table reports:

* Total number of eyes
* How many have pre operative and current medication documented
* Number med free at last follow up
* Number and proportion of complete and qualified successes

---

## 7. Statistical analysis

All analysis is implemented in `05_trab_outcomes.Rmd` using `dplyr`, `tidyr`, `ggplot2` and base R testing functions.

### 7.1 Descriptive IOP analysis

* IOP values are reshaped into long format with timepoint labels.
* Summary statistics for each timepoint:

  * Number of eyes (`n`)
  * Mean, median and standard deviation
  * Standard error and approximate 95 per cent confidence interval

Typical pattern:

* Pre operative mean IOP around 24 to 25 mmHg
* Immediate drop to about 11 mmHg on day 1
* Mean IOP around 14 mmHg at six months
* Mean IOP around 19 mmHg at one year among the smaller subset with data

These are visualised using:

* A line plot of mean IOP versus timepoint with error bars

### 7.2 IOP success proportions

* `success_6mo_30pct` and `success_1yr_30pct` are tabulated.
* Proportions are reported as:

  * Proportion of eyes with six month data that meet the success definition
  * Proportion of the entire cohort that meet the one year success definition

A bar plot shows the percentage of eyes meeting the IOP success criteria at the two key timepoints.

### 7.3 Visual acuity stability

* The `va_status_6mo` variable summarises six month VA outcomes as Improved, Stable or Worsened.

* A bar plot shows the distribution of VA status at six months.

* Visual acuity trajectories are also examined by reshaping logMAR values into long format and computing:

  * Mean logMAR
  * Standard deviation and confidence intervals across timepoints

A separate line plot displays mean logMAR over time, typically showing relative stability.

### 7.4 Medication and success

* Summary counts and proportions:

  * Number of eyes med free at last follow up
  * Number of eyes that achieve complete success
  * Number that achieve qualified success

* A simple bar plot compares complete and qualified success at last follow up.

---

## 8. Inferential tests

Basic paired tests are used to confirm IOP and VA changes.

### 8.1 IOP: baseline vs six months

Among eyes with both pre operative and six month IOP:

* Mean difference between six month and pre operative IOP is approximately −10 mmHg
* Paired t test shows a statistically significant reduction in IOP
* Wilcoxon signed rank test confirms this without assuming normality

### 8.2 Visual acuity: baseline vs six months

Among eyes with paired logMAR values:

* Mean change in logMAR is small and negative, suggesting mild improvement on average
* Confidence intervals for the mean change include zero
* Both paired t test and Wilcoxon signed rank test are not statistically significant at conventional levels

This supports the descriptive conclusion that VA is largely stable with a mix of improved, unchanged and worsened eyes.

---

## 9. Key findings

In this small trabeculectomy cohort:

* IOP falls sharply after surgery and remains lower than baseline at six months and one year among eyes with available follow up.
* A substantial proportion of eyes meet a strict IOP based success definition at six months.
* Only a small fraction of the total cohort meets this strict definition at one year, reflecting limited follow up and late failures in some cases.
* Visual acuity is generally stable. Some eyes improve, some worsen, but mean logMAR change is small and not statistically significant.
* A majority of eyes with documented current medication status are off glaucoma medication at last follow up. However, only a few eyes satisfy both strict IOP criteria and favourable medication status, resulting in a small number of complete and qualified successes as defined in this analysis.

---

## 10. How to run the analysis

1. Open the project in R or RStudio with working directory set to the project root.
2. Ensure required packages are installed:

   * `tidyverse`
   * `lubridate`
   * `scales`
3. Knit the R Markdown files in sequence:

   * `04_trab_cleaning.Rmd`
   * `05_trab_outcomes.Rmd`
4. The outputs will be:

   * Updated `data/trab_clean.csv`
   * HTML reports with tables and figures summarising the analysis.

---

## 11. Limitations

* Small sample size and incomplete follow up, particularly at one year and beyond.
* Medication data are free text and require assumptions to classify as on or off treatment.
* Complications, reoperations and visual field outcomes are not fully captured in structured form and therefore are not included in the success definitions.
* The IOP success definition used here is strict and may differ from other published criteria. It is applied consistently and described explicitly in this report.

These limitations are important when interpreting the results but do not prevent the dataset from serving as a useful demonstration of clinical data cleaning, outcome definition and analysis in R.

```