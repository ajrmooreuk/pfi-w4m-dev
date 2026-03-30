# CLAUDE.md — pfi-w4m-dev

## Project Overview

W4M PFI instance dev repo — the **PFC operator's own** PFI instance. Part of the W4M triad (dev/test/prod). W4M is the root PFC-owner instance with full platform visibility and administrative access.

**PFI Instance ID:** PFI-W4M
**PFI Class:** pfc-owner
**Parent Platform:** PFC (Azlan-EA-AAA)

## Directory Structure

```
.github/                    <- CI/CD workflows (doc naming, label validation, promotion)
PBS/                        <- Programme Breakdown Structure — strategy docs
azlan-github-workflow/      <- Skills distributed from PFC via pfc-release.yml
docs/                       <- Operational docs, guides
instance-data/              <- PFI instance configuration (EMC, DS-ONT, domain ontologies)
pfc-core/                   <- Sealed PFC core assets — DO NOT EDIT
pfc-docs/                   <- PFC-distributed documentation
promotion/                  <- Promotion config (dev → test → prod)
scripts/                    <- Utility scripts
supabase/                   <- Supabase migrations and seed data
tools/                      <- Local tooling
```

## Instance Ontologies

| Ontology | Version | Purpose |
|---|---|---|
| DS-ONT (W4M instance) | v1.0.0 | W4M brand design system tokens |

W4M is the platform operator — it inherits all PFC ontologies and has cross-PFI visibility.

## Naming Conventions

**Mandatory for all new `.md` files in `PBS/STRATEGY/`:**
```
PFI-W4M-[PRODUCT]-[DOC-TYPE]-<Subject>-v<version>.md
```

### Tier
- **PFI-W4M** — this is a PFI repo. `PFC-` prefix is BLOCKED for new files.

### Product Codes
| Code | Domain |
|---|---|
| STRAT | General strategy |
| ARCH | Architecture, ontology |
| DSY | Design system, skeleton, UI |
| VE | Value engineering |
| SEC | Security |
| CICD | CI/CD pipelines |
| OPS | Operations, platform admin |

### Doc Types
`ARCH` | `OPS` | `REL` | `BRIEF` | `STD` | `PLAN` | `GUIDE` | `SPEC` | `TEST` | `IDX`

## Key Files

| File | Purpose |
|---|---|
| `instance-data/` | EMC instance config, DS-ONT brand tokens |
| `pfc-core/ontology-registry.json` | Sealed PFC ontology registry (DO NOT EDIT) |
| `promotion/promotion.env` | Promotion pipeline configuration |

## CI/CD

- `validate-doc-naming.yml` — blocks PRs with non-compliant `.md` files
- PFC- prefix is blocked (tier-aware enforcement)
- Promotion: dev → test → prod via `promote.yml` (manual trigger)

## Related Repos

| Repo | Purpose |
|---|---|
| `ajrmooreuk/Azlan-EA-AAA` | Hub — source of truth for ontologies, workflows, standards |
| `ajrmooreuk/pfi-w4m-test` | W4M test triad |
| `ajrmooreuk/pfi-w4m-prod` | W4M prod triad |

## Status

- Active development
- PFC-owner root instance — full platform visibility
