# CLAUDE.md — Platform & Infrastructure Kit

## What This Repository Is

This is the **Platform & Infrastructure Kit** — an AIEOS kit that governs infrastructure decisions, deployment targets, and environment management. It is Layer 12 of the AIEOS system. It is a foundational kit whose artifacts are created early in an initiative and referenced by downstream kits including Engineering Execution (Layer 4), Release & Exposure (Layer 5), and Reliability & Resilience (Layer 6).

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
tests/                 # Structural integrity checks
```

## Artifact Types

This kit produces three governed artifact types:

1. **Platform Decision Record (PDR)** — Documents a specific technology or infrastructure decision with rationale, alternatives considered, and tradeoffs accepted. Each decision is a separate PDR. PDR IDs are sequential per project.
2. **Infrastructure Specification (ISPEC)** — Defines deployment targets, resource requirements, scaling rules, networking, and operational constraints. Versioned and re-frozen when infrastructure changes.
3. **Environment Matrix (EM)** — Defines all environments, promotion rules between them, parity requirements, access controls, and data management policies. Versioned and re-frozen as environments evolve.

Each artifact type has exactly four governing files: spec, template, prompt, validator.

## Key Rules

- **Specs are the source of truth** — prompts and validators reference specs, never inline rules
- **Validators judge, they do not help** — no suggestions, no redesign
- **Freeze before promote** — PDRs must be frozen before ISPEC references them; ISPEC and EM must be frozen before downstream kits consume them
- **Separate generation and validation** — different AI sessions to prevent self-validation bias
- **No scope expansion** — downstream artifacts must not expand scope beyond upstream
- **No inferred information** — mark missing information explicitly, do not fill gaps
- **ISPEC and EM are versioned** — when infrastructure or environments change, issue a new version; do not edit a frozen artifact
- **Governance model sync** — `docs/governance-model.md` is a synchronized copy of `aieos-governance-foundation/governance-model.md` (canonical authority). Do not edit kit copy directly; update `aieos-governance-foundation` first, then sync all kit copies to match exactly. See governance-model.md §15 for versioning and change protocol.

## Artifact Flow

```
Step 1: Platform Decision Record(s) → generate per decision → validate → freeze
Step 2: Infrastructure Specification → generate from PDRs + requirements
        → validate → freeze (versioned; re-frozen when infrastructure changes)
Step 3: Environment Matrix → generate from ISPEC + organizational policy
        → validate → freeze (versioned; re-frozen as environments evolve)
```

PDRs are independent — each decision gets its own PDR. ISPEC references frozen PDRs. EM references frozen ISPEC.

## Boundary Contracts

- **Downstream (EEK, Layer 4):** Frozen PDRs inform ACF platform assumptions (§2). Frozen ISPEC informs deployment model. Frozen EM informs TDD environment-specific testing.
- **Downstream (REK, Layer 5):** Frozen ISPEC provides deployment target specs for Release Plan. Frozen EM provides environment promotion rules for release strategy.
- **Downstream (RRK, Layer 6):** Frozen ISPEC provides infrastructure monitoring baseline for SRP. DR specs inform RRK incident management.
- **Upstream (RRK, Layer 6):** Infrastructure-related reliability data (RHR trends) may trigger ISPEC revision.
- **Upstream (ODK, Layer 8):** Infrastructure-related PMR findings may trigger PDR reassessment or ISPEC revision.
- See `docs/entry-from-eek.md` for the primary boundary relationship with EEK.

## File Naming

| Type | Pattern |
|------|---------|
| Spec | `{type}-spec.md` |
| Template | `{type}-template.md` |
| Prompt | `{type}-prompt.md` |
| Validator | `{type}-validator.md` |

## When Working on This Kit

- Read the playbook (`docs/playbook.md`) for the full process definition
- Read the governance model (`docs/governance-model.md`) for structural rules
- Check `docs/how-to-use-with-ai.md` for session setup instructions
- Reference `examples/` for worked examples

## Building or Auditing AIEOS Kits

- `aieos-governance-foundation/docs/kit-structure-standard.md` — compliance checklist for building and auditing kits
- `aieos-governance-foundation/docs/philosophy.md` — design rationale for governance model decisions
- `aieos-governance-foundation/docs/layer-model.md` — layer model and kit registry

## Commit Message Style

Follow conventional commits: `docs: <description>`
