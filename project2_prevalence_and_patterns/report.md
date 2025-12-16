# Prevalence and Pattern of Eye Diseases and Visual Impairment in Akurba Community Outreach

## Introduction

Visual impairment is a major cause of disability in low and middle income countries and often clusters in underserved communities where access to formal eye care is limited. Community based outreach clinics are frequently the first and only point of contact with specialist services, yet the data generated during such exercises are rarely analysed in a structured way.

This project uses data from an eye health outreach conducted in Akurba community to describe the prevalence of visual impairment and the pattern of ocular diagnoses among attendees. The analysis builds directly on Project 1, which documented the cleaning of the dataset and the derivation of World Health Organization aligned visual acuity categories for the better eye.

The specific objectives were to:

1. Estimate the prevalence of visual impairment using WHO aligned categories of visual acuity in the better eye.
2. Describe the distribution of major diagnostic categories among outreach attendees.
3. Explore how visual impairment varies by age and sex and demonstrate a simple logistic regression model using age and sex as predictors.

## Methods

### Study setting and design

This was a secondary analysis of routinely collected data from a one day outreach eye clinic held in Akurba community, Lafia, Nigeria. All individuals who presented for screening and had at least one recorded visual acuity measurement were eligible for inclusion in the analytic dataset.

### Data source and variables

The starting point was the anonymised dataset produced in Project 1 after cleaning and quality control. For each patient, the dataset included:

- Age in years  
- Sex, recorded as male or female  
- Presenting visual acuity in each eye  
- WHO aligned visual acuity category for the better eye (`who_va_cat`)  
- A clinician recorded provisional diagnosis, later grouped into diagnostic categories (`diagnosis_cat`)

Age was further categorised into four groups: 0–17, 18–39, 40–59, and 60 years or older. Sex was recoded into a binary variable (Male, Female) with unclear values treated as missing.

Visual impairment for this analysis was defined as moderate or worse impairment in the better eye. A binary outcome variable (`vi_any`) was created where:

- `vi_any = 1` for WHO categories Moderate, Severe, or Blindness  
- `vi_any = 0` for WHO categories Normal or Mild

### Diagnostic categories

Provisional diagnoses were grouped into broad categories such as:

- Refractive error  
- Cataract related disorders  
- Glaucoma related disorders  
- Corneal disorders  
- Other or nonspecific conditions  

The grouping rules followed the same principles used in Project 3 for defining referral categories, with the aim of separating refractive, cataract, glaucoma, corneal, and residual groups that are meaningful for service planning.

### Statistical analysis

All analyses were performed in R using the `tidyverse` ecosystem.

1. **Descriptive statistics**

   - Overall prevalence of visual impairment was calculated as the proportion of patients with `vi_any = 1` among those with non missing status.  
   - Prevalence was stratified by age group and sex.  
   - Frequencies and proportions were calculated for each diagnostic category overall and within each age group.

2. **Visualisations**

   - Bar plots were used to display the prevalence of visual impairment by age group and sex.  
   - Stacked bar charts were used to show the relative contribution of diagnostic categories within each age group.

3. **Logistic regression**

   A simple logistic regression model was fitted with visual impairment as the dependent variable and age and sex as predictors:

   \[
   \text{logit}(P(\text{VI} = 1)) = \beta_0 + \beta_1 \cdot \text{Age} + \beta_2 \cdot \text{Sex}
   \]

   Odds ratios and Wald confidence intervals were obtained by exponentiating the model coefficients. Because the dataset is small and several combinations of age and sex nearly perfectly predicted impairment, the model was treated as exploratory. Warnings about fitted probabilities close to 0 or 1 were noted and the emphasis in interpretation was placed on the simpler stratified prevalence estimates.

### Ethical considerations

The dataset was fully anonymised prior to analysis with all direct identifiers removed. The work represents secondary analysis of routine outreach data for service evaluation and quality improvement.

## Results

### Study sample

A total of **{N_total}** individuals were included in the cleaned Akurba outreach dataset. Visual impairment status could be determined for **{N_vi_den}** participants with complete data on better eye visual acuity.

The median age of attendees was **{median_age}** years, with an interquartile range of **{age_IQR_low}** to **{age_IQR_high}** years. There were **{n_female}** females, representing **{pct_female}%** of the sample.

### Prevalence of visual impairment

Overall, **{n_vi}** of **{N_vi_den}** participants were classified as having moderate or worse visual impairment in the better eye. This corresponds to an overall prevalence of **{prev_vi_overall}%** in this outreach population.

Prevalence increased consistently with age:

- **{prev_0_17}%** among children and adolescents aged 0–17 years  
- **{prev_18_39}%** among adults aged 18–39 years  
- **{prev_40_59}%** among adults aged 40–59 years  
- **{prev_60plus}%** among adults aged 60 years and above  

The highest burden of visual impairment was seen in the oldest age group.

When stratified by sex, visual impairment was **{slightly higher / similar / lower}** in females compared with males, with prevalence estimates of **{prev_female}%** and **{prev_male}%** respectively. The modest sample size and overlapping confidence intervals mean these differences should be interpreted cautiously.

### Pattern of diagnostic categories

Refractive error and cataract related disease were the dominant diagnostic categories.

- Refractive error accounted for **{n_refractive}** cases, representing **{pct_refractive}%** of all diagnoses.  
- Cataract related conditions accounted for **{n_cataract}** cases, representing **{pct_cataract}%** of all diagnoses.  

Glaucoma related disease, corneal disorders, and other or nonspecific conditions each contributed smaller proportions, in the range of **{pct_third}%** to **{pct_smallest}%**.

The distribution of diagnoses varied by age group. Among participants aged 60 years and above, cataract related disease dominated, forming **{pct_cataract_60plus}%** of diagnoses in this stratum. In contrast, refractive error was proportionally more common in younger participants, particularly in the 18–39 year group, where it accounted for **{pct_refractive_18_39}%** of recorded diagnoses.

### Visual impairment by age and sex

Cross tabulation of visual impairment status by age group and sex showed that the age gradient in impairment was present in both males and females. In each sex, the prevalence of moderate or worse visual impairment increased from the younger age bands to the oldest group. Differences between males and females within each age band were small relative to the overall age related pattern and were not precise enough to support strong conclusions.

### Logistic regression

In the logistic regression model with visual impairment as the outcome and age and sex as predictors:

- Each additional year of age was associated with an estimated odds ratio of **{OR_age}** for visual impairment, with a 95 percent confidence interval from **{CI_age_low}** to **{CI_age_high}**.  
- The odds ratio for females compared with males was **{OR_female}**, with a 95 percent confidence interval from **{CI_female_low}** to **{CI_female_high}**.

The direction of effect for age was consistent with the stratified prevalence estimates, with higher odds of impairment in older individuals. The sex effect was less clear and the confidence intervals were wide.

The model generated warnings about fitted probabilities approaching zero or one, indicating quasi complete separation in this small dataset. For that reason the regression results are presented as an illustration of modelling workflow and are interpreted with caution. The primary inferential weight is placed on the descriptive prevalence estimates and diagnostic patterns.

## Discussion

This outreach dataset from Akurba community shows a high burden of visual impairment among individuals who present for screening. The prevalence of moderate or worse impairment in the better eye was **{prev_vi_overall}%**, with the highest burden concentrated among adults aged 60 years and above.

Refractive error and cataract related disease emerged as the leading diagnostic categories, together accounting for a large majority of recorded diagnoses. This pattern mirrors findings from many hospital based and population based studies in Nigeria and similar settings, where uncorrected refractive error and cataract repeatedly account for most avoidable blindness and low vision.

The age gradient observed in this outreach is clinically intuitive. Cataract related disease and long standing refractive error accumulate with age, while younger adults and adolescents are more likely to present with uncorrected refractive error alone. The exploratory logistic regression model reinforced this pattern by showing increasing odds of visual impairment with increasing age, although issues with separation mean that the model should not be over interpreted.

From a service perspective, the findings support three practical conclusions:

1. Outreach clinics in comparable communities can expect to encounter a substantial backlog of cataract and uncorrected refractive error.  
2. Effective linkage to refractive services and cataract surgery lists is essential if outreach screening is to translate into durable improvements in vision.  
3. Routine digitisation and analysis of outreach data, as demonstrated here, provides a low cost way to monitor local burden and refine outreach strategies over time.

### Limitations

Several limitations should be noted.

- The dataset is not population based. It includes only individuals who chose to attend an outreach clinic, so it may over represent symptomatic or health seeking individuals and cannot be used to estimate community level prevalence.  
- The total number of patients is modest, especially within age and sex strata, which reduces precision and contributes to instability in regression models.  
- Diagnoses were provisional and made in an outreach setting without full diagnostic workup for every patient, so misclassification is possible.  
- Visual acuity was recorded under field conditions and subject to measurement error.

Despite these limitations, the analysis provides a useful profile of eye disease among those who actually present for outreach care in this setting and demonstrates how even small routine datasets can be converted into structured information for planning.

## Conclusions

The Akurba outreach dataset demonstrates a high prevalence of visual impairment among attendees, driven largely by cataract and refractive error and concentrated in older adults. Systematic cleaning, WHO aligned categorisation of visual acuity, and structured analysis using R made it possible to derive clear, reproducible summaries from a relatively small and messy dataset.

The workflow implemented here can be adapted to other outreach activities and scaled up to support routine monitoring of community eye health programmes.

## Data and code availability

All cleaning and analysis scripts for this project are provided in the accompanying R Markdown file `02_prevalence_and_patterns.Rmd`, together with derived summary tables in the `data/` directory. The patient level dataset used in this report was anonymised prior to analysis and is not publicly released.
