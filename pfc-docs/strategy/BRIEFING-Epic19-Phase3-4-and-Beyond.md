# Epic 19 Phase 3–4 Plans & PFI Graph Product Launch Roadmap

**Date**: 2026-02-22
**Author**: AJR Moore / Claude Code
**Status**: Briefing for team — Epic 19 progress + remaining work estimate

---

## Current State: Epic 19 Progress

| Phase | Feature | Stories | Status |
|-------|---------|---------|--------|
| 1 | F19.1: Graph-Scope Rule Ontology Modelling | 8/8 | Done |
| 2 | F19.2: PFC Design-Time Graph Composition Engine | 8/8 | Done |
| 2 | F19.5: Product/Service Entity Binding | 5/5 | Done |
| 3 | **F19.3: Set & Freeze — Canonical Graph Snapshots** | 0/6 | **Next** |
| 4 | **F19.4: PFI Instance Graph Generation & Persona Workflows** | 0/6 | Pending |
| 5 | F19.6: External Data Inbound (CANDIDATE) | 0/6 | Backlog |
| 6 | F19.7: External Data Outbound (CANDIDATE) | 0/6 | Backlog |

**21/45 stories done. 12 core stories remaining (F19.3 + F19.4).**

---

## Phase 3: F19.3 — Set & Freeze — Canonical Graph Snapshots

**6 stories** (#216–#221) | ~150 lines in emc-composer.js + ~500 lines tests

### Purpose

Admin finalises a composed graph and locks it as an immutable versioned snapshot. PFI instances inherit frozen snapshots. Version chains enable diff comparison.

### Functions

| Story | Function | What it does |
|-------|----------|-------------|
| S19.3.1 (#216) | `freezeComposedGraph(spec, version, adminRef)` | Deep-clones a ComposedGraphSpec, stamps version/timestamp/admin, sets status to 'locked', Object.freeze()s the result, persists to state + localStorage. If a previous version exists, marks it 'superseded' and links via parentSnapshot. |
| S19.3.2 (#217) | `getCanonicalSnapshot(snapshotId)` | Retrieves a frozen snapshot by ID. Returns an immutable copy (not a reference). Returns null for unknown IDs. |
| S19.3.3 (#218) | `listSnapshotVersions(specId)` | Returns the version chain for a spec — all snapshots sorted by semver. Each entry: `{ snapshotId, version, frozenAt, changeControlStatus, parentSnapshot }`. |
| S19.3.4 (#219) | `inheritSnapshot(instanceId, snapshotId)` | Links a PFI instance to a locked snapshot. Validates status is 'locked'. Triggers composeInstanceGraph() using the frozen spec + live instance data. Multiple instances can inherit the same snapshot. |
| S19.3.5 (#220) | Immutability enforcement (BR-EMC-014) | Deep Object.freeze() on all locked snapshots. Modification attempts rejected. Old snapshot → 'superseded' when new version created. Version bump validation. |
| S19.3.6 (#221) | `diffSnapshots(oldId, newId)` | Reuses Epic 4 diff engine pattern. Compares entities added/removed/modified, edges changed, scope rules changed, ontology sources changed. Handles first version (everything is 'added'). |

### Files Changed

| File | Change |
|------|--------|
| `js/emc-composer.js` | +4 exported functions, deep freeze helper (~150 lines) |
| `js/state.js` | +1 property (snapshotVersionIndex) |
| `tests/snapshot-freeze.test.js` | NEW (~500 lines, ~45 tests) |

### Key Design Decisions

- **No IndexedDB extension** — `state.canonicalSnapshots` Map + localStorage persistence follows existing `compositionManifests` pattern
- **Deep freeze** — recursive `Object.freeze()` utility prevents accidental mutation
- **Semver chain** — parentSnapshot links form a version DAG; `listSnapshotVersions` returns sorted chain
- **Diff reuse** — leverages existing `diffOntologies()` shape from diff-engine.js, adapted for graph node/edge comparison

---

## Phase 4: F19.4 — PFI Instance Graph Generation & Persona Workflows

**6 stories** (#223–#228) | ~130 lines across 3 files + UI changes

### Purpose

Connect frozen templates to PFI runtime. Each instance inherits a snapshot and gets populated with product/persona-specific instance data. Persona selector enables switching between ICP roles (CMO, SoC Manager, Content Manager) to see persona-appropriate workflow graphs.

### Functions

| Story | Function | What it does |
|-------|----------|-------------|
| S19.4.1 (#223) | `generatePFIGraph(instanceId, snapshotId)` | Orchestrates the full pipeline: load snapshot → evaluate scope rules → load instance data → assemble composed graph → store in state.composedPFIGraph. |
| S19.4.2 (#224) | `buildScopedFilteredView(composedGraph, scopeResult)` | Extends existing `buildFilteredView()` with entity-level visibility: visible (in composed data), ghost (in schema but not data), hidden (excluded by rules). Returns ScopedFilteredView with `getScopedNodeRenderMode()` and `isScopedEdgeVisible()`. |
| S19.4.3 (#225) | `generatePersonaWorkflow(instanceId, icpRef)` | Calls `resolvePersonaWorkflowGraph()` scoped by ICP seniority level. Maps VP problems → EFS Epics → Features → UserStories at the persona's scope level (Strategic/Tactical/Operational). |
| S19.4.4 (#226) | Scope-aware rendering integration | Minimal wrapper in graph-renderer.js (~4 render points). When `state.scopeRulesActive`, delegates to scoped functions; otherwise falls through to existing. Fully backward compatible. |
| S19.4.5 (#227) | Product/persona selector UI | Product dropdown (from config.products[]) + persona dropdown (from ICP hierarchy). Selecting a persona triggers `generatePersonaWorkflow()` and re-renders. "All" option shows full composed graph. |
| S19.4.6 (#228) | Composed graph breadcrumb | Format: "PFI: BAIV-AIV / Persona: Soc Manager (8 entities, 3 ontologies)". Click opens scope rule log panel. Updates on persona switch. Hidden when scope rules not active. |

### Files Changed

| File | Change |
|------|--------|
| `js/composition-filter.js` | +buildScopedFilteredView, getScopedNodeRenderMode, isScopedEdgeVisible (~80 lines) |
| `js/pfi-loader.js` | +generatePFIGraph orchestrator (~40 lines) |
| `js/graph-renderer.js` | Scope-aware wrapper at 4 render points (~10 lines) |
| `js/ui-panels.js` | Product/persona selector + breadcrumb (~60 lines) |
| `tests/pfi-graph-generation.test.js` | NEW (~400 lines, ~35 tests) |

### Key Design Decisions

- **Backward compatible** — all existing rendering unchanged when scope rules are off
- **Minimal renderer changes** — 4 lines in graph-renderer.js, logic lives in composition-filter.js
- **ICP hierarchy from VP-ONT** — persona selector populated from `resolveProductContext().icpHierarchy`
- **Entity visibility model** — three states (visible/ghost/hidden) rather than binary show/hide

---

## Beyond Epic 19: What's Needed to Launch a Custom PFI Graph Product

After F19.3 + F19.4, the **core engine** is complete: you can compose, freeze, inherit, render, and persona-scope a PFI graph. But launching a custom PFI graph product for a real client requires additional capabilities.

### Estimated additional features needed (recommended priority order)

1. **PFI Instance Data Authoring** (NEW EPIC — ~8 stories)
   - Currently instance data is hand-crafted JSON-LD files
   - Need guided authoring UI: create VP Problems, ICPs, Solutions for a new product
   - Validation against EMC scope rules as you author
   - Export to JSON-LD for versioning
   - *Without this, every new PFI product requires manual JSON editing*

2. **Supabase/JSONB Graph Persistence** (Epic 34 critical path — ~6 stories)
   - Persist frozen snapshots + instance data to Supabase JSONB tables
   - Replace localStorage with durable cloud storage
   - Multi-user access to same PFI instance
   - *Required for any production deployment beyond single-browser use*

3. **PFI Promotion Pipeline: Dev → Test → Prod** (Epic 34 S4 — ~6 stories)
   - Three-environment promotion workflow per PFI instance
   - 15 repos already created (3 per instance)
   - Promotion gates: test suite pass, snapshot integrity check, admin approval
   - *Prevents untested graph changes reaching production clients*

4. **Client-Org Configuration Wizard** (Epic 9H extension — ~5 stories)
   - Onboarding flow: select industry, org size, maturity, products
   - Auto-populates ORG-CONTEXT + initial scope rules from templates
   - Generates starter PFI instance with recommended ontology composition
   - *Reduces time-to-first-graph from days to minutes for new clients*

5. **F19.6: External Data Inbound** (Epic 19 candidate — 6 stories, already defined)
   - Supabase adapter, REST API adapter, manual/scheduled refresh
   - Augment static JSON-LD with live data sources
   - *Needed when client data lives in external systems*

6. **F19.7: External Data Outbound** (Epic 19 candidate — 6 stories, already defined)
   - Export composed graph as JSONB, Cypher, or API manifest
   - Push-to-Supabase, pull-via-API patterns
   - *Needed for downstream consumption by agents and applications*

7. **BAIV Agent Manager Integration** (Epic 34 S3 — ~6 stories)
   - 16 BAIV agents consume PFI graph data
   - Agent ↔ scope rule binding: agents see only their persona-scoped subgraph
   - pe:AIAgent → agentActsAs/agentOwnsProcess bridging
   - *Required for the agentic platform vision*

8. **PFI Dashboard & Health Monitoring** (NEW — ~4 stories)
   - Admin dashboard: all PFI instances, snapshot versions, inheritance status
   - Health checks: stale data detection, broken joins, coverage gaps
   - *Operational visibility as PFI count scales*

### Summary: Minimum Viable PFI Product Launch

| Priority | What | Stories | Why |
|----------|------|---------|-----|
| **Must** | F19.3 (Set & Freeze) | 6 | Can't launch without version-controlled snapshots |
| **Must** | F19.4 (PFI Runtime + UI) | 6 | Can't launch without rendering + persona selector |
| **Must** | Instance Data Authoring | ~8 | Can't onboard clients without guided authoring |
| **Must** | Supabase Persistence | ~6 | Can't deploy beyond single browser |
| **Should** | Promotion Pipeline | ~6 | Safe multi-environment deployment |
| **Should** | Client-Org Wizard | ~5 | Fast client onboarding |
| **Could** | F19.6 External Inbound | 6 | Live data augmentation |
| **Could** | F19.7 External Outbound | 6 | Agent/app consumption |
| **Could** | Agent Manager | ~6 | Agentic platform integration |
| **Won't (yet)** | Dashboard & Monitoring | ~4 | Operational maturity |

### Estimate

- **Minimum viable launch** (Must): F19.3 + F19.4 + Authoring + Supabase = **~26 stories**
- **Production-ready launch** (Must + Should): add Promotion + Wizard = **~37 stories**
- **Full platform** (all): **~59 stories**

F19.3 and F19.4 are the immediate next steps. After those 12 stories, the engine is complete and the question becomes: **how do we get data in (authoring/import) and persist it (Supabase)?**
