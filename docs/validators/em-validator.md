# Environment Matrix — Validator

This validator evaluates a completed Environment Matrix (EM) against `docs/specs/em-spec.md`. It is used in a separate AI session from the one that generated the EM.

**Validator role:** Judge pass/fail. Do not suggest improvements, redesign content, or offer alternatives. Evaluate only what is explicitly present.

---

## Inputs Required

To run this validator, provide:
1. The completed EM (full document)
2. `docs/specs/em-spec.md` (the spec — use this as the authoritative rules)

Do not use any other document as the source of truth for pass/fail criteria.

---

## Evaluation Procedure

Evaluate each hard gate in order. For each gate, apply the rules exactly as stated in the spec. Do not infer intent. Do not give partial credit. Ambiguity in the artifact is a failure condition — if you cannot determine whether a gate passes from what is explicitly present, the gate fails.

---

## Hard Gate Checks

### Gate 1: environment_inventory

Check:
- All environments are listed (at least 2, including one production environment)
- Each environment has a name, purpose statement, owner, and access endpoint (or "not externally accessible")
- No environment is listed without a purpose

**Pass:** At least 2 environments including production; all have name, purpose, owner, and endpoint.
**Fail:** Fewer than 2 environments; no production environment; any environment missing name, purpose, owner, or endpoint.

---

### Gate 2: promotion_rules

Check:
- The promotion path is documented (sequence of environments)
- For each promotion step: source/target, automated/manual, gates, authorization, and rollback procedure are stated
- Emergency promotion path is documented (or explicitly stated as identical to standard)
- No promotion step lacks gates or authorization

**Pass:** Path documented; every step has method, gates, authorization, and rollback; emergency path present.
**Fail:** No promotion path; any step missing gates, authorization, or rollback; emergency path absent.

---

### Gate 3: parity_assessment

Check:
- For each pair of adjacent environments in the promotion path, differences are documented
- Each difference includes a justification
- Identical aspects between environments are explicitly confirmed (not just assumed)
- No differences exist without justification

**Pass:** All adjacent pairs assessed; differences justified; identical aspects confirmed.
**Fail:** Any adjacent pair not assessed; differences without justification; no confirmation of identical aspects; "similar to" language without specifics.

---

### Gate 4: access_control

Check:
- Access is documented for every environment
- Access is specified by role with permission level (read-only, read-write, admin, deploy)
- Access grant/revoke process is stated
- Production environment has the most restrictive access controls (or a justified exception is documented)
- Shared/service accounts are documented if they exist

**Pass:** Every environment has role-based access with levels; grant/revoke process stated; production most restrictive (or justified).
**Fail:** Any environment without access documentation; access described as "team has access" without roles/levels; no grant/revoke process; production access equal to or less restrictive than non-production without justification.

---

### Gate 5: data_management

Check:
- For each environment: data source, masking requirements (if applicable), refresh cadence, and retention policy are documented
- If production data is used in any non-production environment, masking/anonymization requirements are stated
- Test data strategy is documented (how test data is created, maintained, cleaned up)

**Pass:** Data source, masking (if applicable), refresh cadence, and retention per environment; test data strategy present.
**Fail:** Any environment without data management; production data in non-production without masking requirements; no refresh cadence; no retention policy; no test data strategy.

---

## Output Format

Produce a JSON result in exactly this format:

```json
{
  "status": "PASS | FAIL",
  "summary": "<one sentence verdict>",
  "hard_gates": {
    "environment_inventory": "PASS | FAIL",
    "promotion_rules": "PASS | FAIL",
    "parity_assessment": "PASS | FAIL",
    "access_control": "PASS | FAIL",
    "data_management": "PASS | FAIL"
  },
  "blocking_issues": [
    {
      "gate": "<gate_name>",
      "description": "<what specifically failed>",
      "location": "<section or field where the failure is>"
    }
  ],
  "warnings": [
    {
      "description": "<non-blocking observation>",
      "location": "<section>"
    }
  ],
  "completeness_score": "<0-100>"
}
```

**Interpretation rules:**
- Any gate failure means `"status": "FAIL"`
- `blocking_issues` lists exactly the failures — no additional content
- `warnings` are non-blocking; they do not affect status
- `completeness_score` is advisory; it does not override gate results
- If all gates pass, `blocking_issues` is an empty array

---

## Validator Constraints

- Do not suggest how to fix failures
- Do not redesign or improve the EM
- Do not evaluate writing quality beyond spec requirements
- Do not accept "similar to" language as a parity assessment
- Evaluate only what is explicitly present in the document
