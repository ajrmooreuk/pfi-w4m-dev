# PFC-Core Release Audit Log

**Date:** 2026-02-19
**Operator:** ajrmooreuk (via Claude Code)
**Source repo:** ajrmooreuk/Azlan-EA-AAA
**Workflow:** `.github/workflows/pfc-release.yml`

---

## 1. Workflow Runs

| # | Run ID | Trigger | Tag | Status | Duration | Timestamp (UTC) |
|:-:|--------|---------|-----|--------|----------|-----------------|
| 1 | 22195134110 | workflow_dispatch | manual-20260219-184107 | success | 24s | 2026-02-19T18:41:07Z |
| 2 | 22195262567 | workflow_dispatch | manual-20260219-184500 | success | 26s | 2026-02-19T18:44:51Z |
| 3 | 22196070380 | push (tag) | pfc-v1.0.0 | success | 36s | 2026-02-19T19:07:55Z |

---

## 2. Run 1 — Dry Run (BAIV only)

**Run ID:** 22195134110
**Trigger:** `workflow_dispatch` with `target_pfis=PFI-BAIV`, `dry_run=true`
**Purpose:** Validate pipeline end-to-end without creating PRs

### Targets loaded

| PFI | Dev Repo | Pin | Series |
|-----|----------|-----|--------|
| PFI-BAIV | ajrmooreuk/pfi-baiv-aiv-dev | latest | VE-Series, PE-Series, RCSG-Series, Foundation, Orchestration |

### Results

| PFI | Pin Check | Filtered Ontologies | Changes | PR Created |
|-----|-----------|:-------------------:|---------|:----------:|
| PFI-BAIV | No pfc-version file (first release) | 40 | Yes | No (dry run) |

### Files that would have been written

| Target Repo | File | Description |
|-------------|------|-------------|
| pfi-baiv-aiv-dev | `pfc-core/ontology-registry.json` | Filtered snapshot — 40 ontologies (37 compliant, 1 superseded, 2 placeholder) |
| pfi-baiv-aiv-dev | `pfc-core/pfc-version` | `manual-20260219-184107` |

---

## 3. Run 2 — Real Release (BAIV only)

**Run ID:** 22195262567
**Trigger:** `workflow_dispatch` with `target_pfis=PFI-BAIV`, `dry_run=false`
**Purpose:** First real release to validate PR creation

### Targets loaded

| PFI | Dev Repo | Pin | Series |
|-----|----------|-----|--------|
| PFI-BAIV | ajrmooreuk/pfi-baiv-aiv-dev | latest | VE-Series, PE-Series, RCSG-Series, Foundation, Orchestration |

### Results

| PFI | Pin Check | Filtered Ontologies | Changes | PR Created |
|-----|-----------|:-------------------:|---------|:----------:|
| PFI-BAIV | No pfc-version file (first release) | 40 | Yes | **PR #34** |

### Files written

| Target Repo | File | Additions | Deletions |
|-------------|------|:---------:|:---------:|
| pfi-baiv-aiv-dev | `pfc-core/ontology-registry.json` | 677 | 0 |
| pfi-baiv-aiv-dev | `pfc-core/pfc-version` | 1 | 1 |

### PR #34 — pfi-baiv-aiv-dev

| Field | Value |
|-------|-------|
| Title | PFC-Core release: manual-20260219-184500 |
| Branch | `pfc-release/manual-20260219-184500/20260219-184510` |
| Additions | 678 |
| Deletions | 1 |
| Created | 2026-02-19T18:45:13Z |
| **Merged** | **2026-02-19T19:07:11Z** |
| URL | https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/34 |

---

## 4. Run 3 — Full Release pfc-v1.0.0 (All PFIs)

**Run ID:** 22196070380
**Trigger:** `push` tag `pfc-v1.0.0`
**Purpose:** First production release to all PFI instance dev repos

### Targets loaded (6 PFIs)

| PFI | Dev Repo | Pin | Series | Series Count |
|-----|----------|-----|--------|:------------:|
| PFI-BAIV | ajrmooreuk/pfi-baiv-aiv-dev | latest | VE, PE, RCSG, Foundation, Orchestration | 5 |
| PFI-AIRL-CAF-AZA | ajrmooreuk/pfi-airl-caf-aza-dev | latest | RCSG, VE, Foundation | 3 |
| PFI-W4M | ajrmooreuk/pfi-w4m-dev | latest | VE, PE, Foundation, Orchestration | 4 |
| PFI-W4M-WWG | ajrmooreuk/pfi-w4m-wwg-dev | latest | VE, PE, RCSG, Foundation, Orchestration | 5 |
| PFI-W4M-EOMS | ajrmooreuk/pfi-w4m-eoms-dev | latest | VE, PE, RCSG, Foundation, Orchestration | 5 |
| PFI-VHF | ajrmooreuk/pfi-vhf-nutrition-app-dev | latest | VE, PE, RCSG, Foundation, Orchestration | 5 |

**Note:** PFI-RCS skipped — no `hubSpokeConfig` in registry (no triad repos yet).

### Results per PFI

| PFI | Pin Check | Last Deployed | Filtered Ontologies | Changes | PR |
|-----|-----------|---------------|:-------------------:|:-------:|:--:|
| PFI-BAIV | latest | manual-20260219-184500 | 40 | Yes (version tag only) | **#35** |
| PFI-AIRL-CAF-AZA | latest | (first release) | 31 | Yes | **#36** |
| PFI-W4M | latest | (first release) | 29 | Yes | **#1** |
| PFI-W4M-WWG | latest | (first release) | 40 | Yes | **#31** |
| PFI-W4M-EOMS | latest | (first release) | 40 | Yes | **#31** |
| PFI-VHF | latest | (first release) | 40 | Yes | **#22** |

### Files written per PFI

| Target Repo | `pfc-core/ontology-registry.json` | `pfc-core/pfc-version` | Additions | Deletions |
|-------------|:-:|:-:|:---------:|:---------:|
| pfi-baiv-aiv-dev | Written (40 ont) | `pfc-v1.0.0` | 3 | 3 |
| pfi-airl-caf-aza-dev | Written (31 ont) | `pfc-v1.0.0` | 542 | 1 |
| pfi-w4m-dev | Written (29 ont) | `pfc-v1.0.0` | 487 | 1 |
| pfi-w4m-wwg-dev | Written (40 ont) | `pfc-v1.0.0` | 678 | 1 |
| pfi-w4m-eoms-dev | Written (40 ont) | `pfc-v1.0.0` | 678 | 1 |
| pfi-vhf-nutrition-app-dev | Written (40 ont) | `pfc-v1.0.0` | 146 | 229 |

**BAIV note:** Low diff (3 add / 3 del) because PR #34 (manual release) was already merged — only the version tag changed from `manual-20260219-184500` to `pfc-v1.0.0`.

**VHF note:** 229 deletions — VHF had a pre-existing `pfc-core/ontology-registry.json` placeholder that was larger than the filtered snapshot. The filtered version replaced it.

### PRs created

| PFI | PR # | State | Branch | Created | Merged | URL |
|-----|:----:|-------|--------|---------|--------|-----|
| PFI-BAIV | 35 | OPEN | pfc-release/pfc-v1.0.0/20260219-190820 | 19:08:22Z | — | https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/35 |
| PFI-AIRL-CAF-AZA | 36 | **MERGED** | pfc-release/pfc-v1.0.0/20260219-190820 | 19:08:22Z | 19:57:55Z | https://github.com/ajrmooreuk/pfi-airl-caf-aza-dev/pull/36 |
| PFI-W4M | 1 | OPEN | pfc-release/pfc-v1.0.0/20260219-190820 | 19:08:23Z | — | https://github.com/ajrmooreuk/pfi-w4m-dev/pull/1 |
| PFI-W4M-WWG | 31 | OPEN | pfc-release/pfc-v1.0.0/20260219-190820 | 19:08:24Z | — | https://github.com/ajrmooreuk/pfi-w4m-wwg-dev/pull/31 |
| PFI-W4M-EOMS | 31 | OPEN | pfc-release/pfc-v1.0.0/20260219-190821 | 19:08:25Z | — | https://github.com/ajrmooreuk/pfi-w4m-eoms-dev/pull/31 |
| PFI-VHF | 22 | OPEN | pfc-release/pfc-v1.0.0/20260219-190821 | 19:08:23Z | — | https://github.com/ajrmooreuk/pfi-vhf-nutrition-app-dev/pull/22 |

---

## 5. Ontology Distribution Summary

### Source registry: ont-registry-index.json v9.0.0

| Statistic | Count |
|-----------|:-----:|
| Total ontologies | 42 |
| Compliant | 37 |
| Superseded | 1 |
| Placeholder | 2 |
| Deprecated | 2 |

### Filtered counts by series subscription

| Series | Ontologies | PFIs receiving |
|--------|:----------:|:--------------:|
| VE-Series (incl. VSOM-SA, VSOM-SC) | 15 | All 6 |
| PE-Series (incl. EA-CORE, EA-TOGAF, EA-MSFT, DS) | 8 | BAIV, W4M, W4M-WWG, W4M-EOMS, VHF |
| RCSG-Series (incl. GRC-01..06 sub-series) | 11 | BAIV, AIRL, W4M-WWG, W4M-EOMS, VHF |
| Foundation (incl. ORG-CTX sub-series) | 5 | All 6 |
| Orchestration | 1 | BAIV, W4M, W4M-WWG, W4M-EOMS, VHF |

### Filtered totals by PFI

| PFI | VE | PE | RCSG | Foundation | Orchestration | **Total** |
|-----|:--:|:--:|:----:|:----------:|:-------------:|:---------:|
| PFI-BAIV | 15 | 8 | 11 | 5 | 1 | **40** |
| PFI-AIRL-CAF-AZA | 15 | — | 11 | 5 | — | **31** |
| PFI-W4M | 15 | 8 | — | 5 | 1 | **29** |
| PFI-W4M-WWG | 15 | 8 | 11 | 5 | 1 | **40** |
| PFI-W4M-EOMS | 15 | 8 | 11 | 5 | 1 | **40** |
| PFI-VHF | 15 | 8 | 11 | 5 | 1 | **40** |

### Excluded from all snapshots

| Ontology | Status | Reason |
|----------|--------|--------|
| CA-ONT (Competitive Analysis) | deprecated | Absorbed into ORG-CONTEXT v3.0.0 |
| CL-ONT (Competitive Landscape) | deprecated | Absorbed into ORG-CONTEXT v3.0.0 |

---

## 6. Infrastructure Changes Made

| Item | Detail |
|------|--------|
| **Workflow created** | `.github/workflows/pfc-release.yml` (commit 95b3449, then fix 65a52f0) |
| **Manifest created** | `PBS/ONTOLOGIES/ontology-library/pfc-release-manifest.json` |
| **Registry fixed** | `ont-registry-index.json` — PFI-BAIV `ontologySeries` populated |
| **Secret set** | `PROMOTION_PAT` on ajrmooreuk/Azlan-EA-AAA (2026-02-19T18:37:22Z) |
| **Labels created** | `pfc-core-release` on all 6 PFI dev repos |
| **Pin check fixed** | Uses `matrix.pfcPin` from registry, not pfc-version file content |
| **Tag created** | `pfc-v1.0.0` on Azlan-EA-AAA |

---

## 7. Commits to Azlan-EA-AAA

| SHA | Message | Files |
|-----|---------|:-----:|
| 95b3449 | feat: implement PFC-Core release pipeline for ontology distribution | 5 |
| 65a52f0 | fix: use registry pfcPin instead of pfc-version file for pin check | 1 |

---

## 8. Release pfc-v2.0.0 — First Sealed Skills Distribution

**Date:** 2026-03-01
**Operator:** ajrmooreuk (via Claude Code)
**Tag:** pfc-v2.0.0
**Significance:** First release with sealed-skills distribution (F31.9). Delivers 25 executable skills to all PFI dev repos.

### Workflow Runs

| # | Run ID | Trigger | Target | Status | Purpose |
|:-:|--------|---------|--------|--------|---------|
| 4 | 22548522137 | workflow_dispatch | PFI-BAIV | success | Dry run — validate sealed skills distribution |
| 5 | 22548556988 | workflow_dispatch | PFI-BAIV | success | Live canary — confirm PR creation with 25 skills |
| 6 | 22548629884 | push (tag) | All 7 PFIs | partial (6/7) | Production release pfc-v2.0.0 |

### Run 4 — Dry Run (BAIV only)

**Run ID:** 22548522137
**Trigger:** `workflow_dispatch` with `target_pfis=PFI-BAIV`, `components=all`, `dry_run=true`

- 25 sealed skills listed in output (all source files found)
- Ontology registry snapshot generated
- guard-core.yml, plugin.json, sealed-skills-manifest.json all copied
- No PR created (dry run)

### Run 5 — Live Canary (BAIV only)

**Run ID:** 22548556988
**Trigger:** `workflow_dispatch` with `target_pfis=PFI-BAIV`, `components=all`, `dry_run=false`

**PR created:** [#44](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/44) — 30 files (25 SKILL.md + ontology-registry.json + pfc-version + sealed-skills-manifest.json + plugin.json + guard-core.yml)

### Run 6 — Production Release pfc-v2.0.0 (All PFIs)

**Run ID:** 22548629884
**Trigger:** `push` tag `pfc-v2.0.0`

### Results per PFI

| PFI | Dev Repo | Status | PR # | Skills |
|-----|----------|:------:|:----:|:------:|
| PFI-BAIV | pfi-baiv-aiv-dev | success | **#45** | 25 |
| PFI-AIRL-CAF-AZA | pfi-airl-caf-aza-dev | success | **#48** | 25 |
| PFI-W4M | pfi-w4m-dev | success | **#3** | 25 |
| PFI-W4M-WWG | pfi-w4m-wwg-dev | success | **#33** | 25 |
| PFI-W4M-EOMS | pfi-w4m-eoms-dev | success | **#33** | 25 |
| PFI-VHF | pfi-vhf-nutrition-app-dev | success | **#24** | 25 |
| PFI-PC-DICE | pfi-dice-dev | **failed** | — | — |

**PFI-PC-DICE failure:** `Not Found` — the `pfi-dice-dev` repo does not exist yet (registered in ont-registry-index.json but not bootstrapped). Non-blocking — will succeed once the repo is created.

### Sealed Skills Distributed (25)

| Category | Count | Skills |
|----------|:-----:|--------|
| lifecycle | 7 | close-out, create-epic, create-feature, create-story, review-hierarchy, setup-project-board, setup-repo |
| ve-pipeline | 9 | pfc-org-context, pfc-macro-analysis, pfc-industry-analysis, pfc-vsom, pfc-okr, pfc-kpi, pfc-vp, pfc-kano, pfc-efs |
| delta-pipeline | 5 | pfc-delta-scope, pfc-delta-evaluate, pfc-delta-leverage, pfc-delta-narrate, pfc-delta-adapt |
| orchestrator | 2 | pfc-ve-pipeline, pfc-delta-pipeline |
| plugin | 1 | pfc-reason |
| ontology | 1 | pfc-oaa-v7 |

### Files delivered per PFI dev repo

| Path | Description |
|------|-------------|
| `pfc-core/skills/{name}/SKILL.md` (x25) | Sealed skill definitions |
| `pfc-core/.claude-plugin/plugin.json` | Sealed skills plugin manifest |
| `pfc-core/sealed-skills-manifest.json` | Governance metadata |
| `pfc-core/ontology-registry.json` | Filtered registry snapshot (v10.9.0) |
| `pfc-core/pfc-version` | Version pin: `pfc-v2.0.0` |
| `.github/workflows/guard-core.yml` | CI gate protecting `pfc-core/` |

### Source registry: ont-registry-index.json v10.9.0

| Statistic | Count |
|-----------|:-----:|
| Total ontologies | 53+ |
| OAA version | 7.0.0 |
| Sealed skills | 25 |

### Post-release notes

- BAIV canary PR #44 (from Run 5) should be closed — superseded by PR #45 (from Run 6)
- BAIV v1.0.0 PR #35 and v1.1.0 PR #43 are still open — should be closed as superseded
- PFI-PC-DICE needs repo bootstrap before next release

---

## 9. Release pfc-v2.1.0 — Graph-Scope Config Distribution (F39.1)

**Date:** 2026-03-01
**Operator:** ajrmooreuk (via Claude Code)
**Tag:** pfc-v2.1.0
**Significance:** Adds per-PFI graph-scope config distribution. Supersedes pfc-v2.0.0 — delivers all v2.0.0 content plus `instance-data/config/graph-scope.json`.

### Workflow Enhancement

Commit `d98991a` added graph-scope distribution to `pfc-release.yml`:
- Source: `PBS/ONTOLOGIES/ontology-library/pfi-graph-scopes/{pfi-id}-graph-scope.json`
- Target: `instance-data/config/graph-scope.json` in each PFI dev repo
- Distributed when `components = "ontology-registry"` or `"all"`

### Pre-release cleanup

| Action | Details |
|--------|---------|
| Closed pfc-v2.0.0 PRs | BAIV #45, AIRL #48, W4M #3, W4M-WWG #33, W4M-EOMS #33, VHF #24 — superseded by v2.1.0 |
| Closed duplicate Epic 2 issues | BAIV #32, AIRL #34, W4M-WWG #29, W4M-EOMS #29 — older duplicates replaced by org-specific versions |

### Workflow Run

| # | Run ID | Trigger | Target | Status |
|:-:|--------|---------|--------|--------|
| 7 | 22551795267 | push (tag) | All 7 PFIs | partial (6/7) |

### Results per PFI

| PFI | Dev Repo | Status | PR # | Skills | Graph Scope |
|-----|----------|:------:|:----:|:------:|:-----------:|
| PFI-BAIV | pfi-baiv-aiv-dev | success | **#46** | 25 | 18v/0g/31h |
| PFI-AIRL-CAF-AZA | pfi-airl-caf-aza-dev | success | **#49** | 25 | 13v/3g/33h |
| PFI-W4M | pfi-w4m-dev | success | **#4** | 25 | 13v/0g/36h |
| PFI-W4M-WWG | pfi-w4m-wwg-dev | success | **#34** | 25 | 8v/6g/35h |
| PFI-W4M-EOMS | pfi-w4m-eoms-dev | success | **#34** | 25 | 25v/0g/24h |
| PFI-VHF | pfi-vhf-nutrition-app-dev | success | **#25** | 25 | 7v/4g/39h |
| PFI-PC-DICE | pfi-dice-dev | **failed** | — | — | — |

**PFI-PC-DICE:** Same 404 as v2.0.0 — repo not yet bootstrapped.

### Files delivered per PFI dev repo (v2.1.0 additions in bold)

| Path | Description |
|------|-------------|
| `pfc-core/skills/{name}/SKILL.md` (x25) | Sealed skill definitions |
| `pfc-core/.claude-plugin/plugin.json` | Sealed skills plugin manifest |
| `pfc-core/sealed-skills-manifest.json` | Governance metadata |
| `pfc-core/ontology-registry.json` | Filtered registry snapshot (v10.9.1) |
| `pfc-core/pfc-version` | Version pin: `pfc-v2.1.0` |
| `.github/workflows/guard-core.yml` | CI gate protecting `pfc-core/` |
| **`instance-data/config/graph-scope.json`** | **Per-PFI visible/ghost/hidden ontology classification** |

### Graph-scope configs distributed

| PFI | Visible | Ghost | Hidden | Total |
|-----|:-------:|:-----:|:------:|:-----:|
| PFI-BAIV | 18 | 0 | 31 | 49 |
| PFI-AIRL-CAF-AZA | 13 | 3 | 33 | 49 |
| PFI-W4M | 13 | 0 | 36 | 49 |
| PFI-W4M-WWG | 8 | 6 | 35 | 49 |
| PFI-W4M-EOMS | 25 | 0 | 24 | 49 |
| PFI-VHF | 7 | 4 | 39 | 50 |

---

*End of audit log.*
