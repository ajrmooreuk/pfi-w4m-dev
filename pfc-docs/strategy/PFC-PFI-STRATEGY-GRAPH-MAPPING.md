# PFC→PFI Strategy Graph Mapping — Instance-Specific Graph Composition & Cross-Series Filtering

**Version:** 1.0.0
**Date:** 2026-02-18
**Purpose:** Define how PFC→PFI[instance] strategy graph scoping should work — analogous to DS brand resolution — enabling instance-specific cross-series graphs with connection filtering
**Companion:** [INTERACTION-LOGIC-TREE.md](INTERACTION-LOGIC-TREE.md) — componentised interaction reference

---

## 1. The Problem

The ontology library (42 ontologies, 5 series) serves as the PFC (PF-Core) foundation. Five PFI instances (BAIV, W4M-WWG, W4M-EOMS, AIRL-CAF-AZA, VHF) each need a **different cross-series slice** of this library — with different strategy ontologies active, different compliance frameworks relevant, and different cross-ontology connections visible.

Today, the DS brand resolution pattern successfully maps PFC→PFI for **visual theming** (colours, tokens, archetypes). But the analogous mapping for **graph content** — which ontologies, entities, relationships, and cross-series connections are relevant to a specific PFI instance — is partially built but not yet connected end-to-end.

---

## 2. The DS Brand Resolution Pattern (Reference — Working)

This pattern is fully operational and serves as the template for strategy graph resolution.

```
PFC Core Ontology Definitions
  │  DS-ONT schema: DesignSystem, TokenCategory, PrimitiveToken,
  │  SemanticToken, ComponentToken, BrandVariant, DesignRule
  │
  ▼  resolveDSBrandForPFI(config)          [ds-loader.js:745-767]
PFI Instance Config
  │  Tier 1: designSystemConfig.brand → state.dsInstances lookup
  │  Tier 2: designSystemConfig.fallback
  │  Tier 3: brands[0].toLowerCase() → case-insensitive match
  │
  ▼  applyCSSVars(generateCSSVars(parsed))  [ds-loader.js:513-586]
Instance-Specific Theme Applied
  │  --viz-archetype-{type} overrides (DR-SEMANTIC-005)
  │  --viz-edge-{category} overrides (DR-SEMANTIC-006)
  │  --viz-accent, --viz-surface-default, etc.
  │  refreshArchetypeCache() picks up brand colours
  │
  ▼  State: state.activeDSBrand, state.dsAppliedCSSVars, state.brandContext
```

**Key design properties:**
- Three-tier cascade with fallback
- Single function entry point (`resolveDSBrandForPFI`)
- Declarative config in registry (`designSystemConfig`)
- Automatic side-effects (CSS vars applied, cache refreshed)
- Persistent via localStorage

---

## 3. Proposed Strategy Graph Resolution Pattern

The same cascade pattern, applied to graph content instead of visual theme.

```
PFC Core Ontology Library (42 ontologies, 5 series, 8 graph series)
  │  All ontologies loaded via multi-loader.js
  │  Cross-references detected (3 passes: registry, namespace scan, dependencies)
  │  Bridge nodes identified (threshold: 3+ referencing ontologies)
  │
  ▼  resolveStrategyGraphForPFI(instanceId)  [NEW — emc-composer.js]
PFI Instance Config (from registry pfiInstances[])
  │  requirementScopes: ['STRATEGIC', 'PRODUCT', 'COMPLIANCE', ...]
  │  graphScopeRules: [NEW — EMC v3.0.0 scope rules]
  │  productEntityBindings: [NEW — which entities belong to which products]
  │
  ▼  composeInstanceGraph(scopeRules)  [Epic 19, F19.2 — emc-composer.js]
Composed FilteredView
  │  visibleNamespaces: ontologies in PFI scope (full opacity)
  │  contextGhostNamespaces: context ontologies (55% opacity, dashed)
  │  hiddenNamespaces: irrelevant ontologies (excluded)
  │  activeSeriesSet: which series have visible content
  │  filteredCrossEdges: cross-ontology edges relevant to instance
  │
  ▼  applyCompositionFilter(composition)  [composition-filter.js]
Instance-Specific Strategy Graph Rendered
  │  Tier 0: only active series shown, proportional sizing
  │  Tier 1: only PFI-relevant ontologies + context ghosts
  │  Multi: filtered cross-series connections
  │  ConnMap: instance-scoped connection density
  │
  ▼  State: state.pfiContext (unified — HR-003)
     { instanceId, brand, composition, instanceData, scopeRules }
```

---

## 4. What Exists vs What's Needed

### 4.1 Existing Infrastructure (Built, Working)

| Component | Status | File | Notes |
|---|---|---|---|
| Registry `pfiInstances[]` schema | Done | `ont-registry-index.json` | BAIV has full config; others minimal |
| `listPFIInstances(registryIndex)` | Done | `pfi-loader.js` | Returns array of PFI configs |
| `composeOntologySet(category, maturityLevel)` | Done | `emc-composer.js` | 9 static categories |
| `composeMultiCategory(categories, opts)` | Done | `emc-composer.js` | Union of multiple categories |
| `applyCompositionFilter(composition)` | Done | `composition-filter.js` | FilteredView bridge |
| `getActiveFilteredView()` | Done | `composition-filter.js` | Read by all 5 render functions |
| `clearCompositionFilter()` | Done | `composition-filter.js` | Reset to full graph |
| `FilteredView` data structure | Done | `composition-filter.js` | visible/ghost/hidden namespace sets |
| PFI context toggle (Core/Instance) | Partial | `app.js:2275` | Toggle exists, incomplete wiring |
| Instance picker dropdown | Partial | `app.js:2520-2570` | Populated from registry |
| DS brand auto-resolution | Done | `ds-loader.js:745` | Three-tier cascade |
| Instance data overlay (`inst::` nodes) | Done | `graph-renderer.js:644` | Multi-graph view only |
| Context bar / border glow | Partial | `app.js` | F8.8 stories mostly unstarted |

### 4.2 Gaps (Not Yet Built)

| Gap | Required For | Epic/Issue | Complexity |
|---|---|---|---|
| `resolveStrategyGraphForPFI()` | Analogous to `resolveDSBrandForPFI` | NEW | Medium — wraps existing compose functions |
| Instance-customisable scope rules | Per-PFI ontology inclusion rules | #196 (Epic 19, F19.1) | High — EMC v3.0.0 schema |
| `composeInstanceGraph()` | Entity-level (not just namespace) filtering | #211 (S19.2.5) | High — core composition engine |
| Graph-scope rule evaluation | `evaluateScopeCondition()`, `evaluateScopeRules()` | #208-#209 | Medium |
| Canonical graph snapshots (freeze) | Version-locked PFI templates | #215 (F19.3) | Medium |
| `resolvePersonaWorkflowGraph()` | ICP→persona→workflow scoping | #212 (S19.2.6) | Medium |
| Product/service entity binding | Which entities belong to which BAIV products | #229 (F19.5) | Medium |
| Instance-scoped Tier 0 | Filtered series + proportional sizing | #139 (9E.3) | Low |
| Instance-scoped Connection Map | Instance-relevant cross-ontology edges only | NEW | Low — extends ConnMap |
| Unified PFI state object | Merge `activePFI` + `activeInstanceId` | HR-003 | Low — refactor |
| Strategy cascade visualisation | VSOM→OKR→KPI drill-through lens | #146 (9G) | High |
| Context identity bar (full) | Prominent PFI context indicators | #145 (F8.8) | Medium |

---

## 5. PFI Instance→Strategy Graph Mapping

### 5.1 Instance-to-Ontology Matrix

| Ontology | Series | BAIV | W4M-WWG | W4M-EOMS | AIRL-CAF | VHF |
|---|---|---|---|---|---|---|
| VSOM-ONT | VE | **Y** | **Y** | **Y** | **Y** | **Y** |
| OKR-ONT | VE | **Y** | **Y** | **Y** | **Y** | **Y** |
| VP-ONT | VE | **Y** | **Y** | . | . | **Y** |
| RRR-ONT | VE | **Y** | **Y** | . | . | . |
| PMF-ONT | VE | **Y** | **Y** | . | . | . |
| KPI-ONT | VE | **Y** | **Y** | **Y** | **Y** | . |
| BSC-ONT | VE/SA | **Y** | . | . | . | . |
| INDUSTRY-ONT | VE/SA | . | . | . | . | . |
| REASON-ONT | VE/SA | . | . | . | . | . |
| MACRO-ONT | VE/SA | . | . | . | . | . |
| PORTFOLIO-ONT | VE/SA | . | . | . | . | . |
| NARRATIVE-ONT | VE/SC | . | . | . | . | . |
| CASCADE-ONT | VE/SC | . | . | . | . | . |
| CULTURE-ONT | VE/SC | . | . | . | . | . |
| VIZSTRAT-ONT | VE/SC | . | . | . | . | . |
| PPM-ONT | PE | **Y** | **Y** | **Y** | . | . |
| PE-ONT | PE | **Y** | **Y** | **Y** | . | . |
| EFS-ONT | PE | **Y** | **Y** | . | . | . |
| EA-ONT | PE | . | . | **Y** | . | . |
| EA-CORE-ONT | PE | . | . | **Y** | . | . |
| EA-TOGAF-ONT | PE | . | . | **Y** | . | . |
| EA-MSFT-ONT | PE | . | . | . | **Y** | . |
| DS-ONT | PE | **Y** | **Y** | . | . | . |
| ORG-ONT | Foundation | **Y** | **Y** | **Y** | **Y** | **Y** |
| ORG-CONTEXT-ONT | Foundation | **Y** | **Y** | **Y** | **Y** | . |
| CTX-ONT | Foundation | **Y** | **Y** | **Y** | **Y** | . |
| GA-ONT | Foundation | **Y** | . | . | . | . |
| ORG-MAT-ONT | Foundation | . | . | . | **Y** | . |
| EMC-ONT | Orchestration | **Y** | **Y** | **Y** | **Y** | . |
| GRC-FW-ONT | RCSG | . | . | . | **Y** | . |
| NCSC-CAF-ONT | RCSG | . | . | . | **Y** | . |
| AZALZ-ONT | RCSG | . | . | . | **Y** | . |
| MCSB-ONT | RCSG | . | . | . | **Y** | . |
| MCSB2-ONT | RCSG | . | . | . | **Y** | . |
| RMF-IS27005-ONT | RCSG | . | . | . | **Y** | . |
| GDPR-ONT | RCSG | . | **Y** | . | . | . |
| PII-ONT | RCSG | . | **Y** | . | . | . |
| DSPT-ONT | RCSG | . | . | . | **Y** | . |

### 5.2 Instance Cross-Series Profiles

| Instance | VE | PE | Foundation | RCSG | Orchestration | Active Series |
|---|---|---|---|---|---|---|
| **PFI-BAIV** | 8/15 (53%) | 5/8 (63%) | 4/5 (80%) | 0/10 | 1/1 (100%) | 4 of 5 |
| **PFI-W4M-WWG** | 6/15 (40%) | 4/8 (50%) | 3/5 (60%) | 2/10 (20%) | 1/1 | 5 of 5 |
| **PFI-W4M-EOMS** | 3/15 (20%) | 5/8 (63%) | 3/5 (60%) | 0/10 | 1/1 | 4 of 5 |
| **PFI-AIRL-CAF** | 3/15 (20%) | 1/8 (13%) | 4/5 (80%) | 7/10 (70%) | 1/1 | 5 of 5 |
| **PFI-VHF** | 3/15 (20%) | 0/8 | 1/5 (20%) | 0/10 | 0/1 | 2 of 5 |

**Key insight:** Each instance has a unique cross-series fingerprint. BAIV is VE+PE heavy (strategy + process). AIRL is RCSG heavy (compliance). W4M-EOMS is PE heavy (EA). This is why instance-specific filtering must work across series boundaries — no single series toggle can produce the right view.

### 5.3 Instance Cross-Ontology Connection Profiles

The cross-ontology edges that matter differ by instance:

| Instance | Key Cross-Series Connections | Bridge Entities |
|---|---|---|
| **BAIV** | VSOM→OKR (cascade), VP↔RRR (alignment), EFS↔PE (process), EMC→all (orchestration) | Vision, Objective, Process, Persona |
| **W4M-WWG** | VSOM→OKR, VP↔RRR, GDPR→PII (privacy), EMC→all | Vision, Risk, DataSubject |
| **W4M-EOMS** | OKR→EA (alignment), EA-CORE↔EA-TOGAF (framework), PPM→PE (delivery), EMC→all | Capability, Architecture, Programme |
| **AIRL-CAF** | NCSC-CAF→MCSB (control mapping), AZALZ→MCSB2 (Azure), GRC-FW→all RCSG (governance hub), ORG-MAT→CTX | Control, Assessment, Maturity |
| **VHF** | VSOM→OKR (minimal cascade) | Vision, Objective |

---

## 6. Epic/Feature/Story Cross-Reference Matrix

### 6.1 Core Dependency Chain

```
FOUNDATION (CLOSED)
  └── Epic 9D (#138) — PFI Graph Lifecycle Architecture
        ├── composition-filter.js (190 lines, 28 tests)
        ├── pfi-loader.js (170 lines, 17 tests)
        └── emc-composer.js (9 categories, dependency map)

INSTANCE SELECTION (OPEN — prerequisite for strategy graph)
  ├── Epic 9E (#139) — PFI Instance Selection & Core/Instance View
  │     ├── 9E.1: PFC/PFI Context Toggle (partial)
  │     ├── 9E.2: Instance Picker Dropdown (partial)
  │     ├── 9E.3: Instance-Scoped Tier 0 (not started)
  │     ├── 9E.4: Instance Data Overlay (done — graph-renderer.js:644)
  │     └── 9E.5: Requirement Scope Chips (not started)
  │
  ├── Epic 9F (#140) — Requirement-Scoped Graph Composition
  │     ├── 9F.1: Requirement Category Panel (partial)
  │     ├── 9F.2: Multi-Category Composition (done)
  │     ├── 9F.3: Maturity Slider (not started)
  │     └── 9F.4: Compliance Scope Toggle (not started)
  │
  └── F8.8 (#145) — Core-to-PFI Instance Context Switch UI/UX
        ├── 8.8.1: Context Identity Bar (not started)
        ├── 8.8.2: Graph Border Glow (partial)
        ├── 8.8.3: Context Switch Modal (not started)
        ├── 8.8.4: Node Badge Indicators (not started)
        ├── 8.8.5: Context-Aware Title/Favicon (not started)
        └── 8.8.6: Quick-Switch Drawer (not started)

STRATEGY GRAPH ENGINE (OPEN — the core gap)
  └── Epic 19 (#196) — PFI Graph-Scope Rules (EMC v3.0.0)
        ├── F19.1 (#197): Graph-Scope Rule Ontology Modelling (8 stories)
        ├── F19.2 (#206): PFC Design-Time Graph Composition Engine (8 stories)
        ├── F19.3 (#215): Set & Freeze — Canonical Graph Snapshots (6 stories)
        ├── F19.4 (#222): PFI Instance Graph Generation & Personas (6 stories)
        ├── F19.5 (#229): Product/Service Entity Binding (5 stories)
        ├── F19.6 (#235): External Data Inbound (CANDIDATE, 6 stories)
        └── F19.7 (#242): External Data Outbound (CANDIDATE, 6 stories)
        Total: 7 features, 45 stories, 0/45 done

STRATEGIC LENSES (OPEN — consumers of strategy graph)
  ├── Epic 9G (#146) — Strategic Lens VESM BSC Role Authority
  ├── Epic 9H (#147) — Client-Org and Vertical Market Configuration
  ├── Epic 9I (#148) — Venn Overlap and Inheritance Analysis
  └── F17.1 (#142) — Filterable Graph & Componentised Data Architecture

PLATFORM STRATEGY (OPEN — vision and architecture)
  ├── Epic 34 (#518) — PF-Core VSOM Strategy & Architecture
  │     ├── F34.1: PFC Graph Series Ontology Mapping
  │     ├── F34.2: PFI Instance VSOM Alignment
  │     ├── F34.4: Graph Hierarchy & Isolation Design
  │     └── F34.8: Extensibility Decision Tree
  │
  └── Epic 34 (#514) — Graphing Workbench & Extensibility
        └── L2: Strategy Architecture layer (VSOM, BSC, OKR + VSEM)

COMPLIANCE & EA (OPEN — series-specific content)
  ├── Epic 30 (#370) — GRC Series Architecture
  ├── Epic 29 (#356) — VSOM-to-Value Proposition (NCSC CAF)
  ├── Epic 33 (#505) — Azure Graph & Landing Zone (AIRL)
  └── Epic 17 (#141) — Engineering EA into PF Core
```

### 6.2 Implementation Sequence (Recommended)

| Phase | Epic/Feature | Deliverable | Prerequisite |
|---|---|---|---|
| **P1** | HR-003 (refactor) | Unify `state.activePFI` + `state.activeInstanceId` into `state.pfiContext` | None |
| **P2** | 9E.3 + 9E.5 | Instance-scoped Tier 0 + requirement scope chips | P1 |
| **P3** | F8.8.1 + F8.8.5 | Context identity bar + tab/favicon | P1 |
| **P4** | F19.1 (#197) | EMC v3.0.0 scope rule entities | None (ontology modelling) |
| **P5** | F19.2 + F19.5 | Composition engine + product bindings | P4 |
| **P6** | `resolveStrategyGraphForPFI()` | NEW function — wraps P5 output through composition filter | P1 + P5 |
| **P7** | F19.3 (#215) | Canonical graph snapshots (freeze) | P5 |
| **P8** | F19.4 (#222) | PFI instance graph generation + persona workflows | P6 + P7 |
| **P9** | F8.8.2-6 | Border glow, context switch modal, node badges | P3 + P6 |
| **P10** | 9G (#146) | Strategic lens (VSOM→OKR→KPI cascade view) | P6 |

### 6.3 Cross-Programme Dependencies

| This Feature | Depends On | From Epic |
|---|---|---|
| `resolveStrategyGraphForPFI()` | PFI registry config enrichment | 9E, 9H |
| Instance-scoped ConnMap | Cross-edge filtering per PFI | 9D (done), 9E |
| AIRL compliance graph | GRC-FW-ONT v3.0.0 (hub) | 30 (#370) |
| BAIV agent-scoped graph | Agent entity schema (OAA v7) | 21 (#270) |
| W4M-EOMS EA graph | EA-CORE + EA-TOGAF ontologies | 17 (#141) |
| Persona workflow graphs | EFS persona mapping + ICP hierarchy | 19, F19.4 |
| Design token→strategy link | DS brand resolution PFI binding | 8 (#80), 8B (done) |
| Supabase graph storage | JSONB schema + RLS | 10A (#127), F34.5 |

---

## 7. Interaction Component Impact

How existing interaction components (from [INTERACTION-LOGIC-TREE.md](INTERACTION-LOGIC-TREE.md)) are affected by PFC→PFI strategy graph mapping:

| Component | Current | After Strategy Graph Mapping |
|---|---|---|
| **FLT-001** CompositionFilter | Namespace-level visibility | Entity-level visibility (scope rules) |
| **CTX-001** PFIContextSwitch | Brand + basic composition | Brand + strategy graph + scope rules + entity bindings |
| **CTX-004** InstanceDataOverlay | Multi-graph only, `inst::` nodes | All tiers, integrated with composition filter |
| **NAV-003** DrillToOntology | Any ontology | PFI-scoped: ghost ontologies show context indicator |
| **NAV-004** CrossEdgeNavigate | All cross-ref edges | Filtered to PFI-relevant connections |
| **FLT-007** SeriesHighlight | Full series highlight | PFI-aware: only highlight PFI-active ontologies within series |
| **SEL-001** ShowNodeDetails | Entity details | + Product binding, PFI override delta, persona scope |
| **NEW** StrategyLens (9G) | N/A | VSOM→OKR→KPI cascade drill-through |
| **NEW** PersonaFilter (19.4) | N/A | ICP role → EFS persona → workflow scope |
| **NEW** ScopeRulePreview (19.1) | N/A | Visualise scope rule coverage on graph |

---

## 8. Architecture Diagram — Target State

```
User selects PFI Instance (e.g. "BAIV")
  │
  ├─→ CTX-001: PFIContextSwitch
  │     ├─→ CTX-002: DSBrandResolution (visual theme)
  │     │     └── CSS vars applied, archetype cache refreshed
  │     │
  │     ├─→ resolveStrategyGraphForPFI(instanceId) [NEW]
  │     │     ├── Read pfiInstances[instanceId] from registry
  │     │     ├── Load graphScopeRules from EMC v3.0.0
  │     │     ├── Evaluate scope conditions against ontology library
  │     │     ├── Resolve product entity bindings
  │     │     └── Return composed FilteredView + entity-level bindings
  │     │
  │     ├─→ FLT-001: applyCompositionFilter(composition)
  │     │     ├── visibleNamespaces: PFI-scoped ontologies
  │     │     ├── contextGhostNamespaces: context ontologies (dimmed)
  │     │     ├── hiddenNamespaces: irrelevant (excluded)
  │     │     └── filteredCrossEdges: instance-relevant connections
  │     │
  │     ├─→ CTX-005: updateContextBar() (identity bar)
  │     ├─→ CTX-006: updateGraphBorderGlow() (brand accent)
  │     └─→ Re-render current view
  │
  └─→ All render functions check getActiveFilteredView()
        ├── Tier 0: only PFI-active series, proportional sizing
        ├── Tier 1: PFI-relevant ontologies + ghost context
        ├── Multi: filtered cross-series connections
        ├── ConnMap: instance-scoped connection density
        └── Tier 2: entity detail includes product binding + PFI context
```

---

## 9. Existing File Impact Assessment

| File | Lines | Change Scope | Phase |
|---|---|---|---|
| `emc-composer.js` | ~350 | +330: scope rules, `resolveStrategyGraphForPFI()`, persona resolution | P4-P6 |
| `composition-filter.js` | ~200 | +80: entity-level filtering, scoped cross-edges | P5-P6 |
| `state.js` | ~370 | +15: `pfiContext` unified state, scope rule state | P1 |
| `pfi-loader.js` | ~170 | +40: snapshot inheritance, enriched instance loading | P7 |
| `graph-renderer.js` | ~1850 | ~30: scope-aware render wrappers, instance-scoped ConnMap | P6-P8 |
| `app.js` | ~4700 | ~80: unified PFI state, context bar/glow completion, strategy lens UI | P1-P3, P9 |
| `ui-panels.js` | ~1400 | ~40: product binding display, persona scope in detail panel | P8 |
| `ds-loader.js` | ~870 | ~10: align brand resolution with unified PFI state | P1 |
| `ont-registry-index.json` | ~600 | ~100: enriched PFI configs for all 5 instances | P2-P4 |
| `pf-EMC-ONT-v2.0.0.jsonld` | varies | Major: → v3.0.0 (6 new entity types, scope rules, BAIV examples) | P4 |

---

## Related Strategy Documents

- [INTERACTION-LOGIC-TREE.md](INTERACTION-LOGIC-TREE.md) — Componentised interaction reference (40 components, 5 layers)
- [BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy.md](BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy.md) — Master VSOM (6 strategies, BSC objectives)
- [BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md](BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md) — Registry cascade architecture
- [BRIEFING-Epic34-Graph-Series-Ontology-Mapping.md](BRIEFING-Epic34-Graph-Series-Ontology-Mapping.md) — 8 graph series → 42 ontologies
- [BRIEFING-Epic34-PFI-Instance-Readiness.md](BRIEFING-Epic34-PFI-Instance-Readiness.md) — 5 PFI readiness scores
- [DESIGN-DIRECTOR-TOOLKIT-Graphing-Workbench-Decision-Tree.md](DESIGN-DIRECTOR-TOOLKIT-Graphing-Workbench-Decision-Tree.md) — Workbench extensibility
