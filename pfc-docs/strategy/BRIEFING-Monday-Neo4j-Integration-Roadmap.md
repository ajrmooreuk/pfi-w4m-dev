# Neo4j Integration Roadmap: Hypotheses, Pipelines & Initial Steps

**Monday Team Meeting Briefing | 24 February 2026**
**Companion to:** PF-CORE-ADR-2026-001 v1.1 (Neo4j vs JSON-LD + Claude SDK)
**Context:** Epic 34 Strategy, Epic 19 External Data Connectors, Epic 37 Threat Modelling

---

## 1. Where We Are Today

### What We Already Have (Built)

| Asset | Status | Neo4j Relevance |
|-------|--------|-----------------|
| **cypher-export.js** (visualiser) | Production (v5.3.0) | Generates Neo4j 5.x Cypher scripts from any OAA v6 ontology. Handles constraints, MERGE imports, entity CREATE, relationships, join patterns, business rules. **This is our export pipeline foundation.** |
| **42 ontologies** in JSON-LD | Production | Each can be exported to Cypher today via the visualiser. That's 42 ready-made Neo4j graph schemas. |
| **EMC-ONT v4.0.0** composition engine | Production | Knows how to compose multi-ontology graphs. The composition output could target Neo4j instead of (or alongside) vis-network. |
| **emc-composer.js** | Production | 8 requirement categories, 7 composition rules, 5 graph patterns. Runtime composition logic that could drive Neo4j graph assembly. |
| **Supabase JSONB schema** | Designed (ADR-001) | Ontologies stored as JSONB. Can serve as staging area for Neo4j sync. |
| **MCP integration** (Claude Code) | Active | We already use MCP for Figma. Neo4j MCP Server is a direct plug-in. |

### What We Don't Have Yet

| Gap | Effort | Dependency |
|-----|--------|------------|
| Neo4j instance (even free tier) | 15 min setup | None |
| JSON-LD ingest INTO Neo4j (reverse of export) | Medium | cypher-export.js exists as template |
| Claude SDK agent that reads FROM Neo4j | Medium | Neo4j MCP Server or JS driver |
| EMC ExternalDataSource/Sink entities | Designed in schema (#236, #246) but not implemented | Epic 19 F19.6/F19.7 |
| Multi-ontology Cypher export (composed graph) | Small | emc-composer.js + cypher-export.js wiring |

---

## 2. The ADR Decision (Recap for Team)

**PF-CORE-ADR-2026-001 v1.1: ACCEPTED**

> JSON-LD + Supabase JSONB + Claude SDK = primary graph reasoning layer.
> Neo4j = specialist escalation option, not default.
> For Microsoft-stack clients: Fabric Native Graph preferred over Neo4j (zero extra licence).

This briefing is about **how we prepare the escalation path** so it's ready when clients need it, without over-investing now.

---

## 3. Architecture: Three Graph Layers

```
                                    ┌─────────────────────────────────┐
                                    │  L3: ANALYTICS GRAPH            │
                                    │  Neo4j AuraDB / Fabric Graph    │
                                    │  Multi-hop traversals, GDS      │
                                    │  algorithms, fraud rings,       │
                                    │  network analysis               │
                                    │  (WHEN NEEDED — client-driven)  │
                                    └──────────┬──────────────────────┘
                                               │ Cypher export / sync
                                    ┌──────────┴──────────────────────┐
                                    │  L2: OPERATIONAL GRAPH          │
                                    │  Supabase JSONB + Claude SDK    │
                                    │  Agent reasoning, structured    │
                                    │  outputs, 2-4 hop traversals,   │
                                    │  RBAC, multi-tenant RLS         │
                                    │  (PRIMARY — all clients)        │
                                    └──────────┬──────────────────────┘
                                               │ JSON-LD native
                                    ┌──────────┴──────────────────────┐
                                    │  L1: ONTOLOGY DESIGN GRAPH      │
                                    │  JSON-LD files + OAA Registry   │
                                    │  42 ontologies, 5 series, EMC   │
                                    │  composition, zero-build-step   │
                                    │  visualiser                     │
                                    │  (FOUNDATION — always present)  │
                                    └─────────────────────────────────┘
```

**Key insight:** L1 feeds L2 feeds L3. Each layer adds cost and complexity. Most clients never need L3. The pipeline between layers is what we're designing.

---

## 4. Claude-to-Neo4j Pipeline (What Exists, What's Needed)

### 4.1 Export: Claude/JSON-LD --> Neo4j

**Today (working):**
```
OAA Ontology (JSON-LD)
    │
    ▼ cypher-export.js
Neo4j Cypher Script (.cypher file)
    │
    ▼ Manual: paste into Neo4j Browser / Desktop
Neo4j Graph Database
```

**Target (automated, 2-3 sprints):**
```
Claude SDK Agent Session
    │ structured output (JSON-LD)
    ▼
Supabase JSONB (operational store)
    │ webhook / trigger
    ▼
Neo4j Sync Agent (new)
    │ Uses: cypher-export.js logic + neo4j-driver
    ▼
Neo4j AuraDB (analytics layer)
    │
    ▼ Power BI via Simba connector OR Supabase views
Dashboard
```

**Components needed for automated pipeline:**

| Component | Build/Buy | Effort | Notes |
|-----------|-----------|--------|-------|
| **neo4j-sync-agent.js** | Build | 2-3 days | Wraps cypher-export.js logic, calls Neo4j JS driver. Can run as Supabase Edge Function or standalone. |
| **Neo4j JS driver (browser)** | CDN | 0 effort | `import neo4j from 'https://unpkg.com/neo4j-driver@5.x.x/lib/browser/neo4j-web.esm.min.js'` — zero build step compatible |
| **Composed graph export** | Build | 1-2 days | Wire emc-composer.js output through cypher-export.js to generate multi-ontology Cypher |
| **Incremental sync** | Build | 3-5 days | MERGE-based idempotent sync (cypher-export.js already uses MERGE for imports) |
| **Auth proxy** | Build | 1-2 days | Edge function to hide Neo4j credentials from browser. Required for production. |

### 4.2 Import: Neo4j --> Claude/JSON-LD

**Three paths, increasing sophistication:**

**Path A: MCP Server (simplest, recommended first step)**
```
Claude Code / Claude Desktop
    │ MCP protocol
    ▼
Neo4j MCP Server (official, open source)
    │ Tools: get-neo4j-schema, read-neo4j-cypher, write-neo4j-cypher
    ▼
Neo4j AuraDB
```
- Install: `npm install @neo4j/mcp-server` + config in `.claude/settings`
- Claude can then query any Neo4j graph conversationally
- Zero code to write — configuration only

**Path B: GraphRAG (for knowledge graph construction from client docs)**
```
Unstructured docs (PDFs, web pages, briefs)
    │ neo4j-graphrag Python + AnthropicLLM
    ▼
Claude extracts entities + relationships
    │
    ▼
Neo4j Knowledge Graph
    │ GraphRAG retrieval
    ▼
Claude agent with graph-grounded context
```
- `pip install "neo4j_graphrag[anthropic]"` — Claude is first-class
- Useful for: client onboarding (ingest their existing docs into graph), competitive intelligence, regulatory corpus

**Path C: Bidirectional Sync (future, Epic 19 F19.6/F19.7)**
```
Neo4j AuraDB ◄──────────────► Supabase JSONB
                sync agent
                (Cypher ↔ JSON-LD)
```
- Requires ExternalDataSource/Sink schemas (S19.6.1, S19.7.4 — already designed)
- EMC connector types include `neo4j-bolt` — schema-ready, code not yet built

---

## 5. Neo4j Products: Gradual Pull-In Roadmap

### Phase 0: Free Exploration (Now — Week 1)

| Product | Action | Cost | Value |
|---------|--------|------|-------|
| **Neo4j Desktop** | Install locally. Import one ontology via cypher-export.js output. | Free | Proves the pipeline works. Demo material for Monday. |
| **AuraDB Free** | Create cloud instance (200K nodes, 400K rels). Load VHF 6-ontology composition. | Free | Cloud-hosted demo. Shareable with team. All 42 ontologies fit in free tier. |
| **Neo4j MCP Server** | Configure in Claude Code settings. | Free (OSS) | Claude can query the graph immediately. Conversational graph exploration. |

### Phase 1: Developer Tooling (Month 1-2)

| Product | Action | Cost | Value |
|---------|--------|------|-------|
| **Neo4j Browser** (bundled) | Use for Cypher query development and visual graph exploration alongside visualiser. | Free | Side-by-side: visualiser for ontology design, Neo4j Browser for graph analytics. |
| **APOC Procedures** (bundled) | Enable JSON import/export, path expansion, graph algorithms. | Free | `apoc.export.json.all` exports Neo4j → JSON. `apoc.load.json` imports JSON-LD. |
| **mcp-neo4j-data-modeling** | Schema design + Mermaid viz + Arrows JSON from Claude. | Free (OSS) | Claude-assisted graph schema evolution. |

### Phase 2: Client-Facing Analytics (Month 3-6, when needed)

| Product | Action | Cost | Value Proposition |
|---------|--------|------|-------------------|
| **AuraDB Professional** | Upgrade for client-facing instance. Daily backups, query logs. | ~GBP 65/mo (1GB) | **Insurance client:** claims network analysis, risk exposure traversals. **MarTech:** customer journey graphs for enterprise clients. |
| **Neo4j Bloom** (bundled with Pro) | Visual graph exploration for non-technical client stakeholders. | Included | Client-facing demo: "Here's your enterprise model as an interactive graph." |
| **Graph Data Science (GDS)** | 65+ algorithms: PageRank, Louvain community detection, node similarity. | Included with Pro | **Insurance:** fraud ring detection. **MarTech:** audience segmentation, content recommendation. |

### Phase 3: Enterprise Scale (Month 12-18, client-driven)

| Product | Action | Cost | Value Proposition |
|---------|--------|------|-------------------|
| **Neo4j Fabric Workload** | Deploy within client's existing Microsoft Fabric. | AuraDB Pro + Fabric CU | Insurance clients already on Fabric. Zero procurement friction. Entra ID SSO. |
| **Fabric Native Graph** | Alternative for clients who want zero extra licence. | Fabric CU only | GQL (ISO standard), LinkedIn-scale engine. Monitor for GA readiness. |
| **Aura Agent** | Auto-generate AI agents from Neo4j graph schemas. | TBC (new product) | Our ontologies become the schema. Aura Agent generates tools + prompts automatically. Potential for PFI agent auto-generation. |

---

## 6. Value Propositions by Client Archetype

### Insurance Advisory (GBP 100M)

| Use Case | JSON-LD + Claude (Today) | + Neo4j (When Needed) |
|----------|--------------------------|----------------------|
| **Risk exposure mapping** | Agent traverses ORG-CONTEXT → risk entities. 2-3 hops. Sufficient for most queries. | 5+ hop traversals across claims → policies → agents → reinsurers. GDS path algorithms. |
| **Fraud ring detection** | Not feasible beyond basic pattern matching. | Louvain community detection, connected component analysis. This is where Neo4j earns its keep. |
| **PRA SS1/21 resilience** | VSOM ontology maps impact tolerances. Agent generates compliance evidence. | Graph-based dependency mapping: "If system X fails, which business services are impacted?" GDS influence propagation. |
| **Regulatory reporting** | Structured outputs from GRC-FW ontology. | Graph-powered audit trail: full governance chain from board policy to security control to evidence. |
| **Customer 360** | CRT-ONT role taxonomy + VP-ONT value propositions. | Full network: customer → policies → claims → intermediaries → reinsurers. Neo4j Bloom for relationship exploration. |

**Trigger to escalate:** Client asks for fraud detection, network analysis, or 5+ hop dependency mapping.

### MarTech SaaS MVP

| Use Case | JSON-LD + Claude (Today) | + Neo4j (When Needed) |
|----------|--------------------------|----------------------|
| **Campaign attribution** | Agent reasons over VP → PMF → KPI chain. Single-touch and last-touch. | Multi-touch attribution across full customer journey graph. GDS similarity algorithms for lookalike audiences. |
| **Audience segmentation** | ORG-CONTEXT competitive landscape + CRT-ONT customer roles. | Graph-based clustering: customers who share behaviours, content interactions, purchase patterns. |
| **Content recommendation** | Agent uses ontology relationships to suggest related content. | Collaborative filtering via node similarity. |
| **Competitive intelligence** | INDUSTRY-ONT (Porter's 5 Forces) + ORG-CONTEXT competitors. | Competitive network graph: shared customers, overlapping channels, market position dynamics. |

**Trigger to escalate:** Enterprise client demands real-time graph analytics or brings existing Neo4j investment.

---

## 7. Hypotheses to Test

### H1: Cypher Export Fidelity (Week 1)

> **Hypothesis:** Our existing cypher-export.js produces Cypher scripts that load cleanly into Neo4j 5.x without manual editing.
>
> **Test:** Export the VHF 6-ontology composition, paste into Neo4j Desktop, verify all nodes, relationships, and constraints are created correctly.
>
> **Success criteria:** Zero Cypher syntax errors. All entities and relationships present. Constraint violations caught.
>
> **Risk:** OAA v6.2.0 object-keyed format may produce edge cases. cypher-export.js handles both v5 and v6.2.0 but hasn't been validated against a live Neo4j instance.

### H2: Free Tier Capacity (Week 1)

> **Hypothesis:** All 42 PF-Core ontologies fit within AuraDB Free tier (200K nodes, 400K relationships).
>
> **Test:** Export all 42 ontologies to Cypher, load into AuraDB Free, measure node/relationship counts.
>
> **Success criteria:** Total graph fits within limits. Estimated: ~500-800 entities across 42 ontologies, ~2,000-3,000 relationships. Well within 200K/400K.
>
> **Risk:** Instance data (PFI-BAIV, PFI-VHF, etc.) will add volume. Need to measure instance data separately.

### H3: MCP Conversational Graph (Week 1-2)

> **Hypothesis:** Claude Code with Neo4j MCP Server can answer ontology questions by querying the graph directly, without loading JSON-LD files.
>
> **Test:** Load EMC full enterprise composition into Neo4j. Configure MCP Server. Ask Claude: "Which ontologies are required for an AGENTIC scope at PFI level?" and "What is the agent-role-RBAC chain for the BAIV instance?"
>
> **Success criteria:** Claude generates correct Cypher queries and returns accurate answers grounded in the graph data.
>
> **Risk:** LLM-generated Cypher reliability. The ADR flags this as a known weakness. May need pre-built Cypher templates.

### H4: Composed Graph Export (Week 2-3)

> **Hypothesis:** emc-composer.js output can drive cypher-export.js to produce a single composed Cypher script that creates a multi-ontology graph with cross-references intact.
>
> **Test:** Run EMC ENTERPRISE composition (all 5 series). Export to Cypher. Load into Neo4j. Verify cross-ontology relationships (VP↔RRR, CRT↔VP, agent bridges).
>
> **Success criteria:** Cross-ontology edges render correctly. Join patterns traversable via Cypher. Business rules enforceable via APOC triggers.

### H5: Browser WebSocket Connectivity (Week 2-3)

> **Hypothesis:** The Neo4j JavaScript driver can connect from the visualiser (zero-build-step browser app) to AuraDB via WebSocket without a bundler.
>
> **Test:** Add CDN import of neo4j-driver ESM bundle. Establish connection from browser. Execute read query. Display result in visualiser.
>
> **Success criteria:** Connection established. Query returns data. No build step required.
>
> **Risk:** Credentials exposed in browser. Acceptable for dev/demo. Production requires auth proxy (Edge Function).

### H6: GraphRAG for Client Onboarding (Month 2-3)

> **Hypothesis:** Claude + neo4j-graphrag can extract entities and relationships from a client's existing documents (strategy decks, org charts, risk registers) and build a knowledge graph that aligns with our OAA ontology schemas.
>
> **Test:** Take a sample insurance client strategy document. Use GraphRAG with AnthropicLLM to extract entities. Map extracted entities to VSOM/RRR/ORG-CONTEXT ontology types.
>
> **Success criteria:** 70%+ of extracted entities map to existing ontology types. Remaining 30% identify ontology extension opportunities.
>
> **Risk:** Entity extraction quality varies with document structure. Need prompt engineering for OAA alignment.

---

## 8. Initial Steps

### Week 1 Sprint (Post-Meeting)

| # | Action | Owner | Effort | Tests H# |
|---|--------|-------|--------|----------|
| 1 | Install Neo4j Desktop + create AuraDB Free instance | Dev | 30 min | H2 |
| 2 | Export VHF 6-ontology composition via cypher-export.js, load into both targets | Dev | 2 hrs | H1, H2 |
| 3 | Configure Neo4j MCP Server in Claude Code `.claude/settings` | Dev | 1 hr | H3 |
| 4 | Test conversational queries via MCP | Dev | 2 hrs | H3 |
| 5 | Document results in ADR-LOG.md as ADR-014: Neo4j Integration Evaluation | SA | 2 hrs | — |

### Week 2-3 Sprint

| # | Action | Owner | Effort | Tests H# |
|---|--------|-------|--------|----------|
| 6 | Wire emc-composer.js output through cypher-export.js for composed graph export | Dev | 3-5 days | H4 |
| 7 | Test browser WebSocket connection (CDN neo4j-driver ESM) in visualiser | Dev | 2-3 days | H5 |
| 8 | Build auth proxy Edge Function (Supabase) for Neo4j credentials | Dev | 1-2 days | H5 |
| 9 | Create `emc:ExternalDataSink` type `neo4j-bolt` implementation stub | Dev | 1 day | — |

### Month 2-3 (If Hypotheses Validated)

| # | Action | Owner | Effort | Tests H# |
|---|--------|-------|--------|----------|
| 10 | GraphRAG PoC: client doc → knowledge graph → ontology alignment | Dev + SA | 5-7 days | H6 |
| 11 | Neo4j Bloom demo for insurance advisory client pitch | SA | 2 days | — |
| 12 | APOC JSON import: Neo4j → JSON-LD round-trip test | Dev | 2-3 days | — |
| 13 | Evaluate Aura Agent for PFI agent auto-generation | SA | 3 days | — |

---

## 9. Decision Points (Review Gates)

| Gate | Timing | Question | Decision |
|------|--------|----------|----------|
| **G1** | End of Week 1 | Does cypher-export.js produce clean Cypher? Does MCP work? | If yes → proceed to composed export (Week 2). If no → fix export bugs first. |
| **G2** | End of Week 3 | Can composed multi-ontology graphs load into Neo4j with cross-references? | If yes → Neo4j becomes a validated analytics layer option. If no → investigate graph model mismatch. |
| **G3** | End of Month 2 | Does browser WebSocket work within zero-build-step constraint? | If yes → optional Neo4j panel in visualiser. If no → server-side only (Edge Function proxy). |
| **G4** | End of Month 3 | Does GraphRAG produce useful knowledge graphs from client documents? | If yes → add to client onboarding workflow. If no → keep manual ontology seeding. |
| **G5** | Month 6 | Does any client actually need L3 analytics? | If yes → spin up AuraDB Professional for that client. If no → Neo4j stays in toolbox, not in stack. |

---

## 10. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| **Over-investment in Neo4j before client demand** | Medium | High (wasted effort) | Gate system (G1-G5). Each step is independently valuable. Stop at any gate. |
| **LLM-generated Cypher unreliability** | High | Medium | Pre-built Cypher templates for common queries. Don't rely on ad-hoc Cypher generation for production. |
| **Browser credential exposure** | Certain (if direct) | High (security) | Auth proxy mandatory for production. Free tier acceptable for internal dev/demo only. |
| **Fabric Native Graph stays in preview** | Medium | Low | Doesn't block anything. JSON-LD + Claude SDK is the primary layer regardless. |
| **cypher-export.js edge cases** | Medium | Low | H1 test catches these. Fix and add test coverage. |
| **Aura Agent product maturity** | Medium | Low | Evaluate, don't depend on. Our agent architecture (Claude SDK + OAA) is the primary path. |

---

## 11. Competitive Positioning Impact

### What This Enables Us to Say

**To clients:**
> "We design your enterprise model as governed ontologies. You get graph reasoning from day one via our AI agents. When you need graph analytics — fraud detection, network analysis, dependency mapping — we export your model to Neo4j or Fabric Graph with a single click. No rework. No migration. Your ontology IS the graph schema."

**To Neo4j-led competitors:**
> "They start with a database and work backwards to governance. We start with governance and work forwards to any database. Our ontologies generate Neo4j schemas automatically. Their Cypher queries can't generate ontologies."

**To Microsoft partners:**
> "We work with Fabric, not against it. When your client has Fabric capacity, their graph analytics are included at zero extra cost. We add the reasoning layer that Fabric doesn't have — semantically governed AI agents that understand the business model, not just the data model."

---

## 12. Neo4j Product Landscape (Reference)

### Pricing Tiers (Feb 2026)

| Tier | Cost | Capacity | Key Features |
|------|------|----------|-------------|
| **AuraDB Free** | GBP 0 | 200K nodes, 400K rels | Single DB, community support. **More than enough for all 42 ontologies.** |
| **AuraDB Professional** | ~GBP 65/GB/mo | Up to 128GB RAM | Daily backups, query log analyser, vector search, Bloom. |
| **AuraDB Business Critical** | ~GBP 146/GB/mo | Up to 512GB RAM | Multi-zone HA, 99.95% SLA, RBAC, SSO, 24/7 support. |
| **Neo4j Desktop** | Free | Local only | Enterprise Edition features, single-machine developer licence. |

### Claude/Anthropic Integration Points

| Integration | Maturity | Relevance |
|-------------|----------|-----------|
| **Neo4j MCP Server** (official) | Production | Schema retrieval, read/write Cypher, GDS algorithms. Direct plug-in to Claude Code. |
| **neo4j-graphrag Python** (`AnthropicLLM`) | Production | Vector + graph retrieval, Text2Cypher, community summaries. Claude is first-class provider. |
| **LLM Knowledge Graph Builder** | Labs | Claude-supported entity extraction from unstructured docs into Neo4j. |
| **mcp-neo4j-memory** | Community | Neo4j as Claude's persistent conversation memory (knowledge graph). |
| **mcp-neo4j-data-modeling** | Community | Schema design + Mermaid viz + Arrows JSON. |
| **Aura Agent** | Preview (new) | Auto-generates agents from graph schemas. Can be wrapped as MCP server. |

### Browser Connectivity (Zero-Build-Step Compatible)

```javascript
// ES module import via CDN — no bundler required
import neo4j from 'https://unpkg.com/neo4j-driver@5.x.x/lib/browser/neo4j-web.esm.min.js'

const driver = neo4j.driver(URI, neo4j.auth.basic(USER, PASSWORD))
const session = driver.session()
const result = await session.run('MATCH (n) RETURN n LIMIT 10')
```

**Security caveat:** Credentials visible in client-side code. Production requires auth proxy.

---

## 13. Summary for Monday

1. **The ADR stands:** JSON-LD + Claude SDK primary. Neo4j is an escalation option.
2. **We already have the export pipeline** (cypher-export.js). It just hasn't been tested against a live Neo4j instance. That's a 2-hour task.
3. **Neo4j MCP Server** gives Claude instant graph query capability. Configuration-only, no code.
4. **Free tier is more than enough** for all 42 ontologies + instance data. No cost until a client needs Professional features.
5. **Six hypotheses** to validate in 3 weeks. Each is independently valuable. Gate system prevents over-investment.
6. **The pipeline direction is:** Ontology (JSON-LD) → Claude SDK (reasoning) → Neo4j (analytics, when needed). Not the reverse.
7. **GraphRAG** (Month 2-3) is the most strategically interesting Neo4j capability — turns client documents into ontology-aligned knowledge graphs automatically.
8. **Fabric Native Graph** (GQL, zero extra licence) is preferred over Neo4j for Microsoft-stack clients. Neo4j only if 65+ algorithm library is specifically required.

**Decision needed Monday:** Approve Week 1 sprint (steps 1-5). Estimated 8 hours total across the team.
