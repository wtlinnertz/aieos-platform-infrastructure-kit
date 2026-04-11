# Entry from Engineering Execution Kit (EEK)

**You are here because:** You need to understand the relationship between the Platform & Infrastructure Kit (PIK) and the Engineering Execution Kit (EEK).

**Important note:** Unlike most kit boundaries, PIK is a foundational kit that primarily **provides inputs to** EEK, not the other way around. This entry document helps teams understand the bidirectional relationship.

---

## What PIK Provides to EEK

| Artifact | What EEK Consumes | Where EEK Uses It |
|----------|-------------------|-------------------|
| Frozen PDR(s) | Technology decisions with rationale | ACF §2 platform assumptions — technology choices are not re-decided in EEK |
| Frozen ISPEC | Deployment target specifications | Deployment model definition; infrastructure constraints for SAD |
| Frozen EM | Environment inventory and promotion rules | TDD environment-specific testing; deployment pipeline design |

**First artifact to check:** Frozen PDRs for the initiative — these establish the technology choices that EEK builds upon.

---

## What EEK May Provide Back to PIK

| Signal | What It Triggers |
|--------|-----------------|
| ACF reveals infrastructure gaps during architecture design | New PDR(s) for decisions not yet recorded; ISPEC revision if gaps affect resource specifications |
| TDD reveals environment parity issues during test design | EM revision to address parity gaps or undocumented differences |
| SAD identifies scaling requirements during detailed design | ISPEC revision if scaling rules need adjustment |

---

## What Changes at This Boundary

- PIK artifacts are typically created **before** EEK work begins. If you are starting EEK and no PIK artifacts exist, infrastructure decisions are being made implicitly — consider creating PDRs to make them explicit.
- PIK technology decisions constrain EEK architecture choices. The ACF should not contradict frozen PDRs.
- The EM defines what environments exist — EEK should not invent environments that are not in the EM.

---

## Common Mistakes at This Transition

| Mistake | How to Avoid |
|---------|--------------|
| Starting EEK without checking for frozen PDRs | Review whether technology decisions have been recorded. Create PDRs for any implicit decisions before they become embedded assumptions. |
| ACF making technology decisions that belong in PDRs | Technology selection decisions (database choice, cloud provider, IaC tool) should be PDRs. ACF documents architecture guardrails within those choices. |
| TDD defining environments that are not in the EM | The EM is the single source of truth for environments. TDD references the EM; it does not define new environments. |
| ISPEC and ACF diverging on infrastructure assumptions | Verify the frozen ISPEC is consistent with ACF infrastructure assumptions. If they diverge, resolve before freezing the ACF. |

---

## If PIK Artifacts Do Not Yet Exist

If the initiative has not yet produced PIK artifacts:

1. Identify implicit technology decisions that have already been made.
2. Create PDRs for each decision — even retroactively, the PDR captures the rationale.
3. Create an ISPEC from the PDRs and system requirements.
4. Create an EM from the ISPEC and environment information.
5. Freeze all PIK artifacts before beginning EEK artifact generation.

---

*For the full process, see `docs/playbook.md`.*
