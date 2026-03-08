# PFC-STRAT: Strategic Neural Graph (PFC-SNG) — A Weighted Directed Graph

**Product Code:** PFC-STRAT (Strategy & Briefings)
**Version:** 1.0.0
**Date:** 2026-03-07
**Status:** Draft
**Scope:** Platform Foundation Core (PFC) — Weighted directed meta-graph across the ontology library
**Parent Epic:** [Epic 34 (#518)](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518) — PF-Core Graph-Based Agentic Platform Strategy
**Registry Version:** ont-registry-index.json v11.3.0 (52 ontologies, 9 PFI instances)
**Supersedes:** n/a (new concept)

## Related Epics & Briefings

| Asset | Link | Synergy with PFC-SNG |
|---|---|---|
| Epic 34: PF-Core Platform Strategy | [#518](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518) | Parent — SNG is the connective tissue of the 8 graph series defined here |
| Epic 34 Briefing: VSOM Platform Strategy | [Briefing](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy.md) | The 6 strategies, BSC objectives, and cause-effect chains ARE the pathways the SNG encodes |
| Epic 34 Briefing: Graph Series Mapping | [Briefing](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/BRIEFING-Epic34-Graph-Series-Ontology-Mapping.md) | The 8 graph series are the SNG's major node clusters — SNG adds directed weighted edges between them |
| Epic 34 Briefing: Unified Registry | [Briefing](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md) | SNG becomes the third structural dimension of the registry (alongside series + instances) |
| Epic 34 Briefing: PFI Readiness | [Briefing](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/BRIEFING-Epic34-PFI-Instance-Readiness.md) | Each PFI's readiness score correlates to how many SNG pathways it can activate |
| Epic 19: EMC Graph-Scope Rules | [#127](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/127) | EMC compositions filter WHAT to show; SNG defines HOW it connects — complementary layers |
| Epic 30: GRC Series Architecture | [#370](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/370) | GRC-FW hub IS the constraint layer of the SNG — every governance edge flows from here |
| Epic 40: Workbench | [#577](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/577) | Neural Map view becomes a workbench panel — the strategic architecture view |
| Epic 41: OFM/LSC Bridge | [#600](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/600) | The LSC-OFM fulfilment corridor is an SNG bridge (weight 8, strengthened to 10 for W4M-WWG) |
| Epic 45: W4M-WWG | [#634](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/634) | W4M-WWG's PFI-SNG extends the base graph with fulfilment-dominant pathways |
| Epic 49: VSOM App Planner | [#747](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/747) | The App Planner uses SNG clusters to determine which ontologies an app skeleton needs |
| F49.9: Figma/Pencil Tooling Strategy | [Briefing](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/BRIEFING-F49.9-Figma-Pencil-Design-Tooling-Strategy.md) | Pencil zone layouts can render SNG pathway diagrams as `.pen` design artefacts |
| Epic 59: DTP Database Sync | [#840](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/840) | SNG must be architecture-complete BEFORE Epic 59 syncs it to Supabase JSONB |
| Epic 31: Multi-Instance Delivery | [#394](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/394) | CI/CD promotion pipeline validates SNG pathway integrity per PFI instance |
| PFI-AIRL VE Skill Chain Briefing | [Briefing](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFI-AIRL/) | AIRL's skill chain IS a PFI-specific traversal of the SNG spine, with PbD/SbD gates as constraint edges |

---

## 0. Synergy — Definition in This Context

**Synergy** in the PFC-SNG context is not a buzzword. It has a precise, graph-theoretic meaning:

> **Synergy is the emergent strategic value created when two or more ontologies are connected by a directed pathway that neither ontology produces alone.**

Examples:

| Pathway | Synergy Created | Neither Endpoint Has Alone |
|---|---|---|
| VP --> RRR (weight 9) | Risk-aware value propositions | VP doesn't know risk; RRR doesn't know customer value |
| BSC --> GRC-FW (weight 6) | Governance-aligned strategic objectives | BSC measures but doesn't govern; GRC governs but doesn't measure |
| KPI --> INDUSTRY (weight 7) | Market-contextualised metrics | KPI measures internally; INDUSTRY analyses externally |
| PMF ~~> VSOM (feedback, weight 6) | Strategy refined by market reality | PMF validates but doesn't set strategy; VSOM sets but can't self-validate |
| VSOM --> EA-CORE (weight 6) | Strategy-shaped architecture | VSOM declares intent; EA-CORE declares structure — connected, they become intent-driven architecture |

**Synergy in this graph = the value of the edge, not the value of the nodes.**

The PFC-SNG makes synergy **visible, measurable, and machine-navigable**. Every weighted directed edge IS a synergy. The weight IS the synergy's importance. When we say "the SNG is the moat", we mean: **the synergies between ontologies — not the ontologies themselves — are the defensible IP**.

A competitor can build 52 ontologies (nodes). They cannot replicate the discovered, weighted, validated synergies (edges) without years of real-world traversal.

**Cross-instance synergy** is even more powerful:
- BAIV competitive intel flowing through the Metrics-to-Market bridge strengthens W4M-WWG market sensing
- AIRL compliance patterns flowing through the Governance Constraint layer improve BAIV's risk posture
- Every PFI instance that activates a pathway validates and refines its weight for ALL instances

This is a **compounding network effect on the edges**, not the nodes. That's the moat.

---

## 1. The Problem with the Library Today

The ontology library organises 52 ontologies into series (VE, PE, RCSG, Foundation, Orchestration). The [EMC composer](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/TOOLS/ontology-visualiser/js/emc-composer.js) knows how to compose categories (PRODUCT, COMPETITIVE, STRATEGIC, etc.). The [registry index](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/ONTOLOGIES/ontology-library/ont-registry-index.json) catalogues metadata.

**But none of these encode the strategic neural pathways — the prioritised routes through which strategy becomes reality.**

Today the library is a **flat catalogue**. What's missing is the **graph above the graph** — the meta-architecture that says:

- VSOM is the gravitational centre — everything traces back to it
- The VE Skill Chain (VSOM -> OKR -> KPI -> VP -> Kano -> PMF) is the primary spine
- Some connections are *load-bearing* (Strategy -> Execution) and some are *advisory* (Market -> World-View)
- The flow has direction, weight, and priority — it's not an equal-weight mesh

This meta-graph IS the PFC instance. It's the strategic nervous system. And it's the moat.

---

## 2. What the PFC-SNG Is — A Weighted Directed Graph

### Directed Graph Defined

A **directed graph** (digraph) is a graph where every edge has a direction — it goes *from* node A *to* node B. An arrow, not a line. The direction encodes causation, flow, or dependency.

The PFC-SNG is specifically a **weighted directed graph with typed edges**:

| Property | Meaning | PFC-SNG Application |
|---|---|---|
| **Directed** | Edges have from → to | VSOM *→* OKR (strategy decomposes), PMF *→* VSOM (feedback refines) |
| **Weighted** | Edges have importance scores (1-10) | Strategy-to-Execution = 9, Validation-to-WorldView = 5 |
| **Typed** | Edges have semantic categories | Spine, Bridge, Constraint, Foundation, Feedback, Orchestration |
| **Cyclic** | Some paths loop back | PMF → VP → OKR → VSOM (the refinement cycle) |
| **Clustered** | Nodes group into co-activated sets | VE-Core cluster fires together for any strategy task |

The primary spine (VSOM → OKR → KPI → VP → Kano → PMF) is a **DAG** — directed acyclic, pure top-down decomposition. But the full PFC-SNG includes feedback loops, making it a **directed cyclic graph** overall.

**Direction matters because it tells agents which way to walk:**
- "Improve our value proposition" → start at VP, walk *up* to VSOM for context, *down* to RRR for requirements
- "Are we compliant?" → start at GRC-FW, walk *laterally* to constrained nodes
- "Is our strategy working?" → walk the feedback loop PMF *→* VP *→* OKR *→* VSOM

**Nodes** are ontology namespaces (vsom:, okr:, vp:, etc.). **Edges** are strategic pathways. **Weights** encode importance. **Types** encode behaviour.

---

## 3. The Five Neural Layers

### Layer 1: The Apex — VSOM (Gravitational Centre)

```
                        VSOM
                    (Vision-Strategy-
                   Objectives-Metrics)
                         |
              Everything traces here.
              Every pathway, every PFI,
              every client graph inherits
              this gravitational pull.
```

VSOM is not just another ontology — it's the **root node** of the entire PFC graph. Every strategy, every objective, every metric in every PFI instance is a descendant of a VSOM declaration. The PFC-SNG makes this explicit.

See: [VSOM-ONT](https://github.com/ajrmooreuk/Azlan-EA-AAA/tree/main/PBS/ONTOLOGIES/ontology-library/VE-Series/VSOM-ONT)

### Layer 2: The Primary Spine — VE Skill Chain

```
VSOM ==> OKR ==> KPI ==> VP ==> Kano ==> PMF      (all edges weight 10, directed top-down)
  |        |       |       |       |        |
  |        |       |       |       |        +-- Market validation
  |        |       |       |       +----------- Feature prioritisation
  |        |       |       +------------------- Value articulation
  |        |       +--------------------------- Measurement definition
  |        +----------------------------------- Goal decomposition
  +-------------------------------------------- Strategic intent
```

This is the **primary neural pathway** — the highest-weight path through the graph. Every PFI instance runs this chain. The weight on every edge here is 10/10. This is the strategy-as-code backbone.

**Sub-spines branch from the main spine:**

```
VSOM --+--> VSOM-SA (Strategy Analysis) [direction: inbound, weight: 8]
       |      BSC, INDUSTRY, REASON, MACRO, PORTFOLIO, KANO
       |      These feed INTO VSOM — analytical inputs
       |
       +--> VSOM-SC (Strategy Communication) [direction: outbound, weight: 7]
              NARRATIVE, CASCADE, CULTURE, VIZSTRAT
              These flow FROM VSOM — communication outputs
```

VSOM-SA is the **intake** (inbound edges). VSOM-SC is the **exhaust** (outbound edges). The spine breathes.

See: [VSOM-SA sub-series](https://github.com/ajrmooreuk/Azlan-EA-AAA/tree/main/PBS/ONTOLOGIES/ontology-library/VE-Series/VSOM-SA) | [VSOM-SC sub-series](https://github.com/ajrmooreuk/Azlan-EA-AAA/tree/main/PBS/ONTOLOGIES/ontology-library/VE-Series/VSOM-SC)

### Layer 3: Strategic Bridges — Cross-Series Directed Pathways

These are the load-bearing connections from the VE spine to other series. Each has a **direction**, **weight**, and **type**:

```
From             To                    Bridge Name                Weight  Direction    Type
───────────────────────────────────────────────────────────────────────────────────────────────
VSOM/BSC ──────> PE/PPM/EFS            Strategy-to-Execution       9     top-down     bridge
VP/RRR ────────> PE/PPM                Value-to-Delivery           9     top-down     bridge
VP/RRR ────────> LSC/OFM               Value-to-Fulfilment         8     top-down     bridge
OKR ───────────> KPI/BSC               Goal-to-Measurement         8     top-down     bridge
VP/RRR ────────> GRC-FW                Value-to-Governance         7     lateral      bridge
KPI/INDUSTRY ──> ORG-CONTEXT           Metrics-to-Market           7     lateral      bridge
VSOM ──────────> EA-CORE/DS            Strategy-to-Architecture    6     top-down     bridge
BSC ───────────> GRC-FW/ERM            Scorecard-to-Risk           6     lateral      bridge
PMF ───────────> INDUSTRY/MACRO        Validation-to-WorldView     5     bottom-up    bridge
EMC ───────────> (all series)          Orchestration Hub           8     bidirectional orchestration
```

**The VP-RRR alignment is the second most important pathway after the skill chain itself** (see [JP-VP-RRR-001](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/369)):

```
vp:Problem  ──> rrr:Risk        (problems are risks to the customer)
vp:Solution ──> rrr:Requirement (solutions are requirements to build)
vp:Benefit  ──> rrr:Result      (benefits are measurable results)
```

This isn't just a mapping — it's a neural bridge that fires every time a PFI instance processes value propositions.

### Layer 4: Foundation & Constraint Layers

**Foundation (upward support — implicit, always present):**
```
ORG + ORG-CONTEXT + CTX + GA
  ..> every node above depends on these
  Direction: upward (foundation supports)
  Type: foundation
  Weight: structural (not scored — always loaded)
```

**Governance Constraint (lateral enforcement — does not add value, gates it):**
```
GRC-FW ──| VP, RRR, PE, PPM, EFS, EA-CORE     (constraint edges)
  |
  +── ERM ──| (risk overlay)
  +── GDPR/PII ──| (data protection gates)
  +── NCSC-CAF ──| (cyber assessment gates)
  +── MCSB ──| (security baseline gates)

Direction: lateral (governance constrains, not decomposes)
Type: constraint
Weight: 7 (non-negotiable but not value-generating)
```

See: [Epic 30: GRC Series Architecture](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/370) | [GRC-FW-ONT](https://github.com/ajrmooreuk/Azlan-EA-AAA/tree/main/PBS/ONTOLOGIES/ontology-library/RCSG-Series/GRC-01-GOV/GRC-FW-ONT)

### Layer 5: Intelligence & Feedback Loops

```
Strategy Refinement Cycle (bottom-up, quarterly):
  PMF ~~> VP ~~> OKR ~~> VSOM
  Market validation refines the entire skill chain

Cross-Instance Intelligence (bottom-up, continuous):
  Client Graphs ~~> PFI Instance ~~> PFC-SNG
  Anonymised patterns compound into platform intelligence

Cross-Instance Synergy (lateral, continuous):
  BAIV competitive intel ~~> PFC-G-Market ~~> W4M market sensing
  AIRL compliance patterns ~~> PFC-G-GRC ~~> BAIV governance
```

---

## 4. The Full PFC-SNG Topology

```
                                    VSOM
                                   / |  \
                          [in w:8]/  |   \[out w:7]
                          VSOM-SA   |   VSOM-SC
                         /    |     |     |    \
                      BSC INDUSTRY  |  NARRATIVE CASCADE
                     /  \     |     |     |
            [w:8]  OKR   \   |  CULTURE  VIZSTRAT
                    |     \  |
             [w:8] KPI   MACRO
                    |      |
             [w:10] VP   PORTFOLIO
                  / | \
          [w:10] /  |  \ [w:9]
            Kano    |   RRR ────[w:7]───> GRC-FW ──[w:6]──> ERM
             |      |    |  \                |                |
      [w:10] PMF    |    |   \           GDPR/PII        RMF-IS27005
                    |    |    \          NCSC-CAF
              [w:5] |    |     \         MCSB/DSPT
            ~~~~~~~~|    |      \
           (feedback)|   |   [w:9]
                    |    |    PE ──> PPM ──> EFS
                    |    |                    |
               INDUSTRY |                 DS-ONT
                    |    |                    |
              ORG-CONTEXT|              [w:6] EA-CORE
              /    |    \ |              /    |    \
           ORG   CTX   GA|        EA-TOGAF EA-MSFT EA-ONT
                         |
                    [w:8] LSC <──> OFM
                    (Fulfilment Corridor)

                         |
                    +---------+
                    |   EMC   |  [w:8, bidirectional to all series]
                    +---------+

Legend:
  ══>  Spine (weight 10, directed top-down)
  ──>  Bridge (weight 5-9, directed)
  ──|  Constraint (weight 7, lateral enforcement)
  ..>  Foundation (structural, always present)
  ~~>  Feedback (bottom-up, cyclic)
  <─>  Orchestration (bidirectional)
  [w:N] Edge weight
```

---

## 5. Pathway Classification

| Type | Arrow | Direction | Behaviour | Visual Rendering |
|---|---|---|---|---|
| **Spine** | `══>` | Top-down | Primary skill chain, highest weight, strategy decomposition | Thick gold, solid, `arrows: {to: true}` |
| **Bridge** | `──>` | Top-down or lateral | Load-bearing cross-series connection, synergy-creating | Medium width by weight, dashed, `arrows: {to: true}` |
| **Constraint** | `──\|` | Lateral | Governance gate, does not add value, enforces compliance | Red dotted, `arrows: {to: {type: 'bar'}}` |
| **Foundation** | `..>` | Upward | Structural support, always present, implicit | Gray thin dashed, `arrows: {to: true}` |
| **Feedback** | `~~>` | Bottom-up | Intelligence loop, cyclic, refines strategy | Cyan thin, reverse direction, `arrows: {to: true}` |
| **Orchestration** | `<─>` | Bidirectional | EMC routing, connects all series | Gold, `arrows: {to: true, from: true}` |

---

## 6. Architecture: Three Layers, File-First (No Database Yet)

The SNG must be architecture-complete before [Epic 59: DTP Database Sync](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/840) syncs it to Supabase JSONB. File-first means everything lives in git as JSON.

### 6.1 The Three Structural Dimensions of the Registry

The [Unified Registry](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md) already has two structural dimensions. The SNG is the third:

| Dimension | Section in Registry | Answers | Existing |
|---|---|---|---|
| **Series Organisation** | `seriesRegistry{}` | "What groups do ontologies belong to?" | Yes |
| **PFI Instance Configs** | `pfiInstances[]` | "What does each instance use?" | Yes |
| **Strategic Neural Graph** | `strategicNeuralGraph{}` | "How do they connect and what matters most?" | **NEW** |

Together they form the **complete registry architecture**:
- Series tells you the **vocabulary** (what words exist, grouped by topic)
- PFI tells you the **dialect** (which words each instance speaks)
- SNG tells you the **grammar** (how words connect into strategic meaning)

### 6.2 Where the SNG Lives — In the Registry Index

The SNG is added as a new top-level section in `ont-registry-index.json`:

```
ont-registry-index.json (single source of truth)
|
|-- entries[]                    <-- 52 ontologies (unchanged)
|-- seriesRegistry{}             <-- series organisation (unchanged)
|-- pfiInstances[]               <-- instance configs (unchanged)
|
|-- strategicNeuralGraph{}       <-- NEW: PFC-SNG definition
|     |-- apex                   <-- VSOM as root
|     |-- spine                  <-- VE Skill Chain + sub-spines
|     |-- pathways[]             <-- All directed weighted edges
|     |-- constraints[]          <-- GRC constraint layer
|     |-- foundation             <-- ORG/CTX structural base
|     |-- orchestration          <-- EMC hub
|     |-- feedbackLoops[]        <-- Bottom-up cycles
|     |-- clusters[]             <-- Neural co-activation groups
|     +-- pfiOverrides{}         <-- Per-instance weight adjustments
```

**Why in the registry, not a separate file?** Because the SNG IS the architecture of the registry. It defines how entries relate to each other. It belongs alongside `seriesRegistry` and `pfiInstances`.

### 6.3 The Registry Becomes Cascaded Views (Not Copies)

The registry doesn't fork into PFC vs PFI copies. It stays as **one registry**. The **view** changes:

```
ont-registry-index.json
        |
        |  PFC View = full SNG (all 52 ontologies, all pathways, all weights)
        |
        |  PFI View = SNG filtered by instanceOntologies + EMC requirementScopes
        |             + pfiOverrides applied (weight adjustments, added pathways)
        |
        |  Category View = SNG filtered by EMC composition (PRODUCT, STRATEGIC, etc.)
        |
        +  Agent View = SNG filtered by neural cluster (task-driven)
```

This uses the **same filtering pipeline** that already exists:

1. EMC `composeMultiCategory(requirementScopes)` determines which ontologies are active
2. `constrainToInstanceOntologies(composition, instanceOntologies)` narrows to declared set
3. **NEW:** SNG pathways filtered — only edges where both endpoints survive the filter are rendered
4. **NEW:** `pfiOverrides` applied — weight adjustments for the active PFI instance

The existing code in [app.js `_doSelectPFIInstance()`](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/TOOLS/ontology-visualiser/js/app.js) and [emc-composer.js `constrainToInstanceOntologies()`](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/TOOLS/ontology-visualiser/js/emc-composer.js) already implement this pattern — the SNG extends it.

### 6.4 How the SNG, EMC, and PFI Compose

```
                    +-------------------------------+
                    |  PFC-SNG (Neural Graph)        |
                    |  "How do ontologies connect    |
                    |   strategically?"              |
                    |  Pathways, weights, directions |
                    +---------------+---------------+
                                    |
                    +---------------v---------------+
                    |  EMC Compositions              |
                    |  "What ontologies should I     |
                    |   load for this task?"          |
                    |  Categories, rules, scopes     |
                    +---------------+---------------+
                                    |
                    +---------------v---------------+
                    |  PFI Instance Filter            |
                    |  "What does this instance       |
                    |   actually use?"                |
                    |  instanceOntologies,            |
                    |  requirementScopes,             |
                    |  pfiOverrides (SNG weights)     |
                    +---------------+---------------+
                                    |
                    +---------------v---------------+
                    |  Rendered View                  |
                    |  Nodes + directed weighted      |
                    |  edges for this context         |
                    +-------------------------------+
```

**SNG doesn't replace EMC — it sits above it.**
- EMC answers: "What ontologies should I show?"
- SNG answers: "How do they connect and what matters most?"
- PFI answers: "Which connections does this instance use?"

**Concrete example — PFI-W4M-WWG in FULFILMENT scope:**

| What's Visible | Why |
|---|---|
| VP → LSC → OFM pathway (weight **10**) | Strengthened by PFI override (base 8 → 10, fulfilment is core) |
| VP → RRR bridge (weight 9) | In instanceOntologies, high base weight |
| VP → KPI bridge (weight 8) | In instanceOntologies |
| EMC orchestration hub (weight 8) | EMC in instanceOntologies |
| Foundation: ORG (ghost) | Always present but ghosted |
| NOT: GRC constraint paths | NCSC-CAF not in W4M-WWG instanceOntologies |
| NOT: EA architecture bridges | EA-CORE not in W4M-WWG instanceOntologies |

See: [Epic 45: W4M-WWG](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/634) | [W4M-WWG Operating Guide](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/PFI-WWG/OPERATING-GUIDE-Visualiser.md)

---

## 7. JSON-LD Structure — The `strategicNeuralGraph` Section

This is the new section added to `ont-registry-index.json`:

```json
{
  "strategicNeuralGraph": {
    "@type": "pfc:StrategicNeuralGraph",
    "@id": "pfc:sng-v1.0.0",
    "version": "1.0.0",
    "description": "Weighted directed graph encoding strategic pathways across the PFC ontology library",

    "apex": {
      "namespace": "vsom:",
      "role": "gravitational-centre",
      "rationale": "Every strategy, objective, and metric traces to a VSOM declaration"
    },

    "spine": {
      "name": "VE Skill Chain",
      "weight": 10,
      "direction": "top-down",
      "sequence": [
        { "namespace": "vsom:", "role": "strategic-intent" },
        { "namespace": "okr:", "role": "goal-decomposition" },
        { "namespace": "kpi:", "role": "measurement-definition" },
        { "namespace": "vp:", "role": "value-articulation" },
        { "namespace": "kano:", "role": "feature-prioritisation" },
        { "namespace": "pmf:", "role": "market-validation" }
      ],
      "subSpines": [
        {
          "name": "Strategy Analysis Intake",
          "parent": "vsom:",
          "namespaces": ["bsc:", "industry:", "reason:", "macro:", "portfolio:", "kano:"],
          "direction": "inbound",
          "weight": 8
        },
        {
          "name": "Strategy Communication Exhaust",
          "parent": "vsom:",
          "namespaces": ["narrative:", "cascade:", "culture:", "vizstrat:"],
          "direction": "outbound",
          "weight": 7
        }
      ]
    },

    "pathways": [
      {
        "id": "bridge-strategy-execution",
        "name": "Strategy-to-Execution",
        "from": ["vsom:", "bsc:"],
        "to": ["pe:", "ppm:", "efs:"],
        "weight": 9,
        "direction": "top-down",
        "type": "bridge",
        "edgeLabel": "realizesStrategy",
        "rationale": "How strategic intent becomes managed work"
      },
      {
        "id": "bridge-value-delivery",
        "name": "Value-to-Delivery",
        "from": ["vp:", "rrr:"],
        "to": ["pe:", "ppm:"],
        "weight": 9,
        "direction": "top-down",
        "type": "bridge",
        "edgeLabel": "deliversValue",
        "rationale": "How value propositions become deliverable processes"
      },
      {
        "id": "bridge-value-fulfilment",
        "name": "Value-to-Fulfilment",
        "from": ["vp:", "rrr:"],
        "to": ["lsc:", "ofm:"],
        "weight": 8,
        "direction": "top-down",
        "type": "bridge",
        "edgeLabel": "fulfilsValue",
        "rationale": "How value reaches customers through supply corridors"
      },
      {
        "id": "bridge-goal-measurement",
        "name": "Goal-to-Measurement",
        "from": ["okr:"],
        "to": ["kpi:", "bsc:"],
        "weight": 8,
        "direction": "top-down",
        "type": "bridge",
        "edgeLabel": "measuresGoal",
        "rationale": "How goals decompose into measurable indicators"
      },
      {
        "id": "bridge-value-governance",
        "name": "Value-to-Governance",
        "from": ["vp:", "rrr:"],
        "to": ["grc-fw:"],
        "weight": 7,
        "direction": "lateral",
        "type": "bridge",
        "edgeLabel": "governedBy",
        "rationale": "How value delivery is governed and risk-managed"
      },
      {
        "id": "bridge-metrics-market",
        "name": "Metrics-to-Market",
        "from": ["kpi:", "industry:"],
        "to": ["org-ctx:"],
        "weight": 7,
        "direction": "lateral",
        "type": "bridge",
        "edgeLabel": "contextualisedBy",
        "rationale": "How internal metrics connect to external market reality"
      },
      {
        "id": "bridge-strategy-architecture",
        "name": "Strategy-to-Architecture",
        "from": ["vsom:"],
        "to": ["ea-core:", "ds:"],
        "weight": 6,
        "direction": "top-down",
        "type": "bridge",
        "edgeLabel": "architectsStrategy",
        "rationale": "How strategy shapes technical architecture"
      },
      {
        "id": "bridge-scorecard-risk",
        "name": "Scorecard-to-Risk",
        "from": ["bsc:"],
        "to": ["grc-fw:", "erm:"],
        "weight": 6,
        "direction": "lateral",
        "type": "bridge",
        "edgeLabel": "assessesRisk",
        "rationale": "How balanced scorecard objectives connect to risk management"
      },
      {
        "id": "bridge-validation-worldview",
        "name": "Validation-to-WorldView",
        "from": ["pmf:"],
        "to": ["industry:", "macro:"],
        "weight": 5,
        "direction": "bottom-up",
        "type": "bridge",
        "edgeLabel": "validatesAgainst",
        "rationale": "How market validation feeds back to external analysis"
      }
    ],

    "constraints": [
      {
        "id": "constraint-grc",
        "name": "Governance Constraint Layer",
        "constrainer": "grc-fw:",
        "constrains": ["vp:", "rrr:", "pe:", "ppm:", "efs:", "ea-core:"],
        "type": "constraint",
        "direction": "lateral",
        "weight": 7,
        "edgeLabel": "constrainedBy",
        "children": ["erm:", "gdpr:", "pii:", "ncsc-caf:", "mcsb:", "dspt:"]
      }
    ],

    "foundation": {
      "name": "Structural Foundation",
      "namespaces": ["org:", "org-ctx:", "ctx:", "ga:"],
      "type": "foundation",
      "direction": "upward",
      "rationale": "Identity, context, and structure that every node above depends on"
    },

    "orchestration": {
      "namespace": "emc:",
      "type": "orchestration",
      "direction": "bidirectional",
      "weight": 8,
      "connects": "all-series",
      "rationale": "EMC routes, composes, and maps between all series"
    },

    "feedbackLoops": [
      {
        "id": "feedback-refinement",
        "name": "Strategy Refinement Cycle",
        "path": ["pmf:", "vp:", "okr:", "vsom:"],
        "direction": "bottom-up",
        "weight": 6,
        "frequency": "quarterly",
        "edgeLabel": "refinesStrategy",
        "rationale": "Market validation refines value props, goals, and vision"
      },
      {
        "id": "feedback-cross-instance",
        "name": "Cross-Instance Intelligence",
        "path": ["pfi-client", "pfi-instance", "pfc-sng"],
        "direction": "bottom-up",
        "weight": 5,
        "frequency": "continuous",
        "edgeLabel": "aggregatesIntelligence",
        "rationale": "Anonymised client patterns compound into platform intelligence"
      }
    ],

    "clusters": [
      {
        "id": "cluster-ve-core",
        "name": "VE Core",
        "namespaces": ["vsom:", "okr:", "kpi:", "vp:", "rrr:", "pmf:"],
        "trigger": "Any strategy, value, or planning task"
      },
      {
        "id": "cluster-ve-analysis",
        "name": "VE Analysis",
        "namespaces": ["bsc:", "industry:", "reason:", "macro:", "portfolio:", "kano:"],
        "trigger": "Strategy analysis, planning, or review"
      },
      {
        "id": "cluster-ve-comms",
        "name": "VE Communications",
        "namespaces": ["narrative:", "cascade:", "culture:", "vizstrat:"],
        "trigger": "Strategy communication, storytelling, culture alignment"
      },
      {
        "id": "cluster-execution",
        "name": "Execution",
        "namespaces": ["pe:", "ppm:", "efs:", "ds:"],
        "trigger": "Process engineering, project delivery, design system"
      },
      {
        "id": "cluster-governance",
        "name": "Governance",
        "namespaces": ["grc-fw:", "erm:", "gdpr:", "pii:", "ncsc-caf:", "mcsb:", "dspt:"],
        "trigger": "Compliance, risk assessment, governance review"
      },
      {
        "id": "cluster-architecture",
        "name": "Architecture",
        "namespaces": ["ea-core:", "ea-togaf:", "ea-msft:", "ea:"],
        "trigger": "Architecture decisions, cloud platform, TOGAF"
      },
      {
        "id": "cluster-market-sensing",
        "name": "Market Sensing",
        "namespaces": ["industry:", "macro:", "portfolio:", "org-ctx:"],
        "trigger": "Market analysis, competitive intelligence, external sensing"
      },
      {
        "id": "cluster-fulfilment",
        "name": "Fulfilment",
        "namespaces": ["lsc:", "ofm:", "pe:"],
        "trigger": "Supply chain, logistics, order fulfilment, operations"
      },
      {
        "id": "cluster-identity",
        "name": "Identity",
        "namespaces": ["org:", "org-ctx:", "ctx:", "ga:"],
        "trigger": "Organisation setup, context definition, gap analysis"
      }
    ],

    "pfiOverrides": {
      "PFI-BAIV": {
        "strengthened": [
          { "pathway": "bridge-metrics-market", "weight": 9, "rationale": "Competitive intel is BAIV core" }
        ],
        "added": [
          {
            "id": "baiv-competitive-pathway",
            "name": "AI Visibility Competitive",
            "from": ["vp:"],
            "to": ["industry:", "org-ctx:"],
            "weight": 9,
            "direction": "lateral",
            "type": "bridge",
            "edgeLabel": "competitiveAnalysis"
          }
        ],
        "clusters": [
          {
            "id": "cluster-baiv-competitive",
            "name": "BAIV Competitive",
            "namespaces": ["industry:", "org-ctx:", "vp:", "rrr:"],
            "trigger": "AI visibility competitive analysis"
          }
        ]
      },
      "PFI-W4M-WWG": {
        "strengthened": [
          { "pathway": "bridge-value-fulfilment", "weight": 10, "rationale": "Supply chain IS the W4M-WWG business" }
        ],
        "added": [
          {
            "id": "wwg-corridor-pathway",
            "name": "Fulfilment Corridor",
            "from": ["lsc:"],
            "to": ["ofm:"],
            "weight": 9,
            "direction": "lateral",
            "type": "bridge",
            "edgeLabel": "fulfilmentCorridor"
          }
        ],
        "clusters": [
          {
            "id": "cluster-wwg-fulfilment",
            "name": "WWG Fulfilment",
            "namespaces": ["lsc:", "ofm:", "pe:", "vp:"],
            "trigger": "W4M-WWG supply corridor operations"
          }
        ]
      },
      "PFI-AIRL-CAF-AZA": {
        "strengthened": [
          { "pathway": "bridge-scorecard-risk", "weight": 9, "rationale": "Risk assessment IS the AIRL business" },
          { "pathway": "constraint-grc", "weight": 9, "rationale": "Governance constraints are primary, not secondary" }
        ],
        "clusters": [
          {
            "id": "cluster-airl-assessment",
            "name": "AIRL Assessment",
            "namespaces": ["grc-fw:", "ncsc-caf:", "erm:", "org-ctx:", "dspt:"],
            "trigger": "AIRL cyber assessment and AI readiness"
          }
        ]
      }
    }
  }
}
```

---

## 8. Visualiser Rendering — Using What Already Exists

The [ontology visualiser](https://github.com/ajrmooreuk/Azlan-EA-AAA/tree/main/PBS/TOOLS/ontology-visualiser) already supports directed edges, weighted thickness, semantic colours, and lineage highlighting. The SNG maps directly onto existing capabilities:

### 8.1 Edge Style Mapping

| SNG Pathway Type | Existing Edge Style | Colour | Width Formula | Arrow Type |
|---|---|---|---|---|
| Spine (w:10) | `lineageVE` | #cec528 (gold) | 3.5-4.0px solid | `arrows: {to: {enabled: true}}` |
| Bridge (w:5-9) | `crossSeries` | By weight gradient | `1.0 + (w/10) * 3.0` dashed | `arrows: {to: {enabled: true}}` |
| Constraint (w:7) | **NEW** | #EF5350 (red) | 2.0px dotted | `arrows: {to: {type: 'bar'}}` |
| Foundation | `inheritance` | #888 (gray) | 1.5px dashed | `arrows: {to: {enabled: true}}` |
| Feedback | **NEW** | #00BCD4 (cyan) | 1.5px dot-dash | `arrows: {to: {enabled: true}}` (reversed from/to) |
| Orchestration (w:8) | `crossOntology` | #eab839 (gold) | 2.5px | `arrows: {to: true, from: true}` |

Only 2 new edge styles needed. Everything else maps to existing `EDGE_STYLES` in [state.js](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/TOOLS/ontology-visualiser/js/state.js).

### 8.2 New Files Required

```
ontology-visualiser/
  js/
    sng-renderer.js     <-- NEW: Neural Map view, reads strategicNeuralGraph{}
    sng-filter.js       <-- NEW: SNG + EMC + PFI pathway filtering
    graph-renderer.js   <-- MODIFY: add constraint + feedback edge styles
    state.js            <-- MODIFY: add EDGE_STYLES.constraint, .feedback
    app.js              <-- MODIFY: add Neural Map mode toggle
```

**4 new/modified files. That's it.** The rendering infrastructure already exists.

### 8.3 Neural Map View

A new visualiser mode alongside Graph/Table/Comparison/Diff:

```
Existing:  [Graph] [Table] [Comparison] [Diff]
New:       [Graph] [Table] [Comparison] [Diff] [Neural Map]
```

Neural Map renders:
- **Only SNG-declared pathways** (not all intra-ontology edges — just the strategic connections)
- **Nodes as ontology namespaces** (one node per ontology, not per entity)
- **VSOM at top** (apex, largest node)
- **Edge thickness = pathway weight** (visual priority)
- **Edge colour = pathway type** (spine/bridge/constraint/feedback)
- **Edge arrows = direction** (top-down, bottom-up, lateral, bidirectional)
- **PFI filter active** = only pathways surviving instanceOntologies filter
- **pfiOverrides applied** = strengthened weights render thicker

See: [Epic 40: Workbench](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/577) — Neural Map becomes a workbench panel

---

## 9. PFI Instance Extension — The Quasi-OO Cascade

Each PFI instance inherits the full PFC-SNG and extends via `pfiOverrides`:

```
PFC-SNG (base neural graph — all instances inherit)
  |
  +-- PFI-BAIV-SNG
  |     Strengthens: Metrics-to-Market (7 -> 9, competitive intel is core)
  |     Adds: AI Visibility Competitive pathway (VP -> INDUSTRY/ORG-CTX, weight 9)
  |     Adds cluster: BAIV-Competitive
  |
  +-- PFI-W4M-WWG-SNG
  |     Strengthens: Value-to-Fulfilment (8 -> 10, supply chain IS the business)
  |     Adds: Fulfilment Corridor pathway (LSC -> OFM, weight 9)
  |     Adds cluster: WWG-Fulfilment
  |
  +-- PFI-AIRL-CAF-AZA-SNG
        Strengthens: Scorecard-to-Risk (6 -> 9), Governance Constraint (7 -> 9)
        Adds cluster: AIRL-Assessment
        (Note: governance constraints become PRIMARY pathways, not secondary)
```

Same quasi-OO cascade as the [Unified Registry](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md):
- **Inherit** — take PFC pathway weights as-is
- **Strengthen** — increase weight for domain-critical pathways
- **Add** — new pathways unique to this instance
- **Disable** — (future) suppress pathways not relevant to instance

---

## 10. Why This Is the Moat

### 10.1 The Graph IS the IP

The PFC-SNG is not documentation — it's a **machine-readable strategic architecture**:

- **Agents traverse it** — an agent asked to "improve our value proposition" walks VSOM → VP → RRR → KPI, not randomly searching ontologies
- **PFI instances inherit it** — every new instance gets the neural pathways for free
- **Competitors can't replicate it** — the pathway weights encode years of strategic refinement
- **It compounds** — every PFI instance, every agent interaction refines the weights

### 10.2 What Competitors Would Need to Copy

| Asset | Our Position | Time to Replicate |
|---|---|---|
| 52 ontologies | Built, validated, OAA-compliant | 12-18 months |
| 8 graph series | Organised, mapped, gap-analysed | 6-9 months |
| VE Skill Chain | Encoded, tested, PFI-proven | 6-12 months |
| **PFC-SNG pathway weights** | **Refined through 5+ PFI instances** | **2-3 years minimum** |
| **Cross-series bridges** | **VP-RRR, BSC-GRC, KPI-Industry — validated** | **Cannot be guessed, must be discovered** |
| **PFI weight overrides** | **Domain-specific tuning from real deployments** | **Requires live client data** |

The ontologies are the vocabulary. The PFC-SNG is the **grammar**. You can steal a dictionary but you can't steal fluency.

### 10.3 Network Effects (Synergy Compounding)

```
More PFI instances ──> more pathway traversals ──> better weights
Better weights ──> smarter agents ──> better client outcomes
Better outcomes ──> more clients ──> more traversals
                                              |
                                              v
                                       Compounding moat
                                     (synergy on the edges)
```

---

## 11. Suggested Epic/Feature Planning

| Item | Details | Related |
|---|---|---|
| **Epic** | Epic 64: PFC Strategic Neural Graph | Child of [Epic 34](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518) |
| **F64.1** | Add `strategicNeuralGraph{}` section to `ont-registry-index.json` | Registry index |
| **F64.2** | Pathway catalogue — all bridges, constraints, feedback loops with weights | This briefing |
| **F64.3** | Neural cluster definitions — agent-loadable co-activation groups | [Epic 49](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/747) |
| **F64.4** | PFI-SNG overrides — BAIV, W4M-WWG, AIRL extend base graph | [Epic 45](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/634) |
| **F64.5** | `sng-renderer.js` + `sng-filter.js` — Neural Map visualiser view | [Epic 40](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/577) |
| **F64.6** | Constraint + Feedback edge styles in `state.js` / `graph-renderer.js` | Visualiser |
| **F64.7** | Agent pathway navigation — task → cluster → traversal | [Epic 34 S3](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518) |
| **F64.8** | Weight refinement pipeline — PFI usage → weight adjustment | Future (Horizon 2) |

### Dependency Chain

```
F64.1 (registry schema) ──> F64.2 (pathways) ──> F64.3 (clusters)
                                    |
                              F64.4 (PFI overrides)
                                    |
F64.6 (edge styles) ──────> F64.5 (Neural Map view)
                                    |
                              F64.7 (agent navigation)
                                    |
                              F64.8 (weight refinement) [Horizon 2]
```

### Sequencing Before Epic 59

This must complete before [Epic 59: DTP Database Sync](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/840) because:
- Epic 59 syncs the registry to Supabase JSONB
- The SNG pathways must exist in the registry BEFORE they're synced to the database
- Database schema design depends on knowing the SNG structure
- File-first → architecture-complete → then database

---

## 12. Cross-Epic Synergy Map

Every edge in this map IS a synergy — emergent value that neither epic creates alone:

```
Epic 34 (Platform Strategy)
  |
  +==> Epic 64 (PFC-SNG) <============ THIS
  |      |
  |      +──> Epic 40 (Workbench)         Neural Map as workbench panel
  |      +──> Epic 49 (App Planner)       Clusters drive skeleton selection
  |      +──> Epic 45 (W4M-WWG)           Fulfilment pathway validation
  |      +──> Epic 41 (OFM/LSC)           Fulfilment corridor bridge
  |      +──> Epic 30 (GRC Series)        Constraint layer definition
  |      +──> Epic 19 (EMC Scope Rules)   SNG + EMC complementary filtering
  |      |
  |      +──> Epic 59 (DTP DB Sync)       SNG must be file-complete first
  |             |
  |             +──> Epic 31 (CI/CD)      Promotion validates SNG integrity
  |             +──> Epic 58 (PFC Triad)  PFC-dev/test/prod SNG consistency
  |
  +──> F49.9 (Figma/Pencil)              SNG as .pen design artefact
```

---

*PFC Strategic Neural Graph — The Encoded Architecture of Ideas*
*A Weighted Directed Graph with Typed Edges*
*Status: DRAFT — Architectural Concept, ready for team review*
