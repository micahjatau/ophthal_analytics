# EMRAM Maturity Audit Using a Simplified Stage 0 to 4 Checklist

## Abstract

**Background:** The HIMSS Electronic Medical Record Adoption Model (EMRAM) provides a staged framework for assessing hospital EMR maturity. Many hospitals in low and middle income settings have partial digitalisation but limited documentation of their EMRAM stage.

**Objective:** To apply a simplified EMRAM Stage 0 to 4 checklist to an anonymised tertiary hospital, quantify the level of EMR adoption, and identify key gaps that prevent progression to higher stages.

**Methods:** We used a structured audit tool with one key criterion per EMRAM stage from 0 to 4. For each stage, the hospital team recorded a Yes, No, or Partial response, along with notes or evidence. Data were cleaned and standardised in R, with responses mapped to numeric scores (Yes = 1, Partial = 0.5, No = 0). We summarised mean scores by stage, derived domain level profiles from text tags, and determined the final EMRAM stage as the highest stage with a fully satisfied criterion, assuming all lower stages were also satisfied.

**Results:** The hospital did not meet Stage 0, which describes a fully paper based environment, confirming that some EMR components are in place. Criteria for Stage 1 and Stage 2 were fully satisfied, indicating that ancillary systems and a clinical data repository with coded data are present. The hospital did not satisfy Stage 3 or Stage 4 criteria. Nursing and clinical documentation were in place, but decision support functionality was absent, and Computerised Physician Order Entry (CPOE) had not been implemented. Under the EMRAM hierarchy, the hospital’s maturity was classified as Stage 2.

**Conclusion:** The EMRAM Stage 0 to 4 checklist confirmed early to intermediate EMR maturity, with full compliance at Stage 1 and Stage 2, and clear gaps at Stage 3 and Stage 4. Priority investments include implementation of clinical decision support and CPOE, building on the existing clinical data repository.

---

## Introduction

Hospitals are progressively adopting EMR systems, often in a piecemeal fashion. The HIMSS EMRAM provides a staged framework that describes a pathway from paper based records to fully digital, decision supported care. Each stage introduces specific capabilities such as ancillary systems, clinical data repositories, clinical documentation, decision support, and CPOE.

For hospitals in low and middle income settings, EMRAM offers a common language to describe progress and to benchmark against international peers. However, formal EMRAM assessments are not always available, and many facilities rely on informal descriptions of their digital capabilities.

This project applies a simplified EMRAM Stage 0 to 4 checklist to an anonymised tertiary hospital that has some EMR components in place but has not undergone a full EMRAM assessment. The aim is to determine the hospital’s EMRAM stage using a transparent, data driven method and to identify the main barriers to reaching higher stages.

---

## Methods

### Audit tool

We used a spreadsheet based tool with one row per EMRAM stage, from Stage 0 to Stage 4. For each stage, the tool records:

- The stage label (`EMRAM Stage` e.g. `Stage 0`, `Stage 1`).  
- The key criterion describing that stage (`Key Criteria`).  
- The hospital’s self reported status (`Hospital Status (Yes/No)`), with possible entries `Yes`, `No`, or `Partial`.  
- Free text notes or evidence (`Notes / Evidence`).

The criteria can be summarised as:

- **Stage 0:** No EMR components installed, all records are paper based.  
- **Stage 1:** Basic ancillary systems for laboratory, radiology and pharmacy.  
- **Stage 2:** Clinical data repository in place with controlled vocabulary and coded data.  
- **Stage 3:** Nursing and clinical documentation implemented, with some decision support elements.  
- **Stage 4:** CPOE implemented, typically with advanced decision support.

The audit was completed by local staff familiar with the hospital’s information systems.

### Data preparation

The audit tool was exported as a CSV file (`EMRAM_Stage_0_to_4_Audit_Tool.csv`). We used R for analysis.

Steps:

1. Import the CSV and standardise column names with `clean_names()`.  
2. Extract a numeric `stage` variable from `EMRAM Stage` (0 to 4).  
3. Rename variables to a simpler schema:

   - `stage` – EMRAM stage number  
   - `indicator` – criterion text  
   - `response` – `Yes`, `No`, or `Partial`  
   - `evidence` – notes and supporting detail

4. Harmonise the response field to remove minor spelling or capitalisation differences.

### Scoring

We mapped responses to numeric scores:

- `Yes` = 1  
- `Partial` = 0.5  
- `No` = 0  

For this simplified tool there is one primary indicator per stage, so the mean score at each stage equals the indicator score. The structure was retained to allow extension if future tools include multiple indicators per stage.

We calculated:

- `n_items` per stage  
- Counts of `n_yes`, `n_partial`, `n_no`  
- `mean_score` per stage, defined as the average of `score` across items.

Domain labels were applied to the criterion text to support domain level summaries, although the main focus of this report is the stage based view.

### Determining the final EMRAM stage

The EMRAM model is hierarchical. A hospital cannot be classified at a given stage unless it satisfies all criteria at lower stages.

We used the following logic:

1. Treat Stage 0 as a special case that describes a fully paper based environment. A response of `No` at Stage 0 indicates that the hospital has progressed beyond Stage 0.  
2. For stages 1 to 4, a stage is considered satisfied if the response is `Yes` and no critical gaps are noted in the evidence field.  
3. The final EMRAM stage is the highest stage number where the stage criterion is satisfied, and where all lower stages (from 1 up to that stage) are also satisfied.

This yields a single integer `final_stage` that represents the hospital’s EMR maturity on the Stage 0 to 4 scale.

### Reporting and visualisation

Summary tables and plots were produced in R:

- A table of stage level responses and scores.  
- A bar plot of mean score by stage, expressed as a percentage.  
- Domain profiles where relevant, using simple text based tagging of the indicator.

The narrative report was generated from `Report.Rmd`, which reads `emram_stage_summary.csv`, `emram_domain_summary.csv`, and `emram_final_stage.csv`.

---

## Results

### Stage level responses

The hospital’s responses to the EMRAM Stage 0 to 4 checklist were:

- **Stage 0:** Criterion “No EMR components installed, all records paper based” was marked `No`. This implies the hospital has at least some EMR components and has progressed beyond Stage 0.  
- **Stage 1:** Criterion for basic ancillary systems was marked `Yes`, with notes indicating laboratory, radiology and pharmacy modules that can be viewed when updated, although some paper based processes remain.  
- **Stage 2:** Criterion for a clinical data repository with controlled vocabulary and coded data was marked `Yes`, with the use of ICD coding documented in the notes.  
- **Stage 3:** Criterion for nursing and clinical documentation plus decision support elements was marked `No`. The notes state that clinical documentation exists, but CDSS features are absent.  
- **Stage 4:** Criterion for CPOE was marked `No`, indicating that electronic order entry is not yet in place.

Under the scoring scheme, Stage 1 and Stage 2 received a score of 1, Stage 3 and Stage 4 received a score of 0. Stage 0 received a 0 in the strict sense, but here a 0 is desirable since it indicates the hospital has moved past the fully paper based state.

### Final EMRAM stage

Applying the hierarchical rule from Stage 1 upwards:

- Stage 1 satisfied.  
- Stage 2 satisfied.  
- Stage 3 not satisfied.  
- Stage 4 not satisfied.

Therefore, the highest stage with all lower stages satisfied is **Stage 2**.

### Interpretation of Stage 2

Stage 2 indicates that:

- Ancillary systems for laboratory, radiology and pharmacy are at least partially digital and integrated.  
- A clinical data repository exists, using controlled vocabulary and coded data such as ICD.  
- The hospital has moved decisively beyond the fully paper based environment.

However:

- Clinical documentation and nursing documentation, while present, are not yet supported by embedded decision support.  
- CPOE has not been implemented, so medication and order workflows still rely on paper or non integrated processes.

Taken together, this is consistent with an early to intermediate stage of EMR maturity, with core infrastructure and data repositories in place, but without advanced clinical process automation.

---

## Discussion

### Principal findings

This simplified EMRAM audit classified the hospital at Stage 2 on the EMRAM Stage 0 to 4 scale. The hospital has moved beyond a fully paper based environment, has basic ancillary systems, and has implemented a clinical data repository with coded data. It has not yet implemented full decision support or electronic order entry, and therefore does not meet Stage 3 or Stage 4 criteria.

### Strengths of the approach

- **Transparency:** The scoring rules are simple and explicit. Each stage is backed by a clear Yes or No response, with supporting evidence.  
- **Reproducibility:** All steps, from data cleaning through scoring and final classification, are implemented in R scripts that can be rerun as the system evolves.  
- **Alignment with EMRAM concepts:** The criteria used for each stage match the core EMRAM descriptions for ancillary systems, clinical data repositories, clinical documentation, decision support, and CPOE.  
- **Low burden:** The tool requires only a small number of responses and can be maintained by the local informatics or QI team.

### Limitations

- **Simplified tool:** The implementation here uses a single key criterion per stage, rather than the full, detailed EMRAM indicator set. As a result, some nuances of EMR adoption are not captured.  
- **Self reported data:** Responses are based on local self assessment rather than an external audit. There is a risk of optimism bias or incomplete documentation of edge cases.  
- **No quantitative utilisation measures:** The tool does not measure actual usage rates of EMR features, such as the proportion of orders placed electronically, only their presence or absence.  
- **Limited domain granularity:** Domain summaries are based on text tagging of a small number of criteria, which is sufficient for a high level view but not for deep domain analysis.

### Implications

Despite its simplifications, this audit provides a defensible EMRAM stage classification that can be used to:

- Communicate the hospital’s digital maturity to internal leadership and external partners.  
- Prioritise investment in decision support and CPOE to move from Stage 2 toward Stage 3 and Stage 4.  
- Track progress over time as new systems are implemented and existing ones are upgraded.

The approach also serves as a template that can be extended with more detailed indicators, utilisation metrics, and multiple respondents to strengthen the robustness of the assessment.

---

## Conclusion

Using a structured EMRAM Stage 0 to 4 checklist and a transparent scoring method, the hospital was classified at EMRAM Stage 2. This indicates the presence of basic ancillary systems and a clinical data repository with coded data, but an absence of fully implemented decision support and CPOE.

The assessment highlights clear next steps:

- Implement and integrate clinical decision support into existing documentation workflows.  
- Plan and roll out CPOE for key order types, starting with medications and laboratory tests.  

These investments would move the institution toward higher EMRAM stages, closer integration of clinical processes, and improved support for safe, data driven care.
