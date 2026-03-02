# BRIEFING: DELTA Process — Industry-Agnostic Discovery & Gap Analysis

**Date**: 2026-02-26
**Classification**: PFC-Core (industry/market agnostic)
**Series**: PE-Series (process) + VE-Series (strategy analysis & communication)
**Scope**: PFC base → PFI instance variants → Adaptive Graph integration
**Related Epics**: 34 (Platform Strategy), 48 (VE Pipeline), 49 (Skilled Application Planner)
**Product Context**: SaaS/AI solution — mid-market — starting with AI Visibility (PFI-BAIV)
**Team**: 2 principals driving DELTA as the core product engine

---

## 1. Vision

**One sentence**: A universal, structured discovery-to-adaptation process that extracts context, assesses current vs. desired state, quantifies the delta, and drives measurable gap closure — regardless of industry, domain, or scale.

**The name**: **DELTA** — **D**iscover, **E**valuate, **L**everage, **T**ransform, **A**dapt.

The word "delta" itself means the gap, the change, the difference. The 5-phase cycle embodies the entire process: find the truth of where you are, define where you need to be, quantify what separates the two, act on the highest-impact levers, then adapt as evidence accumulates.

### 1.1 Product Positioning

DELTA is not just a methodology — **it is the core engine of the SaaS/AI product**. The process itself is what mid-market clients pay for: structured, evidence-backed discovery and gap closure delivered through an AI-powered platform.

**The product stack**:

```
PFC DELTA (universal process — industry agnostic)
  └── PFI-BAIV DELTA (marketing / AI visibility instance)
        └── SaaS Product (self-service for mid-market)
              └── First vertical: AI Visibility Assessment & Gap Closure
```

**The client journey** (mid-market company, first engagement):

| Phase | Client Experience | AI Delivers |
|---|---|---|
| **D** Discover | "Tell us about your business and marketing posture" | Context extraction, stakeholder mapping, evidence gathering |
| **E** Evaluate | "Here's where you stand vs best-practice and competitors" | Current-state scoring, gap quantification, MECE decomposition |
| **L** Leverage | "These 3 levers have the highest ROI — here's the evidence" | Sensitivity analysis, hypothesis testing, prioritised recommendations |
| **T** Transform | "Here's your action plan — content, channels, AI optimisation" | OKR cascade, KPI instrumentation, initiative planning |
| **A** Adapt | "Visibility scores improved X%. Next cycle targets Y" | Variance analysis, trend monitoring, next-cycle recommendations |

**Why PFC-PFI hybrid**: The DELTA process logic (phases, gates, MECE discipline, evidence chains) is universal PFC. The marketing domain knowledge (AI visibility metrics, channel assessment, competitive positioning frameworks) is PFI-BAIV. This separation means the same process can be re-skinned for any vertical without re-engineering the engine.

**Relationship to Epic 48/49**: DELTA encompasses the VE pipeline. Phase 2 (Evaluate) invokes VE-SA analysis tools. Phase 4 (Transform) feeds into the Epic 48 strategic cascade and Epic 49 delivery planning. DELTA adds the structured discovery front-end (Phase 1) and the adaptation back-end (Phase 5) that the current VE pipeline lacks.

### 1.2 Design Principles

1. **Process over domain** — The DELTA cycle governs HOW discovery and gap closure happen. WHAT is discovered is layered in by context (ORG-CONTEXT, INDUSTRY, sector-specific ontologies).
2. **Scale-agnostic** — The same 5 phases work whether scoping a single process improvement (narrow), a product-market fit assessment (medium), or an enterprise-wide transformation (broad). Scale is a parameter, not a structural change.
3. **MECE-native** — Every decomposition follows REASON-ONT's MECE discipline. No gaps, no overlaps, explicit evidence chains.
4. **Ontology-composed** — DELTA doesn't reinvent; it orchestrates existing PFC building blocks (VE-SA analysis, VE-SC communication, PE processes, Foundation context) into a repeatable cycle.
5. **PFC first, PFI second** — The core cycle is domain-agnostic. PFI instances bring industry context, sector-specific discovery templates, and domain vocabularies.
6. **Product-grade from day one** — Every artifact, gate, and evidence chain is designed for client-facing delivery, not internal-only use. The output IS the product.

---

## 2. The DELTA Cycle — 5 Phases

```
                    ┌─────────────────────────────────────────┐
                    │              ADAPT (A)                   │
                    │   Monitor · Feedback · Learn · Iterate   │
                    └────────────────┬────────────────────────┘
                                     │ ◄── evidence loop
                                     ▼
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│  DISCOVER (D) │───►│ EVALUATE (E) │───►│ LEVERAGE (L) │
│               │    │               │    │               │
│ Context       │    │ Current State │    │ Impact Levers │
│ Stakeholders  │    │ Future State  │    │ Hypotheses    │
│ Evidence      │    │ Gap Δ         │    │ Options       │
│ Scope         │    │ MECE Trees    │    │ Priorities    │
└──────────────┘    └──────────────┘    └──────┬───────┘
                                                │
                                                ▼
                                        ┌──────────────┐
                                        │ TRANSFORM (T) │
                                        │               │
                                        │ Plan          │
                                        │ Execute       │
                                        │ Deliver       │
                                        └──────────────┘
```

### Phase 1: DISCOVER — "What is the truth of where we are?"

**Purpose**: Extract and structure context. Map the territory. Surface raw evidence before analysis.

**Activities**:
- Scope definition (narrow / functional / enterprise / market)
- Stakeholder identification and perspective capture
- Context gathering (org context, market context, regulatory context)
- Evidence collection (quantitative data, qualitative observations, documentation)
- Current-state artefact capture (processes, capabilities, metrics, pain points, opportunities)

**Ontology building blocks**:
- `orgctx:OrganizationContext` — the organisational anchor
- `orgctx:MarketContext` — market positioning and segments
- `rrr:ExecutiveRole` / `rrr:OperationalRole` — stakeholder mapping
- `pe:ProcessArtifact` — captured evidence artefacts
- `rsn:StrategicQuestion` — the driving question(s) framing the discovery

**Skills invoked**:
- `pfc-org-context` — Foundation context extraction
- `pfc-discovery-scope` (new) — Scoping and stakeholder mapping
- `pfc-evidence-gather` (new) — Structured evidence collection

**Gate G1 (Discovery Complete)**: Scope defined, stakeholders mapped, minimum 3 evidence sources captured, driving question(s) formulated, context artefact validated.

---

### Phase 2: EVALUATE — "What separates where we are from where we need to be?"

**Purpose**: Assess current state against desired future state. Decompose the gap using MECE discipline. This is where the analytical power of VE-SA is unleashed.

**Activities**:
- Current-state assessment (structured baseline using BSC perspectives or domain framework)
- Future-state definition (vision-aligned target using VSOM components)
- MECE decomposition of the gap into analysable branches
- Framework-driven analysis per branch (SWOT, Porter's, Logic Trees, Capability Assessment, etc.)
- Gap quantification — each gap gets a magnitude, impact, and urgency score
- Evidence chain construction — every gap claim links to evidence

**Ontology building blocks**:
- `rsn:MECETree` + `rsn:MECEBranch` — structured decomposition
- `rsn:LogicTree` + `rsn:LogicTreeNode` — quantitative driver analysis
- `ind:SWOTAnalysis` / `ind:SWOTFactor` — strength/weakness/opportunity/threat capture
- `ga:IdentifiedGap` — formalised gap statements
- `ea:CapabilityGap` — capability-level gaps (when in scope)
- `bsc:BSCPerspective` — balanced assessment framework (Financial / Customer / Internal / Learning)
- `bsc:ValueChainActivity` — current maturity scoring (1.0–5.0)
- `vsom:VisionComponent` — future-state aspiration
- `vsom:ObjectivesComponent` — measurable future-state targets
- `rsn:EvidenceItem` — supporting/contradicting/neutral evidence (anti-confirmation-bias)

**Skills invoked**:
- `pfc-vsom` — Future-state vision and strategy definition
- `pfc-industry-analysis` — SWOT/TOWS/Porter's (when market-level scope)
- `pfc-macro-analysis` — PESTEL/Scenario (when macro-level scope)
- `pfc-delta-evaluate` (new) — Current-vs-future state gap decomposition
- `pfc-reason` (new, wraps REASON-ONT) — MECE decomposition and logic tree analysis

**Gate G2 (Evaluation Complete)**: MECE tree validated (isMECE = true), minimum one gap formally quantified per branch, evidence chains documented, current-state baseline scored, future-state targets defined, overall gap magnitude assessed.

---

### Phase 3: LEVERAGE — "Which levers have the highest impact on closing the gap?"

**Purpose**: Identify the highest-impact levers for gap closure. Generate hypotheses. Test assumptions. Prioritise intervention candidates.

**Activities**:
- Sensitivity analysis on logic tree nodes — identify top-3 highest-impact levers
- Hypothesis formation — testable statements about how to close each gap
- Assumption identification — especially MustBeTrue assumptions (REASON-ONT discipline)
- Option generation — candidate interventions per lever
- Impact-effort mapping — prioritise by ROI, feasibility, time-to-impact
- Cross-gap dependency analysis — which closures enable others?
- Strategic recommendation synthesis

**Ontology building blocks**:
- `rsn:StrategicHypothesis` — testable hypothesis per lever
- `rsn:HypothesisAssumption` — MustBeTrue / Important / NiceToHave
- `rsn:AnalysisSynthesis` — convergent/divergent/blind-spot synthesis
- `rsn:StrategicRecommendation` — actionable output with evidence chains
- `rsn:LogicTreeNode.sensitivityRank` — top levers → strategic objectives
- `kpi:VEAnalysis` — value engineering for high-cost interventions
- `kpi:VEFunction` — cost-function-value examination
- `ind:TOWSStrategy` — action-oriented strategic options

**Skills invoked**:
- `pfc-reason` — Hypothesis testing, sensitivity analysis, synthesis
- `pfc-delta-leverage` (new) — Impact-effort prioritisation, option generation
- `pfc-kpi` — Measurement definition for intervention tracking

**Gate G3 (Leverage Complete)**: Top-3 levers identified with sensitivity analysis, minimum one hypothesis per lever with MustBeTrue assumptions tested, strategic recommendations generated with evidence chains, contradicting evidence explicitly sought (BR-RSN-008), impact-effort prioritisation complete.

**Business Rule (DELTA-specific)**: If a MustBeTrue assumption is invalidated at G3, the process MUST loop back to Evaluate (Phase 2) to reframe the gap before proceeding. This prevents acting on false premises.

---

### Phase 4: TRANSFORM — "Execute the highest-impact interventions."

**Purpose**: Plan and execute interventions. Translate strategic recommendations into operational delivery.

**Activities**:
- Intervention planning — initiatives, milestones, resource allocation
- OKR definition — objectives and key results tied to gap closure
- BSC operationalisation — measures linked to KPIs
- Execution — deliver changes (process, capability, product, organisational)
- Quick wins — identify and execute rapid-value opportunities (K-DMAIC pattern)
- Communication — stakeholder narrative, cascade, visual strategy

**Ontology building blocks**:
- `ppm:Initiative` / `ppm:Milestone` — project/programme management
- `okr:Objective` + `okr:KeyResult` — OKR cascade
- `bsc:BalancedScorecard` + `bsc:BSCObjective` + `bsc:BSCMeasure` — balanced measurement
- `kpi:KPI` + `kpi:Metric` — operational measurement
- `vp:ValueProposition` — value articulation (VP-RRR alignment)
- `nar:StrategicNarrative` — communication of the transformation story
- `cas:CascadeTranslation` — audience-specific messaging (VE-SC)
- `efs:Epic` / `efs:Feature` / `efs:Story` — delivery breakdown (when software/digital)

**Skills invoked**:
- `pfc-okr` — OKR definition
- `pfc-kpi` — KPI/metric definition
- `pfc-vp` — Value proposition (with RRR alignment)
- `pfc-efs` — Epic-Feature-Story breakdown (digital delivery)
- `pfc-ppm-plan` — Programme/project planning
- `pfc-delta-narrate` (new) — Transformation narrative using VE-SC patterns

**Gate G4 (Transform Complete)**: Interventions delivered, OKRs baselined, KPIs instrumented, stakeholder communication completed, quick wins captured with visual controls.

---

### Phase 5: ADAPT — "Monitor, learn, and evolve."

**Purpose**: Close the feedback loop. Monitor intervention impact against the gap baseline. Learn from outcomes. Adapt the approach. Feed insights back into the next DELTA cycle.

**Activities**:
- KPI monitoring against gap-closure targets
- Variance analysis — actual vs planned impact
- Feedback collection — stakeholder, process, market signals
- Threshold breach detection — triggers re-evaluation
- Lesson capture — what worked, what didn't, what was surprising
- Cycle determination — minor adjustment, major pivot, or full revision
- Input preparation for next DELTA cycle (if gap not fully closed)

**Ontology building blocks**:
- `vsom:StrategicReviewCycle` — the feedback loop entity
  - TriggerTypes: MetricBreach, MarketEvent, CompetitorAction, Scheduled
  - Outputs: None, Minor-Adjustment, Major-Pivot, Full-Revision
- `vsom:MetricsComponent` — threshold monitoring (Critical/Warning/Target)
- `kpi:KPI.trend` — Improving/Stable/Declining/Volatile
- `rsn:AnalysisSynthesis` — updated synthesis with new evidence
- `pe:ProcessPattern` — PDCA sub-cycle for continuous adaptation

**Skills invoked**:
- `pfc-delta-adapt` (new) — Variance analysis, feedback synthesis, cycle recommendation
- `pfc-kpi` — Metric refresh and trend assessment

**Gate G5 (Adaptation Complete)**: Variance analysis completed, lessons captured, cycle output determined (None/Adjust/Pivot/Revise), next-cycle inputs prepared if gap remains open.

**Business Rule (DELTA-specific)**: Critical threshold breach (MetricsComponent) MUST trigger a MetricBreach review cycle, which re-enters at Phase 2 (Evaluate) with updated evidence. This is not optional — it's the self-correcting mechanism.

---

## 3. Structural Decision — Ontology vs Instance Data

Following the K-DMAIC precedent (DD-K-001):

| Question | Answer |
|---|---|
| Does DELTA introduce genuinely new entity types? | **No** — Every entity maps to existing PE, VE-SA, VE-SC, Foundation types |
| Does it introduce new relationships? | **Partially** — The phase-to-ontology invocation mappings are new, but expressible as `pe:invokesSkill` + `pe:PathStep` |
| Does it introduce new business rules? | **Yes** — DELTA-specific rules (G3 invalidation loop, G5 threshold breach re-entry) |
| Recommendation | **PE-ONT process template** (instance data) + **DELTA skill chain** (PFC skills framework) |

**Architecture**:
```
┌─────────────────────────────────────────────────────┐
│  DELTA Skill Chain (pfc-delta-pipeline)              │
│  Orchestrates: pfc-delta-discover → evaluate →       │
│  leverage → transform → adapt                        │
│                                                      │
│  Each phase invokes existing PFC skills              │
│  (org-context, vsom, industry, reason, kpi, etc.)    │
└──────────────────────┬──────────────────────────────┘
                       │ modelled as
                       ▼
┌─────────────────────────────────────────────────────┐
│  PE-ONT Instance Data Template                       │
│  pe-delta-process-template-v1.0.0.jsonld             │
│                                                      │
│  pe:Process (type: discovery, scope: universal)      │
│  5× pe:ProcessPhase (D, E, L, T, A)                 │
│  5× pe:ProcessGate (G1–G5)                           │
│  15× pe:ProcessArtifact (per phase)                  │
│  8× pe:ProcessMetric                                 │
│  6× pe:Skill (new DELTA-specific skills)             │
│  3× pe:AIAgent                                       │
│  1× pe:ProcessPath (5 PathSteps, 5 PathLinks)        │
│  2× pe:ProcessPattern (DELTA cycle, Adapt sub-loop)  │
└─────────────────────────────────────────────────────┘
```

This mirrors the DMAIC precedent exactly: standard DMAIC = PE-ONT instance data; K-DMAIC (Kaizen) = standalone ontology when new entity types are needed. DELTA uses existing entities, so instance data is the right call.

---

## 4. Cross-Series Ontology Mapping

DELTA is a **cross-series orchestrator** — it doesn't own domain concepts but choreographs them. Here's the mapping of which series contributes what:

### 4.1 Per-Phase Ontology Contributions

| Phase | Foundation | VE-Series | VE-SA | VE-SC | PE-Series |
|---|---|---|---|---|---|
| **D (Discover)** | ORG-CONTEXT, CTX | VSOM (scope) | — | — | PE (Process, Skill) |
| **E (Evaluate)** | ORG-CONTEXT | VSOM, KPI | REASON (MECE, Logic Trees), INDUSTRY (SWOT), BSC, MACRO | — | PE (Artifacts) |
| **L (Leverage)** | — | VSOM, KPI (VE Analysis) | REASON (Hypothesis, Synthesis), INDUSTRY (TOWS) | — | PE (Skill) |
| **T (Transform)** | ORG | VSOM, OKR, KPI, VP, RRR | BSC (operationalise) | NARRATIVE, CASCADE | PE, PPM, EFS |
| **A (Adapt)** | — | VSOM (ReviewCycle), KPI | REASON (updated synthesis) | — | PE (ProcessPattern) |

### 4.2 Cross-Ontology Join Patterns

| Pattern ID | Name | Traversal |
|---|---|---|
| JP-DELTA-001 | Discovery-to-Gap Chain | `orgctx:OrganizationContext → rsn:StrategicQuestion → rsn:MECETree → rsn:MECEBranch → ga:IdentifiedGap` |
| JP-DELTA-002 | Gap-to-Lever Chain | `ga:IdentifiedGap → rsn:LogicTree → rsn:LogicTreeNode [sensitivityRank ≤ 3] → rsn:StrategicHypothesis → rsn:StrategicRecommendation` |
| JP-DELTA-003 | Lever-to-Objective Chain | `rsn:StrategicRecommendation → vsom:ObjectivesComponent → bsc:BSCObjective → okr:Objective → kpi:KPI` |
| JP-DELTA-004 | Objective-to-Delivery Chain | `okr:Objective → ppm:Initiative → efs:Epic → efs:Feature → efs:Story` (when digital) |
| JP-DELTA-005 | Adaptation Feedback Loop | `kpi:KPI [threshold breach] → vsom:StrategicReviewCycle → rsn:StrategicQuestion [updated] → re-enter Phase 2` |
| JP-DELTA-006 | Evidence Lineage | `rsn:EvidenceItem → rsn:StrategicHypothesis → rsn:StrategicRecommendation → vsom:StrategyComponent` — full traceability from evidence to strategy |

### 4.3 The Golden Thread

The complete DELTA traversal — from raw discovery to measured adaptation — is:

```
orgctx:OrganizationContext
  → rsn:StrategicQuestion (what are we trying to answer?)
    → rsn:MECETree (MECE decomposition)
      → rsn:MECEBranch [parallel framework analysis]
        → ga:IdentifiedGap (formalised gaps)
          → rsn:LogicTree → LogicTreeNode [top sensitivity]
            → rsn:StrategicHypothesis (testable)
              → rsn:AnalysisSynthesis (convergent + divergent + blind spots)
                → rsn:StrategicRecommendation (evidence-backed)
                  → vsom:ObjectivesComponent (strategic targets)
                    → bsc:BSCObjective → okr:Objective → kpi:KPI
                      → vsom:StrategicReviewCycle (adaptation loop)
                        → back to rsn:StrategicQuestion
```

This is the **end-to-end discovery-to-adaptation graph traversal**. Every node in this chain has traceability to evidence. Every gap claim links to supporting/contradicting evidence (REASON-ONT anti-confirmation-bias rules).

---

## 5. Scope Scaling — Narrow to Enterprise

DELTA's power is that the same 5-phase process works at any scale. The scale parameter controls which ontologies are invoked and at what depth:

| Scale | Scope Example | Discovery Depth | SA Tools Used | Typical Duration |
|---|---|---|---|---|
| **Narrow** | Single process, single team | One function/department | Logic Trees, basic gap analysis | 1–2 weeks |
| **Functional** | Department, product line | Cross-functional | SWOT, BSC (subset), Logic Trees | 2–4 weeks |
| **Enterprise** | Whole organisation | All BSC perspectives | Full SA stack (MACRO, INDUSTRY, BSC, REASON, PORTFOLIO) | 4–12 weeks |
| **Market** | Industry/sector landscape | Organisation + competitors | Full SA + INDUSTRY-ONT competitive positioning | 8–16 weeks |

**Scale-dependent phase behaviour**:

- **Narrow scope**: Phase 2 (Evaluate) uses Logic Trees and basic current-vs-target scoring. PESTEL and Porter's are skipped. Phase 4 (Transform) might not need PPM — direct to action.
- **Enterprise scope**: Phase 2 uses the full VE-SA 5-layer stack. Phase 4 engages PPM portfolio management and EFS delivery planning.
- **Market scope**: Phase 1 (Discover) extends to competitor analysis and market sizing. Phase 3 (Leverage) uses competitive positioning and Ansoff growth matrix.

The skill chain handles this via `--scope narrow|functional|enterprise|market` parameter, which controls which sub-skills are invoked at each phase.

---

## 6. New Skills Required (PFC-Level)

Six new DELTA-specific skills, all PFC-level (domain-agnostic):

| Skill | Type | Phase | Purpose |
|---|---|---|---|
| `pfc-delta-pipeline` | orchestration | All | Master orchestrator — runs the 5-phase DELTA cycle |
| `pfc-delta-scope` | extraction | D | Scoping frame definition, stakeholder mapping, driving question formulation |
| `pfc-delta-evaluate` | analysis | E | Current-vs-future state assessment, gap quantification, baseline scoring |
| `pfc-delta-leverage` | analysis | L | Impact-effort prioritisation, option generation, dependency mapping |
| `pfc-delta-narrate` | generation | T | Transformation narrative using VE-SC patterns (NARRATIVE, CASCADE) |
| `pfc-delta-adapt` | analysis | A | Variance analysis, feedback synthesis, cycle determination |

**Existing skills reused** (no changes needed):
- `pfc-org-context` (Phase 1)
- `pfc-vsom` (Phases 1, 2, 4)
- `pfc-macro-analysis` (Phase 2, enterprise/market scope)
- `pfc-industry-analysis` (Phase 2, functional/enterprise/market scope)
- `pfc-okr` (Phase 4)
- `pfc-kpi` (Phases 3, 4, 5)
- `pfc-vp` (Phase 4)
- `pfc-efs` (Phase 4, digital delivery)
- `pfc-ppm-plan` (Phase 4, enterprise scope)

**New skill needed (cross-cutting)**:
- `pfc-reason` — wraps REASON-ONT: MECE decomposition, hypothesis testing, logic trees, synthesis. Used across Phases 2, 3, 5. This skill doesn't exist yet and is high-value beyond DELTA.

---

## 7. PFC → PFI Instance Strategy

### 7.1 What stays PFC (universal)

- The 5-phase DELTA cycle structure
- Gate criteria (G1–G5)
- MECE discipline and evidence chain requirements
- Scale parameters (narrow/functional/enterprise/market)
- The DELTA skill chain orchestrator
- Cross-ontology join patterns
- Business rules (G3 invalidation loop, G5 threshold re-entry)

### 7.2 What PFI instances customise

| PFI Instance | Domain Context | Discovery Templates | Sector-Specific Ontologies |
|---|---|---|---|
| **PFI-BAIV** (MarTech) | Digital marketing, lead generation, AI visibility | Marketing channel discovery, AI maturity assessment, brand positioning gap | BAIV instance data, INDUSTRY-ONT (digital) |
| **PFI-W4M-WWG** (Food Import) | Supply chain, sourcing, trade compliance | Corridor analysis, supplier capability gaps, fulfilment process discovery | LSC-ONT, OFM-ONT, W4M instance data |
| **PFI-AIRL** (Azure AI Readiness) | Cloud infrastructure, AI/ML, security compliance | Azure landing zone assessment, AI readiness scoring, CAF control gaps | GRC-FW-ONT, NCSC-CAF instance data |
| **PFI-VHF** (B2C Health & Fitness) | Nutrition, training, client management | Client assessment discovery, programme gap analysis, outcome tracking | NUT-ONT (PE-B2C-NUT), VHF instance data |

### 7.3 PFI Instance Overlay Pattern

```
PFC DELTA Process Template (pe-delta-process-template-v1.0.0.jsonld)
  │
  ├── PFI-BAIV DELTA Variant
  │     └── instanceOntologies: [VP, RRR, KPI, BSC, EMC, INDUSTRY]
  │     └── discoveryTemplate: "marketing-channel-discovery"
  │     └── scopeDefault: "functional"
  │
  ├── PFI-W4M-WWG DELTA Variant
  │     └── instanceOntologies: [VP, RRR, LSC, OFM, KPI, BSC, EMC]
  │     └── discoveryTemplate: "supply-chain-corridor-discovery"
  │     └── scopeDefault: "enterprise"
  │
  ├── PFI-AIRL DELTA Variant
  │     └── instanceOntologies: [VP, RRR, GRC-FW, NCSC-CAF, KPI, BSC]
  │     └── discoveryTemplate: "azure-readiness-assessment"
  │     └── scopeDefault: "enterprise"
  │
  └── PFI-VHF DELTA Variant
        └── instanceOntologies: [VP, RRR, KPI, NUT]
        └── discoveryTemplate: "client-health-assessment"
        └── scopeDefault: "narrow"
```

The EMC composition engine's `constrainToInstanceOntologies()` function already handles this two-layer filter — DELTA categories define the broad ontology universe, `instanceOntologies` narrows to the PFI's declared set.

---

## 8. DELTA vs Existing Methodologies

| Methodology | Focus | DELTA Relationship |
|---|---|---|
| **DMAIC** (standard) | Process defect reduction, statistical | DELTA Phase 2-3 can invoke DMAIC as a nested process when the gap is process-quality related |
| **K-DMAIC** (Kaizen) | Rapid improvement blitz | DELTA Phase 4 (Transform) can invoke K-DMAIC for quick-win execution |
| **VE Pipeline** (Epic 48) | Full strategic planning cycle | DELTA subsumes the VE pipeline — Phases 2-4 are an expanded version of the VE 7-phase flow |
| **TOGAF ADM** | Enterprise architecture cycle | DELTA at enterprise scope parallels ADM phases; EA-CORE-ONT entities populate Phase 2 capability gaps |
| **Design Thinking** | User-centred problem solving | DELTA Phase 1 (Discover) parallels Empathize; Phase 2 parallels Define; Phase 3 parallels Ideate |
| **PDCA** | Continuous improvement | DELTA Phase 5 (Adapt) uses PDCA as a sub-cycle pattern (pe:ProcessPattern) |

**Key distinction**: DELTA is not another methodology — it's the **meta-process** that orchestrates methodology-specific tools at each phase. A DELTA cycle at narrow scope might use only Logic Trees. At enterprise scope, it orchestrates PESTEL + SWOT + BSC + MECE + Logic Trees + TOWS + Ansoff + VE Analysis across Phase 2-3.

---

## 9. Adaptive Graph Integration

DELTA is designed to feed the PFC adaptive graph strategy (Epic 34, S1):

### 9.1 Graph Outputs per Phase

| Phase | Graph Contribution |
|---|---|
| D (Discover) | Context subgraph — org, stakeholders, scope boundaries |
| E (Evaluate) | Gap subgraph — MECE trees, current/future state nodes, gap edges |
| L (Leverage) | Hypothesis subgraph — lever nodes, sensitivity weights, evidence chains |
| T (Transform) | Delivery subgraph — OKR cascade, BSC measures, initiative nodes |
| A (Adapt) | Feedback subgraph — metric trends, review cycle triggers, adaptation edges |

### 9.2 EMC Composition Categories

DELTA outputs map to these EMC composition categories:
- **STRATEGIC** — Phases 2-3 (gap analysis, hypothesis, recommendations)
- **OPERATIONAL** — Phase 4 (delivery planning, process changes)
- **ANALYTICS** — Phases 2-5 (measurement, variance, feedback)
- **COMMUNICATION** — Phase 4 (narrative, cascade)
- **GOVERNANCE** — Phases 3-5 (evidence chains, decision audit trail)

### 9.3 Canonical Snapshot Pattern

Each completed DELTA cycle produces a `CanonicalSnapshot` (EMC-ONT v5.0.0) capturing:
- All gaps identified and their closure status
- All hypotheses tested and their outcomes
- All recommendations and their adoption status
- Gap closure percentage against baseline
- Time series of KPI movements attributed to interventions

---

## 10. Implementation Roadmap

### Phase A: Foundation (Week 1-2)
- [ ] Create `pe-delta-process-template-v1.0.0.jsonld` (PE-ONT instance data)
- [ ] Define 5 ProcessPhases, 5 ProcessGates, ProcessPath with PathSteps
- [ ] Define ProcessArtifacts and ProcessMetrics
- [ ] Register in ontology library (PE-Series)

### Phase B: Core Skills (Week 3-4)
- [ ] `pfc-delta-pipeline` orchestrator skill (master)
- [ ] `pfc-delta-scope` skill (Phase 1)
- [ ] `pfc-delta-evaluate` skill (Phase 2)
- [ ] `pfc-reason` skill (cross-cutting, wraps REASON-ONT)

### Phase C: Extended Skills (Week 5-6)
- [ ] `pfc-delta-leverage` skill (Phase 3)
- [ ] `pfc-delta-narrate` skill (Phase 4, VE-SC)
- [ ] `pfc-delta-adapt` skill (Phase 5)

### Phase D: PFI Instance — BAIV First (Week 7-8)
- [ ] BAIV DELTA variant with marketing discovery template
- [ ] Test: narrow-scope marketing channel gap assessment
- [ ] Test: functional-scope AI maturity discovery
- [ ] Validate full golden thread traversal (JP-DELTA-001 through JP-DELTA-006)

### Phase E: Adaptive Graph Integration (Week 9+)
- [ ] EMC composition category mapping
- [ ] Canonical snapshot generation at cycle completion
- [ ] Visualiser integration for gap-closure dashboards

---

## 11. Relationship to GitHub Issue Structure

This should be raised as an Epic in Azlan-EA-AAA. Suggested structure:

```
Epic N: DELTA Process — Industry-Agnostic Discovery & Gap Analysis
  FN.1: DELTA PE-ONT Process Template
    SN.1.1: Process, phases, gates definition
    SN.1.2: ProcessPath and PathSteps
    SN.1.3: ProcessArtifacts and ProcessMetrics
    SN.1.4: Skills and agents definition
    SN.1.5: Registry registration
  FN.2: DELTA Core Skill Chain (PFC)
    SN.2.1: pfc-delta-pipeline orchestrator
    SN.2.2: pfc-delta-scope skill
    SN.2.3: pfc-delta-evaluate skill
    SN.2.4: pfc-reason skill (cross-cutting)
  FN.3: DELTA Extended Skills
    SN.3.1: pfc-delta-leverage skill
    SN.3.2: pfc-delta-narrate skill (VE-SC)
    SN.3.3: pfc-delta-adapt skill
  FN.4: PFI-BAIV DELTA Variant
    SN.4.1: BAIV discovery templates
    SN.4.2: Narrow-scope test run
    SN.4.3: Functional-scope test run
    SN.4.4: Golden thread validation
  FN.5: Adaptive Graph Integration
    SN.5.1: EMC composition category mapping
    SN.5.2: Canonical snapshot generation
    SN.5.3: Visualiser gap-closure dashboard
```

---

## 12. Success Metrics

| Metric | Target | Measurement |
|---|---|---|
| DELTA cycle completion rate | >80% of initiated cycles reach Phase 5 | `pe:ProcessInstance.status` |
| Gap closure rate per cycle | >60% of identified gaps show measurable improvement | `kpi:KPI.trend = Improving` for gap-linked KPIs |
| Evidence chain integrity | 100% of recommendations trace to evidence | JP-DELTA-006 traversal completeness |
| MECE compliance | 100% of decompositions validated MECE | `rsn:MECETree.isMECE = true` |
| Cross-scope reuse | Same template works at all 4 scales | Successful runs at narrow, functional, enterprise, market |
| PFI variant activation | 3+ PFI instances using DELTA variants | Instance registration count |
| Time to first gap insight | <1 hour for narrow scope, <1 week for enterprise | Phase 2 gate timestamp minus Phase 1 start |

---

*This briefing establishes DELTA as the universal discovery and gap analysis process for PFC-Core. It orchestrates existing VE-SA, VE-SC, PE, and Foundation ontology building blocks into a repeatable, evidence-backed, MECE-disciplined cycle that scales from individual process improvement to enterprise transformation — without being tied to any industry, sector, or functional domain.*

*Next step: Create the PE-ONT process template (`pe-delta-process-template-v1.0.0.jsonld`) and the first DELTA skill (`pfc-delta-pipeline`).*
