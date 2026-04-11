# System Model Record — Specification

Version: v1.0

The System Model Record (SMR) captures the entity-relationship graph of running systems as a governed Markdown document. It provides a single source of truth for service inventory, inter-service dependencies, environment deployment mapping, and artifact version tracking.

---

## What This Artifact Is Not

- **Not an architecture document.** The SAD defines how the system should be built. The SMR documents what is actually deployed and how components relate at runtime.
- **Not a monitoring dashboard.** The SMR is a point-in-time snapshot of system topology, not a real-time view.
- **Not a deployment runbook.** The SMR describes what exists and how it connects; operational procedures live in the ORD and EM.

---

## Purpose

The SMR serves three roles:

1. **Service inventory** — Enumerates all services and components in the deployed system with their current versions, owners, and classifications
2. **Dependency mapping** — Documents the directed graph of inter-service dependencies with type, criticality, and protocol for each edge
3. **Environment context** — Maps which services are deployed to which environments and tracks artifact versions across environments

---

## Upstream Dependencies

- Frozen ISPEC (mandatory) — provides infrastructure baseline
- Frozen PDRs (informational) — provides technology decision context
- Frozen EM (informational) — provides environment definitions

---

## Required Sections

1. Document Control
2. Service Inventory
3. Dependency Graph
4. Environment Deployment Map
5. Artifact Version Matrix
6. Health Status Summary

---

## Content Rules

### Document Control
**Rules**
- SMR ID must be present (format: SMR-{PROJECT}-{NNN})
- System name must be present
- Owner must be named
- Version must be present (SMR is versioned; re-frozen when topology changes)
- Status must be one of: Draft, Validated, Frozen, Deprecated
- ISPEC reference must be present with Frozen status confirmed
- Spec Version and Principles Version must be present

**Failure Examples**
- Missing SMR ID
- No ISPEC reference
- ISPEC not confirmed as Frozen

### Service Inventory
**Rules**
- Every service or component in the system must have an entry
- Each entry must include: service name, service type (API, worker, database, cache, queue, gateway, etc.), current version or commit, owner (team or individual), criticality (critical, standard, auxiliary)
- No service may appear without a type and owner
- Services not yet deployed must be marked as such

**Failure Examples**
- Service listed without type
- Service listed without owner
- Known service omitted from inventory

### Dependency Graph
**Rules**
- Every inter-service dependency must be documented as a directed edge: From (caller) → To (callee)
- Each edge must include: dependency type (synchronous, asynchronous, data, configuration), criticality (critical, degraded, optional), protocol (HTTP, gRPC, message queue, database, file, etc.)
- Critical dependencies must have a fallback behavior noted (or explicit "no fallback")
- The graph must be acyclic for synchronous dependencies (circular sync dependencies are a finding, not a valid state)
- External dependencies (third-party APIs, SaaS services) must be included

**Failure Examples**
- Dependency listed without type or protocol
- Critical dependency without fallback notation
- Known dependency omitted

### Environment Deployment Map
**Rules**
- Every environment from the EM must be represented
- For each environment: list which services are deployed, at what version, and the deployment method
- Environment-specific configuration differences must be noted (not the values themselves, but which parameters differ)
- Environments where a service is not deployed must be explicitly noted

**Failure Examples**
- Environment from EM missing from the map
- Service deployed but not listed in the environment
- No deployment method noted

### Artifact Version Matrix
**Rules**
- A matrix showing service versions across all environments
- Each cell: service name × environment = version (or "not deployed")
- Version drift between environments must be flagged (e.g., staging has v2.3, production has v2.1)
- Last deployment date per environment per service must be included

**Failure Examples**
- Matrix incomplete (missing services or environments)
- Version drift not flagged
- No deployment dates

### Health Status Summary
**Rules**
- Current health status per service: healthy, degraded, down, unknown
- Status date must be present (when this status was last verified)
- For degraded or down services: brief description of the issue
- This section is a snapshot — it is valid as of the status date only

**Failure Examples**
- Service without health status
- Degraded service without issue description
- No status date

---

## Format Requirements

- SMR ID must follow the standard format
- All tables must use consistent column format
- Version references must be specific (commit SHA, semantic version, or build number — not "latest")
- Criticality must use standard values: critical, standard, auxiliary

---

## Completeness Rules

- All 6 sections non-empty
- Every service in the inventory appears in the dependency graph (as a node) and in the environment deployment map
- Every environment from the EM appears in the environment deployment map and version matrix
- No orphan services (in dependency graph but not in inventory)

---

## Relationship Rules

- The SMR is versioned. A new version is frozen when system topology changes (service added, removed, or dependency changed)
- The frozen SMR is referenced by downstream kits for system context
- The SMR does not replace the SAD (architecture intent) or the EM (environment policy) — it documents the actual deployed state

---

## Advisory Sections

### §7 Operational Notes (advisory)
- Known limitations of the current topology
- Planned topology changes
- Technical debt items related to dependencies

No hard gate evaluates this section. Its absence should be noted as a warning, not a failure.

---

## Hard Gates

| # | Gate | Rule |
|---|------|------|
| 1 | `entity_inventory` | Every service and component has a name, type, version, owner, and criticality classification. No service is listed without all required fields. |
| 2 | `relationship_completeness` | Every inter-service dependency is documented with direction, type, criticality, and protocol. No known dependency is omitted. Critical dependencies have fallback notation. |
| 3 | `environment_mapping` | Every environment from the EM is represented. Every service deployment is mapped to its environment with version and deployment method. |
| 4 | `version_tracking` | The artifact version matrix covers all services across all environments. Version drift is flagged. Deployment dates are present. |
| 5 | `dependency_direction` | All dependency edges are directed (From → To). Synchronous dependency cycles are identified as findings. External dependencies are included. |
