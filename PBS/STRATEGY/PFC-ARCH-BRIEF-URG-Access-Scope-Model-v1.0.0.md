# PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0

| Field | Value |
| --- | --- |
| **Document ID** | `PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0` |
| **Date** | 2026-03-16 |
| **Status** | **For Decision** |
| **Epic Refs** | Epic 75 [#1135](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1135) |
| **Parent Strategy** | Epic 34 [#518](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518) — Platform Strategy |
| **Related Docs** | PFC-DSY-BRIEF-App-Skeleton-Skill-Chain-Pipeline-Rationale-v1.0.0 (analogous pattern) |
| **Related Epics** | Epic 59 [#840](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/840), Epic 70 [#1019](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1019), Epic 46 [#683](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/683), Epic 47 [#700](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/700), Epic 10A [#127](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/127) |
| **Owners** | Amanda Moore, Oliver Bardenheier |

---

## 1. Purpose

This strategy brief defines the **URG Access Scope Model** — the security and entitlement architecture that governs what any user can see and use across the PFC platform and all PFI instances.

This document covers:

1. The Security Architect Decision Tree — base design authority for all access control rights
2. Mandatory overarching security rules baked into every cascade level
3. The PFI-as-Tenant model — PFI owners as access gatekeepers within PFC-granted scope
4. Multi-PFI user sign-up — commercially separate, session-isolated
5. The cascading entitlement chain — PFC → PFI → Product/Service → Role(RBAC) → User
6. Definitive deny — nothing accessible in a PFI unless explicitly enabled in URG
7. TDD-mandatory role deployment — no production roles without passing security test plans
8. Rehearsal-staged role deployment — dev → test → prod with metrics gates
9. Human AND machine testing for all roles — dual verification mandatory
10. Auto audit controls at every stage
11. Offline/distributed deployment constraints — batch-only access rights CRUD
12. Progression path — evolves with PPM-EFS build

**Decision required:** Approve this model as the foundational access architecture for PFC-PFI URG, gating all subsequent database, security, and deployment work.

---

## 2. Core Principles — Non-Negotiable Security Foundation

### 2.1 Nothing Works Unless URG Says So

The URG (Unified Registry Graph) is the **single gate** for all platform resources. The foundational rule:

> **Nothing can work in a PFI, and nothing is accessible in a PFI, unless the entry in URG has been explicitly enabled for that PFI.**

This is not application-level filtering. This is **definitive RLS** — enforced at the database row level. If a resource is not enabled for a PFI in URG, it does not exist from that PFI's perspective. No query, no API call, no UI path can surface it.

### 2.2 No Access Rights Exist Until Designed

No RBAC role receives any access control right by default. **Zero access is the starting state.** Access rights are not deployed to any role without passing through the Security Architect Decision Tree (§7) — a controlled, HITL-gated, TDD-verified decision-making process.

### 2.3 Mandatory Overarching Security Rules

The following rules are **baked into the platform at every cascade level** — they cannot be overridden, bypassed, or weakened at any child scope:

| Rule ID | Rule | Applies At | Enforcement |
| --- | --- | --- | --- |
| MSR-01 | No resource is accessible unless explicitly enabled in URG | PFC → all | RLS + URG entitlement table |
| MSR-02 | Child scope can never exceed parent scope | All levels | Cascading RLS — each level checks parent |
| MSR-03 | Cross-PFI data is invisible regardless of role | PFI level | `pfi_id` RLS — session-bound, no exceptions |
| MSR-04 | Multi-PFI users see only their current session PFI | User level | Single `pfi_id` claim per session |
| MSR-05 | No production role deployment without TDD pass | All role changes | Deployment pipeline gate (§8) |
| MSR-06 | All scope changes are audit-logged with actor, timestamp, justification | All levels | `scope_audit_log` — append-only, immutable |
| MSR-07 | All roles must pass both human review AND machine testing | All role changes | Dual-verification gate (§8.5) |
| MSR-08 | Denied resources must not reside in distributed DBs | Distributed PFIs | Batch preparation filter + integrity hash |
| MSR-09 | Emergency revocation is immediate; all other changes follow staged deployment | All levels | Emergency flag bypasses staging; post-hoc audit mandatory |
| MSR-10 | Periodic auto-audit detects and flags scope drift | All levels | `pfc-scope-auditor` (SKL-SEC-04) scheduled run |

These rules are the **constitution** of the access model. Every decision tree evaluation, every role deployment, every scope change must comply. They are tested as part of the TDD suite — any rule violation is a test failure that blocks deployment.

---

## 3. PFI-as-Tenant Model

### 3.1 The PFI Owner is the Tenant

Each PFI instance operates as a **tenant**. The PFI owner controls:

- Which URG resources are **enabled** for their instance (within PFC-granted scope)
- Which products/services are **deployed** within their instance
- Which user access rights are granted or restricted for their specific instance
- User onboarding and offboarding within their tenant boundary

**Constraint:** A PFI owner can only restrict access for their own instance. They cannot modify access for any other PFI, nor can they override PFC-level enablement decisions. They cannot grant access to resources that PFC has not enabled for their PFI (MSR-02).

### 3.2 How Access is Initially Granted or Denied

**At the PFC level:**

1. PFC publishes resources to URG (ontologies, skills, docs, processes, agents, capabilities)
2. Every resource starts with **zero entitlements** — disabled for all PFIs (MSR-01)
3. PFC Security Architect evaluates each resource through the Decision Tree (§7) to determine which PFI types should receive it
4. Only after Decision Tree approval AND TDD pass (§8) are entitlements activated
5. Auto-audit logs every entitlement decision (MSR-06)

**At the PFI level:**

1. PFI owner receives the set of resources PFC has enabled for their instance
2. PFI owner can **further restrict** (disable resources PFC has enabled) but **cannot expand** (enable resources PFC has not enabled for them)
3. PFI owner assigns user access rights within their enabled scope — subject to the same Decision Tree and TDD requirements
4. PFI owner defines product/service boundaries within their instance

**The result:** Access is a narrowing funnel. PFC sets the maximum. PFI narrows. Product narrows further. Role narrows further. At no point can a lower level grant more than its parent allows. At every narrowing step, the Security Architect's Decision Tree governs what is allowed.

### 3.3 PFI Entitlement Provisioning

```
PFC Security Architect
  │
  ├── Evaluates resource via Decision Tree (§7)
  │   └── HITL review: base design of access control rights
  │       └── Determines: which PFI types, which products, which roles
  │
  ├── Defines baseline entitlement profile per PFI type
  │   (e.g., "W4M-type PFI gets ontologies X, Y, Z; skills A, B, C")
  │   └── All other resources: DENIED by default (MSR-01)
  │
  ├── Security test plan generated (§8)
  │   └── TDD suite: positive, negative, boundary, cross-PFI tests
  │       └── Must PASS in dev and test before production
  │
  └── PFC Security Admin executes provisioning (post-approval only)
      │
      └── URG entitlement records created/updated
          │   └── Auto-audit: actor, timestamp, justification, test results ref (MSR-06)
          │
          └── PFI Owner receives enabled resource set
              │
              └── PFI Owner configures user access within scope
                  └── Subject to same Decision Tree + TDD for role changes
```

---

## 4. Multi-PFI User Model — Commercially Separate, Session-Isolated

### 4.1 Separate Sign-Up Per PFI

A user may legitimately need access to multiple PFIs (e.g., a consultant working across W4M and BAIV). However:

- **Each PFI sign-up is commercially and contractually separate** — the user must sign up for each PFI independently
- **PFC enables multiple sign-ups against a single email address** — the platform recognises the same person but treats each PFI membership as a distinct entitlement
- **Each sign-up creates a separate PFI-scoped profile** with its own role assignment, product access, and entitlement set

### 4.2 Session Isolation — Cannot See Across PFIs

When a user is logged into PFI-A:

- They see **only** PFI-A resources, products, and scope
- They **cannot see** their own PFI-B scope, even though the same email has a PFI-B membership (MSR-04)
- They **cannot** cross-query, cross-reference, or infer the existence of PFI-B resources
- Switching PFI requires explicit session change (logout/login or PFI selector with full session reset)

**RLS enforcement:** The session carries a single `pfi_id` claim. All RLS policies filter on this claim. There is no mechanism to hold two PFI contexts simultaneously (MSR-03).

### 4.3 Data Model

```
users
  ├── user_id (UUID, globally unique)
  ├── email (can appear in multiple PFI memberships)
  └── auth_provider

pfi_memberships
  ├── membership_id
  ├── user_id (FK → users)
  ├── pfi_id (FK → pfi_instances)
  ├── role_id (FK → roles — RRR-ONT role within this PFI)
  ├── product_scope[] (which products within this PFI)
  ├── status (active | suspended | revoked)
  ├── signed_up_at
  ├── contract_ref (commercial/contractual reference)
  └── role_deployment_ref (FK → role_deployments — which tested/approved role config)

-- One user, many memberships. Each membership = one PFI.
-- Session resolves to exactly ONE membership at a time.
-- role_deployment_ref links to the TDD-verified role configuration.
```

### 4.4 User-to-Role Assignment — Strict Controls

Users are added to roles under strict controls:

1. **Role must exist and be deployed** — the role configuration must have passed through the Decision Tree (§7) and TDD pipeline (§8) to production
2. **PFI Owner initiates** — only the PFI Owner (or delegated PFI Admin) can assign a user to a role within their instance
3. **Role assignment is audit-logged** — actor, user, role, PFI, timestamp, justification (MSR-06)
4. **No role escalation without re-approval** — changing a user from Role A to Role B (higher privilege) requires a new Decision Tree evaluation
5. **Periodic role review** — auto-audit flags stale assignments (users who haven't accessed the system, roles with no active users)

---

## 5. The Access Scope Chain — Cascading Entitlement

### 5.1 Five Levels of Narrowing

```
Level 1: PFC (Platform)
  │  What resources EXIST in the platform
  │  Governed by: PFC Security Architect
  │  Security rules: MSR-01 to MSR-10 baked in — non-negotiable
  │
Level 2: PFI (Instance / Tenant)
  │  What resources are ENABLED for this instance
  │  Governed by: PFC entitlement (Decision Tree approved) + PFI Owner restrictions
  │  Enforced by: URG entitlement records + RLS on pfi_id
  │  Inherits: all PFC security rules (MSR-01 to MSR-10) — cannot weaken
  │
Level 3: Product / Service
  │  What resources are DEPLOYED for this product line
  │  Governed by: PFI Owner product configuration (within PFI scope)
  │  Enforced by: product_scope in pfi_memberships + RLS
  │  Inherits: PFC + PFI security rules — cannot weaken
  │
Level 4: Role (RBAC — from RRR-ONT)
  │  What OPERATIONS the user can perform on visible resources
  │  Governed by: Decision Tree approved role definition
  │  Enforced by: role_id in pfi_memberships + RLS
  │  Inherits: PFC + PFI + Product security rules — cannot weaken
  │  MUST be TDD-verified before production deployment (MSR-05)
  │
Level 5: User (Individual)
     The intersection of all above = what this user can see and do
     Enforced by: session claim carrying pfi_id + membership_id + role_id
     Assignment: strict controls (§4.4) — audit-logged, no escalation without re-approval
```

### 5.2 Mandatory Security Rules at Each Level

Every level inherits and enforces all parent security rules. Additionally:

| Level | Baked-In Rules |
| --- | --- |
| **PFC** | MSR-01 to MSR-10 defined. All resources start at zero entitlement. Decision Tree is the only path to enablement. |
| **PFI** | Cannot enable anything PFC hasn't enabled. PFI owner can restrict but not expand. Cross-PFI isolation absolute (MSR-03). |
| **Product** | Product scope is a subset of PFI scope. No product can access resources outside its PFI's entitlement. |
| **Role** | No role receives any access until designed via Decision Tree, verified by TDD, and deployed through staged pipeline. Role definitions are immutable once deployed — changes require new version + new Decision Tree cycle. |
| **User** | User assignment to role is audit-logged. No privilege escalation without new Decision Tree evaluation. Periodic auto-review of active assignments. |

### 5.3 Definitive Deny Semantics

- **Deny at Level 2 (PFI):** Resource not enabled for PFI → invisible to ALL users in that PFI, regardless of role, product, or admin status within the PFI
- **Deny at Level 3 (Product):** Resource enabled for PFI but not deployed for user's product → invisible to that user, visible to users on other products within the same PFI (if their product includes it)
- **Deny at Level 4 (Role):** Resource visible but user's role doesn't permit the operation → resource visible (can see) but operation blocked (can't use that action)

**Level 2 deny is absolute.** No PFI-level admin, no user role, no product configuration can override a PFI-level deny. This is the B2B commercial boundary.

### 5.4 B2B Client Organisation Isolation

In B2B scenarios where a PFI serves multiple client organisations:

- The PFI owner may further partition resources by client org
- If Client Org X is not entitled to a resource, **no user in Org X sees it** — even if other client orgs in the same PFI can see it
- This is an optional sub-tenant layer within Level 2, governed by the PFI owner
- Enforcement: `client_org_id` column + RLS policy chained after `pfi_id` check

---

## 6. PFC Security Roles — Scope Planning as a Skill

### 6.1 Role Definitions

| Role | Scope | Responsibility |
| --- | --- | --- |
| **PFC Security Architect** | Platform-wide | **Base design authority.** Reviews and specs all access control rights via Decision Tree (§7). Determines what access control rights are available — none deployed to any RBAC role without their Decision Tree evaluation and HITL approval. Defines mandatory security rules (§2.3). Approves all scope changes. Reviews security test plans and metrics. |
| **PFC Security Admin** | Platform-wide (operational) | Executes entitlement provisioning (post-approval only). Manages URG access records. Runs scope audits. Operates batch access rights for distributed deployments. Cannot approve — only executes approved decisions. |
| **PFI Owner** | Single PFI instance | Configures user access within PFC-granted scope. Assigns users to roles (pre-approved roles only). Manages product boundaries. Onboards/offboards users. Subject to audit. |

### 6.2 Scope Minimisation — Security Architect Led

The PFC Security Architect **systematically minimises scopes**. This is not ad hoc — it follows a structured, decision-tree-governed process:

1. **Start at zero** — no resource has any entitlement until the Decision Tree says otherwise
2. **Enumerate** all resources in URG for the target PFI type
3. **Classify** each resource: required / optional / restricted for this PFI type
4. **Evaluate** each resource through the Decision Tree (§7) — HITL at every gate
5. **Design** the role RBAC scope — what operations each role can perform, on which resources
6. **Test** — generate TDD suite, run in dev, run in test, verify metrics (§8)
7. **Deploy** — staged: dev → test → prod, no skipping stages
8. **Audit** — continuous auto-audit detects drift, triggers re-evaluation

### 6.3 Security Scope Planning Skills

Just as the Skill Builder (SKBLD) uses skills to systematically build and register skills, the **Security Scope Planner** uses skills to systematically plan, test, and approve access scopes:

| Skill ID | Name | Purpose |
| --- | --- | --- |
| SKL-SEC-01 | `pfc-scope-enumerator` | Enumerate all URG resources for a PFI type, classify by required/optional/restricted |
| SKL-SEC-02 | `pfc-scope-minimiser` | Apply least-privilege rules, flag over-broad scopes, suggest narrower alternatives |
| SKL-SEC-03 | `pfc-scope-approver` | Human-in-the-loop approval workflow — Security Architect reviews and signs off |
| SKL-SEC-04 | `pfc-scope-auditor` | Periodic auto-audit — compare current entitlements vs justified, flag drift, detect stale assignments |
| SKL-SEC-05 | `pfc-scope-tester` | Generate exhaustive TDD test cases for scope boundaries, run against dev/test/prod |
| SKL-SEC-06 | `pfc-scope-rehearser` | Rehearse role deployment in staging — simulate real user sessions against proposed scope |
| SKL-SEC-07 | `pfc-security-metrics` | Compute and evaluate security test metrics — coverage, pass rate, boundary coverage, regression |

These skills follow the same registry pattern as all PFC skills — registered in `skills-register-index.json`, governed by PE-ONT process templates, gated by human-in-the-loop approval.

---

## 7. Security Architect Decision Tree — Base Design Authority

### 7.1 Concept

The Security Architect Decision Tree is the **single authority** that determines what access control rights are available for any resource in URG. No access rights are deployed to any RBAC role without passing through this tree. The tree operates with **HITL (human-in-the-loop) review at every decision gate** — the Security Architect reviews, approves, modifies, or rejects at each stage.

This is not a runtime evaluation. This is the **design-time governance process** that produces the access control specification. The specification is then implemented as RLS policies, tested via TDD (§8), rehearsed in staging, and only then deployed to production.

Analogous to the **Skill Builder Decision Tree** (SKBLD Dtree) that determines how skills are built and registered, the **Security Architect Decision Tree** (SA-DT) determines what access control rights exist, for which resources, at which scope, for which roles.

### 7.2 Security Architect Decision Tree (SA-DT)

The tree is evaluated **per resource** by the Security Architect. Each gate requires HITL review. The output is an **Access Control Specification** that feeds into the TDD pipeline (§8).

```
SA-DT: Security Architect evaluates resource R for access control design
════════════════════════════════════════════════════════════════════════

SA-DT-01: RESOURCE CLASSIFICATION
  │  Security Architect reviews resource R.
  │  HITL: What type of resource? What sensitivity level?
  │
  ├── PUBLIC (platform documentation, public ontology schemas)
  │   → Candidate for broad PFI enablement
  │
  ├── STANDARD (skills, process templates, standard tooling)
  │   → Candidate for PFI-type-based enablement
  │
  ├── RESTRICTED (financial data, audit logs, security configs)
  │   → Narrow enablement, elevated role requirement
  │
  └── CONFIDENTIAL (PFI-specific IP, client data, credentials)
      → Per-PFI individual evaluation, highest role only
      │
SA-DT-02: PFI TYPE ELIGIBILITY
  │  Security Architect determines which PFI types can receive this resource.
  │  HITL: Does this PFI type's commercial agreement include this capability?
  │        Does this PFI type's vertical require this resource?
  │
  ├── ALL PFI TYPES → enable for all (subject to per-PFI TDD)
  ├── SPECIFIC PFI TYPES → list eligible types, deny remainder
  └── INDIVIDUAL PFIs ONLY → evaluate case by case
      │
SA-DT-03: PRODUCT / SERVICE SCOPING
  │  Security Architect determines whether this resource is PFI-wide
  │  or scoped to specific products/services within the PFI.
  │  HITL: Is this a platform capability or product-specific feature?
  │
  ├── PFI-WIDE → available to all products within the PFI
  └── PRODUCT-SCOPED → list which products, deny remainder
      │
SA-DT-04: ROLE PERMISSION DESIGN
  │  Security Architect designs the RBAC permissions for this resource.
  │  HITL: Which roles need what level of access?
  │  Apply RRR-ONT L0–L4 role taxonomy.
  │
  │  For EACH role defined in the PFI:
  │  ├── NO ACCESS → role cannot see or use this resource
  │  ├── READ-ONLY → role can view but not modify
  │  ├── READ-WRITE → role can view and modify within constraints
  │  └── ADMIN → full access within PFI scope
  │
  │  Security Architect documents:
  │  ├── Justification for each role's access level
  │  ├── Constraints on write/admin operations (e.g., approval required)
  │  └── Expected behaviour at each scope boundary
      │
SA-DT-05: MANDATORY SECURITY RULE COMPLIANCE CHECK
  │  Security Architect verifies the proposed access control spec
  │  against ALL mandatory security rules (MSR-01 to MSR-10, §2.3).
  │  HITL: Does this spec comply with every rule?
  │
  ├── ALL RULES PASS → proceed to SA-DT-06
  └── ANY RULE FAILS → STOP. Redesign. Return to SA-DT-04 or SA-DT-03.
      │  Cannot proceed with a spec that violates mandatory rules.
      │
SA-DT-06: ACCESS CONTROL SPECIFICATION OUTPUT
  │  Security Architect produces the Access Control Specification:
  │
  │  access_control_spec:
  │    resource_id: R
  │    classification: [PUBLIC | STANDARD | RESTRICTED | CONFIDENTIAL]
  │    pfi_eligibility: [all | specific_types | individual]
  │    product_scope: [pfi_wide | product_list]
  │    role_permissions:
  │      - role: X, access: [none | read | read-write | admin], justification: "..."
  │      - role: Y, access: [none | read | read-write | admin], justification: "..."
  │    msr_compliance: [all_pass]
  │    spec_version: v1.0.0
  │    approved_by: Security Architect (name, date)
  │
  │  HITL: Security Architect signs off on the complete spec.
      │
SA-DT-07: HANDOFF TO TDD PIPELINE (§8)
     │
     └── Access Control Specification → feeds into §8 TDD pipeline
         ├── Security test plan generated from spec
         ├── Tests must pass in dev AND test before any production deployment
         ├── Metrics must meet thresholds (§8.4)
         └── Both human AND machine testing required (§8.5)
         │
         NO PRODUCTION DEPLOYMENT WITHOUT SA-DT-07 COMPLETION ⛔
```

### 7.3 Decision Tree Application — Who Evaluates What

| Scope Change | Who Initiates | Who Evaluates (SA-DT) | Who Approves | Who Executes |
| --- | --- | --- | --- | --- |
| New resource added to URG | PFC team | PFC Security Architect | PFC Security Architect | PFC Security Admin |
| Resource enabled for a PFI | PFC Security Admin | PFC Security Architect | PFC Security Architect | PFC Security Admin |
| Resource disabled by PFI Owner | PFI Owner | N/A (restriction within scope) | PFI Owner | PFI Owner |
| Product scope assignment | PFI Owner | PFC Security Architect (if new scope) | PFC Security Architect + PFI Owner | PFC Security Admin |
| Role permission design/change | PFC Security Architect | PFC Security Architect | PFC Security Architect + PFI Owner | PFC Security Admin |
| User assigned to role | PFI Owner | N/A (role already approved) | PFI Owner | PFI Owner |
| Role escalation (user) | PFI Owner | PFC Security Architect | PFC Security Architect + PFI Owner | PFC Security Admin |
| Emergency revocation | PFC Security Admin | Post-hoc review by Security Architect | Immediate (MSR-09) | PFC Security Admin |

### 7.4 Security Decision Tree as Skill Chain

Just as the Skill Builder has a skill chain for building skills, the Security Decision Tree operates as a **skill chain** for scope governance:

```
SKL-SEC-01 (pfc-scope-enumerator)
  → Enumerate all URG resources, classify per SA-DT-01
     │
SKL-SEC-02 (pfc-scope-minimiser)
  → Apply least-privilege via SA-DT-02 to SA-DT-04, flag over-broad scopes
     │
SKL-SEC-03 (pfc-scope-approver)
  → Human-in-the-loop gate — Security Architect reviews spec (SA-DT-06)
     │
SKL-SEC-05 (pfc-scope-tester)
  → Generate TDD test suite from Access Control Specification (SA-DT-07 → §8)
     │
SKL-SEC-06 (pfc-scope-rehearser)
  → Rehearse in staging (§8.3) — simulate real sessions
     │
SKL-SEC-07 (pfc-security-metrics)
  → Evaluate metrics gates (§8.4) — must pass before production
     │
SKL-SEC-04 (pfc-scope-auditor)
  → Post-deployment auto-audit — continuous drift detection
```

---

## 8. TDD-Mandatory Role Deployment — No Production Without Passing Tests

### 8.1 The Rule

> **No production role can be granted, progressed, or modified without passing TDD security test plans that meet required metrics. This is non-negotiable (MSR-05).**

Every Access Control Specification produced by the Security Architect Decision Tree (§7, SA-DT-06) must pass through the following pipeline before any role becomes active in production.

### 8.2 Security Test Plan Generation

The Access Control Specification feeds into `pfc-scope-tester` (SKL-SEC-05), which generates a **Security Test Plan** containing:

| Test Category | What It Verifies | Generated From |
| --- | --- | --- |
| **Positive access** | User with correct PFI + product + role → resource visible, operations permitted | SA-DT-04 role permissions |
| **Negative access** | User without scope → resource invisible, operations denied | SA-DT-04 denied roles |
| **PFI boundary** | Resource enabled for PFI-A, not PFI-B → PFI-B user sees nothing | SA-DT-02 PFI eligibility |
| **Product boundary** | Resource scoped to Product X → Product Y user sees nothing | SA-DT-03 product scoping |
| **Role boundary** | Read-only role → write operation denied | SA-DT-04 role permission levels |
| **Cross-PFI isolation** | Multi-PFI user logged into PFI-A → zero PFI-B visibility | MSR-03, MSR-04 |
| **Cascade integrity** | Disable at PFI level → automatically invisible at product/role/user | MSR-02 cascade rule |
| **MSR compliance** | Each mandatory security rule (MSR-01 to MSR-10) independently verified | §2.3 mandatory rules |
| **Regression** | Previously passing tests still pass after scope change | Prior test suite |
| **Distributed DB exclusion** | Denied resources not present in distributed DB data set | MSR-08 |

### 8.3 Staged Deployment — Rehearse Before Production

Roles are deployed through a strict staged pipeline. No stage can be skipped.

```
Stage 1: DEV — Design & Initial Testing
  │
  ├── Access Control Specification applied to dev Supabase
  ├── RLS policies generated from spec
  ├── TDD security test suite run — ALL tests must pass
  ├── Security Architect reviews test results
  │   └── HITL: Are the tests comprehensive? Any gaps?
  ├── Auto-audit: verify MSR compliance in dev environment
  │
  └── Gate: ALL dev tests pass → proceed to Stage 2
      └── FAIL → fix, re-test, do NOT proceed
          │
Stage 2: TEST — Rehearsal & Exhaustive Testing
  │
  ├── Role configuration deployed to test environment
  ├── REHEARSAL: simulate real user sessions (SKL-SEC-06)
  │   ├── Create test users for each role × each PFI × each product
  │   ├── Execute full user journeys — login, navigate, access resources, attempt denied operations
  │   ├── Verify: correct resources visible, correct operations available, correct denials
  │   └── Record: session logs for human review
  │
  ├── EXHAUSTIVE TEST MATRIX:
  │   For each PFI instance:
  │     For each product/service within that PFI:
  │       For each role defined for that PFI:
  │         For each resource in URG:
  │           Assert: visible/invisible matches Access Control Specification
  │           Assert: permitted operations match role permissions
  │           Assert: cross-PFI isolation holds
  │           Assert: cascade integrity holds
  │           Assert: all MSR rules pass
  │
  ├── Security Architect reviews rehearsal results + test matrix
  │   └── HITL: Are results correct? Any unexpected behaviour?
  │
  ├── METRICS EVALUATION (§8.4)
  │   └── All metrics must meet thresholds → proceed to Stage 3
  │       └── FAIL → investigate, fix, re-run from Stage 2
          │
Stage 3: PROD — Production Deployment (gated)
  │
  ├── REQUIRES:
  │   ├── Stage 1 (dev) — ALL tests pass ✅
  │   ├── Stage 2 (test) — ALL tests pass + rehearsal approved ✅
  │   ├── Metrics thresholds met ✅
  │   ├── Security Architect sign-off ✅ (HITL)
  │   └── PFI Owner acknowledgement (for PFI-scoped changes) ✅
  │
  ├── Role configuration deployed to production
  ├── Auto-audit verifies production state matches approved spec
  ├── Post-deployment smoke tests run automatically
  │
  └── Role is now ACTIVE in production
      └── Auto-audit begins continuous monitoring (SKL-SEC-04)
```

### 8.4 Security Metrics — Required Thresholds

All metrics are computed by `pfc-security-metrics` (SKL-SEC-07). **All thresholds must be met** before production deployment is permitted.

| Metric | Description | Required Threshold |
| --- | --- | --- |
| **Test pass rate** | % of security tests that pass | 100% — zero failures permitted |
| **Scope coverage** | % of resource × role × PFI combinations tested | 100% — no untested combinations |
| **Boundary coverage** | % of scope boundaries (PFI, product, role edges) with explicit boundary tests | 100% |
| **MSR compliance** | % of mandatory security rules with passing verification tests | 100% |
| **Regression rate** | % of previously passing tests that still pass | 100% — zero regressions |
| **Cross-PFI isolation** | % of cross-PFI test pairs that correctly deny | 100% |
| **Rehearsal pass** | Human reviewer confirms rehearsal sessions behaved correctly | Approved by Security Architect |

**Any metric below threshold blocks production deployment.** There are no exceptions and no overrides.

### 8.5 Dual Verification — Human AND Machine Testing

All roles must be verified by **both** human review and machine testing (MSR-07). Neither alone is sufficient.

**Machine testing (automated):**

- TDD security test suite — generated from Access Control Specification
- Exhaustive matrix testing — all PFI × Product × Role × Resource combinations
- MSR compliance verification — each mandatory rule independently tested
- Regression suite — all prior tests re-run
- Cross-PFI isolation tests — verify no data leakage

**Human testing (Security Architect + designated reviewers):**

- Decision Tree review — Security Architect evaluates each gate (SA-DT-01 to SA-DT-06)
- Rehearsal review — human reviewers walk through simulated user sessions in test environment
- Boundary review — human verification of scope edges (what's visible at the boundary between permitted/denied)
- Audit review — Security Architect reviews audit logs for the deployment
- Sign-off — Security Architect formally approves for production

**Both must pass. If machine tests pass but human review identifies concerns → blocked. If human review approves but machine tests fail → blocked.**

---

## 9. Auto Audit Controls

### 9.1 Continuous Audit

Every scope change, role assignment, resource enablement, and user action is **automatically audit-logged** (MSR-06):

```
scope_audit_log (append-only, immutable)
  ├── audit_id (UUID)
  ├── timestamp (UTC)
  ├── actor (user_id or system_id)
  ├── action (enable | disable | assign | revoke | escalate | deploy | test)
  ├── target_type (resource | role | entitlement | membership)
  ├── target_id
  ├── pfi_id (which PFI context)
  ├── previous_state
  ├── new_state
  ├── justification (mandatory — cannot be blank)
  ├── decision_tree_ref (which SA-DT evaluation produced this action)
  ├── test_plan_ref (which TDD test plan verified this action)
  └── test_results_ref (link to test results that passed)
```

### 9.2 Scheduled Auto-Audit (SKL-SEC-04)

`pfc-scope-auditor` runs on a scheduled basis and checks:

| Check | Detects | Action on Detection |
| --- | --- | --- |
| **Scope drift** | Current RLS policies differ from approved Access Control Specification | Flag for Security Architect review |
| **Stale assignments** | Users assigned to roles but inactive for > N days | Flag for PFI Owner review |
| **Orphaned entitlements** | PFI entitlements for resources that no longer exist in URG | Auto-disable + flag |
| **MSR violation** | Any mandatory security rule not enforced in production | CRITICAL alert — immediate Security Architect notification |
| **Untested changes** | Any scope change that bypassed TDD pipeline | CRITICAL alert — Security Architect must investigate |
| **Cross-PFI leakage** | Any query result containing cross-PFI data | CRITICAL alert — emergency revocation (MSR-09) |
| **Distributed DB contamination** | Denied resources present in distributed DB | CRITICAL alert — emergency purge |

### 9.3 Audit Trail for Compliance

The audit system provides a complete, immutable trail from:

- Decision Tree evaluation → Access Control Specification → Test Plan → Test Results → Deployment → Post-deployment monitoring

This chain is traceable end-to-end. For any resource, at any point in time, the system can answer: *Who approved this access? What tests verified it? When was it deployed? Has it drifted?*

---

## 10. Offline / Distributed Deployment Constraints

### 10.1 The Problem

Some PFI deployments (e.g., Azlan, AZZA) operate in **offline or distributed** environments where a central Supabase instance is not continuously reachable. These deployments use a distributed database that must operate independently.

### 10.2 Constraints for Distributed DB

**Rule 1 — No disallowed resources in the distributed DB (MSR-08):**

> If a resource is not entitled for a PFI, it must **not reside** in that PFI's distributed database. It is not sufficient to deny access via RLS — the data itself must not be present. This prevents any bypass through direct database access, backup extraction, or offline tooling.

**Rule 2 — Batch-only access rights CRUD:**

> For distributed/offline deployments, access rights changes (create, update, delete entitlements) are processed as **batch releases only**. There is no real-time entitlement sync. Changes are:
>
> 1. Prepared centrally (PFC Security Admin — from approved Access Control Specification only)
> 2. Packaged as a signed batch release (integrity hash + actor signature)
> 3. Distributed to the offline PFI
> 4. Applied atomically — all or nothing
> 5. Audit-logged with batch reference (MSR-06)

**Rule 3 — Static scope for offline periods:**

> Between batch releases, the distributed PFI operates with a **static entitlement profile**. No scope changes can occur until the next batch is received and applied. This means:
> - Users cannot gain new access during offline periods
> - Revocations take effect only when the batch is applied
> - The PFI owner is informed of the latency between central change and local effect

**Rule 4 — Batch releases must pass TDD:**

> A batch release is itself a deployment. It must pass TDD verification before distribution — the test suite runs against the batch content to verify only entitled resources are included and all denied resources are excluded.

### 10.3 Distributed DB Resource Provisioning

```
Central (PFC)                     Distributed (PFI)
─────────────                     ─────────────────
Access Control Spec
  (SA-DT approved)    ──TDD──►    Test plan passes
                                  in central staging

URG entitlement
  for PFI-X          ──batch──►   Only entitled resources
                                  deployed to local DB
                                  (denied resources NOT present)

Scope change          ──batch──►  Atomic apply:
  (SA-DT approved,                + add new entitled resources
   TDD verified)                  - remove revoked resources
                                  Audit-logged locally

Audit log             ◄──batch──  Local audit events
                                  synced back to central
```

---

## 11. Progression — Evolves with PPM-EFS Build

The access scope model is the **permanent architecture** — it does not change as the platform grows. What changes is the **set of resources governed by it**.

| Phase | Trigger | What Enters the Scope Chain |
| --- | --- | --- |
| **Phase 1 — PoC** | Epic 75 | Ontologies, skills, docs, processes — prove the cascade model, Decision Tree, and TDD pipeline end-to-end |
| **Phase 2 — PPM** | VE-PPM build | Portfolios, programmes, projects, milestones — portfolio views scoped by cascade. New resource types → new Decision Tree evaluations → new TDD suites |
| **Phase 3 — EFS** | EFS build | Cost models, benefit realisation, ROI, financial reporting — RESTRICTED/CONFIDENTIAL classification. Elevated TDD requirements for financial data |
| **Phase 4 — Full production** | Ongoing | Every new PFC capability automatically inherits the scope chain — URG is the single gate. Decision Tree is the single design authority. TDD is the single deployment gate. |

**Key point:** The Security Architect Decision Tree (§7) and TDD pipeline (§8) ensure that as new resource types enter the platform, they are systematically designed, scoped, tested, rehearsed, and approved before deployment. The model scales without architectural change.

---

## 12. Dependencies & Prerequisites

| Dependency | Status | Notes |
| --- | --- | --- |
| RRR-ONT v4.0.0 | ✅ Available | Role taxonomy for RBAC (L0–L4) |
| EMC-ONT v5.1.0 | ✅ Available | InstanceConfiguration for PFI entitlement mapping |
| PE-ONT v4.0.0 | ✅ Available | Process templates for scope approval workflows |
| URG-ONT v1.0.0 | 🔲 To build | Defined by this brief — entities: Resource, Scope, Entitlement, AccessPolicy, DenyRule |
| ORG-ONT | 🔲 Planned | Client org structure for B2B sub-tenant model |
| Supabase project | 🔲 To provision | Single free-tier project for PoC |
| SKBLD pattern | ✅ Available | Skill Builder decision tree pattern — reuse for Security Architect Decision Tree |

---

## 13. Risks & Mitigations

| ID | Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- | --- |
| R-75-01 | Scope model too complex for PoC | Medium | High | Phase 1 proves 3 levels (PFC→PFI→Role) + Decision Tree + TDD pipeline; Product and Client Org layers added in Phase 2 |
| R-75-02 | Multi-PFI session isolation bypass | Low | Critical | RLS enforced at DB layer; session carries single `pfi_id`; no multi-context sessions (MSR-03, MSR-04) |
| R-75-03 | Distributed batch sync latency causes stale entitlements | Medium | Medium | Document acceptable latency; PFI owner informed; emergency batch process (MSR-09) |
| R-75-04 | Disallowed resources leak into distributed DB | Low | Critical | Batch preparation filters at source; integrity hash; TDD on batch content; auto-audit (MSR-08) |
| R-75-05 | TDD test matrix scope explosion (too many permutations) | Medium | Medium | Hierarchical test generation — PFI level first, drill into product/role only where PFI tests pass |
| R-75-06 | PFI owner over-restricts, breaking required capabilities | Medium | Low | PFC defines "required" resources that PFI owners cannot disable |
| R-75-07 | Security Architect becomes bottleneck for all scope decisions | Medium | High | Decision Tree skill chain (SKL-SEC-01 to 07) automates enumeration, classification, test generation; HITL only at approval gates |
| R-75-08 | Tests pass but real-world behaviour differs | Low | High | Dual verification (§8.5) — human rehearsal in test environment catches what machine tests miss |

---

## 14. Decision Criteria

### Proceed If

- [ ] The 5-level cascade model is accepted as the foundational access architecture
- [ ] PFI-as-Tenant with commercially separate sign-up is confirmed as the user model
- [ ] Definitive deny (nothing in PFI unless URG-enabled) is accepted as the security posture
- [ ] Security Architect Decision Tree (SA-DT) is accepted as the base design authority
- [ ] TDD-mandatory role deployment is accepted — no production roles without passing security test plans
- [ ] 100% test pass rate as production gate is accepted — zero failures permitted
- [ ] Dual verification (human + machine) for all roles is accepted
- [ ] Mandatory security rules (MSR-01 to MSR-10) are accepted as non-negotiable
- [ ] Batch-only access rights for distributed/offline deployments is accepted
- [ ] Resource exclusion from distributed DB (not just RLS deny) is accepted

### Defer If

- [ ] Multi-PFI user model requires legal review before implementation
- [ ] Distributed deployment constraints need further analysis with Azlan/AZZA teams
- [ ] ORG-ONT design needs to be completed before B2B sub-tenant layer can be specified
- [ ] Security metrics thresholds need calibration based on PoC findings

### Reject If

- [ ] The cascading model is deemed too restrictive for current PFI requirements
- [ ] TDD-mandatory deployment is deemed too heavy for PoC phase
- [ ] A simpler access model (e.g., flat RBAC without cascade) is preferred

---

## 15. Epic 75 — Proposed Scope (For Review)

Epic: [#1135](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1135) — PFC-ARCH-URG-POC
Owners: Amanda Moore, Oliver Bardenheier
Priority: P0 — Critical Path

### 15.1 Features

#### F75.1: Security Architect Decision Tree & URG-ONT

| Deliverable | Detail |
| --- | --- |
| URG-ONT v1.0.0 | OAA v7 ontology — entities: Resource, Scope, Entitlement, AccessPolicy, DenyRule |
| SA-DT implementation | Gates SA-DT-01 to SA-DT-07 with HITL at each (§7.2) |
| Mandatory Security Rules | MSR-01 to MSR-10 codified and tested (§2.3) |
| EMC mapping | InstanceConfiguration → PFI entitlement derivation |
| First Access Control Spec | Produced via SA-DT for PoC resource set |

#### F75.2: Multi-PFI User Model & Session Isolation

| Deliverable | Detail |
| --- | --- |
| Data model | `users` + `pfi_memberships` with `role_deployment_ref` (§4.3) |
| Session isolation | Single `pfi_id` per session — no multi-PFI context (MSR-03, MSR-04) |
| Strict role assignment | Audit-logged, no escalation without SA-DT re-evaluation (§4.4) |
| Multi-PFI proof | Same email, two PFIs → login to PFI-A shows zero PFI-B resources |

#### F75.3: Minimal Supabase Schema with Cascading RLS

| Deliverable | Detail |
| --- | --- |
| Core tables | `pfc_resources`, `pfi_entitlements`, `product_entitlements`, `pfi_memberships`, `user_roles`, `scope_audit_log` |
| Cascading RLS | Each level's RLS checks parent scope — child can never exceed parent (MSR-02) |
| PFI deny enforcement | PFI-level deny = row invisible at all child levels, no exceptions |
| Environment | Single Supabase project (free tier) |

#### F75.4: TDD Security Test Pipeline

| Deliverable | Detail |
| --- | --- |
| Test plan generation | From Access Control Specification via SKL-SEC-05 (§8.2) |
| Staged pipeline | Dev → Test (with rehearsal) → Prod — no stage skipping (§8.3) |
| Metrics computation | SKL-SEC-07 evaluates all thresholds (§8.4) |
| Production gate | 100% pass rate mandatory — zero failures permitted (MSR-05) |
| Pipeline proof | Scope change → generates tests → tests pass → metrics met → deploy |

#### F75.5: Dual Verification — Human + Machine Testing

| Deliverable | Detail |
| --- | --- |
| Machine testing | TDD suite, exhaustive matrix, MSR compliance, regression, cross-PFI isolation |
| Human testing | SA-DT gate review, rehearsal walkthrough (SKL-SEC-06), boundary review, sign-off |
| Blocking proof | Human concern blocks even if machine passes; machine failure blocks even if human approves (MSR-07) |

#### F75.6: Auto Audit & Continuous Monitoring

| Deliverable | Detail |
| --- | --- |
| Audit log | `scope_audit_log` — append-only, immutable, with `decision_tree_ref` + `test_results_ref` (§9.1) |
| Scheduled audit | SKL-SEC-04: scope drift, stale assignments, MSR violations, untested changes, cross-PFI leakage (§9.2) |
| Traceability | End-to-end: SA-DT → spec → test plan → results → deployment → monitoring (§9.3) |

#### F75.7: Definitive Deny & Isolation Test Cases

| Test | Scenario | Proves |
| --- | --- | --- |
| **T-75-01** | Resource in PFC, disabled for PFI-A → invisible to all PFI-A users | PFI deny (MSR-01) |
| **T-75-02** | Enabled by PFC, disabled by PFI Owner → invisible within that PFI | PFI Owner restriction within scope (§3.2) |
| **T-75-03** | Resource in PFI, not in user's product scope → invisible | Product narrowing (§5.1 Level 3) |
| **T-75-04** | PFI-A resource → invisible to PFI-B user regardless of role | Cross-PFI isolation (MSR-03) |
| **T-75-05** | Same email in PFI-A and PFI-B → logged into PFI-A sees zero PFI-B | Multi-PFI session isolation (MSR-04) |
| **T-75-06** | Disable at PFI level → invisible at all child levels, no additional action | Cascade integrity (MSR-02) |
| **T-75-07** | Denied resource does not exist in distributed DB data set | Distributed DB exclusion (MSR-08) |
| **T-75-08** | Role that hasn't passed TDD pipeline → cannot be assigned to any user | TDD-mandatory deployment (MSR-05) |
| **T-75-09** | Attempt to bypass any MSR → blocked and audit-logged | MSR enforcement + auto-audit (MSR-06) |

#### F75.8: Documentation & Progression Path

| Deliverable | Detail |
| --- | --- |
| Access Scope Chain spec | Full cascade model documentation |
| SA-DT reference | Evaluation guide + Access Control Spec template |
| Multi-PFI user model spec | Sign-up, session, isolation patterns |
| TDD pipeline reference | Test categories, staged deployment, metrics |
| Distributed deployment | Batch process documentation |
| Progression map | How PPM-EFS features inherit cascade + SA-DT + TDD automatically |

---

### 15.2 Consolidates & Sequences

This epic provides the **focused PoC entry point** for the broader database and security strategy. It consolidates and prioritises:

| Epic | Ref | Relationship | Sequencing |
| --- | --- | --- | --- |
| Epic 59: PFC-ARCH-DBA | [#840](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/840) | **Scales the PoC** — Epic 75 proves the model | Epic 75 → Epic 59 |
| Epic 70: PFC-GRC-DBA | [#1019](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1019) | **GRC layer** — adds governance lifecycle over proven cascade | Epic 59 → Epic 70 |
| Epic 61: PFC-ARCH-CICD | [#947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947) | **CI/CD** — TDD pipeline feeds into DevSecOps | Epic 70 → Epic 61 |
| Epic 10A: PFC-ARCH-SEC | [#127](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/127) | **Security foundation** — MSR + definitive deny + TDD proven here | Epic 75 proves |
| Epic 46: PFC-ARCH-REG — URG | [#683](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/683) | **URG consumer** — renders what Epic 75 scope chain returns | Epic 75 → Epic 46 |
| Epic 47: URG RBAC Context Assistant | [#700](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/700) | **Context engine** — traverses graph within Epic 75 scope chain | Epic 75 → Epic 47 |

**Critical path:** Epic 75 (PoC) → Epic 59 (DTP scale) → Epic 70 (GRC + lifecycle) → Epic 61 (CI/CD integration)

---

### 15.3 Acceptance Criteria

| # | Criterion | Feature |
| --- | --- | --- |
| AC-01 | SA-DT gates (SA-DT-01 to SA-DT-07) implemented with HITL at every gate | F75.1 |
| AC-02 | Access Control Specification produced for PoC resource set via SA-DT | F75.1 |
| AC-03 | MSR-01 to MSR-10 enforced at every cascade level | F75.1 |
| AC-04 | URG-ONT v1.0.0 registered in ontology library | F75.1 |
| AC-05 | Multi-PFI user model with session isolation proven (MSR-03, MSR-04) | F75.2 |
| AC-06 | Cascading RLS deployed — PFI deny = invisible to all below | F75.3 |
| AC-07 | TDD security test pipeline operational — dev → test → prod staged | F75.4 |
| AC-08 | 100% pass rate, scope coverage, boundary coverage, MSR compliance for production gate | F75.4 |
| AC-09 | Dual verification proven — both human review and machine testing required | F75.5 |
| AC-10 | Auto audit operational — scope_audit_log, scheduled drift detection, MSR violation alerts | F75.6 |
| AC-11 | All 9 test cases pass (T-75-01 to T-75-09) | F75.7 |
| AC-12 | Distributed DB exclusion proven (denied resources not present in data set) | F75.7 |
| AC-13 | Documentation sufficient for PPM-EFS evolution and Epic 59 handover | F75.8 |

---

### 15.4 Open Decisions

| ID | Decision | Status | Impact |
| --- | --- | --- | --- |
| OD-75-01 | Scope storage: JWT claims vs session table vs both | TBD | F75.2, F75.3 |
| OD-75-02 | URG-ONT series placement: Orchestration vs Foundation | TBD | F75.1 |
| OD-75-03 | PoC instance data: W4M-WWG vs BAIV vs synthetic | TBD | F75.7 |
| OD-75-04 | SA-DT approval workflow: GitHub Issues vs custom UI | TBD | F75.1 |
| OD-75-05 | Product/Service entitlement granularity: per-resource vs per-category | TBD | F75.3 |
| OD-75-06 | Distributed batch format: signed JSON vs encrypted package | TBD | §10 |
| OD-75-07 | Security metrics thresholds: calibrate from PoC or set upfront | TBD | F75.4 |

---

### 15.5 Phased Delivery

| Phase | Trigger | Scope |
| --- | --- | --- |
| **Phase 1 — PoC (Epic 75)** | Now | PFC→PFI→Role cascade with RLS. Multi-PFI session isolation. SA-DT with full SA-DT-01–07. TDD pipeline (dev→test→prod). Auto-audit. Dual verification. 9 test cases. |
| **Phase 2 — PPM build** | VE-PPM delivery | Add Product/Service layer + B2B Client Org sub-tenant. Full SKL-SEC-01–07 skill chain. Exhaustive test matrix. Security metrics dashboard. |
| **Phase 3 — EFS + Distributed** | EFS delivery | EFS resource scoping (RESTRICTED/CONFIDENTIAL). Batch access rights CRUD for offline (Azlan, AZZA). Distributed DB resource filtering + TDD on batch content. |
| **Phase 4 — Full production** | Ongoing | All resource types governed by URG cascade. SA-DT fully operational. TDD across all PFI instances. Auto-audit continuous. Every new capability inherits automatically. |

---

## 16. Recommendation

**Approve** this brief as the foundational access architecture for PFC-PFI URG, gating all subsequent database, security, and deployment work.

**Immediate actions on approval:**

1. Amanda builds URG-ONT v1.0.0 and graph (w/c 17 Mar 2026)
2. Oliver and Amanda refine SA-DT gates and produce first Access Control Specification
3. Provision single Supabase project (free tier) for PoC
4. Implement F75.1–F75.3 (URG-ONT, user model, schema) as foundation
5. Build F75.4–F75.5 (TDD pipeline, dual verification) — no production roles until this is operational
6. Execute F75.7 test cases (T-75-01 to T-75-09) — all must pass
7. Deliver F75.8 documentation — handover to Epic 59

---

`PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0` · **For Decision** · 2026-03-16
