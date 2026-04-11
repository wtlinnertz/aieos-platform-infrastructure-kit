# System Model Record

## §1 Document Control

| Field | Value |
|-------|-------|
| SMR ID | SMR-{PROJECT}-{NNN} |
| System Name | {system name} |
| Owner | {name or team} |
| Version | {vN.N} |
| Status | {Draft / Validated / Frozen / Deprecated} |
| Date | {YYYY-MM-DD} |
| ISPEC Reference | {ISPEC ID, confirmed Frozen} |
| PDR References | {PDR IDs, or "None"} |
| EM Reference | {EM ID, or "None"} |
| Governance Model Version | {version from governance-model.md} |
| Prompt Version | {version from smr-prompt.md} |
| Spec Version | {version from smr-spec.md} |
| Principles Version | {version from principles file, or N/A} |

---

## §2 Service Inventory

| Service Name | Type | Current Version | Owner | Criticality | Notes |
|-------------|------|----------------|-------|-------------|-------|
| {service name} | {API / worker / database / cache / queue / gateway / other} | {version or commit SHA} | {team or individual} | {critical / standard / auxiliary} | {optional notes} |

---

## §3 Dependency Graph

| # | From (Caller) | To (Callee) | Type | Criticality | Protocol | Fallback |
|---|--------------|-------------|------|-------------|----------|----------|
| 1 | {service A} | {service B} | {synchronous / asynchronous / data / configuration} | {critical / degraded / optional} | {HTTP / gRPC / message queue / database / file / etc.} | {fallback behavior or "no fallback"} |

### External Dependencies

| # | From | To (External) | Type | Criticality | Protocol | Fallback |
|---|------|--------------|------|-------------|----------|----------|
| 1 | {service} | {external service} | {type} | {criticality} | {protocol} | {fallback or "no fallback"} |

---

## §4 Environment Deployment Map

| Service | {Environment 1} | {Environment 2} | {Environment N} |
|---------|-----------------|-----------------|-----------------|
| {service name} | {version, deployment method} | {version, deployment method} | {version, deployment method or "not deployed"} |

### Configuration Differences

| Parameter Category | {Env 1} vs {Env 2} | Notes |
|-------------------|---------------------|-------|
| {category} | {what differs — not the values} | {why it differs} |

---

## §5 Artifact Version Matrix

| Service | {Environment 1} | {Environment 2} | {Environment N} | Drift? |
|---------|-----------------|-----------------|-----------------|--------|
| {service} | {version (date)} | {version (date)} | {version (date)} | {Yes/No} |

---

## §6 Health Status Summary

| Service | Status | Last Verified | Issue Description |
|---------|--------|--------------|-------------------|
| {service} | {healthy / degraded / down / unknown} | {YYYY-MM-DD HH:MM} | {description if degraded/down, or "—"} |

---

## §7 Operational Notes (Advisory)

### Known Limitations

{Known topology limitations, or "None identified."}

### Planned Changes

{Planned topology changes, or "None planned."}

### Technical Debt

{Dependency-related technical debt items, or "None identified."}

---

## Freeze Declaration

| Field | Value |
|-------|-------|
| Frozen By | {name and role} |
| Freeze Date | {YYYY-MM-DD} |
| Frozen Version | {vN.N} |
