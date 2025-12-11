---
title: "FUTH Lafia Ophthalmology Data Portfolio"
layout: default
---

# FUTH Lafia Ophthalmology Data Analysis Portfolio

This site showcases a series of real clinical data projects built from ophthalmology service data at the Federal University Teaching Hospital, Lafia.  
Each project demonstrates practical skills in data cleaning, visual acuity classification, clinical outcomes analysis, and digital health maturity assessment using R.

Use this page as a hub to explore the individual projects and their reports.

---

## Project 1 – Data Cleaning and WHO Visual Acuity Classification

**Goal:** Build a reproducible pipeline for cleaning outreach data and classifying visual acuity using World Health Organization categories for visual impairment.

**What is inside:**

- Parsing and normalising visual acuity strings  
- Custom ranking function for visual acuity  
- WHO aligned classification of the better eye  
- Clean dataset exported for downstream use  

🔗 View project folder: [`Project1/`](Project1/)  

---

## Project 2 – Prevalence and Pattern of Eye Diseases in Akurba Outreach

**Goal:** Estimate the prevalence of visual impairment and describe the pattern of ocular disease in a rural outreach population.

**What is inside:**

- WHO based visual acuity categories  
- Prevalence estimates by age and sex  
- Diagnostic group distribution (refractive error, cataract, glaucoma, corneal disease)  
- Narrative report with methods, results, and discussion  

🔗 View project folder: [`Project2/`](Project2/)  
🔗 Read the report: [`Project2/Report.md`](Project2/Report.md)

---

## Project 3 – Referral Decision Support from Outreach Data

**Goal:** Build a simple referral decision logic using diagnosis categories and visual acuity to flag patients who require further ophthalmic evaluation.

**What is inside:**

- Rule based referral flag (`referral_needed`)  
- Stratified summaries by age, sex, and diagnostic category  
- Basic visualisations of referral burden  

🔗 View project folder: [`Project3/`](Project3/)

---

## Project 4 – Trabeculectomy Outcomes in Glaucoma Patients

**Goal:** Evaluate visual acuity stability, intraocular pressure reduction, and medication burden after trabeculectomy.

**What is inside:**

- Conversion of visual acuity to logMAR  
- Intraocular pressure changes from baseline to follow up  
- Complete versus qualified surgical success definitions  
- Linear mixed models for IOP trajectories  
- Kaplan Meier survival analysis for time to failure  

🔗 View project folder: [`Project4/`](Project4/)  
🔗 Outcomes report: [`Project4/05_trab_outcomes-2.html`](Project4/05_trab_outcomes-2.html)  
🔗 Survival analysis: [`Project4/06_trab_survival.html`](Project4/06_trab_survival.html)

---

## Project 5 – EMRAM Digital Maturity Assessment

**Goal:** Use the HIMSS EMRAM framework to assess hospital electronic medical record maturity and generate a simple digital maturity profile.

**What is inside:**

- Cleaning and standardisation of an EMRAM audit tool  
- Binary and numeric scoring of indicators  
- Domain level summary tables  
- Overall stage assignment and basic visualisations  

🔗 View project folder: [`Project5/`](Project5/)

---

## Methods and Tools

All analyses were performed using:

- R with tidyverse, janitor, ggplot2, lme4, survival and related packages  
- RMarkdown for reproducible reporting  
- Clinical frameworks such as WHO visual impairment categories, glaucoma surgery outcome criteria, and EMRAM stages  

The data used in these projects is fully anonymised and is not intended for public redistribution.

---

## About the Author

This portfolio was created by **Dr. Micah Jatau**, Chief Registrar in Ophthalmology at the Federal University Teaching Hospital, Lafia, with a focus on clinical data analysis, digital health, and decision support.

For collaborations or questions, you can reach out via email.
