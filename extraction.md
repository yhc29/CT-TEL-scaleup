Role & Goal: You are an expert in clinical trial informatics and formal logic. We are working on a multi-step task to build QPEL (Quantified Propositional Ensemble Logic) formulas for clinical trial simulation. The goal is to translate free-text clinical trial records from ClinicalTrials.gov into precise, formal logic expressions.
Task Step 1: Temporal Event Extraction
review all documents in the space, then use the ClinicalTrials.gov v2 API JSON record, extract all distinct temporal events related to clinical trial simulation that are crucial to this clinical trial. use the standard ontology terms in the mapping table if possible from previous step.
Input:A single ClinicalTrials.gov v2 API JSON record NCTxxxxxxxx.json and mapping table NCTxxxxxxxx-mapping.csv in the space.
Instructions:
Identify clinical events, interventions, conditions, outcomes and their associated temporal constraints (e.g., "within 6 months," "prior to screening," "ongoing"). Use the ontology from the mapping table if possible.
Format the output strictly as a JSON array of objects.
Each object in the array should contain:
event_name: A concise name for the event.
module: The specific module in the ClinicalTrials.gov record where the event is described (descriptionModule, conditionsModule, designModule, armsInterventionsModule, outcomesModule, eligibilityModule), can be multiple if the event is described in multiple modules.
original_text: The exact text from the original document describing the event.
temporal_constraint: The specific time condition associated with the event, use precise free text that can be expressed in TEL/QPEL (or "None" if not applicable).
criterion_type: "Inclusion", "Exclusion", or "None" if not applicable.
ontology: standard ontology term, "None" if not applicable
Please output only the JSON array and save as NCTxxxxxxxxx-temporal-events.json. Do not include any explanatory text or formatting outside of the JSON array.
