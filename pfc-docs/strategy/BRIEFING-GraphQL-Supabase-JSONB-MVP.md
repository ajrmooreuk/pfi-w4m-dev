# Briefing: GraphQL + Supabase JSONB — MVP Architecture

**Version:** 1.0.0 | **Date:** 2026-02-26 | **Status:** Proposal
**Aligned to:** Epic 34 (S1 Graph-First), Supabase Secure Connections Proposal v1.0.0
**Depends on:** Migrations 001–004, pg_graphql extension
**Migration:** `005_graphql_layer.sql`

---

## 1. Executive Summary

GraphQL sits between the JSONB ontology store and consumers (visualiser, agents, third-party tools) as a **typed transport layer**. The heavy graph logic stays in Postgres functions; GraphQL shapes the response.

**Key principle:** Ontologies stay as whole JSONB documents. We don't normalise entities/relationships into separate tables. Instead, we use **views**, **computed columns**, and **Postgres functions** to project JSONB into typed GraphQL fields on demand.

```
┌─────────────────────────────────────────────────────────────────┐
│                        CONSUMERS                                │
│                                                                  │
│  Visualiser (Browser)    Claude Agent (MCP)    Third-party (API) │
│         │                      │                      │          │
│         └──────────┬───────────┴──────────┬───────────┘          │
│                    │                      │                       │
│              /graphql/v1           Edge Functions                 │
│              (pg_graphql)          (call same SQL)                │
│                    │                      │                       │
│         ┌──────────┴──────────────────────┘                      │
│         │                                                         │
│  ┌──────▼──────────────────────────────────────────────┐         │
│  │  PostgreSQL                                          │         │
│  │                                                       │         │
│  │  TABLES           VIEWS              FUNCTIONS        │         │
│  │  ┌────────────┐   ┌───────────────┐  ┌─────────────┐ │         │
│  │  │ ontologies │   │ ont_entities  │  │ resolve_    │ │         │
│  │  │ (JSONB)    │──▶│ ont_rels      │  │ composed_   │ │         │
│  │  │            │   │ ont_rules     │  │ graph()     │ │         │
│  │  ├────────────┤   └───────────────┘  ├─────────────┤ │         │
│  │  │ composed_  │                      │ search_     │ │         │
│  │  │ graphs     │                      │ entities()  │ │         │
│  │  ├────────────┤                      ├─────────────┤ │         │
│  │  │ pfi_       │                      │ get_ont_    │ │         │
│  │  │ instances  │                      │ dep_graph() │ │         │
│  │  └────────────┘                      └─────────────┘ │         │
│  └──────────────────────────────────────────────────────┘         │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. What GraphQL Works With

| Component | Fit | Notes |
|-----------|-----|-------|
| **Supabase pg_graphql** | Native | Auto-generates schema from tables, views, computed columns |
| **JSONB ontologies** | Good (via views) | Views flatten `data->'entities'` into typed rows; computed columns expose summary arrays |
| **Graph traversal** | Good (via functions) | `resolve_composed_graph()` does the heavy lifting in SQL |
| **PFI instance cascade** | Good | Single query fetches Core + Instance ontologies in one round-trip |
| **RLS** | Native | pg_graphql respects RLS — PFI scoping is automatic |
| **Vis-network frontend** | Neutral | GraphQL is transport; vis-network doesn't care how data arrives |
| **JSON-LD semantics** | Weak | No native `@context`, namespaces, or prefixes — flattened at view layer |
| **EMC composition** | Partial | Category → ontology mapping stays in JS; SQL handles entity/edge extraction |

---

## 3. Pros and Cons

### Pros

1. **Single round-trip for composed graphs** — One query fetches an EMC composition (entities + relationships + rules + metadata) instead of N REST calls or loading N files
2. **Supabase pg_graphql is free** — Zero additional infrastructure; reads table schema and exposes `/graphql/v1` automatically
3. **Client asks for exactly what it needs** — Visualiser requests `{ id, label, sourceNamespace }` for graph rendering, or full detail for the editor panel. No over-fetching.
4. **Strongly typed schema** — Acts as a contract between JSONB data and consumers. Frontend gets TypeScript-friendly types from introspection.
5. **Subscription support** — pg_graphql supports real-time subscriptions, aligning with Supabase Realtime for future multi-user editing
6. **Introspection** — Auto-generated docs; any developer or agent can discover the API shape without reading code
7. **Works with existing RLS** — No new security layer needed. Queries go through the same PFI-scoped policies already in migrations 001-002

### Cons

1. **JSONB is opaque to auto-schema** — pg_graphql sees `data JSONB` as a scalar. The views and computed columns in migration 005 bridge this gap, but they need maintenance when ontology schema evolves.
2. **N+1 risk on nested queries** — Naive `ontologies → entities → relationships` nesting fires per-row. Mitigated by using the composition functions instead of nested resolvers.
3. **Not a graph query language** — GraphQL is tree-shaped, not graph-shaped. Cycles, shortest-path, and transitive closure need custom SQL functions (e.g., `resolve_composed_graph`), not GraphQL nesting.
4. **Schema rigidity vs ontology flexibility** — Views assume a fixed entity/relationship structure. If OAA schema changes (e.g., new top-level array), views need updating.
5. **No custom resolvers in Supabase** — pg_graphql is SQL-only. Complex logic (EMC category mapping, ghost node classification) stays in Edge Functions or client JS.
6. **Overhead for simple reads** — "Give me VP-ONT v1.2.3" is simpler via direct table query than GraphQL. GraphQL shines for **composed** queries, not simple lookups.

---

## 4. Architecture Decision: What Lives Where

| Logic | Location | Rationale |
|-------|----------|-----------|
| **Entity/relationship extraction from JSONB** | Postgres views + functions | SQL is fastest for JSONB traversal; views give GraphQL typed access |
| **EMC category → ontology mapping** | JS (emc-composer.js) or Edge Function | 11 category compositions are config data, not relational — keep in code |
| **PFI instance constraint** | JS (constrainToInstanceOntologies) | Two-layer filter logic is simpler in JS than SQL |
| **Cross-ontology edge detection** | Postgres function | Namespace prefix matching is a set operation — SQL excels here |
| **Ghost node classification** | JS (composition-filter.js) | Depends on active series set, toggle state — UI concern |
| **Graph composition (full)** | Postgres function (`resolve_composed_graph`) | Gives both GraphQL and Edge Functions access to same logic |
| **Entity search** | Postgres function (`search_entities`) | ILIKE + GIN index across JSONB is fast |

---

## 5. Schema Design: Lightweight JSON, Heavy Graph

### 5a. Ontologies Table (Extended from 001)

```sql
ontologies
├── id          UUID PK
├── pfi_id      UUID FK → pfi_instances
├── name        TEXT           -- 'VP-ONT', 'EMC-ONT'
├── version     TEXT           -- '1.2.3'
├── prefix      TEXT (NEW)     -- 'vp:', 'emc:'        ← extracted from JSONB
├── series      TEXT (NEW)     -- 'VE-Series'           ← extracted from JSONB
├── status      TEXT (NEW)     -- 'active'|'deprecated' ← lifecycle
├── ont_type    TEXT           -- 'pfc'|'pfi'
├── oaa_version TEXT (NEW)     -- '7.0.0'               ← extracted from JSONB
├── entity_count INT (NEW)    -- 17                     ← auto-synced
├── rel_count   INT (NEW)     -- 30                     ← auto-synced
├── rule_count  INT (NEW)     -- 22                     ← auto-synced
├── data        JSONB          -- THE WHOLE JSON-LD DOCUMENT
├── created_by  UUID FK
├── updated_by  UUID FK
└── timestamps
```

**Design decision:** The structured columns (`prefix`, `series`, `entity_count`, etc.) are **denormalised summaries** auto-synced via trigger. The JSONB `data` blob remains the source of truth. This gives GraphQL typed fields to filter/sort on without parsing JSONB at query time.

### 5b. Composed Graphs Table (New)

```sql
composed_graphs
├── id              UUID PK
├── pfi_id          UUID FK → pfi_instances
├── composition_id  TEXT           -- 'comp-MULTI-PFI-...'
├── categories      TEXT[]         -- ['PRODUCT', 'FULFILMENT']
├── context_level   TEXT           -- 'PFC'|'PFI'
├── product_code    TEXT           -- 'WWG'
├── maturity_level  INT            -- 1–5
├── nodes           JSONB          -- [{id, label, sourceNamespace, ...}]
├── edges           JSONB          -- [{from, to, isCrossOntology, ...}]
├── metadata        JSONB          -- {totalNodes, totalEdges, ...}
├── ontology_versions JSONB        -- {"VP-ONT":"1.2.3", ...}
├── is_canonical    BOOLEAN        -- frozen snapshot flag
├── created_by      UUID FK
├── created_at      TIMESTAMPTZ
└── expires_at      TIMESTAMPTZ    -- null = permanent
```

**Design decision:** Composed graphs are **materialised** — computed once, stored, queried many times. The visualiser can either:
- Call `resolve_composed_graph()` for a fresh composition (real-time)
- Query `composed_graphs` for a cached/canonical snapshot (fast)

### 5c. Views (JSONB → Typed Rows)

| View | Source | Purpose |
|------|--------|---------|
| `ontology_entities` | `ontologies.data->'entities'` | Flat entity rows with typed columns |
| `ontology_relationships` | `ontologies.data->'relationships'` | Flat edge rows with domain/range/cardinality |
| `ontology_rules` | `ontologies.data->'businessRules'` | Flat rule rows with severity |

These views are **read-only projections**. Mutations go through the `ontologies` table directly.

---

## 6. GraphQL Schema (Auto-Generated by pg_graphql)

Once migration 005 is applied, pg_graphql auto-generates this schema:

```graphql
type Query {
  # ── Auto-generated from tables ──────────────────────────────
  ontologiesCollection(
    filter: OntologiesFilter
    orderBy: [OntologiesOrderBy!]
    first: Int
    after: Cursor
  ): OntologiesConnection

  composedGraphsCollection(
    filter: ComposedGraphsFilter
    orderBy: [ComposedGraphsOrderBy!]
    first: Int
    after: Cursor
  ): ComposedGraphsConnection

  pfiInstancesCollection(
    filter: PfiInstancesFilter
  ): PfiInstancesConnection

  # ── Auto-generated from views ───────────────────────────────
  ontologyEntitiesCollection(
    filter: OntologyEntitiesFilter
    first: Int
  ): OntologyEntitiesConnection

  ontologyRelationshipsCollection(
    filter: OntologyRelationshipsFilter
    first: Int
  ): OntologyRelationshipsConnection

  ontologyRulesCollection(
    filter: OntologyRulesFilter
    first: Int
  ): OntologyRulesConnection

  # ── RPC functions (exposed via pg_graphql) ──────────────────
  resolveComposedGraph(
    pPfiCode: String!
    pCategories: [String!]
    pIncludeGhosts: Boolean
  ): JSON

  searchEntities(
    pPfiCode: String
    pQuery: String
    pNamespace: String
    pEntityType: String
    pLimit: Int
  ): JSON

  getOntologyDependencyGraph(
    pPfiCode: String
  ): JSON
}

type Ontologies {
  id: UUID!
  pfiId: UUID!
  name: String!
  version: String
  prefix: String
  series: String
  status: String
  ontType: String
  oaaVersion: String
  entityCount: Int
  relCount: Int
  ruleCount: Int
  data: JSON                    # raw JSONB — full ontology document
  createdAt: Datetime
  updatedAt: Datetime

  # ── Computed columns (from functions) ──────────────────────
  entityNames: [String]          # ontologies_entity_names()
  relationshipNames: [String]    # ontologies_relationship_names()
  crossReferences: [String]      # ontologies_cross_references()
  imports: [String]              # ontologies_imports()

  # ── Foreign key navigations ────────────────────────────────
  pfiInstance: PfiInstances       # → pfi_instances
}

type ComposedGraphs {
  id: UUID!
  pfiId: UUID!
  compositionId: String!
  categories: [String]!
  contextLevel: String!
  productCode: String
  maturityLevel: Int
  nodes: JSON
  edges: JSON
  metadata: JSON
  ontologyVersions: JSON
  isCanonical: Boolean
  createdAt: Datetime
  expiresAt: Datetime

  pfiInstance: PfiInstances
}

type OntologyEntities {
  ontologyId: UUID
  pfiId: UUID
  ontologyName: String
  prefix: String
  series: String
  entityId: String
  entityType: String
  entityName: String
  description: String
  properties: JSON
  newInVersion: String
  raw: JSON
}

type OntologyRelationships {
  ontologyId: UUID
  prefix: String
  relId: String
  relType: String
  relName: String
  description: String
  domainIncludes: JSON
  rangeIncludes: JSON
  cardinality: String
  crossOntologyRef: String
  isCrossOntology: Boolean
  raw: JSON
}
```

---

## 7. Example Queries

### 7a. Visualiser: List all ontologies with stats

```graphql
query ListOntologies($pfiId: UUID!) {
  ontologiesCollection(
    filter: { pfiId: { eq: $pfiId }, status: { eq: "active" } }
    orderBy: [{ series: AscNullsLast }, { name: AscNullsLast }]
  ) {
    totalCount
    edges {
      node {
        id
        name
        prefix
        series
        version
        entityCount
        relCount
        ruleCount
        entityNames
        crossReferences
        imports
      }
    }
  }
}
```

### 7b. Visualiser: Get entities for a specific ontology

```graphql
query GetOntologyEntities($ontologyId: UUID!) {
  ontologyEntitiesCollection(
    filter: { ontologyId: { eq: $ontologyId } }
  ) {
    edges {
      node {
        entityId
        entityName
        entityType
        description
        properties
      }
    }
  }
}
```

### 7c. Agent: Compose graph for PFI instance

```graphql
query ComposeGraph {
  resolveComposedGraph(
    pPfiCode: "W4M-WWG"
    pCategories: ["PRODUCT", "FULFILMENT"]
    pIncludeGhosts: true
  )
}

# Returns:
# {
#   "success": true,
#   "nodes": [
#     { "id": "vp::ValueProposition", "label": "ValueProposition",
#       "sourceNamespace": "vp:", "series": "VE-Series", ... },
#     ...
#   ],
#   "edges": [
#     { "from": ["vp:ValueProposition"], "to": ["vp:IdealCustomerProfile"],
#       "label": "Targets ICP", "cardinality": "1..*", ... },
#     ...
#   ],
#   "metadata": {
#     "pfiCode": "W4M-WWG",
#     "categories": ["PRODUCT", "FULFILMENT"],
#     "totalNodes": 82,
#     "totalEdges": 134
#   }
# }
```

### 7d. Agent: Search entities across all ontologies

```graphql
query SearchEntities {
  searchEntities(
    pPfiCode: "W4M-WWG"
    pQuery: "risk"
    pLimit: 20
  )
}
```

### 7e. Visualiser: Get cached canonical snapshot

```graphql
query GetCanonicalSnapshot($pfiId: UUID!) {
  composedGraphsCollection(
    filter: {
      pfiId: { eq: $pfiId }
      isCanonical: { eq: true }
    }
    orderBy: [{ createdAt: DescNullsLast }]
    first: 1
  ) {
    edges {
      node {
        compositionId
        categories
        nodes
        edges
        metadata
        ontologyVersions
        createdAt
      }
    }
  }
}
```

### 7f. Cross-ontology relationship query

```graphql
query CrossOntologyEdges($prefix: String!) {
  ontologyRelationshipsCollection(
    filter: {
      prefix: { eq: $prefix }
      isCrossOntology: { eq: true }
    }
  ) {
    edges {
      node {
        relId
        relName
        domainIncludes
        rangeIncludes
        cardinality
        crossOntologyRef
      }
    }
  }
}
```

---

## 8. Integration with Existing Layers

### Layer 1: Browser (Visualiser)

```javascript
// supabase-client.js — new module for visualiser
import { createClient } from '@supabase/supabase-js';

const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

// Option A: Use GraphQL endpoint directly
async function composeGraphQL(pfiCode, categories) {
  const { data } = await supabase.functions.invoke('graphql', {
    body: {
      query: `query { resolveComposedGraph(pPfiCode: "${pfiCode}", pCategories: ${JSON.stringify(categories)}) }`
    }
  });
  return data;
}

// Option B: Use RPC (simpler, no GraphQL parsing needed)
async function composeRPC(pfiCode, categories) {
  const { data } = await supabase.rpc('resolve_composed_graph', {
    p_pfi_code: pfiCode,
    p_categories: categories
  });
  return data;
}

// Both return the same shape — GraphQL adds typed filtering on top
```

### Layer 2: Claude Agent (MCP)

The existing `pfc_export_graph` MCP tool maps to `resolve_composed_graph()`:

```typescript
// In pfc-supabase MCP server
case "pfc_export_graph": {
  const { pfi_code, categories, format } = args;

  // Call the same SQL function via Edge Function
  const result = await callEdgeFunction('/graph/export', {
    pfi_code,
    categories,
    format: format || 'json'
  });

  // Edge Function internally calls:
  // SELECT resolve_composed_graph(pfi_code, categories)
  return result;
}
```

### Layer 3: Third-party (REST API)

Third-party consumers can call `/graphql/v1` with an API key (future — needs Edge Function proxy for API key auth, since pg_graphql only accepts JWTs natively).

---

## 9. Migration Path: From Client-Side to Server-Side Composition

### Phase 1: MVP (Now)

| What | Where | How |
|------|-------|-----|
| Ontology storage | Supabase JSONB | Migration 001 + 005 |
| Entity/rel views | Postgres views | Migration 005 |
| Basic composition | Postgres function | `resolve_composed_graph()` |
| Entity search | Postgres function | `search_entities()` |
| EMC categories | Client JS | `emc-composer.js` (unchanged) |
| PFI constraint | Client JS | `constrainToInstanceOntologies()` (unchanged) |
| Ghost nodes | Client JS | `composition-filter.js` (unchanged) |

### Phase 2: Server-Side EMC (Future)

| What | Where | How |
|------|-------|-----|
| EMC category config | Postgres table | New `emc_categories` table with category → ontology mappings |
| Full composition | Postgres function | Enhanced `resolve_composed_graph()` reads categories from table |
| PFI constraint | Postgres function | Joins `pfi_instances.instanceOntologies` with composition |
| Ghost classification | Postgres function | Series membership check in SQL |
| Canonical snapshots | `composed_graphs` table | Frozen compositions with ontology version pinning |

### Phase 3: Real-Time (Future)

| What | Where | How |
|------|-------|-----|
| Live graph updates | pg_graphql subscriptions | `subscription { composedGraphsCollection { ... } }` |
| Collaborative editing | Supabase Realtime | Broadcast channel per PFI + composed graph ID |

---

## 10. What NOT to Do

| Anti-Pattern | Why | Instead |
|--------------|-----|---------|
| Normalise entities into a separate table | Premature — adds joins, migration complexity, and sync overhead for no MVP benefit | Keep entities in JSONB, project via views |
| Build GraphQL resolvers outside Postgres | Adds a Node.js/Deno server to maintain | Use pg_graphql computed columns + SQL functions |
| Replace emc-composer.js with SQL | EMC category logic is config-driven and changes frequently | Keep in JS, call SQL for data extraction only |
| Use GraphQL for simple CRUD | Supabase-js direct table access is simpler for basic reads/writes | Use GraphQL for composed/cross-ontology queries only |
| Expose raw JSONB through GraphQL | Defeats the purpose — clients still parse untyped data | Always project through views or computed columns |
| Skip materialised compositions | Re-computing graphs on every request is wasteful for canonical snapshots | Store in `composed_graphs`, serve from cache |

---

## 11. Token Impact (Agent Path)

GraphQL doesn't change the MCP tool count (still 5 tools). The benefit is server-side:

| Before (REST-only) | After (GraphQL + REST) |
|---------------------|------------------------|
| Agent calls `pfc_query_ontologies` → Edge Function → raw SELECT → returns whole JSONB documents | Agent calls `pfc_query_ontologies` → Edge Function → GraphQL subquery → returns exactly the fields needed |
| Response: ~50KB per ontology (full JSON-LD) | Response: ~5KB per ontology (projected fields only) |
| Agent parses entities/relationships in context window | Server pre-extracts entities/relationships |

**Net effect:** Same token cost for tool definitions, **10x smaller payloads** for data transfer. This matters because agent context windows are finite and ontology documents are large.

---

## 12. Decision Points

| # | Decision | Options | Recommendation |
|---|----------|---------|----------------|
| D1 | When to use GraphQL vs RPC | GraphQL for multi-entity composed queries; RPC for single-function calls | **Both** — GraphQL for visualiser, RPC for agents |
| D2 | View refresh strategy | On-demand (current) vs materialised views | On-demand (views are fast on <100 ontologies) |
| D3 | Composed graph caching | No cache / TTL-based / canonical-only | **Canonical-only** — ephemeral compositions are cheap to recompute |
| D4 | GraphQL client library | urql / Apollo / fetch | **Plain fetch** — pg_graphql is simple enough; no client framework needed |
| D5 | Phase 2 timing | With MVP / after MVP / deferred | **After MVP** — prove the pattern first |

---

*Briefing: GraphQL + Supabase JSONB — MVP Architecture v1.0.0*
*Azlan-EA-AAA Platform Foundation*
*26 February 2026*
