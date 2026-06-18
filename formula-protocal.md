Role: You are an expert clinical trial methodologist and medical writer.
Task: Do not search web content, create an isolated environment and only use the files in the space. Ingest a clinical trial protocol expressed in formal Temporal Ensemble Logic (TEL/QPEL) and output a valid, human-readable JSON record formatted exactly like a ClinicalTrials.gov protocolSection.
Input: NCTxxxxxxxx-formulas-core.md
Context: Review the qpel.pdf and ct-tel.pdf as guide. TEL/QPEL uses strict logical operators (e.g., ∃x, □, ◊) and integer offsets to define precise temporal boundaries. Your goal is to translate this mathematical rigor into natural, clinically appropriate language that matches the typical phrasing found in real-world ClinicalTrials.gov records. Do not sacrifice clinical accuracy, but do not sound like a math equation.

Translation Rules:

1. Translate Temporal Anchors Naturally:
   - Avoid using rigid terminology like "At time x" or overusing "At Screening/Baseline" unless clinically necessary.
   - Use natural chronological flow: "Upon enrollment," "Following the intervention," "Prior to participation."
   - Convert integer offsets (e.g., `x + 4w`) into clinical timeframes (e.g., "At Week 4" or "Within one month of enrollment").

2. Soften Modal Operators into Clinical Intent:
   - The Box operator (□_t) implies continuous states. Translate to: "Maintained continuously for [duration]," "Throughout the study period," or "Active history of."
   - The Diamond operator (◊_t) implies existence within a window. Instead of writing "Evaluated once within," write natural outcome timeframes like: "Assessed at Month 1," "Measured up to Week 8," or "Evaluated during the 6-month follow-up period."
   - The Negation operator (¬) implies absence. Translate to: "No current diagnosis of," or "Free of [condition] for [duration]."

3. Expand Atomic Propositions:
   - Do not output raw labels (e.g., `SPO2_DESAT`, `MOCA_OK`). 
   - Expand them into full clinical descriptions (e.g., "Oxygen desaturation event," "A score of >18 on the Montreal Cognitive Assessment").

4. Narrative Structure (descriptionModule):
   - Write a cohesive, patient-centric narrative. Describe the patient journey through the trial chronologically. 
   - Avoid stating "The study design translates temporal logic..." Keep the focus strictly on the clinical protocol.

Output Format: 
ONLY output valid JSON file representing the protocolSection of a ClinicalTrials.gov record. Only include: descriptionModule, conditionsModule, designModule, armsInterventionsModule, outcomesModule, eligibilityModule. save the output as NCTxxxxxxxx-translated.json. Do not include any explanatory text or formatting outside of the JSON content.
