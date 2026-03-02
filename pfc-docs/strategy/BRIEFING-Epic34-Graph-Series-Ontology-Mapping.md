# Epic 34 Companion: Graph Series to Ontology Library Mapping

## PFC Graph Series -- Current Asset Inventory & Gap Analysis

| Field | Value |
|---|---|
| **Parent Epic** | 34: PF-Core Graph-Based Agentic Platform Strategy |
| **Date** | 2026-02-18 |
| **Registry Version** | ont-registry-index.json v9.0.0 (OAA v6.1.0) |
| **Total Ontologies** | 42 entries (37 compliant, 1 superseded, 2 placeholders, 2 deprecated) |

---

## 1. Graph Series to Ontology Mapping

### PFC-G-Orchestration -- Config, Mapping, Adaptive Customisation

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| EMC-ONT | v2.0.0 | v6.1.0 | Compliant | `Orchestration/EMC-ONT/` |

**Coverage:** 1 ontology. EMC handles enterprise model composition, event-driven workflows, service integration.
**Gap:** No dedicated graph config engine ontology. EMC covers orchestration patterns but the PFC-G-Orchestration series needs a **GRAPH-CONFIG-ONT** to define graph configuration, multi-tenant mapping, and adaptive customisation rules.

---

### PFC-G-Foundation -- Org Context, Customers, Partners, Competition

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| ORG-ONT | v3.0.0 | v6.1.0 | Compliant | `Foundation/ORG-ONT/` |
| ORG-CONTEXT-ONT | v3.0.0 | v6.1.0 | Compliant | `Foundation/ORG-CONTEXT-ONT/` |
| CTX-ONT | v1.0.0 | v6.1.0 | Compliant | `Foundation/ORG-CONTEXT-ONT/` |
| ORG-MAT-ONT | v2.0.0 | v6.1.0 | Compliant | `Foundation/ORG-MAT-ONT/` |
| GA-ONT | -- | v6.1.0 | Compliant | `Foundation/GA-ONT/` |

**Coverage:** 5 ontologies. Strong foundation covering org identity, context taxonomy, maturity, gap analysis. Competitive analysis absorbed into ORG-CONTEXT from deprecated CA-ONT/CL-ONT.
**Gap:** No explicit **CUSTOMER-ONT** or **PARTNER-ONT** -- customer/partner entities may be modelled via ORG-CONTEXT but may need dedicated ontologies for client graph (PFI-Client-Foundation) patterns. The `pf-ins-partners/` directory has draft partner ontology data.
**Planned:** ORG-MAT merges into ORG-CONTEXT (maturity dimensions become types/subtypes tree).

---

### PFC-G-VE -- Value Engineering (VSOM, OKR, KPI, VP, PMF)

**Core VE (6 ontologies):**

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| VSOM-ONT | v3.0.0 | v6.1.0 | Compliant | `VE-Series/VSOM-ONT/` |
| OKR-ONT | v2.1.0 | v6.1.0 | Compliant | `VE-Series/OKR-ONT/` |
| VP-ONT | v1.2.3 | v6.1.0 | Compliant | `VE-Series/VP-ONT/` |
| RRR-ONT | -- | v6.1.0 | Compliant | `VE-Series/RRR-ONT/` |
| PMF-ONT | v2.0.0 | v6.1.0 | Compliant | `VE-Series/PMF-ONT/` |
| KPI-ONT | v1.0.0 | v6.2.0 | Compliant | `VE-Series/KPI-ONT/` |

**VSOM-SA Strategy Analysis sub-series (5 ontologies):**

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| BSC-ONT | v1.0.0 | v6.1.0 | Compliant | `VE-Series/VSOM-SA/BSC-ONT/` |
| INDUSTRY-ONT | v1.0.0 | v6.1.0 | Compliant | `VE-Series/VSOM-SA/INDUSTRY-ONT/` |
| REASON-ONT | v1.0.0 | v6.1.0 | Compliant | `VE-Series/VSOM-SA/REASON-ONT/` |
| MACRO-ONT | v1.0.0 | v6.1.0 | Compliant | `VE-Series/VSOM-SA/MACRO-ONT/` |
| PORTFOLIO-ONT | v1.0.0 | v6.1.0 | Compliant | `VE-Series/VSOM-SA/PORTFOLIO-ONT/` |

**VSOM-SC Strategy Communication sub-series (4 ontologies):**

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| NARRATIVE-ONT | v1.0.0 | v6.1.0 | Compliant | `VE-Series/VSOM-SC/NARRATIVE-ONT/` |
| CASCADE-ONT | v1.0.0 | v6.1.0 | Compliant | `VE-Series/VSOM-SC/CASCADE-ONT/` |
| CULTURE-ONT | v1.0.0 | v6.1.0 | Compliant | `VE-Series/VSOM-SC/CULTURE-ONT/` |
| VIZSTRAT-ONT | v1.0.0 | v6.1.0 | Compliant | `VE-Series/VSOM-SC/VIZSTRAT-ONT/` |

**Coverage:** 15 ontologies. **Strongest graph series.** Full VSOM cascade: strategy analysis (5 frameworks), strategy communication (4 channels), core value engineering (6 ontologies). VP-RRR alignment convention in place.
**Gap:** None critical. VSOM-to-OKR-to-KPI cascade is the backbone of S2 (Value-Engineering Everything).

---

### PFC-G-PE -- Process Engineering

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| PPM-ONT | v4.0.0 | v6.1.0 | Compliant | `PE-Series/PPM-ONT/` |
| PE-ONT | v3.0.0 | v6.1.0 | Compliant | `PE-Series/PE-ONT/` |
| EFS-ONT | -- | v6.1.0 | Compliant | `PE-Series/EFS-ONT/` |
| DS-ONT | v1.1.0 | v6.1.0 | Compliant | `PE-Series/DS-ONT/` |

**Coverage:** 4 ontologies (excluding EA which maps to PFC-EA-G). Portfolio/programme/project management, process engineering, enterprise framework services, design system.
**Gap:** No dedicated **AGENT-ONT** for agent process definitions. Agent Template v6.0.0 exists as a pattern but not as a registered ontology. The Agent Manager (S3) will need either a new ontology or an extension to PE-ONT.
**PFI Integration:** `EFS-ONT/PFI-Instance-Integration-Architecture-v1.1.0.md` defines the value chain from VSOM -> VP -> EFS -> Build.

---

### PFC-G-GRC -- Governance, Risk, Compliance

**GRC-01-GOV (Hub):**

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| GRC-FW-ONT | v3.0.0 | v6.1.0 | Compliant | `RCSG-Series/GRC-01-GOV/GRC-FW-ONT/` |

**GRC-02-RISK:**

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| ERM-ONT | v1.0.0 | v6.1.0 | Compliant | `RCSG-Series/GRC-02-RISK/ERM-ONT/` |
| RMF-IS27005-ONT | v1.0.0 | v6.1.0 | Compliant | `RCSG-Series/GRC-02-RISK/RMF-IS27005-ONT/` |

**GRC-03-COMP (Compliance):**

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| GDPR-ONT | v1.0.0 | v6.1.0 | Compliant | `RCSG-Series/GRC-03-COMP/GDPR-ONT/` |
| NCSC-CAF-ONT | v1.0.0 | v6.1.0 | Compliant | `RCSG-Series/GRC-03-COMP/NCSC-CAF-ONT/` |
| DSPT-ONT | v1.0.0 | v6.1.0 | Compliant | `RCSG-Series/GRC-03-COMP/DSPT-ONT/` |

**GRC-04-SEC (Security):**

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| MCSB-ONT | v2.0.0 | v6.1.0 | Compliant | `RCSG-Series/GRC-04-SEC/MCSB-ONT/` |
| MCSB2-ONT | -- | v6.1.0 | Placeholder | `RCSG-Series/GRC-04-SEC/MCSB2-ONT/` |
| PII-ONT | v3.3.0 | v6.1.0 | Compliant | `RCSG-Series/GRC-04-SEC/PII-ONT/` |
| AZALZ-ONT | -- | v6.1.0 | Placeholder | `RCSG-Series/GRC-04-SEC/AZALZ-ONT/` |

**GRC-05-RES (Resource Economics):** Planned, empty.
**GRC-06-AI (AI Governance):** Planned, empty.

**Coverage:** 10 ontologies (8 compliant, 2 placeholders) across 4 active domains. Hub-spoke architecture with GRC-FW as governance superordinate. COBIT 2019 / ISO 38500 / NIST CSF 2.0 aligned.
**Gap:** GRC-05-RES and GRC-06-AI domains are empty. MCSB2-ONT and AZALZ-ONT are placeholders. For PF-Core platform delivery, the GRC series supports multi-jurisdictional compliance (OBJ-SH1) but needs the AI governance domain for agent compliance.

---

### PFC-EA-G -- Enterprise Architecture

| Ontology | Version | OAA | Status | Path |
|---|---|---|---|---|
| EA-ONT | -- | v6.1.0 | Compliant | `PE-Series/EA-ONT/` |
| EA-CORE-ONT | v1.0.0 | v6.1.0 | Compliant | `PE-Series/EA-CORE-ONT/` |
| EA-TOGAF-ONT | v1.0.0 | v6.1.0 | Compliant | `PE-Series/EA-TOGAF-ONT/` |
| EA-MSFT-ONT | v1.0.0 | v6.1.0 | Compliant | `PE-Series/EA-MSFT-ONT/` |

**Coverage:** 4 ontologies. TOGAF ADM methodology, Microsoft Cloud Platform, core EA concepts, and EA portfolio. These live under PE-Series/ but logically form a distinct graph series.
**Gap:** No AWS-ONT or multi-cloud abstraction. EA-CORE provides the platform-agnostic layer but cloud providers beyond Microsoft aren't covered yet.
**Related Epic:** Epic 17 (#141) -- "Engineering EA into PF Core" and Epic 33 (#505) -- "Azure Graph & Ontology-Driven Landing Zone".

---

### PFC-World-View -- External Intelligence, Scenarios

| Ontology | Existing Asset | Coverage |
|---|---|---|
| MACRO-ONT | PESTEL, Scenarios, Futures Funnel, Backcasting | Macro-level external analysis |
| INDUSTRY-ONT | Porter's 5F, SWOT/TOWS, Ansoff Growth | Industry-level external analysis |
| _sketches layers | 5 strategic pattern layers (JSONLD) | Macro -> Industry -> Org -> Analytical -> Portfolio |

**Coverage:** Partial -- MACRO-ONT and INDUSTRY-ONT provide analytical frameworks. The `_sketches/` strategic pattern layers define a 5-layer external analysis architecture.
**Gap:** No dedicated **SCENARIO-ONT** or **WORLD-VIEW-ONT** for ongoing external intelligence monitoring. The frameworks exist for point-in-time analysis but not continuous sensing. This series needs a new ontology to model probable/plausible/outlier scenarios as graph patterns.

---

### PFC/PFI-G-Market -- Dynamic Market/Industry/Sector Intelligence

| Ontology | Existing Asset | Coverage |
|---|---|---|
| INDUSTRY-ONT | Porter's 5F, SWOT/TOWS, Ansoff | Industry analysis frameworks |
| PORTFOLIO-ONT | BCG, Three Horizons, Investment Maps | Portfolio strategy |
| ORG-CONTEXT-ONT | Competitor analysis (absorbed from CA/CL) | Competitive landscape |
| BAIV AIV-Competitive-ONT | AI visibility competitive analysis | BAIV-specific competitive intel |

**Coverage:** Partial -- analytical frameworks and competitive analysis exist. BAIV has instance-level competitive ontology.
**Gap:** No **MARKET-ONT** for dynamic market sensing, sector tracking, or real-time market intelligence. Currently static analysis frameworks, not continuous market graphs.

---

## 2. PFI Instance Asset Inventory

### PFI-BAIV (AI Visibility) -- Most Mature

| Asset | Location | Type |
|---|---|---|
| VP instance | `pfi-BAIV-AIV-ONT/baiv-ai-visibility-vp-instance-v1.0.0.jsonld` | Value Proposition |
| VP+RRR enhanced | `pfi-BAIV-AIV-ONT/baiv-ai-visibility-vp-rrr-enhanced-v1.1.0.jsonld` | VP-RRR aligned |
| RRR roles | `pfi-BAIV-AIV-ONT/RRR-DATA-BAIV-AIV-roles-v1.0.0.jsonld` | 11 RBAC roles |
| EFS security | `pfi-BAIV-AIV-ONT/EFS-DATA-BAIV-AIV-security-v1.0.0.jsonld` | Security layer |
| C-Suite RRR | `pfi-BAIV-AIV-ONT/baiv-csuite-rrr-instances-v1.0.0.jsonld` | Executive roles |
| Security roles | `pfi-BAIV-AIV-ONT/baiv-architecture-security-roles-v1.0.0.jsonld` | Arch security |
| VSOM (OAA) | `pfi-BAIV-AIV-ONT/BAIV_VSOM_OAA_COMPLIANT (2).json` | OAA-compliant VSOM |
| Competitive ONT | `pfi-BAIV-AIV-ONT/AIV-Competitive-ONT/` | 6 files (ontology, registry, test, docs) |
| Security arch | `pfi-BAIV-AIV-ONT/PFI-BAIV-RRR-Security-Architecture-v1.0.0.md` | Requirements doc |
| RBAC impl plan | `RRR-ONT/RRR-PFC-PFI-RBAC-BAIV-ONT/` | Implementation plan + PDFs |

**Repos:** `pfi-baiv-aiv-{dev|test|prod}` (Projects #39/#40/#41)

### PFI-W4M-WWG (World Wide Growth)

| Asset | Location | Type |
|---|---|---|
| pfi-config.json | `scripts/pfi-w4m-wwg-{dev|test|prod}/instance-data/config/` | Config (3 envs) |
| Setup guide | `ARCHITECTURE/PFI-W4M-WWG-Setup-Guide.md` | 13.7 KB |
| Build summary | `ARCHITECTURE/PFI-W4M-WWG-Build-Summary.md` | 3.0 KB |
| VSOM visual guide | `VSOM-ONT/w4m_vsom_visual_guide_v1.0.md` | Instance VSOM |

**Repos:** `pfi-w4m-wwg-{dev|test|prod}` (Projects #48/#49/#50)
**Gap:** VP+RRR instance data not yet seeded.

### PFI-W4M-EOMS (Enterprise Operations)

| Asset | Location | Type |
|---|---|---|
| pfi-config.json | `scripts/pfi-w4m-eoms-{dev|test|prod}/instance-data/config/` | Config (3 envs) |
| Setup guide | `ARCHITECTURE/PFI-W4M-EOMS-Setup-Guide.md` | 14.0 KB |
| Build summary | `ARCHITECTURE/PFI-W4M-EOMS-Build-Summary.md` | 3.0 KB |

**Repos:** `pfi-w4m-eoms-{dev|test|prod}` (Projects #51/#52/#53)
**Gap:** VP+RRR instance data not yet seeded.

### PFI-AIRL-CAF-AZA (AI Readiness -- Cyber Assessment)

| Asset | Location | Type |
|---|---|---|
| VP instance | Seeded in dev repo | `vp-airl-caf-instance-v1.0.0.jsonld` |
| RRR instance | Seeded in dev repo | `rrr-airl-caf-instance-v1.0.0.jsonld` |
| Setup guide | `ARCHITECTURE/PFI-AIRL-CAF-AZA-Setup-Guide.md` | 18.3 KB |
| Build summary | `ARCHITECTURE/PFI-AIRL-CAF-AZA-Build-Summary.md` | 5.1 KB |
| VP in library | `VP-ONT/instance-data/vp-airl-instance-v1.0.0.jsonld` | Canonical source |

**Repos:** `pfi-airl-caf-aza-{dev|test|prod}` (Projects #42/#43/#44)

### PFI-VHF (Virtual Health Foods -- PoC)

| Asset | Location | Type |
|---|---|---|
| Setup guide | `ARCHITECTURE/PFI-VHF-Setup-Guide.md` | 18.9 KB |

**Repos:** `pfi-vhf-nutrition-app-{dev|test|prod}` (Projects #36/#37/#38)

---

## 3. Gap Summary -- What's Needed for Platform

### New Ontologies Required

| Ontology | Graph Series | Purpose | Priority |
|---|---|---|---|
| GRAPH-CONFIG-ONT | PFC-G-Orchestration | Graph config engine, multi-tenant mapping, adaptive customisation | High |
| AGENT-ONT | PFC-G-PE | Agent definitions, clusters, templates, orchestration patterns | High |
| MARKET-ONT | PFC-G-Market | Dynamic market/sector intelligence, continuous sensing | Medium |
| SCENARIO-ONT | PFC-World-View | Probable/plausible/outlier scenarios as graph patterns | Medium |
| CUSTOMER-ONT | PFC-G-Foundation | Client entities, segments, relationships (may extend ORG-CONTEXT) | Medium |
| PARTNER-ONT | PFC-G-Foundation | Fair Slice partner definitions, contracts, revenue sharing | Medium |
| AI-GOV-ONT | PFC-G-GRC (GRC-06-AI) | AI agent compliance, ethical guardrails, governance | Low (Phase 2) |

### Infrastructure Required (Not Ontology)

| Capability | Graph Series | Description |
|---|---|---|
| JSONB Graph Storage | All | Supabase PostgreSQL implementation of graph patterns |
| Agent Manager | PFC-G-PE | Claude SDK integration, LLM Router, three-tier cost |
| Multi-Tenant Isolation | PFC-G-Orchestration | Client graph separation in Supabase |
| Figma Make Pipeline | PFC-G-PE (DS-ONT) | Design-to-code with Next.js + shadcn/ui |
| API Gateway + MCP | PFC-EA-G | Integration layer for enterprise connectivity |

### Existing Gaps to Close

| Action | Asset | Details |
|---|---|---|
| Seed VP+RRR data | W4M-WWG, W4M-EOMS | Instance data not yet created |
| Set PROMOTION_PAT | All 15 PFI repos | GitHub secret for promotion workflow |
| Set Supabase secrets | BAIV dev | SUPABASE_URL, ANON_KEY, SERVICE_KEY |
| Complete MCSB2-ONT | GRC-04-SEC | Currently placeholder |
| Complete AZALZ-ONT | GRC-04-SEC | Currently placeholder |
| Populate GRC-05-RES | GRC-05-RES | Resource economics domain empty |
| Populate GRC-06-AI | GRC-06-AI | AI governance domain empty |

---

## 4. Coverage Heat Map

```
Graph Series          Ontologies  Status      Coverage
--------------------  ----------  ----------  ---------
PFC-G-VE              15          Strong      ██████████ 100%
PFC-G-GRC             10          Good        ████████░░  80%
PFC-G-Foundation       5          Good        ████████░░  80%
PFC-EA-G               4          Good        ████████░░  80%
PFC-G-PE               4          Moderate    ██████░░░░  60%
PFC-G-Orchestration    1          Weak        ███░░░░░░░  30%
PFC-World-View         2*         Partial     ██░░░░░░░░  20%
PFC-G-Market           3*         Partial     ██░░░░░░░░  20%

* Shared ontologies from VE-Series, not dedicated
```

**Strongest:** VE-Series (15 ontologies, full cascade, all compliant)
**Weakest:** PFC-World-View and PFC-G-Market (no dedicated ontologies, borrowing from VE analysis frameworks)
**Critical gap:** PFC-G-Orchestration has only EMC-ONT -- needs graph config engine for multi-tenant platform

---

*Epic 34 Companion -- Graph Series to Ontology Library Mapping*
*Status: DRAFT -- Ready for feature planning*
