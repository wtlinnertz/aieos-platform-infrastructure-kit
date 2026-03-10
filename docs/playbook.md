# Platform & Infrastructure Kit — Playbook

This playbook defines the end-to-end process for the Platform & Infrastructure Kit (Layer 12). It covers how infrastructure decisions, deployment specifications, and environment management are governed — from early initiative planning through frozen artifacts that downstream kits consume.

---

## Artifact Flow

```
Initiative Planning / System Design
         │
         ▼
Step 1: Platform Decision Record(s) (PDR) — generated, one per decision
         │ Validate → Freeze (each independently)
         ▼
Step 2: Infrastructure Specification (ISPEC) — generated
         │ Validate → Freeze
         │ (versioned; re-frozen when infrastructure changes)
         ▼
Step 3: Environment Matrix (EM) — generated
         │ Validate → Freeze
         │ (versioned; re-frozen as environments evolve)
         ▼
Downstream consumption: EEK, REK, RRK
```

PDRs are independent — each decision gets its own PDR, and they can be frozen in any order. ISPEC depends on frozen PDRs. EM depends on frozen ISPEC.

---

## When to Create These Artifacts

This kit is **foundational** — its artifacts are created early in an initiative, before the engineering execution pipeline begins. The typical timing is:

| Artifact | When Created | Why at This Point |
|----------|-------------|-------------------|
| PDR | During initiative planning, as technology decisions are made | Decisions must be recorded before they become implicit assumptions |
| ISPEC | During system design (before or alongside ACF/SAD in EEK) | Infrastructure must be specified before deployment planning |
| EM | During project setup (before or alongside TDD in EEK) | Environments must be defined before environment-specific testing or deployment |

PDRs may also be created at any point during an initiative when a new technology or infrastructure decision arises. Each decision gets its own PDR regardless of timing.

---

## Step 1 — Platform Decision Record

**Artifact:** Platform Decision Record (PDR)
**Type:** Generated (one per decision)
**Spec:** `docs/specs/pdr-spec.md` (6 hard gates)
**Template:** `docs/artifacts/pdr-template.md`
**Prompt:** `docs/prompts/pdr-prompt.md`
**Validator:** `docs/validators/pdr-validator.md`

### Purpose

The PDR captures a single technology or infrastructure decision with its rationale, alternatives considered, and tradeoffs accepted. It provides an auditable trail of platform choices and preserves the context that informed them.

### Inputs
- Decision context from the decision maker (problem, constraints, opportunity)
- Alternatives considered with pros/cons
- Organizational principles (if applicable)

### Process

1. The decision maker identifies that a technology or infrastructure decision needs to be recorded.
2. In a new AI session, provide: decision context + alternatives + `docs/specs/pdr-spec.md` + `docs/artifacts/pdr-template.md` + relevant principles files. Use the PDR prompt (`docs/prompts/pdr-prompt.md`).
3. Review the generated PDR. Confirm the decision statement is accurate, alternatives are complete, and the decision authority is correctly identified.
4. Validate the PDR in a separate session using `docs/validators/pdr-validator.md` and the spec.
5. On PASS: decision authority reviews and freezes. **The PDR is now an immutable record.**
6. On FAIL: address blocking issues and re-generate or correct; re-validate.

### Freeze Points
- Each PDR is frozen independently
- Frozen PDRs are referenced by ISPEC and EM

### Sequential PDRs
PDR IDs are sequential per project: PDR-{PROJECT}-001, PDR-{PROJECT}-002, etc. Multiple PDRs can be in progress simultaneously — they do not depend on each other unless one decision explicitly depends on another.

---

## Step 2 — Infrastructure Specification

**Artifact:** Infrastructure Specification (ISPEC)
**Type:** Generated
**Spec:** `docs/specs/ispec-spec.md` (6 hard gates)
**Template:** `docs/artifacts/ispec-template.md`
**Prompt:** `docs/prompts/ispec-prompt.md`
**Validator:** `docs/validators/ispec-validator.md`

### Purpose

The ISPEC defines the compute, storage, networking, scaling, cost, and disaster recovery specifications for a system. It translates PDR technology decisions into concrete infrastructure requirements.

### Inputs
- Frozen PDRs (all technology decisions that inform this ISPEC)
- System requirements (functional and non-functional)
- Organizational principles (if applicable)

### Process

1. Ensure all relevant PDRs are frozen. The ISPEC must not reference unfrozen technology decisions.
2. In a new AI session, provide: frozen PDRs + system requirements + `docs/specs/ispec-spec.md` + `docs/artifacts/ispec-template.md` + relevant principles files. Use the ISPEC prompt (`docs/prompts/ispec-prompt.md`).
3. Review the generated ISPEC. Confirm resource sizes are realistic, scaling thresholds have rationale, and cost estimates are plausible.
4. Validate the ISPEC in a separate session using `docs/validators/ispec-validator.md` and the spec.
5. On PASS: infrastructure owner reviews and freezes. **The frozen ISPEC is now the active version.**
6. On FAIL: address blocking issues and re-generate or correct; re-validate.

### Freeze Points
- ISPEC must be frozen before EM generation begins
- ISPEC must be frozen before downstream kits reference it (EEK, REK, RRK)

### Reuse
The frozen ISPEC is reused until infrastructure changes materially. See the ISPEC Revision Protocol below.

---

## Step 3 — Environment Matrix

**Artifact:** Environment Matrix (EM)
**Type:** Generated
**Spec:** `docs/specs/em-spec.md` (5 hard gates)
**Template:** `docs/artifacts/em-template.md`
**Prompt:** `docs/prompts/em-prompt.md`
**Validator:** `docs/validators/em-validator.md`

### Purpose

The EM defines all environments, their promotion rules, parity requirements, access controls, and data management policies. It is the single source of truth for environment configuration — other kits reference the EM rather than defining environments themselves.

### Inputs
- Frozen ISPEC
- Environment information from the team
- Organizational principles (if applicable)

### Process

1. Ensure the ISPEC is frozen. The EM references the ISPEC for resource baselines.
2. In a new AI session, provide: frozen ISPEC + environment details + `docs/specs/em-spec.md` + `docs/artifacts/em-template.md` + relevant principles files. Use the EM prompt (`docs/prompts/em-prompt.md`).
3. Review the generated EM. Confirm all environments are documented, promotion rules are correct, and parity assessment is accurate.
4. Validate the EM in a separate session using `docs/validators/em-validator.md` and the spec.
5. On PASS: environment owner reviews and freezes. **The frozen EM is now the active version.**
6. On FAIL: address blocking issues and re-generate or correct; re-validate.

### Freeze Points
- EM must be frozen before downstream kits reference it (REK for promotion rules, RRK for monitoring scope)

---

## Downstream Handoff

### To EEK (Layer 4)
**Handoff artifacts:** Frozen PDRs, frozen ISPEC, frozen EM

- Frozen PDRs inform ACF platform assumptions (§2) — technology choices are not re-decided in EEK
- Frozen ISPEC informs the deployment model and infrastructure constraints
- Frozen EM informs TDD environment-specific testing (which environments exist, how they differ)

### To REK (Layer 5)
**Handoff artifacts:** Frozen ISPEC, frozen EM

- Frozen ISPEC provides deployment target specifications for the Release Plan
- Frozen EM provides environment promotion rules and gates for release strategy

### To RRK (Layer 6)
**Handoff artifacts:** Frozen ISPEC

- Frozen ISPEC provides infrastructure monitoring baseline for SRP
- DR specifications inform RRK incident management planning

---

## Upstream Inputs

### From RRK (Layer 6)
Infrastructure-related reliability data (RHR trends) may trigger ISPEC revision. When RHRs consistently identify infrastructure-related incidents or SLO violations, the ISPEC may need to be revised to address capacity, scaling, or DR gaps.

### From ODK (Layer 8)
Infrastructure-related PMR findings may trigger PDR reassessment or ISPEC revision. When a post-mortem identifies an infrastructure technology decision as a contributing factor, a new PDR may supersede the original decision.

---

## Freeze Points Summary

| Artifact | When Frozen | What It Gates |
|----------|------------|---------------|
| PDR | After validation and decision authority approval | ISPEC generation (for referenced decisions) |
| ISPEC | After validation and infrastructure owner approval | EM generation; EEK, REK, RRK consumption |
| EM | After validation and environment owner approval | REK promotion rules; RRK monitoring scope |

---

## ISPEC Revision Protocol

When compute, storage, networking, scaling rules, or DR strategy must change:

1. **Do not edit the frozen ISPEC.** The frozen ISPEC remains immutable.
2. The team identifies the trigger: reliability findings, scaling issues, cost optimization, new requirements, or organizational decision.
3. Generate a new ISPEC version (v2, v3, etc.) using the ISPEC prompt with updated inputs.
4. Validate and freeze the new ISPEC version.
5. Downstream artifacts that reference the ISPEC must be reviewed: EM may need a new version; REK and RRK references must be updated.
6. If the revision changes DR strategy, notify RRK — the SRP may need revision to reflect updated infrastructure baselines.

---

## EM Revision Protocol

When environments are added or removed, promotion rules change, or parity requirements shift:

1. **Do not edit the frozen EM.** The frozen EM remains immutable.
2. The team identifies the trigger: new environment needed, promotion rules updated, access control changes, or environment decommissioned.
3. Generate a new EM version (v2, v3, etc.) using the EM prompt with updated inputs.
4. Validate and freeze the new EM version.
5. Notify downstream consumers: REK release plans may need to reference the updated promotion rules; RRK monitoring scope may need adjustment.

---

## PDR Supersession Protocol

When a previous technology decision is reversed or superseded:

1. **Do not edit the original frozen PDR.** It remains immutable as a historical record.
2. Create a new PDR documenting the new decision. In the context section, reference the original PDR and explain why the decision is being changed.
3. The new PDR's alternatives section should include the original choice as one of the alternatives assessed.
4. Validate and freeze the new PDR.
5. Update the original PDR's status to `Superseded` and add a reference to the new PDR (non-material amendment — see Amendment Procedure).
6. Review downstream artifacts that reference the original PDR — ISPEC and EM may need new versions.

---

## Session Discipline

Follow these rules in every AI session:

| Rule | Why |
|------|-----|
| One artifact per session | Prevents context contamination across artifacts |
| Separate generation and validation sessions | Prevents self-validation bias |
| Include full frozen upstream artifacts | Do not summarize; the AI needs complete context |
| Include spec and template for generation sessions | The AI generates against the spec, not from memory |
| Include spec and validator for validation sessions | The validator judges against the spec, not against the generated content |

Do not ask the AI that generated an artifact to also validate it in the same session.

---

## Re-Entry Protocol

When a frozen artifact must be corrected:

1. Identify the artifact to be changed (PDR, ISPEC, or EM).
2. Identify all downstream artifacts that reference it or depend on it.
3. Assess the impact: does the change affect ISPEC assumptions? Does it change EM promotion rules? Does it affect downstream kit references?
4. Issue a new version of the artifact (or a superseding PDR).
5. Re-validate the new version.
6. Re-validate all affected downstream artifacts that reference the changed artifact.
7. Document the change in the new version's document control section with a reference to the original and the reason for the correction.

---

## Amendment Procedure

A frozen artifact may be corrected in place without re-validation when **all** of the following criteria are met:

1. The correction does not affect any field evaluated by a hard gate.
2. The correction does not change scope, decisions, owners, or technical specifications.
3. The correction does not affect any field referenced by a downstream artifact.

**Procedure:** Make the correction and add an Amendment Log entry to the artifact's Document Control section: date, what changed, materiality criterion cited, and who authorized the change. No re-validation is required.

**If there is any ambiguity** about whether a change is non-material, treat it as material and issue a new artifact version. The amendment path must not become a workaround for the version protocol.

---

## Principle File Revision

When a principle file in `docs/principles/` changes, use the change categories defined in `aieos-governance-foundation/docs/principle-file-standard.md`:

| Change Category | Version Bump | Re-Entry Impact |
|----------------|-------------|-----------------|
| **Minor** (clarification only) | `v_.x -> v_.x+1` | No re-entry required; already-frozen artifacts remain valid |
| **Significant** (new requirement or tightened constraint) | `v1.x -> v1.x+1` | Review artifacts generated after the change against updated principles; already-frozen artifacts are grandfathered |
| **Breaking** (removal or loosening) | `vN.x -> vN+1.0` | Requires owner authorization and documented business justification; re-entry may be warranted |

Every change to a principle file must bump the version field, even minor clarifications.

---

## Maintaining the Engagement Record

The Engagement Record (ER) is a project-level artifact that lives in the consuming project at `docs/engagement/er-{initiative}.md`. It spans all AIEOS layers and is maintained by each kit's operators as work passes through. The ER spec and format are defined in `aieos-governance-foundation/docs/engagement-record-spec.md`.

**PIK maintains the Layer 12 section of the ER.**

### What to Update During Infrastructure Governance

| Trigger | ER update |
|---------|-----------|
| PDR frozen | Add PDR ID to §5 artifact table |
| ISPEC frozen | Add ISPEC ID and version to §5 |
| ISPEC revised (new version) | Add new ISPEC version to §5; note revision date |
| EM frozen | Add EM ID and version to §5 |
| EM revised (new version) | Add new EM version to §5; note revision date |
| PDR superseded | Update original PDR status in §5; add superseding PDR |
