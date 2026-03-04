# Briefing: PFC EA Architecture — Database Migrations from Supabase to Azure (or Hybrid)

**Version:** 1.0.0 | **Date:** 2026-03-04 | **Status:** PROPOSAL
**Classification:** CONFIDENTIAL — Strategic Planning Asset
**Aligned to:** Epic 34 (S1 Graph-First, S6 Integration & EA), EA-MSFT-ONT v1.1.0, MCSB-ONT v2.0.0
**Depends on:** Migrations 001–005, `resolve_cascaded_config()`, `pfc_registry` table (Migration 004)
**Cross-Refs:** `STRATEGY-Cloud-Vendor-Sovereignty-Multi-Platform-v1.0.0.md`, `BRIEFING-GraphQL-Supabase-JSONB-MVP.md`, `BRIEFING-Unified-Registry-Database-Strategy.md`, `PLAN-Supabase-JSONB-MVP-Platform-Phase.md`

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Decision Context: Why This Matters Now](#2-decision-context)
3. [Current State: Supabase JSONB Platform](#3-current-state)
4. [Option Analysis: Three Migration Paths](#4-option-analysis)
5. [Option A: Azure Database for PostgreSQL — Full Migration](#5-option-a-azure-postgresql)
6. [Option B: Self-Hosted Supabase on Azure](#6-option-b-self-hosted-supabase-on-azure)
7. [Option C: Supabase Managed + Azure Compliance Wrapper](#7-option-c-supabase-managed-azure-wrapper)
8. [MCSB Control Mapping Per Option](#8-mcsb-control-mapping)
9. [JSONB Ontology Encapsulation: What Migrates](#9-jsonb-ontology-encapsulation)
10. [Migration Scripts & Procedures](#10-migration-scripts)
11. [EA-MSFT-ONT Alignment: Azure Resource Model](#11-ea-msft-ont-alignment)
12. [Cost Comparison](#12-cost-comparison)
13. [Decision Matrix](#13-decision-matrix)
14. [Recommended Path: Phased Approach](#14-recommended-path)
15. [Migration Roadmap](#15-migration-roadmap)

---

## 1. Executive Summary

PF-Core's MVP data layer is Supabase (hosted PostgreSQL + Auth + Realtime + Edge Functions). As the platform moves towards enterprise PFI instances — particularly clients in regulated sectors (AIRL, BAIV) — the question arises: **should the database migrate to Microsoft Azure, and if so, what Azure database service best supports JSONB ontology encapsulation while meeting MCSB security standards?**

This briefing evaluates three options:

| Option | Description | Best For |
|--------|-------------|----------|
| **A** | Azure Database for PostgreSQL Flexible Server | Full Azure-native, maximum MCSB compliance |
| **B** | Self-Hosted Supabase on Azure (AKS/Container Apps) | Preserve Supabase DX, Azure infrastructure |
| **C** | Supabase Managed Cloud + Azure compliance wrapper | Fastest, lowest migration cost, adequate for non-sovereign PFIs |

**Key finding:** All three options preserve full JSONB fidelity. The ontology layer (`ontologies` table, `pfc_registry`, `resolve_cascaded_config()`, `resolve_composed_graph()`) migrates without modification because it is pure PostgreSQL. The differentiation is in **auth, realtime, edge functions, and MCSB compliance posture**.

---

## 2. Decision Context

### 2.1 Why Now?

| Driver | Detail |
|--------|--------|
| **AIRL clients require Azure** | PFI-AIRL-CAF-AZA targets UK public sector clients with existing Microsoft Enterprise Agreements (EA). Azure-only procurement is common. |
| **MCSB compliance is contractual** | MCSB v2 (Preview) is referenced in NCSC CAF assessments. Clients expect data workloads on MCSB-governed platforms. |
| **Supabase is US-hosted** | Supabase Inc. runs on AWS `us-east-1`. UK/EU data sovereignty requirements may prohibit this for regulated PFIs. |
| **MVP migrations not yet deployed** | Migrations 001–005 are written but not deployed. Now is the cheapest time to choose the target platform. |

### 2.2 What Must Be Preserved

| Capability | Non-Negotiable? | Notes |
|------------|-----------------|-------|
| **JSONB ontology storage** | YES | 54 ontologies as whole JSONB documents — no normalisation |
| **`resolve_cascaded_config()` function** | YES | 4-tier cascade resolution (Core → Instance → Product → Client) |
| **`resolve_composed_graph()` function** | YES | Server-side EMC graph composition |
| **RLS policies (PFI scoping)** | YES | Row-level security per PFI instance |
| **`pfc_registry` table** | YES | Multi-artifact registry with cascade resolution |
| **pg_graphql extension** | PREFERRED | Auto-generated GraphQL from tables/views — Supabase-specific |
| **Supabase Auth (GoTrue)** | REPLACEABLE | Can substitute Azure AD B2C or Entra ID |
| **Supabase Realtime** | REPLACEABLE | Can substitute Azure SignalR Service |
| **Supabase Edge Functions** | REPLACEABLE | Can substitute Azure Functions |
| **Supabase Storage** | REPLACEABLE | Can substitute Azure Blob Storage |

---

## 3. Current State: Supabase JSONB Platform

### 3.1 Schema Summary (Migrations 001–005)

```
┌─────────────────────────────────────────────────────────┐
│  Supabase PostgreSQL (Migrations 001-005)                │
│                                                          │
│  TABLES                    VIEWS                         │
│  ├── ontologies (JSONB)    ├── ontology_entities          │
│  ├── pfi_instances         ├── ontology_relationships     │
│  ├── composed_graphs       └── ontology_rules             │
│  ├── audit_log                                            │
│  ├── user_pfi_access       FUNCTIONS                      │
│  └── pfc_registry*         ├── resolve_cascaded_config()  │
│                            ├── resolve_composed_graph()   │
│  RLS POLICIES              ├── resolve_artifact_config()  │
│  ├── pfi_id scoping        └── search_entities()          │
│  ├── user_pfi_access join                                 │
│  └── role-based (anon/auth)                               │
│                                                          │
│  EXTENSIONS                                               │
│  ├── pgcrypto              ├── pg_graphql                 │
│  └── pgjwt                 └── pg_stat_statements         │
└─────────────────────────────────────────────────────────┘
* pfc_registry = Migration 004 (spec'd, not written)
```

### 3.2 Supabase-Specific Dependencies

| Component | PostgreSQL Standard? | Azure Equivalent |
|-----------|---------------------|------------------|
| `pgcrypto` | YES | Built-in on Azure PostgreSQL |
| `pg_stat_statements` | YES | Built-in on Azure PostgreSQL |
| `pg_graphql` | NO (Supabase ext) | Not available — use PostGraphile, Hasura, or Azure API Management |
| GoTrue (Auth) | NO (Supabase) | Azure AD B2C / Entra ID |
| Realtime | NO (Supabase) | Azure SignalR / Azure Web PubSub |
| Edge Functions | NO (Supabase/Deno) | Azure Functions (Node.js/Deno) |
| Storage | NO (Supabase) | Azure Blob Storage |
| PostgREST | NO (Supabase) | PostgREST can be self-hosted, or use Azure API Management |

---

## 4. Option Analysis: Three Migration Paths

```
                    ┌──────────────────────┐
                    │   CURRENT STATE      │
                    │   Supabase Cloud     │
                    │   (AWS us-east-1)    │
                    └──────────┬───────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
              ▼                ▼                ▼
    ┌─────────────────┐ ┌──────────────┐ ┌─────────────────┐
    │   OPTION A      │ │  OPTION B    │ │   OPTION C      │
    │   Azure PgSQL   │ │  Supabase    │ │   Supabase      │
    │   Flexible      │ │  on Azure    │ │   Managed +     │
    │   Server        │ │  (AKS)       │ │   Azure Wrapper │
    │                 │ │              │ │                 │
    │ Full Azure-     │ │ Supabase DX  │ │ Fastest path,   │
    │ native, MCSB    │ │ + Azure      │ │ lowest cost,    │
    │ out of box      │ │ infra        │ │ US-hosted       │
    └─────────────────┘ └──────────────┘ └─────────────────┘
```

---

## 5. Option A: Azure Database for PostgreSQL — Flexible Server

### 5.1 Architecture

```
┌──────────────────────────────────────────────────────────────┐
│  AZURE SUBSCRIPTION (ea-msft:AzureSubscription)              │
│  ├── Resource Group: rg-pfc-{env}-{region}                   │
│  │                                                           │
│  │  ┌─────────────────────────────────────────────┐          │
│  │  │  Azure PostgreSQL Flexible Server            │          │
│  │  │  ├── Engine: PostgreSQL 16                   │          │
│  │  │  ├── Tier: Burstable B1ms (MVP)             │          │
│  │  │  │         → General Purpose D2s (prod)      │          │
│  │  │  ├── Storage: 32GB (MVP) → 256GB (prod)     │          │
│  │  │  ├── JSONB: Full native support              │          │
│  │  │  ├── Extensions: pgcrypto, pg_stat, uuid-ossp│          │
│  │  │  ├── RLS: Full PostgreSQL RLS                │          │
│  │  │  ├── Encryption: AES-256 at rest, TLS 1.2+  │          │
│  │  │  └── Backup: Automated, 7-35 day retention   │          │
│  │  └─────────────────────────────────────────────┘          │
│  │                                                           │
│  │  ┌─────────────────┐  ┌────────────────┐                 │
│  │  │ Azure AD B2C /  │  │ Azure          │                 │
│  │  │ Entra ID        │  │ Functions      │                 │
│  │  │ (Auth)          │  │ (Edge Logic)   │                 │
│  │  └─────────────────┘  └────────────────┘                 │
│  │                                                           │
│  │  ┌─────────────────┐  ┌────────────────┐                 │
│  │  │ Azure SignalR   │  │ Azure Blob     │                 │
│  │  │ (Realtime)      │  │ Storage        │                 │
│  │  └─────────────────┘  └────────────────┘                 │
│  │                                                           │
│  │  ┌─────────────────┐  ┌────────────────┐                 │
│  │  │ Azure Key Vault │  │ Azure Monitor  │                 │
│  │  │ (Secrets)       │  │ (Logging)      │                 │
│  │  └─────────────────┘  └────────────────┘                 │
│  │                                                           │
│  │  ┌─────────────────────────────────────────────┐          │
│  │  │  Azure API Management / PostGraphile         │          │
│  │  │  (replaces pg_graphql)                       │          │
│  │  └─────────────────────────────────────────────┘          │
│  │                                                           │
│  └── VNet + Private Endpoints + NSG Rules                    │
└──────────────────────────────────────────────────────────────┘
```

### 5.2 What Migrates Verbatim

| Component | Migration Method | Effort |
|-----------|-----------------|--------|
| All tables (ontologies, pfi_instances, composed_graphs, pfc_registry, audit_log, user_pfi_access) | `pg_dump` → `pg_restore` | Zero code change |
| All views (ontology_entities, ontology_relationships, ontology_rules) | `pg_dump` → `pg_restore` | Zero code change |
| All functions (`resolve_cascaded_config`, `resolve_composed_graph`, `resolve_artifact_config`, `search_entities`) | `pg_dump` → `pg_restore` | Zero code change |
| All RLS policies | `pg_dump` → `pg_restore` | Zero code change |
| All indexes (GIN on JSONB, btree on scope/pfi_id) | `pg_dump` → `pg_restore` | Zero code change |
| All triggers (audit_log, entity_count sync) | `pg_dump` → `pg_restore` | Zero code change |

### 5.3 What Must Be Replaced

| Supabase Component | Azure Replacement | Migration Effort |
|-------------------|-------------------|------------------|
| **GoTrue Auth** | Azure AD B2C + JWT middleware | Medium — rewrite auth token validation, user signup/login flows |
| **PostgREST** | Self-hosted PostgREST or Azure API Management | Low — PostgREST is open-source, can deploy as container |
| **pg_graphql** | PostGraphile (open-source) on Azure Container Apps | Low — PostGraphile auto-generates same GraphQL schema from tables/views |
| **Realtime** | Azure SignalR Service | Medium — rewrite subscription logic |
| **Edge Functions** | Azure Functions (Node.js) | Medium — port Deno Edge Functions to Node.js Azure Functions |
| **Storage** | Azure Blob Storage | Low — change upload/download endpoints |
| **supabase-js SDK** | Custom client or `pg` + Azure SDK | Medium — replace `createClient()` with direct `pg` pool + Azure Identity |

### 5.4 Pros / Cons

| Pros | Cons |
|------|------|
| Full MCSB compliance out of the box | Loss of Supabase DX (instant API, dashboard, auth UI) |
| Microsoft Defender for Cloud integration | Must replace 6 Supabase services with Azure equivalents |
| Azure AD authentication (enterprise SSO) | Higher operational complexity (more Azure services to manage) |
| Private endpoints, VNet isolation | PostGraphile replaces pg_graphql (minor syntax differences) |
| UK South / North Europe regions (data sovereignty) | Higher monthly cost at MVP scale |
| Azure Landing Zone governance (ea-msft:AzureLandingZone) | Longer setup time vs Supabase "click and go" |
| WAF pillar alignment (ea-msft:WAFPillar) | |

---

## 6. Option B: Self-Hosted Supabase on Azure

### 6.1 Architecture

```
┌──────────────────────────────────────────────────────────────┐
│  AZURE SUBSCRIPTION                                          │
│  ├── Resource Group: rg-pfc-supabase-{env}-{region}          │
│  │                                                           │
│  │  ┌─────────────────────────────────────────────┐          │
│  │  │  Azure Kubernetes Service (AKS)              │          │
│  │  │  OR Azure Container Apps                     │          │
│  │  │                                              │          │
│  │  │  Containers:                                 │          │
│  │  │  ├── supabase/postgres (PostgreSQL 16)       │          │
│  │  │  │   └── BACKED BY: Azure PgSQL Flex Server  │          │
│  │  │  ├── supabase/gotrue (Auth)                  │          │
│  │  │  ├── supabase/realtime (WebSocket)           │          │
│  │  │  ├── supabase/storage-api (S3-compat)        │          │
│  │  │  │   └── BACKED BY: Azure Blob Storage       │          │
│  │  │  ├── supabase/edge-runtime (Deno)            │          │
│  │  │  ├── supabase/pg_graphql (extension)         │          │
│  │  │  ├── postgrest/postgrest (REST API)          │          │
│  │  │  └── kong (API Gateway)                      │          │
│  │  └─────────────────────────────────────────────┘          │
│  │                                                           │
│  │  ┌─────────────────┐  ┌────────────────┐                 │
│  │  │ Azure Key Vault │  │ Azure Monitor  │                 │
│  │  │ (Secrets)       │  │ + Log Analytics│                 │
│  │  └─────────────────┘  └────────────────┘                 │
│  │                                                           │
│  └── VNet + Private Endpoints + NSG Rules                    │
└──────────────────────────────────────────────────────────────┘
```

### 6.2 Key Configuration

```yaml
# docker-compose.azure.yml (simplified)
services:
  db:
    # Option: Use Azure PostgreSQL Flexible Server as external DB
    # instead of containerised postgres
    environment:
      POSTGRES_HOST: pfc-db.postgres.database.azure.com
      POSTGRES_PORT: 5432
      POSTGRES_DB: pfc_platform
      POSTGRES_PASSWORD: ${AZURE_KEY_VAULT_SECRET}

  gotrue:
    image: supabase/gotrue:latest
    environment:
      GOTRUE_JWT_SECRET: ${JWT_SECRET}
      GOTRUE_EXTERNAL_AZURE_ENABLED: true  # Azure AD provider
      GOTRUE_EXTERNAL_AZURE_CLIENT_ID: ${AZURE_AD_CLIENT_ID}
      GOTRUE_EXTERNAL_AZURE_SECRET: ${AZURE_AD_CLIENT_SECRET}

  realtime:
    image: supabase/realtime:latest
    environment:
      DB_HOST: pfc-db.postgres.database.azure.com

  storage:
    image: supabase/storage-api:latest
    environment:
      STORAGE_BACKEND: s3  # Azure Blob via S3-compat
      GLOBAL_S3_ENDPOINT: https://pfcstorage.blob.core.windows.net
```

### 6.3 Pros / Cons

| Pros | Cons |
|------|------|
| **Zero application code changes** — supabase-js SDK works identically | You own the entire operational stack (upgrades, patches, scaling) |
| pg_graphql extension available (containerised) | No Supabase Inc. SLA — you are the operator |
| Auth, Realtime, Edge Functions all preserved | AKS complexity (node pools, ingress, TLS, monitoring) |
| Azure PostgreSQL Flexible Server as backing DB (MCSB-compliant data layer) | Must document self-hosted components for MCSB audit |
| GoTrue supports Azure AD as external provider (SSO) | Container image versions must track Supabase releases |
| UK South deployment (data sovereignty) | Higher cost than managed Supabase (~£150-300/mo for AKS cluster) |
| VNet isolation achievable | GoTrue is not MCSB-assessed — you inherit responsibility |

---

## 7. Option C: Supabase Managed + Azure Compliance Wrapper

### 7.1 Architecture

```
┌──────────────────────────────────────────────┐
│  SUPABASE CLOUD (AWS us-east-1)              │
│  ├── PostgreSQL 16 (JSONB, RLS, functions)   │
│  ├── GoTrue Auth (JWT)                       │
│  ├── PostgREST + pg_graphql                  │
│  ├── Realtime (WebSocket)                    │
│  ├── Edge Functions (Deno)                   │
│  └── Storage (S3)                            │
└──────────────┬───────────────────────────────┘
               │ HTTPS (TLS 1.2+)
               │
┌──────────────▼───────────────────────────────┐
│  AZURE COMPLIANCE WRAPPER                     │
│  ├── Azure API Management (rate limit, audit) │
│  ├── Azure Front Door (WAF, DDoS, geo-filter) │
│  ├── Azure AD B2C (enterprise SSO → JWT)      │
│  ├── Azure Key Vault (connection strings)      │
│  ├── Azure Monitor (activity logs, alerts)     │
│  └── Azure Policy (compliance dashboard)       │
└──────────────────────────────────────────────┘
```

### 7.2 Pros / Cons

| Pros | Cons |
|------|------|
| **Fastest to deploy** — Supabase stays as-is | Data resides in US (AWS) — **fails UK data sovereignty** |
| Lowest cost (Supabase Free/Pro + Azure wrapper services) | Supabase is not MCSB-assessed — compliance is your burden |
| Full Supabase DX preserved | Azure wrapper adds latency (US → UK round-trip) |
| Azure AD B2C can issue JWTs that Supabase accepts | Cannot use Azure Private Link to Supabase |
| Good for **non-regulated** PFIs (BAIV, VHF, W4M-WWG) | Audit trail split across Supabase dashboard + Azure Monitor |
| Upgrade path to Option A or B when needed | CLOUD Act exposure on both sides (Microsoft + AWS) |

---

## 8. MCSB Control Mapping Per Option

### 8.1 MCSB Control Domains (from MCSB-ONT v2.0.0)

| MCSB Domain | Control Code | Control Name | Option A | Option B | Option C |
|-------------|-------------|--------------|----------|----------|----------|
| **NS** (Network Security) | NS-1 | Network segmentation | VNet + Private Endpoints | VNet + AKS internal LB | Azure Front Door + WAF |
| **NS** | NS-2 | Cloud service network security | Private Link | Private Link (DB only) | Public endpoint (TLS) |
| **IM** (Identity Management) | IM-1 | Centralised identity and auth | Azure AD / Entra ID | Azure AD via GoTrue | Azure AD B2C → JWT relay |
| **IM** | IM-3 | Application identity management | Managed Identity | Managed Identity (AKS pods) | Supabase service key + vault |
| **PA** (Privileged Access) | PA-1 | Protect admin accounts | Azure PIM / RBAC | AKS RBAC + PIM | Supabase dashboard (MFA) |
| **DS** (Data Security) | DS-1 | Discovery/classification | Azure Purview compatible | Manual | Manual |
| **DS** | DS-2 | Data in transit encryption | TLS 1.2+ (enforced) | TLS 1.2+ (enforced) | TLS 1.2+ (enforced) |
| **DS** | DS-3 | Data at rest encryption | AES-256 (Azure managed) | AES-256 (Azure PgSQL) | AES-256 (Supabase/AWS) |
| **DS** | DS-4 | Data access authorisation | PostgreSQL RLS + Azure AD | PostgreSQL RLS + GoTrue | PostgreSQL RLS + GoTrue |
| **LT** (Logging & Threat Detection) | LT-1 | Enable threat detection | Defender for Cloud | Defender for AKS + DB | Azure Monitor (wrapper only) |
| **LT** | LT-4 | Network logging | VNet Flow Logs + NSG | AKS Network Policy logs | Azure Front Door logs |
| **BR** (Backup & Recovery) | BR-1 | Backup and restore | Azure automated backup | Azure PgSQL backup | Supabase daily backup (Pro) |
| **AI** (AI Security) | AI-1 | AI workload discovery | N/A (DB only) | N/A | N/A |
| **GS** (Governance & Strategy) | GS-1 | Security governance strategy | Azure Policy + Defender | Azure Policy + AKS Policy | Azure Policy (partial) |

### 8.2 MCSB Compliance Score Estimate

| Option | Compliance Coverage | Gaps | Effort to Close |
|--------|-------------------|------|-----------------|
| **A: Azure PgSQL** | **92%** | pg_graphql replacement needs assessment | Low |
| **B: Supabase on Azure** | **78%** | GoTrue, PostgREST, Realtime not MCSB-assessed | Medium (document compensating controls) |
| **C: Supabase + Wrapper** | **55%** | Data sovereignty, Supabase platform not assessed | High (may not close for regulated PFIs) |

---

## 9. JSONB Ontology Encapsulation: What Migrates

### 9.1 The JSONB Layer is Platform-Agnostic

Every ontology is stored as a complete JSON-LD document in the `ontologies.data` JSONB column. This is **pure PostgreSQL** — no Supabase-specific extensions touch the data layer.

```sql
-- This SQL works identically on Supabase, Azure PostgreSQL, AWS RDS, or self-hosted
SELECT
  name, version, prefix, series,
  data->'entities' AS entities,
  data->'relationships' AS relationships,
  jsonb_array_length(data->'entities') AS entity_count
FROM ontologies
WHERE pfi_id = $1
  AND status = 'active'
  AND data @> '{"oaa:series": "VE-Series"}'::jsonb;
```

### 9.2 Ontology Encapsulation Pattern

```
┌────────────────────────────────────────────────────────────┐
│  ontologies TABLE (identical on all three options)          │
│                                                            │
│  ┌──────────────────────────────────────────────────┐      │
│  │  ROW: VP-ONT v3.0.0                              │      │
│  │  ├── id: uuid                                    │      │
│  │  ├── pfi_id: uuid → pfi_instances                │      │
│  │  ├── name: "VP-ONT"                              │      │
│  │  ├── version: "3.0.0"                            │      │
│  │  ├── prefix: "vp:"                               │      │
│  │  ├── series: "VE-Series"                         │      │
│  │  ├── data: {                                     │      │
│  │  │     "@context": { ... },                      │      │
│  │  │     "@id": "vp:schema",                       │      │
│  │  │     "entities": [ 17 entities ],              │      │
│  │  │     "relationships": [ 30 rels ],             │      │
│  │  │     "businessRules": [ 22 rules ],            │      │
│  │  │     "enumerations": [ ... ]                   │      │
│  │  │   }  ← ENTIRE JSON-LD DOCUMENT AS JSONB       │      │
│  │  ├── entity_count: 17  (trigger-synced)          │      │
│  │  ├── rel_count: 30     (trigger-synced)          │      │
│  │  └── rule_count: 22    (trigger-synced)          │      │
│  └──────────────────────────────────────────────────┘      │
│                                                            │
│  54 ontologies × avg 25KB = ~1.35MB total JSONB            │
│  GIN index on `data` column for @> containment queries     │
│  Views project entities/rels/rules as typed rows           │
└────────────────────────────────────────────────────────────┘
```

### 9.3 Cascade Resolution is Pure SQL

`resolve_cascaded_config()` and `resolve_artifact_config()` are `LANGUAGE plpgsql` functions — they work on any PostgreSQL 14+ engine. No migration required.

```sql
-- Works identically on all three options
SELECT resolve_artifact_config(
  'ontology',       -- artifact_type
  'VP-ONT',         -- name
  'W4M-WWG',        -- instance_id
  'WWG-PORTAL',     -- product_id
  NULL               -- client_id
);
-- Returns: merged JSONB config across Core → Instance → Product tiers
```

---

## 10. Migration Scripts & Procedures

### 10.1 Option A: Supabase → Azure PostgreSQL

```bash
#!/bin/bash
# migrate-supabase-to-azure-pgsql.sh

# ── Step 1: Export from Supabase ──────────────────────────────
export SUPABASE_DB_URL="postgresql://postgres:[password]@db.[project].supabase.co:5432/postgres"

# Full schema + data dump (excludes Supabase internal schemas)
pg_dump "$SUPABASE_DB_URL" \
  --no-owner \
  --no-acl \
  --exclude-schema='supabase_*' \
  --exclude-schema='auth' \
  --exclude-schema='storage' \
  --exclude-schema='realtime' \
  --exclude-schema='extensions' \
  --exclude-schema='graphql' \
  --exclude-schema='graphql_public' \
  -Fc -f pfc-platform-dump.pgdump

# ── Step 2: Create Azure PostgreSQL Flexible Server ───────────
az postgres flexible-server create \
  --resource-group rg-pfc-prod-uksouth \
  --name pfc-db-prod \
  --location uksouth \
  --admin-user pfcadmin \
  --admin-password "${AZURE_DB_PASSWORD}" \
  --sku-name Standard_B1ms \
  --tier Burstable \
  --storage-size 32 \
  --version 16 \
  --high-availability Disabled \
  --public-access None  # Private endpoint only

# Enable required extensions
az postgres flexible-server parameter set \
  --resource-group rg-pfc-prod-uksouth \
  --server-name pfc-db-prod \
  --name azure.extensions \
  --value "pgcrypto,pg_stat_statements,uuid-ossp"

# ── Step 3: Import to Azure ───────────────────────────────────
export AZURE_DB_URL="postgresql://pfcadmin:${AZURE_DB_PASSWORD}@pfc-db-prod.postgres.database.azure.com:5432/pfc_platform"

pg_restore "$AZURE_DB_URL" \
  --no-owner \
  --no-acl \
  --single-transaction \
  -d pfc_platform \
  pfc-platform-dump.pgdump

# ── Step 4: Verify JSONB integrity ────────────────────────────
psql "$AZURE_DB_URL" -c "
  SELECT name, version, entity_count, rel_count,
         jsonb_typeof(data) AS data_type,
         octet_length(data::text) AS data_bytes
  FROM ontologies
  ORDER BY series, name;
"

# ── Step 5: Verify functions ──────────────────────────────────
psql "$AZURE_DB_URL" -c "
  SELECT proname, prorettype::regtype
  FROM pg_proc
  WHERE pronamespace = 'public'::regnamespace
  AND proname LIKE 'resolve_%' OR proname LIKE 'search_%';
"

# ── Step 6: Verify RLS ───────────────────────────────────────
psql "$AZURE_DB_URL" -c "
  SELECT tablename, policyname, permissive, cmd
  FROM pg_policies
  WHERE schemaname = 'public';
"
```

### 10.2 Option B: Azure PostgreSQL as Supabase External DB

```bash
# When self-hosting Supabase on AKS, point Supabase at Azure PostgreSQL
# instead of the bundled postgres container

# Helm values for supabase-kubernetes chart
# values-azure.yaml
db:
  enabled: false  # Don't deploy postgres container
  host: pfc-db-prod.postgres.database.azure.com
  port: 5432
  name: pfc_platform
  user: pfcadmin
  password:
    existingSecret: pfc-db-credentials  # From Azure Key Vault via CSI driver
    key: password

auth:
  externalProviders:
    azure:
      enabled: true
      clientId: "${AZURE_AD_CLIENT_ID}"
      secret:
        existingSecret: azure-ad-credentials
        key: clientSecret
```

### 10.3 Option C: No DB Migration Required

For Option C, the database stays on Supabase. The wrapper is Azure-side only:

```bicep
// azure-compliance-wrapper.bicep (Azure Bicep template)
resource apiManagement 'Microsoft.ApiManagement/service@2023-05-01-preview' = {
  name: 'pfc-apim-${env}'
  location: 'uksouth'
  sku: { name: 'Consumption', capacity: 0 }
  properties: {
    publisherEmail: 'platform@pfcore.io'
    publisherName: 'PF-Core Platform'
  }
}

resource supabaseBackend 'Microsoft.ApiManagement/service/backends@2023-05-01-preview' = {
  parent: apiManagement
  name: 'supabase'
  properties: {
    url: 'https://${supabaseProjectRef}.supabase.co'
    protocol: 'http'
    tls: { validateCertificateChain: true, validateCertificateName: true }
  }
}
```

---

## 11. EA-MSFT-ONT Alignment: Azure Resource Model

For Options A and B, the Azure infrastructure maps directly to EA-MSFT-ONT v1.1.0 entities:

| Azure Resource | EA-MSFT-ONT Entity | Relationship |
|---------------|--------------------|----|
| Azure Subscription (billing boundary) | `ea-msft:AzureSubscription` | `ea-msft:belongsToSubscription` |
| Resource Group `rg-pfc-prod-uksouth` | `ea-msft:AzureResourceGroup` | `ea-msft:containsResource` |
| Azure PostgreSQL Flexible Server | `ea-msft:AzureResource` (subtype) | `ea-msft:providedByPlatform` |
| Azure Landing Zone (if CAF-governed) | `ea-msft:AzureLandingZone` | `ea-msft:governedByLandingZone` |
| WAF assessment of platform | `ea-msft:WAFPillar` × 5 | `ea-msft:assessedByPillar` |

### WAF Pillar Assessment for PFC Database

| WAF Pillar | Assessment Focus | Target Criterion |
|-----------|-----------------|-----------------|
| **Reliability** | Automated backup, point-in-time restore, zone-redundant HA | RPO < 5 min, RTO < 1 hour |
| **Security** | TLS 1.2, AES-256, Azure AD auth, Private Endpoints, RLS | MCSB DS-2, DS-3, NS-2 |
| **Cost Optimization** | Burstable B1ms for MVP, scale to GP D2s on demand | <£50/mo MVP, <£200/mo prod |
| **Operational Excellence** | Azure Monitor, diagnostic logs, automated patching | Zero-downtime patching |
| **Performance Efficiency** | GIN indexes on JSONB, read replicas for visualiser | <100ms p95 for `resolve_composed_graph()` |

---

## 12. Cost Comparison

### 12.1 Monthly Cost Estimates (UK South Region)

| Component | Option A: Azure PgSQL | Option B: Supabase on Azure | Option C: Supabase + Wrapper |
|-----------|----------------------|----------------------------|------------------------------|
| **Database** | £12 (B1ms) → £90 (D2s) | £90 (Azure PgSQL D2s) | £0–25 (Supabase Free/Pro) |
| **Compute (API/Functions)** | £15 (Azure Functions consumption) | £100 (AKS B2s node pool) | £0 (Supabase Edge) |
| **Auth** | £0 (Azure AD B2C Free tier, 50K users) | £0 (GoTrue self-hosted) | £0 (Supabase Auth) |
| **Storage** | £2 (Azure Blob, <10GB) | £2 (Azure Blob) | £0 (Supabase Storage) |
| **Networking** | £5 (Private Endpoint + NAT) | £15 (AKS LB + Private Endpoint) | £5 (Azure Front Door) |
| **Monitoring** | £5 (Azure Monitor) | £10 (Monitor + Container Insights) | £5 (Azure Monitor) |
| **API Management** | £0 (PostGraphile on Functions) | £0 (pg_graphql included) | £0 (APIM consumption) |
| **Key Vault** | £1 | £1 | £1 |
| **TOTAL (MVP)** | **~£40/mo** | **~£218/mo** | **~£36/mo** |
| **TOTAL (Production)** | **~£118/mo** | **~£218/mo** | **~£36/mo** |

### 12.2 Hidden Costs

| Factor | Option A | Option B | Option C |
|--------|----------|----------|----------|
| **Migration effort** | 2-3 weeks (replace 6 Supabase services) | 1-2 weeks (AKS setup, Helm config) | 1-2 days (Azure wrapper only) |
| **Ongoing ops** | Low (Azure managed) | High (self-managed Supabase stack) | Low (both managed) |
| **MCSB audit prep** | Low (Azure controls documented) | Medium (document compensating controls) | High (justify US-hosted data) |
| **Upgrade friction** | Low (Azure handles PG upgrades) | Medium (track Supabase container versions) | None |

---

## 13. Decision Matrix

| Criterion | Weight | Option A: Azure PgSQL | Option B: Supabase on Azure | Option C: Supabase + Wrapper |
|-----------|--------|----------------------|----------------------------|------------------------------|
| **JSONB fidelity** | 20% | 10/10 (native PG) | 10/10 (native PG) | 10/10 (native PG) |
| **MCSB compliance** | 25% | 9/10 (native) | 7/10 (partial) | 5/10 (gaps) |
| **UK data sovereignty** | 15% | 10/10 (UK South) | 10/10 (UK South) | 2/10 (US-hosted) |
| **Developer experience** | 10% | 5/10 (no Supabase DX) | 9/10 (full Supabase DX) | 10/10 (managed Supabase) |
| **Operational cost (MVP)** | 10% | 8/10 (~£40/mo) | 4/10 (~£218/mo) | 9/10 (~£36/mo) |
| **Operational complexity** | 10% | 7/10 (Azure managed) | 3/10 (self-managed) | 9/10 (both managed) |
| **Enterprise client acceptance** | 10% | 10/10 (Azure-native) | 7/10 (Azure infra, Supabase brand) | 4/10 (US SaaS dependency) |
| **WEIGHTED TOTAL** | 100% | **8.4** | **7.1** | **6.3** |

---

## 14. Recommended Path: Phased Approach

### Phase 0: MVP (Now → Q2 2026) — Option C

Stay on **Supabase Managed Cloud** for all non-regulated PFI instances:
- Fastest velocity for BAIV, W4M-WWG, VHF
- Deploy migrations 001–005 on Supabase
- Prove the JSONB ontology pattern, `resolve_cascaded_config()`, GraphQL layer
- No Azure infrastructure cost during MVP validation

### Phase 1: Azure Readiness (Q2 2026) — Prepare Option A

- Write migration scripts (`migrate-supabase-to-azure-pgsql.sh`)
- Set up Azure PostgreSQL Flexible Server in UK South (dev/test)
- Validate `pg_dump` → `pg_restore` round-trip with JSONB integrity checks
- Deploy PostGraphile on Azure Functions as pg_graphql replacement
- Test Azure AD B2C JWT flow against PostgreSQL RLS policies

### Phase 2: AIRL Production (Q3 2026) — Deploy Option A

- PFI-AIRL-CAF-AZA moves to **Azure PostgreSQL Flexible Server**
- Azure AD authentication (Entra ID for enterprise SSO)
- Private Endpoints, VNet, Defender for Cloud
- MCSB control mapping documented and audited
- Other PFIs remain on Supabase (Option C) unless they require Azure

### Phase 3: PFI-Selectable Platform (Q4 2026)

- Each PFI instance chooses its data platform at activation time:

```json
{
  "@id": "PFI-AIRL-CAF-AZA",
  "platformConfig": {
    "database": "azure-postgresql",
    "region": "uksouth",
    "auth": "azure-ad-b2c",
    "mcsb_compliant": true
  }
}

{
  "@id": "PFI-W4M-WWG",
  "platformConfig": {
    "database": "supabase-cloud",
    "region": "us-east-1",
    "auth": "supabase-auth",
    "mcsb_compliant": false
  }
}
```

### Decision Summary

| PFI Profile | Recommended Option | Rationale |
|-------------|-------------------|-----------|
| **Regulated (AIRL, NCSC-CAF)** | **Option A** | Azure-native, MCSB-compliant, UK data sovereignty |
| **Enterprise (BAIV, mid-market)** | **Option A or B** | Azure preferred by enterprise clients; Option B if Supabase DX is critical |
| **Startup / PoC (VHF, ANTQ, W4M-WWG)** | **Option C** | Cheapest, fastest, adequate compliance for non-regulated sectors |
| **Self-hosted / sovereign** | **Option B** | Full control, any region, Supabase DX preserved |

---

## 15. Migration Roadmap

```
Q1 2026 (NOW)          Q2 2026              Q3 2026              Q4 2026
    │                      │                    │                    │
    │  ┌────────────────┐  │  ┌──────────────┐ │  ┌──────────────┐ │
    │  │ Phase 0: MVP   │  │  │ Phase 1:     │ │  │ Phase 2:     │ │
    │  │ Supabase Cloud │  │  │ Azure        │ │  │ AIRL on      │ │
    │  │ Migrations     │  │  │ Readiness    │ │  │ Azure PgSQL  │ │
    │  │ 001-005        │  │  │              │ │  │              │ │
    │  │                │  │  │ - Azure PgSQL│ │  │ - Production │ │
    │  │ - Deploy to    │  │  │   dev/test   │ │  │   deploy     │ │
    │  │   Supabase     │  │  │ - pg_dump    │ │  │ - Azure AD   │ │
    │  │ - Prove JSONB  │  │  │   round-trip │ │  │   SSO        │ │
    │  │   pattern      │  │  │ - PostGraphile│ │ │ - MCSB audit │ │
    │  │ - Validate     │  │  │   on Azure   │ │  │ - Defender   │ │
    │  │   cascade      │  │  │   Functions  │ │  │   enabled    │ │
    │  │                │  │  │ - Azure AD   │ │  │              │ │
    │  └────────────────┘  │  │   B2C test   │ │  └──────────────┘ │
    │                      │  └──────────────┘ │                    │
    │                      │                    │  ┌──────────────┐ │
    │                      │                    │  │ Phase 3:     │ │
    │                      │                    │  │ PFI-         │ │
    │                      │                    │  │ Selectable   │ │
    │                      │                    │  │ Platform     │ │
    │                      │                    │  └──────────────┘ │
```

### Deliverables Per Phase

| Phase | Deliverable | Owner |
|-------|-------------|-------|
| 0 | Deploy migrations 001-005 to Supabase | Platform team |
| 0 | Validate `resolve_cascaded_config()` with 5 PFI instances | Platform team |
| 1 | `migrate-supabase-to-azure-pgsql.sh` script | Platform team |
| 1 | Azure PostgreSQL Flexible Server (dev/test, UK South) | DevOps |
| 1 | PostGraphile deployment on Azure Functions | Platform team |
| 1 | Azure AD B2C → PostgreSQL RLS integration test | Security team |
| 2 | PFI-AIRL production deployment on Azure PostgreSQL | Platform + AIRL team |
| 2 | MCSB control evidence package (NS, IM, DS, PA, LT, BR) | Security team |
| 3 | `platformConfig` field in PFI instance registry entries | Platform team |
| 3 | PFI bootstrap script: Supabase or Azure path selection | DevOps |

---

*Briefing: PFC EA Architecture — Database Migrations from Supabase to Azure (or Hybrid)*
*Version 1.0.0 — Status: PROPOSAL*
*Azlan-EA-AAA Platform Foundation*
*4 March 2026*
