# PFC-PPM-BRIEF-GitHub-Projects-PFI-Programme-Management-v2.0.0

**Product Code:** PFC-PPM
**Doc Type:** BRIEF (Strategy Briefing)
**Version:** 2.0.0
**Date:** 2026-03-11
**Status:** Decision Made — Pending Team Review
**Epic Ref:** Epic 61 (#947), Epic 34 (#518), Epic 39 (#569)
**Related:** PPM-ONT v5.0.0, EMC-ONT v5.0.0, PE-ONT v4.0.0, EFS-ONT v2.0.0
**Supersedes:** PFC-PPM-BRIEF-GitHub-Projects-PFI-Programme-Management-v1.0.0

---

## 1. Problem Statement

We have 5 active PFI instances (W4M, W4M-WWG, W4M-EOMS, BAIV, AIRL) with work across 15+ repos (5 triads of dev/test/prod). The v1.0.0 briefing proposed a single flat GitHub Project with filtered views. This was wrong — it contradicts the EMC cascade model (PFC→PFI→Product→Client) and the PPM-ONT hierarchy (Portfolio→Programme→Project).

**The cascade is the architecture.** GitHub Projects must mirror it, not flatten it.

---

## 2. What Changed from v1.0.0

| Area | v1.0.0 | v2.0.0 |
|---|---|---|
| GH Projects | One master project with views | **Cascaded projects mirroring EMC tiers** |
| Sub-instances | Product tier only | **Product AND Client tiers** |
| PFI portfolios | One portfolio for all | **PFIs can have multiple portfolios** |
| Value validation | Not addressed | **Three Voices + PMF/Kano gate all new initiatives** |
| Agile constraint | Not addressed | **Build validated demand only — no speculative structure** |

---

## 3. Ontology Alignment

### 3.1 PPM-ONT ↔ EMC Cascade Mapping

| PPM-ONT Entity | EMC Cascade Tier | GitHub Artefact | Purpose |
|---|---|---|---|
| `ppm:Portfolio` | PFC (Core) | GH Project: "PFC Portfolio" | Strategic oversight, initiative selection |
| `ppm:Programme` | PFI (Instance) | GH Project per PFI | Instance-level delivery, backlog ownership |
| `ppm:Project` | Product / Client | GH Project per product or client | Tactical execution, sprint-level work |

### 3.2 Value Validation Chain

| PPM Level | VE-Series Validation | Question Answered |
|---|---|---|
| Portfolio | VSOM + OKR + Three Voices (VoC/VoP/VoB) | WHY does this initiative exist? |
| Programme | Foundation (ORG, ORG-CONTEXT, ORG-MAT) | WHO does this serve? Are they ready? |
| Project | VP-ONT (Problem→Solution→Benefit) + PMF/Kano | WHAT value does it create? Does the market want it? |

---

## 4. Design Decisions (Resolved)

### DD-001: Cascaded GitHub Projects — Not a Single Flat Board

**Decision: Cascaded projects mirroring EMC tiers.**

The single-project model flattens the governance boundary between tiers. Each tier must have its own project with its own backlog and its own governance scope. Cross-linking via custom fields gives upward visibility without losing tier separation.

**Cascaded Project Structure:**

```
GH Project: "PFC Portfolio"                    ← PFC tier — strategic oversight
│   Tracks: platform epics, cross-cutting work, ontology releases, CI/CD
│   Repos: Azlan-EA-AAA
│
├── GH Project: "PFI-W4M Programme"            ← PFI tier — W4M instance
│   Tracks: W4M epics, features, stories
│   Repos: pfi-w4m-dev (+ test/prod visibility)
│   │
│   ├── GH Project: "W4M-WWG"                 ← Product tier — wholesale wine
│   │   Repos: pfi-w4m-wwg-dev
│   │
│   └── GH Project: "W4M-EOMS"                ← Product tier — order mgmt
│       Repos: pfi-w4m-eoms-dev
│
├── GH Project: "PFI-BAIV Programme"           ← PFI tier — BAIV instance
│   Tracks: BAIV epics, features, stories
│   Repos: pfi-baiv-aiv-dev (+ test/prod visibility)
│   │
│   └── (future Product/Client sub-projects as validated)
│
└── GH Project: "PFI-AIRL Programme"           ← PFI tier — AIRL instance
    Tracks: AIRL epics, features, stories
    Repos: pfi-airl-caf-aza-dev (+ test/prod visibility)
    │
    └── (future Product/Client sub-projects as validated)
```

**Why this is right:**
- Each tier has its own governance boundary, board, and backlog
- Maps 1:1 to PPM-ONT: `ppm:Portfolio` → `ppm:containsProgramme` → `ppm:Programme` → `ppm:containsProject` → `ppm:Project`
- Maps 1:1 to EMC cascade: PFC → PFI → Product → Client
- A PFI can spin up product/client sub-projects independently within its triad repos
- GitHub Projects V2 supports cross-project field linking — no data duplication needed

**Custom fields on EACH project (consistent across cascade):**

| Field | Type | Values | Maps to |
|---|---|---|---|
| `cascade_tier` | Single-select | `PFC`, `PFI`, `Product`, `Client` | EMC cascade tier |
| `parent_ref` | Text | Parent project/programme ID | PPM hierarchy navigation |
| `pfi_instance` | Single-select | `PFC-Core`, `W4M`, `W4M-WWG`, `W4M-EOMS`, `BAIV`, `AIRL` | `emc:InstanceConfiguration.instanceId` |
| `priority` | Single-select | `P0`, `P1`, `P2`, `P3` | Triage priority |
| `triad_env` | Single-select | `dev`, `test`, `prod` | CI/CD target |

---

### DD-002: Sub-Instances at Product AND/OR Client Tier

**Decision: Both. Products at Product tier, client engagements at Client tier.**

The EMC cascade architecture doc already identifies this gap — EMC conflates "instance" with "product". The correct model separates them:

- **Product tier** = PFI expertise domains and vertical markets. These are the PFI's offerings.
- **Client tier** = Specific client engagements that specialise a product. May need own graph scope, own GH Project, own backlog.
- Both connect back to the **same triad repos** — the triad is the CI/CD pipeline, not the programme structure.

**Worked Examples:**

```
PFI-W4M (PFI tier — instance)
├── W4M-WWG (Product tier — vertical market: wholesale wine)
├── W4M-EOMS (Product tier — expertise domain: order management)
└── W4M-CLIENT-XYZ (Client tier — specific client engagement)

PFI-BAIV (PFI tier — instance)
├── BAIV-MARTECH (Product tier — marketing technology)
├── BAIV-ANALYTICS (Product tier — competitive intelligence & analytics)
└── BAIV-CLIENT-ABC (Client tier — specific client engagement)

PFI-AIRL (PFI tier — instance)
├── AIRL-CAFHUB (Product tier — CAF compliance hub platform)
├── AIRL-AZASSURE (Product tier — Azure assurance & landing zone services)
└── AIRL-CLIENT-GOV01 (Client tier — government department engagement)
```

**Key rule:** A single triad (dev/test/prod) can host multiple Product and Client projects. The triad is the deployment pipeline — programme structure lives in PPM instance data and GitHub Projects, not in repo topology.

---

### DD-003: Create PPM Instance Data NOW

**Decision: Now — more critical with the cascade model.**

With cascaded projects, the PPM instance data IS the source of truth that defines:
- Which portfolios exist at which tier
- Which programmes contain which projects
- Which projects connect to which GitHub Projects
- The `ppm:deliversEpic` links tracing GH Projects back to ontology-backed registration

Without instance data, the cascaded GH Projects are disconnected boards. The JSONLD files give them ontological coherence. They also enable:
- Three Voices (VoC/VoP/VoB) scoring at portfolio level — validate WHY before committing resource
- PMF/Kano validation at product tier — does this product create value?
- VP-ONT alignment — Problem→Solution→Benefit mapped to each project

**Instance data set:**

```
PBS/ONTOLOGIES/ontology-library/PE-Series/PPM-ONT/instance-data/
├── pfc-portfolio-instance.jsonld               ← PFC Platform Portfolio
├── pfi-w4m-programme-instance.jsonld           ← W4M Programme
├── pfi-w4m-wwg-project-instance.jsonld         ← W4M-WWG Product Project
├── pfi-w4m-eoms-project-instance.jsonld        ← W4M-EOMS Product Project
├── pfi-baiv-programme-instance.jsonld          ← BAIV Programme
├── pfi-airl-programme-instance.jsonld          ← AIRL Programme
└── (future: product/client project instances as validated)
```

Each PFI programme instance can reference its **own portfolio** if the PFI has multiple independent product lines — `ppm:hasPortfolio` supports one-to-many.

---

### DD-004: Join Pattern JP-PPM-EMC-001 (No Ontology Change)

**Decision: Use existing join pattern. No `pfiInstanceRef`, no PPM-ONT version bump.**

**The question:** Can an instance have a project specific to that instance AND a project specific to a new product, all connected to the same triad repo and cascades?

**The answer:** Yes, inherently. PPM-ONT already supports `ppm:Programme → containsProject → ppm:Project*` (one-to-many). No new property needed.

**Option A — Add `pfiInstanceRef` (rejected):**

| Dimension | Assessment |
|---|---|
| Benefit | Direct one-hop navigation: `ppm:Programme.pfiInstanceRef → emc:InstanceConfiguration` |
| Benefit | Simpler queries |
| Risk | Ontology version bump (PPM-ONT v5.0.0 → v5.1.0) |
| Risk | Couples PPM to EMC — PPM should be domain-agnostic |
| Resource | ~2h: property, registry, tests, graph scopes |

**Option B — Join pattern JP-PPM-EMC-001 (accepted):**

| Dimension | Assessment |
|---|---|
| Benefit | No ontology change, no version bump |
| Benefit | PPM stays domain-agnostic — portable to any cascade model |
| Benefit | More flexible — join pattern bridges PPM to ANY governance framework |
| Risk | Two-hop resolution (acceptable — same pattern as VP↔RRR) |
| Resource | ~30 min: document join pattern, add to EMC composition rules |

**Join pattern definition:**

```
JP-PPM-EMC-001: PPM Programme ↔ EMC Instance Configuration

ppm:Programme("PFI-W4M")
  → ppm:Programme.org_id = "pfi-w4m-001"
  → org:Organisation.orgId = "pfi-w4m-001"
  → emc:InstanceConfiguration.orgId = "pfi-w4m-001"
  → emc:InstanceConfiguration.triadRepos = ["pfi-w4m-dev", "pfi-w4m-test", "pfi-w4m-prod"]
```

**Worked examples — multiple projects per instance:**

```
W4M Programme (ppm:Programme)
├── containsProject → W4M-WWG (ppm:Project)     ← product project
├── containsProject → W4M-EOMS (ppm:Project)    ← product project
└── containsProject → W4M-CLIENT-XYZ (ppm:Project) ← client project
    All three → JP-PPM-EMC-001 → emc:InstanceConfiguration("pfi-w4m")
    All three → same triad repos (pfi-w4m-dev/test/prod)

BAIV Programme (ppm:Programme)
├── containsProject → BAIV-MARTECH (ppm:Project)
└── containsProject → BAIV-CLIENT-ABC (ppm:Project)
    Both → JP-PPM-EMC-001 → emc:InstanceConfiguration("pfi-baiv")
    Both → same triad repos (pfi-baiv-aiv-dev/test/prod)

AIRL Programme (ppm:Programme)
├── containsProject → AIRL-CAFHUB (ppm:Project)
├── containsProject → AIRL-AZASSURE (ppm:Project)
└── containsProject → AIRL-CLIENT-GOV01 (ppm:Project)
    All three → JP-PPM-EMC-001 → emc:InstanceConfiguration("pfi-airl")
    All three → same triad repos (pfi-airl-caf-aza-dev/test/prod)
```

---

### DD-005: PPM-ONT Universal in All Graph Scopes — Portfolio of Initiatives

**Decision: PPM-ONT visible in all graph scopes, scoped correctly per tier.**

PPM-ONT provides the planning and validation structure for every tier. The portfolio of initiatives is the organising unit — VE tells you WHY, Foundation tells you WHO, VP validates WHAT.

**Tier-specific PPM usage:**

| Tier | PPM Focus | Supporting Ontologies | Validation Gate |
|---|---|---|---|
| PFC (Portfolio) | `ppm:PortfolioSelectionCycle`, Three Voices scoring | VSOM, OKR, KPI | Does this initiative create strategic value? |
| PFI (Programme) | `ppm:Programme`, PBS/WBS breakdown | ORG, ORG-CONTEXT, ORG-MAT | Who does it serve? Is the org ready? |
| Product (Project) | `ppm:Project`, `ppm:deliversEpic` | VP-ONT, PMF-ONT, Kano | Does the market want it? Does it solve a real problem? |
| Client (Project) | `ppm:Project`, client-specific scope | VP-ONT, RRR-ONT | Does this client engagement create mutual value? |

**PFIs can have multiple portfolios:**

```
PFI-W4M (org:Organisation)
├── ppm:hasPortfolio → "W4M Product Development"      ← Product portfolio
│   ├── ppm:containsProgramme → W4M Platform Programme
│   │   ├── containsProject → W4M-WWG
│   │   └── containsProject → W4M-EOMS
│   └── (future programmes as validated)
└── ppm:hasPortfolio → "W4M Client Engagements"        ← Client portfolio
    └── ppm:containsProgramme → W4M Client Programme
        └── containsProject → W4M-CLIENT-XYZ

PFI-BAIV (org:Organisation)
├── ppm:hasPortfolio → "BAIV Product Development"
│   └── ppm:containsProgramme → BAIV Platform Programme
│       ├── containsProject → BAIV-MARTECH
│       └── containsProject → BAIV-ANALYTICS
└── ppm:hasPortfolio → "BAIV Client Engagements"
    └── ppm:containsProgramme → BAIV Client Programme
        └── containsProject → BAIV-CLIENT-ABC

PFI-AIRL (org:Organisation)
├── ppm:hasPortfolio → "AIRL Product Development"
│   └── ppm:containsProgramme → AIRL Platform Programme
│       ├── containsProject → AIRL-CAFHUB
│       └── containsProject → AIRL-AZASSURE
└── ppm:hasPortfolio → "AIRL Client Engagements"
    └── ppm:containsProgramme → AIRL Client Programme
        └── containsProject → AIRL-CLIENT-GOV01
```

**Milestones and phases:** GitHub milestones map to `ppm:Project` phases. PBS provides product breakdown, WBS provides work breakdown. Both link to `ppm:Project` via `ppm:hasPBS` and `ppm:hasWBS`.

**Agile constraint:** Do NOT create portfolios, programmes, or projects speculatively. Use Three Voices + PMF/Kano to validate demand BEFORE creating structure. Build what the customer actually needs, not what you think they might want. The PPM structure emerges from validated demand — the ontology enables this discipline, it doesn't replace it.

---

## 5. Decision Summary

| # | Decision | Answer | Rationale |
|---|---|---|---|
| **DD-001** | GH Projects structure | **Cascaded projects mirroring EMC tiers** | PFC portfolio + PFI programme projects + Product/Client projects. Each tier has its own governance boundary. |
| **DD-002** | Sub-instance tier | **Both Product AND Client** | Products = expertise domains/markets. Clients = specific engagements. Both within same triad repos. |
| **DD-003** | PPM instance data timing | **Now** | Source of truth for cascaded projects. Enables Three Voices validation. |
| **DD-004** | pfiInstanceRef vs join | **JP-PPM-EMC-001 join pattern** | No ontology change. PPM stays domain-agnostic. One-to-many projects per instance already supported. |
| **DD-005** | PPM-ONT graph scope | **Universal, scoped per tier** | Portfolio of initiatives at every level. VE/VP/PMF/Kano validate value. PFIs can have multiple portfolios. |

---

## 6. Implementation Phases (Updated)

### Phase 1: GitHub Projects Setup (This Sprint)

1. Rename existing `Azlan-EA-AAA-2` to "PFC Portfolio" (or create new)
2. Create GH Project: "PFI-W4M Programme"
3. Create GH Project: "PFI-BAIV Programme"
4. Create GH Project: "PFI-AIRL Programme"
5. Create GH Project: "W4M-WWG" (Product)
6. Create GH Project: "W4M-EOMS" (Product)
7. Add custom fields to each: `cascade_tier`, `parent_ref`, `pfi_instance`, `priority`, `triad_env`
8. Add repos to correct projects (each PFI project tracks its triad dev repo)
9. Migrate existing issues from flat project to correct cascaded project
10. Extend `set-pbs-id.yml` to extract `PFI Instance:` and `Cascade Tier:` (~30 LOC)
11. Distribute `auto-add-to-projects.yml` to PFI triad repos pointing at their own GH Project

### Phase 2: PPM Instance Data (This Week)

1. Create `pfc-portfolio-instance.jsonld`
2. Create 3x programme instances (W4M, BAIV, AIRL)
3. Create 2x product project instances (W4M-WWG, W4M-EOMS)
4. Wire `ppm:deliversEpic` references to GitHub Epic IDs
5. Document join pattern JP-PPM-EMC-001
6. Update all PFI graph scopes — add PPM-ONT to `visible`

### Phase 3: Process & Validation (Next Sprint)

1. Define `pe:ProcessPath` for PFI Instance Lifecycle
2. Create Three Voices scoring template for portfolio-level initiative selection
3. Create PMF/Kano validation checklist for product-tier projects
4. Define VP-ONT mapping template for project-tier value propositions
5. Consider distributing `auto-add-to-projects.yml` to triad repos

---

## 7. Existing Workflow Integration

No new workflows required for Phase 1. The existing automation stack supports cascaded projects with minimal extension.

| Workflow | Role | Change Needed |
|---|---|---|
| `auto-add-to-projects.yml` | Feeds issues to project board | **Distribute** to PFI triad repos, each pointing at its own GH Project |
| `set-pbs-id.yml` | Extracts metadata to project fields | **Extend** — add PFI Instance + Cascade Tier fields |
| `pfc-release.yml` | Distribution pipeline | None — PPM instance data rides existing payload |
| `register-artifact.yml` | Artifact validation | None — already supports `pfi-<id>:` scoped artifacts |
| `validate-issue-naming.yml` | Cross-repo naming | None |
| `validate-doc-naming.yml` | Tier-aware doc naming | None |
| `promote.yml` | Triad promotion (dev→test→prod) | None |
| `guard-core.yml` | Sealed directory protection | None |

---

## 8. Cross-References

| Document | Relevance |
|---|---|
| [PFC-STRAT-ARCH-PFI-Product-Client-Graph-Cascade-v1.0.0.md](PFC-STRAT-ARCH-PFI-Product-Client-Graph-Cascade-v1.0.0.md) | Cascade architecture — PFC→PFI→Product→Client |
| [PFC-STRAT-BRIEF-VSOM-Programme-Distribution-v1.0.0.md](PFC-STRAT-BRIEF-VSOM-Programme-Distribution-v1.0.0.md) | Federated work orchestration |
| [PFC-CICD-GUIDE-PFI-Release-and-Promotion-v1.0.0.md](PFC-CICD-GUIDE-PFI-Release-and-Promotion-v1.0.0.md) | Hub→spoke CI/CD |
| [PFC-STRAT-BRIEF-PFI-Instance-Readiness-v1.0.0.md](PFC-STRAT-BRIEF-PFI-Instance-Readiness-v1.0.0.md) | Instance activation criteria |
| PPM-ONT v5.0.0 | [ppm-module-v5.0.0-oaa-v7.json](../ONTOLOGIES/ontology-library/PE-Series/PPM-ONT/ppm-module-v5.0.0-oaa-v7.json) |
| EMC-ONT v5.0.0 | [pf-EMC-ONT-v5.2.0.jsonld](../ONTOLOGIES/ontology-library/Orchestration/EMC-ONT/pf-EMC-ONT-v5.2.0.jsonld) |

---

*PFC-PPM-BRIEF-GitHub-Projects-PFI-Programme-Management-v2.0.0*
*Decisions Resolved — 2026-03-11*
