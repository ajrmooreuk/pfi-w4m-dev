# BRIEFING: Enterprise Functional Stack — Do We Need an Org Functions Ontology?

**Date**: 2026-02-26
**Type**: VSOM VE Skill-Chain Evaluation
**Status**: DISCUSSION DRAFT — Not for implementation
**Registry Placeholder**: EFS-ONT (Enterprise Functional Stack) — TBD / Placeholder
**Cross-Refs**: RRR-ONT v4.0.0, PE-ONT v4.0.0, EA-CORE-ONT v1.0.0, BSC-ONT, KPI-ONT, ORG-CONTEXT-ONT, VP-ONT v1.2.3, PMF-ONT v2.0.0, EMC-ONT v5.0.0, INDUSTRY-ONT, MACRO-ONT, REASON-ONT, DMAIC-ONT, K-DMAIC-ONT
**Extended Scope**: Sections 17-22 consolidate the CQ-driven concept classification framework, methodology taxonomy, and Kano Analysis worked example from the VE skill-chain evaluation session

---

## 1. The Question

RRR-ONT v4.0.0 encodes 48 C-Suite and cascading roles across 6 tiers. Each role carries a `functionalDomain` (12 values) and `functionalScope` linking executive accountability to organisational functions like Finance, Marketing, Operations, HR, Technology, Legal, etc.

**But no ontology currently defines what those functions ARE.**

The `function` enum on `FunctionalRole` lists 14 values (Marketing, Sales, Operations, Finance, Technology, HR, Legal, Product, Data, Customer Success, Strategy, Innovation, Compliance, Security) — but these are flat labels, not structured entities with decomposition, maturity, process hierarchies, or capability mapping.

**Core question**: Should we activate EFS-ONT to model organisational functions as first-class entities, or is our ontological position that every function is ultimately a top-down hierarchy of processes (PE-ONT) governed by roles (RRR-ONT)?

---

## 2. The Gap — Worked Example: Operational Excellence

Consider how "Operational Excellence" traverses the current ontology stack:

| Concern | Current Ontology | What It Provides | What's Missing |
|---------|-----------------|------------------|----------------|
| **Who owns it?** | RRR-ONT | COO (L1 Core C-Suite), `functionalDomain: Operations` | Nothing — well covered |
| **What is the function?** | ??? | The `function: Operations` enum value | **No entity** — Operations as a function has no structure, sub-functions, decomposition, or maturity |
| **How is it done?** | PE-ONT | `pf:Process`, `pf:ProcessPhase`, `pf:ProcessGate` | Process hierarchy exists but isn't scoped to a function |
| **SOPs?** | PE-ONT | SOPs are processes — `processType: governance` or custom | SOPs exist as process instances but no formal SOP entity or compliance tracking |
| **What can it do?** | EA-CORE-ONT | `ea:BusinessCapability`, `ea:CapabilityGap` | Architecture-oriented, not function-operational |
| **How do we measure?** | KPI-ONT | KPIs per objective | KPIs not inherently scoped to a function |
| **Strategic frame?** | BSC-ONT | `bscPerspective: Internal Process` | BSC perspective alignment exists but no function→perspective mapping |
| **Maturity?** | ORG-CONTEXT-ONT | 6 maturity dimensions (size, value, marketing, technology, complexity, AI) | Maturity is org-wide, not per-function |

### The Concrete Gap

When a PFI instance (e.g., BAIV) wants to model "the client's Operations function is at maturity level 3 and runs 47 SOPs across 6 sub-functions (Procurement, Warehousing, Logistics, Quality, Maintenance, Production Planning), with the COO accountable and 3 AI agents augmenting Procurement" — **there is no single ontological home for this**.

Today you'd need to:
1. Create an RRR `FunctionalRole` with `function: Operations` (flat label)
2. Create PE-ONT processes for each SOP (but no grouping by function)
3. Create EA-CORE capabilities (architecture-level, not operational)
4. Create KPIs (not scoped to the function)
5. Cross-reference everything manually

**There is no entity that says "this IS the Operations function, here are its sub-functions, here's its maturity, here are its processes, here's who owns it, here's how it maps to BSC perspectives."**

---

## 3. The Philosophical Position — Two Schools

### School A: Functions ARE Process Hierarchies (No New Ontology)

**Argument**: Every organisational function ultimately decomposes into Standard Operating Procedures. "Finance" is just the collection of processes owned by the CFO. "Operations" is just the collection of processes owned by the COO. The function label is a classifier, not a first-class entity.

**Supporting evidence**:
- SOPs are processes. PE-ONT handles processes.
- RRR-ONT's `functionalDomain` enum + role accountability already provides the "who"
- EA-CORE-ONT's `BusinessCapability` provides the "what can it do"
- Adding another ontology increases complexity for questionable value
- The PE-ONT `ProcessPattern` entity could serve as a function template

**What this looks like**:
```
COO (RRR) --owns--> [Process: Procurement SOP-001] (PE)
                     [Process: Warehousing SOP-002] (PE)
                     [Process: Quality SOP-003] (PE)
                     ...tagged with processType + functionalDomain enum
```

**Weakness**: No aggregation point. You can't query "show me the Operations function" — you can only query "show me all processes where the owner's functionalDomain = Operations". No function-level maturity. No function decomposition hierarchy.

### School B: Functions Are First-Class Entities (Activate EFS-ONT)

**Argument**: A function is more than the sum of its processes. It has identity, structure, maturity, budget, headcount, strategic alignment, and lifecycle — none of which are properties of individual processes.

**Supporting evidence**:
- EFS-ONT already exists as a registry placeholder — the need was anticipated
- EA-CORE-ONT has `BusinessCapability` but this is architecture-focused (TOGAF ADM context), not operational
- PFI instances need to model client org structures to map agents/products
- BSC perspectives (Internal Process, Customer, Learning & Growth, Financial) map naturally to functional groupings
- ORG-CONTEXT maturity is org-wide; function-level maturity is a real operational need
- The COO doesn't just "own processes" — they own a function that has structure, budget, targets, and maturity independent of any single process

**What this looks like**:
```
Operations Function (EFS) --ownedBy--> COO (RRR)
    |--subFunction--> Procurement (EFS)
    |     |--hasProcess--> SOP-001 (PE)
    |     |--hasProcess--> SOP-002 (PE)
    |     |--augmentedBy--> AI Agent: ProcurementBot (PE)
    |--subFunction--> Warehousing (EFS)
    |--subFunction--> Quality (EFS)
    |--realizesCapability--> ea:OperationalExcellence (EA-CORE)
    |--mapsToPerspective--> bsc:InternalProcess (BSC)
    |--functionalMaturity--> Level 3: Defined (EFS)
    |--hasKPI--> OpsEfficiency-KPI-001 (KPI)
```

---

## 4. BSC Perspective Cross-Reference

The BSC's four core perspectives map naturally to functional groupings — this is where School B gains significant weight:

| BSC Perspective | Primary Functions | C-Suite Owner(s) | Current Ontology Coverage |
|----------------|-------------------|-------------------|--------------------------|
| **Financial** | Finance, Treasury, Tax, FP&A, Investor Relations | CFO | RRR role exists, no function entity |
| **Customer** | Sales, Marketing, Customer Success, CX, Support | CRO-Revenue, CMO, CCO-Customer, CXO | RRR roles exist, VP-ONT has customer problems/solutions, no function entities |
| **Internal Process** | Operations, Procurement, Logistics, Quality, Production, IT Ops, Security Ops | COO, CTO, CISO | PE-ONT processes exist but not grouped by function. LSC/OFM cover supply chain specifics. |
| **Learning & Growth** | HR, L&D, Talent, Culture, Knowledge Management, Innovation | CHRO, CKO, CIO | RRR roles exist, no function entities. ORG-CONTEXT has maturity but org-wide. |

### Extended BSC Perspectives (RRR-ONT v4.0.0 defines 10):

| BSC Perspective | Primary Functions | Gap |
|----------------|-------------------|-----|
| **Innovation** | R&D, Product, Strategy, Digital | No function entity |
| **Risk** | Risk Management, Compliance, Legal, Internal Audit | GRC-FW-ONT covers governance frameworks but not the Risk function as an org unit |
| **Sustainability** | ESG, Environmental, Social Impact | No function entity |
| **People** | Overlaps L&G — HR, Wellbeing, DEI | No function entity |
| **Digital** | Digital Transformation, Data, AI, Platform | No function entity |
| **Market** | Market Intelligence, Competitive Analysis | ORG-CONTEXT covers competitive landscape but not the function |

**Key insight**: BSC perspectives are strategic lenses. Functions are operational realities. The mapping between them is many-to-many (Finance function contributes to Financial AND Risk perspectives; Operations contributes to Internal Process AND Customer perspectives). This mapping IS the bridge between VE-Series strategic view and PE-Series operational view — and it currently has no ontological home.

---

## 5. The SOP Question

The user raises a critical point: **are SOPs essentially processes?**

**Yes — but with caveats.**

| SOP Characteristic | PE-ONT Coverage | Gap |
|-------------------|-----------------|-----|
| Documented procedure | `pf:Process` + `pf:ProcessArtifact` | Covered |
| Sequential phases | `pf:ProcessPhase` | Covered |
| Quality gates | `pf:ProcessGate` (6 gate types) | Covered |
| Metrics | `pf:ProcessMetric` | Covered |
| Compliance requirement | `processType: governance` | Partially covered |
| Version control | Not explicit | **Gap** — SOPs have formal versioning, approval, review cycles |
| Regulatory linkage | Not explicit | **Gap** — SOPs often required by regulation (GRC-FW-ONT link) |
| Training requirement | Not explicit | **Gap** — SOPs require staff certification (L&G function) |
| Function scoping | Not explicit | **Gap** — SOPs belong to a function, not just a role |
| Audit trail | Not explicit | **Gap** — SOP compliance needs audit evidence |

**Assessment**: PE-ONT can model SOPs as processes, but SOPs have governance, compliance, and lifecycle characteristics that go beyond generic process modelling. This reinforces the need for either:
- (a) An SOP extension to PE-ONT, or
- (b) EFS-ONT acting as the bridge that scopes processes (including SOPs) to functions with compliance/governance metadata

---

## 6. Evaluation Matrix

| Criterion | School A (No New ONT) | School B (Activate EFS-ONT) |
|-----------|----------------------|----------------------------|
| Complexity | Lower — no new ontology | Higher — new ontology + bridges |
| Expressiveness | Limited — flat enum labels | Rich — structured decomposition |
| PFI Instance Modelling | Weak — can't model client functions | Strong — map agents to client functions |
| BSC Alignment | Manual — no formal mapping | Native — function→perspective bridge |
| Maturity Tracking | Org-wide only (ORG-CONTEXT) | Per-function maturity |
| SOP Scoping | By role only | By function + sub-function |
| Agent Mapping | Agent→Process (PE) | Agent→Function→Process chain |
| Capability Bridge | Direct EA-CORE→Process | EA-CORE→Function→Process (richer) |
| Operational Excellence | Implicit in process metrics | Explicit function-level measurement |
| Ontology Count Impact | 52 (no change) | 53 (+1, placeholder already exists) |
| Cross-Series Coherence | VE↔PE gap remains | VE↔EFS↔PE bridge completes the chain |

---

## 7. VSOM VE Skill-Chain Assessment

### Vision
**Every organisational function — from Finance to Operations to Marketing — should be a navigable, measurable, agent-augmentable node in the PFC graph, bridging strategic intent (VE-Series) to operational execution (PE-Series).**

### Strategy: Activate EFS-ONT as the VE↔PE Bridge Ontology

**Recommended Position**: School B — with constraints.

EFS-ONT should NOT attempt to define every function's internal processes (that's PE-ONT's job). Instead, EFS-ONT should be a **thin bridge ontology** that:

1. **Defines functions as first-class entities** with identity, decomposition, and maturity
2. **Bridges to PE-ONT** via `hasProcess` / `hasSOP` relationships (functions scope processes)
3. **Bridges to RRR-ONT** via `ownedByRole` relationship (roles own functions)
4. **Bridges to EA-CORE-ONT** via `realizesCapability` (functions realize capabilities)
5. **Bridges to BSC-ONT** via `contributesToPerspective` (many-to-many function↔perspective map)
6. **Bridges to KPI-ONT** via `measuredBy` (function-level KPIs)
7. **Provides function-level maturity** (extending ORG-CONTEXT's org-wide maturity to per-function granularity)

### Proposed EFS-ONT Entity Sketch (Discussion Only)

| Entity | Purpose | Key Properties |
|--------|---------|----------------|
| `efs:OrgFunction` | Top-level function (Finance, Operations, Marketing...) | name, functionType, maturityLevel, bscPerspectives[], status |
| `efs:SubFunction` | Decomposition (FP&A, Treasury, Tax under Finance) | name, parentFunction, level, maturityLevel |
| `efs:FunctionProcess` | Bridge: scopes a PE process to a function | function, process, sopCompliance, regulatoryRequirement |
| `efs:FunctionMaturity` | Per-function maturity assessment | function, dimension, level (1-5), assessmentDate, evidence |
| `efs:FunctionBudget` | Function-level resource allocation | function, period, headcount, budget, agentCount |

### Proposed Relationship Sketch (Discussion Only)

| Relationship | From | To | Purpose |
|-------------|------|----|---------|
| `ownedByRole` | OrgFunction | RRR:ExecutiveRole | Strategic accountability |
| `hasSubFunction` | OrgFunction | SubFunction | Decomposition hierarchy |
| `hasProcess` | OrgFunction/SubFunction | PE:Process | Process scoping |
| `realizesCapability` | OrgFunction | EA:BusinessCapability | Capability mapping |
| `contributesToPerspective` | OrgFunction | BSC:Perspective | BSC alignment (M:N) |
| `measuredBy` | OrgFunction | KPI:KPI | Function-level KPIs |
| `hasMaturity` | OrgFunction | FunctionMaturity | Per-function maturity |
| `orgContextFunction` | ORG-CONTEXT | OrgFunction | Links org context to functions |

### Objectives

| ID | Objective | BSC Perspective | Metric |
|----|-----------|----------------|--------|
| OBJ-EFS-1 | Every PFI instance can model client org functions to L2 depth | Internal Process | Function coverage % per PFI |
| OBJ-EFS-2 | Agent-to-function mapping enables targeted augmentation | Digital | Agents mapped to functions % |
| OBJ-EFS-3 | BSC perspective alignment is queryable across all functions | Financial + Customer | Perspective coverage completeness |
| OBJ-EFS-4 | Function-level maturity tracked independently of org maturity | Learning & Growth | Functions with maturity scores % |

### Metrics

| Metric | Target | Source |
|--------|--------|--------|
| Entity count (EFS-ONT) | 5-7 (thin bridge, not bloated) | Ontology file |
| Relationship count | 8-12 (cross-series bridges) | Ontology file |
| PFI instances using EFS | 3+ within 6 months of activation | Registry |
| Function→Process links per PFI | 20+ SOPs scoped to functions | Instance data |
| BSC perspective coverage | 100% of 10 perspectives mapped | Instance data |

---

## 8. The Operational Excellence Test Case

If EFS-ONT existed, modelling "Operational Excellence" would look like:

```
efs:OperationsFunction (OrgFunction)
  |--ownedByRole--> rrr:COO
  |--contributesToPerspective--> bsc:InternalProcess (primary)
  |--contributesToPerspective--> bsc:Customer (secondary — delivery quality)
  |--contributesToPerspective--> bsc:Financial (secondary — cost efficiency)
  |--maturityLevel--> 3 (Defined)
  |--hasSubFunction--> efs:Procurement
  |     |--hasProcess--> pe:SOP-PROC-001 "Supplier Evaluation"
  |     |--hasProcess--> pe:SOP-PROC-002 "Purchase Order Processing"
  |     |--augmentedBy--> pe:AIAgent "ProcurementBot" (agentActsAs: rrr:ProcurementManager)
  |     |--maturityLevel--> 4 (Managed)
  |--hasSubFunction--> efs:Warehousing
  |     |--hasProcess--> pe:SOP-WH-001 "Goods Receiving"
  |     |--hasProcess--> pe:SOP-WH-002 "Stock Rotation (FIFO)"
  |     |--maturityLevel--> 2 (Repeatable)
  |--hasSubFunction--> efs:Quality
  |     |--hasProcess--> pe:SOP-QA-001 "Incoming Inspection"
  |     |--hasProcess--> pe:SOP-QA-002 "Non-Conformance Handling"
  |     |--regulatoryLink--> grc:ISO9001Requirement
  |     |--maturityLevel--> 3 (Defined)
  |--hasSubFunction--> efs:Logistics
  |     |--bridgesTo--> lsc:SupplyChain (LSC-ONT handles the detail)
  |     |--maturityLevel--> 3 (Defined)
  |--hasSubFunction--> efs:Maintenance
  |--hasSubFunction--> efs:ProductionPlanning
  |--realizesCapability--> ea:OperationalExcellence
  |--measuredBy--> kpi:OPS-EFF-001 "Operational Efficiency Ratio"
  |--measuredBy--> kpi:OPS-COST-001 "Cost per Unit Processed"
```

### BSC Perspective Walkthrough for Operations Function:

| Perspective | How Operations Contributes | Measurable Via |
|-------------|---------------------------|----------------|
| **Financial** | Cost reduction, efficiency gains, waste elimination | Cost per unit, gross margin impact |
| **Customer** | On-time delivery, quality consistency, order accuracy | OTIF %, defect rate, NPS contribution |
| **Internal Process** | Process standardisation, SOP compliance, cycle time | SOP compliance %, cycle time, throughput |
| **Learning & Growth** | Staff training, capability development, knowledge transfer | Training hours, certification rates, knowledge base coverage |
| **Risk** | Operational risk, supply disruption, quality failure | Incident rate, MTTR, risk score |
| **Digital** | Automation level, agent augmentation, data maturity | Automation %, agent coverage, data quality score |

---

## 9. Recommendation & Direction

### Recommended Direction: Activate EFS-ONT as a Thin Bridge Ontology

**Rationale**:
1. The gap is real — demonstrated by the Operational Excellence worked example
2. The placeholder already exists in the registry — the need was anticipated at architecture time
3. It's a BRIDGE, not a domain ontology — should remain thin (5-7 entities, 8-12 relationships)
4. It completes the VE↔PE chain: `VSOM Strategy → RRR Role → EFS Function → PE Process/SOP`
5. It enables per-function BSC perspective mapping (currently impossible)
6. It enables per-function maturity (currently only org-wide)
7. It enables agent-to-function targeting (critical for BAIV and PFI instances)
8. SOPs remain in PE-ONT — EFS-ONT only scopes them to functions, it doesn't redefine them

### What EFS-ONT Is NOT:
- NOT a replacement for PE-ONT (processes stay in PE)
- NOT a replacement for RRR-ONT roles (roles stay in RRR)
- NOT a replacement for EA-CORE capabilities (capabilities stay in EA-CORE)
- NOT a deep domain ontology (no 500-entity functional decomposition)
- NOT prescriptive about which sub-functions exist (instance data defines those per PFI client)

### Proposed Series Placement: PE-Series

EFS-ONT belongs in PE-Series because it is operationally focused (how the org is structured to execute), even though it bridges heavily to VE-Series (who owns and measures). This is consistent with LSC-ONT and OFM-ONT being in PE-Series while importing from VE-Series.

### Skill Chain: VE → EFS → PE

```
VSOM Vision/Strategy (VE)
    ↓ cascades via
RRR Role Accountability (VE)
    ↓ owns
EFS Org Function (PE — BRIDGE)
    ↓ decomposes into
EFS Sub-Functions (PE — BRIDGE)
    ↓ has processes
PE Process / SOP (PE)
    ↓ measured by
KPI Metrics (VE)
    ↓ aligned to
BSC Perspectives (VE-SA)
    ↓ governed by
GRC Frameworks (RCSG)
```

### Next Steps (If Direction Approved):
1. Feature issue under appropriate epic for EFS-ONT v1.0.0 design
2. Define the function taxonomy (instance data, not ontology-level — ontology defines the structure, PFI instances define the actual functions)
3. Define the SOP compliance bridge pattern (EFS→PE→GRC)
4. W4M-WWG as first PFI test case (7 declared ontologies + EFS = 8)
5. BAIV as second test case (16 agents mapped to functions)

---

## 10. Open Questions for Discussion

1. **Function taxonomy level**: Should EFS-ONT define a reference taxonomy of functions (Finance, Marketing, Operations, etc.) as enum values, or should functions be entirely instance-defined per PFI client?

2. **SOP formality**: Should PE-ONT gain a formal `SOP` entity subtype of `Process` with versioning, approval, and regulatory linkage? Or does the `FunctionProcess` bridge in EFS-ONT handle this?

3. **Maturity model**: Should function maturity use the same CMMI 1-5 scale as EA-CORE-ONT, or a custom scale? Should it decompose by dimension (people, process, technology, data, governance)?

4. **ORG-MAT absorption**: Memory notes that ORG-MAT merges into ORG-CONTEXT. Should function-level maturity also live in ORG-CONTEXT (extended) rather than EFS-ONT?

5. **BSC perspective mapping**: Is the mapping from function to BSC perspective static (defined in ontology) or dynamic (defined per PFI instance)?

6. **EMC composition impact**: Would EFS-ONT be added to EMC composition categories? New category FUNCTIONAL? Or absorbed into OPERATIONAL?

---

## 11. The DMAIC Naming Problem — Ontologies vs Entities vs Patterns

### The Inconsistency

The current library has a structural confusion that DMAIC exposes perfectly:

| Artefact | What It Is | Named As |
|----------|-----------|----------|
| PE-ONT DMAIC template | Instance data of PE-ONT — 5 phases, 5 gates, 15 artifacts as PE entities | `pe-dmaic-process-template-v1.0.0.jsonld` (instance data file) |
| DMAIC-ONT | Standalone ontology — 8 entity types (SIPOC, CTQ, MSA, ProcessCapability, RCA, Experiment, ControlPlan, SixSigmaProject) | `dmaic-v1.0.0-oaa-v7.json` (ontology) |
| K-DMAIC-ONT | Standalone ontology — 8 entity types (KaizenEvent, GembaObservation, WasteCategory, VSM, RapidExperiment, QuickWin, VisualControl, FollowUpAction) | `kaizen-dmaic-v1.0.0-oaa-v7.json` (ontology) |

**The problem**: "DMAIC" appears three times at three different architectural levels, and the naming doesn't make the levels clear. On the graphing canvas:

- **Tier 0 (Series rollup)**: PE-Series is one super-node. DMAIC-ONT and K-DMAIC-ONT are both inside it.
- **Tier 1 (Series drill into PE)**: DMAIC-ONT and K-DMAIC-ONT appear as ontology-level nodes alongside PE-ONT, LSC-ONT, OFM-ONT, etc.
- **Tier 2 (Entity graph)**: Inside DMAIC-ONT you see `dmaic:SixSigmaProject`, `dmaic:SIPOC`, `dmaic:CTQ`, etc.

But "K-DMAIC" as an ontology name suggests it's a **knowledge variant** of DMAIC, when it's actually **Kaizen DMAIC** — an entirely different methodology (3-5 day blitz vs 8-24 week project). The "K" prefix looks like a taxonomy classifier ("K for Knowledge") rather than a methodology name.

### The Deeper Architectural Question

This surfaces a broader naming convention issue across the library:

| Level | What It Represents | Current Naming | Canvas Tier |
|-------|-------------------|----------------|-------------|
| **Series** | A family of related ontologies sharing a strategic domain | `VE-Series`, `PE-Series`, `RCSG-Series`, `Foundation` | Tier 0 |
| **Ontology** | A schema defining entity types, relationships, rules for a specific domain | `VP-ONT`, `PE-ONT`, `DMAIC-ONT` | Tier 1 |
| **Entity Type** | A class/type within an ontology (the schema definition) | `vp:Problem`, `pe:Process`, `dmaic:SIPOC` | Tier 2 |
| **Instance Data** | Concrete instances of entity types | `pe-dmaic-process-template`, `RRR-DATA-csuite-roles` | Tier 2 (rendered as nodes) |
| **Process Pattern** | A reusable template instantiated via PE-ONT's `ProcessPattern` entity | `DMAIC Cycle`, `PDCA Sub-Cycle` | Tier 2 (entity within PE) |

**The confusion**: DMAIC exists simultaneously as:
1. A **Process Pattern** in PE-ONT instance data (how the 5-phase flow works)
2. A **standalone Ontology** DMAIC-ONT (the Six Sigma statistical concepts SIPOC/CTQ/SPC that PE can't express)
3. A **variant Ontology** K-DMAIC-ONT (the Kaizen Lean concepts Gemba/VSM/DOWNTIME that PE can't express)

This is actually **correct architecture** — the PE template models the process flow, while the ONTs model the domain-specific concepts. But the naming doesn't communicate this:

- `PE-ONT` + DMAIC template = "how DMAIC works as a process" (generic flow)
- `DMAIC-ONT` = "what Six Sigma DMAIC needs that generic processes can't express" (domain concepts)
- `K-DMAIC-ONT` = "what Kaizen DMAIC needs that neither PE nor DMAIC-ONT can express" (Lean concepts)

### Proposed Naming Convention Direction

Consider whether the library needs an explicit **level taxonomy** in the registry to prevent this confusion:

| Level | Convention | Example |
|-------|-----------|---------|
| Schema Ontology | `{DOMAIN}-ONT` | `PE-ONT`, `DMAIC-ONT`, `EFS-ONT` |
| Domain Extension Ontology | `{PARENT}-{VARIANT}-ONT` | `DMAIC-KAIZEN-ONT` (not K-DMAIC) |
| Process Pattern/Template | `{PARENT}-PATTERN-{name}` or instance data file | `pe-dmaic-process-template` (existing, correct) |
| Instance Data | `{ONT}-DATA-{scope}` or `{ONT}-INSTANCE-{pfi}` | `RRR-DATA-csuite-roles` (existing, correct) |

The "K-" prefix is ambiguous. Renaming to `DMAIC-KAIZEN-ONT` or placing it as a sub-ontology under `PE-Series/DMAIC-ONT/kaizen/` would make the relationship clearer on the canvas.

**Open question**: Should DMAIC-ONT and K-DMAIC-ONT be a **sub-series** of PE-Series (like VSOM-SA and VSOM-SC are sub-series of VE-Series)? This would give them a grouping node at Tier 1:

```
PE-Series (Tier 0)
  |-- PE-ONT (Tier 1) — generic process schema
  |-- DMAIC Sub-Series (Tier 1 grouping)
  |     |-- DMAIC-ONT (Tier 1.1) — Six Sigma statistical domain
  |     |-- DMAIC-KAIZEN-ONT (Tier 1.1) — Lean Kaizen domain
  |-- LSC-ONT (Tier 1)
  |-- OFM-ONT (Tier 1)
  |-- EFS-ONT (Tier 1) — organisational functions
```

---

## 12. Process Principles vs Business Rules — A Taxonomy of Constraints

### Current State

Every ontology in the library uses the same `businessRules` structure:

```json
{
  "@id": "prefix:BR-X-NNN",
  "condition": "IF ...",
  "action": "THEN ... MUST/SHOULD ...",
  "severity": "error|warning",
  "appliesTo": ["prefix:EntityA"]
}
```

The two severity levels conflate two very different things:

| Severity | Current Use | Actual Meaning |
|----------|-------------|----------------|
| `error` | Mandatory constraint — system MUST enforce | **Rule**: Violation = invalid data |
| `warning` | Advisory guidance — system SHOULD warn | Used for both **principles** AND **soft rules** |

### The Distinction

Consider three types of constraint in an organisational context:

| Type | Character | Enforcement | Scope | Example |
|------|-----------|-------------|-------|---------|
| **Business Rule** | Structural/data constraint | System-enforced, binary pass/fail | Per-entity or per-relationship | "Every process gate must have a threshold ≥ 0" (BR-PE-001) |
| **Process Principle** | Best-practice guidance derived from methodology | Advisory, professional judgement | Per-function or per-process-family | "Lean: eliminate the 8 wastes (DOWNTIME)" / "Six Sigma: decisions require statistical significance (p < 0.05)" |
| **Operating Standard** | Industry/regulatory norm that governs how a function operates | Compliance-auditable, may have legal weight | Per-function, per-industry, per-jurisdiction | "ISO 9001: documented procedures required for all quality-critical processes" / "SOX: financial controls must have segregation of duties" |

### Are Principles Functionally Aligned?

**Yes — and this is the critical insight that ties back to EFS-ONT.**

Principles naturally cluster by organisational function:

| Function | Governing Principles | Source Methodology |
|----------|---------------------|-------------------|
| **Operations** | Eliminate waste (Lean), reduce variation (Six Sigma), continuous improvement (Kaizen), theory of constraints (Goldratt) | Lean, Six Sigma, TOC |
| **Finance** | Segregation of duties, materiality, going concern, prudence, accruals matching | GAAP/IFRS, SOX |
| **Quality** | Prevention over detection, right-first-time, statistical process control, customer focus | ISO 9001, TQM |
| **Technology** | Separation of concerns, DRY, infrastructure as code, least privilege | TOGAF, ITIL, security frameworks |
| **HR** | Duty of care, equal opportunity, competency frameworks, succession planning | Employment law, CIPD |
| **Risk/Compliance** | Defence in depth, risk appetite alignment, proportionality, independence of assurance | ISO 31000, COSO, GRC-FW-ONT |
| **Marketing** | Customer-centricity, data-driven decisions, brand consistency, ethical marketing | CIM, AMA |
| **Sales** | Pipeline discipline, qualification criteria (BANT/MEDDIC), forecast accuracy | Sales methodology frameworks |

**This is where EFS-ONT gains another dimension**: if functions are first-class entities, each function can carry its governing principles as metadata — not as system-enforced rules, but as the body of professional knowledge that guides how the function should operate.

### Proposed Constraint Taxonomy

Instead of overloading `businessRules`, consider a three-tier structure:

```
constraintType: "rule"       → severity: error    → System-enforced. Violation = invalid.
constraintType: "standard"   → severity: error    → Compliance-auditable. Maps to GRC-FW-ONT requirement.
constraintType: "principle"  → severity: advisory → Best-practice guidance. Function-scoped. Not enforced.
```

Or more simply, add a `constraintType` property to the existing `businessRules` array:

```json
{
  "@id": "efs:PR-OPS-001",
  "name": "Lean Waste Elimination Principle",
  "constraintType": "principle",
  "condition": "IF function is Operations",
  "action": "THEN all processes SHOULD be evaluated for the 8 wastes (DOWNTIME)",
  "severity": "advisory",
  "appliesTo": ["efs:OrgFunction"],
  "functionalDomain": "Operations",
  "sourceMethodology": "Lean Manufacturing",
  "crossRef": "kdmaic:WasteCategory"
}
```

This keeps backward compatibility (still a `businessRules` entry) while adding the function-alignment and principle/rule/standard distinction.

### Impact on Existing Ontologies

- **DMAIC-ONT**: BR-D-004 ("p-value < 0.05 for validation") is actually a **principle** of statistical rigour, not a data constraint. It should be `constraintType: "principle"` with `sourceMethodology: "Six Sigma"`.
- **K-DMAIC-ONT**: BR-K-006 ("Team size 5-10 advisory") is a **principle** of Kaizen methodology. Same treatment.
- **GRC-FW-ONT**: Most rules here are **standards** (regulatory/compliance), distinct from both rules and principles.
- **PE-ONT**: BR-PE-003 ("mandatory artifact must be produced before phase completion") is a genuine **rule** — system-enforced, binary.

This taxonomy would be backward-compatible — existing `severity: "error"` rules stay as `constraintType: "rule"`, existing `severity: "warning"` rules get reviewed for whether they're principles or soft rules.

---

## 13. Join Pattern Visualisation — The Missing Canvas Layer

### Current State

The visualiser handles JPs at three levels, none of them visual:

| Layer | What Happens | Where | Visual? |
|-------|-------------|-------|---------|
| **Multi-loader** | `detectCrossReferences()` resolves Entry-ONT `keyBridges` and ontology `oaa:joinPatterns` into `crossEdge` objects | `multi-loader.js` L484-666 | Cross-edges rendered as dashed lines between ontology nodes |
| **EMC composer** | Instance-level `composedGraphSpec.joinPoints` resolved at runtime | `emc-composer.js` L643-663 | Merged into graph as `edgeType: 'crossOntology'` |
| **Cypher export** | `buildJoinPatternComments()` generates informational comments | `cypher-export.js` L268 | Text only, not visual |
| **Join Pattern Registry** | 91 JPs catalogued centrally | `join-pattern-registry.json` | **Not loaded by visualiser at all** |

**Key gaps**:
1. The central `join-pattern-registry.json` (91 JPs) is never loaded or rendered
2. JPs are shown as individual cross-edges but never as **pathways** — you can't see JP-D-001 "VOC-to-Sigma Pipeline" as a connected chain
3. The `joinPath` notation (`Phase -> invokesSkill -> Skill <- agentProvidesSkill <- Agent`) is human-readable but has no visual representation
4. PE-ONT's `ProcessPath`, `PathStep`, `PathLink` entities exist in the schema but have no dedicated renderer
5. Value chains (`pe:ValueChain`) render as ordinary nodes with no chain/flow layout

### What's Needed — Three Visualisation Modes

#### Mode 1: JP Pathway View (Cross-Ontology)

**Purpose**: Trace a specific JP as a highlighted pathway across the merged graph.

```
User selects JP-D-001 "VOC-to-Sigma Pipeline"

Highlighted pathway:
  SixSigmaProject ──hasSIPOC──> SIPOC
                                  ↑
                       ctqFromSIPOC
                                  |
                   CriticalToQuality ──validatedByMSA──> MeasurementSystem
                                                            |
                                                     msaForCapability
                                                            ↓
                                                    ProcessCapability

All other nodes dimmed to ghost. Pathway nodes enlarged.
Edge labels shown. JP metadata in sidebar.
```

**Implementation sketch**: Parse the `joinPath` string into a sequence of `[entity, relationship, entity, ...]` tuples. Highlight the matching nodes/edges in the existing graph. Everything else goes to `CONTEXT_OPACITY`.

#### Mode 2: Virtual Value Chain View (Data Flow)

**Purpose**: Show the input→transform→output chain across processes, using `ProcessArtifact` as the data flow medium.

```
[VOC Data]          [Baseline Report]        [RCA Report]          [Pilot Results]
    |                     |                       |                      |
    v                     v                       v                      v
 ┌─────────┐    ┌──────────────┐    ┌────────────────┐    ┌──────────────────┐
 │ DEFINE   │───>│   MEASURE    │───>│    ANALYZE      │───>│    IMPROVE       │──> ...
 │          │    │              │    │                 │    │                  │
 │ produces:│    │ produces:    │    │ produces:       │    │ produces:        │
 │ Charter  │    │ MSA Report   │    │ Statistical     │    │ Solution Design  │
 │ SIPOC    │    │ Capability   │    │   Analysis      │    │ Pilot Results    │
 └─────────┘    └──────────────┘    └────────────────┘    └──────────────────┘
```

This is a **swimlane or Sankey-like view** where:
- Phases are columns (left to right)
- Artifacts flow between phases via `produces`/`inputTo` relationships
- Gates sit between phases as checkpoints
- AI Agents appear as augmenting overlays

**Implementation sketch**: Use PE-ONT's `ProcessPath` → `PathStep` → `PathLink` chain to determine sequence. Render as a horizontal flow layout (not the default force-directed graph). Artifact edges get directional arrows.

#### Mode 3: Cross-Function Value Stream (EFS + PE + JP)

**Purpose**: If EFS-ONT is activated, show how value flows across organisational functions — the virtual value chain.

```
┌─────────────────────────────────────────────────────────────────────┐
│                    OPERATIONAL EXCELLENCE                           │
│                                                                     │
│  ┌──────────┐    JP-OFM-003     ┌──────────┐   JP-LSC-001          │
│  │PROCUREMENT│──────────────────>│WAREHOUSING│──────────────>        │
│  │ Function  │  Supply-Demand   │ Function  │  End-to-End    ┌─────┐│
│  │           │  Bridge          │           │  Journey       │QUAL.││
│  │ SOP-001   │                  │ SOP-WH-01 │                │     ││
│  │ SOP-002   │                  │ SOP-WH-02 │                │SOP- ││
│  └──────────┘                   └──────────┘                 │QA-01││
│       ↑                              ↑                       └─────┘│
│  [BSC: Internal     [BSC: Internal          [BSC: Customer]         │
│   Process]           Process]                                       │
└─────────────────────────────────────────────────────────────────────┘
         ↑ ownedByRole
         COO (RRR)
         ↑ accountableTo
         VSOM Strategy
```

This view would be **unique to the visualiser** — no standard tool shows the function→sub-function→process→JP pathway in a single canvas. It's the visual expression of the VE→EFS→PE skill chain.

### Canvas Tier Implications

Currently the visualiser has:
- **Tier 0**: Series rollup (VE, PE, RCSG, Foundation, Orchestration)
- **Tier 1**: Ontologies within a series (VP, RRR, KPI... within VE)
- **Tier 1.1**: Sub-series (VSOM-SA, VSOM-SC within VE)
- **Tier 2**: Entity graph within an ontology

JP/Value Chain visualisation would need either:
- A **Tier 2.5** (pathway overlay on the entity graph), or
- A **new view mode** (parallel to Connection Map) that renders the flow/chain layout

### Relationship to EFS-ONT

Without EFS-ONT, JP pathways can only be shown entity-to-entity across ontologies. **With EFS-ONT**, pathways can be grouped by function — "show me all JPs that cross through the Operations function" becomes a queryable, visualisable question.

---

## 14. Revised Open Questions for Discussion

### From Original Briefing (updated):

1. **Function taxonomy level**: Should EFS-ONT define a reference taxonomy of functions as enum values, or entirely instance-defined per PFI?

2. **SOP formality**: Should PE-ONT gain a formal `SOP` subtype of `Process`, or does the EFS bridge handle this?

3. **Maturity model**: CMMI 1-5 (matching EA-CORE) or custom? Per-dimension (people, process, technology, data, governance)?

4. **ORG-MAT absorption**: Should function-level maturity live in ORG-CONTEXT (extended) rather than EFS-ONT?

5. **BSC perspective mapping**: Static (ontology-defined) or dynamic (PFI instance-defined)?

6. **EMC composition impact**: New FUNCTIONAL category, or absorbed into OPERATIONAL?

### From Extended Discussion (new):

7. **K-DMAIC naming**: Rename to `DMAIC-KAIZEN-ONT` for clarity? Or introduce a DMAIC sub-series under PE?

8. **Ontology vs entity level naming convention**: Should the registry enforce a level taxonomy (`schema-ontology`, `domain-extension`, `pattern-template`, `instance-data`) to prevent level confusion on the canvas?

9. **Constraint taxonomy**: Add `constraintType` (rule/standard/principle) to `businessRules` as a backward-compatible extension? Or keep the current error/warning binary and accept that principles are just warnings?

10. **Principles as function metadata**: If EFS-ONT is activated, should each function carry its governing principles (Lean for Operations, GAAP for Finance, etc.) as a `governingPrinciples[]` array?

11. **JP Registry loading**: Should the visualiser load `join-pattern-registry.json` and render JPs as first-class navigable pathways? What canvas tier/view mode?

12. **Value chain renderer**: Should PE-ONT's `ProcessPath`/`PathStep`/`PathLink` entities get a dedicated horizontal-flow renderer (like a simplified BPMN swimlane), distinct from the force-directed entity graph?

13. **Cross-function value stream**: If both EFS-ONT and JP visualisation are activated, should the visualiser support a "virtual value chain" view that shows data/process flow across functions?

---

## 15. Prioritisation Matrix — Business Value, Currency, Blockers & Horizon

This matrix evaluates all 13 open questions plus the four major workstreams surfaced in this briefing against business value, current relevance (currency), what's blocking progress today, and a delivery horizon.

### Scoring Key

| Dimension | 5 (Highest) | 3 (Medium) | 1 (Lowest) |
|-----------|------------|-----------|------------|
| **Business Value** | Directly enables PFI revenue, agent deployment, or client modelling | Improves internal coherence, developer experience, or platform quality | Nice-to-have, cosmetic, or only relevant to edge cases |
| **Currency** | Blocking active work NOW (Epic 45/49/40, PFI onboarding) | Relevant to next-quarter plans (BAIV agents, W4M-WWG launch) | Future concern, no active dependency today |
| **Blocker Severity** | Cannot proceed with current work without this | Workarounds exist but they're fragile or manual | No active blocker, improvement only |

### Master Matrix

| # | Initiative | Business Value | Currency | Blocker Severity | Horizon | Dependencies |
|---|-----------|---------------|----------|-----------------|---------|-------------|
| **A** | **EFS-ONT Activation (thin bridge)** | **5** — PFI instances cannot model client org functions; agents have no functional target | **5** — BAIV needs function→agent mapping NOW; W4M-WWG needs function→SOP scoping | **4** — Workaround is manual tagging via RRR enum, but doesn't scale | **Now → 3 months** | Registry placeholder exists. Depends on Q1 (function taxonomy decision) |
| **B** | **Constraint Taxonomy (rule/standard/principle)** | **4** — Enables function-aligned principles; distinguishes compliance from guidance | **4** — Every new ontology (DMAIC, K-DMAIC, GRC) is already conflating rules and principles in `businessRules` | **3** — Current error/warning works but loses semantic meaning | **Now → 3 months** | Backward-compatible. No hard dependencies. OAA schema extension. |
| **C** | **JP Registry Loading in Visualiser** | **4** — 91 JPs exist but are invisible; cross-ontology navigation is blind | **4** — Multi-ontology comparison (Epic 3) and EMC composition both need JP awareness | **3** — JPs partially resolved via Entry-ONT bridges, but registry never loaded | **Now → 3 months** | Requires `join-pattern-registry.json` parser. No ontology changes needed. |
| **D** | **JP Pathway View (Mode 1)** | **4** — "Trace a join pattern" is a core graph-navigation use case | **3** — Useful but not blocking active epics | **2** — Individual cross-edges render today; pathway highlighting is UX improvement | **3-6 months** | Depends on C (registry loading). Uses existing `joinPath` notation. |
| **E** | **BSC↔Function Perspective Mapping** | **5** — BSC perspectives without functional alignment are strategic abstractions disconnected from operational reality | **4** — BSC-ONT exists but the 4+6 perspectives don't map to anything operational | **4** — Cannot answer "which functions contribute to the Financial perspective" | **Now → 3 months** | Depends on A (EFS-ONT). Relationship definition only, no new entities. |
| **F** | **K-DMAIC Naming / Sub-Series** | **2** — Cosmetic/clarity improvement. No functional impact on system behaviour. | **2** — Not blocking anything. Both ONTs are compliant and functional. | **1** — No blocker. Naming confusion is a human-readability issue only. | **Beyond 6 months** | Registry rename + directory rename. Breaking change for any existing refs. |
| **G** | **Ontology Level Taxonomy in Registry** | **3** — Prevents future confusion as library grows (52→60+ ontologies) | **3** — Becoming relevant as more domain-extension and pattern-template artefacts appear | **2** — No active blocker but DMAIC confusion is a symptom | **3-6 months** | OAA schema extension. Needs governance discussion. |
| **H** | **SOP Formalisation (PE-ONT extension or EFS bridge)** | **4** — SOPs are the atomic unit of operational compliance; currently untyped in PE | **4** — W4M-WWG has 82 OFM entities that are effectively SOPs without formal typing | **3** — SOPs work as generic `pe:Process` but lose versioning, approval, regulatory linkage | **Now → 3 months** | Can be done as PE-ONT v5.0.0 `processCategory: "sop"` enum or as EFS bridge pattern. Decision needed. |
| **I** | **Value Chain Renderer (Mode 2)** | **4** — Horizontal flow layout for processes is a natural complement to the force-directed graph | **3** — PE-ONT has ProcessPath/PathStep/PathLink but they're rendered as ordinary nodes | **2** — Not blocking. DMAIC template works in entity graph view. | **3-6 months** | vis-network custom renderer or separate layout mode. Significant dev effort. |
| **J** | **Cross-Function Value Stream (Mode 3)** | **5** — The "killer feature" — function→process→JP pathway in a single canvas is unique | **2** — Depends on EFS-ONT + JP visualisation both being in place | **1** — Multiple prerequisites not yet met | **Beyond 6 months** | Depends on A + C + D + I. Highest value but longest dependency chain. |
| **K** | **Function-Level Maturity Model** | **4** — Per-function maturity enables targeted agent deployment and improvement prioritisation | **3** — ORG-CONTEXT has org-wide maturity; function-level is a natural extension | **2** — Org-wide maturity works as interim; function-level is an enrichment | **3-6 months** | Depends on A (EFS-ONT). CMMI 1-5 scale or custom. |
| **L** | **Principles as Function Metadata** | **3** — Enriches EFS-ONT functions with governing methodology (Lean, GAAP, etc.) | **2** — Not blocking active work. Useful for client-facing reports and agent context. | **1** — No blocker. Principles can be documented externally. | **Beyond 6 months** | Depends on A (EFS-ONT) + B (constraint taxonomy). |
| **M** | **EMC Composition Category for EFS** | **3** — EMC needs to know about functions for composition filtering | **3** — 11 categories exist; FUNCTIONAL would be 12th | **2** — EMC works without it; functions would be manually included | **3-6 months** | Depends on A (EFS-ONT). `emc-composer.js` update. |

### Horizon Buckets

#### NOW (Deliver within current sprint / immediate action)

| # | Initiative | Action | Effort |
|---|-----------|--------|--------|
| **A** | EFS-ONT Activation | Design EFS-ONT v1.0.0 schema (5-7 entities, 8-12 relationships). Start with Operations function as worked example. Feature issue under Epic 34 or new epic. | Medium — 1 ontology file + registry update + Entry-ONT |
| **B** | Constraint Taxonomy | Add `constraintType` field to OAA schema definition. Retrospectively classify existing rules across all ontologies (error→rule, warning→review for principle/soft-rule). | Small — schema extension + audit pass |
| **C** | JP Registry Loading | Write parser for `join-pattern-registry.json`. Wire into multi-loader alongside existing `detectCrossReferences()`. Surface JP list in sidebar/panel. | Medium — new module + UI panel |
| **E** | BSC↔Function Mapping | Define `contributesToPerspective` relationship in EFS-ONT. Map all 10 BSC perspectives to primary/secondary functions. | Small — relationship + enum mapping in EFS-ONT |
| **H** | SOP Formalisation | Decision: PE-ONT enum extension (`processCategory: "sop"`) vs EFS bridge entity. Then implement chosen approach. | Small — enum addition OR bridge entity |

**Combined "Now" value**: Unblocks PFI function modelling, makes 91 JPs navigable, separates rules from principles, and connects BSC perspectives to operational reality.

#### 3 MONTHS (Next quarter)

| # | Initiative | Action | Effort |
|---|-----------|--------|--------|
| **D** | JP Pathway View | Parse `joinPath` notation into traversal tuples. Implement highlight mode on entity graph (dim non-pathway nodes to ghost). Sidebar shows JP metadata. | Medium — parser + renderer mode |
| **G** | Level Taxonomy in Registry | Define `artefactLevel` enum (schema-ontology, domain-extension, pattern-template, instance-data) in OAA. Apply to all 52+ entries. | Medium — schema + registry audit |
| **I** | Value Chain Renderer | Horizontal-flow layout mode for ProcessPath→PathStep→PathLink chains. Artifact edges as directional arrows. Gates as checkpoint markers. | Large — new layout engine |
| **K** | Function-Level Maturity | Define maturity dimensions per function (people, process, technology, data, governance). CMMI 1-5 scale. Wire into ORG-CONTEXT or EFS-ONT. | Medium — entity design + instance data |
| **M** | EMC Composition Category | Add FUNCTIONAL category to `CATEGORY_COMPOSITIONS`. Update `constrainToInstanceOntologies()` to handle EFS-ONT. | Small — composer code update |

**Combined "3-month" value**: The visualiser gains pathway navigation, value chain flow rendering, and function-level maturity. The registry gets structurally cleaner.

#### BEYOND 6 MONTHS (Strategic roadmap)

| # | Initiative | Action | Effort |
|---|-----------|--------|--------|
| **F** | K-DMAIC Naming | Rename to `DMAIC-KAIZEN-ONT`. Consider DMAIC sub-series. Registry + directory + all references. | Small effort but breaking change |
| **J** | Cross-Function Value Stream | Combine EFS functions + JP pathways + value chain renderer into unified canvas mode. The "killer view". | Large — depends on A+C+D+I all complete |
| **L** | Principles as Function Metadata | Add `governingPrinciples[]` to EFS `OrgFunction`. Populate per-function principle sets. Wire to agent context for methodology-aware prompting. | Medium — schema + instance data + agent integration |

**Combined "Beyond" value**: The full vision — a navigable, function-scoped, principle-governed, JP-traced value stream canvas that no other tool provides.

### Critical Path

```
NOW                          3 MONTHS                      BEYOND
─────────────────────────────────────────────────────────────────────

A: EFS-ONT ──────────────┬── E: BSC↔Function ──┐
                         │                      │
B: Constraint Taxonomy ──┤── K: Function Maturity│── L: Principles
                         │                      │
C: JP Registry ──────────┤── D: JP Pathway ─────┤── J: Cross-Function
                         │                      │      Value Stream
H: SOP Formalisation ────┤── I: Value Chain ────┘
                         │
                         ├── G: Level Taxonomy
                         │
                         └── M: EMC Category

F: K-DMAIC Naming ─────────────────────────────────── (independent, low priority)
```

### Blocker Heat Map — What's Stuck Without Action?

| If We DON'T Do... | What Gets Stuck |
|-------------------|----------------|
| **A (EFS-ONT)** | E, K, L, J, M all blocked. PFI instances can't model client functions. Agent targeting remains role-only. BSC perspectives float disconnected. |
| **C (JP Registry)** | D, J blocked. 91 JPs remain invisible documentation. Cross-ontology navigation stays blind. |
| **B (Constraint Taxonomy)** | L partially blocked. Every new ontology continues conflating rules and principles. GRC standards indistinguishable from business rules. |
| **H (SOP Formalisation)** | W4M-WWG SOPs remain untyped generic processes. Compliance tracking has no formal anchor. |
| **E (BSC↔Function)** | BSC perspectives remain strategic abstractions with no operational grounding. The "Internal Process" perspective can't answer "which functions and SOPs contribute to it?" |

### Decision Points Required

| Decision | Impacts | Options | Recommendation |
|----------|---------|---------|----------------|
| **Function taxonomy: enum or instance-defined?** | A, E, K | (a) Reference enum of ~14 functions matching RRR's existing `function` enum (b) Entirely PFI-defined | **(a)** — Reference enum provides consistency; PFI instances can extend but not redefine core functions |
| **SOP: PE extension or EFS bridge?** | H, A | (a) `processCategory: "sop"` enum on `pe:Process` (b) `efs:FunctionProcess` bridge entity with SOP metadata | **(a) + (b)** — Enum for typing, bridge for function-scoping and compliance metadata. Both are small, complementary. |
| **Maturity: EFS-ONT or ORG-CONTEXT extension?** | K, 4 | (a) `efs:FunctionMaturity` entity in EFS-ONT (b) Extend ORG-CONTEXT-ONT with per-function maturity dimensions | **(a)** — Keep EFS-ONT self-contained. ORG-CONTEXT is org-wide by design; function maturity belongs with the function. |
| **JP view mode: overlay or new canvas?** | D, I, J | (a) Pathway overlay on existing Tier 2 graph (b) New dedicated view mode (c) Both | **(a) for D, (b) for I and J** — Pathway highlighting is an overlay; value chain needs its own layout mode |

---

## 16. The Strategist's Lens — VALUE, COMMUNICATION, COHERENCE

Section 15 evaluates through an engineering lens: blockers, dependencies, effort. A Lean Six Sigma Black Belt and strategist sees differently. Three things matter above all else:

- **VALUE**: Does it create, protect, or enable value? If not, it's waste.
- **COMMUNICATION**: Can the person who needs to act on this understand it instantly? If you can't see it, it doesn't exist. If you can't explain it, you don't own it.
- **COHERENCE**: Does it hold together? Does A lead to B lead to C without contradiction, duplication, or orphaned meaning? Incoherence is the silent killer of strategy execution.

These three are not independent — they form a reinforcing triangle:

```
        VALUE
       /     \
      /       \
     /   The    \
    /  Triangle  \
   /    that      \
  /    matters     \
 /                  \
COMMUNICATION ─── COHERENCE
```

Value without communication is hidden. Communication without coherence is noise. Coherence without value is academic.

### Revaluation Through the Triangle

| # | Initiative | VALUE | COMMUNICATION | COHERENCE | Triangle Score | Engineering Score (S15) | Delta |
|---|-----------|-------|---------------|-----------|---------------|------------------------|-------|
| **A** | EFS-ONT Activation | **5** — Enables PFI client function modelling = direct revenue path | **4** — Functions are the language executives speak; agents mapped to functions communicates instantly | **5** — Fills the VE↔PE gap; without it the ontological system has a hole in the middle | **14** | 14 | 0 |
| **C** | JP Registry Loading | **3** — JPs exist; loading them doesn't create new value, it reveals existing value | **5** — **91 join patterns are invisible. If you can't see it, it doesn't exist.** This is the single biggest communication failure in the platform. | **4** — JPs are the glue between ontologies; making them visible makes the system's coherence visible | **12** | 11 | **+1** |
| **D** | JP Pathway View | **4** — Traced pathways show HOW value flows, not just THAT things are connected | **5** — A highlighted pathway through the graph communicates a complete story in one glance. This is where "lost in translation" gets solved. | **4** — Pathways demonstrate that the ontological joins actually work end-to-end | **13** | 9 | **+4** |
| **I** | Value Chain Renderer | **5** — Input→Transform→Output IS the value chain. Rendering it IS rendering value. | **5** — Horizontal flow is how humans naturally read process: left to right, cause to effect. Force-directed graphs communicate structure, not flow. | **4** — Aligns PE-ONT's ProcessPath data with a visual form that matches its semantic intent | **14** | 9 | **+5** |
| **J** | Cross-Function Value Stream | **5** — The complete picture: function→process→JP→output across org boundaries | **5** — No other tool shows this. A CEO, COO, or Black Belt can look at one canvas and see: "Here's how value flows through my organisation." | **5** — The ultimate coherence test: if it renders cleanly, the ontological system works. If it doesn't, something is broken. | **15** | 8 | **+7** |
| **E** | BSC↔Function Mapping | **5** — BSC without functional grounding is a spreadsheet exercise, not strategy | **5** — "Which functions contribute to the Financial perspective?" is the question every board meeting asks. The fact that we can't answer it ontologically is a communication gap. | **5** — BSC perspectives are the strategic lens; functions are the operational lens. Connecting them is what coherence means. | **15** | 13 | **+2** |
| **B** | Constraint Taxonomy | **3** — Distinguishing rules from principles doesn't directly create value | **4** — When a principle is presented as a rule, people either over-comply (waste) or ignore all rules (risk). Clarity of constraint type communicates expectations. | **5** — Conflating rules, standards, and principles is a fundamental incoherence in the ontological system. Every new ontology inherits this confusion. | **12** | 11 | **+1** |
| **H** | SOP Formalisation | **4** — SOPs are where value is created or destroyed at the operational level | **3** — SOPs typed as generic processes communicates "we don't distinguish execution from design". Typing them properly communicates "we take operations seriously". | **4** — PE-ONT handles process schema; SOPs need operational identity. Without it, the same entity type means everything from "strategic initiative" to "warehouse receiving checklist". | **11** | 11 | 0 |
| **K** | Function-Level Maturity | **4** — Maturity directly informs where to invest, where to automate, where to leave alone | **4** — "Your Procurement function is at Level 4 but Quality is at Level 2" communicates a story that "your org is at Level 3" cannot. | **3** — Enrichment, not structural. Coherence isn't broken without it. | **11** | 9 | **+2** |
| **G** | Level Taxonomy in Registry | **2** — Doesn't create value; prevents future confusion | **4** — When someone asks "is DMAIC an ontology or a pattern?" and the answer is "both, at different levels" — that's a communication failure. Taxonomy fixes it. | **4** — Level confusion is an incoherence, but it's in the library metadata, not in the data model itself. | **10** | 8 | **+2** |
| **M** | EMC Composition Category | **3** — EMC needs to know about functions for filtering | **2** — Internal composer concern. Doesn't affect what users see. | **3** — Alignment improvement. EMC works without it. | **8** | 8 | 0 |
| **F** | K-DMAIC Naming | **1** — Zero value creation. Naming exercise. | **3** — "K-" is ambiguous. But people who use K-DMAIC already know what it means. | **2** — Cosmetic incoherence. | **6** | 5 | **+1** |
| **L** | Principles as Function Metadata | **4** — Agents that know "this is an Operations function, apply Lean principles" create better recommendations | **3** — Useful but not urgent. Principles can be communicated externally. | **3** — Enrichment. Nice-to-have. | **10** | 6 | **+4** |

### What Changes When You Think Like a Strategist

The engineering matrix (Section 15) says: "Do A first, then C, then B, because of dependency chains."

The strategist's triangle says something different:

**The visualisation items (C, D, I, J) all jump significantly.** Because:

> **If you can't see the join patterns, the value chains, and the function-to-process flow, then the entire ontological system is an invisible machine. It might be perfectly coherent inside the JSON files, but coherence that can't be communicated is coherence that doesn't exist.**

This is the Lean principle: **make the invisible visible**. Toyota didn't invent kanban because they needed another tracking system. They invented it because **visibility IS the control mechanism**. An andon cord doesn't fix the problem — it communicates that there IS a problem.

The same applies here:
- JP pathway view doesn't create new join patterns — it makes existing ones **visible**
- Value chain renderer doesn't create new process flows — it makes existing data **readable as flow**
- Cross-function value stream doesn't create new relationships — it makes the complete picture **comprehensible in one view**

### Revised Priority Order (Strategist's View)

Sorting by Triangle Score:

| Rank | # | Initiative | Triangle | Horizon (unchanged) |
|------|---|-----------|----------|-------------------|
| **1=** | **J** | Cross-Function Value Stream | **15** | Beyond (dependency chain) |
| **1=** | **E** | BSC↔Function Mapping | **15** | Now (depends on A only) |
| **3=** | **A** | EFS-ONT Activation | **14** | Now |
| **3=** | **I** | Value Chain Renderer | **14** | 3 months |
| **5** | **D** | JP Pathway View | **13** | 3 months |
| **6=** | **C** | JP Registry Loading | **12** | Now |
| **6=** | **B** | Constraint Taxonomy | **12** | Now |
| **8=** | **H** | SOP Formalisation | **11** | Now |
| **8=** | **K** | Function-Level Maturity | **11** | 3 months |
| **10=** | **G** | Level Taxonomy | **10** | 3 months |
| **10=** | **L** | Principles as Function Metadata | **10** | Beyond |
| **12** | **M** | EMC Composition Category | **8** | 3 months |
| **13** | **F** | K-DMAIC Naming | **6** | Beyond |

### The Tension and The Resolution

The strategist ranks J (Cross-Function Value Stream) joint first. The engineer ranks it last because of dependencies.

**This is not a contradiction.** It means: **J defines the destination. Everything else is the path.**

The resolution is to treat J as the **North Star** and evaluate every "Now" and "3 month" item by asking: **"Does this get us closer to the Cross-Function Value Stream canvas?"**

- A (EFS-ONT): Yes — functions are the swim lanes.
- C (JP Registry): Yes — JPs are the connecting lines.
- E (BSC↔Function): Yes — perspectives are the strategic overlay.
- D (JP Pathway): Yes — pathway tracing is the navigation model.
- I (Value Chain): Yes — horizontal flow is the rendering paradigm.
- B (Constraint Taxonomy): Partially — principles enrich the function lanes.
- H (SOP Formalisation): Yes — SOPs are the atomic content inside function lanes.

**Everything that scores high on the Triangle and is on the critical path to J should be prioritised.** Everything else can wait.

### The Lean Test: What Would a Black Belt Cut?

A Six Sigma Black Belt applying DMAIC to this briefing would ask: "What's the vital few?"

Pareto principle: **80% of the value comes from 20% of the initiatives.**

13 initiatives. 20% = 2-3 items.

The vital few:

| # | Initiative | Why It's Vital |
|---|-----------|----------------|
| **A** | EFS-ONT | Unblocks 5 other items. Without functions, there's no organisational scaffolding. Everything downstream (BSC mapping, maturity, value streams, agent targeting) depends on this. |
| **C+D** | JP Registry + Pathway View | Treat as one initiative. Loading without visualising is waste. Visualising without loading is impossible. Together they make the ontological glue visible. |
| **I** | Value Chain Renderer | The rendering paradigm that makes processes readable as flow. Without it, value chains are force-directed soup. |

**Three initiatives. Everything else either follows from these three or can wait.**

A (functions exist) + C/D (joins are visible) + I (flow is readable) = the foundation for J (the complete value stream canvas) and E (BSC↔Function, which is just a relationship on top of A).

### The Strategist's "Now" List (Revised)

| Priority | Initiative | Strategic Rationale |
|----------|-----------|-------------------|
| **1** | **A: EFS-ONT** | The foundation. Functions are how organisations think about themselves. Everything else hangs off this. |
| **2** | **C: JP Registry Loading** | Make the invisible visible. 91 join patterns exist. Load them. |
| **3** | **E: BSC↔Function** | Connect strategy to operations. This is a relationship definition on top of A — small effort, massive communication value. |
| **4** | **H: SOP Formalisation** | Type the atomic units properly. SOPs are where the rubber meets the road. |
| **5** | **B: Constraint Taxonomy** | Clean the foundations. Stop conflating rules and principles before the library grows further. |

### The Strategist's "3 Month" List (Revised)

| Priority | Initiative | Strategic Rationale |
|----------|-----------|-------------------|
| **1** | **D: JP Pathway View** | C loaded the data. Now light it up. One click, one pathway, one story. |
| **2** | **I: Value Chain Renderer** | The rendering paradigm shift. Horizontal flow. Input→Transform→Output. This is how value is communicated. |
| **3** | **K: Function-Level Maturity** | "Where are we strong? Where are we weak? Where do agents go?" Per-function maturity answers all three. |

### The North Star

> **A CEO should be able to open the visualiser, select their PFI instance, and see — in one canvas — how value flows through their organisation's functions, which processes create that value, which join patterns connect those processes across domains, and where AI agents are augmenting the work. That canvas doesn't exist yet. Everything in this briefing is about building it.**

That's J. That's the destination. VALUE, COMMUNICATION, COHERENCE — all three converge in one view.

---

---

## 17. CQ-Driven Concept Classification Framework

### The Meta-Question

Sections 1-16 ask "should we activate EFS-ONT?" Section 11 asks "how do we name things at different levels?" These are specific instances of a general question that arises every time a new concept enters PF-Core:

> **Given a new analytical method, framework, process, or domain concept — what is the correct ontological route: standalone ontology, join pattern, skill, template, version bump, or instance data?**

This section formalises that decision as a **CQ-driven logic tree** — a reusable classification framework where OAA Competency Questions serve as the evaluation criteria at each decision node.

### The Classification Logic Tree

```
NEW CONCEPT ASSESSMENT
══════════════════════

Q1: ENTITY COUNT — Does it introduce ≥5 genuinely NEW entity types?
│
├─ YES ──────────────────────────────── → Q2 (Ontology candidate)
│
└─ NO (<5 unique entities) ─────────── → Q6 (Sub-ontology route)


Q2: VOCABULARY SCOPE — Is the vocabulary universal or instance-specific?
│
├─ UNIVERSAL (method/framework)  → Core ontology at PFC level → Q3
├─ INSTANCE-SPECIFIC (data only) → PFI instance entries in existing ONT → STOP
└─ SPLIT (method=PFC, data=PFI)  → Ontology definition + PFI instances → Q3


Q3: SERIES PLACEMENT — Which series does it naturally belong to?
│
├─ Value / Strategy / Market     → VE-Series
├─ Process / Execution / Ops     → PE-Series
├─ Governance / Risk / Compliance→ RCSG-Series
├─ Organisational / Context      → Foundation
├─ Orchestration / Composition   → Orchestration/
└─ None — crosses 3+ series      → Q3b: Sub-series or standalone?
   ├─ Groups with 2+ related ONTs → New sub-series
   └─ Standalone cross-cutting    → Orchestration/ or Foundation


Q4: LINEAGE POSITION — Does it sit in an existing VE chain?
│
├─ YES (e.g., between VP and PMF)
│   └─ Insert in lineage, define upstream JP + downstream JP
│   └─ Chain becomes: ...→ VP → [NEW] → PMF → ...
│
└─ NO (parallel or orthogonal)
    └─ New branch with JPs to connection points


Q5: PROCESS / SKILL TEST — Is it also a repeatable analytical process?
│
├─ YES → Define as pe:Process with phases + register pe:Skill(s)
│   ├─ Skill type: analysis | transformation | validation
│   ├─ Provider: agent | human | hybrid
│   └─ Cross-series invocable via pe:invokesSkill
│
└─ NO (static classification only) → Ontology only, no PE bridge


══════════════════════════════════════════════════════════════

Q6: SUB-ONTOLOGY ROUTES (when <5 unique entities)
│
├─ Q6a: Does it define relationships between 2+ existing ONTs?
│   └─ YES → JOIN PATTERN (JP-XXX-NNN)
│       ├─ Define bridgeEntities, cardinality, relationship predicates
│       └─ Register in join-pattern-registry.json
│
├─ Q6b: Is it a reusable analytical VIEW across existing data?
│   └─ YES → EMC COMPOSITION
│       ├─ New ComposedGraphSpec (view definition)
│       ├─ Optionally freeze as CanonicalSnapshot (template)
│       └─ Add to EMC CATEGORY_COMPOSITIONS if new scope
│
├─ Q6c: Is it an extension of ONE existing ontology?
│   └─ YES → VERSION BUMP of that ontology
│       ├─ CQ-FIT: Do all proposed entities share the same namespace concern?
│       ├─ CQ-COH: Would adding these entities violate single-responsibility?
│       └─ Minor or major semver bump
│
├─ Q6d: Is it a repeatable workflow but not a domain?
│   └─ YES → pe:Skill + pe:ProcessPath (no new ontology)
│       ├─ Register skill with owningOntology reference
│       └─ Define ProcessPath for cross-series traversal
│
└─ Q6e: Is it instance-specific configuration?
    └─ YES → GraphScopeRule in EMC InstanceConfiguration
        ├─ ScopeCondition (when to activate)
        └─ ScopeAction (what data to include/exclude)
```

### CQs as Decision Node Criteria

Every node in the logic tree maps to one or more Competency Questions. This makes the classification **auditable and repeatable**:

| Decision Node | Competency Question | Evaluation |
|---|---|---|
| **Q1: Entity count** | CQ-VOC: "What terms does this concept introduce that have no equivalent in any existing ontology namespace?" | Count genuinely novel entities. ≥5 = ontology candidate |
| **Q1: Entity count** | CQ-COV: "For each proposed entity, does an existing entity already cover this semantic space?" | High overlap → consider version bump (Q6c) instead |
| **Q2: Scope** | CQ-UNI: "Is this vocabulary applicable to ALL PFI instances or only specific ones?" | Universal method = PFC. Instance data = PFI. |
| **Q2: Scope** | CQ-SPL: "Can the METHOD be separated from the DATA?" | If yes → split model (ontology def + PFI instances) |
| **Q3: Series** | CQ-SER: "What is the PRIMARY concern — value/market, process/execution, governance/risk, or organisational structure?" | Maps to series placement |
| **Q3: Series** | CQ-BRG: "Does it BRIDGE two series rather than belonging to one?" | Bridge pattern = PE-Series with VE imports (cf. EFS-ONT, LSC-ONT, OFM-ONT) |
| **Q4: Lineage** | CQ-LIN: "Does concept X consume outputs from ontology A and produce inputs for ontology B?" | If yes → insert in lineage chain |
| **Q4: Lineage** | CQ-CHN: "Where in the VE chain does the value transformation occur?" | Position: VSOM→OKR→VP→[?]→PMF→EFS→PE |
| **Q5: Process** | CQ-REP: "Is this a repeatable analytical method with defined inputs and outputs?" | Repeatable = skill candidate |
| **Q5: Process** | CQ-AGT: "Can an agent execute this, or is it inherently human judgement?" | Agent-capable → pe:Skill with provider |
| **Q6a: Cross-ONT** | CQ-BRI: "Does it define NEW relationship types between entities in 2+ existing ontologies?" | Novel cross-ONT relationships = Join Pattern |
| **Q6b: View** | CQ-VEW: "Is it a PROJECTION of existing data through a particular lens?" | Projection = ComposedGraphSpec |
| **Q6c: Extension** | CQ-FIT: "Does every proposed entity share the same namespace concern as ONT-X?" | Same concern + cohesive = version bump |

### The Classification Skill — Meta-Process

The logic tree IS a repeatable analytical process. Applying the tree to itself:

| Test | Answer |
|---|---|
| Q1: ≥5 unique entities? | **No** — the logic tree doesn't introduce entities, it's a decision process |
| Q6d: Repeatable workflow? | **Yes** — invoked every time a new concept enters PF-Core |
| → Route | **pe:Skill** — `emc:classifyNewConcept` |

**Skill definition:**

```
pe:Skill {
  skillId:          "SKILL-EMC-CLASSIFY-001"
  skillName:        "Classify New Concept"
  description:      "Evaluates a proposed concept against OAA CQs to determine
                     its modelling route: ontology, JP, skill, template,
                     extension, or instance data"
  skillType:        "analysis"
  owningOntology:   "EMC-ONT"
  provider:         "hybrid"
  version:          "1.0.0"

  inputs: [
    { name: "conceptName",        dataType: "string",   required: true  },
    { name: "proposedEntities",   dataType: "array",    required: true  },
    { name: "touchedOntologies",  dataType: "array",    required: true  },
    { name: "isRepeatable",       dataType: "boolean",  required: true  },
    { name: "isAgentCapable",     dataType: "boolean",  required: false },
    { name: "proposedByInstance", dataType: "string",   required: false }
  ]

  outputs: [
    { name: "route",              dataType: "enum" },
    { name: "seriesPlacement",    dataType: "string"  },
    { name: "lineagePosition",    dataType: "string"  },
    { name: "cqEvidenceMap",      dataType: "object"  },
    { name: "confidenceScore",    dataType: "number"  }
  ]
}
```

**Route enum values:** `ontology` | `join-pattern` | `skill` | `composed-graph-spec` | `canonical-snapshot` | `version-bump` | `graph-scope-rule` | `process-path`

### Where Each Output Lives

| Route Output | What Gets Created | Where It's Registered |
|---|---|---|
| `ontology` | New .jsonld file + Entry-ONT | ont-registry-index.json |
| `join-pattern` | JP definition | join-pattern-registry.json |
| `skill` | pe:Skill entity | PE-ONT skill registry (owningOntology ref) |
| `composed-graph-spec` | View definition | EMC-ONT instance data |
| `canonical-snapshot` | Frozen template | EMC-ONT instance data |
| `version-bump` | Extended entities in existing ONT | ont-registry-index.json (version update) |
| `graph-scope-rule` | IF-THEN rule | EMC InstanceConfiguration |
| `process-path` | PathStep + PathLink chain | PE-ONT instance data |

### EFS-ONT Through the Logic Tree (Retrospective)

Applying the classification to EFS-ONT itself validates the framework:

| CQ | EFS-ONT Evidence | Score |
|---|---|---|
| CQ-VOC (novel terms) | OrgFunction, SubFunction, FunctionProcess, FunctionMaturity, FunctionBudget | **5 unique** |
| CQ-COV (overlap) | `rrr:functionalDomain` exists as flat enum — but no structured entity | **Medium overlap, insufficient** |
| CQ-UNI (universal?) | Structure is PFC (every org has functions). Actual functions are PFI (each client is different). | **Split** |
| CQ-SER (series) | Operationally focused but bridges VE↔PE | **PE-Series (bridge)** |
| CQ-BRG (bridge?) | **Yes** — touches RRR, PE, EA-CORE, BSC, KPI, ORG-CONTEXT (6 ontologies) | **Bridge ontology** |
| CQ-LIN (lineage) | RRR → **EFS** → PE (roles own functions, functions scope processes) | **Insert in chain** |
| CQ-REP (process?) | **No** — EFS is structural, not processual | **Ontology only** |
| **Route** | **ontology** (PE-Series thin bridge) | Matches Section 9 recommendation |

The logic tree produces the same answer that sections 1-10 reached through narrative analysis — but in a fraction of the time, with auditable CQ evidence.

---

## 18. Methodology Taxonomy & Tagging System

### The Problem

PF-Core already models numerous methodologies (BSC, DMAIC, Kaizen, PESTEL, MECE, Logic Trees, Porter's, SWOT, Ansoff, Scenario Planning, Backcasting, Futures Funnel) — but there is no **explicit taxonomy for classifying methodologies as a category**. Each was placed ad hoc. The CQ-driven logic tree (Section 17) provides the decision mechanism; this section provides the **tagging vocabulary**.

### Current Methodology Coverage Map

```
TIER 1: FULL DEDICATED ONTOLOGY (own namespace, entities, CQs)
═══════════════════════════════════════════════════════════════
  ✅ BSC-ONT           Balanced Scorecard           VE-Series/VSOM-SA
  ✅ DMAIC-ONT         Six Sigma DMAIC              PE-Series
  ✅ K-DMAIC-ONT       Kaizen/Lean DMAIC            PE-Series
  ✅ MACRO-ONT         PESTEL + Scenario Planning    VE-Series/VSOM-SA
  ✅ REASON-ONT        MECE + Logic Trees            VE-Series/VSOM-SA
  ✅ INDUSTRY-ONT      Porter's + SWOT + Ansoff      VE-Series/VSOM-SA

TIER 2: EMBEDDED IN ANOTHER ONTOLOGY (entities within a host)
═══════════════════════════════════════════════════════════════
  ✅ OKR                → implied in VE lineage (VSOM→OKR→VP)
  ✅ Value Stream Map    → entity in K-DMAIC-ONT
  ✅ SWOT/TOWS          → entities in INDUSTRY-ONT
  ✅ Ansoff Matrix       → entities in INDUSTRY-ONT
  ✅ Futures Funnel      → entity in MACRO-ONT
  ✅ Backcasting         → entity in MACRO-ONT

TIER 3: REUSABLE PATTERN (pe:ProcessPattern or pe:Skill)
═══════════════════════════════════════════════════════════════
  ✅ ProcessPattern      → PE-ONT entity (e.g., PATTERN-AGILE-SPRINT)
  ✅ Hypothesis Testing  → REASON-ONT StrategicHypothesis
  ⬜ Pareto Analysis     → NOT YET — would be pe:Skill
  ⬜ Fishbone/Ishikawa   → NOT YET — would be pe:Skill
  ⬜ 5 Whys              → NOT YET — would be pe:Skill

TIER 4: NOT YET IN PF-CORE
═══════════════════════════════════════════════════════════════
  ⬜ Kano Analysis       → Candidate (VE-Series ontology)
  ⬜ Blue Ocean Strategy → Candidate (VE-Series/VSOM-SA ontology)
  ⬜ Jobs To Be Done     → Candidate (VP-ONT version bump)
  ⬜ Design Thinking     → Candidate (PE-Series process)
  ⬜ Theory of Constraints→ Candidate (PE-Series)
  ⬜ Wardley Mapping     → Candidate (VE-Series/VSOM-SA)
```

### The Four Classification Axes

Every methodology entering PF-Core should be tagged across four dimensions:

#### Axis 1: Granularity Tier

```
METHODOLOGY          A named body of knowledge with phases, tools, artefacts
  │                  (e.g., Lean Six Sigma, Blue Ocean Strategy)
  │
  ├─ PROCESS         A structured workflow from that methodology
  │                  (e.g., DMAIC from L6S, Four Actions from Blue Ocean)
  │                  → maps to pe:Process
  │
  ├─ TECHNIQUE       A specific analytical method
  │                  (e.g., Pareto Analysis, Strategy Canvas, Kano Survey)
  │                  → maps to pe:Skill
  │
  └─ TOOL/ARTEFACT   A specific output or instrument
                     (e.g., Control Chart, Value Curve, SWOT Matrix)
                     → maps to pe:ProcessArtifact
```

#### Axis 2: Domain Concern (maps to Series)

| Domain | Series | Examples |
|---|---|---|
| Value / Strategy / Market | VE-Series | Blue Ocean, Kano, BSC, Porter's |
| Process / Operations / Quality | PE-Series | L6S, DMAIC, Kaizen, ToC |
| Governance / Risk / Compliance | RCSG-Series | COBIT, ITIL, ISO 27001 |
| Organisation / Structure | Foundation | RACI (already in RRR), Org Design |
| Reasoning / Decision Science | VE-Series/VSOM-SA | MECE, Logic Trees, Hypothesis |

#### Axis 3: PF-Core Route (from Section 17 logic tree)

| Route | When | Tag |
|---|---|---|
| `route:ontology` | ≥5 unique entities, distinct domain | New ONT file |
| `route:ont-extension` | <5 entities, fits existing ONT | Version bump |
| `route:skill` | Repeatable technique, defined I/O | pe:Skill registration |
| `route:process-pattern` | Reusable workflow template | pe:ProcessPattern |
| `route:join-pattern` | Cross-ONT relationship definition | JP in registry |
| `route:composed-view` | Projection through analytical lens | EMC ComposedGraphSpec |

#### Axis 4: Agent Capability

| Level | Meaning | Tag |
|---|---|---|
| `agent:full` | Agent can execute end-to-end | Fully automatable |
| `agent:hybrid` | Agent assists, human decides | Most analytical methods |
| `agent:human` | Inherently requires human judgement | Workshops, Gemba walks |
| `agent:orchestrator` | Agent sequences other skills | Multi-step analyses |

### Cross-Reference: Existing Methodologies Classified

| Methodology | Granularity | Domain | Route | Agent | Current Home |
|---|---|---|---|---|---|
| Balanced Scorecard | Methodology | Value/Strategy | ontology | hybrid | BSC-ONT |
| Six Sigma DMAIC | Methodology | Process/Quality | ontology | hybrid | DMAIC-ONT |
| Kaizen DMAIC | Methodology | Process/Quality | ontology | hybrid | K-DMAIC-ONT |
| PESTEL | Methodology | Value/Strategy | ontology | hybrid | MACRO-ONT |
| Scenario Planning | Methodology | Value/Strategy | ontology | hybrid | MACRO-ONT |
| MECE Trees | Methodology | Reasoning | ontology | agent | REASON-ONT |
| Logic Trees | Methodology | Reasoning | ontology | agent | REASON-ONT |
| Porter's Five Forces | Process | Value/Strategy | ont-extension | hybrid | INDUSTRY-ONT |
| SWOT/TOWS | Process | Value/Strategy | ont-extension | hybrid | INDUSTRY-ONT |
| Ansoff Matrix | Technique | Value/Strategy | ont-extension | hybrid | INDUSTRY-ONT |
| Value Stream Map | Technique | Process/Quality | ont-extension | hybrid | K-DMAIC-ONT entity |
| ProcessPattern | Template | Process | process-pattern | agent | PE-ONT entity |
| Hypothesis Testing | Technique | Reasoning | ont-extension | hybrid | REASON-ONT entity |

---

## 19. Kano Analysis — Worked Classification Example

### What Is Kano Analysis?

The Kano Model (Noriaki Kano, 1984) classifies product/service attributes into satisfaction categories based on the non-linear relationship between feature implementation and customer satisfaction:

| Category | If Present | If Absent | Satisfaction Curve |
|---|---|---|---|
| **Must-be (Basic)** | No satisfaction increase | Strong dissatisfaction | Asymptotic floor |
| **Performance (One-dimensional)** | Linear satisfaction increase | Linear dissatisfaction | Diagonal |
| **Attractive (Delighter)** | Strong satisfaction increase | No dissatisfaction | Asymptotic ceiling |
| **Indifferent** | No impact | No impact | Flat |
| **Reverse** | Dissatisfaction | Satisfaction | Inverted diagonal |

**Data required**: Customer surveys using functional/dysfunctional question pairs, feature lists, satisfaction scoring.

**Output**: Feature classification matrix, priority ranking, investment allocation guidance, temporal decay tracking.

### CQ Evaluation

| CQ | Evidence | Score |
|---|---|---|
| **CQ-VOC** (novel terms) | KanoCategory (5-type enum), KanoSurvey, KanoQuestion (functional/dysfunctional pair), SatisfactionCurve, KanoDecay, KanoClassification, FeaturePriority | **7 unique entities** |
| **CQ-COV** (overlap) | `pmf:CustomerFeedback` covers satisfaction but NOT category classification. `vp:SuccessMetric` measures but doesn't classify by satisfaction curve. No Kano-specific semantics exist anywhere. | **Low overlap** |
| **CQ-UNI** (universal?) | The Kano METHOD (categories, survey structure, decay model) is universal — applicable to any product. The Kano DATA (which features classified how) is per-PFI instance. | **Split** |
| **CQ-SER** (series) | Customer satisfaction analysis → value/market concern → **VE-Series** | **VE-Series** |
| **CQ-BRG** (bridge?) | No — sits within VE chain, not between series | **Not a bridge** |
| **CQ-LIN** (lineage) | Consumes VP outputs (features, benefits, gains). Produces PMF inputs (validation signals, willingness-to-pay evidence). | **VP → KANO → PMF** |
| **CQ-CHN** (chain position) | VP defines what the product offers. Kano classifies how customers feel about it. PMF validates market fit using those classifications. | **Between VP and PMF** |
| **CQ-REP** (repeatable?) | Yes — 4-phase process: Survey Design → Data Collection → Classification → Decay Assessment | **Yes** |
| **CQ-AGT** (agent-capable?) | Agent can design surveys, classify responses, track decay. Human collects responses and validates insights. | **Hybrid** |

### Classification Output

```
route:         ontology (KANO-ONT)
series:        VE-Series
lineage:       VP → KANO → PMF
granularity:   METHODOLOGY (contains processes + techniques)
agent:         hybrid
skill:         kano:classifyFeatures (skillType: analysis)
process:       kano:KanoAnalysisProcess (4 phases)
```

### Proposed KANO-ONT Entity Sketch (Discussion Only)

| Entity | Purpose | Key Properties |
|---|---|---|
| `kano:KanoCategory` | The 5 satisfaction categories | categoryType (enum: must-be, performance, attractive, indifferent, reverse), satisfactionCurve |
| `kano:KanoSurvey` | A complete Kano survey instrument | targetICP (→ vp:IdealCustomerProfile), surveyDate, sampleSize, confidenceLevel |
| `kano:KanoQuestion` | Functional/dysfunctional question pair | feature (→ vp:Benefit or vp:Solution), functionalQuestion, dysfunctionalQuestion |
| `kano:KanoClassification` | Feature → category assignment | feature, category (→ KanoCategory), confidence, classificationDate, surveyRef |
| `kano:SatisfactionCurve` | Non-linear satisfaction function per category | categoryType, curveFunction, inflectionPoints, customerSegment |
| `kano:KanoDecay` | Category migration over time | feature, fromCategory, toCategory, decayPeriod, detectionDate, evidence |
| `kano:FeaturePriority` | Aggregated prioritisation output | feature, priorityRank, investmentRecommendation (invest/maintain/deprioritise), kanoEvidence[] |

### Proposed Join Patterns

| JP ID | Name | Source → Target | Cardinality | Relationship |
|---|---|---|---|---|
| JP-KANO-VP-001 | VP Feature Classification | vp:Benefit → kano:KanoClassification | 1..* | `classifiedBy` |
| JP-KANO-PMF-001 | Kano to PMF Validation | kano:KanoClassification → pmf:ValidationSignal | 1..1 | `informsValidation` |
| JP-KANO-KPI-001 | Satisfaction Measurement | kano:SatisfactionCurve → kpi:KPI | 1..* | `measuredBy` |
| JP-KANO-RRR-001 | Priority to Requirements | kano:FeaturePriority → rrr:Requirement | 1..1 | `informsRequirement` |

### Updated VE Lineage

```
VSOM → OKR → VP → KANO → PMF → EFS → PE
                   │
                   ├─ JP-KANO-VP-001:  vp:Benefit → kano:KanoClassification
                   ├─ JP-KANO-PMF-001: kano:KanoClassification → pmf:ValidationSignal
                   ├─ JP-KANO-KPI-001: kano:SatisfactionCurve → kpi:KPI
                   └─ JP-KANO-RRR-001: kano:FeaturePriority → rrr:Requirement
```

### What Kano Is NOT:
- NOT a join pattern alone (too many unique entities — 7)
- NOT a template/CanonicalSnapshot alone (has its own vocabulary)
- NOT a PFI instance (method is universal)
- NOT an EMC composition category (it's a domain, not a view)
- NOT an extension of VP-ONT (different concern — VP defines value, Kano classifies satisfaction impact)

### What Kano IS Also:
- A `pe:Skill` — `kano:classifyFeatures` (skillType: `analysis`, provider: `hybrid`)
- A `pe:Process` — `kano:KanoAnalysisProcess` with 4 phases: Survey Design → Data Collection → Classification → Decay Assessment
- A `pe:ProcessPath` node — enabling cross-series traversal (PPM → VP → KANO → PMF)
- An EMC activation — would activate under PRODUCT and STRATEGIC composition scopes

---

## 20. Additional Worked Examples — Classification Comparison

### Blue Ocean Strategy → VE-Series Ontology

| CQ | Evidence | Score |
|---|---|---|
| CQ-VOC | StrategyCanvas, ValueCurve, FourActionsFramework (Eliminate/Reduce/Raise/Create), BuyerUtilityMap, SixPathsFramework, BlueOceanShift, NonCustomerTier (3 tiers), PioneerMigratorSettler | **9 unique** |
| CQ-COV | `industry:CompetitivePosition` covers positioning but NOT value curve methodology. No eliminate/reduce/raise/create semantics. `vp:Differentiator` is related but simpler. | **Low overlap** |
| CQ-UNI | Method universal, data per-PFI | **Split** |
| CQ-SER | Market creation strategy → **VE-Series** | VE-Series |
| CQ-LIN | Consumes INDUSTRY-ONT competitive data, produces VP value propositions | **INDUSTRY → BLUE-OCEAN → VP** |
| CQ-REP | Yes — SixPaths → StrategyCanvas → FourActions → BlueOceanShift | **Yes** |
| CQ-AGT | Agent can map value curves, identify non-customers. Human validates market insight. | **Hybrid** |
| **Route** | **ontology** (BLUE-OCEAN-ONT), VE-Series/VSOM-SA | |

**Relationship to Kano:** Blue Ocean's "Create" action directly feeds Kano's "Attractive" category — new value curves produce delighters. The two form a strategic feedback loop:

```
Blue Ocean "Create" actions → New features
    → Kano classifies as "Attractive"
    → PMF.fitStatus improves
    → Over time, Kano Decay → "MustBe"
    → Blue Ocean re-evaluates → new "Create" actions
```

### Jobs To Be Done (JTBD) → VP-ONT Version Bump

| CQ | Evidence | Score |
|---|---|---|
| CQ-VOC | Job (functional/emotional/social), JobStep, OutcomeExpectation, OverservedJob, UnderservedJob, JobMap, HiringCriteria | **7 unique** |
| CQ-COV | **High** — `vp:Problem` ≈ Job, `vp:PainPoint` ≈ UnderservedJob, `vp:Gain` ≈ OutcomeExpectation, `vp:Solution` ≈ what gets "hired" | **Significant overlap** |
| CQ-FIT | JTBD's core concern IS value proposition — same namespace | **Same concern** |
| CQ-COH | Adding JTBD entities to VP-ONT enriches it without violating single-responsibility | **Cohesive** |
| **Route** | **version-bump** (VP-ONT v2.0.0) | |

**Critical distinction from Kano:** JTBD has enough unique vocabulary (7 entities) to pass Q1, but CQ-COV shows high overlap with VP-ONT. CQ-FIT confirms same namespace concern. So it's a version bump, not a new ontology. The logic tree catches this difference.

### Pareto Analysis → pe:Skill Only

| CQ | Evidence | Score |
|---|---|---|
| CQ-VOC | ParetoCategory (vital few / useful many), CumulativeDistribution, 80/20 threshold | **2-3** |
| Q1 threshold | **FAIL** — <5 unique entities | → Q6 route |
| Q6d: Workflow? | **Yes** — repeatable: rank items → calculate cumulative % → identify 80/20 split → prioritise | **pe:Skill** |
| **Route** | **skill** — `pe:paretoAnalysis` (skillType: analysis, provider: agent, owningOntology: KPI-ONT) | |

### 5 Whys → pe:Skill within REASON-ONT

| CQ | Evidence | Score |
|---|---|---|
| CQ-VOC | RootCause, WhyChain, CausalLayer | **2-3** |
| Q1 | **FAIL** — <5 unique entities | → Q6 route |
| Q6d | **Yes** — repeatable: state problem → ask why → repeat → identify root cause | **pe:Skill** |
| **Route** | **skill** — `rsn:fiveWhysAnalysis` (skillType: analysis, provider: hybrid, owningOntology: REASON-ONT) | |

### Comparison Summary

| Methodology | Unique Entities | Overlap | Route | Series | Granularity | Agent |
|---|---|---|---|---|---|---|
| **Kano** | 7 | Low | `ontology` KANO-ONT | VE | Methodology | Hybrid |
| **Blue Ocean** | 9 | Low | `ontology` BLUE-OCEAN-ONT | VE/VSOM-SA | Methodology | Hybrid |
| **JTBD** | 7 | **High** (VP) | `version-bump` VP v2.0 | VE | Methodology | Hybrid |
| **Pareto** | 2 | N/A | `skill` | KPI-ONT | Technique | Agent |
| **5 Whys** | 2 | N/A | `skill` | REASON-ONT | Technique | Hybrid |
| **EFS** (S1-10) | 5 | Medium | `ontology` EFS-ONT | PE (bridge) | Structure | N/A |

The CQ-driven logic tree consistently produces the correct classification across all concept types — from full methodologies to simple techniques to structural ontologies.

---

## 21. Kano → PMF → Customer Engagement Value Loop

### Why Kano Matters for PMF and Customer Engagement

Kano analysis is not just a classification exercise — it creates a **dynamic feedback loop** between value proposition, market validation, and customer engagement that directly drives PMF status.

### The Value Flow

```
VP-ONT                  KANO-ONT                 PMF-ONT
───────                 ────────                 ───────
vp:Benefit ──────────→ kano:KanoClassification ──→ pmf:ValidationSignal
vp:IdealCustomerProfile→ kano:KanoSurvey ────────→ pmf:CustomerFeedback
vp:Solution ─────────→ kano:FeaturePriority ────→ pmf:WillingnessToPayAnalysis
                            │
                        kano:KanoDecay ──────────→ pmf:PivotAssessment
                            │
                        kano:SatisfactionCurve ──→ kpi:KPI (NPS, CSAT, CES)
                            │
                        FEEDBACK LOOP:
                        pmf:CustomerFeedback ────→ kano:KanoSurvey (re-classify)
                        pmf:PivotAssessment ─────→ vp:ValueProposition (refresh)
```

### Three PMF Signal Types from Kano

Each Kano category produces a different kind of PMF validation signal:

#### Must-Be → Satisfaction Floor Signal

```
vp:Benefit "Fast onboarding"
    → kano:classify → kano:MustBe (basic expectation)
    → pmf:ValidationSignal {
        signalType: "satisfaction-floor",
        strength: 0.9,
        insight: "Absence causes churn. Presence doesn't delight.
                  Investment threshold: ensure parity, don't over-invest."
      }
```

**Strategic implication**: Must-be features are table stakes. Money spent improving them beyond "good enough" is waste (Lean principle: eliminate waste). Redirect investment to Attractive features.

#### Performance → Willingness-to-Pay Signal

```
vp:Benefit "Dashboard response time"
    → kano:classify → kano:Performance (linear satisfaction)
    → pmf:ValidationSignal {
        signalType: "willingness-to-pay",
        strength: 0.7,
        insight: "Faster = higher satisfaction, directly correlated.
                  Customers will pay more for measurable improvement."
      }
```

**Strategic implication**: Performance features are the competitive battleground. Direct correlation between investment and satisfaction means ROI is predictable. Price differentiation maps here.

#### Attractive → Differentiation Signal

```
vp:Benefit "AI-powered recommendations"
    → kano:classify → kano:Attractive (delighter)
    → pmf:ValidationSignal {
        signalType: "differentiation",
        strength: 0.95,
        insight: "Unexpected. Drives NPS and word-of-mouth.
                  This is the Blue Ocean 'Create' zone."
      }
```

**Strategic implication**: Attractive features drive organic growth. They're the reason customers switch from competitors. But they decay (see below).

### Kano Decay — The Strategic Time Bomb

The most powerful insight Kano provides is **category migration over time**:

```
Year 1:  "AI-powered recommendations" → kano:Attractive (delighter)
Year 2:  "AI-powered recommendations" → kano:Performance (expected to improve)
Year 3:  "AI-powered recommendations" → kano:MustBe (table stakes)

Consequence:
  pmf:ProductMarketFit.fitStatus transitions from "achieved" → "eroding"
  UNLESS new Attractive features replace decayed ones
```

This is where Kano connects to the Blue Ocean feedback loop (Section 20) and to `pmf:PivotAssessment`:

```
kano:KanoDecay {
  feature: "AI-powered recommendations",
  fromCategory: "Attractive",
  toCategory: "MustBe",
  decayPeriod: "24 months",
  detectionDate: "2028-01-15"
}
    → pmf:PivotAssessment {
        pivotType: "feature-refresh",
        trigger: "kano-decay-threshold",
        impact: "3 Attractive features decayed to MustBe in 12 months",
        recommendation: "Blue Ocean Create action required"
      }
    → vp:ValueProposition (refresh cycle triggered)
```

### W4M-WWG Worked Example

Applying Kano to the W4M-WWG PFI instance (UK specialist food importer):

| Feature | Kano Category | PMF Signal | Strategic Action |
|---|---|---|---|
| **Cold chain tracking** (AU/NZ→UK) | Must-Be | satisfaction-floor | Ensure parity with competitors. Don't over-invest. |
| **Supplier quality scoring** | Performance | willingness-to-pay | Differentiate on accuracy. Charge premium for real-time scores. |
| **AI demand forecasting** | Attractive | differentiation | Key differentiator. Market aggressively. Track decay. |
| **Basic order management** | Must-Be | satisfaction-floor | Table stakes. Match market standard. |
| **Source corridor optimisation** (4 corridors) | Performance | willingness-to-pay | Direct ROI — better routing = lower cost. Measurable. |
| **Carbon footprint per shipment** | Attractive → Performance | differentiation (decaying) | Was unique 18 months ago, now becoming expected. Invest in next delighter. |

**EMC composition**: KANO-ONT would activate under W4M-WWG's `requirementScopes: [PRODUCT, FULFILMENT, COMPETITIVE]` — all three benefit from satisfaction classification.

### The Customer Engagement Cycle

Kano creates a **continuous engagement loop**, not a one-time classification:

```
1. SURVEY:   kano:KanoSurvey (target: vp:IdealCustomerProfile)
                │
2. CLASSIFY:  kano:KanoClassification (feature → category)
                │
3. SIGNAL:    pmf:ValidationSignal (satisfaction type + strength)
                │
4. MEASURE:   kpi:KPI (NPS delta, CSAT by feature, CES scores)
                │
5. DECAY:     kano:KanoDecay (detect category migration)
                │
6. PIVOT:     pmf:PivotAssessment (refresh or maintain?)
                │
7. REFRESH:   vp:ValueProposition (update features/benefits)
                │
                └─→ Return to step 1 (re-survey with updated features)
```

This cycle maps directly to a `pe:Process` with 7 phases, each of which can `invokesSkill` for the relevant analytical capability. The process can be orchestrated by a `pe:AIAgent` operating in `hybrid` autonomy mode — agent drives steps 2-5 automatically, human reviews steps 1, 6, 7.

---

## 22. Edge Semantics & Node Labels — Visualisation Standards

### The Problem: What Do Edges Mean?

When a concept crosses 2+ ontologies (which Kano, EFS, and most VE-chain concepts do), the edges between ontologies must carry **clear, standardised semantic meaning**. Currently, cross-ontology edges in the visualiser are rendered as dashed lines with no consistent predicate vocabulary.

### Five Standardised Edge Predicates

All cross-ontology relationships in PF-Core should use one of these five predicates:

| Predicate | Semantic Meaning | Cardinality | Use Case | Example |
|---|---|---|---|---|
| `mapsTo` | **Alignment** — same real-world thing, different viewpoint | 1:1 | Standing equivalences between ontologies | `vp:Problem` → `rrr:Risk` (JP-VP-RRR-001) |
| `cascadesTo` / `informsValue` | **Lineage** — output of one feeds input of another | 1:* | Value chain flow, strategic cascade | `vsom:Strategy` → `okr:Objective` (JP-VE-001) |
| `measuredBy` / `trackedBy` | **Measurement** — one entity quantifies another | 1:* | KPI tracking, metric binding | `kano:SatisfactionCurve` → `kpi:KPI` |
| `governedBy` | **Governance** — one entity constrains another | 0:* | Regulatory/compliance control | `pe:Process` → `grc:Requirement` |
| `executesVia` / `invokesSkill` | **Execution** — one entity operationalises another | 0:* | Strategy-to-action chain | `efs:SubFunction` → `pe:Process` |

### The Decision Rule for Edge Semantics

When defining a new cross-ontology relationship (e.g., in a join pattern):

1. **If both entities describe the SAME real-world thing** → `mapsTo` (alignment, 1:1)
2. **If one entity PRODUCES data consumed by another** → `cascadesTo` / `informsValue` (lineage, 1:*)
3. **If one entity MEASURES another** → `measuredBy` / `trackedBy` (measurement, 1:*)
4. **If one entity GOVERNS or CONSTRAINS another** → `governedBy` (governance, 0:*)
5. **If one entity EXECUTES or OPERATIONALISES another** → `executesVia` / `invokesSkill` (execution, 0:*)

These predicates should be standardised across ALL join patterns — not invented per-pattern. The join-pattern-registry already converges on this vocabulary.

### Node Label Standards

| View Context | Label Format | Rationale |
|---|---|---|
| **Single-ontology view** | Entity name only (`Problem`, `Benefit`) | Prefix is redundant when namespace is implicit |
| **Cross-ontology / EMC composition** | `prefix:EntityName` (`vp:Problem`, `kano:MustBe`) | **Essential** — disambiguation across namespaces |
| **Instance data view** | Entity name + instance identifier (`Problem: "Slow onboarding"`) | Shows real data, not just schema |
| **Lineage/path view** | `prefix:Entity` + relationship predicate on edge | Shows both node identity and relationship semantics |
| **JP Pathway view** (Section 13 Mode 1) | Highlighted nodes at full opacity, others at `CONTEXT_OPACITY`. Edge labels show predicate. JP metadata in sidebar. | Pathway-focused UX |

### The Node Label CQ

**CQ-DIS (disambiguation)**: "Can a user identify which ontology a node belongs to without external context?"

- If **yes** (single-ontology view) → label is optional, entity name only
- If **no** (cross-ontology view) → prefix is **mandatory**

This CQ should be evaluated by the renderer at display time based on the current view mode, not hardcoded per node.

### Edge Label Standards

| Context | Show on Edge | Show in Sidebar/Tooltip |
|---|---|---|
| **Default entity graph** | Relationship name (e.g., `hasProcess`, `ownedByRole`) | Full relationship metadata |
| **Cross-ontology edges** | Predicate (e.g., `mapsTo`, `cascadesTo`) | JP ID, cardinality, description |
| **JP Pathway mode** | Predicate + direction arrow | Full JP metadata including bridgeEntities |
| **Value chain mode** | Artifact name flowing between phases | Phase metadata, gate conditions |

**Critical rule**: Edge labels should show the **relationship predicate**, never the JP ID. The JP ID is metadata, not meaning. `vp:Problem → mapsTo → rrr:Risk` communicates instantly. `vp:Problem → JP-VP-RRR-001 → rrr:Risk` does not.

---

## 23. Kano Reframed — VSOM-SA Analytical Framework, Not VE-Chain Link

### The Audit Finding

A full audit of the existing ontology library reveals that the VE-chain already has more Kano support than Section 19 assumed:

| Existing Asset | What It Already Provides | Kano Gap Remaining |
|---|---|---|
| **VP-ONT v1.2.3** | `gainType: Desired/Unexpected` (test data literally says "delighter"), `gainCategory: Functional/Economic/Emotional/Social`, `vp:Benefit` with `benefitType`, `vp:ValidationEvidence` with strength scoring | No 5-category satisfaction classification (Must-Be/Performance/Attractive/Indifferent/Reverse). No satisfaction threshold concept. |
| **PMF-ONT v2.0.0** | `pmf:MarketValidation` aggregates `customerSatisfactionScore`, `netPromoterScore`, `retentionRate`, `churnRate`. `pmf:WillingnessToPayAssessment` covers pricing research (van Westendorp, conjoint). `pmf:CustomerSegmentFit` with `painPointAlignment` (0.0-1.0). | No **decay curve** modelling. No importance-satisfaction mapping. No structured assessment method — just aggregate scores. |
| **KPI-ONT v1.0.0** | Strategic metrics (BSC, OKR). MoSCoW prioritisation. | No customer satisfaction KPIs (NPS/CSAT/CES are in PMF, not KPI). |
| **REASON-ONT v1.0.0** | `rsn:MECETree` with `assignedFramework` — can route to any analytical framework. `rsn:StrategicHypothesis` with evidence chains. `rsn:AnalysisSynthesis` forces cross-analysis convergence. | Generic reasoning — Kano-specific entities needed. |
| **PE-ONT v4.0.0** | `pe:Skill` — cross-cutting, invokable analytical capability with formal I/O. | Kano skill not registered. |
| **JP-VP-003** | `vp:ValueProposition → pmf:validatesMarket → pmf:ProductMarketFit` (active, 1:1) | No Kano-enriched VP→PMF bridge. |

**Score: ~60% coverage exists.** The gap is the 5 genuinely unique Kano concepts: **KanoCategory enum**, **functional/dysfunctional survey structure**, **non-linear satisfaction curves**, **temporal category decay**, and **classification confidence**.

### The L6S Analogy — Why It's Right

Kano Analysis is to VP→PMF what Lean Six Sigma is to PE process improvement:

| Dimension | Lean Six Sigma | Kano Analysis |
|---|---|---|
| **What it is** | Structured analytical methodology with phases, tools, artefacts | Structured analytical methodology with phases, tools, artefacts |
| **Where it lives** | PE-Series (DMAIC-ONT, K-DMAIC-ONT) | VE-Series/VSOM-SA (proposed) |
| **Relationship to chain** | PARALLEL to PE process chain — invoked when quality analysis needed | PARALLEL to VP→PMF chain — invoked when satisfaction analysis needed |
| **Is it a chain link?** | NO — processes don't go "through" DMAIC. DMAIC is applied TO processes. | NO — features don't go "through" Kano. Kano is applied TO features. |
| **Own entities?** | Yes — Control Chart, Process Capability, Sigma Level, Defect Rate, CTQ | Yes — KanoCategory, SatisfactionCurve, KanoDecay, KanoSurvey |
| **pe:Skill?** | Yes — multiple skills (measure process capability, analyse root cause, etc.) | Yes — `kano:classifyFeatures`, `kano:assessDecay` |
| **Agent-capable?** | Hybrid | Hybrid |

### The Critical Reframing: Parallel Lens, Not Sequential Step

Section 19 positioned Kano as a chain link: `VP → KANO → PMF`. This is **wrong**. The correct model:

```
VE MAIN CHAIN (sequential):
  VSOM → OKR → VP ──────────────────→ PMF → EFS → PE
                    │                   ↑
                    │    ENRICHMENT     │
                    │                   │
VSOM-SA PARALLEL LENSES (invoked as needed):
                    ├─→ BSC-ONT ───────┤ (strategy evaluation)
                    ├─→ INDUSTRY-ONT ──┤ (competitive analysis)
                    ├─→ REASON-ONT ────┤ (analytical reasoning)
                    ├─→ MACRO-ONT ─────┤ (environmental scanning)
                    ├─→ PORTFOLIO-ONT ─┤ (portfolio assessment)
                    └─→ KANO-ONT ──────┘ (satisfaction classification)
```

Kano **enriches** the VP→PMF transition but doesn't **interrupt** it. A product can achieve PMF without formal Kano analysis (many do). But Kano makes the VP→PMF signal **dramatically more precise** — instead of "customers seem satisfied" you get "3 features are Must-Be (table stakes), 2 are Performance (competitive battleground), 1 is Attractive (differentiator decaying toward Performance over 18 months)."

This is exactly how L6S works in PE-Series: a process can run without DMAIC, but DMAIC makes the process analysis dramatically more precise.

### What Kano Uniquely Adds (The Thin 40%)

After subtracting the 60% already covered by VP/PMF/KPI/REASON:

| Unique Concept | Why It Can't Live Elsewhere | Proposed Entity |
|---|---|---|
| **5-category satisfaction classification** | VP's `gainType` has 2 values (Desired/Unexpected). Kano's 5 categories are a different analytical framework with distinct satisfaction curves per category. | `kano:KanoCategory` (enum: must-be, performance, attractive, indifferent, reverse) |
| **Functional/dysfunctional survey pairs** | No existing entity models paired questions. PMF has `customerSatisfactionScore` as a scalar. Kano's survey is a structured instrument. | `kano:KanoSurvey`, `kano:KanoQuestion` |
| **Non-linear satisfaction curves** | VP/PMF assume linear satisfaction. Kano's insight is that satisfaction is asymptotic (must-be), linear (performance), or exponential (attractive). | `kano:SatisfactionCurve` |
| **Temporal category decay** | No existing ontology models feature migration over time. This is Kano's killer insight — delighters become table stakes. | `kano:KanoDecay` |
| **Classification confidence** | VP's evidence has strength (Strong/Moderate/Weak). Kano needs statistical confidence on category assignment (sample size, p-value). | Property on `kano:KanoClassification` |

**Entity count: 5-6 unique.** Passes Q1 threshold. But barely — which is why it belongs in VSOM-SA (lightweight, focused) rather than the main VE chain (heavyweight, sequential).

### VSOM-SA Placement — Alongside BSC, INDUSTRY, REASON

```
VE-Series/VSOM-SA/
  ├── BSC-ONT/         (strategic performance evaluation)
  ├── INDUSTRY-ONT/    (competitive/market analysis — Porter's, SWOT, Ansoff)
  ├── REASON-ONT/      (analytical reasoning — MECE, logic trees, hypothesis)
  ├── MACRO-ONT/       (environmental scanning — PESTEL, scenarios)
  ├── PORTFOLIO-ONT/   (portfolio assessment — BCG, Three Horizons)
  └── KANO-ONT/        (satisfaction classification — Kano model, decay tracking)
```

All VSOM-SA ontologies share the same architectural pattern:

1. **Thin, focused** — 5-15 entities modelling one analytical framework
2. **Invoked via pe:Skill** — agent/human triggers the analysis when needed
3. **Consume from VP chain** — take features/benefits/strategies as inputs
4. **Produce for PMF/KPI** — output signals, scores, evidence that enriches validation
5. **Bridged via REASON-ONT** — `rsn:MECETree.assignedFramework = "Kano"` routes reasoning to the right lens

### Skill Registration

```
pe:Skill {
  skillId:          "SKILL-KANO-001"
  skillName:        "Classify Features by Kano Model"
  description:      "Applies Kano satisfaction analysis to VP features/benefits,
                     producing category classifications with confidence scores
                     and temporal decay projections"
  skillType:        "analysis"
  owningOntology:   "KANO-ONT"
  provider:         "hybrid"
  version:          "1.0.0"

  inputs: [
    { name: "features",      source: "vp:Benefit[]",            required: true  },
    { name: "customerBase",   source: "vp:IdealCustomerProfile", required: true  },
    { name: "surveyData",     source: "kano:KanoSurvey",         required: false },
    { name: "priorClassifications", source: "kano:KanoClassification[]", required: false }
  ]

  outputs: [
    { name: "classifications",  target: "kano:KanoClassification[]" },
    { name: "decayProjections", target: "kano:KanoDecay[]" },
    { name: "priorityRanking",  target: "kano:FeaturePriority[]" },
    { name: "pmfSignals",       target: "pmf:ValidationSignal[]" }
  ]

  phases: [
    "Survey Design → Data Collection → Classification → Decay Assessment"
  ]
}

pe:Skill {
  skillId:          "SKILL-KANO-002"
  skillName:        "Assess Kano Decay"
  description:      "Evaluates temporal migration of feature categories,
                     detecting delighter→must-have decay and triggering
                     PMF pivot assessment when threshold breached"
  skillType:        "analysis"
  owningOntology:   "KANO-ONT"
  provider:         "agent"
  version:          "1.0.0"

  inputs: [
    { name: "priorClassifications", source: "kano:KanoClassification[]", required: true },
    { name: "currentClassifications", source: "kano:KanoClassification[]", required: true },
    { name: "decayThreshold",  source: "number", default: 0.3, required: false }
  ]

  outputs: [
    { name: "decayEvents",     target: "kano:KanoDecay[]" },
    { name: "pivotSignal",     target: "pmf:PivotAssessment" },
    { name: "refreshTrigger",  target: "vp:ValueProposition" }
  ]
}
```

### Join Patterns (Updated — Enrichment, Not Chain)

| JP ID | Name | Source → Target | Type | Predicate |
|---|---|---|---|---|
| JP-KANO-VP-001 | VP Feature Input | `vp:Benefit` → `kano:KanoClassification` | **Enrichment** | `classifiedBy` |
| JP-KANO-PMF-001 | Satisfaction Signal | `kano:KanoClassification` → `pmf:ValidationSignal` | **Enrichment** | `informsValidation` |
| JP-KANO-KPI-001 | Satisfaction Metrics | `kano:SatisfactionCurve` → `kpi:KPI` | **Measurement** | `measuredBy` |
| JP-KANO-PMF-002 | Decay → Pivot | `kano:KanoDecay` → `pmf:PivotAssessment` | **Lineage** | `triggersAssessment` |
| JP-KANO-REASON-001 | Reasoning Bridge | `rsn:MECETree` → `kano:KanoCategory` | **Execution** | `assignedFramework` |

Note the type change: these are **enrichment edges**, not **chain edges**. The VP→PMF chain link (JP-VP-003) still operates without Kano. Kano JPs are additive precision.

### EMC Activation

KANO-ONT would activate under existing EMC composition categories:

| Category | Why Kano Activates |
|---|---|
| **PRODUCT** | Feature satisfaction directly informs product decisions |
| **COMPETITIVE** | Kano categories reveal competitive positioning (must-be = parity, attractive = differentiation) |
| **STRATEGIC** | Decay tracking is strategic intelligence — when delighters become table stakes |

No new EMC category needed — Kano fits naturally in 3 existing categories.

### Impact on Section 19 — Correction

Section 19's classification output should be updated:

**Original (Section 19):**

```text
lineage:       VP → KANO → PMF  (WRONG — implies chain link)
```

**Corrected:**

```text
placement:     VE-Series/VSOM-SA (parallel analytical lens)
enriches:      VP → PMF transition (not a chain link)
invoked-via:   pe:Skill SKILL-KANO-001, SKILL-KANO-002
bridged-via:   rsn:MECETree.assignedFramework = "Kano"
```

This is the same correction that would apply to BSC (BSC doesn't sit IN the VSOM→OKR chain — it enriches it), INDUSTRY-ONT (Porter's doesn't sit IN the strategic planning chain — it enriches it), and REASON-ONT (logic trees don't sit IN any chain — they enrich all of them).

### Visualiser & Workbench Implications

For the ontology visualiser (Epic 40 Workbench) and strategy-as-code:

| Concern | Implication |
|---|---|
| **Graph rendering** | KANO-ONT nodes render in VSOM-SA cluster alongside BSC, INDUSTRY, REASON. Cross-ontology edges to VP/PMF use dashed enrichment lines (not solid chain lines). |
| **EMC composer** | `emc-composer.js` CATEGORY_COMPOSITIONS for PRODUCT/COMPETITIVE/STRATEGIC gain KANO-ONT as an optional activation. `constrainToInstanceOntologies()` filters normally — PFI instances must declare KANO in their `instanceOntologies` array. |
| **App skeleton** | No new zones needed. KANO-ONT activates in existing Z-EMC composition panel when a PFI instance includes it. Skill execution flows through existing pe:Skill invocation infrastructure. |
| **Strategy-as-code** | Kano analysis becomes a callable skill in the agentic pipeline: `agent.invoke("SKILL-KANO-001", { features: vpBenefits, customerBase: icp })`. Output feeds PMF validation. Decay assessment runs on schedule (SKILL-KANO-002). |
| **PFI instance impact** | W4M-WWG `instanceOntologies` would add `KANO-ONT` alongside VP, RRR, LSC, OFM, KPI, BSC, EMC. BAIV similarly. VHF (PoC) probably not — too early for satisfaction analysis. |

### Summary — The Useful Approach

Kano is indeed "such a useful approach" — and the L6S analogy is exactly right. Both are:

- **Structured analytical frameworks** with their own vocabulary
- **Parallel lenses** that enrich a main chain without interrupting it
- **Skill-registered** — invoked by agents when the analysis is needed
- **PFI-gated** — instances declare whether they use the framework

The difference from L6S: Kano is VE-facing (satisfaction/value), L6S is PE-facing (process/quality). Same architectural pattern, different domain.

**Recommendation:** Activate KANO-ONT as a lightweight VSOM-SA ontology (5-6 entities, 2 skills, 5 JPs). Register in ont-registry-index.json as part of VE-Series/VSOM-SA. No impact on the main VE chain. Maximum value for PMF precision with minimum architectural disruption.

---

*This briefing is for strategic discussion only. No implementation should proceed without direction approval.*

*Generated: 2026-02-26 (final) | VSOM VE Skill-Chain Evaluation | Strategist's Lens Applied*
*Extended: 2026-02-26 | Sections 17-22: CQ Classification Framework, Methodology Taxonomy, Kano Analysis, Edge Semantics*
*Revised: 2026-02-26 | Section 23: Kano reframed as VSOM-SA parallel lens (not VE-chain link) following ontology audit*
