# Release Bulletin: F61.29 — PFI Project Board Automation

**Version:** 1.0.0
**Date:** 2026-03-17
**Epic:** Epic 61 (#947) — PBS-PFC-ARCHITECTURE.CICD-DevSecOps
**Feature:** F61.29: PFI Project Board Automation [#1241](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1241)
**Status:** COMPLETE (guard-core stub follow-up [#1264](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1264) open)

---

## Summary

Full implementation of the two-project model across all four active PFI dev repos. Every new issue now auto-routes to Engineering or Product project by label. Three quality workflows are now `workflow_call` stubs delegating to hub. Registry carries full instance hierarchy fields. Skills updated to wire everything at instance creation time.

---

## What Was Delivered

### 1. auto-add-to-projects.yml — All 4 Active PFI Dev Repos

New workflow deployed to each PFI dev repo. Routes newly opened issues by label:

- `type:epic`, `type:feature`, `type:story`, `type:wbs`, `type:pbs` → **Product project**
- All other issues → **Engineering project**

Requires `PROJECT_PAT` (classic PAT, `project` scope) — now set on all four repos.

| Repo | Engineering PVT | Product PVT |
| --- | --- | --- |
| `pfi-airl-caf-aza-dev` | `PVT_kwHODCCs_c4BSBN5` | `PVT_kwHODCCs_c4BSBN6` |
| `pfi-w4m-dev` | `PVT_kwHODCCs_c4BSCdM` | `PVT_kwHODCCs_c4BSCdO` |
| `pfi-baiv-aiv-dev` | `PVT_kwHODCCs_c4BSCdP` | `PVT_kwHODCCs_c4BSCdR` |
| `pfi-w4m-rcs-dev` | `PVT_kwHODCCs_c4BSCdS` | `PVT_kwHODCCs_c4BSCdU` |

### 2. workflow_call Stubs — 3 Workflows × 4 Repos (12 files)

Replaced replicated hub quality workflows with thin stubs that delegate via `workflow_call`:

| Workflow | Hub source |
| --- | --- |
| `validate-issue-naming.yml` | `ajrmooreuk/Azlan-EA-AAA/.github/workflows/validate-issue-naming.yml@main` |
| `enforce-registry-link.yml` | `ajrmooreuk/Azlan-EA-AAA/.github/workflows/enforce-registry-link.yml@main` |
| `validate-labels.yml` | `ajrmooreuk/Azlan-EA-AAA/.github/workflows/validate-labels.yml@main` |

Future hub logic changes propagate to all PFI repos automatically — no per-repo updates needed.

### 3. Registry Hierarchy Fields — All 10 PFI Instances

`ont-registry-index.json` now carries full hierarchy metadata on every PFI entry:

| Field | Values |
| --- | --- |
| `instance_type` | `OWNER_INSTANCE` \| `PRODUCT` \| `CLIENT_SUB_INSTANCE` \| `CLIENT_PFI` |
| `parent_pfi` | null (for OWNER_INSTANCE) or owner PFI `@id` |
| `backup_tier` | `T1` for all PFI instances |
| `engineering_project_id` | `PVT_...` (set for 4 active OWNER_INSTANCEs) |
| `product_project_id` | `PVT_...` (set for 4 active OWNER_INSTANCEs) |

PRODUCT instances (W4M-WWG, W4M-EOMS, W4M-ANTQ) carry `parent_pfi: "PFI-W4M"`.

### 4. PROJECT_PAT — New Secret, All 4 Active PFI Dev Repos

Classic PAT with `project` scope. Required for `auto-add-to-projects.yml`. Distinct from `PROMOTION_PAT` — see ARCH-CICD-003 glossary for full PAT type definitions.

| Repo | PROJECT_PAT | PROMOTION_PAT |
| --- | --- | --- |
| `pfi-airl-caf-aza-dev` | ✓ | ✓ |
| `pfi-w4m-dev` | ✓ | ✓ |
| `pfi-baiv-aiv-dev` | ✓ | ✓ |
| `pfi-w4m-rcs-dev` | ✓ | ✓ |

### 5. Standing Operational Epics — 29 Issues Created

7 standing epics created per PFI Engineering project (28 total across AIRL, W4M, BAIV, RCS) + PFC-NEWS epic on hub and all 4 PFI dev repos:

- PE-Marketing, PE-Sales, PE-Operations, Strategy, PE-Development, PE-Support, PPM
- PFI-NEWS-Docs-Ideas (per PFI) + Epic 80: PFC-NEWS on hub [#1255](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1255)

### 6. Skills Updated

| Skill | Changes |
| --- | --- |
| `setup-project-board` | Captures PVT node ID via GraphQL; writes to registry; Hub-admin-only rule documented |
| `setup-repo` | Accepts hierarchy params; generates `auto-add-to-projects.yml`; deploys stubs; writes full registry entry |

---

## Open Follow-Up

| Issue | Description |
| --- | --- |
| [#1264](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1264) | Add `workflow_call` to hub `guard-core.yml` and stub all 4 PFI repos |

---

## Documentation Updated

| Document | Version | Changes |
| --- | --- | --- |
| ARCH-CICD-003 glossary | 1.3.0 | PROJECT_PAT, PROMOTION_PAT, Classic PAT entries added/expanded |
| ARCH-CICD-004 operating guide | 1.4.0 | §7A PROJECT_PAT setup added; status → Active |
| ARCH-CICD-005 PM automation | 1.1.0 | Deployment status table; guard-core follow-up noted; F61.29 marked COMPLETE |
| ARCH-CICD README | — | Version table updated; F61.29 COMPLETE; bulletin linked |
| `ont-registry-index.json` | v10.8.x | instance_type, parent_pfi, backup_tier on all 10 PFI entries |

---

> Epic 61: [#947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947) | Brief: `PBS/STRATEGY/PFC-CICD-BRIEF-Workflow-Governance-Project-Automation-Cascade-v1.0.0.md`
