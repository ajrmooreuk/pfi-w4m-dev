# CLAUDE.md — pfi-w4m-dev

## Project Overview

W4M PFI parent instance dev repo — the **PFC operator's own** PFI instance and originator of PFC (strategy-as-code, graph architecture). Part of the W4M triad (dev/test/prod). W4M is the root PFC-owner instance with full platform visibility, administrative access, and sub-instances: W4M-RCS (Azure/mid-market), W4M-WWG (LSC logistics), W4M-EOMS (sales-to-fulfilment).

**PFI Instance ID:** PFI-W4M
**PFI Class:** pfc-owner
**Parent Platform:** PFC (Azlan-EA-AAA)

## Directory Structure

```
.github/                    <- CI/CD workflows (doc naming, label validation, promotion)
PBS/                        <- Programme Breakdown Structure — strategy docs
  ├── ARCH/                 <- Architecture docs
  ├── Docs/                 <- General docs
  ├── ONTOLOGIES/           <- Instance ontologies
  ├── PFC-Tools/            <- PFC distributed tools
  ├── STRATEGY/             <- Strategy docs (naming convention enforced)
  └── programme/            <- Programme management docs
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

## W4M Sub-PFI Hierarchy

| Sub-PFI | Repo | Focus |
|---|---|---|
| **W4M-RCS** | `ajrmooreuk/pfi-w4m-rcs-dev` | Azure products, mid-market enterprise (AZA-ALZ HCR, WAF, CAF, Cyber) |
| **W4M-WWG** | `ajrmooreuk/pfi-w4m-wwg-dev` | World Wide Gourmet — LSC logistics, 4 supply corridors (AU/NZ/IS/IE) |
| **W4M-EOMS** | `ajrmooreuk/pfi-w4m-eoms-dev` | Endeavour Meats — sales-to-fulfilment (SOP→OFM→LSC), Next.js/Supabase |

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
| ARCH | Architecture, EA, solution architecture |
| DSY | Design system, skeleton, UI |
| VE | Value engineering, VSOM, OKR, PMF |
| PPM | Programme management |
| SEC | Security |
| CICD | CI/CD pipelines |
| OPS | Operations, platform admin |
| ONTL | Ontology library contributions |
| FAIR | FairSlice collaboration |

### Doc Types
`ARCH` | `OPS` | `REL` | `BRIEF` | `STD` | `PLAN` | `GUIDE` | `SPEC` | `TEST` | `IDX`

### Exempt Files
`README.md`, `CHANGELOG.md`, `CLAUDE.md`, `readme.md`, `LICENSE.md`

## Key Files

| File | Purpose |
|---|---|
| `instance-data/` | EMC instance config, DS-ONT brand tokens |
| `pfc-core/ontology-registry.json` | Sealed PFC ontology registry (DO NOT EDIT) |
| `promotion/promotion.env` | Promotion pipeline configuration |

## CI Enforcement

- `validate-doc-naming.yml` — blocks PRs with non-compliant new `.md` files in `PBS/STRATEGY/`
- PFC- prefix is blocked for new files in this repo (tier-aware enforcement)

## Promotion

- dev → test → prod via `promote.yml` (manual trigger)
- Config: `promotion/promotion.env`

## Related Repos

| Repo | Purpose |
|---|---|
| `ajrmooreuk/Azlan-EA-AAA` | Hub — source of truth for ontologies, workflows, standards |
| `ajrmooreuk/pfi-w4m-test` | W4M test triad |
| `ajrmooreuk/pfi-w4m-prod` | W4M prod triad |

## Status

- Active development
- PFC-owner root instance — full platform visibility
- W4M is the originator PFI — EA/Strategy-as-Code/Platform Architecture
- Sub-PFIs: W4M-RCS, W4M-WWG, W4M-EOMS
