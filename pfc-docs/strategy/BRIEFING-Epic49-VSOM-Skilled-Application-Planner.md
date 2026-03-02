# Epic 49: VSOM Skilled Application Planner — Strategy-to-Delivery Pipeline

| Field | Value |
|---|---|
| **Epic** | #747 |
| **Version** | 2.0.0 |
| **Date** | 2026-02-26 |
| **Status** | PROPOSAL |
| **Priority** | HIGH |
| **Classification** | Internal |
| **Ontology Compliance** | DS-ONT v3.0.0, EFS-ONT v2.0.0, PPM-ONT v4.0.0, EMC-ONT v5.0.0, VP-ONT v1.2.3, VSOM-ONT, OKR-ONT v2.1.0, KPI-ONT v1.0.0, RRR-ONT, ORG-CONTEXT |
| **Architecture** | [ARCH-Skeleton-Application-Specification-Framework.md](ARCH-Skeleton-Application-Specification-Framework.md) |
| **Upstream** | Epic 48 (#703) — VE Process Chain Skills |
| **Downstream** | PFI triad repos, DS-ONT v3.1.0, Workbench (Epic 40 #577) |

---

## Executive Summary

Three mature PFC systems — the **application skeleton** (DS-ONT), the **EFS PRD** backlog hierarchy, and the **PPM project plan** — operate in complete isolation. The skeleton defines UI structure but knows nothing about what business function it delivers. The EFS PRD defines product scope but knows nothing about how it renders. The PPM plan governs delivery timelines but knows nothing about the application it manages.

Epic 49 bridges these three systems into a unified **Application Specification Model** and extends the VE skill chain (Epic 48) to automate the generation pipeline from strategic vision through to a configured, deployable GitHub triad with skeleton-specified UI.

The system architecture is defined in [ARCH-Skeleton-Application-Specification-Framework.md](ARCH-Skeleton-Application-Specification-Framework.md), which identifies 5 gaps that this epic closes.

---

## 1. Current State Assessment

### 1.1 What We Have (Assets)

| Asset | Version | Status | Location |
|-------|---------|--------|----------|
| VE Skill Chain — 7 standalone skills | v1.0.0 | Delivered (Epic 48) | `azlan-github-workflow/skills/pfc-{org-context,macro-analysis,industry-analysis,vsom,okr,kpi,vp}` |
| VE Pipeline Orchestrator | v1.0.0 | Delivered (Epic 48, F48.8) | `azlan-github-workflow/skills/pfc-ve-pipeline` |
| **pfc-efs** — EFS PRD Generator | v1.0.0 | **Exists** (Epic 48, F48.0) | `azlan-github-workflow/skills/pfc-efs` |
| Application Skeleton | DS-ONT v3.0.0 | Delivered (Epic 40) | `ontology-library/PE-Series/DS-ONT/instance-data/pfc-app-skeleton-v1.0.0.jsonld` |
| Skeleton Loader + Editor | v1.0.0 | Delivered (F40.13, F40.19, F40.20) | `js/app-skeleton-loader.js`, `js/app-skeleton-editor.js` |
| PFI Skeleton Template | v1.0.0 | Exists | `ontology-library/PE-Series/DS-ONT/instance-data/pfi-app-skeleton-template-v1.0.0.jsonld` |
| PFI Zone ID Convention | v1.0.0 | Documented | `ontology-library/PE-Series/DS-ONT/docs/CONVENTION-PFI-Zone-ID.md` |
| EFS-ONT | v2.0.0 | Stable | `ontology-library/PE-Series/EFS-ONT/efs-ontology.jsonld` |
| PPM-ONT | v4.0.0 | Stable | `ontology-library/PE-Series/PPM-ONT/ppm-module-v4.0.0-oaa-v6.json` |
| EMC Composition Engine | v5.0.0 | Delivered (Epic 19) | `js/emc-composer.js` — 11 composition categories |
| GitHub Workflow Skills | v1.0.0 | Exists | `azlan-github-workflow/skills/{create-epic,create-feature,create-story,setup-repo,setup-project-board}` |
| DELTA Discovery Chain | v1.0.0 | Delivered | `azlan-github-workflow/skills/pfc-delta-{pipeline,scope,evaluate,leverage,narrate,adapt}` |
| PFI Release/Promotion Guide | — | Documented | `PBS/STRATEGY/TRAINING-PFI-Release-and-Promotion-Guide.md` |
| Graph Cascade Architecture | v1.0.0 | Documented | `PBS/STRATEGY/ARCH-PFC-PFI-Product-Client-Graph-Cascade.md` |
| W4M-WWG Instance Data | v1.0.0 | Exists (Epic 45) | 4 VP instances, 7 RRR roles, 4 LSC corridors, 82 OFM entities |

### 1.2 What's Missing (Gaps)

Identified in the [Architecture document](ARCH-Skeleton-Application-Specification-Framework.md), Section 3:

| Gap | Description | Impact |
|-----|-------------|--------|
| **Gap 1** | No Skeleton ↔ EFS binding | Cannot answer: "Which zones deliver Epic 1?" |
| **Gap 2** | No Skeleton ↔ PPM binding | Cannot answer: "Which zones ship in Q2?" |
| **Gap 3** | No EFS ↔ PPM binding | Cannot answer: "Which project delivers this epic?" |
| **Gap 4** | No composition engine for EFS/PPM scoping | Cannot filter skeleton by epic scope or project scope |
| **Gap 5** | No zone CRUD in skeleton editor | Cannot create PFI zones from EFS inventory — requires manual JSONLD editing |

Additionally:

| Gap | Description | Impact |
|-----|-------------|--------|
| **Gap 6** | VE chain stops at VP | No automated path from VP → PPM project plan |
| **Gap 7** | pfc-efs exists but not wired to PPM | EFS skill generates PRD but without project plan context |
| **Gap 8** | No GitHub triad automation | Issue/milestone creation from EFS output is manual |
| **Gap 9** | No Application Specification document | No single artifact composing skeleton + EFS + PPM |

---

## 2. VSOM Framework

### 2.1 Vision

> "Every PFI application is specified by a single Application Specification document that composes skeleton structure, EFS product scope, and PPM delivery plan — generated automatically from the VE skill chain and consumable by both the workbench and external tooling."

### 2.2 Strategies

| # | Strategy | Priority | Addresses Gaps |
|---|----------|:--------:|:--------------:|
| S1 | **Schema-first bridging** — extend DS-ONT and PPM-ONT with cross-ontology join properties | HIGH | 1, 2, 3 |
| S2 | **Skill chain extension** — add PPM plan + zone allocator skills downstream of pfc-efs | HIGH | 6, 7 |
| S3 | **Skeleton editor extension** — zone CRUD operations for PFI zones | HIGH | 5 |
| S4 | **Composition engine extension** — EFS/PPM scoping modes in emc-composer.js | MEDIUM | 4 |
| S5 | **GitHub triad automation** — pfc-gh-triad skill using existing create-epic/feature/story skills | MEDIUM | 8 |
| S6 | **Spec composition** — Application Specification JSONLD composer + coverage metrics | MEDIUM | 9 |

### 2.3 Objectives — Balanced Scorecard

**Internal Process:**
- OBJ-P1: VP → PPM transformation produces valid PPM-ONT output with strategy traceability
- OBJ-P2: EFS → Skeleton zone allocation follows CONVENTION-PFI-Zone-ID convention
- OBJ-P3: Application Specification document composes all three graphs with coverage metrics

**Customer (PFI Instances):**
- OBJ-C1: W4M-WWG full chain produces expected output (2 programmes, 4+ projects, 8+ epics, 6+ zones)
- OBJ-C2: Zone allocation visible in workbench skeleton panel (Z22)

**Learning & Growth:**
- OBJ-L1: All new skills follow pfc-efs 8-section pipeline pattern (from F48.0)
- OBJ-L2: Zone CRUD pattern reusable across all PFI instances

**Financial:**
- OBJ-F1: Full chain cost per application plan < $1.00 (3-tier model)

---

## 3. Architecture Summary

Full architecture defined in [ARCH-Skeleton-Application-Specification-Framework.md](ARCH-Skeleton-Application-Specification-Framework.md).

### 3.1 The Application Specification Model

An Application Specification composes three graphs:

```
SKELETON (Structure)  ──realizes──→  EFS PRD (Function)
        │                                    │
        │ gatedBy                    delivers│
        │                                    │
        └─────────→  PPM PLAN (Temporal)  ←──┘
```

**Three join families, 8 join patterns (JP-SPEC-001 through JP-SPEC-008):**

| Family | Direction | Example |
|--------|-----------|---------|
| Skeleton → EFS | `ds:AppZone` realizesEpic `efs:Epic` | Z-WWG-300 realizes Corridor Expansion epic |
| Skeleton → PPM | `ds:AppZone` gatedByProject `ppm:Project` | Z-WWG-300 gated by Corridor Expansion project |
| EFS → PPM | `ppm:Project` deliversEpic `efs:Epic` | Corridor Expansion project delivers Corridor Expansion epic |

### 3.2 Schema Extensions Required

**DS-ONT v3.0.0 → v3.1.0:**
- `ds:realizesEpic` on AppZone
- `ds:realizesFeatures` on AppZone
- `ds:realizesFeature` on NavItem
- `ds:realizesStory` on ZoneComponent
- `ds:gatedByProject` on AppZone
- `ds:targetMilestone` on AppZone
- `ds:specStatus` on AppZone (planned | in-design | in-dev | shipped | deferred)

**PPM-ONT v4.0.0 → v4.1.0:**
- `ppm:deliversEpic` on Project → Epic
- `ppm:gatesRelease` on Milestone → Release
- `ppm:decomposesFeature` on WBS → Feature

### 3.3 Skill Chain Extension

```
Epic 48 (exists)                    Epic 49 (NEW)
────────────────                    ─────────────
pfc-org-context ─┐
pfc-macro        ├→ pfc-vsom ──→ pfc-okr ──→ pfc-kpi ──→ pfc-vp
pfc-industry    ─┘                                          │
                                                            ▼
                                              ┌── pfc-ppm-plan ◄─── NEW SKILL
                                              │         │
                                              │         ▼
                                              ├── pfc-efs (extend) ◄── EXISTS, wire to PPM
                                              │         │
                                              │         ▼
                                              ├── pfc-zone-allocator ◄── NEW SKILL
                                              │         │
                                              │         ▼
                                              ├── pfc-gh-triad ◄─── NEW SKILL (wraps existing)
                                              │         │
                                              │         ▼
                                              └── pfc-spec-composer ◄── NEW: Application Spec doc
```

### 3.4 Data Flow Contract

```
{working_dir}/ve-pipeline-output/              Epic 48 (exists)
  01-org-context-{instance}.jsonld
  02-macro-analysis-{instance}.jsonld
  03-industry-analysis-{instance}.jsonld
  04-vsom-{instance}.jsonld
  05-okr-{instance}-{quarter}.jsonld
  06-kpi-{instance}.jsonld
  07-vp-{instance}-{icp}.jsonld
  08-ve-pipeline-summary.md

{working_dir}/app-planner-output/              Epic 49 (NEW)
  09-ppm-project-plan-{instance}.jsonld
  10-efs-prd-{instance}.jsonld
  11-skeleton-spec-{instance}.jsonld           Zone allocations with EFS/PPM bindings
  12-app-spec-{instance}.jsonld                Composed Application Specification
  13-zone-allocation-{instance}.md             Human-readable zone allocation report
  14-coverage-report-{instance}.md             Coverage metrics
  15-gh-triad-config-{instance}.jsonld         GitHub repo/issue/milestone config
```

---

## 4. Features and Stories

### Feature Summary

| Feature | Title | Stories | Addresses | Status |
|---------|-------|:-------:|:---------:|--------|
| **F49.1** | DS-ONT v3.1.0 + PPM-ONT v4.1.0 Schema Extensions | 3 | Gaps 1–3 | Backlog |
| **F49.2** | pfc-ppm-plan — PPM Project Plan Skill | 3 | Gap 6 | Backlog |
| **F49.3** | pfc-efs PPM Integration — Wire EFS to PPM Context | 3 | Gap 7 | Backlog |
| **F49.4** | Zone CRUD Operations in Skeleton Editor | 3 | Gap 5 | Backlog |
| **F49.5** | pfc-zone-allocator — EFS to Skeleton Zone Allocation Skill | 4 | Gaps 1, 2 | Backlog |
| **F49.6** | pfc-gh-triad — GitHub Triad Configurator Skill | 3 | Gap 8 | Backlog |
| **F49.7** | pfc-spec-composer — Application Specification Composer | 3 | Gap 9, Gap 4 | Backlog |
| **F49.8** | E2E Validation — W4M-WWG Full Chain | 2 | All | Backlog |

**Totals: 8 features, 24 stories**

---

### F49.1: DS-ONT v3.1.0 + PPM-ONT v4.1.0 Schema Extensions

Extend DS-ONT and PPM-ONT with cross-ontology join properties as defined in [ARCH doc Section 4.2](ARCH-Skeleton-Application-Specification-Framework.md). This is the **schema foundation** — all other features depend on these properties existing.

**Stories:**

- [ ] S49.1.1: Add `realizesEpic`, `realizesFeatures`, `gatedByProject`, `targetMilestone`, `specStatus` to DS-ONT AppZone/NavItem/ZoneComponent schema → DS-ONT v3.1.0
- [ ] S49.1.2: Add `deliversEpic`, `gatesRelease`, `decomposesFeature` to PPM-ONT Project/Milestone/WBS schema → PPM-ONT v4.1.0
- [ ] S49.1.3: Define JP-SPEC-001 through JP-SPEC-008 join patterns in EMC-ONT cross-reference registry

**Dependencies:** None (unblocks F49.2–F49.7)
**Ref:** [ARCH doc Section 4.2–4.3](ARCH-Skeleton-Application-Specification-Framework.md)

---

### F49.2: pfc-ppm-plan — PPM Project Plan Skill

New skill generating PPM-ONT compliant portfolio/programme/project hierarchy from VE pipeline output. Strategies → programmes, VP problems → projects, OKR targets → milestones, RRR roles → resources, pain points → risks.

**Stories:**

- [ ] S49.2.1: Implement pfc-ppm-plan SKILL.md — 8-section pipeline with JP-PPM-001 through JP-PPM-005 join patterns
- [ ] S49.2.2: Create registry entry Entry-SKL-011 with Dtree SKILL_STANDALONE classification
- [ ] S49.2.3: Validate against W4M-WWG: 2 programmes (Growth, OpEx), 4 projects, 6 milestones expected

**Dependencies:** F49.1 (needs PPM-ONT v4.1.0 `deliversEpic` property)
**Input:** `07-vp-{instance}.jsonld` + `04-vsom-{instance}.jsonld` + `05-okr-{instance}.jsonld`
**Output:** `09-ppm-project-plan-{instance}.jsonld`
**Ref:** [ARCH doc Section 5.2](ARCH-Skeleton-Application-Specification-Framework.md)

**Join Patterns:**

| Pattern | From → To | Rule |
|---------|-----------|------|
| JP-PPM-001 | vsom:Strategy → ppm:Programme | 1 strategy = 1 programme |
| JP-PPM-002 | vp:Problem (Critical/Major) → ppm:Project | Severity drives project boundary |
| JP-PPM-003 | okr:KeyResult → ppm:Milestone | KR target date = milestone due date |
| JP-PPM-004 | rrr:FunctionalRole → ppm:Resource | Role → allocation |
| JP-PPM-005 | vp:PainPoint (severity) → ppm:RiskItem | Pain severity = risk level |

---

### F49.3: pfc-efs PPM Integration — Wire EFS to PPM Context

The `pfc-efs` skill **already exists** (`azlan-github-workflow/skills/pfc-efs/`). This feature extends it to accept PPM project plan context so that generated epics carry `deliversEpic` links and features carry WBS decomposition references.

**Stories:**

- [ ] S49.3.1: Extend pfc-efs SKILL.md to accept `09-ppm-project-plan.jsonld` as optional input and populate `efs:Epic.targetRelease` from PPM milestones
- [ ] S49.3.2: Add JP-EFS-PPM-001 (Project → Epic) and JP-EFS-PPM-003 (WBS → Feature) join pattern resolution to EFS output
- [ ] S49.3.3: Validate against W4M-WWG: generate EFS PRD with PPM traceability (every epic linked to a project, every feature linked to WBS)

**Dependencies:** F49.1 (PPM-ONT v4.1.0), F49.2 (PPM plan exists as input)
**Existing skill:** `azlan-github-workflow/skills/pfc-efs/` (ARCHITECTURE-v1.0.0.md, EFSOPS-GUIDE-v1.0.0.md)
**Ref:** [ARCH doc Section 5.3](ARCH-Skeleton-Application-Specification-Framework.md)

---

### F49.4: Zone CRUD Operations in Skeleton Editor

The skeleton editor (`app-skeleton-editor.js`, ~300 lines) can reorder nav items, move components between zones, and reorder zone components. It **cannot create, delete, or edit zones**. This feature adds the missing zone mutation operations required for F49.5 output to be consumed by the workbench.

**Stories:**

- [ ] S49.4.1: Add `addZone()`, `removeZone()`, `updateZoneProperty()` to `app-skeleton-editor.js` with PFI-tier enforcement (cannot modify PFC zones), undo/redo support (snapshot pattern), and CONVENTION-PFI-Zone-ID validation
- [ ] S49.4.2: Add `addNavItem()`, `addAction()` operations with naming convention validation (`nav-L4-{pfi}-{name}`, `action-{pfi}-{name}`)
- [ ] S49.4.3: Zone builder tab in Z22 skeleton panel — create/edit PFI zones with convention validation, spatial diagram update

**Dependencies:** None (independent of schema extensions)
**Ref:** [ARCH doc Section 3, Gap 5](ARCH-Skeleton-Application-Specification-Framework.md)
**Existing code:** `app-skeleton-editor.js` (~300 lines), `app-skeleton-panel.js` (Z22 inspector)
**Convention:** [CONVENTION-PFI-Zone-ID.md](../ONTOLOGIES/ontology-library/PE-Series/DS-ONT/docs/CONVENTION-PFI-Zone-ID.md)

---

### F49.5: pfc-zone-allocator — EFS to Skeleton Zone Allocation Skill

New skill that maps EFS epic/feature/persona inventory to PFI skeleton zones, nav items, and actions following the zone ID convention. Produces a skeleton overlay JSONLD with EFS/PPM bindings (the `realizes` and `gatedBy` properties from F49.1).

**Stories:**

- [ ] S49.5.1: Implement pfc-zone-allocator SKILL.md — 8-section pipeline: load EFS PRD + PPM plan + skeleton template → allocate zones by convention ranges (100s agents, 200s stakeholders, 300s dashboards)
- [ ] S49.5.2: NavItem + Action generator — EFS features → L4 nav items with `ds:realizesFeature` binding and ACTION_REGISTRY entries
- [ ] S49.5.3: Skeleton delta output — zones + navitems + actions as PFI override skeleton.jsonld patch (consumed by cascade merge in app-skeleton-loader.js)
- [ ] S49.5.4: Validate against W4M-WWG: generate PFI skeleton with Z-WWG-300+ dashboards, Z-WWG-200+ stakeholder views, Z-WWG-101+ agent panels, L4 nav items

**Dependencies:** F49.1 (DS-ONT v3.1.0 properties), F49.2 (PPM plan), F49.3 (EFS PRD with PPM links)
**Ref:** [ARCH doc Section 5.4](ARCH-Skeleton-Application-Specification-Framework.md), [ARCH doc Section 6.4](ARCH-Skeleton-Application-Specification-Framework.md) (W4M-WWG worked example)

**Allocation rules (from CONVENTION-PFI-Zone-ID.md):**

| EFS Entity | Zone Range | Example (W4M-WWG) |
|-----------|-----------|-------------------|
| Epic (strategic) | `Z-{PFI}-300+` | Z-WWG-300: Corridor Dashboard |
| Epic (operational) | `Z-{PFI}-101+` | Z-WWG-101: Order Manager |
| Feature (user-facing) | NavItem in parent zone | nav-L4-wwg-supplier-finder |
| Persona | `Z-{PFI}-200+` | Z-WWG-200: Procurement View |
| Agent (from PE-ONT) | `Z-{PFI}-102+` | Z-WWG-102: LSC Agent Panel |

---

### F49.6: pfc-gh-triad — GitHub Triad Configurator Skill

New skill creating dev/test/prod repo triad, project board, milestones, and numbered issue hierarchy. **Wraps existing skills** (`create-epic`, `create-feature`, `create-story`, `setup-repo`, `setup-project-board`) with EFS PRD + PPM plan input.

**Stories:**

- [ ] S49.6.1: Implement pfc-gh-triad SKILL.md — 8-section pipeline orchestrating existing GitHub workflow skills with naming convention enforcement (Epic N → FN.x → SN.x.y)
- [ ] S49.6.2: Dry-run mode (`--dry-run`) generating `15-gh-triad-config.jsonld` without creating live GitHub resources
- [ ] S49.6.3: Milestone mapping — PPM milestones to GitHub milestones with due dates; project board custom fields (VE-Trace to VSOM objective)

**Dependencies:** F49.3 (EFS PRD with PPM links)
**Existing skills:** `create-epic`, `create-feature`, `create-story`, `setup-repo`, `setup-project-board`
**Convention:** Epic N → FN.x → SN.x.y (mandatory, from [project conventions](../../.claude/projects/-Users-amandamoore/memory/MEMORY.md))

---

### F49.7: pfc-spec-composer — Application Specification Composer

New skill composing skeleton + EFS + PPM into a single Application Specification JSONLD document (schema defined in [ARCH doc Section 4.4](ARCH-Skeleton-Application-Specification-Framework.md)). Validates all JP-SPEC join patterns resolve. Computes coverage metrics. Adds composition modes to emc-composer.js.

**Stories:**

- [ ] S49.7.1: Implement pfc-spec-composer — compose skeleton overlay + EFS PRD + PPM plan into `12-app-spec-{instance}.jsonld` with all JP-SPEC-001–008 references resolved
- [ ] S49.7.2: Coverage metrics — compute and output epic coverage, feature coverage, milestone coverage, traceability depth (target: 100% epic, 100% user-facing feature, 80%+ milestone)
- [ ] S49.7.3: Add `SCOPE_BY_EPIC`, `SCOPE_BY_PROJECT`, `SCOPE_BY_MILESTONE` composition modes to emc-composer.js

**Dependencies:** F49.1 (schema), F49.5 (skeleton overlay with bindings)
**Ref:** [ARCH doc Section 4.4, 5.5, 7.3](ARCH-Skeleton-Application-Specification-Framework.md)

---

### F49.8: E2E Validation — W4M-WWG Full Chain

End-to-end validation against the most complete PFI instance. Runs full pipeline, verifies all output artifacts, validates traceability matrix, checks coverage metrics.

**Stories:**

- [ ] S49.8.1: Run complete pipeline against W4M-WWG — verify all output artifacts (09 through 15)
- [ ] S49.8.2: Traceability audit — verify every GH issue traces: issue → story → feature → epic → VP problem → VSOM strategy; every zone traces: zone → epic → project → programme → strategy

**Dependencies:** All features
**Instance data:** Epic 45 (#634) — 4 VP instances, 7 RRR roles, 4 LSC corridors, 82 OFM entities

---

## 5. Quality Gates

| Gate | Skill/Feature | Validates |
|------|--------------|-----------|
| G1 | pfc-ppm-plan | Every programme traces to a VSOM strategy |
| G2 | pfc-ppm-plan | Every project traces to a VP problem (Critical/Major) |
| G3 | pfc-ppm-plan | Resource allocation covers all RRR functional roles |
| G4 | pfc-efs (extended) | Every epic traces to a VP problem via JP-VP-002 |
| G5 | pfc-efs (extended) | Every story has persona + As/IWant/SoThat + >=1 AC |
| G6 | pfc-efs (extended) | VP-RRR alignment maintained (zero-tolerance) |
| G7 | pfc-efs (extended) | Every epic has `deliversEpic` link from PPM project |
| G8 | pfc-zone-allocator | Zone IDs follow CONVENTION-PFI-Zone-ID |
| G9 | pfc-zone-allocator | All strategic epics have dashboard zones allocated |
| G10 | pfc-zone-allocator | All user-facing features have nav items |
| G11 | pfc-gh-triad | Naming convention enforced (Epic N, FN.x, SN.x.y) |
| G12 | pfc-spec-composer | All JP-SPEC references resolve (no dangling @id pointers) |
| G13 | pfc-spec-composer | Coverage: 100% epic, 100% user-facing feature, 80%+ milestone |

---

## 6. Implementation Sequence

```
Phase 1: Schema Foundation (unblocks everything)
  F49.1 — DS-ONT v3.1.0 + PPM-ONT v4.1.0 schema extensions + JP-SPEC patterns
  F49.4 — Zone CRUD operations in skeleton editor (independent, run in parallel)

Phase 2: Core Skills (sequential — each consumes previous output)
  F49.2 — pfc-ppm-plan skill
  F49.3 — pfc-efs PPM integration (extends existing skill)
  F49.5 — pfc-zone-allocator skill

Phase 3: Composition + Delivery (after Phase 2 skills produce artifacts)
  F49.6 — pfc-gh-triad skill (wraps existing GitHub workflow skills)
  F49.7 — pfc-spec-composer + emc-composer.js composition modes

Phase 4: Validation
  F49.8 — E2E W4M-WWG full chain
```

**Critical path:** F49.1 → F49.2 → F49.3 → F49.5 → F49.7 → F49.8

**Parallel track:** F49.4 (zone CRUD) runs alongside Phase 1–2, must complete before F49.5 output can be consumed by workbench.

---

## 7. Three-Tier Cost Model

| Tier | Model | Used For | Est. |
|------|-------|----------|------|
| Tier 1 (Local) | Schema validation | JSON-LD checks, join pattern verification, coverage metrics | Free |
| Tier 2 (Haiku) | Fast generation | Template-driven PPM/EFS, zone allocation, GH issue creation | ~$0.02/run |
| Tier 3 (Sonnet) | Complex synthesis | VP → PPM mapping, story writing, risk assessment | ~$0.15/run |

**Full chain:** ~$0.50 per application plan (7 VE + PPM + EFS + zone + spec + GH)

---

## 8. Acceptance Criteria

- [ ] DS-ONT v3.1.0 published with `realizesEpic`, `realizesFeatures`, `gatedByProject`, `targetMilestone`, `specStatus` properties
- [ ] PPM-ONT v4.1.0 published with `deliversEpic`, `gatesRelease`, `decomposesFeature` relationships
- [ ] JP-SPEC-001 through JP-SPEC-008 join patterns defined in EMC-ONT cross-reference registry
- [ ] pfc-ppm-plan produces valid PPM-ONT output with strategy traceability
- [ ] pfc-efs extended to accept PPM context and populate `deliversEpic` links
- [ ] Skeleton editor supports addZone(), removeZone(), updateZoneProperty(), addNavItem(), addAction()
- [ ] pfc-zone-allocator generates valid skeleton overlay with EFS/PPM bindings following zone convention
- [ ] pfc-gh-triad creates properly numbered issues wrapping existing create-epic/feature/story skills
- [ ] Application Specification JSONLD document composes all three graphs with all JP-SPEC references resolving
- [ ] Coverage metrics computed: 100% epic, 100% user-facing feature, 80%+ milestone
- [ ] emc-composer.js supports SCOPE_BY_EPIC, SCOPE_BY_PROJECT, SCOPE_BY_MILESTONE modes
- [ ] E2E: W4M-WWG full chain produces expected output (2 programmes, 4 projects, 2 epics, 5 features, 6 zones, 5 nav items)
- [ ] VP-RRR alignment (zero-tolerance) maintained through entire chain

---

## 9. Cross-References

### Architecture & Strategy

| Document | Relationship |
|----------|-------------|
| [ARCH-Skeleton-Application-Specification-Framework.md](ARCH-Skeleton-Application-Specification-Framework.md) | **System architecture** — defines the Skeleton ↔ EFS ↔ PPM model, join patterns, schema extensions, worked example |
| [ARCH-PFC-PFI-Product-Client-Graph-Cascade.md](ARCH-PFC-PFI-Product-Client-Graph-Cascade.md) | 4-tier cascade model (PFC → PFI → Product → App) governing skeleton and ontology cascading |
| [VSOM-Strategic-Toolkit-Plan-v1.0.1.md](VSOM-Strategic-Toolkit-Plan-v1.0.1.md) | Original VSOM toolkit vision this extends |
| [BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy.md](BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy.md) | Platform strategy — S2 (VE-Driven), S3 (Agentic), S5 (Figma Make) |
| [BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md](BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md) | Skills registry architecture — quasi-OO cascade pattern all new skills follow |

### Workbench & Skeleton

| Document | Relationship |
|----------|-------------|
| [APP-SKELETON-GUIDE.md](../DESIGN-SYSTEM/APP-SKELETON-GUIDE.md) v1.2.0 | Skeleton architecture & management guide (22 zones, 5 layers, DS-ONT v3.0.0) |
| [PLAN-F40.19-Skeleton-Editor.md](PLAN-F40.19-Skeleton-Editor.md) | Skeleton editor — F49.4 extends this with zone CRUD |
| [PLAN-F40.18-Skeleton-Inspector-Panel.md](PLAN-F40.18-Skeleton-Inspector-Panel.md) | Z22 skeleton panel — F49.4 adds zone builder tab here |
| [CONVENTION-PFI-Zone-ID.md](../ONTOLOGIES/ontology-library/PE-Series/DS-ONT/docs/CONVENTION-PFI-Zone-ID.md) | Zone ID allocation convention — governs all zone allocation in F49.5 |

### Epics & Features

| Issue | Relationship |
|-------|-------------|
| Epic 48 (#703) | **Upstream** — VE skill chain (7 skills + orchestrator) feeding this epic |
| Epic 40 (#577) | **Workbench** — skeleton system consuming zone CRUD (F49.4) and spec output |
| Epic 45 (#634) | **W4M-WWG** — primary validation instance (F49.8) |
| Epic 34 (#518) | Platform strategy — 6 strategies governing this work |
| Epic 39 (#569) | PFI Strategy Cascade — planner distributes to PFI triads |
| Epic 50 (#TBD) | FairSlice — platform economics (potential consumer of spec model) |
| F48.0 (#737) | pfc-efs template pattern — all new skills follow same 8-section format |
| F48.8 (#711) | pfc-ve-pipeline — upstream orchestrator |

### Existing Skills (azlan-github-workflow/skills/)

| Skill | Relationship |
|-------|-------------|
| `pfc-ve-pipeline` | Upstream orchestrator — produces VP output consumed by F49.2 |
| `pfc-efs` | **Exists** — F49.3 extends it with PPM context, not a new skill |
| `pfc-vp` | VP-RRR alignment enforcement — passthrough gate for entire chain |
| `create-epic`, `create-feature`, `create-story` | **Exist** — F49.6 wraps them with EFS PRD input |
| `setup-repo`, `setup-project-board` | **Exist** — F49.6 wraps them for triad scaffold |
| `pfc-delta-pipeline` | Reference pattern — 5-phase orchestrator similar to F49.7 composition |

### Ontologies

| Ontology | Version | Role in Epic 49 |
|----------|---------|----------------|
| DS-ONT | v3.0.0 → **v3.1.0** | Extended with realizesEpic, gatedByProject properties |
| PPM-ONT | v4.0.0 → **v4.1.0** | Extended with deliversEpic, gatesRelease properties |
| EFS-ONT | v2.0.0 | Consumed — epic/feature/story hierarchy |
| EMC-ONT | v5.0.0 | Extended — JP-SPEC join patterns, composition modes |
| VP-ONT | v1.2.3 | Consumed — VP problems/gains/benefits as input |
| VSOM-ONT | — | Consumed — strategies as programme source |
| OKR-ONT | v2.1.0 | Consumed — KRs as milestone source |
| KPI-ONT | v1.0.0 | Consumed — metrics for coverage validation |
| RRR-ONT | — | Consumed — roles for resource allocation, VP-RRR gate |
| ORG-CONTEXT | — | Consumed — org scoping for PFI instance |

### Training & Guides

| Document | Relationship |
|----------|-------------|
| [TRAINING-PFI-Release-and-Promotion-Guide.md](TRAINING-PFI-Release-and-Promotion-Guide.md) | Triad CI/CD promotion pipeline (dev → test → prod) referenced by F49.6 |
| [OPERATING-GUIDE-PFI-Graph-Creation.md](OPERATING-GUIDE-PFI-Graph-Creation.md) | PFI graph creation workflow — reference for zone allocator output consumption |

---

## Appendix A: Ontology Coverage Matrix

| Ontology | Phase 1 Strategise | Phase 2 Cascade | Phase 3 Plan | Phase 4 Allocate | Phase 5 Compose |
|----------|:-:|:-:|:-:|:-:|:-:|
| VSOM-ONT | **P** | R | R | | R |
| OKR-ONT | | **P** | R | | |
| KPI-ONT | | **P** | R | | R |
| VP-ONT | | **P** | **P** | | |
| RRR-ONT | | **P** | R | | |
| PPM-ONT | | | **P** | R | R |
| EFS-ONT | | | **P** | **P** | R |
| DS-ONT | | | | **P** | **P** |
| EMC-ONT | R | R | R | R | **P** |
| ORG-CONTEXT | **P** | | R | | R |

**P** = Primary producer | **R** = Referenced/consumed

---

## Appendix B: Revision History

| Version | Date | Change |
|---------|------|--------|
| 1.0.0 | 2026-02-26 | Initial briefing — skill-chain focused |
| 2.0.0 | 2026-02-26 | **Major revision** — grounded in ARCH doc, restructured around 5 gaps + 9 total gaps, recognises existing pfc-efs and GitHub workflow skills, features aligned to ARCH roadmap phases, comprehensive cross-references |

---

*Epic 49: VSOM Skilled Application Planner — v2.0.0 PROPOSAL*
