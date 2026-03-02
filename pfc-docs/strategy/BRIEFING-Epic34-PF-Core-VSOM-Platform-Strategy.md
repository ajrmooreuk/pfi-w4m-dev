# Epic 34: PF-Core Graph-Based Agentic Platform Strategy

## Analysis Briefing & Planning Document

| Field | Value |
|---|---|
| **Epic** | 34 |
| **Date** | 2026-02-18 |
| **Status** | DRAFT -- Briefing for Analysis & Planning |
| **Classification** | CONFIDENTIAL -- Strategic Planning Asset |
| **Ontology Compliance** | Schema.org Grounded, OAA Registry v3.0, BSC Framework |

---

## Executive Summary

Epic 34 defines the **PF-Core Graph-Based Agentic Platform** -- the strategic programme that transforms the existing ontology library, visualiser, and PFI architecture into a production platform for mid-market enterprises. The VSOM framework (Vision, Strategy, Objectives, Metrics) governs every layer.

**The core thesis:** Replace 5-15 disconnected SaaS tools with one adaptive, graph-powered platform where every business concept is a node, every relationship is traversable, and every agent operates with full strategic context.

**Three-tier architecture:**
- **PF-Core (PFC)** -- shared, transferable foundation (this repo's ontology library is the seed)
- **PF-Instances (PFI)** -- domain-specific platforms: BAIV (AI Visibility), W4M (Value Engineering), AIR (AI Readiness)
- **Client Graphs (PFI-CLIENT)** -- fully customised per-client deployments inheriting validated ontologies

---

## 1. Current State Assessment

### 1.1 What We Have (Assets in Azlan-EA-AAA)

| Asset | Location | Status |
|---|---|---|
| Ontology Library (41 ontologies) | `ontology-library/` | 36 compliant, operational |
| VSOM-ONT v3.0.0 | `VE-Series/VSOM-ONT/` | OAA v6 compliant |
| VSOM-SA sub-series (5 ontologies) | `VE-Series/VSOM-SA/` | BSC, INDUSTRY, REASON, MACRO, PORTFOLIO |
| VSOM-SC sub-series (4 ontologies) | `VE-Series/VSOM-SC/` | NARRATIVE, CASCADE, CULTURE, VIZSTRAT |
| GRC-FW-ONT v3.0.0 | `RCSG-Series/GRC-01-GOV/` | Hub ontology, F30.1 complete |
| ERM-ONT v1.0.0 | `RCSG-Series/GRC-02-RISK/` | ISO 31000, F30.2 just landed |
| Ontology Visualiser v4.5.0 | `PBS/TOOLS/ontology-visualiser/` | 938/939 tests pass |
| PFI Architecture Guides | `PBS/ARCHITECTURE/PFI-*.md` | BAIV, W4M-WWG, W4M-EOMS, AIRL-CAF-AZA |
| PFI Instance Ontologies | `PBS/ONTOLOGIES/pfi-*/` | BAIV (VP, RRR, RBAC, competitive) |
| PF-Core Summary | `VSOM-SC/09-PF-Core-Summary.md` | Series architecture reference |

### 1.2 What's Missing (Gap to Platform)

| Gap | Description | Blocks |
|---|---|---|
| **PFC-G-Orchestration** | Graph config engine, EMC PFI-Org-Product-Service mapping | Platform instance provisioning |
| **JSONB Graph Storage Layer** | PostgreSQL/Supabase JSONB implementation of graph patterns | All runtime capability |
| **Agent Manager** | Multi-agent orchestration with Claude SDK, LLM Router | Intelligence layer |
| **Three-Tier Cost Engine** | Local -> Haiku -> Sonnet cost optimisation pipeline | Sustainable agent economics |
| **Multi-Tenant Graph Isolation** | Client graph separation with cross-tier intelligence | Client deployment |
| **Figma Make Pipeline** | Design-to-code with Next.js + shadcn/ui | User experience layer |
| **API Gateway + MCP** | Integration and interoperability layer | Enterprise connectivity |
| **Digital Contract Management** | Fair Slice revenue/equity sharing governance | Partner ecosystem |

---

## 2. VSOM Framework -- PF-Core Focus

### 2.1 Vision

> **Become the definitive graph-native agentic platform that transforms how mid-market enterprises architect, deploy, and continuously optimise AI-driven strategy, processes, and competitive intelligence -- replacing brittle SaaS stacks with a single adaptive, ontology-powered ecosystem.**

**Three operating levels:**

```
PF-Core (PFC)        -- Shared, transferable foundation
  |-- PF-Instances   -- BAIV (AI Visibility), W4M (Value Engineering), AIR (AI Readiness)
       |-- Client Graphs -- Fully customised per-client org
```

**80/20 principle:** Launch enterprise-grade graph capabilities in days not years. JSONB delivers 80% of graph database benefits at zero additional infrastructure cost.

### 2.2 Six Strategies

| # | Strategy | Priority | Focus |
|---|---|---|---|
| **S1** | Graph-First Architecture | 1 -- Foundation | JSONB graph in Supabase PostgreSQL, Neo4j-ready patterns, all entities as nodes |
| **S2** | Value-Engineering-Driven Everything | 2 -- Governance | Every feature traces to measurable value via VSOM -> OKR -> KPI cascades |
| **S3** | Agentic Orchestration | 3 -- Intelligence | Multi-agent architecture, Agent Template v6.0.0, three-tier cost engine |
| **S4** | Platform Instance & Client Customisation | 4 -- Scalability | PFC -> PFI -> Client graph hierarchy, multi-tenant isolation |
| **S5** | UI/UX Design-to-Production | 5 -- Experience | Figma Make -> Next.js + shadcn/ui, design ontology as graph node |
| **S6** | Integration & Enterprise Architecture | 6 -- Ecosystem | API-first, MCP-native, platform-agnostic EA |

### 2.3 Graph Series Architecture

The platform decomposes into modular graph series, each mapping to existing ontology library series:

| Graph Series | Maps To | Scope |
|---|---|---|
| **PFC-G-Orchestration** | `Orchestration/EMC-ONT` | Config, mapping, adaptive customisation |
| **PFC-G-Foundation** | `Foundation/{ORG,ORG-CONTEXT,CTX,GA}-ONT` | Org context, customers, partners |
| **PFC-G-VE** | `VE-Series/*` | VSOM, OKR, KPI, VP, PMF -- strategy as code |
| **PFC-G-PE** | `PE-Series/*` | Process engineering, PPM, EFS, EA |
| **PFC-G-GRC** | `RCSG-Series/*` | GRC-FW hub, ERM, compliance frameworks |
| **PFC-EA-G** | `PE-Series/{EA-CORE,EA-TOGAF,EA-MSFT,DS}-ONT` | Enterprise Architecture layer |
| **PFC-World-View** | *(new)* | External intelligence: scenarios engine |
| **PFC/PFI-G-Market** | *(new)* | Dynamic market/industry/sector intelligence |

### 2.4 PFI Instance Alignment

Each PFI inherits the PFC graph and extends with domain-specific layers:

**PFI-BAIV (AI Visibility Marketing)**
- Aligns to: S2, S3, S4, S5, S6
- Domain: AI mention rates, citation architecture, content authority
- Existing assets: `pfi-BAIV-AIV-ONT/` (VP, RRR, RBAC, competitive)
- Key metric: AI Visibility Score (Discovery 25%, Authority 20%, Trust 30%, Technical 15%, Impact 10%)
- 16 agents across 4 clusters: Discovery, Analysis, Generation, Optimisation

**PFI-W4M (Value Engineering & MVP Acceleration)**
- Aligns to: S1, S2, S3, S4
- Domain: Idea-to-MVP velocity, PMF validation, strategy execution
- Existing assets: `pfi-w4m-pe/`, `PFI-W4M-WWG-*`, `PFI-W4M-EOMS-*`
- Key metric: PMF Score, Time-to-MVP, Client Success Rate

**PFI-AIR (AI Readiness & Strategy)**
- Aligns to: S1, S2, S3, S6
- Domain: AI maturity, capability development, governance
- Existing assets: `PFI-AIRL-CAF-AZA-*` guides
- Key metric: AI Readiness Score, Capability Maturity Level

---

## 3. Objectives -- Balanced Scorecard (5 Perspectives)

### 3.1 Financial

| ID | Objective | Target | Priority |
|---|---|---|---|
| OBJ-F1 | 100 paying BAIV clients | Q4 2026 | Critical |
| OBJ-F2 | GBP 500K ARR across instances | Q2 2027 | High |
| OBJ-F3 | Operating costs below 30% of revenue | Ongoing | High |
| OBJ-F4 | GBP 100K+ annual partner-originated revenue | Q4 2027 | Medium |

### 3.2 Customer

| ID | Objective | Target | Priority |
|---|---|---|---|
| OBJ-C1 | 40-60% AI visibility improvement for 90% of BAIV clients in 60 days | Ongoing | Critical |
| OBJ-C2 | NPS >= 60 | Q3 2026 | High |
| OBJ-C3 | Client onboarding weeks -> days | Q2 2026 | High |
| OBJ-C4 | 70% client self-service graph customisation | Q4 2026 | Medium |

### 3.3 Internal Process

| ID | Objective | Target | Priority |
|---|---|---|---|
| OBJ-IP1 | 85%+ test coverage with ontological validation TDD | Ongoing | Critical |
| OBJ-IP2 | Agent dev cycle weeks -> days (Agent Template v6.0.0) | Q2 2026 | High |
| OBJ-IP3 | Consolidate to <= 24 tables with JSONB graph storage | Q1 2026 | High |
| OBJ-IP4 | Design-to-production: Figma Make -> Next.js -> deployed < 4 hours | Q3 2026 | Medium |
| OBJ-IP5 | 95%+ reduction in AI agent testing costs (three-tier) | Q1 2026 | High |

### 3.4 Learning & Growth

| ID | Objective | Target | Priority |
|---|---|---|---|
| OBJ-LG1 | OAA Registry v3.0 central governance with competency validation | Q2 2026 | Critical |
| OBJ-LG2 | Claude best agentic practices documented | Q1 2026 | High |
| OBJ-LG3 | 10+ industry vertical ontologies | Q4 2026 | Medium |
| OBJ-LG4 | Knowledge graph 85-95% ontological validation confidence | Ongoing | High |

### 3.5 Stakeholder

| ID | Objective | Target | Priority |
|---|---|---|---|
| OBJ-SH1 | Multi-jurisdictional compliance (UK, EU, USA) via GRC graph | Q3 2026 | High |
| OBJ-SH2 | 10+ Fair Slice partners with digital contract governance | Q4 2026 | Medium |
| OBJ-SH3 | 100% Schema.org ontology compliance validation | Ongoing | High |

---

## 4. Metrics Dashboard

### 4.1 Key Leading Indicators (predict outcomes)

| ID | KPI | Target | Predicts |
|---|---|---|---|
| M-F5 | Client Pipeline Value | GBP 2M | OBJ-F1 (100 clients) |
| M-F6 | MRR Growth Rate | >15% MoM | OBJ-F2 (ARR) |
| M-C5 | Client Engagement Score | >= 75 | OBJ-C2 (NPS) |
| M-C6 | AI Visibility Score | +25pts per client | OBJ-C1 (visibility) |
| M-IP6 | Ontology Validation Confidence | 85-95% | OBJ-IP1 (test coverage) |
| M-IP7 | Graph Query Performance | <200ms p95 | OBJ-IP3 (table consolidation) |

### 4.2 Key Lagging Indicators (confirm outcomes)

| ID | KPI | Target | Confirms |
|---|---|---|---|
| M-F1 | Paying BAIV Clients | 100 | OBJ-F1 |
| M-F2 | Platform ARR | GBP 500K | OBJ-F2 |
| M-C1 | AI Mention Rate Improvement | 40-60% | OBJ-C1 |
| M-C3 | Client Onboarding Time | <= 7 days | OBJ-C3 |
| M-IP2 | Agent Dev Cycle | <= 5 days | OBJ-IP2 |
| M-IP5 | AI Agent Test Cost per Run | < $2.50 | OBJ-IP5 |

### 4.3 Health Thresholds

| Status | Range | Action |
|---|---|---|
| Achieved | 100% of target | Celebrate, set stretch target |
| On Track | >= 75% | Continue current approach |
| At Risk | 50-74% | Investigate root cause, adjust |
| Behind | < 50% | Escalate, reallocate, reassess |

---

## 5. Cause-Effect Chains (BSC Cascades)

### Chain 1: Ontology Excellence -> Platform Quality -> Client Outcomes -> Revenue
```
[LG: OAA Registry & Ontology Library (OBJ-LG1, LG3)]
    -> [IP: 85%+ Test Coverage with Ontological Validation (OBJ-IP1)]
    -> [C: 40-60% AI Visibility Improvement in 60 Days (OBJ-C1)]
    -> [F: 100 Paying BAIV Clients (OBJ-F1)]
```

### Chain 2: Agentic Practices -> Fast Development -> Rapid Onboarding -> ARR
```
[LG: Claude Best Agentic Practices (OBJ-LG2)]
    -> [IP: Agent Dev Cycle Weeks -> Days (OBJ-IP2)]
    -> [C: Client Onboarding Weeks -> Days (OBJ-C3)]
    -> [F: GBP 500K ARR across Instances (OBJ-F2)]
```

### Chain 3: Knowledge Graph -> Cost Efficiency -> Self-Service -> Partner Revenue
```
[LG: Knowledge Graph 80%+ Completeness (OBJ-LG4)]
    -> [IP: 95%+ AI Test Cost Reduction (OBJ-IP5)]
    -> [C: 70% Self-Service Configuration (OBJ-C4)]
    -> [F: Fair Slice Partner Revenue GBP 100K+ (OBJ-F4)]
```

---

## 6. Platform Graph Hierarchy

```
PF-CORE GRAPH (Global)
|
|-- PFC-G-Orchestration ........... Config, mapping, EMC
|-- PFC-G-Foundation .............. Org, context, customers, partners
|-- PFC-G-VE ...................... VSOM, OKR, KPI, VP, PMF
|-- PFC-G-PE ...................... Process engineering
|-- PFC-G-GRC .................... Governance, risk, compliance
|-- PFC-EA-G ..................... Enterprise architecture
|-- PFC-World-View ............... External intelligence / scenarios
|-- PFC-G-Market ................. Market / industry / sector intel
|
|-- PFI-BAIV (AI Visibility)
|   |-- PFI-BAIV-Market/Industry
|   |-- PFI-BAIV-CLIENT-GRAPH
|   |   |-- Client Foundation (Org, Team, Brand)
|   |   |-- Client VE (Value Engineering)
|   |   |-- Client PE (Process Engineering)
|   |   |-- Client GEO (Jurisdiction, Data Residency)
|   |   +-- Client Intermediary (Agency, Partner, Plugin)
|   +-- Cross-Instance Intelligence
|
|-- PFI-W4M (Value Engineering)
|   +-- (same client graph pattern)
|
+-- PFI-AIR (AI Readiness)
    +-- (same client graph pattern)
```

**Key principle:** Intelligence flows both ways -- validated ontologies cascade down (PFC -> PFI -> Client), anonymised patterns aggregate up (Client -> PFI -> PFC). Compounding network effect.

---

## 7. Time Horizons

| Phase | Horizon | Focus |
|---|---|---|
| **Foundation** | 0-12 months | BAIV MVP -> 100 paying clients; PF-Core graph operational; Agent Manager live |
| **Expansion** | 12-36 months | W4M + AIR instances live; multi-tenant graph at 500+ orgs; cross-instance intelligence |
| **Dominance** | 3-5 years | Definitive graph-native agentic platform for mid-market; partner ecosystem; self-improving ontologies |

---

## 8. Traceability Matrix (Vision -> Strategy -> Objective -> Metric)

| Strategy | Objective | BSC Perspective | Leading Metric | Lagging Metric |
|---|---|---|---|---|
| S1: Graph-First | OBJ-IP3 (<= 24 tables) | Process | M-IP7 (query perf) | M-IP3 (table count) |
| S1: Graph-First | OBJ-LG1 (OAA Registry) | L&G | M-IP6 (ont confidence) | M-LG1 (ontologies) |
| S2: VE Everything | OBJ-F1 (100 clients) | Financial | M-F5 (pipeline) | M-F1 (paying clients) |
| S2: VE Everything | OBJ-C1 (AI visibility) | Customer | M-C6 (visibility score) | M-C1 (mention rate) |
| S3: Agentic Orch | OBJ-IP2 (agent dev) | Process | M-C5 (engagement) | M-IP2 (dev cycle) |
| S3: Agentic Orch | OBJ-IP5 (cost reduction) | Process | -- | M-IP5 (cost/run) |
| S4: Instance Custom | OBJ-F2 (GBP 500K ARR) | Financial | M-F6 (MRR growth) | M-F2 (platform ARR) |
| S4: Instance Custom | OBJ-C3 (fast onboard) | Customer | M-C5 (engagement) | M-C3 (onboard days) |
| S5: UI/UX Pipeline | OBJ-IP4 (design->deploy) | Process | M-IP7 (query perf) | M-IP4 (deploy hours) |
| S6: Integration EA | OBJ-SH1 (compliance) | Stakeholder | -- | M-SH1 (jurisdictions) |

---

## 9. VSEM Execution Bridge

| Strategy | Execution Capability | Owner | Leading | Lagging |
|---|---|---|---|---|
| S1 | JSONB Graph Engine + OAA Registry | Platform Architecture | Graph Query <200ms | <= 24 Tables |
| S2 | VSOM Module + OKR Cascade | Ontology Governance | Ont Confidence 85-95% | Schema.org Pass 100% |
| S3 | Agent Manager + Three-Tier Cost | Agent Engineering | Engagement >= 75 | Agent Dev <= 5 days |
| S4 | Multi-Tenant Graph + Digital Contracts | Platform Architecture | MRR Growth >15% | 100 Clients |
| S5 | Figma Make Pipeline | Frontend & Design | Query <200ms | Deploy <4hrs |
| S6 | API Gateway + GRC Engine | Platform Architecture | -- | 3 Jurisdictions |

---

## 10. Epic 34 Feature Candidates (for Planning)

Based on the VSOM analysis, the epic decomposes into features aligned to strategies:

| Feature | Title | Strategy | Description |
|---|---|---|---|
| F34.1 | PFC Graph Series Ontology Mapping | S1 | Map all 8 graph series to existing ontology library; identify gaps; define PFC-G-* schema |
| F34.2 | PFI Instance VSOM Alignment | S2 | Create instance-level VSOM docs for BAIV, W4M, AIR inheriting from PFC VSOM |
| F34.3 | Agent Architecture Blueprint | S3 | Define Agent Manager, agent clusters, Agent Template v6.0.0, three-tier cost model |
| F34.4 | Graph Hierarchy & Isolation Design | S4 | Three-tier graph model (PFC -> PFI -> Client), isolation patterns, cross-tier intelligence |
| F34.5 | JSONB Graph Storage PoC | S1 | Prove JSONB graph patterns in Supabase PostgreSQL for core entities/relationships |
| F34.6 | Metrics & Dashboard Specification | S2 | Define all M-* KPIs, health thresholds, BSC cascade tracking requirements |
| F34.7 | Integration & EA Patterns | S6 | API-first design, MCP server patterns, enterprise connector requirements |

---

## 11. Relationship to Existing Epics

| Epic | Title | Relationship to Epic 34 |
|---|---|---|
| 10A (#127) | Security MVP -- Multi-PFI Foundation | Foundation for S4 multi-tenant isolation |
| 17 (#141) | PF-Core-[W4M-EA] -- Engineering EA into PF Core | Direct predecessor -- EA into PFC-EA-G |
| 30 (#370) | GRC Series Architecture | Feeds PFC-G-GRC graph series |
| 31 (#394) | Multi-Instance Platform Delivery | CI/CD pipeline for S4 instance deployment |
| 32 (#479) | Cross-Repo Programme Visibility | Programme governance tooling |
| 33 (#505) | Azure Graph & Ontology-Driven Landing Zone | Infrastructure patterns for PFC-EA-G |

---

## 12. Competitive Moat

| Dimension | Position |
|---|---|
| **Strategy** | Differentiation through ontology-native intelligence |
| **Basis** | Proprietary graph architecture with Schema.org-grounded JSON-LD enabling semantic AI reasoning |
| **Target** | Mid-market (500-10,000 employees) and ambitious SMBs |
| **Value Prop** | Replace 5-15 SaaS tools with one adaptive graph platform; 10-50x advantage through semantic understanding |
| **Moat** | Proprietary domain ontologies, accumulated graph intelligence, agent orchestration IP, compounding network effects, Fair Slice ecosystem |

---

## Appendix: Glossary

| Term | Definition |
|---|---|
| PF-Core (PFC) | Platform Foundation Core -- shared infrastructure across all ventures |
| PFI | Platform Foundation Instance -- venture-specific (BAIV, W4M, AIR) |
| VSOM | Vision, Strategy, Objectives & Metrics |
| VSEM | Vision, Strategy, Execution, Metrics -- execution-focused |
| BSC | Balanced Scorecard -- five-perspective strategic measurement |
| OAA | Ontology Architect Agent -- quality/validation governance |
| Fair Slice | Revenue/equity sharing via digital contracts |
| Agent Template v6.0.0 | Standardised agent framework -- WHY-before-HOW |
| Three-Tier Cost | Local -> Haiku -> Sonnet (95-100% cost reduction) |
| AI Visibility Score | Composite: Discovery 25%, Authority 20%, Trust 30%, Technical 15%, Impact 10% |

---

*Epic 34 -- PF-Core Graph-Based Agentic Platform Strategy*
*Status: DRAFT -- Ready for analysis and feature planning*
