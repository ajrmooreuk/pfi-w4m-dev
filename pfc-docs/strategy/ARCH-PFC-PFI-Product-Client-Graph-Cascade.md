# Architecture: PFC → PFI → Product → Client Graph Cascade

**Version:** 1.0.0
**Date:** 2026-02-20
**Status:** Architecture Review & Gap Analysis
**Parent Strategy:** Epic 34 (#518) — PF-Core Graph-Based Agentic Platform
**Related Epics:** #196 (Epic 19), #147 (Epic 9H), #140 (Epic 9F), #139 (Epic 9E), #569 (Epic 39)

---

## 1. Executive Summary

EMC-ONT v4.0.0 defines the orchestration schema for composing enterprise models from 46 ontologies across 5 series. It currently supports **two tiers** (PFC core schemas → PFI instance configurations) via `ContextLevel`, `RequirementCategory`, and `InstanceConfiguration`. However, the three-tier cascade described in Epic 34's strategy (PFC → PFI → Client) has **four critical architectural gaps** that must be resolved to enable:

1. **Instance-specific graphs** — filtered by org-context, vertical market, and maturity
2. **Product-specific graphs** — scoped sub-graphs within an instance (e.g., BAIV-AIV vs BAIV-CompetitiveIntel)
3. **Client-specific graphs** — per-client deployments with org-context filtering
4. **B2C customer-specific graphs** — filtered by CRT-ONT customer role and IND-ONT individual type

This document maps what EMC currently provides, identifies the gaps, and proposes the architecture for the full 4-tier graph cascade.

---

## 2. Current State: What EMC v4.0.0 Provides

### 2.1 Four Scoping Dimensions (Implemented in Schema)

```
┌─────────────────────────────────────────────────────────┐
│ ScopingDimension-ContextLevel    →  PFC | PFI           │
│ ScopingDimension-ProductContext  →  BAIV-AIV | VHF | …  │
│ ScopingDimension-OrgContext      →  Industry/Size/Mat.  │
│ ScopingDimension-RequirementScope → STRATEGIC|PRODUCT|… │
└─────────────────────────────────────────────────────────┘
```

These dimensions **exist as entities** but only ContextLevel has concrete resolution logic (the VHF worked example). OrgContext and ProductContext are **described but not operationalised** — no filter engine, no cascade resolution.

### 2.2 Composition Rule Pipeline (7 Rules, Priority-Ordered)

```
Priority 1: FoundationAlways      → ORG-ONT mandatory
Priority 2: DependencyChain       → Transitive deps resolved
Priority 3: MaturityFilter        → Exclude advanced ONTs at low maturity
Priority 4: PFIInstance           → Load product-specific instance data
Priority 5: RCSGGovernance        → Compliance overlay when in scope
Priority 6: AgenticBuild          → PE-ONT + AI agent defs
Priority 7: AgentRoleBridge       → Agent→Role→RBAC chain resolution
```

Rules are **defined declaratively** in the schema. No runtime engine exists yet.

### 2.3 Graph Patterns (5 Patterns)

| Pattern | Description | Status |
|---|---|---|
| **HubSpoke** | ORG-CONTEXT hub → VE, PE, RCSG, Orchestration spokes | Modelled |
| **LineageChain** | VSOM → OKR → VP → PMF → EFS sequential traceability | Modelled, G23 validates |
| **BridgeJoin** | Cross-ontology joins (VP↔RRR, CRT↔VP, EFS↔PPM) | Modelled |
| **GovernanceOverlay** | RCSG-Series overlays onto PE and VE via GRC-FW hub | Modelled |
| **AgentBridge** | pe:AIAgent → RRR roles → RBAC policies | Modelled |

### 2.4 Instance Configurations (3 Defined)

| Instance | Product | Scopes | Required ONTs | Status |
|---|---|---|---|---|
| **BAIV** | BAIV-AIV | PRODUCT, COMPETITIVE, STRATEGIC | Not specified | Partial — no requiredOntologies list |
| **VHF** | VHF-Nutrition | PRODUCT | VP, RRR, PMF, CRT, ORG, ORG-CONTEXT | Complete — with worked example |
| **ANTQ** | ANTQ-Valuations | PRODUCT | VP, RRR, PMF, CRT, ORG, ORG-CONTEXT, ANTIQUES | Complete |

### 2.5 Instance Data Files (20+ Files Across Library)

```
VE-Series/VP-ONT/instance-data/       → AIRL, RCS value propositions
VE-Series/CRT-ONT/instance-data/      → VHF (6 roles), ANTQ (19 roles)
PE-Series/PE-ONT/instance-data/       → Insurance process
PE-Series/PPM-ONT/instance-data/      → Insurance portfolio
PE-Series/DS-ONT/instance-data/       → BAIV, PAND, RCS, VHF, WWG design systems
PE-Series/LOG-ONT/                    → AU-UK meat corridor
Foundation/IND-ONT/                   → AIRL (5), W4M (5), VHF (6) individuals
RCSG-Series/GRC-01-GOV/              → Cross-framework governance mapping
VE-Series/RRR-PFC-PFI-RBAC-BAIV-ONT/ → BAIV RBAC (11 roles, 4 tiers)
```

---

## 3. The Four-Tier Graph Cascade Architecture

### 3.1 Conceptual Model

```
Tier 0: PFC (Platform Foundation Core)
  │     46 ontology SCHEMAS — no instance data
  │     EMC composition rules, graph patterns, series definitions
  │     VE lineage chain: VSOM → OKR → VP → PMF → EFS
  │
  ├── Tier 1: PFI Instance Graph
  │     │     Instance-specific ontology SELECTION + instance data
  │     │     e.g., BAIV activates VE+PE+RCSG, VHF activates VE+Foundation
  │     │     Filtered by: vertical market, maturity level, requirement scopes
  │     │
  │     ├── Tier 2: PFI Product Graph
  │     │     │     Product-specific sub-graph WITHIN an instance
  │     │     │     e.g., BAIV-AIV (MarTech), BAIV-CompIntel (Competitive)
  │     │     │     Each product has own VP, CRT roles, PMF metrics
  │     │     │     Connected to VE lineage at product-specific OKR/KPI nodes
  │     │     │
  │     │     └── Tier 3: Client Graph (B2B) / Customer Graph (B2C)
  │     │           Per-client org-context filtered graph
  │     │           B2B: org:Organisation → org-ctx scoping → role filtering
  │     │           B2C: crt:CustomerRole → ind:IndividualType → purchase behaviour
  │     │           Inherits product graph, filters by org/individual context
```

### 3.2 How Each Tier Connects to VE Lineage

The VE lineage chain (VSOM → OKR → VP → PMF → EFS) is the **strategic backbone** that every tier must connect to:

```
Tier 0 (PFC):    VSOM ──→ OKR ──→ VP ──→ PMF ──→ EFS
                  │         │       │       │       │
Tier 1 (PFI):    │    Instance    Instance  Instance │
                  │    OKRs       VPs      PMFs    │
                  │         │       │       │       │
Tier 2 (Product): │    Product    Product  Product  │
                  │    OKRs       VPs      PMFs    │
                  │         │       │       │       │
Tier 3 (Client):  │    Client    Client    Client   │
                       OKRs     VPs       PMFs
```

**Traversal example — VHF Nutrition B2C customer "Fitness Enthusiast":**
```
PFC VSOM → PFI-VHF OKR (Nutrition app growth) →
  Product VP (VHF-Nutrition value props) →
    CRT filter (B2C_Enthusiast role) →
      IND filter (Fitness Enthusiast individual type) →
        PMF validation (product-market fit for this segment) →
          Purchase behaviour (subscription, monthly, price-sensitive)
```

**Traversal example — BAIV B2B client "Acme Corp":**
```
PFC VSOM → PFI-BAIV OKR (AI visibility objectives) →
  Product VP (BAIV-AIV value props) →
    ORG-CONTEXT filter (Acme Corp: MarTech, 50 employees, maturity 2) →
      RRR roles (CMO, VP Marketing — filtered by org size) →
        CRT filter (B2B_DecisionMaker role) →
          PMF (product-market fit for mid-market MarTech) →
            EFS (feature set scoped to maturity level 2)
```

---

## 4. Gap Analysis: What's Missing

### Gap 1: ProductConfiguration Entity (NEW — Required)

**Problem:** EMC conflates "instance" with "product". BAIV is an instance that could have multiple products (BAIV-AIV, BAIV-CompetitiveIntel, BAIV-Analytics). Currently `InstanceConfiguration` tries to do both jobs.

**Required entity:**
```json
{
  "@id": "emc:ProductConfiguration",
  "name": "ProductConfiguration",
  "description": "A product-level graph scope within a PFI instance. Defines which subset of the instance's ontologies and data are active for a specific product offering.",
  "properties": {
    "productId": "string — unique product identifier",
    "parentInstance": "ref → InstanceConfiguration",
    "productOntologies": "array — ontologies active for THIS product (subset of parent)",
    "productDataFiles": "array — product-specific instance data files",
    "veLineageEntry": "ref — which VE lineage node this product connects to",
    "customerModel": "enum — B2B | B2C | B2B2C",
    "crtRoleFilter": "array — CRT roles applicable to this product",
    "indTypeFilter": "array — IND individual types applicable",
    "orgContextFilter": "object — industry, size, maturity constraints"
  }
}
```

**Impact:** New entity + `hasProductConfig` relationship from InstanceConfiguration. New composition rule `ProductScopeFilter` at priority 4.5 (between PFIInstance and RCSGGovernance).

### Gap 2: ClientGraphScope Entity (NEW — Required)

**Problem:** The three-tier PFC→PFI→Client model from Epic 34 strategy has no client-level entity in EMC. Client graphs need per-client org-context filtering, role scoping, and data isolation.

**Required entity:**
```json
{
  "@id": "emc:ClientGraphScope",
  "name": "ClientGraphScope",
  "description": "A client-specific graph scope within a product. Filters the product graph by the client's org-context (industry, size, maturity, market segment) and role profile.",
  "properties": {
    "clientId": "string — unique client identifier",
    "parentProduct": "ref → ProductConfiguration",
    "orgProfile": {
      "organisation": "ref → org:Organisation",
      "orgContext": "ref → org-ctx:OrganizationContext",
      "industry": "string",
      "size": "enum — SME | MidMarket | Enterprise",
      "maturityLevel": "integer 0-5",
      "marketSegments": "array of org-ctx:MarketSegment refs"
    },
    "activeRoles": "array — RRR FunctionalRole refs active for this client",
    "activeCRTRoles": "array — CRT CustomerRole refs (B2B buyer roles)",
    "graphIsolation": "enum — SHARED | TENANT_ISOLATED | DEDICATED",
    "veLineageOverrides": {
      "clientOKRs": "ref → client-specific OKR instances",
      "clientVPs": "ref → client-specific VP instances",
      "clientPMFs": "ref → client-specific PMF instances"
    }
  }
}
```

**Impact:** New entity + `hasClientScope` relationship from ProductConfiguration. New composition rule `ClientGraphResolution` at priority 8 (after AgentRoleBridge). This is the multi-tenancy boundary.

### Gap 3: B2C CustomerGraphScope (NEW — Required for VHF/ANTQ)

**Problem:** B2C products (VHF, ANTQ) don't have "clients" in the B2B sense — they have individual customers. CRT-ONT and IND-ONT provide the role/individual taxonomy, but EMC has no mechanism to scope a graph to a specific customer segment.

**Required entity:**
```json
{
  "@id": "emc:CustomerSegmentScope",
  "name": "CustomerSegmentScope",
  "description": "A B2C customer segment scope within a product. Filters the product graph by CRT customer role and IND individual type to create segment-specific views.",
  "properties": {
    "segmentId": "string",
    "parentProduct": "ref → ProductConfiguration",
    "customerRole": "ref → crt:CustomerRole",
    "individualType": "ref → ind:IndividualType",
    "purchaseBehaviour": "ref → ind:PurchaseBehaviour",
    "segmentVPs": "array — VP instances targeted at this segment",
    "segmentPMF": "ref → PMF validation for this segment",
    "channelModel": "enum — B2C | B2B2C",
    "engagementPattern": "string — subscription | transactional | freemium"
  }
}
```

**Impact:** Enables VHF to have separate graph views for "Fitness Enthusiast" vs "Competitive Athlete" vs "Parent/Carer" — each seeing different VPs, features, and PMF metrics.

### Gap 4: Graph Filter Engine (NEW — Infrastructure)

**Problem:** The ScopingDimension-OrgContext exists in EMC schema but has no operational filter mechanism. Composition rules describe WHAT to do but not HOW to filter.

**Required capability:**
```
GraphFilterEngine:
  Input:  resolved composition + filter context (org, role, segment)
  Output: filtered graph (subset of nodes + edges visible to this context)

  Filters:
    1. OrgContextFilter   → industry, size, maturity, market segment
    2. RoleFilter         → RRR roles active for this org/individual
    3. CRTRoleFilter      → B2B/B2C/B2B2C customer role scoping
    4. INDTypeFilter      → Individual type + purchase behaviour
    5. MaturityGate       → Exclude ontologies above org maturity
    6. LineageDepthFilter → How deep into VE lineage this scope reaches
```

**Implementation options:**
- **Option A (Supabase JSONB):** `resolve_graph_scope()` SQL function with JSONB containment queries (`@>` operator). Aligns with F34.5 JSONB PoC.
- **Option B (Visualiser JS):** Extend `composition-filter.js` with client/segment scoping. Ship in workbench first, migrate to Supabase later.
- **Option C (Both):** Visualiser for design-time exploration, Supabase for runtime serving.

### Gap 5: VE Lineage Instance Resolution (Enhancement — Required)

**Problem:** G23 validates that ontology **schemas** have cross-ontology refs along the lineage chain. But there's no mechanism to validate that **instance data** connects along the same chain. A VHF VP instance should trace back to a VHF OKR instance which traces to PFC VSOM.

**Required:**
- New OAA v7 gate: **G24: Instance Lineage Traceability** — validates that instance data files maintain lineage chain refs
- Enhancement to `InstanceConfiguration`: `lineageInstances` property mapping each lineage position to instance data files
- Enhancement to `ProductConfiguration`: `veLineageEntry` specifying where in the chain this product connects

```json
"lineageInstances": {
  "VSOM-ONT": { "instanceFile": null, "inheritsFrom": "PFC" },
  "OKR-ONT":  { "instanceFile": "okr-instances-vhf-v0.1.0.json" },
  "VP-ONT":   { "instanceFile": "vp-instances-vhf-v1.0.0.jsonld" },
  "PMF-ONT":  { "instanceFile": "pmf-instances-vhf-v0.1.0.json" },
  "EFS-ONT":  { "instanceFile": null, "status": "not-yet-seeded" }
}
```

---

## 5. Proposed EMC v5.0.0 Entity Model

### 5.1 New Entities

| Entity | Tier | Purpose |
|---|---|---|
| `ProductConfiguration` | 2 | Product-level graph scope within a PFI instance |
| `ClientGraphScope` | 3 (B2B) | Per-client org-context filtered graph |
| `CustomerSegmentScope` | 3 (B2C) | Per-segment CRT/IND filtered graph |
| `GraphFilterSpec` | Cross-tier | Declarative filter definition for graph scoping |

### 5.2 New Relationships

| Relationship | Domain → Range | Purpose |
|---|---|---|
| `hasProductConfig` | InstanceConfiguration → ProductConfiguration | Instance contains products |
| `hasClientScope` | ProductConfiguration → ClientGraphScope | Product has client deployments |
| `hasCustomerSegment` | ProductConfiguration → CustomerSegmentScope | Product has B2C segments |
| `filteredByOrgContext` | ClientGraphScope → org-ctx:OrganizationContext | Client scoped by org-context |
| `filteredByCRTRole` | CustomerSegmentScope → crt:CustomerRole | Segment scoped by CRT role |
| `filteredByIndType` | CustomerSegmentScope → ind:IndividualType | Segment scoped by IND type |
| `connectsToLineageAt` | ProductConfiguration → VE lineage position | Product entry to VE chain |
| `inheritsComposition` | ProductConfiguration → InstanceConfiguration | Quasi-OO inheritance |

### 5.3 New Composition Rules

| Rule | Priority | Condition → Action |
|---|---|---|
| `ProductScopeFilter` | 4.5 | IF product specified → filter instance ONTs to product subset |
| `ClientGraphResolution` | 8.5 | IF client org-context specified → apply org-context filter |
| `B2CSegmentFilter` | 8.6 | IF B2C product + CRT role → apply segment-level graph filter |
| `LineageInstanceBinding` | 9 | IF instance data exists for lineage position → bind to lineage |

### 5.4 New Graph Patterns

| Pattern | Description |
|---|---|
| `ProductSubgraph` | Product-scoped subset of instance graph with own VP/CRT/PMF |
| `ClientIsolation` | Org-context filtered view with tenant isolation boundary |
| `SegmentLens` | B2C customer segment view through CRT role + IND type filter |
| `LineageInstance` | Instance-level VE lineage with data file bindings at each position |

---

## 6. Worked Examples

### 6.1 BAIV — B2B Multi-Product Instance

```
PFC (Tier 0)
  └── PFI-BAIV (Tier 1) — InstanceConfiguration
      │   requirementScopes: [PRODUCT, COMPETITIVE, STRATEGIC, AGENTIC]
      │   requiredOntologies: [VP, RRR, PMF, CRT, ORG, ORG-CONTEXT, VSOM,
      │                        OKR, KPI, BSC, INDUSTRY, PE, EFS, MCSB, GRC-FW]
      │   maturityLevel: 1
      │   verticalMarket: "MarTech"
      │   instanceData: BAIV VP, BAIV RRR/RBAC, BAIV DS, BAIV Competitive
      │
      ├── BAIV-AIV (Tier 2) — ProductConfiguration
      │   │   productOntologies: [VP, RRR, PMF, CRT, ORG, ORG-CONTEXT, OKR, KPI, EFS]
      │   │   customerModel: B2B
      │   │   veLineageEntry: OKR (connects to BAIV OKR: "AI Visibility Objectives")
      │   │   crtRoleFilter: [B2B_DecisionMaker, B2B_Influencer, B2B_Champion]
      │   │
      │   ├── Client: Acme Corp (Tier 3) — ClientGraphScope
      │   │     orgProfile: { industry: "MarTech", size: "MidMarket", maturity: 2 }
      │   │     activeRoles: [CMO, VP_Marketing, Marketing_Director]
      │   │     activeCRTRoles: [B2B_DecisionMaker(CMO), B2B_Influencer(VP_Mktg)]
      │   │     clientOKRs: "Increase AI visibility score by 40% in 6 months"
      │   │     clientVPs: "Acme-specific value proposition"
      │   │     graphIsolation: TENANT_ISOLATED
      │   │
      │   └── Client: Beta Ltd (Tier 3) — ClientGraphScope
      │         orgProfile: { industry: "AdTech", size: "SME", maturity: 1 }
      │         activeRoles: [Founder, Marketing_Manager]
      │         maturityFilter: excludes KPI-ONT, GA-ONT, BSC-ONT (maturity < 3)
      │
      └── BAIV-CompIntel (Tier 2) — ProductConfiguration
            productOntologies: [INDUSTRY, PORTFOLIO, ORG-CONTEXT, ORG, VSOM]
            customerModel: B2B
            veLineageEntry: VSOM (strategic-level, no product VP)
            requirementScopes: [STRATEGIC, COMPETITIVE]
```

### 6.2 VHF — B2C Single-Product Instance

```
PFC (Tier 0)
  └── PFI-VHF (Tier 1) — InstanceConfiguration
      │   requirementScopes: [PRODUCT]
      │   requiredOntologies: [VP, RRR, PMF, CRT, ORG, ORG-CONTEXT]
      │   maturityLevel: 0
      │   verticalMarket: "HealthTech"
      │
      └── VHF-Nutrition (Tier 2) — ProductConfiguration
          │   productOntologies: [VP, RRR, PMF, CRT, ORG, ORG-CONTEXT]
          │   customerModel: B2C
          │   veLineageEntry: VP (product-level, direct to VP)
          │   crtRoleFilter: [B2C_Enthusiast, B2C_Casual, B2C_Professional, B2B2C_Intermediary]
          │
          ├── Segment: Fitness Enthusiast (Tier 3) — CustomerSegmentScope
          │     customerRole: crt:B2C_Enthusiast
          │     individualType: ind:FitnessEnthusiast
          │     purchaseBehaviour: { frequency: "monthly", channel: "app", pricePoint: "mid" }
          │     segmentVPs: [VP-001: "Personalised nutrition for fitness goals"]
          │     segmentPMF: { retentionRate: 0.75, nps: 65 }
          │     channelModel: B2C
          │     engagementPattern: "subscription"
          │
          ├── Segment: Competitive Athlete (Tier 3) — CustomerSegmentScope
          │     customerRole: crt:B2C_Professional
          │     individualType: ind:CompetitiveAthlete
          │     purchaseBehaviour: { frequency: "weekly", channel: "app+coach", pricePoint: "premium" }
          │     segmentVPs: [VP-002: "Performance nutrition with coach integration"]
          │     channelModel: B2C
          │     engagementPattern: "subscription"
          │
          └── Segment: Registered Dietitian (Tier 3) — CustomerSegmentScope
                customerRole: crt:B2B2C_Intermediary
                individualType: ind:RegisteredDietitian
                purchaseBehaviour: { frequency: "per-client", channel: "web+API", pricePoint: "professional" }
                segmentVPs: [VP-003: "Client nutrition management toolkit"]
                channelModel: B2B2C
                engagementPattern: "per-seat"
```

### 6.3 ANTQ — B2C/B2B Hybrid Instance

```
PFC (Tier 0)
  └── PFI-ANTQ (Tier 1) — InstanceConfiguration
      │   requirementScopes: [PRODUCT]
      │   requiredOntologies: [VP, RRR, PMF, CRT, ORG, ORG-CONTEXT, ANTIQUES]
      │   maturityLevel: 0
      │   verticalMarket: "Antiques & Collectibles"
      │
      └── ANTQ-Valuations (Tier 2) — ProductConfiguration
          │   customerModel: B2C/B2B  (hybrid)
          │
          ├── B2B Segment: Auction House (Tier 3) — ClientGraphScope
          │     orgProfile: { industry: "Fine Art & Antiques", size: "SME" }
          │     activeCRTRoles: [B2B_InstitutionalBuyer, B2B_Specialist, B2B_Marketplace]
          │     graphIsolation: SHARED
          │
          ├── B2C Segment: Private Collector (Tier 3) — CustomerSegmentScope
          │     customerRole: crt:B2C_PrivateCollector
          │     individualType: ind:PrivateCollector
          │     channelModel: B2C
          │
          └── B2B2C Segment: Insurance Valuer (Tier 3) — CustomerSegmentScope
                customerRole: crt:B2B2C_InsuranceValuer
                channelModel: B2B2C
```

---

## 7. VE Lineage Chain Connectivity Per Tier

### 7.1 Full Lineage with Tier Binding Points

```
VSOM ─────→ OKR ──────→ VP ───────→ PMF ──────→ EFS
 │           │           │           │           │
 │ Tier 0:  VSOM       OKR        VP          PMF        EFS
 │ (PFC)    Schema     Schema     Schema      Schema     Schema
 │           │           │           │           │
 │ Tier 1:  PFI-VSOM   PFI-OKR    PFI-VP     PFI-PMF    PFI-EFS
 │ (PFI)    instance   instance   instance   instance   instance
 │           │           │           │           │
 │ Tier 2:  (inherits)  Product    Product    Product    Product
 │ (Product)            OKRs       VPs        PMFs       Features
 │                       │           │           │           │
 │ Tier 3:              Client     Client     Client     Client
 │ (Client)             OKRs       VPs        PMFs       Features
```

### 7.2 Lineage Join Patterns Per Tier

| Join Pattern | From → To | Tier | Purpose |
|---|---|---|---|
| JP-VE-001 | vsom:Strategy → okr:Objective | 0→1 | PFC strategy cascades to instance OKRs |
| JP-VE-002 | okr:KeyResult → vp:ValueProposition | 1→2 | Instance OKRs drive product VPs |
| JP-VP-RRR-001 | vp:Problem → rrr:Risk | 2 | Problems are risks (standing convention) |
| JP-VP-RRR-002 | vp:Solution → rrr:Requirement | 2 | Solutions are requirements |
| JP-VP-RRR-003 | vp:Benefit → rrr:Result | 2 | Benefits are measurable results |
| JP-CRT-VP-001 | crt:CustomerRole → vp:TargetCustomer | 2→3 | CRT role scopes VP targeting |
| JP-IND-CRT-001 | ind:IndividualType → crt:CustomerRole | 3 | Individual type classifies CRT role |
| JP-VP-PMF-001 | vp:ValueProposition → pmf:ProductMarketFit | 2→3 | VP validates against PMF |
| JP-PMF-EFS-001 | pmf:Feature → efs:FeatureSet | 2→3 | PMF features become EFS build items |

### 7.3 Instance Data Files Needed Per Lineage Position

| Lineage Position | BAIV | VHF | ANTQ | W4M | AIRL |
|---|---|---|---|---|---|
| VSOM instances | Missing | Missing | Missing | Missing | Missing |
| OKR instances | Missing | Missing | Missing | Missing | Missing |
| VP instances | Exists* | Missing | Missing | Missing | Exists |
| RRR instances | RBAC exists | Missing | Missing | Missing | Missing |
| PMF instances | Missing | Missing | Missing | Missing | Missing |
| CRT instances | Missing | Exists | Exists | Missing | Missing |
| IND instances | Missing | Exists | Missing | Exists | Exists |
| EFS instances | Missing | Missing | Missing | Missing | Missing |

*BAIV VP exists in PFI-BAIV-AIV-DEV repo, not in ontology library.

**Key finding:** No instance has complete VE lineage data. VSOM, OKR, PMF, and EFS instances are **universally missing**.

---

## 8. Implementation Roadmap

### Phase 1: EMC v5.0.0 Schema (Enables Tier 2 — Product Graphs)

**Epic scope:** New feature under Epic 19 or new Epic 41

1. Add `ProductConfiguration` entity to EMC-ONT
2. Add `hasProductConfig` relationship (InstanceConfiguration → ProductConfiguration)
3. Add `connectsToLineageAt` relationship (ProductConfiguration → VE lineage)
4. Add `ProductScopeFilter` composition rule (priority 4.5)
5. Add `ProductSubgraph` graph pattern
6. Update VHF worked example with product tier
7. Validate: all existing tests pass + new product-scope tests

### Phase 2: Client/Segment Scoping (Enables Tier 3 — Client Graphs)

**Epic scope:** Epic 9H (#147) — Client-Org and Vertical Market Configuration

1. Add `ClientGraphScope` entity (B2B)
2. Add `CustomerSegmentScope` entity (B2C)
3. Add `GraphFilterSpec` entity (declarative filter definitions)
4. Add `ClientGraphResolution` and `B2CSegmentFilter` composition rules
5. Add `ClientIsolation` and `SegmentLens` graph patterns
6. Add `filteredByOrgContext`, `filteredByCRTRole`, `filteredByIndType` relationships
7. Implement filter logic in `composition-filter.js` (visualiser)
8. Add BAIV, VHF, ANTQ worked examples with client/segment tiers

### Phase 3: VE Lineage Instance Binding (Enables Full Traceability)

**Epic scope:** Epic 36 (#524) — VSOM Strategy Audit

1. Add `lineageInstances` property to InstanceConfiguration and ProductConfiguration
2. Implement G24 gate: Instance Lineage Traceability
3. Seed missing instance data: VSOM, OKR, PMF instances for BAIV and VHF
4. Validate lineage traversal end-to-end: VSOM → OKR → VP → PMF → EFS

### Phase 4: Runtime Graph Filter Engine (Enables Production Serving)

**Epic scope:** Epic 19 (#196) — PFI Graph-Scope Rules + F34.5 JSONB PoC

1. Implement `resolve_graph_scope()` in Supabase (JSONB containment queries)
2. Implement quasi-OO cascade resolution (PFC → Instance → Product → Client deepMerge)
3. Multi-tenant isolation (row-level security per client graph scope)
4. API endpoint for resolved graph serving

---

## 9. Relationship to Existing Epics

| Epic | What It Covers | Gap It Fills |
|---|---|---|
| **Epic 19 (#196)** | PFI Graph-Scope Rules | Gaps 1, 2, 4 — product/client graph scoping |
| **Epic 9H (#147)** | Client-Org & Vertical Market | Gap 2 — client org-context filtering |
| **Epic 9F (#140)** | Requirement-Scoped Composition | Gap 4 — filter engine |
| **Epic 9E (#139)** | PFI Instance Selection | Gap 1 — instance/product view switching |
| **Epic 36 (#524)** | VSOM Strategy Audit | Gap 5 — lineage instance binding |
| **Epic 38 (#560)** | Strategy Analysis Engine | Supporting — SA pattern pipeline |
| **Epic 39 (#569)** | PFI Strategy Cascade | Supporting — artifact distribution |
| **Epic 40 (#577)** | Graphing Workbench Evolution | UI — filter engine in workbench |

---

## 10. Summary: Five Things to Build

| # | What | Why | Effort |
|---|---|---|---|
| 1 | **ProductConfiguration entity** in EMC v5.0.0 | Separate product from instance — enables multi-product PFIs | M |
| 2 | **ClientGraphScope + CustomerSegmentScope** entities | Enable per-client (B2B) and per-segment (B2C) graph views | L |
| 3 | **Graph filter engine** in composition-filter.js | Operationalise the scoping dimensions that are currently declarative-only | L |
| 4 | **VE lineage instance data** for BAIV + VHF | Without instance data at each lineage position, the chain is schema-only | M |
| 5 | **G24 Instance Lineage Traceability** gate | Validate that instance data maintains lineage chain integrity | S |

**Critical path:** Items 1 and 3 unblock everything else. Item 4 (seeding lineage data) can run in parallel.
