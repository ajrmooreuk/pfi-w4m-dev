# Epic 41: OFM-ONT -- Order Fulfilment Management Ontology

## Analysis Briefing & Planning Document

| Field | Value |
|---|---|
| **Epic** | 41 |
| **Date** | 2026-02-20 |
| **Status** | DRAFT -- Briefing for Analysis & Planning |
| **Classification** | CONFIDENTIAL -- Strategic Planning Asset |
| **Ontology Compliance** | Schema.org Grounded, OAA v6.1.0, BSC Framework |
| **Series** | PE-Series (alongside LSC-ONT) |
| **Namespace** | `ofm:` at `https://oaa-ontology.org/v6/order-fulfilment/` |

---

## Executive Summary

LSC-ONT v1.1.0 models the **logistics operator's view** of supply chains -- corridors, shipments, chain nodes, incidents, escalation, risk assessment, impact assessment, cascade effects, and predictive patterns. It answers: *What happened to the cargo?*

What's missing is the **customer/commercial view**: *What did the customer order? Did we meet our SLA? What did it cost to deliver? Did we make a profit? When the customer complained, what was the operational root cause? How does fulfilment performance feed back into value proposition validation and product-market fit?*

**OFM-ONT (Order Fulfilment Management)** bridges this gap. It sits in PE-Series alongside LSC-ONT and provides:
- **10 core entities** modelling the commercial overlay on logistics operations
- **15 internal relationships** within the order lifecycle
- **8 LSC-ONT bridge relationships** linking orders to shipments, milestones to nodes, breaches to incidents
- **9 VE-Series bridge relationships** linking complaints to VP problems, satisfaction to KPI metrics, margins to PMF validation
- **7 join patterns** encoding the automated assessment flow, profit optimisation chain, and closed-loop feedback
- **12 business rules** enforcing SLA detection, margin calculation, complaint accountability, and VP-RRR alignment

**The core thesis:** An importer or exporter cannot optimise profit or minimise customer dissatisfaction by managing logistics alone. They need a **commercial layer** that translates operational events (delays, incidents, breaches) into customer-facing impacts (SLA failures, complaints, margin erosion) and closes the loop from complaint back to systemic prevention.

**VP-RRR alignment convention applied throughout:**
- `vp:Problem` → `rrr:Risk` (fulfilment failures are risks to the customer)
- `vp:Solution` → `rrr:Requirement` (remediation actions are requirements to build)
- `vp:Benefit` → `rrr:Result` (customer satisfaction is the measurable result)

---

## 1. Current State Assessment

### 1.1 What We Have (Assets)

| Asset | Location | Status |
|---|---|---|
| LSC-ONT v1.1.0 | `PE-Series/LSC-ONT/` | 18 entities, 41 rels, 20 rules, 9 JPs. OAA v6.1.0 compliant |
| LSC Instance Data (AU-UK Meat) | `PE-Series/LSC-ONT/lsc-instances-au-uk-meat-v0.1.0.json` | Prototype v0.1.0 -- corridor, 9 nodes, 12 parties |
| VP-ONT v1.2.3 | `VE-Series/VP-ONT/` | Problem, PainPoint, Gain, Solution, Benefit entities |
| RRR-ONT v4.0.0 | `VE-Series/RRR-ONT/` | 48+ roles L0-L4, RACI, RBAC |
| PMF-ONT v2.0.0 | `VE-Series/PMF-ONT/` | ProductMarketFit, MarketValidation, fitScore |
| KPI-ONT v1.0.0 | `VE-Series/KPI-ONT/` | Vision→Strategy→SO→O→KR→KPI→Metric cascade |
| CRT-ONT v1.0.0 | `VE-Series/CRT-ONT/` | CustomerRole taxonomy -- B2B/B2C/B2B2C |
| EMC-ONT v4.0.0 | `Orchestration/EMC-ONT/` | 8 categories, 7 composition rules, 5 graph patterns |
| Join Pattern Registry | `join-pattern-registry.json` | 91 registered patterns |
| Registry Index v9.4.0 | `ont-registry-index.json` | 46 ontologies, 40 compliant |

### 1.2 Gap Analysis

| Gap | Description | Blocks |
|---|---|---|
| **No customer order model** | LSC tracks shipments, not purchase orders. No link from what the customer ordered to what was physically shipped | Customer experience visibility |
| **No SLA framework** | Delivery promises, temperature guarantees, and documentation timelines have no ontological representation | Automated breach detection |
| **No landed cost model** | Full cost build-up (FOB + freight + duties + inland + handling) is not captured | Profit optimisation |
| **No margin analysis** | Revenue vs cost per order/corridor is not modelled | P&L visibility per corridor |
| **No complaint-to-root-cause tracing** | Customer complaints cannot be linked back to specific LSC incidents or bottlenecks | Closed-loop learning |
| **No customer notification model** | Milestone-triggered communications to customers are not represented | Proactive customer management |
| **No satisfaction feedback loop** | NPS/CSAT data cannot feed back into VP validation or PMF scoring | Market fit validation |
| **No EMC FULFILMENT category** | EMC-ONT has no requirement category that activates the logistics-to-customer bridge | Instance composition for importers |

---

## 2. VSOM Framework

### 2.1 Vision

> **Make every import/export order fully traceable from purchase to delivery, every SLA measurable and automatically enforced, every cost visible to the penny, every complaint traceable to its operational root cause, and every remediation measurable in both customer satisfaction and margin impact -- bridging the logistics operator's world to the customer's experience through a single, graph-queryable ontology.**

### 2.2 Three Strategies

| # | Strategy | Priority | Focus |
|---|---|---|---|
| **S1** | Customer-Centric Fulfilment Visibility | 1 -- Foundation | Expose the customer's view of logistics: order milestones, SLA tracking, proactive notifications. PurchaseOrder as the hub entity linking to LSC shipments via `fulfilledByShipment`. |
| **S2** | Profit Optimisation Through Landed Cost Transparency | 2 -- Commercial | Full cost build-up per order (12+ cost categories), margin analysis, corridor profitability. LandedCost → MarginAnalysis → PMF validation chain. |
| **S3** | Closed-Loop Learning | 3 -- Continuous Improvement | Complaint → root cause incident → bottleneck → risk mitigation → predictive pattern update. Every customer complaint drives systemic improvement. |

### 2.3 Strategy-to-Ontology Mapping

| Strategy | Primary OFM Entities | LSC Bridge | VE Bridge |
|---|---|---|---|
| S1 Visibility | PurchaseOrder, OrderMilestone, CustomerNotification, SLABreach | Shipment, ChainNode, Incident | CRT CustomerRole, KPI Metric |
| S2 Profit | LandedCost, MarginAnalysis | ImpactAssessment | PMF ProductMarketFit |
| S3 Learning | ComplaintCase, RemediationAction, CustomerSatisfaction | Incident, Bottleneck, RiskMitigation, PredictivePattern | VP Problem/Solution/Benefit, RRR Role |

---

## 3. Balanced Scorecard Objectives (5 Perspectives)

### 3.1 Financial

| ID | Objective | Target | Leading Metric | Lagging Metric |
|---|---|---|---|---|
| OBJ-F1 | Reduce margin erosion from SLA breaches | Breach-related cost < 2% of revenue | SLA at-risk early detection rate | Breach cost as % of corridor revenue |
| OBJ-F2 | Achieve full landed cost visibility | 100% of delivered orders have complete cost build-up | Cost category coverage per order | Landed cost variance from estimate |
| OBJ-F3 | Reduce remediation costs through prevention | Remediation spend down 30% YoY | PredictivePattern activation count | Total remediation cost per quarter |

### 3.2 Customer

| ID | Objective | Target | Leading Metric | Lagging Metric |
|---|---|---|---|---|
| OBJ-C1 | Reduce complaint-to-resolution time | Mean < 48 hours | Complaint auto-assignment rate | Mean resolution time |
| OBJ-C2 | Achieve high fulfilment NPS | NPS >= 50 | Post-delivery survey response rate | NPS score (rolling 90-day) |
| OBJ-C3 | Proactive delay notifications | 100% of SLA-at-risk orders notified before breach | Predictive pattern confidence > 0.7 coverage | % of breaches with prior notification |

### 3.3 Internal Process

| ID | Objective | Target | Leading Metric | Lagging Metric |
|---|---|---|---|---|
| OBJ-P1 | Automated SLA breach detection | Detection within 1 hour of milestone variance | Milestone data freshness (minutes) | Mean time to breach detection |
| OBJ-P2 | Root cause linkage for complaints | 100% of resolved complaints linked to LSC incident/bottleneck | Complaint investigation start time | % of complaints with root cause link |
| OBJ-P3 | Margin analysis on all delivered orders | 100% coverage within 5 business days of delivery | Order completion event trigger rate | Margin analysis coverage % |

### 3.4 Learning & Growth

| ID | Objective | Target | Leading Metric | Lagging Metric |
|---|---|---|---|---|
| OBJ-L1 | Complaint feedback drives pattern updates | PredictivePattern updated within 30 days of new complaint cluster | Complaint cluster detection rate | Patterns updated from complaint data |
| OBJ-L2 | Cross-domain analyst capability | Logistics + commercial analysts trained on OFM-LSC bridge | Training completion rate | Cross-domain query usage |

### 3.5 Stakeholder

| ID | Objective | Target | Leading Metric | Lagging Metric |
|---|---|---|---|---|
| OBJ-S1 | Importer single-pane visibility | Order + shipment + SLA + cost + margin in one graph query | Entity bridge completeness | User satisfaction with dashboard |
| OBJ-S2 | Exporter complaint traceability | Complaints traceable to operational bottlenecks | Bridge relationship coverage | Exporter corrective action rate |

---

## 4. Metrics Dashboard

### 4.1 Leading Indicators (Predictive)

| Metric | Source Entity | Formula | Threshold |
|---|---|---|---|
| SLA at-risk rate | OrderMilestone | Count(status=AtRisk) / Count(all active) | < 10% green, 10-20% amber, > 20% red |
| Predictive pattern activation count | lsc:PredictivePattern → OFM trigger | Monthly activations feeding SLABreach predictions | Increasing = healthy |
| Milestone on-track % | OrderMilestone | Count(status=OnTrack or Completed) / Count(all) | > 90% green |
| Landed cost variance from estimate | LandedCost vs PurchaseOrder estimate | Abs(actual - estimate) / estimate | < 5% green, 5-15% amber, > 15% red |

### 4.2 Lagging Indicators (Outcome Confirmation)

| Metric | Source Entity | Formula | Threshold |
|---|---|---|---|
| SLA breach rate | SLABreach | Count(breaches) / Count(SLAs monitored) | < 5% green, 5-10% amber, > 10% red |
| NPS score | CustomerSatisfaction | Standard NPS (Promoters - Detractors) | > 50 green, 20-50 amber, < 20 red |
| Gross margin % | MarginAnalysis | grossMarginPercent per corridor | > 25% green, 15-25% amber, < 15% red |
| Complaint resolution time | ComplaintCase | Mean(resolvedAt - raisedAt) | < 48h green, 48-96h amber, > 96h red |
| Remediation cost per order | RemediationAction | Sum(cost) / Count(orders) | Decreasing = healthy |
| Customer churn rate | CRT CustomerRole + PurchaseOrder | Customers not reordering / Total customers | < 5% green |

### 4.3 Health Thresholds

| Status | Criteria | Action |
|---|---|---|
| **Green** | All leading indicators within threshold, NPS > 50, margin > 25% | Continue monitoring |
| **Amber** | 1-2 leading indicators breached OR NPS 20-50 OR margin 15-25% | Investigate, adjust SLAs, review corridor costs |
| **Red** | 3+ indicators breached OR NPS < 20 OR margin < 15% | Executive escalation, corridor review, remediation sprint |

---

## 5. Cause-Effect Chains

### 5.1 Automated Assessment Flow

```
lsc:PredictivePattern (early warning signal detected)
  → lsc:Hypothesis (test: "Port congestion will delay shipment 3+ days")
      → lsc:Scenario (model: worst-case SLA breach + margin impact + cascade)
          → ofm:SLABreach (predicted breach created, severity assessed)
              → ofm:RemediationAction (pre-emptive: reroute, adjust ETA, notify)
                  → ofm:CustomerNotification (proactive: "Your order may be delayed")
```

**BSC alignment:** OBJ-C3 (proactive notifications), OBJ-P1 (automated detection), OBJ-F3 (prevention reduces remediation cost).

### 5.2 Profit Optimisation Chain

```
ofm:PurchaseOrder (order placed with agreed price)
  → ofm:LandedCost (12 cost categories accumulated during fulfilment)
      → lsc:ImpactAssessment (incident costs roll into landed cost via costImpactedBy)
          → ofm:MarginAnalysis (revenue - total landed cost = gross margin)
              → kpi:Metric (corridor-margin-pct updated on dashboard)
                  → pmf:ProductMarketFit (margin sustainability validates market fit)
```

**BSC alignment:** OBJ-F1 (margin erosion), OBJ-F2 (cost visibility), OBJ-P3 (margin analysis coverage).

### 5.3 Feedback Loop (Complaint to Prevention)

```
ofm:ComplaintCase (customer raises complaint about late delivery)
  → ofm:complaintRootCauseIncident → lsc:Incident (trace to vessel delay)
      → lsc:Incident.occursAtNode → lsc:ChainNode (Port of Felixstowe)
          → lsc:ChainNode.hasBottleneck → lsc:Bottleneck (port congestion)
              → lsc:Bottleneck.mitigates → lsc:RiskMitigation (add contingency buffer)
                  → lsc:RiskMitigation → lsc:PredictivePattern (pattern updated)
                      → [cycle restarts at 5.1 with improved early detection]
```

**BSC alignment:** OBJ-P2 (root cause linkage), OBJ-L1 (pattern updates from complaints), OBJ-S2 (exporter traceability).

---

## 6. Architecture Overview

### 6.1 Entity Model (10 Entities)

```
                    ┌──────────────────────┐
                    │   PurchaseOrder       │ ← Hub entity
                    │   (ofm:hub)           │
                    └──────┬───┬───┬───┬────┘
                           │   │   │   │
          ┌────────────────┘   │   │   └────────────────┐
          ▼                    ▼   ▼                    ▼
  ┌───────────────┐  ┌─────────┐  ┌──────────┐  ┌──────────────┐
  │ OrderMilestone│  │   SLA   │  │LandedCost│  │CustomerNotif.│
  └───────┬───────┘  └────┬────┘  └─────┬────┘  └──────────────┘
          │                │            │
          │           ┌────▼────┐  ┌────▼──────────┐
          │           │SLABreach│  │MarginAnalysis  │
          │           └────┬────┘  └───────────────┘
          │                │
          │    ┌───────────┴───────────┐
          │    ▼                       ▼
          │ ┌──────────────┐  ┌───────────────┐
          │ │RemediationAct│  │ComplaintCase   │
          │ └──────────────┘  └───────┬───────┘
          │                           │
          │                    ┌──────▼──────────┐
          │                    │CustomerSatisfact.│
          │                    └─────────────────┘
```

### 6.2 Cross-Ontology Bridge Map

```
                         VE-Series
            ┌──────┬──────┬──────┬──────┐
            │VP-ONT│PMF   │KPI   │CRT   │RRR-ONT
            │      │      │      │      │
            ▼      ▼      ▼      ▼      ▼
  ┌─────────────────────────────────────────────┐
  │              OFM-ONT (PE-Series)            │
  │  PurchaseOrder ─ SLA ─ Milestone ─ Cost     │
  │  Breach ─ Complaint ─ Satisfaction ─ Margin │
  └─────────────────────┬───────────────────────┘
                        │
            ┌───────────┴───────────┐
            ▼                       ▼
  ┌──────────────────┐   ┌──────────────────┐
  │   LSC-ONT        │   │   Foundation     │
  │   (PE-Series)    │   │   ORG, ORG-CTX   │
  │   Shipment,Node  │   │                  │
  │   Incident,Risk  │   │                  │
  └──────────────────┘   └──────────────────┘
```

---

## 7. Entity Model Detail

### 7.1 PurchaseOrder (Hub Entity)

| Property | Type | Required | Description |
|---|---|---|---|
| orderId | string | Yes | Unique order identifier |
| orderRef | string | Yes | Customer-facing reference number |
| customerName | string | Yes | Customer organisation name |
| customerId | string | Yes | Customer identifier (links to CRT) |
| orderDate | date | Yes | Date order was placed |
| promisedDeliveryDate | date | Yes | Contractual delivery date |
| actualDeliveryDate | date | No | When delivery actually occurred |
| orderValue | number | Yes | Total order value |
| currency | enum | Yes | AUD, GBP, USD, EUR |
| orderStatus | enum | Yes | See enum below |
| fulfilmentChannel | string | No | Direct, Distributor, Agent |
| priority | enum | No | Standard, Expedited, Critical |
| paymentTerms | string | No | Net30, Net60, LC, etc. |
| incoterms | string | No | FOB, CIF, DDP, etc. |

**OrderStatus enum:** Placed, Confirmed, InFulfilment, Shipped, InTransit, Cleared, LastMile, Delivered, PartialDelivery, Disputed, Cancelled, Refunded

### 7.2 ServiceLevelAgreement

| Property | Type | Required | Description |
|---|---|---|---|
| slaId | string | Yes | Unique SLA identifier |
| name | string | Yes | Human-readable SLA name |
| slaType | enum | Yes | DeliveryTime, TemperatureCompliance, OrderAccuracy, ResponseTime, DamageRate, DocumentAccuracy |
| targetValue | number | Yes | Numeric target (e.g. 38 days, 99%) |
| unit | string | Yes | days, percent, hours, celsius |
| measurementMethod | string | Yes | How compliance is measured |
| penaltyClause | string | No | Contractual penalty for breach |
| reviewFrequency | string | No | Monthly, Quarterly, Annual |
| status | enum | Yes | Active, Suspended, Expired |

### 7.3 OrderMilestone

| Property | Type | Required | Description |
|---|---|---|---|
| milestoneId | string | Yes | Unique milestone identifier |
| name | string | Yes | Human-readable milestone name |
| milestoneType | enum | Yes | OrderReceived, OrderConfirmed, PickPack, Dispatched, AtPort, CustomsClearance, InTransit, Arrived, LastMile, Delivered, PODReceived, InvoiceSent, PaymentReceived |
| plannedDate | date | Yes | Expected date |
| actualDate | date | No | When it actually occurred |
| status | enum | Yes | Pending, OnTrack, AtRisk, Breached, Completed, Skipped |
| delayDays | number | No | Variance in days (negative = early) |
| delayReason | string | No | Explanation of variance |

### 7.4 CustomerNotification

| Property | Type | Required | Description |
|---|---|---|---|
| notificationId | string | Yes | Unique notification identifier |
| notificationType | enum | Yes | OrderConfirmation, DispatchNotice, TrackingUpdate, DelayAlert, DeliveryConfirmation, SLABreachNotice, ComplaintAcknowledgement, RemediationOffer, SatisfactionSurvey |
| channel | enum | Yes | Email, SMS, Push, Portal, Phone |
| sentAt | datetime | Yes | When notification was sent |
| deliveredAt | datetime | No | When confirmed delivered |
| readAt | datetime | No | When confirmed read |
| actionRequired | boolean | No | Whether customer action is needed |

### 7.5 LandedCost

| Property | Type | Required | Description |
|---|---|---|---|
| costId | string | Yes | Unique cost line identifier |
| costCategory | enum | Yes | ProductCost, Freight, Insurance, CustomsDuty, ImportTariff, Warehousing, LastMile, Documentation, Inspection, Demurrage, Detention, CurrencyHedge, AgentFees, PortHandling |
| amount | number | Yes | Cost amount |
| currency | enum | Yes | AUD, GBP, USD, EUR |
| isFixed | boolean | Yes | Fixed cost or variable |
| perUnit | string | No | Per container, per kg, per order |
| allocatedToOrder | string | Yes | Order reference this cost is allocated to |
| notes | string | No | Cost justification or variance note |

### 7.6 MarginAnalysis

| Property | Type | Required | Description |
|---|---|---|---|
| analysisId | string | Yes | Unique analysis identifier |
| orderRef | string | Yes | Order being analysed |
| corridorRef | string | No | Supply chain corridor reference |
| totalRevenue | number | Yes | Total invoice value to customer |
| totalLandedCost | number | Yes | Sum of all LandedCost entries |
| grossMargin | number | Yes | totalRevenue - totalLandedCost |
| grossMarginPercent | number | Yes | grossMargin / totalRevenue * 100 |
| targetMarginPercent | number | Yes | Business target margin |
| marginVariance | number | Yes | grossMarginPercent - targetMarginPercent |
| varianceReason | string | No | Explanation if variance > threshold |
| profitabilityStatus | enum | Yes | Profitable, Marginal, BreakEven, LossMaking |

### 7.7 SLABreach

| Property | Type | Required | Description |
|---|---|---|---|
| breachId | string | Yes | Unique breach identifier |
| breachType | enum | Yes | Mirrors SLA slaType enum |
| severity | enum | Yes | Critical, Major, Moderate, Minor |
| detectedAt | datetime | Yes | When breach was detected |
| description | string | Yes | What happened |
| targetValue | number | Yes | SLA target that was breached |
| actualValue | number | Yes | Actual measured value |
| deviationPercent | number | Yes | Deviation from target |
| rootCause | string | No | Brief root cause description |
| financialPenalty | number | No | Contractual penalty amount |
| penaltyCurrency | enum | No | AUD, GBP, USD, EUR |
| customerImpact | string | No | Impact description |
| isNotified | boolean | Yes | Whether customer was notified |
| remediationRequired | boolean | Yes | Whether remediation is needed |

### 7.8 ComplaintCase

| Property | Type | Required | Description |
|---|---|---|---|
| caseId | string | Yes | Unique case identifier |
| caseType | enum | Yes | LateDelivery, DamagedGoods, WrongItem, QualityIssue, DocumentError, Overcharge, ColdChainBreach, MissingItems, CommunicationFailure |
| raisedAt | datetime | Yes | When complaint was raised |
| raisedBy | string | Yes | Customer contact who raised it |
| severity | enum | Yes | Critical, Major, Moderate, Minor |
| status | enum | Yes | Open, Investigating, RemediationInProgress, Resolved, Escalated, Closed, Reopened |
| resolution | string | No | Resolution description |
| satisfactionOutcome | enum | No | Satisfied, PartiallyResolved, Unsatisfied |

### 7.9 CustomerSatisfaction

| Property | Type | Required | Description |
|---|---|---|---|
| satisfactionId | string | Yes | Unique survey identifier |
| surveyType | enum | Yes | PostDelivery, PostComplaint, Periodic, NPS |
| score | number | Yes | Raw score (0-10 for NPS, 1-5 for CSAT) |
| maxScore | number | Yes | Maximum possible score |
| npsCategory | enum | No | Promoter (9-10), Passive (7-8), Detractor (0-6) |
| verbatimFeedback | string | No | Free-text customer comments |
| sentimentScore | number | No | AI-derived sentiment (-1.0 to 1.0) |
| themes | array | No | Extracted themes from feedback |
| surveyDate | date | Yes | When survey was completed |
| responseRate | number | No | Response rate for this survey batch |

### 7.10 RemediationAction

| Property | Type | Required | Description |
|---|---|---|---|
| actionId | string | Yes | Unique action identifier |
| actionType | enum | Yes | CreditNote, Replacement, PartialRefund, FullRefund, PriorityReshipment, SLAWaiver, FutureDiscount, PersonalApology, ProcessChange, SupplierClaim |
| description | string | Yes | What was done |
| cost | number | No | Cost of remediation |
| currency | enum | No | AUD, GBP, USD, EUR |
| approvedBy | string | No | Approver name/role (required if cost > threshold) |
| implementedAt | datetime | No | When action was taken |
| effectiveness | enum | No | Resolved, PartiallyResolved, Ineffective, PendingVerification |
| preventsFutureOccurrence | boolean | No | Whether this is a systemic fix |

---

## 8. Cross-Ontology Bridges

### 8.1 LSC-ONT Bridges (8 Relationships)

| Relationship | Domain | Range | Cardinality | Purpose |
|---|---|---|---|---|
| `ofm:fulfilledByShipment` | PurchaseOrder | lsc:Shipment | 1..* | Link order to physical consignment(s) |
| `ofm:orderUsesChain` | PurchaseOrder | lsc:SupplyChain | 1..1 | Which corridor the order travels |
| `ofm:milestoneAtNode` | OrderMilestone | lsc:ChainNode | 0..1 | Physical location of milestone |
| `ofm:breachTriggeredByIncident` | SLABreach | lsc:Incident | 0..* | LSC incident(s) that caused breach |
| `ofm:breachCausedByBottleneck` | SLABreach | lsc:Bottleneck | 0..* | Systemic bottleneck causing breach |
| `ofm:costImpactedBy` | LandedCost | lsc:ImpactAssessment | 0..* | Disruption costs rolling into landed cost |
| `ofm:complaintRootCauseIncident` | ComplaintCase | lsc:Incident | 0..* | Complaint traces to logistics incident |
| `ofm:remediationInformsMitigation` | RemediationAction | lsc:RiskMitigation | 0..* | Customer remediation drives systemic fix |

### 8.2 VE-Series Bridges (9 Relationships)

| Relationship | Domain | Range | Cardinality | VP-RRR Alignment |
|---|---|---|---|---|
| `ofm:complaintRevealsVPProblem` | ComplaintCase | vp:Problem | 0..* | Problem → Risk |
| `ofm:remediationMapsToVPSolution` | RemediationAction | vp:Solution | 0..* | Solution → Requirement |
| `ofm:satisfactionValidatesBenefit` | CustomerSatisfaction | vp:Benefit | 0..* | Benefit → Result |
| `ofm:marginValidatesPMF` | MarginAnalysis | pmf:ProductMarketFit | 0..1 | Margin sustainability validates PMF |
| `ofm:satisfactionFeedsKPI` | CustomerSatisfaction | kpi:Metric | 0..* | NPS/CSAT feeds KPI dashboard |
| `ofm:breachFeedsKPI` | SLABreach | kpi:Metric | 0..* | Breach rates feed KPI dashboard |
| `ofm:orderPlacedByRole` | PurchaseOrder | crt:CustomerRole | 0..1 | Customer role placing the order |
| `ofm:complaintOwnedByRole` | ComplaintCase | pf:FunctionalRole | 1..1 | Internal role accountable for resolution |
| `ofm:breachAccountableRole` | SLABreach | pf:FunctionalRole | 1..1 | Role accountable for SLA performance |

---

## 9. Business Rules (12 Rules)

### 9.1 Mandatory Rules (Error Severity)

| Rule ID | Name | Condition → Action |
|---|---|---|
| BR-OFM-001 | OrderMustHaveShipment | IF PurchaseOrder.status IN [InFulfilment..Delivered] → MUST have ≥1 fulfilledByShipment |
| BR-OFM-002 | SLABreachDetection | IF OrderMilestone.actualDate > planned AND SLA target exceeded → SLABreach MUST be created |
| BR-OFM-003 | BreachRequiresNotification | IF SLABreach.severity IN [Critical, Major] → CustomerNotification (SLABreachNotice) MUST be sent within 4 hours |
| BR-OFM-004 | ComplaintRequiresOwner | IF ComplaintCase created → complaintOwnedByRole MUST reference valid pf:FunctionalRole |
| BR-OFM-005 | RemediationApproval | IF RemediationAction.cost > currency threshold → approvedBy MUST be populated |
| BR-OFM-006 | MarginCalculation | IF MarginAnalysis exists → grossMargin = totalRevenue - totalLandedCost; grossMarginPercent = grossMargin / totalRevenue * 100 |
| BR-OFM-007 | LandedCostCompleteness | IF PurchaseOrder.status = Delivered → MUST have ≥3 LandedCost entries (ProductCost + Freight + one of Insurance/Customs/Duty) |
| BR-OFM-008 | MilestoneSequenceIntegrity | IF OrderMilestone[n] and [n+1] exist → actualDate[n] ≤ actualDate[n+1] |

### 9.2 Advisory Rules (Warning Severity)

| Rule ID | Name | Condition → Action |
|---|---|---|
| BR-OFM-009 | SatisfactionSurveyTiming | IF PurchaseOrder.status = Delivered → PostDelivery survey SHOULD be created within 7 days |
| BR-OFM-010 | ComplaintRootCauseRequired | IF ComplaintCase.status = Resolved → complaintRootCauseIncident SHOULD have ≥1 reference |
| BR-OFM-011 | VPRRRAlignmentEnforcement | IF ComplaintCase links to vp:Problem → vp:Problem SHOULD also have rrr:Risk alignment |
| BR-OFM-012 | ProactiveNotification | IF PredictivePattern indicates SLA-at-risk AND confidence > 0.7 → DelayAlert SHOULD be sent proactively |

---

## 10. Join Patterns (7 Patterns)

| Pattern ID | Name | Traversal Path | Use Case |
|---|---|---|---|
| JP-OFM-001 | Order-to-Shipment Tracking | PurchaseOrder → fulfilledByShipment → lsc:Shipment → currentlyAt → lsc:ChainNode | Real-time order position |
| JP-OFM-002 | SLA Compliance Dashboard | ServiceLevelAgreement → slaMonitorsMilestone → OrderMilestone; SLABreach → breachTriggeredByIncident → lsc:Incident | SLA health view |
| JP-OFM-003 | Automated Assessment Flow | lsc:PredictivePattern → Hypothesis → Scenario → SLABreach → RemediationAction → CustomerNotification | Predictive-to-notification chain |
| JP-OFM-004 | Profit Optimisation Chain | PurchaseOrder → hasLandedCost → LandedCost → landedCostFeedsMargin → MarginAnalysis → marginValidatesPMF → pmf:ProductMarketFit | Revenue to PMF validation |
| JP-OFM-005 | Feedback Loop | ComplaintCase → complaintRootCauseIncident → lsc:Incident → occursAtNode → lsc:ChainNode → hasBottleneck → lsc:Bottleneck → mitigates → lsc:RiskMitigation | Complaint to systemic fix |
| JP-OFM-006 | Customer Role Experience | crt:CustomerRole ← orderPlacedByRole ← PurchaseOrder → hasSatisfaction → CustomerSatisfaction → satisfactionFeedsKPI → kpi:Metric | Role-specific fulfilment KPIs |
| JP-OFM-007 | VP-RRR Through Fulfilment | ComplaintCase → vp:Problem [→rrr:Risk]; RemediationAction → vp:Solution [→rrr:Requirement]; CustomerSatisfaction → vp:Benefit [→rrr:Result] | VP-RRR alignment via fulfilment data |

---

## 11. EMC-ONT v5.0.0 Changes

### 11.1 New Requirement Category

| Field | Value |
|---|---|
| Code | FULFILMENT |
| Name | Order Fulfilment & Customer Delivery Management |
| Required | OFM-ONT, LSC-ONT, VP-ONT, RRR-ONT, CRT-ONT, KPI-ONT, ORG-ONT, ORG-CONTEXT-ONT |
| Recommended | PMF-ONT, GRC-FW-ONT |
| Optional | EFS-ONT, INDUSTRY-ONT |

### 11.2 New Composition Rule (Priority 8)

**FulfilmentBridge:** When FULFILMENT scope active, load OFM-ONT + LSC-ONT as operational bridge, plus VP-ONT and CRT-ONT for customer-facing view, KPI-ONT for metrics, RRR-ONT for accountability.

### 11.3 New Graph Pattern

**FulfilmentBridge Pattern:** OFM-ONT bridges PE-Series operational logistics (LSC-ONT) to VE-Series customer value engineering (VP, PMF, CRT, KPI) with RRR accountability and closed-loop learning. PurchaseOrder is the hub connecting both views.

### 11.4 New Instance Configuration

| Field | Value |
|---|---|
| Product Code | IMP-MEAT-UK |
| Description | UK meat importer -- fulfilment-focused PFI |
| Context Level | PFI |
| Requirement Scopes | FULFILMENT, COMPLIANCE |
| Required Ontologies | OFM-ONT, LSC-ONT, VP-ONT, RRR-ONT, CRT-ONT, KPI-ONT, ORG-ONT, ORG-CONTEXT-ONT, GRC-FW-ONT |
| Recommended | PMF-ONT, VSOM-ONT |
| Maturity Level | 2 |
| Vertical Market | Food Import / Cold Chain Logistics |

### 11.5 New Business Rule

**FulfilmentBridgeRequired:** When FULFILMENT scope is active, both OFM-ONT and LSC-ONT MUST be present in the composition.

### 11.6 New Relationship

**orchestratesFulfilment:** RequirementCategory → ofm:PurchaseOrder (n:m)

### 11.7 Updated Totals (v4.0.0 → v5.0.0)

| Component | v4.0.0 | v5.0.0 | Delta |
|---|---|---|---|
| RequirementCategories | 8 | 9 | +1 (FULFILMENT) |
| CompositionRules | 7 | 8 | +1 (FulfilmentBridge) |
| GraphPatterns | 5 | 6 | +1 (FulfilmentBridge) |
| BusinessRules | 8 | 9 | +1 (FulfilmentBridgeRequired) |
| InstanceConfigurations | 3 | 4 | +1 (Importer) |
| PE-Series ontologyCount | 10 | 11 | +1 (OFM-ONT) |

---

## 12. Traceability Matrix

| Feature | BSC Objectives Addressed | Strategy |
|---|---|---|
| F41.1 Core Schema | All (foundation for all objectives) | S1, S2, S3 |
| F41.2 LSC Bridges | OBJ-P1, OBJ-P2, OBJ-S1, OBJ-S2 | S1, S3 |
| F41.3 VE Bridges | OBJ-C2, OBJ-F1, OBJ-L1, OBJ-S2 | S2, S3 |
| F41.4 Join Patterns | OBJ-C3, OBJ-P1, OBJ-F2, OBJ-L1 | S1, S2, S3 |
| F41.5 EMC Updates | OBJ-S1 (instance composition) | S1, S2 |
| F41.6 Registry & Validation | Quality gate (all objectives indirectly) | S1 |
| F41.7 Instance Data | OBJ-F2, OBJ-P3, OBJ-C1 (worked examples) | S1, S2, S3 |
| F41.8 Briefing Document | Governance & communication (all) | All |

---

## 13. Feature Candidates

| Feature | Title | Stories | Phase | Complexity |
|---|---|---|---|---|
| F41.1 | OFM-ONT Core Schema (v1.0.0) | 15 | 2 | High -- 10 entities, 15 rels, 12 rules |
| F41.2 | LSC-ONT Bridge Relationships | 9 | 3 | Medium -- 8 cross-refs + import declarations |
| F41.3 | VE-Series Bridge Relationships | 10 | 3 | Medium -- 9 cross-refs + VP-RRR alignment |
| F41.4 | Join Patterns & Automated Assessment Flows | 8 | 4 | Medium -- 7 JPs + registry update |
| F41.5 | EMC-ONT Updates (v5.0.0) | 8 | 5 | Medium -- category, rule, pattern, config |
| F41.6 | Registry, Entry & Validation | 4 | 6 | Low -- registry entry, index, gates |
| F41.7 | Instance Data -- AU-UK Importer Worked Example | 8 | 6 | Medium -- 3 orders, full lifecycle |
| F41.8 | Briefing Document | 8 | 1 | Low -- this document |
| **Total** | | **70** | | |

---

## 14. Dependency Chain (Build Order)

```
Phase 1 ─── F41.8 Briefing Document (this file -- no dependencies)
               │
Phase 2 ─── F41.1 Core Schema (standalone ontology, no cross-refs)
               │
Phase 3 ─── F41.2 LSC Bridges ──┬── (requires F41.1 entities)
             F41.3 VE Bridges ───┘   (F41.2 and F41.3 run in parallel)
               │
Phase 4 ─── F41.4 Join Patterns (requires all bridges from F41.2 + F41.3)
               │
Phase 5 ─── F41.5 EMC Updates (requires stable OFM-ONT from F41.1-F41.4)
               │
Phase 6 ─── F41.6 Registry & Validation ──┬── (requires complete schema)
             F41.7 Instance Data ──────────┘   (run in parallel)
```

**External dependencies (all stable):**
- LSC-ONT v1.1.0 (just released, pinned)
- VP-ONT v1.2.3, RRR-ONT v4.0.0, PMF-ONT v2.0.0, KPI-ONT v1.0.0, CRT-ONT v1.0.0
- EMC-ONT v4.0.0 (base for v5.0.0 update)

---

## 15. Risks & Mitigations

| Risk | Impact | Likelihood | Mitigation |
|---|---|---|---|
| Scope creep into ERP territory (inventory, warehouse management) | Delays, over-engineering | Medium | Strict boundary: OFM models the customer/commercial VIEW, not warehouse operations |
| LSC-ONT changes during OFM development | Bridge breakage | Low | LSC-ONT v1.1.0 just released and stable; pin dependency |
| EMC-ONT priority renumbering cascade | Confusion | Low | Single atomic commit; sequential renumber |
| VP-RRR alignment convention drift | Inconsistency | Medium | Explicit business rule BR-OFM-011 enforces the convention |
| Instance data complexity | Quality issues | Medium | Limit to 3 purchase orders; demonstrate patterns, not exhaustive coverage |

---

## Appendix A: Glossary

| Term | Definition |
|---|---|
| **OFM** | Order Fulfilment Management -- the commercial overlay on logistics operations |
| **LSC** | Logistics & Supply Chain -- the operator's view of physical cargo movement |
| **SLA** | Service Level Agreement -- contractual delivery/quality commitments |
| **Landed Cost** | Total cost of goods delivered to destination (product + freight + duties + handling) |
| **Gross Margin** | Revenue minus total landed cost, expressed as percentage |
| **NPS** | Net Promoter Score -- customer loyalty metric (-100 to +100) |
| **CSAT** | Customer Satisfaction Score -- direct satisfaction measurement |
| **VP-RRR Alignment** | Standing convention: Problem→Risk, Solution→Requirement, Benefit→Result |
| **PMF** | Product-Market Fit -- validation that the market values and will pay for the offering |
| **CRT** | Customer Role Taxonomy -- B2B/B2C/B2B2C customer classification |

## Appendix B: Internal Relationships (15)

| Relationship | Domain | Range | Cardinality | Description |
|---|---|---|---|---|
| `ofm:hasSLA` | PurchaseOrder | ServiceLevelAgreement | 1..* | Order governed by these SLAs |
| `ofm:hasMilestone` | PurchaseOrder | OrderMilestone | 1..* | Order tracking milestones |
| `ofm:hasNotification` | PurchaseOrder | CustomerNotification | 0..* | Notifications for this order |
| `ofm:hasLandedCost` | PurchaseOrder | LandedCost | 0..* | Cost components |
| `ofm:hasMarginAnalysis` | PurchaseOrder | MarginAnalysis | 0..1 | Profitability analysis |
| `ofm:hasBreach` | ServiceLevelAgreement | SLABreach | 0..* | Breach records |
| `ofm:hasComplaint` | PurchaseOrder | ComplaintCase | 0..* | Complaints against order |
| `ofm:hasSatisfaction` | PurchaseOrder | CustomerSatisfaction | 0..* | Satisfaction surveys |
| `ofm:slaMonitorsMilestone` | ServiceLevelAgreement | OrderMilestone | 1..* | SLA monitors milestones |
| `ofm:breachTriggersRemediation` | SLABreach | RemediationAction | 0..* | Breach triggers fix |
| `ofm:complaintLeadsToRemediation` | ComplaintCase | RemediationAction | 0..* | Complaint triggers fix |
| `ofm:remediationNotifiesCustomer` | RemediationAction | CustomerNotification | 0..1 | Remediation communicated |
| `ofm:landedCostFeedsMargin` | LandedCost | MarginAnalysis | 0..1 | Cost feeds margin calc |
| `ofm:complaintAffectsSatisfaction` | ComplaintCase | CustomerSatisfaction | 0..1 | Complaint impacts CSAT |
| `ofm:satisfactionInformsRemediation` | CustomerSatisfaction | RemediationAction | 0..* | Low satisfaction triggers action |
