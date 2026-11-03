# CT-TEL: Scaling Up Formal Representation of Clinical Trial Protocols in Ensemble Logic Using LLMs

[![arXiv](https://img.shields.io/badge/arXiv-2607.21307-b31b1b.svg)](https://arxiv.org/abs/2607.21307)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

This repository accompanies our paper:

> Huang Y, Hao X, Li X, Abeysinghe R, Jiang X, Cui L, Zhang G-Q. **Scaling Up
> Formal Representation of Clinical Trial Protocols in Ensemble Logic Using
> LLMs: A Preliminary Study.** arXiv:2607.21307, 2026. To appear in the
> Proceedings of the 2026 AMIA Symposium.

It provides the four user prompts we used in the CT-TEL workflow. We release
the prompts as used in the study. The generated CT-TEL Library, source records,
model configurations, and evaluation code are not included.

## Overview

Clinical trial protocols encode eligibility, treatment, follow-up, and outcome
requirements in free text. These requirements are inherently temporal, but they
cannot be queried or simulated directly. TEL and QEL provide a formal
representation with metric temporal operators, yet manual encoding does not
scale. We introduce CT-TEL, a prompt-driven workflow that uses LLMs in both
directions of the translation:

1. **Forward translation:** protocol to ontology-grounded events, atomic
   propositions, modular logic blocks, and a canonical TEL formula.
2. **Reverse translation:** canonical formula to a reconstructed six-module
   protocol, with no access to the source text.
3. **Evaluation:** human review, LLM review, and algorithmic lexical and
   semantic scoring against the original record.

The workflow produces a six-level library that preserves traceability from the
canonical formula back to the source text:

```text
Level 0  Raw ClinicalTrials.gov JSON records
Level 1  Ontology-mapped terms
Level 2  Temporal events
Level 3  Atomic propositions
Level 4  Modular logic blocks
Level 5  Canonical TEL formulas
```

## Prompts

The four user prompts cover the forward and reverse translation stages.

| Step | Prompt | Purpose | Input | Output |
|---|---|---|---|---|
| 1 | [`01-preprocessing-ontology-mapping-and-masking.md`](prompts/v1.0/01-preprocessing-ontology-mapping-and-masking.md) | Semantic mapping and ontology masking | ClinicalTrials.gov JSON record | Mapping CSV and masked JSON |
| 2 | [`02-temporal-event-extraction.md`](prompts/v1.0/02-temporal-event-extraction.md) | Temporal event extraction | JSON record and mapping table | JSON array of temporal events |
| 3 | [`03-tel-formalization.md`](prompts/v1.0/03-tel-formalization.md) | TEL formalization | JSON record and temporal events | Markdown with propositions, formula blocks, and canonical formula |
| 4 | [`04-tel-to-protocol.md`](prompts/v1.0/04-tel-to-protocol.md) | TEL-to-protocol translation | Formula file only | Reconstructed six-module protocol JSON |

Step 4 is deliberately isolated: the translator receives only the formula file,
not the source record, mapping table, or event array. See
[`prompts/README.md`](prompts/README.md) for usage notes.

## Repository contents

```text
README.md
CITATION.cff
CITATION.bib
LICENSE
prompts/
├── README.md
└── v1.0/
    ├── 01-preprocessing-ontology-mapping-and-masking.md
    ├── 02-temporal-event-extraction.md
    ├── 03-tel-formalization.md
    └── 04-tel-to-protocol.md
```

## Corpus and results

We applied CT-TEL to 23 Alzheimer Disease and Related Dementias (ADRD)
medication trials from ClinicalTrials.gov: 4 Active, Not Recruiting and 19
Completed. All automated steps used Gemini 3.1 Pro in thinking mode. Across the
corpus, the workflow produced 731 ontology-mapped terms, 317 temporal events,
325 atomic propositions, 185 logic blocks, and 23 canonical formulas.

We evaluated translation fidelity on six sampled trials with four TEL experts
and an independent LLM reviewer. The 24 reviewed items gave Fleiss' kappa of
0.548 (moderate agreement), a consensus human mean of 1.89, and an LLM mean of
1.85 on a four-point scale where 1 is best.

We macro-averaged the algorithmic scores over the 23 canonical trials:

| Module | Lexical similarity | Semantic similarity |
|---|---:|---:|
| Description | 0.452 | 0.790 |
| Conditions | 0.363 | 0.622 |
| Design | 0.422 | 0.762 |
| ArmsInterventions | 0.407 | 0.771 |
| Outcomes | 0.446 | 0.807 |
| Eligibility | 0.460 | 0.860 |

Semantic similarity was computed with `text-embedding-3-large` through Azure
OpenAI; lexical similarity used TF-IDF weighted cosine similarity. We also
report a cross-LLM comparison with Claude Sonnet 4.6, an ontology-masking
ablation, and a failed attempt with GPT-5.4.

## Example

We use trial NCT04179721 as a worked example. Its canonical formula is:

```text
φ_elig = (STAFF_INC)_x ∧ (¬DECLINE_EXC)_x ∧ (¬TEMP_STAFF_EXC)_x
φ_base = (ADRD ∧ BPSD ∧ ADQ_BL ∧ SEWDR_BL)_x
φ_tx   = (□_3m PES_TX)_x ∨ (□_3m CTRL_TX)_x
φ_out  = (◇_1m (ADQ_3M ∧ SEWDR_3M))_(x+3m)
         ∧ (◇_1m (ADQ_15M ∧ SEWDR_15M))_(x+15m)
Φ      = ∃x [ φ_elig ∧ φ_base ∧ φ_tx ∧ φ_out ]
```

The formula states that eligible staff are enrolled at baseline, that ADRD and
BPSD are confirmed and baseline measures recorded, that staff receive either
the PES-4-BPSD or attention-control training continuously for three months, and
that outcomes are reassessed within one-month windows at three and fifteen
months.

## Scope

This repository is limited to the prompts; it is not a full reproducibility
package. In particular:

- The generated per-trial artifacts, source-record snapshots, and scoring code
  are not included. The results above are the published results.
- ClinicalTrials.gov records can change after retrieval, and model behavior can
  vary across provider versions. Re-running the prompts will not reproduce the
  published numbers exactly.
- Prompts 3 and 4 refer to `qpel.pdf`, `ct-tel.pdf`, and
  `qpel-tel-rule-book.md`. These working reference materials are not included.
- We observed two failure modes in the study: temporal distortion of nested
  constraints and injection of implicit clinical assumptions.

## Citation

If you use these prompts, please cite the paper:

```bibtex
@misc{huang2026cttel,
  title         = {Scaling Up Formal Representation of Clinical Trial Protocols
                   in Ensemble Logic Using LLMs: A Preliminary Study},
  author        = {Huang, Yan and Hao, Xubing and Li, Xiaojin and
                   Abeysinghe, Rashmie and Jiang, Xiaoqian and Cui, Licong and
                   Zhang, Guo-Qiang},
  year          = {2026},
  eprint        = {2607.21307},
  archivePrefix = {arXiv},
  primaryClass  = {cs.LO},
  doi           = {10.48550/arXiv.2607.21307},
  url           = {https://arxiv.org/abs/2607.21307},
  note          = {To appear in the Proceedings of the 2026 AMIA Symposium}
}
```

Machine-readable citation metadata is available in
[`CITATION.cff`](CITATION.cff) and [`CITATION.bib`](CITATION.bib).

## License and acknowledgments

The prompts and repository materials are released under the
[MIT License](LICENSE). ClinicalTrials.gov data remains subject to its own terms
of use.

This work was supported in part by National Science Foundation Award IIS2500624
and National Institutes of Health grants R01AG084236 and U24AG098157. The views
are those of the authors and do not necessarily reflect those of the funding
agencies.
