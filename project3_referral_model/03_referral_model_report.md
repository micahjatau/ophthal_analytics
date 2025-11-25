# Referral Prediction Model for a Community Eye Health Outreach in Akurba, Lafia, Nigeria

## Abstract

**Background:** Community eye health outreaches in low resource settings often identify many patients who may require referral to higher level care. However, referral decisions are not always documented in a structured way, and it is useful to know whether simple screening data can reproduce a clear and consistent referral rule.

**Objective:** To develop and internally evaluate a simple referral prediction model using basic demographic and visual acuity data from a World Sight Day outreach in Akurba, Lafia, Nigeria.

**Methods:** We conducted a secondary analysis of an anonymised outreach dataset. The binary outcome was a derived variable, `referral_needed`, defined from diagnostic category and better eye visual acuity according to an a priori referral rule. Predictor variables were age group, sex, better eye visual acuity category and diagnostic category. We fitted a multivariable logistic regression model, split the data into training and test sets using stratified random sampling, and evaluated performance on the held out test set using accuracy, sensitivity, specificity and confusion matrices.

**Results:** The model showed strong discrimination between patients who met the referral rule and those who did not. On the test set, sensitivity was approximately 84.8 percent and specificity approximately 97.2 percent. Better eye visual acuity category was the strongest predictor of meeting the referral rule, with odds of referral rising steeply from normal or mild impairment through to very poor acuity. Age group showed a positive association with referral, while sex had little effect.

**Conclusion:** A simple logistic regression model that uses only age group, sex, better eye visual acuity category and diagnostic category can reproduce a clear referral rule with high sensitivity and very high specificity. This supports the use of structured screening forms and simple statistical models to standardise referral from community eye health outreaches in similar settings.

---

## Introduction

Community based eye health outreaches are a common strategy in low and middle income countries to improve access to basic eye care services. Large numbers of people are screened in a short time, often by teams working in temporary facilities with limited equipment. A key challenge is to decide which patients require referral for further assessment, surgery or specialised treatment at secondary or tertiary facilities.

In practice, referral decisions may be driven by a mixture of visual acuity, provisional diagnosis, patient symptoms and local service constraints. If referral criteria are not explicitly defined and consistently applied, there is a risk that some patients who need higher level care will be missed, while others may be referred unnecessarily.

In this context, simple prediction models can help to formalise and test referral rules using data that can be collected quickly in outreach settings. Logistic regression is well suited to this task because it produces interpretable odds ratios and can operate on categorical predictors such as age group and visual acuity categories.

The present analysis uses data from a World Sight Day outreach conducted in Akurba, Lafia, Nigeria. The aim is to determine whether a small number of routinely collected variables can reproduce a practical referral rule and to quantify the performance of this rule in terms of sensitivity and specificity.

## Methods

### Study setting and data source

Data were obtained from an anonymised database of patients who attended a World Sight Day outreach in Akurba, a peri urban community within Lafia, Nasarawa State, Nigeria. The outreach was organised by the ophthalmology team of a tertiary hospital and provided basic vision screening, refraction and clinical assessment.

The analysis used the cleaned dataset `akurba_with_referral.csv`, which was prepared from the raw outreach registers as part of a separate data cleaning workflow. Personally identifiable information was removed prior to analysis. Each row represented one patient encounter.

### Outcome definition

The primary outcome was a binary indicator variable, `referral_needed`, which operationalised an a priori clinical referral rule. Patients were marked as needing referral if they met any of the following criteria:

- Better eye visual acuity worse than a predefined threshold, consistent with moderate or worse visual impairment.
- Provisional diagnosis suggestive of conditions that require further assessment or surgical management at a higher level facility.
- Other serious findings that, by protocol, should trigger referral.

The exact rule was applied during the data preparation step, and the present analysis treated `referral_needed` as the dependent variable for modelling. The negative class ("0") represented patients who did not meet the referral rule, and the positive class ("1") represented those who did.

### Predictor variables

The following predictors were selected because they are simple to collect and likely to be available in most outreach settings:

- **Age group:** derived from recorded age and classified into ordered categories (Child, Adult, Middle_aged, Elderly).
- **Sex:** coded as Male or Female.
- **Better eye visual acuity category (better_eye_cat):** an ordered categorical variable summarising the better eye into Normal_mild, Moderate, Severe and Very_poor impairment, based on WHO aligned visual acuity bands.
- **Diagnostic category (diagnosis_cat):** a categorical variable grouping related provisional diagnoses into clinically meaningful categories (for example, refractive error, cataract related, posterior segment disease and other anterior segment disorders).

Where necessary, variables were recoded as factors with explicit ordering to reflect clinical severity, particularly for age group and better eye visual acuity category.

### Data preparation

All data preparation and modelling were carried out in R. The dataset was first loaded and inspected for completeness. Records with missing values in the outcome (`referral_needed`) or in any of the selected predictors were excluded from the modelling sample.

Age group, sex, better eye visual acuity category and diagnostic category were converted to factor variables. Age group and better eye categories were ordered so that higher levels corresponded to greater age or more severe visual impairment. The reference levels for the regression model were set to Child for age group, Male for sex, Normal_mild for better eye visual acuity and an appropriate baseline category for diagnosis.

### Model specification

We used logistic regression to model the log odds of meeting the referral rule as a function of the predictors. The model formula was:

`referral_needed ~ age_group + sex + better_eye_cat + diagnosis_cat`

The model was fitted using the `glm` function with a binomial family and logit link. Coefficients were exponentiated to obtain odds ratios, and 95 percent confidence intervals were calculated.

### Train test split and internal validation

To assess model performance on unseen data, the dataset was split into training and test subsets using the `rsample` package. A stratified initial split with a 70 to 30 ratio was applied, using `referral_needed` as the stratification variable to preserve the proportion of positive and negative cases in both subsets.

The logistic regression model was fitted on the training data. Predictions for the test data were obtained as predicted probabilities of `referral_needed`. A default classification threshold of 0.5 was used to convert probabilities into predicted classes ("0" or "1").

### Performance metrics

Model performance on the test set was summarised using:

- **Confusion matrix:** counts of true negatives, false positives, false negatives and true positives.
- **Sensitivity (true positive rate):** proportion of referral cases correctly predicted as needing referral.
- **Specificity (true negative rate):** proportion of non referral cases correctly predicted as not needing referral.
- **Overall accuracy:** proportion of all test cases correctly classified.
- **Additional metrics:** such as Cohen's kappa and other scalar metrics, where helpful, using the `yardstick` package.

These metrics were used to judge whether the model reproduced the referral rule with acceptable sensitivity and specificity.

## Results

### Sample description

After excluding records with missing data in the outcome or predictors, the final modelling sample consisted of outreach attendees with a broad age range from children to elderly adults. The majority of participants were adults and middle aged adults, with fewer children and elderly participants. Both sexes were represented, with a small predominance of females that is typical for eye care outreaches in similar settings.

Better eye visual acuity categories showed a spread from normal or mild impairment through to moderate, severe and very poor visual acuity. Diagnostic categories included refractive error, cataract related conditions and other anterior and posterior segment pathologies.

### Model coefficients

The multivariable logistic regression model identified strong associations between visual acuity, age group and the likelihood of needing referral according to the predefined rule.

Compared with the reference group of patients with Normal_mild visual acuity in the better eye, those with Moderate, Severe or Very_poor categories had progressively higher odds of meeting the referral rule. The increase in odds was steep, reflecting the fact that the referral rule was heavily driven by visual acuity thresholds.

Age group also showed a positive gradient. Adults, middle aged and elderly participants had higher odds of meeting the referral criteria compared with children, although the exact magnitudes depended on the underlying distribution of diagnoses and visual acuity in each group. Sex had no consistent or clinically meaningful effect on the odds of referral once age and visual acuity were accounted for. Some diagnostic categories also contributed additional predictive information, particularly those associated with cataract and other sight threatening conditions.

### Test set performance

On the held out test set, the model delivered strong performance when evaluated using a decision threshold of 0.5 for the predicted probability of referral. The confusion matrix for the test data can be summarised as:

- True negatives (predicted 0, actual 0): 35
- False positives (predicted 1, actual 0): 1
- False negatives (predicted 0, actual 1): 5
- True positives (predicted 1, actual 1): 28

From this, the key performance metrics are:

- **Sensitivity:** 28 / (28 + 5) ≈ 0.848, or 84.8 percent.
- **Specificity:** 35 / (35 + 1) ≈ 0.972, or 97.2 percent.
- **Overall accuracy:** (35 + 28) / (35 + 1 + 5 + 28) = 63 / 69 ≈ 91.3 percent.

These figures indicate that the model correctly identified the vast majority of patients who met the referral rule and very rarely misclassified non referral patients as requiring referral. The balance of high specificity with reasonably high sensitivity is desirable in a context where referral capacity is limited and it is important to avoid overwhelming higher level clinics with unnecessary referrals while still capturing most patients who genuinely need further care.

Although not detailed here, the high level of separation between predicted probabilities for referral and non referral cases suggests that the area under the receiver operating characteristic curve would also be high, consistent with excellent discrimination for a relatively simple model.

## Discussion

This analysis shows that a logistic regression model using only age group, sex, better eye visual acuity category and diagnostic category can reproduce a practical referral rule with high accuracy in a community outreach dataset from Akurba, Lafia. The model achieved sensitivity of about 85 percent and specificity of about 97 percent on a held out test set, which is strong performance for an internal validation.

The finding that better eye visual acuity is the dominant predictor is clinically intuitive. Patients with moderate, severe or very poor visual acuity in the better eye are more likely to meet criteria for referral because they are at higher risk of functional visual impairment and frequently require interventions such as refraction, cataract surgery or specialist assessment. The positive association between older age groups and referral is also expected, given the higher prevalence of cataract and other age related eye diseases in these groups.

From an operational perspective, these results suggest that outreach referral decisions can be formalised using a short list of variables that can be captured quickly during screening. Programs could embed similar models within electronic registers or simple decision support tools. Such tools would not replace clinician judgement but would provide a transparent, auditable baseline rule against which individual decisions can be compared.

However, several limitations must be acknowledged. First, the outcome variable is a derived representation of an internal referral rule, not a record of real world referral decisions or actual uptake of referral. The model therefore tells us how well simple predictors reproduce the rule, rather than how well they predict which patients ultimately reach higher level care and benefit from it.

Second, the sample size is modest, and some visual acuity and diagnostic categories may have relatively few observations, which can lead to unstable odds ratio estimates and wide confidence intervals. There is also the possibility of quasi complete separation in the most severe visual acuity categories, where almost all patients require referral. This does not invalidate the basic conclusion that visual acuity is a strong predictor, but it suggests that estimates for the most extreme categories should be interpreted with caution.

Third, the model does not include all potentially relevant clinical factors such as intraocular pressure, detailed symptom profiles or comorbid conditions. In practice, clinicians may consider these additional factors when deciding whether to refer a patient. Future work could explore extended models that incorporate these variables, provided they can be collected consistently in the outreach context.

Finally, the model was evaluated using internal validation only. External validation on datasets from other outreaches, facilities or regions would be needed to assess generalisability. Differences in case mix, service organisation and referral thresholds might affect performance.

## Conclusion

A simple logistic regression model based on age group, sex, better eye visual acuity category and diagnostic category can reproduce an a priori referral rule with high sensitivity and very high specificity in a community eye health outreach dataset from Akurba, Lafia. This supports the feasibility of using structured screening data and basic statistical models to standardise and document referral decisions in similar outreach programmes.

Future work should focus on external validation, integration into electronic tools used during outreaches, and evaluation of the impact of such decision support on referral uptake and visual outcomes.

## References

1. World Health Organization. World report on vision. Geneva: WHO.
