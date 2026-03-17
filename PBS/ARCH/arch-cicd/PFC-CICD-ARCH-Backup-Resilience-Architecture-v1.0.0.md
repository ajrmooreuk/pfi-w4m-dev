# PFC-CICD-ARCH: Backup & Resilience Architecture

**Version:** 1.0.0
**Date:** 2026-03-17
**Status:** Active — implementation in progress
**Epic:** Epic 61: PFC-ARCH-CICD [#947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947)
**Features:** F61.15 [#1155](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1155) · F61.16 [#1176](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1176) · F61.18 [#1178](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1178) · F61.21 [#1181](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1181) · F61.22 [#1182](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1182) · F61.24 [#1206](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1206) · F61.27 [#1210](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1210) · F61.28 [#1211](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1211)
**Source briefs:** [PFC-CICD-BRIEF-Platform-Backup-Resilience-Strategy-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-Platform-Backup-Resilience-Strategy-v1.0.0.md) (parent) · [PFC-CICD-BRIEF-GitHub-Resilience-Backup-Recovery-Strategy-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-GitHub-Resilience-Backup-Recovery-Strategy-v1.0.0.md) · [PFC-CICD-BRIEF-Backup-Audit-Recovery-Strategy-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-Backup-Audit-Recovery-Strategy-v1.0.0.md) · [PFC-CICD-BRIEF-Cloud-Backup-Export-Strategy-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-Cloud-Backup-Export-Strategy-v1.0.0.md)
**Ops guide:** [PFC-CICD-OPS-Backup-Resilience-v1.0.0.md](PFC-CICD-OPS-Backup-Resilience-v1.0.0.md)
**Test plan:** [PFC-CICD-TEST-Backup-Resilience-v1.0.0.md](PFC-CICD-TEST-Backup-Resilience-v1.0.0.md)

---

## 1. Purpose

This document is the architectural reference for PFC platform backup and resilience. It defines the component model, storage tiers, data flows, tooling inventory, and recovery targets. It does not contain strategy decisions (see briefs above) or operational procedures (see ops guide).

**Scope:** PFC monorepo (Azlan-EA-AAA), PFC triad (pfc-dev/test/prod), all PFI instance triads, client-specific PFI repos, CI/CD audit infrastructure. Excludes database schema (Epic 59/10A).

---

## 2. Resilience Goals

| Goal | Target | Metric |
|---|---|---|
| RPO — code repositories | ≤2h bare clone, ≤15m GitLab mirror | Last successful mirror timestamp |
| RPO — strategy documents & ontologies | ≤4h cloud export | Last export timestamp in `backup-status.json` |
| RPO — CI/CD run metadata | ≤24h export | `cicd-governance.json` freshness |
| RTO — full platform recovery | ≤8h | Tested in recovery drill (F61.16) |
| CI/CD failure detection | ≤5 minutes | Alert latency from run failure to notification |

---

## 3. Backup Tier Model

Derived from `instance_type` in `pfc-instance-registry.json` (see ARCH-CICD-005). Never set manually.

| Tier | Scope | Schedule | Targets |
|---|---|---|---|
| **T0** | Hub (Azlan-EA-AAA), PFC triad | Every 2h (launchd) + GitLab continuous | Local bare + GitLab + Azure Blob (MVP) |
| **T1** | All PFI triads, client PFIs, client sub-instances | Every 2h (launchd) + GitLab continuous | Local bare + GitLab + Azure Blob (MVP) |
| **T2** | Non-material auxiliary repos | Daily | Local bare only |

---

## 4. Architecture Layers

### 4.1 Layer 1 — Local Bare Clones (Immediate, live)

```
macOS (Amanda's machine)
  └── launchd plist → every 2h
        └── gh-backup-bare.sh
              ├── git fetch --all (all T0+T1 repos)
              ├── appends to ~/backup/backup.log
              └── writes ~/backup/repos/{owner}/{repo}.git
```

**Scripts:**

| Script | Location | Schedule | Purpose |
|---|---|---|---|
| `gh-backup-bare.sh` | `~/.local/bin/` | Every 2h via launchd | Bare clone refresh for all registered repos |
| `backup-audit.sh` | `~/.local/bin/` | After each backup run | Parses backup.log, detects staleness >8h, creates GH issue if gap found |
| `backup-housekeep.sh` | `~/.local/bin/` | Weekly (Sunday) | Log rotation (30d), disk usage report |

**Output artefacts:**

| File | Purpose |
|---|---|
| `~/backup/backup.log` | Rolling log of all backup runs — repo, timestamp, SHA, status |
| `~/backup/backup-status.json` | Structured status per repo — last backup time, SHA, staleness flag |

### 4.2 Layer 2 — GitLab Pull Mirror (Cross-provider, RPO ≤15m)

```
GitHub repos (primary)
  └── GitLab pull mirror (every 5-15m)
        └── gitlab.com/pfc-backup/ group
              ├── Azlan-EA-AAA (T0)
              ├── pfc-dev / pfc-test / pfc-prod (T0)
              └── pfi-{name}-{dev,test,prod} (T1, all instances)
```

Private repos require mirror token with `read_repository` scope on GitHub. GitLab mirrors are **private**. Client PFI repos mirrored to dedicated PFI backup GitLab group (account-level isolation — S61.15.3).

### 4.3 Layer 3 — Cloud Archive (Azure Blob, MVP target)

```
gh-backup-bare.sh → git bundle create → {repo}.bundle
                                              └── Azure Blob Storage
                                                    ├── Container: pfc-backup-{YYYY-MM}
                                                    ├── Immutable storage policy (F61.22)
                                                    └── Lifecycle: hot 30d → cool 1yr → delete 3yr
```

Also exports:
- Strategy docs (PBS/STRATEGY/) — Markdown files as-is
- Ontology library (PBS/ONTOLOGIES/) — JSON-LD files
- `backup-status.json` + `cicd-governance.json` for dashboard consumption

Secondary target options: AWS S3 (F61.21), SharePoint/OneDrive (F61.20), Google Drive (F61.19).

### 4.4 Layer 4 — GitHub Metadata Export

```
GH Actions: resilience-sweep.yml (scheduled, 15m)
  └── GitHub GraphQL API
        ├── Issues, epics, features, stories → issues-export.json
        ├── Projects v2 item states → projects-export.json
        └── CI/CD workflow run history → workflow-runs-export.json
              └── committed to PBS/PLATFORM/ on main
```

Provides recovery of PM data (epics/features/stories) independent of GitHub availability. Critical for Projects v2 board recovery (no native export from GitHub).

### 4.5 Layer 5 — CI/CD Governance Audit

```
cicd-audit.sh (runs after backup run)
  └── GH API: workflow run status per repo
        ├── detects: failed runs, stalled workflows, promotion gaps
        ├── writes cicd-governance.json
        └── unified alert router (single path — F61.18)
              └── creates GH issue if threshold breached
```

| File | Purpose |
|---|---|
| `cicd-governance.json` | Per-repo workflow health — last run, status, promotion event log |
| Alert router | Single issue creator — prevents alert storms across F61.15/16/18 |

---

## 5. Component Inventory

| Component | Type | Status | Feature |
|---|---|---|---|
| `gh-backup-bare.sh` | Script (macOS) | Live | F61.15 S61.15.1 |
| launchd plist (2h backup) | macOS automation | Live | F61.15 S61.15.2 |
| `backup-audit.sh` | Script (macOS) | In progress | F61.16 |
| `backup-status.json` | Output artefact | In progress | F61.16 |
| `backup-housekeep.sh` | Script (macOS) | Planned | F61.16 |
| GitLab pull mirror (PFC T0) | External service | In progress | F61.15 S61.15.1 |
| GitLab mirror (PFI T1) | External service | Planned | F61.15 |
| PFI backup account mirror | GH account | Planned | F61.15 S61.15.3 |
| Azure Blob Storage export | Cloud service | Planned | F61.22 |
| AWS S3 export | Cloud service | Planned | F61.21 |
| GitHub metadata export | GH Actions workflow | Planned | F61.15 |
| `cicd-audit.sh` | Script | Planned | F61.18 |
| `cicd-governance.json` | Output artefact | Planned | F61.18 |
| Unified alert router | Script/workflow | Planned | F61.18 |
| `resilience-sweep.yml` | GH Actions workflow | Planned | F61.15 |
| Backup scope registry (URG) | Registry extension | Projected | F61.24 |
| DR failover (Milana/Amanda) | GH Actions + docs | Planned | F61.27 |
| Backup dashboard (Workbench) | Workbench view | Planned | F61.28 |

---

## 6. Instance Registry Integration

Each entry in `pfc-instance-registry.json` carries `backup_tier` (T0/T1/T2). Once F61.24 is implemented:

- `gh-backup-bare.sh` reads repo list from registry — no hardcoded arrays
- `cicd-audit.sh` reads target repos from registry
- `resilience-sweep.yml` reads repo list from registry
- New instance registered → auto-enrolled in next backup cycle

---

## 7. Recovery Architecture

Recovery is tiered by scenario:

| Scenario | Recovery path | RTO |
|---|---|---|
| Single repo loss (GitHub) | Restore from local bare clone → `git push --mirror` | <1h |
| GitHub account compromise | Restore from GitLab mirror to new GH account | <4h |
| Local machine loss | Restore from Azure Blob + GitLab mirror | <8h |
| GitHub + GitLab simultaneous outage | Restore from Azure Blob git bundles | <8h |
| Client PFI repo loss | Restore from Owner PFI backup account mirror | <4h |
| Projects/Issues metadata loss | Restore from GitHub metadata export in PBS/PLATFORM/ | <2h manual |

Full recovery runbook: see [PFC-CICD-OPS-Backup-Resilience-v1.0.0.md](PFC-CICD-OPS-Backup-Resilience-v1.0.0.md).

---

## 8. RAID Summary

| ID | Type | Description | Feature | Status |
|---|---|---|---|---|
| R-CICD-001 | Risk | PFC monorepo no cross-provider backup | F61.15 | Open |
| R-CICD-002 | Risk | 15 PFI repos zero backup (L3×I5=15) | F61.15 | Open — ESCALATE |
| R-CICD-003 | Risk | GitHub Projects/Issues metadata not backed up | F61.15 | Open |
| R-CICD-004 | Risk | CI/CD failure cascade undetected | F61.18 | Partially mitigated |
| R-CICD-005 | Risk | PF-Core-BAIV zero backup — JV-confidential | F61.15 | Open — ESCALATE |
| R-CICD-008 | Risk | Client production environments not in backup scope | F61.15 S61.15.3 | Open |
| R-CICD-009 | Risk | New repos not auto-enrolled in backup | F61.24 | Not yet covered |
| R-CICD-013 | Risk | Recovery runbook not tested — RTO unverified | F61.16 | Open |

Full RAID register: [PFC-CICD-BRIEF-Platform-Backup-Resilience-Strategy-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-Platform-Backup-Resilience-Strategy-v1.0.0.md).

---

## 9. Related Documents

| Document | Location | Relevance |
|---|---|---|
| ARCH-CICD-README | [README.md](README.md) | Set index |
| ARCH-CICD-005 | [05-github-pm-automation.md](05-github-pm-automation.md) | Instance registry + backup_tier derivation |
| PFC-CICD-OPS-Backup-Resilience | [PFC-CICD-OPS-Backup-Resilience-v1.0.0.md](PFC-CICD-OPS-Backup-Resilience-v1.0.0.md) | Operations runbook |
| PFC-CICD-TEST-Backup-Resilience | [PFC-CICD-TEST-Backup-Resilience-v1.0.0.md](PFC-CICD-TEST-Backup-Resilience-v1.0.0.md) | Test plan |
| Platform Backup Resilience Strategy (parent brief) | [PBS/STRATEGY/](../../STRATEGY/PFC-CICD-BRIEF-Platform-Backup-Resilience-Strategy-v1.0.0.md) | All decisions, RAID, feature roadmap |
