# PFC-CICD-OPS: Backup & Resilience — Operations Guide

**Version:** 1.0.0
**Date:** 2026-03-17
**Status:** Active — implementation in progress
**Epic:** Epic 61: PFC-ARCH-CICD [#947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947)
**Architecture doc:** [PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md](PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md)
**Test plan:** [PFC-CICD-TEST-Backup-Resilience-v1.0.0.md](PFC-CICD-TEST-Backup-Resilience-v1.0.0.md)

---

## 1. Purpose

Day-to-day operating procedures for the PFC backup and resilience system. This guide covers routine monitoring, failure response, and recovery execution. For architecture decisions and layer definitions see the ARCH doc above.

---

## 2. Routine Operations

### 2.1 Monitoring Backup Health

**Check:** `~/backup/backup-status.json` — updated after each `gh-backup-bare.sh` run.

```bash
# Quick status check — show repos not backed up in the last 8 hours
jq '.repos[] | select(.staleness_flag == true) | {repo: .name, last_backup: .last_backup_time}' \
  ~/backup/backup-status.json
```

**Check:** `~/backup/backup.log` — rolling log of all backup runs.

```bash
# Last 20 backup events
tail -20 ~/backup/backup.log

# Check for errors in last run
grep "ERROR\|FAIL" ~/backup/backup.log | tail -10
```

**Expected:** All T0 and T1 repos show a backup timestamp within the last 2.5 hours (2h schedule + tolerance).

### 2.2 Verifying launchd Schedule

```bash
# Check backup plist is loaded
launchctl list | grep gh-backup

# Check last run time
launchctl list com.pfc.gh-backup-bare
```

If the plist is not loaded:

```bash
launchctl load ~/Library/LaunchAgents/com.pfc.gh-backup-bare.plist
```

### 2.3 Verifying GitLab Mirror Status

1. Log in to `gitlab.com/pfc-backup/`
2. For each T0 repo: **Settings → Repository → Mirroring repositories**
3. Confirm "Last successful update" timestamp is within 15 minutes
4. If mirror shows error: re-trigger manually via "Update now" button

### 2.4 Checking CI/CD Governance

Once `cicd-governance.json` is live (F61.18):

```bash
# Show repos with failed workflow runs
jq '.repos[] | select(.last_run_status != "success") | {repo: .name, status: .last_run_status, run_at: .last_run_at}' \
  ~/backup/cicd-governance.json
```

---

## 3. Failure Response Procedures

### 3.1 Backup Run Failure

**Symptoms:** `backup-status.json` shows staleness flag; `backup-audit.sh` creates a GitHub issue.

**Steps:**

1. Check `~/backup/backup.log` for the failing repo and error message
2. Verify GitHub PAT is valid: `gh auth status`
3. Re-run manually for the failing repo:

```bash
cd ~/backup/repos/{owner}
git fetch --all
```

4. If PAT expired: rotate via `gh auth login` and update any dependent secrets
5. If network/DNS issue: wait for recovery and re-run `gh-backup-bare.sh` manually
6. Close the GitHub issue created by `backup-audit.sh` once resolved

### 3.2 GitLab Mirror Broken

**Symptoms:** Mirror shows error state; RPO ≤15m target at risk.

**Steps:**

1. Navigate to affected mirror in GitLab → **Settings → Repository → Mirroring**
2. Identify error: typically expired GitHub token or private repo permission change
3. Update mirror token (GitHub → Developer Settings → PAT → `read_repository` scope)
4. Paste new token into GitLab mirror config → Save → "Update now"
5. Confirm mirror updates successfully

### 3.3 Backup Audit Alert — Gap Detected

**Trigger:** `backup-audit.sh` creates a GitHub issue when a repo has not been backed up in >8 hours.

**Steps:**

1. Check the created issue for which repo(s) are affected
2. Follow 3.1 (backup run failure) to restore the run
3. Once backup confirmed: close the issue with a comment referencing the resolution
4. If the gap was caused by a launchd plist failure: reload and verify schedule (§2.2)

---

## 4. Recovery Procedures

### 4.1 Restore a Single Repo from Local Bare Clone

**When:** A GitHub repo is accidentally deleted, corrupted, or needs rollback.

```bash
# Step 1 — navigate to the bare clone
cd ~/backup/repos/{owner}/{repo}.git

# Step 2 — create the replacement repo on GitHub
gh repo create {owner}/{repo} --private

# Step 3 — push all branches and tags
git push --mirror https://github.com/{owner}/{repo}.git
```

**RTO target:** <1h

### 4.2 Restore from GitLab Mirror

**When:** GitHub account is compromised or GitHub is unavailable; local machine is intact.

```bash
# Step 1 — clone from GitLab
git clone --mirror https://gitlab.com/pfc-backup/{repo}.git {repo}.git

# Step 2 — create replacement GitHub repo
gh repo create {owner}/{repo} --private

# Step 3 — push mirror
cd {repo}.git
git push --mirror https://github.com/{owner}/{repo}.git
```

**RTO target:** <4h for full account recovery

### 4.3 Restore from Azure Blob Bundles

**When:** Both GitHub and local machine are unavailable (full disaster recovery).

```bash
# Step 1 — download bundle from Azure Blob
az storage blob download \
  --container-name pfc-backup-{YYYY-MM} \
  --name {repo}.bundle \
  --file {repo}.bundle \
  --account-name {storage_account}

# Step 2 — verify bundle integrity
git bundle verify {repo}.bundle

# Step 3 — clone from bundle
git clone {repo}.bundle {repo}

# Step 4 — create replacement GitHub repo and push
gh repo create {owner}/{repo} --private
cd {repo}
git remote set-url origin https://github.com/{owner}/{repo}.git
git push --mirror
```

**RTO target:** <8h

### 4.4 Restore GitHub Projects / Issues Metadata

**When:** GitHub Projects v2 board or issue data is lost (no native GH export).

1. Locate the most recent export in `PBS/PLATFORM/`:
   - `issues-export.json` — all issues, epics, features, stories
   - `projects-export.json` — Projects v2 item states
   - `workflow-runs-export.json` — CI/CD run history
2. Re-create issues via GitHub API or manual import from JSON
3. Re-populate Projects v2 board fields manually (status, PBS ID, etc.)

**RTO target:** <2h (manual effort required)

### 4.5 Client PFI Repo Recovery

**When:** A client PFI repo is lost and needs restoration.

1. Identify the Owner PFI backup account mirror for the affected client repo (see [ARCH doc §4.2](PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md))
2. Follow procedure 4.2 (GitLab mirror restore) using the PFI backup account as source
3. Notify the Owner PFI administrator immediately — client data recovery requires Owner PFI authorisation

**RTO target:** <4h

---

## 5. Housekeeping

### 5.1 Log Rotation (backup-housekeep.sh — weekly, Sunday)

`backup-housekeep.sh` automatically:

- Rotates `backup.log` entries older than 30 days
- Reports disk usage for `~/backup/repos/`
- Flags any bare clone directories not in the registry (orphaned repos)

Manual check:

```bash
du -sh ~/backup/repos/
ls ~/backup/repos/
```

### 5.2 Azure Blob Lifecycle (once F61.22 live)

Storage lifecycle is managed by Azure policy — no manual action required:

- Hot tier: 0–30 days (active recovery window)
- Cool tier: 30 days – 1 year
- Delete: after 3 years

Review lifecycle policy quarterly or after any storage account reconfiguration.

---

## 6. Alert Router Reference

Once the unified alert router (F61.18) is live, all backup and CI/CD alerts route through a single issue-creation path. This prevents alert storms where multiple scripts all create issues for the same event.

**Alert sources → single GitHub issue:**

| Source | Trigger | Issue label |
| --- | --- | --- |
| `backup-audit.sh` | Repo staleness >8h | `type:incident`, `domain:cicd` |
| `cicd-audit.sh` | Failed workflow, promotion gap | `type:incident`, `domain:cicd` |
| `resilience-sweep.yml` | Metadata export failure | `type:incident`, `domain:cicd` |

---

## 7. Related Documents

| Document | Location |
| --- | --- |
| Backup & Resilience Architecture | [PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md](PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md) |
| Backup & Resilience Test Plan | [PFC-CICD-TEST-Backup-Resilience-v1.0.0.md](PFC-CICD-TEST-Backup-Resilience-v1.0.0.md) |
| Operating Guide (ARCH-CICD-004) | [04-operating-guide.md](04-operating-guide.md) |
| Platform Backup Resilience Strategy | [PBS/STRATEGY/PFC-CICD-BRIEF-Platform-Backup-Resilience-Strategy-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-Platform-Backup-Resilience-Strategy-v1.0.0.md) |
