# VSOM Strategy Briefing: Unified Registry Database — From Ontology Index to Multi-Artifact Platform Registry

## Broadening and Maintaining PF/EMC/PFI/Product/Org Context Across Ontologies, UI/UX, DB, JSON & Code

| Field | Value |
|---|---|
| **Document** | PFC-UNIFIED-REG-DB-STRATEGY-v1.0.0 |
| **Date** | 2026-03-02 |
| **Status** | DRAFT — Ready for strategic review |
| **Companion To** | Epic 34 (#518): Unified Registry Skills Architecture |
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

Epic 34's Unified Registry briefing (2026-02-18) established the architectural principle: **one registry, quasi-OO cascade, all artifact types**. That briefing defined the "what" and "why". This document provides the **"how" and "when"** — a VSOM-led strategy for evolving the current file-based ontology index into a multi-artifact platform registry backed by Supabase JSONB, with full cascade resolution across PF-Core → PFI → Product → Client scopes.

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

The `pfc_registry` table from Epic 34 briefing is confirmed as the target, with clarifications for multi-artifact support:

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
| Epic 34 | #518 | PF-Core Graph-Based Agentic Platform Strategy | Defines unified registry architecture |
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
*Companion to: Epic 34 Unified Registry Skills Architecture*
