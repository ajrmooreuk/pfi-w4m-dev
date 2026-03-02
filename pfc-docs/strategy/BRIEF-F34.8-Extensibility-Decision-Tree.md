# Brief / Terms of Reference: F34.8 — Extensibility Decision Tree

## Interactive Decision Engine for Skills, Plugins, Agents & Combinations

| Field | Value |
|---|---|
| **Parent Epic** | Epic 34: PF-Core Graph-Based Agentic Platform Strategy |
| **Feature** | F34.8: Extensibility Decision Tree |
| **Document Type** | Brief / Terms of Reference |
| **Date** | 2026-02-18 |
| **Status** | BRIEF ISSUED — Ready for Feature Planning |
| **Author** | Platform Architecture |
| **Replaces** | N/A (new feature) |

---

## 1. Background & Context

### 1.1 Problem Statement

The PF-Core platform offers **four distinct extensibility mechanisms** inherited from the Anthropic Claude ecosystem: Skills, Claude Code Plugins, Cowork Plugins, and Agents. These mechanisms can also be combined (Skill+Agent, Plugin+Agent, full-stack). Platform architects, developers, and programme managers currently have **no structured, repeatable method** to determine which mechanism — or combination — to use for a given capability need.

The consequence is:
- Inconsistent architecture decisions across PFI instances (BAIV, W4M, AIR)
- Over-engineering (building agents when skills suffice) or under-engineering (using skills when agents are needed)
- No governance trail for extensibility decisions
- Wasted effort from mismatched patterns

### 1.2 Prior Work & Inputs

This brief synthesises three prior outputs into a single scoped feature:

| Input | Document | Location |
|---|---|---|
| **Design Director Toolkit** | Graphing Workbench & Decision Tree design review | `_strategy/DESIGN-DIRECTOR-TOOLKIT-Graphing-Workbench-Decision-Tree.md` |
| **Extensibility Decision Tree Ontology** | OAA-structured JSON-LD with 7 hypothesis gates, 12 recommendations, 8 test scenarios | `_strategy/extensibility-decision-tree-v1.0.json` |
| **Decision Tree Visualiser** | Working React/JSX interactive UI (Claude.ai prototype) | `_strategy/extensibility-decision-tree-visualiser.jsx` |
| **Skills vs Plugins Guide** | Definitive reference for mechanism comparison | `PBS/ARCHITECTURE/ARCH-Agents/Claude_Skills_vs_Plugins_Definitive_Guide.md` |
| **Epic 34 Platform Strategy** | Parent strategy defining graph-based agentic platform | `_strategy/BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy.md` |
| **Unified Registry Architecture** | Registry cascade model (Core -> Instance -> Client) | `_strategy/BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md` |

### 1.3 Strategic Alignment

| VSOM Element | Alignment |
|---|---|
| **Vision** | Adaptive graph-powered platform replacing disconnected SaaS stacks |
| **Strategy S3** | Agentic Orchestration — multi-agent architecture, Agent Template v6.0.0 |
| **Strategy S2** | Value-Engineering-Driven Everything — governance of extensibility decisions |
| **Objective OBJ-IP2** | Agent dev cycle weeks -> days |
| **Objective OBJ-LG1** | OAA Registry v3.0 central governance |
| **BSC Perspective** | Internal Process + Learning & Growth |

---

## 2. Scope Definition

### 2.1 In Scope

| # | Deliverable | Description |
|---|---|---|
| 1 | **Extensibility Decision Tree ontology (OAA v6.1 compliant)** | Upgrade the existing `extensibility-decision-tree-v1.0.json` to full OAA v6.1.0 compliance (relationships section, business rules, enums, naming conventions) |
| 2 | **Interactive Decision Tree view mode in Graphing Workbench** | New `decision-tree.js` ES module integrated into the ontology visualiser via `setViewMode('decision-tree')`, rendering the 7-gate hypothesis tree using vis-network |
| 3 | **Decision Record output** | JSON-LD `pfc:AutomationDecisionRecord` generated on completion — captures path, scores, recommendation, rationale |
| 4 | **Mermaid export of decision path** | Export the traversed decision path as Mermaid flowchart for inclusion in architecture docs |
| 5 | **Integration with Unified Registry** | Decision tree recommendations map to `pfc:RegistryArtifact` types; scaffold generation for the recommended artifact type |
| 6 | **Test coverage** | Vitest unit tests for decision path traversal, weighted scoring, all 12 terminal nodes reachable |
| 7 | **Registry entry** | `ont-registry-index.json` entry for the Extensibility Decision Tree ontology |

### 2.2 Out of Scope (for this feature)

| # | Item | Reason |
|---|---|---|
| 1 | Agent Dashboard view mode | Separate feature (F34.9) |
| 2 | Registry Browser view mode | Separate feature (F34.10) |
| 3 | Agent Composer / Template Builder | Separate feature (F34.11) |
| 4 | Programme Manager view | Separate feature (F34.12) |
| 5 | Workbench rename / branding | Phase 4 activity |
| 6 | React build toolchain | Visualiser remains zero-build-step; the JSX prototype informs design but implementation is pure ES module |

### 2.3 Constraints

| Constraint | Detail |
|---|---|
| **Zero build step** | Must remain a pure ES module — no React, no bundler. The JSX prototype is reference only |
| **vis-network rendering** | Reuse existing vis-network dependency for decision tree graph rendering |
| **state.js pattern** | New module imports from `state.js`, no circular dependencies |
| **setViewMode() extension** | Register as new view mode in `app.js` |
| **OAA v6.1.0 compliance** | The ontology JSON must pass all core gates (G1-G4) in the audit engine |
| **Existing test suite** | New tests must pass alongside existing 938/939 test suite |

---

## 3. Hypothesis-Testing Decision Model

### 3.1 Three-Layer Architecture

The decision tree operates across three evaluation layers:

| Layer | Purpose | Gates |
|---|---|---|
| **L1: Capability** | Does the need require autonomous reasoning (Agent) or scripted execution (Skill)? | HG-01 (Autonomy), HG-02 (Orchestration) |
| **L2: Composition** | Should the capability be standalone or bundled into a package? | HG-03 (Bundling), HG-04 (Task Complexity), HG-07 (Skill Enhancement) |
| **L3: Distribution** | Who are the end users and how should the capability be delivered? | HG-05 (Agent Distribution), HG-06 (User Persona) |

### 3.2 Gate Scoring Model

Each hypothesis gate:
- Tests a **specific hypothesis** about the capability need
- Evaluates **4 weighted criteria** (weights 2-3, total max 100 weighted points)
- Produces a **normalised score** (0-10) from weighted criteria
- Routes to **PASS / PARTIAL / FAIL** based on thresholds
- Each outcome directs to the **next gate or a terminal recommendation**

### 3.3 Terminal Recommendations (12 Outcomes)

| Category | Outcomes |
|---|---|
| **Agent tier** | Orchestrator, Specialist, Utility, Standalone |
| **Skill tier** | Standalone, Simple, Skill+MCP |
| **Plugin tier** | Claude Code, Cowork, CC+Agent, Cowork+Agent, Lightweight |
| **No action** | Use inline prompting |

### 3.4 Test Scenarios (8, per OAA 60/20/10/10)

| Distribution | Count | Examples |
|---|---|---|
| Typical (60%) | 4 | BAIV Discovery Agent, Brand Guidelines Skill, Client AI Audit, Ontology Generation |
| Edge (20%) | 2 | 50/50 Agent/Skill boundary, Single-use complex analysis |
| Boundary (10%) | 1 | Exact threshold score on HG-01 |
| Invalid (10%) | 1 | Native Claude capability (no extensibility needed) |

---

## 4. Acceptance Criteria

| # | Criterion | Verification |
|---|---|---|
| AC-1 | Decision tree view mode accessible via tab in visualiser | Manual: click tab, view renders |
| AC-2 | All 7 hypothesis gates navigable with criterion scoring (0-10 per criterion) | Manual: traverse all gates |
| AC-3 | All 12 terminal recommendations reachable via valid paths | Automated: Vitest path coverage |
| AC-4 | Weighted scoring normalisation matches specification (weight * score / maxWeighted * 10) | Automated: unit test |
| AC-5 | Decision Record JSON-LD output generated with path, scores, recommendation | Automated: output validation |
| AC-6 | Mermaid export produces valid flowchart of traversed path | Automated: Mermaid syntax check |
| AC-7 | Ontology JSON passes OAA v6.1.0 audit gates G1-G4 | Automated: audit engine test |
| AC-8 | 8 test scenarios produce expected recommendations | Automated: scenario test suite |
| AC-9 | New module follows state.js import pattern, no circular deps | Code review |
| AC-10 | Existing 938 tests unaffected | CI: `npx vitest run` |

---

## 5. Story Decomposition (Candidate)

| Story | Title | Size |
|---|---|---|
| S34.8.1 | Upgrade decision tree ontology to OAA v6.1.0 compliance | M |
| S34.8.2 | Create `decision-tree.js` module with gate evaluation engine | L |
| S34.8.3 | Integrate decision tree as new view mode in `app.js` | S |
| S34.8.4 | Build vis-network rendering for hypothesis gate graph | M |
| S34.8.5 | Implement Decision Record JSON-LD output | S |
| S34.8.6 | Add Mermaid export for traversed decision path | S |
| S34.8.7 | Write Vitest test suite (path coverage, scoring, scenarios) | M |
| S34.8.8 | Add ontology to registry index | S |

---

## 6. Dependencies

| Dependency | Type | Status |
|---|---|---|
| Ontology Visualiser v5.1.0 | Existing codebase | Production |
| vis-network v9.1.2 | Existing dependency | Available |
| `state.js` shared state module | Architecture pattern | Production |
| `setViewMode()` in `app.js` | Extension point | Production |
| `audit-engine.js` OAA gates | Validation | Production |
| `export.js` Mermaid export | Extension point | Production |
| `ont-registry-index.json` | Registry | Production (v9.0.0) |

---

## 7. Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Decision tree ontology may not pass G2C (graph connectivity) due to tree structure | Medium | Low | Trees are connected by definition; ensure relationship section models gate-to-gate edges |
| vis-network rendering of decision flow may conflict with existing graph mode state | Low | Medium | Use separate network instance or gated state reset in `setViewMode()` |
| 12 terminal nodes may not all be reachable in single traversal | N/A (by design) | N/A | Each path tests 2-3 gates; test suite covers all 12 via 8 scenarios |

---

## 8. Artifacts Index

| Artifact | Path | Type |
|---|---|---|
| This Brief | `_strategy/BRIEF-F34.8-Extensibility-Decision-Tree.md` | Terms of Reference |
| Design Director Toolkit | `_strategy/DESIGN-DIRECTOR-TOOLKIT-Graphing-Workbench-Decision-Tree.md` | Design Review |
| Decision Tree Ontology | `_strategy/extensibility-decision-tree-v1.0.json` | OAA Ontology (pre-compliance) |
| Decision Tree Visualiser | `_strategy/extensibility-decision-tree-visualiser.jsx` | React Prototype (reference) |
| Skills vs Plugins Guide | `PBS/ARCHITECTURE/ARCH-Agents/Claude_Skills_vs_Plugins_Definitive_Guide.md` | Reference |
| Epic 34 Strategy | `_strategy/BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy.md` | Strategy |
| Unified Registry Architecture | `_strategy/BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md` | Architecture |

---

## 9. Definition of Done

- [ ] Decision tree ontology passes OAA v6.1.0 core gates (G1-G4)
- [ ] `decision-tree.js` module integrated into visualiser
- [ ] All 12 recommendations reachable (verified by test suite)
- [ ] Decision Record JSON-LD output validated
- [ ] Mermaid export produces valid syntax
- [ ] All new tests pass; existing 938 tests unaffected
- [ ] Registry index updated with ontology entry
- [ ] Feature issue closed; epic body updated

---

*Brief / Terms of Reference — F34.8 Extensibility Decision Tree*
*Status: BRIEF ISSUED*
