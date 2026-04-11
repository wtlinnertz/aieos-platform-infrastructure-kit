# Platform Decision Record

---

## 1. Document Control

| Field | Value |
|-------|-------|
| PDR ID | PDR-{PROJECT}-{NNN} |
| Decision Title | {concise label for this decision} |
| Decision Authority | {named individual who made the decision} |
| Date | {YYYY-MM-DD} |
| Status | Draft / Validated / Frozen / Superseded |
| Superseded By | {PDR-{PROJECT}-{NNN} — only if status is Superseded} |
| Related PDRs | {list of related PDR IDs, or "None"} |
| Governance Model Version | 1.0 |
| Prompt Version | {prompt version} |
| Spec Version | {spec version} |
| Principles Version | {principles file versions} |

---

## 2. Decision Statement

{A single, clear, specific statement of the decision. Not conditional. Not multiple decisions. One decision, stated unambiguously.}

---

## 3. Context

**Problem / Constraint / Opportunity:**

{What prompted this decision? Why was a decision needed at this point?}

**Constraints:**

| Constraint | Description |
|------------|-------------|
| {constraint type} | {description} |
| {constraint type} | {description} |

---

## 4. Alternatives Considered

### Alternative 1: {Name} (Chosen)

| Aspect | Detail |
|--------|--------|
| Description | {what this alternative entails} |

**Pros:**
- {pro 1}
- {pro 2}

**Cons:**
- {con 1}
- {con 2}

### Alternative 2: {Name}

| Aspect | Detail |
|--------|--------|
| Description | {what this alternative entails} |

**Pros:**
- {pro 1}

**Cons:**
- {con 1}

*Add additional alternatives as needed. Minimum 2 required (including the chosen option).*

---

## 5. Decision Rationale

{Why was the chosen alternative selected over the others? Reference specific pros/cons from §4. Do not introduce new criteria here.}

---

## 6. Tradeoffs

| Tradeoff | What Was Accepted |
|----------|-------------------|
| {specific tradeoff} | {specific consequence that was consciously accepted} |

*At least one tradeoff required. Be specific — not "some complexity" but "requires team to learn X, estimated Y-week ramp-up."*

---

## 7. Reversibility Assessment

| Aspect | Assessment |
|--------|------------|
| Classification | {Easily reversible / Reversible with moderate effort / Difficult to reverse / Practically irreversible} |
| Basis | {Why this classification? What would reversal entail?} |
| Estimated Reversal Cost | {time, effort, or financial cost} |
| Mitigation Strategy | {How to reduce lock-in or preserve optionality — required if Difficult to reverse or Practically irreversible} |

---

## 8. Consequences

**Downstream Effects:**

| Artifact Affected | How It Is Affected |
|-------------------|--------------------|
| {artifact type and ID} | {description of impact} |

**Action Items:**

| Action | Owner | Target Date |
|--------|-------|-------------|
| {action triggered by this decision} | {named individual or team} | {YYYY-MM-DD} |

*If no action items, write: "No immediate action items. Decision is informational input to downstream artifacts."*
