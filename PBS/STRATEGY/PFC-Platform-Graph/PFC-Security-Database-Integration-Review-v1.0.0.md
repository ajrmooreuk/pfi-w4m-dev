# Security & Database Integration — AIRL Team Review Pack

**Date:** 2026-03-17
**Status:** For Review
**Audience:** AIRL team — database integration discussion
**Governing Brief:** [PFC-STRAT-BRIEF-Platform-Graph-Architecture-v1.0.0.md](https://github.com/ajrmooreuk/pfi-airl-caf-aza-dev/blob/main/PBS/STRATEGY/PFC-Platform-Graph/PFC-STRAT-BRIEF-Platform-Graph-Architecture-v1.0.0.md)

> **AIRL Working Copy** — compiled from hub strategy docs and epics for team review.
> Source: [`ajrmooreuk/Azlan-EA-AAA — PBS/STRATEGY/`](https://github.com/ajrmooreuk/Azlan-EA-AAA/tree/main/PBS/STRATEGY)
> Hub epic links are read-only references.
>
> ---

## Contents

1. [Epic Reference Map](#epic-reference-map)
2. [Epic 59 — Platform Database Architecture](#epic-59--platform-database-architecture)
3. [Epic 61 — DevSecOps CI/CD Security & DB](#epic-61--devsecops-cicd-security--db)
4. [Epic 70 — Database Distribution GRC Layer](#epic-70--database-distribution-grc-layer)
5. [Epic 76 — TDD-Driven Security Strategy](#epic-76--tdd-driven-security-strategy)
6. [Security-First Platform Implementation Plan](#security-first-platform-implementation-plan)
7. [Platform Database Architecture — MS9 Report](#platform-database-architecture--ms9-report)
8. [Unified Registry Database Brief](#unified-registry-database-brief)
9. [DTP Database Sync / Micro-SaaS Brief](#dtp-database-sync--micro-saas-brief)
10. [Supabase Secure Connections Proposal](#supabase-secure-connections-proposal)

---

## Epic Reference Map

| Epic | Title | Status | Hub Link |
|---|---|---|---|
| E59 | PFC-ARCH-DBA — Platform Database Architecture | OPEN | [https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/840](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/840) |
| E61 | PFC-ARCH-CICD — DevSecOps, CI/CD, DB, Security | OPEN | [https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947) |
| E70 | PFC-GRC-DBA — Database Distribution, GRC & Lifecycle | OPEN | [https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1019](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1019) |
| E75 | PFC-ARCH-URG-POC — Cascading RLS/RBAC Prototype | CLOSED | [https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1135](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1135) |
| E76 | PFC-ARCH-TDD — TDD-Driven Security & CI/CD Gate Governance | OPEN | [https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1196](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1196) |
| E77 Ph1 | URG Access Foundation — 5-level cascade, RLS, RBAC | OPEN | [https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1204](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1204) |

**Sequencing (security + DB):**
```
E77 Phase 1 (cascade model proven)
  → E59 (deploys cascade across DTP triads at scale)
  → E70 (adds GRC lifecycle layer on top of DB)
  → E61 (CI/CD gates enforce security for all URG changes)
  → E76 (TDD gates: SecArch-DT + TDD-DT convergence)
```

---

## Epic 59 — Platform Database Architecture

=== #840 [OPEN] Epic 59: PFC-ARCH-DBA — Platform Database Architecture, PFC-PFI Cascade, Per-Stage Isolation & Micro-SaaS Foundation ===
## Epic 59: Platform Database Architecture — PFC-PFI Cascade, Per-Stage Isolation & Micro-SaaS Foundation

| Field | Value |
| --- | --- |
| **Date** | 2026-03-05 |
| **Priority** | P0 Critical |
| **Status** | STRATEGY BRIEF — For Review & Approval |
| **Parent** | Epic 34: PF-Core Graph-Based Agentic Platform Strategy (#518) |
| **Dependencies** | Epic 31 (#394), Epic 58 (#837), Epic 10A (#127), Epic 53 (#775) |
| **Cross-Refs** | Epic 56 (#834), Epic 39 (#562/#569, closed) |
| **Briefing** | `PBS/STRATEGY/BRIEFING-Epic59-DTP-Database-Sync-Micro-SaaS-Strategy.md` |

---

## Why This Is Its Own Epic

Epic 58 gives PFC its own git triad. Epic 31 defines hub-and-spoke CI/CD. Epic 10A writes the schema. But **none of them implement the database layer as a live, per-stage, cascade-aware platform.**

The existing architecture docs define:
- **ARCH-PFC-PFI-Product-Client-Graph-Cascade** — 4-tier cascade model (Core→Instance→Product→Client)
- **BRIEFING-Unified-Registry-Database-Strategy** — `pfc_registry` + `resolve_artifact_config()` + 10 artifact domains
- **BRIEFING-GraphQL-Supabase-JSONB-MVP** — `resolve_composed_graph()`, `search_entities()`, auto-GraphQL
- **BRIEFING-PFC-EA-Arch-DB-Migrations-Azure-Supabase** — Platform choice per PFI (Supabase/Azure/sovereign)
- **CONVERGENCE-FairSlice-JSONB-Graph-Patterns** — Licensing, royalty, and smart-contract graph patterns

**All assume one Supabase instance.** None address:
1. How each DTP stage gets its own database
2. How schema promotes dev→test→prod
3. How PFC-owned data distributes to PFI databases
4. How PFI instance data promotes independently
5. How customised instances (BAIV, AIRL, W4M, VHF) each run isolated prod databases

This is the **core solution architecture** for the platform — not a CI/CD feature. It operationalises the cascade into real, isolated, promotable databases.

---

## Vision

> Implement the PFC→PFI→Product→Client cascade architecture as a live, per-stage database platform where each DTP triad runs isolated Supabase projects, schema and core data flow one-directionally from PFC to PFI, instance customisation is self-service, and the architecture enables micro-SaaS monetisation at the PFI boundary.

```
PFC-Core (pfc-dev → pfc-test → pfc-prod)
          Schema + Core Ontologies + Registry + Cascade Functions
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
         PFI-BAIV        PFI-AIRL        PFI-W4M-WWG    (+ EOMS, VHF)
         dev→test→prod   dev→test→prod   dev→test→prod
         ┌─────────┐     ┌─────────┐     ┌─────────┐
         │ Tier 0: │     │ Tier 0: │     │ Tier 0: │   ← PFC-owned (read-only)
         │ Core    │     │ Core    │     │ Core    │
         ├─────────┤     ├─────────┤     ├─────────┤
         │ Tier 1: │     │ Tier 1: │     │ Tier 1: │   ← PFI-owned (instance)
         │ Instance│     │ Instance│     │ Instance│
         ├─────────┤     ├─────────┤     ├─────────┤
         │ Tier 2: │     │ Tier 2: │     │ Tier 2: │   ← Product-scoped
         │ Product │     │ Product │     │ Product │
         ├─────────┤     ├─────────┤     ├─────────┤
         │ Tier 3: │     │ Tier 3: │     │ Tier 3: │   ← Client/customer
         │ Client  │     │ Client  │     │ Client  │
         └─────────┘     └─────────┘     └─────────┘
```

---

## Strategy Pillars

### S59.1: Database-Per-Stage Architecture
18 Supabase projects (3 PFC + 15 PFI). Each is a full isolated instance (Postgres + Auth + RLS + Edge Functions). Schema identical at same version; data differs by cascade tier ownership.

### S59.2: Schema & Function Promotion Pipeline
Extend `promote.yml` with `promote-db.yml` — migrations flow dev→test→prod. `resolve_cascaded_config()`, `resolve_composed_graph()`, `resolve_artifact_config()` all promote as schema.

### S59.3: PFC→PFI Core Data Distribution
On PFC-prod release: schema + core ontologies + registry seed → each subscribed PFI-dev, filtered by `ontologySeries`. PFC-owned rows protected by `source = 'pfc-core'` + RLS.

### S59.4: PFI Instance Customisation & Promotion
Each PFI independently promotes its own data (VP instances, RRR roles, graph scope rules, brand config, product configs, client configs) through its triad. The 4-tier cascade resolves per-environment.

### S59.5: Micro-SaaS & PFI→PFI Licensing (Horizon 2)
PFI-to-PFI artifact transfer via PFC mediation. FairSlice royalty patterns. `licensed-pfi-contribution` artifact type in `pfc_registry`.

---

## Features — Phase 1: Foundation (P0 — Weeks 1-2)

### F59.1: Supabase Project Provisioning
Provision all 18 Supabase projects, configure secrets.
- [ ] S59.1.1: Provision 3 PFC Supabase projects (pfc-core-dev, test, prod)
- [ ] S59.1.2: Provision 15 PFI Supabase projects (5 instances × 3 stages)
- [ ] S59.1.3: Document project naming, billing, region strategy
- [ ] S59.1.4: Configure SUPABASE_URL + SUPABASE_ANON_KEY + SUPABASE_SERVICE_KEY on all 18 repos

### F59.2: Missing Migrations & Full Schema Deployment
Write missing migrations, deploy complete schema to all projects.
- [ ] S59.2.1: Write migration 003 (pfc_registry table + resolve_artifact_config function)
- [ ] S59.2.2: Write migration 004 (resolve_cascaded_config — 4-tier Core→Instance→Product→Client)
- [ ] S59.2.3: Deploy migrations 001-005 to all 18 Supabase projects
- [ ] S59.2.4: Validate RLS policies on each project with test users per cascade tier

### F59.3: Secrets & Access Resolution
PROMOTION_PAT and scoped access for all triads.
- [ ] S59.3.1: Create scoped PATs per triad (PFC triad + per-PFI triad)
- [ ] S59.3.2: Set PROMOTION_PAT on remaining 3 PFI repos (W4M-WWG, W4M-EOMS, VHF)
- [ ] S59.3.3: Verify guard-core.yml blocks human PRs on all PFI repos

## Features — Phase 2: PFC Core Pipeline (P0 — Weeks 3-4)

### F59.4: PFC DB Promotion Workflow ✏️
Schema + core data promotion through PFC triad.
- [ ] S59.4.1: Create `promote-db.yml` for PFC triad (dev→test→prod)
- [ ] S59.4.2: Schema dry-run validation (`supabase db push --dry-run`) on test
- [ ] S59.4.3: SME approval gate for test→prod with `needs-sme-approval` label
- [ ] S59.4.4: Cascade resolution integration test suite (all 4 tiers)

### F59.5: PFC→PFI Database Distribution ✏️
Core data flows from pfc-prod to all subscribed PFI dev repos.
- [ ] S59.5.1: Create `pfc-db-release.yml` workflow
- [ ] S59.5.2: Series-subscription filter using `hubSpokeConfig.ontologySeries` from registry
- [ ] S59.5.3: Seed core ontologies from `ont-registry-index.json` → JSONB rows with `source = 'pfc-core'`
- [ ] S59.5.4: Seed `pfc_registry` core rows (10 artifact domains)
- [ ] S59.5.5: Validate full cycle: pfc-dev → pfc-test → pfc-prod → BAIV-dev cascade resolution

## Features — Phase 3: PFI Instance Pipeline (P1 — Weeks 5-6)

### F59.6: PFI DB Promotion Template ✏️
Instance-owned data promotion through each PFI triad.
- [ ] S59.6.1: Create `promote-db.yml` template for PFI triads
- [ ] S59.6.2: PFI-owned data export (`source != 'pfc-core'` filter)
- [ ] S59.6.3: FK integrity + cascade resolution validation post-promotion
- [ ] S59.6.4: Seed BAIV-dev with VP + RRR + graph scope + brand_config instance data

### F59.7: PFI Triad Activation ✏️
Each PFI completes full promotion cycle.
- [ ] S59.7.1: BAIV full cycle: dev → test → prod (PoC — lead instance)
- [ ] S59.7.2: AIRL full cycle
- [ ] S59.7.3: W4M-WWG full cycle
- [ ] S59.7.4: W4M-EOMS full cycle
- [ ] S59.7.5: VHF full cycle

## Features — Phase 4: Governance & Drift (P1 — Weeks 7-8)

### F59.8: DB Drift Detection & Governance ✏️
Schema and data consistency monitoring.
- [ ] S59.8.1: Create `db-drift-detect.yml` cron workflow
- [ ] S59.8.2: Schema diff reporting between stages (per PFC + per PFI)
- [ ] S59.8.3: Auto-create issues on drift detection
- [ ] S59.8.4: DB-inclusive operating guide + runbooks (extends ARCH-CICD-004)
- [ ] S59.8.5: Sovereignty adaptation docs (Azure/self-hosted per Epic 53 phase plan)

## Features — Phase 5: PFI→PFI Licensing (P2 — Horizon 2)

### F59.9: Cross-Instance Licensing Framework ✏️
- [ ] S59.9.1: Extend `pfc_registry` for `licensed-pfi-contribution` artifact type
- [ ] S59.9.2: PFI export workflow (source PFI-prod → PFC-prod review → target PFI-dev)
- [ ] S59.9.3: FairSlice royalty integration for cross-PFI artefacts (CONVERGENCE patterns)
- [ ] S59.9.4: First cross-PFI transfer PoC (e.g., BAIV VP template → AIRL)
- [ ] S59.9.5: Sovereignty tier licensing rules (Epic 53 alignment)

---

## Downstream Consumers

### F8.2: Design Token Storage & Resolution (Epic 8, #80)
- **Stories**: S8.2.1 (#110), S8.2.2 (#111), S8.2.3 (#112), S8.2.4 (#113)
- **Impact**: F8.2 creates DS token tables (`design_system`, `brand_variants`, `token_versions`) that must deploy through F59.2's migration framework
- **Migration slots**: Token schema DDL targets migration 003 or 004 (currently unallocated)
- **Cascade alignment**: `resolve_token()` (S8.2.2) must follow the same 4-tier cascade pattern as `resolve_cascaded_config()` (Core → Instance → Product → Client)
- **Data ownership**: Token schema = PFC-owned (promoted via PFC pipeline); brand_config token overrides = PFI-owned instance data anchored on `pfi_instances.brand_config` JSONB (migration 001)
- **Blocking**: F8.2 stories are BACKLOG until F59.1 (Supabase provisioning) and F59.2 (migration framework) are complete

---

## Architecture Documents (Operationalised by This Epic)

| Document | What It Defines | What This Epic Does With It |
| --- | --- | --- |
| `ARCH-PFC-PFI-Product-Client-Graph-Cascade` | 4-tier cascade model | Implements per-stage, validates resolution per environment |
| `BRIEFING-Unified-Registry-Database-Strategy` | `pfc_registry` + 10 domains | Writes migration 003, seeds per PFI, distributes via pipeline |
| `BRIEFING-GraphQL-Supabase-JSONB-MVP` | `resolve_composed_graph()`, auto-GraphQL | Deploys to all 18 projects, tests per-stage |
| `BRIEFING-PFC-EA-Arch-DB-Migrations-Azure-Supabase` | Platform choice per PFI | Phase 4 sovereignty path; Phase 0 = all Supabase Cloud |
| `CONVERGENCE-FairSlice-JSONB-Graph-Patterns` | Licensing + royalty graph patterns | Phase 5 PFI→PFI licensing artefact flow |
| `PBS/ARCHITECTURE/arch-cicd/02-promotion-pipeline-detail` | Git promotion model | Extended with `promote-db.yml` for database layer |

---

## VE Skills Evaluation

| VE Stage | Status | Notes |
| --- | --- | --- |
| **VSOM** (Vision + Strategy) | ✅ Defined | 5 pillars operationalising cascade architecture |
| **BSC** Objectives | ✅ Defined | 7 objectives across Financial, Customer, Internal, Learning |
| **KPI** Metrics | ✅ Defined | 8 KPIs (5 leading, 2 lagging, 1 future) |
| **VP** (Value Prop) | ✅ Assessed | Isolated, cascade-aware, promotable, licensable DB environments |
| **RRR** (Roles) | ✅ Referenced | Maps to Epic 10A RLS — pf-owner, pfi-admin, pfi-viewer per cascade tier |
| **Kano** | ✅ Assessed | DB-per-stage = Must-Be; Cascade resolution = Performance; PFI→PFI licensing = Attractive |
| **PMF** | ⏳ Target | BAIV-prod running on own Supabase with cascade resolving live data |

---

## Acceptance Criteria

- [ ] 18 Supabase projects provisioned and accessible
- [ ] Migrations 001-005 (including new 003 + 004) deployed to all 18 projects
- [ ] `resolve_cascaded_config()` resolves correctly at all 4 tiers in every project
- [ ] `resolve_composed_graph()` returns correct PFI-scoped graphs per project
- [ ] PFC `promote-db.yml` promotes schema dev→test→prod successfully
- [ ] PFC→PFI distribution seeds correct ontologies per series subscription
- [ ] PFC-owned rows protected (`source = 'pfc-core'`, RLS enforced) in all PFI projects
- [ ] BAIV completes full DB promotion cycle (dev→test→prod) with instance data
- [ ] All 5 PFIs have DB triads operational
- [ ] Schema drift detection running on weekly cron
- [ ] Zero cross-PFI data leakage (RLS isolation verified per cascade tier)
- [ ] Operating guide extended with DB promotion + cascade validation procedures

## Totals

| Metric | Count |
| --- | --- |
| Features | 9 |
| Stories | 37 |
| Completed Features | 0 |

---

> 📋 Full briefing: `PBS/STRATEGY/BRIEFING-Epic59-DTP-Database-Sync-Micro-SaaS-Strategy.md`



---

## Programme Sequencing — MS 9.00 Platform Database Architecture & Security Foundation

This epic is part of a 5-epic dependency chain delivering the PFC-PFI database architecture, security foundation, CI/CD pipeline integration, and cloud sovereignty options.

| Seq | WBS Code | Epic | Issue | Depends On | Status |
|-----|----------|------|-------|------------|--------|
| 1 | DB-ARCH-01 | Epic 58: PFC-Core Own Triad | #837 | — |  |
| 2 | DB-ARCH-02 | Epic 10A: Security MVP (Schema, Auth, RLS, Login) | #127 | Epic 58 |  |
| **3** | **DB-ARCH-03** | **Epic 59: DB Platform Architecture (Per-Stage, Promotion, PFC→PFI)** | **#840** | **Epic 10A** | **← YOU ARE HERE** |
| 4 | DB-ARCH-04 | Epic 31: Hub-and-Spoke CI/CD (Git + DB Promotion) | #394 | Epic 59 |  |
| 5 | DB-ARCH-05 | Epic 53: Cloud Vendor Sovereignty (Azure/Supabase per PFI) | #775 | Epic 59 |  |

**Why this is third:** This epic takes Epic 10A's deployed schema and replicates it across 18 per-stage Supabase projects (3 PFC + 15 PFI), then builds the promotion and distribution pipelines. Without Epic 10A's schema, there's nothing to promote.

**Upstream consumers (not blocked):**
- Epic 8 F8.1–F8.4, F8.6–F8.15 (Design System) — continue independently on local files
- Epic 61 F61.1–F61.4, F61.7–F61.8 (Token Gap, Slides) — continue independently
- Epic 45 (W4M-WWG) — instance data authoring continues in git

**Downstream consumers (blocked until this completes):**
- Epic 61 F61.5 (Token Storage) — needs F59.1 + F59.2
- Epic 61 F61.6 (Agentic Skills) — needs F59.1 + F59.2
- Epic 8 F8.2 S8.2.1–S8.2.4 — rolled forward to Epic 61 F61.5

**PBS Briefings:**
- `PBS/STRATEGY/BRIEFING-Epic59-DTP-Database-Sync-Micro-SaaS-Strategy.md`
- `PBS/STRATEGY/BRIEFING-Epic59-VE-Skill-Chain-OKR-VP-Kano-PMF.md`
- `PBS/STRATEGY/PFI-AIRL/08-DB-Platform-Cascade.md`



---

## Epic 61 — DevSecOps CI/CD Security & DB

=== #947 [OPEN] Epic 61: PFC-ARCH-CICD — DevSecOps, CI/CD, DB, Security & Change Control ===
## Epic 61: PBS-PFC-ARCHITECTURE.CICD-DevSecOps

| Field | Value |
| --- | --- |
| **Date** | 2026-03-08 |
| **PBS ID** | `PBS-PFC-ARCHITECTURE.CICD-DevSecOps` |
| **Status** | COLLECTING EPIC — Active |
| **Parent** | Epic 34: PF-Core Graph-Based Agentic Platform Strategy (#518) |
| **Absorbed** | Epic 60: Unified Platform Delivery (#859) — merged 2026-03-08 |
| **Absorbed** | Epic 6: Package & Distribution — npm, CLI & Docker (#58) — merged 2026-03-12 |
| **Priority** | P1 — Critical path chain |
| **Briefing** | `PBS/STRATEGY/BRIEFING-Epic61-PBS-PFC-ARCHITECTURE-CICD-DevSecOps.md` |
| **Skill Chain** | pfc-vsom → pfc-okr → pfc-kpi → pfc-vp → pfc-rrr → pfc-kano → pfc-pmf |

---

## Vision

Fully automated, gate-protected platform delivery from PFC development through to PFI production — where PFC eats its own dog food, PFI teams receive only validated artefacts, and the infrastructure scales to 100 clients without manual intervention.

## Purpose

Collecting epic that **owns the architectural domain** of CI/CD, Database, Security, and Change Control across PFC and all PFIs. Child epics are sliced by delivery concern — this epic provides the cross-cutting integration, PBS traceability, and Change Control layer that none of them individually own.

## 5 Strategy Pillars (from Epic 60)

| # | Pillar | Epic | Status |
|---|---|---|---|
| S1 | PFC Own Triad | Epic 58 (#837) | Proposed |
| S2 | PFI Triad Standardisation | Epic 31 (#441) | Implemented |
| S3 | Database Promotion Cascade | Epic 59 (#840) | Proposed |
| S4 | Convention Pipeline | Epic 31 (#441) | Implemented |
| S5 | Sealed Skill Distribution | F31.9 | Implemented |

## PMF Score: 28.5% → Target 100%

## Critical Path Chain

```
Epic 58 (PFC Triad) → Epic 10A (Security MVP) → Epic 59 (DB Cascade) → Epic 31/53 (CI/CD + Sovereignty)
       └── all governed by Epic 61 (this epic) ──┘
```

## Collected Child Epics

| PBS Sub-ID | Epic | Issue | Concern | Status |
|---|---|---|---|---|
| `PBS-PFC-ARCH-CMP.URG` | **Epic 46a** | **#683** | **PFC-ONT Unified Registry Graph — schema prerequisite for 10A** | **P1 ⬆️ NEXT: F46.7** |
| `.CICD-Pipeline` | Epic 31 | #441 | Hub-and-spoke CI/CD, conventions, promotion, sealed skills | Implemented (partial) |
| `.PFC-Triad` | Epic 58 | #837 | PFC own dev/test/prod, IP protection | F58.1 ✅, F58.2 ✅, F58.3 in progress |
| `.DB-Cascade` | Epic 59 | #840 | Per-stage Supabase, promote-db.yml, schema promotion | Blocked on Epic 46a |
| `.Security-MVP` | Epic 10A | #127 | RLS, tenant isolation, audit trail, RBAC | Blocked on Epic 46a F46.7 |
| `.Sovereignty` | Epic 53 | #775 | Cloud vendor sovereignty (Azure/Supabase) | Proposed |
| `.Change-Control` | F61.1 | #946 | Unified CC: event schema, CRUD tracking, audit, reporting | Candidate |
| `.PPM` | F61.14 | #1000 | GitHub Projects programme management — cascaded PPM structure, PPM instance data | ✅ Phase 1+2 Complete |

### Revised Critical Path

```
Epic 46a (URG/PFC-ONT) ──▶ Epic 10A (Security/DB Schema) ──▶ Epic 59 (DB Cascade) ──▶ Epic 31/53
         ↑                          ↑
  F46.7 defines what          10A deploys it
  goes in the database         into Supabase

Epic 58 (PFC Triad) runs in parallel — unblocked
```

> **Why 46a comes first:** All platform assets (ontologies, docs, skills, agents, design system components, workflows, app skeletons, databases) become URG entries. PFC-ONT (F46.7) defines the unified schema — 6 entity types + directed top-level graph. Epic 10A must deploy this, not the original 5-table design. File-copy distribution via `pfc-release.yml` is transitional; retired once DB is live with RLS.

## Features (Epic 61 — Cross-Cutting)

### F61.1: Change Control — PFC-PE-Change-Control
- [ ] S61.1.1: Define CC ChangeEvent JSONLD schema (OAA v7 compliant)
- [ ] S61.1.2: Extend sync-registry.js to emit CC events on registry changes
- [ ] S61.1.3: Create cc-events/ directory in pfc-dev (append-only JSONLD)
- [ ] S61.1.4: Add CC event step to pfc-release.yml
- [ ] S61.1.5: Add CC event step to promote.yml
- [ ] S61.1.6: Add CC event step to guard-core.yml (block logging)
- [ ] S61.1.7: Deploy drift-detection.yml with CC event logging
- [ ] S61.1.8: CC dashboard view in ontology visualiser

### F61.2: CC Database Layer (Requires Epic 59)
- [ ] S61.2.1: Create cc_events Supabase table (append-only, RLS-scoped)
- [ ] S61.2.2: PFC admin role bypasses tenant filter (full CC visibility)
- [ ] S61.2.3: PFI scope filtering (own CC events only via RLS)
- [ ] S61.2.4: Add CC step to promote-db.yml (schema migration tracking)

### F61.3: CC Reporting & Analytics
- [ ] S61.3.1: PFC CC report (cross-PFI aggregate view)
- [ ] S61.3.2: PFI CC report (own-scope dashboard)
- [ ] S61.3.3: Integration with Epic 55 portfolio reporting
- [ ] S61.3.4: Ontology extension (CICD-ONT v2.0.0 or CC-ONT v1.0.0)

### F61.4: PBS ID Assignment for Child Epics
- [ ] S61.4.1: Assign PBS-PFC-ARCHITECTURE.CICD-DevSecOps.CICD-Pipeline to Epic 31
- [ ] S61.4.2: Assign PBS-PFC-ARCHITECTURE.CICD-DevSecOps.PFC-Triad to Epic 58
- [ ] S61.4.3: Assign PBS-PFC-ARCHITECTURE.CICD-DevSecOps.DB-Cascade to Epic 59
- [ ] S61.4.4: Assign PBS-PFC-ARCHITECTURE.CICD-DevSecOps.Security-MVP to Epic 10A
- [ ] S61.4.5: Assign PBS-PFC-ARCHITECTURE.CICD-DevSecOps.Sovereignty to Epic 53
- [ ] S61.4.6: Update programme register with all PBS sub-IDs

### F61.5: PAT Governance & Drift Detection (NEW)
- [x] S61.5.1: Create `set-pat-all-triads.sh` — registry-driven PAT rotation for dev+test ✅
- [x] S61.5.2: Create `pat-drift-detection.yml` — weekly + on-registry-change validation ✅
- [ ] S61.5.3: Run initial PAT set across all 6 active PFI dev+test repos
- [ ] S61.5.4: Document PAT permissions & rotation SOP in PFI-CICD guide

### ✅ F61.14: GitHub Projects Programme Management (#1000)
- [x] DD-001–DD-005 design decisions resolved (v2.0.0 briefing)
- [x] GH Projects created: PFC Portfolio (#65), PFI-W4M (#66), PFI-BAIV (#67), PFI-AIRL (#68), W4M-WWG (#69), W4M-EOMS (#70)
- [x] Custom fields on all 6 projects: cascade_tier, pfi_instance, parent_ref, priority, triad_env
- [x] Repos linked to correct projects
- [x] PPM instance data created: portfolio + 3 programmes + 2 product projects
- [x] JP-PPM-EMC-001 join pattern registered (join-pattern-registry.json)
- [x] PPM-ONT moved to visible in AIRL + WWG graph scopes
- [x] set-pbs-id.yml extended — PFI Instance + Cascade Tier extraction live
- [ ] Phase 3: pe:ProcessPath for PFI Instance Lifecycle (next sprint)
- [ ] Migrate existing issues to correct cascaded project (ongoing)

## Features (Absorbed from Epic 60 — Doc Governance)

### ✅ F60.1: VSOM Master Briefing & Document Consolidation (#860)
- [x] S60.1.1: Publish VSOM master briefing (#863) ✅
- [ ] S60.1.2: Create PFI-CICD/README.md index with VSOM context (#864)
- [ ] S60.1.3: Create missing PFI-VHF-Build-Summary.md (#865)
- [ ] S60.1.4: Cross-reference all governed epics in briefing (#866)

### F60.2: PFI-CICD Doc Refresh (#861)
- [ ] S60.2.1: Update PFI-BAIV-Setup-Guide.md (#867)
- [ ] S60.2.2: Update PFI-AIRL-CAF-AZA docs (#868)
- [ ] S60.2.3: Update PFI-VHF-Setup-Guide.md (#869)
- [ ] S60.2.4: Update PFI-W4M-WWG docs (#870)
- [ ] S60.2.5: Update PFI-W4M-EOMS docs (#871)
- [ ] S60.2.6: Update arch-cicd/01–04 with forward references (#872)

### F60.3: Ongoing Doc Governance (#862)
- [ ] S60.3.1: Monthly doc freshness audit process (#873)
- [ ] S60.3.2: Add KPI-PD-07 to programme dashboard (#874)
- [ ] S60.3.3: Doc update notification in pfc-release.yml (#875)

## Features (Absorbed from Epic 6 — Package & Distribution)

### F6.1: npm Package
- [ ] S6.1.1: Install via `npm install @baiv/ontology-visualiser`
- [ ] S6.1.2: TypeScript type definitions included
- [ ] S6.1.3: Embed visualiser as React/Vue component

### F6.2: CLI Tool
- [ ] S6.2.1: Run `oaa-validate ontology.json` and get exit code
- [ ] S6.2.2: JSON output for parsing in scripts
- [ ] S6.2.3: Generate PNG graphs headlessly

### F6.3: Docker Image
- [ ] S6.3.1: Run via `docker run -p 8080:80 baiv/ontology-visualiser`
- [ ] S6.3.2: Mount local ontology files into container

## Two-Tier CC Scope

| Tier | Visibility | Enforcement |
|---|---|---|
| **PFC CC** | ALL objects, ALL PFIs, ALL layers (registry, CI/CD, DB, security) | Supabase PFC admin role |
| **PFI CC** | OWN registry, objects, artifacts, changes only | Supabase RLS: `tenant_id = current_tenant()` |

## 4 CC Layers

| Layer | Source | Events |
|---|---|---|
| 1. Registry | sync-registry.js, ont-registry-index.json | CREATE, UPDATE, DEPRECATE (ontology CRUD) |
| 2. CI/CD | pfc-release.yml, promote.yml, guard-core.yml | RELEASE, PROMOTE, BLOCK, DRIFT |
| 3. Database | promote-db.yml, supabase migrations | CREATE, UPDATE (schema/RLS/functions) |
| 4. Security | Workflows + manual | UPDATE (PAT, branch protection, CODEOWNERS) |

## Acceptance Criteria

- [ ] All 5 child epics have PBS sub-IDs under PBS-PFC-ARCHITECTURE.CICD-DevSecOps
- [ ] CC ChangeEvent JSONLD schema defined and OAA v7 compliant
- [ ] CC events emitted by pfc-release.yml, promote.yml, guard-core.yml
- [ ] PFC can audit/monitor/analyse/report ALL CC events (cross-PFI)
- [ ] PFI can audit/monitor/analyse/report OWN CC events only
- [ ] CC events are append-only (immutable audit trail)
- [ ] Programme register updated with all PBS sub-IDs
- [ ] PROMOTION_PAT set on all registered PFI dev+test repos
- [ ] PAT drift-detection workflow active (weekly + registry-triggered)
- [ ] VSOM master briefing published and cross-referenced
- [ ] PFI-CICD/README.md index created
- [ ] All PFI-CICD docs updated with current refs
- [ ] Monthly doc audit process established
- [ ] npm package publishable via `npm install @baiv/ontology-visualiser`
- [ ] CLI validates ontologies with exit codes (`oaa-validate`)
- [ ] Docker image builds and serves visualiser

## Totals

| Metric | Count |
| --- | --- |
| Features | 14 (5 original + 3 absorbed from Epic 60 + F61.14 + 3 absorbed from Epic 6 + F61.15 + F61.16) |
| Stories | 57 (22 + 13 + 4 new PAT + 8 from Epic 6 + 8 resilience + 2 TDD) |
| Child Epics | 5 (collected) |
| Completed Features | 1 (F61.14 Phase 1+2) |

---

> 📋 Full briefing: `PBS/STRATEGY/BRIEFING-Epic61-PBS-PFC-ARCHITECTURE-CICD-DevSecOps.md`
> 📋 CC detail: `PBS/STRATEGY/BRIEFING-PFC-PE-Change-Control-Candidate-Feature.md`
> 📋 Epic 60 briefing (absorbed): `PBS/STRATEGY/BRIEFING-Epic60-VSOM-Platform-Delivery-Infrastructure.md`
> 📋 Epic 6 absorbed 2026-03-12: F6.1 (npm), F6.2 (CLI), F6.3 (Docker) — F6.4 ✅ completed prior to absorption


### F61.15: GitHub Resilience & Cross-Account Backup (#1155) — P1 HIGH RISK
> **Briefing:** `PBS/STRATEGY/PFC-CICD-BRIEF-GitHub-Resilience-Backup-Recovery-Strategy-v1.0.0.md`
> **Skill:** SKL-122 `pfc-gh-resilience` (candidate)
> **RAID:** R-CICD-001 (#1164), R-CICD-002 (#1165 **ESCALATE**), R-CICD-003 (#1166), R-CICD-004 (#1167)

- [ ] S61.15.1: GitLab backup group + pull mirror setup (16 repos) (#1156)
- [ ] S61.15.2: Local bare clone script + launchd plist (2h schedule) (#1157)
- [ ] S61.15.3: PFI backup account mirror — client private instances (Milana/Amanda) (#1158)
- [ ] S61.15.4: `gh-metadata-export.yml` — Issues/PRs/comments REST export (daily) (#1159)
- [ ] S61.15.5: `gh-projects-export.yml` — Projects v2 GraphQL export (daily) (#1160)
- [ ] S61.15.6: `resilience-alert.yml` — workflow failure → tracking issue (#1161)
- [ ] S61.15.7: `resilience-sweep.yml` — 15m cross-repo health sweep → dashboard JSON (#1162)
- [ ] S61.15.8: Mirror integrity verification script (SHA comparison) (#1163)

**RAID Register:**

| Type | ID | Issue | Title | Score |
|---|---|---|---|---|
| Risk | R-CICD-001 | #1164 | PFC monorepo no cross-provider backup | 10 |
| Risk | R-CICD-002 | #1165 | 15 PFI repos zero backup — **ESCALATE** | **15** |
| Risk | R-CICD-003 | #1166 | Projects/Issues metadata not backed up | 10 |
| Risk | R-CICD-004 | #1167 | CI/CD failure cascade undetected | 12 |
| Req | REQ-CICD-001 | #1168 | All repos mirrored RPO ≤15m | must-have |
| Req | REQ-CICD-002 | #1169 | Metadata exported daily RPO ≤24h | must-have |
| Req | REQ-CICD-003 | #1170 | CI/CD failure alert within 5m | must-have |
| Dep | D-CICD-001 | #1171 | GitLab free-tier pull mirroring | active |
| Dep | D-CICD-002 | #1172 | PROMOTION_PAT (F61.5) | active |
| Dep | D-CICD-003 | #1173 | Workbench dashboard (Epic 40) | deferred |
| Assn | A-CICD-001 | #1174 | GitLab supports 16 concurrent mirrors | unvalidated |
| Assn | A-CICD-002 | #1175 | PFI backup accounts same PAT holder | unvalidated |

### F61.16: TDD Skill Chain Integration — SecArch-DT + TDD-DT Convergence — P1
> **Briefing:** [`PFC-ARCH-BRIEF-TDD-Skill-Chain-Integration-Strategy-v1.0.0.md`](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFC-ARCH-BRIEF-TDD-Skill-Chain-Integration-Strategy-v1.0.0.md) — **For Decision**
> **Related:** [`PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md`](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md) (SecArch-DT origin)
> **Skills:** SKL-122–SKL-129 `pfc-tdd-*` (8 candidate, skills-register-index v2.9.0)
> **Cross-refs:** Epic 75 (#1135) — SecArch-DT + TDD convergence; Epic 65 (#1106) — DSY skill chain TDD verification
> **Status:** Briefing complete, For Decision — pending approval before feature breakdown

**Scope:** TDD as mandatory cross-cutting concern across VE skill chain — no epic/feature/story planned without TDD, no prod deployment without TDD + security testing pass.

**Key deliverables:**
1. **TDD Expert Role** — cross-cutting module persona (VE/DD/PE/FDN/CA/TDD)
2. **TDD Decision Tree (TDD-DT)** — 7-gate guided test design (AI-augmented HITL)
3. **SecArch-DT + TDD-DT Convergence** — security testing as TDD subset, unified test matrix
4. **Certainty Escalation Model** — IDEA→PLAN→DEV→TEST→PROD with evidence gates
5. **CI/CD Gating** — PR-level enforcement, stage gates, release authorisation
6. **8 TDD Skills** (SKL-122–129): classifier, strategist, designer, generator, verifier, gate, metrics, orchestrator

**Stories (pending brief approval):**
- [ ] S61.16.1: Formalise TDD-DT gate definitions as PE-ONT process template
- [ ] S61.16.2: Build SKL-TDD-01 (pfc-tdd-classifier) + SKL-TDD-03 (pfc-tdd-designer) + SKL-TDD-05 (pfc-tdd-verifier) — Phase 2 first three skills




---

## Epic 70 — Database Distribution GRC Layer

=== #1019 [OPEN] Epic 70: PFC-GRC-DBA — PFC-PFI Database Distribution, GRC & Lifecycle Layer ===
## Epic 70: PFC-PFI Database Distribution, GRC & Lifecycle Layer

**Status:** Open | **Priority:** P1 (enables all PFI instance provisioning)
**Aligned to:** Epic 34 (S1 Graph-First, S3 Agentic), Epic 59 (#840 DTP Database Sync)
**Strategy Doc:** [PFC-SUPP-DISC-PFC-PFI-Distribution-Cascade-v1.0.0.md](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFC-SUPP-DISC-PFC-PFI-Distribution-Cascade-v1.0.0.md)
**Implementation Detail:** [PFC-SUPP-PROP-Supabase-Secure-Connections-CLI-API-MCP-v1.2.0.md](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFC-SUPP-PROP-Supabase-Secure-Connections-CLI-API-MCP-v1.2.0.md) §13–19

---

## Objective

Establish the database distribution cascade, data lifecycle management, RBAC model, and GRC baseline that enables PFC to provision, govern, and decommission PFI (and product/client sub-instances) safely, cost-efficiently, and in compliance with jurisdictional requirements.

This epic covers the **governance, schema, security, and data management layer** — not application features. It is a foundational dependency for all PFI instance work.

---

## Scope

### PBS-DB: Database Layer

- [ ] **PBS-DB-001** — Schema design: core tables (`ontologies`, `pfc_registry`, `audit_log`, `profiles`, `pfi_instances`, `api_keys`), RLS policies, `pfi_id` scoping
- [ ] **PBS-DB-002** — Environment triads: dev/test/prod Supabase project separation, credential scoping, no-live-data enforcement
- [ ] **PBS-DB-003** — Distribution cascade: centralised ontology store at PFC, `pfc_query_ontologies()` Edge Function, PFI read access
- [ ] **PBS-DB-004** — Data lifecycle: provisioning → active → suspended → terminating → terminated → archived, kill-switch SQL
- [ ] **PBS-DB-005** — Backup & recovery: Supabase PITR, tested restore procedures, audit log retention (7yr default)

### PBS-GRC: Governance, Risk & Compliance Layer

- [ ] **PBS-GRC-001** — MCSB baseline: 10-domain coverage at PoC/MVP level (IM, PA, NS, DP, LT, IR, PS, GS, AM, BR)
- [ ] **PBS-GRC-002** — RBAC model: 9 roles across PFC/PFI/Product/Client tiers, RLS enforcement, environment-scoped credentials
- [ ] **PBS-GRC-003** — PII protection: exclusion from JSONB graphs, `anonymise_profile()` function, right to erasure cascade
- [ ] **PBS-GRC-004** — Jurisdictional compliance config: `pfi_instances.jurisdiction` JSONB, RCSG-Series ontology selection, data residency
- [ ] **PBS-GRC-005** — Audit & control: append-only `audit_log`, actor tracking, RLS blocks mutation for all roles
- [ ] **PBS-GRC-006** — Kill-switch & incident response: emergency revocation SQL, < 60 second time-to-revoke via CLI
- [ ] **PBS-GRC-007** — Quality gate test fixtures: good/bad/baseline JSONB graphs in `pfc_registry` (artifact_type = 'test-fixture') for feature gating

---

## Phased Delivery

| Phase | Focus | Target |
| --- | --- | --- |
| P1: PoC | PBS-DB-001, PBS-GRC-001/002/003/005/007 — prove schema + RLS + audit | Single Supabase project (free tier) |
| P2: MVP | PBS-DB-002/003/004, PBS-GRC-004/006 — environment triads + PFI lifecycle | 3 Supabase projects (dev/test/prod) |
| P3: Multi-PFI | PBS-DB-005 — backup, recovery, retention | 3 projects, multiple PFIs via RLS |

---

## Acceptance Criteria

- [ ] All core tables exist with `pfi_id` scoping and RLS policies
- [ ] Test fixture confirms: PFI A cannot read PFI B data
- [ ] Dev/test credentials cannot reach prod Supabase project
- [ ] `audit_log` RLS: no UPDATE/DELETE path exists for any role
- [ ] `anonymise_profile()` function replaces PII with synthetic data
- [ ] Kill-switch revokes PFI access in < 60 seconds via CLI
- [ ] `pfi_instances.jurisdiction` JSONB drives RCSG ontology selection
- [ ] Termination protocol purges all PFI rows; audit log retained

---

## Open Decisions (from DISC §10)

| OD | Decision | Status |
| --- | --- | --- |
| OD-03 | Audit log retention: 5yr / 7yr / 10yr | Provisionally 7yr |
| OD-04 | Termination grace period: 14 / 30 / 90 days | Provisionally 30 days |
| OD-05 | Product/client sub-isolation trigger | TBD |
| OD-06 | Cross-region backup phase | TBD |
| OD-07 | Column-level encryption phase | TBD |
| OD-08 | SIEM integration phase | TBD |


---

## Epic 76 — TDD-Driven Security Strategy

=== #1196 [OPEN] Epic 76: PFC-ARCH-TDD — TDD-Driven Development & Security Strategy, Decision Trees & CI/CD Gate Governance ===
## Epic 76: PFC-ARCH-TDD — Dual-Layer Role-Aware TDD Governance

**PBS ID:** `PBS-PFC-ARCHITECTURE.TDD`
**Priority:** P1
**Parent:** Epic 34 (#518)
**Briefing:** `PFC-ARCH-BRIEF-TDD-Dual-Layer-Role-Aware-Skill-Chain-Strategy-v1.1.0.md` — For Decision

---

### Vision

Mandatory TDD governance from idea to production. Nothing moves dev→test→prod without TDD completed, staged, and security-tested. AI augments ~80% of test case generation; HITL governs every gate.

v1.1.0 introduces two complementary skill layers:
- **TDD1 — Process Pipeline**: How TDD is executed (8-gate TDD-DT, certainty ramp, CI/CD enforcement)
- **TDD2 — Role-Aware / RBAC-Sensitive**: Who tests what from which role perspective (VE, DD, PE, FDN, CA)

RBAC is treated as a test boundary. QVF certainty is a per-role vector, not a scalar. A product cannot promote if any role's certainty is below threshold.

---

### Core Rules

1. Nothing moves without TDD — no promote-to-test without TDD design approved; no promote-to-prod without HITL + TDD review complete
2. Security testing is intrinsic — SEC-DT interleaved with TDD-DT, not bolt-on
3. AI-augmented, HITL-governed — AI generates ~80% of test cases; humans validate at gates
4. Every new skill includes TEST-PLAN + ROLE-TEST-PLAN (SKBLD mandate)
5. RBAC boundaries are mandatory test assertions — role-untested boundaries block promotion

---

### Skill Chain (18 skills)

**TDD1 — Process Pipeline (SKL-122–128)**
| SKL | Skill | Function |
|---|---|---|
| SKL-122 | `pfc-tdd1-classifier` | Artefact classification & scope |
| SKL-123 | `pfc-tdd1-strategist` | Test strategy tier selection |
| SKL-124 | `pfc-tdd1-designer` | Test design specification generator |
| SKL-125 | `pfc-tdd1-generator` | Test case code & spec generator |
| SKL-126 | `pfc-tdd1-verifier` | Test evidence verification |
| SKL-127 | `pfc-tdd1-gate` | Stage gate readiness evaluator |
| SKL-128 | `pfc-tdd1-metrics` | Certainty metrics & QVF input |

**TDD2 — Role-Aware / RBAC-Sensitive (SKL-130–138)**
| SKL | Skill | Function |
|---|---|---|
| SKL-130 | `pfc-tdd2-classifier` | Role & RBAC context classifier |
| SKL-131 | `pfc-tdd2-testability` | Role-perspective testability assessor |
| SKL-132 | `pfc-tdd2-test-designer` | Role-bounded test case designer |
| SKL-133 | `pfc-tdd2-coverage-analyser` | Role-perspective coverage analyser |
| SKL-134 | `pfc-tdd2-integration-mapper` | Integration point & contract test mapper |
| SKL-135 | `pfc-tdd2-gate-validator` | Role authority gate validator |
| SKL-136 | `pfc-tdd2-spec-generator` | Role-scoped TDD specification generator |
| SKL-137 | `pfc-tdd2-regression-tracker` | Role-visible regression risk tracker |
| SKL-138 | `pfc-tdd2-security-bridge` | TDD→SEC-DT security bridge |

**Shared Orchestrator (SKL-129)**
| SKL | Skill | Function |
|---|---|---|
| SKL-129 | `pfc-tdd-orchestrator` | Dual-layer pipeline orchestrator (AGENT) |

---

### Features

- [ ] F76.1: TDD Expert Role Definition — #1197
- [ ] F76.2: TDD Decision Tree (TDD-DT) — 8-Gate Process — #1198
- [ ] F76.3: Security Decision Tree (SEC-DT) — 6-Gate Integration — #1199
- [ ] F76.4: TDD Skill Chain — SKL-TDD1 + SKL-TDD2 (18 skills) — #1200
- [ ] F76.5: SKBLD Integration — TEST-PLAN + ROLE-TEST-PLAN scaffolding — #1201
- [ ] F76.6: Planning Template Enhancements — #1202

**Sister Features (Epic 61):**
- [ ] F61.23: CI/CD TDD Gate — promote-to-test — #1203
- [ ] F61.24: CI/CD TDD Gate — promote-to-prod (dual sign-off) — #1205

---

### Phased Delivery

**Phase 1 — Foundation:** TDD Expert role, TDD1 skills (SKL-122–128), TDD2-classifier + testability (SKL-130–131), `tdd-gate.yml` (scalar certainty), SKBLD TEST-PLAN, attribution correction
**Phase 2 — Role-Aware Layer:** TDD2 skills (SKL-132–138), ROLE-TEST-PLAN scaffold, `tdd-gate.yml` role certainty gates, QVF per-role certainty vector
**Phase 3 — Full Pipeline:** SEC-DT gates, dual sign-off, E2E suite, pfc-tdd-orchestrator, `dt:TDDDecisionRecord`
**Phase 4 — Continuous:** Regression tracker → graph evolution, AI test optimisation, TDD metrics dashboard

---

### Dependencies

| Dependency | Status |
|---|---|
| Vitest runner | Available |
| SKBLD Dtree engine | Available |
| SA-DT (URG brief) | For Decision — blocker for SEC-DT full linkage |
| Promotion pipeline | Available |
| GitHub Actions CI | Available |
| RBAC schema | Partial |
| QVF per-role certainty vector | Not built — Phase 2 |



---

## Security-First Platform Implementation Plan

# PLAN: Security-First OAA Workbench & Unified Registry Platform

## Context

The OAA Visualiser is a zero-build-step browser app (41 ES modules, 2210 tests) with **zero authentication** — anyone can access everything. Epic 10A (#127, P0) is fully decomposed (15 stories, 0 started) and gates all PFI instance epics. Three Supabase migrations (001, 002, 005) are written but not deployed. The unified registry strategy (ont-registry-index.json → pfc-registry-index.json) is documented but not started. This plan delivers security and login first, then builds toward the multi-artifact unified registry — covering ontologies, design artefacts, skills, and code as first-class governed entities.

**Aligned to:** Epic 10A (#127), [Epic 34: PFC-VSOM — PF Strategy (#518)](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518), DAILY-TODO P1-P2 critical path, MVP-Security-VSOM-v1.1.0, BRIEFING-Unified-Registry-Database-Strategy, PROPOSAL-Supabase-Secure-Connections-API-MCP-v1.0.0

---

## Phase 0: Deploy Existing Schema (Pre-flight)

**Goal:** Get Supabase live with the 5-table schema + RLS + seed data. No code changes.

| Step | Action | Verification |
|------|--------|-------------|
| 0.1 | Verify Supabase project access (dashboard + SQL editor) | Can run `SELECT 1` |
| 0.2 | Apply `001_create_schema.sql` | 5 tables in `public` schema |
| 0.3 | Apply `002_rls_policies.sql` | 11 RLS policies active |
| 0.4 | Run `seed.sql` | 3 PFI instances (PF-CORE, BAIV-AIV, AIRL-AIR) |
| 0.5 | Enable Email auth, create test user | Auto-profile trigger fires → viewer role |
| 0.6 | Promote test user to pf-owner, assign PF-CORE | Owner can query all tables |

**Files (read-only, already written):**
- `PBS/TOOLS/ontology-visualiser/supabase/migrations/001_create_schema.sql`
- `PBS/TOOLS/ontology-visualiser/supabase/migrations/002_rls_policies.sql`
- `PBS/TOOLS/ontology-visualiser/supabase/seed.sql`

**Do NOT apply 005 yet** — it depends on migrations 003/004 which don't exist.

**Stories:** S10A.1.1–S10A.1.4 (#439–#442, existing)

---

## Phase 1: Auth UI + Supabase Client in Browser

**Goal:** Login/logout in the browser. Unauthenticated users see a login overlay; authenticated users see the workbench.

### New Files

| File | Purpose |
|------|---------|
| `js/config.js` (gitignored) | `SUPABASE_URL` + `SUPABASE_ANON_KEY` exports |
| `js/supabase-client.js` | Client init, `signIn()`, `signUp()`, `signOut()`, `getSession()`, `onAuthStateChange()`, `getSupabase()` singleton |
| `js/auth-ui.js` | Full-screen login overlay (email+password), sign-up toggle, error display, show/hide workbench |
| `tests/supabase-client.test.js` | Auth module tests (mock Supabase client) |
| `tests/auth-ui.test.js` | Login UI render/hide tests |

### Modified Files

| File | Change |
|------|--------|
| `browser-viewer.html` | Add `supabase-js` CDN script tag (after vis-network), add `<div id="auth-overlay">` before workbench content, add `<script type="module">` import for supabase-client.js + auth-ui.js |
| `js/state.js` | Add auth properties: `supabaseClient`, `currentUser`, `currentProfile`, `currentPFIs[]`, `activePFI`, `authReady` |
| `js/app.js` | Auth-gate the `DOMContentLoaded` boot: check session → load profile + PFIs → show workbench, OR show login overlay. Add `onAuthStateChange` listener for sign-out |
| `js/nav-action-registry.js` | Add `signOut` action to ACTION_REGISTRY |

### Boot Sequence Change (app.js)

```
DOMContentLoaded →
  1. initSupabase()
  2. getSession()
  3. IF session → loadUserProfile() → loadUserPFIs() → showWorkbench() → loadSkeleton → loadRegistry
  4. ELSE → showLoginOverlay()
  5. onAuthStateChange(SIGNED_OUT → showLoginOverlay)
```

### loadUserProfile() fetches:
- `profiles` row for `auth.uid()` → `state.currentProfile` (id, display_name, role)
- `user_pfi_access` JOIN `pfi_instances` → `state.currentPFIs[]`
- Default `state.activePFI` to first assigned PFI or PF-CORE

**Stories:** S10A.2.1–S10A.2.6 (mix of existing #448, #457 + new)

---

## Phase 2: RBAC Enforcement in UI

**Goal:** UI respects the user's role. Viewers can't edit. PFI dropdown shows only assigned PFIs.

### New Files

| File | Purpose |
|------|---------|
| `js/rbac-guards.js` | `canEdit()`, `canDelete()`, `canManageUsers()`, `isOwner()` — check `state.currentProfile.role` |
| `tests/rbac-guards.test.js` | Permission matrix tests |

### Modified Files

| File | Change |
|------|--------|
| `js/authoring-ui.js` | Disable authoring toolbar for viewers (wire `canEdit()` guard) |
| `js/app.js` | EMC nav PFI dropdown filters to `state.currentPFIs` (pf-owner sees all) |
| `js/pfi-lifecycle-ui.js` | User assignment CRUD (admin: add/remove users from PFI; owner: change roles) |
| `browser-viewer.html` | Z3 context identity bar: user name + role badge + active PFI |

### RBAC Matrix (4 roles × key actions)

| Action | pf-owner | pfi-admin | pfi-member | viewer |
|--------|----------|-----------|------------|--------|
| View ontologies | ✅ all PFIs | ✅ assigned | ✅ assigned | ✅ assigned |
| Edit ontologies | ✅ | ✅ | ✅ | ❌ |
| Delete ontologies | ✅ | ✅ | ❌ | ❌ |
| Manage users | ✅ all | ✅ own PFI | ❌ | ❌ |
| View audit log | ✅ all | ✅ own PFI | ❌ | ❌ |
| Manage PFI instances | ✅ | ❌ | ❌ | ❌ |

**Stories:** S10A.3.1–S10A.3.5 (mix of existing #458, #459 + new)

---

## Phase 3: RLS-Protected Data Layer (Replace IndexedDB)

**Goal:** Ontology CRUD goes through Supabase. RLS filters by PFI automatically. IndexedDB becomes offline cache.

### New Files

| File | Purpose |
|------|---------|
| `js/supabase-data.js` | `fetchOntologies(pfiCode)`, `saveOntology()`, `updateOntology()`, `deleteOntology()` — all write audit_log entries |
| `js/supabase-provider.js` | `DataProvider` class: try Supabase first, fall back to IndexedDB (`library-manager.js`). Single API surface for all data consumers |
| `scripts/seed-ontologies.js` | One-time Node.js script: read ont-registry-index.json → fetch each JSON-LD → INSERT into `ontologies` table as PF-CORE scope |
| `tests/supabase-data.test.js` | CRUD + audit log tests (mocked client) |

### Modified Files

| File | Change |
|------|--------|
| `js/app.js` | Replace `library-manager.js` calls with `DataProvider` calls |
| `js/library-manager.js` | Becomes the IndexedDB fallback path inside DataProvider (no API change needed for its callers if we route through provider) |

### Audit Log Pattern
Every mutation writes to `audit_log`:
```javascript
{ user_id, pfi_id, action: 'ontology_create|update|delete', resource: ontName, detail: { version, source: 'browser' } }
```

**Stories:** S10A.4.1–S10A.4.6 (existing #460–#462 + new)

---

## Phase 4: Audit & Governance UI (Completes Epic 10A)

**Goal:** Admin audit viewer. User management. Protected zone rendering by role.

### New Files

| File | Purpose |
|------|---------|
| `js/audit-viewer.js` | Z17 panel: query `audit_log` (RLS-scoped), sortable/filterable table, pagination, CSV export |
| `tests/audit-viewer.test.js` | Audit viewer tests |

### Modified Files

| File | Change |
|------|--------|
| `js/pfi-lifecycle-ui.js` | Supabase-backed user management: list users in PFI, invite, remove, change role |
| `js/app.js` | Zone visibility by role: Z17 (audit) → admin+, Z22 (skeleton inspector) → pf-owner |

**At this point Epic 10A (#127) is DONE — all 15 stories delivered.**

**Stories:** S10A.5.1–S10A.5.4 (existing #463–#465 + new)

---

## Phase 5: Unified Registry Foundation

**Goal:** Write missing migrations. Evolve ont-registry-index.json → pfc-registry-index.json. Seed the unified registry table.

### New Files

| File | Purpose |
|------|---------|
| `supabase/migrations/003_api_keys.sql` | `api_keys` table: `key_hash`, `pfi_id`, `permissions[]`, `rate_limit`, `expires_at`. RLS: owner manages all, admin manages own PFI |
| `supabase/migrations/004_pfc_registry.sql` | `pfc_registry` table (10 artifact types, cascade scope), `resolve_artifact_config()` function (core→instance→product→client merge), RLS, GIN indexes |
| `ontology-library/pfc-registry-index.json` | Copy + extend ont-registry-index.json: add `skillEntries[]`, `joinPatternEntries[]`, `documentEntries[]`, extend `pfiInstances[]` with `instanceSkills[]` |
| `scripts/registry-seed.js` | Node.js: read pfc-registry-index.json → upsert into `pfc_registry` table per artifact type + scope |

### Modified Files

| File | Change |
|------|--------|
| `js/github-loader.js` | Fetch `pfc-registry-index.json` first, fall back to `ont-registry-index.json` |
| `js/registry-browser.js` | Add tabs/sections for skills, join patterns, documents alongside ontologies |

### Migration Deploy Order
1. Apply 003_api_keys.sql
2. Apply 004_pfc_registry.sql
3. Apply 005_graphql_layer.sql (already written, can now run)
4. Run registry-seed.js

### pfc_registry Table Schema (from Unified Registry Briefing)
```sql
CREATE TABLE pfc_registry (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  artifact_id TEXT NOT NULL,           -- e.g. "pfc:skill-vsom-v1.0.0"
  artifact_type TEXT NOT NULL,         -- ontology|skill|agent|design-token|skeleton|db-schema|code-module|schema|document|join-pattern
  name TEXT NOT NULL,
  version TEXT NOT NULL,
  scope TEXT NOT NULL,                 -- core|instance|product|client
  instance_id TEXT,                    -- PFI code (NULL for core)
  status TEXT DEFAULT 'active',
  override_type TEXT DEFAULT 'inherit', -- inherit|extend|override
  configuration JSONB DEFAULT '{}',
  base_configuration JSONB DEFAULT '{}',
  dependencies JSONB DEFAULT '[]',
  source_path TEXT,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(artifact_id, scope, instance_id)
);
```

**Stories:** Under Epic 34 (#518) — F34.5: Registry Database Layer (S34.5.1–S34.5.7)

---

## Phase 6: Multi-Artifact Registry Population

**Goal:** Skills, design tokens, skeletons, code modules become first-class registry entries.

### Modified Files

| File | Change |
|------|--------|
| `pfc-registry-index.json` | Add `designTokenEntries[]`, `skeletonEntries[]`, `dbSchemaEntries[]`, `codeModuleEntries[]` |
| `js/emc-composer.js` | Add `constrainToInstanceSkills()`, `constrainToInstanceTokens()` parallel to existing `constrainToInstanceOntologies()` |
| `js/registry-browser.js` | Render all 10 artifact type tabs |
| `scripts/registry-seed.js` | Reseed with expanded artifact set |

**Stories:** Under Epic 34 (#518) — F34.6: Multi-Artifact Registry (S34.6.1–S34.6.6)

---

## Phase 7: Secure API Layer (Edge Functions + MCP)

**Goal:** 3-layer secure connections: browser (done), Edge Functions (API gateway), MCP server (agent access).

### New Files

| File | Purpose |
|------|---------|
| `supabase/functions/_shared/auth.ts` | Shared auth: service key, API key, JWT paths |
| `supabase/functions/ontologies-query/index.ts` | Read ontologies with PFI scope |
| `supabase/functions/artifacts-write/index.ts` | Write registry artifacts + audit |
| `supabase/functions/registry-resolve/index.ts` | `resolve_artifact_config()` RPC |
| `supabase/functions/graph-export/index.ts` | Export composed graphs |
| `supabase/functions/audit-query/index.ts` | Read audit trail (admin+) |
| `supabase/functions/auth-webhook/index.ts` | User lifecycle events |
| `supabase/functions/sync-registry/index.ts` | Git→Supabase sync (CI trigger) |
| `supabase/functions/health/index.ts` | Status endpoint |
| `PBS/TOOLS/pfc-supabase-mcp/src/server.ts` | MCP server: 5 tools (`pfc_query_ontologies`, `pfc_write_artifact`, `pfc_resolve_config`, `pfc_export_graph`, `pfc_audit_query`) |

**Stories:** Under Epic 34 (#518) — F34.7: Secure API Layer (S34.7.1–S34.7.11)

---

## Dependency Chain

```
Phase 0: Deploy schema ──→ Phase 1: Auth UI ──→ Phase 2: RBAC ──→ Phase 3: Data layer
                                                                         │
                                                               Phase 4: Audit (E10A DONE)
                                                                         │
                                                               Phase 5: Unified registry
                                                                    ╱         ╲
                                                        Phase 6: Multi-     Phase 7: Edge
                                                        artifact registry   Functions + MCP
```

Phases 6 and 7 can run in parallel after Phase 5.

---

## Epic/Issue Structure Summary

| Phase | Epic | Features | Stories |
|-------|------|----------|---------|
| 0–4 | Epic 10A (#127) | F10A.1–F10A.5 | ~15 stories (mix existing + new) |
| 5–7 | Epic 34 (#518) | F34.5–F34.7 | ~24 stories (new) |

---

## Verification Strategy

- **Unit tests**: Each phase adds test files (vitest, mocked Supabase client)
- **RLS verification**: SQL queries as different roles confirming row visibility
- **Regression**: `npx vitest run` must stay at 100% pass after each phase
- **Manual**: Login flow, PFI switching, role-gated UI elements
- **Security review**: After Phase 4 (Epic 10A complete) — no bypasses, no data leaks, audit trail complete

---

## Key Existing Files to Reuse (Not Rewrite)

| File | Reuse |
|------|-------|
| `js/library-manager.js` | Becomes IndexedDB fallback inside DataProvider |
| `js/pfi-lifecycle-ui.js` | Extend with Supabase-backed user management |
| `js/nav-action-registry.js` | Add signOut action |
| `js/authoring-ui.js` | Wire rbac-guards.js canEdit() check |
| `js/emc-composer.js` | Extend constrainToInstance*() pattern |
| `js/registry-browser.js` | Extend with multi-artifact tabs |
| `js/github-loader.js` | Update registry URL with fallback |


---

## Platform Database Architecture — MS9 Report

# Programme Summary — MS 9.00 Platform Database Architecture & Security Foundation

**Generated**: 2026-03-05
**Milestone**: [MS 9.00](https://github.com/ajrmooreuk/Azlan-EA-AAA/milestone/11)
**Repository**: [ajrmooreuk/Azlan-EA-AAA](https://github.com/ajrmooreuk/Azlan-EA-AAA)
**Label**: `cross-programme`
**Parent**: [Epic 34: PFC-VSOM — PF Strategy (#518)](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518)

---

## Programme Vision

Deliver the PFC-PFI database architecture as a live, per-stage, cascade-aware platform — covering git triad separation, security foundations (Auth, RLS, RBAC), per-stage database isolation, CI/CD pipeline integration, and cloud sovereignty options.

---

## Programme Totals

| Metric | Count |
|--------|-------|
| Epics | 5 |
| Features | 32 |
| Stories | 139 |
| Stories Done | 16 |
| Stories Remaining | 121 |
| Features Done | 2 (F31.8, F31.9) |
| **F31.11 added** | Ontology Distribution Rights — per-PFI subscription, read-only enforcement, DB migration path (#973) |
| **Last updated** | 2026-03-08 |

---

## Dependency Chain

```
DB-ARCH-01  Epic 58 (#837) ── PFC-Core Own Triad
    │
    ▼
DB-ARCH-02  Epic 10A (#127) ── Security MVP (Schema, Auth, RLS, Login)
    │
    ▼
DB-ARCH-03  Epic 59 (#840) ── DB Platform Architecture (Per-Stage, Promotion, PFC→PFI)
    │
    ├──────────────────┐
    ▼                  ▼
DB-ARCH-04          DB-ARCH-05
Epic 31 (#394)      Epic 53 (#775)
CI/CD Pipeline      Cloud Sovereignty
(Git + DB Promote)  (Azure/Supabase per PFI)
```

---

## PBS Briefing Index

| WBS | Epic | Primary PBS Briefing | Additional Briefings |
|-----|------|---------------------|---------------------|
| DB-ARCH-01 | Epic 58 | `PBS/STRATEGY/BRIEFING-Epic58-PFC-Core-Triad-Separation-Strategy.md` | — |
| DB-ARCH-02 | Epic 10A | `PBS/ARCHITECTURE/Security/MVP-Security-VSOM-v1.1.0.md` | `PBS/ARCHITECTURE/ARCH-MVP-Security/RBAC-PERMISSION-MATRIX.md`, `PBS/ONTOLOGIES/ontology-library/VE-Series/RRR-ONT/RRR_RACI_RBAC_Ontology_Visual_Guide.md` |
| DB-ARCH-03 | Epic 59 | `PBS/STRATEGY/BRIEFING-Epic59-DTP-Database-Sync-Micro-SaaS-Strategy.md` | `PBS/STRATEGY/BRIEFING-Epic59-VE-Skill-Chain-OKR-VP-Kano-PMF.md`, `PBS/STRATEGY/PFI-AIRL/08-DB-Platform-Cascade.md` |
| DB-ARCH-04 | Epic 31 | `PBS/ARCHITECTURE/arch-cicd/01-hub-and-spoke-proposal.md` | `PBS/ARCHITECTURE/arch-cicd/02-promotion-pipeline-detail.md`, `PBS/ARCHITECTURE/arch-cicd/04-operating-guide.md` |
| DB-ARCH-05 | Epic 53 | `PBS/STRATEGY/BRIEFING-PFC-EA-Arch-DB-Migrations-Azure-Supabase.md` | `PBS/STRATEGY/STRATEGY-Cloud-Vendor-Sovereignty-Multi-Platform-v1.0.0.md`, `PBS/STRATEGY/PFI-AIRL/05-Execution-Architecture.md` |

---

## Epic & Feature/Story Breakdown

---

### DB-ARCH-01 — Epic 58: PFC-Core Own Triad

**Issue**: [#837](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/837) | **Priority**: P0 | **Features**: 4 | **Stories**: 16 (0 done)
**Depends on**: — (first in chain)
**PBS**: `PBS/STRATEGY/BRIEFING-Epic58-PFC-Core-Triad-Separation-Strategy.md`

#### F58.1: PFC Triad Bootstrap — [#838](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/838)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-01.1.1 | Extend `bootstrap-triad.sh` for PFC-internal variant | [#839](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/839) | Backlog |
| DB-ARCH-01.1.2 | Create pfc-dev, pfc-test, pfc-prod repos with branch protection | [#841](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/841) | Backlog |
| DB-ARCH-01.1.3 | Seed pfc-dev with distributable assets from Azlan-EA-AAA | [#842](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/842) | Backlog |
| DB-ARCH-01.1.4 | Configure promotion.env for PFC triad | [#843](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/843) | Backlog |

#### F58.2: Pipeline Migration — [#844](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/844)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-01.2.1 | Move `pfc-release.yml` to pfc-prod, update source paths | [#845](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/845) | Backlog |
| DB-ARCH-01.2.2 | Update PROMOTION_PAT scope to pfc-prod only | [#846](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/846) | Backlog |
| DB-ARCH-01.2.3 | Add PFC-specific validation jobs to pfc-test CI | [#847](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/847) | Backlog |
| DB-ARCH-01.2.4 | Dry-run full pipeline: pfc-dev → pfc-test → pfc-prod → PFI dev | [#848](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/848) | Backlog |

#### F58.3: Cutover and Documentation — [#849](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/849)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-01.3.1 | First tagged release from pfc-prod (`pfc-v2.0.0`) | [#850](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/850) | Backlog |
| DB-ARCH-01.3.2 | Archive release trigger from Azlan-EA-AAA | [#851](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/851) | Backlog |
| DB-ARCH-01.3.3 | Update ARCH-CICD-004 operating guide | [#852](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/852) | Backlog |
| DB-ARCH-01.3.4 | Update training guide and team session agenda | [#853](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/853) | Backlog |
| DB-ARCH-01.3.5 | Update e2e-cicd-diagram.md with PFC triad layer | [#854](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/854) | Backlog |

#### F58.4: Ongoing Governance — [#855](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/855)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-01.4.1 | Drift detection between Azlan-EA-AAA reference and pfc-dev | [#856](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/856) | Backlog |
| DB-ARCH-01.4.2 | Monthly audit process | [#857](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/857) | Backlog |
| DB-ARCH-01.4.3 | Document Azlan-EA-AAA post-separation role | [#858](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/858) | Backlog |

---

### DB-ARCH-02 — Epic 10A: Security MVP — Multi-PFI Foundation

**Issue**: [#127](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/127) | **Priority**: P0 | **Features**: 4 | **Stories**: 14 (0 done)
**Depends on**: Epic 58 (#837)
**PBS**: `PBS/ARCHITECTURE/Security/MVP-Security-VSOM-v1.1.0.md`

#### F10A.1: Supabase Schema & RLS Foundation — [#435](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/435)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-02.1.1 | Create Supabase project and configure environment | [#439](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/439) | Backlog |
| DB-ARCH-02.1.2 | Deploy 5-table schema with constraints | [#440](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/440) | Backlog |
| DB-ARCH-02.1.3 | Deploy RLS policies with role-gated write/delete separation | [#441](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/441) | Backlog |
| DB-ARCH-02.1.4 | Seed PFI instances and verify RLS isolation — **Gate G1** | [#442](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/442) | Backlog |

#### F10A.2: Authentication & User Management — [#436](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/436)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-02.2.1 | Integrate Supabase Auth email provider in visualiser | [#448](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/448) | Backlog |
| DB-ARCH-02.2.2 | Create auto-profile trigger and default role assignment | [#457](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/457) | Backlog |
| DB-ARCH-02.2.3 | Implement PFI assignment flow for admin users | [#458](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/458) | Backlog |
| DB-ARCH-02.2.4 | Build PFI context switcher component | [#459](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/459) | Backlog |

#### F10A.3: PFI-Scoped Ontology Storage — [#437](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/437)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-02.3.1 | Create SupabaseProvider data-store abstraction module | [#460](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/460) | Backlog |
| DB-ARCH-02.3.2 | Replace IndexedDB reads with Supabase PFI-scoped queries | [#461](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/461) | Backlog |
| DB-ARCH-02.3.3 | Implement audit log writes on all ontology mutations | [#462](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/462) | Backlog |

#### F10A.4: Minimal Security UI — [#438](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/438)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-02.4.1 | Build login/logout form with Supabase Auth | [#463](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/463) | Backlog |
| DB-ARCH-02.4.2 | Implement protected routes and role-based access | [#464](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/464) | Backlog |
| DB-ARCH-02.4.3 | Create admin-only audit log viewer page | [#465](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/465) | Backlog |

---

### DB-ARCH-03 — Epic 59: Platform Database Architecture — PFC-PFI Cascade, Per-Stage Isolation & Micro-SaaS Foundation

**Issue**: [#840](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/840) | **Priority**: P0 Critical | **Features**: 9 | **Stories**: 37 (0 done)
**Depends on**: Epic 10A (#127)
**PBS**: `PBS/STRATEGY/BRIEFING-Epic59-DTP-Database-Sync-Micro-SaaS-Strategy.md`

#### F59.1: Supabase Project Provisioning — Phase 1

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-03.1.1 | Provision 3 PFC Supabase projects (pfc-core-dev, test, prod) | — | Backlog |
| DB-ARCH-03.1.2 | Provision 15 PFI Supabase projects (5 instances x 3 stages) | — | Backlog |
| DB-ARCH-03.1.3 | Document project naming, billing, region strategy | — | Backlog |
| DB-ARCH-03.1.4 | Configure SUPABASE_URL + ANON_KEY + SERVICE_KEY on all 18 repos | — | Backlog |

#### F59.2: Missing Migrations & Full Schema Deployment — Phase 1

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-03.2.1 | Write migration 003 (pfc_registry + resolve_artifact_config) | — | Backlog |
| DB-ARCH-03.2.2 | Write migration 004 (resolve_cascaded_config — 4-tier cascade) | — | Backlog |
| DB-ARCH-03.2.3 | Deploy migrations 001-005 to all 18 Supabase projects | — | Backlog |
| DB-ARCH-03.2.4 | Validate RLS policies per project with test users per cascade tier | — | Backlog |

#### F59.3: Secrets & Access Resolution — Phase 1

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-03.3.1 | Create scoped PATs per triad (PFC + per-PFI) | — | Backlog |
| DB-ARCH-03.3.2 | Set PROMOTION_PAT on remaining 3 PFI repos | — | Backlog |
| DB-ARCH-03.3.3 | Verify guard-core.yml blocks human PRs on all PFI repos | — | Backlog |

#### F59.4: PFC DB Promotion Workflow — Phase 2

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-03.4.1 | Create `promote-db.yml` for PFC triad (dev→test→prod) | — | Backlog |
| DB-ARCH-03.4.2 | Schema dry-run validation (`supabase db push --dry-run`) on test | — | Backlog |
| DB-ARCH-03.4.3 | SME approval gate for test→prod with `needs-sme-approval` label | — | Backlog |
| DB-ARCH-03.4.4 | Cascade resolution integration test suite (all 4 tiers) | — | Backlog |

#### F59.5: PFC→PFI Database Distribution — Phase 2

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-03.5.1 | Create `pfc-db-release.yml` workflow | — | Backlog |
| DB-ARCH-03.5.2 | Series-subscription filter using `hubSpokeConfig.ontologySeries` | — | Backlog |
| DB-ARCH-03.5.3 | Seed core ontologies from registry → JSONB with `source='pfc-core'` | — | Backlog |
| DB-ARCH-03.5.4 | Seed `pfc_registry` core rows (10 artifact domains) | — | Backlog |
| DB-ARCH-03.5.5 | Validate full cycle: pfc-dev → pfc-test → pfc-prod → BAIV-dev | — | Backlog |

#### F59.6: PFI DB Promotion Template — Phase 3

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-03.6.1 | Create `promote-db.yml` template for PFI triads | — | Backlog |
| DB-ARCH-03.6.2 | PFI-owned data export (`source != 'pfc-core'` filter) | — | Backlog |
| DB-ARCH-03.6.3 | FK integrity + cascade resolution validation post-promotion | — | Backlog |
| DB-ARCH-03.6.4 | Seed BAIV-dev with VP + RRR + graph scope + brand_config data | — | Backlog |

#### F59.7: PFI Triad Activation — Phase 3

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-03.7.1 | BAIV full cycle: dev → test → prod (PoC — lead instance) | — | Backlog |
| DB-ARCH-03.7.2 | AIRL full cycle | — | Backlog |
| DB-ARCH-03.7.3 | W4M-WWG full cycle | — | Backlog |
| DB-ARCH-03.7.4 | W4M-EOMS full cycle | — | Backlog |
| DB-ARCH-03.7.5 | VHF full cycle | — | Backlog |

#### F59.8: DB Drift Detection & Governance — Phase 4

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-03.8.1 | Create `db-drift-detect.yml` cron workflow | — | Backlog |
| DB-ARCH-03.8.2 | Schema diff reporting between stages (per PFC + per PFI) | — | Backlog |
| DB-ARCH-03.8.3 | Auto-create issues on drift detection | — | Backlog |
| DB-ARCH-03.8.4 | DB-inclusive operating guide + runbooks | — | Backlog |
| DB-ARCH-03.8.5 | Sovereignty adaptation docs (Azure/self-hosted per Epic 53) | — | Backlog |

#### F59.9: Cross-Instance Licensing Framework — Phase 5 (Horizon 2)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-03.9.1 | Extend `pfc_registry` for `licensed-pfi-contribution` type | — | Backlog |
| DB-ARCH-03.9.2 | PFI export workflow (source PFI-prod → PFC-prod → target PFI-dev) | — | Backlog |
| DB-ARCH-03.9.3 | FairSlice royalty integration (CONVERGENCE patterns) | — | Backlog |
| DB-ARCH-03.9.4 | First cross-PFI transfer PoC (BAIV VP template → AIRL) | — | Backlog |
| DB-ARCH-03.9.5 | Sovereignty tier licensing rules (Epic 53 alignment) | — | Backlog |

---

### DB-ARCH-04 — Epic 31: Multi-Instance Platform Delivery — Hub-and-Spoke CI/CD Pipeline

**Issue**: [#394](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/394) | **Priority**: P0 | **Features**: 9 | **Stories**: 37 (14 done)
**Depends on**: Epic 59 (#840)
**PBS**: `PBS/ARCHITECTURE/arch-cicd/01-hub-and-spoke-proposal.md`

#### F31.1: PFC-Core Release Workflow — [#400](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/400)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-04.1.1 | Create `pfc-release.yml` workflow | [#411](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/411) | Backlog |
| DB-ARCH-04.1.2 | Define release archive structure | [#412](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/412) | Backlog |
| DB-ARCH-04.1.3 | Tag first PFC release `pfc-v1.0.0` | [#413](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/413) | Backlog |
| DB-ARCH-04.1.4 | Update GitHub Pages deploy to include release metadata | [#414](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/414) | Backlog |

#### F31.2: Convention Promotion Pipeline — [#406](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/406)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-04.2.1 | Create `convention-manifest.json` | [#415](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/415) | Backlog |
| DB-ARCH-04.2.2 | Create `promote.yml` workflow | [#416](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/416) | Backlog |
| DB-ARCH-04.2.3 | Create `auto-tag.yml` workflow | [#417](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/417) | Backlog |
| DB-ARCH-04.2.4 | Configure branch protection per tier | [#418](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/418) | Backlog |

#### F31.3: Live Repo Sync & Version Pinning — [#407](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/407)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-04.3.1 | Create `live-repos.json` registry | [#419](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/419) | Backlog |
| DB-ARCH-04.3.2 | Create `sync-to-live.yml` workflow | [#420](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/420) | Backlog |
| DB-ARCH-04.3.3 | Implement `azlan-workflow-version` pin check | [#421](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/421) | Backlog |
| DB-ARCH-04.3.4 | Create sync PR template with changelog diff | [#422](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/422) | Backlog |
| DB-ARCH-04.3.5 | Programme document distribution via pfc-release.yml | [#883](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/883) | Backlog |
| DB-ARCH-04.3.6 | PFC project board item visibility for PFI stakeholders (read-only mirror) | [#884](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/884) | Backlog |

#### F31.4: Bootstrap & Instance Provisioning — [#408](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/408)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-04.4.1 | Create `bootstrap-triad.sh` | [#423](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/423) | ✅ Done |
| DB-ARCH-04.4.2 | Create instance repo template structure | [#424](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/424) | Backlog |
| DB-ARCH-04.4.3 | Apply labels and project board setup | [#425](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/425) | Backlog |
| DB-ARCH-04.4.4 | Bootstrap BAIV-AIV triad as proof-of-concept | [#426](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/426) | Backlog |

#### F31.5: Drift Detection & Governance — [#409](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/409)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-04.5.1 | Create `drift-detection.yml` cron workflow | [#427](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/427) | Backlog |
| DB-ARCH-04.5.2 | Implement convention file diff against prod | [#428](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/428) | Backlog |
| DB-ARCH-04.5.3 | Auto-create/update GitHub issues on drift | [#429](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/429) | Backlog |
| DB-ARCH-04.5.4 | Create drift resolution runbook | [#430](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/430) | Backlog |

#### F31.6: Operating Guide & Runbooks — [#410](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/410)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-04.6.1 | Write operating guide | [#431](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/431) | ✅ Done |
| DB-ARCH-04.6.2 | Write secret management runbook | [#432](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/432) | Backlog |
| DB-ARCH-04.6.3 | Write incident response runbook | [#433](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/433) | Backlog |
| DB-ARCH-04.6.4 | Write onboarding guide for new PFI delivery teams | [#434](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/434) | Backlog |

#### F31.7: CICD-ONT — CI/CD Pipeline & Delivery Ontology — [#585](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/585)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-04.7.1 | Create `pfc-cicd-v1.0.0-oaa-v6.json` | — | ✅ Done |
| DB-ARCH-04.7.2 | Create `Entry-ONT-CICD-001.json` registry entry | — | ✅ Done |
| DB-ARCH-04.7.3 | Update `ont-registry-index.json` v9.1.0 | — | ✅ Done |
| DB-ARCH-04.7.4 | Validate with visualiser (load + cross-ref resolution) | — | Backlog |

#### ✅ F31.8: CICD-ONT v1.1.0 — OAA Compliance Fix — [#586](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/586)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-04.8.1 | Fix G2C — Add `filteredBy` and `syncsConventions` relationships | — | ✅ Done |
| DB-ARCH-04.8.2 | Fix G8 — Rename `ownerOrganisation` to `ownedByOrganisation` | — | ✅ Done |
| DB-ARCH-04.8.3 | Update changeControl to v1.1.0, add versionHistory | — | ✅ Done |
| DB-ARCH-04.8.4 | Rename ontology file to v1.1.0, update Entry and registry | — | ✅ Done |

#### ✅ F31.9: Sealed Skill Distribution — Guard-Core & PFC-Release Extension — [#819](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/819)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-04.9.1 | Create `guard-core.yml` CI gate workflow | — | ✅ Done |
| DB-ARCH-04.9.2 | Create `sealed-skills-manifest.json` | — | ✅ Done |
| DB-ARCH-04.9.3 | Create `pfc-core/.claude-plugin/plugin.json` sealed plugin manifest | — | ✅ Done |
| DB-ARCH-04.9.4 | Extend `pfc-release.yml` with sealed skills distribution | — | ✅ Done |
| DB-ARCH-04.9.5 | Extend `bootstrap-triad.sh` with sealed skills + guard-core | — | ✅ Done |

---

### DB-ARCH-05 — Epic 53: Cloud Vendor Sovereignty & Multi-Platform PFI Delivery

**Issue**: [#775](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/775) | **Priority**: P1 | **Features**: 6 | **Stories**: 33 (0 done)
**Depends on**: Epic 59 (#840)
**PBS**: `PBS/STRATEGY/BRIEFING-PFC-EA-Arch-DB-Migrations-Azure-Supabase.md`

#### F53.1: Azure DevOps PFI Bootstrap & Triad Support — [#776](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/776)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-05.1.1 | Create `bootstrap-triad-azure.sh` script | [#782](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/782) | Backlog |
| DB-ARCH-05.1.2 | Azure Boards work item templates (Epic/Feature/Story with EFS) | [#783](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/783) | Backlog |
| DB-ARCH-05.1.3 | Azure Pipeline equivalents (promote, validate, enforce-registry) | [#784](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/784) | Backlog |
| DB-ARCH-05.1.4 | `pfc-release-azure.yml` — Azure Artifacts spoke | [#785](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/785) | Backlog |
| DB-ARCH-05.1.5 | Azure environment approval gates (dev→test→prod) | [#786](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/786) | Backlog |
| DB-ARCH-05.1.6 | PoC — Bootstrap PFI-AIRL on Azure DevOps | [#787](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/787) | Backlog |

#### F53.2: Multi-Provider AI Model Router & Claude Regional — [#777](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/777)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-05.2.1 | Define `aiConfig` schema in PFI config | [#788](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/788) | Backlog |
| DB-ARCH-05.2.2 | Claude via Google Vertex Frankfurt deployment guide | [#789](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/789) | Backlog |
| DB-ARCH-05.2.3 | Claude via AWS Bedrock Frankfurt deployment guide | [#790](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/790) | Backlog |
| DB-ARCH-05.2.4 | Azure OpenAI adapter — function calling translation | [#791](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/791) | Backlog |
| DB-ARCH-05.2.5 | Model router implementation (auto-select based on PFI config) | [#792](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/792) | Backlog |
| DB-ARCH-05.2.6 | Token budget tracking per DELTA cycle | [#793](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/793) | Backlog |

#### F53.3: EFS-ONT Platform Adapter Layer — [#778](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/778)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-05.3.1 | EFS ↔ Azure Boards adapter (`az boards` CLI wrapper) | [#794](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/794) | Backlog |
| DB-ARCH-05.3.2 | EFS ↔ GitHub Issues adapter (formalised) | [#795](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/795) | Backlog |
| DB-ARCH-05.3.3 | EFS ↔ GitLab Issues adapter (`glab` CLI wrapper) | [#796](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/796) | Backlog |
| DB-ARCH-05.3.4 | Adapter interface specification (common CRUD contract) | [#797](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/797) | Backlog |
| DB-ARCH-05.3.5 | Cross-platform hierarchy validation tool | [#798](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/798) | Backlog |

#### F53.4: Data Sovereignty Tier Classification & GRC Alignment — [#779](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/779)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-05.4.1 | Sovereignty tier schema in PFI config (`sovereigntyTier: 0-4`) | [#799](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/799) | Backlog |
| DB-ARCH-05.4.2 | CLOUD Act risk assessment per vendor (GRC-FW-ONT aligned) | [#800](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/800) | Backlog |
| DB-ARCH-05.4.3 | ZDR addendum procurement checklist | [#801](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/801) | Backlog |
| DB-ARCH-05.4.4 | Regulatory reference matrix (UK GDPR, e-evidence, NIS2, DORA) | [#802](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/802) | Backlog |
| DB-ARCH-05.4.5 | Sovereignty audit workflow — validate PFI config against tier | [#803](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/803) | Backlog |

#### F53.5: Sovereign Self-Hosted Stack — Gitea + OSS LLM — [#780](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/780)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-05.5.1 | Gitea deployment playbook (Docker Compose + Actions runner) | [#804](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/804) | Backlog |
| DB-ARCH-05.5.2 | vLLM + Llama 4 / Mistral Large serving guide | [#805](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/805) | Backlog |
| DB-ARCH-05.5.3 | LangChain/LangGraph skill adapter | [#806](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/806) | Backlog |
| DB-ARCH-05.5.4 | Keycloak auth integration (replaces Supabase Auth) | [#807](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/807) | Backlog |
| DB-ARCH-05.5.5 | `bootstrap-triad-sovereign.sh` script | [#808](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/808) | Backlog |
| DB-ARCH-05.5.6 | Quality benchmark — DELTA pipeline on OSS vs Claude Opus | [#809](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/809) | Backlog |

#### F53.6: Enterprise M365 Value-Add Integration — [#781](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/781)

| WBS | Story | Issue | Status |
|-----|-------|-------|--------|
| DB-ARCH-05.6.1 | Power BI BSC dashboard template from VSOM KPI-ONT metrics | [#810](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/810) | Backlog |
| DB-ARCH-05.6.2 | Viva Goals ↔ VSOM-ONT OKR sync specification | [#811](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/811) | Backlog |
| DB-ARCH-05.6.3 | Copilot Studio bot for DELTA output consumption | [#812](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/812) | Backlog |
| DB-ARCH-05.6.4 | SharePoint document library for ontology artefact hosting | [#813](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/813) | Backlog |

---

## Downstream Consumers (Not In This Programme)

These epics/features are **not blocked** by MS 9.00 and continue independently:

| Epic | What Continues | What's Blocked |
|------|---------------|----------------|
| Epic 8 (#80) Design System | F8.1, F8.3-F8.4, F8.6-F8.15 (all done) | F8.2 (Token Storage), F8.5 (Agentic) → rolled to Epic 61 |
| Epic 61 (#876) DS Maturity | F61.1-F61.4 (Token Gap), F61.7-F61.8 (Slides) | F61.5 (ex-F8.2), F61.6 (ex-F8.5) — blocked by F59.1+F59.2 |
| Epic 45 (#634) W4M-WWG | Instance data authoring in git | DB storage of instance data |
| Epic 49 (#747) VSOM App Planner | Skeleton, nav, zone layout | DB-backed state persistence |

---

## Key Decisions Required

| # | Decision | Affects | Options |
|---|----------|---------|---------|
| 1 | 4-role MVP vs 7-role RBAC | Epic 10A | 4-role (simple) vs 7-role (agents+API) — recommend 4-role with extension points |
| 2 | Supabase-only or AIRL on Azure from Phase 1 | Epic 59 F59.1 | 18 Supabase vs 15 Supabase + 3 Azure PostgreSQL |
| 3 | Git promote triggers DB promote | Epic 31 + 59 | Unified pipeline vs separate pipelines with webhook |
| 4 | Login UI framework | Epic 10A F10A.4 | Vanilla JS (current stack) vs Shadcn micro-components (Epic 8 bridge) |
| 5 | `resolve_token()` alignment | Epic 59 + 61 | Same function signature as `resolve_cascaded_config()` vs separate |

---

*Programme Summary — MS 9.00 Platform Database Architecture & Security Foundation*
*Generated: 2026-03-05 | 5 Epics, 32 Features, 137 Stories*
*Milestone: https://github.com/ajrmooreuk/Azlan-EA-AAA/milestone/11*


---

## Unified Registry Database Brief

# VSOM Strategy Briefing: Unified Registry Database — From Ontology Index to Multi-Artifact Platform Registry

## Broadening and Maintaining PF/EMC/PFI/Product/Org Context Across Ontologies, UI/UX, DB, JSON & Code

| Field | Value |
|---|---|
| **Document** | PFC-UNIFIED-REG-DB-STRATEGY-v1.0.0 |
| **Date** | 2026-03-02 |
| **Status** | DRAFT — Ready for strategic review |
| **Companion To** | [Epic 34: PFC-VSOM — PF Strategy (#518)](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518): Unified Registry Skills Architecture |
| **Cross-Refs** | PLAN-Supabase-JSONB-MVP-Platform-Phase.md, ARCH-PFC-PFI-Product-Client-Graph-Cascade.md, BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md, BRIEFING-PFC-Context-Assistant-Universal-PBS-Strategy.md |
| **Supersedes** | None (gap-filling document) |
| **VSOM Pattern** | Vision → 4 Strategies → BSC Objectives → Metrics |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Current State Audit: All Registry JSON Files & Versions](#2-current-state-audit-all-registry-json-files--versions)
3. [Gap Analysis: What's Tracked vs What's Missing](#3-gap-analysis-whats-tracked-vs-whats-missing)
4. [VSOM: Vision](#4-vsom-vision)
5. [VSOM: 4 Strategies](#5-vsom-4-strategies)
6. [VSOM: BSC Objectives](#6-vsom-bsc-objectives)
7. [VSOM: Metrics](#7-vsom-metrics)
8. [Artifact Type Taxonomy: 10 Registry Domains](#8-artifact-type-taxonomy-10-registry-domains)
9. [Cascade Mechanics Per Artifact Type](#9-cascade-mechanics-per-artifact-type)
10. [Registry Schema Evolution: ont-registry-index.json → pfc-registry-index.json](#10-registry-schema-evolution-ont-registry-indexjson--pfc-registry-indexjson)
11. [Supabase `pfc_registry` Table: Multi-Artifact Schema](#11-supabase-pfc_registry-table-multi-artifact-schema)
12. [PFI Instance Context Broadening](#12-pfi-instance-context-broadening)
13. [EMC Composition: Multi-Artifact Scope Rules](#13-emc-composition-multi-artifact-scope-rules)
14. [Versioning & Governance Model](#14-versioning--governance-model)
15. [Migration Roadmap: 4 Phases](#15-migration-roadmap-4-phases)
16. [Cross-Reference Map: Existing Briefings & This Document](#16-cross-reference-map-existing-briefings--this-document)

---

## 1. Executive Summary

The PF-Core platform currently manages its knowledge assets through a set of **separate, ontology-centric JSON registries** that evolved organically. The ontology registry (`ont-registry-index.json` v10.9.1) is the most mature, tracking 54 ontologies across 5 series and 9 PFI instances. However, the platform now governs **six additional artifact types** — skills, design tokens, agents, database schemas, UI/UX components, and code modules — none of which have equivalent registry coverage.

Epic 34 (PFC-VSOM)'s Unified Registry briefing (2026-02-18) established the architectural principle: **one registry, quasi-OO cascade, all artifact types**. That briefing defined the "what" and "why". This document provides the **"how" and "when"** — a VSOM-led strategy for evolving the current file-based ontology index into a multi-artifact platform registry backed by Supabase JSONB, with full cascade resolution across PF-Core → PFI → Product → Client scopes.

**Key deliverable:** A single `pfc-registry-index.json` (successor to `ont-registry-index.json`) that indexes **all** governed artifacts — ontologies, skills, agents, design tokens, database schemas, UI/UX skeletons, code modules, JSON-LD contexts, instance data files, and documentation — with per-PFI cascade inheritance.

---

## 2. Current State Audit: All Registry JSON Files & Versions

### 2.1 Primary Registry Files

| # | File | Version | Entries | Scope | Location |
|---|------|---------|---------|-------|----------|
| 1 | `ont-registry-index.json` | **v10.9.1** | 54 ontologies, 5 series, 9 PFI instances | Ontologies only | `ontology-library/` |
| 2 | `join-pattern-registry.json` | **v1.0.0** | 91 join patterns (71 active, 18 proposed, 2 deprecated) | Cross-ontology bridges | `ontology-library/` |
| 3 | `unified-glossary-v3.0.0.json` | **v3.0.0** | All terms across ontology ecosystem | Terminology | `ontology-library/` |
| 4 | `pfc-release-manifest.json` | **v1.0.0** | 4 component types (ontology-registry, visualiser, agents, design-system) | CI/CD distribution | `ontology-library/` |

### 2.2 Ontology Data Files (Instance-Level)

| # | PFI Instance | Declared Ontologies | Instance Data Files | Graph Scope File |
|---|---|---|---|---|
| 1 | PFI-BAIV | 18 (all series) | 12+ `.jsonld` files | `pfi-baiv-graph-scope.json` |
| 2 | PFI-W4M | 13 | 8+ `.jsonld` files | `pfi-w4m-graph-scope.json` |
| 3 | PFI-W4M-WWG | 8 (VP, RRR, LSC, OFM, KPI, BSC, EMC, KANO) | 6+ `.jsonld` files | `pfi-w4m-wwg-graph-scope.json` |
| 4 | PFI-W4M-EOMS | 6 (OFM, LSC heavy) | 4+ `.jsonld` files | `pfi-w4m-eoms-graph-scope.json` |
| 5 | PFI-AIRL-CAF-AZA | 10+ (GRC-heavy) | 5+ `.jsonld` files | `pfi-airl-caf-aza-graph-scope.json` |
| 6 | PFI-VHF | 7 (PoC) | 3+ `.jsonld` files | `pfi-vhf-graph-scope.json` |
| 7 | PFI-RCS | 0 (template) | None | None |
| 8 | PFI-ANTQ | 1+ (ANTIQUES-ONT) | 2+ `.jsonld` files | None |
| 9 | PFI-PC-DICE | Internal tooling | Minimal | `pfi-pc-dice-graph-scope.json` |

### 2.3 Design System & Skeleton Files

| # | File/Type | Version | Status |
|---|-----------|---------|--------|
| 1 | `pfc-app-skeleton-v1.0.0.jsonld` | v1.0.0 | Live — 22 zones, 5 nav layers |
| 2 | DS-ONT ontology | v3.0.0 → v3.1.0 | Live — zone/component/token entities |
| 3 | PFI design system instances | v1.0.0 per PFI | Partial — BAIV and W4M-WWG have `dsInstanceRef` |
| 4 | `.pen` files (Pencil artefacts) | Emerging | F49.9 strategy defined but no registry tracking |

### 2.4 Skills & Agent Files

| # | Location | Count | Status |
|---|----------|-------|--------|
| 1 | `azlan-github-workflow/skills/` | 32 skill directories | Live — VE pipeline, DELTA, EFS, GitHub workflow skills |
| 2 | `PBS/AGENTS/` | Referenced in release manifest | `enabled: false` — not indexed |
| 3 | PE-ONT v4.0.0 skill entities | 5 new entity types | Defined in ontology but not in registry index |

### 2.5 Supabase Schema Files

| # | File | Status |
|---|------|--------|
| 1 | `supabase/migrations/001_create_schema.sql` | Written, not deployed |
| 2 | `supabase/migrations/002_rls_policies.sql` | Written, not deployed |
| 3 | `supabase/migrations/005_graphql_layer.sql` | Written, not deployed |
| 4 | Migration 003 (API keys) | Spec'd, not written |
| 5 | Migration 004 (`pfc_registry` + `resolve_artifact_config`) | Spec'd, not written |

---

## 3. Gap Analysis: What's Tracked vs What's Missing

### 3.1 Coverage Matrix

| Artifact Type | Registry Indexed? | Cascade-Aware? | Version Tracked? | PFI Scoped? | Supabase Ready? |
|---|---|---|---|---|---|
| **Ontologies** | YES (54 entries) | YES (instanceOntologies) | YES (semver) | YES (9 PFIs) | Schema written |
| **Join Patterns** | YES (91 entries) | No | YES (v1.0.0) | No | No |
| **Glossary Terms** | YES (unified) | No | YES (v3.0.0) | No | No |
| **Skills** | NO | Arch only (Epic 34) | No | No | No |
| **Agent Templates** | NO | Arch only (Epic 34) | No | No | No |
| **Design Tokens** | PARTIAL (dsInstanceRef) | PARTIAL (brand field) | No | PARTIAL | No |
| **App Skeletons** | PARTIAL (BAIV only) | No | YES (v1.0.0) | No | No |
| **UI/UX Components** | NO | No | No | No | No |
| **DB Schemas** | NO | No | No | No | No |
| **Code Modules** | NO | No | No | No | No |
| **Documentation** | NO | No | No | No | No |
| **CI/CD Workflows** | NO | No | No | No | No |

### 3.2 Critical Gaps

1. **Skills exist but aren't indexed** — 32 skill directories in `azlan-github-workflow/skills/` with no registry entries
2. **Agent templates referenced but not tracked** — `pfc-release-manifest.json` lists `PBS/AGENTS/` as source but `enabled: false`
3. **Design tokens are fragmented** — `designSystemConfig.brand` in PFI entries but no token-level registry
4. **App skeleton is BAIV-only** — Only one PFI has `appSkeletonConfig`; others have no skeleton metadata
5. **DB schemas are invisible** — 5 Supabase migrations exist but aren't governed artifacts
6. **Code modules have no lineage** — 34 ES modules in visualiser, 32 skill scripts, no registry tracking
7. **Documentation isn't indexed** — 53+ strategy briefings, no registry-level discovery
8. **No `.pen` artifact tracking** — F49.9 defines Pencil as agentic design engine but no registry integration

---

## 4. VSOM: Vision

> **Every governed artifact in PF-Core — ontologies, skills, agents, design tokens, database schemas, UI/UX skeletons, code modules, and documentation — is a first-class entry in a single Unified Registry Database, inheriting through a quasi-OO cascade from Core → Instance → Product → Client, queryable by any human, agent, or API within their authorised PFI scope.**

### Vision Statement Decomposition

| Dimension | Commitment |
|---|---|
| **Scope** | ALL artifact types, not just ontologies |
| **Governance** | Single registry, single cascade, single CI/CD pipeline |
| **Access** | Humans (browser), agents (MCP), APIs (REST) — all PFI-scoped |
| **Resolution** | `resolve_artifact_config()` returns the effective merged config for any artifact at any scope |
| **Source of Truth** | JSON files in Git (authoring) → Supabase JSONB (runtime queries) |
| **Versioning** | Semantic versioning per artifact, per scope level |

---

## 5. VSOM: 4 Strategies

### S1: Broaden the Registry Index — From `ont-` to `pfc-`

**Goal:** Evolve `ont-registry-index.json` (ontologies only) into `pfc-registry-index.json` (all 10 artifact domains).

**Approach:**
- Add new top-level sections: `skillEntries`, `agentEntries`, `designTokenEntries`, `skeletonEntries`, `schemaEntries`, `codeModuleEntries`, `documentEntries`
- Retain `entries` array for ontologies (backward compatible)
- Each new section follows the same entry schema: `@id`, `name`, `version`, `scope`, `status`, `path`, `pfiOverrides`
- Bump version from `ont-registry-index.json` v10.9.1 → `pfc-registry-index.json` v1.0.0

**Artifact type taxonomy:** See [Section 8](#8-artifact-type-taxonomy-10-registry-domains).

### S2: Deepen PFI Instance Context — Per-Artifact Cascade

**Goal:** Every PFI entry carries not just `instanceOntologies` but `instanceSkills`, `instanceTokens`, `instanceSchemas`, `instanceCodeModules` — all following the same ON/OFF/Customised pattern.

**Approach:**
- Extend PFI instance entries with per-artifact-type declarations
- Each declaration mirrors `instanceOntologies`: an array of artifact names that the PFI subscribes to
- Override mechanism: PFI-level `overrides/` directory per artifact type
- EMC composition rules extended to scope non-ontology artifacts

**PFI instance broadening:** See [Section 12](#12-pfi-instance-context-broadening).

### S3: Operationalise the Supabase Registry — `pfc_registry` Table

**Goal:** Deploy the `pfc_registry` table (spec'd in Epic 34 briefing) with multi-artifact support and `resolve_artifact_config()` cascade resolution.

**Approach:**
- Write migration 004: `pfc_registry` table + `resolve_artifact_config()` function
- Seed from `pfc-registry-index.json` (Git → DB sync)
- RLS policies scoped by `pfi_id` (same as `ontologies` table)
- Extend `resolve_cascaded_config()` to cover `brand_tokens`, `app_skeletons`, `skill_configs`

**Supabase schema:** See [Section 11](#11-supabase-pfc_registry-table-multi-artifact-schema).

### S4: Govern the Lifecycle — Versioning, Validation & Distribution

**Goal:** One CI/CD pipeline validates all artifact types. One promotion path distributes them. One audit trail tracks all changes.

**Approach:**
- `registry-ci.yml` validates JSON-LD compliance, OAA conformance, dependency graph integrity for ALL artifacts
- `pfc-release.yml` reads `hubSpokeConfig` per PFI and distributes subscribed artifacts (not just ontologies)
- Audit log in Supabase captures every mutation with actor, action, artifact_id, scope, detail JSONB
- Three-tier testing: Local (free) → Haiku (validation) → Sonnet (production)

---

## 6. VSOM: BSC Objectives

### Financial Perspective

| ID | Objective | Target |
|---|---|---|
| OBJ-F1 | Reduce artifact discovery time for PFI teams | -80% vs current file-system browsing |
| OBJ-F2 | Eliminate duplicate artifact definitions across PFIs | Zero duplication — inherit from Core |
| OBJ-F3 | Enable self-service PFI onboarding via registry subscription | <1 day to activate a new PFI with full artifact set |

### Customer Perspective

| ID | Objective | Target |
|---|---|---|
| OBJ-C1 | Every PFI team can discover all available artifacts within their scope | 100% artifact visibility via registry browser |
| OBJ-C2 | PFI customisations are governed, not ad-hoc | All overrides tracked in registry with audit trail |
| OBJ-C3 | Agent/MCP access is PFI-scoped and auditable | Every agent query returns only scoped results |

### Internal Process Perspective

| ID | Objective | Target |
|---|---|---|
| OBJ-P1 | Single source of truth for all artifact metadata | One registry index file, one Supabase table |
| OBJ-P2 | Cascade resolution works for all artifact types | `resolve_artifact_config()` handles 10 domains |
| OBJ-P3 | CI/CD validates all artifact types, not just ontologies | Single `registry-ci.yml` pipeline |

### Learning & Growth Perspective

| ID | Objective | Target |
|---|---|---|
| OBJ-L1 | Registry architecture documented for new team members | Reading path in catalogue, worked examples per artifact type |
| OBJ-L2 | PFI teams understand cascade inheritance model | Training guide with worked examples for skills, tokens, schemas |
| OBJ-L3 | Agent developers can register new artifact types | Extension pattern documented with template and validation rules |

---

## 7. VSOM: Metrics

| Objective | KPI | Current | Target | Measurement |
|---|---|---|---|---|
| OBJ-F1 | Mean time to find an artifact | ~15 min (file browsing) | <30 sec (registry query) | User timing study |
| OBJ-F2 | Duplicate artifact ratio | Unknown (high for tokens) | 0% | Registry dedup audit |
| OBJ-F3 | PFI onboarding time | ~5 days manual setup | <1 day automated | Calendar tracking |
| OBJ-C1 | Artifact visibility coverage | 20% (ontologies only) | 100% (all 10 domains) | Coverage matrix audit |
| OBJ-C2 | Untracked customisations | High (ad-hoc overrides) | 0 (all in registry) | Override audit scan |
| OBJ-P1 | Registry file count | 4 (separate files) | 1 (unified index) | File count |
| OBJ-P2 | Artifact types with cascade | 1 (ontologies) | 10 (all domains) | Feature checklist |
| OBJ-P3 | CI/CD artifact type coverage | 1 (ontologies) | 10 (all domains) | Pipeline config audit |

---

## 8. Artifact Type Taxonomy: 10 Registry Domains

### Domain Classification

| # | Domain | `artifactType` | Example Entries | Current Count | Registry Section |
|---|--------|-----------------|-----------------|---------------|------------------|
| 1 | **Ontologies** | `ontology` | VP-ONT v3.0.0, EMC-ONT v5.0.0 | 54 | `entries` (existing) |
| 2 | **Join Patterns** | `join-pattern` | JP-PE-SK-001, JP-VP-RRR-001 | 91 | `joinPatternEntries` |
| 3 | **Skills** | `skill` | pfc-vsom, pfc-efs, pfc-delta-pipeline | 32 | `skillEntries` |
| 4 | **Agent Templates** | `agent` | DELTA orchestrator, composition agent | ~5 planned | `agentEntries` |
| 5 | **Design Tokens** | `design-token` | color-primary, font-heading, spacing-base | ~50+ per brand | `designTokenEntries` |
| 6 | **App Skeletons** | `skeleton` | pfc-app-skeleton v1.0.0, PFI zone overrides | 1 + PFI variants | `skeletonEntries` |
| 7 | **DB Schemas** | `db-schema` | 001_create_schema, 005_graphql_layer | 5 migrations | `dbSchemaEntries` |
| 8 | **Code Modules** | `code-module` | emc-composer.js, registry-browser.js | 34 ES modules | `codeModuleEntries` |
| 9 | **JSON-LD Contexts** | `schema` | OAA v7.0.0, PFC namespace registry | ~5 | `schemaEntries` |
| 10 | **Documentation** | `document` | Strategy briefings, architecture docs, training guides | 53+ | `documentEntries` |

### Entry Schema (Shared Across All Domains)

```json
{
  "@id": "Entry-[TYPE]-[CODE]-NNN",
  "name": "Human-readable name",
  "artifactType": "ontology|skill|agent|design-token|skeleton|db-schema|code-module|schema|document|join-pattern",
  "namespace": "prefix:",
  "version": "semver",
  "status": "compliant|proposal|deprecated|superseded|placeholder",
  "scope": "core",
  "path": "./relative/path/to/artifact",
  "series": "VE-Series|PE-Series|RCSG-Series|Foundation|Orchestration|Platform",
  "dependencies": ["Entry-*-NNN"],
  "pfiOverrides": {
    "BAIV": { "status": "active", "overrideType": "extend", "overridePath": "./instances/baiv/overrides/..." },
    "W4M-WWG": { "status": "active", "overrideType": "inherit" }
  },
  "qualityGates": { "testCoverage": ">=85%", "schemaCompliance": true },
  "lastUpdated": "YYYY-MM-DD"
}
```

---

## 9. Cascade Mechanics Per Artifact Type

### 9.1 How Each Domain Inherits

| Domain | Core Definition | Instance Override | Product Override | Client Override |
|---|---|---|---|---|
| **Ontologies** | Full JSON-LD entity/relationship graph | `instanceOntologies` filter + instance data `.jsonld` | Product-specific entities/relationships | Client branding of entity labels |
| **Skills** | `SKILL.md` + assets + scripts | `INSTANCE.md` + config overrides (e.g., BSC perspectives) | Product-specific skill parameters | Client workflow customisations |
| **Agents** | Agent template + capability declarations | Instance-specific agent configurations | Product agent roles | Client agent access rules |
| **Design Tokens** | PFC brand tokens (colours, typography, spacing) | PFI brand override (e.g., W4M #2D5F8A) | Product-specific token extensions | Client brand tokens |
| **Skeletons** | 22-zone base skeleton + 5 nav layers | PFI zone additions/removals + nav item overrides | Product zone allocation | Client zone branding |
| **DB Schemas** | Core table definitions + RLS policies | PFI-specific columns/indexes | Product schema extensions | Client data partitioning |
| **Code Modules** | Core ES modules (34 in visualiser) | PFI-specific module overrides | Product feature modules | Client plugin modules |
| **Schemas** | OAA v7.0.0 + PFC namespace registry | Instance namespace extensions | Product schema profiles | Client validation rules |
| **Documentation** | Strategy briefings, architecture docs | PFI operating guides, instance briefings | Product user guides | Client onboarding docs |
| **Join Patterns** | 91 cross-ontology bridges | Instance-specific pattern activation | Product pattern extensions | Client pattern customisation |

### 9.2 Cascade Resolution Example: Design Token

```
Core:     color-primary = #0099B1  (PFC brand default)
  │
  ├─ PFI-BAIV:    inherit → #0099B1
  ├─ PFI-W4M:     override → #2D5F8A  (W4M brand blue)
  │   ├─ Product W4M-WWG:  inherit → #2D5F8A
  │   └─ Product W4M-EOMS: override → #1A3D5C  (darker variant)
  ├─ PFI-AIRL:    inherit → #0099B1
  └─ PFI-VHF:     override → #4CAF50  (health green)
      └─ Client NHS:  override → #005EB8  (NHS Blue)
```

### 9.3 Cascade Resolution Example: Skill

```
Core:     pfc-vsom v1.0.0  { include_bsc: false, maps: true }
  │
  ├─ PFI-BAIV:    extend → { include_bsc: true, +baiv_perspectives }
  ├─ PFI-W4M:     extend → { include_bsc: true, +wellbeing_perspectives }
  │   ├─ Product W4M-WWG:  extend → { +supply_chain_bsc_metrics }
  │   └─ Product W4M-EOMS: override → { include_bsc: false }  (not needed)
  ├─ PFI-AIRL:    inherit → { include_bsc: false, maps: true }
  └─ PFI-VHF:     OFF  (VSOM not applicable for PoC)
```

### 9.4 Cascade Resolution Example: DB Schema

```
Core:     001_create_schema.sql  (5-table base: ontologies, pfi_instances, composed_graphs, audit_log, user_pfi_access)
  │
  ├─ PFI-BAIV:    extend → 001_baiv_extensions.sql  (+ai_visibility_scores table, +brand_mentions index)
  ├─ PFI-W4M:     extend → 001_w4m_extensions.sql   (+supply_corridors table, +fulfilment_metrics index)
  ├─ PFI-AIRL:    extend → 001_airl_extensions.sql  (+compliance_audit table, +grc_scores index)
  └─ PFI-VHF:     inherit (base schema only)
```

---

## 10. Registry Schema Evolution: `ont-registry-index.json` → `pfc-registry-index.json`

### 10.1 Migration Strategy

The evolution is **additive, not breaking**:

```
ont-registry-index.json v10.9.1 (current)
  │
  │  Rename + extend
  ▼
pfc-registry-index.json v1.0.0 (target)
  ├─ entries[]              ← UNCHANGED (ontology entries, backward compatible)
  ├─ namespaceRegistry{}    ← UNCHANGED
  ├─ seriesRegistry{}       ← EXTENDED (new series: Platform, Skills, Design)
  ├─ statistics{}           ← EXTENDED (per artifact type)
  ├─ validationSummary{}    ← EXTENDED (per artifact type)
  ├─ pfiInstances[]         ← EXTENDED (per-artifact-type subscriptions)
  │
  │  NEW SECTIONS:
  ├─ skillEntries[]         ← 32 skill entries from azlan-github-workflow/skills/
  ├─ agentEntries[]         ← Agent templates from PBS/AGENTS/
  ├─ designTokenEntries[]   ← Token sets extracted from PFI designSystemConfig
  ├─ skeletonEntries[]      ← App skeleton + PFI zone overrides
  ├─ dbSchemaEntries[]      ← Supabase migration files as governed artifacts
  ├─ codeModuleEntries[]    ← ES modules from visualiser + key platform code
  ├─ schemaEntries[]        ← OAA, namespace registry, JSON-LD contexts
  ├─ documentEntries[]      ← Strategy briefings, architecture docs, training guides
  └─ joinPatternEntries[]   ← Absorbed from join-pattern-registry.json
```

### 10.2 PFI Instance Entry Extension

Current PFI instance entry (ontology-only):
```json
{
  "@id": "PFI-W4M-WWG",
  "instanceOntologies": ["VP-ONT", "RRR-ONT", "LSC-ONT", "OFM-ONT", "KPI-ONT", "BSC-ONT", "EMC-ONT", "KANO-ONT"],
  "requirementScopes": ["PRODUCT", "FULFILMENT", "COMPETITIVE"],
  "designSystemConfig": { "brand": null, "fallback": "pfc" }
}
```

Extended PFI instance entry (all artifact types):
```json
{
  "@id": "PFI-W4M-WWG",
  "instanceOntologies": ["VP-ONT", "RRR-ONT", "LSC-ONT", "OFM-ONT", "KPI-ONT", "BSC-ONT", "EMC-ONT", "KANO-ONT"],
  "instanceSkills": ["pfc-vsom", "pfc-vp", "pfc-okr", "pfc-kpi", "pfc-efs", "pfc-delta-pipeline"],
  "instanceAgents": ["delta-orchestrator", "composition-agent"],
  "instanceTokens": ["wwg-brand-tokens-v1.0.0"],
  "instanceSkeletons": ["pfc-app-skeleton-v1.0.0"],
  "instanceSchemas": ["oaa-v7.0.0", "pfc-namespace-registry"],
  "instanceCodeModules": ["emc-composer", "registry-browser", "multi-loader"],
  "instanceDocuments": ["OPERATING-GUIDE-Visualiser", "VSOM-LSC-OFM-Intelligence"],
  "requirementScopes": ["PRODUCT", "FULFILMENT", "COMPETITIVE"],
  "designSystemConfig": {
    "brand": "wwg",
    "fallback": "pfc",
    "configVersion": "1.0.0",
    "dsInstanceRef": "./PE-Series/DS-ONT/instances/wwg-ds-instance-v1.0.0.jsonld"
  },
  "appSkeletonConfig": {
    "baseApp": "pfc-visualiser",
    "pfiOverrides": "./instances/w4m-wwg/overrides/skeleton/"
  }
}
```

### 10.3 Backward Compatibility

| Consumer | Impact | Migration Path |
|---|---|---|
| `multi-loader.js` | Reads `entries[]` only | No change — `entries[]` unchanged |
| `pfi-loader.js` | Reads `pfiInstances[]` | No change — existing fields unchanged, new fields additive |
| `emc-composer.js` | Reads `instanceOntologies` | No change — field unchanged |
| `registry-browser.js` | Reads entire index | Extended to show new artifact types alongside ontologies |
| `github-loader.js` | Fetches `ont-registry-index.json` | Updated to fetch `pfc-registry-index.json` with fallback to `ont-registry-index.json` |
| `pfc-release.yml` | Reads `hubSpokeConfig.ontologySeries` | Extended to read per-artifact-type subscriptions |

---

## 11. Supabase `pfc_registry` Table: Multi-Artifact Schema

### 11.1 Table Design (Migration 004)

The `pfc_registry` table from Epic 34 (PFC-VSOM) briefing is confirmed as the target, with clarifications for multi-artifact support:

```sql
CREATE TABLE pfc_registry (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

  -- Identity
  artifact_id TEXT NOT NULL,             -- "pfc:skill-vsom-v1.0.0" or "Entry-ONT-VP-001"
  artifact_type TEXT NOT NULL,           -- Enum: ontology, skill, agent, design-token, skeleton,
                                         --       db-schema, code-module, schema, document, join-pattern
  artifact_category TEXT,                -- Series or grouping: "VE-Series", "Platform", "RCSG-Series"
  name TEXT NOT NULL,
  version TEXT NOT NULL,                 -- Semantic version

  -- Cascade Position
  scope TEXT NOT NULL,                   -- core, instance, product, client
  instance_id TEXT,                      -- NULL for core; PFI code for instance
  product_id TEXT,                       -- NULL unless product-scoped
  client_id UUID REFERENCES tenants(id), -- NULL unless client-scoped

  -- State
  status TEXT DEFAULT 'active',          -- active, inactive, deprecated, superseded, proposal
  override_type TEXT DEFAULT 'inherit',  -- inherit, extend, override

  -- Configuration (JSONB)
  configuration JSONB DEFAULT '{}',      -- Effective merged config at this scope
  base_configuration JSONB DEFAULT '{}', -- Raw config before cascade merge
  components JSONB DEFAULT '[]',         -- File paths / component references
  dependencies JSONB DEFAULT '[]',       -- Dependency artifact IDs
  quality_gates JSONB DEFAULT '{}',      -- Validation thresholds

  -- Metadata
  artifact_hash TEXT,                    -- SHA-256 of artifact content
  source_path TEXT,                      -- Git path to source file
  environment TEXT NOT NULL DEFAULT 'production',
  deployed_at TIMESTAMPTZ DEFAULT now(),
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),

  UNIQUE(artifact_id, scope, instance_id, product_id, client_id, environment)
);

-- Indexes for cascade resolution
CREATE INDEX idx_registry_type_name ON pfc_registry(artifact_type, name);
CREATE INDEX idx_registry_scope ON pfc_registry(scope, instance_id);
CREATE INDEX idx_registry_status ON pfc_registry(status) WHERE status = 'active';
CREATE INDEX idx_registry_config ON pfc_registry USING gin(configuration);
```

### 11.2 Multi-Artifact `resolve_artifact_config()`

Extended to handle product scope (Tier 2) in addition to core/instance/client:

```sql
CREATE OR REPLACE FUNCTION resolve_artifact_config(
  p_artifact_type TEXT,
  p_name TEXT,
  p_instance_id TEXT DEFAULT NULL,
  p_product_id TEXT DEFAULT NULL,
  p_client_id UUID DEFAULT NULL
)
RETURNS JSONB AS $$
DECLARE
  core_config JSONB;
  instance_config JSONB;
  product_config JSONB;
  client_config JSONB;
  result JSONB;
BEGIN
  -- Tier 0: Core
  SELECT configuration INTO core_config FROM pfc_registry
    WHERE artifact_type = p_artifact_type AND name = p_name
    AND scope = 'core' AND status = 'active';
  result := COALESCE(core_config, '{}'::jsonb);

  -- Tier 1: Instance
  IF p_instance_id IS NOT NULL THEN
    SELECT base_configuration INTO instance_config FROM pfc_registry
      WHERE artifact_type = p_artifact_type AND name = p_name
      AND scope = 'instance' AND instance_id = p_instance_id AND status = 'active';
    IF instance_config IS NOT NULL THEN
      result := result || instance_config;
    END IF;
  END IF;

  -- Tier 2: Product
  IF p_product_id IS NOT NULL THEN
    SELECT base_configuration INTO product_config FROM pfc_registry
      WHERE artifact_type = p_artifact_type AND name = p_name
      AND scope = 'product' AND product_id = p_product_id AND status = 'active';
    IF product_config IS NOT NULL THEN
      result := result || product_config;
    END IF;
  END IF;

  -- Tier 3: Client
  IF p_client_id IS NOT NULL THEN
    SELECT base_configuration INTO client_config FROM pfc_registry
      WHERE artifact_type = p_artifact_type AND name = p_name
      AND scope = 'client' AND client_id = p_client_id AND status = 'active';
    IF client_config IS NOT NULL THEN
      result := result || client_config;
    END IF;
  END IF;

  RETURN result;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### 11.3 Seeding Process (Git → Supabase)

```
pfc-registry-index.json (Git, authoritative)
  │
  │ registry-seed.js (new script)
  │   1. Parse pfc-registry-index.json
  │   2. For each entry in every section (ontologies, skills, agents, tokens, ...)
  │   3. Upsert into pfc_registry with scope='core'
  │   4. For each pfiInstance, upsert instance-level overrides
  │   5. Verify hash integrity
  ▼
pfc_registry table (Supabase, runtime queries)
  │
  │ resolve_artifact_config() (PostgreSQL function)
  │   Cascade resolution at query time
  ▼
API / MCP / Browser consumers
```

---

## 12. PFI Instance Context Broadening

### 12.1 Current vs Target PFI Context

| Context Dimension | Current Coverage | Target Coverage |
|---|---|---|
| **Ontologies** | `instanceOntologies[]` — full support | Unchanged |
| **Instance Data** | `instanceDataFiles[]` — full support | Unchanged |
| **Design System** | `designSystemConfig{}` — brand + fallback | Extended: `instanceTokens[]`, cascade-aware `brand_config` JSONB |
| **App Skeleton** | `appSkeletonConfig{}` — BAIV only | Extended: every PFI gets skeleton config with zone overrides |
| **Skills** | Not tracked | NEW: `instanceSkills[]` — subscribed skill artifacts |
| **Agents** | Not tracked | NEW: `instanceAgents[]` — subscribed agent templates |
| **DB Schemas** | Not tracked | NEW: `instanceSchemas[]` — schema extensions per PFI |
| **Code Modules** | Not tracked | NEW: `instanceCodeModules[]` — module overrides per PFI |
| **Documentation** | Not tracked | NEW: `instanceDocuments[]` — PFI-scoped docs |
| **Graph Scope** | `pfi-*-graph-scope.json` — separate files | Integrated: `graphScopeConfig{}` in PFI instance entry |
| **EMC Config** | `emcInstanceConfigRef` — path reference | Integrated: `emcConfig{}` inline or retained as ref |
| **Hub-Spoke** | `hubSpokeConfig{}` — full support | Extended: per-artifact-type repo paths |
| **Org Context** | `orgContext{}` — basic fields | Extended: ORG-CONTEXT-ONT integration, maturity dimensions |
| **Jurisdictions** | `jurisdictions[]` — country codes | Unchanged |

### 12.2 Org-Specific Context Pattern

Each PFI instance carries organisational context that governs cascade behaviour:

```json
{
  "@id": "PFI-W4M-WWG",
  "orgContext": {
    "organizationRef": "org:w4m-wwg",
    "organizationName": "W4M Worldwide Gourmet",
    "industry": "Food Import / Specialist Retail",
    "industryOntologyRef": "IND-ONT",
    "size": "SME",
    "maturityLevel": 2,
    "verticalMarket": "UK Specialist Food Import",
    "jurisdictions": ["UK", "EU", "AU", "NZ", "IS", "IE"],
    "complianceFrameworks": ["GDPR", "UK-Food-Standards"],
    "grcProfile": "standard"
  },
  "contextLevel": "PFI",
  "productContexts": [
    {
      "@id": "PROD-WWG-PORTAL",
      "name": "W4M-WWG Customer Portal",
      "contextLevel": "Product",
      "additionalOntologies": ["LSC-ONT", "OFM-ONT"],
      "additionalSkills": ["pfc-lsc-corridor-mapper"],
      "zoneOverrides": { "Z15": { "name": "Supply Chain Dashboard" } }
    }
  ]
}
```

---

## 13. EMC Composition: Multi-Artifact Scope Rules

### 13.1 Current EMC Composition (Ontology-Only)

EMC-ONT v5.0.0 currently governs ontology composition through:
- 11 `CATEGORY_COMPOSITIONS` (PRODUCT, COMPETITIVE, STRATEGIC, etc.)
- `constrainToInstanceOntologies()` — filters by PFI's declared ontology set
- `GraphScopeRule` + `ScopeCondition` + `ScopeAction` — declarative filtering

### 13.2 Target: Multi-Artifact Composition

Extend EMC composition to scope **all** artifact types:

| EMC Concept | Current Scope | Extended Scope |
|---|---|---|
| `ContextLevel` | Ontologies | All 10 artifact domains |
| `RequirementCategory` | Ontology composition categories | Skill bundles, token sets, schema profiles |
| `instanceOntologies` filter | Ontology names | All `instance*` arrays per artifact type |
| `GraphScopeRule` | Entity/relationship filtering | Artifact-level activation rules (e.g., "if GRC-heavy PFI, activate compliance skills") |
| `ComposedGraphSpec` | Multi-ontology graph | Multi-artifact bundle (ontologies + skills + tokens + skeleton config) |

### 13.3 Worked Example: W4M-WWG Multi-Artifact Composition

```
EMC Category: FULFILMENT
  │
  ├─ Ontologies: LSC-ONT, OFM-ONT, PE-ONT (via DEPENDENCY_MAP)
  ├─ Skills:     pfc-lsc-corridor-mapper, pfc-ofm-order-tracker
  ├─ Tokens:     wwg-brand-tokens (supply chain dashboard colours)
  ├─ Skeleton:   Z15 = Supply Chain Dashboard zone
  ├─ DB Schema:  supply_corridors table extension
  └─ Documents:  VSOM-LSC-OFM-Intelligence.md, OPERATING-GUIDE-Visualiser.md
```

This composed bundle is what a W4M-WWG user/agent receives when they activate the FULFILMENT category — not just ontology data, but the full artifact set needed to operate within that scope.

---

## 14. Versioning & Governance Model

### 14.1 Version Scheme

| Level | Format | Example | Trigger |
|---|---|---|---|
| **Registry Index** | `pfc-registry-index.json` vX.Y.Z | v1.0.0, v1.1.0 | Any entry addition/removal/status change |
| **Artifact Entry** | Per-entry semver | VP-ONT v3.0.0, pfc-vsom v1.0.0 | Artifact content change |
| **PFI Override** | Inherited from artifact + PFI suffix | VP-ONT v3.0.0-BAIV | PFI customisation change |
| **OAA Compliance** | OAA vX.Y.Z | OAA v7.0.0 | OAA schema evolution |

### 14.2 Governance Rules

| Rule | Enforcement |
|---|---|
| Every artifact MUST have a registry entry before CI/CD distribution | `registry-ci.yml` validation gate |
| Core artifacts require `@ajrmooreuk` approval for changes | `CODEOWNERS` + `guard-core.yml` |
| PFI overrides MUST reference a valid core artifact | Registry integrity check in CI |
| Deprecated artifacts MUST have a `deprecatedBy` reference and grace deadline | CI validation rule |
| Version bumps MUST follow semver (breaking = major, additive = minor, fix = patch) | PR review checklist |
| All artifact mutations are audit-logged in Supabase | Trigger-based `audit_log` insertion |

### 14.3 Quality Gates Per Artifact Type

| Artifact Type | Validation Gates |
|---|---|
| Ontology | OAA v7.0.0 compliance, JSON-LD syntax, entity/relationship cardinality, cross-ref resolution |
| Skill | SKILL.md structure, asset existence, dependency resolution, three-tier cost annotation |
| Agent | Capability declarations, skill references valid, provider type declared |
| Design Token | Token naming convention, value type validation, brand inheritance chain valid |
| Skeleton | Zone ID uniqueness, nav layer ordering, component references valid |
| DB Schema | SQL syntax, RLS policy present, migration ordering, rollback script present |
| Code Module | ESLint pass, export declarations, dependency imports resolvable |
| Schema | JSON-LD `@context` valid, namespace URIs resolvable |
| Document | Frontmatter present, cross-refs valid, catalogue entry exists |
| Join Pattern | Source/target ontology refs valid, bridge entity refs valid, cardinality declared |

---

## 15. Migration Roadmap: 4 Phases

### Phase 1: Index Broadening (Weeks 1-2)

| Step | Deliverable | Dependencies |
|---|---|---|
| 1.1 | Create `pfc-registry-index.json` v1.0.0 from `ont-registry-index.json` v10.9.1 | None |
| 1.2 | Add `skillEntries[]` — index all 32 skills from `azlan-github-workflow/skills/` | Skill directory audit |
| 1.3 | Add `joinPatternEntries[]` — absorb `join-pattern-registry.json` content | None |
| 1.4 | Add `documentEntries[]` — index all 53+ strategy briefings from catalogue | CATALOGUE file |
| 1.5 | Extend `pfiInstances[]` with `instanceSkills[]` per PFI | Skill-to-PFI mapping |
| 1.6 | Update `github-loader.js` to fetch `pfc-registry-index.json` with fallback | Visualiser code change |
| 1.7 | Update `registry-browser.js` to display new artifact types | Visualiser code change |

### Phase 2: Design & Schema Artifacts (Weeks 3-4)

| Step | Deliverable | Dependencies |
|---|---|---|
| 2.1 | Add `designTokenEntries[]` — extract from PFI `designSystemConfig` refs | Token file audit |
| 2.2 | Add `skeletonEntries[]` — index app skeleton + PFI zone overrides | Skeleton file audit |
| 2.3 | Add `dbSchemaEntries[]` — index Supabase migration files | Migration file audit |
| 2.4 | Add `schemaEntries[]` — index OAA, namespace registry, JSON-LD contexts | Schema file audit |
| 2.5 | Extend PFI instances with `instanceTokens[]`, `instanceSkeletons[]` | Phase 2.1-2.2 |
| 2.6 | Write `registry-ci.yml` to validate all artifact types | Validation rule definitions |

### Phase 3: Supabase Operationalisation (Weeks 5-8)

| Step | Deliverable | Dependencies |
|---|---|---|
| 3.1 | Write migration 004: `pfc_registry` table | Table design finalised |
| 3.2 | Write `resolve_artifact_config()` with 4-tier cascade | Migration 004 |
| 3.3 | Write `registry-seed.js` — Git → Supabase sync script | Migration 004, pfc-registry-index.json |
| 3.4 | RLS policies on `pfc_registry` scoped by `pfi_id` | Migration 002 pattern |
| 3.5 | Extend MCP server with `pfc_registry_query` tool | PROPOSAL-Supabase-Secure-Connections |
| 3.6 | Extend `resolve_cascaded_config()` to cover `brand_tokens` | F49.9 S49.9.2 |

### Phase 4: Full Lifecycle Governance (Weeks 9-12)

| Step | Deliverable | Dependencies |
|---|---|---|
| 4.1 | Add `codeModuleEntries[]` — index visualiser ES modules | Module audit |
| 4.2 | Add `agentEntries[]` — index agent templates | Agent template creation |
| 4.3 | `registry-ci.yml` validates ALL 10 artifact domains | Validation rules per type |
| 4.4 | `pfc-release.yml` distributes per-artifact-type subscriptions to PFI triads | Extended `hubSpokeConfig` |
| 4.5 | Audit log captures all registry mutations with full detail JSONB | Supabase trigger |
| 4.6 | Training guide: "Unified Registry for PFI Teams" | Worked examples per artifact |
| 4.7 | Deprecate `ont-registry-index.json` — redirect to `pfc-registry-index.json` | All consumers migrated |

---

## 16. Cross-Reference Map: Existing Briefings & This Document

| Topic | This Document Section | Primary Source | Secondary Sources |
|---|---|---|---|
| Registry architecture (quasi-OO cascade) | S5 S1, S9 | [BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md](BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md) | [ARCH-PFC-PFI-Product-Client-Graph-Cascade.md](ARCH-PFC-PFI-Product-Client-Graph-Cascade.md) |
| Supabase JSONB schema | S11 | [PLAN-Supabase-JSONB-MVP-Platform-Phase.md](PLAN-Supabase-JSONB-MVP-Platform-Phase.md) | [BRIEFING-GraphQL-Supabase-JSONB-MVP.md](BRIEFING-GraphQL-Supabase-JSONB-MVP.md) |
| Access control (RLS/RBAC/MCP) | S11.2, S11.3 | [PROPOSAL-Supabase-Secure-Connections-API-MCP-v1.0.0.md](PROPOSAL-Supabase-Secure-Connections-API-MCP-v1.0.0.md) | PLAN-Supabase Phase 3 |
| Skills as artifacts | S8 #3, S9.3 | [BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md](BRIEFING-Epic34-Unified-Registry-Skills-Architecture.md) | [ARCH-PE-SKILL-CATALOGUE.md](../../TOOLS/ontology-visualiser/ARCH-PE-SKILL-CATALOGUE.md) |
| Design tokens & UI/UX | S8 #5-6, S12 | [BRIEFING-F49.9-Figma-Pencil-Design-Tooling-Strategy.md](BRIEFING-F49.9-Figma-Pencil-Design-Tooling-Strategy.md) | DS-ONT v3.1.0 |
| EMC composition | S13 | [BRIEFING-Epic19-Phase3-4-and-Beyond.md](BRIEFING-Epic19-Phase3-4-and-Beyond.md) | `emc-composer.js` |
| PFI instance context | S12 | [BRIEFING-Epic34-PFI-Instance-Readiness.md](BRIEFING-Epic34-PFI-Instance-Readiness.md) | PFI operating guides |
| CI/CD distribution | S14, S15 | [TRAINING-PFI-Release-and-Promotion-Guide.md](TRAINING-PFI-Release-and-Promotion-Guide.md) | [VSOM-Programme-Distribution-Strategy.md](VSOM-Programme-Distribution-Strategy.md) |
| Context assistant | S4 Vision | [BRIEFING-PFC-Context-Assistant-Universal-PBS-Strategy.md](BRIEFING-PFC-Context-Assistant-Universal-PBS-Strategy.md) | — |
| FairSlice convergence | — | [CONVERGENCE-FairSlice-JSONB-Graph-Patterns.md](CONVERGENCE-FairSlice-JSONB-Graph-Patterns.md) | — |
| Ontology registry (current) | S2.1 | `ont-registry-index.json` v10.9.1 | `join-pattern-registry.json` v1.0.0 |
| Catalogue of all briefings | S16 | [CATALOGUE-Strategy-Briefings-Architecture.md](CATALOGUE-Strategy-Briefings-Architecture.md) | — |

---

## Appendix A: Complete Inventory of Existing Registry-Related Epics & Features

| Epic/Feature | GitHub # | Title | Registry Relevance |
|---|---|---|---|
| [Epic 34: PFC-VSOM](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518) | #518 | PF Strategy | Defines unified registry architecture |
| F34.8 | — | Extensibility Decision Tree | Plugin/skill extensibility patterns |
| F34.11 | — | Unified Registry Skills Artifact Architecture | Skills as registry artifacts |
| Epic 39 | #569 | PFI Strategy Cascade | Instance activation & artifact distribution |
| Epic 40 | #577 | Graphing Workbench Evolution | Registry browser (F40.3) |
| F40.3 | — | Registry Browser View Mode | Full-screen registry navigation UI |
| F40.13 | — | Skeleton Loader Module | Skeleton as registry artifact |
| F40.17 | — | PFI Lifecycle Workbench | PFI instance management UI |
| Epic 46 | — | PFC-ONT Unified Registry (planned) | Registry as ecosystem ontology |
| Epic 47 | — | PFC Context Assistant (planned) | RBAC-aware registry navigation |
| Epic 48 | — | VE Process Chain Skills | Skill pipeline (VSOM → OKR → KPI → VP) |
| Epic 49 | #747 | VSOM Skilled Application Planner | Strategy-to-delivery skill chain |
| F49.9 | #766 | Figma vs Pencil Design Tooling Strategy | `.pen` as registry artifact, token bridge |
| S49.9.2 | — | Token Bridge (Figma → Registry → Supabase) | Design tokens in registry |
| Epic 50 | — | FairSlice Platform Economics | Smart contract registry, partner artifacts |

## Appendix B: File-Based Registry Files (Current State)

| File | Path | Version | Type |
|---|------|---------|------|
| `ont-registry-index.json` | `ontology-library/ont-registry-index.json` | v10.9.1 | Ontology index |
| `join-pattern-registry.json` | `ontology-library/join-pattern-registry.json` | v1.0.0 | Join pattern index |
| `unified-glossary-v3.0.0.json` | `ontology-library/unified-glossary-v3.0.0.json` | v3.0.0 | Terminology |
| `pfc-release-manifest.json` | `ontology-library/pfc-release-manifest.json` | v1.0.0 | CI/CD manifest |
| `pfc-app-skeleton-v1.0.0.jsonld` | `ontology-visualiser/` | v1.0.0 | App skeleton |
| `pfi-baiv-graph-scope.json` | `ontology-library/pfi-graph-scopes/` | — | BAIV graph scope |
| `pfi-w4m-wwg-graph-scope.json` | `ontology-library/pfi-graph-scopes/` | — | W4M-WWG graph scope |
| `pfi-w4m-eoms-graph-scope.json` | `ontology-library/pfi-graph-scopes/` | — | W4M-EOMS graph scope |
| `pfi-airl-caf-aza-graph-scope.json` | `ontology-library/pfi-graph-scopes/` | — | AIRL graph scope |
| `pfi-vhf-graph-scope.json` | `ontology-library/pfi-graph-scopes/` | — | VHF graph scope |
| `pfi-pc-dice-graph-scope.json` | `ontology-library/pfi-graph-scopes/` | — | PC-DICE graph scope |
| `pfi-w4m-graph-scope.json` | `ontology-library/pfi-graph-scopes/` | — | W4M graph scope |

---

*VSOM Strategy Briefing — Unified Registry Database*
*Status: DRAFT — Ready for strategic review and feature planning*
*Companion to: Epic 34: PFC-VSOM — Unified Registry Skills Architecture*


---

## DTP Database Sync / Micro-SaaS Brief

# Epic 59: Platform Database Architecture — PFC-PFI Cascade, Per-Stage Isolation & Micro-SaaS Foundation

**Version:** 2.0.0 | **Date:** 2026-03-05 | **Status:** DRAFT — P0 Critical
**Classification:** CONFIDENTIAL — Strategic Planning Asset
**Aligned to:** S1 Graph-First Architecture, S4 Instance+Client Customisation ([Epic 34: PFC-VSOM — PF Strategy](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518))
**Depends on:** Epic 31 (#394), Epic 58 (#837), Epic 10A (#127), Epic 53 (#775)
**Cross-Refs:** Epic 56 (#834), Epic 39 (#562/#569, closed), [Epic 34: PFC-VSOM — PF Strategy (#518, closed)](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518)

| Field | Value |
|---|---|
| **Document** | BRIEFING-Epic59-Platform-Database-Architecture |
| **Date** | 2026-03-05 |
| **Status** | DRAFT — P0 Critical — Ready for strategic review |
| **Priority** | P0 Critical — blocking platform monetisation and PFI isolation |
| **Companion To** | Epic 59 (#840) |
| **Cross-Refs** | ARCH-PFC-PFI-Product-Client-Graph-Cascade.md, BRIEFING-Unified-Registry-Database-Strategy.md, BRIEFING-GraphQL-Supabase-JSONB-MVP.md, BRIEFING-PFC-EA-Arch-DB-Migrations-Azure-Supabase.md, CONVERGENCE-FairSlice-JSONB-Graph-Patterns.md |
| **VSOM Pattern** | Vision → 5 Strategies → BSC Objectives → Metrics |

---

## 0. Why This Is Its Own Epic

Epic 58 gives PFC its own git triad. Epic 31 defines hub-and-spoke CI/CD. Epic 10A writes the schema. But **none of them implement the database layer as a live, per-stage, cascade-aware platform.**

Five architecture documents define the runtime model:

| Document | What It Defines | Gap |
|----------|----------------|-----|
| **ARCH-PFC-PFI-Product-Client-Graph-Cascade** | 4-tier cascade (Core→Instance→Product→Client) | Assumes single DB — no per-stage isolation |
| **BRIEFING-Unified-Registry-Database-Strategy** | `pfc_registry` + `resolve_artifact_config()` + 10 domains | No per-PFI deployment, no promotion pipeline |
| **BRIEFING-GraphQL-Supabase-JSONB-MVP** | `resolve_composed_graph()`, auto-GraphQL | Assumes one Supabase instance at runtime |
| **BRIEFING-PFC-EA-Arch-DB-Migrations-Azure-Supabase** | Platform choice per PFI (Supabase/Azure/sovereign) | Vendor selection only — not DTP per-stage |
| **CONVERGENCE-FairSlice-JSONB-Graph-Patterns** | Licensing, royalty, smart-contract patterns | Single-tenancy assumed, no cross-instance promotion |

**This epic operationalises all five** — taking the cascade architecture from design documents into real, isolated, promotable databases per stage, per instance, per cascade tier. This is core solution architecture, not CI/CD plumbing.

---

## 1. Executive Summary

PF-Core and each PFI instance operate dev→test→prod (DTP) triads for git artefacts. The missing layer is the **database platform** — each stage needs its own Supabase project, and the 4-tier cascade (Core→Instance→Product→Client) must resolve correctly in every environment. Data must flow in a controlled, one-directional cascade:

```
PFC-Core DB (pfc-dev) → PFC DB (pfc-test) → PFC DB (pfc-prod)
                                                    │
                              ┌─────────────────────┼─────────────────────┐
                              ▼                     ▼                     ▼
                        PFI-BAIV DB (dev)     PFI-AIRL DB (dev)    PFI-W4M DB (dev)
                              │                     │                     │
                              ▼                     ▼                     ▼
                        PFI-BAIV DB (test)    PFI-AIRL DB (test)   PFI-W4M DB (test)
                              │                     │                     │
                              ▼                     ▼                     ▼
                        PFI-BAIV DB (prod)    PFI-AIRL DB (prod)   PFI-W4M DB (prod)
```

**Direction is absolute:** PFC→PFI (never reverse). Each PFI owns its instance data and promotes through its own triad independently. Future: PFI→PFI licensing enables cross-instance ontology and data-structure sharing under commercial terms.

This is **core solution architecture**, not CI/CD plumbing. It operationalises the 4-tier cascade model (`ARCH-PFC-PFI-Product-Client-Graph-Cascade`), the unified registry (`BRIEFING-Unified-Registry-Database-Strategy`), the GraphQL/JSONB runtime (`BRIEFING-GraphQL-Supabase-JSONB-MVP`), the platform-choice architecture (`BRIEFING-PFC-EA-Arch-DB-Migrations-Azure-Supabase`), and the FairSlice licensing patterns (`CONVERGENCE-FairSlice-JSONB-Graph-Patterns`) into real, isolated, promotable databases. Without it, PFI instances cannot run production workloads with data isolation guarantees, and the micro-SaaS boundary cannot be enforced.

---

## 2. Current State Assessment

### What We Have

| Asset | Status | Epic |
|-------|--------|------|
| Hub-and-spoke git pipeline architecture | Designed, partially implemented | Epic 31 (#394) |
| PFC-Core own triad strategy | Briefed, not yet bootstrapped | Epic 58 (#837) |
| 15 PFI triad repos (5 instances × 3 stages) | Created, secrets not set | Epic 39 (closed) |
| Supabase schema (5 tables + RLS) | Written, NOT deployed | Epic 10A (#127) |
| GraphQL layer + `resolve_composed_graph()` | Written, NOT deployed | Migration 005 |
| `pfc_registry` table + `resolve_cascaded_config()` | Planned, NOT written | Migration 004 |
| `bootstrap-triad.sh` script | Done (git repos only) | S31.4.1 (#423) |
| Promotion guide + training docs | Written | Epic 42 (closed) |
| Cloud sovereignty strategy (Azure/self-hosted) | Briefed | Epic 53 (#775) |

### Critical Gaps

| Gap | Impact | Severity |
|-----|--------|----------|
| **No per-stage Supabase projects** | Cannot isolate dev/test/prod data | P0 |
| **No DB promotion workflow** | Schema changes cannot flow dev→test→prod | P0 |
| **No PFC→PFI DB distribution** | PFI instances have no core data | P0 |
| **No DB drift detection** | Schema divergence between stages undetectable | P1 |
| **No PFI→PFI licensing model** | Cross-instance data sharing impossible | P2 |
| **PROMOTION_PAT not set** | No repo can promote (git or DB) | P0 |
| **Supabase secrets set only on BAIV-dev** | 14 of 15 repos have no DB connection | P0 |

---

## 3. VSOM Framework

### Vision

> **Implement the PFC→PFI→Product→Client cascade architecture as a live, per-stage database platform where each DTP triad runs isolated Supabase projects, schema and core data flow one-directionally from PFC to PFI, instance customisation is self-service through the 4-tier cascade, and the architecture enables micro-SaaS monetisation at the PFI boundary.**

### Strategy Pillars

#### S59.1: Database-Per-Stage Architecture

Every DTP stage gets its own Supabase project. PFC has 3 (pfc-dev, pfc-test, pfc-prod). Each PFI has 3. Total for 5 current PFIs: **18 Supabase projects**.

| Layer | Dev | Test | Prod |
|-------|-----|------|------|
| PFC-Core | `pfc-core-dev` | `pfc-core-test` | `pfc-core-prod` |
| PFI-BAIV | `pfi-baiv-dev` | `pfi-baiv-test` | `pfi-baiv-prod` |
| PFI-AIRL | `pfi-airl-dev` | `pfi-airl-test` | `pfi-airl-prod` |
| PFI-W4M-WWG | `pfi-w4m-wwg-dev` | `pfi-w4m-wwg-test` | `pfi-w4m-wwg-prod` |
| PFI-W4M-EOMS | `pfi-w4m-eoms-dev` | `pfi-w4m-eoms-test` | `pfi-w4m-eoms-prod` |
| PFI-VHF | `pfi-vhf-dev` | `pfi-vhf-test` | `pfi-vhf-prod` |

**Design decisions:**
- Each project is a full Supabase instance (Postgres + Auth + RLS + Edge Functions)
- Schema is identical across all projects at the same version
- Data differs: PFC projects hold core ontologies + registry; PFI projects hold instance data + PFC-core subset
- Auth is project-scoped — users authenticate per-project (SSO bridge is a future concern, Epic 53)

#### S59.2: Schema Promotion Pipeline (DB CI/CD)

Extend the existing `promote.yml` git workflow to include database migrations:

```
┌──────────────────────────────────────────────────────────┐
│                  PFC Schema Promotion                     │
│                                                          │
│  pfc-dev DB ──migrate──▶ pfc-test DB ──migrate──▶ pfc-prod DB │
│      │                       │                       │        │
│  supabase/     supabase db   supabase/    approve +  supabase/│
│  migrations/   push --dry    migrations/  supabase   migrations/
│  (authored)    (validated)   (tested)     db push    (released)│
└──────────────────────────────────────────────────────────┘
```

**Migration flow:**
1. Developer authors migration in `supabase/migrations/` on pfc-dev
2. `promote.yml` (dev→test): runs `supabase db push --dry-run` against pfc-test, creates approval PR
3. SME approves → `supabase db push` executes against pfc-test
4. Integration tests run on pfc-test (schema validation, RLS policy tests, seed data verification)
5. `promote.yml` (test→prod): requires `needs-sme-approval` label + bump field, runs against pfc-prod
6. On successful pfc-prod push: triggers `pfc-release.yml` which distributes migrations to PFI dev repos

**Key invariants:**
- Migrations are append-only — never edit a deployed migration
- `supabase db diff` generates change detection between stages
- Rollback is a new forward migration (no destructive rollbacks)

#### S59.3: PFC→PFI Database Distribution

When PFC-prod releases a new version (including schema changes), each subscribed PFI dev repo receives:

1. **Schema migrations** — copied to PFI dev repo `supabase/migrations/`, filtered by series subscription
2. **Core seed data** — ontologies marked `pfi_id = 'PF-CORE'` in the ontologies table (read-only in PFI)
3. **Registry configuration** — `pfc_registry` rows for the PFI's subscribed artifact domains
4. **Cascade defaults** — `resolve_cascaded_config()` base layer values

```
PFC-prod release (pfc-v2.1.0)
    │
    ├─▶ pfi-baiv-dev: schema + VE,PE,RCSG,Foundation,Orchestration seed
    ├─▶ pfi-airl-dev: schema + RCSG,VE,Foundation seed
    ├─▶ pfi-w4m-wwg-dev: schema + VE,PE,RCSG,Foundation,Orchestration seed
    ├─▶ pfi-w4m-eoms-dev: schema + VE,PE,RCSG,Foundation,Orchestration seed
    └─▶ pfi-vhf-dev: schema + VE,Foundation,Orchestration seed
```

**Subscription filtering** uses the existing `hubSpokeConfig.ontologySeries` array from `ont-registry-index.json`. Only ontologies in subscribed series are seeded. The PFI's `pfi_instances` row is auto-created with `brand_config` from the registry.

**Guard-core equivalent for DB:** PFC-owned rows in PFI databases are marked `source = 'pfc-core'` and protected by RLS — only `service_role` (from CI/CD) can write them. PFI users can read but never modify PFC-core data.

#### S59.4: PFI-Owned Instance Data Promotion

Each PFI promotes its own instance data independently through its triad:

```
PFI-BAIV-dev ──promote──▶ PFI-BAIV-test ──promote──▶ PFI-BAIV-prod
     │                          │                          │
     │ VP instances             │ UAT validated            │ Live
     │ RRR roles                │ SME approved             │ Production
     │ Graph scope rules        │ Load tested              │ Customer-facing
     │ Brand config             │                          │
     │ Custom ontology data     │                          │
```

**What PFI promotes (instance-owned data):**
- VP instance data (`baiv-ai-visibility-vp-instance-v1.0.0.jsonld` → JSONB)
- RRR RBAC roles (`RRR-DATA-BAIV-AIV-roles-v1.0.0.jsonld` → JSONB)
- Graph scope rules (`pfi-baiv-graph-scope.json` → `composed_graphs` + scope rules)
- Brand configuration (`brand_config` JSONB on `pfi_instances`)
- Custom ontology extensions (PFI-authored ontologies, if any)
- Client org configurations (when multi-tenant within a PFI)

**What PFI does NOT promote (PFC-owned):**
- Core schema migrations (received from PFC-prod, applied automatically)
- Core ontology data (`source = 'pfc-core'` rows)
- Platform functions (`resolve_cascaded_config()`, `resolve_composed_graph()`)

**Promotion mechanism:**
- `promote-db.yml` workflow in each PFI repo
- Exports PFI-owned rows from source stage DB
- Applies them to target stage DB via `supabase db seed` or Edge Function
- Validates: row counts, FK integrity, RLS policy pass, cascade resolution test

#### S59.5: Micro-SaaS & PFI→PFI Licensing (Future)

The platform architecture naturally enables micro-SaaS at the PFI boundary. Each PFI instance is a self-contained product:

```
┌─────────────────────────────────────────────────────────────┐
│                    PF-Core Platform                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   PFI-BAIV   │  │  PFI-AIRL    │  │  PFI-W4M     │      │
│  │   (MarTech)  │  │  (AI Audit)  │  │  (Supply)    │      │
│  │              │  │              │  │              │      │
│  │  Own DB ×3   │  │  Own DB ×3   │  │  Own DB ×3   │      │
│  │  Own Auth    │  │  Own Auth    │  │  Own Auth    │      │
│  │  Own Clients │  │  Own Clients │  │  Own Clients │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│                                                             │
│  PFC License: pfc-enterprise (core + all series)            │
│  PFI License: per-instance, per-series subscription         │
└─────────────────────────────────────────────────────────────┘
```

**PFI→PFI Licensing model (Horizon 2 — future):**

A PFI should be able to:
1. **Export** a reusable data structure (ontology, VP template, graph scope ruleset) as a **licensable artefact**
2. **Publish** it to the PFC registry (`pfc_registry` with `artifactType = 'licensed-pfi-contribution'`)
3. **Another PFI subscribes** to it via their `hubSpokeConfig`, subject to licensing terms
4. **PFC mediates** — the artefact flows PFI-source-prod → PFC-prod (review) → PFI-target-dev

```
PFI-BAIV-prod                    PFC-prod                    PFI-AIRL-dev
     │                               │                            │
     │  Publish VP template          │                            │
     │  as licensable artefact ────▶ │  Review + approve          │
     │                               │  Add to pfc_registry ────▶ │  Receive as
     │                               │  with license terms        │  subscribed
     │                               │                            │  artefact
     │  Royalty tracking via         │                            │
     │  FairSlice patterns ◀──────── │                            │
```

**Registry schema extension for licensing:**
```json
{
  "artifactId": "BAIV-VP-TEMPLATE-MARTECH-001",
  "artifactType": "licensed-pfi-contribution",
  "sourcePfi": "PFI-BAIV",
  "license": "pfc-pfi-share",
  "royaltyModel": "fairslice:WaterfallRule",
  "subscribedPfis": ["PFI-AIRL"],
  "version": "1.0.0"
}
```

This leverages the FairSlice JSONB patterns already proven in the `CONVERGENCE-FairSlice-JSONB-Graph-Patterns.md` analysis — waterfall rules, audit logs, and smart contract patterns map directly.

---

## 4. Architecture

### 4.1 Database Topology

```
                         ┌─────────────────────┐
                         │    Azlan-EA-AAA      │
                         │   (Hub / Reference)  │
                         │   No own Supabase    │
                         └──────────┬───────────┘
                                    │ git artefacts + migrations
                                    ▼
              ┌─────────────────────────────────────────┐
              │           PFC-Core Triad (Epic 58)       │
              │                                         │
              │  pfc-dev          pfc-test      pfc-prod │
              │  ┌─────┐        ┌─────┐       ┌─────┐  │
              │  │ Git  │──────▶│ Git │──────▶│ Git  │  │
              │  │ Supa │──────▶│Supa │──────▶│ Supa │  │
              │  └─────┘        └─────┘       └─────┘  │
              └──────────────────────┬──────────────────┘
                                     │ pfc-release (git + DB seed)
                    ┌────────────────┼────────────────┐
                    ▼                ▼                ▼
              PFI-BAIV          PFI-AIRL         PFI-W4M
              ┌─────┐          ┌─────┐          ┌─────┐
              │ dev  │          │ dev  │          │ dev  │
              │Git+DB│          │Git+DB│          │Git+DB│
              └──┬───┘          └──┬───┘          └──┬───┘
                 ▼                 ▼                 ▼
              ┌─────┐          ┌─────┐          ┌─────┐
              │ test │          │ test │          │ test │
              │Git+DB│          │Git+DB│          │Git+DB│
              └──┬───┘          └──┬───┘          └──┬───┘
                 ▼                 ▼                 ▼
              ┌─────┐          ┌─────┐          ┌─────┐
              │ prod │          │ prod │          │ prod │
              │Git+DB│          │Git+DB│          │Git+DB│
              └─────┘          └─────┘          └─────┘
```

### 4.2 Data Ownership Model

| Data Category | Owner | Writable By | Promotion Path |
|--------------|-------|-------------|----------------|
| Core schema (DDL) | PFC | CI/CD only | pfc-dev → pfc-test → pfc-prod → all PFI-dev |
| Core ontologies (JSONB) | PFC | CI/CD only | pfc-dev → pfc-test → pfc-prod → subscribed PFI-dev |
| Platform functions (SQL) | PFC | CI/CD only | Part of schema migrations |
| RLS policies | PFC | CI/CD only | Part of schema migrations |
| `pfc_registry` core rows | PFC | CI/CD only | pfc-dev → pfc-test → pfc-prod → all PFI-dev |
| **FairSlice base WaterfallRule templates** (`source='pfc-core'`) | **PFC** | **CI/CD only** | **pfc-dev → pfc-test → pfc-prod → subscribed PFI-dev (Phase 3)** |
| **FairSlice core SmartContract definitions** (PFC-published) | **PFC** | **CI/CD only** | **pfc-dev → pfc-test → pfc-prod → subscribed PFI-dev (Phase 3)** |
| **FairSlice PFI WaterfallRule overrides** (`source='pfi-{code}'`) | **PFI admin** | **PFI admin** | **PFI-dev → PFI-test → PFI-prod** |
| **FairSlice PFI CommissionRule / SmartContract customisations** | **PFI admin** | **PFI admin** | **PFI-dev → PFI-test → PFI-prod** |
| PFI instance config | PFI admin | PFI admin + CI/CD | PFI-dev → PFI-test → PFI-prod |
| VP/RRR instance data | PFI team | PFI members | PFI-dev → PFI-test → PFI-prod |
| Graph scope rules | PFI admin | PFI admin | PFI-dev → PFI-test → PFI-prod |
| Brand/design tokens | PFI admin | PFI admin | PFI-dev → PFI-test → PFI-prod |
| Client org configs | PFI admin | PFI admin | PFI-dev → PFI-test → PFI-prod |
| User profiles/auth | Per-project | Self + admin | Not promoted (per-environment) |
| Audit log | System | Append-only | Not promoted (per-environment) |
| Licensed PFI artefacts | Source PFI | Source PFI + PFC | PFI-prod → PFC-prod → target PFI-dev |

> **FairSlice ownership rationale:** The quasi-OO cascade (FAIRSLICE-ONT → PFI overrides) maps to this ownership model. PFC-core rows provide the base that PFI overrides depend on — without them being seeded at Phase 3, PFI waterfall and commission customisations have no parent to override and the cascade silently breaks. `source = 'pfc-core'` RLS protects these rows in every PFI database.

### 4.3 Cascade Resolution Across Stages

`resolve_cascaded_config()` operates identically in every Supabase project. The 4-tier cascade:

```
Core (PFC-owned, source='pfc-core')
  └─▶ Instance (PFI-owned, pfi_id='PFI-BAIV')
       └─▶ Product (PFI product config)
            └─▶ Client (client-org specific overrides)
```

Each stage (dev/test/prod) resolves independently. The cascade function is **schema** (PFC-owned), the data it resolves over is a mix of PFC-owned (core tier) and PFI-owned (instance/product/client tiers).

### 4.4 Supabase CLI Integration

```yaml
# promote-db.yml (added to existing promote.yml)
- name: Export PFI-owned data from source
  run: |
    supabase db dump --data-only \
      --schema public \
      --filter "source != 'pfc-core'" \
      --db-url ${{ secrets.SOURCE_SUPABASE_DB_URL }} \
      > pfi-instance-data.sql

- name: Apply schema migrations to target
  run: |
    supabase db push \
      --db-url ${{ secrets.TARGET_SUPABASE_DB_URL }}

- name: Seed PFI-owned data to target
  run: |
    psql ${{ secrets.TARGET_SUPABASE_DB_URL }} \
      -f pfi-instance-data.sql

- name: Validate cascade resolution
  run: |
    psql ${{ secrets.TARGET_SUPABASE_DB_URL }} \
      -c "SELECT resolve_cascaded_config('$PFI_CODE', 'ontology', 'VP-ONT');"
```

---

## 5. VE Skills Evaluation

### VSOM Cascade

| VE Stage | Application | Status |
|----------|-------------|--------|
| **VSOM** — Vision | Graph-native micro-SaaS platform with DB-per-stage isolation | Defined above |
| **VSOM** — Strategy | 5 pillars: DB-per-stage, Schema promotion, PFC→PFI distribution, PFI promotion, PFI→PFI licensing | Defined above |
| **OKR** | See BSC Objectives below | Pending |
| **KPI** | See Metrics below | Pending |
| **VP** | PFI operators get isolated, promotable, licensable database environments | Defined |
| **RRR** | Platform roles (pf-owner, pfi-admin) map to DB access tiers via RLS | From Epic 10A |
| **Kano** | DB isolation = Must-Be; PFI→PFI licensing = Attractive | Assessed |
| **PMF** | First signal: BAIV-prod running on own Supabase with live data | Target |

### BSC Objectives

| Perspective | Objective | Metric | Target |
|-------------|-----------|--------|--------|
| **Financial** | OBJ-F59.1: Reduce PFI provisioning cost | Time to provision new PFI DB triad | < 30 min automated |
| **Financial** | OBJ-F59.2: Enable micro-SaaS billing boundary | PFIs with isolated prod DBs | 5/5 by Q2 2026 |
| **Customer** | OBJ-C59.1: PFI data isolation guarantee | Zero cross-PFI data leakage incidents | 0 (hard constraint) |
| **Customer** | OBJ-C59.2: PFI self-service promotion | PFI teams can promote dev→test→prod independently | All 5 PFIs |
| **Internal** | OBJ-IP59.1: Schema consistency across all DBs | Schema drift detected within 24h | 100% coverage |
| **Internal** | OBJ-IP59.2: PFC→PFI distribution < 1 hour | Time from pfc-prod tag to PFI-dev seed complete | < 60 min |
| **Internal** | **OBJ-IP59.3: FairSlice base config seeded to all subscribed PFI-dev** | **All subscribed PFI-dev contain PFC-core WaterfallRule templates + SmartContract definitions (`source='pfc-core'`)** | **Q2 2026 — P1** |
| **Learning** | OBJ-LG59.1: Team capability for DB promotion | PFI teams trained on `promote-db.yml` | All teams |

### Metrics (KPIs)

| KPI | Type | Measure | Target |
|-----|------|---------|--------|
| KPI-59.1 | Leading | Supabase projects provisioned | 18/18 |
| KPI-59.2 | Leading | Secrets configured across repos | 15/15 repos × 3 secrets |
| KPI-59.3 | Leading | Schema migrations passing on all stages | 100% |
| KPI-59.4 | Lagging | Successful dev→test→prod DB promotions | ≥ 1 per PFI |
| KPI-59.5 | Lagging | PFC→PFI distribution completions | ≥ 1 full cycle |
| KPI-59.6 | Lagging | Mean time to detect schema drift | < 24 hours |
| KPI-59.7 | Leading | RLS policy tests passing per project | 100% |
| KPI-59.8 | Future | PFI→PFI licensed artefact transfers | ≥ 1 (Horizon 2) |

---

## 6. Relationship to Existing Epics

### Dependency Graph

```
Epic 10A (#127)                 Epic 58 (#837)
Security MVP                    PFC-Core Own Triad
(Schema + RLS)                  (Git triad)
       │                              │
       │  provides schema             │  provides git pipeline
       │  + RLS policies              │  + promotion workflows
       │                              │
       └──────────┬───────────────────┘
                  │
                  ▼
         ┌─────────────────┐
         │  Epic 59 (THIS) │
         │  DTP DB Sync    │
         │  Micro-SaaS     │
         └────────┬────────┘
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
  Epic 31     Epic 53     Epic 56
  Hub-Spoke   Cloud       Strategy-to-
  CI/CD       Sovereign   Build
  Pipeline    Delivery    Pipeline
```

### Epic Overlap Analysis

| Epic | Overlap With This | Resolution |
|------|-------------------|------------|
| **Epic 31** (#394) | F31.1-F31.6 define git CI/CD; this epic adds DB layer | Extend promote.yml, don't replace |
| **Epic 58** (#837) | PFC triad git repos; this adds Supabase per stage | Co-dependent — PFC triad must exist for DB triad |
| **Epic 10A** (#127) | Schema, RLS, auth are prerequisites | This epic consumes 10A outputs, deploys them per-stage |
| **Epic 53** (#775) | Azure/sovereign alternatives to Supabase | S53.5.4 (Keycloak) replaces Supabase Auth per-sovereign-tier |
| **Epic 56** (#834) | Component-led framework; this adds DB component | DB provisioning becomes a component in the build pipeline |
| **Epic 39** (closed) | Prior art — PFI cascade, artifact migration | Lessons learned: config-not-copy pattern applies to DB seed |

---

## 7. Phased Delivery Roadmap

### Phase 1: Foundation (Weeks 1-2) — P0

| # | Deliverable | Dependency |
|---|-------------|------------|
| F59.1 | Provision 18 Supabase projects (3 PFC + 15 PFI) | Supabase account/billing |
| F59.2 | Deploy schema (migrations 001-005) to all 18 projects | Epic 10A migrations |
| F59.3 | Configure secrets on all 15 PFI repos + 3 PFC repos | PROMOTION_PAT resolution |
| F59.4 | Write migration 004 (`pfc_registry` + `resolve_cascaded_config()`) | Epic 10A, BRIEFING-Unified-Registry-DB |

### Phase 2: PFC Pipeline (Weeks 3-4) — P0

| # | Deliverable | Dependency |
|---|-------------|------------|
| F59.5 | Create `promote-db.yml` workflow for PFC triad | Epic 58 F58.2 |
| F59.6 | Seed PFC-prod with core ontologies from ont-registry-index.json | Registry v10.8.0 |
| F59.7 | Create `pfc-db-release.yml` — distribute schema + seed to PFI dev repos | Epic 31 F31.1 |
| F59.8 | Validate: full cycle pfc-dev → pfc-test → pfc-prod → BAIV-dev | — |

### Phase 3: PFI Pipeline (Weeks 5-6) — P1

| # | Deliverable | Dependency |
|---|-------------|------------|
| F59.9 | Create `promote-db.yml` for PFI triads (template) | Phase 2 |
| F59.10 | Seed BAIV-dev with VP + RRR + graph scope instance data | BAIV instance artefacts |
| **F59.10a** | **Seed subscribed PFI-dev with FairSlice PFC-core rows (WaterfallRule templates + core SmartContract definitions, `source='pfc-core'`) via `pfc-db-release.yml`** | **Epic 50 F50.3 schema + Phase 2 F59.7** |
| F59.11 | BAIV full cycle: dev → test → prod with SME approval | Phase 2 |
| F59.12 | Roll out to AIRL, W4M-WWG, W4M-EOMS, VHF | Phase 3 BAIV PoC |

### Phase 4: Governance & Drift (Weeks 7-8) — P1

| # | Deliverable | Dependency |
|---|-------------|------------|
| F59.13 | Schema drift detection workflow (`db-drift-detect.yml`) | Phase 2-3 |
| F59.14 | DB-inclusive operating guide + runbooks | Epic 31 F31.6 |
| F59.15 | Sovereignty adaptation documentation (Azure/self-hosted) | Epic 53 |

### Phase 5: PFI→PFI Licensing (Horizon 2) — **P1** *(elevated from P2 — monetisation boundary)*

| # | Deliverable | Dependency |
|---|-------------|------------|
| F59.16 | `pfc_registry` extension for licensed-pfi-contribution | Phase 2 |
| F59.17 | PFI export workflow (prod → PFC review → target PFI dev) | Phase 3 |
| F59.18 | **FairSlice royalty integration for cross-PFI artefacts** — `royaltyModel: 'fairslice:WaterfallRule'` governs commercial terms on all licensed PFI contributions | Epic 51 patterns + F59.10a |
| F59.19 | First cross-PFI transfer (e.g., BAIV VP template → AIRL) | F59.16-F59.18 |

---

## 8. Decision Points

| # | Decision | Options | Recommendation |
|---|----------|---------|----------------|
| D1 | Supabase project per stage vs shared project with schema prefixes | Per-project (full isolation) vs per-schema (cheaper) | **Per-project** — aligns with RLS boundary, matches auth isolation |
| D2 | DB promotion mechanism | Supabase CLI (`db push`) vs custom SQL export/import vs Edge Functions | **Supabase CLI** for schema; custom export for instance data |
| D3 | PFC→PFI seed: full dump vs filtered | Full schema + filtered data by series subscription | **Filtered** — uses `ontologySeries` from registry |
| D4 | User auth across stages | Separate users per stage vs SSO across stages | **Separate** — dev/test users must not access prod |
| D5 | When to provision Supabase for sovereign (Azure) PFIs | Now (Supabase managed) vs later (self-hosted) | **Now** for Supabase managed; Epic 53 handles sovereign later |
| D6 | PFI→PFI licensing: registry-mediated vs direct | PFC mediates all transfers vs PFI-to-PFI direct | **PFC-mediated** — maintains platform governance |

---

## 9. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Supabase free-tier limits (2 projects) | High | Blocks provisioning | Use Pro plan; evaluate self-hosted for cost |
| Schema migration conflicts between PFC and PFI | Medium | Data corruption | Append-only migrations; PFC schema is authoritative |
| PROMOTION_PAT scope too broad | High | IP exposure | Scoped tokens per triad (Epic 58 addresses) |
| RLS policy divergence across projects | Medium | Security gap | Schema drift detection (F59.13) |
| PFI→PFI licensing creates data governance complexity | Low | Contractual disputes | FairSlice audit trail + PFC mediation |

---

## 10. Cross-Reference Map

| Document | Relationship |
|----------|-------------|
| `PLAN-Supabase-JSONB-MVP-Platform-Phase.md` (v3.2.0) | Upstream — defines trigger chain this epic operationalises |
| `BRIEFING-Unified-Registry-Database-Strategy.md` | Upstream — `pfc_registry` table this epic deploys per-stage |
| `BRIEFING-PFC-EA-Arch-DB-Migrations-Azure-Supabase.md` | Adjacent — sovereign DB options feed into Phase 4 |
| `TRAINING-PFI-Release-and-Promotion-Guide.md` | Adjacent — must be extended with DB promotion sections |
| `CONVERGENCE-FairSlice-JSONB-Graph-Patterns.md` | **Upstream (base config)** — FairSlice WaterfallRule templates + SmartContract definitions seeded in Phase 3 (F59.10a, OBJ-IP59.3); **Downstream (licensing)** — cross-PFI royalty patterns in Phase 5 (F59.18) |
| **Epic 50 (#754)** | **Adjacent — FairSlice schema (F50.3) is a prerequisite for F59.10a; Epic 50 section 3.5 tier ownership model maps to this epic's data ownership table** |
| **Epic 51** | **Upstream for Phase 5** — VE Skill Chain collaboration patterns feed F59.18 royalty integration |
| `PROPOSAL-Supabase-Secure-Connections-API-MCP-v1.0.0.md` | Adjacent — API/MCP layer sits atop per-project DBs |
| `ARCH-PFC-PFI-Product-Client-Graph-Cascade.md` | Architecture — cascade model this epic implements per-stage |
| `PBS/ARCHITECTURE/arch-cicd/02-promotion-pipeline-detail.md` | Architecture — git promotion this epic extends to DB |

---

*Generated: 2026-03-05 | Updated: 2026-03-09 | Epic 59 | P0 Critical*
*VSOM Skills: Vision ✓ Strategy ✓ BSC ✓ KPI ✓ VP ✓ RRR (ref) ✓ Kano (assessed) ✓*
*v2.1.0 — Added FairSlice tier ownership to data model (section 4.2), OBJ-IP59.3, F59.10a (Phase 3 FairSlice seed), Phase 5 elevated to P1, cross-references updated for Epic 50/51*


---

## Supabase Secure Connections Proposal

# Proposal: Secure Connections — Supabase CLI, API & MCP Architecture

**Version:** 1.2.0 | **Date:** 2026-03-12 | **Status:** Proposal
**Aligned to:** [Epic 34: PFC-VSOM — PF Strategy](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/518) (S1 Graph-First, S3 Agentic), Epic 10A (Security MVP)
**ADR References:** ADR-001 (Supabase-First), ADR-005 (Shared RLS Multi-Tenancy), PF-CORE-ADR-2026-001 (No Neo4j)

---

## 1. Problem Statement

The platform needs four secure data exchange paths:

1. **Claude Code → Supabase CLI** — interactive development, admin, migrations, debugging, ad-hoc queries
2. **Claude Agents → Supabase MCP** — structured tool calls within automated pipeline skills (e.g. DELTA)
3. **Visualiser (Browser) → Supabase** — authenticated users CRUD ontologies (replacing IndexedDB)
4. **Third-party Systems → Supabase** — export/import, webhook triggers, external integrations

The naive approach — "build a Supabase MCP server with 30 tools" — carries **prohibitive token overhead**. Every MCP tool definition ships with every Claude API call: schema, description, parameters, examples. A 30-tool MCP server easily burns 10,000–20,000 tokens per request _before the conversation even starts_. At Sonnet rates that's ~$0.01–0.02/request wasted on tool definitions alone, multiplied across every agent turn in a pipeline.

The Supabase CLI provides a complementary path — Claude Code invokes it via the Bash tool (zero additional token overhead) for operations that don't require structured MCP tool responses. All four interfaces are valid; each serves different contexts.

The architecture must be **token-efficient by design**.

---

## 2. Proposed Architecture: Four Layers

```
                          ┌─────────────────────────┐
                          │     SUPABASE PROJECT     │
                          │  ┌───────────────────┐   │
                          │  │  PostgreSQL + RLS  │   │
                          │  │  5 MVP tables      │   │
                          │  │  pfc_registry      │   │
                          │  │  audit_log         │   │
                          │  └────────┬──────────┘   │
                          │           │               │
                          │  ┌────────┴──────────┐   │
                          │  │  Edge Functions    │   │
                          │  │  (Deno runtime)    │   │
                          │  │  8 functions        │   │
                          │  └────────┬──────────┘   │
                          │           │               │
                          │  ┌────────┴──────────┐   │
                          │  │  Supabase Auth     │   │
                          │  │  JWT + RLS bridge  │   │
                          │  └──────────────────┘   │
                          └───────────┬─────────────┘
                                      │
          ┌───────────────┬───────────┼───────────┬──────────────────┐
          │               │           │           │                    │
  ┌───────┴──────┐ ┌──────┴───────┐ ┌┴──────────┐ ┌───────┴────────┐
  │  LAYER 1     │ │  LAYER 2     │ │  LAYER 3  │ │  LAYER 4       │
  │  Browser     │ │  Claude MCP  │ │  Claude   │ │  REST API      │
  │  (Visualiser)│ │  (Thin)      │ │  CLI      │ │  (Third-party) │
  │              │ │              │ │           │ │                 │
  │  supabase-js │ │  5 tools     │ │  Bash     │ │  API keys       │
  │  anon key    │ │  service key │ │  tool     │ │  Edge Functions │
  │  + JWT/RLS   │ │  + PFI scope │ │  0 tokens │ │  + rate limits  │
  └──────────────┘ └──────────────┘ └───────────┘ └─────────────────┘
```

### Layer 1: Browser → Supabase (Existing Pattern)

Already designed in Epic 10A. No changes needed.

- `supabase-js` client with anon key
- Supabase Auth issues JWT on login
- RLS policies enforce PFI scoping at the database layer
- `auth.uid()` embedded in JWT drives all policy evaluation
- Zero server-side code required for basic CRUD

### Layer 2: Claude Agent → MCP → Edge Functions → Supabase (New)

**The token-efficient design.** Structured tool calls for automated agent pipelines.

### Layer 3: Claude Code → Supabase CLI → Supabase (New)

**Zero token overhead.** Claude Code invokes the Supabase CLI via the Bash tool, which is always present in the context. No MCP tool definitions required. Covers the full Supabase management surface: migrations, Edge Function deployment, SQL execution, secrets management, project administration, and ad-hoc queries.

### Layer 4: Third-party → REST API → Edge Functions → Supabase (New)

API key-authenticated access for external systems (webhooks, data sync, partner integrations).

---

## 3. Layer 2 Deep Dive: Thin MCP Server (5 Tools)

### The Token Budget Problem

| Approach | Tools | Tool Definition Tokens | Per-request Overhead |
|---|---|---|---|
| Full Supabase MCP | 25-35 tools | 15,000-25,000 | Unacceptable |
| Thin domain MCP | 5 tools | 2,500-4,000 | Acceptable |
| Supabase CLI (Bash) | 0 tools | 0 | Full management surface, zero overhead |
| No MCP (raw API) | 0 tools | 0 | No Claude integration |

**Decision: 5 coarse-grained tools that map to domain operations, not CRUD primitives.**

An agent doesn't need `supabase_select`, `supabase_insert`, `supabase_update`, `supabase_delete`, `supabase_rpc` as separate tools — that's ORM-thinking. An agent needs _"get the ontologies for this PFI"_ and _"save this analysis result"_.

### The 5 MCP Tools

```
┌──────────────────────────────────────────────────────────────────┐
│  pfc-supabase MCP Server (Thin)                                  │
│                                                                   │
│  Tool 1: pfc_query_ontologies                                    │
│    Input:  { pfi_code, filters?, include_data? }                 │
│    Output: { ontologies: [...] }                                 │
│    Maps to: Edge Function /ontologies/query                      │
│                                                                   │
│  Tool 2: pfc_write_artifact                                      │
│    Input:  { pfi_code, artifact_type, name, version, data }      │
│    Output: { id, status, audit_id }                              │
│    Maps to: Edge Function /artifacts/write                       │
│                                                                   │
│  Tool 3: pfc_resolve_config                                      │
│    Input:  { artifact_type, name, instance_id?, client_id? }     │
│    Output: { effective_config: {...} }                            │
│    Maps to: Edge Function /registry/resolve                      │
│                                                                   │
│  Tool 4: pfc_export_graph                                        │
│    Input:  { pfi_code, format, scope?, categories? }             │
│    Output: { graph_data, export_url? }                           │
│    Maps to: Edge Function /graph/export                          │
│                                                                   │
│  Tool 5: pfc_audit_query                                         │
│    Input:  { pfi_code, action?, resource?, since?, limit? }      │
│    Output: { entries: [...], total_count }                        │
│    Maps to: Edge Function /audit/query                           │
│                                                                   │
└──────────────────────────────────────────────────────────────────┘
```

### Why 5 and Not 8 or 12

Each additional tool adds ~500-800 tokens to every request. The 5 chosen tools cover:

| Agent Activity | Tool Used |
|---|---|
| DELTA Phase 1 (Scope) — load PFI ontologies | `pfc_query_ontologies` |
| DELTA Phase 2 (Evaluate) — read config cascade | `pfc_resolve_config` |
| DELTA Phase 4 (Narrate) — save analysis output | `pfc_write_artifact` |
| DELTA Phase 5 (Adapt) — export for comparison | `pfc_export_graph` |
| Any phase — check what happened | `pfc_audit_query` |

Auth, user management, and PFI admin are **not exposed via MCP**. Those are UI-only operations (Layer 1) or admin API operations (Layer 3). Agents don't need to create users or manage PFI memberships.

### MCP Server Implementation

```typescript
// pfc-supabase-mcp/src/server.ts
// Thin MCP server — 5 domain tools, ~120 lines

import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const SUPABASE_URL = process.env.SUPABASE_URL;
const SUPABASE_SERVICE_KEY = process.env.SUPABASE_SERVICE_KEY;
const PFI_SCOPE = process.env.PFC_PFI_SCOPE; // Constrain agent to one PFI

const server = new Server({
  name: "pfc-supabase",
  version: "1.0.0",
}, {
  capabilities: { tools: {} }
});

// Each tool is a thin wrapper that:
// 1. Validates input
// 2. Adds PFI scope constraint (agent can't escape its PFI)
// 3. Calls the Edge Function with service key
// 4. Returns shaped response (no raw SQL, no leaky abstraction)

async function callEdgeFunction(path: string, body: object) {
  const res = await fetch(`${SUPABASE_URL}/functions/v1${path}`, {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${SUPABASE_SERVICE_KEY}`,
      "Content-Type": "application/json",
      "X-PFI-Scope": PFI_SCOPE, // Edge function enforces this
    },
    body: JSON.stringify(body),
  });
  if (!res.ok) throw new Error(`Edge function error: ${res.status}`);
  return res.json();
}
```

### Authentication Flow (Agent Path)

```
Claude Agent (skill invocation)
    │
    ▼
pfc-supabase MCP Server (local process)
    │  SUPABASE_SERVICE_KEY (env var)
    │  PFC_PFI_SCOPE (env var — constrains to one PFI)
    ▼
Supabase Edge Function (Deno)
    │  Validates service key
    │  Enforces PFI scope from X-PFI-Scope header
    │  Calls Supabase client with service role
    │  RLS bypassed — Edge Function IS the access control layer
    │  Writes audit_log entry for every mutation
    ▼
PostgreSQL (ontologies, pfc_registry, audit_log)
```

**Why service key and not user JWT?**

Claude agents don't have user sessions. They execute on behalf of a PFI instance, not a specific user. The service key gives full access, but the Edge Function enforces PFI scoping via the `X-PFI-Scope` header — the agent cannot request data outside its declared PFI. Every mutation is audit-logged with `agent_id` in the detail JSONB.

**Key security constraint:** The MCP server reads `PFC_PFI_SCOPE` from environment, not from the agent's request. The agent cannot override its scope. This is configured when the MCP server is launched, not at runtime.

---

## 4. Layer 3 Deep Dive: Supabase CLI for Claude Code

### Why a CLI Layer?

The Supabase CLI (`supabase`) provides a complete management interface that Claude Code can invoke directly via the Bash tool. Unlike MCP tools, the Bash tool is always loaded in the Claude context — adding CLI operations costs **zero additional tokens** per request.

### Common CLI Commands

| Command | Purpose |
| --- | --- |
| `supabase init` | Initialize a new project |
| `supabase start` | Start local dev stack (Docker) |
| `supabase stop` | Stop local stack |
| `supabase login` | Authenticate with Supabase |
| `supabase link --project-ref <ref>` | Link to a remote project |
| `supabase db push` | Push local migrations to remote |
| `supabase db pull` | Pull remote schema as migration |
| `supabase db diff` | Diff local vs remote schema |
| `supabase db execute --sql "..."` | Run ad-hoc SQL against the database |
| `supabase db inspect` | Inspect table sizes, index usage, RLS status |
| `supabase migration new <name>` | Create a new migration file |
| `supabase functions serve` | Serve Edge Functions locally |
| `supabase functions deploy` | Deploy Edge Functions |
| `supabase functions logs <name>` | View Edge Function logs |
| `supabase secrets set KEY=value` | Manage project secrets |
| `supabase gen types typescript` | Generate TypeScript types from schema |
| `supabase projects list` | List all projects |

### CLI vs MCP Coverage

| Capability | CLI Command | MCP Equivalent |
| --- | --- | --- |
| Run SQL queries | `supabase db execute --sql "..."` | Would need a custom MCP tool |
| Apply migrations | `supabase db push` / `supabase migration new` | Not exposed via MCP |
| Deploy Edge Functions | `supabase functions deploy <name>` | Not exposed via MCP |
| Manage secrets | `supabase secrets set KEY=value` | Not exposed via MCP |
| List projects | `supabase projects list` | Not exposed via MCP |
| Inspect tables | `supabase db inspect` | Not exposed via MCP |
| Generate types | `supabase gen types typescript` | Not exposed via MCP |
| View logs | `supabase functions logs <name>` | Not exposed via MCP |
| Local development | `supabase start` / `supabase stop` | Not applicable |
| Database diff | `supabase db diff` / `supabase db pull` | Not exposed via MCP |

### Authentication

```bash
# One-time login (stores access token locally)
supabase login

# Or set access token via environment variable
export SUPABASE_ACCESS_TOKEN=sbp_...

# Link to a specific project
supabase link --project-ref <project-ref>
```

The CLI authenticates using a personal access token or project-level service role key. For Claude Code sessions, the token is configured in the shell environment — Claude does not need to manage authentication per-request.

**Security note:** The CLI access token grants management-plane access (migrations, deploys, secrets). This is appropriate for interactive development sessions where a human is supervising. For unattended automated pipelines, use Layer 2 (MCP) or Layer 4 (REST API) with scoped credentials.

### Token Budget: Zero Overhead

```
┌──────────────────────────────────────────────────────────────┐
│  Supabase CLI via Bash Tool                                    │
│                                                                 │
│  Additional MCP tool definitions required:  0                  │
│  Additional tokens per request:             0                  │
│                                                                 │
│  Claude Code already has the Bash tool loaded.                 │
│  Every supabase CLI command is a standard Bash invocation.     │
│  Full Supabase management surface — no token cost.             │
└──────────────────────────────────────────────────────────────┘
```

### Data Exchange Patterns (CLI)

#### Pattern E: Ad-hoc SQL Query

```bash
supabase db execute --sql "
  SELECT artifact_id, name, version, scope
  FROM pfc_registry
  WHERE artifact_type = 'ontology' AND status = 'active'
  ORDER BY updated_at DESC
  LIMIT 20;
"
```

#### Pattern F: Apply a Migration

```bash
# Create a new migration
supabase migration new add_computed_column

# Edit the generated file, then push
supabase db push
```

#### Pattern G: Deploy an Edge Function

```bash
supabase functions deploy ontologies-query --no-verify-jwt
supabase functions logs ontologies-query --tail
```

#### Pattern H: Generate TypeScript Types from Schema

```bash
supabase gen types typescript --linked > src/types/supabase.ts
```

#### Pattern I: Inspect Database State

```bash
# Table sizes, index usage, RLS status
supabase db inspect --linked
```

### When to Use CLI vs MCP vs REST

| Scenario | Use |
|---|---|
| Interactive dev session — run a query, check data | CLI (Layer 3) |
| Automated DELTA pipeline — agent reads ontologies mid-skill | MCP (Layer 2) |
| Apply a migration during development | CLI (Layer 3) |
| BI tool pulls graph data on a schedule | REST API (Layer 4) |
| Debug an Edge Function | CLI (Layer 3) |
| CI/CD registry sync on merge | REST API (Layer 4) |
| Agent saves analysis artifact mid-conversation | MCP (Layer 2) |
| Generate TypeScript types after schema change | CLI (Layer 3) |

### CLI Setup for Claude Code

Add to project `.env` or shell profile:

```bash
# Supabase CLI authentication
export SUPABASE_ACCESS_TOKEN=sbp_<your-token>

# Or per-project linking (stored in supabase/.temp/)
cd /path/to/project
supabase link --project-ref <project-ref>
```

Claude Code can then invoke any `supabase` command directly. No MCP configuration needed.

---

## 5. Layer 4 Deep Dive: REST API for Third-Party Systems

### Edge Functions as API Gateway

8 Supabase Edge Functions serve both Layer 2 (MCP) and Layer 3 (REST) consumers:

| Edge Function | Path | Auth | Purpose |
|---|---|---|---|
| `ontologies-query` | `/functions/v1/ontologies/query` | Service key or API key | Read ontologies with filters |
| `artifacts-write` | `/functions/v1/artifacts/write` | Service key or API key | Create/update registry artifacts |
| `registry-resolve` | `/functions/v1/registry/resolve` | Service key or API key | Cascade config resolution |
| `graph-export` | `/functions/v1/graph/export` | Service key or API key | Export composed graphs |
| `audit-query` | `/functions/v1/audit/query` | Service key | Read audit trail (admin only) |
| `auth-webhook` | `/functions/v1/auth/webhook` | Webhook secret | User lifecycle events |
| `sync-registry` | `/functions/v1/sync/registry` | Service key | Git → Supabase registry sync |
| `health` | `/functions/v1/health` | None | Health check / status |

### Third-party API Key Pattern

For external systems that need data access (partner integrations, BI tools, CI/CD):

```sql
-- Migration 003: API keys for third-party access
CREATE TABLE public.api_keys (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  pfi_id      UUID NOT NULL REFERENCES public.pfi_instances(id),
  key_hash    TEXT NOT NULL,              -- bcrypt hash of the API key
  name        TEXT NOT NULL,              -- "BAIV CI/CD pipeline"
  permissions TEXT[] NOT NULL DEFAULT '{}', -- ['read:ontologies','write:artifacts']
  rate_limit  INT DEFAULT 100,            -- requests per minute
  expires_at  TIMESTAMPTZ,
  created_by  UUID REFERENCES public.profiles(id),
  created_at  TIMESTAMPTZ DEFAULT now(),
  last_used   TIMESTAMPTZ
);

-- RLS: pf-owner manages all, pfi-admin manages their PFI's keys
ALTER TABLE public.api_keys ENABLE ROW LEVEL SECURITY;

CREATE POLICY api_keys_manage ON public.api_keys FOR ALL USING (
  EXISTS (SELECT 1 FROM public.profiles WHERE id = auth.uid() AND role = 'pf-owner')
  OR (
    EXISTS (SELECT 1 FROM public.profiles WHERE id = auth.uid() AND role = 'pfi-admin')
    AND pfi_id IN (SELECT pfi_id FROM public.user_pfi_access WHERE user_id = auth.uid())
  )
);
```

### Edge Function Authentication Logic

```typescript
// supabase/functions/_shared/auth.ts
// Shared auth module for all Edge Functions

import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

type AuthResult = {
  type: "service" | "api_key" | "jwt";
  pfi_scope: string;       // enforced PFI boundary
  permissions: string[];
  actor_id: string;         // user UUID or API key UUID or "agent:<pfi>"
};

export async function authenticate(req: Request): Promise<AuthResult> {
  const authHeader = req.headers.get("Authorization");
  const pfiScope = req.headers.get("X-PFI-Scope");

  // Path 1: Service key (MCP server / internal)
  if (authHeader === `Bearer ${Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")}`) {
    if (!pfiScope) throw new Error("Service key requests require X-PFI-Scope");
    return {
      type: "service",
      pfi_scope: pfiScope,
      permissions: ["*"],
      actor_id: `agent:${pfiScope}`,
    };
  }

  // Path 2: API key (third-party)
  const apiKey = req.headers.get("X-API-Key");
  if (apiKey) {
    const supabase = createClient(
      Deno.env.get("SUPABASE_URL")!,
      Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!
    );
    const { data: keyRecord } = await supabase
      .from("api_keys")
      .select("id, pfi_id, permissions, rate_limit, expires_at")
      .eq("key_hash", await bcryptHash(apiKey))
      .single();

    if (!keyRecord) throw new Error("Invalid API key");
    if (keyRecord.expires_at && new Date(keyRecord.expires_at) < new Date()) {
      throw new Error("API key expired");
    }
    // Rate limiting check would go here

    // Look up PFI code from UUID
    const { data: pfi } = await supabase
      .from("pfi_instances")
      .select("code")
      .eq("id", keyRecord.pfi_id)
      .single();

    return {
      type: "api_key",
      pfi_scope: pfi!.code,
      permissions: keyRecord.permissions,
      actor_id: keyRecord.id,
    };
  }

  // Path 3: JWT (browser — forwarded, rare for Edge Functions)
  if (authHeader?.startsWith("Bearer ")) {
    const token = authHeader.slice(7);
    const supabase = createClient(
      Deno.env.get("SUPABASE_URL")!,
      Deno.env.get("SUPABASE_ANON_KEY")!,
      { global: { headers: { Authorization: `Bearer ${token}` } } }
    );
    const { data: { user } } = await supabase.auth.getUser();
    if (!user) throw new Error("Invalid JWT");

    // Get user's PFIs
    const { data: access } = await supabase
      .from("user_pfi_access")
      .select("pfi_id, pfi_instances(code)")
      .eq("user_id", user.id);

    return {
      type: "jwt",
      pfi_scope: pfiScope || access?.[0]?.pfi_instances?.code || "PF-CORE",
      permissions: ["*"],  // RLS handles fine-grained access
      actor_id: user.id,
    };
  }

  throw new Error("No valid authentication provided");
}
```

---

## 6. Data Exchange Patterns

### Pattern A: Claude Agent Reads Ontologies (DELTA Pipeline)

```
DELTA pfc-delta-scope skill
  → Claude calls pfc_query_ontologies({ pfi_code: "W4M-WWG", include_data: true })
  → MCP server → POST /functions/v1/ontologies/query
  → Edge Function authenticates (service key), enforces PFI scope
  → SELECT * FROM ontologies WHERE pfi_id = <W4M-WWG UUID>
  → Returns JSON-LD ontology payloads
  → Claude skill receives ontologies in tool result
  → Proceeds with analysis using ontology entities/relationships
```

### Pattern B: Claude Agent Saves Analysis Artifact

```
DELTA pfc-delta-narrate skill produces a narrative document
  → Claude calls pfc_write_artifact({
      pfi_code: "W4M-WWG",
      artifact_type: "analysis",
      name: "DELTA-Cycle-1-Narrative",
      version: "1.0.0",
      data: { /* narrative JSON-LD */ }
    })
  → Edge Function writes to pfc_registry + writes audit_log entry
  → Returns { id: "uuid", audit_id: 42 }
```

### Pattern C: Third-party BI Tool Exports Graph

```
External BI dashboard (e.g., Power BI, Looker)
  → GET /functions/v1/graph/export
      Headers: X-API-Key: <baiv-bi-key>
      Body: { format: "json-ld", scope: "STRATEGIC", categories: ["PRODUCT","COMPETITIVE"] }
  → Edge Function authenticates API key, checks permissions: ["read:ontologies"]
  → Calls emc-composer logic server-side (or pre-composed snapshot)
  → Returns composed graph in requested format
  → BI tool renders/analyses the data
```

### Pattern D: GitHub Actions → Supabase Registry Sync

```
On merge to main:
  → registry-deploy-prod.yml
  → Calls POST /functions/v1/sync/registry
      Headers: Authorization: Bearer <SUPABASE_SERVICE_KEY>
      Body: { artifacts: [ /* changed registry entries */ ] }
  → Edge Function upserts pfc_registry rows
  → Audit log: "registry_sync" action with commit SHA
```

---

## 7. Security Architecture Summary

### Authentication Matrix

| Consumer | Auth Method | Scope Enforcement | RLS Used? |
|---|---|---|---|
| Browser (Visualiser) | Supabase Auth JWT | RLS `auth.uid()` | Yes — primary |
| Claude Agent (MCP) | Service key + X-PFI-Scope | Edge Function enforced | No — bypassed, EF is the gate |
| Claude Code (CLI) | Personal access token / service key | CLI auth + human-supervised | No — management-plane access |
| Third-party (API key) | API key hash lookup | Edge Function + permission array | No — bypassed, EF is the gate |
| GitHub Actions | Service key | Hardcoded to PF-CORE scope | No — trusted CI |

### Secret Management

| Secret | Where Stored | Who Uses |
|---|---|---|
| `SUPABASE_URL` | `.env` / GitHub Secrets | All four layers |
| `SUPABASE_ANON_KEY` | Client-side (public) | Browser only |
| `SUPABASE_SERVICE_ROLE_KEY` | Server-side only, never client | MCP server, Edge Functions, CI |
| `SUPABASE_ACCESS_TOKEN` | Local shell env / `.env` | CLI (personal access token for `supabase` CLI) |
| `PFC_PFI_SCOPE` | MCP server env (per-agent) | MCP server launch config |
| API key (hashed) | `api_keys` table | Third-party consumers |

### Audit Trail

Every mutation through any layer writes to `audit_log`:

```json
{
  "user_id": "uuid-or-null",
  "pfi_id": "uuid",
  "action": "artifact_write",
  "resource": "pfc:analysis-delta-cycle-1-v1.0.0",
  "detail": {
    "actor_type": "agent",
    "actor_id": "agent:W4M-WWG",
    "source": "pfc-delta-narrate",
    "edge_function": "artifacts-write",
    "request_id": "req-uuid"
  }
}
```

### MCSB Alignment (Extended from MVP-Security-VSOM-v1.1.0)

| MCSB Domain | MVP (E10A) | This Proposal Adds |
|---|---|---|
| IM (Identity) | Email auth, JWT | API keys, service key scoping |
| PA (Privileged Access) | 4 roles, RLS | Edge Function PFI enforcement, permission arrays |
| DP (Data Protection) | RLS per PFI | PFI-scoped API keys, agent scope locking |
| LT (Logging) | append-only audit_log | Actor type tracking (user/agent/api_key) |
| NS (Network) | Supabase managed TLS | Edge Function rate limiting |
| GS (Governance) | PFI boundaries | API key lifecycle (expiry, revocation) |
| AM (Asset Management) | — | Registry artifact tracking via `pfc_registry` |

---

## 8. Interface Selection — Decision Framework

### When to Use MCP (Layer 2)

- Claude agent needs Supabase data **mid-conversation** (tool call within a skill)
- Agent is reasoning over ontology data and needs to query/save
- The 5 coarse-grained tools cover the use case

### When to Use CLI (Layer 3)

- Interactive Claude Code session — development, debugging, admin
- Running migrations, deploying Edge Functions, managing secrets
- Ad-hoc SQL queries, schema inspection, type generation
- Any management-plane operation (project setup, linking, local dev)

### When to Use REST API (Layer 4)

- Non-Claude consumer (BI tool, CI/CD, partner system)
- Bulk operations (registry sync, batch export)
- Operations that don't need LLM reasoning

### When to Use Browser (Layer 1)

- User authentication, profile management, PFI membership
- Admin operations (create PFI, manage users, view audit log)
- Any browser-based operation — use `supabase-js` + RLS directly

### The Anti-Pattern to Avoid

```
DON'T: Build a "Supabase MCP" with tools for every table
  ❌ supabase_select, supabase_insert, supabase_update, supabase_delete
  ❌ supabase_auth_signup, supabase_auth_login, supabase_auth_logout
  ❌ supabase_storage_upload, supabase_storage_download
  ❌ supabase_realtime_subscribe

  = 15+ tools = 8,000+ tokens of definitions = per every single request

DO: Build a domain MCP with 5 tools that cover agent use cases
  ✅ pfc_query_ontologies    (reads what agents need)
  ✅ pfc_write_artifact      (saves what agents produce)
  ✅ pfc_resolve_config      (cascade resolution)
  ✅ pfc_export_graph        (composed graph extraction)
  ✅ pfc_audit_query         (compliance visibility)

  = 5 tools = ~3,000 tokens = acceptable overhead
```

---

## 9. Implementation Phases

### Phase 1: Foundation (Unblocks E10A)

**Prerequisites:** Apply existing migrations 001 + 002 to Supabase `eccoai` project.

| Step | Deliverable | Depends On |
|---|---|---|
| 1.1 | Apply 001_create_schema.sql to eccoai | Supabase project access |
| 1.2 | Apply 002_rls_policies.sql | 1.1 |
| 1.3 | Run seed.sql (PF-CORE, BAIV-AIV, AIRL-AIR) | 1.2 |
| 1.4 | Verify RLS with test users | 1.3 |
| 1.5 | Create migration 003 (api_keys table) | 1.2 |

### Phase 2: Edge Functions (API Layer)

| Step | Deliverable | Depends On |
|---|---|---|
| 2.1 | Shared auth module (`_shared/auth.ts`) | Phase 1 |
| 2.2 | `ontologies-query` Edge Function | 2.1 |
| 2.3 | `artifacts-write` Edge Function | 2.1 |
| 2.4 | `registry-resolve` Edge Function | 2.1 |
| 2.5 | `graph-export` Edge Function | 2.1 |
| 2.6 | `audit-query` Edge Function | 2.1 |
| 2.7 | `health` Edge Function | None |
| 2.8 | `sync-registry` Edge Function | 2.1 |

### Phase 3: Supabase CLI (Claude Code Integration)

| Step | Deliverable | Depends On |
|---|---|---|
| 3.1 | Install Supabase CLI (`brew install supabase/tap/supabase`) | None |
| 3.2 | Authenticate CLI (`supabase login`) | 3.1 |
| 3.3 | Link to project (`supabase link --project-ref <ref>`) | 3.2, Phase 1 |
| 3.4 | Validate CLI access — run ad-hoc query via `supabase db execute` | 3.3 |
| 3.5 | Document CLI workflows in CLAUDE.md / operating guide | 3.4 |

### Phase 4: MCP Server (Automated Agent Integration)

| Step | Deliverable | Depends On |
|---|---|---|
| 4.1 | `pfc-supabase` MCP server scaffold (5 tools) | Phase 2 |
| 4.2 | Claude Code settings — register MCP server | 4.1 |
| 4.3 | DELTA pipeline skill updates — use MCP tools | 4.2 |
| 4.4 | Token budget validation (measure actual overhead) | 4.2 |

### Phase 5: Third-party Access

| Step | Deliverable | Depends On |
|---|---|---|
| 5.1 | API key generation UI (admin) | Phase 1.5, Phase 2.1 |
| 5.2 | Rate limiting middleware in Edge Functions | Phase 2 |
| 5.3 | API documentation (OpenAPI spec) | Phase 2 |
| 5.4 | Webhook integration pattern (auth-webhook EF) | Phase 2.1 |

---

## 10. Migration: pfc_registry Table

The `pfc_registry` table from the Unified Registry briefing needs a dedicated migration:

```sql
-- Migration 004: Unified Registry — Epic 34: PFC-VSOM (S1 Graph-First)

CREATE TABLE public.pfc_registry (
  id                UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  artifact_id       TEXT NOT NULL,
  artifact_type     TEXT NOT NULL,
  artifact_category TEXT,
  name              TEXT NOT NULL,
  version           TEXT NOT NULL,
  scope             TEXT NOT NULL CHECK (scope IN ('core','instance','product','client')),
  instance_id       TEXT,
  client_id         UUID,
  status            TEXT DEFAULT 'active' CHECK (status IN ('active','inactive','deprecated')),
  override_type     TEXT DEFAULT 'inherit' CHECK (override_type IN ('inherit','extend','override')),
  configuration     JSONB DEFAULT '{}',
  base_configuration JSONB DEFAULT '{}',
  components        JSONB DEFAULT '[]',
  dependencies      JSONB DEFAULT '[]',
  quality_gates     JSONB DEFAULT '{}',
  artifact_hash     TEXT,
  environment       TEXT NOT NULL DEFAULT 'production',
  deployed_at       TIMESTAMPTZ DEFAULT now(),
  created_at        TIMESTAMPTZ DEFAULT now(),
  updated_at        TIMESTAMPTZ DEFAULT now(),
  UNIQUE(artifact_id, scope, instance_id, client_id, environment)
);

-- GIN index for JSONB containment queries on configuration
CREATE INDEX idx_registry_config ON public.pfc_registry USING GIN (configuration);
CREATE INDEX idx_registry_type_scope ON public.pfc_registry (artifact_type, scope, status);

-- RLS: PFI-scoped read, admin write
ALTER TABLE public.pfc_registry ENABLE ROW LEVEL SECURITY;

-- SELECT: core artifacts visible to all authenticated; instance artifacts to PFI members
CREATE POLICY registry_read ON public.pfc_registry FOR SELECT USING (
  scope = 'core'
  OR EXISTS (SELECT 1 FROM public.profiles WHERE id = auth.uid() AND role = 'pf-owner')
  OR (
    scope = 'instance' AND instance_id IN (
      SELECT pi.code FROM public.pfi_instances pi
      JOIN public.user_pfi_access upa ON upa.pfi_id = pi.id
      WHERE upa.user_id = auth.uid()
    )
  )
);

-- Mutations: pf-owner and pfi-admin only
CREATE POLICY registry_write ON public.pfc_registry FOR INSERT WITH CHECK (
  EXISTS (
    SELECT 1 FROM public.profiles WHERE id = auth.uid()
    AND role IN ('pf-owner', 'pfi-admin')
  )
);

CREATE POLICY registry_update ON public.pfc_registry FOR UPDATE USING (
  EXISTS (
    SELECT 1 FROM public.profiles WHERE id = auth.uid()
    AND role IN ('pf-owner', 'pfi-admin')
  )
);

-- resolve_artifact_config() — cascade resolution function
CREATE OR REPLACE FUNCTION public.resolve_artifact_config(
  p_artifact_type TEXT,
  p_name TEXT,
  p_instance_id TEXT DEFAULT NULL,
  p_client_id UUID DEFAULT NULL
)
RETURNS JSONB AS $$
DECLARE
  core_config JSONB;
  instance_config JSONB;
  client_config JSONB;
  result JSONB;
BEGIN
  SELECT configuration INTO core_config FROM pfc_registry
    WHERE artifact_type = p_artifact_type AND name = p_name
    AND scope = 'core' AND status = 'active';
  result := COALESCE(core_config, '{}'::jsonb);

  IF p_instance_id IS NOT NULL THEN
    SELECT base_configuration INTO instance_config FROM pfc_registry
      WHERE artifact_type = p_artifact_type AND name = p_name
      AND scope = 'instance' AND instance_id = p_instance_id AND status = 'active';
    IF instance_config IS NOT NULL THEN
      result := result || instance_config;
    END IF;
  END IF;

  IF p_client_id IS NOT NULL THEN
    SELECT base_configuration INTO client_config FROM pfc_registry
      WHERE artifact_type = p_artifact_type AND name = p_name
      AND scope = 'client' AND client_id = p_client_id AND status = 'active';
    IF client_config IS NOT NULL THEN
      result := result || client_config;
    END IF;
  END IF;

  RETURN result;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## 11. Token Budget Analysis

### Baseline: Current DELTA Pipeline (No Supabase)

| Component | Tokens |
|---|---|
| System prompt + CLAUDE.md | ~3,000 |
| Skill definition (e.g., pfc-delta-scope) | ~2,000 |
| Existing MCP tools (Pencil, Figma, etc.) | ~8,000 |
| Conversation context | Variable |
| **Total fixed overhead** | **~13,000** |

### With Thin MCP Server (+5 Tools)

| Component | Added Tokens |
|---|---|
| 5 tool definitions (~600 each) | ~3,000 |
| **New total fixed overhead** | **~16,000** |
| **Increase** | **~23%** |

### If We'd Built a Full Supabase MCP (+25 Tools)

| Component | Added Tokens |
|---|---|
| 25 tool definitions (~600 each) | ~15,000 |
| **New total fixed overhead** | **~28,000** |
| **Increase** | **~115%** |

### With Supabase CLI (Layer 3)

| Component | Added Tokens |
|---|---|
| CLI via Bash tool (already loaded) | 0 |
| **New total fixed overhead** | **~13,000 (unchanged)** |
| **Increase** | **0%** |

The CLI approach adds zero tokens — it uses the Bash tool that is always present. The thin MCP approach adds ~3,000 tokens (23% increase). The fat MCP approach would add ~15,000 tokens (115% increase). At scale across DELTA pipeline turns (typically 15-30 agent turns per cycle), the difference is significant.

### Conditional Loading Option (Future)

Claude Code supports per-project MCP server configuration. The `pfc-supabase` MCP server can be conditionally loaded only when running Supabase-connected skills:

```json
// .claude/settings.json (per-project)
{
  "mcpServers": {
    "pfc-supabase": {
      "command": "node",
      "args": ["pfc-supabase-mcp/dist/server.js"],
      "env": {
        "SUPABASE_URL": "...",
        "SUPABASE_SERVICE_KEY": "...",
        "PFC_PFI_SCOPE": "BAIV-AIV"
      }
    }
  }
}
```

When running skills that don't need Supabase (e.g., `pfc-reason`, local ontology analysis), the MCP server doesn't load and its tools don't consume tokens.

---

## 12. Relationship to Existing Architecture

| Existing Asset | This Proposal | Interaction |
|---|---|---|
| Epic 10A migrations (001, 002) | Phase 1 applies them | Direct dependency |
| MVP-Security-VSOM-v1.1.0 | S1 Foundation = Phase 1-2 | Implements the VSOM |
| Unified Registry briefing | Phase 2.4, Migration 004 | `resolve_artifact_config()` goes live |
| Supabase CLI | Phase 3 | Zero-overhead Claude Code interface |
| DELTA skills (Epic 52) | Phase 4.3 | Skills gain Supabase data access |
| ADR-001 (Supabase-First) | Fully aligned | Implements the decision |
| ADR-005 (Shared RLS) | Phase 1 | RLS policies applied |
| Visualiser `supabase-js` | Layer 1 (unchanged) | Browser path stays as designed |
| `emc-composer.js` `exportAsJSONB()` | Layer 2 `pfc_export_graph` | Composed graphs exportable via API |
| PFI instance configs | `pfc_resolve_config` tool | Cascade resolution available to agents |

---

## 13. Database Distribution Cascade — PFC → PFI → Product/Client Instances

### The Distribution Problem

The platform operates a hierarchical instance model:

```
PFC (Platform Foundation Core)
 └── PFI (Platform Foundation Instance)
      ├── Product Instance (e.g. W4M-WWG product line)
      └── Client Instance (e.g. specific client deployment)
```

Each level inherits from the level above. Ontologies, configuration, GRC baselines, and schema definitions originate at PFC and cascade downward. The database architecture must support this cascade whilst keeping costs minimal in PoC/MVP, maintaining strict data isolation, and enabling phased sophistication.

### Environment Triads: Dev / Test / Prod

Every instance (PFC and each PFI) operates a dev/test/prod triad:

```
PFC.dev  →  PFC.test  →  PFC.prod
  │            │            │
  ▼            ▼            ▼
PFI.dev  →  PFI.test  →  PFI.prod
  │            │            │
  ▼            ▼            ▼
Product.dev → Product.test → Product.prod
```

**Hard rules (non-negotiable):**

| Rule | Enforcement |
| --- | --- |
| Dev environments NEVER contain live data | Schema + seed data only |
| Test environments NEVER contain live data | Synthetic/anonymised test data only |
| Production data ONLY stored in prod | RLS + environment isolation |
| No cross-environment data leakage | Separate Supabase projects per environment |
| All promotions are audited | `audit_log` entries for every stage gate |

### PoC/MVP Strategy: Shared Database Design, Phased Split

**Principle: Start with one database design, deploy per environment, split per instance only when proven.**

In early stages, a single schema design serves PFC and all PFIs. The `pfi_id` column in every table + RLS policies enforce logical isolation within the same physical database. This keeps costs to a single Supabase project per environment (3 total for PFC) rather than 3 × N projects.

```
Phase 1 (PoC/MVP): Single Supabase project per environment
┌─────────────────────────────────────────────┐
│  PFC.dev (Supabase project: eccoai-dev)     │
│  ┌──────────────────────────────────────┐   │
│  │  PostgreSQL                          │   │
│  │  pfi_id = 'PF-CORE' (PFC data)      │   │
│  │  pfi_id = 'BAIV-AIV' (BAIV data)    │   │
│  │  pfi_id = 'W4M-WWG' (W4M data)      │   │
│  │  RLS enforces: each PFI sees only    │   │
│  │  its own rows + PF-CORE shared data  │   │
│  └──────────────────────────────────────┘   │
└─────────────────────────────────────────────┘

Phase 2 (Scale): Dedicated Supabase project per PFI (when needed)
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│  PFC.prod    │  │  BAIV.prod   │  │  W4M.prod    │
│  PF-CORE     │  │  BAIV-AIV    │  │  W4M-WWG     │
│  + shared    │  │  + cascaded  │  │  + cascaded  │
│  ontologies  │  │  ontologies  │  │  ontologies  │
└──────────────┘  └──────────────┘  └──────────────┘
```

**Cost benefit:** PoC/MVP runs on 3 Supabase projects (dev/test/prod) rather than 15+ (3 envs × 5+ PFIs). Once patterns are proven, split is a controlled migration — schema is identical, only data moves.

### Cascade Distribution Model

Ontologies and shared configuration flow from PFC → PFI on a controlled, audited basis:

#### Option A: Centralised Ontology Store (Recommended for PoC/MVP)

Ontologies remain in PFC database only. PFIs query PFC for ontology data via Edge Functions or CLI. No ontology duplication.

**Pros:** Single source of truth, no sync drift, minimal storage cost
**Cons:** PFI queries depend on PFC availability, network latency for reads

```
PFI Agent needs ontology data
  → Calls pfc_query_ontologies({ pfi_code: "BAIV-AIV" })
  → Edge Function reads from PFC.prod (centralised)
  → Returns relevant ontologies filtered by PFI scope
  → PFI caches locally if needed (read-only, TTL-based)
```

#### Option B: Cascaded Distribution (Phase 2+)

PFC publishes ontology snapshots to PFI databases on a scheduled or event-driven basis. Each PFI holds a read-only copy.

**Pros:** PFI independence, lower latency, offline-capable
**Cons:** Sync complexity, version drift risk, higher storage cost

**Decision: Start with Option A. Move to Option B only when PFI count or latency demands it.**

### Schema Minimisation Principle

Graphs store the detail — not relational tables. The database schema stays minimal:

| Table | Purpose | Contains |
| --- | --- | --- |
| `ontologies` | Ontology registry | Metadata + JSONB graph payload |
| `pfc_registry` | Artefact registry | Config cascade, versions, quality gates |
| `audit_log` | Immutable audit trail | Every mutation, every actor |
| `profiles` | User identity | Auth, roles, PFI membership |
| `pfi_instances` | Instance registry | PFI codes, status, config |
| `api_keys` | Third-party access | Hashed keys, permissions, expiry |

Custom PFI-specific data, product-specific graphs, client-specific analysis outputs — all stored as **JSONB inside `ontologies` or `pfc_registry`**. The schema does not grow per-PFI. The graph data inside JSONB is the domain model; the relational schema is the access control and audit layer.

**Baseline, good, and bad examples** should be stored as JSONB graph instances within `pfc_registry` (artifact_type = 'test-fixture') so that functions and features can be tested and quality-gated against known-good and known-bad data.

---

## 14. Data Lifecycle Management

### Instance States

| State | Description | Data Action |
| --- | --- | --- |
| `provisioning` | New PFI being set up | Schema applied, seed data loaded, no user data |
| `active` | Normal operation | Full read/write, audit logging active |
| `suspended` | Temporarily disabled (non-payment, compliance review) | Read-only access for PFI admins, all mutations blocked, data retained |
| `terminating` | Decommissioning initiated | Data export offered, countdown to deletion |
| `terminated` | Fully decommissioned | All PFI data purged, audit log retained (regulatory hold period) |
| `archived` | Long-term hold (legal/regulatory) | Encrypted cold storage, no active access, retrievable by PFC admin only |

### Suspension Protocol

1. PFC admin sets instance status to `suspended` via `pfi_instances.status`
2. RLS policies check instance status — suspended instances block all INSERT/UPDATE/DELETE
3. PFI admin retains read-only access to export data
4. Audit log records suspension event with reason and initiator
5. Automated notification to PFI admin contacts

### Termination Protocol

1. PFC admin initiates termination — sets `terminating` status
2. 30-day grace period (configurable) — PFI admin can export all data
3. Data export provided as encrypted archive (JSONB graphs + audit log subset)
4. After grace period: all PFI-scoped rows deleted from `ontologies`, `pfc_registry`, `profiles`, `api_keys`
5. `audit_log` entries retained for regulatory hold period (default: 7 years)
6. Audit log entry records termination completion with row counts and hash of deleted data
7. Instance status set to `terminated` — PFI code reserved (never reused)

### Kill-Switch Mechanism

For emergency scenarios (breach detected, legal order, compromised credentials):

```sql
-- Emergency: immediately revoke all access for a PFI
-- Step 1: Suspend instance (blocks all RLS-gated operations)
UPDATE pfi_instances SET status = 'suspended', suspended_at = now(),
  suspend_reason = 'emergency:security_breach' WHERE code = 'BAIV-AIV';

-- Step 2: Revoke all API keys
UPDATE api_keys SET expires_at = now() WHERE pfi_id = (
  SELECT id FROM pfi_instances WHERE code = 'BAIV-AIV'
);

-- Step 3: Invalidate all user sessions (Supabase Auth)
-- Via Supabase CLI: supabase auth admin delete-sessions --project-ref <ref>

-- Step 4: Audit log the emergency action
INSERT INTO audit_log (pfi_id, action, resource, detail) VALUES (
  (SELECT id FROM pfi_instances WHERE code = 'BAIV-AIV'),
  'emergency_suspend', 'pfi:BAIV-AIV',
  '{"reason":"security_breach","initiated_by":"pfc-admin","timestamp":"..."}'::jsonb
);
```

This can be executed via Supabase CLI (`supabase db execute`) for immediate response.

---

## 15. RBAC Model — PFC, PFI, Product & Client Roles

### Role Hierarchy

```
PFC Level
  pf-owner          Full platform access, all PFIs, all environments
  pfc-admin          PFC admin operations, PFI provisioning, GRC oversight
  pfc-developer      PFC dev/test environments, ontology development, no prod data

PFI Level
  pfi-admin          Full access within their PFI (all environments)
  pfi-developer      PFI dev/test only, no prod access
  pfi-analyst        PFI prod read-only (reports, dashboards, no mutations)

Product/Client Level
  product-admin      Manages product instance within a PFI
  client-user        End-user access scoped to their client instance
  client-readonly    View-only access to client-scoped data
```

### RLS Policy Pattern (Progressive)

```sql
-- Base RLS: PFI scoping (already in place from E10A)
CREATE POLICY pfi_isolation ON ontologies FOR ALL USING (
  -- PF-CORE data visible to all authenticated users
  pfi_id = (SELECT id FROM pfi_instances WHERE code = 'PF-CORE')
  -- PFI-specific data visible only to PFI members
  OR pfi_id IN (
    SELECT pfi_id FROM user_pfi_access WHERE user_id = auth.uid()
  )
);

-- Phase 2: Environment enforcement
-- Profiles carry an 'environment_access' array: ['dev','test','prod']
-- RLS checks environment column against user's allowed environments

-- Phase 3: Product/Client scoping
-- product_id and client_id columns added to relevant tables
-- RLS checks product/client membership via join table
```

**Phasing principle:** Phase 1 (PoC/MVP) uses PFI-level isolation only. Product/client sub-isolation is added when the first multi-product PFI is deployed.

### Preventing Unauthorised Access

| Threat | Mitigation |
| --- | --- |
| Unauthorised database copy/export | Service key restricted to Edge Functions + CLI (env-scoped), no direct DB connection strings exposed |
| Unauthorised data deletion | Delete operations require `pf-owner` or `pfi-admin` role, all deletes audit-logged |
| Unauthorised data movement | No cross-PFI data transfer functions exist; Edge Functions enforce PFI scope |
| Privilege escalation | Roles stored in `profiles` table, RLS checks role on every query, role changes require `pf-owner` |
| Credential compromise | API keys have expiry dates, service keys rotated on schedule, emergency kill-switch available |
| Developer accessing prod data | Environment-scoped credentials: dev service keys cannot reach prod Supabase project |

---

## 16. GRC Baseline — Security by Design, Privacy by Design

### Foundational Principles

1. **Security by Design** — security controls are architectural, not bolt-on
2. **Privacy by Design** — PII protection is structural, not procedural
3. **Least Privilege** — every role, key, and function has minimum necessary access
4. **Defence in Depth** — RLS + Edge Function scoping + API key permissions + audit logging
5. **Traceability** — every data mutation has an audit trail linking actor, action, resource, and timestamp
6. **Data Minimisation** — collect only what is needed; graphs store domain data, relational tables store access control

### PII Protection

| Control | Implementation |
| --- | --- |
| PII identification | Tag columns containing PII in schema documentation; `profiles.email`, `profiles.display_name` |
| Encryption at rest | Supabase encrypts all data at rest (AES-256, managed by Supabase infrastructure) |
| Encryption in transit | TLS 1.3 enforced on all connections (Supabase managed) |
| PII in JSONB graphs | PII MUST NOT be stored in ontology graphs; graph entities reference profile UUIDs, never names/emails |
| Anonymisation for test | Test environments use synthetic data generated from anonymisation functions |
| Right to erasure | User deletion cascades via `ON DELETE CASCADE` on `user_pfi_access`; profile pseudonymised in audit log |
| Breach response | Kill-switch suspends PFI; encrypted backup enables recovery; audit log preserved for forensics |

### Encryption & Anonymisation Strategy

```sql
-- PII columns use pgcrypto for application-level encryption where needed
-- beyond Supabase's at-rest encryption

-- Anonymisation function for test data generation
CREATE OR REPLACE FUNCTION anonymise_profile(p_user_id UUID)
RETURNS VOID AS $$
BEGIN
  UPDATE profiles SET
    email = 'anon-' || gen_random_uuid() || '@test.pfc.local',
    display_name = 'Test User ' || substr(gen_random_uuid()::text, 1, 8)
  WHERE id = p_user_id;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### Jurisdictional GRC Requirements

Each PFI instance declares its jurisdictional context in `pfi_instances`:

```sql
-- Added to pfi_instances table
ALTER TABLE pfi_instances ADD COLUMN jurisdiction JSONB DEFAULT '{}'::jsonb;
-- Example: {"primary": "UK", "regulations": ["GDPR", "DSPT", "NCSC-CAF"], "data_residency": "eu-west-2"}
```

The `jurisdiction` field drives:

- Which GRC ontologies (RCSG-Series) are mandatory for that PFI
- Data residency requirements (Supabase project region selection)
- Regulatory reporting obligations
- Retention periods for audit log data

### MCSB Baseline for Current Architecture

| MCSB Domain | PoC/MVP Baseline | Phase 2+ Enhancement |
| --- | --- | --- |
| IM (Identity) | Supabase Auth, email/password, JWT | MFA, SSO/SAML for enterprise PFIs |
| PA (Privileged Access) | 4 PFC roles + 3 PFI roles, RLS enforcement | Product/client sub-roles, just-in-time access |
| NS (Network Security) | Supabase managed TLS, Edge Functions as gateway | IP allowlisting per PFI, VPC peering for enterprise |
| DP (Data Protection) | AES-256 at rest, TLS in transit, PII exclusion from graphs | Column-level encryption for sensitive fields, key rotation |
| LT (Logging & Threat Detection) | Append-only `audit_log`, actor tracking | SIEM integration, anomaly detection on access patterns |
| IR (Incident Response) | Kill-switch, backup recovery | Automated breach detection, regulatory notification workflow |
| PS (Posture & Vulnerability) | Schema validation, RLS policy testing | Automated security scanning, dependency audit |
| GS (Governance & Strategy) | PFI boundaries, API key lifecycle, jurisdictional config | Compliance dashboards, automated GRC assessment via ontologies |
| AM (Asset Management) | `pfc_registry` tracks all artefacts | Automated drift detection, version compliance checking |
| BR (Backup & Recovery) | Supabase automatic backups, point-in-time recovery | Cross-region backup for enterprise PFIs, tested restore procedures |

---

## 17. Postgres as Persistent Second Brain

### The Problem with Claude Memory

Claude's memory (files, conversation context) is:

- **Transient** — context windows are finite, conversations expire
- **Local** — memory files are machine-specific, not shared across team members
- **Unstructured** — markdown files, not queryable

### Postgres as the Durable Knowledge Layer

The Supabase Postgres database becomes the persistent second brain for PFC and all PFIs:

| Knowledge Type | Storage | Query Pattern |
| --- | --- | --- |
| Ontology definitions | `ontologies.data` (JSONB) | EMC composition, graph traversal |
| Registry artefacts | `pfc_registry.configuration` (JSONB) | Cascade resolution, version lookup |
| Audit history | `audit_log.detail` (JSONB) | Compliance queries, actor tracing |
| PFI state & config | `pfi_instances` + jurisdiction JSONB | Instance management, GRC requirements |
| Analysis outputs | `pfc_registry` (artifact_type = 'analysis') | DELTA pipeline results, trend tracking |

Claude agents query this knowledge via CLI (`supabase db execute`) or MCP tools (`pfc_query_ontologies`). The database is the authoritative source; Claude memory files are a cache/index for fast context loading.

### Sensitive Data in the Second Brain

| Data Sensitivity | Storage Rule | Access Control |
| --- | --- | --- |
| Ontology definitions | JSONB in `ontologies` | PFI-scoped RLS, read by all PFI members |
| User profiles (PII) | `profiles` table | RLS: user sees own profile, admin sees PFI members |
| API keys | `api_keys` table (hashed) | RLS: admin-only, never exposed in query results |
| Client-specific data | JSONB in `pfc_registry` with client_id | RLS: client-scoped, product-admin and above |
| Audit log (contains actor IDs) | `audit_log` | RLS: pfc-admin and pfi-admin only |
| Jurisdictional config | `pfi_instances.jurisdiction` | RLS: pfc-admin manages, pfi-admin reads own |

---

## 18. Phased Development Principles

### Guiding Rules

1. **PoC/MVP: Keep costs to a minimum** — shared database design, 3 Supabase projects max (dev/test/prod)
2. **Do not over-engineer** — build for the current requirement, design for the next phase
3. **Every phase must be traceable** — RRR ontology tracks Risk → Requirement → Result for each capability
4. **Quality gates before promotion** — test fixtures (good/bad/baseline JSONB graphs) validate every feature
5. **Security is never deferred** — RLS, audit logging, and PII protection are Phase 1, not "nice to have"

### Phase Roadmap

| Phase | Focus | Database Config | Cost |
| --- | --- | --- | --- |
| P1: PoC | PFC-only, prove schema + RLS + CLI + Edge Functions | 1 Supabase project (free tier) | $0/mo |
| P2: MVP | PFC dev/test/prod triads, first PFI (BAIV-AIV) | 3 Supabase projects (free/pro) | $0-75/mo |
| P3: Multi-PFI | BAIV, W4M, AIRL active, shared DB with RLS isolation | 3 Supabase projects (pro) | ~$75/mo |
| P4: Scale | Dedicated Supabase projects per PFI where needed | 3 + N projects | Variable |
| P5: Enterprise | VPC peering, SSO, cross-region backup, SIEM | Enterprise Supabase | Negotiated |

### RRR Progression — Database Capabilities

Each capability maps to RRR (Risk → Requirement → Result):

| Risk | Requirement | Result | Phase |
| --- | --- | --- | --- |
| Data isolation failure between PFIs | RLS policies enforce `pfi_id` scoping on every table | Verified by test fixtures: PFI A cannot read PFI B data | P1 |
| Unauthorised access to production data | Environment separation: separate Supabase projects per env | Dev/test credentials cannot reach prod project | P2 |
| PII data breach | PII excluded from JSONB graphs; profiles table encrypted at rest | Penetration test confirms no PII leakage via API | P2 |
| Ontology version drift across PFIs | Centralised ontology store in PFC, PFIs query via Edge Functions | Version consistency verified across all PFI queries | P2 |
| Instance termination data residue | Termination protocol purges all PFI rows, audit log retained | Post-termination scan confirms zero PFI data rows | P3 |
| Cross-jurisdiction compliance failure | Jurisdictional JSONB config drives GRC ontology selection | Automated compliance check validates jurisdiction-specific controls | P3 |
| Credential compromise | Kill-switch + API key expiry + session revocation | Time-to-revoke < 60 seconds via CLI | P2 |
| Audit trail tampering | `audit_log` table: append-only, no UPDATE/DELETE policies | RLS test confirms no mutation path exists for any role | P1 |

### PBS (Product Breakdown Structure) — Database & GRC Layer

This section establishes the PBS elements for tracking database and GRC development:

```
PBS-DB: Database Layer
  PBS-DB-001: Schema design (core tables, RLS policies)
  PBS-DB-002: Environment triads (dev/test/prod separation)
  PBS-DB-003: Distribution cascade (PFC → PFI ontology flow)
  PBS-DB-004: Data lifecycle (provisioning → termination)
  PBS-DB-005: Backup & recovery procedures

PBS-GRC: Governance, Risk & Compliance Layer
  PBS-GRC-001: MCSB baseline architecture
  PBS-GRC-002: RBAC model (PFC/PFI/Product/Client roles)
  PBS-GRC-003: PII protection & anonymisation
  PBS-GRC-004: Jurisdictional compliance config
  PBS-GRC-005: Audit & control framework
  PBS-GRC-006: Kill-switch & incident response
  PBS-GRC-007: Quality gate test fixtures (good/bad/baseline graphs)
```

These PBS elements will map to Epics → Features → Stories as capabilities are developed and tested.

---

## 19. Decision Points for Review

| # | Decision | Options | Recommendation |
|---|---|---|---|
| D1 | MCP server runtime | Node.js / Deno / Python | Node.js (matches existing tooling) |
| D2 | Edge Function deploy | Supabase CLI / GitHub Actions | Both valid — CLI for dev, GH Actions for CI |
| D3 | API key format | UUID / JWT / opaque token | Opaque token (bcrypt-hashed, simple) |
| D4 | Rate limiting | Edge Function / Supabase middleware / external | Edge Function (no extra infra) |
| D5 | Graph export formats | JSON-LD only / + CSV / + RDF | JSON-LD + CSV (covers BI tools) |
| D6 | MCP server loading | Always-on / conditional per skill | Conditional (token-efficient) |
| D7 | CLI auth method | Personal access token / service role key | PAT for interactive, service key for scripts |
| D8 | Ontology distribution | Centralised (PFC-only) / Cascaded (replicated to PFIs) | Centralised for PoC/MVP (Option A) |
| D9 | Database topology | Shared DB with RLS / Dedicated DB per PFI | Shared with RLS for PoC/MVP, split at P4 |
| D10 | PII in JSONB graphs | Allow with encryption / Exclude entirely | Exclude — graphs reference UUIDs only |
| D11 | Audit log retention | 1yr / 5yr / 7yr / indefinite | 7 years (regulatory default, configurable per jurisdiction) |
| D12 | Kill-switch scope | Per-PFI / Per-product / Per-client | Per-PFI (PoC/MVP), per-product (Phase 3+) |
| D13 | Test data strategy | Anonymised prod copy / Fully synthetic | Fully synthetic (no prod data ever in dev/test) |
| D14 | Instance termination grace period | 14 / 30 / 90 days | 30 days (configurable per PFI contract) |

---

_Proposal: Supabase Secure Connections — CLI, API & MCP Architecture v1.2.0_
_Azlan-EA-AAA Platform Foundation_
_12 March 2026_

