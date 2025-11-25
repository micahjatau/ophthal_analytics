# Project 3 – Referral Prediction Model

This project builds a logistic regression model to predict whether outreach patients meet a predefined referral rule using only simple screening variables: age group, sex and better-eye visual acuity category. The goal is to show how far we can approximate a more detailed referral rule with minimal data that can be collected quickly in the field.

The model uses the cleaned dataset produced in **Project 1**:
`project1_clean_qc/data_processed/akurba_with_referral.csv`.

---

## Objectives

- Use the engineered variables from Project 1 to model `referral_needed`.
- Quantify how strongly age and visual acuity are associated with meeting the referral rule.
- Evaluate how well a simple logistic regression can separate referral vs non-referral cases.
- Discuss model performance in terms of screening trade offs (sensitivity vs specificity).

---

## Data

- Source: anonymised World Sight Day outreach dataset from Akurba, Lafia.
- Input file: `../project1_clean_qc/data_processed/akurba_with_referral.csv`
- Key variables used:
  - `referral_needed` – binary flag (0 or 1) defined from diagnostic category and visual acuity in Project 1.
  - `age_group` – Child, Adult, Middle_aged, Elderly.
  - `sex` – Male, Female.
  - `better_eye_cat` – Normal_mild, Moderate, Severe, Very_poor.

Rows with missing values in these variables are excluded from the modelling dataset.

---

## Methods

1. **Preprocessing**
   - Convert `referral_needed` to a factor with levels 0 and 1.
   - Ensure `age_group`, `sex` and `better_eye_cat` are factors with meaningful ordering.
   - Drop observations with missing values in outcome or predictors.

2. **Train test split**
   - Use `rsample::initial_split` with a 70/30 split.
   - Stratify on `referral_needed` to preserve class balance in both sets.

3. **Model**
   - Fit a logistic regression model:

     ```r
     referral_needed ~ age_group + sex + better_eye_cat
     ```

   - Use `broom::tidy()` to obtain odds ratios and confidence intervals.

4. **Evaluation**
   - Predict referral probabilities on the test set.
   - Classify using a 0.5 probability threshold and compute:
     - confusion matrix
     - accuracy, sensitivity, specificity
     - Cohen's kappa
   - Plot ROC curve and compute AUC using `pROC::roc`.

5. **Sensitivity analysis**
   - Repeat evaluation at a lower threshold (0.3) to illustrate the trade off between sensitivity and specificity in a screening context.

---

## Results (summary)

- Model fitted on approximately 160 patients after excluding rows with missing data.
- Predictors:
  - age group
  - sex
  - better-eye visual acuity category

- Key associations:
  - Worse better-eye visual acuity is the dominant driver of referral.
    - Moving from Normal_mild to Moderate VA increases the odds of meeting the referral rule by roughly an order of magnitude.
    - Almost all patients with Severe or Very_poor VA are referred, leading to quasi-complete separation in those categories.
  - Odds of referral increase with age from children to elderly adults.
  - Sex has no meaningful effect on referral status.

- Performance on the held-out test set at a 0.5 threshold:
  - Accuracy: about 91.3%
  - Sensitivity for referral cases: about 84.8%
  - Specificity for non-referral cases: about 97.2%
  - ROC AUC: 0.979, indicating good to excellent discrimination based on age and visual acuity alone.

These results suggest that a simple model using only age and better-eye visual acuity can approximate the more detailed referral rule reasonably well, especially when high specificity is desired.

---

## Limitations

- The outcome `referral_needed` is rule-based, derived from diagnostic category and visual acuity in Project 1, not actual clinician referral decisions.
- There is quasi-complete separation for the most impaired visual acuity categories, so odds ratios for those levels are very large and numerically unstable, although the direction of effect is clear.
- Sample size is modest, and some age-group effects have wide confidence intervals.
- Only three predictors are used; the model ignores potentially relevant information such as specific diagnoses, symptoms, intraocular pressure and comorbidities.

---

## Conclusion
A simple model using age and VA alone can recover the logic of a more detailed referral rule with high discrimination (AUC 0.979). This supports the development of quick triage tools for field use in outreach and community screening.


---


**Next**: Consider testing more complex classifiers or visualizing referral probability over age and VA strata.



See full analysis in [03_referral_model.Rmd](03_referral_model.Rmd), report [03_referral_model_report.md](03_referral_model_report.md) and output [03_referral_model.html](03_referral_model.html).
