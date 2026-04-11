# How to Use This Kit with AI

This guide explains how to set up AI sessions for each step in the Platform & Infrastructure Kit workflow. Follow the session setup instructions precisely. Incorrect session setup is the most common cause of poor artifact quality.

---

## Core Discipline

**One artifact per session.** Do not generate multiple artifacts in the same session.

**Separate generation and validation.** Always validate in a new session. Never ask the AI that generated an artifact to validate it. This produces self-validation bias.

**Include full frozen documents.** Do not summarize upstream artifacts. Provide the complete document.


## PDR. Generation Session

**Session setup:**
```
Documents to provide:
1. Decision context (problem, constraints, opportunity. From decision maker)
2. Alternatives considered with pros/cons (from decision maker)
3. docs/specs/pdr-spec.md
4. docs/artifacts/pdr-template.md
5. Relevant docs/principles/ files (if applicable)

Prompt:
"Generate a Platform Decision Record using the provided decision context.
Follow the prompt in docs/prompts/pdr-prompt.md.
Use the template exactly. Satisfy all hard gates in the spec.
The decision authority must be a named individual.
Do not invent alternatives or tradeoffs. Mark any missing information
with [MISSING: reason]. Output pure Markdown."
```

**After generation:** Review the PDR. Confirm:
- The decision statement is accurate and specific
- All alternatives considered by the decision maker are included
- The decision authority is the correct named individual
- Tradeoffs are specific, not generic
- The reversibility assessment is realistic

**Validation session setup:**
```
Documents to provide:
1. The generated PDR (full document)
2. docs/specs/pdr-spec.md

Prompt:
"Validate this Platform Decision Record against the PDR spec.
Use only the spec as the source of truth for pass/fail criteria.
Do not suggest improvements. Judge only what is explicitly present.
Output JSON using the format defined in docs/validators/pdr-validator.md."
```


## ISPEC. Generation Session

**Session setup:**
```
Documents to provide:
1. All frozen PDRs relevant to this system
2. System requirements (functional and non-functional)
3. docs/specs/ispec-spec.md
4. docs/artifacts/ispec-template.md
5. Relevant docs/principles/ files (if applicable)

Prompt:
"Generate an Infrastructure Specification using the provided inputs.
Follow the prompt in docs/prompts/ispec-prompt.md.
Use the template exactly. Satisfy all hard gates in the spec.
All resource values must include units. All cost estimates must include currency.
Do not invent resource sizes without basis. Mark any missing information
with [MISSING: reason]. Output pure Markdown."
```

**After generation:** Review the ISPEC. Confirm:
- Resource sizes are realistic for the system's requirements
- Scaling thresholds have rationale (not arbitrary values)
- Cost estimates are plausible given the resource specifications
- DR requirements (RPO/RTO) are appropriate for the system's criticality
- All PDR technology decisions are reflected in the resource specifications

**Validation session setup:**
```
Documents to provide:
1. The generated ISPEC (full document)
2. docs/specs/ispec-spec.md

Prompt:
"Validate this Infrastructure Specification against the ISPEC spec.
Use only the spec as the source of truth for pass/fail criteria.
Do not suggest improvements. Judge only what is explicitly present.
Output JSON using the format defined in docs/validators/ispec-validator.md."
```


## EM. Generation Session

**Session setup:**
```
Documents to provide:
1. Frozen ISPEC (full document)
2. Environment information from the team (what environments exist or are planned)
3. docs/specs/em-spec.md
4. docs/artifacts/em-template.md
5. Relevant docs/principles/ files (if applicable)

Prompt:
"Generate an Environment Matrix using the provided inputs.
Follow the prompt in docs/prompts/em-prompt.md.
Use the template exactly. Satisfy all hard gates in the spec.
Document all environments. Do not omit any that exist.
Every difference between environments must have a justification.
Do not assume parity. Confirm identical aspects explicitly.
Output pure Markdown."
```

**After generation:** Review the EM. Confirm:
- All environments are listed (no shadow or undocumented environments)
- Promotion rules match your actual deployment pipeline
- Parity assessment is accurate, verify differences and identical aspects
- Access controls match your actual IAM configuration
- Data management reflects your compliance requirements

**Validation session setup:**
```
Documents to provide:
1. The generated EM (full document)
2. docs/specs/em-spec.md

Prompt:
"Validate this Environment Matrix against the EM spec.
Use only the spec as the source of truth for pass/fail criteria.
Do not suggest improvements. Judge only what is explicitly present.
Output JSON using the format defined in docs/validators/em-validator.md."
```


## Troubleshooting

**Validator returns FAIL on multiple gates**
Check that the generation session included all required inputs. Missing inputs are the most common cause of multi-gate failures.

**Decision statement flagged as vague**
Ensure the decision context provided to the generation session includes a specific decision. Not a question or exploration. the ai cannot make the decision; it can only document one.

**Alternatives section is thin**
The decision maker must provide the alternatives that were actually considered. If only one option was evaluated, the PDR will fail Gate 3. Go back to the decision maker for the alternatives.

**Scaling thresholds lack rationale**
Provide load testing data or historical performance data to the generation session. Without evidence, scaling thresholds are arbitrary and the ISPEC will note them as estimates.

**Parity assessment is generic**
Provide specific environment details to the generation session. Instance counts, database sizes, feature flag states, external service connections. generic input produces generic parity assessments.

**Data management is incomplete**
Ensure the team has answered: what data is in each environment? Is any of it production data? How is it refreshed? This information must come from the team: the AI cannot determine it.
