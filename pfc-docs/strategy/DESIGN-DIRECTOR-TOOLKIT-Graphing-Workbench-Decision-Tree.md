# Design Director Toolkit: Graphing Workbench & Automation Decision Tree

## OAA Visualiser Evolution -- Epic 34 Companion

| Field | Value |
|---|---|
| **Parent Epic** | 34: PF-Core Graph-Based Agentic Platform Strategy |
| **Document** | PFC-DESIGN-DIRECTOR-TOOLKIT-v1.0 |
| **Date** | 2026-02-18 |
| **Status** | DRAFT -- Design Review & Feature Scoping |
| **Scope** | Visualiser -> Graphing Workbench + Automation Decision Engine |
| **Depends On** | BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy, BRIEFING-Epic34-Unified-Registry-Skills-Architecture, Claude_Skills_vs_Plugins_Definitive_Guide |

---

## Part A: ONT Visualiser -> Graphing Workbench Evolution

### 1. Current State (OAA Visualiser v5.1.0)

The Ontology Visualiser is a **27-file, zero-build-step browser application** with 938/939 passing tests. It already supports:

| Capability | Module(s) | Status |
|---|---|---|
| Graph visualisation (vis-network) | `graph-renderer.js`, `app.js` | Production |
| Mermaid diagram rendering | `mermaid-viewer.js` | Production |
| Mindmap freeform canvas | `mindmap-canvas.js` | Production |
| Multi-ontology registry (41 ontologies) | `multi-loader.js`, `github-loader.js` | Production |
| OAA v6.1.0 compliance (10 gates) | `audit-engine.js`, `compliance-reporter.js` | Production |
| Ontology authoring (CRUD, versioning) | `ontology-author.js`, `authoring-ui.js` | Production |
| Diff/changelog engine | `diff-engine.js` | Production |
| EMC composition engine | `emc-composer.js` | Production |
| Design system integration | `ds-loader.js`, `ds-authoring.js`, `ds-codegen.js` | Production |
| Backlog management | `backlog-manager.js`, `backlog-ui.js` | Production |
| Semantic layer filtering | `layer-filter.js`, `composition-filter.js` | Production |
| Multi-format export (PNG/SVG/Mermaid/D3/PDF) | `export.js` | Production |
| PFI instance loading | `pfi-loader.js` | Production |
| IndexedDB library persistence | `library-manager.js` | Production |

**Key architecture pattern:** Centralised `state.js` module (no circular deps) + `setViewMode()` view switcher + tiered navigation (Tier 0/1/2 drill-down).

### 2. Morphing Vision: Graphing Workbench

The visualiser has already grown **beyond ontology viewing** into a multi-purpose graph workbench. The next evolution formalises this by adding three strategic layers:

```
GRAPHING WORKBENCH
|
|-- Layer 1: Graph Canvas (EXISTING -- visualiser core)
|   |-- vis-network graph (entities, relationships, cross-refs)
|   |-- Mermaid diagram renderer
|   |-- Mindmap freeform canvas
|   +-- [NEW] Timeline / Gantt view mode
|
|-- Layer 2: Strategy Architecture (EXISTING foundations + NEW orchestration)
|   |-- VSOM cascade visualisation (Vision -> Strategy -> Objectives -> Metrics)
|   |-- BSC perspective mapping (5 perspectives, cause-effect chains)
|   |-- OKR tree rendering (Objectives -> Key Results -> Initiatives)
|   |-- Graph Series navigation (8 PFC-G-* series)
|   +-- [NEW] VSEM execution bridge view (Strategy -> Execution -> Metrics)
|
|-- Layer 3: Agents & Automation (NEW)
|   |-- Agent registry browser (load/inspect agent definitions)
|   |-- Decision Tree engine (Skill vs Plugin vs Agent selection)
|   |-- Program Manager dashboard (cross-agent orchestration view)
|   |-- Builder automation triggers (invoke agents from workbench)
|   +-- Agent Template v6.0.0 composer
|
+-- Layer 4: Unified Registry Consumer (NEW -- Epic 34 aligned)
    |-- Registry artifact browser (all types: ontologies, skills, agents, tokens, schemas)
    |-- Inheritance cascade viewer (Core -> Instance -> Product -> Client)
    |-- On/Off/Customised toggle inspector
    +-- Artifact dependency graph
```

### 3. Agent Integration Points

The Workbench becomes the **visual control plane** for agents already defined in `PBS/AGENTS/pfc-agents/`:

| Agent | Purpose | Workbench Integration |
|---|---|---|
| **PFC-OAA v6.1+** | Ontology governance, validation, upgrade | Existing (audit engine, upgrade prompt). Enhance: direct invocation via Claude API, streaming validation feedback |
| **PFC-PE-Process-Engineer** | Process execution, 8-phase scaffold | New: process flow visualisation in graph/mermaid mode, phase status tracking, sub-agent coordination view |
| **PFC-Agent-Builder** | Agent template composition, skill packaging | New: visual agent composer (system prompt + skills + MCP bindings), Agent Template v6.0.0 form |
| **Program-Dev-Mngr** | Cross-agent programme orchestration | New: programme dashboard view, agent cluster status, dependency tracking |
| **Product-Mngr-AGN** | Product strategy, roadmap, PMF tracking | New: roadmap timeline view, PMF score integration, VP-RRR alignment display |

### 4. New View Modes for Workbench

Extending the existing `setViewMode()` pattern in `app.js`:

| Mode | Trigger | Rendering | Module |
|---|---|---|---|
| `graph` | Existing | vis-network force graph | `graph-renderer.js` |
| `mermaid` | Existing | Mermaid.js diagrams | `mermaid-viewer.js` |
| `mindmap` | Existing | vis-network freeform | `mindmap-canvas.js` |
| `decision-tree` | **NEW** | Interactive decision flow | `decision-tree.js` (new) |
| `agent-dashboard` | **NEW** | Agent cluster status | `agent-dashboard.js` (new) |
| `registry-browser` | **NEW** | Unified registry inspector | `registry-browser.js` (new) |
| `timeline` | **NEW** | Gantt/roadmap view | `timeline-viewer.js` (new) |

### 5. Module Architecture (Proposed Additions)

```
ontology-visualiser/js/
  # Existing 27 modules (unchanged)
  state.js, app.js, ontology-parser.js, graph-renderer.js, ...

  # New Layer 3 modules
  decision-tree.js          # Interactive Skill/Plugin/Agent decision engine
  agent-dashboard.js        # Agent cluster status + invocation UI
  agent-registry-loader.js  # Parse agent definitions from PBS/AGENTS/
  agent-composer.js         # Agent Template v6.0.0 visual builder
  programme-view.js         # Programme Manager cross-agent orchestration

  # New Layer 4 modules
  registry-browser.js       # Unified Registry artifact inspector
  cascade-viewer.js         # Inheritance cascade visualiser (Core->Instance->Client)
  timeline-viewer.js        # Gantt/roadmap timeline mode
```

**Design principle:** Each new module follows the existing pattern -- pure ES module, imports `state.js`, no circular dependencies, registered via `setViewMode()`.

---

## Part B: Automation Decision Tree -- Skills, Plugins, Agents, or Combination

### 6. Feature Scope: Interactive Decision Engine

**What it is:** An interactive, guided decision tree embedded in the Graphing Workbench that helps platform architects and developers determine the correct automation mechanism for any given use case.

**Why it matters:** The PF-Core platform has three distinct automation mechanisms (Skills, Plugins, Agents) plus combinations thereof. Choosing wrong wastes effort, creates technical debt, and breaks governance. The decision tree encodes the expertise from the [Claude Skills vs Plugins Definitive Guide](../../ARCHITECTURE/ARCH-Agents/Claude_Skills_vs_Plugins_Definitive_Guide.md) into an executable, navigable flow.

**Where it runs:** New `decision-tree` view mode in the Graphing Workbench, rendered using vis-network (interactive graph) with Mermaid export.

### 7. Decision Tree Logic Model

#### 7.1 Entry Questions (Discriminators)

| # | Question | Options | Routes To |
|---|---|---|---|
| Q1 | What is the **primary task type**? | Single repeatable task / Multi-step workflow / Autonomous orchestration / Role-specific workflow | Q2/Q3/Q4/Q5 |
| Q2 | Does it need to work across **multiple Claude surfaces**? | Claude.ai + Claude Code + API / Claude Code only / Cowork only | Skill / Plugin / Cowork Plugin |
| Q3 | Does the workflow need **external tool integrations** (MCP servers, APIs)? | Yes / No | Plugin (bundles MCP) / Skill or Agent |
| Q4 | Does it require **multi-agent coordination** (sub-agents, phase transitions)? | Yes / No | Agent (with sub-agents) / Plugin or Skill |
| Q5 | Who is the **end user persona**? | Developer / Knowledge worker / Platform architect | Plugin / Cowork Plugin / Agent |
| Q6 | Does it need **ontology/graph context** from the registry? | Full graph traversal / Single ontology / No graph needed | Agent / Skill + MCP / Skill |
| Q7 | What **governance level** applies? | Registry-governed artifact / Team convention / Personal automation | Registry Skill + CI / Plugin / Skill |
| Q8 | Is **state persistence** required across sessions? | Yes (long-running) / No (stateless) | Agent / Skill or Plugin |

#### 7.2 Terminal Nodes (Recommendations)

| Terminal | Mechanism | When | Example |
|---|---|---|---|
| **T-SKILL** | Skill only | Single task, cross-platform, stateless, no MCP | VSOM generation, ontology upgrade prompt, brand guidelines |
| **T-PLUGIN** | Plugin only | Developer workflow, bundles skills + MCP + commands | OAA Workbench plugin (validation + GitHub + commands) |
| **T-COWORK** | Cowork Plugin | Non-technical user, role workflow, desktop GUI | Marketing campaign manager, legal review assistant |
| **T-AGENT** | Agent only | Autonomous orchestration, multi-step, state-aware | Process Engineer (8-phase scaffold), OAA (validation + upgrade) |
| **T-SKILL+AGENT** | Skill packaged into Agent | Agent uses skill for domain expertise but orchestrates independently | OAA Agent loads `skill-ontology-validation` for gate checks |
| **T-PLUGIN+AGENT** | Plugin bundles Agent definition | Agent distributed as installable package with MCP bindings | Agent Builder plugin (composer + MCP + agent definitions) |
| **T-SKILL+PLUGIN+AGENT** | Full stack | Registry-governed, multi-surface, orchestrated, distributed | PFC-PE Process Engineer: skills (phase templates) + plugin (MCP for Supabase/GitHub) + agent (orchestration prompt) |
| **T-REGISTRY-SKILL** | Registry Skill artifact | Governed via Unified Registry cascade, CI-validated, inheritable | VSOM Skill (Core -> BAIV customised -> W4M customised -> AIR inherited) |

#### 7.3 Decision Flow (Logic)

```
START
  |
  Q1: What is the primary task type?
  |
  |-- "Single repeatable task"
  |     |
  |     Q2: Cross-platform needed?
  |     |-- "Yes (Claude.ai + Code + API)" -----> T-SKILL
  |     |-- "Claude Code only"
  |     |     |
  |     |     Q3: External tools (MCP)?
  |     |     |-- "Yes" -----> T-PLUGIN
  |     |     +-- "No"  -----> T-SKILL
  |     +-- "Cowork only" -----> T-COWORK
  |
  |-- "Multi-step workflow"
  |     |
  |     Q3: External tools (MCP)?
  |     |-- "Yes"
  |     |     |
  |     |     Q4: Multi-agent coordination?
  |     |     |-- "Yes" -----> T-PLUGIN+AGENT
  |     |     +-- "No"  -----> T-PLUGIN
  |     +-- "No"
  |           |
  |           Q4: Multi-agent coordination?
  |           |-- "Yes" -----> T-SKILL+AGENT
  |           +-- "No"  -----> T-SKILL (multi-skill composition)
  |
  |-- "Autonomous orchestration"
  |     |
  |     Q6: Ontology/graph context?
  |     |-- "Full graph traversal" -----> T-SKILL+PLUGIN+AGENT
  |     |-- "Single ontology" -----> T-SKILL+AGENT
  |     +-- "No graph needed" -----> T-AGENT
  |
  +-- "Role-specific workflow"
        |
        Q5: End user persona?
        |-- "Developer" -----> T-PLUGIN+AGENT
        |-- "Knowledge worker" -----> T-COWORK
        +-- "Platform architect"
              |
              Q7: Governance level?
              |-- "Registry-governed" -----> T-REGISTRY-SKILL
              |-- "Team convention" -----> T-PLUGIN
              +-- "Personal" -----> T-SKILL
```

### 8. PF-Core Agent Mapping Through Decision Tree

| Agent | Decision Path | Result | Components |
|---|---|---|---|
| **PFC-OAA v6.1+** | Autonomous orchestration -> Full graph -> T-SKILL+PLUGIN+AGENT | Skill: `skill-ontology-validation` (gate checks), Plugin: OAA Workbench (MCP for GitHub + registry), Agent: OAA system prompt (multi-step governance) |
| **PFC-PE-Process-Engineer** | Autonomous orchestration -> Full graph -> T-SKILL+PLUGIN+AGENT | Skill: phase templates (8 stages), Plugin: PE toolkit (MCP for Supabase + CI), Agent: orchestration prompt (sub-agent coordination) |
| **PFC-Agent-Builder** | Multi-step workflow -> MCP yes -> Multi-agent yes -> T-PLUGIN+AGENT | Plugin: builder toolkit (MCP for registry + file system), Agent: composition engine |
| **Program-Dev-Mngr** | Role-specific -> Platform architect -> Registry-governed -> T-REGISTRY-SKILL | Registry Skill: programme management templates, cross-agent status aggregation |
| **Product-Mngr-AGN** | Multi-step workflow -> MCP yes -> Multi-agent no -> T-PLUGIN | Plugin: product management (MCP for roadmap tools + analytics) |

### 9. Relationship to Unified Registry

The Decision Tree output maps directly to the Unified Registry artifact types:

```
DECISION TREE OUTPUT           REGISTRY ARTIFACT
-----------------------        ------------------
T-SKILL                  --->  pfc:skill-*         (artifactType: "skill")
T-PLUGIN                 --->  pfc:plugin-*        (artifactType: "plugin")
T-AGENT                  --->  pfc:agent-*         (artifactType: "agent")
T-REGISTRY-SKILL         --->  pfc:skill-* + pfc:instanceOverrides (cascade-governed)
T-SKILL+AGENT            --->  pfc:agent-* with dependencies: [pfc:skill-*]
T-PLUGIN+AGENT           --->  pfc:plugin-* containing agents/ directory
T-SKILL+PLUGIN+AGENT     --->  pfc:plugin-* + pfc:agent-* + pfc:skill-* (full bundle)
```

Every terminal node produces a **registry artifact template** that can be:
1. Generated as JSON-LD scaffold
2. Registered in `registry-index.json`
3. Governed by the Core -> Instance -> Client cascade
4. Validated by CI pipeline (`registry-ci.yml`)

### 10. Decision Tree UI Specification

#### 10.1 Rendering

- **Primary:** vis-network interactive graph (nodes = questions/terminals, edges = choices)
- **Colour coding:**
  - Question nodes: `#5a67d8` (purple) -- matches OAA styling
  - Terminal Skill: `#22c55e` (green)
  - Terminal Plugin: `#3b82f6` (blue)
  - Terminal Agent: `#f59e0b` (amber)
  - Terminal Combination: `#ef4444` (red -- signifies complexity)
  - Terminal Registry-Skill: `#017c75` (teal -- matches brand)
- **Interaction:** Click a question node to expand choices; click a choice to advance; breadcrumb trail shows path taken
- **Export:** Mermaid flowchart, PNG/SVG, JSON decision record

#### 10.2 Decision Record Output

When the user completes the decision tree, generate a structured output:

```json
{
  "@type": "pfc:AutomationDecisionRecord",
  "decisionPath": ["Q1:autonomous-orchestration", "Q6:full-graph-traversal"],
  "recommendation": "T-SKILL+PLUGIN+AGENT",
  "components": {
    "skill": { "template": "skill-scaffold.json", "purpose": "Domain expertise instructions" },
    "plugin": { "template": "plugin-scaffold.json", "purpose": "Distribution + MCP bindings" },
    "agent": { "template": "agent-scaffold.json", "purpose": "Orchestration + multi-step" }
  },
  "registryArtifacts": ["pfc:skill-*", "pfc:plugin-*", "pfc:agent-*"],
  "governanceLevel": "registry-governed",
  "estimatedComplexity": "high",
  "referenceAgents": ["PFC-OAA", "PFC-PE-Process-Engineer"],
  "timestamp": "2026-02-18T00:00:00Z"
}
```

---

## Part C: Implementation Phasing

### Phase 1: Decision Tree MVP (Standalone Feature)

| Item | Detail |
|---|---|
| **New module** | `decision-tree.js` (~300-400 lines) |
| **View mode** | `decision-tree` added to `setViewMode()` |
| **Data** | Hardcoded decision graph (questions + edges + terminals) in module |
| **Rendering** | vis-network with hierarchical layout |
| **Output** | Decision record JSON + Mermaid export |
| **Dependency** | Zero new dependencies (uses existing vis-network) |
| **Tests** | Decision path traversal unit tests, terminal node coverage |

### Phase 2: Agent Dashboard + Registry Browser

| Item | Detail |
|---|---|
| **New modules** | `agent-dashboard.js`, `agent-registry-loader.js`, `registry-browser.js` |
| **View modes** | `agent-dashboard`, `registry-browser` |
| **Data** | Load agent definitions from `PBS/AGENTS/pfc-agents/` via GitHub loader |
| **Rendering** | Agent cluster cards, status indicators, invocation buttons |
| **Registry** | Browse all artifact types from `registry-index.json` (extended format) |

### Phase 3: Agent Composer + Programme View

| Item | Detail |
|---|---|
| **New modules** | `agent-composer.js`, `programme-view.js`, `cascade-viewer.js` |
| **Capability** | Visual Agent Template v6.0.0 builder, inheritance cascade inspector |
| **Integration** | Decision tree output feeds into agent composer as scaffold |
| **Programme view** | Cross-agent orchestration status, dependency graph, phase tracking |

### Phase 4: Full Workbench Rename + Branding

| Item | Detail |
|---|---|
| **Rename** | "OAA Ontology Visualiser" -> "PFC Graphing Workbench" (retain OAA branding for validation engine) |
| **Landing** | Workbench home screen with mode selector (Graph, Mermaid, Mindmap, Decision Tree, Agents, Registry, Timeline) |
| **URL** | Retain GitHub Pages deployment, update page title and meta |

---

## Part D: Decision Tree as Skill, Plugin, and Agent (Meta-Application)

The Decision Tree itself should be implemented as all three mechanisms -- proving the pattern:

| Mechanism | Implementation | Purpose |
|---|---|---|
| **Skill** | `SKILL.md` with decision logic in Markdown + JSON decision graph | Claude.ai users can ask "Should I build a skill or plugin?" and get guided through the tree conversationally |
| **Plugin** | Claude Code plugin bundling the skill + MCP server for registry lookup | Developers run `/decide-automation` in Claude Code, get interactive terminal-based decision flow + scaffold generation |
| **Agent** | Agent prompt with decision tree knowledge + ability to inspect existing registry | Autonomous: given a use case description, the agent traverses the tree, checks existing registry for reusable components, and outputs a complete recommendation with scaffolds |

This triple-implementation serves as the **reference pattern** for all future PF-Core automation, demonstrating how the three mechanisms compose.

---

## Part E: Cross-Reference to Existing Assets

| Existing Asset | Role in Workbench | Location |
|---|---|---|
| Claude Skills vs Plugins Guide | Source of truth for decision tree logic | `PBS/ARCHITECTURE/ARCH-Agents/Claude_Skills_vs_Plugins_Definitive_Guide.md` |
| OAA Workbench Guide | Current workbench definition (v1.2.0) | `PBS/TOOLS/ontology-visualiser/OAA-WORKBENCH-GUIDE.md` |
| OAA Architecture Guide | Visualiser architecture documentation | `PBS/TOOLS/ontology-visualiser/OAA-ARCHITECTURE-GUIDE.md` |
| Agent OAA README | OAA agent definition | `PBS/AGENTS/pfc-agents/agent-oaa/README.md` |
| Process Engineer Spec | PE agent specification | `PE-Series/PE-ONT/ref-ont-pe-files/process-engineer-agent-specification-v1.md` |
| OAA v7 Project-PAL Analysis | Agent architecture reference (Palantir) | `PBS/ONTOLOGIES/OAA-v7-AGENT-ARCHITECTURE-PROJECT-PAL-ANALYSIS.md` |
| Epic 34 Platform Strategy | Parent strategy document | `_strategy/BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy.md` |
| Epic 34 Unified Registry | Registry architecture | `_strategy/BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md` |
| Threat Modelling Briefing | GRC agent integration reference | `_sketches/THREAT-MODELLING-BRIEFING-NOTEBOOK.md` |
| Agent Registry Sample | Agent definition format | `ontology-visualiser/sample-ontologies/agent-registry.json` |

---

## Summary: Design Principles

| Principle | Implementation |
|---|---|
| **Zero build step** | All new modules are pure ES modules, no bundler, no transpilation |
| **state.js is king** | All new modules import shared state, no circular dependencies |
| **setViewMode() extension** | Every new view adds a case to the centralised view switcher |
| **Decision tree encodes governance** | The tree IS the governance -- choosing wrong is harder than choosing right |
| **Registry-first artifacts** | Every decision tree output maps to a Unified Registry artifact type |
| **Triple implementation** | Decision tree itself is Skill + Plugin + Agent (reference pattern) |
| **Incremental morphing** | Visualiser -> Workbench in 4 phases, no big-bang rewrite |
| **Agent-aware, not agent-dependent** | Workbench visualises and invokes agents but doesn't require them to function |

---

*Design Director Toolkit -- Graphing Workbench & Automation Decision Tree*
*Epic 34 Companion -- v1.0 DRAFT*
*Status: Ready for design review and feature planning*
