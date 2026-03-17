# Test Plan: F31.9 — Sealed Skill Distribution

**Date:** 2026-03-01
**Feature:** F31.9 (#819)
**Epic:** Epic 31 (#394)
**Status:** Manual validation (CI/CD infrastructure — no unit tests)

---

## Scope

Validate that sealed skills flow from PFC-Core hub (Azlan-EA-AAA) to PFI triad dev repos as executable-but-not-editable artefacts.

---

## Test Cases

### TC-1: guard-core.yml exists in hub and template repos

| Step | Action | Expected |
|------|--------|----------|
| 1.1 | Check `Azlan-EA-AAA/.github/workflows/guard-core.yml` | File exists, triggers on `pull_request` paths `pfc-core/**` |
| 1.2 | Check `AZLAN-CI-CD/.github/workflows/guard-core.yml` | File exists (pushed via GitHub API, commit `a95b3c1`) |
| 1.3 | Verify workflow logic | Allows `github-actions[bot]` or `pfc-core-release` label; blocks all others |

**Result:** Pass — both files verified in prior session.

### TC-2: sealed-skills-manifest.json is valid

| Step | Action | Expected |
|------|--------|----------|
| 2.1 | Parse `azlan-github-workflow/sealed-skills-manifest.json` | Valid JSON, `sealedSkills` array with 1 entry (close-out) |
| 2.2 | Verify source/target paths | `sourcePath` points to existing `azlan-github-workflow/skills/close-out/SKILL.md` |
| 2.3 | Verify governance block | `authorisedAuthor: github-actions[bot]`, `overrideType: none`, `editableInTriad: false` |

**Result:** Pass — manifest created and verified.

### TC-3: plugin.json enables Claude Code discovery

| Step | Action | Expected |
|------|--------|----------|
| 3.1 | Check `pfc-core/.claude-plugin/plugin.json` in Azlan-EA-AAA | Valid JSON, `name: pfc-core-sealed` |
| 3.2 | Check `pfc-core/.claude-plugin/plugin.json` in AZLAN-CI-CD | Same file (pushed commit `a95b3c1`) |
| 3.3 | Verify `--plugin-dir ./pfc-core` discovers the plugin | Claude Code loads skills from `pfc-core/skills/` |

**Result:** Pass (TC-3.1, TC-3.2). TC-3.3 deferred to first PFI triad bootstrap.

### TC-4: pfc-release.yml distributes sealed skills

| Step | Action | Expected |
|------|--------|----------|
| 4.1 | Review `pfc-release.yml` sealed-skills block | Reads manifest, copies SKILL.md to target path, copies plugin.json, manifest, guard-core.yml |
| 4.2 | Verify `components` input accepts `sealed-skills` and `all` | Input definition includes both options, default is `all` |
| 4.3 | End-to-end: tag `pfc-v*` release | Sealed skills PR created in registered PFI dev repos |

**Result:** Pass (TC-4.1, TC-4.2 — code review). TC-4.3 deferred to first PFC release.

### TC-5: bootstrap-triad.sh scaffolds pfc-core

| Step | Action | Expected |
|------|--------|----------|
| 5.1 | Review `TEMPLATE_FILES` array | Includes `guard-core.yml` |
| 5.2 | Review `SEALED_STRUCTURE_FILES` array | Includes `pfc-core/.claude-plugin/plugin.json` |
| 5.3 | Verify Step 4b creates placeholder | `mkdir -p pfc-core/skills && touch pfc-core/skills/.gitkeep` |
| 5.4 | Dry run `--dry-run` flag | Report shows pfc-core structure, dual plugin-dir launch command |

**Result:** Pass (TC-5.1–5.3 — code review). TC-5.4 deferred to next bootstrap.

### TC-6: guard-core.yml blocks human edits

| Step | Action | Expected |
|------|--------|----------|
| 6.1 | Create PR touching `pfc-core/` as a human user | guard-core check fails with error message |
| 6.2 | Create PR touching `pfc-core/` as `github-actions[bot]` | guard-core check passes |
| 6.3 | Create PR with `pfc-core-release` label | guard-core check passes |

**Result:** Deferred — requires live PFI triad repo with branch protection enabled.

---

## Deferred Tests

| Test | Blocked by | When to run |
|------|-----------|-------------|
| TC-3.3 (plugin discovery) | No PFI triad bootstrapped yet | After S31.4.4 (Bootstrap BAIV triad) |
| TC-4.3 (e2e release) | No `pfc-v1.0.0` tag yet | After S31.1.3 (Tag first PFC release) |
| TC-5.4 (bootstrap dry run) | Script not yet executed | After S31.4.4 |
| TC-6 (guard-core live) | No PFI triad with branch protection | After S31.4.4 |

---

## Summary

| Category | Count |
|----------|:-----:|
| Total test cases | 6 |
| Passed (code review) | 4 (TC-1 through TC-5 partial) |
| Deferred (need live infra) | 4 sub-cases |
| Failed | 0 |
