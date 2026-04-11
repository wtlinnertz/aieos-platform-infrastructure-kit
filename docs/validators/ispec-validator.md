# Infrastructure Specification — Validator

This validator evaluates a completed Infrastructure Specification (ISPEC) against `docs/specs/ispec-spec.md`. It is used in a separate AI session from the one that generated the ISPEC.

**Validator role:** Judge pass/fail. Do not suggest improvements, redesign content, or offer alternatives. Evaluate only what is explicitly present.

---

## Inputs Required

To run this validator, provide:
1. The completed ISPEC (full document)
2. `docs/specs/ispec-spec.md` (the spec — use this as the authoritative rules)

Do not use any other document as the source of truth for pass/fail criteria.

---

## Evaluation Procedure

Evaluate each hard gate in order. For each gate, apply the rules exactly as stated in the spec. Do not infer intent. Do not give partial credit. Ambiguity in the artifact is a failure condition — if you cannot determine whether a gate passes from what is explicitly present, the gate fails.

---

## Hard Gate Checks

### Gate 1: compute_specification

Check:
- Compute resource type is specified (e.g., EC2, ECS Fargate, Lambda, Kubernetes pods)
- Instance/container size is specified with CPU and memory values including units
- Instance/container count or range is specified (minimum, maximum, desired)
- Region and availability zone requirements are stated

**Pass:** Compute type, size (CPU/memory with units), count/range, and region/AZ all present.
**Fail:** Any of type, size, count, or region/AZ absent; size without units; count as "as needed."

---

### Gate 2: storage_specification

Check:
- Storage type is specified per storage need
- Storage size is specified with units (GB, TB)
- IOPS requirements are stated or explicitly marked "not applicable" with reason
- Backup policy is stated: frequency, retention period, and backup location
- Data classification is stated (what kind of data is stored)

**Pass:** Type, size with units, IOPS (or explicit N/A), backup policy (frequency/retention/location), and data classification all present.
**Fail:** Any field absent; size without units; IOPS absent without explanation; backup policy missing any of frequency/retention/location; data classification absent.

---

### Gate 3: network_specification

Check:
- VPC or network boundary design is stated (CIDR ranges, subnet layout)
- Load balancing configuration is stated (type, health check, routing rules)
- DNS requirements are stated (domain names, record types, TTL)
- Firewall/security group rules are stated (ingress/egress rules, port ranges)

**Pass:** VPC design, load balancing, DNS, and firewall rules all present with specific values.
**Fail:** Any of the four areas absent; security groups stated as "default"; load balancer without configuration details.

---

### Gate 4: scaling_rules

Check:
- Auto-scaling triggers are defined with specific metric thresholds (numeric values, not qualitative)
- Both scale-out and scale-in rules are present
- Minimum and maximum instance/container counts are stated
- Cooldown period is stated
- Rationale for threshold choices is provided

**Pass:** Triggers with numeric thresholds, both scale directions, min/max counts, cooldown, and rationale all present.
**Fail:** Only scale-out defined; thresholds qualitative ("when load is high"); min/max absent; no cooldown; no rationale.

---

### Gate 5: cost_estimate

Check:
- Monthly estimated cost is stated with currency
- Cost is broken down by resource category (compute, storage, networking, other)
- Assumptions are stated (utilization rate, data transfer, request volume)
- Cost at minimum scale and cost at maximum scale are both stated
- Date of estimate is present

**Pass:** Monthly cost with currency, category breakdown, assumptions, both scale points, and date all present.
**Fail:** No cost estimate; total without breakdown; no assumptions; only one scale point; no date.

---

### Gate 6: disaster_recovery

Check:
- RPO is stated with time unit
- RTO is stated with time unit
- Failover mechanism is described (active-passive, active-active, cold standby, etc.)
- Backup restoration procedure is referenced or described
- DR testing cadence is stated

**Pass:** RPO and RTO with units, failover mechanism, backup restoration, and testing cadence all present.
**Fail:** RPO or RTO absent or without units; failover absent or "manual intervention" without detail; no backup restoration reference; no testing cadence.

---

## Output Format

Produce a JSON result in exactly this format:

```json
{
  "status": "PASS | FAIL",
  "summary": "<one sentence verdict>",
  "hard_gates": {
    "compute_specification": "PASS | FAIL",
    "storage_specification": "PASS | FAIL",
    "network_specification": "PASS | FAIL",
    "scaling_rules": "PASS | FAIL",
    "cost_estimate": "PASS | FAIL",
    "disaster_recovery": "PASS | FAIL"
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
- Do not redesign or improve the ISPEC
- Do not evaluate writing quality beyond spec requirements
- Do not accept qualitative thresholds as numeric
- Evaluate only what is explicitly present in the document
