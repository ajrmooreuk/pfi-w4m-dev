# PFC-CICD-TEST: Backup & Resilience — Test Plan

**Version:** 1.0.0
**Date:** 2026-03-17
**Status:** Active — tests pending execution
**Epic:** Epic 61: PFC-ARCH-CICD [#947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947)
**Features:** F61.15 [#1155](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1155) · F61.16 [#1176](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1176) · F61.18 [#1178](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1178) · F61.22 [#1182](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1182)
**Architecture doc:** [PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md](PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md)
**Ops guide:** [PFC-CICD-OPS-Backup-Resilience-v1.0.0.md](PFC-CICD-OPS-Backup-Resilience-v1.0.0.md)

---

## 1. Purpose

This test plan defines the acceptance criteria, test cases, and recovery drill procedure for the PFC backup and resilience system. Each test maps to a specific architecture layer and feature. Tests must be executed in the order defined — later tests depend on earlier layers being verified.

---

## 2. Test Scope

| Layer | Component | Feature | Tests |
| --- | --- | --- | --- |
| L1 | Local bare clones + launchd | F61.15, F61.16 | T-L1-001 to T-L1-005 |
| L2 | GitLab pull mirror | F61.15 | T-L2-001 to T-L2-003 |
| L3 | Azure Blob cloud archive | F61.22 | T-L3-001 to T-L3-003 |
| L4 | GitHub metadata export | F61.15 | T-L4-001 to T-L4-002 |
| L5 | CI/CD governance audit | F61.18 | T-L5-001 to T-L5-003 |
| DR | Full recovery drill | F61.16 | T-DR-001 |

---

## 3. Layer 1 — Local Bare Clones

### T-L1-001: Backup Run Execution

**Precondition:** `gh-backup-bare.sh` installed at `~/.local/bin/`. launchd plist loaded.

**Steps:**
1. Run `~/.local/bin/gh-backup-bare.sh` manually
2. Inspect `~/backup/backup.log` for each registered T0 and T1 repo

**Pass criteria:**
- All T0 and T1 repos show a SUCCESS entry in `backup.log` within the last run
- `~/backup/repos/{owner}/{repo}.git` exists for every registered repo
- No ERROR entries in log

---

### T-L1-002: Backup Schedule Verification

**Precondition:** launchd plist for `com.pfc.gh-backup-bare` is loaded.

**Steps:**
1. `launchctl list | grep gh-backup` — confirms plist loaded
2. `launchctl list com.pfc.gh-backup-bare` — confirms last run time is ≤2h ago

**Pass criteria:**
- Plist listed and active
- LastExitStatus = 0 in launchctl output
- Last run within 2h 15m (schedule + tolerance)

---

### T-L1-003: backup-status.json Integrity

**Precondition:** T-L1-001 passed.

**Steps:**
1. Read `~/backup/backup-status.json`
2. Verify schema: each entry has `name`, `last_backup_time`, `sha`, `staleness_flag`
3. Confirm `staleness_flag` is `false` for all T0 and T1 repos
4. Confirm `last_backup_time` is within the last 2.5h for all T0 and T1 repos

**Pass criteria:**
- Valid JSON, all required fields present
- No `staleness_flag: true` entries for registered repos
- Timestamps within tolerance

---

### T-L1-004: Bare Clone Integrity Check

**Precondition:** T-L1-001 passed.

**Steps:**
1. For each registered T0 repo: `git -C ~/backup/repos/{owner}/{repo}.git fsck --no-dangling`
2. For a sample of 3 T1 repos: same command

**Pass criteria:**
- No errors reported by `git fsck`
- All expected refs present (`git -C ~/backup/repos/{owner}/{repo}.git show-ref | head`)

---

### T-L1-005: Staleness Alert — backup-audit.sh

**Precondition:** `backup-audit.sh` installed. A test repo entry exists in backup-status.json with `staleness_flag: true` (or simulate by injecting a stale timestamp).

**Steps:**
1. Inject a stale entry with `last_backup_time` >8h ago into a test entry in `backup-status.json`
2. Run `~/.local/bin/backup-audit.sh` manually
3. Check GitHub for a new issue with label `type:incident`

**Pass criteria:**
- GitHub issue created with correct title referencing the stale repo
- Issue body includes repo name, last backup time, and staleness gap
- Only one issue created (no duplicate storm)

**Cleanup:** Close the test issue; restore `backup-status.json` to pre-test state.

---

## 4. Layer 2 — GitLab Pull Mirror

### T-L2-001: Mirror Active and Updating

**Precondition:** GitLab pull mirror configured for all T0 repos.

**Steps:**
1. Log into `gitlab.com/pfc-backup/`
2. For each T0 repo: Settings → Repository → Mirroring repositories
3. Check "Last successful update" timestamp

**Pass criteria:**
- Mirror status: active (no error state)
- Last successful update: ≤15 minutes ago
- All branches visible in GitLab mirror match GitHub source

---

### T-L2-002: Mirror Divergence Check

**Steps:**
1. On GitHub, identify the latest commit SHA on `main` for `Azlan-EA-AAA`
2. On GitLab mirror: check the same branch HEAD SHA

**Pass criteria:**
- GitLab HEAD SHA matches GitHub HEAD SHA within a 15-minute observation window

---

### T-L2-003: Private Repo Mirror Access

**Precondition:** GitHub PAT with `read_repository` scope is configured in the mirror.

**Steps:**
1. Confirm private repos (BAIV, W4M triads) are mirroring successfully
2. Verify mirror token has not expired (GitLab → Access tokens for the mirror service account)

**Pass criteria:**
- No authentication errors on private repo mirrors
- Mirrors updating within RPO ≤15m

---

## 5. Layer 3 — Azure Blob Cloud Archive

*Tests applicable once F61.22 is implemented.*

### T-L3-001: Bundle Creation and Upload

**Steps:**
1. Run `gh-backup-bare.sh` with cloud export enabled
2. Check Azure Blob container `pfc-backup-{YYYY-MM}` for new bundle files

**Pass criteria:**
- `{repo}.bundle` exists in container for each T0 and T1 repo
- Bundle file size is non-zero
- Upload timestamp is within the last run window

---

### T-L3-002: Bundle Integrity Verification

**Steps:**
1. Download the most recent `Azlan-EA-AAA.bundle` from Azure
2. Run `git bundle verify Azlan-EA-AAA.bundle`
3. Clone from bundle: `git clone Azlan-EA-AAA.bundle test-restore`

**Pass criteria:**
- `git bundle verify` reports no errors
- Clone succeeds and HEAD is at the expected SHA

---

### T-L3-003: Immutable Storage Policy

**Steps:**
1. Attempt to delete a bundle from the `pfc-backup-{YYYY-MM}` container (must fail)
2. Verify lifecycle policy is set: hot 30d → cool 1yr → delete 3yr

**Pass criteria:**
- Delete attempt rejected (immutable policy active)
- Lifecycle rules visible and correctly configured in Azure portal

---

## 6. Layer 4 — GitHub Metadata Export

*Tests applicable once `resilience-sweep.yml` is implemented.*

### T-L4-001: Export Files Committed to PBS/PLATFORM/

**Steps:**
1. Check `PBS/PLATFORM/` for `issues-export.json`, `projects-export.json`, `workflow-runs-export.json`
2. Verify commit timestamp — should be ≤24h old

**Pass criteria:**
- All three export files present and valid JSON
- Commit timestamp within last 24h
- `issues-export.json` contains at least all known open epics

---

### T-L4-002: Export Coverage Spot-Check

**Steps:**
1. Open `issues-export.json`
2. Confirm Epic 61 (#947) is present with correct title and status
3. Confirm a sample of 5 features are present with correct parent links

**Pass criteria:**
- All sampled items present with expected fields
- Parent/child relationships intact in export data

---

## 7. Layer 5 — CI/CD Governance Audit

*Tests applicable once `cicd-audit.sh` and F61.18 are implemented.*

### T-L5-001: cicd-governance.json Freshness

**Steps:**
1. Check `cicd-governance.json` exists and has a `generated_at` timestamp
2. Confirm timestamp is ≤24h old

**Pass criteria:**
- File exists, valid JSON
- `generated_at` within last 24h

---

### T-L5-002: Failed Workflow Detection

**Precondition:** A test workflow run has been deliberately failed in a sandbox repo.

**Steps:**
1. Run `cicd-audit.sh` manually
2. Check `cicd-governance.json` for the failed repo entry
3. Confirm a GitHub issue is created for the failed run

**Pass criteria:**
- Failed repo flagged with `last_run_status: "failure"` in JSON
- GitHub issue created with correct label and body
- Issue created within 5 minutes of audit run (alert latency target)

---

### T-L5-003: Alert Storm Prevention

**Precondition:** Multiple repos show failed runs simultaneously (simulate).

**Steps:**
1. Inject 3 failed-run entries into `cicd-governance.json`
2. Run unified alert router
3. Count GitHub issues created

**Pass criteria:**
- Exactly 1 issue created (not 3 separate issues)
- Issue body references all 3 affected repos

---

## 8. Full Recovery Drill (F61.16)

### T-DR-001: Simulated Single Repo Recovery

**Schedule:** Quarterly — minimum once before F61.16 closure.

**Scope:** Restore one T0 repo from local bare clone to a new GitHub repo (sandbox). Do not restore to production GitHub.

**Steps:**

1. Identify a T0 repo bare clone: `~/backup/repos/{owner}/{repo}.git`
2. Create a sandbox destination repo on GitHub: `gh repo create {owner}/{repo}-drill --private`
3. Execute restore: `git -C ~/backup/repos/{owner}/{repo}.git push --mirror https://github.com/{owner}/{repo}-drill.git`
4. Verify the restored repo:
   - Correct HEAD SHA matches pre-drill snapshot
   - All branches present
   - Tag history intact
5. Record results: start time, end time, total RTO achieved

**Pass criteria:**
- Restore completes without errors
- HEAD SHA, branches, and tags match the source
- RTO achieved ≤1h

**Post-drill:** Delete sandbox repo. Document result and update RAID R-CICD-013 status.

---

## 9. Acceptance Summary

| Test ID | Layer | Feature | Status |
| --- | --- | --- | --- |
| T-L1-001 | L1 Local bare | F61.15 | Pending |
| T-L1-002 | L1 launchd | F61.15 | Pending |
| T-L1-003 | L1 status JSON | F61.16 | Pending |
| T-L1-004 | L1 fsck | F61.16 | Pending |
| T-L1-005 | L1 audit alert | F61.16 | Pending |
| T-L2-001 | L2 GitLab mirror | F61.15 | Pending |
| T-L2-002 | L2 mirror divergence | F61.15 | Pending |
| T-L2-003 | L2 private mirror | F61.15 | Pending |
| T-L3-001 | L3 Azure upload | F61.22 | Not yet — F61.22 pending |
| T-L3-002 | L3 bundle verify | F61.22 | Not yet — F61.22 pending |
| T-L3-003 | L3 immutable policy | F61.22 | Not yet — F61.22 pending |
| T-L4-001 | L4 metadata export | F61.15 | Not yet — sweep.yml pending |
| T-L4-002 | L4 export coverage | F61.15 | Not yet — sweep.yml pending |
| T-L5-001 | L5 governance JSON | F61.18 | Not yet — F61.18 pending |
| T-L5-002 | L5 failure detection | F61.18 | Not yet — F61.18 pending |
| T-L5-003 | L5 alert storm | F61.18 | Not yet — F61.18 pending |
| T-DR-001 | DR drill | F61.16 | Not yet scheduled |

---

## 10. Related Documents

| Document | Location |
| --- | --- |
| Backup & Resilience Architecture | [PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md](PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md) |
| Backup & Resilience Ops Guide | [PFC-CICD-OPS-Backup-Resilience-v1.0.0.md](PFC-CICD-OPS-Backup-Resilience-v1.0.0.md) |
| Platform Backup Resilience Strategy | [PBS/STRATEGY/PFC-CICD-BRIEF-Platform-Backup-Resilience-Strategy-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-Platform-Backup-Resilience-Strategy-v1.0.0.md) |
