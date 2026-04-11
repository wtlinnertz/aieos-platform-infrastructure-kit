# Environment Matrix

---

## 1. Document Control

| Field | Value |
|-------|-------|
| EM ID | EM-{PROJECT}-{NNN} |
| System Name | {system name} |
| Owner | {team or role responsible for environment management} |
| Version | v{N} |
| Status | Draft / Validated / Freeze Pending / Frozen |
| Scope | {which system(s) or project(s) this EM governs} |
| ISPEC Reference | {frozen ISPEC ID and version} |
| Governance Model Version | 1.0 |
| Prompt Version | {prompt version} |
| Spec Version | {spec version} |
| Principles Version | {principles file versions} |

---

## 2. Environment Inventory

| Environment | Purpose | Owner | Access Endpoint |
|-------------|---------|-------|-----------------|
| {e.g., dev} | {why this environment exists} | {team or individual} | {URL or "not externally accessible"} |
| {e.g., staging} | {why this environment exists} | {team or individual} | {URL or "not externally accessible"} |
| {e.g., production} | {why this environment exists} | {team or individual} | {URL} |

*All environments must be listed. If it exists, it must be in this table. Minimum 2 environments required, including one production environment.*

---

## 3. Promotion Rules

### Promotion Path

```
{env 1} → {env 2} → {env 3} → ... → production
```

### Standard Promotion Steps

#### {Source Environment} → {Target Environment}

| Field | Value |
|-------|-------|
| Promotion Method | {Automated / Manual} |
| Gates Required | {tests, approvals, scans that must pass} |
| Authorization | {role or named individual who can authorize} |
| Rollback Procedure | {how to roll back if promotion fails} |

*Repeat for each promotion step in the path.*

### Emergency Promotion Path

| Field | Value |
|-------|-------|
| Path | {which steps can be skipped or expedited} |
| Authorization | {who can authorize emergency promotion} |
| Post-Emergency Requirements | {what must be done after emergency promotion — e.g., retroactive gate checks} |

*If emergency path is identical to standard, write: "Emergency promotion follows the standard path with no deviations."*

---

## 4. Parity Assessment

### {Environment A} vs. {Environment B}

**Identical Aspects:**
- {aspect that is identical between these environments}
- {aspect that is identical between these environments}

**Differences:**

| Aspect | {Environment A} | {Environment B} | Justification |
|--------|-----------------|-----------------|---------------|
| {e.g., instance count} | {value} | {value} | {why this difference exists} |
| {e.g., database size} | {value} | {value} | {why this difference exists} |

*Repeat for each pair of adjacent environments in the promotion path.*

---

## 5. Access Control

### {Environment Name}

| Role | Permission Level | Access Method |
|------|-----------------|---------------|
| {role} | {read-only / read-write / admin / deploy} | {how access is granted} |

**Access Grant/Revoke Process:** {process or tooling reference for managing access to this environment}

*Repeat for each environment. Production must have the most restrictive controls.*

### Shared / Service Accounts

| Account | Environment(s) | Purpose | Owner |
|---------|----------------|---------|-------|
| {account name} | {environments} | {purpose} | {responsible team} |

*If no shared accounts, write: "No shared or service accounts."*

---

## 6. Data Management

### {Environment Name}

| Field | Value |
|-------|-------|
| Data Source | {production data / synthetic data / subset of production / anonymized production / none} |
| Masking/Anonymization | {requirements if production data is used, or "N/A"} |
| Refresh Cadence | {how often data is refreshed — e.g., weekly, on-demand, never} |
| Retention Policy | {how long data is retained in this environment} |

*Repeat for each environment.*

### Test Data Strategy

{How is test data created, maintained, and cleaned up? Is there a shared test data set? Who owns test data quality?}
