# Operating Guide: Creating & Freezing a PFI Product-Specific Graph

**Target**: BAIV-AIV Discovery Build (Phase 1)
**Date**: 2026-02-22
**Prerequisites**: EMC-ONT v5.1.0, Ontology Visualiser v4.5.0+, Epic 19 core features (F19.1-F19.5)

---

## Overview

This guide walks through the full pipeline for creating, composing, freezing, and using a PFI (PF-Instance) product-specific graph. The BAIV-AIV product graph serves as the reference implementation.

**Pipeline flow:**

```
Load Registry → Create PFI Instance → Load Instance Data → Populate Scope Rules
    → Resolve Product Context → Evaluate Scope Rules → Compose Instance Graph
    → Resolve Entity Bindings → Freeze as Canonical Snapshot → Generate PFI Graph
    → (Optional) Filter to Persona Workflow
```

---

## Step 1: Create the PFI Instance

The PFI instance defines which product this graph serves, which requirement categories it covers, and the organisational context.

```javascript
import { createPFIInstance } from './js/emc-composer.js';

const result = createPFIInstance({
  instanceId: 'PFI-BAIV',
  productCode: 'BAIV-AIV',
  instanceName: 'BAIV AI Visibility',
  description: 'B2B MarTech product graph — VP, RRR, EFS ontologies with BAIV instance data',
  requirementScopes: ['PRODUCT', 'COMPETITIVE', 'STRATEGIC'],
  maturityLevel: 1,        // Startup maturity (1-5 scale)
  complianceScope: false,  // No GRC overlay for discovery build
});

// result.instance contains the stored PFI config
// result.compositions contains one composition per requirementScope
```

**What this does:**
- Composes ontology sets for each requirement scope (PRODUCT, COMPETITIVE, STRATEGIC)
- Merges required/recommended ontologies across all scopes
- Stores the instance config in `state.pfiInstances`
- Persists to localStorage

**BAIV-AIV requirement scopes explained:**
| Scope | Core Ontologies | Purpose |
|-------|----------------|---------|
| PRODUCT | VP, PMF, PE, EFS, ORG | Value propositions, product-market fit, delivery |
| COMPETITIVE | CA, CL, GA, ORG | Competitive analysis, gap analysis |
| STRATEGIC | VSOM, OKR, ORG | Vision, strategy, objectives |

---

## Step 2: Load Instance Data Files

Instance data files contain the actual BAIV-specific entities (problems, solutions, roles, stories, etc).

```javascript
import { loadPFIInstanceData } from './js/pfi-loader.js';

const dataResult = await loadPFIInstanceData('PFI-BAIV', registryIndex);
// Fetches all instanceDataFiles[] in parallel
// Parses each with parseOntology()
// Stores in state.pfiInstanceData
```

**Current BAIV instance data files available:**
| Ontology | File | Content |
|----------|------|---------|
| VP-ONT | `vp-airl-instance-v1.0.0.jsonld` | AIRL value propositions (reference) |
| RRR-ONT | `RRR-PFC-PFI-RBAC-BAIV-ONT/` | BAIV RBAC implementation plan |
| DS-ONT | `baiv-app-skeleton-v1.0.0.jsonld` | BAIV app skeleton config |
| DS-ONT | `baiv-ds-instance-v1.0.0.jsonld` | BAIV design system instance |

**Action needed for Discovery Build:** Create BAIV-specific VP instance data (problems, solutions, benefits, ICPs) and EFS instance data (epics, features, stories). These are the core entities that populate the product graph.

---

## Step 3: Populate Scope Rules from EMC Ontology

Scope rules are declarative rules in EMC-ONT that govern what data appears in the composed graph.

```javascript
import { populateScopeRulesFromEMC } from './js/emc-composer.js';

const emcData = /* loaded EMC-ONT v5.1.0 JSON-LD */;
const ruleCount = populateScopeRulesFromEMC(emcData);
// Reads all GraphScopeRule instances from EMC ontology
// Sorts by priority ascending
// Stores in SCOPE_RULES map
```

**BAIV has 3 pre-defined scope rules:**

| Priority | Rule ID | Action | Effect |
|----------|---------|--------|--------|
| 1 | BAIV-AIV-Product-Scope | include-data | Include VP, RRR, EFS entities where productContext == BAIV-AIV |
| 2 | BAIV-MarTech-Market-Scope | include-data | Include ORG-CONTEXT, INDUSTRY entities for MarTech vertical |
| 3 | BAIV-Startup-Maturity-Scope | exclude-data | Exclude KPI, GA entities because maturity < 3 (startup) |

**How rules work:**
- Rules evaluate conditions against the product context (Step 4)
- include-data rules whitelist specific namespaces/entity types
- exclude-data rules blacklist namespaces/entity types
- Higher priority rules fire first; conflicts resolved by priority

---

## Step 4: Resolve Product Context

The product context is a data bag assembled from the PFI instance config and loaded instance data.

```javascript
import { resolveProductContext } from './js/emc-composer.js';

const context = resolveProductContext('PFI-BAIV');
```

**Returns for BAIV-AIV:**
```javascript
{
  products: ['BAIV-AIV'],
  brands: ['BAIV'],
  marketSegments: ['MarTech'],
  maturityLevel: 1,
  jurisdictions: ['UK', 'EU'],
  verticalMarket: 'MarTech',
  requirementScopes: ['PRODUCT', 'COMPETITIVE', 'STRATEGIC'],
  icpHierarchy: [/* ICP entries from VP-ONT instance data */],
  icpSeniority: null,
  personaScope: null,
  gapSeverity: null,
}
```

This context bag is what scope rules evaluate their conditions against.

---

## Step 5: Evaluate Scope Rules

```javascript
import { evaluateScopeRules } from './js/emc-composer.js';

const scopeResult = evaluateScopeRules('PFI-BAIV', context);
```

**Returns:**
```javascript
{
  includedNamespaces: Set(['vp:', 'rrr:', 'efs:', 'org-ctx:', 'ind:']),
  excludedNamespaces: Set(['kpi:', 'ga:']),
  includedEntityTypes: Set([
    'vp:ValueProposition', 'vp:IdealCustomerProfile',
    'rrr:Role', 'rrr:Responsibility',
    'efs:Epic', 'efs:Feature', 'efs:Story',
    'org-ctx:CompetitiveLandscape', 'org-ctx:Competitor',
    'ind:IndustryAnalysis'
  ]),
  excludedEntityTypes: Set(['kpi:KPI', 'kpi:MetricDefinition', 'ga:GapAnalysis', 'ga:Threat']),
  personaScope: null,
  ruleLog: [
    { ruleId: 'BAIV-AIV-Product-Scope', priority: 1, fired: true, action: 'include-data' },
    { ruleId: 'BAIV-MarTech-Market-Scope', priority: 2, fired: true, action: 'include-data' },
    { ruleId: 'BAIV-Startup-Maturity-Scope', priority: 3, fired: true, action: 'exclude-data' },
  ]
}
```

**Why KPI and GA are excluded:** BAIV is maturity level 1 (startup). The maturity-scope rule excludes advanced analytics (KPIs, gap analysis) until the organisation reaches maturity 3+. This keeps the discovery graph focused on core product/market entities.

---

## Step 6: Compose the Instance Graph

```javascript
import { composeInstanceGraph } from './js/emc-composer.js';

const composedGraph = composeInstanceGraph('PFI-BAIV', scopeResult);
```

**What this does:**
1. Filters loaded instance data by included/excluded namespaces and entity types
2. Prefixes node/edge IDs with namespace (e.g. `vp::Problem-1`)
3. Resolves cross-ontology joins from the ComposedGraphSpec:
   - `vp:IdealCustomerProfile` -> `rrr:Role` (icpMapsToRole)
   - `rrr:Role` -> `org:Organisation` (roleWithinOrg)
   - `vp:Problem` -> `efs:Epic` (problemAddressedByEpic)

**Returns:**
```javascript
{
  success: true,
  nodes: [/* all entities from VP, RRR, EFS, ORG-CONTEXT that match scope rules */],
  edges: [/* internal edges + cross-ontology join edges */],
  metadata: {
    ontologySources: ['VP-ONT', 'RRR-ONT', 'EFS-ONT', 'ORG-CONTEXT-ONT'],
    joinCount: 3,
    entityCount: /* total nodes */,
    edgeCount: /* total edges */,
  }
}
```

---

## Step 7: Resolve Entity Bindings

Entity bindings tag every entity with its product affiliation and ICP scope.

```javascript
import {
  resolveProductBindings, resolveICPBindings, inferProductBindings
} from './js/emc-composer.js';

// Explicit bindings: every loaded entity -> BAIV-AIV, confidence 1.0
const productBindings = resolveProductBindings('PFI-BAIV');

// ICP bindings: VP Problem.ownerRole -> cascade to Solutions/Benefits
const icpBindings = resolveICPBindings('PFI-BAIV');

// Inferred bindings: BFS depth-2 from explicit, confidence 0.5 / 0.25
const inferredBindings = inferProductBindings(composedGraph, productBindings);
```

**Why this matters:** When multiple PFI instances share ontologies (e.g. BAIV and VHF both use VP-ONT), entity bindings tell the visualiser which entities belong to which product. The UI shows product/ICP badges in the entity detail panel.

---

## Step 8: Freeze as Canonical Snapshot

Once the composed graph is validated and correct, freeze it as an immutable versioned template.

```javascript
import { freezeComposedGraph } from './js/emc-composer.js';

const composedGraphSpec = {
  specId: 'BAIV-AIV-Graph-Spec',
  componentOntologies: [
    { ontologyRef: 'VP-ONT', series: 'VE-Series', required: true },
    { ontologyRef: 'RRR-ONT', series: 'VE-Series', required: true },
    { ontologyRef: 'EFS-ONT', series: 'PE-Series', required: true },
    { ontologyRef: 'ORG-CONTEXT-ONT', series: 'Foundation', required: true },
  ],
  joinPoints: [
    { from: 'vp:IdealCustomerProfile', to: 'rrr:Role', relationship: 'icpMapsToRole' },
    { from: 'rrr:Role', to: 'org:Organisation', relationship: 'roleWithinOrg' },
    { from: 'vp:Problem', to: 'efs:Epic', relationship: 'problemAddressedByEpic' },
  ],
  scopeRules: ['BAIV-AIV-Product-Scope', 'BAIV-MarTech-Market-Scope', 'BAIV-Startup-Maturity-Scope'],
  entityCount: composedGraph.metadata.entityCount,
};

const freezeResult = freezeComposedGraph(composedGraphSpec, '1.0.0', 'admin@pfc.io');
// freezeResult.snapshot.snapshotId === 'BAIV-AIV-Graph-Spec-v1.0.0'
// freezeResult.snapshot.changeControlStatus === 'locked'
```

**What freezing does:**
- Deep-clones the spec (source mutation cannot affect snapshot)
- Stamps metadata: snapshotId, version, frozenAt, frozenBy, changeControlStatus
- Applies recursive `Object.freeze()` — the snapshot is truly immutable
- Supersedes any previous version (v0.x becomes 'superseded')
- Persists to localStorage for browser-session persistence

**Versioning convention:**
- `1.0.0` — First discovery build baseline
- `1.1.0` — Minor additions (new entities, new joins)
- `2.0.0` — Breaking changes (removed ontologies, restructured joins)

---

## Step 9: Generate PFI Graph from Frozen Snapshot

This is the runtime entry point. It validates the snapshot and re-runs the full pipeline.

```javascript
import { generatePFIGraph } from './js/emc-composer.js';

const graphResult = generatePFIGraph('PFI-BAIV', 'BAIV-AIV-Graph-Spec-v1.0.0');
```

**What this orchestrates:**
1. Validates snapshot exists and is `locked` (rejects superseded snapshots)
2. `resolveProductContext('PFI-BAIV')` -> context bag
3. `evaluateScopeRules('PFI-BAIV', context)` -> scope result
4. `composeInstanceGraph('PFI-BAIV', scopeResult)` -> composed graph
5. Stores in `state.composedPFIGraph`
6. Sets `state.scopeRulesActive = true`
7. Stores scope rule log for breadcrumb click inspection

**After this call**, the visualiser renders the scoped graph with:
- Visible entities (in composed graph)
- Ghost entities (in loaded ontologies but not in scope)
- Hidden entities (excluded by scope rules)

---

## Step 10: Filter to Persona Workflow (Optional)

For persona-specific views (e.g. "What does the Soc Manager see?"):

```javascript
import { generatePersonaWorkflow } from './js/emc-composer.js';

// Show Soc Manager's tactical workflow
const result = generatePersonaWorkflow('PFI-BAIV', 'vp:IdealCustomerProfile-SocManager');

// Clear persona filter (show full graph)
generatePersonaWorkflow('PFI-BAIV', null);
```

**Persona scope levels:**
| Seniority | Role Example | Scope | Sees |
|-----------|-------------|-------|------|
| 1 (Executive) | CMO, CISO | Strategic | High-level problems + objectives |
| 3 (Tactical) | Soc Manager, Director | Tactical | Features, epics, tactical problems |
| 5 (Operational) | Content Manager, Analyst | Operational | User stories, tasks, operational detail |

---

## Updating the Frozen Graph

To update the BAIV graph after adding new instance data or changing scope rules:

### Option A: New Version (Recommended)

```javascript
// 1. Make changes (add instance data, modify scope rules in EMC-ONT)
// 2. Re-compose the graph
const newContext = resolveProductContext('PFI-BAIV');
const newScope = evaluateScopeRules('PFI-BAIV', newContext);
const newGraph = composeInstanceGraph('PFI-BAIV', newScope);

// 3. Freeze as new version (v1.0.0 becomes superseded)
const newSpec = { ...composedGraphSpec, entityCount: newGraph.metadata.entityCount };
const result = freezeComposedGraph(newSpec, '1.1.0', 'admin@pfc.io');

// 4. Compare versions
const diff = diffSnapshots('BAIV-AIV-Graph-Spec-v1.0.0', 'BAIV-AIV-Graph-Spec-v1.1.0');
// diff.summary: { nodesAdded, nodesRemoved, nodesModified, edgesAdded, edgesRemoved }
```

### Option B: Inspect Version History

```javascript
import { listSnapshotVersions } from './js/emc-composer.js';

const versions = listSnapshotVersions('BAIV-AIV-Graph-Spec');
// [
//   { snapshotId: '...v1.0.0', version: '1.0.0', changeControlStatus: 'superseded', ... },
//   { snapshotId: '...v1.1.0', version: '1.1.0', changeControlStatus: 'locked', ... },
// ]
```

---

## UI Features Available

Once a PFI graph is active in the visualiser:

### Core Graph Features

| Feature | Location | Trigger |
|---------|----------|---------|
| **Scope rule log** | Stats bar breadcrumb | Click the cyan "PFI: BAIV-AIV" text |
| **Persona selector** | Toolbar chips | Shows ICP hierarchy with seniority badges |
| **Product/ICP badges** | Entity detail sidebar | Shows product binding + ICP scope per entity |
| **Ghost entities** | Graph canvas | Entities outside scope shown with dashed borders, reduced size |
| **Hidden entities** | Not rendered | Excluded by scope rules (e.g. KPI at maturity 1) |

### PFI Lifecycle Workbench (F40.17)

| Feature | Zone/Location | Trigger |
|---------|---------------|---------|
| **Lifecycle Panel** | Z21 (sliding left, 420px) | L3-admin "Lifecycle" button (visible in PFI mode) |
| **10-step pipeline tracker** | Inside Z21 | Auto-renders with status badges (pending/ready/complete/error) per step |
| **Compose Graph** button | Z21 step 4 + L4 toolbar (BAIV) | Runs steps 4→5→6 (resolve context → evaluate rules → compose graph) |
| **Resolve Bindings** button | Z21 step 7 | Resolves product + ICP + inferred bindings |
| **Snapshot Manager** | Z18 modal | L3-admin "Snapshots" button or Z21 step 8 "Freeze..." |
| **Freeze snapshot** | Inside snapshot modal | Enters version + admin ref, freezes composed graph as immutable snapshot |
| **Version history** | Inside snapshot modal | Lists all versions with status badges, inherit/diff buttons |
| **Diff snapshots** | Inside snapshot modal | Select 2 versions, compare node/edge additions/removals |
| **Binding Inspector** | Z9 sidebar "Bindings" tab | Shows after bindings resolved — product/ICP tables with confidence scores |
| **Binding filter** | Inside Bindings tab | All / Explicit / Inferred filter buttons |
| **BAIV Compose shortcut** | L4 toolbar | "Compose Graph" button (BAIV-specific, visible when PFI-BAIV active) |
| **BAIV Freeze shortcut** | L4 toolbar | "Freeze Snapshot" button (BAIV-specific, visible when graph composed) |

---

## BAIV-AIV Discovery Build: What to Create First

For the first phase discovery build, create these instance data files:

### Must Have (MVP)

1. **`vp-baiv-instance-v1.0.0.jsonld`** (VP-ONT)
   - 3-5 Problems (customer pain points BAIV-AIV solves)
   - 3-5 Solutions (product capabilities)
   - 3-5 Benefits (measurable outcomes)
   - 2-3 IdealCustomerProfiles (CMO, Soc Manager, Content Manager)
   - ownerRole on each Problem (links to ICP for persona scoping)

2. **`rrr-baiv-instance-v1.0.0.jsonld`** (RRR-ONT)
   - 3-5 Roles mapped to ICP profiles
   - 5-10 Responsibilities per role
   - RACI assignments linking roles to VP solutions

3. **`efs-baiv-instance-v1.0.0.jsonld`** (EFS-ONT)
   - 2-3 Epics (one per major VP Problem)
   - 5-8 Features per epic
   - 3-5 Stories per feature (at least for first epic)

### Nice to Have (Phase 1+)

4. **`org-ctx-baiv-instance-v1.0.0.jsonld`** (ORG-CONTEXT-ONT)
   - CompetitiveLandscape for MarTech
   - 2-3 Competitor entries
   - Organisation profile (maturity, size, vertical)

5. **`okr-baiv-instance-v1.0.0.jsonld`** (OKR-ONT)
   - 2-3 Objectives linked to VP Benefits
   - Key Results with measurable targets

---

## Quick Reference: Key Function Signatures

| Function | Parameters | Returns |
|----------|-----------|---------|
| `createPFIInstance` | `{instanceId, productCode, instanceName, description, requirementScopes, maturityLevel?, complianceScope?}` | `{success, instance, compositions}` |
| `loadPFIInstanceData` | `(instanceId, registryIndex?)` | `Promise<{success, files, orgContext}>` |
| `populateScopeRulesFromEMC` | `(emcRawData)` | `number` (count) |
| `resolveProductContext` | `(instanceId)` | Context bag object |
| `evaluateScopeRules` | `(instanceId, context)` | `{includedNamespaces, excludedNamespaces, ruleLog, ...}` |
| `composeInstanceGraph` | `(instanceId, scopeResult)` | `{success, nodes, edges, metadata}` |
| `freezeComposedGraph` | `(composedGraphSpec, version, adminRef)` | `{success, snapshot}` |
| `generatePFIGraph` | `(instanceId, snapshotId)` | `{success, composedGraph, scopeResult, context}` |
| `generatePersonaWorkflow` | `(instanceId, icpRef\|null)` | `{success, workflowGraph, personaRef}` |
| `inheritSnapshot` | `(instanceId, snapshotId)` | `{success, composedGraph}` |
| `diffSnapshots` | `(oldSnapshotId\|null, newSnapshotId)` | `{success, summary, nodes, edges, metadata}` |
| `listSnapshotVersions` | `(specId)` | `[{snapshotId, version, frozenAt, status, parent}]` |
| `resolveProductBindings` | `(instanceId)` | `Map<entityId, [{productCode, bindingType, confidence}]>` |
| `resolveICPBindings` | `(instanceId)` | `Map<entityId, [{icpRef, icpLabel, seniorityLevel}]>` |
| `inferProductBindings` | `(composedGraph, explicitBindings)` | `Map<entityId, [{productCode, bindingType, confidence}]>` |
