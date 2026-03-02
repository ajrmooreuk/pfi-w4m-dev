# Epic 34 Companion: PF-Core Unified Registry -- Skills as Registry Artifacts

## Cross-Instance Deployment via the Unified Registry Cascade

| Field | Value |
|---|---|
| **Parent Epic** | 34: PF-Core Graph-Based Agentic Platform Strategy |
| **Document** | PFC-UNIFIED-REG-SKILLS-v1.0 |
| **Date** | 2026-02-18 |
| **Registry Version** | Unified Registry v4.0.0 |
| **Pattern** | Quasi-OO Inheritance Cascade |

---

## 1. Core Principle: One Registry

There is **one registry**. Not a skill registry, not an ontology registry, not a design-system registry -- one **Unified Registry** that governs all platform artifacts through a single quasi-object-oriented inheritance cascade.

Skills are artifacts in the registry. Just like ontologies, design tokens, agent prompts, PRDs, and schemas. They follow the exact same pattern: defined at Core, inherited by Instance, on/off or customised per Instance or Product, overridable per Client.

```
UNIFIED REGISTRY (Single Source of Truth)
|-- Artifact: VSOM Skill          -> Core ON -> BAIV: ON+customised -> W4M: ON+customised -> AIR: ON
|-- Artifact: OKR Cascade Skill   -> Core ON -> BAIV: ON -> W4M: ON+customised -> AIR: OFF
|-- Artifact: Agent Template      -> Core ON -> BAIV: ON+customised -> W4M: ON -> AIR: ON
|-- Artifact: Design Token Set    -> Core ON -> BAIV: ON+customised -> W4M: ON+customised -> AIR: ON+customised
|-- Artifact: Org Ontology        -> Core ON -> BAIV: ON+customised -> W4M: ON -> AIR: ON+customised
|-- Artifact: GRC Compliance      -> Core ON -> BAIV: ON -> W4M: ON -> AIR: ON+customised
+-- ... every governed artifact follows this pattern
```

---

## 2. Quasi-OO Inheritance Model

The registry operates like class inheritance. Core defines the base class. Instances inherit or override. Products/Clients inherit from their Instance.

### Resolution Order

```
1. Core artifact definition        <- Base class (always loaded)
2. Instance override (if exists)   <- Subclass (extends/overrides)
3. Product override (if exists)    <- Further specialisation
4. Client override (if exists)     <- Final customisation
5. Effective configuration         <- Merged result (deepMerge semantics)
```

Each level can:
- **Inherit** -- take the parent as-is (do nothing)
- **Override** -- replace specific config values
- **Extend** -- add new properties without removing inherited ones
- **Disable** -- set `status: OFF` (artifact not available at this level)

### Inheritance Diagram

```
PF-CORE (Base Class)
  skill: vsom
    status: ON
    version: 1.0.0
    config: { include_bsc: false, include_strategy_maps: true, entity_type: "organisation" }
    references: [schema-mappings, bsc-patterns, mermaid-templates, vsem-ontology]
    scripts: [generate_vsom.py, fetch_oaa.py]
    dependencies: [okr-cascade, agent-template]
        |
        | inherits
        |
   +---------+---------+---------+
   |         |         |         |
  BAIV      W4M       AIR    Client
  ON+ext   ON+ext     ON     (varies)
  +bsc:T   +bsc:T   inherit   may be
  +baiv_   +w4m_    all core   ON/OFF
  persp.   persp.   defaults   +custom
```

---

## 3. The On/Off/Customised Pattern

Every artifact in the registry has three states per scope level:

| State | Meaning | Config |
|---|---|---|
| **ON (inherit)** | Active, using parent defaults exactly | `status: active, overrideType: inherit` |
| **ON (customised)** | Active, with scope-specific overrides | `status: active, overrideType: extend/override` |
| **OFF** | Disabled at this scope level | `status: inactive` |

This is the **same pattern as design-system tokens**:

```
Design Token "color-primary":
  Core:     #0099B1     <- defined
  BAIV:     #0099B1     <- inherited (ON)
  W4M:      #2D5F8A     <- customised (ON + override)
  AIR:      #0099B1     <- inherited (ON)
  Client X: #FF6600     <- customised (ON + override)

Skill "vsom":
  Core:     { bsc: false, maps: true }          <- defined
  BAIV:     { bsc: true, +ai_visibility }       <- customised (ON + extend)
  W4M:      { bsc: true, +wellbeing }           <- customised (ON + extend)
  AIR:      { bsc: false, maps: true }          <- inherited (ON)
  Client M: { status: OFF }                      <- disabled
```

Same cascade. Same resolution. Same registry. Same governance.

---

## 4. Registry Artifact Structure (JSON-LD)

A skill in the Unified Registry follows the same JSON-LD artifact pattern as every other registry entry:

```json
{
  "@context": {
    "@vocab": "https://schema.org/",
    "pfc": "https://platformfoundation.core/registry/",
    "vsem": "https://wings4mind.ai/ontology/vsem/"
  },
  "@type": "pfc:RegistryArtifact",
  "@id": "pfc:skill-vsom-v1.0.0",
  "pfc:artifactType": "skill",
  "pfc:artifactCategory": "value-engineering",
  "name": "VSOM Skill",
  "version": "1.0.0",
  "description": "Vision-Strategy-Objectives-Metrics strategic framework generation",
  "pfc:scope": "core",
  "pfc:status": "active",
  "pfc:governedBy": "OAA Registry v3.0",
  "pfc:configuration": {
    "include_bsc": false,
    "include_strategy_maps": true,
    "entity_type": "organisation",
    "platform_instance": "PF-Core"
  },
  "pfc:components": [
    "SKILL.md",
    "assets/vsom-template.json",
    "references/schema-mappings.md",
    "references/bsc-patterns.md",
    "references/mermaid-templates.md",
    "references/vsem-ontology.json",
    "references/elicitation-prompts.md",
    "scripts/generate_vsom.py",
    "scripts/fetch_oaa.py"
  ],
  "pfc:dependencies": [
    {"@id": "pfc:skill-okr-cascade-v1.0.0"},
    {"@id": "pfc:ontology-vsem-v1.0.0"},
    {"@id": "pfc:schema-org-mappings-v1.0.0"}
  ],
  "pfc:instanceOverrides": {
    "baiv": {
      "pfc:status": "active",
      "pfc:overrideType": "extend",
      "pfc:configuration": {
        "include_bsc": true,
        "platform_instance": "PF-Core-BAIV",
        "baiv_perspectives": ["ai_discovery", "authority", "trust", "technical", "business_impact"],
        "baiv_metrics": ["ai_visibility_score", "mention_rate", "citation_authority"]
      },
      "pfc:additionalComponents": ["INSTANCE.md"]
    },
    "w4m": {
      "pfc:status": "active",
      "pfc:overrideType": "extend",
      "pfc:configuration": {
        "include_bsc": true,
        "platform_instance": "PF-Core-W4M",
        "w4m_perspectives": ["wellbeing_outcomes", "idea_velocity", "pmf_tracking"],
        "w4m_metrics": ["pmf_score", "time_to_mvp", "client_success_rate"]
      },
      "pfc:additionalComponents": ["INSTANCE.md"]
    },
    "air": {
      "pfc:status": "active",
      "pfc:overrideType": "inherit",
      "pfc:configuration": {
        "platform_instance": "PF-Core-AIR"
      }
    }
  },
  "pfc:qualityGates": {
    "testCoverage": ">=85%",
    "schemaOrgCompliance": "100%",
    "oaaRegistryCompliance": true,
    "mermaidSyntaxValid": true
  },
  "pfc:hash": "sha256:...",
  "dateModified": "2026-02-18"
}
```

---

## 5. File System Mapping

The registry is the **logical** governance layer. The file system is the **physical** implementation. They map 1:1:

```
pf-core/
|-- registry/
|   |-- registry-index.json          <- Master index of ALL artifacts
|   |-- skills/
|   |   |-- vsom/
|   |   |   |-- artifact.json        <- Registry entry (JSON-LD)
|   |   |   |-- SKILL.md             <- Core skill definition
|   |   |   |-- assets/
|   |   |   |-- references/
|   |   |   +-- scripts/
|   |   |-- okr-cascade/
|   |   |-- process-engineering/
|   |   |-- agent-template/
|   |   +-- grc-compliance/
|   |-- ontologies/                   <- Existing ontology library content
|   |-- design-system/
|   |-- agents/
|   +-- schemas/
|
|-- instances/
|   |-- baiv/
|   |   +-- overrides/                <- BAIV-specific overrides ONLY
|   |       |-- skills/vsom/INSTANCE.md
|   |       |-- design-system/tokens.override.json
|   |       +-- ontologies/ai-visibility-ontology/
|   |-- w4m/
|   |   +-- overrides/
|   +-- air/
|       +-- overrides/
|
|-- .claude/
|   |-- CLAUDE.md                     <- Core system prompt
|   +-- skills -> ../registry/skills  <- Symlink: Claude sees registry skills
|
+-- .github/workflows/
    |-- registry-ci.yml               <- Validates ALL registry artifacts
    +-- registry-deploy-*.yml
```

**Key insight:** `.claude/skills` is a symlink to `registry/skills/`. Claude Code sees skills. The registry governs; Claude consumes.

---

## 6. Supabase Storage -- Registry as Graph Nodes

In Supabase, registry artifacts are stored in the unified graph -- **one table**, not per-artifact-type tables:

```sql
CREATE TABLE pfc_registry (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  artifact_id TEXT NOT NULL,           -- "pfc:skill-vsom-v1.0.0"
  artifact_type TEXT NOT NULL,         -- "skill", "ontology", "design-token", "agent", "schema"
  artifact_category TEXT,              -- "value-engineering", "process", "governance"
  name TEXT NOT NULL,
  version TEXT NOT NULL,
  scope TEXT NOT NULL,                 -- "core", "instance", "product", "client"
  instance_id TEXT,                    -- NULL for core; "baiv"/"w4m"/"air" for instance
  client_id UUID REFERENCES tenants(id),
  status TEXT DEFAULT 'active',
  override_type TEXT DEFAULT 'inherit',
  configuration JSONB DEFAULT '{}',    -- Effective merged config at this scope
  base_configuration JSONB DEFAULT '{}',
  components JSONB DEFAULT '[]',
  dependencies JSONB DEFAULT '[]',
  quality_gates JSONB DEFAULT '{}',
  artifact_hash TEXT,
  environment TEXT NOT NULL,
  deployed_at TIMESTAMPTZ DEFAULT now(),
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(artifact_id, scope, instance_id, client_id, environment)
);
```

### Config Resolution Function

```sql
CREATE OR REPLACE FUNCTION resolve_artifact_config(
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

## 7. CI/CD: One Pipeline for All Artifacts

Since skills, ontologies, design tokens, and agent definitions are all registry artifacts, they share **one CI/CD pipeline**:

- `registry-ci.yml` -- validates ALL artifacts on PR (JSON-LD, Schema.org, OAA, dependency graph, override compatibility)
- `registry-deploy-prod.yml` -- deploys validated artifacts to Supabase on merge to main
- Three-tier testing: Local (free) -> Haiku (validation) -> Sonnet (production)

---

## 8. Relationship to Existing Assets

| Existing Asset | Maps To | Migration Path |
|---|---|---|
| `ont-registry-index.json` | `registry/registry-index.json` | Extend to cover all artifact types, not just ontologies |
| `ontology-library/*-ONT/` | `registry/ontologies/*-ONT/` | Add `artifact.json` wrappers, retain existing structure |
| `pfi-ontologies/` | `instances/*/overrides/ontologies/` | Move to instance override directories |
| `pfi-BAIV-AIV-ONT/` | `instances/baiv/overrides/ontologies/` | Move under BAIV instance |
| PFI Architecture guides | `instances/*/docs/` | Reference docs per instance |
| Visualiser `pfi-loader.js` | Consumer of registry API | Reads resolved configs at runtime |

---

## 9. Summary: Design Principles

| Principle | Implementation |
|---|---|
| **One registry** | `pfc_registry` -- single table, all artifact types |
| **Quasi-OO cascade** | Core -> Instance -> Product -> Client with inherit/extend/override |
| **On/Off/Customised** | `status: active/inactive` + `override_type` per scope |
| **Same as design-system** | Tokens, skills, ontologies, agents, schemas -- identical governance |
| **Config resolution** | `resolve_artifact_config()` merges up the cascade, deepMerge semantics |
| **Claude sees skills** | `.claude/skills` symlinks to `registry/skills/` |
| **One CI/CD pipeline** | Single validation workflow for all artifact types |
| **Three-tier testing** | Local -> Haiku -> Sonnet cost model for all artifacts |
| **File-first, DB-later** | JSON files in Git (now) -> Supabase sync (scale) |

The registry is the graph. Skills are nodes. The cascade is the inheritance. On/off is the toggle. Same pattern, everywhere, for everything.

---

*Epic 34 Companion -- Unified Registry Architecture*
*Status: DRAFT -- Ready for analysis and feature planning*
