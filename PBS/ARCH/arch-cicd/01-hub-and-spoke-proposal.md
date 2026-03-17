# ARCH-CICD-001: Hub-and-Spoke Multi-Instance Platform Architecture

**Version:** 1.0.0
**Date:** 2026-02-16
**Status:** Proposal
**Author:** Azlan Architecture Team
**Relates to:** Epic 10 (PE E2E), Epic 10A (Security MVP), Epic 12-16 (PFI Instances), Epic 19 (Graph-Scope Rules)

---

## 1. Executive Summary

This proposal defines the CI/CD and repository architecture for distributing PF-Core (PFC) assets to multiple parallel Platform Foundation Instances (PFI). It leverages the existing `my-workflow-test` promotion pipeline (dev-test-prod three-repo model) and extends it to support 3-4 concurrent PFI delivery teams while maintaining ontology integrity, design system consistency, and licence control.

**Recommendation:** Hub-and-Spoke with existing promotion pipeline (Option A).

---

## 2. Architecture Overview

```mermaid
graph TB
    subgraph "PFC-Core Forge (Azlan-EA-AAA)"
        ONT["Ontology Library<br/>40 ontologies"]
        VIS["Ontology Visualiser<br/>12 ES modules"]
        AGT["Agent Prompts<br/>OAA v6.1"]
        DS["Design System Core<br/>Token taxonomy"]
        REG["Registry Index<br/>v7.2.0"]
    end

    subgraph "Workflow Template (my-workflow-test)"
        TPL["Convention Manifest"]
        BST["Bootstrap Script"]
        PRO["Promotion Pipeline"]
        SKL["Claude Code Skills"]
        DRF["Drift Detection"]
    end

    ONT & VIS & AGT & DS & REG --> REL["GitHub Releases<br/>pfc-vN.N.N"]
    REL --> PAGES["GitHub Pages<br/>Static Deploy"]

    TPL & BST & PRO & SKL --> SYNC["sync-to-live.yml"]

    REL --> BAIV
    REL --> AZAIR
    REL --> EASVC
    SYNC --> BAIV
    SYNC --> AZAIR
    SYNC --> EASVC

    subgraph "PFI-BAIV-AIV"
        BAIV["pfi-baiv-aiv<br/>(dev / test / prod)"]
        BAIV_SB["Supabase Project"]
        BAIV_TK["BAIV Brand Tokens"]
        BAIV_ID["Instance Data"]
    end

    subgraph "PFI-AZ-AIR"
        AZAIR["pfi-az-air<br/>(dev / test / prod)"]
        AZAIR_SB["Supabase Project"]
        AZAIR_TK["AZ-AIR Configs"]
        AZAIR_ID["Audit Instance Data"]
    end

    subgraph "PFI-EA-Services"
        EASVC["pfi-ea-services<br/>(dev / test / prod)"]
        EASVC_SB["Supabase Project"]
        EASVC_TK["EA Brand Tokens"]
        EASVC_ID["TOGAF Instance Data"]
    end
```

---

## 3. Options Evaluated

### Option A: Hub-and-Spoke (Recommended)

PFC-Core stays as the single source of truth. Each PFI gets its own repo triad (dev/test/prod). Core assets flow downstream via GitHub Releases and the sync workflow.

```mermaid
flowchart LR
    subgraph "Core Forge"
        A["Azlan-EA-AAA"]
    end

    A -->|"pfc-vN.N.N<br/>GitHub Release"| B["pfi-baiv-aiv-prod"]
    A -->|"pfc-vN.N.N"| C["pfi-az-air-prod"]
    A -->|"pfc-vN.N.N"| D["pfi-ea-services-prod"]

    B --- B1["pfi-baiv-aiv-test"]
    B1 --- B2["pfi-baiv-aiv-dev"]

    C --- C1["pfi-az-air-test"]
    C1 --- C2["pfi-az-air-dev"]

    D --- D1["pfi-ea-services-test"]
    D1 --- D2["pfi-ea-services-dev"]
```

| Criteria | Score |
|----------|:-----:|
| Effort to implement | Low |
| Team isolation | Full |
| Reuses existing pipeline | Yes |
| Licence control | Built-in (version pinning) |
| Ontology integrity | GitHub Pages (immutable per release) |
| Supabase isolation | Per-project (free tier) |

### Option B: Shared-Core Submodule

PFC-Core as a Git submodule inside each instance repo. Fewer repos but painful submodule management.

```mermaid
flowchart LR
    A["Azlan-EA-AAA<br/>(submodule)"] --> B["pfi-baiv-aiv"]
    A --> C["pfi-az-air"]
    A --> D["pfi-ea-services"]
```

| Criteria | Score |
|----------|:-----:|
| Effort to implement | Medium |
| Team isolation | Partial (branch-based) |
| Reuses existing pipeline | No |
| Licence control | Manual |
| Ontology integrity | Submodule ref (can drift) |
| Supabase isolation | Per-project |

### Option C: Monorepo Workspaces

Everything in one repo with npm/pnpm workspaces. Requires build tooling.

| Criteria | Score |
|----------|:-----:|
| Effort to implement | High |
| Team isolation | None (shared repo) |
| Reuses existing pipeline | No |
| Licence control | Complex |
| Ontology integrity | Good (atomic commits) |
| Supabase isolation | Environment-based |

### Decision Matrix

```mermaid
quadrantChart
    title Effort vs Isolation Trade-off
    x-axis "Low Effort" --> "High Effort"
    y-axis "Low Isolation" --> "Full Isolation"
    "Option A: Hub-Spoke": [0.25, 0.9]
    "Option B: Submodule": [0.5, 0.4]
    "Option C: Monorepo": [0.85, 0.2]
```

---

## 4. Hub-and-Spoke Architecture Detail

### 4.1 Repository Topology

```mermaid
graph TB
    subgraph "Tier 0: Core"
        PFC["Azlan-EA-AAA<br/>(PFC-Core Forge)"]
        WFT["my-workflow-test<br/>(Convention Template)"]
    end

    subgraph "Tier 1: Promotion Pipeline"
        WFD["azlan-workflow-dev"]
        WFT2["azlan-workflow-test"]
        WFP["azlan-workflow-prod"]
        WFD -->|"promote"| WFT2
        WFT2 -->|"promote + SME"| WFP
    end

    subgraph "Tier 2: PFI Instance Triads"
        direction TB
        subgraph "BAIV-AIV"
            BD["pfi-baiv-dev"]
            BT["pfi-baiv-test"]
            BP["pfi-baiv-prod"]
            BD -->|"promote"| BT
            BT -->|"promote"| BP
        end
        subgraph "AZ-AIR"
            AD["pfi-azair-dev"]
            AT["pfi-azair-test"]
            AP["pfi-azair-prod"]
            AD -->|"promote"| AT
            AT -->|"promote"| AP
        end
        subgraph "EA-Services"
            ED["pfi-easvc-dev"]
            ET["pfi-easvc-test"]
            EP["pfi-easvc-prod"]
            ED -->|"promote"| ET
            ET -->|"promote"| EP
        end
    end

    WFP -->|"sync conventions"| BP & AP & EP
    PFC -->|"pfc-vN.N.N release"| BP & AP & EP
```

### 4.2 Promotion Flow

```mermaid
sequenceDiagram
    participant Dev as Instance Dev
    participant Test as Instance Test
    participant Prod as Instance Prod
    participant Core as PFC-Core

    Note over Core: Tag pfc-v1.0.0
    Core->>Prod: sync-to-live (ontologies, visualiser, agents)

    Dev->>Dev: Developer commits instance work
    Dev->>Test: promote.yml (dev-to-test)
    Note over Test: SME reviews PR
    Test->>Prod: promote.yml (test-to-prod)
    Note over Prod: Auto-tag release

    Note over Core: Tag pfc-v1.1.0 (ontology update)
    Core->>Prod: sync-to-live (version-pinned check)
    Note over Prod: Instance pinned to v1.0.0 — skipped
    Note over Prod: Admin moves pin to v1.1.0 — synced
```

### 4.3 Core Asset Distribution

| Asset | Distribution Method | Consumer |
|-------|-------------------|----------|
| Ontology Library | GitHub Pages URL | Visualiser multi-loader |
| Visualiser | GitHub Release artifact | Instance deploy pipeline |
| Agent Prompts | GitHub Release artifact | Claude Code plugin |
| Design System Tokens | GitHub Release artifact | Instance build |
| Convention Files | sync-to-live.yml | Instance repo |
| Claude Code Skills | Convention manifest | Instance repo |

### 4.4 Instance Repo Structure

Each PFI instance repo (created by `bootstrap-new-repo.sh`) contains:

```
pfi-{instance}-{tier}/
  .github/
    workflows/           # Convention workflows (synced from prod)
    ISSUE_TEMPLATE/      # Convention templates (synced from prod)
    labels.yml           # Convention labels (synced from prod)
  azlan-github-workflow/ # Claude Code skills (synced from prod)
  instance-data/
    ontologies/          # Instance-specific ontology data (*.jsonld)
    tokens/              # Brand-specific design tokens
    config/              # Instance configuration
  supabase/
    migrations/          # Supabase schema migrations
    seed.sql             # Instance seed data
  tools/
    custom/              # Instance-specific tools
  pfc-core/
    azlan-workflow-version    # Pinned PFC version
    ontology-registry.json    # Cached registry snapshot
    .claude-plugin/
      plugin.json             # Sealed skills plugin manifest
    skills/
      close-out/SKILL.md      # Sealed: 6-stage post-change pipeline
    sealed-skills-manifest.json # Governance metadata
  docs/
    instance-readme.md
```

### 4.5 Sealed Skill Distribution (F31.9)

Some skills must be **executable but not editable** in PFI triad repos. These are distributed via `pfc-release.yml` and protected by `guard-core.yml`.

**Two-phase delivery:**

| Phase | Mechanism | What arrives |
|-------|-----------|-------------|
| Bootstrap (day 0) | `bootstrap-triad.sh` from AZLAN-CI-CD | `guard-core.yml`, `plugin.json`, `pfc-core/skills/.gitkeep` (scaffolding) |
| First PFC release (day 1+) | `pfc-release.yml` from Azlan-EA-AAA | Sealed SKILL.md files, `sealed-skills-manifest.json` |

**Governance controls:**
- `guard-core.yml` CI gate blocks any PR touching `pfc-core/` unless authored by `github-actions[bot]`
- `sealed-skills-manifest.json` declares which skills are sealed with `overrideType: "none"`
- Claude Code discovers sealed skills via `--plugin-dir ./pfc-core`

**Adding a new sealed skill:**
1. Create the SKILL.md in `azlan-github-workflow/skills/{name}/` (Azlan-EA-AAA)
2. Add an entry to `azlan-github-workflow/sealed-skills-manifest.json` with source/target paths
3. Tag a new `pfc-v*` release — `pfc-release.yml` distributes automatically

---

## 5. Licence and Version Control

### 5.1 Version Pinning Model

```mermaid
stateDiagram-v2
    [*] --> Pinned: Instance created
    Pinned --> Latest: Admin moves pin to "latest"
    Latest --> Pinned: Admin pins to specific version
    Pinned --> Pinned: Core release skipped (pin mismatch)
    Latest --> Latest: Core release auto-synced

    state Pinned {
        [*] --> v1_0: Initial
        v1_0 --> v1_1: Admin updates pin
        v1_1 --> v2_0: Admin updates pin
    }
```

### 5.2 live-repos.json Registry

```json
{
  "repos": [
    {
      "name": "ajrmooreuk/Azlan-EA-AAA",
      "pin": true,
      "notes": "Main ontology and visualiser repo"
    },
    {
      "name": "clientorg/pfi-baiv-aiv",
      "pin": "pfc-v1.0.0",
      "license": "pfc-standard",
      "ontologies": ["VE-Series", "PE-Series", "Foundation"],
      "notes": "BAIV AI Visibility instance"
    },
    {
      "name": "clientorg/pfi-az-air",
      "pin": "pfc-v1.0.0",
      "license": "pfc-compliance",
      "ontologies": ["RCSG-Series", "PE-Series", "Foundation"],
      "notes": "Azure AI Readiness - Audits & Assessments"
    },
    {
      "name": "clientorg/pfi-ea-services",
      "pin": "pfc-v1.0.0",
      "license": "pfc-enterprise",
      "ontologies": ["PE-Series", "VE-Series", "Foundation"],
      "notes": "EA Services practice"
    }
  ]
}
```

---

## 6. Supabase Multi-Instance Strategy

```mermaid
graph TB
    subgraph "Supabase Free Tier (per instance)"
        subgraph "supabase-pfi-baiv"
            B_AUTH["Auth (BAIV users)"]
            B_DB["Postgres (BAIV data)"]
            B_RLS["RLS (PFI-scoped)"]
        end
        subgraph "supabase-pfi-azair"
            A_AUTH["Auth (AZ-AIR users)"]
            A_DB["Postgres (audit data)"]
            A_RLS["RLS (PFI-scoped)"]
        end
        subgraph "supabase-pfi-easvc"
            E_AUTH["Auth (EA users)"]
            E_DB["Postgres (EA data)"]
            E_RLS["RLS (PFI-scoped)"]
        end
    end

    subgraph "Shared Schema (from PFC-Core)"
        SCHEMA["Schema template<br/>5 core tables<br/>4 roles (RRR-ONT)"]
    end

    SCHEMA -->|"supabase migration"| B_DB & A_DB & E_DB
```

Each Supabase project:
- Free tier (500MB database, 50K monthly active users)
- Shared schema template from PFC-Core (Epic 10A)
- RLS at database layer scoped to PFI
- Instance-specific seed data
- Independent auth configuration

---

## 7. MVP Execution Sequence

```mermaid
gantt
    title MVP Bootstrap — 3 PFI Instance Triads
    dateFormat YYYY-MM-DD
    axisFormat %b %d

    section Core Preparation
    Create PFC release workflow     :a1, 2026-02-17, 1d
    Tag pfc-v1.0.0                  :a2, after a1, 1d
    Update live-repos.json          :a3, after a2, 1d

    section BAIV-AIV Instance
    Bootstrap BAIV triad            :b1, after a3, 1d
    Seed BAIV instance data         :b2, after b1, 1d
    Configure BAIV Supabase         :b3, after b2, 1d

    section AZ-AIR Instance
    Bootstrap AZ-AIR triad          :c1, after a3, 1d
    Seed AZ-AIR audit data          :c2, after c1, 1d
    Configure AZ-AIR Supabase       :c3, after c2, 1d

    section EA-Services Instance
    Bootstrap EA-Services triad     :d1, after a3, 1d
    Seed EA instance data           :d2, after d1, 1d
    Configure EA Supabase           :d3, after d2, 1d

    section Validation
    First convention sync test      :e1, after b3, 1d
    Drift detection dry run         :e2, after e1, 1d
    End-to-end promotion test       :e3, after e2, 1d
```

---

## 8. Risk Register

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| Repo sprawl (9-12 repos) | Medium | High | Naming convention, drift detection, Claude skills automate management |
| PAT secret management across repos | High | Medium | Single PROMOTION_PAT with scoped access, rotate quarterly |
| Instance divergence from core | Medium | Medium | Weekly drift detection, version pinning |
| Supabase free-tier limits | Low | Low | 500MB per project is generous; monitor usage |
| Team onboarding complexity | Medium | Medium | `bootstrap-new-repo.sh` one-command setup, onboarding.md |

---

## 9. Next Steps

1. Create PFC release workflow in Azlan-EA-AAA (GitHub Action to tag and package core assets)
2. Tag first PFC release (`pfc-v1.0.0`)
3. Bootstrap first PFI triad (BAIV-AIV) as proof-of-concept
4. Run end-to-end promotion test
5. Create Epic for formal implementation (Epic 29: Multi-Instance Platform Delivery)
