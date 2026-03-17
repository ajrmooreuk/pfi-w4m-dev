# ARCH-CICD-002: Promotion Pipeline — Technical Detail

**Version:** 1.2.0
**Date:** 2026-02-19
**Status:** Implemented (All Sections)
**Parent:** ARCH-CICD-001 (Hub-and-Spoke Proposal)

---

## 1. Three-Repo Promotion Model

The promotion pipeline uses three separate repositories per concern (conventions, instance) rather than three branches. This provides full isolation of secrets, CI quota, webhooks, and permissions.

```mermaid
flowchart LR
    subgraph "Convention Pipeline"
        CD["azlan-workflow-dev<br/>Break things freely"]
        CT["azlan-workflow-test<br/>SME validation"]
        CP["azlan-workflow-prod<br/>Source of truth"]
        CD -->|"promote.yml<br/>dev-to-test"| CT
        CT -->|"promote.yml<br/>test-to-prod<br/>+ SME approval"| CP
    end

    subgraph "Distribution"
        CP -->|"auto-tag.yml<br/>vN.N.N"| TAG["Git Tag"]
        TAG -->|"sync-to-live.yml"| LIVE["All Live Repos"]
    end

    subgraph "Instance Pipeline (per PFI)"
        ID["pfi-{name}-dev"] -->|"promote.yml"| IT["pfi-{name}-test"]
        IT -->|"promote.yml<br/>+ review"| IP["pfi-{name}-prod"]
    end

    CP -.->|"conventions"| IP
```

---

## 2. Workflow Execution Detail

### 2.1 Convention Promotion (promote.yml)

```mermaid
sequenceDiagram
    participant Op as Operator
    participant Dev as Dev Repo
    participant Test as Test Repo
    participant Prod as Prod Repo

    Op->>Dev: workflow_dispatch (dev-to-test, bump:minor)
    Dev->>Dev: Read convention-manifest.json
    Dev->>Dev: Checkout source files
    Dev->>Test: Create branch promote/dev-to-test/{timestamp}
    Dev->>Test: Copy all manifest files
    Dev->>Test: Open PR with change summary
    Note over Test: Reviewer approves PR
    Test->>Test: Merge PR to main

    Op->>Test: workflow_dispatch (test-to-prod, bump:minor)
    Test->>Prod: Create branch promote/test-to-prod/{timestamp}
    Test->>Prod: Copy all manifest files
    Test->>Prod: Open PR with SME approval checklist
    Note over Prod: SME + Lead approve PR
    Prod->>Prod: Merge PR to main
    Note over Prod: auto-tag.yml triggers
    Prod->>Prod: Calculate next semver
    Prod->>Prod: Create git tag + GitHub Release
```

### 2.2 Live Repo Sync (sync-to-live.yml)

```mermaid
sequenceDiagram
    participant Prod as Prod Repo
    participant Sync as sync-to-live.yml
    participant Pin as Version Pin Check
    participant Live as Live Repo (x N)

    Note over Prod: New tag pushed (v1.2.0)
    Prod->>Sync: Trigger on tag push
    Sync->>Sync: Read live-repos.json
    loop For each registered repo
        Sync->>Pin: Read azlan-workflow-version
        alt Pin = "latest" or matches tag
            Sync->>Live: Copy convention files
            Sync->>Live: Update azlan-workflow-version
            Sync->>Live: Create sync PR
        else Pin != tag
            Note over Pin: Skip (pinned to different version)
        end
    end
```

### 2.3 Drift Detection (drift-detection.yml)

```mermaid
flowchart TB
    CRON["Weekly Cron<br/>Monday 9am UTC"] --> CLONE["Clone each live repo"]
    CLONE --> DIFF["Diff convention files<br/>against prod"]
    DIFF --> CHECK{Files in sync?}
    CHECK -->|"All match"| OK["No action"]
    CHECK -->|"Drift found"| REPORT["Generate markdown report"]
    REPORT --> ISSUE{Existing drift issue?}
    ISSUE -->|"Yes"| COMMENT["Add comment to issue"]
    ISSUE -->|"No"| CREATE["Create new issue<br/>label: drift-detection"]
```

---

## 3. Convention Manifest

The `convention-manifest.json` defines exactly which files are promoted across repos:

| Category | Files | Count |
|----------|-------|:-----:|
| Issue Templates | epic.yml, feature.yml, story.yml, pbs.yml, wbs.yml, config.yml | 6 |
| PR Template | pull_request_template.md | 1 |
| Labels | labels.yml | 1 |
| Enforcement Workflows | validate-issue-naming.yml, validate-labels.yml, enforce-registry-link.yml | 3 |
| Setup Scripts | setup-labels.sh, setup-branch-protection.sh, setup-gh-project.sh, setup-all.sh, bootstrap-new-repo.sh, migrate-issues-into-hierarchy.sh | 6 |
| Claude Plugin | azlan-github-workflow/ (entire directory) | 1 |
| **Total** | | **18** |

---

## 4. PFC-Core Release Workflow (Implemented 2026-02-19)

The `pfc-release.yml` workflow in Azlan-EA-AAA packages and releases core assets to PFI instance dev repos. First production release: `pfc-v1.0.0` (6 PFIs, 29–40 ontologies each). See `PFC-Release-Audit-Log.md` for full run history.

```mermaid
flowchart TB
    TAG["Tag pfc-vN.N.N"] --> BUILD["Package core assets"]

    BUILD --> PKG_ONT["ontology-library/<br/>All series + registry"]
    BUILD --> PKG_VIS["ontology-visualiser/<br/>12 ES modules + CSS"]
    BUILD --> PKG_AGT["Agent prompts<br/>OAA v6.1 system prompt"]
    BUILD --> PKG_DS["Design system core<br/>Token taxonomy"]

    PKG_ONT & PKG_VIS & PKG_AGT & PKG_DS --> ZIP["Create release archive"]
    ZIP --> REL["GitHub Release<br/>pfc-vN.N.N"]

    REL --> PAGES["Deploy to GitHub Pages"]
    REL --> NOTIFY["Notify live repos<br/>(optional webhook)"]
```

### Release Archive Structure

```
pfc-v1.0.0.tar.gz
  pfc-core/
    ontology-library/
      ont-registry-index.json
      VE-Series/
      PE-Series/
      RCSG-Series/
      Foundation/
      Orchestration/
    ontology-visualiser/
      js/
      css/
      lib/
      index.html
    agents/
      oaa-v6/system-prompt.md
    design-system/
      token-taxonomy.json
    VERSION           # pfc-v1.0.0
    CHANGELOG.md
    LICENSE.md
```

---

## 5. Secret Management

```mermaid
flowchart TB
    subgraph "Secrets Scope"
        PAT["PROMOTION_PAT<br/>(Classic PAT)"]
        PAT -->|"repo + workflow scopes"| CONV["Convention repos<br/>(dev/test/prod)"]
        PAT -->|"repo scope"| LIVE["Live repos<br/>(PFI instances)"]

        SB_BAIV["SUPABASE_URL_BAIV<br/>SUPABASE_KEY_BAIV"]
        SB_AZAIR["SUPABASE_URL_AZAIR<br/>SUPABASE_KEY_AZAIR"]
        SB_EASVC["SUPABASE_URL_EASVC<br/>SUPABASE_KEY_EASVC"]
    end

    subgraph "Access Model"
        PAT_NOTE["Single PAT owned by org admin<br/>Rotate quarterly<br/>Stored as org secret"]
        SB_NOTE["Per-instance Supabase keys<br/>Stored in instance repo secrets<br/>Never shared across instances"]
    end
```

| Secret | Scope | Rotation | Storage |
|--------|-------|----------|---------|
| `PROMOTION_PAT` | All repos (classic PAT, repo+workflow) | Quarterly | Org-level secret |
| `SUPABASE_URL` | Per instance | On project recreation | Instance repo secret |
| `SUPABASE_ANON_KEY` | Per instance | On key rotation | Instance repo secret |
| `SUPABASE_SERVICE_KEY` | Per instance (CI only) | On key rotation | Instance repo secret |

---

## 6. Branch Protection per Tier

| Tier | Mode | PRs Required | Reviews | Force Push | CODEOWNERS Review | Status Checks |
|------|------|:------------:|:-------:|:----------:|:-----------------:|:-------------:|
| Dev | Multi-dev | Yes | 0 (self-merge) | Blocked | Yes (`pfc-core/`) | validate-conventions, guard-core |
| Test | Team | Yes | 1 | Blocked | Yes (`pfc-core/`) | validate-conventions, guard-core |
| Prod | Team | Yes | 1 + enforce admins | Blocked | Yes (`pfc-core/`) | validate-conventions, guard-core |

### Multi-Dev Mode (Dev Repos)

Multi-dev is the default for PFI dev repos. It balances PR-based traceability with solo-developer productivity:

- **PRs required** — no direct push to main (all changes are auditable)
- **0 reviews** — author can self-merge (no blocking on solo dev)
- **No force push** — commit history is immutable
- **Linear history** — clean `git log` for promotion tracking

Upgrade to 1 required review when a second developer joins the instance.

### Branch Mode Summary

| Mode | When to Use | Self-Merge | Reviews |
|------|-------------|:----------:|:-------:|
| `multi-dev` | Dev repo, 1-4 devs | Yes | 0 |
| `team` | Test/prod repos | No | 1+ |

---

## 7. Core Content Protection (Implemented 2026-02-19)

PFI instance repos contain a `pfc-core/` directory with core platform content (ontology registry, version pin). This content is **read-only** in PFI repos — it can only be changed in `Azlan-EA-AAA` and distributed via the PFC-Core release pipeline.

### 7.1 Three-Layer Protection Model

```mermaid
flowchart TB
    subgraph "Layer 1: Access Control"
        PRIVATE["All PFI repos set to PRIVATE<br/>No anonymous clone/download"]
    end

    subgraph "Layer 2: CI Guard"
        GUARD["guard-core.yml<br/>Triggers on PR touching pfc-core/**"]
        GUARD --> CHECK{Author?}
        CHECK -->|"github-actions[bot]"| ALLOW["✅ Automated sync — allowed"]
        CHECK -->|"Human"| BLOCK["❌ BLOCKED — edit in Azlan-EA-AAA"]
    end

    subgraph "Layer 3: CODEOWNERS"
        OWNERS["CODEOWNERS file<br/>/pfc-core/ @ajrmooreuk"]
        OWNERS --> REVIEW["Requires code owner review<br/>even if PR review count is 0"]
    end

    PRIVATE --> GUARD --> OWNERS
```

### 7.2 Protection Components

| Layer | Mechanism | File | Purpose |
|:-----:|-----------|------|---------|
| 1 | Private repos | GitHub repo settings | Prevents unauthorized clone/download of core IP |
| 2 | CI guard | `.github/workflows/guard-core.yml` | Blocks human PRs touching `pfc-core/` — only `github-actions[bot]` may modify |
| 3 | CODEOWNERS | `CODEOWNERS` | Requires `@ajrmooreuk` review for any `pfc-core/` change as second safety net |

### 7.3 guard-core.yml Workflow

```yaml
on:
  pull_request:
    paths:
      - 'pfc-core/**'

jobs:
  guard:
    runs-on: ubuntu-latest
    steps:
      - name: Check author
        run: |
          # Only github-actions[bot] and dependabot[bot] may modify pfc-core/
          # All other authors are blocked with instructions to use Azlan-EA-AAA
```

### 7.4 Deployment Status (2026-02-19)

| Repo | Private | guard-core.yml | CODEOWNERS | Owner Review |
|------|:-------:|:--------------:|:----------:|:------------:|
| pfi-baiv-aiv-dev | ✅ | ✅ | ✅ | ✅ |
| pfi-baiv-aiv-test | ✅ | ✅ | ✅ | ✅ |
| pfi-baiv-aiv-prod | ✅ | ✅ | ✅ | ✅ |
| pfi-airl-caf-aza-dev | ✅ | ✅ | ✅ | ✅ |
| pfi-airl-caf-aza-test | ✅ | ✅ | ✅ | ✅ |
| pfi-airl-caf-aza-prod | ✅ | ✅ | ✅ | ✅ |

### 7.5 How Automated Sync Bypasses the Guard

The PFC-Core release workflow (Section 4) and convention sync (`sync-to-live.yml`) both run as `github-actions[bot]`. When they create PRs that modify `pfc-core/`, the guard-core CI check passes because the bot is in the allowed list. Human-authored PRs touching the same path are blocked.
