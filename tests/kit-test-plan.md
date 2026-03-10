# Platform & Infrastructure Kit — Test Plan

This document contains the structural integrity checks and flow scenario tests for the Platform & Infrastructure Kit. These tests verify that the kit is complete, internally consistent, and capable of producing valid artifacts.

---

## Structural Integrity Checks

Structural checks verify that the kit's files are present, properly named, and internally consistent. These checks do not require AI — they are verifiable by inspection.

### S-01: Four-File Completeness

**Check:** Every governed artifact type has exactly four files: spec, template, prompt, validator.

| Artifact Type | Spec | Template | Prompt | Validator |
|---------------|------|----------|--------|-----------|
| PDR | docs/specs/pdr-spec.md | docs/artifacts/pdr-template.md | docs/prompts/pdr-prompt.md | docs/validators/pdr-validator.md |
| ISPEC | docs/specs/ispec-spec.md | docs/artifacts/ispec-template.md | docs/prompts/ispec-prompt.md | docs/validators/ispec-validator.md |
| EM | docs/specs/em-spec.md | docs/artifacts/em-template.md | docs/prompts/em-prompt.md | docs/validators/em-validator.md |

**Expected result:** All files present.

---

### S-02: Hard Gate Count Alignment

**Check:** Each spec's declared hard gate count matches the validator's gate checks.

| Artifact Type | Spec Hard Gates | Validator Gates |
|---------------|----------------|----------------|
| PDR | 6 | 6 |
| ISPEC | 6 | 6 |
| EM | 5 | 5 |

**Expected result:** Counts match for all three artifact types.

---

### S-03: Hard Gate Name Alignment

**Check:** Gate names in specs match gate names in validators (exact string match for JSON output field names).

| Artifact | Spec Gate Names | Validator Gate Names |
|----------|----------------|---------------------|
| PDR | decision_statement, context_documented, alternatives_assessed, tradeoffs_stated, decision_authority, reversibility_assessment | decision_statement, context_documented, alternatives_assessed, tradeoffs_stated, decision_authority, reversibility_assessment |
| ISPEC | compute_specification, storage_specification, network_specification, scaling_rules, cost_estimate, disaster_recovery | compute_specification, storage_specification, network_specification, scaling_rules, cost_estimate, disaster_recovery |
| EM | environment_inventory, promotion_rules, parity_assessment, access_control, data_management | environment_inventory, promotion_rules, parity_assessment, access_control, data_management |

**Expected result:** All gate names match exactly.

---

### S-04: Prompt-to-Spec Reference Integrity

**Check:** Each generation prompt references the correct spec and template. No prompt inlines content rules.

| Prompt | References Spec | References Template | Inlines Rules? |
|--------|----------------|--------------------|----|
| pdr-prompt.md | docs/specs/pdr-spec.md | docs/artifacts/pdr-template.md | No |
| ispec-prompt.md | docs/specs/ispec-spec.md | docs/artifacts/ispec-template.md | No |
| em-prompt.md | docs/specs/em-spec.md | docs/artifacts/em-template.md | No |

**Expected result:** All prompts reference correct spec and template; no inlined rules.

---

### S-05: Validator-to-Spec Reference Integrity

**Check:** Each validator references its spec as the source of truth. Validators do not reference prompts.

| Validator | References Spec | References Prompt? |
|-----------|-----------------|-------------------|
| pdr-validator.md | docs/specs/pdr-spec.md | No |
| ispec-validator.md | docs/specs/ispec-spec.md | No |
| em-validator.md | docs/specs/em-spec.md | No |

**Expected result:** All validators reference the correct spec; none reference prompts.

---

### S-06: Template Section Alignment

**Check:** Each template's section headings match the required sections listed in the corresponding spec.

| Artifact | Spec Required Sections | Template Sections |
|----------|----------------------|-------------------|
| PDR | Document Control, Decision Statement, Context, Alternatives Considered, Decision Rationale, Tradeoffs, Reversibility Assessment, Consequences | §1-§8 (all present) |
| ISPEC | Document Control, Compute Specification, Storage Specification, Network Specification, Scaling Rules, Cost Estimate, Disaster Recovery | §1-§7 (all present, plus §8 Operational Notes advisory) |
| EM | Document Control, Environment Inventory, Promotion Rules, Parity Assessment, Access Control, Data Management | §1-§6 (all present) |

**Expected result:** All template sections match spec required sections.

---

## Flow Scenario Tests

Flow scenarios verify that the kit's artifacts, when produced in order with appropriate inputs, pass validation. These tests require AI execution.

---

### F-00: Standard Infrastructure Flow

**Scenario:** Make 2 technology decisions (database and deployment platform) → generate PDRs → freeze → generate ISPEC → freeze → generate EM → freeze.

**Setup:**
- Provide: decision context for "Use PostgreSQL as primary database" and "Deploy on ECS Fargate"
- Generate PDR-TEST-001 and PDR-TEST-002 using the PDR prompt
- Validate each PDR → freeze
- Provide system requirements referencing both frozen PDRs
- Generate ISPEC-TEST-001 → validate → freeze
- Provide environment information (dev, staging, production)
- Generate EM-TEST-001 → validate → freeze

**Expected outcomes:**
- PDR-TEST-001: all 6 gates PASS
- PDR-TEST-002: all 6 gates PASS
- ISPEC-TEST-001: all 6 gates PASS; references both PDRs
- EM-TEST-001: all 5 gates PASS; references ISPEC-TEST-001

**Key gate to verify:** PDR Gate 5 (decision_authority) — confirm a named individual, not a team.

---

### F-01: ISPEC Revision After Scaling Change

**Scenario:** Service has been running for 3 months. Load testing reveals current scaling thresholds are too aggressive. Generate a revised ISPEC with updated scaling rules.

**Setup:**
- Use frozen ISPEC v1 (ISPEC-TEST-001 v1)
- Provide updated system requirements with new scaling data
- Generate ISPEC v2 using the ISPEC prompt with updated inputs
- Validate ISPEC v2 → freeze
- Generate EM v2 referencing ISPEC v2 (if environment resources changed)
- Validate EM v2

**Expected outcomes:**
- ISPEC v2: all 6 gates PASS; version is v2; scaling thresholds updated with new rationale; cost estimate updated
- EM v2: all 5 gates PASS; references ISPEC v2

**Key gate to verify:** ISPEC Gate 4 (scaling_rules) — confirm updated rationale references load testing data.

---

### F-02: PDR Supersession

**Scenario:** Original decision was "Use DynamoDB for primary storage." After 6 months, team decides to switch to PostgreSQL. Generate a superseding PDR and update downstream artifacts.

**Setup:**
- Use frozen PDR-TEST-003 (original DynamoDB decision)
- Generate PDR-TEST-004 (PostgreSQL decision) referencing PDR-TEST-003 in context
- Validate PDR-TEST-004 → freeze
- Update PDR-TEST-003 status to Superseded (non-material amendment)
- Generate new ISPEC version reflecting the database change
- Validate new ISPEC

**Expected outcomes:**
- PDR-TEST-004: all 6 gates PASS; context references PDR-TEST-003; DynamoDB listed as an alternative
- ISPEC new version: all 6 gates PASS; storage specification reflects PostgreSQL

**Key gate to verify:** PDR Gate 2 (context_documented) — confirm the context explains why the original decision is being superseded.

---

## Notes

- All structural checks (S-01 through S-06) should be verified before running flow scenarios.
- Flow scenarios F-00 through F-02 cover the three most common operational patterns.
- Additional scenarios may be added as new patterns are identified in production use.
