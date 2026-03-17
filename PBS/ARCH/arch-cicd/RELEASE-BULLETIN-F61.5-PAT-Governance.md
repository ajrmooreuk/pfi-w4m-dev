# Release Bulletin: F61.5 — PAT Governance & Drift Detection

**Version:** 1.0.0
**Date:** 2026-03-08
**Epic:** Epic 61 (#947) — PBS-PFC-ARCHITECTURE.CICD-DevSecOps
**Feature:** F61.5: PAT Governance & Drift Detection
**Stories:** S61.5.1 (set-pat script), S61.5.2 (drift-detection workflow)

---

## Summary

Two new CI/CD artefacts that automate PROMOTION_PAT management across all PFI dev+test repos, driven by the ontology registry so that adding a new PFI instance automatically includes it in PAT rotation and drift detection.

## What's New

### 1. Registry-Driven PAT Rotation Script

**File:** `.github/scripts/set-pat-all-triads.sh`

Sets `PROMOTION_PAT` on every PFI dev+test repo in one command. Reads repos from `ont-registry-index.json` — no hardcoded repo lists.

```bash
echo "ghp_YOUR_TOKEN" | .github/scripts/set-pat-all-triads.sh
```

**Behaviour:**

- Reads `pfiInstances[].hubSpokeConfig.repos.{dev,test}` from registry
- Skips PFIs without repos (RCS, ANTQ) and repos that don't exist yet (PC-DICE)
- Also sets the secret on the hub repo (Azlan-EA-AAA)
- Scope: dev + test only — prod excluded by design

### 2. PAT Drift Detection Workflow

**File:** `.github/workflows/pat-drift-detection.yml`

Validates that `PROMOTION_PAT` is set on every registered PFI dev+test repo.

**Triggers:**

- Weekly: Monday 08:00 UTC
- On push: when `ont-registry-index.json` changes on main
- Manual: workflow dispatch

**On failure:** error message directs to `set-pat-all-triads.sh`.

## Current PAT Coverage (Pre-Fix)

| PFI | Dev | Test |
|-----|-----|------|
| BAIV | Yes | Yes |
| AIRL-CAF-AZA | Yes | Yes |
| W4M | **No** | **No** |
| W4M-WWG | **No** | **No** |
| W4M-EOMS | **No** | **No** |
| VHF | **No** | **No** |
| W4M-RCS | **No** | **No** |

**Action required:** Run `set-pat-all-triads.sh` after commit to bring all repos into compliance.

## PAT Requirements (Classic Token)

| Scope | Required | Purpose |
|-------|----------|---------|
| `repo` | Yes | Cross-repo checkout, push, create PRs |
| `workflow` | Yes | Update workflow files in PFI repos |

> Must be a Classic PAT (`ghp_` prefix). Fine-grained tokens do not support cross-repo operations.

## Documentation Updated

- `PBS/ARCHITECTURE/arch-cicd/04-operating-guide.md` → v1.3.0
  - Section 7 (PROMOTION_PAT Setup): automated script + manual fallback
  - Section 12 (Drift Detection): PAT drift added alongside convention drift
  - Section 13 (Monitoring Checklist): weekly PAT drift check added
  - Section 14 (Quick Reference): 3 new entries

## Epic 61 Updates

- Epic 60 (#859) absorbed into Epic 61 (#947) — all features carried forward
- F61.5 added (PAT Governance): S61.5.1 and S61.5.2 complete
- Remaining: S61.5.3 (initial PAT set), S61.5.4 (rotation SOP doc)

## Dependencies

- `ont-registry-index.json` must have `hubSpokeConfig.repos` for each PFI
- `gh` CLI must be authenticated with a token that has `admin:org` to list secrets
- `PROMOTION_PAT` secret on hub repo used by `pat-drift-detection.yml`

---

> Epic 61: #947 | Briefing: `PBS/STRATEGY/BRIEFING-Epic61-PBS-PFC-ARCHITECTURE-CICD-DevSecOps.md`
