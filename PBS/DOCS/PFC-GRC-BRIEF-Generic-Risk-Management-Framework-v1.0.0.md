# PFC-GRC-BRIEF: Generic Risk Management Framework — Strategy & Discussion Paper

**Document**: PFC-GRC-BRIEF-Generic-Risk-Management-Framework-v1.0.0.md
**Version**: 1.0.0
**Date**: 2026-03-12
**Status**: DRAFT — Strategic Review
**Author**: PFC Platform Team (AI-assisted)
**Supersedes**: `PE-RMF-Generic-Risk-Assessment-Framework.md` (planned, never created)
**Scope**: PFC Platform + all PFI instances
**Feature**: [F30.13 (#1028)](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1028) — Generic Risk Management Framework — Strategy Evaluation & VE Skill Chain Integration
**Parent Epic**: [Epic 30 (#370)](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/370) — GRC Series Architecture

---

## Executive Summary

Risk management is not a compliance exercise — it is a strategic capability that determines whether an organisation can deliver and sustain value for its business, stakeholders, customers, teams, and investors. This paper presents a strategic review of how to manage risk across the PFC platform using a unified, ontology-driven framework that draws on ISO 31000, ISO 27005, COSO ERM, NIST RMF, and UK-specific governance standards (NCSC CAF, Cyber Governance Code of Practice). Critically, it uses the **VE Skill Chain** as the analytical lens — treating risk not as a standalone discipline but as a value-linked, strategy-aligned, measurement-driven capability that integrates with every layer of organisational delivery.

The framework is deliberately **domain-agnostic at Layer 1** — applicable to information security, AI, digital, financial, operational, reputational, legal, and strategic risks — while allowing **sector-specific specialisation at Layers 2 and 3** through ontology composition.

---

## 1. Strategic Context — Why Risk Management Must Be Rethought

### 1.1 The Problem with Siloed Risk

Most organisations manage risk in disconnected silos:

- **Information security** owns cyber risk registers but cannot articulate business impact
- **Finance** tracks financial risk but ignores operational dependencies
- **Legal/compliance** monitors regulatory exposure but operates in isolation from delivery teams
- **Project management** captures project risk but treats it as a documentation obligation, not a decision instrument
- **Board-level governance** receives aggregated dashboards that obscure root causes

The result: risk is measured but not managed; reported but not acted upon; owned on paper but orphaned in practice.

### 1.2 The Value Erosion Pattern

When risk management fails, the failure mode is almost always the same — **value erosion**. Benefits that were promised to stakeholders, customers, and investors are not realised. The connection between a risk materialising and a benefit being lost is rarely made explicit. This is because risk frameworks and value frameworks operate in different vocabularies, different tools, and different governance structures.

**This paper proposes to close that gap** by formally linking risk management to value engineering through ontology-driven integration.

### 1.3 The Modern Risk Landscape

Contemporary organisations face risk categories that did not exist a decade ago:

| Category | Examples | Trend |
|---|---|---|
| **AI & Autonomous Systems** | Model poisoning, hallucination liability, adversarial attacks, prompt injection, bias amplification | Accelerating |
| **Digital & Cloud** | Supply chain compromise, identity federation failure, data residency violations, API abuse | Expanding |
| **Regulatory & Compliance** | GDPR enforcement escalation, AI Act obligations, NIS2 Directive, sector-specific codes | Multiplying |
| **Operational Resilience** | Ransomware disruption, third-party dependency failure, climate-driven infrastructure loss | Intensifying |
| **Strategic & Competitive** | Market disruption by AI-native competitors, talent attrition, platform lock-in | Accelerating |
| **Reputational & Trust** | Data breach disclosure, AI ethics incidents, ESG scrutiny | Amplifying |

No single international standard covers all of these. A composite framework is required.

---

## 2. VE Skill Chain as the Analytical Lens

The **VE (Value Engineering) Skill Chain** provides the structuring logic for this review. Rather than beginning with risk taxonomies (the traditional approach), we begin with **value** — asking what the organisation exists to deliver — and then systematically identify what could prevent that value from being realised.

### 2.1 VE Skill Chain Stages Applied to Risk

```
VSOM ──→ OKR ──→ KPI ──→ VP ──→ KANO ──→ PMF ──→ EFS
  │        │       │      │       │        │       │
  ▼        ▼       ▼      ▼       ▼        ▼       ▼
Strategic  Goal   Metric  Value  Feature  Market   Execution
Risk       Risk   Risk    Prop.  Priority Fit      Delivery
Context    Align  Drift   Risk   Risk     Risk     Risk
```

| VE Stage | Risk Dimension | Key Question |
|---|---|---|
| **VSOM** (Vision-Strategy-Objectives-Metrics) | Strategic risk context | What risks threaten the vision and strategy? What is our risk appetite at each strategic level? |
| **OKR** (Objectives & Key Results) | Goal alignment risk | Are objectives achievable given the current risk landscape? Do key results account for risk-adjusted outcomes? |
| **KPI** (Key Performance Indicators) | Metric & measurement risk | Are we measuring the right leading indicators? Could metric drift mask emerging risks? |
| **VP** (Value Proposition) | Value proposition risk | What risks threaten our ability to solve the customer's problem and deliver the promised benefit? |
| **KANO** (Feature Analysis) | Feature priority risk | Are must-have requirements (including compliance) classified correctly? Are delighter features consuming resources that should address risk? |
| **PMF** (Product-Market Fit) | Market fit risk | Could regulatory change, competitor action, or market shift invalidate our product-market fit? |
| **EFS** (Execution Feature Set) | Delivery & operational risk | What risks exist in the execution pipeline — technical debt, resource constraints, dependency failures, security vulnerabilities? |

### 2.2 VP-RRR Alignment — The Risk-Value Bridge

The standing convention `JP-VP-RRR-001` provides the formal bridge between value and risk:

| VP Construct | RRR Equivalent | Risk Management Interpretation |
|---|---|---|
| `vp:Problem` | `rrr:Risk` | Every customer problem is a risk to value delivery if unaddressed |
| `vp:Solution` | `rrr:Requirement` | Every solution element is a requirement that must be met to treat the risk |
| `vp:Benefit` | `rrr:Result` | Every promised benefit is a result that risk management must protect |
| `vp:Customer` | `rrr:Stakeholder` | Every customer/stakeholder is a risk owner or affected party |

This is not metaphorical — it is a formal ontology join pattern. When a risk materialises, it maps to a specific value proposition element that is degraded. When a control is implemented, it maps to a specific requirement that protects a specific benefit.

### 2.3 The VSOM-SA and VSOM-SC Dimensions

The VE skill chain includes two critical sub-series that inform risk management:

**VSOM-SA (Strategic Analysis):**
- **INDUSTRY-ONT** → Industry-specific risk profiles and regulatory landscapes
- **MACRO-ONT** → PESTLE analysis identifying macro-environmental risks (political, economic, social, technological, legal, environmental)
- **REASON-ONT** → Causal reasoning chains linking root causes to risk impacts
- **PORTFOLIO-ONT** → Portfolio-level risk aggregation and diversification assessment

**VSOM-SC (Strategic Communication):**
- **CASCADE-ONT** → How risk appetite and tolerance cascade from board to team level
- **CULTURE-ONT** → Organisational culture factors that amplify or mitigate risk (blame culture, reporting openness, learning orientation)
- **NARRATIVE-ONT** → How risk is communicated to different audiences (board vs operational vs external)
- **VIZSTRAT-ONT** → Risk visualisation and reporting frameworks

---

## 3. International Standards Review

### 3.1 Foundation Standards

| Standard | Scope | Strengths | Gaps |
|---|---|---|---|
| **ISO 31000:2018** | Enterprise risk management — domain-agnostic principles, framework, process | Universal applicability; risk as value creation enabler; iterative and dynamic | No specific controls; no sector guidance; no measurement framework |
| **ISO 27005:2022** | Information security risk management | Deep IS-specific guidance; integrates with ISO 27001 ISMS; asset-threat-vulnerability model | Limited to information security; does not address strategic or operational risk |
| **COSO ERM 2017** | Enterprise risk management — integrating with strategy and performance | Strategy-aligned; board-level governance; performance integration; culture emphasis | US-centric governance model; less prescriptive on implementation; weak on cyber |
| **NIST RMF (SP 800-37)** | Information system risk management | Rigorous 7-step process; authorisation framework; continuous monitoring | Federal focus; system-centric (not business-centric); heavy documentation burden |
| **NIST CSF 2.0** | Cybersecurity framework | GOVERN function added (2024); outcome-based; flexible tiers; broad adoption | Cybersecurity only; voluntary; no compliance enforcement |
| **IRM Risk Management Standard** | Professional risk management practice | Practical and accessible; strong on risk culture; UK-originated | Less formal than ISO; limited tooling ecosystem |

### 3.2 UK-Specific & Sector Standards

| Standard | Scope | Relevance |
|---|---|---|
| **NCSC CAF v4.0** | Cyber assessment for UK CNI, NIS Regulations, GovAssure | Mandatory for regulated entities; 4 objectives, 14 principles, 41 outcomes |
| **Cyber Governance Code of Practice** | Board-level cyber governance for UK organisations | 5 principles for directors; aligns with Companies Act 2006 s.174 duty of care |
| **NHS DSPT** | Data Security & Protection Toolkit for health/social care | Mandatory for NHS and care providers; maps to NDG standards |
| **FCA/PRA Operational Resilience** | Financial services operational resilience | Important business services; impact tolerances; scenario testing |
| **ISO 22301** | Business continuity management | BIA, continuity strategies, exercise programmes |
| **BS 31100** | Risk management code of practice | Practical UK supplement to ISO 31000 |

### 3.3 AI-Specific Risk Frameworks

| Framework | Scope |
|---|---|
| **EU AI Act** | Risk-based classification (unacceptable, high, limited, minimal); conformity assessment |
| **NIST AI RMF 1.0** | AI risk management lifecycle: Map, Measure, Manage, Govern |
| **OWASP LLM Top 10** | LLM-specific vulnerability taxonomy (prompt injection, training data poisoning, etc.) |
| **MITRE ATLAS** | Adversarial threat landscape for AI systems |
| **ISO/IEC 42001** | AI management system standard |

### 3.4 Synthesis — What the Composite Framework Must Deliver

No single standard is sufficient. The PFC Generic Risk Management Framework must:

1. **Be domain-agnostic at its core** (ISO 31000 principles) while enabling domain-specific instantiation
2. **Link risk to strategy and value** (COSO ERM + VE Skill Chain alignment)
3. **Provide an IS-specific assessment capability** (ISO 27005 + MCSB + NCSC CAF)
4. **Address AI-specific risk** (NIST AI RMF + OWASP LLM + MITRE ATLAS)
5. **Support UK governance obligations** (Cyber Governance Code + NCSC CAF + GDPR)
6. **Enable board-level risk governance** (Three Lines Model + Evaluate-Direct-Monitor cycle)
7. **Integrate with delivery** (EFS + Requirements Traceability + TDD)
8. **Be measurable** (KPI-ONT + OKR-ONT + BSC-ONT aligned risk metrics)

---

## 4. Proposed Three-Layer Architecture

Building on the PE-RMF pattern already established for FloodGraph AI (PFI-PC-DICE), the Generic Risk Management Framework uses a **three-layer architecture** that separates universal risk management process from strategic and domain-specific refinement.

```
┌──────────────────────────────────────────────────────────────────────────┐
│  LAYER 3 — PFI Instance: Domain-Specific Risk Context                    │
│                                                                          │
│  Instance-specific risk registers, controls, compliance mappings         │
│  VP-ONT (instance value proposition risks)                               │
│  RRR-ONT (instance roles, RACI for risk ownership)                       │
│  Domain ontologies (e.g., FLOOD-HAZARD, FINANCIAL-CRIME, AI-SAFETY)     │
│  Sector-specific regulatory mappings (FCA, CQC, Ofsted, EA, etc.)       │
├──────────────────────────────────────────────────────────────────────────┤
│  LAYER 2 — Strategic Refinement: VE-Aligned Risk Strategy                │
│                                                                          │
│  VSOM-ONT (risk appetite aligned to vision/strategy/objectives)          │
│  OKR-ONT (risk-adjusted objectives, risk KRs)                           │
│  KPI-ONT (leading/lagging risk indicators)                               │
│  BSC-ONT (risk across 4 BSC perspectives)                                │
│  VSOM-SA: INDUSTRY (sector risk profile), MACRO (PESTLE risks),         │
│           REASON (causal risk chains), PORTFOLIO (aggregated risk)       │
│  VSOM-SC: CASCADE (risk appetite cascade), CULTURE (risk culture),      │
│           NARRATIVE (risk communication), VIZSTRAT (risk dashboards)    │
│  VP-ONT ↔ RRR-ONT alignment (JP-VP-RRR-001)                            │
├──────────────────────────────────────────────────────────────────────────┤
│  LAYER 1 — Generic Foundation: Universal Risk Management Process         │
│                                                                          │
│  ERM-ONT (ISO 31000 — domain-agnostic risk register, appetite, controls)│
│  RMF-IS27005-ONT (IS-specific assessment — asset, threat, vulnerability) │
│  GRC-FW-ONT (governance — Evaluate-Direct-Monitor, Three Lines Model)   │
│  MCSB-ONT (cloud security controls — 12 domains, Azure/AWS)            │
│  NCSC-CAF-ONT (UK cyber assessment — 4 objectives, 14 principles)      │
│  GDPR-ONT (data protection — 6 principles, 8 rights, 6 lawful bases)   │
│  PII-ONT (PII governance — classification, encryption, access)          │
│  RCSG-FW-ONT (unified RCSG — Cyber Governance Code integration)        │
│  EMC-ONT (graph composition — scope rules, orchestration)               │
└──────────────────────────────────────────────────────────────────────────┘
```

### 4.1 Layer 1 — Generic Foundation

**Purpose**: Provide the universal risk management process, governance structures, and compliance baselines that every PFI instance inherits.

**Core Process** (ISO 31000-aligned):

```
    ┌─────────────────────────────────────────────────┐
    │           COMMUNICATION & CONSULTATION           │
    │  (stakeholder engagement throughout the cycle)   │
    ├────────┬────────┬────────┬────────┬─────────────┤
    │        │        │        │        │             │
    │  1.    │  2.    │  3.    │  4.    │  5.         │
    │ SCOPE  │IDENTIFY│ANALYSE │EVALUATE│ TREAT       │
    │ &      │ RISKS  │ RISKS  │ RISKS  │ RISKS       │
    │CONTEXT │        │        │        │             │
    │        │        │        │        │             │
    ├────────┴────────┴────────┴────────┴─────────────┤
    │           MONITORING & REVIEW                    │
    │  (continuous, event-driven, periodic)            │
    └─────────────────────────────────────────────────┘
```

**Ontology Mapping**:

| Process Step | Primary Ontology Entity | Supporting Entities |
|---|---|---|
| 1. Scope & Context | `ERM-ONT:RiskRegister` + `RMF:RiskContext` | `GRC-FW:GRCContext`, `ORG-CONTEXT` |
| 2. Identify Risks | `ERM-ONT:Risk` + `RMF:Threat` + `RMF:Vulnerability` | `RMF:Asset`, `MACRO-ONT` (PESTLE) |
| 3. Analyse Risks | `RMF:RiskAssessment` + `RMF:RiskCriteria` | `ERM-ONT:RiskAssessment` |
| 4. Evaluate Risks | `ERM-ONT:RiskAppetite` + `RMF:RiskCriteria` | `GRC-FW:GovernancePrinciple` |
| 5. Treat Risks | `RMF:RiskTreatment` + `RMF:Control` | `ERM-ONT:Control`, `MCSB:SecurityControl` |
| Communication | `GRC-FW:GovernanceBody` + `RRR-ONT:ExecutiveRole` | `NARRATIVE-ONT`, `VIZSTRAT-ONT` |
| Monitoring | `RMF:RiskMonitoringPlan` + `RMF:ComplianceMapping` | `KPI-ONT`, `OKR-ONT` |

**Governance Model** (GRC-FW-ONT):

The Three Lines Model provides accountability:

| Line | Role | Risk Function |
|---|---|---|
| **1st Line** | Operational management, delivery teams | Own and manage risks day-to-day; implement controls; report incidents |
| **2nd Line** | Risk management, compliance, security functions | Set standards; provide expertise; monitor and challenge 1st line; aggregate risk |
| **3rd Line** | Internal audit, external assurance | Independent assurance; effectiveness testing; report to board/audit committee |
| **Board/Governing Body** | Strategic oversight | Set risk appetite; approve risk strategy; monitor enterprise risk profile |

### 4.2 Layer 2 — VE-Aligned Risk Strategy

**Purpose**: Connect risk management to value delivery using the full VE Skill Chain, ensuring every risk is traceable to a value impact and every control is justified by a value protection rationale.

**Risk Appetite Cascade** (CASCADE-ONT pattern):

```
Board Risk Appetite Statement
    │
    ├── Strategic Risk Tolerance (VSOM-ONT)
    │       "We will accept moderate market risk to pursue AI-first strategy"
    │
    ├── Objective-Level Risk Boundaries (OKR-ONT)
    │       "Q2 revenue OKR carries ±15% risk-adjusted variance"
    │
    ├── Product Risk Thresholds (VP-ONT)
    │       "Zero tolerance for data breach; moderate tolerance for feature delay"
    │
    └── Operational Risk Limits (EFS)
            "P0 security stories must complete before sprint close"
```

**Risk-Adjusted OKRs** (OKR-ONT + ERM-ONT integration):

Traditional OKRs ignore risk. Risk-adjusted OKRs explicitly account for threats to achievement:

```json
{
  "objective": "Achieve SOC 2 Type II certification by Q3 2026",
  "key_results": [
    {
      "kr": "Complete all 89 control implementations",
      "risk_adjustment": "3 controls depend on vendor API — 30% delay risk",
      "risk_id": "ROPS-012",
      "contingency": "Manual workaround documented for each vendor-dependent control"
    },
    {
      "kr": "Pass external audit with zero critical findings",
      "risk_adjustment": "New auditor unfamiliar with platform — 15% scope creep risk",
      "risk_id": "ROPS-013"
    }
  ]
}
```

**Risk Categories Mapped to VE Chain** (ERM-ONT 23 categories, grouped):

| VE Stage | Risk Categories | Example Risks |
|---|---|---|
| **VSOM** (Strategic) | Strategic, reputational, competitive, regulatory change | Market disruption by AI-native competitor; regulatory regime change |
| **OKR** (Goal) | Execution, resource, dependency, timeline | Key hire departure; funding shortfall; partner withdrawal |
| **KPI** (Measurement) | Data quality, metric gaming, indicator lag | Leading indicators masking emerging risk; vanity metrics |
| **VP** (Value) | Customer, market, product, proposition | Customer churn; value proposition invalidated by competitor; pricing risk |
| **KANO** (Priority) | Feature scope, technical debt, compliance gap | Must-have compliance feature deprioritised; technical debt accumulating |
| **PMF** (Fit) | Market shift, adoption, retention | Sean Ellis score below threshold; M3 retention declining |
| **EFS** (Execution) | Technical, security, operational, AI-specific | Ransomware; supply chain compromise; model poisoning; prompt injection |

### 4.3 Layer 3 — PFI Instance Specialisation

**Purpose**: Each PFI instance inherits Layers 1 and 2 and adds domain-specific risk context, controls, and compliance mappings.

**Example Instantiations**:

| PFI Instance | Layer 3 Additions |
|---|---|
| **W4M** | Web platform risks, content liability, client data protection, marketing compliance |
| **BAIV** | AI agent safety, model governance, autonomous decision liability, OWASP LLM Top 10 |
| **AIRL** | Azure cloud security, NCSC CAF alignment, zero-trust architecture, PbD/SbD gates |
| **EOMS** | Operational management risks, service continuity, SLA compliance |
| **FloodGraph (DICE)** | Construction sector regulation, environmental data accuracy, professional indemnity |

---

## 5. Risk Assessment Methodology

### 5.1 Qualitative Assessment (Default)

The default assessment uses a **5×5 likelihood-impact matrix** with risk scores mapped to treatment urgency:

**Likelihood Scale**:

| Score | Level | Description |
|---|---|---|
| 1 | Rare | May occur only in exceptional circumstances (<5% in 12 months) |
| 2 | Unlikely | Could occur but not expected (5-20%) |
| 3 | Possible | Might occur at some time (20-50%) |
| 4 | Likely | Will probably occur in most circumstances (50-80%) |
| 5 | Almost Certain | Expected to occur (>80%) |

**Impact Scale** (multi-dimensional — assessed across all relevant dimensions):

| Score | Financial | Operational | Reputational | Regulatory | Strategic |
|---|---|---|---|---|---|
| 1 Negligible | <£10k | <1 hour disruption | No media attention | No regulatory interest | No strategic impact |
| 2 Minor | £10k–£100k | <1 day disruption | Local media | Informal enquiry | Minor delay to objectives |
| 3 Moderate | £100k–£1M | 1–5 days disruption | National media | Formal investigation | Objective at risk |
| 4 Major | £1M–£10M | 1–4 weeks disruption | Sustained national media | Enforcement action | Strategy revision required |
| 5 Critical | >£10M | >4 weeks disruption | International media | Licence/authorisation at risk | Viability threatened |

**Risk Score = Likelihood × Impact**

| Score Range | Rating | Treatment Requirement | Governance Escalation |
|---|---|---|---|
| 1–4 | **Low** | Accept or monitor; review quarterly | 1st Line |
| 5–9 | **Medium** | Mitigate; review monthly | 1st Line + 2nd Line oversight |
| 10–15 | **High** | Mitigate urgently; active treatment plan required | 2nd Line; report to risk committee |
| 16–25 | **Critical** | Immediate action; board notification | Board/governing body; 3rd Line assurance |

### 5.2 Treatment Options (ISO 31000 + ISO 27005)

| Option | Description | When to Use | VP-RRR Mapping |
|---|---|---|---|
| **Mitigate** | Implement controls to reduce likelihood and/or impact | Default for most risks above Low | Solution → Requirement |
| **Accept** | Acknowledge risk within appetite; no further action | Low risks; risks where treatment cost exceeds impact | Documented risk appetite decision |
| **Transfer** | Share risk with third party (insurance, outsourcing, contract) | Financial risks; insurable risks; specialist capabilities | Solution via external party |
| **Avoid** | Eliminate the risk by changing strategy, scope, or approach | Risks outside appetite where no effective mitigation exists | Problem removed; VP pivoted |
| **Exploit** | Take on additional risk to capture opportunity | Strategic opportunities with acceptable risk profile | Problem reframed as opportunity |

### 5.3 Business Rules (from ERM-ONT + RMF-IS27005-ONT)

These rules are encoded in the ontologies and enforced by the framework:

1. Every risk **must** have an owner (`RiskOwner` 1..1 mandatory)
2. Every risk **must** reference at least one affected asset or value element
3. Every risk **must** have both a threat source and a vulnerability/weakness
4. High/Critical risks **must not** be accepted without senior management/executive approval
5. Mitigation treatments **must** implement at least one control
6. Every risk assessment **must** have an active monitoring plan upon completion
7. Regulated risks **should** have compliance framework mappings
8. Risk acceptance **requires** documented rationale and approval authority
9. All P0 risks **must** have corresponding EFS stories (REQ-MAP stage of VE skill chain)
10. Risk register **must** be reviewed at minimum quarterly; Critical risks monthly

---

## 6. VE Skill Chain Integration — The REQ-RISK Pipeline

The existing VE Skill Chain for requirements traceability (PFC-GRC-BRIEF-Requirements-Traceability-EFS-Strategy-v1.0.0.md) defines a 7-stage pipeline. **Stage 7 — RISK-UPDATE** is the formal integration point:

```
REQ-DISCOVER → REQ-CLASSIFY → REQ-MAP → REQ-VALIDATE → REQ-TDD → REQ-RTM → RISK-UPDATE
                                                                                   │
                                                                                   ▼
                                                                         ┌─────────────────┐
                                                                         │  Risk Register   │
                                                                         │  (ERM-ONT)       │
                                                                         │                  │
                                                                         │  Auto-generated  │
                                                                         │  risks from:     │
                                                                         │  - RTM gaps      │
                                                                         │  - Partial cover │
                                                                         │  - Missing tests │
                                                                         │  - Control gaps  │
                                                                         └─────────────────┘
```

**Risk-Requirement Linkage Rules** (from PFC-VE-BRIEF v1.1.0):

| Scenario | Action | Risk Score |
|---|---|---|
| Requirement `coverage_status: Gap` (SHALL obligation) | Auto-generate risk | Inherent: P0 |
| Requirement `coverage_status: Partial` | Flag risk | Residual: P1 |
| Risk has no `mitigating_controls[]` | Flag as unmitigated | Escalate to 2nd Line |
| Risk closed with waiver | Link to requirement as `Waived` | Both reference waiver doc |
| New incidental requirement | Check risk register for existing threat coverage | Merge or link |

### 6.1 Proposed Extension — RISK-ASSESS Skill

To complement the REQ-RISK pipeline, a new **RISK-ASSESS** skill is proposed:

```
RISK-ASSESS Skill Stages:

1. RISK-CONTEXT    — Establish scope using VSOM + ORG-CONTEXT + MACRO (PESTLE)
2. RISK-IDENTIFY   — Discover risks from ERM-ONT categories + VP problem statements
3. RISK-ANALYSE    — Score using 5×5 matrix; map to VE chain impact points
4. RISK-EVALUATE   — Compare against risk appetite (VSOM cascade); prioritise
5. RISK-TREAT      — Select treatment; map to controls (MCSB/NCSC-CAF/custom)
6. RISK-MONITOR    — Define KRIs (KPI-ONT); set review cadence; assign owners (RRR-ONT)
7. RISK-REPORT     — Generate risk register, heat maps, board reports (NARRATIVE + VIZSTRAT)
```

This skill would be registered in `skills-register-index.json` and referenced from the appropriate `PFC-SKBLD-REL-*.md` release document.

---

## 7. Risk Governance Architecture

### 7.1 Board-Level Governance (Cyber Governance Code + GRC-FW-ONT)

The UK Cyber Governance Code of Practice (2025) establishes five principles that this framework embeds:

| Code Principle | Framework Implementation | Ontology Mapping |
|---|---|---|
| **Risk Management** — Directors ensure effective risk management | Risk appetite set at board; cascaded via VSOM-SC:CASCADE | `GRC-FW:GovernancePrinciple`, `ERM:RiskAppetite` |
| **Strategy** — Risk integrated into business strategy | VE Skill Chain aligns risk to VSOM objectives | `VSOM:StrategyComponent`, `ERM:Risk` |
| **People** — Skills, culture, and awareness | CULTURE-ONT risk culture assessment; RRR-ONT role accountability | `RRR:ExecutiveRole`, `CULTURE-ONT` |
| **Incident Management** — Preparedness and response | RMF:RiskTreatment + incident response controls | `RMF:RiskMonitoringPlan` |
| **Assurance & Oversight** — Governance structures and accountability | Three Lines Model; GRC-FW governance bodies | `GRC-FW:GovernanceBody`, `GRC-FW:AssuranceActivity` |

### 7.2 Four-Level Governance Model (PFC Architecture)

| Level | Scope | Risk Governance Role |
|---|---|---|
| **PFC Platform** | Cross-cutting platform risk | Sets enterprise risk framework; defines ontology standards; maintains Layer 1 |
| **PFI Instance** | Instance-specific risk | Manages instance risk register; implements Layer 2 + 3; reports to platform |
| **Product** | Product delivery risk | Manages product backlog risks; runs REQ-RISK pipeline; implements controls |
| **Client** | Client-facing risk | Manages client data risks; meets client-specific compliance; reports to product |

### 7.3 Evaluate-Direct-Monitor Cycle (ISO 38500)

```
         ┌──────────┐
         │ EVALUATE  │ ◄── Risk landscape, appetite, tolerance, external changes
         │           │     (MACRO-ONT PESTLE, INDUSTRY-ONT sector analysis)
         └─────┬─────┘
               │
               ▼
         ┌──────────┐
         │  DIRECT   │ ◄── Risk policies, treatment strategies, resource allocation
         │           │     (GRC-FW:GovernancePolicy, ERM:Control, MCSB:SecurityControl)
         └─────┬─────┘
               │
               ▼
         ┌──────────┐
         │ MONITOR   │ ◄── KRIs, risk register reviews, assurance reports, incidents
         │           │     (KPI-ONT, RMF:RiskMonitoringPlan, GRC-FW:AssuranceActivity)
         └─────┬─────┘
               │
               └──────────► Back to EVALUATE (continuous cycle)
```

---

## 8. Risk Categories — Comprehensive Taxonomy

The ERM-ONT defines 23 risk categories. This framework extends them with VE chain alignment:

### 8.1 Strategic Risks (VSOM Layer)

| ID | Category | Description | VE Impact |
|---|---|---|---|
| RS-01 | Strategic Direction | Risk that the chosen strategy fails to deliver intended outcomes | Vision/Strategy invalidated |
| RS-02 | Competitive | Risk from competitor actions or market disruption | VP differentiation eroded |
| RS-03 | Reputation | Risk to brand, trust, and stakeholder confidence | Customer acquisition/retention impacted |
| RS-04 | Regulatory Change | Risk from new or changed regulation | Compliance costs; strategy pivot required |
| RS-05 | Geopolitical | Risk from political instability, trade policy, sanctions | Supply chain; market access |

### 8.2 Financial Risks (BSC Financial Perspective)

| ID | Category | Description | VE Impact |
|---|---|---|---|
| RF-01 | Revenue | Risk to revenue targets | OKR key results at risk |
| RF-02 | Cost | Risk of cost overrun or uncontrolled expenditure | Margin erosion; investment capacity reduced |
| RF-03 | Liquidity | Risk of insufficient cash flow | Operational continuity threatened |
| RF-04 | Investment | Risk that investments do not deliver expected return | Portfolio value destruction |

### 8.3 Operational Risks (EFS Layer)

| ID | Category | Description | VE Impact |
|---|---|---|---|
| RO-01 | Process | Risk from inadequate or failed internal processes | Delivery quality/speed degraded |
| RO-02 | People | Risk from human error, skills gaps, or turnover | Execution capability reduced |
| RO-03 | Systems | Risk from technology failure or inadequacy | Service availability; data integrity |
| RO-04 | External Events | Risk from events beyond organisational control | Business continuity |
| RO-05 | Supply Chain | Risk from third-party dependency failure | Delivery blocked; cost escalation |

### 8.4 Information Security Risks (RMF-IS27005 Layer)

| ID | Category | Description | VE Impact |
|---|---|---|---|
| RIS-01 | Confidentiality | Unauthorised disclosure of information | Regulatory penalty; trust loss; competitive advantage lost |
| RIS-02 | Integrity | Unauthorised modification of information | Decision quality; audit failure; safety risk |
| RIS-03 | Availability | Disruption to information systems and services | Service delivery; revenue; customer experience |
| RIS-04 | Authentication | Identity compromise or credential theft | Privilege escalation; data breach; fraud |
| RIS-05 | Cloud Security | Misconfiguration, shared responsibility gaps, data residency | Compliance failure; data loss; regulatory action |

### 8.5 AI-Specific Risks (ERM-ONT extended categories)

| ID | Category | Description | VE Impact |
|---|---|---|---|
| RAI-01 | Model Poisoning | Training data manipulation affecting model behaviour | Model integrity; decision quality |
| RAI-02 | Prompt Injection | Malicious input exploiting LLM behaviour | Security bypass; data exfiltration |
| RAI-03 | Hallucination | AI generating plausible but incorrect outputs | Decision quality; liability; trust |
| RAI-04 | Bias Amplification | AI perpetuating or amplifying existing biases | Fairness; regulatory; reputational |
| RAI-05 | Adversarial Examples | Inputs crafted to cause misclassification | Safety; security; reliability |
| RAI-06 | Data Poisoning | Corruption of training or fine-tuning data | Model reliability; output quality |
| RAI-07 | Autonomy Risk | AI agent acting beyond intended scope | Liability; safety; governance |
| RAI-08 | Explainability | Inability to explain AI decisions to stakeholders | Regulatory (AI Act); trust; audit |
| RAI-09 | Dependency Lock-in | Over-reliance on specific AI vendor or model | Strategic flexibility; cost; continuity |

### 8.6 Compliance & Legal Risks (GRC Layer)

| ID | Category | Description | VE Impact |
|---|---|---|---|
| RC-01 | Data Protection | GDPR/DPA 2018 non-compliance | Fines (up to 4% turnover); enforcement; trust |
| RC-02 | Sector Regulation | Industry-specific regulatory non-compliance | Licence risk; enforcement; operational restriction |
| RC-03 | Contractual | Failure to meet contractual obligations | Liability; relationship damage; revenue loss |
| RC-04 | Intellectual Property | IP infringement or inadequate IP protection | Competitive advantage; legal costs |
| RC-05 | AI Regulation | EU AI Act, UK AI framework non-compliance | Market access; fines; product restrictions |

---

## 8A. PPM Issues & Risks Log — The Delivery-Level Risk Interface

### 8A.1 The Gap: Programme Risk vs Enterprise Risk

Enterprise risk management (ERM-ONT, Layer 1) operates at the strategic level — risk registers, appetite statements, governance cycles. But most risks **surface and materialise during delivery** — in programmes, projects, and sprints. The PPM-ONT currently captures a `riskScore` on initiatives but lacks a structured **Issues and Risks Log (RAID)** that would provide the operational interface between delivery teams and the enterprise risk framework.

This is the missing bridge between **Layer 1 (generic RMF)** and **Layer 2/3 (VE-aligned execution)**.

### 8A.2 Proposed PPM Issues & Risks Log Structure

A PPM-level Issues and Risks Log (RAID: Risks, Assumptions, Issues, Dependencies) would extend PPM-ONT to provide the delivery-facing risk interface:

| RAID Element | PPM-ONT Entity | ERM-ONT Bridge | Description |
|---|---|---|---|
| **Risk** | `ppm:ProjectRisk` | `erm:Risk` (escalation) | Uncertain event that, if it occurs, will impact delivery of programme/project objectives |
| **Assumption** | `ppm:ProjectAssumption` | — | Condition believed to be true; if invalidated, generates a risk |
| **Issue** | `ppm:ProjectIssue` | `erm:Risk` (materialised) | A risk that has materialised, or a problem that needs resolution |
| **Dependency** | `ppm:ProjectDependency` | `erm:Risk` (if at risk) | External or internal dependency; becomes a risk if delivery is uncertain |

### 8A.3 Escalation Pattern: PPM → ERM

The critical integration is the **escalation pattern** — when does a project-level risk become an enterprise risk?

```text
PPM Issues & Risks Log (Project/Programme Level)
    │
    ├── Low/Medium risks: managed locally by PM / delivery lead
    │   (1st Line — owns and manages day-to-day)
    │
    ├── High risks: reported to programme board; flagged in ERM risk register
    │   (2nd Line — oversight and challenge)
    │
    └── Critical risks / cross-programme issues: escalated to enterprise risk register
        (Board / 3rd Line — governance and assurance)
```

**Escalation triggers** (PPM → ERM):

| Trigger | Action |
|---|---|
| PPM risk score ≥ High (10+) | Auto-flag for ERM register inclusion |
| Risk impacts multiple programmes/PFI instances | Escalate to enterprise risk — cross-programme aggregation |
| Risk threatens strategic objective (VSOM) | Escalate to enterprise risk — VP-RRR bridge activated |
| Issue unresolved beyond tolerance period | Escalate to enterprise risk with governance action required |
| Assumption invalidated with material impact | Generate enterprise risk from invalidated assumption |

### 8A.4 VE Skill Chain Alignment

The PPM Issues & Risks Log maps directly to the VE Skill Chain:

| VE Stage | PPM RAID Role |
|---|---|
| **VSOM** | Strategic risks cascade down as programme constraints and risk appetite boundaries |
| **OKR** | Key Results at risk are flagged as PPM issues; risk-adjusted KRs reference PPM risk IDs |
| **EFS** | Stories and features that are blocked or at risk map to PPM dependencies and issues |
| **REQ-RISK** (Stage 7) | Requirements gaps auto-generate PPM-level risks before escalation to ERM |

### 8A.5 PPM-ONT Extension Scope (Future — PPM v5.1.0 or v6.0.0)

The PPM-ONT extension would add:

- `ppm:RAIDLog` — Container entity for programme/project-level risks, assumptions, issues, dependencies
- `ppm:RAIDEntry` — Individual RAID item with type, description, owner, status, impact, likelihood, mitigation, escalation status
- `ppm:escalatedTo` — Relationship property linking PPM RAID entry to `erm:Risk` when escalated
- `ppm:riskAppetiteBoundary` — Inherited from VSOM cascade; defines escalation threshold per programme
- Business rule: `ppm:RAIDEntry` with `type: Risk` and `score >= escalationThreshold` → auto-generate `erm:Risk`

**Delivered as [F30.14 (#1029)](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1029) — R-RAID Log: Requirements, Risks, Assumptions, Issues & Dependencies Framework.** Adapted from INS-PPL-AZL patterns ([#34](https://github.com/ajrmooreuk/INS-PPL-AZL/issues/34) Stage-Gate, [#36](https://github.com/ajrmooreuk/INS-PPL-AZL/issues/36) PID Template, [#54](https://github.com/ajrmooreuk/INS-PPL-AZL/issues/54) MITRE RMF, [#65](https://github.com/ajrmooreuk/INS-PPL-AZL/issues/65) Enterprise Risk Register). 8 stories covering PPM-ONT v6.0.0 entity extension, ERM escalation bridge, Stage-Gate checkpoints, PID template, VE alignment, programme tracker panel, scoring methodology, and MITRE framework integration.

---

## 9. Implementation Roadmap

### Phase 1 — Foundation (Immediate)

- [x] ERM-ONT v1.0.0 published (ISO 31000, 23 risk categories)
- [x] RMF-IS27005-ONT v1.0.0 published (12 entities, 12 relationships, 10 rules)
- [x] GRC-FW-ONT v3.0.0 published (governance framework, Three Lines Model)
- [x] MCSB-ONT v2.0.0 published (12 control domains)
- [x] NCSC-CAF-ONT v1.0.0 published (4 objectives, 14 principles)
- [x] VP-RRR alignment convention established (JP-VP-RRR-001)
- [x] This strategy paper (PE-RMF Layer 1 specification)

### Phase 2 — VE Integration (Next)

- [ ] Register RISK-ASSESS skill in `skills-register-index.json`
- [ ] Define risk-adjusted OKR template (OKR-ONT extension)
- [ ] Implement risk appetite cascade pattern (CASCADE-ONT → ERM-ONT bridge)
- [ ] Create PFC-level risk register template (JSON schema in `PBS/GRC/`)
- [ ] Define Key Risk Indicators (KRI) taxonomy in KPI-ONT
- [ ] Extend REQ-RISK pipeline with RISK-ASSESS stages

### Phase 3 — Instance Instantiation (Per-PFI)

- [ ] AIRL: PbD/SbD gates + Azure security posture + NCSC CAF (partially complete — v2.0.0)
- [ ] BAIV: AI agent safety risk register + OWASP LLM + MITRE ATLAS
- [ ] W4M: Client data protection + marketing compliance
- [ ] Azure-RCS: Cross-PFI security assessment platform (Epic 68)

### Phase 4 — Automation & Continuous Assurance (Strategic)

- [ ] Supabase-backed risk register (depends on Epic 59)
- [ ] Automated risk scoring from RTM gap analysis (RISK-UPDATE skill)
- [ ] Risk dashboard generation (VIZSTRAT-ONT patterns)
- [ ] Continuous compliance monitoring against MCSB/NCSC-CAF baselines
- [ ] Board risk reporting automation (NARRATIVE-ONT templates)

---

## 10. Discussion Points

### 10.1 Risk Appetite — Who Decides?

**Question**: At what level should risk appetite be formally articulated, and how does it cascade across PFI instances that may have very different risk profiles?

**Position**: Risk appetite must be set at PFC platform level for cross-cutting risks (data protection, platform security, governance standards) but delegated to PFI instance level for domain-specific risks. The CASCADE-ONT pattern provides the mechanism, but the governance decision on authority boundaries needs explicit agreement.

### 10.2 Quantitative vs Qualitative Assessment

**Question**: Should the framework mandate quantitative risk assessment (Monte Carlo, loss exceedance curves, annualised loss expectancy) or remain qualitative (5×5 matrix)?

**Position**: Begin qualitative (Phase 1–2). Introduce semi-quantitative methods where data exists (Phase 3). Reserve full quantitative assessment for high-value, data-rich PFI instances (Phase 4). The ERM-ONT already supports all three methodologies. The VE Skill Chain's QVF (Quantitative Value Finance) stage provides the economic modelling capability when ready.

### 10.3 AI Risk — Special Category or Integrated?

**Question**: Should AI risk be treated as a separate risk domain with its own governance, or integrated into the standard risk taxonomy?

**Position**: Integrated, with AI-specific extensions. The ERM-ONT already includes 9 AI-specific risk categories within the standard taxonomy. AI risks should flow through the same RISK-ASSESS pipeline, the same governance structures, and the same reporting cadence. However, AI-specific controls (OWASP LLM mitigations, MITRE ATLAS countermeasures, AI Act conformity) require specialist knowledge — this is a 2nd Line function.

### 10.4 Vendor & Multi-Cloud Risk

**Question**: How should the framework handle risk from technology vendors and multi-cloud architectures?

**Position**: The MCSB-ONT shared responsibility model provides the foundation. For each cloud service consumed, the responsibility matrix must be instantiated (what the vendor owns vs what the customer owns). Supply chain risk (RO-05) and vendor dependency risk (RAI-09) should be assessed for every critical vendor. The PORTFOLIO-ONT provides aggregation across vendor dependencies.

### 10.5 Regulatory Divergence — UK, EU, International

**Question**: How do we handle regulatory requirements that differ across jurisdictions (UK GDPR vs EU GDPR vs US state laws; EU AI Act vs UK AI framework)?

**Position**: The CTX (Context) ontology and GDPR-ONT `DataResidencyRegion` pattern provide jurisdiction-aware compliance mapping. The GRC-FW-ONT `ComplianceFramework` entity supports multiple concurrent regulatory mappings. The framework should maintain a single risk assessment process but allow jurisdiction-specific compliance overlays at Layer 3.

### 10.6 Cost of Risk Management

**Question**: How do we ensure the risk management framework itself does not become a value-destroying overhead?

**Position**: This is why the VE Skill Chain integration is essential. Every control must trace back to a value protection rationale. If a control cannot be justified in terms of the value it protects (via the VP-RRR bridge), it should be challenged. The BSC-ONT financial perspective provides the mechanism to track risk management cost against risk reduction benefit. **The framework should make risk management cheaper through automation, not more expensive through bureaucracy.**

---

## 11. Ontology Composition — EMC Integration

The EMC-ONT v5.0.0 composition engine enables dynamic graph assembly for risk assessments. A typical risk assessment graph would compose:

```json
{
  "compositionId": "risk-assessment-standard",
  "description": "Standard risk assessment composition for PFI instances",
  "ontologies": [
    { "id": "ERM-ONT", "role": "primary", "purpose": "Risk register and appetite" },
    { "id": "RMF-IS27005-ONT", "role": "supporting", "purpose": "IS risk assessment detail" },
    { "id": "GRC-FW-ONT", "role": "supporting", "purpose": "Governance structure" },
    { "id": "VSOM-ONT", "role": "context", "purpose": "Strategic alignment" },
    { "id": "VP-ONT", "role": "context", "purpose": "Value proposition at risk" },
    { "id": "RRR-ONT", "role": "context", "purpose": "Role accountability" },
    { "id": "KPI-ONT", "role": "supporting", "purpose": "Risk indicators" },
    { "id": "MCSB-ONT", "role": "optional", "purpose": "Cloud security controls" },
    { "id": "NCSC-CAF-ONT", "role": "optional", "purpose": "UK cyber assessment" },
    { "id": "GDPR-ONT", "role": "optional", "purpose": "Data protection compliance" }
  ],
  "constrainToInstanceOntologies": true
}
```

The `constrainToInstanceOntologies` filter ensures that only ontologies subscribed by the active PFI instance are included in the composed graph — maintaining scope discipline.

---

## 12. Conclusion

Risk management is not a department, a register, or a compliance tick-box — it is a **strategic capability** that determines whether an organisation delivers the value it promises to its stakeholders, customers, teams, and investors.

This framework proposes a fundamental shift:

1. **From siloed to integrated** — risk connected to value through the VE Skill Chain
2. **From static to dynamic** — continuous assessment via the REQ-RISK pipeline and RISK-ASSESS skill
3. **From compliance-driven to strategy-driven** — risk appetite cascaded from VSOM through CASCADE-ONT
4. **From domain-specific to domain-agnostic at core** — Layer 1 serves all risk types; Layers 2 and 3 specialise
5. **From manual to ontology-driven** — EMC composition enables AI-assisted risk assessment at scale

The ontology library already contains the semantic foundation: ERM-ONT, RMF-IS27005-ONT, GRC-FW-ONT, MCSB-ONT, NCSC-CAF-ONT, GDPR-ONT, and the full VE-Series. What remains is the operational instantiation — risk registers, assessment cadences, governance bodies, and the RISK-ASSESS skill chain that brings it all to life.

**The next step is to decide**: which PFI instance runs the first full-cycle risk assessment using this framework, and which Layer 3 domain specialisation do we build first?

---

## References

### International Standards
- ISO 31000:2018 — Risk Management: Guidelines
- ISO/IEC 27005:2022 — Information Security Risk Management
- ISO/IEC 27001:2022 — Information Security Management Systems
- ISO/IEC 38500:2024 — Governance of IT for the Organisation
- ISO 22301:2019 — Business Continuity Management Systems
- ISO/IEC 42001:2023 — Artificial Intelligence Management System
- COSO ERM 2017 — Enterprise Risk Management: Integrating with Strategy and Performance
- NIST SP 800-37 Rev.2 — Risk Management Framework for Information Systems
- NIST CSF 2.0 — Cybersecurity Framework
- NIST AI RMF 1.0 — Artificial Intelligence Risk Management Framework

### UK-Specific Standards
- NCSC Cyber Assessment Framework v4.0
- NCSC Cyber Governance Code of Practice v1.0 (2025)
- UK GDPR + Data Protection Act 2018
- IIA Three Lines Model (2020)
- BS 31100 — Risk Management: Code of Practice
- NHS Data Security and Protection Toolkit

### AI-Specific Frameworks
- EU AI Act (Regulation 2024/1689)
- OWASP LLM Top 10
- MITRE ATLAS — Adversarial Threat Landscape for AI Systems

### PFC Ontology Library (Layer 1 Foundation)
- `ERM-ONT v1.0.0` — Enterprise Risk Management (ISO 31000)
- `RMF-IS27005-ONT v1.0.0` — Information Security Risk Management
- `GRC-FW-ONT v3.0.0` — Governance, Risk & Compliance Framework
- `MCSB-ONT v2.0.0` — Microsoft Cloud Security Benchmark
- `NCSC-CAF-ONT v1.0.0` — UK Cyber Assessment Framework
- `GDPR-ONT v1.0.0` — Data Protection Regulation
- `PII-ONT v3.3.0` — PII Governance
- `RCSG-FW-ONT v2.0.0` — Unified RCSG Framework
- `EMC-ONT v5.0.0` — Entity Model Composition

### PFC VE-Series (Layer 2 Value Alignment)
- `VSOM-ONT v3.0.0` — Vision-Strategy-Objectives-Metrics
- `OKR-ONT v2.1.0` — Objectives & Key Results
- `KPI-ONT v1.0.0` — Key Performance Indicators
- `VP-ONT v1.2.3` — Value Proposition
- `RRR-ONT v4.0.0` — Roles, RACI & RBAC
- `BSC-ONT v1.0.0` — Balanced Scorecard
- `KANO-ONT v1.0.0` — Feature Analysis
- `PMF-ONT v2.0.0` — Product-Market Fit
- `MACRO-ONT v1.0.0` — Macro-Environmental Analysis
- `INDUSTRY-ONT v1.0.0` — Industry Classification
- `REASON-ONT v1.0.0` — Causal Reasoning
- `PORTFOLIO-ONT v1.0.0` — Portfolio Management
- `CASCADE-ONT v1.0.0` — Strategy Cascade
- `CULTURE-ONT v1.0.0` — Organisational Culture
- `NARRATIVE-ONT v1.0.0` — Strategy Communication
- `VIZSTRAT-ONT v1.0.0` — Strategy Visualisation

### Related Strategy Briefings
- `PFC-GRC-BRIEF-Requirements-Traceability-EFS-Strategy-v1.0.0.md`
- `PFC-VE-BRIEF-VE-Foundation-QVF-GRC-Risk-Register-Integration-v1.0.0.md`
- `PFC-GRC-PLAN-Security-First-Platform-Implementation-v1.0.0.md`
- `PFC-GRC-BRIEF-Azure-RCS-Assessment-Platform-v1.0.0.md`
- `PFI-AIRL-GRC-BRIEF-02-PbD-SbD-Foundations-v2.0.0.md`
- `PFC-STRAT-BRIEF-VSOM-FloodGraph-AI-FRA-v1.0.0.md`
