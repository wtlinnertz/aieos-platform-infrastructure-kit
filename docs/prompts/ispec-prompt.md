# Infrastructure Specification — Generation Prompt

You are generating an **Infrastructure Specification (ISPEC)** for the Platform & Infrastructure Kit. The ISPEC defines the deployment targets, resource requirements, scaling rules, networking, and operational constraints for a system.

---

## Your Role

You are a generation assistant. Your job is to produce a well-structured ISPEC that satisfies all hard gates defined in `docs/specs/ispec-spec.md`. You do not validate the result — that happens in a separate session.

---

## Inputs Required

Before generating, confirm you have all of the following:

1. **Frozen Platform Decision Records (PDRs)** — the technology decisions that the ISPEC implements
2. **System requirements** — functional and non-functional requirements that inform resource sizing
3. **`docs/specs/ispec-spec.md`** — the authoritative content rules and hard gates (use this, not memory)
4. **`docs/artifacts/ispec-template.md`** — the structure to follow exactly
5. **Organizational principles** (if applicable) — `docs/principles/` files relevant to infrastructure policy

If any of these inputs are missing or incomplete, do not proceed. State what is missing and stop.

---

## Generation Rules

### Structure
- Output pure Markdown.
- Use the template in `docs/artifacts/ispec-template.md` exactly — follow all sections and headings as written. Do not add sections. Do not remove sections. Do not reorder sections.
- The artifact must satisfy every hard gate in `docs/specs/ispec-spec.md`. Review each gate before finalizing.

### Content Rules
- Use the frozen PDRs as the authoritative source for technology choices. Do not contradict PDR decisions.
- Use system requirements to inform resource sizing, scaling thresholds, and DR targets.
- All resource values must include units (vCPU, GB, IOPS, seconds, etc.).
- All cost estimates must include currency.

### What You Must Not Do
- **Do not invent resource sizes.** If requirements do not provide enough information to size resources, mark as `[MISSING: resource sizing requires load testing data or capacity analysis]`.
- **Do not invent scaling thresholds.** If no performance data is available, mark thresholds as `[MISSING: scaling thresholds require load testing or historical data]` and provide placeholder values clearly labeled as estimates.
- **Do not expand scope.** The ISPEC governs only the systems identified in the input.
- **Do not use aspirational language.** All specifications must be concrete values.
- **Do not omit DR requirements.** Every system needs RPO, RTO, and failover strategy — even if the strategy is "cold restore from backup."

### Cost Estimation
- Provide cost estimates at both minimum and maximum scale.
- State all assumptions explicitly — utilization rate, data transfer volume, request volume.
- Include the date of the estimate so readers know when pricing was checked.
- If exact pricing is unavailable, use publicly available cloud provider pricing and note the source.

### Scaling Rationale
- Every scaling threshold must include a rationale. "Scale at 70% CPU" requires an explanation — was it determined by load testing, industry practice, or a specific requirement?
- If no rationale is available from the input, state `[MISSING: rationale for scaling threshold — must be confirmed by performance testing]`.

---

## Common Failure Modes

Avoid these patterns that cause validator failures:

| Pattern | Why It Fails | What to Do Instead |
|---------|-------------|-------------------|
| "Appropriate instance size" | Gate 1: no specific values | "2 vCPU, 4 GB RAM (t3.medium or equivalent)" |
| Storage size without units | Gate 2: missing units | "100 GB SSD" |
| "Default security groups" | Gate 3: no specific rules | List specific ingress/egress rules |
| "Scale when load is high" | Gate 4: qualitative threshold | "Scale out when CPU > 70% for 5 minutes" |
| Cost without breakdown | Gate 5: no category breakdown | Break down by compute, storage, networking |
| "Failover as needed" | Gate 6: no mechanism | "Active-passive with automated DNS failover" |
| Backup policy absent | Gate 2: missing backup | "Daily automated backups, 30-day retention, cross-region" |

---

## Output

Produce the complete ISPEC document following the template structure. Set status to `Draft`.

After generating, self-review against each gate in the spec:
- Gate 1: compute_specification — type, size (CPU/memory), count/range, region/AZ all present?
- Gate 2: storage_specification — type, size with units, IOPS, backup policy, data classification?
- Gate 3: network_specification — VPC design, load balancing, DNS, firewall rules?
- Gate 4: scaling_rules — triggers with thresholds, both scale-out and scale-in, min/max, cooldown, rationale?
- Gate 5: cost_estimate — monthly cost with currency, breakdown, assumptions, both scale points, date?
- Gate 6: disaster_recovery — RPO/RTO with units, failover mechanism, backup restoration, testing cadence?

If any gate would fail, revise before outputting the final document.
