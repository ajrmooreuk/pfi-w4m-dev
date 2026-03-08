# PFC-PBS: Document Controls & Naming Convention — Standard

**Product Code:** PFC-PBS (Platform Build System)
**Version:** 1.0.0
**Date:** 2026-03-07
**Status:** Mandatory
**Scope:** All repos — Azlan-EA-AAA, PFI triads, azlan-github-workflow
**Enforcement:** GitHub Actions (`validate-doc-naming.yml` — planned)

---

## 1. Purpose

Establish a mandatory naming convention for all platform documentation so that any document's **tier**, **product**, **type**, and **version** are immediately identifiable from the filename alone — without opening the file.

This standard applies across:

- **Azlan-EA-AAA** (PFC core monorepo)
- **PFI triad repos** (dev/test/prod for each instance)
- **azlan-github-workflow** (skills, plugins, CI/CD)
- **GitHub Actions workflows**

---

## 2. Filename Pattern

```text
[TIER]-[PRODUCT]-[DOC-TYPE]-<Subject>-v<MAJOR.MINOR.PATCH>.md
```

| Segment | Required | Description | Example |
|---------|----------|-------------|---------|
| `TIER` | Yes | Platform hierarchy tier | `PFC`, `PFI-BAIV`, `PRD-AIVA` |
| `PRODUCT` | Yes | Product/component code (see Section 4) | `SKBLD`, `VIS`, `REG` |
| `DOC-TYPE` | Yes | Document type code (see Section 5) | `ARCH`, `OPS`, `REL`, `BRIEF` |
| `Subject` | Yes | Hyphen-separated descriptive subject | `Skill-Building-Capability` |
| `v<version>` | Yes for versioned docs | Semantic version | `v1.0.0`, `v2.1.0` |

### Examples

```text
PFC-SKBLD-ARCH-Skill-Building-Capability-v1.0.0.md
PFC-SKBLD-OPS-Skill-Building-Capability-v1.0.0.md
PFC-SKBLD-REL-Skill-Building-Capability-v1.0.0.md
PFC-VIS-ARCH-Ontology-Visualiser-v4.5.0.md
PFC-VIS-OPS-Ontology-Visualiser-v4.5.0.md
PFC-VIS-REL-Epic-40-Graphing-Workbench-v1.0.0.md
PFC-REG-ARCH-Unified-Registry-v1.0.0.md
PFC-CICD-OPS-Hub-and-Spoke-Pipeline-v1.0.0.md
PFC-PBS-STD-Document-Controls-Naming-Convention-v1.0.0.md
PFI-BAIV-VE-BRIEF-Discovery-Agent-Strategy-v1.0.0.md
PFI-WWG-LSC-OPS-Supply-Chain-Corridors-v1.0.0.md
PFI-AIRL-GRC-BRIEF-CAF-Compliance-Strategy-v1.0.0.md
PRD-AIVA-APP-ARCH-AI-Visibility-Audit-v1.0.0.md
```

---

## 3. Tier Codes

The tier identifies where in the platform cascade the document belongs.

| Tier Code | Full Name | Scope | Repos |
|-----------|-----------|-------|-------|
| `PFC` | Platform Foundation Core | Shared platform infrastructure | Azlan-EA-AAA |
| `PFI-BAIV` | PFI Instance — BAIV | BAIV MarTech instance | pfi-baiv-aiv-{dev,test,prod} |
| `PFI-WWG` | PFI Instance — W4M-WWG | W4M specialist food importer | pfi-w4m-wwg-{dev,test,prod} |
| `PFI-EOMS` | PFI Instance — W4M-EOMS | W4M enterprise ops | pfi-w4m-eoms-{dev,test,prod} |
| `PFI-W4M` | PFI Instance — W4M | W4M SaaS/EA (legacy) | pfi-w4m-{dev,test,prod} |
| `PFI-AIRL` | PFI Instance — AIRL | Azure AI Readiness | pfi-airl-caf-aza-{dev,test,prod} |
| `PRD-<code>` | Product | Named product within a PFI | Per product |
| `APP-<code>` | Application | Named application within a product | Per app |

### Tier Cascade

```text
PFC (platform-wide)
  +-- PFI-<instance> (instance-specific)
       +-- PRD-<product> (product-specific)
            +-- APP-<app> (application-specific)
```

Documents at a higher tier apply to all lower tiers unless overridden.

---

## 4. Product Codes

Product codes identify the subsystem or component the document relates to.

### PFC-Level Product Codes

| Code | Product/Component | Key Modules |
|------|-------------------|-------------|
| `PBS` | Platform Build System | Standards, conventions, cross-cutting governance |
| `SKBLD` | Skill Builder | Dtree engine, skill scaffolding, skills register |
| `VIS` | Ontology Visualiser | browser-viewer, graph rendering, all view modes |
| `REG` | Unified Registry | ont-registry-index, skills-register-index, JSON-LD entries |
| `EMC` | EMC Composition Engine | emc-composer, category compositions, PFI constraint |
| `SKE` | App Skeleton | pfc-app-skeleton, zone allocator, nav layers |
| `DSY` | Design System | DS-ONT, tokens, cascade, design director |
| `CICD` | CI/CD Pipeline | GitHub Actions, promotion, sealed skills, triads |
| `SUPP` | Supabase Platform | Schema, RLS, JSONB graph, migrations |
| `ONTL` | Ontology Library | ont-registry-index, ontology JSON-LD files, OAA |
| `STRAT` | Strategy & Briefings | VSOM, VE skill chain, platform strategy |
| `AGNT` | Agent Architecture | Agent templates, agent manager, orchestration |
| `GRC` | GRC Framework | GRC-FW-ONT, compliance, governance |
| `URG` | Unified Registry Graph | PFC-ONT ecosystem ontology, registry cascade, context assistant |
| `FAIR` | FairSlice | Royalty, licensing, PFI-to-PFI distribution |

### PFI-Level Product Codes

PFI instances inherit all PFC product codes and may add instance-specific ones:

| Code | Product/Component | Instance |
|------|-------------------|----------|
| `VE` | Value Engineering | All — VE skill chain outputs |
| `LSC` | Logistics & Supply Chain | PFI-WWG |
| `OFM` | Operating & Fulfilment Model | PFI-WWG, PFI-BAIV |
| `DISC` | Discovery Agent | PFI-BAIV |
| `CAF` | CAF Compliance | PFI-AIRL |
| `MKTG` | MarTech / Marketing | PFI-BAIV |

### Adding New Product Codes

When a new subsystem is created:

1. Choose a 2-6 character uppercase code (no hyphens within the code)
2. Add it to the table in this document
3. Use it immediately in all new documents for that subsystem

---

## 5. Document Type Codes

| Code | Type | Description | Example Subject |
|------|------|-------------|-----------------|
| `ARCH` | Architecture | Technical architecture, module design, data models | `Skill-Building-Capability` |
| `OPS` | Operating Guide | How to use, day-to-day operations, runbooks | `Ontology-Visualiser` |
| `REL` | Release Bulletin | What's new, changelog, breaking changes | `Epic-40-Graphing-Workbench` |
| `BRIEF` | Strategy Briefing | Strategic analysis, proposals, business context | `PFI-Instance-Readiness` |
| `PLAN` | Plan | Implementation plans, roadmaps, phase plans | `Supabase-JSONB-MVP` |
| `STD` | Standard | Mandatory conventions, rules, governance | `Document-Controls-Naming-Convention` |
| `TEST` | Test Plan | Test strategy, test cases, validation criteria | `Epic-40-Graphing-Workbench` |
| `SPEC` | Specification | Formal specifications, schemas, contracts | `App-Skeleton-Framework` |
| `PROP` | Proposal | Decision proposals requiring approval | `Supabase-Secure-Connections` |
| `IDX` | Index / Catalogue | Catalogues, manifests, registers | `Strategy-Briefings` |
| `RPT` | Report | Status reports, audit reports, assessments | `Portfolio-Status` |
| `GUIDE` | Guide / Tutorial | Learning-oriented guides, walkthroughs | `SME-Test-Guide` |
| `DEPREQ` | Deploy Request | Deployment requests, release approvals | `F31.9-Sealed-Skills` |
| `LOG` | Audit Log | Chronological records, change logs | `Release-Audit-Log` |

---

## 6. Document Header — Mandatory Fields

Every document must include these fields in its header block:

```markdown
# [TIER]-[PRODUCT]: <Title>

**Product Code:** [TIER]-[PRODUCT] (<Full Product Name>)
**Version:** <MAJOR.MINOR.PATCH>
**Date:** <YYYY-MM-DD>
**Status:** <Draft | Active | Superseded | Deprecated>
```

### Optional Header Fields

```markdown
**Scope:** <What this document covers>
**Audience:** <Who should read this>
**Parent Epic:** Epic N (#issue)
**Feature:** FN.x (#issue)
**Prerequisite:** <Other docs to read first>
**Supersedes:** <Previous document filename>
```

### Example

```markdown
# PFC-SKBLD: Skill Building Capability — Architecture

**Product Code:** PFC-SKBLD (Skill Builder)
**Version:** 1.0.0
**Date:** 2026-03-07
**Status:** Active
**Scope:** Platform Foundation Core (PFC) — Agents, Skills, Plugins
**Parent Epic:** Epic 40 (#577)
```

---

## 7. Version Scheme

All versioned documents follow semantic versioning:

| Change Type | Bump | Example |
|-------------|------|---------|
| Breaking structure change, major rewrite | MAJOR | v1.0.0 -> v2.0.0 |
| New section, significant addition | MINOR | v1.0.0 -> v1.1.0 |
| Corrections, clarifications, typos | PATCH | v1.0.0 -> v1.0.1 |

### Unversioned Documents

Some document types may omit the version suffix:

- `RPT` (status reports) — use date in subject instead: `PFC-PBS-RPT-Portfolio-Status-2026-03-07.md`
- `LOG` (audit logs) — append continuously, no version: `PFC-CICD-LOG-Release-Audit.md`
- `IDX` (indices) — version in file content, not filename: `PFC-STRAT-IDX-Strategy-Briefings.md`

---

## 8. Directory Placement

| Tier | Doc Types | Directory |
|------|-----------|-----------|
| PFC | ARCH, OPS, REL, BRIEF, PLAN, STD, SPEC, PROP | `PBS/STRATEGY/` |
| PFC | ARCH (CI/CD specific) | `PBS/ARCHITECTURE/arch-cicd/` |
| PFC | ARCH, OPS, REL (Visualiser specific) | `PBS/TOOLS/ontology-visualiser/` |
| PFC | ARCH, OPS, REL (Skill family) | `azlan-github-workflow/skills/` |
| PFI | All | `PBS/PFI-<instance>/` or PFI triad repo root |
| PRD | All | Within PFI scope |

Documents should live as close to the code they describe as possible. Platform-wide docs go in `PBS/STRATEGY/`.

---

## 9. Migration Path for Existing Documents

There are currently **83 documents** in `PBS/STRATEGY/` using inconsistent prefixes (`BRIEFING-`, `PLAN-`, `ARCH-`, `STATUS-`, etc.). Migration is **not retrospective** — existing documents keep their current names. The convention applies to:

1. **All new documents** created from 2026-03-07 onward
2. **Documents being significantly revised** (major version bump) — rename at that point
3. **Documents referenced in new features** — rename when the feature touches them

### Legacy Prefix Mapping

For reference, here is how legacy prefixes map to the new convention:

| Legacy Prefix | New Convention | Example |
|---------------|---------------|---------|
| `BRIEFING-Epic34-...` | `PFC-STRAT-BRIEF-...` | Strategy briefing |
| `PLAN-Supabase-...` | `PFC-SUPP-PLAN-...` | Supabase plan |
| `ARCH-Skeleton-...` | `PFC-SKE-ARCH-...` | Skeleton architecture |
| `STATUS-REPORT-...` | `PFC-PBS-RPT-...` | Status report |
| `OPERATING-GUIDE...` | `PFC-VIS-OPS-...` | Operating guide |
| `RELEASE-BULLETIN-...` | `PFC-VIS-REL-...` | Release bulletin |
| `PROPOSAL-...` | `PFC-<product>-PROP-...` | Proposal |
| `MANIFEST-...` | `PFC-STRAT-IDX-...` | Index/catalogue |

---

## 10. CI/CD Enforcement (Planned)

### GitHub Actions Workflow: `validate-doc-naming.yml`

A planned workflow will validate new/modified `.md` files against this convention:

```yaml
# Planned — not yet implemented
name: Validate Document Naming
on:
  pull_request:
    paths: ['PBS/**/*.md', 'azlan-github-workflow/**/*.md']

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Check new docs follow naming convention
        run: |
          # Only check files added or modified in this PR
          # Pattern: ^[A-Z]+-[A-Z]+-[A-Z]+-.*\.md$
          # Warn on violation, don't block (soft enforcement initially)
```

### Enforcement Phases

| Phase | When | Action |
|-------|------|--------|
| Phase 1 (now) | 2026-03-07 | Convention documented, manual compliance |
| Phase 2 | Next CI/CD sprint | Workflow warns on non-compliant new docs |
| Phase 3 | After stabilisation | Workflow blocks PRs with non-compliant new docs |

---

## 11. Triad Repo Document Naming

PFI triad repos (`pfi-*-dev`, `pfi-*-test`, `pfi-*-prod`) follow the same convention with the appropriate tier prefix:

```text
pfi-baiv-aiv-dev/
  docs/
    PFI-BAIV-DISC-ARCH-Discovery-Agent-v1.0.0.md
    PFI-BAIV-VE-OPS-Value-Engineering-Pipeline-v1.0.0.md
    PFI-BAIV-MKTG-REL-MarTech-Integration-v1.0.0.md

pfi-w4m-wwg-dev/
  docs/
    PFI-WWG-LSC-ARCH-Supply-Chain-Corridors-v1.0.0.md
    PFI-WWG-OFM-OPS-Operating-Model-v1.0.0.md

pfi-airl-caf-aza-dev/
  docs/
    PFI-AIRL-CAF-ARCH-CAF-Compliance-Assessment-v1.0.0.md
    PFI-AIRL-GRC-BRIEF-NCSC-CAF-Strategy-v1.0.0.md
```

---

## 12. Skills & Workflow Document Naming

Skills in `azlan-github-workflow/skills/` follow a simplified pattern since the skill folder already provides context:

```text
azlan-github-workflow/skills/pfc-efs/
  SKILL.md                              # Always SKILL.md (no prefix needed)
  registry-entry-v1.0.0.jsonld          # Always this pattern
  PFC-SKBLD-ARCH-EFS-v1.0.0.md         # Architecture (if exists)
  PFC-SKBLD-OPS-EFS-v1.0.0.md          # Ops guide (if exists)
  PFC-SKBLD-REL-EFS-v1.0.0.md          # Release bulletin (if exists)
  PFC-SKBLD-TEST-EFS-v1.0.0.md         # Test plan (if exists)
```

Family-level docs (e.g. DELTA covering 5 skills):

```text
azlan-github-workflow/skills/
  PFC-SKBLD-ARCH-DELTA-Pipeline-v1.0.0.md
  PFC-SKBLD-OPS-DELTA-Pipeline-v1.0.0.md
  PFC-SKBLD-REL-DELTA-Pipeline-v1.0.0.md
  PFC-SKBLD-TEST-DELTA-Pipeline-v1.0.0.md
```

---

## 13. Quick Reference Card

```text
Filename:  [TIER]-[PRODUCT]-[DOC-TYPE]-<Subject>-v<version>.md
Header:    # [TIER]-[PRODUCT]: <Title>
           **Product Code:** [TIER]-[PRODUCT] (<Full Name>)
           **Version:** <version>
           **Date:** <YYYY-MM-DD>
           **Status:** <Draft|Active|Superseded|Deprecated>

Tiers:     PFC | PFI-BAIV | PFI-WWG | PFI-EOMS | PFI-W4M | PFI-AIRL | PRD-* | APP-*
Products:  PBS SKBLD VIS REG EMC SKE DSY CICD SUPP ONTL STRAT AGNT GRC FAIR
Doc Types: ARCH OPS REL BRIEF PLAN STD TEST SPEC PROP IDX RPT GUIDE DEPREQ LOG
```
