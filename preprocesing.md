For the clinical trial record downloaded from clinicaltrials.gov in JSON format in the space, apply the following pre-processing steps to prepare the data for the subsequent tasks:
Step 1. You are a biomedical informatics expert specializing in clinical data standardization. Your task is to map all mappable terms in the 6 modules to standard ontologies following the rules:
### Field-to-Ontology Assignments
Apply the following ontology targets per field. When a field is free text, extract
every distinct clinical entity before mapping.

| Field Path | Entity Type | Primary Ontology | Fallback Ontologies |
|---|---|---|---|
| protocolSection.conditionsModule.conditions[] | Disease/Condition | MeSH | SNOMED-CT, ICD-10-CM, OMIM |
| protocolSection.conditionsModule.keywords[] | Keyword | MeSH | UMLS CUI |
| protocolSection.armsInterventionsModule.interventions[].name | Drug/Device/Procedure | RxNorm (drugs), NCI Thesaurus (biologics/devices) | ATC, MeSH, SNOMED-CT |
| protocolSection.outcomesModule.primaryOutcomes[].measure | Outcome Measure | SNOMED-CT | UMLS CUI, MedDRA PT |
| protocolSection.outcomesModule.secondaryOutcomes[].measure | Outcome Measure | SNOMED-CT | UMLS CUI, MedDRA PT |
| protocolSection.eligibilityModule.eligibilityCriteria | Mixed (NER needed) | UMLS CUI | SNOMED-CT, RxNorm, ICD-10-CM, LOINC, HPO |
| protocolSection.designModule.studyType | Study Design | BRIDG / CDISC CT | — |
| protocolSection.designModule.phases[] | Trial Phase | CDISC CT | — |
| protocolSection.designModule.designInfo.interventionModel | Design Attribute | CDISC CT | — |
| protocolSection.contactsLocationsModule.locations[].country | Geography | ISO 3166-1 | — |
| protocolSection.sponsorCollaboratorsModule.leadSponsor.orgClass | Sponsor Type | CDISC CT | — |

### Eligibility Criteria Parsing
The `eligibilityCriteria` field is unstructured text. You must:
1. Segment the text into individual inclusion and exclusion criteria lines.
2. For each line, identify all named clinical entities (conditions, drugs, lab tests,
   measurements, demographics, biomarkers, procedures).
3. Map each extracted entity to its best ontology code.
4. Preserve the original criterion text alongside mappings.

### Mapping Confidence Levels
Assign one of the following confidence scores to each mapped term:
- **HIGH**: Exact string match or well-known synonym in the target ontology.
- **MEDIUM**: Semantic match via normalized form; minor lexical variation.
- **LOW**: Approximate match; mapped to parent/related concept; requires human review.
- **UNMAPPED**: No suitable match found; flag for manual curation.

### General Rules
- Preserve ALL original field values. Add ontology codes as sibling fields, never replace originals.
- If multiple codes apply (e.g., a drug maps to both RxNorm and ATC), include all.
- If a term is ambiguous, include the top 2 candidates with their confidence scores and a brief disambiguation note.
- Do not hallucinate ontology codes. If you are unsure of a code, mark it UNMAPPED and note the reason.

create the mapping table with one row per mapped entity in a CSV file with the same name but with suffix "-mapping.csv" (e.g., NCTxxxxxxxxxx-mapping.csv) and the following columns:

| # | Source Field | Original Term | Extracted Entity | Ontology | Code | Preferred Label | Confidence | Note |
|---|-------------|---------------|-----------------|----------|------|-----------------|------------|------|

Step 2. For the original JSON file, mask any terms in the mapping table to a placeholder with a random id (e.g., [Label-01], etc.), only mask the terms and keep the rest of the text intact. For example, if the original text is "Patients with Parkinson's disease will be enrolled," and "Parkinson's disease" is mapped to MeSH D010300, the masked text should be "Patients with [Condition-01] will be enrolled,". execute a comprehensive, JSON-wide replacement of all extracted entities including the free-text fields(such as briefSummary and detailedDescription).
Save the masked modules in a new JSON file with the same name but with suffix "-masked.json" (e.g., NCTxxxxxxxxxx-masked.json).
