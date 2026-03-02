# PLAN: Supabase JSONB MVP Platform Phase

**Version:** 2.0.0
**Date:** 2026-03-02
**Status:** PROPOSAL
**Scope:** Strategic platform phase — Supabase-hosted graph library, workbench security, RBAC, APIs, CI/CD database topology
**Excludes:** Neo4j, sovereign self-hosting (Epic 53), Next.js migration, real-time subscriptions

---

## 1. Strategic Vision

### The Trigger Chain

Securing the workbench is not a standalone task — it pulls an entire platform layer into existence:

```
Workbench Login Required
  → Need Authentication (Supabase Auth)
    → Need User Identity (profiles table)
      → Need PFI Scoping (pfi_instances + user_pfi_access)
        → Need RBAC (RRR-ONT role → AccessPolicy → Permission)
          → Need Ontology Storage (JSONB in Supabase)
            → Need Graph Access Control (RLS per PFI tier)
              → Need CI/CD Database Distribution (PFC hub → PFI spoke DBs)
```

Every link in this chain is a dependency — you cannot secure the workbench without building the platform beneath it. This plan treats that chain as the delivery backbone.

### VE Skills Chain Alignment

This plan follows the VE lineage as its strategic spine:

| VE Stage | Platform Equivalent | This Plan |
|----------|-------------------|-----------|
| **VSOM** — Vision, Strategy | Platform vision: graph-first, multi-PFI | Section 1 (this) |
| **RRR** — Roles, RBAC, RACI | Who accesses what, at which tier | Section 3 (RBAC layers) |
| **OKR/KPI** — Objectives, Metrics | Success metrics and phase gates | Section 8 (gates) |
| **VP** — Value Proposition | What each PFI instance gains | Section 5 (cascade) |
| **EFS** — Epics, Features, Stories | Delivery units organised by cascade tier | Section 6 (phased delivery) |
| **PPM** — Portfolio, Programme, Project | Scheduling, dependencies, cost | Section 7 (database topology) |

### What Already Exists (Artefacts in Hand)

| Artefact | Status | Location |
|----------|--------|----------|
| Migration 001 — 5-table schema | SQL written | `supabase/migrations/001_create_schema.sql` |
| Migration 002 — RLS policies | SQL written | `supabase/migrations/002_rls_policies.sql` |
| Migration 005 — pg_graphql + JSONB views + composition functions | SQL written | `supabase/migrations/005_graphql_layer.sql` |
| Seed data — 3 PFI instances | SQL written | `supabase/seed.sql` |
| GraphQL architecture briefing | Complete | `BRIEFING-GraphQL-Supabase-JSONB-MVP.md` |
| Secure Connections proposal (3-layer, 5-tool MCP) | Complete | `PROPOSAL-Supabase-Secure-Connections-API-MCP-v1.0.0.md` |
| RRR-ONT v4.0.0 — RBAC entities | Live | `VE-Series/RRR-ONT/` |
| App Skeleton v2.0.0 — 22 zones, 7 nav layers, 62 actions | Live | `pfc-app-skeleton-v1.0.0.jsonld` |
| Hub-and-spoke CI/CD architecture | Documented, partially implemented | `PBS/ARCHITECTURE/arch-cicd/` |
| EMC-ONT v5.1.0 — 4-tier cascade | Live | `Orchestration/EMC-ONT/` |
| Ontology library — 52 ontologies, registry v10.9.1 | Live on GitHub Pages | `ontology-library/` |

---

## 2. Architecture Overview

```mermaid
flowchart TB
    subgraph "Strategic Layer (VSOM)"
        VIS["Vision: Graph-First<br/>Multi-PFI Platform"]
    end

    subgraph "Identity & Access Layer (RRR-ONT RBAC)"
        AUTH["Supabase Auth<br/>Email + JWT"]
        ROLES["4 Platform Roles<br/>pf-owner | pfi-admin<br/>pfi-member | viewer"]
        RBAC["rbac:AccessPolicy<br/>→ rbac:Permission<br/>(action × resource × effect)"]
        RLS["Row-Level Security<br/>PFI boundary enforcement"]
    end

    subgraph "Application Layer (Skeleton Zones)"
        Z1["Z1 App Shell"]
        Z2["Z2 Toolbar / Z2-dyn Nav"]
        Z3["Z3 Context Identity<br/>(PFI tier)"]
        Z5["Z5 Graph Canvas"]
        Z8["Z8 Library Panel"]
        Z22["Z22 Skeleton Inspector"]
        ZN["Z-{PFI}-nnn<br/>Instance zones"]
    end

    subgraph "Data Layer (EMC Cascade)"
        PFC_DB["PFC Hub Database<br/>Schema template + PF-CORE ontologies"]
        PFI_DB["PFI Spoke Databases<br/>Instance-scoped ontologies"]
        JSONB["ontologies table<br/>JSONB data column"]
        VIEWS["3 projection views<br/>entities | rels | rules"]
        CG["composed_graphs<br/>Cached compositions"]
    end

    subgraph "Distribution Layer (CI/CD)"
        HUB["PFC-Core Forge<br/>(Azlan-EA-AAA)"]
        SPOKE["PFI Triads<br/>dev → test → prod"]
    end

    VIS --> AUTH
    AUTH --> ROLES --> RBAC --> RLS
    RLS --> JSONB
    RBAC -.->|"zone visibility<br/>conditions"| Z3 & Z22 & ZN
    JSONB --> VIEWS --> CG
    PFC_DB -->|"schema + seed"| PFI_DB
    HUB -->|"pfc-release.yml"| SPOKE
    SPOKE -->|"migrations"| PFI_DB

    style VIS fill:#e8eaf6
    style AUTH fill:#e3f2fd
    style ROLES fill:#e3f2fd
    style RBAC fill:#e3f2fd
    style RLS fill:#e3f2fd
    style JSONB fill:#e8f5e9
    style PFC_DB fill:#fff3e0
    style PFI_DB fill:#fff3e0
```

---

## 3. RBAC & Graph Access Control Layers

### 3.1 RRR-ONT RBAC Resolution Chain

The existing RRR-ONT v4.0.0 defines the complete RBAC resolution path:

```mermaid
flowchart LR
    USER["schema:Person<br/>(Supabase Auth user)"]
    RA["pf:RoleAssignment<br/>assignmentType: Primary|Acting|..."]
    ROLE["pf:ExecutiveRole<br/>or pf:FunctionalRole"]
    AP["rbac:AccessPolicy<br/>priority: N, isActive: true"]
    PERM["rbac:Permission<br/>action × resource × effect"]

    USER -->|"fills"| RA -->|"role"| ROLE -->|"governedBy"| AP -->|"grantsPermission"| PERM

    style USER fill:#e3f2fd
    style PERM fill:#ffebee
```

### 3.2 Four-Layer Graph Access Control

Graph access is controlled at four nested layers, from coarsest to finest:

```mermaid
flowchart TD
    subgraph "Layer 1: Platform Role (RLS)"
        L1["pf-owner → all PFIs<br/>pfi-admin → own PFI(s)<br/>pfi-member → own PFI(s), no DELETE<br/>viewer → read-only"]
    end

    subgraph "Layer 2: PFI Instance Scope (EMC)"
        L2["user_pfi_access table<br/>Many-to-many user ↔ PFI<br/>RLS: ontologies.pfi_id IN user's PFIs"]
    end

    subgraph "Layer 3: Ontology Selection (EMC Composition)"
        L3["instanceOntologies array<br/>constrainToInstanceOntologies()<br/>PFI declares which of 52 ontologies it uses"]
    end

    subgraph "Layer 4: Entity/Graph Scope (Graph-Scope Rules)"
        L4["GraphScopeRule + ScopeCondition<br/>Product-level and client-level filtering<br/>ComposedGraphSpec → CanonicalSnapshot"]
    end

    L1 -->|"Who are you?"| L2
    L2 -->|"Which PFI?"| L3
    L3 -->|"Which ontologies?"| L4
    L4 -->|"Which entities/edges?"| GRAPH["Rendered Graph<br/>in Workbench"]

    style L1 fill:#ffebee
    style L2 fill:#fff3e0
    style L3 fill:#e8f5e9
    style L4 fill:#e3f2fd
```

### 3.3 RBAC Matrix — Actions × Resources × Roles

| Resource | Action | pf-owner | pfi-admin | pfi-member | viewer |
|----------|--------|:--------:|:---------:|:----------:|:------:|
| **ontologies** | read (own PFI) | Yes | Yes | Yes | Yes |
| **ontologies** | read (PF-CORE shared) | Yes | Yes | Yes | Yes |
| **ontologies** | read (other PFI) | Yes | No | No | No |
| **ontologies** | create / update | Yes | Yes | Yes | No |
| **ontologies** | delete | Yes | Yes | No | No |
| **composed_graphs** | read | Yes | Yes | Yes | Yes |
| **composed_graphs** | create | Yes | Yes | Yes | No |
| **composed_graphs** | delete | Yes | Yes | No | No |
| **audit_log** | read | Yes | Own PFI | No | No |
| **user_pfi_access** | manage | Yes | Own PFI | No | No |
| **pfi_instances** | create / update | Yes | No | No | No |
| **Skeleton zones** | configure PFI zones | Yes | Yes | No | No |
| **Nav items** | add L4+ PFI items | Yes | Yes | No | No |

### 3.4 Skeleton Zone Visibility by Role & Tier

The Application Skeleton's 22 zones have cascade tiers and visibility conditions that map directly to RBAC:

| Zone | Name | Cascade Tier | Visibility by Role |
|------|------|-----------:|-------------------|
| Z1 | App Shell | PFC | All roles |
| Z2 | Toolbar (static) | PFC | All roles |
| Z2-dyn | Dynamic Nav Bar | PFC | All roles (replaces Z2) |
| Z3 | Context Identity Bar | **PFI** | All authenticated (shows current PFI) |
| Z4 | Left Sidebar | PFC | All roles |
| Z5 | Graph Canvas | PFC | All roles |
| Z6 | Right Detail Panel | PFC | All roles |
| Z7 | Footer Status Bar | PFC | All roles |
| Z8 | Library Panel | PFC | member+ (contains edit actions) |
| Z9 | Diff/Changelog | PFC | All roles |
| Z10 | Export Panel | PFC | member+ |
| Z11 | Minimap | PFC | All roles |
| Z14 | Toast Notifications | PFC | All roles |
| Z16 | Context Drawer | **PFI** | admin+ (PFI config) |
| Z17 | Audit Panel | PFC | admin+ |
| Z18 | Search Overlay | PFC | All roles |
| Z19 | Command Palette | PFC | All roles |
| Z20 | Settings Drawer | PFC | All roles (scoped per role) |
| Z22 | Skeleton Inspector | PFC | pf-owner only |
| Z-{PFI}-nnn | Instance zones | **PFI** | Per PFI config |

**Key principle:** PFC zones are immutable — PFI instances cannot modify them. PFI instances extend via Z3 (identity bar), Z16 (context drawer), and custom Z-{PFI}-nnn zones. Zone visibility conditions reference `state.userRole` and `state.activePFI`.

---

## 4. EMC Cascade: PFC → PFI → Product → Client

### 4.1 Four-Tier Data Architecture

```mermaid
flowchart TD
    subgraph "Tier 0: PFC (Schema Forge)"
        T0["52 ontology SCHEMAS<br/>No instance data<br/>EMC composition rules<br/>App skeleton (22 zones)<br/>Shared schema template"]
    end

    subgraph "Tier 1: PFI Instance"
        T1_BAIV["BAIV-AIV<br/>16 agents, B2B<br/>7 instanceOntologies"]
        T1_W4M["W4M-WWG<br/>Supply-demand<br/>7 instanceOntologies"]
        T1_AIRL["AIRL-CAF-AZA<br/>Azure compliance<br/>6 instanceOntologies"]
    end

    subgraph "Tier 2: Product (Future)"
        T2["ProductConfiguration<br/>Sub-graph within PFI<br/>e.g. BAIV-AIV + BAIV-CompIntel"]
    end

    subgraph "Tier 3: Client (Future)"
        T3["ClientGraphScope (B2B)<br/>CustomerSegmentScope (B2C)<br/>Per-client org-context filter"]
    end

    T0 -->|"schema + seed<br/>via CI/CD"| T1_BAIV & T1_W4M & T1_AIRL
    T1_BAIV -->|"product filter"| T2
    T2 -->|"client/segment filter"| T3

    style T0 fill:#e8eaf6
    style T1_BAIV fill:#e8f5e9
    style T1_W4M fill:#e8f5e9
    style T1_AIRL fill:#e8f5e9
    style T2 fill:#fff3e0
    style T3 fill:#ffebee
```

### 4.2 How RBAC Maps to Cascade Tiers

| Cascade Tier | RBAC Enforcement | Mechanism |
|-------------|-----------------|-----------|
| Tier 0 (PFC) | pf-owner only for schema changes | GitHub repo permissions + guard-core.yml |
| Tier 1 (PFI) | pfi-admin manages instance config, member reads/writes ontologies | Supabase RLS on pfi_id |
| Tier 2 (Product) | Future: product-scoped AccessPolicy | Graph-Scope Rules (EMC v6.0.0) |
| Tier 3 (Client) | Future: client org-context filter | ClientGraphScope entity (Gap 2) |

### 4.3 Org-Context Integration

Org-context flows through the cascade at every tier:

```
PFC: org:Organisation (abstract schema)
  → PFI: org:Organisation (instance data — e.g. "Acme Insurance")
    → Product: org:BusinessUnit (filtered to product scope)
      → Client: org:ClientOrganisation (B2B) or org:CustomerSegment (B2C)
```

Zone Z3 (Context Identity Bar) displays the active org-context. Zone Z16 (Context Drawer) allows admin to configure PFI-specific org-context settings. Both are at PFI cascade tier.

---

## 5. Database Topology: PFC Hub + PFI Spoke Pattern

### 5.1 The Core Decision

Databases follow the same hub-and-spoke model as CI/CD:

```mermaid
flowchart TB
    subgraph "PFC Hub (Schema Forge)"
        PFC_S["Supabase Project: pfc-core<br/>Purpose: Schema template + PF-CORE data<br/>Contains: 52 ontology schemas (Tier 0)<br/>Cost: Free tier"]
    end

    subgraph "PFI Spoke: BAIV"
        BAIV_DEV["supabase-baiv-dev<br/>Free tier<br/>Break things freely"]
        BAIV_TEST["supabase-baiv-test<br/>Free tier<br/>SME validation"]
        BAIV_PROD["supabase-baiv-prod<br/>Free tier → Pro when needed<br/>Source of truth"]
    end

    subgraph "PFI Spoke: W4M-WWG"
        W4M_DEV["supabase-w4m-dev"]
        W4M_TEST["supabase-w4m-test"]
        W4M_PROD["supabase-w4m-prod"]
    end

    subgraph "PFI Spoke: AIRL"
        AIRL_DEV["supabase-airl-dev"]
        AIRL_TEST["supabase-airl-test"]
        AIRL_PROD["supabase-airl-prod"]
    end

    PFC_S -->|"schema migrations<br/>via pfc-release.yml"| BAIV_DEV & W4M_DEV & AIRL_DEV
    BAIV_DEV -->|"promote.yml"| BAIV_TEST -->|"promote.yml"| BAIV_PROD
    W4M_DEV -->|"promote.yml"| W4M_TEST -->|"promote.yml"| W4M_PROD
    AIRL_DEV -->|"promote.yml"| AIRL_TEST -->|"promote.yml"| AIRL_PROD

    style PFC_S fill:#e8eaf6
    style BAIV_PROD fill:#e8f5e9
    style W4M_PROD fill:#e8f5e9
    style AIRL_PROD fill:#e8f5e9
```

### 5.2 Cost-Effective Phasing

**Phase A (MVP — now):** Minimal cost, maximum learning

| Database | Tier | Purpose | Cost |
|----------|------|---------|------|
| pfc-core | Hub | Schema forge + PF-CORE ontologies + dev workbench | Free ($0/mo) |
| pfc-core doubles as BAIV-dev | Spoke | BAIV dev/test via PFI scoping within single project | Free ($0/mo) |
| **Total Phase A** | | | **$0/mo** |

> **Key insight:** In Phase A, a single Supabase project hosts both PFC-CORE and BAIV-AIV data, separated by `pfi_id` and RLS policies. This is sufficient for MVP because RLS already enforces PFI isolation at the database layer. No need for separate Supabase projects until PFI instances need independent auth domains or different regions.

**Phase B (Multi-PFI — when second PFI goes live):**

| Database | Tier | Purpose | Cost |
|----------|------|---------|------|
| pfc-core | Hub | Schema forge + PF-CORE shared | Free |
| supabase-baiv | Spoke | BAIV all tiers (dev/test/prod via RLS) | Free |
| supabase-w4m | Spoke | W4M-WWG all tiers | Free |
| **Total Phase B** | | | **$0/mo** |

> Within each PFI spoke, dev/test/prod can initially share one Supabase project with environment-tagged data. RLS + `pfi_instances.code` distinguishes context. Separate projects per tier only needed when auth domains must diverge (e.g. prod users must not see dev data at all).

**Phase C (Production scale — when clients go live):**

| Database | Tier | Purpose | Cost |
|----------|------|---------|------|
| pfc-core | Hub | Schema forge | Free |
| supabase-baiv-prod | Spoke | BAIV production | Pro ($25/mo) |
| supabase-baiv-dev | Spoke | BAIV dev/test | Free |
| supabase-w4m-prod | Spoke | W4M production | Pro ($25/mo) |
| supabase-w4m-dev | Spoke | W4M dev/test | Free |
| **Total Phase C** | | Per active PFI pair | **$50/mo per PFI** |

### 5.3 What Flows from Hub to Spoke

```mermaid
flowchart LR
    subgraph "PFC Hub Database"
        SCHEMA["Schema template<br/>(migrations 001-005)"]
        PFCORE["PF-CORE ontologies<br/>(52 schemas, Tier 0)"]
        SEED["Seed data<br/>(PFI instance configs)"]
    end

    subgraph "PFI Spoke Database"
        INST_SCHEMA["Deployed schema<br/>(identical to hub)"]
        INST_ONT["Instance ontologies<br/>(subset of 52, with instance data)"]
        INST_USERS["Instance users<br/>(independent auth)"]
        INST_AUDIT["Instance audit log<br/>(PFI-scoped)"]
    end

    SCHEMA -->|"supabase db push<br/>or migration files<br/>in pfc-release"| INST_SCHEMA
    PFCORE -->|"ingest-library.js<br/>scoped to instance"| INST_ONT
    SEED -->|"seed.sql template<br/>+ instance overrides"| INST_SCHEMA

    style SCHEMA fill:#e8eaf6
    style PFCORE fill:#e8eaf6
    style INST_ONT fill:#e8f5e9
    style INST_USERS fill:#e8f5e9
```

### 5.4 Supabase Table Topology (All Environments)

```mermaid
erDiagram
    pfi_instances {
        uuid id PK
        text code UK "BAIV-AIV | W4M-WWG | PF-CORE"
        text name
        jsonb brand_config
        text environment "dev | test | prod (Phase B+)"
    }

    profiles {
        uuid id PK "= auth.users.id"
        text display_name
        text role "pf-owner | pfi-admin | pfi-member | viewer"
    }

    user_pfi_access {
        uuid id PK
        uuid user_id FK
        uuid pfi_id FK
    }

    ontologies {
        uuid id PK
        uuid pfi_id FK
        text name
        text version
        text prefix
        text series
        text status
        text oaa_version
        jsonb data "Full ontology JSONB"
        int entity_count "Auto-computed"
        int rel_count "Auto-computed"
        int rule_count "Auto-computed"
    }

    composed_graphs {
        uuid id PK
        uuid pfi_id FK
        text name
        jsonb nodes
        jsonb edges
        jsonb metadata
        boolean is_canonical
    }

    audit_log {
        bigserial id PK
        uuid user_id FK
        uuid pfi_id FK
        text action
        text resource
        jsonb detail
    }

    pfi_instances ||--o{ user_pfi_access : "has members"
    profiles ||--o{ user_pfi_access : "belongs to"
    pfi_instances ||--o{ ontologies : "owns"
    pfi_instances ||--o{ composed_graphs : "owns"
    pfi_instances ||--o{ audit_log : "scoped to"
    profiles ||--o{ audit_log : "performed by"
```

---

## 6. Phased Delivery Plan

### 6.1 Delivery Model: PPM/EFS Structure

Following the VE → PPM/EFS chain, this plan is one **Programme** containing **4 Projects** (phases), each delivering **Features** composed of **Stories**:

```mermaid
flowchart TD
    subgraph "Programme: Supabase Platform MVP"
        P1["Project 1: Foundation<br/>Schema + Ingest + Auth<br/>finish-to-start dependency"]
        P2["Project 2: Workbench Integration<br/>Loader refactor + Graph Access Control<br/>start-to-start with P1 completion"]
        P3["Project 3: API & Distribution<br/>Edge Functions + MCP + CI/CD DB<br/>start-to-start with P2"]
        P4["Project 4: Composed Graphs<br/>Server-side composition + Caching<br/>finish-to-start from P2"]
    end

    P1 -->|"finish-to-start"| P2
    P2 -->|"start-to-start"| P3
    P2 -->|"finish-to-start"| P4

    style P1 fill:#e8f5e9
    style P2 fill:#e3f2fd
    style P3 fill:#fff3e0
    style P4 fill:#fce4ec
```

### 6.2 Project 1: Foundation (Schema + Ingest + Auth)

**Why first:** You cannot secure the workbench without a database, and you cannot have a database without a schema. Auth is bundled here because it is the minimum viable "login works" — the trigger for the entire chain.

**Maps to:** Epic 10A F10A.1 + F10A.2 (partial) + F5.1 sub-phase 5a/5c

| Feature | Stories | Description |
|---------|---------|-------------|
| **F-MVP-1.1: Supabase Project & Schema** | 4 | Create project (EU-West), deploy migrations 001+002+005, run seed, verify RLS |
| **F-MVP-1.2: Ontology Ingest Pipeline** | 4 | Write ingest script, map ontologies→PFI scopes, run ingest, verify JSONB views + search |
| **F-MVP-1.3: Authentication Foundation** | 3 | Enable Supabase Auth (email), wire `onAuthStateChange`, create login/logout modal |

**Gate 1:** User signs up → gets viewer role → sees PF-CORE ontologies. RLS blocks cross-PFI reads. 52+ ontologies queryable via JSONB views.

**Graph Access Control at this phase:**
- Layer 1 (Platform Role): Enforced via RLS — 4 roles active
- Layer 2 (PFI Scope): Enforced via `user_pfi_access` + RLS on `pfi_id`
- Layer 3 (Ontology Selection): `instanceOntologies` constraint applied client-side (existing EMC)
- Layer 4 (Entity Scope): Deferred to Project 4

### 6.3 Project 2: Workbench Integration

**Why second:** With auth and data in place, the workbench can switch from static file loading to Supabase-backed graph access with role-aware zone visibility.

**Maps to:** Epic 10A F10A.3 + F10A.4 + F5.1 sub-phase 5b

| Feature | Stories | Description |
|---------|---------|-------------|
| **F-MVP-2.1: Data Source Router** | 3 | Create `supabase-provider.js` + `data-source-router.js`, add supabase-js ESM import |
| **F-MVP-2.2: Loader Refactoring** | 4 | Refactor github-loader, multi-loader, library-manager, pfi-loader to use router |
| **F-MVP-2.3: Role-Aware Zone Visibility** | 3 | Wire `state.userRole` to skeleton zone visibility conditions, PFI context switcher (Z3), protected write actions |
| **F-MVP-2.4: Audit & Security UI** | 3 | Audit log writes on all mutations, audit viewer panel (Z17), connection status indicator |

**Gate 2:** Visualiser loads from Supabase when online, falls back to static files offline. Zone visibility reflects user role. PFI context switcher works. Mutations logged. All 1527 tests pass.

**Skeleton zone mapping at this phase:**

| Zone | Auth Requirement | New Behaviour |
|------|-----------------|---------------|
| Z3 Context Identity | Authenticated | Shows active PFI + user role |
| Z8 Library Panel | member+ for writes | Read-only for viewer |
| Z10 Export Panel | member+ | Hidden for viewer |
| Z16 Context Drawer | admin+ | PFI config (hidden for member/viewer) |
| Z17 Audit Panel | admin+ | Shows PFI-scoped audit log |
| Z22 Skeleton Inspector | pf-owner | Unchanged |

### 6.4 Project 3: API & Distribution

**Why third:** With the workbench secured and data flowing, extend access to Claude agents (MCP) and third-party tools (REST API), and establish the CI/CD pipeline for database schema distribution to PFI spokes.

**Maps to:** Secure Connections Proposal + Epic 31 (partial)

| Feature | Stories | Description |
|---------|---------|-------------|
| **F-MVP-3.1: Edge Functions** | 5 | Deploy 5 Edge Functions with 3-path auth (JWT / service key / API key) |
| **F-MVP-3.2: Thin MCP Server** | 3 | 5-tool MCP server wrapping Edge Functions (~3K tokens) |
| **F-MVP-3.3: API Key Management** | 3 | API keys table, bcrypt hashing, permission arrays, rate limiting |
| **F-MVP-3.4: Schema Distribution Pipeline** | 3 | Package migrations in pfc-release, deploy to PFI spoke repos, verify schema parity |

**Gate 3:** MCP connects and queries ontologies. REST API authenticates with API key. Schema migrations flow from PFC hub to at least one PFI spoke repo.

### 6.5 Project 4: Composed Graphs & Workbench Persistence

**Why last:** Composed graphs build on all previous layers — they need auth (who composes), PFI scope (which ontologies), and the data layer (where to cache).

**Maps to:** F36.11 completion + Epic 19 F19.6/F19.7

| Feature | Stories | Description |
|---------|---------|-------------|
| **F-MVP-4.1: Server-Side Composition** | 3 | Verify `resolve_composed_graph()` with ingested data, wire EMC composer to use RPC when online |
| **F-MVP-4.2: Composed Graph Persistence** | 3 | Save/load composed graphs to `composed_graphs` table, canonical snapshot management |
| **F-MVP-4.3: Workbench Session Persistence** | 2 | Workbench session state (view, filters, selections) persisted to Supabase per user per PFI |

**Gate 4:** Multi-category graph composed server-side, cached in DB, reloadable. Workbench session persists across browser reloads. Canonical snapshot retrievable via MCP.

---

## 7. Delivery Sequence & Dependencies

```mermaid
gantt
    title Supabase Platform MVP — Delivery Sequence
    dateFormat YYYY-MM-DD
    axisFormat %b %d

    section Project 1: Foundation
    F-MVP-1.1 Schema Deploy           :p1_1, 2026-03-03, 1d
    F-MVP-1.2 Ontology Ingest         :p1_2, after p1_1, 2d
    F-MVP-1.3 Auth Foundation          :p1_3, after p1_1, 2d
    Gate 1 Verification                :milestone, g1, after p1_2 p1_3, 0d

    section Project 2: Workbench
    F-MVP-2.1 Data Source Router       :p2_1, after g1, 2d
    F-MVP-2.2 Loader Refactoring       :p2_2, after p2_1, 3d
    F-MVP-2.3 Role-Aware Zones         :p2_3, after p2_1, 2d
    F-MVP-2.4 Audit & Security UI      :p2_4, after p2_2, 2d
    Gate 2 Verification                :milestone, g2, after p2_2 p2_3 p2_4, 0d

    section Project 3: API & Distribution
    F-MVP-3.1 Edge Functions           :p3_1, after g2, 3d
    F-MVP-3.2 MCP Server               :p3_2, after p3_1, 2d
    F-MVP-3.3 API Key Management       :p3_3, after p3_1, 2d
    F-MVP-3.4 Schema Distribution      :p3_4, after g2, 3d
    Gate 3 Verification                :milestone, g3, after p3_2 p3_3 p3_4, 0d

    section Project 4: Composed Graphs
    F-MVP-4.1 Server-Side Composition  :p4_1, after g2, 3d
    F-MVP-4.2 Graph Persistence        :p4_2, after p4_1, 2d
    F-MVP-4.3 Workbench Session        :p4_3, after p4_2, 2d
    Gate 4 Verification                :milestone, g4, after p4_3, 0d
```

---

## 8. CI/CD Database Distribution Model

### 8.1 Schema as Artefact

Database migrations are treated as **first-class CI/CD artefacts**, flowing through the same hub-and-spoke pipeline as ontologies and visualiser code:

```mermaid
flowchart LR
    subgraph "PFC-Core Forge"
        MIG["supabase/migrations/<br/>001_create_schema.sql<br/>002_rls_policies.sql<br/>005_graphql_layer.sql"]
        REL["pfc-release.yml<br/>packages migrations<br/>in release archive"]
    end

    subgraph "Convention Pipeline"
        SYNC["sync-to-live.yml<br/>distributes to PFI repos"]
    end

    subgraph "PFI-BAIV Triad"
        DEV_MIG["pfi-baiv-dev/<br/>pfc-core/supabase/migrations/"]
        TEST_MIG["pfi-baiv-test/<br/>pfc-core/supabase/migrations/"]
        PROD_MIG["pfi-baiv-prod/<br/>pfc-core/supabase/migrations/"]
        DEV_DB["supabase-baiv<br/>(or shared MVP DB)"]
    end

    MIG --> REL --> SYNC
    SYNC --> DEV_MIG
    DEV_MIG -->|"promote.yml"| TEST_MIG
    TEST_MIG -->|"promote.yml"| PROD_MIG
    DEV_MIG -->|"supabase db push"| DEV_DB

    style MIG fill:#e8eaf6
    style DEV_DB fill:#e8f5e9
```

### 8.2 Instance-Specific Migrations

PFI spokes can add their own migrations **after** the PFC-shared ones:

```
pfi-baiv-dev/
  pfc-core/
    supabase/
      migrations/
        001_create_schema.sql       ← from PFC (read-only, guard-core.yml)
        002_rls_policies.sql        ← from PFC
        005_graphql_layer.sql       ← from PFC
  supabase/
    migrations/
      100_baiv_instance_config.sql  ← BAIV-specific (instance-owned)
      101_baiv_seed_ontologies.sql  ← BAIV-specific
    seed.sql                        ← BAIV instance seed data
```

PFC migrations use numbers 001–099. PFI instance migrations use 100+. The `guard-core.yml` gate ensures PFI devs cannot modify PFC migrations.

---

## 9. Cross-Epic Consolidation

### 9.1 Epic/Feature Mapping

This plan draws from and consolidates:

```mermaid
flowchart TD
    subgraph "Consolidated into This Plan"
        E10A["Epic 10A #127<br/>Security MVP<br/>4 features, 14 stories<br/>0 done"]
        F51["F5.1 #38<br/>Supabase DB Integration<br/>8 stories, 0 done"]
        F3611["F36.11<br/>JSONB PoC + GraphQL<br/>In Progress"]
        PROP["Secure Connections<br/>Proposal v1.0.0"]
    end

    subgraph "Partially Enabled"
        E31["Epic 31 #394<br/>Multi-Instance Delivery<br/>14/37 done"]
        E19["Epic 19 #196<br/>Graph-Scope Rules<br/>27/45 done"]
        E36["Epic 36 #524<br/>Strategy & Architecture"]
    end

    subgraph "Future (Out of Scope)"
        E53["Epic 53 #775<br/>Cloud Sovereignty"]
        E8["Epic 8 #80<br/>Design Director"]
        E21["Epic 21 #270<br/>Agent Architecture"]
        E33["Epic 33 #505<br/>Azure Landing Zone"]
    end

    E10A -->|"enables"| E31
    E10A -->|"foundation for"| E8
    F51 -.->|"superseded by"| E10A
    F3611 -->|"extends"| E10A
    E10A -->|"prerequisite"| E53
    E31 -->|"DB distribution"| E53
    E19 -->|"composed graphs"| F3611

    style E10A fill:#e8f5e9,stroke:#2e7d32
    style F51 fill:#e8f5e9,stroke:#2e7d32
    style F3611 fill:#e8f5e9,stroke:#2e7d32
    style PROP fill:#e8f5e9,stroke:#2e7d32
    style E53 fill:#f5f5f5
    style E8 fill:#f5f5f5
    style E21 fill:#f5f5f5
    style E33 fill:#f5f5f5
```

### 9.2 Recommended Actions

| Action | Rationale |
|--------|-----------|
| **Close F5.1 (#38)** as superseded | Scope fully absorbed by Epic 10A + F36.11 |
| **Update Epic 10A (#127)** body | Reference this plan as implementation sequence |
| **Mark F36.11 as dependent** on this plan's Project 1 | Schema deploy is the prerequisite |
| **Update Epic 31 (#394)** | Add F-MVP-3.4 (Schema Distribution) as dependency for PFI triad provisioning |

---

## 10. Module Impact Summary

### New Files (12)

| Project | File | Purpose |
|---------|------|---------|
| 1 | `supabase/scripts/ingest-library.js` | Bulk ontology ingestion |
| 2 | `js/supabase-provider.js` | All Supabase CRUD operations |
| 2 | `js/data-source-router.js` | Online/offline data source routing |
| 2 | `js/auth-ui.js` | Login/logout modal |
| 2 | `js/audit-viewer.js` | Audit log viewer (Z17) |
| 3 | `supabase/functions/pfc-query-ontologies/index.ts` | Edge Function |
| 3 | `supabase/functions/pfc-write-artifact/index.ts` | Edge Function |
| 3 | `supabase/functions/pfc-resolve-config/index.ts` | Edge Function |
| 3 | `supabase/functions/pfc-export-graph/index.ts` | Edge Function |
| 3 | `supabase/functions/pfc-audit-query/index.ts` | Edge Function |
| 3 | `mcp-server/index.ts` | Thin 5-tool MCP server |
| 3 | `supabase/migrations/004_registry.sql` | Config cascade table |

### Modified Files (7)

| Project | File | Change |
|---------|------|--------|
| 2 | `browser-viewer.html` | supabase-js ESM import |
| 2 | `js/github-loader.js` | Route through DataSourceRouter |
| 2 | `js/multi-loader.js` | Route through DataSourceRouter |
| 2 | `js/library-manager.js` | Replace IndexedDB with SupabaseProvider |
| 2 | `js/app.js` | Auth state hooks, zone visibility |
| 4 | `js/emc-composer.js` | Optional server-side composition |
| 4 | `js/supabase-provider.js` | Composed graph CRUD |

### Untouched (30+ modules)

All graph rendering, parsing, EMC category definitions, audit engine, diff engine, mermaid panel, skeleton loader/editor, and UI panel modules remain unchanged. They consume parsed `{nodes, edges}` data — the source is invisible to them.

---

## 11. Risks & Mitigations

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| Free tier limits (500MB, 50K MAU) | Medium | Low | 52 ontologies ≈ 1MB. Monitor composed_graphs growth. |
| supabase-js breaks zero-build-step | High | Low | ESM import from esm.sh — no bundler needed |
| RLS perf on JSONB GIN queries | Medium | Medium | GIN index in migration 005. Benchmark at Gate 1. |
| Schema drift between hub and spokes | High | Medium | guard-core.yml prevents spoke modification. Drift detection (Epic 31 F31.5). |
| Multi-Supabase-project cost at scale | Medium | Low | Phase A uses 1 project with RLS isolation. Split only when needed. |
| Auth flow disrupts existing static-file users | Low | Low | Phase 2 works without auth. Anonymous read-only mode preserved. |

---

## 12. Explicit Exclusions

| Excluded | Reason | Future Epic |
|----------|--------|-------------|
| Neo4j | Out of scope per directive | Future S1 |
| Sovereign self-hosting (Keycloak, Gitea) | Epic 53 — needs this MVP first | #775 |
| Design Director integration | S2 strategy — needs S1 foundation | #80 |
| Agentic expansion (Agent Manager) | S3 strategy — needs auth + API | #270 |
| Azure Landing Zone | Enterprise deployment | #505 |
| Next.js / shadcn/ui migration | S5 strategy — visualiser stays zero-build-step | Future |
| Real-time subscriptions | GraphQL briefing Phase 3 | Post-MVP |
| Tier 2 (Product) and Tier 3 (Client) scoping | EMC v6.0.0 gaps — needs ProductConfiguration entity | Future |

---

## 13. Decision Points

| # | Decision | Options | Recommendation |
|---|----------|---------|----------------|
| D1 | Supabase region | EU-West-1 (Ireland) vs US-East-1 | **EU-West-1** (UK data, GDPR) |
| D2 | supabase-js loading | CDN `<script>` vs ESM import | **ESM import** (aligns with module pattern) |
| D3 | Phase A: single vs multiple Supabase projects | 1 project (RLS isolation) vs 1 per PFI | **1 project** (cost-effective MVP, split later) |
| D4 | Offline writes | Block vs queue in IndexedDB | **Queue locally**, sync on reconnect |
| D5 | GraphQL vs direct queries | pg_graphql for all vs supabase-js `.from()` | **Direct queries** for MVP; GraphQL for composed graphs |
| D6 | MCP server runtime | Deno vs Node.js | **Deno** (consistent with Edge Functions) |
| D7 | PFI triad DB: shared vs separate per tier | 1 DB per PFI vs 1 per tier | **1 per PFI** initially, split prod when clients go live |
| D8 | Close F5.1 (#38) | Keep open vs close | **Close** — scope fully absorbed |

---

## 14. Success Metrics

| Metric | Target | Gate |
|--------|--------|------|
| Ontologies in Supabase | 52+ | G1 |
| Query latency p95 | < 200ms | G1 |
| Auth round-trip (signup → login → PFI switch) | End-to-end functional | G1 |
| RLS cross-PFI isolation | Verified | G1 |
| Visualiser loads from Supabase | Yes, with fallback | G2 |
| Zone visibility reflects role | Verified | G2 |
| Test suite pass rate | 1527/1527 (100%) | G2 |
| Audit log captures mutations | All writes logged | G2 |
| MCP tool round-trip | < 500ms | G3 |
| MCP token overhead | < 4,000 tokens | G3 |
| Schema parity hub→spoke | Migration checksums match | G3 |
| Server-side graph composition | Functional | G4 |
| Workbench session persists | Across browser reload | G4 |
