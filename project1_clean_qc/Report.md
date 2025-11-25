# Akurba Outreach Data Cleaning and WHO Visual Acuity Classification

## Abstract

**Background:** Community eye health outreaches generate rich clinical data but are often recorded on paper forms and later entered into spreadsheets with inconsistent structure. Before any analysis, these data must be cleaned, standardised and transformed into clinically meaningful variables.

**Objective:** To clean and quality check anonymised data from a World Sight Day outreach in Akurba, Lafia, Nigeria, and to derive WHO-aligned visual acuity (VA) categories and a rule-based referral flag suitable for downstream analysis and modelling.

**Methods:** We analysed 243 outreach records with seven variables, including patient ID, age, sex, eye-specific VA, and provisional diagnosis. We standardised variable names, recoded sex and age group, and engineered eye-specific VA ranks and a better-eye WHO visual impairment category using a deterministic `va_rank()` function. We then implemented a referral rule that flagged patients for referral if they belonged to high-risk diagnostic categories, had moderate visual impairment or worse in the better eye, or had unassessable VA. We summarised WHO VA categories and referral patterns by diagnostic category.

**Results:** Ages ranged from 0 to 87 years. Most participants were adults or middle-aged, with smaller proportions of children and elderly patients. Better eye WHO categories were: Normal 56.4%, Mild VI 10.7%, Moderate VI 4.9%, Severe VI 13.2%, Blindness 8.2%, and unclassified 6.6%. All patients with cataract-related, glaucoma-related, corneal, trauma-related or “Other” diagnostic categories met the referral rule by design. Among conjunctival or surface disorders and refractive disorders, referral rates were 18.3% and 14.1% respectively.

**Conclusion:** A simple, transparent cleaning pipeline can transform raw outreach data into a well-documented dataset with WHO-aligned VA categories and a reproducible referral flag. The derived features capture clinically meaningful variation in visual impairment and provide a robust foundation for subsequent epidemiological analyses and referral prediction models.

---

## Introduction

Community based eye health outreaches are widely used in low and middle income countries to improve access to basic eye care services. These activities often generate large volumes of patient level data but the information is typically collected on paper forms and later entered into spreadsheets with minimal structure. Without systematic cleaning and documentation, important patterns in visual impairment and referral needs remain difficult to analyse.

For World Sight Day, an outreach was conducted in Akurba, a peri urban community in Lafia, Nasarawa State, Nigeria. Patients underwent basic assessment including age, sex, visual acuity in each eye and a provisional diagnosis. To use these data for quality improvement and research, we first needed to standardise the dataset, recode key variables and derive clinically interpretable measures of visual impairment.

The World Health Organization defines categories of visual impairment based on presenting visual acuity in the better eye. Mapping raw Snellen visual acuity readings into WHO aligned categories allows direct comparison with other studies and facilitates reporting on the burden of visual impairment. In addition, a simple, rule based referral flag can help quantify how many outreach participants require further assessment or treatment at higher level facilities.

This report describes the data cleaning and feature engineering pipeline applied to the Akurba outreach dataset, focusing on the construction of WHO visual acuity categories and a reproducible referral rule.

## Methods

### Data source and structure

We used an anonymised dataset derived from paper registers of a World Sight Day outreach in Akurba. After initial formatting, the working dataset contained 243 rows, one per patient encounter, and seven columns:

- `Patient_ID` anonymised patient identifier  
- `Age` age in completed years  
- `Sex` recorded as “M” or “F” or full words  
- `VA.RE.` and `VA.LE.` Snellen presenting visual acuity for right and left eyes  
- `Provisional.Diagnosis` free text clinical impression  
- `Diagnostic.Category` grouped diagnosis label assigned at data entry  

Initial inspection showed mixed capitalisation, minor spelling variation in diagnoses and inconsistent visual acuity notation, as expected for manually curated outreach data.

### Data cleaning steps

We performed data cleaning in R. Variable names were standardised to lower snake case using `janitor::clean_names()`. We then created a cleaned dataset `data_clean` with clearer variable names:

- `va_re` and `va_le` for right and left eye VA  
- `diagnosis` for the original provisional diagnosis  
- `diagnosis_cat` for the diagnostic category  

Sex values were harmonised to “Male” and “Female” via a `case_when()` recode. Age was converted into an ordered categorical variable `age_group` with four levels:

- `Child` Age < 18 years  
- `Adult` 18 ≤ Age < 45 years  
- `Middle_aged` 45 ≤ Age < 65 years  
- `Elderly` Age ≥ 65 years  

These cut points were chosen to roughly align with life stage and the expected rise in cataract and other age related disease beyond the mid forties and into older age.

Basic quality checks included dimension counts, summaries of each variable and visualisation of missingness using `skimr::skim()` and `naniar::vis_miss()`. Age ranged from 0 to 87 years with plausible values and no evidence of systematic entry errors.

### Visual acuity ranking and WHO category derivation

To translate raw Snellen visual acuity strings into an ordered numeric scale, we implemented a deterministic helper function `va_rank()`. This function:

1. Converts each VA entry to upper case, trims whitespace and treats empty strings, “NA” and “NaN” as missing.  
2. Maps specific Snellen levels to integer ranks, with lower ranks indicating better vision:  

   - 6/6 -> 1  
   - 6/9 -> 2  
   - 6/12 -> 3  
   - 6/18 -> 4  
   - 6/24 -> 5  
   - 6/36 -> 6  
   - 6/60 -> 7  
   - 3/60 -> 8  
   - 1/60 and counting fingers variants -> 9  
   - hand movements and perception of light -> 10  

3. Returns `NA` for any VA strings that do not match expected patterns.

We applied `va_rank()` separately to `va_re` and `va_le` to obtain `va_re_rank` and `va_le_rank`. The better eye rank was defined as

```r
better_eye_rank = pmin(va_re_rank, va_le_rank, na.rm = TRUE)
better_eye_rank = ifelse(is.infinite(better_eye_rank), NA, better_eye_rank)
```

Using `better_eye_rank`, we then derived WHO aligned visual acuity categories in the better eye (`who_va_cat`) with the following mapping:

- Ranks 1 to 2 (6/6 to 6/9) -> “Normal”  
- Rank 3 (6/12) -> “Mild VI”  
- Rank 4 (6/18) -> “Moderate VI”  
- Ranks 5 to 6 (6/24 to 6/36) -> “Severe VI”  
- Ranks ≥ 7 (6/60 and worse, including 3/60, 1/60, CF, HM, PL) -> “Blindness”  

The resulting `who_va_cat` was stored as an ordered factor with levels Normal, Mild VI, Moderate VI, Severe VI and Blindness.

This mapping follows the spirit of WHO visual impairment categories but uses a pragmatic grouping that treats 6/60 and poorer as “Blindness”. This should be explicitly stated in any manuscript, as some WHO documents reserve blindness for VA < 3/60 in the better eye.

### Diagnostic categories and high risk conditions

The `diagnosis_cat` variable grouped individual diagnoses into higher level categories, including for example:

- Refractive disorders  
- Conjunctival or surface disorders  
- Cataract related  
- Glaucoma related  
- Corneal disorders  
- Trauma related  
- Others  

For the purpose of defining referrals, we identified a set of “high risk” diagnostic categories that almost always warrant further assessment at a higher level facility:

```r
high_risk_cats <- c(
  "Cataract-related",
  "Glaucoma-related",
  "Corneal disorders",
  "Trauma-related",
  "Others"
)
```

### Rule based referral flag

We constructed a binary referral flag `referral_needed` using a rule based approach applied to `diagnosis_cat` and `who_va_cat`. A patient was marked as needing referral (`referral_needed = 1`) if any of the following were true:

1. Diagnostic category belonged to `high_risk_cats`.  
2. WHO better eye category was Moderate VI, Severe VI or Blindness.  
3. WHO better eye category was missing because VA was unassessable or unparseable.  

All other patients were assigned `referral_needed = 0`.

This rule prioritises sensitivity to serious disease and visual impairment, ensuring that patients at highest risk are systematically identified for referral.

### Descriptive summaries and visualisations

We summarised the distribution of WHO better eye categories using counts, proportions and bar plots. We also examined referral rates across diagnostic categories by calculating, for each `diagnosis_cat`, the total number of patients, number of referrals and the proportion referred. These results were presented both as tables and as bar charts with referral rate on the y axis.

## Results

### Sample characteristics

The cleaned dataset contained 243 patients, each with age, sex, eye specific VA and a diagnostic category. Age ranged from 0 to 87 years. The age distribution was skewed toward adults and middle aged individuals, with fewer children and elderly patients. Sex distribution showed a slight predominance of female attendees that is consistent with many eye outreach programmes, although exact proportions were not the focus of this report.

### WHO better eye visual acuity categories

The mapping from raw Snellen acuities to WHO style better eye categories produced the following distribution:

- Normal 137 patients, 56.4 percent  
- Mild VI 26 patients, 10.7 percent  
- Moderate VI 12 patients, 4.9 percent  
- Severe VI 32 patients, 13.2 percent  
- Blindness 20 patients, 8.2 percent  
- Unclassified (missing or unparseable VA) 16 patients, 6.6 percent  

These figures show that just under half of participants had some degree of visual impairment in the better eye, with a notable minority classified as severely visually impaired or blind by the chosen criteria.

### Referral patterns by diagnostic category

By design, every patient in the high risk diagnostic categories met the referral rule. Cataract related, glaucoma related, corneal disorders, trauma related and Others all had referral rates of 100 percent. This reflects the clinical judgement that these conditions typically require further investigation, surgery or long term follow up beyond what can be offered at a single outreach visit.

Among more benign categories, the referral rate was lower but still clinically meaningful:

- Conjunctival or surface disorders 18.3 percent referred  
- Refractive disorders 14.1 percent referred  

These referrals arose from patients who also met the visual acuity criteria for referral, that is Moderate VI or worse in the better eye, or for whom visual acuity could not be reliably assessed.

### Referral patterns by age group

Although not the main focus of this report, stratifying the derived `referral_needed` flag by age group showed a clear gradient. Referral rates increased from children through to elderly participants. The highest referral proportions were seen among elderly patients, consistent with the expected burden of cataract and other age related ocular disease in older age groups.

## Discussion

### Principal findings

We developed and applied a transparent data cleaning and feature engineering pipeline to anonymised outreach data from Akurba. Using a deterministic VA ranking function and clearly defined rules, we mapped raw Snellen acuities into WHO style better eye visual impairment categories and constructed a referral flag that reflects both diagnostic category and degree of visual impairment.

Approximately 44 percent of participants had at least mild visual impairment in the better eye, with a notable fraction falling into severe visual impairment or blindness. The referral rule, by design, captured all patients with high risk diagnostic categories and additionally identified impaired individuals within more benign categories such as refractive and conjunctival disorders.

### Strengths of the approach

The pipeline has several strengths.

- Reproducibility. All recoding decisions, including age groups, VA mapping, diagnostic categories and the referral rule, are implemented as explicit code, not hidden formulas.  
- Clinical interpretability. The VA mapping and referral rule are easy to explain to clinicians and programme managers and align broadly with WHO conceptual categories of visual impairment.  
- Better eye focus. Using the better eye to classify visual impairment matches standard epidemiological practice and avoids overestimating impairment based on the worse eye alone.  
- Rule based referral flag. The explicit logic behind `referral_needed` allows the team to audit and, if necessary, refine their referral criteria over time.

### Limitations and considerations

There are also clear limitations.

- WHO category alignment. The current mapping treats 6/60 and poorer as “Blindness”, whereas some WHO definitions reserve blindness for VA < 3/60 in the better eye. This is acceptable if clearly stated but any manuscript should explicitly note the chosen thresholds.  
- High risk “Others” category. Including “Others” in the high risk set guarantees 100 percent referral in this group but may mix serious and relatively benign conditions. A future iteration should consider splitting “Others” into more granular categories.  
- Missing VA handling. The rule to refer patients with unassessable VA prioritises safety but may increase referrals for logistical rather than clinical reasons. Programmes must ensure that referral centres can absorb this load.  
- Single site outreach. The findings are based on one outreach in a specific community and may not generalise to other settings without adjustment for differing case mix and service organisation.

### Implications and next steps

The cleaned dataset and derived variables now form a foundation for further analyses, including

- estimation of age and sex specific prevalence of visual impairment in the outreach population  
- logistic regression models to predict `referral_needed` from minimal screening data  
- evaluation of outreach effectiveness and planning of future community interventions  

Future work should validate the VA mapping and referral rule against clinician decisions and outcomes at the referral centre and may explore more nuanced thresholds or additional predictors, such as presenting complaints or intraocular pressure, where available.

## Conclusion

A structured, code driven cleaning pipeline can convert raw outreach registers into a robust analytic dataset with WHO style visual acuity categories and a rule based referral indicator. For the Akurba World Sight Day outreach, nearly half of participants had some degree of visual impairment in the better eye and a substantial proportion met explicit criteria for referral. This approach enhances transparency, supports audit and quality improvement and prepares the ground for more advanced epidemiological and predictive modelling work.
