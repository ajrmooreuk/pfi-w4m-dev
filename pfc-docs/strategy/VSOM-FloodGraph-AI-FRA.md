# VSOM — PFI-PC-DICE: FloodGraph AI — Construction Flood Risk Assessment Platform

**Document**: PFI-PC-DICE VSOM v1.0.0
**PFI Instance**: PFI-PC-DICE (Paul Cowen — FloodGraph AI)
**Foundation Pattern**: PE-RMF v1.0.0 (Generic Risk Management Framework)
**Date**: 2026-02-24
**Status**: DRAFT — Strategy Definition
**Repo Triad**: DICE-Dev / DICE-test / DICE-prod

---

## 1. POSITIONING — PE-RMF Three-Layer Architecture

PFI-PC-DICE is a **flood risk assessment product** built on the PF-Core PE-RMF pattern. It inherits the generic risk assessment process (Layer 1), adds UK construction regulatory context (Layer 2), and defines the flood-specific value proposition (Layer 3).

```
┌──────────────────────────────────────────────────────────────────────┐
│  LAYER 3 — VP Specifics: Flood Risk Assessment                      │
│  VP-ONT (developers, consultants, LPAs, insurers)                   │
│  RRR-ONT (flood risks → mitigation requirements → assessment results│
│  FLOOD-HAZARD, FLOOD-ZONE, HYDRO, SITE-GEO, SUDS, FRA,            │
│  CONSTRUCTION-RESILIENCE, CLIMATE-PROJECTION                        │
├──────────────────────────────────────────────────────────────────────┤
│  LAYER 2 — Strategic Refinement: UK Construction Sector             │
│  VSOM (product strategy), BSC (FloodGraph performance)              │
│  VSOM-SA: INDUSTRY (construction), REASON, MACRO, PORTFOLIO         │
│  VSOM-SC: NARRATIVE, CASCADE                                        │
│  OKR (quarterly delivery), KPI (assessment quality metrics)         │
│  ORG-CONTEXT (regional planning context, LPA maturity)              │
│  GRC-FW instances: NPPF, BS 85500, CIRIA C753, EA Standing Advice  │
├──────────────────────────────────────────────────────────────────────┤
│  LAYER 1 — PE-RMF Generic Foundation (Inherited)                    │
│  PE (assessment process), PPM (conditional project management)      │
│  OFM (delivery pipeline), LSC (supply chain — surveyors, modellers) │
│  ORG (client organisations), GRC-FW (governance structure)          │
│  EMC (graph composition, scope rules, orchestration)                │
└──────────────────────────────────────────────────────────────────────┘
```

**Ref**: `PBS/STRATEGY/PE-RMF-Generic-Risk-Assessment-Framework.md` for Layer 1 detail.

---

## 2. VISION

> **Democratise expert-level flood risk assessment for UK construction by fusing regulatory knowledge graphs, real-time environmental data, and AI reasoning — reducing FRA turnaround from weeks to hours while eliminating compliance blind spots.**

---

## 3. VALUE PROPOSITION (Layer 3 — VP-ONT + RRR-ONT)

| VP Element | Statement | RRR Alignment |
|------------|-----------|---------------|
| **Customer Segment** | Property developers, planning consultants, LPAs, insurers | — |
| **Problem (→ Risk)** | FRAs are slow (4-8 weeks), expensive (£3k-£15k), inconsistent quality, regulatory complexity growing with climate change | `rrr:Risk` |
| **Solution (→ Requirement)** | AI-augmented graph platform that composes flood, regulatory, site, and construction ontologies into automated assessments with full provenance | `rrr:Requirement` |
| **Benefit (→ Result)** | 80% faster turnaround, 60% cost reduction, zero regulatory omissions, auditable AI reasoning chain, climate-future proofed | `rrr:Result` |

---

## 4. STRATEGIES (S1–S6)

### S1: Graph-First Flood Knowledge Architecture
> All flood risk knowledge modelled as composable ontology graphs, built on PE-RMF Layer 1 composition engine.

- **8 flood-domain ontologies** (Layer 3) composed via EMC-ONT on top of PE-RMF base graph
- Graph-scope rules per flood source (Fluvial, Pluvial, Tidal, Groundwater, Combined)
- Canonical snapshots for audit trail — point-in-time regulatory + environmental state
- Foundation ontologies (ORG, ORG-CONTEXT) inherited from PE-RMF Layer 1

### S2: AI-Augmented Assessment Engine
> Domain agents (Layer 3) injected into PE-RMF generic agent slots to deliver flood-specific intelligence.

- PE-RMF provides: Intake, Data-Gathering, Compliance, Report, Project-Manager, Regulatory-Watch, Archive agents
- DICE injects domain agents into PE-RMF slots:
  - **FRA-Assessment-Agent** → Hazard-Assessment-Agent slot
  - **SuDS-Mitigation-Agent** → Mitigation-Agent slot
  - **Climate-Projection-Agent** → Scenario-Agent slot
  - **FRA-Narrative-Agent** → Narrative-Agent slot

### S3: Regulatory Intelligence & Compliance Automation
> UK flood risk regulatory frameworks modelled as GRC-FW instances (Layer 2), checked by PE-RMF Compliance-Agent.

- **NPPF** — Sequential Test, Exception Test, flood zone classification
- **BS 85500:2015** — Flood resistant/resilient construction guidance
- **CIRIA C753** — SuDS Manual design standards
- **EA Standing Advice** — Decision tree for flood risk by development type
- **EA Climate Change Allowances** — Peak flow/rainfall uplift per epoch
- Regulatory-Watch-Agent (PE-RMF L1) monitors for EA/MHCLG guidance changes

### S4: Multi-Stakeholder Portal & Collaboration
> Role-based views of the assessment graph, following PE-RMF multi-stakeholder pattern.

| Role | View | Key Actions |
|------|------|-------------|
| **Developer** | Site selection, cost estimation, risk summary dashboard | Submit site, compare alternatives, view Sequential Test |
| **Consultant** | Full FRA workspace, ontology browser, evidence attachment | Author assessment, commission surveys, generate report |
| **LPA** | Compliance checklist, test verification, cross-application comparison | Verify Sequential/Exception Test, approve/reject FRA |
| **Insurer** | Risk scoring, portfolio aggregation, claims correlation | Score risk, aggregate portfolio, price premium |

### S5: Instance + Client Customisation (PFI Cascade)
> PFC → PFI-PC-DICE → Client cascade for ontology layering.

- **PFI level**: Flood-domain ontologies, UK regulatory GRC-FW instances, standard assessment templates
- **Client level**: Regional SFRA data overlays, preferred SuDS catalogues, corporate risk appetite thresholds, white-label branding
- Client configurations stored as EMC instance variants within PFI-PC-DICE

### S6: Integration & Enterprise Architecture
> External data sources and platform integrations, following PE-RMF LSC supply chain pattern.

| Integration | Type | PE-RMF Role |
|-------------|------|-------------|
| EA Flood Map for Planning API | Data Provider | `lsc:DataProvider` |
| OS MasterMap / OS Places API | Data Provider | `lsc:DataProvider` |
| BGS Geology / GeoIndex | Data Provider | `lsc:DataProvider` |
| Met Office UKCP18 | Data Provider | `lsc:DataProvider` |
| LPA Planning Portal | Submission target | `pe:ExternalSystem` |
| BIM/IFC models | Import source | `pe:DataInput` |
| Topographic surveyors | Survey Provider | `lsc:SurveyProvider` |
| Hydraulic modelling firms | Specialist Modeller | `lsc:SpecialistModeller` |

---

## 5. ONTOLOGY STACK

### 5.1 Layer 1 — PE-RMF Foundation (Inherited)

| Ontology | Series | PE-RMF Role |
|----------|--------|-------------|
| **PE-ONT** | PE | Assessment process lifecycle (5 phases) |
| **PPM-ONT** | PE | Project management — activates for Level 2+ FRAs |
| **OFM-ONT** | PE | Operational fulfilment — assessment delivery pipeline |
| **LSC-ONT** | PE | Supply chain — surveyors, modellers, data providers |
| **ORG-ONT** | Foundation | Client organisations |
| **ORG-CONTEXT** | Foundation | Organisational structure (foundation level) |
| **GRC-FW-ONT** | GRC | Governance structure (controls, policies, compliance checks) |
| **EMC-ONT** | Orchestration | Graph composition, scope rules, orchestration |

### 5.2 Layer 2 — Strategic Refinement (UK Construction Sector)

| Ontology | Series | DICE Role |
|----------|--------|-----------|
| **VSOM-ONT** | VE | FloodGraph product vision and strategy |
| **BSC-ONT** | VE/VSOM-SA | FloodGraph balanced scorecard |
| **INDUSTRY-ONT** | VE/VSOM-SA | UK construction sector context |
| **REASON-ONT** | VE/VSOM-SA | Strategic reasoning for product decisions |
| **MACRO-ONT** | VE/VSOM-SA | Macro-environmental factors (climate policy, planning reform) |
| **PORTFOLIO-ONT** | VE/VSOM-SA | Product portfolio positioning |
| **NARRATIVE-ONT** | VE/VSOM-SC | Strategy communication narrative |
| **CASCADE-ONT** | VE/VSOM-SC | Strategy cascade to team objectives |
| **OKR-ONT** | VE | Quarterly delivery objectives |
| **KPI-ONT** | VE | Assessment quality metrics, turnaround KPIs |
| **ORG-CONTEXT** | Foundation | Regional planning context, LPA maturity (sector level) |
| **GRC-FW instances** | GRC | NPPF, BS 85500, CIRIA C753, EA Standing Advice, EA Climate Allowances |

### 5.3 Layer 3 — Flood Domain (PFI-PC-DICE Specific)

| Ontology | Prefix | Description |
|----------|--------|-------------|
| **VP-ONT** | `vp:` | Customer segments (developers, consultants, LPAs, insurers), problems, solutions, benefits |
| **RRR-ONT** | `rrr:` | Flood risks, mitigation requirements, assessment results |
| **FLOOD-HAZARD-ONT** | `fh:` | Flood sources (fluvial, pluvial, tidal, groundwater, sewer), return periods, climate scenarios, depth-velocity matrices, hazard ratings |
| **FLOOD-ZONE-ONT** | `fz:` | EA Flood Zones (1, 2, 3a, 3b, functional floodplain), SFRA zones, flood defences, standard of protection |
| **HYDRO-ONT** | `hydro:` | Watercourses, catchments, drainage basins, rainfall-runoff models, FEH/ReFH2 parameters |
| **SITE-GEO-ONT** | `sg:` | Site geometry (GeoSPARQL), elevation model (DTM/DSM), soil/geology, permeability, groundwater vulnerability |
| **SUDS-ONT** | `suds:` | SuDS typology (CIRIA C753), treatment train design, attenuation volumes, greenfield runoff rates |
| **FRA-ONT** | `fra:` | FRA document structure, Sequential Test, Exception Test, assessment sections, evidence requirements |
| **CONSTRUCTION-RESILIENCE-ONT** | `cr:` | Flood resistance/resilience measures (BS 85500), finished floor levels, flood doors/barriers, material classifications |
| **CLIMATE-PROJECTION-ONT** | `cp:` | UKCP18 scenarios (RCP 2.6/4.5/8.5), epoch allowances (2050s/2080s), peak flow uplift, sea level rise |

### 5.4 EMC Instance Configuration

```json
{
  "instanceId": "PFI-PC-DICE",
  "instanceName": "FloodGraph AI — Paul Cowen",
  "foundationPattern": "PE-RMF v1.0.0",
  "instanceOntologies": [
    "PE", "PPM", "OFM", "LSC", "ORG", "ORG-CONTEXT", "GRC-FW", "EMC",
    "VSOM", "BSC", "INDUSTRY", "REASON", "MACRO", "PORTFOLIO",
    "NARRATIVE", "CASCADE", "OKR", "KPI",
    "VP", "RRR",
    "FLOOD-HAZARD", "FLOOD-ZONE", "HYDRO", "SITE-GEO",
    "SUDS", "FRA", "CONSTRUCTION-RESILIENCE", "CLIMATE-PROJECTION"
  ],
  "requirementScopes": [
    "COMPLIANCE", "PRODUCT", "OPERATIONAL", "ANALYTICS", "SECURITY"
  ],
  "graphScopeRules": [
    {
      "ruleId": "GSR-FG-001",
      "name": "Fluvial FRA Composition",
      "conditions": [{ "field": "fra:floodSource", "operator": "equals", "value": "Fluvial" }],
      "actions": [{ "include": ["FLOOD-HAZARD", "FLOOD-ZONE", "HYDRO", "SITE-GEO", "SUDS", "FRA", "CLIMATE-PROJECTION", "GRC-FW"] }]
    },
    {
      "ruleId": "GSR-FG-002",
      "name": "Tidal/Coastal FRA Composition",
      "conditions": [{ "field": "fra:floodSource", "operator": "equals", "value": "Tidal" }],
      "actions": [{ "include": ["FLOOD-HAZARD", "FLOOD-ZONE", "SITE-GEO", "FRA", "CLIMATE-PROJECTION", "CONSTRUCTION-RESILIENCE", "GRC-FW"] }]
    },
    {
      "ruleId": "GSR-FG-003",
      "name": "Full Multi-Source FRA",
      "conditions": [{ "field": "fra:assessmentType", "operator": "equals", "value": "Level3-Detailed" }],
      "actions": [{ "include": ["ALL"] }]
    },
    {
      "ruleId": "GSR-FG-004",
      "name": "PPM Activation for Level 2+ FRA",
      "conditions": [{ "field": "fra:assessmentLevel", "operator": "greaterThanOrEqual", "value": "Level2" }],
      "actions": [{ "include": ["PPM"] }]
    }
  ]
}
```

---

## 6. AGENT ARCHITECTURE

### 6.1 Inherited from PE-RMF (Layer 1 — Generic)

| Agent | PE-RMF Role | DICE Configuration |
|-------|-------------|-------------------|
| **Intake-Agent** | Scope validation, complexity classification | Classifies by flood source count + assessment level |
| **Data-Gathering-Agent** | Data source identification, survey commission | Triggers EA API, OS MasterMap, BGS, UKCP18 calls |
| **Compliance-Agent** | Regulatory checks against GRC-FW instances | Checks NPPF Sequential/Exception Tests, BS 85500, CIRIA C753 |
| **Report-Agent** | Structured report generation | Generates FRA document sections with evidence |
| **Project-Manager-Agent** | PPM lifecycle (Level 2+) | Manages survey → modelling → assessment → SuDS → report phases |
| **Regulatory-Watch-Agent** | Background regulatory monitoring | Watches EA, MHCLG, BSI for flood-related guidance changes |
| **Archive-Agent** | Canonical snapshot + re-assessment scheduling | Archives with flood zone + climate epoch state for reproducibility |

### 6.2 Domain Agents (Layer 3 — DICE Specific)

| Agent | PE-RMF Slot | `pe:AIAgent` Role | Governs | Owns Process |
|-------|-------------|-------------------|---------|--------------|
| **FRA-Assessment-Agent** | Hazard-Assessment | `agentActsAs: fra:Assessor` | `grc:NPPF-Framework` | `fra:HazardAssessmentProcess` |
| **SuDS-Mitigation-Agent** | Mitigation | `agentActsAs: suds:DesignAdvisor` | `grc:CIRIA-C753` | `suds:TreatmentTrainDesign` |
| **Climate-Projection-Agent** | Scenario | `agentActsAs: cp:ClimateAnalyst` | `grc:EA-ClimateGuidance` | `cp:AllowanceCalculation` |
| **FRA-Narrative-Agent** | Narrative | `agentActsAs: fra:ReportAuthor` | `grc:FRA-ReportStandard` | `fra:NarrativeGeneration` |

### 6.3 Orchestration Flow (PE-RMF + DICE Domain Agents)

```
[Site Submission]
    │
    ├── Intake-Agent (PE-RMF L1)
    │   ├── Validate site boundary + flood source(s)
    │   ├── Classify: Level 1 / 2 / 3
    │   └── Level 2+? → Project-Manager-Agent (create project)
    │
    ├── Data-Gathering-Agent (PE-RMF L1)
    │   ├── EA Flood Map API → flood zone lookup
    │   ├── OS MasterMap → site boundary + topology
    │   ├── BGS GeoIndex → soil/geology/groundwater
    │   ├── UKCP18 → climate projections
    │   ├── Level 2+: Commission topographic survey (LSC → surveyor)
    │   └── Level 3: Commission hydraulic modelling (LSC → modeller)
    │
    ├── Climate-Projection-Agent (DICE L3 → Scenario slot)
    │   └── Calculate epoch allowances for model runs
    │
    ├── FRA-Assessment-Agent (DICE L3 → Hazard-Assessment slot)
    │   ├── Compose flood knowledge graph (EMC scope rules)
    │   ├── Identify hazards (depth × velocity × return period)
    │   └── Evaluate risk (hazard rating × vulnerability × consequence)
    │
    ├── Compliance-Agent (PE-RMF L1, parameterised by L2 GRC-FW instances)
    │   ├── NPPF Sequential Test (rank alternative sites)
    │   ├── NPPF Exception Test (wider sustainability benefits)
    │   ├── BS 85500 resilience checklist
    │   └── EA Standing Advice decision tree
    │
    ├── SuDS-Mitigation-Agent (DICE L3 → Mitigation slot)
    │   ├── Design SuDS treatment train (CIRIA C753)
    │   ├── Calculate attenuation volumes
    │   └── Recommend resilience measures (BS 85500)
    │
    ├── Report-Agent (PE-RMF L1)
    │   └── FRA-Narrative-Agent (DICE L3 → Narrative slot)
    │       ├── Generate FRA sections with flood-specific terminology
    │       └── Attach graph provenance + evidence citations
    │
    └── Archive-Agent (PE-RMF L1)
        ├── Canonical snapshot (flood zone state + climate epoch + regulations)
        ├── Schedule re-assessment (if climate epoch changes or regulation updates)
        └── Regulatory-Watch-Agent (background — continuous)
```

---

## 7. BSC OBJECTIVES

### Financial Perspective (F)

| ID | Objective | Target |
|----|-----------|--------|
| **OBJ-F1** | Achieve £2M ARR within 24 months | £2M ARR by Y2 |
| **OBJ-F2** | Reduce FRA production cost by 60% | £1.2k vs £3k baseline |
| **OBJ-F3** | Capture 5% UK FRA market share in 3 years | ~2,500 assessments/year |
| **OBJ-F4** | Recurring revenue from regulatory subscriptions | 40% of revenue |
| **OBJ-F5** | White-label to 10+ consultancy firms | 10 licensees by Y3 |

### Customer Perspective (C)

| ID | Objective | Target |
|----|-----------|--------|
| **OBJ-C1** | FRA turnaround: hours not weeks | < 4hrs (L1), < 2d (L2), < 5d (L3 with PPM) |
| **OBJ-C2** | Zero regulatory omissions | 100% NPPF checklist coverage |
| **OBJ-C3** | NPS > 60 from consultant users | NPS ≥ 60 |
| **OBJ-C4** | LPA first-submission acceptance ≥ 95% | ≥ 95% pass rate |
| **OBJ-C5** | Every recommendation graph-traceable | 100% provenance |
| **OBJ-C6** | All flood sources + combined | 5/5 covered |
| **OBJ-C7** | Level 3 projects on-time and on-budget | ≥ 90% on-time, ≤ 10% variance |

### Internal Process Perspective (P)

| ID | Objective | Target |
|----|-----------|--------|
| **OBJ-P1** | Regulatory ontology currency | ≤ 5 working days lag |
| **OBJ-P2** | AI concordance with expert FRAs | ≥ 92% |
| **OBJ-P3** | Graph composition latency | < 3s p95 |
| **OBJ-P4** | Sequential Test automation | ≥ 95% |
| **OBJ-P5** | SuDS alignment with CIRIA C753 | 100% |
| **OBJ-P6** | Snapshot reproducibility | 100% |
| **OBJ-P7** | PPM milestone adherence | Zero missed gates |
| **OBJ-P8** | Subcontractor utilisation tracking | 100% LSC engagements tracked |

### Learning & Growth Perspective (L)

| ID | Objective | Target |
|----|-----------|--------|
| **OBJ-L1** | EA decision tree ontology coverage | 100% decision nodes |
| **OBJ-L2** | Training corpus: 1,000+ approved FRAs | 1,000 examples |
| **OBJ-L3** | Domain SME team | 3 FTEs (hydrology + planning + graph AI) |
| **OBJ-L4** | Open standards contributions | 2+ ontology submissions to OGC/W3C |
| **OBJ-L5** | Monthly model retraining | Monthly cadence |
| **OBJ-L6** | Climate projection currency | ≤ 30 days of UKCP update |

---

## 8. METRICS (KPI-ONT Aligned)

### 8.1 PE-RMF L1 Metrics (Inherited — Universal)

| KPI ID | Metric | Formula |
|--------|--------|---------|
| KPI-RMF-01 | Assessment Turnaround Time | Median(submission → completion) by level |
| KPI-RMF-02 | Data Quality Score | completeness × currency × accuracy |
| KPI-RMF-03 | Project On-Time Rate | Level 2+ on-time / total Level 2+ |
| KPI-RMF-04 | Project Budget Variance | Abs(actual − planned) / planned |
| KPI-RMF-05 | Supplier Lead Time | Commission → delivery (LSC) |
| KPI-RMF-06 | Graph Composition Latency | p95 composition time |
| KPI-RMF-07 | Snapshot Reproducibility | Reproducible / total |
| KPI-RMF-08 | Re-Assessment Adherence | On-time re-assessments / scheduled |

### 8.2 VSOM L2 Metrics (Sector-Parameterised)

| KPI ID | Metric | Parameterised By |
|--------|--------|-----------------|
| KPI-RMF-10 | Regulatory Completeness Score | NPPF + BS 85500 + CIRIA C753 + EA Standing Advice |
| KPI-RMF-11 | Regulatory Currency Lag | EA/MHCLG/BSI publication dates |
| KPI-RMF-12 | Industry Benchmark Position | UK construction FRA market |
| KPI-RMF-13 | BSC Objective Achievement | FloodGraph BSC structure |
| KPI-RMF-14 | OKR Quarterly Progress | FloodGraph OKRs |

### 8.3 DICE L3 Metrics (Flood Domain Specific)

| KPI ID | Metric | Formula | Frequency |
|--------|--------|---------|-----------|
| KPI-FG-01 | AI Concordance Rate | AI ∩ expert / expert | Monthly |
| KPI-FG-02 | LPA First-Time Acceptance | Accepted / submitted | Monthly |
| KPI-FG-03 | Flood Source Coverage | Supported / 5 | Release |
| KPI-FG-04 | SuDS Design Accuracy | CIRIA-compliant / total recommendations | Monthly |
| KPI-FG-05 | Climate Allowance Currency | Days(UKCP update → model updated) | Per release |
| KPI-FG-06 | Sequential Test Automation Rate | Auto-completed / total | Monthly |
| KPI-FG-07 | Customer NPS | Standard NPS survey | Quarterly |
| KPI-FG-08 | ARR | Sum(subscriptions + licences) | Monthly |
| KPI-FG-09 | Cost per Assessment | Platform cost / assessments | Monthly |
| KPI-FG-10 | Market Share | FloodGraph FRAs / total UK FRAs | Quarterly |

---

## 9. COMPETITIVE POSITIONING

| Competitor | Approach | FloodGraph Differentiator |
|------------|----------|--------------------------|
| Traditional consultancy (WSP, JBA) | Manual expert, 4-8 weeks | AI-augmented, hours not weeks, graph-auditable |
| Flood Re / insurers | Actuarial models, no FRA output | Full NPPF-compliant FRA document generation |
| JFlow / Tuflow | Hydraulic modelling software | Modelling is one input; FloodGraph reasons across regulatory + construction + mitigation |
| CheckMyFloodRisk.co.uk | Basic flood zone lookup | Lookup is step 1; FloodGraph delivers complete assessment |
| LPA in-house tools | Spreadsheet checklists | Graph-native, always current, cross-site comparison |
| Generic PM tools (MS Project) | Project tracking without domain context | PPM integrated with FRA ontology — gates tied to compliance |

---

## 10. DELIVERY ROADMAP

| Phase | Scope | PE-RMF Layer | Timeline |
|-------|-------|--------------|----------|
| **Phase 1 — Foundation** | PE-RMF process engine, core flood ontologies, EA/OS data integration, Level 1 Fluvial FRA | L1 + L3 core | M 1–6 |
| **Phase 2 — AI + Compliance** | Domain agents, NPPF Sequential/Exception Test automation, GRC-FW regulatory instances, FRA report generation | L2 + L3 agents | M 4–9 |
| **Phase 3 — Full Coverage** | All 5 flood sources, SuDS engine, Climate Projection agent, multi-stakeholder portal, PPM for Level 2/3 | L1 PPM + L3 full | M 7–14 |
| **Phase 4 — Scale** | White-label, LPA integration, BIM/IFC import, Regulatory-Watch-Agent, open ontology contributions | L1 + L3 extensions | M 12–20 |
| **Phase 5 — Market** | 100-client target, portfolio analytics for insurers, national SFRA aggregation, PPM benchmarking | L2 VSOM-SA analytics | M 18–30 |

---

## 11. PFI INSTANCE SUMMARY

```
PFI-PC-DICE (Paul Cowen — FloodGraph AI)
│
├── Foundation Pattern: PE-RMF v1.0.0
│
├── Layer 1 — PE-RMF (inherited):
│   └── PE, PPM (conditional), OFM, LSC, ORG, ORG-CONTEXT, GRC-FW, EMC
│
├── Layer 2 — Strategic Refinement (UK Construction):
│   ├── VE-Series: VSOM, BSC, OKR, KPI
│   ├── VSOM-SA: INDUSTRY, REASON, MACRO, PORTFOLIO
│   ├── VSOM-SC: NARRATIVE, CASCADE
│   ├── ORG-CONTEXT (regional/sector)
│   └── GRC-FW instances: NPPF, BS 85500, CIRIA C753, EA Standing Advice
│
├── Layer 3 — Flood Domain (DICE-specific):
│   ├── VP, RRR
│   ├── FLOOD-HAZARD (fh:), FLOOD-ZONE (fz:), HYDRO (hydro:)
│   ├── SITE-GEO (sg:), SUDS (suds:), FRA (fra:)
│   ├── CONSTRUCTION-RESILIENCE (cr:), CLIMATE-PROJECTION (cp:)
│   └── 4 domain agents → PE-RMF agent slots
│
├── PPM Activation:
│   ├── Level 1 — No PPM (automated, minutes-to-hours)
│   ├── Level 2 — Lightweight PPM (task tracking, budget)
│   └── Level 3 — Full PPM (phases, milestones, gates, LSC suppliers)
│
├── Agents: 7 PE-RMF (inherited) + 4 domain (DICE-specific) = 11 total
├── Stakeholder Roles: 4 (Developer, Consultant, LPA, Insurer)
├── Requirement Scopes: COMPLIANCE, PRODUCT, OPERATIONAL, ANALYTICS, SECURITY
├── Graph Scope Rules: 4+ (per flood source + combined + PPM activation)
├── Repo Triad: DICE-Dev / DICE-test / DICE-prod
└── Target: £2M ARR, 2,500 assessments/year, 10 white-label licensees
```

---

*PFI-PC-DICE VSOM v1.0.0 — Built on PE-RMF v1.0.0 generic risk management framework.*
*Ref: PE-RMF (PBS/STRATEGY/PE-RMF-Generic-Risk-Assessment-Framework.md), Epic 34 (#518).*
