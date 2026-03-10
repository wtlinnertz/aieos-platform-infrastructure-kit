# Infrastructure Specification — Specification

Version: v1.0

The Infrastructure Specification (ISPEC) defines the deployment targets, resource requirements, scaling rules, networking, and operational constraints for a system. It is the infrastructure counterpart to the ACF — while the ACF defines architecture guardrails, the ISPEC defines what infrastructure supports the architecture.

The ISPEC is **versioned and re-frozen** when infrastructure changes. When compute, storage, networking, scaling, or DR strategy changes materially, a new ISPEC version is issued; the previous version remains frozen.

---

## What This Artifact Is Not

- **Not an architecture document.** The ISPEC defines the infrastructure that supports the architecture — not the architecture itself. Service boundaries, data flow, and component responsibilities belong in the ACF or SAD.
- **Not a runbook.** The ISPEC defines what infrastructure exists and how it is configured. Operational procedures for managing that infrastructure belong in runbooks or operational guides.
- **Not a cost optimization plan.** The ISPEC includes cost estimates to ensure infrastructure decisions are economically informed. Ongoing cost optimization is an operational activity, not an ISPEC concern.

---

## Purpose

The ISPEC serves three roles:

1. **Infrastructure contract** — Defines the compute, storage, and networking resources required to run the system, enabling teams to provision and validate environments against a known baseline
2. **Scaling blueprint** — Documents auto-scaling triggers, limits, and rationale so that capacity management is governed rather than ad hoc
3. **Disaster recovery plan** — Captures RPO, RTO, and failover mechanisms so that resilience requirements are explicit and testable

---

## Upstream Dependencies

- Frozen Platform Decision Records (PDRs) — provide the technology choices that the ISPEC implements
- System requirements (functional and non-functional) — inform resource sizing and scaling
- Organizational principles: `docs/principles/` files relevant to infrastructure policy (if applicable)

---

## Required Sections

1. Document Control
2. Compute Specification
3. Storage Specification
4. Network Specification
5. Scaling Rules
6. Cost Estimate
7. Disaster Recovery

---

## Content Rules

### Document Control
**Rules**
- ISPEC ID must be present (format: ISPEC-{PROJECT}-{NNN})
- System name must be present
- Owner must be present (team or role responsible for the infrastructure)
- Version must be present (format: v{N}, e.g., v1, v2)
- Status must be one of: Draft | Validated | Frozen
- Scope must state which system(s) or service(s) this ISPEC governs
- PDR references must be listed (the frozen PDRs that inform this ISPEC's technology choices)

**Failure Examples**
- ISPEC ID absent
- Version absent or listed as "initial"
- Scope absent or "all systems"
- No PDR references listed (every ISPEC is informed by at least one technology decision)

### Compute Specification
**Rules**
- Compute resource type must be specified (e.g., EC2, ECS Fargate, Lambda, Kubernetes pods)
- Instance/container size or resource allocation must be specified (CPU, memory)
- Instance/container count or range must be specified (minimum, maximum, desired)
- Scaling triggers must be referenced (detailed in §5 Scaling Rules)
- Region/availability zone requirements must be stated

**Failure Examples**
- Compute type absent
- Size specified as "appropriate" rather than specific values
- Count absent or stated as "as needed"
- No region/AZ requirements

### Storage Specification
**Rules**
- Storage type must be specified per storage need (e.g., RDS PostgreSQL, S3, EBS, DynamoDB)
- Storage size must be specified with units (GB, TB)
- IOPS requirements must be stated if applicable (or explicitly stated as "not applicable" with reason)
- Backup policy must be stated: frequency, retention period, backup location
- Data classification must be stated (what kind of data is stored — PII, financial, logs, etc.)

**Failure Examples**
- Storage type absent
- Size specified without units
- Backup policy absent
- Data classification absent

### Network Specification
**Rules**
- VPC or network boundary design must be stated (CIDR ranges, subnet layout)
- Load balancing configuration must be stated (type, health check, routing rules)
- DNS requirements must be stated (domain names, record types, TTL)
- Firewall/security group rules must be stated (ingress/egress rules, port ranges)
- Inter-service communication method must be stated if multiple services exist (service mesh, direct, message queue)

**Failure Examples**
- No VPC/network design
- Load balancer referenced without configuration details
- DNS requirements absent
- Security group rules absent or "default"

### Scaling Rules
**Rules**
- Auto-scaling triggers must be defined with specific metric thresholds (e.g., "scale out when CPU > 70% for 5 minutes")
- Scale-out and scale-in rules must both be defined
- Minimum and maximum instance/container counts must be stated
- Cooldown period must be stated
- Rationale for threshold choices must be provided (why these specific values)

**Failure Examples**
- Only scale-out rules defined (no scale-in)
- Thresholds described qualitatively ("when load is high")
- No minimum/maximum counts
- No rationale for threshold values

### Cost Estimate
**Rules**
- Monthly estimated cost must be stated with currency
- Cost must be broken down by resource category (compute, storage, networking, other)
- Assumptions must be stated (utilization rate, data transfer volume, request volume)
- Cost at minimum scale and cost at maximum scale must both be stated
- Date of estimate must be stated (cloud pricing changes frequently)

**Failure Examples**
- No cost estimate
- Total cost without breakdown
- No assumptions stated
- Only one scale point estimated

### Disaster Recovery
**Rules**
- Recovery Point Objective (RPO) must be stated with time unit (how much data loss is acceptable)
- Recovery Time Objective (RTO) must be stated with time unit (how long until service is restored)
- Failover mechanism must be described (active-passive, active-active, cold standby, etc.)
- Backup restoration procedure must be referenced or described
- DR testing cadence must be stated (how often DR is tested)

**Failure Examples**
- RPO or RTO absent
- RPO/RTO stated without time units
- Failover mechanism absent or "manual intervention"
- No DR testing cadence

---

## Format Requirements

- All resource sizes must include units (vCPU, GB RAM, GB storage, IOPS)
- Cost estimates must include currency
- Time values must include units (minutes, hours, days)
- Scaling thresholds must be numeric with units (%, count, requests/second)

---

## Completeness Rules

- All seven sections must be present and non-empty
- Compute, storage, and networking all specified with concrete values
- Both scale-out and scale-in rules defined
- Cost estimate at both minimum and maximum scale
- RPO and RTO both stated with time units

---

## Relationship Rules

- The ISPEC is versioned — a new version is required when compute, storage, networking, scaling rules, or DR strategy change materially
- A frozen ISPEC may not be edited — changes require a new version via the ISPEC revision protocol (see playbook)
- The ISPEC references frozen PDRs for technology choices
- The EM references the frozen ISPEC for environment-specific resource configurations
- REK references the frozen ISPEC for deployment target specifications
- RRK references the frozen ISPEC for infrastructure monitoring baselines

---

## Advisory Sections

The following template sections are required by template structure but are evaluated qualitatively — no hard gate applies. Validators note absence but do not block freeze.

- **§8 Operational Notes** — Required in template structure. Validators note absence but do not block freeze. Content is evaluated qualitatively: operational notes should clarify known limitations, planned improvements, or operational caveats.

## Hard Gates

1. **compute_specification** — Compute resource type, size (CPU/memory), count/range, and region/AZ requirements are specified
2. **storage_specification** — Storage type, size with units, backup policy (frequency, retention, location), and data classification are specified; IOPS stated or explicitly marked not applicable
3. **network_specification** — VPC/network design, load balancing, DNS, and firewall/security group rules are specified
4. **scaling_rules** — Auto-scaling triggers with specific metric thresholds defined; both scale-out and scale-in rules present; min/max counts stated; cooldown period stated; rationale provided
5. **cost_estimate** — Monthly cost with currency, breakdown by category, assumptions stated, cost at both minimum and maximum scale, date of estimate present
6. **disaster_recovery** — RPO and RTO stated with time units; failover mechanism described; backup restoration referenced; DR testing cadence stated
