# Environment Matrix — Specification

Version: v1.0

The Environment Matrix (EM) defines all environments (development, staging, production, etc.), their purposes, promotion rules between them, and parity requirements. It establishes what environments exist, how they differ, and how code and configuration move between them.

The EM is **versioned and re-frozen** when environments evolve. When new environments are added, promotion rules change, or parity requirements shift, a new EM version is issued; the previous version remains frozen.

---

## What This Artifact Is Not

- **Not a deployment guide.** The EM defines what environments exist and how artifacts move between them. The mechanics of deployment (scripts, pipelines, commands) belong in the ORD or deployment documentation.
- **Not a configuration management plan.** The EM identifies what differs between environments. The actual configuration values belong in configuration management tooling or a separate configuration artifact.
- **Not an access control policy.** The EM documents who has access to each environment. The enforcement of access controls belongs in identity and access management tooling.

---

## Purpose

The EM serves three roles:

1. **Environment inventory** — Provides a single source of truth for all environments, their purposes, and their owners, preventing undocumented or shadow environments
2. **Promotion governance** — Defines how artifacts (code, configuration, data) move between environments with explicit gates and approval requirements
3. **Parity reference** — Documents where environments are identical and where they differ, with justification for every difference, enabling teams to reason about environment-specific behavior

---

## Upstream Dependencies

- Frozen Infrastructure Specification (ISPEC) — provides the resource baseline that environments implement
- Frozen Platform Decision Records (PDRs) — provide technology choices that affect environment design
- Organizational principles: `docs/principles/` files relevant to environment and deployment policy (if applicable)

---

## Required Sections

1. Document Control
2. Environment Inventory
3. Promotion Rules
4. Parity Assessment
5. Access Control
6. Data Management

---

## Content Rules

### Document Control
**Rules**
- EM ID must be present (format: EM-{PROJECT}-{NNN})
- System name must be present
- Owner must be present (team or role responsible for environment management)
- Version must be present (format: v{N}, e.g., v1, v2)
- Status must be one of: Draft | Validated | Frozen
- Scope must state which system(s) or project(s) this EM governs
- ISPEC reference must be listed (the frozen ISPEC that this EM implements)

**Failure Examples**
- EM ID absent
- Version absent or listed as "initial"
- Scope absent
- No ISPEC reference listed

### Environment Inventory
**Rules**
- All environments must be listed (at minimum: one non-production and one production environment)
- Each environment must have:
  - A name (e.g., dev, staging, production)
  - A purpose statement (why this environment exists)
  - An owner (team or individual responsible for the environment)
  - A URL or access endpoint (or "not externally accessible" if applicable)
- No undocumented environments are permitted — if it exists, it must be in the EM

**Failure Examples**
- Fewer than 2 environments listed
- Environment listed without purpose
- Environment listed without owner
- Production environment missing

### Promotion Rules
**Rules**
- The promotion path must be documented (the sequence of environments code passes through)
- For each promotion step, the following must be stated:
  - Source and target environment
  - Whether promotion is automated or manual
  - What gates must pass before promotion (tests, approvals, scans)
  - Who can authorize promotion (role or named individual)
- Rollback procedure must be stated for each promotion step
- Emergency promotion path must be documented (if different from standard)

**Failure Examples**
- No promotion path documented
- Promotion steps without gates
- No rollback procedure
- Authorization not specified for any step

### Parity Assessment
**Rules**
- For each pair of adjacent environments in the promotion path, documented differences must be listed
- Each difference must include:
  - What differs (e.g., instance count, database size, feature flags, external service connections)
  - Justification for the difference
- Aspects that are identical must be explicitly stated (confirming parity, not assuming it)
- Any differences without justification are flagged as parity gaps

**Failure Examples**
- Parity assessment absent
- Differences listed without justification
- Only differences documented (identical aspects must also be confirmed)
- "Staging is similar to production" without specific comparison

### Access Control
**Rules**
- For each environment, access control must be documented:
  - Who has access (by role, not just by name — roles survive personnel changes)
  - Permission level (read-only, read-write, admin, deploy)
  - How access is granted and revoked (process or tooling reference)
- Production environment must have the most restrictive access controls
- Any shared accounts or service accounts must be documented

**Failure Examples**
- Access control absent for any environment
- Production has the same access as development without justification
- No process for granting/revoking access
- Shared accounts present but undocumented

### Data Management
**Rules**
- For each environment, data management must be documented:
  - Data source (production data, synthetic data, subset of production, anonymized production)
  - If production data is used in non-production environments, data masking or anonymization requirements must be stated
  - Data refresh cadence must be stated (how often non-production data is updated)
  - Data retention policy must be stated per environment
- Test data strategy must be documented (how test data is created, maintained, and cleaned up)

**Failure Examples**
- Data management absent for any environment
- Production data used in non-production without masking requirements
- No data refresh cadence
- No test data strategy

---

## Format Requirements

- Environment names must be consistent throughout the document
- Promotion paths should be presented as a sequence or diagram
- Access levels must use a consistent taxonomy (read-only, read-write, admin, deploy or equivalent)

---

## Completeness Rules

- All six sections must be present and non-empty
- At least 2 environments listed with all required fields
- Promotion rules cover every step in the path
- Parity assessment covers every adjacent environment pair
- Access control covers every environment
- Data management covers every environment

---

## Relationship Rules

- The EM is versioned — a new version is required when environments are added or removed, promotion rules change, or parity requirements shift
- A frozen EM may not be edited — changes require a new version via the EM revision protocol (see playbook)
- The EM references the frozen ISPEC for resource baselines
- REK references the frozen EM for environment promotion rules in the release strategy
- RRK references the frozen EM for monitoring scope (which environments are monitored)
- Other kits reference the EM rather than defining environments themselves

---

## Hard Gates

1. **environment_inventory** — All environments listed with name, purpose, owner, and access endpoint; at least 2 environments present including one production environment
2. **promotion_rules** — Promotion path documented; each step has source/target, automated/manual designation, gates, authorization, and rollback procedure; emergency path documented
3. **parity_assessment** — Differences between adjacent environments documented with justification for each; identical aspects confirmed; no unjustified parity gaps
4. **access_control** — Access documented for every environment by role with permission level; access grant/revoke process stated; production has most restrictive controls (or justified exception)
5. **data_management** — Data source, masking requirements (if applicable), refresh cadence, and retention policy documented per environment; test data strategy documented
