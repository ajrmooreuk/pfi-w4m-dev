# Architecture: Skeleton → EFS → PPM Application Specification Framework

**Version:** 1.0.0
**Date:** 2026-02-26
**Status:** Architecture Review & Gap Analysis
**Parent Strategy:** Epic 49 (#747) — VSOM Skilled Application Planner
**Related Epics:** Epic 40 (#577) — Workbench, Epic 48 (#703) — VE Skills, Epic 45 (#634) — W4M-WWG
**Governing Ontologies:** DS-ONT v3.0.0, EFS-ONT v2.0.0, PPM-ONT v4.0.0, EMC-ONT v5.0.0

---

## 1. Executive Summary

The PFC platform has three mature systems that should compose into a single application specification framework but currently operate in isolation:

1. **Application Skeleton** (DS-ONT) — defines UI structure: zones, nav layers, nav items, actions, components. Fully data-driven, cascade-merged across PFC → PFI → Product → App tiers. 22 zones, 5 nav layers, 62 actions. **Knows nothing about what it delivers.**

2. **EFS PRD** (EFS-ONT) — defines product scope: epics, features, user stories, personas, acceptance criteria. Full backlog hierarchy with strategic traceability to VSOM objectives. **Knows nothing about how it renders.**

3. **PPM Project Plan** (PPM-ONT) — defines delivery structure: portfolios, programmes, projects, PBS/WBS, milestones, dependencies. **Knows nothing about the application it governs.**

These three systems share a common problem: **no formal bridge connects them**. The skeleton is structural, the PRD is functional, and the project plan is temporal. An application specification requires all three: *what zones exist* (skeleton), *what features they deliver* (EFS), and *when they ship* (PPM).

This document defines the architecture for bridging them into a unified Application Specification Model.

---

## 2. Current State

### 2.1 Application Skeleton (DS-ONT v3.0.0)

**6 entity types**, cascadeable across 4 tiers:

| Entity | Count (PFC) | Purpose |
|--------|:-----------:|---------|
| `ds:Application` | 1 | Top-level app definition |
| `ds:AppZone` | 22 (Z1–Z22) | UI layout regions (fixed, floating, sliding, overlay, conditional) |
| `ds:NavLayer` | 5 (L1–L4, L3-split) | Navigation groupings |
| `ds:NavItem` | 26 | Buttons, toggles, dropdowns, separators |
| `ds:Action` | 62 | First-class action entities with guards and accessibility |
| `ds:ZoneComponent` | 25 | Components placed in zones |

**Key properties per zone:**
```
zoneId, zoneName, zoneType, defaultVisible,
visibilityCondition, cascadeTier, domSelector, zIndex
```

**Key properties per nav item:**
```
itemId, label, itemType, action, executesAction (→Action),
renderOrder, visibilityCondition, enabledCondition, stateBinding, cascadeTier
```

**PFI Extension Points:**
- Zones: `Z-{PFI}-{nnn}` (convention documented in CONVENTION-PFI-Zone-ID.md)
- Nav items: `nav-L4-{pfi}-{name}` on L4+
- Actions: `{verb}{PFI_SHORT_CODE}{Feature}`
- Components: `cmp-{pfi}-{name}`

**What skeleton has:** Structure, layout, navigation, action wiring.
**What skeleton lacks:** Any link to *what business function* a zone delivers or *when* it ships.

### 2.2 EFS PRD (EFS-ONT v2.0.0)

**Core hierarchy:**

```
efs:Epic
  ├── epicTheme, businessOutcome, targetRelease
  ├── alignsToObjective → vsom:StrategicObjective
  ├── implementsInitiative → vsom:StrategicInitiative
  └── hasFeature[]
      └── efs:Feature
          ├── featureType, benefitHypothesis, acceptanceCriteria[]
          ├── deliversValue → pmf:ValueProposition
          ├── enablesCapability → efs:Capability
          └── hasStory[]
              └── efs:UserStory
                  ├── asA (→Persona), iWant, soThat
                  ├── storyPoints, storyAcceptanceCriteria[]
                  └── hasTasks[]
                      └── efs:Task
```

**Status enum:** Planning, Ready, In Progress, In Review, Done, Blocked, Deferred

**Integration points:** VSOM (strategic alignment), PMF (value delivery), KPI (metrics), PE (capabilities)

**What EFS has:** Complete backlog hierarchy with personas, ACs, and strategic traceability.
**What EFS lacks:** Any mapping to *which UI zone* implements a feature or *which project* delivers it.

### 2.3 PPM Project Plan (PPM-ONT v4.0.0)

**Core hierarchy:**

```
ppm:Organisation
  └── ppm:Portfolio
      └── ppm:Programme
          ├── programme_type (transformation | innovation | operation | support)
          └── ppm:Project
              ├── project_type, estimated_duration, methodology
              ├── dependsOn → [other Projects]
              ├── deliverables[]
              └── ppm:PBS / ppm:WBS (decomposition)
```

**What PPM has:** Delivery timeline, programme governance, project dependencies, resource planning.
**What PPM lacks:** Any link to *which skeleton zones* a project delivers or *which EFS features* it implements.

### 2.4 Existing Bridge Points (Partial)

| Bridge | Status | Location |
|--------|--------|----------|
| `realizesFeature` relationship | **Defined in EMC-ONT**, not wired in skeleton loader | JP-DS-001: `ds:Zone realizesFeature efs:Feature` |
| Backlog Panel (Z13) | **Displays EFS data** but skeleton structure is decoupled from it | app.js `state.backlogFilterEpic` |
| PFI skeleton template Z-{PFI}-302 | **Conditional PPM zone** exists but has **no content model** | pfi-app-skeleton-template-v1.0.0.jsonld |
| EFS entity types in action wiring | **targetEntityTypes** includes `efs:Epic, efs:Feature, efs:Story` | app-skeleton-loader.js line 209–210 |

---

## 3. Gap Analysis

### Gap 1: No Skeleton ↔ EFS Binding

**Problem:** When a PFI skeleton defines `Z-WWG-300` (Corridor Dashboard), there is no formal property declaring that this zone implements `efs:Epic-Corridor-Expansion`. The `realizesFeature` relationship is defined in EMC-ONT but the skeleton loader does not resolve it.

**Impact:** Cannot answer: "Which zones deliver Epic 1?" or "Which features are unimplemented in the UI?"

**Required properties on `ds:AppZone`:**
```json
{
  "ds:realizesEpic": "efs:Epic-Corridor-Expansion",
  "ds:realizesFeatures": [
    "efs:Feature-Supplier-Discovery",
    "efs:Feature-Corridor-Health"
  ]
}
```

**Required properties on `ds:NavItem`:**
```json
{
  "ds:realizesFeature": "efs:Feature-Supplier-Discovery"
}
```

**Required properties on `ds:ZoneComponent`:**
```json
{
  "ds:realizesStory": "efs:Story-View-Suppliers-By-Corridor"
}
```

### Gap 2: No Skeleton ↔ PPM Binding

**Problem:** When a PPM project `INS-EA-001` has a milestone "MVP — AU+NZ corridors live", there is no skeleton property declaring that zone Z-WWG-300 ships with that milestone.

**Impact:** Cannot answer: "Which zones ship in Q2?" or "What is the skeleton footprint of Project X?"

**Required properties on `ds:AppZone`:**
```json
{
  "ds:gatedByProject": "ppm:Project-Corridor-Expansion",
  "ds:targetMilestone": "ppm:Milestone-MVP-AU-NZ"
}
```

### Gap 3: No EFS ↔ PPM Binding

**Problem:** EFS epics and PPM projects both exist but there is no formal join pattern between them. An epic "Corridor Expansion Platform" should map to PPM project "Corridor Expansion" but this is implicit, not declared.

**Impact:** Cannot answer: "Which project delivers this epic?" or "What is the EFS scope of this project?"

**Required join patterns:**
```
JP-EFS-PPM-001: ppm:Project → deliversEpic → efs:Epic
JP-EFS-PPM-002: ppm:Milestone → gatesRelease → efs:Release
JP-EFS-PPM-003: ppm:WBS → decomposesFeature → efs:Feature
```

### Gap 4: No Composition Engine for EFS/PPM Scoping

**Problem:** EMC Composer has 11 composition categories (PRODUCT, COMPETITIVE, STRATEGIC, etc.) that filter by ontology set. There is no equivalent for filtering by EFS epic scope or PPM project scope.

**Impact:** Cannot say: "Show me only the zones and nav items that belong to Epic 1" or "Show me the skeleton footprint of the Q2 milestone."

**Required composition modes:**
```
SCOPE_BY_EPIC:      filter skeleton to zones/navitems realizing features of Epic X
SCOPE_BY_PROJECT:   filter skeleton to zones/navitems gated by Project Y
SCOPE_BY_MILESTONE: filter skeleton to zones/navitems targeting Milestone Z
```

### Gap 5: No Zone CRUD in Skeleton Editor

**Problem:** The skeleton editor (`app-skeleton-editor.js`, ~300 lines) can reorder nav items, move components between zones, and reorder zone components. It **cannot create, delete, or edit zones**. Zone definitions are read-only after load.

**Impact:** A specification framework must be able to define new PFI zones from EFS epic inventory. Currently this requires manual JSONLD editing.

**Required operations:**
```
addZone(zoneSpec)           → validates Z-{PFI}-{nnn} convention, adds to zoneRegistry
removeZone(zoneId)          → validates not PFC-tier, cascades component removal
updateZoneProperty(id, prop, val) → validates cascade immutability (BR-DS-013)
addNavItem(itemSpec)        → validates naming convention, adds to navLayerRegistry
addAction(actionSpec)       → adds to actionIndex
```

---

## 4. Proposed Model: Application Specification Graph

### 4.1 Conceptual Model

An **Application Specification** is the composition of three graphs:

```
┌─────────────────────────────────────────────────────┐
│                APPLICATION SPECIFICATION              │
│                                                       │
│  ┌──────────────┐   realizes    ┌──────────────┐     │
│  │  SKELETON    │──────────────→│  EFS PRD     │     │
│  │  (Structure) │               │  (Function)  │     │
│  │              │               │              │     │
│  │  Zones       │               │  Epics       │     │
│  │  NavItems    │               │  Features    │     │
│  │  Actions     │               │  Stories     │     │
│  │  Components  │               │  Personas    │     │
│  └──────┬───────┘               └──────┬───────┘     │
│         │                              │             │
│         │ gatedBy              delivers│             │
│         │                              │             │
│  ┌──────▼───────────────────────────────▼──────┐     │
│  │             PPM PROJECT PLAN                │     │
│  │             (Temporal)                      │     │
│  │                                             │     │
│  │  Portfolio → Programme → Project            │     │
│  │  Milestones, Dependencies, WBS              │     │
│  └─────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────┘
```

**Three graphs, three join families:**

| Join Family | Direction | Purpose |
|-------------|-----------|---------|
| **Skeleton → EFS** (realizes) | Zone/NavItem/Component → Epic/Feature/Story | What business function does this UI element deliver? |
| **Skeleton → PPM** (gatedBy) | Zone → Project/Milestone | When does this zone ship? What gates it? |
| **EFS → PPM** (delivers) | Epic/Feature → Project/Milestone/WBS | Which project delivers this epic? Which WBS item covers this feature? |

### 4.2 Entity Extensions

**DS-ONT v3.1.0 — New Properties:**

On `ds:AppZone`:
```json
{
  "ds:realizesEpic": { "@type": "@id", "description": "EFS Epic this zone implements" },
  "ds:realizesFeatures": { "@type": "@id", "@container": "@set", "description": "EFS Features realized by this zone" },
  "ds:gatedByProject": { "@type": "@id", "description": "PPM Project that gates this zone" },
  "ds:targetMilestone": { "@type": "@id", "description": "PPM Milestone for this zone's delivery" },
  "ds:specStatus": { "@type": "xsd:string", "enum": ["planned", "in-design", "in-dev", "shipped", "deferred"] }
}
```

On `ds:NavItem`:
```json
{
  "ds:realizesFeature": { "@type": "@id", "description": "EFS Feature this nav item provides access to" }
}
```

On `ds:ZoneComponent`:
```json
{
  "ds:realizesStory": { "@type": "@id", "description": "EFS UserStory this component implements" }
}
```

**PPM-ONT v4.1.0 — New Relationships:**
```json
{
  "ppm:deliversEpic": { "@type": "@id", "domain": "ppm:Project", "range": "efs:Epic" },
  "ppm:gatesRelease": { "@type": "@id", "domain": "ppm:Milestone", "range": "efs:Release" },
  "ppm:decomposesFeature": { "@type": "@id", "domain": "ppm:WBS", "range": "efs:Feature" }
}
```

### 4.3 Join Patterns

| Pattern | From → To | Rule |
|---------|-----------|------|
| JP-SPEC-001 | `ds:AppZone` → `efs:Epic` | 1 zone can realize 1 epic (strategic zones) |
| JP-SPEC-002 | `ds:AppZone` → `efs:Feature[]` | 1 zone can realize multiple features |
| JP-SPEC-003 | `ds:NavItem` → `efs:Feature` | 1 nav item provides access to 1 feature |
| JP-SPEC-004 | `ds:ZoneComponent` → `efs:UserStory` | 1 component implements 1 story |
| JP-SPEC-005 | `ds:AppZone` → `ppm:Project` | 1 zone is gated by 1 project |
| JP-SPEC-006 | `ds:AppZone` → `ppm:Milestone` | 1 zone targets 1 delivery milestone |
| JP-SPEC-007 | `ppm:Project` → `efs:Epic` | 1 project delivers 1+ epics |
| JP-SPEC-008 | `ppm:WBS` → `efs:Feature` | 1 WBS item decomposes 1 feature |

### 4.4 Application Specification Document Schema

An application specification is a single JSONLD document combining all three graphs:

```json
{
  "@context": {
    "ds": "https://pf-core.dev/ds-ont#",
    "efs": "https://pf-core.dev/efs-ont#",
    "ppm": "https://pf-core.dev/ppm-ont#",
    "spec": "https://pf-core.dev/app-spec#"
  },
  "@type": "spec:ApplicationSpecification",
  "spec:specId": "SPEC-{PFI}-{PRODUCT}-v{semver}",
  "spec:pfiInstance": "PFI-W4M-WWG",
  "spec:product": "W4M-WWG-Platform",
  "spec:version": "1.0.0",
  "spec:status": "in-design",

  "spec:skeleton": { "@id": "ds:app-skeleton-pfi-wwg-v1.0.0" },
  "spec:prd": { "@id": "efs:prd-wwg-platform-v1.0.0" },
  "spec:projectPlan": { "@id": "ppm:plan-wwg-platform-v1.0.0" },

  "spec:zoneAllocations": [
    {
      "spec:zone": { "@id": "ds:Z-WWG-300" },
      "spec:realizesEpic": { "@id": "efs:Epic-Corridor-Expansion" },
      "spec:realizesFeatures": [
        { "@id": "efs:Feature-Supplier-Discovery" },
        { "@id": "efs:Feature-Corridor-Health" }
      ],
      "spec:gatedByProject": { "@id": "ppm:Project-Corridor-Expansion" },
      "spec:targetMilestone": { "@id": "ppm:MS-MVP-AU-NZ" },
      "spec:specStatus": "in-design"
    }
  ],

  "spec:navAllocations": [
    {
      "spec:navItem": { "@id": "ds:nav-L4-wwg-supplier-finder" },
      "spec:realizesFeature": { "@id": "efs:Feature-Supplier-Discovery" },
      "spec:specStatus": "planned"
    }
  ]
}
```

---

## 5. Application Specification Workflow

### 5.1 Input → Process → Output

```
INPUTS                          PROCESS                         OUTPUTS
──────                          ───────                         ───────
07-vp-{instance}.jsonld    ──→  1. PPM Plan Generation     ──→  09-ppm-project-plan.jsonld
04-vsom-{instance}.jsonld  ──→     (VP problems → projects)
05-okr-{instance}.jsonld   ──→     (OKR targets → milestones)

09-ppm-project-plan.jsonld ──→  2. EFS PRD Generation      ──→  10-efs-prd.jsonld
07-vp-{instance}.jsonld    ──→     (VP → epics/features/stories)

10-efs-prd.jsonld          ──→  3. Zone Allocation         ──→  11-skeleton-spec.jsonld
pfi-app-skeleton-template  ──→     (EFS epics → zones)
CONVENTION-PFI-Zone-ID     ──→     (features → nav items)
                                   (stories → components)

All three                  ──→  4. Spec Composition        ──→  12-app-spec-{instance}.jsonld
                                   (skeleton + EFS + PPM
                                    joined via JP-SPEC-001–008)
```

### 5.2 Step 1: PPM Plan Generation

**Input:** VE pipeline output (VSOM strategies, OKR targets, VP problems/solutions)
**Output:** PPM-ONT compliant portfolio/programme/project hierarchy

**Mapping rules:**

| VE Source | PPM Target | Join Pattern |
|-----------|-----------|--------------|
| `vsom:Strategy` | `ppm:Programme` | 1 strategy = 1 programme |
| `vp:Problem` (Critical/Major) | `ppm:Project` | Problem severity drives project boundary |
| `okr:KeyResult` | `ppm:Milestone` | KR target date = milestone due date |
| `rrr:FunctionalRole` | `ppm:Resource` | Role allocation per project |
| `vp:PainPoint` (severity) | `ppm:RiskItem` | Pain severity maps to risk level |

**Quality gates:**
- G1: Every programme traces to a VSOM strategy
- G2: Every project traces to a VP problem
- G3: Resource allocation covers all RRR functional roles

### 5.3 Step 2: EFS PRD Generation

**Input:** PPM plan + VP data
**Output:** EFS-ONT compliant epic/feature/story hierarchy

**Mapping rules:**

| Source | EFS Target | Join Pattern |
|--------|-----------|--------------|
| `vp:Problem` (Critical) | `efs:Epic` | JP-VP-002: severity → backlog level |
| `vp:Gain` | `efs:Feature` | JP-EFS-003: gains → feature deliverables |
| `vp:Benefit` | `efs:UserStory.soThat` | JP-EFS-001: benefits → So That clause |
| `vp:SuccessMetric` | `efs:AcceptanceCriterion` | JP-EFS-002: metrics → testable criteria |
| `vp:ICP` | `efs:Persona` | JP-VP-003: manifestsAsPersona |
| `ppm:Project` | `efs:Epic.targetRelease` | JP-EFS-PPM-001: project → epic delivery |

**Quality gates:**
- G1: Every epic traces to a VP problem
- G2: Every story has persona + As/IWant/SoThat + >=1 AC
- G3: VP-RRR alignment maintained (zero-tolerance)
- G4: Every epic has a `deliversEpic` link from a PPM project

### 5.4 Step 3: Zone Allocation

**Input:** EFS PRD + PFI skeleton template + Zone ID convention
**Output:** PFI skeleton overlay with EFS/PPM bindings

**Allocation rules (from CONVENTION-PFI-Zone-ID.md):**

| EFS Entity | Zone Range | Allocation Rule |
|-----------|-----------|-----------------|
| Epic (strategic) | `Z-{PFI}-300+` | Dashboard zones — one per strategic epic |
| Epic (operational) | `Z-{PFI}-101+` | Management zones — one per operational epic |
| Feature (user-facing) | NavItem in parent zone | L4 nav item with `ds:action` wiring |
| Feature (system/infra) | No zone — background | Tracked in EFS only, no UI surface |
| Persona | `Z-{PFI}-200+` | Stakeholder view zones — one per role |
| Agent (from PE-ONT) | `Z-{PFI}-102+` | Agent panel zones — one per domain agent |

**Nav item generation:**

For each user-facing feature, generate:
```json
{
  "@id": "ds:nav-L4-{pfi}-{feature-slug}",
  "@type": "ds:NavItem",
  "ds:label": "{Feature Name}",
  "ds:itemType": "Button",
  "ds:belongsToLayer": "ds:L4",
  "ds:executesAction": "ds:action-{pfi}-{feature-slug}",
  "ds:realizesFeature": "efs:{feature-id}",
  "ds:cascadeTier": "PFI"
}
```

**Action generation:**

For each nav item, generate a corresponding action:
```json
{
  "@id": "ds:action-{pfi}-{feature-slug}",
  "@type": "ds:Action",
  "ds:actionName": "show{PFI}{FeatureName}",
  "ds:functionRef": "toggleZonePanel",
  "ds:parameterType": "zoneId",
  "ds:accessibilityHint": "Open {Feature Name}"
}
```

**Quality gates:**
- G1: All strategic epics have dashboard zones allocated
- G2: All user-facing features have nav items
- G3: Zone IDs follow `Z-{PFI}-{nnn}` convention with correct range
- G4: No collision with existing PFC zones (Z1–Z99 reserved)

### 5.5 Step 4: Spec Composition

**Input:** Skeleton overlay + EFS PRD + PPM plan
**Output:** Application Specification document (see schema in 4.4)

Compose the three graphs by resolving all JP-SPEC join patterns:
1. Walk each zone → find its `realizesEpic` target in the EFS graph
2. Walk each zone → find its `gatedByProject` target in the PPM graph
3. Walk each PPM project → find its `deliversEpic` target in EFS
4. Validate all references resolve (no dangling @id pointers)
5. Compute coverage metrics:
   - **Feature coverage:** % of EFS features with at least one zone/navitem
   - **Epic coverage:** % of EFS epics with at least one zone
   - **Milestone coverage:** % of zones with a target milestone
   - **Traceability depth:** % of zones traceable to VSOM objective

---

## 6. Worked Example: W4M-WWG

### 6.1 VE Pipeline Input (exists — Epic 48)

```
Org Context:   UK specialist food importer, 4 source corridors (AU/NZ/IS/IE → UK)
VSOM:          2 strategies — Growth (expand corridors) + OpEx (fulfilment)
VP:            4 value propositions, 7 RRR roles, 82 OFM entities
KPI/BSC:       24 KPIs across 4 BSC perspectives
```

### 6.2 PPM Plan Output (Step 1)

```
ppm:Portfolio — W4M-WWG Application Portfolio
│
├── ppm:Programme — Growth
│   │   traces to vsom:Strategy-Growth
│   │
│   ├── ppm:Project — Corridor Expansion Platform
│   │   │   traces to vp:Problem-Limited-Corridors
│   │   ├── ppm:Milestone — MS1: MVP AU+NZ live (Q2 2026)
│   │   └── ppm:Milestone — MS2: IS+IE added (Q3 2026)
│   │
│   └── ppm:Project — Supplier Discovery
│       │   traces to vp:Problem-Supplier-Visibility
│       └── ppm:Milestone — MS3: Supplier DB live (Q2 2026)
│
└── ppm:Programme — Operational Excellence
    │   traces to vsom:Strategy-OpEx
    │
    ├── ppm:Project — Fulfilment Management
    │   │   traces to vp:Problem-Manual-Tracking
    │   ├── ppm:Milestone — MS4: OFM integration (Q2 2026)
    │   └── ppm:Milestone — MS5: LSC automation (Q4 2026)
    │
    └── ppm:Project — Analytics Dashboard
        │   traces to vp:Problem-No-Visibility
        └── ppm:Milestone — MS6: BSC dashboard live (Q3 2026)
```

### 6.3 EFS PRD Output (Step 2)

```
efs:Epic — Corridor Expansion Platform        [deliversEpic ← ppm:Project-Corridor-Expansion]
│
├── efs:Feature — Supplier Discovery Dashboard
│   ├── efs:Story — S1.1.1: View suppliers by corridor      (As Procurement Mgr...)
│   ├── efs:Story — S1.1.2: Export supplier shortlist        (As Procurement Mgr...)
│   └── efs:Story — S1.1.3: Compare supplier ratings         (As MD...)
│
├── efs:Feature — Corridor Health Monitor
│   ├── efs:Story — S1.2.1: View corridor KPIs              (As Ops Manager...)
│   └── efs:Story — S1.2.2: Alert on corridor disruption    (As Ops Manager...)
│
└── efs:Feature — New Corridor Onboarding
    ├── efs:Story — S1.3.1: Configure corridor parameters   (As Admin...)
    └── efs:Story — S1.3.2: Validate corridor compliance     (As Compliance Officer...)

efs:Epic — Fulfilment Management System        [deliversEpic ← ppm:Project-Fulfilment-Mgmt]
│
├── efs:Feature — Order Tracking Dashboard
│   ├── efs:Story — S2.1.1: View orders by status           (As Warehouse Staff...)
│   └── efs:Story — S2.1.2: Update order status             (As Warehouse Staff...)
│
└── efs:Feature — LSC Corridor Automation
    ├── efs:Story — S2.2.1: Auto-route by corridor rules    (As System...)
    └── efs:Story — S2.2.2: Exception handling workflow      (As Ops Manager...)
```

### 6.4 Zone Allocation Output (Step 3)

```
PFC Base Skeleton (Z1–Z22, immutable)
│
└── PFI-WWG Overlay:
    │
    ├── Z-WWG-100  Identity Zone
    │
    ├── Z-WWG-101  Agent Dashboard (OFM agent)
    │   realizesEpic: efs:Epic-Fulfilment-Mgmt
    │   gatedByProject: ppm:Project-Fulfilment-Mgmt
    │   targetMilestone: ppm:MS4-OFM-Integration
    │
    ├── Z-WWG-102  LSC Agent Panel
    │   realizesFeature: efs:Feature-LSC-Corridor-Automation
    │   gatedByProject: ppm:Project-Fulfilment-Mgmt
    │   targetMilestone: ppm:MS5-LSC-Automation
    │
    ├── Z-WWG-200  Procurement View (Persona: Procurement Mgr)
    │   realizesFeatures: [efs:Feature-Supplier-Discovery, efs:Feature-Corridor-Onboarding]
    │
    ├── Z-WWG-201  Ops View (Persona: Ops Manager)
    │   realizesFeatures: [efs:Feature-Corridor-Health, efs:Feature-Order-Tracking]
    │
    ├── Z-WWG-300  Corridor Dashboard
    │   realizesEpic: efs:Epic-Corridor-Expansion
    │   gatedByProject: ppm:Project-Corridor-Expansion
    │   targetMilestone: ppm:MS1-MVP-AU-NZ
    │   specStatus: in-design
    │
    └── Z-WWG-301  Analytics Dashboard
        realizesEpic: efs:Epic-Analytics
        gatedByProject: ppm:Project-Analytics-Dashboard
        targetMilestone: ppm:MS6-BSC-Dashboard
        specStatus: planned
```

**Nav items generated:**

```
nav-L4-wwg-supplier-finder     → realizesFeature: efs:Feature-Supplier-Discovery
nav-L4-wwg-corridor-health     → realizesFeature: efs:Feature-Corridor-Health
nav-L4-wwg-corridor-onboard    → realizesFeature: efs:Feature-Corridor-Onboarding
nav-L4-wwg-order-tracker       → realizesFeature: efs:Feature-Order-Tracking
nav-L4-wwg-lsc-automation      → realizesFeature: efs:Feature-LSC-Corridor-Automation
```

### 6.5 Coverage Metrics

| Metric | Value | Notes |
|--------|:-----:|-------|
| Epic coverage | 2/2 (100%) | Both strategic epics have zones |
| Feature coverage (user-facing) | 5/5 (100%) | All user-facing features have nav items |
| Feature coverage (system) | 0/0 | No system features in this example |
| Milestone coverage | 5/6 (83%) | Z-WWG-301 has MS6 but Z-WWG-200/201 have no milestone (persona zones are cross-cutting) |
| Traceability depth | 100% | All zones trace: Zone → Epic → VP Problem → VSOM Strategy |

---

## 7. State Bindings and Runtime Behaviour

### 7.1 New State Properties

```javascript
state.activeSpecId        = null;    // Currently loaded app spec
state.activeEFSEpicId     = null;    // Filtered epic (from backlog panel)
state.activePPMProjectId  = null;    // Filtered project
state.activeMilestoneId   = null;    // Filtered milestone
state.specCoverageMetrics = {};      // Feature/epic/milestone coverage
```

### 7.2 Visibility Condition Extensions

Zone visibility can reference EFS/PPM filters:

```json
{
  "@id": "ds:Z-WWG-300",
  "ds:visibilityCondition": "state.activeInstanceId === 'PFI-W4M-WWG' && (state.activeEFSEpicId === null || state.activeEFSEpicId === 'efs:Epic-Corridor-Expansion')"
}
```

When user selects "Epic 1: Corridor Expansion" in the backlog panel:
- `state.activeEFSEpicId` is set
- `updateSkeletonVisibility()` re-evaluates all zone conditions
- Only zones realizing Epic 1 features remain visible
- Nav items for other epics are hidden

### 7.3 Composition Engine Integration

Add to `emc-composer.js`:

```javascript
const SPEC_COMPOSITION_MODES = {
  SCOPE_BY_EPIC:      (spec, epicId)      => filter zones where realizesEpic === epicId,
  SCOPE_BY_PROJECT:   (spec, projectId)   => filter zones where gatedByProject === projectId,
  SCOPE_BY_MILESTONE: (spec, milestoneId) => filter zones where targetMilestone === milestoneId,
  SCOPE_BY_PERSONA:   (spec, personaRole) => filter zones allocated to persona role,
  FULL_SPEC:          (spec)              => all zones with all bindings
};
```

---

## 8. Artifacts Produced by This Framework

| # | Artifact | Format | Location | Consumed By |
|---|----------|--------|----------|-------------|
| 1 | PPM Project Plan | JSONLD | `{output}/09-ppm-project-plan-{pfi}.jsonld` | Zone allocator, Workbench graph |
| 2 | EFS PRD | JSONLD | `{output}/10-efs-prd-{pfi}.jsonld` | Zone allocator, Backlog panel (Z13) |
| 3 | Skeleton Spec Overlay | JSONLD | `{output}/11-skeleton-spec-{pfi}.jsonld` | Skeleton loader cascade merge |
| 4 | Application Specification | JSONLD | `{output}/12-app-spec-{pfi}.jsonld` | Spec viewer, Coverage dashboard |
| 5 | Zone Allocation Report | Markdown | `{output}/13-zone-allocation-{pfi}.md` | Human review, design review |
| 6 | Coverage Report | Markdown | `{output}/14-coverage-report-{pfi}.md` | Quality gate validation |

---

## 9. Implementation Roadmap

### Phase 1: Schema Extensions

1. Extend DS-ONT v3.0.0 → v3.1.0: add `realizesEpic`, `realizesFeatures`, `gatedByProject`, `targetMilestone`, `specStatus` to zone/navitem/component schemas
2. Extend PPM-ONT v4.0.0 → v4.1.0: add `deliversEpic`, `gatesRelease`, `decomposesFeature` relationships
3. Define JP-SPEC-001 through JP-SPEC-008 join patterns in EMC-ONT
4. Update `pfi-app-skeleton-template-v1.0.0.jsonld` to include new optional properties

### Phase 2: Zone CRUD + Specification Editor

1. Add `addZone()`, `removeZone()`, `updateZoneProperty()` to `app-skeleton-editor.js`
2. Add zone builder tab to Z22 skeleton panel with convention validation
3. Add `addNavItem()`, `addAction()` operations
4. Undo/redo support for all new operations (snapshot-based, same pattern as existing)

### Phase 3: Specification Skills

1. `pfc-ppm-plan` SKILL.md — 8-section pipeline: VE output → PPM plan (see 5.2)
2. `pfc-efs-prd` SKILL.md — 8-section pipeline: PPM + VP → EFS PRD (see 5.3)
3. `pfc-zone-allocator` SKILL.md — 8-section pipeline: EFS + skeleton template → zone allocation (see 5.4)
4. `pfc-spec-composer` SKILL.md — compose all three into Application Specification document

### Phase 4: Runtime Integration

1. Spec loader in `app-skeleton-loader.js` — parse spec document, resolve EFS/PPM references
2. State bindings for `activeEFSEpicId`, `activePPMProjectId`, `activeMilestoneId`
3. Composition engine modes (SCOPE_BY_EPIC, SCOPE_BY_PROJECT, etc.)
4. Coverage metrics computation and display

### Phase 5: Validation (W4M-WWG)

1. Run full pipeline against W4M-WWG instance data
2. Verify 6 output artifacts
3. Validate coverage metrics (target: 100% epic, 100% user-facing feature, 80%+ milestone)
4. Validate traceability: every zone → epic → VP problem → VSOM strategy

---

## 10. Relationship to Existing Epics

| Epic | What It Covers | What This Architecture Adds |
|------|----------------|---------------------------|
| **Epic 40 (#577)** | Workbench skeleton system (delivered) | Zone CRUD, spec-aware visibility, EFS/PPM state bindings |
| **Epic 48 (#703)** | VE skill chain (7 skills + orchestrator) | 3 new downstream skills (PPM, EFS, zone allocator) |
| **Epic 49 (#747)** | Application planner (briefing exists) | This ARCH doc provides the missing system architecture |
| **Epic 45 (#634)** | W4M-WWG instance | Primary validation target for the framework |
| **Epic 34 (#518)** | Platform strategy | S2 (VE-Driven) and S5 (Figma Make) govern design loop |

---

## 11. Summary: 5 Things to Build

| # | What | Why | Effort |
|---|------|-----|:------:|
| 1 | **DS-ONT v3.1.0 schema extensions** | Enable skeleton to declare what it delivers and when | S |
| 2 | **PPM-ONT v4.1.0 + JP-SPEC join patterns** | Enable project plan to reference EFS scope | S |
| 3 | **Zone CRUD operations** | Skeleton editor cannot create zones — blocks all spec generation | M |
| 4 | **3 specification skills** (ppm-plan, efs-prd, zone-allocator) | Automate the VE → skeleton pipeline | L |
| 5 | **Application Specification composer + runtime** | Unified document joining all three graphs with coverage metrics | M |

**Critical path:** Item 1 + 2 (schema) unblock Item 4 (skills). Item 3 (zone CRUD) unblocks Item 5 (runtime). Items 1–3 can run in parallel.

---

*Architecture: Skeleton → EFS → PPM Application Specification Framework — v1.0.0*
