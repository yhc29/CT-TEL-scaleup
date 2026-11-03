Task Step 2: Formula builder
Role: You are a formal logic engineer specializing in Temporal Ensemble Logic (TEL).
Task: Define all atomic temporal events, create QPEL/TEL formulas to cover all temporal events and temporal constraints, combine all the logical constraints into a canonical QPEL/TEL representation for this clinical trial. Create a file including all necessary Atomic Propositions,formula blocks and the final combined formula that can reconstruct the original protocol.
Input: NCTxxxxxxxxx.json and NCTxxxxxxxxx-temporal-events.json
Instructions:
0. review qpel.pdf and ct-tel.pdf as guide and follow qpel-tel-rule-book.md
1. Define Atomic Propositions (Prop): Create short, uppercase labels for all clinical events (e.g., `ISI_SURVEY`, `CBTi_TX`, `MOCA_OK`).
2. Define Anchor Variables: 
   - Let `x` be the Start Event (e.g., Screening).
   - Let `r` be the Index Event (e.g., Randomization). Use offsets (e.g., `r = x + 7`).
3. Apply Metricized Modal Operators:
   - Box Operator (□_t): Use for continuous or repeatedly sustained states over `t` days (e.g., `□_42 CBTi_TX` for 42 days of treatment).
   - Diamond Operator (◊_t): Use for discrete events occurring *at some point within* a `t`-day window (e.g., `◊_7 BLOOD_DRAW`).
4. Output the logical formula blocks:
   - Eligibility (Pre-x and at x)
   - Baseline (x to r)
   - Intervention (starting at r)
   - Outcomes (offsets from r)

Output Format: 
1. Glossary of Atomic Propositions with event names extracted from previous steps.
2. create QPEL formulas to cover all temporal events and temporal constraints
3. combine all the logical constraints into a canonical QPEL representation for this clinical trial
4. save Atomic Propositions, formula blocks and the final combined formula in a markdown file named NCTxxxxxxxxx-formulas-core.md in the space. Do not include any explanatory text or formatting outside of the markdown content.
