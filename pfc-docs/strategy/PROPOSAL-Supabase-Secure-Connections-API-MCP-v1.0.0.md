# Proposal: Secure Connections — Supabase API & MCP Architecture

**Version:** 1.0.0 | **Date:** 2026-02-26 | **Status:** Proposal
**Aligned to:** Epic 34 (S1 Graph-First, S3 Agentic), Epic 10A (Security MVP)
**ADR References:** ADR-001 (Supabase-First), ADR-005 (Shared RLS Multi-Tenancy), PF-CORE-ADR-2026-001 (No Neo4j)

---

## 1. Problem Statement

The platform needs three secure data exchange paths:

1. **Claude Agents → Supabase** — agents read/write ontologies, registry artifacts, audit logs
2. **Visualiser (Browser) → Supabase** — authenticated users CRUD ontologies (replacing IndexedDB)
3. **Third-party Systems → Supabase** — export/import, webhook triggers, external integrations

The naive approach — "build a Supabase MCP server with 30 tools" — carries **prohibitive token overhead**. Every MCP tool definition ships with every Claude API call: schema, description, parameters, examples. A 30-tool MCP server easily burns 10,000–20,000 tokens per request _before the conversation even starts_. At Sonnet rates that's ~$0.01–0.02/request wasted on tool definitions alone, multiplied across every agent turn in a pipeline.

The architecture must be **token-efficient by design**.

---

## 2. Proposed Architecture: Three Layers

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
                    ┌─────────────────┼──────────────────┐
                    │                 │                    │
            ┌───────┴──────┐  ┌──────┴───────┐  ┌───────┴────────┐
            │  LAYER 1     │  │  LAYER 2     │  │  LAYER 3       │
            │  Browser     │  │  Claude MCP  │  │  REST API      │
            │  (Visualiser)│  │  (Thin)      │  │  (Third-party) │
            │              │  │              │  │                 │
            │  supabase-js │  │  5 tools     │  │  API keys       │
            │  anon key    │  │  service key │  │  Edge Functions │
            │  + JWT/RLS   │  │  + PFI scope │  │  + rate limits  │
            └──────────────┘  └──────────────┘  └─────────────────┘
```

### Layer 1: Browser → Supabase (Existing Pattern)

Already designed in Epic 10A. No changes needed.

- `supabase-js` client with anon key
- Supabase Auth issues JWT on login
- RLS policies enforce PFI scoping at the database layer
- `auth.uid()` embedded in JWT drives all policy evaluation
- Zero server-side code required for basic CRUD

### Layer 2: Claude Agent → MCP → Edge Functions → Supabase (New)

**The token-efficient design.** This is the core of the proposal.

### Layer 3: Third-party → REST API → Edge Functions → Supabase (New)

API key-authenticated access for external systems (webhooks, data sync, partner integrations).

---

## 3. Layer 2 Deep Dive: Thin MCP Server (5 Tools)

### The Token Budget Problem

| Approach | Tools | Tool Definition Tokens | Per-request Overhead |
|---|---|---|---|
| Full Supabase MCP | 25-35 tools | 15,000-25,000 | Unacceptable |
| Thin domain MCP | 5 tools | 2,500-4,000 | Acceptable |
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

## 4. Layer 3 Deep Dive: REST API for Third-party Systems

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

## 5. Data Exchange Patterns

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

## 6. Security Architecture Summary

### Authentication Matrix

| Consumer | Auth Method | Scope Enforcement | RLS Used? |
|---|---|---|---|
| Browser (Visualiser) | Supabase Auth JWT | RLS `auth.uid()` | Yes — primary |
| Claude Agent (MCP) | Service key + X-PFI-Scope | Edge Function enforced | No — bypassed, EF is the gate |
| Third-party (API key) | API key hash lookup | Edge Function + permission array | No — bypassed, EF is the gate |
| GitHub Actions | Service key | Hardcoded to PF-CORE scope | No — trusted CI |

### Secret Management

| Secret | Where Stored | Who Uses |
|---|---|---|
| `SUPABASE_URL` | `.env` / GitHub Secrets | All three layers |
| `SUPABASE_ANON_KEY` | Client-side (public) | Browser only |
| `SUPABASE_SERVICE_ROLE_KEY` | Server-side only, never client | MCP server, Edge Functions, CI |
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

## 7. MCP vs Direct API — Decision Framework

### When to Use MCP (Layer 2)

- Claude agent needs Supabase data **mid-conversation** (tool call within a skill)
- Agent is reasoning over ontology data and needs to query/save
- The 5 coarse-grained tools cover the use case

### When to Use Direct API (Layer 3)

- Non-Claude consumer (BI tool, CI/CD, partner system)
- Bulk operations (registry sync, batch export)
- Operations that don't need LLM reasoning

### When to Use Neither (Stay in Layer 1)

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

## 8. Implementation Phases

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

### Phase 3: MCP Server (Claude Integration)

| Step | Deliverable | Depends On |
|---|---|---|
| 3.1 | `pfc-supabase` MCP server scaffold (5 tools) | Phase 2 |
| 3.2 | Claude Code settings — register MCP server | 3.1 |
| 3.3 | DELTA pipeline skill updates — use MCP tools | 3.2 |
| 3.4 | Token budget validation (measure actual overhead) | 3.2 |

### Phase 4: Third-party Access

| Step | Deliverable | Depends On |
|---|---|---|
| 4.1 | API key generation UI (admin) | Phase 1.5, Phase 2.1 |
| 4.2 | Rate limiting middleware in Edge Functions | Phase 2 |
| 4.3 | API documentation (OpenAPI spec) | Phase 2 |
| 4.4 | Webhook integration pattern (auth-webhook EF) | Phase 2.1 |

---

## 9. Migration: pfc_registry Table

The `pfc_registry` table from the Unified Registry briefing needs a dedicated migration:

```sql
-- Migration 004: Unified Registry — Epic 34 (S1 Graph-First)

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

## 10. Token Budget Analysis

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

The thin approach adds ~3,000 tokens (23% increase). The fat approach would add ~15,000 tokens (115% increase). At scale across DELTA pipeline turns (typically 15-30 agent turns per cycle), the difference is significant.

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

## 11. Relationship to Existing Architecture

| Existing Asset | This Proposal | Interaction |
|---|---|---|
| Epic 10A migrations (001, 002) | Phase 1 applies them | Direct dependency |
| MVP-Security-VSOM-v1.1.0 | S1 Foundation = Phase 1-2 | Implements the VSOM |
| Unified Registry briefing | Phase 2.4, Migration 004 | `resolve_artifact_config()` goes live |
| DELTA skills (Epic 52) | Phase 3.3 | Skills gain Supabase data access |
| ADR-001 (Supabase-First) | Fully aligned | Implements the decision |
| ADR-005 (Shared RLS) | Phase 1 | RLS policies applied |
| Visualiser `supabase-js` | Layer 1 (unchanged) | Browser path stays as designed |
| `emc-composer.js` `exportAsJSONB()` | Layer 2 `pfc_export_graph` | Composed graphs exportable via API |
| PFI instance configs | `pfc_resolve_config` tool | Cascade resolution available to agents |

---

## 12. Decision Points for Review

| # | Decision | Options | Recommendation |
|---|---|---|---|
| D1 | MCP server runtime | Node.js / Deno / Python | Node.js (matches existing tooling) |
| D2 | Edge Function deploy | Supabase CLI / GitHub Actions | GitHub Actions (consistent with registry-ci) |
| D3 | API key format | UUID / JWT / opaque token | Opaque token (bcrypt-hashed, simple) |
| D4 | Rate limiting | Edge Function / Supabase middleware / external | Edge Function (no extra infra) |
| D5 | Graph export formats | JSON-LD only / + CSV / + RDF | JSON-LD + CSV (covers BI tools) |
| D6 | MCP server loading | Always-on / conditional per skill | Conditional (token-efficient) |

---

*Proposal: Supabase Secure Connections — API & MCP Architecture v1.0.0*
*Azlan-EA-AAA Platform Foundation*
*26 February 2026*
