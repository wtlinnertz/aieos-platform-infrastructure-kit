# aieos-platform-infrastructure-kit

**Layer 12 of the AIEOS system — Platform & Infrastructure**

This kit governs infrastructure decisions, deployment targets, and environment management. It is a foundational kit that provides inputs to multiple pipeline layers — its artifacts are created early in an initiative and referenced throughout the delivery lifecycle.

---

## What This Kit Does

Infrastructure decisions, deployment specifications, and environment configurations are foundational inputs that shape architecture, release strategy, and operational health. Without governed infrastructure artifacts, these concerns are scattered across kits that consume them — creating duplication, inconsistency, and invisible assumptions. This kit governs the gap:

- **Technology decisions** — What platform, database, runtime, or service to use and why (with alternatives considered and tradeoffs accepted)
- **Infrastructure specifications** — What compute, storage, networking, and scaling rules support the architecture
- **Environment management** — What environments exist, how they differ, how code and config move between them, and who has access

---

## Artifact Types

This kit produces three governed artifact types:

| Step | Artifact | Purpose |
|------|----------|---------|
| 1 | Platform Decision Record (PDR) | Documents a specific technology or infrastructure decision with rationale, alternatives, and tradeoffs |
| 2 | Infrastructure Specification (ISPEC) | Defines deployment targets, resource requirements, scaling rules, and operational constraints |
| 3 | Environment Matrix (EM) | Defines all environments, promotion rules, parity requirements, and access controls |

Each governed artifact type has exactly four governing files: spec, template, prompt, validator.

---

## Quick Start

1. Read `docs/playbook.md` — the complete process definition
2. Read `docs/how-to-use-with-ai.md` — session setup and AI tool guidance
3. See `examples/` — worked examples

---

## Repository Structure

```
docs/
  principles/          # Organizational infrastructure policy (input material)
  specs/               # Content rules and hard gates per artifact type
  artifacts/           # Templates
  prompts/             # AI generation prompts
  validators/          # Quality gate definitions
  playbook.md          # End-to-end process definition
  index.md             # Documentation entry point
  how-to-adapt.md      # Organizational adoption guidance
  how-to-use-with-ai.md # AI tool usage guide
  governance-model.md  # AIEOS structural rules (reference)
examples/              # Worked examples
tests/
  kit-test-plan.md     # Structural integrity checks and flow scenarios
CLAUDE.md              # AI operating instructions
```

---

## AIEOS Layer

| Layer | Kit | Status |
|-------|-----|--------|
| 2. Product Intelligence | `aieos-product-intelligence-kit` | Built |
| 4. Engineering Execution | `aieos-engineering-execution-kit` | Built |
| 5. Release & Exposure | `aieos-release-exposure-kit` | Built |
| 6. Reliability & Resilience | `aieos-reliability-resilience-kit` | Built |
| 7. Insight & Evolution | `aieos-insight-evolution-kit` | Built |
| 8. Operational Diagnostics | `aieos-operational-diagnostics-kit` | Built |
| **12. Platform & Infrastructure** | **`aieos-platform-infrastructure-kit`** | **Built** |

See `aieos-governance-foundation/docs/layer-model.md` for the full layer model.
