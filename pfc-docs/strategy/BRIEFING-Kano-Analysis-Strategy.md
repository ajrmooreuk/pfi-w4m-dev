# BRIEFING: Kano Analysis — Strategy & Practitioner Guide

**Date:** 2026-02-28
**Author:** Azlan EA-AAA
**Type:** Strategy Analysis Methodology Briefing
**Status:** APPROVED FOR USE
**Audience:** Strategy consultants, product managers, AI agent developers, PFI instance owners
**Companion:** `BRIEFING-KANO-ONT-Satisfaction-Classification-Strategy.md` (ontology & skill architecture)
**Sessions:** S19 (CQ Evaluation), S21 (Value Loop), S23 (Parallel Lens Reframe)

---

## 1. What Is Kano Analysis?

The Kano Model (Noriaki Kano, 1984) is a customer satisfaction classification framework that reveals the **non-linear relationship** between feature implementation and customer satisfaction. It answers a question that most product strategies ignore:

> **Not all features contribute equally to satisfaction — and some features that customers say they want will not move the needle at all.**

Traditional product thinking assumes a linear model: more features = more satisfaction. Kano disproves this. A feature's contribution depends on its **category** — and that category changes over time.

### 1.1 Why Linear Thinking Fails

| Assumption | Reality |
|-----------|---------|
| "If we build it, they'll be satisfied" | Must-Be features prevent dissatisfaction but don't create satisfaction |
| "More investment = more satisfaction" | Attractive features create disproportionate satisfaction with minimal investment |
| "All features contribute equally" | Indifferent features consume budget with zero satisfaction impact |
| "Our differentiators are permanent" | Attractive → Performance → Must-Be decay is inevitable |

### 1.2 The Core Insight

Customer satisfaction is governed by **five distinct response curves**, not one:

```
Satisfaction
    ▲
    │            ╱ Attractive (exponential delight)
    │          ╱
    │        ╱     ╱ Performance (linear, more = better)
    │      ╱     ╱
    │    ╱     ╱
────┼──╱─────╱──────────── Indifferent (flat, no effect)
    │╱     ╱
    │    ╱          Must-Be (asymptotic — absence = dissatisfaction,
    │  ╱                     presence = assumed)
    │╱
    ▼
   Dissatisfaction
                    ──────────────────────────────►
                    Feature Implementation Level
```

---

## 2. The Five Kano Categories

### 2.1 Must-Be (Basic / Threshold)

**Curve:** Asymptotic — diminishing returns
**If present:** Assumed, not appreciated. No satisfaction increase beyond baseline.
**If absent:** Severe dissatisfaction. Deal-killer. Churn trigger.
**Customer voice:** Customers rarely mention these — they're invisible when present, catastrophic when missing.

**Strategic action:** Maintain. Never over-invest. Ensure parity with competitors.

**Examples:**
- Email delivery in a SaaS product
- SSL encryption on a checkout page
- W4M-WWG: Proactive disruption notification — restaurant chains reject suppliers without it

**PMF signal:** Retention. Must-Be gaps correlate directly with churn rate.

---

### 2.2 Performance (Linear / One-Dimensional)

**Curve:** Linear — more is proportionally better
**If present:** Satisfaction increases proportionally with implementation quality.
**If absent:** Dissatisfaction increases proportionally.
**Customer voice:** "I wish it were faster / cheaper / more detailed."

**Strategic action:** Outperform competitors. This is the battleground. Willingness-to-pay elasticity is highest here.

**Examples:**
- Page load speed (every 100ms matters)
- Dashboard granularity (more detail = higher satisfaction)
- W4M-WWG: Real-time shelf-life transparency — more granularity = more trust

**PMF signal:** NPS. Performance features drive recommendation willingness.

---

### 2.3 Attractive (Excitement / Delighter)

**Curve:** Exponential — disproportionate delight
**If present:** Creates surprise, loyalty, word-of-mouth. Customers didn't know they wanted it.
**If absent:** No dissatisfaction — customers don't miss what they never expected.
**Customer voice:** "I didn't know I needed this, but now I can't imagine working without it."

**Strategic action:** Sprint to market. First-mover advantage. These create competitive moats — until they decay.

**Examples:**
- AI-powered recommendations before competitors offer them
- Automatic cross-corridor substitution when competitors require manual intervention
- W4M-WWG: Cross-corridor substitution automation — competitors offer manual only

**PMF signal:** Organic growth. Attractive features drive word-of-mouth and viral adoption.

---

### 2.4 Indifferent

**Curve:** Flat — no effect in either direction
**If present:** No satisfaction change.
**If absent:** No dissatisfaction change.
**Customer voice:** Silence. No feedback. No engagement.

**Strategic action:** Deprioritise. Every pound spent here is wasted. Redirect to Performance or Attractive investment.

**Examples:**
- Internal architecture improvements invisible to the customer
- Configuration options nobody uses
- Backend technology choices the customer never sees

**PMF signal:** None. Indifferent features don't appear in any PMF metric.

---

### 2.5 Reverse (Anti-Feature)

**Curve:** Inverse — presence hurts
**If present:** Satisfaction decreases. Customer actively dislikes the feature.
**If absent:** Satisfaction maintained or improved.
**Customer voice:** "Why did you add this? It makes things worse."

**Strategic action:** Eliminate or segment-gate. If some segments want it and others don't, make it optional.

**Examples:**
- Gamification in an enterprise B2B tool
- Forced social sharing in a privacy-sensitive context
- Auto-play notifications in a focused workflow

**PMF signal:** Negative NPS. Reverse features generate complaints and support burden.

---

## 3. Strategic Rationale — Why Kano for PFC

### 3.1 The VP→PMF Precision Problem

The PFC VE chain runs: `VSOM → OKR → VP → PMF → KPI → EFS → PPM → PE`

At the VP→PMF junction, the current bridge (JP-PMF-001) provides a binary signal:

```
VP: "Here is our value proposition with 5 benefits"
PMF: "fitScore: 0.72, retention: 38%, NPS: 42"
```

The missing layer: **Which of those 5 benefits drove the 0.72? Which caused the 38% retention gap? Which will decay by next quarter?**

Kano provides feature-level attribution:

```
VP: "Here are 5 benefits"
  ↓
KANO: "2 are Must-Be (retention drivers), 2 are Performance
       (NPS drivers), 1 is Attractive (organic growth driver,
       decaying to Performance within 12 months)"
  ↓
PMF: "fitScore: 0.72 BECAUSE Must-Be coverage is 100%,
      Performance is competitive, Attractive is eroding"
```

### 3.2 Three Strategic Questions Kano Answers

| Question | Without Kano | With Kano |
|----------|-------------|-----------|
| **Where to invest?** | Spread budget across all features | Invest in Performance (WTP elasticity) and Attractive (differentiation), maintain Must-Be, eliminate Indifferent |
| **What's at risk?** | Unknown until competitors catch up | Decay tracking shows Attractive→Performance migration 12 months before competitors match |
| **Why did PMF stall?** | "Customers aren't satisfied enough" | "Missing 1 Must-Be feature (churn driver) and 2 Performance features below competitor threshold" |

### 3.3 Alignment to VSOM Six Strategies

| PFC Strategy | Kano Contribution |
|-------------|-------------------|
| **S1: Graph-First Architecture** | Kano classifications as graph nodes with enrichment edges to VP/PMF |
| **S2: VE-Driven Everything** | Kano is a VSOM-SA analytical lens enriching the VE chain |
| **S4: Instance & Client Customisation** | Segment-specific Kano profiles per PFI instance — same feature, different category per segment |
| **S5: UI/UX Design-to-Production** | Kano priority rankings drive feature roadmap in Figma/Pencil workflows |

---

## 4. How to Perform Kano Analysis

### 4.1 The Four-Phase Process

```
Phase 1          Phase 2           Phase 3             Phase 4
SURVEY DESIGN    DATA COLLECTION   CLASSIFICATION      DECAY ASSESSMENT

F/D question     Sample ≥30        Evaluation          Compare prior
pairs per        per segment       matrix mapping      classifications
feature                            F/D → Category      to current
     │                │                  │                    │
     ▼                ▼                  ▼                    ▼
kano:KanoQuestion  kano:KanoSurvey  kano:KanoClassification  kano:KanoDecay
```

### 4.2 Phase 1 — Survey Design (Functional/Dysfunctional Pairs)

For each feature, create two questions:

| Type | Template | Example (W4M-WWG: Cross-corridor substitution) |
|------|----------|------------------------------------------------|
| **Functional** (positive) | "If [feature] were available, how would you feel?" | "If automatic cross-corridor substitution were available when your primary corridor is disrupted, how would you feel?" |
| **Dysfunctional** (negative) | "If [feature] were NOT available, how would you feel?" | "If you had to manually find alternative corridors when your primary supply is disrupted, how would you feel?" |

**Response scale (5-point standard):**
1. I like it
2. I expect it
3. I am neutral
4. I can tolerate it
5. I dislike it

### 4.3 Phase 2 — Data Collection

| Requirement | Threshold | Rationale |
|-------------|-----------|-----------|
| **Sample size** | ≥30 per segment | Statistical validity for classification confidence |
| **Segment isolation** | Separate surveys per ICP | Same feature → different category across segments |
| **Timing** | Quarterly or per major release | Categories decay; stale data = wrong investment decisions |
| **Channel** | Customer interviews, structured surveys, or evidence synthesis | Agent-assisted synthesis acceptable when direct survey data unavailable |

### 4.4 Phase 3 — Classification (Kano Evaluation Matrix)

Cross-reference functional × dysfunctional responses:

| | **Dysfunctional: Like** | **Dysfunctional: Expect** | **Dysfunctional: Neutral** | **Dysfunctional: Tolerate** | **Dysfunctional: Dislike** |
|---|---|---|---|---|---|
| **Functional: Like** | Q | A | A | A | O |
| **Functional: Expect** | R | I | I | I | M |
| **Functional: Neutral** | R | I | I | I | M |
| **Functional: Tolerate** | R | I | I | I | M |
| **Functional: Dislike** | R | R | R | R | Q |

**Legend:** M = Must-Be, O = One-dimensional (Performance), A = Attractive, I = Indifferent, R = Reverse, Q = Questionable (contradictory response — re-examine)

**Classification confidence:**
- Strong: >70% of respondents agree on category
- Moderate: 50-70% agreement
- Weak: <50% — feature may be segment-dependent or poorly defined

### 4.5 Phase 4 — Decay Assessment

Compare current classifications against prior period:

| Transition | What It Means | Strategic Response |
|-----------|---------------|-------------------|
| Attractive → Performance | Competitors caught up. Feature is expected now. | Innovate the next differentiator. |
| Performance → Must-Be | Feature is table stakes. No competitive advantage. | Lock in. Reduce cost. Move investment elsewhere. |
| Attractive → Must-Be | Rapid commoditisation. Market matured fast. | Urgent innovation needed. |
| Must-Be → Indifferent | Market shifted. Feature no longer relevant. | Consider deprecation. |
| Indifferent → Performance | Customer needs evolved. Previously irrelevant feature now matters. | Invest — late-mover disadvantage risk. |

**Decay velocity factors:**
- Competitive pressure (high = fast decay)
- Market maturity (mature = faster convergence to Must-Be)
- Technology commoditisation (open-source, APIs = faster imitation)
- Customer education (more informed customers have higher expectations)

---

## 5. Kano in the VE Chain — Integration Strategy

### 5.1 Upstream Input (From VP-ONT)

KANO-ONT consumes these VP-ONT entities:

| VP Entity | What Kano Uses It For |
|-----------|----------------------|
| `vp:Benefit` | Primary classification target — each benefit → kano:KanoClassification |
| `vp:Solution` | Feature-level grouping for benefits |
| `vp:IdealCustomerProfile` | Survey targeting — different ICPs may classify same feature differently |
| `vp:Gain` | `gainType` provides initial hypothesis (Required ≈ Must-Be, Unexpected ≈ Attractive) |
| `vp:CompetitiveAlternative` | Competitive baseline for decay assessment |
| `vp:ValidationEvidence` | Evidence input when direct survey data unavailable |

### 5.2 Downstream Output (To PMF-ONT)

KANO-ONT produces these PMF-ready signals:

| Kano Output | PMF Entity It Feeds | Signal Type |
|-------------|-------------------|-------------|
| Must-Be coverage gaps | `pmf:MarketValidation.retentionRate` | If Must-Be missing → churn prediction |
| Performance ranking vs. competitors | `pmf:MarketValidation.netPromoterScore` | Performance gap → NPS drag |
| Attractive features present | `pmf:MarketValidation.organicGrowthRate` | Attractive features → word-of-mouth multiplier |
| `kano:FeaturePriority` | `pmf:PMFIteration.hypothesis` | Next iteration should target highest-priority Kano gap |
| `kano:KanoDecay` | `pmf:PivotAssessment` | Decay event → pivot trigger |
| `kano:SatisfactionCurve` | `kpi:KPI` (NPS, CSAT, CES) | Satisfaction model → measurable KPI |

### 5.3 The Value Loop (S21)

Kano creates a continuous feedback loop, not a one-time classification:

```
1. SURVEY    kano:KanoSurvey targets vp:IdealCustomerProfile
     │
     ▼
2. CLASSIFY  kano:KanoClassification maps feature → category
     │
     ▼
3. PRIORITISE kano:FeaturePriority ranks investment (invest/maintain/deprioritise)
     │
     ▼
4. VALIDATE  pmf:ValidationSignal enriched with feature-level attribution
     │
     ▼
5. DECAY     kano:KanoDecay detects category migration over time
     │
     ▼
6. RE-SURVEY Loop back to step 1 with updated competitive landscape
```

**Cadence:** Quarterly for active PFI instances. Semi-annually for stable products. Triggered on major competitive shifts.

---

## 6. Kano Applied to PFI Instances

### 6.1 Which Instances Benefit Most?

| PFI Instance | Kano Readiness | Rationale |
|-------------|---------------|-----------|
| **W4M-WWG** | **High** — immediate | 4 VP features defined, 3 requirement scopes (PRODUCT, FULFILMENT, COMPETITIVE) all benefit from satisfaction classification |
| **BAIV** | **High** — ready | Lead instance (80% readiness), 16 agents, complex feature set needing prioritisation |
| **AIRL-CAF-AZA** | **Medium** — next wave | AI readiness features need Must-Be/Attractive classification for market positioning |
| **ANTQ** | **Medium** — after VP data | CRT instance data seeded, needs VP features before Kano can classify |
| **VHF** | **Low** — too early | PoC stage. Kano is premature before basic VP/PMF validation |

### 6.2 W4M-WWG Deep Dive

**Four features classified across three customer segments:**

| Feature | Restaurant Chains | Wholesalers | Foodservice |
|---------|------------------|-------------|-------------|
| Proactive disruption notification (5-day) | **Must-Be** | Performance | **Must-Be** |
| Cross-corridor substitution automation | **Attractive** | Performance | **Attractive** |
| Real-time shelf-life transparency | Performance | **Must-Be** | **Must-Be** |
| Margin-protected fulfilment dashboard | **Attractive** | Performance | Performance |

**Strategic insights from segment variation:**
- **Restaurant chains** are the most demanding segment — 2 Must-Be requirements vs. 1 for wholesalers
- **Cross-corridor substitution** is the primary differentiator for restaurants and foodservice — invest heavily here
- **Shelf-life transparency** is table stakes for wholesalers and foodservice but a performance feature for restaurants — segment-specific messaging needed
- **Margin dashboard** is a delighter for restaurants (never had it) but expected by wholesalers (finance teams compare) — feature the same capability differently per segment

**Investment recommendation matrix:**

| Feature | Action | Budget Allocation | Expected Impact |
|---------|--------|-------------------|-----------------|
| Disruption notification | Maintain (Must-Be) | 15% — keep reliable, don't over-invest | Retention protection |
| Cross-corridor substitution | Invest heavily (Attractive) | 40% — sprint before competitors imitate | Organic growth + WTP premium (15-20%) |
| Shelf-life transparency | Improve (Performance) | 25% — outperform competitor dashboards | NPS improvement |
| Margin dashboard | Enhance (mixed) | 20% — segment-specific presentation | Segment-targeted satisfaction |

### 6.3 Decay Projection — 18-Month Horizon

```
TODAY (Month 0):
  Cross-corridor substitution → Attractive (no competitor offers automation)

MONTH 6-9:
  Competitor A announces manual-assisted substitution
  → competitive pressure: Rising
  → Recommended: Accelerate innovation on substitution algorithms

MONTH 12:
  Two competitors offer basic automation
  → kano:KanoDecay { from: "attractive", to: "performance" }
  → Cross-corridor substitution is now a competitive battleground, not a differentiator
  → Action: Outperform on speed, accuracy, cost optimisation

MONTH 18-24:
  Industry adopts automation as standard practice
  → kano:KanoDecay { from: "performance", to: "must-be" }
  → Table stakes. Must have next differentiator ready.
  → Triggers: pmf:PivotAssessment { urgency: "high" }
```

**Strategic implication:** W4M-WWG has approximately 12 months before its primary differentiator becomes a competitive battleground. The next Attractive feature must be in development NOW.

---

## 7. Kano and the Blue Ocean Feedback Loop

From S20, Kano and Blue Ocean Strategy form a strategic feedback cycle:

```
Blue Ocean: CREATE new value curve
    │
    ▼
Kano classifies new feature as "Attractive"
    │
    ▼
Market adopts. Competitors imitate.
    │
    ▼
Kano Decay: Attractive → Performance → Must-Be
    │
    ▼
Blue Ocean: Need new CREATE action
    │
    ▼
(cycle repeats)
```

**The insight:** Blue Ocean tells you WHAT to create. Kano tells you HOW LONG you have before it decays. Together, they drive a continuous innovation pipeline with measurable decay velocity.

---

## 8. Kano and BSC — Strategic Measurement

Kano classifications map directly to BSC perspectives:

| BSC Perspective | Kano Category | KPI Type |
|----------------|--------------|----------|
| **Customer** | Must-Be coverage % | Retention rate, churn rate |
| **Customer** | Performance ranking vs. competitors | NPS, CSAT |
| **Customer** | Attractive feature count | Organic growth rate, WOM score |
| **Internal Process** | Classification cadence | Surveys completed per quarter |
| **Internal Process** | Decay detection speed | Months of advance warning before migration |
| **Learning & Growth** | Kano survey capability | Team skill in survey design, data analysis |
| **Financial** | Investment alignment | % of budget aligned to Kano priority (invest/maintain/deprioritise) |

---

## 9. Common Pitfalls

| Pitfall | Why It Happens | How to Avoid |
|---------|---------------|-------------|
| **Treating all features as Performance** | Default linear thinking. "More = better" bias. | Use the F/D matrix rigorously. If the dysfunctional response is "dislike," it might be Must-Be, not Performance. |
| **Ignoring segment variation** | One survey for all customers. | Always segment by ICP. Same feature → different category across segments. |
| **Classifying once, never updating** | Kano is seen as a one-time exercise. | Decay is inevitable. Schedule quarterly re-classification. |
| **Over-investing in Must-Be** | "Customers say they need this!" | Must-Be investment has diminishing returns. Get to parity, then stop. |
| **Ignoring Indifferent** | Features on the roadmap stay on the roadmap. | Indifferent features are sunk cost. Deprioritise ruthlessly. |
| **Assuming Attractive features last** | "This is our moat." | Track competitive landscape. Every Attractive feature has a decay timeline. |
| **Confusing VP gainType with Kano category** | Required ≈ Must-Be, Unexpected ≈ Attractive — close but not the same. | VP's gainType is the customer's *stated* preference. Kano is their *revealed* satisfaction response. The two often diverge. |

---

## 10. Kano as a Parallel Lens — The Architectural Principle

### 10.1 What "Parallel Lens" Means (S23 Decision)

Kano is NOT a step in the VE chain. It is an analytical lens invoked when needed:

```
WRONG (chain link):
  VP → KANO → PMF       ← implies every VP must go "through" Kano

CORRECT (parallel lens):
  VP ──────────────→ PMF        ← chain operates independently
       ↑                 ↑
       └── KANO ─────────┘      ← invoked as enrichment when needed
```

**A product CAN achieve PMF without formal Kano analysis.** Many do. But Kano makes the VP→PMF signal dramatically more precise.

### 10.2 The L6S Analogy

Same pattern as Lean Six Sigma in PE-Series:
- L6S is applied TO processes — processes don't go "through" DMAIC
- Kano is applied TO features — features don't go "through" Kano
- Both are pe:Skill-driven analytical capabilities with structured I/O
- Both enrich the main chain without interrupting it
- Both have their own entities (Control Charts ↔ SatisfactionCurves)
- Both live in a sub-series (PE DMAIC ↔ VE VSOM-SA)

### 10.3 When to Invoke Kano

| Trigger | Action |
|---------|--------|
| New VP formulated (pfc-vp output) | Classify features before PMF validation begins |
| PMF iteration shows stalled metrics | Diagnose: missing Must-Be? Underperforming Performance? |
| Competitor launches similar feature | Assess decay risk on current Attractive features |
| Entering new customer segment | Classify features for new segment (categories likely differ) |
| Quarterly strategy review | Re-classify all features, detect decay, update priority rankings |
| Product roadmap planning | Use Kano priorities to drive investment allocation |

---

## 11. Summary — Kano's Place in PFC Strategy

```
VSOM STRATEGY
    │
    ├── S2: VE-Driven Everything
    │       │
    │       └── VSOM-SA Parallel Lenses
    │               │
    │               ├── L1: MACRO    (where is the world going?)
    │               ├── L2: INDUSTRY (what forces shape our market?)
    │               ├── L3: BSC      (how do we measure strategy?)
    │               ├── L4: REASON   (how do we think about it?)
    │               ├── L5: PORTFOLIO(where do we invest?)
    │               └── L6: KANO     (how do customers respond?)
    │                        │
    │                        └── VP ←enriches→ PMF
    │
    └── VALUE CHAIN
            │
            VP → PMF → KPI → EFS → PPM → PE
```

**Kano Analysis answers the question every product strategy eventually faces:**

> "We built what customers asked for. Why aren't they satisfied?"

Because not all features are equal. Kano tells you which ones matter, which ones don't, and which ones are about to stop mattering.

---

*Classification: CONFIDENTIAL — Strategic Planning Asset*
*Based on: Noriaki Kano (1984), "Attractive Quality and Must-Be Quality"*
*PFC Adaptation: Sessions S19, S21, S23 — consolidated 2026-02-28*
