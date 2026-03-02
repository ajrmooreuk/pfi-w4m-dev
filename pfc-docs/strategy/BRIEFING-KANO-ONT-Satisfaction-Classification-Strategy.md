# BRIEFING: KANO-ONT — Customer Satisfaction Classification Strategy

**Date:** 2026-02-28
**Author:** Azlan EA-AAA
**Type:** VSOM-SA Extension — Ontology + Skill Definition
**Status:** STRATEGY BRIEF — For Review & Approval
**Parent Epic:** Epic 49 (#747) — VSOM Skilled Application Planner
**Registry Placeholder:** KANO-ONT (Customer Satisfaction Classification) — VE-Series/VSOM-SA
**Cross-Refs:** VP-ONT v1.2.3, PMF-ONT v2.0.0, REASON-ONT v1.0.0, BSC-ONT v1.0.0, KPI-ONT, PE-ONT v4.0.0, EMC-ONT v5.0.0
**Sessions In Scope:** S19 (CQ Evaluation), S21 (Value Loop), S23 (Reframe as Parallel Lens)
**Related Briefings:** `BRIEFING-EFS-ONT-*` §17-23, `BRIEFING-VSOM-Strategy-Analysis.md`, `BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md`

---

## 1. Executive Summary

This briefing defines the function, scope, benefits, ontology structure, and skill architecture for **KANO-ONT** — a lightweight VSOM-SA ontology that bridges VP-ONT and PMF-ONT with structured customer satisfaction classification.

The Kano Model (Noriaki Kano, 1984) classifies product/service features into 5 satisfaction categories based on non-linear customer response curves. Today, VP-ONT captures *what customers want* and PMF-ONT validates *whether they'll pay and stay* — but **no ontology classifies which features drive satisfaction, which are table stakes, and which decay over time**. KANO-ONT fills that gap.

**Key decisions consolidated from S19/S21/S23:**
- Kano is a **parallel analytical lens** (like L6S in PE-Series), NOT a chain link
- Placement: `VE-Series/VSOM-SA/KANO-ONT/` alongside BSC, INDUSTRY, REASON, PORTFOLIO, MACRO
- 5-6 unique entities, 2 registered skills, 5 join patterns
- Activates under existing EMC categories (PRODUCT, COMPETITIVE, STRATEGIC) — no new category needed
- ~60% coverage already exists in VP/PMF/REASON — Kano adds the thin 40% that makes the signal precise

---

## 2. The Problem — What's Missing Between VP and PMF

### 2.1 The Gap

| Layer | Ontology | What It Provides | What's Missing |
|-------|----------|-----------------|----------------|
| **Value Design** | VP-ONT v1.2.3 | Problems (4 types), Gains (`gainType`: Required/Expected/Desired/Unexpected), Benefits (7 categories), Differentiators, ICPs | **No classification of HOW features contribute to satisfaction** — gainType has 4 values but no satisfaction curve model |
| **Market Validation** | PMF-ONT v2.0.0 | fitScore (0-1), retentionRate, NPS, churnRate, organicGrowth, WTP assessment | **No root-cause analysis of WHICH features drove PMF** — scalar metrics without feature attribution |
| **Reasoning** | REASON-ONT v1.0.0 | MECETree with `assignedFramework`, StrategicHypothesis, AnalysisSynthesis | **Generic reasoning — no Kano-specific entities** for survey structure, satisfaction curves, or category decay |

### 2.2 The Current VP→PMF Bridge (JP-PMF-001)

```
vp:ValueProposition → pmf:validatesMarket → pmf:ProductMarketFit
```

This is a **binary bridge** — it says "this VP achieved/didn't achieve PMF." It does NOT say:
- *Which* features were must-haves vs. delighters
- *Which* features drove retention vs. organic growth vs. WTP
- *How* feature satisfaction categories will decay over time
- *Where* to invest next based on satisfaction elasticity

### 2.3 Why This Matters

Without Kano classification, PMF validation is a black box:
- **Before Kano:** "Customers seem satisfied" → fitScore: 0.72
- **After Kano:** "3 features are Must-Be (table stakes), 2 are Performance (competitive battleground), 1 is Attractive (differentiator decaying toward Performance over 18 months)" → fitScore: 0.72 **with actionable feature-level attribution**

---

## 3. Function Scope

### 3.1 What KANO-ONT Does

KANO-ONT provides a structured, machine-readable framework for:

1. **Feature Classification** — Assign every VP feature/benefit to one of 5 Kano categories using functional/dysfunctional survey pairs
2. **Satisfaction Modelling** — Define non-linear satisfaction curves per category (asymptotic for Must-Be, linear for Performance, exponential for Attractive)
3. **Temporal Decay Tracking** — Detect and record when features migrate categories (Attractive → Performance → Must-Be) over time
4. **Priority Synthesis** — Aggregate Kano classifications into investment recommendations (invest/maintain/deprioritise)
5. **PMF Signal Enrichment** — Feed precise, feature-level satisfaction data into PMF validation

### 3.2 What KANO-ONT Does NOT Do

- **NOT a chain link** — VP→PMF chain (JP-PMF-001) operates without Kano. Kano is additive precision.
- **NOT a replacement for VP's gainType** — VP captures *what* customers want. Kano classifies *how they respond*.
- **NOT a standalone analysis tool** — Kano consumes VP outputs and produces PMF inputs. It enriches, not replaces.
- **NOT an extension of VP-ONT** — Different concern. VP defines value. Kano classifies satisfaction impact.

### 3.3 Architectural Position — The L6S Analogy

From S23, Kano Analysis is to VP→PMF what Lean Six Sigma is to PE process improvement:

| Dimension | Lean Six Sigma (L6S) | Kano Analysis |
|-----------|---------------------|---------------|
| **Main chain** | PE process improvement | VE value engineering |
| **Where it lives** | PE-Series (DMAIC-ONT, K-DMAIC-ONT) | VE-Series/VSOM-SA |
| **Relationship** | Applied TO processes, not a process step | Applied TO features, not a value chain step |
| **Is it a chain link?** | NO — DMAIC is invoked as needed | NO — Kano is invoked as needed |
| **Own entities?** | Yes — Control Chart, Sigma Level, CTQ | Yes — KanoCategory, SatisfactionCurve, KanoDecay |
| **pe:Skill?** | Yes — multiple DMAIC skills | Yes — SKILL-KANO-001, SKILL-KANO-002 |

### 3.4 The Correct Model (S23 Reframe)

```
VE MAIN CHAIN (always active):
VP → PMF → EFS → PPM → PE

VSOM-SA PARALLEL LENSES (invoked as needed):
MACRO ───→ VSOM.Vision
INDUSTRY ─→ VSOM.Strategy
BSC ──────→ VSOM.Objectives ──→ KPI
REASON ───→ Cross-ontology synthesis
PORTFOLIO → Investment prioritisation
                    │
                    └─→ KANO ──────┘ (satisfaction classification)
                                    ↓
                              VP → PMF (enriched signal)
```

---

## 4. Benefits

### 4.1 Strategic Benefits

| # | Benefit | Measurement | PMF Impact |
|---|---------|-------------|------------|
| B1 | **Feature-level PMF attribution** — Know exactly which features drove retention, NPS, organic growth | % of PMF metrics with feature-level root cause | Transforms PMF from "achieved/not" to "achieved because X, blocked by Y" |
| B2 | **Decay early warning** — Detect when delighters become table stakes before competitors catch up | Months of advance warning before category migration | Prevents competitive surprise; feeds `pmf:PivotAssessment` |
| B3 | **Segment-specific roadmaps** — Different customer segments classify same feature differently | # of segment-specific Kano profiles per PFI instance | Enables targeted product strategy per `pmf:CustomerSegmentFit` |
| B4 | **Investment precision** — Invest in Performance features (WTP elasticity), maintain Must-Haves, sprint on Attractives | Investment efficiency ratio (spend aligned to Kano priority) | Reduces wasted spend on Indifferent features |
| B5 | **VE chain signal quality** — VP→PMF bridge gains 40% more precision from satisfaction classification | Increase in actionable PMF signals per iteration | Faster PMF achievement (fewer blind iterations) |

### 4.2 Operational Benefits

| # | Benefit | Who Benefits |
|---|---------|-------------|
| B6 | **Agent-ready Kano execution** — pe:Skill registration enables agentic pipeline invocation | AI agents (BAIV agent pipeline, W4M-WWG) |
| B7 | **EMC composition fit** — Activates under 3 existing categories with zero new infrastructure | EMC composer, PFI instance administrators |
| B8 | **Reasoning bridge** — `rsn:MECETree.assignedFramework = "Kano"` routes analytical reasoning to the right lens | pfc-reason skill, cross-ontology synthesis |
| B9 | **W4M-WWG immediate applicability** — 4 VP features ready for classification (disruption notification, cross-corridor substitution, shelf-life transparency, margin tracking) | W4M-WWG PFI instance |

---

## 5. Ontology Definition — KANO-ONT v1.0.0

### 5.1 Metadata

```
@id:          KANO-ONT
name:         KANO Ontology (Customer Satisfaction Classification)
prefix:       kano:
namespace:    https://oaa-ontology.org/v6/kano/
version:      1.0.0
oaaVersion:   7.0.0
series:       VE-Series
subSeries:    VSOM-SA
path:         VE-Series/VSOM-SA/KANO-ONT/
status:       compliant (target)
placement:    VE-Series/VSOM-SA (parallel analytical lens)
lineage:      Enriches VP → PMF (not a chain link)
invoked-via:  pe:Skill SKILL-KANO-001, SKILL-KANO-002
bridged-via:  rsn:MECETree.assignedFramework = "Kano"
```

### 5.2 Entity Definitions (6 Entities)

| # | Entity | Purpose | Key Properties |
|---|--------|---------|---------------|
| E1 | **kano:KanoSurvey** | Complete Kano survey instrument for a customer segment | `surveyId`, `targetICP` (→ vp:IdealCustomerProfile), `surveyDate`, `sampleSize`, `confidenceLevel`, `segmentRef` (→ pmf:CustomerSegmentFit), `status` (planned/active/completed) |
| E2 | **kano:KanoQuestion** | Functional/dysfunctional question pair for a single feature | `questionId`, `featureRef` (→ vp:Benefit or vp:Solution), `functionalQuestion` (positive framing), `dysfunctionalQuestion` (negative framing), `surveyRef` (→ KanoSurvey), `responseScale` (5-point Kano standard) |
| E3 | **kano:KanoClassification** | Feature → category assignment with statistical confidence | `classificationId`, `featureRef`, `category` (→ KanoCategory enum), `confidence` (0.0-1.0), `sampleSize`, `classificationDate`, `surveyRef`, `segmentRef`, `evidenceStrength` (Strong/Moderate/Weak) |
| E4 | **kano:SatisfactionCurve** | Non-linear satisfaction function per category | `curveId`, `categoryType` (→ KanoCategory enum), `curveFunction` (asymptotic/linear/exponential/flat/inverse), `inflectionPoints[]`, `customerSegment`, `implementationLevel` (0-100%), `satisfactionLevel` (0-100%) |
| E5 | **kano:KanoDecay** | Category migration over time (the "strategic time bomb") | `decayId`, `featureRef`, `fromCategory`, `toCategory`, `decayPeriodMonths`, `detectionDate`, `evidence`, `competitivePressure` (High/Medium/Low), `recommendedAction` (innovate/accelerate/accept/monitor) |
| E6 | **kano:FeaturePriority** | Aggregated prioritisation output from Kano analysis | `priorityId`, `featureRef`, `priorityRank`, `investmentRecommendation` (invest/maintain/deprioritise/eliminate), `kanoEvidence[]` (→ KanoClassification[]), `wtpElasticity` (0.0-2.0), `segmentVariation` (boolean), `strategicAction` |

### 5.3 Enumerations

**kano:KanoCategory** (5-type enum):

| Category | Satisfaction Curve | If Present | If Absent | Strategic Implication |
|----------|-------------------|------------|-----------|----------------------|
| **must-be** | Asymptotic (diminishing returns) | Assumed, not appreciated | Severe dissatisfaction, deal-killer | Table stakes — maintain, don't over-invest |
| **performance** | Linear (more is better) | Proportional satisfaction increase | Proportional dissatisfaction | Competitive battleground — outperform rivals |
| **attractive** | Exponential (delight multiplier) | Disproportionate satisfaction | No dissatisfaction (unexpected) | Differentiation engine — sprint to market |
| **indifferent** | Flat (no effect) | No satisfaction change | No dissatisfaction change | Cost/complexity without ROI — deprioritise |
| **reverse** | Inverse (presence hurts) | Dissatisfaction increases | Satisfaction maintained | Anti-feature — eliminate or segment-gate |

### 5.4 Business Rules (7)

| Rule | Description | Enforcement |
|------|-------------|-------------|
| BR-KANO-001 | Every KanoClassification MUST reference a valid vp:Benefit or vp:Solution | Mandatory |
| BR-KANO-002 | Every KanoSurvey MUST target a vp:IdealCustomerProfile | Mandatory |
| BR-KANO-003 | KanoClassification.confidence MUST be ≥0.6 for production use (≥0.8 for strategic decisions) | Mandatory |
| BR-KANO-004 | KanoDecay MUST record fromCategory ≠ toCategory (no self-transitions) | Mandatory |
| BR-KANO-005 | FeaturePriority.investmentRecommendation MUST align with KanoCategory (must-be → maintain, attractive → invest) | Advisory |
| BR-KANO-006 | KanoSurvey.sampleSize SHOULD be ≥30 per segment for statistical validity | Advisory |
| BR-KANO-007 | Segment-specific classifications: same feature CAN have different categories across segments | Advisory |

### 5.5 Join Patterns (5)

| Pattern | Name | Join Path | Type | Predicate |
|---------|------|-----------|------|-----------|
| **JP-KANO-VP-001** | VP Feature Input | `vp:Benefit` → `kano:KanoClassification` | Enrichment | `classifiedBy` |
| **JP-KANO-PMF-001** | Satisfaction Signal | `kano:KanoClassification` → `pmf:ValidationSignal` | Enrichment | `informsValidation` |
| **JP-KANO-KPI-001** | Satisfaction Metrics | `kano:SatisfactionCurve` → `kpi:KPI` | Measurement | `measuredBy` |
| **JP-KANO-PMF-002** | Decay → Pivot | `kano:KanoDecay` → `pmf:PivotAssessment` | Lineage | `triggersAssessment` |
| **JP-KANO-REASON-001** | Reasoning Bridge | `rsn:MECETree` → `kano:KanoCategory` | Execution | `assignedFramework` |

All 5 use **enrichment edges** (dashed in visualiser), not chain edges (solid). VP→PMF chain (JP-PMF-001) still operates independently.

### 5.6 Cross-Ontology Edge Predicates

Per S22 standardised edge semantics, all KANO-ONT edges use approved predicates:

| Predicate | Category | Usage |
|-----------|----------|-------|
| `classifiedBy` | Enrichment | VP features classified by Kano analysis |
| `informsValidation` | Enrichment | Kano output enriches PMF validation |
| `measuredBy` | Measurement | Satisfaction curves tracked by KPIs |
| `triggersAssessment` | Lineage | Decay events trigger PMF pivot assessment |
| `assignedFramework` | Execution | REASON-ONT routes to Kano as analytical framework |

---

## 6. Existing Coverage Audit (60% Covered, 40% Gap)

From S23 ontology audit:

| Existing Asset | What It Already Provides | Kano Gap Remaining |
|----------------|--------------------------|-------------------|
| **VP-ONT v1.2.3** | `gainType` (4 values: Required/Expected/Desired/Unexpected) — partial category mapping. `vp:ValidationEvidence` with strength scoring. | 4 values ≠ 5 Kano categories. No survey structure. No satisfaction curves. No decay model. |
| **PMF-ONT v2.0.0** | `customerSatisfactionScore` (scalar), NPS, retention, churn. `pmf:WillingnessToPayAssessment` with methods. | Scalar metrics — no feature attribution. No category-specific signals. |
| **REASON-ONT v1.0.0** | `rsn:MECETree` with `assignedFramework` routing. `rsn:StrategicHypothesis` with evidence chains. | Generic reasoning — can route TO Kano but needs Kano-specific entities at the destination. |
| **PE-ONT v4.0.0** | `pe:Skill` — cross-cutting, invokable analytical capability with formal I/O. | Kano skill not yet registered. |
| **JP-VP-003** | `vp:ValueProposition → pmf:validatesMarket → pmf:ProductMarketFit` | No Kano-enriched VP→PMF bridge. |

**The thin 40% — 5 genuinely unique Kano concepts:**
1. **KanoCategory enum** (5-category model) — VP's `gainType` has 4 values with different semantics
2. **Functional/dysfunctional survey pairs** — No existing entity models paired questions
3. **Non-linear satisfaction curves** — VP/PMF assume linear satisfaction
4. **Temporal category decay** — No existing ontology models feature migration over time
5. **Classification confidence** — Statistical rigour on category assignment (sample size, confidence level)

---

## 7. Skill Architecture — Extensibility Decision Tree Evaluation

### 7.1 Decision Tree Classification — SKILL-KANO-001 (Classify Features)

Running the Extensibility Decision Tree (v1.0) for the primary Kano skill:

**Layer 1 — Capability:**

| Gate | Hypothesis | Score | Outcome |
|------|-----------|-------|---------|
| HG-01 | Requires autonomous reasoning? | **4.2** (PARTIAL) | Some analytical reasoning for satisfaction scoring, but mostly structured framework application. Ambiguous input interpretation (2/3) — survey responses need interpretation. Incomplete information decisions (1/3) — can classify with partial data. State reasoning (1/2) — tracks prior classifications. Multi-capability coordination (0/2) — single-concern. |
| HG-02 | Multi-domain orchestration? | **2.0** (FAIL) | Single ontology (KANO-ONT), no sub-agents, no parallel workflows. |

**Layer 2 — Composition:**

| Gate | Hypothesis | Score | Outcome |
|------|-----------|-------|---------|
| HG-03 | Multi-component bundling? | **2.8** (FAIL) | Single skill, no coordinated MCP integrations needed. |
| HG-04 | Script-requiring complexity? | **5.5** (PARTIAL) | >500 word instructions (yes). Executable scripts for survey processing (partial). Repeatable pattern (yes). Quality gates (yes). |
| HG-07 | Standalone skill + minor MCP? | **3.0** (FAIL) | No MCP integrations needed. |

**Terminal Recommendation: `SKILL_STANDALONE`**

Path: HG-01 PARTIAL (4.2) → HG-03 FAIL (2.8) → HG-04 PARTIAL → `SKILL_STANDALONE`

**Rationale:** Moderate analytical reasoning within a structured Kano framework. Single-concern (satisfaction classification). No orchestration or bundling required. Script-level complexity for survey data processing and classification algorithms.

### 7.2 Decision Tree Classification — SKILL-KANO-002 (Assess Decay)

| Gate | Score | Outcome |
|------|-------|---------|
| HG-01 | **3.8** (PARTIAL) | Temporal reasoning over prior classifications. Pattern detection for category migration. |
| HG-03 | **2.5** (FAIL) | Single skill, no coordination. |
| HG-04 | **4.0** (PARTIAL) | Time-series comparison, threshold detection, alert generation. |

**Terminal Recommendation: `SKILL_STANDALONE`**

Path: HG-01 PARTIAL (3.8) → HG-03 FAIL (2.5) → HG-04 PARTIAL → `SKILL_STANDALONE`

### 7.3 Architecture Decision Record

```json
{
  "@type": "pfc:AutomationDecisionRecord",
  "@id": "decision-kano-skill-2026-001",
  "evaluationId": "EVAL-EXT-KANO-2026-001",
  "problemStatement": "Need structured Kano satisfaction analysis capability for VP feature classification, feeding PMF validation with feature-level attribution",
  "evaluationDate": "2026-02-28",
  "vsomAlignment": {
    "strategicObjectiveId": "vsom:obj-s2-ve-driven-everything",
    "bscPerspective": "Customer"
  },
  "hypothesisResults": [
    {"gateId": "HG-01", "outcome": "PARTIAL", "score": 4.2, "evidence": ["Analytical reasoning for survey interpretation", "Structured framework application (not free-form)"]},
    {"gateId": "HG-02", "outcome": "FAIL", "score": 2.0, "evidence": ["Single ontology scope", "No sub-agent coordination"]},
    {"gateId": "HG-03", "outcome": "FAIL", "score": 2.8, "evidence": ["Single skill, no MCP bundling"]},
    {"gateId": "HG-04", "outcome": "PARTIAL", "score": 5.5, "evidence": ["Survey processing scripts", "Classification algorithm", "Quality gates"]}
  ],
  "recommendation": {
    "pattern": "SKILL_STANDALONE",
    "confidence": 88,
    "rationale": "Moderate autonomy, single-concern, structured analytical framework. Two standalone skills: SKILL-KANO-001 (classify) and SKILL-KANO-002 (decay).",
    "alternativeConsidered": "AGENT_UTILITY (rejected — deterministic classification, not open-ended reasoning)"
  },
  "skillArtifacts": ["SKILL-KANO-001", "SKILL-KANO-002"],
  "registryEntry": "Entry-ONT-KANO-001"
}
```

---

## 8. Skill Definitions

### 8.1 SKILL-KANO-001: Classify Features by Kano Model

```yaml
skillId:          "SKILL-KANO-001"
skillName:        "Classify Features by Kano Model"
version:          "1.0.0"
skillType:        analysis
description:      "Applies Kano satisfaction analysis to VP features/benefits,
                   producing category classifications, satisfaction curves,
                   and investment priority recommendations."
owningOntology:   "KANO-ONT"
provider:         hybrid
idempotent:       true
qualityThreshold: 75
status:           draft
```

**Inputs:**

| # | Name | Source | Required | Description |
|---|------|--------|----------|-------------|
| I1 | `features` | `vp:Benefit[]` and/or `vp:Solution` | **Yes** | VP features/benefits to classify |
| I2 | `customerBase` | `vp:IdealCustomerProfile` | **Yes** | Target segment for classification |
| I3 | `surveyData` | `kano:KanoSurvey` | No | Pre-collected survey responses (if available) |
| I4 | `priorClassifications` | `kano:KanoClassification[]` | No | Previous classifications for decay comparison |
| I5 | `competitiveLandscape` | `vp:CompetitiveAlternative[]` | No | Competitor feature baseline |

**Outputs:**

| # | Name | Target | Description |
|---|------|--------|-------------|
| O1 | `classifications` | `kano:KanoClassification[]` | Feature → category assignments with confidence |
| O2 | `satisfactionCurves` | `kano:SatisfactionCurve[]` | Non-linear satisfaction model per category |
| O3 | `priorityRanking` | `kano:FeaturePriority[]` | Aggregated investment recommendations |
| O4 | `decayProjections` | `kano:KanoDecay[]` | Projected category migrations (if priorClassifications provided) |
| O5 | `pmfSignals` | `pmf:ValidationSignal[]` | PMF-ready feature attribution signals |

**8-Section Pipeline:**

| Section | Activity | Quality Gate |
|---------|----------|-------------|
| S1: Context Loading | Load VP instance data, ICP, org-context, prior Kano data | G1: VP features loaded, ICP identified |
| S2: Survey Design | Generate functional/dysfunctional question pairs for each feature | G2: Every feature has F/D pair, response scale defined |
| S3: Data Collection | Process survey responses (or synthesise from evidence) | G3: Sample size ≥30 per segment (BR-KANO-006) |
| S4: Classification | Apply Kano evaluation matrix — map F/D response pairs to categories | G4: Every feature classified, confidence ≥0.6 (BR-KANO-003) |
| S5: Curve Modelling | Define satisfaction curve per category, identify inflection points | G5: Curve function assigned per category type |
| S6: Decay Assessment | Compare against prior classifications, detect category migration | G6: Decay events flagged with evidence (if prior data exists) |
| S7: Priority Synthesis | Aggregate into investment recommendations, WTP elasticity | G7: Priority ranking covers all features, aligns with category (BR-KANO-005) |
| S8: Output Assembly | Write JSON-LD, generate PMF signals, validate business rules | G8: JSON-LD valid, all BR-KANO rules checked |

### 8.2 SKILL-KANO-002: Assess Kano Decay

```yaml
skillId:          "SKILL-KANO-002"
skillName:        "Assess Kano Category Decay"
version:          "1.0.0"
skillType:        analysis
description:      "Compares current vs. prior Kano classifications to detect
                   category migration (Attractive→Performance→Must-Be).
                   Generates decay events and strategic recommendations."
owningOntology:   "KANO-ONT"
provider:         hybrid
idempotent:       true
qualityThreshold: 70
status:           draft
```

**Inputs:**

| # | Name | Source | Required |
|---|------|--------|----------|
| I1 | `priorClassifications` | `kano:KanoClassification[]` | **Yes** |
| I2 | `currentClassifications` | `kano:KanoClassification[]` | **Yes** |
| I3 | `timeElapsed` | `xsd:duration` | **Yes** |
| I4 | `competitiveLandscape` | `vp:CompetitiveAlternative[]` | No |

**Outputs:**

| # | Name | Target |
|---|------|--------|
| O1 | `decayEvents` | `kano:KanoDecay[]` |
| O2 | `pivotTriggers` | `pmf:PivotAssessment[]` |
| O3 | `innovationTargets` | `kano:FeaturePriority[]` (updated recommendations) |

**Decay Detection Logic:**

```
For each feature with prior + current classification:
  IF prior.category ≠ current.category:
    CREATE kano:KanoDecay {
      fromCategory: prior.category,
      toCategory: current.category,
      decayPeriodMonths: timeElapsed,
      competitivePressure: assessCompetitivePressure(feature, landscape),
      recommendedAction: deriveAction(fromCategory, toCategory)
    }
    IF toCategory = "must-be" AND fromCategory = "attractive":
      CREATE pmf:PivotAssessment {
        trigger: "kano-decay-threshold",
        urgency: "high",
        recommendation: "Innovate — former delighter is now table stakes"
      }
```

---

## 9. EMC Activation Strategy

### 9.1 Category Fit (No New Category Needed)

KANO-ONT activates under 3 existing `CATEGORY_COMPOSITIONS`:

| Category | Why Kano Activates | Tier |
|----------|-------------------|------|
| **PRODUCT** | Product features need satisfaction classification for roadmap prioritisation | `optional` |
| **COMPETITIVE** | Kano categories reveal competitive positioning (must-be = parity, attractive = differentiation) | `optional` |
| **STRATEGIC** | Kano decay informs strategic planning — former delighters becoming table stakes | `optional` |

### 9.2 EMC Composer Changes Required

```javascript
// emc-composer.js — NAME_TO_PREFIX addition
KANO: 'kano:',

// DEPENDENCY_MAP addition
KANO: ['VP', 'PMF'],

// CATEGORY_COMPOSITIONS updates
PRODUCT: {
  // existing required: ['VP', 'PMF', 'PE', 'EFS', 'ORG'],
  optional: ['KPI', 'PPM', 'KANO'],  // add KANO
},
COMPETITIVE: {
  // existing required: ['CA', 'CL', 'GA', 'ORG'],
  optional: ['VSOM', 'KANO'],  // add KANO
},
STRATEGIC: {
  // existing required: ['VSOM', 'OKR', 'ORG'],
  optional: ['ORG-MAT', 'KANO'],  // add KANO
},
```

### 9.3 PFI Instance Activation

PFI instances that want Kano add `"KANO-ONT"` to their `instanceOntologies` array:

```json
// W4M-WWG example
"instanceOntologies": ["VP-ONT", "RRR-ONT", "LSC-ONT", "OFM-ONT", "KPI-ONT", "BSC-ONT", "EMC-ONT", "KANO-ONT"]
```

`constrainToInstanceOntologies()` handles normally — no special logic needed.

---

## 10. Registry Integration

### 10.1 Directory Structure

```
VE-Series/VSOM-SA/
├── BSC-ONT/           (existing)
├── INDUSTRY-ONT/      (existing)
├── REASON-ONT/        (existing)
├── MACRO-ONT/         (existing)
├── PORTFOLIO-ONT/     (existing)
└── KANO-ONT/          (NEW)
    ├── Entry-ONT-KANO-001.json
    ├── kano-ontology-v1.0.0-oaa-v6.json
    └── instance-data/
        └── kano-wwg-instance-v1.0.0.jsonld
```

### 10.2 Registry Entry (ont-registry-index.json)

```json
{
  "@id": "Entry-ONT-KANO-001",
  "name": "KANO Ontology (Customer Satisfaction Classification)",
  "namespace": "kano:",
  "status": "compliant",
  "oaaVersion": "7.0.0",
  "version": "1.0.0",
  "gatesPassed": 7,
  "gatesFailed": 0,
  "validatedDate": "2026-02-28",
  "path": "./VE-Series/VSOM-SA/KANO-ONT/Entry-ONT-KANO-001.json",
  "subSeries": "VSOM-SA"
}
```

### 10.3 Skill Registration (azlan-github-workflow/skills/)

```
azlan-github-workflow/skills/
└── pfc-kano/
    └── SKILL.md          (8-section pipeline, 8 quality gates)
```

Skill YAML frontmatter:
```yaml
---
name: pfc-kano
description: "Applies Kano satisfaction analysis to VP features, classifying by 5 categories (must-be/performance/attractive/indifferent/reverse) with decay tracking. Enriches VP→PMF bridge. Follows KANO-ONT v1.0.0."
argument-hint: "[VP output file, or PFI instance name]"
user-invocable: true
allowed-tools: "Bash(gh *),Read,Grep,Glob,Write"
---
```

---

## 11. W4M-WWG Worked Example

Applying Kano to the W4M-WWG PFI instance (UK specialist food importer, 4 supply corridors):

### 11.1 Features from VP-ONT Instance Data

| Feature (vp:Benefit) | Current gainType | Kano Classification | PMF Signal Type |
|----------------------|-----------------|---------------------|-----------------|
| Proactive disruption notification (5-day advance) | Required | **Must-Be** — Absence = deal-killer for restaurant chains | Retention (if absent, churn >20%) |
| Cross-corridor substitution automation | Desired | **Attractive** — Competitors offer manual only; automation is unexpected value | Organic growth (word-of-mouth from surprise value) |
| Real-time shelf-life transparency | Expected | **Performance** — More granularity = higher satisfaction, linear | NPS (clarity correlates with recommendation) |
| Margin-protected fulfilment with change-cost visibility | Desired | **Performance** — Finance teams compare dashboards across suppliers | WTP (15-20% premium for transparency) |

### 11.2 Segment Variation

| Feature | Restaurant Chains | Wholesalers | Foodservice |
|---------|------------------|-------------|-------------|
| Disruption notification | Must-Be | Performance | Must-Be |
| Cross-corridor substitution | Attractive | Performance | Attractive |
| Shelf-life transparency | Performance | Must-Be | Must-Be |
| Margin tracking | Attractive | Performance | Performance |

### 11.3 Decay Projection (18-Month Horizon)

```
Year 0:  "Cross-corridor substitution" → kano:Attractive (delighter)
Year 1:  Competitors adopt similar → kano:Performance (expected to improve)
Year 2:  Industry standard → kano:MustBe (table stakes)

Action: kano:KanoDecay {
  fromCategory: "attractive",
  toCategory: "performance",
  decayPeriodMonths: 12,
  competitivePressure: "High",
  recommendedAction: "accelerate"
}
→ Triggers pmf:PivotAssessment: "Innovate next differentiator before cross-corridor becomes table stakes"
```

---

## 12. Implementation Roadmap

### 12.1 Stories (Scoped Under Epic 49)

| Story | Title | Size | Dependencies | Deliverable |
|-------|-------|------|-------------|-------------|
| **S49.X.1** | Define KANO-ONT v1.0.0 ontology (OAA v7.0.0 compliant) | M | VP-ONT v1.2.3, PMF-ONT v2.0.0 | `kano-ontology-v1.0.0-oaa-v6.json`, `Entry-ONT-KANO-001.json` |
| **S49.X.2** | Register KANO-ONT in ont-registry-index.json | S | S49.X.1 | Registry entry, validation gates |
| **S49.X.3** | Build pfc-kano SKILL.md (8-section pipeline) | M | S49.X.1 | `azlan-github-workflow/skills/pfc-kano/SKILL.md` |
| **S49.X.4** | Update EMC composer — NAME_TO_PREFIX, DEPENDENCY_MAP, CATEGORY_COMPOSITIONS | S | S49.X.2 | `emc-composer.js` updates |
| **S49.X.5** | W4M-WWG Kano instance data — classify 4 VP features | M | S49.X.1, S49.X.3 | `kano-wwg-instance-v1.0.0.jsonld` |
| **S49.X.6** | Visualiser integration — KANO-ONT renders in VSOM-SA cluster with enrichment edges | M | S49.X.4 | Visualiser graph rendering |
| **S49.X.7** | Vitest test suite — Kano classification, decay detection, EMC activation | M | S49.X.4, S49.X.5 | Test coverage for Kano |

### 12.2 Acceptance Criteria

- [ ] KANO-ONT passes all 7 OAA compliance gates
- [ ] 6 entities, 1 enum, 7 business rules, 5 join patterns defined
- [ ] pfc-kano skill invocable via `/azlan-github-workflow:pfc-kano`
- [ ] EMC composer activates KANO under PRODUCT/COMPETITIVE/STRATEGIC categories
- [ ] W4M-WWG instance has 4 features classified with segment variation
- [ ] Decay detection produces `pmf:PivotAssessment` when category migration detected
- [ ] Visualiser renders KANO-ONT nodes in VSOM-SA cluster alongside BSC/INDUSTRY/REASON
- [ ] All new tests pass (`npx vitest run`)

---

## 13. Consolidated VE Chain Position

With KANO-ONT activated, the complete VE-Series chain and VSOM-SA lens architecture:

```
VE MAIN CHAIN:
  ORG-CONTEXT → VSOM → OKR → VP → [KANO enrichment] → PMF → KPI → EFS → PPM → PE

VSOM-SA PARALLEL LENSES (Layer 1→5 + Kano):
  L1: MACRO-ONT    (mac:)  — PESTEL, Scenarios       → VSOM.Vision
  L2: INDUSTRY-ONT (ind:)  — Porter 5F, SWOT/TOWS    → VSOM.Strategy
  L3: BSC-ONT      (bsc:)  — Balanced Scorecard       → VSOM.Objectives → KPI
  L4: REASON-ONT   (rsn:)  — MECE, Logic Trees        → Cross-ontology synthesis
  L5: PORTFOLIO-ONT(pfl:)  — BCG, Three Horizons      → Investment prioritisation
  L6: KANO-ONT     (kano:) — Satisfaction classification → VP↔PMF enrichment

VSOM-SC (Strategy Communication):
  NARRATIVE-ONT, CASCADE-ONT, CULTURE-ONT, VIZSTRAT-ONT
```

### Key Distinction — KANO as Layer 6

KANO-ONT is positioned as **L6** in the VSOM-SA concentric model:
- L1-L5 feed **into** VSOM (analysis → strategy definition)
- L6 operates **between** VP and PMF (analysis → value validation)
- Same pattern (parallel lens, invoked as needed, pe:Skill-driven) but different insertion point

This follows the S23 reframe: Kano doesn't feed the VSOM spine directly. It enriches the **downstream VE chain** where value meets market.

---

## 14. Cross-Reference — Sessions S19, S21, S23

| Session | Key Finding | How This Briefing Incorporates It |
|---------|-------------|----------------------------------|
| **S19** | 7 entities pass CQ-VOC (§19 of EFS briefing). 4 JPs defined. pe:Skill registration proposed. VP→KANO→PMF lineage. | §5 defines 6 entities (merged SatisfactionCurve into core set). §8 defines 2 skills. §5.5 defines 5 JPs. |
| **S21** | Kano→PMF→Customer Engagement value loop. Decay tracking as "strategic time bomb". W4M-WWG worked example. | §4 captures benefits B2 (decay warning), B5 (PMF signal quality). §11 provides W4M-WWG worked example. §8.2 formalises decay assessment skill. |
| **S23** | Kano reframed as VSOM-SA parallel lens (NOT chain link). L6S analogy. 60% existing coverage. VSOM-SA placement. | §3.3 adopts L6S analogy. §3.4 confirms correct model (parallel lens, not chain link). §6 documents 60/40 coverage split. §10 places in VSOM-SA directory. |

---

## 15. Risk & Mitigations

| Risk | Impact | Mitigation |
|------|--------|-----------|
| Over-engineering — Kano is simple in concept, 6 entities might be too many | Medium | Entities are lean (no nested hierarchies). Can defer SatisfactionCurve to v1.1.0 if 5 entities suffice. |
| Adoption friction — PFI instances need to add `KANO-ONT` to instanceOntologies | Low | EMC composer change is additive (optional tier). No PFI instance forced to adopt. |
| Data quality — Kano surveys need real customer data, not assumptions | High | BR-KANO-003 enforces confidence threshold. BR-KANO-006 recommends sample size. Skill allows evidence-based synthesis when survey data unavailable. |
| Decay detection false positives — market noise vs. real category migration | Medium | SKILL-KANO-002 requires paired prior+current classifications with evidence. competitivePressure field provides context. |

---

*Classification: CONFIDENTIAL — Strategic Planning Asset*
*Sessions in scope: S19 (CQ Evaluation), S21 (Value Loop), S23 (Parallel Lens Reframe)*
*Supersedes: EFS Briefing §19-23 (discussion-only sketches — this briefing is the formal definition)*
