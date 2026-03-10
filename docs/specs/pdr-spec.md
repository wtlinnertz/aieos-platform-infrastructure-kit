# Platform Decision Record — Specification

Version: v1.0

The Platform Decision Record (PDR) documents a single technology or infrastructure decision with its rationale, alternatives considered, and tradeoffs accepted. Each decision is captured in a separate PDR — decisions like "use PostgreSQL over DynamoDB," "deploy on ECS Fargate," or "adopt Terraform for IaC" each produce their own record.

PDRs are individual decision records. PDR IDs are sequential per project. A PDR is frozen once the decision is made and approved. If the decision is later reversed or superseded, a new PDR is issued referencing the original.

---

## What This Artifact Is Not

- **Not a technology evaluation report.** The PDR records the decision and its rationale — not an exhaustive survey of the technology landscape. Alternatives are documented to show the decision was informed, not to rank every option.
- **Not a procurement document.** The PDR captures the technical decision, not the commercial terms. Licensing costs and vendor contracts are referenced if they inform the decision but are not the primary content.
- **Not a design document.** The PDR states what was decided and why. How the decision is implemented belongs in the ACF, SAD, or ISPEC.

---

## Purpose

The PDR serves three roles:

1. **Decision record** — Captures the specific decision, who made it, and when, providing an auditable trail of infrastructure choices
2. **Rationale preservation** — Documents why the decision was made, what alternatives were considered, and what tradeoffs were accepted — preserving context that is otherwise lost over time
3. **Dependency anchor** — Provides a frozen reference that downstream artifacts (ACF, ISPEC, EM) can cite when they depend on a platform choice

---

## Upstream Dependencies

- Initiative context: problem statement, constraints, or opportunity that prompted the decision
- Organizational principles: `docs/principles/` files relevant to the decision domain (if applicable)

---

## Required Sections

1. Document Control
2. Decision Statement
3. Context
4. Alternatives Considered
5. Decision Rationale
6. Tradeoffs
7. Reversibility Assessment
8. Consequences

---

## Content Rules

### Document Control
**Rules**
- PDR ID must be present (format: PDR-{PROJECT}-{NNN})
- Decision title must be present (concise label for this decision)
- Decision authority must be present — the named individual who made the decision (not a team or committee; decisions have individual accountability)
- Date must be present (YYYY-MM-DD format)
- Status must be one of: Draft | Validated | Frozen | Superseded
- If status is Superseded, a reference to the superseding PDR must be present

**Failure Examples**
- PDR ID absent
- Decision authority listed as a team name ("Platform Team") rather than a named individual
- Date absent or in non-standard format
- Status is Superseded but no reference to the superseding PDR

### Decision Statement
**Rules**
- A single, clear, specific decision statement must be present
- The statement must be unambiguous — a reader must be able to determine exactly what was decided
- The statement must not be conditional ("If X, then we will use Y" is not a decision)

**Failure Examples**
- Decision statement absent
- Statement is vague ("We will use a modern database")
- Statement is conditional ("We will use PostgreSQL unless performance testing shows otherwise")
- Multiple decisions combined in one statement (each decision requires its own PDR)

### Context
**Rules**
- The problem, constraint, or opportunity that prompted the decision must be documented
- The context must explain why a decision was needed at this point — not just what the decision is about
- Any constraints that bounded the decision (timeline, budget, team expertise, regulatory, existing infrastructure) must be listed

**Failure Examples**
- Context section absent or empty
- Context describes the technology rather than the problem that prompted the decision
- No constraints listed (every decision has constraints; state them)

### Alternatives Considered
**Rules**
- At least 2 alternatives must be documented (including the chosen option)
- Each alternative must have:
  - A name or description
  - Pros (at least 1)
  - Cons (at least 1)
- The chosen alternative must be clearly identified
- "Do nothing" or "Status quo" counts as a valid alternative if it was genuinely considered

**Failure Examples**
- Fewer than 2 alternatives listed
- Alternatives listed without pros/cons
- Chosen alternative not identified
- Only the chosen option described in detail; others dismissed without analysis

### Decision Rationale
**Rules**
- The rationale must explain why the chosen alternative was selected over the others
- The rationale must reference specific pros/cons from the alternatives assessment — not introduce new criteria
- The rationale must not rely solely on authority ("the CTO decided") without substantive reasoning

**Failure Examples**
- Rationale absent
- Rationale introduces criteria not present in the alternatives assessment
- Rationale is "industry best practice" without specific reasoning for this context
- Rationale relies entirely on authority without technical or business reasoning

### Tradeoffs
**Rules**
- At least one explicit tradeoff must be stated
- Each tradeoff must identify what was given up or accepted as a consequence of the decision
- Tradeoffs must be specific, not generic ("some complexity" is not a tradeoff; "requires team to learn Terraform HCL, estimated 2-week ramp-up" is)
- The tradeoffs section must not merely restate the chosen option's cons — it must frame what was consciously accepted

**Failure Examples**
- Tradeoffs section absent
- Only benefits listed (every decision has tradeoffs)
- Tradeoffs are vague ("some additional complexity")
- Cons from the alternatives section repeated verbatim without framing as accepted consequences

### Reversibility Assessment
**Rules**
- The cost and feasibility of reversing this decision must be documented
- The assessment must classify the decision on a spectrum: easily reversible, reversible with moderate effort, difficult to reverse, or practically irreversible
- The basis for the classification must be stated (e.g., "data migration from PostgreSQL to DynamoDB would require schema redesign and estimated 4 weeks of engineering effort")
- If the decision is difficult to reverse or practically irreversible, the mitigation strategy must be stated (how to reduce lock-in or preserve optionality)

**Failure Examples**
- Reversibility section absent
- Assessment states "reversible" without basis or cost estimate
- Difficult-to-reverse decision documented without mitigation strategy
- Classification without supporting reasoning

### Consequences
**Rules**
- Downstream effects of the decision must be documented
- At minimum, the consequences section must identify which downstream artifacts are affected (ACF, ISPEC, EM, or others)
- Any action items triggered by the decision must be listed with owners

**Failure Examples**
- Consequences section absent
- No downstream artifacts identified
- Action items listed without owners

---

## Format Requirements

- Decision statement must be a single paragraph or sentence, not a bulleted list
- Alternatives must be structured consistently (same fields for each)
- Dates in YYYY-MM-DD format

---

## Completeness Rules

- All eight sections must be present and non-empty
- At least 2 alternatives with pros/cons each
- At least 1 explicit tradeoff
- Reversibility classification with basis

---

## Relationship Rules

- PDRs are individual records — one decision per PDR
- PDR IDs are sequential per project (PDR-{PROJECT}-001, PDR-{PROJECT}-002, etc.)
- A PDR may reference other PDRs if decisions are related
- A superseded PDR retains its frozen state with status changed to Superseded and a reference to the new PDR
- ISPEC and EM may reference frozen PDRs as the basis for platform choices

---

## Hard Gates

1. **decision_statement** — A clear, specific, non-conditional decision is stated; the statement is unambiguous
2. **context_documented** — The problem, constraint, or opportunity that prompted the decision is documented; constraints are listed
3. **alternatives_assessed** — At least 2 alternatives are documented, each with pros and cons; the chosen alternative is identified
4. **tradeoffs_stated** — At least one explicit tradeoff is stated with specificity; tradeoffs frame what was consciously accepted, not just cons restated
5. **decision_authority** — A named individual is identified as the decision maker (not a team or committee)
6. **reversibility_assessment** — Cost and feasibility of reversal are documented; classification is stated with basis; mitigation strategy present if difficult to reverse or practically irreversible
