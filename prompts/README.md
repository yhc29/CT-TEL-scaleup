# CT-TEL prompts

The four files in `v1.0/` are the user prompts used in the CT-TEL workflow
described in the paper. Their text is unchanged from the study; filenames have
been normalized for ordering.

| Step | File | Purpose | Input | Output |
|---|---|---|---|---|
| 1 | [`v1.0/01-preprocessing-ontology-mapping-and-masking.md`](v1.0/01-preprocessing-ontology-mapping-and-masking.md) | Semantic mapping and ontology masking | ClinicalTrials.gov JSON record | Mapping CSV and masked JSON |
| 2 | [`v1.0/02-temporal-event-extraction.md`](v1.0/02-temporal-event-extraction.md) | Temporal event extraction | JSON record and mapping table | JSON array of temporal events |
| 3 | [`v1.0/03-tel-formalization.md`](v1.0/03-tel-formalization.md) | TEL formalization | JSON record and temporal events | Markdown with propositions, formula blocks, and canonical formula |
| 4 | [`v1.0/04-tel-to-protocol.md`](v1.0/04-tel-to-protocol.md) | TEL-to-protocol translation | Formula file only | Reconstructed six-module protocol JSON |

## Usage notes

- Run one trial per context-cleared session so that the model does not carry
  information from another protocol.
- Place the input record in the model's context. The prompts use NCT-prefixed
  filenames such as `NCTxxxxxxxx-mapping.csv`; these can be adapted to your
  storage layout.
- Step 4 must receive only the formula file. Do not provide the source JSON,
  the mapping table, or the event array.
- Prompts 3 and 4 refer to `qpel.pdf`, `ct-tel.pdf`, and
  `qpel-tel-rule-book.md`. These working reference materials are not included.
- The paper describes the mapping step as using UMLS and AD-CDO. The AD-CDO
  vocabulary is not redistributed here; supply an equivalent reference
  terminology when running prompt 1 if needed.
