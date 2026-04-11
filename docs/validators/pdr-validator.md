# Platform Decision Record — Validator

This validator evaluates a completed Platform Decision Record (PDR) against `docs/specs/pdr-spec.md`. It is used in a separate AI session from the one that generated the PDR.

**Validator role:** Judge pass/fail. Do not suggest improvements, redesign content, or offer alternatives. Evaluate only what is explicitly present.

---

## Inputs Required

To run this validator, provide:
1. The completed PDR (full document)
2. `docs/specs/pdr-spec.md` (the spec — use this as the authoritative rules)

Do not use any other document as the source of truth for pass/fail criteria.

---

## Evaluation Procedure

Evaluate each hard gate in order. For each gate, apply the rules exactly as stated in the spec. Do not infer intent. Do not give partial credit. Ambiguity in the artifact is a failure condition — if you cannot determine whether a gate passes from what is explicitly present, the gate fails.

---

## Hard Gate Checks

### Gate 1: decision_statement

Check:
- A single, clear, specific decision statement is present
- The statement is unambiguous — a reader can determine exactly what was decided
- The statement is not conditional ("If X, then Y" fails)
- The statement does not combine multiple decisions

**Pass:** A clear, specific, non-conditional, singular decision is stated.
**Fail:** Decision statement absent; statement is vague, conditional, or combines multiple decisions.

---

### Gate 2: context_documented

Check:
- The problem, constraint, or opportunity that prompted the decision is documented
- The context explains why a decision was needed at this point
- Constraints that bounded the decision are listed (every decision has constraints)

**Pass:** Problem/constraint/opportunity documented; constraints listed.
**Fail:** Context absent or empty; context describes technology rather than the problem; no constraints listed.

---

### Gate 3: alternatives_assessed

Check:
- At least 2 alternatives are documented (including the chosen option)
- Each alternative has a name or description, at least 1 pro, and at least 1 con
- The chosen alternative is clearly identified
- Alternatives beyond the chosen one have substantive analysis (not dismissed without evaluation)

**Pass:** At least 2 alternatives with pros/cons each; chosen identified; all substantively analyzed.
**Fail:** Fewer than 2 alternatives; any alternative missing pros or cons; chosen not identified; non-chosen alternatives dismissed without analysis.

---

### Gate 4: tradeoffs_stated

Check:
- At least one explicit tradeoff is stated
- Each tradeoff identifies what was given up or accepted
- Tradeoffs are specific, not generic ("some complexity" fails; "requires team to learn Terraform HCL, estimated 2-week ramp-up" passes)
- The section does not merely restate the chosen option's cons — it frames what was consciously accepted

**Pass:** At least 1 specific tradeoff stated; framed as conscious acceptance, not just repeated cons.
**Fail:** Tradeoffs absent; only benefits listed; tradeoffs vague; cons from alternatives section repeated verbatim without framing.

---

### Gate 5: decision_authority

Check:
- A named individual is identified as the decision maker
- The decision maker is not a team, committee, or role — it is a specific person
- The decision authority field is present and non-empty

**Pass:** Named individual present as decision authority.
**Fail:** Decision authority absent; listed as a team ("Platform Team"), committee ("Architecture Review Board"), or role ("CTO") without a specific person's name.

---

### Gate 6: reversibility_assessment

Check:
- Cost and feasibility of reversing the decision are documented
- Classification is stated: easily reversible, reversible with moderate effort, difficult to reverse, or practically irreversible
- Basis for the classification is provided (what reversal would entail)
- If classification is "difficult to reverse" or "practically irreversible," a mitigation strategy is present

**Pass:** Classification stated with basis; cost/feasibility documented; mitigation present if difficult or irreversible.
**Fail:** Reversibility absent; classification without basis; "reversible" stated without cost or effort estimate; difficult/irreversible without mitigation strategy.

---

## Output Format

Produce a JSON result in exactly this format:

```json
{
  "status": "PASS | FAIL",
  "summary": "<one sentence verdict>",
  "hard_gates": {
    "decision_statement": "PASS | FAIL",
    "context_documented": "PASS | FAIL",
    "alternatives_assessed": "PASS | FAIL",
    "tradeoffs_stated": "PASS | FAIL",
    "decision_authority": "PASS | FAIL",
    "reversibility_assessment": "PASS | FAIL"
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
- Do not redesign or improve the PDR
- Do not evaluate writing quality beyond spec requirements
- Do not accept vague tradeoffs as sufficient
- Evaluate only what is explicitly present in the document
