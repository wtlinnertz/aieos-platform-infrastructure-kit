# How to Adapt This Kit

This kit provides the structure, rules, and prompts for infrastructure decision governance, deployment specification, and environment management. Adapting it to your organization means filling in the content that is specific to your context — without modifying the governance structure.

---

## What to Adapt

### Organizational Principles

**Directory:** `docs/principles/`

Create principle files that define your organization's infrastructure philosophy. Adapt them to reflect:

- **Technology selection criteria**: Does your organization prioritize managed services over self-hosted? Cloud-native over portable? Single-cloud over multi-cloud?
- **Infrastructure standards**: What compute, storage, and networking standards does your organization maintain? Are there approved technology lists?
- **Environment policy**: How many environments does your organization require? Are there compliance requirements for environment isolation?
- **Disaster recovery requirements**: What RPO/RTO standards does your organization enforce by service tier?
- **Cost governance**: Are there budget thresholds that require additional approval? Cost optimization policies?

### PDR Decision Authority

The PDR spec requires a named individual as decision authority. If your organization uses a different model (e.g., architecture review boards), you may need to identify the accountable individual within that process. The PDR records who made the decision, not who was consulted.

### ISPEC Resource Sizing

The ISPEC template includes common resource categories (compute, storage, networking). If your organization uses additional resource categories (e.g., GPU compute, specialized hardware, edge locations), add rows to the template tables. Do not remove existing categories.

### EM Environment Count

The EM spec requires at least 2 environments (one non-production, one production). If your organization has more environments (e.g., dev, test, staging, pre-prod, production), include all of them. The EM must document every environment that exists.

---

## What Not to Adapt

### Specs

The specs define what makes an artifact valid. Do not soften hard gates to make artifacts easier to produce. If a hard gate is failing consistently, that usually means the artifact is incomplete — not that the gate is wrong.

If you genuinely need to add a hard gate (your organization requires something the spec does not check), add it. Do not remove existing hard gates.

### Validator Logic

Validators evaluate against specs. If a validator is producing unexpected results, check whether the spec accurately captures your requirements — and adjust the spec if needed, not the validator.

### Governance Model

`docs/governance-model.md` is a synchronized copy of the canonical governance model. Do not edit it. If you believe the governance model should change, update `aieos-governance-foundation/governance-model.md` and sync all kit copies.

---

## Adding Artifact Types

If your organization needs additional governed artifacts (e.g., a capacity planning record, a network security assessment), follow the four-file system:

1. Write the spec first — define the hard gates before writing anything else
2. Write the validator — this forces you to verify the spec is evaluable
3. Write the template — structure only, no content rules
4. Write the prompt — generation behavior, references spec and template

Register the new artifact type in the playbook, index, and CLAUDE.md.

---

## Tool Bindings

This kit is tool-agnostic. Templates use generic placeholders for cloud provider services, monitoring systems, and access management tools.

When adopting this kit, create a bindings document:

```
docs/bindings/
  cloud-provider-mapping.md    # Maps ISPEC resource types to your cloud provider services
  iac-tool-mapping.md          # Maps infrastructure management to your IaC tooling
  access-management-mapping.md # Maps EM access control to your IAM system
```

Bindings are not governed artifacts — they have no spec, validator, or prompt. Update them when your tooling changes without touching the governed files.

---

## Scaling to Multiple Systems

This kit governs infrastructure per system or service. For organizations with many systems:

1. Each system gets its own ISPEC and EM (with appropriate version IDs).
2. PDRs may be shared across systems if the decision applies broadly (e.g., "adopt Terraform for all infrastructure").
3. Shared PDRs are referenced by each system's ISPEC.

---

## First-Time Setup Checklist

- [ ] Read `docs/playbook.md` fully before beginning
- [ ] Create organizational principle files in `docs/principles/` if applicable
- [ ] Identify pending technology decisions that need PDRs
- [ ] Generate and freeze PDRs for all current technology decisions
- [ ] Generate and freeze the ISPEC using frozen PDRs as input
- [ ] Generate and freeze the EM using the frozen ISPEC as input
- [ ] Confirm downstream kits (EEK, REK, RRK) reference the frozen PIK artifacts
